---
description: >-
  Membrane's liquidation mechanism has 3 layers designed to have reduced effects
  on a collateral's market price while improving distribution of & democratizing
  access to the liquidated assets
---

# Liquidation Mechanism

The 3 layers of the liquidation mechanism: **Liquidation Queue (LQ)**, **Stability Pool (SP)** and **open market sales**

The **LQ** allows users to bid on specific collateral assets at a range of premium rates.&#x20;

The **SP** acts as a backstop for the entire vault system, its funds used to liquidate for any collateral at a set premium.

As a final measure, any collateral positions that can't get liquidated by the first 2 steps will be sold on the market to avoid the protocol accruing bad debt. In the case it does, pending revenue and/or [MBRN auctions](../smart-contracts/mbrn-auction.md) will cover it, similar to [MakerDAO's Debt Auctions](https://docs.makerdao.com/keepers/the-auctions-of-the-maker-protocol).

**Framing:** Think of the mechanism as a[ layered sifter.](https://twitter.com/lite\_trix/status/1623347765035016193?s=20\&t=VZpse4CI1YzxFn78PYgnMQ) Each level reduces the liquidation amount for the next until all CDT is covered.\
\
**Note: In the case of errors repaying from the liquidation contracts, the error will trigger the collateral to go through the DEX router to ensure all liquidations can be executed by 1 external call of the initial liquidation function.**

### Liquidator Fees

Smart contracts aren't autonomous so they need to be called by an external source. These calls will be incentivized by a liquidation fee determined by free market mechanics. The available fee grows in correlation to the vault's insolvency

_Ex: If a position's liquidation point is 80% LTV and the position gets to 81%, the caller's fee would be 1% of the liquidated collateral._

The fee will keep increasing until a liquidator deems its profitable/desirable to liquidate, but if one waits too long it may lose the chance to capture the fee. This mechanism finds the lowest viable liquidation fee which benefits the user and the overall market.&#x20;

**Note: There is a configurable 1% fee that goes to MBRN stakers**\


Additional Sources:&#x20;

1\)[ ](https://docs.euler.finance/developers/architecture#front-running-protection)[https://docs.euler.finance/developers/architecture#front-running-protection](https://docs.euler.finance/developers/architecture#front-running-protection)\
2\) [https://twitter.com/euler\_mab/status/1537091423748517889](https://twitter.com/euler\_mab/status/1537091423748517889)

### System Benefits

Having pools of **CDT** on standby to liquidate increases the efficiency of the market's demand in smoothing our liquidation process. This provides the mechanism a larger buffer for harsh liquidation periods and reduces the protocol's direct effect on external markets. The wallet distribution of the **LQ** allows a more diverse user base to obtain assets, increasing the % sent to hodlers.

If the liquidation pools are ever empty, they can also be used to facilitate flash loan based liquidations.

**Note: Any user funds in the Stability Pool will be used to repay said user's vaults if liquidated. This doesn't protect the user from the external liquidator & protocol fees.**

The liquidation fee is collected and distributed to an [Auction](../smart-contracts/mbrn-auction.md#startauction) starting an hour later. In the meantime any bad debt accrued will get auctioned off in return for MBRN, inflating supply. Scheduling the fee auctions after the debt auctions induces potential buy pressure after potential sell pressure, i.e. a mint followed by a burn to counteract.

Additional Sources:&#x20;

1\) [https://docs.makerdao.com/keepers/the-auctions-of-the-maker-protocol](https://docs.makerdao.com/keepers/the-auctions-of-the-maker-protocol)\
2\) [https://docs.liquity.org/faq/stability-pool-and-liquidations](https://docs.liquity.org/faq/stability-pool-and-liquidations)\
3\) [https://docs.anchorprotocol.com/protocol/loan-liquidation](https://docs.anchorprotocol.com/protocol/loan-liquidation)

