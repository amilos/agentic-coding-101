# Change: add-transfer-limit

## Why

Each NorthBank `Account` carries a `DailyTransferLimit`, but
`TransferService.Transfer` never checks it. A customer (or a compromised
session) can drain an account in many transfers within a single day, exceeding
the agreed limit. This is a compliance and fraud-control gap.

## What changes

Enforce the per-account daily transfer limit inside `TransferService.Transfer`:

- Before moving any funds, sum the amounts already transferred **out of** the
  source account **today**, add the requested amount, and reject the transfer if
  the total would exceed `fromAccount.DailyTransferLimit`.
- A rejected transfer moves no money and records no ledger entry.
- The rejection is returned via `TransferResult` (a failed result with a clear
  reason), not thrown as an unhandled exception.

## Scope

- **In scope:** the outbound daily limit on the *source* account; a ledger
  helper to sum today's outbound transfers; xUnit coverage.
- **Out of scope:** inbound/receive limits, per-transaction maximums, currency
  conversion, and the separate non-positive-amount bug (tracked as issue #2).

## Impact

- **Specs:** adds requirements under `specs/transfers/`.
- **Code:** `TransferService.cs` (the check), `Ledger.cs` (sum-today helper if
  not already present).
- **Tests:** `tests/PaymentService.Tests/TransferServiceTests.cs` (new cases).
- **Behaviour:** transfers that would breach the daily limit now fail cleanly;
  existing within-limit transfers are unaffected.
