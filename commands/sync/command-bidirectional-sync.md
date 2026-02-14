---
id: command-bidirectional-sync
type: command
name: Bidirectional Sync
description: '---

  allowed-tools: Read, Write, Edit, Bash

  argument-hint: [sync-mode] | --full | --incremental | --dry-run | --conflict-strategy

  description: Enable comprehensive bidirectional GitHub-Linear synchroni...'
category: sync
complexity: medium
keywords:
- api
- audit
- database
- github
- optimization
- performance
capabilities: []
token_estimate: 296
---

<!-- Converted from Claude Command Template -->
<!-- Complexity: Medium -->
<!-- Estimated Tokens: ~296 -->


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




# Bidirectional Sync

> ---
allowed-tools: Read, Write, Edit, Bash
argument-hint: [sync-mode] | --full | --incremental | --dry-run | --conflict-strategy
description: Enable comprehensive bidirectional GitHub-Linear synchroni...

---
allowed-capabilities: File operations, code editing, terminal access, search
argument-hint: [sync-mode] | --full | --incremental | --dry-run | --conflict-strategy
description: Enable comprehensive bidirectional GitHub-Linear synchronization with conflict resolution
---

# Bidirectional Sync

Enable comprehensive bidirectional GitHub-Linear synchronization: **$ARGUMENTS**

## Current Sync Environment

- GitHub status: !`gh api user 2>/dev/null && echo "✓ Authenticated" || echo "⚠ Not authenticated"`
- Linear MCP: Check if Linear MCP server is available and configured
- Sync state: @.sync-state.json or @sync/ (if exists)
- Webhooks: !`gh api repos/{owner}/{repo}/hooks 2>/dev/null | grep -c linear || echo "0"`

## Task

Implement robust bidirectional synchronization between GitHub Issues and Linear tasks:

**Sync Mode**: Use $ARGUMENTS to specify full sync, incremental sync, dry-run preview, or conflict resolution strategy

**Synchronization Framework**:
1. **Sync State Management** - Initialize sync database, track entity relationships, maintain sync history
2. **Conflict Detection** - Identify simultaneous changes, field-level conflicts, timing issues
3. **Resolution Strategies** - NEWER_WINS, GITHUB_WINS, LINEAR_WINS, or intelligent field-level merge
4. **Transaction Management** - Atomic operations, rollback capability, distributed locking
5. **Webhook Integration** - Real-time event handling, sync loop prevention, automated triggers
6. **Data Integrity** - Bidirectional validation, cross-reference maintenance, audit trails

**Advanced Features**: Field-level merge rules, sync loop prevention, webhook automation, performance optimization, comprehensive monitoring.

**Production Ready**: Transaction safety, conflict resolution, error recovery, performance monitoring, comprehensive logging.

**Output**: Complete bidirectional sync system with conflict resolution, webhook integration, performance metrics, and comprehensive sync reporting.

---

## 🚀 Usage

**Reference this template:** `@command-bidirectional-sync.md`


**Platform-specific:**
- **GitHub Copilot**: Add to `.github/copilot-instructions.md`
- **Augment Code**: Use `aug context add` command
- **Cursor/Windsurf**: Reference in chat with `@filename`
- **Claude**: Add to Project Knowledge
- **ChatGPT**: Add to Custom GPT configuration
