---
id: skill-railway-projects
type: skill
name: railway-projects
description: List, switch, and configure Railway projects. Use when user wants to
  list all projects, switch projects, rename a project, enable/disable PR deploys,
  make a project public/private, or modify project settings.
category: railway
complexity: medium
keywords:
- api
- graphql
capabilities: []
token_estimate: 557
---

<!-- Converted from Claude Skill Template -->
<!-- Complexity: Medium -->
<!-- Estimated Tokens: ~557 -->


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




# railway-projects

> List, switch, and configure Railway projects. Use when user wants to list all projects, switch projects, rename a project, enable/disable PR deploys, make a project public/private, or modify project settings.

# Railway Project Management

List, switch, and configure Railway projects.

## When to Use

- User asks "show me all my projects" or "what projects do I have"
- User asks about projects across workspaces
- User asks "what workspaces do I have"
- User wants to switch to a different project
- User asks to rename a project
- User wants to enable/disable PR deploys
- User wants to make a project public or private
- User asks about project settings

## List Projects

The `railway list --json` output can be very large. Run in a subagent and return only essential fields:

- Project: `id`, `name`
- Workspace: `id`, `name`
- Services: `name` (optional, if user needs service context)

```bash
railway list --json
```

Extract and return a simplified summary, not the full JSON.

## List Workspaces

```bash
railway whoami --json
```

Returns user info including all workspaces the user belongs to.

## Switch Project

Link a different project to the current directory:

```bash
railway link -p <project-id-or-name>
```

Or interactively:

```bash
railway link
```

After switching, use railway-status skill to see project details.

## Update Project

Modify project settings via GraphQL API.

### Get Project ID

```bash
railway status --json
```

Extract `project.id` from the response.

### Update Mutation

```bash
bash <<'SCRIPT'
${CLAUDE_PLUGIN_ROOT}/skills/lib/railway-api.sh \
  'mutation updateProject($id: String!, $input: ProjectUpdateInput!) {
    projectUpdate(id: $id, input: $input) { name prDeploys isPublic botPrEnvironments }
  }' \
  '{"id": "PROJECT_ID", "input": {"name": "new-name"}}'
SCRIPT
```

### ProjectUpdateInput Fields

| Field | Type | Description |
|-------|------|-------------|
| `name` | String | Project name |
| `description` | String | Project description |
| `isPublic` | Boolean | Make project public/private |
| `prDeploys` | Boolean | Enable/disable PR deploys |
| `botPrEnvironments` | Boolean | Enable Dependabot/Renovate PR environments |

### Examples

**Rename project:**
```bash
${CLAUDE_PLUGIN_ROOT}/skills/lib/railway-api.sh '<mutation>' '{"id": "uuid", "input": {"name": "new-name"}}'
```

**Enable PR deploys:**
```bash
${CLAUDE_PLUGIN_ROOT}/skills/lib/railway-api.sh '<mutation>' '{"id": "uuid", "input": {"prDeploys": true}}'
```

**Make project public:**
```bash
${CLAUDE_PLUGIN_ROOT}/skills/lib/railway-api.sh '<mutation>' '{"id": "uuid", "input": {"isPublic": true}}'
```

**Multiple fields:**
```bash
${CLAUDE_PLUGIN_ROOT}/skills/lib/railway-api.sh '<mutation>' '{"id": "uuid", "input": {"name": "new-name", "prDeploys": true}}'
```

## Composability

- **View project details**: Use railway-status skill
- **Create new project**: Use railway-new skill
- **Manage environments**: Use railway-environment skill

## Error Handling

### Not Authenticated
```
Not authenticated. Run `railway login` first.
```

### No Projects
```
No projects found. Create one with `railway init`.
```

### Permission Denied
```
You don't have permission to modify this project. Check your Railway role.
```

### Project Not Found
```
Project "foo" not found. Run `railway list` to see available projects.
```


---

## 🚀 Usage

**Reference this template:** `@skill-railway-projects.md`


**Platform-specific:**
- **GitHub Copilot**: Add to `.github/copilot-instructions.md`
- **Augment Code**: Use `aug context add` command
- **Cursor/Windsurf**: Reference in chat with `@filename`
- **Claude**: Add to Project Knowledge
- **ChatGPT**: Add to Custom GPT configuration
