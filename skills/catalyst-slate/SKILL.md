---
name: catalyst-slate
description: "Catalyst Slate — Git-based frontend hosting for React, Next.js, Vue, Angular, Svelte, Astro, SolidJS, Preact and other frameworks with preview deploys. Trigger on 'Slate', 'frontend hosting', 'slate-config.toml', 'deploy React app', or 'cross-domain Slate to function'. Do NOT use for backend APIs or server-side logic — use catalyst-appsail or catalyst-functions instead."
metadata:
  version: "2.2.0"
---

## How It Works

> **Before the MCP calls below, establish context once per session** via the workspace readiness gate: `../catalyst-basics/references/preflight.md`. It resolves and reconciles the org/project so `CatalystbyZoho_List_All_Slate_Apps` uses the correct project ID.

1. **Check if Slate is activated** — Use MCP: call `CatalystbyZoho_List_All_Slate_Apps` with the project ID.
   - **If it returns apps or an empty list** → Slate is activated. Proceed.
   - **If it returns an error or "not found"** → Slate needs a one-time activation. Tell the user:
     > "Please open the Catalyst console, go to your project → Slate (left sidebar) → click **Start Exploring**. This is a one-time step that takes 5 seconds. Let me know when done and I'll continue."
     Wait for confirmation, then proceed.
   - **Never fall back to Web Client (legacy) because Slate isn't activated yet.** Web Client is deprecated — Slate is always the right choice for frontend hosting. The one-time activation is not a reason to change the approach.
   > If Slate was not enabled during `catalyst init`, run `catalyst slate:create --name <name> --framework <framework> -ni` immediately after init. This command updates `catalyst.json` non-interactively — it is faster than manual setup and should be the default recommendation.
2. **Check if Slate app exists** — If no app exists yet, run `catalyst slate:create --name <name> --framework <framework> -ni` to scaffold one. If Slate was not selected during `catalyst init`, run this immediately after init.
3. **Load `references/slate-basics.md`** — for framework setup, `slate-config.toml` format, and baseUrl configuration.
4. **Cross-domain calls** — If the query involves calling functions from a Slate app, apply the full URL + `generateAuthToken()` + CORS whitelist pattern.
   > ⚠️ **Migrating from basic client hosting?** Relative paths like `/server/fn/execute` that worked in basic client **silently break on Slate** — Slate is served from `*.onslate.com` while functions are on `*.catalystserverless.com`. Every function call must become an absolute URL. Find and replace all relative `/server/...` paths with the full `https://<project>.catalystserverless.com/server/...` URL and add `generateAuthToken()` headers.
5. **Serve & test locally, THEN deploy (local-first).** Before deploying, run `catalyst serve` (or `catalyst serve --only slate`), open the local URL the CLI prints, and click through the UI — verify the frontend renders and its function/API calls succeed (managed-service calls proxy to the Development environment). Run any frontend test/build (`npm test`, `npm run build`) at this stage. Iterate locally until it works.
6. **Deploy to Development** — only after the local pass: `catalyst deploy slate <name> -ni` deploys to the current environment. Preview URLs are available after the build completes. Verify on the deployed URL before promoting to Production. Full loop: `../catalyst-basics/references/project-basics.md` → **Environments**.

## Triggers

Use this skill for: "Slate", "frontend hosting", `catalyst slate`, `slate-config.toml`, "deploy React app", "Slate framework", `slate:create`, `slate deploy`, "frontend on Catalyst", "Slate vs Vercel", "cross-domain Slate to function", "Slate baseUrl", "Next.js on Catalyst", or "static frontend on Catalyst".

## References

| Reference | Load when the query is about… |
|-----------|-------------------------------|
| `references/slate-basics.md` | Framework setup, `slate-config.toml` gotchas, baseUrl config, CORS for Slate→function calls, Git deploy, CLI commands |
