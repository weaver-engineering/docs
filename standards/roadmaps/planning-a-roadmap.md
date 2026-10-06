# Planning a Roadmap

## Context
* [Roadmaps](../roadmaps.md) - the definition this guide applies: slices, the slice budget, aspects, complexity and the recorded actuals
* [Glossary](../../glossary.md) - one-line definitions of the terms used here

This is guidance for the architect planning a roadmap with an agent's help. It is not the roadmapper agent's standing
instructions: those say how the agent works, this says what the architect does with it and what to look at. The agent
writes the estimates and does the sums; the architect decides every score, every split and every change to the budget.

## 1 Scoring Aspects

* Score **leaf aspects only**, one at a time, on the scale 1, 2, 3, 5, 8, 13, as a gut-instinct of how much work it is
  to build, verify and document. A whole takes its parts' scores and has none of its own.
* Have the agent propose the score and say what it compared the aspect to. Ask for a comparison with an aspect already
  delivered: "a bit more than the tool gateway" is a better anchor than an abstract number.
* A 13 is a prompt to decompose, not a score to defend. Split it, and score the parts.
* Do not argue between neighbours, 5 or 8. If the choice matters, the aspect is probably two aspects.
* An aspect left unscored counts as 1, which understates it. Score every aspect a slice will deliver before the slice
  is planned.
* Score the work as it is now. An aspect that will arrive largely complete — code generated elsewhere, say — scores
  for the work that remains, not the size of what arrives.

## 2 Summing a Slice's Spend

1. List the aspects the slice will deliver, with each one's score.
2. Add them. That is the slice's **spend**.
3. Compare it with the slice budget (Roadmaps §2). It must not exceed it.
4. Write the slice into the roadmap naming its aspects and their scores, and record the spend as the slice's
   `complexity` when its actuals are recorded.

The budget is a cap, not a target: a slice that spends well under it is fine, and nothing is padded to reach it. Keep
slices thin and end to end.

A behaviour may be added to an aspect so that a behaviour belonging to a requirement can be tested end to end, even if
the added one is in no use case — a list of the configured checks, so the UI aspect has something to test against. Score
it and include it in the spend.

## 3 Splitting an Aspect That Does Not Fit

When an aspect would push a slice over the budget, or is too large to leave room for anything else:

* **Split it into parts**, each an aspect in its own right with its own score, and deliver some of the parts in this
  slice and the rest in a later one.
* Split along what can be verified separately: a part that can be shown working end to end, not a layer.
* Record what the remaining parts need. A need on one part need not wait for the rest of the whole (Roadmaps §3).
* Do not shave scores to make a slice fit. If a score is wrong, correct it and say why; the actuals will show it.

## 4 Reading the Actuals

After a slice completes, its actuals are in its entry in the phase's `roadmap.json` (Roadmaps §2). Before planning the next slice:

* **Compare spend with cost.** For each slice, set `complexity` beside the elapsed time between `start-time` and
  `end-time`, and beside `tokens-in`, `tokens-out` and `thinking-time`. Slices of similar spend should cost similar
  amounts; one that did not points at a score that was wrong.
* **Treat the cost as non-linear.** Twice the spend is more than twice the work. Compare slices of similar spend with
  each other rather than extrapolating from a small one to a large one.
* **Read lines and files as context.** `lines-added` and `files-changed` explain an outlier — a slice that arrived mostly
  written, or one that was mostly deletion — and are not a measure of complexity.
* **Look across several slices before acting.** One slice is an anecdote. After a few, ask which aspects were
  consistently under- or over-scored, and correct those scores and the comparisons the agent uses.

## 5 Recalibrating the Budget

* The test is the **token budget of a 5-hour window**: a slice at the cap should fit well inside it. Raise the budget
  only when slices spending close to it consistently do, with room to spare, and lower it when slices near it get close
  to using the window up.
* Change the value in Roadmaps §2 and record why in its Rationale §6, with the actuals it rests on.
* A new budget applies to slices planned from then on. Delivered slices are history and are not rescored (Roadmaps §2);
  planned slices are re-summed against the new value.
