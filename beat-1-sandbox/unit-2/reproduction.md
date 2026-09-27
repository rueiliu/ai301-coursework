# Unit 2 — Claim and Reproduce

Path: `beat-1-sandbox/unit-2/reproduction.md`

Record of your claim and reproduction on the issue you chose in Unit 1, and of the
evaluation runs that produced `eval-run.txt`. This file is graded at the path above; a copy
kept anywhere else in the repository is not read.

Complete every labelled field below. Each is graded on its own; content placed under the wrong
label is not graded.

---

## Your identity upstream

**GitHub username**

rueiliu

---

## Posted upstream

**Claim comment**

[Link to the comment where you claimed the issue. Use the comment's own permalink, not the
issue page on its own. **Then paste the text of that comment underneath the link** — the
pasted text is what this field is graded on, so copy across what you actually posted.]

**Reproduction comment**

[Link to the comment where you posted your reproduction. It must record the environment
(OS, relevant versions, code state), steps a stranger could follow, and what you observed.
**Then paste the text of that comment underneath the link** — the pasted text is what this
field is graded on, so copy across what you actually posted.]

## Eval iterations

Answer all four sections. Quote source text directly; paraphrase does not satisfy these
fields.

**Run history**

Two runs, in order:

1. Partial run, `--only pkg-03,pkg-09,pkg-20`: **3/3**. I did not use `--limit` for this.
   I picked the three packages that would break the three riskiest things in my rubric:
   `pkg-09` for the honest-cannot-reproduce carve-out in `behavior-matches` (the only rule
   I wrote that lets an artifact *not* show the issue's failure and still pass), `pkg-20`
   for the single-package `disclosure` category the floor exists for, and `pkg-03` for the
   distinction my `comms-conventions` check turns on — a policy that requires human-written
   comments is *not* a disclosure requirement, so it must pass without one. All three agreed,
   which told me the design held before I spent $4. The harness printed
   `categories: clear-accept 2/2  disclosure 1/1` and reminded me that only a full run
   decides the bar.

2. Full 20-package run: **18/20**. This is the run committed as `eval-run.txt` in this
   directory, and these two lines are quoted from it:

   ```
   categories: clear-accept 6/8  disclosure 1/1  no-evidence 4/4  unfollowable-comms 3/3  wrong-target 4/4
   agreement: 18/20 scored items  (bar: 18/20: PASS)
   ```

I stopped there rather than revising and re-running. The run clears the bar and matches every
category, the two disagreements are both the same diagnosed defect rather than two separate
blind spots, and a revision would have meant a fresh confirming full run to keep the uploaded
`rubric.md` consistent with the fingerprint recorded in `eval-run.txt`. I would rather submit a
passing run I can explain than buy two tally points that carry no marks.

**Package analysis**

`pkg-05` (conda, the `--json` stream polluted by an `EnvironmentSectionNotValid` warning).

- My rubric decided: **reject**, on `steps-followable` (and `control-run`, which is
  `preferred` and could not have changed the verdict).
- Gold label: **accept**, category `clear-accept`, noted as "minimal env.yml repro with a
  json.tool parse failure as the artifact".

Why my rubric read it that way. My `steps-followable` pass condition requires the starting
state to be "a public clone, a released artifact, or an input included verbatim in the
package". `pkg-05`'s steps say the author "wrote a minimal `env.yml` containing a valid
`dependencies:` list plus a `category:` section (the section conda does not recognize)". The
file is *described*, precisely, but its contents are never pasted. My check read "described,
not pasted" as "not included verbatim" and failed it.

The check is doing something real — it is the same clause that correctly rejects `pkg-18`,
where the author says the reproduction ran against "our company monorepo (private; I cannot
share it or its layout)". But I wrote the condition around the wrong property. What actually
matters is whether a stranger can **reconstruct** the input, not whether the author **pasted**
it. A two-line YAML file described down to the offending key is reconstructible in seconds;
a private monorepo with an unshared config is not. My wording cannot tell those apart, so it
catches both. `pkg-12` failed for exactly the same reason — its `repro.mjs` is described as
"containing the issue's two `prettier.format` calls" with the range offsets given, which is
reconstructible, and is in fact sourced from the issue itself.

Both of my disagreements are this one defect, not two.

**Check rationale**

Quoted from `tools/repro-check/rubric.md` exactly as it currently reads, the
`steps-followable` row:

> | `steps-followable` | The reproduction steps in the repro report, read as a stranger with
> no access to the author's machine. | A stranger could re-run the attempt from the steps
> alone: the starting state is one they can reach (a public clone, a released artifact, or an
> input included verbatim in the package), and the trigger is given as the exact command,
> input or action, not a description of one. Steps that depend on a private repository, an
> unshared config or fixture, or an unnamed setting the failure needs, fail. | required |

Why it reads that way, and what I rejected to get there. The thing I deliberately rejected was
a count: "the steps are numbered and there are at least three of them", or "the report has a
Steps section". The Unit 2 rubric template warns that structure-shaped checks are what make
graders disagree with themselves, and the lecture's re-run demonstration showed a
"has at least five sections" rule scoring 17/17/18 across three runs against 20/20/20 for a
rule with nothing to interpret. So I wrote the condition around an outcome — can a stranger
re-run this — and named the concrete ways that fails: a private repository, an unshared
config, an unnamed setting.

What I would change if I spent another run on it is the clause in the middle. "An input
included verbatim in the package" is the part that mis-fires, because it tests how the author
formatted the input rather than whether a stranger can obtain it. The repair is to make the
condition reconstructibility: an input that is described precisely enough to rebuild, or that
the issue itself already carries, passes; an input that cannot be obtained at all fails. That
keeps `pkg-18` rejected for the right reason and stops `pkg-05` and `pkg-12` being rejected
for the wrong one. I left the check as it stands so that the rubric in this folder is the one
that produced the committed `eval-run.txt`.

**Trade-offs**

This check changes the result of two packages, and both are false rejects: `pkg-05` and
`pkg-12`, gold `accept`, my verdict `reject`, both on `steps-followable`. Those two
disagreements are the entire gap between my 18/20 and a clean 20/20, and the `note` column of
the committed run names the check on both rows.

What it gives up is stated plainly: as written, the check fails a report whose author
summarised a small input instead of pasting it, even when the summary is complete enough to
rebuild the input. It buys that cost for a real catch — `pkg-18`, where the reproduction ran
inside a private monorepo against an unshared config and no reader can re-run any step. I
would rather a first-issue rubric err toward "a stranger cannot re-run this" than toward
accepting proof nobody can check, but two of eight clear accepts is too high a price for that
bias, and the fix does not require giving up the catch.

The bias also shows up in the direction of the errors overall: every one of my 12 rejects
matched gold, and both of my misses were accepts I held. My rubric is systematically strict
rather than randomly wrong, which is the more comfortable failure mode to carry into live mode
— it holds my own work back rather than letting bad proof through — but it is still a defect,
and it is the one thing I would fix first in Unit 3.

---

Related paths: `eval-run.txt` in this directory; your skill's files in
`tools/repro-check/`.
