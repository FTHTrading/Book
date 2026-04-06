# Security

Defence-in-depth across every layer of the [x402 protocol](../protocol/what-is-x402.md), the [Apostle Chain](../apostle/overview.md), and the Facilitator.

---

## Cryptographic Foundation

| Component | Algorithm | Key Size | Purpose |
|-----------|-----------|----------|---------|
| **Agent Identity** | Ed25519 | 256-bit | Transaction signing |
| **Transaction Hash** | SHA-256 | 256-bit | Tx integrity |
| **Webhook Verification** | HMAC-SHA256 | 256-bit | Webhook authenticity |
| **Nonce** | Monotonic u64 | 64-bit | Replay prevention |
| **Chain ID** | 7332 | — | Cross-chain replay prevention |

## Transaction Security

Every transaction on the [Apostle Chain](../apostle/overview.md) must satisfy:

1. **Valid Ed25519 signature** — signed by the `from` agent's private key
2. **Correct nonce** — must equal the agent's current nonce (monotonically increasing)
3. **Correct chain_id** — must be 7332 (prevents replay on other chains)
4. **Sufficient balance** — sender must have enough ATP
5. **Valid recipient** — `to` agent must exist on-chain

```text
TxEnvelope {
  hash: SHA-256(payload),
  from: agent_uuid,
  nonce: current_nonce,      ← must match ledger
  chain_id: 7332,            ← binds to Apostle Chain
  payload: { ... },
  signature: Ed25519(hash),  ← proves ownership
  timestamp: ISO 8601
}
```

For the full TxEnvelope schema, see [Apostle Chain Overview](../apostle/overview.md#txenvelope-format).

## Key Management

| Key Type | Storage | Access |
|----------|---------|--------|
| Agent Ed25519 keys | `keys/agents.json` on EC2 | Encrypted at rest |
| SSH keys | `~/.ssh/unykorn-devnet-key.pem` | Local only |
| Cloudflare API tokens | `.env` files | Per-project scoped |

## Network Security

| Layer | Protection |
|-------|-----------|
| **Cloudflare** | DDoS protection, SSL termination, DNS proxy |
| **Security Groups** | Port-level access control (SSH: admin, 8402: public, rest: internal) |
| **EC2** | Amazon Linux 2023, auto-patching |
| **WebSocket** | Internal network only (3280 not exposed publicly) |

## Invoice Security (Facilitator)

The x402 Facilitator implements:

- **Time-locked invoices** — TTL-based expiration (default: 300s)
- **Nonce binding** — each invoice has a unique nonce, preventing replay
- **Proof verification** — cryptographic proof required before settlement
- **Rate limiting** — per-namespace, configurable per-policy
- **Namespace isolation** — each service route is scoped to its namespace

## Planned Improvements (Q3 2026)

| Enhancement | Description |
|-------------|------------|
| **Multi-sig treasury** | Replace single-key spending with 3-of-5 multi-sig |
| **Key rotation** | Automated agent key rotation with on-chain migration |
| **Audit logging** | Immutable audit trail for all admin operations |
| **Anomaly detection** | Statistical analysis of transaction patterns |
| **Hardware security** | HSM integration for treasury keys |

For treasury governance controls, see [Treasury and Governance](../chain/treasury.md). For the roadmap timeline, see [Roadmap](./roadmap.md).
