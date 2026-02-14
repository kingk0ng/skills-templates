---
id: command-update-pac-ticket-status
type: command
name: Update PAC Ticket Status
description: '---

  allowed-tools: Read, Write, Edit, Bash

  argument-hint: [ticket-id] | --status | --assignee | --comment

  description: Update PAC ticket status and track progress in Product as Code workflow

  ---...'
category: project-management
complexity: medium
keywords:
- git
capabilities: []
token_estimate: 271
---

<!-- Converted from Claude Command Template -->
<!-- Complexity: Medium -->
<!-- Estimated Tokens: ~271 -->


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




# Update PAC Ticket Status

> ---
allowed-tools: Read, Write, Edit, Bash
argument-hint: [ticket-id] | --status | --assignee | --comment
description: Update PAC ticket status and track progress in Product as Code workflow
---...

---
allowed-capabilities: File operations, code editing, terminal access, search
argument-hint: [ticket-id] | --status | --assignee | --comment
description: Update PAC ticket status and track progress in Product as Code workflow
---

# Update PAC Ticket Status

Update ticket status and track progress in Product as Code workflow: **$ARGUMENTS**

## PAC Environment Check

- PAC directory: !`ls -la .pac/ 2>/dev/null || echo "No .pac directory found"`
- Active tickets: !`find .pac/tickets/ -name "*.yaml" 2>/dev/null | wc -l`
- Recent updates: !`find .pac/tickets/ -name "*.yaml" -mtime -7 2>/dev/null | wc -l`

## Task

Update PAC ticket status and track development progress:

**Arguments**:
- --ticket <ticket-id>: Ticket ID to update (or select interactively)
- --status <status>: New status (backlog/in-progress/review/blocked/done/cancelled)
- --assignee <assignee>: Update assignee
- --comment <comment>: Add progress comment
- --epic <epic-id>: Filter tickets by epic for selection

**Status Update Process**:
1. Validate PAC environment and locate ticket
2. Load current ticket state and validate status transitions
3. Update ticket YAML with new status and timestamp
4. Handle status-specific actions (branch creation, PR suggestions)
5. Update parent epic with ticket progress
6. Generate status update summary with next actions

**Valid Status Transitions**: backlog→in-progress→review→done, with blocked/cancelled as intermediate states.

**Git Integration**: Suggests branch creation for in-progress, PR creation for review, and merge for done status.


---

## 🚀 Usage

**Reference this template:** `@command-update-pac-ticket-status.md`


**Platform-specific:**
- **GitHub Copilot**: Add to `.github/copilot-instructions.md`
- **Augment Code**: Use `aug context add` command
- **Cursor/Windsurf**: Reference in chat with `@filename`
- **Claude**: Add to Project Knowledge
- **ChatGPT**: Add to Custom GPT configuration
