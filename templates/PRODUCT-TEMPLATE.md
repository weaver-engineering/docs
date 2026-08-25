# Product Template

## Context
* [Documentation Standards §2](../standards/documentation-standards.md) - `PRODUCT.md`'s location, and why
  "project" resolves to Product rather than Service in this workspace
* [Product](../standards/concepts/product.md), [Platform](../standards/concepts/platform.md) - what a Product is,
  and how a Platform is simply a Product whose customers are other projects' own SDEs

Template for a Product's own manifest, `PRODUCT.md` at the repo root of a `<project>-docs` repo, sibling to
`README.md` — every such repo *is* one Product (or Platform). The template itself is in the Appendix below, since
it's reference material to copy from, not indexed content in its own right.

# Appendix

````
---
kind: {Product | Platform}
---
# {Product Name}

## Context
* Product (@docs/standards/concepts/product.md) - the convention this document follows

## 1 Purpose

{what this Product is, who it's for — end-users if `kind: Product`, other projects' own SDEs if `kind: Platform`
— and what it does for them}

## 2 Features

{link to docs/features/ — not an enumerated list here, since Features come and go independently of this document;
point at the directory, not a copy of its contents}

## 3 Services

{link to docs/services/ — same reasoning}

## 4 Offerings

{link to docs/offerings/, once it exists — conditional on this Product having at least one deployable, consumable
Service (Documentation Standards §2)}
````

# Rationale

§2-§4 are deliberately links to their own directories rather than enumerated lists repeated here, for the same
reason [HLD Template](HLD-TEMPLATE.md) keeps its own Predicted Service Behaviours section a list of links rather
than restated content: a Product's own Features, Services, and Offerings already have their own homes with their
own accumulating detail — restating them here would just be a second place for the same information to drift out
of sync.
