# Agentic Coding 101 with the GitHub Copilot App

A ready-to-deliver, 2-hour **train-the-trainer** course that teaches engineers how to
use the **GitHub Copilot app** (the agent-native desktop app, GA 17 June 2026) across the
whole software development lifecycle — grounded in a fictional retail-bank core, **NorthBank**.

The scope is deliberate: attendees learn to **use Copilot as an agentic tool, not to build
agents from scratch**.

## Audience

Developers, Architects and QA engineers at a bank (or any regulated shop). No prior
agentic-coding experience is assumed, but attendees should be comfortable with git,
pull requests, C#/.NET, T-SQL, and — for the legacy station — able to read Object Pascal.

## The 2-hour outcome

By the end, every attendee can:

- Reason with the core concepts — **agent sessions & isolated worktrees, canvases,
  My Work, Automations, Agent Merge** — and run local *and* cloud sessions.
- Reproduce **seven concrete SDLC moves** (plan, design, implement, data, legacy, author
  a skill, review & ship) on their own repository.
- Extend Copilot with **skills, plugins and MCP**, and go deeper with **OpenSpec**
  spec-driven development.
- Apply **guardrails** appropriate to a regulated bank.

## Deliverable map

| Path | What it is |
| --- | --- |
| `syllabus.docx` | Formal course syllabus (matches the timings below). |
| `slides/index.html` | The presentation — a single-file **reveal.js** deck (CDN-loaded). |
| `slides/agentic-coding-101.pptx` | The same deck as **PowerPoint** (18 slides, speaker notes included) for editing in PowerPoint/Keynote/Google Slides. |
| `trainer-guide.md` | Internal facilitator script: per-station talking points, timings, fallbacks. |
| `workbook.md` | Participant hands-on workbook: each step with an expected outcome and a fallback. |
| `cheat-sheet.md` / `cheat-sheet.html` | One-page handout of concepts, prompts and shortcuts. |
| `demo-repo/` | The NorthBank sample repo attendees work in (C#, T-SQL, Delphi, tests, skills, MCP). Also **OpenSpec-initialised** (`openspec/` + `.github/prompts/`) for the advanced segment. |

> Some deliverables in this map are produced by their own generators in this package; this
> README is the top-level entry point that ties them together.

## Facilitator setup

### 1. Prerequisites

- The **GitHub Copilot app** installed on every machine (Windows / macOS / Linux) with a
  **paid Copilot licence**. This is the desktop app — *not* the VS Code plugin.
- A GitHub account with permission to create repos in the org you will use.
- Local toolchains for the stations you intend to run live: **.NET 8 SDK**; **SQL Server**
  + **Atlas** (atlasgo.io) and **tSQLt** for the data station; **RAD Studio / Delphi** with
  **DUnitX**, and **Appium + NovaWindows** for the legacy/UI station. Any station can fall
  back to a pre-baked branch or recording if a toolchain or a live agent is unavailable.
- See `demo-repo/README.md` for the full, per-station prerequisites pointer.

### 2. Get the demo repo into an org you control (required)

The cloud coding agent, PRs, **Agent Merge** and **Automations** all need the repo to live
on GitHub under an org you control. The sample is published as a standalone repo that
attendees clone:

**https://github.com/amilos/agentic-coding-101-demo** — carries the per-station fallback
branches (`station-1` … `station-7`, `openspec`) off `main`.

- **Delivering as `amilos`:** it's ready — open it in the Copilot app.
- **Re-delivering elsewhere:** fork it into your own org so you own the cloud sessions/PRs:
  ```bash
  gh repo fork amilos/agentic-coding-101-demo --org YOUR-ORG --clone
  ```
  (The canonical source also lives in this package under `demo-repo/` if you'd rather push a fresh copy.)

Then open the repo in the Copilot app (**Add repository** → select it) so cloud
sessions, `My Work`, PR review and Automations are available.

> `demo-repo/` ships two intentional defects (a missing daily-transfer-limit feature and a
> planted non-positive-amount bug) plus four ready-to-assign issues in `ISSUES.md`. They are
> the raw material for the stations — see the **"Planted issues"** section in
> `demo-repo/README.md` (trainers only; do not spoil them for attendees).

### 3. Open the slides

The deck is a single self-contained file that loads reveal.js from a CDN:

```bash
# just open it in any modern browser
open slides/index.html          # macOS
# xdg-open slides/index.html    # Linux
# start slides/index.html       # Windows
```

- Navigate with **→ / ←**, **Esc** for the overview, and **S** to open **speaker notes**.
- Slide numbers are shown (bottom-right).
- **Offline room?** The top of `slides/index.html` has a comment explaining how to vendor
  reveal.js locally (download the 4.x `dist/` + `plugin/`, then swap the four CDN URLs for
  local paths). Everything else in the deck is already self-contained.

### 4. Run the demos

- **Stations 1–7:** open `demo-repo/` in the Copilot app and use the canonical prompt for
  each station (they are printed on the slides, in `trainer-guide.md`, and on the
  cheat-sheet). Start each demo from **My Work**; drive edits from the app (this matters for
  the Delphi station — RAD Studio has no inline Copilot, the app edits Object Pascal as text).
- **Advanced (OpenSpec):** in the same `demo-repo/` (already OpenSpec-initialised), start from
  **issue #6 (idempotent transfers)** and run `openspec propose add-idempotent-transfers` → review →
  `openspec apply add-idempotent-transfers` → `openspec archive add-idempotent-transfers`. In the app
  these surface as `/propose`, `/apply`, `/archive`-style prompt files under `.github/prompts/`.
  (The generated change is pre-baked on the demo repo's `openspec` branch as a fallback.)
- Each hands-on step has an **expected outcome** and a **fallback** (a pre-baked `station-N`
  branch or a recording) for when a live agent stalls.

## Delivery order & timing (120 min)

| Time | Segment |
| --- | --- |
| 0:00–0:10 | **Framing** — autocomplete → chat → agent-native; "using Copilot, not building agents". |
| 0:10–0:25 | **Core concepts + live app tour** — install community skills from the `agentic-coding-101-marketplace` together. |
| 0:25–0:30 | **Exercise 0 warm-up** — connect the repo, start a first session. |
| 0:30–1:20 | **Seven stations** (~7 min each: demo → micro-exercise → debrief). |
| 1:20–1:25 | **Break**. |
| 1:25–1:45 | **Advanced: OpenSpec** (propose → apply → archive); Spec Kit as contrast. |
| 1:45–2:00 | **Guardrails, train-the-trainer tips, wrap, Q&A, cheat-sheet handout**. |

### The seven stations

1. **Plan / Requirements** *(Architect/Dev)* — GitHub issue → structured task list via a skill.
2. **Design / Architecture** *(Architect)* — options + ADR for idempotent, safely-retried transfers, on a canvas.
3. **Implement in C#** *(Dev)* — assign an issue to an agent session; multi-file feature in PaymentService; open a PR (reviewed & merged in Station 7).
4. **Data / T-SQL** *(Dev/Architect)* — Atlas declarative migration + tSQLt tests; optimise the slow statement query.
5. **Legacy / Delphi + UI test** *(Dev/QA)* — explain & refactor InterestCalc; DUnitX tests; Appium/NovaWindows UI test.
6. **Author a skill** *(All)* — attendees create, install and invoke their own skill (`pii-redaction-check`).
7. **Review & Ship** *(QA/All)* — Copilot code review on the PR, Agent Merge, and a nightly dependency/compliance Automation.
