---
id: command-create-events-from-sheet
type: command
name: Create Events From Sheet
description: '---

  allowed-tools: Bash, Read, Write, Edit

  argument-hint: [task-parameters]

  description: Read event data from a Google Sheets spreadsheet and create Google
  Calendar entries for each row.

  ---...'
category: google-workspace
complexity: medium
keywords:
- api
- github
capabilities: []
token_estimate: 330
---

<!-- Converted from Claude Command Template -->
<!-- Complexity: Medium -->
<!-- Estimated Tokens: ~330 -->


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




# Create Events From Sheet

> ---
allowed-tools: Bash, Read, Write, Edit
argument-hint: [task-parameters]
description: Read event data from a Google Sheets spreadsheet and create Google Calendar entries for each row.
---...

---
allowed-capabilities: File operations, code editing, terminal access, search
argument-hint: [task-parameters]
description: Read event data from a Google Sheets spreadsheet and create Google Calendar entries for each row.
---

# Create Events From Sheet

Execute Google Workspace workflow: $ARGUMENTS

# Create Google Calendar Events from a Sheet

> **PREREQUISITE:** Load the following skills to execute this recipe: `gws-sheets`, `gws-calendar`

Read event data from a Google Sheets spreadsheet and create Google Calendar entries for each row.

## Steps

1. Read event data: `gws sheets +read --spreadsheet-id SHEET_ID --range 'Events!A2:D'`
2. For each row, create a calendar event: `gws calendar +insert --summary 'Team Standup' --start '2025-01-20T09:00' --duration 30 --attendees alice@company.com,bob@company.com`

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
**Original Skill**: `recipe-create-events-from-sheet`


---

## 🚀 Usage

**Reference this template:** `@command-create-events-from-sheet.md`


**Platform-specific:**
- **GitHub Copilot**: Add to `.github/copilot-instructions.md`
- **Augment Code**: Use `aug context add` command
- **Cursor/Windsurf**: Reference in chat with `@filename`
- **Claude**: Add to Project Knowledge
- **ChatGPT**: Add to Custom GPT configuration
