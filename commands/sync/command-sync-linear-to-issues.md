---
id: command-sync-linear-to-issues
type: command
name: Sync Linear to Issues
description: '---

  allowed-tools: Read, Write, Edit, Bash

  argument-hint: [sync-scope] | --team | --project | --priority | --states

  description: Sync Linear tasks to GitHub issues with state mapping and attachment
  ha...'
category: sync
complexity: medium
keywords:
- github
capabilities: []
token_estimate: 327
---

<!-- Converted from Claude Command Template -->
<!-- Complexity: Medium -->
<!-- Estimated Tokens: ~327 -->


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




# Sync Linear to Issues

> ---
allowed-tools: Read, Write, Edit, Bash
argument-hint: [sync-scope] | --team | --project | --priority | --states
description: Sync Linear tasks to GitHub issues with state mapping and attachment ha...

---
allowed-capabilities: File operations, code editing, terminal access, search
argument-hint: [sync-scope] | --team | --project | --priority | --states
description: Sync Linear tasks to GitHub issues with state mapping and attachment handling
---

# Sync Linear to Issues

Sync Linear tasks to GitHub issues with comprehensive state and field mapping: **$ARGUMENTS**

## Current Linear Context

- Linear teams: Available teams and project assignments
- Task count: Linear task query to determine scope
- Target repository: !`gh repo view --json nameWithOwner -q .nameWithOwner 2>/dev/null || echo "No repo context"`
- User mappings: Linear email to GitHub username correspondence

## Task

Execute comprehensive synchronization of Linear tasks to GitHub issues:

**Sync Scope**: Use $ARGUMENTS to filter by Linear team, project, priority levels, or task states

**Synchronization Framework**:
1. **Task Discovery** - Query Linear tasks with filters, extract metadata, validate requirements, prioritize sync
2. **State Mapping** - Transform Linear states to GitHub equivalents, handle priority conversion, map project assignments
3. **Content Transformation** - Build GitHub issue body, preserve formatting, handle attachments, maintain structure
4. **GitHub Integration** - Create issues with proper labels, assign users, set milestones, manage relationships
5. **Attachment Migration** - Download Linear attachments, upload to GitHub, update references, maintain accessibility
6. **Comment Synchronization** - Transfer comments with attribution, preserve context, handle mentions, maintain threading

**Advanced Features**: Intelligent state mapping, attachment handling, comment threading, user mention translation, comprehensive validation.

**Data Fidelity**: Preserve Linear formatting, maintain task relationships, keep timestamps, ensure reference integrity.

**Output**: Complete synchronization results with created issues, attachment migrations, comment transfers, and comprehensive sync reporting.

---

## 🚀 Usage

**Reference this template:** `@command-sync-linear-to-issues.md`


**Platform-specific:**
- **GitHub Copilot**: Add to `.github/copilot-instructions.md`
- **Augment Code**: Use `aug context add` command
- **Cursor/Windsurf**: Reference in chat with `@filename`
- **Claude**: Add to Project Knowledge
- **ChatGPT**: Add to Custom GPT configuration
