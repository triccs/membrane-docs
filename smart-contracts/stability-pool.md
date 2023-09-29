---
description: >-
  Inspired by Liquity's Stability Pool
  (https://docs.liquity.org/faq/stability-pool-and-liquidations)
---

# Stability Pool

The Stability Pool (SP) is the second line of defense in Positions contract liquidations. It acts as a pool of liquidity that can be used to repay insolvent positions in return for discounted collateral assets. \
\
When a position is liquidated, the pool repays its debt in exchange for assets sent by the Positions contract after successful repayments. In contrast to [Liquity's ](https://docs.liquity.org/faq/stability-pool-and-liquidations)pro rata model, this SP is **F**irst In **F**irst **O**ut when it comes to rewarding liquidations to pool liquidity providers.\
\
Due to how the [Liquidation Queue](liquidation-queue.md) calculates liquidations, there will always be something for the SP to liquidate, meaning its advantageous for the first bidder at every liquidation and not just the one's the Liq Queue can't fulfill. This has the added benefit of filtering through spam deposits before large liquidatiosn.\
\
Pro-rata distributions, like the Liq Queue and Liquity's SP are better than FIFO at attracting large capital, but FIFO has direct incentives for competitive replenishes which is better for a pool that isn't prioritized but needs quick refills if the situation calls for it.\
\
We want this step of the liquidation mechanism to be reactive when low while not taking too much potential capital from the Liq Queue which will likely liquidate collateral for lower premiums a majority of the time, which is better for [user solvency](https://twitter.com/euler\_mab/status/1537091423748517889).

**Key Points:**

* **Any user funds in the Stability Pool will be used to repay said user's positions if liquidated. Meaning depositing in the SP doesn't increase liquidation risk for the user.**
* **Unstaked deposits are still used to liquidate but accrue no incentives.**
* **Withdrawals that would leave less than the minmum deposit amount will withdraw the remaining bid.**

## InstantiateMsg

```
#[derive(Serialize, Deserialize, Clone, Debug, PartialEq, JsonSchema)]
pub struct InstantiateMsg {
    pub owner: Option<String>,
    pub asset_pool: Option<AssetPool>,
    pub incentive_rate: Option<Decimal>,
    pub max_incentives: Option<Uint128>,
    pub minimum_deposit_amount: Uint128,
    pub osmosis_proxy: String,
    pub positions_contract: String,
    pub oracle_contract: String,
    pub mbrn_denom: String,
}

pub struct AssetPool {
    pub credit_asset: Asset,
    pub liq_premium: Decimal,
    pub deposits: Vec<Deposit>
}

pub struct Asset {
    pub info: AssetInfo,
    pub amount: Uint128,
}
```

<table><thead><tr><th width="231">Key</th><th>Type</th><th>Description</th></tr></thead><tbody><tr><td><code>*asset_pool</code></td><td>AssetPool</td><td>Initial Asset Pool for the contract</td></tr><tr><td><code>*owner</code></td><td>String</td><td>Owner of the contract, defaults to info.sender</td></tr><tr><td><code>*incentive_rate</code></td><td>Decimal</td><td>Base MBRN incentive rate</td></tr><tr><td><code>*max_incentives</code></td><td>Uint128</td><td>Maximum MBRN the Pool can mint for incentives</td></tr><tr><td><code>*minimum_deposit_amount</code></td><td>Uint128</td><td>Minimum deposit amount</td></tr><tr><td><code>osmosis_proxy</code></td><td>String</td><td>Osmosis Proxy contract address</td></tr><tr><td><code>positions_contract</code></td><td>String</td><td>CDP contract</td></tr><tr><td><code>oracle_contract</code></td><td>String</td><td>Oracle contract</td></tr><tr><td><code>mbrn_denom</code></td><td>String</td><td>MBRN denom</td></tr></tbody></table>

\* = optional

## ExecuteMsg

### `UpdateConfig`

Update Config if info.sender is config.owner

```
#[derive(Serialize, Deserialize, Clone, Debug, PartialEq, JsonSchema)]
#[serde(rename_all = "snake_case")]
pub enum ExecuteMsg {
    UpdateConfig(UpdateConfig),
}

pub struct UpdateConfig {
    owner: Option<String>,
    incentive_rate: Option<Decimal>,
    max_incentives: Option<Uint128>,
    minimum_deposit_amount: Option<Uint128>,
    unstaking_period: Option<u64>,
    osmosis_proxy: Option<String>,
    positions_contract: Option<String>,
    pub oracle_contract: Option<String>,
    mbrn_denom: Option<String>,
}
```

### `Deposit`

Deposit accepted credit assets to corresponding Asset Pools

```
#[derive(Serialize, Deserialize, Clone, Debug, PartialEq, JsonSchema)]
#[serde(rename_all = "snake_case")]
pub enum ExecuteMsg {
    Deposit { 
        user: Option<String>
    }
}
```

| Key     | Type   | Description                            |
| ------- | ------ | -------------------------------------- |
| `*user` | String | Address with claim over the deposit(s) |

\* = optional

### `Withdraw`

Withdraw caller owned deposits from corresponding Asset Pools

```
#[derive(Serialize, Deserialize, Clone, Debug, PartialEq, JsonSchema)]
#[serde(rename_all = "snake_case")]
pub enum ExecuteMsg {
    Withdraw { 
        amount: Uint128
    }
}
```

| Key      | Type    | Description            |
| -------- | ------- | ---------------------- |
| `amount` | Uint128 | Amount to be withdrawn |

### `Restake`

Restake unstak(ed/ing) assets

```
#[derive(Serialize, Deserialize, Clone, Debug, PartialEq, JsonSchema)]
#[serde(rename_all = "snake_case")]
pub enum ExecuteMsg {
    Restake { 
        restake_amount: Decimal,
    }
}
```

| Key              | Type    | Description        |
| ---------------- | ------- | ------------------ |
| `restake_amount` | Decimal | Amount to restake  |

### `Liquidate`

Use Asset Pool assets to repay for a [Position ](positions.md#getposition)and earn discounted assets

```
#[derive(Serialize, Deserialize, Clone, Debug, PartialEq, JsonSchema)]
#[serde(rename_all = "snake_case")]
pub enum ExecuteMsg {
    Liquidate { 
        liq_amount: Decimal
    }
}
```

| Key          | Type    | Description         |
| ------------ | ------- | ------------------- |
| `liq_amount` | Decimal | Amount to be repaid |

### `ClaimRewards`

Claim all discounted assets received from liquidations for the calling address

```
#[derive(Serialize, Deserialize, Clone, Debug, PartialEq, JsonSchema)]
#[serde(rename_all = "snake_case")]
pub enum ExecuteMsg {
    ClaimRewards { }
}
```

### `Distribute`

Called by the Positions contract. Distributes liquidated funds to the users whose Deposits were used to repay the debt.

```
#[derive(Serialize, Deserialize, Clone, Debug, PartialEq, JsonSchema)]
#[serde(rename_all = "snake_case")]
pub enum ExecuteMsg {
    Distribute { //Distributes liquidated funds to users
        distribution_assets: Vec<Asset>,
        distribution_asset_ratios: Vec<Decimal>,
        distribute_for: Uint128,
    }
}
```

<table><thead><tr><th width="327">Key</th><th>Type</th><th>Description</th></tr></thead><tbody><tr><td><code>distribution_assets</code></td><td>Vec&#x3C;Asset></td><td>Assets to be distributed to users</td></tr><tr><td><code>distribution-asset-ratios</code></td><td>Vec&#x3C;Decimal></td><td>Ratios of distribution assets</td></tr><tr><td><code>credit_price</code></td><td>Decimal</td><td>Redemption price of <code>credit_asset</code></td></tr></tbody></table>

### `Repay`

Allows the Positions contract to use user funds to repay for themselves

```
#[derive(Serialize, Deserialize, Clone, Debug, PartialEq, JsonSchema)]
#[serde(rename_all = "snake_case")]
pub enum ExecuteMsg {
    Repay {
        user_info: UserInfo,
        repayment: Asset,
    }
}

pub struct UserInfo {
    pub position_id: Uint128,
    pub position_owner: String,
}
```

| Key         | Type     | Description          |
| ----------- | -------- | -------------------- |
| `user_info` | UserInfo | User's Position info |
| `repayment` | Asset    | Asset to repay       |

## QueryMsg

### `Config`

Returns the current Config fields

```
#[derive(Serialize, Deserialize, Clone, Debug, PartialEq, JsonSchema)]
#[serde(rename_all = "snake_case")]
pub enum QueryMsg {
    Config {}
}

#[derive(Serialize, Deserialize, Clone, Debug, PartialEq, JsonSchema)]
pub struct Config {
    pub owner: Addr, //Positions contract address
    pub incentive_rate: Decimal,
    pub max_incentives: Uint128,
    pub unstaking_period: u64, // in days
    pub mbrn_denom: String,
    pub osmosis_proxy: Addr,
    pub positions_contract: Addr,
    pub oracle_contract: Addr,
}
```

### `CheckLiquidatible`

Returns the amount of said asset that isn't liquidatible (i.e. leftover)

```
#[derive(Serialize, Deserialize, Clone, Debug, PartialEq, JsonSchema)]
#[serde(rename_all = "snake_case")]
pub enum QueryMsg {
    CheckLiquidatible { 
        amount: Decimal  
    }
}

pub struct LiquidatibleResponse {
    pub leftover: Decimal,
}
```

| Key      | Type    | Description         |
| -------- | ------- | ------------------- |
| `amount` | Decimal | Amount to check for |

### `UserClaims`

Returns the `user`'s claimable assets

```
#[derive(Serialize, Deserialize, Clone, Debug, PartialEq, JsonSchema)]
#[serde(rename_all = "snake_case")]
pub enum QueryMsg {
    UserClaims { 
        user: String 
    }
}

pub struct ClaimsResponse {
    pub claims: Vec<Coin>,
}
```

| Key    | Type   | Description                    |
| ------ | ------ | ------------------------------ |
| `user` | String | The user whose claims to check |

### `AssetPool`

Returns Asset Pool info

```
#[derive(Serialize, Deserialize, Clone, Debug, PartialEq, JsonSchema)]
#[serde(rename_all = "snake_case")]
pub enum QueryMsg {
    AssetPool { 
        user: Option<String>,
        deposit_limit: Option<u32>,
        start_after: Option<u32>,        
    }
}

pub struct PoolResponse {
    pub credit_asset: Asset,
    pub liq_premium: Decimal,
    pub deposits: Vec<Deposit>
}
```

| Key              | Type   | Description               |
| ---------------- | ------ | ------------------------- |
| `*user`          | String | User's deposits to return |
| `*deposit_limit` | u32    | Deposit limit             |
| `*start_after`   | u32    | Start after Deposit count |

### `UnclaimedIncentives`

Returns unclaimed incentives for a user in an AssetPool

```
#[derive(Serialize, Deserialize, Clone, Debug, PartialEq, JsonSchema)]
#[serde(rename_all = "snake_case")]
pub enum QueryMsg {
    UnclaimedIncentives {
        user: String,
    }
}

//Returns Uint128
```

| Key    | Type   | Description  |
| ------ | ------ | ------------ |
| `user` | String | User address |

### `CapitalAheadOfDeposit`

Returns capital ahead of each user Deposit in an AssetPool

```
#[derive(Serialize, Deserialize, Clone, Debug, PartialEq, JsonSchema)]
#[serde(rename_all = "snake_case")]
pub enum QueryMsg {
    CapitalAheadOfDeposit {
        user: String,
    }
}

pub struct DepositPositionResponse {
    pub deposit: Deposit,
    pub capital_ahead: Decimal,
}

pub struct Deposit {
    pub user: Addr,
    pub amount: Decimal,
    pub deposit_time: u64,
    pub last_accrued: u64,
    pub unstake_time: Option<u64>,
}
```

| Key    | Type   | Description  |
| ------ | ------ | ------------ |
| `user` | String | User address |
