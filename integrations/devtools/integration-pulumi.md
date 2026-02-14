---
id: integration-pulumi
type: integration
name: pulumi
description: The Pulumi Model Context Protocol (MCP) server enables advanced Infrastructure
  as Code development capabilities for connected agents, providing tools for cloud
  resource discovery and management using Pulumi Cloud and the Pulumi CLI.
category: devtools
complexity: medium
keywords: []
capabilities: []
token_estimate: 262
package_name: '@pulumi/mcp-server@latest'
---

# pulumi

The Pulumi Model Context Protocol (MCP) server enables advanced Infrastructure as Code development capabilities for connected agents, providing tools for cloud resource discovery and management using Pulumi Cloud and the Pulumi CLI.

# pulumi Integration

The Pulumi Model Context Protocol (MCP) server enables advanced Infrastructure as Code development capabilities for connected agents, providing tools for cloud resource discovery and management using Pulumi Cloud and the Pulumi CLI.

## Installation

This integration uses an npm package.

### Option 1: Direct Usage (Recommended)
```bash
npx @pulumi/mcp-server@latest
```

### Option 2: Global Installation
```bash
npm install -g @pulumi/mcp-server@latest
```

## Usage with Different AI Assistants

### Claude Desktop
Add to your Claude configuration file:

```json
{
  "mcpServers": {
    "pulumi": {
      "command": "npx",
      "args": ['-y', '@pulumi/mcp-server@latest', 'stdio']
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

The Pulumi Model Context Protocol (MCP) server enables advanced Infrastructure as Code development capabilities for connected agents, providing tools for cloud resource discovery and management using Pulumi Cloud and the Pulumi CLI.

## Example Use Cases

1. Describe your workflow that needs this integration
2. Reference the capabilities in your AI assistant prompts
3. Let the AI assistant guide you through using the integration



---

## 🚀 Usage

**Reference this template:** `@integration-pulumi.md`


**Platform-specific:**
- **GitHub Copilot**: Add to `.github/copilot-instructions.md`
- **Augment Code**: Use `aug context add` command
- **Cursor/Windsurf**: Reference in chat with `@filename`
- **Claude**: Add to Project Knowledge
- **ChatGPT**: Add to Custom GPT configuration
