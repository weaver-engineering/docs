# Dev Environment Setup

## Context
* [About Weaver Engineering](../about-weaver-engineering.md) - workspace overview

## 1 GitHub Packages Access

Some weaver-engineering projects publish their own tools as real, versioned npm packages to **GitHub Packages** (`npm.pkg.github.com`), scoped `@weaver-engineering`, rather than distributing them as source to check out and build — e.g. the-loom publishes `@weaver-engineering/gate-checks` and `@weaver-engineering/task-phases`, consumed by other repos (magpie-weaver, and any future repo) as ordinary `devDependencies`.

A machine that hasn't been set up for this will see `pnpm install` fail to resolve any `@weaver-engineering/*` package the moment a project's `package.json` lists one as a real dependency rather than a local `workspace:*` link. This is a one-off step for a new development environment, not something any single project's setup covers on its own.

## 2 Machine-Global `~/.npmrc`

Add these two lines to `~/.npmrc` in your home directory (create the file if it doesn't exist yet; if it already has other content — e.g. an unrelated `registry.npmjs.org` token for personal package publishing — append these lines rather than replacing it):

```
@weaver-engineering:registry=https://npm.pkg.github.com
//npm.pkg.github.com/:_authToken=<a token with read:packages scope>
```

The token is whatever `gh auth token` currently returns, as long as that login's scopes include `read:packages` (check with `gh auth status`; if missing, `gh auth refresh -s read:packages` adds it without a full re-login).

**Why machine-global rather than a `.npmrc` committed into each consuming repo:** a committed `.npmrc` using `${GITHUB_TOKEN}`-style environment-variable interpolation would need every shell that runs `pnpm install` to have that variable exported first — and Claude Code's own Bash-tool shells are non-interactive and non-login, so they don't reliably inherit variables exported from `.zshrc`/`.bash_profile` the way a human's terminal does (the same class of gap that once broke `nvm`'s `PATH` setup for those same shells). A `~/.npmrc` addition has no such dependency: every worktree and every agent session on the machine shares the same home directory, so it's set up once and just works everywhere, at the cost of not being tracked by any repo — each new machine needs to repeat this step once.

## 3 Verification

```
npm view @weaver-engineering/gate-checks versions
npm view @weaver-engineering/task-phases versions
```

Both should return a list of published version numbers, not a `401 Unauthorized` or `404 Not Found`. A `401` means the `_authToken` line is missing or the token lacks `read:packages`; a `404` on a package you know exists usually means the `@weaver-engineering:registry` scope mapping line is missing, so npm/pnpm went looking on the public registry instead.

## 4 Local Machine Setup

The whole workspace lives under a single `weaver-engineering/` directory on the dev machine — a plain directory, not itself a git repository. It holds a workspace-wide `CLAUDE.MD` (instructions that apply across every project, e.g. "never approve a PR to `weaver-projects`/`weaver-projects-docs`"), one directory per project (`MagpieWeaver/`, `WeaverProjects/`, `TheLoom/`, `AgentPlugins/`, …), and a single shared `agentWorkTrees/` directory.

### 4.1 Code And Docs As Siblings

Each project directory holds its two repos side by side, checked out directly from GitHub:

```
MagpieWeaver/
  magpie-weaver/         # code repo
  magpieweaver-docs/     # docs repo
```

Same pattern for `WeaverProjects/` (`weaver-projects` + `weaver-projects-docs`), `TheLoom/` (`the-loom` + `the-loom-docs`), and `AgentPlugins/` (`agent-plugins` + `agent-plugins-docs`). These sibling checkouts are each project's **live main worktree** — see §5.

### 4.2 Agent Work Trees

`agentWorkTrees/<project>/` holds a separate `git worktree add` checkout of the code repo for every agent session working on that project in parallel — e.g. `agentWorkTrees/magpie-weaver/architect`, `/agent_1`, `/architect_1` — each normally sitting on its own task branch (`task/WVR-54`, …) so parallel agents never collide on the same branch or the same working directory.

### 4.3 Docs Symlink For Agents

An agent working inside a code worktree still needs to reach that project's docs. Two patterns exist across the projects, oldest to newest:

* **Shared symlink** (`magpie-weaver`, `weaver-projects`): `agentWorkTrees/magpie-weaver/magpieweaver-docs` is a plain filesystem symlink to the single canonical checkout at `MagpieWeaver/magpieweaver-docs`. Every agent worktree under that project reads the same shared docs checkout through the symlink.
* **Per-worktree docs worktree** (`AgentPlugins`, the current convention): each agent code worktree gets its own dedicated `git worktree add` of the docs repo too — e.g. `agent-plugins-wvr95` pairs with its own `agent-plugins-docs-wvr95` — rather than a symlink into a shared checkout.

The shared-symlink pattern has a known failure mode: the symlinked checkout can be behind `origin/main` at the moment an agent reads through it, with nothing to make that visible. Prefer the per-worktree-docs pattern for new projects; see §5 for the related incident that motivates this.

## 5 Live Main Vs Agent Worktrees

Every project has exactly one **live main/dev worktree** — the plain checkout directly under the project directory (`MagpieWeaver/magpie-weaver`, `AgentPlugins/agent-plugins-docs`, …). It tracks `origin/main` live and exists for two things only: giving a human visibility into the actual mainline codebase, and running system-level checks against it. It is never a place for agent work — reading source to plan or scaffold a task, editing, committing, or branching all belong in an agent worktree instead.

All development work happens in an **agent worktree** under `agentWorkTrees/<project>/`, each on its own task branch. If none is free for the work at hand, create a new one with `git worktree add` rather than falling back to the main/dev worktree.

This is enforced as a standing global instruction, not just a convention: Claude Code refuses to read-for-development, edit, commit, or branch from a project's main/dev worktree. A project that hasn't yet split into a separate main/dev vs. agent-worktree layout (`the-loom` at time of writing) has nothing to violate yet; the rule bites once a project gets its first dedicated agent worktree.

**Why this matters, concretely:** a stale or in-progress main worktree looks exactly like a normal checkout, which makes reading from it an easy, invisible mistake. WVR-52 scaffolded packages by reading from a dev worktree that happened to be stale — silently copying a week-old, partially-implemented snapshot instead of the finished version already on `origin/main` — which is exactly the risk §4.3 flags for the docs symlink pattern too.

## 6 Design Lounge Vs Office Workshop

These are two working modes, not two literal rooms — the separation is psychological as much as physical, though it usually does map onto where you actually are:

* **Office workshop** — at the dev machine itself, where multiple agents are running hard in parallel across `agentWorkTrees/`, and the work is orchestration and documents: reviewing diffs, driving gate checks and task phases, steering several agent sessions at once, writing and editing specs.
* **Design lounge** — away from the desk, on a mobile device, talking to one agent at a time through a remote-control session that is still actually running on the dev machine. The pace and mode of thought are different: slower, single-threaded, deep-thought conversation — designing, reviewing, deciding — rather than orchestrating several running processes at once.

Both modes drive the same agent processes on the same dev host; only the interface and the pace of interaction change. See §7 for how the mobile side connects.

## 7 Mobile Setup

The mobile device (tablet/phone) is normally a thin client onto the dev host, used for design-lounge-mode work (§6), reading docs, and reviewing or approving PRs and issues on the move — not a second place project work actually lives.

### 7.1 Obsidian

Obsidian, pointed at a copy of the docs repos, gives offline-friendly reading of markdown on the tablet — Context sections, use cases, standards, etc. — without needing a live connection back to the dev machine.

### 7.2 Termux

Termux on Android provides a real Linux shell on the tablet, used for:

* **`git`** — cloning project repos directly onto the tablet, for a task that needs local files rather than just remote-controlling an agent on the dev machine.
* **Ubuntu** (a full userland inside Termux) — used to run **OpenCode** or **Claude Code** directly on the tablet, if a task needs a locally-running agent rather than remote-controlling the one on the dev machine.

Both of the Ubuntu-hosted agents are "if needed": the default mobile workflow is remote-controlling the dev machine's already-running agents (§7.4, §7.5), not running a second set of agents on the tablet. Local tablet agents are the fallback for when the dev machine is unreachable or the task is small and self-contained.

### 7.3 GitHub

The GitHub mobile app (or mobile web) is used to review and approve PRs from the design lounge — the same review responsibility as at the desk, just on the move.

### 7.4 Claude

Claude Code's Remote Control feature connects a mobile session to a Claude Code session already running on the dev machine, so a design-lounge conversation talks to the same agent and the same working tree as it would from the desk.

### 7.5 OpenCode

OpenCode's own client/server split is what makes this work: the TUI is just one client of a headless HTTP API server, and any other client can talk to that same server instead. On the dev machine, `opencode serve` starts that headless server (default `127.0.0.1:4096`, override with `--port`/`--hostname`); `opencode web` starts the same server plus a bundled browser UI, so a phone's browser can drive it with no app install at all. From the tablet's Termux shell (§7.2), `opencode attach <url>` instead connects a local TUI to that remote server, rather than running a local OpenCode instance against it. There's also a dedicated **OpenCode Mobile** Android app as a third option, for a native client rather than the browser or `attach`.

All three connect over the same mechanism, so the same access controls apply: `OPENCODE_SERVER_PASSWORD` (with an optional `OPENCODE_SERVER_USERNAME`, defaulting to `opencode`) turns on HTTP basic auth on the server, and reaching it from outside the home network is via a VPN like Tailscale rather than exposing the port directly to the internet.

### 7.6 Linear

The Linear mobile app covers issue triage, comments, and status updates from the design lounge — the same workspace as the desk.
