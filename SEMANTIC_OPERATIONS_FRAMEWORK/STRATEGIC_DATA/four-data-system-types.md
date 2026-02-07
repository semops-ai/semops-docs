---
doc_type: hub

pattern: four-data-system-types

provenance: 1p

metadata:
    pattern_type: concept
    brand_strength: low
---

# The Four Data System Types
> A framework for more precisely categorizing data systems to better understand vague reference terminology and tie it to discreet concepts that align with concrete data objects and integration patterns.

**Why it matters:**
Understanding these four types clarifies where system boundaries naturally fall, how the same underlying data manifests differently across system types, and why the relative importance of each type varies by industry and business model.

---

## The Four Types

**The four systems:**

- **Analytics Data System Type** - Instrumented for analysis (uses OLAP as query interface)
- **Application Data System Type** - Operational, transactional (uses OLTP as query interface)
- **Enterprise Work System Type** - Unstructured knowledge artifacts
- **Enterprise Record System Type** - Canonical systems of record

### Analytics Data System

**The Analytical Function includes:**
- **Traditional Analytics** - BI, reports, dashboards, exploratory analysis
- **ML & AI Training** - Model development, feature engineering, fine-tuning, RLHF
- **Runtime Analytics** - A/B testing, experiments, real-time predictions, online learning
  - **Dual nature:** Operational delivery (serve to customer) + Analytical feedback (improve models)

**Full concept:** Analytics Data System

**What it is:**
The complete analytical data ecosystem - including ingestion, storage, transformation, modeling, governance, and ML/AI pipelines. Uses OLAP as its query interface.

**Examples:**
- Data warehouse + dbt transformations + Airflow orchestration + BI tools
- Event streams (Kafka) → Data lake (S3/Iceberg) → OLAP engine (Snowflake) → Looker
- ML platform: Feature store + training pipelines + model registry + inference

**Key properties:**
- Uses OLAP for read-heavy analytical queries (but OLAP is just the query layer)
- Includes ingestion, storage, table formats, transformation, orchestration, governance, ML/AI
- Dimensional modeling (star schema, conformed dimensions)
- Historical time-series analysis
- Instrumented with analytical intent

**Governance approach:**
- **Own the source instrumentation** - Control what gets captured
- **Model semantic relationships BEFORE ETL** - Design dimensional model upfront
- **Enforce conformed dimensions** - Ensure consistency across analytics
- **Measure [semantic coherence](../SEMANTIC_OPTIMIZATION/semantic-coherence.md)** - Track metric definitions, prevent drift

**Integration pattern:** Receives data from Application Data Systems via ETL/ELT

```
Application Data System (OLTP interface)
  ↓ Extract (CDC, batch, streaming)
  ↓ Transform (normalize → dimensional)
  ↓ Load
Analytics Data System (OLAP interface)
```

**Challenge:** Same events, different physics
**Solution:** CDC + dimensional modeling

**Why this matters:**
From [data-silos](data-silos.md):
> "Application data and analytics data are the same data with different physics - same events, different constraints (latency, purpose, windowing, aggregation)."

The divide between Application Data Systems and Analytics Data Systems is query optimization, not semantics. Both represent the same business reality.

---

## Application Data System

**Full concept:** Application Data System

**What it is:**
The complete operational data ecosystem that powers running business applications - including databases, services, APIs, and transactional workloads. Uses OLTP as its query interface.

**Examples:**
- E-commerce: Order service + inventory service + payment service, each with OLTP databases
- SaaS: User service + subscription service + feature flags, with caching and event streams
- Banking: Core banking system + account service + transaction service

**Key properties:**
- Uses OLTP for low-latency reads/writes (but OLTP is just the query layer)
- Includes [bounded contexts](../SYMBIOTIC_ARCHITECTURE/domain-driven-design.md), microservices, event sourcing, APIs, caching
- Current state + recent history
- Lives within bounded contexts ([DDD](../SYMBIOTIC_ARCHITECTURE/domain-driven-design.md))

**Governance approach:**
- **Do not try to unify** - Respect bounded contexts
- Use **context maps** (DDD) to understand relationships
- Apply **[anti-corruption layers](../SYMBIOTIC_ARCHITECTURE/ddd-acl-governance-aas.md)** for integration
- Maintain semantic boundaries explicitly

**Integration pattern:** Feeds Analytics Data System; uses anti-corruption layers for cross-context integration

```
Application Context A ──┐
                        ├── Anti-Corruption Layer ──→ Application Context B
Application Context C ──┘

Application Data System ──→ CDC/ETL ──→ Analytics Data System
```

**Challenge:** Bounded contexts have local semantics that shouldn't be unified
**Solution:** Context maps, ACLs, event-driven integration

**Why this matters:**
Application Data Systems are the operational heartbeat. Each application owns its domain model and cannot be forced into a unified schema without violating DDD principles.

---

## Enterprise Work System

**Full concept:** Enterprise Work System

**What it is:**
Unstructured knowledge artifacts created by knowledge workers - documents, communications, designs, recordings.

**Examples:**
- Notion, Confluence, Google Docs
- Design files, marketing copy, PDFs
- Emails, Slack threads, meeting recordings
- Support tickets with conversation history

**Key properties:**
- 95% of human meaning lives here
- Semantically dense but **structurally weak**
- No schema, no lineage, no formal constraints
- High ambiguity, rapidly changing

**Governance approach:**
- **Add structure through [SemOps](../README.md)** - Do not expect it to magically integrate
- **Build semantic scaffolding** - Ontologies, taxonomies, knowledge graphs
- **Use AI to extract/encode meaning** - But only with governance
- **Accept heterogeneity** - Do not force unified schema

**Integration pattern:** AI extraction + semantic scaffolding → Knowledge Graph

```
Enterprise Work System (unstructured)
  ↓ AI extraction (NER, relationships)
  ↓ Ontology mapping
  ↓ Governance validation
Knowledge Graph / Semantic Layer
```

**Challenge:** No schema, high ambiguity
**Solution:** SemOps + ontologies

**Why this matters:**
This is where organizational knowledge lives. Pundits call this "dark data" or "siloed data," but it is not siloed - it is **unstructured meaning without semantic scaffolding**.

It cannot be "integrated" in the traditional sense. It must be **modeled** and given structure.

---

## Enterprise Record System

**Full concept:** Enterprise Record System

**What it is:**
Canonical systems of record where semantics are tightest and most formally encoded - Finance, ERP, HRIS.

**Examples:**
- General ledger, AP/AR (Accounting)
- ERP systems (SAP, Oracle, NetSuite)
- HRIS (Workday, BambooHR)
- CRM as system of record (Salesforce)

**Key properties:**
- **Highest semantic integrity** in the organization
- Explicit constraints, unforgiving domain rules
- Consistent cross-domain semantics
- Regulatory enforcement (GAAP, SOX, GDPR)

**Governance approach:**
- **Recognize as canonical truth** for their domains
- **Do not try to replace** - Integrate with translation
- **Learn from their governance discipline** - They enforce semantics
- **Use as validation source** - Analytics should reconcile to systems of record

**Integration pattern:** Serves as canonical reference for validation

```
Analytics Data System metrics
  ↓ Reconcile
  ↓ Compare
Enterprise Record System (canonical truth)
```

**Challenge:** Different domains, different models
**Solution:** Anti-corruption layers, periodic reconciliation

**Why this matters:**
Finance data works because:
- Explicit domain model (double-entry accounting)
- Formal constraints (debits = credits)
- Regulatory enforcement
- Unambiguous semantics (what is "revenue"?)
- [Governance as strategy](governance-as-strategy.md) (audit trails required)

**The lesson:** When semantics are explicit and enforced, "silos" do not exist - even across departments.

---

## Cross-System Integration

When all four systems need to feed a unified analytical layer:

```
Application Data System  ─┐
Analytics Data System    ─┤
Enterprise Work System   ─├──→ Data Warehouse/Lake ──→ Semantic Layer ──→ BI Tools
Enterprise Record System ─┘
```

**Challenge:** Heterogeneous sources with different physics
**Solution:** Dimensional modeling + semantic layer (dbt, Looker)

| System Type | Integration Challenge | Integration Pattern |
|-------------|----------------------|---------------------|
| **Application** | Bounded context semantics | CDC + dimensional modeling |
| **Analytics** | Already analytical | Direct integration |
| **Work** | No schema, high ambiguity | AI extraction + ontologies |
| **Record** | Canonical but rigid | [Anti-corruption layers](../SYMBIOTIC_ARCHITECTURE/ddd-acl-governance-aas.md) |

---

## Industry Lens

> A company's industry, business model, and "surface variability" will determine their mix of the properties, importance, and integration patterns for each system.

### Software/SaaS Pattern

**Mix:** 60% Application + 25% Analytics + 10% Work + 5% Record

**Characteristics:**
- Application data IS the product
- Heavy analytics instrumentation for product decisions
- Lightweight systems of record (startup financials)
- Work data in collaboration tools

**Diagnostic signals:**
- Real-time event streams dominant
- Product analytics tools (Amplitude, Mixpanel)
- Cloud-native databases (Postgres, MongoDB)

### Traditional Enterprise Pattern

**Mix:** 40% Record + 30% Application + 20% Analytics + 10% Work

**Characteristics:**
- ERP/Finance systems as backbone
- Custom applications built on SOR data
- Analytics extracted from SOR
- Formal governance processes

**Diagnostic signals:**
- On-premise ERP (SAP, Oracle)
- Batch ETL processes
- Data warehouse for reporting
- Strong compliance requirements

### Professional Services Pattern

**Mix:** 50% Work + 30% Record + 15% Application + 5% Analytics

**Characteristics:**
- Knowledge work artifacts dominant
- Timekeeping/billing as systems of record
- Lightweight applications (project management)
- Analytics mostly financial

**Diagnostic signals:**
- Heavy document management
- CRM for client relationships
- Accounting/timekeeping systems
- Weak analytics instrumentation

### Consumer Tech Pattern

**Mix:** 70% Analytics + 20% Application + 10% Work

**Characteristics:**
- Analytics data IS the signal
- Application data supports user experience
- Product-led, data-driven decisions
- Minimal systems of record (early stage)

**Diagnostic signals:**
- Event streaming infrastructure (Kafka)
- Product analytics platforms
- A/B testing frameworks
- Real-time dashboards

---

## Disambiguating "Data Silos" and "Enterprise"

### The "Data Silos" Myth

From [data-silos](data-silos.md):

> "The pundit mistake is lumping all 'enterprise data' together. These are **fundamentally different data types requiring different approaches**."

**Common confusion:**
- Treating all data as if it should be "unified" in one system
- Expecting same governance approach for operational vs analytical vs unstructured data
- Missing that "silos" are often appropriate [bounded contexts](../SYMBIOTIC_ARCHITECTURE/domain-driven-design.md)

**Reality:**
- Each system type has different optimization targets
- Integration patterns differ by system type
- Governance requirements are not one-size-fits-all

### Diagnostic Framework

**A company's mix of the four systems reveals:**

| Mix Pattern | Industry/Business Model | Example |
|-------------|-------------------------|---------|
| 60% Application + 25% Analytics + 15% Other | Software/SaaS | Product-led growth company |
| 40% Systems of Record + 30% Application + 20% Analytics | Traditional Enterprise | Manufacturing, Banking |
| 50% Enterprise Work + 30% Systems of Record | Professional Services | Consulting, Legal, Accounting |
| 70% Analytics + 20% Application | Consumer Tech/Media | Social media, Content platforms |

From the ingestion-boundary diagnostic framework.

---

## Anti-Patterns

### Treating All Data the Same

**Problem:** Applying application data governance to analytics data (or vice versa)

**Example:**
- Forcing dimensional model into operational database
- OR trying to do analytics on normalized OLTP schema

**Solution:** Recognize different optimization targets

### Trying to Unify Everything

**Problem:** Creating "one unified customer table" across all systems

**Why it fails:**
- Violates [bounded context](../SYMBIOTIC_ARCHITECTURE/domain-driven-design.md) principles
- Forces lowest-common-denominator schema
- Creates tight coupling
- Governance nightmare

**Solution:** Keep contexts separate, integrate at analytical layer

### Ignoring Enterprise Work Data

**Problem:** Only focusing on structured data (OLTP + OLAP)

**Reality:**
- 95% of human meaning is in unstructured work artifacts
- Critical context for understanding decisions
- Cannot be ignored for AI/knowledge initiatives

**Solution:** Invest in [SemOps](../README.md), knowledge graphs, semantic scaffolding

### Not Learning from Systems of Record

**Problem:** Dismissing SOR as "legacy" without recognizing their governance strengths

**Reality:**
- Finance has solved [semantic coherence](../SEMANTIC_OPTIMIZATION/semantic-coherence.md) through constraints
- Regulatory enforcement drives discipline
- Can learn patterns for other domains

**Solution:** Study why finance works, apply patterns elsewhere

---

## Evolution Patterns

**Early stage:** Heavy Application + Analytics
- Focus on product and growth metrics
- Lightweight systems of record
- Minimal work data governance

**Scale-up:** Adding Systems of Record
- Implement proper ERP/Finance
- Formalize HRIS
- Strengthen compliance

**Mature:** All four balanced
- Well-governed systems of record
- Strong analytics infrastructure
- SemOps for work data

### Traditional → Digital

**Traditional:** Heavy Systems of Record
- ERP-centric
- Batch analytics
- Paper-based work

**Digital transformation:** Adding Application + Analytics
- Cloud applications
- Real-time analytics
- Digital work artifacts

**Risk:** Trying to unify instead of integrate

---

## Examples

### End-to-End Data Flow Across System Types

A concrete example of how data moves through system types, gaining properties at each stage:

```text
[Operational DB] --(CDC)--> [Kafka Topic]
     │                         │
     ▼                         ▼
   [Raw Storage (S3)] ← [Stream Processor]
     │
     ▼
 [Table Layer (Iceberg / Delta)]
     │
     ▼
 [Analytic Engine (Spark / SQL / Trino)]
     │
     ▼
 [AI & BI Tools (MLflow / Power BI)]
```

Each transition adds a property: CDC adds **timeliness**, storage adds **durability**, the table layer adds **structure and transactions**, the engine adds **computation**, and AI/BI adds **interpretation**. The operational database (Application Data System) feeds the analytical pipeline (Analytics Data System) — same events, different physics.

For concrete format examples (Parquet schemas, Delta Lake transaction logs, Iceberg metadata, CDC events), see [Data Structures & Examples](../../../Publicv1_Supplemental/examples/data-systems-structure-examples.md).

---

## Related Concepts

### Foundation

- Application Data System - System 1: Operational
- Analytics Data System - System 2: Analytical
- Enterprise Work System - System 3: Knowledge artifacts
- Enterprise Record System - System 4: Canonical truth

### Integration Patterns

- Ingestion boundary - How data enters from each system
- OLTP/OLAP - Technical foundation for systems 1 & 2
- Anti-Corruption Layer (DDD) - Integration between systems

### Analysis

- [business-analytics-patterns](business-analytics-patterns.md) - What gets analyzed
- [data-silos](data-silos.md) - Why "silos" is wrong framing
