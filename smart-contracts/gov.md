---
description: Membrane Governance contract
---

# Governance

* Governance can execute any arbitrary message whose proposals have a minimum voting period of 7 days
* &#x20;Voting power per proposal is based on power when the proposal was created.
* Expedited Proposals can be called by Addresses w/ a [vested allocation ](vesting.md)& should be used only for time-sensitive emergencies
* Proposals can be aligned with by stakers to pass the active threshold, any pending proposal can be deleted within 1 day
* Staker's vote doesn't override the Delegate's vote&#x20;

## InstantiateMsg

```
#[derive(Serialize, Deserialize, Clone, Debug, PartialEq, JsonSchema)]
pub struct InstantiateMsg {
    pub mbrn_staking_contract: String,
    pub vesting_contract_addr: String,
    pub vesting_voting_power_multiplier: Decimal,
    pub proposal_voting_period: u64,
    pub expedited_proposal_voting_period: u64,
    pub proposal_effective_delay: u64,
    pub proposal_expiration_period: u64,
    pub proposal_required_stake: Uint128,
    pub proposal_required_quorum: String,
    pub proposal_required_threshold: String,
    pub whitelisted_links: Vec<String>,
}

```

| Key                                | Type         | Description                      |
| ---------------------------------- | ------------ | -------------------------------- |
| `mbrn_staking_contract`            | String       | MBRN staking contract            |
| `vesting_contract_addr`            | String       | Vesting contract                 |
| `vesting_voting_power_multipler`   | Decimal      | Vesting voting power ratio       |
| `proposal_voting_period`           | u64          | Proposal voting period           |
| `expedited_proposal_voting_period` | u64          | Expedited Proposal voting period |
| `proposal_effective_delay`         | u64          | Proposal effective delay         |
| `proposal_expiration_period`       | u64          | Proposal expiration period       |
| `proposal_required_stake`          | Uint128      | Proposal required stake          |
| `proposal_required_quorum`         | String       | Proposal required quorum         |
| `proposal_required_threshold`      | String       | Proposal required threshold      |
| `whitelisted_links`                | Vec\<String> | Whitelisted links                |

## ExecuteMsg

### `SubmitProposal`

Submit a new proposal in the Governance contract

```
#[derive(Serialize, Deserialize, Clone, Debug, PartialEq, JsonSchema)]
#[serde(rename_all = "snake_case")]
pub enum ExecuteMsg {
    SubmitProposal {
        title: String,
        description: String,
        link: Option<String>,
        messages: Option<Vec<ProposalMessage>>,
        receiver: Option<String>,
        expedited: bool,
    }
}
```

| Key           | Type                  | Description                                    |
| ------------- | --------------------- | ---------------------------------------------- |
| `title`       | String                | Proposal title                                 |
| `description` | String                | Proposal description                           |
| `*link`       | String                | Proposal whitelisted link                      |
| `messages`    | Vec\<ProposalMessage> | Proposal executeble messages                   |
| `receiver`    | String                | If from vesting contract, add Receiver address |
| `expedited`   | bool                  | Expedited Proposal toggle                      |

&#x20;\* = optional

### `CastVote`

Cast vote on an active proposal

```
#[derive(Serialize, Deserialize, Clone, Debug, PartialEq, JsonSchema)]
#[serde(rename_all = "snake_case")]
pub enum ExecuteMsg {
    CastVote {
        proposal_id: u64,
        vote: ProposalVoteOption,
        receiver: Option<String>,
    }
}

pub enum ProposalVoteOption {
    For,
    Against,
    Amend, 
    Remove,
    Align,
}
```

| Key           | Type               | Description                                    |
| ------------- | ------------------ | ---------------------------------------------- |
| `proposal_id` | u64                | Proposal identifier                            |
| `vote`        | ProposalVoteOption | Vote Option                                    |
| `*receiver`   | String             | If from vesting contract, add Receiver address |

&#x20;\* = optional

### `EndProposal`

Set the status of a proposal that has expired

```
#[derive(Serialize, Deserialize, Clone, Debug, PartialEq, JsonSchema)]
#[serde(rename_all = "snake_case")]
pub enum ExecuteMsg {
    EndProposal {
        proposal_id: u64,
    }
}
```

| Key           | Type | Description         |
| ------------- | ---- | ------------------- |
| `proposal_id` | u64  | Proposal Identifier |

### `CheckMessages`

Check messages execution

```
#[derive(Serialize, Deserialize, Clone, Debug, PartialEq, JsonSchema)]
#[serde(rename_all = "snake_case")]
pub enum ExecuteMsg {
    CheckMessages {
        messages: Vec<ProposalMessage>,
        msg_switch: Option<u64>
    }
}

//If passed you'll get returned 'ContractError::MessagesCheckPassed {}'
#[error("Messages check passed. Nothing was committed to the blockchain")]
MessagesCheckPassed {}
```

| Key           | Type                  | Description                                                                     |
| ------------- | --------------------- | ------------------------------------------------------------------------------- |
| `messages`    | Vec\<ProposalMessage> | Messages to check                                                               |
| `*msg_switch` | u64                   | Switch to choose executable msgs to check after the above messages are executed |

&#x20;\* = optional

### `CheckMessagesPassed`

The last endpoint which is executed only if all proposal messages have been passed. Called by the contract at the end of CheckMessages.

```
#[derive(Serialize, Deserialize, Clone, Debug, PartialEq, JsonSchema)]
#[serde(rename_all = "snake_case")]
pub enum ExecuteMsg {
    CheckMessagesPassed { error: Option<bool> }
}
```

### `ExecuteProposal`

Execute a successful proposal

```
#[derive(Serialize, Deserialize, Clone, Debug, PartialEq, JsonSchema)]
#[serde(rename_all = "snake_case")]
pub enum ExecuteMsg {
    ExecuteProposal{
        proposal_id: u64,
    }
}
```

| Key           | Type | Description         |
| ------------- | ---- | ------------------- |
| `proposal_id` | u64  | Proposal Identifier |

### `RemoveCompletedProposal`

Remove a proposal that was already executed (or failed/expired)

```
#[derive(Serialize, Deserialize, Clone, Debug, PartialEq, JsonSchema)]
#[serde(rename_all = "snake_case")]
pub enum ExecuteMsg {
    RemoveCompletedProposal {
        proposal_id: u64,
    }
}
```

| Key           | Type | Description         |
| ------------- | ---- | ------------------- |
| `proposal_id` | u64  | Proposal Identifier |

### `UpdateConfig`

Update parameters in the Governance contract. Only the Governance contract is allowed to update its own parameters through a successful proposal.

```
#[derive(Serialize, Deserialize, Clone, Debug, PartialEq, JsonSchema)]
#[serde(rename_all = "snake_case")]
pub enum ExecuteMsg {
    UpdateConfig( UpdateConfig )
}

#[derive(Serialize, Deserialize, Clone, Debug, PartialEq, JsonSchema)]
pub struct UpdateConfig {
    pub mbrn_denom: Option<String>,
    pub minimum_total_stake: Option<Uint128>,
    pub staking_contract: Option<String>,
    pub vesting_contract_addr: Option<String>,
    pub vesting_voting_power_multiplier: Option<Decimal>,
    pub proposal_voting_period: Option<u64>,
    pub expedited_proposal_voting_period: Option<u64>,
    pub proposal_effective_delay: Option<u64>,
    pub proposal_expiration_period: Option<u64>,
    pub proposal_required_stake: Option<u128>,
    pub proposal_required_quorum: Option<String>,
    pub proposal_required_threshold: Option<String>,
    pub whitelist_remove: Option<Vec<String>>,
    pub whitelist_add: Option<Vec<String>>,
}
```

<table><thead><tr><th width="233">Key</th><th>Type</th><th>Description</th></tr></thead><tbody><tr><td><code>*mbrn_denom</code></td><td>String</td><td>MBRN native token fulldenom</td></tr><tr><td><code>*minimum_total_stake</code></td><td>Uint128</td><td>Minimum total stake to submit proposals</td></tr><tr><td><code>*staking_contract_addr</code></td><td>String</td><td>MBRN staking contract</td></tr><tr><td><code>*vesting_contract_addr</code></td><td>String</td><td>Vesting contract address</td></tr><tr><td><code>*vesting_voting_power_multipler</code></td><td>Decimal</td><td>Vesting voting power ratio</td></tr><tr><td><code>*proposal_voting_period</code></td><td>u64</td><td>Proposal voting period</td></tr><tr><td><code>*expedited_proposal_voting_period</code></td><td>u64</td><td>Expedited Proposal voting period</td></tr><tr><td><code>*proposal_effective_delay</code></td><td>u64</td><td>Proposal effective delay</td></tr><tr><td><code>*proposal_expiration_period</code></td><td>u64</td><td>Proposal expiration period</td></tr><tr><td><code>*proposal_required_stake</code></td><td>u128</td><td>Proposal required stake</td></tr><tr><td><code>*proposal_required_quorum</code></td><td>String</td><td>Proposal required quorum</td></tr><tr><td><code>*proposal_required_threshold</code></td><td>String</td><td>Proposal required threshold</td></tr><tr><td><code>*whitelist_remove</code></td><td>Vec&#x3C;String></td><td>Links to remove from whitelist</td></tr><tr><td><code>*whitelist_add</code></td><td>Vec&#x3C;String></td><td>Links to add to whitelist</td></tr></tbody></table>

&#x20;\* = optional

### `CreateOsmosisGauge`

Use osmosis-std to create a gauge with assets in this contract

```
#[derive(Serialize, Deserialize, Clone, Debug, PartialEq, JsonSchema)]
#[serde(rename_all = "snake_case")]
pub enum ExecuteMsg {
    CreateOsmosisGauge {
        gauge_msg: MsgCreateGauge
    }
}

pub struct MsgCreateGauge {
    pub is_perpetual: bool,
    pub owner: ::prost::alloc::string::String,
    pub distribute_to: ::core::option::Option<super::lockup::QueryCondition>,
    pub coins: ::prost::alloc::vec::Vec<super::super::cosmos::base::v1beta1::Coin>,
    pub start_time: ::core::option::Option<crate::shim::Timestamp>,
    pub num_epochs_paid_over: u64,
    pub pool_id: u64,
}

pub struct QueryCondition {
    /// LockQueryType is a type of lock query, ByLockDuration | ByLockTime
    pub lock_query_type: i32,
    /// Denom represents the token denomination we are looking to lock up
    pub denom: ::prost::alloc::string::String,
    /// Duration is used to query locks with longer duration than the specified
    /// duration. Duration field must not be nil when the lock query type is
    /// `ByLockDuration`.
    pub duration: ::core::option::Option<crate::shim::Duration>,
    /// Timestamp is used by locks started before the specified duration.
    /// Timestamp field must not be nil when the lock query type is `ByLockTime`.
    /// Querying locks with timestamp is currently not implemented.
    pub timestamp: ::core::option::Option<crate::shim::Timestamp>,
}
pub struct Duration {
    pub seconds: i64,
    pub nanos: i32,
}
pub struct Timestamp {
    pub seconds: i64,
    pub nanos: i32,
}

pub struct Coin {
    pub denom: ::prost::alloc::string::String,
    pub amount: ::prost::alloc::string::String,
}
```

| Key         | Type           | Description                    |
| ----------- | -------------- | ------------------------------ |
| `gauge_msg` | MsgCreateGauge | Osmosis-std gauge creation msg |

### `AddToOsmosisGauge`

Use osmosis-std to add to a gauge, owned by this contract, with assets in this contract

```
#[derive(Serialize, Deserialize, Clone, Debug, PartialEq, JsonSchema)]
#[serde(rename_all = "snake_case")]
pub enum ExecuteMsg {
    AddToOsmosisGauge {
        gauge_msg: MsgAddToGauge 
    }
}

pub struct MsgAddToGauge {
    pub owner: ::prost::alloc::string::String,
    pub gauge_id: u64,
    pub rewards: ::prost::alloc::vec::Vec<super::super::cosmos::base::v1beta1::Coin>,
}
pub struct Coin {
    pub denom: ::prost::alloc::string::String,
    pub amount: ::prost::alloc::string::String,
}
```

| Key         | Type          | Description                  |
| ----------- | ------------- | ---------------------------- |
| `gauge_msg` | MsgAddToGauge | Osmosis-std add to gauge msg |

## QueryMsg

### `Config`

Return the contract's configuration

```
#[derive(Serialize, Deserialize, Clone, Debug, PartialEq, JsonSchema)]
#[serde(rename_all = "snake_case")]
pub enum QueryMsg {
    Config {}
}

#[derive(Serialize, Deserialize, Clone, Debug, PartialEq, JsonSchema)]
pub struct Config {
    pub mbrn_denom: String,
    pub minimum_total_stake: Uint128,
    pub staking_contract_addr: Addr,
    pub vesting_contract_addr: Addr,
    pub vesting_voting_power_multiplier: Decimal,
    pub proposal_voting_period: u64,
    pub expedited_proposal_voting_period: u64,
    pub proposal_effective_delay: u64,
    pub proposal_expiration_period: u64,
    pub proposal_required_stake: Uint128,
    pub proposal_required_quorum: Decimal,
    pub proposal_required_threshold: Decimal,
    pub whitelisted_links: Vec<String>,
}

```

### `PendingProposals`

Return the current list of pending proposals

```
#[derive(Serialize, Deserialize, Clone, Debug, PartialEq, JsonSchema)]
#[serde(rename_all = "snake_case")]
pub enum QueryMsg {
    PendingProposals {
        start: Option<u64>,
        limit: Option<u32>,
    }
}

pub struct ProposalResponse {
    pub voting_power: Vec<(Addr, Uint128)>,
    pub proposal_id: Uint64,
    pub submitter: Addr,
    pub status: ProposalStatus,
    pub aligned_power: Uint128,
    pub for_power: Uint128,
    pub against_power: Uint128,
    pub amendment_power: Uint128,
    pub removal_power: Uint128,
    pub start_block: u64,
    pub start_time: u64,
    pub end_block: u64,
    pub delayed_end_block: u64,
    pub expiration_block: u64,
    pub title: String,
    pub description: String,
    pub messages: Option<Vec<ProposalMessage>>,
    pub link: Option<String>,
}
```

| Key      | Type | Description                     |
| -------- | ---- | ------------------------------- |
| `*start` | u64  | Id from which to start querying |
| `*limit` | u32  | Response Limiter                |

### `Proposals`

Return the current list of proposals

```
#[derive(Serialize, Deserialize, Clone, Debug, PartialEq, JsonSchema)]
#[serde(rename_all = "snake_case")]
pub enum QueryMsg {
    Proposals {
        start: Option<u64>,
        limit: Option<u32>,
    }
}

pub struct ProposalResponse {
    pub voting_power: Vec<(Addr, Uint128)>,
    pub proposal_id: Uint64,
    pub submitter: Addr,
    pub status: ProposalStatus,
    pub aligned_power: Uint128,
    pub for_power: Uint128,
    pub against_power: Uint128,
    pub amendment_power: Uint128,
    pub removal_power: Uint128,
    pub start_block: u64,
    pub start_time: u64,
    pub end_block: u64,
    pub delayed_end_block: u64,
    pub expiration_block: u64,
    pub title: String,
    pub description: String,
    pub messages: Option<Vec<ProposalMessage>>,
    pub link: Option<String>,
}
```

| Key      | Type | Description                     |
| -------- | ---- | ------------------------------- |
| `*start` | u64  | Id from which to start querying |
| `*limit` | u32  | Response Limiter                |

&#x20;\* = optional

### `ProposalVoters`

Return proposal voters of specified proposal

```
#[derive(Serialize, Deserialize, Clone, Debug, PartialEq, JsonSchema)]
#[serde(rename_all = "snake_case")]
pub enum QueryMsg {
    ProposalVoters {
        proposal_id: u64,
        vote_option: ProposalVoteOption,
        start: Option<u64>,
        limit: Option<u32>,
        specific_user: Option<String>,
    }
}

Returns Vec<Addr>
```

| Key              | Type               | Description                            |
| ---------------- | ------------------ | -------------------------------------- |
| `proposal_id`    | u64                | Proposal unique id                     |
| `vote_option`    | ProposalVoteOption | Proposal vote option                   |
| `*start`         | u64                | Id from which to start querying        |
| `*limit`         | u32                | The amount of proposals to return      |
| `*specific_user` | String             | Specific user to query vote option for |

&#x20;\* = optional

### `Proposal`

Return information about a specific proposal

```
#[derive(Serialize, Deserialize, Clone, Debug, PartialEq, JsonSchema)]
#[serde(rename_all = "snake_case")]
pub enum QueryMsg {
    Proposal { proposal_id: u64 }
}

pub struct ProposalResponse {
    pub voting_power: Vec<(Addr, Uint128)>,
    pub proposal_id: Uint64,
    pub submitter: Addr,
    pub status: ProposalStatus,
    pub aligned_power: Uint128,
    pub for_power: Uint128,
    pub against_power: Uint128,
    pub amendment_power: Uint128,
    pub removal_power: Uint128,
    pub start_block: u64,
    pub start_time: u64,
    pub end_block: u64,
    pub delayed_end_block: u64,
    pub expiration_block: u64,
    pub title: String,
    pub description: String,
    pub messages: Option<Vec<ProposalMessage>>,
    pub link: Option<String>,
}
```

| Key           | Type | Description        |
| ------------- | ---- | ------------------ |
| `proposal_id` | u64  | Proposal unique id |

### `ProposalVotes`

Return information about the votes cast on a specific proposal

```
#[derive(Serialize, Deserialize, Clone, Debug, PartialEq, JsonSchema)]
#[serde(rename_all = "snake_case")]
pub enum QueryMsg {
    ProposalVotes { proposal_id: u64 }
}

pub struct ProposalVotesResponse {
    pub proposal_id: u64,
    pub for_power: Uint128,
    pub against_power: Uint128,
    pub amendment_power: Uint128,
    pub removal_power: Uint128,
    pub aligned_power: Uint128,
}
```

| Key           | Type | Description        |
| ------------- | ---- | ------------------ |
| `proposal_id` | u64  | Proposal unique id |

### `UserVotingPower`

Return user voting power for a specific proposal

```
#[derive(Serialize, Deserialize, Clone, Debug, PartialEq, JsonSchema)]
#[serde(rename_all = "snake_case")]
pub enum QueryMsg {
    UserVotingPower { 
        user: String, 
        proposal_id: u64, 
        builders: bool, 
    }
}

Returns Uint128
```

| Key           | Type   | Description                                   |
| ------------- | ------ | --------------------------------------------- |
| `user`        | String | User's voting power to query                  |
| `proposal_id` | u64    | Proposal's unique id                          |
| `builders`    | bool   | If user is a receiver from Builder's contract |

### `TotalVotingPower`

Return total voting power for a specific proposal

```
#[derive(Serialize, Deserialize, Clone, Debug, PartialEq, JsonSchema)]
#[serde(rename_all = "snake_case")]
pub enum QueryMsg {
    TotalVotingPower { proposal_id: u64 }
}

Returns Uint128
```

| Key           | Type | Description        |
| ------------- | ---- | ------------------ |
| `proposal_id` | u64  | Proposal unique id |
