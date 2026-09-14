# Product Requirements Document — Card Dispute & Fraud Resolution

**Product:** Redress — Meridian Card Services Dispute Center
**Version:** 1.0
**Status:** Approved for build
**Sector:** Banking & Financial Services (BFS)
**Owner:** Product Team

---

## 1. Overview

Redress lets a Meridian cardholder open a dispute against a recent card
transaction entirely online: verify their card, pick the transaction,
categorize why they're disputing it, describe what happened, and submit —
receiving a case reference number and, where the rules allow it, an
immediate provisional credit.

This document defines requirements for the **case-filing flow** only.
Post-filing features (case status tracking, merchant response handling,
final adjudication) are out of scope for this version.

## 2. Goals

- Let a cardholder file a well-formed dispute in under three minutes.
- Apply eligibility and verification rules transparently and consistently.
- Issue a case reference — and provisional credit where warranted —
  immediately on submission.

## 3. Out of scope

- Case status tracking after filing, merchant responses, chargebacks, and
  final adjudication.
- Multiple transactions disputed in a single case.
- Real identity verification or real fund movement (the demo uses mock
  verification and a mock transaction list).

---

## 4. The flow

The flow is a single page whose sections unlock progressively as the
cardholder completes each one:

1. **Verify** — confirm card (last 4 digits) and billing ZIP/postal code.
2. **Select transaction** — choose the disputed transaction from the
   cardholder's recent activity.
3. **Categorize** — choose a dispute reason; reason-specific fields appear
   as needed.
4. **Describe** — provide a written account of what happened.
5. **Review & submit** — see the computed case priority and provisional
   credit, then submit.

The cardholder may change the selected transaction at any point before
submitting; doing so resets the reason, conditional fields, and computed
case outcome, since they all depend on the transaction chosen.

---

## 5. Detailed requirements

### 5.1 Verify (Section 1)

The cardholder must provide:

- **Last 4 digits of card** (exactly 4 digits)
- **Billing ZIP / postal code** (3–10 alphanumeric characters)

### 5.2 Select transaction (Section 2)

The cardholder chooses exactly one transaction from their recent activity.
Only transactions dated within the last 90 days are selectable; older
transactions are listed but disabled, since Meridian's dispute window has
closed for them.

### 5.3 Categorize (Section 3)

The cardholder chooses exactly one **dispute reason**:

- **Unauthorized / Fraud** — the cardholder didn't make or authorize the
  charge.
- **Billing error** — duplicate charge or wrong amount.
- **Goods not received** — payment made, order never arrived.
- **Cancelled subscription** — charged after cancelling.

Reason-specific requirements:

- **Goods not received** requires an **expected delivery date**, which must
  already be in the past.
- **Cancelled subscription** requires a **cancellation confirmation
  number**.

Regardless of reason, any dispute over **$500** requires **secondary
verification** (last 4 digits of SSN) before the cardholder can continue.

### 5.4 Describe (Section 4)

The cardholder provides a free-text description of what happened, at least
20 characters.

### 5.5 Review & submit (Section 5)

- The system shows the computed **case priority** (High or Standard) and
  **provisional credit** amount before submission.
- On submission, the system issues a **case reference number** in the
  format `CASE-` followed by 6 digits and 2 letters (e.g.
  `CASE-482913-QT`).
- No case reference is shown at any earlier point in the flow.

---

## 6. Acceptance criteria

These are the definitive, testable rules the product must satisfy.

**AC-1 — Provisional credit requires timely fraud filing.**
A provisional credit is issued only when the dispute reason is
Unauthorized / Fraud **and** the disputed transaction is dated within 60
days of today. A fraud dispute filed on a transaction older than 60 days
(but still within the 90-day dispute window) still opens a case, but
receives **no** provisional credit.

**AC-2 — High-value disputes require secondary verification.**
Any dispute on a transaction over $500 requires the cardholder to provide
the last 4 digits of their SSN before they can continue past the
Categorize section, regardless of dispute reason.

**AC-3 — Goods Not Received requires a past delivery date.**
The Goods Not Received reason requires an expected delivery date that is
strictly before today. A future or missing delivery date blocks
progression with a clear error.

**AC-4 — Cancelled Subscription requires a cancellation reference.**
The Cancelled Subscription reason requires a non-empty cancellation
confirmation number. A missing value blocks progression with a clear
error.

**AC-5 — Case priority reflects amount and reason.**
A case is **High** priority if the disputed amount exceeds $1,000, or if
the reason is Unauthorized / Fraud (regardless of amount). Otherwise it is
**Standard** priority.

**AC-6 — Provisional credit is capped.**
Where AC-1 makes a case eligible, the provisional credit equals the
disputed amount, capped at $1,000.

**AC-7 — Case reference issued only after full validation.**
A case reference number is generated only when the transaction, reason,
all reason-specific fields, any required secondary verification, and a
sufficient description are all present and valid. No case reference is
shown before submission succeeds.

**AC-8 — Dispute window is 90 days.**
Only transactions dated within the last 90 days may be selected for a
dispute. Older transactions are visible in the list (for context) but are
disabled and cannot be selected.

---

## 7. Worked examples

These illustrate the acceptance criteria in combination.

**Example A — High-value fraud, filed in time**
- Transaction: Vertex Electronics, $1,299.00, 18 days ago. Reason:
  Unauthorized / Fraud.
- Amount > $500 → secondary verification required (AC-2).
- Priority: amount > $1,000 → **High** (AC-5; fraud reason would also make
  it High on its own).
- Credit eligibility: fraud + 18 days ≤ 60-day window → eligible (AC-1).
  Credit = min($1,299.00, $1,000) = **$1,000.00** (AC-6).

**Example B — Small fraud, filed in time**
- Transaction: Bright Fitness Studio, $89.00, 45 days ago. Reason:
  Unauthorized / Fraud.
- Amount ≤ $500 → no secondary verification needed.
- Priority: fraud reason → **High** (AC-5), even though the amount is
  small.
- Credit eligibility: fraud + 45 days ≤ 60-day window → eligible. Credit =
  min($89.00, $1,000) = **$89.00**.

**Example C — Cancelled subscription**
- Transaction: CloudStream Plus, $15.99, 8 days ago. Reason: Cancelled
  Subscription, cancellation reference required.
- Amount ≤ $500 → no secondary verification needed.
- Priority: amount ≤ $1,000 and reason ≠ fraud → **Standard**.
- Credit eligibility: reason ≠ fraud → not eligible. Credit = **$0.00**
  (the case still opens; it simply carries no interim credit).

**Example D — Fraud filed too late for credit**
- Transaction: Summit Hardware, $610.00, 75 days ago. Reason: Unauthorized
  / Fraud.
- Amount > $500 → secondary verification required (AC-2).
- Priority: fraud reason → **High** (AC-5).
- Credit eligibility: fraud, but 75 days > 60-day credit window → **not**
  eligible (AC-1). Credit = **$0.00**, even though the case is High
  priority and the reason is fraud.

---

## 8. Error and edge handling

- Any required field left empty or invalid blocks progression with a
  section-level error.
- A transaction older than 90 days cannot be selected at all (AC-8).
- Switching the selected transaction resets reason, conditional fields, and
  the computed priority/credit, since all depend on the transaction.
- The priority and provisional credit shown at Review must exactly match
  what's shown on the confirmation screen after submission.
