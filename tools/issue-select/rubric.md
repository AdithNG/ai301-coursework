# Rubric: is this a good first issue?

Every recency threshold below is measured against the bundle's `captured:`
date in eval mode, and against today's date in live mode.

## Checks

| Check | Evidence | Pass condition | Weight |
|---|---|---|---|
| `repo-alive` | Repo facts: the `archived:` flag on the repo line, and the `last push to any branch` date. | `archived: no` AND the last push is 180 days or fewer before the capture date. | required |
| `maintainer-reachable` | Repo facts: the `maintainer first-response sample` list, the `last 5 default-branch commits` list, and the `author_association` shown for the issue opener and for every comment in the thread. | Passes if any one of these holds: a sampled issue has a first owner/member/collaborator response of 45.0 days or fewer; OR this issue was opened by, or has a comment from, a user marked OWNER, MEMBER, or COLLABORATOR; OR one of the last 5 default-branch commits is dated within 90 days of the capture date and its author is not a bot (the account name does not end in `[bot]`). Fails when a repo shows none of the three: code may still be moving, but no human is answering. | required |
| `scope-bounded` | The issue title and body, and the full comment thread. | Fails if any one of these holds: the body is mainly a list of other issue numbers, or the issue calls itself a tracking, umbrella, or mega issue; a maintainer in the thread says the change touches core internals, needs a redesign, or needs a product decision nobody has made; the newest maintainer comment leaves the design unsettled; or the issue asks for help using the software rather than for a change to it. Otherwise passes. A terse body, a missing reproduction, or an empty thread is not a fail on its own. | required |
| `nobody-on-it` | Repo facts: the `this issue:` line (`assignees:` and `linked PRs:` with each PR's state). Plus any pull request or claim named in the comment thread. | `assignees: none`, AND no linked PR is in the `open` state, AND no PR named in the thread is open or awaiting review, AND no comment dated within 180 days of the capture date claims the work ("/assign", "I'll take this", "working on this", "I'm working on a PR") without a later comment showing that attempt was dropped. | required |
| `ai-work-allowed` | Repo facts: the `contribution policy` line. | Fails only on an outright ban on AI-assisted or AI-generated contributions. Passes when the line states no policy, and passes when it states conditions rather than a ban (disclosure, human review, personally understanding and testing the change, no wholly-unreviewed AI output). | required |
| `newcomer-labelled` | The `labels:` field on the issue's header line. | One of `good first issue`, `good-first-issue`, `help wanted`, `documentation`, or `easy` is present. | preferred |
| `still-shipping` | Repo facts: the `latest release` line. | A release is published and dated within 365 days of the capture date. | preferred |

## Verdict rule

Accept if and only if every `required` check grades `pass`. Any required
check graded `fail` or `unclear` produces reject: a first issue whose
evidence is missing is not a first issue worth taking.

`preferred` checks never change the verdict. Report their grades, and use
them to rank the issues that were accepted: an accepted issue that passes
both preferred checks outranks one that passes neither.
