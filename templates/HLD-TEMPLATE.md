# HLD Template

## Context
* [Documentation Standards](../standards/documentation-standards.md) - the document shape (Context, numbered sections, Rationale/Appendix) this template follows
* [Design Directory And HLD](../workflows/feature-workflow/design-directory-and-hld.md) - what an HLD is, where it's filed, and what each of its required sections must contain

Template for a Feature's high-level design document, one file per Feature, filed as `{feature-slug}-hld.md` at
the root of `docs/design/{feature-slug}/` in the project's own docs repo. The template itself is in the Appendix
below, since it's reference material to copy from, not indexed content in its own right.

# Appendix

```
# {Feature Slug} — High-Level Design

## Context
* {link to the initial feature document this design realizes}
* {link to each use case this design realizes}
* Design Directory And HLD (@docs/workflows/feature-workflow/design-directory-and-hld.md) - the convention this document follows

## 1 Scope

{what this design covers, linking to every use case it realizes; what it explicitly excludes}

## 2 Solution Overview

{a concrete map: which components this design introduces or modifies, one line on each one's responsibility, and what each talks to — other components, external dependencies. Not prose, not a restatement of the specific behaviors.}

## 3 Key Decisions

{one subsection per Open Design Question pulled from every use case in scope, resolved or explicitly deferred, plus one subsection per component listed in Solution Overview justifying the interface decided for it}

### 3.1 {Open Design Question, quoted or paraphrased from UC-NNN §7}

{the decision, stated normatively — justification goes in # Rationale, not here}

### 3.2 {Component Name} Interface

{the interface decided for this component, stated normatively — justification goes in # Rationale}

## 4 Data Types

{the shapes of data this Feature persists (data at rest, e.g. what an External Dependency stores) and exchanges (data in flight, e.g. request/response payloads) — concrete enough for specific behaviors to instantiate}

## 5 Internal Components

* {name} — {link to its IC-NNN document} — **{as-is | extended | new}** — {one line: what this design needs from it, or decided for it if extended/new} {`IC-000` is always the system's own entry point — no separate marker needed, the number says it}

## 6 External Dependencies

* {name} — {link to its ED-NNN document, or "new — defined in this design" if none exists yet} — **{as-is | extended | new}** — {one line: what this design uses it for}

## 7 Specific Behaviors

* [SB-{NNN}](specific-behaviors/SB-{NNN}-{slug}.md) - realizes [UC-{NNN}]({link})

## 8 Technology Stack

{languages, frameworks, and libraries this design commits to}

# Rationale

{every candidate considered for each Key Decision in §3 and why it was discarded, not just the winner — see [Design Feature Instructions §4.1](../workflows/feature-workflow/design-feature-instructions.md)}
```

# Rationale

Data Types (§4 of the template), Internal Components (§5), and External Dependencies (§6) are kept as separate
sections rather than folded into Solution Overview, even though all of them describe "what the Feature is made
of," because they answer different reviewer questions: Solution Overview is the map, Data Types is what moves
around the map, Internal Components is what the design built and owns, External Dependencies is what it depends
on outside itself. Collapsing them would make the HLD harder to skim for the one thing a reviewer is actually
checking (e.g. "did this design account for every open question" versus "what does this design depend on").

Each entry in §5 and §6 is classified as-is, extended, or new — not just "new or extended" — because "as-is"
is a real, distinct state: an existing function this design relies on but doesn't change at all, as opposed to
one gaining a new call it doesn't currently make ("extended"). Collapsing the two would lose exactly the
distinction [Design Feature Instructions](../workflows/feature-workflow/design-feature-instructions.md)'s Gap
Analysis phase exists to draw — an as-is item needs no further design decision, an extended one does.

Solution Overview is deliberately a map, not a restatement of the specific behaviors — an agent reading it needs
"what talks to what," which a list of black-box Given/When/Then scenarios can't provide, by [Specific
Behaviors](../workflows/feature-workflow/specific-behaviors.md) §1's own design (a specific behavior is
intentionally silent on internal architecture).

Section 7, Specific Behaviors, is deliberately just a list of links rather than the behaviors themselves — the
HLD identifies which specific behaviors exist; their content lives in the `SB-NNN` files, per [Specific
Behaviors](../workflows/feature-workflow/specific-behaviors.md). Keeping them out of the HLD body avoids
bloating the one document every reviewer and agent starts from with content that already has its own home.
