---
description: The debt token can be used to redeem collateral from Positions that opt-in
---

# Collateral Redemptions

Intrinsic demand is necessary to sustainably regain the peg when it goes below 100%, i.e. $1 for a dollar-pegged token. Membrane has **2** methods to provide said demand, [Liquidation Pools](liquidation-mechanism.md) & Collateral Redemptions. Both can induce demand at any time but Liquidation Pools require liquidations to complete the arbitrage, whereas redemptions can be used to close the loop immediately.

Redemptions are opt-in for Position owners to ensure incentives are aligned and chosen when redemptions do occur. Owners can **1) choose a premium for their collateral**, **2) choose a max % of their loan to repay for** & **3) restrict any collateral from being redeemed**. These ensure the owner is in control of when, what & how much their collateral is redeemed.

Premiums allow the owners to determine their captured % of arbitrage from the repayment. On the other hand, arbitragers will only redeem the debt token when the depeg is larger than the desired premium. This means that the frequency of redemption for a position owners is correlated to their premium.

**Ex:** If the debt token is 97% of its peg, arbitragers would only be able to redeem at a max premium of 2% to profit. There are cases where a 3% max premium would be profitable depending on the spread between market and TWAP prices but that is rare and highly technical.

