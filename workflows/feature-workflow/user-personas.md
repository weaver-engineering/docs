# User Personas

## Context
* [Analysing A Feature](analysing-a-feature.md) - how a persona's use case turns a Feature's capabilities into
  actual benefit
* [Use Cases](use-cases.md) - the actor a persona formalizes, and the use case a persona's goal drives
* [Use Case Template](../../templates/USE-CASE-TEMPLATE.md) - the `Actor` field a persona doc is linked from
* [Documentation Standards §2](../../standards/documentation-standards.md) - `docs/analysis/user-personas/`, the
  directory a persona is filed under

## 1 What A User Persona Is

A User Persona is a named archetype of a use case's actor, stated as a **Goal** — the same real goal a use case is
scoped to (Use Cases §1) — and one or more **Frustrations**: what already goes wrong, or costs effort, on the way
to that goal today. A persona exists to answer "who actually wants this, and why" concretely enough that a
Feature's benefit (Analysing A Feature §2) can be checked against a real actor rather than an assumed one.

A persona is not every fact about a person — job title, demographics, tools they use elsewhere — only what's
load-bearing for *this* Product: the goal a use case pursues on their behalf, and the frustration that goal's
satisfaction is measured against. The toaster's `cook` (goal: cook food; frustrated by having to monitor bread
toasting) and `cleaner` (goal: clean the kitchen; frustrated by the toaster dropping crumbs when moved) are each
just that much, no more.

## 2 Not Every Actor Needs A Persona

A use case's actor (Use Cases §2) may be systematic — another system entirely, invoking a use case with no goal or
frustration to speak of, only a triggering condition. A persona formalizes an actor specifically when that actor is
a human (or a role a human occupies) whose goal and frustration are what makes the use case worth having at all.
Where an actor is systematic, the use case's `Actor` field names it directly; there is no persona doc to link.

## 3 Scope

Filed as `docs/analysis/user-personas/{persona-slug}.md` — a flat file, not the directory-per-entity pattern
(Documentation Standards §2.1): a persona is a single, stated fact (goal, frustrations) that doesn't grow satellite
artifacts the way a Feature or Use Case does. A persona is addressed by its own slug, never a numeric id, the same
convention already used for Feature and Use Case.

A persona is not owned by any one Feature or use case. The same persona (`cook`) may be the actor behind several
use cases, against several different Features' capabilities — and a use case is free to introduce a new persona
the first time it needs one, rather than requiring every persona to be pre-declared before analysis begins.

# Rationale

**Why goal and frustration, and nothing more.** A persona exists to make one check possible: is a Feature's
capability actually a benefit, for someone (Analysing A Feature §2)? Goal and frustration are exactly the two
facts that check needs — anything else about a persona (demographics, job title) has no bearing on whether a given
capability helps them, and including it invites treating this document as a marketing persona instead of an
analysis primitive.

**Why a persona isn't directory-per-entity.** Applying Documentation Standards §2.1's own test: a persona doesn't
accumulate a growing set of sub-documents the way a Feature (use cases, service flows, design tasks) or a Use Case
(one behaviour file per operation) does. It's a single fact worth stating once, so a flat file is the correct
shape, not an under-filled directory.
