---
doc_type: hub

pattern: foundations-data

provenance: 3p

metadata:
    pattern_type: concept
    brand_strength: low
---
# Foundations: Data Systems

> Foundational principles for understanding **how data and data systems actually work** and **how meaning and knowledge are derived**. These aren't best practices or trends—they're the immutable constraints and realities that determine whether data systems function.

---

## Relational Model & Relational Algebra

**What it is:**
The mathematical foundation of relational databases, defining how data should be structured, related, and queried through set theory and first-order predicate logic, introduced by [Codd (1970)](https://doi.org/10.1145/362384.362685).

**Key ideas:**
- Tables (relations), rows (tuples), columns (attributes)
- Normalization forms (1NF, 2NF, 3NF, BCNF)
- Keys: primary, foreign, candidate
- Relational algebra operations (select, project, join, union)
- ACID properties

**Why it matters:**
All modern data patterns—star schemas, dimensional modeling, feature stores—derive from this mathematical foundation. The physics of relational data.

**Sources:**
- Codd, E.F. (1970). "A Relational Model of Data for Large Shared Data Banks." *Communications of the ACM*, 13(6), 377-387
- DOI: 10.1145/362384.362685

---

## Entity-Relationship Model

**What it is:**
A conceptual framework for modeling how real-world entities, their attributes, and relationships become structured data schemas, developed by [Chen (1976)](https://doi.org/10.1145/320434.320440).

**Key ideas:**
- Entities: Things that exist independently
- Attributes: Properties of entities
- Relationships: Connections between entities
- Cardinality: One-to-one, one-to-many, many-to-many
- Bridge from conceptual (meaning) to logical (structure) to physical (implementation)

**Why it matters:**
Shows how abstractions become schemas—the critical translation from business concepts to data structures. Essential for understanding how meaning gets encoded.

**Sources:**
- Chen, P. (1976). "The Entity-Relationship Model—Toward a Unified View of Data." *ACM Transactions on Database Systems*, 1(1), 9-36
- DOI: 10.1145/320434.320440

---

## Dimensional Modeling

**What it is:**
A data warehousing methodology using star schemas (fact tables surrounded by dimension tables) optimized for analytical queries and business intelligence, developed by [Kimball (1996)](https://www.kimballgroup.com/).

**Key ideas:**
- Facts: Measurable business events (transactions, observations)
- Dimensions: Context for facts (who, what, when, where, why)
- Star schema: Facts joined to dimensions
- Grain: Level of detail in fact table
- Conformed dimensions: Shared dimensions across fact tables
- Slowly Changing Dimensions (SCDs)

**Why it matters:**
Explains the mathematical requirements for analytics. Star schemas are not "best practice"—they are physics. Required for OLAP engines, BI tools, and feature stores.

**Sources:**
- Kimball, R. (1996). *The Data Warehouse Toolkit: Practical Techniques for Building Dimensional Data Warehouses*. Wiley. ISBN: 978-0471153375. https://www.kimballgroup.com/data-warehouse-business-intelligence-resources/books/data-warehouse-dw-toolkit/
- https://www.kimballgroup.com/

---

## Data Warehouse Architecture

**What it is:**
Enterprise-scale architecture for subject-oriented, integrated, time-variant, non-volatile data storage designed to support decision-making, developed by [Inmon (2005)](https://www.inmoncif.com/).

**Key ideas:**
- Subject-oriented (organized around business subjects, not applications)
- Integrated (consistent across sources)
- Time-variant (historical perspective)
- Non-volatile (stable, append-only)
- Normalized data model in the warehouse
- Dimensional data marts for analytics

**Why it matters:**
Provides the "soup to nuts" end-to-end view of how to architect data systems at enterprise scale. Foundation for understanding data platform design.

**Sources:**
- Inmon, W.H. (2005). *Building the Data Warehouse* (4th ed.). Wiley. ISBN: 978-0764599446. https://www.wiley.com/en-us/Building+the+Data+Warehouse,+4th+Edition-p-9780764599446
- https://www.inmoncif.com/

---

## Statistical Foundations

**What it is:**
The mathematical science of extracting meaning from noisy data, quantifying uncertainty, and making probabilistic statements.

**Key ideas:**
- Descriptive statistics: Central tendency, variance, distributions
- Inferential statistics: Hypothesis testing, confidence intervals, sampling theory
- Bayesian statistics: Prior/posterior, probabilistic reasoning
- Statistical learning: Bias-variance tradeoff, overfitting, generalization
- Correlation vs. causation

**Why it matters:**
Data cannot be interpreted without understanding distributions, variance, and sampling. Metrics are meaningless without significance and confidence intervals. Foundation for ML/AI and all quantitative reasoning.

**Sources:**
- Freedman, D., Pisani, R., & Purves, R. (2007). *Statistics* (4th ed.). W.W. Norton. ISBN: 978-0393929720. https://wwnorton.com/books/9780393929720
- https://en.wikipedia.org/wiki/Statistics

---

## Data Profiling

**What it is:**
Systematic examination of data to understand its actual state (not assumed state)—analyzing distributions, patterns, completeness, uniqueness, validity, and anomalies.

**Key ideas:**
- Examine distributions and statistical properties
- Identify patterns, outliers, and anomalies
- Assess completeness and quality
- Discover relationships between columns
- Profile before modeling (not after)

**Why it matters:**
Schemas cannot be designed without knowing what data actually exists. Assumptions kill data projects; profiling reveals truth. Statistics without profiling equals garbage in, garbage out.

**Sources:**
- Olson, J.E. (2003). *Data Quality: The Accuracy Dimension*. Morgan Kaufmann. ISBN: 978-1558608917. https://www.elsevier.com/books/data-quality/olson/978-1-55860-891-7
- https://en.wikipedia.org/wiki/Data_profiling

---

## Data Lineage & Provenance
*(W3C PROV)*

**What it is:**
Lineage tracks data flow through systems (transformations, pipelines, dependencies). Provenance tracks data origin, custody, and derivation (who created it, when, why, under what conditions).

**Key ideas:**
- Lineage: What happened to data within a system
- Provenance: Where data came from before entering the system
- Complete audit trail from source to decision
- Impact analysis and root-cause debugging
- Trust chain verification

**Why it matters:**
Data cannot be trusted without knowing its lineage and provenance. Essential for debugging, governance, reproducibility, and treating data as a first-class asset.

**Sources:**
- W3C PROV Model: https://www.w3.org/TR/prov-overview/
- https://en.wikipedia.org/wiki/Data_lineage

---

## CAP Theorem
*(Eric Brewer)*

**What it is:**
A fundamental constraint in distributed systems stating you can only guarantee 2 of 3 properties: Consistency, Availability, Partition tolerance.

**Key ideas:**
- Consistency: All nodes see the same data
- Availability: System responds to requests
- Partition tolerance: System functions despite network failures
- Trade-offs are unavoidable in distributed systems
- No perfect distributed database exists

**Why it matters:**
Immutable physical constraints in distributed systems—you cannot ignore or engineer around these limits. Shapes all modern data architecture decisions.

**Sources:**
- Gilbert, S., & Lynch, N. (2002). "Brewer's Conjecture and the Feasibility of Consistent, Available, Partition-Tolerant Web Services." *ACM SIGACT News*, 33(2), 51-59
- DOI: 10.1145/564585.564601

---

## Data Governance (DAMA-DMBOK)

**What it is:**
Comprehensive framework for managing data as an enterprise asset—encompassing quality, lineage, stewardship, security, and organizational roles.

**Key ideas:**
- Data quality management
- Metadata management
- Data lineage and provenance
- Data stewardship and ownership
- Master data management
- Governance as structural integrity, not just compliance

**Why it matters:**
Governance is about data quality, lineage, and provenance—structural integrity that enables trust and organizational effectiveness, not just access control.

**Sources:**
- DAMA International (2017). *DAMA-DMBOK: Data Management Body of Knowledge* (2nd ed.). Technics Publications. ISBN: 978-1634622349
- https://www.dama.org/cpages/body-of-knowledge

---

## Reference Architecture Patterns

**What it is:**
Standard patterns and blueprints for data system design that encode hard-learned lessons from industry experience.

**Key ideas:**
- Lambda Architecture: Batch + stream processing
- Medallion/Lakehouse: Bronze/silver/gold data layers (raw → refined → curated)
- Data Mesh: Decentralized data ownership with federated governance
- Kappa Architecture: Stream-first processing
- Common vocabulary for system design

**Why it matters:**
Reusable patterns instead of reinventing architecture. Provides end-to-end view of how components fit together. Encodes proven solutions to common problems.

**Sources:**
- Nathan Marz: *Big Data* (Lambda Architecture)
- Databricks Lakehouse: https://www.databricks.com/glossary/medallion-architecture
- Data Mesh: https://martinfowler.com/articles/data-mesh-principles.html

---
