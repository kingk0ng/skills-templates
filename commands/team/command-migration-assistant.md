---
id: command-migration-assistant
type: command
name: Migration Assistant
description: '---

  allowed-tools: Read, Write, Edit, Bash

  argument-hint: [action] | --plan | --analyze | --migrate | --verify | --rollback

  description: Comprehensive system migration assistance with planning, analys...'
category: team
complexity: medium
keywords:
- api
- audit
- github
- optimization
- performance
- test
- testing
capabilities: []
token_estimate: 336
---

<!-- Converted from Claude Command Template -->
<!-- Complexity: Medium -->
<!-- Estimated Tokens: ~336 -->


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




# Migration Assistant

> ---
allowed-tools: Read, Write, Edit, Bash
argument-hint: [action] | --plan | --analyze | --migrate | --verify | --rollback
description: Comprehensive system migration assistance with planning, analys...

---
allowed-capabilities: File operations, code editing, terminal access, search
argument-hint: [action] | --plan | --analyze | --migrate | --verify | --rollback
description: Comprehensive system migration assistance with planning, analysis, execution, and rollback capabilities
---

# Migration Assistant

Execute comprehensive system migrations with planning, verification, and rollback capabilities: **$ARGUMENTS**

## Current Migration Context

- Source systems: GitHub CLI authentication and API access status
- Target systems: Linear MCP server connectivity and permissions
- Backup storage: Available storage space and backup verification
- Migration scope: Data volume estimation and complexity assessment

## Task

Execute systematic migration process with comprehensive safety measures and validation:

**Migration Action**: Use $ARGUMENTS to specify migration planning, analysis, execution, verification, or rollback operations

**Migration Framework**:
1. **Prerequisites Validation** - Verify GitHub CLI authentication, confirm Linear MCP connectivity, validate permissions, ensure backup storage
2. **Migration Planning** - Assess data volume and complexity, design migration strategy, identify dependencies, create rollback plan
3. **Risk Analysis** - Evaluate potential failure points, assess data integrity risks, identify system dependencies, plan contingency measures
4. **Execution Management** - Implement migration phases, monitor progress and health, handle errors gracefully, maintain audit trails
5. **Verification Process** - Validate data integrity, confirm system functionality, test user workflows, verify performance metrics
6. **Rollback Procedures** - Implement safe rollback mechanisms, restore system state, validate recovery, communicate status updates

**Advanced Features**: Incremental migration support, real-time progress monitoring, automated health checks, comprehensive logging, emergency stop mechanisms.

**Safety Measures**: Multi-point backups, integrity validation, rollback testing, system health monitoring, stakeholder communication.

**Output**: Complete migration execution with progress tracking, validation reports, rollback readiness, and post-migration optimization recommendations.

---

## 🚀 Usage

**Reference this template:** `@command-migration-assistant.md`


**Platform-specific:**
- **GitHub Copilot**: Add to `.github/copilot-instructions.md`
- **Augment Code**: Use `aug context add` command
- **Cursor/Windsurf**: Reference in chat with `@filename`
- **Claude**: Add to Project Knowledge
- **ChatGPT**: Add to Custom GPT configuration
