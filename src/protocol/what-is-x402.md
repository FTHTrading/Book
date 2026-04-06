# What is x402?

## The Problem AI Agents Face Today

The internet was built on a free-access model. HTTP 200 serves content. HTTP 401/403 handles auth. But **HTTP 402 — "Payment Required"** — was reserved in RFC 7231 for future use and never standardized.

As AI agents proliferate, they need to:

- **Pay for API calls** autonomously — no human clicking "approve"
- **Settle micropayments** in real-time (< 1 second, not 3-5 business days)
- **Negotiate pricing, access tiers, and service levels** machine-to-machine
- **Operate without human intervention** for each transaction

No existing blockchain or payment system solves this at the protocol level.

## Why Existing Chains Don't Work

General-purpose chains (Ethereum, Solana, etc.) optimize for DeFi, NFTs, and smart contracts. They are not designed for:

| Requirement | Ethereum/Solana | x402 |
|-------------|-----------------|------|
| Sub-second micropayment settlement | ❌ 12s+ blocks, $0.01-$5 fees | ✅ < 50ms, zero platform fees |
| HTTP-native payment in request/response | ❌ Separate tx flow | ✅ Built into HTTP 402 cycle |
| Agent identity & namespace resolution | ❌ Raw addresses only | ✅ Hierarchical `fth.*` namespaces |
| Prepaid credit with Ed25519 proofs | ❌ Not native | ✅ Zero-latency payment channels |
| Receipt batching with Merkle anchoring | ❌ Must build from scratch | ✅ Native protocol feature |

## The x402 Solution

x402 uses HTTP 402 to embed payment directly into the API request/response cycle:

```
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

**Protocol Version**: `fth-x402/2.0`

The entire flow — challenge, payment, verification, settlement, receipt — happens in a single HTTP round-trip. The agent never leaves the API call context.

## Key Properties

- **Zero platform fees** — ATP transfers cost nothing beyond chain gas
- **Sub-50ms settlement** — prepaid credit channels bypass block confirmation
- **Cryptographic receipts** — every payment produces a signed, verifiable receipt
- **Merkle-batched anchoring** — receipts are batched into Merkle trees and anchored on-chain
- **Machine-first** — no CAPTCHAs, no OAuth flows, no human-in-the-loop
- **Auditable** — full chain of custody from request → payment → receipt → anchor
