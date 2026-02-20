---
doc_type: hub
pattern: pattern-operations
provenance: 1p
metadata:
  pattern_type: concept
  brand-strength: high
---

# Pattern Operations

> How to operationalize domain patterns—the promotion loop from flexible edge to stable core, the optimization cycle that balances growth with coherence, and governance workflows that maintain semantic integrity.

For pattern definitions and taxonomy, see [Patterns](patterns.md).

---

## The SemOps Promotion Loop

*How information becomes knowledge and, eventually, Understanding.*

Promotion criteria determine what gets stabilized:

1. **Frequency** - Does the concept repeatedly appear across tools, teams, or datasets?
2. **Cross-team relevance** - Does more than one [bounded context](../EXPLICIT_ARCHITECTURE/domain-driven-design.md) or department need this concept?
3. **Predictive/explanatory value** - Does including this concept improve decisions, forecasts, or understanding?
4. **Stability** - Is the meaning stable over time, with low semantic drift?
5. **Executability** - Does this concept support workflows, automation, metrics, or APIs?
6. **Governance necessity** - Is this concept tied to security, compliance, or PII classification?
7. **Strategic centrality** - Does it behave like a "pillar" concept that organizes related knowledge?

If yes → **Promote it**.

---

## Shared Domain Knowledge Enables Understanding

When promoted concepts:

- Are **shared**
- Are **explainable**
- Support **projection** (forecasting)
- Are used across teams
- Provide **coordination primitives**

…then organizational Understanding becomes possible.

This directly improves:

- Decision quality
- Prioritization
- Trust and transparency
- Tradeoff management
- Execution speed
- Reduction of rework and confusion

```
FLEXIBLE EDGE (low structure)
  ↓ detect signal
  ↓ evaluate recurrence & meaning
  ↓ cluster candidates

PROMOTION CRITERIA (SemOps)
  ↓ cross-team adoption?
  ↓ stable meaning?
  ↓ predictive/explanatory power?

HARD SCHEMA (high structure)
  ↓ added to DDD model
  ↓ added to metrics layer
  ↓ added to canonical docs
  ↓ assigned ownership

SHARED DOMAIN KNOWLEDGE
  ↓ explainability
  ↓ projection
  ↓ coordination

ORGANIZATIONAL UNDERSTANDING
  ↓ decisions improve
  ↓ prioritization sharpens
  ↓ politics decrease
  ↓ execution accelerates
```

---

## The Optimization Cycle: Coherence → Growth → Coherence

### The Lifecycle: Flexible Edge → Stable Core

```
┌─────────────────────────────────────────────────────────────────────────────┐
│  FLEXIBLE EDGE (Experimentation Zone)                                        │
│                                                                              │
│  • Orphan entities (no concept reference)                                    │
│  • Tentative relationships (edges with low strength)                         │
│  • Soft metadata (JSONB fields, not schema columns)                          │
│  • approval_status = 'pending'                                               │
│  • provenance = undefined or tentative                                       │
│                                                                              │
│  What lives here:                                                            │
│  • New ideas being explored                                                  │
│  • Content not yet classified                                                │
│  • Experiments, drafts, hypotheses                                           │
│  • Potential new patterns (not yet named)                                    │
│                                                                              │
└─────────────────────────────────────┬───────────────────────────────────────┘
                                      │
                                      │ CLASSIFICATION + AUDIT
                                      │ (classifier pipeline, human review)
                                      │
                                      ▼
┌─────────────────────────────────────────────────────────────────────────────┐
│  DECISION POINT                                                              │
│                                                                              │
│  Options:                                                                    │
│  1. PROMOTE to stable core (approve concept, assign primary_concept_id)      │
│  2. REJECT (delete or archive)                                               │
│  3. KEEP EXPLORING (needs more work, stay at edge)                           │
│  4. CREATE NEW PATTERN (this reveals a new bounded context → 1p concept)     │
│                                                                              │
└─────────────────────────────────────┬───────────────────────────────────────┘
                                      │
                                      │ APPROVAL
                                      │
                                      ▼
┌─────────────────────────────────────────────────────────────────────────────┐
│  STABLE CORE (Semantically Coherent State)                                   │
│                                                                              │
│  • Concepts with approval_status = 'approved'                                │
│  • Entities with primary_concept_id set                                      │
│  • Explicit provenance (1p/3p)                                               │
│  • Strong edges (SKOS relationships, high strength)                          │
│  • Schema-level fields (not just JSONB)                                      │
│                                                                              │
│  What lives here:                                                            │
│  • Approved patterns (3p standards, 1p innovations)                          │
│  • Ubiquitous language definitions                                           │
│  • Stable bounded contexts                                                   │
│  • The "mean" you want LLMs to converge to                                   │
│                                                                              │
└─────────────────────────────────────────────────────────────────────────────┘
```

### No Equilibrium, Just Thresholds

> "When you reach a semantically coherent state (which just means you've audited and made adjustments to an acceptable threshold... no such equilibrium), then you are free to add new stuff."

**This is the core insight:** [Semantic coherence](semantic-coherence.md) is not an end state. It is a **threshold** that enables growth.

```
┌────────────────────────────────────────────────────────────────────────────┐
│  THE OPTIMIZATION CYCLE                                                     │
│                                                                             │
│                     ┌──────────────────┐                                    │
│                     │  STABLE CORE     │                                    │
│                     │  SC ≥ threshold  │                                    │
│                     │  (can grow now)  │                                    │
│                     └────────┬─────────┘                                    │
│                              │                                              │
│                              │ ADD NEW STUFF                                │
│                              │ (orphans, experiments)                       │
│                              ▼                                              │
│                     ┌──────────────────┐                                    │
│                     │  FLEXIBLE EDGE   │                                    │
│                     │  SC drops        │                                    │
│                     │  (exploring)     │                                    │
│                     └────────┬─────────┘                                    │
│                              │                                              │
│                              │ CLASSIFY + AUDIT                             │
│                              │ (promote, reject, create new patterns)       │
│                              ▼                                              │
│                     ┌──────────────────┐                                    │
│                     │  DECISION        │                                    │
│                     │  SC < threshold? │                                    │
│                     └────────┬─────────┘                                    │
│                              │                                              │
│              ┌───────────────┼───────────────┐                              │
│              │               │               │                              │
│              ▼               ▼               ▼                              │
│     ┌────────────┐   ┌────────────┐   ┌────────────┐                        │
│     │ OPTIMIZE   │   │ GROW MORE  │   │ PAUSE      │                        │
│     │ (promote   │   │ (SC still  │   │ (SC too    │                        │
│     │ or reject) │   │ above)     │   │ low, fix)  │                        │
│     └──────┬─────┘   └──────┬─────┘   └──────┬─────┘                        │
│            │                │                │                              │
│            └────────────────┴────────────────┘                              │
│                              │                                              │
│                              ▼                                              │
│                     ┌──────────────────┐                                    │
│                     │  STABLE CORE     │                                    │
│                     │  SC ≥ threshold  │                                    │
│                     │  (repeat)        │                                    │
│                     └──────────────────┘                                    │
│                                                                             │
└────────────────────────────────────────────────────────────────────────────┘
```

---

## What This Means for Agile (and Beyond)

### Agile's Unit Problem

Agile operates on **tickets** (stories, tasks, bugs).

[Semantic Operations](../README.md) operates on **patterns** (bounded contexts, aggregates).

| Agile                           | Semantic Ops                            |
| ------------------------------- | --------------------------------------- |
| Sprint backlog of stories       | Pattern adoption queue                  |
| Story points (effort)           | Pattern complexity (semantic scope)     |
| Velocity (stories/sprint)       | Coherence delta (patterns integrated)   |
| Definition of Done (checklist)  | Pattern validation (invariants hold)    |

### The Agile Replacement Hypothesis

> "Could Semantic Operations kill Agile? Possibly."

When LLMs can:

1. Identify the right pattern for a problem
2. Implement the pattern correctly (because they know the mean)
3. Validate the implementation against invariants
4. Flag deviations as potential 1p

...then Agile's decomposition step becomes unnecessary.

**The process does not decompose into stories.** Instead:

1. Identify the pattern
2. Ask the LLM to implement it
3. Validate the result
4. Track any 1p deviations

### What Remains Human

- **Choosing which pattern** (judgment, context)
- **Creating 1p patterns** (innovation, meaning-making)
- **Validating 1p patterns** (the LLM does not know them yet)
- **Deciding when to deviate** (risk appetite, strategy)

For practical guidance on these judgment calls — finding the right pattern, sizing it for your architecture, and deciding when to evolve from 3P to 1P — see [Working with Patterns](working-with-patterns.md).

---

## Provenance and Lineage in Pattern Operations

Every promotion, rejection, or merge decision is a **provenance event** — it explains *why* the knowledge graph changed. Pattern operations should be tracked with the same rigor as content lineage.

For the full treatment of provenance and lineage as coherence mechanisms, see [Provenance and Lineage](provenance-lineage-semops.md).

---

## Open Questions

1. **Pattern vs Concept:** Is a pattern a type of concept, or a separate entity? Should the schema distinguish them?

2. **Pattern Granularity:** At what level should patterns be tracked? Repository Pattern is huge; a specific validation rule is tiny. What is the right scope?

3. **Pattern Composition:** How do patterns compose? Is "CQRS + Event Sourcing" a new pattern or a composition of two?

4. **1p Pattern Lifecycle:** When does a 1p pattern become "stable enough" to be treated like 3p? When should LLMs learn it?

5. **Pattern Validation Thresholds:** What similarity score indicates "correct implementation" vs "deviation"?

---

## Related Links

### Theory

- [Patterns](patterns.md) - Pattern definitions, taxonomy, and core properties
- [Working with Patterns](working-with-patterns.md) - Finding, sizing, adopting, and evolving patterns
- [Semantic Optimization](semantic-optimization.md) - Why patterns optimize coherence
- [Provenance and Lineage](provenance-lineage-semops.md) - Provenance/lineage as coherence mechanism

### Implementation

- [Semantic Optimization Implementation](semantic-optimization-implementation.md) - SC formulas, classifiers, RAG integration
- [domain-patterns/](https://github.com/semops-ai/semops-core/tree/main/docs/domain-patterns) - Pattern catalog
