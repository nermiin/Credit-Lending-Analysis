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

---

### US-LEND-06 — Request human review

**As a** declined applicant  
**I want** a person to review an automated decision  
**So that** an error or missing context can be corrected

- Route offered on the decline notice.
- A different underwriter decides within 5 business days.

---

### US-LEND-07 — Withdraw from the agreement

**As a** customer  
**I want** to withdraw within 14 days of signing  
**So that** I can change my mind without penalty

```gherkin
  Scenario: Withdrawal within the period
    Given the loan was funded 10 days ago
    When the customer submits a withdrawal notice
    Then the payoff shows principal plus daily interest only
    And the customer has 30 days to return the funds
```
