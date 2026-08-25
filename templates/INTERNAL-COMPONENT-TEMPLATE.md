# Internal Component Template

## Context
* [Documentation Standards §3](../standards/documentation-standards.md) - the document shape this template follows, including the machine-authored-frontmatter allowance the blocks below use
* [Design Directory And HLD §4](../workflows/feature-workflow/design-directory-and-hld.md) - what an internal component is, where its document lives, and how it differs from an External Dependency
* [Specific Behaviors §2](../workflows/feature-workflow/specific-behaviors.md) - how a `calls:` declaration below is walked into a Predicted Service Behaviour's traced call tree
* [Design Directory And HLD §4.5](../workflows/feature-workflow/design-directory-and-hld.md) - the extra pseudocode and usage lists `interface` functions carry, beyond what's shown below
* [Pseudocode Style](../workflows/feature-workflow/pseudocode-style.md) - the notation `pseudocode:` below is written in, including how a declared exception class backs an `ON FAILURE` clause elsewhere

Template for an internal component document, one file per component per Service, filed as `{component-slug}.md`
under `docs/services/{service-slug}/components/` — proposed first at
`docs/design/{feature-slug}/{design-task-ref}/services/{service-slug}/components/{component-slug}.md`, promoted
on delivery (`concepts/service.md` §2). Addressed `{service-slug}.{component-slug}`, extended to
`{service-slug}.{component-slug}.{function-slug}` for one function. `interface` is reserved for the Service's own
entry point (see Design Directory And HLD §4.4) — every other component takes its own descriptive slug. The
template itself is in the Appendix below, since it's reference material to copy from, not indexed content in its
own right.

# Appendix

````
---
functions:  # one entry per numbered function below, keyed by its own §N — machine-managed; every address is a
            # plain reference (no description folded into the string) since none of these lists carry a per-entry
            # note today
  "1":
    calls:
      - "{service-slug}.{component-slug}.{function-slug} or {service-slug}.{dependency-slug}.{function-slug} — every address this function is expected to call, from this function's own Purpose, not verified against its actual logic. May cross into another Service's own interface where the data flow (service-flows.md) calls for it."
    called_from:
      - "{service-slug}.{component-slug}.{function-slug} — every address whose calls: names this function; added here in the same edit that adds it there, never authored independently"
    # interface functions only — every other component's functions omit these three:
    pseudocode: |
      {the currently designed solution for this operation, in the Pseudocode Style, linked to the specific component and dependency calls it makes — see Design Directory And HLD §4.5}
    used_by_operations:
      - "{use-case-slug}.{operation-slug}"  # every Required Product Behaviour operation that relies on this function directly, if any — some interface functions exist purely to satisfy other Services in the flow, or purely internal Required Service Behaviours, and carry none
    used_by_behaviors:
      - "{service-slug}.{operation-slug}-N"  # every Predicted Service Behaviour that exercises it
  "2":
    calls: []
    called_from: []
---
# {Component Slug} — {Component Name}

**Kind:** {e.g. internal service | module | library | internally-owned data store | Service interface (entry point, `interface` only)}

## Context
* {link to the design task whose Key Decisions first decided this interface}
* {links to any other design tasks currently citing this document, added as each one starts to}

## Purpose

{what this component is responsible for, and what role it plays for the parts of the Service that depend on it. A component doesn't need one cohesive interface — it may be a registry of independent functions rather than a single class- or service-shaped contract; say so here if that's the shape this one takes.}

## 1 {Function Or Interaction Name}

{at minimum, a one-line purpose statement and its own concrete signature for this function — that's the required floor, even before the rest is filled in. Beyond that: request/response shape and error modes, specific enough for another component's or design task's own design to build against directly. Each function defines its own signature; it does not need to conform to some overall shape shared with the component's other functions. A parameter or return value that isn't a primitive cites its Data Types name (HLD Template §4) exactly, in PascalCase — never an informal lowercase alias invented for this one signature, even where the real type feels obvious from context.}

{this function's `calls:`/`called_from:` — and, if it's an `interface` function, its `pseudocode:`/`used_by_operations:`/`used_by_behaviors:` — live in this document's own frontmatter, under `functions."1"` (see above), not written inline here. Every function needs at least one of `calls:` or a sequence diagram below — a function that neither calls anything nor carries a diagram has nothing establishing its expected behaviour beyond the one-line purpose statement.}

{and/or a sequence diagram describing this function's own expected behaviour — not limited to functions that call other things. A leaf function (empty or absent `calls:`) can still be complex enough internally (branching, looping, multiple steps) to need one; a diagram here describes what happens *inside* this one function, independent of which specific behaviour's call tree happens to reach it. This stays inline, unlike the reference lists above — it's narrative to read, not a reference to look up. Same Mermaid `sequenceDiagram` format a specific behaviour's own call tree comment might otherwise summarize (see Specific Behaviors §2) — but scoped to this function alone:}

```mermaid
sequenceDiagram
    participant {this function}
    participant {something it calls, if anything}
    {this function}->>{something it calls}: {...}
```

## 2 {Next Function Or Interaction Name}

{...}

# Rationale

{why this interface was shaped this way — the reasoning behind the decision, not just the decision itself; see Design Directory And HLD §4.2}
````

# Rationale

An IC document carries its own `# Rationale`, unlike an ED document, because its interface is a decision the
design made rather than a fact it observed — see [Design Directory And HLD
§4.2](../workflows/feature-workflow/design-directory-and-hld.md). The design task that first introduced the
component records *that* the decision was made and why, as one of its own Key Decisions; the component's own
document carries the same reasoning forward so a later design task building against it directly — without
necessarily reading the HLD that created it — still has access to why the interface looks the way it does, not
just what it is.

Purpose is left unnumbered per the documentation standard's own §3 (the first section after Context may carry an
implicit, unshown `0`) — numbered functions then start at `1`, so `{service-slug}.{component-slug}.1` always
addresses the first real function rather than Purpose (in prose, cited as `§1`).

Each function's signature lives with the function, not with the component as a whole, because not every component
is shaped like a single class or service with one coherent interface. Some are exactly that; others are better
described as a registry of independently addressable functions with no shared shape at all.

A signature citing an informal lowercase alias instead of the actual PascalCase Data Types name it means reads as
fine locally but breaks the moment a reader tries to find that type's own shape.

**Why `calls:`, `called_from:`, and an `interface` function's own usage lists live in frontmatter, keyed by
`§N`, rather than inline under each numbered function.** These are the same kind of fact as a call tree or a
checksum elsewhere in this process — structured, machine-managed data a tool reads by address, not narrative a
human works through top to bottom (`documentation-standards.md` §3's frontmatter allowance). Scattering a small
YAML block into the middle of every function's own prose section meant a reader had to step around a data
structure to keep reading the contract, and a tool wanting the whole component's call graph had to parse
prose-adjacent YAML out of as many sections as the document has, rather than reading one block at the top.
Indexing that block by the same `§N` the function's own heading carries keeps the two views — human-readable
signature, machine-managed graph edges — aligned without duplicating the function's identity into the YAML a
second way.

`calls:` is declared once, on the function that does the calling, rather than assembled into a separate global
graph document — the same shape this repo already uses for `.index/` (documentation-standards.md §4): a fact
lives with the document that's authoritative for it. `calls:` is deliberately the *superset* of what a function
might call, not a single fixed sequence; a Predicted Service Behaviour's own traced call tree (Specific Behaviors
§2) picks one concrete walk through it for one concrete scenario.

`calls:` is a declared *expectation*, not a verified fact — see [Specific Behaviors
§3](../workflows/feature-workflow/specific-behaviors.md). Treat a mismatch against some behaviour's traced call
tree as something to reconcile through review, not as proof that either side is wrong on its own.

A function's own sequence diagram is a different tool from a call tree, not a smaller version of it, and stays
inline rather than moving to frontmatter for exactly that reason: a call tree records what *actually* happened
for one real scenario, consulted by address; a function's own diagram describes its *expected* internal
behaviour in general, meant to be read, not looked up.

`interface` functions carry `pseudocode:`, `used_by_operations:`, and `used_by_behaviors:` — no other component's
functions do — because only `interface` functions are operations (Design Directory And HLD §4.4): reached
directly from outside the Service, and therefore the only functions several different callers might rely on
independently of each other. An internal, non-interface function's usage is still findable through
`called_from:` walked back to whichever `interface` function it terminates at.

`called_from:` is `calls:` read backwards, maintained the same way this repo's Context sections already keep a
bidirectional link current — added at the target the same moment it's added at the source, never authored
independently later.
