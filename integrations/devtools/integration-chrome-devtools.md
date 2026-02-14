---
id: integration-chrome-devtools
type: integration
name: chrome-devtools
description: A Model Context Protocol server for interacting with Chrome DevTools,
  enabling browser automation, debugging, and performance analysis capabilities.
category: devtools
complexity: medium
keywords: []
capabilities: []
token_estimate: 224
package_name: chrome-devtools-mcp@latest
---

# chrome-devtools

A Model Context Protocol server for interacting with Chrome DevTools, enabling browser automation, debugging, and performance analysis capabilities.

# chrome-devtools Integration

A Model Context Protocol server for interacting with Chrome DevTools, enabling browser automation, debugging, and performance analysis capabilities.

## Installation

This integration uses an npm package.

### Option 1: Direct Usage (Recommended)
```bash
npx chrome-devtools-mcp@latest
```

### Option 2: Global Installation
```bash
npm install -g chrome-devtools-mcp@latest
```

## Usage with Different AI Assistants

### Claude Desktop
Add to your Claude configuration file:

```json
{
  "mcpServers": {
    "chrome-devtools": {
      "command": "npx",
      "args": ['-y', 'chrome-devtools-mcp@latest']
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

A Model Context Protocol server for interacting with Chrome DevTools, enabling browser automation, debugging, and performance analysis capabilities.

## Example Use Cases

1. Describe your workflow that needs this integration
2. Reference the capabilities in your AI assistant prompts
3. Let the AI assistant guide you through using the integration



---

## 🚀 Usage

**Reference this template:** `@integration-chrome-devtools.md`


**Platform-specific:**
- **GitHub Copilot**: Add to `.github/copilot-instructions.md`
- **Augment Code**: Use `aug context add` command
- **Cursor/Windsurf**: Reference in chat with `@filename`
- **Claude**: Add to Project Knowledge
- **ChatGPT**: Add to Custom GPT configuration
