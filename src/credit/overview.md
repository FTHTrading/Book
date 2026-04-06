# Credit Operating Layer

The UnyKorn Commercial Credit OS is a closed-loop credit operating layer built on top of the [x402 protocol](../protocol/what-is-x402.md). It underwrites businesses and agents operating on the network, issues controlled credit lines in stablecoins, and uses x402 receipts and settlement history as underwriting inputs. Repayment happens automatically from incoming network cash flows.

This is not a consumer credit product. It is **commercial network credit** — purpose-built for businesses, merchants, and AI agents that already transact on the x402 stack.

---

## Why Build a Credit Company on x402

The x402 protocol already treats payment proof, receipts, settlement rails, and agent commerce as first-class primitives. The system has live data flowing through the [Apostle Chain](../apostle/overview.md), [UnyKorn L1](../chain/overview.md), and cross-chain bridges to Stellar and XRPL. That data — payment timeliness, settlement success rates, treasury balances, commerce volume — is exactly the raw signal a credit underwriter needs.

Building a credit layer on this stack is not bolting lending onto a blockchain. It is closing the loop: the same system that moves money also decides who gets to borrow it, how much, and under what terms.

## The Four Engines

The credit operating layer is composed of four engines, each with a distinct responsibility.

### Identity Engine

The identity engine binds a credit entity to a verifiable on-chain identity.

| Component | Purpose |
|-----------|---------|
| **Wallet** | Cryptographic identity (Ed25519 keypair or multi-sig) |
| **Namespace** | [x402 namespace](../live/api-reference.md#namespaces) binding the entity to a service route |
| **KYB / KYC** | Business or individual verification (closed-network initially) |
| **Signer History** | Historical key usage, rotation events, signing cadence |
| **Repayment Entity** | Legal or programmatic entity responsible for repayment |
| **Allowed Rails** | Which settlement chains the entity may use (Apostle, Stellar, Solana, XRPL) |

The identity engine does not replace external identity providers. It maps external identity into the on-chain context so the other three engines can operate without ambiguity about who owes what.

### Behavior Engine

The behavior engine collects and normalises on-network activity into underwriting inputs.

| Signal | Source | Weight |
|--------|--------|--------|
| **x402 payment history** | [Facilitator receipts](../live/api-reference.md#receipts-and-batching) | High |
| **Invoice payment timeliness** | Invoice creation and settlement timestamps | High |
| **API usage stability** | Metered consumption patterns via x402 pay-per-request | Medium |
| **Treasury balances** | [On-chain balance queries](../apostle/overview.md#api-endpoints) | Medium |
| **Settlement success rate** | Ratio of attempted to completed settlements | High |
| **Chargeback and dispute events** | Webhook events (`payment.received`, channel disputes) | High |
| **Business revenue flows** | Aggregate inbound payment volume over rolling windows | Medium |

All signals are derived from data the system already produces. No external bureau pull is required for the initial closed-loop product.

### Decision Engine

The decision engine converts behaviour signals into credit decisions.

| Function | Description |
|----------|-------------|
| **Internal risk grades** | Letter grades (A through F) derived from behaviour engine scores |
| **Credit limits** | Maximum outstanding balance, per-draw limits, velocity caps |
| **APR and fees** | Risk-adjusted pricing tied to grade and collateral |
| **Collateral requirements** | Minimum treasury or locked-asset ratios per grade |
| **Reserve requirements** | Platform-level reserve pools sized to expected loss rates |
| **Dynamic throttles** | Real-time limit adjustments based on deteriorating signals |
| **Adverse-action logging** | Every denial or downgrade logged with specific reasons (required for compliance) |

The decision engine is auditable by design. Every approval, denial, limit change, and grade transition is timestamped and stored on-chain or in the credit ledger.

### Settlement Engine

The settlement engine handles the full credit lifecycle.

| Stage | Description |
|-------|-------------|
| **Issuance** | Credit line created, terms set, stablecoin allocation reserved |
| **Drawdown** | Borrower draws against the line; x402 authorisation gate enforces limits |
| **Repayment** | Auto-sweep from incoming settlements, or manual repayment via x402 payment proof |
| **Delinquency** | Missed-payment detection, grace period management, late-fee assessment |
| **Restructuring** | Term modification for performing-but-stressed borrowers |
| **Collections routing** | Escalation path: auto-sweep → notification → throttle → suspension → external |
| **Securitisation pools** | Optional: bundle performing credit into tokenised tranches for capital recycling |

The settlement engine uses x402 for drawdown authorisation and repayment triggers. See [How x402 Fits](./rails.md#how-x402-fits) for the integration model.

## Design Principles

1. **Closed-loop first** — underwrite only entities that transact on the network.
2. **Commercial only** — no unsecured consumer lending until the compliance stack is locked.
3. **Data-native** — every underwriting input comes from on-network behaviour, not external bureau data.
4. **Programmatic enforcement** — credit limits enforced at the protocol level, not by manual review.
5. **Auditable** — every decision is logged with its inputs and rationale.

## Architecture Diagram

```text
┌─────────────────────────────────────────────────────────┐
│                    Credit Operating Layer                │
│                                                         │
│  ┌──────────┐  ┌──────────┐  ┌──────────┐  ┌────────┐  │
│  │ Identity │→│ Behavior │→│ Decision │→│ Settle │  │
│  │  Engine  │  │  Engine  │  │  Engine  │  │ Engine │  │
│  └──────────┘  └──────────┘  └──────────┘  └────────┘  │
│       ↑              ↑              │            │       │
└───────┼──────────────┼──────────────┼────────────┼──────┘
        │              │              │            │
   ┌────┴────┐    ┌────┴────┐   ┌────┴────┐  ┌───┴────┐
   │ Wallets │    │  x402   │   │ Credit  │  │ Rails  │
   │   KYB   │    │Receipts │   │ Ledger  │  │Solana  │
   │Namespace│    │Invoices │   │On-Chain │  │Stellar │
   └─────────┘    └─────────┘   └─────────┘  │Apostle │
                                              │  XRPL  │
                                              └────────┘
```

For the seven credit models this layer supports, see [Credit Models](./models.md). For the rail strategy, see [Rail Strategy](./rails.md).
