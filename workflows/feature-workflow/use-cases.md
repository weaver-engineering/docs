# Use Cases

## Context
* [Feature Workflow](feature-workflow.md) - the workflow step (`Analyse Feature`) that produces use cases and
  their Required Product Behaviours
* [Initial Feature Document](initial-feature-document.md) - what a Feature is, and how it relates to a use case
* [Analysing A Feature](analysing-a-feature.md) - how a use case's operations relate to a Feature's own
  capabilities, and what turns one into a benefit
* [User Personas](user-personas.md) - the goal/frustration pair a use case's human actor is formalized as
* [The Product/Service Model](../../standards/product-service-model.md) - where Use Case sits in the wider
  Platform/Product/Service continuum
* [Required Behaviors](required-behaviors.md) - what a use case's operations turn into once analysed
* [Weaver Engineering Workflows §5](../weaver-workflows.md) - the four-layer interface decision (use case,
  Architecture, Design, Product Offering) a use case's own interface statement is only the first layer of
* [Use Case Template](../../templates/USE-CASE-TEMPLATE.md) - the fill-in-the-blank shape this document describes

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

A use case is written independent of any one Service. Nothing about a use case's own definition — its actors, its
goal, its steps — commits to which Service (or Services) actually realize it; that binding is Design's job, made
while working out *how* to satisfy the use case, never Analysis's. A use case that ends up split across more than
one Service is still one use case: what varies is how many Services its operations resolve to, not how many use
cases were written.

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
§5](../weaver-workflows.md) for the other three (Architecture decides the technology, Design crystallizes the
concrete specification, Product Offering decides how it's actually delivered for consumption).

An operation either invokes a Feature's own capability directly, or is specified inline when no capability yet
covers what it needs — see [Analysing A Feature §4](analysing-a-feature.md) for the distinction and how each is
derived into a Required Behavior.

## 3 Scope

A use case stays at the Product level: it is never filed under, or owned by, any one Service's own
`docs/services/{slug}/`, and it is not owned by any one Feature either — it may invoke capabilities drawn from more
than one Feature on the way to its actor's goal (see [Analysing A Feature](analysing-a-feature.md)). This is what
keeps a use case's requirement stable even if Design later decides to change which Service (or Services) satisfy
it, or to split one Service's responsibility into two: the requirement didn't move, only its realization did.

Filed as `docs/analysis/use-cases/{use-case-slug}/USE-CASE.md` — the directory-per-entity pattern
(`documentation-standards.md` §2.1), since a use case now always grows its own `behaviors/` subdirectory
alongside it (§4). A use case is addressed by its own slug, never a numeric id — the same convention already used
for Feature and Service.

## 4 Required Behaviors

A use case's Required Product Behaviours are what a later Chunk's tests are ultimately built to satisfy. They are
not independently authored: they're mechanically/LLM-derived from this use case's own Goal, Preconditions, Main
Success Scenario, and Extensions, one behaviour document per operation, filed under
`docs/analysis/use-cases/{use-case-slug}/behaviors/{operation-slug}.md`. See [Required
Behaviors](required-behaviors.md) for the derivation itself, the cumulative Given it produces, and why attempting
it is the actual test of whether this use case was written with enough detail.

# Rationale

**Why a use case names no Service.** A use case that named its Service directly would stop being a statement of
requirement and start being a statement of design — indistinguishable, later, from a decision Design was supposed
to make. Keeping a use case Service-agnostic is what lets Design actually choose, and re-choose, without ever
having to edit the requirement itself to do it.

**Why §1.1 exists as its own section, not folded into §1's general statement.** §1 already said a use case is
written independent of any one Service, which implies operation count doesn't set use case boundaries. That
implication went unnoticed in practice: three real use cases were written one-operation-per-use-case anyway,
because nothing said outright that this was the specific failure mode to watch for. Naming the mistake with a
concrete example, rather than trusting the general principle to be applied correctly on its own, is what actually
prevents it recurring — the same reasoning `documentation-standards.md` already applies to itself (its own
Rationale: a standard that states an end state without saying how it's checked gets violated by the very next PR
that touches it).
