---
description: Non-exhaustive list of composability options for Membrane contracts & CDT
---

# Potential Composability

<figure><img src="../.gitbook/assets/image (1).png" alt=""><figcaption></figcaption></figure>

### DEXs

**CDT** provides LPs with a stable pair asset and predictable minimums for ownership of total volume.&#x20;

A stable asset adds stableswap LPs which provide DEXs with substantial reoccuring volume as traders arbitrage differences in system design on top of the usual dose of DEX arb opportunities.

**CDT** is additionally valuable on chains with deployed minting contracts as it acts as a collateral aggregator for market makers, which benefits overall trade execution.

### Vaults

The [Positions](../smart-contracts/positions.md) contract can be used to mint **CDT** for , leveraged yield, liquidation bids and leveraged trading (loops made easy with the [Margin Proxy](../smart-contracts/margin-proxy.md)). It's also ripe for any type of management which includes LTV protections, interest rate arbitrage & **CDT** arbitrage. The [Discount Vault](../smart-contracts/discount-vault.md) is a lego for yield strategies using open loans as it provides accepted LPs with interest rate discounts that increase profit margins.

The liquidation pools, [Liquidation Queue](../smart-contracts/liquidation-queue.md) & [Stability Pool](../smart-contracts/stability-pool.md), can benefit from management that automatically handles rewards (sell, compound, stake) & acts as an option during liquidation periods for profit opportunities.&#x20;

**Notes:**

* For most strategies vaults only need to control the **CDT**.
* [Margin Proxy](../smart-contracts/margin-proxy.md) has looping functionalities for any leverage strategies.

### Collateral for Derivatives, Options, Lending & Perps

The best collateral are low-volatility & high volume. The system incentivizes low-volatility around the peg which attracts the market for stable assets to utilize **CDT.** From there, its collateral grade only improves over time as stable assets monopolize volume in DEXs.

{% embed url="https://www.coingecko.com/en/exchanges/uniswap" %}
In February 2023, the top 4 pools include stable assets
{% endembed %}
