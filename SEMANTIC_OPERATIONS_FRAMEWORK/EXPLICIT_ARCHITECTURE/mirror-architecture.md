# Mirror Architecture

> Mirror Architecture is how SemOps engages with a company's systems, data, processes, and documents in a deep, structural way and implements solutions (including agents and AI) without modifying existing systems or disrupting operations.

---

## Introduction

Every company already has architecture: systems that run, data that flows, processes that execute, documents that govern. The problem is not that companies lack these things. The problem is that engaging with them structurally (understanding what exists, how it connects, and where the gaps are) traditionally requires either invasive access to production systems or months of manual discovery that produces static documentation.

Mirror Architecture solves this by standing up a parallel replica of a company's data architecture using schemas, data, and structured infrastructure as an operational sandbox. The replica is faithful to the original: same entity relationships, same boundary definitions, same integration contracts. But it runs independently. No production queries, no system modifications, no operational risk.

This makes a fundamentally different kind of engagement possible. Instead of analyzing a company from the outside (producing slide decks and recommendations) or modifying systems from the inside (requiring access, permissions, and change management), Mirror Architecture creates a third option: build a running, queryable representation of the company's architecture and operate against that. Agents can reason over real domain structures. Solutions can be prototyped against faithful replicas. Gap analysis compares what the architecture *should* look like against what it *actually* looks like, with both sides represented as data.

The mirror is not a report that gets filed. It is a living system that agents and humans operate against continuously: evaluating new problems, deriving solutions, measuring coherence, and projecting scale.

---

## The Core Insight

You do not need to touch a company's systems to deeply understand and operate within their architecture.

Traditional consulting and systems integration require one of two approaches: either work from the outside (interviews, documentation review, vendor demos) and produce abstract models, or work from the inside (system access, production queries, code review) and risk disruption. Both have fundamental limitations. Outside-in work produces artifacts that are disconnected from reality. Inside-out work requires trust, access, and coordination that slow everything down.

Mirror Architecture combines both into a single methodology. Start from the outside to build a reference model (what the architecture *should* look like based on industry, business model, and public signal). Then refine from the inside as real information becomes available (what the architecture *actually* looks like based on schemas, data profiles, and system access). The mirror is where these two views meet, and the divergences between them are the gap analysis.

But the mirror is not just a snapshot of current state. It is a layered workspace where reference, reality, and improvements coexist simultaneously.

Three properties define a mirror:

1. **Running replica, not abstract model.** The mirror is not a diagram or a document. It is a running system with real schemas, synthetic data, and queryable infrastructure. Agents can execute against it. Queries return results. Pipelines process data through bronze, silver, and gold layers.

2. **Structurally faithful and layered.** The mirror replicates the architectural decisions of the specific company: their entity definitions, their boundary placements, their integration patterns. But it also holds the reference model underneath and active improvements on top, all at the same time. Current state is one layer, not the whole picture.

3. **Non-disruptive.** The mirror runs alongside existing systems without touching them. No production queries, no schema modifications, no agent access to live data. Synthetic data stands in for real data until the engagement progresses to validated access.

---

## The Three Layers

The mirror is not a flat replica. It holds three views of the same architecture simultaneously, and the value comes from the comparison between them.

```text
┌─────────────────────────────────────────────────────┐
│  IMPROVEMENTS (what could be)                       │
│  Gap-filling capabilities, agent processes,         │
│  system replacements, new integrations, WIP         │
│  solutions, prescriptions under evaluation          │
├─────────────────────────────────────────────────────┤
│  REALITY (what actually is)                         │
│  Validated schemas, real data profiles,             │
│  confirmed systems, actual processes,               │
│  discovered dark data, observed gaps                │
├─────────────────────────────────────────────────────┤
│  REFERENCE (what should be)                         │
│  Predicted architecture from outside-in analysis,   │
│  industry patterns, business model classification,  │
│  DDD-derived bounded contexts, ideal processes      │
└─────────────────────────────────────────────────────┘
```

**Reference layer (bottom):** The predicted architecture. What this company's systems, data, and processes *should* look like given its industry, business model, and scale. Built from structured catalogs and industry frameworks before any system access. This layer persists throughout the engagement as the benchmark.

**Reality layer (middle):** The faithful replica. What the company's architecture *actually* looks like based on validated schemas, data profiles, confirmed systems, and observed processes. This layer is where outside-in predictions meet inside-out discovery. Divergences between reference and reality are the gap analysis.

**Improvements layer (top):** The active work. Solutions being prototyped, agent processes being designed, capabilities being filled, system replacements being evaluated, new integrations under development. This layer is where prescriptions live before they become deployments. Agents operate here: reasoning over reference and reality to derive, test, and refine improvements without touching production.

Critically, the improvements layer can hold **multiple competing versions** simultaneously. A gap in the reality layer (say, manual demand forecasting) might have three candidate solutions being explored in parallel: a lightweight script that automates the existing spreadsheet process, an agent-driven pipeline that integrates dark data signals, and a full system replacement. Each version has its own schemas, its own synthetic data, and its own coherence score against the reference layer. They can be compared, tested, and evaluated before any commitment is made.

Synthetic data runs through all three layers. The reference layer uses synthetic data to make the predicted architecture queryable and testable before any real data exists. The reality layer uses synthetic data alongside real data where access is partial or sensitive. The improvements layer uses synthetic data to simulate how proposed changes would behave at scale, under load, or with edge cases that the current system has not yet encountered.

All three layers are queryable at the same time. An agent evaluating a problem can see what the architecture *should* look like (reference), what it *actually* looks like (reality), and what is already being done about the gaps (improvements, possibly in multiple versions). A coherence score measures the distance between reference and reality. A prescription traces from a gap in the reality layer to a specific improvement in the top layer, grounded in the pattern expectations from the reference layer.

This is what makes the mirror a workspace, not a report. Reports capture a single point in time. The mirror holds the entire state of the engagement: where the company should be, where it is, and what is being done to close the distance, including multiple paths forward being evaluated in parallel.

---

## What Gets Mirrored

The mirror replicates *architectural decisions*, not entire systems. This is a critical distinction. You do not need a copy of the application source code, the UI, or the CI/CD pipeline. You need the structures that encode business meaning: what entities exist, how they relate, what constraints apply, and where boundaries fall.

**Mirror (architectural decisions):**

- Schemas and entity definitions (the shape of business data)
- API contracts and integration patterns (how systems communicate)
- Data relationships and foreign key structures (how entities connect)
- Pipeline manifests and transformation logic (how data flows between layers)
- Process definitions and state machines (how work progresses)
- Governance rules and ownership boundaries (who controls what)

**Ignore (implementation details):**

- Application source code (the mirror is about *what*, not *how*)
- User interfaces (presentation is not architecture)
- CI/CD pipelines (deployment is infrastructure, not domain)
- Stated processes not enforced in data (aspirational documentation that does not match reality)

This selectivity is what makes mirroring practical. A company with 50 systems does not need 50 replicated systems. It needs the schema definitions, integration contracts, and boundary decisions from those 50 systems represented as queryable data in a single, coherent architecture.

---

## The Infrastructure Model

The mirror is not a conceptual exercise. It is governed infrastructure with the same data management disciplines that mature data teams apply to warehouses and pipelines: metadata catalogs, quality contracts, lineage tracking, and medallion-layered processing.

### Three Metadata Layers

The mirror applies three metadata layers to every system it represents, whether internal or external. This is the same pattern that data platforms like DataHub use for pipeline governance, extended to architectural knowledge:

| Layer | Question | What it contains |
|-------|----------|-----------------|
| **Catalog** (declared) | What *should* be true? | Schema expectations, relationship declarations, quality thresholds, ownership, data contracts |
| **Telemetry** (observed) | What *actually happened*? | Profiling results, actual schemas, cardinalities, distributions, agent episodes, lineage events |
| **Computed** (derived) | How does reality compare to expectations? | Divergence reports, coherence scores, gap classification (Clean / Concerning / Blocking) |

The design principle: processes declare metadata (catalog) before emitting it (telemetry). Deterministic checks run before expensive reasoning. Coherence measurement starts with metadata validation (is the manifest complete and internally consistent?) and only escalates to LLM reasoning where metadata flags an issue. This is schema-on-write applied to knowledge: validate structure first, investigate values second.

### Corpus Architecture

Knowledge in the mirror is organized into corpus types, each with its own coherence question. What "correct" means depends on what kind of knowledge you are governing:

| Corpus | Content | Coherence Question |
|--------|---------|-------------------|
| `core_kb` | Product specs, brand guidelines, sizing standards, pricing rules | Internal consistency: do our definitions agree with each other? |
| `deployment` | ADRs, integration specs, migration decisions, episodes | Decision-architecture alignment: do our decisions follow our architecture? |
| `published` | Product listings, marketing copy, channel descriptions, support articles | Content quality: does customer-facing content accurately represent our products? |
| `research` | Competitor catalogs, vendor spec sheets, industry standards | Source alignment: are external references still current and correctly characterized? |

Corpus assignment is deterministic via path patterns, not manual classification. Routing rules map file paths to corpus types. The routing rules are themselves governed artifacts: versioned, schema'd, and auditable.

Content then moves through a **medallion architecture**:

- **Bronze.** Raw ingestion with minimal processing. The equivalent of landing raw data in a staging area.
- **Silver.** Entity extraction, deduplication, SKOS classification. LLM-assisted enrichment adds concept ownership, broader/narrower relationships, and content type. Cleaned, typed, relationally linked knowledge.
- **Gold.** Human-reviewed, promoted to stable corpus, with additional metadata enrichment. Production-quality, trusted knowledge artifacts.

### The Three-Agent Pipeline

The mirror is built and maintained by a three-agent pipeline, where each agent maps to a data management discipline:

```text
Agent 1: DISCOVER     Schema extraction + profiling → data contracts
  │                   (Data lineage + metadata management)
  │
Agent 2: POPULATE     Two paths:
  │                   a) Real data: extract → bronze/silver staging
  │                   b) Synthetic: reference model → schema-aware generation
  │                   (Data transformation + quality)
  │
Agent 3: VALIDATE     Joins, constraints, query shapes → divergence report
                      (Data quality + governance)
```

Discovery works in two complementary directions, both anchored to the reference model. Top-down analysis produces a predicted architecture (expected entities, relationships, domain boundaries) from industry patterns and public signal. Bottom-up profiling works from inside the real system, extracting actual schemas, cardinalities, and relationships. Where these two directions diverge is the diagnostic finding.

Population supports both real and synthetic data. Synthetic data is schema-aware: it preserves entity relationships, foreign key integrity, and statistical distributions. This means the mirror is queryable and testable even before real data access is granted, and synthetic data can stand in permanently where access is restricted or sensitive.

Validation compares declared expectations (catalog layer) against observed reality (telemetry layer) and classifies every gap. The output is the computed layer: divergence reports, coherence scores, and gap classification that feeds directly into the improvements layer of the mirror.

### Knowledge Access

The mirror exposes knowledge through three complementary access patterns, following a cost-escalation principle: start cheap and deterministic, escalate to inference only when structure cannot answer the question.

| Access Pattern | Store | Returns | Use Case |
|---------------|-------|---------|----------|
| **Catalog queries** | YAML manifests | Structural answers | "What exists? What implements what? What is the status?" |
| **Embedding search** | PostgreSQL + pgvector | Semantic similarity | "What does it say about X? Find passages about Y." |
| **Graph traversal** | Neo4j, Graphiti | Relationships, temporal state | "What is connected to X? What changed? What did we believe on date Y?" |

Most governance queries resolve from catalogs alone, never touching the embedding infrastructure. This is the same principle that makes the mirror economical: deterministic structure handles the common case, and expensive reasoning is reserved for the cases that actually need it.

### Governing Systems You Do Not Own

Traditional data management assumes you centralize data: extract it from source systems, load it into your warehouse, transform it, then govern it. Mirror Architecture inverts this. The external system stays where it is. SemOps builds a governed representation: schemas profiled, relationships mapped, contracts defined, and the gap between "what should exist" (reference model) and "what actually exists" (real system) measured and classified.

The governance layer wraps around the data without moving it. The metadata graph, the lineage tracking, the quality contracts can all be applied through manifests and agent-driven profiling to systems that remain external. This is data management at its purest: the value is in the metadata model, not in possessing the data itself.

### Mirror in Practice — Ridgeline Outdoor Co

[Ridgeline Outdoor Co.](https://github.com/semops-ai/ridgeline-demo) is a complete, open-source SemOps engagement demo for a fictional $35M D2C outdoor gear brand. The mirror directory shows the infrastructure model in concrete terms.

**The mirror directory structure maps directly to the three layers:**

```
mirror/
├── domain/                          # REFERENCE LAYER
│   ├── outside-in/                  # Predicted architecture
│   │   ├── business-model.yaml      # Revenue model, segments, metrics
│   │   ├── reference-model.yaml     # Predicted system landscape
│   │   ├── system-classification.yaml
│   │   ├── STRATEGIC_DDD.md         # 15 bounded contexts
│   │   └── UBIQUITOUS_LANGUAGE.md   # Shared vocabulary
│   ├── registry.yaml                # 142 capabilities across 15 contexts
│   └── patterns/                    # Process, analytics, domain patterns
│
├── infrastructure/                  # REALITY LAYER (source systems)
│   └── manifest.yaml                # 18 source systems with connection specs
│
├── data/                            # REALITY LAYER (medallion pipeline)
│   ├── manifest.yaml                # Mirror state, divergences, layer refs
│   ├── bronze/manifest.yaml         # Per-extraction contracts (raw)
│   ├── silver/manifest.yaml         # ACL transforms (conforming)
│   ├── gold/manifest.yaml           # Fact tables + metrics
│   └── gold/corpus/                 # Dark data: 7 vector collections
│       ├── routing.yaml             # Dark data → bounded context mapping
│       └── manifest.yaml            # 31K entities, 34K chunks
│
├── application/                     # IMPROVEMENTS LAYER
│   ├── scale-projection/
│   │   └── gaps-by-capability.yaml  # 147 capabilities: Clean/Concerning/Blocking
│   └── topology/
│       └── repos.yaml               # Recommended repo structure
│
└── data/governance/manifest.yaml    # Cross-layer governance rules
```

**Source system inventory** (`infrastructure/manifest.yaml`) declares every system with connection metadata. Ridgeline has 18 systems spanning commerce (Shopify Plus), PIM (Akeneo), fulfillment (ShipBob), returns (Loop), marketing (Klaviyo), support (Gorgias), and more. Each entry declares bounded contexts, API protocols, auth methods, and rate limits.

**Medallion layers** track data through progressive refinement. Bronze manifests declare raw extraction contracts per source (e.g., Loop Returns: ~33K returns/year, free-text reason flagged as dark data target). Silver manifests define ACL transforms that conform data across sources (e.g., `customer_unified` resolves identity across Shopify, Klaviyo, Gorgias, and Yotpo on email). Gold manifests declare fact tables and computed metrics ready for consumption.

**Dark data routing** (`gold/corpus/routing.yaml`) maps unstructured information to bounded contexts with acquisition pipelines. Seven dark data surfaces are identified: free-text return reasons, vendor spec sheets, product reviews, team SOPs, ambassador field notes, support tickets, and merchandising discussions. Each route declares the source system, extraction method, embedding target, and coherence threshold.

**Gap classification** (`scale-projection/gaps-by-capability.yaml`) shows the computed layer in action. Of 147 capabilities, 52% are Clean (proceed), 39% are Concerning (fix before or during generation), and 9% are Blocking (physical/judgment, needs better tooling). Product Development has zero clean capabilities. Subscription has seven out of seven clean. The gap report is the divergence between reference (what should be here) and reality (what is), classified for action.

**Divergence tracking** (`data/manifest.yaml`) explicitly records where the reference model was wrong. Five divergences documented: Marketing dissolved as a bounded context (absorbed into others), Akeneo adoption lower than predicted (60% vs 90%), wholesale still manual, and two others. Each divergence feeds back into the reference catalogs for future engagements.

---

## The Mirror as Operational Interface

Once populated, the mirror stops being an assessment artifact and becomes an operational system. The same agent commands that built the architecture now run against it continuously, operating across all three layers.

**Problem intake:** When a company brings a problem ("Can AI generate our product descriptions?"), it gets evaluated against the mirror architecture. The intake checks the reference layer for expected patterns and capabilities, the reality layer for what actually exists and what is missing, and the improvements layer for work already in progress. The answer is not "yes" or "no" but "here is exactly what exists, what is missing, and what it would take."

**Solution derivation:** Solutions are derived from the architecture, not invented in isolation. Every recommendation traces to bounded contexts, capabilities, and patterns in the mirror. Multiple solution versions can be prototyped in the improvements layer, each with their own synthetic data and coherence scores, and compared before any commitment to production changes.

**Coherence measurement:** The mirror enables continuous measurement of how well the company's reality matches its intended architecture. [Semantic Coherence](../SEMANTIC_OPTIMIZATION/semantic-optimization.md) scoring quantifies the distance between the reference and reality layers. Proposed improvements can be scored against the reference to predict whether they will close the gap. Drift is detected early, not discovered during incidents.

**Agent operations:** Agents operate against the mirror safely. They can reason over real domain structures, prototype solutions in the improvements layer, and validate approaches using synthetic data without any risk to production systems. The mirror is the sandbox where AI meets business architecture.

---

## ADR Alignment

| ADR | Decision | How reflected here |
|-----|----------|--------------------|
| [dx-hub ADR-0004](../decisions/ADR-0004-mirror-architecture.md) | Mirror Architecture framework: system-mirroring methodology for data consulting and AI integration | Core concept, three layers, infrastructure model |

Note: The original ADR-0004 constructs (8-level formalization continuum, independent axes, GAPS.md discipline, Mirror Test) have been absorbed into [Scale Projection](scale-projection.md). This document focuses on the mirror concept itself.

---

## Glossary

| Term | Definition |
|------|-----------|
| **Catalog Layer** | The declared metadata layer: schema expectations, relationship declarations, quality thresholds, ownership, data contracts. What *should* be true |
| **Computed Layer** | The derived metadata layer: divergence reports, coherence scores, gap classification. How reality compares to expectations |
| **Dark Data** | Unstructured or semi-structured information trapped in systems (free-text fields, Slack channels, PDFs, support tickets) that carries real business signal but is not currently queryable or connected to processes |
| **Gap Classification** | Categorization of divergences between reference and reality as Clean (infrastructure only), Concerning (logic changes needed), or Blocking (fundamental rethink required). See [Scale Projection](scale-projection.md) |
| **Improvements Layer** | The top layer of the mirror: active work including gap-filling capabilities, agent processes, system replacements, and WIP solutions. Can hold multiple competing versions simultaneously, each with its own schemas and synthetic data |
| **Medallion Architecture** | Staged data quality pattern (Bronze, Silver, Gold) applied to the mirror. Bronze is raw ingestion, Silver is entity extraction and cross-source conforming, Gold is consumption-ready fact tables and metrics |
| **Mirror** | A layered, running, non-disruptive workspace holding three simultaneous views of a company's architecture: reference (what should be), reality (what is), and improvements (what could be). Not an abstract model or diagram, but a queryable system that agents and humans operate against |
| **Non-disruptive** | The property of operating alongside existing systems without production queries, schema modifications, or agent access to live data |
| **Reality Layer** | The middle layer of the mirror: the faithful replica of current state, built from validated schemas, real data profiles, confirmed systems, and observed processes |
| **Reference Layer** | The bottom layer of the mirror: the predicted architecture from outside-in analysis. Persists throughout the engagement as the benchmark against which reality is measured |
| **Reference Model** | The predicted architecture of a company. Contains bounded contexts, entity definitions, system inventory, and confidence levels. Every prediction is a testable hypothesis |
| **Synthetic Data** | Schema-aware generated data that preserves entity relationships, foreign key integrity, and statistical distributions without using real customer or business data. Used across all three mirror layers: to make the reference queryable, to fill gaps in reality where access is partial, and to simulate proposed improvements |
| **Telemetry Layer** | The observed metadata layer: profiling results, actual schemas, cardinalities, distributions, agent episodes, lineage events. What *actually happened* |

---

## References

- [Domain Pattern: Mirror Architecture](../patterns/domain/mirror-architecture.md)
- [ADR-0002: System Landscape](../decisions/ADR-0002-system-landscape.md) — Bounded contexts, DDD approach
- [ADR-0003: Three-Tier Repo Workflow](../decisions/ADR-0003-three-tier-repo-workflow.md) — Private/public boundaries
- [Ridgeline Outdoor Co. Demo](https://github.com/semops-ai/ridgeline-demo) — Complete open-source mirror engagement example

---

## Version History

| Version | Date | Change | ADR |
|---------|------|--------|-----|
| 0.1.0 | 2026-01-22 | Initial extraction from semops-dx-orchestrator ADR-0004 | dx-hub ADR-0004 |
| 0.2.0 | 2026-03-29 | Extracted to design doc format; ADR-0004 revised note: Continuum/Axes absorbed into Scale Projection | dx-hub ADR-0004 |
| 0.3.0 | 2026-04-07 | Reframed formalization continuum for consulting (Level 2-3 + tool overlays). Added dbt project + observability to mirror structure and axes. Added Data tooling axis. Per semops-dx-orchestrator#294 (#284 mirror architecture alignment). | — |
| 0.4.0 | 2026-04-16 | Added conceptual framing: Introduction, Core Insight, Three Layers, What Gets Mirrored, Infrastructure Model, Mirror as Operational Interface. Replaced Engagement Model with Infrastructure Model (metadata layers, corpus architecture, three-agent pipeline, knowledge access, governing external systems). Added Ridgeline demo examples. Expanded glossary. | — |
