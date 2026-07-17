---
name: catalyst-zoho-mcp
description: "Catalyst Zoho MCP — manage Catalyst infrastructure (tables, buckets, cache) via CatalystbyZoho_* MCP tools using natural language. Trigger on 'Zoho MCP', 'MCP tools', 'catalyst MCP', 'CatalystbyZoho', 'create table with AI', 'MCP setup', 'MCP config', 'global MCP server', 'infrastructure as conversation', 'MCP first', 'avoid Catalyst console', or 'use MCP instead of console', 'switch DC', 'change data center'."
compatibility: "Requires an MCP-capable AI host: Claude Desktop, VS Code with GitHub Copilot, or Cursor."
metadata:
  version: "2.2.1"
---

## How It Works

1. **Check if MCP is connected** — Look for `CatalystbyZoho_*` tools in your tool list.

2. **⛔ MCP NOT connected — HARD STOP.**
   Do NOT write any code, create any files, or call any SDK.
   Ask the user which setup path they prefer, then load `references/zoho-mcp.md` and follow only that path.
   Do not proceed to step 3 until the user confirms `CatalystbyZoho_*` tools are visible in their client.

   > **To work with Catalyst via MCP, choose your setup path:**
   >
   > **Option A — Global MCP Server** *(recommended)*
  > Add your DC-specific URL to your AI client config, authorize once via browser, done. No console needed.
  > US: `https://catalyst.zohomcp.com/mcp/message`, EU: `https://catalyst.zohomcp.eu/mcp/message`, IN: `https://catalyst.zohomcp.in/mcp/message`, AU: `https://catalyst.zohomcp.com.au/mcp/message`, CA: `https://catalyst.zohomcp.ca/mcp/message`, SA: `https://catalyst.zohomcp.sa/mcp/message`, JP: `https://catalyst.zohomcp.jp/mcp/message`, UAE: `https://catalyst.zohomcp.ae/mcp/message`.
   >
   > **Option B — Personal MCP Server** *(custom/team setup)*
   > Create your own server at mcp.zoho.com, select tools, get a personal URL with a token embedded.
   >
   > *Which would you prefer?*

3. **DC switch request?** — If the user asks to switch data centers (e.g. "connect to IN DC", "switch to EU", "logout of MCP server"), load `references/dc-switching.md` and follow it. Do not use `zoho-mcp.md` for this.

4. **Pre-flight sequence** — Always run `CatalystbyZoho_List_All_Organizations` → `CatalystbyZoho_List_All_Projects` first to set project context before any other MCP tool call.

5. **Load `references/zoho-mcp.md`** — for the full tool catalog, execution flow, and common error fixes.

6. **If the query involves DataStore** (create table, add columns, query data) — also load `references/mcp-datastore.md`.

7. **Answer** — Call the appropriate `CatalystbyZoho_*` tool directly. Show the user what tool was called and what it returned.

## Triggers

Use this skill for: "Zoho MCP", "MCP tools", "catalyst MCP", "create table with AI", "MCP DataStore", "MCP Cache", "MCP Stratus", `zoho-mcp-server`, "MCP setup", "MCP config", "global MCP server", "infrastructure as conversation", "Claude MCP Catalyst", "MCP tool error", "MCP not connecting", "use AI to create database table", `CatalystbyZoho_` tool, "switch DC", "change data center", "connect to IN DC", "switch to EU", "logout of MCP server".

## References

| Reference | Load when the query is about… |
|-----------|-------------------------------|
| `references/zoho-mcp.md` | Global MCP server setup (all 3 clients), all available CatalystbyZoho_* tools, execution flow, org→project pre-flight sequence, common MCP errors |
| `references/dc-switching.md` | Switching data centers on any client, including Claude Code plugin cache paths |
| `references/mcp-datastore.md` | Creating tables/columns via MCP, DataStore column types, batch column creation, data type constraints |
