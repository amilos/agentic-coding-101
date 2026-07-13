# Tasks: add-transfer-limit

Ticked off during `openspec apply`.

## 1. Ledger support
- [ ] 1.1 Ensure `ILedger` exposes a method to sum the total amount transferred
      **out of** a given account **on a given day** (e.g.
      `decimal SumTransfersOutOn(string accountId, DateOnly day)`).
- [ ] 1.2 Implement it in `InMemoryLedger` over the `LedgerEntry` list, matching
      on `from == accountId` and `timestamp.Date == day`.

## 2. Enforce the limit in TransferService
- [ ] 2.1 In `Transfer`, after resolving the source account and before moving
      funds, compute `alreadyToday = ledger.SumTransfersOutOn(fromId, today)`.
- [ ] 2.2 If `alreadyToday + amount > fromAccount.DailyTransferLimit`, return a
      failed `TransferResult` with reason "Daily transfer limit exceeded" and
      make **no** balance change and **no** ledger entry.
- [ ] 2.3 Otherwise proceed with the existing transfer logic unchanged.
- [ ] 2.4 Do not log account ids, owner names, or emails in the rejection path.

## 3. Tests (xUnit)
- [ ] 3.1 Transfer within the remaining daily allowance succeeds.
- [ ] 3.2 A single transfer exactly equal to the limit succeeds (boundary).
- [ ] 3.3 A transfer that would exceed the limit is rejected; balances and
      ledger are unchanged.
- [ ] 3.4 Cumulative transfers that cross the limit part-way are rejected at the
      breaching transfer, and earlier ones stand.
- [ ] 3.5 Transfers "yesterday" do not count against "today's" allowance.

## 4. Verify
- [ ] 4.1 `dotnet test` green.
- [ ] 4.2 `openspec validate add-transfer-limit` passes.
