# User Persona Template

## Context
* [User Personas](../workflows/feature-workflow/user-personas.md) - what a persona is, and why it's filed as a flat
  file, not directory-per-entity
* [Documentation Standards](../standards/documentation-standards.md) - the document shape (Context, numbered
  sections, Rationale) this template follows

Template for a single persona, filed as `docs/analysis/user-personas/{persona-slug}.md`. The template itself is in
the Appendix below, since it's reference material to copy from, not indexed content in its own right.

# Appendix

````
# {Persona Slug} — {Title}

## Context
* {links to the root index and any directly relevant docs, each with a one-line summary}

## 1 Goal

{what this persona is trying to achieve, in a sentence — the same "so that" test a use case's own Goal is held to
(Use Cases §1.1)}

## 2 Frustrations

{what already goes wrong, or costs effort, on the way to that goal today — one bullet per frustration, each stated
concretely enough that a capability can be checked against it: does this actually address the frustration, or just
sound related to the goal}

## 3 Use Cases

{grows as use cases naming this persona as their actor are written — one bullet per use case, added as each is
written, never removed}

* [{use-case-slug}](../use-cases/{use-case-slug}/USE-CASE.md)
````

# Rationale

§3 is an accumulating list, not a computed one, for the same reason [Feature
Template](FEATURE-TEMPLATE.md)'s own Use Cases and Design sections are: the information isn't missing at any point
during analysis, so recording it as it becomes known is simpler than reconstructing it later by searching every
use case for this persona's slug.
