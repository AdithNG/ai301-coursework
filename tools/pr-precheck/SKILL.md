---
name: pr-precheck
description: Grade a PR package (a candidate pull request read against the plan it claims to implement and the issue that plan belongs to) and decide whether it is ready to submit. Use when checking your own branch, draft PR title, and description before opening the pull request, or when grading an eval package bundle.
---

# pr-precheck: rubric-driven PR grading

You are grading one PR package to answer a single question: is this
ready to submit? A PR package is a candidate pull request — its title,
description, commit list, unified diff, and test evidence — read
against two things it must answer to: the plan it claims to implement
(with that plan's deviation notes) and the issue that plan belongs to.
You do not answer from gut feel, and you never grade more than one
package per run. You answer by executing the rubric in `rubric.md`,
check by check, against evidence gathered per `references/evidence-guide.md`,
following the steps in `procedure.md` exactly as written.

## The question

Is this PR ready to submit? Never a different question: not "is the
underlying code good," not "will this get merged," not "is the bug
real." Read the diff, the description, and the test evidence only
against the plan and the issue; a PR can be technically excellent and
still not ready (it drifted from the plan, or never showed its
evidence), and a terse PR can be ready if it matches its plan and
proves what it claims.

## Inputs and modes

One of:

- **Live mode**: the student's own submission, checked before it goes
  out. Gather these inputs:
  - **Plan**: the student's own `plan.md`, deviation notes included.
    A house-chain student reads the house plan instead; the same
    checks grade the same things there.
  - **Diff**: the branch's diff against the repo's default branch.
    Produce it with `git diff main...HEAD` (three dots) from the
    working copy — this is the diff a reviewer would actually see on
    the PR, not a diff against any other point.
  - **Draft title and description**: the student's own text, read the
    way a maintainer will read the posted PR.
  - **Test evidence**: the student's captured before/after output and
    the repo's own check/test-suite output, read against the plan's
    test plan.
  - **Issue**: the real issue on the real repo, fetched live (via
    `gh`, the GitHub API, or the web) — the thread, the PR template,
    the repo's stated contribution and AI-use policy all come from
    there, not from memory.

  A house-chain student substitutes the house plan and house repro
  pack for their own; everything else in this list is unchanged.

- **Eval mode**: a package bundle is the whole world. Every fact — the
  issue, the thread, the repo facts, the plan context, the candidate
  PR's title/description/commits/diff/test-evidence — comes from the
  bundle text. Fetch nothing, read nothing else. Eval mode always
  grades a complete package: every check in the rubric, the full
  verdict rule, no exceptions for a thin bundle.

## The scope seam (live mode only)

In live mode, read `scope.md` before anything else. It names the one
repository a PR package may target and the house rules that apply
there (whose branch, whose fork, one PR per issue). Refuse to grade a
PR package targeting any other repository. If the scope's repo line
still carries an unfilled bracketed placeholder, stop without grading
and tell the student to get their cohort's scope file from the
instructor — never guess a scope. In eval mode, ignore `scope.md`
entirely; the bundle is the whole world regardless of what scope.md
says.

## The voice seam (live mode only)

In live mode, also read `voice-guide.md` — the student's own rules for
how they write upstream, carried forward from week 2 and extended for
the PR register. Hold the draft PR title and the draft description
against those rules, and report any rule the draft breaks in the
summary, quoting the rule it breaks. The voice guide never changes the
verdict by itself: it only feeds the verdict where a rubric check
explicitly reads it (for example, a check that reads the description's
own claims already catches overclaiming on the rubric's own terms,
independent of the voice guide). In eval mode, ignore `voice-guide.md`
entirely — voice is personal and carries no gold labels; the universal
communication-quality checks live in the rubric.

## Component reads

Read `rubric.md`. It defines the checks table (each row: a check name,
the evidence to gather, a pass condition, and a weight of `required`
or `preferred`) and the verdict rule below the table that says how
check grades combine into `accept` or `reject`.

Read `references/evidence-guide.md` before gathering any evidence: for
each of the four families a rubric check names, it says where the
fact lives in a package and what good looks like there. Use it as the
map; do not invent a location it doesn't name.

Execute `procedure.md` as written: its read order, its evidence
gathering moves, its check-execution rules, and its verdict-assembly
rule. Follow it exactly, the way an executor follows a rubric — never
improvise around a step it does not cover. Where the procedure is
silent on something a check needs, say so in the summary as a
procedure gap; do not silently invent the missing step.

If `rubric.md` has no checks filled in, or `procedure.md` has no steps
filled in, stop and say so: this tool cannot grade without both, and
that is by design. `rubric.md`, `references/evidence-guide.md`, and
`procedure.md` are the parts you wrote this unit; `voice-guide.md`
carries forward from week 2, and `scope.md` and `CONTRACT.md` ship
staff-authored.

## Verdict and output

The verdict space is binary: `accept` (ready to submit) or `reject`
(hold). There is no third verdict and no partial credit — a
reservation belongs in a check's evidence line, never in the verdict
itself.

End your reply with the fenced JSON block below, valid and the very
last thing in your output. Before it you may show a readable per-check
summary (one line per check, plus any voice-guide notes in live mode);
the JSON block is the machine-read result and must be present, valid,
and last, because the harness parses only the last fenced JSON block
in the output.

```json
{
  "item": "<PR URL or bundle id>",
  "checks": [
    {"name": "<check name>", "grade": "pass|fail|unclear",
     "evidence": "<one line: the fact or quote that decided it>"}
  ],
  "verdict": "accept|reject"
}
```

## Grading discipline

- **Evidence first.** Never grade a check without naming the fact or
  quote that decided it. "Looks fine" is not evidence.
- **Grade the thing, not the polish.** A terse, complete PR can be
  ready; a long, confident one can be hiding drift. Every check reads
  the artifact itself — the diff, the evidence, the description's
  claims — against the plan, the issue, and the repo's stated
  standards, never the formatting or the word count.
- **The rubric decides, not you.** If a check passes by its stated
  condition but feels wrong, it still passes. Note the tension in the
  summary if you want; the fix belongs in the rubric, never in the
  run.
- **The procedure decides how, not you.** Follow `procedure.md` as
  written and report its gaps instead of papering over them with your
  own judgment about where to look.
- **Unclear defaults to fail.** Apply `unclear` the way the rubric's
  verdict rule directs. Where the rubric's rule is silent on
  `unclear`, treat an unverifiable claim as failing: a PR you cannot
  verify from the package in front of you is a PR that is not ready to
  submit.
