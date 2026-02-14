---
id: integration-monday-api-mcp
type: integration
name: monday-api-mcp
description: Enable AI agents to operate reliably within real workflows. This MCP
  is monday.com's open framework for connecting agents into your work OS - giving
  them secure access to structured data, tools to take action, and the context needed
  to make smart decisions.
category: productivity
complexity: medium
keywords: []
capabilities: []
token_estimate: 288
package_name: '@mondaydotcomorg/monday-api-mcp'
---

# monday-api-mcp

Enable AI agents to operate reliably within real workflows. This MCP is monday.com's open framework for connecting agents into your work OS - giving them secure access to structured data, tools to take action, and the context needed to make smart decisions.

# monday-api-mcp Integration

Enable AI agents to operate reliably within real workflows. This MCP is monday.com's open framework for connecting agents into your work OS - giving them secure access to structured data, tools to take action, and the context needed to make smart decisions.

## Installation

This integration uses an npm package.

### Option 1: Direct Usage (Recommended)
```bash
npx @mondaydotcomorg/monday-api-mcp
```

### Option 2: Global Installation
```bash
npm install -g @mondaydotcomorg/monday-api-mcp
```

## Usage with Different AI Assistants

### Claude Desktop
Add to your Claude configuration file:

```json
{
  "mcpServers": {
    "monday-api-mcp": {
      "command": "npx",
      "args": ['@mondaydotcomorg/monday-api-mcp', '-t', 'your_monday_api_token']
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

Enable AI agents to operate reliably within real workflows. This MCP is monday.com's open framework for connecting agents into your work OS - giving them secure access to structured data, tools to take action, and the context needed to make smart decisions.

## Example Use Cases

1. Describe your workflow that needs this integration
2. Reference the capabilities in your AI assistant prompts
3. Let the AI assistant guide you through using the integration



---

## 🚀 Usage

**Reference this template:** `@integration-monday-api-mcp.md`


**Platform-specific:**
- **GitHub Copilot**: Add to `.github/copilot-instructions.md`
- **Augment Code**: Use `aug context add` command
- **Cursor/Windsurf**: Reference in chat with `@filename`
- **Claude**: Add to Project Knowledge
- **ChatGPT**: Add to Custom GPT configuration
