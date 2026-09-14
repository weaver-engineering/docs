# Required Behaviors

## Context
* [Feature Workflow](feature-workflow.md) - the `Analyse Feature` step this document is the output of
* [Analysing A Feature §4](analysing-a-feature.md) - the direct-from-Feature derivation route this document
  doesn't yet describe (see `//TODO` below)
* [Use Cases §2.1](use-cases.md) - Step Contracts: the Boundary/Given/Then a use case states directly on its own
  steps, the source this document folds
* [Use Cases §4](use-cases.md) - what a use case's operations turn into
* [Weaver Engineering Workflows §3](../weaver-workflows.md) - where Required Product Behaviour sits among the
  three kinds of behaviour, and the two reconciliations it feeds
* [Required Behavior Template](../../templates/REQUIRED-BEHAVIOR-TEMPLATE.md) - the fill-in-the-blank shape this
  document describes

## 1 What A Required Product Behaviour Is

A Required Product Behaviour is the Analysis-level statement of what one operation of a use case must actually
produce — concrete enough to say what's needed, entirely independent of how it ends up being delivered: no
Service, Internal Component, or External Dependency named. It's the thing a later Chunk's tests are ultimately
built to satisfy, once Design has bound it to something real (see [Weaver Engineering Workflows
§4](../weaver-workflows.md), Feature-level reconciliation).

## 2 Not Independently Authored — Folded From Step Contracts, And Checksummed

//TODO (WVR-180) — this section describes only the via-use-case derivation route. A capability's Required
Behavior can also be derived directly from its Feature-level definition, with no use case involved (see
[Analysing A Feature §4](analysing-a-feature.md)); reconciling that route's filing location and checksum source
into this document is still open.

A Required Product Behaviour is never written freehand. Each use case operation already carries its own Step
Contract ([Use Cases §2.1](use-cases.md)) — a Boundary, a Given, and a Then, stated directly on the step. A
Required Product Behaviour is a mechanical fold of that: its Given is the operation's own Step Contract Given, and
its Required Effect is the operation's own Step Contract Then, restated as an effect rather than a state. There is
no inference left to make about *what* is required — that was settled when the Step Contract was written; folding
only restates it in this document's own shape, indexed and filed where Feature-level reconciliation expects it.

**A Step Contract's fixtures are a Required Product Behaviour's fixtures — the same artefact, not a second copy.**
An operation's Step Contract already names, by reference, the fixtures that witness its Given and its Then (Use
Cases §2.1); the fold carries those references forward rather than re-deriving or re-authoring anything literal.
This is also what `M3`'s own fixture set is built from, once Design exists to build one: Design references a Step
Contract's fixture as an external fact — an address and a checksum, never parsed or copied — rather than owning a
second fixture that happens to agree with it. Where Design needs a fixture no Step Contract provides (an internal
state no use case has reason to know about), it defines and owns that one itself, distinctly from anything
Analysis stated.

The derivation records a checksum of the exact Step Contracts it read. This is falsifiable the same way every
other reconciliation in this process already is (`documentation-standards.md` §4's own Rationale makes the same
argument for `.index/`): recompute the checksum later, and a mismatch against what's recorded means the use case's
Step Contracts changed since this behaviour was folded, and the fold needs re-running — without anyone needing to
notice by hand that a step's Given or Then quietly shifted underneath an already-folded behaviour.

This also gives "is the use case detailed enough" a real, mechanical test instead of a judgment call. Attempting
the fold and seeing whether it actually succeeds — every operation step has a Boundary, a Given, a Then, and a
fixture behind each, with nothing missing to invent — *is* the check. A fold that has to invent missing detail to
complete itself is exactly the signal that the use case's Step Contracts need more, not a license to invent it
here; the fix is to add or correct the Step Contract, then re-fold.

## 3 The Cumulative Given

**Superseded by Step Contracts, and now a mechanical read rather than a fresh composition.** A use case's Main
Success Scenario is a sequence of steps; some of those steps are operations (Use Cases §2), each carrying its own
Step Contract (Use Cases §2.1). Consecutive Step Contracts already chain by construction — a step's own Given is
exactly what the step or steps before it left Then — so an operation's Required Product Behaviour never needs its
baseline composed from the use case's Preconditions plus every prior operation's Required Effect; it is simply
that operation's own Step Contract Given, already stated:

```
operation 1: Given = Step 1's own Given (= UC Preconditions, at the first operation)   / When / Required Effect (= Step 1's Then)
operation 2: Given = Step 2's own Given (= Step 1's Then)                              / When / Required Effect (= Step 2's Then)
operation 3: Given = Step 3's own Given (= Step 2's Then)                              / When / Required Effect (= Step 3's Then)
...
```

This is what makes Feature-level reconciliation (`weaver-workflows.md` §4) a real check rather than an assumption:
each operation's own required effect is only ever demanded relative to what the use case has already guaranteed
true by that point, so confirming the whole chain holds is confirming the use case's own Postconditions are
actually reached, step by step, not just that each operation looks reasonable in isolation. Because the chain is
now stated rather than composed, a use case whose Step Contracts don't actually chain — an operation's Given that
doesn't match what the preceding step's Then established — is directly checkable, not merely assumed consistent.

An Extension (Use Cases Template §6) supplies its own baseline the same way, inheriting from whichever base step
it branches off, not from the full main-scenario chain — an Extension is a different path through the use case,
not a continuation of the happy path past the point it diverges.

## 4 One File Per Operation, Behaviours Indexed By N

Filed at `docs/analysis/use-cases/{use-case-slug}/behaviors/{operation-slug}.md` — one file per operation, never
one file per behaviour. `{use-case-slug}.{operation-slug}-N` is a *reference* into that file (used the way
`§M.N` already addresses a section elsewhere in this repo's own convention), not a filename pattern: the file
holds every one of this operation's behaviours, indexed internally by `N`.

Within one file, `N` follows the same nested-decimal, heading-depth-tracks-nesting convention a Specific Behavior
document already uses (see `specific-behaviors.md` §4.1 for the full mechanics: entry-state variation and unhappy
paths both multiply one operation into several behaviours; a node with further permutations beneath it is a
parent — Given only, no Required Effect of its own yet; a leaf resolves to a real Required Effect). That
mechanical shape doesn't change here, only what a leaf's own outcome is called: **Required Effect**, not Then —
because it names no Service, where a Design-bound Then eventually will.

Every unhappy path a human can reasonably anticipate at this level belongs here too, including a broad, blanket
requirement with no specific failure mode yet named ("any error is surfaced gracefully, with enough detail to
identify its cause") — that broad statement is what later drives a Service's own graceful-failure behaviours once
Architecture picks real dependencies (`weaver-workflows.md` §3); a dependency-specific failure Design discovers
later that this use case gave no basis to anticipate is an Analysis miss to flag back, not new information Design
is entitled to add here itself (see `design-feature-instructions.md`'s Design→Analysis feedback marker).

## 5 Required Delivery Surface

Stated once, at the document level, alongside the operation's Realizes: which interface kind (UI/CLI/API; the
actor may be systematic) this operation must be reachable through — the first of the four interface layers
(`weaver-workflows.md` §5). Every behaviour in the file shares it; it doesn't vary per entry-state permutation.

# Rationale

**Why Required Product Behaviour is folded rather than authored, unlike a use case's own narrative.** A use
case's Goal/Preconditions/Main Success Scenario/Extensions, and the Step Contracts stated on them, are irreducibly
human judgment — nobody else can decide what an actor actually wants, or where an operation's state boundary
actually sits. Turning that already-stated Given/Then into this document's own filed, indexed shape is mechanical
restatement, not judgment. Treating it as a fold, checksummed against its source, is what makes it cheap to keep
current and gives "is the use case good enough" an actual test instead of a reviewer's impression.

**Why the cumulative Given used to be stated as a composed formula, and no longer is.** Before Step Contracts
existed, an operation's own entry conditions were agreed informally during Design (§5.1 of the old
`design-feature-instructions.md`), late enough that nothing forced them to actually account for what earlier
operations in the same use case had already guaranteed; composing the chain explicitly, in this document, was
what first made Feature-level reconciliation a mechanical walk rather than a judgment call. Step Contracts move
that same statement one level up, onto the use case's own steps, at the point the state is actually decided — so
this document now reads the chain rather than building it, and a use case whose steps don't actually chain is a
defect in the use case, not something this document has to reconcile around.

**A consequence for Design, recorded here rather than acted on.** Design's own substitution check
(`design-feature-instructions.md` §6, the `pseudocode-substitution-checker` skill, and `pseudocode-style.md`'s
Technical-Interpretation vocabulary) was built to bind and compare against Technical Interpretation, which this
change retires. Those documents now describe a step that no longer has an input. Fixing them is out of scope
here — a follow-on ticket should retire or rewrite Design's own substitution machinery to reconcile against
Required Product Behaviours (or Step Contracts directly) instead.

**Why one file per operation rather than one per behaviour.** A behaviour rarely stands alone — entry-state
permutations and unhappy paths of the same operation share almost everything (§4 above), and
`{use-case-slug}.{operation-slug}-N` already reads as a reference into a structured document the same way `§M.N`
does elsewhere in this repo. One file per behaviour would scatter that shared context across many near-duplicate
files for no benefit a reference scheme doesn't already provide.
