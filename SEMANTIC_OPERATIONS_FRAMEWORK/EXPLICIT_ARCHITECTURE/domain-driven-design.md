---
doc_type: hub

pattern: domain-driven-design

provenance: 3p

metadata:
 pattern_type: concept
 brand_strength: low

# this doc requires rewrite - please look to the inline comments
---

# Domain-Driven Design

> Domain-Driven Design ([Eric Evans](https://www.domainlanguage.com/), 2003) is a software design philosophy that says: "Software models should be deeply tied to the business domain they serve."

**Key Characteristics**:
- Emphasizes deep understanding of the core business domain and translating that understanding into software models. 
- Involves identifying the **ubiquitous language** (terms and concepts used by domain experts) and the **key entities** (value objects, and aggregates) within the domain. 
- Domain modeling identifies and technically defines the fundamental business components and their relationships to accurately represent the problem space **in code and data**. 
- As AI is increasingly integrated into software, aligning AI development with a well-understood and modeled domain (as per DDD) becomes more important.

## How DDD Maps to the Semantic Funnel

DDD spans all three transitions of the [Semantic Funnel](../../RESEARCH/FOUNDATIONS/semantic-funnel.md) — it provides the scaffolding for encoding rules at every DIKW level.

```
 Objects: DATA ══════> INFORMATION ══════> KNOWLEDGE ══════> WISDOM
 │ │ │
 Rules: Structural Interpretive Normative
 (entity schemas) (specifications) (policies)
 │ │ │
 Agents: — Domain Services Leadership,
 architects
```

| Element | D → I | I → K | K → W |
|---------|-------|-------|-------|
| **Objects** | Entities, Value Objects, Aggregates | Domain Events, Specifications | Policies, Context Maps, ACLs |
| **Agents** | — (schema enforcement is mechanical) | Domain Services | Leadership, architects |
| **Rules** | Structural — entity schemas, aggregate boundaries, value object constraints | Interpretive — specifications, invariants, domain service logic | Normative — policies, anti-corruption layers, bounded context strategy |
| **Determinism** | High — aggregate boundaries and entity schemas enforce structure mechanically | Medium — pattern detection requires inference; specifications encode but don't eliminate judgment | Low — context boundaries and policies are judgment calls; values-dependent |
| **Mechanism** | Impose structure via aggregate boundaries and entity schemas | Encode business patterns as inspectable architecture; Ubiquitous Language reduces semantic drift | Force alignment between software models and business domain reality |
| **Failure mode** | Unbounded aggregates, missing invariants, anemic domain models | Tribal knowledge, implicit business rules, specs that don't match reality | Semantic drift across bounded contexts, conflicting policies, Conway's Law violations |

**Note:** Domain Events and Repositories are **Objects**, not Agents — they carry or store data but don't decide. Only Domain Services (where business logic executes decisions) are Agents. Infrastructure that executes Rules deterministically is an Object with Rules encoded in it.

### Why DDD Works for Semantic Operations

- **Full-funnel coverage**: DDD is one of the few patterns that encodes rules at every DIKW level — structural, interpretive, and normative
- **Rules become inspectable**: When understanding is encoded in DDD structures, any Agent (human or AI) can determine what rules apply by examining the architecture
- **DDD is organizational as much as technical**: It enforces collaboration between domain experts and engineers (knowledge governance)
- **Meta-rules at Wisdom level**: Ubiquitous Language and Bounded Context strategy are meta-rules — rules that create, validate, or constrain other rules

See [What is Understanding?](../../RESEARCH/FOUNDATIONS/what-is-understanding.md) for how rules reduce decision uncertainty.

### DDD as Foundation, Not Exclusive Choice

**Choosing DDD does not mean choosing only one architecture.** DDD is the general-purpose foundation; other approaches layer on top for specific needs:

- **Data Mesh** requires bounded contexts before it can distribute data ownership
- **Event-Driven Architecture** requires domain events, which emerge from aggregate boundaries
- **PIM (Product Information Management)** operates *within* a bounded context, not instead of DDD

**Example:** Project SemOps uses DDD as the core, but the "Branded Product" domain uses PIM patterns for branded surfaces, delivery channels, and published product entities. The PIM pattern specializes one "bounded context"—it does not replace the overall architecture.

See [Explicit Architecture: Why DDD is the Foundation](what-is-architecture.md#why-ddd-is-the-foundation) for when to add specialized approaches like Data Mesh, Event-Driven, or PIM.

## Key Concepts

Domain-Driven Design organizes software around the business domain rather than technical layers. The core components work together to ensure that code reflects business reality:

### Ubiquitous Language

The foundation of DDD is a **shared vocabulary** that domain experts and developers use consistently. This is not just documentation—it is the actual terms used in code, schemas, APIs, and conversations.

**Why it matters:** Without shared language, the same concept gets different names across teams ("customer" vs "user" vs "account holder"), causing [semantic drift](../../SEMANTIC_OPTIMIZATION/semantic-drift.md) that compounds over time. AI systems inherit this confusion—if the data uses inconsistent terminology, models cannot learn coherent patterns.

**How it is used:** Define terms in a glossary that becomes the source of truth. Use those exact terms in database columns, API endpoints, class names, and documentation. When domain experts and engineers talk, they should be using the same words for the same things.

### Bounded Contexts

A **bounded context** is a boundary within which a particular model applies. The same word can mean different things in different contexts—and that is intentional.

**Example:** "Order" means something different in Fulfillment (a shipment to pack) than in Finance (a revenue recognition event) than in Customer Service (a complaint resolution target). Each context owns its own definition.

**Why it matters:** Trying to create one universal model for everything leads to bloated, compromised abstractions that serve no one well. Bounded contexts let each subdomain optimize for its own needs while defining clear contracts for cross-context communication.

**How it is used:** Identify natural boundaries in the business. Each context gets its own model, its own ubiquitous language, and its own team ownership. Contexts communicate through well-defined interfaces (APIs, events, anti-corruption layers).

### Aggregates & Entities

**Entities** are objects with identity that persists over time (a Customer, an Order, a Product). **Aggregates** are clusters of entities treated as a single unit for data changes, with one entity serving as the **Aggregate Root**—the gatekeeper for all modifications.

**Why it matters:** Aggregates define transactional boundaries and invariant enforcement. All business rules that must be true atomically live inside the aggregate boundary. This prevents data corruption and simplifies reasoning about system behavior.

**How it is used:** Design aggregates around business invariants, not database tables. Keep them small—large aggregates cause performance problems. Cross-aggregate communication happens through domain events (eventual consistency) rather than distributed transactions.

### Patterns and DDD

Patterns help organize how bounded contexts relate to each other at the system level:

- **Context Maps** document relationships between contexts
- **Anti-Corruption Layers** translate between incompatible models
- **Shared Kernels** define common models used by multiple contexts
- **Customer/Supplier** relationships clarify upstream/downstream dependencies

**Why it matters:** As systems grow, the relationships between contexts become the primary architectural concern. Strategic design prevents big-ball-of-mud entropy by making context boundaries and relationships explicit.

---

## Business Focus

> Business goals can reduce to: grow the inputs (volume and velocity—grow revenue, company) and improve the inputs (quality, reduce uncertainty) of decisions and actions. Close examination of any enterprise will reveal that while oversimplified, it is not untrue. [Explicit Architecture](README.md) contributes to both:

**Growth (volume and velocity):**

- Intentional architecture makes change predictable—the organization knows what is affected before starting
- Domain-aligned teams have "Ubiquitous Language" and guardrails on misalignment and disagreement

- AI agents become plug-and-play specialists when domains are well-defined

**Quality (reduce uncertainty):**

- Every business rule is explicit; every metric has a precise, canonical definition
- Deviations from standard patterns require justification, not arbitrary decisions
- Integration becomes configuration, not reconstruction

---

## Architecture is not Infrastructure

> DDD's core claim is that software complexity comes from domain complexity, not technical complexity. If the organization is fighting its infrastructure, it is usually because the domain model is wrong or unclear. The code is revealing something about the understanding.

**Infra First Decision Making:**

Many engineering decisions are made with infrastructure first in mind. This mindset biases toward generic implementation details or stack choices that obscure problem-to-solution fit. Both impose boundaries that may not align with domain boundaries, making everything harder.

**Conventional framing:**

- “We need to scale. What infrastructure do we need? How do we make our code fit that infrastructure?”
- “Can I deploy this?”

**DDD framing:**

- “Understand the domain and encode it in the architecture. When we do, infrastructure choices will be constrained and probably obvious."
- “Do I understand this well enough that deployment will be boring?”

Principles:

1. Business logic is the artifact. Infrastructure is a deployment detail.
2. If scaling is hard, the abstractions are wrong.

See: [AI Agent Scaling Path example](#ai-agent-scaling-path)

### Definitions

`entity`
An Entity is a container that can hold:
Identity - The id that makes it unique
Primitives - Strings, numbers, booleans
Enums - Constrained sets of allowed values
Value Objects - Complex, composite, immutable objects
Timestamps - Special primitives for tracking changes
Collections - Lists, sets (of primitives or Value Objects)
References - Foreign keys to other Entities (like source_id in the Edge entity)
attribute
aggregate
aggregate root

### Aggregate Root

- Aggregate = a cluster of domain objects treated as one unit for data changes.
- Aggregate Root (AR) = the gatekeeper of that cluster:
 - Only the root can be loaded/saved via a repository.
 - Outside code references only the root’s ID (never child IDs).
 - All invariants that must be true atomically live inside the aggregate boundary.
 - Think: one transaction, one invariant bubble, one repository.
- Why it matters:
 - Safety: critical rules (e.g., "exactly one default plan") cannot be violated because changes go through the root.
 - Performance: small, bounded objects → fewer lock/contention problems.
 - Integration: cross-aggregate work is event-driven (eventual consistency) instead of giant distributed transactions.

### Value Objects
`value object`

A Value Object is like a single variable that carries a whole package of related data together.
Example with filespec:
# Instead of having separate variables:
uri = "s3://bucket/file.pdf"
format = "pdf"
hash = "sha256:abc123..."
size_bytes = 104857600
mime_type = "application/pdf"

# You have ONE variable that contains ALL of it:
filespec = FileSpec(
 uri="s3://bucket/file.pdf",
 format="pdf",
 hash="sha256:abc123...",
 size_bytes=104857600,
 mime_type="application/pdf"
)

edge entity
edge predicate

---

## Organizational Solution
<!-- Semantic Operations is about a complete organizational solution and DDD looks at this - reference docs/Publicv1_Candidates/SEMANTIC_OPERATIONS_FRAMEWORK/STRATEGIC_DATA/data-is-organizational-challenge.md, docs/Publicv1_Candidates/SEMANTIC_OPERATIONS_FRAMEWORK/EXPLICIT_ARCHITECTURE/data-truth-discovery.md -->

Cultural Challenges

Traditional DDD is very hard to implement because humans actively disagree on language, or they disagree by default by using different language for the same concept or entity. An AI First Company must be dogmatic about domain precision:

- Every business rule is explicit
- Every workflow is bounded and named
- Every metric and proprietary concept has precise definitions
- Existing common patterns are adopted unless there is a really good reason not to
- AI agents become plug-and-play specialists

---

### DDD and AI

<!-- describe how solves for ai-ready-architecture.md at a broader scope -->

---

## DDD and Semantic Operations
<!-- need a more structured explanation as to why DDD is our choice. We start with a DIKW model to show how we have to create a coherent smeantic state and then optimize through pattern application and innovation -- DDD allows us to do this by providing a scafolding for all of the data structures and corpuses needed for Ai and agentic use and for defining and using patterns which are much more business and domain aware than granular "features" or single concepts -->

[Semantic Operations](../README.md) uses DDD as its architectural foundation. DDD provides a systematic method to increase the probability that knowledge infrastructure is organized around the [domain patterns](../../SEMANTIC_OPTIMIZATION/patterns.md) and rules of the core business domain.

Systems, Org, Product all the same
<!-- need to fill this in -->

DDD is essentially a bridge layer: it ensures that the way information is structured in software reflects real-world domain knowledge. Conversely, it ensures that the way the organization operates does not conflict with the domain knowledge as designed. If something is missing, the system is fixed rather than running a "parallel" operating model outside of the systems.

- DDD provides "Domain Structuring" or "Knowledge Encoding" to knowledge operations. It ensures that organizational information and knowledge is not structured arbitrarily, but encoded in a way that directly mirrors business knowledge.

DDD is organizational as much as technical — it enforces collaboration between domain experts and engineers (knowledge governance).

DDD reduces cognitive load by ensuring consistency in how information is represented and communicated.
- Data First DDD does that, but also optimizes information for data and AI. 
- DDD provides the schema and boundaries that make data interpretable for downstream learning systems.

This makes [DIKW](../../RESEARCH/FOUNDATIONS/dikw-theory-comparison.md) more actionable in software engineering and AI contexts: data pipelines produce information → DDD schemas encode it into domain knowledge → which feeds into both human decision-making and AI reasoning.


### Highlights of why its applicable

- LLMs as Domain Experts: They're actually GREAT at structured domains like accounting, legal, logistics once the ubiquitous language is defined
- No Translation Loss: Everyone (human + AI) speaks the same precise language = zero waste
- Radical Interchangeability: If domain boundaries are crystal clear, anyone can step into any bounded context
- Compound Leverage: Each domain refinement makes BOTH human and AI productivity increase

### Semantic Governance
- "Words Matter": It often seems trivial and even nitpicky or passive aggressive when other people in the org attempt to get others to call something the correct label/name, because after all - "you know what i mean", but this can actually cause big problems.


For semantic operations, clarity matters because it enables thinking clearly and removing extra complexity. The focus is on neutral concepts, fundamental capabilities, and understanding trade-offs.
"Understanding" is achieved when anything can be described in terms of what it is and what it does in neutral, precise, and the simplest language possible.

### 3.1 Bounded Contexts as Semantic Containers
Each concept must be understood **within its specific bounded context**. 
This avoids:
- term collisions 
- inconsistent meaning 
- [semantic drift](../../SEMANTIC_OPTIMIZATION/semantic-drift.md)

[SemOps](../../README.md) uses these boundaries for governance.

---

### Ubiquitous Language as Semantic Governance
All teams use the same terminology for domain concepts. 
This becomes the **semantic catalog** used by:
- schemas 
- metrics 
- AI prompts 
- contracts 

Without Ubiquitous Language, neither humans nor AI can maintain consistency.

---

### Aggregates and Entities as Ontology Anchors
In DDD:
- entities and aggregates define identity, invariants, and rules 
These directly correspond to:
- ontology nodes 
- schema objects 
- event definitions 
- metric entities 

DDD provides the domain model; [Semantic Operations](../README.md) operationalizes it.

---

### Context Maps as Semantic Contracts
Context maps define:
- translation rules 
- relationships between domain areas 
- anti-corruption layers 

Essential because AI and analytics must respect these boundaries.


### Value Objects: Flexible, Semantically sound

example from ike

### Predicate Edge

This is relational - cognitive compression

### DDD Product Alignment

Product = Domain Model Made Manifest

- The customer experience IS the domain boundaries externalized
- The pricing model IS the value object definitions
- The user interface IS the aggregate relationships
- There is nowhere to hide sloppy thinking

### DDD + Patterns

Patterns and Domain Templates

- Why should every company reinvent "customer management" domain models?
- Why should everyone rebuild "financial accounting" bounded contexts?
- 90% of business domains are solved problems - make them commons!

Core Repository:

- Domain Templates - pre-built bounded contexts (CRM, Accounting, HR, etc.)
- Schema Patterns - proven data models for common domains
- AI Agent Configurations - trained on standard domain languages
- Integration Patterns - how to plug enterprise tools into YOUR domain model

## The Network Effect

- Every company contributes back domain refinements
- Domain models get better through collective intelligence
- AI agents get smarter on standard business patterns
- Innovation accelerates because everyone starts from a higher baseline

---

### DDD and AI

- When considering LLMs or analytics, DDD provides the schema and boundaries that make data interpretable for downstream learning systems.


- In the age of AI, a data-centric approach will become increasingly critical for AI-driven success, because AI models are heavily reliant on the availability, quality, quantity, and structure of data for context, training, analysis, and agentic systems. As AI is increasingly integrated into software, aligning AI development with a well-understood and modeled domain (as per DDD) becomes more important. DDD enforces starting with the data model to ensure that the necessary data is identified, understood, and made available for AI applications, as necessary.
- Inputs flow to Outputs: The data flows all touch and not just theoretically. Goals, KPIs, roadmaps, sprint planning are all treated as hypotheses being continually tested and decisions
- "Data First Framing" is an analysis approach that looks only at the data that exists and flows between systems as a way of understanding any complex system, organization, or problem space. Related to "Reference Architecture". A company is its data, and a reference architecture may become a universal artifact that companies use internally to understand their own operation and externally to understand competitors, their industry, vendors, acquisition targets, etc.
 - Case-study: An analyst can examine the data schema of any company in any industry and determine important detail about the company without extensive prior knowledge - similar to what an investor does when investigating a company before an offer or during due diligence - they focus on high level financial metrics and understanding the industry and position through that framing because this is the universal language of M&A and investing - this can be extended to all of the enterprise and even public data (reviews, sentiment, etc.) about the company to understand at a deep level
 - Thought experiment: A galactic analyst is assigned to help an alien civilization fix their production of a product they do not understand - in fact, the analyst also does not understand the culture or anything. The plumbus - fleeb. The analyst has a real-time translator device that does a great job of managing conversation and basic communication, but when it comes to specific technical components and proprietary company terminology, not so much. So, the analyst starts interviewing people and is constantly having to look up terms and formulate a dictionary of terms.
- The ideal state is that everyone who should have access to any data (access control) has the

## Examples

<!-- we should pull from examples files in this repo, but also look to our own GLOBAL_ARCHITECTURE in semops-dx-orchestrator and semops-core -->

### AI Agent Scaling Path

The following is a common example of how an "AI Agent" scales through the stages of exploration and development starting with simple agentic coding with Claude Code:

The levels aren’t about operational maturity. They’re about **domain legibility**:

|Level |What it actually represents |
|----------------------|---------------------------------------------------------------|
|0. Chat Bot |“I’m exploring what this even is” |
|1. Slash commands |“I can name and use a group of operations” |
|2. Manifest |“I can externalize the rules” |
|3. Event triggers |“I know the boundaries and entry points” |
|4. Contracts / schemas|“I can specify inputs and outputs precisely” |
|5. Clean scaling |“The domain model is stable enough that infra is just plumbing”|

The **reverse test**: If I go backwards from 5 down to 0, does the logic still work as a prompt?

If the transition from 4 to 5 is not clean, the missing element is domain knowledge, not infrastructure knowledge. Return to levels 0–3.

---

## Related Links

### Framework Components

- [Explicit Architecture](README.md) - Parent framework
- [Explicit Architecture](what-is-architecture.md) - What architecture actually is
- [Patterns and Bounded Contexts](patterns-and-bounded-contexts.md) - Growing through patterns
- [Anti-Corruption Layer (ACL)](ddd-acl-governance-aas.md) - Semantic firewalls at boundaries
- [SemOps Aggregate Root](semops-aggregate-root.md) - Aggregate root pattern applied to SemOps

### Citations

- Evans, Eric. *Domain-Driven Design*. Addison-Wesley, 2003.
- Vernon, Vaughn. *Implementing Domain-Driven Design*. Addison-Wesley, 2013.
