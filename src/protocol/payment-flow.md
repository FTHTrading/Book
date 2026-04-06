# Payment Flow

## The x402 Payment Lifecycle

Every x402 payment follows a deterministic 6-step flow embedded in a single HTTP request cycle.

### Step 1: Request

An AI agent sends an HTTP request to a paid endpoint:

```
GET /v1/settle HTTP/1.1
Host: settlement.unykorn.org
Authorization: Bearer agent:87724c76-da93-4b1a-9fa6-271ba856338e
```

### Step 2: Challenge (HTTP 402)

The x402 gateway returns HTTP 402 with payment requirements:

```
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

### Step 3: Payment Proof Construction

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

### Step 4: Retry with Proof

The agent retries the original request with the payment proof attached:

```
GET /v1/settle HTTP/1.1
Host: settlement.unykorn.org
X-PAYMENT: <base64-encoded signed TxEnvelope>
```

### Step 5: Verification & Settlement

The facilitator:
1. **Verifies** the Ed25519 signature
2. **Checks** nonce for replay protection
3. **Debits** the sender's balance
4. **Credits** the receiver's balance
5. **Creates** a cryptographic receipt

### Step 6: Delivery + Receipt

```
HTTP/1.1 200 OK
X-RECEIPT: rcpt_a1b2c3...
Content-Type: application/json

{ "data": "..." }
```

The receipt is batched into a Merkle tree and periodically anchored on-chain.

---

## Payment Rails

x402 supports multiple settlement rails:

| Rail | Asset | Role | Latency |
|------|-------|------|---------|
| **Apostle Chain (7332)** | ATP | Primary sovereign settlement | < 50ms |
| **UnyKorn L1 (7331)** | USDF | Canonical ledger, channel finality | < 1s |
| **Stellar** | sUSDF | Bridge rail, Soroban auth-entry | ~5s |
| **XRPL** | xUSDF | Mirror rail, IOU distribution | ~4s |

The Facilitator selects the optimal rail based on amount, urgency, and policy.
