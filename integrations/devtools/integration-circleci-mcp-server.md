---
id: integration-circleci-mcp-server
type: integration
name: circleci-mcp-server
description: Integrate CircleCI build and deployment pipeline management with your
  Claude Code workflow. Monitor builds, trigger deployments, and access project insights.
category: devtools
complexity: medium
keywords: []
capabilities: []
token_estimate: 230
package_name: '@circleci/mcp-server-circleci'
---

# circleci-mcp-server

Integrate CircleCI build and deployment pipeline management with your Claude Code workflow. Monitor builds, trigger deployments, and access project insights.

# circleci-mcp-server Integration

Integrate CircleCI build and deployment pipeline management with your Claude Code workflow. Monitor builds, trigger deployments, and access project insights.

## Installation

This integration uses an npm package.

### Option 1: Direct Usage (Recommended)
```bash
npx @circleci/mcp-server-circleci
```

### Option 2: Global Installation
```bash
npm install -g @circleci/mcp-server-circleci
```

## Usage with Different AI Assistants

### Claude Desktop
Add to your Claude configuration file:

```json
{
  "mcpServers": {
    "circleci-mcp-server": {
      "command": "npx",
      "args": ['-y', '@circleci/mcp-server-circleci']
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

Integrate CircleCI build and deployment pipeline management with your Claude Code workflow. Monitor builds, trigger deployments, and access project insights.

## Example Use Cases

1. Describe your workflow that needs this integration
2. Reference the capabilities in your AI assistant prompts
3. Let the AI assistant guide you through using the integration



---

## 🚀 Usage

**Reference this template:** `@integration-circleci-mcp-server.md`


**Platform-specific:**
- **GitHub Copilot**: Add to `.github/copilot-instructions.md`
- **Augment Code**: Use `aug context add` command
- **Cursor/Windsurf**: Reference in chat with `@filename`
- **Claude**: Add to Project Knowledge
- **ChatGPT**: Add to Custom GPT configuration
