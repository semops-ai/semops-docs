# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

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

## Session Notes

Document significant work sessions in `docs/session-notes/`:

- **Format:** `YYYY-MM-DD-descriptive-title.md` or `ISSUE-NN-description.md`
- **Index:** Update `docs/SESSION_NOTES.md` with new entries
- **Include:** Issue links, status, phases completed, outcomes
- **When:** Multi-step migrations, cross-repo changes, significant decisions

## Project Overview

Project Ike is a documentation-centric knowledge framework repository exploring AI, data, and semantic understanding. It contains markdown documents, schemas, diagrams, and Python scripts for knowledge graph visualization.

**Key Principle:** Use `gh issue list` to view outstanding work. GitHub issues drive development priorities.

## Project Awareness & Context

- **Read `README.md`** for repo purpose, content, and structure
- **Check `docs/decisions/`** for Architecture Decision Records (ADRs)

## Frontmatter Schema

All content aligns with the global knowledge graph schema.

**Ground truth:** [semops-core UBIQUITOUS_LANGUAGE.md](https://github.com/semops-ai/semops-core/blob/main/schemas/UBIQUITOUS_LANGUAGE.md)

**Local guide:** [FRONTMATTER_GUIDE.md](FRONTMATTER_GUIDE.md)

**Note:** Most fields are auto-derived. Only add frontmatter when you need explicit relationships or content metadata.

## Document Creation and Editing

Style guides are maintained in **semops-publisher**:

- [GITHUB_DOCS_FORMAT.md](https://github.com/semops-ai/semops-publisher/blob/main/GITHUB_DOCS_FORMAT.md) - Document structure, naming, markdown conventions
- [technical.md](https://github.com/semops-ai/semops-publisher/blob/main/style-guides/technical.md) - Writing voice for technical docs

### Naming Conventions

- **Folders**: UPPERCASE with underscores (`FIRST_PRINCIPLES/`, `SEMANTIC_OPERATIONS/`)
- **Files**: kebab-case (`semantic-coherence.md`, `what-is-understanding.md`)
- **Root files**: UPPERCASE (`README.md`, `PLANNING.md`)

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
