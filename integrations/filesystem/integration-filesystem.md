---
id: integration-filesystem
type: integration
name: filesystem
description: Secure filesystem access for Claude Code with configurable directory
  permissions and file operations.
category: filesystem
complexity: medium
keywords: []
capabilities: []
token_estimate: 213
package_name: '@modelcontextprotocol/server-filesystem'
---

# filesystem

Secure filesystem access for Claude Code with configurable directory permissions and file operations.

# filesystem Integration

Secure filesystem access for Claude Code with configurable directory permissions and file operations.

## Installation

This integration uses an npm package.

### Option 1: Direct Usage (Recommended)
```bash
npx @modelcontextprotocol/server-filesystem
```

### Option 2: Global Installation
```bash
npm install -g @modelcontextprotocol/server-filesystem
```

## Usage with Different AI Assistants

### Claude Desktop
Add to your Claude configuration file:

```json
{
  "mcpServers": {
    "filesystem": {
      "command": "npx",
      "args": ['-y', '@modelcontextprotocol/server-filesystem', '/path/to/allowed/files']
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

Secure filesystem access for Claude Code with configurable directory permissions and file operations.

## Example Use Cases

1. Describe your workflow that needs this integration
2. Reference the capabilities in your AI assistant prompts
3. Let the AI assistant guide you through using the integration



---

## 🚀 Usage

**Reference this template:** `@integration-filesystem.md`


**Platform-specific:**
- **GitHub Copilot**: Add to `.github/copilot-instructions.md`
- **Augment Code**: Use `aug context add` command
- **Cursor/Windsurf**: Reference in chat with `@filename`
- **Claude**: Add to Project Knowledge
- **ChatGPT**: Add to Custom GPT configuration
