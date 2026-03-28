---
id: command-google-workspace-meet
type: command
name: Google Workspace Meet
description: '---

  allowed-tools: Bash, Read, Write, Edit

  argument-hint: [resource] [method] [flags]

  description: Manage Google Meet conferences.

  ---...'
category: google-workspace
complexity: medium
keywords:
- api
- github
- security
capabilities: []
token_estimate: 516
has_scripts: true
languages:
- bash
---

<!-- Converted from Claude Command Template -->
<!-- Complexity: Medium -->
<!-- Estimated Tokens: ~516 -->


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




# Google Workspace Meet

> ---
allowed-tools: Bash, Read, Write, Edit
argument-hint: [resource] [method] [flags]
description: Manage Google Meet conferences.
---...

---
allowed-capabilities: File operations, code editing, terminal access, search
argument-hint: [resource] [method] [flags]
description: Manage Google Meet conferences.
---

# Google Workspace Meet

Execute Google Workspace Meet operations: $ARGUMENTS

## Prerequisites

- Google Workspace CLI (`gws`) must be installed
- Authentication configured: Run `gws auth status` to verify
- Review `gws meet --help` for all available commands

## Available Resources and Methods

# meet (v2)

> **PREREQUISITE:** Read `../gws-shared/SKILL.md` for auth, global flags, and security rules. If missing, run `gws generate-skills` to create it.

```bash
gws meet <resource> <method> [flags]
```

## API Resources

### conferenceRecords

  - `get` — Gets a conference record by conference ID.
  - `list` — Lists the conference records. By default, ordered by start time and in descending order.
  - `participants` — Operations on the 'participants' resource
  - `recordings` — Operations on the 'recordings' resource
  - `transcripts` — Operations on the 'transcripts' resource

### spaces

  - `create` — Creates a space.
  - `endActiveConference` — Ends an active conference (if there's one). For an example, see [End active conference](https://developers.google.com/workspace/meet/api/guides/meeting-spaces#end-active-conference).
  - `get` — Gets details about a meeting space. For an example, see [Get a meeting space](https://developers.google.com/workspace/meet/api/guides/meeting-spaces#get-meeting-space).
  - `patch` — Updates details about a meeting space. For an example, see [Update a meeting space](https://developers.google.com/workspace/meet/api/guides/meeting-spaces#update-meeting-space).

## Discovering Commands

Before calling any API method, inspect it:

```bash
# Browse resources and methods
gws meet --help

# Inspect a method's required params, types, and defaults
gws schema meet.<resource>.<method>
```

Use `gws schema` output to build your `--params` and `--json` flags.

## Usage

```bash
# List available resources and methods
gws meet --help

# Inspect method schema before calling
gws schema meet.<resource>.<method>

# Execute command with arguments
gws meet $ARGUMENTS
```

## Task

Execute the requested Meet operation: $ARGUMENTS

1. **Verify Prerequisites**
   - Check `gws` is installed: `gws --version`
   - Verify authentication: `gws auth status`
   - Review available commands: `gws meet --help`

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
**Original Skill**: `gws-meet`


---


## 💻 Code Examples


### Example 1

```bash
gws meet <resource> <method> [flags]
```


### Example 2

```bash
# Browse resources and methods
gws meet --help

# Inspect a method's required params, types, and defaults
gws schema meet.<resource>.<method>
```


### Example 3

```bash
# List available resources and methods
gws meet --help

# Inspect method schema before calling
gws schema meet.<resource>.<method>

# Execute command with arguments
gws meet $ARGUMENTS
```


---

## 🚀 Usage

**Reference this template:** `@command-google-workspace-meet.md`


**Platform-specific:**
- **GitHub Copilot**: Add to `.github/copilot-instructions.md`
- **Augment Code**: Use `aug context add` command
- **Cursor/Windsurf**: Reference in chat with `@filename`
- **Claude**: Add to Project Knowledge
- **ChatGPT**: Add to Custom GPT configuration
