# Agent Personas And Use-Case Actors

Working note, not a settled standard — a candidate for promotion into `documentation-standards.md` or the
`use-cases.md` concept doc once this pattern's been seen across more than one project.

## The insight

While writing AgentPlugins' UC-001 (Discuss A Project Concept And Document It) and UC-002 (Auto-Number Document
Sections), a pattern kept showing up: every use case's Main Success Scenario described steps performed by "a
sub-agent," but nothing ever stated what that meant as a set of real, citable facts — it has no persistent
memory between sessions, it needs machine-parseable output to chain into other tools, it's never authorized to
approve PRs. Those facts kept getting re-justified ad hoc, use case by use case, whenever they mattered (e.g.
justifying a `--json` flag requirement), inconsistently, since nothing forced the justification to be the same
each time.

The fix: model the agent doing delegated work as its own first-class persona, same shape as a human persona
(Role/Goals/Frustrations/Technical Proficiency) — see AgentPlugins'
`docs/analysis/user-personas/architects-assistant.md`, alongside the existing `architect.md`.

## The rule that follows

A use case's primary Actor should be whichever persona the use case's central activity actually belongs to, not
whoever nominally owns the outcome:
- Human judgment/dialogue activities (eliciting an undocumented concept, deciding a design tradeoff) keep the
  human persona as primary Actor.
- Purely mechanical delegated work (numbering, indexing, searching, extracting) uses the agent persona as
  primary Actor, with the human noted only as an alternate direct invoker where relevant.

This resolved a concrete ambiguity: UC-002 originally listed its Actor as "the Architect, or a sub-agent acting
on their behalf" — an unresolved dual-actor framing. Once the Assistant persona existed, the answer became
clear: the Assistant is primary Actor for UC-002 onward (mechanical delegation), while UC-001 keeps the
Architect (its defining activity is genuinely a human conversation).

## Why this probably generalizes

Every weaver-engineering project is doing agentic development in some form — human architects delegating work
to AI agents that themselves have real operational constraints. This isn't specific to AgentPlugins' own
documentation tooling; it's likely to recur anywhere a project writes use cases involving both a human and an
agent. Worth watching for repetition in MagpieWeaver, TheLoom, and WeaverProjects before promoting it out of
`notes/` into an actual standard.
