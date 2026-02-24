---
doc_type: hub

pattern: governance-as-a-strategy

provenance: 1p

metadata:
 pattern_type: concept
 brand_strength: high
---

# Governance as Strategy

> Provenance and Lineage are two data management practices that are often seen as uninteresting compliance and pipeline management methods, but as data categories grow and AI integrations consume more and more context, they become critical and can provide added value.

Both Provenance and Lineage are important technical concepts related to: 

- Tracking inherent and change-driven properties of the data itself 
- For the purposes of Project Ike and envisioning the AI First Company, these concepts would extend into foundational principles of how companies operate and how Knowledge Operations are performed.


that definitions, methods, and tools related to technical systems managing data
**Provenance** and **Lineage** are related but distinct concepts, and the industry often conflates them. The following sections break them apart carefully:

---

## Data Provenance

> Provenance describes the *origin, custody, and derivation* of data — including its sources, methods of collection, acquisition context, and transformations from the real world into digital form.

**Characteristics:**

* Focuses on **how raw data was obtained** (instrument, sensor, survey, crawl, API, etc.).
* Includes **intent and trust context** — why the data was created, under what policy or license.
* Often extends **outside the data platform boundary** (e.g., to an IoT device, lab instrument, or data vendor).
* Aligns with **W3C PROV-O ontology** and **FAIR data principles** (scientific, governmental, or regulated contexts).

**Example:**

| Field | Example |
| ------------------------ | ---------------------------- |
| `creator` | “U.S. Census Bureau” |
| `collection_method` | “Web form survey, API v2.3” |
| `license` | “CC-BY 4.0” |
| `acquisition_date` | “2025-05-12T10:34:00Z” |
| `data_subject_scope` | “U.S. residents aged 18+” |
| `ethical_considerations` | “GDPR compliant, anonymized” |

**Common Tools:**

* **Collibra / Alation** — capture data ownership and source documentation.
* **Harvester.io**, **CKAN**, **Dataverse** — scientific and open-data provenance.
* **W3C PROV-O (Provenance Ontology)** — RDF-based model representing “entity–activity–agent” relationships.

---

## Data Lineage

> Lineage represents the *flow of data through systems* — including transformations, pipelines, and dependencies between datasets, columns, and processes.

**Characteristics:**

* Focuses on **system-internal movement and transformation**.
* Records which jobs, queries, or models created or modified data.
* Enables **impact analysis** (what breaks if a column changes?) and **root-cause analysis** (where did this error originate?).
* Often visualized as **directed acyclic graphs (DAGs)** showing nodes (datasets) and edges (transformations).

**Example:**

| Field | Example |
| ---------------- | ---------------------------------------------- |
| `input_dataset` | `raw.orders` |
| `transformation` | `SELECT ... FROM raw.orders JOIN ref.products` |
| `output_dataset` | `analytics.sales_summary` |
| `job_id` | `airflow_dag_2025_10_31` |
| `schema_version` | `v3.2` |
| `engine` | `dbt + Snowflake` |

**Common Tools:**

* **OpenLineage + Marquez** — open standard for pipeline metadata (used by Airflow, Spark, dbt).
* **Apache Atlas** — metadata and lineage tracking for Hadoop and modern lakehouses.
* **LinkedIn DataHub**, **Amundsen** — graph-based data catalogs with lineage visualizations.
* **Microsoft Purview** (Fabric / Azure) and **Unity Catalog** (Databricks) — enterprise metadata + lineage.

---

## Provenance vs. Lineage — Clarified

| Aspect | **Provenance** | **Lineage** |
| ----------------------------- | ---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- | -------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| **Core Question** | *“Where did this data come from, and why does it exist?”* | *“What happened to this data as it moved through our systems?”* |
| **Primary Scope** | Origin, source context, purpose, acquisition method | Transformations, dependencies, schema and system evolution |
| **Temporal Focus** | **Before** the data entered the system | **After** the data entered the system |
| **Granularity** | Dataset or record-level metadata, often external | Field-, table-, job-, or column-level metadata inside the stack |
| **Governance Role** | Supports trust, compliance, and ethical sourcing | Supports reproducibility, impact analysis, debugging |
| **Typical Questions** | "Who collected it?" "When and where was it created?" "What is the license or consent model?" | "Which pipeline produced this table?" "What SQL or model transformed this column?" "What will break if this schema changes?" |
| **Analogy** | The *biography* of the data | The *audit trail* of the data within the environment |
| **Common Metadata Standards** | W3C PROV, FAIR data principles (Findable, Accessible, Interoperable, Reusable) | OpenLineage, Marquez, DataHub, Apache Atlas |
| **Example Tools / Systems** | • Data catalogs capturing external metadata (Collibra, Alation, Harvester.io) <br>• Research / scientific datasets (e.g., Dataverse, Dryad) <br>• Metadata embedded in file headers (EXIF, XML, JSON-LD) | • Pipeline-aware systems (Apache Atlas, DataHub, OpenLineage, Amundsen, Purview, Unity Catalog) <br>• ETL orchestration metadata (Airflow, dbt, Spark lineage) |

---

 ┌───────────────────────────────┐
 │ External World │
 │ (sensors, partners, surveys) │
 └──────────────┬────────────────┘
 │
 Provenance metadata
 ("where did this data come from?")
 │
 ▼
 ┌────────────────────────────────────────┐
 │ Internal Data Ecosystem (Pipelines) │
 │ Ingest → Transform → Store → Consume │
 └────────────────┬───────────────────────┘
 │
 Lineage metadata
 ("what happened to the data internally?")

---

## Relationship Between Them

| Layer | Provenance Focus | Lineage Focus |
| ---------------------- | --------------------------------------------------- | ---------------------------------------------- |
| **Outside the system** | Data source, sensor, collection, ownership, license | — |
| **Ingestion / ETL** | Source system ID, ingestion method | Pipeline job, transform scripts |
| **Storage / Table** | Dataset creation date, retention policy | Table joins, schema evolution |
| **Analytics / ML** | Training data origin, consent, labeling rationale | Model versioning, feature transformation graph |
| **Governance layer** | Ethical & regulatory compliance | Operational reproducibility and auditability |


### Combined Example

Imagine a dataset `customer_feedback` used to train a sentiment model.

**Provenance (answers “Where did this data come from?”):**

```json
{
 "source": "Mobile App Reviews API",
 "collected_by": "DataOps ingestion service",
 "collection_date": "2025-10-30T23:00:00Z",
 "license": "Internal use only",
 "data_subject_region": "US, EU",
 "consent": "Implied user consent per TOS"
}
```

**Lineage (answers “What happened to it in our system?”):**

```json
{
 "input_dataset": "raw.app_reviews",
 "transformation": "clean_text(reviews) -> sentiment_score",
 "output_dataset": "ml.training_reviews_v3",
 "executed_by": "Airflow DAG sentiment_train_v3",
 "engine": "Spark on Databricks",
 "schema_change": {"added_column": "sentiment_score"}
}
```

### Why the Distinction Matters

| Purpose | Provenance | Lineage |
| ------------------------------ | -------------------------------------- | -------------------------------------------------- |
| **Data Trust & Ethics** | Ensures data was obtained legitimately | Ensures transformations are transparent |
| **Compliance (GDPR, HIPAA)** | Tracks consent, license, region | Tracks data subject fields through transformations |
| **Scientific Reproducibility** | Recreate experiment setup | Reproduce computational results |
| **Operational Reliability** | Validate source authenticity | Diagnose data breaks or drift |
| **AI Governance** | “Why do we have this data?” | “How did this model get these features?” |

---

## Emerging Standards and Integration

* **W3C PROV-O** (Provenance Ontology):
 Used by scientific and government orgs for formal provenance graphs.
 Relationships: `Entity → Activity → Agent`.

* **OpenLineage** (Lineage Standard):
 Defines event schemas for jobs, datasets, and runs; supported by Airflow, Spark, dbt, and Marquez.

* **Integration Trend:**
 Modern catalogs (DataHub, Purview, Unity Catalog) are starting to **merge provenance + lineage** in unified metadata graphs —
 provenance nodes (external sources) link to lineage graphs (internal flows).

---

### Summary

| Category | Provenance | Lineage |
| ----------------- | ------------------------------------ | -------------------------------------- |
| **Scope** | Outside → data entry | Inside → data flow |
| **Focus** | Source, ownership, collection method | Transformation, dependency, versioning |
| **Granularity** | Dataset-level | Column/job-level |
| **Typical Tools** | Collibra, Alation, CKAN, W3C PROV | DataHub, Atlas, Purview, OpenLineage |
| **Output** | Source metadata | DAG of data transformations |

---

## Failure Patterns

### Lineage 
Why lineage Is Not Widely Adopted
	Why it happens
🔄 Manual pipelines	Many orgs still use ad hoc scripts, making lineage hard to track
⚙️ Lack of tooling	Tools like dbt or DataHub are not always adopted
📉 No enforcement	Schema changes are not tied to CI/CD or approval processes
🧠 Tribal knowledge	People remember relationships rather than documenting them
🔕 Silent failures	Dashboards often fail quietly, so breakage is not noticed until it is too late
✅ How to Improve Adoption
Make lineage visible — Use tools like dbt docs, Power BI lineage view, or DataHub
Integrate into CI/CD — Enforce schema checks before deployment
Assign data owners — Responsible parties for datasets and models
Automate impact analysis — Tools that raise alerts before breakage
Include in onboarding — Teach data teams to depend on lineage from day one

### Lightweight / Mid-size Teams
Tool	Purpose	Highlights
- dbt + dbt docs	Build & document data pipelines	Auto-generates lineage graphs from models
- Great Expectations	Data validation & profiling	Auto-profiling; test schema & types
- YData Profiling	Data understanding	Instant summary of each table/column
- Airflow + OpenLineage	Orchestration + lineage	Tracks data flows programmatically
- Power BI Lineage View	Dashboard-level lineage	Native support if using Power BI service
- Metabase	Dashboarding with SQL lineage	Lightweight, good for startups
 
### Enterprise / Large-Scale Teams
Tool	Purpose	Highlights
DataHub (by LinkedIn)	Data catalog + lineage	Open-source, supports Airflow/dbt/Snowflake
Amundsen (by Lyft)	Metadata + search	Easy to browse lineage + owners
Atlan / Collibra	Data governance platforms	Rich lineage + compliance features
Monte Carlo / Metaplane	Data observability	Detects schema changes, silent failures, freshness issues
BigQuery INFORMATION_SCHEMA	Metadata via SQL	Built-in support to inspect tables, columns, jobs

### "Lineage Readiness" Checklist for Teams

Use this to assess whether a team or organization is set up to track and manage schema change impact effectively.

Category	Question	Target
📐 Modeling	Is dbt or a similar modeling layer in use?	✅ Yes
🧠 Documentation	Are ERDs or dbt docs generated automatically?	✅ Yes
🔗 Lineage	Can a dashboard column be traced back to the source?	✅ Yes
🚨 Change alerts	Are breaking schema changes detected in CI/CD or via alerting?	✅ Yes
🏷️ Ownership	Are data owners and maintainers clearly defined?	✅ Yes
🧪 Testing	Are there schema tests for nulls, types, uniqueness?	✅ Yes
📊 Profiling	Is data regularly profiled and checked for quality?	✅ Yes
👥 Collaboration	Can BI, engineering, and data science teams all see the same lineage?	✅ Yes
📚 Learning	Are new team members trained on how lineage and schema changes work?

## Foundations for Semantic Operations 

- talk about how this applies

## Knowledge ops, provenance, promotion

Knowledge ops, provenance, and promotion
- idea of promoting data structures to a more structured “hard schema” once some utility threshold has been passed - that is flexible edge, [stable core](../EXPLICIT_ARCHITECTURE/stable-core-flexible-edge.md)
- When an organization onboards information, the goal is constantly to find ways to promote it to shared knowledge

Agile assumes that velocity is good because the problem definition has been granularized enough that there is low probability that the output will be aligned with goals because that granularization and emergent process will “get there” - but, it is also a “body in motion” - keep ppl busy

### Tradeoffs and Lineage

When lineage and provenance are applied up the stack into operating knowledge ops, some areas where they could help become apparent

- Organizations make suboptimal decisions all the time "no solutions, only tradeoffs" - "fast, cheap, good pick 2"
- The common refrain is "tech debt is ok as long as the team is aware", which is true, but really, the team needs to be aware, track it, and actually treat it like a financial liability.
- Good product and business practices advocate being transparent and dispassionate about when tradeoffs are made and having a plan B knowing that could happen - Amazon's tenets and LPs bring this to mind
- With real provenance and lineage, teams could track tradeoffs like they do KPIs or other goal metrics - it is actually a lot like an experiment - the hope is this change does the thing, but there is no certainty and in fact, the bias should be towards not shipping it (guardrails) - the team will either be really confident or remain unsure.
When making big decisions this is hard, psychologically and in practice because the "plan B" might be "this was a bad idea from the start, and now the team is totally screwed" ... but usually it is not that - if the swings are that big, stop reading this, stop looking at data, the extra stress is not needed... and good luck! This actually does happen and the tone here is lighthearted but it is actually a good idea to be aware that this is the tradeoff, and then

---

## Related Links

### Framework Components

- [Strategic Data](README.md) - Parent framework
- [Data Is an Organizational Challenge](data-is-organizational-challenge.md) - Why governance matters organizationally
- [Silent Analytics Failure](silent-analytics-failure.md) - What happens without governance

### Related Concepts

- [Anti-Corruption Layer (ACL)](../EXPLICIT_ARCHITECTURE/ddd-acl-governance-aas.md) - Governance encoded at boundaries
- [Semantic Coherence](../SEMANTIC_OPTIMIZATION/semantic-coherence.md) - Measuring shared meaning