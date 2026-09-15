# Use Cases

## Context
* [Feature Workflow](feature-workflow.md) - the workflow step (`Analyse Feature`) that produces use cases and
  their operations' condition spaces
* [Initial Feature Document](initial-feature-document.md) - what a Feature is, and how it relates to a use case
* [Analysing A Feature](analysing-a-feature.md) - how a use case's operations relate to a Feature's own
  capabilities, and what turns one into a benefit
* [User Personas](user-personas.md) - the goal/frustration pair a use case's human actor is formalized as
* [The Product/Service Model](../../standards/product-service-model.md) - where Use Case sits in the wider
  Platform/Product/Service continuum
* [Architect Solution](architect-solution.md) - what a use case's operations and perceived boundaries feed
  into once analysed
* [Weaver Engineering Workflows §5](../weaver-workflows.md) - the four-layer interface decision (use case,
  Architecture, Design, Product Offering) a use case's own interface statement is only the first layer of
* [Use Case Template](../../templates/USE-CASE-TEMPLATE.md) - the fill-in-the-blank shape this document describes
* [Documentation Standards §2.1](../../standards/documentation-standards.md#21-the-directory-per-entity-pattern) -
  the directory-per-entity pattern a use case's `operations/` and `fixtures/` subdirectories follow
* [Use Case Operation Template](../../templates/USE-CASE-OPERATION-TEMPLATE.md), [Operation Fixtures](operation-fixtures.md) -
  the shape and the rules for what a Step Contract's `STATES` field points at

## 1 What A Use Case Is

A use case is a set of steps the actor and any supporting actors perform to achieve the actor's goal — the happy
path, with possible extensions branching off it (see the [Use Case
Template](../../templates/USE-CASE-TEMPLATE.md)'s Main Success Scenario and Extensions). A step is not always
something the actor does: it may equally be what the product does in response to the actor's own step — steps
alternate between actor action and system response as the scenario plays out, not a flat list of things the actor
alone does.

"Achieve a goal" is what makes a use case a real requirement rather than an implementation detail described from
the outside — a use case exists because an actor needs something to be true afterward, not because some endpoint
happens to be callable.

A use case is written independent of any one functional boundary. Nothing about a use case's own definition — its
actors, its goal, its steps — commits to which Service (or Library, `concepts/service.md` §1) actually realizes
it; that binding is `Architect Solution`'s job, made while working out *how* to satisfy the use case, never
Analysis's. A use case that ends up split across more than one Service is still one use case: what varies is how
many Services its operations resolve to, not how many use cases were written. A use case may still *name* a
boundary it perceives an operation crossing (§2.1) — that names a hypothesis for `Architect Solution` to test,
not a commitment binding it the way naming a real Service would.

### 1.1 A Use Case Is Not "One Operation"

Use case boundaries are set by the actor's real goal, never by how many operations, or how many different
Services, it takes to reach it. Slicing a use case down to match a single operation — or a single Service's own
interface — is a real, recurring mistake, not a hypothetical one: three existing use cases (`index a path`,
`search documentation`, `extract document content`) are each really just one operation apiece serving a single
actor goal that was never written down as its own use case — "as an agent, I need indexed, searchable
documentation, so that I can efficiently find the curated truth for a subject (and its justification/rationale,
if wanted) without bloating my context." That one goal is what should have been the use case; indexing, search,
and extraction are three of its operations, likely against three different Services (an Indexing Service, a
Search Service, a Reading Service) chosen later by Design — not three use cases of their own.

The test for where a use case's boundary actually sits is the actor's own goal, stated in a sentence starting
"so that" — not "how many times does the actor cross the system's boundary to get there," and not "how many
Services end up involved." A use case satisfied by a sequence of several operations against several different
Services is the normal case, not an exception needing special handling.

## 2 Actors, Operations, And Logical Response

An actor is whoever (or whatever) invokes the use case's operations — an end-user, an internal developer of
another project, or another system entirely. A human actor is formalized as a [User Persona](user-personas.md): a
stated Role, Goals, and Frustrations that make the use case worth having, plus a Technical Proficiency (User
Personas §1) — a systematic actor has no persona, since it has no goal or frustration of its own (User Personas
§2). A use case may also involve supporting
actors: other systems or roles the primary actor depends on to reach their goal, without being the one pursuing it
themselves.

The use case's steps decompose into two things, not one: **operations** — the actor invoking one of *some*
Service's own interface endpoints — and, for each operation, the **combined logical response** the Service is
required to produce in return. An operation names the crossing; the logical response is what must be true once
the Service has actually handled it — together they're what a step "does," whichever side of the boundary that
step is written from (§1). A single use case commonly performs more than one operation on the way to its actor's
goal, very often against more than one Service — an elicitation dialogue is several round trips, not one, and the
Indexing/Search/Reading example above is several operations against three different Services — and each is
written as its own numbered step (see the [Use Case Template](../../templates/USE-CASE-TEMPLATE.md)'s Main
Success Scenario).

A use case's own statement of an operation names only which *kind* of interface it requires (UI/CLI/API — an
actor may itself be systematic) and what that interface must be capable of initiating. It is the first of four
separate decisions about that interface, not the whole decision — see [Weaver Engineering Workflows
§5](../weaver-workflows.md) for the other three (`Architect Solution` decides the technology, `Design Service`
crystallizes the concrete specification, `Architect Feature`'s Product Offering decides how it's actually
delivered for consumption).

An operation either invokes a Feature's own capability directly, or is specified inline when no capability yet
covers what it needs — see [Analysing A Feature §4](analysing-a-feature.md) for the distinction and how each is
derived into a Required Behavior.

### 2.1 Step Contracts

A step that performs an operation carries its own **Step Contract**: the boundary it's perceived to cross, and
a pointer to that operation's own condition space.

```
N. {Step name}
    **BOUNDARY:** {the boundary this operation is perceived to cross, optionally with a guess at what kind of
    thing it is — a CLI, an API, a UI — or any further hypothetical detail worth recording}
    **STATES:** [operations/{N}-{operation-slug}.md](operations/{N}-{operation-slug}.md) — the entry states
    this step admits, the state each establishes, and the fixture exposing each
    {narrative description of the step}
```

A step that is pure branching or narrative — nothing crossing a boundary — carries neither, and once assessed is
written as a markdown blockquote:

```
> N. {narrative description of the step}
```

The blockquote is the marker itself, not decoration: it's what a reader has to tell "assessed, deliberately no
Step Contract" apart from "not yet assessed" — otherwise indistinguishable, since both would render as identical
plain prose. A step still in the use case's first-pass, no-Step-Contracts-yet state (below) stays unquoted; the
blockquote only appears once a step has actually been looked at and found to cross no boundary.

**A step does not have one entry state and one exit state — it has a condition space.** An operation's
fixtures bracket a payload dimension, a dependency dimension, a parameter dimension, however many of each
actually vary its behaviour — not a single `Given` and a single `Then`. `STATES` points at a document holding
exactly that: every dimension the operation's fixtures must expose, the invariants they must witness, and the
cells they combine into, one per operation, filed at
`docs/analysis/use-cases/{use-case-slug}/operations/{N}-{operation-slug}.md` ([Use Case Operation
Template](../../templates/USE-CASE-OPERATION-TEMPLATE.md), [Operation Fixtures](operation-fixtures.md) for how
to write one). This is the bracketing this repo used to attempt with a single `Given`/`Then` pair per step; a
pair can only ever express whichever one cell its author happened to have in mind, so it has been replaced.

**Extensions are cells of that same condition space, not operations of their own.** `1a`, `1b`, `1c` are
different outcomes of step 1's own operation, reached under different conditions — sharing one operation
document is the point, since it's the one place the whole set of input conditions an operation actually faces
is visible together. An Extension accordingly carries no Step Contract of its own; its `BOUNDARY` is the same
as the step it branches from (a property of the step, not of a cell), and its states are whichever cell of the
operation document it corresponds to.

**Boundary is a hypothesis, not a commitment, and the hypothesis isn't limited to which boundary.** It's the use
case's own perception, at Analysis time, of where an operation crosses — before any Service exists to actually
own it — and that perception can go beyond a bare name. It may also guess at *what kind* of thing the boundary
looks like (a CLI, an API, a UI) and carry as much further hypothetical shape as actually seems useful to write
down: a bare name is enough where nothing more is obvious, and a fuller sketch is just as legitimate where the
use case already has a strong sense of it. None of it binds anything — it's exactly the same kind of guess as
naming the boundary at all, just at whatever resolution is worth stating.
[`Architect Solution`](architect-solution.md) is what turns a use case's whole set of perceived boundaries and
operations into the real Service topology and data flow ([Weaver Engineering Workflows
§7](../weaver-workflows.md), Service Flows): confirming some, merging others, splitting others. That reframing —
not the use case's narrative alone — is what actually identifies the Services being designed, which is why a use
case is allowed, and expected, to name a boundary — and guess at its shape — it turns out to be wrong about:
doing so is what gives architecting a starting hypothesis to work from, not a decision already made on its
behalf.

**A later operation's own dependency and payload states account for what an earlier one established.** Where
one operation's result becomes another's entry condition, that's stated as one of the later operation's own
dimension values (its document, its Context, names the operation it follows) — the chaining a single `Then`
used to carry is now just an ordinary dependency or payload state of whichever operation comes next.

**A use case is drafted before it's contracted.** The Main Success Scenario and Extensions are a complete,
reviewable statement of the actor's goal with plain narrative steps and no Step Contracts at all; `BOUNDARY`
and `STATES` — and the operation document `STATES` points at — are added in a second pass, once the steps
that cross a boundary are identified. A use case with no Step Contracts yet is an earlier, legitimate state of
the document, not a malformed one — and none of its steps are blockquoted yet either (§2.1, above): that marker
is only added once a step has actually been assessed and found to cross no boundary, so an unquoted step in a
first-pass draft means "not yet reached," never "assessed, no contract."

This is what an earlier version of this document, and of [Use Case
Template](../../templates/USE-CASE-TEMPLATE.md), called a use case's **Technical Interpretation** — solution
independent pseudocode, held in the use case's own Appendix, that a design step would later bind and compare
against, pseudocode-to-pseudocode. That comparison no longer happens: Design reconciles against required effects
derived from the use case, not against a pseudocode restatement of it, so there is nothing left for a separate
pseudocode form to serve. Step Contracts keep only what Technical Interpretation was still actually doing —
identifying operations and fixing the state around them — stated directly on the steps and their own operation
documents, in the same prose the rest of the use case is already written in.

## 3 Scope

A use case stays at the Product level: it is never filed under, or owned by, any one functional boundary's own
`docs/services/{slug}/`, and it is not owned by any one Feature either — it may invoke capabilities drawn from more
than one Feature on the way to its actor's goal (see [Analysing A Feature](analysing-a-feature.md)). This is what
keeps a use case's requirement stable even if `Architect Solution` later decides to change which Service (or
Services) satisfy it, or to split one Service's responsibility into two: the requirement didn't move, only its
realization did.

Filed as `docs/analysis/use-cases/{use-case-slug}/USE-CASE.md` — the directory-per-entity pattern
(`documentation-standards.md` §2.1), since a use case grows its own `operations/` and `fixtures/`
subdirectories alongside it (§2.1). A use case is addressed by its own slug, never a numeric id — the same
convention already used for Feature and Service.

## 4 What A Use Case Feeds

A use case's operation documents and their fixtures (§2.1) are the requirement itself — what a later Chunk's
tests are ultimately built to satisfy. Nothing folds them into a second artifact: they stay where they were
written, and everything downstream references them.

What they feed first is [`Architect Solution`](architect-solution.md), which reads every use case's perceived
boundaries and operations together, Feature-wide, and decides the real Service topology and data flow that will
satisfy them. That decision — the functional boundary each operation is actually specified against — is what
joins Analysis to Design ([Weaver Engineering Workflows §1](../weaver-workflows.md)). Design then works against
a named boundary, sourcing what it must establish from these documents as external facts it references and
never owns ([Operation Fixtures §1](operation-fixtures.md)).

An earlier version of this process had a use case additionally derive **Required Product Behaviours** into its
own `behaviors/` subdirectory — a cumulative Given and Required Effect per operation, checksummed against the
use case. That was an earlier answer to how Analysis joins Design, written before an operation's own condition
space existed to state the same facts more completely and with fixtures attached. It is retired: a use case now
grows `operations/` and `fixtures/`, and no `behaviors/` of its own.

# Rationale

**Why a Step Contract's Boundary may guess an interface kind (CLI/API/UI) without contradicting §2's own use of
that vocabulary.** §2 states a real requirement: the actor-facing interface kind an operation needs, which is
given by what the actor actually is, not guessed. A Step Contract's Boundary can use the same three words for a
different, hypothetical purpose — a guess at what an *internal* boundary the actor never touches might turn out
to look like, offered with no more authority than the boundary name it sits beside. Reusing the vocabulary is
deliberate: both are describing the same kind of thing (an interface's shape), just at different confidence —
one stated, one guessed — so a reader isn't learning two unrelated meanings for CLI/API/UI.

**Why a perceived Boundary (§2.1) doesn't contradict §1's "a use case names no functional boundary."** The two
are different kinds of claim. Naming a *functional* boundary would be naming which Service actually realizes an
operation — a design decision Analysis has no authority to make, and the thing §1 rules out. Naming a *perceived*
boundary is Analysis stating its own read of where a crossing sits, offered as a hypothesis `Architect Solution`
is free to confirm, merge, or split. The distinction is exactly why Step Contracts don't reopen the reversal §1
guards against: a use case still commits to nothing about which Service exists, only to what it looks like one
does, from the actor's own vantage point.

**Why a use case names no functional boundary.** A use case that named its Service (or Library) directly would
stop being a statement of requirement and start being a statement of design — indistinguishable, later, from a
decision `Architect Solution` was supposed to make. Keeping a use case boundary-agnostic is what lets Architecture
actually choose, and re-choose, without ever having to edit the requirement itself to do it.

**Why §1.1 exists as its own section, not folded into §1's general statement.** §1 already said a use case is
written independent of any one Service, which implies operation count doesn't set use case boundaries. That
implication went unnoticed in practice: three real use cases were written one-operation-per-use-case anyway,
because nothing said outright that this was the specific failure mode to watch for. Naming the mistake with a
concrete example, rather than trusting the general principle to be applied correctly on its own, is what actually
prevents it recurring — the same reasoning `documentation-standards.md` already applies to itself (its own
Rationale: a standard that states an end state without saying how it's checked gets violated by the very next PR
that touches it).

**Why an assessed no-Step-Contract step is marked with a blockquote (§2.1), rather than left as plain prose or
given the contracted form's own title line.** §2.1 already establishes that a use case with no Step Contracts
yet is a legitimate, earlier document state, not a malformed one — but that same sentence makes a *partly*
contracted use case genuinely ambiguous: an unquoted step reads identically whether nobody has reached it yet or
it was deliberately assessed and found to cross no boundary. Found while dog-fooding the design-assistant
against real work ([WVR-206](https://linear.app/weaver-engineering/issue/WVR-206), fed in from WVR-203). A
blockquote resolves it more cheaply than either alternative it was weighed against: giving every step the
contracted form's title line makes every narrative step carry a heading it has nothing to put under, and saying
nothing (absence as the only marker) is exactly the ambiguity being fixed. A blockquote costs one character per
line, needs no new field, and is a marker a reader (or an agent) can check for mechanically without parsing
prose.
