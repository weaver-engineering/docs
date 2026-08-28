# HLD Template

## Context
* [Documentation Standards](../standards/documentation-standards.md) - the document shape (Context, numbered sections, Rationale/Appendix) this template follows
* [Design Directory And HLD](../workflows/feature-workflow/design-directory-and-hld.md) - what an HLD is, where it's filed, and what each of its required sections must contain

Template for one design task's high-level design document, filed as `hld.md` at the root of
`docs/design/{feature-slug}/{design-task-ref}/` in the project's own docs repo — usually one Service's worth of
design work (Weaver Engineering Workflows §6). The template itself is in the Appendix below, since it's reference
material to copy from, not indexed content in its own right.

# Appendix

````
# {Design Task Ref} — {Service Slug} High-Level Design

## Context
* {link to the initial feature document this design realizes}
* [Service Flows](../service-flows.md) - the Feature-wide flow this task's own Service participates in
* {link to each use case this design realizes}
* Design Directory And HLD (@docs/workflows/feature-workflow/design-directory-and-hld.md) - the convention this document follows

## 1 Scope

* **Service:** [{Service Slug}]({link to docs/services/{slug}/SERVICE.md, or "new — proposed here" if none exists yet})
* **Use cases:**
  + [{use-case-slug}]({relative link})
  + [{use-case-slug}]({relative link})
* **Delivers:** [chunk-scope.yaml](./chunk-scope.yaml) once it exists; omit this line entirely while the task is still in progress

{what this task's own scope explicitly excludes is stated separately, once, at the Feature level in the initial
feature document — not repeated here}

## 2 Solution Overview

{a concrete map: which components this design introduces or modifies, one line on each one's responsibility, and what each talks to — other components, external dependencies, another Service's own interface where the data flow crosses one. Not prose, not a restatement of the predicted behaviours.}

## 3 Key Decisions

{one subsection per Open Design Question pulled from every use case in scope, resolved or explicitly deferred; one subsection for Crystallizing The Interface if this task's own Service's `interface` hadn't already been crystallized by an earlier task; plus one subsection per component listed in Solution Overview justifying the interface decided for it}

### 3.1 {Open Design Question, quoted or paraphrased from the use case}

{the decision, stated normatively — justification goes in # Rationale, not here}

### 3.2 {Component Name} Interface

{the interface decided for this component, stated normatively — justification goes in # Rationale}

## 4 Data Types

{the shapes of data this Service persists (data at rest, e.g. what an External Dependency stores) and exchanges
(data in flight, e.g. request/response payloads) — concrete enough for behaviours to instantiate. One
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

{one top-level bullet per component — never grouped or nested under whichever Required Service Behaviour introduced it. An Internal Component document can expose more than one function (the same `.{function-slug}` shape an External Dependency's own shim uses); nest each one this design actually touches as a sub-bullet beneath its component, not as a peer entry in a flat list of functions.}

* {Component Name} — {link to its proposal or promoted document, `{service-slug}.{component-slug}`}
  * {function/operation slug} — **{as-is | extended | new}** — {one line: what this design needs from it, or decided for it if extended/new}
  * {another function/operation this design touches on the same component, if any}

{`{service-slug}.interface` is always this Service's own entry point — no separate marker needed, the slug says it.}

## 6 External Dependencies

{same shape as §5 — one top-level bullet per dependency, its own operations nested beneath it}

* {Dependency Name} — {link to its proposal or promoted document, `{service-slug}.{dependency-slug}`}
  * {operation slug} — **{as-is | extended | new}** — {one line: what this design uses it for}

## 7 Predicted Service Behaviours

* [{service-slug}.{operation-slug}](./services/{service-slug}/behaviors/{operation-slug}.md) - binds [{use-case-slug}.{operation-slug}]({link to the Required Product or Required Service Behaviour it realizes})

## 8 Technology Stack

{languages, frameworks, and libraries this design commits to}

# Rationale

{every candidate considered for each Key Decision in §3 and why it was discarded, not just the winner — see [Design Feature Instructions §5](../workflows/feature-workflow/design-feature-instructions.md)}
````

# Rationale

Data Types (§4 of the template), Internal Components (§5), and External Dependencies (§6) are kept as separate
sections rather than folded into Solution Overview, even though all of them describe "what this Service is made
of," because they answer different reviewer questions: Solution Overview is the map, Data Types is what moves
around the map, Internal Components is what the design built and owns, External Dependencies is what it depends
on outside itself. Collapsing them would make the HLD harder to skim for the one thing a reviewer is actually
checking.

Each entry in §5 and §6 is classified as-is, extended, or new — not just "new or extended" — because "as-is"
is a real, distinct state: an existing function this design relies on but doesn't change at all, as opposed to
one gaining a new call it doesn't currently make ("extended"). Collapsing the two would lose exactly the
distinction Gap Analysis (Design Feature Instructions §3) exists to draw.

Solution Overview is deliberately a map, not a restatement of the predicted behaviours — an agent reading it
needs "what talks to what," which a list of black-box Given/When/Then scenarios can't provide, by [Specific
Behaviors §1](../workflows/feature-workflow/specific-behaviors.md)'s own design.

§7, Predicted Service Behaviours, is deliberately just a list of links rather than the behaviours themselves —
the HLD identifies which behaviours exist; their own record lives in `reconciliation.yaml`, per [Specific
Behaviors](../workflows/feature-workflow/specific-behaviors.md).

**Why §5 and §6 nest functions/operations under their component, not under whichever behaviour introduced them.**
An Internal Component (or External Dependency) document can expose more than one function/operation, the same
way an ED document's own shim is a set of numbered interactions, not one undifferentiated capability. Nesting
each touched function under its owning component makes that structure the organizing principle instead of the
behaviour that happened to surface it first — a component two behaviours both rely on appears once, with both
functions listed beneath it.

**Why §4 mandates one field per line and named nested types, rather than leaving formatting to whoever's
writing.** A dogfooded design pass (WVR-95) found this section's own worked types inconsistently formatted, and
most function signatures elsewhere in the design citing an informal lowercase placeholder instead of the real
PascalCase type. Requiring every structured value to be named, and named consistently in every signature that
uses it, removes the anonymous middle ground both problems were hiding in.
