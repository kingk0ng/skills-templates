---
id: integration-context7
type: integration
name: context7
description: Context7 MCP pulls up-to-date, version-specific documentation and code
  examples straight from the source — and places them directly into your prompt.
category: devtools
complexity: medium
keywords: []
capabilities: []
token_estimate: 232
package_name: '@upstash/context7-mcp'
---

# context7

Context7 MCP pulls up-to-date, version-specific documentation and code examples straight from the source — and places them directly into your prompt.

# context7 Integration

Context7 MCP pulls up-to-date, version-specific documentation and code examples straight from the source — and places them directly into your prompt.

## Installation

This integration uses an npm package.

### Option 1: Direct Usage (Recommended)
```bash
npx @upstash/context7-mcp
```

### Option 2: Global Installation
```bash
npm install -g @upstash/context7-mcp
```

## Usage with Different AI Assistants

### Claude Desktop
Add to your Claude configuration file:

```json
{
  "mcpServers": {
    "context7": {
      "command": "npx",
      "args": ['-y', '@upstash/context7-mcp']
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

Context7 MCP pulls up-to-date, version-specific documentation and code examples straight from the source — and places them directly into your prompt.

## Example Use Cases

1. Describe your workflow that needs this integration
2. Reference the capabilities in your AI assistant prompts
3. Let the AI assistant guide you through using the integration



---

## 🚀 Usage

**Reference this template:** `@integration-context7.md`


**Platform-specific:**
- **GitHub Copilot**: Add to `.github/copilot-instructions.md`
- **Augment Code**: Use `aug context add` command
- **Cursor/Windsurf**: Reference in chat with `@filename`
- **Claude**: Add to Project Knowledge
- **ChatGPT**: Add to Custom GPT configuration
