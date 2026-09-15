# Use Case Operation Template

## Context
* [Documentation Standards](../standards/documentation-standards.md) - the document shape (Context, numbered
  sections, Rationale/Appendix) this template follows, and §2.1's directory-per-entity pattern this document
  uses
* [Use Cases §2.1](../workflows/feature-workflow/use-cases.md) - a Step Contract's `STATES` field, which this
  document is the target of
* [Operation Fixtures](../workflows/feature-workflow/operation-fixtures.md) - the rules and conventions this
  template's own shape follows; read that before writing one of these, not just this template
* [Architect Solution](../workflows/feature-workflow/architect-solution.md) - what this document feeds, and
  where the boundary it hypothesises is actually decided
* [Operation Template](OPERATION-TEMPLATE.md) - a Service's own design-side operation document; a different
  artefact at a different layer, named similarly by coincidence of vocabulary rather than by relation

Template for one use-case operation's condition space, filed at
`docs/analysis/use-cases/{use-case-slug}/operations/{N}-{operation-slug}.md`, where `N` is the step number —
one document per operation, never per step-shape, since an operation's own Extensions are cells of the same
condition space, not operations of their own. The template itself is in the Appendix below, since it's
reference material to copy from, not indexed content in its own right.

This document carries no methodology — no explanation of what a dimension, an invariant, or a route to a
value *is*, why rank order is chosen, why immaterial pruning matters, or what makes a cell an extension. All
of that is [Operation Fixtures](../workflows/feature-workflow/operation-fixtures.md)'s job, stated once. What
belongs here is only why *this* operation's dimensions are these — the judgement calls specific to it.

# Appendix

```
# {N} — {Operation Title}

The condition space of [{use-case-slug}](../USE-CASE.md) step {N}, and the states bracketing it: the
dimensions its fixtures must expose, the invariants they must witness, and the cells they combine into, each
with the state it establishes and the fixture exposing it.

## Context
* [{use-case-slug}](../USE-CASE.md) - the use case whose step {N} this analyses
* {links to the fixture sets this document references}
* {links to any standard the outputs must satisfy}

## 1 Dimensions

Every dimension of this operation's condition space, in rank order.

| Rank | Dimension | Category |
|---|---|---|
| {rank} | `{dimension-slug}` | payload · dependency · parameter |

{one line: which dimension(s) are ranked outermost, and why — which values end the operation earliest}

## 2 Payload States

What varies in the operation's own input. Projection: `given`.

### 2.{n} `{dimension-slug}`

{what this axis varies over, and any rule for how its values partition it}

| Ordinal | Value | Means |
|---|---|---|

### {n} {What Is Not A Dimension — optional}

{a condition deliberately left unmodelled as an axis: a route to a value, an invariant, or a constant every
fixture carries regardless — and why}

## 3 Dependency States

What the operation's own dependencies present it with. Projection: `given`.

### 3.{n} `{dimension-slug}` — same shape as §2

## 4 Parameter Options

The knobs of the invocation itself — same entry state, different call. Projection: `when`.

### 4.{n} `{dimension-slug}` — same shape as §2

## 5 Invariants

{each invariant names the cell that witnesses it, as an `@`-prefixed cell reference, and that cell names the
invariant back in §6}

| Invariant | The rule it defines | Witnessed by |
|---|---|---|
| **{invariant name}** | {the rule, in terms a reader can check} | @`{cell-id}` — [`{fixture-set}.{fixture}`]({link}), and how it shows |

## 6 Output Fixtures

{the condition space as a tree, nested in rank order — see Operation Fixtures for the notation. A node's own
label is plain; only a *reference* to a cell takes the `@` sigil}

* {N} - **{dimension}:** `{value}`
  > **Establishes:** …
  >
  > **Witnesses:** *{invariant name}* (§5), and how
  >
  > **Payload:** [`{fixture-set}.{fixture}`]({link})
  >
  > **{dependency-slug}:** [`{fixture-set}.{fixture}`]({link})
  >
  > **Result:** [`{fixture-set}.{fixture}`]({link})
  >
  > **Stdout:** `{fixture-set}.{fixture}` — [txt]({link}) · [json]({link})
  >
  > **Stderr:** [`{fixture-set}.{fixture}`]({link})

{one line per role, and one line per dependency named for the dependency it states — at most one of each, so a
leaf's own coverage reads off the block at a glance rather than having to be counted}

{pruned nodes shown at their own position in the tree, with their reason}

## 7 Coverage

{confirm every value of every dimension is still exposed by a kept leaf, naming the leaf as @`{cell-id}` so the
claim can be checked by eye against §6 rather than taken on trust; name whatever the enumeration itself found
that the use case's narrative had not — a route rather than a dimension, a leaf nobody had anticipated, and
so on}

# Rationale

{why this operation's dimensions are these, and any other judgement call specific to it — not the rules
Operation Fixtures already states}
```

# Rationale

**Why this document carries no methodology of its own.** A methodology sentence repeated in every operation
document is either right, in which case it belongs stated once, or it drifts between documents until it
isn't — and it crowds out the thing the document actually exists to show a reader: why *these* dimensions,
for *this* operation. Anything explaining why a document of this kind is shaped the way it is belongs to the
standard; anything explaining why this operation's condition space looks the way it does belongs here. The
test is mechanical enough to apply while writing: a sentence that would be true of every operation document
is methodology, and doesn't belong in one.

**Why one document per operation, not per step-shape.** A step's Extensions are different outcomes of the
same operation reached under different conditions — the whole point of enumerating a condition space is one
place where every input condition an operation actually faces is visible together. Splitting that across a
document per Extension would scatter the dimensions the Extensions share and make it impossible to see
whether the space is actually covered.

**Why the template names three category headings (Payload/Dependency/Parameter States) rather than one flat
list.** The three read differently even before any dimension is assigned to them — a payload dimension is
something the input varies, a dependency dimension is something the operation's own dependency presents it
with, a parameter dimension is a knob on the invocation with the entry state held fixed — and separating them
under [projection](../workflows/feature-workflow/use-cases.md#21-step-contracts) (`given` for the first two,
`when` for the third) is what keeps "same entry state, different invocation" from reading as a second entry
state with nowhere formal to sit.

**Why this template shares vocabulary — dimension, projection, rank — with the design-side [Operation
Template](OPERATION-TEMPLATE.md) without being the same artefact.** Both describe a condition space over an
operation, at different layers: this one is Analysis's own, solution-independent; the design-side one is a
Service's own operation, bound to real types and a real signature. The overlap in vocabulary is not
coincidence — a design's own dimensions are frequently a mechanical read of an operation document's — but the
two remain genuinely separate documents, owned by different people at different points in the process, and
neither is derived from the other by this ticket's own scope.
