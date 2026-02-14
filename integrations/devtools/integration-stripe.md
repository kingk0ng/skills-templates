---
id: integration-stripe
type: integration
name: stripe
description: Let your AI agents interact with the Stripe API by using our MCP server.
category: devtools
complexity: medium
keywords: []
capabilities: []
token_estimate: 215
package_name: '@stripe/mcp'
---

# stripe

Let your AI agents interact with the Stripe API by using our MCP server.

# stripe Integration

Let your AI agents interact with the Stripe API by using our MCP server.

## Installation

This integration uses an npm package.

### Option 1: Direct Usage (Recommended)
```bash
npx @stripe/mcp
```

### Option 2: Global Installation
```bash
npm install -g @stripe/mcp
```

## Usage with Different AI Assistants

### Claude Desktop
Add to your Claude configuration file:

```json
{
  "mcpServers": {
    "stripe": {
      "command": "npx",
      "args": ['-y', '@stripe/mcp', '--tools=all']
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

Let your AI agents interact with the Stripe API by using our MCP server.

## Example Use Cases

1. Describe your workflow that needs this integration
2. Reference the capabilities in your AI assistant prompts
3. Let the AI assistant guide you through using the integration



---

## 🚀 Usage

**Reference this template:** `@integration-stripe.md`


**Platform-specific:**
- **GitHub Copilot**: Add to `.github/copilot-instructions.md`
- **Augment Code**: Use `aug context add` command
- **Cursor/Windsurf**: Reference in chat with `@filename`
- **Claude**: Add to Project Knowledge
- **ChatGPT**: Add to Custom GPT configuration
