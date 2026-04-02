---
doc_type: hub

pattern: domain-driven-design

provenance: 3p

metadata:
    pattern_type: concept
    brand_strength: low
---

# Domain-Driven Design

> Domain-Driven Design ([Eric Evans](https://www.domainlanguage.com/), 2003) is a software design philosophy that says: software models should be deeply tied to the business domain they serve. SemOps adopts DDD as its primary architectural framework because it provides the scaffolding for encoding business knowledge at every level — from entity schemas through business processes to strategic governance.

---

## What DDD Is

Domain-Driven Design is an approach to building software that starts with the business, not the technology. Instead of choosing a database and fitting the business into it, DDD says: understand the domain first, model it precisely, and let the technology follow.

The core insight is that **software complexity comes from domain complexity, not technical complexity.** If a system is hard to change, it is usually because the domain model is wrong or unclear — not because the infrastructure is inadequate.

DDD provides:

- **A shared language** between business experts and engineers (Ubiquitous Language)
- **Clear boundaries** around different parts of the business (Bounded Contexts)
- **Precise data models** that reflect business reality (Entities, Aggregates, Value Objects)
- **Coordination patterns** for how different parts of the business work together (Context Maps, Sagas)
- **Measurement structures** that connect metrics to the domain model (Analytics Patterns)

### DDD as Foundation, Not Exclusive Choice

Choosing DDD does not mean choosing only one architecture. DDD is the general-purpose foundation; other approaches layer on top for specific needs:

- **Data Mesh** requires bounded contexts before it can distribute data ownership
- **Event-Driven Architecture** requires domain events, which emerge from aggregate boundaries
- **PIM (Product Information Management)** operates *within* a bounded context, not instead of DDD

Every specialized architecture presupposes that the foundational question — "what is the business domain?" — has been answered. DDD answers it.

---

## Core Concepts

### Ubiquitous Language

The foundation of DDD is a **shared vocabulary** that domain experts and developers use consistently. This is not just documentation — it is the actual terms used in code, schemas, APIs, and conversations.

**Why it matters:** Without shared language, the same concept gets different names across teams ("customer" vs "user" vs "account holder"), causing semantic drift that compounds over time. AI systems inherit this confusion — if the data uses inconsistent terminology, models cannot learn coherent patterns.

**How it works:** Define terms in a glossary that becomes the source of truth. Use those exact terms in database columns, API endpoints, class names, and documentation. When domain experts and engineers talk, they use the same words for the same things.

Traditional DDD found this difficult because humans actively disagree on language — or disagree by default by using different terms for the same concept. An AI-first approach must be dogmatic about domain precision: every business rule is explicit, every workflow is bounded and named, every metric has a precise definition.

### Bounded Contexts

A **bounded context** is a boundary within which a particular model applies. The same word can mean different things in different contexts — and that is intentional.

**Example:** In an e-commerce company, "Order" means something different in Fulfillment (a shipment to pack) than in Finance (a revenue recognition event) than in Customer Service (a complaint resolution target). Each context owns its own definition.

**Why it matters:** Trying to create one universal model for everything leads to bloated, compromised abstractions that serve no one well. Bounded contexts let each subdomain optimize for its own needs while defining clear contracts for cross-context communication.

**A company bolted on is a bounded context.** When a company acquires another company, that company's systems, language, and models do not magically merge. The acquired company operates as a bounded context with its own internal model. Integration happens through explicit translation layers — anti-corruption layers in DDD terms — not by forcing everything into one model. This is true whether the "company" is an acquisition, a new business unit, or a major vendor integration.

### Entities, Aggregates, and Value Objects

**Entities** are objects with identity that persists over time — a Customer, an Order, a Product. They are defined by *who they are*, not by their attributes.

**Value Objects** are objects defined entirely by their attributes — an Address, a Price, a DateRange. Two value objects with the same attributes are identical. They carry a package of related data together:

```python
# Instead of separate variables:
uri = "s3://bucket/file.pdf"
format = "pdf"
hash = "sha256:abc123..."

# A Value Object bundles them:
filespec = FileSpec(
    uri="s3://bucket/file.pdf",
    format="pdf",
    hash="sha256:abc123..."
)
```

**Aggregates** are clusters of entities and value objects treated as a single unit for data changes. One entity serves as the **Aggregate Root** — the gatekeeper for all modifications.

**Why it matters:** Aggregates define transactional boundaries and invariant enforcement. All business rules that must be true atomically live inside the aggregate boundary. Outside code references only the root's ID — never child IDs directly. This prevents data corruption and simplifies reasoning about system behavior.

**How it works:** Design aggregates around business invariants, not database tables. Keep them small. Cross-aggregate communication happens through domain events (eventual consistency) rather than distributed transactions.

### Context Maps and Integration Patterns

As systems grow, the relationships between bounded contexts become the primary architectural concern. **Context maps** document these relationships explicitly:

- **Anti-Corruption Layer (ACL)** — translates between incompatible models, protecting each context's integrity
- **Shared Kernel** — common model used by multiple contexts (use sparingly — it creates coupling)
- **Customer-Supplier** — one context serves another; the supplier accommodates the customer's needs
- **Published Language** — a well-documented, shared language for integration (e.g., industry standards)
- **Conformist** — one context adopts another's model as-is (pragmatic when translation cost exceeds benefit)

**Example:** In an e-commerce company, the Product Catalog context publishes product data via a Published Language. The Storefront context consumes it as a Conformist (no need to translate — it uses the same product model). The Finance context uses an ACL to translate product data into revenue recognition entities with completely different semantics.

---

## Beyond Traditional DDD

Traditional DDD focuses on tactical patterns (entities, aggregates) and strategic patterns (bounded contexts, context maps). SemOps extends DDD to cover three pattern types that together encode a complete business domain.

### Domain Patterns

The classical DDD building blocks, applied to the structural model of the business. These define *what exists* and *how it relates*:

- Entities and aggregates model the core business objects
- Value objects capture the attributes and measurements
- Bounded contexts establish semantic boundaries
- Context maps and integration patterns govern cross-boundary communication

### Process Patterns

Operational workflows encoded as domain-aware coordination. These define *how work gets done*:

**Sagas** coordinate multi-step business processes that span aggregate boundaries. In an e-commerce company, order fulfillment is a saga: it coordinates inventory reservation, payment capture, picking and packing, and shipping — each owned by a different aggregate, each with its own failure and compensation logic.

**Choreographies** are event-driven coordination where each bounded context reacts to domain events independently. No central orchestrator — each context knows what to do when it sees a relevant event. In the same e-commerce example, when an order is placed: Inventory reacts by reserving stock, Finance reacts by authorizing payment, Fulfillment reacts by queuing the pick. Each context acts independently; the overall process emerges from their individual reactions.

**Goals** are business objectives encoded as measurable process outcomes. A goal like "reduce order-to-delivery time by 20%" connects directly to the saga steps it depends on, making it traceable to specific process bottlenecks.

### Analytics Patterns

Measurement and metrics encoded as first-class domain concepts. These define *how performance is measured*:

- **Metrics as domain entities** — KPIs, SLAs, and business measures defined with the same rigor as operational entities. "Customer Lifetime Value" is not just a report; it is a domain concept with a precise definition, calculation method, and owning context.
- **Dimensional models derived from aggregates** — analytics schemas are not invented independently. They are derived from the domain model, ensuring that what gets measured reflects what actually exists.
- **Coherence scoring** — measuring how well implementation matches the domain model. If the analytics show metrics that do not trace to domain entities, something is being measured that the business does not formally understand.

These three pattern types work together: domain patterns define the structure, process patterns operate within it, and analytics patterns measure it. An e-commerce company's "order-to-cash" is simultaneously a domain pattern (the entities involved), a process pattern (the saga that coordinates them), and an analytics pattern (the metrics that measure it — gross margin, fulfillment time, return rate).

---

## DDD and AI

DDD and AI have a symbiotic relationship. Traditional DDD was hard to implement because it required sustained discipline that most organizations could not maintain. AI changes the economics:

**What DDD gives AI:**

- Precise schemas and boundaries make data interpretable for downstream learning systems
- Ubiquitous language eliminates the translation loss between training data and business intent
- Aggregate boundaries define exactly where an agent's authority starts and stops
- Domain events provide clear triggers for agent activation

**What AI gives DDD:**

- LLMs are excellent at structured domains (accounting, legal, logistics) once the ubiquitous language is defined
- AI agents can enforce domain rules that humans forget or skip
- Continuous schema validation that would be impractical for human-only teams
- Pattern detection across contexts that reveals implicit architecture

**The compound effect:** Each domain refinement makes both human and AI productivity increase. A better domain model produces better agent behavior, which produces better domain feedback, which refines the model further.

### AI Agent Scaling Path

The levels are not about operational maturity. They are about **domain legibility**:

| Level | What it represents |
| ----- | ----------------- |
| 0. Chat Bot | "I'm exploring what this even is" |
| 1. Slash commands | "I can name and use a group of operations" |
| 2. Manifest | "I can externalize the rules" |
| 3. Event triggers | "I know the boundaries and entry points" |
| 4. Contracts / schemas | "I can specify inputs and outputs precisely" |
| 5. Clean scaling | "The domain model is stable enough that infra is just plumbing" |

The **reverse test**: go backward from 5 to 0 — does the logic still work as a prompt? If the transition from 4 to 5 is not clean, the missing element is domain knowledge, not infrastructure knowledge.

---

## Architecture Is Not Infrastructure

DDD's core claim is that software complexity comes from domain complexity, not technical complexity. If the organization is fighting its infrastructure, it is usually because the domain model is wrong or unclear.

**Infrastructure-first thinking:**

- "We need to scale. What infrastructure do we need?"
- "Can I deploy this?"

**DDD thinking:**

- "Understand the domain and encode it in the architecture. Infrastructure choices will be constrained and probably obvious."
- "Do I understand this well enough that deployment will be boring?"

Business logic is the artifact. Infrastructure is a deployment detail. If scaling is hard, the abstractions are wrong.

---

## How DDD Maps to the Semantic Funnel

DDD spans all three transitions of the [Semantic Funnel](../../RESEARCH/FOUNDATIONS/semantic-funnel.md) — it provides the scaffolding for encoding rules at every DIKW level.

```text
  Objects:  DATA ══════> INFORMATION ══════> KNOWLEDGE ══════> WISDOM
               │                │                  │
  Rules:  Structural       Interpretive        Normative
          (entity schemas)  (specifications)    (policies)
               │                │                  │
  Agents:     —           Domain Services    Leadership,
                                              architects
```

| Element | D → I | I → K | K → W |
|---------|-------|-------|-------|
| **Objects** | Entities, Value Objects, Aggregates | Domain Events, Specifications | Policies, Context Maps, ACLs |
| **Rules** | Structural — entity schemas, aggregate boundaries, value object constraints | Interpretive — specifications, invariants, domain service logic | Normative — policies, anti-corruption layers, bounded context strategy |
| **Determinism** | High — aggregate boundaries and entity schemas enforce structure mechanically | Medium — pattern detection requires inference; specifications encode but don't eliminate judgment | Low — context boundaries and policies are judgment calls; values-dependent |
| **Failure mode** | Unbounded aggregates, missing invariants, anemic domain models | Tribal knowledge, implicit business rules, specs that don't match reality | Semantic drift across bounded contexts, conflicting policies, Conway's Law violations |

---

## Related Links

### Framework Components

- [Explicit Architecture](README.md) — how DDD becomes a fully encoded company structure
- [What Architecture Actually Is](what-is-architecture.md) — architecture vs. infrastructure, rule types, and design approaches
- [Patterns and Bounded Contexts](patterns-and-bounded-contexts.md) — growing through patterns
- [Anti-Corruption Layer (ACL)](ddd-acl-governance-aas.md) — semantic firewalls at boundaries
- [DDD Solves AI Transformation](ddd-solves-ai-transformation.md) — the argument for DDD as AI enabler
- [SemOps Aggregate Root](semops-aggregate-root.md) — aggregate root pattern applied to SemOps
- [BizBok-DDD Derivation](bizbok-ddd-overlay.md) — aligning business architecture with DDD through mechanical derivation

### Citations

- Evans, Eric. *Domain-Driven Design*. Addison-Wesley, 2003.
- Vernon, Vaughn. *Implementing Domain-Driven Design*. Addison-Wesley, 2013.
