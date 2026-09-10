# Operation Template

## Context
* [Design Layout Standards §5.2](../standards/design-layout-standards.md) - the operation as a layout unit
* [Behaviours Template](BEHAVIOURS-TEMPLATE.md) - the condition space and behaviours this operation is defined over
* [SLIs Template](SLIS-TEMPLATE.md) - the service level indicators a Service's operations carry

Template for an operation's own document. It is `operations/{operation-slug}.md` while one document describes
the operation, and `operations/{operation-slug}/OPERATION.md` once a second document — `BEHAVIOURS.md` or
`SLIs.md` — earns it a directory.

An operation is a point on a design target's perimeter at which it can be invoked from outside itself. It is
**not** an endpoint: an endpoint is a protocol-bound exposure of an operation, one operation may be exposed by
several, and a Library's operations are exposed by none at all.

# Appendix

````
---
_claims:
  - _target: bnd/{design-slug}/op/{operation-slug}
    _sourcing: {kind: document, basis: "@{repo-slug}/docs/analysis/use-cases/UC-NNN-{slug}.md"}
    slug: {operation-slug}
    purpose: §1
  - _target: bnd/{design-slug}/op/{operation-slug}
    _sourcing: {kind: elicited, basis: "§2"}
    signature:
      name: {operationName}
      parameters:
        - {name: {argName}, position: 1, type: type/{type-slug}}
      returns: type/{type-slug}
      raises:
        - {type: type/{exception-slug}, condition: "{when it is raised}"}
  - _target: bnd/{design-slug}/op/{operation-slug}
    _sourcing: {kind: elicited, basis: "§3"}
    realizedBy: bnd/{design-slug}/bnd/{interface-boundary-slug}/fn/{function-slug}
  - _target: bnd/{design-slug}/op/{operation-slug}
    _sourcing: {kind: document, basis: "@{repo-slug}/docs/analysis/use-cases/UC-NNN-{slug}.md"}
    critical: true
  # The dimensions this operation must behave over. They define the condition space; the rank
  # that turns them into a tree is stated in BEHAVIOURS.md.
  - _target: bnd/{design-slug}/op/{operation-slug}/dim/{dimension-slug}
    _sourcing: {kind: document, basis: "@{repo-slug}/docs/analysis/use-cases/UC-NNN-{slug}.md"}
    slug: {dimension-slug}
    kind: {payload | parameter}
    rank: 1
    projection: {given | when}
    source: type/{type-slug}
    values:
      - slug: {value-slug}
        ordinal: 1
        predicate: {field: {field-name}, operator: lt, value: 1000}
        fixtures:
          - slug: {fixture-slug-a}
          - slug: {fixture-slug-b}
      - slug: {value-slug}
        ordinal: 2
        predicate: {field: {field-name}, operator: gte, value: 1000}
        fixtures:
          - slug: {fixture-slug-b}
---
# {Operation Name}

## Context
* [{the design entry document}](../{design-slug}.md)
* [Behaviours](BEHAVIOURS.md) - the condition space this operation is defined over

## 1 Purpose

{what invoking this operation is for}

## 2 Signature

`{operationName}({argName}: {type-slug}): {return-type-slug}`

**Raises**

| Exception | Condition |
| :--- | :--- |
| `{exception-slug}` | {when it is raised} |

## 3 Realization

{the function that implements this operation. Where the design target contains an interface-kind boundary, the
realizing function must be on it. The operation's signature and the realizing function's must conform.}

* `bnd/{interface-boundary-slug}/fn/{function-slug}`

## 4 Criticality

{whether this operation sits on a critical user journey — a kind of use case — which is what determines whether
it needs a service contract and therefore SLIs. Either it points at the use case that says so, or the architect
asserts it and the assertion is recorded as a key decision. Analysis does not block design; it justifies the
value of the behaviours the design specifies.}

* **Critical**: {yes | no}
* **Basis**: {the use case, or the design decision recording the architect's assertion}

## 5 Condition Dimensions

{the variations this operation must behave over. The operation owns these — they are what its behaviours are
derived against, combined with the realizing function's own description.}

**Assessed from** {how these were arrived at — one of:}

* {the analysis that derived them, as a link and a checksum; or}
* {the architect's own assertion of the variations in scope, with the decision recording it.}

{Recorded rather than implied: a list cannot distinguish the behaviour-affecting variations from the ones
somebody happened to write down, and only the first supports a coverage claim. Analysis should define them and
cannot block design, so where the feed-in does not, the architect does.}

| Rank | Dimension | Kind | Projection | Over |
| :--- | :--- | :--- | :--- | :--- |
| 1 | {dimension-slug} | payload | given | `type/{type-slug}`, field `{field-name}` |
| 2 | {dimension-slug} | parameter | when | the `{argName}` parameter |

{Rank belongs here because it is what nests the tree and therefore what every cell is addressed as. Given
dimensions rank above When dimensions where nothing else dictates the order — state constrains which invocations
are meaningful, never the reverse — and cross-cutting dimensions rank below both, so the functional projection is
the nominal subtree. A behaviour document is a view into the space and states no rank of its own: if two views
could, one cell would have two addresses.}

### 5.1 {dimension-slug}

{what varies along this axis, and why it affects the outcome}

| Ordinal | Value | Means | Exposed by |
| :--- | :--- | :--- | :--- |
| 1 | {value-slug} | {the predicate, in terms the model can check} | {fixture-a}, {fixture-b} |
| 2 | {value-slug} | {...} | {fixture-b}, {fixture-c} |

{Values must be exhaustive and mutually exclusive over the thing they partition: `< 1000` and `>= 1000`, never
`< 1000` and `> 1000`.}

{The fixture relationship is many-to-many, and the claim runs from the condition: one fixture exposes values
across several dimensions at once, and one value is exposed by several fixtures. The same fixture naturally
appears against more than one value — see FIXTURES-TEMPLATE.md.}

### 5.3 Excluded Cells

{present only where the architect excludes a combination explicitly. That is a judgement about the space rather
than about a behaviour, so it belongs here with the rest of the structure. A rule-derived prune needs no entry —
the rule is in the model and will prune the same branch again — but an exclusion with no recorded reason is
indistinguishable from a branch nobody got to.}

| Cell | Why this combination does not exist |
| :--- | :--- |
| {cell-id} | {the reason} |

## 6 Functions

{a temporary working list, present only early in the design: the functions required to support this operation,
{slug} and prose. Functions belong to the boundaries that structure them, so these move out to those boundaries'
own documents as those boundaries are drawn, and this section goes with them.}
````

# Rationale

Purpose, signature, realization and criticality are separate claims because they are sourced differently and
change on different schedules. A purpose typically comes from a use case and is owned elsewhere; a signature and
a realization are the architect's own elicited decisions; criticality may be either. Anchoring them separately
keeps a change to one from re-judging the rest, and keeps the sourcing honest — a review of this operation can
see which facts came from a document nobody here may change and which the architect asserted.

§5 is explicitly temporary rather than a permanent home for a function list. Recording functions against the
operation is how a design starts — it is the natural way to think before the boundaries are drawn — but a
function belongs to exactly one boundary, and leaving a second list here would give the design two places
stating the same thing, which the duplicate-claim advisory would then have to keep raising.
