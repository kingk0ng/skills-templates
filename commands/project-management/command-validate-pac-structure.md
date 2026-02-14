---
id: command-validate-pac-structure
type: command
name: Validate PAC Structure
description: '---

  allowed-tools: Read, Bash

  argument-hint: [scope] | --file | --epic | --fix | --pre-commit

  description: Validate Product as Code project structure and files for PAC specification
  compliance

  ---...'
category: project-management
complexity: medium
keywords: []
capabilities: []
token_estimate: 262
---

<!-- Converted from Claude Command Template -->
<!-- Complexity: Medium -->
<!-- Estimated Tokens: ~262 -->


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




# Validate PAC Structure

> ---
allowed-tools: Read, Bash
argument-hint: [scope] | --file | --epic | --fix | --pre-commit
description: Validate Product as Code project structure and files for PAC specification compliance
---...

---
allowed-capabilities: File operations, code editing, terminal access, search
argument-hint: [scope] | --file | --epic | --fix | --pre-commit
description: Validate Product as Code project structure and files for PAC specification compliance
---

# Validate PAC Structure

Validate Product as Code project structure and files for PAC specification compliance: **$ARGUMENTS**

## Current PAC State

- PAC directory: !`ls -la .pac/ 2>/dev/null || echo "No .pac directory found"`
- Configuration: @.pac/pac.config.yaml (if exists)
- Epic count: !`find .pac/epics/ -name "*.yaml" 2>/dev/null | wc -l`
- Ticket count: !`find .pac/tickets/ -name "*.yaml" 2>/dev/null | wc -l`

## Task

Comprehensive validation of PAC project structure and specification compliance:

**Validation Scope**: Use $ARGUMENTS for specific files/epics or validate entire PAC structure

**Validation Checks**:
1. **Structure Validation** - Directory structure and required files
2. **Configuration Compliance** - PAC config file format and values
3. **Epic Validation** - YAML syntax, required fields, and spec compliance
4. **Ticket Validation** - Format, metadata, and epic references
5. **Cross-Reference Integrity** - Epic-ticket relationships and dependencies
6. **Data Consistency** - Timestamps, status transitions, and ID uniqueness

**Output**: Detailed validation report with compliance status, issues found, and specific recommendations for fixes. Use --fix to automatically resolve common issues.

**Exit Codes**: 0 (valid), 1 (errors found), 2 (configuration issues)


---

## 🚀 Usage

**Reference this template:** `@command-validate-pac-structure.md`


**Platform-specific:**
- **GitHub Copilot**: Add to `.github/copilot-instructions.md`
- **Augment Code**: Use `aug context add` command
- **Cursor/Windsurf**: Reference in chat with `@filename`
- **Claude**: Add to Project Knowledge
- **ChatGPT**: Add to Custom GPT configuration
