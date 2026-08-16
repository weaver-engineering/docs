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

# Appendix

````
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

```yaml
reconciliation:
  checked_at: null  # set once Design Feature Instructions §7.1 first passes
  uc_technical_interpretation_checksums: {}  # "UC-{NNN}": "{checksum}", one per relying use case named above
  function_checksums: {}  # "IC-000 §{M}": "{checksum}", one per function/prose the bound pseudocode and call trees below reach
  reviewed_by: null  # set once §7.2 human review completes
  reviewed_at: null
```

## 1 {Happy Path, Or Named Entry-State Variation}

**Realizes:** {which variation this is — "happy path", or "happy path (extension {M}{letter})" if this entry state exercises a UC Extension instead of the base steps}

**Given** {the concrete, pre-interaction state of every external dependency involved for this variation — literal example values, citing `ED-NNN §M.N`}

**When** {the concrete interaction at the system's boundary that starts this operation — a specific call or request, with concrete parameters; the same When recurs for every behavior in this document}

**Then** {the concrete external-dependency interactions and/or final state that result for this variation}

**Call Tree**

```yaml
call_tree:
  address: "IC-000 §{M}"  # entry point
  children:
    - address: "IC-{NNN} §{M}"
      children:
        - address: "ED-{NNN} §{M.N}"
```

## 1.1 {Permutation Of §1 — name the one condition that changed}

**Realizes:** {same variation as §1, unless this permutation itself crosses into a named Extension}

**Given** — as §1, but {only the condition(s) that actually differ}

**When** — as §1 {, but {the delta}, if the trigger itself differs — otherwise omit this line entirely}

**Then** — as §1, but {only what actually differs in the outcome}

**Call Tree** — as §1 {, or the delta, if the shape of the tree itself changed — see Specific Behaviors §4.1 for
when a permutation stays nested versus when it needs a fresh top-level number instead}

## 2 {Named Unhappy Path}

**Realizes:** {"unhappy path — {what fails}" — see Specific Behaviors §2.5 for the three points at which an unhappy path becomes identifiable}

**Given** {as above, but with the failure condition present}

**When** {same When as §1, or the relevant extension's own trigger}

**Then** {what the system does in response to the failure — the "graceful" behavior being defined}

**Call Tree**

```yaml
call_tree:
  address: "IC-000 §{M}"
  children:
    - address: "ED-{NNN} §{M.N}"
```

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
diff two fully-restated behaviors by eye. See [Specific Behaviors
§4.1](../workflows/feature-workflow/specific-behaviors.md) for the full rule, including when a permutation is
substantial enough to need a fresh top-level number instead of a nested one.

**Why one document holds several behaviors, not just one.** An operation is rarely exercised only one way —
different valid entry states can exercise different use-case Extensions, and every one of those has its own
unhappy alternatives. All of them share the same base steps and, usually, the same When; only the Given and Then
actually vary between them. See [Specific Behaviors §2.5](../workflows/feature-workflow/specific-behaviors.md).

**Why `reconciliation:` is checksums, not a checkbox.** A checkbox only ever records that a check passed once;
it can't tell anyone whether it's still true. Recording the checksums a passing check depended on instead makes
the record falsifiable — recomputing and comparing them later is enough to know whether anything the check
actually relied on has changed since, without re-reading or re-reasoning about content that hasn't moved. See
[Design Feature Instructions §7.1](../workflows/feature-workflow/design-feature-instructions.md).

**Call Tree, not Call Chain.** A real invocation is a tree, not a list: a function calls children, each of
which returns control to its caller before that caller (maybe) calls a sibling. A flat list can't represent
"A calls B, B returns, A calls C" without reading like B calls C. `children:` nests to say exactly that — see
[Specific Behaviors §2.6](../workflows/feature-workflow/specific-behaviors.md).

**No diagram here.** A specific behavior's call tree records what *actually* happened for one concrete scenario;
a sequence diagram belongs on the function whose own expected behaviour it's describing, not on the specific
behavior that happened to reach it — see the [Internal Component
Template](INTERNAL-COMPONENT-TEMPLATE.md) and [Specific Behaviors §2.6](../workflows/feature-workflow/specific-behaviors.md).
