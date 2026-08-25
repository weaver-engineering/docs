# Required Behavior Template

## Context
* [Documentation Standards](../standards/documentation-standards.md) - the document shape (Context, numbered
  sections, Rationale/Appendix) this template follows
* [Required Behaviors](../workflows/feature-workflow/required-behaviors.md) - what a Required Product Behaviour
  is, the derivation-and-checksum shape below, and why one file holds every behaviour of one operation
* [Weaver Engineering Workflows §3](../workflows/weaver-workflows.md) - Required Service Behaviour, the second
  user of this same shape
* [Specific Behaviors §4.1](../workflows/feature-workflow/specific-behaviors.md) - the nested-numbering,
  heading-depth-tracks-nesting convention this template's own numbered sections follow

One template, two users, differing only in owner, source, and filing root:

| | Required Product Behaviour | Required Service Behaviour |
|---|---|---|
| Derived from | a use case's own Technical Interpretation | a Service's own slice of the Feature's Service Flows |
| Owned by | Analysis | Architect (Services) |
| Filed at (final) | `docs/analysis/use-cases/{use-case-slug}/behaviors/{operation-slug}.md` | `docs/services/{service-slug}/behaviors/{operation-slug}.md`, proposed first at `docs/design/{feature-slug}/{design-task-ref}/services/{service-slug}/behaviors/{operation-slug}.md` |
| Addressed as | `{use-case-slug}.{operation-slug}-N` | `{service-slug}.{operation-slug}-N` |

One file per operation, never one file per behaviour — `{...}-N` above is a *reference* into the file, the same
way `§M.N` already addresses a section elsewhere in this repo, not a filename pattern. The file holds every
behaviour of this operation, indexed internally by `N`. The template itself is in the Appendix below, since it's
reference material to copy from, not indexed content in its own right.

# Appendix

````
---
derivation:
  derived_at: "{ISO timestamp}"
  source_checksum: "{checksum of the exact source content read to derive the behaviours below}"
---
# {Operation Slug} — {Operation Title}

## Context
* {link to the owning use case or Service}
* Required Behaviors (@docs/workflows/feature-workflow/required-behaviors.md) - the convention this document follows

**Realizes:** {the use case step(s), or Service Flow slice, this operation covers}

**Required Delivery Surface:** {UI | CLI | API — the first of the four interface layers, Weaver Engineering Workflows §5}

## 1 {Shared Entry Condition — Happy Path, Or Named Entry-State Variation}

**Realizes:** {which variation this is — "happy path", or "happy path (extension {M}{letter})" if this entry state exercises a use case Extension instead of the base steps}

**Given** {the concrete entry conditions/fixtures for this variation — the use case's own Preconditions plus every
prior operation's own Required Effect (Required Behaviors §3), literal example values, no Service named}

{If nothing further permutes this condition, §1 is a leaf: it's a complete behaviour on its own, so continue
directly with its own **Required Effect** here, in the same shape §1.1 uses below. If, as shown in this
template, further permutations exist beneath it (§1.1, §1.2, ...), §1 is a parent instead — the Given above is
everything it states; its own outcome isn't determined until a child narrows it (Specific Behaviors §4.1).}

### 1.1 {First Permutation Of §1 — name the one condition that decides the outcome}

{heading depth tracks nesting depth, one `#` deeper per `.`-separated segment beyond the first}

**Realizes:** {which variation this is — the happy path, or a named unhappy path, of either the base steps or a named Extension}

**Given** — as §1, but {only the condition(s) that actually differ}

**Required Effect** {the concrete, Service-agnostic outcome this variation demands — what must be true afterward, stated abstractly enough that no Service, Internal Component, or External Dependency is named}

### 1.2 {Second Permutation Of §1 — the alternative condition}

**Realizes:** {which variation this is, same shape as §1.1}

**Given** — as §1, but {the other condition(s) that differ}

**Required Effect** — as §1.1, but {only what actually differs}

## 2 {Named Unhappy Path}

**Realizes:** {"unhappy path — {what fails}", or a broad, not-yet-specific requirement like "any error is surfaced gracefully, with enough detail to identify its cause" where no concrete failure mode is knowable yet}

**Given** {as above, but with the failure condition present, where one is already knowable}

**Required Effect** {what graceful behaviour actually means here}

## 3 {Next Behaviour Title}

{a fresh top-level number for a genuinely different branch, or {N}.{M} nested under an existing one for a further permutation of it}

{...}
````

# Rationale

**Why one template serves both Required Product and Required Service Behaviour.** Both are the same kind of
fact, asked at two different levels of the same system: "what is required here, stated abstractly enough that
nothing downstream has been decided yet." A Required Product Behaviour asks that of a use case's own operation; a
Required Service Behaviour asks it of a Service, once architecting has decided that Service participates in a
flow. Giving them separate templates would suggest they're different kinds of thing when the only real
differences are who derives them, what they're checksummed against, and where they're filed — all already
captured in the table above.

**Why a derivation checksum, not a `reviewed`/call-tree block like a Specific Behavior document carries.** This
document records what's *required*, never what's *predicted* — binding to real functions, call trees, and
`reviewed` status all belong to Design's own reconciliation record (see `specific-behaviors.md` and
`chunk-scope.md`), never here. Keeping this document to just a derivation checksum is what makes it a stable,
independent artifact Design's own prediction can be reconciled against (Weaver Engineering Workflows §4) without
either one being able to silently drift into restating the other.

**Why "as §N, but..." and the nested-numbering convention are identical to a Specific Behavior document's.** The
permutation problem — several behaviours of one operation sharing every condition but one — is exactly the same
problem at this level as it is once a behaviour is bound to a real Service; reusing the same convention (Specific
Behaviors §4.1) rather than inventing a parallel one keeps a reader's mental model of "how behaviours nest" the
same across both documents they'll read for the same operation.
