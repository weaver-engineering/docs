# Architect Services / Design Service — Instructions

## Context
* [Feature Workflow](feature-workflow.md) - the workflow steps (`Architect Services`, `Design Service`) this
  process carries out
* [Architect Feature](architect-feature.md) - the Feature-wide Service Flows this process reads a slice of
* [Design Directory And HLD](design-directory-and-hld.md) - the HLD, Internal Component, and External Dependency
  shapes this process populates
* [Specific Behaviors](specific-behaviors.md) - the Predicted Service Behaviour shape and Given/When/Then/Call
  Tree format this process populates
* [Required Behavior Template](../../templates/REQUIRED-BEHAVIOR-TEMPLATE.md) - the shape both Required Product
  and Required Service Behaviour share
* [HLD Template](../../templates/HLD-TEMPLATE.md), [Internal Component Template](../../templates/INTERNAL-COMPONENT-TEMPLATE.md),
  [External Dependency Template](../../templates/EXTERNAL-DEPENDENCY-TEMPLATE.md), [Specific Behavior
  Template](../../templates/SPECIFIC-BEHAVIOR-TEMPLATE.md) - the fill-in-the-blank shapes this process fills in
* [Pseudocode Style](pseudocode-style.md) - the notation this process writes and compares
* [Chunk Scope](chunk-scope.md) - the per-design-task record §7 and §9.2 below write incrementally
* [Weaver Engineering Workflows §3, §4](../weaver-workflows.md) - the three kinds of behaviour and the two
  reconciliations this whole process exists to produce and check

These are the steps an agent follows to carry out `Architect Services` and `Design Service`
([Feature Workflow](feature-workflow.md)) for one design task — one Service, usually — and produce a complete
design task directory. The architect's judgement is required at specific, named points (§3 ideation, §4
Crystallize The Interface, §7's side-effect resolution, §9 final review, §10 Design→Analysis feedback) —
everywhere else the process is mechanical. It is also iterative, not a strict waterfall — see §11 — and
deliberately broken into small, independently-resumable units of work: §1 defines how to tell, from the current
state of a design task directory alone, exactly which unit comes next.

Every point where this process "presents" something to the architect assumes the content is already committed
and pushed to the design task directory's own pull request. Presenting something not yet pushed isn't presenting
it at all — it's narrating uncommitted work.

## 1 Determining The Next Unit Of Work

Before doing anything, check the design task directory's current state in this order. The first gap found is the
next unit of work; everything before it is already done.

1. Does `docs/design/{feature-slug}/service-flows.md` exist, naming this task's own Service and this task's own
   HLD Scope (`hld.md` §1)? If not, `Architect Feature` (a different task, possibly this one) is a prerequisite —
   confirm scope before anything else.
2. Does every operation this Service's slice of the flow names have a derived Required Service Behaviour file
   under `services/{service-slug}/behaviors/`? Any that don't need §2, `Architect Services`.
3. Has this Service's own `interface` been crystallized (Design Directory And HLD §4.4) — a concrete
   specification, not just the kind+technology `service-flows.md` already names? If not, §4 is next.
4. Does the HLD's Internal Components and External Dependencies list classify every function this Service's
   Required Service Behaviours collectively touch as as-is, extended, or new? If not, §5 is next.
5. For every item classified extended or new, and every Open Design Question carried by a use case in scope: is
   there a resolved Key Decision with a Rationale entry naming every alternative considered? Any unresolved need
   §6.1. Once every gap is resolved, has the merge pass (§6.2) run since the last one was added? If not, that's
   next.
6. For every Required Service Behaviour clean per step 5: does it carry a bound-pseudocode block (§6.3)? Any
   that don't need §6.3.
7. For every Required Service Behaviour: has its Predicted Service Behaviour been traced (§7), with no
   `//REVIEW` line remaining? First one still `//REVIEW`, or still `//TODO`, needs §7.
8. For every traced behaviour: does its call tree's every node appear in its parent's declared `calls:`? Any
   mismatch needs §8.
9. For every behaviour clean per §8: does `reconciliation.yaml`'s own checksums still match the design's current
   content? If empty, or a checksum no longer matches, §9.1 is next.
10. Project-wide (this Feature's every design task, and every other Feature's) — does every leaf behaviour have a
    `reviewed` entry (§9.2), with no `//REDESIGN_REQUIRED` flag standing? Needs §9.2 wherever it doesn't.
11. Once every Service `service-flows.md` names has a fully-reviewed set of Predicted Service Behaviours: has
    Feature-level reconciliation (`architect-feature.md` §5) been run against the current state of
    `feature-reconciliation.yaml`? If stale or absent, that's next — mechanically the same shape as step 9, one
    level up.

Steps 2–9 can be scoped to one Service, one operation, or the whole design task; told to "work on `payments`,"
an agent runs the same sequence restricted to that Service's own artifacts. Steps 10–11 can't be scoped that
way — their whole purpose is catching invalidation or non-coverage that reaches outside the task currently being
worked on — so they always run at their own stated scope.

## 2 Architect Services

Whole-Service, reading this Service's own slice of `service-flows.md` (the operations that land on it, what it
receives from upstream, what it must pass downstream) together with every Required Product Behaviour whose
operation names this Service. Derive this Service's own Required Service Behaviours — both the ones a use case's
operation touches directly, and any purely internal to this Service's own participation in the flow (Weaver
Engineering Workflows §3) — and write them, one file per operation, at
`docs/design/{feature-slug}/{design-task-ref}/services/{service-slug}/behaviors/{operation-slug}.md`, using the
[Required Behavior Template](../../templates/REQUIRED-BEHAVIOR-TEMPLATE.md), checksummed against the
`service-flows.md` slice they were derived from.

Design never invents a Service behaviour during binding (§6 onward) — a behaviour has to exist here first,
derived from the architected flow, before anything can bind it to a real function. Where tracing a bound
pseudocode later (§7) reveals an effect the flow doesn't actually require, or a required effect the flow can't
actually produce, that's a defect in this step, not a discovery Design gets to silently correct — return here and
revise, or raise it back to `Architect Feature` if the flow itself is wrong.

Exit: this Service has a checksummed Required Service Behaviour file for every operation its slice of the flow
names.

## 3 Gap Analysis

Whole-Service: read every Required Service Behaviour derived in §2 together, as one body of requirement, and ask
of each candidate Internal Component or External Dependency function: could this function's own pseudocode or
prose stand in for this piece without changing what it describes? The answer is always one of:

* **As-is** — yes, and the candidate needs no call it doesn't already make. Its `calls:`/pseudocode doesn't
  change; its usage lists (Design Directory And HLD §3.4, §4.5) still grow to record the new behaviour relying on
  it.
* **Extended** — yes, but only once the candidate gains a call it doesn't currently make.
* **New** — no existing function stands in for this piece at all.

This is judgement, not a literal diff: compare what the behaviour needs against each candidate's existing
`calls:` and Purpose, not against its exact wording. Only relevant functions get classified.

Where the gap is closed by extending an existing function, find every other behaviour now relying on pseudocode
that's about to change before making the change — its usage list (Design Directory And HLD §3.4, §4.5) names them
directly, or walk `called_from:` back to the owning `interface` function for a non-entry-point function. Each one
found this way has an existing `reconciliation.yaml` entry that becomes stale the moment the change is made — §1
step 9 picks this up mechanically via the checksum that no longer matches.

Exit: the HLD's Internal Components and External Dependencies sections list every relevant function, each
classified as-is, extended, or new.

## 4 Crystallize The Interface

The first concrete thing `Design Service` does for a Service whose `interface` hasn't already been crystallized
by an earlier design task against the same Service (§1 step 3 — most design tasks against an already-established
Service skip straight to §3). `service-flows.md` decided what the interface *is*, technologically (Weaver
Engineering Workflows §5, layer 2); this step decides exactly what it *looks like*: the actual CLI tool name,
arguments, and outputs; the actual UI wireframes; the actual API methods and their Data Types (Design Directory
And HLD §2, item 4).

Any outline guidance a use case's own supporting material carries (rough wireframes, "the actor wants a CLI tool
like X that does Y and outputs Z") informs this step — it never constrains it. Turning guidance into a binding,
concrete specification is this step's own job, recorded as this HLD's own Key Decision for the `interface`
component (Design Directory And HLD §2.1) the same way any other component's interface decision is.

Exit: `docs/design/{feature-slug}/{design-task-ref}/services/{service-slug}/components/interface.md` exists with
a concrete signature for every function this design task's own Required Service Behaviours need from it.

## 5 Ideation And Solution Shape

Every item classified extended or new in §3, and every Open Design Question carried by a use case in scope, is a
gap needing a decision. This phase runs in two passes.

### 5.1 Per-Gap Ideation

Chunkable one gap at a time. For each: ask "how might we close this gap" and generate candidate solutions —
genuinely more than one where more than one is plausible. Score each against the project's NFRs and the gap's own
requirements, then choose one. Record the choice as a Key Decision, and every discarded candidate in the HLD's
`# Rationale`, with why it was discounted.

Choosing an interface means choosing what data it takes and returns, not just its name and signature — recorded
in the HLD's own Data Types as part of the same Key Decision.

Exit, per gap: a resolved Key Decision with a Rationale entry naming every considered alternative, and, where the
interface introduces a new data shape, an entry for it in Data Types.

### 5.2 The Merge Pass

Whole-Service, once every individual gap has its own resolution: read every Key Decision from §5.1 together and
look for gaps that turned out to want the same underlying function. Where they are, consolidate into a single Key
Decision and a single component function, updating every affected Required Service Behaviour's expected call tree
to match, and removing the superseded candidates' own entries from the HLD's §5/§6.

Once settled, every component or dependency named in the HLD's §5/§6 needs its own standing proposal document —
create it at the proposal path (Design Directory And HLD §3.1/§4.1) if it doesn't already exist, with one
numbered section per function this design task currently needs, its purpose statement and signature transcribed
from the interface already decided (§5.1), not left as a placeholder. Link the document from the HLD's §5/§6
entry, replacing the bare name §3's Gap Analysis recorded there.

This closes Solution Shape. Commit and push, then stop — a natural point to let the architect look over the diff
before §6.3 builds bound pseudocode on top of these interfaces.

Exit: no two Key Decisions describe functions that should have been the same one, and every component or
dependency named in the HLD's §5/§6 has its own proposal document, numbered and linked.

## 6 Recording The Bound Pseudocode

Once every gap in scope is closed, each Required Service Behaviour has, for every piece of required effect it
states, either an as-is/extended function already classified (§3) or a newly-decided one (§5). Walk that
behaviour's own Required Effect and substitute each call, branch, or whole body with the function now decided to
satisfy it — binding each abstract target to its real address (`[address: name - args]`, Pseudocode Style §2).
Record the result in this design task's own `reconciliation.yaml`, keyed by the behaviour's address
(`{service-slug}.{operation-slug}-N`).

This is Design's own bound pseudocode, distinct from — and reconciled against — the Required Service Behaviour it
binds (Weaver Engineering Workflows §4, Service-level reconciliation). The Required Service Behaviour document
itself is never edited by this step: a change to it is a change to Architecture's own requirement (§2), not
something Design is permitted to make here.

Exit: every Required Service Behaviour in scope has a bound-pseudocode entry in `reconciliation.yaml`.

## 7 Deriving Predicted Service Behaviours

Per Required Service Behaviour (chunkable one at a time): trace its own Given (already fully stated — Architect
Services already agreed the entry-condition shape in §2, the same nested-numbering convention Required Behaviors
uses) through this task's own bound pseudocode (§6) — following each bound call into the Internal Component or
External Dependency's own pseudocode or prose it names — to derive the concrete Then and the call tree that
produced it, rather than authoring either freehand.

Tracing occasionally surfaces a Data Type whose Key Decision (§5.1) never actually specified a value or behaviour
for the case at hand. That's a real absence in it, not something validly inferable — name the gap and return to
§5.1 to make the missing decision.

Write the derived Then and call tree into `reconciliation.yaml`, flagged `//REVIEW` until the architect confirms
it — a fresh session can tell "derived, not yet confirmed" from document state alone. A behaviour predicted here
for the first time is new work this design task is introducing — record it as `new` in this task's own
[chunk scope](chunk-scope.md) at the same time. Commit and push, then present it for a quick sanity check: the
Required Service Behaviour restated, and the predicted result.

Revise and re-present, still flagged, if the architect disagrees. Once confirmed, remove the `//REVIEW` line in a
follow-up commit before moving to the next behaviour.

Exit: every Required Service Behaviour in scope has a fully-traced Predicted Service Behaviour, confirmed and
with no `//REVIEW` line remaining.

## 8 Call Tree Reconciliation

Chunkable per individual reconciliation issue, and idempotent. For every predicted behaviour, check that every
node in its call tree appears in the `calls:` declared by its parent. Where it doesn't:

* If it's a genuine design gap — the call was never actually decided — return to §5.1 to make that decision.
* If it's a simple documentation error — the `calls:` list is stale or mistyped — correct it directly.

Exit: every behaviour's call tree is fully backed by its nodes' declared `calls:`.

## 9 Design Review

### 9.1 Mechanical Reconciliation (Service-Level)

This is Weaver Engineering Workflows §4's second reconciliation: does this Service's own Predicted Service
Behaviour actually match its Required Service Behaviour? Recompute the checksum of the Required Service Behaviour
and of every function's pseudocode or prose the bound pseudocode names, and compare against what's recorded in
`reconciliation.yaml`. A match means the binding is still valid, cheaply. A mismatch — the Required Service
Behaviour changed (return to §2, or raise a Design→Analysis feedback per §10 if the change actually needs to flow
further upstream), or some function's pseudocode changed — means §6/§7's binding has to run again for this
behaviour.

A changed function's reach isn't confined to this one behaviour, or this Service. Walk backward from the changed
function, project-wide, to every behaviour — in any Service, any Feature — whose own recorded call tree contains
that function's address. Clear the `reviewed` entry for exactly those behaviours.

The check also runs the other way, and this half can't be shortcut by a checksum: walking the bound pseudocode
under this behaviour's own entry state, does it produce any External Dependency interaction the Required Service
Behaviour never anticipated? That's an unexpected external side effect, and it gets flagged regardless. Resolving
it is never mechanical — either the pseudocode is wrong and needs to conditionally exclude the effect, or the
Required Service Behaviour's own understanding was incomplete and needs correcting (§2, or a Design→Analysis
feedback per §10 if the gap traces all the way back to the use case itself).

Also walk the bound pseudocode for exceptions that are both unhandled and undeclared — every `RAISE` and every
uncaught propagation, checked against whether it's either caught locally or added to that function's own declared
contract (Pseudocode Style §4). Anything neither caught nor declared is a reconciliation failure.

Record a passing result in `reconciliation.yaml`: a checksum of the Required Service Behaviour and of every
function's pseudocode or prose the bound pseudocode names, as of the moment reconciliation passed.

Exit: `reconciliation.yaml`'s checksums match the design's current content.

### 9.2 Human Review

For each behaviour needing review (§1 step 10 — never reviewed, or a function change cleared its approval per
§9.1), regenerate its expected result by tracing its own entry state through the *current* design.

* **Regenerated result matches what's recorded** — refresh the checksums. If this behaviour already carried a
  genuine prior `reviewed_by`/`reviewed_at` that §9.1 just cleared, restore `reviewed` without further human
  touch. Otherwise (first time reaching review) it still needs a real first review.
* **Regenerated result doesn't match** — flag `//REDESIGN_REQUIRED`, recording the actual disconnect. Surface it
  to the architect — three-way, never automatic:
  * **Accept the new behaviour.** Update the recorded content in place, present it fresh as `//REVIEW`. Record
    `mutated` in this task's own chunk scope if the address belongs to a different, already-shipped task; nothing
    further if it's this task's own still-`new` entry.
  * **Reject it.** Push the design back to evolve the function further until it satisfies whatever prompted the
    change *and* this behaviour regenerates to match its original content again.
  * **Remove it.** The evolved design doesn't produce this behaviour under any entry state anymore. Delete the
    section and its `reconciliation.yaml` entry — needs an explicit, unambiguous yes/no confirmation before
    deleting anything, never folded into a routine "does this look right." Record `deleted` in chunk scope if the
    address belongs to a different, already-shipped task; otherwise just remove the `new` entry.

Once every Service `service-flows.md` names has every behaviour reviewed, Feature-level reconciliation
(`architect-feature.md` §5, `feature-reconciliation.yaml`) becomes checkable — the same mechanical
checksum-and-walk shape as §9.1, one level up, walking Service-to-Service through the flow instead of function-to-
function within one Service.

Exit: every behaviour in scope has a `reviewed` entry, with no `//REDESIGN_REQUIRED` flag standing.

## 10 Design→Analysis Feedback

Design never edits a use case, a Required Product Behaviour, or a Required Service Behaviour unilaterally, in
either direction. When architecting or binding genuinely discovers that a use case's own definition needs to
change — not a new failure mode this Service happens to encounter (that's an Analysis miss to flag as a gap in
§2's own Required Service Behaviour derivation, since the use case should already have stated the broad
requirement that drives it — Weaver Engineering Workflows §3), but a real structural fact about how the use case
itself has to work (an operation analysed as synchronous turning out to need to be asynchronous, requiring an
additional "wait for/receive result" step) — flag `//ANALYSIS_REVISION_REQUIRED` on the use case itself, naming
the specific change needed and why.

The architect resolves it, never automatically:

* **Accept.** Analysis revises the use case (and re-derives any Required Product Behaviour the change touches,
  Required Behaviors §2) in place. Design resumes against the revision.
* **Reject.** Design must find a solution that satisfies the use case as it already stands — no revision happens.

This generalizes the one narrow case the original version of this process allowed design work to reach back past
its own scope (an unexpected external side effect, §9.1) into the general mechanism this process actually needs.

## 11 The Feedback Loop

Design is iterative, not a strict pass through §2–§9. A problem found during §9.2 review, or later during `Chunk
The Design`, sends the design back to whichever phase actually owns the broken decision — usually §5.1 (a Key
Decision needs revisiting) or §7 (a behaviour was wrong) — which gets revised there. "Revised" means updated in
place: the HLD, and every other design document, always reflects the design's current, actual state. Everything
downstream is then re-validated by re-running §1's own check from that point forward.

That cascade isn't bounded by this design task, or this Feature — §9.2 runs project-wide precisely because a
function change can invalidate an already-approved behaviour from an unrelated, earlier design task, one that may
already be built and shipped through `Chunk The Design`. This process resolves the *design* side of that fully;
the deployed code a shipped Chunk already produced from the now-superseded behaviour is downstream of this
process entirely, and needs its own handling (chunk-scope's own `mutated` case).

# Rationale

**Why Architect Services (§2) is a separate phase from Gap Analysis (§3), not folded into it.** Gap Analysis asks
"does something already exist that satisfies this requirement" — a question that only makes sense once the
requirement itself is known. Under the old process, the requirement (an operation's entry conditions and Then)
was invented by Design as a side effect of Gap Analysis and binding; that's exactly the reversal this whole
rework exists to fix. Deriving Required Service Behaviours first, from the architected flow rather than from
whatever Design happens to notice while binding, is what makes "Design only ever binds a Service's behaviours to
its functions" (the architect's own framing) literally true rather than aspirational.

**Why Crystallize The Interface (§4) is its own explicit step, ahead of Gap Analysis.** A real gap in the WVR-95
design work: nothing in the original process ever said when a Service's own `interface` — its actual CLI
arguments, its actual wireframes, its actual API methods — gets decided, as distinct from deciding *that* a UI,
CLI, or API is needed (a use case's own concern) or *what kind* it is (Architecture's own concern,
`service-flows.md`). Without an explicit step, this either never happens deliberately (the interface accretes
implicitly across whatever the first few behaviours happen to need) or gets asserted without being checked
against what the use case's own guidance material actually wanted. Naming it as the first thing `Design Service`
does closes that gap the same way §4.2's own document-creation step (below) closed a similar one for standing
component documents.

**Why the two reconciliations (§9.1, and Feature-level in `architect-feature.md`) are kept as genuinely separate
checks rather than one combined pass.** Required Service Behaviour and Predicted Service Behaviour are
independent artifacts specifically so each reconciliation can be a cheap, targeted checksum comparison (Weaver
Engineering Workflows §4) — collapsing them into one check would mean either re-deriving both together every
time (defeating the point of checksumming either), or silently only ever checking one side.

**Why Design→Analysis feedback (§10) is a named marker with accept/reject resolution, not a silent edit in either
direction.** The original process allowed exactly one narrow case (an unexpected external side effect) to send
work back into Analysis, framed as an exception to an otherwise Design-contained loop. Working through the
Architecture/Analysis split surfaced that this is actually the general case for any structural fact Design learns
about a use case that Architecture had no way to know in advance (sync vs. async being the clearest example) —
narrowing it to "unexpected side effects only" would leave every other genuine structural discovery with no
sanctioned path back, inviting exactly the silent Design-edits-the-requirement failure mode this whole ticket
exists to close. Requiring an explicit accept/reject, mirroring `//REDESIGN_REQUIRED`'s own shape, keeps the
asymmetry intact: Design can request a change, but only Analysis can actually make one.

**Why a newly-discovered unhappy path is explicitly *not* routed through the same feedback marker.** It would be
easy to conflate "Design found something Analysis didn't anticipate" as one category needing one mechanism. A
dependency-specific failure mode is different in kind from a structural fact about the use case: the use case
should already have stated the broad, technology-independent requirement ("errors are surfaced gracefully")
that a specific failure mode is just an instance of satisfying (Weaver Engineering Workflows §3) — treating its
discovery as feedback-worthy would mean Analysis revising the use case every time Design picks a new dependency,
which is exactly the noise the broad-requirement pattern exists to avoid. The real bug, if the broad requirement
is genuinely missing, is caught by attempting the Required Product Behaviour derivation and finding it
under-specified (Required Behaviors §2) — a different check, run earlier, by Analysis itself.

**Why `reconciliation.yaml` is checksums, not a checkbox.** A checkbox only ever records that a check passed
once; it can't say whether it's still true. Checksums make the record falsifiable — the same argument
`documentation-standards.md` §4 already makes for `.index/`.

**Why §9.1 is a subset check, not an equality check.** A Required Service Behaviour and a Service's own designed
pseudocode are written for different purposes and can never read the same — one is deliberately solution-
independent, the other names real addresses. Requiring instead that everything the Required Service Behaviour
asks for has a corresponding path through the designed pseudocode is the actual property that matters.

**Why an unexpected side effect, and a structural use-case discovery, can't be resolved mechanically.** Design can
tell, mechanically, whether an effect is present or absent, or whether a use case's own operation ordering no
longer matches reality — it can't tell whether that's a bug in the design or a gap in the use case's own
understanding, because both look identical from the design's own side. Only a human looking at what the effect or
discovery actually is, in context, can tell which.
