# Rubric: is this a good first issue?

## Checks

| Check | Evidence | Pass condition | Weight |
|---|---|---|---|
| `repo-alive` | Repo facts: the `archived:` flag on the repo line, and the dates in "last 5 default-branch commits" | `archived: no`, AND the newest of the last 5 default-branch commits is dated within 90 days of the bundle's capture date (live mode: within 90 days of today). A commit authored by a bot counts only if it merges a human's pull request. | required |
| `unclaimed` | Repo facts: `this issue: assignees:` and `linked PRs:` with each PR's state. Plus every comment in the thread, with its date and `author_association` | All three hold: (1) `assignees: none`; (2) no linked PR in `open` state (a `closed` or `merged` PR is a finished or abandoned attempt, not a claim); (3) no comment dated within 120 days of the capture date in which someone says they are taking, picking up, or working on the issue. An older claim than 120 days is stale and does not block; a maintainer replying "go ahead, it's free" clears clause 3 whatever the date. | required |
| `scope-bounded` | The issue title, `body_markdown`, its labels, and the comment thread | The issue asks for one bounded change, meaning NONE of these disqualifiers is present: (a) it presents itself as an umbrella, tracking, or mega issue, or asks for a change across the codebase rather than in one named place (title or body says "megaissue", "tracking", "across the codebase", or lists independent sub-tasks meant to be split into separate PRs); (b) the thread runs 20 or more comments with no maintainer comment settling on one approach; (c) a maintainer says in the thread that the fix touches core internals, or that the design is still undecided; (d) it is a feature request with neither acceptance criteria in the body nor any endorsement from a maintainer (no `help wanted` / `good first issue` label, and no OWNER / MEMBER / COLLABORATOR comment supporting it). A terse bug report with reproduction steps, or a docs task with a named target file, is bounded and passes. A pure usage question ("how do I get this to work?") fails. | required |
| `policy-allows-ai` | Repo facts: the "contribution policy" line, including any AI policy it quotes or summarizes | The policy does not ban AI-assisted contributions outright. Stated conditions pass: disclosure, human review, personally understanding and testing the change, or closing PRs that appear untested. Silence passes ("no statement on AI"). Only a flat refusal to accept AI-written code or docs fails. | required |
| `shipped-recently` | Repo facts: the "latest release" line | A release dated within 365 days of the capture date. A repo with no releases at all does not pass this check, but it is not disqualified by it: commit activity carries liveness for repos that never tag releases. | preferred |
| `responds-to-issues` | Repo facts: the "maintainer first-response sample (5 recently updated issues)" | At least one issue in the sample got a first response from an owner, member, or collaborator, at any latency. | preferred |

## Verdict rule

Accept only if every `required` check passes. A single `required` fail is a
reject. `unclear` on a `required` check counts as a fail: a first issue I
cannot verify is not one I should take. `preferred` checks never change the
verdict; they rank the issues that are already accepted, and an accepted
issue that passes both preferred checks outranks one that passes neither.
