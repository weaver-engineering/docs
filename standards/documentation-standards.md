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
`docs/features/` or `docs/templates/`), but these are the required minimum, each with a defined purpose so it's
unambiguous where new content belongs:

```
docs/
docs/analysis/
docs/analysis/use-cases/
docs/analysis/user-personas/
docs/design/
docs/architecture/
notes/
```

This structure applies to each project's own `<project>-docs` repo. It does not apply to this repo
(`weaver-engineering/docs`), which organizes by `workflows/`, `projects/`, and `standards/` instead — but this
repo still complies with §3 through §5.

## 3 Document Shape

* Optional YAML frontmatter, if present, comes first.
* `# Title` in InitCaps, matching the file's slug.
* `## Context` — links, each with a one-line summary, to documents that provide context for this one. Not
  indexed.
* Body sections, numbered `## N Title` / `### N.M Title` / code blocks `N.M.a`. The first section (or
  subsection) after Context may be left unnumbered in the visible heading; it still gets the implicit id `0`
  for indexing.
* Optional `# Appendix` — supplementary reference material. Not indexed.
* Optional `# Rationale` — the justification for the document's content: why it says what it says, options
  considered and discarded. Not indexed. Kept separate so the indexed body stays concise and an agent doesn't
  have to read justification to get the facts, while it's still available for edge-case analysis.

Factual/normative content belongs in the numbered body. Justification belongs in `# Rationale`. This standard
polices that separation — it does not apply to itself by exception (see this document's own Rationale, below).

## 4 Indexing

Every directory under `docs/` (in this repo: every directory holding documents subject to this standard) gets a
`.index/` subdirectory. For each `<slug>.md` in that directory:

* `.index/<slug>.sections.yaml` — one entry per numbered section/code-block: id, title, type (`section` |
  `code-block`), `start_line`, `end_line`.
* `.index/<slug>.words.yaml` — significant words (stopwords excluded), each with the section(s) it appears in
  and the count per section.

Together these support full-text search across a doc, a directory, or a whole docs repo — finding which
document or section covers a subject — and extraction of just the relevant section instead of a full-document
read. See the Appendix for a worked example.

## 5 Compliance At PR Time

Any PR touching `docs/` must leave every document it adds or modifies compliant with §2 through §4, index
entries included, before it can merge. This is checked at review time; there is no tooling yet that enforces it
automatically (see Rationale).

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
blurb

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
    end_line: 23
  "1.0":
    title: "Another Unnumbered First Section"
    type: section
    start_line: 15
    end_line: 17
  "1.1":
    title: "The First Numbered Section Of The Subsection"
    type: section
    start_line: 18
    end_line: 23
  "1.1.a":
    title: "Figure Title"
    type: code-block
    start_line: 21
    end_line: 23
```

and the word index (`this-is-a-title.words.yaml`) is:

```yaml
preamble:
  some: 1
  initial: 1
  blurb: 1
sections:
  "0":
    blurb: 1
  "1":
    blurb: 1
  "1.0":
    blurb: 1
  "1.1":
    blurb: 1
  "1.1.a":
    some: 1
    code: 1
    block: 1
    content: 1
```

`preamble` holds words from any text before `## Context` (title blurb) — it exists outside the numbered-section
scheme entirely, since that text isn't part of section `0` or any other section.

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
