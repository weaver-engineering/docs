# Retrospective Instructions

## Context
* [Feature Workflow](feature-workflow.md) - the workflow this step belongs to
* [Retrospective Report](retrospective-report.md) - what a retrospective report is, when it's required, and what it must contain
* [Retrospective Template](../../templates/RETROSPECTIVE-TEMPLATE.md) - the fill-in-the-blank shape to produce
* [Documentation Standards](../../standards/documentation-standards.md) - the document shape and indexing the produced report must comply with

These are the steps an agent follows, architect-led, to run a retrospective and produce a compliant [retrospective report](retrospective-report.md). The point of the process is specifically to capture actions to take to improve — evidence-gathering and elicitation exist to surface good candidate actions, not to produce a polished narrative for its own sake.

This process is manual and interim. [WVR-97](https://linear.app/weaver-engineering/issue/WVR-97/define-the-retrospective-use-cases-for-agentplugins) tracks defining the AgentPlugins use case(s) for proper retrospective tooling; once that tooling exists, this document should be replaced with a short pointer to it rather than continuing to carry the full manual process.

## 1 Identify The Workflow And The Invocation Being Retrospected

Confirm which workflow (or sub workflow, e.g. the [Chunk Cycle](../chunk-cycle/chunk-cycle-workflow.md)) is being retrospected, and the task or Feature whose completion (or abandonment) triggered it. Note every tracking identifier for that invocation — a Linear issue, a project-local task id (e.g. `MAG-46`), or both — they go in the report's frontmatter and are the source of `{ref}` in its filename.

## 2 Gather The Evidence

Read the output notes of the task itself and of any associated tasks that drove the invocation of the workflow: [specification documents](specification-document.md), PRs and their review comments, gate-check/task-phase history, and any session notes or transcripts left by the coding and supervising agents involved. This evidence is what §1 and §2 of the report are drawn from — it is not optional background; the elicitation conversation in the next step depends on the agent already having a concrete read of what happened, not the architect having to reconstruct it from memory.

## 3 Draft What Went Well And What Didn't Go Well

From the evidence, draft the report's §1 What Went Well and §2 What Didn't Go Well as naked bullets — no elaboration, no sub-structure, just the observation. Then work through both lists with the architect: confirm each bullet, correct it, drop it, or add ones the evidence missed. This is an elicitation conversation, not a presentation — the architect's own recollection is a legitimate source alongside the evidence gathered in step 2.

## 4 Propose Future Actions

Review the confirmed §1 and §2 bullets and propose candidate actions — concrete enough to become a ticket on their own, not vague sentiment ("be more careful"). For each candidate, also propose which project's tracker it belongs in, as part of the same proposal rather than a separate question. [The Loom](https://linear.app/weaver-engineering/project/the-loom-4c38cc05439d) (guardrail work that constrains autonomous coding agents) and [AgentPlugins](https://linear.app/weaver-engineering/project/agentplugins-dda1b1e01d31) (work that empowers sub-agents to better support the architect) are the most likely candidates, but neither is a required destination — propose whichever project actually owns the fix.

## 5 Elicit A Decision On Each Action

Walk each proposed action past the architect individually, action and destination project together as a single "should we do this" decision. The architect may agree, agree but change the destination project, or reject the action outright. If rejected, capture the architect's rationale in the same exchange — don't move on without it.

## 6 Raise Tickets And Record The Outcome

For each agreed action, raise a Linear issue in the agreed project (team `Weavers`) and record it in the report's §3 Future Actions with a link to the ticket. For each rejected action, record it in §4 Rejected Actions with the rationale captured in step 5. Nothing appears in §3 without a ticket already raised — an agreed-but-not-yet-ticketed action isn't done with this step yet.

## 7 File The Report

Fill in the [retrospective template](../../templates/RETROSPECTIVE-TEMPLATE.md) with the confirmed content and file it at `workflows/feedback/{workflow-slug}/task-{ref}.retro.md` in this repo (e.g. `workflows/feedback/feature-workflow/task-mag-46.retro.md`), using the primary tracking identifier from step 1 — a Linear issue id where one exists, otherwise the project-local task id — as `{ref}`. Bring the new document into compliance with the [Documentation Standards](../../standards/documentation-standards.md) — Context section, numbered body, index entries — the same as any other document added to this repo.
