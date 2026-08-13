# Retrospective Template

## Context
* [Documentation Standards](../standards/documentation-standards.md) - the document shape (Context, numbered sections) this template follows
* [Retrospective Report](../workflows/feature-workflow/retrospective-report.md) - what a retrospective is, when it's required, and what the report it produces must contain
* [Retrospective Instructions](../workflows/feature-workflow/retrospective-instructions.md) - the process an agent follows to fill this template in

Template for a single retrospective report, one file per retrospected invocation of a workflow, filed under `workflows/feedback/{workflow-slug}/` in this repo as `task-{ref}.retro.md`. The template itself is in the Appendix below, since it's reference material to copy from, not indexed content in its own right.

# Appendix

```
---
project: {project-slug}
tracking_ids: [{tracking-id}]
completed: {YYYY-MM-DD}
---
# {Feature Or Task Title} Retrospective

## Context
* [{Workflow Name}]({relative link to the workflow document this retrospective concerns}) - the workflow being retrospected
* {link to the originating task/Feature - a Linear issue, a project-local task id such as `MAG-46`, or both - with a one-line summary}

## 1 What Went Well

- {naked bullet - the observation only, no elaboration}

## 2 What Didn't Go Well

- {naked bullet - the observation only, no elaboration}

## 3 Future Actions

- {action the architect agreed to} — agreed, tracked as {link to the ticket raised for it}

## 4 Rejected Actions

- {action the agent proposed} — rejected: {the architect's rationale}
```

# Rationale

**Naked bullets in §1/§2** — the capture step is evidence-gathering and elicitation, not analysis. Forcing structure or justification onto each bullet at this stage would slow down getting the raw observations down and risks the agent editorializing before the architect has even confirmed the bullet belongs.

**§3 only holds agreed, ticketed actions; §4 exists at all** — a proposed action that never gets ticketed isn't yet a commitment and doesn't belong in §3. But a rejected proposal is still a real record — what was considered, and why it wasn't worth doing — and dropping it silently would lose that signal for anyone reading the retrospective later. See [Retrospective Instructions §4-§6](../workflows/feature-workflow/retrospective-instructions.md) for how an action moves from proposal to one of these two outcomes.

**Filed under `workflows/feedback/{workflow-slug}/`, not a project's own docs or a tooling repo** — the retrospective is feedback on the *workflow*, not on whichever project happened to invoke it. Collocating it with the workflow's own definition means the feedback is discoverable exactly where the workflow is defined, regardless of which project produced it and without depending on cross-repo search tooling that doesn't exist yet. See [Retrospective Report §3](../workflows/feature-workflow/retrospective-report.md).
