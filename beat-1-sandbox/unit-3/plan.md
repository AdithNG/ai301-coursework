# Plan: issue #53 — PII scrubber fails to redact parenthesized US phone numbers

Repo: `codepath/pathreview-ai301-fa26-s1`. Builds on the Unit 2 reproduction
posted at https://github.com/codepath/pathreview-ai301-fa26-s1/issues/53#issuecomment-5795322543.

## Diagnosis

`PIIScrubber.PII_PATTERNS["phone_us"]` in `safety/pii_scrubber.py` is:

```
\b(?:\+?1[-.]?)?\(?([0-9]{3})\)?[-.]?([0-9]{3})[-.]?([0-9]{4})\b
```

The three separator positions (after an optional `+1` prefix, after the
area code, and between the last two digit groups) each accept an
optional dash or dot (`[-.]?`) but not a space. My Unit 2 repro report
isolated this directly: testing the pattern against each format named
in `test_us_phone_formats` showed the two dash/dot formats
(`555-123-4567`, `555.123.4567`) match, while the two space-separated
ones (`(555) 123-4567`, `+1 555 123 4567`) do not — and closing the
space in the parenthesized case (`(555)123-4567`) restores the match,
isolating the space itself as the missing character, not the
parentheses. The issue's own reproduction ("Call me at (555) 123-4567
or 555-123-4567" scrubbing the second number but not the first) is one
instance of this same gap.

## Scope

**In scope:** widen the separator character class in the three
positions of `PII_PATTERNS["phone_us"]` to also accept a literal space,
so all four formats in `test_us_phone_formats` — and the issue's own
example — redact and detect correctly.

**Not in scope:**

- The `street_address` pattern's unrelated over-matching bug I found
  while investigating in Unit 2 (`Pl` matching case-insensitively
  inside ordinary words like "applications," which is what actually
  fails `test_mixed_pii_and_text` — not the phone bug). That is a
  separate defect from a different issue and this plan does not touch
  it.
- The other `PII_PATTERNS` entries (`email`, `phone_intl`, `ssn`).
  Nothing about this fix changes them.
- Using `\s` (which would also match newlines/tabs) instead of a
  literal space — see Risk below for why.

## Approach

File: `safety/pii_scrubber.py`, the `phone_us` entry in
`PIIScrubber.PII_PATTERNS`.

Change the pattern from

```
\b(?:\+?1[-.]?)?\(?([0-9]{3})\)?[-.]?([0-9]{3})[-.]?([0-9]{4})\b
```

to

```
\b(?:\+?1[-. ]?)?\(?([0-9]{3})\)?[-. ]?([0-9]{3})[-. ]?([0-9]{4})\b
```

— adding a literal space alongside the dash and dot at all three
separator positions. No other line in the file changes.

## Test plan

Re-run my Unit 2 repro steps against the change:

1. The issue's own snippet:
   `PIIScrubber().scrub('Call me at (555) 123-4567 or 555-123-4567')`
   must return both numbers redacted (currently: only the second is).
   `PIIScrubber().detect('Call me at (555) 123-4567')` must return a
   non-empty list (currently: `[]`).
2. The four named failing tests must flip from `XFAIL` to passing —
   and since they are `strict=True`, CI will fail with `XPASS(strict)`
   until the markers are removed, which is the signal the fix landed:
   `test_us_phone_number_redaction`, `test_us_phone_formats`,
   `test_detect_phone_pii`, `test_phone_at_start_of_text`.
3. Full `tests/unit/test_pii_scrubber.py` suite stays green apart from
   those four flipping, confirming no other PII pattern regressed
   (in particular `test_detect_no_false_positives` and
   `test_scrub_idempotent`, which would catch new false positives from
   an over-broad separator).
4. Direct regex check that the fix does not span newlines (a phone
   number split across lines by unrelated formatting must not
   false-positive): `re.search(pattern, "555\n123\n4567")` must stay
   `None`.

## Risk

I chose a literal space (`[-. ]?`) over `\s` deliberately: `\s` also
matches newlines and tabs, which would let the pattern span line breaks
in prose text (for example a resume with unrelated numbers on adjacent
lines) and redact things that are not phone numbers. I have not tested
this against a large corpus of real resume/report text beyond the
course's own test suite, so a wider false-positive case beyond what
`test_detect_no_false_positives` covers is possible; I'll watch for it
in review rather than expand the fix to guard against inputs nobody
has reported.

## Deviations

The change matches the plan exactly: one line in `PII_PATTERNS["phone_us"]`,
nothing else, and the four named tests flip from `XFAIL` to passing with
the rest of the suite (25 tests in the file, then the full 380-test unit
suite) staying green.

One thing the build taught me that the plan didn't anticipate: the fix
leaves a stray leading `(` unredacted. `scrub()` on the issue's exact
snippet now returns `"Call me at ([REDACTED] or [REDACTED]"` — the
closing `)` is consumed by the match, but the opening `(` is not,
because `\b` cannot match between the preceding space and `(` (both are
non-word characters), so the match itself starts one character later,
at the first digit. No digits leak — both assertions in
`test_us_phone_number_redaction` still pass — but the output isn't
fully clean.

I confirmed this is a pre-existing property of the original pattern, not
something my change introduced: the original regex has the same `\(?`
placement and would leave the same stray `(` on a no-space parenthesized
input like `(555)123-4567` — it just never surfaced before, because the
space-separated case never matched at all. I'm leaving it as-is rather
than widening this fix's scope to also anchor the match on the opening
parenthesis: it's a cosmetic artifact with no PII exposure, the four
named tests don't check for it, and fixing it isn't needed to close the
reported bug. Flagging it in the PR for review rather than silently
folding it in.
