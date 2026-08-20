# HLD Template

## Context
* [Documentation Standards](../standards/documentation-standards.md) - the document shape (Context, numbered sections, Rationale/Appendix) this template follows
* [Design Directory And HLD](../workflows/feature-workflow/design-directory-and-hld.md) - what an HLD is, where it's filed, and what each of its required sections must contain

Template for a Feature's high-level design document, one file per Feature, filed as `{feature-slug}-hld.md` at
the root of `docs/design/{feature-slug}/` in the project's own docs repo. The template itself is in the Appendix
below, since it's reference material to copy from, not indexed content in its own right.

# Appendix

````
# {Feature Slug} — High-Level Design

## Context
* {link to the initial feature document this design realizes}
* {link to each use case this design realizes}
* Design Directory And HLD (@docs/workflows/feature-workflow/design-directory-and-hld.md) - the convention this document follows

## 1 Scope

* [Feature {slug}]({link to the initial feature document}) — {one-line summary of what this Feature explicitly excludes, stated once here, not per Design Task}
  - Design Task: {ANALYSIS-NNN or ticket ref} — {delivers [{design-task-ref}-chunk-scope.yaml](./{design-task-ref}-chunk-scope.yaml) once it exists; omit this link entirely while the task is still in progress}
    + [UC-{NNN}]({relative link})
    + [UC-{NNN}]({relative link})

{repeat the "Design Task" bullet, nested under the same Feature bullet, once per separate design ticket that has
worked on this Feature — see Design Directory And HLD §2 and Chunk Scope for the full convention. A use case
appears under exactly one Design Task, ever.}

## 2 Solution Overview

{a concrete map: which components this design introduces or modifies, one line on each one's responsibility, and what each talks to — other components, external dependencies. Not prose, not a restatement of the specific behaviors.}

## 3 Key Decisions

{one subsection per Open Design Question pulled from every use case in scope, resolved or explicitly deferred, plus one subsection per component listed in Solution Overview justifying the interface decided for it}

### 3.1 {Open Design Question, quoted or paraphrased from UC-NNN §7}

{the decision, stated normatively — justification goes in # Rationale, not here}

### 3.2 {Component Name} Interface

{the interface decided for this component, stated normatively — justification goes in # Rationale}

## 4 Data Types

{the shapes of data this Feature persists (data at rest, e.g. what an External Dependency stores) and exchanges
(data in flight, e.g. request/response payloads) — concrete enough for specific behaviors to instantiate. One
subsection per type, `### 4.M {TypeName}`, `TypeName` in PascalCase. Every function signature elsewhere in this
design (Internal Component Template, External Dependency Template) that takes or returns a non-primitive value
cites this exact name — never an informal, lowercase alias invented at the call site. A structured value nested
inside another type's own field is never inlined anonymously; it gets its own named entry here instead,
referenced by name. Primitives (`path`, `string`, `bool`, `int`, `float`, `void`, a literal string-enum like
`"list" | "details"`) are the only things that stay unnamed.}

### 4.1 {TypeName}

{one line: what produces this value, and which function(s) consume it}

```
{TypeName} = {
  {field}: {Type},
  {field}: {Type}
}
```

{always one field per line, even for a single-field type — never collapsed onto one line just because it fits.
An array of a named type is `[{TypeName}]`; an array of a primitive is `[{primitive}]`. A field whose own value
is itself structured cites another `### 4.N` entry by name, never an inline anonymous object.}

### 4.2 {Next Type Name}

{...}

## 5 Internal Components

{one top-level bullet per component — never grouped or nested under whichever use case(s) introduced it. An
Internal Component document can expose more than one function/operation (the same `§M.N` shape an External
Dependency's own shim uses); nest each one this design actually touches as a sub-bullet beneath its component,
not as a peer entry in a flat list of functions.}

* {Component Name} — {link to its IC-NNN document, or "new — defined in this design" if none exists yet}
  * {function/operation name} — **{as-is | extended | new}** — {one line: what this design needs from it, or decided for it if extended/new}
  * {another function/operation this design touches on the same component, if any}

{`IC-000` is always the system's own entry point — no separate marker needed, the number says it.}

## 6 External Dependencies

{same shape as §5 — one top-level bullet per dependency, its own operations nested beneath it, never grouped by
use case}

* {Dependency Name} — {link to its ED-NNN document, or "new — defined in this design" if none exists yet}
  * {operation name, `§M.N`} — **{as-is | extended | new}** — {one line: what this design uses it for}

## 7 Specific Behaviors

* [SB-{NNN}](specific-behaviors/SB-{NNN}-{slug}.md) - realizes [UC-{NNN}]({link})

## 8 Technology Stack

{languages, frameworks, and libraries this design commits to}

# Rationale

{every candidate considered for each Key Decision in §3 and why it was discarded, not just the winner — see [Design Feature Instructions §4.1](../workflows/feature-workflow/design-feature-instructions.md)}
````

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

**Why §5 and §6 nest functions/operations under their component, not under whichever use case introduced them.**
An earlier version of this template used one flat bullet per function. In practice, a first real design pass
(dogfooding this process against WVR-95) grouped those bullets by whichever use case had introduced each
candidate, since that's the order Gap Analysis naturally discovers them in — producing a list organized by
requirement rather than by the components §5/§6 are actually meant to inventory. The real structure was already
sitting one level up: an Internal Component (or External Dependency) document can expose more than one
function/operation, the same way an ED document's own shim is a set of numbered `§M.N` interactions, not one
undifferentiated capability. Nesting each touched function under its owning component makes that structure the
organizing principle instead of the use case that happened to surface it first — a component two use cases both
rely on appears once, with both functions listed beneath it, rather than being torn across two separate,
use-case-grouped bullets.

**Why §4 mandates one field per line and named nested types, rather than leaving formatting to whoever's
writing.** The same dogfooding pass found this section's own worked types inconsistently formatted — some
fields one per line, others crammed onto a single long line wherever a nested object was inlined anonymously —
and, separately, most function signatures elsewhere in the design citing an informal lowercase placeholder
(`report`, `words`, `scope`) instead of the PascalCase type actually defined here for that exact value
(`NumberingReport`, `Words`, `Scope`). Both problems share one cause: nothing said a structured value always
gets its own named entry, so it was easy to inline a shape once and never name it, and easy for a signature
elsewhere to reference that unnamed shape informally rather than by a real type name. Requiring every structured
value to be named, and named consistently in every signature that uses it, removes the anonymous middle ground
both problems were hiding in.
