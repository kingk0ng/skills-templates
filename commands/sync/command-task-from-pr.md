---
id: command-task-from-pr
type: command
name: Task from PR
description: '---

  allowed-tools: Read, Write, Edit, Bash

  argument-hint: [pr-number] | --team | --estimate | --batch-process | --auto-create

  description: Create Linear tasks from GitHub pull requests with intelligen...'
category: sync
complexity: medium
keywords:
- github
- testing
capabilities: []
token_estimate: 353
---

<!-- Converted from Claude Command Template -->
<!-- Complexity: Medium -->
<!-- Estimated Tokens: ~353 -->


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




# Task from PR

> ---
allowed-tools: Read, Write, Edit, Bash
argument-hint: [pr-number] | --team | --estimate | --batch-process | --auto-create
description: Create Linear tasks from GitHub pull requests with intelligen...

---
allowed-capabilities: File operations, code editing, terminal access, search
argument-hint: [pr-number] | --team | --estimate | --batch-process | --auto-create
description: Create Linear tasks from GitHub pull requests with intelligent content extraction and task sizing
---

# Task from PR

Create Linear tasks from GitHub pull requests with intelligent analysis: **$ARGUMENTS**

## Current PR Environment

- Repository: !`gh repo view --json nameWithOwner -q .nameWithOwner 2>/dev/null || echo "No repo context"`
- PR status: Based on $ARGUMENTS PR number or batch processing criteria
- Linear teams: Available teams for task assignment
- User mappings: GitHub username to Linear user correspondence

## Task

Generate Linear tasks from GitHub pull requests with comprehensive content analysis:

**PR Source**: Use $ARGUMENTS to specify PR number, team assignment, size estimation, or batch processing mode

**Task Generation Framework**:
1. **PR Analysis** - Extract comprehensive PR data, parse description structure, identify key components, analyze changes
2. **Content Extraction** - Parse structured sections, extract checklists, identify technical details, capture requirements
3. **Intelligent Sizing** - Estimate task complexity from code changes, file count, review comments, testing requirements
4. **Task Construction** - Build Linear task with proper formatting, preserve PR context, maintain references, structure content
5. **Team Assignment** - Map to appropriate Linear team, assign based on code areas, set priorities from labels
6. **Validation & Creation** - Check for duplicates, validate task structure, create in Linear, establish bidirectional links

**Advanced Features**: Smart content parsing, automated size estimation, intelligent team mapping, comprehensive validation, batch processing.

**Quality Assurance**: Duplicate detection, content validation, proper formatting, relationship maintenance, comprehensive error handling.

**Output**: Successfully created Linear tasks with comprehensive PR context, accurate sizing estimates, proper team assignments, and complete bidirectional linking.

---

## 🚀 Usage

**Reference this template:** `@command-task-from-pr.md`


**Platform-specific:**
- **GitHub Copilot**: Add to `.github/copilot-instructions.md`
- **Augment Code**: Use `aug context add` command
- **Cursor/Windsurf**: Reference in chat with `@filename`
- **Claude**: Add to Project Knowledge
- **ChatGPT**: Add to Custom GPT configuration
