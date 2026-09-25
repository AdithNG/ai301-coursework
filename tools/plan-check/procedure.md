# Procedure: how this skill grades a plan package

## Read order

1. Read the repo-facts block first. Note down, in this order: the
   contribution policy's AI-disclosure stance (none stated / silent /
   required), and the bug-report template asks (not scored directly,
   but useful context for check 3).
2. Read the issue itself (title, body, labels). Note the reported
   symptom in one sentence: what a user observed, not why.
3. Read every line of `## Thread highlights` (live mode: the live
   thread). Note down any maintainer signal: a stated direction, an
   in-progress patch or commit, an explicit preference for one kind of
   fix over another, or an explicit "this part is hard" caveat. If
   there is no such signal, note that explicitly ("no maintainer
   direction stated") rather than leaving a blank.
4. Read the `## Repro evidence` block (live mode: the student's posted
   week-2 repro comment, or the house repro pack) before reading the
   plan. For every numbered step and every control run in it, note the
   exact fact it establishes ("control run with X removed: behavior
   stays normal" / "artifact pins the defect to stage Y, before stage
   Z runs"). This list is what `diagnosis-grounded` checks against, so
   build it before you have seen the plan's own claim — reading the
   plan first anchors you on its story instead of the evidence's.
5. Only then read the candidate plan in full, then the candidate plan
   comment. Note the plan's stated cause, its scope statement and full
   change list, its named files/steps, its test plan, and any stated
   risk — one line each.
6. Live-mode only: read `scope.md`, confirm the issue is in the scoped
   repo, and note any house rule that changes how a check reads
   evidence here. Then read `voice-guide.md` and hold the draft plan
   comment against it (this does not feed the rubric's verdict; report
   any broken rule separately in the summary).

## Evidence gathering

For each required check, the fact comes from a fixed pair of
locations — gather both before grading that check:

- `diagnosis-grounded`: the plan's stated cause (step 5's note) against
  every control/artifact fact from step 4's list. Check each one
  individually; one contradiction is enough to fail.
- `scope-bounded`: the plan's full proposed-changes list (step 5), not
  just its first item. Read every numbered item before deciding.
- `plan-executable`: the plan's approach/steps and any file, function,
  or line reference named in them (step 5).
- `test-plan-observable`: the plan's test plan (step 5) against the
  repro evidence's own steps and artifacts (step 4).
- `thread-aware`: the maintainer-signal note from step 3, against the
  plan's chosen approach and what the plan comment says about it.
- `ai-disclosure`: the AI-disclosure stance from step 1, against the
  plan comment's text (step 5) — look for an explicit disclosure
  sentence, not an inference.

For the two preferred checks:

- `risk-named`: any risk/unknown line in the plan (step 5).
- `comment-matches-plan`: the plan comment against the plan's own
  diagnosis, scope, and test plan (both from step 5) — a direct
  side-by-side, not a vibe check.

## Check execution

Grade in this fixed order: `diagnosis-grounded`, `scope-bounded`,
`plan-executable`, `test-plan-observable`, `thread-aware`,
`ai-disclosure`, then the two preferred checks. This order matters
because `diagnosis-grounded` is upstream of two others: if the cause is
wrong, `plan-executable` and `test-plan-observable` are still graded
against what the plan actually says (a well-executed plan for the
wrong cause still gets its own honest grades on those checks — do not
let one failure bleed into another's evidence), but note in each
check's evidence line when it is only checking the plan's internal
consistency, not whether the plan will work.

Grade every check using only the evidence gathered for it; do not
re-read the whole package per check. If the evidence a check names is
genuinely absent from the package (not merely thin), grade `unclear`
and say what's missing, not `fail` — `fail` is for evidence that is
present and answers the check in the negative. A check with no
evidence to gather at all (for example, `plan-executable` when the
plan states no approach whatsoever) grades `fail`, not `unclear`: the
absence itself is the decisive fact for that check, not a gap in your
search.

For `thread-aware` specifically: if step 3 found no maintainer
signal, this check passes by default — there is nothing to be aware
of. Only fail it when a real signal exists and the plan neither
follows it nor mentions it.

For `ai-disclosure` specifically: if step 1 found no disclosure
requirement (silent or none stated), this check passes regardless of
what the comment says about AI use.

## Verdict assembly

Apply the rubric's stated rule exactly: `accept` only if all six
required checks graded `pass`; any required check graded `fail` or
`unclear` produces `reject`. The two preferred checks are recorded in
the output but never read when deciding the verdict — compute the
verdict from the six required grades alone, then attach the preferred
grades to the same output afterward.

When the verdict is `reject`, the JSON's `evidence` field for each
failing required check must quote or name the specific contradicting
fact (the control run, the missing file name, the thread comment) —
not a restatement of the check's own name. The summary's headline
sentence names the single check that would need to change first for
the plan to become acceptable: prefer `diagnosis-grounded` over the
others if it failed, since a plan built on a wrong cause is not worth
fixing anything else about first.
