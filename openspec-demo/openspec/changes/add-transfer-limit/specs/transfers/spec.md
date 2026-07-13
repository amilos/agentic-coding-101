# Spec delta: transfers

Delta for the `add-transfer-limit` change. On `openspec archive`, the `ADDED`
requirements below are merged into the canonical `openspec/specs/transfers/`
capability.

## ADDED Requirements

### Requirement: Daily transfer limit enforcement

The system SHALL reject an outbound transfer when the total amount transferred
out of the source account on the current day, including the requested amount,
would exceed that account's `DailyTransferLimit`. A rejected transfer SHALL move
no funds and SHALL record no ledger entry.

#### Scenario: Transfer within the remaining daily allowance succeeds

- **GIVEN** account `A` has a `DailyTransferLimit` of 1,000 and a sufficient balance
- **AND** no transfers have been made out of `A` today
- **WHEN** a transfer of 400 is made from `A` to `B`
- **THEN** the transfer succeeds
- **AND** `A`'s balance is reduced by 400 and a ledger entry is recorded

#### Scenario: A transfer reaching the limit exactly is allowed

- **GIVEN** account `A` has a `DailyTransferLimit` of 1,000 and a sufficient balance
- **AND** no transfers have been made out of `A` today
- **WHEN** a transfer of exactly 1,000 is made from `A` to `B`
- **THEN** the transfer succeeds

#### Scenario: A transfer exceeding the limit is rejected

- **GIVEN** account `A` has a `DailyTransferLimit` of 1,000 and a sufficient balance
- **AND** no transfers have been made out of `A` today
- **WHEN** a transfer of 1,000.01 is made from `A` to `B`
- **THEN** the transfer is rejected with reason "Daily transfer limit exceeded"
- **AND** `A`'s and `B`'s balances are unchanged
- **AND** no ledger entry is recorded

#### Scenario: Cumulative transfers that cross the limit are rejected at the breach

- **GIVEN** account `A` has a `DailyTransferLimit` of 1,000 and a sufficient balance
- **AND** a transfer of 700 out of `A` has already succeeded today
- **WHEN** a further transfer of 400 is made from `A` to `B`
- **THEN** the further transfer is rejected with reason "Daily transfer limit exceeded"
- **AND** the earlier 700 transfer still stands

#### Scenario: Prior-day transfers do not count toward today's allowance

- **GIVEN** account `A` has a `DailyTransferLimit` of 1,000
- **AND** a transfer of 900 out of `A` succeeded yesterday
- **WHEN** a transfer of 900 is made from `A` to `B` today
- **THEN** the transfer succeeds

#### Scenario: An account with a zero limit cannot transfer out

- **GIVEN** account `A` has a `DailyTransferLimit` of 0
- **WHEN** a transfer of any positive amount is made from `A` to `B`
- **THEN** the transfer is rejected with reason "Daily transfer limit exceeded"
