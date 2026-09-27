# Evidence guide: where proof lives in a reproduction package

## Environment

**Where it lives.** In an eval bundle: the first lines of the
`## Candidate repro report`, usually an `Environment:` line; read it against
the `## Issue` section (the version the reporter was on, and any version the
thread later confirms) and against the `latest release` and `bug reports`
lines in the `## Repo facts` block, which say what build the template asks
reporters to be on. In live mode: the `Environment:` line of the draft
report, read against the issue body, the repo's bug-report template in
`.github/`, and the Releases page for what "latest" currently means.

**What good looks like.** It names the exact build under test — a release
version, tag, or commit SHA, not "latest" or "main" alone — and the platform
that build ran on, including whatever the issue's failure actually depends
on: OS and architecture for a native crash, the driver for a container-runtime
bug, the shell for a prompt bug, the build profile when debug and release
fail differently. When the build differs from the one the issue targets, the
report says so in words and says why that is still informative. A record that
silently tests an older version than the issue was confirmed on is worse than
no record, because it reads as agreement while testing something else.

## Steps

**Where it lives.** In an eval bundle: the steps block of the
`## Candidate repro report`, read against the reproduction steps in the
`## Issue` body. In live mode: the steps in the draft report, read against
the issue's own steps and the repo's README and CONTRIBUTING for how the
project expects to be built and run.

**What good looks like.** A stranger could re-run it from the steps alone.
The starting state is reachable by anyone — a public clone at a named
revision, an installed release, or an input file whose contents are pasted
into the package — and each step is the literal command, input, or action,
with the flags and the file it ran on. The trigger the issue names is the
trigger the steps use. Steps fail this bar when they depend on something
only the author has: a private monorepo, a config or fixture that is
referenced but not shown, a setting the failure needs that is never named.
Terseness is not the problem; three exact commands beat ten narrated ones.

## Behavior shown

**Where it lives.** In an eval bundle: the fenced output excerpts, logs, exit
codes and transcripts inside the `## Candidate repro report`, read against
the artifacts and error text quoted in the `## Issue` body and anything the
`## Thread highlights` add about what the real failure looks like. In live
mode: the code blocks in the draft report, read against the issue thread.

**What good looks like.** The artifact exhibits the issue's failure, not a
neighbouring one, and it came from the issue's input. Compare three things
before deciding: the input that produced the artifact against the input the
issue names; the failure mode against the issue's failure mode; and the exit
code or error identity against the issue's. A graceful validation error is
not a crash. A compile error is not a runtime error. Exit 1 is not exit 101.
An artifact that shows only that the program starts, prints a banner, or
lists its state shows nothing about the bug. This is the check most often
defeated by presentation: a long, well-formatted, confident report whose one
code block shows the wrong failure is still the wrong failure, and a plain
six-line report whose block shows the right one is proof.

An honest cannot-reproduce is read differently. There the artifact is not
expected to show the failure; it is expected to show a real attempt at the
trigger the issue names, and the report is expected to claim nothing more
than that the attempt did not produce it.

## Honesty

**Where it lives.** At the seam between what the package asserts and what it
shows: the assertions are in the prose of the `## Candidate claim comment`
and the narration around the artifacts in the `## Candidate repro report`;
the backing is in the artifacts themselves. The report's own "expected" and
"actual" lines are where the two are most often inconsistent.

**What good looks like.** Every claim has its receipt inside the package. A
stated reproduction is accompanied by the failing run. A stated root cause
("a debounce race", "a regression in the dependency") is accompanied by the
observation that points at it, not just by confidence. Words like
"guaranteed", "verified", "confirmed on two machines" raise the bar rather
than meet it: each needs the run behind it. The "expected" line describes
what the issue says should happen and the "actual" line describes what the
artifact shows, not the reverse and not the author's assumption.

A report that says plainly "I could not reproduce this", shows the attempt,
and names what differed between its environment and the issue's is fully
honest and is worth posting; it tells the maintainer something real. The
failure mode to catch is the opposite one: enthusiasm with no artifact, or
a confident diagnosis resting on an artifact that does not support it.

## Comms

**Where it lives.** In an eval bundle: the `## Candidate claim comment` and
the comment text of the `## Candidate repro report`, read against the
`contribution policy` and `bug reports` lines of the `## Repo facts` block
and against the specifics in the `## Issue` title and body. In live mode:
the draft comments, read against the repo's CONTRIBUTING.md, any AI_POLICY.md
or AGENTS.md, the issue and PR templates in `.github/`, and the issue itself.

**What good looks like.** Two separate things, and they fail separately.

*Policy.* Read the stated policy for what it actually demands, because the
demands differ in kind. A policy that requires **disclosure** ("all AI usage
must be disclosed, stating the tool and the extent of the assistance")
obliges the comment to say so, in the comment, naming what the assistance
was. A policy that only requires **understanding and responsibility** ("only
submit code you fully understand", "assistive AI use is allowed, and the
contributor must take responsibility") obliges nothing in the text of the
comment. A policy that requires **human authorship** of comments ("comments
to maintainers must be written by humans in their own words") is satisfied
by a comment that reads as a person wrote it, and is not a disclosure rule.
Silence obliges nothing. Course work is AI-assisted, so a disclosure duty,
where one exists, always applies.

*Claim.* The claim comment is specific when it could not be pasted onto a
different issue: it names the version, the behavior, or the file at stake.
It is honest when it promises only what the author controls — an
investigation, a report, a finding — and never a fix, a merge, or a date.
"Please assign me, I'll have this fixed in two days" fails both halves at
once: it is interchangeable and it promises an outcome. Boilerplate warmth
("great project!!", "+1 any update?") is not a comms failure on its own; it
is a comms failure when nothing specific sits underneath it.
