---
description: How does CDT incentivize stability?
---

# Interest Rates

As the debt cap utilization **for an asset** increases, the **variable rate** rises linearly towards the soft  "max" rate for the collateral type. This is to incentivize vault owners using those assets to pay back debts as the protocol's risk in that asset increases. Rates will increase multiplicatively if the utilization continues past the soft max. Theoretically trending towards the market driven rate for the collateral type. **NOTE: Collateral assigned "rate hikes" will have higher rates that don't gradually increase but will still multiply once past the cap. These assets that tend to be yield bearing will also have redemptions force enabled at 99% of the peg.**

Below, the arrow represents the debt cap utilization for an asset & shows the current interest rate. As rates increase there is more pressure for vault owners to repay their loans, releasing pressure from liabilities in the system.

<figure><img src="../../.gitbook/assets/image (4).png" alt=""><figcaption></figcaption></figure>

## **Rate Influence 1: Price**&#x20;

The rates act as described above when the market price of **CDT** is within some margin of error in comparison to the redemption price (peg). Outside of said band, the rates will be influenced by price action to incentivize **CDT** stability. This means if above peg, rates will trend towards 0 & below peg, rates will increase in proportion to the distance from the peg.

The system operates optimally with a stable debt token which is why the system prioritizes stability incentives over short-term revenue.

## Rate Influence 2: Liquidity

The debt cap is correlated to **CDT** liquidity. This translates to increased rates as liquidity decreases and decreased rates as it increases.&#x20;

**Note: Because rates are percentages, the incentives they create will have more effect on the CDT market as well-capitalized users enter the system. This means as CDT total supply fluctuates, its augmented stability & volatility will increase and decrease in correlation.**

## Rate Influence 3: Collateral Composition

Collateral types are given maximum supply ratios which when broken, increases rates.

The collateral's ratio of outstanding debt is also used to influence rates based on the ratio of its debt cap fulfilled. Optimally we'd want to calculate the debt distribution bottoms up from each individual Position, but because the composition ratio will change as price moves, it's impossible to do efficiently (we'd have to calc each position's ratio's every time). So instead we use the Basket's collateral ratios to distribution debt & calculate rates. This method is feasible because collateral isn't accounted for unless there is open debt in the Position.&#x20;

## Rate Influence 4: Volatility&#x20;

Once added to the basket, the collateral's price volatility is saved in 48 instances which culminates to a minimum 8 hours of volatility data. This data is used to calculate an average volatility that is compared to the current volatility during asset usage. If the new vol. is above the average, the asset's supply cap is reduced by the % difference. Otherwise if the new vol. is lower, the supply cap is increased by the average.

This results in increased rates when volatility spikes over the short term & float back down as the real time volatility stays consistent with the average. This was added most importantly for black swan events like LUNA & CRV that see unexpected volatility in otherwise "strong" assets. Speed of rate change is key in these scenarios to incentivize borrowers to repay Membrane debt first before other markets who will inevitably increase their rates as well.
