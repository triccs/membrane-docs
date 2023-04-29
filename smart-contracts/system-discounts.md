---
description: Calculates discounts for Membrane leverage instruments
---

# System Discounts

## InstantiateMsg

```
#[derive(Serialize, Deserialize, Clone, Debug, PartialEq, Eq, JsonSchema)]
pub struct InstantiateMsg {
    pub owner: Option<String>,
    pub oracle_contract: String,
    pub positions_contract: String,
    pub staking_contract: String,
    pub stability_pool_contract: String,
    pub lockdrop_contract: Option<String>,
    pub discount_vault_contract: Option<String>,
    pub minimum_time_in_network: u64, //in days
}
```

| Key                        | Type   | Description                           |
| -------------------------- | ------ | ------------------------------------- |
| `*owner`                   | String | Contract owner                        |
| `oracle_contract`          | String | Oracle contract address               |
| `positions_contract`       | String | Positions contract address            |
| `staking_contract`         | String | Staking contract address              |
| `stability_pool_contract`  | String | Stability Pool contract address       |
| `*lockdrop_contract`       | String | Lockdrop contract address             |
| `*discount_vault_contract` | String | Discount Vault contract address       |
| `minimum_time_in_network`  | u64    | Minimum time in network for discounts |

&#x20;\* = optional

## ExecuteMsg

### `UpdateConfig`

Update contract configuration

```
#[derive(Serialize, Deserialize, Clone, Debug, PartialEq, Eq, JsonSchema)]
#[serde(rename_all = "snake_case")]
pub enum ExecuteMsg {
    UpdateConfig(UpdateConfig),
}

pub struct UpdateConfig {
    pub owner: Option<String>,  
    pub mbrn_denom: Option<String>,      
    pub oracle_contract: Option<String>,
    pub positions_contract: Option<String>,
    pub staking_contract: Option<String>,
    pub stability_pool_contract: Option<String>,
    pub lockdrop_contract: Option<String>,
    pub discount_vault_contract: Option<(String,bool)>, //address, add
    pub minimum_time_in_network: Option<u64>, //in days
}
```

## QueryMsg

### `Config`&#x20;

Returns contract configuration

```
Returns contract configuration
#[derive(Serialize, Deserialize, Clone, Debug, PartialEq, Eq, JsonSchema)]
#[serde(rename_all = "snake_case")]
pub enum QueryMsg {
    Config {}
}

pub struct Config {
    pub owner: Addr,
    pub mbrn_denom: String,
    pub oracle_contract: Addr,
    pub positions_contract: Addr,
    pub staking_contract: Addr,
    pub stability_pool_contract: Addr,
    pub lockdrop_contract: Option<Addr>,
    pub discount_vault_contract: Vec<Addr>,
    pub minimum_time_in_network: u64, //in days
}
```

### `UserDiscount`

Returns % discount for a user

```
#[derive(Serialize, Deserialize, Clone, Debug, PartialEq, Eq, JsonSchema)]
#[serde(rename_all = "snake_case")]
pub enum QueryMsg {
    UserDiscount { user: String }
}

//Returns Decimal
```

| Key    | Type   | Description  |
| ------ | ------ | ------------ |
| `user` | String | User address |
