---
id: command-google-workspace-slides
type: command
name: Google Workspace Slides
description: '---

  allowed-tools: Bash, Read, Write, Edit

  argument-hint: [resource] [method] [flags]

  description: Google Slides: Read and write presentations.

  ---...'
category: google-workspace
complexity: medium
keywords:
- api
- github
- security
capabilities: []
token_estimate: 559
has_scripts: true
languages:
- bash
---

<!-- Converted from Claude Command Template -->
<!-- Complexity: Medium -->
<!-- Estimated Tokens: ~559 -->


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




# Google Workspace Slides

> ---
allowed-tools: Bash, Read, Write, Edit
argument-hint: [resource] [method] [flags]
description: Google Slides: Read and write presentations.
---...

---
allowed-capabilities: File operations, code editing, terminal access, search
argument-hint: [resource] [method] [flags]
description: Google Slides: Read and write presentations.
---

# Google Workspace Slides

Execute Google Workspace Slides operations: $ARGUMENTS

## Prerequisites

- Google Workspace CLI (`gws`) must be installed
- Authentication configured: Run `gws auth status` to verify
- Review `gws slides --help` for all available commands

## Available Resources and Methods

# slides (v1)

> **PREREQUISITE:** Read `../gws-shared/SKILL.md` for auth, global flags, and security rules. If missing, run `gws generate-skills` to create it.

```bash
gws slides <resource> <method> [flags]
```

## API Resources

### presentations

  - `batchUpdate` — Applies one or more updates to the presentation. Each request is validated before being applied. If any request is not valid, then the entire request will fail and nothing will be applied. Some requests have replies to give you some information about how they are applied. Other requests do not need to return information; these each return an empty reply. The order of replies matches that of the requests.
  - `create` — Creates a blank presentation using the title given in the request. If a `presentationId` is provided, it is used as the ID of the new presentation. Otherwise, a new ID is generated. Other fields in the request, including any provided content, are ignored. Returns the created presentation.
  - `get` — Gets the latest version of the specified presentation.
  - `pages` — Operations on the 'pages' resource

## Discovering Commands

Before calling any API method, inspect it:

```bash
# Browse resources and methods
gws slides --help

# Inspect a method's required params, types, and defaults
gws schema slides.<resource>.<method>
```

Use `gws schema` output to build your `--params` and `--json` flags.

## Usage

```bash
# List available resources and methods
gws slides --help

# Inspect method schema before calling
gws schema slides.<resource>.<method>

# Execute command with arguments
gws slides $ARGUMENTS
```

## Task

Execute the requested Slides operation: $ARGUMENTS

1. **Verify Prerequisites**
   - Check `gws` is installed: `gws --version`
   - Verify authentication: `gws auth status`
   - Review available commands: `gws slides --help`

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
**Original Skill**: `gws-slides`


---


## 💻 Code Examples


### Example 1

```bash
gws slides <resource> <method> [flags]
```


### Example 2

```bash
# Browse resources and methods
gws slides --help

# Inspect a method's required params, types, and defaults
gws schema slides.<resource>.<method>
```


### Example 3

```bash
# List available resources and methods
gws slides --help

# Inspect method schema before calling
gws schema slides.<resource>.<method>

# Execute command with arguments
gws slides $ARGUMENTS
```


---

## 🚀 Usage

**Reference this template:** `@command-google-workspace-slides.md`


**Platform-specific:**
- **GitHub Copilot**: Add to `.github/copilot-instructions.md`
- **Augment Code**: Use `aug context add` command
- **Cursor/Windsurf**: Reference in chat with `@filename`
- **Claude**: Add to Project Knowledge
- **ChatGPT**: Add to Custom GPT configuration
