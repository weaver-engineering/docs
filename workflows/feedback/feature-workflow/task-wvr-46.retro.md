---
project: magpie-weaver
tracking_ids: [WVR-46, MAG-46]
completed: 2026-08-13
---
# Tool Up Task Phasing Retrospective

## Context
* [Feature Workflow](../../feature-workflow/feature-workflow.md) - the workflow being retrospected
* [WVR-46](https://linear.app/weaver-engineering/issue/WVR-46/tool-up-task-phasing) - "Tool Up Task Phasing," the Magpie Weaver Feature this retrospective concerns; delivered under the project-local id `MAG-46` across 18 spec chunks (00-18) through the Feature Workflow and its Chunk Cycle sub-workflow

## 1 What Went Well

- Real e2e testing against actual git/GitHub after every build-phase merge, not just trusting a green mocked CI run
- Two-gate review structure (test-phase review, build-phase review) with different review criteria for each
- Pre-handoff spec review (shim-dependency, existing-test-contradiction, test-file-layout checks) caught real gaps before they reached test-writer
- Batched pre-sequencing review across multiple upcoming specs (12-15, 16-18) caught cross-chunk issues before they surfaced individually
- Agents reliably recognized work outside their own authority and reported `needs-architect-intervention` cleanly instead of guessing or working around
- Additive-only interface extension pattern avoided compile breaks when an agent needed a new primitive
- Clear-down of stale phase branches after each Main Gate merge became a consistent, reliable habit
- Three-tier thin-shim framework (dedicated dev-testing chunk / quick-route+manual verification / ad-hoc override) gave a clear, reusable rule for sequencing stub resolution
- Disposable e2e fixtures were always fully cleaned up, no permanent pollution of shared repos
- The full 18-chunk backlog delivered end-to-end with no abandoned or reworked scope
- Deliberately dogfooding the workflow to surface permission gaps as agents hit them live, rather than trying to pre-provision every permission upfront

## 2 What Didn't Go Well

- Mocked tests and green CI repeatedly failed to catch real git-behavior bugs - at least 8 separate real bugs found only via e2e testing across the backlog
- A branch-naming rename (`build/{ref}` -> `main/{ref}` -> `ready/{ref}`) wasn't fully propagated, silently breaking `merged-pending-cleanup` detection in the regular route for a significant stretch
- The "existing-test contradiction" pre-handoff check wasn't a standing checklist item from the start, so the same class of miss recurred across several chunks before being named explicitly
- MAG-47's missing standard review-comment-resolution template forced ad-hoc prompting for every review-comment fix relayed to a live agent session, repeatedly, and was deliberately deferred rather than fixed
- Recurring OpenCode platform reliability issues (503s, a ~76 minute stuck turn, a silently undispatched prompt, stalls after context compaction) caused repeated process friction
- Mixing agent-permission-update work with actively monitoring an agent session delivering changes caused process friction
- The architect's assistant worked in the coding agent's own worktree instead of the dedicated architect worktree, causing a live agent session's test commit to land on the wrong branch
- No standing rule against agents running `gh pr merge` existed until it was attempted twice in the same build-implementer session
- `lib/` shared-logic extraction (LLD-designated) sat unused through two full spec/test/build cycles, each reimplementing the same logic privately, before being noticed
- A gate-checks coverage-computation bug silently reported 100% coverage on every commit outside gate-checks itself, masking dead code for part of the backlog
- The architect's assistant left the architect's primary/dev worktree checked out on a task branch instead of rebased to `origin/main`; only caused confusion during this Feature's own workflow, but later fed a stale, partially-implemented mid-workflow snapshot into the-loom's initial package scaffold ([WVR-52](https://linear.app/weaver-engineering/issue/WVR-52/setup-the-looms-repo)) when the architect assumed that worktree was tracking `main`

## 3 Future Actions

- Write up the Chunk Cycle workflow document, capturing the operational discipline proven over this backlog (two-gate review, e2e-verification-over-green-mocked-CI, the pre-handoff checklist, the three-tier thin-shim framework) — agreed, already tracked as [WVR-80](https://linear.app/weaver-engineering/issue/WVR-80/define-the-chunk-cycle-workflow) (raised before this retrospective; reconfirmed here as the home for these findings)
- Instruct sub-agents to stay confined to their own worktree, mirroring the architect-side rule already in place — agreed, tracked as [WVR-98](https://linear.app/weaver-engineering/issue/WVR-98/instruct-sub-agents-to-stay-confined-to-their-own-worktree)
- Add a mechanical guardrail verifying a scaffold-source worktree is on and current with `origin/main` before anything reads from it — agreed, tracked as [WVR-100](https://linear.app/weaver-engineering/issue/WVR-100/guardrail-verify-a-scaffold-source-worktree-is-current-with-originmain)
- Raise [WVR-47](https://linear.app/weaver-engineering/issue/WVR-47/add-standard-review-comment-resolution-agent-flow)'s (the review-comment-resolution template) priority now that this Feature's full backlog is complete, the condition the architect set for revisiting it — agreed, ticket already existed; moved to Ready and raised to High priority as part of this retrospective
- Investigate a stall/watchdog mechanism for headless OpenCode agent sessions — agreed, tracked as [WVR-99](https://linear.app/weaver-engineering/issue/WVR-99/investigate-a-stallwatchdog-mechanism-for-headless-opencode-agent)

## 4 Rejected Actions

No actions were rejected during this retrospective.
