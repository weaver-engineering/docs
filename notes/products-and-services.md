# Mental Model: Mapping Platforms, Products, Product Offerings, Use Cases, Services, Systems, and SLOs

## Executive Overview

Building successful software systems requires alignment between business intent, user goals, and backend architecture. When misaligned, organizations risk building unused features, missing reliability targets, or failing to communicate value. 

This framework establishes a clear continuum from **platform enablement** down to **operational guarantees**, highlighting the core execution role of the Software Development Engineer (SDE).

---

## Conceptual Definitions & Relationships

```
[ Platform (as a Product) ]   Infrastructure, Tooling & Ecosystem Engine
     │
     ▼
[ Product ]                    Parent Commercial Application / Solution
     │
     ▼
[ Use Case ]                   User Intent & Workflow (End-User or Internal Developer)
     │
     ▼
[ Product Offering ]           Commercial Packaging & Feature Tiers
     │
     ▼
[ Service ]                    Functional Application Boundaries (SDE Implementation Layer)
     │
     ▼
[ System ]                     Underlying Infrastructure & Datastores
     │
     ▼
[ SLO ]                        Measurable Reliability Guarantees
```

### 0a. Platform ("Platform as a Product")
* **Definition:** The shared infrastructure, APIs, and tooling layer that acts as an enabling engine for internal teams or external developers.
* **Core Question:** *"What capabilities do we build to empower engineers to ship features safely and fast?"*
* **Role:** A Platform is the direct *product* produced by a Platform Engineering team. Its "customers" are product engineers or third-party developers, and it has its own explicit use cases (e.g., self-service service provisioning, unified auth).

### 0b. Product (Parent Commercial Solution / Application)
* **Definition:** The standalone application or software solution delivered directly to end-users to solve domain-specific problems.
* **Core Question:** *"What complete software solution do we bring to market?"*
* **Role:** Serves as the primary user-facing boundary containing underlying offerings and features.

### 1. Use Cases (User Perspective)
* **Definition:** The specific workflow, context, or problem a user (end-customer or internal developer) aims to accomplish.
* **Core Question:** *"How do I solve this specific problem in my daily work?"*
* **Role:** Dictates value perception and required operational characteristics (e.g., speed vs. consistency).

### 2. Product Offerings (Business Perspective)
* **Definition:** The commercial packaging, feature sets, tiering, and capabilities delivered to the user.
* **Core Question:** *"What capabilities do we build and sell?"*
* **Role:** Packages technical capabilities into explicit value propositions (e.g., Free, Pro, Enterprise tiers).

### 3. Services (Application / Logical Layer — Primary SDE Domain)
* **Definition:** Orchestrated functional execution units and application boundaries that implement business logic.
* **Core Question:** *"Which business logic processes handle this capability?"*
* **Role:** Translates request flows into domain-specific execution boundaries.

### 4. Systems (Infrastructure / Hardware Layer)
* **Definition:** The underlying compute, network, database, and storage assets that execute services.
* **Core Question:** *"What infrastructure resources power our application code?"*
* **Role:** Provides raw computing power, persistence, and connectivity required by application services.

### 5. Service Level Objectives (SLOs) (Operational / Quality Layer)
* **Definition:** Quantifiable targets defining acceptable reliability, latency, and performance thresholds.
* **Core Question:** *"How reliable does this system need to be to avoid breaking the user experience?"*
* **Role:** Forms the operational contract between technical delivery and user satisfaction.

---

## SDE Ownership Across the Continuum

The Software Development Engineer (SDE) acts as the primary translation layer across this entire model—taking high-level Use Cases and Product Offerings, mapping them to logical Service boundaries, implementing them on target Systems, and ensuring they operate within defined SLOs.

* **Platform Level (Product SDE vs. Platform SDE):**
  * **Product SDEs:** Act as "customers" of the platform, utilizing shared platform services and APIs to ship features rapidly without reinventing infrastructure.
  * **Platform SDEs:** Build the platform itself as their primary product, treating internal engineers as users to enable self-service workflows.
* **Use Case & Product Offering Level:** SDEs partner with Product Managers to translate user workflows into software logic, ensuring that feature designs account for operational constraints (e.g., choosing an asynchronous design for batch tasks).
* **Service Level (Core Implementation):** SDEs own the logical application boundaries. They write business logic and categorize services into archetypes (Request-Driven, Pipeline, or Storage) to select appropriate design patterns.
* **System & SLO Level (Operational Accountability):** SDEs choose and configure underlying databases and compute resources. They define metrics (SLIs) to monitor system performance and take responsibility for error budgets when SLO limits are threatened.

---

## Service Archetypes (*The Art of SLOs* Framework)

In *The Art of SLOs*, services are categorized into three distinct operational archetypes based on how work is processed:
1. **Request-Driven Services:** Synchronously process real-time requests initiated by a client (e.g., HTTP/gRPC APIs, web frontends).
2. **Pipeline Services:** Asynchronously process, transform, or aggregate streams or batches of data (e.g., ETL jobs, event processors, log handlers).
3. **Storage Services:** Persist and retrieve data over time with guarantees on data safety and access speed (e.g., SQL/NoSQL databases, object stores, caches).

---

## SLI Categories & Service Archetype Applicability

Different service archetypes require different Service Level Indicator (SLI) types to measure their health accurately.

### SLI Applicability Matrix

| SLI Category | Primary Definition | Request-Driven | Pipeline | Storage |
| :--- | :--- | :---: | :---: | :---: |
| **Availability** | The proportion of valid requests or operations serviced successfully (non-error status). | **Primary** | Secondary | **Primary** |
| **Latency** | The time taken to service a request or complete an operation, evaluated against a threshold. | **Primary** | Secondary | **Primary** |
| **Quality** | The proportion of requests served without degradation (e.g., using fallback cache or stripped features). | **Primary** | N/A | Secondary |
| **Throughput** | The rate of processing data, requests, or transactions over time. | Secondary | **Primary** | **Primary** |
| **Freshness** | The elapsed time between when data was generated/received and when it is processed and visible. | N/A | **Primary** | Secondary |
| **Correctness** | The proportion of records or data inputs correctly transformed without error or data corruption. | Secondary | **Primary** | Secondary |
| **Coverage** | The proportion of valid target data successfully processed during a job run or window. | N/A | **Primary** | N/A |
| **Durability** | The probability that data, once written, can be successfully retrieved without loss over time. | N/A | N/A | **Primary** |

---

## Architectural Mapping Matrix

| Dimension | Perspective | Primary Focus | Key Example Context | SDE Ownership Role |
| :--- | :--- | :--- | :--- | :--- |
| **Platform** | Developer / Enabling | Shared APIs & tooling | Internal Developer Platform (IDP) / AWS | Platform SDE: Builds platform tools |
| **Product** | Market / Application | Parent software solution | GitHub Platform / Analytics Suite | Product SDE: Integrates feature sets |
| **Use Case** | User Context | Task completion & problem solving | Querying data for real-time analytics | Translates workflows into tech tasks |
| **Product Offering** | Commercial / PM | Features, tiers, and pricing | Analytics API Tier (100k req/mo) | Builds tier-specific capabilities |
| **Service** | Software Architecture | Domain logic & execution boundaries | Query Execution & Cache Services | **Core Owner:** Writes application code |
| **System** | Infrastructure / SRE | Hardware, databases, networks | Postgres Cluster + Redis Caching | Selects datastores & infrastructure |
| **SLO** | Operations / Reliability | Thresholds for uptime, latency, errors | 99.9% of queries complete in $< 200\text{ms}$ | Implements instrumentation & alerts |

---

## Key Interdependencies

* **SDEs Bridge Business Intent to Architecture:** SDEs sit at the intersection of Product Offerings and Systems, translating Use Cases into functional Services while enforcing performance bounds via SLOs.
* **Platforms are the Product of Platform Teams:** Treating an internal platform as a product with developer customers ensures that infrastructure serves explicit use cases (e.g., sub-5-minute app deployments) rather than ad-hoc scripts.
* **Use Cases Drive SLO Thresholds:** Critical workflows (e.g., payment processing) demand strict SLOs ($99.999\%$ uptime, sub-100ms latency), whereas non-real-time workflows (e.g., batch reporting) tolerate lower thresholds.
* **Service Archetypes Dictate SLI Selection:** Choosing SLIs requires identifying whether the service is Request-Driven, Pipeline, or Storage; applying latency metrics to a batch pipeline is less useful than measuring freshness or throughput.
* **Product Offerings Group Services:** A single product offering rarely maps to one service; it orchestrates multiple microservices across different archetypes into a cohesive solution.
* **SLO Breaches Risk Product Failure:** When underlying systems violate their SLOs, product promises break, directly blocking users from executing their use cases and driving churn.

---

## References & Sources

* [Google Site Reliability Engineering — Service Level Objectives](https://sre.google/sre-book/service-level-objectives/): Defines SLI metric categories (Availability, Latency, Quality, Throughput, Freshness, Correctness, Coverage, Durability) and operational frameworks.
* [Google Cloud Workshop — The Art of SLOs](https://sre.google/resources/practices-and-processes/art-of-slos/): Categorizes microservices into three core operational archetypes: Request-Driven, Pipeline, and Storage services.
* [CNCF Platform Engineering — Platform as a Product Guide](https://tag-app-delivery.cncf.io/whitepapers/platforms-as-products/): Outlines how platform engineering teams build internal developer platforms treated as software products for developer end-users.
* [Martin Fowler — Software Architecture Continuum](https://martinfowler.com/architecture/): Establishes foundational concepts mapping business intent, domain boundaries, and backend system designs.
