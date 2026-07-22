# Using Catalyst Skills with Cursor

> Shared steps — Installation, getting your Zoho MCP URL (MCP Setup Step 1), and the pre-flight checklist — are in `setup-common.md`. This file covers only what's specific to Cursor.

## Skill Activation

Cursor reads skills from the `skills/` directory in your workspace. To verify:

1. Open Cursor in your Catalyst project directory
2. Open the Composer (Cmd+I / Ctrl+I)
3. Ask: "What Catalyst skills are available?"

## MCP Setup — Step 2: Add to Cursor MCP config

First complete **Step 1** in `setup-common.md` to get your Zoho MCP URL. Then create or edit `.cursor/mcp.json` in your project root:

```json
{
  "mcpServers": {
    "catalyst-by-zoho": {
      "type": "streamable-http",
      "url": "https://<server-name>-<org-id>.zohomcp.com/mcp/<auth-token>/message"
    }
  }
}
```

Alternatively, configure globally via **Cursor Settings → MCP → Add Server** and paste the URL.

After saving, restart Cursor. Confirm MCP is active by checking **Cursor Settings → MCP** for a green status indicator next to `catalyst-by-zoho`.

## Common Errors (Cursor)

See `setup-common.md` for errors common to all IDEs. Cursor-specific:

| Error | Cause | Fix |
|-------|-------|-----|
| MCP not connecting | Config path wrong or URL placeholder not replaced | Verify the URL in `.cursor/mcp.json` is your actual Zoho MCP URL from mcp.zoho.com |
| MCP shows red/error status | Invalid or expired URL | Regenerate the URL at mcp.zoho.com and update the config |
