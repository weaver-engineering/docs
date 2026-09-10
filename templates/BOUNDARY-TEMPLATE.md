# Boundary Template

## Context
* [Design Layout Standards §2, §5.1](../standards/design-layout-standards.md) - the containment rule, and what a boundary document holds
* [Design Layout Standards §4](../standards/design-layout-standards.md) - why this file is `{slug}.md` sometimes and `BOUNDARY.md` other times

Template for a boundary's own document. It is `{boundary-slug}.md` while one document describes the boundary,
and `BOUNDARY.md` once the boundary owns a directory — the rename is the whole mechanism of the split, and the
content is identical either way. The design root always owns a directory, so its boundary document is always
`BOUNDARY.md`.

A boundary holds **content but no requirements**: its functions, their signatures and descriptions, the
interfaces its consumers depend on, and the types it defines. What it must *do* follows from the required
behaviours of its consumers, and belongs to the design target containing it. A boundary document therefore never
carries operations, condition spaces or behaviours — unless it is the design root, whose operations live in
`operations/`.

Every contained boundary has a `kind`: `interface`, `business-domain`, `shared-logic`, or `dependency`. Only a
`dependency` boundary may hold `dependsOn`, and its functions are thin shims — one-for-one translations of an
operation of the boundary depended on.

# Appendix

````
---
_claims:
  - _target: bnd/{design-slug}/bnd/{boundary-slug}
    _sourcing: {kind: elicited, basis: "§1"}
    slug: {boundary-slug}
    name: {Boundary Name}
    purpose: §1
    kind: {interface | business-domain | shared-logic | dependency}
  - _target: bnd/{design-slug}/bnd/{boundary-slug}/iface/{interface-slug}
    _sourcing: {kind: elicited, basis: "§2"}
    slug: {interface-slug}
    purpose: §2
  - _target: bnd/{design-slug}/bnd/{boundary-slug}/fn/{function-slug}
    _sourcing: {kind: elicited, basis: "§3.1"}
    slug: {function-slug}
    purpose: §3.1
    visibility: {perimeter | private}
    interface: iface/{interface-slug}
    signature:
      name: {functionName}
      parameters:
        - {name: {argName}, position: 1, type: type/{type-slug}}
      returns: type/{type-slug}
      raises:
        - {type: type/{exception-slug}, condition: "{when it is raised}"}
    calls:
      - bnd/{design-slug}/bnd/{other-boundary-slug}/fn/{function-slug}
---
# {Boundary Name}

## Context
* [{the containing boundary or design entry document}](../{design-slug}.md)
* Design Layout Standards (@docs/standards/design-layout-standards.md) - the layout this document follows

## 1 Purpose

{what this boundary is responsible for, in a sentence or two}

## 2 Interfaces

{the groupings of this boundary's perimeter functions that its consumers depend on — including consumers inside
the same design target, since shared logic's own interface is exactly what its sibling domains depend on}

### 2.1 {interface-slug}

{what this interface is for}

## 3 Functions

{one subsection per function held inline. A function earns its own {function-slug}.md individually, without
obliging its siblings to move — so this section holds the inline ones and points at any that have moved out.
Most boundaries are a mix, and that is the normal state. See FUNCTION-TEMPLATE.md.}

**Moved out**

| Function | |
| :--- | :--- |
| [{function-slug}]({function-slug}.md) | {one line} |

### 3.1 {function-slug}

{one line on what this function does, then its description: prose, pseudocode, or a sequence diagram — whichever
the function actually needs, and more than one where it needs more than one}

`{functionName}({argName}: {type-slug}): {return-type-slug}`

**Raises** `{exception-slug}` — {the condition under which it is raised}

**Calls** {the functions this one invokes}

### 3.2 {function-slug}

{...}

## 4 Contained Boundaries

{present only once this boundary contains others — at which point this document is BOUNDARY.md and they are its
siblings in this directory}

* [{contained-boundary-slug}]({contained-boundary-slug}.md) — {kind}, {one line}

## 5 Dependencies

{present only on a dependency-kind boundary: what lies outside the perimeter, and the shim functions that reach
it}

* {depended-on boundary} — {what this design uses it for}

## 6 Data Model

{the types this boundary defines, while they fit here; when they no longer do, they move to DATA-MODEL.md and
this section becomes an overview and a link — see DATA-MODEL-TEMPLATE.md}
````

# Rationale

Functions get a subsection each rather than a table row each, because a section is the unit an anchor addresses
and a claim's extent is one act of attribution. A table of every function in one section means a claim about any
one function is re-judged whenever any other changes; a subsection per function means an edit to one stops
invalidating the rest. The table shape is correct only once the functions have moved to their own documents and
what remains really is a single act of pointing at them.

§4, §5 and §6 are shown but described as conditional because a boundary that contains nothing, depends on
nothing and defines no types should not carry three empty sections. An empty section is anchorable, and prose
that arrives in one later has no digest to go stale — so the section is created when there is something to put
in it.
