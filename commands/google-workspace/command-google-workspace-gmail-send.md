---
id: command-google-workspace-gmail-send
type: command
name: Google Workspace Gmail Send
description: '---

  allowed-tools: Bash, Read, Write, Edit

  argument-hint: [resource] [method] [flags]

  description: Gmail: Send an email.

  ---...'
category: google-workspace
complexity: medium
keywords:
- api
- github
- security
capabilities: []
token_estimate: 488
has_scripts: true
languages:
- bash
---

<!-- Converted from Claude Command Template -->
<!-- Complexity: Medium -->
<!-- Estimated Tokens: ~488 -->


> **How to Use This Template**
>
> This template can be used with various AI coding assistants:
> 
> **GitHub Copilot:**
> - Add to `.github/copilot-instructions.md` in your repository
> - Reference in chat: `@workspace` to include in context
> - Add specific sections to your workspace instructions
> 
> **Augment Code:**
> - Load context: `aug context add <path-to-this-file>`
> - Reference in queries naturally
> - Use with specific commands
> 
> **Claude (Desktop/Web):**
> - Add to Project Knowledge in Claude Projects
> - Reference in custom instructions
> - Copy relevant sections as needed
> 
> **ChatGPT:**
> - Add to Custom GPT configuration
> - Include in conversation instructions
> - Use as system prompt
> 
> **Generic Usage:**
> Copy the content below and paste it into your AI assistant's context window
> or system instructions.

---




# Google Workspace Gmail Send

> ---
allowed-tools: Bash, Read, Write, Edit
argument-hint: [resource] [method] [flags]
description: Gmail: Send an email.
---...

---
allowed-capabilities: File operations, code editing, terminal access, search
argument-hint: [resource] [method] [flags]
description: Gmail: Send an email.
---

# Google Workspace Gmail Send

Execute Google Workspace Gmail Send operations: $ARGUMENTS

## Prerequisites

- Google Workspace CLI (`gws`) must be installed
- Authentication configured: Run `gws auth status` to verify
- Review `gws gmail-send --help` for all available commands

## Available Resources and Methods

# gmail +send

> **PREREQUISITE:** Read `../gws-shared/SKILL.md` for auth, global flags, and security rules. If missing, run `gws generate-skills` to create it.

Send an email

## Usage

```bash
gws gmail +send --to <EMAIL> --subject <SUBJECT> --body <TEXT>
```

## Flags

| Flag | Required | Default | Description |
|------|----------|---------|-------------|
| `--to` | ✓ | — | Recipient email address |
| `--subject` | ✓ | — | Email subject |
| `--body` | ✓ | — | Email body (plain text) |

## Examples

```bash
gws gmail +send --to alice@example.com --subject 'Hello' --body 'Hi Alice!'
```

## Tips

- Handles RFC 2822 formatting and base64 encoding automatically.
- For HTML bodies, attachments, or CC/BCC, use the raw API instead:
- gws gmail users messages send --json '...'

> [!CAUTION]
> This is a **write** command — confirm with the user before executing.

## See Also

- [gws-shared](../gws-shared/SKILL.md) — Global flags and auth
- [gws-gmail](../gws-gmail/SKILL.md) — All send, read, and manage email commands

## Usage

```bash
# List available resources and methods
gws gmail-send --help

# Inspect method schema before calling
gws schema gmail-send.<resource>.<method>

# Execute command with arguments
gws gmail-send $ARGUMENTS
```

## Task

Execute the requested Gmail Send operation: $ARGUMENTS

1. **Verify Prerequisites**
   - Check `gws` is installed: `gws --version`
   - Verify authentication: `gws auth status`
   - Review available commands: `gws gmail-send --help`

2. **Inspect Method Schema**
   - Before calling any method, inspect its parameters
   - Use `gws schema` to understand required fields
   - Review parameter types and constraints

3. **Execute Operation**
   - Build command with appropriate flags
   - Use `--params` for query/path parameters
   - Use `--json` for request body
   - Handle pagination with `--max-results` or `--page-token`

4. **Error Handling**
   - Check command output for errors
   - Review API quotas and rate limits
   - Handle authentication issues
   - Retry transient failures

---

**License**: Apache License 2.0
**Source**: [Google Workspace CLI](https://github.com/googleworkspace/cli)
**Original Skill**: `gws-gmail-send`


---


## 💻 Code Examples


### Example 1

```bash
gws gmail +send --to <EMAIL> --subject <SUBJECT> --body <TEXT>
```


### Example 2

```bash
gws gmail +send --to alice@example.com --subject 'Hello' --body 'Hi Alice!'
```


### Example 3

```bash
# List available resources and methods
gws gmail-send --help

# Inspect method schema before calling
gws schema gmail-send.<resource>.<method>

# Execute command with arguments
gws gmail-send $ARGUMENTS
```


---

## 🚀 Usage

**Reference this template:** `@command-google-workspace-gmail-send.md`


**Platform-specific:**
- **GitHub Copilot**: Add to `.github/copilot-instructions.md`
- **Augment Code**: Use `aug context add` command
- **Cursor/Windsurf**: Reference in chat with `@filename`
- **Claude**: Add to Project Knowledge
- **ChatGPT**: Add to Custom GPT configuration
