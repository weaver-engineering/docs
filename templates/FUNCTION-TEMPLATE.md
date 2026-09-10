# Function Template

## Context
* [Design Layout Standards §5.1](../standards/design-layout-standards.md) - when a boundary's functions move out of its own document
* [Boundary Template](BOUNDARY-TEMPLATE.md) - where a function lives before it earns its own document

Template for `{function-slug}.md`, a single function's own document. A function earns one when the boundary's
functions become too cumbersome to share `BOUNDARY.md`, or when one function alone is intricate enough to want
the room. Either way the boundary now has more than one document and occupies a directory.

A function belongs to exactly one boundary, which is why its address nests through that boundary rather than
through the design target governing it. Promotion of a containing boundary therefore never re-addresses it.

A function declares what it **calls**. What calls it is derived and never authored, and so is the call graph
assembled from those declarations — neither appears in this document.

# Appendix

````
---
_claims:
  - _target: bnd/{design-slug}/bnd/{boundary-slug}/fn/{function-slug}
    _sourcing: {kind: elicited, basis: "§1"}
    slug: {function-slug}
    purpose: §1
    visibility: {perimeter | private}
    interface: iface/{interface-slug}
  - _target: bnd/{design-slug}/bnd/{boundary-slug}/fn/{function-slug}
    _sourcing: {kind: elicited, basis: "§2"}
    signature:
      name: {functionName}
      parameters:
        - {name: {argName}, position: 1, type: type/{type-slug}}
      returns: type/{type-slug}
      raises:
        - {type: type/{exception-slug}, condition: "{when it is raised}"}
  - _target: bnd/{design-slug}/bnd/{boundary-slug}/fn/{function-slug}
    _sourcing: {kind: elicited, basis: "§3"}
    calls:
      - bnd/{design-slug}/bnd/{other-boundary-slug}/fn/{called-function-slug}
  - _target: bnd/{design-slug}/bnd/{boundary-slug}/fn/{function-slug}
    _sourcing: {kind: elicited, basis: "§4"}
    emits:
      - slug: {metric-slug}
        kind: {counter | gauge | histogram | timer}
        measures: "{what is being measured}"
        unit: {count | milliseconds | bytes | ratio | per-second | ...}
        labels: [{label-slug}]
---
# {Function Name}

## Context
* [{the boundary this function belongs to}](BOUNDARY.md)

## 1 Purpose

{one line: what this function does}

## 2 Signature

`{functionName}({argName}: {type-slug}): {return-type-slug}`

**Raises**

| Exception | Condition |
| :--- | :--- |
| `{exception-slug}` | {when it is raised} |

{`raises` is a contract, not a summary of the implementation. An exception a function propagates without
declaring it here is a failure mode nobody has designed a response for, and is detected by walking the call
graph.}

## 3 Description

{what the design actually says this function does, and what a behaviour is traced through. One or more of:
prose, for logic that is genuinely narrative; pseudocode, the normal form once the function participates in a
traced behaviour; a sequence diagram, where the interaction across boundaries is the point. A description that
cannot be traced is not a description for this purpose, whatever its literary merits.}

### 3.1 Calls

{the functions this one invokes, and under what circumstances}

* `{called-function-slug}` — {why}

## 4 Metrics

{present only where this function emits any}

| Metric | Kind | Measures | Unit |
| :--- | :--- | :--- | :--- |
| `{metric-slug}` | {counter} | {what is being measured} | {count} |

{Emission itself is recorded as an effect on a behaviour, not here: this section says what the function *can*
produce, and the behaviour says under which entry condition it actually does.}
````

# Rationale

Signature, description, calls and metrics are separate claims anchored to separate sections rather than one
claim covering the whole document. They are established by different prose and change independently — a
signature settles early and rarely moves, while a description is rewritten as the design evolves — so anchoring
them separately means editing the description stops invalidating the signature.

Metrics carry both `measures` and `unit` because they answer different questions and both are needed. A reader
has to know that a number counts failed records rather than failed batches, which no enumeration can carry; a
machine has to compare, convert and aggregate, which prose cannot support.
