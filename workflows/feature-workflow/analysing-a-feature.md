# Analysing A Feature

## Context
* [Initial Feature Document](initial-feature-document.md) - what a Feature is: the capabilities it groups, existing
  before design or Service decomposition
* [Use Cases](use-cases.md) - the full mechanical shape (actor, goal, steps, extensions) a use case takes once one
  exists
* [User Personas](user-personas.md) - the goal/frustration pair a use case's human actor is formalized as
* [Required Behaviors](required-behaviors.md) - the mechanical derivation both routes described here feed into
* [Feature Workflow §1](feature-workflow.md) - the `Analyse Feature` step this document guides

## 1 Capability: What A Feature Actually Groups

A Feature is a logical grouping of **capabilities** — things a customer can do through the product. A toaster
offering the Feature `toasts-bread` groups the capabilities `insert bread`, `remove toast`, `set timer`, and `start
toasting`; a second Feature, `crumb-tray`, groups `eject crumb tray` and `insert crumb tray`. A capability, not a
use case, is what a Feature groups — see [Initial Feature Document](initial-feature-document.md)'s own Rationale
for why this replaces an earlier, wrong definition.

A capability is real and designable the moment it's named, independent of anything else in this document — see §3.

## 2 A Persona's Use Case Is What Turns A Capability Into A Benefit

A [User Persona](user-personas.md) states a Role, one or more Goals, one or more Frustrations, and its Technical
Proficiency. A use case is a sequence of
**operations** a persona performs, some of which invoke a Feature's own capability, to reach that goal and relieve
that frustration (Use Cases §1). Only in relation to a persona's use case does a capability become a **benefit** —
a capability with no exercising use case is still valid, but its benefit, and to whom, is unproven.

The toaster's `crumb-tray` capability is not a benefit for the `cook` persona (goal: cook food; frustrated by
monitoring toast) — nothing in `make-toast` ever touches it. It only becomes a benefit once a second persona,
`cleaner` (goal: clean the kitchen; frustrated by the toaster dropping crumbs when moved), and their use case
`throw-away-crumbs`, actually exercise it:

```
make-toast (persona: cook)
  1. cook uses toaster - insert bread
  2. cook uses toaster - set timer
  3. cook uses toaster - start toasting
  4. cook waits for toaster to finish
  5. cook uses toaster - remove toast

throw-away-crumbs (persona: cleaner)
  1. cleaner uses toaster - eject crumb tray
  2. cleaner cleans crumb tray
  3. cleaner uses toaster - insert crumb tray
```

Several use cases may invoke the same capability; a capability invoked by no use case yet is not thereby invalid —
see §3.

## 3 A Feature Doesn't Require A Use Case To Be Designed

There is no requirement to have a use case to design a Feature. A capability's own effect can be derived directly
from the Feature that groups it, with no use case involved at all (§4, Route 1) — doing so may be of questionable
benefit (§2), but the capability is still valid and fully designable.

What a use case adds, when one exists, is precision, not existence. A Feature `sorting-and-filtering` is, without
any use case, satisfied by *any* sort/filter behaviour — nothing narrows which fields, which order, or which
values matter. The use case `prepare-purchase-order` (persona: sales representative; goal: find the cheapest large
widget) narrows this to a specific requirement: filter to large widgets, sort by price descending. The capability
didn't change; what's required of it got sharper.

## 4 Deriving Required Behaviors: Two Routes

A capability's Required Behavior ([Required Behaviors](required-behaviors.md)) can be derived one of two ways:

1. **Directly from the Feature.** The capability's own effect is derived from its Feature-level definition, with no
   use case involved. This is the generic, unconstrained behaviour §3 describes — always available, since a
   capability exists independent of any use case.
2. **Via a use case's operation.** A use case's step that invokes the product is an **operation**, distinct from a
   capability (§4.1). An operation either:
   * **defers to an existing capability** — reusing (and, per §3, narrowing) that capability's own definition to
     what this specific use case actually needs; or
   * **defines itself inline** — its own effect, written in the same style a capability's would be, when no
     capability yet covers what this step needs.

   Both forms are written in the same shape, so both feed the same mechanical derivation (Required Behaviors §2)
   regardless of whether the specification they read lives at the Feature level or is local to one use case.

### 4.1 Operation And Capability Are Distinct

An operation is a use case's own step; a capability is a Feature's own catalogued, reusable unit. Not every
operation in a use case corresponds to a capability at all — in `throw-away-crumbs` above, step 2, "cleaner cleans
crumb tray," is a real operation with a real effect (the toaster's crumb count returns to zero — a change to an
**External Dependency**, the toaster's own crumb-holding state, legitimate only under a precondition the product's
own capabilities established: the tray must already be ejected) but there is no toaster capability for it. Nothing
performs it systematically — a human does it directly — so no Required Behavior derives from it at all: Required
Behavior derivation, by either route in §4, only ever applies to an operation something systematic actually
performs.

This cuts the other way too: an operation can be genuinely systematic and still not correspond to any documented
capability, simply because no Feature has catalogued it yet. Such an operation isn't exempt the way `clean crumb
tray` is — it still derives a Required Behavior, just via the inline route (§4), never the direct-from-Feature
route, until (if ever) it's promoted into a capability of its own.

# Rationale

**Why capability replaces "collection of use cases" as what a Feature groups.** A Feature named and scoped before
any use case exists (Initial Feature Document §1) has to group *something* concrete even then — a use case can't be
that something, since a Feature with zero use cases would then have nothing to group at all, contradicting §3's
own claim that a Feature is fully designable without one. Capability is the thing that's actually there from the
moment a Feature is conceived: what the product lets a customer do, independent of who (if anyone, yet) wants to do
it and why.

**Why benefit is stated as relative to a persona, never as a property of a Feature by itself.** Judging a Feature
"worth building" by its own capabilities alone has no stopping point — anything a product technically could do
looks equally plausible in isolation. Requiring an actual persona's actual use case to exercise a capability before
calling it a benefit is what keeps "is this worth building" answerable, and prevents cutting a capability that is a
real benefit, just for a persona nobody had written down yet (`crumb-tray`, for `cleaner`, is exactly this case).

**Why Required Behavior needs two routes, not one.** A single via-use-case route would make §3 false: a Feature
could never be designed before a use case existed, since nothing would derive its capabilities' Required Behaviors
until one did. The inline half of Route 2 exists for the mirror reason: requiring every use case operation to
already have a matching capability would block a use case from specifying a one-off interaction that hasn't been
(or may never be) generalized into something reusable.

**Status.** This document is a first cut (WVR-180), written before the Doc Search & Reporting Feature (WVR-179) has
actually been analysed against it. Expect revision as that analysis exercises the model for real — in particular,
[Required Behaviors](required-behaviors.md) itself still describes only the via-use-case derivation (its own §2);
reconciling the direct-from-Feature route into its mechanics (filing location, checksum source) is open, flagged
there as `//TODO (WVR-180)`.
