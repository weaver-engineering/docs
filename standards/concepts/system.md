# System

## Context
* [The Product/Service Model](../product-service-model.md) - where System sits in the wider continuum
* [Service](service.md) - what a System runs
* [Documentation Standards §2](../documentation-standards.md) - `docs/infrastructure/`'s location
* [Glossary](../../glossary.md) - one-line definition and pointer back to this document

## 1 Definition

The System layer is the compute, network, and datastore infrastructure a Service actually runs on — how a
Service is deployed, not what it does. Documented under `docs/infrastructure/`, one level below `docs/services/`
since it's an operational concern about a Service, not part of the Service's own interface or components.
