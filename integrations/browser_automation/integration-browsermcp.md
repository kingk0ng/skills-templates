---
id: integration-browsermcp
type: integration
name: browsermcp
description: With Browser MCP, you can use MCP to automate your browser so that AI
  applications can navigate the web, fill out forms, and more.
category: browser_automation
complexity: medium
keywords: []
capabilities: []
token_estimate: 239
package_name: '@browsermcp/mcp@latest'
---

# browsermcp

With Browser MCP, you can use MCP to automate your browser so that AI applications can navigate the web, fill out forms, and more.

# browsermcp Integration

With Browser MCP, you can use MCP to automate your browser so that AI applications can navigate the web, fill out forms, and more.

## Installation

This integration uses an npm package.

### Option 1: Direct Usage (Recommended)
```bash
npx @browsermcp/mcp@latest
```

### Option 2: Global Installation
```bash
npm install -g @browsermcp/mcp@latest
```

## Usage with Different AI Assistants

### Claude Desktop
Add to your Claude configuration file:

```json
{
  "mcpServers": {
    "browsermcp": {
      "command": "npx",
      "args": ['@browsermcp/mcp@latest']
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

With Browser MCP, you can use MCP to automate your browser so that AI applications can navigate the web, fill out forms, and more.

## Example Use Cases

1. Describe your workflow that needs this integration
2. Reference the capabilities in your AI assistant prompts
3. Let the AI assistant guide you through using the integration



---

## 🚀 Usage

**Reference this template:** `@integration-browsermcp.md`


**Platform-specific:**
- **GitHub Copilot**: Add to `.github/copilot-instructions.md`
- **Augment Code**: Use `aug context add` command
- **Cursor/Windsurf**: Reference in chat with `@filename`
- **Claude**: Add to Project Knowledge
- **ChatGPT**: Add to Custom GPT configuration
