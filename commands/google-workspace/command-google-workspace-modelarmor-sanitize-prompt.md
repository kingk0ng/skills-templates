---
id: command-google-workspace-modelarmor-sanitize-prompt
type: command
name: Google Workspace Modelarmor Sanitize Prompt
description: '---

  allowed-tools: Bash, Read, Write, Edit

  argument-hint: [resource] [method] [flags]

  description: Google Model Armor: Sanitize a user prompt through a Model Armor template.

  ---...'
category: google-workspace
complexity: medium
keywords:
- api
- github
- security
capabilities: []
token_estimate: 487
has_scripts: true
languages:
- bash
---

<!-- Converted from Claude Command Template -->
<!-- Complexity: Medium -->
<!-- Estimated Tokens: ~487 -->


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




# Google Workspace Modelarmor Sanitize Prompt

> ---
allowed-tools: Bash, Read, Write, Edit
argument-hint: [resource] [method] [flags]
description: Google Model Armor: Sanitize a user prompt through a Model Armor template.
---...

---
allowed-capabilities: File operations, code editing, terminal access, search
argument-hint: [resource] [method] [flags]
description: Google Model Armor: Sanitize a user prompt through a Model Armor template.
---

# Google Workspace Modelarmor Sanitize Prompt

Execute Google Workspace Modelarmor Sanitize Prompt operations: $ARGUMENTS

## Prerequisites

- Google Workspace CLI (`gws`) must be installed
- Authentication configured: Run `gws auth status` to verify
- Review `gws modelarmor-sanitize-prompt --help` for all available commands

## Available Resources and Methods

# modelarmor +sanitize-prompt

> **PREREQUISITE:** Read `../gws-shared/SKILL.md` for auth, global flags, and security rules. If missing, run `gws generate-skills` to create it.

Sanitize a user prompt through a Model Armor template

## Usage

```bash
gws modelarmor +sanitize-prompt --template <NAME>
```

## Flags

| Flag | Required | Default | Description |
|------|----------|---------|-------------|
| `--template` | ✓ | — | Full template resource name (projects/PROJECT/locations/LOCATION/templates/TEMPLATE) |
| `--text` | — | — | Text content to sanitize |
| `--json` | — | — | Full JSON request body (overrides --text) |

## Examples

```bash
gws modelarmor +sanitize-prompt --template projects/P/locations/L/templates/T --text 'user input'
echo 'prompt' | gws modelarmor +sanitize-prompt --template ...
```

## Tips

- If neither --text nor --json is given, reads from stdin.
- For outbound safety, use +sanitize-response instead.

## See Also

- [gws-shared](../gws-shared/SKILL.md) — Global flags and auth
- [gws-modelarmor](../gws-modelarmor/SKILL.md) — All filter user-generated content for safety commands

## Usage

```bash
# List available resources and methods
gws modelarmor-sanitize-prompt --help

# Inspect method schema before calling
gws schema modelarmor-sanitize-prompt.<resource>.<method>

# Execute command with arguments
gws modelarmor-sanitize-prompt $ARGUMENTS
```

## Task

Execute the requested Modelarmor Sanitize Prompt operation: $ARGUMENTS

1. **Verify Prerequisites**
   - Check `gws` is installed: `gws --version`
   - Verify authentication: `gws auth status`
   - Review available commands: `gws modelarmor-sanitize-prompt --help`

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
**Original Skill**: `gws-modelarmor-sanitize-prompt`


---


## 💻 Code Examples


### Example 1

```bash
gws modelarmor +sanitize-prompt --template <NAME>
```


### Example 2

```bash
gws modelarmor +sanitize-prompt --template projects/P/locations/L/templates/T --text 'user input'
echo 'prompt' | gws modelarmor +sanitize-prompt --template ...
```


### Example 3

```bash
# List available resources and methods
gws modelarmor-sanitize-prompt --help

# Inspect method schema before calling
gws schema modelarmor-sanitize-prompt.<resource>.<method>

# Execute command with arguments
gws modelarmor-sanitize-prompt $ARGUMENTS
```


---

## 🚀 Usage

**Reference this template:** `@command-google-workspace-modelarmor-sanitize-prompt.md`


**Platform-specific:**
- **GitHub Copilot**: Add to `.github/copilot-instructions.md`
- **Augment Code**: Use `aug context add` command
- **Cursor/Windsurf**: Reference in chat with `@filename`
- **Claude**: Add to Project Knowledge
- **ChatGPT**: Add to Custom GPT configuration
