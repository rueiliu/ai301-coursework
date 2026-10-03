# Unit 3 — Plan and Build

Path: `beat-1-sandbox/unit-3/plan-and-implement.md`

Record of your plan, the branch you built it on, and the evaluation runs that produced
`eval-run.txt`. This file is graded at the path above; a copy kept anywhere else in the
repository is not read.

Complete every labelled field below. Each is graded on its own; content placed under the wrong
label is not graded.

---

## Posted upstream

**GitHub username**

rueiliu

**Plan comment**

https://github.com/codepath/pathreview-ai301-fa26-s3/issues/61#issuecomment-5967990415

My plan for #61, built from the reproduction I posted above.

The fix is one line: `api/routes/health.py:32` passes `"SELECT 1"` to `db.execute()` as a bare string, and SQLAlchemy 2.x requires that wrapped in `text()`. My repro's control run shows the same session answers the same query fine once it's wrapped, so the database itself was never the problem — only the call's argument. I'll wrap it in `text()` and add a regression test at `tests/unit/test_health.py` (there's no existing health test) whose fixture enforces the exact rule this bug breaks: it raises `ArgumentError` for a bare string and succeeds for a `TextClause`.

Scope: just this probe. The Redis branch a few lines down fails for an unrelated reason (`Settings` has no `redis_host`) — that's #62, and I'm leaving it alone here. I'll also drop `call-overload` from this file's mypy suppression list in `pyproject.toml` per the seeded-bug convention in CONTRIBUTING.md — checked with mypy that it's the one of the three disabled codes that's actually inert here, before and after the fix, so it's the one I can honestly remove.

I see PR #82 already has an open fix for this same line, bundled with a Redis fix for #62. I'm scoping mine to #61 only, built from my own reproduction, since the two touch disjoint lines and can be reviewed independently.

Test plan: re-run my week-2 repro steps against the change and confirm `dependencies.postgres` reads `healthy` with no `ArgumentError`, plus the new unit test passing.

---

**Follow-up correction**, posted after I found the plan's `pyproject.toml` claim was wrong (see `plan.md`'s Deviations section for the full account): https://github.com/codepath/pathreview-ai301-fa26-s3/issues/61#issuecomment-5968049994

Correction to my plan above: the `pyproject.toml` change doesn't happen.

I'd said I checked that `call-overload` was inert in `api.routes.health`'s mypy suppression list and would remove it. That check ran in an incomplete environment — my venv had `redis` but not `types-redis`, so mypy wasn't actually checking the Redis constructor call at all. With the project installed properly (`pip install -e ".[dev]"`), `call-overload` does fire, on the Redis constructor at line 47 — that's `#62`'s code, not this issue's. With the original three-code suppression list untouched and only the `text()` fix applied, mypy reports zero errors on the file.

So the fix for #61 is exactly the one line plus the regression test, nothing in `pyproject.toml`. Everything else in my plan built as posted; full details in `plan.md`'s Deviations section once the PR is up.

---

## Your branch

**Branch**

fix/61-health-probe-raw-sql

**Evidence**

My week-2 reproduction script (`repro_61.py`), re-run against a real PostgreSQL 16 instance
(`docker compose up -d db`), before and after the fix. The script's `[control]` block wraps the
query in `text()` directly (always passes, both before and after — it demonstrates the rule the
bug breaks). Its `[failing]` block calls `db.execute()` with a bare string directly, bypassing
`health_check()`, to show the raw SQLAlchemy rule on its own; that block still raises
`ArgumentError` after the fix too, by design, since it is not going through the fixed code path.
The line that actually matters is `[symptom]`, which calls the real `health_check()` handler.

**Before** (posted in my Unit 2 repro comment, commit `2f4e82f`, code unfixed):

```
$ APP_ENV=production PYTHONPATH=. ./.venv/bin/python repro_61.py
[control]  await db.execute(text('SELECT 1')) -> 1   (database is up)
[failing]  await db.execute('SELECT 1') -> sqlalchemy.exc.ArgumentError: Textual SQL expression 'SELECT 1' should be explicitly declared as text('SELECT 1')
[symptom]  GET /health -> HTTP 503
[symptom]  dependencies.postgres = 'unhealthy'  (database answered SELECT 1 above)
```

**After** (branch `fix/61-health-probe-raw-sql`, same live database, same script):

```
$ APP_ENV=production PYTHONPATH=. ./.venv/bin/python repro_61.py
[control] 1 <- DB answers
[failing] ArgumentError: Textual SQL expression 'SELECT 1' should be explicitly declared as text('SELECT 1')
2026-10-03 05:01:43 [debug    ] postgres_health_check_passed
2026-10-03 05:01:43 [error    ] redis_health_check_failed      error="'Settings' object has no attribute 'redis_host'"
2026-10-03 05:01:43 [debug    ] vector_db_health_check_passed
[symptom] GET /health -> 503
[symptom] dependencies.postgres = 'healthy'
```

`dependencies.postgres` flips from `'unhealthy'` to `'healthy'` — the actual probe this issue is
about now passes. `GET /health` still reports `503` overall in both runs, because Redis (`#62`,
out of scope, unfixed) is also down in this environment; that is why the test plan and the new
unit test assert the `postgres` key directly rather than the route's overall HTTP status.

Secondary evidence, the new regression test, same branch, same live database:

```
$ APP_ENV=production PYTHONPATH=. ./.venv/bin/python -m pytest tests/unit/test_health.py -v
tests/unit/test_health.py::TestHealthCheckPostgresProbe::test_postgres_probe_reports_healthy PASSED [ 50%]
tests/unit/test_health.py::TestHealthCheckPostgresProbe::test_postgres_probe_statement_is_textclause PASSED [100%]
========================= 2 passed, 1 warning in 0.37s =========================
```

And a negative control on the test itself: reverting just `api/routes/health.py` (keeping the
test) and re-running confirms the test actually catches the regression, rather than passing
vacuously:

```
$ git stash push -- api/routes/health.py && APP_ENV=production PYTHONPATH=. ./.venv/bin/python -m pytest tests/unit/test_health.py -v
tests/unit/test_health.py::TestHealthCheckPostgresProbe::test_postgres_probe_reports_healthy FAILED
tests/unit/test_health.py::TestHealthCheckPostgresProbe::test_postgres_probe_statement_is_textclause FAILED
========================= 2 failed, 1 warning in 0.33s =========================
$ git stash pop   # fix restored
```

Full repo test suite and mypy, same branch, confirming no collateral damage:

```
$ APP_ENV=production PYTHONPATH=. ./.venv/bin/python -m pytest tests/unit/ -q
377 passed, 53 xfailed, 5 warnings in 8.24s

$ APP_ENV=production PYTHONPATH=. ./.venv/bin/mypy api/routes/health.py --ignore-missing-imports
Success: no issues found in 1 source file
```

## Eval iterations

Answer all four sections. Quote source text directly; paraphrase does not satisfy these
fields.

**Run history**

Two runs, in order:

1. Partial run, `--only pkg-16,pkg-09,pkg-20`: **3/3**. I picked these three deliberately
   rather than using `--limit`: `pkg-16` is the hardest `wrong-cause` package (the diagnosis
   reads as plausible prose and only a close read of the pyarrow control run in step 4 rules
   it out), `pkg-09` is a `clear-accept` that defers related work with a stated reason (the
   case my `scope-bounded` check could have falsely rejected if it conflated "narrows the fix"
   with "incomplete"), and `pkg-20` is the one `thread-convention` package that tests the
   disclosure half of `comms-conventions`. All three agreed, which told me the design held on
   its three riskiest edges before I spent $4.
2. Full 20-package run: **19/20**. This is the run committed as `eval-run.txt` in this
   directory, and these two lines are quoted from it:

   ```
   categories: clear-accept 6/7  scope-creep 4/4  thread-convention 2/2  unbuildable 3/3  wrong-cause 4/4
   agreement: 19/20 scored items  (bar: 18/20: PASS)
   ```

I stopped there rather than revising and re-running. The run clears the bar and matches every
category including the 2-item `thread-convention` floor, and the single disagreement is a
narrow, diagnosable wording issue in one check rather than a missed family — I judged that
worth writing up honestly rather than burning another $4 to chase 20/20, since the assignment
states a first run clearing the bar loses nothing.

**Package analysis**

`pkg-14` (zellij, reattach color-query leak into the active pane).

- My rubric's decision: **reject**, on `executable`.
- Gold label: **accept**, category `clear-accept`, noted as "honestly scoped-down: reattach
  handshake fix with a regression-window repro; defers the untestable Windows variant and says
  so; arguable on the deferral, ready as scoped".

Why my rubric read it that way. The plan's `Files:` line reads: "the client attach/reattach path
in `zellij-server` (session connection handling) and `zellij-client`'s terminal query issuance;
**exact functions to be pinned in the PR after tracing the query issuance with debug logs, which
I have working**." My `executable` check fails a plan when "a real decision about the core fix is
left open for build time," and "exact functions to be pinned... after tracing" reads exactly like
that pattern on a surface scan — the same shape as `pkg-10`'s "profile and see" or `pkg-18`'s
"recover() somewhere."

Reading it again against my own evidence guide's distinction (a hedge on "a genuinely secondary
detail" passes; a hedge on "the fix itself" fails), the two are not actually alike. The plan has
already committed to everything that makes a fix a fix: the mechanism (drain or consume pending
OSC query responses before pane input is wired), the general site (two specific, named modules —
not "somewhere" or "whichever is easier"), and it states the author already has working debug-log
tracing showing where the leak originates. What is deferred is only the literal function name
inside an already-narrowed module, after tracing that is already done — not whether to do it, not
where, not how. `pkg-10`/`pkg-17`/`pkg-18` defer the decision itself (which layer, which
mechanism, whether to vendor or patch upstream); `pkg-14` defers only the paperwork of writing
down a name. My check's wording doesn't distinguish "undecided" from "decided, not yet
transcribed," and it read the second as the first.

**Check rationale**

Quoted from `tools/plan-check/rubric.md` exactly as it currently reads, the `executable` row:

> | `executable` | The plan's approach and files/areas, read as a stranger with no access to the
> author who must start work from the plan alone. | The plan names the specific file(s) or
> location(s) to change and commits to one chosen approach for the core fix. It fails when a
> real decision about the core fix is left open for build time ("upstream or vendored, whichever
> is easier", "wherever the input stack turns out to live", "profile and see"), when no file or
> area is named at all, or when the next action is "investigate" rather than a change. Open
> questions about a genuinely secondary detail (which of two already-equivalent call sites to
> also audit) do not fail this check; an open question about the fix itself does. | required |

Why it reads that way, and what I rejected to get there. I deliberately rejected a check that
counts structure — "the plan has a Files section" or "at least one file path appears" — because
`pkg-10`, `pkg-17`, and `pkg-18` all technically mention files or areas in prose ("the input
stack", "upstream or vendored") while deferring the one decision that actually matters: what the
fix *is*. So I wrote the condition around a committed approach for the core fix specifically, with
three named examples of what deferring the fix itself sounds like, and one explicit carve-out for
a hedge on a secondary detail.

What I would change if I spent another run on it: the carve-out needs a second clause,
distinguishing "undecided" (a real open question about the fix) from "decided, but the exact
identifier isn't written down yet, and the author shows work proving the decision is already made"
(tracing already done, module already named, mechanism already chosen). `pkg-14` is the second
kind and my check currently reads it as the first. I left the check as written so the rubric in
this folder matches the fingerprint recorded in `eval-run.txt`.

**Trade-offs**

This check changes the result of exactly one package: `pkg-14`, gold `accept`, my verdict
`reject`, on `executable`. It is the entire gap between my 19/20 and a clean 20/20.

What it gives up, stated plainly: as written, the check cannot tell a plan that has genuinely
left its core decision for build time from a plan that has made every real decision and is only
missing the literal function name inside an already-named, already-traced module. It buys that
cost for a real catch: `pkg-10`'s "profile and see" and `pkg-18`'s "recover() somewhere, upstream
or vendored, whichever is easier" both name an area in prose without committing to an approach,
and a looser check risks letting that kind of plan through as if a vague mention of a file were
the same as a decision. I would rather a plan-check rubric err toward holding a plan whose last
sentence sounds unfinished than toward passing one that defers its actual decision to the build,
but one false reject out of seven clear-accepts is a real cost, and the fix (distinguish
"undecided" from "decided, not yet transcribed") does not require giving up the catch — it is a
clarifying clause, not a loosening.

The direction of the miss is also informative: all four `wrong-cause`, all four `scope-creep`, all
three `unbuildable`, and both `thread-convention` packages matched gold exactly, and the one miss
is a false reject rather than a false accept. My rubric is systematically strict on this one check
rather than randomly wrong across several, which is the safer failure mode to carry into live
mode — it holds my own plan to a higher bar than it needs to, rather than letting an underspecified
one through — but it is the one thing I would revise first before running this rubric again.

---

Related paths: `plan.md` and `eval-run.txt` in this directory; your skill's files in
`tools/plan-check/`.
