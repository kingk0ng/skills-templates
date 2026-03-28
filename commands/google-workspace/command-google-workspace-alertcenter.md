---
id: command-google-workspace-alertcenter
type: command
name: Google Workspace Alertcenter
description: '---

  allowed-tools: Bash, Read, Write, Edit

  argument-hint: [resource] [method] [flags]

  description: Google Workspace Alert Center: Manage Workspace security alerts.

  ---...'
category: google-workspace
complexity: medium
keywords:
- api
- database
- github
- security
capabilities: []
token_estimate: 642
has_scripts: true
languages:
- bash
---

<!-- Converted from Claude Command Template -->
<!-- Complexity: Medium -->
<!-- Estimated Tokens: ~642 -->


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




# Google Workspace Alertcenter

> ---
allowed-tools: Bash, Read, Write, Edit
argument-hint: [resource] [method] [flags]
description: Google Workspace Alert Center: Manage Workspace security alerts.
---...

---
allowed-capabilities: File operations, code editing, terminal access, search
argument-hint: [resource] [method] [flags]
description: Google Workspace Alert Center: Manage Workspace security alerts.
---

# Google Workspace Alertcenter

Execute Google Workspace Alertcenter operations: $ARGUMENTS

## Prerequisites

- Google Workspace CLI (`gws`) must be installed
- Authentication configured: Run `gws auth status` to verify
- Review `gws alertcenter --help` for all available commands

## Available Resources and Methods

# alertcenter (v1beta1)

> **PREREQUISITE:** Read `../gws-shared/SKILL.md` for auth, global flags, and security rules. If missing, run `gws generate-skills` to create it.

```bash
gws alertcenter <resource> <method> [flags]
```

## API Resources

### alerts

  - `batchDelete` — Performs batch delete operation on alerts.
  - `batchUndelete` — Performs batch undelete operation on alerts.
  - `delete` — Marks the specified alert for deletion. An alert that has been marked for deletion is removed from Alert Center after 30 days. Marking an alert for deletion has no effect on an alert which has already been marked for deletion. Attempting to mark a nonexistent alert for deletion results in a `NOT_FOUND` error.
  - `get` — Gets the specified alert. Attempting to get a nonexistent alert returns `NOT_FOUND` error.
  - `getMetadata` — Returns the metadata of an alert. Attempting to get metadata for a non-existent alert returns `NOT_FOUND` error.
  - `list` — Lists the alerts.
  - `undelete` — Restores, or "undeletes", an alert that was marked for deletion within the past 30 days. Attempting to undelete an alert which was marked for deletion over 30 days ago (which has been removed from the Alert Center database) or a nonexistent alert returns a `NOT_FOUND` error. Attempting to undelete an alert which has not been marked for deletion has no effect.
  - `feedback` — Operations on the 'feedback' resource

### v1beta1

  - `getSettings` — Returns customer-level settings.
  - `updateSettings` — Updates the customer-level settings.

## Discovering Commands

Before calling any API method, inspect it:

```bash
# Browse resources and methods
gws alertcenter --help

# Inspect a method's required params, types, and defaults
gws schema alertcenter.<resource>.<method>
```

Use `gws schema` output to build your `--params` and `--json` flags.

## Usage

```bash
# List available resources and methods
gws alertcenter --help

# Inspect method schema before calling
gws schema alertcenter.<resource>.<method>

# Execute command with arguments
gws alertcenter $ARGUMENTS
```

## Task

Execute the requested Alertcenter operation: $ARGUMENTS

1. **Verify Prerequisites**
   - Check `gws` is installed: `gws --version`
   - Verify authentication: `gws auth status`
   - Review available commands: `gws alertcenter --help`

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
**Original Skill**: `gws-alertcenter`


---


## 💻 Code Examples


### Example 1

```bash
gws alertcenter <resource> <method> [flags]
```


### Example 2

```bash
# Browse resources and methods
gws alertcenter --help

# Inspect a method's required params, types, and defaults
gws schema alertcenter.<resource>.<method>
```


### Example 3

```bash
# List available resources and methods
gws alertcenter --help

# Inspect method schema before calling
gws schema alertcenter.<resource>.<method>

# Execute command with arguments
gws alertcenter $ARGUMENTS
```


---

## 🚀 Usage

**Reference this template:** `@command-google-workspace-alertcenter.md`


**Platform-specific:**
- **GitHub Copilot**: Add to `.github/copilot-instructions.md`
- **Augment Code**: Use `aug context add` command
- **Cursor/Windsurf**: Reference in chat with `@filename`
- **Claude**: Add to Project Knowledge
- **ChatGPT**: Add to Custom GPT configuration
