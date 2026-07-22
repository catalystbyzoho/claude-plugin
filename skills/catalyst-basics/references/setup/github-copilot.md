# Using Catalyst Skills with GitHub Copilot (VS Code)

> Shared steps — Installation, getting your Zoho MCP URL (MCP Setup Step 1), and the pre-flight checklist — are in `setup-common.md`. This file covers only what's specific to GitHub Copilot in VS Code.

## Skill Activation

GitHub Copilot in VS Code picks up skills from the `skills/` directory. To verify:

1. Open the Copilot Chat panel (Ctrl+Shift+I / Cmd+Shift+I)
2. Ask: "What Catalyst skills are available?"

## MCP Setup — Step 2: Add to VS Code MCP config

First complete **Step 1** in `setup-common.md` to get your Zoho MCP URL. Then create `.vscode/mcp.json` in your workspace root. **Note the VS Code dialect differs:** the top-level key is `servers` (not `mcpServers`) and the type is `"http"` (not `"streamable-http"`):

```json
{
  "servers": {
    "catalyst-by-zoho": {
      "type": "http",
      "url": "https://<server-name>-<org-id>.zohomcp.com/mcp/<auth-token>/message"
    }
  }
}
```

Alternatively, configure globally via **VS Code Settings → MCP** (search "MCP" in Settings UI).

After saving, VS Code will prompt you to start the MCP server. Accept. Confirm it's running via **View → Output → MCP Server: catalyst-by-zoho**.

## Common Errors (GitHub Copilot)

See `setup-common.md` for errors common to all IDEs. Copilot-specific:

| Error | Cause | Fix |
|-------|-------|-----|
| MCP server not starting | URL in `.vscode/mcp.json` is a placeholder | Replace the placeholder with your actual URL from mcp.zoho.com |
| `ZohoMCP_*` meta-tools not appearing in Copilot | `.vscode/mcp.json` not detected or URL invalid | Check the MCP Output channel for errors; verify the URL is your actual Zoho MCP URL. (Only the `ZohoMCP_*` meta-tools appear — the `CatalystbyZoho_*` names never show in the tool list.) |
| Wrong environment targeted | Zoho MCP defaults to Development | Switch explicitly in the Zoho MCP console if needed |
