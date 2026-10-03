# Roadmaps

## Context
* [Documentation Standards](documentation-standards.md) - the document shape a roadmap follows, and the directory structure it sits beside
* [Product/Service Model](product-service-model.md) - the Product a roadmap is the build plan of
* [dem-docs ROADMAP/](https://github.com/weaver-engineering/dem-docs/blob/main/ROADMAP/ROADMAP.md) - the first roadmap, which this standard is drawn from
* [Planning a Roadmap](roadmaps/planning-a-roadmap.md) - the architect's guide to scoring, budgeting and recalibrating with an agent's help
* [The roadmap skill](https://github.com/weaver-engineering/agent-plugins/tree/main/claude-skills/roadmap) - generates a roadmap's charts and ends its phases; its own `SKILL.md` is the full reference

A project's **roadmap** says how its product is being built: the phases the work falls into, in each phase the slices
delivered so far and the slices planned next, and the aspects of the product that are known and can wait. It is the
one place the whole of that can be read without filling Linear with work that is not yet ready to be done.

## 1 Where It Lives

A roadmap is a `ROADMAP/` directory at the root of the project's docs repo, beside `PRODUCT.md`:

```
ROADMAP/ROADMAP.md              the entry document: the phases, and which is current. It never moves.
ROADMAP/{phase-slug}/ROADMAP.md one phase's roadmap: its slices and aspects, and the charts it embeds, beside it.
ROADMAP/{phase-slug}/actual-complexity.yaml what each slice the phase delivered actually cost (§2).
```

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

* **Requires** — what had to be defined in the docs before it could be built: the concepts and decisions its tickets
  point their workers at;
* **Delivers** — what it adds to the running product.

A delivered slice also names its tickets, linked to Linear.

**Delivered slices are history, and do not change.** They record what was built and what it rested on at the time;
where the design has moved on since, the design documents say so, not the roadmap.

**Planned slices are aspirations, and do.** They are the current best guess at what comes next, and in what order,
and are rewritten as that changes. What is uncertain about a planned slice is the slice itself — what goes into it,
and when — not the things it names, which are known to be needed. **A planned slice becomes a ticket when it is next,
and not before.**

**A slice has a budget.** The **slice budget** is a ceiling on complexity, not itself a complexity and not a number on
the Fibonacci scale (§3). It is a cap and not a target: it exists to keep a slice from overloading an agent, and a
slice that spends less is not a worse slice. A planned slice names the aspects it will deliver, each with its complexity; the sum of
those complexities is the slice's **spend**, an unscored aspect counting as 1, and **a slice's spend must not exceed
the budget.** An aspect whose slice would not fit is split into parts until the slice that delivers it does. A behaviour may be
added to an aspect for the sake of end-to-end testing, even if it is in no use case itself, provided it lets a behaviour
that is part of a requirement be tested end to end — a list of the configured checks, say, so that the UI aspect has
something to test against. It is work like any other and counts in the spend. The
budget is **45**. That is its starting value, derived from the complexity of the aspects the hackathon delivered
(Appendix §1, Rationale §6), and it is recalibrated from the recorded actuals (below) as slices run against it.

**Whenever a slice is completed, the scope of the next slice is reassessed**, so that slices do not quietly grow: its
spend is summed again against the budget, with the completed slice's actuals in hand. Planning a phase is identifying
its slices: a new phase's initial plan holds aspects, and no slices.

**Actuals are recorded for every slice delivered.** Each phase keeps an `actual-complexity.yaml` in its folder,
`ROADMAP/{phase-slug}/`, in which each slice the phase delivered has an entry:

```yaml
slices:
  - slug: the slice's slug
    complexity: the slice's spend, as planned
    start-time: UTC date-time, ISO 8601
    end-time: UTC date-time, ISO 8601
    tokens-in: number
    tokens-out: number
    thinking-time: seconds
    lines-added: number
    files-changed: number
```

The entry is written **each time a slice completes, with the roadmap update**, by the first of these there is: a
roadmapper session; an orchestrator session sitting on the roadmap and orchestrating its delivery; the worker that
delivered the slice. `lines-added` and `files-changed` are counted from the diffs of the slice's merged pull requests.
They give the other numbers context — a slice that arrives mostly written, as the hackathon's console did, adds many
lines for little complexity — and are not a measure of complexity. Read beside each slice's `complexity`, the file
shows how well complexity is being assessed and what the budget should be. An ended phase's file stays in its folder as
it ended (§6).

## 3 Aspects That Can Wait

**Aspects that can wait are not slices.** They are parts of the product that are known — a capability, a decision
still open, a design that will be needed — and that no slice yet depends on. Recording them is what lets them wait
safely; not all of them will ever become a slice of their own, and an aspect is moved into a slice when one takes it
on.

An aspect is either one we are **sure** the product will have, or one we are **not yet sure** is real. Each aspect
points at the design document that records it as undecided, where one does.

**An aspect may have parts.** They are the pieces it decomposes into, each an aspect in its own right, sure or not
yet sure, to any depth.

**An aspect may need others.** What it needs is what must be in place before work on it can start: other aspects,
parts of them, or slices. Needs are a best guess, as planned slices are, and are revised as freely. Decomposing an
aspect is how the work that needs it gets started sooner: what needs only one part of an aspect need not wait for the
rest of it. A need on a whole means waiting for the whole to end; a whole's own needs hold back all of its parts.

**A leaf aspect may carry a complexity**: a gut-instinct score, a Fibonacci number — 1, 2, 3, 5, 8 or 13. An unscored
aspect counts as 1. Nothing is scored above 13: anything more complex must be broken into parts. A whole has no score
of its own; it takes its parts' (Rationale §4). The slice that delivers an aspect spends its complexity against the
slice budget (§2).

## 4 The Where We Are Picture

A roadmap opens with a picture of where the product has got to, drawn as an SVG, `where-we-are.svg`, beside the
document and linked from it. It shows:

* **the phases**, in a chain along the top: completed ones green, the current one highlighted, planned ones faint and
  dashed, with a dashed arrow into them. A line beneath separates the phase history from the current phase's slices;
  one arrow leaves the current phase, crosses the line and ends floating above the slices;
* **the slices**, as compact cards: each is titled with its number, name and tickets, its items are bullets — ✓ done,
  ▶ doing, ? open question, ✕ blocked — and a planned slice is faint and dashed. A delivered slice is green and the
  current slice highlighted. Slices sit side by side with an arrowhead between each pair, up to four, then switch to a
  vertical stack with arrows between them, the bullets in two columns once a card has more than three.

A roadmap with no phases draws only its slices; a phase with no slices draws only the chain, the line and the arrow.
The aspects are not in it: they are in the aspects gantt chart (§5). It follows the viewer's light or dark theme and
scales to its container.

**4.a Examples**

A phase with three planned slices (dem-ambient made up), with its [metadata](roadmaps/phase-roadmap-horizontal.json):

![Where we are: three phases, and the current phase's three slices side by side](roadmaps/phase-roadmap-horizontal.svg)

And a phase of seven slices, which stack vertically (the hackathon's own), with its
[metadata](roadmaps/phase-roadmap-vertical.json):

![Where we are: the phases, and the current phase's seven slices stacked](roadmaps/phase-roadmap-vertical.svg)

## 5 The Aspects Gantt Chart

A phase's roadmap carries a chart of how we believe the future depends on itself: its aspects, and what each needs
before work on it can start. The architect, supported by an agent, replans it every time a slice lands, so it is
deliberately cheap and disposable — no plan survives contact. It replaces the dependency diagram earlier roadmaps
carried (Rationale §4). It sits in the roadmap's aspects that can wait, not in its opening, because it answers a
different question: not where the product has got to, but what has to happen before a piece of it can start.

It is drawn as an SVG, `aspects-gantt.svg`, beside the document and linked from it, with these rules:

* **A leaf lasts its complexity**, in units: 1 if unscored. No dates are shown, only units, counted from the day the
  roadmap was last updated.
* **Unlimited concurrency.** An aspect starts when everything it needs has ended; a delivered slice has ended at 0.
  There are no resource limits: only dependencies hold work back.
* **A whole spans its parts**, from its earliest part's start to its latest part's end, drawn as a bracket. A whole's
  needs hold back all of its parts, and a need on a whole means waiting for the whole to end.
* **One row per aspect**, its parts nested beneath it. An arrow runs from what is needed to what needs it, and each
  needed aspect's arrows keep a lane of their own. An aspect we are unsure of is faint and dashed.
* It follows the viewer's light or dark theme and scales to its container.

A roadmap may also present **focused charts**. The future can be complicated, and a chart of only some aspects, and
everything they need, transitively — including what their wholes need — is easier to read. The named aspects are
outlined. A focused chart is for a conversation or a pull request, not for the roadmap: it is printed, not generated
into the document.

**5.a Examples**

A made-up set of aspects, four levels deep and scored 2 to 13, with needs across levels, an aspect waiting on two
wholes, and aspects we are unsure of ([metadata](roadmaps/gantt-demo.json)):

![The aspects gantt chart: a four-level set of aspects with needs across levels](roadmaps/gantt-demo.svg)

The metadata that draws it lists each aspect with its `id`, `parts`, `needs` and `complexity`. Excerpt:

```json
{
  "aspect": "the review UI", "id": "ui", "needs": ["store"],
  "parts": [
    { "aspect": "the diff view", "id": "diff", "complexity": 8 },
    { "aspect": "the editing chat head", "id": "chat", "needs": ["relay"],
      "parts": [
        { "aspect": "the chat panel", "id": "panel", "complexity": 5 },
        { "aspect": "applying a chosen resolution", "id": "apply", "needs": ["panel"], "complexity": 13 }
      ] },
    { "unsure": "the analyst's view of the work", "id": "analyst", "needs": ["diff"], "complexity": 8 }
  ]
}
```

The same chart focused on applying a chosen resolution, which shows only what it needs
([focused chart](roadmaps/gantt-demo-focus-apply.svg)):

![A focused chart: applying a chosen resolution, and everything it needs](roadmaps/gantt-demo-focus-apply.svg)

## 6 Phases

A roadmap is divided into **phases**: each a stretch of work with a name and a roadmap of its own, in a folder named for
it. When one phase ends, another begins. The hackathon was DEM's first phase; its next is dem-ambient.

**The entry document** (`ROADMAP/ROADMAP.md`) describes the phases as they are identified, with the where-we-are
picture (§4) generated from its metadata. Its sections are the current phase, the planned phases and the completed
phases, in that order:

* a section with nothing in it is left out, and **the others renumber**: with a current phase and completed phases but
  no planned phases, they are §1 Current Phase and §2 Completed Phases; with planned phases as well, §2 is Planned
  Phases and §3 Completed Phases; with no current phase, Planned Phases is §1 and Completed Phases §2;
* a section with several phases has a subsection (§n.m) for each; a section with only one has none;
* each phase's entry names the phase, links to its roadmap, and says in a paragraph what the phase is for, or what it
  delivered. Future phases may be named tentatively.

**A phase's roadmap** has the sections of §§2–5: how the roadmap works, delivered slices, planned slices, and the
aspects that can wait with their gantt chart, then a Rationale. It keeps three headings exactly as written, because
ending the phase depends on them: `## Context`, `## 2 Delivered Slices` and `## 4 Aspects That Can Wait`. Ending a phase
carries the Context and the how-it-works prose over, clears the slices, and carries the aspects section over; if one of
the three is missing it stops, writing nothing. A new phase's roadmap starts with no slices, and with
every undelivered aspect carried over from the phase that ended. Needs on slices of the ended phase are dropped,
because those slices have ended.

**Ending a phase.** The current phase becomes completed, and the next becomes current, or is added. The next phase's
roadmap is created from the ended one, cleared down to the undelivered aspects, with its own charts; the entry
document's metadata and picture are regenerated. The roadmap skill does that mechanical part. The architect-supported
agent then updates the entry document's prose — the current and completed phases' sections — and the new roadmap's
introduction. **The ended phase's folder is left exactly as it ended**: its charts stay as they were, including any
form of chart a later version of this standard no longer uses.

## 7 The Roadmap Skill

**The charts are never drawn or edited by hand.** They are generated from metadata by the `roadmap` Claude skill, so
that keeping a roadmap current is a change of state rather than a redrawing, and so that its format cannot drift. The
metadata is JSON, stored gzipped and base64-encoded in the roadmap document's frontmatter under `roadmap`; each
generated chart sits beside the document as an SVG, linked from between its own two markers in it. Each chart carries a
**stamp**: a hash of the metadata it was drawn from.

| Command | Does |
|---|---|
| `extract <doc> [<json>]` | decompresses the metadata to JSON |
| `apply <doc> <json>` | validates the JSON, stores it in the frontmatter, and regenerates the links and charts |
| `check <doc>` | fails if a chart's stamp is not the stored metadata's, or a link is not the one the metadata calls for |
| `render <doc\|json>` | prints the where-we-are chart, changing nothing |
| `gantt <doc\|json>` | prints the aspects gantt chart, changing nothing |
| `focus <doc\|json> <id>...` | prints a focused gantt chart of the named aspects and what they need (§5), changing nothing |
| `end-phase <entry-doc> <slug>` | ends the current phase and starts the next (§6) |

The metadata lists the `phases` (each a name and a state: `completed`, `current` or `planned`), the slices — each with its
section number, title, tickets, state (`delivered`, `current` or `planned`) and items (`done`, `doing`, `question`,
`blocked` or `plain`) — and the aspects, each plain text for one we are sure of or `unsure` for one we are not, or in a
long form that adds an `id`, its `parts`, its `needs` and its `complexity`. `apply` refuses a need that names no aspect
or slice, two aspects with one id, an aspect needing itself or its own part or whole, needs that form a cycle, a
complexity that is not 1, 2, 3, 5, 8 or 13, and a complexity on an aspect that has parts. Updating a roadmap is:
extract the metadata, change it, apply it, bring the prose into line, and check. `check` is run on the entry document
and the current phase's roadmap; an ended phase is left alone, and `apply` is never run on one.

**The skill's source is in `agent-plugins`**, in `claude-skills/roadmap/`, where it is built and tested, so that it can
eventually be released as a published plugin. It is TypeScript, run with Node and with nothing to install. It is also
installed as a global skill, in `~/.claude/skills/roadmap/`, so that it is available from inside any project's docs
repo — every project's roadmap uses the same skill.

The skill also records and reads a phase's `actual-complexity.yaml` (§2), so that actuals are written the same way by
whoever writes them; that extension is [WVR-234](https://linear.app/weaver-engineering/issue/WVR-234).

# Appendix

## 1 The Hackathon's Aspects, Scored

The aspects each of DEM's hackathon slices delivered
([the hackathon roadmap](https://github.com/weaver-engineering/dem-docs/blob/main/ROADMAP/hackathon/ROADMAP.md) §2),
scored on the complexity scale (§3) after the fact, from the roadmap and the preparation retrospective, and corrected
by the architect. The totals are each slice's spend. They are what the starting budget was derived from (Rationale §6).

**2.1 Foundations — 14**

| Aspect | Complexity |
|---|---|
| The `dem` and `dem-docs` repositories set up to standard, with CI | 3 |
| Continuous deployment: the CDK stack on every merge, and `--as` environments | 5 |
| The outline architecture | 3 |
| The section model, registries, scope, table and MCP server, as decisions | 3 |

**2.2 Walking Skeleton — 12**

| Aspect | Complexity |
|---|---|
| `create_doc`, `update_doc` and `read_doc` over the MCP function, sectioning each document | 5 |
| A section read as a report with its ancestry's Context | 2 |
| Section checksums | 1 |
| A static bearer token | 1 |
| Seeding registries | 2 |
| The dev setup guide | 1 |

**2.3 Claims — 21**

| Aspect | Complexity |
|---|---|
| Claims made: named, engine-identified, anchored | 5 |
| Relinking by checksum on every write | 5 |
| Soundness reported when claims are read | 3 |
| Self-healing resync within a time budget | 5 |
| `list_claims` by document or target | 2 |
| Reading a whole rationale or appendix | 1 |

**2.4 The Next Unit Of Work — 35**

| Aspect | Complexity |
|---|---|
| Check sets in a versioned bucket, validated when saved | 5 |
| Work scopes | 3 |
| The fold, with arrays combining | 8 |
| The `soundness` and `competition` checks | 8 |
| Findings, with their resolutions as skills | 5 |
| Acknowledgements, confirmed by re-running the check | 3 |
| `next_unit_of_work`, `acknowledge_finding` and `read_model` | 3 |

**2.5 Plugins And Domain Checks — 37**

| Aspect | Complexity |
|---|---|
| The plugin, check and projection interfaces | 8 |
| Plugins loaded in dependency order | 3 |
| The tool gateway | 5 |
| The document store, its bucket and the registries lens | 8 |
| The built-in doc-standards plugin | 5 |
| Check sets with real levels, and maturity reported | 3 |
| The claim model in valued positions | 5 |

**2.6 Laws And Judgement — 61**

| Aspect | Complexity |
|---|---|
| Claims naming their law and its checksum, refused on save otherwise | 5 |
| The fold filling each law's positions; plugins declaring a position twice failing to load | 5 |
| ElastiCache Serverless for Valkey, caching the current and next unit of work per work scope | 8 |
| `set-current-work` | 2 |
| `dem.judge.law` and the record of what a document was judged against | 8 |
| The `judgement` check, `unjudged-document` and `law-drift` | 5 |
| The tool gateway offering only the current work's tools | 3 |
| The DEM agent | 5 |
| Change requests and `dem.change-request` | 5 |
| Acknowledging through the gateway, and withdrawing soft resolutions | 3 |
| Every tool named with hyphens | 1 |
| `dem.reconcile` for competition | 5 |
| Test-only plugins and check sets, and the fixture plugin for the end-to-end run | 3 |
| Disambiguated stacks torn down after 48 hours inactive | 3 |

**2.7 The Console — 32**

| Aspect | Complexity |
|---|---|
| The REST API function beside the MCP function, calling the same service operations | 8 |
| The console on S3 behind CloudFront, `/api` routed to the HTTP API, deployed by the pipeline and torn down with `--as` environments | 8 |
| The analyst's soft resolutions over REST | 5 |
| Cognito: analysts signing in, added by `pnpm add-analyst` | 5 |
| The console moved into the monorepo on the service's types, its mock client kept | 3 |
| Changes noticed by polling every 10 seconds | 2 |
| A registry entry, written when a registry is seeded | 1 |

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

## 3 Why the charts are generated, and check compares a stamp

Redrawn by hand, a chart costs a careful edit every time anything moves, and each edit is a chance for its format to
drift. Generated, its format is fixed in one place and its upkeep is a change of state in the metadata.

A chart is a picture of its metadata. What goes stale is the metadata changing without the chart being redrawn, and a
stamp catches exactly that. Comparing the drawing byte for byte would fail every roadmap each time the drawing
improved, and would be wrong for an ended phase, whose charts must stay as they were. The metadata is JSON because a
deployed skill is a copy of its folder with nothing installed, and Node reads JSON without a package. It is compressed
into the frontmatter because it is not for reading — the prose and the charts are — and it should travel with the
document it describes without filling the top of it.

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
in. The order of the phases is in the entry document, not in the folders' names.

## 6 Why the slice budget is a complexity ceiling, and why it starts at 45

The hackathon's slices grew until the last one used up one 5-hour token budget and about 97% of the next — the most a
Claude Pro plan can sensibly hold. A budget in time or tokens cannot be set before a slice is built, but complexity can,
because the aspects already carry it. A slice is sized by what it delivers, and the roadmapper and architect already
score that. The budget is a ceiling in the same units, and not a Fibonacci number, because it is a sum of scores and not
a score.

**How 45 was derived.** The hackathon's aspects were scored after the fact (Appendix §1) and the slices' totals set
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
adds an entry to `actual-complexity.yaml`, and the budget is changed when the entries show that slices of a given spend
use more of the token budget of a 5-hour window than they should, or far less. The budget is right when a slice at the
cap fits well inside that token budget. Aspect scores are corrected the same way.
