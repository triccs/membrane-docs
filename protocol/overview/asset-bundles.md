# Asset Bundles

Membrane's vault system allows anyone to "bundle" accepted assets in a single position. Bundling assets treats their value as a single unit, proportional to the respective value and parameters of each underlying asset. This enables vaults to hedge liquidation risk via bundling with less volatile assets, or uncorrelated assets.

**Ex**: Bundling OSMO & USDC to reduce the vault's volatility

<figure><img src="../../.gitbook/assets/image (6).png" alt=""><figcaption><p>Unbumdled vs. Bundled </p></figcaption></figure>

Interest rates, LTV and supply/debt cap distribution are all affected by the bundling process, but for simplicity we'll use an example with only LTV.\
\
**Ex:** A position with 2 assets, each 50% of the position with 30% - 40% and 40% - 53% max LTV ranges respectively will result in position parameters of 35% max borrow LTV and 46.5% max LTV.\
\
**Note:** _All positions are liquidated proportionally. Users can open multiple positions so if a proportional liquidation is undesired, the deposits should be in separate positions._

_**Note:** The borrowable LTV increases inversely proportional to the collateral's supply ratio in the Basket. Meaning the less debt collateralized by an asset, the more that can be borrowed at once._\
