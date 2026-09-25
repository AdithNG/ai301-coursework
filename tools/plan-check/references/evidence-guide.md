# Evidence guide: where evidence lives in a plan package

## Diagnosis and grounding

**Where it lives.** The plan's `Diagnosis`/`Cause` section (or its
equivalent opening paragraph), read line by line against every line of
the `## Repro evidence` block — its numbered steps, its control runs, and
any artifact fact stated there (an exit code, a value, a line number a
prior investigator pinned). In live mode: the student's own posted week-2
repro comment on the issue (or the house repro pack) stands in for the
repro-evidence block.

**What good looks like.** Every control run and artifact fact in the
repro evidence is consistent with the claimed cause — none of them would
behave differently than shown if the claimed cause were true. A
diagnosis that matches the reported symptom but not a specific control
run (a control that still works normally on the exact code path the
diagnosis blames, or an artifact that times a defect to a different
stage than the diagnosis names) has failed this before any other check
runs, however confidently it's stated or however good the citation
attached to it. Re-derive the mechanism from the evidence yourself
rather than trusting the plan's own summary of it — a wrong diagnosis
reads exactly like a right one until you check it against a control.

## Scope

**Where it lives.** The plan's `Scope`/`Proposed changes` section: what
it says is in scope, what it explicitly rules out, and the full list of
changes it proposes, read together (a plan can name a narrow in-scope
line and then propose five items that blow past it).

**What good looks like.** One bounded change sized to the reported
defect, with an explicit not-in-scope line naming what the plan is
deliberately not touching. Watch for the tell phrase: "rather than
patch/fix just X, I plan to fix the whole/properly Y" is the plan
announcing its own scope creep. So is any item introduced with "while
I'm in there," a new user-facing option nobody asked for, a dependency
migration, or a test-harness rewrite bundled onto a bug fix. A plan that
lists both the minimal fix and extra unrelated work is scored on the
whole list, not the first item.

## Executability

**Where it lives.** The plan's `Approach`/`Files`/`Steps` section, and
any file paths, function names, or line numbers it names.

**What good looks like.** A stranger with the plan and the linked
evidence could start the first step without asking the author anything:
each step names a concrete file or module, and either a concrete
function/line or a concrete, already-validated method for pinning the
exact spot within it — a debug flag, a log format, a tool the plan
states it has already run and that already shows the origin. That is
different from "investigate," "profile," "look into," "explore," or
"whatever turns up" standing in for a named location, or from naming
two or three candidate subsystems with no stated way to tell which one
is responsible — both of those mean the plan hasn't actually found
where the problem lives yet, which also usually means
`diagnosis-grounded` has nothing to check, since there is no stated
cause to verify.

## Test plan

**Where it lives.** The plan's `Test plan` section, read against the
repro evidence's own numbered steps, controls, and any exact
values/exit-codes they show.

**What good looks like.** The test plan re-runs the repro evidence's own
steps (or an equally specific substitute) and names the exact observable
result that would prove the defect gone — a specific line of output, an
exit code, a control that must stay unchanged. "The suite passes" or
"nothing regresses" with no tie back to the reported symptom is not a
test plan for this defect; "should feel faster" or "should look better"
names no signal a stranger could check.

## Honesty

**Where it lives.** Any risks, open questions, or stated unknowns in the
plan, read against how certain the rest of the plan's language is. Live
mode only: after a build deviates, the same place in an updated
`plan.md` (its deviation notes section) is where an honest mid-build
change gets recorded — the skill re-grades the updated package, and a
deviation that exists only in the diff and never in the plan is the
dishonest version of this same family.

**What good looks like.** The plan says what it hasn't verified yet (an
unmeasured cost, a question left for review, a case outside its tested
scenarios) instead of presenting every part of the approach as settled.
An unnamed risk is not automatically a failure elsewhere in the rubric,
but a plan that states no unknowns at all while making a nontrivial
change is not being fully honest about what building it will show. A
deviation note that says plainly what changed and why, even when the
change is embarrassing ("the approach in the posted plan didn't work
because X"), is the honest form; a plan silently rewritten to match
whatever got built is not.

## Comms

**Where it lives.** The plan comment, read against two things: the
issue's `## Thread highlights` (or the live thread) for any maintainer
signal — a stated direction, an in-progress patch, an explicit
preference — and the repo-facts block's contribution policy / AI-use
policy line.

**What good looks like.** When the thread already shows where a
maintainer wants this to go, or that they are already partway into a
fix themselves, the plan comment says so and either follows that
direction or gives a reason for diverging — silently pursuing an
unrelated angle (documentation instead of the code fix a maintainer
already patched toward, for instance) while never mentioning what the
thread already contains is not thread-aware, however good the plan is
otherwise. Where the repo's policy requires disclosing AI assistance,
the comment states it explicitly, naming the tool and the extent of the
help, the same standard as week 2's claim and repro comments. Silence in
the policy is not a disclosure requirement; only a stated policy creates
one.
