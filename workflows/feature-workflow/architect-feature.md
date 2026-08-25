# Architect Feature

## Context
* [Feature Workflow](feature-workflow.md) - the workflow step this document defines
* [Weaver Engineering Workflows §1, §3, §4](../weaver-workflows.md) - Architecture as a cross-cutting
  responsibility, the three kinds of behaviour, and the two reconciliations this step's output feeds
* [Required Behaviors](required-behaviors.md) - the Required Product Behaviours this step reads, Feature-wide
* [The Product/Service Model](../../standards/product-service-model.md), [Service §3](../../standards/concepts/service.md)
  - the Service archetypes this step chooses between
* [Design Feature Instructions](design-feature-instructions.md) - `Architect Services` and `Design Service`, the
  per-Service work this step's output feeds
* [Service Flows Template](../../templates/SERVICE-FLOWS-TEMPLATE.md) - the fill-in-the-blank shape this process
  populates

`Architect Feature` decides the Service topology and data flow that will satisfy a Feature's use cases. It's
Architecture's own responsibility paired with Analysis (Weaver Engineering Workflows §1) — not a Design activity,
and not required before Design can start (§4 below).

## 1 Entry Requirements

At least one use case in scope with a derived, checksummed set of Required Product Behaviours (`Analyse Feature`).
Every use case's own Technical Interpretation and Required Product Behaviours are read together, Feature-wide —
the same breadth Gap Analysis already reads a use case's Technical Interpretation at (`design-feature-instructions.md`
§3) — not one use case at a time, since a Service one use case needs may be exactly the Service another use case
in the same Feature also needs, and that's only visible reading them together.

## 2 What This Step Decides

For the Feature's use cases considered together:

* **Topology** — which Services are involved: existing Services this Feature reuses, and new ones it proposes.
  A Service earns its own place in the flow the same way an Internal Component earns its own document today
  (`design-directory-and-hld.md` §4.3) — used by more than one operation, or a genuine functional boundary, not
  just "a name for a step in the flow."
* **Archetype** — for each Service involved, which of the three archetypes it is (Request-Driven, Pipeline,
  Storage — `concepts/service.md` §3), since this governs which SLIs and which kind of design that Service's own
  `Design Service` step will actually produce.
* **Interface** — for each Service's own `.interface`, what it actually *is*, technologically (a React/TS SPA; a
  `pnpm` CLI tool vs. a bash script vs. a compiled binary; REST vs. RPC) — the second of the four interface layers
  (Weaver Engineering Workflows §5). Not yet the concrete specification (wireframes, exact CLI arguments, exact
  API methods) — that's `Design Service`'s own first step, Crystallize The Interface.
* **Supporting systems** — what each Service actually needs to run on (`docs/infrastructure/`), at the level of
  "a queue, a relational store, a scheduled job runner," not a deployment topology (that's `Architect
  Implementation`'s own concern once it exists).
* **Data & Service Flow** — how data actually moves between the chosen Services to turn a use case's own
  operations into a result: which Service each operation lands on first, what it passes downstream, and in what
  order. This is what a Service's own required behaviours (§3) get derived *from* — Design never invents a
  Service behaviour; Architecture derives it here from walking this same flow.

This can surface Services with no use-case-visible role at all — a purely internal participant in the chosen data
flow, invisible to any use case's own steps (the leaderboard example, Weaver Engineering Workflows §3). Naming
such a Service here, even though no use case ever touches it, is exactly this step's job: without it, nothing
would ever derive that Service's own required behaviours at all.

## 3 Recording Service Flows

Recorded once per Feature, Feature-wide, at `docs/design/{feature-slug}/service-flows.md` — sibling to, but above,
the per-design-task directories `docs/design/{feature-slug}/{design-task-ref}/` (Design Directory And HLD §1),
since Service Flows spans however many separate design tasks it takes to design every Service it names, the same
way the Feature's own use case Scope already spans however many design tasks it takes to design them
(`design-directory-and-hld.md` §2). Produced from the [Service Flows Template](../../templates/SERVICE-FLOWS-TEMPLATE.md).

Like an HLD's own Scope, `service-flows.md` accumulates: a later design task may extend it (a new use case in the
same Feature needing a Service the existing flow doesn't yet cover) rather than every design task needing its own
copy.

## 4 Not Formally Required

A Service can be designed without a `service-flows.md` naming it at all (Weaver Engineering Workflows §2) — the
architect designing that Service simply asserts "these are its required behaviours" without a recorded data-flow
justification. This doesn't block `Design Service`; it blocks Feature-level reconciliation (§5), which has
nothing to walk if no flow was ever recorded.

## 5 Exit Criteria, And What It Feeds

`service-flows.md` names every Service the current use cases in scope need, each with its own archetype,
interface kind+technology, supporting systems, and its place in the data flow; every operation named by an
in-scope Required Product Behaviour is covered by at least one Service in the flow, or explicitly deferred with a
reason.

Its output feeds two things directly:

* **`Architect Services`**, per Service named in the flow — derives that Service's own Required Service
  Behaviours from its slice of the flow (`design-feature-instructions.md`).
* **Feature-level reconciliation** (Weaver Engineering Workflows §4) — walks the flow, once every Service it
  names has its own Required Service Behaviours, to confirm the combination actually produces the Required
  Product Behaviours it was derived to satisfy. Recorded at `docs/design/{feature-slug}/feature-reconciliation.yaml`,
  Feature-wide and sibling to `service-flows.md` — distinct from any one design task's own `reconciliation.yaml`
  (`chunk-scope.md`), which only ever covers Service-level reconciliation for that one task's own Service.

# Rationale

**Why Service Flows is Feature-scoped, not design-task-scoped.** A design task is naturally scoped to one Service
at a time (`Design Service` loops per Service, Weaver Engineering Workflows §6) — but the flow connecting several
Services together is a fact about the Feature, true regardless of which task happens to be designing which
Service this week. Scoping it to a single task would mean either duplicating it into every task that touches any
Service in the flow, or picking one arbitrary task to "own" a fact that isn't really its own. Filing it at the
Feature's own design root, accumulated the same way the HLD's Scope already accumulates across tasks, avoids
both.

**Why Feature-level and Service-level reconciliation get separate artifacts.** Weaver Engineering Workflows §4
already establishes these as two distinct checks, precisely because Required and Predicted Service Behaviour need
to stay independent artifacts (a service's own concern) while the flow-to-product check (a Feature's own concern)
operates one level up, across every Service the flow names at once. A single shared artifact would force one of
the two checks to either read state that isn't really its own, or wait on a task that hasn't started yet.
