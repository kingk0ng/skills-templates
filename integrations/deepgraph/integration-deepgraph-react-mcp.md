---
id: integration-deepgraph-react-mcp
type: integration
name: DeepGraph React MCP
description: Analyze React component hierarchies, state flows, and dependencies. Visualize
  your React application architecture.
category: deepgraph
complexity: medium
keywords: []
capabilities: []
token_estimate: 218
package_name: mcp-code-graph@latest
---

# DeepGraph React MCP

Analyze React component hierarchies, state flows, and dependencies. Visualize your React application architecture.

# DeepGraph React MCP Integration

Analyze React component hierarchies, state flows, and dependencies. Visualize your React application architecture.

## Installation

This integration uses an npm package.

### Option 1: Direct Usage (Recommended)
```bash
npx mcp-code-graph@latest
```

### Option 2: Global Installation
```bash
npm install -g mcp-code-graph@latest
```

## Usage with Different AI Assistants

### Claude Desktop
Add to your Claude configuration file:

```json
{
  "mcpServers": {
    "DeepGraph React MCP": {
      "command": "npx",
      "args": ['-y', 'mcp-code-graph@latest', 'facebook/react']
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

Analyze React component hierarchies, state flows, and dependencies. Visualize your React application architecture.

## Example Use Cases

1. Describe your workflow that needs this integration
2. Reference the capabilities in your AI assistant prompts
3. Let the AI assistant guide you through using the integration



---

## 🚀 Usage

**Reference this template:** `@integration-deepgraph-react-mcp.md`


**Platform-specific:**
- **GitHub Copilot**: Add to `.github/copilot-instructions.md`
- **Augment Code**: Use `aug context add` command
- **Cursor/Windsurf**: Reference in chat with `@filename`
- **Claude**: Add to Project Knowledge
- **ChatGPT**: Add to Custom GPT configuration
