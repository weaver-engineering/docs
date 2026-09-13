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
* [Required Behaviors](../workflows/feature-workflow/required-behaviors.md) - what this document's Step
  Contracts and operations turn into, and the `behaviors/` subdirectory they're derived into

Template for a single use case, filed as `docs/analysis/use-cases/{use-case-slug}/USE-CASE.md` — the
directory-per-entity pattern, since a use case always grows a `behaviors/` subdirectory alongside it (one file
per operation, holding that operation's Required Product Behaviours), and commonly grows a `fixtures/`
subdirectory alongside it too, holding the concrete fixtures its Step Contracts reference (Use Cases §2.1). A use
case is addressed by its own slug, never a numeric id. The template itself is in the Appendix below, since it's
reference material to copy from, not indexed content in its own right.

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
§2.1) — the boundary it's perceived to cross, the state it assumes, and the state it establishes, each witnessed
by a fixture:

N. {Step name}
    **Boundary:** {the boundary this operation is perceived to cross}
    **GIVEN:** {the state this operation assumes} [{fixture}](fixtures/{file}.md#{section})
    {narrative description of the step}
    **THEN:** {the state this operation establishes} [{fixture}](fixtures/{file}.md#{section})

A step that is pure branching or narrative — nothing crossing a boundary — carries none of these; the state in
force is whatever the preceding contract established. A use case may be drafted with plain narrative steps and no
Step Contracts at all; Boundary/GIVEN/THEN are added in a second pass, once the steps crossing a boundary are
identified (Use Cases §2.1). While adding Step Contracts, note for each operation whether it defers to an
existing capability or needs its own inline spec (Analysing A Feature §4); once identified, Required Behaviors
folds it into `behaviors/{operation-slug}.md` — link each operation to its own behaviour file here.

A step that relies on another use case's functionality references it inline, by id, at that step, as an actual
markdown link to that use case's own directory — e.g. "...generates the section index
([find-a-path](../use-cases/find-a-path/USE-CASE.md))...". This is the only place dependencies are declared: no
separate "Depends On" summary field. Once the word-indexer exists, a document's dependency set is mechanically
recoverable anyway, by scanning its own word index for use-case-slug links outside `## Context` — so a bespoke
field would just be a second place for the same information to drift out of sync with the actual steps.}

## 5 Postconditions

{observable end state once the main scenario completes — what Feature-level
reconciliation ultimately checks the chained Required Product Behaviours
against, see Required Behaviors §3}

## 6 Extensions

{numbered-step branches, Cockburn-style: `<step><letter>` — e.g. `2a`, `4c` —
each naming the branching condition and what happens instead. A branch step that performs an operation carries
its own Step Contract the same way a main-scenario step does, with its GIVEN inherited from whichever step it
branches off (Use Cases §2.1) rather than from the full main-scenario chain.}

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
wanted on its own; Boundary/GIVEN/THEN add the state and boundary detail that later derivation and architecting
need, but adding them is a distinct, later act of analysis, not a precondition for the narrative itself being
finished. Requiring Step Contracts up front would force every use case through a heavier first draft than its
goal always warrants.

**Why fixtures live in their own `fixtures/` subdirectory rather than back in this document.** The directory a
use case occupies exists specifically so it can carry growing satellite material next to `USE-CASE.md`
(Documentation Standards §2.1) — `behaviors/` already does this for Required Product Behaviours. A fixture needs
to be distinctly and separately referenceable the way a behaviour file already is, which an inline appendix
section cannot give it without inventing a second addressing scheme; a sibling file, referenced by section
anchor, gets that for free from the indexing this repo already has. Nothing here requires one file per fixture —
fixtures that are naturally cohesive (a command's CLI output and log lines, say) may share a document, sectioned,
so long as each fixture resolves to its own addressable heading.
