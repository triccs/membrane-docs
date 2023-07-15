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

<table><thead><tr><th width="232">Key</th><th>Type</th><th>Description</th></tr></thead><tbody><tr><td><code>*owner</code></td><td>String</td><td>Owner of contract</td></tr><tr><td><code>initial_allocation</code></td><td>Uint128</td><td>Builder's Allocation</td></tr><tr><td><code>pre_launch_contributors</code></td><td>String</td><td>Address receiving pre-launch contributors allocation</td></tr><tr><td><code>mbrn_denom</code></td><td>String</td><td>MBRN token denom</td></tr><tr><td><code>osmosis_proxy</code></td><td>String</td><td>Osmosis Proxy  contract address</td></tr><tr><td><code>staking_contract</code></td><td>String</td><td>MBRN Staking contract address</td></tr></tbody></table>

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

<table><thead><tr><th width="197">Key</th><th>Type</th><th>Description</th></tr></thead><tbody><tr><td><code>recipient</code></td><td>String</td><td>Address of Receiver</td></tr><tr><td><code>allocation</code></td><td>Uint128</td><td>Allocation amount</td></tr><tr><td><code>*vesting_period</code></td><td>VestingPeriod</td><td>VestingPeriod for receiver's allocation</td></tr></tbody></table>

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

<table><thead><tr><th>Key</th><th width="188">Type</th><th>Description</th></tr></thead><tbody><tr><td><code>proposal_id</code></td><td>u64</td><td>Proposal identifier</td></tr><tr><td><code>vote</code></td><td>ProposalVoteOption</td><td>Proposal vote option</td></tr></tbody></table>

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
