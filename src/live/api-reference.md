# API Reference

Complete endpoint reference for both the [Apostle Chain](../apostle/overview.md) (chain 7332) and the [x402 Facilitator](../protocol/architecture.md). All examples use `localhost` — replace with the production host as needed.

---

> **Apostle Chain Base URL**: `http://localhost:7332`
> **Facilitator Base URL**: `http://localhost:3100`

---

## Apostle Chain API (Chain 7332)

### Health and Status

#### `GET /health`
Returns chain health.

#### `GET /status`
```json
{
  "chain_id": 7332,
  "height": 3480,
  "agents": 20,
  "mempool_size": 0,
  "proposer": "active"
}
```

### Transactions

#### `POST /v1/tx`
Submit a signed TxEnvelope.

**Request Body** — `TxEnvelope`:
```json
{
  "hash": "a1b2c3...64hex",
  "from": "bare-uuid",
  "nonce": 42,
  "chain_id": 7332,
  "payload": {
    "type": "transfer",
    "to": "bare-uuid",
    "asset": "ATP",
    "amount": "3000000000000000000"
  },
  "signature": "128-char-hex-ed25519",
  "timestamp": "2026-04-03T14:00:00Z"
}
```

> **Important**: `amount` MUST be a string. Rust serde internally-tagged enums do not support `u128` even with `arbitrary_precision`. See [Apostle Chain Overview](../apostle/overview.md#txenvelope-format) for full details.

**Response 200**:
```json
{ "ok": true, "hash": "a1b2c3..." }
```

### Agent Management

#### `POST /v1/agents/register`
Register a new agent identity.

#### `POST /v1/agents/batch-register`
Register multiple agents in one call.

#### `POST /v1/agents/heartbeat`
Agent heartbeat signal.

#### `GET /v1/agent/{id}/balance`
Query agent balance. Accepts bare UUID or `agent:UUID`.

```json
{
  "agent_id": "agent:87724c76-da93-4b1a-9fa6-271ba856338e",
  "balances": { "ATP": "500000000000000000000000" },
  "nonce": 15
}
```

### Airdrop (Genesis Only)

#### `POST /v1/airdrop`

```json
{
  "recipients": [
    {
      "label": "treasury",
      "apo_amount": "100000000000000000000000",
      "agent_id": "2e5d78c2-1e58-4cf7-88eb-a139c6c620ef"
    }
  ]
}
```

**Response 200**:
```json
{
  "ok": true,
  "results": [
    {
      "agent_id": "agent:2e5d78c2-...",
      "label": "treasury",
      "private_key_hex": "...",
      "public_key_hex": "...",
      "apo_minted": "100000000000000000000000",
      "apo_balance": "100000000000000000000000"
    }
  ]
}
```

### Receipts

#### `GET /v1/receipts`
Query settlement receipts.

---

## x402 Facilitator API (Port 3100)

### Health and Admin

| Endpoint | Method | Purpose |
|----------|--------|---------|
| `/health` | GET | Service health |
| `/admin/stats` | GET | Aggregated statistics |
| `/admin/activity` | GET | Recent 20 receipts |

### Invoices

| Endpoint | Method | Purpose |
|----------|--------|---------|
| `/invoices` | POST | Create invoice (402 response trigger) |
| `/invoices/:id` | GET | Look up invoice |

**Create Invoice**:
```json
{
  "resource": "/api/v1/genesis/repro-pack/alpha",
  "namespace": "x402.route.genesis-repro",
  "asset": "USDF",
  "amount": "0.50",
  "receiver": "uny1_x402_TREASURY",
  "policy": { "kyc_required": false, "rate_limit": "100/hour" },
  "ttl_seconds": 300
}
```

### Verification & Settlement

#### `POST /verify`
Verify a payment proof and settle.

**Proof Types**:

| Type | Description | Rail |
|------|-------------|------|
| `prepaid_credit` | Deduct from USDF balance | unykorn-l1 |
| `channel_spend` | Spend from payment channel | unykorn-l1 |
| `signed_auth` | Stellar soroban auth | stellar |
| `tx_hash` | On-chain tx reference | any |

**Error Codes**: `invoice_not_found`, `invoice_redeemed`, `invoice_expired`, `nonce_mismatch`, `rate_limited`, `invalid_proof`, `insufficient_amount`, `channel_sequence_invalid`, `rail_not_allowed`

### Credit Ledger

| Endpoint | Method | Purpose |
|----------|--------|---------|
| `/credits/register` | POST | Register wallet + Ed25519 pubkey |
| `/credits/deposit` | POST | Deposit USDF |
| `/credits/:wallet` | GET | Balance |
| `/credits/:wallet/account` | GET | Full account details |
| `/credits/:wallet/transactions` | GET | Paginated tx history |

### Payment Channels

| Endpoint | Method | Purpose |
|----------|--------|---------|
| `/channels/open` | POST | Open channel with deposit |
| `/channels/:id` | GET | Channel state |
| `/channels/:id/close` | POST | Close channel |

### Receipts and Batching

| Endpoint | Method | Purpose |
|----------|--------|---------|
| `/receipts` | GET | List recent receipts |
| `/receipts/:id` | GET | Single receipt |

### Namespaces

| Endpoint | Method | Purpose |
|----------|--------|---------|
| `/namespaces` | GET | List all namespace records |
| `/namespaces/:ns` | GET | Look up specific namespace |

### L1 Anchoring

| Endpoint | Method | Purpose |
|----------|--------|---------|
| `/l1/health` | GET | UnyKorn L1 chain status |
| `/l1/batches` | GET | Recent receipt batches |
| `/l1/anchor` | POST | Trigger manual batch anchor |
| `/l1/batch/:id` | GET | Batch with receipts |

### Webhooks

| Endpoint | Method | Purpose |
|----------|--------|---------|
| `/webhooks` | POST | Create subscription |
| `/webhooks?wallet=` | GET | List subscriptions |
| `/webhooks/:id` | GET | Subscription details |
| `/webhooks/:id` | PATCH | Update subscription |
| `/webhooks/:id` | DELETE | Delete subscription |
| `/webhooks/:id/deliveries` | GET | Delivery history |
| `/webhooks/:id/test` | POST | Send test event |

**Webhook Events**: `payment.received`, `payment.batched`, `channel.opened`, `channel.closed`, `credit.deposited`, `anchor.confirmed`

**Verification**: HMAC-SHA256 — `HMAC(body, secret) === X-402-Signature`

For the protocol design behind these endpoints, see [Payment Flow](../protocol/payment-flow.md) and [System Architecture](../protocol/architecture.md).
