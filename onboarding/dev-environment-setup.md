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
