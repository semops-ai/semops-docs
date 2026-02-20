---
doc_type: hub
pattern: aggregate-root
provenance: 1p
metadata:
    pattern_type: concept
    brand_strength: medium
---

# Aggregate Root for SemOps Agentic Enterprise

In Domain Driven Design, **Aggregate Root** is the single entity in an aggregate through which all access to the aggregate occurs. It enforces invariants for the entire aggregate and defines the transactional consistency boundary.

> "Each aggregate has a root entity (the aggregate root) and a boundary. The root is the only member of the aggregate that outside objects are allowed to hold references to. Invariants must be maintained for all changes within the aggregate boundary." - From Domain-Driven Design by Eric Evans (2003)

## Less Technical Definition

Think of an Aggregate Root as the **stable core** of a system component. Like the foundation of a building, it provides the fixed reference point that everything else connects to. External systems never reach inside to touch the internal pieces directly - they only interact with the root. This stability is what allows you to add new capabilities, connect new systems, or change internal details without breaking everything that depends on it. The root is your **contract with the outside world** - as long as it stays stable, the outside world does not need to know or care what happens inside.

---

## Characteristics

**Single Entry Point**
- External objects reference only the root
- Internal entities/value objects are hidden
- All modifications go through root methods

**Invariant Enforcement**
- Root validates all changes
- Business rules enforced at the boundary
- Invalid states are impossible

**Transactional Boundary**
- Changes to the aggregate are atomic
- Eventual consistency between aggregates
- No transactions spanning multiple aggregates

**Lifecycle Control**
- Root controls creation of internal objects
- Deleting root deletes all internal objects
- Internal objects cannot outlive the root

## SemOps Adoption

In [Semantic Operations](../README.md), the Aggregate Root pattern ensures that complex knowledge structures maintain their integrity. When a concept is the aggregate root, all related content, attributions, and delivery records are accessed and modified through the concept itself. This provides a stable API for knowledge management - external systems reference the concept, and internal details can evolve without breaking integrations.

### How the Aggregate Root Maps to the Semantic Funnel

The Aggregate Root is an **Object** that enforces Rules across all DIKW levels within a bounded domain — but it doesn't decide. Human and AI **Agents** interact with the aggregate through the root; the root constrains how Agents can act on its internal Objects.

```
  Objects:  DATA ══════> INFORMATION ══════> KNOWLEDGE ══════> WISDOM
               │                │                  │               │
  Rules:  Structural       Interpretive        Normative       Meta
          (boundary)       (invariants)        (validity)      (identity)
               │                │                  │               │
  Agents:     —                —            Domain experts    Leadership
```

| Element | D → I | I → K | K → W |
|---------|-------|-------|-------|
| **Objects** | Raw inputs captured within aggregate boundary | Domain events, value objects | Policies, context maps |
| **Agents** | — (boundary enforcement is mechanical) | — (invariant checking is mechanical once encoded) | Domain experts, leadership (define what "valid" means) |
| **Rules** | Structural — aggregate boundary, entity schemas, value object definitions | Interpretive — invariants maintained across the aggregate, consistency rules | Normative — root defines validity for this domain; single entry point as contract with outside world |
| **Determinism** | High — boundary enforcement is mechanical | High — invariant checking is deterministic once rules are encoded | Low — defining what "valid" means requires judgment |
| **Mechanism** | Single entry point constrains all access; internal objects hidden from outside | Invariants make invalid states impossible within boundary; all changes validated atomically | Transactional boundary limits scope of what can go wrong; root's contract with outside world allows internal evolution |
| **Failure mode** | Unbounded aggregates, direct access to internal objects | Invariant violations, cross-aggregate transactions | No clear validity definition, root bypassed, boundary drift |

### SemOps Aggregate Root

In SemOps, the **Concept** entity serves as the aggregate root for knowledge artifacts. Its identity is defined by its `id` plus its `foundations` (the hypothesis it embodies). Value objects like content versions, file specs, and attributions belong to the concept but have no independent identity.

**Examples:**
```text
Entity (has identity)
├── Concept    ← identity = id + foundations (the hypothesis)
└── Surface    ← identity = platform + destination
```

```
Value Object (no identity, replaceable)
├── concept_content  ← belongs to concept, versioned, deletable
├── filespec         ← just describes a file
├── attribution      ← just describes authorship
└── delivery         ← just a record of publication event
```

```yaml
concept:
  id: semantic-coherence
  provenance: 1p
  
  # CONSTITUTIVE (identity)
  foundations:
    - dikw
    - information-theory
    - lineage
    - ubiquitous-language
  claim: "Semantic alignment requires these foundations working together"
```

## General Examples

### Company Pivot = Changing Root

A pivot is identity change, not state change.
```
Pre-pivot:
  concept: "we-sell-dvds-by-mail"
  foundations: [logistics, inventory, physical-media]
```
```
Post-pivot:
  concept: "we-stream-content"
  foundations: [licensing, bandwidth, recommendation-algo]
```
That's not the same company in a DDD sense - it's a new aggregate root. The legal entity persists (Netflix Inc.), but the bounded synthesis is entirely different. The old hypothesis was falsified or abandoned; a new one was asserted. This is why pivots are so hard and risky - you're not iterating on state, you're tearing down identity and rebuilding. All your value objects (processes, code, content, team skills) were built for the old foundations. They don't transfer cleanly. A "pivot" that doesn't change foundations isn't really a pivot - it's just strategy adjustment. State change, not identity change.

**Key principle:** All access goes through the root; the root enforces all business rules.

### Order Transaction

```python
# Order is the Aggregate Root
class Order:
    order_id: str                       # Entity identity
    line_items: list[OrderLineItem]     # Internal entities
    shipping_address: Address           # Value object
    total: Money                        # Computed value object

    def add_line_item(self, product_id: str, quantity: int, price: Money):
        # Root enforces invariants
        if quantity <= 0:
            raise InvalidOrder("Quantity must be positive")
        line_item = OrderLineItem(product_id, quantity, price)
        self.line_items.append(line_item)
        self.total = self._calculate_total()

    def _calculate_total(self) -> Money:
        return sum(item.subtotal for item in self.line_items)
```

**Why Aggregate Root:**
- `Order` is the only externally accessible object
- `OrderLineItem` cannot be accessed directly
- `add_line_item()` enforces quantity > 0
- `total` is always recalculated (invariant maintained)

**Rule 1: Reference only the root**
- Good: `order = get_order(order_id)`
- Bad: `line_item = get_line_item(line_item_id)`

**Rule 2: Enforce invariants in the root**
- Good: `order.add_line_item(...)` validates and maintains total
- Bad: `order.line_items.append(...)` bypasses validation

**Rule 3: Keep aggregates small**
- Only include objects that must change together
- Large aggregates create contention

**Rule 4: Use eventual consistency between aggregates**
- Order created → event → Inventory updated (eventually)
- No transactions spanning Order and Inventory

---

## Related Links

### Citations
- Evans, Eric. *Domain-Driven Design*. Addison-Wesley, 2003. (Chapter 6: Aggregates)

### See Also

- [Domain-Driven Design](domain-driven-design.md) - The methodology that defines aggregate roots
- [Patterns and Bounded Contexts](patterns-and-bounded-contexts.md) - Aggregates live within contexts
- [What is Wisdom?](wisdom-aggregate-root.md) - Related aggregate root concept
