# Semantic Coherence

>**Semantic Coherence** is the degree to which human and machine knowledge is available, consistent, and stable across organizations and their systems.


**Key insights:**

1. **Coherence:** A coherent system is one where humans and AI agents can operate correctly and autonomously because semantics are aligned. Rules, decisions, goals, and resources are clear, shared, and operable.

2. **Measurable:** Within the Semantic Operations Framework, coherence is a composite metric across three dimensions — not an aspiration but an operational measure with concrete targets for improvement.

3. **Continuous audit:** Coherence requires ongoing measurement to maintain — meaning fragments without investment. The Framework and AI enable audit automation through the same processes that provide acceleration.

4. **Culturally beneficial:** Coherence solves well-understood and persistent organizational problems — knowledge that cannot be found (availability), terms that mean different things to different teams (consistency), and definitions that drift silently over time (stability).

---

## How Semantic Coherence Maps to the Semantic Funnel

Semantic Coherence is the runtime measurement of whether meaning is being maintained across [Semantic Funnel](../../RESEARCH/FOUNDATIONS/semantic-funnel.md) transitions. It spans I→K→W — the transitions where uncertainty increases and meaning is most fragile.

```
  Objects:  DATA -------> INFORMATION ══════> KNOWLEDGE ══════> WISDOM
                                         │                 │
  Rules:                           Interpretive        Normative
                                   (definitions,       (governance,
                                    shared vocabulary)  drift control)
                                         │                 │
  Agents:                          Teams, AI agents    Governance teams,
                                                       leadership
```

| Element | I → K | K → W |
|---------|-------|-------|
| **Objects** | Canonical definitions, semantic contracts, ubiquitous language terms | Governance policies, coherence thresholds, corpus-artifact deltas |
| **Agents** | Teams and AI agents (consume and produce meaning at runtime) | Governance teams, leadership (define what "coherent" means) |
| **Rules** | Interpretive — availability (can it be found?), consistency (same meaning across contexts?) | Normative — stability (does meaning drift?), threshold policies (SC ≥ 0.80 = coherent) |
| **Determinism** | Medium — consistency is measurable but requires interpretive judgment to assess cross-context alignment | Low — stability thresholds and governance priorities are judgment calls; what counts as "acceptable drift" is normative |
| **Mechanism** | Formalize shared meaning as semantic contracts (not just agreements); force queries through semantic layers that enforce canonical definitions | Treat semantic changes like API changes — versioned, impact-analyzed, governed; continuous monitoring detects drift before it compounds |
| **Failure mode** | Low availability (definitions cannot be found), low consistency ("revenue" means different things to Finance vs. Sales), AI amplifies incoherence | Low stability (uncontrolled semantic drift), no governance mechanism, coherence treated as project rather than continuous process |

---

## Metric Definition

**Semantic Coherence Goal State**
> The system is optimized when shared meaning exists at runtime across domains, systems, roles, and AI boundaries — and when the data structures themselves encode the semantics of intent, incentives, and outcomes.

Semantic Coherence is a composite measure:

```
SC = (A × C × S)^(1/3)

Where:
- A = Availability (discoverability, accessibility, retrievability)
- C = Consistency (cross-context alignment)
- S = Stability (resistance to drift over time)
```

**Why geometric mean?** If any dimension collapses to zero, coherence collapses to zero. Missing availability cannot be compensated for with extra consistency. All three dimensions must be present.

**Coherence thresholds:**
- **≥0.80:** High coherence - organization can make aligned decisions
- **0.60–0.79:** Moderate coherence - friction exists, reconciliation needed
- **0.40–0.59:** Low coherence - significant misalignment, decision-making impaired
- **<0.40:** Semantic failure - silos dominate, no shared understanding

---

## The Three Components

### Semantic Availability

**Can people and systems find the meaning they need?**

Availability measures whether semantic definitions are:
- **Discoverable:** Can the definition be found?
- **Accessible:** Can it be reached when needed?
- **Retrievable:** Can it be obtained in usable form?

**Low availability symptoms:**
- Teams ask "where is the definition of X?"
- Duplicate definitions created because originals cannot be found
- Tribal knowledge hoarded by individuals
- Documentation exists but nobody knows where

**Measurement:**
```
Availability = (# concepts with accessible definitions) / (# concepts used in org)
```

---

### Semantic Consistency

**Do different systems and teams interpret concepts the same way?**

Consistency measures cross-context alignment:
- Same term has same meaning across departments
- Database field semantics match dashboard semantics
- AI models interpret concepts same as humans

**Low consistency symptoms:**
- "Revenue" means different things to Finance vs. Sales
- Same metric shows different values in different dashboards
- Integration projects discover semantic conflicts late
- AI outputs do not match human expectations

**Measurement:**
```
Consistency = 1 - (variance in definitions across contexts)
```

---

### Semantic Stability

**Does meaning stay constant over time without drift?**

Stability measures resistance to uncontrolled change:
- Definitions do not drift without governance
- Breaking changes are versioned and communicated
- Historical data remains interpretable

**Low stability symptoms:**
- Same query returns different results month-over-month
- Historical comparisons invalid due to definition changes
- Teams unaware that meanings changed
- "The data used to work, now it does not"

**Measurement:**
```
Stability = 1 - (rate of uncontrolled semantic changes)
```

See [semantic-drift.md](semantic-drift.md) for detailed analysis.

---

## Why Coherence Matters

### For Humans

Without coherence, organizations experience:
- **Decision paralysis:** Which "revenue" number is correct?
- **Coordination failure:** Teams cannot collaborate without shared vocabulary
- **Trust erosion:** If data conflicts, people stop trusting any of it
- **Knowledge silos:** Understanding locked in individuals, not shared

### For AI

A proxy operational test for Semantic Coherence is whether agents can operate correctly and autonomously.

| Component | Theoretical Definition | **Operational Test** |
|-----------|------------------------|---------------------|
| **Availability (A)** | Can people/systems find meaning? | **Can Claude Code find the definition it needs by reading files?** |
| **Consistency (C)** | Same meaning across contexts? | **Does the term mean the same thing in code, docs, schema, and conversation?** |
| **Stability (S)** | Meaning does not drift? | **Can "customer" be diffed between January and now?** |

**Why this matters:** If an AI agent cannot find, trust, or rely on definitions, neither can distributed human teams. The agent is the canary.


AI systems require coherence because:
- **Training data semantics:** Models learn from data—if semantics are inconsistent, models learn noise
- **Prompt interpretation:** LLMs interpret prompts using embedded semantics—drift causes unpredictable outputs
- **RAG retrieval:** Retrieval depends on semantic similarity—incoherent definitions fragment retrieval
- **Agent coordination:** Multi-agent systems need shared ontology to coordinate actions

**The AI amplification problem:** AI does not just suffer from low coherence—it amplifies it. An LLM trained on semantically incoherent data will confidently produce semantically incoherent outputs.

---

## Measuring Coherence

### Why Measurement Requires Audit

[Semantic Coherence](semantic-coherence.md) is not a metric to sample. It is a state to verify. The distinction matters.

**The accounting analogy:** Every month, finance teams "close the books." This is not just running reports. It is a reconciliation process that catches discrepancies, resolves ambiguities, and produces a known-good state. Only after the close can you trust the numbers for decisions.

Semantic coherence measurement works the same way:

| Accounting Close | Semantic Audit |
|------------------|----------------|
| Reconcile accounts | Reconcile definitions across systems |
| Catch booking errors | Catch semantic drift and contradictions |
| Resolve ambiguous entries | Resolve conflicting interpretations |
| Produce auditable state | Produce semantically coherent state |
| Enable trusted reporting | Enable trusted AI/human decisions |

**Why "audit" not just "measurement":**
- **Measurement** implies passive observation: read values, report numbers
- **Audit** implies active verification: check consistency, surface discrepancies, achieve reconciled state

Coherence cannot be measured without the reconciliation step. The act of measuring *is* the audit.

**The payoff:** Just as monthly close enables confident financial decisions, semantic audit enables confident operational decisions, by humans and AI agents alike. Definitions are known to be aligned before action is taken on them.

### Audit Methodology

#### Step 1: Inventory Core Concepts
Collect:
- glossary nodes  
- metric definitions  
- schema entities  
- events/features  
- ontology classes  

#### Step 2: Score Semantic Stability (0-1)
Check:
- definition drift  
- schema changes  
- semantic deltas across artifacts  

#### Step 3: Score Semantic Availability (0-1)
Check:
- catalog presence  
- clarity & completeness  
- discoverability  
- clear lineage  

#### Step 4: Score Semantic Consistency (0-1)
Check:
- cross-team alignment  
- semantic layer definitions  
- schema congruence  
- metric standardization  

#### Step 5: Compute Semantic Coherence
For each concept:

```
SC_concept = (Stability * Availability * Consistency) ** (1/3)
```

For the org:

```
SC_org = Average(SC_concept)
```

#### Step 6: Identify Hotspots
- SC < 0.4: High semantic risk  
- SC 0.4-0.7: Needs governance  
- SC > 0.7: Healthy  

#### Step 7: Recommend Semantic Optimization
Typical actions:
- glossary refinement
- [DDD](../EXPLICIT_ARCHITECTURE/domain-driven-design.md) boundary definition
- schema alignment
- metric consolidation
- ontology restructuring

### Surface Area Prioritization

Not all semantic errors are equal. **Prioritize by surface area**: the combination of visibility (how many people see it) and decision weight (how important are the decisions it influences).

Semantic errors compound. A wrong definition in an obscure script matters less than a wrong definition in a board deck. Audit effort should follow impact:

```
Impact = Visibility x Decision Weight

Where:
- Visibility = How many people/systems consume this artifact
- Decision Weight = How consequential are the decisions based on it
```

#### Surface Area Tiers

| Tier | Visibility | Decision Weight | Examples | Audit Priority |
|------|------------|-----------------|----------|----------------|
| **Critical** | Org-wide | Strategic | Board metrics, OKRs, investor reports | Continuous + deep quarterly |
| **High** | Cross-team | Tactical | Executive dashboards, team KPIs, API contracts | Monthly validation |
| **Medium** | Team-level | Operational | Team dashboards, internal reports, schemas | On-change validation |
| **Low** | Individual | Exploratory | Ad-hoc queries, personal scripts, drafts | Standard lineage only |

#### Measuring Surface Area

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

#### Prioritization Matrix

```
                    HIGH DECISION WEIGHT
                           |
         +-----------------+-----------------+
         |                 |                 |
         |   MEDIUM        |   CRITICAL      |
         |   (Schedule)    |   (Continuous)  |
         |                 |                 |
LOW      +-----------------+-----------------+ HIGH
VISIBILITY                 |                   VISIBILITY
         |                 |                 |
         |   LOW           |   HIGH          |
         |   (On-change)   |   (Monthly)     |
         |                 |                 |
         +-----------------+-----------------+
                           |
                    LOW DECISION WEIGHT
```

#### Application to Audit Steps

For each concept/artifact in the audit:

1. **Score visibility** (1-5): How many people/systems see this?
2. **Score decision weight** (1-5): How important are decisions based on this?
3. **Compute surface area**: `Visibility x Decision Weight`
4. **Prioritize**: Higher surface area = more audit depth

**Example:**

| Artifact | Visibility | Decision Weight | Surface Area | Audit Depth |
|----------|------------|-----------------|--------------|-------------|
| "Revenue" in board deck | 5 | 5 | 25 | Deep quarterly + every change |
| "Customer" API definition | 4 | 4 | 16 | Monthly + schema change triggers |
| Team utilization dashboard | 3 | 2 | 6 | On-change validation |
| Personal SQL query | 1 | 1 | 1 | Standard lineage only |

### Technical Implementation Overview

The audit methodology can be operationalized using a RAG pipeline architecture with MLops practices. This turns semantic coherence from a manual assessment into a continuous measurement system.

#### Architecture Summary

```
CORPUS (Source of Truth)
    |
    v
EMBEDDING PIPELINE (chunk, embed, store)
    |
    v
CLASSIFIER PIPELINE (4-tier: Rule -> Embed -> Graph -> LLM)
    |
    v
SC MEASUREMENT (A x C x S)^(1/3)
```

#### Component Mapping

| SC Component | Theoretical | Technical Proxy |
|--------------|-------------|-----------------|
| **Availability (A)** | Can the definition be found? | Retrieval success rate |
| **Consistency (C)** | Same meaning across contexts? | Embedding similarity |
| **Stability (S)** | Meaning does not drift? | Temporal embedding distance |

#### The 4-Tier Classifier Pipeline

1. **Rule-Based**: Completeness, required fields, naming conventions
2. **Embedding**: Vector similarity, coherence, duplicate detection
3. **Graph**: Connectivity, centrality, relationship density
4. **LLM**: Semantic fit, definition quality

For detailed implementation including code examples, DDD object mapping, ML processes (drift detection, contradiction detection), and corpus-artifact delta calculation, see [Semantic Optimization Implementation](semantic-optimization-implementation.md).

### Working Backwards Audit (Analytics Systems)

For analytics systems, standard lineage monitoring is not enough. High-visibility outputs need **inverse validation**: start from what people see and trace back to ensure semantic correctness at every layer.

#### The Problem

Traditional data audits work forward:
```
Source -> Transform -> Aggregate -> Dashboard
  v         v           v           v
  ok        ok          ok          ?
```

But semantic errors compound. A dashboard metric can be "correct" (pipeline runs, numbers appear) while being **semantically wrong** (the number does not mean what people think it means).

#### Working Backwards: The Inverse Audit

Start from high-surface-area outputs and trace back:

```
Dashboard/Report (what executives see)
       |
       v
Aggregation Logic (how it is computed)
       |
       v
Transform Layer (business rules applied)
       |
       v
Schema Definition (what the fields mean)
       |
       v
Source Data (where it actually comes from)
```

At each layer, validate:
- **Does the meaning match the intent?**
- **Is the definition consistent with canonical glossary?**
- **Would a human and an AI interpret this the same way?**

#### The Working Backwards Checklist

For each high-surface-area metric:

**1. Definition Validation**
```
[] Metric has canonical definition in glossary?
[] Definition matches what dashboard label says?
[] Business users would describe it the same way?
```

**2. Aggregation Logic Validation**
```
[] Aggregation matches definition (SUM vs AVG vs COUNT)?
[] Time grain is explicit and correct?
[] Filters match intended population?
[] Edge cases handled correctly (nulls, zeros, negatives)?
```

**3. Transform Layer Validation**
```
[] Business rules documented?
[] Rules match canonical definitions?
[] No silent assumptions embedded in code?
[] Transform logic matches what users expect?
```

**4. Schema Validation**
```
[] Field definitions match glossary?
[] Data types are appropriate?
[] Constraints enforce invariants?
[] Schema documentation exists and is current?
```

**5. Source Validation**
```
[] Source system is authoritative for this data?
[] Extraction logic is correct?
[] Source semantics understood?
[] Any semantic translation documented?
```

#### Implementation: Reverse Lineage Query

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

#### Red Flags in Working Backwards Audit

| Red Flag | What It Means | Action |
|----------|---------------|--------|
| **No canonical definition** | Metric exists without glossary entry | Add to glossary, get stakeholder sign-off |
| **Label != Definition** | Dashboard says one thing, glossary says another | Reconcile immediately: someone is wrong |
| **Undocumented aggregation** | SUM/AVG/COUNT with no explanation | Document and validate with business owner |
| **Silent filters** | WHERE clauses that exclude data without documentation | Make explicit, validate intent |
| **Multiple sources for same concept** | "Customer count" from 3 different tables | Designate authoritative source, document delta |
| **Orphan transform** | Business logic with no traceability | Connect to lineage or investigate |

#### Integration with SC Metrics

Working backwards findings feed into Semantic Coherence scores:

| Finding | SC Impact |
|---------|-----------|
| Definition mismatch | Lowers **Consistency (C)** |
| Undocumented logic | Lowers **Availability (A)** |
| Source drift | Lowers **Stability (S)** |
| All layers validated | High **SC score** for this metric |

#### The Executive Version

For board decks and executive reports, add one more validation:

**"Can you explain this number in one sentence that matches the glossary definition?"**

If the answer is no, the metric has a semantic coherence problem, even if the pipeline is running perfectly.

---

## Achieving Coherence

### Establish Canonical Definitions

Every concept needs:
- **One authoritative definition**
- **Clear owner** responsible for maintenance
- **Version control** for tracking changes
- **Accessibility** for discovery

Example:
```yaml
# UBIQUITOUS_LANGUAGE.md
active_user:
  definition: "User with at least one login in past 30 days"
  owner: "Product Analytics Team"
  version: "2.1"
  last_updated: "2024-01-15"
```

### Implement Semantic Layer

Force all queries through a semantic layer that enforces canonical definitions:
- dbt metrics layer
- LookML semantic model
- Enterprise ontology
- API contracts with semantic validation

**Benefit:** Definitions enforced at query time—cannot accidentally use wrong semantics.

### Govern Changes

Treat semantic changes like API changes:
- **Breaking changes** require major version bump
- **Migration guides** for consumers
- **Deprecation warnings** before removal
- **Impact analysis** before approval

### Monitor Continuously

Build coherence into operations:
- **Automated drift detection** (weekly)
- **Cross-team alignment surveys** (quarterly)
- **Corpus-artifact delta measurement** (monthly)
- **Coherence dashboard** for visibility

### DDD as Foundation

[Domain-Driven Design](../EXPLICIT_ARCHITECTURE/domain-driven-design.md) provides structural coherence:
- **[Bounded contexts](../EXPLICIT_ARCHITECTURE/domain-driven-design.md)** contain semantic scope
- **[Ubiquitous language](../EXPLICIT_ARCHITECTURE/domain-driven-design.md)** enforces shared vocabulary
- **[Aggregate Root](../EXPLICIT_ARCHITECTURE/semops-aggregate-root.md)** encodes invariants in code
- **Context maps** manage cross-boundary translation

---

## Coherence as Core CS Principles

Semantic Coherence is not a new concept—it is the application of fundamental software engineering principles (DRY, consistent abstractions, composability) at organizational scale. The same principles that make codebases maintainable make organizations maintainable.

See [Agentic Coding at Scale: Semantic Coherence as Core CS Principles](../EXPLICIT_ARCHITECTURE/agentic-coding-at-scale.md#semantic-coherence-as-core-cs-principles) for how these map from codebase to organization.

---

## Coherence vs. Related Concepts

| Concept | Relationship |
|---------|-------------|
| **[Semantic Operations](../README.md)** | Coherence is the **goal**; SemOps is the **practice** to achieve it |
| **[Semantic Optimization](../SEMANTIC_OPTIMIZATION/semantic-optimization.md)** | Coherence is the **target state**; optimization is the **process** of balancing growth and coherence |
| **Semantic Drift** | Drift **degrades** coherence; monitoring drift preserves stability |
| **Data Quality** | Quality is necessary but not sufficient—high-quality data can exist with incoherent semantics |
| **Data Governance** | Governance provides **mechanisms** for coherence; coherence is the **outcome** |

---

## Key Takeaways

1. **Coherence = Availability × Consistency × Stability**
   - Geometric mean because all three required
   - If any collapses, coherence collapses

2. **Coherence is runtime, not storage**
   - Coherence is not stored—conditions for it are created
   - Exists when humans and machines coordinate using shared semantics

3. **Corpus-Artifact Delta diagnoses coherence health**
   - Gap between definitions and usage reveals drift
   - Regular audits catch problems before they compound

4. **AI amplifies incoherence**
   - Models trained on inconsistent data produce inconsistent outputs
   - Coherence is prerequisite for trustworthy AI

5. **[DDD](../EXPLICIT_ARCHITECTURE/domain-driven-design.md) provides structural foundation**
   - [Bounded contexts](../EXPLICIT_ARCHITECTURE/domain-driven-design.md) scope semantics
   - [Ubiquitous language](../EXPLICIT_ARCHITECTURE/domain-driven-design.md) enforces shared vocabulary
   - Without DDD, coherence requires heroic effort

---

## Examples

### KPI Drift: When Coherence Breaks Down

Consider an organization where "revenue" means different things to different teams:

- **Finance:** Recognized revenue per GAAP (after delivery, net of returns)
- **Sales:** Booking value at contract signature
- **Product:** In-app purchase amount at transaction time

Each team builds dashboards using their definition. Leadership sees three different "revenue" numbers. AI models trained on one definition produce outputs incompatible with the other two. This is low coherence in action — the Consistency dimension has collapsed, and no amount of Availability or Stability compensates.

**Measuring the gap:** If the organization has 100 key business concepts and only 60 have consistent definitions across teams, Consistency ≈ 0.60. Combined with high Availability (0.90) and moderate Stability (0.75), the composite coherence score is SC = (0.90 × 0.60 × 0.75)^(1/3) ≈ 0.74 — moderate coherence with friction.

### The Three Forces Acting on Coherence

Coherence is not static — three forces constantly act on it:

1. **Semantic Drift (entropy):** New hires, local optimizations, shadow semantics, KPI variants — natural forces pulling meaning apart
2. **Semantic Operations (negentropy):** Detecting drift, resolving contradictions, maintaining boundaries — the counterforce
3. **Semantic Optimization (evolution):** Introducing new domains, hypotheses, and patterns — changing the shape of equilibrium while preserving integrity

The enterprise oscillates around an attractor state (Semantic Equilibrium) that is never fully reached but always shapes system behavior.

For extended examples of coherence dynamics, equilibrium forces, and KPI scenarios, see:

- [Semantic Coherence Examples](../../../Publicv1_Supplemental/examples/semantic_coherence.md)
- [Semantic Equilibrium](../../../Publicv1_Supplemental/examples/semantic-equilibrium.md)

---

## Related Links

### Part Of

- [Semantic Operations Framework](../README.md) - Parent framework

### Narrower Concepts

- [Semantic Drift](semantic-drift.md) - Threat to stability
- Semantic Availability - Discoverability component
- Semantic Stability - Resistance to drift
- Semantic Consistency - Cross-context alignment

### See Also

- [Semantic Optimization](semantic-optimization.md) - Process of achieving coherence
- [Domain-Driven Design](../EXPLICIT_ARCHITECTURE/domain-driven-design.md) - Architectural foundation
- [Provenance and Lineage](provenance-lineage-semops.md) - Structural encoding of meaning
