---
id: integration-github
type: integration
name: github
description: Direct GitHub API integration for repository management, issue tracking,
  pull requests, and collaborative development workflows.
category: integration
complexity: medium
keywords: []
capabilities: []
token_estimate: 217
package_name: '@modelcontextprotocol/server-github'
---

# github

Direct GitHub API integration for repository management, issue tracking, pull requests, and collaborative development workflows.

# github Integration

Direct GitHub API integration for repository management, issue tracking, pull requests, and collaborative development workflows.

## Installation

This integration uses an npm package.

### Option 1: Direct Usage (Recommended)
```bash
npx @modelcontextprotocol/server-github
```

### Option 2: Global Installation
```bash
npm install -g @modelcontextprotocol/server-github
```

## Usage with Different AI Assistants

### Claude Desktop
Add to your Claude configuration file:

```json
{
  "mcpServers": {
    "github": {
      "command": "npx",
      "args": ['-y', '@modelcontextprotocol/server-github']
    }
  }
}
```

### Generic Usage
This integration provides external API/service access. For AI assistants that don't support
MCP directly, you may need to:

1. Install the package manually
2. Use the API it exposes
3. Reference the functionality in your prompts

## What This Provides

Direct GitHub API integration for repository management, issue tracking, pull requests, and collaborative development workflows.

## Example Use Cases

1. Describe your workflow that needs this integration
2. Reference the capabilities in your AI assistant prompts
3. Let the AI assistant guide you through using the integration



---

## 🚀 Usage

**Reference this template:** `@integration-github.md`


**Platform-specific:**
- **GitHub Copilot**: Add to `.github/copilot-instructions.md`
- **Augment Code**: Use `aug context add` command
- **Cursor/Windsurf**: Reference in chat with `@filename`
- **Claude**: Add to Project Knowledge
- **ChatGPT**: Add to Custom GPT configuration
