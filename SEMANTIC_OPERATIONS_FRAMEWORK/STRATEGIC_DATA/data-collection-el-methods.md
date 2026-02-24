---
doc_type: hub

pattern: data-collection-el-methods

provenance: 3p

metadata:
 pattern_type: concept
 brand_strength: low
---

# Data Collection & EL Methods: A Comprehensive Guide

Understanding when to use different Extract-Load approaches—from managed platforms like Fivetran to code-first frameworks like DLT—requires examining both the technical properties of data sources and the business contexts that drive data movement.

---

## The Core Tradeoff Spectrum

At the highest level, the tradeoff is between **speed-to-value** and **control and cost**:

| Approach | Characteristics |
|----------|-----------------|
| **Managed platforms (Fivetran, Stitch)** | Maximum convenience, minimum control, highest marginal cost |
| **Open-source managed (Airbyte, Meltano)** | Middle ground, self-hosted option, connector ecosystem |
| **Python frameworks (DLT, Singer taps)** | Code-first, high control, requires engineering investment |
| **Custom pipelines** | Maximum control, maximum effort, sometimes the only option |

---

## Source Properties That Drive the Decision

The source's characteristics should heavily influence the approach.

### 1. Connector Maturity & API Stability

When pulling from Salesforce, Stripe, HubSpot, or other major SaaS platforms, Fivetran/Airbyte have battle-tested connectors that handle pagination, rate limiting, incremental loads, and schema changes. The value proposition is real—these connectors encode thousands of hours of edge-case handling.

But if the source is a niche vertical SaaS, a legacy system with a quirky API, or an internal service, connector coverage drops off fast. This is where DLT or custom work becomes necessary.

### 2. Change Data Capture (CDC) Requirements

This is a major differentiator:

- **Fivetran** has deep CDC support for databases (Postgres, MySQL, SQL Server) using log-based replication. They handle the complexity of maintaining replication slots, handling schema evolution, etc.
- **Airbyte** offers CDC but it is less mature; teams will hit more edge cases.
- **DLT** does not do CDC natively—it pairs with Debezium or similar, using DLT for the downstream movement.
- **Custom** means implementing CDC independently, which is genuinely hard to do reliably.

If real-time or near-real-time sync from operational databases is needed, CDC support quality matters enormously.

### 3. Schema Complexity & Evolution

Sources vary wildly here:

- **Rigid, well-documented schemas** (most mature SaaS APIs): Any approach works
- **Nested/semi-structured data** (MongoDB, JSON APIs): DLT handles this elegantly with its schema inference and nested data normalization. Fivetran/Airbyte also handle it but with less transparency.
- **Rapidly evolving schemas**: Managed platforms handle this automatically but sometimes too aggressively (adding unwanted columns). DLT provides schema contracts and evolution policies under direct control.

### 4. Volume & Velocity

- **Low volume, batch is fine**: Any approach works; optimize for maintainability
- **High volume, batch**: Cost becomes a factor—Fivetran charges by Monthly Active Rows, which can get expensive. Self-hosted Airbyte or DLT pipelines have compute costs but no per-row fees.
- **High velocity/streaming**: None of these are streaming-native. Streaming scenarios call for Kafka Connect, Flink, or custom consumers.

### 5. Authentication Complexity

OAuth flows, token refresh, multi-tenant auth patterns—managed platforms handle these seamlessly. With DLT or custom pipelines, teams implement and maintain auth logic. For sources with complex auth (e.g., multi-step OAuth with short-lived tokens), this maintenance burden is real.

---

## DLT (Data Load Tool) Deep Dive

DLT deserves special attention because it occupies an interesting niche. Its philosophy is:

- **Declarative pipelines as Python code**: Describe what is needed, not how to paginate
- **Schema inference with contracts**: It figures out the schema but allows enforcing constraints
- **Incremental loading primitives built-in**: Cursor-based, merge keys, etc.
- **Destination-agnostic**: Same pipeline code works for DuckDB locally, Snowflake in prod

### When DLT Shines

- APIs lack pre-built connectors
- Custom transformation during extraction is needed
- Pipeline logic belongs in version control with tests
- Cost sensitivity at scale is a concern

### When DLT Is Less Ideal

- Fivetran/Airbyte already has a mature connector for the source
- CDC from databases is required
- Zero Python maintenance is preferred

---

## Decision Framework

A structured approach to choosing the appropriate EL method:

```
1. Is there a mature, maintained connector in Fivetran/Airbyte?
 ├─ Yes → Is cost acceptable at your volume? 
 │ ├─ Yes → Use managed platform
 │ └─ No → Consider self-hosted Airbyte or DLT recreation
 └─ No → Continue...

2. Is this a database source needing CDC?
 ├─ Yes → Fivetran (if budget allows) or Debezium + custom
 └─ No → Continue...

3. Is this a REST/GraphQL API?
 ├─ Yes → DLT is excellent here
 │ (Also consider: Airbyte low-code connector builder)
 └─ No → Continue...

4. Is this a file source (S3, SFTP, etc.)?
 ├─ Yes → DLT handles this; so do managed platforms
 └─ No → Continue...

5. Is this a proprietary/legacy system?
 └─ Custom pipeline time (but consider DLT as the framework)
```

---

## Hybrid Approaches

In practice, mature data teams often use multiple approaches:

- **Fivetran** for the 10-15 core SaaS connectors that would be painful to maintain
- **DLT** for internal APIs, niche sources, and custom CDC downstream processing
- **Custom** for truly weird legacy integrations

The managed platform handles the undifferentiated heavy lifting; code-based approaches handle everything bespoke.

---

## Business Use-Cases by Source Type

Understanding the *why* behind data movement reveals the actual architecture of the business. Different source types map to fundamentally different business contexts.

### Operational/Product Databases (Postgres, MySQL, MongoDB)

**What it represents**: The core state of the product—users, transactions, content, relationships. This *is* the business in digital form.

**Why extract it**:

- **Product analytics**: Understanding user behavior, feature adoption, conversion funnels. The product team needs to answer "what are users actually doing?" without querying production.
- **Separating read from write workloads**: Production databases are optimized for transactions, not analytical queries. A 30-second analytical query could degrade user experience.
- **ML feature stores**: Recommendation engines, fraud detection, personalization—all need historical product data joined with behavioral signals.
- **Data products for customers**: B2B companies often find that customers want analytics on *their* usage. This requires extracting, aggregating, and re-serving operational data.
- **Regulatory/compliance**: Audit trails, data retention policies, right-to-deletion tracking.

**The organizational signal**: If a company is heavily investing in CDC from their production database, they're typically product-led and trying to become more data-driven about the product itself.

---

### SaaS Business Tools (Salesforce, HubSpot, Zendesk, Marketo)

**What it represents**: How *internal teams* operate—sales pipeline, marketing campaigns, support tickets. These are systems of record for business processes, not the product.

**Why extract it**:

- **Cross-functional analytics**: Marketing wants to know which campaigns led to closed deals. Support wants to correlate ticket volume with product releases. None of these tools talk to each other natively.
- **Customer 360**: Joining Salesforce account data + Zendesk tickets + product usage + billing data to get a complete view of a customer relationship. This is the primary objective for B2B companies.
- **Attribution modeling**: "Which marketing touches actually influenced revenue?" requires joining marketing automation data with CRM closed-won data with potentially ad platform spend data.
- **Operational reporting outside native tools**: Salesforce reports are limited. Finance wants pipeline forecasts in their BI tool alongside actual revenue from the ERP.
- **GTM efficiency metrics**: CAC, LTV, sales cycle length, win rates by segment—all require joining data across tools.

**The organizational signal**: Heavy SaaS extraction usually indicates a maturing revenue organization trying to optimize go-to-market. It is about understanding and improving the *business machine*, not the product.

---

### Event/Behavioral Data (Segment, Amplitude, Mixpanel, raw clickstream)

**What it represents**: The stream of user actions—every click, page view, feature interaction. Granular behavioral exhaust.

**Why extract it**:

- **Product analytics at scale**: Event platforms have query limitations. Extracting to a warehouse enables custom analysis.
- **Joining behavior with outcomes**: "Users who did X converted at Y rate" requires joining events with transactional data.
- **ML training data**: Behavioral sequences are the input for churn prediction, recommendation systems, fraud models.
- **Marketing personalization**: "Show this message to users who haven't completed onboarding step 3."

**The organizational signal**: Investment here indicates a company serious about product-led growth or sophisticated personalization/ML.

---

### Third-Party/Enrichment APIs (Clearbit, ZoomInfo, market data feeds)

**What it represents**: External context not generated internally.

**Why extract it**:

- **Lead enrichment**: Clearbit provides company size, industry, technologies used—informing lead scoring and routing.
- **Market intelligence**: Pricing data, competitor tracking, economic indicators.
- **Risk/compliance**: Sanctions screening, credit data, identity verification.

**The organizational signal**: Usually indicates either a sales-heavy organization (enrichment) or one operating in regulated/financial domains (risk data).

---

### Partner/File-Based Data (S3 drops, SFTP, EDI)

**What it represents**: Data from business relationships that predate modern APIs.

**Why it exists**:

- **Enterprise partnerships**: Large enterprises often share data via scheduled file drops, not APIs. Retail sell-through data, supply chain signals, etc.
- **Legacy system integration**: The ERP from 2008 does not have an API; it exports CSVs nightly.
- **Regulatory filings**: SEC data, healthcare claims, government datasets.

**The organizational signal**: Presence of significant file-based ingestion often indicates enterprise B2B relationships or regulated industry operations.

---

## The Meta-Pattern: What Your Sources Reveal About the Business

The mix of sources reveals the company's theory of value creation:

| Dominant Sources | Likely Business Model | Core Question Being Answered |
|-----------------|----------------------|------------------------------|
| Product DB + Events | Product-led growth | "How do we make the product stickier?" |
| CRM + Marketing SaaS | Sales-led B2B | "How do we optimize the revenue engine?" |
| Product DB + CRM + Support | Customer success-driven | "How do we retain and expand customers?" |
| Third-party + Product DB | Data-as-product or fintech | "How do we create unique insights?" |
| Files + Legacy DBs | Enterprise/regulated | "How do we modernize without breaking things?" |

---

## Examples

### Why Structured Data Matters for ML Pipelines

The choice of EL method determines the quality of downstream ML workflows. With a proper star schema fed by well-governed EL pipelines, feature engineering for customer segmentation becomes straightforward:

```sql
SELECT
 c.CustomerKey,
 DATEDIFF(CURRENT_DATE, MAX(t.TransactionDate)) as Recency,
 COUNT(DISTINCT t.TransactionID) as Frequency,
 SUM(t.Amount) as Monetary
FROM fact_transactions t
JOIN dim_customer c ON t.CustomerKey = c.CustomerKey
GROUP BY c.CustomerKey
```

This query works cleanly because the dimensional model enforces clear grain, referential integrity, and conformed dimensions. Without that structure — when EL pipelines dump raw data without modeling — data scientists spend 80% of their time wrangling data instead of building models.

The same pattern applies to LLM fine-tuning: structured customer data from properly governed EL pipelines produces clean instruction-response pairs for training. Unstructured dumps from ad-hoc pipelines require extensive manual reconciliation.

For detailed SQL examples comparing structured vs. unstructured approaches for ML clustering and LLM fine-tuning, see [ML/AI Need Structure](../../../Publicv1_Supplemental/examples/ai-ml-need-structure-examples.md).

---

## Summary

Choosing an EL approach is not just a technical decision—it is shaped by:

1. **Source properties**: Connector availability, CDC needs, schema complexity, volume, and auth complexity
2. **Business context**: What the data represents and why it needs to move
3. **Organizational maturity**: Budget, engineering capacity, and maintenance tolerance

The most effective data teams use hybrid approaches: managed platforms for commodity connectors, code-first frameworks like DLT for custom sources and cost optimization, and custom pipelines only when absolutely necessary.

---

## Related Links

### Framework Components

- [Strategic Data](README.md) - Parent framework
- [Data Systems Essentials](data-systems-essentials.md) - Core capabilities and system lenses
- [Surface Analysis](surface-analysis.md) - Where data enters the system

### Related Concepts

- [Physics of Data](physics-of-data.md) - Why data movement has real costs
- [Why Structure Matters](why-structure-matters.md) - Structure requirements for analytics and AI