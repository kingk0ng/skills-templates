---
id: integration-dynatrace-mcp-server
type: integration
name: dynatrace-mcp-server
description: Manage and interact with the Dynatrace Platform for real-time observability
  and monitoring.
category: devtools
complexity: medium
keywords: []
capabilities: []
token_estimate: 209
package_name: '@dynatrace-oss/dynatrace-mcp-server@latest'
---

# dynatrace-mcp-server

Manage and interact with the Dynatrace Platform for real-time observability and monitoring.

# dynatrace-mcp-server Integration

Manage and interact with the Dynatrace Platform for real-time observability and monitoring.

## Installation

This integration uses an npm package.

### Option 1: Direct Usage (Recommended)
```bash
npx @dynatrace-oss/dynatrace-mcp-server@latest
```

### Option 2: Global Installation
```bash
npm install -g @dynatrace-oss/dynatrace-mcp-server@latest
```

## Usage with Different AI Assistants

### Claude Desktop
Add to your Claude configuration file:

```json
{
  "mcpServers": {
    "dynatrace-mcp-server": {
      "command": "npx",
      "args": ['-y', '@dynatrace-oss/dynatrace-mcp-server@latest']
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

Manage and interact with the Dynatrace Platform for real-time observability and monitoring.

## Example Use Cases

1. Describe your workflow that needs this integration
2. Reference the capabilities in your AI assistant prompts
3. Let the AI assistant guide you through using the integration



---

## 🚀 Usage

**Reference this template:** `@integration-dynatrace-mcp-server.md`


**Platform-specific:**
- **GitHub Copilot**: Add to `.github/copilot-instructions.md`
- **Augment Code**: Use `aug context add` command
- **Cursor/Windsurf**: Reference in chat with `@filename`
- **Claude**: Add to Project Knowledge
- **ChatGPT**: Add to Custom GPT configuration
