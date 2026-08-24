# Documentation Standards

## Context
* [About Weaver Engineering](../about-weaver-engineering.md) - workspace overview
* [Weaver Engineering Workflows](../workflows/weaver-workflows.md) - the Feature Workflow this standard supports

## 1 Notes Versus Docs

`notes/` is free-form scratch: no template, no indexing, no linking requirement. It is where a concept lives
before it has been brought into compliance with this standard and moved into `docs/`. Everything under `docs/`
in a project's `<project>-docs` repo — and, for this repo, everything outside `notes/` — must comply with §2
through §5.

## 2 Minimum Directory Structure

Every project's `<project>-docs` repo has at least this structure. Additional directories are allowed (e.g. a
`docs/templates/`), but these are the required minimum, each with a defined purpose so it's unambiguous where new
content belongs:

```
PRODUCT.md
docs/
docs/analysis/
docs/analysis/use-cases/
docs/analysis/user-personas/
docs/features/
docs/design/
docs/architecture/
docs/services/
docs/infrastructure/
docs/glossary.md
notes/
```

* **`PRODUCT.md`** — repo root, sibling to `README.md`. The project's own front-matter-bearing record: what the
  project is, whether it's a Platform or a Product (a Platform is simply a Product whose customers are other
  projects' own SDEs, not end-users — no separate structural marker), and pointers into the rest of this
  structure. Every `<project>-docs` repo *is* one Product (or Platform) — see [Weaver Engineering
  Projects](../projects/weaver-projects.md) for why "project" resolves to Product rather than Service.
* **`docs/features/`** — one directory per Feature (`{feature-slug}/FEATURE.md`): a collection of Use Cases to be
  implemented or supported. A Feature exists before design, before any Service decomposition, and before route to
  market — it's a planning-level grouping, not a technical or commercial one.
* **`docs/services/`** — one directory per Service (`{slug}/SERVICE.md`): a Service's own interface, its Internal
  Components and External Dependencies, and its SLOs/SLIs. A Service is the unit Design actually decomposes a
  Feature's use cases into — see §2.1 and `docs/design/`'s own Rationale below for how a Service's contents get
  there.
* **`docs/architecture/`** — how this Product's Services connect to each other. Does **not** hold any one
  Service's own components or external dependencies (those live under that Service's own `docs/services/{slug}/`)
  — this directory is exclusively the cross-Service, Product-level integration picture.
* **`docs/infrastructure/`** — how Services are actually deployed: compute, network, datastores. The System layer
  of the Platform→Product→Use Case→Product Offering→Service→System→SLO continuum.
* **`docs/glossary.md`** — one entry per concept term used across this repo's docs: the term, a one-line
  definition, and a link to its full definition elsewhere. **Any new concept introduced into this repo's docs
  gets a glossary entry in the same PR that introduces it** — not deferred to a later cleanup pass.

`docs/offerings/` (`{slug}/OFFERING.md`) is **conditional**, not part of the unconditional minimum above: it
appears once the product has at least one deployable, consumable Service. A Product Offering is the channel — UI,
CLI, API — through which a Service's endpoint is actually consumed; a product with nothing deployable yet has
nothing for an Offering to expose, so the directory has no reason to exist until then.

This structure applies to each project's own `<project>-docs` repo. It does not apply to this repo
(`weaver-engineering/docs`), which organizes by `workflows/`, `projects/`, and `standards/` instead — but this
repo still complies with §3 through §5, including the glossary rule above: see this repo's own
[glossary.md](../glossary.md).

### 2.1 The Directory-Per-Entity Pattern

A concept that grows multiple satellite artifacts over its lifetime — rather than staying a single, self-contained
document — gets its own directory, with an UPPERCASE `{CONCEPT}.md` manifest as that directory's entry point:
`PRODUCT.md`, `docs/services/{slug}/SERVICE.md`, `docs/features/{slug}/FEATURE.md`, and, once a Use Case has grown
behaviors of its own, `docs/analysis/use-cases/{slug}/USE-CASE.md`. The manifest may carry machine-authored
frontmatter (§3) alongside its narrative body — a hybrid a plain `.md` document elsewhere in this repo doesn't
otherwise carry.

This is a test, not a fixed list: apply it to any future concept by asking whether it accumulates its own growing
set of sub-documents (a Service's components and dependencies; a Feature's use cases; a Use Case's own behaviors)
or stays a single fact worth stating once. Product Offering hasn't yet been confirmed either way — nothing so far
has shown it accumulating satellite artifacts the way a Service or Feature does, but that's not a settled no,
just not yet a demonstrated yes.

## 3 Document Shape

* Optional YAML frontmatter, if present, comes first. Where frontmatter is machine-authored — a `SERVICE.md`'s
  structural metadata, a design task's reconciliation record — its absence is a **flagged deficiency**, never a
  compliance failure: a tool that depends on it reports the gap and offers to (re)generate it, the same tolerance
  `.index/` files already get (§4's own Rationale), rather than erroring outright. This is what keeps a document
  usable even when it arrived from outside this ecosystem, or before its own frontmatter-writing tool existed yet.
* `# Title` in InitCaps, matching the file's slug.
* `## Context` — links, each with a one-line summary, to documents that provide context for this one. Not
  indexed.
* Body sections, numbered `## N Title` / `### N.M Title` / code blocks `N.M.a`. The first section (or
  subsection) after Context may be left unnumbered in the visible heading; it still gets the implicit id `0`
  for indexing.
* Optional `# Appendix` — supplementary reference material. Excluded from word-indexing (§4), along with
  everything nested beneath it — but still locatable and extractable via `.sections.yaml` (§4), not dropped
  from the index entirely.
* Optional `# Rationale` — the justification for the document's content: why it says what it says, options
  considered and discarded. Excluded from word-indexing (§4), along with everything nested beneath it — but
  still locatable and extractable via `.sections.yaml` (§4), not dropped from the index entirely. Kept separate
  so the indexed body stays concise and an agent doesn't have to read justification to get the facts, while
  it's still available for edge-case analysis.

Factual/normative content belongs in the numbered body. Justification belongs in `# Rationale`. This standard
polices that separation — it does not apply to itself by exception (see this document's own Rationale, below).

## 4 Indexing

`.index/` files are hand-written today only because the tooling to generate and maintain them automatically
doesn't exist yet. That tooling is already being built, not merely anticipated: the "Doc Search & Retrieval"
Feature (`@agent-plugins-docs/docs/design/doc-search-and-retrieval/doc-search-and-retrieval-feature.md`, Linear
[WVR-95](https://linear.app/weaver-engineering/issue/WVR-95)) realizes indexing itself
(`@agent-plugins-docs/docs/analysis/use-cases/UC-003-index-a-path.md`), searching an existing index
(`@agent-plugins-docs/docs/analysis/use-cases/UC-005-search-documentation.md`), and extracting content located by
either (`@agent-plugins-docs/docs/analysis/use-cases/UC-006-extract-document-content.md`). Once it ships, it does
a full reindex of a repo from scratch — a hand-written `.index/` entry doesn't get preserved or merged with the
tool's own output, it gets overwritten by it. Don't hand-write or hand-update `.index/` entries when creating or
editing a doc: the effort isn't worth spending against a reindex that's already going to discard it.

Every directory under `docs/` (in this repo: every directory holding documents subject to this standard) gets a
`.index/` subdirectory. For each `<slug>.md` in that directory:

* `.index/<slug>.sections.yaml` — one entry per section/code-block, **keyed by its id** (`number` from §3,
  including the implicit `0` case — every section has one, and, unlike a title, it's guaranteed unique): `title`,
  `type` (`section` | `code-block`), `start_line`, `end_line`.
* `.index/<slug>.words.yaml` — significant words per section, keyed by id the same way, so a section's
  structural entry and its word counts share the same key. The document itself is a node too, keyed by its own
  implicit id `0` — its word counts are whatever text appears before `## Context` (there is no separate
  `preamble` key; the document is just the root of the same id-keyed scheme every section uses).

  Word extraction rules:
  * Case-insensitive.
  * `-`, `_`, `.`, `:`, `/` stay inside a token (not word breaks); every other character is whitespace, except
    `@`, which starts a verbatim `@{repo-slug}/{path}[/§M.N]` token — indexed exactly as written, never
    decomposed (see §6).
  * A markdown link (`[text](url)`) is indexed by its **URL only**, truncated to the URI (no query string, no
    `#anchor`) — the visible link text is not separately indexed as prose.
  * Words reduce to a root form before indexing and before stopword matching — plurals and possessive `'s`
    contract to the base (`architects`/`architect's` → `architect`). The canonical root stopword list is
    [`stopwords.yaml`](stopwords.yaml): a generic English list plus domain terms whose ubiquity in this corpus gives
    them little discriminating power for search (e.g. `document`, which blocks `doc`/`docs`/`documents`/
    `documented`/`documentation` too, since stopwords apply to the root). It's a starting point, expected to be
    refined over time via retrospective analysis of index bloat, not fixed permanently at authoring time.
  * No minimum word length, no minimum occurrence count.
  * Section numbers, non-numbered code blocks, and `## Context` content are never indexed.
  * `# Appendix` and `# Rationale` (§3) open a word-indexing exclusion **zone**: every heading nested beneath
    one, by depth, is excluded too, until a heading returns to that depth or shallower. The zone only suppresses
    word-indexing — every heading inside it still gets its own `.sections.yaml` entry, same as any other
    section, so it stays locatable and extractable by reference even though it's excluded from search.
  * A word's count is scoped only to the node (document or section) where it textually appears — never rolled
    up into ancestor sections. Aggregation across a tree of documents is the search tool's job at query time
    (reading every `.words.yaml` under a scope and walking it), not the indexer's job at index time.
* `.index/<slug>.todo.yaml` — every `//TODO`-style marker in the document: `text` (its content), `section`
  (the containing node's title — the document's own title if it's before `## Context`), `line`, and `ref` (a
  task reference like `WVR-88`, pulled out separately when the text contains one, rather than left buried in
  `text`). A marker is recognized case-insensitively as `todo` or `to-do`, either prefixed by `//` or `#`
  (any amount of whitespace between the prefix and the word, including none) or postfixed by `:` (same
  whitespace rule) — so `//TODO`, `# todo`, and `TODO:` all match.

Together these support full-text search across a doc, a directory, or a whole docs repo — finding which
document or section covers a subject — and extraction of just the relevant section instead of a full-document
read. See the Appendix for a worked example.

## 5 Compliance At PR Time

Any PR touching `docs/` must leave every document it adds or modifies compliant with §2 through §4, index
entries included, before it can merge. This is checked at review time; there is no tooling yet that enforces it
automatically (see Rationale).

## 6 Cross-References

Within a document, a section reference is a `§M.N` or `§M.N.O` token — either plain text ("see §3.2") or the
text of a markdown link (`[§3.2](#3-2-title)`). These, together with any markdown link's `#anchor` target, are
the only same-document tokens a renumbering tool may rewrite when sections move. A bare `A.B.C`-shaped string
elsewhere in the prose (a version number, say) is never touched — the `§` sigil, or appearing as a link, is
what makes something a reference.

A reference to a heading or section in a *different* project's docs repo uses
`@{repo-slug}/{path}[/][§M.N]` — e.g. `@magpieweaver-docs/docs/glossary.md/§4` for a specific section, or
`@magpieweaver-docs/docs/glossary.md` for the whole document. This is exempt from same-document renumbering:
a tool renumbering sections in the current file must never touch an `@{...}` token, since it names a different
repository and file entirely, outside that tool's reach.

# Appendix

Worked example. Given:

```
# This Is A Title

Some initial blurb

## Context
* [link title](link) - <summary>
* [link title](link) - <summary>

## Unnumbered First Section
blurb

## 1 The First Numbered Section
The architects manage the architect's own blurb.

//TODO - expand this section (WVR-99)

### Another Unnumbered First Section
blurb

### 1.1 The First Numbered Section Of The Subsection
blurb

``` 1.1.a Figure Title
some code block content
```
```

the sections index (`this-is-a-title.sections.yaml`) is:

```yaml
sections:
  "0":
    title: "Unnumbered First Section"
    type: section
    start_line: 9
    end_line: 11
  "1":
    title: "The First Numbered Section"
    type: section
    start_line: 12
    end_line: 25
  "1.0":
    title: "Another Unnumbered First Section"
    type: section
    start_line: 17
    end_line: 19
  "1.1":
    title: "The First Numbered Section Of The Subsection"
    type: section
    start_line: 20
    end_line: 25
  "1.1.a":
    title: "Figure Title"
    type: code-block
    start_line: 23
    end_line: 25
```

the word index (`this-is-a-title.words.yaml`) is:

```yaml
"0":
  initial: 1
  blurb: 1
sections:
  "0":
    blurb: 1
  "1":
    architect: 2
    manage: 1
    blurb: 1
  "1.0":
    blurb: 1
  "1.1":
    blurb: 1
  "1.1.a":
    code: 1
    block: 1
    content: 1
```

`some` and `own` don't appear — both are on the stopword list. `architects` and `architect's` both reduce to
the root `architect`, so section `1` shows `architect: 2`, not two separate entries. The document root uses the
same implicit `0` id as any other unnumbered section — `sections.yaml` and `words.yaml` share one id scheme
throughout, root included.

and the TODO index (`this-is-a-title.todo.yaml`) is:

```yaml
todos:
  - text: "expand this section (WVR-99)"
    section: "The First Numbered Section"
    line: 15
    ref: "WVR-99"
```

The document itself is a node too, keyed by its own `# Title` (`"This Is A Title"`) — its word counts are
whatever text appears before `## Context`. There's no separate `preamble` key; the document root uses the same
title-keyed scheme every section uses, it's just keyed by the document's own title instead of a section's.

# Rationale

The first version of this standard (five prose principles: one concept per document, index-reachability,
bidirectional links, `//TODO` markers, tooling-over-manual-discipline) stated an end state without ever defining
when or how it was checked. It was violated by the first two PRs that touched docs after it was written — both
added documents with no index links — because nothing short of a human noticing enforced it, and no one did.

This version is stricter in three ways, each addressing a specific gap the failure exposed:
* **A fixed directory structure** replaces "put documents somewhere sensible" with a small set of directories
  each having one job, so there's never ambiguity about where new content belongs or an excuse for it to end up
  unreachable because its location doesn't map to anything in an index.
* **A fixed document shape with mandatory Context and numbered sections** exists because the actual audience for
  this documentation is an AI agent with a limited context budget and no memory between sessions, not (only) a
  human. An agent finding a document has to be able to tell what it's for, what it depends on, and which
  section answers its specific question, without reading the whole thing.
* **PR-time compliance, stated explicitly** turns "should be indexed" into a checked step, even though no
  automated gate exists yet — this repo's own retrofit (fixing the two PRs that violated the first version) is
  the forcing function for writing this section down precisely enough to check against.

The `.index/` machine-index scheme, and PR-time compliance without automated enforcement, are a deliberate
sequencing choice: the standard defines the target shape now; the five tools that would enforce and generate it
mechanically (auto-numberer, section indexer, word indexer, search, extractor) are separate, larger pieces of
work, scoped through the normal Feature Workflow rather than folded into defining the standard itself. Until
they exist, `.index/` files are hand-written — tedious for now, but it means "indexed" is actually true today
instead of deferred again the way index-reachability was deferred the first time.

Directory-structure and document-shape rules for the mandatory `docs/analysis`/`docs/design`/`docs/architecture`
split (§2) apply only to each project's own `<project>-docs` repo, not to this repo. `weaver-engineering/docs`
predates that structure, already has an established and populated organization (`workflows/`, `projects/`,
`standards/`), and isn't documenting one project's own analysis/design/architecture — restructuring it to fit a
shape designed for a single project's docs would cost real migration effort for no clarity gain here.

**Why "project" resolves to Product, not Service (§2).** WVR-166 asked directly whether the existing
one-code-repo-plus-one-docs-repo unit maps to Product or Service, once a Product can decompose into several
Services. It maps to Product: every `<project>-docs` repo observed so far (the-loom, magpie-weaver,
weaver-projects) reads as a whole commercial solution (or, for the-loom, a Platform — a Product whose customers
are other projects' own SDEs) rather than one functional execution boundary within a larger one. Mapping "project"
to Service instead would mean a Product that grows a second Service has to fragment into a second code-and-docs
repo pair — a far bigger, unforced structural commitment with nothing today suggesting it's actually needed.
Service, instead, becomes a subdivision *within* one project's own docs repo (`docs/services/`), which is exactly
what §2 now reflects.

**Why `docs/design/` stays its own top-level directory rather than folding under `docs/features/`.** Once Specific
Behaviors move to being owned by their Use Case (Analysis's own territory — see the deferred Feature Workflow
rewrite this document doesn't itself cover) and a design task's reconciliation record moves off the behavior it
binds and onto the design task instead, `docs/design/{feature-slug}/{design-task-ref}/` ends up holding *only*
Design's own output: the HLD, the chunk scope, and that reconciliation record. Nothing Analysis owns lives there,
and nothing Design owns leaks into `docs/analysis/` or `docs/features/` — keeping it a separate directory makes
that split literal rather than a convention someone has to remember.

**Why `docs/services/` isn't populated directly by Design.** A design task's own directory *proposes* new or
extended Services, and the components, external dependencies, and interfaces they need, as part of its solution
shape — nothing there is a standing fact about the product yet, only a candidate. Those proposals become real
entries under `docs/services/{slug}/...` only once the design is actually delivered. `docs/design/` never holds
standing product facts for the same reason a pull request isn't the same thing as what's already merged: a
Feature's design directory can genuinely propose two competing shapes for the same Service before either ships,
and nothing under `docs/services/` should have to referee that until one of them actually does.

§6's `@{repo-slug}/{path}[/§M.N]` syntax settles what AgentPlugins' UC-001 (Discuss A Project Concept And
Document It) had flagged as an open design question: each project's docs exist both as a local repo in the
weaver-engineering workspace and canonically on GitHub, so a plain relative markdown link across repos only
resolves in one of those two contexts. The `@{repo-slug}/{path}` form sidesteps that by naming the repo
explicitly rather than encoding a filesystem-relative or GitHub-relative path, leaving resolution (local
checkout vs. GitHub URL) to whatever's reading the reference rather than baking one choice into the link itself.

§4's `.index/` entries key by id, not title — an earlier version of this section keyed by title instead,
reasoning that markdown doesn't enforce heading numbers and a title is the only thing every section is
guaranteed to have. That's true, but it isn't the property that actually matters for a map key: a title is
guaranteed to *exist*, but nothing guarantees it's *unique*, and two headings can legitimately share one —
including, once the Appendix/Rationale word-indexing zone existed (below), two ordinary sections that happen to
carry the same title at different points in a document. Requiring document-wide unique titles just to keep a
safe key would be a real authoring restriction for no good reason. A section's own `id` (its `number`, including
the implicit `0` case) doesn't have this problem: it's guaranteed to exist for exactly the same reason title
is, and it's guaranteed unique by construction — only the very first section may go unnumbered, and every later
sibling always gets a real, distinct number (§3). `title` moved to being an ordinary field on the entry instead,
still present, still useful for display, just no longer depended on for correctness. Existing hand-written
`.index/` files predating this correction are left as they are rather than retrofitted, for the same reason the
original title-keying correction did: they'll get corrected the next time their subject document is edited, or
once the indexing tool itself exists and does a real pass.

§4's Appendix/Rationale word-indexing exclusion is a **zone**, covering everything nested beneath the heading
rather than just the heading itself — found while designing AgentPlugins' UC-006 (Extract Document Content): a
real subsection genuinely nested under `# Appendix` is still supplementary material, not part of the indexed
body, and treating only the literal `# Appendix`/`# Rationale` heading as excluded would leave its own children
searchable, defeating the point. The zone only suppresses word-indexing (search), not `.sections.yaml`
(location/extraction) — a heading inside the zone stays fully addressable by reference, so an agent that finds
"the distilled truth" via search can still deliberately follow a link into the supporting justification behind
it; only the noise of that justification is kept out of ordinary search results. A related idea — a *local*
`# Rationale` or `# Appendix` scattered under an arbitrary numbered section, rather than the one canonical
instance §3 describes — was considered and explicitly dropped rather than adopted: nothing in this standard
supports more than one Appendix or one Rationale per document, and the zone mechanism happening to be
position-agnostic as an implementation detail isn't a claim otherwise.

§4's word-indexing rules (stemming, stopwords, link-by-URL-only, `todo.yaml`) were elicited while writing
AgentPlugins' UC-003 (Index A Path) — the section-structure schema was settled first, but the actual word-index
mechanics had never been asked about at all and were being invented ad hoc per retrofit. Two choices worth
flagging: indexing markdown links by URL rather than link text is deliberate, not an oversight — in this
workspace's convention, meaningful slugs live in the path (`architects-assistant.md`), so link text is often
redundant with it, and indexing both would double-count the same signal. And `todo.yaml` exists as a separate
file rather than folded into `words.yaml` because a `//TODO` is a distinct kind of fact (outstanding work, with
its own optional task reference) rather than ordinary indexed prose — conflating the two would make "find open
TODOs" and "search document content" the same query when they're not.
