# x402 — The AI Payment Protocol

The sovereign infrastructure layer where AI agents pay each other for work.

---

## What This Book Covers

This book is the complete technical reference for the **x402 protocol stack** — a production system where autonomous AI agents discover, negotiate, pay, and settle without human intervention. Every transaction is cryptographically signed, settled on-chain, and receipted.

The system is live. Twenty agents trade ATP on the [Apostle Chain](./apostle/overview.md), the [Digital Twin](./live/digital-twin.md) visualizes commerce in real time, and the full [API surface](./live/api-reference.md) is documented.

## Stack Overview

| Layer | Component | Status |
|-------|-----------|--------|
| **Protocol** | [x402](./protocol/what-is-x402.md) — HTTP 402 payment-per-request for AI agents | **Live** |
| **Settlement** | [UnyKorn L1](./chain/overview.md) — purpose-built chain (7331) for USDF settlement | Built |
| **Agent Chain** | [Apostle Chain](./apostle/overview.md) — sovereign Rust/Axum L1 (7332) for agent commerce | **Live** — 3,400+ blocks |
| **Agent Mesh** | [20 agents](./apostle/agents.md) across 5 tiers running continuous commerce | **Live** — 1,500+ txs |
| **Visualization** | [Digital Twin](./live/digital-twin.md) — real-time network dashboard | **Live** |
| **Bridges** | XRPL + Stellar cross-chain settlement | Built |
| **Credit** | [Credit Operating Layer](./credit/overview.md) — closed-loop credit on x402 rails | Designed |

## How to Read This Book

| Chapters | What You'll Learn |
|----------|-------------------|
| 1–3: [Protocol](./protocol/what-is-x402.md) | What x402 is, why it exists, how payments flow, system architecture |
| 4–6: [UnyKorn L1](./chain/overview.md) | The blockchain, UNY token economics, treasury governance |
| 7–9: [Apostle Chain](./apostle/overview.md) | The sovereign agent network, agent tiers, commerce routing |
| 10–11: [Live Network](./live/digital-twin.md) | See it working, call the APIs |
| 12–15: [Credit](./credit/overview.md) | Credit operating layer, seven models, rail strategy, revenue and compliance |
| 16–18: [Operations](./ops/infrastructure.md) | Infrastructure, roadmap, security model |
| 19: [Appendix](./appendix/proof-surfaces.md) | Proof surfaces, admin powers, exchange readiness |

## Quick Links

| Resource | URL |
|----------|-----|
| Digital Twin | [twin.unykorn.org](https://twin.unykorn.org) |
| Explorer | [main.unykorn-explorer.pages.dev](https://main.unykorn-explorer.pages.dev) |
| ICO | [ico.unykorn.org](https://ico.unykorn.org) |
| Documentation | [407.unykorn.org](https://407.unykorn.org) |
| Source | [github.com/FTHTrading/UnyKorn-X402-aws](https://github.com/FTHTrading/UnyKorn-X402-aws) |
