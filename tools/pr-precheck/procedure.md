# Procedure: how this tool grades a PR package

## Read order

1. Read the repo-facts block first. Note down: the stated PR-template
   sections/checklist (or "no template"), any required artifact for a
   bug fix (changelog, doc entry, issue-closing reference), and the
   AI-disclosure stance (none stated / silent / required).
2. Read the issue and its thread highlights. Note the reported symptom
   in one sentence, and any explicit maintainer direction (a stated
   preference, a cause already pinned down, an in-progress patch).
3. Read the plan-context block's `Plan` paragraph before looking at the
   diff at all. Note, as a list: every file/area the plan names in
   scope, its "Not in scope" line verbatim, and every item the plan's
   `Test plan` names (every repro step, every control, every suite/
   check it says to run). This list is what `diff-in-scope` and
   `test-evidence-complete` check against, so build it before the diff
   can anchor you on a different story.
4. Read the candidate PR's `Title`, then `Description`, then `Commits`,
   then the `Diff` itself hunk by hunk, then `Test evidence` last. Note
   while reading the diff: every file touched, and whether each hunk
   is (a) in the plan's scope list, (b) covered by a deviation note, or
   (c) neither. Note while reading the description: every factual claim
   it makes about what changed. Note while reading test evidence: which
   of step 3's test-plan items now have a shown result, and which
   don't.
5. Live-mode only: read `scope.md`, confirm the PR targets the scoped
   repo, and note any house rule. Then read `voice-guide.md` and hold
   the draft title and description against it (reported separately in
   the summary; it does not feed the checks below unless a check
   names it).

## Evidence gathering

- `diff-in-scope`: the per-hunk list from step 4 against the plan's
  scope/not-in-scope lines from step 3, read literally. If the plan's
  scope paragraph itself names changing a given function or file
  (even one shared by other callers elsewhere in the codebase), a hunk
  that makes exactly that change is in scope — do not reason past what
  the bundle shows about unseen callers or downstream effects; judge
  the diff against the plan's literal words, not against speculation.
  A hunk is "covered" by a deviation only if that note is visible in
  the description (step 4); a deviation that exists only in `plan.md`
  and never reached the description does not satisfy this check — the
  reviewer reading the PR cannot see `plan.md`.
- `description-matches-diff`: the description's claim list from step 4
  against the diff itself, claim by claim. A claim is true only if the
  diff actually does what it says; absence claims ("no other changes")
  are checked against the full per-hunk list from step 4, not just the
  headline hunk.
- `test-evidence-complete`: the test-plan item list from step 3 against
  both the `Test evidence` section and the diff's own test files, item
  by item. For each item, mark it found if either a concrete shown
  result or a named test (visible in the diff, with assertions that
  match that item) covers it; mark it not-found only when neither does.
  One not-found item is enough to fail, regardless of how thorough the
  found ones are.
- `diff-reviewable`: scan every hunk in the diff for the debris tells
  named in the evidence guide (commented-out code, debug prints, dead
  functions, TODO/scratch notes, whitespace-only hunks) and the commit
  list for a pure-debugging-trail pattern. Note the specific hunk or
  commit that trips it.
- `template-compliance`: the repo-facts block's template/checklist
  list from step 1 against the description and diff from step 4,
  section by section. A repo with no stated template passes this
  check immediately — record that as the evidence, do not grade it
  `unclear`.
- `ai-disclosure`: the AI-disclosure stance from step 1 against the
  description's text from step 4. If the stance is "none stated" or
  "silent," pass immediately regardless of what the description says.
- `title-specific` / `commit-history-coherent` (preferred): the title
  and commit list from step 4, read on their own terms.

## Check execution

Grade in this fixed order: `diff-in-scope`, `description-matches-diff`,
`test-evidence-complete`, `diff-reviewable`, `template-compliance`,
`ai-disclosure`, then the two preferred checks. Grade each using only
the evidence gathered for it in the section above; do not re-read the
whole package per check.

`diff-in-scope` and `description-matches-diff` are graded independently
even though they read the same two artifacts: a diff can stay in scope
while the description still misdescribes it (wrong mechanism stated),
and a description can be accurate about a diff that is itself out of
scope. Grade both on their own terms; do not let one's pass excuse the
other.

If the evidence a check names is genuinely absent from the package
(not merely thin), grade `unclear` and say what's missing. A check
whose evidence is present and answers the check in the negative grades
`fail`, not `unclear` — `unclear` is for a genuine gap in the package,
not a weak case.

## Verdict assembly

Apply the rubric's stated rule exactly: `accept` only if all six
required checks (`diff-in-scope`, `description-matches-diff`,
`test-evidence-complete`, `diff-reviewable`, `template-compliance`,
`ai-disclosure`) graded `pass`; any one graded `fail` or `unclear`
produces `reject`. The two preferred checks are recorded in the output
but never enter the verdict computation.

When the verdict is `reject`, quote in the JSON's `evidence` field for
each failing required check the specific fact that decided it — the
named hunk, the missing test-plan item, the missing template section —
never a restatement of the check's name. When more than one required
check failed, the summary's headline sentence names the first failing
check in the rubric's stated order above (`diff-in-scope` first, since
a PR built on a drifted diff is not worth evaluating further on its own
terms before that's fixed).
