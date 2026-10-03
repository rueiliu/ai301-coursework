# Evidence guide: where evidence lives in a plan package

## Diagnosis and grounding

**Where it lives.** In an eval bundle: the plan's `Diagnosis` (or equivalent
opening) section of `## Candidate plan`, read against every step, command,
and control run in `## Repro evidence`. In live mode: the plan's diagnosis
section, read against the student's posted repro comment from week 2 (or
the house repro pack).

**What good looks like.** The stated cause is consistent with every
observation the repro evidence contains, not just the headline symptom. Pay
special attention to control runs: a control that isolates one variable
(the same command with a flag removed, the same input through an alternate
path, the same operation at a different context) tells you what the defect
is *not*, and a diagnosis the control rules out fails regardless of how
reasonable it reads on its own. Diagnoses that survive every control, or
that explicitly address why an apparently-contradicting control does not
rule them out, pass. A diagnosis is also ungrounded if it rests only on
confidence words ("clearly", "obviously") with no cited step behind them.

## Scope

**Where it lives.** In an eval bundle: the plan's scope / in-scope /
not-in-scope statement and its list of files or areas, read against the
issue's own description of the defect in `## Issue` and the site the repro
evidence implicates. In live mode: the same sections of the draft plan,
read against the issue body and thread.

**What good looks like.** One bounded change at the site(s) the evidence
actually implicates. A plan that narrows the fix and says why (defers a
related rework, a secondary variant, or a tangential cleanup with a stated
reason) is scoped, not incomplete — several accepted plans here do exactly
this. A plan fails this check when the "fix" is accompanied by unrelated
work the issue never asked for: a migration, a new abstraction layer, a
redesign of adjacent code, a CI job, a feature. The size of the write-up is
not the signal; a five-section proposal can still be one bounded fix, and a
one-paragraph plan can still smuggle in a rewrite. Read what changes, not
how many words describe it.

## Executability

**Where it lives.** In an eval bundle: the plan's approach/changes section
and its `Files:` line (or equivalent), read as someone who will start
editing from the plan alone. In live mode: the same section of the draft.

**What good looks like.** At least one specific file or location is named,
and the core fix has one chosen approach, not a menu of options to decide
between during the build. Watch for decisions dressed as plans: "recover()
somewhere", "upstream or vendored, whichever is easier", "investigate
whether X or Y is the right layer" are all deferrals of the fix itself, not
descriptions of it. An open question about a genuinely secondary detail
(whether to also audit one more call site of the same pattern) does not
fail this check — only an open question about what the fix *is* does.

## Test plan

**Where it lives.** In an eval bundle: the plan's `Test plan:` line, read
against the exact commands, inputs, and artifacts in `## Repro evidence`.
In live mode: the draft plan's test plan, read against the student's week-2
repro steps and observed output.

**What good looks like.** The test plan names a specific, checkable outcome
tied to this fix: re-running the repro's exact command and stating the
expected exit code or printed value, re-running a control unchanged to
confirm nothing else moved, or a named regression test asserting a specific
behavior. It fails on a standard with no outcome specific to this fix:
"run the full test suite", "make sure nothing regresses", "should feel
faster", "nothing else should feel broken". A test plan that is only "re-run
the week-2 repro steps and confirm the error no longer appears" is decisive
enough to pass, because it names a specific, observable check.

## Honesty

**Where it lives.** In an eval bundle: the plan's stated risks, unknowns,
and any explicit deferrals, read against how confidently the rest of the
plan is written. In live mode: the same section of the draft, and — after a
build — the `## Deviations` section of `plan.md`, which must be re-graded
once filled in.

**What good looks like.** A real unknown is named as one ("I have not
confirmed whether the same pattern exists at the two sibling call sites;
I will check and note it in the PR") rather than silently folded in as
settled. A deferral of related work states the reason for deferring, not
just the fact of it. This family does not gate the verdict on its own in
this rubric (`risks-named` is preferred), but it is the lens for reading
`## Deviations`: a deviation recorded honestly, in the student's own words,
is what this family rewards even when the deviation itself is "nothing
changed."

## Comms

**Where it lives.** In an eval bundle: `## Candidate plan comment`, read
against `## Thread highlights` for any maintainer-badged (OWNER, MEMBER,
COLLABORATOR) comment that already proposes a cause, confirms a location,
or posts a fix/patch and asks for something — and against the `## Repo
facts` block's contribution-policy line for any AI-use disclosure
requirement. In live mode: the draft plan comment, read against the live
issue thread and the repo's CONTRIBUTING.md / AI_POLICY.md / AGENTS.md.

**What good looks like.** Two separate things, graded separately.

*Thread.* When a maintainer has already settled a direction in the thread
— named a cause, pointed at a file, posted a patch and asked for testing —
the plan comment has to engage it: build on it, or say plainly why it is
taking a different path instead. A comment that proceeds as though the
thread said nothing, when it said something specific, fails here even if
the comment is otherwise well-written. When the thread has no maintainer
direction yet, there is nothing to engage, and the check imposes no duty.

*Policy.* Read what the stated policy actually requires. A policy that
requires **disclosure** ("all AI usage must be disclosed, stating the tool
and the extent") obliges the comment to say so, naming the assistance. A
policy that only requires **understanding and testing** ("only submit code
you fully understand and have tested") or **human-written comments**
obliges nothing about disclosure. Silence obliges nothing. Course work is
AI-assisted, so wherever a disclosure duty exists, it always applies.

## Prior art

**Where it lives.** `## Thread highlights` and any linked PRs named there
or in the issue's development sidebar, read against the plan's approach.

**What good looks like.** The plan acknowledges an existing attempt — an
open PR at the same fix, a prior closed attempt, a related issue someone
else is working — and says how its own approach relates to it, rather than
proceeding as if it were the first and only attempt. This is a preferred
signal: its absence never fails a plan on its own, but its presence is what
separates a plan that is merely correct from one a maintainer can route
without duplicated work.
