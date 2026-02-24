---
doc_type: hub

pattern: surface-analysis

provenance: 1p

metadata:
 pattern_type: concept
 brand_strength: medium
---
# Surface Analysis

> **Surface Analysis** is a frame for understanding where business actually happens by interpreting data sources in their lineage context. Sources describe *what* data is and *where* it comes from; Surfaces reveal *what it means* for the business.

**Why this matters:** Organizations often misunderstand their own business because they confuse *systems* with *surfaces*. Strategy decks claim "omnichannel" while 90% of transactions come from POS. Data teams build elaborate integrations between systems that share no semantic boundary. The "data silo" framing leads to unification efforts that destroy meaning rather than create it. Surface Analysis provides a rigorous alternative: trace where data actually crosses boundaries, and the actual business interfaces with reality become visible.

**Key insight:** Generic "break down silos" advice fails because it ignores that different surfaces represent different meaning-systems. What looks like fragmentation is often appropriate semantic separation. Surface Analysis reveals the difference.

---

## Technical Definition
Surface does have a technical definition in the digital analytics domain, and this framework merely extends it to include other domains and types of data sources. The standard [GA4 platform/device dimensions](https://support.google.com/analytics/answer/9143382?hl=en) define surface as a tuple:

| Dimension | Definition | Examples |
| :---------------- | :----------------- | :--------------------------------- |
| Platform | Method of access | Web, iOS, Android |
| Device Category | Hardware class | Desktop, Mobile, Tablet |
| Operating System | OS | Windows, macOS, iOS, Android |
| Browser | Client application | Chrome, Safari, Firefox (web only) |
| App Version | Release identifier | v2.3.1 (native apps) |
| Screen Resolution | Display dimensions | 1920x1080, 375x812 |

**Surface as API Analogy**

Surface is like describing an API, but generalized beyond code interfaces to **any boundary where data crosses**.

Surface generalizes this to all data boundaries:

| API Concept | Surface Equivalent | Examples |
|-------------|-------------------|----------|
| **Endpoint** | Source location | ERP module, web page, Slack channel, agent tool |
| **Method** | Interaction mode | Manual entry, automated sync, event stream, query |
| **Schema** | Data shape | Transaction record, chat message, file upload |
| **Auth** | Access boundary | Internal user, customer, system service account |
| **Contract** | Semantic guarantees | SLA, data quality, freshness, completeness |

**Surface is Source + Purpose**
Surface is the business-legible interpretation of what data modeling reveals.

| Concept | Question | Nature |
|---------|----------|--------|
| **Source** | "Where exactly does data come from?" | Technical, precise, local |
| **Surface** | "What business interface does this source represent?" | Strategic, interpretive, contextual |

**The relationship:** Sources are evidence of Surfaces. If an organization understands its sources deeply, they reveal the actual business surfaces — which may be very different from what org charts or strategy decks claim.

---

## Components

### Source: The Technical Atom

> "What does the shape of our data sources tell us about where our business actually interfaces with reality?"

A **Source** is a `value object` — a tuple of technical properties that describe a data origin:

```yaml
source:
 platform: web | mobile | api | pos | erp | slack | ...
 os: ios | android | windows | macos | n/a | ...
 app_type: browser | native | pwa | integration | manual | ...
 interaction_mode: event_stream | batch | manual_entry | query | ...
 actor_type: customer | employee | system | partner | ai_agent | ...
 direction: inbound | outbound | bidirectional
 schema: <data shape>
```

Sources can be described standalone — teams can profile and catalog sources without understanding where they fit in the larger system.

**Examples of Sources:**
- Web checkout event stream (platform: web, mode: event_stream, actor: customer)
- SAP GL manual journal entry (platform: erp, mode: manual_entry, actor: employee)
- Slack channel message (platform: slack, mode: event_stream, actor: employee)
- Partner API webhook (platform: api, mode: event_stream, actor: system)

### Surface: Source in Context

A **Surface** is a Source positioned in the lineage graph. Surface adds:

- **Data World** — which of the 4 systems does this belong to?
- **Upstream** — what feeds this source?
- **Downstream** — what consumes this source?
- **Boundary Type** — is this where data enters the system, or is it derived internally?

```yaml
surface:
 # Source (the tuple)
 source:
 platform: web
 app_type: browser
 interaction_mode: event_stream
 actor_type: customer
 direction: inbound

 # Position (lineage context)
 data_world: oltp
 boundary_type: boundary
 upstream: [] # Boundary source — nothing upstream
 downstream:
 - event-stream-raw
 - marketing-pixel-relay
 - product-analytics

 # Lineage metadata
 analytics_outputs:
 - conversion-funnel
 - attribution-model
 systems_touched:
 - product-db
 - marketing-platform
 - warehouse
```

**Surface = Source + Lineage**

Teams can inventory Sources without understanding the business. But Surface Analysis requires the lineage — because Surface *is* the Source-in-context.

### The Tuple Pattern

Surfaces are identified by a combination of properties, not a single attribute — the **tuple pattern**.

### Digital Surfaces

```
Surface = (Platform, OS, App Type, Client, Auth Context)

Examples:
- (Web, macOS, Browser, Chrome, Anonymous)
- (Mobile, iOS, Native App, v2.3.1, Authenticated)
- (API, N/A, REST, Partner SDK, OAuth)
```

### Enterprise Surfaces

```
Surface = (System, Module, Interaction Mode, Actor Type)

Examples:
- (SAP, GL, Manual Entry, Finance User)
- (SAP, GL, Automated, Integration Service)
- (Workday, Payroll, Batch Upload, HR System)
- (Slack, Channel, Message, Employee)
- (Snowflake, Semantic Layer, Query, BI Tool)
```

### AI Surfaces

```
Surface = (Interface, Model, Orchestration, Context Source)

Examples:
- (Chat, Claude, Direct, User Input)
- (Agent, GPT-4, LangChain, RAG Pipeline)
- (Embedding, ada-002, Batch, Document Store)
- (Tool Call, Claude, Agentic, MCP Server)
```

The tuple reveals:
1. **Where** the boundary is (system, platform, location)
2. **How** data crosses it (manual, automated, streaming, batch)
3. **What** crosses (schema, format, granularity)
4. **Who** is on either side (actor types, auth context)
5. **Why** it exists (purpose, business function)

---

## Provenance Position: Boundary vs. Internal

A critical property of any Surface is its **provenance position** — is this where data enters the system (boundary), or is it derived from upstream sources (internal)?

| Provenance Position | Meaning | Examples |
|---------------------|---------|----------|
| **Boundary source** | Data enters the system here | Web events, API calls, POS transactions, file uploads, manual entry |
| **Internal source** | Data is derived/transformed inside | Analytics tables, ML features, aggregated metrics, BI dashboards |

**Why this matters:**

- **Boundary sources** = where meaning enters the system (high semantic authority)
- **Internal sources** = where meaning is transformed (semantic authority inherited from upstream)

Analytics is not "surfaceless" — it has surfaces (dashboards, query tools, semantic layers). But those are **internal sources** in provenance terms. The data already crossed a boundary somewhere upstream. Query surfaces, BI dashboards, semantic layers — these are real surfaces, but their semantic authority derives from the boundary sources that feed them.

### External vs. Internal Surfaces

Surfaces divide into **external** (customer/partner-facing) and **internal** (employee/system-facing):

| Surface Type | Examples | What Sources Reveal |
|--------------|----------|---------------------|
| **External** | Web app, mobile app, POS, API, call center | How customers/partners interact with the business |
| **Internal** | Slack, Notion, ERP UI, BI dashboard, internal tools | How employees create and consume meaning |

**The dark data problem reframed:** Most "enterprise work data" issues are **internal surface problems**. Internal surfaces are not instrumented with the same rigor as external surfaces — organizations treat customer interactions as first-class data sources, but employee interactions as afterthoughts.

---

## Analytics Ingestion Surfaces

> How data crosses the boundary into Analytics Data Systems.

Analytics Data Systems are **internal sources** — they derive meaning from upstream boundary sources. But the *patterns* by which data enters the analytical layer vary significantly in ownership, reliability, and semantic preservation.

**Ingestion patterns by surface type:**

| Pattern | Use Case | Example | Boundary Type |
|---------|----------|---------|---------------|
| **Event Streams** | Real-time behavioral data | Segment, Amplitude SDK → Kafka → Warehouse | Boundary (instrumented) |
| **Client-side Tracking** | Web/mobile user behavior | Google Analytics, Heap JavaScript → Collection API | Boundary (instrumented) |
| **Server-side Events** | Backend instrumentation | API middleware → Event bus → Analytics store | Boundary (instrumented) |
| **ETL from OLTP** | Transforming operational data | Nightly extract from Postgres → Transform → Load to warehouse | Internal (derived) |
| **CDC Streams** | Real-time operational sync | Debezium → Kafka → Delta Lake | Internal (derived) |

**Ownership dynamics:**

| Aspect | Reality |
|--------|---------|
| **1P (First-party)** | The organization controls the instrumentation |
| **Political challenge** | Analytics wants deeper events; engineering owns the codebase; neither owns the boundary |
| **Result** | Analytics instrumentation often becomes a "political orphan" |

**Reliability by pattern:**

| Pattern | Reliability | Why |
|---------|-------------|-----|
| **Client-side** | Low | Ad blockers (30-50% loss), network issues, user manipulation |
| **Server-side** | High | Guaranteed execution, controlled environment |
| **CDC/ETL** | High | System-to-system, no user interference |

**Best practice:** Dual instrumentation — client-side for real-time UX feedback, server-side for authoritative metrics (especially revenue).

**Surface Analysis insight:** The ingestion pattern determines whether the pipeline captures a *boundary source* (new data entering the system) or an *internal source* (data already captured elsewhere, now being transformed for analytics). This distinction affects semantic authority — boundary sources have higher authority because they're closer to the original event.

---

## Data Collection Methods: Extract-Load Approaches

> How data physically moves from sources to analytical systems — the practical implementation of ingestion patterns.

Understanding data collection methods connects Surface Analysis theory to operational reality. The choice of Extract-Load (EL) approach depends on both source properties and business context.

### The Core Tradeoff Spectrum

At the highest level, the tradeoff is **speed-to-value** against **control and cost**:

| Approach | Characteristics |
|----------|-----------------|
| **Managed platforms (Fivetran, Stitch)** | Maximum convenience, minimum control, highest marginal cost |
| **Open-source managed (Airbyte, Meltano)** | Middle ground, self-hosted option, connector ecosystem |
| **Python frameworks (DLT, Singer taps)** | Code-first, high control, requires engineering investment |
| **Custom pipelines** | Maximum control, maximum effort, sometimes the only option |

### Source Properties That Drive the Decision

The source's characteristics — which Surface Analysis already catalogs — directly influence the EL approach:

**1. Connector Maturity & API Stability**

Major SaaS platforms (Salesforce, Stripe, HubSpot) have battle-tested connectors that handle pagination, rate limiting, incremental loads, and schema changes. Niche vertical SaaS, legacy systems, or internal services require code-first approaches.

**2. Change Data Capture (CDC) Requirements**

| Approach | CDC Support |
|----------|-------------|
| **Fivetran** | Deep CDC for databases using log-based replication |
| **Airbyte** | CDC available but less mature |
| **DLT** | Not native — pair with Debezium for database CDC |
| **Custom** | Full control but genuinely hard to do reliably |

For real-time or near-real-time sync from operational databases, CDC support quality matters enormously.

**3. Schema Complexity & Evolution**

| Schema Type | Implications |
|-------------|--------------|
| **Rigid, well-documented** (mature SaaS APIs) | Any approach works |
| **Nested/semi-structured** (MongoDB, JSON APIs) | DLT handles elegantly; managed platforms less transparent |
| **Rapidly evolving** | Managed platforms handle automatically; DLT gives explicit control |

**4. Volume & Velocity**

| Pattern | Considerations |
|---------|----------------|
| **Low volume, batch** | Optimize for maintainability |
| **High volume, batch** | Cost matters — managed platforms charge per-row |
| **High velocity/streaming** | None of these are streaming-native; use Kafka Connect, Flink |

**5. Authentication Complexity**

OAuth flows, token refresh, multi-tenant auth — managed platforms handle these seamlessly. Code-based approaches require implementing and maintaining auth logic.

### Decision Framework

```
1. Is there a mature connector in Fivetran/Airbyte?
 ├─ Yes → Is cost acceptable at the expected volume?
 │ ├─ Yes → Use managed platform
 │ └─ No → Consider self-hosted Airbyte or code-first
 └─ No → Continue...

2. Is this a database source needing CDC?
 ├─ Yes → Fivetran (if budget) or Debezium + custom
 └─ No → Continue...

3. Is this a REST/GraphQL API?
 ├─ Yes → DLT excels here; also Airbyte connector builder
 └─ No → Continue...

4. Is this a file source (S3, SFTP)?
 ├─ Yes → DLT or managed platforms both handle this
 └─ No → Continue...

5. Is this a proprietary/legacy system?
 └─ Custom pipeline (consider DLT as framework)
```

### Hybrid Approaches in Practice

Mature data teams use multiple approaches:
- **Managed platforms** for 10-15 core SaaS connectors that would be painful to maintain
- **Code-first frameworks** for internal APIs, niche sources, custom CDC processing
- **Custom pipelines** for truly unique legacy integrations

The managed platform handles undifferentiated heavy lifting; code-based approaches handle everything bespoke.

### Business Context by Source Type

The *why* behind data movement reveals business architecture. Different source types map to different business contexts:

| Source Type | What It Represents | Why Extract It |
|-------------|-------------------|----------------|
| **Product databases** (Postgres, MySQL) | Core product state — users, transactions, content | Product analytics, separating read/write workloads, ML features, compliance |
| **SaaS business tools** (Salesforce, HubSpot) | Internal team operations — sales, marketing, support | Cross-functional analytics, Customer 360, attribution, GTM metrics |
| **Event/behavioral data** (Segment, Amplitude) | User action streams — clicks, views, interactions | Product analytics at scale, behavior-outcome joins, ML training |
| **Third-party APIs** (Clearbit, market data) | External context the organization does not generate | Lead enrichment, market intelligence, risk/compliance |
| **File-based data** (S3 drops, SFTP, EDI) | Data from pre-API business relationships | Enterprise partnerships, legacy integration, regulatory filings |

### What the Source Mix Reveals

The dominant sources reveal the company's theory of value creation:

| Dominant Sources | Likely Business Model | Core Question |
|-----------------|----------------------|---------------|
| Product DB + Events | Product-led growth | "How do we make the product stickier?" |
| CRM + Marketing SaaS | Sales-led B2B | "How do we optimize the revenue engine?" |
| Product DB + CRM + Support | Customer success-driven | "How do we retain and expand?" |
| Third-party + Product DB | Data-as-product/fintech | "How do we create unique insights?" |
| Files + Legacy DBs | Enterprise/regulated | "How do we modernize safely?" |

**Surface Analysis connection:** This is Surface Topology operationalized — the mix of data collection methods and source types reveals where the business actually interfaces with reality.

---

## Data System Types Lens

The [Four Data System Types](four-data-system-types.md) each have characteristic surface patterns:

Functional Data System: Operational
 │
 ├── Query Surface: OLTP (transactional queries)
 ├── Event Surface: Kafka, CDC streams
 ├── API Surface: REST/GraphQL endpoints
 ├── State Surface: Application databases
 └── Cache Surface: Redis, in-memory
 
Functional Data System: Analytical 
 │
 ├── Query Surface: OLAP (analytical queries)
 ├── Storage Surface: Data lake, warehouse
 ├── Semantic Surface: Metrics layer, cube
 ├── ML Surface: Feature store, model serving
 └── BI Surface: Dashboards, reports


---

## Data Model Everything Lens

Surface Analysis is the interpretive output of **[Discovery Through Data](../EXPLICIT_ARCHITECTURE/discovery-through-data.md)** — the practice of tracing actual data flows while ignoring organizational abstractions (teams, systems, vendors).

```
Data Model Everything (method)
 │
 ▼
 Sources discovered
 Flows traced
 Lineage mapped
 │
 ▼
 Surface Analysis (interpretation)
 │
 ▼
 "This source is a boundary into OLTP"
 "This source feeds these analytics outputs"
 "This is where the business actually touches customers"
```

| Activity | Output | Audience |
|----------|--------|----------|
| **Data modeling** | Sources + Lineage graph | Engineers, data teams |
| **Surface analysis** | Surfaces (sources in context) | Strategy, leadership, cross-functional |

One could stop at the technical level — "here are the sources, here is the lineage." That is useful for engineering. But the moment the question becomes **"what does this mean for the business?"** — that is Surface Analysis.

---

## The Four Data Worlds and Their Surfaces

The four data worlds each have characteristic surface types:

| Data World | Primary Surfaces | Surface Character |
|------------|------------------|-------------------|
| **Application (OLTP)** | Product surfaces — web app, mobile, API | Customer-facing, high semantic integrity, boundary sources |
| **Analytics (OLAP)** | Query surfaces — dashboards, semantic layer, BI tools | Internal sources, derived meaning, consumption-oriented |
| **Enterprise Work** | Collaboration surfaces — Slack, Docs, Email, Notion | Employee-facing, low semantic structure, often uninstrumented |
| **Systems of Record** | Back-office surfaces — ERP UI, HRIS, finance systems | Constraint enforcement, high integrity, often manual + automated |

```
┌─────────────────────────────────────────────────────────────┐
│ 4 DATA WORLDS │
│ ┌─────────────┐ ┌─────────────┐ ┌─────────────┐ ┌─────────┐│
│ │ OLTP │ │ OLAP │ │ Enterprise │ │ Systems ││
│ │ (Product) │ │ (Query) │ │ Work │ │ of Rec ││
│ └──────┬──────┘ └──────┬──────┘ └──────┬──────┘ └────┬────┘│
│ │ │ │ │ │
│ External + Internal Internal Internal │
│ Boundary (derived) (unstructured) (structured) │
└─────────┼──────────────┼───────────────┼──────────────┼─────┘
 │ │ │ │
 ▼ ▼ ▼ ▼
 ┌──────────┐ ┌──────────┐ ┌──────────┐ ┌──────────┐
 │ Web/Mobile│ │ BI Dash │ │ Slack │ │ SAP GL │
 │ Checkout │ │ Query │ │ Notion │ │ Workday │
 │ API Call │ │ Semantic │ │ Email │ │ Finance │
 └──────────┘ └──────────┘ └──────────┘ └──────────┘
```

**Key insight:** Understanding which surfaces extend across which data worlds — and where they overlap — reveals what's actually happening in the business.

---

## Surface Topology: The Strategic Map

When all surfaces in an organization are mapped, the result is the **Surface Topology** — a map of where business actually interfaces with reality.

```
┌─────────────────────────────────────────────────────────────┐
│ SURFACE TOPOLOGY │
│ (where the business actually happens) │
│ │
│ External surfaces ◄──────────────► Internal surfaces │
│ (customer/partner) (employee/system) │
│ │
│ ┌─────────────────────────────────────────────────────┐ │
│ │ BOUNDARY │ │
│ │ Web ─── Mobile ─── API ─── POS ─── Call Center │ │
│ └───────────────────────┬─────────────────────────────┘ │
│ │ │
│ ▼ │
│ ┌─────────────────────────────────────────────────────┐ │
│ │ INTERNAL │ │
│ │ Event Streams ─► Warehouse ─► Analytics ─► BI │ │
│ │ │ │ │
│ │ └──► ML Features ─► Models ─► Predictions │ │
│ │ │ │
│ │ Slack ─── Notion ─── Email ─── Docs (dark) │ │
│ │ │ │
│ │ ERP ─── HRIS ─── Finance ─── Compliance │ │
│ └─────────────────────────────────────────────────────┘ │
└─────────────────────────────────────────────────────────────┘
```

### What Surface Topology Reveals

A company might *say* they're "omnichannel retail" but their sources reveal:
- 90% of transaction events from POS
- 8% from web
- 2% from mobile app

**Surface Analysis reveals:** "You're a physical retailer with a website, not an omnichannel company."

Surface Topology shows:
- **Where business actually happens** (by volume/activity at each surface)
- **Which surfaces are instrumented vs. dark** (does the organization have visibility?)
- **Mismatches between stated strategy and actual surface activity**
- **Where semantic authority lives** (trace upstream to boundary)

---

## AI Surfaces

AI introduces new surface types that fit the same model:

| AI Surface | Interface Type | Provenance Position | Characteristics |
|------------|----------------|---------------------|-----------------|
| **Chat interface** | Human ↔ AI | Boundary | User intent enters here |
| **RAG pipeline** | System ↔ Knowledge | Internal | Retrieves from existing sources |
| **Agentic system** | AI ↔ Tools/APIs | Boundary-spanning | Orchestrates across multiple surfaces |
| **Embedding generation** | Content → Vector | Internal | Transformation of existing content |
| **Tool call (MCP)** | Agent ↔ System | Depends on tool | Agent extending reach to other surfaces |

**The interesting case:** Agentic systems are **boundary-spanning** — they interact with multiple surfaces (external APIs, internal data, user input). The "surface" of an agent is effectively the union of all the surfaces it can touch.

```
┌─────────────────────────────────────────────────────────────┐
│ AI SURFACE TOPOLOGY │
│ │
│ User ─────► Chat Interface (boundary) │
│ │ │
│ ▼ │
│ ┌─────────────┐ │
│ │ Agent │ │
│ │ (orchestr.) │ │
│ └──────┬──────┘ │
│ │ │
│ ┌────────────┼────────────┐ │
│ ▼ ▼ ▼ │
│ ┌──────────┐ ┌──────────┐ ┌──────────┐ │
│ │ RAG │ │ Tools │ │ External │ │
│ │ Pipeline │ │ (MCP) │ │ APIs │ │
│ │(internal)│ │ (spans) │ │(boundary)│ │
│ └──────────┘ └──────────┘ └──────────┘ │
└─────────────────────────────────────────────────────────────┘
```

---

## The Unified Model

```
┌─────────────────────────────────────────────────────────────┐
│ DATA MODEL EVERYTHING │
│ (trace flows, ignore systems/teams/vendors) │
│ │
│ METHOD │
└─────────────────────────────────────────────────────────────┘
 │
 ▼
┌─────────────────────────────────────────────────────────────┐
│ SOURCES │
│ (technical, precise) │
│ │
│ The tuple: platform, mode, actor, direction, schema │
│ │
│ Provenance: Boundary ◄────────────────► Internal │
│ (enters system) (derived) │
│ │
│ TECHNICAL OUTPUT │
└─────────────────────────────────────────────────────────────┘
 │
 ▼
┌─────────────────────────────────────────────────────────────┐
│ SURFACE ANALYSIS │
│ (interpret sources in context) │
│ │
│ Surface = Source + Lineage Position │
│ │
│ • Which data world? │
│ • What's upstream/downstream? │
│ • Boundary or internal? │
│ • What analytics outputs depend on this? │
│ │
│ INTERPRETED OUTPUT │
└─────────────────────────────────────────────────────────────┘
 │
 ▼
┌─────────────────────────────────────────────────────────────┐
│ SURFACE TOPOLOGY │
│ (the map of where business actually happens) │
│ │
│ External surfaces ◄──────────────► Internal surfaces │
│ (customer/partner) (employee/system) │
│ │
│ 4 Data Worlds positioned: │
│ • OLTP → Product surfaces (external) │
│ • OLAP → Query surfaces (internal) │
│ • Enterprise Work → Collaboration surfaces (internal) │
│ • Systems of Record → Back-office surfaces (internal) │
│ │
│ STRATEGIC MAP │
└─────────────────────────────────────────────────────────────┘
```

---

## Industry Variation

The same data system with identical technical properties can represent entirely different business realities across industries. Surface Analysis must account for domain context:

| Industry | Primary Surfaces | What This Means |
|----------|------------------|-----------------|
| **SaaS** | Web app, API | Product *is* the surface — usage data is business data |
| **Retail** | POS, Web, Mobile, Marketplace | Multi-surface — need to understand which dominates |
| **Insurance** | Agent portal, Call center, Claims system | Surfaces are workflows, not channels |
| **B2B Services** | CRM, Contract system, Delivery platform | Surfaces are relationship touchpoints |
| **Manufacturing** | IoT sensors, ERP, Supply chain | Surfaces span physical and digital |
| **Healthcare** | EMR, Patient portal, Claims | Surfaces are compliance-constrained |

**This is why generic "data silo" advice fails.** The surfaces mean different things in different domains. What looks like a "silo" in one company is simply a different meaning-system with different business physics in another.

---

## Surface Analysis in Practice

### Step 1: Data Model Everything

Trace actual data flows without regard to org structure:
- Profile what data exists
- Map where it flows
- Identify sources (the tuples)
- Build the lineage graph

### Step 2: Classify Sources

For each source, determine:
- **Provenance position:** Boundary or internal?
- **Data world:** OLTP, OLAP, Enterprise Work, or System of Record?
- **Actor type:** Customer, employee, system, partner, AI?
- **Interaction mode:** Streaming, batch, manual, query?

### Step 3: Position in Lineage

Connect sources to their context:
- What's upstream? (Where does this data come from?)
- What's downstream? (What consumes this?)
- What analytics outputs depend on this?
- What's the path back to the boundary source?

### Step 4: Build Surface Topology

Map the full surface landscape:
- External vs. internal surfaces
- Instrumented vs. dark surfaces
- Volume/activity distribution across surfaces
- Boundary sources (semantic authority) vs. internal (derived)

### Step 5: Strategic Interpretation

Ask the business questions:
- Where does business actually happen? (Follow the volume)
- Does surface distribution match stated strategy?
- Which surfaces are under-instrumented?
- Where is semantic authority concentrated?
- What would break if a boundary source changed?

---

## Key Takeaways

### 1. Source and Surface Are Not The Same

**Source** = local, technical, the tuple of properties
**Surface** = Source + lineage position = business meaning in context

Teams can catalog sources without understanding the business. Surface Analysis requires the lineage.

### 2. Surface Is a Categorical Device, Not a New Definition

Surface does not create new properties — it makes explicit what is already implicit in the source. Understanding the source deeply means already knowing the surface. Surface is just the vocabulary for articulating it.

### 3. Provenance Position Determines Semantic Authority

- **Boundary sources** = where meaning enters (high authority)
- **Internal sources** = where meaning is derived (inherited authority)

Trace upstream to find who owns the meaning.

### 4. The Four Data Worlds Have Characteristic Surfaces

- OLTP → Product surfaces (external, boundary)
- OLAP → Query surfaces (internal, derived)
- Enterprise Work → Collaboration surfaces (internal, unstructured)
- Systems of Record → Back-office surfaces (internal, structured)

### 5. Surface Topology Is the Strategic Map

The collection of all surfaces shows where business actually interfaces with reality — often different from what org charts and strategy decks claim.

### 6. AI Adds New Surface Types

Chat interfaces, RAG pipelines, agentic systems, tool calls — these are surfaces that follow the same model. Agents are notable as boundary-spanning surfaces.

### 7. Industry Context Matters

The same technical surface means different things in different domains. Generic advice fails because it ignores business physics.

---

## Examples

### DIKW Mapping to Architecture Layers

The following mapping shows how Surface Analysis connects to the broader framework — from raw signals through strategic interpretation:

| DIK(U)W Layer | Architecture Layer | Surface Analysis Role |
| ------------- | ------------------ | --------------------- |
| **Data** | Strategic Data (physical substrate) | Sources discovered, profiled |
| **Information** | Strategic Data (modeled, structured) | Source properties cataloged as tuples |
| **Knowledge** | Explicit Architecture (patterns, domains) | Sources positioned in lineage as Surfaces |
| **Understanding** | Semantic Optimization (coherence, alignment) | Surface Topology reveals actual business interfaces |
| **Wisdom** | Leadership decisions, strategy | Strategic interpretation — does surface distribution match stated strategy? |

Surface Analysis bridges the technical (sources, lineage) and the strategic (where business actually happens). Teams can catalog sources without understanding the business. But Surface Analysis requires the lineage context — because Surface *is* the Source-in-context.

For detailed DIKW-to-architecture mappings and the full conceptual connection framework, see [Concept Connections](../../../Publicv1_Supplemental/examples/framework-connection-examples.md).

---

## Related Links

### Method

- [Discovery Through Data](../EXPLICIT_ARCHITECTURE/discovery-through-data.md) - The practice that produces surfaces

### Source Data

- [Data Silos](data-silos.md) - The actual fragmentation problem
- [Four Data System Types](four-data-system-types.md) - OLTP, OLAP, Work, Record
- [Business Analytics Patterns](business-analytics-patterns.md) - Functional vs. surface analytics
- [Data Collection & EL Methods](data-collection-el-methods.md) - How data moves between surfaces

### Framework

- [Strategic Data](README.md) - Parent framework
- [Anti-Corruption Layer (ACL)](../EXPLICIT_ARCHITECTURE/ddd-acl-governance-aas.md) - Bounded contexts and boundaries

### Citations

- [GA4 Analytics dimensions and metrics](https://support.google.com/analytics/answer/9143382?hl=en) - Google Analytics Help, platform and device dimension definitions
- [Reis & Housley (2022)](https://www.oreilly.com/library/view/fundamentals-of-data/9781098108298/) - *Fundamentals of Data Engineering*, O'Reilly Media
