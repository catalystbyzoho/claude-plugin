# Catalyst Agent Skills

A collection of AI agent skills for [Catalyst by Zoho](https://catalyst.zoho.com) — the full-stack serverless cloud platform.

These skills give AI coding agents (Claude, etc.) deep knowledge of Catalyst's platform, services, SDK patterns, and best practices — enabling them to write deployment-ready Catalyst code, recommend the right architecture, and even manage infrastructure directly via Zoho MCP tools.

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

### Option 1: Claude Code (Plugin)

> **Prerequisites:** You must have the [Claude Code CLI](https://docs.anthropic.com/en/docs/claude-code/overview) installed. Open your **system terminal** (not the Claude Code desktop app or web interface) and start a session with `claude` before running the commands below.

**Steps:**

1. Register this repository as a plugin marketplace source:
   ```
   /plugin marketplace add catalystbyzoho/agent-skills
   ```

2. Install the Catalyst skill plugin:
   ```
   /plugin install catalyst-by-zoho@catalyst-by-zoho
   ```

3. Set up Zoho MCP so Claude can manage Catalyst infrastructure (create tables, cache) directly from the conversation:
   1. Go to [mcp.zoho.com](https://mcp.zoho.com) → create or open an MCP server
   2. Under **Tools → Config Tools**, search for **"Catalyst by Zoho"** and add it
   3. Under **Connections**, set authorization to **"On Demand"**
   4. Copy your MCP server URL (format: `https://<server>-<org>.zohomcp.com/mcp/<token>/message`)
   5. In your project, open `.mcp.json` and replace the `<YOUR_ZOHO_MCP_URL>` placeholder with your URL

   > For full details and verification steps, see the [Zoho MCP setup section](#zoho-mcp-setup-recommended-all-tools) below.


### Option 2: Gemini CLI (Extension)

```bash
gemini extensions install https://github.com/catalystbyzoho/agent-skills
```

### Option 3: Cursor

**Via symlink (recommended):**
```bash
git clone https://github.com/catalystbyzoho/agent-skills.git
ln -s /path/to/catalyst-skills/skills/catalyst-by-zoho ~/.cursor/rules/catalyst-by-zoho
```

**Or via the plugin manifest:** The `.cursor-plugin/plugin.json` in this repo points Cursor
to the `skills/` directory automatically if Cursor supports plugin manifests in your version.
Alternatively, copy the `.cursor-plugin/` and `skills/` folders into your project root.

### Option 4: GitHub Copilot

1. Clone the repo:
   ```bash
   git clone https://github.com/catalystbyzoho/agent-skills.git
   ```
2. **Append** the contents of `skills/SKILL.md` to your project's `.github/copilot-instructions.md` — do not replace the file if it already has content.

   > **Strip the frontmatter first.** `SKILL.md` starts with a YAML frontmatter block that `copilot-instructions.md` does not support. Remove everything between and including the opening and closing `---` lines before appending:
   > ```
   > ---
   > name: catalyst-by-zoho
   > description: >
   >   ...
   > ---
   > ```
   > Everything after the second `---` is the skill content — that's what you append.

   Quick one-liner to append with frontmatter stripped:
   ```bash
   awk '/^---/{f++; next} f>=2' skills/SKILL.md >> .github/copilot-instructions.md
   ```

3. Copy the `skills/references/` folder into `.github/references/` for full context

### Option 5: Windsurf

1. Clone the repo:
   ```bash
   git clone https://github.com/catalystbyzoho/agent-skills.git
   ```
2. Copy `skills/` into your project's `.windsurfrules/` directory

### Option 6: OpenAI Codex

1. Install the skill as a plugin:
   ```bash
   codex plugin marketplace add catalystbyzoho/agent-skills
   ```
2. Open Codex and run `/plugins` to verify `catalyst-by-zoho` appears in the active list.
3. Add your Zoho MCP server URL to Codex's MCP settings (see **Zoho MCP setup** below).

### Option 7: Kiro

1. Clone the repo (if you haven't already):
   ```bash
   git clone https://github.com/catalystbyzoho/agent-skills.git
   ```
2. Symlink the skill into Kiro's steering directory:
   ```bash
   ln -s "$(pwd)/agent-skills/skills/catalyst-by-zoho" ~/.kiro/steering/catalyst-by-zoho
   ```
3. Add your Zoho MCP server to Kiro's MCP config (`~/.kiro/settings/mcp.json`):
   ```json
   {
     "mcpServers": {
       "zoho-catalyst": {
         "url": "https://<your-server>-<org>.zohomcp.com/mcp/<token>/message",
         "type": "http"
       }
     }
   }
   ```
4. Restart Kiro — the skill and MCP tools will be available in new conversations.

### Option 8: Any other AI tool (Manual)

1. Clone the repo:
   ```bash
   git clone https://github.com/catalystbyzoho/agent-skills.git
   ```
2. Point your AI tool to the `skills/` directory, or copy its contents to wherever your tool reads skill/instruction files

### Zoho MCP setup (recommended, all tools)

To let the AI manage Catalyst infrastructure (create tables, query data, manage cache/buckets) directly:

1. Create a Zoho MCP server at [mcp.zoho.com](https://mcp.zoho.com)
2. Add **"Catalyst by Zoho"** tools to the server
3. Enable **"On Demand"** authorization (not "Authorization via Connection")
4. Copy your MCP server URL — go to the **Connect tab** → **Server URL** field (format: `https://<server>-<org>.zohomcp.com/mcp/<token>/message`)
5. Add it to your tool's MCP config, or update the `<YOUR_ZOHO_MCP_URL>` placeholder in `.mcp.json`

> **Note:** `.mcp.json` ships with a placeholder URL. See the `_setup` field inside for instructions.

See `skills/references/zoho-mcp-tools.md` for detailed setup and verification steps.

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
