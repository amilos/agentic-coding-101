---
name: openspec-archive
description: Archive a completed OpenSpec change (archive step) — fold deltas into specs and move the change to archive.
---

You are running the OpenSpec **archive** step.

Input: the change id to archive (e.g. `add-transfer-limit`).

Only archive a change that has been **applied and merged**. Then:

1. Read `openspec/changes/<change-id>/specs/` and merge each spec delta into the
   canonical living specification under `openspec/specs/`:
   - `## ADDED Requirements` become permanent requirements in the matching
     `openspec/specs/<capability>/spec.md`.
   - `## MODIFIED Requirements` replace their prior versions.
2. Move the entire `openspec/changes/<change-id>/` folder to
   `openspec/changes/archive/<YYYY-MM-DD>-<change-id>/`, preserving its
   proposal, design, tasks, and deltas for history.
3. Ensure `openspec/changes/` now contains only in-flight changes.

Finish by summarising which requirements were folded into which capability and
the archive path the change was moved to.
