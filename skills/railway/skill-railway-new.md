---
id: skill-railway-new
type: skill
name: railway-new
description: Create Railway projects, services, and databases with proper configuration.
  Use when user says "setup", "deploy to railway", "initialize", "create project",
  "create service", or wants to deploy from GitHub. Handles initial setup AND adding
  services to existing projects. For databases, use railway-railway-database skill
  instead.
category: railway
complexity: medium
keywords:
- api
- database
- deploy
- deployment
- github
- go
- mongodb
- mysql
- postgres
- python
- react
- rust
- test
- typescript
capabilities: []
token_estimate: 2415
has_references: true
reference_count: 4
---

<!-- Converted from Claude Skill Template -->
<!-- Complexity: Medium -->
<!-- Estimated Tokens: ~2,415 -->
<!-- Includes Reference Materials -->

> **How to Use This Template**
>
> This template can be used with various AI coding assistants:
> 
> **GitHub Copilot:**
> - Add to `.github/copilot-instructions.md` in your repository
> - Reference in chat: `@workspace` to include in context
> - Add specific sections to your workspace instructions
> 
> **Augment Code:**
> - Load context: `aug context add <path-to-this-file>`
> - Reference in queries naturally
> - Use with specific commands
> 
> **Claude (Desktop/Web):**
> - Add to Project Knowledge in Claude Projects
> - Reference in custom instructions
> - Copy relevant sections as needed
> 
> **ChatGPT:**
> - Add to Custom GPT configuration
> - Include in conversation instructions
> - Use as system prompt
> 
> **Generic Usage:**
> Copy the content below and paste it into your AI assistant's context window
> or system instructions.

---




# railway-new

> Create Railway projects, services, and databases with proper configuration. Use when user says "setup", "deploy to railway", "initialize", "create project", "create service", or wants to deploy from GitHub. Handles initial setup AND adding services to existing projects. For databases, use railway-railway-database skill instead.

# New Project / Service / Database

Create Railway projects, services, and databases with proper configuration.

## When to Use

- User says "deploy to railway" (add service if linked, init if not)
- User says "create a railway project", "init", "new project" (explicit new project)
- User says "link to railway", "connect to railway"
- User says "create a service", "add a backend", "new api service"
- User says "create a vite app", "create a react website", "make a python api"
- User says "deploy from github.com/user/repo", "create service from this repo"
- User says "add postgres", "add a database", "add redis", "add mysql", "add mongo"
- User says "connect to postgres", "wire up the database", "connect my api to redis"
- User says "add postgres and connect to the server"
- Setting up code + Railway service together

## Prerequisites

Check CLI installed:

```bash
command -v railway
```

If not installed:

> Install Railway CLI:
>
> ```
> npm install -g @railway/cli
> ```
>
> or
>
> ```
> brew install railway
> ```

Check authenticated:

```bash
railway whoami --json
```

If not authenticated:

> Run `railway login` to authenticate.

## Decision Flow

```
railway status --json (in current dir)
     │
┌────┴────┐
Linked    Not Linked
  │            │
  │       Check parent: cd .. && railway status --json
  │            │
  │       ┌────┴────┐
  │    Parent      Not linked
  │    Linked      anywhere
  │       │            │
  │   Add service   railway list
  │   Set rootDir      │
  │   Deploy       ┌───┴───┐
  │       │      Match?  No match
  │       │        │        │
  │       │      Link    Init new
  └───────┴────────┴────────┘
           │
    User wants service?
           │
     ┌─────┴─────┐
    Yes         No
     │           │
Scaffold code   Done
     │
railway add --service
     │
Configure if needed
     │
Ready to deploy
```

## Check Current State

```bash
railway status --json
```

- **If linked**: Add a service to the existing project (see below)
- **If not linked**: Check if a PARENT directory is linked (see below)

### When Already Linked

**Default behavior**: "deploy to railway" = add a service to the linked project.

Do NOT create a new project unless user EXPLICITLY says:

- "new project", "create a project", "init a project"
- "separate project", "different project"

App names like "flappy-bird" or "my-api" are SERVICE names, not project names.

```
User: "create a vite app called foo and deploy to railway"
Project: Already linked to "my-project"

WRONG: railway init -n foo
RIGHT: railway add --service foo
```

### Parent Directory Linking

Railway CLI walks up the directory tree to find a linked project. If you're in a subdirectory:

```bash
cd .. && railway status --json
```

**If parent is linked**, you don't need to init/link the subdirectory. Instead:

1. Create service: `railway add --service <name>`
2. Set `rootDirectory` to subdirectory path via environment skill
3. Deploy from root: `railway up`

**If no parent is linked**, proceed with init or link flow.

## Init vs Link Decision

**Skip this section if already linked** - just add a service instead.

Only use this section when NO project is linked (directly or via parent).

### Check User's Projects

The output can be large. Run in a subagent and extract only:
- Project `id` and `name`
- Workspace `id` and `name`

```bash
railway list --json
```

### Decision Logic

1. **User explicitly says "new project"** → Use `railway init`
2. **User names an existing project** → Use `railway link`
3. **Directory name matches existing project** → Ask: link existing or create new?
4. **No matching projects** → Use `railway init`
5. **Ambiguous** → Ask user

## Create New Project

```bash
railway init -n <name>
```

Options:

- `-n, --name` - Project name (auto-generated if omitted in non-interactive mode)
- `-w, --workspace` - Workspace name or ID (required if multiple workspaces exist)

### Multiple Workspaces

If the user has multiple workspaces, `railway init` requires the `--workspace` flag.

Get workspace IDs from:

```bash
railway whoami --json
```

The `workspaces` array contains `{ id, name }` for each workspace.

**Inferring workspace from user input:**
If user says "deploy into xxx workspace" or "create project in my-team", match the
name against the workspaces array and use the corresponding ID:

```bash
# User says: "create a project in my personal workspace"
railway whoami --json | jq '.workspaces[] | select(.name | test("personal"; "i"))'
# Use the matched ID: railway init -n myapp --workspace <matched-id>
```

## Link Existing Project

```bash
railway link -p <project>
```

Options:

- `-p, --project` - Project name or ID
- `-e, --environment` - Environment (default: production)
- `-s, --service` - Service to link
- `-t, --team` - Team/workspace

## Create Service

After project is linked, create a service:

```bash
railway add --service <name>
```

**For GitHub repo sources**: Create an empty service, then invoke the railway-environment skill to configure the source via staged changes API. Do NOT use `railway add --repo` - it requires GitHub app integration which often fails.

Flow:

1. `railway add --service my-api`
2. Invoke railway-environment skill to set `source.repo` and `source.branch`
3. Apply changes to trigger deployment

### Configure Based on Project Type

Reference [railpack.md](../reference/railpack.md) for build configuration.
Reference [monorepo.md](../reference/monorepo.md) for monorepo patterns.

**Static site (Vite, CRA, Astro static):**

- Railpack auto-detects common output dirs (dist, build)
- If non-standard output dir: invoke railway-environment skill to set `RAILPACK_STATIC_FILE_ROOT`
- Do NOT use `railway variables` CLI - always use the environment skill

**Node.js SSR (Next.js, Nuxt, Express):**

- Verify `start` script exists in package.json
- If custom start needed: invoke railway-environment skill to set `startCommand`

**Python (FastAPI, Django, Flask):**

- Verify `requirements.txt` or `pyproject.toml` exists
- Auto-detected by Railpack, usually no config needed

**Go:**

- Verify `go.mod` exists
- Auto-detected, no config needed

### Monorepo Configuration

**Critical decision:** Root directory vs custom commands.

**Isolated monorepo** (apps don't share code):

- Set Root Directory to the app's subdirectory (e.g., `/frontend`)
- Only that directory's code is available during build

**Shared monorepo** (TypeScript workspaces, shared packages):

- Do NOT set root directory
- Set custom build/start commands to filter the package:
  - pnpm: `pnpm --filter <package> build`
  - npm: `npm run build --workspace=packages/<package>`
  - yarn: `yarn workspace <package> build`
  - Turborepo: `turbo run build --filter=<package>`
- Set watch paths to prevent unnecessary rebuilds

See [monorepo.md](../reference/monorepo.md) for detailed patterns.

## Project Setup Guidance

Analyze the codebase to ensure Railway compatibility.

### Analyze Codebase

Check for existing project files:

- `package.json` → Node.js project
- `requirements.txt`, `pyproject.toml` → Python project
- `go.mod` → Go project
- `Cargo.toml` → Rust project
- `index.html` → Static site
- None → Guide scaffolding

**Monorepo detection:**

- `pnpm-workspace.yaml` → pnpm workspace (shared monorepo)
- `package.json` with `workspaces` field → npm/yarn workspace (shared monorepo)
- `turbo.json` → Turborepo (shared monorepo)
- Multiple subdirs with separate `package.json` but no workspace config → isolated monorepo

### Scaffolding Hints

If no code exists, suggest minimal patterns from [railpack.md](../reference/railpack.md):

**Static site:**

> Create an `index.html` file in the root directory.

**Vite React:**

```bash
npm create vite@latest . -- --template react
```

**Astro:**

```bash
npm create astro@latest
```

**Python FastAPI:**

> Create `main.py` with FastAPI app and `requirements.txt` with dependencies.

**Go:**

> Create `main.go` with HTTP server listening on `PORT` env var.

## Databases

For adding databases (Postgres, Redis, MySQL, MongoDB), use the railway-railway-database skill.

The railway-railway-database skill handles:
- Creating database services
- Connection variable references
- Wiring services to databases

## Composability

- **After service created**: Use railway-deploy skill to push code
- **For advanced config**: Use railway-environment skill (buildCommand, startCommand)
- **For domains**: Use railway-domain skill
- **For status checks**: Use railway-status skill
- **For service operations** (rename, delete, status): Use railway-service skill

## Error Handling

### CLI Not Installed

```
Railway CLI not installed. Install with:
  npm install -g @railway/cli
or
  brew install railway
```

### Not Authenticated

```
Not logged in to Railway. Run: railway login
```

### No Workspaces

```
No workspaces found. Create one at railway.com or verify authentication.
```

### Project Name Taken

```
Project name already exists. Either:
- Link to existing: railway link -p <name>
- Use different name: railway init -n <other-name>
```

### Service Name Taken

```
Service name already exists in this project. Use a different name:
  railway add --service <other-name>
```

## Examples

### Create HTML Static Site

```
User: "create a simple html site and deploy to railway"

1. Check status → not linked
2. railway init -n my-site
3. Guide: create index.html
4. railway add --service my-site
5. No config needed (index.html in root auto-detected)
6. Use deploy skill: railway up
7. Use domain skill for public URL
```

### Create Vite React Service

```
User: "create a vite react service"

1. Check status → linked (or init/link first)
2. Scaffold: npm create vite@latest frontend -- --template react
3. railway add --service frontend
4. No config needed (Vite dist output auto-detected)
5. Use deploy skill: railway up
```

### Add Python API to Project

```
User: "add a python api to my project"

1. Check status → linked
2. Guide: create main.py with FastAPI, requirements.txt
3. railway add --service api
4. No config needed (FastAPI auto-detected)
5. Use deploy skill
```

### Link and Add Service

```
User: "connect to my backend project and add a worker service"

1. railway list --json → find "backend"
2. railway link -p backend
3. railway add --service worker
4. Guide setup based on worker type
```

### Deploy to Railway (Ambiguous)

```
User: "deploy to railway"

1. railway status → not linked
2. railway list → has projects
3. Directory is "my-app", found project "my-app"
4. Ask: "Found existing project 'my-app'. Link to it or create new?"
5. User: "link"
6. railway link -p my-app
7. Ask: "Create a service for this code?"
```

### Add Service to Isolated Monorepo

```
User: "create a static site in the frontend directory"

1. Check: /frontend has its own package.json, no workspace config
2. This is isolated monorepo → use root directory
3. railway add --service frontend
4. Invoke environment skill to set rootDirectory: /frontend
5. Set watch paths: /frontend/**
```

### Add Service to TypeScript Monorepo

```
User: "add a new api package to this turborepo"

1. Check: turbo.json exists, pnpm-workspace.yaml exists
2. This is shared monorepo → use custom commands, NOT root directory
3. Guide: create packages/api with package.json
4. railway add --service api
5. Invoke environment skill to set buildCommand and startCommand (do NOT set rootDirectory)
6. Set watch paths: /packages/api/**, /packages/shared/**
```

### Deploy Existing pnpm Workspace Package

```
User: "deploy the backend package to railway"

1. Check: pnpm-workspace.yaml exists → shared monorepo
2. railway add --service backend
3. Invoke environment skill to set buildCommand and startCommand
4. Set watch paths for backend + any shared deps
```

### Deploy Subdirectory of Linked Project

```
User: "create a vite app in my-app directory and deploy to railway"
CWD: ~/projects/my-project/my-app (parent already linked to "my-project")

1. Check status in my-app → not linked
2. Check parent: cd .. && railway status → IS linked to "my-project"
3. DON'T init/link the subdirectory
4. Scaffold: bun create vite my-app --template react-ts
5. cd my-app && bun install
6. railway add --service my-app
7. Invoke environment skill to set rootDirectory: /my-app
8. Deploy from root: railway up
```


---


## 📚 Reference Materials


### Railpack

# Railpack Reference

Railpack is Railway's default builder. Zero-config for most projects.

Full docs: https://railpack.com/llms.txt

## Detection

Railpack analyzes source code to detect language, framework, and build requirements automatically.

Supported: Node, Python, Go, PHP, Java, Ruby, Rust, Elixir, Gleam, Deno, C/C++, static files.

## Static Sites

### Detection Patterns

Railpack serves static files via Caddy when it detects:
1. `Staticfile` in root (can specify `root: dist`)
2. `index.html` in root
3. `public/` directory
4. `RAILPACK_STATIC_FILE_ROOT` env var set

### Root Directory Priority

1. `RAILPACK_STATIC_FILE_ROOT` env var
2. `root` in `Staticfile`
3. `public/` directory
4. Current directory (if index.html exists)

### Common Patterns

Railpack auto-detects common static build outputs. Only set `RAILPACK_STATIC_FILE_ROOT` for non-standard output directories.

| Framework | Build Output | Config Needed |
|-----------|--------------|---------------|
| Plain HTML | root | None (auto-detected) |
| Vite | dist | None (auto-detected) |
| Astro (static) | dist | None (auto-detected) |
| Create React App | build | None (auto-detected) |
| Angular | dist/<project> | `RAILPACK_STATIC_FILE_ROOT=dist/<project>` (non-standard path) |
| Custom output | varies | Set if output dir is non-standard |

### Custom Caddyfile

Put a `Caddyfile` in project root to override default serving behavior.

## Node.js

### Detection
- `package.json` in root

### Version Priority
1. `RAILPACK_NODE_VERSION` env var
2. `engines.node` in package.json
3. `.nvmrc` or `.node-version`
4. Default: Node 22

### Package Manager Detection
1. `packageManager` field in package.json (enables Corepack)
2. Lock file: `pnpm-lock.yaml`, `bun.lockb`, `yarn.lock`
3. Default: npm

### Build Command
Auto-detected from package.json `scripts.build`. Override via `buildCommand` in service settings.

### Start Command
Auto-detected:
1. `scripts.start` in package.json
2. `main` field in package.json
3. `index.js` or `index.ts` in root

Override via `startCommand` in service settings.

### Framework Detection

| Framework | Detection | Notes |
|-----------|-----------|-------|
| Next.js | `next` in dependencies | Caches `.next/cache` |
| Vite | `vite` in devDependencies | Static or SSR mode |
| Astro | `astro` in dependencies | Caches `.astro` |
| Nuxt | `nuxt` in dependencies | Auto start command |
| Remix | `@remix-run/*` in deps | - |

### Static Site Mode (SPA)

For frameworks like Vite, CRA, Astro (static), Angular:
- Railpack builds then serves via Caddy
- Set `RAILPACK_SPA_OUTPUT_DIR` if output isn't `dist`

## Python

### Detection
- `requirements.txt`, `pyproject.toml`, `Pipfile`, or `uv.lock`

### Version Priority
1. `RAILPACK_PYTHON_VERSION` env var
2. `.python-version` file
3. `requires-python` in pyproject.toml
4. Default: Python 3.12

### WSGI/ASGI Auto-Config

| Framework | Start Command |
|-----------|---------------|
| Django | `gunicorn <project>.wsgi` |
| FastAPI | `uvicorn main:app --host 0.0.0.0` |
| Flask | `gunicorn app:app` |

Override via `startCommand` in service settings.

## Go

### Detection
- `go.mod` in root

### Build
Compiles to binary automatically. For `cmd/` structure, set binary name or use `RAILPACK_GO_BIN`.

## Rust

### Detection
- `Cargo.toml` in root

### Build
Release build by default. Binary auto-detected from Cargo.toml.

## Configuration

### Preferred: Service Settings

Use `environment` skill to set:
- `buildCommand` - Custom build command
- `startCommand` - Custom start command

These are stored in Railway and don't pollute your codebase.

### Environment Variables

| Variable | Purpose |
|----------|---------|
| `RAILPACK_STATIC_FILE_ROOT` | Static file serving directory |
| `RAILPACK_SPA_OUTPUT_DIR` | SPA build output (defaults to `dist`) |
| `RAILPACK_NODE_VERSION` | Node.js version |
| `RAILPACK_PYTHON_VERSION` | Python version |
| `RAILPACK_GO_BIN` | Go binary name |
| `RAILPACK_PACKAGES` | Additional Mise packages (`pkg@version`) |
| `RAILPACK_BUILD_APT_PACKAGES` | System packages for build |
| `RAILPACK_DEPLOY_APT_PACKAGES` | System packages for runtime |

### railpack.json

Advanced config for custom build steps:

```json
{
  "$schema": "https://schema.railpack.com",
  "packages": {
    "ffmpeg": "latest"
  },
  "deploy": {
    "aptPackages": ["libmagic1"]
  }
}
```

### railway.toml

Alternative config format:

```toml
[build]
builder = "RAILPACK"
buildCommand = "npm run build"

[deploy]
startCommand = "npm start"
```

## Minimal Project Scaffolding

When no code exists, suggest these patterns:

### Static HTML
```html
<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <title>My Site</title>
</head>
<body>
  <h1>Hello Railway</h1>
</body>
</html>
```

### Vite React
```bash
npm create vite@latest . -- --template react
```

### Astro
```bash
npm create astro@latest
```

### Python FastAPI
```python
# main.py
from fastapi import FastAPI
app = FastAPI()

@app.get("/")
def root():
    return {"message": "Hello Railway"}
```

```txt
# requirements.txt
fastapi
uvicorn
```

### Go HTTP Server
```go
// main.go
package main

import (
    "fmt"
    "net/http"
    "os"
)

func main() {
    port := os.Getenv("PORT")
    if port == "" {
        port = "8080"
    }
    http.HandleFunc("/", func(w http.ResponseWriter, r *http.Request) {
        fmt.Fprintf(w, "Hello Railway")
    })
    http.ListenAndServe(":"+port, nil)
}
```

## Troubleshooting

| Issue | Solution |
|-------|----------|
| Static site 404s | Check output dir - set `RAILPACK_STATIC_FILE_ROOT` if non-standard |
| Wrong build command | Use `environment` skill to set `buildCommand` |
| Wrong start command | Use `environment` skill to set `startCommand` |
| Missing system package | Add to `RAILPACK_BUILD_APT_PACKAGES` or `RAILPACK_DEPLOY_APT_PACKAGES` |
| Wrong Node version | Set `RAILPACK_NODE_VERSION` or add `.nvmrc` |
| Wrong Python version | Set `RAILPACK_PYTHON_VERSION` or add `.python-version` |




### Environment Config

# Environment Config Reference

The `EnvironmentConfig` object is used to configure services, volumes, and shared variables in Railway.

## Structure

```json
{
  "services": {
    "<serviceId>": {
      "source": { ... },
      "build": { ... },
      "deploy": { ... },
      "variables": { ... },
      "networking": { ... }
    }
  },
  "sharedVariables": { ... },
  "volumes": { ... },
  "buckets": { ... }
}
```

Only include fields being changed. The patch is merged with existing config.

## Service Config

### Source
| Field | Type | Description |
|-------|------|-------------|
| `image` | string | Docker image (e.g., `nginx:latest`) |
| `repo` | string | Git repository URL |
| `branch` | string | Git branch to deploy |
| `commitSha` | string | Specific commit SHA |
| `rootDirectory` | string | Root directory (monorepos) |
| `checkSuites` | boolean | Wait for GitHub check suites |
| `autoUpdates.type` | disabled \| patch \| minor | Auto-update policy for Docker images |

### Build
| Field | Type | Description |
|-------|------|-------------|
| `builder` | NIXPACKS \| DOCKERFILE \| RAILPACK | Build system |
| `buildCommand` | string | Command for Nixpacks builds |
| `dockerfilePath` | string | Path to Dockerfile |
| `watchPatterns` | string[] | Patterns to trigger deploys |
| `nixpacksConfigPath` | string | Path to nixpacks config |

### Deploy
| Field | Type | Description |
|-------|------|-------------|
| `startCommand` | string | Container start command |
| `multiRegionConfig` | object | Region → replica config. See [Multi-Region Config](#multi-region-config). |
| `healthcheckPath` | string | Health check endpoint |
| `healthcheckTimeout` | number | Seconds to wait for health |
| `restartPolicyType` | ON_FAILURE \| ALWAYS \| NEVER | Restart behavior |
| `restartPolicyMaxRetries` | number | Max restart attempts |
| `cronSchedule` | string | Cron schedule for cron jobs |
| `sleepApplication` | boolean | Sleep when inactive |

### Variables
| Field | Type | Description |
|-------|------|-------------|
| `value` | string | Variable value |
| `isOptional` | boolean | Allow empty value |

Set to `null` to delete a variable.

For variable references, see [variables.md](variables.md).

### Lifecycle
| Field | Type | Description |
|-------|------|-------------|
| `isDeleted` | boolean | Mark for deletion (requires ADMIN) |
| `isCreated` | boolean | Mark as newly created |

## Multi-Region Config

Controls replica count per region. Structure: region name → `{ numReplicas }` or `null` to remove.

```json
{
  "multiRegionConfig": {
    "us-west2": { "numReplicas": 3 },
    "europe-west4-drams3a": { "numReplicas": 2 }
  }
}
```

### Available Regions

| Name | Location | Aliases |
|------|----------|---------|
| `us-west2` | US West (California) | "us west", "california" |
| `us-east4-eqdc4a` | US East (Virginia) | "us east", "virginia" |
| `europe-west4-drams3a` | EU West (Amsterdam) | "europe", "eu", "amsterdam" |
| `asia-southeast1-eqsg3a` | Southeast Asia (Singapore) | "asia", "singapore" |

### Interpreting User Requests

- "add 3 replicas to europe" → `{ "europe-west4-drams3a": { "numReplicas": 3 } }`
- "add a replica to all regions" → set `numReplicas: 1` for all 4 regions
- "remove from asia" → `{ "asia-southeast1-eqsg3a": null }`
- "increase replicas to 5" (no region specified) → query current config first, update existing region(s)

**Important:** When user doesn't specify a region, query the current `multiRegionConfig` and modify the existing region(s). Don't assume a default region.

## Common Operations

### Set Build Command
```json
{ "services": { "<serviceId>": { "build": { "buildCommand": "npm run build" } } } }
```

### Set Start Command
```json
{ "services": { "<serviceId>": { "deploy": { "startCommand": "node server.js" } } } }
```

### Set Replicas
```json
{ "services": { "<serviceId>": { "deploy": { "multiRegionConfig": { "us-west2": { "numReplicas": 3 } } } } } }
```

### Add Variables
```json
{ "services": { "<serviceId>": { "variables": { "API_KEY": { "value": "xxx" } } } } }
```

### Delete Variable
```json
{ "services": { "<serviceId>": { "variables": { "OLD_VAR": null } } } }
```

### Add Shared Variable
```json
{ "sharedVariables": { "DATABASE_URL": { "value": "postgres://..." } } }
```

### Change Docker Image
```json
{ "services": { "<serviceId>": { "source": { "image": "nginx:latest" } } } }
```

### Connect GitHub Repo
```json
{ "services": { "<serviceId>": { "source": { "repo": "owner/repo", "branch": "main" } } } }
```

### Change Git Branch
```json
{ "services": { "<serviceId>": { "source": { "branch": "develop" } } } }
```

### Set Health Check
```json
{ "services": { "<serviceId>": { "deploy": { "healthcheckPath": "/health", "healthcheckTimeout": 30 } } } }
```

### Change Builder
```json
{ "services": { "<serviceId>": { "build": { "builder": "DOCKERFILE", "dockerfilePath": "./Dockerfile" } } } }
```

### Delete Service
```json
{ "services": { "<serviceId>": { "isDeleted": true } } }
```

### Delete Volume
```json
{ "volumes": { "<volumeId>": { "isDeleted": true } } }
```

### New Service Instance
```json
{ "services": { "<serviceId>": { "isCreated": true, "source": { "image": "nginx" } } } }
```

**Note:** `isCreated: true` is required for new service instances.




### Variables

# Variables Reference

Variables in Railway support references to other services, shared variables, and Railway-provided values.

## Template Syntax

```
${{NAMESPACE.VAR}}
```

| Namespace | Description |
|-----------|-------------|
| `shared` | Shared variables (project-wide) |
| `<serviceName>` | Variables from another service (case-sensitive) |

## Examples

**Reference shared variable:**
```json
{ "value": "${{shared.DOMAIN}}" }
```

**Reference another service's variable:**
```json
{ "value": "${{api.API_KEY}}" }
```

**Combine with text:**
```json
{ "value": "https://${{shared.DOMAIN}}/api" }
```

## Railway-Provided Variables

Railway injects these into every service automatically.

### Networking
| Variable | Description | Example | Availability |
|----------|-------------|---------|--------------|
| `RAILWAY_PUBLIC_DOMAIN` | Public domain | `myapp.up.railway.app` | Only if service has a domain |
| `RAILWAY_PRIVATE_DOMAIN` | Private DNS (internal only) | `myapp.railway.internal` | Always |
| `RAILWAY_TCP_PROXY_DOMAIN` | TCP proxy domain | `roundhouse.proxy.rlwy.net` | Only if TCP proxy enabled |
| `RAILWAY_TCP_PROXY_PORT` | TCP proxy port | `11105` | Only if TCP proxy enabled |

**Note:** `RAILWAY_PUBLIC_DOMAIN` is only available if the service has a domain configured.
Check the service's environment config to verify a domain exists before referencing it.

### Context
| Variable | Description |
|----------|-------------|
| `RAILWAY_PROJECT_ID` | Project ID |
| `RAILWAY_PROJECT_NAME` | Project name |
| `RAILWAY_ENVIRONMENT_ID` | Environment ID |
| `RAILWAY_ENVIRONMENT_NAME` | Environment name |
| `RAILWAY_SERVICE_ID` | Service ID |
| `RAILWAY_SERVICE_NAME` | Service name |
| `RAILWAY_DEPLOYMENT_ID` | Deployment ID |
| `RAILWAY_REPLICA_ID` | Replica ID |
| `RAILWAY_REPLICA_REGION` | Region (e.g., `us-west2`) |

### Volume (if attached)
| Variable | Description |
|----------|-------------|
| `RAILWAY_VOLUME_NAME` | Volume name |
| `RAILWAY_VOLUME_MOUNT_PATH` | Mount path |

### Git (if deployed from GitHub)
| Variable | Description |
|----------|-------------|
| `RAILWAY_GIT_COMMIT_SHA` | Commit SHA |
| `RAILWAY_GIT_BRANCH` | Branch name |
| `RAILWAY_GIT_REPO_NAME` | Repository name |
| `RAILWAY_GIT_REPO_OWNER` | Repository owner |
| `RAILWAY_GIT_AUTHOR` | Commit author |
| `RAILWAY_GIT_COMMIT_MESSAGE` | Commit message |

## Wiring Services Together

### Frontend → Backend (public network)
Use when: Browser makes requests to API (browser can't access private network)

```json
{
  "services": {
    "<frontendId>": {
      "variables": {
        "API_URL": { "value": "https://${{backend.RAILWAY_PUBLIC_DOMAIN}}" }
      }
    }
  }
}
```

### Service → Database (private network)
Use when: Backend connects to database (faster, no egress cost, more secure)

Railway databases auto-generate connection URL variables. Use the private versions:

| Database | Variable Reference |
|----------|-------------------|
| Postgres | `${{Postgres.DATABASE_URL}}` |
| MySQL | `${{MySQL.DATABASE_URL}}` |
| Redis | `${{Redis.REDIS_URL}}` |
| Mongo | `${{Mongo.MONGO_URL}}` |

**Postgres/MySQL example:**
```json
{
  "services": {
    "<apiId>": {
      "variables": {
        "DATABASE_URL": { "value": "${{Postgres.DATABASE_URL}}" }
      }
    }
  }
}
```

**Redis example:**
```json
{
  "services": {
    "<apiId>": {
      "variables": {
        "REDIS_URL": { "value": "${{Redis.REDIS_URL}}" }
      }
    }
  }
}
```

**Mongo example:**
```json
{
  "services": {
    "<apiId>": {
      "variables": {
        "MONGO_URL": { "value": "${{Mongo.MONGO_URL}}" }
      }
    }
  }
}
```

**Note:** Service names are case-sensitive. Match the exact name from your project (e.g., "Postgres", "Redis").

### Service → Service (private network)
Use when: Microservices communicate internally

```json
{
  "services": {
    "<workerServiceId>": {
      "variables": {
        "API_INTERNAL_URL": { "value": "http://${{api.RAILWAY_PRIVATE_DOMAIN}}:${{api.PORT}}" }
      }
    }
  }
}
```

## When to Use Public vs Private

| Use Case | Domain | Reason |
|----------|--------|--------|
| Browser → API | Public | Browser can't access private network |
| Service → Service | Private | Faster, no egress, more secure |
| Service → Database | Private | Databases should never be public |
| External webhook → Service | Public | External services need public access |
| Cron job → API | Private | Internal communication |




### Monorepo

# Monorepo Reference

Railway supports two types of monorepo deployments with different configuration approaches.

## Key Decision: Root Directory vs Custom Commands

| Approach | When to Use | What Happens |
|----------|-------------|--------------|
| **Root Directory** | Isolated apps (no shared code) | Only that directory's code is available |
| **Custom Commands** | Shared monorepos (TypeScript, workspaces) | Full repo available, filter at build/start |

**Critical:** Setting root directory means code outside that directory is NOT available during build. For monorepos with shared packages, use custom commands instead.

## Isolated Monorepo

Apps are completely independent - no shared code between directories.

```
├── frontend/        # React app (standalone)
│   ├── package.json
│   └── src/
└── backend/         # Python API (standalone)
    ├── requirements.txt
    └── main.py
```

### Configuration

Set **Root Directory** for each service:
- Frontend service: `/frontend`
- Backend service: `/backend`

Each service only sees its own directory's code.

### When to Use

- Frontend and backend in different languages
- No shared packages or dependencies
- Each app has its own package.json/requirements.txt
- Apps don't import from sibling directories

## Shared Monorepo

Apps share code from common packages or the root.

```
├── package.json           # Root workspace config
├── packages/
│   ├── frontend/
│   │   ├── package.json
│   │   └── src/
│   ├── backend/
│   │   ├── package.json
│   │   └── src/
│   └── shared/            # Shared utilities
│       ├── package.json
│       └── src/
└── tsconfig.json          # Shared TS config
```

### Configuration

Do NOT set root directory. Instead, use custom build and start commands:

**pnpm:**
```
Build: pnpm --filter backend build
Start: pnpm --filter backend start
```

**npm workspaces:**
```
Build: npm run build --workspace=packages/backend
Start: npm run start --workspace=packages/backend
```

**yarn workspaces:**
```
Build: yarn workspace backend build
Start: yarn workspace backend start
```

**bun:**
```
Build: bun run --filter backend build
Start: bun run --filter backend start
```

**Turborepo:**
```
Build: turbo run build --filter=backend
Start: turbo run start --filter=backend
```

### When to Use

- TypeScript/JavaScript monorepo with workspaces
- Packages import from sibling packages (`@myapp/shared`)
- Shared tsconfig.json, eslint config at root
- Using pnpm, yarn workspaces, npm workspaces, or bun
- Using Turborepo, Nx, or similar build tools

## Watch Paths

Prevent changes in one package from triggering rebuilds of other services.

Set watch paths for each service to only rebuild when relevant files change:

| Service | Watch Paths |
|---------|-------------|
| frontend | `/packages/frontend/**`, `/packages/shared/**` |
| backend | `/packages/backend/**`, `/packages/shared/**` |

Include shared packages in watch paths if the service depends on them.

### Pattern Format

Uses gitignore-style patterns:
```
/packages/backend/**     # All files in backend
/packages/shared/**      # All files in shared (if depends on it)
!**/*.md                 # Ignore markdown changes
```

## Configuration Examples

### Isolated: React + Python API

Two separate apps, no shared code.

**Frontend service:**
- Root Directory: `/frontend`
- No custom commands needed (Railpack auto-detects)

**Backend service:**
- Root Directory: `/backend`
- No custom commands needed

### Shared: TypeScript Monorepo with pnpm

Frontend and backend share a `@myapp/shared` package.

**Frontend service:**
- Root Directory: (leave empty)
- Build Command: `pnpm --filter frontend build`
- Start Command: `pnpm --filter frontend start`
- Watch Paths: `/packages/frontend/**`, `/packages/shared/**`

**Backend service:**
- Root Directory: (leave empty)
- Build Command: `pnpm --filter backend build`
- Start Command: `pnpm --filter backend start`
- Watch Paths: `/packages/backend/**`, `/packages/shared/**`

### Shared: Turborepo

**Frontend service:**
- Root Directory: (leave empty)
- Build Command: `turbo run build --filter=frontend`
- Start Command: `turbo run start --filter=frontend`
- Watch Paths: `/apps/frontend/**`, `/packages/**`

**Backend service:**
- Root Directory: (leave empty)
- Build Command: `turbo run build --filter=backend`
- Start Command: `turbo run start --filter=backend`
- Watch Paths: `/apps/backend/**`, `/packages/**`

## Common Mistakes

### Using Root Directory for Shared Monorepos

**Wrong:**
```
Root Directory: /packages/backend
```
This breaks builds because `@myapp/shared` isn't available.

**Right:**
```
Root Directory: (empty)
Build Command: pnpm --filter backend build
Start Command: pnpm --filter backend start
```

### Forgetting Watch Paths

Without watch paths, changing `frontend/` triggers a rebuild of `backend/`.

Always set watch paths for monorepos to avoid unnecessary builds.

### Missing Shared Packages in Watch Paths

If `backend` imports from `shared`, include both in watch paths:
```
/packages/backend/**
/packages/shared/**
```

Otherwise changes to `shared` won't trigger backend rebuilds.

## Detecting Monorepo Type

Check for these indicators:

**Isolated monorepo:**
- Separate package.json in each directory
- No workspace config in root package.json
- No imports between directories

**Shared monorepo:**
- Root package.json with `workspaces` field
- `pnpm-workspace.yaml` exists
- Packages import from each other (`@myapp/shared`)
- Shared tsconfig.json at root
- turbo.json or nx.json at root




---

## 🚀 Usage

**Reference this template:** `@skill-railway-new.md`


**Platform-specific:**
- **GitHub Copilot**: Add to `.github/copilot-instructions.md`
- **Augment Code**: Use `aug context add` command
- **Cursor/Windsurf**: Reference in chat with `@filename`
- **Claude**: Add to Project Knowledge
- **ChatGPT**: Add to Custom GPT configuration
