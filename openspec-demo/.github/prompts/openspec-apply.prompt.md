---
name: openspec-apply
description: Implement an approved OpenSpec change (apply step) against its spec deltas and tasks.
---

You are running the OpenSpec **apply** step.

Input: the change id to implement (e.g. `add-transfer-limit`).

Do the following:

1. Read the change folder `openspec/changes/<change-id>/`:
   `proposal.md`, `design.md`, `tasks.md`, and the spec delta under `specs/`.
2. Treat `specs/<capability>/spec.md` as the **acceptance criteria**. Every
   `#### Scenario:` must hold once you are done.
3. Work through `tasks.md` in order, implementing the code changes described.
   Tick each checkbox (`- [ ]` → `- [x]`) as you complete it.
4. Add or update tests so each scenario in the spec delta is covered.
5. Respect the NorthBank conventions in `openspec/project.md` (money is
   `decimal`; validate before mutating; no PII in logs).
6. **Show the diff before applying**, then run the test suite.

Do not change the scope agreed in `proposal.md`. If you discover the spec is
wrong or incomplete, stop and flag it rather than silently widening the change.

Finish by reporting which tasks are done, the test result, and any spec gaps
found.
