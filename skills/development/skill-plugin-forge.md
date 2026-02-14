---
id: skill-plugin-forge
type: skill
name: plugin-forge
description: Create and manage Claude Code plugins with proper structure, manifests,
  and marketplace integration. Use when creating plugins for a marketplace, adding
  plugin components (commands, agents, hooks), bumping plugin versions, or working
  with plugin.json/marketplace.json manifests.
category: development
complexity: medium
keywords:
- git
- python
- react
- testing
- vue
capabilities: []
token_estimate: 724
has_references: true
reference_count: 3
---

<!-- Converted from Claude Skill Template -->
<!-- Complexity: Medium -->
<!-- Estimated Tokens: ~724 -->
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




# plugin-forge

> Create and manage Claude Code plugins with proper structure, manifests, and marketplace integration. Use when creating plugins for a marketplace, adding plugin components (commands, agents, hooks), bumping plugin versions, or working with plugin.json/marketplace.json manifests.

# CC Plugin Forge

## Purpose

Build and manage Claude Code plugins with correct structure, manifests, and marketplace integration. Includes workflows, automation scripts, and reference docs.

## When to Use

- Creating new plugins for a marketplace
- Adding/modifying plugin components (commands, skills, agents, hooks)
- Updating plugin versions
- Working with plugin or marketplace manifests
- Setting up local plugin testing
- Publishing plugins

## Getting Started

### Create New Plugin

Use `create_plugin.py` to generate plugin structure:

```bash
python scripts/create_plugin.py plugin-name \
  --marketplace-root /path/to/marketplace \
  --author-name "Your Name" \
  --author-email "your.email@example.com" \
  --description "Plugin description" \
  --keywords "keyword1,keyword2" \
  --category "productivity"
```

This automatically:

- Creates plugin directory structure
- Generates `plugin.json` manifest
- Creates README template
- Updates `marketplace.json`

### Bump Version

Use `bump_version.py` to update versions in both manifests:

```bash
python scripts/bump_version.py plugin-name major|minor|patch \
  --marketplace-root /path/to/marketplace
```

Semantic versioning:

- **major**: Breaking changes (1.0.0 → 2.0.0)
- **minor**: New features, refactoring (1.0.0 → 1.1.0)
- **patch**: Bug fixes, docs (1.0.0 → 1.0.1)

## Development Workflow

### 1. Create Structure

Manual approach (if not using script):

```bash
mkdir -p plugins/plugin-name/.claude-plugin
mkdir -p plugins/plugin-name/commands
mkdir -p plugins/plugin-name/skills
```

### 2. Plugin Manifest

File: `plugins/plugin-name/.claude-plugin/plugin.json`

```json
{
  "name": "plugin-name",
  "version": "0.1.0",
  "description": "Plugin description",
  "author": {
    "name": "Your Name",
    "email": "your.email@example.com"
  },
  "keywords": ["keyword1", "keyword2"]
}
```

### 3. Register in Marketplace

Update `.claude-plugin/marketplace.json`:

```json
{
  "name": "plugin-name",
  "source": "./plugins/plugin-name",
  "description": "Plugin description",
  "version": "0.1.0",
  "keywords": ["keyword1", "keyword2"],
  "category": "productivity"
}
```

### 4. Add Components

Create in respective directories:

| Component | Location | Format |
|-----------|----------|--------|
| Commands | `commands/` | Markdown with frontmatter |
| Skills | `skills/<name>/` | Directory with `SKILL.md` |
| Agents | `agents/` | Markdown definitions |
| Hooks | `hooks/hooks.json` | Event handlers |
| MCP Servers | `.mcp.json` | External integrations |

### 5. Local Testing

```bash
# Add marketplace
/plugin marketplace add /path/to/marketplace-root

# Install plugin
/plugin install plugin-name@marketplace-name

# After changes: reinstall
/plugin uninstall plugin-name@marketplace-name
/plugin install plugin-name@marketplace-name
```

## Plugin Patterns

### Framework Plugin

For framework-specific guidance (React, Vue, etc.):

```
plugins/framework-name/
├── .claude-plugin/plugin.json
├── skills/
│   └── framework-name/
│       ├── SKILL.md
│       └── references/
├── commands/
│   └── prime/
│       ├── components.md
│       └── framework.md
└── README.md
```

### Utility Plugin

For tools and commands:

```
plugins/utility-name/
├── .claude-plugin/plugin.json
├── commands/
│   ├── action1.md
│   └── action2.md
└── README.md
```

### Domain Plugin

For domain-specific knowledge:

```
plugins/domain-name/
├── .claude-plugin/plugin.json
├── skills/
│   └── domain-name/
│       ├── SKILL.md
│       ├── references/
│       └── scripts/
└── README.md
```

## Command Naming

Subdirectory-based namespacing with `:` separator:

- `commands/namespace/command.md` → `/namespace:command`
- `commands/simple.md` → `/simple`

Examples:

- `commands/prime/vue.md` → `/prime:vue`
- `commands/docs/generate.md` → `/docs:generate`

## Version Management

**Important:** Update version in BOTH locations:

1. `plugins/<name>/.claude-plugin/plugin.json`
2. `.claude-plugin/marketplace.json`

Use `bump_version.py` to automate.

## Git Commits

Use conventional commits:

```bash
git commit -m "feat: add new plugin"
git commit -m "fix: correct plugin manifest"
git commit -m "docs: update plugin README"
git commit -m "feat!: breaking change"
```

## Reference Docs

Detailed documentation included:

| Reference | Content |
|-----------|---------|
| `references/plugin-structure.md` | Directory structure, manifest schema, components |
| `references/marketplace-schema.md` | Marketplace format, plugin entries, distribution |
| `references/workflows.md` | Step-by-step workflows, patterns, publishing |

### Scripts

| Script | Purpose |
|--------|---------|
| `scripts/create_plugin.py` | Scaffold new plugin |
| `scripts/bump_version.py` | Update versions |


---


## 📚 Reference Materials


### Workflows

# Plugin Development Workflows

## Creating a New Plugin

### 1. Create Plugin Directory Structure

```bash
mkdir -p plugins/plugin-name/.claude-plugin
mkdir -p plugins/plugin-name/commands
mkdir -p plugins/plugin-name/skills
```

### 2. Create Plugin Manifest

Create `plugins/plugin-name/.claude-plugin/plugin.json`:

```json
{
  "name": "plugin-name",
  "version": "0.1.0",
  "description": "Plugin description",
  "author": {
    "name": "Your Name",
    "email": "your.email@example.com"
  },
  "keywords": ["keyword1", "keyword2"]
}
```

### 3. Add Plugin to Marketplace

Update `.claude-plugin/marketplace.json` by adding entry to `plugins` array:

```json
{
  "name": "plugin-name",
  "source": "./plugins/plugin-name",
  "description": "Plugin description",
  "version": "0.1.0",
  "keywords": ["keyword1", "keyword2"],
  "category": "productivity"
}
```

### 4. Add Plugin Components

Create commands, skills, agents, or hooks as needed in their respective directories.

## Version Bumping

When making changes to a plugin, update version in **both** locations:

1. `plugins/<plugin-name>/.claude-plugin/plugin.json`
2. `.claude-plugin/marketplace.json` (matching plugin entry)

**Semantic versioning:**

- **Major (x.0.0)**: Breaking changes
- **Minor (0.x.0)**: New features, refactoring
- **Patch (0.0.x)**: Bug fixes, documentation only

## Local Testing Workflow

### Initial Setup

```bash
# Add marketplace
/plugin marketplace add /path/to/marketplace-root

# Install plugin
/plugin install plugin-name@marketplace-name
```

### Iterative Testing

After making changes to a plugin:

```bash
# Uninstall
/plugin uninstall plugin-name@marketplace-name

# Reinstall
/plugin install plugin-name@marketplace-name

# Restart Claude Code to load changes
```

**Note:** Claude Code caches plugin files, so restart may be required for changes to take effect.

## Publishing Workflow

### 1. Commit Changes

Use conventional commits:

```bash
git add .
git commit -m "feat: add new plugin"
git commit -m "fix: correct plugin manifest"
git commit -m "docs: update plugin README"
```

### 2. Push to Repository

```bash
git push origin main
```

### 3. Distribution

**GitHub-hosted marketplace:**

Users add via:

```bash
/plugin marketplace add owner/repo
/plugin install plugin-name@marketplace-name
```

**Local marketplace:**

Users add via absolute path:

```bash
/plugin marketplace add /path/to/marketplace
```

## Command Naming Convention

Commands use subdirectory-based namespacing:

- File: `commands/namespace/command.md`
- Invoked as: `/namespace:command`
- The `:` represents directory separator `/`

**Examples:**

- `commands/prime/vue.md` → `/prime:vue`
- `commands/docs/generate.md` → `/docs:generate`
- `commands/simple.md` → `/simple`

## Common Plugin Patterns

### Framework Plugin

Structure for framework-specific guidance (React, Vue, Nuxt, etc.):

```
plugins/framework-name/
├── .claude-plugin/plugin.json
├── skills/
│   └── framework-name/
│       ├── SKILL.md              # Quick reference
│       └── references/           # Library-specific patterns
├── commands/
│   └── prime/                    # Namespace for loading patterns
│       ├── components.md
│       └── framework.md
└── README.md
```

### Utility Plugin

Structure for tools and utilities:

```
plugins/utility-name/
├── .claude-plugin/plugin.json
├── commands/
│   ├── action1.md
│   └── action2.md
└── README.md
```

### Domain Plugin

Structure for domain-specific knowledge:

```
plugins/domain-name/
├── .claude-plugin/plugin.json
├── skills/
│   └── domain-name/
│       ├── SKILL.md
│       ├── references/
│       │   ├── schema.md
│       │   └── policies.md
│       └── scripts/
│           └── automation.py
└── README.md
```




### Marketplace Schema

# Marketplace Schema Reference

## Marketplace Structure

A marketplace is a JSON catalog enabling plugin discovery and distribution.

**File location:** `.claude-plugin/marketplace.json`

## Required Fields

```json
{
  "name": "marketplace-identifier",
  "owner": {
    "name": "Maintainer Name",
    "email": "maintainer@example.com"
  },
  "plugins": []
}
```

**name**: Kebab-case marketplace identifier
**owner**: Maintainer contact information
**plugins**: Array of plugin entries

## Optional Marketplace Fields

**description**: Marketplace overview text
**version**: Release version
**pluginRoot**: Base path for relative plugin sources

## Plugin Entry Schema

Each plugin entry in the `plugins` array:

**Required:**

- `name`: Plugin identifier (kebab-case, must match plugin.json)
- `source`: Plugin origin specification

**Optional:**

- `description`: Plugin purpose
- `version`: Plugin version (semantic versioning)
- `author`: Creator information
- `homepage`: URL
- `repository`: URL
- `license`: SPDX identifier
- `keywords`: Array of search terms
- `category`: Classification (e.g., "framework", "productivity")
- `tags`: Additional discovery tags
- `commands`: Path to commands directory
- `agents`: Path to agents directory
- `hooks`: Path to hooks configuration
- `mcpServers`: Path to MCP configuration

## Source Specifications

### Relative Path Source

```json
{
  "name": "my-plugin",
  "source": "./plugins/my-plugin"
}
```

### GitHub Source

```json
{
  "name": "my-plugin",
  "source": {
    "source": "github",
    "repo": "owner/repo"
  }
}
```

### Generic Git Source

```json
{
  "name": "my-plugin",
  "source": {
    "source": "url",
    "url": "https://git.example.com/plugin.git"
  }
}
```

## Complete Example

```json
{
  "name": "example-marketplace",
  "description": "Example plugin marketplace",
  "version": "1.0.0",
  "owner": {
    "name": "Marketplace Owner",
    "email": "owner@example.com"
  },
  "pluginRoot": "./plugins",
  "plugins": [
    {
      "name": "example-plugin",
      "source": "./example-plugin",
      "description": "Example plugin",
      "version": "1.0.0",
      "keywords": ["example"],
      "category": "productivity"
    }
  ]
}
```

## Team Distribution

Configure automatic marketplace availability via `.claude/settings.json`:

```json
{
  "extraKnownMarketplaces": [
    {
      "source": {
        "source": "github",
        "repo": "company/marketplace"
      }
    }
  ]
}
```




### Plugin Structure

# Plugin Structure Reference

## Directory Hierarchy

```
plugin-name/
├── .claude-plugin/
│   └── plugin.json          # Required: Plugin metadata manifest
├── commands/                 # Optional: Custom slash commands
├── agents/                   # Optional: Agent definitions
├── skills/                   # Optional: Agent Skills
│   └── skill-name/
│       ├── SKILL.md         # Required for each skill
│       ├── scripts/         # Optional: Executable code
│       ├── references/      # Optional: Documentation
│       └── assets/          # Optional: Output files
├── hooks/                    # Optional: Event handlers
│   └── hooks.json
└── .mcp.json                # Optional: MCP server integrations
```

## Plugin Manifest (`plugin.json`)

**Location:** `.claude-plugin/plugin.json` (must be in this directory)

**Required fields:**

- `name`: Unique identifier (kebab-case)

**Standard metadata:**

- `version`: Semantic versioning (e.g., "1.0.0")
- `description`: Plugin purpose
- `author`: Object with name, email, url
- `homepage`: URL
- `repository`: URL
- `license`: SPDX identifier
- `keywords`: Array of search terms

**Component paths (optional):**

- `commands`: Path to commands directory
- `agents`: Path to agents directory
- `hooks`: Path to hooks configuration
- `mcpServers`: Path to MCP configuration

### Example plugin.json

```json
{
  "name": "example-plugin",
  "version": "1.0.0",
  "description": "Example plugin for demonstration",
  "author": {
    "name": "Plugin Creator",
    "email": "creator@example.com"
  },
  "keywords": ["example", "demo"]
}
```

## Component Types

### Commands

**Location:** `commands/` directory
**Format:** Markdown files with frontmatter
**Naming:** Subdirectories create namespaces via `:`

```
commands/
├── simple.md              # Invoked as /simple
└── namespace/
    └── command.md         # Invoked as /namespace:command
```

### Agents

**Location:** `agents/` directory
**Format:** Markdown files describing agent capabilities

### Skills

**Location:** `skills/` directory
**Format:** Subdirectories with `SKILL.md` file
**Structure:** See skills documentation

### Hooks

**Location:** `hooks/hooks.json` or path specified in manifest
**Events:** PreToolUse, PostToolUse, UserPromptSubmit, Notification, Stop, SubagentStop, SessionStart, SessionEnd, PreCompact

### MCP Servers

**Location:** `.mcp.json` at plugin root
**Purpose:** External tool integrations

## Path Requirements

- All paths relative to plugin root
- Start with `./` for custom paths
- Use `${CLAUDE_PLUGIN_ROOT}` for dynamic resolution in hooks/MCP
- Components must be at plugin root, not inside `.claude-plugin/`




---

## 🚀 Usage

**Reference this template:** `@skill-plugin-forge.md`


**Platform-specific:**
- **GitHub Copilot**: Add to `.github/copilot-instructions.md`
- **Augment Code**: Use `aug context add` command
- **Cursor/Windsurf**: Reference in chat with `@filename`
- **Claude**: Add to Project Knowledge
- **ChatGPT**: Add to Custom GPT configuration
