---
name: catalyst-basics
description: "Core Catalyst project setup — directory structure, environments, CLI commands, and all Catalyst IDs (Project ID, ZAID, Table ID, Segment ID, Org ID). Trigger on 'start a Catalyst project', 'what is .catalystrc', 'where do I find my Table ID', 'difference between Development and Production', or any Catalyst ID or CLI question."
metadata:
  version: "2.2.0"
---

> **⚠️ PRE-FLIGHT CHECK:** Before creating any project files, confirm that `.catalystrc` and
> `catalyst.json` exist in the project directory. If they don't:
> 1. Use MCP (`CatalystbyZoho_List_All_Organizations` → `CatalystbyZoho_List_All_Projects`) to *list* orgs/projects.
> 2. **🔒 Never assume the org/project.** If there is no project initialized, or the project is ambiguous, **STOP and ask the user which project (and org) to work in** — a single project in the list is not permission to auto-select it; confirm first. Never infer the project from the directory name or a past session. (Full rule: `references/preflight.md` → Golden Rule.)
> 3. Only after the project is resolved **and confirmed** run: `catalyst init --org <orgId> -p <projectId> -ni`
> 4. **Never ask the user to run `catalyst init` interactively. Never create these files manually** — they are CLI-generated and must come from the CLI.
>
> **Always run the `catalyst` CLI in non-interactive mode.** Every command takes a `-ni` / `--non-interactive` flag (or set `ZCATALYST_NON_INTERACTIVE=1`; requires CLI v1.27.0+) and accepts its inputs as flags, so the CLI never prompts. Interactive menus exist for humans only — agents must never rely on them. The one exception is `catalyst login`, whose browser OAuth step needs the user. See the required flags per command in `references/cli.md`.

## How It Works

1. **Pre-flight first** — Establish and verify org/project context per the canonical sequence in `references/preflight.md` (read `.catalystrc` for org/project, confirm parity with MCP, or resolve via MCP if `.catalystrc` is absent). Use MCP for all other resource IDs instead of asking the user.
2. **New to Catalyst?** — If the user is setting up Catalyst for the first time or asking "how do I start", load `references/quick-start.md` for the full walkthrough.
3. **Console navigation** — If the user asks how to find IDs, create tables, set permissions, or configure CORS in the console, load `references/console-ui-guide.md`.
4. **Project structure** — Load `references/project-basics.md` for directory layout, `catalyst.json`, IDs, and dev-to-prod checklist.
5. **CLI questions** — Load `references/cli.md` for the exact command, flags, and safety rules.
6. **Answer** — Provide the specific ID path or CLI command needed. Never ask the user to manually look up IDs when MCP is connected.

## 🧭 Establishing Org + Project Context

**Follow the single canonical pre-flight:** `references/preflight.md`. It is the authoritative source — do not restate its steps here.

> **🔒 Never assume the org or project.** Resolve them from `.catalystrc` or a user-confirmed MCP list — never guess, never auto-pick a lone project without confirming, never carry a project over from another session or infer it from the folder name. **If no project is initialized or it can't be resolved, ask the user which project (and org) to start with before doing anything.**

**For all other resource details** (table names, bucket names, ZAIDs, etc.), use MCP tools directly — do NOT ask the user to copy-paste IDs from the console.

**If MCP tools are NOT available:**
- Prompt the user to connect Zoho MCP before proceeding
- Guide them to: **VS Code → Settings → MCP** (or Claude Desktop `claude_desktop_config.json`) and add the Catalyst MCP server
- Fall back to reading `.catalystrc` and `catalyst.json` from the local project directory only as a last resort

> **Never ask the user to manually look up IDs from the console** if MCP is connected. Every project detail — org ID, project ID, table IDs, ZAIDs, bucket names — is retrievable via MCP tools. Asking the user to hunt for IDs when MCP is available wastes time and introduces copy-paste errors.

## References

Load the relevant reference file for detailed information:

| Reference | Contents |
|-----------|----------|
| `references/preflight.md` | **Workspace readiness gate** — the canonical pre-flight every skill links to: establish + reconcile org/project (`.catalystrc` ↔ MCP via `Get_Project_By_Id`), environment awareness, and how it pairs with the SessionStart hook |
| `references/quick-start.md` | First-time setup — install CLI, `catalyst init … -ni`, find org/project IDs, add a function, serve locally, deploy, create a Data Store table, configure CORS/Authorized Domains |
| `references/console-ui-guide.md` | Console navigation — finding Project ID/ZAID/Table ID, creating tables with typed columns, enabling App User permissions (Scopes and Permissions), Authorized Domains + CORS toggle |
| `references/project-basics.md` | Project directory structure, `catalyst.json`, `.catalystrc`, `catalyst-config.json`, environments, dev-to-prod checklist, all Catalyst IDs (Project ID, ZAID, Table ID, Segment ID, etc.) |
| `references/cli.md` | Full CLI command reference — all subcommands with flags, Slate/AppSail non-interactive setup, `catalyst serve` port behavior, deploy scoping, safety rules, resource-first development order |
| `references/architecture.md` | Service selection guide — which Catalyst service to use for which pattern, typical stack combinations, DC availability table for regionally restricted services |
| `references/setup/claude-code.md` | Installing skills and connecting Zoho MCP in Claude Code (project `.mcp.json`) |
| `references/setup/cursor.md` | Installing skills and connecting Zoho MCP in Cursor |
| `references/setup/github-copilot.md` | Installing skills and connecting Zoho MCP in GitHub Copilot (VS Code) |

