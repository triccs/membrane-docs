---
description: Membrane interest rate discounts for network contributors
---

# System Discounts

Membrane only charges fees that are crucial for protocol functions, the 2 types being: **loan interest** & **liquidation fees**. Interest enables influence over demand while liquidation fees add a cost to unintended liquidation use cases. This leaves interest rates as the only fee that we can reasonably add discounts for (at the moment).

All of the discounts are based on the user's "value in network" divided by outstanding **CDT** loans, meaning 100 **CDT** worth of value is necessary to get an 100% discount on the interest for 100 **CDT**, 90.9% discount for 110 **CDT.** This enables user's contributing to the overall resilience of the network to take full advantage of any rate arbitrage opportunities.

This means CDT can be deposited into the SP for 0 cost, LPs can profitably provide liquidity at lower yields & stakers get guaranteed discounts for backstopping the supply.

**Note: All of these contracts accrue your positions before withdrawals**

<table><thead><tr><th>Discount for</th><th>Qualifier</th><th data-hidden></th></tr></thead><tbody><tr><td><strong>MBRN</strong></td><td><a href="../smart-contracts/staking.md">Staked</a> &#x26; based on market price. Floor price of 1 CDT. Includes pending rewards.</td><td></td></tr><tr><td><strong>CDT</strong></td><td>Deposit in <a href="../smart-contracts/stability-pool.md">Stability Pool</a> for 7 days. Includes pending incentives.</td><td></td></tr><tr><td><strong>LPs</strong></td><td>Deposit in Discount Vault for 7 days (no lock required) or in Osmosis gauges that are longer than 7 days.</td><td></td></tr></tbody></table>

