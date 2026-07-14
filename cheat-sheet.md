# Agentic Coding 101 — Quick Reference

*GitHub Copilot app · NorthBank core banking · print & keep*

---

## Prompt patterns

**Weak:** `Fix the transfer bug.`
**Good:** `TransferService.Transfer accepts a 0 or negative amount, letting money flow the wrong way. Reject non-positive amounts and add an xUnit test.`

**Give context:** name the file/class, the rule, the expected behaviour, and the constraint (e.g. "money is `decimal`").

**Ask for a plan first:** end a prompt with *"Propose a plan"* (planning mode). You don't ask for the diff — each session runs in a worktree and the app shows the diff on the **Canvas** by default; review it there before you apply or merge.

**Iterate:** feed back the failing test or the review comment; don't re-explain from scratch. Small steps beat one giant prompt.

| Do | Avoid |
|---|---|
| State the file, rule, and expected outcome | "Make it better" |
| Ask for options/ADR on design calls | Let the agent pick silently |
| Read the Canvas diff before you apply | Merge without reading it |
| Paste the failing test back to iterate | Restart the whole prompt |

---

## Copilot app essentials

- **Sessions (local vs cloud):** agent runs; local on your machine, GitHub-hosted **cloud** sessions run remotely.
- **Worktrees:** each session runs in its own **isolated git worktree** — no clobbering your working copy.
- **Canvases:** shared surfaces showing plans, terminals, diffs, and workflow state.
- **My Work:** a queue of your sessions, issues, and PRs.
- **Automations:** recurring/background prompts (e.g. nightly checks).
- **Agent Merge:** guides a PR through review and merge.

> The Copilot **app** is the agent-native **desktop** app (Win/macOS/Linux, paid Copilot licence). It is **not** the VS Code plugin. Delphi: no native inline completion in RAD Studio — drive edits from the app; it treats Object Pascal as text.

---

## Skills

A **SKILL.md** is a folder of instructions Copilot loads when relevant.

```
.github/skills/<name>/SKILL.md
```

**Frontmatter + body:**
```markdown
---
name: pii-redaction-check
description: Flags account numbers, names, emails in logs/console.
---
Instructions the agent follows when the skill is invoked…
```

- **Author:** write the YAML frontmatter (`name`, `description`) + markdown instructions.
- **Install:** drop it under `.github/skills/`.
- **Invoke:** reference it by name in a prompt, or let Copilot auto-load it.

**Community:** Addy Osmani **`agent-skills`** (24 lifecycle skills) · Matt Pocock **short-form skills** (tiny SKILL.md files) · **`github/awesome-copilot`** collection.
**Use a pack:** clone it and `cp -R` the skill folders into `.github/skills/` — these repos aren't Copilot marketplaces, so `/plugin install` won't find them. Shortcut: `npx skills add addyosmani/agent-skills`.

---

## Plugins

Bundle **agents** (`*.agent.md`), **skills**, **hooks** (`hooks.json`), and **MCP** servers. A **marketplace** = a repo with `marketplace.json` under `.github/plugin`.

```
In the Copilot app:
Settings → Plugins → Install → Add Marketplace
   amilos/agentic-coding-101-marketplace
→ Install:  addyosmani-agent-skills
→ Install:  mattpocock-skills
```
Course marketplace bundles Addy Osmani + Matt Pocock skills (MIT, attributed). Plain skills repos aren't marketplaces — copy those straight into `.github/skills/`.

## MCP

**Model Context Protocol** — servers wiring external tools/data into the agent. Configured in **`mcp.json`** / **`.github/mcp.json`** or the repo coding-agent settings.

---

## Spec-driven (OpenSpec)

Lightweight, brownfield-first. Flow:

```
openspec propose add-idempotent-transfers   → review generated proposal/specs/tasks
openspec apply   add-idempotent-transfers   → implement
openspec archive add-idempotent-transfers   → close out
```

Generates `.github/prompts/*.prompt.md`, surfaced as `/propose`, `/apply`, `/archive` slash commands.

**Heavier alternative — GitHub Spec Kit:** templates + CLI, `/specify` `/plan` `/tasks`, agent-agnostic (30+ agents).

---

## Testing quick refs

| Tool | Use |
|---|---|
| **tSQLt** | SQL Server unit tests for stored procs |
| **Atlas** | Declarative schema-as-code migrations (mssql) |
| **DUnitX** | Delphi/Win32 unit tests |
| **Appium + NovaWindows** | Win32 VCL UI automation |

---

## Guardrails (banking)

- **Review every diff.** Read it before it lands.
- **Never merge unreviewed AI output.**
- **Money is `decimal` / `Currency`, never `float`.**
- **No PII in logs or prompts** (account numbers, names, emails).
- **Keep secrets out of context** — no keys/connection strings in prompts.
- **Respect licence & data policy** for anything the agent pulls in.

---

## Handy prompts (canonical)

- **Plan:** `Using the planning-and-task-breakdown skill, read ISSUES.md issue #1 and produce a structured task list with acceptance criteria and the files likely to change.`
- **Design/ADR:** `We retry failed transfers. Propose 2–3 designs to make Transfer idempotent so a retry never double-posts. Compare trade-offs, then write an ADR to docs/adr/0001-idempotent-transfers.md.`
- **Implement:** `Implement ISSUES.md issue #1 (daily transfer limit) across PaymentService. Update TransferService and add xUnit tests.`
- **Atlas migration:** `In atlas/schema.hcl, add a nullable Reference column of type nvarchar(140) to the LedgerEntries table, then generate the migration with atlas migrate diff.`
- **tSQLt:** `Write tSQLt tests for dbo.PostTransaction covering a normal post and a non-positive amount that must be rejected.`
- **SARGable query:** `Explain why db/reports_slow.sql is slow and rewrite it to be SARGable; suggest the index to add.`
- **Delphi refactor:** `Explain legacy/InterestCalc.pas, then refactor CalculateMonthlyInterest to use banker's rounding to 2 decimal places without changing the public signature.`
- **DUnitX:** `Generate DUnitX tests for CalculateMonthlyInterest including a rounding edge case.`
- **Appium:** `Scaffold an Appium + NovaWindows test that loads a statement for account 1001 in StatementViewer and asserts the list is populated.`
- **Author skill:** `Create a skill named pii-redaction-check that flags account numbers, names and emails written to logs or console. Put it in .github/skills/pii-redaction-check/SKILL.md, then run it over TransferService.cs.`
- **Review:** `Review this PR for banking-safety issues and summarise required changes.`
- **Automation:** `Every night, check for outdated NuGet dependencies and known CVEs and open an issue if any are found.`
