# Chunk Scope

## Context
* [Feature Workflow](feature-workflow.md) §3 - the `Chunk The Design` step this artefact is the entry-scope for
* [Design Directory And HLD](design-directory-and-hld.md) §1 - the per-design-task directory this artefact is a
  sibling within
* [Design Feature Instructions §7, §9.2](design-feature-instructions.md) - the process that decides a behaviour is
  new/mutated/deleted, and when a design task's own work is actually complete
* [Required Behaviors §4](required-behaviors.md) - the `{use-case-or-service-slug}.{operation-slug}-N` addressing
  this artefact's own behaviours list uses

A Feature comprises many Services, not all designed and delivered together — a single Feature's `service-flows.md`
accumulates across however many separate design tasks it takes to design them all. Chunk Scope is the artefact
that says, for one specific design task, exactly which specific behaviours are now required of the system as a
result of its work — new ones it introduced, existing ones it changed, existing ones it removed — so that [Chunk
The Design](feature-workflow.md) has an unambiguous, bounded scope to work from, instead of the Feature's entire,
ever-growing design history.

## 1 Purpose

Chunk Scope plays no role in design itself. Nothing in [Design Feature Instructions](design-feature-instructions.md)
reads it, and it records no fact the design task directory doesn't already carry elsewhere — it is a pure
consequence of a design task reaching completion, never an input to reaching that completion.

## 2 How It's Built

Not a single, end-of-task computation — each entry is recorded the moment its status is actually decided, which
already happens at three specific points in the process, never retroactively diffed or reconstructed afterward:

* Design Feature Instructions §7 predicts a behaviour for the first time → append `{address, status: new}`.
* §9.2's `//REDESIGN_REQUIRED` resolves as **accept**, for an address belonging to a *different*, already-shipped
  design task → append `{address, status: mutated}` in *this* task's own chunk scope. An address that only has a
  `new` entry in this same, still-open task's own chunk scope needs nothing further — it stays `new`.
* §9.2's `//REDESIGN_REQUIRED` resolves as **remove**, for an address belonging to a different, already-shipped
  design task → append `{address, status: deleted}`. For an address only ever `new` in this same, still-open
  task, remove its `new` entry entirely instead.

The file starts existing from the first `new` entry a design task produces, and keeps growing throughout the
task's own lifetime. It stops growing once the design task itself is complete: Design Feature Instructions §9.2
reaching project-wide, fixed-point completion covering every Service the design task named.

## 3 Location, Naming, And The Completion Signal

`chunk-scope.yaml`, filed at the root of the design task's own directory —
`docs/design/{feature-slug}/{design-task-ref}/` (Design Directory And HLD §1) — sibling to `hld.md` and
`reconciliation.yaml`, since it's a summary artefact over that task's own content, not one more Service proposal
within it. No `{design-task-ref}-` filename prefix is needed: the directory itself already scopes it.

The file's own existence only means a design task has produced at least one behaviour so far — it does not mean
the task is complete. Completion is a separate, later signal: the HLD's own §1 Scope (Design Directory And HLD
§2) links to a design task's chunk scope only once §9.2 has reached full, project-wide completion for it. A
design task with no such link is still in progress, or was interrupted, regardless of how many entries its own
`chunk-scope.yaml` already holds.

## 4 Required Shape

Exactly one field is required:

```yaml
behaviors:
  - address: "payments.charge-card-1"
    status: new       # new | mutated | deleted
  - address: "payments.charge-card-1.1"
    status: mutated
  - address: "notifications.send-receipt-2"
    status: deleted
```

Every specific behaviour this design task's work touched, addressed the same way Required Behaviors and Specific
Behaviors already address one (`{use-case-or-service-slug}.{operation-slug}-N`), tagged with exactly one status:

* **`new`** — didn't exist before this design task; a wholly new test to write.
* **`mutated`** — existed and passed before, its expected result changed; an existing test to edit.
* **`deleted`** — existed and passed before, no longer applies under the evolved design at all (Design Feature
  Instructions §9.2's third `//REDESIGN_REQUIRED` resolution — removal); an existing test to retire.

`mutated` and `deleted` both mean touching a test that's already passing, not adding a new one — the ordinary
spec/test/build pathway is deliberately built to resist exactly that, so both need the quick pathway
([the chunk sequence](the-chunk-sequence.md)) instead of the ordinary one. `new` doesn't.

## 5 Optional Fields

Everything else is a convenience, not a requirement. [Chunk The Design](feature-workflow.md) has access to the
whole design task directory, not just this one file, and may or may not use any of these:

```yaml
feature: {feature-slug}
service: {service-slug}
hld: docs/design/{feature-slug}/{design-task-ref}/hld.md
delivers: [find-curated-truth.index-a-path, find-curated-truth.search-documentation]  # operations this design task actually targeted
modifies:                          # operations whose behaviour changed as a side effect, grouped by Feature
  {feature-slug-a}: [some-use-case.some-operation]
  {feature-slug-b}: [another-use-case.another-operation]
```

`delivers` stays a flat list — a design task only ever targets operations within its own one Service. `modifies`
groups by Feature slug because it can't make that same assumption: Design Feature Instructions §9.2's own
invalidation walk is project-wide, so a design task can genuinely ripple into a different Feature's already-shipped
behaviour.

## 6 Not A Dependency Graph

Chunk Scope says *what* is now required; it says nothing about *how the behaviours it lists relate to each
other*. Chunking's own first job is understanding the functional interdependencies between the behaviours in
scope — for that it reads the specific call trees already recorded in `reconciliation.yaml`
([Specific Behaviors §2](specific-behaviors.md)), not anything precomputed here. No information is lost by Chunk
Scope not carrying sequencing data: that discovery hasn't happened yet at the point Chunk Scope is written, so
there is nothing yet to carry.

# Rationale

**Why Chunk Scope is a separate artefact from `service-flows.md`, not the same list.** `service-flows.md` is
Feature-level and cumulative — every Service ever brought into the flow, across however many design tasks it
took. Chunk Scope is bounded to one design task's own contribution. Conflating them would mean `Chunk The Design`
either has to re-derive which part of the Feature's whole design history is actually new since the last time it
ran, or re-chunk the entire Feature from scratch every time.

**Why Chunk Scope records behaviours, not operations or Services.** An operation's own behaviour file can hold
several behaviours with different fates in the same design task — some new, some mutated, some untouched.
Recording at operation granularity would either force every behaviour under a touched operation into the same
status regardless of whether it actually changed, or require a second, finer-grained list anyway. Behaviours are
already the unit `Chunk The Design` builds tests from — recording status at that same granularity means no
translation step between what Chunk Scope says and what chunking actually needs.

**Why `deleted` behaviours are Chunk Scope's concern, not just `mutated`'s.** A deleted behaviour means an
existing, currently-passing test needs retiring — the same "touching something that already passes" property
that puts `mutated` on the quick pathway rather than the ordinary one. Folding it into `mutated` would blur a
real distinction chunking needs.

**Why entries are appended as each status is decided, not computed by diffing at the end.** That information is
never actually missing at any point during the work: §7 already knows a behaviour is new the moment it predicts
one for the first time, and §9.2 already knows whether a `//REDESIGN_REQUIRED` resolution was accept or remove
the moment the architect decides it. Recording the fact when it's already known, rather than reconstructing it
later from a diff, is both simpler and removes an entire class of bug.

**Why `delivers` and `modifies` are optional, not required.** Chunk Scope's one job is to bound *what* a design
task requires, which the `behaviors` list alone already does completely. The rest exist because they're
convenient context a reader or a future tool might want, not because chunking depends on them.
