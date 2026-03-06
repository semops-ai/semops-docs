---
doc_type: hub

pattern: patterns

provenance: 1p

metadata:
 pattern_type: concept
 brand_strength: high
---

# Patterns

> A **Pattern** is a semantic structure with enough self-contained meaning to be recognized and operated on as a whole — by both humans and machines.

Patterns work in the Semantic Operations Framework because they preserve complete concepts — enough context for a human to recognize what they're looking at, and enough structure for AI to match against its training data:

- **AI leverage:** Known patterns exist in AI training data as proven, validated structures. Implementations can be requested at different levels of rigor — from textbook canonical to pragmatic shortcut — and the approach that fits the context can be selected.

- **Rapid adoption:** Whole patterns can be quickly adopted — validated structures adapted to the domain and systems through [Explicit Architecture](../EXPLICIT_ARCHITECTURE/README.md) and [Semantic Coherence](semantic-coherence.md). AI accelerates this because the canonical form is already in the model. The friction shifts from implementation to decision: *should* we adopt this, not *can* we.

- **Evolution:** Standard patterns provide stable baselines; optimized patterns derive from them with tracked, intentional deviations. Nuanced decisions and trade-offs are captured in lineage, enabling distinction between intentional innovation and unintentional (and counterproductive) reinvention. See [Pattern Operations](pattern-operations.md).

---

## How Patterns Map to the Semantic Funnel

Patterns are Knowledge-level **Objects** in the [Semantic Funnel](../../RESEARCH/FOUNDATIONS/semantic-funnel.md) — self-contained semantic structures that encode meaning beyond raw relationships (Information). The pattern taxonomy spans all levels, but the primary transition is I→K: transforming structured information into actionable, domain-grounded knowledge.

```text
 Objects: DATA -------> INFORMATION ══════> KNOWLEDGE -------> WISDOM
 │
 Rules: Interpretive
 (domain-grounded
 semantic units)
 │
 Agents: Domain experts,
 AI (for 3p patterns)
```

| Element | I → K |
| ------- | ----- |
| **Objects** | Patterns — both standard (3p = third-party) baselines and optimized (1p = first-party) innovations, encoding domain knowledge into bounded, operational units |
| **Agents** | Domain experts (assert patterns from domain understanding); AI (implement and validate 3p patterns where training data provides canonical forms) |
| **Rules** | Interpretive — pattern boundaries define scope, invariants define validity, provenance tracks origin, lineage tracks evolution |
| **Determinism** | Medium — standard patterns (3p) are high-determinism (canonical forms known); optimized patterns (1p) require interpretive judgment for deviations from baseline |
| **Mechanism** | [Semantic compression](semantic-compression.md) at the right granularity: patterns are concrete enough to implement and validate, abstract enough to compose and reason about. AI's regression to mean becomes a superpower for 3p adoption — canonical, correct implementations |
| **Failure mode** | Feature-based growth (ad-hoc additions without semantic boundaries); patterns without provenance or lineage; atomizing too small (lose context) or too large (cannot evolve) |

---
## Why Patterns?

A pattern is a common sense concept. When someone says "we have a CRM," everyone who understands even the basic definition of a CRM in the room gets it. They know it connects them to a large body of established practices for "managing customers" and they will likely have a set of capabilities that immedately come to mind like customer support workflow, promotion targeting, or order history. CRM is a pattern.

The challenge isn't using patterns. People use them intuitively all the time. The challenge is writing a definition tight enough for a domain model while preserving that intuitive simplicity. What follows is our attempt to capture "you know it when you see it" in structural terms.

SemOps formalizes patterns to enable three things:

- **Fast, accurate bootstrapping** — patterns compress large bodies of domain knowledge into AI-legible packages. An agent that sees "adopt CRM" can immediately reason about implied capabilities, established practices, and architectural fit. This lets humans and AI bootstrap new domains together, leaning into what AI is already good at: recognizing and reasoning about consensus knowledge.
- **Fast growth with semantic guardrails** — patterns provide the structured baseline that [Semantic Coherence](semantic-coherence.md) measures against. You can iterate and grow quickly because the pattern model is semantically structured enough for coherence processes to detect drift and signal corrections just as quickly. Move fast *and* know when you've broken something.
- **Domain-to-infrastructure bridge** — patterns don't stay on a whiteboard. They encode into the architecture through the pattern→capability→repository→infrastructure chain. When you adopt CRM, that becomes capabilities (contact management, ticketing), which map to repos, which deploy services. This encoding is what makes coherence auditable at runtime — not just at design time — and what makes [scale projection](scale-projection.md) possible. You can ask "if we scale this architecture, do the patterns still hold?" because the patterns are *in* the running system, not just *about* it.

---

## Definition

A pattern is a [data shape](../EXPLICIT_ARCHITECTURE/data-shapes.md) — an evolving semantic structure that converges toward the ideal approach for a domain need. At any point in time, a pattern is an approach that:

1. **Fits a domain** — the subject matter of the work
2. **Implies capabilities** — what the system should be able to do
3. **Is recognizable** — someone with basic understanding of the domain would see why this approach fits this problem space

Patterns span the full range from non-technical concepts to highly technical implementation details. SECI (Nonaka's knowledge creation model) is a pattern — it fits knowledge management, implies practices like externalization and combination, and anyone in the field would recognize why it applies. Medallion Architecture is also a pattern — it fits data processing, implies staged curation (Bronze, Silver, Gold), and any data engineer would recognize it. CRM is a domain pattern. Event Sourcing is an implementation pattern. The definition covers all of them equally.

These three properties are the **fitness criteria** for the search. They describe what makes a data shape a valid pattern — what it must satisfy to be a candidate. The lifecycle (provenance, SKOS taxonomy, neutral primitives, 1P/3P classification) is the search process that reshapes the pattern over time, converging it toward better domain fit.

A pattern is dynamic, has identity, is registered, tracked, versioned. But the shape of the data evolves: what attributes matter, what capabilities are implied, what relationships exist. The entity persists; the shape converges. Early in the lifecycle, the shape is rough ("we need something like CRM"). Through adoption it takes on consensus form. Through innovation it's refined further. Through coherence measurement, the distance between current shape and ideal shape is quantified. The pattern is the current best shape. The lifecycle is the search for a better one.

### Composable Without Loss

Patterns can be composed into larger structures without losing their individual meaning. A "Sales Domain" pattern can contain "Customer", "Order", and "Product" patterns—each remains coherent independently.

This scales to large additions: entire domain architectures—new product lines, business models, or integration partners—can be added as patterns. The new pattern composes with existing patterns rather than fragmenting the system. Feature-based growth cannot do this; it would result in hundreds of disconnected requirements. Pattern-based growth adds one coherent unit that happens to be large.

---

## Pattern Taxonomy

Patterns are typed based on their purpose and provenance. The specific types needed will be driven by the domain—there is no universal taxonomy. Types are tracked via frontmatter metadata (`pattern-type`), enabling the flexible edge approach to pattern evolution.

### By Provenance (Origin)

The provenance distinction is fundamental to any pattern-based system:

| Type | Provenance | Lineage | Definition | Role |
| --------------------- | --------------------------------- | -------------------------------------- | ------------------------------------------------ | --------------- |
| **Standard Pattern** | 3p (third-party: external source) | — | Proven solution adopted as-is | Baseline |
| **Optimized Pattern** | 3p | Tracked deltas from standard → yours | Intentional, understood deviations from baseline | Differentiation |

**Why this matters:** The provenance split determines how AI can help. For 3p patterns, AI can implement, validate, and optimize toward the canonical form. For 1p patterns, AI must be more cautious—these are organization-specific innovations that the model has not seen.

### By Purpose (Pattern-Type)

The purpose types below emerged from SemOps's needs—each domain will drive different categories:

| Pattern-Type | Purpose | SemOps Examples |
| ------------ | ------- | --------------- |
| **concept** | Foundational ideas and principles | Semantic Coherence, Domain Pattern |
| **domain** | Business domain bounded contexts | Sales, Authentication, Publishing |
| **process** | Workflows and operations | Content Classification, Promotion Loop |
| **implementation** | Technical realizations | RAG Pipeline, Classifier Architecture |
| **integration** | Cross-boundary connections | Context Maps, ACLs |

Other domains might need different types: **regulatory** patterns for compliance-heavy industries, **transaction** patterns for financial systems, **event** patterns for real-time systems. The principle is the same—type patterns based on what the domain needs to distinguish.

For practical guidance on finding patterns by type, sizing them for your architecture, and evolving from 3P adoption to 1P innovation, see [Working with Patterns](working-with-patterns.md).

---

### Domain Is About-ness

"Domain" means the about-ness of the work. This concept appears across architectural disciplines — [Domain Driven Design](../EXPLICIT_ARCHITECTURE/domain-driven-design.md) uses it for software modeling, `BIZBOK` uses it for business capability mapping, `TOGAF` uses it for enterprise architecture — but the core meaning is the same: the subject matter the work is about. A CRM vendor's domain is customer relationships. A research lab's domain is their field of study. A publishing platform's domain is content lifecycle. A theoretical physics project's domain is theoretical physics.

This breadth matters. Patterns aren't limited to business operations. If the problem is entirely theoretical or conceptual, patterns still apply — the approach just fits a theoretical domain rather than an operational one. SECI (Nonaka's knowledge creation model) is a pattern that fits knowledge management. It's not a business process or a software feature. It's an approach to a domain.

### Patterns Imply Capabilities

A pattern carries expectations about what the system should be able to do. Adopting CRM means you should have contact management, ticketing, and SLA tracking. Adopting Digital Asset Management means you should have content ingestion, approval workflows, and multi-channel distribution. These aren't rigid requirements — they're the capabilities that someone familiar with the domain would expect.

The strength of this implication varies by pattern type:

- **Domain patterns** directly imply specific capabilities. CRM → contact management, ticketing, SLA tracking.
- **Implementation patterns** constrain how capabilities are built. Medallion architecture → staged data processing (Bronze, Silver, Gold).
- **Concept patterns** influence capability design without directly implying specific features. SECI → knowledge externalization practices.

This gradient is important. When a domain pattern is adopted, you can enumerate expected capabilities with confidence. When a concept pattern is adopted, the influence is real but less concrete. The domain model handles both — it just weights the implication differently.

**Implementation example** — the full traceability chain from [STRATEGIC_DDD.md](https://github.com/semops-ai/semops-core/blob/main/docs/STRATEGIC_DDD.md):

```text
Pattern → Capability → Repository
(why)      (what)       (where)
```

| Capability | Implements Patterns | Delivered By |
|-----------|-------------------|--------------|
| `domain-data-model` | `ddd`, `skos`, `prov-o`, `explicit-architecture`, `edge-predicates`, `shape`, `unified-catalog` | semops-hub-pr |
| `ingestion-pipeline` | `semantic-ingestion` (1p), `etl` (3p), `medallion-architecture` (3p) | semops-hub-pr |
| `publishing-pipeline` | `dam` (3p), `dublin-core` (3p), `cms` (3p), `pim` (3p) | publisher-pr |
| `coherence-scoring` | `semantic-coherence` | data-pr, semops-hub-pr |

Every capability traces to at least one pattern. Gaps indicate either a missing pattern or an unjustified capability. This table is parsed into the database and queryable at runtime via the `capability_coverage` view — the encoded architecture, not documentation about it.

### Recognizable Fit

"Fits" is inherently subjective. The bar is practical: a person (or AI agent) with basic understanding of the domain can see why this approach applies to this problem space.

This is also why patterns are a natural fit for AI agents. The connection runs deeper than "LLMs know what CRM means."

**Patterns optimize for the context window.** An agent's central constraint is context — the tokens the model can attend to at inference time. Anthropic calls managing this "context engineering" and identifies it as the hard problem of agent design. Patterns are compressed domain knowledge: "adopt CRM" loads an entire body of practices, capabilities, and architectural implications into a few tokens. They are [semantic compression](semantic-compression.md) optimized for exactly the resource agents have least of.

**Patterns are how agents plan.** Planning is not a primitive capability of agents — it is a behavioral pattern that emerges from combining model reasoning, prompts, and memory. Chain of Thought, ReAct, Tree of Thoughts are all prompting techniques that compose from the same primitives. SemOps patterns work the same way: they decompose into capabilities, which decompose into scripts. When an agent reasons about "adopt CRM," it is performing Chain-of-Thought planning against the pattern's implied capability set — decomposing the pattern into actionable steps. The pattern gives the agent something structured to reason *about*.

**The traceability chain is structurally isomorphic to agent architecture.** The SemOps Pattern → Capability → Script chain mirrors how agent behavioral patterns decompose into primitives (Model, Context, Prompt, Memory, Tools) which decompose into implementations (LLM calls, vector stores, tool APIs). This is not a metaphor — it is the same structural principle. Higher-order behaviors decompose into composable units, which decompose into executable code. The same coherence signals apply: a capability with no pattern link is unjustified code; a pattern with no capability link is an undelivered aspiration. An agent provisioned with Memory that no behavioral pattern requires is over-engineered. The audit works identically in both systems.

**Patterns correspond to consensus knowledge.** Established patterns have extensive representation in training data — they are precisely the kind of knowledge LLMs reason about best. When an agent sees "adopt CRM," it can immediately reason about implied capabilities, established practices, and architectural fit because CRM is a consensus concept with high training signal. The same property that makes patterns recognizable to humans (fitness criterion 3) makes them legible to AI.

For the full analysis, see [Agent Primitives](https://github.com/semops-ai/semops-research/blob/main/docs/research/agent-primitives.md) — a cross-source analysis of 13 authoritative sources validating the structural isomorphism between agent architecture and the SemOps pattern model.

---

## The Pattern Lifecycle

The definition above describes fitness criteria — what makes a data shape a valid pattern. The lifecycle is the search process that reshapes the pattern over time, converging it toward better domain fit. Provenance (1P/2P/3P), SKOS taxonomy, neutral primitives, the Semantic Optimization Loop — all lifecycle. Each stage refines what the pattern means, what it implies, and how it relates to other patterns. The pattern entity persists across stages; its shape converges toward better fit.

### Discovery

A need surfaces — a feature request, an architecture gap, a new domain to model. This is search initialization: the system doesn't yet know the ideal shape, only the need it must serve. The crucial design choice: **the input does not have to be structured.** A human or agent can enter at any level of specificity:

- **A single capability** ("customer support needs to see order history when talking to a customer") — the system identifies which patterns imply that capability, evaluates the existing architecture, and surfaces not just the capability but the full pattern set the organization needs.
- **A set of capabilities** ("ticketing, contact management, SLA tracking") — the set implies a pattern; the system confirms and identifies gaps.
- **A broad pattern** ("apply CRM") — fine as-is. You may only implement a few capabilities now. Partial implementation of a pattern is expected. Coherence tracks the unimplemented capabilities as signals, not failures.
- **A pattern at the wrong scale** — more on this below.

The single-capability case is where bootstrapping matters most. The person asking may not know what CRM is or what a customer support architecture looks like. They just know their support team can't see order history. But the system can reason about it: "order history access" implies a CRM pattern (customer data, interaction history) and a customer support pattern (case management, agent tooling). Evaluated against the existing architecture — which might have nearly zero CRM capabilities — the system doesn't just say "add order history access." It says "based on your domain, you need these patterns" and surfaces the full implied capability set for each.

Even if only one or two capabilities are stood up initially, the architecture is correct from the start. The patterns provide the complete shape. Coherence tracks what's implemented vs. what's implied. The gap between "what exists" and "what the patterns say should exist" isn't a failure — it's a roadmap. You're building toward the right architecture from day one, even when starting from a single need.

This flexibility is intentional. The domain model must be deep enough — patterns, lineage, coherence signals — that new ideas can enter without upfront classification. The model does the work of figuring out where things fit. Frictionless ideation, rigorous evaluation.

**Implementation example — `/intake` on research-pr** ([session notes](https://github.com/semops-ai/semops-research/blob/main/docs/session-notes/ISSUE-24-project-review-intake.md)):

An `/intake` evaluation of research-pr inventoried 4,746 lines of production code, 98 tests, and 31 research documents. The process evaluated the repository's capabilities against registered patterns and produced a coverage table:

| Capability | Pattern Coverage |
|-----------|-----------------|
| `corpus-meta-analysis` | `semantic-ingestion`, `raptor`, `agentic-rag` |
| `data-due-diligence` | `ddd`, `data-modeling`, `explicit-architecture`, `business-domain` |
| `reference-generation` | `ddd`, `data-modeling`, `explicit-architecture`, `business-domain`, `agentic-rag`, `bizbok` |
| `agentic-ddd-derivation` | `ddd`, `bizbok-ddd-derivation`, `ddd-agent-boundary-encoding` |

The intake found that `llm-enrichment` — previously listed as a fifth capability — was not actually a capability. It was the LLM-augmented path within `reference-generation`. The correction removed it from the capability registry and folded its pattern coverage into the parent capability. This is the kind of structural correction that discovery enables: the pattern model is deep enough for an agent to identify when something is miscategorized, not just missing.

### The Bootstrapping Problem: You Don't Know the Domain

The hardest case at intake isn't wrong scale or missing capabilities. It's **wrong domain.** People naturally frame problems from their business identity rather than from the domain the problem belongs to.

Consider a house-cleaning business that needs to solve a scheduling problem. The owner thinks "house-cleaning scheduling" — framing the problem from their business identity. But "house-cleaning scheduling" isn't a domain. It's a business context applied to a domain capability. The actual domain is appointment or resource scheduling — a well-solved supporting domain with established patterns (calendar management, route optimization, workforce allocation).

This distinction matters because it changes which patterns fit. "House-cleaning scheduling" implies building something custom. "Resource scheduling" implies adopting an established pattern with known capabilities. The decomposition to neutral primitives reveals what the business actually needs: time slot management, staff assignment, travel optimization, customer notifications. None of these are house-cleaning-specific. They're scheduling capabilities that any service business would recognize.

Business architecture frameworks like BIZBOK formalize this insight with three layers of capability vocabulary:

1. **Common reference model** — supporting domains every business has (finance, HR, scheduling, IT). These are commodity capabilities. The patterns are well-established and the right answer is usually adoption, not innovation.
2. **Industry reference model** — core domain capabilities for that industry. For house cleaning: service quality assurance, inspection protocols, supply management. These are where competitive advantage lives.
3. **Company-specific** — where 1P innovation happens. What this particular business does differently.

The bootstrapping problem is that at intake, the domain→capability map doesn't exist yet. The person knows what business they run but not which domain each problem belongs to. They conflate their business identity with the problem domain: "house-cleaning scheduling" instead of "resource scheduling in a service business."

Intake is domain discovery. Before the system can search for the right pattern shape, it needs to identify which domain the problem actually belongs to. The classification — is this a core domain problem, a supporting domain problem, or a generic one? — determines which patterns are candidates. A core domain problem might need 1P innovation. A supporting domain problem almost certainly has a 3P pattern that fits. Misidentifying a supporting domain problem as core leads to building custom solutions for solved problems.

This is also where the BizBok→DDD derivation path applies: classify the business → derive which capabilities are core vs. supporting → identify the domain each capability belongs to → find patterns that fit that domain. The intake process walks this chain, even when the input is as simple as "I need to solve scheduling."

**Implementation example — bootstrapping a net-new domain** ([session notes](https://github.com/semops-ai/semops-core/blob/main/docs/session-notes/ISSUE-163-ecommerce-tshirts.md)):

Input: "I want to sell SemOps t-shirts on the website with full e-commerce and offsite fulfillment." No patterns, capabilities, or repos specified — pure natural language.

The `/intake` process ran a full territory map: semantic search against the pattern registry, KB entity search, and a scan of all 23 capabilities. Results:

| Search | Best Match | Similarity | Verdict |
|--------|-----------|-----------|---------|
| Pattern registry | `pim` (Product Information Management) | 0.315 | Weak — product data governance, not transactions |
| KB entities | `backstage-software-catalog` | 0.312 | Not related — software catalogs, not commerce |
| Capability scan | `financial-pipeline` | Closest | Invoicing/accounting, not retail |

**No meaningful matches.** The system correctly identified e-commerce as entirely outside the current domain model — no patterns for transactions, payments, order management, or fulfillment exist anywhere. Rather than forcing a fit, it classified the input as a net-new bounded context and recommended:

1. Adopt a 3P pattern (`headless-commerce`) rather than build custom
2. Classify as **generic** (commerce is not core to semantic operations)
3. Consider a turnkey solution (Shopify/Printful) over building it into the architecture

This is the bootstrapping chain working end-to-end: unstructured input → domain discovery → pattern search → gap identification → honest assessment that the right answer is adoption, not innovation, in a domain that's entirely supporting.

### Handling Scale: "Too Small" and "Too Big"

**"Too small" is not a real problem.** If you adopt CRM but only implement ticketing, that's fine. You adopted CRM. The pattern is the pattern regardless of how much is implemented today. Coherence tracks the gap between what was adopted and what was built, but the gap is an observation, not a failure. Partial implementation is the normal state — few organizations implement every capability a pattern implies.

**"Too big" means wrong scale or wrong domain, not wrong amount.** Consider a local delivery company. Someone proposes adopting Maersk-style global shipping. Technically, global shipping "fits" delivery — both move things from A to B. But recognizable fit catches the mismatch: someone with basic domain understanding of local delivery would say the right consensus pattern is last-mile delivery, not global shipping.

The decomposition to neutral capabilities reveals why. Global shipping implies customs clearance, container logistics, port management. None of these apply to a local delivery business. The capabilities decompose into a different set than what the company needs. The right pattern — last-mile delivery — is simpler, well understood at the correct scale, and implies the right capabilities: route optimization, driver dispatch, delivery confirmation.

**Right fit is determined by judgement and coherence across the architecture, not just individual pattern-to-capability matching in isolation.** Global shipping might match some local delivery capabilities one-by-one. But evaluated across the full architecture of a local delivery business — no ports, no customs, no containers — the mismatch is clear. The architecture-level view catches what per-link matching misses.

### Adoption (3P)

Adoption gives the pattern its consensus shape. An established approach is registered as a pattern. The key principle: adoption expresses the pattern at the **neutral, primitive level** — the non-proprietary core.

You can start from a proprietary reference. "Do it the Salesforce way" is a fine starting point. But adoption requires decomposing the proprietary approach into neutral capabilities: contact management, ticket lifecycle, automation rules. What remains after decomposition is the consensus pattern. What the proprietary source added on top is either discarded or registered as a distinct 1P innovation.

This decomposition serves two purposes:

1. **Honest capability mapping.** The capabilities should reflect what the domain needs, not what a particular vendor provides. A company doesn't need "Salesforce flows" — they need automation rules. The neutral framing lets the architecture be vendor-independent.
2. **Recognizable fit validation.** If you can't express the pattern in neutral terms, the fit may be to a vendor's ecosystem rather than to the domain. That's a signal worth investigating.

### Innovation (1P)

Innovation reshapes the pattern beyond consensus — synthesizing from established building blocks into something closer to the ideal fit. Novel approaches are synthesized from established building blocks. SemOps' `semantic-coherence` pattern combines information theory with system quality metrics into something new. `semantic-ingestion` synthesizes ETL, Medallion Architecture, and MLOps into a pipeline where every byproduct becomes a queryable knowledge artifact.

1P patterns are linked back to their 3P foundations via SKOS broader/narrower relationships. This ensures they remain grounded in shared knowledge even when the synthesis is novel. The 3P layer is "don't reinvent the wheel." The 1P layer is where differentiation happens. Both are first-class lifecycle steps.

### The Semantic Optimization Loop

The full cycle: adopt 3P standards → innovate 1P on top → link via SKOS → measure coherence → repeat. This is the engine of Semantic Operations — the search algorithm that converges patterns toward their ideal shape.

```text
Pattern ──produces──→ Capability
   ^                      |
   └──── Coherence ←──audits──┘
         (informs)
```

Pattern is the prescriptive force — "what should we look like?" Coherence Assessment is the evaluative counterpart — "does reality match intent?" Together they form a feedback loop. Pattern sets the target. Coherence measures the gap. The optimization minimizes the gap over time.

---

## Patterns and Capabilities

The relationship between patterns and capabilities works in two directions.

**Bottom-up:** A capability declares which patterns it implements. "The ingestion pipeline implements semantic-ingestion, ETL, and medallion architecture." This is the current operational model — capabilities trace to patterns, and every capability must trace to at least one.

**Top-down:** A pattern implies which capabilities should exist. "Adopting CRM implies contact management, ticketing, and SLA tracking should exist." This direction is what drives reference architecture generation.

**Alignment is the degree to which these two directions agree.** When they match, the architecture is coherent. When they don't, there are signals:

| Signal | Meaning |
|--------|---------|
| Expected capability missing | A pattern implies something that doesn't exist |
| Capability without pattern | Something exists without domain justification |
| Cross-domain composition | A capability draws from multiple domains — valid innovation or a mislink? |
| Proprietary pattern not decomposed | An approach represents vendor lock-in rather than domain fit |
| Scale mismatch | The pattern set doesn't match the business architecture at the right scale |

These are coherence audit signals, not gates. They inform decisions; they don't block them. The flexible edge is free to exist — aggregate root invariants protect the stable core.

---

## Reference Architecture: Patterns as Building Blocks

This is where patterns connect to the larger vision. The claim: **the same pattern model can describe any domain's ideal architecture.**

### The Flow

Take any company. Classify its business — sector, industry, scale, business model. From that classification, a reference architecture generator can work **domain-down**:

1. **Business classification** → what kind of business is this? A logistics SaaS company. A regional trade show organizer. A publishing platform.

2. **Domain patterns** → what patterns fit this domain? A logistics company's patterns are things like supply chain management, fleet tracking, warehouse management, last-mile delivery. These aren't SemOps patterns — they're the patterns that run *that company's* domain.

3. **Implied capabilities** → each pattern implies what the system should be able to do. Supply chain management implies shipment tracking, route optimization, carrier management, inventory visibility. Fleet tracking implies vehicle telemetry, maintenance scheduling, driver assignment. These are the capabilities someone with logistics domain knowledge would expect.

4. **Ideal state** → the full pattern set and its implied capabilities constitute the "should be" baseline. This is the reference architecture — what the company's systems should look like if the domain is well-served.

Every step in this flow depends on the three properties of the pattern definition:

- **Fits a domain** → the patterns are selected because they fit the logistics domain (about-ness)
- **Implies capabilities** → each pattern carries expectations about what should exist (the top-down direction)
- **Recognizable fit** → someone in logistics would see why supply chain management fits and why global container shipping doesn't (scale/domain check)

### Three Architecture Perspectives

Reference architecture is one of three perspectives that together provide a complete picture:

**Reference architecture** works **domain-down.** Given a business domain and its patterns, enumerate the implied capabilities. That's the "should be" baseline. Business Architecture → DDD Architecture → Capabilities filled with Patterns = ideal state.

**Scale projection** works **infrastructure-up.** From the actual implementation — what's built, what's running, what infrastructure exists — project forward to scale. Does the domain model hold up? A local delivery business that adopted global shipping patterns would find itself building ports and customs clearance at scale. The projection reveals the mismatch before the investment is made.

**Coherence measurement** is where they meet. It quantifies the gap between the domain-down ideal state and the infrastructure-up projected reality. Coverage, alignment, and regression signals measure the distance. The three perspectives are facets of the same architecture-level coherence evaluation.

---

## Why Patterns Work for AI

Patterns are a natural fit for AI agents for the same reasons they work for humans — but the alignment runs deeper than "AI can read about CRM."

**Consensus in training data.** Patterns correspond to established ideas with extensive representation in the data language models are trained on. When an agent encounters "adopt DDD," it can reason about bounded contexts, aggregates, and ubiquitous language because those concepts are well-represented in its training data. This isn't a coincidence — it's the same property as "recognizable fit." The things that are recognizable to domain experts are recognizable to AI because they're the consensus ideas the domain has produced.

**Semantic compression.** A pattern name carries a large body of meaning in a small context package. "CRM" is two words that imply an entire capability set, established practices, vendor landscape, and common pitfalls. For an AI agent operating within a limited context window, this compression is valuable — the pattern name serves as a pointer to extensive structured knowledge.

**Grounding to domain problems.** Patterns prevent AI from reasoning in the abstract. Instead of "build a system that manages customer relationships" (vague), the agent works with "adopt CRM, which implies contact management, ticketing, and SLA tracking" (structured). The pattern provides the scaffold that turns vague intent into specific, auditable architecture.

**Structured rulesets.** Patterns come with implicit rules — what belongs, what doesn't, how things relate. These rules are exactly the kind of structured reasoning AI agents handle well. The agent doesn't have to invent the rules; it applies established ones.

**Architecture legibility.** Because patterns fit into a known architecture (SKOS taxonomy, capability mapping, coherence signals), agents can navigate the full structure. They can answer questions like "what patterns does this company use?", "what capabilities are implied but missing?", "what would change if we adopted this new pattern?" The architecture becomes queryable because patterns make it explicit.

---

## The Registry: 3P Standards and 1P Innovations

SemOps maintains a pattern registry organized by provenance.

### 3P Standards

Third-party patterns are established approaches adopted as-is. They represent the "don't reinvent the wheel" layer — consensus knowledge that provides the foundation.

The current registry includes standards like DDD (the primary architecture pattern), SKOS (concept taxonomy), PROV-O (provenance tracking), Dublin Core (content metadata), DAM (digital asset management), and others. Each is adopted at the neutral, primitive level.

### 1P Innovations

First-party patterns are novel approaches synthesized from 3P building blocks. Each innovation combines established foundations into something new:

- **Semantic Coherence** — combines information theory with system quality metrics. The objective function of the Semantic Optimization Loop.
- **Semantic Ingestion** — synthesizes ETL and Medallion Architecture with domain-aware enrichment. Every byproduct becomes a queryable artifact.
- **Scale Projection** — synthesizes RLHF, SECI, and data profiling. Manual processes intentionally generate ML training data, creating a path to autonomous execution.
- **[Explicit Enterprise](../EXPLICIT_ARCHITECTURE/explicit-enterprise.md)** — synthesizes Platform Engineering with agent-first system design. Humble tools become agent-addressable signal streams.
- **Semantic Object Pattern** — patterns themselves as first-class semantic objects with SKOS taxonomy and adoption history. The aggregate root is the innovation.
- **Agentic Lineage** — extends lineage tracking with agent decision context and trust provenance.

Each 1P pattern is linked back to its 3P foundations via SKOS, ensuring the innovation remains grounded.

### The Relationship

The 3P layer provides vocabulary and building blocks. The 1P layer provides differentiation and synthesis. Together they form the knowledge architecture — established patterns that anyone can reason about, plus novel combinations that create unique value.

---

## Examples

### Netflix UDA: Bootstrapping and Monotonic Extension

Netflix unveiled Unified Data Architecture (UDA) in June 2025 — a foundational system that addresses the core problem of modeling business entities consistently across dozens of internal systems.

**Upper** is a bootstrapping upper ontology at the core of UDA:

| Property | Description |
|----------|-------------|
| **Self-describing** | Defines what a domain model is |
| **Self-referencing** | Models itself as a domain model |
| **Self-validating** | Conforms to its own validation rules |
| **Federated** | Closed for modification, open for extension |
| **Standards-based** | Built on W3C RDF (graph representation) and SHACL (validation) |

Upper supports **monotonic extension** — new attributes and relationships can be added without modifying existing definitions. This is critical for a growing organization: teams can extend the model without breaking existing consumers.

#### Bootstrapping: The Move That Creates the Foundation

The critical property is bootstrapping. Upper doesn't just define domain models — it defines *what a domain model is*, then models itself as an instance of that definition. The foundation is its own first citizen. Before Netflix can model any business entity, Upper must exist to define the rules. And Upper exists by conforming to its own rules.

SemOps' `semantic-object-pattern` makes the same bootstrapping move, one level up. A pattern is a data shape converging toward ideal domain fit. The semantic-object-pattern is the data shape that defines what valid data shapes are — and it's itself one. The aggregate root is the innovation.

| Upper (Netflix) | Patterns (SemOps) |
|---|---|
| Defines what a domain model is | Pattern defines what an approach is |
| Models itself as a domain model | semantic-object-pattern is itself a pattern |
| Conforms to its own validation rules | Pattern model implies capabilities → coherence audits whether those capabilities exist |
| Bootstraps from RDF, SHACL | Bootstraps from SKOS, PROV-O, DDD |

Both bootstrap from established standards (3P) to create something self-referential (1P). Neither invents its foundations from scratch. Upper doesn't invent graph representation — it adopts RDF. SemOps doesn't invent taxonomy or provenance — it adopts SKOS and PROV-O. The bootstrapping move is: adopt established foundations, then use them to define yourself. The Semantic Optimization Loop is a bootstrapping loop.

**Bootstrapping is what separates a pattern model from a pattern catalog.** A catalog lists patterns. A bootstrapping system defines what patterns are, manages itself as a pattern, validates itself by its own rules, and extends without breaking. This is a lifecycle property — it describes how the semantic-object-pattern works, not what any pattern is.

#### Monotonic Extension: A Use-Case Choice

Both Upper and SemOps support monotonic extension — growth without breaking the foundation. But the enforcement mechanism differs, and the difference is a deliberate choice about where to draw the boundary between [stable core and flexible edge](../EXPLICIT_ARCHITECTURE/stable-core-flexible-edge.md).

| | Netflix Upper | SemOps Patterns |
|---|---|---|
| Extension mechanism | Structural (SHACL constraints) | Relational (SKOS broader/narrower) |
| Invariant enforcement | Schema-level — structurally prevents breaking changes | Coherence audit — signals drift, doesn't block |
| Why | Dozens of teams; breaking changes cause outages | Small org; flexibility outweighs rigidity |
| Maps to | Stable core with hard boundary | Flexible edge with audit, not gate |

Netflix *needs* a gate because at their scale, an uncoordinated schema change breaks downstream consumers. SemOps deliberately chooses not to gate because the cost of rigidity outweighs the cost of drift at current scale. Both achieve "extend without breaking" — one structurally, one relationally.

Monotonic extension is not a single pattern. It's a property that can be enforced at different levels depending on the use case. The choice of enforcement mechanism is a stable-core vs. flexible-edge decision.

#### Self-Validation: The Pattern Model as Its Own First Test Case

The bootstrapping property creates a natural proof of the coherence model. `semantic-object-pattern` implies capabilities: pattern registration, SKOS taxonomy management, coherence measurement. Coherence audits whether those capabilities exist and are implemented. If they aren't, the system flags *itself*.

The pattern model is its own first test case for coherence. If the coherence mechanism works, it catches gaps in the very system that defines it. If it doesn't catch those gaps, the mechanism itself needs work. The bootstrapping property turns the system into a self-testing loop.

### Semantic Ingestion: A Data Shape Evolving

The `semantic-ingestion` pattern illustrates how a data shape converges through the lifecycle. The need is simple: "we need to ingest documents into the knowledge base." The shape of the solution evolves through three stages, each building on the last.

#### Stage 1: ETL (3P) — The Initial Shape

The starting shape is Extract-Transform-Load. It's the consensus pattern for moving data between systems: pull data from a source, apply transformation logic, load it into a destination. ETL fits the domain (data processing), implies capabilities (extraction, transformation, loading), and is recognizable (any data engineer knows what ETL means).

The data shape at this stage: **source → transform → destination.** Linear. The pipeline's job is to get data from A to B, applying rules along the way. Byproducts of transformation — intermediate formats, classification decisions, validation results — are discarded or logged. They aren't part of the shape.

#### Stage 2: Medallion Architecture (3P) — Staged Refinement

Medallion Architecture adopts ETL and adds structure to the transformation. Instead of a single transform step, data passes through tiers of increasing curation:

- **Bronze** — raw ingestion, minimal transformation
- **Silver** — cleaned, validated, structured
- **Gold** — enriched, business-ready, queryable

The data shape changes: **source → Bronze → Silver → Gold.** Still linear, but now each stage has a defined quality level. The Medallion pattern reshapes ETL by making the transformation stages explicit and named. The implied capabilities expand: not just "transform data" but "curate data through defined quality tiers."

The byproducts are still incidental. The Bronze layer exists to feed Silver. Silver exists to feed Gold. The intermediate states serve the pipeline, not the consumer.

#### Stage 3: Semantic Ingestion (1P) — The Shape Shift

The 1P innovation reshapes the pattern fundamentally. The insight: **every byproduct of ingestion is a knowledge artifact.**

When a document is ingested, the pipeline produces classifications, detected entity relationships, coherence scores, embeddings, heading hierarchies. In ETL or Medallion, these are intermediate processing artifacts — useful during transformation, discarded after. In semantic ingestion, they become first-class queryable outputs.

The data shape changes from linear to radial:

```text
ETL/Medallion:          Semantic Ingestion:
                                    ┌── classifications
source → transform → output    source → transform ─┼── embeddings
                                    ├── detected edges
                                    ├── coherence scores
                                    └── enriched entity
```

The implied capabilities shift accordingly. ETL implies extraction, transformation, loading. Medallion adds staged curation. Semantic ingestion implies all of these plus: classification persistence, edge detection, embedding generation, coherence scoring at ingest time. The `ingestion-pipeline` capability in the registry maps to all three patterns — ETL, Medallion, and Semantic Ingestion — because the shape evolved through all three.

#### What Changed and What Didn't

The pattern entity persisted across all three stages. The domain (data processing) didn't change. The fitness criteria were satisfied at every stage — each shape fits the domain, implies capabilities, and is recognizable. What evolved was the data shape itself:

| Stage | Shape | Byproducts | Implied capabilities |
|-------|-------|------------|---------------------|
| ETL (3P) | Linear: source → transform → destination | Discarded | Extraction, transformation, loading |
| Medallion (3P) | Staged: Bronze → Silver → Gold | Serve the pipeline | + staged curation, quality tiers |
| Semantic Ingestion (1P) | Radial: every stage produces artifacts | First-class knowledge | + classification, edge detection, embedding, coherence scoring |

The 3P foundations provided the consensus shapes. The 1P innovation reshaped the pattern beyond consensus — "byproducts are artifacts" is not something ETL or Medallion says. It's a synthesis that emerges from applying those patterns to a domain (knowledge management) where the intermediate representations carry as much meaning as the final output.

The SKOS lineage records this evolution: `semantic-ingestion` extends both `etl` and `medallion-architecture`. The links aren't just metadata — they're the search history. They record which shapes were tried, which were kept, and how the current shape was derived.

---

## Summary

A pattern is a data shape — an evolving semantic structure that converges toward the ideal approach for a domain need. At any point in time, it fits a domain, implies capabilities, and is recognizable. These are the fitness criteria. The lifecycle is the search process that reshapes the pattern toward better fit.

The power isn't in the definition. It's in what the definition enables:

- **Reference architecture** — patterns as portable building blocks for modeling any company's domain
- **Coherence measurement** — patterns as the "should be" baseline against which reality is measured
- **AI alignment** — patterns as the semantic compression layer that makes architecture legible to agents
- **The Semantic Optimization Loop** — patterns as the prescriptive force, coherence as the evaluative force, optimization as the cycle that makes systems converge toward their intended design

The formal specification lives in [UBIQUITOUS_LANGUAGE.md](https://github.com/semops-ai/semops-core/blob/main/schemas/UBIQUITOUS_LANGUAGE.md). The strategic design lives in [STRATEGIC_DDD.md](https://github.com/semops-ai/semops-core/blob/main/docs/STRATEGIC_DDD.md). This document is the story that connects them.



---

## Related Links

### Core Concepts

- [Pattern Operations](pattern-operations.md) - Promotion loop, optimization cycle
- [Working with Patterns](working-with-patterns.md) - Finding, sizing, adopting, and evolving patterns
- [Provenance and Lineage](provenance-lineage-semops.md) - Provenance/lineage as coherence mechanism
- [Stable Core, Flexible Edge](stable-core-flexible-edge.md) - Evolution principle
- [Data Shapes](data-shapes.md) - Edge experimentation mechanism

### Architecture

- [SemOps Aggregate Root](semops-aggregate-root.md) - Why pattern is the aggregate root
- [Patterns and Bounded Contexts](../EXPLICIT_ARCHITECTURE/patterns-and-bounded-contexts.md) - DDD integration
- [Domain-Driven Design](../EXPLICIT_ARCHITECTURE/domain-driven-design.md) - Foundation patterns

### Implementation & Examples

- [Domain Pattern Examples](../../Publicv1_Supplemental/examples/domain-pattern-examples.md) - W3C mappings, industry alignment, emergence narrative
- [3P Domain Patterns](../../Publicv1_Supplemental/examples/3p-domain-patterns.md) - SemOps implementation example
- [Semantic Optimization Implementation](semantic-optimization-implementation.md) - Technical realization
