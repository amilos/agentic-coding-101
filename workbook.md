# Agentic Coding 101 — Participant Workbook

**GitHub Copilot app · Hands-on session · 2 hours**

Welcome. Over the next two hours you will drive the **GitHub Copilot app** — the
agent-native desktop app — through a full slice of the software lifecycle on a
fictional retail bank, **NorthBank**. You will not watch slides the whole time:
you will plan, design, implement, test, review, ship, *and* author your own
skill, all from the app on your own machine.

Everything you do converges on **one real change**, carried end-to-end:

> **Enforce a daily transfer limit** in NorthBank's `TransferService`, and while
> you are in there, discover and squash a planted bug that lets money flow the
> wrong way.

By the close you will have touched planning, architecture, C# implementation,
T-SQL data work, legacy Delphi, a bespoke skill, code review, Agent Merge, an
Automation, and spec-driven development with OpenSpec.

---

## How to read this workbook

Each exercise follows the same rhythm. Look for these labels:

- **Goal** — one sentence on what you are about to achieve.
- **Prompt** — copy-paste this **verbatim** into the app. Prompts live in fenced
  code blocks so you can grab them cleanly.
- **Steps** — numbered, imperative. Do them in order.
- **Expected outcome** — what a healthy run produces.
- ✅ **Checkpoint** — "you should now see…". If you cannot tick it, flag a
  trainer before moving on.
- 🚀 **Stretch goal** — optional, for when you finish early.

> **The demo → your turn → checkpoint rhythm.** For each station the trainer
> demos once (~2 min), then you run the same prompt on your machine (~3 min),
> then we debrief against the checkpoint (~2 min). The workbook is your
> "your turn" script — you can also replay any station on your own later.

### 🆘 If your agent stalls

Agents are non-deterministic — a session may hang, wander, or produce an
unexpected diff. That is normal in a live room. When it happens:

1. **Steer, don't restart.** Reply in the same session: *"Stop. Focus only on
   `TransferService.cs`."*
2. **Reset the worktree.** Each session runs in its own isolated git worktree —
   discard it and start a fresh session from `main`.
3. **Fall back to the pre-baked artefact.** Every station has a ready-made
   branch or file (e.g. `solution/station-3`, or a file under `openspec-demo/`).
   Reading a known-good solution is a legitimate way to learn what "good" looks
   like.

---

## Key concepts (30-second refresher)

- **Agent session** — a unit of work; each runs in its own **isolated git
  worktree**, so parallel sessions never clobber each other.
- **Canvas** — a shared surface showing the plan, a terminal, a diff, or
  workflow state as the agent works.
- **My Work** — your queue of sessions, issues and PRs.
- **Automation** — a recurring/background prompt (e.g. a nightly check).
- **Agent Merge** — the app guiding a PR through review and merge.
- **Skill** — a folder (`SKILL.md` with YAML frontmatter) Copilot loads when
  relevant. You author one in Exercise 6.
- **Local vs cloud sessions** — run on your machine *or* on GitHub-hosted
  infrastructure. Your paid licence opens both today.

> **Delphi note.** There is *no* inline Copilot completion inside RAD Studio.
> The app edits Object Pascal as plain text on the worktree — agentic edits,
> refactors and test generation all work fine. Drive it from the app, not the IDE.

---

## The NorthBank demo repo — your map

You will keep coming back to these paths. Skim them now:

| Path | What lives there |
|------|------------------|
| `demo-repo/src/PaymentService/TransferService.cs` | The core `Transfer(...)` method — **the gap and the bug live here** |
| `demo-repo/src/PaymentService/Account.cs` | `Account` record incl. `DailyTransferLimit` |
| `demo-repo/src/PaymentService/Ledger.cs` | `ILedger` + `InMemoryLedger`, incl. `SumTransfersToday(...)` |
| `demo-repo/src/PaymentService/Program.cs` | Tiny console: one sample transfer, prints balances |
| `demo-repo/tests/PaymentService.Tests/TransferServiceTests.cs` | xUnit — one passing happy-path test |
| `demo-repo/ISSUES.md` | 4 ready-to-assign issues (the work backlog) |
| `demo-repo/db/schema.sql`, `db/proc_PostTransaction.sql`, `db/reports_slow.sql` | T-SQL schema, posting proc, slow report |
| `demo-repo/atlas/schema.hcl`, `atlas/atlas.hcl` | Atlas declarative schema (mssql) |
| `demo-repo/tests/tsqlt/test_PostTransaction.sql` | tSQLt tests (one written to FAIL) |
| `demo-repo/legacy/InterestCalc.pas` | Delphi `CalculateMonthlyInterest` (rounding weakness) |
| `demo-repo/ui/StatementViewer.dpr`, `ui/uMainForm.pas/.dfm` | Win32 VCL statement screen |
| `demo-repo/tests/dunitx/…`, `tests/appium/…` | DUnitX + Appium/NovaWindows scaffolds |
| `demo-repo/.github/skills/banking-review/SKILL.md` | A complete sample skill (read it for reference) |
| `demo-repo/.github/skills/pii-redaction-check/SKILL.md` | A **starter** skill — you complete it in Exercise 6 |

> 🔎 **The two planted defects (you will discover them, so don't peek too hard):**
> `TransferService.Transfer(...)` (1) never enforces `DailyTransferLimit` — the
> **gap** — and (2) never rejects a `0` or negative `amount` — the **bug** that
> lets money flow the wrong way.

---

# Exercise 0 — Warm-up: connect and start your first session

**Goal:** open the Copilot app, get the NorthBank repo onto your machine, and
run your very first local agent session. ⏱ ~5 min.

### Steps

1. **Open the GitHub Copilot app** (desktop). Sign in if prompted — you need
   your paid Copilot licence, which you already have.
2. **Connect the training repo.** From the home screen choose **Clone / Open
   repository** and point it at the NorthBank repo the trainer shared. If you
   already have it locally, open the folder directly.
3. Once it opens, browse the file tree and confirm you can see
   `demo-repo/src/PaymentService/TransferService.cs`.
4. **Start your first local session.** Click **New session**, keep it **Local**,
   and run this warm-up prompt:

   ```
   Give me a one-paragraph overview of what the PaymentService project does, and list the public methods on TransferService.
   ```

5. Read the reply. Notice the session is running in its own worktree and that
   the response references real files.

**Expected outcome:** a short, accurate summary naming
`TransferService.Transfer(string fromId, string toId, decimal amount)` and
mentioning the ledger. No files are changed.

### ✅ Checkpoint

You should now see:

- The Copilot app open with the NorthBank repo loaded.
- One completed **local** session in **My Work**.
- A summary that correctly names the `Transfer` method's signature.

### 🚀 Stretch goal

Ask a follow-up in the same session: *"Which file would I change to add a
per-account daily limit?"* — see whether it points you at `TransferService.cs`
and `Ledger.cs`.

### 🆘 If your agent stalls

If the repo will not clone, open the folder the trainer pre-placed on your
machine instead, then re-run step 4.

---

# Exercise 1 — Plan / Requirements

*Lead persona: Architect / Developer.*

**Goal:** turn a plain-English GitHub issue into a structured, actionable task
list using a **skill**. ⏱ ~7 min.

Open `demo-repo/ISSUES.md` and read **issue #1 — "Enforce daily transfer limit
in TransferService"**. This is the gap you will close across the whole session.

### Prompt

```
Using the planning-and-task-breakdown skill, read ISSUES.md issue #1 and produce a structured task list with acceptance criteria and the files likely to change.
```

### Steps

1. Start a **new local session**.
2. Paste the prompt above verbatim and run it.
3. Watch the plan appear on the **canvas**. Read each task.
4. Sanity-check the "files likely to change" list against your repo map.

**Expected outcome:** a numbered task list covering the total via
`SumTransfersToday(...)`, a limit check in `Transfer(...)`, a clear failure
message, and a test — naming `TransferService.cs`, `Ledger.cs`,
`TransferServiceTests.cs`.

### ✅ Checkpoint

You should now see:

- A structured task list with **acceptance criteria** for issue #1.
- `src/PaymentService/TransferService.cs` in the "files likely to change" list.
- A mention that `SumTransfersToday` already exists and can be reused.

### 🚀 Stretch goal

Ask it to also list *edge cases* worth testing (exactly-at-limit, over-limit,
first transfer of the day).

### 🆘 If your agent stalls

`planning-and-task-breakdown` comes from the **`agentic-coding-101-marketplace`**
you added in §5. If it's not found, either add the marketplace now
(**Settings → Plugins → Add Marketplace**), or just run the prompt without the
skill phrase — *"Read ISSUES.md issue #1 and produce a structured task list…"* —
you'll get a slightly less structured but usable plan.

---

# Exercise 2 — Design / Architecture

*Lead persona: Architect.*

**Goal:** compare design options for **idempotent** transfers and capture the
decision in an ADR on a canvas. ⏱ ~7 min.

NorthBank retries failed transfers. Without care, a retry could double-post.
This exercise is about *thinking before coding*.

### Prompt

```
We retry failed transfers. Propose 2–3 designs to make Transfer idempotent so a retry never double-posts. Compare trade-offs, then write an ADR to docs/adr/0001-idempotent-transfers.md.
```

### Steps

1. Start a **new local session**.
2. Paste the prompt verbatim and run it.
3. On the canvas, read the 2–3 options (e.g. client-supplied idempotency key,
   dedupe on a natural transaction id, ledger-level unique constraint).
4. Review the trade-offs table, then open the generated
   `docs/adr/0001-idempotent-transfers.md`.

**Expected outcome:** an ADR with **Context / Options / Decision /
Consequences**, comparing at least two approaches and recommending one (an
idempotency key is the usual pick).

### ✅ Checkpoint

You should now see:

- A new file `docs/adr/0001-idempotent-transfers.md` in the diff.
- At least two distinct designs with explicit trade-offs.
- A stated **Decision** with reasoning.

### 🚀 Stretch goal

Ask: *"Where would the idempotency key be enforced — in `TransferService`, in
the `ILedger`, or at the database? Show me the smallest change."*

### 🆘 If your agent stalls

If no ADR file is written, ask: *"Write the ADR you just described to
`docs/adr/0001-idempotent-transfers.md` now."* If it still stalls, a reference
ADR is on the `solution/station-2` branch.

---

# Exercise 3 — Implement in C#

*Lead persona: Developer.*

**Goal:** implement the daily-limit feature, review the diff on the Canvas
before it lands, **and open a PR** — that PR is what you review and merge in
Exercise 7. This is the heart of the session. ⏱ ~8 min.

### Prompt

```
Implement ISSUES.md issue #1 (daily transfer limit) across PaymentService. Update TransferService and add xUnit tests.
```

### Steps

1. Start a **new local session** (or assign issue #1 to an agent session from
   **My Work** if your trainer set that up).
2. Paste the prompt verbatim and run it.
3. **Read the diff on the canvas before approving.** Check that
   `Transfer(...)`:
   - reads today's total via `_ledger.SumTransfersToday(fromId)`,
   - rejects the transfer when `today's total + amount` would exceed
     `from.DailyTransferLimit`,
   - returns a clear failure message.
4. Confirm the new xUnit test asserts an over-limit transfer fails.
5. Apply the diff, then run the tests (see below).
6. **Open a PR** from the session (push the branch → *Create pull request*, or
   let the cloud agent open it). Leave it open — you'll review and merge this PR
   in **Exercise 7**.

### Verify it builds and passes

Open the session terminal on the canvas (or a local shell) and run:

```
dotnet test demo-repo/tests/PaymentService.Tests/PaymentService.Tests.csproj
```

**Expected outcome:** `TransferService.cs` gains a limit check;
`TransferServiceTests.cs` gains an over-limit test; all tests pass green.

### ✅ Checkpoint

You should now see:

- A diff touching `src/PaymentService/TransferService.cs` with a
  `DailyTransferLimit` check that uses `SumTransfersToday`.
- A new failing-then-passing test for the over-limit case.
- `dotnet test` reporting **all tests passed**.
- An **open PR** for the daily-limit change — you'll review and merge it in Exercise 7.

### 🚀 Stretch goal

Ask the agent to add an **exactly-at-limit** boundary test and confirm your
check uses the correct comparison (a transfer that hits the limit exactly
should still succeed).

### 🆘 If your agent stalls

If the build breaks, paste the compiler error back into the same session and
say *"Fix this and re-run the tests."* A known-good implementation is on
`solution/station-3`.

---

# Exercise 4 — Data / T-SQL

*Lead persona: Developer / Architect.*

**Goal:** evolve the schema declaratively with **Atlas**, write **tSQLt** tests
for the posting proc, and make a slow report query fast. ⏱ ~8 min (three
short prompts).

You will work against `demo-repo/db/…` and `demo-repo/atlas/…`.

### 4a — Add a column with Atlas

```
In atlas/schema.hcl, add a nullable Reference column of type nvarchar(140) to the LedgerEntries table, then generate the migration with `atlas migrate diff`.
```

1. Run the prompt in a **new local session**.
2. Check that `atlas/schema.hcl` now declares `Reference` on the
   `LedgerEntries` table and a migration was generated for **mssql**.

### 4b — tSQLt tests for the posting proc

```
Write tSQLt tests for dbo.PostTransaction covering a normal post and a non-positive amount that must be rejected.
```

1. Run it. Two tests should appear under `tests/tsqlt/test_PostTransaction.sql`.
2. Note that the **non-positive-amount** test is expected to **FAIL** against
   the current `proc_PostTransaction.sql` — the proc has no guard yet. That
   failing test is the point: it motivates the fix (the same theme as the C#
   bug in Exercise 6).

### 4c — Make the slow report SARGable

```
Explain why db/reports_slow.sql is slow and rewrite it to be SARGable; suggest the index to add.
```

1. Run it. Read the explanation.
2. Confirm the rewrite removes the function-on-column pattern (e.g.
   `CONVERT(date, PostedAt) = @Day` becomes a half-open range
   `PostedAt >= @Day AND PostedAt < @Day + 1`) and that a supporting index on
   `PostedAt` is suggested.

**Expected outcome:** a declarative `Reference` column + migration; two tSQLt
tests (one failing on purpose); a SARGable rewrite of `reports_slow.sql` plus an
index suggestion.

### ✅ Checkpoint

You should now see:

- `Reference` added to `LedgerEntries` in `atlas/schema.hcl` + a migration file.
- A tSQLt test that **fails** for non-positive amounts (this is expected).
- A rewritten query with **no function wrapping `PostedAt`** and an index
  recommendation.

### 🚀 Stretch goal

Ask the agent to also update `proc_PostTransaction.sql` to reject non-positive
amounts, then confirm your failing tSQLt test would now pass.

### 🆘 If your agent stalls

No SQL Server / Atlas / tSQLt installed locally? That's fine — this station is
about *reading and reasoning* about the generated SQL. Pre-baked outputs are on
`solution/station-4` if the live run wanders.

---

# Exercise 5 — Legacy / Delphi + UI test

*Lead persona: Developer / QA.*

**Goal:** understand and safely refactor legacy Object Pascal, generate DUnitX
tests, and scaffold a Windows UI test — all from the app, no IDE completion
needed. ⏱ ~8 min (three short prompts).

### 5a — Explain and refactor the interest calc

```
Explain legacy/InterestCalc.pas, then refactor CalculateMonthlyInterest to use banker's rounding to 2 decimal places without changing the public signature.
```

1. Run in a **new local session**. Read the explanation first.
2. Note the current **rounding weakness** (it divides the annual rate by 12 and
   truncates instead of rounding to 2 dp).
3. Confirm the refactor keeps the signature
   `function CalculateMonthlyInterest(Balance: Currency; AnnualRatePercent:
   Double): Currency;` and applies banker's rounding to 2 dp.

### 5b — DUnitX tests

```
Generate DUnitX tests for CalculateMonthlyInterest including a rounding edge case.
```

1. Run it. Confirm a `[TestFixture]` with 2–3 `[Test]` cases appears, including
   one rounding case that would have failed against the *old* implementation.

### 5c — Appium / NovaWindows UI test

```
Scaffold an Appium + NovaWindows test that loads a statement for account 1001 in StatementViewer and asserts the list is populated.
```

1. Run it. Confirm the scaffold under `tests/appium/` launches
   `StatementViewer.exe`, targets `edtAccountId`, types `1001`, clicks
   `btnLoad`, and asserts `lstStatement` is populated.
2. Note the app path and automation ids are placeholders/comments — that's
   deliberate for the classroom.

**Expected outcome:** a signature-preserving 2-dp banker's-rounding refactor;
DUnitX tests incl. a rounding edge case; a runnable-shaped Appium scaffold
targeting the real control names.

### ✅ Checkpoint

You should now see:

- `CalculateMonthlyInterest` unchanged in signature but now rounding to 2 dp.
- A DUnitX rounding test that expresses the corrected expectation.
- An Appium test referencing `edtAccountId`, `btnLoad`, `lstStatement`.

### 🚀 Stretch goal

Ask the agent to explain, in one paragraph, *why* banker's rounding matters for
a bank (bias over millions of postings).

### 🆘 If your agent stalls

You don't need RAD Studio or a Windows box today — reason about the generated
Pascal and JS. Reference output is on `solution/station-5`.

---

# Exercise 6 — Author your first skill 🛠️

*Lead persona: everyone.*

**Goal:** create, install and invoke **your own skill** — a `pii-redaction-check`
that flags account numbers, names and emails written to logs or console — then
run it over `TransferService.cs`. ⏱ ~8 min.

This is the star exercise: skills are how you teach Copilot *your* rules.

### What a skill is

A skill is a folder containing a `SKILL.md` file with **YAML frontmatter** and a
markdown body. Copilot loads it when the `description` matches what you are
doing. A starter template is already at
`demo-repo/.github/skills/pii-redaction-check/SKILL.md` (frontmatter + a TODO
body). You can complete it by hand *or* have the agent do it — the canonical
prompt does both.

### Prompt

```
Create a skill named pii-redaction-check that flags account numbers, names and emails written to logs or console. Put it in .github/skills/pii-redaction-check/SKILL.md, then run it over TransferService.cs.
```

### Folder layout

Your finished skill should look like this:

```
.github/
  skills/
    pii-redaction-check/
      SKILL.md
```

### SKILL.md — YAML frontmatter + body

If you write it by hand, use this shape (frontmatter is required):

```markdown
---
name: pii-redaction-check
description: >-
  Flags NorthBank PII — account numbers, customer names, and email addresses —
  written to logs, console, or exceptions. Use when reviewing code that logs or
  prints anything in the PaymentService.
---

# PII redaction check

When reviewing a file, flag any code path that writes personally identifiable
information to a log, `Console`, or exception message.

Treat these as PII:

- **Account numbers / ids** — e.g. values used as `fromId` / `toId`.
- **Customer names** — the `Owner` field on `Account`.
- **Email addresses** — anything matching an email pattern.

For each finding, report: the file and line, what leaked, and a safe
alternative (log a **masked** value or a non-reversible reference id instead).
If nothing leaks, say so explicitly.
```

### Steps

1. Start a **new local session**.
2. Run the canonical prompt above (it fills in `SKILL.md` **and** invokes it).
   Or: edit `SKILL.md` by hand using the shape above, then invoke it separately.
3. **Make the app pick it up.** Skills under `.github/skills/` are discovered
   from the repo automatically. If a freshly written skill isn't recognised,
   start a **new session** so the app re-scans skills, and make sure the
   `description` clearly matches the task.
4. **Invoke it over the target file:**

   ```
   Run the pii-redaction-check skill over src/PaymentService/TransferService.cs and list any findings.
   ```

5. Read the findings. `TransferService.cs` today returns messages like
   `Unknown source account '{fromId}'` — decide with the agent whether echoing
   an account id counts as PII leakage for NorthBank.

**Expected outcome:** a complete `SKILL.md` with valid frontmatter (`name`,
`description`), and a report from running it over `TransferService.cs` that
either flags the account-id echoing in failure messages or explains why it's
acceptable.

### ✅ Checkpoint

You should now see:

- `.github/skills/pii-redaction-check/SKILL.md` filled in with `name` and
  `description` in the frontmatter.
- The skill recognised by the app.
- A findings report produced by running the skill against `TransferService.cs`.

### 🚀 Stretch goal

Point the skill at `Program.cs` (which prints balances) and see whether it
flags the console output. Then browse Addy Osmani's `agent-skills` or Matt
Pocock's short-form skills for inspiration on how tightly to scope a
`description`.

### 🆘 If your agent stalls

If the skill isn't picked up, double-check the folder is exactly
`.github/skills/pii-redaction-check/` and the file is named `SKILL.md` with a
valid `---` YAML block at the very top. A completed reference skill is on
`solution/station-6`, and `banking-review/SKILL.md` is a working example to
copy the structure from.

---

# Exercise 7 — Review & Ship

*Lead persona: QA / everyone.*

**Goal:** run a Copilot **code review** on your PR, drive it through **Agent
Merge**, and set up an **Automation**. This is also where the **planted bug**
gets caught. ⏱ ~8 min.

By now your daily-limit change (Exercise 3) is ready to become a PR.

### Prompt (review)

```
Review this PR for banking-safety issues and summarise required changes.
```

### Steps

1. Open your daily-limit change as a **pull request** from the app.
2. Run the review prompt above.
3. **Read the findings carefully.** A good banking review should surface the
   **planted bug**: `Transfer(...)` never rejects a `0` or negative `amount`,
   so a negative transfer moves money the *wrong* way. If the review misses it,
   nudge: *"What happens if `amount` is zero or negative?"*
4. Fix the bug in the same or a follow-up session: reject non-positive amounts
   with a clear message, and add an xUnit test proving a negative transfer
   fails. Re-run `dotnet test`.
5. **Agent Merge.** Let the app guide the PR through review and merge — read
   each step rather than click-through blindly.
6. **Create an Automation** (a nightly background prompt):

   ```
   Every night, check for outdated NuGet dependencies and known CVEs and open an issue if any are found.
   ```

**Expected outcome:** a review that flags the non-positive-amount bug; a fix
with a new passing test; the PR merged via Agent Merge; a nightly Automation
registered.

### ✅ Checkpoint

You should now see:

- A review comment identifying the missing non-positive-amount guard.
- A committed fix + a new test that fails without it and passes with it.
- The PR merged, and an **Automation** listed in your app.

### 🚀 Stretch goal

Run your own `pii-redaction-check` skill from Exercise 6 as part of the review
and fold its findings into the summary.

### 🆘 If your agent stalls

If Agent Merge can't complete (branch protection, no remote), stop at the review
+ fix and describe the merge you *would* run. Reference PR and fix are on
`solution/station-7`.

---

# Exercise 8 — Advanced: spec-driven development with OpenSpec

*For everyone — this is the post-break advanced block.*

**Goal:** run a full **OpenSpec** loop — **propose → apply → archive** — for the
`add-transfer-limit` change, and see how it surfaces as slash commands in the
app. ⏱ ~15 min.

**OpenSpec** is lightweight, brownfield-first, spec-driven development. The flow
is: describe a change as a *proposal* with specs and tasks, apply it, then
archive it once shipped. It generates `.github/prompts/*.prompt.md` files that
the Copilot app/CLI surface as **slash commands** (e.g. `/propose`, `/apply`,
`/archive`).

### Steps

1. In a session terminal (or a local shell), run the **propose** step:

   ```
   openspec propose add-transfer-limit
   ```

2. **Review the generated artefacts** — the proposal, specs, and task list that
   OpenSpec scaffolds for `add-transfer-limit`. Read them; this is the "spec"
   you're agreeing to before code.
3. Run **apply** to turn the accepted proposal into changes:

   ```
   openspec apply add-transfer-limit
   ```

4. Once satisfied, **archive** the completed change:

   ```
   openspec archive add-transfer-limit
   ```

5. **Find the slash commands.** Open `.github/prompts/` and note the
   `*.prompt.md` files — these appear as `/propose`, `/apply`, `/archive`-style
   commands inside the app. Try invoking one from the session prompt.

**Expected outcome:** a generated proposal + specs + tasks for
`add-transfer-limit`; an applied change; an archived record; and visible
`.github/prompts/*.prompt.md` slash commands.

### ✅ Checkpoint

You should now see:

- An `add-transfer-limit` proposal with specs and a task list.
- The change **applied**, then **archived**.
- `.github/prompts/*.prompt.md` files surfaced as slash commands in the app.

### 🚀 Stretch goal

Contrast OpenSpec with **GitHub Spec Kit** (`/specify`, `/plan`, `/tasks`):
heavier, template + CLI driven, agent-agnostic. When would each fit NorthBank?

### 🆘 If your agent stalls

If the live `openspec` run stalls or the CLI isn't installed, the **pre-baked
artefacts are in `openspec-demo/`** — open that folder to read a completed
propose → apply → archive cycle for `add-transfer-limit` and the generated
prompt files, and carry on from there.

---

# What you learned — take-it-home checklist

Tick these off. You have now, hands-on:

- [ ] Connected a repo and run **local** agent sessions in the Copilot app.
- [ ] Turned an issue into a **structured task list** with a skill (Ex 1).
- [ ] Compared designs and written an **ADR** on a canvas (Ex 2).
- [ ] Implemented a multi-file **C# feature** and reviewed the diff *before*
      applying (Ex 3).
- [ ] Done **declarative schema** work with Atlas and written **tSQLt** tests,
      and made a query **SARGable** (Ex 4).
- [ ] Refactored **legacy Delphi** safely and scaffolded **DUnitX** +
      **Appium/NovaWindows** tests (Ex 5).
- [ ] **Authored, installed and invoked your own skill** (Ex 6).
- [ ] Run a **Copilot code review**, caught the planted bug, used **Agent
      Merge**, and set up an **Automation** (Ex 7).
- [ ] Run an **OpenSpec** propose → apply → archive loop and found the
      `/propose`-style slash commands (Ex 8).

### Habits to take back to your day job

- **Read the Canvas diff before you apply or merge.** Always. The agent proposes; you decide.
- **Steer, don't restart.** Most "bad" runs are fixed with one more sentence.
- **One session per unit of work** — isolated worktrees keep parallel work safe.
- **Teach the agent your rules with skills** — a tight `description` is what
  makes a skill fire at the right moment.
- **Spec before code for anything non-trivial** — OpenSpec keeps the agent (and
  you) honest.
- **Let Automations do the boring vigilance** — nightly dependency/CVE checks,
  compliance sweeps.

### Keep exploring

- **Addy Osmani `agent-skills`** — 24 lifecycle skills, Copilot-compatible.
- **Matt Pocock's short-form skills** — how small a good `SKILL.md` can be.
- **`github/awesome-copilot`** — a broader community collection.
- **OpenSpec** and **GitHub Spec Kit** — two takes on spec-driven development.

Well done — you drove a change from issue to merge, and taught Copilot a new
trick along the way. 🎉
