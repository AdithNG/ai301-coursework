# Unit 4 — Test and Submit

Path: `beat-1-sandbox/unit-4/pull-request.md`

Record of the pull request you opened against the Path Review repo, and of the evaluation
runs that produced `eval-run.txt`.

---

## Your pull request

**Pull request**

https://github.com/codepath/pathreview-ai301-fa26-s1/pull/83

**Branch**

`fix/53-phone-regex-space`

## Eval iterations

**Run history**

1. `--limit 5` smoke run — **4/5**, one from each of `clear-accept`, `standards-wall`,
   `silent-drift`, and `not-tested` agreed; the one disagreement was `pkg-05`, rejected
   with `failed: test-evidence-complete`, which previewed the problem the full run then
   showed at scale.
2. Full 20-package run (no `--save-run`) — **16/20**, below the bar. `not-tested`,
   `silent-drift`, `standards-wall`, and `unreviewable` all matched fully, but
   `clear-accept` was only 3/7: four false rejects (`pkg-05`, `pkg-08`, `pkg-13`,
   `pkg-16`, `pkg-19`, depending on the run — see below), nearly all on
   `test-evidence-complete` demanding a literal shown transcript for every test-plan
   item, including ones that were really "pre-existing behavior is unaffected" controls
   a reviewer could verify by reading the diff itself (a guard clause that doesn't fire,
   a `gsub` with nothing to match).
3. `--only pkg-05,pkg-04,calib-04` after loosening `test-evidence-complete` to also
   accept a diff-provable no-op for that narrow kind of control — **2/2 scored**
   (`pkg-05` flipped to accept; `pkg-04` and `calib-04`, genuine `not-tested` rejects,
   held).
4. `--only pkg-08,pkg-13,pkg-16,pkg-19,pkg-04,pkg-07,pkg-10,pkg-14,pkg-03,pkg-06,pkg-09,pkg-17`
   after a second fix to `diff-in-scope` (a false reject on `pkg-16` over a plan that
   explicitly named changing a shared function, which my check had read as scope creep
   by speculating about an unseen caller) — **11/12**. Three of the four remaining
   false rejects flipped (`pkg-08`, `pkg-13`, `pkg-16`); every `not-tested` and
   `silent-drift` canary held. `pkg-19` still disagreed — see Package analysis.
5. Confirming full 20-package run with `--save-run eval-run.txt` — **19/20**, which is
   the `agreement: 19/20 scored items  (bar: 18/20: PASS)` line in the committed
   `eval-run.txt`. Category tallies on that run:
   `clear-accept 6/7  not-tested 4/4  silent-drift 4/4  standards-wall 2/2  unreviewable 3/3`.

**Package analysis**

`pkg-19` (mikefarah/yq#2819, "Preserve indent for single-quoted scalars ending in a blank
line"). **My tool: reject. Gold label: accept.**

The plan's diagnosis is grounded and the fix matches it exactly: the single-quoted
emitter's break-handling resets indentation state after writing a line break, the
double-quoted writer doesn't, and removing the reset fixes the trailing-blank-line
dedent. My tool's `diff-in-scope`, `description-matches-diff`, `diff-reviewable`,
`template-compliance`, and `ai-disclosure` all correctly pass this PR. The one check that
holds it is `test-evidence-complete`, on the plan's second control: "the two controls
unchanged (double-quoted twin; single-quoted without the trailing blank line)." The
double-quoted control is trivially provable from the diff alone — that writer's code
isn't touched at all. But the no-trailing-blank-line control sits inside the *same*
modified code path (`if is_break(...)`, which runs for every line break in the scalar,
not only the trailing one) with no shown transcript and no scenario-matching test added
for it. My tool reasoned that this makes it a genuine, non-trivial claim about the fix's
own behavior in a second scenario — not a "this part is untouched" control a diff alone
settles — and failed it on that basis, consistent with how it correctly caught the
fd `--threads` calibration package's un-shown second repro case.

I think this is one of the "genuinely arguable" calls the assignment pre-warns about,
not a clean miss: crediting it would require either trusting that intermediate breaks'
indentation state gets overwritten by subsequent content-writing code before it matters
(plausible, but not shown in the few lines of context the bundle includes), or accepting
a prose assertion with no artifact backing the specific claim. I chose not to loosen the
check further to cover this case, because the same wording change that would credit it
risks re-opening exactly the gap `test-evidence-complete` exists to close in the
`not-tested` category (a claimed-but-unshown second scenario) — and the two canaries I
ran to confirm that category's floor (`pkg-04`, `calib-04`) are built on the identical
shape of claim.

**Check rationale**

The check as it is currently written in the uploaded `tools/pr-precheck/rubric.md`:

> | `test-evidence-complete` | The test-evidence section plus the diff's own content, read against every item the plan's test plan named (every repro step, every control run, the suite/checks run) — not just the first one shown. | Every item the plan's test plan named is satisfied by one of three things: (1) a shown, observable result in the test-evidence section (a concrete before/after, an exit code, a specific output value); (2) a named, scenario-specific automated test visible in the diff whose assertions directly match that item, confirmed as included in a shown passing test run; or (3), for an item that only claims *pre-existing, untouched behavior is unaffected* (a control, not a second fix scenario), the diff's own control flow provably shows that code path is untouched or structurally a no-op for that case — trace it yourself rather than demanding a separate transcript for a claim the diff already settles. A test merely claimed to exist with no visible name or matching assertion does not count, and (3) never covers a claim that the *fix itself* correctly handles a second, distinct scenario or failure mode (that still needs (1) or (2)). Fails when an item has none of the three, when the evidence proves nothing observable at all, or when the repo's own checks were never run. | required |

It started with only clause (1): a shown result, full stop. The first full run showed
three different packages (`pkg-08`, `pkg-13`, `pkg-19`, later joined by `pkg-05` and
`pkg-16` for other reasons) all failing on a "control unchanged" item asserted in prose
with no separate transcript — but in most of those cases the diff itself made the claim
trivially checkable: a guard clause (`if !AnyTrackedChanges() { return err }`) that
simply doesn't run the original code when false, a `gsub` over a character class that's
a no-op when none of those characters are present. Demanding a transcript for a claim
the diff already proves was punishing a correct, verifiable PR for not restating
something redundant.

Clause (2) came from `pkg-05` specifically: a control covered by a named, passing unit
test (`same_name_same_key_replaces_with_warning`) whose assertions matched the test-plan
item exactly, which my first version didn't credit at all because it only looked for
prose transcripts. Clause (3) is deliberately narrow — it only covers a claim that
something *untouched* stays untouched, never a claim that the *fix* works in a second
scenario — specifically so it couldn't swallow the `not-tested` category's real failure
mode, which is exactly a second scenario claimed and never shown.

**Trade-offs**

Clause (3)'s narrowness is also its cost: it only credits a control when the diff's
control flow genuinely proves the claim on its own (a guard that doesn't fire, a
no-match substitution), and `pkg-19` is the package that falls on the wrong side of that
line — its "untouched" claim sits inside modified control flow, not separate from it, so
I can't credit it from the diff alone the way I can for `pkg-08` and `pkg-13`, and it
stays a disagreement with gold. I accept that cost over the alternative of loosening
clause (3) to cover "the claim is plausible from reading nearby code," because that
version would have to extend to any claim a confident diagnosis makes about code the
bundle doesn't fully show — which is precisely the overclaiming pattern `test-evidence-complete`
and the `not-tested` category exist to catch. The three canaries I re-ran after each
revision (`pkg-04`, `calib-04` for clause 2; `pkg-04`, `pkg-07`, `pkg-10`, `pkg-14` for
clause 3) all stayed correctly rejected, which is what tells me the narrower version,
not a looser one, is the right place to have landed.

---

Related paths: `eval-run.txt` in this directory; your skill's files in
`tools/pr-precheck/`.
