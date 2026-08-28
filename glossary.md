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
| [Chaos Testing](workflows/feature-workflow/architect-deployment.md) | //TODO — what chaos testing a Service is required to pass. |
| [Consumable Service](workflows/feature-workflow/deploy-offering.md) | //TODO — what makes a Service actually consumable through its Offering, as distinct from merely Functional. |
| [Continuous Deployment](workflows/feature-workflow/architect-deployment.md) | //TODO — Architect Deployment's own CD decisions. |
| [Continuous Integration](workflows/feature-workflow/architect-implementation.md) | //TODO — Architect Implementation's own CI decisions. |
| [Design Docs](workflows/feature-workflow/design-directory-and-hld.md) | A design task's own directory: its HLD, chunk scope, reconciliation record, and every proposal it's made. |
| [Dev Infra](workflows/feature-workflow/architect-implementation.md) | //TODO — a Service's own development infrastructure. |
| [Directory-Per-Entity Pattern](standards/documentation-standards.md#21-the-directory-per-entity-pattern) | A concept that grows multiple satellite artifacts gets its own directory with an UPPERCASE `{CONCEPT}.md` manifest. |
| [Feature](workflows/feature-workflow/initial-feature-document.md) | A collection of use cases to be implemented or supported, existing before design, Service decomposition, or route to market. |
| [Functional Service](workflows/feature-workflow/deploy-service.md) | //TODO — what makes a deployed Service "functional." |
| [Observability](workflows/feature-workflow/architect-implementation.md) | //TODO — what observability Architect Implementation is required to provide for. |
| [Platform](standards/concepts/platform.md) | A Product whose customers are other projects' own SDEs rather than end-users (e.g. `the-loom`). |
| [Predicted Service Behaviour](workflows/feature-workflow/specific-behaviors.md) | What a Service's own designed components/functions actually predict will happen, read off its own bound pseudocode. Design's own claim. |
| [Product](standards/concepts/product.md) | One Weaver Engineering project: one code repository plus one `<project>-docs` repository. |
| [Product Offering](standards/concepts/product-offering.md) | The channel — UI, CLI, API — through which a Service's endpoint is actually consumed; the route to market. |
| [Required Product Behaviour](workflows/feature-workflow/required-behaviors.md) | A use case operation's cumulative Given/Required Effect, mechanically/LLM-derived from the use case and checksummed against it. |
| [Required Service Behaviour](workflows/feature-workflow/design-feature-instructions.md) | What a Service is required to do, derived from architecting the design — independent of, and reconciled against, its Predicted Service Behaviour. |
| [Service](standards/concepts/service.md) | The functional execution boundary a Use Case's operations run against; owns its own interface, components, dependencies, and SLOs/SLIs. |
| [Service Flows](workflows/feature-workflow/architect-feature.md) | The Service topology and data flow chosen, by architecting a Feature, to satisfy its use cases. |
| [SLO / SLI](standards/concepts/slo.md) | A quantified reliability target for one Service, and what's measured to check it. Recorded per-Service. |
| [System](standards/concepts/system.md) | The compute, network, and datastore infrastructure a Service runs on. |
| [Test Infra](workflows/feature-workflow/architect-tests.md) | //TODO — a Service's own test infrastructure. |
| [Use Case](workflows/feature-workflow/use-cases.md) | An actor's real goal, achieved through one or more operations against one or more Services. |
