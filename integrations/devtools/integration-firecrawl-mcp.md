---
id: integration-firecrawl-mcp
type: integration
name: firecrawl-mcp
description: A Model Context Protocol (MCP) server implementation that integrates
  with Firecrawl for web scraping capabilities.
category: devtools
complexity: medium
keywords: []
capabilities: []
token_estimate: 217
package_name: firecrawl-mcp
---

# firecrawl-mcp

A Model Context Protocol (MCP) server implementation that integrates with Firecrawl for web scraping capabilities.

# firecrawl-mcp Integration

A Model Context Protocol (MCP) server implementation that integrates with Firecrawl for web scraping capabilities.

## Installation

This integration uses an npm package.

### Option 1: Direct Usage (Recommended)
```bash
npx firecrawl-mcp
```

### Option 2: Global Installation
```bash
npm install -g firecrawl-mcp
```

## Usage with Different AI Assistants

### Claude Desktop
Add to your Claude configuration file:

```json
{
  "mcpServers": {
    "firecrawl-mcp": {
      "command": "npx",
      "args": ['-y', 'firecrawl-mcp']
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

A Model Context Protocol (MCP) server implementation that integrates with Firecrawl for web scraping capabilities.

## Example Use Cases

1. Describe your workflow that needs this integration
2. Reference the capabilities in your AI assistant prompts
3. Let the AI assistant guide you through using the integration



---

## 🚀 Usage

**Reference this template:** `@integration-firecrawl-mcp.md`


**Platform-specific:**
- **GitHub Copilot**: Add to `.github/copilot-instructions.md`
- **Augment Code**: Use `aug context add` command
- **Cursor/Windsurf**: Reference in chat with `@filename`
- **Claude**: Add to Project Knowledge
- **ChatGPT**: Add to Custom GPT configuration
