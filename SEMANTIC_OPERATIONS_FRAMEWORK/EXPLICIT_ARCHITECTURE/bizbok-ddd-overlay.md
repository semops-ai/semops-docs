# BIZBOK → DDD Overlay: Financial Services Worked Example

> Derived from BIZBOK Guide v14 §8.1 Financial Services Reference Model.
> Demonstrates how Business Architecture artifacts map to Domain-Driven Design
> tactical and strategic patterns.

## Method: BIZBOK → DDD Derivation

The BIZBOK already defines a step-by-step derivation path (§6.5 SOA, §6.6 Data
Architecture). We reframe that derivation using DDD terminology.

| Step | BIZBOK Source | DDD Output |
|------|--------------|------------|
| 1 | Level 1 Capability → one business object each | Bounded Context boundary |
| 2 | Business Object (anchor noun) | Aggregate Root |
| 3 | Primary Information Concept | Entity (independent lifecycle) |
| 4 | Secondary Information Concept | Entity or Value Object (dependent) |
| 5 | Information Concept States | Aggregate state machine |
| 6 | Information Concept Types | Type discriminator / subtypes |
| 7 | Information Concept Relationships | Context Map relationships |
| 8 | Capability "modifies" relationship | Write model (command side) |
| 9 | Capability "uses" relationship | Read model (query side) |
| 10 | Value Stream Stage exit criteria | Domain Event |
| 11 | Value Stream → Capability cross-map | Saga / Process Manager |

---

## Step 1: Bounded Contexts from Level 1 Capabilities

BIZBOK axiom: each L1 capability is anchored to exactly one business object.
In DDD, this is the bounded context boundary — single ownership of a
consistency boundary.

### Financial Services Core Bounded Contexts

```
┌─────────────────────────────────────────────────────────────┐
│                   STRATEGIC TIER                            │
│  (Cross-cutting contexts — shared kernel or generic)        │
│                                                             │
│  Strategy · Plan · Policy · Campaign · Brand · Market       │
│  Message · Research · Initiative · IP Rights                │
└─────────────────────────────────────────────────────────────┘

┌─────────────────────────────────────────────────────────────┐
│                     CORE TIER                               │
│  (Core domain — custom, invest heavily)                     │
│                                                             │
│  ┌──────────────┐  ┌──────────────┐  ┌──────────────┐      │
│  │  Agreement    │  │  Customer    │  │  Partner     │      │
│  │  Management   │  │  Management  │  │  Management  │      │
│  └──────────────┘  └──────────────┘  └──────────────┘      │
│                                                             │
│  ┌──────────────┐  ┌──────────────┐  ┌──────────────┐      │
│  │  Finance     │  │  Financial   │  │  Investment  │      │
│  │  Management  │  │  Instrument  │  │  Portfolio   │      │
│  └──────────────┘  └──────────────┘  └──────────────┘      │
│                                                             │
│  ┌──────────────┐  ┌──────────────┐  ┌──────────────┐      │
│  │  Order       │  │  Collateral  │  │  Product     │      │
│  │  Management  │  │  Management  │  │  Management  │      │
│  └──────────────┘  └──────────────┘  └──────────────┘      │
│                                                             │
│  ┌──────────────┐                                           │
│  │  Channel     │                                           │
│  │  Management  │                                           │
│  └──────────────┘                                           │
└─────────────────────────────────────────────────────────────┘

┌─────────────────────────────────────────────────────────────┐
│                   SUPPORTING TIER                           │
│  (Supporting subdomains — buy or good-enough)               │
│                                                             │
│  Asset · Competency · Content · Decision · Event ·          │
│  Facility · HR · Incident · Information · Inquiry ·         │
│  Interaction · Job · Language · Legal Proceeding ·          │
│  Location · Schedule · Submission · Time · Training · Work  │
└─────────────────────────────────────────────────────────────┘
```

### DDD Classification

| BIZBOK Tier | DDD Context Type | Investment Strategy |
|-------------|-----------------|---------------------|
| Core/Customer-Facing | Core Domain | Custom code, deep modeling |
| Strategic/Direction | Generic Subdomain | Configurable, possibly purchased |
| Supporting | Supporting Subdomain | Minimal viable, buy or outsource |

---

## Step 2: Aggregate Roots from Business Objects

Each L1 capability's anchor noun becomes the aggregate root.

| Bounded Context | Aggregate Root | BIZBOK Business Object |
|----------------|----------------|----------------------|
| Agreement Management | `Agreement` | Agreement |
| Customer Management | `Customer` | Customer |
| Partner Management | `Partner` | Partner |
| Finance Management | `FinancialAccount` | Financial Account (primary child) |
| Financial Instrument Mgmt | `FinancialInstrument` | Financial Instrument |
| Investment Portfolio Mgmt | `InvestmentPortfolio` | Investment Portfolio |
| Order Management | `Order` | Order |
| Collateral Management | `Collateral` | Collateral |
| Product Management | `Product` | Product |
| Channel Management | `Channel` | Channel |

---

## Step 3-4: Entities and Value Objects from Information Concepts

### Agreement Context (fully worked)

The Agreement bounded context contains the Agreement aggregate. From the
BIZBOK information map:

```
Agreement (Aggregate Root — Primary Information Concept)
│
├── AgreementTerm (Entity — Secondary, has own lifecycle)
│   States: Pending, In Force, Terminated, Abandoned
│   Types: Survivable, Non-survivable
│
├── AgreementType (Value Object — classification)
│   Values: Bilateral, Multilateral, Express, Implied
│
├── AgreementState (Value Object — current lifecycle position)
│   Values: Pending, In Force, Terminated, Abandoned
│
└── AgreementPreference (Value Object — derived from L2 capability)
    Explicit (customer choice) vs. Implicit (derived from behavior)
```

**Derivation logic (BIZBOK §6.6):**
- Agreement Term is *secondary* to Agreement → it lives inside the aggregate
  but has independent identity (its own states, own lifecycle)
- Types and States from the information map become value objects or enums
- Child capabilities (L2) imply attributes:
  - Agreement Preference Mgmt → `preferences` attribute
  - Agreement Access Mgmt → `accessLevel` attribute
  - Agreement Risk Rating → `riskRating` attribute
  - Agreement Type Mgmt → `type` discriminator
  - Agreement State Mgmt → `state` machine
  - Agreement History Mgmt → `history` event log

### Finance Context (fully worked — L1 with L2 decomposition)

Finance Management is the only L1 capability in FS with a full L2 breakdown
published. Each L2 capability owns its own business object, so each branch
is its own aggregate — each with an independent consistency boundary,
its own lifecycle states, and its own invariants. They reference each
other by ID, never by direct object reference.

```
Finance (Bounded Context)
│
├── FinancialAccount (Aggregate)
│   Root: FinancialAccount
│   States: Pending, Open/Current, Closed, Suspended/Frozen
│   Types: Asset, Liability, Income, Expense, Equity/Capital
│   Owns: balance, access rules, audit history
│   Invariant: balance = sum(transactions where account = this)
│   L2 Capability: Financial Account Management
│
├── FinancialTransaction (Aggregate)
│   Root: FinancialTransaction
│   States: Executed, Pending, Rejected, Cancelled
│   Types: Sale, Purchase, Receipt, Payment, Deposit, Withdrawal
│   References: accountId (by ID, not object)
│   Invariant: once Executed, amount is immutable
│   L2 Capability: Financial Transaction Management
│
├── Payment (Aggregate — see design note below)
│   Root: Payment
│   States: Unpaid → Paid → Settled | Cancelled → Reversed
│   Types: Inbound, Outbound
│   Contains: MonetaryAmount (VO), Currency (VO)
│   References: agreementId, accountId, transactionId (by ID)
│   Invariant: settles a specific obligation between parties
│   L2 Capability: Payment Management
│   Note: Has identity (paymentId) and mutable lifecycle,
│         so Entity minimum. Own aggregate because BIZBOK gives
│         it a dedicated L2 capability (single-writer axiom).
│
├── MonetaryAmount (Value Object — embedded in Payment and others)
│   Immutable: {amount, currency} pair — value equality
│   Types: Negative, Positive, Zero
│   States: Determined, Estimated, Undetermined
│   Used by: Payment, FinancialTransaction, FinancialPosition
│   L2 Capability: Monetary Amount Management
│
├── Currency (Value Object — embedded in MonetaryAmount)
│   Immutable: currency code (CAD, USD, GBP...)
│   Types: Representational, Intrinsic
│   L2 Capability: Currency Management
│
├── FinancialForecast (Aggregate)
│   Root: FinancialForecast
│   States: Being-Prepared, Completed, Current, Historical
│   Types: Balance Sheet Projection, Cash Flow, Budget, Net-Worth
│   Invariant: only one "Current" forecast per type at a time
│   L2 Capability: Financial Forecast Management
│
├── FinancialPosition (Aggregate)
│   Root: FinancialPosition
│   States: Being-Prepared, Completed, Current, Historical
│   Types: Balance Sheet, Cash Flow, Income & Expense, Net-Worth
│   Invariant: point-in-time snapshot — immutable once Completed
│   L2 Capability: Financial Position Management
│
└── Tax (Aggregate)
    Root: Tax
    Types: Income, Sales, Property
    References: transactionId, accountId, jurisdictionId
    L2 Capability: Tax Management
```

**Why each L2 = its own aggregate:** The BIZBOK axiom is that each capability
"can only modify the information concept sharing its business object." This
means Financial Transaction Management writes to FinancialTransaction, Payment
Management writes to Payment, Financial Account Management writes to
FinancialAccount — they never directly mutate each other. That's the aggregate
consistency boundary by definition. They coordinate through domain events:

```
PaymentExecuted { paymentId, amount, fromAccountId, toAccountId }
  → FinancialTransaction context creates transaction record
  → FinancialAccount context updates balances
  → Tax context calculates tax implications
```

Each aggregate enforces its own invariants independently. The saga (value
stream) coordinates the cross-aggregate workflow.

### Design Note: Payment — Aggregate vs Value Object

**Q: Isn't Payment just a Value Object wrapping MonetaryAmount + Currency?**

No. The `{amount, currency}` pair is **MonetaryAmount** — that's the Value
Object (immutable, value equality, no identity). Payment is a distinct concept:

| Criterion | MonetaryAmount | Payment |
|-----------|---------------|---------|
| Identity | No — $100 USD = $100 USD | Yes — payment  ≠ payment  |
| Mutability | Immutable | Mutable (Unpaid → Paid → Settled) |
| Lifecycle | None | Created, executed, settled, possibly reversed |
| BIZBOK category | Secondary | Secondary (but has own L2 capability) |
| DDD pattern | Value Object | Aggregate (has identity + state transitions) |

**Why not demote Payment to an Entity inside FinancialTransaction?**

It depends on the domain. If payments always map 1:1 to transactions, they
could live inside FinancialTransaction. But in practice:

- **Partial payments** — multiple payments settle one obligation
- **Payment plans** — scheduled payments exist before any transaction
- **Reversals** — a reversal references a specific prior payment

These scenarios require Payment to have its own identity and lifecycle
independent of any single transaction — hence its own aggregate with the
BIZBOK's single-writer axiom enforced via Payment Management.

**Composition:** Payment *contains* MonetaryAmount (VO) and Currency (VO)
as embedded value objects. The aggregate root is Payment; the value objects
describe the amount being paid.

---

## Step 5: State Machines from Information Concept States

Each information concept's states define the aggregate's lifecycle. BIZBOK
states map directly to DDD state machines with domain events on transitions.

### Agreement Lifecycle

```
                    ┌──────────────┐
         ┌────────→│   Pending    │
         │         └──────┬───────┘
         │                │
         │     AgreementActivated
         │                │
         │         ┌──────▼───────┐
         │         │   In Force   │
         │         └──────┬───────┘
         │                │
         │    ┌───────────┼────────────┐
         │    │           │            │
         │  Terminated  Abandoned    (renewed)
         │    │           │            │
         │    ▼           ▼            │
         │ ┌──────┐  ┌──────────┐     │
         │ │Term- │  │Abandoned │     │
         │ │inated│  │          │     │
         │ └──────┘  └──────────┘     │
         │                            │
         └────────────────────────────┘
```

### Financial Transaction Lifecycle

```
  ┌──────────────┐
  │   Pending    │──── TransactionRejected ───→ [Rejected]
  └──────┬───────┘
         │
  TransactionExecuted
         │
  ┌──────▼───────┐
  │   Executed   │──── TransactionCancelled ──→ [Cancelled]
  └──────────────┘
```

### Order Lifecycle

```
  [Open] → [Pending] → [Executed] → [Closed]
                │              │
                └→ [Expired]   └→ [Cancelled]
```

---

## Step 6: Domain Events from Value Stream Stages

Each value stream stage has exit criteria. When exit criteria are met, a
domain event is produced. The "Execute Financial Transaction" value stream:

```
Value Stream: Execute Financial Transaction

Stage 1: Initiate Transaction
  Exit: "Request accepted"
  → Event: TransactionRequestAccepted { transactionId, requesterId, agreementId }

Stage 2: Validate Transaction Request
  Exit: "Request validated for processing"
  → Event: TransactionRequestValidated { transactionId, validatedBy }

Stage 3: Identify Related Impacts
  Exit: "Transaction impacts identified"
  → Event: TransactionImpactsIdentified { transactionId, fees[], charges[] }

Stage 4: Perform Transaction
  Exit: "Transaction performed"
  → Event: TransactionPerformed { transactionId, amount, fromAccount, toAccount }

Stage 5: Notify Stakeholders
  Exit: "All stakeholders notified"
  → Event: StakeholdersNotified { transactionId, notifiedParties[] }

Stage 6: Verify Compliance
  Exit: "All compliance validation complete"
  → Event: TransactionComplianceVerified { transactionId, auditTrailId }
```

### Saga: Execute Financial Transaction

The value stream → capability cross-mapping defines which bounded contexts
participate at each stage. This is the saga/process manager:

```python
class ExecuteFinancialTransactionSaga:
    """Orchestrates the Execute Financial Transaction value stream.

    Each step maps to a BIZBOK value stream stage. Participating
    contexts derived from capability cross-mapping (§8.1 Fig 8.1.5).
    """

    def handle_initiate(self, cmd: InitiateTransaction):
        # Stage 1 capabilities: Submission Mgmt, Channel Mgmt
        # Contexts involved: Channel, Submission (supporting)
        self.emit(TransactionRequestAccepted(...))

    def handle_validate(self, evt: TransactionRequestAccepted):
        # Stage 2 capabilities: Agreement Access Mgmt,
        #   Customer Risk Mgmt, Partner Risk Mgmt, Policy Compliance
        # Contexts involved: Agreement, Customer, Partner, Policy
        self.emit(TransactionRequestValidated(...))

    def handle_identify_impacts(self, evt: TransactionRequestValidated):
        # Stage 3 capabilities: Monetary Amount Mgmt,
        #   Financial Transaction Risk Mgmt, Tax Definition
        # Contexts involved: Finance, Product
        self.emit(TransactionImpactsIdentified(...))

    def handle_perform(self, evt: TransactionImpactsIdentified):
        # Stage 4 capabilities: Financial Transaction Recording,
        #   Financial Account Mgmt, Payment Mgmt
        # Contexts involved: Finance (write)
        self.emit(TransactionPerformed(...))

    def handle_notify(self, evt: TransactionPerformed):
        # Stage 5 capabilities: Message Mgmt, Channel Mgmt
        # Contexts involved: Message, Channel (supporting)
        self.emit(StakeholdersNotified(...))

    def handle_verify_compliance(self, evt: StakeholdersNotified):
        # Stage 6 capabilities: Policy Compliance Determination
        # Contexts involved: Policy (supporting)
        self.emit(TransactionComplianceVerified(...))
```

---

## Step 7: Context Map from Information Concept Relationships

BIZBOK information concept relationships define how bounded contexts interact.
The "modifies" vs "uses" distinction maps directly to upstream/downstream.

### Context Map: Financial Services Core

```
┌─────────────────────────────────────────────────────────────────┐
│                      CONTEXT MAP                                │
│                                                                 │
│   ┌──────────┐    Conformist     ┌──────────┐                  │
│   │ Customer │◄─────────────────│  Policy   │                  │
│   │          │                   │          │                  │
│   └────┬─────┘                   └──────────┘                  │
│        │                              ▲                         │
│        │ Customer-Supplier            │ Conformist              │
│        │ (Customer upstream)          │                         │
│        ▼                              │                         │
│   ┌──────────┐    Shared Kernel  ┌────┴─────┐                  │
│   │Agreement │◄─────────────────│ Finance  │                  │
│   │          │──────────────────►│          │                  │
│   └────┬─────┘                   └────┬─────┘                  │
│        │                              │                         │
│        │ Partnership                  │ Customer-Supplier       │
│        │                              │ (Finance upstream)      │
│        ▼                              ▼                         │
│   ┌──────────┐                   ┌──────────┐                  │
│   │Collateral│                   │  Order   │                  │
│   │          │                   │          │                  │
│   └──────────┘                   └────┬─────┘                  │
│                                       │                         │
│                                       │ Customer-Supplier       │
│                                       ▼                         │
│                                  ┌──────────┐                  │
│                                  │Financial │                  │
│                                  │Instrument│                  │
│                                  └──────────┘                  │
│                                                                 │
│   Relationship derivation:                                      │
│   - Agreement "modifies" Agreement → owns writes               │
│   - Agreement "uses" Customer, Partner, Product, Payment,       │
│     Financial Account, Policy → reads from those contexts       │
│   - Finance "modifies" Financial Account, Transaction           │
│   - Finance "uses" Agreement, Customer, Partner, Currency       │
│   - Order "modifies" Order → owns writes                       │
│   - Order "uses" Financial Instrument, Customer, Partner        │
└─────────────────────────────────────────────────────────────────┘
```

### Relationship Type Derivation Rules

| BIZBOK Relationship | DDD Context Map Pattern |
|--------------------|------------------------|
| Context A "modifies" Object X | A owns X (upstream for X) |
| Context A "uses" Object Y from Context B | A is downstream of B |
| Primary → Secondary dependency | Same aggregate or Shared Kernel |
| Both contexts "use" same object | Shared Kernel or Published Language |
| One-way "uses" only | Customer-Supplier (used context is upstream) |
| Cross-tier relationship (Core ↔ Supporting) | Conformist (core conforms to supporting's API) |

---

## Step 8: CQRS from Modifies vs. Uses

BIZBOK's "modifies" and "uses" distinction is literally CQRS:

```
┌─────────────────────────────────────────────┐
│        Agreement Management Context          │
│                                              │
│  COMMAND SIDE (modifies)                     │
│  ├── CreateAgreement                         │
│  ├── ActivateAgreement                       │
│  ├── TerminateAgreement                      │
│  ├── UpdateAgreementPreferences              │
│  └── RateAgreementRisk                       │
│                                              │
│  QUERY SIDE (uses — reads from other ctxs)   │
│  ├── Customer   → customer identity, risk    │
│  ├── Partner    → partner details            │
│  ├── Product    → product terms, pricing     │
│  ├── Finance    → financial account balance  │
│  ├── Policy     → compliance rules           │
│  └── Channel    → delivery preferences       │
└─────────────────────────────────────────────┘
```

This maps cleanly to service derivation (BIZBOK §6.5):
- **Business Services Layer** = Command handlers (one per L2-L3 capability)
- **Data Services Layer** = Query/read model services
- **Integration Services Layer** = Anti-Corruption Layer
- **Operational Services Layer** = Legacy system adapters

---

## Business Scenario Walkthrough: Open Checking Account

BIZBOK §8.1 provides a complete scenario. Here it is in DDD event-driven form:

```
Value Stream: Establish Financial Agreement
Scenario: Customer opens checking account via web portal

Stage 1: Initiate Request
  Command: SubmitAccountApplication { customerId, productType: "checking", channel: "web" }
  Context: Submission (supporting)
  Event: ApplicationSubmitted { applicationId, customerId, channel }

Stage 2: Identify Needs
  Command: AssessCustomerNeeds { customerId, applicationId }
  Context: Customer (core) + Product (core)
  Reads: Customer.preferences, Product.eligibleProducts(channel, location)
  Event: NeedsAssessed { applicationId, recommendedProducts[], selectedProduct }

Stage 3: Collect Approval
  Command: EvaluateApplicant { applicationId, customerId }
  Context: Customer (core) — risk evaluation
  Reads: Customer.identity, Customer.creditScore (via external service)
  Side effects: AML check, fraud detection
  Event: ApplicantApproved { applicationId, riskLevel }
      OR: ApplicantFlagged { applicationId, reason, pendingReview: true }

Stage 4: Activate Agreement
  Command: ActivateAgreement { applicationId, customerId, productId, terms }
  Context: Agreement (core) — WRITES
  Side effects: Generate account number, create on systems
  Event: AgreementActivated { agreementId, accountNumber, customerId }
  → Triggers in Finance context:
    Command: OpenFinancialAccount { agreementId, accountType, customerId }
    Event: FinancialAccountOpened { accountId, agreementId }

Stage 5: Post-Activation
  Subscribers to AgreementActivated:
  - Channel context → send welcome email/SMS
  - Compliance context → validate documentation
  - Customer context → update customer profile
  Events: WelcomeNotificationSent, ComplianceValidated, CustomerProfileUpdated
```

---

## Summary: The Derivation is Mechanical

The key insight is that **BIZBOK → DDD is not an art — it's a derivation**.
Given a BIZBOK reference model, the DDD strategic and tactical patterns
follow deterministically:

1. **L1 Capabilities** → Bounded Contexts (boundary is the business object)
2. **Capability Tiers** → Core / Supporting / Generic classification
3. **Information Concepts** → Aggregates, Entities, Value Objects
4. **States** → State machines with domain events on transitions
5. **Types** → Discriminated unions / subtype hierarchies
6. **Relationships** → Context Map (upstream/downstream from modifies/uses)
7. **Value Stream Stages** → Domain Events (exit criteria = event trigger)
8. **Value Stream × Capability cross-map** → Saga participants
9. **Modifies vs. Uses** → CQRS command/query separation
10. **Capability Behaviors** → Service variants within a context

This means for any industry with a BIZBOK reference model, we can
**mechanically generate a starter DDD context map** as part of the
diligence engine output.

---

## Source References

- BIZBOK Guide v14, §2.2 Capability Mapping (p.64-121)
- BIZBOK Guide v14, §2.5 Information Mapping (p.192-226)
- BIZBOK Guide v14, §6.5 SOA Alignment (p.547-560)
- BIZBOK Guide v14, §6.6 Data Architecture Alignment (p.561-574)
- BIZBOK Guide v14, §8.1 Financial Services Reference Model (p.596-617)
- BIZBOK Guide v14, §8.6 Common Reference Model (p.674-701)
