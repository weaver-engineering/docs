# Chunk Scope

## Context
* [Feature Workflow](feature-workflow.md) §3 - the `Chunk The Design` step this artefact is the entry-scope for
* [Design Directory And HLD](design-directory-and-hld.md) §2 - the HLD's own §1 Scope, whose Design Task bullets this artefact is pegged to
* [Design Feature Instructions](design-feature-instructions.md) §7.2 - the process that decides a behavior is new/mutated/deleted, and when a design task's own work is actually complete
* [Specific Behaviors](specific-behaviors.md) - the `SB-NNN §id` addressing this artefact's own behaviors list uses

A Feature comprises many use cases, not all designed and delivered together — a single Feature's HLD accumulates
Scope across however many separate design tickets it takes to cover them. Chunk Scope is the artefact that says,
for one specific design task, exactly which specific behaviors are now required of the system as a result of its
work — new ones it introduced, existing ones it changed, existing ones it removed — so that [Chunk The
Design](feature-workflow.md) has an unambiguous, bounded scope to work from, instead of the Feature's entire,
ever-growing design history.

## 1 Purpose

Chunk Scope plays no role in design itself. Nothing in [Design Feature Instructions](design-feature-instructions.md)
reads it, and it records no fact the design directory doesn't already carry elsewhere — it is a pure consequence
of a design task reaching completion, never an input to reaching that completion.

## 2 How It's Built

Not a single, end-of-task computation — each entry is recorded the moment its status is actually decided, which
already happens at three specific points in the process, never retroactively diffed or reconstructed afterward:

* Design Feature Instructions §5.2 derives a leaf for the first time → append `{address, status: new}`.
* §7.2's `//REDESIGN_REQUIRED` resolves as **accept** → append `{address, status: mutated}`.
* §7.2's `//REDESIGN_REQUIRED` resolves as **remove** → append `{address, status: deleted}`.

The file starts existing from the first `new` entry a design task produces, and keeps growing throughout the
task's own lifetime — the same incremental, resumable shape as everything else in this process (a `//TODO`
placeholder becoming `//REVIEW` becoming approved, one behavior at a time, never computed in one pass at the
end). It stops growing once the design task itself is complete: Design Feature Instructions §7.2 reaching
project-wide, fixed-point completion covering every use case the design task named.

## 3 Location, Naming, And The Completion Signal

`{design-task-ref}-chunk-scope.yaml`, filed at the root of the Feature's own design directory
(`docs/design/{feature-slug}/`, Design Directory And HLD §1) — alongside the HLD, not nested under
`specific-behaviors/`, since it's a summary artefact over that directory's content, not one more piece of it.

The file's own existence only means a design task has produced at least one behavior so far — it does not mean
the task is complete. Completion is a separate, later signal: the HLD's own §1 Scope (Design Directory And HLD
§2) links to a Design Task's chunk scope only once §7.2 has reached full, project-wide completion for it. A
Design Task bullet with no such link is still in progress, or was interrupted, regardless of how many entries
its own chunk-scope.yaml already holds — mechanically checkable the same way every other completion state in
this process is (Design Feature Instructions §1).

## 4 Required Shape

Exactly one field is required:

```yaml
behaviors:
  - address: "SB-014 §1"
    status: new       # new | mutated | deleted
  - address: "SB-014 §1.1"
    status: mutated
  - address: "SB-020 §2"
    status: deleted
```

Every specific behavior this design task's work touched, addressed the same way Specific Behaviors already
addresses one (`SB-NNN §id`), tagged with exactly one status:

* **`new`** — didn't exist before this design task; a wholly new test to write.
* **`mutated`** — existed and passed before, its expected result changed; an existing test to edit.
* **`deleted`** — existed and passed before, no longer applies under the evolved design at all (Design Feature
  Instructions §7.2's third `//REDESIGN_REQUIRED` resolution — removal); an existing test to retire.

`mutated` and `deleted` both mean touching a test that's already passing, not adding a new one — the ordinary
spec/test/build pathway is deliberately built to resist exactly that, so both need the quick pathway
([the chunk sequence](the-chunk-sequence.md)) instead of the ordinary one. `new` doesn't.

## 5 Optional Fields

Everything else is a convenience, not a requirement. [Chunk The Design](feature-workflow.md) has access to the
whole design directory, not just this one file, and may or may not use any of these:

```yaml
feature: {feature-slug}
hld: docs/design/{feature-slug}/{feature-slug}-hld.md
delivers: [UC-001, UC-002]         # use cases this design task actually targeted
modifies:                          # use cases whose behavior changed as a side effect, grouped by Feature
  {feature-slug-a}: [UC-041, UC-042]
  {feature-slug-b}: [UC-045]
```

`delivers` stays a flat list — a design task only ever targets use cases within its own one Feature. `modifies`
groups by Feature slug because it can't make that same assumption: Design Feature Instructions §7.2's own
invalidation walk is project-wide, so a design task can genuinely ripple into a different Feature's already-shipped
behavior. Use case numbers are unique project-wide, so the grouping is for readability, not disambiguation.

## 6 Not A Dependency Graph

Chunk Scope says *what* is now required; it says nothing about *how the behaviors it lists relate to each
other*. Chunking's own first job is understanding the functional interdependencies between the behaviors in
scope — for that it reads the specific call trees already recorded in the design directory it has full access
to ([Specific Behaviors §2.6](specific-behaviors.md)), not anything precomputed here. No information is lost by
Chunk Scope not carrying sequencing data: that discovery hasn't happened yet at the point Chunk Scope is
written, so there is nothing yet to carry.

# Rationale

**Why Chunk Scope is a separate artefact from the HLD's own Scope, not the same list.** The HLD's §1 Scope is
Feature-level and cumulative — every use case ever brought into design for this Feature, across however many
design tasks it took. Chunk Scope is bounded to one design task's own contribution. Conflating them would mean
`Chunk The Design` either has to re-derive which part of the Feature's whole design history is actually new
since the last time it ran, or re-chunk the entire Feature from scratch every time — both defeat the purpose of
scoping design work into separate tasks in the first place.

**Why Chunk Scope records behaviors, not use cases.** A use case can require several operations, and an
operation's own `SB-NNN` can hold several behaviors with different fates in the same design task — some new,
some mutated, some untouched by this particular round of work. Recording at use-case granularity would either
force every behavior under a touched use case into the same status regardless of whether it actually changed, or
require a second, finer-grained list anyway. Behaviors are already the unit `Chunk The Design` builds tests
from (Specific Behaviors §2.9) — recording status at that same granularity means no translation step between
what Chunk Scope says and what chunking actually needs.

**Why `deleted` behaviors are Chunk Scope's concern, not just `mutated`'s.** A deleted behavior means an
existing, currently-passing test needs retiring — the same "touching something that already passes" property
that puts `mutated` on the quick pathway rather than the ordinary one. Folding it into `mutated` would blur a
real distinction chunking needs: editing a test's expectations is not the same operation as removing it
entirely, even though both bypass the ordinary add-a-new-test gate for the same underlying reason.

**Why Chunk Scope carries no dependency or sequencing information.** Working through this shape surfaced that
nothing is actually lost by this omission: at the point Chunk Scope is written, nobody has yet asked how its
listed behaviors relate to each other — that discovery is `Chunk The Design`'s own first job, done by reading
the specific call trees already sitting in the design directory it has full access to. Precomputing it into
Chunk Scope would mean maintaining a second, derived copy of information the call trees already state directly,
with no guarantee the two stay in sync.

**Why entries are appended as each status is decided, not computed by diffing at the end.** An earlier version
of this document treated Chunk Scope as something computed once, in full, only after a design task's §7.2 work
finished — which would mean reconstructing, after the fact, which behaviors were new versus mutated versus
deleted by comparing against the Feature's prior chunk scopes. That information is never actually missing at any
point during the work: §5.2 already knows a behavior is new the moment it derives one for the first time, and
§7.2 already knows whether a `//REDESIGN_REQUIRED` resolution was accept or remove the moment the architect
decides it. Recording the fact when it's already known, rather than reconstructing it later from a diff, is
both simpler and removes an entire class of bug (the reconstruction disagreeing with what actually happened).

**Why `delivers` and `modifies` are optional, not required.** Chunk Scope's one job is to bound *what* a design
task requires, which the `behaviors` list alone already does completely — `Chunk The Design` can build every
test it needs from that field by itself. `delivers`/`modifies`/`feature`/`hld` exist because they're convenient
context a reader or a future tool might want, not because chunking depends on them; making them required would
imply a dependency that doesn't actually exist.
