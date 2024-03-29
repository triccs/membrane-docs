---
description: MBRN is the liquidity backstop for the Membrane network
---

# MBRN Tokenomics

The total supply of **MBRN** will initially be 100 million **MBRN**, with 91% allocated to the community, and 9% vested to pre-launch contributors (2 year cliff, 1 linear). 10k of the remaining allocation to the community is vested for a year linear to 10 pre-launch community members.

### Governance (Autonomous Network Co-Creation)

The primary parameters set by communal governance are accepted collateral types and params. This allows the community to confer acknowledgement of additional risks corresponding to the collateral that are not visible on-chain, and therefore cannot be autonomously adjusted for.

Unstaking period is decided by governance. Rewards/fees are still earned while unstaking. The unstaking period is a mechanism used to align the token holders who are distributed revenue with the system's backstop mechanism, the[ debt auction](../smart-contracts/mbrn-auction.md#startauction). In the case of bad debt, stakers who earn assets from liquidations shouldn't be able to unstake and sell during a **MBRN** auction.

Stakers can **delegate their commission with or without voting power** to other community members who are more knowledgable or are worthy of financial support. There are no barriers to being a delegate. _**NOTE:**_ Your vote does NOT override your delegate's vote nor can you redelegate to effect an ongoing proposal

Regarding proposing authority, **any staker can propose** or align with a proposal. Once a proposal has at least 1k **MBRN** aligned with it, it'll become active and able to vote for. All Pending proposals have a day to garner alignment so it's better to round up potentials beforehand.

_**Note:** Vested allocations have reduced voting power, can submit proposals & receive revenue (not **MBRN** inflation)_

### Value Flows

Governance can toggle interest rate revenue flows to stakers, initially staked **MBRN** will receive all revenue. **MBRN**-denominated staking rewards are enabled on a schedule, for example 10% for 3 months. This makes sure the protocol isn't passively centralizing stake in stakers without a plan.

Governance has ownership over the protocol's liquidation fee, initially set at 1%. Governance can toggle a[ fee auction](../smart-contracts/mbrn-auction.md#startauction) to swap the fees if desired.

**MBRN** can additionally benefit from interest rate discounts, requirements explained in [System Discounts](system-discounts.md).
