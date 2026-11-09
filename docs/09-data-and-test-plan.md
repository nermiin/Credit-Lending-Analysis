# Data Requirements and Test Plan: Lending Origination

## 1. Application Record

| Field | Type | Mandatory | Rule |
|-------|------|-----------|------|
| `applicationId` | UUID | Yes | Used across bureau, decision, core (FR-01) |
| `customerId` | String | Yes | KYC status must be PASS |
| `requestedAmount` | Decimal | Yes | EUR 2,000 to 35,000 |
| `requestedTerm` | Integer | Yes | 12 to 60 months |
| `loanPurpose` | Enum | Yes | For regulatory reporting |
| `employmentType` | Enum | Yes | EMPLOYED, SELF_EMPLOYED, RETIRED |
| `verifiedNetIncome` | Decimal | Yes | Per evidence rule BR-LEND-02 |
| `incomeSource` | Enum | Yes | OPEN_BANKING, PAYSLIP, TAX, PENSION |
| `declaredExpenses` / `livingCostFloor` | Decimal | Yes | Higher value used |
| `existingDebtService` | Decimal | Yes | Bureau plus declared |
| `consentBureauAt` / `consentOpenBankingAt` | Timestamp | Yes | Captured before any data pull |
| `pdBand` / `score` | Enum / Integer | Yes | Scorecard output |
| `decision` | Enum | Yes | APPROVED, REFERRED, DECLINED, COUNTER_OFFER |
| `decisionReasons` | List | If declined | Reason codes, ranked |
| `decisionMode` | Enum | Yes | AUTOMATED or MANUAL |
| `offerExpiresAt` | Timestamp | If offered | 48h after the offer |
| `withdrawalEndsAt` | Date | If funded | Funding date plus 14 days |

## 2. Decline Reason Catalog

| Code | Internal meaning | Customer wording |
|------|------------------|------------------|
| `AFFORD_CAP` | Installment above cap, no counter-offer | The repayments would be too high for your income and commitments |
| `BUREAU_ADVERSE` | Adverse credit file | Information held by a credit reference agency, with its name and how to get a copy |
| `SCORE_CUTOFF` | Below score cut-off | Your application did not meet our lending criteria |
| `PRODUCT_BOUNDS` | Outside product limits | We cannot offer a loan for this amount or term |
| `RESIDENCY` | Not eligible by residence | This product is not available in your country of residence |

Fraud flags and internal thresholds are never disclosed to the applicant.

## 3. Integration Requirements

| System | Direction | Trigger | On failure |
|--------|-----------|---------|------------|
| Credit bureau | Out, sync | After consent | Hold, retry 3 times, then tell the customer |
| Open banking | Out, redirect | Income step | Fall back to payslip upload |
| Fraud platform | Out, sync | After submission | Refer, never auto-approve |
| E-signature | Out, async | Offer accepted | Offer stays valid to expiry |
| Core banking | Out, sync | After signature | Retry, alert ops, block duplicate account |
| Payments | Out | After account creation | Status query before any re-send |

Before any re-send of a disbursement, the payment status SHALL be checked, so that a loan can never be funded twice.

## 4. Metrics Definitions

| Metric | Formula | Target |
|--------|---------|--------|
| STP rate | Decisions without manual touch / all decisions | 85% |
| Pull-through | Funded / approved | At least 72% |
| Counter-offer acceptance | Accepted counter-offers / counter-offers made | Baseline first |
| Human review overturn | Declines reversed on review / reviews | Below 5%, a higher rate triggers a rule check |
| Early delinquency | 30 DPD at 6 months / funded cohort | Below 2.5% |

## 5. UAT Plan

| TC | Covers | Input | Expected |
|----|--------|-------|----------|
| TC-01 | BR-LEND-01 | Disposable 1,850, installment 623.30 | Approved |
| TC-02 | FR-03 boundary | Installment equal to cap | Passes |
| TC-03 | FR-03 boundary | Cap plus EUR 0.01 | Counter-offer |
| TC-04 | CR-03 | Declared 400, floor 950 | Floor used |
| TC-05 | BR-LEND-02 | Self-employed, 2 tax years | Rule-defined income used |
| TC-06 | FR-02 | Consent absent | No bureau call made |
| TC-07 | FR-04 | Acceptance after 48h | Rejected, EXPIRED |
| TC-08 | FR-05 | Disbursement before signature | Blocked |
| TC-09 | FR-06 | Existing customer signs | Funds within 15 minutes |
| TC-10 | FR-09 | Withdrawal on day 14 | Accepted, payoff calculated |
| TC-11 | FR-09 | Withdrawal on day 15 | Refused, early repayment offered |
| TC-12 | FR-10 | Customer requests review | Case created, 5 business day timer |
| TC-13 | FR-07 | Decline from bureau data | Notice names database |
| TC-14 | NFR | Peak decision load | p95 below 3s |
| TC-15 | NFR | Keyboard and screen reader | WCAG 2.1 AA journey passes |

**Exit criteria:** Credit Risk confirms 500 synthetic applications against expected outcomes, Compliance signs off disclosures, and the first 100 funded loans are reconciled to signed contracts.
