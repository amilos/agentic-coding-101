# Archived changes

This folder holds changes that have been **applied, merged, and archived**.

## What `openspec archive` does

Running:

```bash
openspec archive add-transfer-limit
```

after a change is merged performs two things:

1. **Folds the spec deltas into the living specification.** The `ADDED` /
   `MODIFIED` requirements from
   `openspec/changes/add-transfer-limit/specs/transfers/spec.md` are merged into
   the canonical capability at `openspec/specs/transfers/`, so the daily-limit
   rule becomes part of NorthBank's permanent, always-true spec — no longer a
   pending proposal.

2. **Moves the change out of the active set.** The whole
   `openspec/changes/add-transfer-limit/` folder is relocated here with a date
   prefix, e.g.:

   ```
   openspec/changes/archive/2026-07-01-add-transfer-limit/
   ```

This keeps `openspec/changes/` showing only *in-flight* work, while preserving
the full history (proposal, design, tasks, deltas) of every shipped change for
audit and onboarding.

> In this demo the archive is empty because `add-transfer-limit` is still shown
> as an active, worked example. During a live session, archiving it would create
> the dated folder above and update `openspec/specs/transfers/`.
