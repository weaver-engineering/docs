# Platform

## Context
* [The Product/Service Model](../product-service-model.md) - where Platform sits in the wider continuum
* [Product](product.md) - what a Platform specializes
* [Glossary](../../glossary.md) - one-line definition and pointer back to this document

## 1 Definition

A Platform is a Product whose customers are other projects' own SDEs rather than end-users — shared
infrastructure, tooling, or APIs that let product engineers ship without reinventing it. `the-loom` is a Platform
in this sense.

## 2 No Separate Structural Treatment

Nothing structurally distinguishes a Platform from any other Product: it has its own `PRODUCT.md`, its own Use
Cases, its own Services. The only difference is who its Use Cases' actors are — internal SDEs on other projects,
rather than end-users. A Platform's `PRODUCT.md` states this directly; no separate directory or field exists to
mark it.
