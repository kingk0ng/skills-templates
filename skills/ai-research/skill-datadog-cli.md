---
id: skill-datadog-cli
type: skill
name: datadog-cli
description: Datadog CLI for searching logs, querying metrics, tracing requests, and
  managing dashboards. Use this when debugging production issues or working with Datadog
  observability.
category: ai-research
complexity: medium
keywords:
- api
capabilities: []
token_estimate: 577
has_references: true
reference_count: 5
---

<!-- Converted from Claude Skill Template -->
<!-- Complexity: Medium -->
<!-- Estimated Tokens: ~577 -->
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




# datadog-cli

> Datadog CLI for searching logs, querying metrics, tracing requests, and managing dashboards. Use this when debugging production issues or working with Datadog observability.

# Datadog CLI

A CLI tool for AI agents to debug and triage using Datadog logs and metrics.

## Required Reading

**You MUST read the relevant reference docs before using any command:**
- [Log Commands](references/logs-commands.md)
- [Metrics](references/metrics.md)
- [Query Syntax](references/query-syntax.md)
- [Workflows](references/workflows.md)
- [Dashboards](references/dashboards.md)

## Setup

### Environment Variables (Required)

```bash
export DD_API_KEY="your-api-key"
export DD_APP_KEY="your-app-key"
```

Get keys from: https://app.datadoghq.com/organization-settings/api-keys

### Running the CLI

```bash
npx @leoflores/datadog-cli <command>
```

For non-US Datadog sites, use `--site` flag:
```bash
npx @leoflores/datadog-cli logs search --query "*" --site datadoghq.eu
```

## Commands Overview

| Command | Description |
|---------|-------------|
| `logs search` | Search logs with filters |
| `logs tail` | Stream logs in real-time |
| `logs trace` | Find logs for a distributed trace |
| `logs context` | Get logs before/after a timestamp |
| `logs patterns` | Group similar log messages |
| `logs compare` | Compare log counts between periods |
| `logs multi` | Run multiple queries in parallel |
| `logs agg` | Aggregate logs by facet |
| `metrics query` | Query timeseries metrics |
| `errors` | Quick error summary by service/type |
| `services` | List services with log activity |
| `dashboards` | Manage dashboards (CRUD) |
| `dashboard-lists` | Manage dashboard lists |


## Quick Examples

### Search Errors
```bash
npx @leoflores/datadog-cli logs search --query "status:error" --from 1h --pretty
```

### Tail Logs (Real-time)
```bash
npx @leoflores/datadog-cli logs tail --query "service:api status:error" --pretty
```

### Error Summary
```bash
npx @leoflores/datadog-cli errors --from 1h --pretty
```

### Trace Correlation
```bash
npx @leoflores/datadog-cli logs trace --id "abc123def456" --pretty
```

### Query Metrics
```bash
npx @leoflores/datadog-cli metrics query --query "avg:system.cpu.user{*}" --from 1h --pretty
```

### Compare Periods
```bash
npx @leoflores/datadog-cli logs compare --query "status:error" --period 1h --pretty
```

## Global Flags

| Flag | Description |
|------|-------------|
| `--pretty` | Human-readable output with colors |
| `--output <file>` | Export results to JSON file |
| `--site <site>` | Datadog site (e.g., `datadoghq.eu`) |

## Time Formats

- **Relative**: `30m`, `1h`, `6h`, `24h`, `7d`
- **ISO 8601**: `2024-01-15T10:30:00Z`

## Incident Triage Workflow

```bash
# 1. Quick error overview
npx @leoflores/datadog-cli errors --from 1h --pretty

# 2. Is this new? Compare to previous period
npx @leoflores/datadog-cli logs compare --query "status:error" --period 1h --pretty

# 3. Find error patterns
npx @leoflores/datadog-cli logs patterns --query "status:error" --from 1h --pretty

# 4. Narrow down by service
npx @leoflores/datadog-cli logs search --query "status:error service:api" --from 1h --pretty

# 5. Get context around a timestamp
npx @leoflores/datadog-cli logs context --timestamp "2024-01-15T10:30:00Z" --service api --pretty

# 6. Follow the distributed trace
npx @leoflores/datadog-cli logs trace --id "TRACE_ID" --pretty
```

See [workflows.md](references/workflows.md) for more debugging workflows.


---


## 📚 Reference Materials


### Workflows

# Common Workflows

## Incident Triage

Step-by-step workflow for investigating production issues.

```bash
# 1. Quick error overview
npx @leoflores/datadog-cli errors --from 1h --pretty

# 2. Is this new? Compare to previous period
npx @leoflores/datadog-cli logs compare --query "status:error" --period 1h --pretty

# 3. Find error patterns
npx @leoflores/datadog-cli logs patterns --query "status:error" --from 1h --pretty

# 4. Narrow down by service
npx @leoflores/datadog-cli logs search --query "status:error service:payment-api" --from 1h --pretty

# 5. Get context around a specific timestamp
npx @leoflores/datadog-cli logs context --timestamp "2024-01-15T10:30:00Z" --service api --before 5m --after 2m --pretty

# 6. Follow the distributed trace
npx @leoflores/datadog-cli logs trace --id "TRACE_ID" --pretty
```

## Real-time Debugging

Monitor logs as they arrive.

```bash
# Stream all errors
npx @leoflores/datadog-cli logs tail --query "status:error" --pretty

# Watch specific service
npx @leoflores/datadog-cli logs tail --query "service:api status:error" --pretty

# Monitor deployments
npx @leoflores/datadog-cli logs tail --query "service:deploy" --pretty
```

## Service Health Check

Assess overall service health.

```bash
# List all services
npx @leoflores/datadog-cli services --from 24h --pretty

# Check error distribution for a service
npx @leoflores/datadog-cli logs agg --query "service:api" --facet status --from 1h --pretty

# Check CPU/memory usage
npx @leoflores/datadog-cli metrics query --query "avg:system.cpu.user{service:api}" --from 1h --pretty
npx @leoflores/datadog-cli metrics query --query "avg:system.mem.used{service:api}" --from 1h --pretty

# Error summary for service
npx @leoflores/datadog-cli errors --service api --from 24h --pretty
```

## Export for Sharing

Save results to files for reports or sharing.

```bash
# Save search results
npx @leoflores/datadog-cli logs search --query "status:error" --from 1h --output errors.json --pretty

# Save error summary
npx @leoflores/datadog-cli errors --from 24h --output error-report.json --pretty

# Save metrics data
npx @leoflores/datadog-cli metrics query --query "avg:system.cpu.user{*}" --from 24h --output cpu-metrics.json --pretty
```

## Multi-Query Investigation

Run parallel queries for comprehensive view.

```bash
# Compare error types across services
npx @leoflores/datadog-cli logs multi \
  --queries "api-errors:service:api status:error,payment-errors:service:payment status:error,auth-errors:service:auth status:error" \
  --from 1h --pretty
```

## ⚠️ Safe Dashboard Update

**CRITICAL:** Dashboard updates are destructive and replace the entire dashboard.

Follow the **Safe Dashboard Update Workflow** in [dashboards.md](dashboards.md) to avoid data loss.




### Dashboards

# Dashboards Reference

## ⚠️ CRITICAL: Dashboard Update is DESTRUCTIVE

**The `dashboards update` command REPLACES the entire dashboard, not just the fields you specify.**

If you omit any of these fields during an update, they will be **permanently deleted**:
- `--template-variables` → Template variables will be removed
- `--description` → Description will be cleared
- `--notify-list` → Notify list will be cleared

**Example of DATA LOSS:**
```bash
# This will DELETE template variables and description!
npx @leoflores/datadog-cli dashboards update \
  --id "abc-def-ghi" \
  --title "My Dashboard" \
  --layout ordered \
  --widgets '[...]'  # Only widgets provided, other fields wiped!
```

## Safe Dashboard Update Workflow

**ALWAYS follow this 3-step process when updating dashboards:**

> ⚠️ **Important:** Always use `--output` to save to a temp file instead of capturing output in a bash variable. JSON with special characters, newlines, or ANSI codes can break `jq` parsing when piped through `echo`.

### Step 1: Backup the Current Dashboard to a Temp File
```bash
# Save the current dashboard state BEFORE any changes
# Using --output ensures clean JSON without encoding issues
npx @leoflores/datadog-cli dashboards get --id "abc-def-ghi" --output /tmp/dashboard.json
```

### Step 2: Modify and Preserve All Fields
```bash
# Extract existing values directly from the file (not through echo!)
TEMPLATE_VARS=$(jq -c '.dashboard.templateVariables // []' /tmp/dashboard.json)
DESCRIPTION=$(jq -r '.dashboard.description // ""' /tmp/dashboard.json)

# Modify widgets (example: change title of widget at index 1)
WIDGETS=$(jq -c '.dashboard.widgets | .[1].definition.title = "New Title"' /tmp/dashboard.json)

# Update with ALL fields preserved
npx @leoflores/datadog-cli dashboards update \
  --id "abc-def-ghi" \
  --title "My Dashboard" \
  --layout ordered \
  --widgets "$WIDGETS" \
  --description "$DESCRIPTION" \
  --template-variables "$TEMPLATE_VARS" \
  --pretty
```

### Step 3: Verify the Update
```bash
# Confirm all fields are intact
npx @leoflores/datadog-cli dashboards get --id "abc-def-ghi" --pretty
```

### Recovery from Accidental Data Loss
```bash
# If you have a backup file, restore from it
WIDGETS=$(jq -c '.dashboard.widgets' /tmp/dashboard.json)
TEMPLATE_VARS=$(jq -c '.dashboard.templateVariables // []' /tmp/dashboard.json)
DESCRIPTION=$(jq -r '.dashboard.description // ""' /tmp/dashboard.json)
TITLE=$(jq -r '.dashboard.title' /tmp/dashboard.json)
LAYOUT=$(jq -r '.dashboard.layoutType' /tmp/dashboard.json)

npx @leoflores/datadog-cli dashboards update \
  --id "abc-def-ghi" \
  --title "$TITLE" \
  --layout "$LAYOUT" \
  --widgets "$WIDGETS" \
  --description "$DESCRIPTION" \
  --template-variables "$TEMPLATE_VARS" \
  --pretty
```

### Why Use Files Instead of Variables?

❌ **Don't do this** - prone to parsing errors:
```bash
DASHBOARD_JSON=$(npx @leoflores/datadog-cli dashboards get --id "abc-def-ghi")
WIDGETS=$(echo "$DASHBOARD_JSON" | jq -c '.dashboard.widgets')  # May fail!
```

✅ **Do this** - reliable with any JSON content:
```bash
npx @leoflores/datadog-cli dashboards get --id "abc-def-ghi" --output /tmp/dashboard.json
WIDGETS=$(jq -c '.dashboard.widgets' /tmp/dashboard.json)  # Always works
```

## Commands Overview

| Command | Description |
|---------|-------------|
| `dashboards list` | List all dashboards |
| `dashboards get` | Get full dashboard definition |
| `dashboards create` | Create a new dashboard |
| `dashboards update` | ⚠️ **DESTRUCTIVE** - Replaces entire dashboard |
| `dashboards delete` | Delete a dashboard |
| `dashboard-lists list` | List all dashboard lists |
| `dashboard-lists get` | Get dashboard list details |
| `dashboard-lists create` | Create a new list |
| `dashboard-lists update` | Update list name |
| `dashboard-lists delete` | Delete a list |
| `dashboard-lists items` | List dashboards in a list |
| `dashboard-lists add-items` | Add dashboards to list |
| `dashboard-lists delete-items` | Remove dashboards from list |

## Dashboard Flags

| Flag | Commands | Description |
|------|----------|-------------|
| `--id` | get, update, delete | Dashboard ID |
| `--title` | create, update | Dashboard title |
| `--layout` | create, update | `ordered` or `free` |
| `--widgets` | create, update | Widgets JSON (or stdin) |
| `--description` | create, update | ⚠️ **Required on update to preserve** |
| `--template-variables` | create, update | ⚠️ **Required on update to preserve** |
| `--notify-list` | create, update | ⚠️ **Required on update to preserve** |
| `--read-only` | create, update | Make read-only |

## Examples

### List & Get
```bash
npx @leoflores/datadog-cli dashboards list --pretty
npx @leoflores/datadog-cli dashboards get --id "abc-def-ghi" --pretty
```

### Create Dashboard
```bash
npx @leoflores/datadog-cli dashboards create \
  --title "API Monitoring" \
  --layout ordered \
  --widgets '[{"definition":{"type":"timeseries","requests":[{"q":"avg:system.cpu.user{*}"}]}}]' \
  --pretty
```

### With Template Variables
```bash
npx @leoflores/datadog-cli dashboards create \
  --title "Service Dashboard" \
  --layout ordered \
  --template-variables '[{"name":"env","prefix":"env","default":"prod"},{"name":"service","prefix":"service","default":"*"}]' \
  --widgets '[{"definition":{"type":"timeseries","requests":[{"q":"avg:system.cpu.user{$env,$service}"}]}}]' \
  --pretty
```

### Using Stdin
```bash
cat widgets.json | npx @leoflores/datadog-cli dashboards create --title "My Dashboard" --layout ordered --pretty
```

## Template Variables

```json
{"name": "env", "prefix": "env", "default": "prod"}
```

Use in queries: `avg:system.cpu.user{$env,$service}`

## Widget Types

### Timeseries
```json
{"definition": {"type": "timeseries", "title": "CPU", "requests": [{"q": "avg:system.cpu.user{*}"}]}}
```

### Query Value
```json
{"definition": {"type": "query_value", "title": "Errors", "requests": [{"q": "sum:errors{*}.as_count()"}]}}
```

### Top List
```json
{"definition": {"type": "toplist", "title": "Top Services", "requests": [{"q": "top(sum:errors{*} by {service}, 10, 'sum', 'desc')"}]}}
```

## Layout Types

- **ordered**: Auto-arranged responsive grid
- **free**: Manual positioning with x, y, width, height

## Dashboard Lists

```bash
# List all
npx @leoflores/datadog-cli dashboard-lists list --pretty

# Add dashboards to list
npx @leoflores/datadog-cli dashboard-lists add-items --id 123 \
  --dashboards '[{"type":"custom_timeboard","id":"abc-def-ghi"}]' --pretty
```




### Query Syntax

# Datadog Query Syntax

## Operators

| Operator | Example | Description |
|----------|---------|-------------|
| `AND` | `service:api status:error` | Both conditions (implicit) |
| `OR` | `status:error OR status:warn` | Either condition |
| `-` | `-status:info` | Exclude |
| `*` | `service:api-*` | Wildcard |
| `>=` `<=` | `@http.status_code:>=400` | Numeric comparison |
| `[TO]` | `@duration:[1000 TO 5000]` | Range |

## Common Attributes

| Attribute | Description |
|-----------|-------------|
| `service` | Service name |
| `status` | Log level (error, warn, info, debug) |
| `host` | Hostname |
| `@http.status_code` | HTTP status code |
| `@http.method` | HTTP method |
| `@http.url` | Request URL |
| `@error.kind` | Error type |
| `@error.message` | Error message |
| `@trace_id` | Trace ID |
| `@dd.trace_id` | Datadog trace ID |

## Time Formats

### Relative
- `1m` - 1 minute
- `30m` - 30 minutes
- `1h` - 1 hour
- `6h` - 6 hours
- `24h` - 24 hours
- `7d` - 7 days

### Absolute
- ISO 8601: `2024-01-15T10:30:00Z`

## Example Queries

```bash
# All errors
status:error

# Errors in specific service
service:api status:error

# 5xx HTTP errors
@http.status_code:>=500

# Exclude info logs
-status:info

# Multiple services
service:api OR service:payment

# Timeout errors
error:timeout OR @error.kind:TimeoutError

# Slow requests (>1s)
@duration:>=1000
```




### Metrics

# Metrics Reference

## metrics query

Query timeseries metrics from Datadog.

```bash
npx @leoflores/datadog-cli metrics query --query "<metrics-query>" [--from <time>] [--to <time>]
```

| Flag | Default | Description |
|------|---------|-------------|
| `--query` | required | Metrics query |
| `--from` | `15m` | Start time |
| `--to` | `now` | End time |

## Query Format

```
<aggregation>:<metric>{<tags>}
```

**Aggregations:** `avg`, `sum`, `min`, `max`, `count`

## Examples

### System Metrics
```bash
npx @leoflores/datadog-cli metrics query --query "avg:system.cpu.user{*}" --from 1h --pretty
npx @leoflores/datadog-cli metrics query --query "avg:system.mem.used{*}" --from 1h --pretty
```

### Service-Specific
```bash
npx @leoflores/datadog-cli metrics query --query "avg:system.cpu.user{service:api}" --from 1h --pretty
```

### APM Metrics
```bash
npx @leoflores/datadog-cli metrics query --query "sum:trace.http.request.errors{service:api}.as_count()" --from 1h --pretty
npx @leoflores/datadog-cli metrics query --query "p99:trace.http.request.duration{service:api}" --from 1h --pretty
```

### With Tags
```bash
npx @leoflores/datadog-cli metrics query --query "avg:system.cpu.user{env:prod,service:api}" --from 1h --pretty
```

## Output

Returns series with:
- Metric name and scope
- Point list (timestamp/value pairs)
- Tags
- Latest value + min/max/avg stats




### Logs Commands

# Log Commands Reference

## logs search

Search logs with filters.

```bash
npx @leoflores/datadog-cli logs search --query "<query>" [--from <time>] [--to <time>] [--limit <n>] [--sort <order>]
```

| Flag | Default | Description |
|------|---------|-------------|
| `--query` | `*` | Datadog search query |
| `--from` | `15m` | Start time |
| `--to` | `now` | End time |
| `--limit` | `100` | Max results (max: 1000) |
| `--sort` | `-timestamp` | Sort order |

```bash
npx @leoflores/datadog-cli logs search --query "service:api status:error" --from 1h --pretty
```

## logs tail

Stream logs in real-time. Press Ctrl+C to stop.

```bash
npx @leoflores/datadog-cli logs tail --query "<query>" [--interval <seconds>]
```

| Flag | Default | Description |
|------|---------|-------------|
| `--interval` | `2` | Polling interval in seconds |

```bash
npx @leoflores/datadog-cli logs tail --query "status:error" --pretty
```

## logs trace

Find all logs for a distributed trace across services.

```bash
npx @leoflores/datadog-cli logs trace --id "<trace-id>" [--from <time>] [--to <time>]
```

Searches both `@trace_id` and `@dd.trace_id` attributes.

```bash
npx @leoflores/datadog-cli logs trace --id "abc123def456" --from 24h --pretty
```

## logs context

Get logs before and after a specific timestamp.

```bash
npx @leoflores/datadog-cli logs context --timestamp "<iso-timestamp>" [--before <time>] [--after <time>] [--service <svc>]
```

| Flag | Default | Description |
|------|---------|-------------|
| `--before` | `5m` | Time window before |
| `--after` | `5m` | Time window after |
| `--service` | - | Filter by service |

```bash
npx @leoflores/datadog-cli logs context --timestamp "2024-01-15T10:30:00Z" --service api --before 5m --after 2m --pretty
```

## logs patterns

Group similar log messages to find patterns. Replaces UUIDs, numbers, IPs, etc.

```bash
npx @leoflores/datadog-cli logs patterns --query "<query>" [--from <time>] [--limit <n>]
```

Returns top 50 patterns with counts and sample messages.

```bash
npx @leoflores/datadog-cli logs patterns --query "status:error" --from 1h --pretty
```

## logs compare

Compare log counts between current period and previous period.

```bash
npx @leoflores/datadog-cli logs compare --query "<query>" --period <time>
```

Shows absolute and percentage change with directional arrows.

```bash
npx @leoflores/datadog-cli logs compare --query "status:error" --period 1h --pretty
```

## logs multi

Run multiple queries in parallel.

```bash
npx @leoflores/datadog-cli logs multi --queries "name1:query1,name2:query2" [--from <time>]
```

```bash
npx @leoflores/datadog-cli logs multi --queries "errors:status:error,warnings:status:warn" --from 1h --pretty
```

## logs agg

Aggregate logs by facet.

```bash
npx @leoflores/datadog-cli logs agg --query "<query>" --facet <facet> [--from <time>]
```

**Common facets:** `status`, `service`, `host`, `@http.status_code`, `@error.kind`

```bash
npx @leoflores/datadog-cli logs agg --query "*" --facet status --from 1h --pretty
```




---

## 🚀 Usage

**Reference this template:** `@skill-datadog-cli.md`


**Platform-specific:**
- **GitHub Copilot**: Add to `.github/copilot-instructions.md`
- **Augment Code**: Use `aug context add` command
- **Cursor/Windsurf**: Reference in chat with `@filename`
- **Claude**: Add to Project Knowledge
- **ChatGPT**: Add to Custom GPT configuration
