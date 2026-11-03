# Contribution: Allocation plot should be shown always in the project detail page

Path: `beat-2-contribution/contribution-story.md`

This file is Assignment 5, and it is graded at the path above; a copy kept anywhere else in
the repository is not read. **Keep every section header below under its exact name** — the
format criteria grade exactly that. Your course repo link is the whole submission: no tool
files and no evidence uploads are part of Assignment 5.

**Contribution Number:** [1]
**Student:** Adith Gunaseelan
**GitHub Username:** AdithNG
**Issue:** https://github.com/ImperialCollegeLondon/proCAT/issues/860 (final pick, after #4356, #4293, and intent-drift-skill#80 all fell through on 2026-11-03; see Why I Chose This Issue)
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

I installed the scout overlay on my Unit 1 `issue-select` skill and wrote `search-strategy.md` from my fit profile: three `gh search issues` queries, and a ranking pipeline that drops assigned/frontend-labeled/stale results and sorts the rest by comment count. The first scan's reply never closed with the required fenced JSON block, so I re-ran it with an explicit reminder before trusting the output. The second run graded cleanly, but it surfaced three real problems: one repo took 3 of 5 slots because the comment-count tie-break had no other ordering, a spelling-based label filter would have missed a "dashboard"-labeled frontend issue, and two of the five graded candidates (a screen-recording task, a screenshot-collection task) weren't code changes at all — my Unit 1 `scope-bounded` check passed both anyway, since it had only ever been written against sandbox issues, which are always code changes. I patched the ranking filters (a per-repo cap, a wider label list) and added a real fail condition to `scope-bounded` for non-code deliverables, logged in `scan-notes.md`. Revisited this on 2026-11-03 after my original pick fell through twice (couldn't reproduce one issue's bug on a fresh install, then found a second was already fixed under a different issue number): re-ran the same scan and it surfaced three more gaps no amount of passing checks had caught before — a data-entry task that only failed correctly because I read the repo's own CONTRIBUTING.md, a feature issue blocked by another still-open issue with no check for that at all, and the single best-scoped issue in the batch turning out to need macOS hardware I don't have. None of those are fixed yet; they're logged in `scan-notes.md` as real gaps. Settled on `intent-drift-skill#80` by hand-checking claim status directly via `gh` across a larger batch before reading any bodies, after the Issue Selection site surfaced the same already-claimed problem a third time. That pick needed an instructor exception though, since it came from my own scan rather than the site (the assignment allows this, but says to ask first), so I released it and went back to the site itself instead. Checking claim status *before* reading bodies this time (the same discipline, just applied to the site's own results) caught several more dead ends fast — a repo mistagged Python that's actually Haskell, an issue with four competing open PRs, a Fortran-primary climate-model repo, an internal tracking issue mixed into real candidates, and one good-looking issue already being worked by another contributor with an open PR up. **Final pick: `ImperialCollegeLondon/proCAT#860`**, genuinely clean and sourced from the site itself.

**Unit 6: Installed the voice-guard and wrote my repo memory file**

Installing the voice-guard hit the same Windows `claude`-CLI-not-on-PATH issue every eval harness has hit this term; fixed it the same way, with `shutil.which`. The guard caught a real mistake of mine before it went out: an early release-comment draft stated an old bug's root cause as my own verified fact when I'd only read it in a fix commit's comment, never run the broken code myself — the guard quoted my own rule back at me ("I name what I observed and what I only suspect, as two different things") and I rewrote it. For the memory file, one scoped-orientation run on `main/plots.py`/`views.py`/`models.py` found the exact root cause (`create_allocation_plot_for_project` returns early on zero allocations, before computing the project-FTE trace that doesn't need any) plus a caveat I hadn't seen myself: `Project.fte()` asserts dates are set, so the fix needs to guard on missing dates, not missing allocations. It also found that one existing test's premise (`test_time_evolution_plot_no_plot`) breaks once the fix lands and will need rewriting, not just a passing re-run — caught before I wrote a single line of the actual fix.

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

**This issue went through three picks before landing (2026-11-03), and I'm recording all of it rather than quietly editing history.** I claimed `#4356` and investigated it for real — a scoped orientation, then a live reproduction attempt on fresh installs of Chrome and Firefox. The setting saved correctly in both, the opposite of what's reported, and `repro-check` accepted that as an honest, evidenced non-reproduction. My remaining hypothesis (a storage-cache desync only visible on a non-fresh profile) would have taken another full test cycle to confirm, and I was a week behind, so with my professor's sign-off to pick something faster I released it and moved to `#4293` ("Cinema mode makes my screen go black") — unclaimed, well-diagnosed by another contributor in its thread. That one turned out to already be fixed: a commit from three weeks earlier had replaced the exact buggy mechanism the issue described, just under a different issue number (`#4353`) that was never linked back, so GitHub never closed it. Released that one too, then found `intent-drift-skill#80` by direct `gh` search rather than the course's curated site — a legitimate scout find, but the assignment's own text says a scan-found issue needs an instructor's okay first, which I hadn't gotten, so rather than assume it I released that claim and went back to the Issue Selection site itself, the assignment's stated default path. Checking claim status *before* reading bodies this time (the same discipline, just applied earlier) caught several more dead ends fast: a repo mistagged Python that's actually Haskell at core, an issue with four competing open PRs already racing to fix it, a Fortran-primary climate-model repo, an issue literally labeled `tracking` mixed into real candidates, and one good-looking issue already being actively worked by another contributor with located files and an open PR.

**Landed on `ImperialCollegeLondon/proCAT#860`** ("Allocation plot should be shown always in the project detail page"): genuinely clean — zero assignees, zero comments, zero timeline events ever, opened by a COLLABORATOR, with human commits as recent as four days before I claimed it. My scout's verdict: **accept**, difficulty note **moderate** — the issue describes the wrong behavior (the plot is hidden unless someone's allocated) without naming a file, so finding the actual location was mine to do; I traced it myself to `main/plots.py`'s `create_allocation_plot_for_project`, which returns early when there are no allocations instead of producing an empty/zero-state plot. This fits my profile well: a real Python backend/Django project, a concrete and bounded display-logic bug, not a CSS/frontend task. I hope to learn how to navigate an unfamiliar Django codebase well enough to find the right fix location myself, the way I had to here. **Claim comment:** https://github.com/ImperialCollegeLondon/proCAT/issues/860#issuecomment-6603747270.

**Toolkit**

- Tool: issue-scout (search strategy, ranking filters, and rubric grading, in both scan mode and direct mode against hand-picked site candidates), plus plain `gh` for fast claim-status verification across the later rounds
- What it produced: an initial scan whose top five all accepted but fit poorly; `#4356` and `#4293` chosen, claimed, and released in turn as investigation ruled each out; `intent-drift-skill#80` found and released once I realized it needed an instructor exception I hadn't gotten; `ImperialCollegeLondon/proCAT#860` as the final, genuinely clean, site-sourced pick
- Difficulties: the scan's own ranking tied almost the entire result set at 0 comments with no other ordering; the Issue Selection site's filters don't check claim status at all (true across three separate rounds of picks); `nobody-on-it` only disqualifies an *open* linked PR, so it silently would have passed an issue with an already-merged-but-unlinked fix (`#4188`, and effectively `#4293` too) had I not checked PR/commit history by hand each time
- What changed: added a per-repo cap and a wider frontend-label list to `search-strategy.md`; added, then renamed, the non-code-deliverable fail condition (`scope-bounded` → `scope-feasible`); logged the merged-PR gap and the "big unfiltered site pull needs per-candidate verification" lesson in `scan-notes.md` — neither fixed in the rubric yet, both real gaps for a future patch

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

*Note: this section covers the final issue, `proCAT#860`. The two
earlier reproduction attempts (`#4356`'s honest non-repro on a fresh
install, and `#4293` turning out to already be fixed) are preserved in
Why I Chose This Issue above as the reasons for each pivot — not
deleted, just no longer the active issue's repro record.*

**Environment Setup**

Fork `AdithNG/proCAT` cloned locally, main branch, current as of
2026-11-03. Python/Django environment set up with `uv sync` (uv
0.11.20) and `python manage.py migrate` — no Docker, no SSO/OIDC setup
needed since this doesn't touch auth. Test runner is plain `pytest`
(confirmed via README.md; CONTRIBUTING.md describes the testing
requirement but never actually states the command).

**Steps to Reproduce**

1. Using the repo's own `phase`/`project_static` test fixtures (a
   project with valid dates and a funded phase, but zero `Allocation`
   records), call `project.fte(include_excess=False)` directly.
2. Call `main.plots.create_allocation_plot_for_project(project,
   x_range=None)` on the same project.
3. Observed result: step 1 returns a real, computable FTE value (sum
   `42.65`) — the data a plot would need exists. Step 2 returns `None`
   anyway.

**Reproduction Evidence**

- **Commit showing reproduction:** none — an isolated pytest run, not committed (see My findings).
- **Screenshots/logs:**
  ```
  project.fte() with zero allocations: 42.65015190465062 (computable, not an error)
  create_allocation_plot_for_project() returned: None
  PASSED
  ```
- **My findings:** The bug reproduces cleanly today. `main/plots.py:678`'s
  `create_allocation_plot_for_project` checks whether any `Allocation`
  rows exist and returns `None` immediately if not — *before* it ever
  computes `project.fte(...)`, which is the actual data an "always show
  an initial FTE line" plot needs and which is fully available
  regardless of allocations. I confirmed this with a direct, isolated
  call rather than relying on the existing combined test
  (`test_time_evolution_plot_no_plot`), which conflates this gate with
  a separate one on the unrelated `evolution_plot`. One thing the issue
  itself doesn't mention: `project.fte()` asserts `start_date`/
  `end_date` are set, so a project with no dates would crash rather
  than show an empty plot — the eventual fix needs to guard on missing
  dates, not missing allocations. One honest gap: I only ran the
  zero-allocation case, not a comparison run with allocations present
  confirming the plot does render there — `repro-check` flagged this
  correctly as a failed preferred check, not a blocker.

**Toolkit**

- Tool: repro-check (live mode, full package: claim-draft.md +
  repro-draft.md graded together against the issue)
- What it produced: an `accept` verdict on a direct, isolated
  reproduction — every required check passed, with one preferred check
  (`control-comparison`) honestly failed rather than papered over
- Difficulties: the obvious existing test (`test_time_evolution_plot_no_plot`)
  doesn't cleanly isolate this bug on its own, since it conflates two
  separate plot-gating paths; writing a direct scratch test against
  the function itself was the only way to get unambiguous evidence
- What changed: nothing in the rubric; what I'd still do before
  building the actual fix is the comparison run `control-comparison`
  flagged as missing

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
