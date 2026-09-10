# Data Model Template

## Context
* [Design Layout Standards §5.4](../standards/design-layout-standards.md) - when the data model moves out of a boundary's document, and how it recurses
* [Boundary Template](BOUNDARY-TEMPLATE.md) - where the data model lives before it earns its own document

Template for `DATA-MODEL.md`: the types a boundary defines. It starts as a `## Data Model` section of the
boundary's own document, and moves here when it becomes cumbersome — which, on a contained boundary, is what
earns that boundary its directory. A data model that grows sub models earns a `DATA-MODEL/` directory of its
own, with this document keeping a data-model-at-a-glance diagram and links, and each sub model taking the same
shape one level down.

**The obligation covers every shape that crosses a boundary** — in and out through the design's own perimeter
operations, in and out through its dependency boundaries, and across the perimeters of its contained
boundaries. Shapes used only inside a boundary by its private functions still have types; the design simply
makes no completeness claim about them.

A type is defined by exactly one boundary, and a reference resolves by walking outward from the referencing
boundary, taking the nearest definition — so a shape shared across several contained boundaries is defined once,
on the boundary containing them all. The **dictionary** is a design target's derived view over every type in its
own scope; it is not a document anyone writes.

# Appendix

````
---
_claims:
  - _target: bnd/{design-slug}/bnd/{boundary-slug}/type/{type-slug}
    _sourcing: {kind: elicited, basis: "§2.1"}
    slug: {type-slug}
    purpose: §2.1
    kind: {scalar | enum | structure | collection | external}
    fields:
      - name: {field-name}
        type: type/{type-slug}
        required: true
      - name: {field-name}
        type: type/{type-slug}
        required: false
        presenceDependsOn: {onField: {other-field-name}, value: {literal}}
  - _target: bnd/{design-slug}/bnd/{boundary-slug}/type/{type-slug}
    _sourcing: {kind: elicited, basis: "§2.2"}
    rules:
      - slug: {rule-slug}
        description: §2.2
        if:
          - {field: {field-name}, operator: eq, value: {literal}}
        then:
          - {field: {other-field-name}, operator: present, value: true}
        thenForbidden:
          - {field: {third-field-name}, operator: eq, value: {literal}}
---
# {Boundary Name} Data Model

## Context
* [{the boundary defining these types}](BOUNDARY.md)

## 1 At A Glance

```mermaid
classDiagram
    class {TypeName} {
        +{type-slug} {fieldName}
    }
```

{one paragraph orienting a reader in what these types are for and how they relate}

{Once sub models exist, this section keeps the overview diagram and links to them:}

* [{sub-model-slug}](DATA-MODEL/{sub-model-slug}.md) — {one line}

## 2 Types

### 2.1 {type-slug}

{what this type represents}

| Field | Type | Required | Present when |
| :--- | :--- | :--- | :--- |
| {field-name} | `{type-slug}` | yes | always |
| {field-name} | `{type-slug}` | no | `{other-field-name}` is `{literal}` |

{A field may exist only when another field holds a particular value — card details that exist only when the
payment method is a card. This makes a structure a tree rather than a flat record, and it is read directly:
when the payment method is something else, nothing beneath card details is a dimension at all.}

### 2.2 Validity Rules

{what makes an instance valid, stated so it can be solved rather than only read. Negating a type's structural
invariants together with its rules yields the combinations that are invalid, and those are exactly what prunes
an operation's condition space. A prose constraint cannot prune anything.}

| Rule | If | Then | Forbidden |
| :--- | :--- | :--- | :--- |
| {rule-slug} | {predicates that activate it, empty means always} | {what must also hold} | {what must not} |

{A field-local bound is the degenerate case: an empty `if`, with `then` asserting the bounds. There is
deliberately no separate per-field constraint notation.}

### 2.3 {type-slug}

{an `external` type — owned by a depended-on boundary and referenced rather than defined. Recorded so
signatures resolve and dimensions can be derived from it, without this design claiming authorship of something
it does not control.}
````

# Rationale

Presence dependency and validity rules are kept as separate things rather than folded into one notation,
because they produce different prunings and the condition space needs both. A validity rule says a combination
is impossible and removes a branch, narrowing the tree. A presence dependency says the question does not apply
and collapses a subtree into a leaf, shortening it. Recovering the second from the first would mean proving
irrelevance from a set of impossibility statements, which is harder to compute and easier to get subtly wrong
than reading a declared parent link.

Each type gets its own subsection so that adding a type, or changing one type's fields, does not re-judge every
claim about every other type in the document.
