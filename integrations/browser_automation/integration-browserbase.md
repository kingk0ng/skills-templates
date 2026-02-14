---
id: integration-browserbase
type: integration
name: browserbase
description: This server provides cloud browser automation capabilities using Browserbase
  and Stagehand. It enables LLMs to interact with web pages, take screenshots, extract
  information, and perform automated actions with atomic precision.
category: browser_automation
complexity: medium
keywords: []
capabilities: []
token_estimate: 254
package_name: '@browserbasehq/mcp-server-browserbase'
---

# browserbase

This server provides cloud browser automation capabilities using Browserbase and Stagehand. It enables LLMs to interact with web pages, take screenshots, extract information, and perform automated actions with atomic precision.

# browserbase Integration

This server provides cloud browser automation capabilities using Browserbase and Stagehand. It enables LLMs to interact with web pages, take screenshots, extract information, and perform automated actions with atomic precision.

## Installation

This integration uses an npm package.

### Option 1: Direct Usage (Recommended)
```bash
npx @browserbasehq/mcp-server-browserbase
```

### Option 2: Global Installation
```bash
npm install -g @browserbasehq/mcp-server-browserbase
```

## Usage with Different AI Assistants

### Claude Desktop
Add to your Claude configuration file:

```json
{
  "mcpServers": {
    "browserbase": {
      "command": "npx",
      "args": ['@browserbasehq/mcp-server-browserbase']
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

This server provides cloud browser automation capabilities using Browserbase and Stagehand. It enables LLMs to interact with web pages, take screenshots, extract information, and perform automated actions with atomic precision.

## Example Use Cases

1. Describe your workflow that needs this integration
2. Reference the capabilities in your AI assistant prompts
3. Let the AI assistant guide you through using the integration



---

## 🚀 Usage

**Reference this template:** `@integration-browserbase.md`


**Platform-specific:**
- **GitHub Copilot**: Add to `.github/copilot-instructions.md`
- **Augment Code**: Use `aug context add` command
- **Cursor/Windsurf**: Reference in chat with `@filename`
- **Claude**: Add to Project Knowledge
- **ChatGPT**: Add to Custom GPT configuration
