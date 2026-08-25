# Service Template

## Context
* [Documentation Standards §2](../standards/documentation-standards.md) - `docs/services/{slug}/SERVICE.md`'s
  location and the design-proposes/delivery-promotes rule
* [Service](../standards/concepts/service.md) - what a Service is, its three archetypes, and what it owns
* [Design Directory And HLD §3, §4](../workflows/feature-workflow/design-directory-and-hld.md) - where a
  Service's own components, dependencies, and behaviours actually live and how they're addressed

Template for a Service's own manifest, `docs/services/{service-slug}/SERVICE.md` — the directory-per-entity
pattern, since a Service accumulates components, external dependencies, and behaviours over its own lifetime,
across however many design tasks touch it. The template itself is in the Appendix below, since it's reference
material to copy from, not indexed content in its own right.

# Appendix

````
---
archetype: {Request-Driven | Pipeline | Storage}
---
# {Service Slug}

## Context
* {link to the owning Product}
* Service (@docs/standards/concepts/service.md) - the convention this document follows

## 1 Purpose

{what this Service is responsible for, and what role it plays in the Product it belongs to}

## 2 Interface

* [interface](components/interface.md) — this Service's own reserved entry point (Design Directory And HLD §4.4)

## 3 Internal Components

{grows as design tasks against this Service deliver — one bullet per component, added as each is promoted from a
design task's own proposal (Design Directory And HLD §4.1)}

* [{component-slug}](components/{component-slug}.md)

## 4 External Dependencies

{same shape as §3}

* [{dependency-slug}](external-dependencies/{dependency-slug}.md)

## 5 Behaviours

{same shape — every Required/promoted behaviour this Service is now required to exhibit}

* [{operation-slug}](behaviors/{operation-slug}.md)

## 6 SLOs / SLIs

{this Service's own reliability targets, keyed to its archetype above — see SLO / SLI §2}
````

# Rationale

Archetype is recorded in frontmatter, not just narrative prose, because it's a structural fact other tooling
needs to read mechanically (which SLI categories apply, per `SLO / SLI §2`) — the same reasoning
`documentation-standards.md` §3 already gives for any manifest's machine-authored frontmatter.

§3-§5 are accumulating lists rather than a point-in-time snapshot, the same shape [Chunk
Scope](../workflows/feature-workflow/chunk-scope.md) already uses: a Service's own standing content only ever
grows by promotion from a delivered design task (`concepts/service.md` §2), never by direct edit, so the manifest
recording that growth as it happens — rather than being recomputed — matches how the content actually comes to
exist.
