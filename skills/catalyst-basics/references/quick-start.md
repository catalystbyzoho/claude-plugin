# Catalyst Quick Start

Catalyst is Zoho's serverless application platform. You write backend logic as
Node.js/Java/Python functions, store data in Data Store or NoSQL, serve frontends
via Slate, and ship without managing servers.

## The 5 core services

| Service | What it does | When to use |
|---------|-------------|-------------|
| **Functions** | Run Node.js/Java/Python code on HTTP triggers or events | Any backend logic, APIs, file processing |
| **Data Store** | Managed relational DB — CRUD via SDK + ZCQL queries | Structured, tabular data with fixed schema |
| **Stratus** | Object storage (files, images, blobs up to 250 GB each) | File uploads, static assets, large data |
| **Slate** | Git-based frontend hosting (React, Next.js, Vue, Angular, …) | Serving your web UI |
| **Cache** | In-memory key-value store with TTL (max 48 h) | Sessions, rate-limit counters, ephemeral data |

---

## From zero to deployed — the walkthrough

### Step 1 — Install CLI and log in

```bash
npm install -g zcatalyst-cli
catalyst login --dc <dc> -ni   # non-interactive; <dc> = us|eu|in|au|ca|sa|jp|uae
catalyst whoami                # confirm logged-in user
```

> `login` still opens a browser for OAuth sign-in (needs a human once) — the
> `--dc <dc> -ni` flags only skip the data-center selection prompt. All other
> commands below run fully non-interactively. Requires CLI v1.27.0+.

### Step 2 — Find your Org ID

Open the Catalyst Console at `https://console.catalyst.zoho.com/baas/index`. Your Org ID appears in the URL once you're inside an org:
`https://console.catalyst.zoho.com/baas/{OrgID}/index`

Copy that number — you'll pass it as `--org <orgId>` to `catalyst init`.

> **First time?** Your **first** Catalyst project must be created in the console → **Create Project**. After that, `catalyst init` can create or link additional projects from the CLI. If you have no project yet, create one in the console, then return here.

### Step 3 — Initialize the project

```bash
mkdir my-app && cd my-app
catalyst init --org <orgId> -p <projectId> -ni
```

`catalyst init` links the org and project non-interactively. See `cli.md` for the
full flag list, feature options, and the current supported runtime/stack list.

> An interactive prompt walkthrough also exists (`catalyst init` with no flags),
> but agents must always use the `-ni` form above — never rely on the prompts.

This creates:
- `catalyst.json` — project metadata (do not edit manually)
- `.catalystrc` — org/env config (do not edit manually)

### Step 4 — Find your Project ID

After `catalyst init`, open the Catalyst Console and navigate to your project. Your Project ID appears in the URL:
`https://console.catalyst.zoho.com/baas/<ORG_ID>/project/<PROJECT_ID>/`

### Step 5 — Add a function

```bash
catalyst functions:add --name <name> --type <type> --stack <stack> -ni
```

Creates `functions/<name>/index.js` (for Node.js) non-interactively. For the full
flag list and the current supported runtime/stack values, see `cli.md`.

> An interactive form (`catalyst functions:add` with no flags — arrow-key menus for
> type, stack, and npm-init fields) exists for humans, but agents must use the `-ni`
> form above.

A minimal Advanced I/O function (raw-http template):

```javascript
// functions/my_api/index.js
'use strict';
const catalyst = require('zcatalyst-sdk-node');

module.exports = async (catalystApp, context, req, res) => {
  res.writeHead(200, { 'Content-Type': 'application/json' });
  res.end(JSON.stringify({ message: 'Hello from Catalyst' }));
};
```

### Step 6 — Serve and test locally (do this before every deploy)

Catalyst has three environments — **Local** (your machine), **Development** (remote sandbox), and **Production** (remote, live). Local-first means: run and test on Local, *then* deploy to Development. `catalyst serve` runs your Functions/AppSail/Slate code locally, and calls to managed services (Data Store, Stratus, Cache, Auth, …) are proxied to the **Development** environment — so Local needs a Development env to work fully.

```bash
catalyst serve
# The serve port is dynamic — the CLI prints the actual URL on startup.
# Functions are served at: http://localhost:<port>/server/<function_name>/execute
```

Test it (use the port `catalyst serve` printed on startup — it is dynamic, never hardcoded):
```bash
curl http://localhost:<port>/server/my_api/execute
# then run any project tests, e.g.:
npm test
```

Iterate locally until it works. Local iteration is fast and free; a failed remote deploy is slow and pollutes Development.

### Step 7 — Deploy to Development

Only once the local serve + test pass succeeds:

```bash
catalyst deploy -ni
# or deploy only functions:
catalyst deploy --only functions -ni
```

Your function is live at:
`https://<project_domain>.catalystserverless.com/server/<function_name>/execute`

Verify it on the Development URL, then promote to Production (`catalyst deploy --production -ni`) only after Development is verified — see the dev-to-prod checklist in `project-basics.md`.

---

## Create a table, set permissions, configure CORS

These are console tasks — see `console-ui-guide.md` for the click-by-click steps:

- **Create a Data Store table** with typed columns (`ROWID` primary key is auto-created).
- **Scopes and Permissions** — enable App User Insert/Update/Delete (required for SDK writes from authenticated users; otherwise you get a 403).
- **Authorized Domains + CORS toggle** — the Catalyst gateway injects the CORS headers automatically for authorized production origins; you only add CORS code in functions for `localhost` dev.

> If Zoho MCP is connected, create tables directly via `CatalystbyZoho_Create_Table` instead of the console — see `../../catalyst-datastore/SKILL.md`.

## Common Errors

| Error | Cause | Fix |
|-------|-------|-----|
| `catalyst: command not found` | CLI not installed globally | Run `npm install -g zcatalyst-cli` |
| `catalyst.json` is `{}` after init | No project linked yet | Run `catalyst project:use <project-name> -ni` in the project directory |
| Function 401 in browser but works with curl | Authentication required in Security Rules | Add `"authentication": "open"` to `catalyst-config.json` for public endpoints |
| CORS error in production frontend | Domain not in Authorized Domains | Add the Slate/frontend domain in Console → Settings → Authorized Domains and enable CORS toggle |
