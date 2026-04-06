# Treasury Policy

This page documents the current treasury holdings, spending controls, permitted uses, and the planned migration to multi-sig governance.

---

## Holdings

| Wallet | Balance | Chain | Controller |
|--------|---------|-------|------------|
| Main Treasury | 400,000,000 UNY | UnyKorn L1 (7331) | Deployer key |
| LP Reserve | 100,000,000 UNY | UnyKorn L1 (7331) | Deployer key |
| **Total** | **500,000,000 UNY** | | 50% of supply |

## Spending Policy

### Current State (Single Key)

| Tier | Amount | Approval |
|------|--------|----------|
| < 1M UNY | Founder discretion | Single key |
| 1M – 10M UNY | Documented justification | Single key (logged) |
| > 10M UNY | Board/advisor consultation | Single key (risk accepted) |

> **Known risk**: Treasury is controlled by a single deployer key. See [Target State](#target-state-multi-sig--q3-2026) below.

### Target State (Multi-Sig — Q3 2026)

| Tier | Amount | Approval |
|------|--------|----------|
| < 100K UNY | Any signer | 1-of-5 |
| 100K – 1M UNY | Standard | 2-of-5 |
| 1M – 10M UNY | Full + timelock | 3-of-5 + 48-hour delay |
| > 10M UNY | Governance vote | 3-of-5 + 7-day lock |

## Permitted Uses

- Protocol development and engineering
- Ecosystem grants and partnerships
- Exchange listing fees and liquidity provision
- Market maker token loans
- Operational expenses (infrastructure, legal, compliance)
- Bug bounty rewards

## Prohibited Uses

- Personal use by team members
- Speculative trading
- Market manipulation
- Transfers to sanctioned jurisdictions

## Revenue Distribution

All x402 protocol revenue follows the [revenue flywheel](./tokenomics.md#revenue-flywheel):

| Allocation | % | Mechanism |
|------------|---|-----------|
| Buyback and Burn | 40% | Deflationary pressure |
| LP Provision | 20% | Protocol-owned liquidity |
| Treasury | 20% | Operational reserve |
| Staking Rewards | 15% | Staker incentives |
| Insurance Fund | 5% | Incident recovery |

## Transparency Commitments

- All treasury transactions will be publicly verifiable on the UnyKorn L1 Explorer
- Monthly treasury reports published
- All allocations above 1M UNY disclosed publicly
- Migration to Gnosis Safe multi-sig with 3-of-5 signing planned for Q3 2026

Treasury-held assets may also serve as collateral for [asset-backed credit lines](../credit/models.md#5-tokenised-asset-backed-credit) within the [Credit Operating Layer](../credit/overview.md).
