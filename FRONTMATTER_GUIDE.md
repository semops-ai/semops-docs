# Frontmatter Guide

Quick guide for adding YAML frontmatter to markdown documentation in this repository.

---

## Frontmatter Rules for This Repo

All documents in this repo have a number of schema attributes in common and therefore don;t need to be included as frontmatter, as the purpose of the frontmatter is ingestion, which will be managed by the [ike-semantic-ops](https://github.com/timjmitchell/ike-semantic-ops). Ingestion will auto-derive the following from the repo and markdown:
### Repo
All documents in this repo are 
- `provenance` = "1p" (except where explicitly edited)
- `content_type`: "github_doc" 
- `media_type`: "text" 
- `language`: "en" 
- `authors` = "Tim Mitchell" (except where explicitly edited)
- `organization`: "TJM Consulting" # Institution/company
- `platform`: "github" # Publication platform: youtube, 
- `channel`: "timjmitchell" 

### Markdown
- **Title** - from first H1 heading or filename
- **Description** - from first paragraph
- **Category** - from folder name (FIRST_PRINCIPLES → first-principles)
- **Tags** - from directory and keyword extraction
- **Provenance** - defaults to 1p (first-party/owned)
- **Content type** - defaults to "article"

**Just write good markdown and the system handles the rest.**

---

## Minimal Example (Relationships Only)

```yaml
---
relationships:
 - predicate: depends_on
 target_file: dikw.md
 strength: 0.9

 - predicate: part_of
 target_file: semantic-operations.md
 strength: 1.0
---

# Your Document Title

Your content starts here...
```

**That's it!** Everything else auto-derived.

---

## Optional Fields (If Needed)

```yaml
---
# Custom entity ID (otherwise auto-generated)
entity_id: semantic-coherence

# Override auto-derived title/description
title: Custom Title
description: Custom description here

# Semantic classification (for knowledge graph queries)
metadata:
 semantic_type: concept # concept | framework | methodology | pattern | guide
 abstraction_level: intermediate # foundational | intermediate | advanced

 # Taxonomy (SKOS hierarchy)
 broader_concepts: [semantic-operations]
 narrower_concepts: [semantic-drift, coherence-audit]

# Relationships (W3C PROV-O based)
relationships:
 - predicate: depends_on # Prerequisite knowledge
 target_file: dikw.md
 strength: 0.9

 - predicate: part_of # Hierarchical component
 target_file: semantic-operations.md
 strength: 1.0

 - predicate: related_to # Associated concept
 target_file: domain-driven-design.md
 strength: 0.7
---
```

---

## Relationship Predicates

| Predicate | Use For |
| -------------- | --------------------------------------------- |
| `depends_on` | Prerequisite knowledge (must read this first) |
| `part_of` | Hierarchical component (chapter in book) |
| `related_to` | Associated concept (helpful but not required) |
| `cites` | External reference or citation |
| `derived_from` | Transformed/adapted from source |

**Strength values:**
- `1.0` - Essential relationship
- `0.8-0.9` - Strong relationship
- `0.5-0.7` - Moderate relationship
- `0.3-0.4` - Weak relationship

---

## Complete Schema Reference

For full schema details, see the [ike-semantic-ops repository](https://github.com/timjmitchell/ike-semantic-ops):

- [UBIQUITOUS_LANGUAGE.md](https://github.com/timjmitchell/ike-semantic-ops/blob/main/schemas/UBIQUITOUS_LANGUAGE.md) - Entity model and schema
- [doc-style.md](https://github.com/timjmitchell/ike-semantic-ops/blob/main/docs/domain-patterns/doc-style.md) - Documentation conventions
- [edge-predicates.md](https://github.com/timjmitchell/ike-semantic-ops/blob/main/docs/domain-patterns/edge-predicates.md) - Complete predicate reference

---

## Quick Start Template

See [frontmatter-template.md](docs/templates/frontmatter-template.md) for copy-paste examples.
