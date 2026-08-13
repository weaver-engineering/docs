# Welcome to Weaver Engineering

## Context
* [About Weaver Projects](about-weaver-engineering.md)

The weaver-engineering organisation is for developing business solutions using 100% agent authored code.

The idea is to collaborate and share ideas and ways of working between like-minded architects and to provide
the tools and sub-agents, workflows and processes to achieve fit-for-purpose quality results without becoming 
beholden to AI.

Architect tools should always seek to support the architect to achieve their objectives without relying on 
expensive frontier models for **all** AI work. In many cases a quality result can be achieved through lighter,
less capable and ultimately significantly cheeper models.

## Projects
Weaver Engineering projects have both a code repository and a separate docs repository, e.g. `the-loom` code 
repository and `the-loom-docs` documentation repository are the code and documentation of the `Loom` project. 
Projects are managed in linear at [linear.app/weaver-engineering](https://linear.app/weaver-engineering)

For more details see [Weaver Engineering Projects](projects/weaver-projects.md).

## Workflows
To work effectively and efficiently with software development agents while still retaining ownership of the code 
base and delivering fit-for-purpose solutions requires consistent workflows delivering quality specifications that 
the agents can deliver with limited supervision. To minise spec misses (when the spec the agent recieves is not 
accurate enough or specific enough or queued out of sequence etc.) the processes of the workflows preceeding the 
agents own spec/test/build workflow should be defined, repeatable and AI asssisted and their outputs should be 
reviewed, cross checked and AI assisted.

The Weaver Engineering organisation seeks to identify and codify these workflows an support them with AI tooling 
to assist software architects to deliver fit-for-purpose solutions agentically.

For more details see [Weaver Engineering Workflows](workflows/weaver-workflows.md).

## Documentation Standards
Project documentation is written to be read by AI agents as much as by architects: a fixed minimum directory
structure per project docs repo, a fixed document shape (Context, numbered sections, optional Rationale/Appendix),
and a machine-maintained per-document index supporting full-text search — with compliance checked at PR time,
not left aspirational.

For more details see [Documentation Standards](standards/documentation-standards.md).

## CI/CD Setup
Every project's code and docs repos are set up the same way: a gate-check workflow validating every PR, and
branch protection enforcing that it actually ran and passed before a merge is possible.

For more details see [CI/CD Setup](standards/ci-cd-setup.md).

## Templates
Reusable document templates for shapes that recur across projects — currently just the use-case template, since
it applies to any project's docs repo, not one in particular.

For more details see [Use Case Template](templates/USE-CASE-TEMPLATE.md).

## Environment Setup
One-off steps for getting a new weaver-engineering development environment working — not specific to any one
project, e.g. authenticating a new machine to GitHub Packages so it can resolve packages like `@weaver-engineering/gate-checks`.

For more details see [Dev Environment Setup](onboarding/dev-environment-setup.md).

