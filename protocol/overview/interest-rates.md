---
description: How does CDT stabilize?
---

# Interest Rates

As the debt cap utilization **for an asset** increases, the **variable rate** rises linearly towards the "max" rate for the collateral type. This is to incentivize vault owners using those assets to pay back debts as the protocol's risk in that asset increases.

Below the arrow represents the debt cap utilization for an asset & shows the current interest rate. As rates increase there is more pressure for vault owners to repay their loans, releaseingpressure from the system.

<figure><img src="../../.gitbook/assets/image (4).png" alt=""><figcaption></figcaption></figure>

## **Rate Influence 1: Price**&#x20;

The rates act as described above when market price of **CDT** is within some margin of error in comparsion to the redemption price (peg). Outside, the rates will react to the price to incentivize **CDT** stability. This means if above peg, rates will trend towards 0 & below peg, rates will increase in proportion to the distance from the peg.

The system operates optimally with a stable debt token which is why the system prioritizes stability incentives over revenue.

## Rate Influence 2: Liquidity

The debt cap is correlated to **CDT** liquidity. This translates to increased rates as liquidity decreases and decreased rates as it increases.&#x20;

**Note: Because rates are percentages, the incentives they create will have more effect on the CDT market as well-capitalized users enter the system. This means as CDT total supply fluctuates, its augmented stability & volatility will increase and decrease in correlation.**

## Rate Influence 3: Collateral Composition

Collateral types are given maximum supply ratios which when broken, increase rates.
