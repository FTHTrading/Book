# Rail Strategy

The [Credit Operating Layer](./overview.md) uses a split-rail model. Each settlement chain serves a specific role in the credit lifecycle. x402 is not the credit model — it is the **collection, authorisation, and settlement fabric** that all credit operations flow through.

---

## How x402 Fits

x402 provides the gateway for every payment-required, draw-required, and repay-required action in the credit system.

| x402 Function | Credit Use |
|---------------|------------|
| **Paid access** | Gated credit dashboards, underwriting reports, score lookups |
| **Metered consumption** | Usage-based credit draws (API spend, compute, data) |
| **Drawdown authorisation** | HTTP 402 challenge before credit disbursement — borrower must present valid proof |
| **Repayment triggers** | Auto-sweep incoming [facilitator settlements](../live/api-reference.md#verification-and-settlement) toward outstanding balances |
| **Proof of payment** | Cryptographic receipt for every drawdown and repayment event |
| **Machine-to-machine collections** | [Agent-to-agent](../apostle/commerce.md) auto-collection without human intervention |
| **Wallet-authenticated access** | SIWX (Sign-In With X) for repeat borrower access |
| **Facilitator-based settlement** | No merchant or borrower runs direct chain infrastructure — the [facilitator](../protocol/architecture.md) handles settlement |

The key insight: the x402 facilitator already supports [private deployment](../protocol/architecture.md) with custom KYC/KYT flows. A credit facilitator is the same pattern — a private facilitator that additionally checks credit limits and enforces drawdown policy before settling.

## Rail Assignment

| Rail | Role in Credit Stack | Best For |
|------|---------------------|----------|
| **Solana** | High-speed agent and merchant spend credit | Fast revolving draws, high-frequency agent spending, real-time credit replenishment |
| **Stellar** | Stablecoin treasury credit, business lending, institutional flows | Business disbursements, repayment routing, sponsored/gas-abstracted flows |
| **Apostle Chain (7332)** | Credit memory, policy enforcement, attestation | Risk grades, limit records, credit events, agent spend tracking |
| **UnyKorn L1 (7331)** | USDF settlement anchoring, receipt batching | Final settlement, audit trail, [L1 anchoring](../live/api-reference.md#l1-anchoring) |
| **XRPL** | Cross-border settlement, xUSDF routing | International business credit settlement |

## Solana for Credit

Solana supports all SPL and Token-2022 tokens. The x402 ecosystem includes `@x402/svm`, and the facilitator handles Solana-specific duplicate-settlement protection (blockhash lifetime).

**What Solana is good for in the credit stack**:

- Fast revolving credit draws (sub-second finality).
- Low-friction merchant settlement.
- High-frequency [agent spending](./models.md#4-agent-credit) — agents can draw and spend hundreds of times per hour.
- Real-time credit replenishment from incoming payments.
- Consumer-like UX if the product ever moves to lighter-weight retail models.

**Operational considerations**:

| Concern | Mitigation |
|---------|------------|
| Duplicate settlement | x402 facilitator handles blockhash-based dedup |
| Transaction lifecycle sensitivity | Solana flow design is more lifecycle-sensitive than EVM signed-message models — the facilitator abstracts this |
| Congestion during network spikes | Rate-limit credit draws during high-congestion periods; queue and retry |

## Stellar for Credit

Stellar supports SEP-41/Soroban tokens and uses `token.transfer(from, to, amount)`. The TypeScript SDK supports sponsored transactions (gas abstraction).

**What Stellar is good for in the credit stack**:

- Stablecoin credit products (USDF, USDC).
- Treasury-grade settlement with strong institutional positioning.
- Sponsored/gas-abstracted flows — borrowers never pay gas.
- Business disbursements and repayment routing.
- Cleaner regulatory positioning for institutional credit products.

**Maturity note**: x402 clearly supports Stellar, and the ecosystem lists it as a rail. However, the current x402 seller quickstart's production guidance explicitly names Base and Solana. Treat Stellar as **supported but facilitator-maturity dependent** — validate the facilitator deployment path before committing Stellar as the primary rail for a production credit product.

## Apostle Chain for Credit Memory

The [Apostle Chain](../apostle/overview.md) is not a settlement rail for external credit — it is the **internal credit memory and policy layer**.

**What lives on the Apostle Chain**:

| Data | Purpose |
|------|---------|
| Risk grade history | Every grade assignment and transition, timestamped |
| Credit limit records | Current and historical limits per entity |
| Credit events | Drawdowns, repayments, delinquencies, restructures |
| Agent spend tracking | Real-time [agent credit](./models.md#4-agent-credit) balances and velocity |
| Policy attestations | Policy engine decisions (approvals, denials, adverse actions) |
| Collateral locks | Tokenised asset locks for [asset-backed credit](./models.md#5-tokenised-asset-backed-credit) |

This data is queryable via the existing [Apostle Chain API](../apostle/overview.md#api-endpoints). The proposer produces blocks only when credit events occur — no empty block overhead.

## UnyKorn L1 for Settlement Anchoring

[UnyKorn L1](../chain/overview.md) anchors final settlement receipts. Every credit drawdown and repayment that settles on Solana or Stellar produces a receipt that is [batch-anchored](../live/api-reference.md#l1-anchoring) to L1. This creates an immutable audit trail independent of any single settlement rail.

## Rail Selection Flow

```text
Credit Event
    │
    ├─ Agent spend draw?
    │     └─ Apostle Chain (policy check) → Solana (fast settlement)
    │
    ├─ Business credit disbursement?
    │     └─ Apostle Chain (policy check) → Stellar (stablecoin settlement)
    │
    ├─ Invoice advance?
    │     └─ Apostle Chain (policy check) → Stellar or Solana (borrower preference)
    │
    ├─ Repayment auto-sweep?
    │     └─ Same rail as original disbursement → Apostle Chain (credit event log)
    │
    └─ Receipt anchoring?
          └─ UnyKorn L1 (batch anchor)
```

Every path starts with a policy check on the Apostle Chain and ends with a receipt anchor on UnyKorn L1.

For revenue streams generated by this rail infrastructure, see [Revenue and Compliance](./revenue.md). For the credit models these rails support, see [Credit Models](./models.md).
