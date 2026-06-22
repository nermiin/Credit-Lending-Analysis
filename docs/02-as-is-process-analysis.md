# As-Is Process Analysis — Loan Origination

## Swimlane — Application to Funding

```mermaid
flowchart TB
    subgraph Customer
        A1[Start application] --> A2[Upload documents]
        A2 --> A3[Accept offer]
    end

    subgraph Digital
        A3 --> B1[Auto decision engine]
        B1 --> B2{STP?}
        B2 -->|No| B3[Email ops — no SLA]
    end

    subgraph Operations
        B3 --> C1[Manual data re-key]
        C1 --> C2[Call customer for missing docs]
        C2 --> C3[Approve in legacy system]
    end

    subgraph Finance
        C3 --> D1[Batch disbursement file]
        D1 --> D2[Next-day SEPA batch]
    end
```

## Pain Points

| ID | Description | Impact |
|----|-------------|--------|
| PP-L01 | 34% applications leave digital journey for manual ops | Cost €18/app avg |
| PP-L02 | Offer expiry not enforced — rate drift | Revenue leakage |
| PP-L03 | Disbursement batch once daily | Poor CX vs competitors |
| PP-L04 | No single application ID across bureau + core | Reconciliation errors |

## Bottleneck Analysis

Average manual path: **4.2 days** vs STP path: **47 minutes**.  
Primary delay: document re-request loop (62% of manual cases).
