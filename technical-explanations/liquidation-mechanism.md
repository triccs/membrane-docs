---
description: >-
  The protocol liquidates what collateral it can through the Liquidation Queue
  (LQ) then the Stability Pool (SP) before liquidating the remaining collateral
  on the market.
---

# Liquidation Mechanism

## &#x20;                        LQ -> SP -> Market Sale

\
In the case that the modules take too much collateral without fully repaying the liquidation, leaving the router without enough collateral to sell on the market to recoup debts, the protocol will skip the discounts and sell collateral for the full amount of debt from the start.

If either of the modules error or don't repay what was queried beforehand, the submessage reply will catch it and sell the collateral on the market to cover. If the LQ has leftovers it will try to use the SP to cover, but if that also errors it'll use the sell wall for both. \
\
The last message that gets executed is the [BadDebtCheck ](../smart-contracts/positions.md#baddebtcheck)CallbackMsg. The check is lazy in the sense that it doesn't look for undercollateralized positions, only uncollateralized.This is because the liquidation function market sells collateral once the position value is under the Stability Pool + caller + protocol fee threshold.\
\
On success, i.e. bad debt is true, the contract repays the position w/ protocol revenue and/or activates a debt auction through the [Auction ](../smart-contracts/mbrn-auction.md)contract. It being lazy allows it to be added without slowing down liquidation calls and if necessary, auctions can also be initiated by Governance.

#### Liquidation Function Walkthrough

* Fetch target\_position
* Assert Insolvency
* Check if the user has funds in the SP to repay and repay what's possible
* Calc + add message for per asset fees (caller + protocol)

Sift remaining collateral amount through LQ

* Check if LQ can liquidate the collateral amount and create the SubMsgs to do so
* Keep track of how much value is left in the position

Whatever credit isn't liquidated gets sent to the SP

* Query `sp_liq_fee`
* Check if SP has enough credit to liquidate
* Whatever it can't is sent to the Sell Wall
* Assign `RepayPropagation` fields
* Build SP sub\_msgs
* If `leftover_position_value` isn't enough to repay a potential remaining `credit_repayment_amount`, NULL every sub\_msgs added and Sell Wall everything
* Reassign `RepayPropagation` fields

#### Reply Function Walkthrough

_User Funds from SP:_

* On error, send repayment responsibilities to the SP if used
* If SP wasn't used, send to sell wall&#x20;



_Liquidation Queue:_

* Parse `repaid_amount` from the Response and subtract that from the total allocated to the queue
* Parse `collateral_amount` from the Response and send collateral amount liquidated to the Queue
* Update position claims for user, ie decrease how much they can withdraw\


_Stability Pool:_

* Query leftover credit to repay from `RepayPropagation`
* Send SP and LQ leftovers to the sell wall and add to existing Sell Wall distributions
* If there are none, send only LQ leftovers to the SP
