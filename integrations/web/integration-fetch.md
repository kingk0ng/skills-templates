---
id: integration-fetch
type: integration
name: fetch
description: Web content fetching and data extraction capabilities. Access external
  APIs, scrape web content, and integrate external data sources.
category: web
complexity: medium
keywords: []
capabilities: []
token_estimate: 224
package_name: '@modelcontextprotocol/server-fetch'
---

# fetch

Web content fetching and data extraction capabilities. Access external APIs, scrape web content, and integrate external data sources.

# fetch Integration

Web content fetching and data extraction capabilities. Access external APIs, scrape web content, and integrate external data sources.

## Installation

This integration uses an npm package.

### Option 1: Direct Usage (Recommended)
```bash
npx @modelcontextprotocol/server-fetch
```

### Option 2: Global Installation
```bash
npm install -g @modelcontextprotocol/server-fetch
```

## Usage with Different AI Assistants

### Claude Desktop
Add to your Claude configuration file:

```json
{
  "mcpServers": {
    "fetch": {
      "command": "npx",
      "args": ['-y', '@modelcontextprotocol/server-fetch']
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

Web content fetching and data extraction capabilities. Access external APIs, scrape web content, and integrate external data sources.

## Example Use Cases

1. Describe your workflow that needs this integration
2. Reference the capabilities in your AI assistant prompts
3. Let the AI assistant guide you through using the integration



---

## 🚀 Usage

**Reference this template:** `@integration-fetch.md`


**Platform-specific:**
- **GitHub Copilot**: Add to `.github/copilot-instructions.md`
- **Augment Code**: Use `aug context add` command
- **Cursor/Windsurf**: Reference in chat with `@filename`
- **Claude**: Add to Project Knowledge
- **ChatGPT**: Add to Custom GPT configuration
