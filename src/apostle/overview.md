# Apostle Chain — Chain 7332

The Apostle Chain is a sovereign Rust/Axum Layer 1 blockchain purpose-built for autonomous AI agent commerce. While [UnyKorn L1](../chain/overview.md) handles USDF stablecoin settlement, the Apostle Chain is where the [20-agent mesh](./agents.md) transacts and settles in real time.

---

## Chain Specifications

| Property | Value |
|----------|-------|
| **Chain Name** | Apostle Chain |
| **Chain ID** | 7332 |
| **Native Token** | ATP (Apostle Token Primitive) |
| **Decimals** | 18 |
| **Language** | Rust + Axum |
| **Port** | 7332 |
| **Crates** | 10 — apostle-{api, types, ledger, consensus, settlement, bridge, mesh} + subcrates |
| **Status** | **Live** — production on AWS EC2 |

## Current State (April 2026)

| Metric | Value |
|--------|-------|
| **Block Height** | 3,400+ |
| **Registered Agents** | 20 |
| **Total Transactions** | 1,500+ |
| **Transaction Latency** | < 50ms |
| **Proposer** | Active — produces blocks on tx arrival (no empty blocks) |

## API Endpoints

| Endpoint | Method | Purpose |
|----------|--------|---------|
| `/health` | GET | Health check |
| `/status` | GET | Chain height, agent count, mempool size |
| `/v1/tx` | POST | Submit signed transaction (TxEnvelope) |
| `/v1/airdrop` | POST | Mint ATP to agents (genesis only) |
| `/v1/agents/register` | POST | Register new agent |
| `/v1/agents/batch-register` | POST | Batch register agents |
| `/v1/agents/heartbeat` | POST | Agent heartbeat |
| `/v1/agent/{id}/balance` | GET | Query agent balance (accepts bare UUID or `agent:UUID`) |
| `/v1/receipts` | GET | Query settlement receipts |

For full request/response examples, see the [API Reference](../live/api-reference.md).

## TxEnvelope Format

Every transaction on the Apostle Chain is a signed `TxEnvelope`:

```json
{
  "hash": "64-char hex (no 0x prefix)",
  "from": "bare-uuid",
  "to": "bare-uuid",
  "nonce": 42,
  "chain_id": 7332,
  "payload": {
    "type": "transfer",
    "to": "bare-uuid",
    "asset": "ATP",
    "amount": "3000000000000000000"
  },
  "signature": "128-char hex (Ed25519)",
  "timestamp": "2026-04-05T12:00:00Z"
}
```

> **Serialization note**: The `amount` field must be a **string** in JSON. Rust's serde internally-tagged enums do not support `u128` even with `arbitrary_precision`. All amounts use the `str_u128` serialization module.

## Settlement Bridges

| Bridge | Asset | Direction | Status |
|--------|-------|-----------|--------|
| **XRPL** | xUSDF / ATP | Bidirectional | Built |
| **Stellar** | sUSDF / ATP | Bidirectional | Built |

## Crate Architecture

```text
apostle-chain/
├── crates/
│   ├── apostle-api/          # Axum routes, handlers, middleware
│   ├── apostle-types/        # Asset enum, TxPayload, AgentId, str_u128
│   ├── apostle-ledger/       # Balance state, nonce tracking, account management
│   ├── apostle-consensus/    # Proposer loop (50ms tick), block finalization
│   ├── apostle-settlement/   # Cross-chain settlement execution
│   ├── apostle-bridge/       # XRPL + Stellar bridge adapters
│   └── apostle-mesh/         # Agent discovery, heartbeat, mesh topology
```

For the agents running on this chain, see [The Agent Mesh](./agents.md). For commerce routing, see [Commerce Routing and ATP](./commerce.md).
