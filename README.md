# Catalyst Agent Skills

A collection of AI agent skills for [Catalyst by Zoho](https://catalyst.zoho.com) — the full-stack serverless cloud platform.

These skills give Claude Code (and Cowork) deep knowledge of Catalyst's platform, services, SDK patterns, and best practices — enabling it to write deployment-ready Catalyst code, recommend the right architecture, and even manage infrastructure directly via Zoho MCP tools.

## What's included

| Skill | Description |
|-------|-------------|
| `catalyst-by-zoho` | Complete Catalyst development assistant — covers all services, SDKs, CLI, architecture patterns, pricing, migration guides, and Zoho MCP tool-based resource management |

> **Note:** For edge-case lookups not covered by the Tier 1 or Tier 2 reference files, the skill instructs agents to search the official Catalyst docs site (`docs.catalyst.zoho.com`) and fetch individual pages — rather than bundling the full documentation dump locally. This keeps the repo lean while ensuring accurate, up-to-date answers.

### The skill covers

- **Compute**: Functions (7 types), AppSail (PaaS with Docker support)
- **Storage**: Data Store, ZCQL, Stratus (S3-compatible), NoSQL, Cache
- **Frontend**: Slate (Git-based, SSR support), Web Client Hosting
- **Integration**: Signals (event bus), Connections (OAuth manager)
- **Orchestration**: Circuits (workflows), Job Scheduling, Pipelines (CI/CD)
- **AI/ML**: Zia Services, QuickML, ConvoKraft (chatbots)
- **Browser Automation**: SmartBrowz (headless browser)
- **DevOps**: Logs, APM, Alerts, GitHub integration
- **Developer Tools**: CLI, SDKs (Node.js/Java/Python/Web/Android/iOS/Flutter), REST APIs, VS Code Extension
- **Zoho MCP**: Create tables, query data, manage buckets/cache directly from LLM conversations

## Installation

### Claude Code / Cowork (Plugin)

> **Prerequisites:** You must have the [Claude Code CLI](https://docs.anthropic.com/en/docs/claude-code/overview) installed. Open your **system terminal** (not the Claude Code desktop app or web interface) and start a session with `claude` before running the commands below.

**Steps:**

1. Register this repository as a plugin marketplace source:
   ```
   /plugin marketplace add catalystbyzoho/claude-plugin
   ```

2. Install the Catalyst skill plugin:
   ```
   /plugin install catalyst-by-zoho@catalyst-by-zoho
   ```

3. The plugin ships with the Catalyst MCP server **pre-configured** in `.mcp.json`, so Claude can manage Catalyst infrastructure (create tables, query data, manage cache/buckets) directly from the conversation — no manual MCP setup or URL generation required.

   > **Region note:** `.mcp.json` defaults to the **US** data center endpoint (`https://catalystbyzoho.zohomcp.com/mcp/message`). If your Catalyst account is on a different DC (EU, IN, AU, JP, SA, CA), see the [Catalyst MCP server section](#catalyst-mcp-server) below.

### Catalyst MCP server

The plugin includes a dedicated **Catalyst MCP server** that lets Claude manage your Catalyst infrastructure (create tables, query data, manage cache/buckets) directly.

It comes pre-wired in `.mcp.json`:

```json
{
  "mcpServers": {
    "catalyst-by-zoho": {
      "type": "streamable-http",
      "url": "https://catalystbyzoho.zohomcp.com/mcp/message"
    }
  }
}
```

The default URL points to the **US** data center. If your Catalyst account lives in another region (EU, IN, AU, JP, SA, CA), update the `url` to the matching DC endpoint — or just ask Claude to switch it for you. The first time Claude invokes a Catalyst tool, you'll be prompted to authorize the connection.

See `skills/references/zoho-mcp-tools.md` for the full list of available tools and verification steps.

## What the skill enables

- **Architecture recommendations** — get Catalyst-specific service picks for your use case
- **Production-ready code generation** — correct handler signatures, SDK patterns, `catalyst-config.json`, and project structure that works with `catalyst deploy`
- **Platform migration guidance** — maps AWS, GCP, Azure, Vercel, Firebase, Supabase, and Heroku services to Catalyst equivalents
- **Infrastructure management via Zoho MCP** — create tables, insert data, run ZCQL queries, manage cache/buckets directly from the AI conversation
- **Pricing estimation** — detailed cost breakdowns with unit prices and free tier offsets

## Contributing

We welcome contributions! See [CONTRIBUTING.md](CONTRIBUTING.md) for guidelines.

## About CATALYST.md

`CATALYST.md` at the repo root is a **lightweight MCP routing stub** — it contains only
the minimal context needed for MCP-enabled tools (e.g. Claude Code with Zoho MCP connected)
to discover and invoke `CatalystbyZoho_*` tools correctly.

**It is not a replacement for the full skill.** If you are building a Catalyst application,
always use `skills/SKILL.md` (and its `references/` folder), which includes
the complete service catalog, SDK patterns, handler signatures, architecture guidance, and
all reference docs. `CATALYST.md` alone is insufficient for app development.

## License

This project is licensed under the Apache 2.0 License — see the [LICENSE](LICENSE) file for details.

## Resources

- [Catalyst Documentation](https://docs.catalyst.zoho.com/en/)
- [Catalyst Pricing](https://catalyst.zoho.com/pricing.html)
- [Catalyst GitHub](https://github.com/catalystbyzoho)
- [Zoho MCP](https://mcp.zoho.com/)
