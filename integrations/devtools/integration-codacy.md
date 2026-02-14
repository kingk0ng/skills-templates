---
id: integration-codacy
type: integration
name: codacy
description: MCP Server for the Codacy API, enabling access to repositories, files,
  quality, coverage, security and more.
category: devtools
complexity: medium
keywords: []
capabilities: []
token_estimate: 219
package_name: '@codacy/codacy-mcp'
---

# codacy

MCP Server for the Codacy API, enabling access to repositories, files, quality, coverage, security and more.

# codacy Integration

MCP Server for the Codacy API, enabling access to repositories, files, quality, coverage, security and more.

## Installation

This integration uses an npm package.

### Option 1: Direct Usage (Recommended)
```bash
npx @codacy/codacy-mcp
```

### Option 2: Global Installation
```bash
npm install -g @codacy/codacy-mcp
```

## Usage with Different AI Assistants

### Claude Desktop
Add to your Claude configuration file:

```json
{
  "mcpServers": {
    "codacy": {
      "command": "npx",
      "args": ['-y', '@codacy/codacy-mcp']
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

MCP Server for the Codacy API, enabling access to repositories, files, quality, coverage, security and more.

## Example Use Cases

1. Describe your workflow that needs this integration
2. Reference the capabilities in your AI assistant prompts
3. Let the AI assistant guide you through using the integration



---

## 🚀 Usage

**Reference this template:** `@integration-codacy.md`


**Platform-specific:**
- **GitHub Copilot**: Add to `.github/copilot-instructions.md`
- **Augment Code**: Use `aug context add` command
- **Cursor/Windsurf**: Reference in chat with `@filename`
- **Claude**: Add to Project Knowledge
- **ChatGPT**: Add to Custom GPT configuration
