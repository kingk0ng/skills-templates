---
id: integration-supabase
type: integration
name: supabase
description: Connect your Claude Code to Supabase using MCP
category: database
complexity: medium
keywords: []
capabilities: []
token_estimate: 201
package_name: '@supabase/mcp-server-supabase@latest'
---

# supabase

Connect your Claude Code to Supabase using MCP

# supabase Integration

Connect your Claude Code to Supabase using MCP

## Installation

This integration uses an npm package.

### Option 1: Direct Usage (Recommended)
```bash
npx @supabase/mcp-server-supabase@latest
```

### Option 2: Global Installation
```bash
npm install -g @supabase/mcp-server-supabase@latest
```

## Usage with Different AI Assistants

### Claude Desktop
Add to your Claude configuration file:

```json
{
  "mcpServers": {
    "supabase": {
      "command": "npx",
      "args": ['-y', '@supabase/mcp-server-supabase@latest', '--read-only', '--project-ref=<project-ref>']
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

Connect your Claude Code to Supabase using MCP

## Example Use Cases

1. Describe your workflow that needs this integration
2. Reference the capabilities in your AI assistant prompts
3. Let the AI assistant guide you through using the integration



---

## 🚀 Usage

**Reference this template:** `@integration-supabase.md`


**Platform-specific:**
- **GitHub Copilot**: Add to `.github/copilot-instructions.md`
- **Augment Code**: Use `aug context add` command
- **Cursor/Windsurf**: Reference in chat with `@filename`
- **Claude**: Add to Project Knowledge
- **ChatGPT**: Add to Custom GPT configuration
