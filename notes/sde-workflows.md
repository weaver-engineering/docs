### SDE Workflows for 100% Agent-Authored Code Development

#### Overview & Paradigm Shift
Transitioning to 100% agent-authored code fundamentally shifts the Software Development Engineer (SDE) role from **implementation builder** to **specifier, architect, and verifier**[span_0](start_span)[span_0](end_span).

In this model, humans do not write production logic or individual unit tests[span_1](start_span)[span_1](end_span). Instead, SDEs author watertight, machine-readable specifications, domain boundaries, and behavioral expectations[span_2](start_span)[span_2](end_span). Agents write all production code and run iterations against strict test coverage constraints[span_3](start_span)[span_3](end_span). By restricting agents strictly to service-level integration testing (network/API boundary assertions), implementation details remain disposable, while the behavioral contracts, NFRs, and SLOs remain locked[span_4](start_span)[span_4](end_span).

The verification loop shifts left: the agent first acts as a "Test Author", raising a pull request containing only failing behavioral tests generated against the SDE's specs[span_5](start_span)[span_5](end_span). The architect/SDE reviews these failing tests to ensure they accurately reflect the required domain behaviors before any implementation code is generated[span_6](start_span)[span_6](end_span).

---

#### Core SDE Agentic Workflows

##### 1. Architectural & Domain Specification Workflow
* **Goal:** Establish unambiguous service boundaries and data models so agents do not bleed domain logic or break system contracts[span_7](start_span)[span_7](end_span).
* **SDE Actions:**
    * Define service boundaries, external dependencies, and input/output schema contracts (e.g., OpenAPI, AsyncAPI, gRPC Protobuf)[span_8](start_span)[span_8](end_span).
    * Classify the service into its operational archetype (**Request-Driven**, **Pipeline**, or **Storage**) to establish applicable design patterns[span_9](start_span)[span_9](end_span)[span_10](start_span)[span_10](end_span).
    * Specify data models, entity relationships, state transitions, and persistent storage schema constraints[span_11](start_span)[span_11](end_span).
* **Artifact Output:** Interface contracts, API schemas, and database migration blueprints[span_12](start_span)[span_12](end_span).

##### 2. Service-Level Test Suite Generation Workflow (Stage 1 Agent)
* **Goal:** Generate an executable, 100% failing service-level test suite operating as a read-only behavioral contract[span_13](start_span)[span_13](end_span).
* **SDE & Agent Actions:**
    * SDE maps Use Cases into Gherkin/BDD-style scenarios (Given/When/Then) targeting external network boundaries[span_14](start_span)[span_14](end_span).
    * Stage 1 Agent ingests specs and contracts to generate containerized integration test assertions (covering happy paths, edge cases, error modes, and idempotency guarantees)[span_15](start_span)[span_15](end_span).
    * Stage 1 Agent opens a "Failing Test Suite" draft pull request with 0% implementation code[span_16](start_span)[span_16](end_span).
* **Artifact Output:** An executable, failing service-level test PR operating against real ephemeral environments (e.g., Testcontainers)[span_17](start_span)[span_17](end_span).

##### 3. Behavioral Review & Test Verification Workflow (Architect Review)
* **Goal:** Verify that the failing test suite accurately captures true Use Case intent, boundary conditions, and domain expectations before code generation begins[span_18](start_span)[span_18](end_span).
* **SDE / Architect Actions:**
    * Inspect the agent-generated failing test suite against business Use Cases and domain expectations[span_19](start_span)[span_19](end_span).
    * Request revisions or approve and merge the failing test PR into the target branch[span_20](start_span)[span_20](end_span).
    * Merging locks the test suite as a read-only contract for the implementation phase[span_21](start_span)[span_21](end_span).
* **Artifact Output:** Approved and merged immutable test suite contract[span_22](start_span)[span_22](end_span).

##### 4. Operational Contract & SLO Specification Workflow
* **Goal:** Embed non-functional requirements (NFRs) directly into verifiable test scripts so agents build resilient infrastructure (e.g., retries, pooling, caching)[span_23](start_span)[span_23](end_span).
* **SDE Actions:**
    * Define key Service Level Indicators (SLIs) aligned with the service archetype (e.g., Latency/Availability for Request-Driven vs. Freshness/Coverage for Pipelines)[span_24](start_span)[span_24](end_span)[span_25](start_span)[span_25](end_span).
    * Establish explicit Service Level Objectives (SLOs), such as 99.9% success rate under 1,000 RPS or p95 latency < 150ms[span_26](start_span)[span_26](end_span).
    * Author load profile scenarios and fault-injection specs (chaos engineering assertions) to validate error budgets and system limits[span_27](start_span)[span_27](end_span).
* **Artifact Output:** Executable load tests (e.g., k6, Locust), telemetry/metric emission assertions, and health check contracts[span_28](start_span)[span_28](end_span).

##### 5. Sandboxed Implementation Orchestration Workflow (Stage 2 Agent)
* **Goal:** Provide the agent with an isolated execution environment to write production code that satisfies the approved tests without human intervention[span_29](start_span)[span_29](end_span).
* **SDE & Agent Actions:**
    * Configure the agent execution environment with read-only access to specs/tests and write access *only* to service implementation directories[span_30](start_span)[span_30](end_span).
    * Define execution limits: branch coverage targets (e.g., >= 95%), linter rules, and dependency constraints[span_31](start_span)[span_31](end_span).
    * Stage 2 Agent runs an autonomous write-build-test loop in the sandbox until all service-level tests and load/SLO specs pass[span_32](start_span)[span_32](end_span).
* **Artifact Output:** Implementation pull request with passing integration runs, verified branch coverage reports, and passing load/SLO suites[span_33](start_span)[span_33](end_span).

##### 6. Operational Release & Security Verification Workflow
* **Goal:** Final sanity check and automated validation of generated code prior to production deployment[span_34](start_span)[span_34](end_span).
* **SDE Actions:**
    * Confirm test suite immutability (verify the agent did not modify test files during the implementation phase)[span_35](start_span)[span_35](end_span).
    * Review automated static analysis, dependency vulnerability scans, and security reports[span_36](start_span)[span_36](end_span).
    * Approve implementation pull request for deployment[span_37](start_span)[span_37](end_span).
* **Artifact Output:** Approved implementation pull request and deployable artifact[span_38](start_span)[span_38](end_span).

---

#### Workflow & Tooling Matrix

| Workflow Phase | Primary SDE / Architect Action | Agent Action | Tooling & Automation Focus |
| :--- | :--- | :--- | :--- |
| **Phase 1: Domain & Contract Spec** | Defines API contracts, DB blueprints, & NFR/SLO targets[span_39](start_span)[span_39](end_span). | Idle / Passive[span_40](start_span)[span_40](end_span). | **Spec Linter & Schema Validator:** Enforces contract syntactical correctness and standardizes blueprint formatting[span_41](start_span)[span_41](end_span). |
| **Phase 2: Test Suite Generation** | Ingests specs; triggers Stage 1 agent run[span_42](start_span)[span_42](end_span). | Generates containerized integration tests (100% failing) & opens PR[span_43](start_span)[span_43](end_span). | **Contract-to-Test Engine:** Auto-scaffolds BDD/Gherkin assertions against real ephemeral dependencies (e.g., Testcontainers)[span_44](start_span)[span_44](end_span). |
| **Phase 3: Architectural Intent Review** | Reviews test PR for behavioral completeness and edge cases[span_45](start_span)[span_45](end_span). | Responds to feedback by updating test assertions[span_46](start_span)[span_46](end_span). | **Use-Case to Test Diff Visualizer:** Maps test assertions directly back to requirements to highlight missing edge cases[span_47](start_span)[span_47](end_span). |
| **Phase 4: Operational Contract Spec** | Defines SLIs/SLOs and load profile parameters[span_48](start_span)[span_48](end_span). | Generates load profiles and fault-injection scripts[span_49](start_span)[span_49](end_span). | **SLO Load Spec Generator:** Translates NFRs into executable load and fault-injection scripts (e.g., k6, Locust)[span_50](start_span)[span_50](end_span). |
| **Phase 5: Implementation Loop** | Approves Test PR; hands off to Stage 2 agent[span_51](start_span)[span_51](end_span). | Runs autonomous write-build-test loop in sandbox until code passes and coverage rules are met[span_52](start_span)[span_52](end_span). | **Agent Execution Sandbox:** Containerized orchestrator enforcing strict write-locks on test files while granting write access to source code[span_53](start_span)[span_53](end_span). |
| **Phase 6: Final Release & Security** | Final check on security and static analysis; approves deployment[span_54](start_span)[span_54](end_span). | Idle[span_55](start_span)[span_55](end_span). | **Behavioral Diff & Verification Hub:** Audits PRs against initial specs, flagging unasserted branches or security flaws[span_56](start_span)[span_56](end_span). |

---

#### Public References & Foundational Sources

* [Google Site Reliability Engineering — Service Level Objectives](https://sre.google/sre-book/service-level-objectives/): Defines SLI metric categories (Availability, Latency, Quality, Throughput, Freshness, Correctness, Coverage, Durability) and operational frameworks[span_57](start_span)[span_57](end_span).
* [Google Cloud Workshop — The Art of SLOs](https://sre.google/resources/practices-and-processes/art-of-slos/): Categorizes microservices into three core operational archetypes: Request-Driven, Pipeline, and Storage services[span_58](start_span)[span_58](end_span).
* [CNCF Platform Engineering — Platform as a Product Guide](https://tag-app-delivery.cncf.io/whitepapers/platforms-as-products/): Outlines how platform engineering teams build internal developer platforms treated as software products for developer end-users[span_59](start_span)[span_59](end_span).
* [Martin Fowler — Software Architecture Continuum](https://martinfowler.com/architecture/): Establishes foundational concepts mapping business intent, domain boundaries, and backend system designs[span_60](start_span)[span_60](end_span).
