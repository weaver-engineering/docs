# Roadmaps

## Context
* [Documentation Standards](documentation-standards.md) - the document shape a roadmap follows, and the directory structure it sits beside
* [Product/Service Model](product-service-model.md) - the Product a roadmap is the build plan of
* [dem-docs ROADMAP/](https://github.com/weaver-engineering/dem-docs/blob/main/ROADMAP/ROADMAP.md) - the first roadmap, which this standard is drawn from
* [Planning a Roadmap](roadmaps/planning-a-roadmap.md) - the architect's guide to scoring, budgeting and recalibrating with an agent's help
* [The roadmap skill](https://github.com/weaver-engineering/agent-plugins/tree/main/claude-skills/roadmap) - changes a roadmap's data and generates its documents and charts; its own `SKILL.md` is the command reference

A project's **roadmap** says how its product is being built: the phases the work falls into, in each phase the slices
delivered so far and the slices planned next, and the aspects of the product that are known and can wait. It is the
one place the whole of that can be read without filling Linear with work that is not yet ready to be done.

## 1 Where It Lives

A roadmap is a `ROADMAP/` directory at the root of the project's docs repo, beside `PRODUCT.md`:

```
ROADMAP/roadmap.json                    the phases, and the context links every roadmap document carries.
ROADMAP/ROADMAP.md                      the entry document: the phases, and which is current. It never moves.
ROADMAP/where-we-are.svg
ROADMAP/{phase-slug}/roadmap.json       the current phase's slices, aspects and actuals (§7).
ROADMAP/{phase-slug}/ROADMAP.md         the phase's roadmap, and the charts it embeds, beside it.
ROADMAP/{phase-slug}/where-we-are.svg
ROADMAP/{phase-slug}/aspects-gantt.svg
```

**A roadmap is data.** Every `ROADMAP.md` and chart is generated, in full, from the `roadmap.json` files, so there is
no hand-written prose in a roadmap document: why roadmaps work as they do is here, in this standard, and every
generated document links to it. The data is changed only through the roadmap skill's validated commands and then
generated (§7); a document, a chart or a `roadmap.json` is never edited by hand.

`PRODUCT.md` links to the entry document from its Context, and the two stay separate: the product record says what the
product is, the roadmap says how it is getting there. Nothing in a roadmap ever moves or is renamed, so no link into it
ever breaks (Rationale §5).

**Titles.** The entry document is titled "Roadmap". A phase's roadmap is titled with the phase's name and "Roadmap":
"Hackathon Roadmap" for the phase slug `hackathon`. A phase that has not yet been named has no roadmap of its own. A
phase must be named before its first slice is delivered, and a roadmap may name future phases tentatively.

A roadmap is optional. A project with nothing yet to plan has no roadmap, not an empty one. The first thing a roadmap
does is name its first phase.

## 2 Slices

A product is built in **slices**: thin, end-to-end pieces of it, each delivered by a ticket or a few, and verified
end to end. Each slice records two things:

* **Requires** — what had to be in place before it could be built: the concepts and decisions its tickets point their
  workers at;
* **Delivers** — what it adds to the running product.

A slice also names its tickets, linked to Linear. **A slice's number is its section and its place in it**: 1.n for a
delivered slice, 2.1 for the current slice and 3.n for a planned one; it is addressed by its slug.

**Delivered slices are history, and do not change.** They record what was built and what it rested on at the time;
where the design has moved on since, the design documents say so, not the roadmap.

**Planned slices are aspirations, and do.** They are the current best guess at what comes next, and in what order,
and are rewritten as that changes. What is uncertain about a planned slice is the slice itself — what goes into it,
and when — not the things it names, which are known to be needed. **A planned slice becomes a ticket when it is next,
and not before.**

**A slice is the set of aspects it delivers**, and Requires and Delivers are generated from them: a slice **delivers**
its aspects, and **requires** the needs of its aspects that are outside it, each with where it is. A slice may start only when every aspect in it is mature and is blocked
by nothing outside the slice (what it needs is done, or is in the slice), and it is delivered when every aspect in it is
done (§3). Naming a whole delivers its parts, and an aspect is in at most one slice. A planned slice may hold aspects in
any status.

**A slice has a budget.** The **slice budget** is a ceiling on complexity, not itself a complexity and not a number on
the Fibonacci scale (§3). It is a cap and not a target: it exists to keep a slice from overloading an agent, and a
slice that spends less is not a worse slice. A planned slice names the aspects it will deliver, each with its complexity; the sum of
those complexities is the slice's **spend**, an unscored aspect counting as 1, and **a slice's spend must not exceed
the budget.** An aspect whose slice would not fit is split into parts until the slice that delivers it does. A behaviour may be
added to an aspect for the sake of end-to-end testing, even if it is in no use case itself, provided it lets a behaviour
that is part of a requirement be tested end to end — a list of the configured checks, say, so that the UI aspect has
something to test against. It is work like any other and counts in the spend. The
budget is **45**. That is its starting value, derived from the complexity of the aspects the hackathon delivered
(the hackathon roadmap's Appendix, Rationale §6), and it is recalibrated from the recorded actuals (below) as slices run against it.

**Whenever a slice is completed, the scope of the next slice is reassessed**, so that slices do not quietly grow: its
spend is summed again against the budget, with the completed slice's actuals in hand. Planning a phase is identifying
its slices: a new phase's initial plan holds aspects, and no slices. A slice is current from when it starts until it is
delivered, and there is at most one.

**Actuals are recorded for every slice delivered.** They live in the delivered slice's entry in its phase's
`roadmap.json` (§7), under `actuals`:

```json
{ "slug": "foundations", "state": "delivered", "aspects": ["store"],
  "actuals": {
    "complexity": 14,
    "start-time": "2026-09-12T08:05:00Z", "end-time": "2026-09-12T08:48:00Z",
    "tokens-in": 182000, "tokens-in-cached": 1450000, "tokens-out": 41000,
    "thinking-time": 310, "lines-added": 2200, "files-changed": 61 } }
```

`complexity` is the slice's spend: the sum of the complexity of the aspects the slice delivers, derived and not passed
in. Times are UTC, ISO 8601, `thinking-time` is in seconds, and `tokens-in` does not count cached reads; they are
`tokens-in-cached`.

The entry is written **each time a slice completes, with the roadmap update**, by the first of these there is: a
roadmapper session; an orchestrator session sitting on the roadmap and orchestrating its delivery; the worker that
delivered the slice. `lines-added` and `files-changed` are counted from the diffs of the slice's merged pull requests.
They give the other numbers context — a slice that arrives mostly written, as the hackathon's console did, adds many
lines for little complexity — and are not a measure of complexity. Read beside each slice's `complexity`, the actuals
show how well complexity is being assessed and what the budget should be. **A delivered slice is never edited**, and an
ended phase's data stays as it ended (§6).

## 3 Aspects That Can Wait

**Aspects that can wait are not slices.** They are parts of the product that are known — a capability, a decision
still open, a design that will be needed — and that no slice yet depends on. Recording them is what lets them wait
safely; not all of them will ever become a slice of their own, and an aspect is named in a slice's `aspects` when one
takes it on.

An aspect is either one we are **sure** the product will have, or one we are **not yet sure** is real. Each aspect
points at the design document that records it as undecided, where one does.

**An aspect may have parts.** They are the pieces it decomposes into, each an aspect in its own right, sure or not
yet sure, to any depth.

**An aspect may need others.** What it needs is what must be in place before work on it can start: other aspects
or parts of them: aspects need aspects, never slices. Needs are a best guess, as planned slices are, and are revised as freely. Decomposing an
aspect is how the work that needs it gets started sooner: what needs only one part of an aspect need not wait for the
rest of it. A need on a whole means waiting for the whole to end; a whole's own needs hold back all of its parts.

**A leaf aspect may carry a complexity**: a gut-instinct score, a Fibonacci number — 1, 2, 3, 5, 8 or 13. An unscored
aspect counts as 1. Nothing is scored above 13: anything more complex must be broken into parts. A whole has no score
of its own; it takes its parts' (Rationale §4). The slice that delivers an aspect spends its complexity against the
slice budget (§2).

**An aspect has a name and a description**, and the description is written once, in the one place the roadmap
document describes it (§6).

**What is done is history.** A done aspect is never edited — not its name, description, score, needs or place — nor
removed. A whole with a done part keeps its name, id and place: it can still be described, and gain new parts. Another
aspect may stop needing a done one.

**An aspect has a status**: `open` (needs investigation and understanding), `question` (further investigation or
documentation is needed), `mature` (understood, documented and ready to build), `doing` (implementation is under way)
or `done`. It moves `open` → `mature` or `question`; `question` → `mature` or `question`; `mature` → `doing` → `done`.
A whole has none of its own, being as done as its parts.

**What `mature` means depends on the kind of aspect.** For an aspect that is built from a design that already exists,
it is as above: understood, documented and ready to build. Two kinds cannot meet that, because what they depend on is
not written yet, so for them it means that what the work is *about* is known:

* **A design aspect** — one whose work is to write a design — is mature when the **scope of the design is agreed**: what
  has to be designed. Its documentation is the work itself, so it cannot be a precondition of it.
* **A deployable aspect** — one whose work is to deploy something — is mature when **the design covers it**: what has
  to be deployed is known.

Without this, no design slice could ever start (Rationale §8).

**Blocked is not a status**: an aspect is blocked while an
aspect it needs, or that a whole it is part of needs, is not done.

## 4 The Where We Are Picture

A roadmap opens with a picture of where the product has got to, drawn as an SVG, `where-we-are.svg`, beside the
document and linked from it, both the entry document's and the current phase's. It shows:

* **the phases**, in a chain along the top: completed ones green, the current one highlighted, planned ones faint and
  dashed, with a dashed arrow into them. A line beneath separates the phase history from the current phase's slices;
  one arrow leaves the current phase, crosses the line and ends floating above the slices;
* **the slices**, as compact cards: each is titled with its number, name and tickets, its aspects are bullets — ✓ done,
  ▶ doing, ● mature, ? question, ○ open, ✕ blocked — and a planned slice is faint and dashed. A delivered slice is green and the
  current slice highlighted. Slices sit side by side with an arrowhead between each pair, up to four, then switch to a
  vertical stack with arrows between them, the bullets in two columns once a card has more than three.

A roadmap with no phases draws only its slices; a phase with no slices draws only the chain, the line and the arrow.
The aspects are not in it: they are in the aspects gantt chart (§5). It follows the viewer's light or dark theme and
scales to its container.

**4.a Examples**

A phase with three planned slices (dem-ambient made up; the skill's
[example roadmap](https://github.com/weaver-engineering/agent-plugins/tree/main/claude-skills/roadmap/examples/ROADMAP)
holds its data):

![Where we are: three phases, and the current phase's three slices side by side](roadmaps/phase-roadmap-horizontal.svg)

And a phase of seven slices, which stack vertically (the hackathon's own):

![Where we are: the phases, and the current phase's seven slices stacked](roadmaps/phase-roadmap-vertical.svg)

## 5 The Aspects Gantt Chart

A phase's roadmap carries a chart of how we believe the future depends on itself: its aspects, and what each needs
before work on it can start. The architect, supported by an agent, replans it every time a slice lands, so it is
deliberately cheap and disposable — no plan survives contact. It replaces the dependency diagram earlier roadmaps
carried (Rationale §4). It sits in the roadmap's aspects that can wait, not in its opening, because it answers a
different question: not where the product has got to, but what has to happen before a piece of it can start.

It is generated as an SVG, `aspects-gantt.svg`, beside the document and linked from it, with these rules:

* **A leaf lasts its complexity**, in units: 1 if unscored. No dates are shown, only units, counted from the day the
  roadmap was last updated.
* **Unlimited concurrency.** An aspect starts when everything it needs has ended; an aspect that is done has ended at 0, and is not drawn.
  There are no resource limits: only dependencies hold work back.
* **A whole spans its parts**, from its earliest part's start to its latest part's end, drawn as a bracket. A whole's
  needs hold back all of its parts, and a need on a whole means waiting for the whole to end.
* **One row per aspect**, its parts nested beneath it. An arrow runs from what is needed to what needs it, and each
  needed aspect's arrows keep a lane of their own. An aspect we are unsure of is faint and dashed.
* **It draws every aspect not in a delivered slice**, coloured by how near its work is: **the current slice's aspects
  are highlighted**; **a planned slice's aspects are shaded light grey, fainter the further out the slice is
  planned**; an aspect in no slice yet is blue.
* It follows the viewer's light or dark theme and scales to its container.

A roadmap may also present **focused charts**. The future can be complicated, and a chart of only some aspects, and
everything they need, transitively — including what their wholes need — is easier to read. The named aspects are
outlined. A focused chart is for a conversation or a pull request, not for the roadmap: it is printed, not generated
into the document.

**5.a Examples**

A made-up set of aspects, four levels deep and scored 2 to 13, with needs across levels, an aspect waiting on two
wholes, and aspects we are unsure of ([data](roadmaps/gantt-demo.json)):

![The aspects gantt chart: a four-level set of aspects with needs across levels](roadmaps/gantt-demo.svg)

The data that draws it lists each aspect with its `id`, `name`, `description`, `parts`, `needs` and `complexity`.
Excerpt:

```json
{
  "id": "ui", "name": "the review UI", "description": "…", "needs": ["store"],
  "parts": [
    { "id": "diff", "name": "the diff view", "description": "…", "complexity": 8 },
    { "id": "chat", "name": "the editing chat head", "description": "…", "needs": ["relay"],
      "parts": [
        { "id": "panel", "name": "the chat panel", "description": "…", "complexity": 5 },
        { "id": "apply", "name": "applying a chosen resolution", "description": "…", "needs": ["panel"], "complexity": 13 }
      ] },
    { "id": "analyst", "name": "the analyst's view of the work", "description": "…", "needs": ["diff"], "complexity": 8, "unsure": true }
  ]
}
```

The same chart focused on applying a chosen resolution, which shows only what it needs
([focused chart](roadmaps/gantt-demo-focus-apply.svg)):

![A focused chart: applying a chosen resolution, and everything it needs](roadmaps/gantt-demo-focus-apply.svg)

## 6 Phases

A roadmap is divided into **phases**: each a stretch of work with a name and a roadmap of its own, in a folder named for
it. When one phase ends, another begins. The hackathon was DEM's first phase; its next is dem-ambient. Each phase is
delivered, current or future, and each has a slug, a title and a description, all in `ROADMAP/roadmap.json`.

**The entry document** (`ROADMAP/ROADMAP.md`) is generated from it, with the where-we-are picture (§4). Its sections are
the current phase, the planned phases and the completed phases, in that order, each phase with its description:

* a section with nothing in it is left out, and **the others renumber**: with a current phase and completed phases but
  no planned phases, they are §1 Current Phase and §2 Completed Phases; with planned phases as well, §2 is Planned
  Phases and §3 Completed Phases; with no current phase, Planned Phases is §1 and Completed Phases §2;
* a section with several phases has a subsection (§n.m) for each; a section with only one has none;
* each phase's entry names the phase, links to its roadmap, and carries its description. Future phases may be named
  tentatively.

**A phase's roadmap** is generated from the phase's `roadmap.json`, and holds no rationale or how-it-works prose: this
standard is where that lives, and the roadmap links to it. It has a Context (a link back to the entry document, the
context links every roadmap document shares, and this standard), the phase's description, the where-we-are picture, and
then:

* **1 Delivered Slices**, **2 Current Slice** and **3 Planned Slices** — each slice with its tickets, its spend, its
  actuals once recorded, what it requires and what it delivers (§2);
* **4 Future Aspects** — the aspects gantt chart (§5), then a subsection for each top-level whole with a part in no
  slice, in the gantt chart's order, and the aspects with no whole last.

**Each aspect is described once**, in the section for the furthest stage it has reached: a delivered slice, the current
slice, a planned slice, or the future. A whole that no slice names is as far along as its least advanced part: it is
described with its latest part, or in the future while any part is there. The future section reads as if no slice had
been planned, and its headings nest exactly as the gantt chart's aspects do, so the prose and the picture can be read
against each other. Taking an aspect into a slice adds its id to the slice, and the generated document moves its
description to that slice's section. An aspect is written under the italic path of the wholes it is part of, then a
bullet with its complexity and description:

```
*the agent runtime/agent backends*
* **the Claude Agent SDK backend:** (8) - the agent runtime's first backend, over the Claude Agent SDK
* **a Codex backend:** (5, not yet sure) - …
```

**Ending a phase.** **A phase ends only once its current slice is delivered.** The current phase becomes completed,
and the next, a future phase, becomes current. The new phase's `roadmap.json` takes the ended phase's planned slices and
every aspect its delivered slices did not take; what they took is dropped, with its parts and any whole left with no
parts, and the needs on them, because those aspects are done. The roadmap skill does that, and generates the entry
document and the new phase's roadmap. **The ended phase's folder is left exactly as it ended**: its documents and
charts stay as they were, including any form of document or chart a later version of this standard no longer uses.
A phase that ended before a phase's data was kept in `roadmap.json` (DEM's hackathon) has none, and needs none.

## 7 The Roadmap Skill

**Nothing in a roadmap is written or drawn by hand.** The data is JSON, in the `roadmap.json` files (§1), and the
`roadmap` Claude skill changes it, validated, and generates every roadmap document and chart from it, so that keeping a
roadmap current is a change of data rather than an edit, and so that its format cannot drift. Each generated file
carries two **stamps**: a hash of the data it was generated from, and a hash of its own content.

The commands, in groups (the skill's own `SKILL.md` is the full reference, with every flag):

* **The roadmap and its phases:** `init`, `context add|edit|remove`, `phase add|edit|remove|move|start`, `end-phase`
  (§6);
* **Aspects:** `aspect add|edit|score|status|need|unneed|move|remove`;
* **Slices:** `slice add|edit|remove|move|take|drop|start|deliver`;
* **Actuals:** `record` writes a delivered slice's actuals into its entry in the phase's `roadmap.json` (§2); `report`
  prints each slice's complexity beside its duration, tokens, thinking time, lines added and files changed, with the
  phase's totals, and its duration and tokens per unit of complexity, changing nothing;
* **Writing and reading:** `generate` writes the documents and charts; `check` fails if a file is missing, was not
  generated from the stored `roadmap.json`, or has been edited by hand; `render`, `gantt` and `focus <id>...` print a
  chart, changing nothing (a focused chart, §5, is printed and not generated into the document); `import` moves a
  roadmap kept the old way, with gzipped frontmatter and an `actual-complexity.yaml`, to `roadmap.json`.

Each command validates its change and writes nothing if it is refused. The data lists the phases, and each phase's
slices — each with its slug, title, tickets, state (`delivered`, `current` or `planned`) and `aspects`, a list of the
ids of the aspects it delivers, and its `actuals` once delivered — and its aspects, each with an `id`, a `name`, a
`description`, whether it is `unsure`, its `needs`, its `parts` or its `complexity` and `status`. The aspects stay in
the phase's top-level `aspects`; a slice only names them. A slice's spend (§2) is the sum of the complexity of the
leaves it delivers, an unscored one counting as 1, and `record` writes it as the entry's `complexity`.

The skill refuses a need that names no aspect, or that names a slice; two aspects with one id, or two slices with one
slug; an aspect needing itself or its own part or whole; needs that form a cycle; a complexity that is not 1, 2, 3, 5, 8
or 13, or on an aspect that has parts; a status that moves other than along the workflow in §3; an aspect in more than
one slice; a slice that is current or delivered while breaking the rule in §2; any change to what is done or delivered
(§2, §3); and empty text, or text of more than one line. Updating a roadmap is: change the data with the commands,
`generate`, and `check`. `generate` refuses to overwrite a file that was edited by hand, or that it never generated,
unless it is forced, which is for the first generation after `import`. `check` is run on the whole roadmap; it leaves an
ended phase alone.

**The skill's source is in `agent-plugins`**, in `claude-skills/roadmap/`, where it is built and tested, so that it can
eventually be released as a published plugin. It is TypeScript, run with Node and with nothing to install. It is also
installed as a global skill, in `~/.claude/skills/roadmap/`, so that it is available from inside any project's docs
repo — every project's roadmap uses the same skill.

# Rationale

## 1 Why a roadmap is a document, not tickets

Tickets are for work that is ready to be done. Written for work that is not, they bloat the tracker with future
concerns that need housekeeping of their own — re-ranking, rewording, closing what the design has moved past. A
document holds the same knowledge at a fraction of the upkeep, and a planned slice becomes a ticket at the moment it
is next.

## 2 Why delivered slices are immutable, and the next slice's scope is reassessed

A delivered slice is a fact about what was built and what it rested on. Rewriting it as the design moves would lose
the one thing it records. The design documents hold the current design; the roadmap holds its history.

The hackathon's slices grew as it went: the first was built in minutes, the last took hours. A slice's scope is
reassessed each time one is completed, because that is the moment the cost of the last is known and the next can still
be cut down.

## 3 Why a roadmap is generated from data, and check compares stamps

Written or redrawn by hand, a document or a chart costs a careful edit every time anything moves, and each edit is a
chance for its format to drift. An early roadmap also described each aspect in several places — a slice, a list, a
chart — and could not be reviewed, because the aspects in the prose could not be matched to those in the picture.
Generated from one set of data, each aspect is described once, its format is fixed in one place, and upkeep is a change
of data. That is also why a roadmap document holds no explanation of its own: the explanation is here, once.

A generated file is a picture of its data. What goes stale is the data changing without the file being regenerated, and
a stamp of the data catches exactly that. Comparing a drawing byte for byte would fail every roadmap each time the
drawing improved, and would be wrong for an ended phase, whose documents must stay as they were; a later version of the
skill drawing differently breaks no roadmap. Each file also stamps its own content, so a hand edit is caught and
`generate` will not overwrite it by accident.

The data is JSON, in a `roadmap.json` beside the documents it generates, and no longer in frontmatter, for two reasons.
A deployed skill is a copy of its folder with nothing installed, and Node reads JSON without a package. And the data is
now the roadmap's one source, changed by commands and read by people through the documents it generates, so it is kept
as a file of its own rather than compressed into the top of a document it would then have to travel inside.

## 4 Why the aspects are a gantt chart, parts are nested, and complexity is coarse

The where-we-are picture answers where the product has got to; the gantt chart answers what has to happen before a
piece of it can start. Drawn as one, the second question's arrows would tangle the first's. Parts exist to shrink that
second answer: an aspect that needs all of another waits for all of it, while one that needs a single part can start as
soon as that part is in — and decomposing the aspect needed is what makes the difference visible.

The gantt chart replaced a Mermaid dependency diagram that showed the same needs. The architect wants to see how we
believe the future depends on itself, with arrows, in a form that is cheap to regenerate each time a slice lands.
Mermaid's gantt has no dependency arrows, so the chart is drawn as an SVG, and because the future is fluid, text diffs
of it do not matter.

Aspects are deliberately lightweight and may change, vanish, split or consolidate at any time, so the complexity scale
is coarse and quick. The gaps between Fibonacci numbers stop anyone arguing between neighbours, and a high score is the
prompt to decompose.

## 5 Why phases have a folder each, and the entry document never moves

A roadmap that only grows stops being a plan and becomes a log. A phase closes one stretch of work and opens the next:
what was delivered is kept, as it was, in a roadmap of its own; what is left over starts the next phase's roadmap, open
to planning. A symlink to the current phase's roadmap does not work on GitHub — it does not render the target, and
relative images break — and renaming or moving the roadmap as a phase ends would break every link to it. A folder per
phase, with its charts embedded relatively, keeps every link good, and one entry document that never moves is the way
in. Generating an ended phase's documents again would rewrite its history, which is why they are left as they ended. The order of the phases is in the entry document, not in the folders' names.

## 6 Why the slice budget is a complexity ceiling, and why it starts at 45

The hackathon's slices grew until the last one used up one 5-hour token budget and about 97% of the next — the most a
Claude Pro plan can sensibly hold. A budget in time or tokens cannot be set before a slice is built, but complexity can,
because the aspects already carry it. A slice is sized by what it delivers, and the roadmapper and architect already
score that. The budget is a ceiling in the same units, and not a Fibonacci number, because it is a sum of scores and not
a score.

**How 45 was derived.** The hackathon's aspects were scored after the fact (the scores are in [the hackathon roadmap's Appendix](https://github.com/weaver-engineering/dem-docs/blob/main/ROADMAP/hackathon/ROADMAP.md#appendix)) and the slices' totals set
against what the architect remembers of how long each took to build; the slices were not logged, which is why
actuals are now recorded. The totals tracked the remembered build times closely enough to trust: Laws And Judgement (61)
was the big one, and Plugins And Domain Checks (37) took about 45 minutes. The Console (32) scores lower than its
size in lines suggests, because its UI arrived largely complete from a generated TypeScript prototype and the work was
the REST function, Cognito and the deployment.

The retrospective's guide was a slice of about 90 minutes at most, which the architect judges a little generous. The
budget was not found by halving the largest slice, because complexity is not linear in time — twice the spend is more
than twice the work. It was placed just above the slice that took about 45 minutes, so that no slice is more than a little
more complex than Plugins And Domain Checks. It is a cap, not a size to aim for.

**Why it is recalibrated.** 45 rests on one remembered duration and scores given after the event. Every slice delivered
records its actuals in its entry in `roadmap.json`, and the budget is changed when the entries show that slices of a given spend
use more of the token budget of a 5-hour window than they should, or far less. The budget is right when a slice at the
cap fits well inside that token budget. Aspect scores are corrected the same way.

## 7 Why aspects have a status, and a slice is a set of aspects

Slices first listed items of their own, each marked done, doing or blocked, and those bullets duplicated the aspects:
the same piece of the product was described once in a slice and once, with its complexity and needs, as an aspect. A
slice's spend then had to be passed in by hand. Making a slice the set of aspects it delivers, named by id, leaves each
aspect in one place with its complexity, status and needs, so the spend, the card's bullets and the recorded actuals are
all computed from the same thing.

The status says how far an aspect is from being built, not whether it is waiting. `open` and `question` are
understanding, `mature` is ready to build, `doing` and `done` are the build. Moves are restricted to that order, so a
roadmap cannot claim work started on something no one has understood. **Blocked is derived and not a status** because it
is a fact about other aspects: it changes when something it needs is done, with no one editing the blocked aspect. A slice
starts only when every aspect in it is mature and unblocked from outside, which is how a slice is kept from stalling an
agent part-way through. Needs name aspects and not slices, because a slice is only a grouping of aspects and may be
regrouped; an aspect waits on what it truly needs.

## 8 Why a phase's design can be a slice of its own, and what `mature` means for it

A phase's design is work like any other: it takes effort, it can overrun, and what it produces is what later slices
rest on. So it is planned the same way, as aspects (a design aspect for each thing to be designed), grouped into a
slice, scored, and spent against the slice budget. A design slice that is not costed is the one that quietly grows, and
a design that is only a precondition is invisible to the plan: nothing says how long it will take or what waits on it.
Needs then say what the design holds up, and the aspects that need it start when it is done.

That only works if a design slice can start. A slice starts when every aspect in it is mature (§2), and `mature` meant
understood, documented and ready to build: a design aspect cannot be documented before it is written, so no design
slice could ever have started. A design aspect is therefore mature when its **scope** is agreed — what has to be
designed — which is all that is needed to begin writing it; the design itself is the work. A deployable aspect is
mature when the design covers it, because what has to be deployed is then known, and the design it rests on is itself a
delivered aspect by the time it is built.

## 9 Why what is done is never edited

A done aspect and a delivered slice are facts about what was built, and the actuals recorded against them are
measurements of it. Editing either would let the plan quietly agree with the outcome, and the budget and the scores
could no longer be recalibrated against it. A whole with a done part keeps its name, id and place for the same reason,
and because a delivered slice names the part by id: a roadmap's past must still read as it did.
