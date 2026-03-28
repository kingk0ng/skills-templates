---
id: command-deploy-apps-script
type: command
name: Deploy Apps Script
description: '---

  allowed-tools: Bash, Read, Write, Edit

  argument-hint: [task-parameters]

  description: Push local files to a Google Apps Script project.

  ---...'
category: google-workspace
complexity: medium
keywords:
- api
- deploy
- github
capabilities: []
token_estimate: 344
---

<!-- Converted from Claude Command Template -->
<!-- Complexity: Medium -->
<!-- Estimated Tokens: ~344 -->


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




# Deploy Apps Script

> ---
allowed-tools: Bash, Read, Write, Edit
argument-hint: [task-parameters]
description: Push local files to a Google Apps Script project.
---...

---
allowed-capabilities: File operations, code editing, terminal access, search
argument-hint: [task-parameters]
description: Push local files to a Google Apps Script project.
---

# Deploy Apps Script

Execute Google Workspace workflow: $ARGUMENTS

# Deploy an Apps Script Project

> **PREREQUISITE:** Load the following skills to execute this recipe: `gws-apps-script`

Push local files to a Google Apps Script project.

## Steps

1. List existing projects: `gws apps-script projects list --format table`
2. Get project content: `gws apps-script projects getContent --params '{"scriptId": "SCRIPT_ID"}'`
3. Update content: `gws apps-script projects updateContent --params '{"scriptId": "SCRIPT_ID"}' --json '{"files": [{"name": "Code", "type": "SERVER_JS", "source": "function main() { ... }"}]}'`
4. Create a new version: `gws apps-script projects versions create --params '{"scriptId": "SCRIPT_ID"}' --json '{"description": "v2 release"}'`

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
**Original Skill**: `recipe-deploy-apps-script`


---

## 🚀 Usage

**Reference this template:** `@command-deploy-apps-script.md`


**Platform-specific:**
- **GitHub Copilot**: Add to `.github/copilot-instructions.md`
- **Augment Code**: Use `aug context add` command
- **Cursor/Windsurf**: Reference in chat with `@filename`
- **Claude**: Add to Project Knowledge
- **ChatGPT**: Add to Custom GPT configuration
