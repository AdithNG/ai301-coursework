# Evidence guide: where proof lives in a reproduction package

## Environment

**Where it lives.** In an eval bundle: the repro report's environment
line(s), usually right after "Environment:" — tool version, install
method, OS/platform, and any extra dimension (driver, build profile,
config, browser/language). Compare it against the issue's own header
facts and the repo-facts block's `contribution policy`/template lines,
and against anything the thread pins down as the confirmed-reproducing
configuration. In live mode: the same "Environment:" block in the
draft report, checked against the live issue's stated version, the
repo's bug-report template (it usually asks for a version and OS), and
any comment that narrows which configuration reproduces.

**What good looks like.** Every dimension the issue calls out as
behavior-determining has a concrete value in the report — not just
tool version and OS, but whichever axis the issue itself turns on
(debug vs release build, a specific driver, a specific browser-language
order, a config flag). A value that differs from the issue's own is
fine as long as the report says so and reasons about it ("filed against
13.0.0; unchanged on 15.2.0") rather than silently substituting an
untested version and claiming the same conclusion.

## Steps

**Where it lives.** The repro report's numbered steps or command
transcript, read against the issue's own reproduction steps or minimal
example. In live mode, also check whether any input, config file, or
codebase the steps depend on is something a reader could actually get
(a public repo, a snippet given in full, a public playground link) or
is described as private/internal with no substitute offered.

**What good looks like.** Someone with only the stated environment and
what is public on the issue could run the same commands against the
same starting state and land at the same trigger point. The literal
input matches the issue's trigger (same flag shape, same syntax, same
file contents) unless a deviation is named and justified. A step whose
essential material cannot be shared ("our internal config, not
shareable") fails this even if the reporter insists the situation
matches, because nobody else can check it.

## Behavior shown

**Where it lives.** The repro report's output excerpts, error text,
logs, or screenshot descriptions, read side by side with the issue's
own description of the symptom: its exact error message or panic
signature, its exit code, or its visible defect (crashes vs. doesn't
crash, corrupts vs. renders correctly).

**What good looks like.** The artifact demonstrates the same symptom,
not a nearby one produced by a slightly different input or a different
code path. Read the error text and the exit behavior literally: a
validation error is not a panic, a different exception class is not
the reported one, and "the window stayed open and the prompt returned"
is not a crash when the issue is specifically about crashing. A report
that shows only environment/version confirmation, or only a narrated
diagnosis of the suspected code with nothing actually run, has not
shown behavior at all, whatever confidence the prose carries.

## Honesty

**Where it lives.** The gap between the report's stated conclusion
("confirmed," "reproducible," "cannot reproduce") and what its own
artifacts actually contain.

**What good looks like.** The claimed outcome is exactly as strong as
the shown evidence. An honest cannot-reproduce report is a pass when it
is backed by a real attempt: environment stated, steps taken, and
(where it can tell) what may have differed from the original report —
that is a complete, useful answer, not a lesser one. A report is
dishonest by this guide's standard when its language ("100% confirm,"
"conclusively demonstrates," "exactly as described") outruns an artifact
that shows something adjacent, or outruns having run nothing at all.
Confidence is never evidence; read past the adjectives to what was
actually executed and observed.

## Comms

**Where it lives.** The claim comment's own wording, read against two
things: the repo's stated conventions (a bug-report/PR template, a
contribution policy, an AI-use disclosure requirement) in the
repo-facts block or `CONTRIBUTING.md`/`AI_POLICY.md`, and this course's
own claim norm (promise an investigation, never a fix or a date).

**What good looks like.** The claim states what the contributor has
done or will do to investigate, and nothing more: no demand to be
assigned or to have the issue "reserved," no promised fix, no delivery
date or guarantee. Where the repo's policy requires disclosing AI
assistance, the disclosure is explicit and names the tool and the
extent of the help ("I used an AI assistant to help me organize this
report; I ran and verified every step myself") — not implied, not
omitted because the report happens to read cleanly. Silence in the
policy on AI use is not a disclosure requirement; only a stated policy
creates one.
