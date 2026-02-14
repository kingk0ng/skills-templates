---
id: command-supabase-backup-manager
type: command
name: Supabase Backup Manager
description: '---

  allowed-tools: Read, Write, Edit, Bash

  argument-hint: [operation] | --backup | --restore | --schedule | --validate | --cleanup

  description: Manage Supabase database backups with automated scheduli...'
category: database
complexity: medium
keywords:
- database
- optimization
- test
- testing
capabilities: []
token_estimate: 362
---

<!-- Converted from Claude Command Template -->
<!-- Complexity: Medium -->
<!-- Estimated Tokens: ~362 -->


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




# Supabase Backup Manager

> ---
allowed-tools: Read, Write, Edit, Bash
argument-hint: [operation] | --backup | --restore | --schedule | --validate | --cleanup
description: Manage Supabase database backups with automated scheduli...

---
allowed-capabilities: File operations, code editing, terminal access, search
argument-hint: [operation] | --backup | --restore | --schedule | --validate | --cleanup
description: Manage Supabase database backups with automated scheduling and recovery procedures
---

# Supabase Backup Manager

Manage comprehensive Supabase database backups with automated scheduling and recovery validation: **$ARGUMENTS**

## Current Backup Context

- Supabase project: MCP integration for backup operations and status monitoring
- Backup storage: Current backup configuration and storage capacity
- Recovery testing: Last backup validation and recovery procedure verification
- Automation status: !`find . -name "*.yml" -o -name "*.json" | xargs grep -l "backup\|cron" 2>/dev/null | head -3` scheduled backup configuration

## Task

Execute comprehensive backup management with automated procedures and recovery validation:

**Backup Operation**: Use $ARGUMENTS to specify backup creation, data restoration, schedule management, backup validation, or cleanup procedures

**Backup Management Framework**:
1. **Backup Strategy** - Design backup schedules, implement retention policies, configure incremental backups, optimize storage usage
2. **Automated Backup** - Create database snapshots, export schema and data, validate backup integrity, monitor backup completion
3. **Recovery Procedures** - Test restore processes, validate data integrity, implement point-in-time recovery, optimize recovery time
4. **Schedule Management** - Configure automated backup schedules, implement backup monitoring, setup failure notifications, optimize backup windows
5. **Storage Optimization** - Manage backup storage, implement compression strategies, archive old backups, monitor storage costs
6. **Disaster Recovery** - Plan disaster recovery procedures, test recovery scenarios, document recovery processes, validate business continuity

**Advanced Features**: Automated backup validation, recovery time optimization, cross-region backup replication, backup encryption, compliance reporting.

**Monitoring Integration**: Backup success monitoring, failure alerting, storage usage tracking, recovery time measurement, compliance reporting.

**Output**: Complete backup management system with automated schedules, recovery procedures, validation reports, and disaster recovery planning.

---

## 🚀 Usage

**Reference this template:** `@command-supabase-backup-manager.md`


**Platform-specific:**
- **GitHub Copilot**: Add to `.github/copilot-instructions.md`
- **Augment Code**: Use `aug context add` command
- **Cursor/Windsurf**: Reference in chat with `@filename`
- **Claude**: Add to Project Knowledge
- **ChatGPT**: Add to Custom GPT configuration
