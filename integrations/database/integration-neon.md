---
id: integration-neon
type: integration
name: Neon
description: MCP server for interacting with Neon Management API and databases
category: database
complexity: medium
keywords: []
capabilities: []
token_estimate: 205
package_name: mcp-remote
---

# Neon

MCP server for interacting with Neon Management API and databases

# Neon Integration

MCP server for interacting with Neon Management API and databases

## Installation

This integration uses an npm package.

### Option 1: Direct Usage (Recommended)
```bash
npx mcp-remote
```

### Option 2: Global Installation
```bash
npm install -g mcp-remote
```

## Usage with Different AI Assistants

### Claude Desktop
Add to your Claude configuration file:

```json
{
  "mcpServers": {
    "Neon": {
      "command": "npx",
      "args": ['-y', 'mcp-remote', 'https://mcp.neon.tech/mcp']
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

MCP server for interacting with Neon Management API and databases

## Example Use Cases

1. Describe your workflow that needs this integration
2. Reference the capabilities in your AI assistant prompts
3. Let the AI assistant guide you through using the integration



---

## 🚀 Usage

**Reference this template:** `@integration-neon.md`


**Platform-specific:**
- **GitHub Copilot**: Add to `.github/copilot-instructions.md`
- **Augment Code**: Use `aug context add` command
- **Cursor/Windsurf**: Reference in chat with `@filename`
- **Claude**: Add to Project Knowledge
- **ChatGPT**: Add to Custom GPT configuration
