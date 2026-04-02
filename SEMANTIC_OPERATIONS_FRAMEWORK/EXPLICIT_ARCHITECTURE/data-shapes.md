---
doc_type: hub

pattern: data-shapes

provenance: 3p

metadata: 
    pattern_type: concept
    brand_strength: low
---

# Data Shape

> A pattern for defining the concrete data structure of an entity or pattern, distinct from the system-wide schema.

**Provenance:** 3p (W3C SHACL, JSON Schema community)
**Derives From:** SHACL, JSON Schema, data modeling practice
**Pattern Registry:** [ai-workflow-kit/schemas/pattern_v1.yaml](https://github.com/semops-ai/ai-workflow-kit/blob/main/schemas/pattern_v1.yaml)

---

## Core Distinction: Shape vs Schema

| Term | Scope | Mutability | Example |
|------|-------|------------|---------|
| **Schema** | Database/system-wide structure | Versioned, migrated | `phase2-schema.sql`, entire database DDL |
| **Shape** | Single entity/pattern's data structure | Immutable value object | What fields a "Pattern" has |

**Analogy:**
- **Schema** is the blueprint for the whole building
- **Shape** is the specification for one type of brick

**In our context:**
- `ike-semantic-ops` owns the **schema** (`phase2-schema.sql`, all tables)
- Each **pattern** has a **shape** (its specific data structure within that schema)

When we say `shape: TBD` on a pattern, we mean "the concrete attributes this pattern contributes to the schema are not yet defined."

---

## Industry Usage of "Shape"

### 1. Semantic Web / Knowledge Graphs (W3C SHACL)

[W3C SHACL](https://www.w3.org/TR/shacl/) (Shapes Constraint Language) is the primary standard.

**Key characteristics:**
- Shapes describe **constraints**, not logical assertions (unlike OWL)
- A shape is "enough information to identify a particular pattern"
- **Closed World Assumption** - aligns with business expectations (vs OWL's Open World)
- Can validate data AND model data (dual purpose)
- Works across RDF, JSON-LD, and other formats

From [Ontotext](https://www.ontotext.com/knowledgehub/fundamentals/what-is-shacl/):
> "SHACL can be used to describe the structure of data – be it stored in RDF or JSON or similar formats."

From [TopQuadrant](https://archive.topquadrant.com/shacl-the-next-generation-data-modeling-language/):
> "SHACL can be used as a modeling language, and not only as a validation language."

**SHACL vs OWL/RDFS:**
- SHACL contains metadata about datasets but serves a different purpose than RDFS and OWL
- OWL enables **inferencing**; SHACL enables **validation**
- SHACL assumes "Closed World" - what's not stated is false
- OWL assumes "Open World" - what's not stated is unknown

### 2. Database / Data Modeling

From [Ted Neward's "The Shapes of Data"](https://blogs.newardassociates.com/blog/2025/the-shapes-of-data.html):

There are only a few fundamental **data shapes**:
- **Relational** - tables with foreign keys
- **Object** - nested structures with references
- **Graph** - nodes and edges
- **Document** - self-contained hierarchical records

The "shape" isn't always how data is stored on disk, but how it's **conceptually modeled** and understood by the database engine.

### 3. Data Analysis / Visualization

From [FILWD](https://filwd.substack.com/p/shape-the-data-shape-the-thinking-10a):
> "Different combinations of variables produce different 'data shapes,' i.e., relationships, comparisons, and trends."

> "Selection and aggregation determine the shape of the data, and the shape of the data determines the focus of our analysis and the messages we communicate."

Shape here means: how you select and aggregate data determines what patterns you can see.

### 4. Platform Modeling (ThingWorx/IoT)

From [PTC ThingWorx](https://support.ptc.com/help/thingworx/platform/r9/en/ThingWorx/Help/Composer/DataShapes.html):
> "A Data Shape is a named set of field definitions and related metadata."

### 5. Common Data Model (Microsoft)

From [Microsoft CDM](https://learn.microsoft.com/en-us/common-data-model/sdk/logical-definitions):
> "An entity describes the structural shape and semantic meaning for records of data."

> "Standard traits in the Common Data Model define data formats, data shapes, usage guidance and restrictions, semantic meanings, and structural information."

### 6. Statistics

In statistical analysis, "shape" refers to distribution characteristics:
- **Symmetry** - left/right mirror images
- **Skewness** - asymmetry direction
- **Kurtosis** - tail weight / peakedness

---

## SemOps Use of Shape

### SHACL Implementation via JSONB

Our schema implements the SHACL pattern through **JSONB metadata columns** with **JSON Schema validation**. This gives us the flexibility of schema-less storage with the validation benefits of SHACL.

**Where Shape is applied:**

| Table | Column | JSON Schema | Purpose |
|-------|--------|-------------|---------|
| `entity` | `metadata` | `content_metadata_v1.json` | Content-specific attributes |
| `edge` | `metadata` | (flexible) | Relationship properties |
| `surface` | `metadata` | (per-platform) | Platform configuration |
| `delivery` | `metadata` | (flexible) | Delivery context |

**Why this is SHACL:**

1. **Closed World Assumption** - We validate what's defined, reject undefined
2. **Constraints, not ontology** - Metadata schemas define "what must be" not "what could be"
3. **Validation + Modeling** - Same schema validates input AND documents structure
4. **Format-agnostic** - Works whether stored as JSONB, exported as JSON-LD, or displayed as forms

**Example: Entity Metadata Shape**

```json
{
  "type": "object",
  "properties": {
    "word_count": { "type": "integer" },
    "reading_time_minutes": { "type": "integer" },
    "language": { "type": "string", "pattern": "^[a-z]{2}$" }
  },
  "additionalProperties": false  // Closed World
}
```

This JSON Schema IS our SHACL shape - it constrains what fields an entity's metadata can have.

### Pattern Shape Definition

In `ai-workflow-kit/schemas/pattern_v1.yaml`, each pattern has a `shape` field:

```yaml
pattern:
  id: provenance-first-design
  name: Provenance-First Design (1P/2P/3P)
  provenance: 1p
  derives_from: [prov-o]
  shape: TBD  # Concrete data structure pending
  # ...
```

The shape will define:
- **Fields** - what attributes this pattern contributes
- **Types** - data types for each field
- **Constraints** - validation rules, enums, ranges
- **Relationships** - how it connects to other shapes

### Shape vs Schema in Project Ike

| Aspect | Schema | Shape |
|--------|--------|-------|
| **Scope** | All of `ike-semantic-ops` | One pattern |
| **Owner** | `ike-semantic-ops` | Defined per pattern |
| **File** | `phase2-schema.sql` | `pattern_v1.yaml` |
| **Granularity** | System-wide | Single concern |
| **Evolution** | Migrations | Lineage (`derives_from`) |

### Example: Provenance-First Design Shape (Future)

```yaml
shape:
  fields:
    - name: provenance
      type: enum
      values: [1p, 2p, 3p]
      required: true
  constraints:
    - "3p entities cannot have delivery records"
  contributes_to:
    - table: entity
      column: provenance
```

---

## Why "Shape" Instead of Other Terms

| Term | Why Not |
|------|---------|
| `schema` | Overloaded - means entire database structure |
| `model` | Too generic, also overloaded |
| `type` | Programming language connotation |
| `structure` | Vague |
| `definition` | Could mean documentation |
| `spec` | Abbreviation, less clear |

**"Shape"** is:
- Used by W3C (SHACL) for exactly this purpose
- Implies "what it looks like" without formal ontology baggage
- Works as both noun ("the shape") and verb ("shape the data")
- Familiar to data practitioners across domains

---

## Shapes and Stable Core, Flexible Edge

Data Shapes are the **technical implementation of the flexible edge** in the [Stable Core, Flexible Edge](stable-core-flexible-edge.md) principle:

| Aspect | Schema (Stable Core) | Shape (Flexible Edge) |
| ------ | -------------------- | --------------------- |
| **Scope** | System-wide structure | Single entity/pattern |
| **Mutability** | Versioned, migrated | Immutable value object |
| **Purpose** | Enforce invariants | Test semantic representation |
| **Change cost** | High (migration) | Low (new shape) |

Shapes allow experimentation without commitment — you can test semantic representations at the edge before promoting them to the stable core schema.

---

## Related Concepts

- **[Stable Core, Flexible Edge](stable-core-flexible-edge.md)** - Architectural principle shapes implement
- **[Patterns](../SEMANTIC_OPTIMIZATION/patterns.md)** - Bounded synthesis requiring authorship
- **[Abstractions](abstractions.md)** - Entity and metric abstractions tested via shapes
- **[SKOS](https://www.w3.org/2004/02/skos/)** - Knowledge organization (concepts, labels)
- **[PROV-O](https://www.w3.org/TR/prov-o/)** - Provenance (derivation, attribution)
- **[DDD](https://domainlanguage.com/ddd/)** - Value objects, entities, aggregates

---

## References

- [W3C SHACL Specification](https://www.w3.org/TR/shacl/)
- [SHACL - Wikipedia](https://en.wikipedia.org/wiki/SHACL)
- [What Is SHACL - Ontotext](https://www.ontotext.com/knowledgehub/fundamentals/what-is-shacl/)
- [SHACL: The Next-Generation Data Modeling Language - TopQuadrant](https://archive.topquadrant.com/shacl-the-next-generation-data-modeling-language/)
- [The Shapes of Data - Neward Associates](https://blogs.newardassociates.com/blog/2025/the-shapes-of-data.html)
- [Shape the Data, Shape the Thinking - FILWD](https://filwd.substack.com/p/shape-the-data-shape-the-thinking-10a)
- [Data Shapes - ThingWorx](https://support.ptc.com/help/thingworx/platform/r9/en/ThingWorx/Help/Composer/DataShapes.html)
- [Common Data Model - Microsoft](https://learn.microsoft.com/en-us/common-data-model/sdk/logical-definitions)
- [Connected Data London 2024 - SHACL Masterclass](https://2024.connected-data.london/talks/shacl-masterclass/)
