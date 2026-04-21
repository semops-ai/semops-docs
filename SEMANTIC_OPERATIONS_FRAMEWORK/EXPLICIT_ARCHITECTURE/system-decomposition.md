# System Decomposition

> System decomposition breaks named abstractions such as vendor products, platforms, and design patterns into neutral, functional primitives. It is the mechanism through which SemOps derives Semantic Patterns and Capabilities and the building blocks of Explicit Enterprise.

---

## Decomposition in Explicit Architecture

[Explicit Architecture](explicit-architecture.md) encodes business rules as inspectable, queryable structure. Semantic Patterns and Capabilities are the core artifacts of that structure. System decomposition is how those artifacts are derived.

The derivation works in one direction. A vendor product or technical framework is a composite. Decomposition extracts the neutral functional primitives from that composite, each an Architecture Building Block. Those primitives become capabilities. When capabilities from multiple decompositions are compared and synthesized, patterns emerge.

```text
Vendor Product (SBB / composite)
    |
    +-- Primitive 1 (ABB) --> Capability
    +-- Primitive 2 (ABB) --> Capability
    +-- Primitive 3 (ABB) --> Capability
    +-- ...

Multiple decompositions --> cross-product comparison --> Pattern
```

TOGAF provides the structural frame, but two additional reference systems give primitives a stable identity independent of any vendor's branding:

- **BIZBOK capability naming** uses a business object plus action convention ("Contact Management," "Sales Pipeline Tracking") that names what the primitive does without referencing how any vendor implements it.
- **APQC Process Classification Framework (PCF)** maps each primitive to a business process group (e.g., "3.5 Develop and manage sales plans"). This makes primitives comparable across vendors and domains because they share a common process reference.

Without decomposition, architecture discussions operate on vendor composites ("we use Salesforce," "we run on Shopify"). With decomposition, the same discussions operate on neutral capabilities that can be compared, recomposed, and governed independently of vendor choice.

---

## Two Decomposition Targets

SemOps applies decomposition to two distinct target types. Domain decomposition breaks vendor products into business capabilities. Implementation decomposition breaks technical frameworks and design patterns into platform-neutral technical capabilities. The methodology is structurally the same (extract neutral primitives from a named composite), but the reference frames and classification backbones differ.

### Domain Decomposition: Vendor Products

A vendor product bundles multiple business capabilities under a single brand. Decomposition separates the brand from the function. Each primitive receives a neutral name, a vendor-branded name for traceability, an APQC process context, a description of what it actually does, and a list of open protocols that could serve the same function.

A single primitive from a Salesforce CRM decomposition illustrates the structure:

```yaml
- name: Sales Pipeline Tracking
  branded_name: Opportunities
  apqc_context: "3.5"
  apqc_name: Develop and manage sales plans
  description: >
    Track potential revenue through stages from qualification to close.
    Each opportunity has a value, probability, stage, and close date.
  protocols:
    - SQL
```

"Sales Pipeline Tracking" is the neutral primitive. "Opportunities" is what Salesforce calls it. The APQC reference ("3.5") connects it to a standard business process. The protocol list (SQL) indicates that this function can be served by any relational database with an appropriate schema, not only by Salesforce's proprietary sObject model.

The SemOps decomposition catalog contains over 40 vendor products decomposed in this format, spanning CRM, commerce, ERP, CMS, DAM, orchestration, and other categories. Each decomposition follows the same schema, making cross-vendor comparison mechanical rather than subjective. A CRM primitive from Salesforce can be compared directly to the equivalent primitive from Zoho, Pipedrive, or Attio because all share the same naming convention and process reference.

### Implementation Decomposition: Platforms and Design Patterns

Domain decomposition targets vendor products that serve business functions. Implementation decomposition targets a different set of composites: technical frameworks (Kubernetes, dbt), infrastructure platforms (Backstage, DataHub), and codified design patterns (event sourcing, anti-corruption layer). These composites provide technical capabilities rather than business capabilities, and they require a different classification backbone.

[DD-0021](https://github.com/semops-ai/semops-dx-orchestrator/blob/main/docs/design-docs/DD-0021-implementation-pattern-decomposition-and-classification.md) defines a composite backbone with three dimensions:

| Dimension | What It Classifies | Example Values |
| --------- | ----------------- | -------------- |
| **Technical concern** | What infrastructure problem the pattern solves | state-management, runtime-infrastructure, data-architecture, boundary-integration |
| **Architectural layer** | Where in the stack it operates | in-process, service-boundary, platform-service, design-methodology |
| **Adoption scope** | How broadly adopting it affects the architecture | local, boundary, platform, methodology |

The three dimensions combine to produce an implementation signature. Kubernetes is `runtime-infrastructure x platform-service x platform`. Event sourcing is `state-management x service-boundary x boundary`. The repository pattern is `state-management x in-process x local`. Each signature reveals what the pattern does, where it operates, and what the governance weight of adopting or replacing it is.

Granularity follows technical concern complexity. Kubernetes, a broad infrastructure platform, decomposes into approximately 12 capabilities (container scheduling, service discovery, declarative reconciliation, horizontal scaling, and others). Event sourcing, a focused design pattern, decomposes into 5 (event persistence, state reconstruction, temporal querying, event projection, snapshot optimization). The difference reflects the complexity of the technical concern, not an arbitrary target.

The primitiveness test for implementation patterns is more concrete than for domain patterns. A capability is primitive if the platform exposes it as an independently configurable function with its own API surface, failure mode, and lifecycle. Kubernetes exposes pod scheduling and service discovery as separate API objects with independent failure modes. It does not expose etcd storage as a first-class capability because etcd is absorbed into the control plane. The test is observable: configuration options, documentation structure, and failure independence are documented artifacts.

---

## From Vendor Lock-In to Explicit Enterprise

Decomposition reveals where vendor choice creates dependency. Each decomposition includes two diagnostic fields: lock-in signals and open alternatives.

**Lock-in signals** are specific, concrete vectors that create switching cost. A Shopify decomposition identifies ten distinct signals, including Liquid (a proprietary templating language with no portability), Shopify Functions (checkout extensibility locked to the platform), and metafield schemas (proprietary typed storage with no standard export). A NetSuite decomposition identifies SuiteScript (proprietary server/client scripting), SuiteFlow (vendor-specific automation), and SuiteQL (SQL-like but NetSuite-specific schema). These are not generic complaints about vendor lock-in. They are named, per-product switching costs that decomposition surfaces.

**Open alternatives** identify what neutral stack could serve the same primitives. For Shopify, the catalog names Medusa or Saleor with PostgreSQL, Stripe, and a CDN. For NetSuite, it names ERPNext or Odoo with PostgreSQL and standard integrations. The alternative is not a recommendation. It is an architectural observation: these primitives can be served by standards-based infrastructure.

Individual primitives can also carry an `explicit_alternative` field that maps the specific primitive to a standards-based implementation:

```yaml
explicit_alternative:
  what: PostgreSQL + double-entry accounting library
  standard: Double-entry bookkeeping, GAAP/IFRS chart of accounts standards
  agent_friendly: >
    Standard SQL schema, any tool can read/write,
    no proprietary scripting required
  build_effort: medium
```

This connects directly to the [Explicit Enterprise](explicit-enterprise.md) argument. Explicit Enterprise proposes neutral infrastructure over platform, architecture over integration, and semantic coherence over shortcuts. System decomposition is the methodology that operationalizes that proposition. It answers the question Explicit Enterprise raises: "How do you identify what is neutral versus what is proprietary in a vendor product?" The answer is decomposition. Break the product into primitives, check the protocols, read the lock-in signals, and examine the alternatives. The Explicit Enterprise principle becomes an architectural assessment rather than a philosophical position.

---

## Self-Decomposition

SemOps applies the decomposition methodology to itself. The `semops-platform.yaml` catalog entry treats SemOps as a vendor product and decomposes it using the same schema, naming conventions, and diagnostic fields applied to every other product in the catalog.

The self-decomposition identifies eight lock-in signals, including YAML registry format (SemOps-specific schemas with no interoperability standard), the composite classification backbone (a 1P taxonomy not portable to other systems), the coherence scoring formula, and the agent command vocabulary (slash commands that encode SemOps-specific workflows). It names the open alternative as a combination of TOGAF ADM, DataHub, Great Expectations, Backstage, and OpenLineage, noting that no single tool covers the full SemOps surface.

Self-decomposition serves two functions. First, it validates that the methodology works on meta-infrastructure (a platform whose domain is other companies' domains), not only on application-level products. Second, it makes SemOps's own lock-in vectors explicit and inspectable, applying the same transparency the methodology demands of every other product.

---

## The Codified Methodology

Decomposition is codified as a repeatable, agent-executable process. The `/decompose-vendor` command takes a vendor name as input, researches the product's features and API documentation, extracts neutral primitives with APQC process group mappings, identifies lock-in signals, and writes a catalog YAML file. The command enforces the methodology's constraints: neutral names (not vendor branding), APQC grounding (every primitive maps to a process group), and functional decomposition (by business capability, not by code module or API endpoint).

For implementation patterns, decomposition follows a two-phase process defined in DD-0021. Phase 1 decomposes a single platform or design pattern into capabilities, each receiving a platform-neutral name, a composite backbone classification, and a delivery target (the domain capability it serves). Phase 2 compares capabilities across platforms that address the same technical concern, producing three tiers: universal capabilities (present in all platforms, defining the technical concern), common capabilities (present in most, with varying approaches), and differentiators (platform-specific choices). Phase 2 produces 1P patterns, platform-neutral abstractions composed from capabilities observed across multiple platforms.

The two phases mirror the domain decomposition workflow. Phase 1 is analogous to decomposing a single vendor product. Phase 2 is analogous to comparing decompositions across vendors in the same category to discover what is essential to the domain versus what is vendor-specific. The structural parallelism is intentional: whether decomposing Salesforce or Kubernetes, the method is extract neutral primitives, then compare across implementations to find what is universal.

---

## Related Links

### Framework Context

- [Explicit Architecture](explicit-architecture.md) -- parent pillar
- [Explicit Enterprise](explicit-enterprise.md) -- the argument this methodology operationalizes
- [Patterns and Bounded Contexts](patterns-and-bounded-contexts.md) -- what decomposition produces

### Design and Methodology

- [DD-0021: Implementation Pattern Decomposition and Classification](https://github.com/semops-ai/semops-dx-orchestrator/blob/main/docs/design-docs/DD-0021-implementation-pattern-decomposition-and-classification.md) -- composite backbone, worked examples
- [System Primitive Decomposition](https://github.com/semops-ai/semops-research/blob/main/docs/research/system-primitive-decomposition.md) -- methodology doc
- [Decomposition Catalog Schema](https://github.com/semops-ai/semops-research/blob/main/data/catalogs/decompositions/_schema.yaml) -- YAML schema definition

### Implementation

- [Decomposition Catalog](https://github.com/semops-ai/semops-research/tree/main/data/catalogs/decompositions) -- 40+ vendor product decompositions
- [SemOps Self-Decomposition](https://github.com/semops-ai/semops-research/blob/main/data/catalogs/decompositions/semops-platform.yaml) -- 1P meta-infrastructure decomposition
