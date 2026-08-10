# Documentation Standards

## Context
* [About Weaver Engineering](../about-weaver-engineering.md)
* [Weaver Engineering Workflows](../workflows/weaver-workflows.md)

## Why

Every project's documentation exists to be read by AI agents as much as by
architects — agents doing analysis, design, spec-writing, and
implementation all draw their context from these docs. An agent has a
limited context budget and no memory between sessions: documentation that
is large, unfocused, or poorly linked wastes tokens establishing context an
agent should have been able to find directly, and risks confusing the agent
with irrelevant material. These are the minimum requirements every
project's docs repo (`<project>-docs`) must meet.

## 1. One concept per document

A document should cover a single, well-bounded concept, component, or
decision — not a sprawling topic. If a document is accumulating sections
that could stand alone and be linked to instead, split it. Prefer several
short, focused documents over one long one; an agent only needs to load the
documents relevant to its current task, not an entire topic area.

## 2. Every document is reachable from an index

Each docs repo maintains a project index/overview document (see
`magpieweaver-docs`' `docs/magpie-weaver.md` for the reference shape) that
links out to the repo's documents by area. A document with no inbound link
from the index — or from another already-linked document — is effectively
lost; agents and architects alike find context by following links outward
from the index, not by searching the filesystem.

## 3. Cross-references are explicit, bidirectional markdown links

Related concepts link to each other directly (`[Term](path/to/term.md)`),
not by implication or shared vocabulary alone. Where document A depends on
or elaborates document B, link both directions where practical — B should
be discoverable from A and A discoverable from B (`magpieweaver-docs`'
`docs/glossary.md` does this within a document using `§`-numbered section
references).

Cross-*project* links carry an added, currently unresolved problem: each
project's docs exist both locally in the weaver-engineering workspace and
canonically on GitHub, and a plain relative markdown link only resolves in
one of those two contexts. Until that's resolved (see AgentPlugins'
"Discuss A Project Concept And Document It" use case, in
`agent-plugins-docs`, §7), prefer same-project links and refer to other
projects' concepts by name in prose rather than a broken or
context-dependent link.

## 4. Undocumented or stale concepts are marked, not silently missing

Where a concept is known to need documenting or updating but hasn't been
addressed yet, mark the spot with a `//TODO` tag naming the gap, at the
location in the docs where the content belongs — see
`workflows/feature-workflow/use-cases.md` in this repo for a live example
(`//TODO - Define what a use case is`). This gives both the location and
the identity of the gap without requiring the full detail up front, and is
the anchor a Linear issue references when raising the work — branch
protection requires every change to reference a Linear issue.

## 5. Tooling exists to make these standards easy to keep, not just easy to state

Because agents are the ones maintaining this documentation day to day, the
standards above should be enforced and assisted by tooling rather than
relied on as manual discipline — e.g. a sub-agent/tool that finds and
resolves `//TODO` tags (AgentPlugins' "Discuss A Project Concept And
Document It" use case), checks that new or changed documents are
reachable from the relevant index, and
flags one-way links. Where a standard is hard for an agent to satisfy
consistently by hand, that's a signal to build a tool for it, not to
relax the standard.
