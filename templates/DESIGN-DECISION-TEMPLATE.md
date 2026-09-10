# Design Decision Template

## Context
* [Design Layout Standards §3, §5.10](../standards/design-layout-standards.md) - the concern subdirectories, and why this is a register rather than a log

Template for a document under `DESIGN-DECISIONS/{concern}/{decision-slug}.md`: one decision, the question it
closed, and the options considered. Concern subdirectories are created only when a decision is taken under
them — interface and manifest, data model, boundaries, NFRs and observability are the expected concerns, not
the permitted ones.

**This is a register of the design's current state, not a history.** A superseded decision is retained and
marked superseded, because "this was considered and replaced" is still true of the present; the elements it
produced are deleted rather than left standing beside their replacement. A change request against another design
target is deleted outright once satisfied, since nothing about the present depends on it having been made.

**Every candidate is recorded, not just the chosen one.** A decision recording one option is indistinguishable
from a decision where only one option was ever thought of, and from one made first and justified afterwards. The
discarded candidates and their assessments are the evidence that the question was actually asked.

An **open design question** — a gap the design knows about and has not closed — may be recorded in any document.
Its resolution is recorded here.

# Appendix

A decision:

````
---
_claims:
  - _target: bnd/{design-slug}/kd/{decision-slug}
    _sourcing: {kind: elicited, basis: "§1"}
    slug: {decision-slug}
    question: §1
    decidedBy: {who}
    decidedAt: {YYYY-MM-DD}
    status: {current | superseded}
    produces:
      - bnd/{design-slug}/bnd/{boundary-slug}/fn/{function-slug}
    supersedes:
      - bnd/{design-slug}/kd/{other-decision-slug}
---
# {Decision Name}

## Context
* [{the design entry document}](../../{design-slug}.md)
* {the open design question this closes, where it was recorded elsewhere}

## 1 The Question

{the gap being closed, in a sentence or two — what the design needed an answer to and did not have}

## 2 Candidates

### 2.1 {candidate-slug} — chosen

{what this option is, and why it was chosen}

### 2.2 {candidate-slug}

{what this option is}

**Discounted because** {what ruled it out}

| Assessment | Means |
| :--- | :--- |
| `analysis` | assessed against the design's NFRs and the gap's own requirements, and scored worse |
| `logical-argument` | ruled out by an argument that it cannot work |
| `poc-finding` | ruled out by something a proof of concept demonstrated |

{A proof of concept records its finding here and needs no permanent artefact of its own.}

## 3 What This Produced

{the elements this decision brought into existence — every one of them traces back here}

* `bnd/{boundary-slug}/fn/{function-slug}` — {one line}

## 4 Supersedes

{present only where this decision consolidates or replaces others. They become superseded and are retained; the
elements they produced are removed rather than left alongside their replacement.}

* [{other-decision-slug}]({other-decision-slug}.md) — {what it decided, and why this replaces it}
````

An open design question, recorded wherever it was raised:

````
_claims:
  - _target: bnd/{design-slug}/odq/{question-slug}
    _sourcing: {kind: elicited, basis: "§N"}
    slug: {question-slug}
    subject: bnd/{design-slug}/bnd/{boundary-slug}
    question: §N
    raisedBy: {the architect, an agent, or the finding that surfaced it}
    blocks: {M0 | M1 | M2 | M3 | M4 | M5}
    closedBy: bnd/{design-slug}/kd/{decision-slug}
````

A change request against another design target:

````
_claims:
  - _target: bnd/{design-slug}/cr/{request-slug}
    _sourcing: {kind: elicited, basis: "§N"}
    slug: {request-slug}
    subject: bnd/{other-design-slug}/op/{operation-slug}
    change: §N
    ticket: "{the ticket raised so the owning design can do the work}"
    blocks: {M0 | M1 | M2 | M3 | M4 | M5}
````

# Rationale

Each candidate gets its own subsection rather than a row in a table because a candidate carries an assessment
that is genuinely prose — an argument, or what a proof of concept showed — and because candidates are added one
at a time as options are considered. A table would mean every claim about the decision is re-judged whenever one
option's assessment is written up.

A question may be raised against the model itself rather than against any element, which is why `subject` is
optional. Such a question typically blocks nothing yet, and recording it is exactly what makes it a gap somebody
knows about rather than one nobody has noticed.

A change request is shown alongside the decision template but is deliberately not a decision. Both record
something the design needs and does not have, and they differ in who may close them: a question is answered by a
decision this design is entitled to make, while a change request waits on another design target exercising
authority this one does not have. Collapsing them would make "blocked on someone else" indistinguishable from
"we have not decided yet", and only the second is actionable here.
