# Rubric: is this pull request ready to submit?

Every check below judges the artifact itself — the diff, the evidence,
the description's claims — against the plan and the repo's own stated
asks. Never the write-up's shape. A terse PR that matches its plan and
shows real evidence passes; a long confident one that drifted, doesn't.

An honest deviation, recorded in the plan's deviation notes and carried
into the description, is not drift. These checks fail silent gaps, not
disclosed ones.

## Checks

| Check | Evidence | Pass condition | Weight |
|---|---|---|---|
| `diff-in-scope` | Every changed file and hunk in the diff, read against the plan's stated scope/files list and its deviation notes. | Every change in the diff is either named in the plan's scope or covered by an honest deviation note carried into the description. Judge each hunk against what the plan's own scope paragraph literally states will change — including when the plan itself names changing a shared function or a function used by more than one caller, which is in scope exactly as written, not scope creep. Fails when the diff contains a change beyond that with no note — an unrelated refactor, a new config option or feature, a dependency bump, a file the plan never named — or when it omits something the plan's scope said the fix required. Do not fail this check on speculation about code the bundle does not show (an unseen caller, a downstream consumer); judge the diff against the plan's literal scope statement, not against effects you cannot verify from the package in front of you. | required |
| `description-matches-diff` | The PR description's own claims about what changed (including claims of absence, like "no other changes"), read against the actual diff. | Every claim the description makes about the change is true of the diff. Fails when the description asserts something the diff doesn't do, denies a change the diff does contain, or describes the fix's mechanism differently from what the diff actually implements. | required |
| `test-evidence-complete` | The test-evidence section plus the diff's own content, read against every item the plan's test plan named (every repro step, every control run, the suite/checks run) — not just the first one shown. | Every item the plan's test plan named is satisfied by one of three things: (1) a shown, observable result in the test-evidence section (a concrete before/after, an exit code, a specific output value); (2) a named, scenario-specific automated test visible in the diff whose assertions directly match that item, confirmed as included in a shown passing test run; or (3), for an item that only claims *pre-existing, untouched behavior is unaffected* (a control, not a second fix scenario), the diff's own control flow provably shows that code path is untouched or structurally a no-op for that case — trace it yourself rather than demanding a separate transcript for a claim the diff already settles. A test merely claimed to exist with no visible name or matching assertion does not count, and (3) never covers a claim that the *fix itself* correctly handles a second, distinct scenario or failure mode (that still needs (1) or (2)). Fails when an item has none of the three, when the evidence proves nothing observable at all, or when the repo's own checks were never run. | required |
| `diff-reviewable` | The unified diff's content, hunk by hunk. | Every hunk is part of the actual fix a reviewer would need to read. Fails when the diff carries debris: commented-out code, debug print/log statements, an unused or dead function left in, a stray TODO/scratch note, or a whitespace-only/reformatting hunk riding along with no functional change. | required |
| `template-compliance` | The repo-facts block's stated PR-template asks or required checklist items, read against the description's actual content and the diff. | Every section or checklist item the repo's own template requires is present with real content — not boilerplate, not left as the template's own placeholder text — and any artifact the template names as required for this kind of change (a changelog entry, a doc update, an issue-closing reference) is actually in the diff when the template asks for it. Fails when a required section is missing, filled with boilerplate, or a required artifact the template asks for is absent from the diff. Repos with no stated template pass this automatically. | required |
| `ai-disclosure` | Repo facts: the contribution policy / AI-use policy line. Plus the PR description text. | Passes when the policy states no AI-disclosure requirement, or is silent on AI use (silence passes). When the policy requires disclosing AI assistance, passes only if the description explicitly discloses AI tool use and its extent. Fails when disclosure is required and the description says nothing about it. | required |
| `title-specific` | The PR title. | Names the actual fix, file, or symptom — not a generic placeholder ("fixed the bug!!", "update code") that could sit on any issue. | preferred |
| `commit-history-coherent` | The commit list. | Commits tell a coherent story of the fix (even if not squashed to one) rather than a raw debugging trail ("wip", "fix", "oops", "cleanup" with no description of what each did). | preferred |

## Verdict rule

Accept if and only if every `required` check grades `pass`. Any required
check graded `fail` or `unclear` produces reject: a PR whose fidelity to
its plan, or whose evidence, cannot be verified from the package is not
ready to submit.

`preferred` checks never change the verdict. Report their grades, and use
them to note extra strength in an accepted PR.
