---
description: >-
  Users can deposit collateral into Membrane’s vault mechanism and mint
  Collateralized Debt Tokens (CDT)
---

# Overview

The mechanism is roughly analogous to a “Line of Credit”, wherein vault owners can deposit their collateral to receive a line of credit against it. This unique functionality enables a large amount of flexibility in otherwise rigid token positions.

### Protocol Functions

The Vault mechanism is composed of 5 primary parts: **Deposit**, **Withdraw**, **Mint**, **Repay**, and **Liquidate**.\
\
In the **Deposit** stage, anyone can deposit their collateral into the protocol, represented as a “bundle”. Bundling assets uses their average interest rate and “Loan to Value” (LTV) in proportion, giving vaults the ability to mitigate volatile asset risk.

<figure><img src="../../.gitbook/assets/image (7).png" alt=""><figcaption><p>With bundled positions, the minter avoids liquidation of both assets</p></figcaption></figure>

Vault owners can **Withdraw** collateral limited by the borrowable LTV

In the **Mint** stage, the owner can mint CDT up to the borrowable LTV

Anyone can **Repay** any outstanding CDT loans

If the LTV ratio exceeds maximum LTV (meaning there isn’t enough collateral to safely guarantee the backed value of the CDT assets), the vault will be **Liquidated**.
