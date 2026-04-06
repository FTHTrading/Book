# UnyKorn L1 — Chain Overview

## Chain Specifications

| Property | Value |
|----------|-------|
| **Chain Name** | UnyKorn L1 |
| **Chain ID** | 7331 |
| **Native Token** | UNY |
| **Consensus** | Custom PoA → PoS migration planned |
| **Block Time** | ~1 second target |
| **Primary Asset** | USDF (stablecoin, 7 decimals) |
| **Status** | **BUILT** — controlled devnet |

## Purpose

UnyKorn L1 is a **purpose-built blockchain** designed for one thing: settling AI agent payments. Unlike general-purpose chains, every design decision optimizes for:

1. **Sub-second settlement** of high-frequency micropayments
2. **HTTP-native payment flows** embedded in API request/response cycles
3. **Agent identity** and hierarchical namespace resolution
4. **Prepaid credit systems** with Ed25519-signed proofs for zero-latency payment
5. **Receipt batching** with Merkle tree anchoring for auditability at scale

## On-Chain Modules

| Module | Purpose |
|--------|---------|
| **Payment Channels** | Prepaid USDF channels for instant off-chain settlement |
| **Receipt Anchoring** | Merkle root storage for batched receipt verification |
| **Namespace Registry** | Hierarchical `x402.*` naming with rail and policy resolution |
| **Trade Finance** | Letter of credit, invoice factoring modules |
| **Compliance Engine** | KYC/AML policy enforcement at the chain level |

## Infrastructure

The L1 is designed to run on a 5-node AWS fleet:

| Instance | Role | Type |
|----------|------|------|
| Alpha | Validator + API | t3.large |
| Bravo | Validator | t3.large |
| Charlie | Validator | t3.large |
| Delta | x402 host + Facilitator | t3.large |
| Echo | Oracle + monitoring | t3.medium |

Terraform modules for all 9 infrastructure components are code-complete.

## Relationship to Apostle Chain

| | UnyKorn L1 (7331) | Apostle Chain (7332) |
|-|-------------------|---------------------|
| **Purpose** | USDF stablecoin settlement | Sovereign agent commerce (ATP) |
| **Language** | TypeScript + Terraform | Rust/Axum |
| **Status** | BUILT (devnet) | LIVE (production) |
| **Agents** | Channel-based payment | Direct on-chain settlement |
| **Primary Asset** | USDF | ATP |

Both chains will bridge via the x402 credit pool, enabling agents to pay in ATP while merchants settle in USDF.
