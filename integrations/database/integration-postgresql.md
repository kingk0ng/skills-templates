---
id: integration-postgresql
type: integration
name: postgresql
description: Connect to PostgreSQL databases for advanced data operations, complex
  queries, and enterprise database management.
category: database
complexity: medium
keywords: []
capabilities: []
token_estimate: 214
package_name: '@modelcontextprotocol/server-postgres'
---

# postgresql

Connect to PostgreSQL databases for advanced data operations, complex queries, and enterprise database management.

# postgresql Integration

Connect to PostgreSQL databases for advanced data operations, complex queries, and enterprise database management.

## Installation

This integration uses an npm package.

### Option 1: Direct Usage (Recommended)
```bash
npx @modelcontextprotocol/server-postgres
```

### Option 2: Global Installation
```bash
npm install -g @modelcontextprotocol/server-postgres
```

## Usage with Different AI Assistants

### Claude Desktop
Add to your Claude configuration file:

```json
{
  "mcpServers": {
    "postgresql": {
      "command": "npx",
      "args": ['-y', '@modelcontextprotocol/server-postgres']
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

Connect to PostgreSQL databases for advanced data operations, complex queries, and enterprise database management.

## Example Use Cases

1. Describe your workflow that needs this integration
2. Reference the capabilities in your AI assistant prompts
3. Let the AI assistant guide you through using the integration



---

## 🚀 Usage

**Reference this template:** `@integration-postgresql.md`


**Platform-specific:**
- **GitHub Copilot**: Add to `.github/copilot-instructions.md`
- **Augment Code**: Use `aug context add` command
- **Cursor/Windsurf**: Reference in chat with `@filename`
- **Claude**: Add to Project Knowledge
- **ChatGPT**: Add to Custom GPT configuration
