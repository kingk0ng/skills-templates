---
id: command-audit-external-sharing
type: command
name: Audit External Sharing
description: '---

  allowed-tools: Bash, Read, Write, Edit

  argument-hint: [task-parameters]

  description: Find and review Google Drive files shared outside the organization.

  ---...'
category: google-workspace
complexity: medium
keywords:
- api
- audit
- github
capabilities: []
token_estimate: 338
---

<!-- Converted from Claude Command Template -->
<!-- Complexity: Medium -->
<!-- Estimated Tokens: ~338 -->


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




# Audit External Sharing

> ---
allowed-tools: Bash, Read, Write, Edit
argument-hint: [task-parameters]
description: Find and review Google Drive files shared outside the organization.
---...

---
allowed-capabilities: File operations, code editing, terminal access, search
argument-hint: [task-parameters]
description: Find and review Google Drive files shared outside the organization.
---

# Audit External Sharing

Execute Google Workspace workflow: $ARGUMENTS

# Audit External Drive Sharing

> **PREREQUISITE:** Load the following skills to execute this recipe: `gws-drive`

Find and review Google Drive files shared outside the organization.

> [!CAUTION]
> Revoking permissions immediately removes access. Confirm with the file owner first.

## Steps

1. List externally shared files: `gws drive files list --params '{"q": "visibility = '\''anyoneWithLink'\''"}'`
2. Check permissions on a file: `gws drive permissions list --params '{"fileId": "FILE_ID"}'`
3. Revoke if needed: `gws drive permissions delete --params '{"fileId": "FILE_ID", "permissionId": "PERM_ID"}'`

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
**Original Skill**: `recipe-audit-external-sharing`


---

## 🚀 Usage

**Reference this template:** `@command-audit-external-sharing.md`


**Platform-specific:**
- **GitHub Copilot**: Add to `.github/copilot-instructions.md`
- **Augment Code**: Use `aug context add` command
- **Cursor/Windsurf**: Reference in chat with `@filename`
- **Claude**: Add to Project Knowledge
- **ChatGPT**: Add to Custom GPT configuration
