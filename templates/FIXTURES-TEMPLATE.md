# Fixtures Template

## Context
* [Design Layout Standards §5.11](../standards/design-layout-standards.md) - where the fixture claim lives and where its content lives
* [Operation Template](OPERATION-TEMPLATE.md) - the conditions fixtures expose, and where the claim lives

Template for `fixtures/`: the concrete state that makes a condition value real enough to trace against and,
later, to test against. Fixtures can be anything — payload samples, dependency entity states, error responses,
sample stdout, stderr or logged output, CLI examples, filesystem layouts — and a fixture's extension follows its
content.

**The fixture claim and the fixture content live in different places, and the split matters.**

* The **claim** belongs with whatever defines the condition, which is the **operation**
  ([Operation Template](OPERATION-TEMPLATE.md) §5) — not `BEHAVIOURS.md`, which holds the behaviours associated
  with an aggregate condition rather than the conditions themselves. A fixture attaches to a condition **value**,
  so it is named once per value and every cell selecting that value can draw on it.

  **A cell's *possible* set is derived; its *chosen* set is recorded.** The possible set is the union of the
  fixtures exposing every value the cell selects. From it the cell chooses one fixture for the payload, one for
  the output, and one per dependency. A cell whose possible set is **empty** has no concrete state it could be
  traced against — the values it selects are exposed by nothing — which is a `no-suitable-fixtures` finding.

  **The relationship is many-to-many, and the claim runs from the condition.** One fixture exposes values across
  several dimensions at once — a single sample order payload exhibits a value of `order-value`, of
  `customer-state` and of `payment-method` — and one value is exposed by several fixtures. So each condition
  claims the set of fixtures exposing it, and the sets overlap: condition 1 claims fixtures **A, B and C** expose
  it, condition 2 claims **B, C and D** do. B and C are stated once as content and named by both. The overlap is
  the normal case, not redundancy — fixtures are reused precisely because they carry the same characteristics.
* The **content** lives here, as a file the design points at with a checksummed reference. A file carrying no
  claims is data with a schema of its own; nothing is written into it, and it is never parsed for design
  content. A change to it moves the checksum and re-judges what depends on it.

**Fixtures are not required until a call tree exists.** Their dominant kind stands in for dependency-state
dimensions, which do not exist until the design reveals which dependencies an operation actually reaches — and
without concrete state there is nothing to trace, so reconciliation is not meaningfully possible before them
either.

**A stub or mock standing in for a contained design target is a checkable claim.** There must be a behaviour of
the operation it stands for whose expected effects match what the fixture declares; where none does, the fixture
implies behaviour the mocked design does not produce, and that is a finding. Where it stands for an unmodelled
external dependency there is nothing to reconcile against, and it is simply authored.

# Appendix

Layout — grouped into subdirectories where that helps:

````
- fixtures
    - {fixture-slug}.json
    - {fixture-slug}.txt
    - {fixture-group-slug}
        - {fixture-slug}.json
        - {fixture-slug}.yaml
````

The claim, authored in `OPERATION.md` alongside the condition value it exposes:

````
_claims:
  - _target: bnd/{design-slug}/op/{operation-slug}/dim/{dimension-slug}
    _sourcing: {kind: elicited, basis: "§5.1"}
    values:
      - slug: {value-slug}
        ordinal: 2
        fixtures:
          - slug: {fixture-slug-a}
          - slug: {fixture-slug-b}
      - slug: {other-value-slug}
        ordinal: 3
        fixtures:
          - slug: {fixture-slug-b}
          - slug: {fixture-slug-c}
  - _target: bnd/{design-slug}/fx/{fixture-slug-b}
    _sourcing: {kind: elicited, basis: "§5.1"}
    slug: {fixture-slug-b}
    kind: {seed | stub | mock}
    standsFor: bnd/{other-design-slug}/op/{operation-slug}
    content: "fixtures/{fixture-slug-b}.json"
````

`{fixture-slug-b}` is named by two values and defined once. The fixture itself is addressed in its own right,
which is what lets several conditions name it without restating it.

| `kind` | Means |
| :--- | :--- |
| `seed` | state a dependency is preloaded with |
| `stub` | a canned response |
| `mock` | a stub plus an expectation |

`standsFor` is required for a `stub` or a `mock`: it names the operation — of a contained design target, or of a
depended-on boundary — whose result the fixture declares. A `seed` needs none, since it is state rather than a
response.

Where the content is small enough to read inline, it may be a literal in the claim instead of a file reference.
The file is for anything a reader would rather see on its own terms.

# Rationale

Fixtures attach to condition values rather than to cells because a dependency-state value appears in every cell
of the subtree beneath it, often dozens. Attaching the fixture to the cell would mean restating the same canned
response once per leaf, with nothing keeping the copies identical, and would make changing that state a bulk
edit across the space.

The content sits outside the claim-bearing documents because a fixture is data, with a schema its own tooling
cares about. Writing frontmatter into a JSON payload sample would corrupt the file in order to answer a question
nobody is asking of it — the design says what it needs to know about the fixture in the claim that points at it,
which is the same way any out-of-scope file participates in a design.

Grouping is by whatever helps a reader rather than by a prescribed axis. Fixtures are shared across cells by
construction, and often across operations in practice, so a scheme that filed them by the first thing that used
them would be wrong as soon as the second thing did.
