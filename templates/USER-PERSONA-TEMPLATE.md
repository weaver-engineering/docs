# User Persona Template

## Context
* [User Personas](../workflows/feature-workflow/user-personas.md) - what a persona is, and why it's filed as a flat
  file, not directory-per-entity
* [Documentation Standards](../standards/documentation-standards.md) - the document shape (Context, numbered
  sections, Rationale) this template follows

Template for a single persona, filed as `docs/analysis/user-personas/{persona-slug}.md`, extracted from
AgentPlugins' own personas (`architect`, `architects-assistant`, `design-assistant`) rather than invented from
scratch. The template itself is in the Appendix below, since it's reference material to copy from, not indexed
content in its own right.

# Appendix

````
# The {Persona Name} Persona

{one or two sentences: who or what this persona is}

## Context
* {link to the root index for this repo}
* {other personas this one relates to — who it works on behalf of, what broader persona it specializes, if any}

## 1 Role

{what this persona actually does. State plainly whether it's the primary actor for the use cases it appears in, or
a supporting actor alongside someone else who remains primary — and who that primary actor is, if not this
persona}

## 2 Goals

{numbered list — what this persona is trying to achieve}

1. ...

## 3 Frustrations

{numbered list — what already goes wrong, or costs effort, on the way to those goals today. Concrete enough that a
capability can be checked against it: does it actually address this, or just sound related to a goal}

1. ...

## 4 Technical Proficiency

{how capable this persona is, and what that implies for how a use case can work with it — how much can be
delegated outright versus needs a human actively present, for instance}
````

# Rationale

The shape (Role, Goals, Frustrations, Technical Proficiency, in that order) is extracted from AgentPlugins' own
existing persona docs rather than designed from first principles — three personas (`architect`,
`architects-assistant`, `design-assistant`) had already converged on this exact structure independently of any
written standard, which is a stronger signal than a template author guessing what fields matter.

**Why Role and Technical Proficiency, beyond Goals and Frustrations.** Goals and Frustrations are what make a
capability checkable as a benefit (Analysing A Feature §2) — but AgentPlugins' own personas show two more facts
are load-bearing in practice: Role settles whether a use case is written from this persona's own point of view at
all (primary) or only touches it in passing (supporting), and Technical Proficiency settles how a use case can
actually work with this persona — an AI-agent persona's proficiency directly determines what can be delegated to
it wholesale versus what needs a human actively present, and the same question applies, just as concretely, to a
human end-user persona of differing skill levels.
