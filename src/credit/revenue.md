# Revenue and Compliance

The [Credit Operating Layer](./overview.md) generates revenue from six primary streams and one optional stream. This page also documents the compliance requirements that apply as the credit product matures, and the recommended rollout sequence.

---

## Revenue Streams

| # | Stream | Description | Margin Profile |
|---|--------|-------------|---------------|
| 1 | **Origination fees** | One-time fee on credit line creation or increase | 1–3% of line size |
| 2 | **Spread and yield** | Interest on outstanding credit balances | Risk-grade-adjusted APR |
| 3 | **Servicing fees** | Monthly account maintenance, reporting, monitoring | Fixed per account |
| 4 | **Late and restructuring fees** | Penalties for missed payments; fees for term modification | Event-based |
| 5 | **Facilitator and settlement fees** | x402 facilitator processing fees on every drawdown and repayment | Per-transaction basis points |
| 6 | **White-label infrastructure fees** | Platform fees charged to [partners](./models.md#7-white-label-credit-rails) running credit programs on the stack | Monthly SaaS + per-transaction |

### Optional Seventh Stream

| Stream | Description |
|--------|-------------|
| **Underwriting and score API** | Sell access to the [reputation score](./models.md#6-reputation-to-credit-conversion) and underwriting engine as an API for partners |

> **Compliance warning**: Selling credit scores or underwriting decisions to third parties can trigger FCRA (Fair Credit Reporting Act) obligations. Do not commercialise the score API until legal counsel has confirmed the regulatory posture. See [Compliance Requirements](#compliance-requirements) below.

## Revenue Model by Credit Type

| Credit Model | Primary Revenue | Secondary Revenue |
|-------------|----------------|-------------------|
| [Spend-based network credit](./models.md#1-spend-based-network-credit) | Spread on outstanding balance | Facilitator fees |
| [Invoice and receivables credit](./models.md#2-invoice-and-receivables-credit) | Advance fee (discount rate) | Servicing fee |
| [Merchant working-capital lines](./models.md#3-merchant-and-api-working-capital-lines) | Spread + origination | Late fees |
| [Agent credit](./models.md#4-agent-credit) | Facilitator fees + spread | Operator servicing |
| [Asset-backed credit](./models.md#5-tokenised-asset-backed-credit) | Spread (lower, collateralised) | Origination + liquidation fees |
| [Reputation-to-credit](./models.md#6-reputation-to-credit-conversion) | Spread + origination | Score API (optional, regulated) |
| [White-label rails](./models.md#7-white-label-credit-rails) | Infrastructure fees | Per-transaction settlement |

## Compliance Requirements

### When Compliance Matters

The credit product starts as closed-loop commercial credit. At that stage, regulatory burden is manageable. As the product expands, compliance requirements escalate.

| Product Stage | Regulatory Exposure |
|--------------|-------------------|
| **Closed-loop B2B credit** | Low — internal underwriting, no consumer data |
| **Merchant working capital** | Medium — state lending license requirements vary |
| **Agent credit** | Low — programmatic, no natural-person borrower |
| **Invoice financing** | Medium — may require factoring or lending license |
| **Consumer or retail credit** | **High** — full ECOA/FCRA/state licensing |
| **Score API sold externally** | **High** — FCRA "consumer reporting agency" classification |

### Key Regulations

| Regulation | Trigger | Requirement |
|-----------|---------|-------------|
| **ECOA / Regulation B** | Making credit decisions about any applicant | Anti-discrimination protections; adverse-action notices with specific reasons for every denial or downgrade |
| **FCRA / Regulation V** | Using consumer-report data or operating as a consumer reporting agency | Permissible purpose requirements; data accuracy obligations; dispute resolution; consumer access rights |
| **State lending licenses** | Originating loans to borrowers in specific states | License-by-state; CSBS maintains a current 50-state survey of requirements |
| **Truth in Lending (TILA)** | Consumer credit products | Disclosure of APR, fees, terms in standardised format |
| **Bank Secrecy Act / AML** | Any financial institution or money services business | KYC/KYB, suspicious activity reporting, transaction monitoring |

### Mitigation Strategy

1. **Start with B2B only** — no natural-person borrowers in the initial product.
2. **Keep scores internal** — do not sell, share, or report reputation scores externally.
3. **Log every decision** — the [Decision Engine](./overview.md#decision-engine) records every approval, denial, limit change, and adverse action with specific reasons.
4. **Use private facilitators** — x402 supports [private facilitator deployment](../protocol/architecture.md) with custom KYC/KYT flows.
5. **Engage counsel early** — get a formal lending-license memo and jurisdiction analysis before expanding beyond closed-loop.
6. **Never market the internal score as a "credit score"** unless you are prepared to accept FCRA obligations.

## Rollout Sequence

| Phase | Product | Prerequisite |
|-------|---------|-------------|
| **1** | Closed-loop commercial credit | [4 engines](./overview.md#the-four-engines) operational; closed-network identity |
| **2** | Merchant and API working capital | State lending license analysis; facilitator credit-limit integration |
| **3** | Agent spend credit | [Agent mesh](../apostle/agents.md) policy controls; real-time velocity tracking |
| **4** | Invoice and receivables financing | Receivable verification pipeline; counterparty scoring |
| **5** | White-label partner programs | Partner onboarding, branded UI, revenue-share contracts |
| **6** | Broader regulated lending | Full compliance stack: ECOA, FCRA, state licenses, legal counsel memo |

### Phase 1 Deliverables

The minimum viable credit product:

| Deliverable | Description |
|-------------|-------------|
| Identity engine | Wallet + namespace + KYB binding |
| Behavior engine | Receipt-based payment history aggregation |
| Decision engine | Risk grading (A–F), limit assignment, adverse-action logging |
| Settlement engine | Stablecoin credit issuance, auto-sweep repayment |
| Credit ledger | On-chain credit balance and event tracking (Apostle Chain) |
| Credit facilitator | Private x402 facilitator with credit-limit enforcement |
| Admin dashboard | Credit line management, monitoring, reporting |

### Phase 1 Rails

| Rail | Use |
|------|-----|
| [Apostle Chain](../apostle/overview.md) | Credit memory, policy, attestation |
| [Stellar](./rails.md#stellar-for-credit) | Stablecoin disbursement and repayment |
| [UnyKorn L1](../chain/overview.md) | Receipt anchoring |

Solana is added in Phase 2 for merchant and agent high-frequency draws.

## Bottom Line

The strongest first move is **commercial network credit**: underwrite only entities that already transact on the system, use x402 receipts as underwriting inputs, and repay automatically from network cash flows.

The revenue model scales from direct lending yield (early phases) to platform infrastructure fees (Phase 5+), which carries less balance-sheet risk and more recurring revenue.

For the full architecture, see [Credit Operating Layer](./overview.md). For the seven credit models, see [Credit Models](./models.md). For the rail strategy, see [Rail Strategy](./rails.md).
