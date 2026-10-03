# Evidence guide: where evidence lives in a PR package

## Plan fidelity (harness category: silent-drift)

**Where it lives.** In an eval bundle: the plan-context block's `Plan`
paragraph (its scope line, its "Not in scope" line, and the files it
names) and any deviation notes it carries, read against the candidate
PR's `Diff` and `Description`. In live mode: your own `plan.md`
(deviation notes section included) and `git diff main...HEAD` from
your working copy, read against your draft title and description.

**What good looks like.** Walk the diff file by file, hunk by hunk.
Every one either sits inside what the plan's scope names, or is
explicitly covered by a deviation note that made it into the
description. Read the plan's scope paragraph literally: when it names
changing a specific function or file, a hunk that makes exactly that
change is in scope even if that function is shared by other callers
elsewhere in the codebase — don't fail a hunk over speculation about
an unseen caller or a downstream effect the bundle never shows you. A dependency bump, a new config field, a "while I'm in
there" refactor, or a second file the plan never mentioned are all
drift, however small or well-intentioned, unless disclosed. Read the
description's own claims the same way: "implements the plan exactly,"
"no other changes," or a specific mechanism description is a factual
claim about the diff, not a tone — check it against the diff's actual
content, not against how confident it sounds. A description that says
less than the diff does ("no other changes" when a feature rode along)
fails this exactly as hard as a diff that does less than the plan
promised.

## Test evidence (harness category: not-tested)

**Where it lives.** The candidate PR's `Test evidence` section, read
against the plan-context block's `Test plan` line item by item — every
repro step it names, every control run, the suite or checks it says to
run. In live mode: your own captured before/after output and the
repo's own check commands (lint, typecheck, test suite — whatever the
repo's own `make`/CI config runs), read against your posted plan's test
plan and your week-2 repro steps.

**What good looks like.** Count the items the plan's test plan named,
then find each one's result — not just the first or the most dramatic,
and not only in the test-evidence section. A result counts in either of
two forms: a concrete output shown directly (a specific value, an exit
code, a line that changed), or a named automated test visible in the
diff whose assertions line up with that specific item, included in a
shown passing run. The second form is not a lesser substitute — a
scenario-matching test that runs in CI every time is at least as
decisive as a one-off pasted transcript, and the two are often used for
different items of the same test plan (a manual before/after for the
headline repro, a unit test for a secondary control). What fails this
family either way: a plan that named two failure modes or two repro
cases where only one has either form of result, with the second
silently absent; a bare "tests pass" or "confirmed working" with
nothing printed and no test identifiable by name; or a claim that a test
exists with no visible test to check the claim against. The repo's own
checks (the suite, the linter, whatever `CONTRIBUTING.md` or the PR
template names) are actually run, with their outcome visible, not
asserted from memory.

## Diff quality (harness category: unreviewable)

**Where it lives.** The unified diff itself, hunk by hunk, and the
commit list above it.

**What good looks like.** Every hunk earns its place: a reviewer could
read straight through and see only the fix. Debris tells are concrete
and specific — a commented-out line or block, a `// TODO` or `# DBG`
scratch note, a function defined but never called, an `eprintln!`/
`print`/`console.log` left in from debugging, or a hunk whose only
change is whitespace/reformatting with no behavior difference. A
commit list of "wip" / "fix" / "fmt" tells the same story at the
history level. None of this is about length or polish — a five-line
diff can carry debris, and a three-hundred-line diff can be perfectly
clean if every line is the fix.

## Standards and comms (harness category: standards-wall)

**Where it lives.** The repo-facts block's stated PR-template
asks/required checklist and its contribution/AI-use policy, read
against the description's actual content and the diff. In live mode:
the repo's real PR template (`.github/PULL_REQUEST_TEMPLATE.md` or
similar) and `CONTRIBUTING.md`/`AI_POLICY.md`, read against your draft.
Any explicit maintainer direction in the thread (a stated preference, a
cause already pinned down) belongs here too, read against what the PR
actually does.

**What good looks like.** Treat every section or checkbox the template
states as required the same way you'd treat a rubric's own required
check: present, with real content specific to this change, not the
template's own placeholder text left untouched and not a generic line
that could sit on any PR. When the template or a bug-fix convention
calls for an artifact — a changelog entry, a doc update, an explicit
"Closes #N" — that artifact needs to actually be in the diff, not just
promised in prose. Whether the description's claims match the diff is
plan fidelity, above; this family is only about whether the repo's own
stated asks were honored. Where a policy requires AI-use disclosure,
the description names the tool and the extent of the assistance
explicitly — the same standard as weeks 2 and 3's comments. Silence in
a policy is not a requirement; only a stated one creates one.
