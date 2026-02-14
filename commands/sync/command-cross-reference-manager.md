---
id: command-cross-reference-manager
type: command
name: Cross-Reference Manager
description: '---

  allowed-tools: Read, Write, Edit, Bash

  argument-hint: [action] | audit | repair | map | validate | export

  description: Manage cross-platform reference links between GitHub and Linear with
  integrit...'
category: sync
complexity: medium
keywords:
- audit
- database
- github
- test
capabilities: []
token_estimate: 323
---

<!-- Converted from Claude Command Template -->
<!-- Complexity: Medium -->
<!-- Estimated Tokens: ~323 -->


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




# Cross-Reference Manager

> ---
allowed-tools: Read, Write, Edit, Bash
argument-hint: [action] | audit | repair | map | validate | export
description: Manage cross-platform reference links between GitHub and Linear with integrit...

---
allowed-capabilities: File operations, code editing, terminal access, search
argument-hint: [action] | audit | repair | map | validate | export
description: Manage cross-platform reference links between GitHub and Linear with integrity checking
---

# Cross-Reference Manager

Manage comprehensive cross-platform reference links with integrity validation: **$ARGUMENTS**

## Current Reference State

- GitHub CLI: !`gh --version 2>/dev/null && echo "✓ Available" || echo "⚠ Not available"`
- Linear MCP: Check Linear MCP server connectivity and authentication
- Reference database: @.reference-mappings.json or reference state files
- Link integrity: !`find . -name "*sync*" -o -name "*reference*" | wc -l` mapping files found

## Task

Implement comprehensive cross-reference management for GitHub-Linear integration:

**Management Action**: Use $ARGUMENTS to specify audit, repair, mapping, validation, or export operations

**Reference Management Framework**:
1. **Reference Database** - Initialize mapping storage, track bidirectional links, maintain sync history
2. **Integrity Auditing** - Scan cross-references, identify orphaned links, detect mismatches, validate consistency
3. **Smart Repair** - Fix broken references, update outdated links, consolidate duplicates, remove invalid entries
4. **Mapping Visualization** - Display reference networks, show connection health, highlight problems, provide statistics
5. **Deep Validation** - Verify link functionality, test bidirectional navigation, check field consistency, ensure data integrity
6. **Export & Documentation** - Generate mapping reports, create backup files, provide import instructions, maintain audit trails

**Advanced Features**: Automated orphan detection, intelligent reference reconstruction, duplicate consolidation, comprehensive validation.

**Data Protection**: Backup before modifications, transaction-based operations, rollback capabilities, comprehensive logging.

**Output**: Complete reference management system with integrity reports, repair summaries, mapping visualizations, and comprehensive cross-platform link maintenance.

---

## 🚀 Usage

**Reference this template:** `@command-cross-reference-manager.md`


**Platform-specific:**
- **GitHub Copilot**: Add to `.github/copilot-instructions.md`
- **Augment Code**: Use `aug context add` command
- **Cursor/Windsurf**: Reference in chat with `@filename`
- **Claude**: Add to Project Knowledge
- **ChatGPT**: Add to Custom GPT configuration
