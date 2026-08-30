# Use Case Template

## Context
* [Documentation Standards](../standards/documentation-standards.md) - the document shape (Context, numbered
  sections, Rationale/Appendix) this template follows, and §2.1's directory-per-entity pattern this document uses
* [Use Cases](../workflows/feature-workflow/use-cases.md) - what a use case is within the Feature Workflow, and
  why its boundary is the actor's real goal, not operation count
* [User Personas](../workflows/feature-workflow/user-personas.md) - what the `Actor` field below links to, when the
  actor is human
* [Analysing A Feature](../workflows/feature-workflow/analysing-a-feature.md) - how an operation relates to a
  Feature's own capability, deferred to or defined inline
* [Required Behaviors](../workflows/feature-workflow/required-behaviors.md) - what this document's Technical
  Interpretation and operations turn into, and the `behaviors/` subdirectory they're derived into
* [Pseudocode Style](../workflows/feature-workflow/pseudocode-style.md) - the notation the Technical
  Interpretation appendix below is written in

Template for a single use case, filed as `docs/analysis/use-cases/{use-case-slug}/USE-CASE.md` — the
directory-per-entity pattern, since a use case always grows a `behaviors/` subdirectory alongside it (one file
per operation, holding that operation's Required Product Behaviours). A use case is addressed by its own slug,
never a numeric id. The template itself is in the Appendix below, since it's reference material to copy from, not
indexed content in its own right.

# Appendix

```
# {Use Case Slug} — {Title}

**Actor:** {link to the persona doc under `docs/analysis/user-personas/`, or the systematic actor's name directly
if this use case has no persona (User Personas §2)}
**Scope:** {one or two lines on what this use case does *not* cover, with a
forward reference to §7 if there's a longer list}

## Context
* {links to the root index and any directly relevant docs, each with a one-line summary}

## 1 Goal

{what the actor is trying to achieve, in a sentence or two — the actual test
of this use case's own boundary (Use Cases §1.1), not how many operations or
Services it ends up taking to get there}

## 2 Trigger

{the event or condition that starts this use case}

## 3 Preconditions

{what must already be true for this use case to be invoked}

## 4 Main Success Scenario

{numbered steps. A step that relies on another use case's functionality
references it inline, by id, at that step, as an actual markdown link to
that use case's own directory — e.g. "...generates the section index
([find-a-path](../use-cases/find-a-path/USE-CASE.md))...". This is the only
place dependencies are declared: no separate "Depends On" summary field.
Once the word-indexer exists, a document's dependency set is mechanically
recoverable anyway, by scanning its own word index for use-case-slug links
outside `## Context` — so a bespoke field would just be a second place for
the same information to drift out of sync with the actual steps.}

## 5 Postconditions

{observable end state once the main scenario completes — what Feature-level
reconciliation ultimately checks the chained Required Product Behaviours
against, see Required Behaviors §3}

## 6 Extensions

{numbered-step branches, Cockburn-style: `<step><letter>` — e.g. `2a`, `4c` —
each naming the branching condition and what happens instead}

## 7 Open Design Questions (not resolved by this use case)

{things this use case deliberately leaves undecided, and why — scope
boundaries, not justification for what *is* decided; justification belongs
in a `# Rationale` section per the documentation standard, if one is needed}

# Appendix

## Technical Interpretation

{pseudocode for the steps and Extensions above, in the [Pseudocode Style](../workflows/feature-workflow/pseudocode-style.md), but independent of any solution — no
Internal Component, External Dependency, or Service named, no call trees. This is Analysis's own crystallization
of what this use case actually requires, written during `Analyse Feature`, not Design. It is immutable once
written, in the sense that matters here: it is never edited to reflect whatever solution gets chosen around it,
and it does not change just because that solution does. An edit to this section is an edit to the use case's own
requirement, not ordinary design work.

While writing this pseudocode, every point where the actor invokes the product is an operation (Use Cases §2) —
identify each one as it's written, noting whether it defers to an existing capability or needs its own inline spec
(Analysing A Feature §4). Once each operation is identified, Required Behaviors derives its Required Product
Behaviours into `behaviors/{operation-slug}.md`, checksummed against this Technical Interpretation and the rest of
this use case; link each operation to its own behaviour file here.}
```

# Rationale

The shape had only ever existed as one worked example (AgentPlugins' UC-001, "Discuss A Project Concept And
Document It") before this template formalized it — worth stating explicitly since a use case can be written for
any project, not just AgentPlugins, so the template belongs here rather than in a single project's docs repo.

Putting the template in `# Appendix` rather than a numbered body section is a direct use of the documentation
standard's own vocabulary: it's supplementary reference material to copy from, not indexed content in its own
right — placeholder text (`{...}`) throughout would make for a fairly useless search index anyway. This mirrors
how `magpieweaver-docs`' `ADR-TEMPLATE.md`/`SPEC-TEMPLATE.md` are treated, predating this indexing scheme.

Technical Interpretation lives in the use case's own `# Appendix`, written during Analysis, rather than in a
design directory. It's the pseudocode reflection of *this* use case specifically, tightly coupled to its own
steps and Extensions, and it needs to sit next to the narrative it reflects for Required Behaviors' own
derivation (and, later, Design's pseudocode-to-pseudocode review) to be a comparison against something concrete
rather than something recalled from memory. It stays unindexed and un-numbered because, like the rest of
`# Appendix`, it's supplementary to the use case's own actor-level narrative, not part of it — a reader wanting
just the "wh...s" (per Feature Workflow's `Analyse Feature` step) doesn't need it.

**Why Technical Interpretation moved to being written during Analysis, reversing an earlier version of this
template.** An earlier version of this document had Technical Interpretation "grown and maintained during the
Design step (not Analysis)" — the exact reversal this whole rework exists to fix. It was already, by its own
description, solution-independent (no Internal Component, External Dependency, or call tree named) — nothing
about its actual content required it to wait for Design; only its ownership was wrong. Moving it to Analysis
closes the reversal directly: Analysis crystallizes what's required (this pseudocode, and the Required Product
Behaviours derived from it), Design only ever works out how.

Immutability is stated directly, not left implicit in "grown and maintained during Analysis." An earlier draft
only said Technical Interpretation "does not change just because the chosen solution around it does," which
reads as a description of what tends to happen rather than a rule — leaving open whether a designer, mid-binding,
might reasonably tidy the Technical Interpretation to match whatever it just got bound to. Stating outright that
any edit to it is an edit to the use case closes that gap: Design's own record of "what was substituted for
what" belongs on its own reconciliation record, never folded back into the requirement it was checked against.
