# Unit 2 — Claim and Reproduce

Path: `beat-1-sandbox/unit-2/reproduction.md`

Record of your claim and reproduction on the issue you chose in Unit 1, and of the
evaluation runs that produced `eval-run.txt`.

---

## Your identity upstream

**GitHub username**

AdithNG

---

## Posted upstream

**Claim comment**

https://github.com/codepath/pathreview-ai301-fa26-s1/issues/53#issuecomment-5795289338

```
Hi! I'd like to take this as a first contribution. I set up the repo and ran the four tests named above (`test_us_phone_number_redaction`, `test_us_phone_formats`, `test_detect_phone_pii`, `test_phone_at_start_of_text`); all four currently xfail, matching the report.

Reading `PIIScrubber.PII_PATTERNS["phone_us"]` in `safety/pii_scrubber.py`, my guess is the pattern allows an optional dash or dot between the area code and the next group (`\)?[-.]?`) but not a space, so `(555) 123-4567` (space after the closing parenthesis) wouldn't match while `555-123-4567` does — I haven't run the regex in isolation to confirm that's the actual mechanism yet, just read it off the pattern.

Next I'll write up a full reproduction (environment, exact commands, before/after output) and post it here, then look at how to widen that separator without over-matching other formats before proposing anything.
```

**Reproduction comment**

https://github.com/codepath/pathreview-ai301-fa26-s1/issues/53#issuecomment-5795322543

````
## Reproduction report

**Environment:** Windows 10.0.26100 (Git Bash), Python 3.12.3, repo at commit `f89c06f` (main, 2026-09-16). I did **not** run the full `make setup` (Docker/Postgres/Redis, `alembic upgrade head`, frontend install) — this bug and its tests are pure-Python with no DB or API dependency, so I created a venv and ran `pip install -e ".[dev]"` directly, then `pytest` against just `tests/unit/test_pii_scrubber.py`. Flagging that deviation from the documented setup path in case it matters for anyone re-running this.

**Steps:**

```
$ python -m venv .venv
$ .venv/Scripts/pip install -e ".[dev]"
$ .venv/Scripts/python -m pytest tests/unit/test_pii_scrubber.py -v -m unit
```

**Observed — the four tests the issue names:**

```
tests/unit/test_pii_scrubber.py::TestPIIScrubber::test_us_phone_number_redaction XFAIL
tests/unit/test_pii_scrubber.py::TestPIIScrubber::test_us_phone_formats XFAIL
tests/unit/test_pii_scrubber.py::TestPIIScrubber::test_detect_phone_pii XFAIL
tests/unit/test_pii_scrubber.py::TestPIIScrubber::test_phone_at_start_of_text XFAIL
```

All four `xfail` (fail as expected), matching the issue.

**Observed — the issue's own repro snippet, run directly:**

```
>>> from safety.pii_scrubber import PIIScrubber
>>> s = PIIScrubber()
>>> s.scrub('Call me at (555) 123-4567 or 555-123-4567')
'Call me at (555) 123-4567 or [REDACTED]'
>>> s.detect('Call me at (555) 123-4567')
[]
```

Matches the issue exactly: the dashed number is redacted, the parenthesized one is not, and `detect()` reports nothing for it.

**Mechanism, isolated:** `PIIScrubber.PII_PATTERNS["phone_us"]` is
`\b(?:\+?1[-.]?)?\(?([0-9]{3})\)?[-.]?([0-9]{3})[-.]?([0-9]{4})\b` — the
separators after the area code (`\)?[-.]?`) accept an optional dash or dot
but not a space. Testing the pattern directly against each format named in
`test_us_phone_formats`:

```
>>> import re
>>> pattern = r"\b(?:\+?1[-.]?)?\(?([0-9]{3})\)?[-.]?([0-9]{3})[-.]?([0-9]{4})\b"
>>> [ (t, re.search(pattern, t, flags=re.IGNORECASE)) for t in
...   ["555-123-4567", "(555) 123-4567", "555.123.4567", "+1 555 123 4567"] ]
[('555-123-4567', <...match '555-123-4567'>),
 ('(555) 123-4567', None),
 ('555.123.4567', <...match '555.123.4567'>),
 ('+1 555 123 4567', None)]
```

Confirms my claim comment's guess: the dash/dot formats match, the two
space-separated ones (parenthesized-with-space, and the `+1` international
prefix written with spaces) don't. So the underlying gap is "no space in the
separator class," and the issue's parenthesized case is the instance of it a
reporter actually hit. As a direct control, closing the space
(`(555)123-4567`, no space after the parenthesis) does match, isolating the
space itself as what breaks the parenthesized case:

```
>>> re.search(pattern, "(555)123-4567", flags=re.IGNORECASE)
<...match '555)123-4567'>
```

**Expected:** `scrub()` redacts `(555) 123-4567` the same way it redacts
`555-123-4567`, and `detect()` reports it.

**Actual:** confirmed as described — the parenthesized format passes
through `scrub()` unredacted and `detect()` finds nothing, while the dashed
format is handled correctly, in this environment and on current `main`.
````

## Eval iterations

**Run history**

1. `--limit 4` smoke run — **4/4**, one from each of `clear-accept`, `no-evidence`, and
   `wrong-target` (both `wrong-target` items in the sample agreed).
2. Full 20-package run (no `--save-run`, kept the results with `--out` for inspection) —
   **19/20**. The one disagreement was `pkg-01` (gold `accept`), which my rubric rejected
   with `failed: environment-recorded`: the check flagged the report's `multidict 6.6.0`
   as an unacknowledged version difference, when that version only appears in the
   thread's root-cause discussion of a *different*, already-fixed issue (`#1637`), not as
   a stated reproduction requirement for this one — and the report's own artifact still
   showed the bug regardless of that version.
3. `--only pkg-01,pkg-16,pkg-06` after rewriting the check — **3/3**. `pkg-01` flipped to
   `accept` (the fix); `pkg-16` and `pkg-06` (canaries: both correctly `reject` via
   `environment-recorded` before the edit, for real version/environment gaps) stayed
   `reject`, so the loosened wording did not cost either of them.
4. Confirming full 20-package run with `--save-run eval-run.txt` — **20/20**, which is the
   `agreement: 20/20 scored items  (bar: 18/20: PASS)` line in the committed `eval-run.txt`.
   Category tallies on that run:
   `clear-accept 8/8  disclosure 1/1  no-evidence 4/4  unfollowable-comms 3/3  wrong-target 4/4`.

**Package analysis**

`pkg-16` (pandas-dev/pandas#66656, "tuple name at creation is fine, but `rename_axis`
with that tuple fails"). **My rubric: reject. Gold label: reject.**

The candidate's repro report runs the issue's exact public example and shows the exact
traceback the thread's own root-cause comment predicts (`ValueError: Length of new names
must be 1, got 3` from `Index.set_names`) — `steps-followable`, `evidence-supports-claim`,
and `control-comparison` all pass cleanly on the artifact alone. What fails it is
`environment-recorded`: the report's environment is `pandas 1.5.3 (pip), Python 3.10.12,
Ubuntu 22.04`, but the issue's own template requires confirming the bug "on the latest
version and on the main branch," which the reporter did (and a thread comment separately
confirms on `2.3.3`/main) — and the candidate silently substitutes a roughly four-year-old
release with no acknowledgment of the gap at all, unlike (for example) `calib-01`'s
candidate, who explicitly names and reasons about testing a different version than the
report. My rubric reads a *silent* version substitution against a repo's own stated
confirmation requirement as a fail, even when the same bug happens to still reproduce
there, because a stranger reading the comment has no way to tell whether that agreement
is the same bug or a coincidence one version apart — the report's own confidence
("confirmed... on both versions") is exactly the kind of claim my rubric's `Honesty`
family (in the evidence guide) treats as needing to match what was actually verified
against the repo's own bar for "confirmed," not just what happened to run.

**Check rationale**

The check as it is currently written in the uploaded `tools/repro-check/rubric.md`:

> | `environment-recorded` | The repro report's environment record (tool version, OS/platform, and any other config, driver, or build dimension the issue itself calls behavior-relevant — e.g. "debug build panics, release build wraps", or "Windows + vmware driver"). | Every dimension the issue's own report, its bug-report template, or a maintainer/thread comment states is REQUIRED to confirm reproduction (a specific OS, a specific driver, a build profile such as debug-vs-release, or an explicit "confirm on the latest version / main branch" template ask) is stated with a concrete value. When the report's value differs from what that requirement names, the report says so and reasons about it (e.g. "the issue was filed against 13.0.0; behavior is unchanged on 15.2.0"). Fails when such a required dimension is missing entirely, or a differing value is used with no acknowledgment. A dependency's version mentioned only as background for a different, already-fixed issue's root cause does not need separate acknowledgment when the report's own artifact still demonstrates the reported symptom regardless — the artifact carries that weight instead. | required |

It started narrower — "if a stated value differs from the version/config the issue *or
its thread* confirms the bug on, the report says so" — and the first full run showed why
that was too broad: `pkg-01`'s thread names a `multidict` version only while explaining
why a *different*, already-closed issue (`#1637`) happened, not as a condition for
reproducing *this* one, and the candidate's own artifact reproduced the reported symptom
regardless of that number. Treating every version mentioned anywhere in a thread as
something the report must reconcile punished a clean report for a detail that was never
a reproduction requirement in the first place.

The fix keeps the same shape — state the dimension, or acknowledge a difference — but
narrows *which* dimensions count: only ones the issue's own report, its template, or a
comment states are required to confirm the bug at all (a stated OS/driver dependency, an
explicit "confirm on latest/main" template ask), not any version number that comes up in
passing. It also adds an explicit carve-out for a dependency version raised only as
background on a different issue's root cause, when the artifact itself still shows the
reported behavior — the artifact is left to carry that weight instead of the environment
line.

**Trade-offs**

The narrower wording is what let `pkg-01` flip from reject to accept without touching
`pkg-16` or `pkg-06`, both re-run as canaries in the same `--only` batch specifically
because they also fail on `environment-recorded` and use the same "differs from the
issue's stated version/config" logic the edit rewrote — `pkg-16`'s silent four-year-old
pandas against an explicit "confirm on latest/main" template ask, and `pkg-06`'s missing
OS/driver entirely against an issue that is explicitly Windows-plus-`vmware`-specific.
Neither moved, because both differences are exactly what the narrowed condition still
requires acknowledging: a stated confirmation requirement (pandas' template) or a stated
behavior-determining dimension (minikube's driver), not incidental thread chatter.

What the narrowing gives up: a report that name-drops a stale dependency version
*and* the issue's thread happens to be discussing a real regression in that same
dependency, with no comment flagging the coincidence, would now pass this check on that
detail alone — my rubric would have to catch it, if at all, on `evidence-supports-claim`
matching the wrong symptom instead. I accept that gap because I'd rather have the check
fire on dimensions a package actually tells the reader are required than have it flag
every version number a thread happens to mention, which is what cost `pkg-01` its correct
`accept` in the first place.

---

Related paths: `eval-run.txt` in this directory; your skill's files in
`tools/repro-check/`.
