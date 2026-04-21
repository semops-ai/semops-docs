# SemOps as Context Engineering

## What SemOps Is

SemOps is a stack-neutral data management system that works in tandem with AI agents to maintain semantic consistency across an entire company through governance.

In practice, SemOps is a layer that bolts onto an existing organization — or that a new organization can be built on from scratch. The company adopts the same architecture principles (Domain-Driven Design, explicit bounded contexts, structured data objects with typed edges) that SemOps uses internally, but the company's domain and operations are what gets encoded. The result is a machine-legible version of the business: goals encoded as patterns, decisions as architectural records, domain knowledge as a schema with typed edges, strategy as provenance chains, roadmap as capability lifecycle.

This encoded business becomes the substrate that AI agents operate on. The agent doesn't need a separate knowledge base, a bespoke memory system, or hand-curated retrieval pipelines — because the architecture itself is all of those things.

## SemOps as Top-Down Context Engineering

Context Engineering is *the set of strategies for curating and maintaining the optimal set of tokens during LLM inference.* Managing context means managing five agent primitives: the **Model** (LLM), **Context** (active token window), **Prompt** (instructions), **Memory** (stored context), and **Tools** (actions, retrieval of context).

SemOps solves context engineering top-down, systematically, and as a consequence of the design. Three core components do the work:

- **Explicit Architecture** — The encoded business. Structured data objects connected through typed edges, spanning technical and non-technical artifacts. The architecture IS the agent's memory.
- **Patterns** — The operational layer. Patterns are how work moves forward — the pattern → capability → script chain defines what agents do and what tools they have.
- **Semantic Coherence** — The measurement and maintenance layer. A quantitative formula that tells you whether context is healthy, plus runtime agents that continuously reconcile the real world against what the architecture claims.

The context plumbing — whether it's RAG, a manifest, a SQL query, or a graph traversal — is decided when the architecture is chosen and patterns are deployed. There is no second discovery phase.

---

## Explicit Architecture: Memory Is the Architecture Is the Business

The deepest insight in SemOps is that agent memory and business knowledge are the same artifact. When you encode a company into an Explicit Architecture, you haven't built an "agent memory system" — you've built a machine-legible version of the business. The agent queries it the same way a new employee would learn the company, except it's structured, governed, and queryable.

### What the Architecture Contains

The architecture is structured data objects connected to everything — including concepts that aren't technical artifacts at all. Business goals, domain vocabulary, strategic decisions, process definitions, organizational boundaries — all encoded as first-class data with typed relationships.

To make this concrete, here's what a single repository looks like as an architectural object:

```yaml
# From registry.yaml — capabilities trace up to patterns and down to delivery, and even reference the github issue that created it
- id: gap-analysis
  name: Gap Analysis
  status: active
  strategic_classification: core
  implements_patterns:
    - togaf
    - semantic-ingestion
  delivered_by:
    - semops-research
  governance:
    issue: semops-dx-orchestrator#211
    criteria: Predicted architecture vs detected signals — outside-in delta
```

Its queryable data, not documentation. Every capability traces **up** to the business patterns it implements which themselves have strategic knowledge. They trace **down** to the repos that deliver it. The governance field links to the GitHub issue where decisions are tracked. Strategy, architecture, implementation, and audit are all edges in the same data model.

The repos themselves trace down to concrete infrastructure:

| Repo | Owns | Type |
|------|------|------|
| **semops-hub** | PostgreSQL, Qdrant, Neo4j, Docling, n8n, Supabase, Query API, MCP Server | Always-on (Docker Compose) |
| **semops-research** | Qdrant (local), Neo4j (local) + consumes Ollama, Qdrant, Docling from semops-hub | On-demand + Consumer |
| **data-pr** | Jupyter Lab, MLflow | On-demand (DevContainer) |
| **sites-pr** | Next.js dev servers, Supabase local | On-demand |
| **semops-publisher** | ComfyUI | On-demand |
| **semops-backoffice** | *(connects to external APIs)* | External only |

The full stack plus non-technical rules, goals, and strategic intent is a single connected graph. An agent can traverse from "when was this strategic core pattern changed?" all the way down to "what capability changed using which code?" because every layer is an edge in the same data model.

Every entity carries provenance: 1P (first-party), 2P (partner, contract), 3P (external) and lifecycle (planning, in-progreess, active, retired). This isn't metadata added later — it's structural, embedded at ingestion. The physical boundary provided strategic insight (differentiation), as well as solid governance virtually everywhere. Validated knowledge lives in governed SQL (the core), while experimental or unvalidated data stays in the flexible edge (vectors, graph) until explicitly promoted through review.

The data layer IS the RAG corpus and the database. There's no separate retrieval system bolted on after the fact:

```text
                    ┌─────────────────────────────────┐
                    │      Encoded Business           │
                    │    Patterns · Capabilities      │
                    │  Decisions · Strategy · Goals   │
                    └──────────────┬──────────────────┘
                                   │
              ┌────────────┬───────┴───────┬────────────┐
              ▼            ▼               ▼            ▼
        ┌──────────┐ ┌──────────┐  ┌───────────┐ ┌──────────┐
        │   SQL    │ │  Vector  │  │ Knowledge │ │   YAML   │
        │  Schema  │ │Embeddings│  │   Graph   │ │ Manifests│
        └────┬─────┘ └────┬─────┘  └─────┬─────┘ └────┬─────┘
             │            │              │            │
             ▼            ▼              ▼            ▼
        Deterministic  Semantic     Relationship   Structural
          queries     similarity    traversal      lookups
```

Four query modes access the same governed knowledge — an agent might access all four accomplishing a task.


### How This Solves Memory, Context, and Prompt

**Memory** — globally available, stratified by importance. The critical architecture data — patterns, capabilities, the governed vocabulary, integration maps — is available everywhere through shared tools. An MCP server exposes the knowledge base to any agent in any repo. A query API serves the same governed data to workflows, dashboards, and CI pipelines. The registries (YAML manifests) travel with the codebase. No matter where an agent operates, the architecture is reachable — not because of per-agent configuration, but because the tools that access it are shared infrastructure. The more strategically critical the data, the more defined the schema and the lighter the cognitive load: a pattern lookup returns a compact, typed object — not a document to parse.

**Context** — each agent gets just what it needs. Corpus routing starts with the thinnest source (a YAML manifest or SQL query) and only reaches for heavier stores (vectors, graph) when the question demands it. Repo boundaries enforce physical separation — an agent in a content repo loads style guides and format specs, not schema internals or research pipelines. The governed vocabulary prevents ambiguity within each boundary. The result is minimal, high-signal context windows by default.

**Prompt** — thin by design. Because the architecture and infrastructure are fully encoded, and because the system is built agent-first, agents are built alongside any new capability, which results in simple, deterministic tasks — most agents are actually workflows with almost no margin for error. When the data layer provides structured objects that prompts can reference, prompts don't need to carry context themselves. Instead of long system-prompt preambles explaining the domain, prompts reference the architecture. The heavy lifting is in the data relationships, not the instructions.

### Example: Ridgeline Vendor Merchandising

Ridgeline Outdoor Co. is a $35M D2C outdoor gear brand — Shopify Plus storefront, 4,000 SKUs, 25-person team. Here's a task that touches all three primitives — Memory, Context, and Prompt — without any per-agent configuration.

A vendor sends a new merchandising sheet — marketing copy, lifestyle imagery, suggested retail positioning for next season's line. An agent needs to:

1. **Match to product records** — SQL query via MCP tools against the product catalog. The governed schema means the agent doesn't guess at field names or product hierarchies; the data model is typed and the tool returns structured results.
2. **Check internal promo plans** — The repo manifest has document metadata for upcoming promotions. The agent queries the manifest (YAML — thinnest source) and finds a promo plan with a launch date 10 days out.
3. **Retrieve promo plan content** — Because the date is within 2 weeks, the agent pulls promo plan chunks from the vector store — the heavier corpus, reached only because the thinner source indicated it was needed.
4. **Reason a proposal** — Using a template from the repo, the agent combines vendor merchandising language with internal promo positioning to draft a product launch proposal.

No one configured this agent's memory, context, or prompt for this specific task. The product records were in SQL because the architecture put them there. The promo plan was discoverable through the manifest because every document carries metadata. The template was in the repo because agents are built alongside capabilities. Corpus routing escalated from manifest → SQL → vectors as the task required. The prompt was thin — "match, check timing, draft proposal per template" — because the architecture carried the domain knowledge.

---

## Patterns: The Operational Layer

If the architecture is memory, patterns are operations — the forward movement of the business.

A pattern is a semantic compression unit. "Adopt CRM" loads an entire body of implied capabilities, integration patterns, and domain concepts in a few tokens — because the pattern, its edges, and its capability chain are structured objects in a registry, not prose in a document. Patterns are how SemOps converts architectural knowledge into agent action.

### The Pattern → Capability → Script Chain

Every pattern decomposes into capabilities, and every capability decomposes into scripts or tools — all at the same level of abstraction, all explicit. This chain is deterministic and complete: an agent doesn't need to infer what a pattern means or guess what tools it should have, because the decomposition is already done.

The MCP tools that agents call return structured, governed responses — typed outputs from schema queries, not generated prose. When an agent needs to act, it follows the chain: pattern defines the domain, capability defines the operation, script/tool defines the execution. No gaps for the model to fill in, no hallucination surface.

This has a second-order effect: because memory and tools are so structured, deterministic workflows can handle tasks that would otherwise require autonomous agents improvising. The threshold for when you need autonomy goes up, and with it, the surface area for hallucination goes down.

### How This Solves Tools and Model

**Tools** are defined by the architecture, not configured per agent. The pattern → capability chain narrows the options to the point where the right tools are obvious. An agent operating on a content pattern gets content tools. An agent operating on an infrastructure pattern gets infrastructure tools. The architecture makes the selection.

**Model** selection is the one genuine choice point. SemOps is architecture-independent with respect to models — but better context, memory, and tools reduce how much the model needs to compensate. When the architecture provides structured, complete, governed data, even a smaller model performs well because it's not filling gaps.

### Corpus Routing: The Right Store for the Right Query

Different questions need different data representations. "What patterns exist?" is a SQL query. "What's similar to this concept?" is a vector search. "How does this capability connect to that strategy?" is a graph traversal. "What's the current state of this repo?" is a manifest lookup.

Corpus routing directs each query to the right store, so the agent gets precise answers — not document fragments that dilute the context window with noise. This is dilution defense built into the architecture: the agent never receives more tokens than the query requires.

### Example: "We Need a CRM"

A company says they need a CRM. They know two things they want: track customer interactions and manage a sales pipeline. That's it — two capabilities, loosely described.

The agents don't find a "standard CRM schema" — there isn't one. Instead, they research CRM solutions across the market: enterprise platforms (Salesforce, Dynamics), mid-market (HubSpot, Pipedrive), stripped-down tools (Folk, Attio), and open-source options (Twenty, SuiteCRM). From the decomposition, they arrive at 8 common capabilities that recur across the category — contact management, pipeline tracking, activity logging, reporting/analytics, email integration, lead scoring, workflow automation, and territory management.

The two capabilities the company asked for map to contact management and pipeline tracking. But here's where patterns earn their keep: the entire CRM pattern — all 8 capabilities — gets inserted into the architecture. The two requested capabilities are adopted (active). The other six are registered as available — they exist in the architecture as typed objects with known relationships, integration patterns, and capability definitions, but they're not active yet.

This matters because it prevents patchwork architecture. Six months later, when someone asks for "better reporting on sales activity," the architecture already has `reporting-analytics` as an available capability within the CRM pattern. The agent doesn't research CRM from scratch, doesn't invent a bespoke analytics solution, doesn't bolt on a tool that overlaps with pipeline tracking. It activates an existing capability within a coherent pattern that was designed for this category from the start.

The company gets bootstrapped to an industry-standard solution architecture — even if they never buy the SaaS. The pattern carries the domain knowledge: what capabilities CRM encompasses, how they relate, what integration patterns connect them to the rest of the business. Whether they implement with Salesforce, a Supabase schema, or spreadsheets is an infrastructure decision. The architecture is the same.

---

## Semantic Coherence: Measurement and Maintenance

The architecture provides the memory. Patterns provide the operations. Semantic coherence tells you whether it's all working — and keeps it working over time.

### The Coherence Formula

```
SC = (Availability × Consistency × Stability)^(1/3)
```

Geometric mean — any dimension at zero collapses the entire score. This structural choice reflects a key insight: context quality is not additive. Perfect availability with zero consistency (contradictory information flooding the window) produces zero useful context.

| Dimension | What It Measures | What a Low Score Means |
|---|---|---|
| **Availability** | Can the agent find what it needs? | Retrieval failure — the agent will hallucinate or ask unnecessary questions |
| **Consistency** | Is the information coherent and non-contradictory? | Context pollution — the agent will produce incoherent output |
| **Stability** | Does the same query produce the same context over time? | Context drift — the agent's behavior becomes non-deterministic across runs |

SC is a **leading indicator** of context quality. It tells you that context failures are accumulating before any specific agent fails. A dropping Stability score means staleness is building up. A dropping Consistency score means vocabulary is diverging. A dropping Availability score means retrieval is degrading. Each dimension maps directly to the context failure modes that agent systems encounter.

### Runtime Agents: Continuous Reconciliation

The coherence score diagnoses problems. Runtime agents fix them. These agents continuously reconcile the real world against what the architecture claims — not as scheduled maintenance, but as part of normal operations:

**Architecture audit** — `/arch-sync` and `/global-arch-sync` reconcile the state of every repo against the registry. When drift is detected, it's a metadata update event, not a breakage event — the system surfaces the gap, and the reaction can be automatic correction or human review. This catches context rot (information that has decayed) and staleness (old versions served when newer ones exist).

**Intake evaluation** — `/intake` evaluates whether new input matches the domain model. New content, new entities, new relationships all pass through classification and quality gates before entering the governed corpus. This prevents context poisoning (bad data entering the system) and maintains provenance.

**Project review** — `/project-review` checks deliverables against project specs. This catches drift between what was planned and what's being built — a different kind of staleness that accumulates within active work, not just in stored data.

**Ingestion diff** — When content enters the corpus through semantic ingestion, the pipeline diffs against what's already stored. Changed entities, new relationships, updated attributes — each is a signal that the existing corpus may be stale. This is another audit surface: not a scheduled check, but an organic consequence of the system operating.

Each layer catches what the previous layer missed. Runtime alignment catches staleness at the point of use. Batch audit catches systemic drift. Ingestion diff catches changes at the point of entry. Coherence measurement catches the accumulated effect. Four layers, operating at different granularities and different points in the lifecycle, make it progressively harder for stale or bad data to survive to the point where an agent acts on it.

### Agentic Lineage: Making It Observable

Every agent session is an **episode** — an immutable record of what was in the context window at decision time, which tools were called in what sequence, and what artifacts were produced.

| Primitive | What Lineage Records |
|-----------|---------------------|
| **Model** | Which model, which version, inference parameters |
| **Context** | Context snapshot — what was in the window at decision time |
| **Prompt** | Which skill definition, which instructions were active |
| **Memory** | What was retrieved from Memory into Context, what was persisted |
| **Tools** | Decision trail — which tools were called, in what sequence, with what results |

Design-time traceability (coherence scoring) says "this capability should implement this pattern." Runtime traceability (agentic lineage) records "this agent DID use these primitives for this pattern with this context." Together they close the loop — and when an agent produces bad output, lineage traces it to the root cause:

```
Agent produces bad output
    │
    ▼
Agentic lineage → check context snapshot
    │
    ├── Low Availability? → retrieval failure (Memory → Context pipeline broken)
    ├── Low Consistency?  → context pollution (conflicting information in window)
    └── Low Stability?    → context drift (underlying sources changed between runs)
```

### The Flywheel

The structural approach compounds in a way that bottom-up context engineering cannot:

```
Encode business into architectural data structures
    → Agents/workflows operate on governed, traceable context
        → Their outputs feed back through semantic ingestion
            → The corpus grows, coherence measurement refines
                → Next cycle has better context to operate on
```

Agent outputs feed back through the same semantic ingestion pipeline that built the corpus in the first place, so the system updates as it operates. The architecture doesn't just serve context — it improves with use.

### Two Modes: Operate and Develop

The substrate supports two fundamentally different modes of work, and the boundary between them is what makes the architecture safe to evolve.

**Operations** is the company doing business. Agents follow the graph: governed corpus for knowledge, decision documents for reasoning, episodic lineage for traceability. Governance isn't separate from operations — it IS operations. The metadata maintenance loop and the operational loop are the same loop.

**Development** is where new ideas live. Agents are free to explore — high-reasoning models, branching experiments, speculative analysis. No guardrails on inference cost because the point is discovery.

**The decision gate** is the boundary between them. When development produces something worth operationalizing, it passes through a formal decision: an architectural artifact that captures what was decided, why, and what trade-offs were accepted. That artifact becomes part of the governed substrate. You reason deeply once, the decision persists, and operations consumes it cheaply forever.

### Example: Ridgeline Merch Planning — SC: 0.52

This is where context engineering gets hard. Ridgeline's merchandising team makes $2M+ in annual buying decisions in Slack threads and voice huddles. "Go deep on Apex" means "double the buy quantity on the Apex Shell" — but only if you know Apex is a product line, not a vendor.

The dimensional breakdown tells the coherence story:

- **Availability: 0.60** — 782 threads extracted, but 40% of decision context lives in DMs or voice huddles with no text record. Only 61% of identified buy decisions have rationale attached.
- **Consistency: 0.50** — Zero UL terms exist for buying decisions. "Go deep," "pull back," "kill the colorway" are the actual vocabulary. Product names resolve (88% automated) but the merchandising decision language is entirely unmodeled.
- **Stability: 0.48** — Threads older than 6 months lose 45% of their context to team turnover. The previous merchandising director's decisions have zero traceable rationale. Buy decision rationale has a 9-month shelf life (one seasonal cycle).

This is context rot, fragmentation, and staleness compounding. And the geometric mean ensures the damage compounds: 0.48 Stability drags the composite to 0.52 regardless of how well you extract the threads.

The prescription isn't "build a smarter agent." It's "fix the substrate." A structured decision capture workflow — tagged Slack messages extracted as records with product, quantity, rationale, and date — would move Availability from 0.60 toward 0.80. Adding merchandising terms to the UL would move Consistency from 0.50 toward 0.70. But Stability requires behavior change: decisions need to be captured when they're made, not reconstructed from thread archaeology months later. That's a governance problem, not a technology problem — and the SC score is what diagnosed it.

---

## The Pattern Across All Three

| Surface | SC | Root Cause | What Fixes It |
|---|---|---|---|
| Vendor specs (0.91) | High | Fragmentation — data exists but isn't wired to surfaces | Architecture — connect existing governed data |
| Support tickets (0.68) | Medium | Confusion — vocabulary gap between domains | Patterns — expand UL, propagates automatically |
| Merch planning (0.52) | Low | Rot + Fragmentation + Staleness compounding | Coherence — measurement diagnoses, governance fixes |

The progression reveals what top-down context engineering means in practice. The highest-coherence surface needed an architectural connection, not an agent. The medium surface needed a vocabulary fix that propagated through the pattern chain. The lowest surface needed governance — human behavior change that no agent can substitute for, but that the coherence score diagnosed.

None of these prescriptions started with "build an agent." Seven of the twelve prescriptions across the full engagement are agents, but they come in Phases 2 and 4 — after the architectural connections and data quality foundations are in place. The agent vs. workflow decision is a consequence of solving context structurally, not a starting point.

---

## Why the Primitives Collapse

For three of five agent primitives, the architecture narrows the design space to the point where the right answer is obvious:

| Primitive | Traditional Approach | What SemOps Provides |
|---|---|---|
| **Memory** | Build per agent — vector store, retrieval logic | The encoded business IS memory. Exists before the agent does. |
| **Context** | Engineer per agent — select documents, build retrieval | Bounded contexts scope it. The answer follows from the problem boundary. |
| **Tools** | Configure per agent — pick APIs, build integrations | Pattern → Capability chain narrows the options. The answer follows from the architecture. |
| **Prompt** | Engineer per agent | Domain-grounded — patterns and UL give prompts structured objects to reference. Thin prompts, not long preambles. |
| **Model** | Select per agent | Architecture-independent — genuine choice point. Better context reduces how much the model compensates. |

Memory, Context, and Tools are deterministic. Prompt and Model selection are where genuine judgment lives. That's the ratio you want: most of the agent design follows from the architecture, and the creative work is concentrated where it matters.

---

## Agent vs. Workflow Doesn't Matter

When context is solved structurally, the orchestration pattern is a local optimization. An intake evaluation is agentic — the LLM decides how to classify and route. An architecture sync is a deterministic workflow — it runs the same structural checks every time. A knowledge base query is a simple retrieval tool. All three operate on the same encoded structure, produce governed output, and feed the same ingestion flywheel.

Use an agent when you need autonomy. Use a workflow when you need determinism. Use a human when you need judgment. The encoded structure supports all three equally because it's not coupled to any of them.

This is why advanced patterns — multi-agent orchestration, reflection loops, dynamic tool generation — evolve naturally from simple workflows. You start with a deterministic workflow, and if the problem grows, you graduate to a more autonomous method. Same architecture, same data, different orchestration on top. The evolution is cheap because the prerequisites — memory, governance, traceability — are already in place at every layer.

---

## Sources

### Primary (Defining References)

- [Building Effective Agents — Anthropic (2024)](https://www.anthropic.com/research/building-effective-agents)
- [Effective Context Engineering for AI Agents — Anthropic (2025)](https://www.anthropic.com/engineering/effective-context-engineering-for-ai-agents)
- [Nine Context Failures — Chris Lema](https://chrislema.com/ai-context-failures-nine-ways-your-ai-agent-breaks/)
- [Context Engineering Survey — Arxiv 2507.13334](https://arxiv.org/html/2507.13334v1)
- [LLM Powered Autonomous Agents — Lilian Weng (2023)](https://lilianweng.github.io/posts/2023-06-23-agent/)
- [A Survey on LLM-based Autonomous Agents — Wang et al. (2024)](https://arxiv.org/abs/2308.11432)

### Frameworks and SDKs

- [OpenAI Agents SDK](https://openai.github.io/openai-agents-python/)
- [AWS Strands Agents](https://strandsagents.com/latest/)
- [Google Cloud Agents Whitepaper](https://ppc.land/google-cloud-releases-comprehensive-agentic-ai-framework-guideline/)
- [LangChain/LangGraph](https://blog.langchain.com/how-to-think-about-agent-frameworks/)
- [Microsoft Agent Framework](https://learn.microsoft.com/en-us/agent-framework/overview/)

### Practitioner

- [Agents — Chip Huyen (2025)](https://huyenchip.com/2025/01/07/agents.html)
- [Andrew Ng — Agentic Design Patterns (2024)](https://learn.deeplearning.ai/courses/agentic-ai/information)
