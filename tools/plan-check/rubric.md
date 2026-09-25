# Rubric: is this plan ready to post and build from?

Every check below judges the plan itself against the evidence it should
answer to, never its formatting. A terse plan that names the right file
and the right test passes; a long confident one that doesn't, fails.

## Checks

| Check | Evidence | Pass condition | Weight |
|---|---|---|---|
| `diagnosis-grounded` | The plan's stated cause, read against every control run and artifact fact in the repro-evidence block. | The diagnosis is consistent with everything the repro evidence shows. Fails when the repro evidence contains a control run or artifact fact that directly contradicts the claimed mechanism — a control that still behaves normally on the exact path the diagnosis blames, or an artifact that pins the defect to a different point than the diagnosis names — even when the diagnosis cites a thread comment or reads as fully confident. | required |
| `scope-bounded` | The plan's proposed changes / scope statement. | The plan is one change sized to the reported defect, with an explicit not-in-scope line, and adds nothing beyond what fixing the defect requires. Fails when the plan frames the minimal fix as insufficient and proposes rebuilding, migrating, unifying, or "fixing the whole class/pipeline/lifecycle," or bundles an unrelated refactor, new option, or feature "while in there." | required |
| `plan-executable` | The plan's approach/steps and any files, modules, or locations it names. | A stranger holding only the plan and the linked evidence could start immediately: each step names a concrete file or module, and either a concrete function/line within it or a concrete, already-validated method for pinning the exact spot (a debug tool or log output the plan states it already has working) — not merely an intention to go figure out where to look. Fails when a step names no file or module at all and substitutes investigate/profile/look into/explore language, when the plan expresses real uncertainty about which subsystem is even responsible (naming candidate layers with no way to tell them apart), or when it defers the file/module itself — not just the exact line — to the build. | required |
| `test-plan-observable` | The plan's test plan, read against the repro evidence's steps and artifacts. | The test plan re-runs (or names an equivalently specific, decisive check for) the repro evidence's own steps and states the exact observable signal that shows the defect is gone (an exit code, a specific output value, a specific rendered line) — not merely "the suite passes" or a subjective feel. Fails when the only named test is a generic full-suite run disconnected from the reported symptom, or the success condition is not something a stranger could check pass/fail on. | required |
| `thread-aware` | The plan and plan comment, read against the issue's thread highlights. | When the thread already shows a maintainer's stated direction, an in-progress fix, or an explicit signal about what they want, the plan either follows that direction or explicitly says why it takes a different one. Fails when the thread shows a maintainer already working toward, or favoring, a different fix than the one proposed, and the plan says nothing about it. | required |
| `ai-disclosure` | Repo facts: the contribution policy / AI-use policy line. Plus the plan comment text. | Passes when the policy states no AI-disclosure requirement, or is silent (silence passes). When the policy requires disclosing AI assistance, passes only if the plan comment explicitly discloses AI tool use. Fails when disclosure is required and the comment says nothing about it. | required |
| `risk-named` | The plan's stated risks, unknowns, or open questions. | The plan names at least one concrete thing it has not yet resolved (a cost not yet measured, a question left to review, a case it does not cover), rather than presenting the whole approach as fully certain. | preferred |
| `comment-matches-plan` | The plan comment, read against the plan's own diagnosis, scope, and test plan. | The comment accurately summarizes what the plan actually says — same cause, same scope, same test — and does not add a claim, a scope item, or a certainty the plan itself does not carry. | preferred |

## Verdict rule

Accept if and only if every `required` check grades `pass`. Any required
check graded `fail` or `unclear` produces reject: a plan whose cause is
unverified against its own evidence, or whose scope or executability
cannot be confirmed from the package, is not ready to build from.

`preferred` checks never change the verdict. Report their grades, and use
them to note extra strength in an accepted plan.
