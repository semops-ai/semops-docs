# Business Architecture

> **Business Architecture** defines what a business does, what it knows, how it delivers value, and who does the work.
 
[Explicit Architecture](explicit-architecture) via [Domain-Driven Design](domain-driven-design) encodes those definitions into queryable, connected data structures. Together they produce the complete model of a business: one side names the things, the other side gives them boundaries, lifecycles, and rules.

Most businesses have business architecture. It exists in org charts, strategy decks, process maps, and the heads of people who have been there long enough. The problem is that it is implicit. You cannot query it, connect it to systems, or hand it to an agent. Explicit Architecture solves this by providing the technical scaffolding (bounded contexts, aggregates, state machines, context maps) that makes business architecture inspectable and machine-readable.

Neither discipline is complete alone. Business Architecture without DDD produces strategy decks disconnected from systems. DDD without Business Architecture produces technically clean code that may not reflect what the business actually does. The meeting point is where both disciplines answer the same questions with the same nouns, and those answers are encoded in data structures rather than documents.

---

## Two Disciplines, One Model

### Business Architecture: The Business Side

Business Architecture is formalized primarily through the BIZBOK (Business Architecture Body of Knowledge, maintained by the Business Architecture Guild, current version v14). It is a body of knowledge, not a prescriptive process framework. It defines concepts, domains, artifacts, and principles, then leaves sequencing and prioritization to the practitioner.

BIZBOK defines four **core domains** that form the architectural foundation. These are designated "core" because they change infrequently. A company restructures processes constantly, but its capabilities and information concepts are stable over years.

| Domain | What It Answers | Key Artifact |
|--------|----------------|-------------|
| **Capability** | What does the business do? | Capability Map |
| **Value Stream** | How does the business deliver value? | Value Stream Map |
| **Information** | What does the business know and track? | Information Map |
| **Organization** | Who does the business consist of? | Organization Map |

Extended domains (Strategy, Initiative, Policy, Product, Stakeholder, Metrics, Decisions and Events) layer on top of this foundation.

The chain reads top-to-bottom:

```text
Strategy (what we want to achieve)
  --> Capability Map (what we must be able to do)
    --> Value Streams (how we deliver value to stakeholders)
      --> Information Map (what data we create and consume)
        --> Organization Map (who owns what)
```

**The critical insight for practitioners:** the capability map derives from the information map. Business objects (nouns) come first. Once you know what the business tracks and manages, Level 1 capabilities follow directly: one per major business object. "Customer Management" anchors to "Customer." "Agreement Management" anchors to "Agreement." The naming convention (noun + action) makes the capability map directly readable as a data entity inventory.

### Explicit Architecture: The Technical Side

[Domain-Driven Design](domain-driven-design) provides the technical scaffolding. Where Business Architecture names the things, DDD gives them precise boundaries, lifecycle rules, and coordination patterns:

| DDD Concept | What It Provides |
|-------------|-----------------|
| **Bounded Context** | A boundary within which a particular model applies. Same word, different meaning in different contexts. |
| **Aggregate Root** | The primary entity that owns a consistency boundary. All mutations go through it. |
| **Entity** | Something with identity and a lifecycle. |
| **Value Object** | Something defined by its attributes, not identity. Immutable. |
| **Domain Event** | A signal that something happened. Past tense. Triggers downstream processes. |
| **Context Map** | How bounded contexts relate: upstream/downstream, shared kernel, anti-corruption layer. |
| **Saga / Process Manager** | Coordination across bounded context boundaries. |

DDD's strategic patterns (bounded contexts, context maps) answer "where are the boundaries?" DDD's tactical patterns (aggregates, entities, value objects, events) answer "what lives inside each boundary and how does it behave?"

### Where They Meet: The Full Data Model

The two disciplines answer the same structural questions from different starting points and converge on the same nouns:

| Question | Business Architecture Answer | DDD Answer |
|----------|------------------------------|------------|
| What are the fundamental things this business tracks? | Business Objects (Information Map) | Aggregate Roots |
| Where are the ownership boundaries? | Level 1 Capabilities (one per business object) | Bounded Contexts |
| What states can things be in? | Information Concept States | Aggregate state machines |
| What are the variants? | Information Concept Types | Type discriminators / subtypes |
| How do the parts relate? | Capability/Value Stream cross-mapping | Context Map (upstream/downstream) |
| What signals that work completed? | Value Stream Stage exit criteria | Domain Events |
| How does work flow across boundaries? | Value Stream stages | Sagas and choreography |
| Who writes vs. who reads? | Capability "modifies" vs. "uses" | CQRS (command/query separation) |

When both disciplines agree on the same nouns and boundaries, you have a business model that is simultaneously legible to business stakeholders (capability maps, value stream maps) and executable in systems (bounded contexts, aggregates, APIs). This is what "Explicit Architecture" means in SemOps: the business model and the technical model are the same model, expressed in two complementary vocabularies.

---

## The Derivation: From Business Facts to Technical Architecture

The BIZBOK already defines a step-by-step derivation path (SS6.5 SOA, SS6.6 Data Architecture). SemOps reframes that derivation using DDD terminology. The key insight is that **this derivation is mechanical, not creative.** Given a set of business architecture artifacts, the DDD strategic and tactical patterns follow deterministically.

### The 11-Step BIZBOK to DDD Derivation

| Step | Business Architecture Input | DDD Output |
|------|---------------------------|------------|
| 1 | Level 1 Capability (one business object each) | Bounded Context boundary |
| 2 | Business Object (anchor noun) | Aggregate Root |
| 3 | Primary Information Concept | Entity (independent lifecycle) |
| 4 | Secondary Information Concept | Entity or Value Object (dependent) |
| 5 | Information Concept States | Aggregate state machine |
| 6 | Information Concept Types | Type discriminator / subtypes |
| 7 | Information Concept Relationships | Context Map relationships |
| 8 | Capability "modifies" relationship | Write model (command side) |
| 9 | Capability "uses" relationship | Read model (query side) |
| 10 | Value Stream Stage exit criteria | Domain Event |
| 11 | Value Stream to Capability cross-map | Saga / Process Manager |

### Why the Derivation Works

The derivation works because BIZBOK and DDD share a structural axiom: **single ownership of a named thing.** In BIZBOK, each Level 1 capability is anchored to exactly one business object. In DDD, each bounded context owns exactly one aggregate root. The anchor noun rule (BIZBOK) and the single-writer rule (DDD) are the same principle expressed in different vocabularies.

This axiom has been validated across five independent industry reference frameworks:

| Source | How It Confirms the Axiom |
|--------|--------------------------|
| BIZBOK v14 | Each L1 capability anchored to one business object |
| BIAN Service Landscape v9 (banking) | Each service domain has one Control Record |
| HL7 FHIR v5 (healthcare) | Each resource (Patient, Encounter, Organization) is one context's root |
| TM Forum ODA (telecom) | Each component has one primary business object |
| Context Mapper CML (insurance, cargo) | Each bounded context has one aggregate root |

### Relationship Type Derivation Rules

The "modifies" vs. "uses" distinction in BIZBOK maps directly to DDD's upstream/downstream relationships and to CQRS command/query separation:

| BIZBOK Relationship | DDD Context Map Pattern |
|--------------------|------------------------|
| Context A "modifies" Object X | A owns X (upstream for X) |
| Context A "uses" Object Y from Context B | A is downstream of B |
| Primary to Secondary dependency | Same aggregate or Shared Kernel |
| Both contexts "use" same object | Shared Kernel or Published Language |
| One-way "uses" only | Customer-Supplier (used context is upstream) |
| Cross-tier relationship (Core to Supporting) | Conformist (core conforms to supporting's API) |

### Entity vs. Value Object Decision

When deriving DDD tactical patterns from BIZBOK information concepts, apply these tests in order (first match wins):

| Test | Result | Example |
|------|--------|---------|
| Has its own identity that matters beyond its attributes? | Entity | AgreementTerm (not interchangeable) |
| Has its own lifecycle states? | Entity (minimum) | PaymentSchedule (Pending, Active, Completed) |
| Has its own L2+ capability (single-writer)? | Aggregate Root | Payment (Payment Management writes to it) |
| Immutable once created? | Value Object | MonetaryAmount (amount + currency) |
| Interchangeable by value? | Value Object | Currency, Period, Address |
| Classification or label? | Value Object (or enum) | AgreementType, Status |

### Strategic Classification

BIZBOK capability tiers map directly to DDD domain types, which determine investment strategy:

| BIZBOK Tier | DDD Domain Type | Investment Strategy |
|-------------|----------------|---------------------|
| Core / Customer-Facing | Core Domain | Build custom, deep modeling |
| Strategic / Direction-Setting | Generic Subdomain | Configurable, buy or SaaS |
| Supporting | Supporting Subdomain | Minimal viable, buy or outsource |

---

## Process: The Bridge Between Architecture and Execution

Business Architecture and DDD together define **what exists** (entities, boundaries, relationships) and **what state things can be in** (lifecycles, events). But a business also needs to answer: **what work happens, and who does it?**

Process is the universal unit of work. Three frameworks contribute different perspectives:

| Framework | What It Tells You | Granularity |
|-----------|------------------|-------------|
| **APQC PCF** | What processes should exist (taxonomy) | Category, Process Group, Process, Activity, Task |
| **DDD** | Where processes live and what boundaries they respect | Command handlers, domain services, sagas, choreography |
| **BIZBOK** | How processes deliver stakeholder value | Value stream stages, capability cross-mapping |

The derivation chain:

```text
Process (APQC: what work exists)
  --> placed in DDD (which bounded context owns it, what boundaries it crosses)
  --> produces Pattern (reusable, named, registered)
  --> Pattern defines Capabilities (what the process delivers)
  --> Capability is fulfilled by an Executor (human, agent, rule-engine, hybrid)
```

The executor is a deployment decision, not a modeling decision. When a process is automated, the pattern and capabilities do not change. Only the executor does. This separation is what makes the model stable across organizational evolution: capabilities mature from manual to autonomous without restructuring the architecture.

### DDD Process Constructs

DDD distributes processes across several constructs depending on scope:

| DDD Construct | Process Role | Scope |
|---------------|-------------|-------|
| **Command Handler** | Single-step state transition on an aggregate | Within aggregate |
| **Domain Service** | Stateless operation spanning aggregates within one context | Within bounded context |
| **Domain Event** | Signal that a process step completed | Cross-boundary trigger |
| **Saga / Process Manager** | Multi-step, cross-boundary process with compensation | Cross bounded context |
| **Policy** | Reactive rule: "when X happens, do Y" | Cross-boundary, event-driven |

### APQC Levels Map to DDD Constructs

| APQC Level | DDD Equivalent | Example |
|------------|---------------|---------|
| Level 1: Category | Bounded Context or cluster | "Manage Supply Chain" |
| Level 2: Process Group | Subdomain within a context | "Plan supply chain resources" |
| Level 3: Process | Application Service / Saga | "Manage demand for products" |
| Level 4: Activity | Command Handler or Domain Service | "Develop baseline demand forecasts" |
| Level 5: Task | Implementation detail | Individual steps within an activity |

APQC Level 3 processes are the natural unit for DDD sagas. They describe end-to-end workflows that cross aggregate and bounded context boundaries.

### Cognitive Load: What Kind of Thinking a Capability Requires

Each capability has a cognitive load level that describes the complexity of what the process delivers. This is independent of who executes it:

| Level | Definition | Automation Readiness |
|-------|-----------|---------------------|
| **Deterministic** | Fixed rules, lookup tables, no interpretation | Immediate: script or rule engine |
| **Categorical** | Known output categories, defined scoring criteria | High: decision trees, classifiers |
| **Inferential** | Synthesizing signals, recognizing patterns across sources | Medium: needs data pipelines, possibly ML/LLM |
| **Reasoning** | Multi-step analysis with hypothesis-test-revise loops | Lower: observation-action loop with tool access |
| **Judgment** | Values-based tradeoffs where reasonable people disagree | Lowest: agent prepares analysis, human decides |

Cognitive load determines where on the automation gradient a capability sits. Capabilities evolve down the scale as data, models, and organizational consensus accumulate: what starts as judgment becomes reasoning, then inferential, then categorical, then deterministic. The architecture does not change during this evolution. Only the executor does.

---

## The Complete Picture

When Business Architecture and Explicit Architecture via DDD are composed, the resulting model answers every structural question about a business:

```text
Business Strategy (why we exist, what we want to achieve)
  |
  v
Business Architecture (BIZBOK)
  |-- Capability Map: what we do (L1 = bounded contexts, L2-L3 = aggregates)
  |-- Value Streams: how we deliver value (stages = domain events, cross-map = sagas)
  |-- Information Map: what we know (business objects = aggregate roots, states, types)
  |-- Organization Map: who does it (ownership = context teams)
  |
  v
Explicit Architecture (DDD)
  |-- Bounded Contexts: ownership boundaries with precise models
  |-- Aggregates: consistency boundaries with invariants
  |-- Context Map: how boundaries coordinate (upstream/downstream, shared kernel, ACL)
  |-- Domain Events: what happened (process telemetry, saga triggers)
  |-- State Machines: lifecycle rules (from information concept states)
  |
  v
Process Model (APQC + DDD + BIZBOK)
  |-- What work exists (APQC taxonomy)
  |-- Where it lives (DDD bounded context placement)
  |-- How it flows (value stream stages, sagas, choreography)
  |-- What it delivers (capabilities with cognitive load levels)
  |-- Who does it (executor: human, agent, rule-engine, hybrid)
  |
  v
Implementation
  |-- Data Architecture: information map --> logical data entities --> physical models
  |-- Application Architecture: capabilities --> services and APIs
  |-- Agent Architecture: capabilities with advancing cognitive load --> agent prescriptions
```

This chain is not aspirational. Each arrow represents a mechanical derivation step. Given a BIZBOK reference model for an industry, the DDD strategic patterns, tactical patterns, process model, and data architecture follow deterministically. For any of the eight industries with BIZBOK reference models (Financial Services, Insurance, Healthcare, Government, Manufacturing, Telecommunications, Transportation, Mission-Driven), a starter architecture can be generated before a single interview is conducted.

The test remains the same: **Can an agent (human or AI) determine what rules apply in a given context by inspecting the architecture?** When Business Architecture and DDD are composed into a single model, the answer is yes. The business objects, their boundaries, their lifecycles, their relationships, their coordination patterns, and the processes that act on them are all encoded in queryable data structures rather than documents, tribal knowledge, or implicit convention.

---

## Industry Reference Models

BIZBOK provides pre-built business architecture templates for eight industry sectors. Each includes capability maps, value streams, and information maps representing what a "typical" organization in that industry looks like.

| Industry | Components |
|----------|-----------|
| Financial Services | Capability maps, value streams, information maps |
| Insurance | Capability maps, value streams, information maps |
| Healthcare Provider | Capability maps, value streams, information maps |
| Government | Capability maps, value streams, information maps |
| Manufacturing | Capability maps, value streams, information maps |
| Telecommunications | Capability maps, value streams, information maps |
| Transportation | Capability maps, value streams, information maps |
| Mission-Driven Organizations | Capability maps, value streams, information maps |

A Common Reference Model (industry-agnostic) serves as the foundation, covering capabilities like Finance Management and Human Resource Management that apply across all industries.

For industries without a BIZBOK reference model, SemOps falls back to the Common Reference Model plus APQC PCF process taxonomy (processes imply data entities) plus industry classification signals (NAICS, NIST).

See [BIZBOK to DDD Overlay](bizbok-ddd-overlay) for the fully worked Financial Services derivation example.

---

## Relationship to Other Frameworks

| Framework | How It Connects |
|-----------|----------------|
| **TOGAF** | BIZBOK is the content methodology for TOGAF Phase B (Business Architecture). TOGAF provides the architecture development method; BIZBOK provides the substance of what business architecture artifacts look like. |
| **APQC PCF** | APQC provides process taxonomy (how work is done); BIZBOK provides capability and value stream models (what can be done and how value flows). APQC Level 4-5 processes realize BIZBOK capabilities. |
| **DAMA-DMBOK** | DAMA defines data management knowledge areas. BIZBOK's information map feeds DAMA's Data Architecture and Data Modeling. Information concepts become logical data models become physical data models. |
| **ArchiMate** | ArchiMate provides formal notation across the full stack. BIZBOK maps to ArchiMate's Business Layer. Organization becomes Business Actor, Value Stream stays Value Stream, Capability becomes Business Function/Service. |
| **Operating Model Canvas** | OMC is the consulting engagement tool (scoped, 12-week engagement). BIZBOK is the enterprise architecture tool (persistent, comprehensive). OMC's POLISM elements map to BIZBOK domains: Processes = Value Streams, Information = Information Map + Application Mapping, Organization = Organization Map, Management System = Strategy + Metrics. |
