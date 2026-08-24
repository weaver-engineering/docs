# Initial Feature Document

## Context
* [Feature Workflow](feature-workflow.md) - the `Start` entry requirement this document satisfies, and the whole
  workflow a Feature moves through afterward
* [Use Cases](use-cases.md) - what a Feature groups
* [Documentation Standards §2.1](../../standards/documentation-standards.md) - the directory-per-entity pattern
  `docs/features/{slug}/FEATURE.md` follows

## 1 What A Feature Is

A Feature is a collection of use cases to be implemented or supported. It exists before design, before any
decision about which Service (or Services) will realize it, and before any route to market — a Feature can be
named and scoped the moment someone has an idea worth pursuing, long before anything about *how* it's delivered
is known. A use case grouped under a Feature always belongs to exactly one Feature.

At minimum a Feature is a collection of behaviors — but behaviors without a use case attached to them have
questionable value on their own, which is why a Feature is defined here as grouping use cases specifically, not
behaviors directly (see [Use Cases §4](use-cases.md)).

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

**Why a Feature groups use cases rather than behaviors directly.** Behaviors are read off a call tree traced
against something concrete (see [Specific Behaviors §1](specific-behaviors.md)) — without a use case's actor and
goal behind it, a behavior has no way to say *why* it should exist, only that it technically could. Grouping by
use case keeps a Feature's own scope answerable to "what is this actually for," not just "what does this do."
