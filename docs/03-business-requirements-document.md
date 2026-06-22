# Business Requirements Document — Digital Loan Origination

| Project | Aurora Lending — Origination v2 |
|---------|----------------------------------|
| Version | 1.0 |

---

## 1. Business Objectives

- Increase STP rate from 66% to 85% within 12 months.
- Reduce cost-per-loan originated by 30%.
- Same-day disbursement for existing bank customers.

## 2. Application Data Requirements

| Data Element | Mandatory | Validation |
|--------------|-----------|------------|
| Applicant identity | Yes | KYC PASS status |
| Employment type | Yes | Enum: employed, self-employed, retired |
| Net monthly income | Yes | Open banking or payslip |
| Loan amount & term | Yes | Within product bounds |
| Purpose of loan | Yes | Enum list (regulatory reporting) |
| Existing commitments | Yes | Bureau + declared |

## 3. Decision Outcomes

| Outcome | Customer Experience | Ops Action |
|---------|----------------------|------------|
| **APPROVED** | Show binding offer | None |
| **REFERRED** | "We're reviewing" — 24h SLA | Underwriter queue |
| **DECLINED** | Adverse action notice | None — regulatory letter |
| **COUNTER_OFFER** | Lower amount or shorter term | Optional accept |

## 4. Functional Requirements

| ID | Requirement | Priority |
|----|-------------|----------|
| FR-01 | Single `applicationId` across all integrations | Must |
| FR-02 | Real-time bureau pull with applicant consent | Must |
| FR-03 | Affordability cap: installment ≤ 40% disposable income | Must |
| FR-04 | Offer valid 48 hours; auto-expire | Must |
| FR-05 | E-sign binding contract before disbursement | Must |
| FR-06 | Instant disbursement to internal accounts | Should |
| FR-07 | Adverse action reason codes per regulation | Must |
| FR-08 | Save & resume application within 30 days | Should |

## 5. Affordability Rule (BR-LEND-01)

```
disposable_income = net_income - declared_essential_expenses - existing_debt_service
max_installment = disposable_income * 0.40
```

If requested installment > max_installment → COUNTER_OFFER or DECLINE.

## 6. Disbursement Rules

| Customer Type | Disbursement SLA | Rail |
|---------------|------------------|------|
| Existing current account | ≤ 15 minutes | Internal transfer |
| External bank | Same business day if signed before 15:00 CET | SEPA Credit |

## 7. NFRs

- Decision p95 < 3s (excluding manual path)
- 99.9% availability during business hours
- WCAG 2.1 AA for customer UI requirements
