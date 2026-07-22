---
name: catalyst-zoho-mcp
description: "Catalyst Zoho MCP — manage Catalyst infrastructure (tables, buckets, cache) via CatalystbyZoho_* MCP tools using natural language. Trigger on 'Zoho MCP', 'MCP tools', 'catalyst MCP', 'CatalystbyZoho', 'create table with AI', 'MCP setup', 'MCP config', 'global MCP server', 'infrastructure as conversation', 'MCP first', 'avoid Catalyst console', or 'use MCP instead of console', 'switch DC', 'change data center'."
compatibility: "Requires an MCP-capable AI host: Claude Desktop, VS Code with GitHub Copilot, or Cursor."
metadata:
  version: "2.3.1"
---

## How It Works

1. **Check if MCP is connected** — Look for the `ZohoMCP_*` **meta-tools** (`ZohoMCP_getSchema`, `ZohoMCP_executeTool`, `ZohoMCP_listTools`, `ZohoMCP_getFeatures`) in your tool list. Their presence is the connectivity signal. The `CatalystbyZoho_*` names are **not** shown as tools — they are `tool_name` values passed to `ZohoMCP_executeTool`.

2. **⛔ MCP NOT connected — HARD STOP.**
   Do NOT write any code, create any files, or call any SDK.
   Ask the user which setup path they prefer, then load `references/zoho-mcp.md` and follow only that path.
   Do not proceed to step 3 until the `ZohoMCP_*` meta-tools are present in the tool list (this is the "MCP connected" signal — the `CatalystbyZoho_*` names never appear as tools).

   > **To work with Catalyst via MCP, choose your setup path:**
   >
   > **Option A — Global MCP Server** *(recommended)*
  > Add your DC-specific URL to your AI client config, authorize once via browser, done. No console needed. Pick your DC's URL from the table in `references/zoho-mcp.md` (setup) — or `references/dc-switching.md` to change DCs later.
   >
   > **Option B — Personal MCP Server** *(custom/team setup)*
   > Create your own server at mcp.zoho.com, select tools, get a personal URL with a token embedded.
   >
   > *Which would you prefer?*

3. **DC switch request?** — If the user asks to switch data centers (e.g. "connect to IN DC", "switch to EU", "logout of MCP server"), load `references/dc-switching.md` and follow it. Do not use `zoho-mcp.md` for this.

4. **Pre-flight sequence** — Once per session, before your first MCP tool call, follow the single canonical pre-flight in `../catalyst-basics/references/preflight.md`: read org (`projects[].env[].id`) and project (`projects[].id`) from `.catalystrc` and confirm parity with MCP via `CatalystbyZoho_Get_Project_By_Id`, or resolve via `List_All_Organizations` → `List_All_Projects` when `.catalystrc` is absent. Once it passes, trust the context for the rest of the session — do not re-verify before every call.

5. **Load `references/zoho-mcp.md`** — for the full tool catalog, execution flow, and common error fixes.

6. **If the query involves DataStore** (create table, add columns, query data) — also load `references/mcp-datastore.md`.

7. **Answer** — Invoke the appropriate `CatalystbyZoho_*` operation via `ZohoMCP_executeTool` (passing its name as the `tool_name` argument; fetch its schema first with `ZohoMCP_getSchema`). Show the user which operation was called and what it returned.

## Triggers

Use this skill for: "Zoho MCP", "MCP tools", "catalyst MCP", "create table with AI", "MCP DataStore", "MCP Cache", "MCP Stratus", `zoho-mcp-server`, "MCP setup", "MCP config", "global MCP server", "infrastructure as conversation", "Claude MCP Catalyst", "MCP tool error", "MCP not connecting", "use AI to create database table", `CatalystbyZoho_` tool, "switch DC", "change data center", "connect to IN DC", "switch to EU", "logout of MCP server".

## References

| Reference | Load when the query is about… |
|-----------|-------------------------------|
| `../catalyst-basics/references/preflight.md` | **The workspace readiness gate** (canonical pre-flight, lives in `catalyst-basics`) — establishing + verifying org/project (via `.catalystrc` + `Get_Project_By_Id` reconciliation, or `List_All_*` fallback) and environment awareness. The single source every skill links to. |
| `references/zoho-mcp.md` | Global MCP server setup (all 3 clients), all available CatalystbyZoho_* tools, execution flow, common MCP errors |
| `references/dc-switching.md` | Switching data centers on any client, including Claude Code plugin cache paths |
| `references/mcp-datastore.md` | Creating tables/columns via MCP, DataStore column types, batch column creation, data type constraints |
