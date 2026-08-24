# SLO / SLI

## Context
* [The Product/Service Model](../product-service-model.md) - where SLO/SLI sits in the wider continuum
* [Service](service.md) §3 - the archetypes this document's applicability matrix is keyed to, and where SLOs are actually filed
* [Glossary](../../glossary.md) - one-line definition and pointer back to this document

## 1 Definition

A Service Level Objective (SLO) is a quantified reliability target for one Service; a Service Level Indicator
(SLI) is what's actually measured to check it. SLOs/SLIs are recorded under that Service's own
`docs/services/{slug}/`, not centrally — which SLI categories are even relevant depends on the Service's own
archetype (§2).

## 2 SLI Applicability Matrix

| SLI Category | Request-Driven | Pipeline | Storage |
| :--- | :---: | :---: | :---: |
| **Availability** | Primary | Secondary | Primary |
| **Latency** | Primary | Secondary | Primary |
| **Quality** | Primary | N/A | Secondary |
| **Throughput** | Secondary | Primary | Primary |
| **Freshness** | N/A | Primary | Secondary |
| **Correctness** | Secondary | Primary | Secondary |
| **Coverage** | N/A | Primary | N/A |
| **Durability** | N/A | N/A | Primary |

A latency SLO on a Pipeline Service, or a freshness SLO on a Request-Driven one, is very often the wrong metric —
this table is what a Service's own SLO section checks itself against before committing to a target.

# Rationale

**Why SLOs live per-Service rather than centrally.** The SLI Applicability Matrix (§2) is precisely why: which
categories even apply depends on a Service's own archetype, so a central SLO document would either have to
duplicate that same table's reasoning per Service anyway, or produce SLOs that don't actually fit the Service
they're attached to. Filing them under the owning Service's own `docs/services/{slug}/` keeps a Service's
interface, its components, and its reliability targets as one coherent, self-contained fact set.
