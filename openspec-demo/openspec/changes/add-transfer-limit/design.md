# Design: add-transfer-limit

## Context

`TransferService.Transfer(fromId, toId, amount)` currently: resolves both
accounts, checks the source has sufficient balance, moves the money, and appends
a `LedgerEntry`. It never consults `Account.DailyTransferLimit`. We need to add a
**cumulative daily** check on outbound transfers from the source account.

"Daily" means the calendar day of the transfer's timestamp (server local date
for the demo). The limit is per source account, taken from
`fromAccount.DailyTransferLimit`.

## Approach

Add the check at the point of validation, before any state changes, so a
rejected transfer is a clean no-op.

```
today       = DateOnly.FromDateTime(clock.Now)          // demo: DateTime.Today
alreadyOut  = ledger.SumTransfersOutOn(fromId, today)   // sum of prior debits today
if (alreadyOut + amount > fromAccount.DailyTransferLimit)
    return TransferResult.Failed("Daily transfer limit exceeded");
// ...existing balance check + move + ledger append unchanged...
```

The ledger owns the summation because it owns the entries; `TransferService`
stays a thin policy layer.

## Decisions

- **Boundary is inclusive.** Reaching the limit exactly is allowed; only
  *exceeding* it (`>`) is rejected. So a fresh account with limit 1,000 may
  transfer exactly 1,000 in a day.
- **Cumulative, not per-transaction.** We sum *today's* outbound total, not just
  the single amount, so many small transfers can still breach the limit.
- **Source account only.** The limit constrains money leaving an account.
  Inbound limits are out of scope.
- **Return, don't throw.** Consistent with the existing `TransferResult` pattern;
  callers already branch on success/failure.
- **Ordering.** The daily-limit check runs before the balance check (both before
  any mutation); either failing is a no-op, so relative order only affects which
  reason wins. We check the limit first so a limit breach is reported even when
  the balance is also insufficient.

## Edge cases

- Timestamps from a previous day must not count toward today's total.
- An account whose `DailyTransferLimit` is 0 can make no outbound transfers.
- The non-positive-amount defect (issue #2) is intentionally **not** fixed here;
  keep this change focused on the limit so the two demos stay crisp.

## Alternatives considered

- *Enforce in `InMemoryLedger` on append* — rejected; couples policy to storage
  and can't produce a `TransferResult`.
- *Track a running daily total on `Account`* — rejected; needs a reset job and
  drifts from the ledger, which is the source of truth.
