# CI/CD Setup

## Context
* [About Weaver Engineering](../about-weaver-engineering.md) - workspace overview
* [Dev Environment Setup](../onboarding/dev-environment-setup.md) - the local machine auth a code repo's CI depends on
* [Documentation Standards](documentation-standards.md) - the document shape this standard follows

Every weaver-engineering project has a code repository and a separate docs repository (see [README](../README.md)). Both need the same rigour applied consistently: a gate-check workflow validating every PR, and branch protection enforcing that the gate actually ran and passed before a merge is possible — not left to reviewer discipline. This standard defines that shape once, so every project's repos are set up the same way rather than each reinventing it. It was reverse-engineered from the-loom/the-loom-docs and magpie-weaver/magpieweaver-docs — the first two projects to have it — rather than designed from scratch; §5's compliance table is what motivates extending it to the rest.

## 1 Code Repo

### 1.1 Workflows

Two GitHub Actions workflows, `.github/workflows/build-gate.yaml` and `.github/workflows/main-gate.yaml`. Both are thin wrappers around `pnpm gate-check <build-gate|main-gate>` — the validation logic itself lives in `@weaver-engineering/gate-checks`, not in the workflow file. There is no `test-gate.yaml`: the spec/{ref} → test/{ref} promotion isn't a PR merge (`task-phases`' own `promote` command forks the branch directly), so nothing GitHub-side needs to gate it.

* `build-gate.yaml` — triggers on `pull_request` into `build/**`, only when `github.head_ref` starts with `test/`. Runs `pnpm gate-check build-gate`.
* `main-gate.yaml` — triggers on `pull_request` into `main`, only when `github.head_ref` starts with `ready/` or `task/` (the full route and the quick route respectively). Runs `pnpm gate-check main-gate`.

Both need a `.github/scripts/json-to-yaml.mjs` helper (renders the gate's JSON result as readable YAML in the run log) and both report a commit status (`BuildGate`/`MainGate`) that the branch-protection rulesets in §1.3 require.

### 1.2 Consuming `gate-checks`/`task-phases`

Every code repo except the-loom itself consumes `@weaver-engineering/gate-checks` and `@weaver-engineering/task-phases` as real, versioned `devDependencies` published to GitHub Packages — not vendored source. The-loom is the one exception: it *is* the canonical source, so it uses its own local workspace packages directly rather than depending on its own published output.

* Root `package.json` — `devDependencies` include `"@weaver-engineering/gate-checks": "^<version>"` and `"@weaver-engineering/task-phases": "^<version>"`; `scripts` include `"gate-check": "pnpm exec gate-checks"` and `"task": "pnpm exec task"` (the `pnpm exec` prefix matters — it's what resolves the registry package's `bin` entry; a bare `"gate-checks"`/`"task"` script has nothing to resolve against and fails at run time).
* Workflow auth — both workflows' `Setup Node` step needs `registry-url: 'https://npm.pkg.github.com'`, the `Install dependencies` step needs `env: NODE_AUTH_TOKEN: ${{ secrets.GITHUB_TOKEN }}`, and the job-level `permissions:` block needs `packages: read`. No separate secret — this reuses the workflow's own built-in token, same pattern as the-loom's own `release.yaml` (which publishes these packages in the first place).
* No committed `.npmrc` — CI gets its registry config from the `actions/setup-node` step above; local/agent-machine resolution is a one-off machine setup step, not a repo file (see [Dev Environment Setup](../onboarding/dev-environment-setup.md)).
* No "reinstall to relink workspace bins" step needed — that pattern only existed while these packages were local `workspace:*` links; a registry-installed package's bin is already resolvable straight after `pnpm install`.

### 1.3 Branch Protection (Rulesets)

Three GitHub rulesets (Settings → Rules → Rulesets), all `target: branch`:

* **`branch-naming-policy`** — `include: ["~ALL"]`, `exclude` every known phase-branch pattern (`refs/heads/main`, `refs/heads/spec/**`, `refs/heads/test/**`, `refs/heads/task/**`, `refs/heads/build/**`, `refs/heads/ready/**`; the-loom's own copy additionally excludes `refs/heads/changeset-release/**` for its release-automation branches — add that exclusion only in a repo that also publishes via changesets). Single rule: `{"type": "creation"}` — blocks creating any branch that doesn't match one of the excluded (i.e. known-good) patterns. No bypass actors.
* **`build-protection`** — `include: ["refs/heads/build/**"]`. A `pull_request` rule (`required_approving_review_count: 0`, `allowed_merge_methods: ["rebase"]`) plus a `required_status_checks` rule requiring `BuildGate`. Bypass: `RepositoryRole` id `5` (Admin), mode `always`.
* **`main-protection`** — `include: ["refs/heads/main"]`. Same shape as `build-protection` but `allowed_merge_methods: ["squash"]` and requires `MainGate`. Same bypass actor.

### 1.4 Code Repo Merge Settings

`allow_merge_commit: false`, `allow_rebase_merge: true`, `allow_squash_merge: true`, `delete_branch_on_merge: false`. Merge commits are disabled outright — the rulesets already restrict each protected branch to one specific method (rebase for `build/**`, squash for `main`), so disabling merge-commit at the repo level closes off the one method neither ruleset ever allows, rather than leaving it as a route someone could still pick by hand.

## 2 Docs Repo

### 2.1 Workflow

A single `.github/workflows/main-gate.yml` (note: `.yml`, not `.yaml` — an existing inconsistency between the two docs repos that set the precedent, not worth correcting retroactively, but new docs repos should just pick one and move on rather than deliberate over it). It does **not** depend on `gate-checks` or `pnpm` at all — it's a self-contained `actions/github-script` step that checks out the PR head, reads the latest commit message, and validates two things: the title matches `^[A-Z]+-[0-9]+` (a task ref), and there's a non-empty body. Reports a `MainGate` commit status either way.

### 2.2 Branch Protection

One ruleset, "Validate main commits" — `include: ["refs/heads/main"]`, `allowed_merge_methods: ["squash"]`, requires the `MainGate` status check. Bypass actors: `OrganizationAdmin` and `RepositoryRole` id `5`, both mode `pull_request` (bypass still goes through a PR, doesn't allow a raw direct push — slightly tighter than the code repos' `always` bypass on `build-protection`/`main-protection`; not necessarily deliberate, just how the reference repos happened to be set up, but worth keeping since it's the safer of the two).

### 2.3 Docs Repo Merge Settings

Same as §1.4: `allow_merge_commit: false`, `allow_rebase_merge: true`, `allow_squash_merge: true`, `delete_branch_on_merge: false`.

## 3 A Docs Repo That Isn't A Project's Own

`weaver-engineering/docs` (this repo) doesn't belong to a single project, but the same reasoning applies — a PR merging here should still be gated on a real, task-ref'd commit message, not left to review discipline alone. It follows the plain §2 docs-repo pattern (no `gate-checks` dependency, since it has no code either), not some workspace-specific variant.

## 4 Sequencing

Turn on a branch's ruleset **after** confirming its required workflow actually reports the status check it requires, not before — a ruleset requiring a check that has never once posted leaves every future PR permanently blocked pending an admin bypass. In practice: land the workflow file via an ordinary (unprotected) PR first, confirm the check appears (GitHub runs a `pull_request`-triggered workflow using the version on the PR's own head branch, so this is visible before merge, not just after), then add the ruleset.

## 5 Compliance

| Project | Code repo | Docs repo |
|---|---|---|
| The Loom | ✅ | ✅ |
| Magpie Weaver | ✅ | ✅ |
| AgentPlugins | ⚠️ workflows exist but package.json doesn't provide `gate-checks`/`task`; no branch protection on either repo | ⚠️ workflow correct; no branch protection |
| Weaver Projects | ❌ no CI at all | ❌ no CI at all |
| Weaver Engineering (this repo) | n/a (docs only) | ✅ |

Update this table as each gap closes — it's the thing that should make "is project X set up right" a lookup, not a re-investigation.
