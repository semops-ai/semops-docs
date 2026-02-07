---
doc_type: hub

pattern: ddd-acl-governance-aas

provenance: 3p

metadata:
    pattern_type: concept
    brand_strength: low
---

# Anti-Corruption Layer (ACL)

An **anti-corruption layer** is an isolating layer that sits between two [bounded contexts](domain-driven-design.md), translating requests and responses to prevent the downstream context's domain model from being corrupted by the upstream context's model. It protects the integrity of the core domain.

## Definition (Evans)

From [Domain-Driven Design](domain-driven-design.md) by Eric Evans (2003):

> "Create an isolating layer to provide clients with functionality in terms of their own domain model. The layer talks to the other system through its existing interface, requiring little or no modification to the other system. Internally, the layer translates in both directions as necessary between the two models."

**Key principle:** The domain model should not be compromised by external systems—translate at the boundary.

---

## Core Characteristics

### 1. Translation in Both Directions

ACLs translate requests and responses:
- **Inbound:** External model → Internal model
- **Outbound:** Internal model → External model
See [Example 1](#example-1)

### 2. Protects Domain Invariants

ACLs enforce internal domain rules even when external data does not:
- Validate incoming data against internal constraints
- Reject invalid translations
- Apply domain transformations
- Ensure internal model consistency
See [Example 2](#example-2)

### 3. Isolates Internal Model from External Changes

When upstream system changes its model, ACL absorbs the impact:
- Only the ACL needs updating
- Internal domain model remains stable
- Tests verify translation correctness
- [Semantic coherence](../SEMANTIC_OPTIMIZATION/semantic-coherence.md) is preserved

**Example:** Upstream changes `full_name` to `first_name` + `last_name`:
```python
# Before: external_record['full_name']
# After: f"{external_record['first_name']} {external_record['last_name']}"

# Internal Entity remains unchanged:
# attributes["name"] still works
```

### 4. Makes Integration Explicit

ACLs document integration contracts:
- What external systems are depended on
- How external models map to internal models
- What transformations are applied
- What edge cases exist

**The pattern:** ACLs are **living documentation** of cross-context integration.

## Anti-Corruption Layer vs. Other Integration Patterns

From DDD's Context Mapping patterns:

| Pattern                   | When to Use                                           | Coupling | Corruption Risk              |
| ------------------------- | ----------------------------------------------------- | -------- | ---------------------------- |
| **Anti-Corruption Layer** | External system not under team control, poor model quality | Low      | None - ACL protects          |
| **Conformist**            | External system, downstream adopts their model as-is          | High     | High - no protection         |
| **Customer/Supplier**     | Upstream owns model, downstream adapts                | Medium   | Medium - some influence      |
| **Shared Kernel**         | Two contexts share common model subset                | High     | Medium - coordination needed |
| **Open Host Service**     | Downstream exposes API, others integrate                      | Low      | None - downstream controls           |

**Use ACL when:**
- Upstream model is poorly designed
- Upstream system is not under team control
- Upstream changes frequently
- Internal model quality is strategic priority

## When NOT to Use an ACL

ACLs add complexity—use them only when necessary:

### Do Not Use ACL If:

1. **The team controls the upstream system**
   - Fix the upstream model instead
   - Use Shared Kernel or Customer/Supplier pattern

2. **Upstream model is high quality**
   - If their model is good, adopt it (Conformist pattern)
   - Example: Stripe API, Twilio API (well-designed)

3. **Integration is trivial**
   - Simple 1:1 field mapping does not need ACL
   - Overhead is not worth the complexity

4. **The team is the upstream**
   - Expose an Open Host Service instead
   - Let downstream contexts build their own ACLs if needed

**The test:** If upstream model corruption would damage the domain model's integrity, use an ACL.

---

## How to Build an Anti-Corruption Layer

### Step 1: Define Internal Domain Model First

**Do this BEFORE looking at external system:**
- What entities exist in the domain?
- What is the ubiquitous language?
- What invariants must hold?
- What value objects encapsulate domain logic?

**Why:** The domain model should be driven by the domain, not by external APIs.

### Step 2: Analyze External Model

Understand upstream system:
- What entities/objects does it expose?
- What terminology does it use?
- What fields/attributes are available?
- What constraints/invariants does it enforce (or not)?

### Step 3: Define Translation Rules

Map external model → internal model:
- Entity mapping (external `Customer` → internal `Entity`)
- Attribute mapping (external `full_name` → internal `attributes["name"]`)
- Value transformations (external status codes → internal enum)
- Aggregation (multiple external records → one internal entity)
- Splitting (one external record → multiple internal entities)

### Step 4: Implement ACL Interface

Create a service that provides internal domain operations using external system:

```python
class ExternalSystemACL:
    """Provides domain operations by translating external system."""

    def __init__(self, external_api_client):
        self.external_api = external_api_client

    def fetch_customer_entity(self, customer_id: str) -> Entity:
        """Fetch customer as internal Entity."""
        external_customer = self.external_api.get_customer(customer_id)
        return self._translate_to_entity(external_customer)

    def create_customer_entity(self, entity: Entity) -> str:
        """Create customer in external system from Entity."""
        external_payload = self._translate_from_entity(entity)
        response = self.external_api.create_customer(external_payload)
        return response['customer_id']

    def _translate_to_entity(self, external_record: dict) -> Entity:
        """Internal translation logic."""
        # Validation, transformation, mapping
        pass

    def _translate_from_entity(self, entity: Entity) -> dict:
        """Internal translation logic."""
        # Validation, transformation, reverse mapping
        pass
```

### Step 5: Test Translation Correctness

Write tests for ACL:
- Valid external records → valid internal entities
- Invalid external records → exceptions (protect invariants)
- Round-trip (internal → external → internal) preserves semantics
- Edge cases (null fields, missing data, unexpected values)

**The ACL is the integration contract—test it thoroughly.**

---

## Anti-Patterns: When ACLs Fail

### 1. **Leaky ACL**

External model details leak into internal code:
- **Symptom:** Internal code checks for external field names or status codes
- **Result:** Internal model is corrupted by external concerns

**Fix:** ACL must be complete—internal code never touches external model.

### 2. **Bidirectional Coupling**

ACL depends on both external and internal models tightly:
- **Symptom:** Changing either side breaks the ACL
- **Result:** Brittle integration, high maintenance cost

**Fix:** ACL should adapt to external changes without forcing internal changes.

### 3. **No Validation**

ACL blindly translates without enforcing invariants:
- **Symptom:** Invalid external data creates invalid internal entities
- **Result:** Domain model is corrupted by bad external data

**Fix:** ACL must validate and reject invalid translations.

### 4. **God Object ACL**

One giant ACL handles all external integrations:
- **Symptom:** ACL knows about every external system and every internal entity
- **Result:** Unmaintainable complexity

**Fix:** One ACL per external system; composition over monolith.

---

## ACL Enables AI-Driven Pattern Adoption

ACLs allow **safe acquisition of domain patterns via AI**:

### 1. AI-Assisted ACL Generation

Given:
- Internal domain model ([ubiquitous language](domain-driven-design.md), entities)
- External API spec (OpenAPI, GraphQL schema)

AI can generate ACL translation layer:
```python
# Prompt: "Generate ACL to translate Stripe Customer API to internal Entity model"
# AI output: Complete CustomerACL with translation methods
```

---

## Why It Matters for SemOps

Anti-corruption layers are **semantic firewalls**—they enforce boundaries where semantic integrity matters most. This is fundamental to:

- **[Governance as a strategy](../STRATEGIC_DATA/governance-as-strategy.md)** - ACLs actively prevent semantic drift at boundaries
- **Domain pattern adoption** - Ensure the foundation starts with proven patterns, not inheriting bad designs
- **[Semantic coherence](../SEMANTIC_OPTIMIZATION/semantic-coherence.md)** - Protect internal [ubiquitous language](domain-driven-design.md) from external terminology
- **Organizational commitment** - ACLs make semantic ownership explicit

**For AI transformation:** ACLs define where AI agents must translate between domain languages. Without ACLs, AI inherits semantic confusion from every system it integrates with.

### Real Example SemOps

The `ike-semantic-ops` repository uses ACLs when integrating external semantic standards:

### Edge Predicates as ACL

**Problem:** External ontologies (Schema.org, PROV-O, Dublin Core) have their own vocabularies for relationships.

**Solution:** `EdgePredicate` enum serves as an ACL, mapping external vocabularies to internal ubiquitous language:

```python
class EdgePredicate(str, Enum):
    """Internal ubiquitous language for relationships."""
    CITES = "cites"
    DEPENDS_ON = "depends_on"
    DERIVED_FROM = "derived_from"
    VERSION_OF = "version_of"
    RELATED_TO = "related_to"

class SemanticStandardsACL:
    """Translate between internal predicates and external ontologies."""

    SCHEMA_ORG_MAPPING = {
        "cites": "schema:citation",
        "depends_on": "schema:isBasedOn",
        "derived_from": "prov:wasDerivedFrom",
        "version_of": "schema:isVariantOf"
    }

    PROV_O_MAPPING = {
        "derived_from": "prov:wasDerivedFrom",
        "cites": "prov:wasAttributedTo"
    }

    def to_schema_org(self, predicate: EdgePredicate) -> str:
        """Translate internal predicate to Schema.org vocabulary."""
        return self.SCHEMA_ORG_MAPPING.get(predicate.value, "schema:relatedLink")

    def from_schema_org(self, schema_org_term: str) -> EdgePredicate:
        """Translate Schema.org term to internal predicate."""
        reverse_map = {v: k for k, v in self.SCHEMA_ORG_MAPPING.items()}
        internal = reverse_map.get(schema_org_term, "related_to")
        return EdgePredicate(internal)
```

**Benefits:**
1. **Internal code** uses clean, domain-specific predicates (`cites`, `depends_on`)
2. **External export** uses standard ontology terms (`prov:wasDerivedFrom`)
3. **Translation is explicit** and testable
4. **Internal model is protected** from external vocabulary changes

### ACL Aligns with "Governance as Strategy"

From the Ike Framework concept [Governance as Strategy](../STRATEGIC_DATA/governance-as-strategy.md):

**ACLs are the enforcement mechanism for semantic governance:**

1. **Offense, not defense**
   - ACLs actively prevent bad models from entering the domain
   - Governance enables pattern adoption (not just compliance)
   - Semantic state is versioned and measurable

2. **Schema = Meaning**
   - ACL is a schema transformation layer
   - Every data transformation is a first-class process
   - Lineage tracks how external data becomes internal entities

3. **Semantic coherence at runtime**
   - ACLs enforce ubiquitous language at integration points
   - Translation rules are explicit and testable
   - Drift is detectable (external model vs. ACL mapping)

**The pattern:** ACLs allow teams to **acquire patterns and apply them through AI confidently** because the semantic state is protected from external corruption.

---

### ACL and Strategic Data Practices

ACLs embody the [Strategic Data](../STRATEGIC_DATA/README.md) principle that **schema = meaning**:

### 1. ACL as Schema Transformation

External schema → ACL → Internal schema

```python
# External schema (upstream API)
{
    "customer_id": "C12345",
    "full_name": "Jane Doe",
    "contact_email": "jane@example.com",
    "status_code": "ACTIVE",
    "created_timestamp": "2024-01-15T10:30:00Z"
}

# ACL transforms to internal schema
Entity(
    entity_id="customer-C12345",
    entity_type=EntityType.CUSTOMER,
    attributes={
        "name": "Jane Doe",
        "email": "jane@example.com",
        "status": "active",
        "created_at": datetime(2024, 1, 15, 10, 30, 0)
    }
)
```

### 2. ACL Preserves Semantic Intent

External systems often have **implementation-focused** schemas (technical field names, codes, IDs).

ACLs translate to **domain-focused** schemas (ubiquitous language, value objects, domain types).

**Example:**
```python
# External: Technical implementation details
{
    "rec_id": 78493,
    "rec_type": 3,
    "field_1": "Product A",
    "field_2": 29.99,
    "field_3": "USD"
}

# ACL translates to domain semantics
Product(
    product_id="product-78493",
    name="Product A",
    price=Money(amount=29.99, currency="USD")
)
```

### 3. ACL as Semantic Lineage

ACLs make **data provenance explicit**:
- Where did this data come from? (external system)
- How was it transformed? (ACL translation rules)
- When did it enter the domain? (timestamp)
- What external entity does it correspond to? (external ID preserved in attributes)

**Example:**
```python
Entity(
    entity_id="customer-C12345",
    entity_type=EntityType.CUSTOMER,
    attributes={
        "name": "Jane Doe",
        "email": "jane@example.com",
        "_external_source": "legacy_crm",
        "_external_id": "C12345",
        "_imported_at": "2024-11-27T14:22:00Z"
    }
)
```

---


### 2. ACL as Pattern Template

Once ACLs have been built for common systems (Salesforce, Stripe, HubSpot), they become **reusable patterns**:
- ACL for CRM system → template for all CRM integrations
- ACL for payment processor → template for all payment integrations

**AI can apply these patterns to new integrations.**

### 3. Semantic Coherence Measurement

ACLs make semantic drift **measurable**:
```python
# Compare external model to internal model via ACL
coherence_score = measure_translation_fidelity(
    external_records=external_api.get_all_customers(),
    acl=CustomerACL,
    internal_entities=entity_repository.get_all_customers()
)
```

If ACL translation is lossy or distorting, coherence score drops.

---

## Key Takeaways

1. **ACLs protect domain model integrity**
   - External systems cannot corrupt internal ubiquitous language
   - Translation is explicit and controlled
   - Internal model remains stable despite external changes

2. **ACLs are semantic firewalls**
   - Governance as strategy: active prevention of corruption
   - Schema = meaning: ACL transforms external schema to internal semantics
   - Lineage: ACL is the provenance layer for external data

3. **ACLs enable pattern adoption**
   - Integrate with poorly-designed systems without inheriting bad design
   - Acquire external patterns confidently (ACL isolates risk)
   - AI can generate ACLs when domain model is well-defined

4. **Use ACLs selectively**
   - Do not add complexity for trivial integrations
   - Use when upstream model quality is poor or the team lacks control
   - One ACL per external system

5. **ACLs are testable contracts**
   - Translation rules are explicit
   - Validation enforces invariants
   - Tests verify correctness

**The core insight:** Semantic coherence cannot be maintained if external chaos enters the domain unchecked—ACLs are the boundary defense.

---

## Examples
### Example 1
```python
# External system (upstream) uses "customer_record"
# Internal system (downstream) uses "Entity"

class CustomerACL:
    def fetch_entity(self, external_id: str) -> Entity:
        """Translate external customer record to internal Entity."""
        customer_record = external_api.get_customer(external_id)

        # Translation layer
        return Entity(
            entity_id=f"customer-{customer_record['id']}",
            entity_type=EntityType.CUSTOMER,
            attributes={
                "name": customer_record['full_name'],
                "email": customer_record['contact_email'],
                "status": self._map_status(customer_record['status'])
            }
        )

    def _map_status(self, external_status: str) -> str:
        """Map external status codes to internal domain language."""
        mapping = {
            "ACTIVE": "active",
            "SUSPENDED": "inactive",
            "DELETED": "churned"
        }
        return mapping.get(external_status, "unknown")
```
### Example 2
```python
def create_order_from_external(self, external_order: dict) -> Order:
    """Create internal Order from external system."""

    # ACL enforces internal invariants
    if external_order['total_amount'] <= 0:
        raise ValueError("Order total must be positive")

    if not external_order.get('customer_id'):
        raise ValueError("Order must have customer")

    # Transform to internal model
    return Order(
        order_id=generate_order_id(),
        customer=self.customer_acl.fetch_entity(external_order['customer_id']),
        line_items=self._transform_line_items(external_order['items']),
        total=Money(external_order['total_amount'], external_order['currency'])
    )
```


## Related Links

### Citations

- Evans, Eric. *Domain-Driven Design: Tackling Complexity in the Heart of Software*. Addison-Wesley, 2003. (Chapter 14: Maintaining Model Integrity)
- Vernon, Vaughn. *Implementing Domain-Driven Design*. Addison-Wesley, 2013. (Chapter 3: Context Maps)

### Builds On

- [Domain-Driven Design](domain-driven-design.md) - Core DDD concepts
- [Patterns and Bounded Contexts](patterns-and-bounded-contexts.md) - Where ACLs apply
- [Governance as Strategy](../STRATEGIC_DATA/governance-as-strategy.md) - ACLs as governance enforcement

### See Also

- [DDD Solves AI Transformation](ddd-solves-ai-transformation.md) - ACLs enable AI integration
- [Strategic Data](../STRATEGIC_DATA/README.md) - Schema transformations and lineage
