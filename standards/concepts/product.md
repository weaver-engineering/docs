# Product

## Context
* [The Product/Service Model](../product-service-model.md) - where Product sits in the wider continuum
* [Platform](platform.md) - the specialization of Product whose customers are other projects' own SDEs
* [Weaver Engineering Projects §1](../../projects/weaver-projects.md) - the normative "project = Product" rule this document backs
* [Documentation Standards §2](../documentation-standards.md) - `PRODUCT.md`'s location and shape
* [Glossary](../../glossary.md) - one-line definition and pointer back to this document

## 1 Definition

A Product is one Weaver Engineering project: one code repository plus one `<project>-docs` repository, recorded
in that repo's own `PRODUCT.md`.

## 2 Decomposing Into Services Doesn't Multiply Products

A Product that decomposes into several Services does not become several Products. Service is a subdivision
within the Product's own docs repo (`docs/services/`), not a unit that earns its own code-and-docs repo pair —
the one-project-one-repo-pair convention tracks Product, never Service. See [Weaver Engineering Projects
§1](../../projects/weaver-projects.md) for the full reasoning.
