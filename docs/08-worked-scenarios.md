# Worked Scenarios: Lending Origination

Fictional applicants. Installments use an annuity formula, with the APR as an effective annual rate and the monthly rate `(1 + APR)^(1/12) - 1`.

## S-01 Straight-Through Approval

| Input | EUR |
|-------|-----|
| Net monthly income | 3,200.00 |
| Essential expenses | (1,100.00) |
| Existing debt service | (250.00) |
| **Disposable income** | **1,850.00** |
| **Maximum installment at 40%** | **740.00** |

| Offer | Value |
|-------|-------|
| Requested | EUR 20,000 over 36 months at 7.9% APR |
| Installment | EUR 623.30, within cap |
| Total repayable | EUR 22,438.70 |
| Total cost of credit | EUR 2,438.70 |

**Result:** APPROVED, binding offer valid for 48 hours.

## S-02 Counter-Offer

| Input | EUR |
|-------|-----|
| Net monthly income | 2,400.00 |
| Essential expenses | (1,050.00) |
| Existing debt service | (300.00) |
| **Disposable income** | **1,050.00** |
| **Maximum installment at 40%** | **420.00** |

Requested EUR 18,000 over 48 months at 8.9% APR gives EUR 444.15, which is above the cap.

| Option | Principal | Term | Installment | Total repayable | Cost of credit |
|--------|-----------|------|-------------|-----------------|----------------|
| Requested (not affordable) | 18,000 | 48 | 444.15 | 21,319.13 | 3,319.13 |
| Counter A: lower amount | 17,000 | 48 | 419.47 | 20,134.73 | 3,134.73 |
| Counter B: longer term | 18,000 | 60 | 369.79 | 22,187.19 | 4,187.19 |

The maximum principal at EUR 420.00 over 48 months is EUR 17,021.34, rounded down to the nearest EUR 100 per product rules.

**Design point:** Counter B has the lowest installment, but costs EUR 1,052.46 more than Counter A. Showing the total cost next to the installment is a responsible lending requirement, and US-LEND-03 should not lead with installment alone.

## S-03 Living-Cost Floor (CR-03)

| | As declared | With floor |
|---|-------------|-----------|
| Net income | 2,000.00 | 2,000.00 |
| Essential expenses | 400.00 declared | 950.00 floor applied |
| Debt service | 100.00 | 100.00 |
| Disposable income | 1,500.00 | 950.00 |
| Maximum installment | 600.00 | 380.00 |

A requested installment of EUR 520.00 passes on declared figures and fails once the floor is applied.

## S-04 Withdrawal After Same-Day Funding

| Item | Value |
|------|-------|
| Loan | EUR 12,000 over 24 months at 6.9% APR, installment EUR 535.59 |
| Funded and contract signed | Wednesday 10 June 2026 |
| Withdrawal right ends | 24 June 2026 |
| Withdrawal notice | 20 June 2026, 10 days after funding |
| Accrued interest, illustrative | 12,000 x 6.9% / 365 x 10 = EUR 22.68 |
| Amount to return | EUR 12,022.68, within 30 days of notice |

Payoff is the principal plus daily interest. No early repayment fee or arrangement margin is added.

## S-05 Disbursement Cut-Off

| Customer | Signed at | Rail | Funds expected |
|----------|-----------|------|----------------|
| Existing account | 22:10 Tuesday | Internal | Within 15 minutes |
| External bank | 14:58 Tuesday | SEPA Credit | Same business day |
| External bank | 15:01 Tuesday | SEPA Credit | Next business day |
| External bank | 16:00 Friday | SEPA Credit | Monday |

## S-06 Business Case

Illustrative volume of 10,000 applications a month at EUR 18 for each manual case.

| | Manual share | Manual cases | Monthly cost (EUR) |
|---|--------------|--------------|--------------------|
| Today, 66% STP | 34% | 3,400 | 61,200 |
| Target, 85% STP | 15% | 1,500 | 27,000 |
| **Saving** | | 1,900 | **34,200** |

That is **EUR 410,400 a year**, before including the document re-request loop, which accounts for 62% of manual cases.

## Edge Case Register

| ID | Situation | Expected Behavior | Rule |
|----|-----------|-------------------|------|
| E-01 | Installment exactly at cap | Passes, cap is inclusive | FR-03 |
| E-02 | Disposable income is zero or negative | DECLINED, no counter-offer | BR-LEND-01 |
| E-03 | Offer accepted at 48h and 1 second | Rejected, EXPIRED, new application offered | FR-04 |
| E-04 | Bureau service down | Application held and customer told, no decision on partial data | FR-02 |
| E-05 | Customer resumes draft after 10 days | Data refreshed with new consent | BR-LEND-03 |
| E-06 | Counter-offer below minimum principal of EUR 2,000 | DECLINED | Product bounds |
