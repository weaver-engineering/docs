# Service

## Context
* [The Product/Service Model](../product-service-model.md) - where Service sits in the wider continuum
* [Product](product.md) - what a Service belongs to
* [System](system.md) - the deployment layer a Service runs on
* [SLO / SLI](slo.md) - the reliability targets a Service carries
* [Documentation Standards §2](../documentation-standards.md) - `docs/services/{slug}/SERVICE.md`'s location and the design-proposes/delivery-promotes Rationale
* [Glossary](../../glossary.md) - one-line definition and pointer back to this document

## 1 Definition

A Service is the functional execution boundary a Use Case's operations actually run against — the primary domain
Design works in. Each Service owns, under its own `docs/services/{slug}/`:

* its own public **interface** — the reserved entry point every Service has, addressed as `{service-slug}.interface`;
* its **Internal Components** and **External Dependencies** — the pieces the interface is built from;
* its **SLOs/SLIs** ([SLO / SLI](slo.md)) — reliability targets specific to how this Service actually operates.

## 2 Design Proposes; Delivery Promotes

A Service's contents are never written directly by Design. A design task *proposes* a new or extended Service —
its interface, components, dependencies — inside its own design directory
(`docs/design/{feature-slug}/{design-task-ref}/`); the proposal becomes a real entry under `docs/services/{slug}/`
only once that design is actually delivered. See [Documentation Standards](../documentation-standards.md)'s own
Rationale for why `docs/design/` never holds standing product facts.

## 3 Service Archetypes

Per *The Art of SLOs*, a Service falls into one of three operational archetypes, which governs which SLI
categories actually matter for it (see [SLO / SLI §2](slo.md)):

| Archetype | What It Does | Examples |
| :--- | :--- | :--- |
| **Request-Driven** | Synchronously serves real-time requests from a client | HTTP/gRPC APIs, web frontends |
| **Pipeline** | Asynchronously processes, transforms, or aggregates streams or batches | ETL jobs, event processors |
| **Storage** | Persists and retrieves data with guarantees on safety and access speed | SQL/NoSQL databases, object stores |
