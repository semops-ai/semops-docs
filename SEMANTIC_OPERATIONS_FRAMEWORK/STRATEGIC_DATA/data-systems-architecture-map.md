---
doc_type: hub

pattern: data-systems-architecture-map

provenance: 1p

metadata:
    pattern_type: concept
    brand_strength: low
---

# Data Systems & Domain Architecture Map

> A relationship map connecting the Strategic Data frameworks (Four Data System Types, Data Engineering Core Framework, Surface Analysis) with Domain-Driven Design concepts (Bounded Context, Repository, Domain, Context Mapping).

**Why this matters:** These frameworks already describe the same reality from different angles. Making the connections explicit prevents redundant explanation, enables cross-referencing, and reveals that DDD is not a separate concern from data systems thinking — it is the structural glue that holds data system relationships together.

---

## The Three Strategic Data Frameworks

Three documents in Strategic Data operate at different zoom levels on the same subject:

| Document | Question It Answers | Zoom Level |
|---|---|---|
| **[Four Data System Types](four-data-system-types.md)** | "What kinds of data systems exist?" | Macro — the 4 categories |
| **[Data Engineering Core Framework](data-engineering-core-framework.md)** | "What's inside an Analytics Data System?" | Internal — architecture of one type |
| **[Surface Analysis](surface-analysis.md)** | "Where does data cross boundaries?" | Boundaries — interfaces between types |

```
Four Data System Types (the WHAT — 4 categories)
    │
    ├── Application Data ──┐
    ├── Analytics Data ────┤── Surface Analysis (the WHERE — boundaries between them)
    ├── Enterprise Work ───┤
    └── Enterprise Record ─┘
         │
         └── Analytics Data ── Data Engineering Core Framework (the HOW — internal architecture)
                               7 components, 5 lifecycle stages, 6 undercurrents
```

### Four Data System Types → Data Engineering Core Framework

Data Engineering Core Framework describes the internal architecture of the Analytics Data System type. The 7 components (ingestion, storage, table layer, compute, orchestration, catalog, ML/AI), 5 lifecycle stages, and 6 undercurrents are the anatomy of what Four Data System Types calls "the complete analytical data ecosystem." Data Engineering Core Framework is the deep dive into one of the four types.

### Four Data System Types → Surface Analysis

Surface Analysis describes where data crosses boundaries between the four system types. Each type has characteristic surface patterns:

| System Type | Surface Character | Provenance Position |
|---|---|---|
| **Application Data** | Product surfaces — web, mobile, API | External, boundary |
| **Analytics Data** | Query surfaces — dashboards, semantic layer | Internal, derived |
| **Enterprise Work** | Collaboration surfaces — Slack, docs, email | Internal, unstructured |
| **Enterprise Record** | Back-office surfaces — ERP, finance, HRIS | Internal, structured |

### Data Engineering Core Framework → Surface Analysis

The 7 components create the internal surfaces through which data flows within Analytics. The ingestion component is the boundary surface — where data enters the Analytics Data System from other system types. This is why Surface Analysis's "provenance position" (boundary vs internal) matters: ingestion is where semantic authority transfers from the operational domain to the analytical domain.

---

## DDD as Connective Tissue

Domain-Driven Design concepts are not a parallel framework — they are the structural relationships that connect these data system frameworks. Each DDD concept maps to a specific relationship between the three Strategic Data docs.

### Domain: The Business Reality

**DDD concept:** The subject area to which the user applies a program — the business problem being solved.

**How it connects:** The Domain is the business reality that all four system types represent differently. From [Data Silos](data-silos.md):

> "Application data and analytics data are the same data with different physics — same events, different constraints (latency, purpose, windowing, aggregation)."

The Domain doesn't live in any single system type. It manifests across all of them:

| System Type | How the Domain Manifests | Optimized For |
|---|---|---|
| **Application Data** | Current state, operational transactions | Low-latency reads/writes |
| **Analytics Data** | Historical patterns, dimensional analysis | Read-heavy aggregation |
| **Enterprise Work** | Unstructured knowledge about the domain | Human meaning capture |
| **Enterprise Record** | Canonical truth, constraint enforcement | Semantic integrity |

Same business events. Four different representations. Four different physics.

### Bounded Context: Each System Type Is One

**DDD concept:** An explicit boundary within which a particular domain model applies, with its own ubiquitous language.

**How it connects:** At the macro level, each of the Four Data System Types is a bounded context:

- Each has different semantic rules (normalized vs dimensional vs unstructured vs constrained)
- Each has different governance approaches ("don't unify" vs "model before ETL" vs "add scaffolding" vs "recognize as canonical")
- Each has its own "ubiquitous language" — an "order" means different things in OLTP vs OLAP vs the knowledge graph

This is why Four Data System Types says "don't try to unify everything" — the same principle as DDD's "respect bounded context boundaries."

Within Application Data Systems, there are further bounded contexts (microservices, domain services). But the four system types are the first-order boundaries.

### Repository: Application Data Only

**DDD concept:** A mechanism for encapsulating storage, retrieval, and search behavior, mediating between the domain model and the data mapping layer.

**How it connects:** The DDD Repository pattern applies specifically to Application Data Systems. It mediates between Aggregates and their OLTP persistence layer.

The other system types have fundamentally different data access patterns:

| System Type | Data Access Pattern | Why Not "Repository" |
|---|---|---|
| **Application Data** | DDD Repository (persists Aggregates via OLTP) | — this is where it applies |
| **Analytics Data** | Dimensional queries, semantic layer, OLAP engines | No aggregates to persist; read-optimized, not write-optimized |
| **Enterprise Work** | Search, retrieval, knowledge graph queries | No schema, no aggregates; AI extraction, ontologies |
| **Enterprise Record** | Ledger operations, constraint validation | Closest to Repository but with regulatory enforcement layer |

When someone says "repository" in a DDD context, they mean the Application Data System's transactional persistence — not a data warehouse, not a knowledge graph, not an ERP system.

### Context Mapping: Surface Analysis Generalized

**DDD concept:** Patterns describing relationships between bounded contexts — how they communicate, translate, and protect their models.

**How it connects:** Surface Analysis is DDD Context Mapping generalized beyond application boundaries to all data boundaries.

| DDD Context Mapping Pattern | Surface Analysis Equivalent |
|---|---|
| **Anti-Corruption Layer** | Boundary between system types — translation at the interface |
| **Published Language** | Shared schema/contract at a surface (e.g., CDC event format) |
| **Open Host Service** | API surface exposed to multiple consumers |
| **Shared Kernel** | Common schema shared between systems (rare, tightly coupled) |
| **Customer-Supplier** | Upstream/downstream relationship between surfaces |

Surface Analysis's **provenance position** maps directly to DDD upstream/downstream:

- **Boundary sources** = upstream contexts (where meaning originates, high semantic authority)
- **Internal sources** = downstream contexts (where meaning is derived, inherited authority)

### Aggregates and Domain Events: The Flow Between Types

**Aggregates** live in Application Data Systems. They are clusters of entities with enforced invariants, persisted via the Repository pattern.

**Domain Events** are how aggregates communicate what happened. When a domain event is captured via CDC and flows into the Analytics pipeline described by Data Engineering Core Framework, the same business event changes physics:

```
Aggregate (Application Data)
    │
    └── Domain Event
            │
            ├── Surface Analysis: this is a boundary source
            │                     for the Analytics Data System
            │
            └── CDC → Ingestion → Storage → Transform → Serving
                       │
                       └── Data Engineering Core Framework: this is the lifecycle
                           the event follows through Analytics
```

The DDD concept (Domain Event) becomes the Surface Analysis concept (boundary source) which enters the Data Engineering Core Framework pipeline (lifecycle stages). Three frameworks, one flow.

---

## The Unified View

```
Domain (the business reality — same events everywhere)
    │
    ├── Manifests in 4 System Types ─────────── four-data-system-types.md
    │   │
    │   ├── Application Data ── Bounded Contexts (DDD)
    │   │   │                   Repository pattern (data access)
    │   │   │                   OLTP query interface
    │   │   └── Aggregates, Domain Events, Services
    │   │
    │   ├── Analytics Data ──── data-engineering-core-framework.md
    │   │   │                   OLAP query interface
    │   │   └── 7 Components, 5 Lifecycle, 6 Undercurrents
    │   │
    │   ├── Enterprise Work ── SemOps / Knowledge Graphs
    │   │   └── Unstructured, needs semantic scaffolding
    │   │
    │   └── Enterprise Record ── Canonical truth
    │       └── Constraint enforcement, regulatory compliance
    │
    ├── Boundaries between types ─── surface-analysis.md
    │   ├── Sources (technical tuples)
    │   ├── Surfaces (sources in lineage context)
    │   ├── Provenance position → DDD upstream/downstream
    │   └── Surface Topology (strategic map)
    │
    └── Integration patterns ──────── DDD Context Mapping
        ├── ACL, Published Language, Open Host Service
        ├── CDC: Application → Analytics (domain events changing physics)
        └── AI extraction: Work → Knowledge Graph
```

---

## Cross-Reference Gaps

These relationships exist but are not yet explicit in the source documents:

### four-data-system-types.md

- Should reference Data Engineering Core Framework as "the internal architecture of the Analytics Data System"
- Should name Bounded Context as the DDD pattern that explains why each type has different semantic rules and governance

### data-engineering-core-framework.md

- Should reference Surface Analysis — the ingestion component is the boundary surface for Analytics
- Should note that CDC/ETL ingestion represents DDD Domain Events crossing from Application Data into Analytics
- Should reference Four Data System Types to position itself as "the anatomy of one type"

### surface-analysis.md

- Should name DDD Context Mapping as the pattern it generalizes
- Should connect provenance position to DDD upstream/downstream context relationships

### what-is-architecture.md

- Should reference Four Data System Types as concrete examples of bounded contexts at the data layer
- The "DDD is the foundation" argument would be strengthened by showing how the four system types are bounded contexts that DDD governs

---

## Related Links

### Strategic Data Frameworks

- [Four Data System Types](four-data-system-types.md) - Macro categorization of data systems
- [Data Engineering Core Framework Data Systems](data-engineering-core-framework.md) - Analytics internal architecture (Reis & Housley)
- [Surface Analysis](surface-analysis.md) - Boundary and interface analysis
- [Data Silos](data-silos.md) - "Same events, different physics"
- [Strategic Data](README.md) - Parent pillar

### Domain-Driven Design

- [What Is Architecture?](../EXPLICIT_ARCHITECTURE/what-is-architecture.md) - DDD as architectural foundation
- [Domain-Driven Design](../EXPLICIT_ARCHITECTURE/domain-driven-design.md) - Core DDD concepts
- [Anti-Corruption Layers](../EXPLICIT_ARCHITECTURE/ddd-acl-governance-aas.md) - Boundary integration patterns
- [Scale Projection](../EXPLICIT_ARCHITECTURE/scale-projection.md) - DDD concepts at SaaS scale
