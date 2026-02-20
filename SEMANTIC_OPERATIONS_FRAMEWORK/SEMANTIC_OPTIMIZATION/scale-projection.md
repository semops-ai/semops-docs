---
doc_type: hub

pattern: scale-projection

provenance: 1p

metadata:
    pattern_type: concept
    brand_strength: medium
---

# Scale Projection

> **Scale Projection** uses the path of infrastructure scaling as a diagnostic — testing and validating your core business logic and architecture by sketching what changes at the next order of magnitude.

## Overview

Scale Projection applies across multiple dimensions — data volume, model quality, knowledge capture, infrastructure capacity — and prevents both premature optimization and architectural dead ends. Rather than building for hypothetical scale, it projects forward from current reality and asks: *what would need to change at 10x?*

The core claim inverts the usual framing:

> If the infrastructure delta between local and scaled is anything other than "wrapper and plumbing," your [architecture](../EXPLICIT_ARCHITECTURE/README.md) (not your infrastructure) needs work.

Conventional thinking: "We need better infrastructure to scale." Scale Projection: "If scaling is hard, our domain model is wrong."

---

## The Diagnostic

Scale Projection works by sketching the scaled version of a system — not deploying it, just describing it — and categorizing the delta:

| Category | What It Means | Signal |
| -------- | ------------- | ------ |
| **Clean** | Pure infrastructure swap (file queue → SQS, SQLite → Postgres) | Architecture is sound — this is capacity planning |
| **Concerning** | Logic changes needed (different branching, new edge cases) | Domain model may have coupling to infrastructure |
| **Blocking** | Fundamental rethink required (data ownership, service boundaries) | Architecture needs work before scaling |

If the delta is all Clean, the architecture is validated — scaling is a plumbing exercise. If Concerning or Blocking items appear, the architecture is telling you something, and that signal is valuable *now*, before you actually need to scale.

---

## 3P Lineage

Scale Projection draws from and extends several established patterns:

| 3P Pattern | What It Provides | Scale Projection Extension |
| ---------- | ---------------- | -------------------------- |
| **[Hexagonal Architecture](../EXPLICIT_ARCHITECTURE/domain-driven-design.md)** (Cockburn) | Domain isolated from infrastructure via ports/adapters | Domain logic should be *identical* across scale levels, not just isolated |
| **Maturity Models** (CMMI, DevOps) | Progressive levels of process capability | Levels represent *domain understanding*, not process maturity |
| **Fitness Functions** (Ford, Parsons, Kua) | Testable architectural properties, automated drift detection | *Sketch* the scaled version as a diagnostic — don't deploy it |
| **Capacity Planning** (SRE) | Forecast resource needs from growth projections | Use infrastructure delta to reveal *architecture* problems, not just resource needs |

---

## Application to Patterns

In the context of [Working with Patterns](working-with-patterns.md), Scale Projection informs:

- **[Pattern sizing](working-with-patterns.md#sizing-patterns)** — understanding whether a pattern's scope will hold at the next scale level. If a pattern breaks at 10x, it may be too tightly coupled to current infrastructure.
- **Competitive analysis** — projecting what architecture and infrastructure changes would be needed to match a competitor's pattern, and [coherence scoring](semantic-coherence.md) the scenario for unseen impacts
- **Table stakes validation** — determining whether a 3P pattern delivers enough capability for projected growth, or whether 1P innovation is genuinely needed

Synthetic data can test assumptions at 10x scale without waiting for organic growth.

---

## Implementation

The full implementation pattern and project are maintained in the platform repo:

- [Scale Projection pattern (semops-dx-orchestrator)](https://github.com/semops-ai/semops-dx-orchestrator/blob/main/docs/domain-patterns/scale-projection.md) — Selection criteria, decision framework, implementation details

---

## Related Links

### Core Concepts

- [Working with Patterns](working-with-patterns.md) - Pattern sizing, competitive analysis
- [Patterns](patterns.md) - Pattern definitions and taxonomy
- [Semantic Coherence](semantic-coherence.md) - Measuring pattern integrity
- [Stable Core, Flexible Edge](../EXPLICIT_ARCHITECTURE/stable-core-flexible-edge.md) - Evolution principle

### Architecture

- [Explicit Architecture](../EXPLICIT_ARCHITECTURE/README.md) - Architecture as encoded rules
- [Domain-Driven Design](../EXPLICIT_ARCHITECTURE/domain-driven-design.md) - Hexagonal architecture, bounded contexts

### Implementation

- [Scale Projection (semops-dx-orchestrator)](https://github.com/semops-ai/semops-dx-orchestrator/blob/main/docs/domain-patterns/scale-projection.md) - Full pattern documentation
- [Open Primitive Pattern](https://github.com/semops-ai/semops-dx-orchestrator/blob/main/docs/domain-patterns/open-primitive-pattern.md) - Infrastructure selection criteria
