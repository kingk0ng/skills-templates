---
id: command-batch-rename-files
type: command
name: Batch Rename Files
description: '---

  allowed-tools: Bash, Read, Write, Edit

  argument-hint: [task-parameters]

  description: Rename multiple Google Drive files matching a pattern to follow a consistent
  naming convention.

  ---...'
category: google-workspace
complexity: medium
keywords:
- api
- github
capabilities: []
token_estimate: 339
---

<!-- Converted from Claude Command Template -->
<!-- Complexity: Medium -->
<!-- Estimated Tokens: ~339 -->


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




# Batch Rename Files

> ---
allowed-tools: Bash, Read, Write, Edit
argument-hint: [task-parameters]
description: Rename multiple Google Drive files matching a pattern to follow a consistent naming convention.
---...

---
allowed-capabilities: File operations, code editing, terminal access, search
argument-hint: [task-parameters]
description: Rename multiple Google Drive files matching a pattern to follow a consistent naming convention.
---

# Batch Rename Files

Execute Google Workspace workflow: $ARGUMENTS

# Batch Rename Google Drive Files

> **PREREQUISITE:** Load the following skills to execute this recipe: `gws-drive`

Rename multiple Google Drive files matching a pattern to follow a consistent naming convention.

## Steps

1. Find files to rename: `gws drive files list --params '{"q": "name contains '\''Report'\''"}' --format table`
2. Rename a file: `gws drive files update --params '{"fileId": "FILE_ID"}' --json '{"name": "2025-Q1 Report - Final"}'`
3. Verify the rename: `gws drive files get --params '{"fileId": "FILE_ID", "fields": "name"}'`

## Task

Execute this workflow with the following parameters: $ARGUMENTS

1. **Prerequisites Check**
   - Verify `gws` CLI is installed: `gws --version`
   - Confirm authentication: `gws auth status`
   - Load required GWS skills (check PREREQUISITE section above)

2. **Parameter Preparation**
   - Parse task parameters from $ARGUMENTS
   - Validate required inputs
   - Prepare JSON payloads and flags

3. **Execute Workflow Steps**
   - Follow the steps outlined above
   - Replace placeholder IDs with actual values
   - Handle errors and retries
   - Log progress and results

4. **Verify Results**
   - Confirm each step completed successfully
   - Verify changes in Google Workspace
   - Report final status and any issues

## Tips

- Use `--dry-run` flag when available to preview changes
- Always inspect API schemas before calling: `gws schema <service>.<resource>.<method>`
- Check command help for all flags: `gws <service> <resource> <method> --help`

---

**License**: Apache License 2.0
**Source**: [Google Workspace CLI](https://github.com/googleworkspace/cli)
**Original Skill**: `recipe-batch-rename-files`


---

## 🚀 Usage

**Reference this template:** `@command-batch-rename-files.md`


**Platform-specific:**
- **GitHub Copilot**: Add to `.github/copilot-instructions.md`
- **Augment Code**: Use `aug context add` command
- **Cursor/Windsurf**: Reference in chat with `@filename`
- **Claude**: Add to Project Knowledge
- **ChatGPT**: Add to Custom GPT configuration
