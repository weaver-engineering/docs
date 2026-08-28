# Specific Behaviors

## Context
* [Feature Workflow](feature-workflow.md) - the workflow step (`Design Service`) this document is an output of
* [Design Feature Instructions §6-§9](design-feature-instructions.md) - the process that produces and checks this
  document's content; this document defines the shape, that one defines how to get there
* [Required Behaviors](required-behaviors.md) - the Required Service Behaviour a Predicted Service Behaviour is
  bound from, and reconciled against
* [Use Cases §2](use-cases.md) - what an operation is
* [Design Directory And HLD](design-directory-and-hld.md) - Internal Components and External Dependencies, whose
  declared `calls:` a call tree walks
* [Weaver Engineering Workflows §3, §4](../weaver-workflows.md) - where Predicted Service Behaviour sits among
  the three kinds of behaviour, and the reconciliation it's checked by
* [Specific Behavior Template](../../templates/SPECIFIC-BEHAVIOR-TEMPLATE.md) - `reconciliation.yaml`'s own shape

## 1 What A Predicted Service Behaviour Is

A Predicted Service Behaviour is Design's own claim, for one Required Service Behaviour, about what a Service's
chosen components and functions will actually produce: the concrete call tree — real `{service-slug}.{component-slug}.{function-slug}`
and `{service-slug}.{dependency-slug}.{function-slug}` addresses, with literal example values — traced by
following the Required Service Behaviour's own entry state through this design task's own bound pseudocode
(Design Feature Instructions §6-§7).

It is never authored freehand: it's read off a traced call tree through the Service's own call graph (§2), the
same way the Required Service Behaviour it binds was itself derived, not invented (Required Behaviors §2). And it
is always reconciled against that Required Service Behaviour (Weaver Engineering Workflows §4) — the two are
independent artifacts on purpose, never one collapsing into the other, so that reconciliation stays a cheap
checksum comparison rather than a fresh semantic walk every time either one is touched.

There is no separate prose Given/When/Then document for a Predicted Service Behaviour. The Required Service
Behaviour's own Given and Required Effect (Required Behaviors §4, §5) already state, in human-readable prose,
what's required; the call tree — comment-annotated with the concrete interaction at each node, the same
convention already used below — states what Design predicts actually happens to satisfy it. Together they're
what a Chunk's failing tests are built from; splitting the same fact across two documents that could drift apart
would cost more than it buys.

A specific behaviour's When is always exactly one *operation* (Use Cases §2) — one invocation of one
Service's own `interface` function. A use case's actor may cross that boundary more than once to reach their
goal; each crossing is its own behaviour, never several crossings folded under one When.

## 2 The Call Graph Is Data; A Call Tree Is A Walk Through It

A function's `calls:` defines what *could* happen — every address it might, under some circumstance, invoke. A
behaviour's call tree defines what *actually* happens — the one walk through that space a given operation takes.
Logically, one Service has exactly one call graph — every component function's `calls:` declaration is one set of
edges in it, and every behaviour's call tree is a walk through some part of it (occasionally crossing into another
Service's own `interface`, where the data flow calls for it — Design Directory And HLD §4.1). Drawing the whole
graph as a single diagram would be too large to read; a behaviour doesn't need the whole graph, only the one tree
relevant to its own scenario.

A call is a tree, not a list: when a function calls another, control returns to the caller once the callee is
done, and the caller may then call something else — a sibling of the first call, not something the first call's
own target called. A call tree is a required, nested YAML structure of addresses —
`{service-slug}.{component-slug}.{function-slug}` or `{service-slug}.{dependency-slug}.{function-slug}` — rooted
at one of the Service's own `interface` functions, each node's `children:` listing what it called, in call order:

```yaml
call_tree:
  address: "accounts.interface.view-account"
  children:
    - address: "accounts.session-cache.resolve"
      children:
        - address: "accounts.auth-service.verify"  # verify(abcd) -> authenticated(john)
    - address: "accounts.session-cache.find-account"
      children:
        - address: "accounts.accounts-store.find"  # find(john) -> johnAccountDoc
```

This says: the entry point calls `session-cache.resolve`, which calls `auth-service.verify` and returns; the
entry point then separately calls `session-cache.find-account` (a sibling of the first call, not something
`auth-service.verify` itself called), which calls `accounts-store.find` and returns. The inline comment on each
leaf is what makes the tree human-legible on its own — the concrete interaction, not just its address — standing
in for the prose Then §1 explains this format doesn't otherwise carry.

An External Dependency operation is always a leaf (Design Directory And HLD §3: it never declares `calls:`), but
a leaf isn't always an External Dependency — a component function that does its own work without calling anything
further is just as valid a leaf.

## 3 Two Independent Views, Reconciled By Review

A function's `calls:` and a behaviour's call tree are not the same fact recorded twice — they're two independent
views of it, formed two different ways:

* **`calls:`** is bottom-up: whoever defines a function declares, from that function's own Purpose, what it
  *could* call. Formed once, at the function's own definition.
* **A call tree** is top-down: whoever derives a behaviour determines what a Required Service Behaviour's own
  scenario *actually* causes to happen, reasoning from each candidate function's stated Purpose.

The two won't always agree on a first pass. A node present in a tree but missing from its parent's `calls:`
doesn't mean the tree is wrong; it might mean the `calls:` declaration is incomplete. Either way, the disagreement
is the useful signal — exactly what Design Feature Instructions §8 exists to catch and resolve.

## 4 Where It's Recorded

Every behaviour this design task predicts is recorded in that task's own `reconciliation.yaml`
(`docs/design/{feature-slug}/{design-task-ref}/reconciliation.yaml`), keyed by its Required Service Behaviour's
own address (`{service-slug}.{operation-slug}-N`) — bound pseudocode, call tree, checksums, and `reviewed` status
all together (Specific Behavior Template). There is no per-behaviour markdown document, and no project-wide
`{slug}-NNN` numbering sequence of its own: a Predicted Service Behaviour's identity is always the Required
Service Behaviour it binds.

## 5 Design Review: Two Comparisons, Not One

Design review checks the design against what it's actually meant to satisfy through two independent comparisons
(Weaver Engineering Workflows §4), never conflated:

* **Predicted vs. Required (Service-level, Design Feature Instructions §9.1)** — does this call tree, traced
  under the Required Service Behaviour's own entry state, actually produce its Required Effect? A cheap checksum
  comparison once both sides are independently recorded, re-run whenever either's checksum moves.
* **Human review (§9.2)** — systematic isn't the same as correct. A fine detail gets missed in the pseudocode, a
  misunderstanding survives mechanical reconciliation, and the process itself has no way to catch that it's
  producing something subtly wrong; only a human checking the actual result against their own understanding of
  the Required Service Behaviour can. These are the clearest, most human-legible statement of the Service's
  actual behaviour anywhere in the design — clearer than the bound pseudocode, clearer than the call trees
  underneath. That's what makes this the critical checkpoint, not a formality after the real work is done: these
  behaviours are what a Chunk's failing tests are built from, and an architect who doesn't genuinely understand
  one inherits the same gap at every PR built against it.

# Rationale

**Why there's no separate prose document for a Predicted Service Behaviour, unlike the Required Service Behaviour
it binds.** An earlier draft of this rework kept a full Given/When/Then markdown document per behaviour, filed
under the design task directory, alongside `reconciliation.yaml`. That duplicated almost everything the Required
Service Behaviour already states (the Given, the effect) while only ever adding the concrete call tree — which
already needs to exist as structured YAML for reconciliation to walk mechanically. A comment-annotated call tree
node already carries the one piece of information a prose Then would have added ("what got called, with what,
returning what"); a third artifact restating it in prose would be pure duplication with its own drift risk, not a
clearer document.

**Why `calls:` and a call tree are two independent views rather than one fact checked against itself.** Making
`calls:` deterministic from what a function actually does would mean capturing that function's real logic in the
design — a specification of the implementation, not a design of it. Treating `calls:` as a declared expectation,
and a call tree as an independently traced determination, keeps both cheap to write while still giving something
to check them against each other with.

**Why a call tree lives in `reconciliation.yaml`, not inline in a behaviour's own prose.** A call tree, a
checksum, and a review timestamp are all the same kind of fact — structured, mechanical, produced and consumed by
tooling — while a Required Service Behaviour's Given/Required Effect is narrative, written for a human to read and
reason about. Keeping every behaviour's mechanical facts in one place, keyed by the same address the Required
Service Behaviour it binds already uses, means anything a script needs is one lookup, not a walk through prose.

**Why a call tree, not a list.** A real invocation returns control to its caller before that caller (maybe) calls
something else. A flat list of addresses can't represent that without looking like each call's target called the
next address in the list — see the worked example, where a flat rendering would misleadingly read as
`auth-service.verify` calling `session-cache.find-account`.
