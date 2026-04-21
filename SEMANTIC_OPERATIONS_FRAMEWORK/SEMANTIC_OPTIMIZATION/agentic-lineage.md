# Agentic Lineage

> **Agentic Lineage** extends data lineage to track AI agent interactions as first-class provenance events in the knowledge graph. It is the telemetry foundation for [Semantic Coherence](semantic-coherence.md) measurement: without structured records of what agents did, what context informed their decisions, and what artifacts they produced, scoring algorithms have nothing to score.

---

## The Observability Gap

Agent observability frameworks like Langfuse and LangSmith capture **what the agent did**: LLM calls, tool use, token counts, latency, cost. OpenTelemetry captures **how the system performed**: latency, errors, throughput. OpenLineage captures **how data flowed** through pipelines: dataset A to job B to dataset C. None of them answer the question that matters most for trust: "What data and context informed this agent output, and why should I trust it?"

| Standard | Question It Answers |
| -------- | ------------------- |
| OpenTelemetry | "How did the system perform?" (latency, errors, throughput) |
| OpenLineage | "How did the data flow?" (dataset A to job B to dataset C) |
| Langfuse / LangSmith | "What did the agent do?" (LLM calls, tool use, traces) |
| **Agentic Lineage** | "What informed this output and why should I trust it?" |

The gap is **provenance with trust signals**. Langfuse tells you the agent called a SQL tool and got 47 rows. It does not tell you whether those 47 rows were the right data, whether the agent's interpretation was coherent with the domain model, or whether the same query last week would have produced a different answer.

These frameworks also treat agent telemetry as special operational data, separate from the rest of the system. Separate dashboards, separate storage, separate query languages. Agent operations produce entities, create relationships, and make decisions that affect the knowledge base. That data should follow the same governance rules as everything else: same entity model, same lineage edges, same coherence measurement, same audit layers. An agent classification episode is no different from a pipeline ingestion run. Both consume inputs, apply transformations, and produce outputs that need provenance.

---

## Core Concepts

### Runs and Episodes

All lineage flows through one event structure. Whether it is batch ingestion processing markdown files, an architecture sync checking pattern traceability, or a multi-turn agentic synthesis, they all emit the same **run** + **episode** records.

A **run** is a bounded execution driven by a manifest: an ingestion batch, a slash command, a scheduled job. It carries constant dimensions that apply to every operation within the execution (source, corpus, agent, model).

An **episode** is one meaningful operation within a run. It is the unit of lineage. Episodes carry per-target data: what was examined, what was found, what score. Most context (corpus, source, agent, model) is inherited from the run. The episode is lightweight by design.

Episodes are entities in the corpus, queryable by the same agents that produce them. A corpus query can find prior episodes ("what happened last time we audited this repo?"). An agent running architecture sync can find the previous sync episodes and compare findings. The lineage is self-referential: an agent query that finds episodes itself emits an episode.

### Manifests

Every automated process reads a **manifest**: a source config YAML, a slash command definition, a cron schedule. The episode inherits its dimensional context from the manifest, not from per-event computation.

```
Manifest (source config YAML / command definition)
    |
    +-- defines: source_id, corpus, content_type, lifecycle_stage,
                 attribution, classification config
         |
         +-- Run (reads manifest, snapshots it)
              |
              +-- Episode 1 (inherits corpus, source from run)
              +-- Episode 2
              +-- Episode N
```

The run snapshots the manifest at execution time. This means you can always answer: "what config was active when this episode was recorded?"

### The Granularity Principle

Emit an episode when something worth finding later happened. Not for every operation that succeeded normally.

For a batch ingestion of 200 files: 1 run record and approximately 200 entity-level episodes (one per file, with classification, chunking, and embedding as fields on the episode, not separate episodes). Additional episodes only for errors or surprising findings.

Sub-operations (classify, chunk, embed) are metadata on the entity episode, not separate episodes, unless one independently fails or produces a surprising finding. The test: would a future agent want to find this specific event? If yes, it is an episode. If it is just a log line, capture it as metadata on the parent episode.

---

## From Data Lineage to Decision Lineage

DataHub was identified as the closest existing system to what agentic lineage needs because it already solves the **data governance as infrastructure** problem. Agentic lineage is fundamentally the same problem extended to agents.

DataHub receives events about what happened in a pipeline. It stores entities (datasets, jobs, runs) as first-class objects with identity. It stores relationships as lineage edges connecting entities into a queryable graph. It applies governance through tags, glossary terms, ownership, and domains. It exposes an API so systems can query it programmatically.

That is 80% of agentic lineage. The entity/edge model maps directly to Domain-Driven Design (entities have identity, relationships have typed predicates). The governance model is the same concern as coherence measurement, solved with manual human tagging instead of automated scoring. The OpenLineage event model is already the right abstraction for "something happened, here's what was consumed and produced."

The key insight: **DataHub treats metadata as a first-class product, not a byproduct of pipelines.** That is the same philosophy as governance as strategy: metadata is not compliance overhead, it is competitive advantage.

### What Changes for Agents

DataHub tracks data lineage: dataset A to job B to dataset C. Agentic lineage tracks **decision lineage**: context loaded, agent reasoning, artifact produced. The structural pattern is identical (a directed acyclic graph of inputs to transformation to outputs), but the semantics change.

| DataHub Concept | Agentic Lineage Equivalent | What Changes |
| --------------- | -------------------------- | ------------ |
| Dataset | Context snapshot (documents, tools, instructions) | Input is a context window, not a table |
| Job | Agent operation (classification, RAG query, synthesis) | Transformation includes reasoning, not just SQL |
| Run | Episode (bounded agent interaction) | Captures reasoning strategy, not just start/end |
| Facets (metadata) | Trust signals (coherence score, confidence, grounding) | Quality is measured, not manually tagged |
| Tags/Glossary | SC = (A x C x S)^(1/3) | Governance by measurement, not by manual annotation |
| Lineage edge | Decision trail + provenance edge | Edges carry "why" (reasoning), not just "what" (data flow) |

The innovation is not architectural. It is semantic. The same event-entity-edge-governance stack, extended with episode-centric provenance, bi-temporal tracking, coherence scoring, and an agent-first interface (MCP) where agents query the catalog rather than humans browsing a UI.

---

## Three Storage Layers

Each storage layer answers a different class of question. All three are needed for the full Semantic Coherence measurement model.

| Layer | Storage | Query Type | SC Dimension |
|-------|---------|-----------|-------------|
| **PostgreSQL** (runs + episodes) | Facts + dimensions | "What happened? How much? How often?" | Availability + Consistency |
| **Neo4j** (graph) | Current relationships | "What is connected? What path exists?" | Consistency (structural) |
| **Graphiti** (temporal graph) | Historical relationships | "What did we believe on date X?" | Stability |

**PostgreSQL** is the fact store. Episodes accumulate here. Metric queries (AVG, GROUP BY, JOIN) run here. This is where corpus coherence scores, drift reports, error rates, and throughput metrics come from.

**Neo4j** is the relationship query layer. When architecture sync checks pattern to capability to infrastructure traceability, that is a graph traversal, not a SQL join across episodes. When intake asks "what patterns are related to this concept?", that is a graph query. Neo4j always reflects current state.

**Graphiti** adds the temporal dimension that neither PostgreSQL episodes nor Neo4j provide. Episodes record what happened *at* a point in time. Neo4j records what the state *is*. Graphiti records what the state *was*. When a pattern's `derives_from` changes, Graphiti preserves both old and new state with `valid_time` ranges. This enables: "what did we believe about pattern X on February 1st?" That is exactly what Stability measurement needs. You cannot get that from episode timestamps alone, because episodes record operations, not knowledge state.

### Bi-Temporal Model

Graphiti provides two time dimensions:

- **valid_time**: when was this fact true in the real world?
- **transaction_time**: when did the system record this fact?

This distinction matters because knowledge changes are not always discovered when they happen. A pattern's meaning might drift over weeks before anyone notices. The bi-temporal model preserves this distinction, enabling both "when did reality change?" and "when did we find out?" queries.

---

## Architecture Lineage

Architecture lineage is a first-class concept parallel to pipeline lineage. Where pipeline lineage tracks how assets are built (source to bronze to silver to gold), architecture lineage tracks **what to build and why** (goals to patterns to capabilities to data requirements).

| Lineage Type | Tracks | Example |
| ------------ | ------ | ------- |
| **Pipeline lineage** | How an asset was produced | "vendor-spec.pdf to preprocessed to chunked to embedded to stored in Qdrant" |
| **Architecture lineage** | Why an asset should exist and what it connects to | "understand vendor quality to quality-assurance pattern to product-integrity capability to vendor spec data to dark data gap" |

Architecture lineage introduces two node types to the lineage graph.

### Concepts

Domain concepts (patterns, capabilities, data requirements) that participate in traversal. When the architecture model says "the quality-assurance pattern requires vendor specification data," that is an architecture lineage edge from the pattern to a data requirement. Concepts are not episodes. They are the things episodes act upon.

### Dark Data

Data that should exist per the architecture model but does not exist in any system, or exists but is not captured or digitized. Dark data is the output of architecture lineage traversal: the gaps between what the model requires and what is materialized. Dark data entries have a lifecycle: identified, planned, materialized (at which point they become regular data with pipeline lineage).

Architecture lineage traversal answers "given a goal, what data do I need, and what is missing?" The traversal follows typed edges through the domain model and categorizes dependencies:

| Category | Meaning | Action |
| -------- | ------- | ------ |
| **Declared/available** | Data requirement exists and is materialized | No action. Consume it |
| **Declared/not-acquired** | Data requirement exists but not yet in the system | Acquire (e.g., Airbyte extraction, API call) |
| **Derived/nonexistent** | Data requirement must be created by cross-referencing existing assets | Derive (new corpus creation from existing data) |

---

## The Plan Entity

The **plan** bridges architecture lineage (why) to pipeline execution (how). A plan is the output of architecture lineage traversal. It records what triggered the traversal, what dependencies were identified, and what materialization is needed.

Plans are first-class entities, not episode metadata. They have identity, lifecycle (planned, in progress, complete, abandoned), and a link back to the run that produced them. When a pipeline run executes a materialization, it links back to the plan that triggered it. This closes the loop: architecture lineage produces a plan, pipeline execution fulfills the plan, and the plan records both the reasoning (via episodes) and the outcome.

Traversal infrastructure (typed edges, traversal mechanism, dependency status) is independent of goal entity modeling. The plan entity bridges traversal to telemetry. The plan records whatever triggered the traversal, without requiring the system to have a fully resolved goal model.

---

## Semantic Ingestion as Dual-Output Pattern

Semantic ingestion is ingestion that produces dual outputs:

1. **Pipeline lineage output**: corpus assets (SQL entities, vector embeddings, graph edges)
2. **Architecture lineage output**: domain model updates (concept discovered, confirmed, or contradicted; new edges; dark data identified)

Every ingestion episode is simultaneously a pipeline lineage event and an architecture lineage event. An episode that processes a vendor spec PDF produces pipeline lineage (chunks and embeddings stored) and architecture lineage (new concept "Alpine Textiles pilling defect" discovered, contradicts existing "vendor quality A-grade" assertion). Both outputs flow through the same instrumentation. The distinction is in how consumers interpret the episode.

---

## Compute: Processing Episodes into Analytics

Generating events (emitting episodes) is one workstream. Processing those episodes into actionable analytics is the other.

### Three Compute Tiers

| Tier | Type | Trigger | Storage Layer | Example |
|------|------|---------|--------------|---------|
| **Automated** | Scheduled metrics | Cron / CI | PostgreSQL (episodes) | Corpus coherence, staleness, throughput |
| **Enforced** | Graph constraint checks | PR / deploy / slash command | Neo4j (graph rules) | Pattern to capability traceability, orphan detection |
| **Investigative** | Ad-hoc agent analysis | Human curiosity / score drop | All three layers | Concept drift, definition inconsistency, temporal regression |

All three tiers emit episodes. All three are queryable. The difference is who triggers them and how structured the question is.

**Automated metrics** are scheduled jobs that compute aggregate measures from the episode fact table. Corpus coherence over time, document drift per entity, staleness (entities with the oldest last-episode), source reliability (error rate by source), operation throughput. These run on a cadence and produce time-series data for trend detection.

**Enforced constraints** are graph queries that check architectural rules. Pattern to capability traceability must exist. Every capability must have a registered pattern. Every repo must have declared capabilities. These are pass/fail checks, not scores. But they emit audit episodes with findings, so they feed back into the automated metrics (e.g., "constraint violation rate over time").

**Investigative analysis** is ad-hoc, agent-driven, and triggered by a human asking a question, a score drop, or a finding from the other two tiers. Has the definition of "episode" drifted across documents? When did a `derives_from` relationship change, and did downstream entities update? Investigations use all three storage layers depending on what the question requires. They also emit episodes. An agent that investigates concept drift produces findings, and those findings are episodes in the fact table. Next time someone asks the same question, the prior investigation is findable.

### Progressive Investigation

The tiers compose into a progressive investigation chain. Each layer identifies where to look next:

```
Tier 1: Automated metric detects anomaly
    |  "core_kb coherence dropped 4%"
    v
Tier 2: Constraint check localizes
    |  "3 capabilities lost their pattern traceability after Feb 15"
    v
Tier 3: Investigation diagnoses
    |  "Pattern 'ddd' was updated Feb 14 (Graphiti). derives_from
    |   changed. Downstream capabilities not yet realigned (Neo4j)"
    v
Action: Fix or flag
       "Update capability mappings, or flag for human review"
```

This follows the same pattern as observability in production systems: metrics to alerts to traces to root cause.

---

## Manual Lineage as Bootstrapping

Before building automated telemetry, you can bootstrap provenance through manual documentation rituals: session notes, ADR updates, architecture docs, index files. This builds the discipline of recording what happened, why, and what changed.

Manual lineage is scaffolding. It works until the system outgrows it:

- **It is lossy.** Not everything gets recorded. What context was loaded? What was ignored?
- **It is inconsistent.** Different sessions capture different levels of detail.
- **It is not queryable.** Session notes are markdown, not structured data.
- **It does not support measurement.** You cannot compute corpus coherence from ADRs, detect cross-corpus drift from session notes, or answer "what did we believe on February 1st?" from documents that only show current state.

The transition from manual to automated follows three phases:

1. **Dual-write.** Skills continue to update docs and emit lineage episodes. Both exist. This validates that episodes capture the same information as manual docs.
2. **Episode-primary.** Session notes become views over episode data rather than manually authored documents. The episode record is the source of truth; a session note is a generated report from episodes tagged to that issue.
3. **Episode-only.** Manual doc-update rituals are removed from skill definitions. Episodes are the sole audit trail. Markdown reports are generated on demand from episode queries, not maintained manually.

Some documents do not go away. Architecture docs capture desired state (episodes capture actual state; the gap between them is a coherence signal). ADRs capture decision rationale. CLAUDE.md files are the compression layer for agent instructions. Pattern registries remain the source of truth. All of these persist alongside automated lineage.

---

## Architectural Decisions

Two ADRs inform the design of agentic lineage infrastructure:

**Co-Equal Aggregates (semops-hub ADR-0012)** establishes Pattern and Coherence as co-equal core aggregates in the domain model. Lineage infrastructure serves the Coherence aggregate by providing the telemetry data that coherence scoring consumes. Without lineage, the Coherence aggregate has no inputs.

**Coherence Measurement Model (semops-hub ADR-0014)** defines the goal-driven coherence model with Availability, Consistency, and Stability dimensions. The three storage layers map directly to these three SC dimensions: PostgreSQL for Availability and Consistency metrics, Neo4j for structural Consistency (graph constraints), Graphiti for Stability (temporal state queries).

---

## Related Concepts

- [Semantic Coherence](semantic-coherence.md): the measurement framework that agentic lineage provides telemetry for
- [Semantic Patterns](semantic-patterns.md): the entities that lineage edges connect to and that architecture lineage traverses
- [Pattern Operations](pattern-operations.md): the lifecycle operations that produce lineage events
- [Governance as Strategy](governance-as-strategy.md): the principle that governance (including lineage) is competitive advantage, not compliance overhead
