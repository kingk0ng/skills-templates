---
id: integration-microsoftclarity-mcp-server
type: integration
name: '@microsoft/clarity-mcp-server'
description: ''
category: devtools
complexity: medium
keywords: []
capabilities: []
token_estimate: 178
package_name: '@microsoft/clarity-mcp-server'
---

# @microsoft/clarity-mcp-server

# @microsoft/clarity-mcp-server Integration



## Installation

This integration uses an npm package.

### Option 1: Direct Usage (Recommended)
```bash
npx @microsoft/clarity-mcp-server
```

### Option 2: Global Installation
```bash
npm install -g @microsoft/clarity-mcp-server
```

## Usage with Different AI Assistants

### Claude Desktop
Add to your Claude configuration file:

```json
{
  "mcpServers": {
    "@microsoft/clarity-mcp-server": {
      "command": "npx",
      "args": ['@microsoft/clarity-mcp-server', '--clarity_api_token=your-api-token-here']
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



## Example Use Cases

1. Describe your workflow that needs this integration
2. Reference the capabilities in your AI assistant prompts
3. Let the AI assistant guide you through using the integration



---

## 🚀 Usage

**Reference this template:** `@integration-microsoftclarity-mcp-server.md`


**Platform-specific:**
- **GitHub Copilot**: Add to `.github/copilot-instructions.md`
- **Augment Code**: Use `aug context add` command
- **Cursor/Windsurf**: Reference in chat with `@filename`
- **Claude**: Add to Project Knowledge
- **ChatGPT**: Add to Custom GPT configuration
