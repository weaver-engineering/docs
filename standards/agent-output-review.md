# Agent Output Review

## Context
* [Weaver Engineering Workflows](../workflows/weaver-workflows.md) - the Feature Workflow and Chunk Cycle this standard sits alongside
* [Chunk Cycle Workflow](../workflows/chunk-cycle/chunk-cycle-workflow.md) - the headless track whose own PR checkpoints this standard assigns review rigor to
* [CI/CD Setup](ci-cd-setup.md) - the branch-protection and gate-check shape this standard's enforcement builds on
* [Documentation Standards](documentation-standards.md) - the document shape this standard follows
* [Definition of Ready](definition-of-ready.md) / [WVR-160](https://linear.app/weaver-engineering/issue/WVR-160/enforce-a-11-ticketsession-model-architect-approval-for-newsub-work) - the sibling standards whose own Rationale sections both deferred headless-work review as "separate work, not an extension of this document" — this standard is that separate work, scoped to review
* [Definition of Done](definition-of-done.md) - the sibling standard governing when a ticket's own work is complete; this standard governs the PR mechanism that work is actually merged through
* [WVR-95 Design The Feature Retrospective](../workflows/feedback/feature-workflow/task-wvr-95.retro.md) - the retrospective whose Future Actions raised this standard ([WVR-158](https://linear.app/weaver-engineering/issue/WVR-158/fix-the-tracking-pr-squash-merge-divergence-properly-and-define-the))

An agent's output — docs, design, or code — is reviewed by a human before it reaches `main`. This standard defines one git mechanism that applies uniformly, and how the depth of review differs between the two shapes of work an agent does.

## 1 Scope
Applies to every PR into `main` in every weaver-engineering repo, raised by an agent session on the architect's behalf, whether that session is interactive (docs, design, planning) or headless (the-loom's Chunk Cycle).

## 2 The Rule
One PR lifecycle, regardless of which pattern (§3) applies:

1. Raise a PR against `main` as soon as the first commit exists — not once the work feels done. This is what lets the architect monitor a session's cumulative output without needing direct visibility into its worktree.
2. Push every subsequent commit to that same PR.
3. At a lock-in point, squash to exactly one commit and force-push, having first rebased onto the current `origin/main` so nothing upstream is silently clobbered by a stale base.
4. A human reviews and squash-merges. Agents never merge their own PRs.
5. Immediately after merge, before any new commit, the branch is reset onto `origin/main`'s new tip — the just-merged commit's content already matches it exactly, so nothing is lost, and the branch starts its next round of work from a genuinely non-diverged base.

See `weaver-engineering/TRACKING_PR.md` for the literal command sequence — this section states the rule, not the commands, so the two don't drift apart.

## 3 Two Patterns
The rule in §2 is constant; how much scrutiny a PR gets, and when, differs by pattern.

### 3.1 Interactive / Iterative
Docs, design, and planning work: the architect and the agent collaborate live, and the PR raised in §2 step 1 stays open for the work's whole duration. The continuously-growing PR diff *is* the review mechanism — the architect watches it change as commits land, which is how they confirm the output reflects their actual direction even when they can't see the worktree directly. There is no separate review moment to define; it's continuous by construction.

### 3.2 Headless Code
The Chunk Cycle (task→spec→test→build→ready→main): review concentrates at two existing checkpoints rather than continuously, because nobody is watching live.

* **The test→build PR** (`build-gate.yaml`, a PR into `build/**` from a `test/**` head) gets real rigor. The architect reviews the failing tests themselves against the design's expected behaviors — this is where behavioral correctness actually gets pinned down, because a test is the executable form of the design's intent.
* **The →main PR** (`main-gate.yaml`) relaxes deliberately. By this point correctness was already front-loaded into a watertight upfront design and a carefully reviewed failing-test PR, and `gate-checks` has already mechanically verified tests pass and coverage is met — re-litigating code quality line-by-line here would be redundant. What's left is a sanity check (tidy, structured as designed, tests passing, coverage achieved) plus one thing that can't be skipped: an end-to-end check of the added behaviors, run against the agent's actual worktree, confirming the one question code review alone can't answer — does it behave.

The architect is responsible for the correctness of an agent's output throughout either pattern; only *where* that responsibility is exercised shifts for headless work, front-loaded rather than back-loaded.

**Not yet resolved**: what "build" means for the e2e check once a real build step is required in the headless pipeline (today's worktree-direct e2e check doesn't need one) is flagged as needing further thought, not settled here.

## 4 Enforcement
Two mechanisms, at different points of completeness:

* `next-unit-of-work-detector`'s `checkTrackingPr` (canonical source in `agent-plugins`) verifies both that a design task's branch has an open tracking PR, and that the branch hasn't diverged from `origin/main` — catching a session that skipped the §2 step 5 reset, mechanically, rather than letting the original divergence bug recur silently.
* Docs repos are specified (not yet implemented — see the follow-on ticket from WVR-158) to consume `@weaver-engineering/gate-checks` themselves, the same package code repos already depend on, gaining the single-commit check §2 step 3 requires as a mechanical gate rather than relying on discipline alone. This needs a lighter docs-repo check profile in `gate-checks` (title/body plus single-commit, no coverage/build requirement) — see [CI/CD Setup](ci-cd-setup.md) §2.

# Rationale
This standard was raised as a Future Action from the WVR-95 retrospective (WVR-158), the same origin as [Definition of Ready](definition-of-ready.md) (WVR-161) and its Definition of Done sibling (WVR-160) — but where those two standards explicitly carve out headless implementation work as a different track with different mechanics, this one exists specifically to cover that different track's own review model, alongside closing a mechanical gap (tracking-PR divergence) in the interactive track's own tooling.

The two problems this standard resolves — recurring tracking-PR divergence, and no defined review process for agent output — turned out to share one root cause and one fix. A PR that's opened on the first commit and kept open throughout gives the architect continuous visibility without needing worktree access; squashing it to one commit before merge, and resetting the branch immediately after, is what keeps that same long-lived branch from diverging the next time the same thing happens. Headless work can't rely on continuous visibility (nobody is watching a build agent's worktree live), so its review model instead concentrates rigor at the one checkpoint where behavioral intent is actually legible — the failing tests — and treats the final merge as a lighter confirmation that the already-locked-in correctness held, plus a live check that the result actually behaves.
