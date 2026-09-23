# Scope: where to look, and who is looking

<!--
This file is the skill's field of view. The rubric (rubric.md) decides
whether an issue is GOOD; the scope decides which issues are candidates
at all, and whose hands the issue would land in. It applies in live mode
only: in eval mode the bundle is the whole world and this file is
ignored.

Two parts. Staff wrote the first; you write the second.
-->

## Where candidates come from

Only issues in the course's Path Review repository are candidates:

- Repo: `codepath/pathreview-ai301-fa26-s1`

Do not search, fetch, or grade issues from any other repository, however
promising. The wider GitHub comes later in the course; for now the field
is Path Review.

**Path Review house rule.** Path Review is a classroom, and your
classmates are not strangers. Ignore the usual claim signals here: other
students' claim comments (and there may be several on one issue) do not
block an issue, and finding some on the issue you want is normal. Claim
anyway: course credit attaches to the pull request you open, not to
whether it merges, so a shared issue costs nobody anything. Everything
else in the rubric applies as written.

## Your fit profile

<!-- YOU write this part: a few sentences about you. What languages and
tools you have actually used, what you want to get better at, anything
you want to avoid. The skill uses this only to RANK the issues your
rubric accepts, never to change a verdict: fit cannot rescue an issue
your rubric rejects, and cannot sink one it accepts. -->

I am most comfortable in Python: scripts, CLIs, small backend services, and
the usual test runners around them. I have read and edited other people's
Python more often than I have designed my own, so a bug with a named file
and a reproducible failure suits me better than an open design question.

What I want to get better at is working inside an unfamiliar codebase:
finding the right file, matching the surrounding style, and writing a test
that would have caught the bug.

Rank down issues whose work is mostly frontend (CSS, layout, visual
polish), and issues that need a toolchain I would have to install and learn
before I could reproduce anything. Rank up issues where the fix is in
Python, the failing behaviour is described concretely, and the repo has a
test suite I can run.
