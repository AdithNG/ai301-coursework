# Unit 1 — Issue Selection

Path: `beat-1-sandbox/unit-1/selection.md`

Record of the issue carried into Unit 2, and of the evaluation runs that produced
`eval-run.txt`.

---

## Selected issue

**Issue link**

https://github.com/codepath/pathreview-ai301-fa26-s1/issues/53

**Verdict output**

````
All evidence gathered. Here's the grading.

**Summary (issue #53 — "PII scrubber fails to redact parenthesized US phone numbers"):**

- `repo-alive` — **pass**: `archived: no`, last push 2026-09-16 (7 days before today's 2026-09-23, well within 180).
- `maintainer-reachable` — **pass**: issue opener `Aburke225` is a `COLLABORATOR`; also last 5 default-branch commits are by this non-bot human, newest 2026-09-16 (within 90 days).
- `scope-bounded` — **pass**: concrete bug in a named file (`pii_scrubber.py`) with reproduction steps and four named failing tests; not a tracking/umbrella issue, no unsettled design.
- `nobody-on-it` — **pass**: `assignees: none`, no linked/cross-referenced PRs, empty comment thread — no claim.
- `ai-work-allowed` — **pass**: no AI ban; `docs/CONTRIBUTING.md` describes workflow (xfail removal, CI) but bans nothing.
- `newcomer-labelled` (preferred) — **pass**: carries `good first issue` (plus `bug`, `safety`, `tier-1`).
- `still-shipping` (preferred) — **fail**: no releases published.

All required checks pass → **accept**. This is a strong fit for the profile: a reproducible Python bug with a named file and named failing tests in a repo with a runnable pytest suite — no frontend work, no unfamiliar toolchain to install.

```json
{
  "item": "https://github.com/codepath/pathreview-ai301-fa26-s1/issues/53",
  "checks": [
    {"name": "repo-alive", "grade": "pass", "evidence": "archived: no; last push 2026-09-16, 7 days before capture date 2026-09-23 (within 180)"},
    {"name": "maintainer-reachable", "grade": "pass", "evidence": "issue opener Aburke225 has author_association COLLABORATOR; also authored last 5 commits, newest 2026-09-16 (within 90 days), non-bot"},
    {"name": "scope-bounded", "grade": "pass", "evidence": "bounded bug in pii_scrubber.py with repro steps and named failing tests; not tracking/umbrella; empty thread, no unsettled design"},
    {"name": "nobody-on-it", "grade": "pass", "evidence": "assignees: none; no linked/cross-referenced PRs in timeline; comment thread empty — no claim"},
    {"name": "ai-work-allowed", "grade": "pass", "evidence": "docs/CONTRIBUTING.md states workflow conditions but no ban on AI-assisted contributions"},
    {"name": "newcomer-labelled", "grade": "pass", "evidence": "labels include 'good first issue' (also bug, safety, tier-1)"},
    {"name": "still-shipping", "grade": "fail", "evidence": "gh release list returned no published releases"}
  ],
  "verdict": "accept"
}
```
````

---

## Eval iterations

**Run history**

1. `--limit 3` smoke run — **2/3**. Disagreed on `issue-01`, which my rubric rejected
   with `failed: maintainer-responds`.
2. `--only issue-01` re-run after rewriting that check — **1/1**.
3. Full 20-issue run with `--save-run eval-run.txt` — **18/20**, which is the
   `agreement: 18/20 scored items  (bar: 18/20: PASS)` line in the committed
   `eval-run.txt`. Category tallies on that run:
   `claimed 4/4  clear-accept 8/8  dead-repo 3/3  policy 1/1  scope 2/4`.

(Before run 1 there was an aborted invocation that produced no scores: the harness
crashed writing the prompt to the `claude` CLI's stdin, because on Windows Python
encodes subprocess stdin as cp1252 and several bundles contain emoji. Re-running with
`PYTHONUTF8=1` fixed it. No issues were graded, so there is no agreement score for it.)

**Issue analysis**

`issue-15` (zulip/zulip#19589, "Separate `command` and `text` field for
slack-compatible outgoing webhook"). **My rubric: accept. Gold label: reject.**

My rubric read it as accept because every required check found a clean pass.
`nobody-on-it` was the check that should have caught it and did not: the repo facts
read `assignees: none`, and both linked PRs (`#20840`, `#23123`) are `closed`, not
open, so my "no linked PR is in the `open` state" condition was satisfied. My claim
clause only looks back 180 days from the 2026-08-05 capture date, and the newest
`@zulipbot claim` in the thread is from years earlier, so it did not fire either.
`scope-bounded` also passed: the body describes one concrete change to one webhook
payload, no maintainer in the thread calls it a core rewrite, and a maintainer
(`eeshangarg`, MEMBER) actively helped pin the format down.

What my rubric could not see is the *pattern* across those 97 comments. The issue has
been open since 2021-08-18, and the thread is a graveyard: `LoganNiswander`,
`leighadennis`, `blackbird7112`, `BrianMcDowell` and `sudhanshu154` each claimed it and
each got auto-unassigned by `zulipbot` after 14 days of no progress, and the two PRs
that did get written were both closed unmerged. The evidence guide names this exact
signal — "an issue open for years with several abandoned attempts (closed, unmerged PRs
in its history) is telling you something about its real difficulty" — and my checks
read each of those facts individually as *good* news (no assignee now, no open PR now)
when their accumulation is the strongest available evidence that the issue is harder
than it looks. A five-year record of newcomers bouncing off is a scope signal, and my
rubric has no check that reads history as a rate rather than as a current state.

**Check rationale**

The check as it is currently written in the uploaded `tools/issue-select/rubric.md`:

> | `maintainer-reachable` | Repo facts: the `maintainer first-response sample` list, the `last 5 default-branch commits` list, and the `author_association` shown for the issue opener and for every comment in the thread. | Passes if any one of these holds: a sampled issue has a first owner/member/collaborator response of 45.0 days or fewer; OR this issue was opened by, or has a comment from, a user marked OWNER, MEMBER, or COLLABORATOR; OR one of the last 5 default-branch commits is dated within 90 days of the capture date and its author is not a bot (the account name does not end in `[bot]`). Fails when a repo shows none of the three: code may still be moving, but no human is answering. | required |

It started much stricter — one source, "a sampled issue has a first
owner/member/collaborator response of 30.0 days or fewer" — and the smoke run showed
why that was wrong. `issue-01` (conda/conda) has exactly one non-empty entry in its
response sample, `#16275 ... 32.9 days`. Gold says accept; my rubric rejected it, and
it rejected it by 2.9 days against a threshold I had picked out of the air rather than
from any evidence in the set.

Two things were wrong, and raising the number would only have fixed one. The threshold
was arbitrary, so I moved it to 45.0 days, which is wide enough that the "days to first
response" numbers in the bundles stop being decided at the margin. More importantly the
check was reading one weak source when the bundle carries three independent ones, so I
made it a disjunction. A maintainer who commits human code to the default branch this
month is reachable whether or not a sampled issue happened to get a fast reply, and the
sample is only five issues, so its silence is thin evidence. The check now fails only
when all three sources agree nobody is home — which is the question the check is
actually asking.

**Trade-offs**

It changed `issue-01` from reject to accept, which is the change I wanted, and it gives
up the ability to fail a repo where humans commit steadily but never answer issues.
That is a real first-issue failure mode — your PR gets ignored even though the code is
moving — and my rubric can no longer see it on its own. I accepted the loss because of
what the disjunction costs versus what it buys on this set: the eval categories are
`claimed`, `clear-accept`, `dead-repo`, `policy` and `scope`, and none of them is an
unresponsive-maintainer category, so a strict version of this check can only produce
false rejects against the eight `clear-accept` items while catching nothing the set was
built to test. `issue-01` was that false reject happening in a `--limit 3` smoke run
that touched only three of the twenty items, which suggests the strict version would
have cost more than one.

The loss is also smaller than it looks, because `repo-alive` still fails any repo whose
last push is more than 180 days old, and that is what actually caught the `dead-repo`
items (3/3 on the final run). A genuinely abandoned project fails there before
`maintainer-reachable` is consulted. What escapes is the narrower case of a repo that is
busy in commits and silent in its issue tracker, and I would rather miss that live —
where I can read the tracker myself before claiming — than reject working issues in
eval.

---

## Selection rationale

**Selection rationale**

**1. Fit to my interests and to the time available.** Issue #53 is a Python regex bug in
one named file, `pii_scrubber.py`: the scrubber redacts `555-123-4567` but misses
`(555) 123-4567`. That is the shape of work I said I wanted in my fit profile — read
someone else's Python, find the right line, match the surrounding style — and it is
small enough that the interesting part is writing the test rather than understanding the
architecture. It needs nothing installed beyond the repo's own pytest suite, which
matters with a Unit 2 deadline behind it; #72, which my skill also accepted, would have
meant getting `passlib[bcrypt]` and `jose` working before I could reproduce anything.

**2. What the verdict identified correctly, and what I weighed that the rubric could
not.** The verdict got the mechanical facts right and I trust them: no assignee, no
linked PR, an empty comment thread, an active COLLABORATOR maintainer who pushed a week
ago, and a contribution policy that sets conditions rather than banning AI-assisted
work. What the rubric cannot weigh is that the empty thread is worth more to me than the
checks credit it for. My rubric treats "zero comments" as merely the absence of a claim,
a neutral fact; for choosing among three issues it already accepted, it was the deciding
one, because #68 and #72 each have classmates who have posted plans and I would rather
not duplicate someone's half-finished work even where the house rule says I may. The
rubric also cannot see that a PII bug has an obvious correctness boundary — a phone
number is either redacted or it is not — so I will know when I am done, which is not
true of the test-coverage issues in the same tier.

**3. Anticipated difficulty in claiming it.** Low. Nobody has claimed it, it carries
`good first issue` and `tier-1`, and the Path Review house rule means even a classmate's
claim would not block me. The real risk is not the claim but the fix: the four failing
tests named in the issue suggest the maintainer has a specific set of phone formats in
mind, and a regex that passes those four could still be wrong about formats they did not
list. I expect to spend more time deciding which formats belong in scope than writing
the pattern, and that is the question I will ask in the claim comment in Unit 2.

---

Related paths: `eval-run.txt` in this directory; your skill's files in
`tools/issue-select/`.
