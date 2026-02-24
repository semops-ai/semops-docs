---
doc_type: hub

pattern: semantic-compression

provenance: 1p

metadata:
 pattern_type: concept
 brand_strength: high
---

# Semantic Compression

> The investment of energy and meaning to encode knowledge into reusable structures that enable efficient understanding.

**Status:** 1p Concept | **Date:** 2025-12-10

---

## Definition

In information theory and NLP, **semantic compression** refers to reducing information while preserving meaning—replacing specific terms with general ones, encoding messages into "semantic normal forms," or using abstraction as lossy compression. The lattice theory of information captures this as "abstraction as a form of lossy semantic compression" where semantic entropy is reduced while core meaning persists.

**This framework extends that foundation:** Semantic compression is the process of encoding meaning into structured forms that reduce the cognitive and computational cost of future understanding. It requires upfront investment—energy, expertise, design decisions—but yields **reusable potential energy** that makes subsequent analysis, reasoning, and AI operations dramatically more efficient.

```
Raw information → [Compression effort] → Structured form → [Efficient retrieval/reasoning]
```

The compression is not lossless—choices are made about what to preserve and what to discard. Those choices embed meaning. The resulting structure becomes a **semantic anchor** that grounds future interpretation.

---

## The Energy Trade-off

| Phase | Energy Required | Who Pays | When |
|-------|-----------------|----------|------|
| **Compression** | High | Domain experts, architects, designers | Once (or rarely) |
| **Usage** | Low | Everyone who queries, reasons, builds | Repeatedly |

**This is computational amortization applied to meaning:**
- Without structure: pay the interpretation cost on every query
- With structure: pay the compression cost once, benefit forever

The more a compressed structure is reused, the greater the return on the compression investment.

---

## Examples from the Framework

### 1. Relational Databases / Dimensional Models {concept: dimensional-modeling}

The canonical example of semantic compression:

```
Raw events (billions of rows)
 → Star schema (facts + dimensions)
 → Efficient aggregation, joins, time intelligence
```

A well-designed schema encodes:
- Conceptual boundaries
- Domain semantics
- Cardinality and constraints
- Reasoning pathways
- Task affordances

**The compression:** Business logic that defines what "fiscal quarter" means, how products roll up to categories, which customers count as "active"—all encoded into the dimensional structure.

**The payoff:** Queries that would take hours on raw data return in seconds. The schema *means* something.

### 2. Domain Patterns / Bounded Contexts {concept: domain-pattern}

[DDD](../EXPLICIT_ARCHITECTURE/domain-driven-design.md) patterns compress organizational knowledge:

```
Tribal knowledge + implicit rules + edge cases
 → Bounded context with explicit invariants
 → Reusable domain model
```

A [bounded context](../EXPLICIT_ARCHITECTURE/domain-driven-design.md) does not just organize code—it **compresses the semantics** of a business domain into a form that:
- New team members can learn
- AI agents can navigate
- Systems can enforce

**The compression:** Deciding what "Customer" means in this context, what operations are allowed, what invariants must hold.

**The payoff:** Coherent behavior across the entire context. Eliminates ambiguity around definitions.

### 3. Code Repositories / Everything is GitHub {concept: everything-is-github}

Repositories compress organizational knowledge:

```
Decisions, discussions, experiments, rollbacks
 → Git history with PRs, commits, branches
 → Queryable lineage graph
```

Each repository is a **semantic function** that compresses:
- What this bounded context knows
- How it relates to other contexts
- What conventions apply here
- What decisions led to this state

**The compression:** The folder structure, CLAUDE.md, frontmatter schemas—all design decisions that encode meaning.

**The payoff:** AI agents can operate with minimal context. Claude Code reads 5K tokens of repo context instead of 100K tokens of raw information.

### 4. Code Classes / Libraries {concept: abstractions}

Object-oriented and functional abstractions:

```
Repeated logic + edge cases + variations
 → Class hierarchy or function library
 → Reusable, testable, composable
```

A well-designed class encodes:
- What this abstraction *is* (identity)
- What operations it supports (interface)
- What invariants it maintains (constraints)
- How it relates to other abstractions (composition)

**The compression:** The naming, the API design, the encapsulation decisions.

**The payoff:** Other code can use the abstraction without understanding its internals.

### 5. Abstractions (1p Concept) {concept: abstractions}

Our specific framing of entity and metric abstractions:

| Type | Compression | Example |
|------|-------------|---------|
| **Entity Abstraction** | Many instances → one conceptual identity | "Album" = multiple SKUs, editions, regions |
| **Metric Abstraction** | Many events → one semantic summary | "Unique Users" = identity resolution + time window + filters |

**The compression:** Defining what counts as "the same thing" (entity) or "how this is measured" (metric).

**The payoff:** Humans think at the concept level. The abstraction handles the complexity.

### 6. Compound Metrics / Formulas / Algorithms

Mathematical compression of domain logic:

```
Complex multi-step reasoning
 → Formula or algorithm
 → Executable, testable, reusable
```

Examples:
- **[Semantic Coherence](semantic-coherence.md):** `SC = (A × C × S)^(1/3)` — compresses three dimensions into one score
- **ROI:** `(Gain - Cost) / Cost` — compresses business value into one ratio
- **PageRank:** Compresses link structure into authority scores

**The compression:** The mathematical form itself. Choosing *this* formula, not another, embeds domain judgment.

**The payoff:** Complex reasoning becomes a lookup or calculation.

---

## Why This Matters for AI

AI agents benefit disproportionately from semantic compression:

| Without Compression | With Compression |
|--------------------|------------------|
| Parse raw logs to understand events | Query dimensional model |
| Infer business rules from code | Read explicit bounded context |
| Guess at relationships | Navigate typed edges |
| Hallucinate definitions | Ground in schema |

**The key insight:** LLMs are trained on compressed knowledge (Wikipedia, textbooks, code). They struggle with uncompressed information (raw logs, messy wikis, implicit rules).

When semantically compressed context is provided:
- Less tokens needed (efficiency)
- More meaning per token (density)
- Fewer misinterpretations (coherence)
- Better grounding (reliability)

---

## Compression and the Three Pillars

Semantic compression directly supports [semantic coherence](semantic-coherence.md):

| Pillar | How Compression Helps |
|--------|----------------------|
| **Availability (A)** | Compressed forms are discoverable, queryable, indexed |
| **Consistency (C)** | The compression decision enforces one interpretation |
| **Stability (S)** | Compressed structures resist drift—they are explicit |

**Uncompressed knowledge drifts.** Different people interpret the same raw data differently over time.

**Compressed knowledge persists.** The schema, the pattern, the formula—they encode the interpretation and make it durable.

---

## The Cost of Not Compressing

What happens when the compression step is skipped:

| Symptom | Root Cause |
|---------|------------|
| "What do you mean by X?" | No canonical definition |
| Conflicting dashboards | No shared dimensional model |
| AI hallucinations | No grounded schema |
| Onboarding takes months | Tribal knowledge, no explicit patterns |
| Every query is expensive | No pre-computed aggregations |
| Semantic drift over time | No explicit contracts |

**The false economy:** "We do not have time to design the schema" → "We spend 10x the time interpreting raw data"

### The False Promises

Technology vendors keep selling the idea that compression can be skipped:

| Promise | Reality |
|---------|---------|
| "Schema-on-read means you do not need structure upfront" | Structure is still needed—just deferred to query time (much more expensive) |
| "NoSQL gives you flexibility" | It gives chaos—schema moved to application code (inconsistently) |
| "Data lakes store everything" | Storage is cheap, meaning is expensive—unmaintained lakes become swamps |
| "Modern BI tools handle anything" | They work best with proper dimensional models—always have |
| "AI will figure out the schema" | AI needs schema to function—cannot bootstrap meaning from nothing |

**The actual cost:**

| Approach | Investment | When Paid |
|----------|------------|-----------|
| **Upfront compression** | Days to weeks | Once (or rarely) |
| **Deferred compression** | Months to years of analyst time | Repeated for every query |
| **No compression** | Permanent semantic debt | Paid forever in confusion |

---

## The Historical Pattern

We've swung from structure to chaos and back—repeatedly:

| Era | Approach | Result |
|-----|----------|--------|
| **1970s-1990s** | Relational databases enforced schema | Slow but correct |
| **2000-2010** | NoSQL—"Schema is overhead" | Fast but fragmented |
| **2008-2016** | Schema-on-read—"Store now, figure it out later" | More volume, less understanding |
| **2016-2020** | Semantic renaissance (Iceberg, dbt, Looker) | Rediscovered that schema IS truth |
| **2021-Present** | LLMs—"AI will figure it out" | AI amplifies semantic errors |

**The pattern:** Every time technology advances, fundamentals are forgotten. Then they are rediscovered painfully.

**Better technology raises the bar for discipline, not lowers it:**
- More powerful tools → more ways to build wrong things faster
- More data → more opportunities for semantic chaos
- More AI → more amplification of errors

Technology cannot solve discipline problems

---

## When to Compress

Not everything needs compression. The investment should match the reuse:

| Reuse Level | Compression Investment | Example |
|-------------|----------------------|---------|
| **One-off analysis** | None | Ad-hoc query on raw data |
| **Repeated team use** | Light | Shared SQL, documented metrics |
| **Cross-team use** | Medium | Dimensional model, bounded context |
| **Org-wide use** | Heavy | Canonical schema, formal ontology |
| **AI consumption** | Heavy | Structured metadata, typed relationships |

**Rule of thumb:** If an AI agent will read it, compress it.

---

## Related Links

### Theory
- [Semantic Coherence](semantic-coherence.md) - What compression enables
- [patterns.md](patterns.md) - Patterns as compressed meaning
- [semantic-operations.md](semantic-operations.md) - Operating on compressed knowledge

### Examples
- [abstractions.md](../atoms/abstractions.md) - Entity and metric abstractions
- [dimensional-modeling.md](../atoms/dimensional-modeling.md) - The canonical compression pattern
- [everything-is-github.md](everything-is-github.md) - Repos as semantic functions

### Practice
- [UBIQUITOUS_LANGUAGE.md](https://github.com/timjmitchell/ike-semantic-ops/blob/main/schemas/UBIQUITOUS_LANGUAGE.md) - Compressed domain definitions

### Citations
- [Semantic Compression with Information Lattice Learning](https://arxiv.org/abs/2404.03131) - Lattice theory framing of "abstraction as lossy semantic compression"
- [Semantic Compression With Large Language Models](https://arxiv.org/abs/2304.12512) - LLM-based semantic compression for context reduction
- [Semantic compression - Wikipedia](https://en.wikipedia.org/wiki/Semantic_compression) - Overview of NLP and information-theoretic definitions
