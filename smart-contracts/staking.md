---
description: MBRN Staking contract
---

# Staking

* The Positions contract uses this contract's [DepositFee ](staking.md#depositfee)to allocate liquidation fees to stakers staked at the time of the liquidation. There is a variable fee wait time.&#x20;
* Rewards are earned in the unstaking period but have no voting power, as its primary use is to restrict sales prompted by an activated Debt Auction.&#x20;
* Stakers can restake after starting to unstake if **MBRN** hasn't been withdrawn.&#x20;
* The [Vesting](vesting.md) contract doesn't receive inflationary **MBRN** rewards.
* MBRN-denominated staking rewards are enabled on a schedule to ensure the DAO isn't passively centralizing stake in stakers
* Can't unstake if an executable proposal in Governance has passed that you have voted Yes to hasn't executed its messages

## InstantiateMsg

```
#[derive(Serialize, Deserialize, Clone, Debug, PartialEq, JsonSchema)]
pub struct InstantiateMsg {
    pub owner: Option<String>,
    pub positions_contract: Option<String>,
    pub auction_contract: Option<String>,
    pub vesting_contract: Option<String>,
    pub osmosis_proxy: Option<String>,   
    pub incentive_schedule: Option<StakeDistribution>,
    pub fee_wait_period: Option<u64>,  
    pub unstaking_period: Option<u64>,    
    pub mbrn_denom: String,
    pub dex_router: Option<String>,
    pub max_spread: Option<Decimal>,
}

pub struct StakeDistribution {
    pub rate: Decimal,
    pub duration: u64, //in days
}
```

| Key                   | Type              | Description                                                 |
| --------------------- | ----------------- | ----------------------------------------------------------- |
| `*owner`              | String            | Contract owner                                              |
| `*positions_contract` | String            | Positions contract address                                  |
| `*auction_contract`   | String            | Auction contract address                                    |
| `*vesting_contract`   | String            | Vesting contract address                                    |
| `*osmosis_proxy`      | String            | Osmosis Proxy contract address                              |
| `*incentive_schedule` | StakeDistribution | Desired staking rate schedule, defaults to 10% for 3 months |
| `*fee_wait_period`    | u64               | Waiting period before stakers earn fees from FeeEvents      |
| `*unstaking_period`   | u64               | Unstaking period in days, defaults to 3 days                |
| `mbrn_denom`          | String            | MBRN full denom                                             |
| `*dex_router`         | String            | DEX Router contract address                                 |
| `*max_spread`         | Decimal           | Max spread for asset routing, defaults to 10%               |

&#x20;\* = optional

## ExecuteMsg

### `UpdateConfig`

Update contract configuration

```
#[derive(Serialize, Deserialize, Clone, Debug, PartialEq, JsonSchema)]
#[serde(rename_all = "snake_case")]
pub enum ExecuteMsg {
    UpdateConfig {
        owner: Option<String>,
        positions_contract: Option<String>,
        auction_contract: Option<String>,
        vesting_contract: Option<String>,
        osmosis_proxy: Option<String>,
        mbrn_denom: Option<String>,  
        incentive_schedule: Option<StakeDistribution>,
        fee_wait_period: Option<u64>,     
        unstaking_period: Option<u64>,            
        dex_router: Option<String>,
        max_spread: Option<Decimal>,
    }
}
```

| Key                   | Type              | Description                                            |
| --------------------- | ----------------- | ------------------------------------------------------ |
| `*owner`              | String            | Contract owner                                         |
| `*positions_contract` | String            | Positions contract address                             |
| `*auction_contract`   | String            | Auction contract address                               |
| `*vesting_contract`   | String            | Vesting contract address                               |
| `*osmosis_proxy`      | String            | Osmosis Proxy contract address                         |
| `*incentive_schedule` | StakeDistribution | Desired staking rate schedule                          |
| `*fee_wait_period`    | u64               | Waiting period before stakers earn fees from FeeEvents |
| `*unstaking_period`   | u64               | Unstaking period in days                               |
| `*mbrn_denom`         | String            | MBRN full denom                                        |
| `*dex_router`         | String            | DEX Router contract address                            |
| `*max_spread`         | Decimal           | Max spread for asset routing                           |

&#x20;\* = optional

### `Stake`

Stake MBRN for user

```
#[derive(Serialize, Deserialize, Clone, Debug, PartialEq, JsonSchema)]
#[serde(rename_all = "snake_case")]
pub enum ExecuteMsg {
    Stake { 
        user: Option<String>,
    }
}
```

| Key     | Type   | Description                                |
| ------- | ------ | ------------------------------------------ |
| `*user` | String | User to stake for, defaults to info.sender |

&#x20;\* = optional

### `Unstake`

Withdraw desired stake for info.sender

```
#[derive(Serialize, Deserialize, Clone, Debug, PartialEq, JsonSchema)]
#[serde(rename_all = "snake_case")]
pub enum ExecuteMsg {
    Unstake { 
        mbrn_amount: Option<Uint128>,
    }
}
```

| Key            | Type    | Description                                     |
| -------------- | ------- | ----------------------------------------------- |
| `*mbrn_amount` | Uint128 | MBRN amount to unstake, defaults to total stake |

&#x20;\* = optional

### `Restake`

Restake unstak(ed/ing) MBRN

```
#[derive(Serialize, Deserialize, Clone, Debug, PartialEq, JsonSchema)]
#[serde(rename_all = "snake_case")]
pub enum ExecuteMsg {
    Restake { 
        mbrn_amount: Uint128,
    }
}
```

| Key           | Type    | Description            |
| ------------- | ------- | ---------------------- |
| `mbrn_amount` | Uint128 | MBRN amount to restake |

### `ClaimRewards`

Claim all staking rewards for info.sender

```
#[derive(Serialize, Deserialize, Clone, Debug, PartialEq, JsonSchema)]
#[serde(rename_all = "snake_case")]
pub enum ExecuteMsg {
    ClaimRewards { 
        claim_as_native: Option<String>, //Native FullDenom
        claim_as_cw20: Option<String>, //Contract Address
        send_to: Option<String>,
        restake: bool,
    }
}
```

| Key                | Type   | Description                              |
| ------------------ | ------ | ---------------------------------------- |
| `*claim_as_native` | String | Native token full denom to claim fees as |
| `*claim_as_cw20`   | String | Cw20 token address to claim fees as      |
| `*send_to`         | String | Address to send rewards to               |
| `restake`          | bool   | Restake MBRN toggle                      |

&#x20;\* = optional

### `DepositFee`

Positions contract deposit's liquidation fees to be distributed to stakers

```
#[derive(Serialize, Deserialize, Clone, Debug, PartialEq, JsonSchema)]
#[serde(rename_all = "snake_case")]
pub enum ExecuteMsg {
    DepositFee { }
}
```

### `TrimFeeEvents`

Trims the state object containing the list of FeeEvents, callable by owner

```
#[derive(Serialize, Deserialize, Clone, Debug, PartialEq, JsonSchema)]
#[serde(rename_all = "snake_case")]
pub enum ExecuteMsg {
    TrimFeeEvents { }
}
```

## QueryMsg

### `Config`

Returns Config

```
#[derive(Serialize, Deserialize, Clone, Debug, PartialEq, JsonSchema)]
#[serde(rename_all = "snake_case")]
pub enum QueryMsg {
    Config {}
}

pub struct ConfigResponse {
    pub owner: String, 
    pub positions_contract: String,
    pub builders_contract: String,
    pub osmosis_proxy: String,    
    pub staking_rate: String,
    pub fee_wait_period: String,
    pub unstaking_period: String,    
    pub mbrn_denom: String,
    pub dex_router: String,
    pub max_spread: String, 
}
```

### `UserStake`

Returns Staker information

```
#[derive(Serialize, Deserialize, Clone, Debug, PartialEq, JsonSchema)]
#[serde(rename_all = "snake_case")]
pub enum QueryMsg {
    UserStake { 
        staker: String,    
    }
}

pub struct StakerResponse {
    pub staker: String,
    pub total_staked: Uint128,
    pub deposit_list: Vec<(String, String)>, //Amount and timestamp of each deposit
}
```

| Key      | Type   | Description      |
| -------- | ------ | ---------------- |
| `staker` | String | Staker's address |

### `StakerRewards`

Returns Staker rewards

```
#[derive(Serialize, Deserialize, Clone, Debug, PartialEq, JsonSchema)]
#[serde(rename_all = "snake_case")]
pub enum QueryMsg {
    StakerRewards { 
        staker: String,    
    }
}

pub struct RewardsResponse {
    pub claimables: Vec<Asset>,
    pub accrued_interest: Uint128,
}
```

| Key      | Type   | Description      |
| -------- | ------ | ---------------- |
| `staker` | String | Staker's address |

### `Staked`

Returns list of all Staked

```
#[derive(Serialize, Deserialize, Clone, Debug, PartialEq, JsonSchema)]
#[serde(rename_all = "snake_case")]
pub enum QueryMsg {
    Staked {
        limit: Option<u32>,
        start_after: Option<u64>, //Timestamp in seconds
        end_before: Option<u64>,  //Timestamp in seconds
        unstaking: bool,          //true if u want unstakers included
    }
}

#[derive(Serialize, Deserialize, Clone, Debug, PartialEq, JsonSchema)]
pub struct StakedResponse {
    pub stakers: Vec<StakeDeposit>,
}

pub struct StakeDeposit {
    pub staker: Addr,
    pub amount: Uint128,
    pub deposit_time: u64,
}
```

| Key            | Type | Description                           |
| -------------- | ---- | ------------------------------------- |
| `*limit`       | u32  | Limit # of entries returned           |
| `*start_after` | u64  | Start after a block time in seconds   |
| `*end_before`  | u64  | End before a block time in seconds    |
| `unstaking`    | bool | True to include unstakers in response |

&#x20;\* = optional

### `FeeEvents`

Returns list of all Fee Events

```
#[derive(Serialize, Deserialize, Clone, Debug, PartialEq, JsonSchema)]
#[serde(rename_all = "snake_case")]
pub enum QueryMsg {
    FeeEvents {
        limit: Option<u32>,
        start_after: Option<u64>, //Timestamp in seconds
    }
}

pub struct FeeEventsResponse {
    pub fee_events: Vec<FeeEvent>,
}

pub struct FeeEvent {
    pub time_of_event: u64,
    pub fee: LiqAsset,
}
```

| Key            | Type | Description                         |
| -------------- | ---- | ----------------------------------- |
| `*limit`       | u32  | Limit # of entries returned         |
| `*start_after` | u64  | Start after a block time in seconds |

&#x20;\* = optional

### `TotalStaked`

Returns total MBRN staked

```
#[derive(Serialize, Deserialize, Clone, Debug, PartialEq, JsonSchema)]
#[serde(rename_all = "snake_case")]
pub enum QueryMsg {
    TotalStaked { }
}

pub struct TotalStakedResponse {
    pub total_not_including_vested: Uint128,
    pub vested_total: Uint128,
}
```

### `IncentiveSchedule`

Returns current staking rate duration&#x20;

```
#[derive(Serialize, Deserialize, Clone, Debug, PartialEq, JsonSchema)]
#[serde(rename_all = "snake_case")]
pub enum QueryMsg {
    IncentiveSchedule {}
}

pub struct StakeDistribution {
    pub rate: Decimal,
    pub duration: u64, //in days
}

pub struct StakeDistributionLog {
    pub ownership_distribution: StakeDistribution,
    pub start_time: u64,
}
```
