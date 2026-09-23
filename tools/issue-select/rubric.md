# Rubric: is this a good first issue?

Proposed draft for student review and calibration; not yet evaluated.
Measure recency against the capture date in eval mode, and today in live mode.
Use only the supplied snapshot in eval mode. Live sources are mapped in references/evidence-guide.md.

## Checks

| Check | Evidence | Pass condition | Weight |
|---|---|---|---|
| Maintainer activity | Repo-facts last five default-branch commits and dated maintainer responses; live commit history and comments by OWNER, MEMBER, or COLLABORATOR. | At least one human-authored default-branch commit, merge of a human-authored PR, or maintainer response within 90 days. Bot-only updates do not qualify. Response latency alone cannot establish recency unless its issue opening date is also given. | required |
| Repository in use | Repo-facts archived flag, latest release, and last five default-branch commits; live repository metadata, Releases, and commit history. | Not archived AND either a release within 365 days or a human-authored default-branch commit/merge within 90 days. No release is acceptable if qualifying commits exist. Stars alone do not pass. | required |
| Bounded contribution | Issue body, labels, full comment thread, creation date, and closed unmerged PR history. | One concrete bug fix, documentation change, test change, or feature with an observable outcome. Fail for a pure usage question, umbrella/tracking issue, unresolved competing designs without a maintainer decision, or explicit evidence that core parser/runtime/architecture changes are needed. Also fail an issue older than 730 days with at least two abandoned implementation PRs. A terse body, absent reproduction, or checklist for one outcome does not alone fail. A good-first-issue label overrides none of these failures. | required |
| Available work | Repo-facts assignees and linked PR states; full thread including informal PR links, claim comments, and relinquishments. Live: Assignees, Development, and thread. | No current assignee, no open implementation PR, and no unretracted claim within 30 days. Later explicit relinquishment clears a claim; closed unmerged PRs alone are not active claims. A merged implementation PR fails unless a maintainer identifies remaining work. Apply scope.md's student-claim exception only in live Path Review mode. | required |
| Compatible contribution policy | Repo-facts contribution-policy text; live CONTRIBUTING.md, linked contributor rules, dedicated AI policy files, and PR templates. | No explicit prohibition of the proposed AI-assisted workflow. Disclosure, personal understanding, human review, and testing requirements pass and must be recorded as obligations. Silence passes after checking the policy locations; inaccessible evidence is unclear. Distinguish a ban on fully generated contributions from permission for assistive AI use. | required |
| Clear starting point | Issue body and maintainer comments naming files, functions, reproduction steps, or verification commands. | At least one specific file/function to investigate OR a concrete reproduction/verification procedure is supplied. | preferred |

## Verdict rule

Accept if and only if every required check passes. A required fail or unclear rejects. Missing evidence is unclear unless the check explicitly says otherwise. Execute and report all checks even after a failure. Preferred checks never change a verdict; use them and the live fit profile only to rank accepted candidates. Quote the determining evidence for every grade and identify required checks causing rejection.
