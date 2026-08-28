# Feature Workflow

## Context
* [About Weaver Projects](../../about-weaver-engineering.md)
* [Weaver Engineering Workflows](../weaver-workflows.md) - the full cross-SDLC model (three kinds of behaviour,
  two reconciliations, Architecture as a cross-cutting responsibility) this workflow is one slice of
## Workflow
```
(Start) ---> [Analyse Feature] ---> [Architect Feature] ---> [Design Service] <-+
                                                                     |          |
                                                                     V          |
                                                          [Chunk The Design]    |
                                                                     |          |
                                                                     V          |
                                                          [Sequence Chunks]     |
                                                                     |          |
                                                                     V          |
                                                          [Schedule Delivery]   |
                                                                     |          |
                                                                     V          |
                                                          [Implement/Test/      |
                                                           Deploy Service] -----+
                                                                     |
                                                                     V
                                                          [Test Feature]
                                                                     |
                                                                     V
(Done) <------------------------------------------------  [Deploy Offering]
                                                                     |
                                                                     V
                                                          [Retrospective]
```
`Design Service` through `Implement/Test/Deploy Service` loops once per Service `Architect Feature`'s own
Service Flows identify (Weaver Engineering Workflows §6) — a Feature commonly fans out into several
independently-designed-and-delivered Services before `Test Feature` exercises the assembled whole.

### Start
The entry requirements for the Feature workflow are that a 'Feature' has been suggested for delivery as part of a
project. The 'Feature' **must** have an [initial feature document](initial-feature-document.md) outlining the
Feature. The Feature workflow is independent of which project includes the Feature, so the initial feature
document **must** include such tracking frontmatter as is required by the project. The initial feature document
should provide sufficient details to understand the general idea of what is required and why.

### 1 Analyse Feature
The entry requirement of `Analyse Feature` is a suitable [initial feature document](initial-feature-document.md).

The process of analysing a Feature is to understand the 'wh...s' of the Feature — who it's for, what it does and
its benefit, where and when it happens, which resources it affects, why it happens — by writing its
[use cases](use-cases.md): an actor's real goal, achieved through one or more operations, never sliced down to
match one operation or one Service (Use Cases §1.1). For each use case, its Main Success Scenario and Extensions
are rewritten as Technical Interpretation (solution-independent pseudocode, Use Case Template), and each
operation's [Required Product Behaviours](required-behaviors.md) are mechanically/LLM-derived from it and
checksummed.

The whole Feature does not need to be analysed before design work can begin — this step is not formally required
at all (Weaver Engineering Workflows §2) — but a Feature analysed enough to avoid future rework of an already-
designed Service is worth the cost of doing it properly.

### 2 Architect Feature
The entry requirement is at least one use case with derived Required Product Behaviours. `Architect Feature`
decides the Service topology, archetypes, interfaces, supporting systems, and data flow that will satisfy them,
recorded as [Service Flows](architect-feature.md). Also not formally required — skipping it means a Service's own
design can proceed on an architect's bare assertion of its required behaviours, with nothing to reconcile that
assertion against (Weaver Engineering Workflows §2).

### 3 Design Service
The entry requirement is `service-flows.md` naming this Service, or an architect's own direct assertion of what
it's required to do if Architecture was skipped. For the Service (or Services) in scope, `Architect Services`
derives that Service's own Required Service Behaviours from its slice of the flow, then `Design Service`
crystallizes the Service's own interface and binds those behaviours to real components and functions, producing
Predicted Service Behaviours. The full process, including how to resume it partway through from a fresh session
with no memory of prior ones, is defined in [Design Feature Instructions](design-feature-instructions.md).

The output is a [design task directory](design-directory-and-hld.md) — the `hld.md` for this Service, its
`chunk-scope.yaml` (the bounded record of exactly which behaviours this task introduced, changed, or removed),
and its `reconciliation.yaml` (the mechanical record of every behaviour it predicted, bound, and reviewed).
Design documentation is read by agents, so it should be clear, to the point, and separated into appropriate sub
documents to avoid overloading agents with context that isn't relevant to their goal.

### 4 Chunk The Design
The entry requirements are the design task directory, its own Predicted Service Behaviours, and its
`chunk-scope.yaml` — the bounded list of behaviours, tagged new/mutated/deleted, this specific design task
actually produced. A design task directory may hold more design history than any one chunking pass needs; chunk
scope is what says exactly which part of it this pass covers.

Most Services require more than one specification to be successfully delivered by agentic software engineers
without an over-reliance on large contexts and frontier models, both of which have a significant negative impact
on AI costs.

The process of chunking the design breaks it down into incrementally deliverable Chunks. Each Chunk **must**
deliver at least one of the behaviours named in `chunk-scope.yaml` such that end-to-end (e2e) testing can verify
that delivering the Chunk adds (or, for a `mutated`/`deleted` behaviour, correctly changes) that specific
behaviour in the project. Each Chunk **must** have its external dependencies, and its dependencies on other
Chunks, clearly identified, in a [specification document](specification-document.md) that allows [the chunk
sequence](the-chunk-sequence.md) to be mechanically derived.

The outputs are the specification documents defining each chunk and the mechanically derived chunk sequence.

### 5 Schedule Delivery Of The Chunks
The entry requirements are the specification documents and the chunk sequence. The scheduled delivery of the
Chunks is an AI-assisted automation of the dispatch and monitoring of the AI agents delivering the code changes
to implement each Chunk — a mechanical process. Each Chunk has a defined place in [the chunk
sequence](the-chunk-sequence.md), and [the scheduler](the-scheduler.md) only allows a Chunk to start once all of
its dependencies are satisfied, mechanically driving it through the `spec/test/build` phases (see [Chunk Cycle
Workflow](../chunk-cycle/chunk-cycle-workflow.md) — `Implement Service`/`Test Service`). AI supervising agents
monitor progress, outputs, and permission requests, only surfacing issues to the architect that genuinely require
their input. **Note:** the architect is always required to approve pull requests (PRs).

The output is a working Service — `Deploy Service` (`../weaver-workflows.md`) makes it a `Functional Service`.

### 6 Test Feature
The entry requirement is every Service `service-flows.md` names in a `Functional Service` state. Before a Feature
can be considered done, its Required Product Behaviours **must** be tested end to end — the use cases exercised
and found to be delivering their expected benefits (see [Feature Testing](feature-testing.md)).

There is no requirement to wait for every Service to be delivered before testing what's already functional. If a
Service fails to deliver its expected benefits, the behaviours it was required to realize return to `Architect
Services`/`Design Service` to be reworked.

Only once **all** Services `service-flows.md` names have been delivered and **passed** Feature testing can the
Feature be said to be done, ready for `Deploy Offering`.

### 7 Retrospective
The entry requirement is the completed Feature. At the end of each completed Feature workflow it is advisable to
reflect on what went well, what didn't go well, and what actions will be taken to do better in the future.

The completion of a retrospective is very important while initially working through the Feature workflow and
ironing out its wrinkles and developing its tools.

The output is a [document](retrospective-report.md) recording the learning points and future actions, recorded
in `@the-loom-docs/feedback/feature-workflow` so they may be analysed to deliver improvements to the agents and
tooling supporting the Feature workflow or any of its sub-workflows (e.g. [Chunk Cycle
Workflow](../chunk-cycle/chunk-cycle-workflow.md)).

### Done
The Feature workflow is done when every Service `service-flows.md` names has been delivered, passed Feature
testing, and the Feature's Offering has been deployed.
