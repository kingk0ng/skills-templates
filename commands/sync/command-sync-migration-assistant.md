---
id: command-sync-migration-assistant
type: command
name: Sync Migration Assistant
description: '---

  allowed-tools: Read, Write, Edit, Bash

  argument-hint: [migration-type] | --github-to-linear | --linear-to-github | --bidirectional
  | --validate

  description: Comprehensive migration assistant for l...'
category: sync
complexity: medium
keywords:
- audit
- database
- github
- optimization
- performance
- testing
capabilities: []
token_estimate: 318
---

<!-- Converted from Claude Command Template -->
<!-- Complexity: Medium -->
<!-- Estimated Tokens: ~318 -->


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




# Sync Migration Assistant

> ---
allowed-tools: Read, Write, Edit, Bash
argument-hint: [migration-type] | --github-to-linear | --linear-to-github | --bidirectional | --validate
description: Comprehensive migration assistant for l...

---
allowed-capabilities: File operations, code editing, terminal access, search
argument-hint: [migration-type] | --github-to-linear | --linear-to-github | --bidirectional | --validate
description: Comprehensive migration assistant for large-scale GitHub-Linear data transitions with validation and rollback
---

# Sync Migration Assistant

Execute comprehensive data migration between GitHub and Linear with enterprise-grade capabilities: **$ARGUMENTS**

## Current Migration Environment

- Source system: !`gh --version 2>/dev/null && echo "GitHub CLI available" || echo "GitHub CLI needed"`
- Target system: Linear MCP server connectivity and authentication status
- Migration scope: Analysis of data volume and complexity for planning
- Infrastructure: Database, queue services, and processing capacity assessment

## Task

Implement large-scale data migration with comprehensive validation and enterprise features:

**Migration Type**: Use $ARGUMENTS to specify GitHub-to-Linear, Linear-to-GitHub, bidirectional setup, or validation mode

**Migration Framework**:
1. **Pre-Migration Assessment** - Data volume analysis, dependency mapping, risk assessment, resource planning
2. **Migration Planning** - Phased approach design, rollback strategy, validation checkpoints, timeline estimation
3. **Data Extraction** - Comprehensive data harvesting, relationship preservation, metadata capture, error handling
4. **Transformation Engine** - Field mapping, format conversion, validation rules, data enrichment
5. **Migration Execution** - Batch processing, progress tracking, error recovery, quality assurance
6. **Post-Migration Validation** - Data integrity verification, relationship validation, performance testing, rollback readiness

**Enterprise Features**: Large-scale batch processing, comprehensive error recovery, detailed audit trails, rollback capabilities, performance optimization.

**Quality Assurance**: Multi-stage validation, data integrity checks, relationship verification, comprehensive testing, enterprise monitoring.

**Output**: Complete migration system with phased execution, comprehensive validation, detailed reporting, and enterprise-grade reliability for large-scale data transitions.

---

## 🚀 Usage

**Reference this template:** `@command-sync-migration-assistant.md`


**Platform-specific:**
- **GitHub Copilot**: Add to `.github/copilot-instructions.md`
- **Augment Code**: Use `aug context add` command
- **Cursor/Windsurf**: Reference in chat with `@filename`
- **Claude**: Add to Project Knowledge
- **ChatGPT**: Add to Custom GPT configuration
