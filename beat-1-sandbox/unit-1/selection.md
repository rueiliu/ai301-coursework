# Unit 1 — Issue Selection

Path: `beat-1-sandbox/unit-1/selection.md`

Record of the issue carried into Unit 2, and of the evaluation runs that produced
`eval-run.txt`. This file is graded at the path above; a copy kept anywhere else in
the repository is not read.

Complete every labelled field below. Each is graded on its own; content placed under the
wrong label is not graded.

---

## Selected issue

**Issue link**

https://github.com/codepath/pathreview-ai301-fa26-s1/issues/61

**Verdict output**

Ranked live-mode read-out for all three candidates I graded in one run, pasted verbatim.
The skill accepted all three and ranked this issue (#61) first on my fit profile; its
per-issue object is the first element of the closing JSON array, with `"verdict": "accept"`.

**The verdict must record `accept` for this issue.** Choose an issue your own skill
accepts. If your skill rejects every candidate you try, that is a signal about your
rubric rather than about the issues: revise it and re-run — retries are unlimited and a
partial re-run costs about $0.20 — or run the skill on different candidates. Output
recording `reject` for the issue you chose earns no credit for this field.

All required checks pass for all three. Here's the evidence summary.

**Repo-level (shared):**
- `repo-alive`: **pass** — `archived: no`, newest commit 2026-09-16 (5 days ago, human author Aburke225).
- `policy-allows-ai`: **pass** — `docs/CONTRIBUTING.md` describes a seeded-bug/xfail workflow; no ban on AI-assisted contributions.
- `shipped-recently` (preferred): **fail** — `latestRelease: null`, no releases (not disqualifying; commit activity carries liveness).
- `responds-to-issues` (preferred): **pass** — COLLABORATOR Aburke225 responded on issues #43 and #52.

**Per-issue:** all three are OPEN, `assignees: none`, zero comments (no claim within 120 days), terse bug reports with reproduction steps + a named target file, and carry `good first issue` + `bug` + `tier-1` labels. Issue #72 has one cross-referenced PR #1 in `merged` state — a finished attempt, not an open claim, so it does not block.

---

**Ranked read-out (all accepted):**

1. **#61 — Health-check DB probe raw SQL string** — Best fit: FastAPI backend (`api` label) *and* a SQLAlchemy `text()` fix, hitting the user's two stated strengths (Python backend + SQL) at once, with a runnable reproduction.
2. **#72 — `verify_password` raises on malformed hash** — Backend (`core/security.py`) with an explicit existing test to extend (`tests/unit/test_security.py`, xfail marker to remove) — exactly the "existing test I can extend" the fit profile rewards; 1–2 hr estimate.
3. **#53 — PII scrubber parenthesized phone numbers** — Python backend with a runnable reproduction and four named failing tests, but regex/PII work is further from the SQL/data-handling center of the fit profile.

Rejected: none.

```json
[
  {
    "item": "https://github.com/codepath/pathreview-ai301-fa26-s1/issues/61",
    "checks": [
      {"name": "repo-alive", "grade": "pass", "evidence": "archived: no; newest of last 5 commits dated 2026-09-16, within 90 days of today, human author Aburke225"},
      {"name": "unclaimed", "grade": "pass", "evidence": "assignees: none; no linked PRs; zero comments so no claim within 120 days"},
      {"name": "scope-bounded", "grade": "pass", "evidence": "terse bug report with reproduction steps and one named file api/routes/health.py; labels bug + good first issue + tier-1"},
      {"name": "policy-allows-ai", "grade": "pass", "evidence": "docs/CONTRIBUTING.md sets a seeded-bug/xfail workflow with no ban on AI-assisted contributions"},
      {"name": "shipped-recently", "grade": "fail", "evidence": "latestRelease: null — repo has no releases"},
      {"name": "responds-to-issues", "grade": "pass", "evidence": "COLLABORATOR Aburke225 commented on issues #43 and #52"}
    ],
    "verdict": "accept"
  },
  {
    "item": "https://github.com/codepath/pathreview-ai301-fa26-s1/issues/72",
    "checks": [
      {"name": "repo-alive", "grade": "pass", "evidence": "archived: no; newest of last 5 commits dated 2026-09-16, human author Aburke225"},
      {"name": "unclaimed", "grade": "pass", "evidence": "assignees: none; only cross-referenced PR #1 is state MERGED (finished attempt, not an open claim); zero comments"},
      {"name": "scope-bounded", "grade": "pass", "evidence": "bounded bug in core/security.py + tests/unit/test_security.py, xfail marker to remove, 1-2 hr estimate; labels bug + good first issue + tier-1"},
      {"name": "policy-allows-ai", "grade": "pass", "evidence": "docs/CONTRIBUTING.md; no AI ban"},
      {"name": "shipped-recently", "grade": "fail", "evidence": "latestRelease: null — no releases"},
      {"name": "responds-to-issues", "grade": "pass", "evidence": "COLLABORATOR Aburke225 responded on issues #43 and #52"}
    ],
    "verdict": "accept"
  },
  {
    "item": "https://github.com/codepath/pathreview-ai301-fa26-s1/issues/53",
    "checks": [
      {"name": "repo-alive", "grade": "pass", "evidence": "archived: no; newest of last 5 commits dated 2026-09-16, human author Aburke225"},
      {"name": "unclaimed", "grade": "pass", "evidence": "assignees: none; no linked PRs; zero comments so no claim within 120 days"},
      {"name": "scope-bounded", "grade": "pass", "evidence": "bug in pii_scrubber.py with reproduction snippet and four named failing tests; labels bug + good first issue + tier-1"},
      {"name": "policy-allows-ai", "grade": "pass", "evidence": "docs/CONTRIBUTING.md; no AI ban"},
      {"name": "shipped-recently", "grade": "fail", "evidence": "latestRelease: null — no releases"},
      {"name": "responds-to-issues", "grade": "pass", "evidence": "COLLABORATOR Aburke225 responded on issues #43 and #52"}
    ],
    "verdict": "accept"
  }
]
```

---

## Eval iterations

Quote source text directly in each field below. Paraphrase does not satisfy them.

**Run history**

Two runs, in order:

1. `--limit 3` smoke run (`issue-01` through `issue-03`): **3/3 scored items.** This was a
   partial run, so the harness printed no bar verdict and no category line. I did it only to
   confirm the skill could parse my rubric before committing a full run's cost, and I picked a
   smoke run rather than going straight to the full set because a formatting mistake in the
   checks table would have wasted about $4 instead of about $0.60.
2. Full 20-issue run: **19/20**. This is the run committed as `eval-run.txt` in this
   directory, and these two lines are quoted from it:

   ```
   categories: claimed 4/4  clear-accept 8/8  dead-repo 3/3  policy 1/1  scope 3/4
   agreement: 19/20 scored items  (bar: 18/20: PASS)
   ```

I did not revise the rubric between the two runs, and I did not run `--only` re-grades,
because the smoke run surfaced no disagreements and the full run cleared both the bar and the
category floor on its first attempt. The single disagreement it did produce is `issue-20`,
analysed below; I judged that chasing 20/20 was not worth another $4 when the assignment
states a first run that clears the bar loses nothing.

**Issue analysis**

`issue-20` (excalidraw, "Add company logo shape to the toolbar").

- My rubric's decision: **accept**
- Gold label: **reject**, category `scope`, with the note "one-line feature wish with no spec
  and a product decision hiding inside"

My `scope-bounded` check fails an issue only when at least one of four named disqualifiers
fires, and on this issue none of them did:

- (a) did not fire: the issue asks for one new toolbar shape in one named place. It is not an
  umbrella or tracking issue and does not list sub-tasks to be split into separate PRs.
- (b) did not fire: the thread has zero comments, so there is no long unsettled debate to
  detect.
- (c) did not fire: no maintainer commented at all, so no maintainer said the fix touches core
  internals or that the design is undecided.
- (d) is the disqualifier that should have caught it, and the reason it did not is how I wrote
  the condition. I required *both* halves of a conjunction — "neither acceptance criteria in
  the body nor any endorsement from a maintainer". The body's "Describe the solution you'd
  like" section does read as acceptance criteria: it asks that users be able to "place, resize,
  and move it like other elements, and it should export correctly". That made the first half
  false, so the conjunction was false and the disqualifier never triggered, even though the
  second half was true — the issue carries no labels at all and has no maintainer endorsement.

So my rubric accepted it on the strength of a well-formatted template body. What the gold note
identifies is something my rubric has no check for at all: the issue was filed by `cursor[bot]`
rather than by a human user or a maintainer, and "add *our company's* logo as a first-class
shape" is a product decision that only the maintainers can make, not a defect with a single
correct fix. A newcomer could write the code and still have the PR closed because the project
never agreed the feature should exist. My four checks all ask whether the work is *doable*;
none of them asks whether the project has agreed the work should *happen*.

**Check rationale**

Quoted from `tools/issue-select/rubric.md` exactly as it currently stands, the
`responds-to-issues` row:

> | `responds-to-issues` | Repo facts: the "maintainer first-response sample (5 recently
> updated issues)" | At least one issue in the sample got a first response from an owner,
> member, or collaborator, at any latency. | preferred |

I first drafted this check the way the lecture's own worked example has it — a latency
threshold, "a reply within 30 days" — and as `required`, on the reasoning that a project which
never answers contributors will never review my PR either.

Reading the bundles showed that would have been a mistake, and specifically which bundles would
have paid for it. The conda repo behind `issue-01`, `issue-09` and `issue-16` has a response
sample where four of the five sampled issues read "no maintainer comment in thread" and the
fifth is 32.9 days. All three of those issues are gold `accept`, and they are `clear-accept`
issues, not arguable ones. A `required` check with a 30-day line would have rejected three
clear accepts by itself, which alone puts the 18/20 bar out of reach before any scope call is
even argued. The sample is thin and noisy: five recently-updated issues on a large busy repo
tell you more about which threads happened to be sampled than about whether maintainers are
present.

So the current form keeps the signal but changes two things about it. It drops the latency
threshold entirely and asks only whether anyone with an owner, member or collaborator badge
ever answers; and it is `preferred`, so it ranks accepted issues instead of gating them.

**Trade-offs**

What this check gives up: the rubric can no longer reject a repo that commits actively but
ignores outsiders. A project whose maintainers merge their own work daily and never answer a
single external issue passes every `required` check I have, and `responds-to-issues` — the one
check that would notice — cannot change that verdict by design. That is a real gap, and it is
the case I accept this check will miss.

Nothing changed elsewhere as a result, and here is how I know. The two bundles whose response
samples are entirely silent are `issue-02` and `issue-07`, both showing five of five "no
maintainer comment in thread". Both are still rejected, because `repo-alive` catches them on
commit age instead: 970 and 1281 days past the capture date against a 90-day threshold. The
full run's category line confirms that demoting the response check cost no dead-repo detection
at all — `dead-repo 3/3`. In other words, on this eval set the responsiveness signal is
redundant with commit recency, and every issue it would have rejected was already rejected on
harder evidence. The case it would have caught uniquely — an active repo that is unresponsive
only to outsiders — is one this eval set does not contain, which is exactly why I was willing
to demote it on this evidence and would revisit it on a real repo.

---

## Selection rationale

Graded on whether all three are answered, in your own words. Not on how good the
reasoning is, and not on length — a short honest answer to each earns the full marks.
This is also the basis for the claim comment you write in Unit 2.

**Selection rationale**

**1. Fit to my interests and to the time available.**

My background is Python and SQL, but on the data-analysis side — pandas, notebooks, querying
and reshaping datasets — and what I want from this course is engineering practice rather than
more analysis. Issue #61 sits exactly on that line. The bug is that `api/routes/health.py`
executes the literal string `"SELECT 1"`, and SQLAlchemy 2.x requires textual SQL to be wrapped
in `sqlalchemy.text()`, so the probe raises `ArgumentError` and the health check reports the
database as down even when it is reachable. The SQL half is familiar ground; the unfamiliar
half — a FastAPI route, an ORM's API contract, a health-check endpoint — is the backend
engineering I signed up to learn. It is also firmly not frontend, which I wanted to avoid.

On time: the fix is one call in one named file, and the issue gives me the exact error string
to reproduce and confirm against. That is a small enough change that I can spend my time
learning how the repo is set up and run rather than on the fix itself, which is what I want out
of the first one.

**2. What the verdict identified correctly, and what I weighed that the rubric could not.**

The verdict got the things I built it to get. It confirmed the repo is alive on commit evidence
(newest commit 2026-09-16, `archived: no`, human author), that the issue is genuinely free
(`assignees: none`, no linked PRs, zero comments so no claim inside my 120-day window), that
the scope is bounded (a terse bug report with reproduction steps and one named target file), and
that nothing in the contribution policy bans AI-assisted work. It also correctly declined to
hold the missing releases against the repo: `shipped-recently` failed, but as a `preferred`
check it cannot sink a verdict, which is the behaviour I wanted after seeing an accept in the
eval set with no releases at all.

What I weighed that the rubric could not: all three candidates were accepted, so the rubric
told me nothing about which to take. Choosing between them was mine to do. I picked #61 over
#72 and #53 because of what the fix teaches rather than anything the checks measure — #72 is
password-hash error handling and #53 is regex work on phone numbers, both bounded and both
fine, but neither puts me inside an ORM's session and query layer the way #61 does, and that
layer is the part of backend work my analysis background has never touched. The rubric also
cannot see that a health-check route is a good place to be a newcomer: if I break it, I break a
diagnostic endpoint, not user-facing behaviour.

**3. Anticipated difficulty in claiming it.**

Low on the social side, higher on the setup side. Path Review is a classroom and its house rule
says classmates' claim comments do not block an issue, and course credit attaches to the pull
request I open rather than to whether it merges — so even if someone else takes #61 too,
nothing is lost. The issue currently has zero comments and no assignee, so I am not stepping on
anyone as things stand.

The real difficulty I expect is reproduction, which is Unit 2's job. Reproducing this needs the
stack running with a live database session, and my experience is with notebooks against
existing databases rather than standing up a service locally. So I expect the setup — getting
the app running and hitting `GET /health` — to cost me more than the one-line fix, and I would
rather discover that on a small bug than a large one.

---

Related paths: `eval-run.txt` in this directory; your skill's files in
`tools/issue-select/`.
