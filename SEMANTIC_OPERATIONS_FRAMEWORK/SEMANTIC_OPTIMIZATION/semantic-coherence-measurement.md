---
doc_type: hub
pattern: semantic-coherence-measurement
provenance: 1p
metadata:
  pattern_type: concept
  brand-strength: high
# 
---
# Semantic Coherence Measurement

> How to measure semantic coherence: audit methodology, technical implementation, and operational validation.

---

## Why Measurement Requires Audit

[Semantic Coherence](semantic-coherence.md) is not a metric to sample—it is a state to verify. The distinction matters.

**The accounting analogy:** Every month, finance teams "close the books." This is not just running reports—it is a reconciliation process that catches discrepancies, resolves ambiguities, and produces a known-good state. Only after the close can you trust the numbers for decisions.

Semantic coherence measurement works the same way:

| Accounting Close | Semantic Audit |
|------------------|----------------|
| Reconcile accounts | Reconcile definitions across systems |
| Catch booking errors | Catch semantic drift and contradictions |
| Resolve ambiguous entries | Resolve conflicting interpretations |
| Produce auditable state | Produce semantically coherent state |
| Enable trusted reporting | Enable trusted AI/human decisions |

**Why "audit" not just "measurement":**
- **Measurement** implies passive observation—read values, report numbers
- **Audit** implies active verification—check consistency, surface discrepancies, achieve reconciled state

Coherence cannot be measured without the reconciliation step. The act of measuring *is* the audit.

**The payoff:** Just as monthly close enables confident financial decisions, semantic audit enables confident operational decisions—by humans and AI agents alike. Definitions are known to be aligned before action is taken on them.

---

## 1. Components of Semantic Coherence

Semantic Coherence measures how reliably meaning propagates across an organization. It has three components:

### Stability

How stable a concept's definition remains across time, teams, systems, and business processes.

**Indicators:**
- Version drift
- Conflicting definitions
- Misaligned schemas

### Availability

How easily a concept's canonical definition can be located and used.

**Indicators:**
- Presence in glossary/ontology
- Catalog indexing
- Lineage visibility
- Access and permissions clarity

### Consistency

How uniformly a concept is interpreted across systems or domains.

**Indicators:**
- Consistent metric logic
- Aligned grain and keys
- [DDD](../EXPLICIT_ARCHITECTURE/domain-driven-design.md) boundary compliance
- Absence of conflicting usage patterns

### The Formula

```
Semantic Coherence = (Stability × Availability × Consistency) ^ (1/3)
```

The geometric mean ensures that a low score in one dimension lowers overall coherence.

---

## 3. Audit Methodology

### **Step 1 — Inventory Core Concepts**
Collect:
- glossary nodes  
- metric definitions  
- schema entities  
- events/features  
- ontology classes  

---

### **Step 2 — Score Semantic Stability (0–1)**
Check:
- definition drift  
- schema changes  
- semantic deltas across artifacts  

---

### **Step 3 — Score Semantic Availability (0–1)**
Check:
- catalog presence  
- clarity & completeness  
- discoverability  
- clear lineage  

---

### **Step 4 — Score Semantic Consistency (0–1)**
Check:
- cross-team alignment  
- semantic layer definitions  
- schema congruence  
- metric standardization  

---

### **Step 5 — Compute Semantic Coherence**
For each concept:

```
SC_concept = (Stability * Availability * Consistency) ** (1/3)
```

For the org:

```
SC_org = Average(SC_concept)
```

---

### **Step 6 — Identify Hotspots**
- SC < 0.4 → High semantic risk  
- SC 0.4–0.7 → Needs governance  
- SC > 0.7 → Healthy  

---

### **Step 7 — Recommend Semantic Optimization**
Typical actions:
- glossary refinement
- [DDD](../EXPLICIT_ARCHITECTURE/domain-driven-design.md) boundary definition
- schema alignment
- metric consolidation
- ontology restructuring

---

## 3.5 Surface Area Prioritization

Not all semantic errors are equal. **Prioritize by surface area**—the combination of visibility (how many people see it) and decision weight (how important are the decisions it influences).

### The Principle

Semantic errors compound. A wrong definition in an obscure script matters less than a wrong definition in a board deck. Audit effort should follow impact:

```
Impact = Visibility × Decision Weight

Where:
- Visibility = How many people/systems consume this artifact
- Decision Weight = How consequential are the decisions based on it
```

### Surface Area Tiers

| Tier | Visibility | Decision Weight | Examples | Audit Priority |
|------|------------|-----------------|----------|----------------|
| **Critical** | Org-wide | Strategic | Board metrics, OKRs, investor reports | Continuous + deep quarterly |
| **High** | Cross-team | Tactical | Executive dashboards, team KPIs, API contracts | Monthly validation |
| **Medium** | Team-level | Operational | Team dashboards, internal reports, schemas | On-change validation |
| **Low** | Individual | Exploratory | Ad-hoc queries, personal scripts, drafts | Standard lineage only |

### Measuring Surface Area

**Visibility proxies:**
- Dashboard/report view counts
- API call frequency
- Document access logs
- Email distribution list size
- Downstream dependency count

**Decision weight proxies:**
- Connected to OKRs/KPIs?
- Referenced in executive communications?
- Feeds into financial reporting?
- Triggers automated actions?
- Used in customer-facing systems?

### Prioritization Matrix

```
                    HIGH DECISION WEIGHT
                           │
         ┌─────────────────┼─────────────────┐
         │                 │                 │
         │   MEDIUM        │   CRITICAL      │
         │   (Schedule)    │   (Continuous)  │
         │                 │                 │
LOW      ├─────────────────┼─────────────────┤ HIGH
VISIBILITY                 │                   VISIBILITY
         │                 │                 │
         │   LOW           │   HIGH          │
         │   (On-change)   │   (Monthly)     │
         │                 │                 │
         └─────────────────┼─────────────────┘
                           │
                    LOW DECISION WEIGHT
```

### Application to Audit Steps

For each concept/artifact in the audit:

1. **Score visibility** (1-5): How many people/systems see this?
2. **Score decision weight** (1-5): How important are decisions based on this?
3. **Compute surface area**: `Visibility × Decision Weight`
4. **Prioritize**: Higher surface area = more audit depth

**Example:**

| Artifact | Visibility | Decision Weight | Surface Area | Audit Depth |
|----------|------------|-----------------|--------------|-------------|
| "Revenue" in board deck | 5 | 5 | 25 | Deep quarterly + every change |
| "Customer" API definition | 4 | 4 | 16 | Monthly + schema change triggers |
| Team utilization dashboard | 3 | 2 | 6 | On-change validation |
| Personal SQL query | 1 | 1 | 1 | Standard lineage only |

### Why This Matters

Without prioritization, audits become:
- **Too expensive** — checking everything equally wastes resources
- **Too slow** — critical errors wait in queue behind trivial ones
- **Too noisy** — alert fatigue from low-impact findings

Surface area prioritization ensures:
- **Critical artifacts get continuous attention**
- **Audit resources match business impact**
- **Semantic errors are caught where they matter most**

---

## 4. Technical Implementation Overview

The audit methodology can be operationalized using a RAG pipeline architecture with MLops practices. This turns semantic coherence from a manual assessment into a continuous measurement system.

### Architecture Summary

```
CORPUS (Source of Truth)
    │
    ▼
EMBEDDING PIPELINE (chunk, embed, store)
    │
    ▼
CLASSIFIER PIPELINE (4-tier: Rule → Embed → Graph → LLM)
    │
    ▼
SC MEASUREMENT (A × C × S)^(1/3)
```

### Component Mapping

| SC Component | Theoretical | Technical Proxy |
|--------------|-------------|-----------------|
| **Availability (A)** | Can the definition be found? | Retrieval success rate |
| **Consistency (C)** | Same meaning across contexts? | Embedding similarity |
| **Stability (S)** | Meaning does not drift? | Temporal embedding distance |

### The 4-Tier Classifier Pipeline

1. **Rule-Based** — Completeness, required fields, naming conventions
2. **Embedding** — Vector similarity, coherence, duplicate detection
3. **Graph** — Connectivity, centrality, relationship density
4. **LLM** — Semantic fit, definition quality

For detailed implementation including code examples, DDD object mapping, ML processes (drift detection, contradiction detection), and corpus-artifact delta calculation, see [Semantic Optimization Implementation](semantic-optimization-implementation.md).

### Operational Test: The Agent Canary

The ultimate test of SC is operational: **Can an AI agent find and use definitions correctly?**

If the agent cannot find it, neither can distributed human teams. The agent is the canary.

See [Semantic Coherence](semantic-coherence.md) for the full definition and key insights about SC as an operational test.

---

## 5. Working Backwards Audit (Analytics Systems)

For analytics systems, standard lineage monitoring is not enough. High-visibility outputs need **inverse validation**—start from what people see and trace back to ensure semantic correctness at every layer.

### The Problem

Traditional data audits work forward:
```
Source → Transform → Aggregate → Dashboard
  ↓         ↓           ↓           ↓
 ✓         ✓           ✓           ?
```

But semantic errors compound. A dashboard metric can be "correct" (pipeline runs, numbers appear) while being **semantically wrong** (the number does not mean what people think it means).

### Working Backwards: The Inverse Audit

Start from high-surface-area outputs and trace back:

```
Dashboard/Report (what executives see)
       │
       ▼
Aggregation Logic (how it is computed)
       │
       ▼
Transform Layer (business rules applied)
       │
       ▼
Schema Definition (what the fields mean)
       │
       ▼
Source Data (where it actually comes from)
```

At each layer, validate:
- **Does the meaning match the intent?**
- **Is the definition consistent with canonical glossary?**
- **Would a human and an AI interpret this the same way?**

### Surface Area Prioritization

Not all outputs need the same scrutiny. Prioritize by **surface area**—how many people see and use the metric:

| Surface Area | Examples | Audit Frequency |
|--------------|----------|-----------------|
| **Very High** | Board deck metrics, quarterly reports, OKRs | Every change + quarterly deep audit |
| **High** | Executive dashboards, team KPIs | Monthly validation |
| **Medium** | Operational dashboards, team reports | On-change validation |
| **Low** | Ad-hoc queries, exploration | Standard lineage only |

**Measurement:** Surface area can be measured via:
- Dashboard view counts
- Report distribution lists
- Downstream dependencies
- Decision impact (which decisions rely on this?)

### The Working Backwards Checklist

For each high-surface-area metric:

**1. Definition Validation**
```
□ Metric has canonical definition in glossary?
□ Definition matches what dashboard label says?
□ Business users would describe it the same way?
```

**2. Aggregation Logic Validation**
```
□ Aggregation matches definition (SUM vs AVG vs COUNT)?
□ Time grain is explicit and correct?
□ Filters match intended population?
□ Edge cases handled correctly (nulls, zeros, negatives)?
```

**3. Transform Layer Validation**
```
□ Business rules documented?
□ Rules match canonical definitions?
□ No silent assumptions embedded in code?
□ Transform logic matches what users expect?
```

**4. Schema Validation**
```
□ Field definitions match glossary?
□ Data types are appropriate?
□ Constraints enforce invariants?
□ Schema documentation exists and is current?
```

**5. Source Validation**
```
□ Source system is authoritative for this data?
□ Extraction logic is correct?
□ Source semantics understood?
□ Any semantic translation documented?
```

### Implementation: Reverse Lineage Query

```python
def working_backwards_audit(dashboard_metric_id: str) -> AuditReport:
    """
    Trace a metric backwards from dashboard to source.
    Validate semantic correctness at each layer.
    """
    report = AuditReport(metric_id=dashboard_metric_id)

    # 1. Get the metric as displayed
    metric = get_dashboard_metric(dashboard_metric_id)
    report.display_label = metric.label
    report.display_description = metric.description

    # 2. Get canonical definition
    canonical = get_glossary_definition(metric.concept_id)
    report.definition_match = semantic_match(
        metric.description,
        canonical.definition
    )

    # 3. Trace aggregation logic
    agg_logic = get_aggregation_logic(metric.source_query)
    report.aggregation_documented = agg_logic.has_documentation
    report.aggregation_matches_definition = validate_aggregation(
        agg_logic,
        canonical
    )

    # 4. Trace transform layer
    transforms = get_upstream_transforms(agg_logic.source_table)
    for t in transforms:
        transform_check = TransformValidation(
            name=t.name,
            has_documentation=t.has_docs,
            rules_match_glossary=validate_transform_rules(t, canonical)
        )
        report.transforms.append(transform_check)

    # 5. Trace to source
    sources = get_source_tables(transforms)
    for s in sources:
        source_check = SourceValidation(
            name=s.name,
            is_authoritative=s.is_authoritative_for(canonical.concept_id),
            extraction_documented=s.has_extraction_docs,
            semantic_translation_documented=s.has_translation_docs
        )
        report.sources.append(source_check)

    # 6. Compute overall semantic validity
    report.semantic_validity_score = compute_validity_score(report)

    return report
```

### Red Flags in Working Backwards Audit

| Red Flag | What It Means | Action |
|----------|---------------|--------|
| **No canonical definition** | Metric exists without glossary entry | Add to glossary, get stakeholder sign-off |
| **Label ≠ Definition** | Dashboard says one thing, glossary says another | Reconcile immediately—someone is wrong |
| **Undocumented aggregation** | SUM/AVG/COUNT with no explanation | Document and validate with business owner |
| **Silent filters** | WHERE clauses that exclude data without documentation | Make explicit, validate intent |
| **Multiple sources for same concept** | "Customer count" from 3 different tables | Designate authoritative source, document delta |
| **Orphan transform** | Business logic with no traceability | Connect to lineage or investigate |

### Integration with SC Metrics

Working backwards findings feed into Semantic Coherence scores:

| Finding | SC Impact |
|---------|-----------|
| Definition mismatch | Lowers **Consistency (C)** |
| Undocumented logic | Lowers **Availability (A)** |
| Source drift | Lowers **Stability (S)** |
| All layers validated | High **SC score** for this metric |

### The Executive Version

For board decks and executive reports, add one more validation:

**"Can you explain this number in one sentence that matches the glossary definition?"**

If the answer is no, the metric has a semantic coherence problem—even if the pipeline is running perfectly.

---

## Related Links

### Concepts
- [Semantic Coherence](semantic-coherence.md) — What is being measured
- [Semantic Optimization](README.md) — The goal (optimize coherence while growing)

### Implementation
- [Semantic Optimization Implementation](semantic-optimization-implementation.md) — Full technical details (classifiers, DDD mapping, ML processes)
- [Pattern Operations](pattern-operations.md) — Promotion loop, governance

### Context
- [Patterns](patterns.md) — Unit of meaning being measured
- [Provenance & Lineage](provenance-lineage-semops.md) — Tracking origin and change

