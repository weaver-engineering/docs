# Glossary

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
| [Benefit](workflows/feature-workflow/analysing-a-feature.md) | What a capability becomes only in relation to a persona's use case that actually exercises it — not a property of a Feature by itself. |
| [Capability](workflows/feature-workflow/analysing-a-feature.md) | A logical unit a Feature groups: something a customer can do through the product, independent of any use case. |
| [Category Directory](standards/design-layout-standards.md#3-category-directories) | A flat directory holding a category of satellite artefacts in a design (`operations/`, `NFRs/`, `fixtures/`), created when first populated — not a boundary, and not subject to the containment rule. |
| [Chaos Testing](workflows/feature-workflow/architect-deployment.md) | //TODO — what chaos testing a Service is required to pass. |
| [Chunk Sequence](workflows/feature-workflow/the-chunk-sequence.md) | //TODO — the mechanically derived delivery order across a design task's Chunks. |
| [Chunks](workflows/feature-workflow/specification-document.md) | //TODO — the incrementally deliverable specification documents a design task's Predicted Service Behaviours are broken into. |
| [Consumable Service](workflows/feature-workflow/deploy-offering.md) | //TODO — what makes a Service actually consumable through its Offering, as distinct from merely Functional. |
| [Continuous Delivery](workflows/feature-workflow/architect-deployment.md) | //TODO — Architect Deployment's own CD decisions. |
| [Continuous Integration](workflows/feature-workflow/architect-implementation.md) | //TODO — Architect Implementation's own CI decisions. |
| [Cross-Cutting Boundary](standards/design-layout-standards.md#58-nfrs) | A named association of a set of NFR rules with the functions they are imposed on; a design may declare several of one category, and each earns its own document. |
| [Deployed Feature](workflows/feature-workflow/deploy-offering.md) | //TODO — the deployed, running instance of a Feature's Product Offering(s). |
| [Design Directory](standards/design-layout-standards.md#1-the-design-directory) | The directory a design occupies, marked by a `DESIGN.yaml` declaring its namespace; its subdirectories are part of it, except any that is itself a design directory. |
| [Design Docs](workflows/feature-workflow/design-directory-and-hld.md) | A design task's own directory: its HLD, chunk scope, reconciliation record, and every proposal it's made. |
| [Design Target](standards/design-layout-standards.md#7-why-the-layout-is-drawn-this-way) | A boundary designed in its own right: it owns its requirements, is mockable, bounds a trace, bounds authority, and owns its NFR consideration. |
| [Dev Infra](workflows/feature-workflow/architect-implementation.md) | //TODO — a Service's own development infrastructure. |
| [Directory-Per-Entity Pattern](standards/documentation-standards.md#21-the-directory-per-entity-pattern) | A concept that grows multiple satellite artifacts gets its own directory with an UPPERCASE `{CONCEPT}.md` manifest. |
| [Feature](workflows/feature-workflow/initial-feature-document.md) | A logical grouping of capabilities a customer can perform through the product, existing before design, Service decomposition, or route to market. |
| [Functional Boundary](standards/concepts/service.md) | What a spec is actually written against, not a Feature or Product directly — Service (deployable) is the common case, Library (specifiable, not deployed) is parked. |
| [Functional Feature](workflows/feature-workflow/feature-testing.md) | //TODO — the Feature-level state once every in-scope Service has passed Feature testing. |
| [Functional Service](workflows/feature-workflow/deploy-service.md) | //TODO — what makes a deployed Service "functional." |
| [Integrations](workflows/feature-workflow/design-feature-instructions.md) | //TODO — one of Architect Service's own outputs, paired with Design. |
| [Observability](workflows/feature-workflow/architect-implementation.md) | //TODO — what observability Architect Implementation is required to provide for. |
| [Platform](standards/concepts/platform.md) | A Product whose customers are other projects' own SDEs rather than end-users (e.g. `the-loom`). |
| [Predicted Service Behaviour](workflows/feature-workflow/specific-behaviors.md) | What a Service's own designed components/functions actually predict will happen, read off its own bound pseudocode. Design's own claim. |
| [Product](standards/concepts/product.md) | One Weaver Engineering project: one code repository plus one `<project>-docs` repository. |
| [Product Offering](standards/concepts/product-offering.md) | The channel — UI, CLI, API — through which a Service's endpoint is actually consumed; the route to market. |
| [Promotion](standards/design-layout-standards.md#7-why-the-layout-is-drawn-this-way) | Turning a contained boundary into a design target of its own, in place: it changes the boundary's type, never its position, so no address changes and — given the right layout — no document moves. |
| [Required Product Behaviour](workflows/feature-workflow/required-behaviors.md) | A use case operation's cumulative Given/Required Effect, mechanically/LLM-derived from the use case and checksummed against it. |
| [Required Service Behaviour](workflows/feature-workflow/design-feature-instructions.md) | What a Service is required to do, derived from architecting the design — independent of, and reconciled against, its Predicted Service Behaviour. |
| [Service](standards/concepts/service.md) | The functional execution boundary a Use Case's operations run against; owns its own interface, components, dependencies, and SLOs/SLIs. |
| [Service Flows](workflows/feature-workflow/architect-solution.md) | The Service topology and data flow chosen, by architecting a solution, to satisfy a Feature's use cases. |
| [Service Interface](workflows/feature-workflow/design-feature-instructions.md) | The Service's own concrete interface specification, crystallized by Design before any gap analysis. |
| [SLO / SLI](standards/concepts/slo.md) | A quantified reliability target for one Service, and what's measured to check it. Recorded per-Service. |
| [System](standards/concepts/system.md) | The compute, network, and datastore infrastructure a Service runs on. |
| [System Requirements](workflows/feature-workflow/design-feature-instructions.md) | //TODO — one of Architect Service's own outputs, paired with Design. |
| [Test Infra](workflows/feature-workflow/architect-tests.md) | //TODO — a Service's own test infrastructure. |
| [Trivial Boundary](standards/design-layout-standards.md#23-the-trivial-boundary) | A boundary that comfortably stays a single document: one interface, at most 9 functions, and a pooled allowance of one new crossing type and one new exception per function. |
| [Use Case](workflows/feature-workflow/use-cases.md) | An actor's real goal, achieved through one or more operations, each deferring to a Feature's own capability or defined inline. |
| [User Persona](workflows/feature-workflow/user-personas.md) | A use case's human actor, formalized as a Role, Goals, Frustrations, and a Technical Proficiency. |
