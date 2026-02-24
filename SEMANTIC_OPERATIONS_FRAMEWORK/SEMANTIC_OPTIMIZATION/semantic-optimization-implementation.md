---
doc_type: hub
pattern: semantic-optimization-implementation
provenance: 1p
metadata:
 pattern_type: concept
 brand-strength: high
---

# Semantic Optimization Implementation

> Technical implementation of semantic optimization: classifier pipelines, DDD object mapping, ML processes, corpus-artifact delta, and decision cadence.

This document maps theoretical concepts to concrete, technical implementations—connecting SC formulas to classifiers, RAG pipelines to coherence metrics, and DDD objects to measurement infrastructure.

---

## The Core Thesis: Patterns + Provenance

Before diving into formulas, understand the foundational claim:

> In order leverage AI to accelerate business goals, "semantically coherent" systems and organizations must be created and maintained. This requires a shift in focus and a data rigor that is difficult in theory, but now very possible because of AI. Everything is now data (code, work product, analytics, decisions). The question is: **how can organizations operate on all of it coherently?**

### The Pattern as Unit of Meaning

The **pattern** ( = [aggregate root](../EXPLICIT_ARCHITECTURE/semops-aggregate-root.md) in the [DDD](../EXPLICIT_ARCHITECTURE/domain-driven-design.md) schema) is the optimal unit for:

1. **Coherence** - Has clear semantic boundaries, maintains invariants, is broad enough to contain real meaning, and is a real, definable data structure.
2. **AI collaboration** - LLMs excel at standard patterns; they struggle with atoms that do not compose or amorphous ideas that cannot be concrete
3. **Growth** - New patterns can be added without destabilizing existing ones

**The insight:** Do not decompose the world into meaningless atoms. Do not try to implement amorphous ideas. Work at the pattern level—it is where meaning lives.

### The Provenance Split: 3p vs 1p

| Provenance | Definition | AI Relationship |
|------------|------------|-----------------|
| **3p (Third Party)** | External standard patterns (DDD, SKOS, [DIKW](../../RESEARCH/FOUNDATIONS/dikw-theory-comparison.md), etc.) | LLMs are trained on these. They are the "mean knowledge." Start here. |
| **1p (First Party)** | Organization innovation/change on top of 3p | This is the organization's differentiation. Track it explicitly. |

**The workflow:**
1. **Start with standard patterns (3p)** - Adopt SKOS, PROV-O, DAM, DDD wholesale
2. **Track changes (1p)** - Every deviation from standard is marked and justified
3. **Grow coherently** - New ideas start as orphans, get classified, promoted to stable core

**Why this matters for AI:**
- LLMs know 3p patterns deeply (they've seen millions of examples)
- LLMs struggle with unmarked 1p (they do not know what is standard versus innovation)
- Explicit provenance lets AI work confidently on 3p and flag 1p for human review

### The Meta-Design

From [SYSTEM_CONTEXT.md](https://github.com/timjmitchell/ike-semantic-ops/blob/main/docs/SYSTEM_CONTEXT.md):

> "Instead of building features, **apply whole patterns**. Start with the best standard, time-tested pattern that fits."

| Domain Type | Description | Pattern Source |
|-------------|-------------|----------------|
| **Core Domain** | The organization's differentiator—innovate here | Original thinking (1p) |
| **Supporting Domain** | Necessary but not differentiating | Adopt industry patterns (DAM, publishing) |
| **Generic Domain** | Commodity | Buy or copy wholesale (auth, CRM) |

**Current pattern adoptions:**
- **SKOS** (W3C) - concept relationships
- **PROV-O** (W3C) - lineage and provenance
- **DAM** (industry) - digital asset management
- **Dublin Core** - attribution metadata

Innovation occurs only in the core. Everything else follows proven patterns.

---

## Theory → Practice Mapping

### Theoretical Layer

| Concept | Formula | Source |
|---------|---------|--------|
| **Semantic Coherence** | SC = (A × C × S)^(1/3) | [Semantic Coherence](semantic-coherence.md) |
| **Semantic Optimization** | Maximize coherence while growing | [Semantic Optimization](README.md) |
| **Domain Pattern** | Asserted bounded context | [patterns.md](patterns.md) |

**Key theoretical claims:**

- Coherence (across humans and machines) is a necessary condition for truly impactful use of AI and data
- AI reduces the **energy barrier** to understanding, does not replace it
- Patterns are concrete structures that achieve [semantic compression](semantic-compression.md) and are well understood by LLMs
- Patterns are a **unit of meaning input** (not features) 

### Practical Layer

| Component | Implementation | Location |
|-----------|---------------|----------|
| **Classifier Pipeline** | 4-tier evaluation (Rule → Embed → Graph → LLM) | ike-semantic-ops/scripts/classifiers/ |
| **Content Classification** | 5-phase decomposition pipeline | ike-semantic-ops/docs/domain-patterns/content-classify-pattern.md |
| **Promotion Workflow** | 6-phase execution plan | ike-semantic-ops/docs/domain-patterns/concept-promotion-plan.md |
| **Schema** | PostgreSQL + pgvector + Neo4j | ike-semantic-ops/schemas/ |

---

## How Practice Operationalizes Theory

### 1. Semantic Availability (A)

**Theoretical definition:** Can people and systems find the meaning they need?

| Theoretical | Practical Measure | Classifier |
|-------------|-------------------|------------|
| Discoverable | Concept exists in KB with definition | RuleBasedClassifier.completeness |
| Accessible | Has embedding, queryable | EmbeddingClassifier (existence) |
| Retrievable | Part of connected graph | GraphClassifier.connectivity |

**Current state:** Individual measures exist but are not composed into aggregate Availability score.

### 2. Semantic Consistency (C)

**Theoretical definition:** Do different systems and teams interpret concepts the same way?

| Theoretical | Practical Measure | Classifier |
|-------------|-------------------|------------|
| Cross-context alignment | Similarity to related concepts | EmbeddingClassifier.coherence |
| Same meaning across systems | SKOS edges (broader/narrower/related) | RuleBasedClassifier.has_relationships |
| No conflicting definitions | Duplicate detection | EmbeddingClassifier.is_potential_duplicate |

**Current state:** Measures are KB-internal. No cross-system consistency check against code, dashboards, or external docs.

### 3. Semantic Stability (S)

**Theoretical definition:** Does meaning stay constant over time without drift?

| Theoretical | Practical Measure | Classifier |
|-------------|-------------------|------------|
| Resistance to drift | Version tracking | Not yet implemented |
| Controlled changes | approval_status workflow | Schema design |
| Historical interpretability | Audit trail in classification table | Classification records |

**Current state:** Stability is implicit in approval workflow. No drift detection over time.

### 4. SOKPI Components

| SOKPI Component | Practical Proxy | Status |
|-----------------|-----------------|--------|
| Knowledge Completeness (KC) | RuleBasedClassifier.completeness | ✅ Implemented |
| Semantic Alignment (SA) | EmbeddingClassifier.coherence | ✅ Implemented |
| Assumption Validity (AV) | LLMClassifier.semantic_fit | ✅ Implemented |
| Drift Cost (DC) | Not measured | ❌ Gap |

---

## Measurement Process

1. Composite SC Score

Individual A, C, S proxies exist across classifiers, but no aggregation into:

```
SC = (A × C × S)^(1/3)
```


2. Drift Detection

Stability (S) requires temporal comparison. Current system is point-in-time snapshot only.
Semantic drift detector compares current vs historical embeddings.

3. Cross-System Consistency

Consistency (C) should check across systems (code, docs, dashboards).

4. SOKPI Calculator

SOKPI components measured separately by different classifiers. `compute_sokpi` aggregating classifier outputs into single readiness score.

5. Corpus-Artifact Delta
defined in [Semantic Coherence](semantic-coherence.md):
```
Delta = |Corpus Semantics - Artifact Semantics|
```
Artifact sampling and comparison pipeline.

---

## The Classifier → Formula Mapping

This is the concrete bridge:

```
┌─────────────────────────────────────────────────────────────────────┐
│ THEORETICAL FORMULA │
│ │
│ SC = (Availability × Consistency × Stability)^(1/3) │
│ │
└─────────────────────────────────────────────────────────────────────┘
 │
 ▼
┌─────────────────────────────────────────────────────────────────────┐
│ CLASSIFIER OUTPUTS → FORMULA INPUTS │
│ │
│ Availability (A) = │
│ weighted_avg( │
│ RuleBasedClassifier.completeness, # definition exists │
│ GraphClassifier.connectivity, # reachable in graph │
│ has_embedding # queryable │
│ ) │
│ │
│ Consistency (C) = │
│ weighted_avg( │
│ EmbeddingClassifier.coherence, # similar to related │
│ 1 - EmbeddingClassifier.duplicate_sim, # not a duplicate │
│ RuleBasedClassifier.has_relationships # SKOS edges exist │
│ ) │
│ │
│ Stability (S) = │
│ weighted_avg( │
│ 1 - drift_score, # NOT YET IMPLEMENTED │
│ approval_status == 'approved', # governance gate │
│ GraphClassifier.has_hierarchy_cycle # structural integrity │
│ ) │
│ │
└─────────────────────────────────────────────────────────────────────┘
 │
 ▼
┌─────────────────────────────────────────────────────────────────────┐
│ COMPUTED SCORE │
│ │
│ SC_concept = (A × C × S)^(1/3) │
│ SC_corpus = mean(SC_concept for all approved concepts) │
│ │
└─────────────────────────────────────────────────────────────────────┘
```

---

## Practical Examples

See [semops-examples/](semops-examples/) for concrete applications:

- **[so-kb-classifier.md](semops-examples/so-kb-classifier.md)** - How the classifier pipeline operationalizes coherence evaluation
- **[patterns-promotion.md](semops-examples/patterns-promotion.md)** - How concept promotion operationalizes semantic optimization (growth + coherence balance)
- **[roi-kpi-gap.md](semops-examples/roi-kpi-gap.md)** - Example of semantic decoherence in practice

---

## How Patterns + Provenance Enable the Classifiers

The classifier pipeline operationalizes the pattern/provenance model:

### Content Classify Pattern: 5-Phase Pipeline

```
Phase 1: OWNERSHIP DETECTION ← "Is this 3p or 1p?"
 ↓
Phase 2: ATOMIC EXTRACTION ← "What patterns are here?"
 ↓
Phase 3: DEDUP & MATCHING ← "Does this match existing 3p patterns?"
 ↓
Phase 4: EDGE CLASSIFICATION ← "How does 1p relate to 3p foundations?"
 ↓
Phase 5: HUB CONSTRUCTION ← "What aggregate roots emerge?"
```

**Key insight:** Phase 1 (ownership detection) comes FIRST because provenance changes everything downstream:
- **3p concepts** → Safe to auto-match, auto-link, auto-merge
- **1p concepts** → Never auto-merge, always flag for human review

### Classifier Tiers and Provenance

| Tier | Classifier | Provenance Role |
|------|------------|-----------------|
| 1 | RuleBasedClassifier | Validates completeness regardless of provenance |
| 2 | EmbeddingClassifier | Measures similarity to known 3p patterns |
| 3 | GraphClassifier | Checks structural coherence with pattern hierarchy |
| 4 | LLMClassifier | Evaluates 1p quality (is this a real innovation or noise?) |

**The LLM tier is expensive because 1p is hard:**
- 3p can be validated against known patterns (cheap, deterministic)
- 1p requires judgment about novelty and value (expensive, probabilistic)

### The Promotion Workflow: Orphan → Stable Core

```
┌─────────────────┐ ┌─────────────────┐ ┌─────────────────┐
│ FLEXIBLE EDGE │────▶│ CLASSIFIERS │────▶│ STABLE CORE │
│ │ │ │ │ │
│ Orphan content │ │ Score against │ │ Approved │
│ New ideas │ │ 3p patterns │ │ concepts │
│ Messy input │ │ Detect 1p │ │ Pattern graph │
└─────────────────┘ └─────────────────┘ └─────────────────┘
 ↑ │
 │ ↓
 │ ┌─────────────────┐
 │ │ HUMAN REVIEW │
 │ │ │
 └───────────────│ 1p flagged │
 (iterate) │ for judgment │
 └─────────────────┘
```

**This is [Semantic Optimization](README.md) in action:**
- Growth happens at the flexible edge (orphans, new ideas)
- Coherence is maintained by the stable core (approved patterns)
- The classifier pipeline is the balancing mechanism

---

## RAG Pipeline as Semantic Optimization Infrastructure

**Key discovery:** Building/maintaining a RAG corpus IS classification. The act of chunking, embedding, and organizing content for retrieval is the same act as measuring semantic coherence.

This means: **when the RAG solution is upgraded, the semantic optimization solution is being designed simultaneously.**

See for detailed RAG architecture research.

### The Convergence

| RAG Component | Semantic Optimization Role |
|---------------|---------------------------|
| **Chunking** | Defines the "pattern" boundaries - what is a coherent unit of meaning? |
| **Embedding** | Operationalizes consistency measurement - similar embeddings = aligned semantics |
| **Metadata extraction** | Ownership detection (1p/3p), provenance tracking, SKOS relationships |
| **Vector DB** | The semantic availability layer - can agents find the meaning they need? |
| **Retrieval quality metrics** | Direct proxies for SC components (precision = consistency, recall = availability) |

### RAG Metrics → SC Components

| RAG Metric | SC Component | Interpretation |
|------------|--------------|----------------|
| **Context Precision** | Consistency (C) | Retrieved chunks are semantically aligned with the query |
| **Context Recall** | Availability (A) | All relevant meaning is retrievable |
| **Faithfulness** | Stability (S) | LLM output is grounded in the corpus (no drift from canonical definitions) |
| **Answer Relevance** | Coherence (composite) | The system produces aligned, useful outputs |

### Experimentation Variables

From the research, these RAG variables map to semantic optimization concerns:

| Variable | Semantic Impact |
|----------|-----------------|
| **Chunk size** | Pattern granularity - too small loses context, too large loses precision |
| **Chunk overlap** | Boundary handling - how much context bleeds across pattern boundaries |
| **Embedding model** | Quality of semantic similarity measurement |
| **Retrieval method** | Vector-only vs hybrid (vector + keyword) vs filtered (using SKOS metadata) |

**Insight:** Filtering retrieval using SKOS metadata (broader/narrower/related) is a direct application of pattern hierarchy for improved coherence.

### The Measurement Connection

The [SC Measurement](semantic-coherence-measurement.md) methodology describes:
1. Inventory core concepts
2. Score stability, availability, consistency
3. Compute coherence
4. Identify hotspots
5. Recommend optimization

**This IS the RAG pipeline workflow:**
1. Ingest content → Inventory
2. Chunk + embed → Make available
3. Extract metadata + classify → Score consistency
4. Query performance metrics → Measure coherence
5. Tune pipeline → Optimize

The audit playbook and RAG pipeline are the same system viewed from different angles.

---

## Multi-Signal Coherence Measurement

Beyond basic RAG metrics, the ingestion pipeline can feed purpose-built ML processes to capture deeper coherence signals. As content flows through chunking → embedding → storage, specialized analyzers can branch off.

### Architecture: Ingestion with ML Branches

```
 ┌─────────────────────────┐
 │ SENTIMENT / TONE │
 │ ANALYZER │
 │ │
 │ → Consistency of voice │
 │ → Confidence signals │
 └─────────────────────────┘
 ▲
 │
┌──────────┐ ┌──────────┐ ┌──────────┴──────────┐ ┌──────────┐
│ INGEST │────▶│ CHUNK │────▶│ EMBEDDING + │────▶│ STORE │
│ │ │ │ │ CLASSIFICATION │ │ │
│ docs │ │ split │ │ │ │ SQL │
│ code │ │ by │ │ ownership (1p/3p) │ │ Vector │
│ assets │ │ pattern │ │ SKOS edges │ │ Graph │
└──────────┘ └──────────┘ └──────────┬──────────┘ └──────────┘
 │
 ▼
 ┌─────────────────────────┐
 │ SEMANTIC DRIFT │
 │ DETECTOR │
 │ │
 │ → Compare to baseline │
 │ → Flag definition changes│
 └─────────────────────────┘
 │
 ▼
 ┌─────────────────────────┐
 │ CONTRADICTION │
 │ DETECTOR │
 │ │
 │ → Cross-chunk conflict │
 │ → Definition variance │
 └─────────────────────────┘
 │
 ▼
 ┌─────────────────────────┐
 │ COHERENCE METRICS │
 │ AGGREGATOR │
 │ │
 │ → Compute A, C, S │
 │ → Compute SC score │
 └─────────────────────────┘
```

### ML Processes and Their SC Signals

| ML Process | Input | Output Signal | SC Component |
|------------|-------|---------------|--------------|
| **Sentiment/Tone Analyzer** | Chunk text | Confidence score, hedging detection, assertion strength | Stability (S) - uncertain language may indicate unstable definitions |
| **Semantic Drift Detector** | Current embedding vs baseline | Drift magnitude, drift direction | Stability (S) - direct measurement |
| **Contradiction Detector** | Chunk pairs with same concept tag | Conflict score, conflicting passages | Consistency (C) - same concept, different claims |
| **Coverage Analyzer** | Concept graph vs chunk coverage | Orphan concepts, over/under-documented areas | Availability (A) - gaps in the corpus |
| **Complexity Analyzer** | Chunk text | Readability score, jargon density | Availability (A) - can agents parse the meaning? |
| **Provenance Classifier** | Chunk + context | 1p/3p confidence, attribution signals | Consistency (C) - is ownership clear? |
| **Topic Coherence (LDA/BERTopic)** | Corpus-wide | Topic distribution, topic drift over time | Consistency (C) - are concepts clustering correctly? |

### Concrete ML Approaches

#### 1. Semantic Drift Detection

**Goal:** Measure how much a concept's meaning has changed over time.

```python
# Pseudocode
def detect_drift(concept_id: str) -> float:
 """
 Compare current embedding to historical baseline.
 Returns drift score 0.0 (stable) to 1.0 (completely changed).
 """
 current_embedding = get_current_embedding(concept_id)
 baseline_embedding = get_baseline_embedding(concept_id) # From approval date

 # Cosine distance
 drift = 1 - cosine_similarity(current_embedding, baseline_embedding)

 # Optional: Compare definition text with sentence-level similarity
 current_def = get_current_definition(concept_id)
 baseline_def = get_baseline_definition(concept_id)
 text_drift = semantic_similarity(current_def, baseline_def)

 return weighted_average(drift, text_drift)
```

**Tools:** sentence-transformers, custom embedding comparison

#### 2. Contradiction Detection

**Goal:** Find chunks that make conflicting claims about the same concept.

```python
# Pseudocode
def find_contradictions(concept_id: str) -> list[Contradiction]:
 """
 Find chunks tagged with same concept that make conflicting claims.
 """
 chunks = get_chunks_for_concept(concept_id)

 contradictions = []
 for chunk_a, chunk_b in combinations(chunks, 2):
 # Use NLI model (Natural Language Inference)
 # Labels: entailment, neutral, contradiction
 result = nli_model.predict(chunk_a.text, chunk_b.text)

 if result.label == "contradiction" and result.confidence > 0.8:
 contradictions.append(Contradiction(
 concept=concept_id,
 chunk_a=chunk_a,
 chunk_b=chunk_b,
 confidence=result.confidence
 ))

 return contradictions
```

**Tools:** transformers (DeBERTa-v3 for NLI), cross-encoder models

#### 3. Tone/Confidence Analysis

**Goal:** Detect hedging, uncertainty, and assertion strength.

```python
# Pseudocode
def analyze_confidence(chunk: str) -> ConfidenceSignals:
 """
 Detect linguistic signals of certainty/uncertainty.
 """
 # Hedging words: "might", "perhaps", "possibly", "seems"
 hedging_score = count_hedging_words(chunk) / word_count(chunk)

 # Assertion strength: "is", "must", "always" vs "could", "sometimes"
 assertion_ratio = strong_assertions(chunk) / total_claims(chunk)

 # Sentiment polarity (positive/negative/neutral)
 sentiment = sentiment_model.predict(chunk)

 return ConfidenceSignals(
 hedging=hedging_score,
 assertion_strength=assertion_ratio,
 sentiment=sentiment
 )
```

**Tools:** VADER, TextBlob, custom hedging lexicon, fine-tuned classifiers

#### 4. Topic Coherence (Corpus-Level)

**Goal:** Measure whether concepts cluster as expected.

```python
# Pseudocode
def measure_topic_coherence -> TopicCoherenceReport:
 """
 Use topic modeling to see if concept clusters match SKOS hierarchy.
 """
 # Extract topics from corpus
 topics = bertopic_model.fit_transform(all_chunks)

 # Compare to expected clusters (from SKOS broader/narrower)
 expected_clusters = get_skos_clusters

 # Measure alignment
 alignment_score = compare_clusters(topics, expected_clusters)

 # Identify misaligned concepts
 misaligned = find_misaligned_concepts(topics, expected_clusters)

 return TopicCoherenceReport(
 alignment=alignment_score,
 misaligned_concepts=misaligned,
 suggested_reclassifications=suggest_fixes(misaligned)
 )
```

**Tools:** BERTopic, Top2Vec, coherence scoring (Gensim)

### Aggregating into SC Score

All ML signals feed into the coherence aggregator:

```python
def compute_semantic_coherence(concept_id: str) -> float:
 """
 Aggregate all signals into SC = (A × C × S)^(1/3)
 """
 # Availability (A)
 a_coverage = coverage_analyzer.score(concept_id) # Is it documented?
 a_complexity = 1 - complexity_analyzer.score(concept_id) # Is it readable?
 a_retrievable = retrieval_tester.score(concept_id) # Can RAG find it?
 A = weighted_avg([a_coverage, a_complexity, a_retrievable])

 # Consistency (C)
 c_contradictions = 1 - contradiction_detector.score(concept_id)
 c_topic_alignment = topic_coherence.score(concept_id)
 c_provenance_clarity = provenance_classifier.confidence(concept_id)
 C = weighted_avg([c_contradictions, c_topic_alignment, c_provenance_clarity])

 # Stability (S)
 s_drift = 1 - drift_detector.score(concept_id)
 s_confidence = confidence_analyzer.assertion_strength(concept_id)
 s_approved = 1.0 if concept.approval_status == 'approved' else 0.5
 S = weighted_avg([s_drift, s_confidence, s_approved])

 # Geometric mean
 SC = (A * C * S) ** (1/3)

 return SC
```

### Implementation Phases

| Phase | Focus | ML Components | Effort |
|-------|-------|---------------|--------|
| **Phase 1** | Baseline metrics | Drift detection (embedding comparison), basic coverage | Low |
| **Phase 2** | Consistency signals | Contradiction detection (NLI), provenance classification | Medium |
| **Phase 3** | Corpus-level | Topic coherence (BERTopic), cross-concept analysis | Medium |
| **Phase 4** | Nuanced signals | Tone/confidence analysis, complexity scoring | Low-Medium |
| **Phase 5** | Full aggregation | SC calculator, dashboard, alerting | Medium |

### Open Design Questions

1. **Training data:** Are labeled examples needed for contradiction detection, or can off-the-shelf NLI models be used?

2. **Baseline establishment:** When should the "baseline" for drift detection be snapshotted? At approval time? At each release?

3. **Weighting:** How should the different signals within each SC component be weighted? Equal? Learned?

4. **Thresholds:** What drift magnitude triggers a review? What contradiction confidence is actionable?

5. **Compute budget:** Which ML processes run on every ingest vs batch scheduled?

---

## Hypothesis: Decision Cadence Drives Measurement Cadence

**Core question:** When should coherence be measured? Constantly? On-demand? Scheduled?

**Key insight:** The value of a coherence score is realized at the moment of decision. Measuring faster than decisions are made creates noise and overwhelms humans; measuring slower creates risk of operating on stale information.

### The Hypotheses

> **H1: Coherence measurement frequency should match decision frequency.**
>
> "Constant optimization" is meaningless if humans only make decisions monthly. But if AI agents are making micro-decisions in real-time, coherence becomes operating infrastructure, not a report.

> **H2: In agentic coding contexts, coherence measurement becomes infrastructure, not reporting.**
>
> When agents are constantly deciding which files to read, how to interpret requirements, and what patterns to apply, coherence isn't a "score"—it's the operating substrate.

> **H3: The "audit playbook" is really multiple playbooks calibrated to different decision cycles.**
>
> Strategic audits are deep and infrequent. Continuous audits are lightweight and constant. Different contexts need different playbooks.

### Decision Cadence Spectrum

| Context | Decision Frequency | Coherence Measurement | Mode |
|---------|-------------------|----------------------|------|
| **Strategic planning** | Quarterly/Annually | Batch audit before planning cycle | Report |
| **Product roadmap** | Monthly | Monthly coherence dashboard | Dashboard |
| **Engineering sprints** | Bi-weekly | Sprint boundary checks | Gate |
| **Agentic coding** | Real-time (continuous) | Live context substrate | Infrastructure |

### Two Modes of Coherence

**Mode 1: Coherence as Report**
- Scheduled batch measurement
- Produces scores, identifies hotspots
- Consumed by humans in planning cycles
- Matches: strategic, product, governance decisions
- Example: Quarterly semantic coherence audit with remediation plan

**Mode 2: Coherence as Infrastructure**
- Real-time or near-real-time
- Embedded in context-sharing systems
- Consumed by agents and humans during work
- Matches: agentic coding, rapid iteration teams
- Example: DX Hub that provides live context to all agents

### The Agentic Coding Case

When AI agents are making micro-decisions constantly:
- Which files to read?
- How to interpret requirements?
- What patterns to apply?
- Is this 3p that can be trusted or 1p that should be flagged?

Coherence is not a "score"—it is the **operating substrate**. The agents need:
- Live access to approved patterns (stable core)
- Real-time provenance signals (is this 3p that can be trusted or 1p that should be flagged?)
- Immediate feedback on consistency (does this match existing definitions?)

### Example: DX Hub Pattern (ai-workflow-kit)

The **ai-workflow-kit** repo (soon to be `semantic-ops/dx`) is a concrete implementation of the DX Hub pattern. It serves both human developers AND AI agents as the shared context substrate.

**See:**
- [ai-workflow-kit/docs/PROCESS.md](https://github.com/timjmitchell/ai-workflow-kit/blob/main/docs/PROCESS.md) - Development processes, ADR aggregation
- [ai-workflow-kit/docs/INFRASTRUCTURE.md](https://github.com/timjmitchell/ai-workflow-kit/blob/main/docs/INFRASTRUCTURE.md) - Shared services, stack decisions
- [ai-workflow-kit ADR-0001](https://github.com/timjmitchell/ai-workflow-kit/blob/main/docs/decisions/ADR-0001-semops-organization-migration.md) - Multi-repo architecture

#### How It Works

```
┌─────────────────────────────────────────────────────────────────────┐
│ DX HUB (ai-workflow-kit / semantic-ops/dx) │
├─────────────────────────────────────────────────────────────────────┤
│ │
│ AGGREGATED CONTEXT (from all repos): │
│ ├── ADR_INDEX.md - Cross-repo architecture decisions │
│ ├── INFRASTRUCTURE.md - Services, ports, stack preferences │
│ ├── UBIQUITOUS_LANGUAGE.md - Domain term definitions │
│ ├── GLOBAL_ARCHITECTURE.md - System map, bounded contexts │
│ └── PROCESS.md - Workflows, naming conventions, audit processes │
│ │
│ DISTRIBUTION (to all repos via CLI): │
│ ├── `workflow init` - Install slash commands + experiment templates │
│ ├── `/prime-global` - Load DX Hub context into agent session │
│ ├── `/plan` - Reference global docs when planning │
│ └── `/fixauth` - Troubleshoot with reference to PROCESS.md │
│ │
│ COHERENCE ENFORCEMENT: │
│ ├── Stack decision tree (when to use what) │
│ ├── Naming conventions (entity IDs, predicates, branches) │
│ ├── Schema change process (high-impact, all repos affected) │
│ └── Deprecated repo warnings (audit for stale references) │
│ │
└─────────────────────────────────────────────────────────────────────┘
```

#### The Multi-Repo Pattern (Like Departments in a Company)

From [ADR-0001](https://github.com/timjmitchell/ai-workflow-kit/blob/main/docs/decisions/ADR-0001-semops-organization-migration.md):

| Repo | Role | Bounded Context |
|------|------|-----------------|
| **dx** (ai-workflow-kit) | Developer/Agent experience | Process, tooling, global docs |
| **orchestrator** (ike-semantic-ops) | Infrastructure owner | Schema, services, knowledge graph |
| **docs** (semops-docs) | Theory, implementation docs | Concepts, patterns, research |
| **publisher** (ike-publisher) | Content workflow | Blog agents, publishing |
| **sites** (resumator) | Frontend, deployed apps | User-facing surfaces |
| **data-kit** (data-systems-toolkit) | Data eng/DS utilities | Product analytics |

**This is exactly like departments in a company:**
- Each repo has a bounded context (clear responsibility)
- The DX Hub provides shared context (like company-wide policies)
- Cross-repo coordination follows explicit processes
- Each repo maintains local autonomy within global constraints

#### Why This Matters for Coherence

The DX Hub pattern operationalizes coherence at the **organizational level**:

1. **Availability:** All repos can access shared definitions via `/prime-global`
2. **Consistency:** Single source of truth for infrastructure, processes, terminology
3. **Stability:** Schema changes require explicit ADRs, notify all dependent repos

**The experiment framework** (worktrees, parallel Docker experiments) is also coherence infrastructure—it enables testing changes in isolation before merging to the stable core.

**Key insight:** The DX Hub is both a coherence enforcement mechanism (rules that constrain agents) and a coherence measurement point (tracking what decisions were made via ADRs, detecting drift via audits).

### Multiple Audit Playbooks

Different decision cycles need different audit depths:

| Playbook | Cadence | Depth | Compute | Output |
|----------|---------|-------|---------|--------|
| **Strategic Audit** | Quarterly | Full corpus, all ML signals | High (batch) | Executive summary + remediation plan |
| **Sprint Audit** | Bi-weekly | Changed concepts only, basic metrics | Medium | Go/no-go for sprint |
| **Continuous Audit** | Real-time | Hot path concepts, lightweight checks | Low (streaming) | Live warnings, agent context |

### Testing the Hypotheses

To validate these hypotheses:

1. **Instrument decision points:** Track when humans and agents actually make semantic decisions
2. **Correlate with value:** Measure whether coherence scores at decision time predict decision quality
3. **Experiment with cadences:** Try different measurement frequencies, observe cognitive load vs value
4. **Compare modes:** Run the same team with "coherence as report" vs "coherence as infrastructure"

### Open Questions for This Hypothesis

- What is the latency budget for "real-time" coherence in agentic contexts? 100ms? 1s? 10s?
- How can "decision moments" be detected to trigger measurement?
- Can optimal cadence be derived from decision frequency automatically?
- What is the cost/benefit of continuous measurement vs on-demand?
- How can cognitive overload from constant coherence signals be prevented?

---

## SC Metrics Mapped to DDD Objects

The theoretical SC formula operates on abstract "concepts." The practical system operates on concrete DDD objects. Here's the explicit mapping.

### The DDD Object Model (from UBIQUITOUS_LANGUAGE.md)

| DDD Object | Role | SC Relevance |
|------------|------|--------------|
| **Concept** (Aggregate Root) | Stable semantic unit - durable ideas, principles, IP | **The thing being measured** - SC is computed per concept |
| **Entity** (DAM Layer) | Concrete artifact - blog post, code file, diagram | **Manifestation of concepts** - what is sampled for corpus-artifact delta |
| **Edge** (SKOS/PROV-O) | Typed relationship between objects | **Structure** - provides graph connectivity for Consistency |
| **Classification** | Audit record from classifier pipeline | **The measurement** - stores A, C, S proxy scores |
| **Embedding** | Vector representation of concept/entity | **Similarity substrate** - enables Consistency measurement |

### SC Components → DDD Measurements

#### Availability (A): "Can agents find the meaning they need?"

| Theoretical Measure | DDD Object | Field/Query | Classifier |
|---------------------|------------|-------------|------------|
| **Discoverable** | `concept` | `definition IS NOT NULL AND definition != ''` | RuleBasedClassifier.completeness |
| **Accessible** | `concept` | `embedding IS NOT NULL` (can be vector-searched) | EmbeddingClassifier (existence check) |
| **Retrievable** | `concept` + `edge` | Graph connectivity - can reach from approved concepts | GraphClassifier.connectivity |
| **Documented** | `entity` | Entities with `primary_concept_id = concept.id` exist | COUNT(entities per concept) |

**Aggregate A Score:**
```python
def compute_availability(concept_id: str) -> float:
 concept = get_concept(concept_id)

 # Discoverable: has definition
 discoverable = 1.0 if concept.definition else 0.0

 # Accessible: has embedding
 accessible = 1.0 if concept.embedding is not None else 0.0

 # Retrievable: connected to approved graph
 retrievable = graph_classifier.connectivity_score(concept_id)

 # Documented: has entities
 entity_count = count_entities_for_concept(concept_id)
 documented = min(1.0, entity_count / 3) # 3+ entities = fully documented

 return weighted_avg([discoverable, accessible, retrievable, documented],
 weights=[0.3, 0.2, 0.3, 0.2])
```

#### Consistency (C): "Do different systems interpret concepts the same way?"

| Theoretical Measure | DDD Object | Field/Query | Classifier |
|---------------------|------------|-------------|------------|
| **Cross-context alignment** | `concept` + `concept` | Embedding similarity between related concepts | EmbeddingClassifier.coherence |
| **No conflicting definitions** | `concept` | Duplicate detection (similarity > 0.95) | EmbeddingClassifier.is_potential_duplicate |
| **SKOS structure** | `concept_edge` | Has broader/narrower/related edges | RuleBasedClassifier.has_relationships |
| **Entity alignment** | `entity` → `concept` | Entities tagged to same concept have similar embeddings | Entity embedding variance per concept |

**Aggregate C Score:**
```python
def compute_consistency(concept_id: str) -> float:
 # Cross-context alignment: similar to neighbors
 coherence = embedding_classifier.coherence_score(concept_id)

 # No duplicates: not suspiciously similar to another concept
 duplicate_penalty = embedding_classifier.max_duplicate_similarity(concept_id)
 no_duplicates = 1.0 if duplicate_penalty < 0.95 else 0.0

 # SKOS structure: has relationships
 has_skos = 1.0 if rule_classifier.has_relationships(concept_id) else 0.5

 # Entity alignment: entities for this concept cluster together
 entity_variance = compute_entity_embedding_variance(concept_id)
 entity_aligned = 1.0 - min(1.0, entity_variance)

 return weighted_avg([coherence, no_duplicates, has_skos, entity_aligned],
 weights=[0.4, 0.2, 0.2, 0.2])
```

#### Stability (S): "Does meaning stay constant over time?"

| Theoretical Measure | DDD Object | Field/Query | Classifier |
|---------------------|------------|-------------|------------|
| **Governance gate** | `concept` | `approval_status = 'approved'` | Direct field check |
| **Version control** | `entity` | `version` field tracks changes | Version count/history |
| **Drift resistance** | `concept` | `embedding` compared to baseline at `approved_at` | DriftDetector (compare embeddings over time) |
| **Structural integrity** | `concept_edge` | No hierarchy cycles | GraphClassifier.has_hierarchy_cycle |

**Aggregate S Score:**
```python
def compute_stability(concept_id: str) -> float:
 concept = get_concept(concept_id)

 # Governance: is approved
 approved = 1.0 if concept.approval_status == 'approved' else 0.5

 # Version stability: not frequently changing
 entity_versions = get_version_history(concept_id)
 version_stability = 1.0 - min(1.0, len(entity_versions) / 10) # Many versions = instability

 # Drift: embedding hasn't moved since approval
 drift_score = drift_detector.compute_drift(concept_id) # 0.0 = stable, 1.0 = drifted
 drift_resistance = 1.0 - drift_score

 # Structural: no cycles
 no_cycles = 0.0 if graph_classifier.has_hierarchy_cycle(concept_id) else 1.0

 return weighted_avg([approved, version_stability, drift_resistance, no_cycles],
 weights=[0.3, 0.2, 0.3, 0.2])
```

### Composite SC Calculation

```python
def compute_semantic_coherence(concept_id: str) -> SemanticCoherenceScore:
 A = compute_availability(concept_id)
 C = compute_consistency(concept_id)
 S = compute_stability(concept_id)

 # Geometric mean - if any collapses, coherence collapses
 SC = (A * C * S) ** (1/3)

 return SemanticCoherenceScore(
 concept_id=concept_id,
 availability=A,
 consistency=C,
 stability=S,
 coherence=SC,
 threshold_met=SC >= 0.80
 )

def compute_corpus_coherence -> float:
 """Aggregate SC across all approved concepts."""
 approved_concepts = get_concepts_by_status('approved')
 scores = [compute_semantic_coherence(c.id).coherence for c in approved_concepts]
 return mean(scores)
```

---

## Corpus-Artifact Delta: Theory to DDD

The **corpus-artifact delta** measures the gap between canonical definitions and real-world usage:

```
Delta = |Corpus Semantics - Artifact Semantics|
```

### Mapping to DDD Objects

| Theoretical Term | DDD Object | What It Represents |
|-----------------|------------|-------------------|
| **Corpus** | `concept` (approved) | Canonical definitions - the "should be" |
| **Artifacts** | `entity` + external files | Real-world manifestations - the "is" |
| **Delta** | Embedding distance | Semantic gap between definition and usage |

### The Stable Core vs Flexible Edge Connection

From UBIQUITOUS_LANGUAGE.md:

> **Stable Core:** Concepts + entities with `primary_concept_id` set
> **Flexible Edge:** Orphan entities (`primary_concept_id = NULL`)

**This maps directly to corpus-artifact:**

| Zone | DDD Objects | Corpus-Artifact Role |
|------|-------------|---------------------|
| **Stable Core** | Approved concepts + linked entities | The **corpus** - canonical, governed |
| **Flexible Edge** | Orphan entities, pending concepts | **Artifacts at risk** - may have drifted |
| **External** | Code files, dashboards, docs not yet ingested | **Unmonitored artifacts** - unknown delta |

### Operationalizing the Delta

#### Step 1: Define Corpus (Source of Truth)

```python
def get_corpus -> list[Concept]:
 """Canonical definitions = approved concepts with definitions."""
 return db.query(Concept).filter(
 Concept.approval_status == 'approved',
 Concept.definition.isnot(None)
 ).all
```

#### Step 2: Sample Artifacts

Artifacts come from multiple sources:

| Artifact Source | DDD Path | Ingestion Method |
|-----------------|----------|------------------|
| **Entity content** | `entity.filespec.uri` → file | Read file, extract text, embed |
| **Code files** | External repos | Scan for concept mentions, embed context |
| **Dashboards** | External BI tools | Extract metric definitions, embed |
| **Conversations** | Slack/docs | Sample discussions, embed |

```python
def sample_artifacts(concept_id: str) -> list[ArtifactSample]:
 """Get real-world usage samples for a concept."""
 samples = []

 # 1. Linked entities (already in system)
 entities = get_entities_for_concept(concept_id)
 for entity in entities:
 content = read_file(entity.filespec.uri)
 samples.append(ArtifactSample(
 source="entity",
 source_id=entity.id,
 text=content,
 embedding=embed(content)
 ))

 # 2. Code mentions (external scan)
 code_mentions = scan_codebase_for_concept(concept_id)
 for mention in code_mentions:
 samples.append(ArtifactSample(
 source="code",
 source_id=mention.file_path,
 text=mention.context,
 embedding=embed(mention.context)
 ))

 # 3. Add more sources as needed...

 return samples
```

#### Step 3: Compute Delta

```python
def compute_corpus_artifact_delta(concept_id: str) -> DeltaScore:
 """
 Measure semantic distance between canonical definition and actual usage.
 Low delta = artifacts match corpus (good)
 High delta = drift between definition and reality (bad)
 """
 concept = get_concept(concept_id)
 corpus_embedding = concept.embedding # Canonical definition

 artifacts = sample_artifacts(concept_id)
 if not artifacts:
 return DeltaScore(delta=0.0, sample_count=0, warning="No artifacts sampled")

 # Compute distance from each artifact to corpus
 distances = []
 for artifact in artifacts:
 # Cosine distance: 0.0 = identical, 2.0 = opposite
 distance = cosine_distance(corpus_embedding, artifact.embedding)
 distances.append(distance)

 # Aggregate: mean distance
 mean_delta = mean(distances)
 max_delta = max(distances)

 # Identify outliers (high delta = drifted artifacts)
 outliers = [a for a, d in zip(artifacts, distances) if d > 0.5]

 return DeltaScore(
 concept_id=concept_id,
 delta=mean_delta,
 max_delta=max_delta,
 sample_count=len(artifacts),
 outliers=outliers
 )
```

#### Step 4: Integrate into SC (Enhanced Formula)

The full Coherence KPI from semantic-coherence.md:

```
Coherence_KPI = (A × C × S)^(1/3) × (1 - Delta_normalized)
```

```python
def compute_full_coherence_kpi(concept_id: str) -> float:
 sc = compute_semantic_coherence(concept_id)
 delta = compute_corpus_artifact_delta(concept_id)

 # Normalize delta to 0-1 (assuming max practical delta is 1.0)
 delta_normalized = min(1.0, delta.delta)

 # Apply delta penalty
 coherence_kpi = sc.coherence * (1 - delta_normalized)

 return coherence_kpi
```

### Classification Pipeline: Where Delta Fits

The classifier pipeline already writes to the `classification` table. Delta can be added as another score:

| Classifier | What It Measures | SC Component |
|------------|------------------|--------------|
| RuleBasedClassifier | Completeness, format | **Availability** |
| EmbeddingClassifier | Similarity, duplicates | **Consistency** |
| GraphClassifier | Connectivity, cycles | **Consistency + Stability** |
| LLMClassifier | Quality, semantic fit | **All components** |
| **DeltaClassifier** (new) | Corpus-artifact gap | **Delta penalty** |

```python
class DeltaClassifier(BaseClassifier):
 """Measures corpus-artifact delta for concepts."""

 classifier_id = "delta-v1"

 def classify(self, concept_id: str) -> Classification:
 delta = compute_corpus_artifact_delta(concept_id)

 return Classification(
 target_type="concept",
 target_id=concept_id,
 classifier_id=self.classifier_id,
 scores={
 "delta_mean": delta.delta,
 "delta_max": delta.max_delta,
 "sample_count": delta.sample_count,
 "promotion_ready": delta.delta < 0.3 # Low delta = healthy
 },
 rationale=f"Sampled {delta.sample_count} artifacts. Mean delta: {delta.delta:.2f}"
 )
```

### Visual: SC + Delta → DDD Objects

```
┌─────────────────────────────────────────────────────────────────────────────┐
│ SEMANTIC COHERENCE MEASUREMENT │
│ │
│ CORPUS (Stable Core) ARTIFACTS (Flexible Edge + External) │
│ ┌────────────────────┐ ┌────────────────────────────────────┐ │
│ │ concept │ │ entity (linked) │ │
│ │ - id │◄──delta───▶│ - primary_concept_id = concept.id │ │
│ │ - definition │ │ - filespec.uri → content │ │
│ │ - embedding │ │ - embedding │ │
│ │ - approval_status │ ├────────────────────────────────────┤ │
│ │ │ │ entity (orphan) │ │
│ │ Measured by: │◄──delta───▶│ - primary_concept_id = NULL │ │
│ │ • A (Availability)│ │ - (potential new pattern) │ │
│ │ • C (Consistency) │ ├────────────────────────────────────┤ │
│ │ • S (Stability) │ │ external files │ │
│ └────────────────────┘◄──delta───▶│ - code repos │ │
│ │ - dashboards │ │
│ SC = (A × C × S)^(1/3) │ - conversations │ │
│ └────────────────────────────────────┘ │
│ │
│ COHERENCE_KPI = SC × (1 - Delta) │
│ │
│ ────────────────────────────────────────────────────────────────────────── │
│ │
│ CLASSIFICATION TABLE (Audit Trail) │
│ ┌────────────────────────────────────────────────────────────────────────┐ │
│ │ target_id | classifier_id | scores | rationale │ │
│ │───────────┼───────────────┼───────────────────────┼────────────────────│ │
│ │ semantic- │ rule-based-v1 │ {completeness: 0.95} │ "Definition..." │ │
│ │ coherence │ embedding-v1 │ {coherence: 0.88} │ "Similar to..." │ │
│ │ │ graph-v1 │ {connectivity: 0.92} │ "4 hops to..." │ │
│ │ │ delta-v1 │ {delta_mean: 0.15} │ "Sampled 12..." │ │
│ └────────────────────────────────────────────────────────────────────────┘ │
│ │
│ Aggregate into: │
│ • A = avg(completeness, connectivity, ...) │
│ • C = avg(coherence, has_relationships, ...) │
│ • S = avg(approved, no_cycles, drift_resistance, ...) │
│ • Delta = delta_mean │
│ • COHERENCE_KPI = (A × C × S)^(1/3) × (1 - Delta) │
│ │
└─────────────────────────────────────────────────────────────────────────────┘
```

### Current Gaps and Next Steps

| Gap | DDD Impact | Action |
|-----|-----------|--------|
| **No DeltaClassifier** | Cannot measure corpus-artifact gap | Implement `DeltaClassifier` |
| **No external artifact ingestion** | Only measure internal entities | Build code/dashboard scanners |
| **No composite SC view** | Must aggregate manually | Create `semantic_coherence_scores` view |
| **No drift baseline** | Cannot compute temporal S | Store embedding snapshots at `approved_at` |
| **Classification scores not aggregated** | Individual scores, no composite | Build `compute_sc` function |

---

## Open Questions

1. **Weighting:** How should the different classifier outputs be weighted when computing A, C, S?

2. **Thresholds:** What SC score indicates "healthy"? The theory says ≥0.80, but do the proxy measures align?

3. **Cross-system scope:** What external systems should be measured for Consistency? Code repos? Dashboards? Customer-facing docs?

4. **Drift baseline:** Is historical data available, or should drift measurement start from now?

5. **Priority:** Which gap to close first—composite score, drift detection, or cross-system consistency?

6. **1p Detection Accuracy:** How reliable is ownership detection? What is the false positive rate (marking 3p as 1p = wasted human review) vs false negative (auto-merging 1p innovation)?

7. **RAG-SC Integration:** Should SC scoring be implemented as part of the RAG pipeline evaluation suite? Should Ragas metrics be used as SC proxies?

---

## Related Links

### Concepts

- [Semantic Optimization](README.md) - Core concept
- [Semantic Coherence](semantic-coherence.md) - Target state definition
- [SC Measurement](semantic-coherence-measurement.md) - Audit methodology
- [Patterns](patterns.md) - Unit of meaning
- [Pattern Operations](pattern-operations.md) - Promotion loop, optimization cycle

### Practice

- [semops-core domain-patterns/](https://github.com/semops-ai/semops-core/tree/main/docs/domain-patterns) - Pattern documentation
- [semops-core UBIQUITOUS_LANGUAGE.md](https://github.com/semops-ai/semops-core/blob/main/schemas/UBIQUITOUS_LANGUAGE.md) - Schema definitions

### DX Hub

- [semops-dx-orchestrator PROCESS.md](https://github.com/semops-ai/semops-dx-orchestrator/blob/main/docs/PROCESS.md) - Development processes
- [semops-dx-orchestrator GLOBAL_ARCHITECTURE.md](https://github.com/semops-ai/semops-dx-orchestrator/blob/main/docs/GLOBAL_ARCHITECTURE.md) - Multi-repo architecture
