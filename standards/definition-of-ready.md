# Definition Of Ready

## Context
* [Weaver Engineering Workflows](../workflows/weaver-workflows.md) - the Feature Workflow and Chunk Cycle this standard sits alongside
* [Chunk Cycle Workflow](../workflows/chunk-cycle/chunk-cycle-workflow.md) - the headless, the-loom-orchestrated implementation track this standard does not cover
* [Documentation Standards](documentation-standards.md) - the document shape this standard follows
* [WVR-95 Design The Feature Retrospective](../workflows/feedback/feature-workflow/task-wvr-95.retro.md) - the retrospective whose Future Actions raised this standard ([WVR-161](https://linear.app/weaver-engineering/issue/WVR-161/definition-of-ready)) alongside its Definition of Done sibling ([WVR-160](https://linear.app/weaver-engineering/issue/WVR-160/enforce-a-11-ticketsession-model-architect-approval-for-newsub-work))

An architect works most Linear issues together with an assisting agent session, not alone and not headlessly. This standard defines when such an issue is ready to be handed to a session — in effect, what "helping the architect work effectively and efficiently with an assisting agent" requires an issue to already contain. It is a definition, not a gate: checking an issue against it today is a manual, interactive judgement the architect makes before opening or resuming a session against it (§4).

## 1 Scope
Applies to a Linear issue the architect is about to resolve together with an assisting agent session — design, docs, planning, retrospectives, and issues like this standard's own originating ticket. Also applies to a sub-issue split out to bound an agent session's context (§3), on the same terms as any other issue.

Does not apply to headless implementation work (an issue like "implement feature AAA-000"), which the-loom orchestrates under its own task/spec/build/ready mechanics ([Chunk Cycle Workflow](../workflows/chunk-cycle/chunk-cycle-workflow.md)) — a different track with different mechanics, not covered here.

## 2 Ready Criteria
An issue meets the Definition of Ready when all of the following hold.

### 2.1 Self-Contained Objective
The issue states the outcome wanted in language a new session can act on directly. Referencing a parent issue for shared context is expected and fine — issues are documents, not sessions, and a fresh session can read a linked parent issue same as any other document. What the issue must not require is access to the parent's own *session* — its live conversation history isn't available to a new session, and needing it would defeat the point of splitting the work out in the first place (§3).

### 2.2 Actionable Acceptance Criteria
The issue has explicit, checkable acceptance criteria, not a restated title and not an aspiration. These are what the session's own output is checked against on completion (see [WVR-160](https://linear.app/weaver-engineering/issue/WVR-160/enforce-a-11-ticketsession-model-architect-approval-for-newsub-work)'s Definition of Done).

### 2.3 Located
The issue identifies which project and which repos the work happens in.

### 2.4 Unblocked
The issue's dependencies on other issues are recorded (e.g. Linear's blocking/blocked-by relations), not just known informally, so whether it's actually unblocked can be checked mechanically rather than judged from memory.

### 2.5 Single Skill/Sub-Agent
The issue is resolvable within one skill, sub-agent, or plain interactive chat — one identifiable shape of session. A given skill or sub-agent may itself serve more than one workflow, but a single issue's resolution should only ever need one. Needing to switch shape partway through a session is a signal the issue was too broad and should have been split (§3), not something to push through in place.

## 3 Sub-Issues Bound Context, Not Information
When a session working its own issue hits side work that doesn't belong in its own context, the agent proposes a sub-issue — it doesn't raise one or start a session against it unilaterally. Raising the ticket and starting a session against it is the architect's decision. What the agent is responsible for is populating that ticket with enough of its own current context that the new sub-session won't need to read back through the parent session's history to get going — that's what makes the sub-issue satisfy §2.1.

Once the sub-issue exists, it's the architect who is now working two sessions instead of one, splitting focus and switching between them — the sub-session is still an interactive architect-and-agent session needing the architect's own input, not a handoff the architect can walk away from. The parent session continues its own work, or the architect has it wait on the sub-issue's output, at the architect's discretion.

The sub-issue itself must independently satisfy §2 in full, including §2.1: it can and should reference the parent issue for context, but must carry what the architect and a fresh agent session need in order to act, without either of them reading the parent session's own history.

## 4 Enforcement
Not yet automated. Today, the architect confirms an issue meets §2 before opening or resuming a session against it — a manual, interactive check, consistent with this governing interactive architect-agent work rather than headless implementation (§1). Tooling to check and groom issues against this standard is anticipated future work, not delivered by this document.

# Rationale
This standard was raised as a Future Action from the WVR-95 Design The Feature retrospective, alongside its output-side counterpart [WVR-160](https://linear.app/weaver-engineering/issue/WVR-160/enforce-a-11-ticketsession-model-architect-approval-for-newsub-work) (enforcing a 1:1 ticket:session model with its own Definition of Done). The two are deliberately symmetric: WVR-160 governs what "done" means for a session's own output, checked at session end; this standard governs what "ready" means for what a session is given to start from, checked at session start — both over the same 1:1 ticket:session unit of work.

An early draft of §2 included "no outstanding open questions" and "sized to fit one session" as hard criteria. Both were dropped after discussion: an open question can legitimately get resolved *through* the work of an issue rather than needing to be closed beforehand, and an oversized issue can still be started and split further as its true scope is discovered mid-flight (§3) rather than being blocked pending an upfront size estimate that's hard to make accurately anyway. Gating on either would have meant judging something vague and hard to check in advance. §2.4 and §2.5 replace them with the concrete, checkable signals that actually matter: dependencies recorded rather than remembered, and a session needing to switch skill/sub-agent shape mid-issue treated as the trigger for a split, rather than trying to predict that need upfront.

§1 excludes headless implementation work deliberately, not as an oversight: that track already has its own mechanics (task/spec/build/ready branches, gate-checks) governing what it means for work to be ready to promote through them. This standard is about the different, less mechanical problem of an issue being ready for an *architect and an assisting agent to work together* — a headless-implementation Definition of Ready, if one turns out to be useful, is separate work, not an extension of this document.
