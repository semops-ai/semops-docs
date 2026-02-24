---
doc_type: hub
pattern: discovery-through-data
provenance: 1p
metadata:
 pattern_type: concept
 brand_strength: medium
---

# Discovery Through Data

> Observable data reveals organizational truth. Your business model, processes, customers, and operations are all represented in data structures and event logs—and therefore can be discovered, validated, and improved.

**Status:** 1p Concept | **Date:** 2026-01-20

---

## The Core Thesis

Organizations do not know themselves as well as they think. Strategy documents describe aspirations. Process diagrams show idealized flows. Policies state intentions. But the data tells the truth:

- **Data structures reveal actual relationships** between entities
- **Event logs reveal actual processes** as they execute
- **The gap between "should" and "does"** is measurable

This is not a problem to solve—it is an opportunity to exploit. The same data that exposes dysfunction can guide optimization.

---

## Two Lenses, One Truth

Understanding organizational truth requires two complementary perspectives:

| Lens | Question | Method |
|------|----------|--------|
| **Strategic (Model Everything)** | "What does this organization actually represent?" | Model data structures semantically |
| **Behavioral (Process Mining)** | "What does this organization actually do?" | Extract processes from event logs |

These are not separate activities. They are two views of the same underlying reality: **structure** (what exists) and **behavior** (how it operates).

---

## The Strategic Lens: Model Everything

The business model, customers, products, competitors, and organization are all represented as data structures in systems. [Domain-Driven Design](domain-driven-design.md) tells the organization to model itself and its architecture. This approach goes a level deeper: a tactical method for modeling the organization through examining actual data structures.

### Source-Up Thinking

**System-up thinking (wrong direction):**

- "How does the organization integrate Marketing's data with Product's data?"
- "How does the organization build a Customer 360 from CRM + analytics?"
- "How does the organization break down silos between teams?"

**Source-up thinking (right direction):**

- "What events are captured at the user interaction?"
- "Who owns defining what gets instrumented?"
- "What is the single source of truth for each event?"
- "How are consistent definitions ensured from the start?"

**The difference:**

- System-up → endless integration projects, drifting definitions, conflicting metrics
- Source-up → one instrumentation layer, consistent definitions by design

### Common Issues Revealed by Modeling

Root-cause analysis through data modeling reveals issues difficult to uncover any other way:

**Missing or implicit architecture:** Many organizations have no explicit data architecture—just accumulated systems. Modeling exposes what is actually there versus what people assume exists.

**Instrumentation ownership:** Product owns the codebase but does not care about analytics depth. Analytics wants data but cannot modify the source. Instrumentation becomes a political orphan.

**Unenforced data contracts:** Source systems change without notice. Downstream consumers build on assumptions that break. Nobody owns the relationship.

**Misaligned schemas and metrics:** When everything is modeled and similar-but-different metrics are found, most teams cannot justify why they need a different version. The reason: they did not want a dependency.

**Organizational splits mirror technical splits:** Each fork creates divergence. The further upstream the split, the worse it gets.

### Reference Architecture: "This Is You" vs "This Should Be You"

Given a company's industry, business model, and basic system details, a **reference architecture** can be generated—what their data landscape *should* look like based on known patterns:

| Domain | Reference Patterns |
|--------|-------------------|
| **Business model** | Revenue streams, cost structures, value chain |
| **Customer analytics** | Known surfaces, lifecycle stages, segmentation |
| **Transactional data** | Standard schemas for orders, invoices, inventory |
| **Product information** | PIM structures, catalog hierarchies |
| **Customer support** | Ticket schemas, resolution workflows |
| **Industry-specific** | Healthcare (claims), retail (POS), SaaS (usage) |

This creates two powerful comparisons:

1. **"This is you"** — What your actual data structures reveal about your organization
2. **"This should be you"** — What reference patterns suggest based on your business type

The gap between them is diagnostic. Missing expected structures indicate capability gaps. Unexpected structures indicate either innovation or technical debt. Misaligned structures indicate organizational dysfunction.

### Building a Reference Architecture Generator

This comparison can be operationalized as a tool. The pipeline:

```
INPUTS
├── Domain chunks (business models, industry patterns)
├── Known ERP/system patterns (SAP, Salesforce, Shopify, etc.)
├── Industry-specific schemas (healthcare claims, retail POS, SaaS usage)
└── Platform documentation (Databricks, Snowflake, AWS, etc.)
 │
 ▼
KNOWLEDGE BASE
├── Core business metrics by industry
├── ERP data patterns
├── Transaction data schemas
├── Standard transformation patterns
└── Platform-specific architectures
 │
 ▼
REFERENCE ARCHITECTURE
├── Expected data systems
├── Expected data flows
├── Expected schemas and metrics
├── Expected integration points
 │
 ▼
SIMULATION
├── Synthetic data generation
├── Transform execution
├── Behavior modeling
└── "What would this company's data look like?"
```

**The power:** Before touching a client's real systems, the system can:
- Generate what their architecture *should* look like
- Create synthetic data that behaves like theirs would
- Model transformations and data flows
- Compare the simulation to reality when access is obtained

This pairs with:
- **[Agentic Lineage](https://github.com/semops-ai/semops-data/issues/27)** — Discovers "what actually is" through event logs and lineage tracking
- **Stack Simulation** ([semops-data branch: 001-stack-simulation-lineage](https://github.com/semops-ai/semops-data/tree/001-stack-simulation-lineage)) — Provides the simulation infrastructure: synthetic data generation, dbt transforms on DuckDB, OpenLineage events to Marquez

Together, these provide both the "should be" reference architecture and the "actually is" discovery—plus the ability to simulate the gap before touching real systems.

### Principles

1. **Single Source of Truth at Capture** — Define events once with complete context
2. **Understand Analytics Patterns** — They share the same source; design instrumentation to serve all types
3. **Governance at the Source** — Security and compliance are source problems
4. **Consistent Definitions by Design** — Create shared definitions before systems enforce them
5. **Model Meaning Down** — Start with business meaning, model explicitly, then design systems

---

## The Behavioral Lens: Process Mining

Process mining discovers, analyzes, and improves processes by extracting knowledge from event logs. It sits at the intersection of data mining and business process management.

### The Three Pillars

| Type | Input | Output | Question |
|------|-------|--------|----------|
| **Discovery** | Event logs only | Process model ("as-is") | "What actually happens?" |
| **Conformance** | Event logs + reference model | Deviations, violations | "Does reality match design?" |
| **Enhancement** | Event logs + existing model | Improved model, bottleneck analysis | "How can we improve?" |

### Event Log Structure

The foundation of process mining is the event log:

| Attribute | Description | Example |
|-----------|-------------|---------|
| **Case ID** | Unique process instance | `order-12345` |
| **Activity** | What happened | `approve_order` |
| **Timestamp** | When it happened | `2025-12-10T14:32:00Z` |
| **Resource** | Who/what did it | `user:jane`, `system:api` |
| **Attributes** | Context | `amount: 5000`, `priority: high` |

Every digital system generates event logs. Those logs contain the truth about how work actually happens.

### Conformance Checking

Compare reality against expectation:

```text
Expected: entity → classify → validate → store
Actual: entity → store (skipped validation!)
```

**Conformance checking = auditing at the process level.**

This directly parallels [semantic coherence](../SEMANTIC_OPTIMIZATION/semantic-coherence.md) validation—comparing what should happen against what does happen.

---

## The Synthesis: Episode-Centric Provenance

When strategic modeling (what exists) is combined with behavioral mining (what happens), the result is **Episode-Centric Provenance**: a complete record of every operation, what context informed it, and how well it aligned with expectations.

### The Episode Model

Every significant operation is an **episode** within a bounded **run**:

```text
ingestion_run
│ id: uuid
│ started_at: timestamp
│ source: string
│ config: jsonb
│
└── episode (1:many)
 │
 ├── operation: enum (INGEST, CLASSIFY, VALIDATE, PUBLISH, ...)
 ├── target_type: enum (entity | pattern | delivery)
 ├── target_id: string
 │
 ├── context_patterns[]: string[] ← What patterns were retrieved
 ├── coherence_score: float ← How well did output align?
 │
 ├── model_info: jsonb ← Which model made the decision
 └── created_at: timestamp
```

This structure captures:

- **What happened** (operation, target)
- **What context informed it** (retrieved patterns)
- **How trustworthy is it** (coherence score)
- **Who/what decided** (model info)

### Three Types of Lineage

| Lineage Type | What It Tracks | Example |
|--------------|----------------|---------|
| **Data Lineage** | Dataset → Job → Dataset | OpenLineage-style flow |
| **Semantic Lineage** | Pattern → (SKOS) → Pattern | Concept relationships |
| **Agentic Lineage** | Episode → produced → Entity | What informed each decision |

**Agentic Lineage** is the key innovation—tracking not just data flow, but *what data/context informed each agent decision and why it should be trusted*.

---

## Coherence as Conformance

In process mining, conformance checking compares actual execution against expected models. In [semantic operations](../README.md), **coherence scoring** serves the same purpose: measuring how well outputs align with expected patterns.

### The Coherence Formula

```text
SC = (Availability × Consistency × Stability)^(1/3)
```

- **Availability** — Is it discoverable? (retrieval coverage)
- **Consistency** — Same meaning across contexts? (embedding cluster coherence)
- **Stability** — Low drift over time? (embedding comparison across windows)

### Per-Episode Coherence

```text
coherence_score = cosine_similarity(
 entity_embedding,
 mean(context_pattern_embeddings)
)
```

Low coherence indicates potential misclassification—the semantic equivalent of a process violation.

---

## Implementation: SemOps Ingestion v2

The [SemOps Ingestion v2 Architecture](https://github.com/semops-ai/semops-dx-orchestrator/blob/main/docs/GLOBAL_ARCHITECTURE.proposed.md) directly implements these concepts:

| Concept | Implementation |
|---------|---------------|
| Episode-centric provenance | `ingestion_run` + `ingestion_episode` tables |
| Process discovery | Lineage graph traversal (Neo4j) |
| Conformance checking | Coherence scoring per episode |
| Enhancement | Closed-loop optimization with analytics feedback |
| Bi-temporal tracking | Graphiti for stability measurement |

### The Closed Loop

```text
READINESS (SOKPI) SUCCESS (Analytics)
───────────────── ──────────────────
Knowledge Complete? ───► Engagement Metrics
Semantically Coherent? ───► Conversion Rate
Assumptions Valid? ───► Retention/Resonance
 ◄───
Drift Cost ◄─────── Feedback: Did it work?
```

Pattern effectiveness can be measured: "Content with pattern X converts at Y%." If coherence correlates with success, optimizing for coherence optimizes for outcomes.

---

## Practical Application

### Start with Concrete Data

Data is often discussed in generalities. But the best explanation is to look at the raw data itself, exactly in context.

If two numbers should match and do not:

- The source data is different
- The transformation is not doing the same thing
- Both

Either they ARE the same metric (technical problem) or they ARE NOT (semantic problem revealed by usage).

### Follow the Lineage

Gaps in lineage are a common culprit. When the origin of a number cannot be traced, it cannot be trusted.

### Profile Everything

Data profiling cuts through ambiguity:

- Bad schemas
- Faulty metric calculations
- Missing values
- Unexpected distributions

### Apply Standard Patterns

For every calculation, the question is: what does the organization do differently than what it is "supposed to" do?

**Synthetic data validates the pattern:** Model the standard pattern with fake data where the answer is known in advance—then self-validate the entire system.

---

## The Anti-Pattern: Ship Now, Analytics Later

"Let's just ship the feature and add analytics tracking later."

**Why this is catastrophic:**

1. **The critical moment is lost** — Cannot retroactively capture events that already happened
2. **Technical debt compounds** — Retrofitting is 10x harder than building it in
3. **Semantic drift is baked in** — Product ships with implicit semantics; analytics tries to reverse-engineer
4. **Compliance is impossible** — Cannot retroactively add PII protection

**The correct approach: Instrument during development.**

---

## Key Takeaways

### 1. Observable Data Reveals Truth

Structure and behavior are knowable through data. The gap between "should" and "does" is measurable and actionable.

### 2. Two Lenses, One Method

Strategic modeling (what exists) and process mining (what happens) are complementary views of the same reality. Combine them for complete understanding.

### 3. Episode-Centric Provenance

Track every operation with its context and quality signals. This enables audit, debugging, and optimization.

### 4. Coherence Is Conformance

Measuring semantic alignment serves the same purpose as process conformance checking—validating that reality matches expectations.

### 5. Source-Up Wins

Design instrumentation at the source. Retrofit integration never catches up.

---

## Related Links

### Theory

- [Semantic Coherence](../SEMANTIC_OPTIMIZATION/semantic-coherence.md) - The coherence measurement framework
- [Semantic Compression](../SEMANTIC_OPTIMIZATION/semantic-compression.md) - Process models as compressed behavior

### Implementation

- [SemOps Ingestion v2 Architecture](https://github.com/semops-ai/semops-dx-orchestrator/blob/main/docs/GLOBAL_ARCHITECTURE.proposed.md) - Episode-centric provenance implementation
- [Agentic Lineage Project](https://github.com/semops-ai/semops-data/issues/27) - Open source agent provenance system

### Problem Space

- [Silent Analytics Failure](../STRATEGIC_DATA/silent-analytics-failure.md) - When metrics mean nothing
- [Data Silos](../STRATEGIC_DATA/data-silos.md) - The real issue behind silos
- [Governance as Strategy](../STRATEGIC_DATA/governance-as-strategy.md) - Provenance as operational principle

### External Concepts

- [Domain Events (DDD)](https://www.innoq.com/en/blog/2019/01/domain-events-versus-event-sourcing/) - Raw material for mining
- [Event Sourcing](https://medium.com/swlh/event-sourcing-as-a-ddd-pattern-fea6de35fcca) - Complementary pattern
- [Process Mining - Wikipedia](https://en.wikipedia.org/wiki/Process_mining) - Overview and history
- [Process Mining 101 - Apromore](https://apromore.com/process-mining-101) - Practical introduction
