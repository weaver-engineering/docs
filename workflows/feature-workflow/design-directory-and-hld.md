# The Design Directory And HLD

## Context
* [Feature Workflow](feature-workflow.md) - the workflow steps (`Architect Services`, `Design Service`) this
  document defines the output of
* [Design Feature Instructions](design-feature-instructions.md) - the process that produces and checks this
  document's content; this document defines the shape, that one defines how to get there
* [Architect Feature](architect-feature.md) - the Feature-wide Service Flows this design task's own scope is a
  slice of
* [Pseudocode Style](pseudocode-style.md) - the notation a top-level function's own pseudocode (§4.5) is written in
* [Use Cases](use-cases.md) - the analysis-step artifact a design translates into concrete decisions
* [Required Behaviors](required-behaviors.md) - the Required Product Behaviours a design must satisfy
* [Specific Behaviors](specific-behaviors.md) - the Predicted Service Behaviours a design identifies; this
  document defines where they come from, that one defines their format
* [Chunk Scope](chunk-scope.md) - the artefact a design task's own §1 Scope entry links to once its work is complete
* [HLD Template](../../templates/HLD-TEMPLATE.md) - the fill-in-the-blank shape of the HLD itself
* [External Dependency Template](../../templates/EXTERNAL-DEPENDENCY-TEMPLATE.md) - the fill-in-the-blank shape of an ED document
* [Internal Component Template](../../templates/INTERNAL-COMPONENT-TEMPLATE.md) - the fill-in-the-blank shape of an IC document
* [Documentation Standards](../../standards/documentation-standards.md) - the document shape and directory structure this convention builds on

## 1 Design Directory Location And Naming

A Feature's design work lives at `docs/design/{feature-slug}/` in the project's own `<project>-docs` repo —
`docs/design/` is already part of the minimum directory structure every such repo has. Two things live directly
at that root, Feature-wide, accumulated across however many design tasks it takes: `service-flows.md` (Architect
Feature) and `feature-reconciliation.yaml` (Weaver Engineering Workflows §4's Feature-level reconciliation).

Everything else belongs to one specific design task, filed under `docs/design/{feature-slug}/{design-task-ref}/`
— its entry point is `hld.md`, the high-level design document for that task's own slice of the Feature (one
Service, usually — `Design Service` loops per Service, Weaver Engineering Workflows §6). Alongside it: `chunk-scope.yaml`
(Chunk Scope) and `reconciliation.yaml` (this task's own Predicted-vs-Required reconciliation record, §6 below),
plus that task's own proposals for any new or extended Service — `services/{service-slug}/components/`,
`services/{service-slug}/external-dependencies/`, `services/{service-slug}/behaviors/` — promoted into that
Service's own standing home (`docs/services/{slug}/...`) only once the design is actually delivered
(`concepts/service.md` §2).

Other supporting documents and sub-folders may exist inside the design task directory — a data model, a sequence
diagram, a longer discussion of one component — but every one of them **must** be reachable by following links
starting from the HLD (§5). A supporting document that nothing links to is not part of the design; it does not
satisfy this convention regardless of where it sits on disk.

## 2 The HLD

The HLD is a single Markdown document following the [Documentation Standards](../../standards/documentation-standards.md)'
shape (frontmatter, `## Context`, numbered body, optional `# Appendix`/`# Rationale`), produced from the
[HLD Template](../../templates/HLD-TEMPLATE.md). Its numbered body **must** cover:

1. **Scope** — which use case(s), and which Service(s), this design task actually covers, linking
   `service-flows.md` for the flow this task's own Service(s) participate in. A Service appears under exactly one
   Design Task at a time, ever; reopening an already-designed Service happens only through Design Feature
   Instructions §9.2's `//REDESIGN_REQUIRED`, never by adding it under a second Design Task.
2. **Solution Overview** — a concrete map, not prose: which components this design introduces or modifies, one
   line on each one's responsibility, and what each talks to (other components, external dependencies). A
   reader orients here before going anywhere else in the document.
3. **Key Decisions** — see §2.1. Covers both Open Design Questions and, for every component listed in Solution
   Overview, the interface decision behind it — including, first, Crystallizing The Interface (Design Feature
   Instructions §4) if this task's own Service's `.interface` hasn't already been crystallized by an earlier task.
4. **Data Types** — the shapes of data this Service introduces or consumes, concrete enough for [Predicted
   Service Behaviours](specific-behaviors.md) to instantiate with example values.
5. **Internal Components** — see §4.
6. **External Dependencies** — see §3.
7. **Predicted Service Behaviours** — links to the behaviour file(s) this design produces, one per operation
   (§6).
8. **Technology Stack** — languages, frameworks, and libraries the design commits to.

### 2.1 Key Decisions Must Resolve Open Design Questions

Every use case in scope (§1) may carry its own `## 7 Open Design Questions` — things it deliberately left
undecided. The HLD's Key Decisions section **must** address every one of them, either:

* resolved, stated normatively, with the justification in `# Rationale` per the documentation standard's own
  split between fact and justification; or
* explicitly deferred, with a stated reason it's out of scope for this Design Task.

An open design question with no corresponding entry in Key Decisions is not a complete design — it is a design
that hasn't finished being designed yet, whether or not that fact is obvious from a casual read.

## 3 External Dependencies

An external dependency is a system whose interactions a Service's design depends on but **cannot unit-test** —
its real behavior can only be observed by actually calling it: a network boundary, real time, real randomness,
another team's own data. This is a narrower bar than "outside our control." A third-party *library* linked
directly into the Service's own code is not an external dependency in this sense, however little the design
controls its source — it runs in-process, its calls are ordinary function calls, and it can be exercised in a
unit test like any other code path. What makes something external, here, is that a real test of it needs the real
thing (or a stand-in for it end-to-end), not merely that the project didn't write it.

Because the raw interface a real dependency offers is usually far larger than what one Service actually needs — a
message queue's full API, a REST API with dozens of endpoints — an ED document doesn't restate that whole
surface. It describes a thin shim: the narrow, Service-specific interface this Service actually calls, translated
one-for-one from what the dependency natively offers. The shim's own interface is what an ED document's numbered
operations describe (§3.1), and it's also the only interface a Predicted Service Behaviour is permitted to mock
(§3.3) — an in-process library dependency, not being external by this section's own definition, is exercised for
real in a unit test instead.

### 3.1 Where External Dependency Documents Live, And How They're Addressed

Each Service documents the external dependencies *it itself* uses under its own `docs/services/{service-slug}/external-dependencies/`
— a sibling to that Service's own `docs/services/{service-slug}/components/` (§4.1). Each dependency gets its own
file, `{dependency-slug}.md`, produced from the [External Dependency
Template](../../templates/EXTERNAL-DEPENDENCY-TEMPLATE.md), and is addressed as `{service-slug}.{dependency-slug}`
— extended to `{service-slug}.{dependency-slug}.{function-slug}` to cite one specific operation. Its numbered
body defines each interaction or operation the dependency's *shim* offers as its own numbered section — a
concrete request/response contract, error modes included — so that any later document can cite one specific
interaction via `{service-slug}.{dependency-slug}.{function-slug}` (equivalently, the documentation standard's
`§M.N` syntax within that dependency's own file) rather than the whole ED document.

While a design task is in progress, a new or extended dependency is *proposed* at
`docs/design/{feature-slug}/{design-task-ref}/services/{service-slug}/external-dependencies/{dependency-slug}.md`
and promoted verbatim to `docs/services/{service-slug}/external-dependencies/{dependency-slug}.md` only once the
design is delivered (`concepts/service.md` §2) — the same proposal-then-promotion lifecycle every other Service
artifact in this design task follows.

### 3.2 Ownership Is Per-Service, Not Per-Dependency

An ED document is owned by the Service that wrote it — there's no cross-Service ownership question to resolve,
because an ED document was never a description of the external system's own API in the first place; it's a
description of *this Service's own shim* over it (§3). Two Services that both depend on the same third-party
system — both use Kafka, say — inevitably end up with two, unrelated ED documents, because their shims are
unrelated: one Service's Kafka shim might expose `postNewWidgetEvent`; another's has nothing to do with widgets
and never will. That was never duplication to be weighed as a tradeoff — it's simply two different Services each
documenting their own narrow use of a shared technology, which happen to have nothing in common beyond the name
of the thing underneath.

Within one Service, before writing a new ED document, a design checks whether that Service already has a shim for
the dependency; if so, and the new behaviour needs a new operation from it, the shim is extended in place (a new
numbered section added) rather than a second, competing shim invented for the same dependency within the same
Service.

### 3.3 Referencing An External Dependency

The HLD's External Dependencies section names each dependency the design uses and links to its ED document. A
Predicted Service Behaviour's Given/Then goes further, citing the exact interaction it relies on —
`{service-slug}.{dependency-slug}.{function-slug}` — with concrete example values, not the ED document's abstract
contract restated in prose. This is also the only boundary a Predicted Service Behaviour's own test is permitted
to mock (§3): everything else in a call tree — every Internal Component function — runs for real when it's
exercised end to end.

### 3.4 Each Operation Lists The Behaviours That Rely On It

Every ED operation names every behaviour that depends on it — not just links in, but an explicit "used by" list
the operation's own author maintains, covering both Required Service Behaviours a use case's own operation
touches directly and ones purely internal to the Service (Weaver Engineering Workflows §3). This list is recorded
in the ED document's own frontmatter, keyed by the operation's own `§N` (address, note — a reference and its
description are always separate fields, never one concatenated string), not written inline under the numbered
section it belongs to (External Dependency Template). This is what lets design review check that the shim is
still honestly thin: line up every relying behaviour's actual usage of the same shimmed operation side by side,
and a shim that's quietly stopped being thin — one behaviour needing subtly more than the shim's simple, linear
translation can honestly give it — becomes visible as a real discrepancy between usages, rather than staying
hidden inside a shim that reads as simple from any one behaviour's point of view alone.

## 4 Internal Components

A Predicted Service Behaviour is deliberately black-box: its Given/When/Then is defined only by external-dependency
state and interaction at the Service's own boundary, never by anything internal to how the Service is built.
That's correct — it's what makes a behaviour testable without caring how it's implemented — but it also means a
behaviour carries no information at all about how the Service's own code is decomposed. An internal component is
how the design supplies that blueprint.

An internal component is a part of one Service's own solution — a module or internal interface — whose contract
the design deliberately decides, as distinct from an external dependency's contract, which the design merely
observes. Not every implementation detail is an internal component: something only earns a component document if
its interface is genuinely a boundary — used by more than one Chunk, or likely to be depended on or extended by a
future design task against the same Service. A detail private to a single Chunk stays undocumented at this level.

### 4.1 Where Internal Component Documents Live, And How They're Addressed

Each Service documents its own internal components under `docs/services/{service-slug}/components/`. Each
component gets its own file, `{component-slug}.md`, produced from the [Internal Component
Template](../../templates/INTERNAL-COMPONENT-TEMPLATE.md), addressed as `{service-slug}.{component-slug}` —
extended to `{service-slug}.{component-slug}.{function-slug}` for one function — with the same numbered,
`§M.N`-referenceable interaction shape as an ED document.

Proposed while a design task is in progress at
`docs/design/{feature-slug}/{design-task-ref}/services/{service-slug}/components/{component-slug}.md`, promoted
to `docs/services/{service-slug}/components/{component-slug}.md` on delivery — same lifecycle as §3.1.

Each function also declares, on itself, every address it may call — recorded in the component document's own
frontmatter, keyed by that function's own `§N` (`calls:` — see the [Internal Component
Template](../../templates/INTERNAL-COMPONENT-TEMPLATE.md)), not written inline under the numbered section it
belongs to — and, the reverse, every address whose own `calls:` names it (`called_from:`, same frontmatter entry)
— maintained together, one edit adding both sides. There is no separate call-graph document: a Service's whole
call graph is just the union of every function's own declaration.
A call can cross into another Service's own `.interface` (§4.4) where the data flow (`service-flows.md`) actually
calls for it — that crossing is itself an operation of the called Service, addressed the same way any of its
other functions are.

### 4.2 Deciding An Interface Versus Observing One

An ED document and an IC document look alike, but they're written from opposite directions. An ED document
records a fact about a system the project doesn't control — get it right, and there's nothing to justify. An IC
document records a decision the design made about a system the project does control — the interface itself is
one of this HLD's Key Decisions (§2.1), and the reasoning behind shaping it that way belongs in the IC document's
own `# Rationale`, not just the HLD's.

### 4.3 What Earns Its Own Document

Either of two triggers is enough on its own:

* **Used by more than one Chunk within this design task.** Observable once Chunking happens — a component two
  Chunks both call is, definitionally, a boundary.
* **Likely to be reused by a future design task against the same Service.** The harder call, because nothing
  observable proves it yet. A judgment call for whoever is designing, to make explicitly and record in Key
  Decisions (§2.1) when the component is introduced.

If either call turns out wrong once Chunking happens, the design is revised, the same as any other Key Decision
would be if Chunking exposed a gap in it.

### 4.4 A Service's Own Entry Point Is Always `interface`

A Service's own external interface — what a caller outside the Service actually invokes, an HTTP endpoint, a CLI
command, a queue consumer — is not a separate kind of document. It's a component document like any other,
reserved at the fixed component-slug `interface` rather than identified only by its `Kind` — so a reader always
knows where to find it without checking every component's `Kind` field first:
`docs/services/{service-slug}/components/interface.md`, addressed `{service-slug}.interface`. An operation
(§4.5, Use Cases §2) is, by definition, one invocation of one of a Service's own `interface`
functions — the root of every Predicted Service Behaviour's traced call tree is always one of them.

### 4.5 Interface Functions Carry Their Own Pseudocode And Usage Lists

A Service's own `interface` functions carry more than the request/response contract every other component
function has (§4.1). Each one also carries, in the component document's own frontmatter alongside `calls:`/
`called_from:` (§4.1, Internal Component Template), keyed by that function's own `§N`:

* its own pseudocode, describing the currently designed solution for that operation, linked to the specific
  Internal Component and External Dependency calls it makes — the general, all-callers designed solution, as
  distinct from any one relying behaviour's own bound pseudocode, which changes across design iterations
  differently for each one;
* a list of every Required Product Behaviour operation that relies on it, if any (some `interface` functions
  exist purely to satisfy other Services in the flow, or purely internal Required Service Behaviours — see
  Weaver Engineering Workflows §3);
* a list of every Predicted Service Behaviour that exercises it.

Both lists exist for the same reason §3.4 requires one on every ED operation, applied here to what the Service
itself is expected to do: an `interface` function may be relied on by several different callers, each possibly
demanding something slightly different of it, and its own pseudocode has to actually satisfy all of them at once.
Only `interface` functions carry this — an internal, non-interface function's usage is found instead by walking
its own `called_from:` (§4.1) back to whichever `interface` function it terminates at.

## 5 Navigability

Every file under a design task's own directory — its HLD and every supporting document, sub-folder, or Service
proposal — must be reachable by following links starting from that task's `hld.md`. A reviewer, human or agent,
starts there and can reach the entire task's own design without being told where anything else lives. The Feature
root's own `service-flows.md` sits one level up, outside any single task's own navigability requirement, but
every task's HLD links to it (§2 Scope) so a reader can still reach the wider flow this task's own Service
participates in.

## 6 From Required Behaviours To Predicted Service Behaviours

`Architect Services` (Design Feature Instructions) walks `service-flows.md` to derive, for a Service in the flow,
its own Required Service Behaviours — an independent artifact from anything Design produces (Weaver Engineering
Workflows §3). This HLD's own Key Decisions (§2.1) are what turn those Required Service Behaviours, plus this
task's own Crystallize The Interface step, into concrete, technology-specific interactions — a chosen interface,
a chosen data shape, a chosen external dependency. That concrete version, expressed as a testable Given/When/Then
and traced call tree, is a Predicted Service Behaviour: this document stops at "the HLD identifies which
behaviours it predicts and links to them"; their own format, and the process that derives them, is defined in
[Specific Behaviors](specific-behaviors.md), not repeated here.

# Appendix

Worked example of a design task directory's shape (contents illustrative, not literal filenames):

```
docs/design/user-account-view/
  service-flows.md
  feature-reconciliation.yaml
  WVR-201/
    hld.md
    chunk-scope.yaml
    reconciliation.yaml
    services/
      user-accounts/
        components/
          interface.md
          session-cache.md
        external-dependencies/
          auth-service.md
          accounts-mongo.md
        behaviors/
          view-own-account.md
          handle-expired-session.md

docs/services/
  user-accounts/
    SERVICE.md
    components/
      interface.md
      session-cache.md
    external-dependencies/
      auth-service.md
      accounts-mongo.md
    behaviors/
      view-own-account.md
      handle-expired-session.md
```

Everything under `docs/design/user-account-view/WVR-201/services/user-accounts/` is a *proposal* — it becomes the
standing content under `docs/services/user-accounts/` only once design task `WVR-201` is delivered
(`concepts/service.md` §2). A later, unrelated design task that also needs the `user-accounts` Service extends
the delivered documents in place rather than creating its own competing copy.

# Rationale

**Why `docs/services/{slug}/` rather than the design directory itself.** An early version of this convention
considered filing external dependency contracts and internal components inside each Feature's own design
directory, since that's where they're first needed. That breaks down as soon as a second design task needs the
same Service: either it duplicates the contract inside its own directory (drift risk, no single place to update
when the real system's interface changes) or it reaches across into another task's design directory for a fact
that isn't really about that task at all. `docs/services/{slug}/` is where a Service's own standing facts belong
for the same reason `docs/architecture/` used to serve this role project-wide — see `documentation-standards.md`'s
own Rationale on why a Service now owns this rather than the project as a whole.

**Why "duplication" across Services turned out not to be a real problem.** Two Services both depending on, say,
Kafka aren't two copies of the same content — they're unrelated content that happens to name the same dependency,
because an ED document was never a description of the third-party system itself, only of one Service's own
narrow, translated use of it. There is nothing to accept a tradeoff over, and no future shared resource to design
toward if drift ever "became a problem," because there is no shared content to drift.

**Why Key Decisions must enumerate every Open Design Question explicitly.** The point of a design being
*reviewable* is that the architect can audit it without a close read of the whole document — a checklist against
each linked use case's §7, not a search for anything that might have been missed. Requiring every open question
to show up, resolved or explicitly deferred, turns that audit into a mechanical check instead of a judgment call
about whether the design document happened to be thorough.

**Why Internal Components exist as a concept distinct from External Dependencies, rather than folding both into
one document type with a `Kind` field.** The two are written from opposite directions — an ED document records an
observed fact, an IC document records a decision — and that difference has to show up structurally, not just as a
value in a field. Merge them into one type and nothing stops an ED document quietly drifting into inventing a
decision the design has no authority to make, or an IC document skipping the justification a real decision
actually owes.

**Why not every internal detail becomes an Internal Component.** Documenting every module boundary a design task
happens to introduce would turn `docs/services/{slug}/` into a second, competing description of the Service's own
code that drifts from the actual implementation the moment anyone refactors a Chunk-private detail. Restricting
IC documents to interfaces that actually cross a Chunk or design-task boundary keeps it limited to facts that
genuinely need to outlive the Chunk that first created them.

**Why a Service's own entry point is a fixed, reserved slug (`interface`) rather than just a `Kind` value.** The
entry point is where every call tree in the Service's own design starts; making it findable by a fixed address
rather than a linear search through every component's `Kind` is worth the one special case, the same way a fixed
convention (`README`, `index.html`) earns its keep in other systems for exactly this reason. This is the
Service-scoped successor to the earlier, single project-wide `IC-000` — every Service now has exactly one, not
just the project as a whole.

**Why "used by" lists (§3.4, §4.5) exist but a mechanical check that nothing's missing from them doesn't live
here.** The lists themselves are design output — required content on the operation or function's own document,
the same as its request/response contract is. Whether every relying behaviour that should be listed actually is,
though, is a completeness check, better run by tooling every time the design changes, not something a human
reviewer re-derives by hand at each review.

**Why an interface function's own pseudocode and a relying behaviour's bound pseudocode are two artifacts, not
one.** An interface function's own pseudocode has to serve every behaviour that relies on it at once, so it's
necessarily a superset of what any single one needs — exactly what "could happen" versus "actually happens"
already means for `calls:`/call tree. Recording each relying behaviour's own bound instance separately is what
lets reconciliation check one behaviour's actual requirement without the superset's own breadth getting in the
way of a clean pass/fail.
