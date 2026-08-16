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
5. For every `SB-NNN` stub clean per step 4: does it carry a bound-pseudocode block (§4.3) for each use case
   that relies on it? Any that don't need §4.3, one `SB-NNN` at a time.
6. For every `SB-NNN` stub created in §2: does it have at least one fully-derived behavior, rather than only its
   `//TODO`? Any that don't need §5, one `SB-NNN` at a time.
7. For every derived behavior: does its call tree's every node appear in its parent's declared `calls:`? Any
   mismatch needs §6, one reconciliation issue at a time.
8. For every `SB-NNN` clean per §6 (step 7): does its `reconciliation:` block (§7.1) have checksums that still
   match the design's current content? If the block is empty, or a checksum no longer matches, §7.1 is next.
9. For every `SB-NNN` clean per §7.1: does its `reconciliation:` block have `reviewed_by` set? If not, §7.2 is
   next.

Once every `SB-NNN` in scope reaches step 9, the design is complete — ready for `Chunk The Design`
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
of pseudocode, and walk it piece by piece — a single call (`<--[...]`), an entire branch, or, on occasion, a
whole Technical Interpretation in one go — asking, of each candidate Internal Component or External Dependency
function: could this function's own pseudocode or prose stand in for this piece without changing what it
describes? The answer is always one of:

* **As-is** — yes, and the candidate needs no call it doesn't already make. Its `calls:`/pseudocode doesn't
  change; its usage lists (Design Directory And HLD §3.4, §4.5) still grow to record the new use case relying on
  it.
* **Extended** — yes, but only once the candidate gains a call it doesn't currently make.
* **New** — no existing function stands in for this piece at all.

This is judgement, not a literal diff: compare what the piece needs against each candidate's existing `calls:`
and Purpose, not against its exact wording. Only relevant functions get classified — the rest of the project's
existing catalog is never enumerated just to mark it irrelevant. The same question, asked again later against
the design's current state rather than a candidate's, is what §7.1's reconciliation re-checks.

Exit: the HLD's Internal Components and External Dependencies sections list every relevant function, each
classified as-is, extended, or new (see the [HLD Template](../../templates/HLD-TEMPLATE.md)).

## 4 Phase 3: Ideation And Solution Shape

Every item classified extended or new in §3, and every Open Design Question carried by a use case in scope, is
a gap needing a decision. This phase runs in three passes.

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

Once every gap's Key Decision is settled and consolidated, every Internal Component or External Dependency named
in the HLD's §5/§6 needs its own standing document — that document is the actual design artifact defining its
functions, not just the HLD's own summary of it, and it will always be needed once something is listed there at
all. For each one not yet created: create it — [Internal Component
Template](../../templates/INTERNAL-COMPONENT-TEMPLATE.md) or [External Dependency
Template](../../templates/EXTERNAL-DEPENDENCY-TEMPLATE.md) — assigned the next free `IC-NNN`/`ED-NNN` in the
project's own sequence, with one numbered `§M`/`§M.N` section per function or operation this Feature currently
needs (a later Feature may add more to the same document rather than creating a competing one, per Design
Directory And HLD §3.2's ownership rule). Link it from the HLD's §5/§6 entry, replacing the bare name Gap
Analysis (§3) recorded there.

`IC-000` is the one component every project needs from its first Feature onward (Design Directory And HLD §4.4).
If this is that first Feature, `IC-000`'s own document doesn't exist yet either — creating it, with this
Feature's own operations as its first numbered functions, is part of this same step, not a special case to defer
or skip.

Exit: no two Key Decisions from this Feature describe functions that should have been the same one, and every
component or dependency named in the HLD's §5/§6 has its own standing document — numbered, linked from the HLD,
with a placeholder section per function or operation this Feature needs.

### 4.3 Recording The Bound Pseudocode

Once every gap in scope is closed (§4.1, §4.2), each `SB-NNN` stub's operation has, for every piece of Technical
Interpretation it covers, either an as-is/extended function already classified in §3 or a newly-decided one from
§4.1/§4.2. For each use case relying on that operation (Specific Behaviors §3), walk that use case's own
Technical Interpretation slice one more time and substitute each call, branch, or whole body with the function
now decided to satisfy it — binding each abstract target (`[name - args]`) to its real address
(`[address: name - args]`, Pseudocode Style §2). Record the result as this `SB-NNN`'s own bound pseudocode,
alongside its existing step-range Realizes (Specific Behaviors §3, §4) — one bound-pseudocode block per relying
use case, since each use case's own Technical Interpretation is a separate, immutable source, even where two
happen to bind to the exact same functions.

The Technical Interpretation itself is never edited by this step. Substituting into it produces a new, derived
artifact recorded on the `SB-NNN` document — a change to the use case's own Technical Interpretation is a change
to the use case (Specific Behaviors §2.1), not something this step, or any step of Design, is permitted to make.
This bound pseudocode is what §5 traces to derive each behavior's Then, and what §7.1 checksums against —
rather than either re-deriving the substitution from scratch each time, or comparing the Technical Interpretation
directly against a function's own pseudocode with nothing on record showing how the two were ever actually bound
together.

Exit: every `SB-NNN` stub in scope carries a bound-pseudocode block for each use case that relies on it.

## 5 Phase 4: Deriving Specific Behaviors

Per `SB-NNN` stub created in §2 (chunkable one at a time), regardless of whether §3/§4 did anything with the
operation it covers:

1. Establish every valid entry state (Given) this operation needs a behavior for. The use case's own
   Preconditions and Extensions are the first source; where they don't fully specify one, elicit the missing
   detail directly from the architect rather than inventing it.
2. For each entry state, systematically trace it through this `SB-NNN`'s own bound pseudocode (§4.3) — following
   each bound call into the Internal Component or External Dependency's own pseudocode or prose it names — to
   derive the concrete Then, rather than authoring it freehand.
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

The subset relationship this check protects — under some valid entry state, does the operation's bound
pseudocode (§4.3) produce every behavior the relying use case's Technical Interpretation requires? — was already
established once, piece by piece, when that bound pseudocode was substituted into existence. Re-establishing it
here isn't a fresh semantic walk by default: it's confirming nothing it depended on has since changed. Recompute
the checksum of each relying use case's Technical Interpretation slice and of every function's pseudocode or
prose the bound pseudocode names, and compare against what's recorded in the `SB-NNN`'s own `reconciliation:`
block. A match means the substitution is still valid, cheaply. A mismatch — the Technical Interpretation changed
(a use-case-level edit, invalidating the requirement itself), or some function's pseudocode changed (an
extension elsewhere, found via §4.1's own `called_from:` walk) — means §4.3's substitution has to run again for
this operation: the same judgement as the first time, not a shortcut around it.

The check also runs the other way, and this half can't be shortcut by a checksum: walking the bound pseudocode
under this behavior's own entry state, does it produce any External Dependency interaction that neither the
Technical Interpretation nor the behavior's own Then anticipates? That's an unexpected external side effect, and
"the required behaviors are all present too" doesn't excuse it — it gets flagged regardless. Resolving it is
never mechanical: either the pseudocode or prose is wrong and needs to conditionally exclude the effect for this
entry state, or the use case's own understanding was incomplete and the use case itself needs correcting to
expect it. Which way to go is always a human judgement call (§8) — this is the one point in the whole process
where a finding can send the design back past its own scope, into Analysis.

Run this for every `SB-NNN` clean per §6, against its bound pseudocode — plus, for every External Dependency
operation involved, the same subset check against its `used_by:` list (Design Directory And HLD §3.4): is every
relying use case's actual usage still something the shim's declared interface is capable of, or has one quietly
started needing more than a thin shim can honestly give it? Unlike the operation-level check above, this one
isn't the same substitution judgement run again — it's comparing several already-settled usages against each
other and against the shim's own declared interface for uniformity, not asking whether one thing can stand in
for another.

Also walk the bound pseudocode for exceptions that are both unhandled and undeclared: every `RAISE` and every
uncaught propagation from a call, checked against whether it's either caught locally or added to that function's
own declared contract for whatever calls it (see [Pseudocode Style §4](pseudocode-style.md)). Anything neither
caught nor declared is a reconciliation failure — a failure mode nobody has designed a response for — not
something to wave through because the rest of reconciliation is otherwise clean.

Record a passing result in the `SB-NNN` document's own `reconciliation:` block (see the [Specific Behavior
Template](../../templates/SPECIFIC-BEHAVIOR-TEMPLATE.md)): a checksum of each relying use case's own Technical
Interpretation slice, and a checksum of every function's pseudocode or prose the bound pseudocode names, as of
the moment reconciliation passed. This makes the record falsifiable — recomputing the same checksums later and
finding a mismatch means something the reconciliation depended on has since changed and it needs to be re-run,
without re-reading or re-reasoning about content that hasn't actually moved.

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

**Why standing document creation is folded into §4.2 rather than left implicit.** The first real design pass
(dogfooding this process against WVR-95) closed out every gap in §4.1, decided every component's interface, and
only then noticed nothing in the process actually said when those components' own `IC-NNN`/`ED-NNN` documents
get created and numbered — including `IC-000` itself, which had never been created at all, since this was the
project's first Feature. §4.1 alone can't be where this happens: a gap considered in isolation can't yet know
whether the merge pass is about to fold it into another one, and numbering a document only to immediately
discover it duplicates another wastes the one thing a project-level number is supposed to be — stable once
assigned. §4.2, once the deduplicated set of components is finally known, is the first point where creating and
numbering them doesn't risk exactly that.

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

**Why the bound pseudocode is recorded on the `SB-NNN`, not left implicit in §3's classification.** Classifying
a function as-is, extended, or new records *that* something satisfies a piece of the Technical Interpretation;
it doesn't record *which* piece, or how the substitution actually reads once made. Without recording the bound
pseudocode itself, every later step — deriving a Then (§5), reconciling it (§7.1) — would have to re-derive the
same substitution from the classification and the Technical Interpretation each time, silently repeating
judgement work that only ever needed doing once. Recording it once, on the `SB-NNN` it belongs to, is also what
makes reconciliation a cheap checksum comparison (§7.1) instead of a semantic walk run from scratch on every
check.

**Why substitution isn't fixed at single-call granularity.** An earlier framing of gap analysis considered only
one call (`<--[...]`) at a time. That's too narrow: a single existing function is often broad enough to satisfy
an entire branch of a Technical Interpretation's pseudocode, or, occasionally, the whole thing — and forcing the
judgement down to one call at a time in those cases would produce a bound pseudocode that's technically
substituted but reads as a needlessly fragmented restatement of what one function already does in full. Letting
the unit of substitution be whatever piece — a call, a branch, or the whole body — a candidate's own pseudocode
or prose genuinely stands in for keeps the bound pseudocode as simple as the actual design allows.

**Why `reconciliation:` is checksums, not a checkbox.** A checkbox only ever records that a check passed once;
it can't say whether it's still true. Checksums make the record falsifiable: recompute them later, and a
mismatch against what's recorded means something the check depended on has changed, without needing to re-read
or re-reason about content that hasn't actually moved — the same shape of argument this repo's own `.index/`
files (documentation-standards.md §4) make for staying accurate without a human re-deriving them by hand.
