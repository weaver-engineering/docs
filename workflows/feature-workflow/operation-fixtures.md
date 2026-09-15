# Operation Fixtures

## Context
* [Use Cases §2.1](use-cases.md) - Step Contracts, and the `STATES` field this document's own artefact is the
  target of
* [Use Case Operation Template](../../templates/USE-CASE-OPERATION-TEMPLATE.md) - the fill-in-the-blank shape this document's
  rules apply to
* [Architect Solution](architect-solution.md) - what an operation document feeds, Feature-wide
* [Weaver Engineering Workflows §3](../weaver-workflows.md) - why Analysis produces no behaviour document of
  its own, and what Design references instead
* [Condition Model](@agent-plugins-docs/docs/design/design-assistant/datamodel/condition-model.md) - projection
  (`given`/`when`), the formal home for a parameter dimension

This is the rulebook for writing one operation's condition space: the dimensions its fixtures must expose, the
invariants they must witness, and the cells they combine into. [Use Case Operation
Template](../../templates/USE-CASE-OPERATION-TEMPLATE.md) is the shape; this document is why the shape is what
it is, and how to fill it in. An operation document itself carries none of this — a rule repeated in every operation
document either drifts out of step with this one or crowds out the thing that document exists to show: why
*this* operation's dimensions are these.

## 1 Writing Fixtures Is The Review

**This is the single most important thing this document has to say.** Writing — or accepting — an operation's
fixtures is not preparation for a later review. It is the review, and for a behaviour whose fixtures are
wholly Analysis's own, it is the *only* one that ever happens.

Nothing downstream re-derives what is written here; it is referenced. Where every fixture behind a design's
behaviour is external to that design — authored here, referenced there — the design's own approval check
asserts that behaviour mechanically: it does not raise it, does not present it, and never puts it in front of
an architect. That is the correct trade — a fact stated once should not be ratified twice — but it has a sharp
consequence. A fault in a fixture is never questioned again. Nothing downstream checks a fixture's *truth*,
only consistency *with* it, so the moment it is written is the last point at which a wrong required behaviour
can be caught cheaply. After that, everything is correct by construction, all the way down to whatever gets
built against it.

So the enumeration this document teaches exists to serve that moment. A condition space is drawn out —
dimensions, invariants, cells — specifically so a human can look at the table and judge whether the
behaviours it describes are the ones actually wanted, rather than reading a narrative and trusting that its
coverage is complete. §4's immaterial pruning has the same root: an unreviewable table defeats the purpose
as surely as an incomplete one. §3's conditions-before-fixtures rule has it too — a fixture written against a
half-formed idea of the conditions encodes that half-formed idea, and nothing downstream will ever disagree
with it.

**What a fixture is not.** Not a test case, and not an example. It is the statement of required behaviour
that everything written later is checked against. Treat it with the weight that carries.

## 2 Three Categories, Three Obligations

Not everything that could vary an operation's behaviour is a dimension. Three categories exist, and each
carries a different obligation:

| Category | What it is | Obligation |
|---|---|---|
| **Dimension** | varies what the operation does | every value exposed across the kept cells |
| **Invariant** | defines what the operation does *without* varying it — present or absent, the rule is the same | witnessed **exactly once**, by a leaf of the main success scenario — seen working, not seen surviving a fault |
| **Route to a value** | neither — a fixture-level property of *how* a dimension reaches one of its values | no obligation of its own; the reason one value may legitimately have several fixtures |

**Dimension.** Multiplies the condition space — every value needs its own cell, or its own place within a
cell, somewhere the table can be checked against it.

**Invariant.** Earns no cell, because a cell exists to be missing something, and an invariant is never
missing — multiplying it would produce cells that differ in nothing. Because it earns no cell, the table can
never catch its own absence; the exactly-once obligation is what stands in for that. Falsifiable from both
ends: the invariants table names the cell that witnesses each one, and that cell names the invariant back
(§6). Either half without the other is a fault — an invariant with no witnessing cell is a requirement
nothing demonstrates, and a cell claiming an invariant the table doesn't list claims something of no one.
"Exactly once" is a real constraint, not a formality: two rules sharing one name, each witnessed at a
different cell, is a sign the invariant was drawn wrong and needs splitting — being witnessed twice is what
exposes it.

**Route to a value.** Something changes *how* a dimension reaches one of its values without changing what the
operation then does — modelling it as a second dimension would multiply the space to observe an outcome that
doesn't vary, and modelling it as nothing would leave a value's second fixture unexplained. Naming the
category is what stops a route being tried as a dimension, found not to earn its keep, and re-tried the same
way next time.

## 3 Conditions Before Fixtures

**Write the dimensions before writing a single fixture.** This is a rule earned the expensive way: writing
fixtures first and reading the conditions back out of them produced three successive, and wrong, accounts of
one fault condition across three review rounds — each account correct about what the fixture in front of it
showed, and wrong about what the operation actually required, because a fixture only ever encodes whatever
conditions its author had in mind at the time. Enumerating the dimensions first settles it in one pass:
everything a fixture needs to witness is decided before the fixture exists to witness it, rather than
recovered afterward from whatever happened to get written down.

The dimensions themselves usually surface things a narrative alone does not:

* **A condition can be a route, not an axis** (§2) — found by asking what actually distinguishes two ways of
  reaching the same outcome, a question fixtures-first never poses.
* **Some content is carried by every fixture rather than varying** — a constant is not a dimension, and
  modelling it as one doubles a table to observe something that never changes.
* **Rank order is load-bearing** (§4) — invisible until the cells are actually nested, at which point the
  wrong order turns what should be two failure cells into every combination of them, repeated.
* **A dependency can be missing a value nobody thought to ask for** — the requirement never said what happens
  when a dependency is absent, and no amount of fixture-writing surfaces a response nobody has specified; only
  enumerating the dependency's own states does.

## 4 Ranking And Pruning

**Rank is stated once**, in the dimensions table (§1 of the operation document), never repeated per
dimension. A rank carried on each dimension's own section would make ranks appear out of sequence whenever
categories interleave — a payload dimension opening at rank 2 because a dependency outranks it — which reads
as an error and invites a renumbering that would break the nesting it's meant to describe. Rank is a property
of the space's own nesting, not of any one dimension.

**Rank order is chosen, not incidental.** Put the dimension whose values end the operation earliest outermost
in the tree — nesting decides how much a pruned leaf actually removes, and the wrong order turns what should
be a handful of distinct failures into that many repeated once per surviving combination beneath them.

**A cell whose outcome does not vary below it is a leaf.** It carries what the operation establishes and its
own fixtures; a cell with real permutation beneath it carries only its own identity and defers to its
children.

**Two prune reasons, and they mean different things:**

| Reason | Says | Guard |
|---|---|---|
| **Invalid** | this combination cannot arise | — |
| **Immaterial** | this combination can arise, but establishes nothing a kept cell doesn't already | every value of every dimension must still be exposed by *some* kept cell |

Get the guard wrong and a prune is a gap wearing a prune's own clothes — a value pruned out of every cell it
appeared in is a value the operation was never actually asked about. Immaterial pruning is what makes a space
reviewable rather than merely complete: an unpruned product is exactly what a human cannot review, and it is
also what would otherwise demand a fixture written per row before design ever saw the table at all. This is
work analysis does, deliberately, before the space reaches anyone else — not a shortcut left for design to
take later.

## 5 Extensions

**The extension test is about the actor, not the flow.** A cell is an extension of the use case where the
actor has to do something different as a result — not wherever the operation's internal path differs. A cell
that reaches its result by a different route and still hands the actor exactly what they asked for is part of
the main success scenario's own condition space, however many branches the operation took to get there.

**A fact in the report is never an extension.** Where an operation's own deliverable includes a report — a
change report, an error report, whatever form it takes — reporting something is part of delivering it, not a
departure from delivery. A warning, a partial result named as such, an outcome the report states plainly: all
of these are the main success scenario doing its job. What makes a cell an extension is the operation *not
delivering* — nothing written, nothing the actor asked for produced — which is a different condition from the
report simply having something to say.

### 5.1 Failures Nobody Can Name Yet

Every unhappy path a human can reasonably anticipate belongs in the condition space, as a cell like any other —
and enumerating a dependency's own states is what surfaces most of them (§3). But analysis will not name every
failure a real dependency can produce, because it has not chosen the dependency: which failure modes exist is
Architecture's decision to make later. What analysis can still state, and should, is the **blanket
requirement** — "any error is surfaced gracefully, with enough detail to identify its cause" — carried as an
invariant (§2) rather than multiplied into cells nobody can yet describe. That blanket statement is what later
obliges a Service to have a graceful answer for each real failure mode its chosen dependencies turn out to
have.

**A failure Design discovers that this analysis gave no basis to anticipate is an Analysis miss, not Design's
to fix.** Design may find that a dependency it chose fails in a way no cell here covers and no invariant
reaches. It does not get to add the requirement itself: that would be the solution deciding what was required,
which is the one thing this whole separation exists to prevent. It flags it back, this document gains the
dimension or the cell, and the fixtures follow — the same route any other gap in the requirement takes.

## 6 Cell Notation

**A cell is referenced with `@`.** `@1.2.1.2` reads as *at cell 1.2.1.2*, and is never a section number or an
ordinal — the sigil is what tells a reader, and a mechanical check, which kind of number they're looking at.
A node's own label in the tree stays plain, with no `@`: it *defines* a cell, and only a reference *to* one
takes the sigil.

This earns its own convention twice over. **Mechanically**, "every `@` reference resolves to a real node" is a
check — and one that has already caught real corruption: a version string and section ids a bulk edit had
silently turned into cell references. **Visually**, it is what makes a coverage claim checkable by eye: every
`@` in the invariants table (§2) and the coverage section is a cell a reader can go and find in the tree, so
scanning for which cells are named — and which values are named nowhere — is a matter of looking rather than
of counting. A convention that only read well would not give that; a sigil that marks exactly one kind of
number does.

**The cell table is a tree, not a markdown table.** A column per dimension leaves most cells repeating a word
and squeezes the two columns that actually carry the argument against the margin. Nest one node per line
instead, each identified by its own number, the dimension it adds, and that dimension's ordinal:

```
* 1.1.2.1 - **figure:** `absent`
  > **Establishes:** …
  >
  > **Witnesses:** *invariant name* (§5), and how
  >
  > **Payload:** [`fixture-set.fixture`](…)
  >
  > **document-store:** [`fixture-set.layout`](…)
  >
  > **Result:** [`fixture-set.fixture`](…)
  >
  > **Stdout:** `fixture-set.report` — [txt](…) · [json](…)
  >
  > **Stderr:** [`fixture-set.fixture`](…)
```

Everything a node says about itself is block-quoted beneath it, one statement per line, with a bare `>`
between statements — omit that and consecutive quoted lines join into one paragraph, losing the separation
the structure exists to give. Pruned nodes are shown at their own position in the tree, with their reason
inline, never collected into a second table: a prune is a fact about a position, and moving it elsewhere
loses that.

## 7 Fixtures By Role, Always Set-Qualified

A leaf's fixtures are named by the role they play, not by position:

| Role | Holds |
|---|---|
| **Payload** | what goes into the operation |
| **{dependency-slug}** | the state one dependency it reads or writes presents — one line per dependency, **named for that dependency** |
| **Result** | what the operation leaves in a dependency it writes to |
| **Stdout** | what it prints on its answer channel, a report included |
| **Stderr** | what it prints on the channel reserved for the tool itself failing, where that is a real leaf of this space rather than a condition nobody modelled |

**At most one line of each role per leaf**, which is the second reason for naming them: a leaf's own coverage
reads off its block at a glance — a missing Result, a dependency nobody gave a state, a cell printing nothing
— instead of having to be counted or inferred from position. Two lines of one role is a sign the cell is
really two cells.

**Every fixture name is set-qualified**: `{fixture-set}.{fixture}`, never a bare name. A bare name collides
across an operation's own fixture sets far more often than it looks like it should — `before` is not one
document, it is however many fixture sets an operation needs, each with its own `before`. Pointing a leaf at a
bare fixture index instead of a role-qualified name is how fixtures go unreferenced anywhere in the document
that's supposed to account for them.

Naming fixtures by role rather than position also makes two real shapes visible that a flat list hides: a
cell can have **no payload at all**, where a dependency's own state is the entirety of what varies for it; and
a cell can **reuse another cell's payload**, with only its dependency line carrying the whole difference
between them. Neither is expressible in a scheme that assumes one fixture per cell in a fixed order.

## 8 Report Fixtures Are Fixtures, Not Prose

Where an operation reports on what it did, the report is a fixture like any other — a concrete artefact
witnessing a state — not a place for the analysis's own voice to appear.

**What a report should contain and how it should read is the use case's to demand, not this document's.** A use
case may want a narrative, a table, a running commentary, a single line, or nothing at all; what the operation
reports is a requirement like any other, and it belongs in the operation's own dimensions and cells. Where
`number-document-sections` asked for something terse and line-oriented — a section head, one item per line, its
location, a short reason, readable at a glance — that was *that* actor's ask, arrived at by analysing what they
needed, and it is an example of the kind of thing a use case decides rather than a shape reports take.

**A machine rendering is the one part that generalises, and it generalises for a reason worth stating.** Where
a caller declares it will parse the answer, that caller is, in this workspace, almost always an agent — and an
agent has no memory between sessions and no way to ask afterward what happened. It cannot recover from prose
mixed into the answer, and it cannot come back later to find out what a run did. That is a cross-cutting
concern of working this way at all, not a property of any one operation: so wherever an operation has a machine
rendering, expect it to be one parsable document and nothing else on the stream, and to state its own outcome
explicitly rather than leaving that to be inferred from what is present or absent. An operation whose analysis
concludes otherwise should say why.

**Where success is stated on more than one channel, the channels must agree.** `number-document-sections` has
both a report and an exit code in front of its caller at once, and made the exit code agree with the report
rather than appear in it — a disagreement there is not a second opinion, it's an operation that cannot be
believed on either channel. Whether an operation has that shape at all is its own analysis to settle; that
channels stating the same fact must not be able to contradict each other is general.

**Where an operation declines a judgement call, ask whether the caller needs to be told.** An opinionated rule
will sometimes be wrong, and a caller who never learns where the opinion was applied has no way to check it —
which is why `number-document-sections` warns rather than declining silently. Whether that matters here is a
question for the analysis, but it is a question worth actually asking rather than defaulting to silence.

**Guard against explanatory prose creeping into a report fixture.** Whatever shape the use case asked for, a
report fixture written across several review rounds tends to accumulate paragraphs justifying what happened —
that is the analysis talking, not the tool, and it belongs in this document's own prose or the operation
document's Rationale, never in a fixture meant to witness exactly what the tool itself would produce.

## 9 Fixture Layout

Fixtures live in a sibling `fixtures/` subdirectory next to `USE-CASE.md` and `operations/` (Documentation
Standards §2.1) — never inline in either. One directory per **fixture set**,
`fixtures/{fixture-set-slug}/`, holding:

* `FIXTURES.md` — an index: which fixtures this set holds, what each one witnesses (cell reference and role),
  and a Rationale explaining what's actually being exhibited and why this combination, together, is the
  payload it is.
* the fixtures themselves, as the real artefacts they are — a markdown document, a `.txt` report, a `.json`
  report — never abstracted into a format that isn't what the operation would actually produce or consume.

A fixture set may hold one fixture or several under their own headings, grouped however they're naturally
cohesive — a command's rendered reports together, say. One file per fixture is not required, only that each
fixture resolves to its own addressable location.

**One rendering per fixture set that produces one**, not one per input permutation the rendering happens to
cross. A report's rendering logic applies uniformly across every cell that produces a report; it is a fact
about the operation's output, not a fact about any one input, so it is held once per producing set rather than
repeated.

# Rationale

**Why this document exists separately from [Use Case Operation Template](../../templates/USE-CASE-OPERATION-TEMPLATE.md).**
The template is a shape to fill in; this is why the shape is what it is and how to fill it in correctly.
Folding both into one document would mean every operation document either carries this whole rulebook inline
— repeated, and eventually drifting, across every operation an analysis ever writes — or the template ships
with no guidance at all and each author reconstructs the rules from the worked example by inference. Neither
is what happened here: the rules were each earned in a real review round, and stating them once, separately
from any single operation's own content, is what keeps that cost paid only once.

**Why §1 comes first, ahead of any mechanical rule.** Every rule after it exists to serve the review §1
describes — an unreviewable table (no pruning), an unfalsifiable invariant (no exactly-once obligation), a
fixture nobody can find (no set-qualified naming) all fail the same way: they make it harder for the one
person who will ever be asked to notice something wrong. Stating that first is what makes the rest of the
document read as one argument rather than a list of unrelated conventions.

**Why conditions-before-fixtures (§3) is stated as an obligation, with its cost named, rather than a
preference.** A softer framing — "consider writing conditions first" — invites exactly the failure mode that
produced three wrong accounts of one fault condition: fixtures are concrete and satisfying to write, and
conditions are abstract and easy to defer. Naming the actual cost of getting the order wrong is what makes
the rule worth following under time pressure, not just in principle.
