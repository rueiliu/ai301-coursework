# Plan: issue #61 — health probe raw SQL string

## Diagnosis

The Postgres probe in `api/routes/health.py:32` calls
`await db.execute("SELECT 1")` with a bare Python string. SQLAlchemy 2.x
does not accept a bare string as an executable statement; it requires
textual SQL to be wrapped in `sqlalchemy.text()`. My week-2 reproduction
pins exactly this, from a clean checkout of `2f4e82f` against a live
PostgreSQL 16 instance (the `db` service from `docker-compose.yml`):

> `[control] await db.execute(text('SELECT 1')) -> 1 (database is up)`
> `[failing] await db.execute('SELECT 1') -> sqlalchemy.exc.ArgumentError: Textual SQL expression 'SELECT 1' should be explicitly declared as text('SELECT 1')`
> `[symptom] GET /health -> HTTP 503` / `dependencies.postgres = 'unhealthy'`

The control run is the part the diagnosis rests on: the same session,
against the same live database, answers the same `SELECT 1` successfully
the moment it is wrapped in `text()`. That rules out a database
connectivity problem and a database-config problem — the database is
reachable the whole time — and leaves the raw-string argument to
`execute()` as the only thing that changes between the passing and
failing calls. There is no other variable in my reproduction that this
diagnosis has to survive; it is a single `except Exception` clause
around a single statement.

## Scope

**In scope:** the Postgres probe's `execute()` call at
`api/routes/health.py:32`, and a regression test for it.

**Not in scope:** the Redis probe a few lines below, which fails for an
unrelated reason (`Settings` has no `redis_host`/`redis_port` attribute,
only `redis_url`) — that is issue #62, and my week-2 repro report already
separated it out rather than folding it into this fix. I am not touching
the Redis branch, the vector-db branch, or the overall response shape of
`health_check()`.

**Prior art:** PR #82 (`pakmultilinks-dot`, open) already fixes this same
line, bundled together with the separate Redis fix for #62 in one PR. My
plan takes the same one-line approach for #61 specifically, built from my
own week-2 reproduction rather than theirs, and scoped to #61 alone per
my own claim on this issue; I am not racing or duplicating their Redis
fix, and the two PRs can be reviewed independently since they touch
disjoint lines.

## Files I'll touch

- `api/routes/health.py` — wrap the Postgres probe's SQL in `text()`.
- `tests/unit/test_health.py` — new file; no health test currently exists
  in the repo (confirmed: `tests/unit/` has no `test_health.py`, and
  PR #82's own description notes the same gap).
- ~~`pyproject.toml`~~ — planned, not built; see Deviations. The mypy
  suppression list stays untouched for this issue.

## Approach

1. In `api/routes/health.py`, change line 32 from
   `await db.execute("SELECT 1")` to
   `await db.execute(text("SELECT 1"))`, adding
   `from sqlalchemy import text` to the imports at the top of the file.
2. Add `tests/unit/test_health.py`, following the repo's existing
   convention for async route tests (`@pytest.mark.unit`,
   `@pytest.mark.asyncio`, an `AsyncMock` session fixture — the pattern
   `tests/unit/test_review_service.py` already uses). The fixture's
   `execute()` enforces the real SQLAlchemy rule this bug is about: it
   raises `ArgumentError` if handed a bare `str`, and succeeds
   (returning a scalar) if handed a `TextClause`. Two tests:
   - the probe reports `dependencies.postgres == "healthy"` against the
     fixture;
   - the exact object passed to `execute()` is a `sqlalchemy.sql.elements.TextClause`,
     not a `str` — a direct regression guard against the defect
     returning in a future edit.
3. No other line in the health route changes, and `pyproject.toml` is
   not touched (planned, not built; see Deviations).

## Test plan

Primary: re-run my week-2 reproduction steps against the fixed code,
against the same live PostgreSQL 16 instance (`docker compose up -d db`,
`APP_ENV=production PYTHONPATH=. ./.venv/bin/python repro_61.py`).

- **Before** (posted in my week-2 repro comment):
  `[failing] ArgumentError: Textual SQL expression 'SELECT 1' should be explicitly declared as text('SELECT 1')`,
  `GET /health -> HTTP 503`, `postgres = 'unhealthy'`.
- **Expected after**: no `ArgumentError` from the Postgres call; the
  `postgres` key reads `'healthy'`; `GET /health` reports `200` for the
  Postgres dependency specifically (the route may still read `503`
  overall only if Redis — #62, out of scope — is also down in the test
  environment, which is why the test asserts the `postgres` key directly
  rather than the route's overall HTTP status).

Secondary: `pytest tests/unit/test_health.py -v` passes, exercising the
same assertion without needing Docker up.

## Risks and unknowns

- I have not yet confirmed whether any other route in the codebase
  passes a bare string to `db.execute()`; I scoped this fix to the one
  line the issue names rather than auditing the whole codebase for the
  same pattern, since that would be separate work the issue does not
  ask for. I will grep for `execute("` elsewhere and mention anything
  suspicious in the PR description rather than fixing it here.
- My repro invoked the route handler directly rather than through
  uvicorn/HTTP end-to-end; the test plan's primary check is therefore
  at the handler level. If the CI environment's Docker stack differs
  from mine in some way I haven't anticipated, the handler-level result
  should still hold since the defect is in Python/SQLAlchemy coercion,
  not in networking or the HTTP layer.

## Deviations

**The `pyproject.toml` mypy suppression change did not happen, and the
plan was wrong about why.** I planned to remove `call-overload` from the
`api.routes.health` disable list, on the strength of a mypy run that
showed the code inert both before and after the `text()` fix. That run
used an incomplete environment: my venv had the `redis` package but not
`types-redis` (a `dev`-extra dependency), so mypy had no stubs for
`redis.Redis(...)` and silently skipped checking it, which is exactly
where `call-overload` actually fires — on the Redis constructor call at
line 47, inside `#62`'s code, not `#61`'s.

I caught this by installing the project properly (`pip install -e
".[dev]"`) before running the full test suite, and re-ran mypy under
that complete environment as a sanity check on the suppression-list
change specifically. It reversed the earlier finding: with
`call-overload` removed, mypy reports exactly the Redis-constructor
error, 1 error in 1 file; with the original three-code suppression list
left untouched and only the `text()` fix applied, mypy reports zero
errors. So `call-overload` is `#62`'s suppression, not `#61`'s, exactly
like `attr-defined` — neither belongs to this issue, and the correctly
scoped change touches only `api/routes/health.py` and the new test file.
I reverted the `pyproject.toml` edit before committing.

I posted my plan comment before running the full-environment check, so
it states the now-incorrect claim that I verified `call-overload` as
inert and would remove it. I'm posting a follow-up comment on the issue
correcting that, per the house rule that a posted plan which is no
longer true gets a correction rather than a silent fix.

Nothing else about the plan changed: the diagnosis, the `health.py` fix,
the new test, and the test plan all built exactly as posted.
