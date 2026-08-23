---
project: agent-plugins
tracking_ids: [WVR-95, WVR-155]
completed: 2026-08-23
---
# Design The Doc Search & Retrieval MCP Server Retrospective

## Context
* [Feature Workflow](../../feature-workflow/feature-workflow.md) - the workflow being retrospected, specifically its `Design The Feature` step
* [WVR-95](https://linear.app/weaver-engineering/issue/WVR-95/design-the-doc-search-and-retrieval-mcp-server) - "Design the Doc Search & Retrieval MCP server," the first Feature run end to end through Design The Feature and its `design-assistant` sub-agent, dogfooding both

## 1 What Went Well

- The hard-stop-on-unexpected-skill-behavior protocol worked exactly as designed — caught five real bugs (WVR-142, 149, 150, 153, 154) no test suite had caught, before any damage stuck
- design-assistant's own resumability held up in every case actually tested — cold start, runtime restart, session hand-off, all with zero shared memory
- Real dogfooding surfaced two process-documentation bugs (WVR-151, WVR-152) that pure review would never have found — both needed an actual green-field project to expose an implicit assumption
- The standing-instructions simplification (moving per-phase content out of design-assistant.md into next-unit-of-work-detector's own live output) shrank it from 137 to under 100 lines, and follow-on fixes never touched the sub-agent's own instructions again
- Independent verification discipline — never trusting a peer's "done" report without checking the actual diff or CI directly — caught real problems, including a mistake made by the supervising session itself
- Batch-vs-individual review judgement was escalated properly rather than assumed — design-assistant asked before batching, and only for verified-identical repeats

## 2 What Didn't Go Well

- No tracking PR existed for 90+ commits after the original PR was squash-merged early, only noticed by accident — closed properly by WVR-143, not just detected: the tool now errors without one, and opening it is the agent's own standing responsibility, confirmed when the same situation recurred later in this same session
- The underlying squash-merge divergence still recurs by construction every time progress is locked in — closing "no PR ever existed" doesn't stop the next tracking PR from immediately diverging from `main` the same way, needing the same manual `git merge -X ours` resolution again
- `agent-plugins-docs` PR #15 sat open and stale for a long time before anyone happened to investigate it — nothing in the process prompted a review
- extractSection's fence-blindness took two separate tickets (WVR-149, then WVR-153) to actually fully fix — the first fix covered only part of the real bug surface
- The completed design isn't yet ready for `Chunk The Design` to start, despite that being the process's whole intention — it never required architecting the service's own boundaries: what the documentation-tools capability actually is as a product/service, how consuming agents/tools interact with it, what its own external interface is
- The supervising session redeployed a skill from a stale worktree checkout and verified it against a stale copy of itself — caught mid-way, wasted a cycle
- No automated install/build mechanism exists yet (WVR-94) — deployment was manual `cp -r` syncing throughout, which is what caused the mistake above
- Sub-work tickets were opened ad hoc throughout the session without the architect's prior approval, and confirming they'd all actually been driven to completion required a manual, one-off audit at the end
- Linear ticket status updates silently failed to stick more than once this session, unnoticed until that final audit

## 3 Future Actions

- Rethink design-assistant's own scope — decompose the single fixed Design workflow into smaller, composable workflows adaptable to different environments and ways of working — agreed, tracked as [WVR-157](https://linear.app/weaver-engineering/issue/WVR-157/rethink-design-assistants-scope-decompose-the-single-fixed-design)
- Fix the tracking-PR squash-merge divergence properly, and define the review/monitor process for agent-produced outputs (docs and code) — agreed, tracked as [WVR-158](https://linear.app/weaver-engineering/issue/WVR-158/fix-the-tracking-pr-squash-merge-divergence-properly-and-define-the)
- Note in the Architect's Guide that sessions should be cleared/restarted more often now the skill family is proven, to reduce token usage — agreed, tracked as [WVR-159](https://linear.app/weaver-engineering/issue/WVR-159/architects-guide-note-that-sessions-should-be-clearedrestarted-more)
- Enforce a 1:1 ticket:session model — architect approval for any new/sub work and whether it's a sub-issue or standalone, Linear metadata linking a ticket to its own session for resumption, session completeness driven by the ticket's own sub-issue list, and an enforced Definition of Done per ticket — agreed, tracked as [WVR-160](https://linear.app/weaver-engineering/issue/WVR-160/enforce-a-11-ticketsession-model-architect-approval-for-newsub-work)
- Raise [WVR-94](https://linear.app/weaver-engineering/issue/WVR-94/document-cross-platform-capability-parity-mechanics)'s (cross-platform capability parity / the real plugin delivery mechanism) priority now that this Feature's own dogfooding gave concrete evidence of the cost of not having it — agreed, ticket already existed; raised to High priority as part of this retrospective

## 4 Rejected Actions

- Update "skill-builder"'s own standing practice to audit every known call-site variant before considering a shared-helper bugfix complete — rejected: "skill-builder" was never a persistent role, only a session name used for context management during this dogfooding; the underlying insight (short, role-scoped sessions are a validated pattern, but only with strong standing instructions and mechanical tools to bridge the memory gap) was folded into WVR-159 instead
- Document a standing rule to always verify against `origin/main` before any skill/sub-agent redeploy — rejected: that's a transient workaround; the real fix is developing the actual plugin delivery mechanism, already tracked as WVR-94
- A standalone periodic PR-staleness checker — rejected: treats a symptom, not the actual gap; folded into WVR-158's broader review/monitor-process scope instead
