# Positions

The Positions contract implements the logic for Collateralized Debt Positions (CDPs), through which users can receive debt tokens against their deposited collateral.

The contract also contains the logic for initiating liquidations of CDPs and sending assets to the DEX router but external debt repayment logic goes through the **Queue** and **Stability Pool** contracts.

**Notes:**&#x20;

* [_**Deposits**_](positions.md#deposit) _take asset data directly from the message to ensure correctness._&#x20;
* [_**Withdrawals**_ ](positions.md#withdraw)_are checked for validity (in the SubMsg reply) before state is permanently changed._&#x20;
* [_**Supply caps**_](positions.md#editbasket) _don't affect withdrawals, in otherwords, they only restrict deposits. Can be set to 0 to set the collateral's debt\_cap to 0 which locks mints and spikes interest rates._
* _Adding collateral assets adds a queue for them in Liq Queue contract and a price feed for them in the Oracle contract_
* _LP share supply caps are based on its ratio without double counting its assets, though its debt is counted towards its pool assets' caps._
* _Each credit\_asset has a contract level liquidity multiplier calculated as the sum of all multipliers of basket's with the same credit\_asset_&#x20;
* _Negative rates are turned off when total basket debt is above the desired debt utilization_

## InstantiateMsg

```
#[cw_serde]
pub struct InstantiateMsg {
    pub owner: Option<String>,
    pub oracle_time_limit: u64, 
    pub debt_minimum: Uint128,  
    pub liq_fee: Decimal,
    pub collateral_twap_timeframe: u64, 
    pub credit_twap_timeframe: u64,     
    //Contracts
    pub stability_pool: Option<String>,
    pub dex_router: Option<String>,
    pub staking_contract: Option<String>,
    pub oracle_contract: Option<String>,
    pub osmosis_proxy: Option<String>,
    pub debt_auction: Option<String>,
    pub liquidity_contract: Option<String>,
    pub discounts_contract: Option<String>,
}
```

<table><thead><tr><th width="267.4108280254777">Key</th><th width="206">Type</th><th>Description</th></tr></thead><tbody><tr><td><code>*owner</code></td><td>String</td><td>Contract owner that defaults to info.sender</td></tr><tr><td><code>oracle_time_limit</code></td><td>u64</td><td>Limit in seconds that the oracle has before the values are invalid</td></tr><tr><td><code>debt_minimum</code></td><td>Uint128</td><td>Minimum value in debt per position</td></tr><tr><td><code>liq_fee</code></td><td>Decimal</td><td>Fee that goes to the protocol during liquidations</td></tr><tr><td><code>collateral_twap_timeframe</code></td><td>u64</td><td>TWAP length in minutes</td></tr><tr><td><code>credit_twap_timeframe</code></td><td>u64</td><td>TWAP length in minutes</td></tr><tr><td><code>rate_slope_multiplier</code></td><td>Decimal</td><td>Interest rate slope multiplier</td></tr><tr><td><code>base_debt_cap_multiplier</code></td><td>Uint128</td><td>Base debt cap multiplier</td></tr><tr><td><code>*stability_pool</code></td><td>String</td><td>Stability Pool Contract</td></tr><tr><td><code>*dex_router</code></td><td>String</td><td>DEX Router Contract</td></tr><tr><td><code>*staking_contract</code></td><td>String</td><td>MBRN staking contract</td></tr><tr><td><code>*oracle_contract</code></td><td>String</td><td>Oracle contract</td></tr><tr><td><code>*osmosis_proxy</code></td><td>String</td><td>Osmosis Proxy contract to use SDK modules</td></tr><tr><td><code>*debt_auction</code></td><td>String</td><td>Auction Contract that sells protocol tokens to repay debt</td></tr><tr><td><code>*liquidity_contract</code></td><td>String</td><td>Liquidity Check contract address</td></tr><tr><td><code>*discounts_contract</code></td><td>String</td><td>System Discounts contract address</td></tr></tbody></table>

\* = optional

## ExecuteMsg

### `UpdateConfig`

Update Config by the current config.owner

```
#[cw_serde]
pub enum ExecuteMsg {
    UpdateConfig(UpdateConfig),
}

pub struct UpdateConfig {
    pub owner: Option<String>,
    pub stability_pool: Option<String>,
    pub dex_router: Option<String>,
    pub osmosis_proxy: Option<String>,
    pub debt_auction: Option<String>,
    pub staking_contract: Option<String>,
    pub oracle_contract: Option<String>,
    pub liquidity_contract: Option<String>,
    pub discounts_contract: Option<String>,
    pub liq_fee: Option<Decimal>,
    pub debt_minimum: Option<Uint128>,
    pub base_debt_cap_multiplier: Option<Uint128>,
    pub oracle_time_limit: Option<u64>,
    pub credit_twap_timeframe: Option<u64>,
    pub collateral_twap_timeframe: Option<u64>,
    pub cpc_multiplier: Option<Decimal>,
    pub rate_slope_multiplier: Option<Decimal>,
}
```

<table><thead><tr><th width="307">Key</th><th width="113">Type</th><th>Description</th></tr></thead><tbody><tr><td><code>*owner</code></td><td>String</td><td>Owner of contract</td></tr><tr><td><code>*stability_pool</code></td><td>String</td><td>Stability Pool contract</td></tr><tr><td><code>*dex_router</code></td><td>String</td><td>Dex Router contract</td></tr><tr><td><code>*osmosis_proxy</code></td><td>String</td><td>Osmosis Proxy contract</td></tr><tr><td><code>*debt_auction</code></td><td>String</td><td>Debt Auction contract</td></tr><tr><td><code>*staking_contract</code></td><td>String</td><td>MBRN Staking contract</td></tr><tr><td><code>*oracle_contract</code></td><td>String</td><td>Oracle contract</td></tr><tr><td><code>*liquidity_contract</code></td><td>String</td><td>Liquidity Check contract </td></tr><tr><td><code>*discounts_contract</code></td><td>String</td><td>System Discounts contract</td></tr><tr><td><em><code>*liq_fee</code></em></td><td>Decimal</td><td>Liquidation fee</td></tr><tr><td><code>*debt_minimum</code></td><td>Uint128</td><td>Debt minimum in terms of value</td></tr><tr><td><code>*base_debt_cap_multiplier</code></td><td>Uint128</td><td>Debt Minimum Multiplier for a base debt cap</td></tr><tr><td><code>*oracle_time_limit</code></td><td>u64</td><td>Oracle expiration time limit in seconds</td></tr><tr><td><code>*collateral_twap_timeframe</code></td><td>u64</td><td>TWAP length in minutes</td></tr><tr><td><code>*credit_twap_timeframe</code></td><td>u64</td><td>TWAP length in minutes</td></tr><tr><td><code>*cpc_multiplier</code></td><td>Decimal</td><td>Multiplier for credit price difference to augment redemption price rate</td></tr><tr><td><code>*rate_slope_multiplier</code></td><td>Decimal</td><td>Multiplier for the 2nd slope in the interest rate formula</td></tr></tbody></table>

&#x20;\* = optional

### `Deposit`

Deposits basket accepted collateral to a new or existing position.

```
#[cw_serde]
pub enum ExecuteMsg {
    Deposit {
        position_id: Option<Uint128>,
        position_owner: Option<String>,
    },
}
```

<table><thead><tr><th width="212">Key</th><th>Type</th><th>Description</th></tr></thead><tbody><tr><td>*<code>position_id</code></td><td>Uint128</td><td>Position ID to deposit to. If none is passed, a new position is created.</td></tr><tr><td>*<code>position_owner</code></td><td>String</td><td>Owner of the position, defaults to info.sender</td></tr></tbody></table>

\* = optional

### `IncreaseDebt`

Increase debt of a position. Only callable by the position owner and limited by the position's max borrow LTV.

```
#[cw_serde]
pub enum ExecuteMsg {
    IncreaseDebt { 
        position_id: Uint128,
        amount: Uint128,
        LTV: Option<Decimal>,
        mint_to_addr: Option<String>,
    }, 
}
```

| Key             | Type    | Description                |
| --------------- | ------- | -------------------------- |
| `position_id`   | Uint128 | ID of position             |
| `amount`        | Uint128 | Amount to increase debt by |
| `*LTV`          | Decimal | Increase debt to an LTV    |
| `*mint_to_addr` | String  | Address to mint credit to  |

&#x20;\* = optional

### `Withdraw`

Withdraw assets from the caller's position as long as it leaves the position solvent in relation to the max borrow LTV

```
#[cw_serde]
pub enum ExecuteMsg {
    Withdraw {
        position_id: Uint128,
        assets: Vec<Asset>,
        send_to: Option<String>,
    },
}
```

| Key           | Type        | Description                                             |
| ------------- | ----------- | ------------------------------------------------------- |
| `position_id` | Uint128     | ID of position                                          |
| `assets`      | Vec\<Asset> | Assets to withdraw from the position                    |
| `*send_to`    | String      | Address to send withdrawn assets to, defaults to sender |

### `Repay`

Repay outstanding debt for a position, not exclusive to the position owner.

```
#[cw_serde]
pub enum ExecuteMsg {
    Repay {
        position_id: Uint128,
        position_owner: Option<String>, 
        send_excess_to: Option<String>,
    },
}
```

<table><thead><tr><th width="227">Key</th><th>Type</th><th>Description</th></tr></thead><tbody><tr><td><code>position_id</code></td><td>Uint128</td><td>ID of Position</td></tr><tr><td><code>*position_owner</code></td><td>String</td><td>Owner of Position to repay </td></tr><tr><td><code>*send_excess_to</code></td><td>String</td><td>Address to send excess repayment assets to</td></tr></tbody></table>

\* = optional

### `Accrue`

Accrue interest for a Position. Allows external control of rate discounts, i.e. accrue before withdrawing capital that makes the user eligible for discounts.

```
#[cw_serde]
pub enum ExecuteMsg {
    Accrue {     
        position_owner: Option<String>, 
        position_ids: Vec<Uint128>, 
    }
}
```

| Key               | Type          | Description          |
| ----------------- | ------------- | -------------------- |
| `*position_owner` | String        | Position owner       |
| `position_ids`    | Vec\<Uint128> | List of Position IDs |

&#x20;\* = optional&#x20;

### `ClosePosition`

Close position by selling collateral to repay debt

```
#[cw_serde]
pub enum ExecuteMsg {
    ClosePosition {
        position_id: Uint128,
        max_spread: Decimal,
        send_to: Option<String>,
    }
}
```

| Key           | Type    | Description                                                  |
| ------------- | ------- | ------------------------------------------------------------ |
| `position_id` | Uint128 | Position ID                                                  |
| `max_spread`  | Decimal | Spread used to ensure collateral sold repays the debt amount |
| `*send_to`    | String  | Send withdrawn assets to from a closed position              |

&#x20;\* = optional

### `LiqRepay`

Repay function for [Stability Pool](stability-pool.md) liquidations. Used to repay insolvent positions and distribute liquidated funds.

```
#[derive(Serialize, Deserialize, Clone, Debug, PartialEq, JsonSchema)]
#[serde(rename_all = "snake_case")]
pub enum ExecuteMsg {
    LiqRepay { }
}
```

### `Liquidate`

Assert's the position is insolvent and calculates the distribution of repayment to the various liquidation modules. Does a bad debt check at the end of the procedure that starts a[ MBRN auction](mbrn-auction.md) if necessary.

```
#[cw_serde]
pub enum ExecuteMsg {
    Liquidate {
        position_id: Uint128,
        position_owner: String,
    },
}
```

<table><thead><tr><th width="207">Key</th><th>Type</th><th>Description</th></tr></thead><tbody><tr><td><code>position_id</code></td><td>Uint128</td><td>ID of Position</td></tr><tr><td><code>position_owner</code></td><td>String</td><td>Owner of Position</td></tr></tbody></table>

### `RedeemCollateral`

This function allows the redemption of the debt token for collateral assets associated with Positions that have opted in for redemption. The redemption process involves calculating the redeemable amount based on the credit amount, collateral premium, and collateral ratios. The collateral assets are then transferred to the sender's address, redeemed credit subtracted from the Position's debt and any excess credit is returned to the sender as well.

```
#[cw_serde]
pub enum ExecuteMsg {
    RedeemCollateral {
        max_collateral_premium: Option<u128>,
    },
}
```

| Key                       | Type | Description                                                             |
| ------------------------- | ---- | ----------------------------------------------------------------------- |
| `*max_collateral_premium` | u128 | Max collateral premium you're willing to pay when redeeming debt tokens |

&#x20;\* = optional

### `EditRedeemability`

This function allows for the flexible editing and enabling of debt token redemption for specific positions owned by an address, with options to modify redeemability, premiums, maximum loan repayment, and restricted collateral assets.

```
#[cw_serde]
pub enum ExecuteMsg {
    EditRedeemability {
        position_ids: Vec<Uint128>,
        redeemable: Option<bool>,
        premium: Option<u128>,
        max_loan_repayment: Option<Decimal>,
        restricted_collateral_assets: Option<Vec<String>>,
    },
}
```

| Key                             | Type          | Description                                                         |
| ------------------------------- | ------------- | ------------------------------------------------------------------- |
| `position_ids`                  | Vec\<Uint128> | Positions to act on                                                 |
| `*redeemable`                   | bool          | Toggle to add or remove redeemability                               |
| `*premium`                      | u128          | Desired premium for the Position's redeemable collateral            |
| `*max_loan_repayment`           | Decimal       | Max % of the outstanding loan that can be used to redeem collateral |
| `*restricted_collateral_assets` | Vec\<String>  | Collateral assets restricted from redemption, swaps the full list.  |

&#x20;\* = optional

### `MintRevenue`

Mint pending revenue from the contract's Basket, only usable by config

```
#[cw_serde]
pub enum ExecuteMsg {
    MintRevenue {
        send_to: Option<String>,
        repay_for: Option<UserInfo>, 
        amount: Option<Uint128>,
    },
}

pub struct UserInfo {
    pub position_id: Uint128,
    pub position_owner: String,
}
```

<table><thead><tr><th>Key</th><th width="200">Type</th><th>Description</th></tr></thead><tbody><tr><td><code>*send_to</code></td><td>String</td><td>Address to send revenue to,</td></tr><tr><td><code>*repay_for</code></td><td>UserInfo</td><td>Position Info to repay for w/ revenue. To be used for BadDebt situations.</td></tr><tr><td><code>*amount</code></td><td>Uint128</td><td>Amount to mint, defaults to all</td></tr></tbody></table>

&#x20;\* = optional

### `CreateBasket`

Add Basket to the Position's contract, only callable by the contract owner.

```
#[cw_serde]
pub enum ExecuteMsg {
    CreateBasket {
        basket_id: Uint128,
        collateral_types: Vec<cAsset>,
        credit_asset: Asset,
        credit_price: Option<Decimal>,
        base_interest_rate: Option<Decimal>,
        credit_pool_ids: Vec<u64>, //For liquidity measuring
        liq_queue: Option<String>,
    }
}
```

<table><thead><tr><th width="243">Key</th><th>Type</th><th>Description</th></tr></thead><tbody><tr><td><code>basket_id</code></td><td>Uint128</td><td>Basket ID</td></tr><tr><td><code>collateral_type</code></td><td>Vec&#x3C;cAsset></td><td>List of accepted cAssets</td></tr><tr><td><code>credit_asset</code></td><td>Asset</td><td>Asset info for Basket's credit asset</td></tr><tr><td><code>*credit_price</code></td><td>Decimal</td><td>Price of credit in basket</td></tr><tr><td><code>*base_interest_rate</code></td><td>Decimal</td><td>Base interest rate for collateral types</td></tr><tr><td><code>credit_pool_ids</code></td><td>Vec&#x3C;u64></td><td>Used to measure credit liquidity in Osmosis</td></tr><tr><td><code>*liq_queue</code></td><td>String</td><td>Liquidation Queue contract</td></tr></tbody></table>

\* = optional

### `EditBasket`

Add cAsset, change owner and/or change credit\_interest of an existing Basket.

```
#[cw_serde]
pub enum ExecuteMsg {    
    EditBasket(EditBasket),
}

pub struct EditBasket {
    pub added_cAsset: Option<cAsset>,
    pub liq_queue: Option<String>,
    pub credit_pool_infos: Option<Vec<PoolType>>, 
    pub collateral_supply_caps: Option<Vec<SupplyCap>>,
    pub multi_asset_supply_caps: Option<Vec<MultiAssetSupplyCap>>,
    pub base_interest_rate: Option<Decimal>,
    pub credit_asset_twap_price_source: Option<TWAPPoolInfo>,
    pub negative_rates: Option<bool>, 
    pub cpc_margin_of_error: Option<Decimal>,
    pub frozen: Option<bool>,
    pub rev_to_stakers: Option<bool>,
}
```

<table><thead><tr><th width="224">Key</th><th>Type</th><th>Description</th></tr></thead><tbody><tr><td><code>*added_cAsset</code></td><td>cAsset</td><td>cAsset object to add to accepted basket objects</td></tr><tr><td><code>*liq_queue</code></td><td>String</td><td>Liq Queue contract for the credit asset</td></tr><tr><td><code>*credit_pool_ids</code></td><td>Vec&#x3C;PoolType></td><td>Osmosis Pool IDs to query credit liquidity from</td></tr><tr><td><code>*collateral_supply_caps</code></td><td>Vec&#x3C;SupplyCap></td><td>Collateral supply caps </td></tr><tr><td><code>*multi_asset_supply_caps</code></td><td>Vec&#x3C;MultiAssetSupplyCap></td><td>Multi-asset collateral supply caps</td></tr><tr><td><code>*base_interest_rate</code></td><td>Decimal</td><td>Base interest rate for collateral types</td></tr><tr><td><code>*credit_asset_twap_price_source</code></td><td>TWAPPoolInfo</td><td>Oracle information to store for credit price queries</td></tr><tr><td><code>*negative_rates</code></td><td>bool</td><td>Toggle to allow negative repayment interest </td></tr><tr><td><code>*cpc_margin_of_error</code></td><td>Decimal</td><td>Margin of Error before the credit interest is effected by the TWAP price</td></tr><tr><td><code>*frozen</code></td><td>bool</td><td>Freeze withdrawals &#x26; debt increases to fix bugs</td></tr><tr><td><code>*rev_to_stakers</code></td><td>bool</td><td>Toggle revenue to stakers</td></tr></tbody></table>

\* = optional

### `EditcAsset`

Edit a basket's cAsset

```
#[derive(Serialize, Deserialize, Clone, Debug, PartialEq, JsonSchema)]
#[serde(rename_all = "snake_case")]
pub enum ExecuteMsg {
    EditcAsset {
        asset: AssetInfo, 
        //Editables
        max_borrow_LTV: Option<Decimal>, //aka what u can borrow up to
        max_LTV: Option<Decimal>, //ie liquidation point 
    }
}
```

<table><thead><tr><th width="220">Key</th><th>Type</th><th>Description</th></tr></thead><tbody><tr><td>asset</td><td>AssetInfo</td><td>Asset to edit</td></tr><tr><td><code>*max_borrow_LTV</code></td><td>Decimal</td><td>Maximum borrowable LTV</td></tr><tr><td><code>*max_LTV</code></td><td>Decimal</td><td>Point of Liquidation LTV</td></tr></tbody></table>

&#x20;\* = optional

### `Callback`

Messages usable only by the contract to enable functionality in line with message semantics

```
#[cw_serde]
pub enum ExecuteMsg {
    Callback(CallbackMsg)
}

pub enum CallbackMsg {
    BadDebtCheck {
        position_id: Uint128,
        position_owner: Addr,
    },
}
```

## CallbackMsg

### `BadDebtCheck`

After liquidations, this checks for bad debt in the liquidated position.

```
#[cw_serde]
BadDebtCheck {
        position_id: Uint128,
        position_owner: Addr,
}
```

<table><thead><tr><th width="207">Key</th><th>Type</th><th>Description</th></tr></thead><tbody><tr><td><code>position_id</code></td><td>Uint128</td><td>ID of Position</td></tr><tr><td><code>position_owner</code></td><td>Addr</td><td>Owner of Position</td></tr></tbody></table>

## QueryMsg

### `Config`

Returns Config

```
#[cw_serde]
pub enum QueryMsg {
    Config {}
}

pub struct Config {
    pub owner: Addr,
    pub stability_pool: Option<Addr>,
    pub dex_router: Option<Addr>,
    pub staking_contract: Option<Addr>,
    pub osmosis_proxy: Option<Addr>,
    pub debt_auction: Option<Addr>,
    pub oracle_contract: Option<Addr>,
    pub liquidity_contract: Option<Addr>,
    pub discounts_contract: Option<Addr>,
    pub liq_fee: Decimal,    
    pub collateral_twap_timeframe: u64,
    pub credit_twap_timeframe: u64,    
    pub oracle_time_limit: u64,
    pub cpc_multiplier: Decimal,
    pub debt_minimum: Uint128, 
    pub base_debt_cap_multiplier: Uint128,
    pub rate_slope_multiplier: Decimal,
}
```

### `GetUserPositions`

Returns all Positions from a user

```
#[cw_serde]
pub enum QueryMsg {
    GetUserPositions { 
        user: String
        limit: Option<u32>,
    },
}

pub struct PositionResponse {
    pub position_id: String,
    pub collateral_assets: Vec<cAsset>,
    pub cAsset_ratios: Vec<Decimal>,
    pub credit_amount: String,
    pub basket_id: String,
    pub avg_borrow_LTV: Decimal,
    pub avg_max_LTV: Decimal,        
}
```

<table><thead><tr><th width="180">Key</th><th>Type</th><th>Description</th></tr></thead><tbody><tr><td><code>user</code></td><td>String</td><td>Position owner to query for</td></tr><tr><td><code>*limit</code></td><td>u32</td><td>Position Response limiter</td></tr></tbody></table>

\* = optional

### `GetPosition`

Returns single Position data

```
#[cw_serde]
pub enum QueryMsg {
    GetPosition { 
        position_id: Uint128, 
        position_owner: String 
    },
}

pub struct PositionResponse {
    pub position_id: String,
    pub collateral_assets: Vec<cAsset>,
    pub cAsset_ratios: Vec<Decimal>,
    pub credit_amount: String,
    pub basket_id: String,
    pub avg_borrow_LTV: Decimal,
    pub avg_max_LTV: Decimal,        
}
```

<table><thead><tr><th width="180">Key</th><th>Type</th><th>Description</th></tr></thead><tbody><tr><td><code>position_id</code></td><td>Uint128</td><td>ID of Position</td></tr><tr><td><code>basket_id</code></td><td>Uint128</td><td>ID of Basket the Position is in </td></tr><tr><td><code>user</code></td><td>String</td><td>User that owns position</td></tr></tbody></table>

### `GetBasketRedeemability`

Returns redemption info for viable basket positions

```
#[cw_serde]
pub enum QueryMsg {
    GetBasketRedeemability {
        position_owner: Option<String>,
        start_after: Option<u128>,
        limit: Option<u32>,
    },
}
```

| Key               | Type   | Description                 |
| ----------------- | ------ | --------------------------- |
| `*position_owner` | String | Specific owner to query for |
| `*start_after`    | u128   | Premium to start after      |
| `*limit`          | u32    | Response limiter            |

&#x20;\* = optional

### `GetBasketPositions`

Returns all positions in a basket with optional limits

```
#[cw_serde]
pub enum QueryMsg {
    GetBasketPositions { 
        start_after: Option<String>,
        limit: Option<u32>,
    },
}

pub struct PositionsResponse {
    pub user: String,
    pub positions: Vec<Position>,
}

pub struct Position {
    pub position_id: Uint128,
    pub collateral_assets: Vec<cAsset>,
    pub credit_amount: Decimal,
    pub basket_id: Uint128,
}
```

<table><thead><tr><th width="190">Key</th><th>Type</th><th>Description</th></tr></thead><tbody><tr><td><code>*start_after</code></td><td>String</td><td>User address to start after</td></tr><tr><td><code>*limit</code></td><td>u32</td><td>Limit to # of users parsed through</td></tr></tbody></table>

\* = optional

### `GetBasket`

Returns Basket parameters

```
#[cw_serde]
pub enum QueryMsg {
    GetBasket {  }, 
}

pub struct Basket {
    pub basket_id: Uint128,
    pub current_position_id: Uint128,
    pub collateral_types: Vec<cAsset>,
    pub collateral_supply_caps: Vec<SupplyCap>, 
    pub multi_asset_supply_caps: Vec<MultiAssetSupplyCap>,
    pub credit_asset: Asset, 
    pub credit_price: Decimal, 
    pub base_interest_rate: Decimal, 
    pub pending_revenue: Uint128,
    pub credit_last_accrued: u64,
    pub rates_last_accrued: u64,
    pub oracle_set: bool, 
    pub negative_rates: bool, 
    pub frozen: bool, 
    pub rev_to_stakers: bool,
    pub cpc_margin_of_error: Decimal,
    pub liq_queue: Option<Addr>,
}
```

### `GetBasketDebtCaps`

Returns a basket's debt caps per collateral asset, calculates on every call

```
#[cw_serde]
pub enum QueryMsg {
    GetBasketDebtCaps {}
}

//Returns Vec<DebtCap>
pub struct DebtCap {
    pub collateral: AssetInfo,
    pub debt_total: Uint128,
    pub cap: Uint128,
}
```

### `GetBasketBadDebt`

Returns a basket's bad debt

```
#[cw_serde]
pub enum QueryMsg {
    GetBasketBadDebt {}
}

#[derive(Serialize, Deserialize, Clone, Debug, PartialEq, JsonSchema)]
pub struct BadDebtResponse {
    pub has_bad_debt: Vec<(PositionUserInfo, Uint128)>,
}
```

### `GetPositionInsolvency`

Returns a single position's insolvency info

```
#[cw_serde]
pub enum QueryMsg {
    GetPositionInsolvency {
        position_id: Uint128,
        position_owner: String,
    }
}

pub struct InsolvencyResponse {
    pub insolvent_positions: Vec<InsolventPosition>,
}
```

<table><thead><tr><th width="212">Key</th><th>Type</th><th>Description</th></tr></thead><tbody><tr><td><code>position_id</code></td><td>Uint128</td><td>Position ID to query</td></tr><tr><td><code>position_owner</code></td><td>String</td><td>Owner of the position</td></tr></tbody></table>

### `GetCreditRate`

Returns basket's redemption price interest and its sign&#x20;

```
#[cw_serde]
pub enum QueryMsg {
    GetBasketInterest {}
}

#[derive(Serialize, Deserialize, Clone, Debug, PartialEq, JsonSchema)]
pub struct InterestResponse {
    pub credit_interest: Decimal,
    pub negative_rate: bool,
}
```

### `GetCollateralInterest`

Get collateral interest rates from a Basket

```
#[cw_serde]
pub enum QueryMsg {
    GetCollateralInterest {}
}

pub struct CollateralInterestResponse {
    pub rates: Vec<Decimal>,    
}
```

### `Propagation`

Returns `RepayPropagation.`Used internally to test state propagation.

```
#[cw_serde]
pub enum QueryMsg {
    Propagation {}
}

pub struct LiquidationPropagation {
    pub per_asset_repayment: Vec<Decimal>,
    pub liq_queue_leftovers: Decimal, 
    pub stability_pool: Decimal,   
    pub user_repay_amount: Decimal,
    pub positions_contract: Addr,
    pub position_info: UserInfo,
}

```
