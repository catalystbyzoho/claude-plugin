# Catalyst by Zoho — Agent Rules

> This file serves two purposes: (1) MCP tool routing for agents with Zoho MCP connected,
> and (2) behavioral rules every agent should follow when working on Catalyst projects.
> For complete SDK patterns, architecture guidance, and service details, use the relevant skill
> under `skills/` (start with `skills/catalyst-basics/SKILL.md`) and its reference files.

---

## MCP Tool Usage

- Leverage Catalyst services for serverless compute, relational data storage, object storage, AI/ML, and workflow orchestration.
- When the Zoho MCP `ZohoMCP_*` meta-tools (`ZohoMCP_getSchema`, `ZohoMCP_executeTool`, `ZohoMCP_listTools`, `ZohoMCP_getFeatures`) are available in your tool list, MCP is connected — use it for infrastructure operations (create tables, query data, manage buckets/cache) instead of asking the user to do it manually in the console. Each operation is a `CatalystbyZoho_*` name passed as the `tool_name` argument to `ZohoMCP_executeTool` (fetch its schema first with `ZohoMCP_getSchema`); the `CatalystbyZoho_*` names are never shown as tools themselves.
- **MCP pre-flight — follow the single canonical sequence:** `skills/catalyst-basics/references/preflight.md`. Run it **once per session** (not before every call — trust it once it passes). In short: establish the active org (`.catalystrc` → `projects[].env[].id`) and project (`projects[].id`), then confirm the MCP account and CLI agree via `CatalystbyZoho_Get_Project_By_Id` before you start operating. If `.catalystrc` is absent, resolve via `List_All_Organizations` → `List_All_Projects`. Do not restate the steps here — that file is authoritative.
- Always default to `"Development"` environment unless the user explicitly requests production.
- If `List_All_Tables` returns `PERMISSION_NEEDED`, ask the user for the correct project ID from their Catalyst console URL (format: `.../project/<project_id>/...`).
- If the user asks to create tables, query data, manage cache, or set up infrastructure — use MCP tools directly instead of asking them to go to the console.

---

## Skill Discovery

Before starting any Catalyst task, load the most specific matching skill SKILL.md and its reference files:

| Task type | Load this skill / reference |
|-----------|------------------------------|
| Architecture / "what should I use for X", DC restrictions | `skills/catalyst-basics/references/architecture.md` |
| Function types, handler signatures, `catalyst-config.json`, API Gateway | `skills/catalyst-functions/SKILL.md` → `references/functions-basics.md` |
| File uploads, streaming, advanced function patterns | `skills/catalyst-functions/references/functions-advanced.md` |
| API Gateway routing, rate limits | `skills/catalyst-functions/references/api-gateway.md` |
| AppSail — persistent servers, Docker, managed runtimes | `skills/catalyst-appsail/SKILL.md` |
| Slate — frontend hosting, Git deploy, cross-domain calls | `skills/catalyst-slate/SKILL.md` |
| Data Store, ZCQL, table permissions, pagination | `skills/catalyst-datastore/SKILL.md` |
| Stratus — object storage, signed URLs, multipart upload | `skills/catalyst-stratus/SKILL.md` |
| NoSQL — document storage, flexible schema | `skills/catalyst-nosql/SKILL.md` |
| Authentication, login/signup, ZAID, Connections/OAuth | `skills/catalyst-authentication/SKILL.md` |
| Cache — in-memory key-value, TTL | `skills/catalyst-cache/SKILL.md` |
| Pricing, cost estimation, free tier, GB-seconds | `skills/catalyst-pricing/references/pricing-basics.md` |
| SDKs — Node.js, Web, Python, Java, Android, iOS, Flutter | `skills/catalyst-sdk/SKILL.md` |
| Zia Services, QuickML, OCR, AutoML | `skills/catalyst-zia/SKILL.md` |
| MCP tool setup, `CatalystbyZoho_*` tool calls | `skills/catalyst-zoho-mcp/references/zoho-mcp.md` |
| CLI commands, project init, `catalyst.json` structure | `skills/catalyst-basics/references/cli.md` |
| Project IDs, environments, directory layout | `skills/catalyst-basics/references/project-basics.md` |

If Tier 2 reference files don't contain the answer, use a site-scoped web search (`site:docs.catalyst.zoho.com <term>`) and fetch the specific page URL returned. Do NOT fabricate docs URLs — all Catalyst documentation lives under `https://docs.catalyst.zoho.com/en/`.

---

## CLI Usage — always non-interactive

- The `catalyst` CLI is interactive by default (arrow-key menus, prompts). Agents drive TUIs poorly, so **always run it in non-interactive (NI) mode.**
- Enable NI mode any of three equivalent ways (requires CLI v1.27.0+): the `-ni` flag, the `--non-interactive` flag, or the env var `ZCATALYST_NON_INTERACTIVE=1`.
- **Pass every input as a flag** so the CLI never has to prompt — e.g. `catalyst init --org <orgId> -p <projectId> -ni`, `catalyst functions:add --name <name> --type <type> --stack <stack> -ni`. The required flags per command are in `skills/catalyst-basics/references/cli.md`.
- Interactive prompts exist for humans; **agents must never rely on them.** The one unavoidable exception is `catalyst login`, which opens a browser for OAuth — use `catalyst login --dc <dc> -ni` to skip the DC-selection prompt, but the browser sign-in itself needs the user.

---

## Project Initialization

- Before creating any files, check for `.catalystrc` and `catalyst.json` in the working directory.
- If **both exist**: project is already initialized — do NOT re-scaffold or overwrite them.
- If `catalyst.json` exists but `.catalystrc` is missing: project is not linked — instruct the user to run `catalyst login --dc <dc> -ni` then `catalyst init --org <orgId> -p <projectId> -ni` (non-interactive; see the CLI Usage rule above).
- Only scaffold new project files if neither file exists.

---

## Code Conventions

- Follow Catalyst's strict directory layout: functions go under `functions/`, web client under `client/`, `catalyst.json` at project root.
- Always use the correct handler signature per function type — consult `skills/catalyst-functions/references/functions-basics.md` before writing any function code.
- For AppSail, always use `process.env.X_ZOHO_CATALYST_LISTEN_PORT` with a fallback port (e.g., `|| 9000`).
- When writing code that uses any Catalyst ID (Table ID, ZAID, Segment ID, Org ID, Project ID), always add an inline comment telling the user exactly where to find it in the console. Never leave ID placeholders unexplained.
- For new projects, never recommend the deprecated **standalone services** — File Store, Event Listeners, and the standalone Cron scheduler. Use **Stratus** (storage), **Signals** (data-change triggers), and **Job Scheduling** (scheduled execution) instead. Note this is distinct from the function *types*: the **Event function type** is still current (trigger it with Signals, not Event Listeners), and scheduled work should use the **Job function type** with Job Scheduling rather than the legacy Cron function type.

---

## Deployment Preferences

- Prefer `catalyst deploy -ni` from the CLI over manual console uploads (always non-interactive — see the CLI Usage rule).
- Always verify project structure matches Catalyst's requirements before suggesting deploy.
- After deployment, recommend checking **DevOps → Logs** for execution errors on first invocation.
- For scheduled (Job Scheduling) and event-driven (Signals) functions, proactively suggest configuring Application Alerts.

---

## Verification

- After writing code, verify it matches Catalyst's expected project structure and handler signatures.
- After infrastructure creation via MCP tools, verify access with a read operation (e.g., `List_All_Tables`) before performing writes.
- Never leave placeholder IDs unexplained — always tell the user where to find the real value in the console.
