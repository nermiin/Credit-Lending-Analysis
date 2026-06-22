# User Stories — Lending Origination

### US-LEND-01 — Submit loan application

**As a** customer  
**I want** to apply with prefilled KYC data  
**So that** I do not re-enter information the bank already holds

- Existing customers: name, address, IBAN prefilled from core.
- Consent captured for bureau and open banking before pull.

---

### US-LEND-02 — Receive instant decision

```gherkin
  Scenario: STP approval
    Given all eligibility and affordability checks pass
    When the customer submits the application
    Then a binding offer is displayed within 60 seconds
    And APR, installment, and total cost are shown
```

---

### US-LEND-03 — Counter-offer flow

**As a** customer  
**I want** to see an alternative offer when my requested amount is unaffordable  
**So that** I can still obtain a smaller loan

- Show side-by-side: requested vs counter offer.
- Declining counter-offer ends journey with no bureau re-pull for 30 days.

---

### US-LEND-04 — Underwriter referral queue

**As an** underwriter  
**I want** referred applications with complete document package  
**So that** I can decide within 24 hours

- Queue sorted by SLA breach risk.
- Checklist: income verified, fraud cleared, policy exceptions flagged.

---

### US-LEND-05 — Disbursement confirmation

**As a** customer  
**I want** notification when funds arrive  
**So that** I know I can use the loan

- Push + email with amount, value date, and first payment date.
