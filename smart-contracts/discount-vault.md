---
description: No lock vault that enables rate discounts for the depositor
---

# Discount Vault

## InstantiateMsg&#x20;

```
#[derive(Serialize, Deserialize, Clone, Debug, PartialEq, Eq, JsonSchema)]
pub struct InstantiateMsg {
    pub owner: Option<String>,   
    pub positions_contract: String,
    pub osmosis_proxy: String,
    pub accepted_LPs: Vec<u64>, //Assumption that the LP is 50:50 
}
```

| Key                  | Type      | Description                                        |
| -------------------- | --------- | -------------------------------------------------- |
| `*owner`             | String    | Contract owner                                     |
| `positions_contract` | String    | Positions contract                                 |
| `osmosis_proxy`      | String    | Osmosis Proxy contract                             |
| `accepted_LPs`       | Vec\<u64> | GAMM share tokens allowed to deposit for discounts |

&#x20; \* = optional

## ExecuteMsg

### `Deposit`

Deposit accepted LP share token into the contract

```
#[derive(Serialize, Deserialize, Clone, Debug, PartialEq, Eq, JsonSchema)]
#[serde(rename_all = "snake_case")]
pub enum ExecuteMsg {
    Deposit { }
}
```

### `Withdraw`

Withdraw LPs from the contract&#x20;

```
#[derive(Serialize, Deserialize, Clone, Debug, PartialEq, Eq, JsonSchema)]
#[serde(rename_all = "snake_case")]
pub enum ExecuteMsg {
    Withdraw { 
        withdrawal_assets: Vec<Asset>,  //in GAMM share tokens (AssetInfo::NativeToken)  
    }
}
```

| Key                 | Type        | Description                |
| ------------------- | ----------- | -------------------------- |
| `withdrawal_assets` | Vec\<Asset> | List of assets to withdraw |

### `ChangeOwner`

Change contract owner

```
#[derive(Serialize, Deserialize, Clone, Debug, PartialEq, Eq, JsonSchema)]
#[serde(rename_all = "snake_case")]
pub enum ExecuteMsg {
    ChangeOwner {
        owner: String,        
    }
}
```

| Key     | Type   | Description        |
| ------- | ------ | ------------------ |
| `owner` | String | New contract owner |

### `EditAcceptedLPs`

Edit LPs accepted for deposits

```
#[derive(Serialize, Deserialize, Clone, Debug, PartialEq, Eq, JsonSchema)]
#[serde(rename_all = "snake_case")]
pub enum ExecuteMsg {
    EditAcceptedLPs {
        pool_ids: Vec<u64>,
        remove: bool,
    }
}
```

| Key        | Type      | Description              |
| ---------- | --------- | ------------------------ |
| `pool_ids` | Vec\<u64> | Osmosis Pool IDs         |
| `remove`   | bool      | Toggle to remove/add IDs |

### `ToggleDeposits`

Enable or disable deposits

```
#[derive(Serialize, Deserialize, Clone, Debug, PartialEq, Eq, JsonSchema)]
#[serde(rename_all = "snake_case")]
pub enum ExecuteMsg {
    ToggleDeposits { enable: bool },
}
```



| Key      | Type | Description                |
| -------- | ---- | -------------------------- |
| `enable` | bool | Enable or disable deposits |

## QueryMsg

### `Config`

Returns contract configuration

```
#[derive(Serialize, Deserialize, Clone, Debug, PartialEq, Eq, JsonSchema)]
#[serde(rename_all = "snake_case")]
pub enum QueryMsg {
    Config { }
}

pub struct Config {
    pub owner: Addr,
    pub positions_contract: Addr,
    pub osmosis_proxy: Addr,
    pub accepted_LPs: Vec<LPPoolInfo>,
    pub deposits_enabled: bool,
}
```

### `User`

Returns User deposit information

```
#[derive(Serialize, Deserialize, Clone, Debug, PartialEq, Eq, JsonSchema)]
#[serde(rename_all = "snake_case")]
pub enum QueryMsg {
    User { 
        user: String,
        minimum_deposit_time: Option<u64>, //in days
    }
}

pub struct UserResponse {
    pub user: String,
    pub deposits: Vec<VaultedLP>,
    pub discount_value: Uint128,
}
```

### `Deposits`

Returns subset of all deposits

```
#[derive(Serialize, Deserialize, Clone, Debug, PartialEq, Eq, JsonSchema)]
#[serde(rename_all = "snake_case")]
pub enum QueryMsg {
    Deposits {
        limit: Option<u64>, //User limit
        start_after: Option<String>, //user
    }
}
```

| Key            | Type   | Description                       |
| -------------- | ------ | --------------------------------- |
| `*limit`       | u64    | Limit number of users in response |
| `*start_after` | String | Start after a user address        |

&#x20;\* = optional
