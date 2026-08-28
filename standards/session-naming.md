# Session Naming

## Context
* [WVR-160](https://linear.app/weaver-engineering/issue/WVR-160/enforce-a-11-ticketsession-model-architect-approval-for-newsub-work) - the 1:1 ticket:session enforcement this standard's naming rule is the visible marker for
* [WVR-177](https://linear.app/weaver-engineering/issue/WVR-177/package-the-peer-session-startstop-mechanism-as-a-dispatcher) - the dispatcher tooling that applies this rule mechanically when it starts and stops a session; this standard defines the rule, not the mechanism that applies it
* [WVR-95 Design The Feature Retrospective](../workflows/feedback/feature-workflow/task-wvr-95.retro.md) - the retrospective whose Future Actions raised the whole 1:1 ticket:session line of work, including this standard's own originating ticket ([WVR-174](https://linear.app/weaver-engineering/issue/WVR-174/define-a-session-naming-standard-so-a-session-can-be-renamed-to-match))
* [Definition of Ready](definition-of-ready.md) - the sibling standard governing what a session is given to start from; this one governs what the session is called once it starts
* [Definition of Done](definition-of-done.md) - the sibling standard governing what "done" means for the ticket this session's worker resolves

A Weaver Engineering workspace has exactly two kinds of Claude Code session: one long-lived **dispatcher**, rooted at `weaver-engineering` itself, which starts and stops every other session; and any number of **workers**, each spun up by the dispatcher with its root, git branch, Linear task, and agent persona all fixed at startup and never changed thereafter. This standard defines what a worker is named, and why that name never needs to change.

## 1 Scope
Applies to every session in the workspace. The dispatcher is named `Dispatcher` — see §2. Every other session is a worker and is named per §2 for its entire life.

## 2 Naming Rule
A worker's name is built from up to three parts, in this fixed order, each omitted entirely when absent rather than replaced with a placeholder:

```
<Product>[ - <IssueRef>][ : <Agent>]
```

| Product | Issue ref | Agent | Name |
|---|---|---|---|
| yes | yes | yes | `AgentPlugins - WVR-177 : design-assistant` |
| yes | no | yes | `AgentPlugins : design-assistant` |
| yes | yes | no | `AgentPlugins - WVR-177` |
| yes | no | no | `AgentPlugins` |

- **`<Product>`** is the repo/directory the worker is rooted in: `AgentPlugins`, `MagpieWeaver`, `WeaverProjects`, or `Docs`. `Docs` is a deliberate exception to "matches the directory name" - the docs repo lives at `weaver-engineering/docs`, not inside a product-named subdirectory the way the others do, so its token is spelled out explicitly rather than derived.
- **`<IssueRef>`** is the Linear issue key exactly as Linear displays it (`WVR-177`), with nothing appended. Absent for a worker with no ticket - an ad hoc discussion against a product's docs and code, with nothing to implement.
- **`<Agent>`** is the custom agent persona the worker was started with (`design-assistant`, etc.), absent for a plain worker with no `--agent` flag.
- The dispatcher itself is always named exactly `Dispatcher` - it isn't rooted in a single product and isn't 1:1 with any ticket, so it doesn't fit this grammar at all.

## 3 Branch Naming
A worker tied to a Linear issue is always on branch `<type>/<issue-ref>` (e.g. `task/WVR-174`). What `<type>` is - and how it gets chosen - is decided at worker startup and is [WVR-177](https://linear.app/weaver-engineering/issue/WVR-177/package-the-peer-session-startstop-mechanism-as-a-dispatcher)'s concern, not this standard's; this rule only fixes the format so a branch and its worker are always mutually derivable.

## 4 Renaming, Sub-Issues, and Multi-Ticket Sessions Don't Apply
Because a worker's root, branch, task, and agent are all fixed at startup, none of the following situations a naming standard would normally have to define can actually occur:

- **Renaming mid-session** - never happens. A worker's name is set once, at launch, from what it's launched with.
- **A sub-issue split out mid-session** - is never a rename of the current worker. Raising a sub-issue is an architect decision (Definition of Ready §3); if the architect starts a session against it, that's the dispatcher launching a *new* worker with its own name from §2, never the existing worker taking on a second identity.
- **A session spanning more than one ticket over its lifetime** - can't happen. When a worker's ticket is done, the worker is stopped; further work is a new worker.

## 5 Cross-Session Addressing
Because a worker's name never changes after launch, it's a stable address for its entire life - `SendMessage`/`ListAgents` never need to account for a peer's name going stale mid-conversation, which a rename-based scheme would have to.

## 6 Enforcement
Not yet automated. Applying this rule - choosing the right name and passing it when a worker is started - is exactly what [WVR-177](https://linear.app/weaver-engineering/issue/WVR-177/package-the-peer-session-startstop-mechanism-as-a-dispatcher)'s dispatcher skill does once built. Until then, the architect names a worker by hand per §2 when starting one directly.

# Rationale
This standard exists to give [WVR-160](https://linear.app/weaver-engineering/issue/WVR-160/enforce-a-11-ticketsession-model-architect-approval-for-newsub-work)'s 1:1 ticket:session model a concrete, visible marker - a name in `ListAgents`/FleetView that says at a glance which product, which ticket, and which agent persona a session is. An earlier draft treated sessions as long-lived per-product "lounges" that would need renaming as they moved between tickets, which raised real open questions about when to rename and how cross-session addressing would survive it. Splitting sessions into a single dispatcher plus short-lived, single-purpose workers (root/branch/task/agent fixed at startup) removed the need for renaming entirely rather than answering those questions - a worker's name is simply correct from the moment it's launched, for as long as it exists.
