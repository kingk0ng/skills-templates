---
id: integration-devbox
type: integration
name: DevBox
description: This server enables natural language interactions for developer-focused
  operations like managing Dev Boxes, configurations, and pools.
category: devtools
complexity: medium
keywords: []
capabilities: []
token_estimate: 219
package_name: '@microsoft/devbox-mcp@latest'
---

# DevBox

This server enables natural language interactions for developer-focused operations like managing Dev Boxes, configurations, and pools.

# DevBox Integration

This server enables natural language interactions for developer-focused operations like managing Dev Boxes, configurations, and pools.

## Installation

This integration uses an npm package.

### Option 1: Direct Usage (Recommended)
```bash
npx @microsoft/devbox-mcp@latest
```

### Option 2: Global Installation
```bash
npm install -g @microsoft/devbox-mcp@latest
```

## Usage with Different AI Assistants

### Claude Desktop
Add to your Claude configuration file:

```json
{
  "mcpServers": {
    "DevBox": {
      "command": "npx",
      "args": ['-y', '@microsoft/devbox-mcp@latest']
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

This server enables natural language interactions for developer-focused operations like managing Dev Boxes, configurations, and pools.

## Example Use Cases

1. Describe your workflow that needs this integration
2. Reference the capabilities in your AI assistant prompts
3. Let the AI assistant guide you through using the integration



---

## 🚀 Usage

**Reference this template:** `@integration-devbox.md`


**Platform-specific:**
- **GitHub Copilot**: Add to `.github/copilot-instructions.md`
- **Augment Code**: Use `aug context add` command
- **Cursor/Windsurf**: Reference in chat with `@filename`
- **Claude**: Add to Project Knowledge
- **ChatGPT**: Add to Custom GPT configuration
