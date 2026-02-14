---
id: integration-notionapi
type: integration
name: notionApi
description: Official MCP server for Notion API
category: productivity
complexity: medium
keywords: []
capabilities: []
token_estimate: 193
package_name: '@notionhq/notion-mcp-server'
---

# notionApi

Official MCP server for Notion API

# notionApi Integration

Official MCP server for Notion API

## Installation

This integration uses an npm package.

### Option 1: Direct Usage (Recommended)
```bash
npx @notionhq/notion-mcp-server
```

### Option 2: Global Installation
```bash
npm install -g @notionhq/notion-mcp-server
```

## Usage with Different AI Assistants

### Claude Desktop
Add to your Claude configuration file:

```json
{
  "mcpServers": {
    "notionApi": {
      "command": "npx",
      "args": ['-y', '@notionhq/notion-mcp-server']
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

Official MCP server for Notion API

## Example Use Cases

1. Describe your workflow that needs this integration
2. Reference the capabilities in your AI assistant prompts
3. Let the AI assistant guide you through using the integration



---

## 🚀 Usage

**Reference this template:** `@integration-notionapi.md`


**Platform-specific:**
- **GitHub Copilot**: Add to `.github/copilot-instructions.md`
- **Augment Code**: Use `aug context add` command
- **Cursor/Windsurf**: Reference in chat with `@filename`
- **Claude**: Add to Project Knowledge
- **ChatGPT**: Add to Custom GPT configuration
