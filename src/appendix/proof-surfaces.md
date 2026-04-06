# Appendix: Proof Surfaces

Proof surfaces are publicly accessible endpoints and verification points for the UnyKorn x402 system. They allow anyone to independently verify claims made in this documentation.

---

## Public Endpoints (LIVE)

| Surface | URL | What It Proves |
|---------|-----|----------------|
| **Digital Twin** | [twin.unykorn.org](https://twin.unykorn.org) | Real-time agent mesh visualization, live transactions |
| **Explorer** | [main.unykorn-explorer.pages.dev](https://main.unykorn-explorer.pages.dev) | Dashboard, blocks, transactions, agents, namespaces |
| **ICO Site** | [ico.unykorn.org](https://ico.unykorn.org) | Token sale, tiers, tokenomics, roadmap |
| **Gateway** | `x402-gateway-staging.kevanbtc.workers.dev` | x402 route enforcement, 402 responses |
| **GitHub** | [github.com/FTHTrading/UnyKorn-X402-aws](https://github.com/FTHTrading/UnyKorn-X402-aws) | All source code, commit history |
| **Whitepaper** | [docs/WHITEPAPER.md](https://github.com/FTHTrading/UnyKorn-X402-aws/blob/main/docs/WHITEPAPER.md) | Full protocol specification |
| **Tokenomics** | [docs/TOKENOMICS.md](https://github.com/FTHTrading/UnyKorn-X402-aws/blob/main/docs/TOKENOMICS.md) | Economic model |
| **Docs Site** | [407.unykorn.org](https://407.unykorn.org) | Documentation hub |

## Discovery Endpoints (LIVE)

| Endpoint | Data |
|----------|------|
| `/.well-known/agent.json` | A2A Agent Card |
| `/.well-known/x402-pay` | x402 Payment Descriptor |
| `/.well-known/ai-plugin.json` | OpenAI/ChatGPT Plugin Manifest |
| `/.well-known/openapi.json` | OpenAPI 3.1 Specification |
| `/health` | Service health check |

## Apostle Chain Endpoints (LIVE on EC2)

| Endpoint | What It Proves |
|----------|----------------|
| `GET /health` | Chain is running |
| `GET /status` | Block height, agent count, mempool |
| `GET /v1/agent/{id}/balance` | Agent balances are real |
| `GET /v1/receipts` | Settled transactions on record |

## Not Yet Publicly Verifiable

| Item | Status | Path to Verification |
|------|--------|---------------------|
| UnyKorn L1 block production | No public RPC | Deploy public RPC endpoint |
| Transaction finality metrics | No mainnet | Mainnet launch |
| Treasury wallet on-chain balance | No mainnet | Multi-sig deployment |
| Vesting contract enforcement | Not deployed | Smart contract deployment |
| Third-party audit report | Not completed | Engage auditor |
| Uptime metrics / SLA dashboard | Not built | Build status page |

## Admin Powers Disclosure

The UNY token contract is **immutable** (no proxy pattern):

| Power | Exists? |
|-------|---------|
| Mint new tokens | **No** — no mint function |
| Burn tokens | **Yes** (holder-only) — ERC20Burnable |
| Pause transfers | **No** — no pause function |
| Freeze accounts | **No** — no freeze/blacklist |
| Upgrade contract | **No** — immutable, no proxy |
| Admin withdrawal | **No** — no admin withdrawal function |

> **Known risk**: Treasury allocation is controlled by a single deployer key. Migration to 3-of-5 multi-sig with timelock is planned for Q3 2026.

## Exchange Readiness Summary

| Area | Score | Status |
|------|-------|--------|
| Legal & Issuer | 40% | Missing: formal counsel memo, jurisdiction analysis |
| Token Documentation | 80% | Whitepaper + Tokenomics complete |
| Technical Integration | 60% | Code complete, missing: public RPC, testnet |
| Security | 50% | OpenZeppelin, immutable, missing: audit |
| Market Structure | 20% | No market maker yet |
| Operations & Support | 40% | Guardian daemons, missing: status page |
| Exchange Docs | 70% | Compliance engine built, per-exchange packets pending |
