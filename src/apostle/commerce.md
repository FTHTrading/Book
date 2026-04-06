# Commerce Routing and ATP

The [Apostle Chain](./overview.md) is not a test-net with random transfers — every transaction represents a real service interaction between [mesh agents](./agents.md). This page documents the commerce map, service URIs, ATP token properties, and the circular economy that keeps the mesh self-sustaining.

---

## How Agents Trade

Agent A pays Agent B for a specific economic function — purpose-driven commerce, not random transfers.

The commerce map defines **who pays whom for what**:

```text
settlement       → treasury          (settlement fees)
exchange-listing → intelligence      (price intelligence)
onboarding       → documentation     (doc lookups)
market-monitor   → exchange-listing  (trade signals)
wallet-ops       → compliance        (KYC checks)
reconciliation   → settlement        (ledger access)
incident-response→ risk              (risk assessments)
listing-packet   → settlement        (listing execution)
customer-desk    → onboarding        (agent routing)
compliance       → policy            (policy queries)
risk             → compliance        (risk signoffs)
policy           → treasury          (budget approval)
treasury         → genesis-treasury  (treasury sweeps)
intelligence     → market-monitor    (data feeds)
documentation    → listing-packet    (doc packaging)
chairman         → treasury          (chairman directives)
operator         → x402-credit-pool  (infra payments)
```

Each agent has multiple possible targets — one is picked at random per cycle, introducing economic variety while preserving the role-based logic.

## x402 Service URIs

Every commerce route maps to an `x402://` service URI — the canonical address of the service being purchased:

| Agent | Service URI |
|-------|------------|
| settlement | `x402://settlement.unykorn.org/v1/settle` |
| exchange-listing | `x402://exchange.unykorn.org/v1/list` |
| onboarding | `x402://onboarding.unykorn.org/v1/register` |
| market-monitor | `x402://monitor.unykorn.org/v1/price-feed` |
| wallet-ops | `x402://wallet.unykorn.org/v1/provision` |
| reconciliation | `x402://ledger.unykorn.org/v1/reconcile` |
| incident-response | `x402://guardian.unykorn.org/v1/triage` |
| listing-packet | `x402://assets.unykorn.org/v1/package` |
| customer-desk | `x402://desk.unykorn.org/v1/route` |
| compliance | `x402://compliance.unykorn.org/v1/check` |
| risk | `x402://risk.unykorn.org/v1/assess` |
| policy | `x402://policy.unykorn.org/v1/enforce` |
| treasury | `x402://treasury.unykorn.org/v1/sweep` |
| intelligence | `x402://intel.unykorn.org/v1/aggregate` |
| documentation | `x402://docs.unykorn.org/v1/fetch` |

## ATP (Apostle Token Primitive)

ATP is the native token of the Apostle Chain. It is the unit of exchange for all agent commerce.

| Property | Value |
|----------|-------|
| **Symbol** | ATP |
| **Aliases** | APO, atp, apo |
| **Decimals** | 18 |
| **Serialization** | `"ATP"` in JSON (via `Asset::Apo`) |
| **Amount format** | String in JSON (e.g., `"3000000000000000000"` = 3 ATP) |

### Other Assets on Apostle Chain

| Asset | Ticker | Decimals | Purpose |
|-------|--------|----------|---------|
| ATP | ATP | 18 | Native commerce token |
| UNY | UNY | 18 | L1 governance token (bridged) |
| USDF | USDF | 7 | Stablecoin (bridged from Stellar/XRPL) |
| XRP | XRP | 6 | XRPL bridge asset |
| XLM | XLM | 7 | Stellar bridge asset |

## Transaction Flow

A single commerce cycle for an agent:

```text
1. Agent wakes up (tier-based sleep interval)

2. Pick target from COMMERCE_MAP
   └─ e.g., settlement picks [treasury, genesis-treasury]
   └─ randomly selects one

3. Fetch current nonce
   └─ GET /v1/agent/{id}/balance → { nonce: N }

4. Pick amount from TIER_AMOUNTS
   └─ e.g., execution tier: 1.0 – 10.0 ATP
   └─ convert to base units: 3.5 ATP → "3500000000000000000"

5. Build TxEnvelope
   └─ tx_builder.build_transfer_tx(...)
   └─ hash = SHA-256(payload), sign with Ed25519

6. Submit to chain
   └─ POST /v1/tx → { ok: true, hash: "..." }
   └─ fast-path settlement + mempool insertion

7. Emit to WebSocket
   └─ { event: "x402_transaction", from, to, amount, tier, ... }
   └─ picked up by mesh-pulse → digital twin animation

8. Sleep (tier-based interval)
   └─ e.g., execution: 10 – 40 seconds
```

For endpoint details, see the [API Reference](../live/api-reference.md). For the TxEnvelope schema, see [Apostle Chain Overview](./overview.md#txenvelope-format).

## Commerce Economics

The mesh creates a self-sustaining circular economy:

```text
Operators
  │
  ▼ (fund)
Control ←── Policy approval ── Execution
  │                                │
  ▼ (govern)                       ▼ (work)
Treasury ←── Sweeps ── Settlement ← Listing
  │                                │
  ▼ (allocate)                     ▼ (inform)
Intelligence ←── Data ── Market Monitor
  │
  ▼ (serve)
Interface (customer-desk)
```

ATP flows **downward** from operators through control to execution. Data flows **upward** from intelligence through execution to control. The treasury acts as the central monetary authority, receiving fees and sweeping funds to genesis-treasury.

For agent balances and tier economics, see [The Agent Mesh](./agents.md#tier-economics). For the [Digital Twin](../live/digital-twin.md), mesh-pulse transactions drive real-time visualisation. For how commerce data feeds credit underwriting, see [Credit Operating Layer](../credit/overview.md) and [Agent Credit](../credit/models.md#4-agent-credit).
