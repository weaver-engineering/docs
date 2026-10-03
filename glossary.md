# Glossary

//TODO WVR-187 — out of date: the Design Feature Instructions and the Internal Component Template were removed, and the links to them taken out of this document; prose mentions of them may remain. Needs a thorough tidy-up.

## Context
* [The Product/Service Model](standards/product-service-model.md) - the continuum most entries below sit within
* [Documentation Standards §2.1](standards/documentation-standards.md) - the directory-per-entity pattern entry below is drawn from

One row per concept term used across this repo's docs: the term (linked to the document that defines it) and a
one-line definition. Any new concept introduced into this repo's docs gets a row here in the same PR that
introduces it (Documentation Standards §2) — this file is never left to catch up later. A term's own document is
where detail actually lives and is expected to grow; this table stays a one-line pointer to it, never a second
copy of its content.

## 1 Terms

| Term | Definition |
| :--- | :--- |
| [Aspect That Can Wait](standards/roadmaps.md#3-aspects-that-can-wait) | A known part of a product — a capability, an open decision, a needed design — that no slice yet depends on; recorded in the roadmap, not a slice, and either sure to exist or not yet sure. It may have parts, each an aspect in its own right, and may need other aspects, parts or slices. |
| [Benefit](workflows/feature-workflow/analysing-a-feature.md) | What a capability becomes only in relation to a persona's use case that actually exercises it — not a property of a Feature by itself. |
| [Aspects Gantt Chart](standards/roadmaps.md#5-the-aspects-gantt-chart) | A phase roadmap's generated chart of what its aspects that can wait need before work on them can start — aspects, their parts and slices, each leaf lasting its complexity in units; replaces the earlier dependency diagram. |
| [Capability](workflows/feature-workflow/analysing-a-feature.md) | A logical unit a Feature groups: something a customer can do through the product, independent of any use case. |
| [Category Directory](standards/design-layout-standards.md#3-category-directories) | A flat directory holding a category of satellite artefacts in a design (`operations/`, `NFRs/`, `fixtures/`), created when first populated — not a boundary, and not subject to the containment rule. |
| [Chaos Testing](workflows/feature-workflow/architect-deployment.md) | //TODO — what chaos testing a Service is required to pass. |
| [Chunk Sequence](workflows/feature-workflow/the-chunk-sequence.md) | //TODO — the mechanically derived delivery order across a design task's Chunks. |
| [Chunks](workflows/feature-workflow/specification-document.md) | //TODO — the incrementally deliverable specification documents a design task's Predicted Service Behaviours are broken into. |
| [Complexity](standards/roadmaps.md#3-aspects-that-can-wait) | A leaf aspect's gut-instinct score, a Fibonacci number from 1 to 13; anything more complex is broken into parts. |
| [Consumable Service](workflows/feature-workflow/deploy-offering.md) | //TODO — what makes a Service actually consumable through its Offering, as distinct from merely Functional. |
| [Continuous Delivery](workflows/feature-workflow/architect-deployment.md) | //TODO — Architect Deployment's own CD decisions. |
| [Continuous Integration](workflows/feature-workflow/architect-implementation.md) | //TODO — Architect Implementation's own CI decisions. |
| [Cross-Cutting Boundary](standards/design-layout-standards.md#58-nfrs) | A named association of a set of NFR rules with the functions they are imposed on; a design may declare several of one category, and each earns its own document. |
| [Deployed Feature](workflows/feature-workflow/deploy-offering.md) | //TODO — the deployed, running instance of a Feature's Product Offering(s). |
| [Design Directory](standards/design-layout-standards.md#1-the-design-directory) | The directory a design occupies, marked by a `DESIGN.yaml` declaring its namespace; its subdirectories are part of it, except any that is itself a design directory. |
| [Design Docs](workflows/feature-workflow/design-directory-and-hld.md) | A design task's own directory: its HLD, chunk scope, reconciliation record, and every proposal it's made. |
| [Design Target](standards/design-layout-standards.md#7-why-the-layout-is-drawn-this-way) | A boundary designed in its own right: it owns its requirements, is mockable, bounds a trace, bounds authority, and owns its NFR consideration. |
| [Dev Infra](workflows/feature-workflow/architect-implementation.md) | //TODO — a Service's own development infrastructure. |
| [Dimension](workflows/feature-workflow/operation-fixtures.md#2-three-categories-three-obligations) | An axis that varies what an operation does — payload, dependency, or parameter — every value of which must be exposed across its kept cells. |
| [Directory-Per-Entity Pattern](standards/documentation-standards.md#21-the-directory-per-entity-pattern) | A concept that grows multiple satellite artifacts gets its own directory with an UPPERCASE `{CONCEPT}.md` manifest. |
| [Feature](workflows/feature-workflow/initial-feature-document.md) | A logical grouping of capabilities a customer can perform through the product, existing before design, Service decomposition, or route to market. |
| [Functional Boundary](standards/concepts/service.md) | What a spec is actually written against, not a Feature or Product directly — Service (deployable) is the common case, Library (specifiable, not deployed) is parked. |
| [Functional Feature](workflows/feature-workflow/feature-testing.md) | //TODO — the Feature-level state once every in-scope Service has passed Feature testing. |
| [Functional Service](workflows/feature-workflow/deploy-service.md) | //TODO — what makes a deployed Service "functional." |
| [Invariant](workflows/feature-workflow/operation-fixtures.md#2-three-categories-three-obligations) | A condition that defines what an operation does without varying it — earns no cell, and must be witnessed exactly once. |
| [Observability](workflows/feature-workflow/architect-implementation.md) | //TODO — what observability Architect Implementation is required to provide for. |
| [Phase](standards/roadmaps.md#6-phases) | One named stretch of a product's work, with a roadmap of its own in a folder named for it; when it ends, the next begins. |
| [Platform](standards/concepts/platform.md) | A Product whose customers are other projects' own SDEs rather than end-users (e.g. `the-loom`). |
| [Predicted Service Behaviour](workflows/feature-workflow/specific-behaviors.md) | What a Service's own designed components/functions actually predict will happen, read off its own bound pseudocode. Design's own claim. |
| [Product](standards/concepts/product.md) | One Weaver Engineering project: one code repository plus one `<project>-docs` repository. |
| [Product Offering](standards/concepts/product-offering.md) | The channel — UI, CLI, API — through which a Service's endpoint is actually consumed; the route to market. |
| [Promotion](standards/design-layout-standards.md#7-why-the-layout-is-drawn-this-way) | Turning a contained boundary into a design target of its own, in place: it changes the boundary's type, never its position, so no address changes and — given the right layout — no document moves. |
| [Roadmap](standards/roadmaps.md) | A project's `ROADMAP/` directory: an entry document naming its phases, and in each phase's own roadmap its delivered slices, which are history; its planned slices, which are aspirations; and the aspects that can wait. |
| [Route To A Value](workflows/feature-workflow/operation-fixtures.md#2-three-categories-three-obligations) | A fixture-level property of *how* a dimension reaches one of its values, not a dimension itself — the legitimate reason one value has several fixtures. |
| [Service](standards/concepts/service.md) | The functional execution boundary a Use Case's operations run against; owns its own interface, components, dependencies, and SLOs/SLIs. |
| [Service Flows](workflows/feature-workflow/architect-solution.md) | The Service topology and data flow chosen, by architecting a solution, to satisfy a Feature's use cases. |
| [Slice](standards/roadmaps.md#2-slices) | A thin, end-to-end piece of a product, delivered by a ticket or a few; records what it required the docs to define, and what it delivers. |
| [SLO / SLI](standards/concepts/slo.md) | A quantified reliability target for one Service, and what's measured to check it. Recorded per-Service. |
| [Step Contract](workflows/feature-workflow/use-cases.md#21-step-contracts) | A use case operation's own perceived Boundary plus a pointer to its condition space (an operation document). Replaces Technical Interpretation. |
| [System](standards/concepts/system.md) | The compute, network, and datastore infrastructure a Service runs on. |
| [Test Infra](workflows/feature-workflow/architect-tests.md) | //TODO — a Service's own test infrastructure. |
| [Trivial Boundary](standards/design-layout-standards.md#23-the-trivial-boundary) | A boundary that comfortably stays a single document: one interface, at most 9 functions, and a pooled allowance of one new crossing type and one new exception per function. |
| [Use Case](workflows/feature-workflow/use-cases.md) | An actor's real goal, achieved through one or more operations, each deferring to a Feature's own capability or defined inline. |
| [User Persona](workflows/feature-workflow/user-personas.md) | A use case's human actor, formalized as a Role, Goals, Frustrations, and a Technical Proficiency. |
