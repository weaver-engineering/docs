# Use Case Template

## Context
* [Documentation Standards](../standards/documentation-standards.md) - the document shape (Context, numbered sections, Rationale/Appendix) this template follows
* [Use Cases](../workflows/feature-workflow/use-cases.md) - what a use case is within the Feature Workflow (`//TODO`)
* [Specific Behaviors §2.1](../workflows/feature-workflow/specific-behaviors.md) - what the Technical Interpretation appendix below is for, and how design review uses it
* [Pseudocode Style](../workflows/feature-workflow/pseudocode-style.md) - the notation the Technical Interpretation appendix below is written in

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

# Appendix

## Technical Interpretation

{pseudocode for the steps and Extensions above, in the [Pseudocode Style](../workflows/feature-workflow/pseudocode-style.md), but independent of any solution — no
Internal Component or External Dependency named, no call trees. This is the design's own crystallization of
what this use case actually requires, grown and maintained during the Design step (not Analysis). It is
immutable once written, in the sense that matters here: it is never edited to reflect whatever solution gets
chosen around it, and it does not change just because that solution does. An edit to this section is an edit to
the use case's own requirement, not ordinary design work — see Design Feature Instructions §4.3. Design review
compares it, pseudocode to pseudocode, against the bound pseudocode recorded on the `SB-NNN` document(s) it links
to below (Design Feature Instructions §4.3) — see Specific Behaviors §2.1 and §2.8.

Once each operation this use case performs is identified (Specific Behaviors §2.4), link each one to the
`SB-NNN` document it causes to exist — a use case may perform several operations, so this is usually more than
one link.}
```

# Rationale

The shape had only ever existed as one worked example (AgentPlugins' UC-001, "Discuss A Project Concept And
Document It") before this template formalized it — worth stating explicitly since a use case can be written for
any project, not just AgentPlugins, so the template belongs here rather than in a single project's docs repo.

Putting the template in `# Appendix` rather than a numbered body section is a direct use of the documentation
standard's own vocabulary: it's supplementary reference material to copy from, not indexed content in its own
right — placeholder text (`{...}`) throughout would make for a fairly useless search index anyway. This mirrors
how `magpieweaver-docs`' `ADR-TEMPLATE.md`/`SPEC-TEMPLATE.md` are treated, predating this indexing scheme.

Technical Interpretation lives in the use case's own `# Appendix`, produced during Design, rather than in the
design directory alongside everything else Design produces. It's the pseudocode reflection of *this* use case
specifically, tightly coupled to its own steps and Extensions, and it needs to sit next to the narrative it
reflects for the pseudocode-to-pseudocode review comparison to be a comparison at all — filing it in the design
directory instead would mean flipping between two files to do the one thing this section exists for. It stays
unindexed and un-numbered because, like the rest of `# Appendix`, it's supplementary to the use case's own
actor-level narrative, not part of it — an agent reading the use case for its "wh...s" (per the Feature
Workflow's Analyse step) doesn't need it, only Design and design review do.

Immutability is stated directly, not left implicit in "grown and maintained during the Design step." An earlier
draft only said Technical Interpretation "does not change just because the chosen solution around it does,"
which reads as a description of what tends to happen rather than a rule — leaving open whether a designer,
mid-substitution (Design Feature Instructions §4.3), might reasonably tidy the Technical Interpretation to match
whatever it just got bound to. Stating outright that any edit to it is an edit to the use case closes that gap:
the design's own record of "what was substituted for what" belongs on the `SB-NNN` it produced, never folded
back into the requirement it was checked against.
