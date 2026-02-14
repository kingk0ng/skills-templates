---
id: skill-using-neon
type: skill
name: using-neon
description: Guides and best practices for working with Neon Serverless Postgres.
  Covers getting started, local development with Neon, choosing a connection method,
  Neon features, authentication (@neondatabase/auth), PostgREST-style data API (@neondatabase/neon-js),
  Neon CLI, and Neon's Platform API/SDKs. Use for any Neon-related questions.
category: database
complexity: medium
keywords:
- api
- ci/cd
- database
- postgres
- python
- rest
- typescript
capabilities: []
token_estimate: 581
has_references: true
reference_count: 14
---

<!-- Converted from Claude Skill Template -->
<!-- Complexity: Medium -->
<!-- Estimated Tokens: ~581 -->
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




# using-neon

> Guides and best practices for working with Neon Serverless Postgres. Covers getting started, local development with Neon, choosing a connection method, Neon features, authentication (@neondatabase/auth), PostgREST-style data API (@neondatabase/neon-js), Neon CLI, and Neon's Platform API/SDKs. Use for any Neon-related questions.

# Neon Serverless Postgres

Neon is a serverless Postgres platform that separates compute and storage to offer autoscaling, branching, instant restore, and scale-to-zero. It's fully compatible with Postgres and works with any language, framework, or ORM that supports Postgres.

## Neon Documentation

Always reference the Neon documentation before making Neon-related claims. The documentation is the source of truth for all Neon-related information.

Below you'll find a list of resources organized by area of concern. This is meant to support you find the right documentation pages to fetch and add a bit of additonal context.

You can use the `curl` commands to fetch the documentation page as markdown:

**Documentation:**

```bash
# Get list of all Neon docs
curl https://neon.tech/llms.txt

# Fetch any doc page as markdown
curl -H "Accept: text/markdown" https://neon.tech/docs/<path>
```

Don't guess docs pages. Use the `llms.txt` index to find the relevant URL or follow the links in the resources below.

## Overview of Resources

Reference the appropriate resource file based on the user's needs:

### Core Guides

| Area               | Resource                           | When to Use                                                    |
| ------------------ | ---------------------------------- | -------------------------------------------------------------- |
| What is Neon       | `references/what-is-neon.md`       | Understanding Neon concepts, architecture, core resources      |
| Referencing Docs   | `references/referencing-docs.md`   | Looking up official documentation, verifying information       |
| Features           | `references/features.md`           | Branching, autoscaling, scale-to-zero, instant restore         |
| Getting Started    | `references/getting-started.md`    | Setting up a project, connection strings, dependencies, schema |
| Connection Methods | `references/connection-methods.md` | Choosing drivers based on platform and runtime                 |
| Developer Tools    | `references/devtools.md`           | VSCode extension, MCP server, Neon CLI (`neon init`)           |

### Database Drivers & ORMs

HTTP/WebSocket queries for serverless/edge functions.

| Area              | Resource                        | When to Use                                         |
| ----------------- | ------------------------------- | --------------------------------------------------- |
| Serverless Driver | `references/neon-serverless.md` | `@neondatabase/serverless` - HTTP/WebSocket queries |
| Drizzle ORM       | `references/neon-drizzle.md`    | Drizzle ORM integration with Neon                   |

### Auth & Data API SDKs

Authentication and PostgREST-style data API for Neon.

| Area        | Resource                  | When to Use                                                         |
| ----------- | ------------------------- | ------------------------------------------------------------------- |
| Neon Auth   | `references/neon-auth.md` | `@neondatabase/auth` - Authentication only                          |
| Neon JS SDK | `references/neon-js.md`   | `@neondatabase/neon-js` - Auth + Data API (PostgREST-style queries) |

### Neon Platform API & CLI

Managing Neon resources programmatically via REST API, SDKs, or CLI.

| Area                  | Resource                            | When to Use                                  |
| --------------------- | ----------------------------------- | -------------------------------------------- |
| Platform API Overview | `references/neon-platform-api.md`   | Managing Neon resources via REST API         |
| Neon CLI              | `references/neon-cli.md`            | Terminal workflows, scripts, CI/CD pipelines |
| TypeScript SDK        | `references/neon-typescript-sdk.md` | `@neondatabase/api-client`                   |
| Python SDK            | `references/neon-python-sdk.md`     | `neon-api` package                           |


---


## 📚 Reference Materials


### Devtools

# Neon Developer Tools

Neon provides developer tools to enhance your local development workflow, including a VSCode extension and MCP server for AI-assisted development.

## Quick Setup with neon init

The fastest way to set up all Neon developer tools:

```bash
npx neon init
```

This command:

- Installs the Neon VSCode extension
- Configures the Neon MCP server for AI assistants
- Sets up your local environment for Neon development

For full CLI reference:

```bash
curl -H "Accept: text/markdown" https://neon.tech/docs/reference/cli-init
```

## VSCode Extension

The Neon VSCode extension provides:

- **Database Explorer**: Browse projects, branches, tables, and data
- **SQL Editor**: Write and execute queries with IntelliSense
- **Branch Management**: Create, switch, and manage database branches
- **Connection String Access**: Quick copy of connection strings

**Install from VSCode:**

1. Open Extensions (Cmd/Ctrl+Shift+X)
2. Search "Neon"
3. Install "Neon" by Neon

**Or via command line:**

```bash
code --install-extension neon.neon-vscode
```

For detailed documentation:

```bash
curl -H "Accept: text/markdown" https://neon.tech/docs/local/vscode-extension
```

## Neon MCP Server

The Neon MCP (Model Context Protocol) server enables AI assistants like Claude, Cursor, and GitHub Copilot to interact with your Neon databases directly.

### Capabilities

The MCP server provides AI assistants with:

- **Project Management**: List, create, describe, and delete projects
- **Branch Operations**: Create branches, compare schemas, reset from parent
- **SQL Execution**: Run queries and transactions
- **Schema Operations**: Describe tables, get database structure
- **Migrations**: Prepare and complete database migrations with safety checks
- **Query Tuning**: Analyze and optimize slow queries
- **Neon Auth**: Provision authentication for your branches

### Setup

**Option 1: Via neon init (Recommended)**

```bash
npx neon init
```

**Option 2: Manual Configuration**

Add to your AI assistant's MCP configuration:

```json
{
  "mcpServers": {
    "neon": {
      "command": "npx",
      "args": ["-y", "@neondatabase/mcp-server-neon"],
      "env": {
        "NEON_API_KEY": "your-api-key"
      }
    }
  }
}
```

Get your API key from: https://console.neon.tech/app/settings/api-keys

### Common MCP Operations

| Operation                    | What It Does                  |
| ---------------------------- | ----------------------------- |
| `list_projects`              | Show all Neon projects        |
| `create_project`             | Create a new project          |
| `run_sql`                    | Execute SQL queries           |
| `get_connection_string`      | Get database connection URL   |
| `create_branch`              | Create a database branch      |
| `prepare_database_migration` | Safely prepare schema changes |
| `provision_neon_auth`        | Set up Neon Auth              |

For full MCP server documentation:

```bash
curl -H "Accept: text/markdown" https://neon.tech/docs/ai/neon-mcp-server
```

## Documentation Resources

| Topic              | URL                                           |
| ------------------ | --------------------------------------------- |
| CLI Init Command   | https://neon.tech/docs/reference/cli-init     |
| VSCode Extension   | https://neon.tech/docs/local/vscode-extension |
| MCP Server         | https://neon.tech/docs/ai/neon-mcp-server     |
| Neon CLI Reference | https://neon.tech/docs/reference/neon-cli     |




### Neon Cli

# Neon CLI

The Neon CLI is a command-line interface for managing Neon Serverless Postgres directly from your terminal. It provides the same capabilities as the Neon Platform API and is ideal for scripting, CI/CD pipelines, and developers who prefer terminal workflows.

## Installation

**macOS (Homebrew):**

```bash
brew install neonctl
```

**npm (cross-platform):**

```bash
npm install -g neonctl
```

**Direct download:**

```bash
curl -fsSL https://neon.tech/install.sh | bash
```

## Authentication

Authenticate with your Neon account:

```bash
neonctl auth
```

This opens a browser for OAuth authentication and stores credentials locally.

For CI/CD or non-interactive environments, use an API key:

```bash
export NEON_API_KEY=your-api-key
```

Get your API key from: https://console.neon.tech/app/settings/api-keys

## Common Commands

### Project Management

```bash
# List all projects
neonctl projects list

# Create a new project
neonctl projects create --name my-project

# Get project details
neonctl projects get <project-id>

# Delete a project
neonctl projects delete <project-id>
```

### Branch Operations

```bash
# List branches
neonctl branches list --project-id <project-id>

# Create a branch
neonctl branches create --project-id <project-id> --name dev

# Delete a branch
neonctl branches delete <branch-id> --project-id <project-id>
```

### Connection Strings

```bash
# Get connection string
neonctl connection-string --project-id <project-id>

# Get connection string for specific branch
neonctl connection-string --project-id <project-id> --branch-id <branch-id>

# Get pooled connection string
neonctl connection-string --project-id <project-id> --pooled
```

### SQL Execution

```bash
# Run SQL query
neonctl sql "SELECT * FROM users LIMIT 10" --project-id <project-id>

# Run SQL from file
neonctl sql --file schema.sql --project-id <project-id>
```

### Database Management

```bash
# List databases
neonctl databases list --project-id <project-id> --branch-id <branch-id>

# Create database
neonctl databases create --project-id <project-id> --name mydb

# List roles
neonctl roles list --project-id <project-id> --branch-id <branch-id>
```

## Output Formats

The CLI supports multiple output formats:

```bash
# JSON output (default for scripting)
neonctl projects list --output json

# Table output (human-readable)
neonctl projects list --output table

# YAML output
neonctl projects list --output yaml
```

## CI/CD Integration

Example GitHub Actions workflow:

```yaml
- name: Create preview branch
  env:
    NEON_API_KEY: ${{ secrets.NEON_API_KEY }}
  run: |
    neonctl branches create \
      --project-id ${{ vars.NEON_PROJECT_ID }} \
      --name preview-${{ github.event.pull_request.number }}
```

## CLI vs MCP Server vs SDKs

| Tool           | Best For                                          |
| -------------- | ------------------------------------------------- |
| Neon CLI       | Terminal workflows, scripts, CI/CD pipelines      |
| MCP Server     | AI-assisted development with Claude, Cursor, etc. |
| TypeScript SDK | Programmatic access in Node.js/TypeScript apps    |
| Python SDK     | Programmatic access in Python applications        |
| REST API       | Direct HTTP integration in any language           |

## Documentation Resources

| Topic          | URL                                                    |
| -------------- | ------------------------------------------------------ |
| CLI Reference  | https://neon.tech/docs/reference/neon-cli              |
| CLI Install    | https://neon.tech/docs/reference/cli-install           |
| CLI Auth       | https://neon.tech/docs/reference/cli-auth              |
| CLI Projects   | https://neon.tech/docs/reference/cli-projects          |
| CLI Branches   | https://neon.tech/docs/reference/cli-branches          |
| CLI Connection | https://neon.tech/docs/reference/cli-connection-string |

Fetch CLI documentation:

```bash
curl -H "Accept: text/markdown" https://neon.tech/docs/reference/neon-cli
```




### Referencing Docs

# Referencing Neon Docs

The Neon documentation is the source of truth for all Neon-related information. Always verify Neon-related claims, configurations, and best practices against the official documentation.

## Getting the Documentation Index

To get a list of all available Neon documentation pages:

```bash
curl https://neon.tech/llms.txt
```

This returns an index of all documentation pages with their URLs and descriptions.

## Fetching Individual Documentation Pages

To fetch any documentation page as markdown for review:

```bash
curl -H "Accept: text/markdown" https://neon.tech/docs/<path>
```

**Examples:**

```bash
# Fetch the API reference
curl -H "Accept: text/markdown" https://neon.tech/docs/reference/api-reference

# Fetch connection pooling docs
curl -H "Accept: text/markdown" https://neon.tech/docs/connect/connection-pooling

# Fetch branching documentation
curl -H "Accept: text/markdown" https://neon.tech/docs/introduction/branching

# Fetch serverless driver docs
curl -H "Accept: text/markdown" https://neon.tech/docs/serverless/serverless-driver
```

## Common Documentation Paths

| Topic              | Path                                 |
| ------------------ | ------------------------------------ |
| Introduction       | `/docs/introduction`                 |
| Branching          | `/docs/introduction/branching`       |
| Autoscaling        | `/docs/introduction/autoscaling`     |
| Scale to Zero      | `/docs/introduction/scale-to-zero`   |
| Connection Pooling | `/docs/connect/connection-pooling`   |
| Serverless Driver  | `/docs/serverless/serverless-driver` |
| JavaScript SDK     | `/docs/reference/javascript-sdk`     |
| API Reference      | `/docs/reference/api-reference`      |
| TypeScript SDK     | `/docs/reference/typescript-sdk`     |
| Python SDK         | `/docs/reference/python-sdk`         |

## Framework and Language Guides

```bash
# Next.js
curl -H "Accept: text/markdown" https://neon.tech/docs/guides/nextjs

# Django
curl -H "Accept: text/markdown" https://neon.tech/docs/guides/django

# Drizzle ORM
curl -H "Accept: text/markdown" https://neon.tech/docs/guides/drizzle

# Prisma
curl -H "Accept: text/markdown" https://neon.tech/docs/guides/prisma
```

## Best Practices

1. **Always verify** - When answering questions about Neon features, APIs, or configurations, fetch the relevant documentation to verify your response is accurate.

2. **Check llms.txt first** - If you're unsure which documentation page covers a topic, fetch the llms.txt index to find the relevant URL. Don't make up URLs.

3. **Docs are the source of truth** - If there's any conflict between your training data and the documentation, the documentation is correct. Neon features and APIs evolve, so always defer to the current docs.

4. **Cite your sources** - When providing information from the docs, reference the documentation URL so users can read more if needed.




### Getting Started

# Getting Started with Neon

Interactive guide to help users get started with Neon in their project. Sets up their Neon project (with a connection string) and connects their database to their code.

For the official getting started guide:

```bash
curl -H "Accept: text/markdown" https://neon.tech/docs/get-started/signing-up
```

## Interactive Setup Flow

### Step 1: Check Organizations and Projects

**First, check for organizations:**

- If they have 1 organization: Default to that organization
- If they have multiple organizations: List all and ask which one to use

**Then, check for projects within the selected organization:**

- **No projects**: Ask if they want to create a new project
- **1 project**: Ask "Would you like to use '{project_name}' or create a new one?"
- **Multiple projects (<6)**: List all and let them choose
- **Many projects (6+)**: List recent projects, offer to create new or specify by name/ID

### Step 2: Database Setup

**Get the connection string:**

- Use the MCP server to get the connection string for the selected project

**Configure it for their environment:**

- Most projects use a `.env` file with `DATABASE_URL`
- For other setups, check project structure and ask

**Before modifying .env:**

1. Try to read the .env file first
2. If readable: Use search_replace to update or append
3. If unreadable: Use append command or show the line to add manually:

```
DATABASE_URL=postgresql://user:password@host/database
```

### Step 3: Install Dependencies

Recommend drivers based on deployment platform and runtime. For detailed guidance, see `connection-methods.md`.

**Quick Recommendations:**

| Environment              | Driver                     | Install                                |
| ------------------------ | -------------------------- | -------------------------------------- |
| Vercel (Edge/Serverless) | `@neondatabase/serverless` | `npm install @neondatabase/serverless` |
| Cloudflare Workers       | `@neondatabase/serverless` | `npm install @neondatabase/serverless` |
| AWS Lambda               | `@neondatabase/serverless` | `npm install @neondatabase/serverless` |
| Traditional Node.js      | `pg`                       | `npm install pg`                       |
| Long-running servers     | `pg` with pooling          | `npm install pg`                       |

For detailed serverless driver usage, see `neon-serverless.md`.
For complex scenarios (multiple runtimes, hybrid architectures), reference `connection-methods.md`.

### Step 4: Understand the Project

**If it's an empty/new project:**
Ask briefly (1-2 questions):

- What are they building?
- Any specific technologies?

**If it's an established project:**
Skip questions - infer from codebase. Update relevant code to use the driver.

### Step 5: Authentication (Optional)

**Skip if project doesn't need auth** (CLI tools, scripts, static sites).

**If project could benefit from auth:**
Ask: "Does your app need user authentication? Neon Auth can handle sign-in/sign-up, social login, and session management."

**If they want auth:**

- Use MCP server `provision_neon_auth` tool
- Guide through framework-specific setup
- Configure environment variables
- Set up basic auth code

For detailed auth setup, see `neon-auth.md`. For auth + database queries, see `neon-js.md`.

### Step 6: ORM Setup

**Check for existing ORM** (Prisma, Drizzle, TypeORM).

**If no ORM found:**
Ask: "Want to set up an ORM for type-safe database queries?"

If yes, suggest based on project. If no, proceed with raw SQL.

For Drizzle ORM integration, see `neon-drizzle.md`.

### Step 7: Schema Setup

**Check for existing schema:**

- SQL migration files
- ORM schemas (Prisma, Drizzle)
- Database initialization scripts

**If existing schema found:**
Ask: "Found existing schema definitions. Want to migrate these to your Neon database?"

**If no schema:**
Ask if they want to:

1. Create a simple example schema (users table)
2. Design a custom schema together
3. Skip schema setup for now

**Example schema:**

```sql
CREATE TABLE users (
  id SERIAL PRIMARY KEY,
  email VARCHAR(255) UNIQUE NOT NULL,
  name VARCHAR(255),
  created_at TIMESTAMP DEFAULT NOW()
);
```

### Step 8: What's Next

"You're all set! Here are some things I can help with:

- Neon-specific features (branching, autoscaling, scale-to-zero)
- Connection pooling for production
- Writing queries or building API endpoints
- Database migrations and schema changes
- Performance optimization"

## Security Best Practices

1. Never commit connection strings to version control
2. Use environment variables for all credentials
3. Prefer SSL connections (default in Neon)
4. Use least-privilege database roles
5. Rotate API keys and passwords regularly

## Resume Support

If user says "Continue with Neon setup", check what's already configured:

- MCP server connection
- .env file with DATABASE_URL
- Dependencies installed
- Schema created

Then resume from where they left off.

## Developer Tools

For the best development experience, set up Neon's developer tools:

```bash
npx neon init
```

This installs the VSCode extension and configures the MCP server for AI-assisted development.

For detailed setup instructions, see `devtools.md`.

## Documentation Resources

| Topic              | URL                                                 |
| ------------------ | --------------------------------------------------- |
| Getting Started    | https://neon.tech/docs/get-started/signing-up       |
| Connecting to Neon | https://neon.tech/docs/connect/connect-intro        |
| Connection String  | https://neon.tech/docs/connect/connect-from-any-app |
| Frameworks Guide   | https://neon.tech/docs/get-started/frameworks       |
| ORMs Guide         | https://neon.tech/docs/get-started/orms             |
| VSCode Extension   | https://neon.tech/docs/local/vscode-extension       |
| MCP Server         | https://neon.tech/docs/ai/neon-mcp-server           |




### Features

# Neon Features

Overview of Neon's key platform features. For detailed information, fetch the official docs.

## Branching

Create instant, copy-on-write clones of your database at any point in time. Branches are isolated environments perfect for development, testing, and preview deployments.

```bash
curl -H "Accept: text/markdown" https://neon.tech/docs/introduction/branching
```

**Key Points:**

- Branches are instant (no data copying)
- Copy-on-write means branches only store changes from parent
- Use for: dev environments, staging, testing, preview deployments
- Branches can have their own compute endpoint

**Use Cases:**

| Use Case            | Description                                 |
| ------------------- | ------------------------------------------- |
| Development         | Each developer gets isolated branch         |
| Preview Deployments | Branch per PR/preview URL                   |
| Testing             | Reset test data by recreating branch        |
| Schema Migrations   | Test migrations on branch before production |

If the Neon MCP server is available, you can use it to list and create branches. Otherwise, refer to the Neon CLI or Platform API.

## Autoscaling

Neon automatically scales compute resources based on workload demand.

```bash
curl -H "Accept: text/markdown" https://neon.tech/docs/introduction/autoscaling
```

**Key Points:**

- Scales between min and max compute units (CUs)
- Responds to CPU and memory pressure
- No manual intervention required
- Configure limits per project or endpoint

## Scale to Zero

Databases automatically suspend after a period of inactivity, reducing costs to storage-only.

```bash
curl -H "Accept: text/markdown" https://neon.tech/docs/introduction/scale-to-zero
```

**Key Points:**

- Default suspend after 5 minutes of inactivity (configurable)
- First query after suspend has ~500ms cold start
- Storage is always maintained
- Perfect for dev/staging environments with intermittent use

## Instant Restore

Restore your database to any point within your retention window without backups.

```bash
curl -H "Accept: text/markdown" https://neon.tech/docs/introduction/branch-restore
```

**Key Points:**

- Point-in-time recovery without pre-configured backups
- Restore window depends on plan (7-30 days)
- Create branches from any point in history
- Time Travel queries to view historical data

## Read Replicas

Create read-only compute endpoints to scale read workloads.

```bash
curl -H "Accept: text/markdown" https://neon.tech/docs/introduction/read-replicas
```

**Key Points:**

- Read replicas share storage with primary (no data duplication)
- Instant creation
- Independent scaling from primary
- Use for: analytics, reporting, read-heavy workloads

## Connection Pooling

Built-in connection pooling via PgBouncer for efficient connection management.

```bash
curl -H "Accept: text/markdown" https://neon.tech/docs/connect/connection-pooling
```

**Key Points:**

- Enabled by adding `-pooler` to endpoint hostname
- Transaction mode by default
- Supports up to 10,000 concurrent connections
- Essential for serverless environments

## IP Allow Lists

Restrict database access to specific IP addresses or ranges.

```bash
curl -H "Accept: text/markdown" https://neon.tech/docs/introduction/ip-allow
```

## Logical Replication

Replicate data to/from external Postgres databases.

```bash
curl -H "Accept: text/markdown" https://neon.tech/docs/guides/logical-replication-guide
```

## Neon Auth

Managed authentication that branches with your database.

```bash
curl -H "Accept: text/markdown" https://neon.tech/docs/auth/overview
```

**Key Points:**

- Sign-in/sign-up with email, social providers (Google, GitHub)
- Session management
- UI components included
- Branches with your database

For setup, see `neon-auth.md`. For auth + data API, see `neon-js.md`.

## Feature Documentation Reference

| Feature             | Documentation                                           | Resource       |
| ------------------- | ------------------------------------------------------- | -------------- |
| Branching           | https://neon.tech/docs/introduction/branching           | -              |
| Autoscaling         | https://neon.tech/docs/introduction/autoscaling         | -              |
| Scale to Zero       | https://neon.tech/docs/introduction/scale-to-zero       | -              |
| Instant Restore     | https://neon.tech/docs/introduction/branch-restore      | -              |
| Read Replicas       | https://neon.tech/docs/introduction/read-replicas       | -              |
| Connection Pooling  | https://neon.tech/docs/connect/connection-pooling       | -              |
| IP Allow            | https://neon.tech/docs/introduction/ip-allow            | -              |
| Logical Replication | https://neon.tech/docs/guides/logical-replication-guide | -              |
| Neon Auth           | https://neon.tech/docs/auth/overview                    | `neon-auth.md` |
| Data API            | https://neon.tech/docs/data-api/overview                | `neon-js.md`   |




### Neon Serverless

# Neon Serverless Driver

Patterns and best practices for connecting to Neon databases in serverless environments using the `@neondatabase/serverless` driver. The driver connects over **HTTP** for fast, single queries or **WebSockets** for `node-postgres` compatibility and interactive transactions.

For official documentation:

```bash
curl -H "Accept: text/markdown" https://neon.tech/docs/serverless/serverless-driver
```

## Installation

```bash
# Using npm
npm install @neondatabase/serverless

# Using JSR
bunx jsr add @neon/serverless
```

**Note:** Version 1.0.0+ requires **Node.js v19 or later**.

For projects that depend on `pg` but want to use Neon's WebSocket-based connection pool:

```json
"dependencies": {
  "pg": "npm:@neondatabase/serverless@^0.10.4"
},
"overrides": {
  "pg": "npm:@neondatabase/serverless@^0.10.4"
}
```

## Connection String

Always use environment variables:

```typescript
// For HTTP queries
import { neon } from "@neondatabase/serverless";
const sql = neon(process.env.DATABASE_URL!);

// For WebSocket connections
import { Pool } from "@neondatabase/serverless";
const pool = new Pool({ connectionString: process.env.DATABASE_URL! });
```

**Never hardcode credentials:**

```typescript
// AVOID
const sql = neon("postgres://username:password@host.neon.tech/neondb");
```

## HTTP Queries with `neon` function

Ideal for simple, "one-shot" queries in serverless/edge environments. Uses HTTP `fetch` - fastest method for single queries.

### Parameterized Queries

Use tagged template literals for safe parameter interpolation:

```typescript
const [post] = await sql`SELECT * FROM posts WHERE id = ${postId}`;
```

For manually constructed queries:

```typescript
const [post] = await sql.query("SELECT * FROM posts WHERE id = $1", [postId]);
```

**Never concatenate user input:**

```typescript
// AVOID: SQL Injection Risk
const [post] = await sql("SELECT * FROM posts WHERE id = " + postId);
```

### Configuration Options

```typescript
// Return rows as arrays instead of objects
const sqlArrayMode = neon(process.env.DATABASE_URL!, { arrayMode: true });
const rows = await sqlArrayMode`SELECT id, title FROM posts`;
// rows -> [[1, "First Post"], [2, "Second Post"]]

// Get full results including row count and field metadata
const sqlFull = neon(process.env.DATABASE_URL!, { fullResults: true });
const result = await sqlFull`SELECT * FROM posts LIMIT 1`;
// result -> { rows: [...], fields: [...], rowCount: 1, ... }
```

## WebSocket Connections with `Pool` and `Client`

Use for `node-postgres` compatibility, interactive transactions, or session support.

### WebSocket Configuration

For Node.js v21 and earlier:

```typescript
import { Pool, neonConfig } from "@neondatabase/serverless";
import ws from "ws";

// Required for Node.js < v22
neonConfig.webSocketConstructor = ws;

const pool = new Pool({ connectionString: process.env.DATABASE_URL! });
```

### Serverless Lifecycle Management

Create, use, and close the pool within the same invocation:

```typescript
// Vercel Edge Functions example
export default async (req: Request, ctx: ExecutionContext) => {
  const pool = new Pool({ connectionString: process.env.DATABASE_URL! });

  try {
    const { rows } = await pool.query("SELECT * FROM users");
    return new Response(JSON.stringify(rows));
  } catch (err) {
    console.error(err);
    return new Response("Database error", { status: 500 });
  } finally {
    ctx.waitUntil(pool.end());
  }
};
```

**Avoid** creating a global `Pool` instance outside the handler.

## Transactions

### HTTP Transactions

For running multiple queries in a single, non-interactive transaction:

```typescript
const [newUser, newProfile] = await sql.transaction(
  [
    sql`INSERT INTO users(name) VALUES(${name}) RETURNING id`,
    sql`INSERT INTO profiles(user_id, bio) VALUES(${userId}, ${bio})`,
  ],
  {
    isolationLevel: "ReadCommitted",
    readOnly: false,
  },
);
```

### Interactive Transactions

For complex transactions with conditional logic:

```typescript
const pool = new Pool({ connectionString: process.env.DATABASE_URL! });
const client = await pool.connect();
try {
  await client.query("BEGIN");
  const {
    rows: [{ id }],
  } = await client.query("INSERT INTO users(name) VALUES($1) RETURNING id", [
    name,
  ]);
  await client.query("INSERT INTO profiles(user_id, bio) VALUES($1, $2)", [
    id,
    bio,
  ]);
  await client.query("COMMIT");
} catch (err) {
  await client.query("ROLLBACK");
  throw err;
} finally {
  client.release();
  await pool.end();
}
```

## Environment-Specific Optimizations

```javascript
// For Vercel Edge Functions, specify nearest region
export const config = {
  runtime: "edge",
  regions: ["iad1"], // Region nearest to your Neon DB
};

// For Cloudflare Workers, consider using Hyperdrive
// https://neon.tech/blog/hyperdrive-neon-faq
```

## ORM Integration

For Drizzle ORM integration with the serverless driver, see `neon-drizzle.md`.

### Prisma

```typescript
import { neonConfig } from "@neondatabase/serverless";
import { PrismaNeon, PrismaNeonHTTP } from "@prisma/adapter-neon";
import { PrismaClient } from "@prisma/client";
import ws from "ws";

const connectionString = process.env.DATABASE_URL;
neonConfig.webSocketConstructor = ws;

// HTTP adapter
const adapterHttp = new PrismaNeonHTTP(connectionString!, {});
export const prismaClientHttp = new PrismaClient({ adapter: adapterHttp });

// WebSocket adapter
const adapterWs = new PrismaNeon({ connectionString });
export const prismaClientWs = new PrismaClient({ adapter: adapterWs });
```

### Kysely

```typescript
import { Pool } from "@neondatabase/serverless";
import { Kysely, PostgresDialect } from "kysely";

const dialect = new PostgresDialect({
  pool: new Pool({ connectionString: process.env.DATABASE_URL }),
});

const db = new Kysely({ dialect });
```

**NOTE:** Do not pass the `neon()` function to ORMs that expect a `node-postgres` compatible `Pool`.

## Error Handling

```javascript
// Pool error handling
const pool = new Pool({ connectionString: process.env.DATABASE_URL });
pool.on("error", (err) => {
  console.error("Unexpected error on idle client", err);
  process.exit(-1);
});

// Query error handling
try {
  const [post] = await sql`SELECT * FROM posts WHERE id = ${postId}`;
  if (!post) {
    return new Response("Not found", { status: 404 });
  }
} catch (err) {
  console.error("Database query failed:", err);
  return new Response("Server error", { status: 500 });
}
```




### Neon Platform Api

# Neon Platform API

The Neon Platform API allows you to manage Neon projects, branches, databases, and resources programmatically. You can use the REST API directly or through official SDKs.

## Options

| Method         | Package/URL                         | Best For                        |
| -------------- | ----------------------------------- | ------------------------------- |
| REST API       | `https://console.neon.tech/api/v2/` | Any language, direct HTTP calls |
| TypeScript SDK | `@neondatabase/api-client`          | Node.js, TypeScript projects    |
| Python SDK     | `neon-api`                          | Python scripts and applications |
| CLI            | `neonctl`                           | Terminal-based management       |

## Documentation

```bash
# REST API documentation
curl -H "Accept: text/markdown" https://neon.tech/docs/reference/api-reference

# TypeScript SDK
curl -H "Accept: text/markdown" https://neon.tech/docs/reference/typescript-sdk

# Python SDK
curl -H "Accept: text/markdown" https://neon.tech/docs/reference/python-sdk

# CLI
curl -H "Accept: text/markdown" https://neon.tech/docs/reference/neon-cli
```

For the interactive API reference: https://api-docs.neon.tech/reference/getting-started-with-neon-api

## Sub-Resources

For detailed information, reference the appropriate sub-resource:

### REST API Details

| Topic                         | Resource                         |
| ----------------------------- | -------------------------------- |
| Guidelines, Auth, Rate Limits | `neon-rest-api/guidelines.md`    |
| Projects                      | `neon-rest-api/projects.md`      |
| Branches, Databases, Roles    | `neon-rest-api/branches.md`      |
| Compute Endpoints             | `neon-rest-api/endpoints.md`     |
| API Keys                      | `neon-rest-api/keys.md`          |
| Operations                    | `neon-rest-api/operations.md`    |
| Organizations                 | `neon-rest-api/organizations.md` |

### SDKs

| Language   | Resource                 |
| ---------- | ------------------------ |
| TypeScript | `neon-typescript-sdk.md` |
| Python     | `neon-python-sdk.md`     |

## Quick Start

### Authentication

All API requests require a Neon API key:

```bash
Authorization: Bearer $NEON_API_KEY
```

### API Key Types

| Type           | Scope                           | Best For                      |
| -------------- | ------------------------------- | ----------------------------- |
| Personal       | All projects user has access to | Individual use, scripting     |
| Organization   | Entire organization             | CI/CD, org-wide automation    |
| Project-scoped | Single project only             | Project-specific integrations |

### Rate Limits

- 700 requests per minute (~11 per second)
- Bursts up to 40 requests per second per route
- Handle `429 Too Many Requests` with retry/backoff

## Common Operations Quick Reference

| Operation          | REST API                                   | TypeScript SDK              | Python SDK            |
| ------------------ | ------------------------------------------ | --------------------------- | --------------------- |
| List Projects      | `GET /projects`                            | `listProjects({})`          | `projects()`          |
| Create Project     | `POST /projects`                           | `createProject({...})`      | `project_create(...)` |
| Get Connection URI | `GET /projects/{id}/connection_uri`        | `getConnectionUri({...})`   | `connection_uri(...)` |
| Create Branch      | `POST /projects/{id}/branches`             | `createProjectBranch(...)`  | `branch_create(...)`  |
| Start Endpoint     | `POST /projects/{id}/endpoints/{id}/start` | `startProjectEndpoint(...)` | `endpoint_start(...)` |

## Error Handling

| Status | Meaning      | Action                       |
| ------ | ------------ | ---------------------------- |
| 401    | Unauthorized | Check API key                |
| 404    | Not Found    | Verify resource ID           |
| 429    | Rate Limited | Implement retry with backoff |
| 500    | Server Error | Retry or contact support     |




### Neon Typescript Sdk

# Neon TypeScript SDK

The `@neondatabase/api-client` TypeScript SDK is a typed wrapper around the Neon REST API. It provides methods for managing all Neon resources, including projects, branches, endpoints, roles, and databases.

For core concepts (Organization, Project, Branch, Endpoint, etc.), see `what-is-neon.md`.

## Documentation

```bash
curl -H "Accept: text/markdown" https://neon.tech/docs/reference/typescript-sdk
```

## Installation

```bash
npm install @neondatabase/api-client
```

## Authentication

```typescript
import { createApiClient } from "@neondatabase/api-client";

const apiKey = process.env.NEON_API_KEY;
if (!apiKey) {
  throw new Error("NEON_API_KEY environment variable is not set.");
}

const apiClient = createApiClient({ apiKey });
```

## Projects

### List Projects

```typescript
const response = await apiClient.listProjects({});
console.log("Projects:", response.data.projects);
```

### Create Project

```typescript
const response = await apiClient.createProject({
  project: { name: "my-project", pg_version: 17, region_id: "aws-us-east-2" },
});
console.log(
  "Connection URI:",
  response.data.connection_uris[0]?.connection_uri,
);
```

### Get Project

```typescript
const response = await apiClient.getProject("your-project-id");
```

### Update Project

```typescript
await apiClient.updateProject("your-project-id", {
  project: { name: "new-name" },
});
```

### Delete Project

```typescript
await apiClient.deleteProject("project-id");
```

### Get Connection URI

```typescript
const response = await apiClient.getConnectionUri({
  projectId: "your-project-id",
  database_name: "neondb",
  role_name: "neondb_owner",
  pooled: true,
});
console.log("URI:", response.data.uri);
```

## Branches

### Create Branch

```typescript
import { EndpointType } from "@neondatabase/api-client";

const response = await apiClient.createProjectBranch("your-project-id", {
  branch: { name: "feature-branch" },
  endpoints: [{ type: EndpointType.ReadWrite, autoscaling_limit_max_cu: 1 }],
});
```

### List Branches

```typescript
const response = await apiClient.listProjectBranches({
  projectId: "your-project-id",
});
```

### Get Branch

```typescript
const response = await apiClient.getProjectBranch("your-project-id", "br-xxx");
```

### Update Branch

```typescript
await apiClient.updateProjectBranch("your-project-id", "br-xxx", {
  branch: { name: "updated-name" },
});
```

### Delete Branch

```typescript
await apiClient.deleteProjectBranch("your-project-id", "br-xxx");
```

## Databases

### Create Database

```typescript
await apiClient.createProjectBranchDatabase("your-project-id", "br-xxx", {
  database: { name: "my-app-db", owner_name: "neondb_owner" },
});
```

### List Databases

```typescript
const response = await apiClient.listProjectBranchDatabases(
  "your-project-id",
  "br-xxx",
);
```

### Delete Database

```typescript
await apiClient.deleteProjectBranchDatabase(
  "your-project-id",
  "br-xxx",
  "my-app-db",
);
```

## Roles

### Create Role

```typescript
const response = await apiClient.createProjectBranchRole(
  "your-project-id",
  "br-xxx",
  {
    role: { name: "app_user" },
  },
);
console.log("Password:", response.data.role.password);
```

### List Roles

```typescript
const response = await apiClient.listProjectBranchRoles(
  "your-project-id",
  "br-xxx",
);
```

### Delete Role

```typescript
await apiClient.deleteProjectBranchRole(
  "your-project-id",
  "br-xxx",
  "app_user",
);
```

## Endpoints

### Create Endpoint

```typescript
import { EndpointType } from "@neondatabase/api-client";

const response = await apiClient.createProjectEndpoint("your-project-id", {
  endpoint: { branch_id: "br-xxx", type: EndpointType.ReadOnly },
});
```

### List Endpoints

```typescript
const response = await apiClient.listProjectEndpoints("your-project-id");
```

### Start/Suspend/Restart Endpoint

```typescript
// Start
await apiClient.startProjectEndpoint("your-project-id", "ep-xxx");

// Suspend
await apiClient.suspendProjectEndpoint("your-project-id", "ep-xxx");

// Restart
await apiClient.restartProjectEndpoint("your-project-id", "ep-xxx");
```

### Update Endpoint

```typescript
await apiClient.updateProjectEndpoint("your-project-id", "ep-xxx", {
  endpoint: { autoscaling_limit_max_cu: 2 },
});
```

### Delete Endpoint

```typescript
await apiClient.deleteProjectEndpoint("your-project-id", "ep-xxx");
```

## API Keys

### List API Keys

```typescript
const response = await apiClient.listApiKeys();
```

### Create API Key

```typescript
const response = await apiClient.createApiKey({ key_name: "my-script-key" });
console.log("Key:", response.data.key); // Store securely!
```

### Revoke API Key

```typescript
await apiClient.revokeApiKey(1234);
```

## Operations

### List Operations

```typescript
const response = await apiClient.listProjectOperations({
  projectId: "your-project-id",
});
```

### Get Operation

```typescript
const response = await apiClient.getProjectOperation(
  "your-project-id",
  "op-xxx",
);
```

## Organizations

### Get Organization

```typescript
const response = await apiClient.getOrganization("org-xxx");
```

### List Members

```typescript
const response = await apiClient.getOrganizationMembers("org-xxx");
```

### Create Org API Key

```typescript
const response = await apiClient.createOrgApiKey("org-xxx", {
  key_name: "ci-key",
  project_id: "project-xxx", // Optional: scope to project
});
```

### Invite Member

```typescript
import { MemberRole } from "@neondatabase/api-client";

await apiClient.createOrganizationInvitations("org-xxx", {
  invitations: [{ email: "dev@example.com", role: MemberRole.Member }],
});
```

## Error Handling

```typescript
async function safeApiOperation(projectId: string) {
  try {
    const response = await apiClient.getProject(projectId);
    return response.data;
  } catch (error: any) {
    if (error.isAxiosError) {
      const status = error.response?.status;
      switch (status) {
        case 401:
          console.error("Check your NEON_API_KEY");
          break;
        case 404:
          console.error("Resource not found");
          break;
        case 429:
          console.error("Rate limit exceeded");
          break;
        default:
          console.error("API error:", error.response?.data?.message);
      }
    }
    return null;
  }
}
```




### Neon Drizzle

# Neon and Drizzle Integration

Integration patterns, configurations, and optimizations for using **Drizzle ORM** with **Neon** Postgres.

For official documentation:

```bash
curl -H "Accept: text/markdown" https://neon.tech/docs/guides/drizzle
```

## Choosing the Right Driver

Drizzle ORM works with multiple Postgres drivers. See `connection-methods.md` for the full decision tree.

| Platform                | TCP Support | Pooling             | Recommended Driver         |
| ----------------------- | ----------- | ------------------- | -------------------------- |
| Vercel (Fluid)          | Yes         | `@vercel/functions` | `pg` (node-postgres)       |
| Cloudflare (Hyperdrive) | Yes         | Hyperdrive          | `pg` (node-postgres)       |
| Cloudflare Workers      | No          | No                  | `@neondatabase/serverless` |
| Netlify Functions       | No          | No                  | `@neondatabase/serverless` |
| Deno Deploy             | No          | No                  | `@neondatabase/serverless` |
| Railway / Render        | Yes         | Built-in            | `pg` (node-postgres)       |

## Connection Setup

### 1. TCP with node-postgres (Long-Running Servers)

Best for Railway, Render, traditional VPS.

```bash
npm install drizzle-orm pg
npm install -D drizzle-kit @types/pg dotenv
```

```typescript
// src/db.ts
import { drizzle } from "drizzle-orm/node-postgres";
import { Pool } from "pg";

const pool = new Pool({ connectionString: process.env.DATABASE_URL });
export const db = drizzle({ client: pool });
```

### 2. Vercel Fluid Compute with Connection Pooling

```bash
npm install drizzle-orm pg @vercel/functions
npm install -D drizzle-kit @types/pg
```

```typescript
// src/db.ts
import { attachDatabasePool } from "@vercel/functions";
import { drizzle } from "drizzle-orm/node-postgres";
import { Pool } from "pg";
import * as schema from "./schema";

const pool = new Pool({ connectionString: process.env.DATABASE_URL });
attachDatabasePool(pool);

export const db = drizzle({ client: pool, schema });
```

### 3. HTTP Adapter (Edge Without TCP)

For Cloudflare Workers, Netlify Edge, Deno Deploy. Does NOT support interactive transactions.

```bash
npm install drizzle-orm @neondatabase/serverless
npm install -D drizzle-kit dotenv
```

```typescript
// src/db.ts
import { drizzle } from "drizzle-orm/neon-http";
import { neon } from "@neondatabase/serverless";

const sql = neon(process.env.DATABASE_URL!);
export const db = drizzle(sql);
```

### 4. WebSocket Adapter (Edge with Transactions)

```bash
npm install drizzle-orm @neondatabase/serverless ws
npm install -D drizzle-kit dotenv @types/ws
```

```typescript
// src/db.ts
import { drizzle } from "drizzle-orm/neon-serverless";
import { Pool, neonConfig } from "@neondatabase/serverless";
import ws from "ws";

neonConfig.webSocketConstructor = ws; // Required for Node.js < v22

const pool = new Pool({ connectionString: process.env.DATABASE_URL });
export const db = drizzle(pool);
```

## Drizzle Config

```typescript
// drizzle.config.ts
import { config } from "dotenv";
import { defineConfig } from "drizzle-kit";

config({ path: ".env.local" });

export default defineConfig({
  schema: "./src/schema.ts",
  out: "./drizzle",
  dialect: "postgresql",
  dbCredentials: {
    url: process.env.DATABASE_URL!,
  },
});
```

## Migrations

```bash
# Generate migrations
npx drizzle-kit generate

# Apply migrations
npx drizzle-kit migrate
```

## Schema Definition

```typescript
// src/schema.ts
import { pgTable, serial, text, integer, timestamp } from "drizzle-orm/pg-core";

export const usersTable = pgTable("users", {
  id: serial("id").primaryKey(),
  name: text("name").notNull(),
  email: text("email").notNull().unique(),
  role: text("role").default("user").notNull(),
  createdAt: timestamp("created_at").defaultNow().notNull(),
});

export type User = typeof usersTable.$inferSelect;
export type NewUser = typeof usersTable.$inferInsert;

export const postsTable = pgTable("posts", {
  id: serial("id").primaryKey(),
  title: text("title").notNull(),
  content: text("content").notNull(),
  userId: integer("user_id")
    .notNull()
    .references(() => usersTable.id, { onDelete: "cascade" }),
  createdAt: timestamp("created_at").defaultNow().notNull(),
});

export type Post = typeof postsTable.$inferSelect;
export type NewPost = typeof postsTable.$inferInsert;
```

## Query Patterns

### Batch Inserts

```typescript
export async function batchInsertUsers(users: NewUser[]) {
  return db.insert(usersTable).values(users).returning();
}
```

### Prepared Statements

```typescript
import { sql } from "drizzle-orm";

export const getUsersByRolePrepared = db
  .select()
  .from(usersTable)
  .where(sql`${usersTable.role} = $1`)
  .prepare("get_users_by_role");

// Usage: getUsersByRolePrepared.execute(['admin'])
```

### Transactions

```typescript
export async function createUserWithPosts(user: NewUser, posts: NewPost[]) {
  return await db.transaction(async (tx) => {
    const [newUser] = await tx.insert(usersTable).values(user).returning();

    if (posts.length > 0) {
      await tx.insert(postsTable).values(
        posts.map((post) => ({
          ...post,
          userId: newUser.id,
        })),
      );
    }

    return newUser;
  });
}
```

## Working with Neon Branches

```typescript
import { drizzle } from "drizzle-orm/neon-http";
import { neon } from "@neondatabase/serverless";

const getBranchUrl = () => {
  const env = process.env.NODE_ENV;
  if (env === "development") return process.env.DEV_DATABASE_URL;
  if (env === "test") return process.env.TEST_DATABASE_URL;
  return process.env.DATABASE_URL;
};

const sql = neon(getBranchUrl()!);
export const db = drizzle({ client: sql });
```

## Error Handling

```typescript
export async function safeNeonOperation<T>(
  operation: () => Promise<T>,
): Promise<T> {
  try {
    return await operation();
  } catch (error: any) {
    if (error.message?.includes("connection pool timeout")) {
      console.error("Neon connection pool timeout");
    }
    throw error;
  }
}
```

## Best Practices

1. **Connection Management** - See `connection-methods.md` for platform-specific guidance
2. **Neon Features** - Utilize branching for development/testing (see `features.md`)
3. **Query Optimization** - Batch operations, use prepared statements
4. **Schema Design** - Leverage Postgres-specific features, use appropriate indexes




### Neon Auth

# Neon Auth

Neon Auth provides authentication for your application. It's available as:

- `@neondatabase/auth` - Auth only (smaller bundle)
- `@neondatabase/neon-js` - Auth + Data API (full SDK, see `neon-js.md`)

For official documentation:

```bash
curl -H "Accept: text/markdown" https://neon.tech/docs/auth/overview
```

## Package Selection

| Need                    | Package                 | Bundle  |
| ----------------------- | ----------------------- | ------- |
| Auth only               | `@neondatabase/auth`    | Smaller |
| Auth + Database queries | `@neondatabase/neon-js` | Full    |

## Installation

```bash
# Auth only
npm install @neondatabase/auth

# Auth + Data API
npm install @neondatabase/neon-js
```

## Quick Setup Patterns

### Next.js App Router

**1. API Route Handler:**

```typescript
// app/api/auth/[...path]/route.ts
import { authApiHandler } from "@neondatabase/auth/next";
export const { GET, POST } = authApiHandler();
```

**2. Auth Client:**

```typescript
// lib/auth/client.ts
import { createAuthClient } from "@neondatabase/auth/next";
export const authClient = createAuthClient();
```

**3. Use in Components:**

```typescript
"use client";
import { authClient } from "@/lib/auth/client";

function AuthStatus() {
  const session = authClient.useSession();
  if (session.isPending) return <div>Loading...</div>;
  if (!session.data) return <SignInButton />;
  return <div>Hello, {session.data.user.name}</div>;
}
```

### React SPA

```typescript
import { createAuthClient } from "@neondatabase/auth";
import { BetterAuthReactAdapter } from "@neondatabase/auth/react/adapters";

const authClient = createAuthClient(import.meta.env.VITE_NEON_AUTH_URL, {
  adapter: BetterAuthReactAdapter(),
});
```

### Node.js Backend

```typescript
import { createAuthClient } from "@neondatabase/auth";

const auth = createAuthClient(process.env.NEON_AUTH_URL!);
await auth.signIn.email({ email, password });
const session = await auth.getSession();
```

## Environment Variables

```bash
# Next.js (.env.local)
NEON_AUTH_BASE_URL=https://ep-xxx.neonauth.c-2.us-east-2.aws.neon.build/dbname/auth
NEXT_PUBLIC_NEON_AUTH_URL=https://ep-xxx.neonauth.c-2.us-east-2.aws.neon.build/dbname/auth

# Vite/React (.env)
VITE_NEON_AUTH_URL=https://ep-xxx.neonauth.c-2.us-east-2.aws.neon.build/dbname/auth
```

## Sub-Resources

For detailed documentation:

| Topic                    | Resource                       |
| ------------------------ | ------------------------------ |
| Next.js App Router setup | `neon-auth/setup-nextjs.md`    |
| React SPA setup          | `neon-auth/setup-react-spa.md` |
| Auth methods reference   | `neon-auth/auth-methods.md`    |
| UI components            | `neon-auth/ui-components.md`   |
| Common mistakes          | `neon-auth/common-mistakes.md` |

## Key Imports

```typescript
// Auth client (Next.js)
import { authApiHandler, createAuthClient } from "@neondatabase/auth/next";

// Auth client (vanilla)
import { createAuthClient } from "@neondatabase/auth";

// React adapter (NOT from main entry)
import { BetterAuthReactAdapter } from "@neondatabase/auth/react/adapters";

// UI components
import {
  NeonAuthUIProvider,
  AuthView,
  SignInForm,
} from "@neondatabase/auth/react/ui";
import { authViewPaths } from "@neondatabase/auth/react/ui/server";

// CSS
import "@neondatabase/auth/ui/css";
```

## Common Mistakes

1. **Wrong adapter import**: Import `BetterAuthReactAdapter` from `auth/react/adapters` subpath
2. **Forgetting to call adapter**: Use `BetterAuthReactAdapter()` with parentheses
3. **Missing CSS**: Import from `ui/css` or `ui/tailwind` (not both)
4. **Missing "use client"**: Required for components using `useSession()`
5. **Wrong createAuthClient signature**: First arg is URL: `createAuthClient(url, { adapter })`

See `neon-auth/common-mistakes.md` for detailed examples.




### Neon Js

# Neon JS SDK

The `@neondatabase/neon-js` SDK provides a unified client for Neon Auth and Data API. It combines authentication handling with PostgREST-compatible database queries.

**Auth only?** Use `neon-auth.md` instead for smaller bundle size.

For official documentation:

```bash
curl -H "Accept: text/markdown" https://neon.tech/docs/reference/javascript-sdk
```

## Package Selection

| Use Case        | Package                      | Notes               |
| --------------- | ---------------------------- | ------------------- |
| Auth + Data API | `@neondatabase/neon-js`      | Full SDK            |
| Auth only       | `@neondatabase/auth`         | Smaller bundle      |
| Data API only   | `@neondatabase/postgrest-js` | Bring your own auth |

## Installation

```bash
npm install @neondatabase/neon-js
```

## Quick Setup Patterns

### Next.js (Most Common)

**1. API Route Handler:**

```typescript
// app/api/auth/[...path]/route.ts
import { authApiHandler } from "@neondatabase/neon-js/auth/next";
export const { GET, POST } = authApiHandler();
```

**2. Auth Client:**

```typescript
// lib/auth/client.ts
import { createAuthClient } from "@neondatabase/neon-js/auth/next";
export const authClient = createAuthClient();
```

**3. Database Client:**

```typescript
// lib/db/client.ts
import { createClient } from "@neondatabase/neon-js";
import type { Database } from "./database.types";

export const dbClient = createClient<Database>({
  auth: { url: process.env.NEXT_PUBLIC_NEON_AUTH_URL! },
  dataApi: { url: process.env.NEON_DATA_API_URL! },
});
```

### React SPA

```typescript
import { createClient } from "@neondatabase/neon-js";
import { BetterAuthReactAdapter } from "@neondatabase/neon-js/auth/react/adapters";

const client = createClient<Database>({
  auth: {
    adapter: BetterAuthReactAdapter(),
    url: import.meta.env.VITE_NEON_AUTH_URL,
  },
  dataApi: { url: import.meta.env.VITE_NEON_DATA_API_URL },
});
```

### Node.js Backend

```typescript
import { createClient } from "@neondatabase/neon-js";

const client = createClient<Database>({
  auth: { url: process.env.NEON_AUTH_URL! },
  dataApi: { url: process.env.NEON_DATA_API_URL! },
});
```

## Environment Variables

```bash
# Next.js (.env.local)
NEON_AUTH_BASE_URL=https://ep-xxx.neonauth.c-2.us-east-2.aws.neon.build/dbname/auth
NEXT_PUBLIC_NEON_AUTH_URL=https://ep-xxx.neonauth.c-2.us-east-2.aws.neon.build/dbname/auth
NEON_DATA_API_URL=https://ep-xxx.apirest.c-2.us-east-2.aws.neon.build/dbname/rest/v1

# Vite/React (.env)
VITE_NEON_AUTH_URL=https://ep-xxx.neonauth.c-2.us-east-2.aws.neon.build/dbname/auth
VITE_NEON_DATA_API_URL=https://ep-xxx.apirest.c-2.us-east-2.aws.neon.build/dbname/rest/v1
```

## Database Queries

All query methods follow PostgREST syntax (same as Supabase):

```typescript
// Select with filters
const { data } = await client
  .from("items")
  .select("id, name, status")
  .eq("status", "active")
  .order("created_at", { ascending: false })
  .limit(10);

// Insert
const { data, error } = await client
  .from("items")
  .insert({ name: "New Item", status: "pending" })
  .select()
  .single();

// Update
await client.from("items").update({ status: "completed" }).eq("id", 1);

// Delete
await client.from("items").delete().eq("id", 1);
```

For complete Data API query reference, see `neon-js/data-api.md`.

## Auth Methods

### BetterAuth API (Default)

```typescript
// Sign in/up
await client.auth.signIn.email({ email, password });
await client.auth.signUp.email({ email, password, name });
await client.auth.signOut();

// Get session
const session = await client.auth.getSession();

// Social sign-in
await client.auth.signIn.social({
  provider: "google",
  callbackURL: "/dashboard",
});
```

### Supabase-Compatible API

```typescript
import { createClient, SupabaseAuthAdapter } from "@neondatabase/neon-js";

const client = createClient({
  auth: { adapter: SupabaseAuthAdapter(), url },
  dataApi: { url },
});

await client.auth.signInWithPassword({ email, password });
await client.auth.signUp({ email, password });
const {
  data: { session },
} = await client.auth.getSession();
```

## Sub-Resources

| Topic            | Resource                     |
| ---------------- | ---------------------------- |
| Data API queries | `neon-js/data-api.md`        |
| Common mistakes  | `neon-js/common-mistakes.md` |

## Key Imports

```typescript
// Main client
import {
  createClient,
  SupabaseAuthAdapter,
  BetterAuthVanillaAdapter,
} from "@neondatabase/neon-js";

// Next.js integration
import {
  authApiHandler,
  createAuthClient,
} from "@neondatabase/neon-js/auth/next";

// React adapter (NOT from main entry - must use subpath)
import { BetterAuthReactAdapter } from "@neondatabase/neon-js/auth/react/adapters";

// UI components
import {
  NeonAuthUIProvider,
  AuthView,
  SignInForm,
} from "@neondatabase/neon-js/auth/react/ui";
import { authViewPaths } from "@neondatabase/neon-js/auth/react/ui/server";

// CSS (choose one)
import "@neondatabase/neon-js/ui/css"; // Without Tailwind
// @import '@neondatabase/neon-js/ui/tailwind'; // With Tailwind v4 (in CSS file)
```

## Generate Types

```bash
npx neon-js gen-types --db-url "postgresql://..." --output src/types/database.ts
```

## Common Mistakes

1. **Wrong adapter import**: Import `BetterAuthReactAdapter` from `auth/react/adapters` subpath
2. **Forgetting to call adapter**: Use `SupabaseAuthAdapter()` with parentheses
3. **Missing CSS import**: Import from `ui/css` or `ui/tailwind` (not both)
4. **Wrong package for auth-only**: Use `@neondatabase/auth` for smaller bundle
5. **Missing "use client"**: Required for auth client components

See `neon-js/common-mistakes.md` for detailed examples.




### Neon Python Sdk

# Neon Python SDK

The `neon-api` Python SDK is a Pythonic wrapper around the Neon REST API. It provides methods for managing all Neon resources, including projects, branches, endpoints, roles, and databases.

For core concepts (Organization, Project, Branch, Endpoint, etc.), see `what-is-neon.md`.

## Documentation

```bash
curl -H "Accept: text/markdown" https://neon.tech/docs/reference/python-sdk
```

## Installation

```bash
pip install neon-api
```

## Authentication

```python
import os
from neon_api import NeonAPI

api_key = os.getenv("NEON_API_KEY")
if not api_key:
    raise ValueError("NEON_API_KEY environment variable is not set.")

neon = NeonAPI(api_key=api_key)
```

## Projects

### List Projects

```python
all_projects = neon.projects()
```

### Create Project

```python
new_project = neon.project_create(
    project={
        'name': 'my-new-project',
        'pg_version': 17
    }
)
```

### Get Project Details

```python
project = neon.project(project_id='your-project-id')
```

### Update Project

```python
neon.project_update(
    project_id='your-project-id',
    project={
        'name': 'renamed-project',
        'default_endpoint_settings': {
            'autoscaling_limit_min_cu': 1,
            'autoscaling_limit_max_cu': 2,
        }
    }
)
```

### Delete Project

```python
neon.project_delete(project_id='project-to-delete')
```

### Get Connection URI

```python
uri = neon.connection_uri(
    project_id='your-project-id',
    database_name='neondb',
    role_name='neondb_owner'
)
print(f"Connection URI: {uri.uri}")
```

## Branches

### Create Branch

```python
new_branch = neon.branch_create(
    project_id='your-project-id',
    branch={'name': 'feature-branch'},
    endpoints=[
        {'type': 'read_write', 'autoscaling_limit_max_cu': 1}
    ]
)
```

### List Branches

```python
branches = neon.branches(project_id='your-project-id')
```

### Get Branch Details

```python
branch = neon.branch(project_id='your-project-id', branch_id='br-xxx')
```

### Update Branch

```python
neon.branch_update(
    project_id='your-project-id',
    branch_id='br-xxx',
    branch={'name': 'updated-branch-name'}
)
```

### Delete Branch

```python
neon.branch_delete(project_id='your-project-id', branch_id='br-xxx')
```

## Databases

### Create Database

```python
neon.database_create(
    project_id='your-project-id',
    branch_id='br-xxx',
    database={'name': 'my-app-db', 'owner_name': 'neondb_owner'}
)
```

### List Databases

```python
databases = neon.databases(project_id='your-project-id', branch_id='br-xxx')
```

### Delete Database

```python
neon.database_delete(
    project_id='your-project-id',
    branch_id='br-xxx',
    database_id='my-app-db'
)
```

## Roles

### Create Role

```python
new_role = neon.role_create(
    project_id='your-project-id',
    branch_id='br-xxx',
    role_name='app_user'
)
print(f"Password: {new_role.role.password}")
```

### List Roles

```python
roles = neon.roles(project_id='your-project-id', branch_id='br-xxx')
```

### Delete Role

```python
neon.role_delete(
    project_id='your-project-id',
    branch_id='br-xxx',
    role_name='app_user'
)
```

## Endpoints

### Create Endpoint

```python
neon.endpoint_create(
    project_id='your-project-id',
    endpoint={
        'branch_id': 'br-xxx',
        'type': 'read_only'
    }
)
```

### Start/Suspend Endpoint

```python
# Start
neon.endpoint_start(project_id='your-project-id', endpoint_id='ep-xxx')

# Suspend
neon.endpoint_suspend(project_id='your-project-id', endpoint_id='ep-xxx')
```

### Update Endpoint

```python
neon.endpoint_update(
    project_id='your-project-id',
    endpoint_id='ep-xxx',
    endpoint={'autoscaling_limit_max_cu': 2}
)
```

### Delete Endpoint

```python
neon.endpoint_delete(project_id='your-project-id', endpoint_id='ep-xxx')
```

## API Keys

### List API Keys

```python
api_keys = neon.api_keys()
```

### Create API Key

```python
new_key = neon.api_key_create(key_name='my-script-key')
print(f"Key (store securely!): {new_key.key}")
```

### Revoke API Key

```python
neon.api_key_revoke(1234)  # key ID
```

## Operations

### List Operations

```python
ops = neon.operations(project_id='your-project-id')
```

### Get Operation Details

```python
op = neon.operation(project_id='your-project-id', operation_id='op-xxx')
```




### Connection Methods

# Connection Methods

Guide to selecting the optimal connection method for your Neon Postgres database based on deployment platform and runtime environment.

For official documentation:

```bash
curl -H "Accept: text/markdown" https://neon.tech/docs/connect/choose-connection
```

## Decision Tree

Follow this flow to determine the right connection approach:

### 1. What Language Are You Using?

**Not TypeScript/JavaScript** → Use **TCP with connection pooling** from a secure server.

For non-TypeScript languages, connect from a secure backend server using your language's native Postgres driver with connection pooling enabled.

| Language/Framework  | Documentation                               |
| ------------------- | ------------------------------------------- |
| Django (Python)     | https://neon.tech/docs/guides/django        |
| SQLAlchemy (Python) | https://neon.tech/docs/guides/sqlalchemy    |
| Elixir Ecto         | https://neon.tech/docs/guides/elixir-ecto   |
| Laravel (PHP)       | https://neon.tech/docs/guides/laravel       |
| Ruby on Rails       | https://neon.tech/docs/guides/ruby-on-rails |
| Go                  | https://neon.tech/docs/guides/go            |
| Rust                | https://neon.tech/docs/guides/rust          |
| Java                | https://neon.tech/docs/guides/java          |

**TypeScript/JavaScript** → Continue to step 2.

---

### 2. Client-Side App Without Backend?

**Yes** → Use **Neon Data API** via `@neondatabase/neon-js`

This is the only option for client-side apps since browsers cannot make direct TCP connections to Postgres. See `neon-js.md` for setup.

```bash
curl -H "Accept: text/markdown" https://neon.tech/docs/reference/javascript-sdk
```

**No** → Continue to step 3.

---

### 3. Long-Running Server? (Railway, Render, traditional VPS)

**Yes** → Use **TCP with connection pooling** via `node-postgres`, `postgres.js`, or `bun:pg`

Long-running servers maintain persistent connections, so standard TCP drivers with pooling are optimal.

**No** → Continue to step 4.

---

### 4. Edge Environment Without TCP Support?

Some edge runtimes don't support TCP connections. Rarely the case anymore.

**Yes** → Continue to step 5 to check transaction requirements.

**No** → Continue to step 6 to check pooling support.

---

### 5. Does Your App Use SQL Transactions?

**Yes** → Use **WebSocket transport** via `@neondatabase/serverless` with `Pool`

WebSocket maintains connection state needed for transactions. See `neon-serverless.md` for setup.

**No** → Use **HTTP transport** via `@neondatabase/serverless`

HTTP is faster for single queries (~3 roundtrips vs ~8 for TCP). See `neon-serverless.md` for setup.

```bash
curl -H "Accept: text/markdown" https://neon.tech/docs/serverless/serverless-driver
```

---

### 6. Serverless Environment With Connection Pooling Support?

**Vercel (Fluid Compute)** → Use **TCP with `@vercel/functions`**

Vercel's Fluid compute supports connection pooling. Use `attachDatabasePool` for optimal connection management.

```bash
curl -H "Accept: text/markdown" https://neon.tech/docs/guides/vercel-connection-methods
```

**Cloudflare (with Hyperdrive)** → Use **TCP via Hyperdrive**

Cloudflare Hyperdrive provides connection pooling for Workers. Use `node-postgres` or any native TCP driver.

See https://neon.tech/docs/guides/cloudflare-hyperdrive for more on connecting with Cloudflare Workers and Hyperdrive.

**No pooling support (Netlify, Deno Deploy)** → Use `@neondatabase/serverless`

Fall back to the decision in step 5 based on transaction requirements.

---

## Quick Reference Table

| Platform                | TCP Support | Pooling             | Recommended Driver         |
| ----------------------- | ----------- | ------------------- | -------------------------- |
| Vercel (Fluid)          | Yes         | `@vercel/functions` | `pg` (node-postgres)       |
| Cloudflare (Hyperdrive) | Yes         | Hyperdrive          | `pg` (node-postgres)       |
| Cloudflare Workers      | No          | No                  | `@neondatabase/serverless` |
| Netlify Functions       | No          | No                  | `@neondatabase/serverless` |
| Deno Deploy             | No          | No                  | `@neondatabase/serverless` |
| Railway / Render        | Yes         | Built-in            | `pg` (node-postgres)       |
| Client-side (browser)   | No          | N/A                 | `@neondatabase/neon-js`    |

---

## ORM Support

Popular TypeScript/JavaScript ORMs all work with Neon:

| ORM     | Drivers Supported                               | Documentation                         |
| ------- | ----------------------------------------------- | ------------------------------------- |
| Drizzle | `pg`, `postgres.js`, `@neondatabase/serverless` | https://neon.tech/docs/guides/drizzle |
| Kysely  | `pg`, `postgres.js`, `@neondatabase/serverless` | https://neon.tech/docs/guides/kysely  |
| Prisma  | `pg`, `@neondatabase/serverless`                | https://neon.tech/docs/guides/prisma  |
| TypeORM | `pg`                                            | https://neon.tech/docs/guides/typeorm |

All ORMs support both TCP drivers and Neon's serverless driver depending on your platform.

For Drizzle ORM integration with Neon, see `neon-drizzle.md`.

---

## Vercel Fluid + Drizzle Example

Complete database client setup for Vercel with Drizzle ORM and connection pooling. See `neon-drizzle.md` for more examples.

```typescript
// src/lib/db/client.ts
import { attachDatabasePool } from "@vercel/functions";
import { drizzle } from "drizzle-orm/node-postgres";
import { Pool } from "pg";

import * as schema from "./schema";

const pool = new Pool({
  connectionString: process.env.DATABASE_URL,
});
attachDatabasePool(pool);

export const db = drizzle({ client: pool, schema });
```

**Why `attachDatabasePool`?**

- First request establishes the TCP connection (~8 roundtrips)
- Subsequent requests reuse the connection instantly
- Ensures idle connections close gracefully before function suspension
- Prevents connection leaks in serverless environments

---

## Gathering Requirements

When helping a user choose their connection method, gather this information:

1. **Deployment platform**: Where will the app run? (Vercel, Cloudflare, Netlify, Railway, browser, etc.)
2. **Runtime type**: Serverless functions, edge functions, or long-running server?
3. **Transaction requirements**: Does the app need SQL transactions?
4. **ORM preference**: Using Drizzle, Kysely, Prisma, or raw SQL?

Then provide:

- The recommended driver/package
- A working code example for their setup
- The correct npm install command

---

## Documentation Resources

| Topic                      | URL                                                     |
| -------------------------- | ------------------------------------------------------- |
| Choosing Connection Method | https://neon.tech/docs/connect/choose-connection        |
| Serverless Driver          | https://neon.tech/docs/serverless/serverless-driver     |
| JavaScript SDK             | https://neon.tech/docs/reference/javascript-sdk         |
| Connection Pooling         | https://neon.tech/docs/connect/connection-pooling       |
| Vercel Connection Methods  | https://neon.tech/docs/guides/vercel-connection-methods |




### What Is Neon

# What is Neon

Neon is a serverless Postgres platform designed to help you build reliable and scalable applications faster. It separates compute and storage to offer modern developer features such as autoscaling, branching, instant restore, and scale-to-zero.

For the full introduction, fetch the official docs:

```bash
curl -H "Accept: text/markdown" https://neon.tech/docs/introduction
```

## Core Concepts

Understanding Neon's resource hierarchy is essential for working with the platform effectively.

| Concept          | Description                                                           | Key Relationship          |
| ---------------- | --------------------------------------------------------------------- | ------------------------- |
| Organization     | Highest-level container for billing, users, and projects              | Contains Projects         |
| Project          | Primary container for all database resources for an application       | Contains Branches         |
| Branch           | Lightweight, copy-on-write clone of database state                    | Contains Databases, Roles |
| Compute Endpoint | Running PostgreSQL instance (CPU/RAM for queries)                     | Attached to a Branch      |
| Database         | Logical container for data (tables, schemas, views)                   | Exists within a Branch    |
| Role             | PostgreSQL role for authentication and authorization                  | Belongs to a Branch       |
| Operation        | Async action by the control plane (creating branch, starting compute) | Associated with Project   |

## Key Differentiators

1. **Serverless Architecture**: Compute scales automatically and can suspend when idle
2. **Branching**: Create instant database copies without duplicating storage
3. **Separation of Compute and Storage**: Pay for compute only when active
4. **Postgres Compatible**: Works with any Postgres driver, ORM, or tool

## Documentation Resources

| Topic                  | Documentation URL                                         |
| ---------------------- | --------------------------------------------------------- |
| Introduction           | https://neon.tech/docs/introduction                       |
| Architecture           | https://neon.tech/docs/introduction/architecture-overview |
| Plans & Billing        | https://neon.tech/docs/introduction/about-billing         |
| Regions                | https://neon.tech/docs/introduction/regions               |
| Postgres Compatibility | https://neon.tech/docs/reference/compatibility            |

```bash
# Fetch architecture docs
curl -H "Accept: text/markdown" https://neon.tech/docs/introduction/architecture-overview

# Fetch plans and billing
curl -H "Accept: text/markdown" https://neon.tech/docs/introduction/about-billing
```

## When to Use Neon

Neon is ideal for:

- **Serverless applications**: Functions that need database access without managing connections
- **Development workflows**: Branch databases like code for isolated testing
- **Variable workloads**: Auto-scale during traffic spikes, scale to zero when idle
- **Cost optimization**: Pay only for active compute time and storage used




---

## 🚀 Usage

**Reference this template:** `@skill-using-neon.md`


**Platform-specific:**
- **GitHub Copilot**: Add to `.github/copilot-instructions.md`
- **Augment Code**: Use `aug context add` command
- **Cursor/Windsurf**: Reference in chat with `@filename`
- **Claude**: Add to Project Knowledge
- **ChatGPT**: Add to Custom GPT configuration
