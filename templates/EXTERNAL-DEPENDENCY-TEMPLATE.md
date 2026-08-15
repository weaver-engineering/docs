# External Dependency Template

## Context
* [Documentation Standards](../standards/documentation-standards.md) - the document shape (Context, numbered sections, Rationale/Appendix) this template follows
* [Design Directory And HLD §3](../workflows/feature-workflow/design-directory-and-hld.md) - what an external dependency is, the thin-shim convention, and where its document lives
* [Pseudocode Style §3](../workflows/feature-workflow/pseudocode-style.md) - how the error modes named below back an `ON FAILURE` clause in a caller's pseudocode

Template for an external dependency document, one file per dependency, filed as `ED-NNN-{slug}.md` under
`docs/architecture/external-dependencies/` in the depending project's own docs repo. The template itself is in
the Appendix below, since it's reference material to copy from, not indexed content in its own right.

# Appendix

````
# ED-{NNN} — {Dependency Name}

**Kind:** {e.g. REST API | database | MCP tool | message queue | filesystem}

## Context
* {links to any design directories currently citing this document, added as each one starts to}

## Purpose

{what this dependency is, and why it's genuinely external — something this project can't unit-test, not just a linked library. If it wraps a broader third-party API, say so here: the sections below describe this project's own thin shim over it, not the dependency's full native interface.}

## 1 {Operation Or Interaction Name}

{concrete request shape, response shape, and error modes — specific enough for a specific behavior's Given/Then to cite this section directly. This is the shim's own interface, not a restatement of the dependency's full API. Each named error mode is an exception class a caller's `ON FAILURE` clause can reference (Pseudocode Style §3) — name them precisely enough to be used that way, not just described in passing.}

{no `calls:` declaration here — an External Dependency's own operation is always a leaf in the call graph; what a third-party system does internally isn't something this project observes or declares. See Design Directory And HLD §3 and Specific Behaviors §2.6.}

```yaml
used_by:
  - "UC-{NNN} - {one line on what this use case needs from this operation, in its own terms}"
```

{every relying use case, so design review can compare usages side by side and catch a shim that's stopped being thin — see Design Directory And HLD §3.4}

## 2 {Next Operation Or Interaction Name}

{...}
````

# Rationale

Each operation or interaction gets its own numbered section, rather than one prose description covering the
whole dependency, so that a specific behavior can cite exactly the interaction it depends on — `ED-NNN §M.N` —
using the documentation standard's existing `§M.N` cross-reference syntax, without pulling in interactions it
doesn't use. See [Design Directory And HLD §3.1](../workflows/feature-workflow/design-directory-and-hld.md) for
why this document type exists at the project level rather than inside any one Feature's design directory.

Purpose is left unnumbered per the documentation standard's own §3 (the first section after Context may carry
an implicit, unshown `0`) — numbered operations then start at `1`, so `ED-NNN §1` always addresses the first
real operation rather than Purpose.

An ED operation never declares `calls:` — see [Internal Component
Template](INTERNAL-COMPONENT-TEMPLATE.md) — because the whole reason External Dependency and Internal Component
are different document types is that one is observed and the other is decided. Giving an ED operation calls of
its own would mean asserting something about a system this project doesn't control and can't verify; every call
chain necessarily terminates at an ED operation rather than passing through it.

`used_by:` exists so design review can compare, side by side, what every relying use case actually needs from
one operation — the check that catches a shim quietly becoming less thin than it reads as from any single use
case's point of view (see [Design Directory And HLD
§3.4](../workflows/feature-workflow/design-directory-and-hld.md)).
