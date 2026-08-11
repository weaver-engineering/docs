# Use Case Template

## Context
* [Documentation Standards](../standards/documentation-standards.md) - the document shape (Context, numbered sections, Rationale/Appendix) this template follows
* [Use Cases](../workflows/feature-workflow/use-cases.md) - what a use case is within the Feature Workflow (`//TODO`)

Template for a single use case, one file per use case, filed under a project's own `docs/analysis/use-cases/`
(project-scoped `UC-NNN` ids, mirroring that project's `ADR-NNN` sequence if it has one). The template itself is
in the Appendix below, since it's reference material to copy from, not indexed content in its own right.

# Appendix

```
# UC-{NNN} — {Title}

**Actor:** {link to the persona doc under `docs/analysis/user-personas/`}
**Scope:** {one or two lines on what this use case does *not* cover, with a
forward reference to §7 if there's a longer list}

## Context
* {links to the root index and any directly relevant docs, each with a one-line summary}

## 1 Goal

{what the actor is trying to achieve, in a sentence or two}

## 2 Trigger

{the event or condition that starts this use case}

## 3 Preconditions

{what must already be true for this use case to be invoked}

## 4 Main Success Scenario

{numbered steps. A step that relies on another use case's functionality
references it inline, by id, at that step, as an actual markdown link to
that use case's file — e.g. "...generates the section index
([UC-003](../UC-003-....md))...". This is the only place dependencies are
declared: no separate "Depends On" summary field. Once the word-indexer
(UC-004) exists, a document's dependency set is mechanically recoverable
anyway, by scanning its own word index for `UC-\d+` tokens outside
`## Context` — so a bespoke field would just be a second place for the same
information to drift out of sync with the actual steps.}

## 5 Postconditions

{observable end state once the main scenario completes}

## 6 Extensions

{numbered-step branches, Cockburn-style: `<step><letter>` — e.g. `2a`, `4c` —
each naming the branching condition and what happens instead}

## 7 Open Design Questions (not resolved by this use case)

{things this use case deliberately leaves undecided, and why — scope
boundaries, not justification for what *is* decided; justification belongs
in a `# Rationale` section per the documentation standard, if one is needed}
```

# Rationale

The shape had only ever existed as one worked example (AgentPlugins' UC-001, "Discuss A Project Concept And
Document It") before this template formalized it — worth stating explicitly since a use case can be written for
any project, not just AgentPlugins, so the template belongs here rather than in a single project's docs repo.

Putting the template in `# Appendix` rather than a numbered body section is a direct use of the documentation
standard's own vocabulary: it's supplementary reference material to copy from, not indexed content in its own
right — placeholder text (`{...}`) throughout would make for a fairly useless search index anyway. This mirrors
how `magpieweaver-docs`' `ADR-TEMPLATE.md`/`SPEC-TEMPLATE.md` are treated, predating this indexing scheme.
