# Feature Template

## Context
* [Documentation Standards §2.1](../standards/documentation-standards.md) - the directory-per-entity pattern
  `docs/features/{slug}/FEATURE.md` follows
* [Initial Feature Document](../workflows/feature-workflow/initial-feature-document.md) - what a Feature is, and
  what this document must capture at minimum
* [Feature Workflow](../workflows/feature-workflow/feature-workflow.md) - the workflow this document is the
  `Start` entry requirement for

Template for a Feature's own manifest, `docs/features/{feature-slug}/FEATURE.md` — the directory-per-entity
pattern, since a Feature accumulates state as the workflow proceeds: its use cases as `Analyse Feature` finds
them, its Service Flows and design tasks as `Architect Feature`/`Design Service` complete them. The template
itself is in the Appendix below, since it's reference material to copy from, not indexed content in its own
right.

# Appendix

````
---
{whatever tracking frontmatter the owning project requires — issue references, status. The Feature Workflow is
independent of which project a Feature belongs to, so this document doesn't fix a shape for it beyond requiring
it be present.}
---
# {Feature Slug}

## Context
* {link to the Product this Feature belongs to}
* Initial Feature Document (@docs/workflows/feature-workflow/initial-feature-document.md) - the convention this document follows

## 1 What This Feature Is

{enough narrative to understand the general idea of what's required and why, sufficient for Analyse Feature to
begin — it does not need to already enumerate every use case, since analysis is exactly what discovers those}

## 2 Use Cases

{grows as Analyse Feature identifies them — one bullet per use case, added as each is written, never removed}

* [{use-case-slug}](../../analysis/use-cases/{use-case-slug}/USE-CASE.md)

## 3 Design

{grows as Architect Feature / Design Service proceed}

* **Service Flows:** [service-flows.md](../../design/{feature-slug}/service-flows.md) — omit this line entirely
  until `Architect Feature` has produced it
* **Design Tasks:**
  + [{design-task-ref}](../../design/{feature-slug}/{design-task-ref}/hld.md) — {Service slug} — {"complete" once
    Design Feature Instructions §9.2 reaches full completion for it, otherwise omit any further annotation}

# Rationale

{why this Feature exists, and why it's scoped the way §1 states — an earlier alternative scope considered and
discarded, if any}
````

# Rationale

Frontmatter is left entirely to the owning project rather than given a fixed shape here, for the same reason
[Initial Feature Document](../workflows/feature-workflow/initial-feature-document.md) already states: the Feature
Workflow itself is project-independent, and different projects track issues, status, and ownership differently.
Requiring a specific shape here would either be wrong for some project or force every project into a lowest
common denominator.

§2 and §3 are written as accumulating lists, not point-in-time snapshots, because that's literally what they
are — the same "grows incrementally, never computed in one pass" shape [Chunk Scope](../workflows/feature-workflow/chunk-scope.md)
already uses for the same reason: the information isn't missing at any point during the work, so recording it as
it becomes known is both simpler and removes a whole class of reconstruction bug.
