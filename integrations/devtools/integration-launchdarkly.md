---
id: integration-launchdarkly
type: integration
name: LaunchDarkly
description: Official LaunchDarkly MCP Server for feature flag management and experimentation.
  Enables AI agents to interact with LaunchDarkly APIs for managing feature flags,
  AI configs, targeting rules, and gradual rollouts across multiple environments.
category: devtools
complexity: medium
keywords: []
capabilities: []
token_estimate: 269
package_name: '@launchdarkly/mcp-server'
---

# LaunchDarkly

Official LaunchDarkly MCP Server for feature flag management and experimentation. Enables AI agents to interact with LaunchDarkly APIs for managing feature flags, AI configs, targeting rules, and gradual rollouts across multiple environments.

# LaunchDarkly Integration

Official LaunchDarkly MCP Server for feature flag management and experimentation. Enables AI agents to interact with LaunchDarkly APIs for managing feature flags, AI configs, targeting rules, and gradual rollouts across multiple environments.

## Installation

This integration uses an npm package.

### Option 1: Direct Usage (Recommended)
```bash
npx @launchdarkly/mcp-server
```

### Option 2: Global Installation
```bash
npm install -g --package
```

## Usage with Different AI Assistants

### Claude Desktop
Add to your Claude configuration file:

```json
{
  "mcpServers": {
    "LaunchDarkly": {
      "command": "npx",
      "args": ['-y', '--package', '@launchdarkly/mcp-server', '--', 'mcp', 'start', '--api-key', 'api-xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx']
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

Official LaunchDarkly MCP Server for feature flag management and experimentation. Enables AI agents to interact with LaunchDarkly APIs for managing feature flags, AI configs, targeting rules, and gradual rollouts across multiple environments.

## Example Use Cases

1. Describe your workflow that needs this integration
2. Reference the capabilities in your AI assistant prompts
3. Let the AI assistant guide you through using the integration



---

## 🚀 Usage

**Reference this template:** `@integration-launchdarkly.md`


**Platform-specific:**
- **GitHub Copilot**: Add to `.github/copilot-instructions.md`
- **Augment Code**: Use `aug context add` command
- **Cursor/Windsurf**: Reference in chat with `@filename`
- **Claude**: Add to Project Knowledge
- **ChatGPT**: Add to Custom GPT configuration
