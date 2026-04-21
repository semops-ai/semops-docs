# Spec-Driven Design

> Spec-driven design treats a structured specification, not documentation, as the primary artifact that drives implementation. SemOps extends this by creating a specification engine that creates a governed spec object, evaluates it, validates lifecycle, and tracks provenance.

---

## Overview

SemOps already treats domain artifacts as governed data: [Patterns](semantic-patterns.md) have provenance, [Capabilities](semantic-patterns.md#capabilities-the-atoms) have lineage, lifecycle states are validated against structural evidence. [Governance as Strategy](governance-as-strategy.md) makes the case that provenance and lineage are not compliance overhead but strategic infrastructure.

Spec-driven design extends this same discipline to coordination. Project specs, acceptance criteria, dependencies, and lifecycle transitions become data objects governed by the specification engine, with the same rigor applied to Patterns and Capabilities. The spec declares what must be true. The engine checks whether it holds. The gap between declared and actual is surfaced as a finding, not buried in a status meeting.

The principle has deep roots: Design by Contract (Meyer), Contract-First API Design (OpenAPI), Behavior-Driven Development (Cucumber), formal specification (TLA+/Alloy), Model-Driven Architecture, and Domain-Driven Design (Evans) all share a structural insight: **when the specification is precise enough to evaluate, the distance between intent and implementation becomes measurable.** SemOps extends this insight in its own direction: the specification engine validates domain artifacts, project artifacts, and the relationships between them with the same model.

For the specification engine implementation, see [Semantic Optimization Implementation](semantic-optimization-implementation.md). For Pattern definitions and lifecycle, see [Semantic Patterns](semantic-patterns.md) and [Pattern Operations](pattern-operations.md).

---

## Specifications as Governed Data

The core move is treating specifications the way [Governance as Strategy](governance-as-strategy.md) treats data: as objects with provenance, lineage, lifecycle, and validation.

In conventional coordination, a project spec is a document. Someone writes it, someone else reads it, and the distance between the two interpretations is invisible. Tickets derived from the spec carry fragments of context, interpreted independently, reassembled hoping the pieces fit. Status is declared on a board. Nothing validates that the declaration is honest.

In SemOps, a project spec is a data object with machine-readable structure:

- **Provenance**: where did this spec come from? Which Patterns does it reference? Which design docs and ADRs informed it?
- **Lineage**: what happened to this spec as it moved through the system? Which acceptance criteria were met? Which dependencies resolved?
- **Lifecycle**: what is the validated state of this artifact? Not "what does the board say" but "what does the evidence show?"
- **Validation**: the specification engine evaluates the spec against typed predicates, the same way it evaluates Patterns and Capabilities

This is the same provenance/lineage discipline described in [Governance as Strategy](governance-as-strategy.md), applied one level up. Data governance asks "where did this data come from, and what happened to it?" Spec-driven design asks the same question about coordination artifacts: where did this project definition come from, what are its dependencies, and is its claimed status honest?

### The Specification Engine

The specification engine (`specifications.yaml`) already governs domain artifacts with 44+ specifications across classification, validation, quality, and coherence types. It is entity-type-agnostic by design: the `Specification` base class evaluates any entity against a predicate and returns a uniform result.

Extending it to project coordination means:

| Domain governance (today) | Project governance (extension) |
| ------------------------- | ------------------------------ |
| "Every Capability needs a bounded context" | "Every project spec needs a verifiable outcome" |
| "No phantom Pattern references" | "No acceptance criteria pointing to nonexistent issues" |
| "Pattern definitions must be self-contained" | "Project dependencies must be resolved before work starts" |
| "Lifecycle field matches actual state" | "Criteria lifecycle matches linked issue state" |

Same engine. Same predicate model. Same invocation modes (pre_condition, batch, on_demand). Same fix-inline principle: mechanical fixes applied immediately, judgment flagged for human review. New entity types, new specifications.

---

## The Pattern as Spec Boundary

The central question in spec-driven design is: what defines the boundary of a spec?

In SemOps, the answer is the [Pattern](semantic-patterns.md). A Pattern is a named abstraction that carries implicit knowledge: expected Capabilities, presumed complexity, cost, interconnectedness with other concerns. "CRM" is a Pattern. Everyone in the room nods when you say it. But ask five people what CRM includes and you get five different answers. The name carries all of this implicit knowledge, but none of it is explicit until you unpack it.

**Unpacking is decomposition.** When you decompose a Pattern, you discover what is actually inside: the Capabilities it requires, each with intrinsic properties (tier, cognitive load, infrastructure binding, lineage). "CRM" decomposes into contact management, pipeline tracking, forecasting, activity logging, and so on. These Capabilities are the atoms, standardized parts that exist in the world, [discovered through observation](working-with-patterns.md), not invented for this project.

This makes the Pattern a natural spec boundary:

- **It has a name** that carries domain meaning, both humans and LLMs recognize it
- **It has contents** that can be enumerated, the Capabilities discovered through decomposition
- **It has boundaries**, what is inside and what is outside
- **It is non-self-defining**, you must decompose it to know what it actually contains, which forces explicit, honest scoping
- **Its contents have intrinsic properties**, tier, cognitive load, infrastructure binding are discoverable facts, not assigned estimates

The spec boundary is the Pattern boundary. When you define a spec, you are specifying: this Pattern, with these Capabilities, at these lifecycle states. This produces a natural hierarchy:

| Level | What It Is | Example |
| ----- | ---------- | ------- |
| **Spec** | A Pattern being realized | "Parts Catalog Foundation" |
| **Acceptance criteria** | Individual Capabilities reaching target lifecycle | "Specification evaluation is active" |
| **Issue** | Execution handle for moving a Capability forward | "Move glossary enforcement from planned to active" |

Not all work maps to a single Pattern. Analysis of SemOps projects reveals four categories, but the spec does not need to self-classify. The coverage data (which Patterns and Capabilities a spec touches) decides:

| Category | Coverage Signature |
| -------- | ------------------ |
| **Pattern realization** | 1 Pattern; Capabilities are a subset of its decomposition |
| **Concern coordination** | 2+ Patterns; Capabilities span Pattern boundaries |
| **Engagement instance** | 1 Pattern with `relationship: instantiates`; Capabilities inherited from base |
| **Infrastructure enablement** | 0 Patterns; implementation-type Capabilities with `enables` relationships |

**Start with a single Pattern.** If the work does not fit, that is a signal worth examining: either the Pattern boundaries need revision, there is a new Pattern waiting to be discovered, or the work is genuinely cross-cutting. Cross-cutting is a finding, not a starting point.

### DDD Alignment

For those familiar with [Domain-Driven Design](../EXPLICIT_ARCHITECTURE/domain-driven-design.md), the mapping is direct:

| Concept | DDD | SemOps |
| ------- | --- | ------ |
| **Boundary** | Bounded Context | Pattern |
| **Internal units** | Aggregates / Entities | Capabilities |
| **Invariants** | Aggregate rules | Specification engine predicates |
| **Boundary relationships** | Context Map | Spec dependencies |
| **Language** | Ubiquitous Language | Pattern name + Capability names |
| **Validation** | Aggregate enforces rules | Spec engine evaluates lifecycle + integrity |

DDD explains _why_ boundaries exist. The Pattern/Capability model explains _what is inside_. The specification engine adds what neither provides on its own: **lifecycle validation**. DDD does not tell you whether your Bounded Context is actually implemented. The Pattern model does not tell you whether its Capabilities have infrastructure bindings. The spec engine validating lifecycle across all of them is what closes the loop.

---

## Lifecycle Validation

A specification is only trustworthy if its claims are honest. This is where spec-driven design connects directly to [Governance as Strategy](governance-as-strategy.md): provenance and lineage tracking are the mechanisms that make lifecycle claims verifiable rather than aspirational.

Every artifact in the system has a lifecycle state: **planned, draft, in_progress, active, retired.** Unlike status (which can be declared by anyone at any time), lifecycle is spec-engine-validated against structural evidence:

| Lifecycle | Meaning | What the Spec Engine Checks |
| --------- | ------- | --------------------------- |
| **planned** | Declared intent, no implementation | Artifact exists in registry. Not wired to operational pathways. |
| **draft** | Design in progress | Structural outline exists. |
| **in_progress** | Implementation underway | Implementation artifact exists (code, infrastructure binding, structured frontmatter). |
| **active** | Operational baseline | Dependencies are at least in_progress. Operational wiring exists. |
| **retired** | Intentionally decommissioned | No active artifacts depend on this. |

Lifecycle transitions are specification-governed:

- A specification cannot be `active` if its infrastructure dependencies are not available
- A Capability cannot be `active` if its `delivered_by` repo does not have the implementation
- A project spec cannot be `active` if its dependency specs are still `planned`
- Acceptance criteria cannot be `done` if the linked issue is still open

This is data governance applied to coordination artifacts. The same question Governance as Strategy asks about data ("is this dataset's claimed status honest?") is asked about every spec, every criterion, every dependency. The specification engine is the validator, and it must also validate itself.

---

## The Four Stages

The `spec-driven-design` pattern decomposes into four stages: **specify, evaluate, transform, govern.** SemOps already practices all four, though not all are equally mature.

```text
SPECIFY ------> EVALUATE ------> TRANSFORM ------> GOVERN
declare what    check whether    derive work       maintain integrity
must be true    it holds         from the gap      over time
```

| Stage | What Happens | SemOps Equivalent |
| ----- | ------------ | ----------------- |
| **Specify** | Declare contracts, preconditions, invariants, acceptance criteria | `/classify`, `/predict`, structured frontmatter on project specs |
| **Evaluate** | Check whether specs hold, surface violations, measure gaps | Specification engine (`specifications.yaml`), `/pattern-audit` |
| **Transform** | Derive implementation from spec, generate work units, create issues | `/synthesize`, `/derive`, `/project-create` |
| **Govern** | Maintain spec integrity over time, validate lifecycle transitions | `/issue`, `/project-review`, `/status` |

SemOps is strongest in **evaluate** (the specification engine is operational) and **govern** (lifecycle validation, pattern governance). The **specify** stage (declaring contracts) and **transform** stage (deriving implementation from specs) are where the most active development is happening.

These four stages mirror the SemOps pipeline: classify + predict maps to specify, the specification engine maps to evaluate, synthesize + derive maps to transform, and the lifecycle/issue machinery maps to govern. This is not analogy: it is the same pattern, applied to itself.

---

## What This Replaces

Conventional project coordination decomposes work into stories, features, and tasks. These units have no intrinsic domain meaning. A "story" varies by interpreter. An "epic" has no invariants. Story points measure effort, not meaning or risk.

Three structural problems:

1. **Meaning is destroyed during decomposition.** A coherent conceptual model is broken into hundreds of tickets, each interpreted independently, then reassembled hoping the pieces fit.
2. **The process tracks activity, not meaning.** Velocity, burndown, story points encode none of the semantic intent of the work.
3. **The consumer mismatch.** These units were designed for human developers who carry architectural intent in their heads. AI agents consuming "As a user, I want to search for a product" will produce a technically plausible implementation of a semantically hollow statement: the same failure mode as a human with minimal domain context, but faster.

Spec-driven design addresses all three: decomposition into Capabilities preserves meaning (Capabilities have intrinsic properties), the specification engine tracks semantic health (not just completion status), and structured specs are agent-consumable by construction.

| Conventional | Spec-Driven | Why |
| ------------ | ----------- | --- |
| Epics and detailed stories | Structured specs with Pattern boundaries | Spec carries architectural intent that epics try and fail to capture |
| Board as source of truth | Board as read model, spec as source of truth | Status derived from spec completion, not manually maintained |
| Tickets carry all context | Tickets are execution pointers into the spec | "Implement Section 3 of spec X" rather than carrying all context |
| Sequencing lives on the board | Sequencing lives in the spec | Dependencies and gates are specified, not arranged on a Kanban |

---

## Relationship to Other Concepts

Spec-driven design is the coordination mechanism that ties together several Semantic Optimization components:

- **[Governance as Strategy](governance-as-strategy.md)** is the foundational principle. Spec-driven design is what governance as strategy looks like when applied to coordination: provenance, lineage, and lifecycle validation on project artifacts, not just data assets.
- **[Semantic Coherence](semantic-coherence.md)** provides the measurement model. Coherence metrics (availability, consistency, stability) are inputs to specification evaluation.
- **[Semantic Patterns](semantic-patterns.md)** provides the unit of meaning. Patterns are spec boundaries; Capabilities are acceptance criteria.
- **[Pattern Operations](pattern-operations.md)** provides the lifecycle machinery. The promotion loop validates that Patterns and Capabilities are real, not aspirational.
- **[Scale Projection](scale-projection.md)** provides the validation technique. Scale projection tests whether specs hold under scaled conditions.
- **[SemOps as Context Engineering](semops-context-engineering.md)** provides the architectural framing. The encoded business is both the agent's memory and the substrate that specs govern.

---

## Sources

The `spec-driven-design` pattern is registered in the parts catalog as a 3P pattern with shape `specify, evaluate, transform, govern`, synthesized from nine external sources (Meyer, OpenAPI, Cucumber, QuickCheck, TLA+/Alloy, MDA, Nygard, GitHub Copilot Workspace, Evans). Full decomposition: 28 capabilities across the four stages.

**Design doc:** [DD-0027: Spec-Driven Semantic Coordination](https://github.com/semops-ai/semops-dx-orchestrator/blob/main/docs/design-docs/DD-0027-spec-driven-semantic-coordination.md)
**Research:** [Spec-Driven Design for Agentic Workflows](https://github.com/semops-ai/semops-dx-orchestrator/blob/main/docs/research/spec-driven-design-for-agentic-workflows.md) (landscape survey)
**Decomposition:** [Spec-Driven Design Decomposition](https://github.com/semops-ai/semops-research/blob/main/docs/research/decompositions/spec-driven-design.md) (semops-research#107)
