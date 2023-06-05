---
description: MBRN is the network token for Membrane
---

# MBRN Tokenomics

The total supply of MBRN will be 100 million MBRN, with 90% allocated to the community, and 10% vested to pre-launch contributors.

### Governance

The primary parameters set by communal governance are accepted collateral types and params. This allows the community to confer acknowledgement of additional risks corresponding to the collateral that are not visible on-chain, and therefore cannot be autonomously adjusted for.

Unstaking period is decided by governance. Rewards/fees are still earned while unstaking. The unstaking period is a mechanism used to align the token holders who are distributed revenue with the system's backstop mechanism, the[ debt auction](../smart-contracts/mbrn-auction.md#startauction). In the case of bad debt, stakers who earn assets from liquidations shouldn't be able to unstake and sell during a **MBRN** auction.

Vested allocations have no voting power but can submit proposals & receive revenue (not **MBRN** inflation).

### Value Flows

Governance can toggle interest rate revenue flows to stakers, initially staked **MBRN** will receive all revenue. MBRN-denominated staking rewards are enabled on a schedule, for example 10% for 3 months. This makes sure the DAO isn't passively centralizing stake in stakers without a plan.

**MBRN** has ownership over the protocol's liquidation fee, initially set at 1%. Governance can toggle a[ fee auction](../smart-contracts/mbrn-auction.md#startauction) to swap the fees if desired.

**MBRN** additionally benefits from interest rate discounts, requirements explained in [System Discounts](system-discounts.md).
