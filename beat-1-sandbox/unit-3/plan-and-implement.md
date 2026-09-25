# Unit 3 — Plan and Build

Path: `beat-1-sandbox/unit-3/plan-and-implement.md`

Record of your plan, the branch you built it on, and the evaluation runs that produced
`eval-run.txt`.

---

## Posted upstream

**GitHub username**

AdithNG

**Plan comment**

https://github.com/codepath/pathreview-ai301-fa26-s1/issues/53#issuecomment-5831785431

````
Following up with my plan. `PII_PATTERNS["phone_us"]` in `safety/pii_scrubber.py` allows an optional dash or dot at each separator position but not a space, which is exactly why `(555) 123-4567` doesn't match — I isolated this by testing the pattern directly against each format in `test_us_phone_formats`: the dash/dot formats match, the two space-separated ones don't, and closing the space in the parenthesized case restores the match.

Plan: add a literal space alongside the dash/dot at all three separator positions, one line in `PII_PATTERNS["phone_us"]`, nothing else. I'm deliberately using a literal space rather than `\s`, since `\s` would also match newlines and could span unrelated lines in prose text — flagging that as the one thing I haven't stress-tested beyond this repo's own test suite.

Test: the issue's own snippet plus the four named failing tests (`test_us_phone_number_redaction`, `test_us_phone_formats`, `test_detect_phone_pii`, `test_phone_at_start_of_text`) flipping from `XFAIL` to passing, with the rest of the suite staying green.

Not touching in this change: the `street_address` pattern's separate over-matching issue I noticed while investigating last week (it's what actually fails `test_mixed_pii_and_text`, unrelated to this bug) — that's a different defect for a different issue.

Update: built the change above and re-ran the four named tests plus the full suite; all green, no regressions. One thing I found while verifying: the fix leaves the opening `(` unredacted on a parenthesized number (`scrub()` now returns `"Call me at ([REDACTED]..."`, no digits leak, but the `(` itself stays). I checked and this is a pre-existing property of the original pattern's `\b`/`\(?` placement, not something this change introduced — it just never surfaced before because the space-separated case never matched at all. Leaving it as-is since it's cosmetic and the named tests don't cover it; happy to take a follow-up if it's worth cleaning up.
````

---

## Your branch

**Branch**

`fix/53-phone-regex-space`

**Evidence**

Before (unfixed `main`, commit `f89c06f`):

```
$ .venv/Scripts/python -c "
from safety.pii_scrubber import PIIScrubber
s = PIIScrubber()
print(s.scrub('Call me at (555) 123-4567 or 555-123-4567'))
print(s.detect('Call me at (555) 123-4567'))
"
Call me at (555) 123-4567 or [REDACTED]
[]

$ .venv/Scripts/python -m pytest tests/unit/test_pii_scrubber.py::TestPIIScrubber::test_us_phone_number_redaction tests/unit/test_pii_scrubber.py::TestPIIScrubber::test_us_phone_formats tests/unit/test_pii_scrubber.py::TestPIIScrubber::test_detect_phone_pii tests/unit/test_pii_scrubber.py::TestPIIScrubber::test_phone_at_start_of_text -v
tests/unit/test_pii_scrubber.py::TestPIIScrubber::test_us_phone_number_redaction XFAIL
tests/unit/test_pii_scrubber.py::TestPIIScrubber::test_us_phone_formats XFAIL
tests/unit/test_pii_scrubber.py::TestPIIScrubber::test_detect_phone_pii XFAIL
tests/unit/test_pii_scrubber.py::TestPIIScrubber::test_phone_at_start_of_text XFAIL
============================= 4 xfailed in 0.75s ==============================
```

After (branch `fix/53-phone-regex-space`, commit `0ffbc93`):

```
$ .venv/Scripts/python -c "
from safety.pii_scrubber import PIIScrubber
s = PIIScrubber()
print(s.scrub('Call me at (555) 123-4567 or 555-123-4567'))
print(s.detect('Call me at (555) 123-4567'))
"
Call me at ([REDACTED] or [REDACTED]
[{'type': 'phone_us', 'value': '555) 123-4567', 'start': 12, 'end': 25}]

$ .venv/Scripts/python -m pytest tests/unit/test_pii_scrubber.py::TestPIIScrubber::test_us_phone_number_redaction tests/unit/test_pii_scrubber.py::TestPIIScrubber::test_us_phone_formats tests/unit/test_pii_scrubber.py::TestPIIScrubber::test_detect_phone_pii tests/unit/test_pii_scrubber.py::TestPIIScrubber::test_phone_at_start_of_text -v
tests/unit/test_pii_scrubber.py::TestPIIScrubber::test_us_phone_number_redaction PASSED
tests/unit/test_pii_scrubber.py::TestPIIScrubber::test_us_phone_formats PASSED
tests/unit/test_pii_scrubber.py::TestPIIScrubber::test_detect_phone_pii PASSED
tests/unit/test_pii_scrubber.py::TestPIIScrubber::test_phone_at_start_of_text PASSED
============================== 4 passed in 0.53s ==============================

$ .venv/Scripts/python -m pytest tests/unit/test_pii_scrubber.py -v -m unit
... (25 items)
======================== 24 passed, 1 xfailed in 0.78s ========================
```

(The one remaining `xfail`, `test_mixed_pii_and_text`, is the unrelated `street_address`
over-matching defect — out of scope per `plan.md`, unaffected by this change.)

Full unit suite (`tests/unit`, `-m unit`), to confirm nothing outside `pii_scrubber` moved:

```
================ 379 passed, 49 xfailed, 5 warnings in 13.14s =================
```

`ruff check`, `black --check`, and `mypy` all pass clean on both changed files.

## Eval iterations

**Run history**

1. `--limit 5` smoke run — **5/5**, one from each of `clear-accept`, `thread-convention`,
   and `wrong-cause`.
2. Full 20-package run (no `--save-run`) — **19/20**. The one disagreement was `pkg-14`
   (gold `accept`), rejected with `failed: plan-executable`: the check's wording demanded
   a named function or line for every step, but the plan named a concrete subsystem
   (`zellij-server`'s session connection handling) plus an already-working debug method
   (`zellij --debug` output the author states shows the leak's origin) for pinning the
   exact function — meaningfully more concrete than the genuinely vague unbuildable
   packages, which name no subsystem at all.
3. `--only pkg-14,pkg-10,pkg-17,pkg-18` after loosening `plan-executable` to accept a
   named module plus an already-validated pinning method — **3/4**. The three
   `unbuildable` canaries (`pkg-10`, `pkg-17`, `pkg-18`) stayed correctly `reject`
   (none of them name any subsystem at all, let alone a validated method), confirming the
   loosened wording didn't cost the category. `pkg-14` still disagreed, but on a
   different check this time (`diagnosis-grounded`, over how far the plan's reasoning
   about a thread-reported cache observation should be read) — the fix I made worked as
   intended; this second disagreement reads as ordinary grading variance on a genuinely
   subtle package rather than a rubric gap, so I did not chase it with another edit.
4. Confirming full 20-package run with `--save-run eval-run.txt` — **19/20**, which is
   the `agreement: 19/20 scored items  (bar: 18/20: PASS)` line in the committed
   `eval-run.txt`. Category tallies on that run:
   `clear-accept 6/7  scope-creep 4/4  thread-convention 2/2  unbuildable 3/3  wrong-cause 4/4`.

**Package analysis**

`pkg-14` (zellij-org/zellij#5174, "OSC color sequences leak into terminal on session
reattach via SSH"). **My rubric: reject. Gold label: accept.**

The plan is strong on every other axis: scope is one bounded change (the Unix reattach
handshake) with an explicit, reasoned deferral of the Windows variant the thread also
reports; the test plan re-runs the repro's own 5-cycle reattach loop with an exact
observable ("no rgb strings in any pane"); a risk is named and mitigated (bounding the
input drain to OSC patterns rather than a timer, to avoid eating a keystroke). What my
rubric's `diagnosis-grounded` check catches is a secondary piece of the diagnosis's own
reasoning: the plan says "the cache control fits: with an empty cache the color data is
refetched along the fresh-attach path once," explaining a thread commenter's observation
that clearing `~/.cache/zellij` produces one clean attach before the leak returns. My
check reads the repro evidence's own control line — "the next attach is clean, the one
after leaks again" — as ambiguous about whether that "next attach" is itself a reattach
(which the plan's core theory says should always leak, cache or not) or a fresh session
create (which the plan already says is clean). The plan's own explanation for why an
empty cache would route even a reattach through the clean fresh-attach path is stated
but not independently verified against anything in the repro evidence the way the
primary signal is (the 0.44.1-clean / 0.44.2-leaking regression window, and fresh-attach
staying clean on every run). My check is built to fail a diagnosis when any part of its
reasoning isn't traceable to a specific fact in the evidence, and this is a real instance
of that — a secondary, corroborating piece of reasoning, not the plan's central claim,
resting on an inference the evidence doesn't fully settle either way.

**Check rationale**

The check as it is currently written in the uploaded `tools/plan-check/rubric.md`:

> | `plan-executable` | The plan's approach/steps and any files, modules, or locations it names. | A stranger holding only the plan and the linked evidence could start immediately: each step names a concrete file or module, and either a concrete function/line within it or a concrete, already-validated method for pinning the exact spot (a debug tool or log output the plan states it already has working) — not merely an intention to go figure out where to look. Fails when a step names no file or module at all and substitutes investigate/profile/look into/explore language, when the plan expresses real uncertainty about which subsystem is even responsible (naming candidate layers with no way to tell them apart), or when it defers the file/module itself — not just the exact line — to the build. | required |

It started narrower: "each step names a concrete file, function, or location," full stop.
The first full run showed exactly why that was too strict. `pkg-14`'s plan names the two
specific files/subsystems responsible (`zellij-server`'s session connection handling,
`zellij-client`'s terminal query issuance) and states it already has a working diagnostic
(`zellij --debug` output) that shows the leak's origin — it just hasn't run that tool yet
to write down the exact function name. That is a fundamentally different situation from
the genuinely unbuildable packages in the set (`pkg-10`, `pkg-17`, `pkg-18`), which name
no file at all and use "investigate," "profile," or "look into" as a stand-in for having
found the problem's location. Grading both the same way — demanding a pinned function in
every case — punished a plan that had actually located the defect down to two named
modules and a validated way to finish narrowing it.

The fix keeps the same bar (a stranger must be able to start immediately) but widens what
counts as "enough to start": a named file/module plus a stated, already-working method
for pinning the rest, not just a named function/line outright. It still fails a plan that
names no file or module, or that admits real uncertainty about which subsystem is even
responsible.

**Trade-offs**

The looser wording is what let a plan stop short of the exact function, as long as it
names the file/module and a validated way to find the rest — and I confirmed with three
canaries (`pkg-10`, `pkg-17`, `pkg-18`, all `unbuildable`) that this did not cost the
category: none of them names any subsystem at all, so the loosened bar still rejects them
outright, for the same reason as before.

What the looser wording gives up: a plan that names a big, correct-sounding module ("the
input handling layer") and claims to have "a way to narrow it down" without actually
demonstrating that method already works, could now pass on the strength of the claim
alone rather than the file name plus a genuinely validated tool. `pkg-14`'s plan earns
the pass because it says the debug output is something it "has working," past tense, not
something it plans to try — but my check can't independently verify that claim any better
than `diagnosis-grounded` can independently verify a diagnosis's every inference, which
is exactly the axis `pkg-14` still disagrees on. I accept that gap because the eval set's
`unbuildable` category is specifically built around plans with no named location at all
(as the three canaries confirm), not around plans that name a location and lightly
overstate how validated their narrowing method is — that finer distinction would need its
own check, and I'd rather add it when I have a package that actually tests it than guess
at the wording now.

---

Related paths: `plan.md` and `eval-run.txt` in this directory; your skill's files in
`tools/plan-check/`.
