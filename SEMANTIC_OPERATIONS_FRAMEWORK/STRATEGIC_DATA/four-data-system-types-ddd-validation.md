---
doc_type: hub

pattern: four-data-system-types-ddd-validation

provenance: 1p

metadata:
 pattern_type: validation
 brand_strength: low
---

# Four Data System Types: DDD Validation

> Validating the [Four Data System Types](four-data-system-types.md) framework against Domain-Driven Design research. The core finding: DDD concepts map **differently** to each type — different patterns apply, different governance is needed, different integration patterns emerge. Arbitrary categories wouldn't produce this. The four types carve reality at genuine architectural joints.

**Why it matters:**
The Four Data System Types framework claims that every organization's data landscape decomposes into four bounded contexts (Application, Analytics, Work, Record) — each with distinct physics, governance, and integration patterns. This document presents the evidence from DDD research that supports, refines, or challenges that claim.

**Source research:** [Data Architecture Through the DDD Lens](https://github.com/semops-ai/semops-data/blob/main/docs/research/ddd-data-architecture.md) 

---

## The Core Test

If the four types are genuine bounded contexts, DDD concepts should map **differently** to each type. If they're arbitrary categories, DDD would map uniformly.

**Result: DDD maps differently to each type.**

| System Type | DDD Concepts That Apply | DDD Concepts That Don't Apply |
|---|---|---|
| **Application Data** | Repository, Aggregate, Domain Event, Bounded Context | — (DDD's home turf) |
| **Analytics Data** | Bounded Context, ACL, Ubiquitous Language, Context Map | Repository (no aggregates to persist) |
| **Enterprise Record** | Invariant enforcement, constraint validation | Repository (ledger operations, not entity persistence) |
| **Enterprise Work** | Minimal | Almost all (pre-model — needs scaffolding before DDD applies) |

Each type requires different architectural patterns from the same toolkit. This is the definition of a bounded context.

---

## What the Research Confirms

### 1. "Same Events, Different Physics" — Mechanism Identified

The four-types doc claims Application and Analytics represent "the same data with different physics." The DDD research identifies the specific mechanism: when an `OrderPlaced` domain event becomes a CDC row change, **five things are lost**:

| Lost in Translation | What It Means |
|---|---|
| **Business intent** | The "why" behind the state change |
| **Event vocabulary** | The ubiquitous language of the source domain |
| **Aggregate boundary** | CDC exposes individual table mutations; the aggregate boundary is invisible |
| **Invariant context** | CDC cannot tell you which business rule was satisfied |
| **Causal chain** | CDC captures isolated changes; domain events carry correlation IDs |

The physics genuinely differ — not just in query optimization, but in semantic preservation. The four-types framework correctly identifies this as a fundamental boundary, not a deployment convenience.

**Source:** [Domain Events → Event-Driven Data Architecture](https://github.com/semops-ai/semops-data/blob/main/docs/research/ddd-data-architecture.-domain-events--event-driven-data-architecture)

### 2. "Don't Try to Unify" — SSOT Failure Cascade Documented

The four-types framework says: respect bounded contexts, don't unify across types. The DDD research documents the specific failure cascade when this principle is violated:

```
"We need a Single Source of Truth"
 → Normalize everything into one model (Inmon / MDM)
 → Force incompatible domain concepts into shared namespace
 → Anemic model that serves no context well
 → Teams route around it (shadow analytics, local spreadsheets)
 → "We can't trust the warehouse"
 → Buy new platform / start Data Mesh initiative
 → "We need a Single Source of Truth" (cycle repeats)
```

**The alternative trust model:** Lineage replaces SSOT. Multiple valid representations are fine — Sales' Customer, Finance's Customer, Support's Customer — as long as lineage lets you trace any value back to its boundary source and understand which context's definition you're looking at.

This reframes the four-types integration principle from "don't unify" (negative) to "trust through traceability" (positive).

**Source:** [Lineage Replaces SSOT](https://github.com/semops-ai/semops-data/blob/main/docs/research/ddd-data-architecture.md#lineage-replaces-ssot)

### 3. Integration Patterns per Type — Confirmed by Context Mapping

The four-types framework assigns each type a distinct integration challenge and pattern. DDD context mapping confirms each one:

| System Type | Claimed Pattern | DDD Confirmation |
|---|---|---|
| **Application** | CDC + dimensional modeling | CDC is Conformist by default — analytics has no negotiating power with source. The staging-as-ACL pattern handles translation. |
| **Analytics** | Direct integration | Already operates within the analytical bounded context |
| **Work** | AI extraction + ontologies | DDD has no pattern for pre-model data — this is genuinely distinct territory |
| **Record** | Anti-corruption layers | Record system rigidity means ACLs absorb the translation cost |

These patterns weren't derived from the four-types framework — they fell out independently from the DDD context mapping analysis.

**Source:** [Context Maps → Data Flow Architecture](https://github.com/semops-ai/semops-data/blob/main/docs/research/ddd-data-architecture.-context-maps--data-flow-architecture)

### 4. Kimball Validates Analytics as Its Own Bounded Context

Kimball's business-process orientation produces star schemas that are essentially bounded contexts. The Enterprise Bus Matrix is a context map — a grid of business processes vs. conformed dimensions that shows which contexts share which concepts.

This independently validates that Analytics Data Systems have their own architectural logic — dimensional modeling, semantic layers, conformed dimensions — that is structurally different from OLTP domain modeling.

**Source:** [Kimball vs. Inmon Through the DDD Lens](https://github.com/semops-ai/semops-data/blob/main/docs/research/ddd-data-architecture.md#kimball-vs-inmon-through-the-ddd-lens)

### 5. Data Mesh Validates the Application ↔ Analytics Boundary

Dehghani's founding argument is that domain boundaries should persist across the OLTP/OLAP divide:

> "Though we have adopted domain oriented decomposition and ownership when implementing operational capabilities, curiously we have disregarded the notion of business domains when it comes to data."

Her framework is evidence that Application and Analytics are genuinely different system types requiring explicit boundary management — the core claim of the four-types framework.

### 6. dbt Validates Analytics Governance Is Structurally Different

dbt independently converged on DDD principles from the data engineering side:

| dbt Feature | DDD Equivalent | What It Proves |
|---|---|---|
| Staging layer | Anti-Corruption Layer | Analytics needs boundary protection from source systems |
| Semantic layer (MetricFlow) | Ubiquitous Language | Analytics has its own vocabulary enforcement needs |
| dbt Mesh (multi-project) | Bounded Contexts | Analytics contains multiple internal bounded contexts |
| Data tests | Aggregate Invariants | Analytics has its own invariant enforcement (at rest, not runtime) |
| `ref` function | Structural lineage | Analytics governance is lineage-first by design |

You don't govern a dbt project the way you govern a microservice. The governance discipline is genuinely different.

**Source:** [dbt: Independent Convergence on DDD Principles](https://github.com/semops-ai/semops-data/blob/main/docs/research/ddd-data-architecture.-dbt-independent-convergence-on-ddd-principles)

### 7. Enterprise Record Governance Resembles DDD Invariants

The four-types framework claims finance works because "semantics are explicit and enforced." DDD's aggregate invariant pattern describes the same mechanism:

| Finance Governance | DDD Equivalent |
|---|---|
| Double-entry accounting (debits = credits) | Aggregate invariant — formal constraint on state |
| Chart of accounts | Ubiquitous language — enforced vocabulary |
| Audit trail | Domain event log — complete history of state changes |
| Period close | Aggregate boundary — consistency boundary in time |

Enterprise Record systems independently implement DDD governance patterns through regulatory enforcement rather than software design. This validates the four-types claim that Record systems have the "highest semantic integrity" — they achieve it through the same mechanisms DDD prescribes.

---

## Where the Framework Can Be Refined

### 1. Bounded Context Nesting

The four-types framework says each type IS a bounded context. The research found that within Analytics, you need **multiple** bounded contexts (Finance mart, Sales mart, HR Gold layer). Databricks explicitly recommends "multiple Gold layers by business need" — which is bounded contexts at the Gold tier.

**Resolution:** The four types are **first-order** boundaries. Within each type, there are second-order bounded contexts. The framework is correct at its level of abstraction but could be more explicit about the nesting.

### 2. Enterprise Work as DDD Frontier

DDD concepts map beautifully to Application and Analytics, reasonably to Record, and **poorly to Work**. This is because DDD assumes structured domain models, and Enterprise Work is pre-model — it needs semantic scaffolding before domain patterns can apply.

This is a **strength** of the framework, not a weakness. The four types identify a category that mainstream architectural thinking (DDD included) doesn't cover. Enterprise Work isn't "Application Data with bad schemas" — it's a genuinely distinct system type that requires its own governance approach (SemOps, knowledge graphs, AI extraction).

### 3. Enterprise Record Deserves Deeper DDD Analysis

The research focused heavily on Application → Analytics (the richest territory for concrete DDD mappings). The Record → Analytics boundary and Record's internal DDD properties (invariant enforcement, audit trails, period boundaries) are documented above but deserve their own dedicated analysis.

### 4. Industry Mix Percentages Are Heuristic

The mix predictions (SaaS: 60% App, 25% Analytics, etc.) are directional heuristics. The DDD research neither confirms nor challenges specific percentages — they're an empirical claim, not an architectural one. They remain useful for diagnostic framing.

---

## Summary

| Claim | Verdict | Evidence |
|---|---|---|
| Each type is a bounded context | **Confirmed** | DDD maps differently to each type |
| Same events, different physics | **Confirmed + mechanism identified** | CDC loses 5 specific semantic properties |
| Don't try to unify | **Confirmed + reframed** | SSOT failure cascade documented; lineage as alternative |
| Each type has distinct integration patterns | **Confirmed** | Context mapping produces same patterns independently |
| Analytics has its own governance | **Confirmed** | Kimball, dbt, Data Mesh all validate independently |
| Enterprise Record has highest semantic integrity | **Confirmed** | Finance governance ≈ DDD invariant enforcement |
| Enterprise Work needs different approach | **Confirmed** | DDD has no patterns for pre-model data |
| Industry mix percentages | **Not tested** | Empirical claim, not architecturally verifiable |

**The four types carve at genuine joints.** The DDD research explains *why* those boundaries exist (different semantic physics) and *how* they manifest architecturally (different DDD patterns per type). The framework does what a good taxonomy should: it predicts architectural differences that turn out to be real.

---

## Related Links

### Strategic Data

- [Four Data System Types](four-data-system-types.md) — The framework being validated
- [Data Systems Architecture Map](data-systems-architecture-map.md) — DDD as connective tissue between Strategic Data frameworks
- [Data Silos](data-silos.md) — "Same events, different physics"
- [Surface Analysis](surface-analysis.md) — Boundary and interface analysis
- [Governance as Strategy](governance-as-strategy.md) — Governance patterns across system types

### Explicit Architecture

- [What Is Architecture?](../EXPLICIT_ARCHITECTURE/what-is-architecture.md) — Architecture ≠ infrastructure
- [Domain-Driven Design](../EXPLICIT_ARCHITECTURE/domain-driven-design.md) — Core DDD concepts
- [Anti-Corruption Layers](../EXPLICIT_ARCHITECTURE/ddd-acl-governance-aas.md) — Boundary integration patterns

### Research (semops-data)

- [Data Architecture Through the DDD Lens](https://github.com/semops-ai/semops-data/blob/main/docs/research/ddd-data-architecture.md) — Full research document
- [Four Data System Types (semops-data reference copy)](https://github.com/semops-ai/semops-data/blob/main/docs/research/four-data-system-types.md) — Due diligence reference copy
