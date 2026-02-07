---
doc_type: hub

pattern: semantic-funnel

provenance: 1p

metadata:
    pattern_type: concept
    brand_strength: high
# Semantic Funnel is the core mental model combining OAR (Objects, Agents, Rules) with DIKW hierarchy. Renamed from "DIKW Mental Model" per ADR-0001.
---

# The Semantic Funnel

> A mental model combining **Objects, Agents, Rules (OAR)** with the **DIKW hierarchy** to describe how meaning flows through organizations and systems.

<!-- fix this diagram -->
```
        Objects + Agents + Rules (OAR)
                    ↓
         ┌──────────────────────┐
         │        DATA          │  ← Raw objects, no meaning
         │    ┌──────────────┐  │
         │    │ INFORMATION  │  │  ← Structural rules applied
         │    │  ┌────────┐  │  │
         │    │  │KNOWLEDGE│  │  │  ← Interpretive rules applied
         │    │  │ ┌────┐ │  │  │
         │    │  │ │ W  │ │  │  │  ← Normative rules applied
         │    │  │ └────┘ │  │  │
         │    │  └────────┘  │  │
         │    └──────────────┘  │
         └──────────────────────┘

Each level narrows as meaning increases and uncertainty decreases.
```

---

## Objects, Agents, Rules (OAR)

This mental model reduces complex systems, processes, and organizations to a very simple and fundamental set.  With guidance from [Agent-Based Modeling](https://en.wikipedia.org/wiki/Agent-based_model) and [Business Rules](https://en.wikipedia.org/wiki/Business_rule) this model enables thinking about only a few entities:

| Entity | Definition | Examples |
|--------|------------|----------|
| **Objects** | Things that exist and can be acted upon | Data, documents, products, money, decisions |
| **Agents** | Things that can decide | People, teams, software, AI systems |
| **Rules** | Things that constrain, rules | process → policy → regulations → laws of physics |

**Key insight:** Regardless of domain, "work" is humans and machines (Agents) acting on documents, products, decisions, money, code (Objects). All Objects that flow through a system of constraints (Rules), which are also Objects, which are also created by Agents.

### Rules = Understanding

**Rules are crystallized understanding** Rules are decisions that already happened, now encoded so transformation can occur without requiring a fresh decision every time. When rules are insufficient or conflicting, a decision is required, and probability re-enters the system.

| Rule Type | DIKW Level | Example |
|-----------|------------|---------|
| **Structural rules** | D→I | "This field is a date, that column is revenue" |
| **Interpretive rules** | I→K | "When these three signals align, it indicates churn risk" |
| **Normative rules** | K→W | "We prioritize long-term customer value over short-term revenue" |
| **Meta-rules** | Wisdom | Rules that create, validate, or constrain other rules |

See [What is Understanding?](what-is-understanding.md) for deep dive on understanding as process and how rules encode it.

---

## The DIKW Hierarchy

With OAR as the foundation, the next step is describing how Objects transform through progressive stages of meaning. The [DIKW hierarchy](https://en.wikipedia.org/wiki/DIKW_pyramid) is a framework from information science describing this progression:

```
Data ──────> Information ──────> Knowledge ──────> Wisdom
         │                │                │
    Relationships     Patterns        Principles
      Structure       Causality         Values
         ↑                ↑                ↑
[              Understanding (Process)                   ]

Each transformation requires cognitive investment to add meaning.
```

### Key Insights

- Each level requires a process of understanding that generates **correct meaning** (implied by "false information ≠ information")
- Each transformation requires increased energy; *Understanding* is the engine that drives transformation
- Understanding is not a static state but a **process**, per [Bellinger et al's interpretation](dikw-theory-comparison.md)

**Each level requires more complex understanding:**

- **Data → Information**: Understanding **relationships** (how to structure)
- **Information → Knowledge**: Understanding **patterns** (how signals connect)
- **Knowledge → Wisdom**: Understanding **principles** (why patterns exist, how to judge)

### Determinism Decreases as You Climb

| Transformation | Determinism | What's Required |
|----------------|-------------|-----------------|
| **D→I** | High | Apply the right schema—once correct, repeatable |
| **I→K** | Medium | Interpret patterns, infer causality—probabilistic |
| **K→W** | Low | Apply judgment, ethics, values—highly probabilistic |
ns** (how signals connect)

This maps directly to where errors come from:

- **Deterministic failures** (D→I): Objects mismanaged, systems not aligned, relationships wrong—unforced errors, fixable through better system rules and more reliable agents
- **Probabilistic failures** (I→K→W): Bad interpretations, wrong data object inputs, bad judgments—fixable with relevant objects and better decision-making rules

### Level Characteristics

1. **Distinct stages**: Each level is qualitatively different and not interchangeable—K cannot be reached without developing I first
2. **Transformational progression**: Moving up requires active processing of increasing complexity and uncertainty, not passive accumulation
3. **Level definitions**:
   - **Data**: Raw facts with no inherent meaning—isolated symbols, measurements, observations ("know nothing")
   - **Information**: Data organized and contextualized with relationships answering *who, what, where, when* ("know-what")
   - **Knowledge**: Information revealing patterns and actionable signals, answering *how* ("know-how")
   - **Wisdom**: Knowledge evaluated through principles, values, and ethics—predicting long-term consequences, understanding *why* it matters
4. **Human judgment essential**: Wisdom requires uniquely human capabilities—values, ethics, and long-term contextual reasoning
5. **Normatively true**: The framework assumes no such thing as false information or false knowledge—that simply means it does not meet the definition

---

## DIKW Through AI Lens

> The Semantic Funnel is a useful mental model for human and machine processing.

**Core principle:** AI is excellent at D→I→K transformations but does not achieve Understanding or Wisdom in the human sense. However, AI can **simulate and stimulate** understanding by helping humans manage context and accelerate the meaning-generation process.

### Training vs. Inference

**Training: Building Knowledge**

| DIKW Level | AI Process | What Happens |
|------------|------------|--------------|
| **Data** | Raw tokens, text, images | Unstructured inputs |
| **Information** | Tokenization, normalization | Structure imposed via embedding |
| **Knowledge** | Statistical learning, pattern encoding | Associations stored in model weights |

**Meaning injected by:** Training data curation, labeling, architectures chosen by humans

**Inference: Using Knowledge**

| DIKW Level | AI Process | What Happens | Meaning Injected By |
|------------|------------|--------------|---------------------|
| **Data** | Raw prompt tokens | User input | None yet |
| **Information** | Context window embedding | Structured representation | Prompt engineering |
| **Knowledge** | Pattern matching, retrieval | Access stored associations | Model training |
| **Understanding** (simulated) | Contextualized reasoning | Explanations, synthesis | System prompts, RAG, grounding data |
| **Wisdom** (simulated) | Policy-aligned outputs | Guidance, recommendations | RLHF, guardrails, principles encoded |

### AI as Meaning Accelerator

AI does not **create** Understanding or Wisdom—it helps humans **generate them faster and more consistently**:

- **Holds massive context in place** while humans explore
- **Rapidly retrieves and structures knowledge** for evaluation
- **Constructs/deconstructs knowledge structures** quickly
- **Suspends understanding long enough** to overcome the energy barrier

**Key insight:** AI creates "suspended understanding"—not an oracle with answers, but a system that maintains context while humans work towards correct meaning.

### AI = Analytics + Autonomy + Scale − Determinism

Analytics by itself cannot decide or execute on its own. AI is analytics that can **decide**—that is what makes it an Agent. AI can also *decide* + *execute* at a higher DIKW level than traditional analytics:
**Each level requires more complex understanding:**

- **Data → Information**: Understanding **relationships** (how to structure)
- **Information → Knowledge**: Understanding **patterns** (how signals connect)
- **Knowledge → Wisdom**: Understanding **principles** (why patterns exist, how to judge)

### Determinism Decreases as You Climb

| Transformation | Determinism | What's Required |
|----------------|-------------|-----------------|
| **D→I** | High | Apply the right schema—once correct, repeatable |
| **I→K** | Medium | Interpret patterns, infer causality—probabilistic |
| **K→W** | Low | Apply judgment, ethics, values—highly probabilistic |

| Transformation | Analytics + Human | AI |
|----------------|-------------------|-----|
| **D→I** | Decide + Execute | Decide + Execute |
| **I→K** | Decide | Decide + Execute |
| **K→W** | Decide | Support |

AI combines the **decision capability** of humans with the **speed/scale** of data systems, but inherits the error profile of humans: it can be wrong.

| | Analytics + Human | AI |
|------------------|-------------------|-----|
| **Determinism** | Non-deterministic | Non-deterministic |
| **Error Source** | Human judgment | AI judgment |
| **Speed** | Slow | Fast |
| **Scale** | Low | High |

### The AI Challenge

> **Get the autonomy and scale but control non-deterministic errors.**

**AI Conditions:** Rules and Objects need to be coherent semantic objects to enable AI agents.

**Optimization:** The more coherent and goal-aligned the semantic objects are, the lower the non-deterministic errors.

This connects directly to [Semantic Coherence](../../SEMANTIC_OPERATIONS_FRAMEWORK/SEMANTIC_OPTIMIZATION/semantic-coherence.md)—the conditions that make AI reliable are the same conditions that make organizations coherent.

---

## Applied in Semantic Operations Framework

### Strategic Data: D→I Transformation

The Data → Information transformation is **measurable**:

- Schema design = injecting structural meaning into raw data
- Dimensional modeling = encoding business semantics as star schemas
- Energy cost is calculable (compute, storage, query performance)
- "Schema-on-read" does not eliminate cost—it defers and multiplies it

| DIKW Level | Storage Format | Example |
|------------|----------------|---------|
| **Data** | Raw files, unstructured text, binary blobs | `.txt`, `.log`, raw JSON, images, audio |
| **Information** | Structured/typed tables with basic schema | Parquet, Delta Lake with flat schemas, CSV with headers |
| **Knowledge** | Relational schemas, aggregations, statistical models | Normalized databases, dimensional models, trained ML models |
| **Understanding** | Semantically rich domain models with governance | DDD aggregates, canonical data models, metrics definitions, ontologies |
| **Wisdom** | Principles and constraints encoded into systems | Business rules in code, policy-as-code, leadership principles as mechanisms |

**[Strategic Data](../../SEMANTIC_OPERATIONS_FRAMEWORK/STRATEGIC_DATA/README.md):** Manages the physical D→I transformation

### Semantic Optimization: I→K→U

The Information → Knowledge → Understanding transformations require **active maintenance**:

- [Semantic Coherence](../../SEMANTIC_OPERATIONS_FRAMEWORK/SEMANTIC_OPTIMIZATION/semantic-coherence.md) = shared understanding at runtime across systems and teams
- [Semantic Optimization](../../SEMANTIC_OPERATIONS_FRAMEWORK/SEMANTIC_OPTIMIZATION/semantic-optimization.md) = balancing growth (new domains, new meaning) vs. stability (maintaining coherence)
- Corpus audit vs. Artifact audit = measuring semantic alignment at scale
- Drift detection = identifying when meaning degrades over time

Semantic Operations treats meaning as a **runtime property**, not a design-time decision.

### Symbiotic Architecture: Encoding Wisdom

**Global Architecture:** Encodes Wisdom into systems

[Wisdom as Aggregate Root](../../SEMANTIC_OPERATIONS_FRAMEWORK/SYMBIOTIC_ARCHITECTURE/wisdom-aggregate-root.md) - Wisdom → executable systems:

- [Domain-Driven Design](../../SEMANTIC_OPERATIONS_FRAMEWORK/SYMBIOTIC_ARCHITECTURE/domain-driven-design.md) = business semantics as code ([Ubiquitous Language](../../SEMANTIC_OPERATIONS_FRAMEWORK/SYMBIOTIC_ARCHITECTURE/domain-driven-design.md))
- [Bounded Contexts](../../SEMANTIC_OPERATIONS_FRAMEWORK/SYMBIOTIC_ARCHITECTURE/domain-driven-design.md) = managing semantic boundaries
- Conway's Law = organizational structure must align with data architecture
- Policy-as-code = principles encoded into system constraints

Architecture is fundamentally the most important rules encoded in the scaffolding of the entire organization.

See [Domain-Driven Design](../../SEMANTIC_OPERATIONS_FRAMEWORK/SYMBIOTIC_ARCHITECTURE/domain-driven-design.md) for how business wisdom becomes software.

---

## Physics of Meaning

The Semantic Funnel is grounded in physics—meaning is not free; it requires energy and follows physical principles.

### Energy and Entropy

| Principle | Application |
|-----------|-------------|
| **Energy** | Value requires work. Adding meaning (schema, interpretation, judgment) costs energy |
| **No free lunch** | You cannot skip levels. Someone must inject meaning at each transformation |
| **Entropy** | Semantic drift is entropy increase—meaning degrades without active maintenance |
| **Conservation** | Objects persist through transformations; only Rules determine allowed state changes |

### Determinism vs Uncertainty

Some decisions are **physics problems** (deterministic), others are **business problems** (uncertain):

- **Physics problems**: Given correct inputs and rules, the answer is determined. D→I is largely physics—apply the right schema and the transformation is repeatable
- **Business problems**: Multiple valid outcomes exist; judgment and values required. K→W is business—principles guide but do not determine

**Practical implication:** Identifying which decisions are physics problems makes them tractable. "No free lunch" and "RTFM" apply—the answer exists when the work is done to find it.

### Vectors and Direction

Everything has direction:

| Concept | Vector Metaphor |
|---------|-----------------|
| **Strategy** | A vector with direction and magnitude—where the organization is heading and how fast |
| **DDD** | A vector pointing at a domain—bounded contexts define the direction |
| **Semantics** | Meaning has direction—it flows from raw to refined, from data to wisdom |
| **Architecture** | Encoded vectors—the rules that constrain which directions are allowed |

This connects to actual vector search and embeddings—semantic similarity is literally distance in vector space. The term "[semantic coherence](../../SEMANTIC_OPERATIONS_FRAMEWORK/SEMANTIC_OPTIMIZATION/semantic-coherence.md)) refers to vectors pointing in compatible directions.

### Structure = Meaning

**"Meaning = semantics = structure"**—structure is the mathematical reality required to generate meaning:

- Understanding the business boundary (the "surface") simplifies complex problems
- Once the boundary surface is identified, it can be encoded in rules and architecture
- Surface analysis reveals which problems are simpler than they appear

See [Why Structure Matters](../../SEMANTIC_OPERATIONS_FRAMEWORK/STRATEGIC_DATA/why-structure-matters.md) for detailed exploration.

---

## Example: D→I Transformation (Deterministic)

**Data (raw bytes):**

```
192.168.1.45,1678901234,/products/shoes,200,1523
192.168.1.67,1678901235,/checkout,500,892
192.168.1.45,1678901240,/cart,200,2341
```

By itself, meaningless bytes.

**Information (structured with schema):**

| IP Address | Timestamp | URL Path | Status Code | Response Time (ms) |
|------------|-----------|----------|-------------|-------------------|
| 192.168.1.45 | 1678901234 | /products/shoes | 200 | 1523 |
| 192.168.1.67 | 1678901235 | /checkout | 500 | 892 |
| 192.168.1.45 | 1678901240 | /cart | 200 | 2341 |

**The Deterministic Nature:**

Once the schema is applied (injecting meaning), the transformation is **deterministic**:

- Column 1 = IP Address
- Column 2 = Unix timestamp
- Column 3 = URL path
- Column 4 = HTTP status code
- Column 5 = Response time

**No uncertainty** about what the information *is* once structure is defined. Same data + same schema = same information, every time.

But someone had to **add the schema from outside**—the data does not "know" it is an IP address. That is the **energy** required to create meaning.

---

## Origin

The DIKW component emerged from:

- **Milan Zeleny** (1987): "Management Support Systems: Towards Integrated Knowledge Management"
- **Russell L. Ackoff** (1989): "From Data to Wisdom"
- **Gene Bellinger, Durval Castro, Anthony Mills** (2004): "Data, Information, Knowledge, and Wisdom"

The OAR component draws from:

- **Agent-Based Modeling**: Computational models of agent interactions
- **Business Rules**: Formal specification of business logic and constraints

See [dikw-theory-comparison.md](supplemental/dikw-theory-comparison.md) for detailed comparison of DIKW versions.

---

## Related Links

### Builds On

- [Foundations](README.md) - Foundational theories for the Semantic Operations Framework

### See Also

- [What is Understanding?](what-is-understanding.md) - Understanding as process, rules, and uncertainty
- [Semantic Coherence](../../SEMANTIC_OPERATIONS_FRAMEWORK/SEMANTIC_OPTIMIZATION/semantic-coherence.md) - DIKW applied to organizational meaning
- [Semantic Optimization](../../SEMANTIC_OPERATIONS_FRAMEWORK/SEMANTIC_OPTIMIZATION/semantic-optimization.md) - Balancing growth vs. stability
- [Strategic Data](../../SEMANTIC_OPERATIONS_FRAMEWORK/STRATEGIC_DATA/README.md) - D→I transformation in practice
- [Why Structure Matters](../../SEMANTIC_OPERATIONS_FRAMEWORK/STRATEGIC_DATA/why-structure-matters.md) - Physics of structure and meaning
- [Runtime Emergence](../CURRENT_CONTEXT/AI_TRANSFORMATION/runtime-emergence.md) - Why transformation only exists at runtime
- [Symbiotic Architecture](../../SEMANTIC_OPERATIONS_FRAMEWORK/SYMBIOTIC_ARCHITECTURE/README.md) - Encoding business wisdom into systems

### Supplemental

- [DIKW Theory Comparison](supplemental/dikw-theory-comparison.md) - Zeleny vs. Ackoff vs. Bellinger

---

## Sources

- Ackoff, R.L. (1989). "From Data to Wisdom." *Journal of Applied Systems Analysis*, 16, 3-9. https://doi.org/10.1016/0378-7206(89)90062-1
- Bellinger, G., Castro, D., & Mills, A. (2004). "Data, Information, Knowledge, and Wisdom." http://www.systems-thinking.org/dikw/dikw.htm
- Rowley, J. (2007). "The wisdom hierarchy: representations of the DIKW hierarchy." *Journal of Information Science*, 33(2), 163-180. https://doi.org/10.1177/0165551506070706
- Zeleny, M. (1987). "Management Support Systems: Towards Integrated Knowledge Management." *Human Systems Management*, 7(1), 59-70.
- Agent-Based Modeling: https://en.wikipedia.org/wiki/Agent-based_model
- Business Rules: https://en.wikipedia.org/wiki/Business_rule
