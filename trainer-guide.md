# Trainer Guide — Agentic Coding 101 with the GitHub Copilot App

**Format:** 2-hour train-the-trainer session · **Audience:** Developers, Architects, QA engineers · **Tool:** the GitHub Copilot **app** (agent-native desktop app, GA 17 June 2026) — *not* the VS Code plugin.

> **Internal document.** This guide is written for a trainer delivering the session cold — not the original author. It names sources and reveals the planted defects in the demo repo. Do **not** hand it to participants; use the workbook and cheat-sheet for that.

---

## 1. How to use this guide

Read this end-to-end **once** before you deliver, then run the session with it open on a second screen. Each station is a self-contained boxed section you can glance at while the room works. The structure mirrors the timing table in §3 exactly.

Notation used throughout:

- **`monospace`** = something you type into the Copilot app verbatim (canonical prompt — do not paraphrase).
- **Click-path** = literal narration of where to click in the app.
- **> Fallback** boxes = what to do when a live agent stalls, is slow, or the network is flaky.

### The training package (deliverables)

You have been handed a package. Know what each piece is for:

| File / folder | Purpose | Who sees it |
|---|---|---|
| `syllabus.docx` | Formal course outline, learning outcomes, timings. | Buyer / L&D |
| `slides/` | Presenter deck for the session. | Projected |
| `workbook.md` | Participant hands-on workbook — every station's task + expected outcome. | Participants |
| `cheat-sheet` | One-page quick reference (concepts, prompts, click-paths). Handed out at wrap. | Participants |
| `demo-repo/` | The NorthBank sample repo with **planted defects** driven live. | Trainer + participants |
| `openspec-demo/` | OpenSpec-enabled variant for the advanced segment. | Trainer + participants |
| `trainer-guide.md` | **This file.** | Trainer only |

---

## 2. Pre-session prep checklist

> **Do this the day before, not the morning of.** The cloud coding-agent, PRs, Agent Merge and Automations all require the demo repo to live in **your own GitHub org** under a **paid Copilot licence**. You cannot borrow the author's repo.

- [ ] **Clone and push the demo repo to your own org.** `demo-repo/` must exist as a real GitHub repository you control. Cloud sessions, PR creation, Agent Merge and Automations act on *that* remote.
- [ ] **Confirm licences.** Every machine that will drive an agent needs a paid Copilot licence signed in. Verify your own first; ask participants to confirm theirs in the joining instructions.
- [ ] **Install the Copilot app** (desktop, macOS/Windows/Linux) on the training machine and sign in. Confirm it is the **app**, not the VS Code extension.
- [ ] **Pre-bake fallback branches** for every station (see per-station boxes). Push them so you can `git checkout` or open the finished PR instantly if a live agent stalls. Suggested names: `fallback/station-1` … `fallback/station-7`, plus `fallback/openspec`.
- [ ] **Capture screenshots / short recordings** of each station's happy path. Store them locally — do not rely on the network at delivery time.
- [ ] **Run each demo once, start to finish, on the training machine.** This warms caches, surfaces licence prompts, and tells you the real wall-clock time each agent takes today.
- [ ] **Install the community skill you'll demo live:** confirm `/plugin install addyosmani/agent-skills` works from your account (it's used in §5).
- [ ] **Verify the planted defects are intact** in `demo-repo/src/PaymentService/TransferService.cs` — the missing daily-limit enforcement and the non-positive-amount acceptance. If a previous run committed the fix, reset from `main`.
- [ ] **Prepare `openspec-demo/`** with OpenSpec initialised so `.github/prompts/*.prompt.md` surface as slash commands.
- [ ] **Room + network:** test wifi bandwidth; have a mobile hotspot as backup. Have the deck and all recordings available **offline**.

---

## 3. Timing table (sums to 120 min)

| Time | Duration | Segment |
|---|---|---|
| 0:00–0:10 | 10 min | Framing |
| 0:10–0:25 | 15 min | Core concepts + live app tour (install Addy Osmani `agent-skills` together) |
| 0:25–0:30 | 5 min | Exercise 0 — warm-up (connect repo, first session) |
| 0:30–1:20 | 50 min | Seven stations (~7 min each: demo → micro-exercise → debrief) |
| 1:20–1:25 | 5 min | Break |
| 1:25–1:45 | 20 min | Advanced: OpenSpec (propose→apply→archive); Spec Kit as contrast |
| 1:45–2:00 | 15 min | Guardrails, train-the-trainer tips, wrap, Q&A, cheat-sheet handout |
| | **120 min** | **Total** |

---

## 4. Framing script — 0:00–0:10

**Goal of this segment:** set expectations, land the arc, and pre-empt the two biggest misconceptions.

**Talking points (paraphrase, don't read):**

1. **Welcome + why now.** "We're not here to hype AI. We're here to learn a *tool* — the GitHub Copilot app — and where it fits in a real SDLC on a real banking codebase."
2. **The arc.** Draw it on the whiteboard:
   > **autocomplete → chat → agent-native**
   > - *Autocomplete* (the old Copilot): finishes your line. You drive every keystroke.
   > - *Chat*: you ask, it answers, you copy-paste. Still you doing the work.
   > - *Agent-native* (today's app): you delegate a **task**. The agent works in its own **worktree**, edits many files, runs tests, opens a PR. You review and steer.
3. **Set the boundary — say this explicitly:**
   > **"We're using Copilot, not building agents."** We are not writing an agent framework, not calling model APIs, not doing prompt engineering research. We drive a shipped product.
4. **What "agentic" buys you.** Parallelism (multiple sessions at once), isolation (worktrees, nothing touches your working tree until you say so), and delegation (you review diffs instead of typing them).
5. **The honest caveat.** The agent is fast and confident and sometimes wrong. Today's real skill is **review**. Every diff gets read.
6. **Roadmap the day.** Concepts tour → warm-up → seven SDLC stations on NorthBank → OpenSpec → guardrails. Personas: Developers, Architects, QA — everyone has a station where they lead.

> **Trainer note:** Keep this to 10 minutes. Resist demoing here — the tour is next.

---

## 5. Concepts tour + live app tour — 0:10–0:25

**Goal:** give everyone a shared vocabulary and one live "wow" moment (installing a real community skill) before hands-on.

### 5a. Core app concepts — live, with click-paths and analogies

Open the Copilot app on the projector. Walk these in order. Keep each to ~90 seconds.

> **Agent sessions & isolated worktrees**
> Click **New session**, type a trivial prompt (e.g. *"list the C# files in this repo"*), and start it.
> **Narrate:** "Each session runs in its own **git worktree** — a separate checkout. Nothing it does touches my working tree until I merge."
> **Analogy:** "Every session gets its own sandbox desk. Ten agents, ten desks, no one knocking over your coffee."

> **Canvases**
> Open the session's **canvas**.
> **Narrate:** "The canvas is the shared surface — the plan, the running terminal, the diff, the workflow state, all live."
> **Analogy:** "The agent's whiteboard. You watch it think and act in real time."

> **My Work**
> Click **My Work**.
> **Narrate:** "Your queue across everything — sessions, issues, PRs. This is home base."
> **Analogy:** "An inbox for work items, not messages."

> **Automations**
> Open **Automations**.
> **Narrate:** "Recurring or background prompts. Cron for agents — e.g. a nightly dependency scan." (We build one in Station 7.)

> **Agent Merge**
> Show it on any open PR (or a screenshot if none exists yet).
> **Narrate:** "Guides a PR through review and merge — it's an assistant for the merge, **not** an auto-merger. A human still approves."

> **Local vs cloud sessions**
> **Narrate:** "Sessions run **locally** on this machine, or **cloud** — GitHub-hosted. Cloud is what powers assign-an-issue-to-an-agent and PR flows. That's why the repo must live in your org."

### 5b. Customisation concepts — explained simply

Whiteboard three boxes:

| Concept | What it is | Analogy |
|---|---|---|
| **Skills** | Folders of instructions/scripts. A `SKILL.md` with YAML frontmatter (`name`, `description`) the agent loads *when relevant*. | A recipe card the agent picks up when the task matches. |
| **Plugins** | Bundles that ship custom agents (`*.agent.md`), skills, hooks (`hooks.json`), MCP servers (`.mcp.json`/`mcp.json`), LSP config. Install with `/plugin install owner/repo`. | An app store bundle — several capabilities in one install. |
| **MCP** | Model Context Protocol servers wired into repo/coding-agent settings — gives the agent new tools/data. | A USB port: plug in a tool (filesystem, GitHub, a database) and the agent can use it. |

### 5c. The live install moment — do this together

> **Everyone types this at once:**
> **`/plugin install addyosmani/agent-skills`**
>
> **Narrate:** "This is Addy Osmani's real community pack — 24 lifecycle skills, Copilot-compatible. We just gave every session in this repo a set of new recipes it can reach for."
> **Point out** where installed skills appear in the app, and mention siblings: **Matt Pocock's short-form skills** and the **github/awesome-copilot** collection.
> **Teaching moment:** customisation is *composable* — you'll author your own skill in Station 6, and this is exactly the mechanism.

> **> Fallback (network flaky):** show a screenshot of the pack installed and its skills listed. The concept lands without the live call.

---

## 6. Exercise 0 — warm-up — 0:25–0:30

**Goal:** every participant connects the demo repo and starts one session successfully. Shake out licence/sign-in problems now, not mid-station.

**Facilitate:**

1. Everyone opens the Copilot app and **connects the NorthBank demo repo** (their clone/fork or the shared org repo, per your joining instructions).
2. Start **one local session** with a read-only prompt so nothing changes:
   > *"Summarise what this repository does and list the main projects."*
3. Confirm each person sees: a session, its **canvas**, and a sensible summary naming **PaymentService**, the **legacy Delphi** code, and the **T-SQL** under `db/`.

> **Success criterion:** hands up when you see a summary. Do not proceed until ~90% are up.
> **Common snags:** not signed in / no licence; repo not connected; started a *cloud* session when local was intended (fine, just note the difference). Circulate and unblock.

---

## Stations 1–7 — 0:30–1:20

Run each station as **demo (≈3 min) → micro-exercise (≈2 min) → debrief (≈2 min)**. Type the canonical prompt **verbatim** — the workbook and cheat-sheet quote the same text, and consistency is the whole point of the package. Real file paths below are under `demo-repo/`.

> **Planted defects (trainer eyes only).** The whole station arc pivots on two defects in `src/PaymentService/TransferService.cs`:
> 1. **The gap** — `Transfer` does **not** enforce `DailyTransferLimit`. Built in Stations 1 & 3.
> 2. **The bug** — `Transfer` does **not** reject non-positive `amount`; `0` or negative is accepted, letting money flow the wrong way. Found in Stations 3 & 6.
> These are the **only** two defects — keep demos crisp. They are **not** commented in code; they must be discoverable.

---

### Station 1 — Plan / Requirements

> **Goal:** turn a GitHub issue into a structured task list using a skill.
> **Lead persona:** Architect / Dev.

**Prompt (verbatim):**
```
Using the requirements-to-tasks skill, read ISSUES.md issue #1 and produce a structured task list with acceptance criteria and the files likely to change.
```

**Files referenced:** `demo-repo/ISSUES.md` (issue #1 = "Enforce daily transfer limit in TransferService"), `src/PaymentService/TransferService.cs`, `src/PaymentService/Account.cs` (the `DailyTransferLimit` field).

**What the agent should do:** load the requirements-to-tasks skill, read issue #1, emit a numbered task list with acceptance criteria and predict the files it will touch (TransferService, Ledger for today's-sum, tests).

**Teaching moment:** a skill turns a vague issue into an actionable plan *before* any code is written. Planning is delegable too.

**Common failure modes:**
- Agent doesn't invoke the skill → its frontmatter `description` didn't match. Say so and re-prompt naming the skill explicitly.
- Task list omits acceptance criteria → point at the issue's acceptance-criteria section and re-run.

> **> Fallback:** show the pre-baked task list (screenshot) or open `fallback/station-1`. Read the acceptance criteria aloud.

---

### Station 2 — Design / Architecture

> **Goal:** generate design options and an ADR for idempotent, safely-retried transfers, on a canvas.
> **Lead persona:** Architect.

**Prompt (verbatim):**
```
We retry failed transfers. Propose 2–3 designs to make Transfer idempotent so a retry never double-posts. Compare trade-offs, then write an ADR to docs/adr/0001-idempotent-transfers.md.
```

**Files referenced:** `src/PaymentService/TransferService.cs`, `src/PaymentService/Ledger.cs`; output written to `demo-repo/docs/adr/0001-idempotent-transfers.md`.

**What the agent should do:** propose 2–3 approaches (e.g. idempotency key on the ledger entry, dedupe on `(from,to,amount,window)`, transactional outbox), compare trade-offs, then write a structured ADR file.

**Teaching moment:** the agent is a design *sparring partner*. It surfaces options and trade-offs; the human chooses. The canvas keeps the reasoning visible.

**Common failure modes:**
- Jumps to code instead of an ADR → re-prompt: "Don't implement; write the ADR only."
- Only one option → "Give me at least two alternatives with trade-offs."

> **> Fallback:** open the pre-baked `docs/adr/0001-idempotent-transfers.md` from `fallback/station-2` and walk the trade-off table.

---

### Station 3 — Implement in C#

> **Goal:** assign an issue to an agent session and land a multi-file feature in PaymentService.
> **Lead persona:** Dev.

**Prompt (verbatim):**
```
Implement ISSUES.md issue #1 (daily transfer limit) across PaymentService. Update TransferService and add xUnit tests. Show me the diff before applying.
```

**Files referenced:** `src/PaymentService/TransferService.cs`, `src/PaymentService/Ledger.cs` (sum of today's transfers), `tests/PaymentService.Tests/TransferServiceTests.cs` (one existing happy-path test).

**What the agent should do:** add daily-limit enforcement in `Transfer`, using the ledger's today's-total, and add xUnit tests for the limit — **and pause to show the diff before applying**.

**Teaching moment:** "**Show me the diff before applying**" is the phrase that keeps you in control. Review every hunk before it touches the tree.

**Common failure modes:**
- Applies without pausing → re-run and stress the diff-first clause; discard and redo.
- Fixes only the limit and silently walks past the non-positive-amount bug → *good* — that's Station 6's job. Don't let it wander there now.
- Tests reference a method that doesn't exist → have the agent run the tests and self-correct.

> **> Fallback:** open the finished PR from `fallback/station-3` and review the diff as if the agent produced it.

---

### Station 4 — Data / T-SQL

> **Goal:** declarative migration with Atlas, tSQLt tests for the posting proc, and a query optimisation.
> **Lead persona:** Dev / Architect.

**Prompt 4a (verbatim):**
```
Add a nullable Reference column to LedgerEntries using Atlas declarative schema (atlas/schema.hcl) and generate the migration.
```
**Prompt 4b (verbatim):**
```
Write tSQLt tests for dbo.PostTransaction covering a normal post and a non-positive amount that must be rejected.
```
**Prompt 4c (verbatim):**
```
Explain why db/reports_slow.sql is slow and rewrite it to be SARGable; suggest the index to add.
```

**Files referenced:** `atlas/schema.hcl`, `atlas/atlas.hcl`, `db/proc_PostTransaction.sql`, `tests/tsqlt/test_PostTransaction.sql`, `db/reports_slow.sql`, `db/schema.sql`.

**What the agent should do:** (4a) edit `schema.hcl` to add nullable `LedgerEntries.Reference` and generate the Atlas migration; (4b) write two tSQLt tests — the non-positive-amount test is **written to fail** against the current guard-less proc, motivating a fix; (4c) explain the non-SARGable `WHERE` (e.g. `CONVERT(date, PostedAt)=@Day`), rewrite to a range predicate, and recommend the supporting index.

**Teaching moment:** the agent works across *data* too — schema-as-code, DB unit tests, and query tuning — and a **failing test is a feature**: it documents the gap before the fix.

**Common failure modes:**
- Atlas migration not generated, only the HCL edited → prompt "now generate the migration".
- tSQLt test written to pass (hiding the gap) → insist the non-positive case asserts rejection so it fails today.
- Query "optimised" but still non-SARGable → point at the `CONVERT`/`YEAR()` wrapper on the column.

> **> Fallback:** show pre-baked outputs from `fallback/station-4` — the migration file, the failing tSQLt run, and the before/after query with the index DDL.

---

### Station 5 — Legacy / Delphi + UI test

> **Goal:** explain & refactor legacy Object Pascal, generate DUnitX tests, scaffold an Appium/WinAppDriver UI test.
> **Lead persona:** Dev / QA.

**Prompt 5a (verbatim):**
```
Explain legacy/InterestCalc.pas, then refactor CalculateMonthlyInterest to use banker's rounding to 2 decimal places without changing the public signature.
```
**Prompt 5b (verbatim):**
```
Generate DUnitX tests for CalculateMonthlyInterest including a rounding edge case.
```
**Prompt 5c (verbatim):**
```
Scaffold an Appium + WinAppDriver test that loads a statement for account 1001 in StatementViewer and asserts the list is populated.
```

**Files referenced:** `legacy/InterestCalc.pas` (unit `uInterestCalc`, rounding weakness — divides annual by 12, uses `Trunc`, no 2-dp rounding); `tests/dunitx/InterestCalcTests.dpr` + `InterestCalcTests.pas`; `ui/StatementViewer.dpr`, `ui/uMainForm.pas`, `ui/uMainForm.dfm` (controls `edtAccountId`, `btnLoad` caption "Load", `lstStatement`/`memStatement`); `tests/appium/statement_ui_test.js`, `tests/appium/package.json`.

**What the agent should do:** (5a) explain the rounding weakness and refactor to banker's rounding at 2 dp, keeping the signature; (5b) generate DUnitX `[Test]` cases including a rounding edge case that fails against the *old* implementation; (5c) scaffold a webdriverio-style Appium test that launches `StatementViewer.exe`, targets `edtAccountId`, clicks `btnLoad`, asserts `lstStatement` populated.

**Teaching moment:** **no native inline Copilot completion exists inside RAD Studio** — but the app is repo/worktree-based and edits Object Pascal as text, so agentic explain/refactor/test-gen works fine. Drive it **from the app, not the IDE**. This is the "even our legacy code is in scope" moment for the QA/architect crowd.

**Common failure modes:**
- Agent tries to open RAD Studio → remind the room it edits Pascal as text; no IDE needed.
- Refactor changes the signature → re-prompt "keep the public signature identical".
- Appium test hard-codes paths → fine, they're placeholders; note the automation IDs come from the stable control `Name`s.

> **> Fallback:** open `fallback/station-5` — the refactored unit, the DUnitX run showing the rounding test now green, and the Appium scaffold.

---

### Station 6 — Author a skill

> **Goal:** attendees create, install and invoke their **own** skill.
> **Lead persona:** All.

**Prompt (verbatim):**
```
Create a skill named pii-redaction-check that flags account numbers, names and emails written to logs or console. Put it in .github/skills/pii-redaction-check/SKILL.md, then run it over TransferService.cs.
```

**Files referenced:** starter template at `.github/skills/pii-redaction-check/SKILL.md` (frontmatter + TODO body), the complete reference `.github/skills/banking-review/SKILL.md`, and the target `src/PaymentService/TransferService.cs`.

**What the agent should do:** flesh out the starter `SKILL.md` (name/description frontmatter + detection body), then run it over `TransferService.cs` and report any PII-in-logs findings. Running it over `TransferService.cs` is also a natural moment for the **non-positive-amount bug** to surface if the review skill is broad.

**Teaching moment:** customisation closes the loop — you consumed a community skill in §5, now you author one. `SKILL.md` frontmatter (`name`, `description`) is what makes the agent pick it up.

**Common failure modes:**
- Skill created but not invoked → "now run it over TransferService.cs".
- Frontmatter malformed → point at `banking-review/SKILL.md` as the reference shape.

> **> Fallback:** open the completed `pii-redaction-check/SKILL.md` from `fallback/station-6` and run it live (it's short and fast).

---

### Station 7 — Review & Ship

> **Goal:** Copilot code review on the PR, Agent Merge, and an Automation.
> **Lead persona:** QA / All.

**Prompt (verbatim):**
```
Review this PR for banking-safety issues and summarise required changes.
```
Then run **Agent Merge**. Then create an **Automation** with this prompt (verbatim):
```
Every night, check for outdated NuGet dependencies and known CVEs and open an issue if any are found.
```

**Files referenced:** the PR from Station 3 (limit feature) against `demo-repo`; review informed by `.github/skills/banking-review/SKILL.md` and `.github/agents/security-reviewer.agent.md`.

**What the agent should do:** review the PR against NorthBank rules (money is `decimal`/`Currency` not float, no PII in logs, every transfer path validated, ledger balanced) and summarise required changes — ideally flagging the **non-positive-amount bug** if it's still in scope. Then Agent Merge guides the PR to merge (human approves). The Automation registers a nightly job that opens issues for outdated NuGet deps / CVEs.

**Teaching moment:** the loop closes — review, ship, and automate ongoing hygiene. **Agent Merge assists; it does not auto-merge.** A human still approves.

**Common failure modes:**
- Review is generic → point it at the banking-review skill / security-reviewer agent.
- Agent Merge blocked by branch protection / no approval → expected; explain the human gate is deliberate.
- Automation created but participants expect it to run instantly → it's *scheduled* (nightly); show where it lives in **Automations**.

> **> Fallback:** show a screenshot of the review comments, the Agent Merge flow, and the registered Automation from `fallback/station-7`.

---

## 7. Break — 1:20–1:25

Five minutes. Tell the room the next segment (OpenSpec) needs no setup from them — they watch, then optionally follow in `openspec-demo/`.

---

## 8. Advanced — OpenSpec — 1:25–1:45

**Goal:** show lightweight spec-driven development end-to-end, and contrast it with Spec Kit.

**Context to give the room:** **OpenSpec** (github.com/Fission-AI/OpenSpec) is a lightweight, **brownfield-first** spec-driven workflow: **propose → apply → archive**. It generates `.github/prompts/*.prompt.md` that the Copilot app/CLI surface as **slash commands**.

**Run the flow live** (in `openspec-demo/`), narrating each step:

| Step | Command | In the app | What happens |
|---|---|---|---|
| Propose | `openspec propose add-transfer-limit` | `/propose`-style prompt | Generates a proposal + spec deltas + tasks. Review them. |
| Apply | `openspec apply add-transfer-limit` | `/apply` | Implements against the approved spec. |
| Archive | `openspec archive add-transfer-limit` | `/archive` | Folds the change into the living spec and closes it out. |

**Teaching moment:** the spec is the source of truth, and the change is proposed, reviewed, applied, then archived — auditable and reviewable, which matters in a bank.

> **Contrast — Spec Kit (github/spec-kit).** Heavier: templates + a CLI, commands `/specify /plan /tasks`, and **agent-agnostic** (30+ agents). Frame it as: *OpenSpec = light, brownfield, fast to adopt; Spec Kit = structured, template-driven, portable across agents.* Pick per project.

> **> Fallback:** if the CLI stalls, open the pre-generated proposal/specs/tasks from `fallback/openspec` and walk propose→apply→archive from the files. The concept survives without a live run.

---

## 9. Guardrails & wrap — 1:45–2:00

### 9a. Guardrails — say these plainly

> **Non-negotiables for using agents on real code:**
> - **Review every diff.** The agent is fast and confident and sometimes wrong. "Show me the diff before applying" is the default posture.
> - **PII / data policy.** No customer data, account numbers, names or emails in prompts, logs or console. (This is literally what Station 6's skill checks.)
> - **Licensing.** Paid Copilot licence required; respect repo and dependency licences the agent introduces.
> - **No auto-merge.** Agent Merge *assists* — a human approves and merges. Keep branch protection on.

### 9b. Train-the-trainer meta

**Pacing.** Stations are ~7 minutes and agents are non-deterministic. If a live agent is slow, **switch to the fallback immediately** rather than watching a spinner — you have branches and recordings for exactly this. Protect the break and the OpenSpec slot; they're where you'd otherwise overrun.

**Handling skeptics.** Don't argue capability — show the diff and the review step. The pitch is "delegation with review", not "magic". Acknowledge failure modes openly; a trainer who names the limitations earns trust.

**Keeping architects & QA engaged during code-heavy bits.** Every station tags a lead persona — call it out and hand the wheel. Architects lead Stations 1 & 2 (planning, ADR); QA leads Stations 5 & 7 (UI tests, review & ship). During C#/T-SQL heavy stretches, ask architects "is this the design we chose?" and QA "what test is missing here?" so they stay in the loop.

**Room setup.** Projector shows *your* screen for demos; participants work on their own machines. Second screen for you runs this guide. Have the deck and all recordings **offline**.

**If wifi / cloud is flaky.** Cloud sessions, PRs, Agent Merge and Automations need the network. If it's degraded: run **local** sessions for the code stations, use **fallback branches/recordings** for anything cloud (PR, merge, Automation), and defer the live `/plugin install` to a screenshot. Have a mobile hotspot ready.

### 9c. Wrap

- Recap the arc: autocomplete → chat → **agent-native**, and the seven SDLC stations.
- Reiterate: **we used Copilot, we didn't build agents.**
- **Hand out the cheat-sheet.**
- Q&A. Point people at the workbook to redo stations solo.

---

## 10. Appendix

### Quick troubleshooting

| Symptom | Likely cause | Fix |
|---|---|---|
| Agent won't start a session | Not signed in / no paid licence | Sign in; confirm licence. |
| No cloud session / PR / Agent Merge | Repo not in your org, or network down | Push demo-repo to your org; use fallback branch. |
| `/plugin install` fails | Network or account issue | Show the install screenshot; continue. |
| Skill not invoked | Frontmatter `description` didn't match | Re-prompt naming the skill; check `SKILL.md` frontmatter. |
| Planted defect already fixed | A prior run committed the fix | Reset `TransferService.cs` from `main`. |
| Agent tries to open RAD Studio (Station 5) | Assumes IDE needed | Remind: app edits Pascal as text, no IDE. |
| tSQLt / DUnitX test passes when it should fail | Test written too leniently | Insist the negative case asserts rejection. |
| Agent Merge blocked | Branch protection / no approval | Expected — explain the human gate. |
| Live agent slow | Non-determinism | Switch to fallback branch/recording. |

### Package links (other deliverables)

- `syllabus.docx` — formal outline & outcomes.
- `slides/` — presenter deck.
- `workbook.md` — participant hands-on tasks (mirrors Stations 1–7).
- `cheat-sheet` — one-page reference; hand out at wrap.
- `demo-repo/` — NorthBank sample with planted defects. Key paths: `src/PaymentService/TransferService.cs`, `src/PaymentService/Account.cs`, `src/PaymentService/Ledger.cs`, `tests/PaymentService.Tests/TransferServiceTests.cs`, `db/*.sql`, `atlas/schema.hcl`, `legacy/InterestCalc.pas`, `ui/*`, `tests/tsqlt/`, `tests/dunitx/`, `tests/appium/`, `.github/skills/`, `.github/agents/`, `.github/mcp.json`, `ISSUES.md`.
- `openspec-demo/` — OpenSpec-enabled variant for §8.

### External references (trainer-only)

- GitHub Copilot app (desktop, GA 17 June 2026) — paid licence required.
- Addy Osmani `agent-skills` — github.com/addyosmani/agent-skills
- Matt Pocock short-form skills; github/awesome-copilot collection.
- OpenSpec — github.com/Fission-AI/OpenSpec · GitHub Spec Kit — github/spec-kit
- Atlas — atlasgo.io (mssql) · tSQLt · DUnitX · Appium + WinAppDriver.
