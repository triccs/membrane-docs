---
description: MBRN Staking contract
---

# Staking

* The Positions contract uses this contract's [DepositFee ](staking.md#depositfee)to allocate liquidation fees to stakers staked at the time of the liquidation. There is a variable fee wait time.&#x20;
* Rewards are earned in the unstaking period but have no voting power, as its primary use is to restrict sales prompted by an activated Debt Auction.&#x20;
* Stakers can restake after starting to unstake if **MBRN** hasn't been withdrawn.&#x20;
* The [Vesting](vesting.md) contract doesn't receive inflationary **MBRN** rewards.
* MBRN-denominated staking rewards are enabled on a schedule to ensure the DAO isn't passively centralizing stake in stakers
* Can't unstake if you have voted on an active proposal or an executable proposal that Governance has passed, that you have voted Yes to, hasn't executed its messages
* All delegation and unstaking actions claim rewards. if you dont want to restake the awarded **MBRN**, use the [ClaimRewards ](staking.md#claimrewards)msg
* You can disable the voting power for delegations which makes them commission only
* The amount of stake used to calculate vesting revenue is toggleable using [UpdateConfig's ](staking.md#updateconfig)vesting\_rev\_multiplier. WARNING: setting to 0 is permanent

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
}

pub struct StakeDistribution {
    pub rate: Decimal,
    pub duration: u64, //in days
}
```

<table><thead><tr><th width="254">Key</th><th>Type</th><th>Description</th></tr></thead><tbody><tr><td><code>*owner</code></td><td>String</td><td>Contract owner</td></tr><tr><td><code>*positions_contract</code></td><td>String</td><td>Positions contract address</td></tr><tr><td><code>*auction_contract</code></td><td>String</td><td>Auction contract address</td></tr><tr><td><code>*vesting_contract</code></td><td>String</td><td>Vesting contract address</td></tr><tr><td><code>*osmosis_proxy</code></td><td>String</td><td>Osmosis Proxy contract address</td></tr><tr><td><code>*incentive_schedule</code></td><td>StakeDistribution</td><td>Desired staking rate schedule, defaults to 10% for 3 months</td></tr><tr><td><code>*fee_wait_period</code></td><td>u64</td><td>Waiting period before stakers earn fees from FeeEvents</td></tr><tr><td><code>*unstaking_period</code></td><td>u64</td><td>Unstaking period in days, defaults to 3 days </td></tr><tr><td><code>mbrn_denom</code></td><td>String</td><td>MBRN full denom</td></tr></tbody></table>

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
        max_commission_rate: Option<Decimal>, 
        keep_raw_cdt: Option<bool>,
        vesting_rev_multiplier: Option<Decimal>,
    }
}
```

<table><thead><tr><th width="248">Key</th><th>Type</th><th>Description</th></tr></thead><tbody><tr><td><code>*owner</code></td><td>String</td><td>Contract owner</td></tr><tr><td><code>*positions_contract</code></td><td>String</td><td>Positions contract address</td></tr><tr><td><code>*auction_contract</code></td><td>String</td><td>Auction contract address</td></tr><tr><td><code>*vesting_contract</code></td><td>String</td><td>Vesting contract address</td></tr><tr><td><code>*osmosis_proxy</code></td><td>String</td><td>Osmosis Proxy contract address</td></tr><tr><td><code>*mbrn_denom</code></td><td>String</td><td>MBRN full denom</td></tr><tr><td><code>*incentive_schedule</code></td><td>StakeDistribution</td><td>Desired staking rate schedule</td></tr><tr><td><code>*fee_wait_period</code></td><td>u64</td><td>Waiting period before stakers earn fees from FeeEvents</td></tr><tr><td><code>*unstaking_period</code></td><td>u64</td><td>Unstaking period in days</td></tr><tr><td><code>*max_commission_rate</code></td><td>Decimal</td><td>Max commission rate</td></tr><tr><td><code>*keep_raw_cdt</code></td><td>bool</td><td>Toggle to give to stakers or send to the fee auction to exchange for a different asset</td></tr><tr><td><code>*vesting_rev_multiplier</code></td><td>Decimal</td><td>Transform the % of its revenue the vesting contract receives</td></tr></tbody></table>

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
        send_to: Option<String>,
        restake: bool,
    }
}
```

<table><thead><tr><th width="225">Key</th><th>Type</th><th>Description</th></tr></thead><tbody><tr><td><code>*send_to</code></td><td>String</td><td>Address to send rewards to</td></tr><tr><td><code>restake</code></td><td>bool</td><td>Restake MBRN toggle</td></tr></tbody></table>

&#x20;\* = optional

### `UpdateDelegations`

Delegate **MBRN** to a Governator

```
#[derive(Serialize, Deserialize, Clone, Debug, PartialEq, JsonSchema)]
#[serde(rename_all = "snake_case")]
pub enum ExecuteMsg {
    UpdateDelegations {
        governator_addr: Option<String>,
        mbrn_amount: Option<Uint128>,
        delegate: Option<bool>,
        fluid: Option<bool>,
        commission: Option<Decimal>,
        voting_power_delegation: Option<bool>,
    }
}
```

| Key                        | Type    | Description                                                                            |
| -------------------------- | ------- | -------------------------------------------------------------------------------------- |
| `*governator_addr`         | String  | Governator to act upon                                                                 |
| `*mbrn_amount`             | Uint128 | Amount to act upon                                                                     |
| `*delegate`                | bool    | To delegate or undelegate                                                              |
| `*fluid`                   | bool    | Toggle fluidity of delegation "Can the governator delegate your delegation?"           |
| `*commission`              | Decimal | Governator's commission rate                                                           |
| `*voting_power_delegation` | bool    | Toggle voting power delegation, i.e. (dis)allow your delegate to use your voting power |

\* = optional

### `DelegateFluidDelegations`

Delegate fluidly delegated **MBRN.** Once delegated, the **MBRN** can't be undelegated by the governator, only the initial staker.&#x20;

```
#[derive(Serialize, Deserialize, Clone, Debug, PartialEq, JsonSchema)]
#[serde(rename_all = "snake_case")]
pub enum ExecuteMsg {
    DelegateFluidDelegations {
        governator_addr: String,
        mbrn_amount: Option<Uint128>,
    }
}
```

| Key                | Type    | Description            |
| ------------------ | ------- | ---------------------- |
| `*governator_addr` | String  | Governator to act upon |
| `*mbrn_amount`     | Uint128 | Amount to act upon     |

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

pub struct Config {
    pub owner: Addr,
    pub mbrn_denom: String,
    pub incentive_schedule: StakeDistribution,
    pub fee_wait_period: u64,
    pub unstaking_period: u64,
    pub max_commission_rate: Decimal,
    pub keep_raw_cdt: bool,
    pub vesting_rev_multiplier: Decimal,
    pub positions_contract: Option<Addr>,
    pub auction_contract: Option<Addr>,
    pub vesting_contract: Option<Addr>,
    pub governance_contract: Option<Addr>,
    pub osmosis_proxy: Option<Addr>,
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

### `UserRewards`

Returns user (_staker and delegate_) rewards

```
#[derive(Serialize, Deserialize, Clone, Debug, PartialEq, JsonSchema)]
#[serde(rename_all = "snake_case")]
pub enum QueryMsg {
    UserRewards { 
        user: String,    
    }
}

pub struct RewardsResponse {
    pub claimables: Vec<Asset>,
    pub accrued_interest: Uint128,
}
```

| Key    | Type   | Description      |
| ------ | ------ | ---------------- |
| `user` | String | Staker's address |

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

### `Delegations`

Returns list of DelegationInfo

```
#[derive(Serialize, Deserialize, Clone, Debug, PartialEq, JsonSchema)]
#[serde(rename_all = "snake_case")]
pub enum QueryMsg {
    Delegations {
        limit: Option<u32>,
        start_after: Option<String>,
        user: Option<String>,
    }
}

pub struct DelegationResponse {
    pub user: Addr,
    pub delegation_info: DelegationInfo,
}

pub struct DelegationInfo {    
    pub delegated: Vec<Delegation>,
    pub delegated_to: Vec<Delegation>,
    pub commission: Decimal,
}

pub struct Delegation {
    pub delegate: Addr,
    pub amount: Uint128,
    pub fluidity: bool,
    pub voting_power_delegation: bool,
    pub time_of_delegation: u64,
}
```

|                |        |                                |
| -------------- | ------ | ------------------------------ |
| `*limit`       | u32    | Response limit                 |
| `*start_after` | String | Start after governator address |
| `*user`        | String | Query a specific user          |

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
