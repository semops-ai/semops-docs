---
doc_type: hub

pattern: scale-projection

provenance: 1p

metadata:
 pattern_type: concept
 brand_strength: medium
---

# Scale Projection

> Production-readiness is a property of your abstractions, not your infrastructure.

**Scale Projection** is a validation technique: project your current architecture to a scaled deployment and measure the gap. If the gap is purely infrastructure (queues, auth, containers), your domain model is coherent. If it requires logic changes, your abstractions are wrong.

---

## Core Insight

Scale Projection inverts the conventional framing of scaling problems. The standard assumption is: "scaling is hard because infrastructure is hard." Scale Projection says: **infrastructure is obvious if your architecture is correct.** Scaling might be expensive or complex, but it should not be hard to figure out. If it is, the problem is architectural, not infrastructural.

This connects directly to [Explicit Architecture](what-is-architecture.md): if architecture is truly explicit — domain rules encoded independently of implementation — then changing the infrastructure should not require changing the domain logic. Scale Projection is the test for that claim.

```text
Traditional: "We need better infrastructure to scale"
Scale Projection: "Infrastructure is obvious — if it isn't, your architecture needs work"
```

### The Test

Project your current system to the next order of magnitude. Categorize every required change:

| Gap Category | What Changes | Signal |
|-------------|-------------|--------|
| **Clean** | Connection strings, queue config, container orchestration, auth providers | Domain model is coherent — architecture is explicit |
| **Concerning** | Multi-tenancy logic, locking strategies, batch sizes hardcoded in business logic | Accruing domain debt — architecture is partially implicit |
| **Blocking** | Wrong bounded context boundaries, entity definitions that assume single-instance, business rules coupled to infrastructure | Fix abstractions before proceeding — architecture is implicit |

Clean gaps are expected and healthy. They confirm that architecture and infrastructure are properly separated. Concerning and blocking gaps reveal where implicit assumptions have leaked into the domain model.

---

## How It Works

### The Continuum

Scale Projection defines 8 levels representing progressive domain understanding, not operational maturity:

```text
Level 1: Chat → Exploratory conversation, no structure
Level 2: Script → Single-file automation, manual execution
Level 3: Project → Multi-file, version-controlled, documented
Level 4: Service → Running process, API boundary, configuration
Level 5: Composed → Multiple services, defined interfaces
Level 6: Orchestrated → Workflow coordination, state management
Level 7: Governed → Policy enforcement, audit, compliance
Level 8: Scaled → Multi-instance, distributed, production-grade
```

The key insight: **each level represents how well you understand the domain, not how much infrastructure you have.** A system at Level 3 that has clean gaps to Level 8 has better architecture than a system at Level 8 with blocking gaps.

### The Axes

Six independent dimensions of formalization. A system can be at different levels on different axes:

| Axis | What It Measures |
|------|-----------------|
| **Directed** | How explicitly the system's goals and boundaries are defined |
| **Context** | How much domain context is available to the system |
| **Isolation** | How well components are separated from each other |
| **Access** | How data and services are exposed and consumed |
| **State** | How state is managed, persisted, and recovered |
| **Observability** | How visible system behavior is to operators and agents |

### The Manifest

Business logic is the portable artifact across all levels. Infrastructure is just the wrapper.

```text
Level 3 (Project): python ingest.py --source semops-docs
Level 5 (Composed): docker compose run ingest --source semops-docs
Level 8 (Scaled): kubectl apply -f jobs/ingest-semops-docs.yaml

Same business logic. Different wrappers.
```

If the manifest (the business logic) changes between levels, that's a gap signal.

### GAPS.md

The coherence test artifact. For each system component, enumerate what changes at the projected scale level:

```markdown
## Component: Ingestion Pipeline

### Clean Gaps (infrastructure only)
- PostgreSQL connection: localhost:5434 → RDS endpoint
- Qdrant: single instance → Qdrant Cloud cluster
- Concurrency: sequential → queue-based with workers

### Concerning Gaps (logic changes needed)
- Embedding batch size hardcoded in ingest script (should be configurable)
- Source config assumes local filesystem paths

### Blocking Gaps (architectural rethink)
- (none)

**Assessment:** Coherent. Two concerning items to address.
```

---

## Connection to Explicit Architecture

Scale Projection validates the central claim of [Explicit Architecture](what-is-architecture.md): that architecture is the encoding of business rules independent of implementation choices.

### The Architecture-Infrastructure Crosswalk

The SemOps ecosystem already demonstrates this separation. The [architecture-to-infrastructure crosswalk](https://github.com/semops-ai/semops-dx-orchestrator/blob/main/docs/GLOBAL_INFRASTRUCTURE.md) traces the full chain:

```text
Pattern → Capability → Script → Library → Service → Port
(why) (what) (how) (with) (where) (address)
```

Scale Projection asks: **if you change everything to the right of "Script," does anything to the left change?** If not, the architecture is explicit. If it does, implicit assumptions have leaked across the boundary.

### The SemOps Proof

```text
LOCAL DEVELOPMENT CLOUD DEPLOYMENT
laptop AWS / GCP / Azure
└── docker compose up └── kubernetes / ECS
 ├── postgres:5434 ├── RDS Postgres
 ├── neo4j:7474 ├── Neo4j Aura
 ├── qdrant:6333 └── Qdrant Cloud
 └── docling:5001

What changes: connection strings, scaling parameters
What doesn't change: architecture, data flows, API contracts, business logic
```

The [STRATEGIC_DDD](https://github.com/semops-ai/semops-core/blob/main/docs/STRATEGIC_DDD.md) capabilities, integration patterns, and traceability chains remain identical. The queries that govern the system (`SELECT` your architecture) work the same regardless of whether PostgreSQL is local Docker or managed RDS.

### Projection at Real Scale: A SaaS Product

The SemOps Proof above is a clean example — local to cloud, same stack. But Scale Projection's value is in harder projections. Consider a SaaS product growing from a single-region deployment to a global, multi-tenant, regulated product serving millions of users.

In this scenario, a **tenant** is a customer — an organization that subscribes to the product. **Users** are individuals within that tenant who have roles and permissions. Tenants are not flat: a tenant admin typically has significant control over internal structure — creating sub-organizations by region, department, or team, delegating permissions, and defining policies that apply within those boundaries. (This is how cloud services like AWS Organizations and Azure Management Groups work.) The tenant boundary is the top-level unit for isolation, billing, data residency, and compliance — but those concerns can cascade through the tenant's internal hierarchy. The scaling decisions below happen at the tenant boundary, not the user boundary.

A mature SaaS product operates across all [Four Data System Types](../STRATEGIC_DATA/four-data-system-types.md): the **Application Data System** (transactional, OLTP — where DDD Aggregates live and the Repository pattern applies), the **Analytics Data System** (OLAP — coherence scoring, product analytics, dimensional modeling), the **Enterprise Work System** (unstructured knowledge — issues, project boards, support tickets), and the **Enterprise Record System** (canonical truth — billing, compliance, audit). A typical SaaS mix is 60% Application + 25% Analytics + 10% Work + 5% Record. The scaling challenges below apply primarily to the Application Data System — where DDD discipline most directly determines whether gaps are Clean or Blocking. The other system types have different scaling physics (dimensional modeling for analytics, AI extraction for work data, regulatory enforcement for records), but the architectural question is the same: **does scaling require changing your domain logic?**

The complications are real: multi-region latency (US East to EU West adds 80-120ms RTT), data sovereignty laws that constrain where data can physically reside, databases that need partitioning beyond 1TB, compute clusters that require dedicated platform teams at 500+ nodes, egress costs that become a top-3 line item at 50TB/month, and observability bills that can reach millions per year.

The question Scale Projection asks is not "can you handle this?" — it's **"which of these complications require changing your domain logic?"**

#### Multi-Tenancy

| DDD Discipline | Gap Category | What Happens |
| --- | --- | --- |
| Tenant isolation handled by the **Repository** (DDD); domain aggregates are tenant-unaware | **Clean** | Add tenant routing, connection pooling, isolation config. Domain untouched. |
| Tenant-specific business rules embedded in domain logic; aggregates assume shared state | **Blocking** | Must extract tenancy into its own **Bounded Context** and redefine **Aggregate** boundaries. |

In DDD, the **Repository** is the abstraction that mediates between the domain model and the data layer. It is responsible for retrieving and persisting **Aggregates** — self-contained clusters of domain objects with enforced business rules. When the repository handles tenant isolation (filtering data to one tenant before the domain model sees it), the domain logic never needs to know which tenant it is operating within. The **Aggregate Root** — the entry point to each aggregate — processes business rules against whatever data the repository provides, without tenant awareness.

In practice, this means the three common isolation models — shared schema with row-level security, schema-per-tenant, and database-per-tenant — are infrastructure decisions. If aggregates don't contain tenant logic, you choose the model based on customer requirements and cost, not architecture constraints. Shared schema works for thousands of small tenants. Database-per-tenant works for enterprise customers needing SOC2/HIPAA isolation at ~$50-70/month per managed instance. A tiered hybrid is common. None of this touches business logic — unless tenancy was baked into the domain.

#### Data Sovereignty (GDPR, PIPL, LGPD)

| DDD Discipline | Gap Category | What Happens |
| --- | --- | --- |
| PII owned by a dedicated **Bounded Context** (e.g., Identity); other contexts **reference by identity** | **Clean** | Deploy the Identity context in the regulated region. Other contexts route to it. **Domain Events** carry IDs, not PII. |
| PII scattered across aggregates, events, and contexts; no clear ownership | **Blocking** | Must extract PII into a dedicated context before you can comply. This is a domain boundary redesign, not an infrastructure change. |

A **Bounded Context** in DDD is a semantic boundary where a particular domain model applies — it owns its own data, language, and rules. **Reference by identity** (Vernon's Rule 3) means that when one context needs to refer to an entity in another context, it uses an opaque identifier rather than embedding the full object. **Domain Events** are messages that announce something meaningful happened within a context.

When these patterns are followed, PII naturally concentrates in the context that owns identity. An Order context holds a `customer_id`, not the customer's name and email. A Support context holds a `user_id`, not the user's address. This means data residency becomes a deployment question — put the Identity context in the EU — rather than a data archaeology project to find PII scattered across every table and event stream.

GDPR doesn't strictly require EU data residency, but the legal cost of Standard Contractual Clauses and Transfer Impact Assessments often exceeds the cost of running EU infrastructure. China's PIPL is stricter — data for 1M+ individuals must be stored in China, typically through a licensed local cloud partner. The regulatory landscape varies, but the architectural question is constant: **does your domain model know where PII lives?** If a single bounded context owns PII and other contexts reference by identity (Evans' **Context Mapping**), data residency is a deployment topology decision. If PII is everywhere, it's an architectural rethink.

#### Database Partitioning and Sharding

| DDD Discipline | Gap Category | What Happens |
| --- | --- | --- |
| Small **Aggregates**, **reference by identity**, **eventual consistency** across boundaries (Vernon's four rules) | **Clean** to **Concerning** | Aggregate Root ID is the natural shard key. Partition within contexts. Some batch sizes or filesystem assumptions may need config. |
| Large aggregate clusters, direct object references across boundaries, cross-aggregate **invariants** enforced in transactions | **Blocking** | Cross-shard joins are orders of magnitude slower. Must redesign aggregate boundaries before partitioning is feasible. |

Vernon's four rules of **Aggregate** design are central here:

1. **Model true invariants in consistency boundaries.** An invariant is a business rule that must always be true — for example, "an order total must equal the sum of its line items." The aggregate is the boundary within which invariants are enforced atomically.
2. **Design small aggregates.** Most aggregates should be a root entity with value objects; large clusters perform and scale poorly.
3. **Reference other aggregates by identity.** No direct object references across aggregate boundaries — only opaque IDs. This is the rule that makes partitioning possible, because aggregates that reference each other by ID can live on different database partitions without requiring cross-partition joins.
4. **Use eventual consistency outside the boundary.** Cross-aggregate coordination happens through **Domain Events**, not transactions. One aggregate publishes an event; another reacts asynchronously.

When these rules are followed, the **Aggregate Root** ID is the natural shard key — each aggregate is already an independent consistency unit that can be stored and retrieved without knowing where other aggregates live. This is not coincidence; DDD aggregate boundaries and database partition boundaries solve the same problem: **limiting the scope of consistency.**

PostgreSQL handles 100M-500M rows per table before query latency increases as indexes exceed RAM. At 1-10TB, partitioning (splitting a logical table into physical sub-tables on the same instance) is essential. Beyond 10TB, sharding (distributing across separate database instances) becomes a real conversation. If aggregates follow Vernon's rules, sharding is an infrastructure decision — pick a shard key, route queries. If they don't — if aggregates hold direct references to objects in other aggregates, or enforce invariants across aggregate boundaries in a single transaction — you must redesign the domain model before partitioning is feasible.

#### Multi-Region Deployment

| DDD Discipline | Gap Category | What Happens |
| --- | --- | --- |
| Bounded Contexts communicate via **Published Language** / **Open Host Service**; Domain Events are aggregate-scoped and PII-free | **Clean** | Deploy contexts per-region. Replicate event streams. **Anti-Corruption Layers** handle cross-region translation. |
| **Shared Kernel** spanning regions with mutable state; events contain PII; synchronous cross-context calls | **Blocking** | Synchronous calls across 80-120ms RTT break latency budgets. PII in events prevents cross-border replication. Shared mutable state requires distributed consensus. |

DDD defines several **Context Mapping** patterns that describe how Bounded Contexts relate to each other. The ones that matter most at multi-region scale:

- **Published Language** — a standard interchange format (e.g., a versioned API schema) that contexts use to communicate. Because the format is defined independently of either context, each region can run its own instance and still interoperate.
- **Open Host Service** — a well-documented, versioned API that a context exposes for others to consume. Regional instances of the same service serve local consumers with low latency.
- **Anti-Corruption Layer (ACL)** — a translation boundary that protects one context's domain model from another's. At regional scale, ACLs filter and transform data crossing region boundaries — for example, stripping PII from event streams before they leave a regulated region.
- **Shared Kernel** — a small, shared piece of the domain model owned jointly by two contexts. This is pragmatic at single-region scale but dangerous across regions: schema changes must be coordinated simultaneously across all regions, and if the shared kernel contains PII, it creates a data sovereignty blocking gap.
- **Customer-Supplier** — an upstream/downstream relationship where the upstream context provides data or services and the downstream context consumes them. This pattern works naturally across regions because it already assumes the two contexts operate autonomously.

Active-active multi-region (writes in every region) is dramatically harder than active-passive (one region writes, others read). PostgreSQL doesn't support native multi-master — you need extensions like pgEdge Spock or EDB BDR, with conflict resolution strategies (last-write-wins, CRDTs, application-level). But this is an infrastructure decision if your Bounded Contexts are properly isolated. The Context Mapping patterns above are exactly the integration patterns that work across region boundaries because they already assume autonomy between contexts.

#### Compute and Bandwidth

| DDD Discipline | Gap Category | What Happens |
| --- | --- | --- |
| Services align with **Bounded Contexts**; stateless request handling; cross-context communication via **Domain Events** | **Clean** | Scale pods horizontally. Add node pools. Tune autoscaling thresholds. Business logic unchanged. |
| Monolithic services spanning multiple contexts; in-memory state shared across requests | **Concerning** to **Blocking** | Can't scale independently. Must decompose into context-aligned services before horizontal scaling works. |

When each service maps to a Bounded Context, it can be scaled independently — more instances of the Order service don't require more instances of the Identity service. Cross-context communication via Domain Events (asynchronous messages about what happened) rather than synchronous calls means services don't block on each other during scaling operations.

At 50-100 Kubernetes nodes, you need dedicated cost and capacity tooling. At 500+, multi-cluster management becomes necessary. At 1,000+, you're running a platform team managing etcd performance and API server scaling. AWS egress at 50TB/month costs ~$4,250; at 500TB/month, over $35,000. Webhook fan-out to 10,000 endpoints requires per-endpoint rate limiting, exponential backoff with jitter, and dead letter queues. All of this is infrastructure. The domain logic — what the webhooks contain, what the APIs return, what the services compute — doesn't change if Bounded Contexts are properly defined.

#### Observability

| DDD Discipline | Gap Category | What Happens |
| --- | --- | --- |
| Observability is infrastructure; the domain emits **Domain Events**, infrastructure instruments them | **Clean** | Add monitoring, tracing, centralized logging. Domain logic unchanged. |
| Metrics, traces, or health checks coupled to domain internals; domain objects carry observability concerns | **Concerning** | Must decouple before scaling, or costs compound (metrics cardinality explosion at scale can multiply time series by orders of magnitude). |

The DDD principle here is separation of concerns: Domain Events announce what happened in the domain ("order placed," "payment failed"), and the infrastructure layer decides what to measure, trace, log, and retain. The domain model should not be aware of how it is being observed.

A 500-host enterprise deployment can reach $3-4M/year on commercial observability platforms. Metrics cardinality — the number of unique time series — is the most insidious scaling problem. A metric with dimensions `method × status × pod_id` at 1,000 pods produces 25,000 time series. Add `user_id` and it becomes billions. Full-fidelity tracing (recording every request) is cost-prohibitive at scale — most organizations sample 1-10% of successful requests and 100% of errors. All of these are infrastructure tuning decisions that don't change what Domain Events the system produces.

#### The Pattern

Every scaling challenge above follows the same structure:

```text
If DDD discipline was followed → Clean or Concerning gap → Infrastructure work
If DDD discipline was not followed → Blocking gap → Architecture redesign first
```

This is Scale Projection's diagnostic value. You don't discover these problems by deploying at scale. You discover them by **projecting** your current architecture to scale and categorizing the gaps. The act of projection reveals where implicit assumptions have leaked across domain boundaries — before those assumptions become production incidents.

---

## 3P Lineage

Scale Projection extends established patterns with a novel diagnostic application:

| Source Pattern | What It Contributes | What Scale Projection Extends |
|---------------|--------------------|-----------------------------|
| Hexagonal Architecture (Cockburn) | Domain doesn't know infrastructure | Domain logic is *identical* across scale levels, not just isolated |
| Maturity Models (CMMI) | Progressive capability levels | Levels represent *domain understanding*, not process maturity |
| Fitness Functions (Ford/Parsons/Kua) | Automated architecture validation | *Sketch* the scaled version as a diagnostic, don't deploy it |
| Capacity Planning (SRE) | Forecast resource needs | Use infrastructure delta to reveal *architecture* problems |

### Novelty Claim

> If the infrastructure delta between local and scaled is anything other than "wrapper and plumbing," your architecture (not your infrastructure) needs work.

The novel move is using the projection as a **diagnostic tool for architecture quality**, not as a deployment plan. You don't need to deploy at scale to benefit from Scale Projection — the act of projecting reveals where domain logic has been coupled to infrastructure assumptions.

---

## Scale Projection and the Semantic Funnel

Scale Projection connects to the [Semantic Funnel](../../RESEARCH/FOUNDATIONS/semantic-funnel.md) through rule types:

| Gap Type | Semantic Funnel Rule Level | Meaning |
|----------|---------------------------|---------|
| **Clean** | Structural (D→I) | Infrastructure rules change; domain rules don't |
| **Concerning** | Interpretive (I→K) | Business logic has implicit infrastructure assumptions |
| **Blocking** | Normative (K→W) | Fundamental domain boundaries need redesign |

Clean gaps mean the structural rules (schemas, contracts) are properly separated from infrastructure. Concerning gaps mean interpretive rules (business logic, patterns) have leaked across the boundary. Blocking gaps mean the normative rules (policies, governance, boundaries) were never made explicit.

---

## Related Links

### Framework Context
- [Explicit Architecture](what-is-architecture.md) - The principle Scale Projection validates
- [Stable Core, Flexible Edge](stable-core-flexible-edge.md) - The scaling principle
- [Semantic Coherence](../SEMANTIC_OPTIMIZATION/semantic-coherence.md) - What Scale Projection measures

### Implementation
- [PROJECT-25: Scale Projection](https://github.com/semops-ai/semops-dx-orchestrator/blob/main/docs/project-specs/PROJECT-25-scale-projection.md) - Project spec and execution plan
- [STRATEGIC_DDD](https://github.com/semops-ai/semops-core/blob/main/docs/STRATEGIC_DDD.md) - Architecture encoded in SQL
- [GLOBAL_INFRASTRUCTURE](https://github.com/semops-ai/semops-dx-orchestrator/blob/main/docs/GLOBAL_INFRASTRUCTURE.md) - Architecture-to-infrastructure crosswalk

### 3P References
- Cockburn, Alistair. *Hexagonal Architecture*. 2005.
- Ford, Neal; Parsons, Rebecca; Kua, Patrick. *Building Evolutionary Architectures*. O'Reilly, 2017.
- Beyer, Betsy et al. *Site Reliability Engineering*. O'Reilly, 2016.
