# OpenSpec demo — spec-driven development for NorthBank

This is the **advanced-topic** asset for *Agentic Coding 101*. It shows how
[OpenSpec](https://github.com/Fission-AI/OpenSpec) drives a change through a
lightweight, spec-first workflow, worked end-to-end for **ISSUES.md issue #1 —
"Enforce daily transfer limit in TransferService"** (the `add-transfer-limit`
change).

> Assumption note: this folder is a faithful *approximation* of OpenSpec's real
> layout, built to teach the flow without requiring a live install. Command and
> package names below were confirmed against the repo at build time
> (`@fission-ai/openspec`, `openspec init/update`, propose/apply/archive). If a
> detail differs in the version you install, trust the repo — see
> <https://github.com/Fission-AI/OpenSpec>.

---

## What OpenSpec is

OpenSpec is a **lightweight, spec-driven development** framework. Instead of
prompting an agent straight into code, you first agree on *what should change*
in a small set of markdown artifacts, then let the agent implement against that
agreed spec, then fold the result back into your living specification.

It is **brownfield-first**: it assumes you already have a codebase (like the
NorthBank `PaymentService`) and want to make one well-scoped change at a time.

Everything lives under an `openspec/` directory as plain markdown — reviewable
in a normal PR, diffable, no database.

---

## Install

OpenSpec ships as an npm package. Install it globally:

```bash
npm i -g @fission-ai/openspec
```

or run it ad-hoc without installing:

```bash
npx @fission-ai/openspec init
```

> Check the repo for the exact current package name and commands
> (<https://github.com/Fission-AI/OpenSpec>) — the package is
> `@fission-ai/openspec` at time of writing.

Initialise it inside the repo:

```bash
cd demo-repo
openspec init      # creates openspec/ and generates .github/prompts/*.prompt.md
```

`openspec update` refreshes the generated agent instructions and prompt files
after an OpenSpec version bump.

---

## The flow: propose → apply → archive

| Step | Command | What happens |
|------|---------|--------------|
| **Propose** | `openspec propose add-transfer-limit` | Creates `openspec/changes/add-transfer-limit/` with `proposal.md`, `tasks.md`, `design.md`, and **spec deltas** under `specs/`. You review and edit these *before any code is written*. |
| **Apply** | `openspec apply add-transfer-limit` | The agent implements the tasks against the agreed spec, ticking off `tasks.md` as it goes and producing a diff you review. |
| **Archive** | `openspec archive add-transfer-limit` | Once merged, folds the deltas into the canonical `openspec/specs/` and moves the change into `openspec/changes/archive/<date>-add-transfer-limit/`. |

The key idea: **the spec is reviewed before the code exists.** Disagreements
surface in `proposal.md`/`spec.md`, which are cheap to change, rather than in a
1,000-line diff.

Validate a change at any point:

```bash
openspec validate add-transfer-limit
```

---

## Slash commands inside the Copilot app

`openspec init` generates prompt files under **`.github/prompts/*.prompt.md`**.
The GitHub Copilot app (and CLI) surface any `.github/prompts/<name>.prompt.md`
as a **slash command** — so after init you get, in the session prompt box:

- `/openspec-propose` — scaffold a new change proposal
- `/openspec-apply` — implement an approved change
- `/openspec-archive` — archive a completed change

Typing `/openspec-propose add-transfer-limit` in a Copilot **agent session**
runs the propose prompt with your argument, inside the session's isolated git
worktree, and shows the generated artifacts on a canvas for review. This is the
same three prompt files included under `.github/prompts/` in this demo.

---

## OpenSpec vs GitHub Spec Kit

Both are spec-driven, but they sit at different weights.

| | **OpenSpec** | **GitHub Spec Kit** |
|---|---|---|
| Weight | Lightweight, minimal artifacts | Heavier — templates + a CLI (`specify`) |
| Orientation | **Brownfield-first** (change an existing codebase) | Greenfield-friendly, feature-from-scratch |
| Core flow | **propose → apply → archive** | `/specify` → `/plan` → `/tasks` (→ implement) |
| Artifacts | `proposal.md`, `tasks.md`, `design.md`, spec deltas | spec / plan / tasks templates per feature |
| Spec model | Living `openspec/specs/`, changed via reviewed *deltas* | Per-feature spec + plan documents |
| Agent support | Copilot + others via generated prompts | **Agent-agnostic, 30+ agents** |
| Repo | github.com/Fission-AI/OpenSpec | github.com/github/spec-kit |

Rule of thumb: reach for **OpenSpec** when you want a crisp, reviewable change
to an existing service; reach for **Spec Kit** when you're standing up a new
feature and want richer scaffolding and broad agent portability.

---

## Run it live in the session (mini-script)

In a Copilot agent session on `demo-repo`:

```text
1. openspec init
   → creates openspec/ + .github/prompts/*.prompt.md (slash commands appear)

2. /openspec-propose add-transfer-limit
   (or: openspec propose add-transfer-limit)
   → generates proposal.md, design.md, tasks.md, specs/transfers/spec.md
   → REVIEW them on the canvas; tighten the acceptance criteria

3. /openspec-apply add-transfer-limit
   → agent edits TransferService.cs + Ledger.cs, adds xUnit tests,
     ticks off tasks.md; review the diff before applying

4. Run the tests, open a PR, get it reviewed & merged

5. /openspec-archive add-transfer-limit
   → folds the delta into openspec/specs/, moves change to changes/archive/
```

Expected outcome: `TransferService.Transfer` now rejects a transfer that would
push the account over its `DailyTransferLimit`, with tests proving it, and the
daily-limit rule is captured permanently in `openspec/specs/transfers/`.

## If the live run stalls, use these pre-baked artifacts

If the agent stalls, rate-limits, or the room's connectivity dies, **the
finished artifacts are already checked in** under
`openspec/changes/add-transfer-limit/`. Walk the audience through them directly:

- `proposal.md` — why + what
- `design.md` — the enforcement approach and edge cases
- `tasks.md` — the implementation checklist
- `specs/transfers/spec.md` — the Given/When/Then acceptance criteria (the delta)

Then show `openspec/changes/archive/README.md` to explain what `archive` would
do. This lets the advanced segment land even with zero live agent time.
