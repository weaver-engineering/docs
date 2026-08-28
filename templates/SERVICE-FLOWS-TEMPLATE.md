# Service Flows Template

## Context
* [Documentation Standards](../standards/documentation-standards.md) - the document shape (Context, numbered
  sections, Rationale/Appendix) this template follows
* [Architect Feature](../workflows/feature-workflow/architect-feature.md) - the process that produces this
  document, and where it's filed
* [Service §3](../standards/concepts/service.md) - the three archetypes §1 below chooses between

Template for a Feature's Service Flows, one file per Feature, filed at `docs/design/{feature-slug}/service-flows.md`
— accumulated across however many design tasks it takes to design every Service it names, the same way an HLD's
own Scope accumulates. The template itself is in the Appendix below, since it's reference material to copy from,
not indexed content in its own right.

# Appendix

````
# {Feature Slug} — Service Flows

## Context
* {link to the initial feature document}
* {link to each use case this flow covers}
* Architect Feature (@docs/workflows/feature-workflow/architect-feature.md) - the process that produced this document

## 1 Services

{one bullet per Service this flow involves — existing, reused as-is or extended, or new}

* [{Service Name}]({link to docs/services/{slug}/SERVICE.md, or "new — proposed here" if none exists yet})
  * **Archetype:** {Request-Driven | Pipeline | Storage}
  * **Interface:** {UI | CLI | API} — {the technology decided for it, e.g. "React/TS SPA", "pnpm CLI tool", "REST API"}
  * **Supporting Systems:** {what it needs to run on, e.g. "a queue", "a relational store" — not a deployment topology}

## 2 Data & Service Flow

{a diagram — Mermaid `flowchart`, one node per Service plus the operations that trigger them — showing how data
actually moves between the Services above to turn each operation into a result, followed by one paragraph per
hop stating what's passed downstream and why. This is what a Service's own Required Service Behaviours (Architect
Services) are derived from — not restated prose, an actual traceable flow.}

```mermaid
flowchart LR
    {Operation} --> {Service A}
    {Service A} --> {Service B}
```

## 3 Operation Coverage

{every operation named by a Required Product Behaviour in scope, and which Service(s) in §1 realize it — the
exit criterion Architect Feature §5 checks}

| Operation | Covered By | Notes |
| :--- | :--- | :--- |
| `{use-case-slug}.{operation-slug}` | [{Service Name}](#1-services) | {anything worth noting — deferred, split across more than one Service, etc.} |

# Rationale

{why this topology — alternatives considered for Service boundaries, archetype choices, or the flow shape, and
why they were discounted; the same "record what wasn't chosen and why" convention a Key Decision already follows}
````

# Rationale

Services are listed flat, one bullet each, the same way `HLD-TEMPLATE.md` §5/§6 lists Internal Components and
External Dependencies flat rather than grouped by whichever use case happened to surface them first — the
Services in a flow are the organizing structure this document exists to establish, not an incidental grouping.

The Data & Service Flow diagram is required, not optional prose, for the same reason a specific behavior's call
tree is required data rather than a narrative summary (Specific Behaviors §2): "the data moves through these
Services in this order" is exactly the kind of fact reconciliation needs to walk mechanically, and prose alone
can't be walked.
