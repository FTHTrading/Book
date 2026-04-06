# Roadmap

## Phase Overview

| Phase | Name | Status |
|-------|------|--------|
| 1 | Foundation | ✅ Complete |
| 2 | Registry & Automation | ✅ Complete |
| 3 | Testing & Verification | 🔄 Current |
| 4 | Liquidity & Visibility | ⬜ Planned |
| 5 | Cross-chain & RWA | ⬜ Planned |

---

## Phase 1 — Foundation ✅

- UNY ERC-20 deployed on Avalanche C-Chain
- UNY/USDC and UNY/WAVAX LP pools on LFJ (TraderJoe)
- Master Stack monorepo (`packages/`, `registry/`, `scripts/`)
- Registry scaffolding: chains, tokens, pools, wallets, XRPL
- Hardhat project: compile, test, deploy pipeline (Solidity 0.8.24)
- Wallet UI MVP: Vite + React + TypeScript

## Phase 2 — Registry & Automation ✅

- 30 contracts in registry (GlacierMint, OptimaMint, DTT, OGB, OIB, SHO)
- TLD namespace registry: 78 TLDs with token_ids, IPFS CIDs
- IPFS CID index: 33 CIDs with provenance
- XRPL IOU catalog: 10 assets
- Pool pair addresses via Dexscreener API
- Build tools: `inventory.mjs`, `build-registry.mjs`, `build-proof-pack.mjs`
- Sync tools: `opensea-sync.mjs`, `genMeta.ts`, `updateURIs.ts`

## Phase 3 — Testing & Verification 🔄

Current focus:

- [ ] Live OpenSea v2 API testing for `opensea-sync.mjs`
- [ ] TLD metadata generation and verification
- [ ] Contract verification on Polygonscan + Routescan
- [ ] IPFS pinning of contract ABIs
- [x] **x402 Protocol — LIVE on Apostle Chain** (20 agents, 3,400+ blocks)
- [x] **Digital Twin deployed** (twin.unykorn.org)
- [x] **Full settlement pipeline** (Ed25519, fast-path, WebSocket)

## Phase 4 — Liquidity & Visibility

- Add LP depth to UNY/USDC and UNY/WAVAX pools
- List UNY on additional DEX aggregators
- Deploy wallet UI to production (IPFS / Vercel)
- Git tag `v0.1.0-avalanche-deploy`
- Publish x402 protocol documentation (this book)

## Phase 5 — Cross-chain & RWA

- Polygon deployment pipeline
- Bridge setup: Avalanche ↔ Polygon
- Solana integration (Wormhole or native)
- RWA proof documentation
- Governance / DAO tooling

---

## x402-Specific Milestones

| Milestone | Target | Status |
|-----------|--------|--------|
| Apostle Chain live | Q1 2026 | ✅ |
| 20-agent mesh bootstrapped | Q1 2026 | ✅ |
| Ed25519 signed transactions | Q2 2026 | ✅ |
| Digital twin visualization | Q2 2026 | ✅ |
| XRPL settlement bridge | Q2 2026 | Built |
| Stellar settlement bridge | Q2 2026 | Built |
| Multi-sig treasury | Q3 2026 | Planned |
| Public agent registration | Q3 2026 | Planned |
| Cross-chain relay production | Q4 2026 | Planned |
