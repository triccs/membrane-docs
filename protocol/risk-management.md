# Risk Management

### Collateral Parameters

Each unique asset has a **debt cap** (CDT mint cap) and a **supply cap**. The **debt cap** is based off the proportional value of the given asset within Membrane's TVL. The **supply cap** is a TVL ratio for each asset set by governance. There is also the ability to add **multi-asset supply caps** to group risks. This allows a customizable composition of collateral risk, minimizing the effects of bad debt or a malicious token attack to the caps assigned to the asset.\
\
Collateral deemed riskier will also have lower minimum collateral ratios (i.e. lower LTVs).\
\
Risk type -> Solved By:\
\- Systemic or Protocol Risk -> Supply/Debt Caps\
\- Volatility Risk -> Collateral Composition\
\- Liquidity Risk -> Stability Pool\
\
An amalgamation of them all will factor into the interest rate ranges decided by governance. In general, rates increase when caps are near their respective set ratios to incentivize correction and vice versa.

### Operating Fund

Optionally funded by protocol revenue, the operating fund is used to cover bad debt generated due to inefficient liquidations during harsh market conditions. This fund isn't a separate contract, instead its the quantity of pending revenue earned by the system that is yet to be "redeemed".\
\
While not covering liabilities, it acts as a living safety net to give user extra insurance that the protocol will remain solvent and can be used for funding that doesn't require incentive alignment.

### MBRN Auction

If there is ever a shortfall event that results in more bad debt than pending revenue in the Safety Fund, **MBRN** will be auctioned off at a discount to recapitalize the system.

### Oracles

The Position's contract price feed is updated at an interval set by [`oracle_time_limit`](../smart-contracts/positions.md#config). On Osmosis at 6s block times, every 6s is an extra block the attacker needs to manipulate price for, decreasing the cost but increasing external risks of the manipulation. Starting at 60s, an attacker would need to hold an incongruent price for 10 blocks which equates to a cost of [**\~130 times the liquidity in the pool**](https://delphilabs.medium.com/which-one-should-you-use-arithmetic-or-geometric-mean-twap-ded01532bf49) to alter the price by 20% upwards as well as being exposed to MEV type risks.

### State Assurance

After [Deposit](../smart-contracts/positions.md#deposit), [Withdraw](../smart-contracts/positions.md#withdraw), [Repay ](../smart-contracts/positions.md#repay)& [IncreaseDebt](../smart-contracts/positions.md#increasedebt), the state is checked to assert proper values and intentions were saved.
