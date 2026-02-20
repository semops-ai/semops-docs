# Research: How "Architecture" Is Defined Across Industry

> **Purpose:** Evidence base for Issue #110 — backing the claim that most organizations (and the industry itself) conflate architecture with infrastructure.
> **Date:** 2026-02-18
> **Related Issue:** [semops-docs#110](https://github.com/semops-ai/semops-docs/issues/110)

---

## Executive Summary

The industry's own definitions prove the point: mainstream sources define "architecture" primarily in terms of infrastructure — where code runs, how data flows, what tools are used. Only DDD practitioners and a handful of respected voices define architecture in terms of business rules, domain semantics, and consequential decisions.

**The gradient runs from pure infrastructure to pure domain meaning:**

```text
INFRASTRUCTURE ◄──────────────────────────────────► DOMAIN MEANING

Snowflake     Informatica    AWS/IBM    TOGAF/Gartner    Fowler/Booch/Evans
(pure infra)  (data verbs)   (mixed)    (principles)     (decisions, rules, domain)
```

---

## Part 1: Mainstream & Reference Definitions

### Wikipedia — Software Architecture

> "Software architecture design focuses on designing the infrastructure within which application functionality can be realized and executed such that the functionality is provided in a way which meets the system's non-functional requirements."

**Source:** https://en.wikipedia.org/wiki/Software_architecture

**Analysis:** Explicitly frames architecture as infrastructure. Creates a binary: architecture = infrastructure, application design = actual domain functionality. This is the definition most people encounter first.

### Wikipedia — Data Architecture

> "Data architecture is composed of models, policies, rules, and standards that govern which data is collected and how it is stored, arranged, integrated, and put to use in data systems and in organizations."

**Source:** https://en.wikipedia.org/wiki/Data_architecture

**Analysis:** Entirely about the mechanical lifecycle of data — storage, arrangement, integration. No mention of what the data means or what business rules it encodes.

### IEEE 1471:2000

> "The fundamental organization of a system embodied in its components, their relationships to each other, and to the environment, and the principles guiding its design and evolution."

**Source:** http://www.iso-architecture.org/ieee-1471/defining-architecture.html

**Analysis:** Structural framing. Includes "principles guiding design and evolution" but the emphasis is on components and relationships — easily read as infrastructure components.

### ISO/IEC/IEEE 42010:2011 (current standard, supersedes IEEE 1471)

> "Fundamental concepts or properties of a system in its environment embodied in its elements, relationships, and in the principles of its design and evolution."

**Source:** https://en.wikipedia.org/wiki/ISO/IEC_42010

**Analysis:** Notable improvement — shifts from "fundamental organization" to "fundamental concepts or properties." This nuance has not propagated into popular definitions.

### Bass, Clements, Kazman (SEI/CMU canonical textbook)

> "The software architecture of a system is the set of structures needed to reason about the system. These structures comprise software elements, relations among them, and properties of both."

**Source:** *Software Architecture in Practice*, 3rd Edition

**Analysis:** Emphasizes structural reasoning. No explicit mention of domain rules, business semantics, or the "why" behind decisions.

---

## Part 2: Vendor & Analyst Definitions

### TOGAF / The Open Group

**General Architecture:**

> "The structure of components, their inter-relationships, and the principles and guidelines governing their design and evolution over time."

**Data Architecture:**

> "The Data Architecture describes the structure of an organization's logical and physical data assets and data management resources."

**Source:** https://pubs.opengroup.org/architecture/togaf9-doc/arch/chap02.html

**Analysis:** The general definition is abstract and principles-oriented — notably does not mention specific technologies. The data architecture definition, however, falls back to "logical and physical data assets" — pure plumbing.

### Gartner

> "A series of principles, guidelines, or rules used by an enterprise to direct the process of acquiring, building, modifying, and interfacing IT resources throughout the enterprise."

**Source:** https://www.gartner.com/en/information-technology/glossary/architecture

**Analysis:** Places architecture in the domain of organizational decision-making and governance, not technology selection. One of the better mainstream definitions.

### AWS

**Data Architecture:**

> "Data architecture is the overarching framework that describes and governs an organization's data collection, management, and usage."

**Source:** https://aws.amazon.com/what-is/data-architecture/

**Analysis:** Opens with governance-centric language but the page content immediately pivots to AWS services (S3, Redshift, Kinesis). The definition is a funnel to infrastructure products.

### Microsoft

**Software Architecture (Patterns & Practices):**

> "Software application architecture is the process of defining a structured solution that meets all of the technical and operational requirements, while optimizing common quality attributes such as performance, security, and manageability."

**Architecture Styles (Azure Architecture Center):**

> "An architecture style places constraints on the design, including the set of elements that can appear and the allowed relationships between those elements."

**Source:** https://learn.microsoft.com/en-us/previous-versions/msp-n-p/ee658098(v=pandp.10) and https://learn.microsoft.com/en-us/azure/architecture/guide/architecture-styles/

**Analysis:** The Azure Architecture Center is an outlier among vendors — explicitly distinguishes architecture from technology choices and focuses on constraints and relationships. The Patterns & Practices definition bridges business and technical requirements.

### IBM

**Data Architecture:**

> "A data architecture describes how data is managed — from collection and transformation through distribution and consumption — setting the blueprint for how it flows through the organization."

**Source:** https://www.ibm.com/think/topics/data-architecture

**Analysis:** Pure plumbing. Collection, transformation, distribution, consumption, flow.

### Snowflake

> "Snowflake's architecture is a hybrid of traditional shared-disk and shared-nothing database architectures."

**Source:** https://docs.snowflake.com/en/user-guide/intro-key-concepts

**Analysis:** Defines "architecture" exclusively in terms of platform technical design. The most extreme example of architecture = infrastructure in the entire survey.

### Informatica

> "Data architecture is the blueprint for how data is collected, structured, transformed, managed, stored and used in an enterprise."

**Source:** https://www.informatica.com/resources/articles/modern-data-architecture.html

**Analysis:** Data processing verbs + platforms, tools, and storage solutions. Infrastructure through and through.

### Databricks

> "Data architecture is the blueprint for how organizations collect, store, and use data."

**Source:** https://www.databricks.com/glossary/data-architecture

**Analysis:** Leans infrastructure. "Blueprint" framing echoed by Informatica.

### Dataversity

> "Data architecture describes the infrastructure that connects a Business Strategy and Data Strategy with technical execution."

**Source:** https://www.dataversity.net/what-is-data-architecture/

**Analysis:** Explicitly uses the word "infrastructure." The most candid about the conflation.

---

## Part 3: DDD Practitioners & Respected Authorities

### Martin Fowler & Ralph Johnson

**Ralph Johnson (via Fowler):**

> "In most successful software projects, the expert developers working on that project have a shared understanding of the system design. This shared understanding is called 'architecture.'"

> "Architecture is the decisions that you wish you could get right early in a project."

**Martin Fowler's preferred definition:**

> "Architecture is about the important stuff. Whatever that is."

**On irreversibility:**

> "Software architecture is those decisions which are both important and hard to change."

> "I think that one of an architect's most important tasks is to remove architecture by finding ways to eliminate irreversibility in software designs."

**Sources:**
- https://martinfowler.com/architecture/
- https://martinfowler.com/ieeeSoftware/whoNeedsArchitect.pdf (IEEE Software, 2003)

**Key insight:** Architecture is a social construct (shared understanding) combined with consequential decisions (hard and expensive to reverse). Fowler redefines the architect's job as reducing the number of decisions that qualify as architectural.

### Grady Booch

> "Architecture represents the significant design decisions that shape a system, where significant is measured by cost of change."

> "All architecture is design but not all design is architecture."

> "Architectural styles come and go but the enduring fundamentals (crisp abstractions; clear separation of concerns; balanced distribution of responsibilities; simplicity) endure and never go out of style."

**Source:** "On Design" (2006); https://www.bredemeyer.com/whatis.htm

**Key insight:** Introduces an economic test — significance measured by cost of change. The enduring fundamentals are all conceptual properties, not technology choices.

### Eric Evans (Domain-Driven Design)

> "The heart of software is its ability to solve domain-related problems for its user."

> "A model is a selectively simplified and consciously structured form of knowledge."

> "The vital detail about the design is captured in the code. A well-written implementation should be transparent, revealing the model underlying it."

**Source:** *Domain-Driven Design* (2003); https://www.goodreads.com/work/quotes/173058

**Key insight:** Evans never provides a tidy "architecture is X" definition. In DDD, architecture is not a separate concern imposed on the domain — it emerges from deep engagement with domain complexity. The most consequential architectural decisions are about how you model the problem space, not which database or framework you select.

### Vaughn Vernon

> "Architecture emerges as a result of proper domain modeling."

**Source:** https://www.infoq.com/news/2013/04/DDD-Architecture-Styles/

**Key insight:** Architecture styles (Hexagonal, CQRS, Event Sourcing) serve to protect and enable the domain model, not the other way around. This directly inverts the vendor-centric view.

### Mark Richards & Neal Ford

Four-dimensional definition:

1. **Structure** — the architecture style(s) used
2. **Architecture Characteristics** — success criteria orthogonal to functionality (the "-ilities")
3. **Architecture Decisions** — rules for how the system should be constructed
4. **Design Principles** — guidelines that inform construction

**First Law:** "Everything in software architecture is a trade-off."

**Second Law:** "Why is more important than how."

**Source:** *Fundamentals of Software Architecture* (O'Reilly, 2020); http://fundamentalsofsoftwarearchitecture.com/

**Key insight:** "Describing an architecture solely by the structure does not wholly elucidate an architecture." Knowing you use Kubernetes + PostgreSQL + AWS tells you about structure at best — nothing about characteristics, decisions, or principles.

### Gregor Hohpe

**On what architects actually do:**

> "I sell options."

**On complexity:**

> "Excessive complexity is nature's punishment for those who are unable to make decisions."

**On the architect's domain:**

> "Good architects, therefore, deal with change. This means that they live in the system's first derivative."

**On architecture vs. title:**

> "It's not anything on your business card... It's really a way of thinking."

**Sources:**
- https://architectelevator.com/architecture/architecture-options/
- https://www.infoq.com/presentations/architect-lessons/
- *The Software Architect Elevator* (O'Reilly, 2020)

**Key insight:** An option has value because it defers commitment. Infrastructure is a commitment already made. Architecture creates the ability to choose different infrastructure later.

### Simon Brown (C4 Model)

> "Architecture is about the stuff that's hard or costly to change. It's about the big or 'significant' decisions."

**Source:** *Software Architecture for Developers*; https://c4model.com/

### Eoin Woods

> "Software architecture is the set of design decisions which, if made incorrectly, may cause your project to be cancelled."

**Source:** Referenced in *Software Architecture in Practice* (3rd Ed., SEI); https://www.bredemeyer.com/whatis.htm

**Key insight:** Risk-based test for architecture. If a decision can kill the project when wrong, it is architectural.

---

## Part 4: Synthesis

### The Evidence

| Source Category | How "Architecture" Is Defined | Example |
|----------------|-------------------------------|---------|
| **Data platform vendors** | Infrastructure components | "collected, structured, transformed, managed, stored" (Informatica) |
| **Cloud vendors** | Governance language → infrastructure products | "describes and governs... collection, management, and usage" → S3/Redshift (AWS) |
| **Reference encyclopedias** | Infrastructure with academic framing | "designing the infrastructure within which application functionality can be realized" (Wikipedia) |
| **Standards bodies** | Abstract structure + principles | "fundamental concepts or properties... principles of design and evolution" (ISO 42010) |
| **Analyst firms** | Principles and governance | "principles, guidelines, or rules" (Gartner) |
| **DDD / respected practitioners** | Consequential decisions + domain meaning | "significant decisions... measured by cost of change" (Booch); "shared understanding" (Johnson); "the heart of software is domain problems" (Evans) |

### What Architecture IS (Practitioner Consensus)

| Dimension | Source |
|-----------|--------|
| Significant decisions measured by cost of change | Booch, Fowler, Brown, Woods |
| Shared understanding among expert developers | Johnson, Brown |
| Trade-offs between competing concerns | Richards/Ford (First Law), Hohpe |
| Fundamental concepts + principles of evolution | IEEE/ISO 42010 |
| The reasoning (why), not just the result (how) | Richards/Ford (Second Law) |
| Options that defer commitment under uncertainty | Hohpe |
| What emerges from deep domain understanding | Evans, Vernon |
| Risk management — decisions that can kill the project | Woods |

### What Architecture IS NOT (But Is Commonly Called Architecture)

| Misconception | Why It Fails |
|---------------|--------------|
| Architecture = infrastructure selection | Infrastructure is ONE decision; architecture is the reasoning framework that determines WHICH infrastructure decisions matter and WHY |
| Architecture = technology stack | Technology choices may or may not be architectural depending on cost of change (Booch) and project risk (Woods) |
| Architecture = diagrams | Architecture is shared understanding; diagrams are communication tools (Johnson, Brown) |
| Architecture = the deployment topology | Deployment is one structural dimension; architecture also includes characteristics, decisions, and principles (Richards/Ford) |
| Architecture = big-design-up-front | Decisions you WISH you could get right early, not ones that MUST be made early (Johnson); good architecture REDUCES irreversibility (Fowler) |

---

## Part 5: The Claim — Supported

**Claim from Issue #110:** "The industry conflates infrastructure with architecture."

**Evidence:**
1. Wikipedia's software architecture definition explicitly equates architecture with infrastructure
2. Every data architecture definition from vendors (Snowflake, Informatica, Databricks, IBM, AWS) describes data plumbing — collection, storage, transformation, movement
3. Dataversity literally says data architecture "describes the infrastructure"
4. Google searching "data architecture" returns content about ETL pipelines, databases, and cloud storage
5. The only definitions that center domain meaning come from DDD practitioners (Evans, Vernon) and a handful of respected software thinkers (Fowler, Booch, Hohpe, Richards/Ford)
6. Even the ISO standard improved from "organization" (infra-leaning) to "concepts or properties" (meaning-leaning) — acknowledging the drift

**The gradient is clear:** The closer a source is to selling technology, the more likely it defines architecture as infrastructure. The closer a source is to solving business problems with software, the more likely it defines architecture as consequential decisions about domain meaning.

---

## Source URLs

### Mainstream & Standards
- https://en.wikipedia.org/wiki/Software_architecture
- https://en.wikipedia.org/wiki/Data_architecture
- http://www.iso-architecture.org/ieee-1471/defining-architecture.html
- https://en.wikipedia.org/wiki/ISO/IEC_42010

### Vendors & Analysts
- https://pubs.opengroup.org/architecture/togaf9-doc/arch/chap02.html
- https://www.gartner.com/en/information-technology/glossary/architecture
- https://aws.amazon.com/what-is/data-architecture/
- https://learn.microsoft.com/en-us/previous-versions/msp-n-p/ee658098(v=pandp.10)
- https://learn.microsoft.com/en-us/azure/architecture/guide/architecture-styles/
- https://www.ibm.com/think/topics/data-architecture
- https://docs.snowflake.com/en/user-guide/intro-key-concepts
- https://www.informatica.com/resources/articles/modern-data-architecture.html
- https://www.databricks.com/glossary/data-architecture
- https://www.dataversity.net/what-is-data-architecture/

### DDD & Practitioners
- https://martinfowler.com/architecture/
- https://martinfowler.com/ieeeSoftware/whoNeedsArchitect.pdf
- https://www.bredemeyer.com/whatis.htm
- http://fundamentalsofsoftwarearchitecture.com/
- https://c4model.com/
- https://architectelevator.com/architecture/architecture-options/
- https://www.infoq.com/presentations/architect-lessons/
- https://www.infoq.com/news/2013/04/DDD-Architecture-Styles/
- https://www.goodreads.com/work/quotes/173058
- https://www.eferro.net/2017/06/quotes-about-software-architecture.html
