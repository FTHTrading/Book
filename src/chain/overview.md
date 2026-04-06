# UnyKorn L1 — Chain Overview

UnyKorn L1 is a purpose-built blockchain for settling AI agent payments. Every design decision — sub-second blocks, namespace resolution, prepaid credit channels — optimizes for high-frequency micropayment settlement.

---

## Chain Specifications

| Property | Value |
|----------|-------|
| **Chain Name** | UnyKorn L1 |
| **Chain ID** | 7331 |
| **Native Token** | UNY |
| **Consensus** | Custom PoA (PoS migration planned) |
| **Block Time** | ~1 second target |
| **Primary Asset** | USDF (stablecoin, 7 decimals) |
| **Status** | Built — controlled devnet |

## Why a Separate L1?

The [x402 protocol](../protocol/what-is-x402.md) requires sub-second settlement, HTTP-native payment channels, and hierarchical namespace resolution. General-purpose chains do not provide these natively. UnyKorn L1 exists specifically to be the canonical USDF ledger and receipt anchoring layer for the protocol stack.

## On-Chain Modules

| Module | Purpose |
|--------|---------|
| **Payment Channels** | Prepaid USDF channels for instant off-chain settlement |
| **Receipt Anchoring** | Merkle root storage for batched receipt verification |
| **Namespace Registry** | Hierarchical `x402.*` naming with rail and policy resolution |
| **Trade Finance** | Letter of credit, invoice factoring modules |
| **Compliance Engine** | KYC/AML policy enforcement at the chain level |

## Infrastructure Plan

The L1 is designed to run on a 5-node AWS fleet:

| Instance | Role | Type |
|----------|------|------|
| Alpha | Validator + API | t3.large |
| Bravo | Validator | t3.large |
| Charlie | Validator | t3.large |
| Delta | x402 host + Facilitator | t3.large |
| Echo | Oracle + monitoring | t3.medium |

Terraform modules for all 9 infrastructure components are code-complete.

## UnyKorn L1 vs Apostle Chain

| | UnyKorn L1 (7331) | Apostle Chain (7332) |
|-|-------------------|---------------------|
| **Purpose** | USDF stablecoin settlement | Sovereign agent commerce (ATP) |
| **Language** | TypeScript + Terraform | Rust / Axum |
| **Status** | Built (devnet) | **Live** (production) |
| **Agents** | Channel-based payment | Direct on-chain settlement |
| **Primary Asset** | USDF | ATP |

Both chains bridge via the x402 credit pool, enabling agents to pay in ATP while merchants settle in USDF. For the Apostle Chain in detail, see [Chain 7332 Overview](../apostle/overview.md). For token economics, see [UNY Tokenomics](./tokenomics.md).
