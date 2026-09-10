# SLIs Template

## Context
* [Design Layout Standards §5.9](../standards/design-layout-standards.md) - which operations carry SLIs, and why they are Service-only
* [SLO / SLI](../standards/concepts/slo.md) - what an SLI is and how it relates to the SLO that monitors it
* [Function Template](FUNCTION-TEMPLATE.md) - the functions that emit the metrics an SLI combines
* https://github.com/OpenSLO/OpenSLO - the OpenSLO specification, canonical for the objects below

Template for `operations/{operation-slug}/SLIs.md`: one operation's service level indicators. Its presence is
what earns an operation its directory, alongside `OPERATION.md`.

**SLIs exist only for a Service.** A Library's functions emit metrics like any others — a library that cannot
report its own counts is unobservable inside whatever deploys it, and a host service cannot instrument its
internals from outside. What a Library cannot do is say whether the numbers are acceptable, because
acceptability is a statement about delivery and a Library delivers nothing on its own.

**Two things decide what is required, and they answer different questions.** The **archetype** decides which
delivery dimensions need an SLI. A **critical user journey** — a kind of use case — decides which operations need
a service contract, and therefore need SLIs at all: an operation on such a journey has to achieve a contract for
the journey to hold. Criticality is recorded on the operation itself
([Operation Template](OPERATION-TEMPLATE.md) §4).

Analysis does not block design. It justifies the value of the behaviours a design has already specified — so an
operation's criticality may equally be the architect's assertion, recorded as a decision, and the design
proceeds either way.

| Archetype | Delivery dimensions requiring an SLI |
| :--- | :--- |
| request-response | availability, latency, quality |
| batch | coverage, correctness, freshness, throughput |
| storage | durability, throughput, latency |

**An SLI is an OpenSLO object.** The model does not invent a notation for service level indicators; it requires
the open standard, pins the revision, and requires that the definition validates against it. A definition that
fails validation is a finding.

**The SLO stays outside the design.** The design owns the measurement — this is measurable, here is how — and
the Product owns the promise about acceptable values of it, because the same SLI can carry different SLOs for
different offerings.

# Appendix

````
---
_claims:
  - _target: bnd/{design-slug}/op/{operation-slug}/sli/{sli-slug}
    _sourcing: {kind: elicited, basis: "§1.1"}
    slug: {sli-slug}
    dimension: {availability | latency | quality | coverage | correctness | freshness | throughput | durability}
    specVersion: openslo/v1
---
# {Operation Name} SLIs

## Context
* [{the operation}](OPERATION.md)
* {the Product's own SLO catalog, where the objectives against these indicators live}

## 1 Indicators

{one subsection per delivery dimension the archetype requires. A dimension with no indicator is a gap the
maturity gate reports.}

### 1.1 {sli-slug}

**Dimension** latency

{what this measures about the operation, in a sentence — what a reader needs to know that the definition below
does not say}

```yaml
apiVersion: openslo/v1
kind: SLI
metadata:
  name: {sli-slug}
spec:
  description: {what is being measured}
  ratioMetric:
    counter: true
    good:
      metricSource:
        type: {source}
        spec:
          query: {the query over the metrics this design emits}
    total:
      metricSource:
        type: {source}
        spec:
          query: {the query over the metrics this design emits}
```

**Combines** {derived — the design's own metrics these queries resolve to. Not authored here: it is read off the
definition and resolved against the metrics the functions emit. An SLI whose queries reference a metric no
function emits is a finding, because it measures something the design does not produce.}

### 1.2 {sli-slug}

{...}

## 2 Dimensions Not Covered

{present only where a dimension the archetype requires has no indicator yet, so the gap is legible rather than
merely absent}

| Dimension | Why not yet |
| :--- | :--- |
| {dimension} | {what is missing — usually a metric no function emits yet} |
````

# Rationale

The definition is an OpenSLO object embedded verbatim rather than a set of fields this template invents. An SLI
expressed in a private format is a description of a measurement; one expressed in OpenSLO is a measurement
existing tooling can evaluate, and one that survives leaving this design. What the design adds around it is the
linkage the standard has no reason to carry: which delivery dimension it serves, and whether the metrics it
queries are ones this design actually emits.

`specVersion` is recorded per indicator rather than once per project because "valid OpenSLO" is not a question
with an answer unless it names a revision — the standard evolves, and a definition valid under one need not be
valid under the next. A design's indicators will normally share a version, and a mixed set is legitimate while a
migration is in progress.

`combines` is marked derived rather than given a place to fill in, because it is read off the queries in the
definition and resolved against the metrics the design's functions emit. Authoring it would create a second
statement of the same fact, which could then disagree with the definition it was supposed to describe.
