---
description: >-
  Membrane's liquidation mechanism has 2 layers designed to have reduced effects
  on a collateral's market price while improving distribution of & democratizing
  access to the liquidated assets
---

# Liquidation Mechanism

The 2 layers of the liquidation mechanism: **Liquidation Queue (LQ) aka Single-Asset Pool &** **Stability Pool (SP) aka Omni-Asset Pool**

The **LQ** allows users to bid on specific collateral assets at a range of premium rates.&#x20;

The **SP** acts as a backstop for the entire vault system, its funds used to liquidate for any collateral at a set premium. Since the **SP** gets 2ndary priority for liquidations, if the fee is too high the system's liquidation efficiency will decrease but it must be high enough to attract deposits.

If bad debt is accrued, pending revenue and/or [MBRN auctions](../smart-contracts/mbrn-auction.md) will cover it, similar to [MakerDAO's Debt Auctions](https://docs.makerdao.com/keepers/the-auctions-of-the-maker-protocol).

**Framing:** Think of the mechanism as a[ layered sifter.](https://twitter.com/lite\_trix/status/1623347765035016193?s=20\&t=VZpse4CI1YzxFn78PYgnMQ) Each level reduces the liquidation amount for the next until all CDT is covered.\
\
**Note: It is assumed that sophisticated liquidators will source liquidity from outside of Osmosis long term but the foundational risk of our minting mechanism lies with Osmosis liquidity in a situation where all other DEX chains halt or can't fulfill trades.**

### Liquidator Fees

Smart contracts aren't autonomous so they need to be called by an external source. These calls will be incentivized by a liquidation fee priced by free market mechanics, the available fee growing in proportion to the vault's insolvency.

_Ex: If a position's liquidation point is 80% LTV and the position gets to 81%, the caller's fee would be 1% of the liquidated collateral._

The fee will keep increasing until a liquidator deems its profitable/desirable to liquidate, but if one waits too long they may lose the chance to capture the fee. This mechanism finds the lowest viable liquidation fee which benefits the user and the overall market.  Because the liquidator is only receiving the fee, there is a lower barrier for activation compared to systems where they need to liquidate the fee & the collateral.&#x20;

**Note: There is a configurable 1% fee controlled by governance to dissuade self-liquidations or stop-loss usage**\


Additional Resources:&#x20;

1\)[ ](https://docs.euler.finance/developers/architecture#front-running-protection)[https://docs.euler.finance/developers/architecture#front-running-protection](https://docs.euler.finance/developers/architecture#front-running-protection)\
2\) [https://twitter.com/euler\_mab/status/1537091423748517889](https://twitter.com/euler\_mab/status/1537091423748517889)

### System Benefits

Having pools of **CDT** on standby to liquidate increases the efficiency of the market's demand in smoothing our liquidation process. This provides the mechanism a larger buffer for harsh liquidation periods and reduces the protocol's direct effect on external markets. On top of that, the wallet distribution of the **LQ** allows a more diverse user base to obtain assets, increasing the % sent to hodlers (i.e. % not sold during liquidation).

If the liquidation pools are ever empty, they can also be used to facilitate flash loan based liquidations.

**Note: Any user funds in the Stability Pool will be used to repay said user's vaults if liquidated. This doesn't protect the user from the external liquidator & protocol fees.**

Witholding revenue could be a very powerful strategic decision. Fee [auctions ](../smart-contracts/mbrn-auction.md)allow the DAO to convert all liquidation fees into a single desired asset. Combine that with **CDT**-denominated revenue from interest rates and the protocol has both sides of any **CDT LP** they need. One of the most efficient ways to use this is to build **P**rotocol **O**wned **L**iquidity in order to eliminate the need for liquidity incentives long term, reducing the business cost of **CDT** to 0.

Additional Resources:&#x20;

1\) [https://docs.makerdao.com/keepers/the-auctions-of-the-maker-protocol](https://docs.makerdao.com/keepers/the-auctions-of-the-maker-protocol)\
2\) [https://docs.liquity.org/faq/stability-pool-and-liquidations](https://docs.liquity.org/faq/stability-pool-and-liquidations)\
3\) [https://docs.anchorprotocol.com/protocol/loan-liquidation](https://docs.anchorprotocol.com/protocol/loan-liquidation)

