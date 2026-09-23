# Rubric: is this reproduction package ready to post?

Every check below judges the artifact or the words, never the write-up's
shape. A terse report that shows the right thing passes; a long confident
one that doesn't, fails.

## Checks

| Check | Evidence | Pass condition | Weight |
|---|---|---|---|
| `environment-recorded` | The repro report's environment record (tool version, OS/platform, and any other config, driver, or build dimension the issue itself calls behavior-relevant — e.g. "debug build panics, release build wraps", or "Windows + vmware driver"). | Every dimension the issue's own report, its bug-report template, or a maintainer/thread comment states is REQUIRED to confirm reproduction (a specific OS, a specific driver, a build profile such as debug-vs-release, or an explicit "confirm on the latest version / main branch" template ask) is stated with a concrete value. When the report's value differs from what that requirement names, the report says so and reasons about it (e.g. "the issue was filed against 13.0.0; behavior is unchanged on 15.2.0"). Fails when such a required dimension is missing entirely, or a differing value is used with no acknowledgment. A dependency's version mentioned only as background for a different, already-fixed issue's root cause does not need separate acknowledgment when the report's own artifact still demonstrates the reported symptom regardless — the artifact carries that weight instead. | required |
| `steps-followable` | The reproduction steps in the repro report. | A stranger holding only the stated environment and public information (the issue's own text, a public repo, a public playground link) could execute the same steps and reach the same trigger point. Fails when any essential input, file, or config is private or unshareable ("our internal config, not shareable") with no public substitute, or when a step is vague where the issue gives something concrete (a literal command, a literal file). | required |
| `evidence-supports-claim` | The repro report's output excerpts, logs, or screenshots, read against the specific symptom the issue names (its exact error signature, panic, exit code, or visible defect) — OR, on an honest non-reproduction, the attempt itself. | Passes on either of two outcomes: (1) an artifact from actually running the trigger is shown and it exhibits the same symptom the issue names, not an adjacent one (a different error message, a different exit code, or the described crash simply not happening, all fail this even when the report's tone claims a match); or (2) the report states plainly that it could not reproduce the behavior, backed by a real attempt (environment and steps shown) and, where the report can tell, what may have differed. Fails when no artifact of the actual triggering action exists at all — only setup/version confirmation, or a narrative diagnosis with nothing run — or when a shown artifact contradicts the issue's stated symptom while the report claims otherwise. | required |
| `claim-professional` | The claim comment's own wording. | The claim comment does not demand assignment or ask that the issue be "reserved," and does not promise a fix or a delivery date ("guaranteed," "within N days"). It states only what the contributor has done (or will do) to investigate. | required |
| `ai-disclosure` | Repo facts: the contribution policy / AI-use policy line. Plus the claim comment and repro report text. | Passes when the policy states no AI-disclosure requirement, or is silent on AI use (silence passes). When the policy requires disclosing AI assistance, passes only if the claim comment or repro report explicitly discloses AI tool use. Fails when disclosure is required and neither comment mentions it. | required |
| `control-comparison` | The repro report's steps. | The report includes a control or comparison run that isolates the variable the issue names (a run with the suspected flag/input removed, or the same steps under a different condition), not just the one failing run. | preferred |
| `next-step-named` | The claim comment. | The claim comment names one concrete next investigative step (a file, a function, or a specific question), not just "I'll look into it." | preferred |

## Verdict rule

Accept if and only if every `required` check grades `pass`. Any required
check graded `fail` or `unclear` produces reject: proof that cannot be
verified, or a comment that oversteps, is not ready to post.

In live claim-only-draft mode, a check whose evidence is the repro report
(`environment-recorded`, `steps-followable`, `evidence-supports-claim`) is
graded `unclear` with evidence `not yet applicable: claim-only draft` and
is excluded from the verdict rule, per the skill's claim-only-draft
instructions; the verdict then answers only whether the claim comment
(`claim-professional`, and `ai-disclosure` where the disclosure could
appear in the claim) is ready to post.

`preferred` checks never change the verdict. Report their grades, and use
them to note extra strength in an accepted package.
