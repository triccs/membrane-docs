---
description: Contract that holds logic for vested MBRN in the builder's allocation
---

# Vesting

Builder's staked allocations can vote with a Governance determined % of their voting power and receive normal revenue minus **MBRN** inflationary rewards. Staking revenue needs to be claimed for the contract before being able to distribute to the receiver. Voting and proposal creation by allocation receivers is also done through this contract.

## InstantiateMsg

```
#[derive(Serialize, Deserialize, Clone, Debug, PartialEq, JsonSchema)]
pub struct InstantiateMsg {
    pub owner: Option<String>,
    pub initial_allocation: Uint128,
    pub pre_launch_contributors: String,
    pub mbrn_denom: String,
    pub osmosis_proxy: String,
    pub staking_contract: String,
}
```

| Key                       | Type    | Description                                          |
| ------------------------- | ------- | ---------------------------------------------------- |
| `*owner`                  | String  | Owner of contract                                    |
| `initial_allocation`      | Uint128 | Builder's Allocation                                 |
| `pre_launch_contributors` | String  | Address receiving pre-launch contributors allocation |
| `mbrn_denom`              | String  | MBRN token denom                                     |
| `osmosis_proxy`           | String  | Osmosis Proxy  contract address                      |
| `staking_contract`        | String  | MBRN Staking contract address                        |

&#x20;\* = optional

## ExecuteMsg

### `AddRecipient`

Add address eligible to receiver a MBRN allocation

```
#[derive(Serialize, Deserialize, Clone, Debug, PartialEq, JsonSchema)]
#[serde(rename_all = "snake_case")]
pub enum ExecuteMsg {
    AddRecipient {
        recipient: String,
    }
}
```

| Key         | Type   | Decription           |
| ----------- | ------ | -------------------- |
| `recipient` | String | Address of Recipient |

### `RemoveRecipient`

Remove Receiver and any allocations

```
#[derive(Serialize, Deserialize, Clone, Debug, PartialEq, JsonSchema)]
#[serde(rename_all = "snake_case")]
pub enum ExecuteMsg {
    RemoveRecipient {
        recipient: String,
    }
}
```

| Key         | Type   | Description          |
| ----------- | ------ | -------------------- |
| `recipient` | String | Address of Recipient |

### `AddAllocation`

Add MBRN allocation to an eligible Recipient

```
#[derive(Serialize, Deserialize, Clone, Debug, PartialEq, JsonSchema)]
#[serde(rename_all = "snake_case")]
pub enum ExecuteMsg {
    AddAllocation {
        recipient: String,
        allocation: Uint128,
        vesting_period: Option<VestingPeriod>,
    }
}

pub struct VestingPeriod {
    pub cliff: u64, //In days
    pub linear: u64, //In days
}
```

| Key               | Type          | Description                             |
| ----------------- | ------------- | --------------------------------------- |
| `recipient`       | String        | Address of Receiver                     |
| `allocation`      | Uint128       | Allocation amount                       |
| `*vesting_period` | VestingPeriod | VestingPeriod for receiver's allocation |

&#x20;\* = optional

### `WithdrawUnlocked`

Withdraw unlocked tokens for info.sender if address is a Receiver

```
#[derive(Serialize, Deserialize, Clone, Debug, PartialEq, JsonSchema)]
#[serde(rename_all = "snake_case")]
pub enum ExecuteMsg {
    WithdrawUnlocked { }
}
```

### `ClaimFeesForContract`

Claim staking liquidation fee rewards for the contract which distributes it to Receivers with an allocation&#x20;

```
#[derive(Serialize, Deserialize, Clone, Debug, PartialEq, JsonSchema)]
#[serde(rename_all = "snake_case")]
pub enum ExecuteMsg {
    ClaimFeesforContract { }
}
```

### `ClaimFeesForReceiver`

If info.sender is a Receiver, claim allocated rewards

```
#[derive(Serialize, Deserialize, Clone, Debug, PartialEq, JsonSchema)]
#[serde(rename_all = "snake_case")]
pub enum ExecuteMsg {
    ClaimFeesforContract { }
}
```

### `SubmitProposal`

Submit MBRN Governance proposal&#x20;

```
#[derive(Serialize, Deserialize, Clone, Debug, PartialEq, JsonSchema)]
#[serde(rename_all = "snake_case")]
pub enum ExecuteMsg {
    SubmitProposal {
        title: String,
        description: String,
        link: Option<String>,
        messages: Option<Vec<ProposalMessage>>,
        expedited: bool,
    }
}

pub struct ProposalMessage {
    /// Order of execution of the message
    pub order: Uint64,
    /// Execution message
    pub msg: CosmosMsg,
}
```

| Key           | Type                  | Description                   |
| ------------- | --------------------- | ----------------------------- |
| `title`       | String                | Proposal title                |
| `description` | String                | Proposal description          |
| `*link`       | String                | Proposal link                 |
| `*messages`   | Vec\<ProposalMessage> | Proposal executeable messages |
| `expedited`   | bool                  | Expedited Proposal toggle     |

&#x20;\* = optional

### `CastVote`

Vote for MBRN proposal

```
#[derive(Serialize, Deserialize, Clone, Debug, PartialEq, JsonSchema)]
#[serde(rename_all = "snake_case")]
pub enum ExecuteMsg {
    CastVote {
        proposal_id: u64,
        vote: ProposalVoteOption,
    }
}

pub enum ProposalVoteOption {
    For,
    Against,
}
```

| Key           | Type               | Description          |
| ------------- | ------------------ | -------------------- |
| `proposal_id` | u64                | Proposal identifier  |
| `vote`        | ProposalVoteOption | Proposal vote option |

### `UpdateConfig`

Update Config if contract owner

```
#[derive(Serialize, Deserialize, Clone, Debug, PartialEq, JsonSchema)]
#[serde(rename_all = "snake_case")]
pub enum ExecuteMsg {
    UpdateConfig {
        owner: Option<String>,
        mbrn_denom: Option<String>,
        osmosis_proxy: Option<String>,
        staking_contract: Option<String>,
        additional_allocation: Option<Uint128>,
    }
}
```

| Key                     | Type    | Description                                     |
| ----------------------- | ------- | ----------------------------------------------- |
| `*owner`                | String  | Contract owner                                  |
| `*mbrn_denom`           | String  | MBRN full denom                                 |
| `*osmosis_proxy`        | String  | Osmosis Proxy contract addr                     |
| `*staking_contract`     | String  | MBRN staking contract addr                      |
| `*addtional_allocation` | Uint128 | Increase contract's available allocation amount |

&#x20;\* = optional

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
    pub initial_allocation: Uint128,
    pub mbrn_denom: String,
    pub osmosis_proxy: Addr,
    pub staking_contract: Addr,
}
```

### `Recipients`

Returns list of Recipients

```
#[derive(Serialize, Deserialize, Clone, Debug, PartialEq, JsonSchema)]
#[serde(rename_all = "snake_case")]
pub enum QueryMsg {
    Recipients {}
}

//Vec<ReceiverResponse>
pub struct ReceiverResponse {
    pub recipient: String,
    pub allocation: Option<Allocation>,
    pub claimables: Vec<Asset>,
}
```

### `Allocation`

Returns allocation for a Recipient

```
#[derive(Serialize, Deserialize, Clone, Debug, PartialEq, JsonSchema)]
#[serde(rename_all = "snake_case")]
pub enum QueryMsg {
    Allocation {
        recipient: String,
    }
}

pub struct AllocationResponse {
    pub amount: String,
    pub amount_withdrawn: String,
    pub start_time_of_allocation: String, //block time of allocation in seconds
    pub vesting_period: VestingPeriod,  //In days
}
```

| Key         | Type   | Description       |
| ----------- | ------ | ----------------- |
| `recipient` | String | Recipient address |

### `UnlockedTokens`

Returns a Receiver's unlocked allocation

```
#[derive(Serialize, Deserialize, Clone, Debug, PartialEq, JsonSchema)]
#[serde(rename_all = "snake_case")]
pub enum QueryMsg {
    UnlockedTokens {
        recipient: String,
    }
}
```

| Key         | Type   | Description       |
| ----------- | ------ | ----------------- |
| `recipient` | String | Recipient address |

### `Recipient`

Returns a Recipient

```
#[derive(Serialize, Deserialize, Clone, Debug, PartialEq, JsonSchema)]
#[serde(rename_all = "snake_case")]
pub enum QueryMsg {
    Recipient {
        recipient: String,
    }
}

pub struct ReceiverResponse {
    pub recipient: String,
    pub allocation: Option<Allocation>,
    pub claimables: Vec<Asset>,
}
```

| Key         | Type   | Description       |
| ----------- | ------ | ----------------- |
| `recipient` | String | Recipient address |
