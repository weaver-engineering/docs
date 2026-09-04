# Initial Feature Document

## Context
* [Feature Workflow](feature-workflow.md) - the `Start` entry requirement this document satisfies, and the whole
  workflow a Feature moves through afterward
* [Analysing A Feature](analysing-a-feature.md) - what a capability is, and how a use case turns one into a
  demonstrated benefit
* [Use Cases](use-cases.md) - what a use case is, and how it relates to what a Feature groups
* [Documentation Standards §2.1](../../standards/documentation-standards.md) - the directory-per-entity pattern
  `docs/features/{slug}/FEATURE.md` follows

## 1 What A Feature Is

A Feature is a logical grouping of **capabilities** — things a customer can do through the product (see
[Analysing A Feature §1](analysing-a-feature.md)). It exists before design, before any decision about which
Service (or Services) will realize it, and before any route to market — a Feature can be named and scoped the
moment someone has an idea worth pursuing, long before anything about *how* it's delivered is known, and before
any use case has been written against it at all.

A capability is not the same thing as a use case: several use cases may invoke the same capability, a capability
may be invoked by no use case at all, and a use case may invoke capabilities drawn from more than one Feature. A
Feature groups capabilities, not use cases — see [Analysing A Feature](analysing-a-feature.md) for the full model,
including why a capability is still fully valid and designable with no use case behind it at all.

## 2 What The Initial Document Must Capture

The initial feature document is `docs/features/{feature-slug}/FEATURE.md` — the entry requirement for [Feature
Workflow](feature-workflow.md)'s `Start`, and the seed of the same document the rest of the workflow accumulates
state into (its Use Cases as they're identified, its Design Task scope once [Design The
Feature](design-directory-and-hld.md) begins). At minimum it needs:

* whatever tracking frontmatter the project requires (issue references, status) — the Feature Workflow is
  independent of which project a Feature belongs to, so this document doesn't fix a shape for it beyond requiring
  it be present;
* enough narrative to understand the general idea of what's required and why, sufficient for `Analyse The
  Feature` ([Feature Workflow](feature-workflow.md) §1) to begin — it does not need to already enumerate every use
  case, since analysis is exactly what discovers those.

# Rationale

**Why a Feature groups capabilities, not use cases.** An earlier version of this document defined a Feature as a
collection of use cases. That can't be right for a Feature that exists, nameable and scoped, before any use case
has been written against it (§1) — grouping use cases would leave such a Feature grouping nothing at all. A
capability is what's actually there from the moment a Feature is conceived: what the product lets a customer do,
regardless of who (if anyone yet) wants to do it and why. A use case remains what gives a capability a
demonstrated benefit — see [Analysing A Feature §2](analysing-a-feature.md) — it just isn't what a Feature itself
groups.

At minimum a Feature is still a collection of behaviors in the sense that matters: a capability with no use case
attached to it has unproven, not zero, value — which is exactly why a Feature is defined here as grouping
capabilities, checked against use cases separately, rather than behaviors named freehand with no way to say why
they should exist (see [Specific Behaviors §1](specific-behaviors.md)).
