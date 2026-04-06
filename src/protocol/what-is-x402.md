# What Is x402?

x402 embeds payment directly into the HTTP request/response cycle using the long-reserved `402 Payment Required` status code. It gives AI agents a native way to pay for API calls — no human approval, no separate transaction flow, no multi-second block confirmations.

---

## The Problem

HTTP 402 was reserved in RFC 7231 for "future use" and never standardized. Meanwhile, AI agents need to:

- Pay for API calls autonomously — no human clicking "approve"
- Settle micropayments in real time, not in 3–5 business days
- Negotiate pricing and access tiers machine-to-machine
- Operate without human intervention per transaction

No existing blockchain or payment rail solves this at the HTTP layer.

## Why Existing Chains Fall Short

General-purpose chains optimize for DeFi, NFTs, and smart contracts. They are not designed for embedded HTTP commerce.

| Requirement | Ethereum / Solana | x402 |
|-------------|-------------------|------|
| Sub-second micropayment settlement | 12s+ blocks, $0.01–$5 fees | < 50ms, zero platform fees |
| HTTP-native payment in request/response | Separate transaction flow | Built into HTTP 402 cycle |
| Agent identity and namespace resolution | Raw addresses only | Hierarchical `x402.*` namespaces |
| Prepaid credit with Ed25519 proofs | Not native | Zero-latency payment channels |
| Receipt batching with Merkle anchoring | Must build from scratch | Native protocol feature |

## The x402 Model

x402 uses HTTP 402 to embed payment directly into the API call:

```text
┌──────────┐                    ┌───────────────┐
│ AI Agent │ ── GET /api/data → │ x402 Gateway  │
│          │                    │               │
│          │ ← HTTP 402 ────── │ Payment       │
│          │   + price: 0.5 ATP│ Required      │
│          │   + receiver: ... │               │
│          │                    │               │
│          │ ── GET /api/data → │               │
│          │   + X-PAYMENT:    │ Verify →      │
│          │     signed proof  │ Settle →      │
│          │                    │ Receipt →     │
│          │ ← HTTP 200 ────── │ Deliver       │
│          │   + data payload  │               │
└──────────┘                    └───────────────┘
```

The entire flow — challenge, payment, verification, settlement, receipt — happens in a single HTTP round-trip. The agent never leaves the API call context.

**Protocol version**: `x402/2.0`

## Key Properties

| Property | Detail |
|----------|--------|
| **Zero platform fees** | ATP transfers cost nothing beyond chain gas |
| **Sub-50ms settlement** | Prepaid credit channels bypass block confirmation |
| **Cryptographic receipts** | Every payment produces a signed, verifiable receipt |
| **Merkle-batched anchoring** | Receipts are batched into Merkle trees and anchored on-chain |
| **Machine-first** | No CAPTCHAs, no OAuth, no human-in-the-loop |
| **Auditable** | Full chain of custody: request → payment → receipt → anchor |

For the full payment lifecycle, see [Payment Flow](./payment-flow.md). For the component architecture behind this model, see [System Architecture](./architecture.md).
