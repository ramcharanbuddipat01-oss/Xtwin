# export Digital Twin

> **AI-powered Export Intelligence & Reconciliation Platform for Indian MSME Exporters**

Build Bharat × AWS Hackathon — WeMakeDevs

---

## 1. What Are We Building?

We are building a prototype **Digital Twin of an export shipment**.

The problem is that information about a single export shipment is fragmented across multiple documents and systems:

```text
Exporter
   │
   ├── Order
   ├── Commercial Invoice
   ├── Packing List
   ├── Shipping Bill
   ├── GST Filing
   ├── Customs
   ├── Shipment / Logistics
   ├── Bank Payment
   ├── IGST Refund
   ├── EDPMS
   ├── e-BRC
   └── Export Incentives
```

These systems contain different representations of the **same underlying shipment**.

When information becomes inconsistent, the exporter may face:

- refund delays
- payment reconciliation problems
- compliance issues
- missing documentation
- unresolved shipment exceptions
- money being stuck
- uncertainty about what action to take

### Our solution

Create a **canonical digital representation of each shipment**.

Then use deterministic reconciliation + AI reasoning to answer:

> **What is the current state of this shipment?**

> **Is anything wrong?**

> **How much money is affected?**

> **What caused the problem?**

> **What should the exporter do next?**

---

# 2. IMPORTANT — What This Is NOT

We are **NOT** building:

- a generic document checker
- a generic AI chatbot
- an OCR demo
- a generic logistics dashboard
- a full customs replacement
- a full banking platform
- a complete government integration platform
- a predictive ML system without data

The hackathon MVP is a **vertical slice of the larger Export Digital Twin vision**.

---

# 3. Product Vision

The long-term product is an:

# AI Export Control Tower

A system that maintains a continuously updated digital representation of an export shipment across its entire lifecycle.

Conceptually:

```text
                         EXPORT SHIPMENT
                                │
             ┌──────────────────┼──────────────────┐
             ↓                  ↓                  ↓
        COMMERCIAL          COMPLIANCE          LOGISTICS
             │                  │                  │
          Invoice            GST              Shipment
          Order              Customs          Carrier
          Product            EDPMS            EGM
             │                  │                  │
             └──────────────────┼──────────────────┘
                                ↓
                       DIGITAL TWIN
                                │
             ┌──────────────────┼──────────────────┐
             ↓                  ↓                  ↓
           STATE             RISKS             MONEY
             │                  │                  │
             └──────────────────┼──────────────────┘
                                ↓
                         AI INVESTIGATION
                                ↓
                     ROOT CAUSE + ACTION
```

---

# 4. Hackathon MVP

Because the hackathon has approximately **36 hours remaining**, we are NOT implementing the complete digital twin.

We are proving the core thesis with one end-to-end slice:

```text
Sample Shipment
      ↓
Canonical Shipment Record
      ↓
Documents + GST + Bank Data
      ↓
Reconciliation Engine
      ↓
Shipment State
      ↓
Financial Impact
      ↓
Bedrock AI Investigation
      ↓
Root Cause + Recommended Action
```

---

# 5. The Core Demo

We will have three synthetic shipments.

## Shipment 1 — Healthy

Everything reconciles.

```text
Customs       ✓
GST           ✓
Refund        ✓
Bank          ✓
e-BRC         ✓
```

Result:

```text
SHIPMENT HEALTHY
No critical reconciliation issues.
```

---

## Shipment 2 — Refund Risk

Example:

```text
Shipping Bill:
INV-2026-0847

GST Filing:
INV/2026/0847
```

The reconciliation engine detects:

```text
GST_INVOICE_MISMATCH
```

The shipment contains:

```text
Shipment Value: ₹8,00,000
IGST: ₹1,44,000
```

The control tower shows:

```text
REFUND
🔴 AT RISK

₹1,44,000
```

The user can ask:

> Why is my refund at risk?

Bedrock receives the structured shipment state and reconciliation findings and explains the root cause.

---

## Shipment 3 — Payment Reconciliation

Example:

```text
Expected shipment amount:
₹8,00,000

Bank received:
₹7,80,000

Difference:
₹20,000
```

The system identifies:

```text
PAYMENT_AMOUNT_MISMATCH
```

The control tower shows:

```text
BANK
⚠ RECONCILIATION REQUIRED

₹20,000 difference
```

The user can investigate the issue.

---

# 6. Core Product Principle

## Deterministic software is the source of truth.

AI is NOT responsible for determining whether numbers match.

For example:

```python
if shipping_bill.invoice_number != gst.invoice_number:
    create_issue(...)
```

This is deterministic.

Likewise:

```text
₹8,00,000 ≠ ₹7,80,000
```

does not require AI.

---

# 7. What AI Does

Amazon Bedrock is used for **investigation and explanation**.

The AI receives structured information such as:

```json
{
  "shipment": {},
  "documents": {},
  "reconciliation_issues": [],
  "financial_impact": {},
  "statuses": {}
}
```

It can answer:

> Why is my refund pending?

> What is wrong with this shipment?

> How much money is at risk?

> What should I investigate first?

> Explain this issue in simple language.

The AI must only use facts provided in the shipment context.

## AI MUST NOT:

- invent shipment information
- invent financial amounts
- invent document values
- invent government statuses
- claim an issue exists when the reconciliation engine did not detect one
- override deterministic reconciliation results

---

# 8. Architecture

## High-Level

```text
                    NEXT.JS FRONTEND
                           │
                           ↓
                     FASTAPI BACKEND
                           │
              ┌────────────┼────────────┐
              ↓            ↓            ↓
         PostgreSQL    Reconciliation  S3
              │            │            │
              │            ↓            │
              │       Shipment State    │
              │            │            │
              └────────────┼────────────┘
                           ↓
                     Amazon Bedrock
                           │
                           ↓
                  AI Investigation
                           │
                           ↓
                    Frontend Response
```

---

# 9. Technology Stack

## Frontend

- Next.js
- React
- TypeScript
- Tailwind CSS
- Lucide React

## Backend

- Python
- FastAPI
- SQLAlchemy
- Pydantic

## Database

- PostgreSQL
- Amazon RDS PostgreSQL for deployment

## Storage

- Amazon S3

## AI

- Amazon Bedrock

## Deployment

- AWS App Runner — Backend
- AWS Amplify — Frontend
- Amazon RDS — Database
- Amazon S3 — Documents
- Amazon Bedrock — AI

---

# 10. AWS Philosophy

Do NOT add AWS services just to make the architecture look complicated.

For the MVP, we prefer:

```text
S3
RDS PostgreSQL
Bedrock
App Runner
Amplify
```

Potential services such as:

```text
Lambda
SQS
EventBridge
Step Functions
ECS
Kafka
```

are NOT required for the MVP.

Reliability and working functionality are more important than architectural complexity.

---

# 11. Database

The MVP database should remain small.

Core tables:

```text
exporters
shipments
products
documents
gst_records
payments
reconciliation_issues
shipment_status
```

We should avoid unnecessary normalization.

Document-specific extracted/synthetic information can be stored as JSON where appropriate.

Example:

```text
documents
---------
id
shipment_id
type
data_json
file_url
created_at
```

---

# 12. Canonical Shipment

The shipment is the central object.

Example:

```json
{
  "id": "EXP-1042",
  "shipment_value": 800000,
  "currency": "INR",
  "destination": "Germany",

  "customs": {
    "status": "CLEARED"
  },

  "gst": {
    "status": "FILED",
    "invoice_number": "INV/2026/0847"
  },

  "shipping_bill": {
    "invoice_number": "INV-2026-0847"
  },

  "refund": {
    "status": "AT_RISK",
    "amount": 144000
  },

  "bank": {
    "status": "RECEIVED",
    "amount": 800000
  }
}
```

This structure is illustrative.

The final schema must be documented in:

```text
/docs/data-model.md
```

---

# 13. Reconciliation Engine

The reconciliation engine is deterministic.

It compares related information.

Examples:

```text
Invoice ↔ GST
Invoice ↔ Shipping Bill
Shipment ↔ Bank
```

Potential issues:

```text
GST_INVOICE_MISMATCH
GSTIN_MISMATCH
HSN_MISMATCH
VALUE_MISMATCH
QUANTITY_MISMATCH
PAYMENT_AMOUNT_MISMATCH
MISSING_DOCUMENT
```

Every issue should contain:

```text
id
shipment_id
type
severity
field
source_a
value_a
source_b
value_b
description
financial_impact
created_at
```

---

# 14. Shipment Status

The system should calculate status from actual data.

Example:

```text
CUSTOMS
GST
SHIPMENT
REFUND
BANK
e-BRC
```

Each can have:

```text
HEALTHY
PENDING
AT_RISK
BLOCKED
```

Do not manually hard-code the dashboard status separately from the underlying data.

The dashboard should derive status from the shipment state.

---

# 15. Financial Impact

The product should emphasize **money**, not just errors.

Examples:

```text
Shipment Value
IGST Refund Amount
Payment Expected
Payment Received
Payment Difference
Money At Risk
```

Example:

```text
Shipment Value:       ₹8,00,000
Expected Refund:      ₹1,44,000
Refund At Risk:       ₹1,44,000
```

The purpose is to answer:

> **"Why does this issue matter financially?"**

---

# 16. Frontend

## Control Tower

The primary dashboard should show:

```text
EXPORT CONTROL TOWER

Active Shipments
Money At Risk
Open Issues

------------------------------------------------

Shipment    Value     Customs   GST   Refund   Bank
------------------------------------------------
EXP-1042    ₹8L       ✓         ✓     ⚠        ✓
EXP-1043    ₹12L      ✓         ✓     ✓        ✓
EXP-1044    ₹5L       ✓         ⚠     ⚠        ⚠
```

---

# 17. Shipment Detail

Clicking a shipment should show:

```text
EXPORT SHIPMENT #EXP-1042

₹8,00,000
Germany
Cotton Garments

--------------------------------------

CUSTOMS       ✓ CLEARED
GST           ✓ FILED
SHIPMENT      ✓ DELIVERED
REFUND        ⚠ AT RISK
BANK          ✓ RECEIVED
e-BRC         ○ PENDING
```

Then:

```text
REFUND AT RISK

₹1,44,000

Cause:
GST invoice number does not match
Shipping Bill invoice number.
```

---

# 18. AI Investigation UI

Provide a button:

```text
Why is my refund at risk?
```

or an input:

```text
Ask about this shipment...
```

Example:

```text
USER:
Why is my refund at risk?

AI:

Your refund is at risk because the invoice number
in the GST filing does not match the invoice number
in the Shipping Bill.

GST:
INV/2026/0847

Shipping Bill:
INV-2026-0847

Recommended action:
Verify the invoice reference and correct the
relevant filing before proceeding.
```

---

# 19. API Contract

The backend should expose a small, stable API.

Initial endpoints:

```text
GET /health

GET /shipments

GET /shipments/{shipment_id}

GET /shipments/{shipment_id}/documents

GET /shipments/{shipment_id}/issues

GET /shipments/{shipment_id}/financial-summary

POST /shipments/{shipment_id}/ask
```

Optional:

```text
POST /seed
```

The frontend must consume these APIs rather than directly accessing the database.

---

# 20. Repository Structure

Recommended:

```text
/
├── frontend/
│   ├── app/
│   ├── components/
│   ├── lib/
│   └── types/
│
├── backend/
│   ├── app/
│   │   ├── api/
│   │   ├── models/
│   │   ├── schemas/
│   │   ├── services/
│   │   ├── reconciliation/
│   │   └── ai/
│   │
│   ├── tests/
│   └── requirements.txt
│
├── data/
│   ├── healthy_shipment.json
│   ├── refund_risk_shipment.json
│   └── payment_mismatch_shipment.json
│
├── docs/
│   ├── architecture.md
│   ├── data-model.md
│   ├── api-contract.md
│   └── demo-script.md
│
├── README.md
└── .env.example
```

---

# 21. Team Ownership

## Person 1 — Technical Lead / Integration

Responsibilities:

- architecture
- canonical shipment model
- reconciliation engine
- API contracts
- backend integration
- final integration
- code review
- demo flow

Primary tools:

- Codex
- Claude
- GitHub
- Postman

---

## Person 2 — Backend / Database

Responsibilities:

- SQLAlchemy models
- PostgreSQL
- migrations
- seed data
- shipment APIs
- document APIs
- financial summary APIs

Primary tools:

- Codex/Cursor
- PostgreSQL
- Docker
- Postman

---

## Person 3 — Frontend

Responsibilities:

- control tower
- shipment list
- shipment detail
- status cards
- financial impact UI
- issue display
- AI investigation interface

Primary tools:

- Cursor/Codex
- Next.js
- TypeScript
- Tailwind

---

## Person 4 — AWS / AI

Responsibilities:

- AWS setup
- IAM
- Bedrock integration
- AI prompts
- S3
- RDS connectivity
- App Runner
- Amplify
- deployment

Primary tools:

- AWS Console
- Codex
- Claude
- AWS documentation

---

# 22. Development Rules

## Rule 1 — One source of truth

The canonical shipment model is defined in:

```text
/docs/data-model.md
```

Do not independently invent fields.

---

## Rule 2 — API contract first

Frontend and backend communicate only through the documented API.

Defined in:

```text
/docs/api-contract.md
```

---

## Rule 3 — No unnecessary architecture

Do not introduce:

- microservices
- Kafka
- event sourcing
- complex orchestration
- unnecessary queues
- unnecessary AI frameworks

unless the team explicitly agrees that it is required.

---

## Rule 4 — Deterministic source of truth

Reconciliation results come from Python/business logic.

Bedrock explains results.

Bedrock does NOT determine whether a numerical mismatch exists.

---

## Rule 5 — Synthetic data is acceptable

We are demonstrating the product concept.

We do not need live ICEGATE/GSTN/RBI/EDPMS integrations for the MVP.

Do not waste hackathon time trying to obtain inaccessible government APIs.

---

## Rule 6 — Every feature must work end-to-end

Prefer:

```text
ONE COMPLETE WORKING FEATURE
```

over:

```text
FIVE PARTIALLY WORKING FEATURES
```

---

# 23. What We Are NOT Building

Explicitly cut:

- live ICEGATE integration
- live GSTN integration
- live RBI/EDPMS integration
- live banking integrations
- full 14-stage export lifecycle
- predictive ML risk scoring
- multi-tenant enterprise security
- sophisticated authentication
- complete regulatory automation
- payment infrastructure
- FX infrastructure
- full customs filing system
- generic AI chatbot
- unnecessary microservices

These belong to the long-term product roadmap, not the hackathon MVP.

---

# 24. Future Product

The hackathon MVP proves:

```text
Canonical Shipment
       ↓
Reconciliation
       ↓
State
       ↓
Financial Impact
       ↓
AI Investigation
```

The eventual product can expand to:

```text
                    EXPORT CONTROL TOWER
                             │
        ┌────────────────────┼────────────────────┐
        ↓                    ↓                    ↓
   PRE-SHIPMENT          IN-TRANSIT           POST-SHIPMENT
        │                    │                    │
   Documentation          Logistics             Customs
   Compliance             Tracking              GST
   Risk                   EGM                   Refund
                                               Bank
                                               EDPMS
                                               e-BRC
                                               Incentives
```

Eventually the system could provide:

- real-time shipment state
- automated reconciliation
- compliance monitoring
- refund tracking
- payment reconciliation
- money-at-risk analysis
- exception management
- AI investigation
- recommended actions
- exporter-wide financial visibility

---

# 25. Product Thesis

The product thesis we are testing is:

> **Exporters do not need another document-storage system. They need a unified intelligence layer that understands one shipment across fragmented commercial, government, logistics and financial records.**

The digital twin is the foundation.

The intelligence layer is the product.

---

# 26. The Demo Story

The demo should follow this sequence:

### 1. Show the control tower

Multiple shipments.

Some healthy.

Some at risk.

### 2. Select the problematic shipment

Show:

```text
₹8L shipment

Customs ✓
GST ✓
Shipment ✓
Refund ⚠
Bank ✓
```

### 3. Show financial impact

```text
₹1.44L refund at risk
```

### 4. Show the root cause

```text
GST invoice:
INV/2026/0847

Shipping Bill:
INV-2026-0847
```

### 5. Ask AI

> Why is my refund at risk?

### 6. Bedrock explains

Root cause + impact + recommended action.

### 7. Fix the data

Re-run reconciliation.

### 8. Show clean state

```text
REFUND ✓
```

The audience should understand:

> **We didn't just find a mismatch. We connected the mismatch to the shipment's financial state and explained the root cause.**

---

# 27. Success Criteria

The MVP is successful if a judge can understand within ~30 seconds:

### Problem

Export information is fragmented.

### Product

We create a digital twin of the shipment.

### Intelligence

We reconcile all available information.

### Impact

We identify operational/financial problems.

### AI

Bedrock investigates and explains the root cause.

### AWS

S3 + RDS + Bedrock + App Runner + Amplify power the system.

---

# 28. Final Principle

## Build the smallest system that proves the biggest idea.

We are NOT trying to build the entire future company in 36 hours.

We are trying to prove:

> **"If we create a unified digital representation of an export shipment, we can turn fragmented export data into actionable financial and compliance intelligence."**

Everything in the MVP should serve that thesis.
