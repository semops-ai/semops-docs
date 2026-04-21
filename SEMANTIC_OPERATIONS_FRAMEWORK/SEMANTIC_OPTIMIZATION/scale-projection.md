# Scale Projection

> Scale Projection is a validation technique that tests domain model coherence by projecting current architecture to a scaled deployment and measuring the gap. If the gap is purely infrastructure, the domain model is coherent. If it requires logic changes, the abstractions are wrong.

## Overview

Scale Projection inverts the conventional framing of scaling. Instead of asking "what infrastructure do we need?", it asks "do we understand the domain well enough that infrastructure choices are constrained, possibly obvious?" The technique: sketch what a system would look like at the next order of magnitude, then categorize every gap as Clean (infrastructure only), Concerning (logic changes needed), or Blocking (fundamental rethink). When the sketch cannot be drawn cleanly, the missing piece is domain knowledge, not infrastructure knowledge.

The core claim:

> If the infrastructure delta between local and scaled is anything other than "wrapper and plumbing," the [architecture](../EXPLICIT_ARCHITECTURE/explicit-architecture.md) (not the infrastructure) needs work.

Scale Projection evolved from "Progressive Productization." The early iteration used the term "Mirror Architecture" for structural scaffolding (directory layout, parallel representations), but the value was never the structure. It was what the gap between local and projected reveals about domain coherence.

---

## The Diagnostic

Scale Projection works by sketching the scaled version of a system and categorizing the delta between current and projected state.

| Category | Meaning | Signal |
| -------- | ------- | ------ |
| **Clean** | Pure infrastructure swap (file queue to SQS, SQLite to Postgres) | Architecture is sound. This is capacity planning. |
| **Concerning** | Logic changes needed (different branching, new edge cases, implicit assumptions) | Domain model may have coupling to infrastructure. |
| **Blocking** | Fundamental rethink required (data ownership, service boundaries) | Architecture needs work before scaling. |

When the delta is all Clean, the architecture is validated and scaling is a plumbing exercise. When Concerning or Blocking items appear, the architecture is surfacing a signal that is valuable *before* the system actually needs to scale.

---

## Core Concepts

### The Continuum

The Continuum defines eight levels of formalization representing **domain legibility**. Unlike CMMI's five linear maturity levels, Continuum levels are not sequential. A feature may jump from Level 0 to Level 4 when the domain is well understood, or stay at Level 1 indefinitely when it is not.

| Level | What It Represents | Key Characteristic |
| ----- | ------------------ | ------------------ |
| 0. Chat | "I am exploring what this even is" | Ephemeral, manual |
| 1. Slash commands | "I can name the operations" | Structured invocation |
| 2. Subagents / CLAUDE.md | "I can externalize the rules" | Repo-persisted context |
| 3. External trigger | "I know the boundaries and entry points" | Event-driven |
| 4. Local container | "I can specify inputs and outputs precisely" | Isolated execution |
| 5. Cloud sandbox | "The domain model is stable enough that infra is plumbing" | Managed infrastructure |
| 6. Deployed service | Production | Full state, auth, orchestration |
| 7. Scaled infra | Enterprise scale | Distributed, multi-tenant |

The discipline is staying in the lab (levels 0 through 3) until the business logic is *earned*, not assumed. Productionization then becomes straightforward: wrapping known-good logic in infrastructure. Treating levels 0 through 3 as "quick MVP" and rushing to 4+ before the manifest is solid means scaling confusion.

### The Axes

Six independent dimensions of formalization. Strong isolation with weak observability is a valid state. The axes are diagnostic, not prescriptive.

| Axis | Informal to Formal |
| ---- | ------------------ |
| **Directed** | Chat, Slash, Event, API, Scheduled |
| **Context** | Conversation, Hooks, Manifest, DB-backed |
| **Isolation** | Shared env, Worktree, Container, VM, Cluster |
| **Access** | Just you, Team, Authenticated users, Public |
| **State** | Stateless, Issue-as-queue, Managed queue, DB |
| **Observability** | Print, Logs, Metrics, Tracing |

Not all axes move together, and they do not need to. Axes describe *where a system is* on the formalization spectrum. Scale Vectors (below) describe *what happens when projecting forward*.

### GAPS.md

GAPS.md is the coherence test artifact. Maintained per-repo, it categorizes every gap between local and projected as Clean, Concerning, or Blocking. When "Blocking" has items, stop and fix abstractions. When "Concerning" grows faster than "Clean," the system is accruing domain debt.

The coherence test asks four questions:

| Question | Healthy Answer |
| -------- | -------------- |
| Can every capability be expressed as K8s resources? | Yes |
| Is the diff just infrastructure? | Yes |
| Do manifests work without changing domain logic? | Yes |
| Can every gap be explained? | Yes |

### The Manifest

The manifest (context, tools, constraints, prompts) represents business logic as the portable artifact across all Continuum levels. The manifest is identical whether running as a CLI, container, or distributed service. Infrastructure is the wrapper.

The manifest that emerges from this process is crystallized understanding of the problem. The slash commands are not shortcuts; they are hypotheses being tested.

### DDD Lens

The Continuum levels map to [Domain-Driven Design](../EXPLICIT_ARCHITECTURE/domain-driven-design.md) concepts. Symptoms that appear to be infrastructure problems are often domain understanding problems:

| Symptom | Conventional Diagnosis | Domain Diagnosis |
| ------- | ---------------------- | ---------------- |
| Scaling is complicated | Need better infra | Boundaries are wrong |
| Adding features is hard | Tech debt | Manifest structure does not match real task taxonomy |
| Agents fail unpredictably | Need better prompts | Constraints are not explicit because they are not understood |
| Integration is messy | Need middleware | Contracts do not reflect actual data flows |

---

## Scale Vectors

Scale Vectors are the dimensions along which a projection is performed. Where the Axes describe formalization state, Scale Vectors describe growth dimensions that stress architecture. Part of the methodology is selecting which vectors are relevant. A solo data pipeline does not need the Team Size vector, and an internal tool does not need Geographic Distribution.

| Vector | Guiding Question | Typical Clean Gap | Typical Concerning Gap |
| ------ | ---------------- | ----------------- | ---------------------- |
| **Data Volume** | What happens at 10x / 100x current data? | Add object storage, partition tables | Business logic assumes everything fits in memory |
| **Concurrent Users** | What happens with N simultaneous users? | Add auth proxy, connection pooling | Domain model has implicit single-user assumptions |
| **Geographic Distribution** | What if users or data are global? | Add CDN, regional replicas | Data model assumes co-located storage |
| **Throughput / Latency** | What if response time matters at volume? | Swap in-process queue for managed queue | Business logic is synchronous by design |
| **Data Complexity** | What if schema or relationships grow? | Add indexes, migrate storage engine | Entity relationships are implicit in code, not in schema |
| **Regulatory / Compliance** | What if audit, privacy, or access control requirements apply? | Add audit logging, encryption layer | No concept of data ownership in domain model |
| **Team Size** | What if multiple contributors work on this? | Add CI pipeline, branch protection | Knowledge is tribal; contracts are not explicit enough to onboard |

### Selecting Vectors

When performing a projection:

- **Start with the forcing function.** What is the most likely growth dimension?
- **Add regulatory if external-facing.** Any system that handles user data or integrates with external services should project along Regulatory/Compliance.
- **Skip what does not apply.** A batch data pipeline may only need Data Volume and Data Complexity.
- **Project one vector at a time.** Combining vectors in a single pass obscures which vector reveals which gap.
- **Record which vectors informed each gap.** GAPS.md entries should note the projection context so future reviewers can interpret them.

### Relationship to Axes

Axes and vectors interact. A gap that requires moving along an Axis (for example, from "Stateless" to "DB-backed" on the State axis) is likely Clean. A gap that requires domain logic to change regardless of axis position is Concerning or Blocking.

| Scale Vector | Most Affected Axes |
| ------------ | ------------------ |
| Data Volume | State, Isolation |
| Concurrent Users | Access, State, Isolation |
| Geographic Distribution | Context, Isolation |
| Throughput / Latency | Directed, State |
| Data Complexity | Context, State |
| Regulatory / Compliance | Access, Observability |
| Team Size | Context, Observability, Directed |

---

## Repo as Scaling Unit

A code repository is simultaneously three things: an **architecture** boundary (capability delivery), an **infrastructure** boundary (deployable unit), and an **organization** boundary (agent/team role). These align because they derive from the same domain model. Scale Projection uses the repo as the unit of analysis.

### K8s Projection per Repo

Each repo maps to a Kubernetes (K8s) **namespace** containing its deployments. The projection generates K8s manifests for each capability and observes which scale vectors are handled by standard K8s mechanisms (Clean) versus which require domain logic changes (Concerning/Blocking).

K8s manifests serve as thinking tools, not deployment artifacts. They do not need to `kubectl apply` successfully. They need to be expressive enough to reveal whether the delta between local and scaled is just infrastructure. Writing the manifest forces a concrete question: "What K8s resource runs this capability?" When the answer requires changing domain logic, the architecture has a gap.

### Data Volume: OLTP/OLAP Split

Data volume scaling uses a two-layer architecture:

| Layer | Scope | Governance | K8s Shape |
| ----- | ----- | ---------- | --------- |
| **Per-repo (OLTP)** | Each repo owns its operational data | DDD-governed; repo controls writes | StatefulSet per repo namespace |
| **Cross-repo (OLAP)** | Analytical layer reads from all repos | Customer-Supplier integration; observes, does not own | CronJobs pulling from repo Services |

The OLTP/OLAP distinction preserves repo boundaries while enabling cross-cutting analytics. The gap appears only when analytical queries need to JOIN across repos at the database level rather than at the application level.

### Team Size: Human/Agent Scaling

Team size scales along two different curves:

| Dimension | How It Scales | Constraint |
| --------- | ------------- | ---------- |
| **Agent scaling** | Add repos (new domain = new namespace with CLAUDE.md + typed integrations) | Context window boundary; each agent scoped to its repo domain |
| **Human scaling** | Add cross-boundary judgment capacity (humans float across repos) | Humans are not bound to repos; their value is cross-domain perspective |

Agents specialize per repo (domain experts), while humans generalize across repos (strategists). Adding a human does not mean adding a repo. It means adding judgment capacity that agents cannot provide. If adding a human requires architecture changes, the repo boundaries are wrong.

---

## Infrastructure Tier Taxonomy

Infrastructure tiers are operational shapes within each data system type. They sit between Data System Type and Scale Vectors in the methodology chain:

```text
Pattern, Capability, Data System Type, Infrastructure Tier, K8s Resource Type, Scale Vectors, Gap
```

Each tier has a K8s resource type that defines its concrete projection target. The K8s resource type determines which scale vectors are handled by standard mechanisms and which reveal gaps. Tiers are extracted from projections, not designed upfront.

### Analytics Data System

| Tier | Physics | K8s Resource |
| ---- | ------- | ------------ |
| **Ingestion** | Batch, throughput, staging, idempotency | CronJob + PVC |
| **Storage** | Indexing, partitioning, schema evolution | StatefulSet + PVC |
| **Reference/Dimensional** | Conformed dimensions, semantic layer | ConfigMap or CRD |
| **Query/Serving** | Latency, concurrency, connection pooling | Deployment + Service + HPA |
| **Measurement** | Aggregation, batch validation | CronJob |
| **Lineage** | DAG structure, provenance tracking | StatefulSet (graph DB) |

### Application Data System

| Tier | Physics | K8s Resource |
| ---- | ------- | ------------ |
| **API/Web** | Request/response, routing, latency | Deployment + Service + Ingress |
| **Persistence** | ACID transactions, aggregate consistency | StatefulSet + PVC |
| **Session/Auth** | User state, token management | Deployment + Service (or managed) |
| **Cache** | Ephemeral, latency optimization | Deployment (no PVC) |
| **Background Processing** | Async, eventual consistency | Job / CronJob |

### Enterprise Record System

| Tier | Physics | K8s Resource |
| ---- | ------- | ------------ |
| **Ledger** | Strict consistency, double-entry invariants | StatefulSet (single-writer) |
| **Audit** | Append-only, immutable history | StatefulSet + PVC (append-only) |
| **Constraint Enforcement** | Business rule validation, regulatory compliance | ValidatingWebhook or Operator |
| **Reporting** | Aggregation over record history | CronJob + Deployment |

### Enterprise Work System

| Tier | Physics | K8s Resource |
| ---- | ------- | ------------ |
| **Extraction/Classification** | Batch, AI-intensive, high compute | Job (GPU-enabled) |
| **Knowledge Store** | Graph/vector, semantic search | StatefulSet + PVC |
| **Governance** | Ontology management, quality validation | CronJob + ConfigMap |
| **Search/Discovery** | Read-heavy, latency-sensitive | Deployment + Service + HPA |

### Cross-Type Observations

- **Storage tiers converge across types.** StatefulSet + PVC appears in every data system type. The difference is physics: Analytics partitions by time/dimension, Application by tenant/aggregate, Record by period, Work by source.
- **Query tiers converge.** Deployment + Service + HPA appears in Analytics (Query/Serving), Application (API/Web), and Work (Search/Discovery). The K8s shape is identical; the domain logic behind the Service differs.
- **Enterprise Work mirrors Analytics shifted left.** Work's Extraction maps to Analytics' Ingestion. Work's Knowledge Store maps to Analytics' Storage. This reflects the Enterprise Work to Analytics transition, where Work produces raw material that Analytics structures.
- **Enterprise Record is the most constrained.** The Ledger tier requires single-writer StatefulSet. Double-entry invariants require serialized writes. If a Record system needs horizontal write scaling, the invariant model is wrong.

---

## Why This Is Practical Now

Architecture validation has historically been expensive. The conventional approach required deploying to test environments, staffing platform teams, and running capacity tests. By the time validation happened, sunk cost made it hard to revisit fundamentals.

Scale Projection changes what the projection targets. K8s manifests are thinking tools, not deployment artifacts. They do not need to `kubectl apply`. They need to be expressive enough to reveal whether the delta between local and scaled is just infrastructure. This reframing makes the test cheap.

### Coding Assistants as Enablers

Coding assistants operating on structured repo docs make it possible to build business logic and test scaling properties in the same session:

- **Build business logic** in the normal course of implementing capabilities
- **Run the projection** as a slash command that walks the chain interactively
- **Get architecture feedback while designing**, not after shipping
- **The projection is a conversation**, not a ceremony requiring separate teams and tooling

The prerequisites are already present in a well-structured repo: `ARCHITECTURE.md` (capabilities, ownership, DDD classification), `INFRASTRUCTURE.md` (current state), and pattern tracing (capability to pattern links). The coding assistant reads these, walks the chain, and the human validates where reasoning breaks down.

### The Chain as Conversation

```text
Pattern, Capability, K8s Resource, Gap
```

Each link is walked interactively. The assistant reads ARCHITECTURE.md, traces each capability to its governing patterns, proposes a K8s resource type based on the capability's operational shape, and the human catches where assumptions do not hold. When a link cannot be traced, that is the signal that the domain is not understood yet.

### Testing Future Properties from Current State

A system at Continuum levels 1 through 2 (slash commands, CLAUDE.md) can test level 6 through 7 properties (multi-tenant, distributed, production-grade) without leaving the IDE. The projection surface (K8s manifests) is a text format. The inputs (ARCHITECTURE.md, INFRASTRUCTURE.md) are text files. The output (GAPS.md) is a text file. The entire chain is readable, diffable, and versionable.

---

## Resourcing Methodology

Once GAPS.md identifies gaps, the resourcing methodology answers: "The gap is known. Now what?"

### Triage by Gap Category

| Category | Resolution Approach | Urgency |
| -------- | ------------------- | ------- |
| **Clean** | Evaluate infrastructure options; resolve when needed | Low. Known and bounded. |
| **Concerning** | Investigate domain model; determine if abstraction changes are needed before choosing infrastructure | Medium. Accruing debt if ignored. |
| **Blocking** | Stop. Fix the domain model. Re-project after fix. | High. No infrastructure investment until resolved. |

Never resource a Concerning or Blocking gap with infrastructure. When the domain model is wrong, better infrastructure scales the wrong thing faster.

### Resolution Process

For each gap in GAPS.md:

1. **Classify the gap type.** Infrastructure gaps (domain model correct, need different wrapper), abstraction gaps (implicit assumptions break under scale), or boundary gaps (responsibilities in the wrong place).
2. **Map the design space.** For infrastructure gaps, identify categories of solutions (managed cloud, self-hosted, in-process). For abstraction gaps, identify what the domain model needs to express that it currently does not.
3. **Evaluate against constraints** including cost at projected scale, operational complexity, team familiarity, vendor lock-in, open-source availability, and coherence with existing patterns.
4. **Validate against scale vectors.** A solution that resolves the current gap but creates a new gap at the next projection is a deferral, not a resolution.
5. **Document the decision.** Significant choices get an ADR (Architecture Decision Record). Routine infrastructure choices are annotated directly in GAPS.md.

---

## 3P Lineage

Scale Projection draws from and extends established patterns:

| Source | Contribution | Extension |
| ------ | ------------ | --------- |
| [Domain-Driven Design](../EXPLICIT_ARCHITECTURE/domain-driven-design.md) (Evans) | Bounded contexts, ubiquitous language, domain model primacy | Gap analysis as DDD diagnostic. "If scaling requires logic changes, boundaries are wrong." |
| Hexagonal Architecture (Cockburn) | Domain does not know infrastructure | Test it: write the K8s manifest, see if domain logic changes |
| Maturity Models (CMMI) | Progressive capability levels | Levels represent domain understanding, not process maturity. Non-linear. |
| Fitness Functions (Ford, Parsons, Kua) | Automated architecture validation | Give it a target: fitness of current state vs. projected K8s manifests |
| Capacity Planning (SRE) | Forecast resource needs | Invert it: use infrastructure delta to reveal architecture problems |
| Kubernetes (CNCF) | Declarative infrastructure-as-data with reconciliation loops | The projection surface: concrete, diffable, vendor-neutral. K8s resource types define what "clean gap" means. |
| Architecture Runway (SAFe) | Forward-looking investment in architectural capability | Scale Projection is the diagnostic that identifies what the Runway should invest in |
| AWS Well-Architected (6 pillars) | Structured evaluation across cost, reliability, performance, security | Resourcing constraint evaluation criteria map to these pillars |
| ThoughtWorks Technology Radar | Adopt / Trial / Assess / Hold categorization | Options in the design space can be categorized by maturity and confidence |

---

## Application to Patterns

In the context of [Working with Patterns](working-with-patterns.md), Scale Projection informs:

- **[Pattern sizing](working-with-patterns.md#sizing-patterns).** Understanding whether a pattern's scope will hold at the next scale level. When a pattern breaks at 10x, it may be too tightly coupled to current infrastructure.
- **Competitive analysis.** Projecting what architecture and infrastructure changes would be needed to match a competitor's pattern, and [coherence scoring](semantic-coherence.md) the scenario for unseen impacts.
- **Table stakes validation.** Determining whether a 3P pattern delivers enough capability for projected growth, or whether 1P innovation is genuinely needed.

---

## Worked Example: semops-hub

The first full projection against semops-hub (2026-03-08) projected seven capabilities to K8s manifests:

| Capability | K8s Resource | Gap |
| ---------- | ------------ | --- |
| `domain-data-model` | Job, ConfigMap | Clean |
| `internal-knowledge-access` | Deployment+HPA, Sidecar | 3 Concerning (connection pooling, auth, cache invalidation) |
| `ingestion-pipeline` | CronJob (x2) | 2 Concerning (raw content cache, concurrent write control) |
| `pattern-management` | Job | Clean |
| `coherence-scoring` | CronJob | Clean |
| `agentic-lineage` | *(embedded in ingestion)* | Open question |
| `bounded-context-extraction` | Job | Planned |

**Result:** 0 Blocking, 5 Concerning, 9 Clean. Coherence verdict: Clean overall, with operational concerns.

The projection surfaced a classification correction: the repo was initially classified as an Analytics Data System. Walking the chain revealed it is infrastructure/schema authority. It owns the domain model and serves it, rather than analyzing data flowing through medallion layers. This reclassification eliminated a false "no staging layer" gap from earlier manual analysis. The correction emerged because the K8s manifests forced a concrete question: "What K8s resource runs this capability?" When the answer did not match Analytics patterns, the system type was wrong.

---

## Related Links

### SemOps Concepts

- [Working with Patterns](working-with-patterns.md) - Pattern sizing, competitive analysis
- [Patterns](semantic-patterns.md) - Pattern definitions and taxonomy
- [Semantic Coherence](semantic-coherence.md) - Measuring pattern integrity
- [Stable Core, Flexible Edge](../EXPLICIT_ARCHITECTURE/stable-core-flexible-edge.md) - Evolution principle

### Architecture

- [Explicit Architecture](../EXPLICIT_ARCHITECTURE/explicit-architecture.md) - Architecture as encoded rules
- [Domain-Driven Design](../EXPLICIT_ARCHITECTURE/domain-driven-design.md) - Hexagonal architecture, bounded contexts

### Implementation

- [Scale Projection pattern (semops-hub)](https://github.com/semops-ai/semops-hub/blob/main/docs/domain-patterns/scale-projection.md) - Full pattern documentation with selection criteria and decision framework
- [Scale Projection Guide (semops-dx-orchestrator)](https://github.com/semops-ai/semops-dx-orchestrator/blob/main/docs/SCALE_PROJECTION_GUIDE.md) - Step-by-step implementation guide
