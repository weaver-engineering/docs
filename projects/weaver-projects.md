# Weaver Engineering Projects

## Context
* [About Weaver Projects](../about-weaver-engineering.md)
* [Documentation Standards](../standards/documentation-standards.md) - the `<project>-docs` repo structure (`PRODUCT.md`, the minimum directory set) this document's conventions build on

//TODO - Add documentation here about how Weaver Engineering Projects are maintained

## 1 Project Maps To Product

A Weaver Engineering "project" — one code repository plus one `<project>-docs` repository — is one Product, or a
Platform (a Product whose customers are other projects' own SDEs rather than end-users, e.g. `the-loom`). No
structural marker distinguishes the two; it's conceptual, stated in the project's own `PRODUCT.md`.

A Product that decomposes into more than one Service does **not** gain a second project. Service is a subdivision
within the project's own docs repo (`docs/services/`, [Documentation Standards
§2](../standards/documentation-standards.md)), not a unit that earns its own code-and-docs repo pair — the
one-project-one-repo-pair convention tracks Product, never Service.

## 2 Documentation Standards
All Weaver Engineering projects should include a `README.md` file with a brief overview of the project in their docs repo.

The `README.md` should include a link to a `PRODUCT.md` which should include *front-matter* about the project. For example the issue management provider for the project, so that agents and tools have a quick reference for details about a project.

## 3 Non-Functional Requirements
A project should identify the non functional requirements (NFRs) which are specifically important for the project, so the design for the project can focus on supporting these NFRs.


