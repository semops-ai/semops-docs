# Architecture

> **Repo:** `semops-docs`
> **Role:** Documents/Theory - Theory and implementation documentation
> **Status:** ACTIVE
> **Version:** 1.1.0
> **Last Updated:** 2026-02-06

## Role

Docs-pr is the **theory and documentation** bounded context within SemOps. It contains conceptual documentation, implementation notes, and knowledge framework content that may be published publicly.

## Ownership

**What this repo owns (source of truth for):**

- Theoretical documentation (1P bounded concepts)
- Implementation decision records
- Concept documentation and rendering
- Private notes and drafts
- Knowledge framework exploration
- **SEMOPS_DOCS** - Theory documentation for public semops-docs repo

**What this repo does NOT own (consumed from elsewhere):**

- Schema definitions → semops-core (UBIQUITOUS_LANGUAGE.md)
- Process documentation → semops-dx-orchestrator
- Publishing workflows → semops-publisher
- Style guides → semops-publisher

## Key Components

| Component | Purpose |
|-----------|---------|
| `docs/` | Core documentation and concepts |
| `docs/SEMOPS_DOCS/` | Theory documentation for public publishing |
| `docs/1p-bounded-concepts/` | First-party concept definitions |
| `docs/decisions/` | Architecture Decision Records |
| `FRONTMATTER_GUIDE.md` | YAML frontmatter schema for knowledge graph |

## Public Output

Docs-pr content is published to:

- **semops-docs** (planned) - Public repository for SemOps theory documentation
- **semops.ai** - Framework documentation on semops-sites

## Dependencies

| Repo | What We Consume |
|------|-----------------|
| semops-core | Schema (UBIQUITOUS_LANGUAGE.md), knowledge graph |
| semops-dx-orchestrator | Process docs, global architecture |
| semops-publisher | Style guides (style-guides/technical.md, style-guides/blog.md) |

## Document Standards

### Naming Conventions

- **Folders:** UPPERCASE with underscores (`FIRST_PRINCIPLES/`, `SEMANTIC_OPERATIONS/`)
- **Files:** kebab-case (`semantic-coherence.md`)
- **Root files:** UPPERCASE (`README.md`, `PLANNING.md`)

### Document Structure

1. YAML frontmatter (optional, for knowledge graph integration)
2. H1 title (one per document)
3. Brief definition (1-2 sentences)
4. `## Overview` section
5. Major sections separated by `---` horizontal rules
6. `## Related Links` section at end

### Frontmatter

Documents integrate with SemOps knowledge graph via YAML frontmatter. See [FRONTMATTER_GUIDE.md](../FRONTMATTER_GUIDE.md) for schema.

Ground truth schema: [semops-core UBIQUITOUS_LANGUAGE.md](https://github.com/semops-ai/semops-core/blob/main/schemas/UBIQUITOUS_LANGUAGE.md)

## Data Flows

```text
Concepts/Ideas → semops-docs (documentation) → semops-core (knowledge graph)
 → semops-publisher (publishing)
 → semops-sites (public rendering)
```

## Related Documentation

- [GLOBAL_ARCHITECTURE.md](https://github.com/semops-ai/semops-dx-orchestrator/blob/main/docs/GLOBAL_ARCHITECTURE.md) - System landscape
- [FRONTMATTER_GUIDE.md](../FRONTMATTER_GUIDE.md) - Knowledge graph integration
- [semops-core UBIQUITOUS_LANGUAGE.md](https://github.com/semops-ai/semops-core/blob/main/schemas/UBIQUITOUS_LANGUAGE.md) - Domain definitions
