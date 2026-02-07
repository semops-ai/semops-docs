---
doc_type: hub

pattern: everything-is-data

provenance: 1p

metadata:
    pattern_type: concept
    brand_strength: medium
---

# Everything is Data

> In the AI era, the boundary between "data" and "everything else" collapses. Code, documents, models, patterns, decisions, and conversations all become inputs to agentic systems. This expands the scope of Strategic Data from "managing databases" to "managing all semantic artifacts."

**Key insights:** AI does not distinguish between traditional data and other artifacts. Everything that can be parsed, embedded, or reasoned over becomes a potential training signal, context source, or agent input. Getting data management right is no longer optional—it is existential.

---

## The Expansion

Traditional data management focused on a narrow slice:

| Traditional "Data" | Managed By | Governance |
|-------------------|------------|------------|
| Database records | DBAs, Data Engineers | Schema, constraints |
| Analytics/metrics | BI teams, Analysts | Semantic layer, definitions |
| Files/documents | ... nobody? | Ad hoc |

AI/agentic systems treat **everything** as data:

| AI-Era "Data" | Why It's Data Now | Semantic Requirement |
|---------------|-------------------|---------------------|
| **Code** | Patterns, structure, intent—all parseable, embeddable, learnable | Consistent naming, explicit types, documented invariants |
| **Work artifacts** | Slack, docs, meetings—context for decisions, training signal for agents | Structure through ontologies, explicit relationships |
| **Models** | Weights, architectures, training provenance—artifacts with lineage | Version control, reproducibility, lineage tracking |
| **Patterns** | Reusable semantic structures—templates for meaning | Explicit schema, composability rules |
| **Decisions** | Rationale, context, outcomes—learning signal | Decision logs, impact tracking, simulation capability |
| **Conversations** | Prompts, responses, corrections—feedback loops | Session context, preference learning, drift detection |

**The shift:** From managing structured records to managing **all semantic artifacts**.

---

## Why This Amplifies Strategic Data

The [Strategic Data](README.md) framework already argues:
- Data has physics (energy costs, structure requirements)
- Schema = meaning (no structure, no semantics)
- Source-up thinking (define meaning at capture)

The "everything is data" premise **amplifies** these principles:

### 1. Existing Debt Compounds

| Pre-AI Debt | AI-Era Amplification |
|-------------|---------------------|
| Inconsistent metric definitions | Agent outputs vary by context, hallucination risk |
| Undocumented tribal knowledge | Agents cannot access what humans "just know" |
| Code without explicit types | AI struggles to reason about intent |
| Decisions without rationale | No learning signal, repeated mistakes |

**The multiplier:** Every piece of semantic debt becomes a potential failure mode for every agent interaction.

### 2. The Four Data Types Expand

The [Four Data System Types](four-data-system-types.md) framework remains valid, but scope expands:

| System Type | Traditional Scope | AI-Era Scope |
|-------------|-------------------|--------------|
| **Application** | Transactional records | + API contracts, code patterns, runtime behavior |
| **Analytics** | Metrics, reports | + Model outputs, experiment results, predictions |
| **Enterprise Work** | Documents, email | + Conversations, agent interactions, collaborative artifacts |
| **Systems of Record** | Finance, ERP | + Model registries, decision logs, governance records |

**Enterprise Work becomes central, not peripheral.** The 95% of meaning that lived in "dark data" is now primary input for agentic systems.

### 3. Coherence Scope Expands

[Semantic Coherence](../SEMANTIC_OPTIMIZATION/semantic-coherence.md) defined coherence across systems and teams. Now it must span:

```
Traditional coherence scope:
  Database ↔ Dashboard ↔ Report ↔ Human interpretation

AI-era coherence scope:
  Code ↔ Docs ↔ Schema ↔ Model ↔ Agent ↔ Human ↔ Decision ↔ Outcome

Every node is a potential entry point for an agent.
Every edge is a potential semantic mismatch.
```

---

## The Coherence Requirement

Agents need coherent semantics across ALL artifacts because they:

### 1. Consume Everything as Context

An agent working on a task might consume:
- Code (to understand implementation)
- Docs (to understand intent)
- Schema (to understand data structure)
- Conversations (to understand preferences)
- Decisions (to understand constraints)

**If these are incoherent, agent output is unpredictable.**

### 2. Produce Artifacts That Become Data

Agent outputs become inputs:
- Generated code → future training data
- Responses → preference signals
- Decisions → precedents
- Documents → knowledge base

**If outputs lack structure, the feedback loop degrades.**

### 3. Operate Across Boundaries

Agentic systems span:
- Multiple codebases
- Multiple data systems
- Multiple team contexts
- Multiple time horizons

**[Bounded contexts](../SYMBIOTIC_ARCHITECTURE/domain-driven-design.md) that worked for humans break down when agents traverse them freely.**

---

## Implications for Strategic Data

### 1. Instrument Everything

Source-up thinking applies to all artifacts:

```yaml
# Traditional: instrument application events
event: checkout_completed
  user_id: required
  order_id: required

# AI-era: instrument ALL semantic artifacts
decision:
  id: pricing-model-v2
  rationale: "A/B test showed 15% lift"
  context: [experiment-123, stakeholder-review-2024-01]
  outcome: pending

code_change:
  id: commit-abc123
  intent: "Optimize query performance"
  patterns_applied: [caching, index-optimization]
  semantic_impact: [latency-reduction, cost-reduction]
```

### 2. Schema Everything

No schema = no semantics. This now applies beyond databases:

| Artifact | Schema Requirement |
|----------|-------------------|
| Code | Explicit types, documented interfaces, named patterns |
| Decisions | Structured logs with rationale, context, outcomes |
| Conversations | Session context, topic classification, preference signals |
| Documents | Frontmatter, relationship links, provenance |

### 3. Own Lineage Everywhere

Lineage tracking extends to all artifacts:

```
Code change → triggered by decision → informed by data → validated by test → deployed to production → observed in metrics → fed back to model
```

**Every link in this chain is now "data" that agents consume and produce.**

---

## The Strategic Data Foundation

This is why Strategic Data is foundational to the [Semantic Operations Framework](../README.md):

```
Without Strategic Data discipline:
  Traditional data is messy
  + AI treats everything as data
  = Exponential semantic chaos

With Strategic Data discipline:
  Traditional data is coherent
  + AI treats everything as data
  = Coherent AI operations
```

**The bottom line:** If an organization cannot manage databases, it certainly cannot manage the expanded scope of AI-era data. Strategic Data is table stakes—not an optimization, but a prerequisite.

---

## Key Takeaways

1. **AI collapses the data boundary**
   - Code, docs, models, patterns, decisions—all are data now
   - Traditional data management scope was always too narrow

2. **Existing debt compounds**
   - Every semantic gap becomes a failure mode
   - Undocumented knowledge = agent blind spots

3. **Enterprise Work becomes central**
   - The 95% of meaning in "dark data" is now primary input
   - Structure through [SemOps](../README.md) is no longer optional

4. **Coherence scope expands**
   - Not just database ↔ dashboard
   - Code ↔ docs ↔ schema ↔ model ↔ agent ↔ human ↔ decision

5. **Strategic Data is prerequisite**
   - Get the basics right before AI amplifies existing problems
   - Source-up thinking, schema everything, own lineage

---

## Related Links

### Parent Concept

- [Strategic Data](README.md) - Framework for data as physics

### Framework Components

- [Four Data System Types](four-data-system-types.md) - Categories expand in scope
- [Semantic Coherence](../SEMANTIC_OPTIMIZATION/semantic-coherence.md) - Coherence across all artifacts
- [Data Silos](data-silos.md) - Why the framing is wrong

### See Also

- [AI-Ready Architecture](../SYMBIOTIC_ARCHITECTURE/ai-ready-architecture.md) - Code as data
- [Surface Analysis](surface-analysis.md) - Instrumentation boundaries expand
