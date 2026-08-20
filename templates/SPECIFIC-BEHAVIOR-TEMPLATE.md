# Specific Behavior Template

## Context
* [Documentation Standards](../standards/documentation-standards.md) - the document shape (Context, numbered sections, Rationale/Appendix) this template follows
* [Specific Behaviors](../workflows/feature-workflow/specific-behaviors.md) - what a specific behavior is, the `SB-NNN` convention, and the Given/When/Then/Call Tree format each section below follows
* [Design Feature Instructions §7](../workflows/feature-workflow/design-feature-instructions.md) - the process that produces and checks the `reconciliation:` block below

Template for a specific-behaviors document, one file per (use case, operation) pair — one file per use case's use
of one operation (see Specific Behaviors §3), filed under `docs/design/{feature-slug}/specific-behaviors/` in
the project's own docs repo. `SB-NNN` is its own independent numbering sequence, not tied to `UC-NNN`. The
template itself is in the Appendix below, since it's reference material to copy from, not indexed content in its
own right.

A document built from this template passes through a real intermediate state, not just empty-stub and
fully-derived: once its outline of entry conditions is agreed (Design Feature Instructions §5.1), every numbered
section below exists with a real heading. A parent section — one with children nested beneath it — holds its
real Given directly, written as part of §5.1 itself, since that condition is already known and it has no
When/Then to placehold in the first place, and no call tree of its own; it carries no lifecycle marker. A leaf
section — one with no further nested condition beneath it (Specific Behaviors §4.1) — is a complete behavior,
and moves through a lifecycle of its own as work on it proceeds:

* **`//TODO`** — not yet derived; a bare placeholder in place of its Given/When/Then (§5.1's own exit state for
  every leaf). Its call tree doesn't exist yet either — there's no entry for it in frontmatter at all.
* **`//REVIEW`** — Given/When/Then derived and committed, its call tree written into frontmatter alongside it,
  awaiting confirmation: either §5.2's first derivation, or a fresh regeneration after `REDESIGN_REQUIRED`
  resolved (§7.2).
* **Approved** — confirmed correct. Not a prose marker: this behavior's own entry in the document's YAML
  frontmatter (`reconciliation.behaviors`, keyed by its id) carries its call tree, and a `reviewed` field naming
  who confirmed it and when. A leaf with no marker and a `reviewed` field set is approved; no marker and no
  `reviewed` field means nothing has looked at it yet in this pass.
* **`//REDESIGN_REQUIRED`** — approved once, but a dependency changed since and regenerating this behavior's
  expected result against the current design no longer matches what's recorded; carries the actual disconnect
  found, not just the fact one exists, so a cold session doesn't have to re-derive it. Always resolves back to
  `//REVIEW`, never straight back to approved — see Design Feature Instructions §7.2.

Three of these four states are prose, deliberately: human-visible and machine-searchable, the same convention as
the original `//TODO`. Only approval carries real weight (an identity, a timestamp, a call tree, checksums) and
lives in frontmatter instead — a document with no lifecycle markers left anywhere in its body reads as visually
complete to a human skimming it, which is exactly what the mechanical check (every leaf approved, every checksum
current) also confirms. A behavior's own prose section, in turn, only ever states what happens and why — Given,
When, Then — never the mechanical trace that produced it; that trace lives with the rest of its frontmatter
facts instead of scattered inline through the body. That mixed state is what makes the agreed outline itself
resumable from the document alone, without needing the approval conversation remembered.

# Appendix

````
---
reconciliation:
  checked_at: null  # set once Design Feature Instructions §7.1 first passes
  uc_technical_interpretation_checksums: {}  # "UC-{NNN}": "{checksum}", one per relying use case named above
  function_checksums: {}  # "IC-000 §{M}": "{checksum}", one per function/prose the bound pseudocode reaches
  behaviors:  # one entry per leaf behavior below (Specific Behaviors §4.1) — never for a parent, which has
              # neither a call tree nor a review state of its own
    "1.1":
      call_tree:  # required the moment this leaf leaves //TODO (Design Feature Instructions §5.2) — never
                  # abbreviated against a sibling or a parent, even when the shape is identical to one
        address: "IC-000 §{M}"  # entry point
        children:
          - address: "IC-{NNN} §{M}"
            children:
              - address: "ED-{NNN} §{M.N}"
      reviewed: null  # {reviewed_by, reviewed_at} once Design Feature Instructions §7.2 confirms it; removed
                       # entirely (not blanked) the moment a function checksum change invalidates it — a leaf
                       # with no reviewed field here needs §7.2, whether it never had one or just lost it
    "1.2":
      call_tree:
        address: "IC-000 §{M}"
        children:
          - address: "ED-{NNN} §{M.N}"
      reviewed: null
    "2":
      call_tree:
        address: "IC-000 §{M}"
        children:
          - address: "ED-{NNN} §{M.N}"
      reviewed: null
---
# SB-{NNN} — {Operation Title}

## Context
* [UC-{NNN}]({relative link to the use case this file realizes}) - the use case this file's behaviors realize {repeat this line per relying use case, if more than one shares this exact operation and entry state — see Specific Behaviors §3}
* Specific Behaviors (@docs/workflows/feature-workflow/specific-behaviors.md) - the convention this document follows

**Realizes:** {UC-{NNN} step(s) {M}[-{M+1}], the base steps every specific behavior below is a variation of}

**Bound Pseudocode (UC-{NNN}):** {repeat this block per relying use case named above — each use case's own Technical Interpretation slice, substituted per Design Feature Instructions §4.3}

```
{the use case's Technical Interpretation for step(s) {M}[-{M+1}], with each call, branch, or whole body replaced
by the function decided to satisfy it, in the bound form of Pseudocode Style §2 — `[{address}: {name} - {args}]`}
```

## 1 {Shared Entry Condition — Happy Path, Or Named Entry-State Variation}

**Realizes:** {which variation this is — "happy path", or "happy path (extension {M}{letter})" if this entry state exercises a UC Extension instead of the base steps}

**Given** {the concrete, pre-interaction state of every external dependency involved for this variation — literal example values, citing `ED-NNN §M.N`}

{If nothing further permutes this condition, §1 is a leaf: it's a complete behavior on its own, so continue
directly with its own **When** and **Then** here, in the same shape §1.1 uses below, and give it its own
`reconciliation.behaviors."1"` entry in frontmatter. If, as shown in this template, further permutations exist
beneath it (§1.1, §1.2, ...), §1 is a parent instead — the Given above is everything it states. It has no When
or Then of its own, and no frontmatter entry either: its outcome isn't determined until a child narrows it. See
Specific Behaviors §4.1.}

### 1.1 {First Permutation Of §1 — name the one condition that decides the outcome}

{heading depth tracks nesting depth, one `#` deeper per `.`-separated segment beyond the first — see Specific
Behaviors §4.1}

**Realizes:** {which variation this is — the happy path, or a named unhappy path (§2.5), of either the base
steps or a named Extension}

**Given** — as §1, but {only the condition(s) that actually differ}

**When** {the concrete interaction at the system's boundary that starts this operation — a specific call or
request, with concrete parameters; the same When recurs for every behavior in this document. Written in full
here since §1 itself has none to abbreviate against — see §1.2 below for the case where a sibling does}

**Then** {the concrete external-dependency interactions and/or final state that result for this variation}

### 1.2 {Second Permutation Of §1 — the alternative condition}

**Realizes:** {which variation this is, same shape as §1.1}

**Given** — as §1, but {the other condition(s) that differ}

**When** — as §1.1 {, but {the delta}, if the trigger itself differs — otherwise omit this line entirely; a
leaf abbreviates against a sibling that already has the field, never against a Given-only parent}

**Then** — as §1.1, but {only what actually differs in the outcome}

## 2 {Named Unhappy Path}

**Realizes:** {"unhappy path — {what fails}" — see Specific Behaviors §2.5 for the three points at which an unhappy path becomes identifiable}

**Given** {as above, but with the failure condition present}

**When** {same When as §1, or the relevant extension's own trigger}

**Then** {what the system does in response to the failure — the "graceful" behavior being defined}

## 3 {Next Behavior Title}

{a fresh top-level number for a genuinely different branch, or {N}.{M} nested under an existing one for a
further permutation of it — see Specific Behaviors §4.1}

{...}
````

# Rationale

Every individual behavior shares the same Realizes/Given/When/Then/Call Tree shape rather than varying by
behavior, because the point of a specific behavior is that it's mechanically consumable — a reviewer or a test
generator can rely on the shape being identical every time rather than parsing prose to find the relevant
clause. See [Specific Behaviors §1](../workflows/feature-workflow/specific-behaviors.md) for why literal example
values are required rather than abstract descriptions.

**Two levels of Realizes, and what the document level actually carries.** The document-level Realizes, stated
once, names the base use-case step(s) every behavior in this file is a variation of, and — one block per relying
use case — the bound pseudocode substituted for those steps (Design Feature Instructions §4.3). Each individual
behavior's own Realizes only records which variation it is — not the full step list again, which would just
repeat the document-level statement under every heading. See
[Specific Behaviors §3](../workflows/feature-workflow/specific-behaviors.md) and
[§4](../workflows/feature-workflow/specific-behaviors.md).

**Why `uc_technical_interpretation_checksums` is a map, not a single value.** A document-level Realizes can name
more than one relying use case (§3); each one has its own, independently-maintained Technical Interpretation and
its own bound-pseudocode block. A single checksum field would either only ever validate one of them or silently
conflate several use cases' content into one hash, either of which would let a change to any use case but the
first go unnoticed by reconciliation. Keying it the same way `function_checksums` already is keeps every relying
use case independently falsifiable.

**Nested numbering (§1.1) and the "as §N, but..." convention.** A behavior that shares every Given condition but
one with its nearest relative is still, correctly, its own specific behavior — but writing it out in full hides
the one thing worth knowing about it: which condition was actually varied. Numbering it as a child of the
behavior it permutes, and stating only the delta, keeps that relationship visible instead of asking a reader to
diff two fully-restated behaviors by eye. Its heading is one level deeper than its parent's (`###`, not `##`) —
depth tracks nesting, not just the number — so the relationship is visible in document structure, not only in
the id itself. See [Specific Behaviors §4.1](../workflows/feature-workflow/specific-behaviors.md) for the full
rule, including when a permutation is substantial enough to need a fresh top-level number instead of a nested
one.

**Why "as §N, but..." only ever abbreviates a field the cited section actually has.** Given always inherits
from the direct parent, since every node — leaf or parent — states one. When and Then don't exist on a parent
(see below), so a leaf directly under one has nothing there to abbreviate against and states them in full, the
same as any top-level behavior would; a later sibling leaf may still abbreviate against that first leaf instead,
once one full statement exists to point to. §1.1/§1.2 in this Appendix show both: §1.1 writes When/Then in full
because §1 is Given-only, and §1.2 then abbreviates against §1.1. This abbreviation is prose-only — a leaf's call
tree, in frontmatter, is never abbreviated against anything, sibling or parent, even where the shape is
identical (see "Why `reviewed` is a map keyed by behavior id," below, and Specific Behaviors §4.1).

**Why a parent section holds only a Given, written during §5.1 rather than placeholded for §5.2.** A parent
node's own outcome isn't determined yet — it's what its children each resolve differently — so it has no Then
to derive in the first place, no call tree to trace, and no frontmatter entry at all; its Given is already
exactly what the architect just approved when the outline itself was agreed. Placeholding it would invite
re-deciding something already settled; see [Specific Behaviors §4.1](../workflows/feature-workflow/specific-behaviors.md)
for the worked login/token example this follows.

**Why one document holds several behaviors, not just one.** An operation is rarely exercised only one way —
different valid entry states can exercise different use-case Extensions, and every one of those has its own
unhappy alternatives. All of them share the same base steps and, usually, the same When; only the Given and Then
actually vary between them. See [Specific Behaviors §2.5](../workflows/feature-workflow/specific-behaviors.md).

**Why `reconciliation:` is checksums, not a checkbox.** A checkbox only ever records that a check passed once;
it can't tell anyone whether it's still true. Recording the checksums a passing check depended on instead makes
the record falsifiable — recomputing and comparing them later is enough to know whether anything the check
actually relied on has changed since, without re-reading or re-reasoning about content that hasn't moved. See
[Design Feature Instructions §7.1](../workflows/feature-workflow/design-feature-instructions.md).

**Why `reconciliation.behaviors` is a map keyed by behavior id, not a single `reviewed_by`/`reviewed_at` pair for
the whole document.** A function change only invalidates the specific behaviors whose own recorded call tree
actually reaches it (Design Feature Instructions §7.2) — not every behavior that happens to share the same
`SB-NNN` document. A single document-level pair can't express "these three behaviors are still approved, this
fourth one just lost its approval" — it would force clearing (or keeping) approval for behaviors the change never
actually touched. Keying it by behavior id, the same shape `function_checksums` already uses, means invalidation
is exact instead of collateral.

**Why a behavior's own map entry carries its call tree, not just its review state.** Earlier drafts of this
template kept a behavior's call tree inline in its own prose section and only put review state in frontmatter —
splitting one behavior's mechanical facts across two places in the document for no reason other than history. A
call tree isn't narrative any more than a checksum or a reviewed-by field is; grouping all three under one
`behaviors."{id}"` entry means everything mechanical about a specific behavior is a single lookup, and a
behavior's own prose section is left stating only what happens and why.

**Why three of the four lifecycle states are prose markers and approval alone is frontmatter.** `//TODO`,
`//REVIEW`, and `//REDESIGN_REQUIRED` all mean "not done yet, and here's what's outstanding" — a human skimming
the document needs to see that at a glance, and a mechanical check needs to find it by search, so plain,
consistent prose serves both. Approval doesn't need to be seen in the body at all — once every marker is gone,
the document already reads as complete — but it does need to carry more than a boolean (who, when, against which
checksums), which prose isn't a natural fit for. Splitting by what each state actually needs to carry, rather
than forcing all four into one representation, is what keeps a fully-approved document visually clean instead of
cluttered with markers that have nothing left to say.

**Call Tree, not Call Chain.** A real invocation is a tree, not a list: a function calls children, each of
which returns control to its caller before that caller (maybe) calls a sibling. A flat list can't represent
"A calls B, B returns, A calls C" without reading like B calls C. `children:` nests to say exactly that — see
[Specific Behaviors §2.6](../workflows/feature-workflow/specific-behaviors.md).

**No diagram here.** A specific behavior's call tree records what *actually* happened for one concrete scenario;
a sequence diagram belongs on the function whose own expected behaviour it's describing, not on the specific
behavior that happened to reach it — see the [Internal Component
Template](INTERNAL-COMPONENT-TEMPLATE.md) and [Specific Behaviors §2.6](../workflows/feature-workflow/specific-behaviors.md).
