# The Product/Service Model

## Context
* [Documentation Standards](documentation-standards.md) - the directory structure (§2) and directory-per-entity
  pattern (§2.1) this continuum is placed within
* [Platform](concepts/platform.md), [Product](concepts/product.md), [Product Offering](concepts/product-offering.md),
  [Service](concepts/service.md), [System](concepts/system.md), [SLO / SLI](concepts/slo.md) - one document per
  concept, each grown independently over time
* [Use Cases](../workflows/feature-workflow/use-cases.md) - the Use Case concept, defined in the Feature Workflow rather than here
* [Initial Feature Document](../workflows/feature-workflow/initial-feature-document.md) - the Feature concept, likewise
* [Glossary](../glossary.md) - the one-line index this document, and every concept it names, is reachable from

This document supersedes `notes/products-and-services.md` and the relevant part of `notes/sde-workflows.md`: it
keeps their underlying continuum but replaces their generic, source-derived framing with what actually applies to
Weaver Engineering's own docs structure and workflow. It documents the continuum itself — how the rungs relate —
not the concepts individually; each concept's own definition lives in its own document (linked above and from
the [Glossary](../glossary.md)) so it can grow independently without this document accumulating unrelated detail.

## 1 The Continuum

```
Platform (a Product)
     |
     v
Product
     |
     v
Use Case            <-- Feature groups these (workflows/feature-workflow/initial-feature-document.md)
     |
     v
Product Offering
     |
     v
Service
     |
     v
System
     |
     v
SLO / SLI
```

Each rung is a different question asked of the same thing: what capability are we building
([Platform](concepts/platform.md)/[Product](concepts/product.md)), what does a user actually need to do ([Use
Case](../workflows/feature-workflow/use-cases.md)), how do customers get to it ([Product
Offering](concepts/product-offering.md)), what executes it ([Service](concepts/service.md)), what does that run
on ([System](concepts/system.md)), and how reliable does it have to be ([SLO/SLI](concepts/slo.md)).

## 2 How The Rungs Connect

* A Platform is a specialization of Product ([Platform §1](concepts/platform.md)) — no separate rung of its own.
* A Product decomposes into one or more Services, never into further Products ([Product
  §2](concepts/product.md)).
* A Use Case's operations run against a Service's own interface, but a Use Case names no Service directly — see
  [Use Cases §1](../workflows/feature-workflow/use-cases.md).
* A Product Offering exposes one or more Services' endpoints as a channel a customer actually uses; a Service's
  endpoint may be reachable through more than one Offering ([Product Offering §3](concepts/product-offering.md)).
* A Service runs on a System (its deployment) and carries its own SLOs/SLIs, keyed to its own operational
  archetype ([Service §3](concepts/service.md), [SLO / SLI §2](concepts/slo.md)).

Feature sits beside Use Case, not on the chain itself — it's the planning-level grouping of use cases into
deliverable units, defined in [Initial Feature Document](../workflows/feature-workflow/initial-feature-document.md).

# Rationale

**Why Feature isn't a rung on the continuum.** The original source material this document supersedes
(`notes/products-and-services.md`) had no concept of Feature at all — it's specific to how Weaver Engineering
actually plans and delivers work, not to the market/architecture continuum the rest of this document describes.
Feature groups Use Cases for planning and delivery; it doesn't sit between any two of the continuum's own rungs,
so forcing it into the diagram in §1 would misrepresent what it actually is.

**Why each concept has its own document rather than being defined here.** An earlier version of this document
defined Platform, Product, Service, Product Offering, System, and SLO/SLI inline, section by section. That
conflated two different jobs: describing how the continuum's rungs relate to each other (this document's actual
job) and defining what each rung individually means — a job better done in a document scoped to that one concept,
since a concept like Service is expected to keep growing detail over time (its archetypes, its SLO rules, its
design-proposes/delivery-promotes lifecycle) independent of anything else in the continuum. Splitting them out
also gives the [Glossary](../glossary.md) something concrete to link each term to, rather than a shared document
where every entry points at the same page.
