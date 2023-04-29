---
description: >-
  Contract that auctions discounted MBRN to repay bad debts in the Positions
  contract or discounted fees that burns MBRN.
---

# Auction

Auctions are initialized by the [Positions ](positions.md)contract and [Governance ](gov.md). The discount increases overtime to ensure the auction is fulfilled.

## InstantiateMsg

```
#[derive(Serialize, Deserialize, Clone, Debug, PartialEq, JsonSchema)]
pub struct InstantiateMsg {
    pub owner: Option<String>,
    pub oracle_contract: String,
    pub osmosis_proxy: String,
    pub positions_contract: String,
    pub twap_timeframe: u64,
    pub mbrn_denom: String,
    pub initial_discount: Decimal,
    pub discount_increase_timeframe: u64,
    pub discount_increase: Decimal,
}
```

| Key                           | Type    | Description                                                    |
| ----------------------------- | ------- | -------------------------------------------------------------- |
| `*owner`                      | String  | Contract owner                                                 |
| `oracle_contract`             | String  | Oracle contract address                                        |
| `osmosis_proxy`               | String  | Osmosis Proxy address                                          |
| `positions_contract`          | String  | Position's contract address                                    |
| `twap_timeframe`              | u64     | Timeframe for TWAPs                                            |
| `mbrn_denom`                  | String  | MBRN token full denom                                          |
| `initial_discount`            | Decimal | Starting discount of auctions                                  |
| `discount_increase_timeframe` | u64     | Timeframe in which the discount is increased                   |
| `discount_increase`           | Decimal | Increase in discount per unit of discount\_increase\_timeframe |

&#x20;\* = optional

## ExecuteMsg

### `StartAuction`

Start or add to ongoing auction

```
#[derive(Serialize, Deserialize, Clone, Debug, PartialEq, JsonSchema)]
#[serde(rename_all = "snake_case")]
pub enum ExecuteMsg {
    StartAuction {
        repayment_position_info: Option<UserInfo>,
        send_to: Option<String>,
        auction_asset: Asset,
    }
}

pub struct UserInfo {
    pub basket_id: Uint128,
    pub position_id: Uint128,
    pub position_owner: String,
}

pub struct Asset{
    pub info: AssetInfo,
    pub amount: Uint128,
}
```

| Key                        | Type     | Description                           |
| -------------------------- | -------- | ------------------------------------- |
| `*repayment_position_info` | UserInfo | Position info that holds the bad debt |
| `*send_to`                 | String   | Send captial to this address          |
| `auction_asset`            | Asset    | Asset info + amount to swap for       |

### `RemoveAuction`

Remove ongoing auction

```
#[derive(Serialize, Deserialize, Clone, Debug, PartialEq, JsonSchema)]
#[serde(rename_all = "snake_case")]
pub enum ExecuteMsg {
    RemoveAuction {}
}
```

### `SwapForMBRN`

Swap for MBRN with CDT

```
#[derive(Serialize, Deserialize, Clone, Debug, PartialEq, JsonSchema)]
#[serde(rename_all = "snake_case")]
pub enum ExecuteMsg {
    SwapForMBRN {}
}
```

### `SwapWithMBRN`

Swap with MBRN for fee assets

```
#[derive(Serialize, Deserialize, Clone, Debug, PartialEq, JsonSchema)]
#[serde(rename_all = "snake_case")]
pub enum ExecuteMsg {
    SwapWithMBRN { auction_asset: AssetInfo }
}
```

| Key             | Type      | Description         |
| --------------- | --------- | ------------------- |
| `auction_asset` | AssetInfo | Auction asset denom |

### `UpdateConfig`

Update contract configurations

```
#[derive(Serialize, Deserialize, Clone, Debug, PartialEq, JsonSchema)]
#[serde(rename_all = "snake_case")]
pub enum ExecuteMsg {
    UpdateConfig(UpdateConfig)
}

pub struct UpdateConfig {
    pub owner: Option<String>,
    pub oracle_contract: Option<String>,
    pub osmosis_proxy: Option<String>,
    pub mbrn_denom: Option<String>,
    pub positions_contract: Option<String>,
    pub cdt_denom: Option<String>,
    pub twap_timeframe: Option<u64>,
    pub initial_discount: Option<Decimal>,
    pub discount_increase_timeframe: Option<u64>, //in seconds
    pub discount_increase: Option<Decimal>,       //% increase
}
```

| Key                            | Type    | Description                                                     |
| ------------------------------ | ------- | --------------------------------------------------------------- |
| `*owner`                       | String  | Contract owner                                                  |
| `*oracle_contract`             | String  | Oracle contract address                                         |
| `*osmosis_proxy`               | String  | Osmosis Proxy address                                           |
| `*mbrn_denom`                  | String  | MBRN denom                                                      |
| `*positions_contract`          | String  | Positions contract address                                      |
| `*cdt_denom`                   | String  | CDT denom                                                       |
| `*twap_timeframe`              | u64     | TWAP timeframe for oracle queries                               |
| `*initial_discount`            | Decimal | Initial auction MBRN price discount                             |
| `*discount_increase_timeframe` | u64     | Timeframe in which the multiple of discount\_increase increases |
| `*discount_increase`           | Decimal | Increase in discount per unit of discount\_increase\_timeframe  |

&#x20;\* = optional

## QueryMsg

### `Config`

Return Config

```
#[derive(Serialize, Deserialize, Clone, Debug, PartialEq, JsonSchema)]
#[serde(rename_all = "snake_case")]
pub enum QueryMsg {
    Config {}
}

pub struct Config {    
    pub owner: Addr,
    pub oracle_contract: Addr,
    pub osmosis_proxy: Addr,
    pub mbrn_denom: String,
    pub positions_contract: Addr,
    pub cdt_denom: String,
    pub twap_timeframe: u64,
    pub initial_discount: Decimal,
    pub discount_increase_timeframe: u64, //in seconds
    pub discount_increase: Decimal, //% increase
}
```

### `OngoingFeeAuctions`

Returns list of ongoing FeeAuctions

```
#[derive(Serialize, Deserialize, Clone, Debug, PartialEq, JsonSchema)]
#[serde(rename_all = "snake_case")]
pub enum QueryMsg {
    OngoingFeeAuctions {
        debt_asset: Option<AssetInfo>,
        limit: Option<u64>,
        start_after: Option<u64>,
    }
}

//Returns list of FeeAuction
pub struct FeeAuction {
    pub auction_asset: Asset,
    pub auction_start_time: u64,
}
```

| Key            | Type      | Description                               |
| -------------- | --------- | ----------------------------------------- |
| `*debt_asset`  | AssetInfo | Specific auction to return, list of 1     |
| `*limit`       | u64       | Limit to the amount of returned responses |
| `*start_after` | u64       | Asset to filter out of responses          |

&#x20;\* = optional

### `DebtAuction`

Returns DebtAuction info

```
#[derive(Serialize, Deserialize, Clone, Debug, PartialEq, JsonSchema)]
#[serde(rename_all = "snake_case")]
pub enum QueryMsg {
    DebtAuction {}
}

pub struct DebtAuction {
    pub remaining_recapitalization: Uint128,
    pub repayment_positions: Vec<RepayPosition>,
    pub send_to: Vec<AuctionRecipient>,
    pub auction_start_time: u64,
}
```
