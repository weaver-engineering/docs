# Design The Feature — Instructions

## Context
* [Feature Workflow](feature-workflow.md) - the workflow step (`2 Design The Feature`) this process carries out
* [Design Directory And HLD](design-directory-and-hld.md) - the HLD, Internal Component, and External Dependency shapes this process populates
* [Specific Behaviors](specific-behaviors.md) - the `SB-NNN` shape and Given/When/Then/Call Tree format this process populates
* [HLD Template](../../templates/HLD-TEMPLATE.md), [Internal Component Template](../../templates/INTERNAL-COMPONENT-TEMPLATE.md), [External Dependency Template](../../templates/EXTERNAL-DEPENDENCY-TEMPLATE.md), [Specific Behavior Template](../../templates/SPECIFIC-BEHAVIOR-TEMPLATE.md) - the fill-in-the-blank shapes this process fills in
* [Use Case Template §Technical Interpretation](../../templates/USE-CASE-TEMPLATE.md) - what §2 below produces
* [Pseudocode Style](pseudocode-style.md) - the notation §2, §4, and §7.1 write and compare

These are the steps an agent follows to carry out the `Design The Feature` step of the [Feature
Workflow](feature-workflow.md) and produce a complete design directory. The architect's judgement is required at
specific, named points (§4.1 ideation, §5 elicitation and sanity-checks, §7.2 final review) — everywhere else the
process is mechanical. It is also iterative, not a strict waterfall — see §8 — and deliberately broken into
small, independently-resumable units of work: §1 defines how to tell, from the current state of a design
directory alone, exactly which unit comes next, so that a session with no memory of any previous one can pick up
immediately.

## 1 Determining The Next Unit Of Work

Before doing anything, check the design directory's current state in this order. The first gap found is the
next unit of work; everything before it is already done.

1. Does the HLD exist with its Scope naming every use case in this Feature? If not, confirm scope before
   anything else — nothing downstream can start.
2. For each use case in scope: does it have a non-empty `## Technical Interpretation`? Any that don't need §2,
   one use case at a time.
3. Does the HLD's Internal Components and External Dependencies list classify every function the Technical
   Interpretations collectively touch as as-is, extended, or new? If not, §3 is next.
4. For every item classified extended or new, and every Open Design Question carried by a use case in scope: is
   there a resolved Key Decision with a Rationale entry naming every alternative considered? Any unresolved need
   §4.1, one gap at a time. Once every individual gap is resolved, has the merge pass (§4.2) run since the last
   one was added? If not, that's next.
5. For every `SB-NNN` stub created in §2: does it have at least one fully-derived behavior, rather than only its
   `//TODO`? Any that don't need §5, one `SB-NNN` at a time.
6. For every derived behavior: does its call tree's every node appear in its parent's declared `calls:`? Any
   mismatch needs §6, one reconciliation issue at a time.
7. For every `SB-NNN` clean per §6: does its `reconciliation:` block (§7.1) have checksums that still match the
   design's current content? If the block is empty, or a checksum no longer matches, §7.1 is next.
8. For every `SB-NNN` clean per §7.1: does its `reconciliation:` block have `reviewed_by` set? If not, §7.2 is
   next.

Once every `SB-NNN` in scope reaches step 8, the design is complete — ready for `Chunk The Design`
([Feature Workflow](feature-workflow.md) §3). This check can be scoped to one use case, one operation, or the
whole Feature: told to "work on `UC-105`," an agent runs the same sequence restricted to that use case's own
artifacts.

## 2 Phase 1: Technical Interpretation

For each use case in scope, independent of every other (chunkable per use case): rewrite its Main Success
Scenario and Extensions as pseudocode, in technical language, entirely independent of any solution — naming no
Internal Component, no External Dependency, no call tree. Record it as the use case's own `## Technical
Interpretation` appendix (see the [Use Case Template](../../templates/USE-CASE-TEMPLATE.md)), not in the design
directory.

While writing this pseudocode, every point where the actor crosses the system's own boundary is an operation
(Design Directory And HLD §4.4). Identify each one as it's written and immediately create its `SB-NNN` stub:
filed, linked from this Technical Interpretation, with a single `//TODO` standing in for its behaviors — Given,
When, Then, and Call Tree aren't known yet, that's §5. Do this for every operation the use case implies,
regardless of whether anything about how it's satisfied is understood yet; an operation that later turns out to
need no new design work at all still gets its stub here, because §5 still has to derive its specific behaviors.

Exit: every use case in scope has a Technical Interpretation, and every operation it implies has a linked
`SB-NNN` stub.

## 3 Phase 2: Gap Analysis

Whole-Feature, not per use case: read every in-scope use case's Technical Interpretation together, as one body
of pseudocode, and classify every Internal Component or External Dependency function it touches — existing or
newly needed — as one of:

* **As-is** — an existing function already produces what's needed without gaining any call it doesn't currently
  make. Its `calls:`/pseudocode doesn't change; its usage lists (Design Directory And HLD §3.4, §4.5) still grow
  to record the new use case relying on it.
* **Extended** — an existing function needs to gain a call it doesn't currently make.
* **New** — no existing function serves this need at all.

This is judgement, not a literal diff: compare what the pseudocode needs against each candidate's existing
`calls:` and Purpose, and ask whether invoking it as currently specified already closes the loop. Only relevant
functions get classified — the rest of the project's existing catalog is never enumerated just to mark it
irrelevant.

Exit: the HLD's Internal Components and External Dependencies sections list every relevant function, each
classified as-is, extended, or new (see the [HLD Template](../../templates/HLD-TEMPLATE.md)).

## 4 Phase 3: Ideation And Solution Shape

Every item classified extended or new in §3, and every Open Design Question carried by a use case in scope, is
a gap needing a decision. This phase runs in two passes.

### 4.1 Per-Gap Ideation

Chunkable one gap at a time, independent of every other gap. For each: ask "how might we close this gap" and
generate candidate solutions — genuinely more than one where more than one is plausible, since the question's
own conditionality ("might") means no candidate starts out favored. Score each against the project's NFRs and
the gap's own requirements, then choose one. Record the choice as a Key Decision (Design Directory And HLD
§2.1). Record every candidate that wasn't chosen in the HLD's `# Rationale`, with why it was discounted —
analysis of its qualities, a logical argument that rules it out, or a proof-of-concept's finding. A candidate
ruled out by a proof-of-concept records the PoC's finding in that same Rationale entry; the PoC itself doesn't
need its own permanent artifact.

Where the gap is closed by extending an existing function (§3), find every other use case now relying on
pseudocode that's about to change before making the change. An `IC-000` function or an External Dependency
operation names them directly (Design Directory And HLD §4.5, §3.4). A non-entry-point Internal Component
function doesn't carry that list itself — walk its `called_from:` (Design Directory And HLD §4.1) back, one hop
at a time, until reaching the `IC-000` function(s) it terminates at, then read their usage lists from there.
Either way, every use case found this way has an existing `reconciliation:` block (§7.1) that becomes stale the
moment the change is made, not at some later point someone happens to notice; each becomes due for
re-verification, which §1's own check picks up mechanically the next time it runs for that `SB-NNN`, via the
checksum that no longer matches.

Exit, per gap: a resolved Key Decision with a Rationale entry naming every considered alternative and why it
was discounted.

### 4.2 The Merge Pass

Whole-Feature, once every individual gap has its own resolution: read every Key Decision from §4.1 together and
look for gaps that turned out, once visible side by side, to want the same thing — two independently-chosen
functions that are really one (a Key Decision for "post a large purchase order" and another for "post a
purchase order" to the same queue, say, that should just be one function). Where they are, consolidate them into
a single Key Decision and a single Internal Component or External Dependency function, and update every affected
`SB-NNN` stub's expected call tree to match.

Exit: no two Key Decisions from this Feature describe functions that should have been the same one.

## 5 Phase 4: Deriving Specific Behaviors

Per `SB-NNN` stub created in §2 (chunkable one at a time), regardless of whether §3/§4 did anything with the
operation it covers:

1. Establish every valid entry state (Given) this operation needs a behavior for. The use case's own
   Preconditions and Extensions are the first source; where they don't fully specify one, elicit the missing
   detail directly from the architect rather than inventing it.
2. For each entry state, systematically trace it through the operation's pseudocode — the top-level function's
   own, and every nested Internal Component or External Dependency's pseudocode or prose it calls — to derive
   the concrete Then, rather than authoring it freehand.
3. Present the resulting behavior to the architect immediately, one at a time, for a quick sanity check — not a
   formal approval, just "is this really what the use case demands." Revise and re-check if not.

Exit: every `SB-NNN` has its happy path and every identified unhappy path (Specific Behaviors §2.5) recorded as
a fully-derived behavior, each already sanity-checked as it was written.

## 6 Phase 5: Call Tree Reconciliation

Chunkable per individual reconciliation issue, and idempotent — re-running it when nothing has changed just
re-confirms there's nothing to do. For every derived behavior, check that every node in its call tree appears in
the `calls:` declared by its parent (Specific Behaviors §2.7). Where it doesn't:

* If it's a genuine design gap — the call was never actually decided — return to §4.1 to make that decision.
* If it's a simple documentation error — the `calls:` list is stale or mistyped — correct it directly; no new
  decision is needed.

Exit: every behavior's call tree is fully backed by its nodes' declared `calls:`.

## 7 Phase 6: Design Review

### 7.1 Mechanical Reconciliation

The assertion this check makes is a subset relationship, not textual equality: under some valid entry state,
could the combined pseudocode and function prose reachable from the operation's entry point produce every
behavior the relying use case's Technical Interpretation requires? The Technical Interpretation is the
specification — what the use case demands. The entry point's own pseudocode, plus everything it calls, is what's
actually been designed. "Compare" means walking the designed pseudocode's own `<--` calls, `IF`/`ELSE` branches,
and `ON FAILURE` handling to find a corresponding path for each step the Technical Interpretation requires, not
checking whether the two read the same — they can't, one is solution-independent and the other names real
addresses (Pseudocode Style §2). The designed pseudocode may legitimately do more than any one use case asks of
it, since the same operation can serve several use cases at once (§3, each covered by its own accounted-for
requirement) — it must never do less. A Technical Interpretation step with no corresponding path through the
designed pseudocode is exactly the gap this check exists to catch.

The check also runs the other way: walking the designed pseudocode under this behavior's own entry state, does
it produce any External Dependency interaction that neither the Technical Interpretation nor the behavior's own
Then anticipates? That's an unexpected external side effect, and "the required behaviors are all present too"
doesn't excuse it — it gets flagged regardless. Resolving it is never mechanical: either the pseudocode or prose
is wrong and needs to conditionally exclude the effect for this entry state, or the use case's own understanding
was incomplete and the use case itself needs correcting to expect it. Which way to go is always a human
judgement call (§8) — this is the one point in the whole process where a finding can send the design back past
its own scope, into Analysis.

Run this for every `SB-NNN` clean per §6, against its operation — plus, for every External Dependency operation
involved, the same subset check against its `used_by:` list (Design Directory And HLD §3.4): is every relying
use case's actual usage still something the shim's declared interface is capable of, or has one quietly started
needing more than a thin shim can honestly give it?

Also walk every function's pseudocode for exceptions that are both unhandled and undeclared: every `RAISE` and
every uncaught propagation from a call, checked against whether it's either caught locally or added to that
function's own declared contract for whatever calls it (see [Pseudocode Style §4](pseudocode-style.md)). Anything
neither caught nor declared is a reconciliation failure — a failure mode nobody has designed a response for —
not something to wave through because the rest of reconciliation is otherwise clean.

Record a passing result in the `SB-NNN` document's own `reconciliation:` block (see the [Specific Behavior
Template](../../templates/SPECIFIC-BEHAVIOR-TEMPLATE.md)): a checksum of the relevant slice of the use case's
Technical Interpretation, and a checksum of every involved function's pseudocode or prose, as of the moment
reconciliation passed. This makes the record falsifiable — recomputing the same checksums later and finding a
mismatch means something the reconciliation depended on has since changed and it needs to be re-run, without
re-reading or re-reasoning about content that hasn't actually moved.

Exit: the `SB-NNN`'s `reconciliation:` block has checksums matching the design's current content.

### 7.2 Human Review

Only once §7.1 is clean for an `SB-NNN` does this start, agent-driven: present each of its behaviors
individually, not batched, with the use case detail it realizes, the provenance of each Given condition (which
document defined it, whether the architect supplied it directly, or it was inferred and on what basis), and its
effects. Unlike §5's quick sanity check, this is the real review — what's approved here becomes the specific
behaviors a Chunk's failing tests are built from (Specific Behaviors §2.9).

Exit: the `SB-NNN`'s `reconciliation:` block has `reviewed_by` and `reviewed_at` set.

## 8 The Feedback Loop

Design is iterative, not a strict pass through §2–§7. A problem found during §7.2 review, or later during
`Chunk The Design` ([Feature Workflow](feature-workflow.md) §3), sends the design back to whichever phase
actually owns the broken decision — usually §4.1 (a Key Decision needs revisiting) or §5 (a behavior was wrong)
— which gets revised there. Everything downstream is then re-validated by re-running §1's own check from that
point forward, rather than assumed still correct: a stale `reconciliation:` block (§7.1) is exactly the
mechanism that catches this without anyone needing to remember to check by hand. The same mechanism is what
catches the cascading case in §4.1: extending a function that other use cases already rely on invalidates their
`SB-NNN`s' own reconciliation, not just the gap that prompted the change.

Most findings loop back within Design. One doesn't: an unexpected external side effect (§7.1) can be resolved
by correcting the use case itself rather than the pseudocode, which sends the design back past its own scope
into Analysis — the one point in this process where that's the right destination, not a sign the process has
gone somewhere it shouldn't.

# Rationale

**Why this is a separate document from Design Directory And HLD and Specific Behaviors, rather than folded into
them.** Those two documents define what a complete design looks like — the shape of the HLD, the format of a
specific behavior. This document defines how to get there. Burying the process inside the shape definitions
means a reader trying to find "what do I do next" has to reconstruct a sequence from scattered mechanics
described for other reasons; keeping it separate, the way [Retrospective
Instructions](retrospective-instructions.md) is already kept separate from [Retrospective
Report](retrospective-report.md) in this same directory, means the process can be read start to end on its own,
and the shape documents stay focused on what they're actually for.

**Why §1 exists at all, ahead of the phases themselves.** The entire point of breaking design into small,
resumable units (per WVR-106) is that a new session, with no memory of any previous one, can pick up immediately
— which only works if "what's next" is derivable mechanically from the documents' own current state, not
recalled from context that a fresh session doesn't have. Every phase's own exit criteria (stated at the end of
each section) is written so §1's check can be automated later, even though today it's read and applied by hand.

**Why Gap Analysis (§3) needs three states, not two.** "New or extended" was the original framing, but it
collapses a real distinction: an existing function this design relies on but doesn't change at all ("as-is") is
a different fact from one gaining a call it doesn't currently make ("extended") — the first needs no further
decision, the second needs one in §4. Two states would either force as-is items to be called "extended" (and
then need a Key Decision they don't actually require), or leave them unclassified (and then be indistinguishable
from something nobody checked at all).

**Why ideation (§4.1) requires recording discarded alternatives, not just the winner.** A Key Decision that only
states what was chosen looks identical whether one option was ever seriously considered or five were. Recording
every discarded candidate and why is what makes "how might we" a real question asked with initially equal
weight, rather than a formality wrapped around a decision already made — and it gives a later reviewer, or a
later Feature revisiting the same gap, the actual reasoning to check against instead of just a conclusion.

**Why the merge pass (§4.2) is a separate step from per-gap ideation, not folded into it.** A gap considered on
its own has no visibility into any other gap's resolution — two independently-correct answers to two different
questions can still turn out to want the same underlying function, and there's no way to notice that until both
answers already exist to compare. Running it as its own whole-Feature pass, after every individual gap is
resolved, is what makes that comparison possible at all.

**Why §5's per-behavior check and §7.2's review are different events, not one.** §5 happens the moment a
behavior is first derived, informally, to catch an obviously wrong result before moving on to the next one —
speed matters more than rigor there. §7.2 happens only once §7.1's mechanical reconciliation is already clean,
with full provenance for every condition, because what's approved there is what a later Chunk's failing tests
are built from — rigor matters more than speed there. Merging them would either slow down §5's fast iteration
loop or under-scrutinize the review that actually matters.

**Why §7.1 is a subset check, not an equality check.** A use case's Technical Interpretation and an entry point's
own designed pseudocode are written for different purposes and can never read the same — one is deliberately
solution-independent, the other names real addresses (Pseudocode Style §2). Requiring them to match textually
would be requiring something that's structurally impossible by design, not a meaningful check. Requiring instead
that everything the Technical Interpretation asks for has a corresponding path through the designed pseudocode
is the actual property that matters — the design satisfies the use case — and it's the same relationship a
shared operation already needs between several use cases at once (§3): each one only needs to be covered, not to
be the whole story.

**Why an unexpected side effect can't be resolved mechanically.** The subset check (above) can tell, mechanically,
whether an effect is present or absent — it can't tell whether an unanticipated one is a bug in the design or a
gap in the use case's own understanding, because both look identical from the design's side: an External
Dependency interaction the Technical Interpretation never mentioned. Only a human looking at what the effect
actually is, in context, can tell which. That's also why it's the one finding in this whole process allowed to
send work back into Analysis rather than staying inside Design (§8) — the design didn't necessarily do anything
wrong, so the fix isn't necessarily the design's to make.

**Why `reconciliation:` is checksums, not a checkbox.** A checkbox only ever records that a check passed once;
it can't say whether it's still true. Checksums make the record falsifiable: recompute them later, and a
mismatch against what's recorded means something the check depended on has changed, without needing to re-read
or re-reason about content that hasn't actually moved — the same shape of argument this repo's own `.index/`
files (documentation-standards.md §4) make for staying accurate without a human re-deriving them by hand.
