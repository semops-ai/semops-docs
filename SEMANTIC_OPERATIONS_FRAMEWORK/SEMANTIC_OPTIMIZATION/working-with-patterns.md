---
doc_type: hub

pattern: working-with-patterns

provenance: 1p

metadata:
    pattern_type: concept
    brand_strength: high
---

# Working with Patterns

> Practical guidance for finding, sizing, adopting, and evolving domain patterns — the judgment-driven work that turns pattern theory into operational reality.

For pattern definitions and taxonomy, see [Patterns](patterns.md). For the promotion loop and optimization cycle, see [Pattern Operations](pattern-operations.md).

## Overview

[Patterns](patterns.md) describes what patterns are. [Pattern Operations](pattern-operations.md) describes the operational machinery — promotion loops, optimization cycles, governance. This document covers the decisions that sit between those two: **how to find the right pattern, how to size, how to adopt, and how to evolve it from a standard baseline toward intentional innovation.**

Each stage has rules and guidelines — where to look, what to avoid, how to size, when to evolve. Judgment is still required to apply them in context, but the guidance here is concrete and opinionated, not abstract. Methods and tooling for pattern-specific research are in development, but applying your own judgment alongside these rules is valuable regardless — it will help you understand your problem and your business more deeply. We are all learning.

The lifecycle follows a natural arc:

```text
Find 3P Pattern → Size It → Adopt It → Use It → Evolve to 1P → Strategic Leverage
```

Pattern types — concept, domain, process, implementation, integration — are described in the [Pattern Taxonomy](patterns.md#pattern-taxonomy). The types that demand the most judgment are **domain** and **concept** patterns, because they encode meaning rather than mechanism. This document focuses primarily on those, with guidance on technical/infrastructure patterns where the rules differ.

---

## Finding 3P Domain Patterns

Starting from zero — you have a capability you need, and you are looking for a pattern to adopt. This is the most important step, because the pattern you choose shapes everything downstream: your architecture, your agents' understanding, your ability to compose and evolve.

### Look in the Domain, Not Your Industry

This one is important. Your architecture and [DDD](../EXPLICIT_ARCHITECTURE/domain-driven-design.md) tell you what problem you are solving. Look at the boundaries and the [bounded contexts](../EXPLICIT_ARCHITECTURE/patterns-and-bounded-contexts.md) — then look for patterns in **that domain**, not necessarily in your industry.

A home cleaning service has a very large domain overlap with delivery services. The operational challenge — "get crews and equipment to customers on schedule" — is a logistics and dispatch problem. The cleaning domain itself has very little to do with the problems in that "bounded context". If you search for "cleaning industry scheduling solutions," you will find narrow, industry-specific tools. If you search for "dispatch and routing optimization," you will find well-established patterns used across delivery, field service, and fleet management — patterns that are far more mature, better documented, and already in AI training data.

The bounded context tells you what domain to look in. The industry tells you who else has the same problem — and they may not be your competitors at all.

### Domain Patterns: Principles, Not Implementation

When looking for a non-technical capability — say accounting, customer management, scheduling — what you want is a set of **principles, rules, and procedures**. Look for patterns that are well-established and align with open standards, industry standards, law, and compliance.

**Not technical details to implement.** Align with [architecture](../EXPLICIT_ARCHITECTURE/README.md), not infrastructure.

This is how you embed correct meaning into your systems. This is how you avoid enterprise obfuscation that neither you nor your agents can cut through. This is how you understand your systems as well as your business domain — because when done correctly, your systems *are* your business domain.

| Looking For | Right Source | Wrong Source |
| ----------- | ----------- | ------------ |
| Accounting capability | GAAP/IFRS principles, double-entry bookkeeping rules | A specific ERP's data model |
| Customer management | CRM domain model, customer lifecycle states | Salesforce field definitions |
| Content publishing | Editorial workflow standards, content lifecycle | WordPress plugin architecture |
| Compliance | Regulatory frameworks, audit trail requirements | A vendor's compliance dashboard |

The pattern describes what the domain says is correct. The implementation follows from the pattern, not the other way around.

### Architecture-Level Pattern Sources

SemOps leans heavily into [SKOS](https://github.com/semops-ai/semops-dx-orchestrator/blob/main/docs/domain-patterns/skos-pattern.md)/W3C for architecture-level patterns, and that is "technical" in a sense. But look at how the patterns are used — we are adopting their **schema and definitions**, not their infrastructure. W3C standards make excellent pattern sources because they are:

- **Open** — no vendor lock-in, no licensing constraints
- **Stable** — decades of community validation
- **Well-documented** — extensive specifications and examples
- **AI-friendly** — thoroughly represented in training data

The same principle applies broadly: look for patterns that are well-established, neutral, and documented. Standards bodies (W3C, ISO, NIST), professional associations, and academic consensus are good starting points. See [Domain Pattern Examples](../../Publicv1_Supplemental/examples/domain-pattern-examples.md) for SemOps's W3C mappings and industry alignment.

### Finding Technical and Infrastructure Patterns

For technical and infrastructure patterns, a similar rule applies — but the emphasis shifts from domain principles to **neutral, core capability solutions**. Look for primitives and compose simple primitives rather than bundled solutions.

This will almost always produce the solution that is:

- The most **robust** — built on proven, focused components
- The most **extensible** — primitives compose; bundles constrain
- The most **agent-friendly** — standard protocols and APIs
- The most **[semantically coherent](semantic-coherence.md)** — clear boundaries, no hidden coupling
- The most **architecture-friendly** — swap components without redesigning

Even when you pick a vendor for convenience, **unpack the pattern** so that the underlying neutral capabilities are explicit.

**Example:** SemOps uses Supabase. But everything we use in Supabase, our agents and we understand as individual components — Postgres, pgvector, the UI layer. We know why we selected it: its ability to operate as shared infrastructure in self-hosted and local environments, then scale to cloud. But we know this, and we still know what the components are, and we know we do not use all of them. We could build our own Supabase equivalent and swap it — no architecture changes.

The pattern is "relational database + vector store + API layer for shared local-to-cloud infrastructure." The vendor is an implementation choice within that pattern. See the [Open Primitive Pattern](https://github.com/semops-ai/semops-dx-orchestrator/blob/main/docs/domain-patterns/open-primitive-pattern.md) for the full decision framework.

---

## Sizing Patterns

### As Big as You Can, and It Still Works

It is impossible to define precisely how "big" or broad a pattern should be. The rule of thumb is: **as big as you can, and it still works.**

What "it still works" means:

- It can be encoded into an object that fits in your architecture
- It matches the capabilities you are intending to deliver
- It maps to a bounded context (or a coherent subset of one)
- It can be validated — you can test whether an implementation conforms
- It can compose with other patterns without losing its own meaning

You can always test by trying. Adopt a pattern at a given scope, exercise it across real use cases, and see if it holds.

### When Patterns Are Too Small

Going "too small" means you end up combining patterns because you chose the same pattern to solve multiple capabilities — it was just the order of operations. You implement pattern A for capability X, then implement pattern A again for capability Y, and realize they are the same pattern covering a broader bounded context.

This is totally fine and how it should work. It is not a failure — it is discovery. Composition follows the same [composability property](patterns.md#composable-without-loss) that makes patterns work in the first place. Each constituent pattern remains coherent independently; the composed pattern is coherent at a higher level.

Some sizing decisions will be limited by scale. [Scale Projection](scale-projection.md) helps you think about this — designing for current scale while keeping clear upgrade paths to the next order of magnitude.

### Sizing Signals

| Signal | Diagnosis | Action |
| ------ | --------- | ------ |
| Pattern maps cleanly to one bounded context | Right size | Adopt |
| Pattern spans multiple bounded contexts | Too large | Decompose into constituent patterns |
| Pattern covers only part of a bounded context | Possibly too small | Look for the broader pattern, or compose |
| Pattern requires infrastructure assumptions | Wrong layer | Strip infrastructure, keep domain |
| Same pattern solves multiple capabilities | Too small (discovered) | Combine — this is expected |

---

## From 3P to 1P: The Evolution Lifecycle

### Following the Pattern

Once a 3P pattern is adopted and in use, you may find you want to change it. The good news: a 3P pattern's boundaries are probably well-understood, and your proposed change already has a validation check — you know what you think you are solving, and the baseline tells you whether the pattern already handles it.

**First check:** It could be that the pattern already has a standard way to do what you want, but you had not gotten there yet. This is the most common and most desirable outcome — nothing new is needed, keep following the pattern. This happens frequently with mature standards like SKOS, [PROV-O](https://github.com/semops-ai/semops-dx-orchestrator/blob/main/docs/domain-patterns/edge-predicates.md), or dimensional modeling, where the specification is deep and the full capability set takes time to discover.

### Vendor Capability Adoption

If you do want something different, it may align with a unique or novel capability offered by a vendor — SaaS, platform, or otherwise. Maybe more than one vendor offers it.

Adopt the capability, and now you have the basic 3P pattern plus one capability that is Vendor X, but *only* that. The nice thing: the vendor has documentation, maybe APIs, and you have artifacts to document it. Your agents will understand it immediately because the vendor's capabilities are described in their own public documentation.

The pattern stays 3P. The vendor is an implementation choice within the pattern, just like Supabase is an implementation choice within the infrastructure pattern. Track it, document it, know the boundary.

### Genuine 1P Innovation

Then there is actual innovation — where the domain is an area where you **intentionally** want to innovate because it is part of why you exist as a business. Differentiation, positioning, or perhaps it is operational and you will improve quality, coverage, or speed.

In this case, the innovation object should:

- **Fit into your architecture** — it is not floating outside the system
- **Have a well-defined goal** — you know what capability it adds and why
- **Have a reason** — differentiation, competitive advantage, operational improvement
- **Be tracked** — lineage from the 3P baseline, with intentional deviations documented

This is the only case that creates true 1P provenance. See [Provenance and Lineage](provenance-lineage-semops.md) for how deviations are tracked.

### Starting 1P When Nothing Fits

Sometimes you start with a 1P pattern because there just is not anything that fits. That is fine — go for it. But there are probably pieces of 3P patterns that you can compose, and it is better to do that so that you at least have a baseline. It is also a check on your ideas: is it really completely novel? And if so, is that a good thing?

You may also find that you need to discover more patterns to solve more defined problems inside a bigger pattern. A large 1P pattern often decomposes into sub-problems where established 3P solutions exist for some of them. Composing 3P pieces within a 1P container gives you the best of both: proven components where they exist, innovation where it matters.

### The Lifecycle

```text
3P Adopted → Used in Context → Change Needed?
                                      │
                    ┌─────────────────┼─────────────────┐
                    │                 │                   │
               Already Handled   Vendor Aligns      Genuine 1P Need
               (stay 3P)         (3P + vendor)      (tracked deviation)
                                                          │
                                                    Fits Architecture?
                                                    Well-defined goal?
                                                          │
                                                    1P Pattern Created
                                                    (lineage: derives from 3P)
```

| Trigger | Result | Provenance | Risk |
| ------- | ------ | ---------- | ---- |
| Standard already covers it | Stay 3P | 3P | Low — canonical form maintained |
| Vendor capability match | 3P + vendor | 3P | Medium — vendor dependency, but documented |
| Genuine differentiation need | 1P innovation | 1P | Assessed — intentional, tracked, bounded |
| Nothing fits | Start 1P, compose from 3P pieces | 1P | Higher — validate against 3P baseline |

---

## Strategic Benefits

### Table Stakes and Intentional Innovation

One of the biggest benefits of pattern-based thinking is making visible where effort is actually going. Most organizations have significant resources devoted to capabilities that are effectively table stakes — standard, well-understood domain patterns that do not differentiate the business.

The hypothesis: roughly **80% of a company's effort can be delivered and maintained at table stakes** — standard 3P domain patterns that match or exceed industry norms. The remaining **20% is genuine 1P innovation** that deserves focused resources: differentiation, positioning, operational advantage.

The compounding benefit:

- The **[stable core](../EXPLICIT_ARCHITECTURE/stable-core-flexible-edge.md)** (3P table stakes) gets more stable as patterns mature — less maintenance, fewer surprises, more predictable
- The **[flexible edge](../EXPLICIT_ARCHITECTURE/stable-core-flexible-edge.md)** (1P innovation) can push further because it is not burdened with reinventing solved problems
- Resources concentrate on innovation, not on maintaining custom implementations of standard capabilities

**Avoiding unintentional innovation:** If you are spending engineering effort on a capability that a 3P pattern already delivers, that effort is not innovation — it is unintentional reinvention. The pattern approach makes this visible. You can audit your portfolio and ask: for each capability, is this genuinely 1P, or are we reinventing a 3P pattern without realizing it?

| Situation | What It Likely Means | Action |
| --------- | -------------------- | ------ |
| Building custom auth system | Reinventing 3P | Adopt OAuth/OIDC pattern |
| Building custom analytics pipeline | Reinventing 3P | Adopt dimensional modeling pattern |
| Building unique content pipeline | Genuine 1P | Track, measure, protect |
| Building custom scheduling | Check first | Domain pattern probably exists |

See [Stable Core, Flexible Edge](../EXPLICIT_ARCHITECTURE/stable-core-flexible-edge.md) for the architectural principle behind this split.

### Competitive Analysis Through Patterns

A side benefit of patterns is that you can adopt, match, or simply track competitors' differentiation in the form of patterns. This opens a completely different vector:

1. **Identify competitor patterns** — From their public signals (products, APIs, documentation, job postings, marketing), identify what patterns they appear to be executing
2. **Coherence score their execution** — Use [Semantic Coherence](semantic-coherence.md) measurement to assess how well their public signals align with the pattern they claim. You could score it in their context, or score the pattern against your own architecture
3. **Project the impact** — Using [Scale Projection](scale-projection.md), figure out what architecture and infrastructure changes you might need to match a competitor's pattern. Even coherence score the scenario to surface initially unseen impacts — customer support load, messaging confusion with another feature, training requirements

This reframes competitive analysis from "what features do they have?" to **"what patterns are they executing, and how coherently?"** The former leads to feature-chasing. The latter leads to strategic decisions about which patterns to adopt, which to match, and which to ignore because they do not fit your architecture.

---

## Related Links

### Core Concepts

- [Patterns](patterns.md) - Pattern definitions, taxonomy, and core properties
- [Pattern Operations](pattern-operations.md) - Promotion loop, optimization cycle
- [Semantic Coherence](semantic-coherence.md) - Measuring pattern integrity
- [Provenance and Lineage](provenance-lineage-semops.md) - Tracking pattern evolution
- [Scale Projection](scale-projection.md) - Designing for next order of magnitude

### Architecture

- [Stable Core, Flexible Edge](../EXPLICIT_ARCHITECTURE/stable-core-flexible-edge.md) - Evolution principle
- [Patterns and Bounded Contexts](../EXPLICIT_ARCHITECTURE/patterns-and-bounded-contexts.md) - DDD integration
- [Domain-Driven Design](../EXPLICIT_ARCHITECTURE/domain-driven-design.md) - Bounded contexts and ubiquitous language

### Implementation & Examples

- [Domain Pattern Examples](../../Publicv1_Supplemental/examples/domain-pattern-examples.md) - W3C mappings, industry alignment, emergence narrative
- [3P Domain Patterns](../../Publicv1_Supplemental/examples/3p-domain-patterns.md) - SemOps implementation example
- [Open Primitive Pattern](https://github.com/semops-ai/semops-dx-orchestrator/blob/main/docs/domain-patterns/open-primitive-pattern.md) - Infrastructure pattern selection criteria
- [Domain Pattern Registry](https://github.com/semops-ai/semops-dx-orchestrator/blob/main/docs/domain-patterns/) - Registered patterns across the ecosystem
