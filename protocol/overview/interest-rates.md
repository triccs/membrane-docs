---
description: How does CDT incentivize stability?
---

# Interest Rates

As the debt cap utilization **for an asset** increases, the **variable rate** rises linearly towards the "max" rate for the collateral type. This is to incentivize vault owners using those assets to pay back debts as the protocol's risk in that asset increases.

Below the arrow represents the debt cap utilization for an asset & shows the current interest rate. As rates increase there is more pressure for vault owners to repay their loans, releasing pressure from liabilities in the system.

<figure><img src="../../.gitbook/assets/image (4).png" alt=""><figcaption></figcaption></figure>

## **Rate Influence 1: Price**&#x20;

The rates act as described above when the market price of **CDT** is within some margin of error in comparsion to the redemption price (peg). Outside of said band, the rates will be influenced by price action to incentivize **CDT** stability. This means if above peg, rates will trend towards 0 & below peg, rates will increase in proportion to the distance from the peg.

The system operates optimally with a stable debt token which is why the system prioritizes stability incentives over short-term revenue.

## Rate Influence 2: Liquidity

The debt cap is correlated to **CDT** liquidity. This translates to increased rates as liquidity decreases and decreased rates as it increases.&#x20;

**Note: Because rates are percentages, the incentives they create will have more effect on the CDT market as well-capitalized users enter the system. This means as CDT total supply fluctuates, its augmented stability & volatility will increase and decrease in correlation.**

## Rate Influence 3: Collateral Composition

Collateral types are given maximum supply ratios which when broken, increases rates.

The collateral's ratio of outstanding debt is also used to influence rates based on the ratio of its debt cap fulfilled. Optimally we'd want to calculate the debt distribution bottoms up from each individual Position, but because the composition ratio will change as price moves, it's impossible to do efficiently (we'd have to calc each position's ratio's every time). So instead we use the Basket's collateral ratios to distribution debt & calculate rates. This method is feasible because collateral isn't accounted for unless there is open debt in the Position.&#x20;

This creates a relationship where a collateral's price decreasing will increase its rates proportionally to the amount of Positions solely comprised of said collateral since its ratio won't decrease with the price, i.e. its debt total won't decrease with its price.
