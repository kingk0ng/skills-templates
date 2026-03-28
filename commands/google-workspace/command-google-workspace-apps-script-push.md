---
id: command-google-workspace-apps-script-push
type: command
name: Google Workspace Apps Script Push
description: '---

  allowed-tools: Bash, Read, Write, Edit

  argument-hint: [resource] [method] [flags]

  description: Google Apps Script: Upload local files to an Apps Script project.

  ---...'
category: google-workspace
complexity: medium
keywords:
- api
- github
- security
capabilities: []
token_estimate: 491
has_scripts: true
languages:
- bash
---

<!-- Converted from Claude Command Template -->
<!-- Complexity: Medium -->
<!-- Estimated Tokens: ~491 -->


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




# Google Workspace Apps Script Push

> ---
allowed-tools: Bash, Read, Write, Edit
argument-hint: [resource] [method] [flags]
description: Google Apps Script: Upload local files to an Apps Script project.
---...

---
allowed-capabilities: File operations, code editing, terminal access, search
argument-hint: [resource] [method] [flags]
description: Google Apps Script: Upload local files to an Apps Script project.
---

# Google Workspace Apps Script Push

Execute Google Workspace Apps Script Push operations: $ARGUMENTS

## Prerequisites

- Google Workspace CLI (`gws`) must be installed
- Authentication configured: Run `gws auth status` to verify
- Review `gws apps-script-push --help` for all available commands

## Available Resources and Methods

# apps-script +push

> **PREREQUISITE:** Read `../gws-shared/SKILL.md` for auth, global flags, and security rules. If missing, run `gws generate-skills` to create it.

Upload local files to an Apps Script project

## Usage

```bash
gws apps-script +push --script <ID>
```

## Flags

| Flag | Required | Default | Description |
|------|----------|---------|-------------|
| `--script` | ✓ | — | Script Project ID |
| `--dir` | — | — | Directory containing script files (defaults to current dir) |

## Examples

```bash
gws script +push --script SCRIPT_ID
gws script +push --script SCRIPT_ID --dir ./src
```

## Tips

- Supports .gs, .js, .html, and appsscript.json files.
- Skips hidden files and node_modules automatically.
- This replaces ALL files in the project.

> [!CAUTION]
> This is a **write** command — confirm with the user before executing.

## See Also

- [gws-shared](../gws-shared/SKILL.md) — Global flags and auth
- [gws-apps-script](../gws-apps-script/SKILL.md) — All manage and execute apps script projects commands

## Usage

```bash
# List available resources and methods
gws apps-script-push --help

# Inspect method schema before calling
gws schema apps-script-push.<resource>.<method>

# Execute command with arguments
gws apps-script-push $ARGUMENTS
```

## Task

Execute the requested Apps Script Push operation: $ARGUMENTS

1. **Verify Prerequisites**
   - Check `gws` is installed: `gws --version`
   - Verify authentication: `gws auth status`
   - Review available commands: `gws apps-script-push --help`

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
**Original Skill**: `gws-apps-script-push`


---


## 💻 Code Examples


### Example 1

```bash
gws apps-script +push --script <ID>
```


### Example 2

```bash
gws script +push --script SCRIPT_ID
gws script +push --script SCRIPT_ID --dir ./src
```


### Example 3

```bash
# List available resources and methods
gws apps-script-push --help

# Inspect method schema before calling
gws schema apps-script-push.<resource>.<method>

# Execute command with arguments
gws apps-script-push $ARGUMENTS
```


---

## 🚀 Usage

**Reference this template:** `@command-google-workspace-apps-script-push.md`


**Platform-specific:**
- **GitHub Copilot**: Add to `.github/copilot-instructions.md`
- **Augment Code**: Use `aug context add` command
- **Cursor/Windsurf**: Reference in chat with `@filename`
- **Claude**: Add to Project Knowledge
- **ChatGPT**: Add to Custom GPT configuration
