# System Interface Template

## Context
* [Design Layout Standards §5.6](../standards/design-layout-standards.md) - what the system interface covers, and why it exists even when mostly exemptions
* [Endpoints Template](ENDPOINTS-TEMPLATE.md) - the consumer-facing side, which this document does not cover

Template for `SYSTEM-INTERFACE.md`: how the service interacts with the system hosting it, as distinct from how a
consumer interacts with the service. Configuration injected into it, signals it handles, diagnostics it emits,
and what it implicitly takes from its host.

**Each area is declared or explicitly exempted, with a reason where exempted.** An area nobody examined and a
genuinely empty one are indistinguishable if silence is permitted, so this document exists even when most of it
is exemptions. That is the whole value of a closed list: being able to assert that every area was considered.

The host-and-kernel area is the one most likely to be forgotten and most likely to cause the failures that are
hardest to reproduce. What it declares are implicit dependencies, and each becomes a **dependency-state
dimension** in the condition spaces of the operations that read it — a boundary reading the clock has
clock-dependent behaviour whether or not anyone modelled it, and declaring the dependency is what brings it into
the condition space where it can be covered.

A Library has no process and therefore no system interface. This document does not exist on one.

# Appendix

````
---
_claims:
  - _target: bnd/{design-slug}/ep/{endpoint-slug}
    _sourcing: {kind: elicited, basis: "§1.1"}
    slug: {endpoint-slug}
    vector: configuration-and-environment
    kind: {environment-variable | cli-argument | mounted-file | remote-config}
    protocol: "{environment variable}"
    address: "{VARIABLE_NAME}"
    direction: inbound
  - _target: bnd/{design-slug}/ep/{endpoint-slug}
    _sourcing: {kind: elicited, basis: "§2.1"}
    slug: {endpoint-slug}
    vector: system-signals
    kind: {process-signal | hardware-interrupt | exit-code}
    protocol: "POSIX signal"
    address: "SIGTERM"
    direction: inbound
  - _target: bnd/{design-slug}
    _sourcing: {kind: elicited, basis: "§4"}
    perimeter:
      - {vector: host-and-kernel, exempt: true, exemptionReason: "{why}"}
---
# {Design Name} System Interface

## Context
* [{the design entry document}]({design-slug}.md)
* [Endpoints](ENDPOINTS.md) - how consumers reach this service, which this document does not cover

## 1 Configuration And Environment

{the operational context injected at startup or runtime: environment variables, CLI arguments, mounted files,
remote configuration. Each is a point on the perimeter with a contract and a required behaviour — a missing
startup credential is a designed-for case, not an accident.}

### 1.1 {endpoint-slug}

| | |
| :--- | :--- |
| Kind | environment-variable |
| Address | `{VARIABLE_NAME}` |
| Required | {yes, or the default when absent} |
| Purpose | {what the service does with it} |
| Behaviour when absent or invalid | {what happens, which is a required effect somewhere} |

## 2 System Signals

{host and orchestrator control of the process lifecycle: process signals, hardware interrupts, exit codes. This
is where graceful shutdown is designed for rather than assumed.}

### 2.1 {endpoint-slug}

| | |
| :--- | :--- |
| Kind | process-signal |
| Address | `SIGTERM` |
| Purpose | {what the service does on receiving it} |
| Behaviour | {the shutdown sequence, and what it guarantees about in-flight work} |

## 3 Streams And Telemetry

{outbound diagnostics: stdout, stderr, metrics scrape, health probe, trace export. What is emitted, in what
form, and where anything reads it from.}

### 3.1 {endpoint-slug}

| | |
| :--- | :--- |
| Kind | {stdout \| stderr \| metrics-scrape \| health-probe \| trace-export} |
| Address | {the path, port or stream} |
| Purpose | {what it carries} |

## 4 Host And Kernel

{implicit inputs from the host: system clock, shared memory, filesystem paths, file descriptors. Each declared
here becomes a dependency-state dimension in the condition spaces of the operations that read it.}

| Dependency | What is read | Becomes a dimension of |
| :--- | :--- | :--- |
| system-clock | {what the service does with the time} | {the operations that read it} |

## 5 Assessment

{one row per area, so that "considered and found nothing" is on the record rather than looking like "nobody
looked"}

| Area | Assessment |
| :--- | :--- |
| Configuration and environment | declared — §1 |
| System signals | declared — §2 |
| Streams and telemetry | declared — §3 |
| Host and kernel | **exempt**: {why nothing here reads implicit host state} |
````

# Rationale

The areas are enumerated rather than described, and each endpoint gets its own subsection. "What are this
service's inputs?" is an open question and gets an incomplete answer; "does it take CLI arguments, does it read
mounted files, does it handle signals?" is a finite walk with an answer per item. A subsection per endpoint then
keeps a change to one from re-judging the claims about the rest.

§5 restates as a table what §1 to §4 already show, and earns its place by making the *negative* answer visible.
The rest of the document records what exists; only this section can record that an area was examined and found
empty, which is the difference between a closed list that proves something and one that proves nothing.

Host and kernel is modelled despite exposing nothing anyone invokes, because a perimeter that only covered the
areas with visible endpoints would systematically exclude the sources of non-determinism — clock drift, file
descriptor exhaustion, shared memory contention — which are exactly the conditions a design most needs to have
covered.
