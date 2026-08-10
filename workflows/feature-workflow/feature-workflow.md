# Feature Workflow

## Context
* [About Weaver Projects](../../about-weaver-engineering.md)
* [Weaver Engineering Workflows](../weaver-workflows.md)
## Workflow
```
(Start) ------> [Analyse Feature] <---+
                        |             |
                        V             |
                [Design Feature] -----+
                        |             ^
                        V             |
                [Chunk Design]        |
                        |             |
                        V             |
                [Sequence Chunks]     |
                        |             |
                        V             |
                [Schedule Delivery]   |
                        |             |
                        V             |
(Done) <------- [Test Feature] -------+
                        |
                        V
                [Retrospective]
```
### Start
The entry requirements for the Feature workflow are that a 'Feature' has been suggested for delivery as part of a project. The 'Feature' **must** have a an [initial feature document](initial-feature-document.md) outlining the Feature.
The Feature workflow is independent which project includes the Feature so the initial feature document **must** include such tracking frontmatter as is required by the project. 
The initial feature document should provide sufficient details to understand the general idea of what is required and why.
 
### 1 Analyse The Feature
The entry requirements of the `Analyse Feature` step of the workflow is a suitable [initial feature document](initial-feature-document.md).

The process of analysing a Feature is to understand the 'wh...s' of the Feature.
* **Wh**o - e.g. who is it for?
* **Wh**at - e.g. what does it do? What is the benefit?
* **Wh**ere - e.g. where does it exist?
* **Wh**en - e.g. when does it happen?
* **Wh**ich - e.g. which resources does it affect?
* **Wh**y - e.g. why does it happen?
The output of analysis includes a set of [required behaviors](required-behaviors.md). Typically there required behaviors are delivered through a number of [use cases](use-cases.md) which capture the 'wh...s' of the Feature. 
Required behaviors do not need to be defined through [use cases](use-cases.md) but they provide valuable background for the design process when weighing solution options and 'massaging' the requirements to suit the technology landscape. Without the associated use cases the design has no way of knowing which requirements might be flexible nor by how much. Use cases assist the design and delivery of fit-for-purpose solutions.
The whole feature does not need to be analysed before design work can begin but the feature should be analysed enough to avoid future redesign to support later use cases of the Feature.

### 2 Design The Feature
The entry requirements of the `Design Feature` step of the workflow is a set of [required behaviors](required-behaviors.md) and the projects important NFRs.
The design process considers various permutations and combinations of technical solutions to provide the required behaviors while supporting the projects NFRs.
It documents the specific details of the behaviors, `data types`, `interfaces`, `resources`, `external dependencies`, `technology stack` etc. of the design.
The design **must** provide a comprehensive and consistent vision of the solution which provides the required behaviors. 

The output of the design process is the [design directory and hld](design-directory-and-hld.md) within the projects docs repo which includes a `{design-slug}-hld.md` high level design document for the Feature. Other supporting documentation and sub folders may exist in the design folder but all files in the design folder and any sub folders must be navigable from the HLD.
The design documentation will be read by the agents so they should be clear, to the point and separated into appropriate sub documents as necessary to avoid overloading agents with context that is not relevant to their goal. The design identifies the [specific behaviors](specific-behaviors.md) `Given/When/Then` system level behaviors that must be implemented to complete the feature.
### 3 Chunk The Design
The entry requirements of the `Chunk Design` step of the workflow are the [design directory and hld](design-directory-and-hld.md) and the designs [specific behaviors](specific-behaviors.md).
Most features require more than one specification to be sussessfully delivered by agentic software engineers without an over reliance on large contexts and frontier models both if which have a significant negative impact on AI costs.
The process of chunking the design breaks the design down into incrementally deliverable Chunks. Each Chunk **must** deliver at least 1 of the [specific behaviors](specific-behaviors.md) of the Feature such that end-to-end (e2e) testing can verify that delivering the Chunk adds the Features specific behaviour to the project.
Each of the identified Chunks **must** have its external dependencies clearly identified. It **must** also have its dependencies on other Chunks clearly identified. 
Each Chunk **must** have a [specification document](specification-document.md) that defines what the Chunk is required to deliver, including the Chunks external and inter-chunk dependencies. The inclusion these dependencies allows for a mechanical derivation of [the chunk sequence](the-chunk-sequence.md).

The outputs of the chunking process are the [specification documents](specification-document.md) defining each chunk and the mechically derived [chunk sequence](the-chunk-sequence.md).
### 4 Schedule Delivery Of The Chunks
The entry requirements of the `Shedule Delivery` step of the workflow are the [specification documents](specification-document.md) and the [chunk sequence](the-chunk-sequence.md). 
The scheduled delivery of the Chunks is an AI assisted automation of the dispatch and monitoring of the AI agents delivering the code changes to implement the chunk. The scheduling itself is a mechanical process. Each Chunk has a defined place in the [chunk sequence](the-chunk-sequence.md) and [the scheduler](the-scheduler.md) will only allow a Chunk to start once all of its dependencies are satisfied. [The scheduler](the-scheduler.md) mechanically drives the Chunk through the `spec/test/build` workflow ([chunk cycle](chunk-cycle-workflow.md)). AI [supervising agents](supervising-agents.md) are used to monitor the progress, outputs, permission requests of the AI [coding agents](coding-agents.md) only surfacing issues to the architect that genuinely require their input. 
**Note** the architect is always required to approve pull requests (PRs)

The output of the delivery process is the working Feature or at least some if the [use cases](use-cases.md) of the Feature.
### 5 Test The Delivered Feature 
The entry requirements of the `Test Feature` step of the workflow are the working [use cases](use-cases.md) of the Feature. 
Before a Feature can be truly considered done the [required behaviors](required-behaviors.md) of the Feature **must** be tested, i.e. the use cases excercised and found to be delivering the their expected benefits. 

There is no requirement for the Feature testing to wait for all the Chunks to be delivered. If a Chunk or Chunks fail to deliver their expected benefits then the behaviors realised by the Chunk are returned to the Analysis/Design steps to be reworked.

Only once **all** the Chunks have been delivered and **passed** [Feature testing](feature-testing.md) can the Feature be said to be done.
### 6 Retrospective
The entry requirements of the `Retrospective` step of the workflow is the completed Feature.

At the end of each completed Feature workflow it is advisable to reflect on what went well, what didn't go well, and what actions will be taken to do better in the future.

The completion of a retrospective is very important while initially working through the Feature workflow and ironing out its wrinkles and developing its tools.

The outputs of the retrospective is a [document](retrospective-report.md) recording the learning points and future actions to be taken to do better in the future. This document is recorded in @the-loom-docs/feedback/feature-workflow so that they may be analysed to deliver improvements to the agents and tooling supporting the Feature workflow or any if its sub wirkflows e.g. the [chunk cycle](chunk-cycle-workflow.md)
### Done
The Feature workflow is done when all the Chunks fir the Feature have been delivered and passed [Feature testing](feature-testing.md).
