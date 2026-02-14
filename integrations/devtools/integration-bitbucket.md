---
id: integration-bitbucket
type: integration
name: bitbucket
description: A Node.js/TypeScript Model Context Protocol (MCP) server for Atlassian
  Bitbucket Cloud. Enables AI systems (e.g., LLMs like Claude or Cursor AI) to securely
  interact with your repositories, pull requests, workspaces, and code in real time.
category: devtools
complexity: medium
keywords: []
capabilities: []
token_estimate: 269
package_name: '@aashari/mcp-server-atlassian-bitbucket'
---

# bitbucket

A Node.js/TypeScript Model Context Protocol (MCP) server for Atlassian Bitbucket Cloud. Enables AI systems (e.g., LLMs like Claude or Cursor AI) to securely interact with your repositories, pull requests, workspaces, and code in real time.

# bitbucket Integration

A Node.js/TypeScript Model Context Protocol (MCP) server for Atlassian Bitbucket Cloud. Enables AI systems (e.g., LLMs like Claude or Cursor AI) to securely interact with your repositories, pull requests, workspaces, and code in real time.

## Installation

This integration uses an npm package.

### Option 1: Direct Usage (Recommended)
```bash
npx @aashari/mcp-server-atlassian-bitbucket
```

### Option 2: Global Installation
```bash
npm install -g @aashari/mcp-server-atlassian-bitbucket
```

## Usage with Different AI Assistants

### Claude Desktop
Add to your Claude configuration file:

```json
{
  "mcpServers": {
    "bitbucket": {
      "command": "npx",
      "args": ['-y', '@aashari/mcp-server-atlassian-bitbucket']
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

A Node.js/TypeScript Model Context Protocol (MCP) server for Atlassian Bitbucket Cloud. Enables AI systems (e.g., LLMs like Claude or Cursor AI) to securely interact with your repositories, pull requests, workspaces, and code in real time.

## Example Use Cases

1. Describe your workflow that needs this integration
2. Reference the capabilities in your AI assistant prompts
3. Let the AI assistant guide you through using the integration



---

## 🚀 Usage

**Reference this template:** `@integration-bitbucket.md`


**Platform-specific:**
- **GitHub Copilot**: Add to `.github/copilot-instructions.md`
- **Augment Code**: Use `aug context add` command
- **Cursor/Windsurf**: Reference in chat with `@filename`
- **Claude**: Add to Project Knowledge
- **ChatGPT**: Add to Custom GPT configuration
