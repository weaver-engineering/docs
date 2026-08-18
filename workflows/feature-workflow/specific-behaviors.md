# Specific Behaviors

## Context
* [Feature Workflow](feature-workflow.md) - the workflow step (`2 Design The Feature`) this document is an output of, and step `3 Chunk The Design`, which scopes each Chunk around delivering one or more of these
* [Design Feature Instructions](design-feature-instructions.md) - the process that produces and checks this document's content; this document defines the shape, that one defines how to get there
* [Design Directory And HLD](design-directory-and-hld.md) - Internal Components and External Dependencies, whose declared `calls:` this document's traced call trees walk, and where `IC-000`'s top-level functions and their pseudocode live
* [Use Cases](use-cases.md) - the Main Success Scenario steps and Extensions a specific behavior walks through
* [Use Case Template §Technical Interpretation](../../templates/USE-CASE-TEMPLATE.md) - the pseudocode appendix a use case grows during Design, that §2.1 below is built from
* [Specific Behavior Template](../../templates/SPECIFIC-BEHAVIOR-TEMPLATE.md) - the fill-in-the-blank shape of an SB document
* [Documentation Standards](../../standards/documentation-standards.md) - the document shape and `§M.N` cross-reference syntax this convention builds on

## 1 What A Specific Behavior Is

A specific behavior is a concrete, testable, system-level condition, defined entirely by:

* the state of the external dependencies involved, before the interaction;
* the interaction with the system itself — what actually crosses the system's boundary; and
* the expected final state, expressed as the resulting external-dependency interactions and/or the system's own
  response.

"Concrete" means literal example values, not variables or abstract descriptions — a user named `john` holding
JWT `abcd`, not "an authenticated user." A specific behavior is written so that an end-to-end test can be built
directly from it: seed the Given, perform the When, assert the Then. External Dependencies are the only
interface that test is permitted to mock (see [Design Directory And HLD §3](design-directory-and-hld.md)) —
every Internal Component runs for real.

This deliberately excludes anything about how the system is built internally — a specific behavior says nothing
about which of the Feature's own components are involved or how they collaborate. That's not an omission: it's
what keeps a specific behavior testable purely from outside, regardless of implementation. The internal
counterpart — the components a design decides to build, and the interfaces between them — is [Internal
Components](design-directory-and-hld.md), which lives in the HLD. §2 is how the two connect: a specific
behavior's Given and Then aren't invented directly, they're read off a traced call tree through the project's
own call graph.

A specific behavior's When is always exactly one *operation* — one invocation of one of the system's own
top-level functions (see §2.4). A use case's actor may cross that boundary more than once to reach their goal;
each crossing is its own specific behavior, never several crossings folded under one When (§3). And a single
operation, reached under different conditions, is very often several specific behaviors, not one (§2.5).

A specific behavior is never about one external dependency's capability in isolation. Authentication, for
example, is not itself a specific behavior — no use case's goal is "the system authenticates a user" on its own.
Instead, authentication is a precondition and an interaction that appears inside the Given/Then of every specific
behavior that actually needs it (see the worked example in the Appendix). The same pattern repeats for any
dependency: it is exercised only as part of a real, goal-directed interaction, never on its own.

## 2 Deriving A Specific Behavior From A Use Case

Deriving specific behaviors isn't a per-behavior recipe — it's a pipeline run once for the whole Feature, over
every use case in scope together:

1. **Technical Interpretation** (§2.1) — crystallize each use case's own steps into pseudocode, independent of
   any solution.
2. **Gap analysis** (§2.2) — read all of it together; find what already exists and what's genuinely new.
3. **Solution shape** (§2.3) — design the new Internal Components the gap actually calls for.
4. **Operations** (§2.4) — identify every distinct invocation of the system's own top-level interface each use
   case requires.
5. **Many specific behaviors per operation** (§2.5) — entry-state variation and unhappy paths, both multiply one
   operation into several specific behaviors.
6. **Call trees** (§2.6, §2.7) — for each specific behavior, trace and reconcile the tree of Internal Component
   and External Dependency calls that realize it.
7. **Design review** (§2.8, §2.9) — verify the design still actually satisfies what was crystallized in step 1,
   and that a human genuinely understands and agrees with the specific behaviors that resulted.

A specific behavior's Given/When/Then (§4) is read off the reconciled tree from step 6, not authored
independently of it.

### 2.1 Technical Interpretation

Before any solution is considered, each use case in scope gets pseudocode: its Main Success Scenario steps and
Extensions, rewritten in technical language, but naming no Internal Component, no External Dependency, no call
tree — the design's own crystallization of what the use case actually requires, independent of how it ends up
being delivered. This lives in the use case's own `# Appendix`, as `## Technical Interpretation` (see the [Use
Case Template](../../templates/USE-CASE-TEMPLATE.md)), not in the design directory — it needs to sit next to the
narrative it reflects, and it's expected to stay valid across design iterations even as the chosen solution
around it changes (§2.8) — it is never edited to reflect whatever solution gets chosen; doing that would be
editing the use case's own requirement, not a normal part of Design (see Design Feature Instructions §4.3). Once
Operations (§2.4) identifies which `SB-NNN` document(s) each of a use case's operations causes to exist, the
Technical Interpretation links out to them — the reverse of each `SB-NNN` document's own Realizes (§3, §4), which
links back to the use case.

### 2.2 Gap Analysis

Read every use case's Technical Interpretation for the Feature as a single body of pseudocode, and ask what it
actually needs: which existing [Internal Components](design-directory-and-hld.md) already cover parts of it,
and which External Dependency interfaces are required — expressed in the Feature's own terms, as a thin shim
(design-directory-and-hld.md §3), not the dependency's native surface. Some of a Feature's External Dependency
needs are indirect, inherited from reusing an existing Internal Component that itself already depends on one.
This step's output is the shape of the existing system and exactly what it's missing — not yet a solution, an
inventory: which External Dependency operations are reused unchanged, and which are new. Once every gap this
inventory surfaces is closed (§2.3), each piece this step matched — a call, a branch, or occasionally a whole
Technical Interpretation — is substituted into the operation's own bound pseudocode (Design Feature Instructions
§4.3), recorded on its `SB-NNN` document.

### 2.3 Solution Shape

With the gap known, design the new Internal Components the Feature actually needs — this is ordinary HLD work
(Solution Overview, Key Decisions; see [Design Directory And HLD §2](design-directory-and-hld.md)), not repeated
here. Doing it after the gap analysis rather than before means the shape being designed responds to a known,
concrete gap instead of guessing at one.

### 2.4 Operations

An operation is one invocation of one of `IC-000`'s own functions — the system's own top-level interface (see
[Design Directory And HLD §4.4](design-directory-and-hld.md)). A use case's actor may cross the system's boundary
more than once on the way to their goal (an elicitation dialogue is several round-trips, not one); each crossing
is a distinct operation. For each use case in scope, identify every operation its steps require — some already
exist as `IC-000` functions from earlier Features, some are new.

### 2.5 Many Specific Behaviors Per Operation

An operation rarely produces just one specific behavior. Two distinct sources multiply it, and every specific
behavior that results still belongs in the same `SB-NNN` document — the one for this use case's use of this
operation (§3) — not filed separately by source:

**Entry-state variation.** The same operation, reached with different valid inputs, can produce different
expected outcomes — a big widget and a small widget may both be saved by the same operation, but they're still
two distinct business requirements, each deserving its own specific behavior, even where (as here) they turn
out to route through the exact same call tree (§2.6). Some entry-state variations are exactly what exercises one
of the use case's own Extensions rather than its base happy path.

**Unhappy paths.** "The system must behave gracefully at all times" is a blanket requirement this pipeline
doesn't derive from any single use case — it's layered on top, and it surfaces unhappy-path specific behaviors
at three different points, as three different things become knowable:

* From the use case itself, at Technical Interpretation (§2.1): an Extension may already describe a specific,
  anticipated failure — "the database is unavailable" — with its own defined reaction. Knowable before any
  solution exists.
* From the chosen solution, once Operations (§2.4) identifies which real External Dependencies are actually
  involved: failure modes the use case's own authors had no way to anticipate, because they didn't know which
  technology would be chosen.
* From the pseudocode itself, once it's written against real Internal Components and External Dependencies: a
  purely logical exceptional condition — a divide by zero, an invariant that doesn't hold — with nothing to do
  with any dependency failing at all.

Every one of these, once identified, gets its own specific behavior defining what graceful behavior actually
means in that circumstance.

These permutations are not flat. Where several specific behaviors share every Given condition but one — the same
account lookup with a different user, the same failure with one more dependency also down — that relationship is
itself worth recording, not just the fact that each is individually valid. §4.1 sets out the nested numbering
and inheritance convention that captures it.

### 2.6 The Call Graph Is Data; A Call Tree Is A Walk Through It

A function's `calls:` defines what *could* happen — every address it might, under some circumstance, invoke. A
specific behavior's call tree defines what *actually* happens — the one walk through that space a given
operation takes, as a consequence of that use case's own needs. Logically, a project has exactly one call graph
— every `IC-NNN` function's `calls:` declaration is one set of edges in it, and every specific behavior's call
tree is a walk through some part of it. Drawing that whole graph as a single diagram would be too large to read;
a specific behavior doesn't need the whole graph, only the one tree relevant to its own scenario. So the graph
itself is never drawn in full. It exists only as the sum of every function's own `calls:` declaration (see the
[Internal Component Template](../../templates/INTERNAL-COMPONENT-TEMPLATE.md)), and each specific behavior
states just its own tree through it.

A call is a tree, not a list: when a function calls another, control returns to the caller once the callee is
done, and the caller may then call something else — a sibling of the first call, not something the first call's
own target called. A flat list can't say that without reading as if each address called the next one in the
list. A call tree is a required, nested YAML structure of addresses — `IC-NNN §M` or `ED-NNN §M.N` — rooted at
one of `IC-000`'s own functions, each node's `children:` listing what it called, in call order, ending wherever
a node has no children. An External Dependency operation is always a leaf (§3 of Design Directory And HLD: it
never declares `calls:`), but a leaf isn't always an External Dependency — an Internal Component function that
does its own work without calling anything further (data transformed before being persisted, say) is just as
valid a leaf, it just happens to be an `IC-NNN` address instead of an `ED-NNN` one:

```yaml
call_tree:
  address: "IC-000 §1"
  children:
    - address: "IC-004 §1"
      children:
        - address: "ED-001 §1"
    - address: "IC-004 §2"
      children:
        - address: "ED-002 §1"
```

This says: the entry point (`IC-000 §1`) calls `IC-004 §1`, which calls `ED-001 §1` and returns; the entry point
then separately calls `IC-004 §2` (a sibling of the first call, not something `IC-004 §1` itself called), which
calls `ED-002 §1` and returns. A flat list of the same four addresses would have been ambiguous about exactly
that — whether `IC-004 §2` was called by the entry point or by `ED-001 §1`.

Ordinarily, each node's address appears in the `calls:` list declared by its parent — `IC-004 §1`'s own document
lists `ED-001 §1` among what it calls. Where it doesn't, see §2.7: that's not automatically an error in the
tree.

There is no diagram here. A rendered sequence diagram describes a function's own *expected* behavior in general
— it belongs on that function's own `IC-NNN` document (see the [Internal Component
Template](../../templates/INTERNAL-COMPONENT-TEMPLATE.md)), not on a specific behavior's call tree, which
records what *actually* happened for one concrete scenario instead. The two aren't different renderings of the
same thing.

A call tree lives with the specific behavior it derived, embedded directly beneath that behavior's Given/When/Then
(§4) in its `SB-NNN` file.

### 2.7 Two Independent Views, Reconciled By Review

A function's `calls:` and a specific behavior's call tree are not the same fact recorded twice — they're two
independent views of it, formed two different ways:

* **`calls:`** is bottom-up: whoever defines a function declares, from that function's own Purpose, what it
  *could* call. It's formed once, at the function's own definition, not re-derived for every use case that
  reaches it.
* **A call tree** is top-down: whoever derives a specific behavior determines what a use case's own scenario
  *actually* causes to happen, reasoning from each candidate function's stated Purpose — not by copying that
  function's own `calls:` list.

Making `calls:` mechanically authoritative — deterministic from the function's actual logic — would mean
capturing that logic in the design, which is far more than a design should carry (see `# Rationale`). So the two
views are independently formed, and won't always agree on a first pass. A node present in a tree but missing
from its parent's `calls:` doesn't mean the tree is wrong; it might mean the `calls:` declaration is incomplete.
Either way, the disagreement itself is the useful signal — it's exactly what systematic design review exists to
catch and resolve, by updating whichever view turns out to be wrong.

### 2.8 Design Review: Pseudocode-To-Pseudocode Comparison

Design review checks the design against what it's actually meant to satisfy by comparing pseudocode to
pseudocode, the same shape of check §3.4 of [Design Directory And HLD](design-directory-and-hld.md) runs for
External Dependency shims: a use case's Technical Interpretation (§2.1) against the `SB-NNN`'s own bound
pseudocode (Design Feature Instructions §4.3) for that use case — the record of exactly which function was
substituted for each call, branch, or whole body the Technical Interpretation named. The two are maintained
independently — Technical Interpretation stays fixed as the use case's own requirement, the bound pseudocode is
re-derived whenever something it depends on changes (Design Feature Instructions §7.1) — so this comparison is
what actually confirms the design still does what the use case needs, rather than assuming it because the two
haven't been looked at side by side recently. Where a `SB-NNN` document's Realizes (§3) names more than one use
case, this comparison runs against every one of them, not just whichever use case happened to create the
document first.

### 2.9 Human Review Of The Specific Behaviors Themselves

Everything from §2.1 through §2.8 is systematic — but systematic isn't the same as correct. A fine detail gets
missed in the pseudocode, a misunderstanding survives the reconciliation in §2.7, and the process itself has no
way to catch that it's producing something subtly wrong; only a human checking the actual result against their
own understanding of the use case can. The specific behaviors that fall out of this pipeline are likely the
clearest, most human-readable statement of the system's actual behavior anywhere in the design — clearer than
the pseudocode they were derived from, clearer than the call trees underneath them. That's exactly what makes
the human review of the specific behaviors themselves the critical checkpoint in this process, not a formality
after the real work is done: the reviewing architect has to actually read and understand each one and assert
that it's genuinely what the use case requires, not trust that a systematic process got there on its own.

This matters beyond the review itself. These specific behaviors are what a Chunk's failing tests are built from
([Feature Workflow](feature-workflow.md)'s `Chunk Design` step) — they're the concrete definition of "done" a
later PR is judged against. An architect who doesn't genuinely understand a specific behavior at this stage
inherits the same gap at every PR built against it, with far less context each time to notice something is
wrong.

## 3 The SB-NNN Convention

`SB-NNN` is its own independent, project-scoped numbering sequence — the same as `ED-NNN` and `IC-NNN` are — not
tied to `UC-NNN`. A single `SB-NNN` document is scoped to one use case's use of one operation (§2.4): every
specific behavior inside it, the entry-state variations and unhappy paths alike (§2.5), shares the same base
use-case step(s), just under different conditions. It's filed at
`docs/design/{feature-slug}/specific-behaviors/SB-NNN-{slug}.md` in the depending project's own docs repo,
produced from the [Specific Behavior Template](../../templates/SPECIFIC-BEHAVIOR-TEMPLATE.md).

Where two different use cases drive the same operation with the exact same entry state — genuinely identical
Given, not merely similar — they share one `SB-NNN` document rather than each getting their own: the document's
Realizes (§4) is extended to name both use cases' relevant steps, each with its own bound pseudocode (Design
Feature Instructions §4.3), not duplicated into a second document. This is what lets design review's
pseudocode-to-pseudocode comparison (§2.8) check every relying use case's own Technical Interpretation against
its own bound pseudocode, not just the one that happened to create the document first. Short of that exact
coincidence, reuse of an operation is not a reason to skip deriving a new specific behavior: a function correctly
saving a big widget doesn't establish that it correctly saves a small one, even if today it's the exact same code
path — different entry states are different business requirements that only happen to share an implementation,
and only a specific behavior for each keeps that regression-resilient once they might not.

Each use case's Technical Interpretation links out to the `SB-NNN` document(s) each of its operations causes to
exist (§2.1) — the reverse of the `SB-NNN` document's own Realizes, which links back to the use case(s) it
realizes.

## 4 The Given/When/Then Format

An `SB-NNN` document opens with a document-level **Realizes**: the base use-case step(s) this operation covers,
for every use case that shares it (§3), each paired with that use case's own bound pseudocode for the operation
(Design Feature Instructions §4.3) — stated once, before any individual specific behavior. Each numbered section
that follows is then one specific behavior:

* **Realizes** — which variation of the document-level Realizes this behavior is: the happy path, or a named
  unhappy path (§2.5), of either the base steps or a named Extension. Not a restatement of the full step list —
  that's already declared once, at the document level.
* **Given** — the concrete, pre-interaction state of every external dependency involved for this variation, each
  named and citing the specific interaction it relies on in its ED document (see [Design Directory And
  HLD](design-directory-and-hld.md) §3) as `ED-NNN §M.N`, with literal example values.
* **When** — the one operation (§2.4) that starts this behavior's walk: a specific call or request, with
  concrete parameters, not a restatement of the use case's actor-level trigger. The same When recurs across
  every specific behavior in the document — what varies between them is the Given and, downstream, the Then.
* **Then** — the concrete external-dependency interactions and/or final state that result for this variation:
  what got called, with what, returning what, and what the system itself returns or emits.
* **Call Tree** — the traced call tree (§2.6) that derived this behavior, required, embedded directly beneath
  the Then.

### 4.1 Nested Numbering For Input-Condition Permutations

The permutations §2.5 describes aren't flat. A behavior numbered `{N}.{M}` is a permutation of its immediate
parent `{N}`: it changes one input condition (occasionally more) and inherits everything else. Its Given, When,
Then, and Call Tree may each be stated as "As §{N}, but {the actual delta}" instead of restated in full — a
reader assembles the complete picture by reading the parent first, the same way a numbered use-case Extension
(`2a`, `4c`) is only ever read together with the step it branches from. Nesting can go deeper than one level
(`{N}.{M}.{L}`) wherever a further permutation changes just one more condition again, each level inheriting
everything its own parent didn't override.

The nesting is a heading depth, not just a number: `{N}` is `##`, `{N}.{M}` is `###`, `{N}.{M}.{L}` is `####`,
and so on — one level deeper per `.`-separated segment beyond the first. A fresh top-level id always resets back
to `##`. This is what lets a reader (or a mechanical script) tell a permutation from a sibling by structure
alone, without parsing the number itself.

A fresh top-level number, not a nested one, is for a genuinely different branch — a different named Extension, a
different unhappy-path source (§2.5), or a change substantial enough that "as §N, but..." would end up restating
almost everything anyway. The test is whether the relationship is actually "the same scenario, with one thing
different" or merely "these two behaviors happen to share some values" — nesting exists to record the former,
not to force an artificial parent onto the latter.

See the [Specific Behavior Template](../../templates/SPECIFIC-BEHAVIOR-TEMPLATE.md) for the literal shape, and
the Appendix below for a worked example.

## 5 Relationship To Chunking

The `Chunk Design` step of the [Feature Workflow](feature-workflow.md) scopes each Chunk around delivering at
least one specific behavior, such that end-to-end testing can verify the Chunk adds that behavior to the
project. Citing a behavior by its address — `SB-NNN §M` — is what makes a Chunk's dependencies on other Chunks
mechanically derivable later, the same way `UC-NNN` links already make a use case's dependencies recoverable by
scanning its own references.

# Appendix

Worked example — `SB-014`, one operation (`GET /user/account`) realizing steps 2-3 of `UC-101`, with a happy
path, a nested permutation of it, and one unhappy specific behavior:

````
# SB-014 — View Own Account

**Realizes:** UC-101 steps 2-3

**Bound Pseudocode (UC-101):**

```
FUNCTION IC-000 §1:
  identity <-- [ED-001 §1: resolve_session - bearer_token]
    ON FAILURE (unavailable): RETURN 503, no body
  IF identity IS NOT authenticated:
    RETURN 401
  account <-- [ED-002 §1: find_account - identity.user_id]
    ON FAILURE (unavailable): RETURN 503, no body
  RETURN 200, account
```

## 1 Happy Path

**Realizes:** happy path

**Given** a user `john` holding JWT `abcd`, and `ED-002 §1` (Accounts Mongo) holding a document `johnAccountDoc`
keyed by `john`

**When** `john` sends `GET /user/account` with header `Authorization: Bearer abcd`

**Then** `ED-001 §1` (Auth Service) is called with `abcd` and returns `authenticated` for `john`; `ED-002 §1` is
queried for `john` and returns `johnAccountDoc`; the API responds `200` with body `johnAccountDoc`

**Call Tree**

```yaml
call_tree:
  address: "IC-000 §1"  # GET /user/account
  children:
    - address: "IC-004 §1"  # session: resolve(abcd)
      children:
        - address: "ED-001 §1"  # auth service: verify(abcd) -> authenticated(john)
    - address: "IC-004 §2"  # session: find(john)
      children:
        - address: "ED-002 §1"  # accounts mongo: find(john) -> johnAccountDoc
```

### 1.1 A Different Account Holder

**Realizes:** happy path

**Given** — as §1, but a user `jane` holding JWT `wxyz`, and `ED-002 §1` holding a document `janeAccountDoc`
keyed by `jane`

**When** — as §1, but `jane` sends the request with header `Authorization: Bearer wxyz`

**Then** — as §1, but `ED-001 §1` returns `authenticated` for `jane`; `ED-002 §1` returns `janeAccountDoc`; the
API responds `200` with body `janeAccountDoc`

**Call Tree** — as §1 (identical shape; only the leaf values differ)

## 2 Accounts Store Unavailable

**Realizes:** unhappy path — `ED-002` (Accounts Mongo) unavailable

**Given** a user `john` holding JWT `abcd`, and `ED-002 §1` (Accounts Mongo) unreachable

**When** `john` sends `GET /user/account` with header `Authorization: Bearer abcd`

**Then** `ED-001 §1` (Auth Service) is called with `abcd` and returns `authenticated` for `john`; `ED-002 §1` is
queried for `john` and raises a connection error; the API responds `503` with no body

**Call Tree**

```yaml
call_tree:
  address: "IC-000 §1"  # GET /user/account
  children:
    - address: "IC-004 §1"  # session: resolve(abcd)
      children:
        - address: "ED-001 §1"  # auth service: verify(abcd) -> authenticated(john)
    - address: "IC-004 §2"  # session: find(john)
      children:
        - address: "ED-002 §1"  # accounts mongo: find(john) -> connection error
```
````

The document-level bound pseudocode is `IC-000 §1`'s own logic with `UC-101`'s abstract calls already substituted
— `resolve_session` bound to `ED-001 §1`, `find_account` bound to `ED-002 §1` — recorded once, per Design Feature
Instructions §4.3, rather than re-derived by every behavior below it. `§1.1` is a permutation of `§1`, not a
sibling — its own heading is one level deeper (`###`, not `##`) precisely because it's nested under `§1` rather
than beside it: same call tree shape, same failure-free path through the bound pseudocode, only the concrete
account holder differs, so its Given/When/Then say only what's different and its Call Tree isn't restated at
all. `§2` is
a fresh top-level number because it isn't a permutation of `§1`'s input at all — it's a different path through
the same bound pseudocode (the `ED-002` call's `ON FAILURE` branch instead of its success path), which is exactly
the kind of change too substantial for "as §1, but..." to say cleanly. All three specific behaviors share
`SB-014`'s document-level Realizes (steps 2-3 of `UC-101`) and the same When — the same operation,
`GET /user/account`. Authentication appears in every one of them purely as a precondition inside Given/Then,
never as a specific behavior of its own: the same `ED-001 §1` interaction would appear again, with different
concrete values, in every other specific behavior anywhere in the design that requires an authenticated caller.

# Rationale

**Why `SB-NNN` is its own independent numbering sequence rather than mirroring `UC-NNN`.** An earlier version of
this convention tied `SB-NNN` to `UC-NNN` directly, on the theory that a specific behavior always belonged to
exactly one use case. That stopped holding once specific behaviors turned out to be keyed to operations, not use
cases: a single use case can require several operations (many `SB-NNN` per `UC-NNN`), and — since two use cases
can legitimately share an operation and entry state (§3) — a single `SB-NNN` document can end up realizing more
than one use case at once. Neither direction is a clean 1:1 that a shared number could express; `ED-NNN` and
`IC-NNN` already establish the pattern for an artifact whose identity doesn't derive from any other document's
own numbering, so `SB-NNN` follows it instead.

**Why Given/Then must use literal example values, not abstract descriptions.** The entire point of a specific
behavior is that it is mechanically testable — an end-to-end test is built directly from it by seeding the Given
and asserting the Then. "An authenticated user" cannot be seeded; `john` holding JWT `abcd` can be. Abstract
phrasing would also erase the distinction this document exists to make: that specific behaviors are the *design*
made concrete, not a restatement of the use case's own, deliberately technology-agnostic, narrative.

**Why Technical Interpretation comes before any solution work, not alongside it.** Sketching Internal Components
and External Dependencies at the same time as crystallizing what a use case actually requires risks the
crystallization quietly bending to fit whatever solution is already in mind, rather than the solution being
shaped by the actual requirement. Pure, dependency-free pseudocode first is what makes the later gap analysis
(§2.2) an honest comparison against a fixed target, and what makes design review's pseudocode-to-pseudocode
check (§2.8) meaningful — comparing a solution against a requirement that was itself derived from the solution
would not catch anything.

**Why the document-level Realizes carries bound pseudocode, not just the step-range pointer.** "`UC-101` steps
2-3" says which requirement an operation covers; it says nothing about how the design actually satisfies it. Without
recording the substitution itself — which function stood in for which call, branch, or whole body — every later
step would have to re-derive it: deriving a Then (§5) would need to re-walk the Technical Interpretation against
the current design from scratch, and design review (§2.8) would have nothing concrete to checksum. Recording the
bound pseudocode once, alongside the pointer it was derived from, is what lets both of those be a lookup instead
of a fresh derivation.

**Why nested numbering, not a flat list of every permutation.** A specific behavior that differs from another
only in which user is logged in is still, correctly, its own specific behavior (§3) — reuse of a call tree
doesn't collapse two different business requirements into one. But writing each one out in full, every time, as
if it shared nothing with its nearest relative buries the one fact that actually matters for a reader trying to
understand the shape of an operation's behavior: which conditions were varied deliberately, and which just came
along for the ride because they happened to be part of the same Given. Nesting numbers and inheriting everything
a child doesn't explicitly override keeps that relationship visible instead of forcing a reader to diff two full
behaviors by eye to discover it.

**Why nesting is a heading depth, not just a number.** An earlier version of this convention numbered a
permutation (`§1.1`) without changing its heading level — still `##`, same as its parent `§1`. That leaves the
nesting relationship expressed only in the number itself, recoverable by a human reading carefully but not by
anything that parses document structure rather than text — including the very tools this whole process exists
to make possible (see Design Feature Instructions §5.1, which writes these headings as placeholders a mechanical
script later has to read back). Making depth track nesting means the structure is visible two ways at once,
redundantly rather than relying on one fragile source of truth.

**Why unhappy paths are identified at three different points instead of all at once.** "Behave gracefully at
all times" sounds like a single requirement, but what it takes to satisfy it isn't knowable all at once: a
use-case-anticipated failure is knowable from the use case alone, before any solution exists; a real dependency's
actual failure modes aren't knowable until Operations (§2.4) picks one; a purely logical exceptional condition
isn't knowable until the pseudocode is written against real components. Demanding all unhappy paths be
identified in one pass would mean pretending to know things about a solution that doesn't exist yet, or leaving
genuinely solution-specific failure modes undiscovered because the pass that could have found them already
happened.

**Why the graph is never drawn whole.** There is logically one call graph per project — every function's
`calls:` is a fragment of the same graph, and every specific behavior's call tree is a walk through it. Rendering
the whole thing as a single diagram would be accurate but useless past a handful of functions: too large to
read, and mostly irrelevant to whichever one scenario a reviewer actually cares about at a time. Keeping the
graph as distributed data (§2.6) and only ever recording the one tree a specific behavior needs avoids that
without losing anything — the full graph is still recoverable, by tooling or by hand, as the union of every
function's declaration, the same way this repo's own `.index/` files (documentation-standards.md §4) are never
one global index but are still assemblable into one.

**Why a call tree, not a list.** A real invocation returns control to its caller before that caller (maybe)
calls something else. A flat list of addresses can't represent that without looking like each call's target
called the next address in the list, which is often simply false — see the worked example, where a flat
rendering of `IC-000 §1, IC-004 §1, ED-001 §1, IC-004 §2, ED-002 §1` would misleadingly read as `ED-001 §1`
calling `IC-004 §2`. Nesting `children:` records exactly who called whom.

**Why a call tree is required but a rendered diagram never appears on a specific behavior.** Once `calls:`
exists as data, redrawing the same edges as a fresh diagram for every specific behavior that happens to touch
them would be pure duplication — the request/response detail already lives on the function's own document, and
a hand-drawn diagram can silently drift from it. More fundamentally, a specific behavior's tree and a function's
own diagram describe different things: the tree is what *actually happened* in one concrete scenario; a
function's diagram, if it has one, is what that function is *expected to do* in general. Putting a diagram on a
specific behavior would conflate the two. The tree is the minimal fact worth recording directly, on the
behavior; a diagram, when one is worth drawing, belongs on the function it describes instead (see the [Internal
Component Template](../../templates/INTERNAL-COMPONENT-TEMPLATE.md)).

**Why a call tree and its nodes' `calls:` are two independent views rather than one fact checked against itself
(§2.7).** Making `calls:` deterministic from what a function actually does would mean capturing that function's
real logic in the design — every branch, every condition — which is a specification of the implementation, not
a design of it, and belongs in the Chunk's own code once it's built, not in a document meant to stay "clear, to
the point" ([Feature Workflow](feature-workflow.md) §2). Treating `calls:` as a declared expectation instead,
and a call tree as an independently traced determination, keeps both cheap to write while still giving something
to check them against each other with. A mismatch is real information either way — either the function's own
document is missing something, or the tree claims a call the function's design was never actually asked to
support — and systematic design review is what decides which, not an automated pass/fail gate standing in for a
judgment call the design stage genuinely can't make on its own.

**Why "coverage" isn't this document's concern.** An earlier version of this document treated "every use case
step has a realizing specific behavior" and "every internal component function is reached by some call tree" as
things design review should mechanically check and a design document should explain how to check. Both are real
properties worth being true, but checking that they hold is a repeatable, mechanical audit — exactly the kind of
job that belongs with the guardrails that keep a coding agent honest against system-level tests once a Chunk is
being built (see [Design Directory And HLD's own Rationale](design-directory-and-hld.md) on the same point for
"used by" lists), not with the design documents themselves. This document's job is to produce specific behaviors
correctly derived from the pipeline in §2; auditing that nothing was skipped is a separate, later concern.
