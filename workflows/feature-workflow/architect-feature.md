# Architect Feature

## Context
* [Feature Workflow](feature-workflow.md) - the workflow step this document defines
* [Weaver Engineering Workflows §1](../weaver-workflows.md) - Architecture as a cross-cutting responsibility,
  paired here with Analysis
* [Architect Solution](architect-solution.md) - the sibling Architecture responsibility paired with Analysis,
  deciding the Service topology and functional boundary rather than the Product Offering
* [Product Offering](../../standards/concepts/product-offering.md) - the channel this step decides

`Architect Feature` decides how a Feature is actually offered for consumption — its Product Offering(s): which
channel (UI, CLI, API) a Feature's use cases are reached through, as distinct from `Architect Solution`'s own job
of deciding the Service topology those channels are built on.

//TODO - not yet discussed in enough depth to write up properly (Weaver Engineering Workflows §2 — not formally
required; skipping it leaves a Feature's Product Offering asserted rather than derived and reconciled against a
recorded justification). An earlier version of this process folded this decision into `Architect Solution` under
one shared name; splitting it out here is this ticket's own change (Weaver Engineering Workflows, intro) — the
process for actually making this decision still needs the same depth of treatment `Architect Solution` and
`Design Service` have already had.
