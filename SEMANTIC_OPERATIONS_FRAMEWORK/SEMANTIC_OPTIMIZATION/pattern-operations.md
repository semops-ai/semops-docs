# Pattern Operations

> How Patterns are operationalized: the lifecycle that produces them, the promotion loop from flexible edge to stable core, the optimization cycle that balances growth with coherence, and the governance layers that maintain semantic integrity.

For Pattern definitions and taxonomy, see [Semantic Patterns](semantic-patterns.md).

---

## Pattern Lifecycle

Patterns are living objects. They have a lifecycle that resembles Agile work more than traditional documentation.

### How 1P Patterns Come into Existence

1P Patterns are produced through a three-phase process:

1. **Decompose.** Break down 3P sources (vendor products, frameworks, standards) into constituent capabilities. Each capability receives a vendor-neutral name, a functional description, and a classification backbone mapping.
2. **Discover.** Cross-reference decompositions to identify universal capabilities that recur across multiple sources. The universal core, conditional tier, and differentiators emerge from a presence matrix.
3. **Compose.** Synthesize discovered capabilities into a registerable 1P Pattern with per-capability lineage tracking.

Each phase adds strategic information. Decomposition reveals what a product actually does. Discovery reveals what the category requires. Composition produces an organization-owned Pattern that encodes which capabilities matter, where they came from, and why they were selected.

### How 3P Patterns Enter the System

3P Patterns are registered when a recognized external framework, methodology, or product architecture is adopted as a reference point. Registration captures the source and which capabilities the ecosystem adopts from it.

3P Patterns serve a dual role: they are reference shapes in their own right, and they are inputs to the decomposition process that produces 1P Patterns.

### Lifecycle Over Time

A Pattern starts as a hypothesis: "CRM" looks like the right shape. Decomposition tests that hypothesis against real products. Discovery refines it. Composition produces something the organization owns. Over time, capabilities get added, reclassified, or deprecated as the domain evolves. New vendors enter the space and decomposition runs again, discovering capabilities that did not exist before.

The 1P Pattern composed today is a snapshot of current understanding. Lineage means that snapshot is traceable: the origin of every piece is known. Provenance means the distinction between what is owned and what is adopted is explicit. When the world changes, the impact is identifiable.

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
│  • Potential new Patterns (not yet named)                                    │
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
│  • Approved Patterns (3p standards, 1p innovations)                          │
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

[Semantic Operations](../README.md) operates on **Patterns** (bounded contexts, aggregates).

| Agile                           | Semantic Ops                            |
| ------------------------------- | --------------------------------------- |
| Sprint backlog of stories       | Pattern adoption queue                  |
| Story points (effort)           | Pattern complexity (semantic scope)     |
| Velocity (stories/sprint)       | Coherence delta (Patterns integrated)   |
| Definition of Done (checklist)  | Pattern validation (invariants hold)    |

### The Agile Replacement Hypothesis

> "Could Semantic Operations kill Agile? Possibly."

When LLMs can:

1. Identify the right Pattern for a problem
2. Implement the Pattern correctly (because they know the mean)
3. Validate the implementation against invariants
4. Flag deviations as potential 1P

...then Agile's decomposition step becomes unnecessary.

**The process does not decompose into stories.** Instead:

1. Identify the Pattern
2. Ask the LLM to implement it
3. Validate the result
4. Track any 1P deviations

### What Remains Human

- **Choosing which Pattern** (judgment, context)
- **Creating 1P Patterns** (innovation, meaning-making)
- **Validating 1P Patterns** (the LLM does not know them yet)
- **Deciding when to deviate** (risk appetite, strategy)

For practical guidance on these judgment calls, finding the right Pattern, sizing it for your architecture, and deciding when to evolve from 3P to 1P, see [Working with Patterns](working-with-patterns.md).

---

## Governance

Governance operates through three layers:

### Entry Gates

Entry gates prevent bad data from entering the registries. At registration time, classification and validation specifications run against the candidate: duplicate detection, naming quality, lineage requirements, definition completeness. A Pattern or capability that fails an entry gate is not registered until the issue is resolved.

### Ongoing Audit

Ongoing audit detects drift that cannot be caught at entry. Cross-registry rules check for overlap across Patterns, lifecycle drift, stale edges, partial adoption, and orphan capabilities. These run as batch operations during architecture sync and produce findings with severity and fix recommendations.

### Fitness Measurement

Audit findings feed into ecosystem automation that drives project prioritization. Fitness measurement provides quantitative signals: how many Patterns lack capabilities, how many capabilities lack infrastructure bindings, how much of the architecture is aspirational vs. operational. These signals connect directly to [Semantic Coherence](semantic-coherence.md) measurement.

For implementation detail on governance mechanics (registration gates, audit rules, lifecycle enforcement, schemas, ownership), see [DD-0022: Registry Governance](https://github.com/semops-ai/semops-dx-orchestrator/blob/main/docs/design-docs/DD-0022-pattern-capability-registry-governance.md).

---

## Provenance and Lineage in Pattern Operations

Every promotion, rejection, or merge decision is a **provenance event** that explains *why* the knowledge graph changed. Pattern operations should be tracked with the same rigor as content lineage.

For the full treatment of provenance and lineage as coherence mechanisms, see [Provenance and Lineage](provenance-lineage-semops.md).

---

## Resolved Design Questions

These questions were open during early development. Each has been resolved through implementation and documented in design docs.

| Question | Resolution | Reference |
| -------- | ---------- | --------- |
| Pattern vs. Concept | Separate entities. Classification specifications (decomposition test, infrastructure binding test, edge-only test) determine type at intake. | [DD-0016](https://github.com/semops-ai/semops-dx-orchestrator/blob/main/docs/design-docs/DD-0016-concept-entity-model.md) |
| Pattern granularity | Granularity follows domain complexity (Tenet 2). Narrow tools decompose into ~10 capabilities; broad platforms decompose into 14-26. | [DD-0009 Tenets](https://github.com/semops-ai/semops-dx-orchestrator/blob/main/docs/design-docs/DD-0009-pattern-capability-foundational-model.md) |
| Pattern composition | `derives_from` tracks assembly lineage. A composition of two Patterns (e.g., CQRS + Event Sourcing) is a new 1P Pattern with both in its lineage. | [DD-0009 Composition](https://github.com/semops-ai/semops-dx-orchestrator/blob/main/docs/design-docs/DD-0009-pattern-capability-foundational-model.md) |
| 1P lifecycle maturity | Lifecycle transitions (planned, active, deprecated) are aggregate methods with domain events. Maturity is measured by capability coverage and infrastructure binding, not time. | [DD-0022 Lifecycle](https://github.com/semops-ai/semops-dx-orchestrator/blob/main/docs/design-docs/DD-0022-pattern-capability-registry-governance.md) |
| Validation thresholds | Specification engine with composable predicates. Each spec returns pass/fail with context and severity, not similarity scores. | [DD-0009 Specifications](https://github.com/semops-ai/semops-dx-orchestrator/blob/main/docs/design-docs/DD-0009-pattern-capability-foundational-model.md) |

---

## Related

### Theory

- [Semantic Patterns](semantic-patterns.md): Pattern definitions, taxonomy, and core properties
- [Working with Patterns](working-with-patterns.md): Finding, sizing, adopting, and evolving Patterns
- [Semantic Optimization](semantic-optimization.md): Why Patterns optimize coherence
- [Provenance and Lineage](provenance-lineage-semops.md): Provenance/lineage as coherence mechanism

### Design Docs

- [DD-0009: Pattern and Capability Foundational Model](https://github.com/semops-ai/semops-dx-orchestrator/blob/main/docs/design-docs/DD-0009-pattern-capability-foundational-model.md): Parts catalog model, decomposition tenets, specifications
- [DD-0022: Registry Governance](https://github.com/semops-ai/semops-dx-orchestrator/blob/main/docs/design-docs/DD-0022-pattern-capability-registry-governance.md): Entry gates, audit rules, lifecycle enforcement

### Implementation

- [Semantic Optimization Implementation](semantic-optimization-implementation.md): SC formulas, classifiers, RAG integration
- [domain-patterns/](https://github.com/semops-ai/semops-hub/tree/main/docs/domain-patterns): Pattern catalog
