# Required Behavior Template

## Context
* [Documentation Standards](../standards/documentation-standards.md) - the document shape (Context, numbered
  sections, Rationale/Appendix) this template follows
* [Weaver Engineering Workflows §3](../workflows/weaver-workflows.md) - what a Required Service Behaviour is,
  and the reconciliation it feeds
* [Architect Solution](../workflows/feature-workflow/architect-solution.md) - the Service Flows a Required
  Service Behaviour is derived from
* [Specific Behaviors §4.1](../workflows/feature-workflow/specific-behaviors.md) - the nested-numbering,
  heading-depth-tracks-nesting convention this template's own numbered sections follow

Template for a **Required Service Behaviour**: what one Service is required to do at one of its operations,
derived from that Service's own slice of the Feature's Service Flows and owned by `Architect Service`. Filed at
`docs/services/{service-slug}/behaviors/{operation-slug}.md`, proposed first inside the owning design task at
`docs/design/{feature-slug}/{design-task-ref}/services/{service-slug}/behaviors/{operation-slug}.md`, and
addressed as `{service-slug}.{operation-slug}-N`.

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
* {link to the owning Service}
* {link to the Service Flows slice this operation's behaviours are derived from}

**Realizes:** {the Service Flow slice this operation covers — and, where this operation is one a use case's own
step actually triggers, that use case's operation document and the cells of it this behaviour answers to}

## 1 {Shared Entry Condition — Happy Path, Or Named Entry-State Variation}

**Realizes:** {which variation this is — "happy path", or a named variation of it}

**Given** {the concrete entry conditions for this variation, taken from what the Service Flows say this Service
receives at this point — literal example values, no Internal Component or External Dependency named}

{If nothing further permutes this condition, §1 is a leaf: it's a complete behaviour on its own, so continue
directly with its own **Required Effect** here, in the same shape §1.1 uses below. If, as shown in this
template, further permutations exist beneath it (§1.1, §1.2, ...), §1 is a parent instead — the Given above is
everything it states; its own outcome isn't determined until a child narrows it (Specific Behaviors §4.1).}

### 1.1 {First Permutation Of §1 — name the one condition that decides the outcome}

{heading depth tracks nesting depth, one `#` deeper per `.`-separated segment beyond the first}

**Realizes:** {which variation this is — the happy path, or a named unhappy path}

**Given** — as §1, but {only the condition(s) that actually differ}

**Required Effect** {the concrete outcome this variation demands of this Service — what must be true afterward, stated abstractly enough that no Internal Component or External Dependency is named}

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

**Why this template no longer serves two users.** An earlier version served both Required Product Behaviour and
Required Service Behaviour, on the argument that they were one kind of fact asked at two levels. Required
Product Behaviour has since been retired: a use case's own operation documents state its condition space, its
cells and its fixtures directly, and Design references those rather than a second, folded restatement of them
([Use Cases §2.1](../workflows/feature-workflow/use-cases.md), [Operation
Fixtures](../workflows/feature-workflow/operation-fixtures.md)). What remains is the one user that was never a
restatement of anything upstream: a Service's own required behaviours, derived from architecting a flow, and
covering behaviours no use case ever sees.

**Why there is no Required Delivery Surface field.** An earlier version stamped the interface kind (UI/CLI/API)
onto each behaviour document. A delivery surface is a property of a deployable boundary rather than of a
behaviour, and it is formally decided by [`Architect
Solution`](../workflows/feature-workflow/architect-solution.md), in context and against the use cases the
boundary has to satisfy — so it is recorded there, with the Service it belongs to, and a behaviour document
neither states nor restates it. Where that decision has not been made, the question is still answered rather
than deferred: the architect asserts it and the design claims the fact against that assertion ([Weaver
Engineering Workflows §2](../workflows/weaver-workflows.md)). A behaviour document is not the right home in
either case.

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
