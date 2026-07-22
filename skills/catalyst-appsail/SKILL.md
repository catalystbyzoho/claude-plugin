---
name: catalyst-appsail
description: "Catalyst AppSail — persistent backend PaaS with managed runtimes (Node.js, Java, Python) and custom Docker containers. Trigger on 'AppSail', 'persistent server', 'Docker on Catalyst', 'X_ZOHO_CATALYST_LISTEN_PORT', or 'long-running process on Catalyst'. Do NOT use for stateless request/response handlers, event-driven functions, or scheduled jobs — use catalyst-functions instead."
compatibility: "Requires Catalyst CLI (`npm install -g zcatalyst-cli`). Custom Docker deployments additionally require Docker Desktop."
metadata:
  version: "2.1.0"
---

## How It Works

1. **Identify the runtime** — Managed runtime (Node.js, Java, Python) or custom Docker container.
2. **Load `references/appsail-basics.md`** — for PORT config, environment variables, Dockerfile requirements, and deploy commands.
3. **PORT rule** — Always use `process.env.X_ZOHO_CATALYST_LISTEN_PORT`, never hardcode `PORT` or `3000`.
4. **Serve & test locally, THEN deploy (local-first).** Before `catalyst deploy appsail`, run `catalyst serve` and exercise the app's routes on Local (managed-service calls proxy to the Development environment), and run the project's test suite. Iterate locally until it passes; only then deploy to Development (`catalyst deploy appsail --name <name> -ni`), verify on the Development URL, and promote to Production only after that. Full loop: `../catalyst-basics/references/project-basics.md` → **Environments**.
5. **AppSail vs Functions decision** — If the user is unsure which to use, apply this matrix:

   | | Catalyst Functions | AppSail |
   |---|---|---|
   | Code structure | Catalyst-specific templates required | Any framework, any format, fully independent |
   | Use case | HTTP handlers, events, cron, Zoho integrations | Persistent servers, long-running processes, Docker |
   | Billing | Per API call | Per instance uptime |
   | Dependency management | Handled by Catalyst | You manage all libraries and frameworks |

## Triggers

Use this skill for: "AppSail", "persistent server", "Docker on Catalyst", "backend hosting", `X_ZOHO_CATALYST_LISTEN_PORT`, `appsail:add`, "catalyst managed runtime", "deploy Express app", "AppSail vs Functions", "long-running process on Catalyst", or "containerized app on Catalyst".

## References

| Reference | Load when the query is about… |
|-----------|-------------------------------|
| `references/appsail-basics.md` | Runtimes, Docker setup, PORT env var, env variables, deploy commands, AppSail vs Functions decision table |
