---
name: catalyst-zoho-mcp
description: "Catalyst Zoho MCP — manage Catalyst infrastructure (tables, buckets, cache) via CatalystbyZoho_* MCP tools using natural language. Trigger on 'Zoho MCP', 'MCP tools', 'catalyst MCP', 'CatalystbyZoho', 'create table with AI', 'MCP setup', 'MCP config', or 'infrastructure as conversation'."
compatibility: "Requires an MCP-capable AI host: Claude Desktop, VS Code with GitHub Copilot, or Cursor."
metadata:
  version: "2.0.0"
---

## How It Works

1. **Verify MCP is connected** — Look for `CatalystbyZoho_*` in the tool list.
   - If tools are **present**: proceed to step 2.
   - If tools are **absent**: run the setup flow below before doing anything else.
2. **Pre-flight sequence** — Always run `CatalystbyZoho_List_All_Organizations` → `CatalystbyZoho_List_All_Projects` first to set project context before any other MCP tool call.
3. **Load `references/zoho-mcp.md`** — for the full tool catalog, execution flow, and common error fixes.
4. **Answer** — Call the appropriate `CatalystbyZoho_*` tool directly. Show the user what tool was called and what it returned.

## MCP Setup Flow (when tools are absent)

If `CatalystbyZoho_*` tools are not in the tool list:

1. Check whether `.mcp.json` exists in the project root.
   - If **missing**: tell the user the plugin ships a `.mcp.json` pre-configured for the **US data center** — they can copy it from the plugin directory or create it manually (see `references/zoho-mcp.md` for the snippet). Then proceed to step 2.
   - If **present**: the file exists but the server isn't connecting — likely a DC mismatch or the MCP server hasn't been created yet. Proceed to step 2.
2. Ask: *"Which data center is your Catalyst account on? (US / EU / IN / AU / JP / SA / CA)"*
   - If **US**: the default `.mcp.json` endpoint is already correct. Guide the user to create their MCP server at [mcp.zoho.com](https://mcp.zoho.com) and verify the connection (see `references/zoho-mcp.md` setup section).
   - If **any other DC**: load `references/zoho-mcp.md` (DC endpoints table) and guide them to update `.mcp.json` with the correct endpoint for their region.
3. Tell the user to restart Claude Code after saving `.mcp.json`.
4. After restart, verify by checking for `CatalystbyZoho_*` tools — if still missing, check the common errors table in `references/zoho-mcp.md`.

## DC Switch Flow (when user wants to change data center)

If the user says they are on a non-US DC or asks how to change the MCP endpoint:

1. Load `references/zoho-mcp.md` (DC endpoints table).
2. Give the user the correct `url` value for their DC.
3. Tell them to update the `url` field in `.mcp.json` and restart Claude Code.

## Triggers

Use this skill for: "Zoho MCP", "MCP tools", "catalyst MCP", "create table with AI", "MCP DataStore", "MCP Cache", "MCP Stratus", `zoho-mcp-server`, "MCP setup", "MCP config", "infrastructure as conversation", "Claude MCP Catalyst", "MCP tool error", "MCP not connecting", "use AI to create database table", or `CatalystbyZoho_` tool.

## References

| Reference | Load when the query is about… |
|-----------|-------------------------------|
| `references/zoho-mcp.md` | MCP server setup (Claude Desktop + VS Code), all available CatalystbyZoho_* tools, execution flow, org→project pre-flight sequence, common MCP errors |
