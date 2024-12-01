---
description: How does CDT incentivize stability?
---

# Interest Rates

Rates sit at their base rate until their individual supply caps are breached. Once breached, the rates begin to multiply for every percent over the cap to a max of 100%. This is to incentivize vault owners using those assets to pay back debts as the protocol's risk in that asset increases.  Theoretically trending towards the market driven rate for the collateral type.\
**NOTE: Collateral assigned "rate hikes" will have higher rates that will accrue additive rates when over cap instead of multiplicative. These assets, that tend to be yield bearing, will also have redemptions force enabled at 99% of the peg.**

Below, the arrow represents the debt cap utilization for an asset & a hypothetical interest rate. As rates increase there is more pressure for vault owners to repay their loans, releasing pressure from liabilities in the system.

<figure><img src="../../.gitbook/assets/image (4).png" alt=""><figcaption><p>Rates won't start at 0 unless there are being discounted due to an above peg depeg. <br>This is just to show the influence of rates on debt repayment</p></figcaption></figure>

## **Rate Influence 1: Price**&#x20;

The rates act as described above when the market price of **CDT** is within some margin of error in comparison to the redemption price (peg). Outside of said band, the rates will be influenced by price action to incentivize **CDT** stability. This means if above peg, rates will trend towards 0 & below peg, rates will increase in proportion to the distance from the peg.

The system operates optimally with a stable debt token which is why the system prioritizes stability incentives over short-term revenue.

## Rate Influence 2: Protocol Collateral Composition

Collateral types are given maximum supply ratios (supply caps) which when broken, increases rates. These caps are then dynamically shrunk using volaility trackers explained below.

## Rate Influence 3: Volatility&#x20;

Once added to the basket, the collateral's price volatility is saved in 48 instances which culminates to a minimum 8 hours of volatility data. This data is used to calculate an average volatility that is compared to the current volatility during asset usage. If the new vol. is above the average, the asset's supply cap is reduced by the % difference. Otherwise if the new vol. is lower, the supply cap is increased by the average.

This results in increased rates when volatility spikes over the short term & float back down as the real time volatility stays consistent with the average. This was added most importantly for black swan events like LUNA & CRV that see unexpected volatility in otherwise "strong" assets. Speed of rate change is key in these scenarios to incentivize borrowers to repay Membrane debt first before other markets who will inevitably increase their rates as well.
