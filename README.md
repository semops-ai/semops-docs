# semops-docs

This repository contains the theory, frameworks, and research behind [Semantic Operations (SemOps)](https://semops.ai) — a practical framework for aligning technology and organization to benefit from data, AI, and agentic systems. It includes foundational research, the three-pillar framework (Strategic Data, Explicit Architecture, Semantic Optimization), and supporting analysis.

> For users of [Claude Code](https://claude.ai/code) or other AI coding assistants, the sections below provide agent context for working with this repository.

---

## Role in Global Architecture

**Bounded Context:** Documents/Theory

```text
semops-dx-orchestrator [PLATFORM/DX] ─────► Distributes templates to all repos
 │
 └── semops-core [MODEL] ─► Owns schema, knowledge graph
       │
       └── semops-docs [DOCUMENTS] ◄── You are here
             │
             └── Theory, implementation docs, knowledge content
```

**What This Repo Owns:**

- Documentation content for knowledge graph
- First principles, frameworks, and theory documents
- AI transformation and semantic operations concepts

**Coordinates With:**

- `semops-core` - Schema definitions (UBIQUITOUS_LANGUAGE.md)
- `semops-dx-orchestrator` - Process templates and tooling
- `semops-publisher` - Content publishing workflow, style guides

## Repository Structure

```
RESEARCH/                              # Research and foundations
├── CURRENT_CONTEXT/                   # Current AI/data landscape analysis
│   └── AI_TRANSFORMATION/             # AI transformation research
└── FOUNDATIONS/                        # Theoretical foundations
    ├── semantic-funnel.md             # Core mental model
    ├── dikw-theory-comparison.md      # Knowledge hierarchy analysis
    └── ...

SEMANTIC_OPERATIONS_FRAMEWORK/         # The SemOps framework
├── STRATEGIC_DATA/                    # Pillar 1: Data as strategic asset
├── EXPLICIT_ARCHITECTURE/             # Pillar 2: Strategy encoded in systems
└── SEMANTIC_OPTIMIZATION/             # Pillar 3: Pattern-driven operations
```

## Document Creation and Editing

Style guides are maintained in **semops-publisher**:

- [technical.md](https://github.com/semops-ai/semops-publisher/blob/main/style-guides/technical.md) - Writing voice for technical docs

### Naming Conventions

- **Folders**: UPPERCASE with underscores (`FIRST_PRINCIPLES/`, `SEMANTIC_OPERATIONS/`)
- **Files**: kebab-case (`semantic-coherence.md`, `what-is-understanding.md`)
- **Root files**: UPPERCASE (`README.md`)

### Document Structure

1. YAML frontmatter (optional but recommended for core concepts)
2. H1 title (ONE per document)
3. Brief definition (1-2 sentences)
4. `## Overview` section
5. Major sections separated by `---` horizontal rules
6. `## Related Links` section at end

### Related Links Format

Two layers of relationships:

1. **Frontmatter `relationships`** - Machine-readable edges for knowledge graph
2. **Markdown "Related Links" section** - Human-readable navigation

```markdown
## Related Links

### Citations
- [Title](URL) - 3P authoritative sources
```

## Related Repositories

- [semops-core](https://github.com/semops-ai/semops-core) - Schema, knowledge graph, semantic operations
- [semops-dx-orchestrator](https://github.com/semops-ai/semops-dx-orchestrator) - Platform tooling, process docs
- [semops-publisher](https://github.com/semops-ai/semops-publisher) - Content publishing workflow, style guides
