# Use Case Template

## Context
* [Documentation Standards](../standards/documentation-standards.md) - the document shape (Context, numbered
  sections, Rationale/Appendix) this template follows, and §2.1's directory-per-entity pattern this document uses
* [Use Cases](../workflows/feature-workflow/use-cases.md) - what a use case is within the Feature Workflow, and
  why its boundary is the actor's real goal, not operation count
* [User Personas](../workflows/feature-workflow/user-personas.md) - what the `Actor` field below links to, when the
  actor is human
* [Analysing A Feature](../workflows/feature-workflow/analysing-a-feature.md) - how an operation relates to a
  Feature's own capability, deferred to or defined inline
* [Architect Solution](../workflows/feature-workflow/architect-solution.md) - what this document's Step
  Contracts and operations feed into, Feature-wide
* [Use Case Operation Template](USE-CASE-OPERATION-TEMPLATE.md), [Operation Fixtures](../workflows/feature-workflow/operation-fixtures.md) -
  the shape and rules for what a Step Contract's `STATES` field points at

Template for a single use case, filed as `docs/analysis/use-cases/{use-case-slug}/USE-CASE.md` — the
directory-per-entity pattern, since a use case grows `operations/` alongside it (one file per operation, its own
condition space — [Use Case Operation Template](USE-CASE-OPERATION-TEMPLATE.md)) and `fixtures/` (the concrete
fixtures those condition spaces reference, [Operation
Fixtures](../workflows/feature-workflow/operation-fixtures.md)). A use case is addressed by its own slug, never
a numeric id. The template itself is in the Appendix below, since it's reference material to copy from, not
indexed content in its own right.

# Appendix

```
# {Use Case Slug} — {Title}

**Actor:** {link to the persona doc under `docs/analysis/user-personas/`, or the systematic actor's name directly
if this use case has no persona (User Personas §2)}
**Scope:** {one or two lines on what this use case does *not* cover, with a
forward reference to §7 if there's a longer list}

## Context
* {links to the root index and any directly relevant docs, each with a one-line summary}

## 1 Goal

{what the actor is trying to achieve, in a sentence or two — the actual test
of this use case's own boundary (Use Cases §1.1), not how many operations or
Services it ends up taking to get there}

## 2 Trigger

{the event or condition that starts this use case}

## 3 Preconditions

{what must already be true for this use case to be invoked}

## 4 Main Success Scenario

{numbered steps. A step that performs an operation (Use Cases §2) carries its own **Step Contract** (Use Cases
§2.1) — the boundary it's perceived to cross, and a pointer to that operation's own condition space:

N. {Step name}
    **BOUNDARY:** {the boundary this operation is perceived to cross, optionally with a guess at what kind of
    thing it is — a CLI, an API, a UI — or any further hypothetical detail worth recording}
    **STATES:** [operations/{N}-{operation-slug}.md](operations/{N}-{operation-slug}.md) — the entry states
    this step admits, the state each establishes, and the fixture exposing each
    {narrative description of the step}

A step that is pure branching or narrative — nothing crossing a boundary — carries neither. A use case may be
drafted with plain narrative steps and no Step Contracts at all; `BOUNDARY`/`STATES` are added in a second
pass, once the steps crossing a boundary are identified (Use Cases §2.1). While adding Step Contracts, note
for each operation whether it defers to an existing capability or needs its own inline spec (Analysing A
Feature §4), and write its condition space at `operations/{N}-{operation-slug}.md` (Use Case Operation
Template). Nothing folds that into a further document: it is the requirement, and everything downstream
references it.

A step that relies on another use case's functionality references it inline, by id, at that step, as an actual
markdown link to that use case's own directory — e.g. "...generates the section index
([find-a-path](../use-cases/find-a-path/USE-CASE.md))...". This is the only place dependencies are declared: no
separate "Depends On" summary field. Once the word-indexer exists, a document's dependency set is mechanically
recoverable anyway, by scanning its own word index for use-case-slug links outside `## Context` — so a bespoke
field would just be a second place for the same information to drift out of sync with the actual steps.}

## 5 Postconditions

{observable end state once the main scenario completes — what Feature-level
reconciliation ultimately checks the chain of operations against, see
Weaver Engineering Workflows §4}

## 6 Extensions

{numbered-step branches, Cockburn-style: `<step><letter>` — e.g. `2a`, `4c` —
each naming the branching condition and what happens instead. An Extension is a cell of the operation it
branches from, not an operation of its own — it carries no Step Contract, only a pointer to which cell of that
operation's own document (`operations/{N}-{operation-slug}.md`) it corresponds to (Use Cases §2.1).}

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

**Why Step Contracts replace what an earlier version of this template called Technical Interpretation.**
Technical Interpretation was solution-independent pseudocode, held in this Appendix, that Design would later
*bind* — substituting a real function for each logical call — and compare against, pseudocode-to-pseudocode. The
design process that comparison served no longer exists: Design is now required to reconcile against required
effects derived from the use case, not against a pseudocode restatement of it (see the metamodel's own account of
why bound pseudocode was dropped). With nothing left to bind against or compare to, a separate pseudocode
dialect has no work left to do. What Technical Interpretation was still actually doing — identifying operations,
and fixing the state around them, while writing the steps — survives as Step Contracts (Use Cases §2.1), stated
directly on the Main Success Scenario and Extensions steps themselves, in the same plain prose the rest of the
use case is already written in. There is accordingly no longer a Technical Interpretation appendix: a use case
with Step Contracts on its steps needs nothing more.

**Why Step Contracts are stated on the steps directly rather than kept in their own Appendix section.** An
Appendix exists for supplementary material a reader of the narrative doesn't need — which was true of Technical
Interpretation's pseudocode, a separate reflection of the steps rather than the steps themselves. A Step Contract
is not a reflection of its step; it *is* the step's own contract, so separating it out would recreate the two
documents-that-must-agree problem Technical Interpretation's binding step existed to manage, for no reason left
to manage it.

**Why a use case may be drafted with no Step Contracts and still be complete.** The actor-level narrative — Goal,
Preconditions, Main Success Scenario, Extensions, Postconditions — is a real, reviewable statement of what's
wanted on its own; `BOUNDARY`/`STATES` and the operation documents they point at add the condition-space detail
that later derivation and architecting need, but adding them is a distinct, later act of analysis, not a
precondition for the narrative itself being finished. Requiring Step Contracts up front would force every use
case through a heavier first draft than its goal always warrants.

**Why a Step Contract points at a condition space rather than stating a single Given/Then pair.** An earlier
version of this template bracketed a step with a single entry state and a single established state, each
carrying one fixture. A step that crosses a boundary rarely has just one of either — it has a condition space,
several dimensions of it, and a Given/Then pair can only ever express whichever one cell its author happened
to have written first. `STATES` points at [Use Case Operation Template](USE-CASE-OPERATION-TEMPLATE.md)'s own document instead,
which is that same bracketing done properly: every dimension, every cell, and the fixture behind each — see
[Operation Fixtures](../workflows/feature-workflow/operation-fixtures.md) for why and how.

**Why fixtures live in their own `fixtures/` subdirectory rather than back in this document.** The directory a
use case occupies exists specifically so it can carry growing satellite material next to `USE-CASE.md`
(Documentation Standards §2.1) — `operations/` already does this for each operation's own condition space. A
fixture needs to be distinctly and separately referenceable the way those already are, which an inline appendix
section cannot give it without inventing a second addressing scheme; a sibling file, referenced by section
anchor, gets that for free from the indexing this repo already has. Nothing here requires one file per fixture — fixtures that are naturally
cohesive (a command's rendered reports, say) may share a document, sectioned, so long as each fixture resolves
to its own addressable heading.
