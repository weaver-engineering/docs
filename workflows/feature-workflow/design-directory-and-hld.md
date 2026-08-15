# The Design Directory And HLD

## Context
* [Feature Workflow](feature-workflow.md) - the workflow step (`2 Design The Feature`) this document defines the output of
* [Use Cases](use-cases.md) - the analysis-step artifact a design translates into concrete decisions
* [Required Behaviors](required-behaviors.md) - the broader analysis output a design must satisfy
* [Specific Behaviors](specific-behaviors.md) - the Given/When/Then behaviors a design identifies; this document defines where they come from, that one defines their format
* [HLD Template](../../templates/HLD-TEMPLATE.md) - the fill-in-the-blank shape of the HLD itself
* [External Dependency Template](../../templates/EXTERNAL-DEPENDENCY-TEMPLATE.md) - the fill-in-the-blank shape of an ED document
* [Internal Component Template](../../templates/INTERNAL-COMPONENT-TEMPLATE.md) - the fill-in-the-blank shape of an IC document
* [Documentation Standards](../../standards/documentation-standards.md) - the document shape and directory structure this convention builds on

## 1 Design Directory Location And Naming

The design directory lives at `docs/design/{feature-slug}/` in the project's own `<project>-docs` repo — `docs/design/`
is already part of the minimum directory structure every such repo has. Its entry point is
`{feature-slug}-hld.md`, the high-level design document for the Feature.

Other supporting documents and sub-folders may exist inside the design directory — a data model, a sequence
diagram, a longer discussion of one component — but every one of them **must** be reachable by following links
starting from the HLD (§5). A supporting document that nothing links to is not part of the design; it does not
satisfy this convention regardless of where it sits on disk.

## 2 The HLD

The HLD is a single Markdown document following the [Documentation Standards](../../standards/documentation-standards.md)'
shape (frontmatter, `## Context`, numbered body, optional `# Appendix`/`# Rationale`), produced from the
[HLD Template](../../templates/HLD-TEMPLATE.md). Its numbered body **must** cover:

1. **Scope** — what this design covers, linking to every [use case](use-cases.md) it realizes, and what it
   explicitly excludes.
2. **Solution Overview** — a concrete map, not prose: which components this design introduces or modifies, one
   line on each one's responsibility, and what each talks to (other components, external dependencies). A
   reader orients here before going anywhere else in the document — see §4 for why this can't just be the
   specific behaviors restated.
3. **Key Decisions** — see §2.1. Covers both Open Design Questions and, for every component listed in Solution
   Overview, the interface decision behind it.
4. **Data Types** — the shapes of data the Feature introduces or consumes, concrete enough for [specific
   behaviors](specific-behaviors.md) to instantiate with example values.
5. **Internal Components** — see §4.
6. **External Dependencies** — see §3.
7. **Specific Behaviors** — links to the [SB-NNN](specific-behaviors.md) file(s) this design produces, one per
   source use case (§6).
8. **Technology Stack** — languages, frameworks, and libraries the design commits to.

### 2.1 Key Decisions Must Resolve Open Design Questions

Every use case in scope (§1's link list) may carry its own `## 7 Open Design Questions` — things it deliberately
left undecided. The HLD's Key Decisions section **must** address every one of them, either:

* resolved, stated normatively, with the justification in `# Rationale` per the documentation standard's own
  split between fact and justification; or
* explicitly deferred, with a stated reason it's out of scope for this Feature.

An open design question with no corresponding entry in Key Decisions is not a complete design — it is a design
that hasn't finished being designed yet, whether or not that fact is obvious from a casual read.

## 3 External Dependencies

An external dependency is a system whose interactions the design depends on but **cannot unit-test** — its real
behavior can only be observed by actually calling it: a network boundary, real time, real randomness, another
team's own data. This is a narrower bar than "outside our control." A third-party *library* linked directly into
the Feature's own code is not an external dependency in this sense, however little the design controls its
source — it runs in-process, its calls are ordinary function calls, and it can be exercised in a unit test like
any other code path. What makes something external, here, is that a real test of it needs the real thing (or a
stand-in for it end-to-end), not merely that the project didn't write it.

Because the raw interface a real dependency offers is usually far larger than what this project actually needs
— a message queue's full API, a REST API with dozens of endpoints — an ED document doesn't restate that whole
surface. It describes a thin shim: the narrow, system-specific interface this project actually calls, translated
one-for-one from what the dependency natively offers. If an event system supports many event kinds and this
project only ever raises one, linearly translated from its own data, the shim is no more than
`postNewWidgetEvent(widget)` — not the event system's whole native API. The shim's own interface is what an
ED-NNN document's numbered operations describe (§3.1), and it's also the only interface a specific behavior is
permitted to mock (§3.3) — an in-process library dependency, not being external by this section's own
definition, is exercised for real in a unit test instead.

### 3.1 Where External Dependency Documents Live

Each depending project documents the external dependencies it uses under its own `docs/architecture/external-dependencies/`
— `docs/architecture/` is already part of the minimum directory structure every `<project>-docs` repo has. Each
dependency gets its own file, `ED-NNN-{slug}.md`, produced from the [External Dependency
Template](../../templates/EXTERNAL-DEPENDENCY-TEMPLATE.md). Its numbered body defines each interaction or
operation the dependency's *shim* offers as its own numbered section — a concrete request/response contract,
error modes included — so that any later document can cite one specific interaction via the documentation
standard's `§M.N` cross-reference syntax rather than the whole ED document.

### 3.2 Ownership Is Per-Project, Not Per-Dependency

An ED document is owned by the project that wrote it — there's no cross-project ownership question to resolve,
because an ED document was never a description of the external system's own API in the first place; it's a
description of *this project's own shim* over it (§3). Two projects that both depend on the same third-party
system — both use Kafka, say — inevitably end up with two, unrelated ED documents, because their shims are
unrelated: System A's Kafka shim might expose `postNewWidgetEvent`; System B's has nothing to do with widgets
and never will. That was never duplication to be weighed as a tradeoff — it's simply two different projects each
documenting their own narrow use of a shared technology, which happen to have nothing in common beyond the name
of the thing underneath.

Within one project, before writing a new ED document, a design checks whether its own project already has a
shim for that dependency; if so, and the new use case needs a new operation from it, the shim is extended in
place (a new interaction added as a new numbered section) rather than a second, competing shim invented for the
same dependency — the same "update in place" rule
@agent-plugins-docs/docs/analysis/use-cases/UC-001-discuss-concept-and-document.md already applies to concept
documents.

### 3.3 Referencing An External Dependency

The HLD's External Dependencies section names each dependency the design uses and links to its ED document. A
[specific behavior](specific-behaviors.md)'s Given/Then goes further, citing the exact interaction it relies on —
`ED-NNN §M.N` — with concrete example values, not the ED document's abstract contract restated in prose. This is
also the only boundary a specific behavior's own test is permitted to mock (§3): everything else in a call tree
— every Internal Component function — runs for real when a specific behavior is exercised end to end.

### 3.4 Each Operation Lists The Use Cases That Rely On It

Every ED operation's own section names every use case that depends on it — not just links in, but an explicit
"used by" list the operation's own author maintains. The reason is different from why an Internal Component
function tracks its usages (§4.5): here, it's what lets design review check that the shim is still honestly
thin. Line up every use case's actual usage of the same shimmed operation side by side, and a shim that's
quietly stopped being thin — one use case needing subtly more than the shim's simple, linear translation can
honestly give it — becomes visible as a real discrepancy between usages, rather than staying hidden inside a
shim that reads as simple from any one use case's point of view alone.

## 4 Internal Components

A [specific behavior](specific-behaviors.md) is deliberately black-box: its Given/When/Then is defined only by
external-dependency state and interaction at the system's own boundary, never by anything internal to how the
Feature is built. That's correct — it's what makes a specific behavior testable without caring how it's
implemented — but it also means specific behaviors carry no information at all about how the Feature's own code
is decomposed. Left there, an agent implementing one Chunk has only the specific behaviors it was handed and no
shared blueprint for how its piece should fit with what other Chunks are building. An internal component is how
the design supplies that blueprint.

An internal component is a part of the Feature's own solution — a service, module, or internal interface — whose
contract the design deliberately decides, as distinct from an external dependency's contract, which the design
merely observes. Not every implementation detail is an internal component: something only earns a component
document if its interface is genuinely a boundary — used by more than one Chunk, or likely to be depended on or
extended by a future Feature. A detail private to a single Chunk stays undocumented at this level; that's
ordinary engineering judgment for whoever implements the Chunk, not an architectural fact worth pinning down.

### 4.1 Where Internal Component Documents Live

Each project documents its own internal components under `docs/architecture/components/`, a sibling to
`docs/architecture/external-dependencies/`. Each component gets its own file, `IC-NNN-{slug}.md`, produced from
the [Internal Component Template](../../templates/INTERNAL-COMPONENT-TEMPLATE.md), with the same numbered,
`§M.N`-referenceable interaction shape as an ED document — component documents are standing, project-level
facts for the same reason external dependency documents are (§3.2's ownership rule applies here too): a
component this Feature introduces may be exactly what a later, unrelated Feature needs to extend or call.

Each function also declares, on itself, every address it may call (`calls:`, a small YAML block — see the
[Internal Component Template](../../templates/INTERNAL-COMPONENT-TEMPLATE.md)). There is no separate call-graph
document: the project's whole call graph is just the union of every function's own declaration, assembled by
reading them rather than maintained as a second copy — see [Specific Behaviors §2.6](specific-behaviors.md).

### 4.2 Deciding An Interface Versus Observing One

An ED document and an IC document look alike, but they're written from opposite directions. An ED document
records a fact about a system the project doesn't control — get it right, and there's nothing to justify. An IC
document records a decision the design made about a system the project does control — the interface itself is
one of this HLD's Key Decisions (§2.1), and the reasoning behind shaping it that way belongs in the IC document's
own `# Rationale`, not just the HLD's.

### 4.3 What Earns Its Own Document

Either of two triggers is enough on its own:

* **Used by more than one Chunk within this Feature.** Observable once Chunking happens — a component two
  Chunks both call is, definitionally, a boundary.
* **Likely to be reused by a future Feature.** The harder call, because nothing observable proves it yet — it's
  a bet on where the project is going, not a fact Chunking will confirm or deny. It's also the one most easily
  missed: a component built for a single Chunk today gives no local signal that it's about to matter elsewhere,
  so there's nothing that forces the question the way a second Chunk's dependency does. This is a judgment call
  for whoever is designing, to make explicitly and record in Key Decisions (§2.1) when the component is
  introduced — not something to leave implicit for an agent to infer, and not something to wait on until a
  second Feature forces a retrofit.

If either call turns out wrong once Chunking happens (a component assumed private turns out to be shared, or
vice versa), the design is revised, the same as any other Key Decision would be if Chunking exposed a gap in it.

### 4.4 The System's Own Entry Point Is Always IC-000

The Feature's own external interface — what a caller outside the system actually invokes, an HTTP endpoint, a
CLI command, a queue consumer — is not a separate kind of document. It's an `IC-NNN` document like any other,
reserved at the fixed number `IC-000` rather than identified only by its `Kind` — so a reader always knows where
to find it without checking every component's `Kind` field first. An operation (§4.5, [Specific Behaviors
§2.4](specific-behaviors.md)) is, by definition, one invocation of one of `IC-000`'s own functions — the root of
every specific behavior's traced call tree is always one of them.

### 4.5 Top-Level Functions Carry Their Own Pseudocode And Usage Lists

`IC-000`'s functions carry more than the request/response contract every `IC-NNN` function has (§4.1). Each one
also carries:

* its own pseudocode, describing the currently designed solution for that operation, linked to the specific
  Internal Component and External Dependency calls it makes — this is the "current designed solution" half of
  the pair described in [Specific Behaviors §2.1](specific-behaviors.md), which changes across design
  iterations, as distinct from a use case's own Technical Interpretation, which doesn't;
* a list of every use case step that relies on it;
* a list of every specific behavior that exercises it (see [Specific Behaviors §3](specific-behaviors.md) on
  when reusing an existing, unchanged function still gets a new specific behavior — whenever the entry state
  differs, which is most of the time — versus when it's added to an existing one instead, on the rare occasion
  two use cases coincide on the exact same entry state).

Both lists exist for the same reason §3.4 requires one on every ED operation, applied here instead to what the
system itself is expected to do: a top-level function may be relied on by several different use cases, each
possibly demanding something slightly different of it, and its own pseudocode has to actually satisfy all of
them at once — the lists are what makes that checkable at design review, by the same kind of pseudocode-to-
pseudocode comparison §3.4 uses for shims (see [Specific Behaviors §2.8](specific-behaviors.md)). Only `IC-000`'s
own functions carry this — an internal, non-entry-point function is reached only through `IC-000`'s own
functions and the call trees that lead to it, which is enough traceability on its own.

## 5 Navigability

Every file under the design directory — the HLD and every supporting document or sub-folder — must be reachable
by following links starting from the HLD. A reviewer, human or agent, starts at `{feature-slug}-hld.md` and can
reach the entire design without being told where anything else lives. This is what "a comprehensive and
consistent vision of the solution" ([Feature Workflow](feature-workflow.md) §2) means structurally, not just as
an aspiration: nothing in the design directory is discoverable only by knowing it's there.

## 6 From The HLD To Specific Behaviors

The HLD's Key Decisions (§2.1) are what turn each linked use case's actor-level Main Success Scenario steps and
Extensions into concrete, technology-specific interactions — a chosen interface, a chosen data shape, a chosen
external dependency. That concrete version of a step, expressed as a testable Given/When/Then, is a [specific
behavior](specific-behaviors.md). This document stops at "the HLD identifies which specific behaviors exist and
links to them"; their own format, and the process that derives them, is defined in [Specific
Behaviors](specific-behaviors.md), not repeated here.

# Appendix

Worked example of a design directory's shape (contents illustrative, not literal filenames):

```
docs/design/user-account-view/
  user-account-view-hld.md
  specific-behaviors/
    SB-101-view-own-account.md
    SB-104-handle-expired-session.md

docs/architecture/external-dependencies/
  ED-001-auth-service.md
  ED-002-accounts-mongo.md

docs/architecture/components/
  IC-000-user-account-api.md
  IC-001-session-cache.md
```

`ED-001`, `ED-002`, `IC-000`, and `IC-001` sit under `docs/architecture/`, not under the `user-account-view`
design directory, because they are standing facts about the project — dependencies it observes, or components
it has built and may reuse — not facts scoped to this one Feature. A later, unrelated Feature that also needs
the auth service, or that wants to reuse the session cache `IC-001` introduced, extends the relevant document in
place rather than creating its own copy within its own design directory. `IC-000` is the project's single,
standing entry point — it doesn't get a new number for every Feature; a later Feature that adds a new endpoint
extends `IC-000` with a new numbered function instead.

# Rationale

**Why `docs/architecture/` rather than the design directory itself.** An early version of this convention
considered filing external dependency contracts inside each Feature's own design directory, since that's where
they're first needed. That breaks down as soon as a second Feature needs the same dependency: either it
duplicates the contract inside its own design directory (drift risk, no single place to update when the real
system's interface changes) or it reaches across into another Feature's design directory for a fact that isn't
really about that Feature at all. `docs/architecture/` already exists in every project's docs repo for exactly
this kind of project-level structural fact, so external dependencies belong there instead. The same argument
applies to internal components once a design decides one: the decision is this Feature's, but the resulting
contract stops being Feature-scoped the moment a second Feature might reuse it.

**Why "duplication" across projects turned out not to be a real problem.** An earlier version of this document
treated a second project also depending on, say, Kafka as accepted, deliberate duplication — a tradeoff to live
with rather than solve, on the theory that the "real" definition of a shared third-party dependency arguably
belongs above any single project. That framing didn't survive §3's thin-shim definition: an ED document was
never a description of the third-party system itself, only of this project's own narrow, translated use of it.
Two projects' shims over the same underlying technology aren't two copies of the same content — they're
unrelated content that happens to name the same dependency. There was nothing to accept a tradeoff over, and no
future shared resource to design toward if drift ever "became a problem," because there is no shared content to
drift.

**Why Key Decisions must enumerate every Open Design Question explicitly.** The point of a design being
*reviewable* is that the architect can audit it without a close read of the whole document — a checklist against
each linked use case's §7, not a search for anything that might have been missed. Requiring every open question
to show up, resolved or explicitly deferred, turns that audit into a mechanical check instead of a judgment
call about whether the design document happened to be thorough.

**Why Internal Components exist as a concept distinct from External Dependencies, rather than folding both into
one document type with a `Kind` field.** The two are written from opposite directions — an ED document records
an observed fact, an IC document records a decision — and that difference has to show up structurally, not just
as a value in a field. Merge them into one type and nothing stops an ED document quietly drifting into inventing
a decision the design has no authority to make (the design cannot decide what the real Auth Service's API looks
like), or an IC document skipping the justification a real decision actually owes, on the mistaken assumption
that documenting either kind of fact is all any entry here ever needs. Separate templates keep that distinction
visible to whoever is filling one in, rather than relying on them to remember it.

**Why not every internal detail becomes an Internal Component.** Documenting every module boundary a Feature
happens to introduce would turn `docs/architecture/` into a second, competing description of the codebase that
drifts from the actual code the moment anyone refactors a Chunk-private detail — exactly the kind of ongoing
maintenance burden a project's own source and tests are better suited to reflect accurately. Restricting IC
documents to interfaces that actually cross a Chunk or Feature boundary keeps `docs/architecture/` limited to
facts that genuinely need to outlive the Chunk that first created them.

**Why `IC-000` is a fixed, reserved number rather than just a `Kind` value.** An earlier version of this
convention identified the entry point only by its `Kind` field, the same as every other component — technically
sufficient, since a reader could always check. But the entry point is where every call tree in the design
directory starts; making it findable by a fixed address rather than a linear search through every component's
`Kind` is worth the one special case, the same way a fixed convention (`README`, `index.html`) earns its keep in
other systems for exactly this reason.

**Why "used by" lists (§3.4, §4.5) exist but a mechanical check that nothing's missing from them doesn't live
here.** The lists themselves are design output — required content on the operation or function's own document,
the same as its request/response contract is. Whether every use case that should be listed actually is, though,
is a completeness check, and completeness checks belong with the guardrails that keep a coding agent honest
against system-level tests once a Chunk is actually being built, not with the design documents themselves. An
earlier version of this document framed a function with no recorded usage as a "design smell" for design review
to go looking for — that conflated two different jobs: writing the list (design's job, done once, per function,
as it's introduced or extended) and auditing that no list anywhere is missing an entry (a mechanical, repeatable
check better run by tooling every time the design changes, not something a human reviewer re-derives by hand at
each review).
