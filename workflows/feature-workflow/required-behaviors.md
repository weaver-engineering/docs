# Required Behaviors

## Context
* [Feature Workflow](feature-workflow.md) - the `Analyse Feature` step this document is the output of
* [Use Cases §4](use-cases.md) - what a use case's operations turn into
* [Weaver Engineering Workflows §3](../weaver-workflows.md) - where Required Product Behaviour sits among the
  three kinds of behaviour, and the two reconciliations it feeds
* [Pseudocode Style](pseudocode-style.md) - the notation a use case's own Technical Interpretation is written in,
  the source this derivation reads
* [Required Behavior Template](../../templates/REQUIRED-BEHAVIOR-TEMPLATE.md) - the fill-in-the-blank shape this
  document describes

## 1 What A Required Product Behaviour Is

A Required Product Behaviour is the Analysis-level statement of what one operation of a use case must actually
produce — concrete enough to say what's needed, entirely independent of how it ends up being delivered: no
Service, Internal Component, or External Dependency named. It's the thing a later Chunk's tests are ultimately
built to satisfy, once Design has bound it to something real (see [Weaver Engineering Workflows
§4](../weaver-workflows.md), Feature-level reconciliation).

## 2 Not Independently Authored — Derived, And Checksummed

A Required Product Behaviour is never written freehand. It's a mechanical/LLM derivation from the use case's own
Goal, Preconditions, Main Success Scenario, and Extensions — the same kind of move Design already makes turning a
Technical Interpretation into bound pseudocode (`design-feature-instructions.md` §6), just run one step
earlier, directly against the use case's own narrative instead of against a solution-independent pseudocode
rewrite of it.

The derivation records a checksum of the exact use case content it read. This is falsifiable the same way every
other reconciliation in this process already is (`documentation-standards.md` §4's own Rationale makes the same
argument for `.index/`): recompute the checksum later, and a mismatch against what's recorded means the use case
changed since this behaviour was derived, and the derivation needs re-running — without anyone needing to notice
by hand that a step's wording quietly shifted underneath an already-derived behaviour.

This also gives "is the use case detailed enough" a real, mechanical test instead of a judgment call. Attempting
the derivation and seeing whether it actually succeeds — produces literal fixtures and a real required effect
without inventing anything not already implied by the use case's own Preconditions/Extensions — *is* the check.
A derivation that has to invent missing detail to complete itself is exactly the signal that the use case needs
more, not a license to invent it here; the fix is to revise the use case, then re-derive.

## 3 The Cumulative Given

A use case's Main Success Scenario is a sequence of steps; some of those steps are operations (Use Cases §2).
Each operation's own Required Product Behaviour inherits, as its baseline Given, the use case's own entry
conditions (Preconditions) plus the Required Effect of every operation that precedes it in the scenario:

```
operation 1: Given(UC Preconditions)                          / When / Required Effect
operation 2: Given(UC Preconditions + operation-1 Required Effect)              / When / Required Effect
operation 3: Given(UC Preconditions + operation-1 + operation-2 Required Effect) / When / Required Effect
...
```

This is what makes Feature-level reconciliation (`weaver-workflows.md` §4) a real check rather than an assumption:
each operation's own required effect is only ever demanded relative to what the use case has already guaranteed
true by that point, so confirming the whole chain holds is confirming the use case's own Postconditions are
actually reached, step by step, not just that each operation looks reasonable in isolation.

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

**Why Required Product Behaviour is derived rather than authored, unlike a use case's own narrative.** A use
case's Goal/Preconditions/Main Success Scenario/Extensions are irreducibly human judgment — nobody else can
decide what an actor actually wants. Turning that narrative into concrete, literal fixtures and effects is
mechanical translation, not judgment, the same distinction `design-feature-instructions.md` already draws between
authoring a Key Decision (judgment) and substituting an already-decided function into bound pseudocode
(mechanical). Treating it as a derivation, checksummed against its source, is what makes it cheap to keep current
and gives "is the use case good enough" an actual test instead of a reviewer's impression.

**Why the cumulative Given is stated as an explicit formula rather than left implicit in "operations happen in
sequence."** Before this, an operation's own entry conditions were agreed informally during Design (§5.1 of the
old `design-feature-instructions.md`), late enough that nothing forced them to actually account for what earlier
operations in the same use case had already guaranteed. Stating the chain explicitly, at Analysis time, is what
lets Feature-level reconciliation be a mechanical walk instead of a judgment call about whether a Service's
required behaviours "seem to" add up to the use case's goal.

**Why one file per operation rather than one per behaviour.** A behaviour rarely stands alone — entry-state
permutations and unhappy paths of the same operation share almost everything (§4 above), and
`{use-case-slug}.{operation-slug}-N` already reads as a reference into a structured document the same way `§M.N`
does elsewhere in this repo. One file per behaviour would scatter that shared context across many near-duplicate
files for no benefit a reference scheme doesn't already provide.
