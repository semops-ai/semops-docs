---
doc_type: reference
provenance: 1p
canonical_source: data-pr/docs/research/ddd-data-architecture.md
role_in_method: "Foundation for decision matrix in  (Explicit Enterprise Data Toolkit)"
---

# Data Architecture Through the DDD Lens

> **Issue:** 
> **Parent:**  (Explicit Enterprise Data Toolkit)
> **Date:** 2026-02-21

## Purpose

This document maps DDD concepts to concrete data architecture decisions. Not "DDD is good for data" (established in [what-is-architecture.md](../../semops-docs-refs/what-is-architecture.md) and [data-systems-architecture-map.md](../../semops-docs-refs/data-systems-architecture-map.md)) but **how** each DDD concept translates into a specific architectural choice that a generalist can follow.

The goal: architecture defines data structures, data structures select infrastructure. This document covers the architecture layer. Infrastructure choices come after.

---

## The Core Mapping

| DDD Concept | Data Architecture Decision | Section |
|---|---|---|
| Bounded Context | How to organize the data pipeline by domain | [1](-bounded-contexts--pipeline-organization) |
| Aggregate | How to determine fact table grain and what semantics survive | [2](-aggregates--dimensional-modeling) |
| Anti-Corruption Layer | Where translation happens between data system boundaries | [3](-anti-corruption-layers--integration-patterns) |
| Context Map | How data systems relate to each other | [4](-context-maps--data-flow-architecture) |
| Domain Event | How operational data flows to analytical systems | [5](-domain-events--event-driven-data-architecture) |
| All of the above | The architecture-before-infrastructure sequence | [6](-the-sequence) |
| Independent convergence | Why dbt keeps showing up and what it proves | [7](-dbt-independent-convergence-on-ddd-principles) |

---

## 1. Bounded Contexts → Pipeline Organization

### The Two-Axis Problem

A data pipeline has two organizational axes: **stage** (how far the data has been transformed) and **domain** (which business area it serves). DDD says you need both.

dbt's official project structure guide captures this precisely:

> "The fundamental principle underlying all structure is moving data from **source-conformed** (shaped by external systems out of our control) to **business-conformed** (shaped by the needs, concepts, and definitions we create)."
> — [dbt Developer Hub, How We Structure Our dbt Projects](https://docs.getdbt.com/best-practices/how-we-structure/1-guide-overview)

The inflection point is the intermediate layer:

> "Intermediate models shift towards being business-conformed, splitting our models up into subdirectories **not by their source system, but by their area of business concern**."
> — [dbt Developer Hub, Intermediate Models](https://docs.getdbt.com/best-practices/how-we-structure/3-intermediate)

This produces a two-axis folder structure:

```
models/
  staging/           ← organized by SOURCE SYSTEM (technical boundary)
    stripe/
    salesforce/
    shopify/
  intermediate/      ← organized by BUSINESS DOMAIN (semantic boundary)
    finance/
    marketing/
  marts/             ← organized by BUSINESS DOMAIN (bounded context)
    finance/
      orders.sql
      payments.sql
    marketing/
      customers.sql
```

**The DDD reading:** Staging is organized by the *upstream bounded context* (the source system). Intermediate and Marts are organized by the *downstream bounded context* (the business domain). The transition from source-system organization to domain organization is where the model crosses from one context to another.

### Do OLTP Domain Boundaries Persist Into Analytics?

**Two valid positions exist, and the answer depends on organizational maturity:**

**Position A: The warehouse is a new bounded context.** "Customer" in Sales OLTP and "Customer" in the analytics warehouse are distinct concepts. The OLTP customer has a lifecycle (creation, update, churn). The analytical customer is a dimension — a snapshot used to slice metrics. They share a key but model different things.

**Position B: Domain ownership should persist (Data Mesh).** Dehghani's foundational argument:

> "Though we have adopted domain oriented decomposition and ownership when implementing operational capabilities, curiously we have disregarded the notion of business domains when it comes to data."
> — [Dehghani, How to Move Beyond a Monolithic Data Lake to a Distributed Data Mesh](https://martinfowler.com/articles/data-monolith-to-mesh.html)

**The synthesis:** Domain *ownership* persists; the *model* transforms. The Sales team should own the definition of their analytical data products, but those products are dimensional models, not OLTP schemas.

| Layer | Owner | Model | DDD Analog |
|---|---|---|---|
| Sales CRM (OLTP) | Sales team | Normalized, mutable state | Source bounded context |
| Staging | Data/Sales team | 1:1 source mirror, cleaned | ACL — translation zone |
| Intermediate | Analytics team | Business logic joins, re-grained | Domain logic in new context |
| Sales Mart | Analytics team | Dimensional, fact + dimensions | Published interface |
| Semantic Layer | Platform team | Metric definitions, shared vocabulary | Published Language |

### Data Mesh: DDD Applied to Analytics (and When It's Overkill)

Dehghani explicitly took four DDD concepts and applied them to data:

| DDD Concept | Data Mesh Application |
|---|---|
| Bounded Context | Data domains — ownership boundaries for data products |
| Domain Events | Streaming ingestion — semantically rich events as input |
| Anti-Corruption Layer | Ingestion cleaning step for legacy/COTS sources |
| Ubiquitous Language | Data contracts — enforced vocabulary at domain boundaries |

**When Data Mesh is overkill:** Most organizations don't need full Data Mesh. The simpler version is **domain-organized dbt** within a single project:

1. Use dbt Groups to assign models to domain-aligned teams
2. Organize mart directories by domain (`finance/`, `marketing/`)
3. Set models to "private" by default; "public" only for shared interfaces
4. Use git CODEOWNERS aligned to domain teams

This gives you bounded-context benefits without the organizational overhead of distributed data product teams.

> "If your organization is new to dbt, start in a single project."
> — [Jenna Jordan, So You Want to Build a Data Mesh](https://jennajordan.me/blog/data-mesh-dbt)

### Ubiquitous Language for Data

The DDD concept of ubiquitous language — shared vocabulary within a bounded context — manifests at three levels in data:

| Level | What It Governs | Example | Tooling |
|---|---|---|---|
| **Schema** (staging) | Column naming conventions | `cust_ph_no` → `customer_phone_number` | dbt staging models, YAML docs |
| **Metrics** (semantic layer) | Business measure definitions | "Revenue" = sum of confirmed order totals, excluding refunds | dbt MetricFlow, Cube.js |
| **Contracts** (domain boundary) | Cross-domain shared vocabulary | "Active Customer" = purchase within 90 days | Data contracts, SML |

The metric proliferation problem is a ubiquitous language breakdown:

> "You may start out with one approach to calculating and representing revenue. However, as time evolves... different teams end up with different definitions and understandings of this key concept."
> — [dbt Labs, Semantic Layer Introduction](https://www.getdbt.com/blog/semantic-layer-introduction)

When "revenue" means different things in Finance vs. Sales vs. the CEO dashboard, the bounded context has no enforced language.

---

## 2. Aggregates → Dimensional Modeling

### How Aggregates Map to Fact Tables

In OLTP, a DDD aggregate is "a cluster of domain objects that can be treated as a single unit" with enforced invariants (Fowler). In OLAP, the aggregate boundary collapses and the **grain decision** replaces the consistency boundary.

**Example: Order Aggregate**

OLTP (DDD):
- **Aggregate root:** Order (enforces: total must match line items, order must be valid before payment)
- **Entities:** OrderLine (identity within aggregate)
- **Value objects:** ShippingAddress, Money (no identity)
- **Domain events:** OrderPlaced, OrderCompleted, OrderCancelled

OLAP (Dimensional):
- **Fact table at order-line grain:** Each row = one OrderLine. The Order "explodes" into its parts. OrderID becomes a degenerate dimension.
- **Or order-header grain:** Each row = one Order. Lines aggregated into measures (total, item count).

**The tension:** DDD's grain is "one atomic transaction" (the Order aggregate). Kimball's recommended analytical grain is *lower* (OrderLine) because it enables richer slicing by product dimension. These pull in opposite directions.

### What Happens to Invariants

This is where domain semantics are dropped:

| OLTP Invariant | OLAP Equivalent | What's Lost |
|---|---|---|
| Order total must match line items | dbt test: `sum(line_amount) = order_total` | Enforcement moves from runtime to load-time |
| Order must be "pending" before payment | Timestamps record completed states | State machine transitions are flattened |
| ShippingAddress is a value object (no identity) | May become a dimension with surrogate key | Value semantics lost when given identity |

**The dimensional model preserves the *results* of invariant enforcement. It discards the mechanism, the rules, and the context of why those rules exist.** The "why" lives only in the source system and documentation.

### Kimball vs. Inmon Through the DDD Lens

| Approach | DDD Analog | Alignment |
|---|---|---|
| **Kimball** (star schema by business process) | Event-driven, bounded-context-aligned | Organizes around *business processes* — what happened. Each star schema is relatively self-contained, like a bounded context. |
| **Inmon** (normalized enterprise warehouse) | Single unified enterprise model | Organizes around *entities* — what exists. Contradicts DDD's core insight that a unified model is dangerous. Forces incompatible domain concepts into shared namespace. |

> DDD explicitly endorses multiple bounded contexts with their own ubiquitous languages and *acknowledges that the same concept (e.g., "Customer") means different things in Sales vs. Billing vs. Shipping*.

Inmon's 3NF warehouse tries to normalize these differences away. DDD says those differences are features, not bugs.

**The modern layered modeling approach:**

The synthesis is in the *modeling approach* (Bronze/Silver/Gold with domain-organized tiers), not in any particular infrastructure. A lakehouse (Delta Lake, Iceberg) is just a storage/compute layer — you can build an Inmon monolith on it just as easily as properly bounded Kimball models. The architecture lives in the modeling decisions and semantic layer, not the storage format. This same approach works on DuckDB, Postgres, Snowflake, or Parquet files on a laptop:

- **Bronze:** Raw source data, append-only → preserves original bounded context representation
- **Silver:** Cleaned, conformed, possibly Data Vault → domain context preserved, boundaries respected
- **Gold:** Dimensional models (Kimball star schemas) → query-optimized, bounded-context-aligned
- **Semantic layer:** Metric definitions, ubiquitous language enforcement → what gives the stack its DDD properties

Without the semantic layer, any data platform is just a pile of tables with a catalog. With it, you have bounded contexts, metric definitions, and contracts. Kimball's approach operates at the Gold layer. Data Vault operates at Silver. The semantic layer operates above Gold. Infrastructure is a deployment detail.

### Lineage Replaces SSOT

The deeper issue with Inmon is not normalization per se — it's the **Single Source of Truth (SSOT)** assumption that drives it. Inmon's entire architecture rests on the premise that trust comes from having *one canonical representation*. The normalized enterprise warehouse exists to be that one truth. Everything downstream is a derived view.

This SSOT obsession creates a predictable failure cascade:

```
"We need a Single Source of Truth"
    → Normalize everything into one model (Inmon / MDM)
    → Force incompatible domain concepts into shared namespace
    → Anemic model that serves no context well
    → Teams route around it (shadow analytics, local spreadsheets)
    → "We can't trust the warehouse"
    → Buy a new platform / start a Data Mesh initiative
    → "We need a Single Source of Truth" (cycle repeats)
```

**DDD offers a different trust model: trust through traceability, not through unification.**

SSOT says: there is ONE correct answer, stored in ONE place. If there's only one "Customer" table, there can't be conflicting definitions.

Lineage says: there can be **multiple valid representations** — Sales' Customer, Finance's Customer, Support's Customer — as long as you can:

1. **Trace** any value back to its boundary source
2. **See** what transformations were applied at each step
3. **Understand** which bounded context's definition you're looking at
4. **Reconcile** when needed — not by unifying, but by comparing transformation paths back to shared origins

This reframes the core governance question:

| SSOT Framing | Lineage Framing |
|---|---|
| "Which number is right?" | "Which context's definition are you asking about?" |
| "Who owns THE definition?" | "Is the lineage complete and the transformation documented?" |
| "How do we unify these?" | "Why do they differ, and is that difference appropriate?" |
| Trust = one copy | Trust = traceability |

**The connection to existing frameworks:**

- **Surface Analysis** — provenance position (boundary vs. internal) tells you where semantic authority lives. You don't need SSOT if you know which source has authority for which context.
- **"Same events, different physics"** (from `data-silos.md`) — the events are the same. The representations differ because the optimization targets differ. Lineage connects them without forcing unification.
- **Governance as Strategy** — governance isn't centralizing into one copy. It's ensuring that any value can be explained: where it came from, how it was transformed, and which domain's rules apply.
- **Four Data System Types** — each type is a bounded context with its own valid model of the same business reality. Lineage connects them; SSOT would collapse them.

**The practical implication for the toolkit :** The expensive governance tools (Collibra, Atlan) sell SSOT-as-product — golden records, canonical definitions, master data management. The explicit enterprise alternative is: **SSOT is the wrong goal. Traceability is the right goal. Lineage gives you traceability. Lineage can be built from open primitives** (OpenLineage + Postgres + provenance tracking). This connects directly to the open-source data hub work in .

### Why Lineage-Over-SSOT Is Agent-Native

The lineage-over-SSOT argument has a second, structural consequence: **it determines whether AI agents can govern data at all.**

An agent — per the five-primitive model in [agent-primitives.md](agent-primitives.md) — is Model + Context + Prompt + Memory + Tools. The critical primitive is **Context**: the bounded token window at inference time. An agent can only reason about what fits in its window *right now*. This is not a limitation to be engineered around — it is the fundamental operating constraint, and it maps directly to DDD's bounded context.

SSOT assumes a single observer who needs ONE answer. But a multi-agent system has N observers, each with their own bounded Context window, each operating in a different domain. The structural mismatch:

| Data Governance Model | Agent Architecture Implication |
|---|---|
| **SSOT** (one canonical representation) | All agents must coordinate through a master system. Context windows polluted with governance overhead. Memory becomes a coordination bottleneck. |
| **Lineage** (multiple valid representations, traceable) | Each agent works with its domain's representation. Context stays clean and narrow. Memory is domain-scoped. Governance is post-hoc traceability. |

The SSOT failure cascade has an agent-specific variant:

```
"We need a Single Source of Truth for agent state"
    → Force all agents to coordinate through one master system
    → Context windows polluted with reconciliation/governance overhead
    → Agents can't reason cleanly about their own domain
    → Hallucinations, wrong-context answers, coordination deadlocks
    → "We can't trust the agents"
    → Add more governance layers / human checkpoints
    → (cycle repeats)
```

**Lineage dissolves this.** Each agent operates in its bounded context — Sales agent works with Sales' "Customer," Finance agent works with Finance's "Customer." The governance question is not "which agent has THE answer" but "can we trace how each agent arrived at its answer?" This is the same reframe as the data governance table above, applied to agent infrastructure:

| SSOT Agent Governance | Lineage Agent Governance |
|---|---|
| "Which agent's answer is canonical?" | "Which domain's definition did this agent use?" |
| "How do we synchronize agent state?" | "Is the agent's lineage — input, context, reasoning, output — complete?" |
| "Who controls THE master record?" | "Can we trace from any agent output back to its boundary source?" |

**The connection to existing primitives:** Agentic lineage (see [agent-primitives.md](agent-primitives.md), "Where Agentic Lineage Fits") already records per-episode provenance of all five primitives — which model, what was in the context window, which instructions were active, what was retrieved from memory, what tools were called. This IS lineage-over-SSOT applied to agent infrastructure. The same architectural principle governs both data flows and agent flows: trust through traceability, not through unification.

**The practical implication:** Multi-agent data governance does not need a centralized "agent truth store." It needs the same thing data governance needs — lineage. OpenLineage for the data pipeline. Agentic lineage for the agent pipeline. Both built from open primitives. Both traceable back to boundary sources. Both allowing multiple valid representations connected by provenance, not collapsed into a master.

### Mapping Table

| DDD Concept | Dimensional Modeling Equivalent |
|---|---|
| Aggregate root | Grain declaration — the atom of the business event |
| Aggregate invariant | Data quality test (enforcement at rest, not runtime) |
| Value object | Degenerate dimension or mini-dimension (may lose value semantics) |
| Entity within aggregate | Fact table row (identity within partition) |
| Aggregate boundary | Business process (Kimball Step 1) |
| Bounded context | Star schema / data mart |
| Shared kernel | Conformed dimension |
| Context map | Enterprise Bus Matrix |

---

## 3. Anti-Corruption Layers → Integration Patterns

### Is the ETL/ELT Pipeline an ACL?

Evans distinguishes an ACL from a simple translation layer by *intent*:

> "It's that you are protecting your model from the concepts on the other side. The concepts on the other side of the wall will mess you up if you have to try and deal with them."
> — Evans, *Domain-Driven Design*, Chapter 14

**The dbt staging layer meets this definition** when its intent is to protect the analytical model from source system volatility:

> "Source-conformed data is shaped by external systems out of our control, while business-conformed data is shaped by the needs, concepts, and definitions we create."
> — [dbt Developer Hub, How We Structure Our dbt Projects](https://docs.getdbt.com/best-practices/how-we-structure/1-guide-overview)

This is Evans' ACL in different words. The staging layer translates the source system's model (their column names, their data types, their conventions) into the analytical domain's own model.

**The distinction matters:** An ACL says "the analytics domain owns its own concepts and adapts external data to them." A mere translation layer says "we copy the source's model but with nicer column names." If you're just renaming columns, it's a translator. If you're mapping foreign concepts into your domain's ubiquitous language, it's an ACL.

**The full pipeline as ACL:**

```
Source System (upstream bounded context)
    │
    ▼
Staging (ACL — translation + protection)
    │
    ▼
Intermediate (domain logic in analytical context)
    │
    ▼
Mart (the protected domain model)
    │
    ▼
Semantic Layer (Published Language for consumers)
```

### Is the Semantic Layer an ACL?

The semantic layer occupies a dual position:

1. **Open Host Service + Published Language** from the warehouse side — it offers a formal, versioned interface in business vocabulary that all consumers use
2. **ACL** from the consumer side — it protects consumers from warehouse schema changes

When the warehouse renames a column, the semantic layer absorbs the change. Consumers never see it. This is protective isolation — Evans' ACL.

The semantic layer is a genuine bounded context sitting between the warehouse model and the consumer model, with its own domain language (metrics, dimensions, entities in business vocabulary).

### Medallion Architecture Through DDD

| Layer | DDD Role | Key Property |
|---|---|---|
| **Bronze** | Raw upstream model — no domain model applied | Source-conformed, append-only |
| **Silver** | ACL / translation zone — emerging domain model | Cleaned, conformed, business logic starting |
| **Gold** | Protected domain model — explicit business semantics | Bounded-context-aligned, consumption-ready |

**The ACL is the Silver layer** — the transformation logic that reads from Bronze (source-conformed) and writes to Silver (analytically-conformed). The Silver→Gold boundary is progressive refinement within the same domain.

**Key insight from Databricks:** When they recommend creating "multiple gold layers to meet different business needs" (HR, Finance, IT), they are implicitly recommending bounded contexts at the Gold tier. HR's "employee headcount" may differ from Finance's. They should. Each Gold layer expresses its own domain's ubiquitous language.

### Reverse ETL: Two ACLs in Opposite Directions

When analytical data flows back to operational systems (ML scores → CRM, customer segments → ad platforms), the DDD pattern is **not** Partnership or Shared Kernel. It's two independent ACLs:

- Forward: Analytics ACL translates CRM data into warehouse concepts
- Reverse: Reverse ETL ACL translates warehouse concepts back into CRM field formats

The systems remain decoupled. Each direction has its own translation logic. Neither needs a shared model.

**Watch for feedback loops:** analytics computes metric from CRM data → pushes back to CRM → modifies behavior → changes CRM data → re-ingests. DDD's concept of aggregate invariants would flag this as a design smell.

---

## 4. Context Maps → Data Flow Architecture

DDD's context mapping patterns describe how bounded contexts relate. Applied to data systems:

### Pattern Mapping

| DDD Context Map Pattern | Data Architecture Equivalent | When It Applies |
|---|---|---|
| **Conformist** | CDC from source with no transformation influence | Analytics accepts whatever source emits. Source team doesn't care about analytics. **This is the default.** |
| **Customer/Supplier** | Negotiated source schema changes | Analytics team has influence over upstream schema (e.g., requesting a `created_at` column) |
| **Anti-Corruption Layer** | dbt staging + transformation layer | Analytics protects its model from source volatility |
| **Published Language** | Versioned event schemas in schema registry (Avro/Protobuf) | Source publishes stable, documented event contracts |
| **Open Host Service** | Well-documented source API used by multiple consumers | Source team maintains a formal interface (not just raw table access) |
| **Shared Kernel** | Conformed dimensions (Customer, Product, Date) | Shared reference data across multiple data products |
| **Separate Ways** | No integration — each domain maintains its own data | When the cost of integration exceeds the value |

### CDC Is Almost Always Conformist

In most organizations, the OLTP source system **does not care** about analytics consumers. The operational team's schema is dictated by their own domain needs. Analytics receives whatever the source emits and adapts.

This is the Conformist pattern. The OLTP system is upstream; analytics is downstream with no negotiating power. Analytics cannot ask the product team to rename `user_id` to `customer_id` or add timestamps the operational system finds unnecessary.

**The full pipeline reads as:** Conformist at the CDC boundary + ACL at the transformation boundary + domain model at the mart boundary.

When a source team deliberately designs their system to support analytics — publishing events via Kafka with maintained Avro schemas, version-controlled contracts in a schema registry — the relationship upgrades to **Open Host Service + Published Language**. This is an organizational commitment, not just a technical choice.

### The Enterprise Bus Matrix Is a Context Map

Kimball's Enterprise Bus Matrix — a grid of business processes (rows) vs. conformed dimensions (columns) — is the analytical equivalent of DDD's context map. It shows which bounded contexts share which concepts and where integration is required.

---

## 5. Domain Events → Event-Driven Data Architecture

### CDC Is Not Domain Events

This distinction is critical and frequently confused:

> "CDC is about capturing data changes within a system's bounded context, usually in the terms of the physical model, while events are domain model level broadcasts... that represent semantically significant events in a language that external systems can understand."
> — [Kislay Verma, Domain Events vs. CDC](https://kislayverma.com/software-architecture/domain-events-versus-change-data-capture/)

| Dimension | CDC Capture | Domain Event |
|---|---|---|
| **Trigger** | INSERT into `orders` table | `OrderPlaced` raised by Order aggregate |
| **Content** | Column values: `order_id`, `status='pending'` | `OrderPlaced { orderId, items, totalAmount, channel }` |
| **Intent** | None — it's a database operation | Explicit: a customer placed an order |
| **Context** | Physical schema of the orders table | Ubiquitous language of the Sales context |

**What CDC forces downstream analytics to do:** reconstruct intent from state. Instead of `OrderPaid` events that carry business meaning, systems get row-level changes that force every consumer to diff fields and infer what happened.

**What is lost in OLTP → OLAP via CDC:**

1. **Business intent** — the "why" behind the state change
2. **Event vocabulary** — the ubiquitous language of the domain
3. **Aggregate boundary context** — CDC exposes individual table mutations; the aggregate boundary is invisible
4. **Invariant context** — CDC cannot tell you which invariant was satisfied
5. **Causal chain** — CDC captures isolated changes; domain events carry correlation IDs

### The Outbox Pattern as Bridge

The transactional outbox pattern solves this: write both the operational state update AND the domain event to an outbox table atomically in the same transaction. CDC (Debezium) reads from the outbox table, not from operational tables.

The downstream pipeline then receives semantically complete domain events rather than database mutations. The Bronze layer stores `OrderPlaced` events with full business context rather than normalized table snapshots.

### Event Sourcing: The Ideal Source for Analytics

When the source system uses event sourcing, the "raw data" IS domain events. No translation loss because no translation.

**Mapping to medallion architecture:**
- **Bronze:** The event store itself (or faithful copy). Append-only, immutable, chronologically ordered, carries domain semantics.
- **Silver:** Projections over the event stream — builds current-state read models (entity tables with SCD Type 2 naturally captured).
- **Gold:** Dimensional models built from Silver projections.

Event sourcing eliminates the "slowly changing" ambiguity: every dimension change is a named event with a timestamp and reason. `CustomerAddressChanged { oldAddress, newAddress, changedAt, reason }` gives the modeler exactly what SCD Type 2 needs.

**The practical tradeoff:** Event sourcing is architecturally ideal but operationally complex. The outbox pattern achieves ~80% of the benefits with ~20% of the effort ([Bemi, Rethinking Event Sourcing](https://blog.bemi.io/rethinking-event-sourcing/)).

### OpenLineage as Domain Events for the Pipeline

OpenLineage RunEvents (START, COMPLETE, FAIL) are domain events for the **data pipeline bounded context**. The data pipeline is its own domain with its own ubiquitous language (Jobs, Runs, Datasets, Facets).

A RunEvent(COMPLETE) is the pipeline domain's equivalent of "OrderCompleted" in the sales domain — something that happened that domain experts (data engineers) care about. OpenLineage effectively implements event sourcing for data infrastructure: the sequence of RunEvents constitutes the complete history of what transformations ran, producing what data.

A lineage catalog (Marquez, DataHub) is the read model / projection built from these events.

---

## 6. The Sequence

Given all of the above, the architecture-before-infrastructure sequence for data:

```
1. UNDERSTAND THE BUSINESS DOMAIN (DDD)
   │
   ├── What bounded contexts exist?
   │   → Sales, Marketing, Finance, Product, Support...
   │
   ├── What data system types does this business need?
   │   → Predict from Four Data System Types by business type
   │   → SaaS: 60% App, 25% Analytics, 10% Work, 5% Record
   │
   └── What ubiquitous language exists (or should)?
       → What does "customer" mean? "Revenue"? "Active user"?

2. MAP THE BOUNDARIES (Surface Analysis)
   │
   ├── Where does data cross boundaries?
   │   → Source systems → staging (ACL)
   │   → Between business domains (context maps)
   │   → Between data system types (OLTP → OLAP)
   │
   ├── What are the context mapping patterns?
   │   → Most CDC: Conformist (source doesn't care about analytics)
   │   → Need negotiation? Customer/Supplier
   │   → Need protection? ACL (staging layer)
   │   → Need shared concepts? Shared Kernel (conformed dimensions)
   │
   └── What is the provenance position of each source?
       → Boundary (where meaning enters) vs. Internal (derived)

3. DESIGN THE ARCHITECTURE
   │
   ├── Pipeline organization
   │   → Staging: by source system (upstream context)
   │   → Intermediate/Marts: by business domain (downstream context)
   │
   ├── Dimensional model
   │   → Aggregates → grain decisions
   │   → Bounded contexts → star schemas / marts
   │   → Shared kernel → conformed dimensions
   │
   ├── Integration patterns
   │   → ACL at staging layer (protect analytical model)
   │   → CDC vs. domain events (prefer outbox if possible)
   │   → Medallion tiers (Bronze = raw, Silver = ACL, Gold = domain model)
   │
   └── Semantic contracts
       → Metric definitions (ubiquitous language for data)
       → Data contracts at domain boundaries
       → Published Language via semantic layer

4. THEN PICK INFRASTRUCTURE
   │
   ├── The architecture above is infrastructure-agnostic
   │   → It works with DuckDB or Snowflake
   │   → It works with dbt or SQLMesh
   │   → It works with Postgres or Delta Lake
   │
   └── Infrastructure is a deployment detail
       → "I want BI" → Evidence, Metabase, Superset, Streamlit
       → "I want a pipeline" → dbt + DuckDB, SQLMesh, Hamilton
       → "I want lineage" → OpenLineage + Postgres + MCP server
       → "I want governance" → it's a discipline, not a product
```

### What This Gives the Generalist

The generalist doesn't need to know Spark internals or Snowflake optimization. They need to:

1. **Identify their bounded contexts** — what business domains exist?
2. **Predict their system mix** — what data systems should exist?
3. **Map their boundaries** — where does data cross domains?
4. **Design their pipeline stages** — staging (ACL) → intermediate (logic) → marts (domain model)
5. **Define their metrics** — ubiquitous language for analytical consumers
6. **Then pick tools** — and agents can handle the implementation

The architecture is the hard part. Infrastructure is configuration. Agents excel at configuration.

---

## 7. dbt: Independent Convergence on DDD Principles

dbt keeps appearing in every section of this document — staging as ACL, pipeline organization by bounded context, semantic layer as ubiquitous language, Mesh as context mapping. This isn't coincidence. dbt independently arrived at the same architectural principles as DDD, starting from the data engineering problem rather than the software architecture problem.

### What dbt Actually Is

dbt (data build tool) is a transformation framework created by Tristan Handy at Fishtown Analytics in 2016. The founding insight was that data teams had a **collaboration problem rooted in isolation**:

> "Analytics teams have a workflow problem today. Too often, analysts operate in isolation, and this creates suboptimal outcomes."
> — [Handy, Building a Mature Analytics Workflow (2016)](https://www.getdbt.com/blog/building-a-mature-analytics-workflow)

The innovation was not the technology but the **workflow philosophy**: apply software engineering practices — version control, testing, documentation, modularity, CI/CD — to the transformation layer, without requiring analysts to become software engineers. SQL remained the authoring language. The discipline came from the surrounding infrastructure.

The key reframe: **analytic code is an asset.**

> "Analytic code is an asset, just like the code that powers your website and application, and producing high-quality code requires being serious about version control."

Mechanically, dbt executes SQL `SELECT` statements against a warehouse, manages the dependency graph via `ref` and `source` functions, runs declarative tests, and generates documentation from code. It does NOT extract or load. It operates entirely on data that's already in the warehouse.

### Why dbt Aligns With SemOps and Provenance-First Thinking

The alignment is structural, not superficial. dbt independently implemented the same principles that DDD prescribes and that SemOps frames as "explicit architecture":

**1. Lineage as a Byproduct of Structure (Not an Afterthought)**

dbt's most philosophically important design decision: the `ref` function. Every model must explicitly declare its upstream dependencies. You cannot write a dbt model with hidden upstream dependencies. Lineage is structurally guaranteed, not optionally maintained.

> "Using the ref and source functions, data practitioners explicitly define model relationships within SQL, automatically creating lineage as a byproduct of normal development work."
> — [dbt Developer Hub](https://docs.getdbt.com/docs/build/ref)

> "Your DAG should be automatically inferred and created with your data transformation and pipelines."

This is the provenance-first design principle from SemOps, implemented at the tool level. Manual lineage documentation is notoriously stale. dbt makes it impossible to skip by deriving lineage from code structure. The DAG is the lineage graph.

**2. Semantic Layer as Ubiquitous Language Enforcement**

dbt's Semantic Layer (MetricFlow, GA at Coalesce 2023, open-sourced under Apache 2.0 at Coalesce 2025) enforces shared metric definitions at the infrastructure level. "Revenue" is not a word in a glossary — it is a code artifact with a precise, machine-verifiable definition:

```yaml
metrics:
  - name: revenue
    type: simple
    label: "Total Revenue"
    type_params:
      measure: total_revenue
    filter: |
      {{ Dimension('order__status') }} = 'completed'
```

If the definition changes in dbt, it changes everywhere simultaneously. There is no coordination problem, no drift, no version misalignment.

> "Metrics should not be probabilistic or depend on an LLM guessing each calculation. They should be deterministic."
> — [dbt Labs, Open Source MetricFlow](https://www.getdbt.com/blog/open-source-metricflow-governed-metrics)

This is Evans' Ubiquitous Language enforced by the build system. It's also the answer to the "five different revenues" problem that plagues every organization at scale — the metric proliferation that is, in DDD terms, a ubiquitous language breakdown.

**3. Staging Layer as Anti-Corruption Layer**

As established in [Section 3](-anti-corruption-layers--integration-patterns), dbt's staging layer meets Evans' definition of an ACL:

> "Source-conformed data is shaped by external systems out of our control, while business-conformed data is shaped by the needs, concepts, and definitions we create."

The staging layer translates foreign concepts (source system column names, data types, conventions) into the analytical domain's own ubiquitous language. dbt doesn't use the term "ACL" — they say "source-conformed → business-conformed." Same pattern, different vocabulary.

**4. dbt Mesh as Bounded Contexts**

dbt Mesh (multi-project architecture) implements DDD's bounded context pattern with notable precision:

| DDD Concept | dbt Mesh Equivalent |
|---|---|
| Bounded Context | dbt Project (with own ownership, language, rules) |
| Published Language / Open Host Service | Public models with enforced contracts |
| Internal domain model | Private/protected models |
| Context Map | Cross-project `ref` dependency graph |
| ACL at boundary | Contract enforcement at project boundary |
| Ubiquitous Language | MetricFlow semantic definitions |

Model access controls (`private` / `protected` / `public`) mirror software engineering's encapsulation pattern. Contracts define the guaranteed schema of public models — if the output doesn't conform, the build fails. Versioning provides migration paths for breaking changes. This is the DDD published language pattern made machine-enforced.

> "dbt Mesh and data mesh both share a single fundamental mission: bringing software engineering best practices to data engineering."
> — [dbt Developer Hub, Intro to dbt Mesh](https://docs.getdbt.com/best-practices/how-we-mesh/mesh-1-intro)

**5. Tests as Invariant Enforcement**

dbt's testing philosophy — declarative assertions that run in the pipeline — is the OLAP equivalent of DDD's aggregate invariants. In OLTP, invariants prevent invalid state transitions at runtime. In dbt, tests prevent invalid data from propagating downstream at build time.

> "Data tests are assertions you make about your models... Tests return a set of failing records — they seek to find violations of your stated assumptions."
> — [dbt Developer Hub, Data Tests](https://docs.getdbt.com/docs/build/data-tests)

Four built-in tests (`unique`, `not_null`, `accepted_values`, `relationships`) cover the most common data invariants. Custom tests extend to any SQL-expressible assertion. This is shift-left quality — catching violations during development, not discovering them when dashboards break.

**6. Documentation from Code (Not Alongside It)**

dbt generates documentation from YAML annotations co-located with model definitions, lineage derived from the DAG, test coverage, and source freshness metadata. Documentation is not a separate artifact maintained manually — it is derived from the code. This is the same principle as code-as-documentation in software engineering.

For provenance specifically: dbt Exposures extend lineage downstream to consumption. An exposure declares a downstream consumer (a BI dashboard, a notebook, a reverse-ETL sync), completing the lineage chain: **raw source → staged model → mart → Tableau dashboard**. The full provenance path is captured.

### dbt and OpenLineage: Complementary Lineage Models

dbt's native lineage is **structural** — inferred from code at parse time, scoped to the dbt project. OpenLineage is **runtime** — events emitted during execution, spanning multiple tools.

The integration bridge is `openlineage-dbt` (`dbt-ol`), which wraps dbt runs and emits OpenLineage events from dbt's output artifacts (`manifest.json`, `run_results.json`, `catalog.json`). This federates dbt's lineage into broader organizational lineage graphs spanning ingestion (Airflow, Spark), transformation (dbt), and consumption.

The two approaches are complementary:
- dbt gives you the structural/static lineage graph (what COULD flow)
- OpenLineage gives you the runtime execution lineage (what DID flow)
- Together they provide complete provenance: design-time structure + runtime evidence

### Why This Matters for the Toolkit

dbt is the **proof that the explicit enterprise approach works at scale**. It's the most widely adopted data transformation tool (hundreds of thousands of practitioners), and its success is built entirely on the principles this document describes: bounded contexts, ACLs, ubiquitous language, lineage-first design, tests as invariants, documentation from code.

For the generalist audience of : dbt is not an expensive enterprise tool. The core is open-source (Apache 2.0). MetricFlow is open-source. The project structure, testing, and documentation features are all available in dbt Core. dbt Cloud adds collaboration and enterprise features, but the architectural principles are free.

This makes dbt the natural "infrastructure choice" that comes *after* the architecture decisions in [Section 6](-the-sequence). You design bounded contexts, map boundaries, define semantic contracts — then dbt is one of the tools that implements that architecture.

### Key dbt Sources

- [Handy, Building a Mature Analytics Workflow (2016)](https://www.getdbt.com/blog/building-a-mature-analytics-workflow) — founding philosophy
- [What Is Analytics Engineering?](https://www.getdbt.com/blog/what-is-analytics-engineering) — role definition
- [The Analytics Development Lifecycle](https://www.getdbt.com/resources/the-analytics-development-lifecycle) — ADLC formalization
- [dbt Semantic Layer Introduction](https://www.getdbt.com/blog/semantic-layer-introduction) — metric proliferation problem
- [Open Source MetricFlow](https://www.getdbt.com/blog/open-source-metricflow-governed-metrics) — deterministic metrics
- [Open Semantic Interchange (OSI)](https://www.getdbt.com/blog/dbt-labs-affirms-commitment-to-open-semantic-interchange-by-open-sourcing-metricflow) — open standard for semantic interchange
- [Intro to dbt Mesh](https://docs.getdbt.com/best-practices/how-we-mesh/mesh-1-intro) — bounded context implementation
- [Model Contracts](https://docs.getdbt.com/docs/mesh/govern/model-contracts) — published language enforcement
- [dbt + OpenLineage Integration](https://openlineage.io/docs/integrations/dbt/) — lineage bridge

---

## Key Insights

### 1. The dbt Staging Layer Is an Anti-Corruption Layer

dbt says "source-conformed → business-conformed." Evans says "protect your model from the concepts on the other side." Same insight, different vocabulary. This connection is not made in the dbt documentation, but the structural equivalence is precise.

### 2. Kimball Aligns With DDD; Inmon Contradicts It

Kimball organizes by business process (≈ bounded context). Inmon organizes by enterprise entity (≈ single unified model). DDD says the unified model is dangerous. The modern layered approach uses Kimball at Gold, Data Vault at Silver, and the semantic layer for ubiquitous language — but that's a modeling decision, not an infrastructure one. A lakehouse without a semantic layer is just a monolith on object storage.

### 3. Lineage Replaces SSOT as the Trust Mechanism

The SSOT obsession drives the Inmon/MDM cycle: unify everything → anemic model → teams route around it → "we can't trust the warehouse" → buy new platform → repeat. DDD offers a different trust model: **trust through traceability, not through unification.** Multiple valid representations are fine — Sales' Customer, Finance's Customer, Support's Customer — as long as lineage lets you trace any value back to its boundary source, see what transformations applied, and understand which context's definition you're looking at. This reframes governance from "who owns THE definition" to "is the lineage complete." It also reframes the toolkit argument: expensive tools sell SSOT-as-product; the explicit enterprise alternative is lineage from open primitives.

### 4. CDC Is Conformist by Default

Most analytics systems are in a Conformist relationship with their source systems. The ACL (staging/transformation) is how analytics absorbs this dependency while protecting its own model. Upgrading to Published Language (schema registry, versioned events) is an organizational commitment.

### 5. Domain Events > CDC for Analytics Quality

CDC captures state changes; domain events capture business intent. What's lost in CDC: intent, vocabulary, aggregate context, invariants, causal chains. The outbox pattern bridges this. Event sourcing eliminates the gap entirely.

### 6. Gold Layers Should Be Bounded Contexts

When Databricks recommends "multiple Gold layers by business need," they're recommending bounded contexts. Each Gold schema should express its own domain's ubiquitous language with its own ownership.

### 7. The Semantic Layer Is Both OHS and ACL

From the warehouse side: Open Host Service + Published Language (formal interface for all consumers). From the consumer side: ACL (protects from warehouse schema changes). It's a genuine bounded context between warehouse and consumers.

### 8. OpenLineage Is Event Sourcing for Pipelines

OpenLineage RunEvents are domain events for the data pipeline bounded context. The event sequence is a complete history. A lineage catalog is a read-model projection.

### 9. Lineage-Over-SSOT Is Agent-Native

SSOT assumes a single observer who needs one canonical answer. Multi-agent systems have N observers, each with a bounded Context window operating in a different domain. SSOT forces agent coordination through a master system — polluting context windows, creating memory bottlenecks, and reproducing the Inmon failure cascade at the agent infrastructure level. Lineage dissolves this: each agent works with its domain's representation, governance is post-hoc traceability (agentic lineage records per-episode provenance of all five agent primitives), and the trust model is identical to data lineage — trace any output back to its boundary source. See [agent-primitives.md](agent-primitives.md) for the structural argument from the agent architecture side.

---

## Sources

### DDD Foundations
- Evans, Eric. *Domain-Driven Design*. Addison-Wesley, 2003.
- [Fowler, DDD_Aggregate](https://martinfowler.com/bliki/DDD_Aggregate.html)
- [Vernon, Model True Invariants in Consistency Boundaries](https://www.informit.com/articles/article.aspx?p=2020371&seqNum=2)
- [Fowler, BoundedContext](https://martinfowler.com/bliki/BoundedContext.html)
- [Azure Architecture Center, Anti-Corruption Layer Pattern](https://learn.microsoft.com/en-us/azure/architecture/patterns/anti-corruption-layer)

### Data Mesh + DDD
- [Dehghani, Monolithic Data Lake to Distributed Data Mesh](https://martinfowler.com/articles/data-monolith-to-mesh.html)
- [Dehghani, Data Mesh Principles and Logical Architecture](https://martinfowler.com/articles/data-mesh-principles.html)
- [Data Mesh Architecture](https://www.datamesh-architecture.com/)
- [Dehghani, Data Mesh: The Beginning, Revisited](https://www.nextdata.com/our-pov/data-mesh-the-beginning-revisited)

### dbt + Pipeline Organization
- [dbt Developer Hub, How We Structure Our dbt Projects](https://docs.getdbt.com/best-practices/how-we-structure/1-guide-overview)
- [dbt Developer Hub, Staging Models](https://docs.getdbt.com/best-practices/how-we-structure/2-staging)
- [dbt Developer Hub, Intermediate Models](https://docs.getdbt.com/best-practices/how-we-structure/3-intermediate)
- [dbt Developer Hub, Mart Models](https://docs.getdbt.com/best-practices/how-we-structure/4-marts)
- [dbt Developer Hub, Intro to dbt Mesh](https://docs.getdbt.com/best-practices/how-we-mesh/mesh-1-intro)
- [dbt Labs, Semantic Layer Introduction](https://www.getdbt.com/blog/semantic-layer-introduction)
- [dbt Labs, Universal Semantic Layer](https://www.getdbt.com/blog/universal-semantic-layer)
- [dbt Labs, Building a Kimball Dimensional Model with dbt](https://docs.getdbt.com/blog/kimball-dimensional-model)
- [Jordan, So You Want to Build a Data Mesh](https://jennajordan.me/blog/data-mesh-dbt)

### Dimensional Modeling + DDD
- [Kimball Group, Four-Step Design Process](https://www.kimballgroup.com/data-warehouse-business-intelligence-resources/kimball-techniques/dimensional-modeling-techniques/four-4-step-design-process/)
- [Kimball Group, Conformed Dimensions](https://www.kimballgroup.com/data-warehouse-business-intelligence-resources/kimball-techniques/dimensional-modeling-techniques/conformed-dimension/)
- [Holistics, Kimball's Dimensional Data Modeling](https://www.holistics.io/books/setup-analytics/kimball-s-dimensional-data-modeling/)
- [TDAN, Inmon vs Kimball](https://tdan.com/data-warehouse-design-inmon-versus-kimball/20300)
- [Asahara, Data Vault Methodology paired with DDD](https://eugeneasahara.com/2021/01/02/data-vault-methodology-and-domain-driven-design/)

### CDC, Domain Events, Event Sourcing
- [Verma, Domain Events vs. CDC](https://kislayverma.com/software-architecture/domain-events-versus-change-data-capture/)
- [Streaming Data, CDC Is Still an Anti-pattern](https://www.streamingdata.tech/p/change-data-capture-is-still-an-anti)
- [Debezium, DDD Aggregates via CDC-CQRS Pipeline](https://debezium.io/blog/2023/02/04/ddd-aggregates-via-cdc-cqrs-pipeline-using-kafka-and-debezium/)
- [Debezium, Event Sourcing vs. CDC](https://debezium.io/blog/2020/02/10/event-sourcing-vs-cdc/)
- [Debezium, Reliable Data Exchange with the Outbox Pattern](https://debezium.io/blog/2019/02/19/reliable-microservices-data-exchange-with-the-outbox-pattern/)
- [Fowler, Event Sourcing](https://martinfowler.com/eaaDev/EventSourcing.html)
- [Bemi, Rethinking Event Sourcing](https://blog.bemi.io/rethinking-event-sourcing/)

### Medallion Architecture
- [Azure Databricks, Medallion Lakehouse Architecture](https://learn.microsoft.com/en-us/azure/databricks/lakehouse/medallion)
- [Data Engineering Weekly, Revisiting Medallion Architecture](https://www.dataengineeringweekly.com/p/revisiting-medallion-architecture-760)

### OpenLineage
- [OpenLineage, Object Model](https://openlineage.io/docs/spec/object-model/)
- [ByteDoodle, Data Lineage Through OpenLineage Events](https://blog.bytedoodle.com/data-lineage-through-openlineage-events/)

### Context Mapping
- [Context Mapper, Customer/Supplier](https://contextmapper.org/docs/customer-supplier/)
- [Context Mapper, Conformist](https://contextmapper.org/docs/conformist/)
- [DDD Practitioners Guide, ACL](https://ddd-practitioners.com)
- [Codecentric, From Data Product to Data Architecture with DDD](https://www.codecentric.de/en/knowledge-hub/blog/when-business-meets-technology-from-data-product-to-data-architecture-with-domain-driven-design)

---

## Related Documents

### In semops-docs (Canonical Frameworks)
- `EXPLICIT_ARCHITECTURE/what-is-architecture.md` — Architecture ≠ infrastructure
- `EXPLICIT_ARCHITECTURE/domain-driven-design.md` — Core DDD concepts
- `EXPLICIT_ARCHITECTURE/ddd-acl-governance-aas.md` — ACL patterns
- `EXPLICIT_ARCHITECTURE/patterns-and-bounded-contexts.md` — Context mapping
- `STRATEGIC_DATA/data-systems-architecture-map.md` — DDD as connective tissue
- `STRATEGIC_DATA/data-system-classification.md` — System types as bounded contexts
- `STRATEGIC_DATA/surface-analysis.md` — Boundary analysis

### In data-pr (Implementation)
- `docs/research/lineage-standards.md` — OpenLineage, Marquez
- `docs/research/provenance-standards.md` — W3C PROV
- `docs/research/data-architecture-tools.md` — DataHub, OpenMetadata
