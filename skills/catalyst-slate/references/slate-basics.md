> **Slate is the preferred frontend deployment for all new Catalyst projects.**
> It supersedes legacy Web Client Hosting with modern Git-based workflows and native framework support.

## Key Features

- Native support: Next.js, React (Vite/CRA), Vue, Angular, Svelte, Astro, SolidJS, Preact, static HTML
- Git-based deployment (GitHub/GitLab repos)
- Auto build and deploy on push
- Preview deployments for branches
- Custom domain mapping
- Environment variables
- SSR and ISR support (Next.js)

---

## CLI Commands

```bash
catalyst slate:create --name <name> --framework <framework> -ni    # Add Slate app
catalyst slate:link --source <path> -ni       # Link existing dir
catalyst slate:unlink --name <name> -ni       # Unlink a Slate app
catalyst serve                                # ✅ Run the app locally — serves Slate + Functions + AppSail together (preferred; see rule below)
catalyst serve --only slate                   # Serve just Slate — for isolating a component or when a full serve errors out
catalyst deploy slate <name> -ni              # Deploy a Slate app to Development
catalyst deploy slate <name> -m "msg" -ni     # Deploy with message
catalyst deploy slate <name> --production -ni # Deploy to Production ⚠️ (only after Development is verified)
```

> ⚠️ **Run locally with `catalyst serve` — never the native dev server** (`npm run dev`, `vite`,
> `next dev`, `ng serve`, …). Only `catalyst serve` supplies the Catalyst wrapper (`/__catalyst/sdk/init.js`,
> session cookies, ZAID, managed-service proxy); run the bundler directly and the UI renders but **auth,
> `getCurrentUser`, login/signup, and session-backed function calls silently break**. Prefer plain
> `catalyst serve` (no `--only`) — it serves Slate **plus** Functions and AppSail, so the frontend's calls to
> your own backend also work locally. Scope with `--only`/`--except` (e.g. `--only slate`) when a full serve
> errors out, or to isolate or speed up a single component.

> **Local-first loop:** run `catalyst serve`, open the local URL the CLI prints, click through
> the UI and confirm its function/API calls work (managed-service calls proxy to Development), then run
> the build/tests. Deploy to Development only after this passes; promote to Production only after
> verifying on the Development URL. Canonical model: `../../catalyst-basics/references/project-basics.md` → **Environments**.

**Slate URL format** (varies by data center):

| DC | URL format |
|----|------------|
| US | `https://<subdomain>.onslate.com` |
| EU | `https://<subdomain>.onslate.eu` |
| IN | `https://<subdomain>.onslate.in` |
| AU | `https://<subdomain>.onslate.au` |
| CA | `https://<subdomain>.onslate.ca` |

---

## Supported Frameworks

| Framework Value | Detection | Build Output |
|----------------|-----------|--------------|
| `static` | HTML/CSS/JS | `.` or `public/` |
| `react-vite` | React + Vite | `dist/` |
| `nextjs` | Next.js | `out/` or `.next/` |
| `vue` | Vue.js | `dist/` |
| `angular` | Angular | `dist/<project-name>` |
| `svelte` | SvelteKit | `dist/` or `build/` |
| `astro` | Astro | `dist/` |
| `solidjs` | SolidJS | `dist/` |
| `preact` | Preact | `dist/` |
| `create-react-app` | CRA | `build/` |

---

## Linking an existing directory (Non-Interactive)

Link an existing local build/source directory as a Slate app non-interactively
(CLI v1.27.0+). `--framework` is auto-detected but can be passed explicitly:

```bash
catalyst slate:link --source /absolute/path/to/client -ni
catalyst slate:link --source <path> --name <name> --framework <framework> -ni
catalyst deploy slate <name> -m "initial deploy" -ni
```

> **Legacy fallback (CLI < v1.27.0)** — `slate:link` was interactive-only on older
> CLIs. If you cannot upgrade, configure the app by hand instead:
> 1. Create `.catalyst/slate-config.toml` inside the build output directory:
>    ```toml
>    framework = "static"
>    deployment_name = "default"
>    ```
>    (No build step / pure static HTML/CSS/JS? Your source dir *is* the output dir — place the file directly inside `client/`.)
> 2. Add to `catalyst.json` with an **absolute** source path:
>    ```json
>    "slate": [{ "name": "my-frontend", "source": "/absolute/path/to/client" }]
>    ```
> 3. Deploy: `catalyst deploy slate <name> -m "initial deploy" -ni`

## Common Errors

### `slate-config.toml` wiped by clean builds

The `.catalyst/slate-config.toml` lives inside the build output directory (e.g., `dist/`). Build commands that clean the output (`--clean`, `rm -rf dist/`) delete this file.

```bash
# Recreate after every clean build (Vite/React example):
npm run build && mkdir -p dist/.catalyst && \
  echo -e 'framework = "static"\ndeployment_name = "default"' > dist/.catalyst/slate-config.toml && \
  catalyst deploy slate <name> -ni

# Expo web example:
npx expo export --platform web --clear && \
  mkdir -p dist/.catalyst && \
  echo -e 'framework = "static"\ndeployment_name = "default"' > dist/.catalyst/slate-config.toml && \
  catalyst deploy slate <name> -ni
```

### `baseUrl` breaks assets on Slate

If your build config has a `baseUrl` or `basePath` set to a non-root path, all JS/CSS URLs get prefixed. Since Slate serves from root `/`, every asset returns 404.

**Fix:** Remove `baseUrl`/`basePath` from your build config before building for Slate. Only set it when serving from inside a function or AppSail sub-path.

### Slate + Serverless Functions cross-origin (works with correct setup)

1. Add Slate domain to Authorized Domains: Console → Authentication → Whitelisting → + Add Domain → enable CORS toggle
2. **Do NOT set CORS headers in function code for production origins** — gateway injects them automatically
3. Only set CORS headers for `localhost` (local dev)

### Slate + AppSail (does NOT work without same-origin trick)

Slate → AppSail calls get blocked by Catalyst's auth layer. **Solution:** serve the frontend from AppSail itself using `express.static()` so all calls are same-origin.

---

## Slate vs Legacy Web Client Hosting

**IMPORTANT:** Slate and Web Client Hosting are different services with different behaviors. Understanding the difference is critical for authentication and routing.

| Aspect | Slate | Legacy Web Client Hosting |
|--------|-------|---------------------------|
| **Serves from** | Root `/` — `*.onslate.com` | `/app/` path — same domain as functions |
| **Function call URLs** | **Must be absolute** — `https://<project>.catalystserverless.com/server/<fn>/execute` | Can be relative — `/server/<fn>/execute` |
| **Routing controlled by** | Framework router + `_redirects` | `client-package.json` |
| **`client-package.json` role** | Optional, SDK hints only | Required, defines routing |
| **SPA fallback** | `_redirects` or `.catalyst/slate-config.toml` | Automatic |
| **Build output** | Any (`dist/`, `build/`, `out/`) | Must be `client/` |
| **Deployment command** | `catalyst deploy slate <name> -ni` | `catalyst deploy -ni` (deploys client) |
| **Environment variables** | Build-time only (no runtime config) | Build-time only |
| **Modern framework support** | React, Next.js, Vue, Angular, Svelte, etc. | Basic HTML/CSS/JS |

> ⚠️ **Migrating from legacy client hosting to Slate?** Relative function URLs like `/server/fn/execute` that worked before **silently break on Slate** — Slate is served from `*.onslate.com` while functions live on `*.catalystserverless.com`. Find and replace every relative `/server/...` path with its absolute `https://<project>.catalystserverless.com/server/...` equivalent, and add `generateAuthToken()` headers to each call.

### client-package.json for Slate

**The file is OPTIONAL for Slate.** If included, the SDK reads `login_redirect` and `homepage` values to determine redirect behavior after authentication, but these do NOT control your app's actual routing.

**Best practice for Slate + Embedded Auth:**
```json
{
  "name": "your-app-name",
  "version": "0.0.1",
  "homepage": "/",
  "login_redirect": "/"
}
```

Place in `public/` (Vite/CRA) or `static/` (Next.js) so it's copied to build output. **Paths MUST start with `/`** to avoid legacy `/app/` prefix.

---

## SPA Routing (React, Vue, Angular)

Single-Page Applications use client-side routing. All paths must serve `index.html` to let the framework router handle navigation.

### Method 1: _redirects File (Recommended)

Create `public/_redirects` (Vite/CRA) or `static/_redirects` (Next.js):
```
/* /index.html 200
```

This file is automatically copied to build output and instructs Slate to:
1. Serve `index.html` for ALL paths
2. Return 200 status (not 302 redirect)
3. Let client-side router handle navigation

### Method 2: slate-config.toml

For frameworks without public folder, add to `.catalyst/slate-config.toml`:
```toml
framework = "react-vite"
deployment_name = "default"

[[redirects]]
from = "/*"
to = "/index.html"
status = 200
```

**Important:** This file lives in build output (`dist/.catalyst/slate-config.toml`). Clean builds delete it. Recreate after each build or use `_redirects` method instead.

### Framework-Specific Notes

**Vite/React:** Use `public/_redirects` (automatically copied to `dist/`)  
**Create React App:** Use `public/_redirects` (automatically copied to `build/`)  
**Next.js Static:** Not needed (Next.js handles fallback)  
**Angular:** Use `src/assets/_redirects` (configure angular.json to copy to dist)  
**Vue:** Use `public/_redirects` (automatically copied to `dist/`)

### Testing SPA Routing

After deployment:
1. Visit root URL → Should load
2. Visit nested route (e.g., `/marketplace/123`) → Should load (not 404)
3. Refresh on nested route → Should stay on that route (not 404)

If step 2 or 3 fails, SPA fallback is not configured.

---

## Environment Variables for Slate

Slate deployments are **static builds**. There is NO runtime environment variable configuration in the Console. All environment variables must be set at **build time**.

### Vite / React (CRA) / Vue

1. Create `.env.production` in project root:
```bash
VITE_API_BASE=https://your-function.catalystserverless.in/server/your_api
VITE_CATALYST_ZAID=your_production_zaid
```

2. Build:
```bash
npm run build
```

3. Deploy:
```bash
catalyst deploy slate <name> -ni
```

Variables are bundled into the static assets and cannot be changed without rebuilding.

### Next.js (SSR/ISR)

Next.js on Slate supports runtime variables via `NEXT_PUBLIC_*` prefix:

```javascript
// next.config.js
module.exports = {
  env: {
    NEXT_PUBLIC_API_URL: process.env.NEXT_PUBLIC_API_URL,
  }
}
```

Set during build:
```bash
NEXT_PUBLIC_API_URL=https://... npm run build
catalyst deploy slate <name> -ni
```

### Angular

Use `environment.prod.ts`:
```typescript
export const environment = {
  production: true,
  apiUrl: 'https://your-function.catalystserverless.in/server/api'
};
```

Build: `ng build --configuration production`

### Key Principle

**Build-time only.** Changing variables requires rebuilding and redeploying. This is different from serverless functions which support runtime environment variables in the Console.

---

## Slate + Embedded Auth: Legacy /app/ Path Handling

**CRITICAL:** When using Embedded Authentication with Slate, the Catalyst SDK may redirect to `/app/` (legacy Web Client Hosting path). Since Slate serves from root `/`, this causes 404 errors.

### Symptom
- User accesses Slate URL
- Redirected to `https://your-app.onslate.com/app/`
- Returns 404 or "PATTERN_NOT_MATCHED" error

### Root Cause
The `/__catalyst/sdk/init.js` script contains legacy redirect logic from Web Client Hosting that appends `/app/` when:
- `login_redirect` doesn't start with `/` in `client-package.json`, OR
- Certain Console authentication configurations are set

Legacy Web Client Hosting served apps from `/app/` path. Slate serves from `/`.

### Solution (React Router / SPA frameworks)

Add catch-all routes that redirect `/app/*` back to root:

```typescript
// React Router v6
import { Navigate } from "react-router-dom";

<Routes>
  {/* ... other routes ... */}
  
  {/* Legacy /app/ redirect for Catalyst SDK compatibility */}
  <Route path="/app" element={<Navigate to="/" replace />} />
  <Route path="/app/*" element={<Navigate to="/" replace />} />
  
  <Route path="*" element={<NotFound />} />
</Routes>
```

```javascript
// Vue Router
{
  path: '/app/:pathMatch(.*)*',
  redirect: '/'
}
```

```typescript
// Angular
{
  path: 'app',
  redirectTo: '/',
  pathMatch: 'full'
},
{
  path: 'app/**',
  redirectTo: '/'
}
```

### Solution (Static HTML / No Router)

Add to `public/_redirects`:
```
/app/* / 200
```

### Prevention

Ensure `client-package.json` paths start with `/`:
```json
{
  "homepage": "/",
  "login_redirect": "/"
}
```

**Note:** For Slate apps, `client-package.json` is read by the SDK but does NOT control actual routing (unlike legacy hosting). It only influences SDK redirect behavior.
