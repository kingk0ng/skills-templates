---
id: command-linear-task-to-issue
type: command
name: Linear Task to Issue
description: '---

  allowed-tools: Read, Write, Edit, Bash

  argument-hint: [task-id] | --repo | --milestone | --close-linear | --skip-attachments

  description: Convert Linear tasks to GitHub issues with relationship pr...'
category: sync
complexity: medium
keywords:
- database
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




# Linear Task to Issue

> ---
allowed-tools: Read, Write, Edit, Bash
argument-hint: [task-id] | --repo | --milestone | --close-linear | --skip-attachments
description: Convert Linear tasks to GitHub issues with relationship pr...

---
allowed-capabilities: File operations, code editing, terminal access, search
argument-hint: [task-id] | --repo | --milestone | --close-linear | --skip-attachments
description: Convert Linear tasks to GitHub issues with relationship preservation and metadata mapping
---

# Linear Task to Issue

Convert Linear tasks to GitHub issues with comprehensive relationship mapping: **$ARGUMENTS**

## Current Task Context

- Task details: Based on $ARGUMENTS task identifier or selection criteria
- Target repository: !`gh repo view --json nameWithOwner -q .nameWithOwner 2>/dev/null || echo "No repo context"`
- User mappings: Linear email to GitHub username correspondence
- Attachment handling: Linear attachment access and GitHub upload capabilities

## Task

Execute precise conversion of Linear tasks to GitHub issues:

**Task Target**: Use $ARGUMENTS to specify task identifier, target repository, milestone mapping, or processing preferences

**Conversion Framework**:
1. **Task Analysis** - Fetch complete Linear task data, extract relationships, analyze content structure, identify priorities
2. **Content Transformation** - Build GitHub issue body, map Linear fields, preserve formatting, handle rich content
3. **GitHub Integration** - Create issue with proper structure, apply labels, assign users, set milestones, manage relationships
4. **Attachment Migration** - Download Linear attachments, upload to GitHub, update references, maintain accessibility
5. **Comment Import** - Transfer comments with attribution, preserve timestamps, maintain context, handle mentions
6. **Cross-Reference Setup** - Create bidirectional links, update Linear task, maintain sync database, enable navigation

**Advanced Features**: Rich content conversion, attachment handling, relationship mapping, user mention translation, comprehensive validation.

**Relationship Management**: Preserve parent-child relationships, maintain team context, map project associations, handle dependencies.

**Output**: Successfully created GitHub issue with complete data migration, accurate field mappings, preserved relationships, and comprehensive conversion report.

---

## 🚀 Usage

**Reference this template:** `@command-linear-task-to-issue.md`


**Platform-specific:**
- **GitHub Copilot**: Add to `.github/copilot-instructions.md`
- **Augment Code**: Use `aug context add` command
- **Cursor/Windsurf**: Reference in chat with `@filename`
- **Claude**: Add to Project Knowledge
- **ChatGPT**: Add to Custom GPT configuration
