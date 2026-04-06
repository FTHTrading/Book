# System Architecture

The x402 stack is a layered system with clear separation between client interaction, edge enforcement, settlement logic, and chain finality.

---

## Architecture Diagram

```text
┌─────────────────────────────────────────────────────────────────┐
│                    CLIENT / AGENT LAYER                          │
│  Browser  │  AI Agent  │  Wallet  │  Enterprise Client          │
│                    │                                             │
│              x402 Wallet SDK (JS)                                │
│  • intercept 402  • resolve namespaces  • sign channel spends   │
└────────────────────┬────────────────────────────────────────────┘
                     │
                     ▼
┌─────────────────────────────────────────────────────────────────┐
│              x402 EDGE GATEWAY (Cloudflare Worker)               │
│  Route matching • 402 generation • Proof forwarding • Rate limit │
└────────────────────┬────────────────────────────────────────────┘
                     │
                     ▼
┌─────────────────────────────────────────────────────────────────┐
│                   UNYKORN FACILITATOR                             │
│  Invoice Service  │  Payment Verifier  │  Namespace Resolver     │
│  Channel Manager  │  Receipt Batcher   │  Policy Engine          │
│  Replay Guard     │  Credit Ledger     │  Rail Adapters          │
└────────────────────┬────────────────────────────────────────────┘
                     │
          ┌──────────┼──────────┐
          ▼          ▼          ▼
    ┌──────────┐ ┌────────┐ ┌──────┐
    │UnyKorn L1│ │Stellar │ │ XRPL │
    │ USDF     │ │ sUSDF  │ │xUSDF │
    │ channels │ │ auth   │ │ IOU  │
    │ anchoring│ │ bridge │ │mirror│
    └──────────┘ └────────┘ └──────┘
```

## Component Responsibilities

| Component | Owns | Does Not Own |
|-----------|------|-------------|
| **Cloudflare Worker** | HTTP 402 enforcement, route matching, rate limiting | Settlement logic, state |
| **Facilitator** | Proof verification, replay protection, invoice management, receipt creation | HTTP routing, file serving |
| **UnyKorn L1 (7331)** | Canonical USDF ledger, payment channels, receipt anchoring | Client interaction |
| **Apostle Chain (7332)** | Sovereign agent settlement (ATP), agent registry, commerce mesh | USDF operations |
| **Stellar** | Bridge rail, Soroban auth-entry compatibility | Primary settlement |
| **XRPL** | Mirror rail, xUSDF distribution | Settlement authority |

## Package Map

The monorepo contains 40+ packages across multiple languages.

### Core Infrastructure

| Package | Language | Purpose |
|---------|----------|---------|
| `x402-core` | TypeScript | Protocol types, payment proof construction |
| `x402-facilitator` | TypeScript / Fastify | Settlement brain (port 3100) |
| `x402-gateway` | TypeScript / CF Worker | Edge enforcement |
| `x402-treasury` | TypeScript / Fastify | Wallet management (port 3200) |
| `x402-guardian` | TypeScript / Fastify | 8-daemon monitoring army (port 3300) |
| `x402-financial-core` | Rust | Ledger, settlement, vault, risk — 6 crates (port 4400) |

### Agent and Commerce

| Package | Language | Purpose |
|---------|----------|---------|
| `x402-agent-ecosystem` | Python | 20-agent commerce mesh and [Digital Twin](../live/digital-twin.md) |
| `agent-core` | TypeScript | AgentRegistry, TaskManager, BudgetManager |
| `a2a-sdk` | TypeScript | Agent-to-agent cards, routing, messages |
| `mcp-servers` | TypeScript | 14 MCP server definitions |

### Settlement and Finance

| Package | Language | Purpose |
|---------|----------|---------|
| `settlement-engine` | TypeScript | Receipt creation, Merkle trees, signing |
| `x402-stellar-bridge` | TypeScript | Stellar / Soroban x402 integration |
| `genesis-ledger` | TypeScript | Double-entry append-only ledger |
| `treasury-core` | TypeScript | Treasury state, refill logic, spend caps |

### Visualization and Interface

| Package | Language | Purpose |
|---------|----------|---------|
| `x402-mesh-pulse` | TypeScript | Real-time event bus (13+ event types) |
| `unykorn-explorer` | TypeScript | Block explorer |
| `unykorn-ico` | TypeScript | ICO site at ico.unykorn.org |
| `x402-site` | TypeScript | Marketing site |

For how these components interact during a payment, see [Payment Flow](./payment-flow.md). For the settlement chains in detail, see [UnyKorn L1](../chain/overview.md) and [Apostle Chain](../apostle/overview.md).
