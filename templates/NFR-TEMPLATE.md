# NFR Template

## Context
* [Design Layout Standards §5.8](../standards/design-layout-standards.md) - the single starting document, and when it earns a directory
* [Boundary Template](BOUNDARY-TEMPLATE.md) - the functions a cross-cutting boundary selects

Template for `NFR.md`, and for the per-boundary documents it grows into. A design's NFR consideration starts as
**one document**, which does not exist until NFRs have been considered at all, and which may be as little as an
exemption per category. Once functions are selected onto a cross-cutting boundary, that boundary earns its own
document — a cross-cutting boundary is a boundary — so `NFR.md` gains a sibling and the pair occupies an `NFR/`
directory.

**The categories are not a closed set.** Security, resilience, concurrency and state-transaction are the ones
currently identified; others may be. Obliging every design to carry a document per category just to exempt
itself from each would be excessive, which is why one document with a section per category considered is the
starting shape.

Two things are deliberately separate here. An **NFR rule** is a standalone definition of what a constraint
requires; a **cross-cutting boundary** is a named association of a rule set with the functions it is imposed on.
Several boundaries may apply the same rule, which is why the rule is not owned by any of them.

**Every rule in scope is explicitly assessed.** A rule is either applied — a cross-cutting boundary declared,
selecting the functions it governs — or the design exempts itself, declaring that no function here is on that
boundary. There is no third state, and a rule in scope with neither is a finding.

**Below the cross-cutting boundary the directory partitions by rule**, and each rule holds the non-functional
behaviours its deviations demand, in `{rule-slug}/BEHAVIOURS.md`. An NFR is a deviation from nominal, so those
are exactly the cells where this rule's cross-cutting dimension sits off its nominal value — the functional
projection stays in the operation's own `BEHAVIOURS.md` ([Behaviours Template](BEHAVIOURS-TEMPLATE.md)).

A rule's view is **non-contiguous by design**: it is the view of the design's enforcement of that rule through
whichever path requires it, so it holds subtrees scattered across the space rather than one branch. Grouping this
way is what makes completeness and coverage readable per rule.

**It is keyed on operation.** A rule reaches every operation whose call tree touches a function it governs, so its
view draws cells from several condition spaces at once — and a cell id means nothing outside the operation that
owns it. The document is organised by operation first and cell second; a bare cell id in it would be ambiguous.
Like `BEHAVIOURS.md`, it is system-generated rather than hand-written ([Behaviours Template](BEHAVIOURS-TEMPLATE.md)).

Where a cell is off nominal on two rules at once, **both views state it**. What matters is that the behaviour is
recorded, not that it is recorded once: the values are identical, the fold is well defined, and the duplication
is structural rather than evidence the documents are drawn along the wrong lines.

**Rules inherit; selections never do.** A design inherits rule definitions from the Product that owns it and
from any containing design target, and must then decide locally which of its own functions each rule applies
to. Where a rule is inherited, this document holds provenance only — a link and a checksum. Where the design
defines the rule itself, the rule is stated in full.

A selector reaches down through contained boundaries and stops at any contained design target, which assesses
the same in-scope rules for itself.

# Appendix

````
---
_claims:
  - _target: bnd/{design-slug}/xc/{xc-slug}
    _sourcing: {kind: elicited, basis: "§2"}
    slug: {xc-slug}
    kind: {security | resilience | concurrency | state-transaction}
    applies:
      - nfr/{rule-slug}
    selects:
      - bnd/{design-slug}/bnd/{boundary-slug}/fn/{function-slug}
  - _target: nfr/{locally-defined-rule-slug}
    _sourcing: {kind: elicited, basis: "§3.1"}
    slug: {locally-defined-rule-slug}
    kind: {security | resilience | concurrency | state-transaction}
    description: §3.1
    contributesDimensions:
      - slug: {dimension-slug}
        kind: cross-cutting
        projection: {given | when}
        values:
          - {slug: {value-slug}, ordinal: 1}
          - {slug: {value-slug}, ordinal: 2}
---
# {Security | Resilience | Concurrency | State Transactions}

## Context
* [{the design entry document}](../{design-slug}.md)
* {link to the Product's NFR rule catalog}

## 1 Rules In Scope

{every rule of this kind the design is in scope for, and what the design has done about each. A rule with no
row here is a rule nobody assessed.}

| Rule | Origin | Assessment |
| :--- | :--- | :--- |
| `{rule-slug}` | inherited — {link} | applied by §2 |
| `{rule-slug}` | inherited — {link} | **exempt** — see §4 |
| `{locally-defined-rule-slug}` | defined here — §3.1 | applied by §2 |

## 2 {xc-slug}

{the cross-cutting boundary: which rules it imposes, and which functions it imposes them on. A design may
declare several of one category — distinct boundaries applying different rule sets to different function sets —
and each earns its own document, since a cross-cutting boundary is a boundary.}

**Applies** `{rule-slug}`

**Selects**

| Function | Why it is on this boundary |
| :--- | :--- |
| `bnd/{boundary-slug}/fn/{function-slug}` | {what crosses here that the rule constrains} |

## 3 Rules Defined Here

{present only where this design defines a rule of its own, which is then in scope for anything it contains}

### 3.1 {locally-defined-rule-slug}

{the constraint in the architect's words}

**Contributes dimensions** — {the cross-cutting dimensions this rule adds to the condition space of every
operation reaching a function it governs, with their values}

**Contributes required effects** — {what must happen at the cells selecting those values}

## 4 Exemptions

{present only where the design exempts itself from a rule in scope. An exemption is a claim, not a silence: it
is reviewable, and falsifiable later when it stops being true.}

| Rule | Why no function here is on this boundary |
| :--- | :--- |
| `{rule-slug}` | {the reason} |
````

# Rationale

A rule's definition and the association applying it are anchored to different sections because the same rule
genuinely applies in several places — a service's auth rule and a promoted domain's auth rule are one
requirement imposed on two function sets. Fusing them would mean copying the rule's content per place it
applies, with nothing keeping the copies in step.

The rules-in-scope table is a section of its own rather than being implied by what the rest of the document
happens to mention, because the whole value of the assessment is being able to assert that every rule was
considered. A rule that was never thought about and a rule that turned out not to apply look identical if the
document only records the ones that did.

Exemptions get their own section for the same reason. An exemption recorded as an absence cannot be reviewed,
and cannot be found later by a Product-level pass looking for exemptions that have stopped being true.
