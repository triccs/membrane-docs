---
description: Contract that holds price fetching information for assets
---

# Oracle

* This oracle contract queries Osmosis TWAP and Pyth network for prices. It's recurrent oracle is OSMO denominated TWAP prices that are then used later to produce USD(-par) prices. The Pyth OSMO/USD price is prioritized, if unavailable it averages USD-par TWAP prices from Osmosis to denominate prices in. This means the CDT, the Positions contract's debt token, flips betweem USD and USD-par (USDC initially) pegs depending on USD availability.

## InstantiateMsg

```
#[derive(Serialize, Deserialize, Clone, Debug, PartialEq, JsonSchema)]
pub struct InstantiateMsg {
    pub owner: Option<String>,
    pub positions_contract: Option<String>,
    pub pyth_osmosis_address: Option<String>,
}
```



| Key                     | Type   | Description                             |
| ----------------------- | ------ | --------------------------------------- |
| `*owner`                | String | Contract owner, defaults to info.sender |
| `*positions_contract`   | String | Positions contract address              |
| `*pyth_osmosis_address` | String | Pyth Network's oracle contract address  |

&#x20;\* = optional

## ExecuteMsg

### `UpdateConfig`

Updates contract configuration

```
#[derive(Serialize, Deserialize, Clone, Debug, PartialEq, JsonSchema)]
#[serde(rename_all = "snake_case")]
pub enum ExecuteMsg {
    UpdateConfig {
        owner: Option<String>,
        positions_contract: Option<String>,
        osmo_usd_pyth_feed_id: Option<PriceIdentifier>,
        pyth_osmosis_address: Option<String>,
        pools_for_usd_par_twap: Option<Vec<TWAPPoolInfo>>,
    }
}

pub struct PriceIdentifier(
    #[serde(with = "hex")]
    #[schemars(with = "String")]
    [u8; 32],
);

pub struct TWAPPoolInfo {
    pub pool_id: u64,
    pub base_asset_denom: String,
    pub quote_asset_denom: String,
}
```

| Key                       | Type            | Description                            |
| ------------------------- | --------------- | -------------------------------------- |
| `*owner`                  | String          | Contract owner                         |
| `*positions_contract`     | String          | Position's contract address            |
| `*osmo_usd_pyth_feed_id`  | PriceIdentifier | OSMO/USD Pyth price feed id            |
| `*pyth_osmosis_address`   | String          | Pyth Network's oracle contract address |
| `*pools_for_usd_par_twap` | TWAPPoolInfo    | Osmosis pools for OSMO/USD-par TWAP    |

&#x20;\* = optional

### `AddAsset`

Add Asset oracle information to the contract

```
#[derive(Serialize, Deserialize, Clone, Debug, PartialEq, JsonSchema)]
#[serde(rename_all = "snake_case")]
pub enum ExecuteMsg {
    AddAsset {
        asset_info: AssetInfo,
        oracle_info: AssetOracleInfo,
    }
}

pub enum AssetInfo {
    Token {
        address: Addr,
    },
    NativeToken {
        denom: String,
    },
}

pub struct AssetOracleInfo {
    pub osmosis_pool_for_twap: TWAPPoolInfo,
    pub pools_for_osmo_twap: Vec<TWAPPoolInfo>,
    pub is_usd_par: bool,
}

pub struct TWAPPoolInfo {
    pub pool_id: u64,
    pub base_asset_denom: String,
    pub quote_asset_denom: String,
 }
```

| Key           | Type            | Description                 |
| ------------- | --------------- | --------------------------- |
| `asset_info`  | AssetInfo       | Asset info                  |
| `oracle_info` | AssetOracleInfo | Oracle info for added asset |

### `EditAsset`

Replace existing asset oracle info

```
#[derive(Serialize, Deserialize, Clone, Debug, PartialEq, JsonSchema)]
#[serde(rename_all = "snake_case")]
pub enum ExecuteMsg {
    EditAsset {
        asset_info: AssetInfo,
        oracle_info: Option<AssetOracleInfo>,
        remove: bool,
    }
}
```

| Key            | Type            | Description            |
| -------------- | --------------- | ---------------------- |
| `asset_info`   | AssetInfo       | Asset info             |
| `*oracle_info` | AssetOracleInfo | Asset oracle info      |
| `remove`       | bool            | Toggle to remove asset |

&#x20;\* = optional

## QueryMsg

### `Config`

Returns contract configuration

```
#[derive(Serialize, Deserialize, Clone, Debug, PartialEq, JsonSchema)]
#[serde(rename_all = "snake_case")]
pub enum QueryMsg {
    Config {}
}

pub struct Config {
    pub owner: Addr,
    pub positions_contract: Option<Addr>,
}
```

### `Price`

Returns list of asset prices and average price&#x20;

```
#[serde(rename_all = "snake_case")]
pub enum QueryMsg {
    Price {
        asset_info: AssetInfo,
        twap_timeframe: u64, 
        basket_id: Option<Uint128>,
    }
}

pub struct PriceResponse {
    pub prices: Vec<PriceInfo>, 
    pub avg_price: Decimal,
}
```

| Key              | Type      | Description                                     |
| ---------------- | --------- | ----------------------------------------------- |
| `asset_info`     | AssetInfo | Asset info                                      |
| `twap_timeframe` | u64       | TWAP timeframe (in days) for TWAP prices        |
| `*basket_id`     | Uint128   | Basket\_id to select oracle quote asset for CDT |

&#x20;\* = optional

### `Prices`

Returns list of PriceResponse for list of assets

```
#[serde(rename_all = "snake_case")]
pub enum QueryMsg {
    Prices {
        asset_infos: Vec<AssetInfo>,
        twap_timeframe: u64,
    }
}

pub struct PriceResponse {
    pub prices: Vec<PriceInfo>, 
    pub price: Decimal,
}
```

| Key              | Type            | Description                              |
| ---------------- | --------------- | ---------------------------------------- |
| `asset_info`     | Vec\<AssetInfo> | Asset infos                              |
| `twap_timeframe` | u64             | TWAP timeframe (in days) for TWAP prices |

### `Assets`

Returns list of oracle info for a list of assets

```
#[serde(rename_all = "snake_case")]
pub enum QueryMsg {
    Assets {
        asset_infos: Vec<AssetInfo>,
    }
}

//Vec of 
pub struct AssetResponse {
    pub asset_info: AssetInfo,
    pub oracle_info: AssetOracleInfo,
}
```

| Key           | Type            | Description        |
| ------------- | --------------- | ------------------ |
| `asset_infos` | Vec\<AssetInfo> | List of Asset Info |
