---
id: integration-firefly
type: integration
name: firefly
description: Connect to Firefly AI services for advanced AI-powered development assistance,
  code analysis, and intelligent suggestions directly in your Claude Code environment.
category: devtools
complexity: medium
keywords: []
capabilities: []
token_estimate: 232
package_name: '@fireflyai/firefly-mcp'
---

# firefly

Connect to Firefly AI services for advanced AI-powered development assistance, code analysis, and intelligent suggestions directly in your Claude Code environment.

# firefly Integration

Connect to Firefly AI services for advanced AI-powered development assistance, code analysis, and intelligent suggestions directly in your Claude Code environment.

## Installation

This integration uses an npm package.

### Option 1: Direct Usage (Recommended)
```bash
npx @fireflyai/firefly-mcp
```

### Option 2: Global Installation
```bash
npm install -g @fireflyai/firefly-mcp
```

## Usage with Different AI Assistants

### Claude Desktop
Add to your Claude configuration file:

```json
{
  "mcpServers": {
    "firefly": {
      "command": "npx",
      "args": ['-y', '@fireflyai/firefly-mcp']
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

Connect to Firefly AI services for advanced AI-powered development assistance, code analysis, and intelligent suggestions directly in your Claude Code environment.

## Example Use Cases

1. Describe your workflow that needs this integration
2. Reference the capabilities in your AI assistant prompts
3. Let the AI assistant guide you through using the integration



---

## 🚀 Usage

**Reference this template:** `@integration-firefly.md`


**Platform-specific:**
- **GitHub Copilot**: Add to `.github/copilot-instructions.md`
- **Augment Code**: Use `aug context add` command
- **Cursor/Windsurf**: Reference in chat with `@filename`
- **Claude**: Add to Project Knowledge
- **ChatGPT**: Add to Custom GPT configuration
