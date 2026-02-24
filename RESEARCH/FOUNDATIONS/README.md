---
pattern: research-foundations

provenance: 3p

metadata: 
 doc_type: structure
 brand-strength: low
# this doc is a fairly straight ahead structure doc that's synthesizing 3p concepts - reviewed and merged content from semantic-foundations.md and hub-first-principles.md (now deleted)
---

# Foundations

> Initial examinations into understanding AI in a business context led in two directions: low level technical investigation, and high level human behaviors. First principles thinking was a critical exercise, and it led to these foundational theories and frameworks which provided insights and understanding where current trends and pure technology understanding could not.

---

## Top 5

## The Semantic Funnel (OAR + DIKW)

> A mental model combining **Objects, Agents, Rules (OAR)** with the **DIKW hierarchy** to describe how meaning flows through systems. Objects transform through progressive stages of meaning (Data → Wisdom), operated on by Agents, constrained by Rules at every level.

**What it is:**
The Semantic Funnel integrates two frameworks:

- **OAR (Objects, Agents, Rules)**: The fundamental entities in any system—things that exist (Objects), things that decide (Agents), and things that constrain (Rules)
- **[DIKW](dikw-theory-comparison.md) Hierarchy**: Progressive transformation of Data → Information → Knowledge → Wisdom, originally formalized by [Ackoff (1989)](https://doi.org/10.1016/0378-7206(89)90062-1)

This foundational framework acts as the mental model for virtually all ideas pursued in this project. See: [The Semantic Funnel](semantic-funnel.md)

**Why it matters:**
The Semantic Funnel underpins the Ike Framework as a mental bridge between data and deeper meaning. It explains:

- Why data systems require semantic structure (Data → Information transformation)
- How [Semantic Coherence](../../SEMANTIC_OPERATIONS_FRAMEWORK/SEMANTIC_OPTIMIZATION/semantic-coherence.md) enables shared Knowledge across organizations
- Why Understanding is a process, not a state—requiring continuous interpretation and context
- The foundation for [Semantic Optimization](../../SEMANTIC_OPERATIONS_FRAMEWORK/SEMANTIC_OPTIMIZATION/README.md): measuring and maintaining meaning at runtime
- Why AI needs structured data: LLMs operate at the Information/Knowledge level but cannot bootstrap Understanding without human-injected semantics
- How Rules encode understanding at each DIKW level (structural, interpretive, normative, meta-rules)

**Sources:**

- Ackoff, R.L. (1989). "From Data to Wisdom." *Journal of Applied Systems Analysis*, 16, 3-9. https://doi.org/10.1016/0378-7206(89)90062-1
- Rowley, J. (2007). "The wisdom hierarchy: representations of the DIKW hierarchy." *Journal of Information Science*, 33(2), 163-180. https://doi.org/10.1177/0165551506070706
- Agent-Based Modeling: https://en.wikipedia.org/wiki/Agent-based_model
- Business Rules: https://en.wikipedia.org/wiki/Business_rule
- See [The Semantic Funnel](semantic-funnel.md) for comprehensive treatment

### Domain-Driven Design (DDD)

**What it is:**
A software development philosophy emphasizing that software models should be deeply tied to the business domain they serve, using a [ubiquitous language](../../SEMANTIC_OPERATIONS_FRAMEWORK/EXPLICIT_ARCHITECTURE/domain-driven-design.md) shared between technical and domain experts within explicit [bounded contexts](../../SEMANTIC_OPERATIONS_FRAMEWORK/EXPLICIT_ARCHITECTURE/domain-driven-design.md), as defined by [Evans (2003)](https://www.domainlanguage.com/ddd/).

**Key ideas:**
- **Ubiquitous Language**: Shared vocabulary between business and technical teams that becomes the code
- **Bounded Contexts**: Explicit boundaries where specific models and language apply
- **Domain Models**: Software structures that accurately represent business reality, not just technical requirements
- **Strategic Design**: Context mapping, anti-corruption layers, continuous collaboration with domain experts
- Models evolve with business understanding

**Why it matters:**
DDD is the bedrock for architecting systems tied to human organizations and meaning. It provides:
- The architectural foundation for encoding business semantics into technical systems (schema as meaning)
- Framework for [Semantic Coherence](../../SEMANTIC_OPERATIONS_FRAMEWORK/SEMANTIC_OPTIMIZATION/semantic-coherence.md) within Bounded Contexts
- Pattern for managing semantic boundaries and translations between contexts
- Foundation for the Ike Framework's emphasis on "meaning down" vs "system up" thinking
- Why organizational structure and data architecture must align (Conway's Law)
- How to build systems where code and business language are one (Ubiquitous Language)

The Ike Framework applies this principle throughout—domain models that reflect organizational meaning, not just technical convenience.

**Sources:**
- Evans, E. (2003). *Domain-Driven Design: Tackling Complexity in the Heart of Software*. Addison-Wesley. ISBN: 978-0321125217. https://www.domainlanguage.com/ddd/
- Vernon, V. (2013). *Implementing Domain-Driven Design*. Addison-Wesley. ISBN: 978-0321834577
- https://www.domainlanguage.com/
- See [Explicit Architecture](../../SEMANTIC_OPERATIONS_FRAMEWORK/EXPLICIT_ARCHITECTURE/README.md) for full treatment

### Systems Theory & Cybernetics

**What it is:**
A science of feedback, control, and system-level behavior that studies how systems maintain stability, adapt, and evolve through feedback loops and emergent properties, pioneered by [Wiener (1948)](https://mitpress.mit.edu/9780262537087/) in his foundational work on cybernetics, extended by [Beer (1972)](https://www.wiley.com/en-us/Brain+of+the+Firm-p-9780471948391) with the Viable Systems Model, [Ackoff (1981)](https://www.wiley.com/en-us/Creating+the+Corporate+Future-p-9780471090113) with systems thinking for organizations, and [Meadows (2008)](https://www.chelseagreen.com/product/thinking-in-systems/) with her analysis of leverage points in complex systems.

**Key ideas:**
- **Feedback Loops**: Positive (amplifying) and negative (stabilizing) feedback drive system behavior
- **System Viability (Beer)**: Organizations as viable systems requiring balance and regulation
- **Organizational Cybernetics**: How organizations self-regulate through information flows
- **Systems Thinking (Meadows)**: Understanding interrelationships over isolated components
- **Emergence**: System properties that arise from interactions, not individual parts
- **Homeostasis**: Systems maintain stability through self-regulation

**Why it matters:**
Systems Theory provides the measurement bridge to semantic drift and a way to operationalize meaning:
- **Semantic Drift as System Behavior**: Meaning degrades through feedback loops—detecting drift requires measuring semantic coherence over time
- **Foundation for [Semantic Optimization](../../SEMANTIC_OPERATIONS_FRAMEWORK/SEMANTIC_OPTIMIZATION/README.md)**: Continuous measurement, reinforcement, and improvement of shared understanding
- **Error Propagation**: Semantic errors compound through system feedback—small misalignments amplify
- **Organizational Cybernetics**: Why data governance must be an active feedback system, not static rules
- **Viable Systems Model**: Organizations need real-time semantic coherence to function (like homeostasis)
- **Leverage Points**: Small interventions in semantic systems (like conformed dimensions) can have outsized impact

This provides the theoretical basis for treating semantic coherence as a measurable, controllable system property rather than a one-time design decision.

**Sources:**
- Wiener, N. (1948). *Cybernetics: Or Control and Communication in the Animal and the Machine*. MIT Press. ISBN: 978-0262537087. https://mitpress.mit.edu/9780262537087/
- Beer, S. (1972). *Brain of the Firm*. Wiley. ISBN: 978-0471948391. https://www.wiley.com/en-us/Brain+of+the+Firm-p-9780471948391
- Ackoff, R.L. (1981). *Creating the Corporate Future: Plan or be Planned For*. Wiley. ISBN: 978-0471090113
- Meadows, D.H. (2008). *Thinking in Systems: A Primer*. Chelsea Green. ISBN: 978-1603580557. https://www.chelseagreen.com/product/thinking-in-systems/

## Formal Ontology Engineering
*(W3C, OWL, RDF, Description Logics)*

**What it is:**
A discipline for defining the *meaning* of concepts, relationships, and constraints using machine-interpretable structures.

**Key ideas:**
- Classes, properties, individuals
- Subsumption hierarchies
- Ontology alignment
- Description logics (DL) used for inference
- W3C standards for semantic interoperability

**Why it matters:**
This is the backbone of semantic precision, grounding, and consistency across systems.

**Sources:**
- W3C OWL Overview: https://www.w3.org/OWL/
- RDF Primer: https://www.w3.org/TR/rdf11-primer/
- Baader et al., *The Description Logic Handbook*: https://dl.kr.org/
- Protégé Ontology Editor (Stanford): https://protege.stanford.edu/


## Data Lineage & Provenance
*(W3C PROV)*

**What it is:**
Lineage tracks data flow through systems (transformations, pipelines, dependencies). Provenance tracks data origin, custody, and derivation (who created it, when, why, under what conditions).

**Key ideas:**
- Lineage: What happened to data within a system
- Provenance: Where data came from before entering the system
- Complete audit trail from source to decision
- Impact analysis and root-cause debugging
- Trust chain verification

**Why it matters:**
Data cannot be trusted without knowing its lineage and provenance. Essential for debugging, governance, reproducibility, and treating data as a first-class asset.

**Sources:**
- W3C PROV Model: https://www.w3.org/TR/prov-overview/
- https://en.wikipedia.org/wiki/Data_lineage


---

### Information Theory

**What it is:**
A mathematical theory of communication that quantifies information as reduction in uncertainty, establishing the physical foundations for how data can be transmitted, stored, and compressed, as pioneered by [Shannon (1948)](https://doi.org/10.1002/j.1538-7305.1948.tb01338.x).

**Key ideas:**
- **Information Entropy**: Measure of uncertainty or information content
- **Channel Capacity**: Fundamental limits on data transmission
- **Compression Bounds**: Theoretical limits on lossless compression
- **Noise and Error Correction**: Managing signal degradation
- **Information is Physical**: Data has mass, energy, and thermodynamic properties
- Information is statistical, not semantic (Shannon explicitly excluded meaning)

**Why it matters:**
Information Theory reveals the "physicality" of data and information—a powerful insight for [Strategic Data](../../SEMANTIC_OPERATIONS_FRAMEWORK/STRATEGIC_DATA/README.md):
- **Data is Physical**: It obeys thermodynamic laws, has energy costs, cannot be created or destroyed arbitrarily
- **Structure Requires Energy**: The Data → Information transformation (DIKW) has a thermodynamic minimum energy cost
- **No Free Lunch**: Schema-on-read does not eliminate structure costs; it defers and multiplies them
- **Deterministic Operations**: Once data has structure, transformations become mathematically tractable and provably correct
- **Resource Consumption is Calculable**: Infrastructure costs are physics-constrained, not negotiable
- Why dimensional modeling works: It amortizes the structural energy cost once during ETL instead of paying repeatedly at query time

This is the theoretical foundation for why data architecture matters and why "flexible" schemas that avoid structure ultimately cost more.

**Sources:**
- Shannon, C.E. (1948). "A Mathematical Theory of Communication." *Bell System Technical Journal*, Vol. 27, pp. 379–423, 623–656. https://doi.org/10.1002/j.1538-7305.1948.tb01338.x
- Brillouin, L. (1956). *Science and Information Theory*. Academic Press. ISBN: 978-0486497556
- https://en.wikipedia.org/wiki/Information_theory

---

---

## Human Cognition and Organization

> Theories explaining how humans process information, construct knowledge, and organize work.

**Full treatment:** [Theoretical Foundations: Human Cognition and Organization](foundations-cognition.md)

- [Sociotechnical Systems Theory (STS)](foundations-cognition.md#sociotechnical-systems-theory-sts)
- [Schema Theory & Constructivism](foundations-cognition.md#schema-theory--constructivism)
- [Statistical Learning Theory](foundations-cognition.md#statistical-learning-theory)
- [Information Processing Theory](foundations-cognition.md#information-processing-theory)
- [Knowledge Work Theory](foundations-cognition.md#knowledge-work-theory)
- [Systems Thinking](foundations-cognition.md#systems-thinking)

---

## Data Foundations

> How data and data systems actually work—immutable constraints and realities.

**Full treatment:** [Theoretical Foundations: Data Foundations](foundations-data.md)

- [Relational Model & Relational Algebra](foundations-data.md#relational-model--relational-algebra)
- [Entity-Relationship Model](foundations-data.md#entity-relationship-model)
- [Dimensional Modeling](foundations-data.md#dimensional-modeling)
- [Data Warehouse Architecture](foundations-data.md#data-warehouse-architecture)
- [Statistical Foundations](foundations-data.md#statistical-foundations)
- [Data Profiling](foundations-data.md#semops-dataofiling)
- [Data Lineage & Provenance](foundations-data.md#data-lineage--provenance)
- [CAP Theorem](foundations-data.md#cap-theorem)
- [Data Governance (DAMA-DMBOK)](foundations-data.md#data-governance-dama-dmbok)
- [Reference Architecture Patterns](foundations-data.md#reference-architecture-patterns)

---

## Semantic Systems and Architecture

> Defining, representing, and managing meaning in technical systems.

**Full treatment:** [Theoretical Foundations: Semantic Systems and Architecture](foundations-semantic.md)

- [Formal Ontology Engineering](foundations-semantic.md#formal-ontology-engineering)
- [Knowledge Representation & Formal Semantics](foundations-semantic.md#knowledge-representation--formal-semantics)
- [Library & Information Science](foundations-semantic.md#library--information-science)
- [Organizational Learning & Knowledge Creation](foundations-semantic.md#organizational-learning--knowledge-creation)
- [Cognitive Systems Engineering](foundations-semantic.md#cognitive-systems-engineering)
- [Socio-Technical Systems Design](foundations-semantic.md#socio-technical-systems-design)
- [Semiotics & Meaning Theory](foundations-semantic.md#semiotics--meaning-theory)
- [Agent-Based Modeling](foundations-semantic.md#agent-based-modeling)
- [Business Rules](foundations-semantic.md#business-rules)

---
