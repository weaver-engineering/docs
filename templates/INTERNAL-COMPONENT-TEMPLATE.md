# Internal Component Template

## Context
* [Documentation Standards](../standards/documentation-standards.md) - the document shape (Context, numbered sections, Rationale/Appendix) this template follows
* [Design Directory And HLD §4](../workflows/feature-workflow/design-directory-and-hld.md) - what an internal component is, where its document lives, and how it differs from an External Dependency
* [Specific Behaviors §2.6](../workflows/feature-workflow/specific-behaviors.md) - how a `calls:` declaration below is walked into a specific behavior's traced call tree
* [Specific Behaviors §2.7](../workflows/feature-workflow/specific-behaviors.md) - why `calls:` is a declared expectation rather than a verified fact, and how it's reconciled against a traced call tree
* [Design Directory And HLD §4.5](../workflows/feature-workflow/design-directory-and-hld.md) - the extra pseudocode and usage lists `IC-000`'s own functions carry, beyond what's shown below

Template for an internal component document, one file per component, filed as `IC-NNN-{slug}.md` under
`docs/architecture/components/` in the owning project's own docs repo. `IC-000` is reserved for the system's own
entry point (see Design Directory And HLD §4.5) — every other component takes the next free number. The
template itself is in the Appendix below, since it's reference material to copy from, not indexed content in
its own right.

# Appendix

````
# IC-{NNN} — {Component Name}

**Kind:** {e.g. internal service | module | library | internally-owned data store | system interface (entry point, IC-000 only)}

## Context
* {link to the design directory whose Key Decisions first decided this interface}
* {links to any other design directories currently citing this document, added as each one starts to}

## Purpose

{what this component is responsible for, and what role it plays for the parts of the system that depend on it. A component doesn't need one cohesive interface — it may be a registry of independent functions rather than a single class- or service-shaped contract; say so here if that's the shape this one takes.}

## 1 {Function Or Interaction Name}

{at minimum, a one-line purpose statement and its own concrete signature for this function — that's the required floor, even before the rest is filled in. Beyond that: request/response shape and error modes, specific enough for another component's or Feature's design to build against directly. Each function defines its own signature; it does not need to conform to some overall shape shared with the component's other functions.}

{every function needs at least one of the two below — a function that neither calls anything nor carries a diagram has nothing establishing its expected behaviour beyond the one-line purpose statement:}

```yaml
calls:
  - "{IC-NNN §M or ED-NNN §M.N — every address this function is expected to call, from this function's own Purpose, not verified against its actual logic}"
```

{and/or a sequence diagram describing this function's own expected behaviour — not limited to functions that call other things. A leaf function (empty or absent `calls:`) can still be complex enough internally (branching, looping, multiple steps) to need one; a diagram here describes what happens *inside* this one function, independent of which specific behavior's call tree happens to reach it. Same Mermaid `sequenceDiagram` format a specific behavior's optional diagram would use, if it had one (see Specific Behaviors §2.6) — but scoped to this function alone:}

```mermaid
sequenceDiagram
    participant {this function}
    participant {something it calls, if anything}
    {this function}->>{something it calls}: {...}
```

{`IC-000` functions only — every other component's functions stop above:}

```yaml
pseudocode: |
  {the currently designed solution for this operation, linked to the specific IC/ED calls it makes — see Design Directory And HLD §4.5}
used_by_steps:
  - "UC-{NNN} step {M}"
used_by_behaviors:
  - "SB-{NNN} §{M}"
```

## 2 {Next Function Or Interaction Name}

{...}

# Rationale

{why this interface was shaped this way — the reasoning behind the decision, not just the decision itself; see Design Directory And HLD §4.2}
````

# Rationale

An IC document carries its own `# Rationale`, unlike an ED document, because its interface is a decision the
design made rather than a fact it observed — see [Design Directory And HLD
§4.2](../workflows/feature-workflow/design-directory-and-hld.md). The HLD that first introduced the component
records *that* the decision was made and why, as one of its own Key Decisions; the component's own document
carries the same reasoning forward so a later Feature building against `IC-NNN` directly — without necessarily
reading the HLD that created it — still has access to why the interface looks the way it does, not just what it
is.

Purpose is left unnumbered per the documentation standard's own §3 (the first section after Context may carry
an implicit, unshown `0`) — numbered functions then start at `1`, so `IC-NNN §1` always addresses the first real
function rather than Purpose.

Each function's signature lives with the function, not with the component as a whole, because not every
component is shaped like a single class or service with one coherent interface. Some are exactly that; others
are better described as a registry of independently addressable functions with no shared shape at all. Requiring
one interface description at the component level would force the first shape onto components that are honestly
the second, and the numbered-function structure already gives each function everywhere it needs to stand on its
own regardless of which shape the component actually has.

A function's one-line purpose blurb and its own signature are a required floor, not the target — a purpose with
no signature can't be built against, and a signature with no stated purpose is exactly as unreviewable as a full
contract nobody bothered to explain.

`calls:` is declared once, on the function that does the calling, rather than assembled into a separate global
graph document. This is the same shape this repo already uses for `.index/` (documentation-standards.md §4):
a fact lives with the document that's authoritative for it, and anything wanting the whole picture — a global
call graph, a full-text search index — assembles it by reading every local declaration rather than by
maintaining a second, centralized copy that can drift from the first. `calls:` is deliberately the *superset* of
what a function might call — a real function often branches — not a single fixed sequence; a specific behavior's
own traced call tree (Specific Behaviors §2.6) picks one concrete walk through it for one concrete scenario.

`calls:` is a declared *expectation*, not a verified fact — see [Specific Behaviors
§2.7](../workflows/feature-workflow/specific-behaviors.md). Making it mechanically accurate would require
capturing this function's actual internal logic in the design, which this document deliberately doesn't do.
Treat a mismatch against some specific behavior's traced call tree as something to reconcile through review, not
as proof that either side is wrong on its own.

A function's own sequence diagram is a different tool from a specific behavior's call tree, not a smaller
version of it. A specific behavior's tree records what *actually* happened for one real scenario — an
after-the-fact trace. A function's own diagram, by contrast, can describe its *expected* internal behaviour in
general — the logical steps it takes regardless of which caller reached it, branches and loops included, the
same way its `calls:` declaration is a superset rather than one concrete walk. That's why a diagram belongs here,
on the function, and not on a specific behavior's call tree — see [Specific Behaviors
§2.6](../workflows/feature-workflow/specific-behaviors.md).

`IC-000` functions carry `pseudocode:`, `used_by_steps:`, and `used_by_behaviors:` — no other component's
functions do — because only `IC-000`'s functions are operations (Design Directory And HLD §4.4): reached
directly from outside the system, and therefore the only functions several different use cases might rely on
independently of each other, each with their own expectations of it. An internal, non-entry-point function is
only ever reached through a call tree that already starts at `IC-000`, which is traceability enough on its own
— see [Design Directory And HLD §4.5](../workflows/feature-workflow/design-directory-and-hld.md).
