---
doc_type: hub

pattern: business-analytics-patterns

provenance: 3p

metadata:
    pattern_type: concept
    brand_strength: low
---

# Business Analytics Patterns

>This document covers **function-based analytics** (BI, Product, Marketing, Customer) - defined by WHAT business question they answer.

**Business Analytics** encompasses the family of analytics disciplines focused on understanding and optimizing business performance through dimensional analysis of transactional data.

## Analytics by Business Function

These four categories are defined by the business questions they answer:

- **BI/Reporting Analytics** - Historical business performance ("What happened?")
- **Product Analytics** - User behavior and product engagement ("How do users interact?")
- **Marketing Analytics** - Campaign performance and attribution ("Which efforts drive results?")
- **Customer Analytics** - Customer lifetime value and segmentation ("Who are our customers?")

### Analytics by Surface/Channel

- **Web/Digital Analytics** - The aggregated instrumentation layer for digital surfaces

Web/Digital Analytics is different from the four above. It is not defined by a business function but by a **channel where multiple functions converge**. Because so many businesses have product usage, marketing, customer service, and commerce all happening on web/digital surfaces, the unified instrumentation of this channel warrants its own category, tooling, and expertise.

While the functional categories share fundamental patterns (transactional/event aggregation, dimensional models, time-series analysis), Web/Digital Analytics is the **measurement layer for a surface** that captures all of them through a single instrumentation stack.

See: [Surface Analysis](surface-analysis.md)

---

## Common Patterns Across Business Analytics

These patterns are shared across BI, Product, Marketing, and Customer analytics:

| Pattern Category | Common Elements | Standard Implementations |
|-----------------|-----------------|-------------------------|
| **Core Dimensions** | • Time (Date/Hour hierarchies)<br>• Customer/User (ID, segments, cohorts)<br>• Product (SKU, category hierarchies)<br>• Geography (Country, Region, City)<br>• Channel (Web, Mobile, Store, Partner) | • Conformed dimensions (Kimball)<br>• Slowly Changing Dimensions (SCD Type 2)<br>• Hierarchical rollups |
| **Fact Table Patterns** | • Transaction grain (most atomic)<br>• Daily snapshot grain<br>• Periodic snapshot (monthly/quarterly)<br>• Accumulating snapshot (lifecycle tracking) | • Star schema<br>• Snowflake schema<br>• Fact constellation |
| **Time Series Aggregations** | • Daily/Weekly/Monthly rollups<br>• Year-over-Year (YoY) comparisons<br>• Month-over-Month (MoM) growth<br>• Rolling averages (7-day, 30-day, 90-day)<br>• Cumulative metrics (MTD, QTD, YTD) | • Date dimension with calendar hierarchies<br>• Fiscal period mappings<br>• Relative time calculations |
| **Metric Calculations** | • Additive metrics (sum across dimensions)<br>• Semi-additive (sum across some dimensions)<br>• Non-additive (ratios, averages)<br>• Derived metrics (calculated fields) | • Pre-aggregated fact tables<br>• Calculated measures in BI tools<br>• Metric layers (dbt, Looker) |
| **Grain Considerations** | • One row per transaction/event<br>• One row per customer-day<br>• One row per customer-product-month<br>• Multiple grains for different use cases | • Atomic fact tables<br>• Aggregated fact tables<br>• Bridge tables for many-to-many |
| **Common Data Sources** | • Transactional databases (OLTP)<br>• Application event logs<br>• Third-party integrations (payments, ads)<br>• Customer data platforms (CDPs) | • ETL/ELT pipelines<br>• Change Data Capture (CDC)<br>• Event streaming (Kafka) |
| **Dimensional Modeling** | • Star schema as foundation<br>• Conformed dimensions across business processes<br>• SCD for historical tracking<br>• Dimension hierarchies for drill-down | • Kimball methodology<br>• Data vault (for enterprise DW)<br>• Dimensional bus matrix |
| **Time Patterns** | • Point-in-time snapshots<br>• Period-over-period analysis<br>• Cohort retention curves<br>• Seasonality adjustments<br>• Trend analysis | • Date spine tables<br>• Calendar vs fiscal periods<br>• Event date vs reporting date |
| **User/Customer Tracking** | • Unique identifiers (customer_id, user_id)<br>• Anonymous → identified resolution<br>• Multi-device identity stitching<br>• Household/account aggregation | • Identity graphs<br>• Cookie → User ID mapping<br>• Cross-device tracking |

---

## Business Analytics Sub-Categories

### BI / Reporting Analytics

**Focus:** Historical business performance - "What happened?"

**Unique Characteristics:**
- Broad coverage across all business functions
- Regular reporting cadence (daily/weekly/monthly)
- Executive dashboards and KPI tracking
- Variance analysis (Budget vs Actual)

**Unique Dimensions:**
- **Department/Cost Center** - Organizational hierarchy
- **GL Account** - Financial chart of accounts
- **Business Unit/Division** - Corporate structure
- **Project/Initiative** - Strategic program tracking

**Unique Metrics:**
- Revenue (recognized, deferred, recurring)
- Gross Margin, EBITDA
- Budget variance
- Headcount, productivity ratios
- Inventory levels, turns

**Unique Time Patterns:**
- Fiscal period close processes
- Board reporting cycles
- Regulatory reporting deadlines
- Annual planning cycles

**Standard Models:**
- Sales Analysis (Orders, Revenue, Units)
- Financial P&L rollups
- Inventory Management
- Operational KPI dashboards

**Common Tools:**
- Tableau, Power BI, Looker
- SAP BusinessObjects, Oracle OBIEE
- Google Data Studio
- dbt + BI layer

---

### Product Analytics

**Focus:** User behavior and product engagement - "How do users interact with our product?"

**Unique Characteristics:**
- Event-based data model
- Session-level analysis
- Funnel conversion tracking
- Cohort retention analysis
- A/B testing frameworks

**Unique Dimensions:**
- **Event Type** - Page view, click, purchase, signup
- **Session** - Session ID, entry point, exit point
- **Feature** - Product feature, page section, UI element
- **Device/Platform** - Mobile app, web, OS version, browser
- **Experiment/Variant** - A/B test assignment

**Unique Metrics:**
- Daily Active Users (DAU), Monthly Active Users (MAU)
- DAU/MAU ratio (stickiness)
- Retention (Day 1, Day 7, Day 30)
- Session duration, sessions per user
- Feature adoption rate
- Funnel conversion rates
- Time to value (TTV)

**Unique Time Patterns:**
- Cohort retention curves (% retained over time)
- Activation windows (first 7 days)
- Feature usage frequency distributions
- Session clustering (power users vs casual)

**Standard Models:**
- AARRR Pirate Metrics (Acquisition, Activation, Retention, Revenue, Referral)
- Product-Market Fit scoring
- Engagement ladder (new → casual → core → power users)
- Feature usage matrix

**Event Schema Pattern:**
```
Events Fact Table:
- event_id
- user_id
- session_id
- event_type
- event_timestamp
- event_properties (JSON)
- device_id
- platform
```

**Common Tools:**
- Amplitude, Mixpanel
- Google Analytics 4
- Heap, PostHog
- Segment (CDP) + warehouse

---

### Marketing Analytics

**Focus:** Campaign performance and customer acquisition - "Which marketing efforts drive results?"

**Unique Characteristics:**
- Multi-touch attribution
- Campaign ROI tracking
- Customer journey mapping
- Media mix modeling
- Incrementality testing

**Unique Dimensions:**
- **Campaign** - ID, name, type, objective
- **Channel** - Paid search, organic, email, social, display
- **Creative** - Ad copy, image, video variant
- **Audience/Segment** - Targeting criteria
- **Touchpoint** - Position in customer journey (first, middle, last)
- **UTM Parameters** - Source, medium, campaign, content, term

**Unique Metrics:**
- Customer Acquisition Cost (CAC)
- Return on Ad Spend (ROAS)
- Cost Per Click (CPC), Cost Per Acquisition (CPA)
- Click-Through Rate (CTR)
- Conversion Rate by channel
- Attribution credit (first-touch, last-touch, multi-touch)
- Marketing Qualified Leads (MQLs)
- LTV:CAC ratio

**Unique Time Patterns:**
- Campaign flight dates (start/end)
- Attribution windows (7-day click, 1-day view)
- Attribution decay curves
- Lift measurement periods
- Media saturation curves

**Standard Models:**
- Multi-Touch Attribution (MTA)
  - Linear, Time-decay, Position-based, Data-driven
- Marketing Mix Modeling (MMM)
- Customer Journey Analytics
- Channel performance dashboards
- Campaign ROI analysis

**Attribution Pattern:**
```
Touchpoints Fact Table:
- touchpoint_id
- user_id
- campaign_id
- channel
- timestamp
- touchpoint_position (first, middle, last)
- attribution_credit (calculated)
- conversion_id (if converted)
```

**Common Tools:**
- Google Analytics, Adobe Analytics
- Facebook Ads Manager, Google Ads
- HubSpot, Marketo
- Attribution platforms (Rockerbox, Northbeam)
- MMM tools (Recast, Meridian)

---

### Customer Analytics

**Focus:** Customer lifetime value and behavior - "Who are our customers and what are they worth?"

**Unique Characteristics:**
- Customer as unit of analysis
- Lifetime value calculations
- Segmentation and scoring
- Churn prediction
- Next best action recommendations

**Unique Dimensions:**
- **Customer Segment** - High-value, at-risk, champion, etc.
- **Lifecycle Stage** - Prospect, new, active, churned, reactivated
- **Acquisition Source** - How customer was originally acquired
- **RFM Bucket** - Recency/Frequency/Monetary score buckets
- **Contract/Subscription** - Plan type, tenure, renewal date

**Unique Metrics:**
- Customer Lifetime Value (CLV/LTV)
- Recency, Frequency, Monetary (RFM) scores
- Churn rate (customer, revenue)
- Retention rate
- Net Promoter Score (NPS)
- Customer Satisfaction (CSAT)
- Customer Health Score
- Expansion revenue (upsell/cross-sell)
- Average Revenue Per User (ARPU)

**Unique Time Patterns:**
- Customer tenure (time since first purchase)
- Days since last activity (recency)
- Purchase frequency distributions
- Cohort retention curves
- Contract renewal cycles
- Reactivation windows

**Standard Models:**
- RFM Segmentation
- Customer Lifetime Value (CLV) prediction
  - Historical CLV (sum of past purchases)
  - Predictive CLV (forecast future value)
- Customer Health Scoring
- Churn Prediction Models
- Next Best Offer/Action
- Win-back campaigns

**Customer Snapshot Pattern:**
```
Customer Summary Table (One row per customer):
- customer_id
- first_purchase_date
- last_purchase_date
- total_orders
- total_revenue
- avg_order_value
- rfm_score
- ltv_predicted
- segment
- churn_risk_score
- last_updated_timestamp
```

**Common Tools:**
- Customer Data Platforms (Segment, mParticle)
- CRM Analytics (Salesforce, HubSpot)
- Specialized CLV tools (Custora, Retained)
- Data warehouse + ML (dbt + Python)

---

## Web/Digital Analytics: A Surface-Dependent Category

Unlike the previous four categories (BI, Product, Marketing, Customer), Web/Digital Analytics is not defined by a business function but by a **channel or surface**. It exists as its own category because:

1. **Multiple business activities converge on "the web"** - Product usage, marketing campaigns, customer service, and commerce all happen on digital surfaces
2. **Unified instrumentation** - A single tracking implementation (Google Analytics, Adobe Analytics) captures events that span Product, Marketing, and Customer concerns
3. **Shared session context** - The same user session might involve product exploration (Product Analytics), campaign attribution (Marketing Analytics), and customer identification (Customer Analytics)

**What Web Analytics Really Is:**

Web/Digital Analytics is the **aggregation of instrumentation on a digital surface**. Any of the functional analytics above (BI, Product, Marketing, Customer) can happen "on the web" - what makes Web Analytics distinct is that it measures all of them through a unified lens tied to the channel itself.

| Functional Category | When It Happens on Web | Web Analytics Captures |
|---------------------|------------------------|------------------------|
| **Product Analytics** | User engages with product features in browser/app | Feature usage, funnels, session behavior |
| **Marketing Analytics** | User arrives via campaign, ad click, email | Traffic source, attribution, landing pages |
| **Customer Analytics** | User identifies, purchases, returns | Visitor identification, conversion, journey |
| **BI/Reporting** | Business outcomes tracked online | Revenue, transactions, KPIs |

**Why It's Its Own Category:**

Most businesses have significant activity on web/digital surfaces, making it practical to have dedicated:
- **Tooling** - Google Analytics, Adobe Analytics, specialized web tools
- **Teams** - Web analytics specialists, digital marketing teams
- **Metrics** - Sessions, pageviews, bounce rate (surface-specific measures)
- **Infrastructure** - Tag managers, tracking pixels, session recording

The channel-centric view provides value because:
- It is where the **instrumentation converges** - one tag manager, one analytics platform
- It captures **cross-functional user journeys** in a single session
- It enables **surface-specific optimization** - page load speed, mobile experience, SEO

### Surface-Specific Dimensions

- **Session** - Session ID, entry page, exit page, duration
- **Page/Content** - URL, page title, content type, category
- **Traffic Source** - Direct, organic search, paid search, referral, social
- **Referrer** - Specific referring site/page
- **Search Terms** - Keywords (organic and paid)
- **Landing Page** - Entry point to site
- **Device/Browser** - Desktop, mobile, tablet; Chrome, Safari, etc.

**Surface-Specific Metrics:**
- Sessions, Pageviews, Unique Visitors
- Bounce Rate (single-page sessions)
- Pages per Session, Average Session Duration
- Exit Rate (% exiting from specific page)
- Site Search usage

These are **instrumentation-level metrics** - they measure engagement with the surface itself, not the underlying business function.

**Standard Models:**
- **Acquisition → Behavior → Conversion** (Google Analytics model)
- Content engagement scoring
- Landing page optimization
- Site search analysis

**Session Pattern:**
```
Sessions Fact Table:
- session_id
- user_id (if identified)
- session_start_timestamp
- session_end_timestamp
- session_duration_seconds
- pageviews
- entry_page
- exit_page
- traffic_source
- traffic_medium
- campaign
- device_category
- converted (boolean)
```

**Key Insight:** Web/Digital Analytics is not a fifth business function - it's the **measurement layer for a surface** where multiple business functions converge. Organizations treat it as its own category because the web surface is so significant that it warrants dedicated instrumentation, tooling, and expertise.

### Common Tools

- Google Analytics (UA, GA4)
- Adobe Analytics
- Matomo (open-source)
- Heap, FullStory (session replay)
- Tag management: Google Tag Manager, Tealium

### Why Web/Digital is the Primary Surface-Dependent Category

Other surfaces exist where business functions converge, but they haven't achieved the same category status as Web/Digital Analytics. Here's why:

| Surface | Functions Converge? | Unified Instrumentation? | Dedicated Category? | Why/Why Not |
|---------|---------------------|-------------------------|---------------------|-------------|
| **Web/Digital** | ✅ Product, Marketing, Customer, Commerce | ✅ GA, Adobe, tag managers | ✅ "Web Analytics" | Ubiquitous surface, mature tooling, dedicated teams |
| **Mobile App** | ✅ Same as web | ✅ Firebase, Adjust, AppsFlyer | ⚠️ Merged into "Digital" | Treated as part of the same digital surface (GA4 handles both) |
| **POS/Retail** | ✅ Sales, Inventory, Customer, Payments | ✅ POS systems (Square, Shopify POS) | ⚠️ Absorbed into BI | Metrics (basket size, tender mix) are more transactional than behavioral |
| **Call Center** | ✅ Sales, Support, Customer | ✅ CCaaS platforms (Genesys, Five9) | ⚠️ Part of Customer Analytics | Contact center metrics often rolled into CX or Customer Analytics |
| **IoT/Sensors** | ⚠️ Mostly Ops + Supply Chain | ✅ IoT platforms (Azure IoT, AWS IoT) | ❌ Operational Analytics | Not business analytics - more about machine health, predictive maintenance |

**The test for surface-dependent analytics category:**
1. **Multiple business functions converge** on this surface
2. **Unified instrumentation** exists for this surface
3. **Dedicated tooling, teams, and metrics** have emerged as their own discipline

Web/Digital passes all three tests decisively. Other surfaces either:
- Get absorbed into existing functional categories (POS → BI, Call Center → Customer)
- Are treated as part of the same digital surface (Mobile → Digital Analytics)
- Fall into operational rather than business analytics (IoT → Operational Analytics)

**Why this matters:** When building analytics infrastructure, recognize that Web/Digital Analytics is a special case. It's not a fifth business function to model alongside BI, Product, Marketing, and Customer - it's the instrumentation layer for a surface that captures all of them. Other surfaces should be modeled as part of their respective functional categories, not as parallel surface-dependent categories.

---

## Other Analytics Categories

While **Business Analytics** covers the core commercial analysis needs (including digital surfaces), organizations also require:

### Operational Analytics
- Real-time system monitoring
- APM (Application Performance Monitoring)
- Infrastructure metrics
- SLA tracking

### Financial Analytics
- General ledger analysis
- Budget planning & forecasting
- Cash flow management
- Regulatory compliance reporting

### Supply Chain Analytics
- Inventory optimization
- Demand forecasting
- Supplier performance
- Logistics tracking

### Predictive/ML Analytics
- Feature engineering pipelines
- Model performance tracking
- A/B testing frameworks
- Recommendation engines

---

## Reference: Standard Patterns & Frameworks

### Dimensional Modeling Standards

- **Kimball Methodology** - The gold standard for dimensional modeling
- **Data Vault 2.0** - For enterprise data warehouses with audit requirements
- **Inmon Approach** - Normalized enterprise data warehouse

### Industry Frameworks

- **APQC Process Classification Framework** - Cross-industry process models
- **SCOR Model** - Supply chain operations reference
- **ITIL** - IT service management

### Metric Standards

- **SaaS Metrics** (Christoph Janz, David Skok)
  - ARR, MRR, Churn, CAC, LTV
- **E-commerce Metrics**
  - GMV, AOV, Conversion Rate, Cart Abandonment
- **Consumer Apps**
  - DAU/MAU, Retention, Session Duration

### Open Schemas & Standards

- **Google Analytics 4** - Event-based digital analytics schema
- **Segment Spec** - Track, Identify, Page, Screen, Group specs
- **Common Data Model (Microsoft)** - Cross-business schemas
- **FHIR** (Healthcare), **ACORD** (Insurance), **FIX** (Financial markets)

---

## Key Takeaways

1. **Business Analytics shares common foundations:**
   - All aggregate from transactional/event sources
   - All use dimensional modeling (Star schema, conformed dimensions)
   - All rely on time-series analysis with common patterns
   - All center around Customer, Product, Time, Channel dimensions

2. **Sub-categories differ in:**
   - **Focus/Perspective** - What question they answer
   - **Unique dimensions** - Domain-specific attributes
   - **Unique metrics** - Category-specific KPIs
   - **Time patterns** - Analysis cadences and windows

3. **The pattern is reusable:**
   - Start with common dimensional foundation
   - Add category-specific extensions
   - Use same modeling techniques
   - Leverage shared infrastructure (ETL, BI tools, data warehouse)

4. **Modern data stack enables:**
   - Single source of truth (data warehouse)
   - Shared semantic layer (dbt, Looker, MetricFlow)
   - Consistent metric definitions across categories
   - Cross-functional analysis (combining Product + Marketing + Customer)

This commonality is why organizations can achieve **[semantic coherence](../SEMANTIC_OPTIMIZATION/semantic-coherence.md)** across business analytics - the underlying patterns are consistent, allowing for shared understanding and reusable components.

---

## Related Links

### Framework Components

- [Strategic Data](README.md) - Parent framework
- [The Four Data System Types](four-data-system-types.md) - System types that produce analytics data
- [Data Silos Explained](data-silos.md) - Why analytics patterns are shared, not siloed
- [Surface Analysis](surface-analysis.md) - Where analytics data originates

### Related Concepts

- [Semantic Coherence](../SEMANTIC_OPTIMIZATION/semantic-coherence.md) - Shared meaning across analytics
- [Patterns](../SEMANTIC_OPTIMIZATION/patterns.md) - Self-contained semantic structures
