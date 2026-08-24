# Use Cases

## Context
* [Feature Workflow](feature-workflow.md) - the workflow step (`1 Analyse The Feature`) that produces use cases
* [Initial Feature Document](initial-feature-document.md) - the Feature a use case is grouped under
* [The Product/Service Model](../../standards/product-service-model.md) - where Use Case sits in the wider
  Platform/Product/Service continuum
* [Use Case Template](../../templates/USE-CASE-TEMPLATE.md) - the fill-in-the-blank shape this document describes

## 1 What A Use Case Is

A use case is a set of steps the actor and any supporting actors perform to achieve the actor's goal — the happy
path, with possible extensions branching off it (see the [Use Case
Template](../../templates/USE-CASE-TEMPLATE.md)'s Main Success Scenario and Extensions). A step is not always
something the actor does: it may equally be what the product does in response to the actor's own step — steps
alternate between actor action and system response as the scenario plays out, not a flat list of things the actor
alone does.

"Achieve a goal" is what makes a use case a real requirement rather than an implementation detail described from
the outside — a use case exists because an actor needs something to be true afterward, not because some endpoint
happens to be callable.

A use case is written independent of any one Service. Nothing about a use case's own definition — its actors, its
goal, its steps — commits to which Service (or Services) actually realize it; that binding is Design's job, made
while working out *how* to satisfy the use case, never Analysis's. A use case that ends up split across more than
one Service is still one use case: what varies is how many Services its operations resolve to, not how many use
cases were written.

## 2 Actors, Operations, And Logical Response

An actor is whoever (or whatever) invokes the use case's operations — an end-user, an internal developer of
another project, or another system entirely. A use case may also involve supporting actors: other systems or
roles the primary actor depends on to reach their goal, without being the one pursuing it themselves.

The use case's steps decompose into two things, not one: **operations** — the actor invoking one of a Service's
interface endpoints — and, for each operation, the **combined logical response** the Service is required to
produce in return. An operation names the crossing; the logical response is what must be true once the Service
has actually handled it — together they're what a step "does," whichever side of the boundary that step is
written from (§1). A single use case commonly performs more than one operation on the way to its actor's goal —
an elicitation dialogue is several round trips, not one — and each is written as its own numbered step (see the
[Use Case Template](../../templates/USE-CASE-TEMPLATE.md)'s Main Success Scenario).

## 3 Scope

A use case stays at the Product level: it is never filed under, or owned by, any one Service's own
`docs/services/{slug}/` — it belongs to the Feature that groups it (see [Initial Feature
Document](initial-feature-document.md)) and lives under `docs/analysis/use-cases/`. This is what keeps a use
case's requirement stable even if Design later decides to change which Service (or Services) satisfy it, or to
split one Service's responsibility into two: the requirement didn't move, only its realization did.

## 4 Required Behaviors

A use case's required behaviors come from combining each operation's logical response (§2) with the entry
conditions in force when that operation was invoked — that combination is the concrete, testable condition a
later Chunk's tests are built to satisfy. See [Required Behaviors](required-behaviors.md) for how this fits into
a Feature's own analysis output.

# Rationale

**Why a use case names no Service.** A use case that named its Service directly would stop being a statement of
requirement and start being a statement of design — indistinguishable, later, from a decision Design was supposed
to make. Keeping a use case Service-agnostic is what lets Design actually choose, and re-choose, without ever
having to edit the requirement itself to do it.
