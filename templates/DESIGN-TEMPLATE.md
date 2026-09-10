# Design Template

## Context
* [Design Layout Standards §1](../standards/design-layout-standards.md) - the design directory, its marker, and the single entry document
* [Documentation Standards §3](../standards/documentation-standards.md) - the document shape every template here follows

Template for the two files that bring a design directory into existence: `DESIGN.yaml`, which marks the
directory and declares the namespace, and `{design-slug}.md`, the design's single entry document and index.
They are created together and neither is meaningful alone.

`DESIGN.yaml` makes no claims and is never assessed — it is read before parsing begins, to establish where and
what the design is. Nothing else in a design directory is a support file of that kind.

The entry document starts by carrying the boundary and its operations directly. Both move out as the design
grows ([Design Layout Standards §5.1, §5.2](../standards/design-layout-standards.md)), leaving the
index behind. Both states are shown below.

# Appendix

`DESIGN.yaml`:

````
# The namespace this design's addresses are rooted in. Every address in every claim in this
# directory resolves against it, which is why it cannot itself be a claim.
namespace: {design-slug}

# The checks this design is assessed against — either a pointer outward to the Product's or the
# organisation's own configuration, or the set stated inline. There is no default: a design
# directory that does not say what it is checked against cannot be assessed at all.
checks: {../../../CHECKS.yaml | inline}
````

`{design-slug}.md`, at the start — boundary and operations still inline:

````
---
_claims:
  - _target: bnd/{design-slug}
    _sourcing: {kind: elicited, basis: "§1"}
    slug: {design-slug}
    name: {Design Name}
    purpose: §1
  - _target: bnd/{design-slug}/op/{operation-slug}
    _sourcing: {kind: document, basis: "@{repo-slug}/docs/analysis/use-cases/UC-NNN-{slug}.md"}
    slug: {operation-slug}
    purpose: §2.1
---
# {Design Slug}

## Context
* {link to the Feature or Use Case this design serves}
* Design Layout Standards (@docs/standards/design-layout-standards.md) - the layout this design follows

## 1 Purpose

{what this boundary is responsible for, in a sentence or two — what it is for, not how it works}

## 2 Operations

{the points on this boundary's perimeter at which it can be invoked from outside itself}

### 2.1 {operation-slug}

{what invoking this operation is for}

### 2.2 {operation-slug}

{...}
````

`{design-slug}.md`, once the design has grown — the index:

````
---
_claims:
  - _target: bnd/{design-slug}
    _sourcing: {kind: elicited, basis: "§1"}
    slug: {design-slug}
    name: {Design Name}
    purpose: §1
---
# {Design Slug}

## Context
* {link to the Feature or Use Case this design serves}
* Design Layout Standards (@docs/standards/design-layout-standards.md) - the layout this design follows

## 1 Purpose

{what this boundary is responsible for}

## 2 Boundary

{one paragraph of overview}

* [Boundary](BOUNDARY.md) — this design's own functions and interfaces
* [{contained-boundary-slug}]({contained-boundary-slug}.md) — {one line}

## 3 Operations

| Operation | Purpose |
| :--- | :--- |
| [{operation-slug}](operations/{operation-slug}.md) | {one line} |

## 4 Design Documents

{added as each comes to exist — never listed before it does}

* [Manifest](MANIFEST.md) — what this design is built as and how it is consumed
* [Data Model](DATA-MODEL.md) — the types this boundary defines
* [System Interface](SYSTEM-INTERFACE.md) — how the service interacts with its host
* [Endpoints](ENDPOINTS.md) — how data flows in and out under BAU
* [NFRs](NFR/) — the cross-cutting rules incorporated and the functions they govern
* [Design Decisions](DESIGN-DECISIONS/) — open questions and the decisions that closed them
* [Fixtures](fixtures/) — the concrete state behaviours are traced against
````

# Rationale

The check configuration sits beside the namespace for the same reason, one level up: it determines which checks
run, and the checks are what give positions their meaning — so a claim declaring the configuration would be a
claim at a position the configuration has not yet licensed. Both are circular in the same way, and both are read
as configuration before parsing begins. A missing configuration is therefore not a finding but an unassessable
directory, the same category as a missing `DESIGN.yaml`.

The namespace is declared in `DESIGN.yaml` rather than claimed in the entry document because every claim's
address is resolved against it. A claim inside the file declaring it would have to be addressed within a
namespace that same file is still establishing, so keeping the marker outside the claim mechanism entirely is
what stops it from being a special case in the parser.

The entry document is shown in two states because a template that only showed the mature shape would invite
creating empty `## Operations` and `## Design Documents` sections on day one — and an empty section is a
section a claim could be anchored to, which is precisely the drift the anchoring rules exist to prevent. A
design starts with a purpose and some operations, and the index grows underneath it.
