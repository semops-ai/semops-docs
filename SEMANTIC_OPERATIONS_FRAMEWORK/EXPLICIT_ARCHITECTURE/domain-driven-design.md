# Domain-Driven Design

> Domain-Driven Design (DDD) is a software design philosophy that says: software models should be deeply tied to the business domain they serve. SemOps adopts DDD as its primary architectural framework because it provides the scaffolding for encoding business knowledge at every level — from entity schemas through business processes to strategic governance.

---

## Origin

([Eric Evans](https://www.domainlanguage.com/), 2003)

## What DDD Is

Domain-Driven Design is an approach to building software that starts with the business, not the technology. Instead of choosing a database and fitting the business into it, DDD says: understand the domain first, model it precisely, and let the technology follow.

The core insight is that **software complexity comes from domain complexity, not technical complexity.** If a system is hard to change, it is usually because the domain model is wrong or unclear — not because the infrastructure is inadequate.

DDD provides:

- **A shared language** between business experts and engineers (Ubiquitous Language)
- **Clear boundaries** around different parts of the business (Bounded Contexts)
- **Precise data models** that reflect business reality (Entities, Aggregates, Value Objects)
- **Coordination patterns** for how different parts of the business work together (Context Maps, Sagas)
- **Measurement structures** that connect metrics to the domain model (Analytics Patterns)

---



## Core Concepts

### Ubiquitous Language

The foundation of DDD is a **shared vocabulary** that domain experts and developers use consistently. This is not just documentation — it is the actual terms used in code, schemas, APIs, and conversations.

Without shared language, the same concept gets different names across teams ("customer" vs "user" vs "account holder"), causing semantic drift that compounds over time. AI systems inherit this confusion — if the data uses inconsistent terminology, models cannot learn coherent patterns.

Define terms in a glossary that becomes the source of truth. Use those exact terms in database columns, API endpoints, class names, and documentation. When domain experts and engineers talk, they use the same words for the same things.

### Bounded Contexts

A **bounded context** is a boundary within which a particular model applies. The same word can mean different things in different contexts — and that is intentional.

**Example:** In an e-commerce company, "Order" means something different in Fulfillment (a shipment to pack) than in Finance (a revenue recognition event) than in Customer Service (a complaint resolution target). Each context owns its own definition.

Trying to create one universal model for everything leads to bloated, compromised abstractions that serve no one well. Bounded contexts let each subdomain optimize for its own needs while defining clear contracts for cross-context communication.

**A company bolted on is a bounded context.** When a company acquires another company, that company's systems, language, and models do not magically merge. The acquired company operates as a bounded context with its own internal model. Integration happens through explicit translation layers — anti-corruption layers in DDD terms — not by forcing everything into one model.

### Strategic Classification

DDD classifies subdomains by their strategic importance to the business. This determines where to invest:

- **Core** — unique to this business and a source of competitive advantage. Invest deeply here — build custom, iterate, protect.
- **Supporting** — important but not unique. Needed to make the core work, but another company in the same industry would solve it similarly. Build or configure.
- **Generic** — standard functionality with no competitive differentiation. Buy off the shelf — accounting, email, authentication.

This classification drives architecture decisions: core subdomains get the best engineering and the most domain modeling rigor. Generic subdomains get commodity solutions. Getting this wrong — over-investing in generic, under-investing in core — is one of the most common strategic mistakes.

---

## Tactical Patterns

The building blocks for modeling the domain layer. These are the concrete structures that express business rules in code.

### Entity

An object defined by its **identity**, not its attributes. A Customer remains the same Customer even if their name or address changes.

Entities have lifecycle — they are created, modified, and potentially retired. Design entities around the business identity that matters, not the database primary key.

### Value Object

An object defined entirely by its **attributes**. Two value objects with the same attributes are identical. An Address, a Price, a DateRange — they carry a package of related data together and have no independent identity.

Value objects are immutable. If you need a different value, you create a new one rather than modifying the existing one.

### Aggregate

A cluster of entities and value objects treated as a single unit for data changes. One entity serves as the **Aggregate Root** — the gatekeeper for all modifications.

Aggregates define transactional boundaries and invariant enforcement. All business rules that must be true atomically live inside the aggregate boundary. Outside code references only the root's ID — never child entities directly. This prevents data corruption and simplifies reasoning about system behavior.

Design aggregates around business invariants, not database tables. Keep them small. Cross-aggregate communication happens through domain events rather than distributed transactions.

### Domain Event

A record of something significant that happened in the domain. Domain events are immutable and timestamped — "Order Placed", "Payment Captured", "Shipment Dispatched".

Events connect bounded contexts without coupling them. When an aggregate changes state, it publishes an event. Other contexts react to the event independently, without knowing the internals of the publishing context.

### Domain Service

A stateless operation that expresses domain logic not naturally belonging to any entity or value object. If a business operation involves multiple aggregates or crosses entity boundaries, it belongs in a domain service rather than being forced into one entity.

Domain services are defined in terms of the ubiquitous language. "Transfer funds between accounts" is a domain service — it operates on two aggregates and belongs to neither.

### Repository

A collection-like interface for retrieving and persisting aggregates. Repositories abstract the underlying data store so the domain layer expresses business logic without knowledge of databases, file systems, or APIs.

Code requests an aggregate by its identity or by criteria meaningful to the domain. How that maps to SQL, document stores, or graph queries is the repository's concern, not the domain's.

### Factory

An object or method responsible for creating complex aggregates. When constructing an aggregate requires coordinating multiple entities and value objects while satisfying invariants, that construction logic belongs in a factory rather than scattered across calling code.

Factories encapsulate creation complexity and ensure the aggregate is valid from the moment it exists.

### Specification

A composable predicate that encapsulates a business rule. Instead of embedding query conditions or validation logic throughout the codebase, specifications express rules as named, testable objects.

"Customer is eligible for premium pricing" becomes a specification that can be combined with others, reused across contexts, and tested independently. Specifications separate the *what* (the rule) from the *where* (the code that applies it).

---

## Integration Patterns

As systems grow, the relationships between bounded contexts become the primary architectural concern. **Context maps** document these relationships explicitly. Each pattern below describes a different relationship type.

### Anti-Corruption Layer

A translation layer that protects one bounded context from another's model. When two contexts have fundamentally different models, the ACL translates between them so neither context is contaminated by the other's assumptions.

**Example:** A Finance context uses an ACL to translate product data from the Product Catalog into revenue recognition entities with completely different semantics. The catalog sees "products"; finance sees "revenue line items."

### Shared Kernel

A common model shared by multiple bounded contexts. Both contexts agree on a subset of the model and co-own it — changes require agreement from both sides.

Use sparingly. Shared kernels create coupling between contexts. They are appropriate when two contexts genuinely share core concepts and the cost of translation exceeds the cost of coordination.

### Customer-Supplier

One bounded context (the supplier) provides data or services that another (the customer) depends on. The supplier accommodates the customer's needs within reason, and the customer adapts to what the supplier provides.

This is the most common cross-context relationship. The key is that both sides acknowledge the dependency and manage it explicitly rather than pretending it does not exist.

### Published Language

A well-documented, shared language for integration between contexts. Rather than one context conforming to another's internal model, both use an agreed-upon external standard — industry formats, API schemas, or domain-specific interchange formats.

Published languages work best when grounded in established standards (XBRL for financial reporting, HL7 for healthcare, EDI for supply chain).

### Conformist

One bounded context adopts another's model as-is, without translation. The downstream context accepts the upstream model and builds directly on it.

This is pragmatic when the cost of building a translation layer exceeds the benefit. It trades model purity for simplicity — appropriate when the upstream model is close enough to the downstream context's needs.

### Edge Predicates

The relationships between entities, patterns, and bounded contexts are expressed as typed **edge predicates**. Each predicate carries precise semantics — it defines what the relationship means, not just that one exists.

**Strategic predicates** (how contexts relate):

- **implements** — a capability follows a standard or pattern
- **delivered_by** — a capability is built in a specific repository or service
- **integration** — two systems share data or services across a context boundary
- **adopts** — a context uses an external standard as-is (conformist relationship)
- **extends** — a context builds on a standard with additions

**Knowledge predicates** (SKOS-derived, how concepts relate):

- **broader** — a more specific concept is part of a broader one
- **narrower** — a broader concept includes more specific ones
- **related** — associated concepts without hierarchical relationship

**Lineage predicates** (how artifacts trace):

- **derived_from** — created by transforming an earlier version
- **cites** — formally references another artifact for support

Edge predicates are the vocabulary of the context map. Without typed predicates, a relationship graph becomes an undifferentiated web — connected but meaningless. With them, every edge is a statement about how the business works.

---

## Process Patterns

Operational workflows encoded as domain-aware coordination. These define how work gets done across aggregate and context boundaries.

### Saga

A multi-step business process that coordinates across aggregate boundaries. Each step has defined failure and compensation logic — if step 3 fails, the saga knows how to undo steps 1 and 2.

**Example:** Order fulfillment is a saga: it coordinates inventory reservation, payment capture, picking and packing, and shipping — each owned by a different aggregate, each with its own rollback behavior.

### Choreography

Event-driven coordination where each bounded context reacts to domain events independently. No central orchestrator — each context knows what to do when it sees a relevant event. The overall process emerges from individual reactions.

**Example:** When an order is placed: Inventory reacts by reserving stock, Finance reacts by authorizing payment, Fulfillment reacts by queuing the pick. Each context acts independently.

### Goal

A business objective encoded as a measurable process outcome. Goals connect process execution to analytics — "reduce order-to-delivery time by 20%" traces directly to the saga steps it depends on, making it possible to identify specific process bottlenecks.

---

## Extended Patterns

Patterns that extend DDD's core tactical and strategic toolkit for specific architectural needs.

### CQRS

**Command Query Responsibility Segregation** — separating read and write operations into distinct models. The write model enforces business rules and maintains consistency. The read model is optimized for queries and can be denormalized, cached, or shaped for specific consumers.

CQRS is valuable when read and write patterns diverge significantly — when the data structures that enforce business rules efficiently are not the same structures that answer business questions efficiently.

### Event Sourcing

Instead of storing the current state of an entity, store the sequence of events that produced it. The current state is derived by replaying events. Every state change is preserved, providing a complete audit trail and the ability to reconstruct state at any point in time.

Event sourcing pairs naturally with domain events and CQRS — events are the write model, and read models are projections built from the event stream.

---

## Analytics Patterns

Measurement and metrics encoded as first-class domain concepts. These define how performance is measured:

- **Metrics as domain entities** — KPIs, SLAs, and business measures defined with the same rigor as operational entities. "Customer Lifetime Value" is not just a report; it is a domain concept with a precise definition, calculation method, and owning context.
- **Dimensional models derived from aggregates** — analytics schemas are not invented independently. They are derived from the domain model, ensuring that what gets measured reflects what actually exists.
- **Coherence scoring** — measuring how well implementation matches the domain model. If the analytics show metrics that do not trace to domain entities, something is being measured that the business does not formally understand.

These three pattern types work together: domain patterns define the structure, process patterns operate within it, and analytics patterns measure it.

---

## DDD and AI

DDD and AI have a symbiotic relationship. Traditional DDD was hard to implement because it required sustained discipline that most organizations could not maintain. AI changes the economics.

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

---

## Related Links

### Framework Components

- [Explicit Architecture](explicit-architecture.md) — how DDD becomes a fully encoded company structure
- [What Architecture Actually Is](what-is-architecture.md) — architecture vs. infrastructure, rule types, and design approaches
- [Patterns and Bounded Contexts](patterns-and-bounded-contexts.md) — growing through patterns
- [Anti-Corruption Layer (ACL)](ddd-acl-governance-aas.md) — semantic firewalls at boundaries
- [DDD Solves AI Transformation](ddd-solves-ai-transformation.md) — the argument for DDD as AI enabler
- [SemOps Aggregate Root](semops-aggregate-root.md) — aggregate root pattern applied to SemOps
- [BizBok-DDD Derivation](bizbok-ddd-overlay.md) — aligning business architecture with DDD through mechanical derivation

### Reference

- [DDD Reference Catalog](https://github.com/semops-ai/semops-dx-orchestrator/blob/main/docs/patterns/domain/ddd-reference-catalog.md) — complete Evans 51-element taxonomy with definitions
- [DDD Pattern Registration](https://github.com/semops-ai/semops-dx-orchestrator/blob/main/docs/patterns/domain/ddd.md) — SemOps adoption context and interpretation
- [DD-0024: DDD Decomposition](https://github.com/semops-ai/semops-dx-orchestrator/blob/main/docs/design-docs/DD-0024-ddd-decomposition.md) — capability and validation rule derivation from DDD elements

### Citations

- Evans, Eric. *Domain-Driven Design*. Addison-Wesley, 2003.
- Vernon, Vaughn. *Implementing Domain-Driven Design*. Addison-Wesley, 2013.
