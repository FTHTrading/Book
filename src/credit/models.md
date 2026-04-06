# Credit Models

The [Credit Operating Layer](./overview.md) supports seven distinct models for generating credit on the x402 stack. Each model draws on different underwriting signals and serves a different segment of the network. They are not mutually exclusive — a mature deployment runs several in parallel.

---

## Model Summary

| # | Model | Borrower | Collateral | Primary Signal |
|---|-------|----------|------------|----------------|
| 1 | [Spend-based network credit](#1-spend-based-network-credit) | Merchants, agents, businesses | None (performance-based) | x402 payment history |
| 2 | [Invoice and receivables credit](#2-invoice-and-receivables-credit) | Businesses with inbound cash flow | Verified invoices / receivables | Invoice quality and counterparty |
| 3 | [Merchant and API working-capital lines](#3-merchant-and-api-working-capital-lines) | Merchants, API providers | Revolving (volume-backed) | Payment volume, dispute rate |
| 4 | [Agent credit](#4-agent-credit) | AI agents | Policy-bound (programmatic) | Tier, spend history, budget |
| 5 | [Tokenised asset-backed credit](#5-tokenised-asset-backed-credit) | Businesses with on-chain assets | Treasury, RWA, stablecoin reserves | Collateral value and liquidity |
| 6 | [Reputation-to-credit conversion](#6-reputation-to-credit-conversion) | Any network participant | Reputation score | On-network behaviour composite |
| 7 | [White-label credit rails](#7-white-label-credit-rails) | Partner businesses | Varies by program | Partner-defined underwriting |

---

## 1. Spend-Based Network Credit

Use x402 payment history as the raw underwriting signal. If an agent, merchant, or business consistently pays for services on time and at volume, that performance data supports a credit line.

**How it works**:

1. Entity transacts on the network for a qualifying period (e.g. 90 days).
2. The [Behavior Engine](./overview.md#behavior-engine) aggregates payment timeliness, volume, and settlement success rate.
3. The [Decision Engine](./overview.md#decision-engine) assigns a risk grade and credit limit.
4. Credit line is issued in stablecoins (USDF, USDC, or equivalent).
5. Repayment is auto-swept from future incoming settlements.

**Why x402 makes this natural**: The protocol already supports [prepaid credits](../live/api-reference.md#credit-ledger), usage aggregation, and facilitator-based settlement. The infrastructure for tracking spend and auto-sweeping repayment already exists.

**Underwriting inputs**:

| Signal | Source |
|--------|--------|
| Total payment volume (30/60/90 day) | Facilitator receipts |
| On-time settlement ratio | Receipt timestamps vs invoice TTL |
| Average transaction size | Receipt amounts |
| Payment velocity trend | Rolling window analysis |
| Dispute / chargeback count | Webhook events |

---

## 2. Invoice and Receivables Credit

Advance funds against verified invoices, API receivables, subscriptions, ad revenue, or service contracts flowing through the network.

**How it works**:

1. Borrower submits receivables (outstanding invoices, subscription contracts, verified API revenue).
2. The system scores receivable quality: counterparty creditworthiness, historical payment rates, concentration risk.
3. A percentage of the receivable value is advanced (typically 70–90%).
4. Repayment auto-sweeps from the specific settlement when the receivable pays.
5. Remaining balance (minus fees) is released to the borrower.

**Underwriting inputs**:

| Signal | Weight |
|--------|--------|
| Counterparty payment history | High |
| Invoice age and TTL | High |
| Concentration (single-counterparty exposure) | Medium |
| Historical receivable default rate | High |
| Borrower's own settlement history | Medium |

**Risk controls**: Maximum advance rate per grade, counterparty concentration limits, automatic hold on disputed invoices.

---

## 3. Merchant and API Working-Capital Lines

A merchant or service on the x402 network gets a revolving credit line based on operating performance.

**Underwriting inputs**:

| Signal | Description |
|--------|-------------|
| Payment volume | Total inbound and outbound settlement volume |
| Repayment history | On-time rate for any existing credit |
| Volatility | Standard deviation of monthly revenue |
| Refund and dispute rate | Chargebacks, reversals, failed settlements |
| Wallet liquidity | Current on-chain balances across allowed rails |
| Contract revenue quality | Duration, counterparty diversity, renewal rates |

**Line characteristics**:

- Revolving — draw and repay without reapplication.
- Auto-adjusting — limit moves up or down based on trailing 90-day performance.
- Multi-rail — draws can settle on any [allowed rail](./rails.md).
- Rate-tiered — APR scales with risk grade.

---

## 4. Agent Credit

Each [mesh agent](../apostle/agents.md) gets a controlled spend line for compute, data, or execution services.

This model is unique because the credit is **programmatic and policy-bound**:

| Control | Description |
|---------|-------------|
| **Allowed endpoints** | Agent can only spend at whitelisted [x402 service URIs](../apostle/commerce.md#x402-service-uris) |
| **Max spend per draw** | Hard cap per individual transaction |
| **Velocity cap** | Maximum ATP or stablecoin spend per hour / day |
| **Fail-safe kill switch** | Automatic suspension if anomaly detected |
| **Settlement-only whitelist** | Credit can only settle to pre-approved counterparties |
| **Budget period** | Credit resets on a defined cadence (daily, weekly, monthly) |

**How it works on the Apostle Chain**:

1. Agent is [registered](../apostle/overview.md#api-endpoints) with an `AgentId` and Ed25519 keypair.
2. The credit system assigns a spend line based on agent tier and historical performance.
3. Each `POST /v1/tx` checks the agent's remaining credit before settlement.
4. Spend is tracked in real time; the [Behavior Engine](./overview.md#behavior-engine) updates the agent's risk profile continuously.
5. Credit replenishes from the agent's incoming settlement flows or from operator top-up.

**Why this matters**: Agent credit turns autonomous agents into economic actors that can operate beyond their current balance. The policy controls ensure no single agent can drain the system.

---

## 5. Tokenised Asset-Backed Credit

Use treasury assets, RWAs, stablecoin reserves, invoices, or tokenised cash-flow rights as collateral for credit lines.

**Collateral types**:

| Collateral | LTV Range | Liquidation Model |
|------------|-----------|-------------------|
| Stablecoin reserves (USDF, USDC) | 85–95% | Instant auto-liquidation |
| [Treasury holdings](../chain/treasury.md) | 50–70% | 48-hour delay, multi-sig approval |
| Tokenised invoices | 60–80% | Auto-sweep on receivable payment |
| RWA tokens | 40–60% | Governance-gated liquidation |
| ATP / UNY tokens | 30–50% | Market-price oracle, margin call |

**Why this works best for business credit**: Collateral and cash-flow are easier to verify and govern in a B2B context. Consumer collateral valuation and liquidation introduce regulatory complexity that is better deferred.

**Risk controls**: Loan-to-value (LTV) monitoring, margin calls at threshold, automatic collateral top-up requests, liquidation waterfalls.

---

## 6. Reputation-to-Credit Conversion

Build an internal score from on-network behaviour and use it as the basis for credit decisions.

**Score components**:

| Factor | Weight | Source |
|--------|--------|--------|
| Successful x402 settlements | 25% | Facilitator receipts |
| Uptime and availability | 15% | Agent heartbeat, service health |
| Delivery SLA compliance | 15% | Invoice TTL vs settlement time |
| Refund and dispute ratio | 15% | Webhook events |
| Signer continuity | 10% | Key rotation frequency, key age |
| Treasury cushion | 10% | Balance-to-liability ratio |
| Verified counterparties | 10% | Number and quality of trade partners |

**Important compliance note**: This internal reputation score is not automatically a regulated "credit bureau" product. However, once you start making formal credit decisions based on it or reporting the data externally, FCRA (Fair Credit Reporting Act) and ECOA (Equal Credit Opportunity Act) obligations apply. See [Compliance](./revenue.md#compliance-requirements) for details.

**Mitigation**: Keep the score internal. Do not sell it. Do not report it to external parties. Use it only for on-network credit decisions within the closed-loop system.

---

## 7. White-Label Credit Rails

Let other businesses launch their own credit programs on the platform.

**What partners can build**:

| Program Type | Description |
|-------------|-------------|
| **Branded credit lines** | Partner-branded revolving credit for their customers |
| **Invoice advances** | Receivables financing under partner's brand |
| **Embedded B2B financing** | Credit integrated into partner's procurement flow |
| **Supplier credit** | Financing for a partner's supply chain |
| **API usage credit** | Pre-approved spend for API consumption |
| **Agent spend accounts** | Managed credit wallets for partner's agents |

**How this works**:

1. Partner defines their underwriting criteria, limits, and pricing.
2. The platform provides the [four engines](./overview.md#the-four-engines) as infrastructure.
3. Partner's customers interact with the partner's brand — the credit OS runs underneath.
4. Settlement, collections, and reporting flow through x402 rails.
5. The partner earns the spread; the platform earns infrastructure fees.

**This turns the system into a credit infrastructure company**, not just a lender. Revenue shifts from direct lending yield to platform fees, which scales better and carries less balance-sheet risk.

---

## Best First Product

Start here:

> **UnyKorn Commercial Credit OS** — underwrites businesses and agents operating on the network; issues controlled credit lines in stablecoins; uses x402 receipts and settlement history as underwriting inputs; repays automatically from incoming network cash flows. No unsecured consumer lending.

For the recommended rollout sequence, see [Rollout Sequence](./revenue.md#rollout-sequence). For the rail strategy that supports these models, see [Rail Strategy](./rails.md).
