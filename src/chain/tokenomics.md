# UNY Token & Tokenomics

## Token Fundamentals

| Property | Value | Status |
|----------|-------|--------|
| **Name** | UnyKorn Token | LIVE |
| **Symbol** | UNY | LIVE |
| **Blockchain** | UnyKorn L1 (Chain ID 7331) | BUILT |
| **Decimals** | 18 | LIVE |
| **Total Supply** | 1,000,000,000 (1 billion) | LIVE |
| **Max Supply** | 1,000,000,000 (hard-capped) | LIVE |
| **Inflation** | 0% — no new tokens can ever be created | LIVE |
| **Mintable** | No | LIVE |
| **Pausable** | No | LIVE |
| **Upgradeable** | No | LIVE |

> **Architecture Note**: UNY exists in two forms:
> 1. **Native L1 UNY** — gas token on UnyKorn L1 (Chain 7331)
> 2. **Wrapped UNY (wUNY)** — planned ERC-20 for CEX listings and cross-chain interoperability

## Token Distribution

| Category | % | Tokens | Vesting |
|----------|---|--------|---------|
| **ICO / Public Sale** | 20% | 200,000,000 | Seed: 6mo cliff + 12mo linear |
| **Infrastructure & Validators** | 35% | 350,000,000 | 48 months gradual |
| **AI Compute Subsidies** | 10% | 100,000,000 | On-demand as agents onboard |
| **Protocol Treasury** | 15% | 150,000,000 | Multi-sig, 6mo timelock |
| **Ecosystem Grants** | 10% | 100,000,000 | Per-grant (6-24 months) |
| **Team & Advisors** | 10% | 100,000,000 | 12mo cliff + 36mo linear |

```
████████████████████░░░░░░░░░░░░░░░░░░░░  20%  ICO / Public Sale
█████████████████████████████████████░░░░  35%  Infrastructure
██████████░░░░░░░░░░░░░░░░░░░░░░░░░░░░░░  10%  AI Compute
████████████████░░░░░░░░░░░░░░░░░░░░░░░░  15%  Treasury
██████████░░░░░░░░░░░░░░░░░░░░░░░░░░░░░░  10%  Ecosystem
██████████░░░░░░░░░░░░░░░░░░░░░░░░░░░░░░  10%  Team
```

## Revenue Flywheel

All x402 protocol fees are distributed:

| Allocation | % | Mechanism |
|------------|---|-----------|
| **Buyback & Burn** | 40% | Market buy → permanent burn |
| **LP Provision** | 20% | Protocol-owned AMM liquidity |
| **Treasury** | 20% | Operational reserve |
| **Staking Rewards** | 15% | Distributed to UNY stakers |
| **Insurance Fund** | 5% | Incident recovery reserve |

## Existing Liquidity

UNY is currently listed on:

- **LFJ (TraderJoe)** — UNY/USDC pool on Avalanche C-Chain
- **LFJ (TraderJoe)** — UNY/WAVAX pool on Avalanche C-Chain

## ATP (Apostle Token Primitive)

ATP is the native token of the Apostle Chain (7332), used for agent-to-agent commerce:

| Property | Value |
|----------|-------|
| **Symbol** | ATP |
| **Decimals** | 18 |
| **Chain** | Apostle (7332) |
| **Current Supply** | ~2.45M ATP across 20 agents |
| **Purpose** | Agent-to-agent service payments |
| **Settlement** | Direct on-chain, < 50ms |
