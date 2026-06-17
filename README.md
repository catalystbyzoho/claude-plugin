# Catalyst Claude Code Plugin

The official Claude Code plugin for [Catalyst by Zoho](https://catalyst.zoho.com) — the full-stack serverless cloud platform.

The plugin bundles two things:

- **Modular skills** — a set of focused, service-specific skills that give Claude Code (and Cowork) deep knowledge of Catalyst's platform, services, SDK patterns, and best practices, so it can write deployment-ready Catalyst code and recommend the right architecture.
- **A pre-configured MCP connector** — the Catalyst MCP server ships wired into `.mcp.json`, letting Claude manage your Catalyst infrastructure directly (create tables, query data, manage cache/buckets) straight from the conversation.

## What's included

### Skills

The skills are modularised — instead of one monolithic skill, each Catalyst service or concern is its own focused skill, loaded on demand only when relevant. `catalyst-by-zoho` is the top-level entry point that routes to the specialised skills below.

| Skill | Description |
|-------|-------------|
| `catalyst-by-zoho` | Top-level entry point — expert Catalyst assistant that routes to the specialised skills below |
| `catalyst-basics` | Project setup, directory structure, environments, CLI commands, and all Catalyst IDs (Project ID, ZAID, Table ID, Segment ID, Org ID) |
| `catalyst-functions` | Serverless functions — all 7 types, handler signatures, `catalyst-config.json`, Security Rules, API Gateway routing, file uploads, middleware, and testing |
| `catalyst-appsail` | AppSail — persistent backend PaaS with managed runtimes (Node.js, Java, Python) and custom Docker containers |
| `catalyst-datastore` | Data Store — relational cloud database with ZCQL, CRUD, table permissions, and pagination |
| `catalyst-nosql` | NoSQL — non-relational document database for unstructured/JSON-heavy data with flexible per-item schema |
| `catalyst-cache` | Cache — in-memory key-value store with TTL for ephemeral session and temporary data |
| `catalyst-stratus` | Stratus — S3-compatible object storage with upload/download, signed URLs, and multipart upload |
| `catalyst-slate` | Slate — Git-based frontend hosting for React, Next.js, Vue, Angular, Svelte, Astro, and more |
| `catalyst-authentication` | Authentication — login/signup, ZAID, Web SDK auth flows, Security Rules, and OAuth via Connections |
| `catalyst-sdk` | SDKs — initialization and method reference for Node.js, Web, Python, Java, Android, iOS, and Flutter |
| `catalyst-zia` | Zia Services and QuickML — OCR, Face Analytics, Text Analytics, Object Detection, Barcode Reader, Content Moderation, and AutoML |
| `catalyst-pricing` | Pricing — free tier limits, pay-as-you-go rates, GB-seconds calculation, and cost estimation |
| `catalyst-zoho-mcp` | Zoho MCP — manage Catalyst infrastructure (tables, buckets, cache) via `CatalystbyZoho_*` MCP tools using natural language |

> **Note:** For edge-case lookups not covered by a skill's bundled reference files, skills instruct agents to search the official Catalyst docs site (`docs.catalyst.zoho.com`) and fetch individual pages — rather than bundling the full documentation dump locally. This keeps the repo lean while ensuring accurate, up-to-date answers.

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

See `skills/catalyst-zoho-mcp/references/zoho-mcp.md` for the full list of available tools and verification steps.

## What the plugin enables

- **Architecture recommendations** — get Catalyst-specific service picks for your use case
- **Production-ready code generation** — correct handler signatures, SDK patterns, `catalyst-config.json`, and project structure that works with `catalyst deploy`
- **Platform migration guidance** — maps AWS, GCP, Azure, Vercel, Firebase, Supabase, and Heroku services to Catalyst equivalents
- **Infrastructure management via Zoho MCP** — create tables, insert data, run ZCQL queries, manage cache/buckets directly from the AI conversation
- **Pricing estimation** — detailed cost breakdowns with unit prices and free tier offsets

## About CATALYST.md

`CATALYST.md` at the repo root is a **lightweight MCP routing stub** — it contains only
the minimal context needed for MCP-enabled tools (e.g. Claude Code with Zoho MCP connected)
to discover and invoke `CatalystbyZoho_*` tools correctly.

**It is not a replacement for the skills.** If you are building a Catalyst application,
always use the skills under `skills/` (each with its own `references/` folder), which
together cover the complete service catalog, SDK patterns, handler signatures, architecture
guidance, and all reference docs. `CATALYST.md` alone is insufficient for app development.

## License

This project is licensed under the Apache 2.0 License — see the [LICENSE](LICENSE) file for details.

## Resources

- [Catalyst Documentation](https://docs.catalyst.zoho.com/en/)
- [Catalyst Pricing](https://catalyst.zoho.com/pricing.html)
- [Catalyst GitHub](https://github.com/catalystbyzoho)
- [Zoho MCP](https://mcp.zoho.com/)
