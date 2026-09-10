# Manifest Template

## Context
* [Design Layout Standards §5.5](../standards/design-layout-standards.md) - what the manifest holds, and the inherit/tighten/exempt rule
* [System Interface Template](SYSTEM-INTERFACE-TEMPLATE.md) - the host-facing side of a Service, which this document does not cover

Template for `MANIFEST.md`: what this design is built as, and how a consumer gets at it. Every design target has
one, Library and Service alike — a Library is built, packaged, published and consumed, and its distribution is
its whole consumption story.

**Every setting is stated, inherited or exempted.** There is no fourth state, and a setting that is none of
those is a finding. Exemption is a real answer and often the right one — a Library has no container build
stages, a single-package repository has no module structure worth stating — but it is an answer, recorded,
rather than a blank.

A contained design target **inherits** its container's manifest per setting. It may **tighten** what it inherits
— narrowing an approved dependency matrix, banning something more — and may never loosen it; a contradiction is
a finding rather than a local override. An uncontained design target has nothing to inherit from and declares
its own, which is the standalone Library case and the reason the manifest sits at this level at all.

The runtime settings in §2 apply to a Service only. None of them has a referent on a Library: no image to base,
no paradigm to execute under, nothing to wire to infrastructure, and no health to probe.

# Appendix

````
---
_claims:
  - _target: bnd/{design-slug}
    _sourcing: {kind: elicited, basis: "§1"}
    buildManifest:
      - {kind: language, value: "{language}"}
      - {kind: language-version, value: "{version}"}
      - {kind: build-tool, value: "{tool}"}
      - {kind: package-manager, value: "{manager}"}
      - {kind: architecture-pattern, value: "{pattern}"}
      - {kind: directory-layout, value: "{layout}"}
      - {kind: module-structure, exempt: true, exemptionReason: "{why}"}
      - {kind: repository-path, value: "{path}"}
      - {kind: approved-dependencies, value: ["{dependency}"]}
      - {kind: banned-dependencies, value: ["{dependency}"]}
      - {kind: style-and-lint-rules, value: ["{rule}"]}
      - {kind: test-runner, value: "{runner}"}
      - {kind: assertion-library, value: "{library}"}
      - {kind: mocking-tooling, value: "{tooling}"}
      - {kind: artifact-form, value: "{published-package | in-process-import | source-dependency | container-image}"}
      - {kind: registry, value: "{registry}"}
      - {kind: coordinates, value: "{coordinates}"}
  - _target: bnd/{design-slug}
    _sourcing: {kind: elicited, basis: "§2"}
    runtimeManifest:
      - {kind: runtime-version, value: "{version}"}
      - {kind: base-image, value: "{image}"}
      - {kind: execution-paradigm, value: "{long-running-server | ephemeral-worker | event-consumer | scheduled-job | wasm-module}"}
      - {kind: core-framework, value: ["{framework}"]}
      - {kind: persistence-driver, value: ["{driver}"]}
      - {kind: config-mechanism, value: ["{mechanism}"]}
      - {kind: observability-sdk, value: ["{sdk}"]}
      - {kind: build-output-target, value: "{target}"}
      - {kind: container-build-stages, value: "{stages}"}
      - {kind: healthcheck-command, value: "{command}"}
---
# {Design Name} Manifest

## Context
* [{the design entry document}]({design-slug}.md)
* {the containing design target this inherits from, where there is one}

## 1 Build

{what this codebase is written in, built with, constrained to, tested by, and how a consumer obtains it}

### 1.1 Language And Build System

| Setting | Value | Source |
| :--- | :--- | :--- |
| language | {value} | stated |
| language-version | {value} | inherited from {containing design target} |
| build-tool | {value} | stated |
| package-manager | {value} | stated |

### 1.2 Topology

| Setting | Value | Source |
| :--- | :--- | :--- |
| architecture-pattern | {value} | stated |
| directory-layout | {value} | stated |
| module-structure | — | **exempt**: {why} |
| repository-path | {value} | stated |

### 1.3 Dependency Constraints

{what a function is permitted to call. A contained design target may narrow these and may not widen them.}

| Setting | Value | Source |
| :--- | :--- | :--- |
| approved-dependencies | {list} | inherited, tightened — {what was removed, and why} |
| banned-dependencies | {list} | stated |
| style-and-lint-rules | {list} | inherited |

### 1.4 Test Stack

| Setting | Value | Source |
| :--- | :--- | :--- |
| test-runner | {value} | stated |
| assertion-library | {value} | stated |
| mocking-tooling | {value} | stated |

### 1.5 Distribution

{the access vector: how a consumer gets at this. For a Library this is the whole consumption story; for a
Service it coexists with the endpoints, which say how a running process is reached rather than how the artifact
is obtained.}

| Setting | Value | Source |
| :--- | :--- | :--- |
| artifact-form | {value} | stated |
| registry | {value} | stated |
| coordinates | {value} | stated |

## 2 Runtime

{a Service only — none of these has a referent on a Library}

### 2.1 Runtime And Execution

| Setting | Value | Source |
| :--- | :--- | :--- |
| runtime-version | {value} | stated |
| base-image | {value} | stated |
| execution-paradigm | {value} | stated |

### 2.2 Frameworks, Wiring And Scaffold

| Setting | Value | Source |
| :--- | :--- | :--- |
| core-framework | {list} | stated |
| persistence-driver | {list} | stated |
| config-mechanism | {list} | stated |
| observability-sdk | {list} | stated |
| build-output-target | {value} | stated |
| container-build-stages | — | **exempt**: {why} |
| healthcheck-command | {value} | stated |
````

# Rationale

The settings are enumerated rather than the dimensions described. An earlier shape gave each dimension a prose
description — "architecture and directory pattern, module or workspace structure, path relative to the
repository root" — which bundles several separate facts into one cell and asks for them as a single open
question. Prose invites recognition, and what comes back is whatever the architect already had in mind. Naming
each setting makes elicitation a finite walk with an answer required per item, and makes a missing answer
detectable rather than merely absent.

The `Source` column is present on every table because the difference between stated, inherited and exempt is
what makes the manifest checkable at all. A value with no source is indistinguishable from one somebody typed in
without deciding anything, and an inherited value that has quietly been loosened is exactly the contradiction the
inheritance rule exists to catch.

Build and runtime are separate sections rather than one list because the split is what lets a Library have a
complete manifest. Everything about producing an artifact belongs to anything that is designed; only what
presupposes a running process belongs to deployment.
