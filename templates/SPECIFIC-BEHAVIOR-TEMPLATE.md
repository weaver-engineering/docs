# Specific Behavior Template

## Context
* [Documentation Standards](../standards/documentation-standards.md) - the document shape this template departs
  from — this artefact is data, not a narrative document, see below
* [Specific Behaviors](../workflows/feature-workflow/specific-behaviors.md) - what a Predicted Service Behaviour
  is, and why it has no separate prose document of its own
* [Design Feature Instructions §6-§9](../workflows/feature-workflow/design-feature-instructions.md) - the process
  that produces and checks the shape below

Template for `reconciliation.yaml`, one file per design task, filed at
`docs/design/{feature-slug}/{design-task-ref}/reconciliation.yaml` — sibling to `hld.md` and `chunk-scope.yaml`
(Design Directory And HLD §1). Unlike every other template in this repo, this one has no narrative body at all:
it's the mechanical record of Design's own binding, and the Required Service Behaviour it's reconciled against
already carries every piece of human-readable prose (Required Behavior Template). The template itself is in the
Appendix below.

Each entry, keyed by the behaviour's own address (`{service-slug}.{operation-slug}-N`), passes through the same
lifecycle a Specific Behavior document's own leaf used to carry as prose markers — now expressed structurally,
since there's no body text left for a marker to live in:

* **Not yet predicted** — no entry exists for this address at all. Design Feature Instructions §6/§7 haven't
  reached it yet.
* **`//REVIEW`** — `call_tree` is present, `reviewed` is `null`: bound and traced, awaiting confirmation, either
  §7's first derivation or a fresh regeneration after `//REDESIGN_REQUIRED` resolved.
* **Approved** — `reviewed` carries `{reviewed_by, reviewed_at}`.
* **`//REDESIGN_REQUIRED`** — `redesign_required` carries `{found_at, disconnect}`: approved once, but a
  regeneration under the current design no longer matches what's recorded. Always resolves back to `//REVIEW`
  (`reviewed` cleared, `redesign_required` cleared), never straight back to approved.

# Appendix

```yaml
behaviors:
  "payments.charge-card-1":
    bound_pseudocode: |
      # this behaviour's own Required Effect, with each abstract call substituted for the real address that
      # satisfies it (Pseudocode Style §2) — required the moment binding (Design Feature Instructions §6) reaches
      # this behaviour
      FUNCTION payments.interface.charge-card:
        token <-- [payments.stripe-gateway.tokenize - card]
        result <-- [payments.stripe-gateway.charge - token, amount]
        RETURN result
    call_tree:  # required the moment this behaviour leaves "not yet predicted" (§7) — every leaf carries an
                # inline comment naming the concrete interaction, since there's no prose Then to state it instead
      address: "payments.interface.charge-card"
      children:
        - address: "payments.stripe-gateway.tokenize"  # tokenize(card-4242) -> tok_abc
        - address: "payments.stripe-gateway.charge"    # charge(tok_abc, 4200) -> ch_123, succeeded
    required_service_behaviour_checksum: null  # set once Design Feature Instructions §9.1 first passes
    function_checksums: {}  # "{address}": "{checksum}", one per function/prose the bound pseudocode reaches
    reviewed: null  # {reviewed_by, reviewed_at} once §9.2 confirms; removed entirely (not blanked) the moment a
                     # function checksum change invalidates it
    redesign_required: null  # {found_at, disconnect} once §9.2 finds a regenerated mismatch against a prior
                               # approval; always resolves back to reviewed: null (//REVIEW), never straight back
                               # to approved
  "payments.charge-card-1.1":
    bound_pseudocode: "..."
    call_tree: {...}
    required_service_behaviour_checksum: null
    function_checksums: {}
    reviewed: null
    redesign_required: null
```

# Rationale

**Why this template has no narrative body, unlike every other document produced by this process.** A Specific
Behavior document used to carry Given/When/Then prose alongside its own frontmatter reconciliation block — but
that prose is now the Required Service Behaviour's own job (Required Behavior Template), stated once, independent
of and reconciled against whatever this file predicts. Keeping a second, near-duplicate prose statement here
would reintroduce exactly the drift risk splitting Required and Predicted Service Behaviour into independent
artifacts was meant to prevent (Weaver Engineering Workflows §4).

**Why `required_service_behaviour_checksum` is singular, unlike the old `uc_technical_interpretation_checksums`
map.** The old shape let one Specific Behavior document serve more than one use case, so it needed a checksum per
relying use case. A Predicted Service Behaviour binds exactly one Required Service Behaviour — its own identity
*is* that behaviour's address — so there's exactly one checksum to track.

**Why `reconciliation.yaml` is keyed by full address, not a document-local id.** The old per-document frontmatter
could get away with local ids (`"1.1"`) because the document itself supplied the rest of the address. This file
covers every behaviour a whole design task predicts, potentially across several operations and even several
Services if the task's own scope spans more than one — a local id has nothing to be local *to* anymore.

**Why three of the four lifecycle states are structural fields rather than prose markers, unlike the process this
template's predecessor followed.** `//TODO`, `//REVIEW`, and `//REDESIGN_REQUIRED` used to be human-visible,
machine-searchable prose precisely because they lived inside a document a human was already reading start to
end. This file is never read that way — it's consulted by address, by tooling, the same way `chunk-scope.yaml`
already is — so the same information expressed as presence/absence of a field is exactly as searchable
(`redesign_required != null`) without needing prose markers nobody reads inline.
