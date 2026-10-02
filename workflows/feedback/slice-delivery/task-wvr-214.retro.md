---
project: document-evolution-engine
tracking_ids: [WVR-214, WVR-215, WVR-217, WVR-218, WVR-220, WVR-221, WVR-222, WVR-223]
completed: 2026-10-02
---
# DEM Hackathon Preparation Retrospective

## Context
* Slice delivery - the workflow being retrospected: an orchestrator session maintaining the design docs and writing
  slice tickets, a Dispatcher session starting worker sessions, and workers delivering each slice; not yet defined in
  its own document
* [WVR-214](https://linear.app/weaver-engineering/issue/WVR-214) - the orchestrator's running dem-docs ticket, which
  spanned the whole preparation for the DEM hackathon; the slices it prepared were delivered as WVR-215, WVR-217,
  WVR-218, WVR-220, WVR-221 and WVR-222, and the demo plugin as WVR-223

This retrospective covers the processes followed while preparing for the hackathon, not the demo at its end, which
failed because the last-minute releases could not be demoed with no agent runtime running on the demo machine.

## 1 What Went Well

- The orchestrator session oversaw the docs and prepared the inputs for each slice
- Dispatching worker contexts
- Concurrent agent runtimes — orchestrator, dispatcher and worker — worked as a team through tickets, with workers
  feeding doc updates back
- Vertical slices with end-to-end tests
- Checking each ticket before dispatch caught expensive gaps before a worker started
- The orchestrator's independent end-to-end verification on a disambiguated stack confirmed each slice before merge
- The UI lets the analyst visualise the work: excellent and needed, though it needs significant improvement
- The roadmap was excellent, though it could be improved
- Large slices, 2 hours of headless Opus 5.5, delivered what was needed because the documentation work supported them
- Design docs drafted one concept at a time and agreed before stitching in kept each review small
- Docs state what must be true, with removals recorded on the next code ticket, kept design and code changes apart
- Reviewing docs as git-diff rendered markdown shows exactly what the agent changed
- Reviewing agent output as multi-line prompts, which Remote Control supports but the native Claude Code runtime
  doesn't

## 2 What Didn't Go Well

- We broke our own processes, and it bit us at the end with no time to fix it
- Branching from a stale base and building outside the ticket workflow were among the processes broken
- Slice size grew from 10–15 minutes for the first slice to over 2 hours for the last, which used up one 5-hour token
  budget and about 97% of the next
- The last slice is the most a Claude Pro plan can sensibly hold; bigger slices would need a Max plan, which is
  beyond our capacity for now
- DEM and its UI weren't linked to an agent runtime
- Reviewing changes in the GitHub PR viewer worked but was mechanical and cumbersome for the architect; we want the
  review and the editing chat head in the same UI
- Dispatched sessions didn't save their session locally, so they couldn't be stopped and resumed
- Squash commits rewrite history, which makes clean-up and rebases temperamental
- Orchestrator drafts sometimes stated inferences or invented terms as design, which took correction rounds
- Expiring credentials (AWS, Linear) interrupted work, with no pre-flight check

## 3 Future Actions

- A slice-size budget in the roadmap standard: each slice fits one 5-hour Pro window with margin, and each slice
  ticket records its actual duration and token use — agreed, tracked as
  [WVR-224](https://linear.app/weaver-engineering/issue/WVR-224)
- A roadmapper agent, proposing the next slices from a roadmap sized to the slice budget — agreed, tracked as
  [WVR-225](https://linear.app/weaver-engineering/issue/WVR-225)
- dem-ambient: a local developer service with a review UI, linked to the whole agent team, using DEM for claims and
  the local filesystem for docs, with the review and the editing chat head in the same UI — agreed, tracked as
  [WVR-226](https://linear.app/weaver-engineering/issue/WVR-226)
- External plugins for DEM, loaded from outside the dem repository, so the hard-coded plugins and checks the
  hackathon added leave DEM — agreed, tracked as [WVR-227](https://linear.app/weaver-engineering/issue/WVR-227)
- Dispatched sessions save locally, with the session's id on the worker's ticket, so a worker can be stopped and
  resumed — agreed, tracked as [WVR-228](https://linear.app/weaver-engineering/issue/WVR-228)
- A pre-flight guardrail before code work: a ticket reference, a fresh base, and a branch the main gate accepts —
  agreed, tracked as [WVR-229](https://linear.app/weaver-engineering/issue/WVR-229)
- A credentials check at session start, for AWS, Linear and the MCP token — agreed, tracked as
  [WVR-230](https://linear.app/weaver-engineering/issue/WVR-230)
- Replace squash-and-reset with a merge model that doesn't rewrite history; the same ticket is the running ticket for
  the retro actions' changes to weaver-engineering/docs, held by an orchestrator session rooted there — agreed,
  tracked as [WVR-231](https://linear.app/weaver-engineering/issue/WVR-231)

## 4 Rejected Actions

- Link DEM's console to an agent runtime, so a chosen resolution is handed to a running DEM agent rather than pasted
  into one — rejected: solved by dem-ambient (WVR-226)
- Add a self-check to the orchestrator's standing instructions, so every statement in a design draft traces to an
  architect decision or an existing doc — rejected: design completeness belongs in a DEM plugin, the
  design-assistant's; what is really missing is external plugins (WVR-227)
