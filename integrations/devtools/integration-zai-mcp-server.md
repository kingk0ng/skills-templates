---
id: integration-zai-mcp-server
type: integration
name: zai-mcp-server
description: Vision MCP Server - Z.AI capability implementation based on the Model
  Context Protocol (MCP), providing powerful Z.AI GLM-4.6V capabilities for MCP-compatible
  clients such as Claude Code and Cline, including image analysis, video understanding,
  and other features.
category: devtools
complexity: medium
keywords: []
capabilities: []
token_estimate: 271
package_name: '@z_ai/mcp-server'
---

# zai-mcp-server

Vision MCP Server - Z.AI capability implementation based on the Model Context Protocol (MCP), providing powerful Z.AI GLM-4.6V capabilities for MCP-compatible clients such as Claude Code and Cline, including image analysis, video understanding, and other features.

# zai-mcp-server Integration

Vision MCP Server - Z.AI capability implementation based on the Model Context Protocol (MCP), providing powerful Z.AI GLM-4.6V capabilities for MCP-compatible clients such as Claude Code and Cline, including image analysis, video understanding, and other features.

## Installation

This integration uses an npm package.

### Option 1: Direct Usage (Recommended)
```bash
npx @z_ai/mcp-server
```

### Option 2: Global Installation
```bash
npm install -g @z_ai/mcp-server
```

## Usage with Different AI Assistants

### Claude Desktop
Add to your Claude configuration file:

```json
{
  "mcpServers": {
    "zai-mcp-server": {
      "command": "npx",
      "args": ['-y', '@z_ai/mcp-server']
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

Vision MCP Server - Z.AI capability implementation based on the Model Context Protocol (MCP), providing powerful Z.AI GLM-4.6V capabilities for MCP-compatible clients such as Claude Code and Cline, including image analysis, video understanding, and other features.

## Example Use Cases

1. Describe your workflow that needs this integration
2. Reference the capabilities in your AI assistant prompts
3. Let the AI assistant guide you through using the integration



---

## 🚀 Usage

**Reference this template:** `@integration-zai-mcp-server.md`


**Platform-specific:**
- **GitHub Copilot**: Add to `.github/copilot-instructions.md`
- **Augment Code**: Use `aug context add` command
- **Cursor/Windsurf**: Reference in chat with `@filename`
- **Claude**: Add to Project Knowledge
- **ChatGPT**: Add to Custom GPT configuration
