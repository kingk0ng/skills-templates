---
id: command-issue-to-linear-task
type: command
name: Issue to Linear Task
description: '---

  allowed-tools: Read, Write, Edit, Bash

  argument-hint: [issue-number] | --team | --project | --close-github | --skip-comments

  description: Convert individual GitHub issues to Linear tasks with comp...'
category: sync
complexity: medium
keywords:
- database
- github
capabilities: []
token_estimate: 328
---

<!-- Converted from Claude Command Template -->
<!-- Complexity: Medium -->
<!-- Estimated Tokens: ~328 -->


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




# Issue to Linear Task

> ---
allowed-tools: Read, Write, Edit, Bash
argument-hint: [issue-number] | --team | --project | --close-github | --skip-comments
description: Convert individual GitHub issues to Linear tasks with comp...

---
allowed-capabilities: File operations, code editing, terminal access, search
argument-hint: [issue-number] | --team | --project | --close-github | --skip-comments
description: Convert individual GitHub issues to Linear tasks with comprehensive data preservation
---

# Issue to Linear Task

Convert GitHub issues to Linear tasks with comprehensive field mapping: **$ARGUMENTS**

## Current Conversion Context

- Repository: !`gh repo view --json nameWithOwner -q .nameWithOwner 2>/dev/null || echo "No repo context"`
- Issue details: Based on $ARGUMENTS issue number or selection criteria
- Linear teams: Available Linear teams and project assignments
- User mappings: @user-mappings.json or GitHub-Linear user correspondence

## Task

Execute precise conversion of individual GitHub issues to Linear tasks:

**Issue Target**: Use $ARGUMENTS to specify issue number, conversion options, team assignment, or processing preferences

**Conversion Framework**:
1. **Issue Analysis** - Fetch complete issue data, extract metadata, analyze content structure, infer priorities
2. **Data Transformation** - Map fields accurately, convert formats, preserve relationships, enhance descriptions
3. **Linear Integration** - Create task with proper formatting, assign team/project, set priorities, manage labels
4. **Content Migration** - Import comments with attribution, handle attachments, preserve formatting, maintain threading
5. **Reference Management** - Create bidirectional links, update sync database, maintain cross-references, enable navigation
6. **Validation & Confirmation** - Verify conversion accuracy, confirm field mappings, validate relationships, provide preview

**Advanced Features**: Smart priority inference, intelligent user mapping, attachment handling, comment threading, comprehensive validation.

**Data Fidelity**: Preserve original formatting, maintain all metadata, keep comment attribution, ensure relationship integrity.

**Output**: Successfully converted Linear task with complete data preservation, accurate field mappings, bidirectional references, and comprehensive conversion summary.

---

## 🚀 Usage

**Reference this template:** `@command-issue-to-linear-task.md`


**Platform-specific:**
- **GitHub Copilot**: Add to `.github/copilot-instructions.md`
- **Augment Code**: Use `aug context add` command
- **Cursor/Windsurf**: Reference in chat with `@filename`
- **Claude**: Add to Project Knowledge
- **ChatGPT**: Add to Custom GPT configuration
