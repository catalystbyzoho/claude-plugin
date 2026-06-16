---
name: catalyst-by-zoho
description: "Expert coding assistant for Catalyst by Zoho — Zoho's full-stack serverless cloud platform. Use for any question about Catalyst services, CLI, SDKs, architecture, pricing, or Zoho MCP tool-based infrastructure management."
metadata:
  version: "2.0.0"
---

# Catalyst by Zoho — Skill Index

This is the routing layer. Load the most specific matching skill — do not answer from this file alone.

---

## Philosophy

- **Prefer MCP over asking.** If `CatalystbyZoho_*` tools are available, use them. Never ask the user to copy IDs from the console.
- **Default to Development.** Always target the Development environment unless the user explicitly says "production" or "deploy to prod".
- **Prefer Functions over AppSail for simple HTTP.** Functions are cheaper (per-invocation billing), simpler to deploy, and require no infrastructure management. Reach for AppSail only when the use case genuinely requires a persistent process, WebSockets, or a custom runtime.
- **Show cost before building.** For any new infrastructure (functions, AppSail, Stratus buckets), load `catalyst-pricing` and give a brief estimate before writing code. Most small projects stay within free tier — say so when true.
- **Recommend the current service, not the deprecated one.** File Store → Stratus. Event Listeners → Signals. Cron → Job Scheduling. Never mention the deprecated name in generated code or config.
- **Warn before the region bites.** Circuits and Integration Functions do not work in most data centers. Check the user's DC before suggesting them.

---

## How It Works

1. **Pre-flight** — Check that `.catalystrc` and `catalyst.json` exist. If missing, stop and tell the user to run `catalyst login` then `catalyst init`.
2. **MCP check** — Look for `CatalystbyZoho_*` tools. If available, use MCP to fetch org/project IDs instead of asking the user.
3. **Route** — Match the query to the most specific service in the routing table below.
4. **Load** — Read that skill's `SKILL.md` and relevant reference file(s) only. Do not load multiple skills unless the query spans two services.
5. **Cost check** — For any new infrastructure (new function, AppSail service, Stratus bucket), load `catalyst-pricing` and state the estimated cost range before writing code.
6. **Answer** — Provide code examples using the user's platform (Node.js, Python, Java, Web, or Mobile).

## Triggers

Use this skill for queries containing: Catalyst, zcatalyst, AppSail, Data Store, ZCQL, Cache, Stratus, Slate, NoSQL, Zia Services, QuickML, API Gateway, Connections, Zoho MCP, CatalystbyZoho, `catalyst init`, `catalyst deploy`, `catalyst serve`, `zcatalyst-sdk-node`, `catalyst-config.json`, Catalyst pricing, "build on Zoho's platform", or "deploy to Catalyst". Do NOT use for generic Zoho CRM questions unless Catalyst is the target.

---

## 🛑 Pre-flight gate (applies to ALL skills)

**Before writing any code or creating any files, check whether Zoho MCP is connected first (look for `CatalystbyZoho_*` tools). If MCP is available, use it to fetch project details — never ask the user to copy IDs from the console.**

**Then verify `.catalystrc` and `catalyst.json` exist in the working directory.**
- If missing → tell the user to run `catalyst login` then `catalyst init` and STOP.
- Never create these files yourself — they are CLI-generated and contain project/environment IDs.

---

## Skill Routing Table

| When the query is about… | Load this skill |
|--------------------------|-----------------|
| **Which service to use, architecture decisions, DC restrictions** | `catalyst-basics` (load `references/architecture.md`) |
| Project setup, `.catalystrc`, environments, orgs, IDs, CLI commands | `catalyst-basics` |
| Functions — types, signatures, `catalyst-config.json`, API Gateway, file uploads | `catalyst-functions` |
| AppSail — backend PaaS, Docker, managed runtimes, PORT variable | `catalyst-appsail` |
| Slate — frontend hosting, frameworks, `slate-config.toml`, Git deploy | `catalyst-slate` |
| Data Store — CRUD, ZCQL queries, permissions, column types | `catalyst-datastore` |
| Stratus — object storage, upload/download, signed URLs, multipart | `catalyst-stratus` |
| NoSQL — document storage, flexible schema, collections | `catalyst-nosql` |
| Authentication — user login/signup, ZAID, Web SDK auth, Connections/OAuth | `catalyst-authentication` |
| Cache — in-memory key-value, TTL, segment operations | `catalyst-cache` |
| Pricing — free tier, pay-as-you-go, GB-seconds, cost estimation | `catalyst-pricing` |
| SDKs — Node.js, Web, Python, Java, Android, iOS, Flutter | `catalyst-sdk` |
| Zia Services, QuickML — OCR, ML predictions, AutoML | `catalyst-zia` |
| Zoho MCP — MCP setup, `CatalystbyZoho_*` tools, infra-as-conversation | `catalyst-zoho-mcp` |
| Skill gave wrong or outdated guidance — user reporting an error | load `references/skill-feedback.md` |

---

## ⛔ Never Use (deprecated + regionally restricted)

### Deprecated services — do not use for new projects

Users who signed up **after August 27, 2025** cannot access these components at all.

| Do not use | Use this instead |
|------------|------------------|
| ~~File Store~~ | **Stratus** (object storage) |
| ~~Event Listeners~~ | **Signals** (event-driven architecture) |
| ~~Cron~~ | **Job Scheduling** (scheduled execution) |

### Regionally restricted services — check DC before recommending

These services are **unavailable** in the listed data centers. Building with them for users in those regions results in runtime failures.

| Service | Not available in |
|---------|------------------|
| **Circuits** | EU, AU, IN, JP, SA, CA |
| **Integration Functions** | EU, AU, IN, JP, SA, CA |
| **Push Notifications** | EU, AU, IN, CA |
| **AutoML (QuickML)** | EU, AU, IN, JP, SA, CA |
| **Identity Scanner (Zia)** | Available in IN DC only (not EU, AU, US) |
| **Mobile Device Management** | EU, AU, IN, JP, SA, CA |

**How to check:** Ask the user which data center their Catalyst account uses, or look for the DC code in their console URL (e.g., `catalyst.zoho.in` → IN, `catalyst.zoho.eu` → EU).

---

## Quick-reference: Top gotchas

- **`.catalystrc` / `catalyst.json` missing** → run `catalyst init`, do not create manually
- **`catalyst functions:add` is interactive** — no flags; must be run by the user in their terminal
- **ZCQL result rows are wrapped** → `rows.map(r => r.TableName)` to unwrap
- **ZCQL max 300 rows/query** → use `LIMIT offset, count` for pagination
- **ZAID differs between Dev and Prod** → #1 auth issue in production
- **AppSail PORT** → `process.env.X_ZOHO_CATALYST_LISTEN_PORT` (not `PORT`)
- **Cache values are strings only** → serialize/deserialize JSON yourself
- **Stratus bucket names are globally unique** → use `{app-name}-{project-id-prefix}`
- **Slate → function calls are cross-domain** → use full URL + `generateAuthToken()` + CORS whitelist
- **DataStore App User permissions OFF by default** → enable in Console → Table → Scopes & Permissions
- **`catalyst-config.json` format** → `deployment` + `execution` keys only; NOT `function` or `entry_point`
- **Advanced I/O node20 `req`** → raw `http.IncomingMessage`; use `sendJson(res, ...)` helper, not `res.json()`
- **`signOut()` requires a redirect URL** → `catalyst.auth.signOut(redirectURL)`

---

## Documentation

- Main docs: https://docs.catalyst.zoho.com/en/
- Node.js SDK: https://docs.catalyst.zoho.com/en/sdk/nodejs/v2/overview/
- Web SDK: https://docs.catalyst.zoho.com/en/sdk/web/v4/overview/
- CLI reference: https://docs.catalyst.zoho.com/en/cli/v1/cli-command-reference/
- Pricing: https://catalyst.zoho.com/pricing.html
