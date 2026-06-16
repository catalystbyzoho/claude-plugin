---
name: catalyst-slate
description: "Catalyst Slate — Git-based frontend hosting for React, Next.js, Vue, Angular, Svelte, Astro and other frameworks with preview deploys. Trigger on 'Slate', 'frontend hosting', 'slate-config.toml', 'deploy React app', or 'cross-domain Slate to function'. Do NOT use for backend APIs or server-side logic — use catalyst-appsail or catalyst-functions instead."
metadata:
  version: "2.0.0"
---

## How It Works

1. **Check if Slate app exists** — Run `catalyst slate:list` or use MCP to verify. If not, guide the user through `catalyst slate:create`.
2. **Load `references/slate-basics.md`** — for framework setup, `slate-config.toml` format, and baseUrl configuration.
3. **Cross-domain calls** — If the query involves calling functions from a Slate app, apply the full URL + `generateAuthToken()` + CORS whitelist pattern.
4. **Deploy** — `catalyst slate push` deploys to the current environment. Preview URLs are available immediately after push.

## Triggers

Use this skill for: "Slate", "frontend hosting", `catalyst slate`, `slate-config.toml`, "deploy React app", "Slate framework", `slate:create`, `slate deploy`, "frontend on Catalyst", "Slate vs Vercel", "cross-domain Slate to function", "Slate baseUrl", "Next.js on Catalyst", or "static frontend on Catalyst".

## References

| Reference | Load when the query is about… |
|-----------|-------------------------------|
| `references/slate-basics.md` | Framework setup, `slate-config.toml` gotchas, baseUrl config, CORS for Slate→function calls, Git deploy, CLI commands |
