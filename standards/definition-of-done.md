# Definition Of Done

## Context
* [Weaver Engineering Workflows](../workflows/weaver-workflows.md) - the Feature Workflow and Chunk Cycle this standard sits alongside
* [Chunk Cycle Workflow](../workflows/chunk-cycle/chunk-cycle-workflow.md) - the headless, the-loom-orchestrated implementation track this standard does not cover
* [Documentation Standards](documentation-standards.md) - the document shape this standard follows
* [Definition of Ready](definition-of-ready.md) - the sibling standard governing what a session is given to start from; this one governs what "done" means for the ticket it started from
* [Agent Output Review](agent-output-review.md) - the sibling standard governing the PR mechanism a ticket's own work is actually merged through
* [Session Naming](session-naming.md) - the sibling standard governing the worker session's own identity; together with the two standards above, this forms the third of the concrete mechanisms enforcing the 1:1 ticket:session model
* [WVR-95 Design The Feature Retrospective](../workflows/feedback/feature-workflow/task-wvr-95.retro.md) - the retrospective whose Future Actions raised this standard ([WVR-160](https://linear.app/weaver-engineering/issue/WVR-160/enforce-a-11-ticketsession-model-architect-approval-for-newsub-work)) alongside its Definition of Ready sibling ([WVR-161](https://linear.app/weaver-engineering/issue/WVR-161/definition-of-ready))

An architect works most Linear issues together with an assisting agent session, ended when that session reports the ticket complete. This standard defines what "done" actually means for such a ticket — the criteria that claim is checked against, not the session's own say-so.

## 1 Scope
Applies to a Linear issue resolved together with an assisting agent session — design, docs, planning, retrospectives, and any sub-issue split out under Definition of Ready §3.

Does not apply to headless implementation work, which the-loom orchestrates under its own task/spec/build/ready mechanics ([Chunk Cycle Workflow](../workflows/chunk-cycle/chunk-cycle-workflow.md)) — that track's own build-then-ready gates already define what "done" means for it, not covered here.

## 2 Done Criteria
A ticket meets the Definition of Done when all of the following hold — ticket-level facts only; nothing here depends on how many sessions the work took.

### 2.1 Acceptance Criteria Verified
Each of the issue's own acceptance criteria ([Definition of Ready](definition-of-ready.md) §2.2) is checked individually against what was actually produced at completion, not restated from memory or assumed met because related work happened.

### 2.2 Sub-Issues Resolved
Every sub-issue raised from this ticket ([Definition of Ready](definition-of-ready.md) §3) is itself resolved, via Linear's own parent/sub-issue relation — not tracked separately by hand.

### 2.3 Closure Comment Recorded
A comment posted at closing states how each acceptance criterion was actually met. Where a plan drove the work, recording it as a linked Document alongside the comment is sensible practice (see `weaver-engineering/LINEAR.md`) — not every ticket has a plan behind it, so this is not itself a required criterion.

### 2.4 Status Reflects Reality
The Linear status is updated to match actual completion at the moment it's actually complete, not left stale until someone happens to notice.

## 3 Enforcement
Not yet automated. A standing instruction in `weaver-engineering/LINEAR.md` (loaded via a catch-all pointer in `weaver-engineering/CLAUDE.MD`) governs the full ticket lifecycle a session follows, including checking this Definition of Done before closing — a behavioral rule a session follows, not mechanically gated tooling. Tooling to check a ticket against this standard automatically is anticipated future work, not delivered by this document — the same honest framing as [Definition of Ready](definition-of-ready.md) §4.

# Rationale
This standard was raised as a Future Action from the WVR-95 Design The Feature retrospective, alongside its input-side counterpart [Definition of Ready](definition-of-ready.md) (WVR-161). The two are deliberately symmetric: Definition of Ready governs what a session is given to start from, checked at session start; this standard governs what "done" means for a session's own output, checked at session end — both over the same 1:1 ticket:session unit of work.

No session-count criterion, and no session-resumable metadata, are included in §2 deliberately: both describe a mechanism for finding and resuming the right session for a ticket, not a fact about whether the ticket's own work is actually complete. That mechanism, if it turns out to be needed, is separate work from what this document defines.
