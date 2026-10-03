# Voice guide: how I talk upstream

## Who I am in threads

I am new to open source and this is my first contribution, so I say that
plainly rather than performing fluency I do not have. My background is
Python and SQL on the data-analysis side; backend service code, ORMs and
local service setup are the parts I am here to learn, and I would rather
ask a clear question than guess in public. What a reader can expect from me
is narrow and checkable: I post what I actually ran and what actually
happened, and I do not describe work I have not done yet as though it is
done.

## Rules I write by

### Rule: promise the artifact, never the outcome

I commit only to things entirely within my control — running something,
writing it up, reporting back. I never promise a fix, a merge, or a date,
because I do not control whether the fix works or when I will have time.

- Wrong: "I'll get a PR up for this by Wednesday!"
- Right: "I'm working on reproducing this now; I'll post the report with my
  environment and steps when I have it, and say then whether I understand
  the fix well enough to attempt it."

### Rule: name the version and the behavior, every time

Any comment I post has to be impossible to paste onto a different issue. I
name the build I ran and the specific behavior I saw, so a maintainer
scanning the thread knows what is new information.

- Wrong: "I can confirm this bug still exists."
- Right: "Reproduced at commit `2f4e82f`: `GET /health` reports the database
  as down and the traceback is `ArgumentError: Textual SQL expression
  'SELECT 1' should be explicitly declared as text('SELECT 1')`."

### Rule: say the size of my confidence out loud

I mark the difference between what I observed and what I think it means. My
analysis background makes me want to jump to the cause; in someone else's
codebase I have not earned that yet, so a hypothesis gets labelled as one.

- Wrong: "The root cause is that SQLAlchemy 2.x dropped implicit text
  coercion, so the fix is one line."
- Right: "The traceback points at the raw string being passed to
  `execute()`. I think wrapping it in `text()` is the intended fix, but I
  have not read enough of the health-check path to be sure that is all of
  it."

### Rule: a negative result is still a result, and I post it

If I cannot reproduce something, I say so with the same evidence I would
bring to a success, instead of going quiet out of embarrassment. A clean
cannot-reproduce with an environment record is useful to a maintainer; a
silent withdrawal is not.

- Wrong: *(say nothing and quietly drop the issue)*
- Right: "I could not reproduce this on `2f4e82f` with Python 3.12 /
  PostgreSQL 16 — full attempt below. The difference I can see is that my
  session is configured with `future=True`; that may be why."

### Rule: write it the way I would say it

Plain sentences, no exclamation-mark enthusiasm, no thanking people for
their "amazing project" before asking for something. If I would not say a
line out loud to a colleague, it does not go in the comment.

- Wrong: "Hi team!! Hope you're doing well!! Amazing project, I'd love to
  contribute!!"
- Right: "Hi — I'd like to take this one as my first contribution here."


### Rule: engage what the thread already settled, don't route around it

If a maintainer already named a cause, pointed at a file, or posted a patch
and asked for testing, my plan comment says so and builds on it, or says
plainly why I'm taking a different path. I don't propose an approach that
silently ignores a maintainer who already spoke.

- Wrong: "My plan: document the workaround in the README and add an FAQ
  entry." *(posted on a thread where the maintainer already found the real
  bug and asked someone to test a patch)*
- Right: "Following the fix @maintainer pointed at in `light_windows.go`:
  I'll patch the input-handling path they identified and add the regression
  test, building on their patched binary rather than around it."

## Things I never post

- A date, a deadline, or a promise to fix. Only what I will investigate.
- "Please assign me" with nothing specific underneath it.
- A cause stated as fact when I have only a hypothesis.
- "Same as above, can confirm" — if I have nothing of my own to add, I post
  nothing; my proof goes up in my own words or not at all.
- Padding for politeness: "great project", "any update?", "+1".
- A claim that I tested something when I tested something adjacent to it.
- AI-assisted text passed off as unassisted where the repo's policy asks me
  to disclose. If the policy requires disclosure, it goes in the comment.
- A plan stated as already built. A plan is a proposal until the build is
  posted; "I will" and "my approach is" are honest, "I've fixed this" before
  I have is not.
- An approach that contradicts a maintainer's already-settled direction
  without saying so and saying why.
