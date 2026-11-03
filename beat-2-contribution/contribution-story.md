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
**Pull Request:** https://github.com/ImperialCollegeLondon/proCAT/pull/867

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

**Unit 7: Installed the ship-gate, wrote my manifest, and let it catch a real mistake before the PR went out**

Manifest: `uv sync` → `uv run mypy .` → `uv run pytest`, sourced from `.github/workflows/ci.yml`, both checks clean on `main` before I touched anything. Installing the ship-gate hit the exact same Windows `claude`-CLI-not-on-PATH issue every tool's eval harness has hit this term; fixed `check_gate.py` the same way, with `shutil.which`. The real value showed up on the first hand-run: `pr-precheck`'s `description-matches-diff` check rejected my branch because my PR description said the renamed test (`test_time_evolution_plot_no_phases_still_shows_allocation_plot`) now covered the no-dates case — it doesn't, it asserts the opposite (the plot now shows); the actual no-dates case is a separate test I'd named loosely. I'd genuinely gotten it backwards, not just worded it sloppily. Fixed the description, re-ran, got `ship-gate: PASS` on the second try, with both the manifest's checks and all eight `pr-precheck` checks passing.

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

On a project's detail page, the allocation plot (the chart showing how much FTE capacity is allocated to the project over time) is only shown once someone has actually been allocated to the project. Until then, there's no plot at all, so there's no way to see the project's required FTE up front.

**Expected Behavior**

The plot should always show, even with zero allocations, starting with just the project's required FTE line so there's an initial sense of what the project needs before anyone is assigned.

**Current Behavior**

`main/plots.py:678`'s `create_allocation_plot_for_project` checks whether any `Allocation` rows exist for the project and returns `None` immediately if not — before it ever computes `project.fte(...)`, which is the actual FTE data the plot needs and which is fully computable regardless of allocations. The calling code (`create_project_evolution_layout`, `plots.py:764-772`) only appends the plot to the page if it isn't `None`, so the whole thing silently disappears.

**Affected Components**

`main/plots.py` (`create_allocation_plot_for_project`, the fix location), `main/models.py` (`Project.fte()`, which the fix needs to call earlier — and which asserts `start_date`/`end_date` are set, a caveat the fix has to guard against separately), and `tests/main/test_views.py` (`TestProjectDetailInlineView::test_time_evolution_plot_no_plot`, whose premise — that zero allocations means no plot at all — will need rewriting once the fix lands).

**Toolkit**

- Tool: my own reading of the code (scoped orientation, recorded in `CLAUDE.local.md`), confirmed by direct, isolated reproduction (see Reproduction Process)
- What it produced: the exact fix location and a caveat the issue itself never mentioned (the `start_date`/`end_date` assertion)
- Difficulties: none specific to this section — the harder part was isolating the bug cleanly, covered in Reproduction Process
- What changed: nothing yet; this section is the diagnosis the fix is built from

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

---

## Solution Approach

**Analysis**

`main/plots.py:678-684`, `create_allocation_plot_for_project`, gates its entire body on `Allocation.objects.filter(project=project).exists()` and returns `None` before computing anything. The actual data the plot needs — `project.fte(include_excess=False)` (`main/models.py:530`) — doesn't depend on allocations at all: it sums the FTE trace from the project's phases and returns a zero-valued series if there are none, never erroring. `fte()` does assert `self.start_date is not None` and `self.end_date is not None` (models.py:547-548), so that's the real precondition for the plot, not the presence of allocations. The calling code (`create_project_evolution_layout`, plots.py:754-772) just appends whatever `create_allocation_plot_for_project` returns, so fixing the one function fixes the page.

**Proposed Solution**

Move the `fte = project.fte(include_excess=False)` computation (and the "Project FTE" trace) above the allocations check, and replace the "no allocations → return None" early exit with a "no dates → return None" check instead, since that's the actual precondition `fte()` needs. The per-user allocation traces stay inside a loop over `Allocation.objects.filter(project=project)`, which naturally produces zero extra traces (just the one "Project FTE" line) when there are no allocations, instead of needing special-case handling. I'm deliberately **not** touching `create_project_evolution_plot` (the separate evolution/usage plot) or the outer `views.py` gate on the combined layout — both are unrelated to this bug, and `#859`'s open PR already touches a different part of this same file (multi-allocation traces), so I'm keeping this change minimal and non-overlapping.

**Implementation Plan**

1. In `main/plots.py`, rewrite `create_allocation_plot_for_project`'s guard clause: replace `if not allocations.exists(): return None` with a check on `project.start_date`/`project.end_date`, and move the `fte = project.fte(...)` line above it.
2. Rewrite `tests/main/test_views.py::TestProjectDetailInlineView::test_time_evolution_plot_no_plot`, whose current premise (zero phases/allocations ⇒ no plot at all) no longer holds — it needs to assert the plot now *does* appear with just the "Project FTE" line.
3. Add a direct unit test for `create_allocation_plot_for_project` in `tests/main/test_plots.py` covering the zero-allocation case explicitly (that file currently has zero references to "allocation" at all), per CONTRIBUTING.md's "every addition needs its own tests" rule.

[No deviations yet — will record here if the build surfaces one.]

**Toolkit**

- Tool: plan-check (live mode, `plan.md` + `plan-comment.md` graded against the issue and my own reproduction findings)
- What it produced: an `accept` verdict on every required check; the one honest miss was the preferred `risk-named` check — my plan states no open risk or unknown anywhere, which is true but worth naming rather than reading as fully certain
- Difficulties: the skill's live-mode procedure expects a posted week-2 repro comment on the issue as its evidence source, but this unit explicitly says not to post that report — I used my own `repro-draft.md`/Reproduction Process findings as the stand-in instead and flagged the gap rather than inventing a step
- What changed: nothing in the plan itself yet (it passed as written); noting for my own build that I should state an explicit risk next time rather than writing a fully-confident plan

---

## Testing Strategy

Re-ran the exact reproduction from Reproduction Process on the fix branch: before, `create_allocation_plot_for_project()` returned `None` despite a computable FTE value; after, it returns a real `figure` with a single "Project FTE" legend item. Three new direct unit tests in `tests/main/test_plots.py` cover the function itself (zero allocations, allocations present, no date range — the real remaining no-plot case). `tests/main/test_views.py::TestProjectDetailInlineView::test_time_evolution_plot_no_plot` is rewritten and renamed, since its old premise (zero allocations ⇒ no plot) no longer holds; a new test covers the genuinely-still-empty Tentative/no-dates case instead. Full suite: 272 passed (268 on `main`, net +4), `mypy .` clean, matching the ship manifest's checks. Full before/after output in `test_evidence.md`.

**Toolkit**

- Tool: the repo's own test suite (`uv run pytest`, `uv run mypy .`) plus a direct, isolated reproduction re-run on the branch
- What it produced: 272 passed (net +4 over main's 268), mypy clean, and a before/after transcript showing the exact symptom gone
- Difficulties: none — the existing test that broke (`test_time_evolution_plot_no_plot`) broke for exactly the reason predicted in the plan, which made rewriting it straightforward rather than a surprise
- What changed: nothing beyond what the plan already named

---

## Implementation Notes

[A running log, added to as you work. Date each entry. What you
built, decisions you made when you made them, wrong turns included.]

**2026-11-03**

Branch `fix/860-always-show-allocation-plot`. Made the fix exactly as planned: moved `fte = project.fte(...)` above the guard clause in `create_allocation_plot_for_project` and swapped the allocations check for a dates check. Ran `test_time_evolution_plot_no_plot` first to confirm it failed as predicted (a plot now appears, so its old "no plot" assertion no longer held) — that failure was itself confirmation the fix works, not a problem to hide. Rewrote it to assert the plot *does* appear with just the "Project FTE" line, renamed it since "no_plot" was no longer accurate, and added a genuinely-still-no-plot case (a Tentative project with no dates) to cover the real remaining gate. Also added three direct unit tests on `create_allocation_plot_for_project` itself in `test_plots.py` (zero allocations, allocations present, no dates) — that file had zero prior coverage of this function at all. Full suite: 272 passed (268 on main, net +4), `mypy .` clean. No deviations from the plan.

**Toolkit**

- Tool: `mypy`/`pytest` as a tight build-verify loop, run after each change rather than once at the end
- What it produced: an immediate, expected test failure that confirmed the fix worked before I'd written a single new test
- Difficulties: none — the one existing test's docstring was already stale/wrong before I touched it ("Check that a project with no phases has a time evolution plot" on a test that asserted the opposite), a pre-existing small bug I noticed but left alone since it's outside this issue's scope
- What changed: nothing beyond the plan; the build matched it exactly

---

## Pull Request

**PR Link:** https://github.com/ImperialCollegeLondon/proCAT/pull/867

**PR Description:** The allocation plot on a project's detail page was only shown once someone was actually allocated to the project, because `create_allocation_plot_for_project` returned `None` before ever computing the project's FTE data, which doesn't need any allocations to exist. The fix moves that computation above the guard clause and checks for a missing date range instead of missing allocations. Includes before/after test evidence (272 passed, net +4, mypy clean), a renamed/rewritten test for the behavior that changed, three new direct unit tests for the function itself, an honest note that pre-commit's `markdownlint-cli` hook couldn't be verified locally due to an unrelated Node.js environment issue (not part of the repo's actual CI), and a disclosure of how Claude Code was used throughout.

**Maintainer Feedback:**

- 2026-11-03: none yet — just opened.

**Status:** Awaiting review

**Toolkit**

- Tool: ship-gate (ran the manifest's 3 checks, then `pr-precheck` live on the branch), plus the Windows `claude`-CLI-not-on-PATH fix applied to `check_gate.py` itself
- What it produced: a real, caught mistake before the PR went out — `pr-precheck` flagged that my description conflated the renamed test (which now shows the plot) with the new no-dates test (the one that still shows nothing), which I'd genuinely gotten backwards; fixed it, re-ran, got `ship-gate: PASS`
- Difficulties: the same Windows PATH issue every tool's eval harness and hook has hit this term; fixed identically with `shutil.which`
- What changed: the PR description's accuracy, caught by the tool before a maintainer ever saw it

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
