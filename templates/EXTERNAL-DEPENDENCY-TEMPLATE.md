# External Dependency Template

## Context
* [Documentation Standards §3](../standards/documentation-standards.md) - the document shape this template follows, including the machine-authored-frontmatter allowance the `used_by:` block below uses
* [Design Directory And HLD §3](../workflows/feature-workflow/design-directory-and-hld.md) - what an external dependency is, the thin-shim convention, and where its document lives
* [Pseudocode Style §3](../workflows/feature-workflow/pseudocode-style.md) - how the error modes named below back an `ON FAILURE` clause in a caller's pseudocode

Template for an external dependency document, one file per dependency per Service, filed as `{dependency-slug}.md`
under `docs/services/{service-slug}/external-dependencies/` — proposed first at
`docs/design/{feature-slug}/{design-task-ref}/services/{service-slug}/external-dependencies/{dependency-slug}.md`,
promoted on delivery (`concepts/service.md` §2). Addressed `{service-slug}.{dependency-slug}`, extended to
`{service-slug}.{dependency-slug}.{function-slug}` for one operation. The template itself is in the Appendix
below, since it's reference material to copy from, not indexed content in its own right.

# Appendix

````
---
operations:  # one entry per numbered operation below, keyed by its own §N — machine-managed; a reference (an
             # address) and its own note are always separate fields, never concatenated into one string
  "1":
    used_by:
      - address: "{service-slug}.{operation-slug}-N"
        note: "{one line on what this behaviour needs from this operation, in its own terms}"
  "2":
    used_by: []
---
# {Dependency Slug} — {Dependency Name}

**Kind:** {e.g. REST API | database | MCP tool | message queue | filesystem}

## Context
* {links to any design tasks currently citing this document, added as each one starts to}

## Purpose

{what this dependency is, and why it's genuinely external — something this Service can't unit-test, not just a linked library. If it wraps a broader third-party API, say so here: the sections below describe this Service's own thin shim over it, not the dependency's full native interface.}

## 1 {Operation Or Interaction Name}

{concrete request shape, response shape, and error modes — specific enough for a behaviour's Given/Required Effect, and Design's own bound pseudocode, to cite this section directly. This is the shim's own interface, not a restatement of the dependency's full API. Each named error mode is an exception class a caller's `ON FAILURE` clause can reference (Pseudocode Style §3) — name them precisely enough to be used that way, not just described in passing. A request or response field that isn't a primitive cites its Data Types name (HLD Template §4) exactly, in PascalCase, the same rule the Internal Component Template's own signatures follow — never an informal lowercase alias.}

{no `calls:` declaration here — an External Dependency's own operation is always a leaf in the call graph; what a third-party system does internally isn't something this Service observes or declares. See Design Directory And HLD §3 and Specific Behaviors §2.}

{every relying Required/Predicted Service Behaviour is recorded in this document's own frontmatter, under `operations."1".used_by` — see above, not written inline here. This is what lets design review compare every usage of this operation side by side and catch a shim that's stopped being thin, without hunting through prose to find them.}

## 2 {Next Operation Or Interaction Name}

{...}
````

# Rationale

Each operation or interaction gets its own numbered section, rather than one prose description covering the
whole dependency, so that a behaviour can cite exactly the interaction it depends on —
`{service-slug}.{dependency-slug}.{function-slug}` — without pulling in interactions it doesn't use. See [Design
Directory And HLD §3.1](../workflows/feature-workflow/design-directory-and-hld.md) for why this document type
exists at the Service level rather than inside any one design task's own directory.

Purpose is left unnumbered per the documentation standard's own §3 (the first section after Context may carry an
implicit, unshown `0`) — numbered operations then start at `1`, so `{service-slug}.{dependency-slug}.1` always
addresses the first real operation rather than Purpose (in prose, cited as `§1`).

An ED operation never declares `calls:` — see [Internal Component
Template](INTERNAL-COMPONENT-TEMPLATE.md) — because the whole reason External Dependency and Internal Component
are different document types is that one is observed and the other is decided. Giving an ED operation calls of
its own would mean asserting something about a system this Service doesn't control and can't verify; every call
chain necessarily terminates at an ED operation rather than passing through it.

**Why `used_by:` lives in frontmatter, keyed by `§N`, rather than inline under each numbered section.** This is
the same kind of fact as a call tree or a checksum — structured, machine-managed data consulted by tooling and by
design review's own side-by-side comparison (`documentation-standards.md` §3's frontmatter allowance) — not
narrative a reader works through top to bottom. Keeping it inline mid-section mixed the two: a reader trying to
read the operation's own contract had to step around a data structure sitting in the middle of it, and a tool
trying to read every usage had to parse prose-adjacent YAML scattered across as many sections as the document
has, instead of one block at the top.

**Why each `used_by:` entry is `{address, note}`, never a single string concatenating the two.** An earlier
version of this template wrote each entry as one string, `"{address} - {note}"` — convenient to type, but it
means nothing can read the address back out mechanically without re-parsing prose, defeating the actual purpose
of recording it as a reference at all. Splitting them is what lets `used_by:` be read by a script the same way any
other address list in this system already is (`calls:`, a call tree), while still carrying the human-readable
note design review actually needs to compare usages side by side (Design Directory And HLD §3.4).
