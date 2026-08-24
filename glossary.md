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
| [Directory-Per-Entity Pattern](standards/documentation-standards.md#21-the-directory-per-entity-pattern) | A concept that grows multiple satellite artifacts gets its own directory with an UPPERCASE `{CONCEPT}.md` manifest. |
| [Feature](workflows/feature-workflow/initial-feature-document.md) | A collection of use cases to be implemented or supported, existing before design, Service decomposition, or route to market. |
| [Platform](standards/concepts/platform.md) | A Product whose customers are other projects' own SDEs rather than end-users (e.g. `the-loom`). |
| [Product](standards/concepts/product.md) | One Weaver Engineering project: one code repository plus one `<project>-docs` repository. |
| [Product Offering](standards/concepts/product-offering.md) | The channel — UI, CLI, API — through which a Service's endpoint is actually consumed; the route to market. |
| [Service](standards/concepts/service.md) | The functional execution boundary a Use Case's operations run against; owns its own interface, components, dependencies, and SLOs/SLIs. |
| [SLO / SLI](standards/concepts/slo.md) | A quantified reliability target for one Service, and what's measured to check it. Recorded per-Service. |
| [System](standards/concepts/system.md) | The compute, network, and datastore infrastructure a Service runs on. |
| [Use Case](workflows/feature-workflow/use-cases.md) | An actor operating the system, through a Service's own interface endpoint, to achieve a goal. |
