# Design Layout Standards

## Context
* [Documentation Standards](documentation-standards.md) - the document shape, indexing and cross-reference rules this standard builds on
* [Product Service Model](product-service-model.md) - the Product, Service and Offering the designs governed here belong to
* [Service](concepts/service.md) - the functional boundary a design is written against
* [Weaver Engineering Workflows](../workflows/weaver-workflows.md) - where design sits in the workflow
* @agent-plugins-docs/docs/design/design-assistant/datamodel/DATA-MODEL.md - the facts a design records, stated independently of any file
* @agent-plugins-docs/docs/design/design-assistant/serialization/SERIALIZATION.md - how those facts are written to and read from these documents

## Purpose

This standard says how a design's documents are laid out: which documents exist, what concern each one holds,
and how a design is cut up as it grows. It applies to every design in Weaver Engineering. It is independent of
any tool — the design assistant reads and writes designs laid out this way, and neither owns nor enforces the
layout.

It is **guidance, not a validity condition**. Every layout parses: no document is an entity, and the model is
the fold of every claim in scope, so nothing in the schema can reject one. A design that arrived laid out some
other way is brought under the model as it stands, and is not reorganised to comply.

The guidance exists because the default an agent falls back to is writing everything in one document. That
parses, folds and reconciles perfectly well, and it gets steadily harder to read, review and re-judge as the
design grows. Two mechanisms in the serialization make the cost concrete: two documents competing to state one
fact raises a `duplicate-claim` advisory, which must be answered before a design can reconcile; and a claim's
anchor covers every section nested beneath it, so a coarse document re-judges a great deal on every edit.

## 1 The Design Directory

A design occupies a directory. Its subdirectories are part of it, except any subdirectory that is itself a
design directory.

### 1.1 `DESIGN.yaml`

A directory is a design directory exactly when it contains a `DESIGN.yaml` at its root. The file declares two
things:

* the **namespace** the design's addresses are rooted in;
* the **check configuration** the design is assessed against — stated inline, or inherited: from the design
  containing this one, or by pointer to the Product's or the organisation's own configuration. **No
  configuration is a failure**, not a default: a design directory that does not say what it is checked against
  cannot be assessed at all.

Inheritance from a containing design is the ordinary case, and it is the same shape the build manifest already
follows (§5.5): a contained design target takes its container's configuration unless it says otherwise. An
uncontained design has no container to inherit from and must state or point at its own.

It makes no claims and is never assessed itself. It is read before parsing begins, to establish where and what
the design is — a question that has to be answered before there is anything to parse.

Both declarations are here for the same reason, and neither could be a claim. The namespace is what every claim's
address resolves against, so a claim declaring it would have to be addressed within a namespace that same file is
still establishing. The configuration is what determines which checks run, and the checks are what give positions
their meaning — so a claim declaring the configuration would be a claim at a position the configuration has not
yet licensed. Both are circular in the same way, and both are therefore read as configuration, before parsing
begins.

A missing configuration is consequently **not a finding**. It is the same category of problem as a missing
`DESIGN.yaml`: not work to do inside the design, but a directory that cannot be assessed.

### 1.2 The Entry Document

A design has exactly one entry document, `{design-slug}.md`, and every document in the design is reachable from
it. A design may contain many directories; it does not gain a second entry point.

At the start of a design the entry document also carries the description of the boundary and its operations.
Both move out as the design grows (§5.1, §5.2), and what remains is the index.

## 2 Layout By Boundary Containment

### 2.1 The Rule

**A boundary occupies a directory of its own as soon as more than one document describes it**, and stays a
single document while one suffices.

The rule applies recursively, at any depth, and it applies to the data model a boundary defines in the same
way: a boundary owns the data model it defines, so the moment that data model earns its own document the
boundary has two documents and earns its directory.

The rule is mechanical. Deciding to *create* a second document is always the architect's, and no threshold in
this standard forces one.

Splitting a document changes nothing about the model. A document is prose and the claims that index it, so
moving claims between documents is placement, never authorship: the same claims stand, targeting the same
addresses, and a move is a deletion and a creation the fold does not notice.

### 2.2 What Earns A Boundary Another Document

**Every boundary has its own document.** So a boundary that contains another boundary has, by that fact alone,
more than one document, and occupies a directory. This is not a judgement and no threshold is involved: any
contained boundary at all produces the second document, and §2.1 applies immediately.

The remaining triggers are the ones where judgement does come in:

* function count, or an individual function's complexity, outgrowing the one document;
* the boundary's own data model outgrowing what fits inline;
* a supporting document being added.

### 2.3 The Trivial Boundary

A **trivial boundary** is one that comfortably stays a single document:

* a single interface;
* at most 9 functions;
* a pooled allowance of at most one new type and one new exception per function, drawn on across functions
  rather than capped per function;
* where a trivial type has no nested structure.

Only types that **cross** the boundary count against the allowance. Shapes used only by a boundary's private
functions still have types; the data dictionary makes no completeness claim about them, so they place no
obligation on the boundary's documents.

### 2.4 The Advisory Count

**Whether a boundary actually splits is the architect's choice. The advisory count says how insistently they
should be told.** It is not a threshold for action and nothing acts on it.

Each thing that pushes a boundary past §2.3's budget adds to the count:

| Count | How insistently to say so |
| :--- | :--- |
| 0 | say nothing |
| 1 | mention it |
| several | press the point |

Breaching the budget splits nothing by itself, and a boundary may sit at a high count indefinitely because the
architect has looked and decided it reads better as it is. What the count changes is the volume of the prompt,
never the outcome.

Every threshold and budget in §2.3 and §2.4 is configurable in any tooling that reports against it.

The count is surfaced by a **global check** — one that sits at no maturity level, so its findings never reset a
design's maturity. How a design is cut into documents says nothing about whether the design is complete or
accurate, so a boundary over budget is real work to do and leaves the design exactly as mature as it was. The
`out-of-date-diagram` check (§6.1) is global for the same reason. Acknowledgement is this one's only resolution
besides splitting.

## 3 Category Directories

`operations/`, `fixtures/` and `DESIGN-DECISIONS/` are not boundaries and do not follow §2.1. They are flat
categories, and each is created when the category is first populated rather than when it acquires a second
member.

Contained boundaries are **not** gathered into a category directory. They sit directly in the design directory,
because they are the design's containment axis: the directory tree is the boundary tree. Everything else is a
category of satellite artefact that would otherwise litter the design root.

`NFR/`, `ENDPOINTS/`, `DATA-MODEL/`, `BOUNDARY/` and `BEHAVIOURS/` are **not** category directories, despite
looking like them. Each is a single document that earned a directory under §2.1 — `NFR.md` becoming
`NFR/NFR.md` alongside its siblings, exactly as `DATA-MODEL.md` becomes `DATA-MODEL/DATA-MODEL.md`. The
singular name is the tell: it names the document, not a category of them (§4).

`DESIGN-DECISIONS/` additionally holds concern subdirectories, created **only when a decision is taken under
that concern**. They are not pre-created, and the set is open — interface and manifest, data model, boundaries,
NFRs and observability are the expected ones, not the permitted ones.

## 4 Naming

* A boundary's own document is `{slug}.md` while it is a single document, and `BOUNDARY.md` once it owns a
  directory. The rename is the whole mechanism of the split.
* The design root always owns a directory, so its boundary document is always `BOUNDARY.md`.
* A document with a fixed role is UPPERCASE and hyphenated: `DATA-MODEL.md`, `SYSTEM-INTERFACE.md`,
  `STATE-TRANSACTIONS.md`. This follows the directory-per-entity pattern
  [Documentation Standards](documentation-standards.md) §2.1 already uses.
* **`X.md` earning a directory becomes `X/X.md`.** `NFR.md` → `NFR/NFR.md`, `DATA-MODEL.md` →
  `DATA-MODEL/DATA-MODEL.md`, `ENDPOINTS.md` → `ENDPOINTS/ENDPOINTS.md`. The document keeps its name and its
  role; what changes is that it now has siblings. The name stays **singular** because it names that document,
  which is what distinguishes it from a category directory (§3).
* A document named for a model element takes that element's slug.
* Hyphens throughout; underscores are not used in document or directory names.

## 5 The Documents

Each document below appears when the design has something to put in it, never pre-created empty.

### 5.1 `BOUNDARY.md`

The description of a boundary: its purpose, the functions belonging to it with their slugs and prose, and its
interfaces. Every boundary has one, at every level, whether or not it is a design target.

A boundary holds content but no requirements. What it must *do* follows from the required behaviours of its
consumers, and is owned by whatever design target contains it.

**A function earns its own `{function-slug}.md` individually.** One function becoming intricate enough to want
the room does not oblige its siblings to move with it: a boundary may hold most of its functions inline and a
couple in their own documents, and that mixed state is the normal one rather than a transitional stage.
`BOUNDARY.md` keeps the inline ones and points at the rest.

A function's own document holds what the inline form holds — prose, pseudocode, sequence diagrams, and what it
calls — so moving one out is a relocation and nothing more.

### 5.2 `operations/` And `OPERATION.md`

An operation is a layout unit like a boundary: while one document describes it, it is `{operation-slug}.md`
directly in `operations/`; a second document earns it a directory, and its own document becomes `OPERATION.md`.

**What the operation owns**, and why the document is more than a pointer:

* **its contract** — what invoking it is for, and its signature;
* **where to start** — the function that realizes it, which is the entry point a trace walks from;
* **whether it is critical** — and so whether it needs SLIs (§5.9);
* **its payload and parameter dimensions and values, with their provenance** — the variations the operation must
  behave over, and how they were arrived at;
* **the fixtures that expose each value** (§5.11).

The last two are what give the document a reason to exist. Without them an operation document is a pointer at a
function and nothing more.

**What the dimensions define is the condition space, not the behaviour.** They say what varies, and so which
cells exist. Three different things then settle what happens in those cells, and keeping them apart is what makes
the design reconcilable at all:

| | Comes from |
| :--- | :--- |
| the **condition space** | the dimensions and values recorded here |
| the **required** behaviour at a cell | the use case, the operation's own prose, or the architect's assertion |
| the **expected** behaviour at a cell | tracing the design — the call tree, the cell's condition, and its fixtures |

Reconciliation is the comparison of the last two. An operation that derived its requirements from its own design
would be comparing the design against itself.

**Everything that determines the shape of the space is recorded here, and nowhere else.** That includes each
dimension's **rank**, which is what nests the tree, and any cell the architect **excludes** explicitly, which is a
judgement about the space rather than about a behaviour. A behaviour document is a *view* into the space (§5.3,
§5.8), and a view claims behaviours, never structure.

The reason is that cell ids are derived from ordinals in rank order, and a cell id is an **address**. If two views
could each state a rank they would either duplicate it or disagree — and a disagreement would give one cell
different addresses in different documents, which nothing downstream could reconcile. One space, one ranking, one
set of ids.

**The dimensions are recorded as assessed, not merely listed.** A list cannot distinguish "these are the
behaviour-affecting variations, and they were considered" from "these are the ones somebody happened to write
down", and only the first supports a coverage claim. So the operation records how its dimensions were arrived at:
a pointer to the analysis that derived them, or the architect's own assertion of the variations in scope.
Analysis should define them and cannot block design, so where the feed-in does not, it falls to the architect —
and doing that analysis is then the architect's job.

This is a recorded fact rather than a convention, because **an unanswered question has to be detectable from the
model**. Findings are computed from model state, and the next unit of work is derived over them, so a dimension
with no recorded basis is reported as outstanding work in the same way an uncovered cell is. A standard that only
asked for the basis by convention would leave the gap invisible the moment someone skipped it.

It sits with the operation rather than with the data model because **what is behaviour-affecting is
operation-specific**. One type used by three operations may be bucketed differently for each, since a threshold
exists only because some operation behaves differently across it. What belongs to the type is what is *possible*
— its validity rules and presence dependencies (§5.4); what belongs here is what makes a difference.

**The signature appears on both the operation and its realizing function, and that is not duplication.** They
are different entities at different addresses — `…/op/create-order` and `…/fn/create-order` — so two claims, one
on each, compete for nothing. What the design asserts is that they *conform*, which is a check between two facts
rather than one fact written twice. The operation's signature is its contract, settled as part of stating what is
required, and it is settled before any function exists to derive it from.

### 5.3 `BEHAVIOURS.md`

**This is the spec.** It associates each named cell of an operation's condition space with its fixtures and its
required results, which is to say it defines what the failing tests must be — and by consequence what the code an
agent writes must actually do. Everything else in a design exists so that this document can be written with
confidence.

**It holds the behaviours associated with the aggregate condition, not the conditions themselves.** The
dimensions, their values and their ranks all belong to the operation (§5.2) — they are what the operation must
behave over, and they are what determines which cells exist and what each is addressed as.

**This document claims no structure.** The tree it displays is rendered from the operation's own dimensions
rather than asserted, which is what lets the NFR views (§5.8) display overlapping parts of the same space without
any of them disagreeing about what a cell is called. Derived dimensions are recorded where their source is: a
cross-cutting dimension with the rule contributing it, a dependency-state dimension with the operation, as
derived content written by the service.

#### 5.3.1 It Is Always System-Generated

`BEHAVIOURS.md` is the one document where the **claims drive the prose** rather than the prose being indexed by
claims. It is written by the service, never by hand, and the generation has three distinct inputs:

* the document's **structure**, written mechanically from the operation's dimensions and their ranks;
* **prose that is still valid**, copied forward mechanically with its addresses updated;
* **judgement prose**, physically written by the service, with new or updated frontmatter addressing it.

The judgement it transcribes is the architect's. Typically the architect and the agent assess the operation's
prose and anything else bearing on it, the fixtures are defined and linked to the cell and the parameter values
fixed, the agent proposes a behaviour, the architect corrects it, the agent revises, the architect approves — and
then the document is updated. **Every behaviour is reviewed and approved by a human at least once.** That is
demanding, and it is deliberately *the* human in the loop: it is the point at which a person takes responsibility
for what the code will be made to do.

**A hand edit is not forbidden, it is invalidating.** Editing the document outside that process invalidates its
claims, and they are re-judged — the ordinary treatment of frontmatter the service did not write.

#### 5.3.2 Validity Is Stronger Than Agreement

Re-judgement checks more than whether prose and frontmatter agree. A behaviour must **reconcile with the
dimensions of its condition space and with its fixtures**. A cell that does not is **invalid** — whether or not
its claim and its prose concur, and irrespective of whether a human approved it. Approval is not a substitute for
validity: a person can approve a behaviour that the space it sits in no longer supports.

An invalid behaviour is a finding. It is resolved by re-expressing it or by removing it, and which of those is
right is a judgement rather than a derivation.

#### 5.3.3 Re-Ranking Is A Rename

Rank numbers generate the dotted-decimal ids that structure this document, so re-ranking a dimension re-addresses
every cell beneath the change. That is a **name change**, and name changes are mechanical: the structure is
regenerated, the claims are renamed, still-valid prose is carried across with its addresses updated, and only
genuinely new or re-judged behaviours need the architect again. Nothing about a rename is a semantic change, and
nothing about it should cost a review.

#### 5.3.4 Presentation Collapses Where Variation Does Not Discriminate

Two collapses keep a tree readable without losing anything, and both are derivations rather than choices.

**Consecutive single-variant dimensions collapse into one section.** Where a run of sequentially ranked dimensions
each contributes exactly one value, nothing branches across them, so they are presented as a single section
carrying all of those Givens together — a value may jump rank this way. Rare in functional behaviour, common in
non-functional, where most dimensions sit at their nominal value.

**In an NFR view, payload conditions collapse to those that discriminate on the boundary.** A rule's view is not
interested in the whole range of payload variation; it is interested in variation that causes *different functions
on the NFR boundary* to be reached. Two payload values reaching exactly the same boundary functions are
indistinguishable for that rule, and present as one.

This is **not** derivable from the inter-condition validity rules — they say what combinations are possible, not
what the design does with them — but it **is** derivable from the call tree, which records which functions each
cell's entry condition actually reaches. Walking it identifies every route to the boundary, and what is wanted is
the **minimum set of variants that reaches all of the boundary's functions**.

Two things follow from that being a minimisation.

**Ties are broken by lowest ordinal first.** Several different minimal sets may exist, and without a deterministic
rule a system-generated document would churn between equally valid presentations while the design stood still,
moving its checksums and raising findings for nothing.

**The collapse holds only while NFR rules are uniform over what crosses a function.** A rule that distinguished
kinds of data — card numbers must never reach the log stream — would discriminate on payload directly, and this
collapse would stop being sound. When such rules arrive the consequence reaches further than the collapse: the call
tree walk would then have to find fixtures that **expose all sides of the rule**, since a rule about card numbers
is only exercised by a condition carrying one and a condition carrying none. Minimising for function coverage and
satisfying a data predicate are different objectives, and the selection would have to serve both. That is a problem
for when data-driven boundary rules exist, not now.

#### 5.3.5 Cell Ids Are Operation-Specific

A cell id is only meaningful within one operation's space: `cell/1.2.3` of `create-order` and `cell/1.2.3` of
`cancel-order` are unrelated. The addressing already carries this, since a cell's address nests through its
operation — but two consequences have to be honoured by anything writing or reading these documents:

* **every claim on the condition space names its operation**, by addressing through it;
* **the NFR behaviour views are keyed on operation** (§5.8). A rule's view holds cells drawn from every operation
  that reaches the functions it governs, so it is organised by operation first and cell second. A bare cell id in
  such a document would be ambiguous.

**`BEHAVIOURS.md` holds the operation's *functional* behaviours. The non-functional ones live under `NFR/`**
(§5.8). The line between them is mechanical rather than editorial, and rests on the fact that **an NFR is a
deviation from nominal**: every cross-cutting dimension has a nominal value that coincides with the functional
path — auth present, latency within budget — and its other values are departures from it, each imposing a route
back to nominal or a graceful exit.

So a cell where every cross-cutting dimension sits at its nominal value is a functional behaviour, and a cell
where any sits off it is a non-functional one. Cross-cutting dimensions therefore rank **below** the functional
ones, which makes the functional tree the nominal projection of the space and hangs each NFR variant beneath the
functional leaf it departs from.

`BEHAVIOURS.md` consequently does not show the whole space, only that projection. Coverage is still asserted
over the whole of it, so the completeness check spans both locations.

The space accretes as the design matures, and this is normal rather than a defect: payload and parameter
dimensions first, then cross-cutting dimensions as each NFR rule's selector resolves against real functions,
then dependency-state dimensions once the call tree reveals which dependencies the operation actually reaches.

Adding a dimension adds cells, and **every added cell is uncovered until either a behaviour occupies it or an
inter-condition validity rule collapses it**. Those are the two dispositions; a cell in neither is outstanding
work.

Given and When are a projection over the same dimensions rather than two structures — the state the operation
is invoked against, and which variant of the call this is. Given dimensions rank above When dimensions where
nothing else dictates the order: state constrains which invocations are meaningful, never the reverse.

The dimensions' own provenance is recorded with them, on the operation (§5.2).

**The condition space and the behaviours are one structure.** Each nested section is one cell, titled with its
dotted-decimal id and the value it selects, so the section hierarchy *is* the tree: the Given accumulates by
containment, and a leaf adds the When projection and what is required there. That also makes anchoring correct
without effort, since an anchor covers its nested subsections — so a claim at a non-leaf cell covers exactly that
cell's subtree, and the anchor's containment and the cell's containment are the same containment.

**Four levels of nesting is the practical ceiling, and it is a readability limit rather than a markdown one.** A
document actively using more than about four levels of indentation is unreadable however the indentation is
achieved, so the fact that markdown runs out of heading levels at six is a coincidence rather than the constraint.

**Operations are repeated top-level sections, not a level of nesting.** A behaviours document holds
`## {Operation} Behaviours` once per operation it covers, each with its own tree beneath it. Keying on operation
therefore costs no depth — every operation gets the full four levels — which is what makes the NFR views (§5.8)
affordable despite spanning several operations.

**Where a tree splits is declared by the operation's ranked dimensions, not computed from a depth count.** An
arbitrary cut at the fourth level would rarely fall anywhere meaningful, and the document is already divided by
functional versus non-functional and by operation before depth becomes a question at all. More than four genuinely
orthogonal payload dimensions is uncommon; when it happens, the useful split is on the **first** one or two
dimensions rather than the last, because a high cut yields coherent subtrees a reader can hold — "for orders under
a thousand, …" — where a low cut yields fragments. So the operation declares its break rank, and the split follows
it.

**The parent document becomes an index.** It keeps the tree down to the break rank and, at its deepest retained
level, points at the sub-documents rather than continuing:

```
### Given {dimension} = {value}
#### Given {dimension} = {value}
##### Given {dimension} = {value} → {sub-behaviours document}
```

This is the most combinatorially explosive artefact in a design, and splitting it is ordinary rather than
exceptional for any mature operation.

### 5.4 `DATA-MODEL.md`

The types a boundary defines, as mermaid diagrams and prose. It starts as a `## Data Model` section of the
boundary's own document and moves out when it becomes cumbersome; that move is what earns a contained boundary
its directory.

A data model that evolves sub models earns a `DATA-MODEL/` directory: `DATA-MODEL.md` keeps a data-model-at-a-
glance diagram and links, and each sub model gets its own document. The same recursion continues downward.

A type is defined by exactly one boundary, and a reference resolves by walking outward from the referencing
boundary, taking the nearest definition. A shape shared across several contained boundaries is therefore
defined once, on the boundary containing them all.

### 5.5 `MANIFEST.md`

What the design is built as and how a consumer gets at it: language, build system, topology, dependency
constraints, test stack, and distribution. Prose, with the manifest carried as frontmatter.

Every design target has one, Library and Service alike — a Library is built, packaged, published and consumed,
and distribution is its whole consumption story.

A contained design target **inherits** its container's manifest per setting. It may tighten what it inherits and
may never loosen it; a contradiction is a finding rather than a local override. An uncontained design target has
nothing to inherit from and declares its own. Every setting is stated, inherited or exempted — there is no
fourth state, and an exemption is an answer rather than a blank.

A Service's manifest also carries the settings that only mean something for a running process: runtime and base
image, execution paradigm, frameworks, wiring and infrastructure, and operational scaffold. None of these has a
referent on a Library.

### 5.6 `SYSTEM-INTERFACE.md`

How the service interacts with the system hosting it, as distinct from how a consumer interacts with the
service: the configuration and environment injected into it, the signals it handles, the diagnostics it emits,
and what it implicitly takes from its host.

Each of those must be **declared or explicitly exempted**, with a reason where exempted. A vector nobody
examined and a genuinely empty one are indistinguishable if silence is permitted, so this document exists even
when most of it is exemptions.

What the host-and-kernel part declares are implicit dependencies — a system clock, a filesystem path, a file
descriptor — and each becomes a dependency-state dimension in the condition spaces of the operations that read
it. A boundary reading the clock has clock-dependent behaviour whether or not anyone modelled it; declaring the
dependency is what brings it into the condition space where it can be covered.

**The host is a depended-on boundary like any other, reached through a `dependency`-kind boundary's shims.** It
is outside the perimeter, in the same category as a datastore or another team's service, and the rule that only a
`dependency`-kind boundary may hold `dependsOn` is what makes every outward crossing locatable. Reading the clock
is an outward crossing, so it goes through a shim like the rest.

**Only the crossings the design actually makes are defined.** A shim is a one-for-one translation of a single
operation of the depended-on boundary, so a design that reads the time defines one shim function and nothing
else. There is no obligation to enumerate the kernel, and no boundary has to exist for host facilities nothing
touches.

This is load-bearing rather than tidy. A dependency-state dimension is derived from a dependency interaction
found in the call tree — so a domain function reading the clock directly, with no shim, would leave no call-tree
node to derive the dimension from, and the non-determinism could not be brought into the condition space at all.
The shim is what makes the clock coverable.

So this document **declares the dependency** — what is read, that it is a source of non-determinism, and which
dimension it becomes — and points at the shim. It does not define functions. Giving it that ability would make it
a second kind of thing holding functions, which both duplicates what a dependency boundary already does and
breaks the rule that every function belongs to exactly one boundary.

A Library has no process, and therefore no system interface. This document does not exist on one.

### 5.7 `ENDPOINTS.md`

The network endpoints: how data flows in and out of the service under BAU conditions, joining it to the other
services in the service flow.

Notation follows the endpoint kind rather than one house format:

| Endpoint kind | Notation |
| :--- | :--- |
| request-response, webhook | OpenAPI |
| server-sent events | OpenAPI — a `text/event-stream` response |
| message-consumer, message-producer | AsyncAPI |
| WebSocket | AsyncAPI — its WebSocket binding |
| gRPC, including streaming | the `.proto` **referenced**, never restated — it is the interface definition |
| in-process API | the binding only — module path, how it is imported, which operation it exposes |

The last two are the cases where nothing is described here because the contract lives elsewhere. For gRPC the
`.proto` *is* the contract, so it is referenced as an external document with a checksum, exactly as a neighbour
service's contract is. An in-process API has no wire protocol at all: its contract is the operation's signature
and the types it names, which the design already holds in its boundary and data model documents, so restating
any of it here would create a copy with nothing keeping it in step.

A topic the service publishes to belongs here: consumers subscribe to it, and nobody else owns the schema.

A call the service *makes* to another service is **not** an endpoint. It is a thin shim on a dependency
boundary, and the contract being consumed belongs to the other side — referenced as an external document with a
checksum, never restated.

When the list becomes unwieldy it splits into an `ENDPOINTS/` directory with a document per interface — a
logical grouping of functions, usually entity-centric, named for the physical interface it is likely to become.
This is the same sense of "interface" as a boundary's own grouping of its perimeter functions.

A Library has no endpoints.

### 5.8 `NFR.md`

A design's NFR consideration starts as **one document**, `NFR.md`, which does not exist until NFRs have been
considered at all. It may be as little as an exemption per category — that is a complete and legitimate
assessment, and a design should not be obliged to carry a separate document per category in order to say that
none of them applies.

Its sections are the cross-cutting categories. Each holds the rules that apply under that category, or
references to them:

* where a rule is **inherited** — from the Product that owns the design, or from a containing design target —
  the section holds provenance only: a link and a checksum, so a change to the rule is detected without the
  design claiming ownership of it;
* where the design **defines** the rule itself, the rule is stated in full;
* where the design **exempts** itself, the exemption is recorded with its reason.

**The categories are not a closed set.** Security, resilience, concurrency and state-transaction are the ones
currently identified; others may be. Requiring every design to hold a document per category so that it can
exempt itself from each is excessive, which is why the starting shape is one document with a section per
category actually considered.

**Which functions a rule governs is never inherited.** That is the local judgement each design makes, and it
must be recorded either by selecting functions or by exempting the design from the rule. A rule in scope with
neither is a finding.

**A cross-cutting boundary is a boundary, so it earns its own document.** Once functions are selected onto one,
`NFR.md` has a sibling and §2.1 applies: NFR consideration occupies an `NFR/` directory, with `NFR.md` keeping
the assessment overview and a document per cross-cutting boundary beneath it.

**Below the cross-cutting boundary, the directory partitions by rule**, and each rule holds the non-functional
behaviours its deviations demand — `NFR/{xc-slug}/{rule-slug}/BEHAVIOURS.md`. This is the same recursion
boundaries and the data model follow, and it groups the required behaviours by the rule demanding them, which is
the unit completeness and coverage are actually judged in: "does this rule's requirement hold everywhere it
reaches" is the question a reviewer asks, and it is answerable by reading one document.

**A rule's view is deliberately non-contiguous.** It is the view of the design's enforcement of that rule
through whichever path requires it, so it holds a set of subtrees scattered across the condition space rather
than one contiguous branch. A rule contributing more than one dimension spans accordingly.

**It is keyed on operation.** A rule reaches every operation whose call tree touches a function it governs, so its
view holds cells drawn from several spaces at once — and a cell id means nothing outside the operation that owns
it (§5.3.5). The document is therefore organised by operation first and cell second — as repeated top-level
sections rather than a level of nesting, so keying on operation costs no depth (§5.3) — and it is system-generated
for the same reasons `BEHAVIOURS.md` is (§5.3.1).

A rule's given tree is also much shallower than a functional one. Its dimensions are mostly orthogonal, and its
payload conditions collapse to only those that discriminate between functions on its boundary (§5.3.4), so the
four available levels are rarely the binding constraint here.

Where a cell is off-nominal on two rules at once — a rollback under a concurrent writer, say, where neither
deviation short-circuits the other — it belongs to both rules' views, and **both views state it**. Most such
cells do not arise in the first place: a deviation that terminates or diverts the path collapses everything
downstream of it, which is ordinary irrelevance pruning, so no-auth never reaches the dependency-latency
dimension at all.

What matters is that the behaviour is recorded, not that it is recorded once. The prose may legitimately be in
scope twice, and the duplication it produces is **structural and expected** rather than a sign the documents are
drawn along the wrong lines — which is what the `duplicate-claim` advisory exists to catch. The values are
identical, so the fold is well-defined and the behaviour stands either way.

A selector reaches down through contained boundaries and stops at any contained design target, which assesses
the same in-scope rules for itself.

### 5.9 `SLIs.md`

An operation's service level indicators, as prose and OpenSLO `SLI` objects, drawing on metrics emitted by the
functions that operation reaches.

SLIs exist only for a Service. A Library's functions emit metrics like any others — a library that cannot
report its own counts is unobservable inside whatever deploys it — but a Library delivers nothing on its own,
so it can never say whether the numbers are acceptable.

Two things decide what is required, and they answer different questions. **The archetype decides which delivery
dimensions need an SLI** — availability, latency and quality for request/response; coverage, correctness,
freshness and throughput for batch; durability, throughput and latency for storage. **A critical user journey
decides which operations need a service contract**, and therefore need SLIs at all: a critical user journey is a
kind of use case, and an operation on one has to achieve a contract for the journey to hold.

Analysis does not block design. It **justifies the value** of the behaviours a design has already specified — so
an operation's criticality may equally be asserted by the architect and recorded as a decision, and the design
proceeds either way.

The SLO that monitors an SLI stays outside the design. The design owns the measurement; the Product owns the
promise about acceptable values of it.

### 5.10 `DESIGN-DECISIONS/`

The design's open questions and the decisions that closed them, one document per decision, in concern
subdirectories created as decisions are taken (§3).

This is a register of the design's **current state, not a history**. A superseded decision is retained and
marked superseded, because "this was considered and replaced" is still true of the present; the elements a
superseded decision produced are deleted rather than left standing beside their replacement. A change request
against another design target is deleted once satisfied, since nothing about the present depends on it having
been made.

An open design question may be recorded in any document; its resolution is recorded here. A unit of work may
declare that a decision needs taking, and its resolution is recorded here too.

### 5.11 `fixtures/`

The content of the design's fixtures: payload samples, dependency entity states, error responses, sample
logged, stdout and stderr output, CLI examples, filesystem layouts. Grouped into subdirectories where that
helps.

**The fixture claim and the fixture content live in different places.** A fixture attaches to a condition value,
so its claim belongs with whatever defines that condition — which is the **operation** (§5.2), not
`BEHAVIOURS.md`. The content lives here, as a file the design points at with a checksummed reference rather than
parses. A file carrying no claims is data with a schema of its own; nothing is written into it.

**The relationship is many-to-many, and the claim runs from the condition.** One fixture exposes several values
across several dimensions — a single sample order payload exhibits a value of `order-value`, of `customer-state`
and of `payment-method` all at once — and one value is exposed by several fixtures. So each condition claims the
set of fixtures that expose it, and the sets overlap:

* condition 1 claims that fixtures **A, B and C** expose it;
* condition 2 claims that fixtures **B, C and D** expose it.

B and C appear in both, stated once each as content and named by both conditions. Nothing is restated, and the
overlap is the normal case rather than a sign of redundancy — fixtures are reused precisely because they carry
the same characteristics.

**A cell's *possible* fixture set follows from the values it selects, and the cell then chooses from it.** The
two are different things:

* the **possible** set is derived — the union of the fixtures exposing every value the cell selects;
* the **chosen** set is a selection from it, one fixture for the payload, one for the output, and one per
  dependency.

A cell whose possible set is **empty** has no concrete state it could be traced against, which is a
`no-suitable-fixtures` finding: the values it selects are exposed by nothing.

A stub or mock standing in for a contained design target is a claim about what the other side does, and it is
checkable: there must be a behaviour of the operation it stands for whose expected effects match what the
fixture declares. Where it stands for an unmodelled external dependency there is nothing to reconcile against,
and it is simply authored.

## 6 What Is Never Authored

Several things that look like documents are **derived views**, computed from the model and unable to disagree
with it. None is a document anyone maintains: the function catalog, the data dictionary, the call graph, what
calls a function, an interface's membership, cell ids, and a cell's fixture set.

An SLI's combined metrics are **not** in that list. The architect authors the queries, which name the metrics
explicitly; what is mechanical is only resolving those names against the metrics the design's functions emit. The
fact is authored and its resolution is checked — which is why an SLI querying a metric nothing emits is a finding
rather than an empty derivation.

**None of the derived views is present anywhere in a design directory**, and that is deliberate: they are
questions the tooling answers, not documents the layout holds. Once a design's claims are parsed into the model,
producing them is trivial, so storing them would only create something able to go stale.

**Written by the service is not the same as derived.** `BEHAVIOURS.md` is generated in full (§5.3.1) and its
required effects are nonetheless *authored*: they carry sourcing, not provenance, because they record the
architect's judgement about what must happen rather than a reading of what the design does. Who types the
characters has no bearing on whether a fact is derived — and conflating the two would collapse the separation
between required and expected effects that reconciliation depends on entirely.

Anchors, digests and attestations never appear in an authored document either. An architect may write a claim
directly into a document's frontmatter — it is read as a proposal, re-judged against the prose, then written and
signed — but the anchoring and the signature belong to the writer. An author producing their own would be
certifying their own work, which is what the attestation exists to detect.

### 6.1 Derived Embedded Diagrams

Some derived views are most useful as pictures, and are worth having in the design for a human to review: an
operation's call graph through the boundaries with the cross-cutting boundaries it crosses highlighted, or the
data model reachable from one function. These are **generated from the model**, so they are subservient to the
design's claims — and once embedded in a document they can fall out of step with what they depict.

A derived embedded diagram is therefore **falsifiable**, and carries exactly two things:

* a **key** — `{kind, target}` — naming what kind of diagram it is and the model address it was generated for.
  `kind` is a closed enumeration, for the same reason the perimeter vectors are: a generator has to know what it
  can be asked for. Passing the key back to the model regenerates the diagram, or its checksum, or both.
* a **checksum** — a single aggregate over the claimed positions the generator read.

The key is what keeps the metadata small. A diagram may read a great many positions — every type in a signature,
every function on a path — and none of them is listed, because the key is sufficient to recompute *which*
positions those were. The generator resolves the key, reads the same positions again, and aggregates. Two fields,
whatever the size of the diagram.

**The positions are not fixed; the key is.** As the design evolves it may add a type to a signature or a function
to a path, so what the key resolves to changes — and the key itself never does. That is what makes it a key
rather than a manifest: a manifest of positions would have to be rewritten every time the design grew, and would
be wrong in exactly the cases the checksum exists to catch.

**This is not provenance, and the difference is directional.** Provenance points at prose and falsifies the
claims made against it. A derived diagram is the reverse: it points at **claims** and falsifies **generated
prose** — the picture itself. Provenance also admits sources outside the design's scope, where a diagram's only
inputs are claimed positions inside it. Reusing it would mean inverting what it is for and widening what it
accepts.

Recomputing the checksum and finding it moved means the diagram no longer depicts the design. That is an
`out-of-date-diagram` finding with a **single mechanical resolution**: regenerate from the key. Nothing needs a
human, which is also why re-addressing may legitimately trigger it — renaming an element changes the labels in
the picture, so the picture really does need redrawing, and redrawing costs nothing.

**This is a global check.** It sits at no maturity level, so finding an out-of-date diagram never resets a
design's maturity — whether a picture in a document still matches says nothing about whether the design it
depicts is complete or accurate. The check is defined here, with this standard, because the documentation of a
design is this standard's concern; the next-unit-of-work detector surfaces it by being configured with it, as it
is for every check. The advisory count in §2.4 is global for the same reason.

Note what this check does *not* need: it requires no position in the design model at all, because the diagram's
metadata is not a claim. A check that required new positions would be extending what the design records, which
is a different kind of addition and not one a documentation standard should be making.

**The metadata is not a claim.** It asserts nothing about the boundary being designed; it records what this
diagram was generated from. It sits in the diagram's own frontmatter, inside the fenced block, so it cannot
collide with the document's `_claims`, and a diagram can be moved between documents without disturbing either.
Mermaid parses its frontmatter as YAML and ignores keys it does not recognise, so the metadata renders as
nothing:

```mermaid
---
kind: {diagram-kind}
target: {model-address}
checksum: {checksum}
---
...
```

A diagram carrying no such metadata is an ordinary hand-drawn diagram: the design's own prose, authored like any
other content, and never falsified against the model. Only an annotated diagram is derived, and only a derived
one can be found out of date.

## 7 Why The Layout Is Drawn This Way

Scope is directory-based, so a boundary's artefacts can leave a containing design's scope only by occupying a
directory of their own. That makes the cost of promoting a contained boundary depend entirely on how the layout
was drawn, long before anyone knows which boundary will be promoted:

* artefacts already in their own directory — promotion adds a `DESIGN.yaml` and an entry document to a
  directory that already exists. The scope walk stops there, the subtree becomes its own design, and **no
  document moves**;
* artefacts interleaved with the rest of the design's documents — every document must be moved until the
  subtree is separable, before anything else can happen.

Promotion is already costly enough in planning toil — a new design target needs its own design work, its own
ticket, its own review and its own NFR assessment — that the layout should not be adding to the bill.

Promotion changes a boundary's type in place, so **no address changes**: an address nests through the
boundaries that structure an element, not through the design target governing it. Extraction to a standalone
Library and relocation under a different parent are different operations, and both change the boundary's
position and therefore re-address it and everything beneath it.

# Appendix

A worked walkthrough of a design directory evolving. Each step is a snapshot: what the directory looks like,
and what changed to get there. Subtrees unaffected by a step are elided as `...`.

## A.1 The Entry Into Design

The entry into design is a boundary and its operations. The minimum starting state:

```
- {design-slug}
    - DESIGN.yaml
    - {design-slug}.md
```

* `DESIGN.yaml` — marks the design directory, declares the namespace, and names the checks the design is
  assessed against (§1.1). Without the configuration the directory cannot be assessed at all, so this is the
  first thing that exists.
* `{design-slug}.md` — the entry point and index; at this stage it also carries the boundary and its
  operations, {slug} & prose.

## A.2 The Boundary And Operations Move Out

```
- {design-slug}
    - DESIGN.yaml
    - {design-slug}.md
    - BOUNDARY.md
    - operations
        - {operation-slug-1}.md
        - {operation-slug-2}.md
```

* `{design-slug}.md` — gains a `## Boundary` section with a table of operations pointing at each one.
* `BOUNDARY.md` — the description of the design boundary.
* `{operation-slug-N}.md` — the prose of the operation, evolved and extended.

## A.3 The Functions Supporting The Operations

* `{operation-slug-N}.md` — grows a temporary list of the functions required to support the operation, {slug} &
  prose. Temporary, because functions belong to the boundaries that structure them and move there as those
  boundaries are drawn.

## A.4 The Design Records Decisions

```
- {design-slug}
    - DESIGN.yaml
    - {design-slug}.md
    - BOUNDARY.md
    - DESIGN-DECISIONS
        - interface
            - {decision-slug-1}.md
        - data-model
            - {decision-slug-2}.md
    - operations
        - ...
```

Concern subdirectories appear as decisions are taken under them, not before.

## A.5 The Contained Boundaries

```
- {design-slug}
    - DESIGN.yaml
    - {design-slug}.md
    - BOUNDARY.md
    - {domain-slug-1}.md
    - {domain-slug-2}.md
    - {shared-slug-1}.md
    - {dependency-slug-1}.md
    - {dependency-slug-2}.md
    - DESIGN-DECISIONS
        - ...
    - operations
        - ...
```

* `BOUNDARY.md` — grows the list of functions in the boundary, {slug} and prose.
* `{domain-slug-N}.md`, `{shared-slug-N}.md` — the boundary's description and the functions it contains.
* `{dependency-slug-N}.md` — same, for a dependency boundary, whose functions are thin shims: one-for-one
  translations of an operation of the boundary depended on.
* `{operation-slug-N}.md` — simplifies to description prose and a link to the function realizing it.

## A.6 A Contained Boundary Evolves Sub Boundaries

```
    - {domain-slug-2}
        - BOUNDARY.md
        - {sub-boundary-slug-1}.md
        - {sub-boundary-slug-2}.md
```

* `{domain-slug-2}/BOUNDARY.md` — was `{domain-slug-2}.md`.

A *single* trivial sub-boundary is enough: one child document plus the boundary's own is two documents, so the
directory is earned at the first child, not the second.

## A.7 A Sub Boundary Evolves Sub Boundaries

```
    - {domain-slug-2}
        - BOUNDARY.md
        - {sub-boundary-slug-1}
            - BOUNDARY.md
            - {sub-sub-boundary-slug-1}.md
            - {sub-sub-boundary-slug-2}.md
        - {sub-boundary-slug-2}.md
```

The same rule, one level down, to any depth.

## A.8 A Contained Boundary Is Promoted

```
    - {domain-slug-2}
        - DESIGN.yaml          <-- added
        - {domain-slug-2}.md   <-- added
        - BOUNDARY.md
        - {sub-boundary-slug-1}.md
        - {sub-boundary-slug-2}.md
```

* Two files are added to a directory that already existed. No document moves and no address changes (§7).
* The new `DESIGN.yaml` declares its own namespace and its check configuration — normally inherited from the
  design that contains it, which is what keeps a promoted boundary assessed exactly as it was the moment before
  (§1.1). A promoted directory with no configuration at all is unassessable from the moment it is promoted, which
  is the one way to get promotion wrong here.
* Promotion should cost no human review: the containing design's traces now stop at the promoted target rather
  than walking through it, and should regenerate to the same expected effects. A promotion producing a wave of
  `redesign-required` has changed something it should not have.

## A.9 The Data Model

Inline first, as a `## Data Model` section of the boundary's own document with mermaid diagrams and prose. When
it becomes cumbersome:

```
- {design-slug}
    - DESIGN.yaml
    - {design-slug}.md
    - DATA-MODEL.md
    - BOUNDARY.md
    - ...
```

* `BOUNDARY.md` — loses its diagrams; `## Data Model` becomes an overview and a link.

Then sub models, and sub sub models:

```
    - DATA-MODEL
        - DATA-MODEL.md
        - {sub-model-slug-1}
            - DATA-MODEL.md
            - {sub-sub-model-slug-1}.md
        - {sub-model-slug-2}.md
```

And the same on a contained boundary, where it is what earns that boundary its directory:

```
    - {domain-slug-2}
        - BOUNDARY.md
        - DATA-MODEL.md
        - {sub-boundary-slug-1}.md
```

## A.10 The Manifest, System Interface And Endpoints

```
- {design-slug}
    - DESIGN.yaml
    - {design-slug}.md
    - MANIFEST.md
    - SYSTEM-INTERFACE.md
    - ENDPOINTS.md
    - DATA-MODEL.md
    - BOUNDARY.md
    - ...
```

On a Library, only `MANIFEST.md` of the three exists. When the endpoints become unwieldy:

```
    - ENDPOINTS
        - ENDPOINTS.md
        - {interface-slug-1}.md
        - {interface-slug-2}.md
```

* `ENDPOINTS.md` — keeps the per-vector assessment and points at the groups, under §4's `X.md` becoming
  `X/X.md`.

## A.11 The NFRs

NFRs are considered, and the answer is that none of the categories applies here:

```
- {design-slug}
    - DESIGN.yaml
    - {design-slug}.md
    - NFR.md
    - ...
```

* `NFR.md` — a section per category considered, each recording an exemption and its reason. A complete
  assessment, and often the whole of it.

Then a rule is incorporated and functions are selected onto a cross-cutting boundary, which is a boundary and
earns its own document:

```
    - NFR
        - NFR.md
        - {xc-slug-1}.md
        - {xc-slug-2}.md
```

* `NFR.md` — keeps the assessment overview: every rule in scope, and what the design did about each.
* `{xc-slug-N}.md` — one cross-cutting boundary: the rules it imposes and the functions it selects.

Then the rules acquire the non-functional behaviours their deviations demand, and each earns its own directory:

```
    - NFR
        - NFR.md
        - {xc-slug-1}
            - {xc-slug-1}.md
            - {rule-slug-1}
                - BEHAVIOURS.md
            - {rule-slug-2}
                - BEHAVIOURS.md
        - {xc-slug-2}.md
```

* `{rule-slug-N}/BEHAVIOURS.md` — the behaviours at every cell this rule's deviations reach, across whichever
  operations reach the functions it governs. A non-contiguous view of the condition space by design.

## A.12 A Function Earns Its Own Document

```
    - BOUNDARY
        - BOUNDARY.md
        - {function-slug-1}.md
```

* `BOUNDARY.md` — keeps the functions that are still inline, and points at the one that moved.

* `{function-slug-N}.md` — function prose, pseudocode, sequence diagrams, and what it calls.

A function moves out on its own account. Its siblings stay where they are, and a boundary holding most of its
functions inline and one or two separately is the normal state rather than a stage on the way to somewhere.

## A.13 Metrics And Observability

```
    - operations
        - {operation-slug-1}
            - OPERATION.md
            - SLIs.md
        - {operation-slug-2}
            - OPERATION.md
            - SLIs.md
```

* `{function-slug-N}.md` — gains the metrics it emits.
* `OPERATION.md` — was `{operation-slug-N}.md`; the second document earns the operation its directory.

## A.14 The Behaviours Accrete

```
        - {operation-slug-1}
            - OPERATION.md
            - BEHAVIOURS.md
            - SLIs.md
```

The dimensions themselves are the operation's, recorded in `OPERATION.md` with their provenance (§5.2). What
grows here is the space they generate. It grows in this order, and each step adds cells that stay uncovered until
a behaviour occupies them or an inter-condition validity rule collapses them:

1. the base given tree, from payload conditions;
2. the when-set, from parameter options;
3. cross-cutting dimensions, as each NFR rule's selector resolves against real functions — the nominal values
   extend this document, while the deviations become the rules' own `BEHAVIOURS.md` under `NFR/` (§5.3, §5.8);
4. dependency-state dimensions, once the call tree reveals which dependencies the operation reaches.

Then, when it becomes unwieldy:

```
            - BEHAVIOURS
                - BEHAVIOURS.md
                - {cell-id-1}.md
                - {cell-id-2}.md
```

## A.15 The Fixtures

```
    - fixtures
        - {fixture-slug-1}.???
        - {fixture-slug-2}.???
        - {fixture-group-slug-1}
            - {fixture-slug-3}.???
```

Fixtures can be anything, and their extension follows their content.

## A.16 The Required Results

* `BEHAVIOURS.md` — its frontmatter gains the call trees and reconciliation.

A trace starts at the operation's realizing function with the cell's entry condition and its fixture set, and
follows each call into the called function's own description. It stops at a contained design target's
operation, at a dependency boundary's shim, and at a function that calls nothing further; it walks straight
through everything else.

# Rationale

**Why there are two devices rather than one.** The containment rule alone does not predict `operations/` or
`fixtures/`, and forcing it to would mean either scattering satellite artefacts across the design root or
inventing a boundary that does not exist to hold them. Contained boundaries are the containment axis, so the
directory tree can simply *be* the boundary tree; operations, NFRs, fixtures and decisions are flat categories
of artefact hanging off the design, and a category directory is the honest way to say so. Keeping the two
devices named and separate stops the containment rule from being quietly weakened to accommodate cases it was
never about.

**Why the threshold is a second document rather than a second boundary.** An earlier form gave every contained
boundary a directory of its own, on the grounds that promotion is then always free. It over-reaches: a shared
logic boundary may be nothing but a handful of function descriptions in prose, whose natural shape is one
document with a subsection per function, and pushing that into a directory of its own buys nothing. Waiting for
the second document costs, at worst, one file move if that boundary is later promoted — which is a different
order of cost from untangling interleaved documents, the case §7 actually exists to prevent.

**Why the budget is advisory rather than a rule.** The model cannot tell whether a boundary with eleven
functions is badly drawn or simply a boundary with eleven functions. A forcing threshold would make architects
work around a rule that is right most of the time and wrong some of it, and would put the tooling in the
position of restructuring a design on a count. A cumulative advisory count puts the observation in front of the
architect, keeps the decision where the judgement is, and — being configurable — does not hard-code one
project's sense of "too big" into every project's layout.

**Why a document's granularity is treated as a design decision rather than a formatting one.** An anchor covers
every section nested beneath it, so a claim anchored at a broad section is re-judged whenever anything under it
changes. Document and section structure is therefore the lever that controls re-judgement precision, with
nothing to configure — well-structured prose is rewarded with less spurious re-judgement. Saying so explicitly
is what turns splitting a section from an editing convenience into the mechanism it actually is.

**Why the notation for endpoints follows the endpoint kind.** One house format across every kind would mean
describing a topic as though it were a route, which is the same overreach as inventing a notation for service
level indicators instead of requiring OpenSLO. OpenAPI describes a server's offered HTTP surface and has no
vocabulary for publish/subscribe; AsyncAPI has exactly that vocabulary and is the mature standard for it.
Requiring each where it fits keeps `ENDPOINTS.md` one document while leaving each kind described in the
notation that existing tooling can actually read.

**Why outbound calls are excluded from the endpoints.** An endpoint is a point on the service's own perimeter —
something the outside reaches, or something the service publishes as part of its contract. A call the service
makes to a neighbour is neither: the contract belongs to the other side, and restating it here would create a
copy with nothing keeping it in step with the original. Modelling it as a dependency boundary's shim, with the
other side's own contract held as a checksummed reference, gives the design what it needs — detection when that
contract changes — without claiming ownership of something it does not own.

**Why duplicating a behaviour across two rule views is tolerable.** It looks like the thing the `duplicate-claim`
advisory exists to prevent, and it is the opposite: that advisory catches documents drawn along the wrong lines,
where two of them compete to state one fact by accident. Here the duplication is predicted by the layout, the
values are identical, and the fold is well defined — so the behaviour stands, and what the design loses is at
most an acknowledgement. The consequence downstream is equally mild: a behaviour described twice may be tested
twice, and the build gates require at least one failing test rather than every new test failing, so a chunk that
re-adds an existing test alongside at least one new behaviour passes exactly as it should. Neither the claim
mechanism nor the delivery gates are disturbed by saying a true thing twice.

**Why a "decision log" is a register rather than a log.** The model holds a design's current state and not its
history: superseded decisions are retained because "this was considered and replaced" is still true now, while
the elements they produced are deleted, and a satisfied change request is deleted outright. Calling the
directory a log invites append-only history, and a reader who finds a resolved request or an obsolete element
has to work out whether it still matters — where the answer is always no.

**Why this standard does not enforce anything.** Two constraints make enforcement impossible rather than merely
undesirable. Every layout parses, because the model is the fold of every claim in scope and no document is an
entity — so there is nothing in the schema that could reject one. And a design may be brought under the model
having arrived laid out however it was, at any state of maturity, which the workflow has to handle without
reorganising it first. A standard that could only be followed by new work written through the tooling would be
a standard for a minority of the designs it is supposed to govern.
