---
description: >-
  A lockdrop splits a preset token amount between participants proportional to
  their deposits * time vested, i.e. you gain tokens by vesting for longer.
---

# Lockdrop (Launch)

For launch, Membrane has 10M tokens up for grabs in the lockdrop that will linearly vest based on the individually chosen vesting period, at a max of 365 days. At the end of the lockdrop, the deposited OSMO (minus _**operational fees**)_ will be matched with an additional 1M MBRN, paired into an LP & sent to the Governance contract controlled by stakers. _**Operational fees**_ are 201 OSMO used to create 1 **CDT** pools & 1 **MBRN** pool.&#x20;

After the creation of the 2 pools, the CDT/OSMO pool will receive incentives, 2M within the span of a year. The launch goal is to guarantee liquidity for **MBRN** so that **CDT** has a [backstop ](risk-management.md#mbrn-auction)from day 1, so participating in the lockdrop is akin to seeding the protocol's backstop.

**Note:** The protocol is deployed before launch starts, so users can experiment beforehand. The entire process is handled by a [smart contract](https://github.com/MembraneFinance/membrane-core/tree/main/contracts/launch).

**NOTE: MBRN** is vested linearly & staked when claimed
