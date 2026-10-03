# Procedure: how this skill grades a plan package

## Read order

1. Read the issue context first: title, body, and labels. Note the behavior
   the issue reports and any specific location or cause the reporter or a
   maintainer already names.
2. Read the thread highlights (or the live thread) next, in order. Note any
   point where a maintainer (OWNER, MEMBER, or COLLABORATOR) proposes a
   cause, confirms a location, points at a fix, or posts a patch or test
   build and asks for something back. This is "settled direction": write it
   down verbatim if it exists, and write "none settled" if it does not.
3. Read the repro evidence next, start to finish, including every control
   run. For each step, note what it shows and what it rules out. A control
   that isolates one variable (a flag removed, a path substituted, an
   alternate context) is read as evidence against any cause that the
   control's outcome contradicts, not just as background detail.
4. Read the repo-facts block: the contribution policy line and any AI-use
   policy it states. Classify it as one of: requires disclosure, requires
   understanding/testing only, requires human-written comments only, or
   silent. Only the first classification creates a disclosure duty.
5. Read the candidate plan in full, then the candidate plan comment in
   full. Do not grade anything until all of the above has been read once.

## Evidence gathering

- **Diagnosis and grounding**: pull the plan's stated cause, one sentence
  if possible. Pull every control run and step from the repro evidence
  gathered in Read order step 3. Hold them side by side.
- **Scope**: pull the plan's in-scope and not-in-scope statements and its
  named files/areas. Pull the issue's own description of the defect (one
  behavior, one location) from Read order step 1.
- **Executability**: pull the plan's named files/locations and its chosen
  approach for the core fix. Note any sentence that defers a decision about
  the core fix itself to build time, as opposed to a secondary detail.
- **Test plan**: pull the plan's stated test plan verbatim. Pull the
  repro evidence's exact commands, inputs, and artifacts (exit codes,
  printed values, specific strings) from Read order step 3.
- **Comms**: pull the plan comment verbatim. Pull the "settled direction"
  note and the AI-policy classification from Read order steps 2 and 4.
- **Risks and prior art** (preferred checks): pull the plan's stated risks
  or deferrals, and any linked PR or prior attempt named in the thread
  highlights or the issue's development sidebar.

## Check execution

1. Grade `diagnosis-grounded` first: does any control or step gathered
   above contradict the stated cause? If yes, fail and quote the
   contradicting step; this check decides the verdict regardless of how
   well-written the rest of the plan is, so grade it before anything else
   can anchor the read.
2. Grade `scope-bounded`: compare the plan's named changes against the
   issue's one reported defect. A plan whose changes stay at the
   implicated site(s), with any excluded related work stated and reasoned,
   passes. A plan listing unrelated improvements, migrations, or new
   abstractions alongside the fix fails, quoting the item that goes beyond
   the issue.
3. Grade `executable`: scan the approach for a named file/location and one
   committed choice for the core fix. A hedge on a genuinely secondary
   detail does not fail this check; a hedge on the fix itself does.
4. Grade `test-plan-decisive`: compare the stated test plan against the
   repro evidence's exact artifacts. A specific, checkable outcome tied to
   this fix passes; a generic standard with no outcome specific to this fix
   fails.
5. Grade `comms-conventions` last, as two sub-grades that both must hold:
   thread engagement (does the comment engage any settled direction from
   Read order step 2, or is none settled) and policy (does the comment
   meet the disclosure duty classified in Read order step 4, if any).
   Record both sub-grades; the check fails if either one does.
6. Grade `risks-named` and `prior-art-engaged` from the evidence gathered
   for them. These never change the verdict.
7. If the evidence a check needs is genuinely absent from the package (not
   merely unclear after reading), grade that check `unclear` rather than
   guessing, and say what was missing. Do not re-read the whole package to
   grade a later check; the notes from Evidence gathering are sufficient
   once taken.

## Verdict assembly

1. Apply the rubric's verdict rule: `accept` only if `diagnosis-grounded`,
   `scope-bounded`, `executable`, `test-plan-decisive`, and
   `comms-conventions` all grade `pass`. Any one of them at `fail` or
   `unclear` makes the verdict `reject`.
2. `preferred` checks (`risks-named`, `prior-art-engaged`) are reported but
   never entered into the accept/reject decision.
3. In the output summary, name the single check that decided a `reject`
   verdict and quote the fact that failed it; if more than one `required`
   check failed, name all of them. For an `accept` verdict, note which
   `preferred` checks passed, since those are what make an accepted plan
   easier to act on.
4. The same inputs must always produce the same verdict: if a second
   grading pass of the same package would read the evidence differently,
   that is a procedure gap, not a judgment call — note it rather than
   resolving it silently.
