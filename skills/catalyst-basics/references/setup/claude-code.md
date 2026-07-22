# Using Catalyst Skills with Claude Code

> Shared steps — Installation, getting your Zoho MCP URL (MCP Setup Step 1), and the pre-flight checklist — are in `setup-common.md`. This file covers only what's specific to Claude Code.

## Skill Activation

Claude Code picks up skills automatically from the `skills/` directory. To verify:

1. Open Claude Code in your Catalyst project directory
2. Ask: "What Catalyst skills are available?"
3. Claude will list the active skills from `catalyst-by-zoho/SKILL.md`

## MCP Setup — Step 2: Add the MCP server to your project

First complete **Step 1** in `setup-common.md` to get your Zoho MCP URL. Claude Code reads MCP servers from a `.mcp.json` file in your **project root** (this is different from Claude Desktop, which uses `claude_desktop_config.json`). Create or edit `.mcp.json`:

```json
{
  "mcpServers": {
    "catalyst-by-zoho": {
      "type": "streamable-http",
      "url": "https://catalyst.zohomcp.com/mcp/<auth-token>/message"
    }
  }
}
```

The simplest path is to copy the `.mcp.json` file from this repo into your project root and replace the `<YOUR_ZOHO_MCP_URL>` placeholder. Alternatively, run `claude mcp add` to register the server interactively.

After saving, restart Claude Code (or run `/mcp` to reconnect). Confirm MCP is connected by looking for the `ZohoMCP_*` meta-tools (`ZohoMCP_getSchema`, `ZohoMCP_executeTool`, `ZohoMCP_listTools`, `ZohoMCP_getFeatures`) in the tool list. (The `CatalystbyZoho_*` operations are not listed as tools — they are `tool_name` values you pass to `ZohoMCP_executeTool`.)

## Common Errors (Claude Code)

See `setup-common.md` for errors common to all IDEs. Claude Code-specific:

| Error | Cause | Fix |
|-------|-------|-----|
| MCP tools not appearing | `.mcp.json` not saved or Claude Code not reconnected | Save `.mcp.json` in the project root, then restart Claude Code or run `/mcp` |
