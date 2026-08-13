# The Retrospective Report

## Context
* [Feature Workflow](feature-workflow.md) - the workflow this step belongs to, and its entry requirement (a completed or abandoned Feature)
* [Retrospective Instructions](retrospective-instructions.md) - the step-by-step process an agent follows to run one of these and produce a report
* [Retrospective Template](../../templates/RETROSPECTIVE-TEMPLATE.md) - the fill-in-the-blank shape a report follows
* [Documentation Standards](../../standards/documentation-standards.md) - the document shape this report, and the reports it describes, must comply with

## Purpose
The retrospective is the last step of the [Feature Workflow](feature-workflow.md). Its entry requirement is a completed Feature — all its Chunks delivered and passed [Feature Testing](feature-testing.md) — or, if the Feature was abandoned or substantially reworked partway through, the point at which work on it stopped. Its purpose is specifically to capture actions to take to improve the workflow and the tooling supporting it — not to produce a status report on the Feature itself. The [use cases](use-cases.md) and [required behaviors](required-behaviors.md) already cover whether the Feature works; the retrospective is about how well the *workflow and tooling* served the people and agents delivering it, and what should change as a result.

## 1 Process
Running a retrospective is architect-led and AI-assisted: an agent gathers evidence of what actually happened, works through it with the architect to agree what went well and what didn't, proposes candidate actions, and takes each one to the architect for a decision. The full step-by-step process is defined in [Retrospective Instructions](retrospective-instructions.md); this document defines what the resulting report must contain and where it's filed.

## 2 Report Structure
The report is a single Markdown document, produced from the [retrospective template](../../templates/RETROSPECTIVE-TEMPLATE.md), with frontmatter identifying:
* the project the Feature belongs to
* the invocation's tracking identifier(s) — a Linear issue, a project-local task id (e.g. `MAG-46`), or both
* the date the retrospective was completed

The body has four required numbered sections:
1. **What Went Well** — naked bullets: what worked and should be kept or reused. The observation itself, not a discussion of it.
2. **What Didn't Go Well** — naked bullets: friction, spec misses, tooling gaps, or agent misbehaviour encountered. Same bar as §1.
3. **Future Actions** — only actions the architect agreed to, each with a link to the ticket raised for it. An action with no ticket yet doesn't belong here.
4. **Rejected Actions** — actions the agent proposed that the architect turned down, each recorded with the architect's rationale. A rejection is still a useful record of what was considered and why it wasn't worth doing.

## 3 Storage Location
The report is filed in this repo, collocated with the workflow it concerns, rather than in a project's own docs repo or a tooling repo — the feedback is about the workflow definition itself, not about whichever project happened to invoke it. It lives at `workflows/feedback/{workflow-slug}/task-{ref}.retro.md`, e.g. `workflows/feedback/feature-workflow/task-mag-46.retro.md`, using the invocation's primary tracking identifier (a Linear issue id where one exists, otherwise the project-local task id) as `{ref}`.

## 4 From Actions To Tickets
An agreed Future Action is only recorded in the report once its ticket exists — see [Retrospective Instructions §6](retrospective-instructions.md). The destination project for that ticket is proposed by the agent alongside the action itself, as part of the same "should we do this" decision the architect makes, rather than settled separately afterward. [The Loom](https://linear.app/weaver-engineering/project/the-loom-4c38cc05439d) and [AgentPlugins](https://linear.app/weaver-engineering/project/agentplugins-dda1b1e01d31) are the most likely destinations — the Loom for guardrail work that constrains autonomous coding agents, AgentPlugins for work that empowers sub-agents to better support the architect — but neither is a required destination; the agent proposes whichever project actually owns the fix, and the architect can accept or change it as part of agreeing to the action.
