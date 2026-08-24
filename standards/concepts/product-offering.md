# Product Offering

## Context
* [The Product/Service Model](../product-service-model.md) - where Product Offering sits in the wider continuum
* [Service](service.md) - what an Offering exposes
* [Documentation Standards §2](../documentation-standards.md) - `docs/offerings/{slug}/OFFERING.md`'s conditional location
* [Glossary](../../glossary.md) - one-line definition and pointer back to this document

## 1 Definition

A Product Offering is the channel — UI, CLI, API — through which a Service's endpoint is actually consumed. It's
the route to market: how a customer gets from wanting to achieve a Use Case's goal to actually invoking the
Service endpoint that realizes it.

## 2 Conditional On A Deployable Service

`docs/offerings/{slug}/OFFERING.md` is conditional on the Product having at least one deployable, consumable
Service to expose — there's nothing for a channel to carry before then. See [Documentation Standards
§2](../documentation-standards.md).

## 3 Many-To-Many With Service

A single Offering commonly exposes more than one Service's worth of capability, and a single Service's endpoint
may be reachable through more than one Offering (the same API also driving a CLI, say) — Offering and Service are
a many-to-many relationship, not a one-to-one wrapping.

# Rationale

**Why Product Offering is defined as a channel, not "commercial packaging" generally.** An earlier framing (in
`notes/products-and-services.md`, since superseded) described Offering as pricing tiers and feature sets —
accurate as far as it goes, but not concrete enough to define what `docs/offerings/{slug}/OFFERING.md` actually
has to contain. "The channel a Service's endpoint is consumed through" is testable: a reader can point at the
actual UI, CLI, or API surface an Offering names, rather than restate an abstract commercial description with
nothing to check it against.
