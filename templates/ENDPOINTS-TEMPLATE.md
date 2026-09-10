# Endpoints Template

## Context
* [Design Layout Standards §5.7](../standards/design-layout-standards.md) - what counts as an endpoint, the notation per kind, and when this splits
* [System Interface Template](SYSTEM-INTERFACE-TEMPLATE.md) - the host-facing side, which this document does not cover
* [Operation Template](OPERATION-TEMPLATE.md) - the operations these endpoints expose

Template for `ENDPOINTS.md`: the network endpoints — how data flows in and out of the service under BAU
conditions, joining it to the other services in the service flow. When the list becomes unwieldy it splits into
an `ENDPOINTS/` directory: `ENDPOINTS.md` keeps the per-vector assessment and points at the groups, with a
document of this shape per interface beside it.

**An endpoint is not an operation.** An operation is the abstract invocable point and owns the behaviours; an
endpoint is a protocol-bound exposure of one. The same operation exposed over REST and gRPC is two endpoints and
one set of behaviours — which is the whole reason they are separate, since adding a protocol must not duplicate
a condition space or re-derive a behaviour.

**A call the service makes to another service is not an endpoint.** It is a thin shim on a dependency boundary,
and the contract being consumed belongs to the other side — referenced as an external document with a checksum,
never restated here. A topic the service *publishes to* is different: consumers subscribe to it and nobody else
owns the schema, so it belongs here.

Notation follows the endpoint kind rather than one house format:

| Endpoint kind | Notation |
| :--- | :--- |
| request-response, webhook | OpenAPI |
| server-sent events | OpenAPI — a `text/event-stream` response |
| message-consumer, message-producer | AsyncAPI |
| WebSocket | AsyncAPI — its WebSocket binding |
| gRPC, including streaming | the `.proto` referenced, never restated |
| in-process API | the binding only — the contract is the operation signature |

A Library has no endpoints. This document does not exist on one.

# Appendix

````
---
_claims:
  - _target: bnd/{design-slug}/ep/{endpoint-slug}
    _sourcing: {kind: elicited, basis: "§1.1"}
    slug: {endpoint-slug}
    vector: network-and-invocation
    kind: {request-response | streaming | message-consumer | message-producer | webhook | in-process-api}
    protocol: "{REST | gRPC | Kafka | ...}"
    address: "{POST /v1/{resource}}"
    direction: {inbound | outbound | bidirectional}
    exposes: bnd/{design-slug}/op/{operation-slug}
  - _target: bnd/{design-slug}
    _sourcing: {kind: elicited, basis: "§3"}
    perimeter:
      - {vector: network-and-invocation, declared: true}
---
# {Design Name} Endpoints

## Context
* [{the design entry document}]({design-slug}.md)
* [System Interface](SYSTEM-INTERFACE.md) - how this service interacts with its host, which this document does not cover

## 1 Request/Response

{synchronous invocation, described in OpenAPI. One subsection per endpoint.}

### 1.1 {endpoint-slug}

| | |
| :--- | :--- |
| Exposes | [`{operation-slug}`](operations/{operation-slug}/OPERATION.md) |
| Protocol | REST |
| Address | `POST /v1/{resource}` |
| Direction | inbound |

```yaml
openapi: 3.1.0
paths:
  /v1/{resource}:
    post:
      operationId: {operationName}
      requestBody:
        content:
          application/json:
            schema:
              $ref: '#/components/schemas/{TypeName}'
      responses:
        '201':
          description: {what a successful call returns}
```

{The schema names the same types the data model defines. Where the two disagree, the data model is what the
condition space is derived from.}

## 2 Messaging

{event-driven flow, described in AsyncAPI. A topic this service publishes to is part of its published contract;
a topic it consumes from is how data reaches it.}

### 2.1 {endpoint-slug}

| | |
| :--- | :--- |
| Exposes | [`{operation-slug}`](operations/{operation-slug}/OPERATION.md) |
| Protocol | Kafka |
| Address | `{topic-name}` |
| Direction | {inbound for a consumer, outbound for a producer} |

```yaml
asyncapi: 3.0.0
channels:
  {channel-name}:
    address: {topic-name}
    messages:
      {message-name}:
        payload:
          $ref: '#/components/schemas/{TypeName}'
operations:
  {operation-name}:
    action: {send | receive}
    channel:
      $ref: '#/channels/{channel-name}'
```

## 3 Other

{streaming and in-process APIs, where neither standard fits — described in prose with the endpoint's own
frontmatter carrying the structured facts}

### 3.1 {endpoint-slug}

| | |
| :--- | :--- |
| Exposes | [`{operation-slug}`](operations/{operation-slug}/OPERATION.md) |
| Kind | {streaming \| in-process-api} |
| Protocol | {protocol} |
| Address | {address} |

{what crosses here, and in what shape}
````

# Rationale

Notation follows the endpoint kind because one house format across every kind would mean describing a topic as
though it were a route. OpenAPI describes a server's offered HTTP surface and has no vocabulary for
publish/subscribe; AsyncAPI has exactly that vocabulary and is the mature standard for it, under Linux
Foundation governance with 3.0 having resolved the publish/subscribe ambiguity that made 2.x awkward. Requiring
each where it fits is the same move the model already makes in requiring OpenSLO rather than inventing an SLI
notation — and it leaves each kind described in something existing tooling can actually read.

The specification version is pinned in each block rather than declared once for the document, because "valid
OpenAPI" and "valid AsyncAPI" are not questions with answers unless they name a revision. A mixed set is
legitimate while a migration is in progress.

Each endpoint carries an explicit link to the operation it exposes rather than relying on adjacency, because one
operation may be exposed several times over. The behaviours belong to the operation, and an endpoint that looked
like it owned them would invite a second condition space per protocol.
