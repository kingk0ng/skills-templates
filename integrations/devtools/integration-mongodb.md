---
id: integration-mongodb
type: integration
name: MongoDB
description: A Model Context Protocol server to connect to MongoDB databases and MongoDB
  Atlas Clusters.
category: devtools
complexity: medium
keywords: []
capabilities: []
token_estimate: 215
package_name: mongodb-mcp-server@latest
---

# MongoDB

A Model Context Protocol server to connect to MongoDB databases and MongoDB Atlas Clusters.

# MongoDB Integration

A Model Context Protocol server to connect to MongoDB databases and MongoDB Atlas Clusters.

## Installation

This integration uses an npm package.

### Option 1: Direct Usage (Recommended)
```bash
npx mongodb-mcp-server@latest
```

### Option 2: Global Installation
```bash
npm install -g mongodb-mcp-server@latest
```

## Usage with Different AI Assistants

### Claude Desktop
Add to your Claude configuration file:

```json
{
  "mcpServers": {
    "MongoDB": {
      "command": "npx",
      "args": ['-y', 'mongodb-mcp-server@latest', '--readOnly']
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

A Model Context Protocol server to connect to MongoDB databases and MongoDB Atlas Clusters.

## Example Use Cases

1. Describe your workflow that needs this integration
2. Reference the capabilities in your AI assistant prompts
3. Let the AI assistant guide you through using the integration



---

## 🚀 Usage

**Reference this template:** `@integration-mongodb.md`


**Platform-specific:**
- **GitHub Copilot**: Add to `.github/copilot-instructions.md`
- **Augment Code**: Use `aug context add` command
- **Cursor/Windsurf**: Reference in chat with `@filename`
- **Claude**: Add to Project Knowledge
- **ChatGPT**: Add to Custom GPT configuration
