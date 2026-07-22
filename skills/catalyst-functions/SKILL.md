---
name: catalyst-functions
description: "Catalyst serverless functions — all 7 types (Basic I/O, Advanced I/O, Event, Cron, Job, Integration, Browser Logic), handler signatures, catalyst-config.json, Security Rules, API Gateway routing, file uploads, busboy, Express middleware, environment variables, function URL, and function testing. Requires MCP connection — check for the ZohoMCP_* meta-tools before any operation. Trigger on 'write a function', 'catalyst function', 'API Gateway', 'Security Rules', 'function not found', 'function returns 401', 'busboy', 'middleware', 'function URL', 'environment variable in function', 'duplicate CORS headers', 'CORS error in browser', 'Access-Control-Allow-Origin multiple values', 'function URL 404', 'execute suffix', 'function timeout', 'function hangs', or any function type question. Do NOT use for persistent servers, long-running processes, or Docker deployments — use catalyst-appsail instead."
compatibility: "Requires Catalyst CLI (`npm install -g zcatalyst-cli`). Function runtimes: Node.js 24/22/20/18 (node24 recommended), Java 25/21/17/11/8, Python 3.13/3.12/3.11/3.10. See ../catalyst-basics/references/cli.md for the full --stack list."
metadata:
   version: "2.1.0"
---

## How It Works

1. **Pre-flight (once per session).** Confirm the `ZohoMCP_*` meta-tools (`ZohoMCP_getSchema`, `ZohoMCP_executeTool`, `ZohoMCP_listTools`, `ZohoMCP_getFeatures`) are present in the tool list — that is the "MCP connected" signal (the `CatalystbyZoho_*` names never appear as tools). Then complete the canonical readiness gate → `../catalyst-basics/references/preflight.md`. It establishes/verifies org/project and scaffolds a missing project via `catalyst init --org <orgId> -p <projectId> -ni` (never interactive; NI mode links an existing project — if none exists, the user must create one in the console first). Then add functions non-interactively:
   ```bash
   catalyst functions:add --name <name> --type <type> --stack <stack> -ni
   # e.g. catalyst functions:add --name api --type aio --stack node20 -ni
   ```

2. **Identify the function type** — Basic I/O for simple request/response, Advanced I/O for raw HTTP control, Event for trigger-based (triggered by Signals; Event Listeners is deprecated), Job for scheduled (via Job Scheduling — prefer over the legacy Cron type for new projects), Integration for Zoho service events, Browser Logic for Puppeteer.
3. **Load `references/functions-basics.md`** — for the matching handler signature, `catalyst-config.json` keys, SDK init pattern, and CORS setup.
4. **Load `references/functions-advanced.md`** — for file uploads (busboy), streaming responses, error handling, or chaining functions.
5. **Load `references/api-gateway.md`** — for routing rules, rate limiting, or gateway-level CORS.
6. **Validate config** — Confirm `catalyst-config.json` uses `deployment` + `execution` keys only. Never use `function` or `entry_point`.
7. **Serve & test locally, THEN deploy (local-first).** Before `catalyst deploy`, run `catalyst serve` and test the function on Local — Data Store / Stratus / other managed calls proxy to the Development environment automatically:
   - HTTP functions (Basic/Advanced I/O): `curl http://localhost:<port>/server/<name>/execute` (port is dynamic — use what the CLI prints).
   - Event/Cron/Job/Integration functions (no HTTP trigger): `catalyst functions:execute <name> --input '{…}'`.
   - Run any project test suite (`npm test`, `pytest`). Iterate locally until it passes.
   - Only then deploy to Development: `catalyst deploy --only functions:<name> -ni` (one function) or `catalyst deploy --only functions -ni` (all). Verify on the Development URL before promoting to Production. Full loop: `../catalyst-basics/references/project-basics.md` → **Environments**.

## Security Checklist

- **Functions are publicly accessible by default.** Security Rules sets `authentication` to `optional` when a function is created — its URL is globally accessible to everyone with no restrictions. Set `"authentication": "required"` in the Security Rules JSON for any function that reads or writes user data or sensitive resources.
- **API Gateway replaces Security Rules — do not use both.** Enabling API Gateway automatically disables Security Rules. Pick one auth/routing layer per function.

## Triggers

Use this skill for: "write a function", "catalyst function", "Basic I/O", "Advanced I/O", "Event function", "Cron function", "Browser Logic", `catalyst-config.json`, "function handler", "API Gateway", "rate limiting", "busboy", "file upload in function", `catalyst deploy --only functions:<function-name>`, `catalyst functions:add`, "function CORS", or any function type or function configuration question.

Deployment command note:
- Use `catalyst deploy --only functions:<function-name> -ni` to deploy one function (where `functions:<name>` targets the function by its folder name).
- Use `catalyst deploy --only functions -ni` to deploy all functions at once.

## References

| Reference | Load when the query is about… |
|-----------|-------------------------------|
| `references/functions-basics.md` | **Start here for any function question.** Function type selection, Basic I/O and Advanced I/O handler signatures, `catalyst-config.json`, user-scope vs admin-scope, CORS, Security Rules, execution limits |
| `references/functions-advanced.md` | **Advanced I/O patterns only.** Express vs raw-http template differences, file uploads (busboy), streaming files from Stratus, error handling patterns, CORS for local dev, local testing, function chaining, ZCQL result unwrapping, HTTP payload limits |
| `references/functions-templates.md` | **Event, Cron, Job, or Integration functions.** Handler templates for all background/scheduled types, SDK component reference, retry behavior, cold start data, and the full common errors table |
| `references/api-gateway.md` | **API Gateway config only.** Enable/disable gateway, routing rules, rate limiting, CORS via gateway |
