---
doc_type: hub

pattern: three-five-seven-data-systems

provenance: 3p

metadata:
    pattern_type: concept
    brand_strength: low
---

# Three Five Seven Data System Frame

A simplified way of understanding analytics data systems.
*Framework adapted from [Reis & Housley (2022)](https://www.oreilly.com/library/view/fundamentals-of-data/9781098108298/), Fundamentals of Data Engineering, O'Reilly Media.*

## Three Categories

| View | Abstraction | Question | Nature |
|------|-------------|----------|--------|
| **Components** | System architecture | "What do we build with?" | Nouns (layers, tools) |
| **Lifecycle** | Data flow | "What happens to data?" | Verbs (stages, activities) |
| **Undercurrents** | Engineering discipline | "How do we do it well?" | Practices (cross-cutting) |

### Components (System Layers)

| Component | Function | Examples |
|-----------|----------|----------|
| **Ingestion Layer** | Move data into platform | Kafka, Fivetran, Airbyte, CDC |
| **Storage Layer** | Persist data durably | S3, ADLS, GCS, HDFS |
| **Table Layer** | ACID transactions + schema | Delta Lake, Iceberg, Hive |
| **Compute Engines** | Query and process | Spark, Snowflake, BigQuery, DuckDB |
| **Orchestration Tools** | Coordinate workflows | Airflow, Dagster, Prefect |
| **Catalog & Governance** | Metadata + access control | Unity Catalog, DataHub, Purview |
| **ML/AI Services** | Model training + serving | MLflow, SageMaker, Vertex AI |

### Lifecycle (Data Flow Stages)

| Stage | Activity | Input → Output |
|-------|----------|----------------|
| **Generation** | Data created at source | Events → Raw records |
| **Ingestion** | Data moved to platform | Source → Landing zone |
| **Storage** | Data persisted durably | Landing → Durable store |
| **Transformation** | Data reshaped for use | Raw → Modeled |
| **Serving** | Data delivered to consumers | Modeled → Analytics, ML, Apps |

### Functions

| Discipline | Concern | Applies To |
|------------|---------|------------|
| **Security** | Access, encryption, compliance | All stages |
| **Data Management** | Quality, lineage, semantics, governance | All stages |
| **DataOps** | Observability, reliability, incidents | All stages |
| **Data Architecture** | System design, patterns, trade-offs | All components |
| **Orchestration** | Dependencies, scheduling, failure handling | All workflows |
| **Software Engineering** | Testing, version control, CI/CD | All code |

## How They Relate

```
              UNDERCURRENTS (disciplines - the "how")
   ┌────────────────────────────────────────────────────────┐
   │ Security │ Data Mgmt │ DataOps │ Arch │ Tic │ SWE     │
   └────────────────────────────────────────────────────────┘
                         ↕ ↕ ↕ ↕ ↕
   ┌────────────────────────────────────────────────────────┐
   │ Generation → Ingestion → Storage → Transform → Serving │
   └────────────────────────────────────────────────────────┘
                    LIFECYCLE (process - the "what")
                         ↕ ↕ ↕ ↕ ↕
   ┌────────────────────────────────────────────────────────┐
   │ Ingestion │ Storage │ Table │ Compute │ Catalog │ ML  │
   └────────────────────────────────────────────────────────┘
                 COMPONENTS (layers - the "with what")
```

Components are assembled into a platform. Lifecycle flows through those components. Undercurrents ensure quality at every intersection. Vendor differentiation tends to be execution quality rather than fundamental capability.

> *Framework adapted from [Reis & Housley (2022)](https://www.oreilly.com/library/view/fundamentals-of-data/9781098108298/), Fundamentals of Data Engineering, O'Reilly Media.*

---

## Component Deep Dives

### Component 1: Ingestion & Streaming

**Purpose:** Move data from source systems into the platform, often in real time.

| Aspect                   | Description                                                                         |
| ------------------------ | ----------------------------------------------------------------------------------- |
| **Core functions**       | Event collection, message queuing, CDC replication, API ingestion, stream buffering |
| **Data latency**         | Real-time (sub-second) → Near real-time (seconds–minutes)                           |
| **Key patterns**         | Publish/subscribe, append-only logs, exactly-once semantics                         |
| **Representative tools** | Kafka, Kinesis, Pulsar, Azure Event Hubs, Confluent Cloud, Debezium                 |
| **Outputs**              | Structured event streams, change logs, incremental files                            |
| **AI/ML link**           | Real-time feature generation, online prediction streams                             |

**Example pipeline:**
Operational DB → CDC stream (Debezium → Kafka topic) → Lakehouse landing zone

---

### Component 2: Raw Storage

**Purpose:** Store any form of data—structured, semi-structured, unstructured—in cheap, durable storage.

| Aspect                   | Description                                                                 |
| ------------------------ | --------------------------------------------------------------------------- |
| **Core functions**       | Blob/object storage, hierarchical partitioning, lifecycle management        |
| **Data types**           | CSV, JSON, Avro, Parquet, images, video, logs                               |
| **Typical latency**      | Minutes to hours; append or batch writes                                    |
| **Representative tools** | AWS S3, Azure Data Lake Storage (ADLS), Google Cloud Storage (GCS), OneLake |
| **Outputs**              | Immutable objects, raw files awaiting transformation                        |
| **AI/ML link**           | Foundation for training data lakes and unstructured model inputs            |

---

### Component 3: Table Layer (Transactional Data Lake)

**Purpose:** Add structure, schema, and ACID transactions on top of object storage.

| Aspect                   | Description                                                                            |
| ------------------------ | -------------------------------------------------------------------------------------- |
| **Core functions**       | Table metadata management, schema evolution, snapshot isolation, time travel           |
| **Data latency**         | Minutes; micro-batch updates possible                                                  |
| **Representative tools** | Apache Iceberg, Delta Lake, Apache Hudi, AWS S3 Tables                                 |
| **Outputs**              | Logical "tables" referencing Parquet/ORC files with manifest/log metadata              |
| **AI/ML link**           | Enables reproducible training sets, feature history, consistent input-output semantics |

---

### Component 4: Analytic Engines

**Purpose:** Execute computation—SQL, Spark, or vector operations—on structured or semi-structured data.

| Aspect                   | Description                                                                                       |
| ------------------------ | ------------------------------------------------------------------------------------------------- |
| **Core functions**       | Query parsing, optimization, distributed execution, caching                                       |
| **Data latency**         | Seconds to minutes                                                                                |
| **Representative tools** | Databricks Spark/Photon, Snowflake virtual warehouses, BigQuery engine, Trino/Presto, Synapse SQL |
| **Outputs**              | Query results, materialized views, analytic aggregates                                            |
| **AI/ML link**           | Data prep and feature extraction; vector functions for embeddings; ML model scoring within SQL    |

---

### Component 5: Orchestration & Pipelines

**Purpose:** Define, schedule, and monitor data transformations and workflows.

| Aspect                   | Description                                                                              |
| ------------------------ | ---------------------------------------------------------------------------------------- |
| **Core functions**       | Job scheduling, dependency graphs, DAG execution, failure recovery                       |
| **Data latency**         | Seconds (streaming) → Hours (batch)                                                      |
| **Representative tools** | Apache Airflow, Databricks Workflows, Fabric Data Factory, AWS Glue, n8n, Step Functions |
| **Outputs**              | Managed dataflows, ETL/ELT pipelines, transformation jobs                                |
| **AI/ML link**           | Feature engineering workflows; retraining and evaluation jobs                            |

---

### Component 6: Catalog, Governance & Security

**Purpose:** Provide discoverability, lineage, access control, and policy enforcement.

| Aspect                   | Description                                                                                    |
| ------------------------ | ---------------------------------------------------------------------------------------------- |
| **Core functions**       | Metadata catalog, RBAC/ABAC, lineage tracking, audit logging, compliance                       |
| **Representative tools** | Unity Catalog (Databricks), Purview (Microsoft), AWS Lake Formation, Google Dataplex, Collibra |
| **Outputs**              | Certified datasets, data dictionaries, access policies                                         |
| **AI/ML link**           | Essential for trustworthy AI — data provenance, bias detection, reproducibility                |

---

### Component 7: AI / ML Services

**Purpose:** Operationalize data for predictive and generative applications.

| Aspect                   | Description                                                                                                  |
| ------------------------ | ------------------------------------------------------------------------------------------------------------ |
| **Core functions**       | Feature stores, model training, experiment tracking, model deployment, vector search                         |
| **Representative tools** | MLflow, SageMaker, Vertex AI, Databricks Model Serving, Snowpark ML, Azure ML                                |
| **Outputs**              | Trained models, inference endpoints, feature vectors                                                         |
| **Dependent layers**     | Requires high-quality, governed, structured data from previous capabilities                                  |
| **Bidirectional link**   | Models can also *produce* new data (embeddings, labels, synthetic records) that feed back into the lakehouse |

---

## Examples

### End-to-End Data Flow Through Components

A typical pipeline illustrates how Components, Lifecycle, and Undercurrents interact:

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
 [AI & BI Tools (MLflow / Power BI / Snowflake Cortex)]
```

At each stage, value is added through a different property:

| Layer | Adds | Example |
| ----- | ---- | ------- |
| Stream | Timeliness | Kafka event |
| Storage | Durability | S3 Parquet file |
| Table | Structure | Iceberg snapshot |
| Engine | Computation | SQL query |
| AI/BI | Intelligence | Power BI / ML model |

Together these form the **Data → Information → Knowledge → Wisdom** continuum — raw events gain structure, then governance, then interpretation.

For concrete format examples (Parquet schemas, Delta Lake transaction logs, Iceberg metadata snapshots, CDC events), see [Data Structures & Examples](../../../Publicv1_Supplemental/examples/data-systems-structure-examples.md).

---

## Related Links

### Parent Concepts

- [Data Systems Essentials](data-systems-essentials.md) - Hub document for Analytics Data Systems

### Complementary Frameworks

- [The Three Forces](three-forces.md) - Volume/Velocity, Latency, Structure forces that shape architecture
- [Data System Types](four-data-system-types.md) - Where Analytics Data Systems fit in the typology

### Citations

- [Reis & Housley (2022)](https://www.oreilly.com/library/view/fundamentals-of-data/9781098108298/) - *Fundamentals of Data Engineering*, O'Reilly Media
