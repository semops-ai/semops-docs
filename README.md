
# semops-docs

Framework theory, concept documentation, and research foundations for [Semantic Operations (SemOps)](https://semops.ai). This is the "why" and "what" behind the implementation in the [semops-ai](https://github.com/semops-ai) organization.

## What This Is

This repo owns the theoretical foundations — the frameworks, research, and concept definitions that the implementation repos exhibit. When other repos mention a SemOps concept (Semantic Coherence, Patterns, the Semantic Funnel), links will resolve here.

The documentation includes several concept documents organized by directory into [Research](RESEARCH/) and three framework pillars: [Strategic Data](SEMANTIC_OPERATIONS_FRAMEWORK/STRATEGIC_DATA/), [Symbiotic Architecture](SEMANTIC_OPERATIONS_FRAMEWORK/SYMBIOTIC_ARCHITECTURE/), and [Semantic Optimization](SEMANTIC_OPERATIONS_FRAMEWORK/SEMANTIC_OPTIMIZATION/). Documents are written for a mixed audience: technical enough for engineers to map concepts to architecture, accessible enough for leadership to understand the strategic rationale.

This repo is not an implementation. There is no code to run, no services to deploy, no dependencies to install. It is structured Markdown with a reading order.

Part of the [semops-ai](https://github.com/semops-ai) organization. For system-level architecture and how all repos relate, see [semops-dx-orchestrator](https://github.com/semops-ai/semops-dx-orchestrator).

**What this repo is NOT:**

- Not implementation documentation — for how the system is built, see the implementation repos ([semops-core](https://github.com/semops-ai/semops-core), [semops-dx-orchestrator](https://github.com/semops-ai/semops-dx-orchestrator))
- Not a blog or narrative content — for published articles and thought leadership, see [semops.ai](https://semops.ai) and [timjmitchell.com](https://timjmitchell.com)
- Not a textbook — concepts are explained at the depth needed to inform architecture and strategy decisions, not for academic completeness

## Documents as Operational Knowledge

These documents are not just files to read — they are structured content designed to be machine-readable and composable. Each document carries YAML frontmatter with metadata (content type, provenance, related concepts) and follows a convention where each H2 section is a self-contained unit of meaning.

The [knowledge base](https://github.com/semops-ai/semops-core) ingests these documents using an atom-hub decomposition model. Each concept document (H1) is a hub — the identity of the concept. Each H2 section within it is an atom — independently embeddable, retrievable, and linkable. When the knowledge base processes this repo, it decomposes documents into atoms, embeds them, and discovers relationships between atoms across documents through structural analysis (markdown cross-references), semantic similarity (vector embeddings), and content analysis.

This means a query to the knowledge base doesn't return whole documents. It assembles a response from atoms across multiple concept hubs, guided by the edges between them. The documents are the source of truth; the knowledge graph is a derived, queryable view of the same content.

The practical consequence for authors: each H2 section should be independently coherent. It should make sense when retrieved on its own, outside the context of the surrounding document. Cross-references between sections and documents create the navigational structure that both human readers and the knowledge base use.

## How to Read This Repo

**If you want the executive summary:**
Start with the [framework introduction](SEMANTIC_OPERATIONS_FRAMEWORK/README.md), which covers what SemOps is, the core value proposition, and how the three pillars fit together.

**If you want the foundational mental model:**
Read [Semantic Funnel](RESEARCH/FOUNDATIONS/semantic-funnel.md) first. It introduces the three entities (Objects, Agents, Rules) and the knowledge process (Data → Information → Knowledge → Understanding → Wisdom) that ground the entire framework.

**If you want to understand one pillar in depth:**
Each pillar has its own README that serves as a reading guide with recommended order:
- [Strategic Data](SEMANTIC_OPERATIONS_FRAMEWORK/STRATEGIC_DATA/README.md) — Data as a first-class strategic asset
- [Symbiotic Architecture](SEMANTIC_OPERATIONS_FRAMEWORK/SYMBIOTIC_ARCHITECTURE/README.md) — Encoding strategy into system structure
- [Semantic Optimization](SEMANTIC_OPERATIONS_FRAMEWORK/SEMANTIC_OPTIMIZATION/README.md) — Measuring and governing shared meaning

**If you're coming from an implementation repo:**
You likely followed a concept link. Each concept document is self-contained — read it, then follow its cross-references to related concepts. The pillar READMEs provide broader context if you want to understand where the concept fits.

**If you want the research behind the framework:**
The [Research](RESEARCH/) section contains theoretical foundations (DIKW theory, DDD, ontology, cognitive science) and a current-context analysis of AI transformation challenges.

## What's Here

### Research Foundations

| Section | Documents | What It Covers |
| ------- | --------- | -------------- |
| [Foundations](RESEARCH/FOUNDATIONS/) | 6 | Semantic Funnel (core mental model), DIKW theory comparison, cognition, data theory, semantic theory, understanding |
| [Current Context](RESEARCH/CURRENT_CONTEXT/) | AI transformation meta-analysis | Why organizational AI adoption isn't meeting ROI expectations, and what the gap reveals about meaning |

### Semantic Operations Framework

| Pillar | Documents | What It Covers |
| ------ | --------- | -------------- |
| [Strategic Data](SEMANTIC_OPERATIONS_FRAMEWORK/STRATEGIC_DATA/) | 16 | Data as strategic asset, four data system types, governance as strategy, data physics, analytics evolution |
| [Symbiotic Architecture](SEMANTIC_OPERATIONS_FRAMEWORK/SYMBIOTIC_ARCHITECTURE/) | 15 | Domain-Driven Design, explicit architecture, AI-ready systems, semantic flywheel, agentic coding, stable core / flexible edge |
| [Semantic Optimization](SEMANTIC_OPERATIONS_FRAMEWORK/SEMANTIC_OPTIMIZATION/) | 7 | Semantic coherence (measurement + governance), Patterns as semantic units, pattern operations, compression |

### How Concepts Connect to Implementation

The three pillars map to implementation repos:

| Pillar | Primary Implementation |
| ------ | --------------------- |
| Strategic Data | [semops-core](https://github.com/semops-ai/semops-core) — quality tiers, knowledge base, coherence measurement |
| Symbiotic Architecture | [semops-dx-orchestrator](https://github.com/semops-ai/semops-dx-orchestrator) — DDD repo structure, Pattern as aggregate root, integration patterns |
| Semantic Optimization | [semops-publisher](https://github.com/semops-ai/semops-publisher) — edit capture for style learning, content coherence |

[semops-data](https://github.com/semops-ai/semops-data) implements measurement capabilities described across all three pillars. [semops-sites](https://github.com/semops-ai/semops-sites) implements the delivery surface where concepts become published web content.

## Status

| Section | Maturity | Notes |
| ------- | -------- | ----- |
| Semantic Funnel | Stable | Core mental model, foundational to everything else |
| Research Foundations | Stable | Theoretical grounding complete |
| Current Context | Stable | AI transformation analysis |
| Strategic Data | Stable | 16 concept documents |
| Symbiotic Architecture | Stable | 15 concept documents |
| Semantic Optimization | Maturing | 7 concept documents, coherence measurement evolving with implementation |

This is a living documentation set. Concepts stabilize as they are validated through implementation in the semops-ai repos. Documents are updated when implementation reveals new understanding or when the framework's language evolves.

## References

### Related

- **[semops-dx-orchestrator](https://github.com/semops-ai/semops-dx-orchestrator)** — System architecture, cross-repo coordination, and design principles for the implementation
- **[semops-core](https://github.com/semops-ai/semops-core)** — Schema, knowledge graph, and shared infrastructure that implements concepts from this repo
- **[semops-publisher](https://github.com/semops-ai/semops-publisher)** — AI-assisted content creation, where concepts become published content
- **[semops-data](https://github.com/semops-ai/semops-data)** — Data platform implementing measurement and analytics concepts
- **[semops-sites](https://github.com/semops-ai/semops-sites)** — Web surfaces where framework content is published
- **[semops.ai](https://semops.ai)** — Narrative introduction to SemOps for a general audience
- **[timjmitchell.com](https://timjmitchell.com)** — Blog, thought leadership, and project narrative

### Influences

- **[DIKW Hierarchy](https://en.wikipedia.org/wiki/DIKW_pyramid)** — Data → Information → Knowledge → Wisdom knowledge process
- **[Domain-Driven Design](https://www.domainlanguage.com/ddd/)** (Eric Evans) — Bounded contexts, ubiquitous language, aggregate roots
- **[SKOS](https://www.w3.org/2004/02/skos/)** — Simple Knowledge Organization System for concept representation
- **[PROV-O](https://www.w3.org/TR/prov-o/)** — W3C provenance ontology for lineage tracking
- **[Schema.org](https://schema.org/)** — Shared vocabulary for structured data on the web

## License

[MIT](LICENSE)
