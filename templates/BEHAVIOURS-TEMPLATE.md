# Behaviours Template

## Context
* [Design Layout Standards §5.3](../standards/design-layout-standards.md) - the condition space, how it accretes, and when it splits
* [Operation Template](OPERATION-TEMPLATE.md) - the operation this condition space belongs to
* [Fixtures Template](FIXTURES-TEMPLATE.md) - the concrete state a condition value is realized by

**This is not a template to fill in. It is the shape the service emits.** `BEHAVIOURS.md` is always
system-generated ([Design Layout Standards §5.3.1](../standards/design-layout-standards.md)) — the one document
where the claims drive the prose rather than the prose being indexed by claims. What follows is therefore a
specification of what a generator produces, and a reference for reading the result.

`BEHAVIOURS.md` **is the spec**: it associates each named cell of an operation's condition space with its fixtures
and its required results, which is to say it defines what the failing tests must be, and so what the code an agent
writes must actually do. Every operation has exactly one condition space, and coverage is asserted against it — so
anything the space does not contain is, by definition, not required.

Three things go into generating it: the **structure**, written mechanically from the operation's dimensions and
their ranks; **prose still valid**, carried forward with its addresses updated; and **judgement prose**, physically
written by the service once the architect has approved what it says. Every behaviour is reviewed and approved by a
human at least once — that is the human in the loop, and the point at which a person takes responsibility for what
the code will be made to do.

A hand edit is not forbidden but it is invalidating: the claims are re-judged, and re-judgement asks more than
whether prose and frontmatter agree. A behaviour must reconcile with its space's dimensions and with its fixtures;
a cell that does not is **invalid** regardless of whether its prose and claim concur, and regardless of whether a
human approved it. An invalid behaviour is a finding, resolved by re-expressing or removing it.

Cell ids are **operation-specific** — `cell/1.2.3` of one operation is unrelated to `cell/1.2.3` of another — and
re-ranking a dimension is a rename, handled mechanically, carrying valid prose across and returning only genuinely
re-judged behaviours to the architect.

**This document holds the operation's *functional* behaviours — the nominal projection of the space.** Every
cross-cutting dimension has a nominal value coinciding with the functional path; a cell sitting at nominal on all
of them is functional and lives here, while a cell off nominal on any of them is a non-functional behaviour and
lives under `NFR/{xc-slug}/{rule-slug}/BEHAVIOURS.md` ([NFR Template](NFR-TEMPLATE.md)). Cross-cutting
dimensions therefore rank below the functional ones. Coverage is asserted over the whole space, so completeness
spans both locations.

The space is a matrix expressed as a tree. Dimensions are ranked, the tree nests them in rank order, and a cell
is one concrete combination of values, identified by the ordinals selected from the root down to it: `1.2.3`.
Behaviours attach to **leaves only** — a non-leaf cell's outcome is not determined by its own partial condition,
which is what having children means.

**The space accretes, and that is normal rather than a defect.** Payload and parameter dimensions come first,
from the operation's signature and input types. Cross-cutting dimensions arrive as each NFR rule's selector
resolves against real functions. Dependency-state dimensions arrive last, once the call tree reveals which
dependencies the operation actually reaches — they genuinely cannot exist earlier, because which dependencies an
operation touches is a consequence of the design rather than an input to it. Every added cell is uncovered until
either a behaviour occupies it or an inter-condition validity rule collapses it.

**Given and When are a projection, not a second structure.** A behaviour's Given and When are the cell's own
selected dimension values, filtered by each dimension's projection, so a behaviour cannot describe an entry
condition its cell does not.

**Call trees, expected effects and reconciliation appear in this document's frontmatter and are never
authored.** They are derived by tracing the design and carry provenance rather than sourcing. The required effects
beside them are **authored** — they carry sourcing, because they record the architect's judgement about what must
happen rather than a reading of what the design does. That the service writes both is irrelevant to which is
which: who types the characters has no bearing on whether a fact is derived, and conflating the two would collapse
the separation reconciliation depends on.

# Appendix

````
---
_claims:
  # Nothing structural is claimed here. The dimensions, their values, their ranks and any
  # explicit exclusion are all the operation's (OPERATION.md §5) — this document is a view
  # into the space they define, and states only what is required in it.
  - _target: bnd/{design-slug}/op/{operation-slug}/cell/1.1.2
    _sourcing: {kind: document, basis: "@{repo-slug}/docs/analysis/use-cases/UC-NNN-{slug}.md"}
    requiredEffects:
      - kind: {returns | raises | dependency-interaction | state-change | stream-output | exit-code | emits-metric}
        target: bnd/{design-slug}/op/{operation-slug}
        description: §3.1
        predicate: {field: status, operator: eq, value: pending}
    realizes:
      - "@{repo-slug}/docs/analysis/use-cases/UC-NNN-{slug}.md"
---
# {Operation Name} Behaviours

## Context
* [{the operation}](OPERATION.md)

## 1 Dimensions In Play

{a pointer, not a definition, and rendered rather than claimed — a view claims behaviours, never structure. The
dimensions, values and ranks are the operation's own; see OPERATION.md §5 for what they are, what they range over,
and how they were arrived at. Derived dimensions appear here too: the cross-cutting ones each rule contributed, and
the dependency-state ones the call tree revealed.}

| Rank | Dimension | Kind | Defined in |
| :--- | :--- | :--- | :--- |
| 1 | {dimension-slug} | payload | [OPERATION.md §5.1](OPERATION.md) |
| 2 | {dimension-slug} | parameter | [OPERATION.md §5.2](OPERATION.md) |
| 3 | {dimension-slug} | dependency-state | derived from the call tree |

## 2 {Operation Name} Behaviours

{One top-level section per operation this document covers, repeated. Operation is not a level of nesting, so every
operation's tree gets the full four levels beneath it.

Each nested section is one cell, titled with its dotted-decimal id and the value it selects, so the section
hierarchy *is* the condition tree and the Given accumulates as you descend. A leaf adds the When projection and
what is required there.

Rendered, not claimed: the cells and their nesting follow from the operation's dimensions and their ranks. An
architect's explicit exclusion is recorded with the space, in OPERATION.md §5.3.}

### 2.1 Given {dimension-slug} = {value-slug}

{rendered from the value — what this condition means}

#### 2.1.1 Given {dimension-slug} = {value-slug}

{rendered from the value}

##### 2.1.1.1 Given {dimension-slug} = {value-slug}, {dimension-slug} = {value-slug}

{consecutive dimensions contributing a single value each collapse into one section carrying all of their Givens —
a value may jump rank this way. Rare here, common in an NFR view.}

###### 2.1.1.1.1 When {dimension-slug} = {value-slug}, {dimension-slug} = {value-slug}

{the leaf. Given is everything accumulated above; When is the when-projected values, rendered together since the
split is a projection rather than a second structure.}

**Required effects**

| Kind | Target | Effect |
| :--- | :--- | :--- |
| returns | the operation | {what must happen, and its checkable predicate} |

**Realizes** {the use case step this satisfies, where one exists}

**Fixtures** {only the ones this behaviour uses — one for the payload, one for the output, one per dependency —
chosen from the cell's possible set rather than reproducing it. A cell whose possible set is empty is a
`no-suitable-fixtures` finding.}

#### 2.1.2 Given {dimension-slug} = {value-slug} — pruned

{why this combination does not exist. A validity rule on the input type makes it impossible: the branch is
removed and the tree narrows. The section exists so the absence is legible rather than silent.}

### 2.2 Given {dimension-slug} = {value-slug} — collapsed, leaf

{why nothing below this cell distinguishes anything further. A field's presence dependency makes the question
inapplicable: the subtree collapses and the tree shortens. Being a leaf, it carries a behaviour like any other.}

## 3 {Other Operation Name} Behaviours

{the next operation, with its own tree}

````

# Rationale

The condition space and the behaviours are one structure because they were never two. A separate tree followed by
a flat list of leaves states the nesting twice — once as a diagram and once as a set of ids — and nothing keeps the
two renderings in step. Making the section hierarchy *be* the tree removes the duplication, and the Given then
accumulates by containment rather than by being restated per leaf.

It also makes anchoring do the right thing for free. An anchor covers every section nested beneath it, so a claim
anchored at a non-leaf cell covers exactly the behaviours in that cell's subtree — the anchor's containment and the
cell's containment are the same containment. A claim at a leaf covers that behaviour alone, so re-judging one
behaviour does not disturb its siblings.

**Four levels is a readability ceiling, not a markdown one.** A document actively using more than about four levels
of indentation is unreadable however the indentation is achieved; markdown running out of headings at six is a
coincidence. Where a tree splits is declared by the operation's ranked dimensions rather than cut at a depth — an
arbitrary cut would rarely fall anywhere meaningful, and a high cut on the first one or two dimensions yields
coherent subtrees where a low cut yields fragments. The parent document then keeps the tree down to the break rank
and points at sub-documents from its deepest retained level.

Pruned and collapsed cells keep sections of their own rather than being omitted. An absent branch and a branch
nobody considered look identical if the document simply skips it, and the difference is what the coverage claim
rests on.

Given and When are shown as rendered rather than as fields to fill in, because storing them would create a
second copy of the cell's own values that can disagree with the condition space — and that disagreement is
precisely what makes a coverage claim meaningless.
