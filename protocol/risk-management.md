# Risk Management

### Fiat Token Risk Mitigation

Fiat-backed tokens like USDC have 2 main existential risks: _**blacklisting**_ & _**bank insolvencies**_. Assuming fiat tokens will all be minted on some external chain in the Cosmos (ex: [Noble](https://twitter.com/noble\_xyz?s=20)), blacklisting will blacklist entire chains which allows any collateral to still be liquidated if holders are selling due to the blacklist. If there are no liquidations because liquidity is pulled faster than the sales are made, those tokens are frozen & the protocol will have to recapitalize the system with the [Operating Fund](risk-management.md#operating-fund) or a [**MBRN** auction](risk-management.md#mbrn-auction). This is why no matter what, total fiat exposure must be minimized in order to minimize potential insolvencies that the token holders are responsible for.

Bank insolvencies or fear thereof will be reflected in token price of the collateral. Due to Membrane's oracles which use [Pyth ](https://docs.pyth.network/pythnet-price-feeds/cosmwasm)to fetch a USD price for collateral, these fiat tokens will be liquidatible in the extent of an emergency scenario unlike collateral protocols that entrench them in a PSM or use static prices.

### Collateral Parameters

Each unique asset has a **debt cap** (CDT mint cap) and a **supply cap**. The **debt cap** is based off the proportional value of the given asset within Membrane's TVL. The **supply cap** is a TVL ratio for each asset set by governance. There is also the ability to add **multi-asset supply caps** to group risk profiles. This allows a customizable composition of collateral risk, minimizing the effects of bad debt or a malicious token attack to the caps assigned to the asset.\
\
Collateral deemed riskier will also have lower minimum collateral ratios (i.e. lower LTVs).\
\
Risk type -> Mitigated By:\
\- Systemic or Protocol Risk -> Supply/Debt Caps\
\- Volatility Risk -> Collateral Composition\
\- Liquidity Risk -> Stability Pool\
\
An amalgamation of them all will factor into the interest rate ranges decided by governance. In general, rates increase as caps approach their respective ratios to incentivize correction and vice versa.

### Oracles

The Position's contract price feed is updated at an interval set by [`oracle_time_limit`](../smart-contracts/positions.md#config). The [Oracle ](../smart-contracts/oracle.md)contract has the option to use [Pyth](https://docs.pyth.network/documentation/how-pyth-works) oracles (which use multiple providers) or Osmosis TWAPs. On Osmosis at 6s block times, every 6s is an extra block the attacker needs to manipulate price for, decreasing the cost but increasing external (MEV) risks for the manipulator. Starting at 60s, the minimum cost for an attacker, as they would need to hold an incongruent price for 10 blocks, is [**\~130 times the liquidity in the pool**](https://delphilabs.medium.com/which-one-should-you-use-arithmetic-or-geometric-mean-twap-ded01532bf49) to alter the price by -20% as well as being exposed to the aforementioned MEV in the form of price stabilization between similar pools. Holding a manipulated price for longer reduces initial cost but becomes unrealistic with the amount of MEV they become susceptible to. Manipulating for shorter time frames requires a near impossible cost (172 trillion times liquidity), so that is less of a worry.

On top of that, there is circuit breaker that prevents 20% movements within the window of oracle updates.

### Operating Fund

Optionally funded by protocol revenue, the operating fund is used to cover bad debt generated due to inefficient liquidations during harsh market conditions or blacklisted collateral. This fund isn't a separate contract, instead its the quantity of pending revenue earned by the system that is yet to be "redeemed".\
\
While not covering liabilities, it acts as a living safety net to give users extra insurance that the protocol will remain solvent and can be used for funding that doesn't require incentive alignment.

### MBRN Auction

If there is ever a shortfall event that results in more bad debt than pending revenue in the Safety Fund, **MBRN** will be auctioned off at a discount to recapitalize the system. This esstentially converts **MBRN's** market demand into a safety net but shouldn't be overused as we've seen with the hyperinflation of Terra's LUNA.

### State Assurance

After [Deposit](../smart-contracts/positions.md#deposit), [Withdraw](../smart-contracts/positions.md#withdraw), [Repay ](../smart-contracts/positions.md#repay)& [IncreaseDebt](../smart-contracts/positions.md#increasedebt), the contract state is checked to assert proper values and intentions were saved.
