---
description: >-
  Proxy to Osmosis SDK module functions. Enforces max supplies for tokens
  created by Membrane. Calculates the liquidity multiplier for Positions
  contracts.
---

# Osmosis Proxy

## InstantiateMsg

```
pub struct InstantiateMsg {}
```

## ExecuteMsg

### `CreateDenom`

Create native asset denom using Osmosis' tokenfactory

```
#[cw_serde]
pub enum ExecuteMsg {
    CreateDenom {
        subdenom: String,        
        max_supply: Option<Uint128>,
    }
}
```

| Key           | Type    | Description                               |
| ------------- | ------- | ----------------------------------------- |
| `subdenom`    | String  | Subdenom for native asset                 |
| `*max_supply` | Uint128 | Token max supply enforced by the contract |

&#x20;\* = optional

### `ChangeAdmin`

Change Admin for owned tokenfactory denom

```
#[cw_serde]
pub enum ExecuteMsg {
    ChangeAdmin {
        denom: String,
        new_admin_address: String,
    }
}
```

| Key                 | Type   | Description                           |
| ------------------- | ------ | ------------------------------------- |
| `denom`             | String | Owned denom to edit                   |
| `new_admin_address` | String | Admin address to migrate ownership to |

### `MintTokens`

Mint tokens for owned token denoms

```
#[cw_serde]
pub enum ExecuteMsg {
    MintTokens {
        denom: String,
        amount: Uint128,
        mint_to_address: String,
    }
}
```

| Key               | Type    | Description               |
| ----------------- | ------- | ------------------------- |
| `denom`           | String  | Token denom to mint       |
| `amount`          | Uint128 | Amount to mint            |
| `mint_to_address` | String  | Address to mint tokens to |

### `BurnTokens`

Burn tokens&#x20;

```
#[cw_serde]
pub enum ExecuteMsg {
    BurnTokens {
        denom: String,
        amount: Uint128,
        burn_from_address: String,
    }
}
```

| Key                 | Type    | Description                 |
| ------------------- | ------- | --------------------------- |
| `denom`             | String  | Token denom to burn         |
| `amount`            | Uint128 | Amount to mint              |
| `burn_from_address` | String  | Address to burn tokens from |

### `EditTokenMaxSupply`

Edit contract enforced token max supply

```
#[cw_serde]
pub enum ExecuteMsg {
    EditTokenMaxSupply {
        denom: String,
        max_supply: Uint128,
    }
}
```

| Key          | Type    | Description         |
| ------------ | ------- | ------------------- |
| `denom`      | String  | Denom's max to edit |
| `max_supply` | Uint128 | New max supply      |

### `UpdateConfig`

Update the contract configuration

```
#[cw_serde]
pub enum ExecuteMsg {
    UpdateConfig {
        owner: Option<Owner>,
        liquidity_multiplier: Option<Decimal>, 
        add_owner: bool,
        debt_auction: Option<String>,
        positions_contract: Option<String>,
        liquidity_contract: Option<String>,
        oracle_contract: Option<String>,
    }
}
```

| Key                     | Type    | Descripiton                                           |
| ----------------------- | ------- | ----------------------------------------------------- |
| `*owner`                | Owner   | New contract owner                                    |
| `add_owner`             | bool    | Add or remove new owner                               |
| `*liquidity_multiplier` | Decimal | Debt Token liquidity multiplier to create mint limits |
| `*debt_auction`         | String  | Debt Auction address                                  |
| `*positions_contract`   | String  | Positions Contract address                            |
| `*liquidity_contract`   | String  | Liquidity Contract address                            |
| `*oracle_contract`      | String  | Oracle contract address                               |

&#x20; \* = optional

### `EditOwner`

Edit restrictions for a contract owner

```
#[cw_serde]
pub enum ExecuteMsg {
    EditOwner {
        owner: String,
        stability_pool_ratio: Option<Decimal>,
        non_token_contract_auth: Option<bool>,
    }
}
```

| Key                        | Type    | Description                                  |
| -------------------------- | ------- | -------------------------------------------- |
| `owner`                    | String  | Owner in to edit                             |
| `*stability_pool_ratio`    | Decimal | Allocate SP cap space to an Owner's debt cap |
| `*non_token_contract_auth` | bool    | Toggle authority for non-token executables   |

&#x20;\* = optional

## QueryMsg

### `Config`

Returns the contract configuration

```
#[cw_serde]
pub enum QueryMsg {
    Config { }
}

pub struct Config {
    pub owners: Vec<Owner>,
    pub liquidity_multiplier: Option<Decimal>,
    pub debt_auction: Option<Addr>,
    pub positions_contract: Option<Addr>,
    pub liquidity_contract: Option<Addr>,
    pub oracle_contract: Option<Addr>,
}
```

### `GetOwner`

Return Owner parameters

```
#[cw_serde]
pub enum QueryMsg {
    GetOwner { owner: String }
}


pub struct OwnerResponse {
    pub owner: Owner,
    pub liquidity_multiplier: Decimal,
}

pub struct Owner {
    pub owner: Addr,
    pub total_minted: Uint128,
    pub stability_pool_ratio: Option<Decimal>,
    pub non_token_contract_auth: bool,
    pub is_position_contract: bool,
}
```

| Key     | Type   | Description    |
| ------- | ------ | -------------- |
| `owner` | String | Owner to query |

### `GetDenom`

Returns full denom of the tokenfactory token representing the subdenom

```
#[cw_serde]
pub enum QueryMsg {
    GetDenom {
        creator_address: String,
        subdenom: String,
    }
}

pub struct GetDenomResponse {
    pub denom: String,
}
```

| Key               | Type   | Description                  |
| ----------------- | ------ | ---------------------------- |
| `creator_address` | String | Admin address of token denom |
| `subdenom`        | String | Token subdenom               |

### `GetContractDenoms`

Returns list of denoms saved in state

```
#[cw_serde]
pub enum QueryMsg {
    GetContractDenoms {
        limit: Option<u32>,
    }
}

//Returns Vec<String>
```

| Key      | Type | Description  |
| -------- | ---- | ------------ |
| `*limit` | u32  | Object limit |

&#x20;\* = optional

### `PoolState`

Returns a list all tokens traded on it with current liquidity (spot) for a given pool ID.  As well as the total number of LP shares and their denom.

```
#[cw_serde]
pub enum QueryMsg {
    PoolState { id: u64 }
}

pub struct PoolStateResponse {
    pub assets: Vec<Coin>,
    pub shares: Coin,
}
```

| Key  | Type | Description     |
| ---- | ---- | --------------- |
| `id` | u64  | Pool identifier |

### `GetTokenInfo`

Returns current supply and max supply for token denoms created with the contract

```
#[cw_serde]
pub enum QueryMsg {
    GetTokenInfo {
        denom: String,
    }
}

pub struct TokenInfoResponse {
    pub denom: String,
    pub current_supply: Uint128,
    pub max_supply: Uint128,
}
```

| Key     | Type   | Description  |
| ------- | ------ | ------------ |
| `denom` | String | Token denom  |
