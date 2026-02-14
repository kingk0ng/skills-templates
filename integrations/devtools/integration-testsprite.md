---
id: integration-testsprite
type: integration
name: TestSprite
description: TestSprite’s MCP reads your intent, tests your code, and tells you what
  to fix.
category: devtools
complexity: medium
keywords: []
capabilities: []
token_estimate: 213
package_name: '@testsprite/testsprite-mcp@latest'
---

# TestSprite

TestSprite’s MCP reads your intent, tests your code, and tells you what to fix.

# TestSprite Integration

TestSprite’s MCP reads your intent, tests your code, and tells you what to fix.

## Installation

This integration uses an npm package.

### Option 1: Direct Usage (Recommended)
```bash
npx @testsprite/testsprite-mcp@latest
```

### Option 2: Global Installation
```bash
npm install -g @testsprite/testsprite-mcp@latest
```

## Usage with Different AI Assistants

### Claude Desktop
Add to your Claude configuration file:

```json
{
  "mcpServers": {
    "TestSprite": {
      "command": "npx",
      "args": ['@testsprite/testsprite-mcp@latest']
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

TestSprite’s MCP reads your intent, tests your code, and tells you what to fix.

## Example Use Cases

1. Describe your workflow that needs this integration
2. Reference the capabilities in your AI assistant prompts
3. Let the AI assistant guide you through using the integration



---

## 🚀 Usage

**Reference this template:** `@integration-testsprite.md`


**Platform-specific:**
- **GitHub Copilot**: Add to `.github/copilot-instructions.md`
- **Augment Code**: Use `aug context add` command
- **Cursor/Windsurf**: Reference in chat with `@filename`
- **Claude**: Add to Project Knowledge
- **ChatGPT**: Add to Custom GPT configuration
