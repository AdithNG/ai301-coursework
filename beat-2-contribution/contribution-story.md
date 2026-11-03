# Contribution: Cinema mode makes my screen go black

Path: `beat-2-contribution/contribution-story.md`

This file is Assignment 5, and it is graded at the path above; a copy kept anywhere else in
the repository is not read. **Keep every section header below under its exact name** — the
format criteria grade exactly that. Your course repo link is the whole submission: no tool
files and no evidence uploads are part of Assignment 5.

**Contribution Number:** [1]
**Student:** Adith Gunaseelan
**GitHub Username:** AdithNG
**Issue:** https://github.com/code-charity/youtube/issues/4293 (pivoted from #4356 on 2026-11-03; see Why I Chose This Issue)
**Pull Request:** [GitHub PR link, once opened]

One repository, built across units 5 to 8, telling the whole story of your real
contribution. Fill it as you work, not at the end — the sections below ask for evidence that
is far easier to write down as it happens than to reconstruct in the last weekend.

Contribution Number stays `[1]` unless you make more than one contribution this term; it
just numbers them.

---

## Toolkit

Every story section below, from Why I Chose This Issue through Pull
Request, ends with a Toolkit block: which tool did that phase's work
and how you used it, what it produced, what difficulties or
shortcomings it presented, and what you changed or would change
because of them. Together the blocks are the thread the rubric's
Toolkit points read, and they are the raw material for your unit-9
portfolio, so keep them current as you work rather than
reconstructing them later.

A Toolkit block looks like this (copy it into each story section and
fill it there):

```text
**Toolkit**

- Tool: [which tool did this phase's work, and how you used it]
- What it produced: [described in a sentence or a short excerpt]
- Difficulties: [what fought you or fell short, or "none met"]
- What changed: [the patch, revision, or workaround, what you would
  update, or "nothing"]
```

This section also keeps one short dated entry per week of tool work,
starting below: what you built or changed that week, what broke, and
what you changed because of it. The blocks tell the story where it
happened; these entries are the toolkit's own timeline.

All four entries are graded. Keep every heading below, and fill each
one in the week it belongs to.

**Unit 5: Installed the scout, wrote a search strategy, and patched a real rubric gap the wild exposed**

I installed the scout overlay on my Unit 1 `issue-select` skill and wrote `search-strategy.md` from my fit profile: three `gh search issues` queries, and a ranking pipeline that drops assigned/frontend-labeled/stale results and sorts the rest by comment count. The first scan's reply never closed with the required fenced JSON block, so I re-ran it with an explicit reminder before trusting the output. The second run graded cleanly, but it surfaced three real problems: one repo took 3 of 5 slots because the comment-count tie-break had no other ordering, a spelling-based label filter would have missed a "dashboard"-labeled frontend issue, and two of the five graded candidates (a screen-recording task, a screenshot-collection task) weren't code changes at all — my Unit 1 `scope-bounded` check passed both anyway, since it had only ever been written against sandbox issues, which are always code changes. I patched the ranking filters (a per-repo cap, a wider label list) and added a real fail condition to `scope-bounded` for non-code deliverables, logged in `scan-notes.md`.

**Unit 6: Installed the voice-guard, patched the scout's scope check for the wild, and claimed the issue**

I replaced `scope-bounded` with the patched `scope-feasible` check, keeping my own Unit 5 non-code-deliverable and support-request fail conditions since the staff patch text didn't carry them forward, and added the difficulty-note section so an open edge gets flagged instead of silently rejected or silently passed. Re-grading both of my accepted picks live under the new rubric kept both at accept, but #4356 came back **moderate** (the bug is narrowed to one of two candidate files, not pinned yet) while #4348 came back **ambitious** (its one-line body never defines what "block playlists" means) — a distinction the old rubric had no way to say out loud. Installing the voice-guard hit the same Windows `claude`-CLI-not-on-PATH issue every unit's eval harness has hit; fixed it the same way, with `shutil.which`. My claim comment passed both the guard and `repro-check`'s claim-only-draft rubric and is now posted: https://github.com/code-charity/youtube/issues/4356#issuecomment-6482628565. Live reproduction, though, did not go as planned: testing on fresh installs in both Chrome and Firefox (the reporter's browser), the "fit to window" value saved correctly both times — the opposite of what's reported. `repro-check`'s full-package grading still came back accept, since its `evidence-supports-claim` check explicitly allows an honest, evidenced non-reproduction as a passing outcome rather than forcing a false confirmation; that's the rubric doing exactly the job it's meant to. Given I'm a week behind and my professor okayed picking something faster to reproduce, I released #4356 honestly on its thread and pivoted to `#4293` instead, in the same repo. Re-picking by hand (not another scan) also surfaced a real rubric gap: `nobody-on-it` only disqualifies an *open* linked PR, so it would have silently waved through `#4188`, which already has a merged fix the maintainer never linked or closed the issue for — caught only because I checked PR state by hand instead of trusting the timeline's absence of an "open" flag. Noted in `scan-notes.md` as a patch to make later, not made yet.

**Unit 7: [one line on the tool work]**

[2-4 sentences: the ship-gate and your gate manifest. What it produced
on your real branch, what fought you, what changed because of it.]

**Unit 8: [one line on the tool work]**

[2-4 sentences: the PR-monitor and your playbook. What it read, what it
drafted, what you did with the draft.]

---

## Why I Chose This Issue

My own scan (three `gh search issues` queries against my Python-first fit profile, ranked and graded with my scout) came back with a top five that all technically passed `scope-bounded` but were weak fits once I actually read them: a repo-wide rename, an issue with a completely empty body, and two tasks that weren't code changes at all (a screen recording, a folder of screenshots). None of that was a rubric bug — it told me my own search strategy wasn't pointed at anything I actually wanted to work in. So I went hand-picking from the Issue Selection site instead: four candidates from `AcademySoftwareFoundation` repos, all four rejected (three already had open PRs attached, one had turned into an open-ended, pick-any-file campaign), then four from `code-charity/youtube`, a browser extension — outside my Python-first profile, but inside the explicit JS carve-out my fit profile states ("I can also read plain JavaScript... enough to fix a small, clearly-described bug"). My scout's verdict on `#4356` was **accept**: a maintainer-labeled bug where a contributor had already traced the failure to one behavior ("Fit to window" never getting saved) and named two candidate files, with a comment that invited help rather than claiming the fix exclusively. Under Unit 6's patched rubric, re-grading it live gave it a **moderate** difficulty note — the location is narrowed but not pinned to one file, so finding it is still my work to do. I hope to learn how to navigate an unfamiliar codebase well enough to find the right file myself and write a test that would have caught this.

An honest read of the scout: its biggest real miss this round wasn't a rubric check at all, it was upstream of grading — the Issue Selection site's own filters don't check claim status, so six of my eight hand-picked candidates were already spoken for before I ever graded them. My rubric's `nobody-on-it` check caught every one of those correctly once I actually graded them, so nothing slipped through, but I'd rather not burn grading time on issues the site's own filters could have screened out; that's a note for my search strategy, not my rubric. On the check that did change this unit, `scope-bounded` → `scope-feasible`: I'm glad it exists now, because it gave me language for exactly what #4356 is (a real bug, narrowed but not pinned) instead of either quietly failing it for the open edge or quietly passing it without saying so.

**Pivot (2026-11-03): #4356 → #4293.** I claimed #4356 and investigated it for real: a scoped orientation of the two files its thread named, then a live reproduction attempt on fresh installs of both Chrome and Firefox (the original reporter's browser). The setting saved correctly in both — the opposite of what's reported — and `repro-check` accepted that as an honest, evidenced non-reproduction rather than forcing a false confirmation. My best remaining hypothesis (a storage-cache desync that only shows up on an existing, non-fresh profile) was real but would have taken another full test cycle to confirm or rule out, and I'm a week behind, so with my professor's sign-off to pick something faster, I released the claim honestly on the thread and moved to `#4293` ("Cinema mode makes my screen go black"): unclaimed, no open or merged PR attached, and already diagnosed down to exact file:line CSS suspects by another contributor in the thread (`RaghavShubham`), which I verified still match current master before claiming it. This is a cleaner first issue than #4356 turned out to be — concretely bounded, a fast visual reproduction instead of a storage race, and most of the "where does this live" work already handed to me honestly in the open. **Claim comment:** https://github.com/code-charity/youtube/issues/4293#issuecomment-6601716005. (Released #4356 honestly first: https://github.com/code-charity/youtube/issues/4356#issuecomment-6601682024.)

**Toolkit**

- Tool: issue-scout (my scan's search strategy and ranking filters, and my rubric's direct-mode grading on hand-picked site candidates), then plain `gh` searching/filtering by hand for the pivot
- What it produced: a scan whose top five all accepted but fit poorly; direct grading of 8 Issue-Selection-site picks that narrowed to #4356 first; after #4356's repro stalled, a hand search of `code-charity/youtube`'s own open good-first-issue bugs turned up #4293 as unclaimed and well-diagnosed
- Difficulties: the scan's own ranking tied almost the entire result set at 0 comments with no other ordering; the Issue Selection site's filters don't check claim status; and while re-picking, I found `nobody-on-it`'s own gap — it only disqualifies an *open* linked PR, so it would have silently passed an issue with an already-merged-but-unlinked fix (`#4188`) had I not checked PR state by hand
- What changed: added a per-repo cap and a wider frontend-label list to `search-strategy.md`; added, then renamed, the non-code-deliverable fail condition (`scope-bounded` → `scope-feasible`); noted the merged-PR gap in `scan-notes.md` for a future patch

---

## Understanding the Issue

**Problem Description**

[In your own words, what is broken or missing?]

**Expected Behavior**

[What should happen?]

**Current Behavior**

[What actually happens?]

**Affected Components**

[Which parts of the codebase are involved?]

**Toolkit**

- Tool:
- What it produced:
- Difficulties:
- What changed:

---

## Reproduction Process

*Note: this section now covers #4293. #4356's reproduction attempt (an
honest, evidenced non-repro on a fresh install in both Chrome and
Firefox, accepted by `repro-check`) is preserved in the Why I Chose
This Issue section above, as the reason for the pivot — not deleted,
just no longer the active issue's repro record.*

**Environment Setup**

[To fill once tested: same fork, same two browsers as before.]

**Steps to Reproduce**

1. [Set theme to "plain" in Appearance > Themes.]
2. [Open a video with ambient lighting / cinema mode on, observe.]
3. [Turn off Appearance > Player > Ambient Lighting, observe whether the blackout clears, per the thread's suggested isolation test.]

**Reproduction Evidence**

- **Commit showing reproduction:** [link to commit in your fork]
- **Screenshots/logs:** [if applicable]
- **My findings:** [pending the live test]

**Toolkit**

- Tool:
- What it produced:
- Difficulties:
- What changed:

---

## Solution Approach

**Analysis**

[Your analysis of the root cause: what is causing the issue, traced
to the files and functions involved.]

**Proposed Solution**

[High-level description of your fix approach, and what you are
deliberately NOT changing.]

**Implementation Plan**

1. [Modify file X to do Y]
2. [Add or change Z]
3. [Update tests]

[Record deviations here as they happen: what changed mid-build and
why. A recorded deviation is honest work; silent drift is not.]

**Toolkit**

- Tool:
- What it produced:
- Difficulties:
- What changed:

---

## Testing Strategy

[What observable checks prove the fix: your reproduction steps re-run
against the built change (before and after), and the repo's own test
suite or checks with the outcome shown. "Tests pass" with nothing
observable named is not a strategy.]

**Toolkit**

- Tool:
- What it produced:
- Difficulties:
- What changed:

---

## Implementation Notes

[A running log, added to as you work. Date each entry. What you
built, decisions you made when you made them, wrong turns included.]

**[Date]**

[Entry]

**[Date]**

[Entry]

**Toolkit**

- Tool:
- What it produced:
- Difficulties:
- What changed:

---

## Pull Request

**PR Link:** [GitHub PR URL when opened]

**PR Description:** [what the PR says; much of the content above
adapts into it]

**Maintainer Feedback:**

- [Date]: [feedback received, or "none yet"; silence is a normal
  state and costs you nothing]
- [Date]: [how you addressed it]

**Status:** [Awaiting review / Iterating / Merged / Closed. The
status is a fact you record, never a grade: the PR is graded as
opened.]

**Toolkit**

- Tool:
- What it produced:
- Difficulties:
- What changed:

---

## Learnings and Reflections

**Technical Skills Gained**

[What you learned technically, named concretely.]

**Challenges Overcome**

[What was hard and how you solved it; point at the real events, the
Toolkit blocks above are full of them.]

**What I'd Do Differently Next Time**

[Reflection on your process.]

---

## Resources Used

[What actually helped, not a reading list. List things this story
already shows you using: the doc you followed to set the environment
up, the thread that explained the bug, the person who answered you.
One real entry beats ten plausible ones.]

- [Link to documentation that helped]
- [Discussion, issue, or post that helped]
