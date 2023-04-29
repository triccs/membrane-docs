---
description: Membrane Governance contract
---

# Governance

* Governance can execute any arbitrary message whose proposals have a minimum voting period of 7 days
* &#x20;Voting power per proposal is based on power when the proposal was created.
* Expedited Proposalscan be called by Addresses w/ a [vested allocation ](vesting.md)& should be used only for time-sensitive emergencies

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
        //If from the builder's contract
        receiver: Option<String>,
        expedited: bool,
    }
}
```

| Key           | Type                  | Description                                      |
| ------------- | --------------------- | ------------------------------------------------ |
| `title`       | String                | Proposal title                                   |
| `description` | String                | Proposal description                             |
| `*link`       | String                | Proposal whitelisted link                        |
| `messages`    | Vec\<ProposalMessage> | Proposal executeble messages                     |
| `receiver`    | String                | If from Builder's contract, add Receiver address |
| `expedited`   | bool                  | Expedited Proposal toggle                        |

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
}
```

| Key           | Type               | Description                                      |
| ------------- | ------------------ | ------------------------------------------------ |
| `proposal_id` | u64                | Proposal identifier                              |
| `vote`        | ProposalVoteOption | Vote Option                                      |
| `*receiver`   | String             | If from Builder's contract, add Receiver address |

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
    }
}

//If passed you'll get returned 'ContractError::MessagesCheckPassed {}'
#[error("Messages check passed. Nothing was committed to the blockchain")]
    MessagesCheckPassed {},
```

| Key        | Type                  | Description       |
| ---------- | --------------------- | ----------------- |
| `messages` | Vec\<ProposalMessage> | Messages to check |

### `CheckMessagesPassed`

The last endpoint which is executed only if all proposal messages have been passed. Called by the contract at the end of CheckMessages.

```
#[derive(Serialize, Deserialize, Clone, Debug, PartialEq, JsonSchema)]
#[serde(rename_all = "snake_case")]
pub enum ExecuteMsg {
    CheckMessagesPassed {}
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

| Key                                 | Type         | Description                             |
| ----------------------------------- | ------------ | --------------------------------------- |
| `*mbrn_denom`                       | String       | MBRN native token fulldenom             |
| `*minimum_total_stake`              | Uint128      | Minimum total stake to submit proposals |
| `*staking_contract_addr`            | String       | MBRN staking contract                   |
| `*vesting_contract_addr`            | String       | Vesting contract address                |
| `*vesting_voting_power_multipler`   | Decimal      | Vesting voting power ratio              |
| `*proposal_voting_period`           | u64          | Proposal voting period                  |
| `*expedited_proposal_voting_period` | u64          | Expedited Proposal voting period        |
| `*proposal_effective_delay`         | u64          | Proposal effective delay                |
| `*proposal_expiration_period`       | u64          | Proposal expiration period              |
| `*proposal_required_stake`          | u128         | Proposal required stake                 |
| `*proposal_required_quorum`         | String       | Proposal required quorum                |
| `*proposal_required_threshold`      | String       | Proposal required threshold             |
| `*whitelist_remove`                 | Vec\<String> | Links to remove from whitelist          |
| `*whitelist_add`                    | Vec\<String> | Links to add to whitelist               |

&#x20;\* = optional

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
    pub proposal_id: Uint64,
    pub submitter: Addr,
    pub status: ProposalStatus,
    pub for_power: Uint128,
    pub against_power: Uint128,
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

| Key      | Type | Description                       |
| -------- | ---- | --------------------------------- |
| `*start` | u64  | Id from which to start querying   |
| `*limit` | u32  | The amount of proposals to return |

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
    pub proposal_id: Uint64,
    pub submitter: Addr,
    pub status: ProposalStatus,
    pub for_power: Uint128,
    pub against_power: Uint128,
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
