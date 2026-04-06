# Payment Flow

Every x402 payment follows a deterministic six-step lifecycle embedded in a single HTTP request cycle. This page walks through each step with exact payload formats.

---

## Step 1 — Request

An AI agent sends a standard HTTP request to a paid endpoint:

```http
GET /v1/settle HTTP/1.1
Host: settlement.unykorn.org
Authorization: Bearer agent:87724c76-da93-4b1a-9fa6-271ba856338e
```

## Step 2 — Challenge (HTTP 402)

The [x402 Gateway](./architecture.md) returns HTTP 402 with payment requirements:

```http
HTTP/1.1 402 Payment Required
X-PAYMENT-REQUIRED: {
  "asset": "ATP",
  "amount": "3000000000000000000",
  "receiver": "agent:a1b2c3d4-...",
  "namespace": "x402.route.settlement",
  "nonce": "n_abc123",
  "ttl_seconds": 300
}
```

The challenge specifies the asset, amount (in base units), receiving agent, namespace, and time-to-live.

## Step 3 — Proof Construction

The agent constructs a signed payment proof using its Ed25519 keypair:

```json
{
  "hash": "a1b2c3d4...64hex",
  "from": "87724c76-da93-4b1a-9fa6-271ba856338e",
  "to": "receiver-uuid",
  "asset": "ATP",
  "amount": "3000000000000000000",
  "nonce": 42,
  "chain_id": 7332,
  "signature": "128hex...",
  "timestamp": "2026-04-05T12:00:00Z"
}
```

> The `amount` field must be a **string** in JSON. See [Apostle Chain Overview](../apostle/overview.md) for the `str_u128` serialization requirement.

## Step 4 — Retry with Proof

The agent retries the original request with the payment proof attached:

```http
GET /v1/settle HTTP/1.1
Host: settlement.unykorn.org
X-PAYMENT: <base64-encoded signed TxEnvelope>
```

## Step 5 — Verification and Settlement

The [Facilitator](./architecture.md) processes the proof:

1. **Verify** the Ed25519 signature
2. **Check** nonce for replay protection
3. **Debit** the sender's balance
4. **Credit** the receiver's balance
5. **Create** a cryptographic receipt

## Step 6 — Delivery and Receipt

```http
HTTP/1.1 200 OK
X-RECEIPT: rcpt_a1b2c3...
Content-Type: application/json

{ "data": "..." }
```

The receipt is batched into a Merkle tree and periodically anchored on-chain.

---

## Payment Rails

x402 supports multiple settlement rails. The Facilitator selects the optimal rail based on amount, urgency, and policy.

| Rail | Asset | Role | Latency |
|------|-------|------|---------|
| **Apostle Chain (7332)** | ATP | Primary sovereign settlement | < 50ms |
| **UnyKorn L1 (7331)** | USDF | Canonical ledger, channel finality | < 1s |
| **Stellar** | sUSDF | Bridge rail, Soroban auth-entry | ~5s |
| **XRPL** | xUSDF | Mirror rail, IOU distribution | ~4s |

For the full architectural breakdown of these components, see [System Architecture](./architecture.md).
