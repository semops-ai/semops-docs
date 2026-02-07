---
doc_type: hub

pattern: three-forces

provenance: 3p

metadata:
    pattern_type: concept
    brand_strength: low
---
# The Three Forces of Data Architecture

> Every data architecture decision is shaped by three fundamental forces: **Volume & Velocity**, **Latency**, and **Structure & Variability**. These forces are physics, not preferences—they determine what is possible and what trade-offs emerge.

**Key insight:** These forces determine architecture, not vendor preferences or industry trends. Match architecture to the actual forces. Platforms differ only in how they respond to these constraints.

| Force | Range | What Changes | Drives Design For |
|-------|-------|--------------|-------------------|
| **1. Volume & Velocity** | Small & static → Massive & streaming | Distribution, partitioning, compression, parallel compute | *How much data? How fast does it grow?* |
| **2. Latency / Time Sensitivity** | Offline → Batch → Near-real-time → Real-time | Message queues, streams, in-memory processing | *How fresh must insights be?* |
| **3. Structure & Variability** | Strictly structured → Semi-structured → Unstructured | Schema strategy (on-write vs on-read), storage format | *How predictable is the data shape?* |

**Example applications:**
- Streaming telemetry (high volume, high velocity, semi-structured) → Kafka or Kinesis
- Quarterly finance data (low velocity, structured) → Warehouse with batch ETL
- IoT sensor data (high velocity, variable structure) → Data lake with schema-on-read

---

## Force 1: Volume & Velocity

**Question:** How much data? How fast does it arrive?

| Scale | Characteristics | Implications |
|-------|-----------------|--------------|
| Small | GBs, batch, single machine | Simple tools work fine |
| Medium | TBs, near-real-time | Need distributed compute |
| Large | PBs, streaming | Need cloud-scale architecture |

Volume and velocity determine whether you need distributed systems, how you partition data, and how you scale compute resources.

### Horizontal vs. Vertical Scaling

| Scaling Type | What It Means | Pros | Cons | Best For |
|--------------|---------------|------|------|----------|
| **Vertical (scale up)** | Add more CPU/RAM to a single machine | Simple (no code changes), better for single-threaded workloads | Hardware limits, expensive, single point of failure | OLTP databases, legacy apps |
| **Horizontal (scale out)** | Add more machines to distribute work | Nearly unlimited scale, fault tolerance | Requires distributed architecture, coordination overhead | OLAP, streaming, big data |

**Modern data systems favor horizontal scaling** because:
- Cloud makes it easy to spin up/down nodes
- Data volumes exceed single-machine capacity
- Distributed systems are fault-tolerant

### Partitioning: Organizing Data for Scale

**Partitioning** divides large datasets into smaller, manageable chunks based on one or more columns (partition keys).

**Why partition?**
- **Query performance** — Read only relevant partitions instead of scanning entire dataset
- **Pipeline efficiency** — Process only new/changed partitions (incremental processing)
- **Cost reduction** — Reduce data scanned (especially important in pay-per-scan systems like BigQuery, Athena)
- **Parallelism** — Distribute work across multiple workers/nodes
- **Data lifecycle management** — Easily delete or archive old partitions

**Example:**
```
# Without partitioning
/data/events.parquet  (10TB, must scan all to find yesterday's data)

# With partitioning by date
/data/events/date=2025-01-15/*.parquet
/data/events/date=2025-01-16/*.parquet
/data/events/date=2025-01-17/*.parquet
```

Query: `SELECT * FROM events WHERE date = '2025-01-17'`
- **Without partitioning:** Scan 10TB
- **With partitioning:** Scan only files in `date=2025-01-17/` (~33GB)

### Common Partition Keys

| Partition Key | Use Case | Pros | Cons | Best For |
|---------------|----------|------|------|----------|
| **Date/Time** | Time-series data (logs, events, IoT) | Aligns with most queries ("last 7 days"), easy archival | Can create hot partitions (today's data gets all writes) | Event logs, clickstreams, sensor data |
| **Geography** | Multi-region data | Compliance (GDPR), query locality | Uneven data distribution (US > other regions) | Customer data, regulatory compliance |
| **Entity ID** | User, account, tenant | Even distribution, supports multi-tenancy | Queries across all users require full scan | SaaS platforms, multi-tenant apps |
| **Category/Type** | Event type, product category | Logical separation | Requires knowing categories upfront | Product catalogs, event taxonomies |
| **Composite** | Date + region, date + tenant | Balances multiple access patterns | More complex, deeper directory trees | Hybrid use cases |

**Anti-pattern: Over-partitioning**
- Too many small partitions (e.g., partition by second or by individual user in a 10M user base)
- Creates metadata overhead, slow query planning
- **Rule of thumb:** Target 100MB–1GB per partition file

### Hive-Style vs. Hidden Partitioning

| Approach | How It Works | Pros | Cons | Used By |
|----------|--------------|------|------|---------|
| **Hive-style** | Partition columns in directory path (`/events/year=2025/month=01/`) | Simple, compatible with most tools | Schema changes require directory renames | S3, ADLS, Hive, Athena, Glue |
| **Hidden partitioning** | Partition metadata stored separately | Schema evolution without moving files, partition transforms | Requires Iceberg/Delta/Hudi awareness | Iceberg, Delta Lake, Hudi |

**Hidden partitioning example (Iceberg):**
```sql
CREATE TABLE events (
  event_time TIMESTAMP,
  user_id STRING,
  event_type STRING
)
PARTITIONED BY (days(event_time))
```
- Files are not in directories like `/day=2025-01-17/`
- Partition info stored in Iceberg metadata
- Can change partitioning scheme without rewriting data

### Scaling Patterns by Capability

| Capability | Scaling Strategy | How It Works | Example Tools |
|------------|------------------|--------------|---------------|
| **Ingestion & Streaming** | Partition-based parallelism | More partitions = more parallel consumers | Kafka consumer groups, Kinesis shard scaling |
| **Raw Storage** | Object storage auto-scales | Cloud providers handle distribution transparently | S3, ADLS, GCS (effectively infinite scale) |
| **Table Layer** | File-level parallelism | Query engines read multiple files in parallel | Spark reads 1000 Parquet files across 100 executors |
| **Analytic Engines** | Cluster scaling (auto or manual) | Add/remove compute nodes based on workload | Databricks clusters, Snowflake warehouses, BigQuery slots |
| **Orchestration** | Task parallelism + worker scaling | Scheduler distributes tasks to worker pool | Airflow workers, Prefect agents, Kubernetes pods |
| **AI/ML** | Model parallelism + data parallelism | Distribute training across GPUs/nodes | SageMaker multi-GPU training, Databricks distributed ML |

### Auto-Scaling Strategies

| Platform | Auto-Scaling Mechanism | Trigger | Example |
|----------|------------------------|---------|---------|
| **Databricks** | Cluster auto-scaling | CPU/memory utilization | Start with 2 nodes, scale to 10 under load, back to 2 when idle |
| **Snowflake** | Multi-cluster warehouses | Query queue depth | Auto-add warehouses when >5 queries queued, auto-suspend after 5min idle |
| **BigQuery** | Slot auto-scaling (serverless) | Query demand | Automatically allocate slots (compute units) per query |
| **AWS Glue** | DPU auto-scaling | Job resource needs | Start with 2 DPUs, scale to 100 for large Spark jobs |
| **Kinesis** | Shard auto-scaling | Throughput (MB/s) | Scale from 5 shards to 20 when ingestion spikes |

**Cost optimization:**
- **Auto-pause/suspend** — Shut down compute when idle (Databricks, Snowflake)
- **Spot/preemptible instances** — Use cheaper interruptible VMs for fault-tolerant workloads
- **Reserved capacity** — Pre-purchase compute for predictable workloads

### Sharding vs. Replication

| Strategy | Purpose | How It Works | Trade-Offs |
|----------|---------|--------------|------------|
| **Sharding** | Distribute data across nodes for scale | Each node holds a subset of data (e.g., users 1-1000 on node A, 1001-2000 on node B) | Harder to query across shards, potential hot spots |
| **Replication** | Copy data to multiple nodes for availability | Each node holds all data (or a full copy of a partition) | Storage cost increases, write coordination needed |

---

## Force 2: Latency / Time Sensitivity

**Question:** How fresh must data be?

| Latency | Use Case | Architecture |
|---------|----------|--------------|
| Offline (days) | Historical analysis | Batch ETL |
| Batch (hours) | Daily reporting | Scheduled jobs |
| Near-real-time (minutes) | Dashboards | Micro-batch, CDC |
| Real-time (seconds) | Fraud detection | Streaming |

Latency requirements determine whether you use batch processing, streaming, or something in between.

### Ingestion Paradigms

| Type | Description | Key Characteristics | Canonical Tools | Example Use |
|------|-------------|---------------------|-----------------|-------------|
| **Batch** | Processes data in fixed-size loads | Bounded, scheduled, file/table-based | Airflow, dbt, Spark, Azure Data Factory | Nightly load of orders from ERP |
| **Micro-batch** | Small, frequent batches (simulate streaming) | Time-windowed batches, low latency | Spark Structured Streaming, Flink (with triggers) | Near-real-time metrics on web traffic |
| **True Streaming** | Continuous record-by-record ingestion | Unbounded, low-latency, often stateful | Kafka, Flink, Pulsar, Kinesis | Fraud detection on payment events |
| **CDC (Change Data Capture)** | Ingests only changed records from a source | Log-based, incremental, usually event-driven | Debezium, HVR, Fivetran | Syncing changes from Postgres to a warehouse |
| **Append-only Log** | Immutable event stream | Timestamped events, no overwrite, idempotent | Kafka topics, Event Hubs | Order lifecycle events (created, paid, shipped) |
| **File-based** | Data delivered as files to be ingested | Semi-structured, often dropped to blob storage | S3 + Lambda, Azure Data Lake | IoT sensor CSVs dropped every hour |

### Batch vs. Streaming: When to Choose

| Factor | Go Streaming | Go Batch |
|--------|--------------|----------|
| Latency-sensitive | ✅ Yes | ❌ No |
| Large volumes at once | ❌ Difficult | ✅ Yes |
| Complex joins & aggregations | ⚠️ Stateful logic required | ✅ Simple |
| Auditability | ✅ Event logs | ⚠️ Harder |
| Event-based sources | ✅ Yes | ❌ No |
| File-based drops | ❌ No | ✅ Yes |

### Key Concepts: Streaming vs. Batch

| Concept | Streaming | Batch |
|---------|-----------|-------|
| **Input** | Unbounded (open-ended) | Bounded (fixed dataset) |
| **Triggering** | Continuous or windowed | Manual or scheduled |
| **Processing** | Record-by-record or micro-batch | Full table or file |
| **Latency** | Milliseconds to seconds | Minutes to hours |
| **Output** | Real-time dashboards, alerts, stateful services | Reports, models, data warehouses |

### Streaming in Message Systems

In message queues, partitioning enables parallelism and ordering:

| System | Partition Mechanism | Key Concepts | Example |
|--------|---------------------|--------------|---------|
| **Kafka** | Topic partitions (configurable count) | Messages with same key → same partition (ordering guarantee) | Topic `user-events` with 10 partitions; all events for user `12345` go to partition 3 |
| **Kinesis** | Shards (auto-scaling) | Partition key determines shard assignment | Stream with 5 shards; partition by `user_id` to ensure user event ordering |
| **Event Hubs** | Partitions (similar to Kafka) | Partition key for routing | 16 partitions, partition by `device_id` |

**Why partition streams?**
1. **Parallelism** — Each consumer reads from 1+ partitions independently
2. **Ordering** — Messages with same key are ordered within a partition
3. **Scalability** — Add partitions to increase throughput

### Events, Streams, and Tables

| Concept | Temporal Focus | Data Shape | Example Systems | Analogy |
|---------|----------------|------------|-----------------|---------|
| **Event** | Instantaneous ("something happened") | Atomic JSON record | Kafka, Kinesis | A single heartbeat |
| **Stream** | Continuous ordered series of events | Append-only log | Kafka topic, Kinesis stream | The pulse itself |
| **Table** | Stable, structured state at a point in time | Columns and rows (Parquet, Delta, Iceberg) | Snowflake table, Delta Lake | A snapshot of the body's condition |

**Temporal aggregation pipeline:** Streams produce events in real time → Tables accumulate them into queryable state

---

## Force 3: Structure & Variability

**Question:** How structured is the data? How much does it change?

| Structure | Characteristics | Strategy |
|-----------|-----------------|----------|
| Highly structured | Fixed schema, low variability | Schema-on-write, star schema |
| Semi-structured | JSON, nested, evolving | Schema-on-read with eventual structure |
| Unstructured | Text, images, audio | Feature extraction required |

Structure determines your schema strategy, storage formats, and how much energy you spend on the Data → Information transformation.

### Schema-on-Write vs. Schema-on-Read

| Approach | When Structure Is Applied | Pros | Cons | Best For |
|----------|---------------------------|------|------|----------|
| **Schema-on-write** | At ingestion time (ETL) | Query performance, consistency, governance | Less flexibility, upfront design cost | Warehouses, dimensional models |
| **Schema-on-read** | At query time | Flexibility, faster ingestion | Query-time cost, inconsistent results | Data lakes, exploration |

**The physics reality:**
- Schema-on-write pays the energy cost **once** during ETL
- Schema-on-read pays the energy cost **every time** you query
- "Schemaless" systems defer the cost; they do not eliminate it

### Storage Formats by Structure

| Format | Structure Level | Characteristics | Best For |
|--------|-----------------|-----------------|----------|
| **CSV** | Low | Simple, human-readable, no types | Legacy systems, small datasets |
| **JSON** | Semi-structured | Nested, flexible, self-describing | APIs, logs, document stores |
| **Avro** | Semi-structured | Schema evolution, compact binary | Streaming, Kafka |
| **Parquet** | Columnar | Compression, predicate pushdown | Analytics, data lakes |
| **ORC** | Columnar | Similar to Parquet, Hive-optimized | Hive ecosystems |
| **Delta/Iceberg** | Table format | ACID transactions, time travel, schema evolution | Lakehouses |

### Schema Evolution Patterns

| Pattern | Description | Table Format Support |
|---------|-------------|---------------------|
| **Add column** | New nullable column | All (Delta, Iceberg, Hudi) |
| **Rename column** | Change column name | Iceberg (native), Delta (with mapping) |
| **Drop column** | Remove column | All (soft delete in metadata) |
| **Widen type** | int → long, float → double | All |
| **Partition evolution** | Change partition scheme | Iceberg (without rewrite), Delta (with rewrite) |

**Hidden partitioning (Iceberg)** is particularly powerful for Force 3:
- Partition scheme stored in metadata, not directory structure
- Can evolve partitioning without rewriting data
- Supports transforms: `days(timestamp)`, `bucket(user_id, 16)`, `truncate(sku, 4)`

### The Structure Gradient

```
          HIGH STRUCTURE
     (deterministic operations)
              │
    ┌─────────┴─────────┐
    │                   │
Structured        Semi-Structured
(Star Schema)     (JSON, Nested)
    │                   │
    └─────────┬─────────┘
              │
        Unstructured
     (Text, Images, Audio)
              │
          LOW STRUCTURE
   (requires feature extraction)
```

**Key insight from physics:** The Data → Information transformation requires energy. The more variable the source structure, the more energy required to reach consistent, queryable state.

---

## Forces in Combination

The three forces interact to create architecture patterns:

### Pattern Matrix

| Volume/Velocity | Latency | Structure | Architecture Pattern |
|-----------------|---------|-----------|---------------------|
| Low | Batch | Structured | Traditional warehouse |
| High | Batch | Structured | Cloud warehouse (Snowflake, BigQuery) |
| High | Real-time | Semi-structured | Streaming + lake (Kafka → Delta) |
| High | Real-time | Unstructured | Stream processing + ML (Flink + feature extraction) |
| Low | Real-time | Structured | Operational analytics (HTAP) |

### Example: Event Pipeline

```
[App] → Kafka (50 partitions) → Spark Streaming (50 executors) → S3 (partitioned by date)
       ─────────────────────   ───────────────────────────────   ────────────────────────
       Force 2: Real-time      Force 1: Horizontal scale         Force 1: Partitioning
       Force 3: Semi-struct    Force 1: Parallelism              Force 3: Columnar (Parquet)
```

### Example: Warehouse Query

```sql
SELECT region, SUM(revenue) FROM sales WHERE date >= '2025-01-01' GROUP BY region
```

- **Force 1:** Storage partitioning (by date) → Only scan Jan 2025 files
- **Force 1:** Compute scaling → Snowflake auto-adds warehouses if query queue builds
- **Force 2:** Batch latency acceptable → Result caching saves future compute
- **Force 3:** Structured (star schema) → Deterministic, efficient aggregation

### Example: ML Training Pipeline

```
[Feature Store (partitioned by user_id)] → Spark (100 workers) → Training (4 GPUs)
─────────────────────────────────────────   ──────────────────   ─────────────────
Force 1: Partitioned for parallel read      Force 1: Scale out   Force 1: GPU parallelism
Force 3: Structured features                Force 1: 100x parallel
```

---

## Anti-Patterns

### Force 1 Anti-Patterns (Volume & Velocity)

| Anti-Pattern | Problem | Solution |
|--------------|---------|----------|
| **Small file problem** | Thousands of tiny files (< 10MB) slow down queries | Compact/coalesce files (Delta OPTIMIZE, Iceberg compaction) |
| **Hot partitions** | One partition gets 90% of traffic | Re-partition by a more evenly distributed key |
| **Always-on clusters** | Dev/test clusters run 24/7 | Auto-pause after inactivity |
| **Ignoring data skew** | One Spark task processes 10x more data than others | Repartition with salt, use AQE (Adaptive Query Execution) |
| **Over-partitioning** | Millions of tiny partitions | Target 100MB–1GB per partition |

### Force 2 Anti-Patterns (Latency)

| Anti-Pattern | Problem | Solution |
|--------------|---------|----------|
| **Streaming when batch works** | Unnecessary complexity for hourly reports | Use batch for non-real-time needs |
| **Batch when streaming needed** | Fraud detection with 1-hour delay | True streaming for time-sensitive use cases |
| **Ignoring exactly-once semantics** | Duplicate records in output | Use idempotent writes, transaction support |

### Force 3 Anti-Patterns (Structure)

| Anti-Pattern | Problem | Solution |
|--------------|---------|----------|
| **Schema-on-read for everything** | Query performance degrades, inconsistent results | Schema-on-write for core analytics |
| **No schema evolution plan** | Breaking changes require full rewrite | Use table formats with evolution support (Iceberg) |
| **Treating unstructured as structured** | LLMs hallucinate, models misclassify | Feature extraction, semantic scaffolding |

---

## Platform-Specific Guidance

### AWS

| Service | Force 1 (Volume) | Force 2 (Latency) | Force 3 (Structure) |
|---------|------------------|-------------------|---------------------|
| **S3** | Unlimited storage, Hive-style partitioning | N/A (storage only) | Any format |
| **Kinesis** | Shard auto-scaling | Real-time streaming | Semi-structured |
| **Athena** | Serverless, partition pruning | Batch (seconds-minutes) | Schema-on-read |
| **Glue** | DPU auto-scaling | Batch ETL | Schema registry |

### Azure

| Service | Force 1 (Volume) | Force 2 (Latency) | Force 3 (Structure) |
|---------|------------------|-------------------|---------------------|
| **ADLS Gen2** | Unlimited, hierarchical namespace | N/A (storage only) | Any format |
| **Event Hubs** | Partition auto-scaling | Real-time streaming | Semi-structured |
| **Synapse** | DWU scaling, distributions | Batch-interactive | Schema-on-write |
| **Databricks** | Cluster auto-scaling | Streaming + batch | Delta Lake (ACID) |

### GCP

| Service | Force 1 (Volume) | Force 2 (Latency) | Force 3 (Structure) |
|---------|------------------|-------------------|---------------------|
| **GCS** | Unlimited storage | N/A (storage only) | Any format |
| **Pub/Sub** | Auto-scaling | Real-time streaming | Semi-structured |
| **BigQuery** | Serverless, auto-partitioning | Interactive (seconds) | Schema-on-write |
| **Dataflow** | Auto-scaling workers | Streaming + batch | Beam schemas |

### Snowflake

| Aspect | Force 1 (Volume) | Force 2 (Latency) | Force 3 (Structure) |
|--------|------------------|-------------------|---------------------|
| **Compute** | Multi-cluster warehouses, auto-scale | Seconds-minutes | N/A |
| **Storage** | Automatic micro-partitions | N/A | Columnar, compressed |
| **Features** | Clustering keys for large tables | Result caching | Strong typing, schema enforcement |

### Databricks

| Aspect | Force 1 (Volume) | Force 2 (Latency) | Force 3 (Structure) |
|--------|------------------|-------------------|---------------------|
| **Compute** | Cluster auto-scaling, Photon | Streaming (Structured Streaming) | N/A |
| **Storage** | Delta Lake, Z-ordering | N/A | ACID, schema evolution |
| **Features** | Auto-optimize, auto-compaction | Change data feed | Unity Catalog governance |

---

## Key Takeaways

1. **The three forces are physics, not preferences**
   - Volume/Velocity → determines distribution and parallelism
   - Latency → determines batch vs. streaming
   - Structure → determines schema strategy and storage format

2. **Match architecture to your actual forces**
   - Do not over-engineer for forces that do not apply
   - Do not under-engineer for forces that do

3. **Partitioning is not optional at scale**
   - Directly impacts query performance, pipeline efficiency, and cost
   - Target 100MB–1GB per partition file

4. **Horizontal scaling is the norm**
   - Modern data systems scale by adding nodes, not bigger machines
   - Auto-scaling balances cost and performance

5. **Schema strategy has energy costs**
   - Schema-on-write pays once; schema-on-read pays repeatedly
   - "Flexible" schemas hide the cost; they do not eliminate it

6. **Forces interact**
   - High volume + real-time + semi-structured = most complex architecture
   - Low volume + batch + structured = simplest architecture

7. **Platforms differ in implementation, not fundamentals**
   - All platforms respond to the same three forces
   - Vendor choice is execution quality, not capability

---

## Related Links

### Parent Concepts
- [Physics of Data](physics-of-data.md) - Why data obeys physical laws
- [Data Systems Essentials](data-systems-essentials.md) - 7 Core Capabilities overview

### Force-Specific Deep Dives
- [OLTP vs OLAP Lens](data-systems-essentials.md#oltp-vs-olap-lens) - Latency profiles for different workloads
- [Why Structure Matters](why-structure-matters.md) - The case for schema discipline

### Applied Patterns
- [Business Analytics Patterns](business-analytics-patterns.md) - How forces shape analytics
- [Data System Types](four-data-system-types.md) - System types and their force profiles

### Citations
- [Reis & Housley (2022)](https://www.oreilly.com/library/view/fundamentals-of-data/9781098108298/) - *Fundamentals of Data Engineering*, O'Reilly Media
- [Kleppmann (2017)](https://dataintensive.net/) - *Designing Data-Intensive Applications*, O'Reilly Media
