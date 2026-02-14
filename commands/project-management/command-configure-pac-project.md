---
id: command-configure-pac-project
type: command
name: Configure PAC Project
description: '---

  allowed-tools: Read, Write, Edit, Bash

  argument-hint: [project-name] | --minimal | --epic-name | --owner

  description: Initialize Product as Code (PAC) project structure with templates and
  configur...'
category: project-management
complexity: medium
keywords:
- git
capabilities: []
token_estimate: 245
---

<!-- Converted from Claude Command Template -->
<!-- Complexity: Medium -->
<!-- Estimated Tokens: ~245 -->


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




# Configure PAC Project

> ---
allowed-tools: Read, Write, Edit, Bash
argument-hint: [project-name] | --minimal | --epic-name | --owner
description: Initialize Product as Code (PAC) project structure with templates and configur...

---
allowed-capabilities: File operations, code editing, terminal access, search
argument-hint: [project-name] | --minimal | --epic-name | --owner
description: Initialize Product as Code (PAC) project structure with templates and configuration
---

# Configure PAC Project

Initialize Product as Code (PAC) project structure: **$ARGUMENTS**

## Current Project State

- Git status: !`git status --porcelain | wc -l` uncommitted changes
- PAC structure: !`ls -la .pac/ 2>/dev/null | head -5 || echo "No PAC directory"`
- Existing epics: !`find .pac/epics/ -name "*.yaml" 2>/dev/null | wc -l`

## Task

Configure and initialize PAC project structure for version-controlled product management:

**Setup Process**:
1. **Project Analysis** - Validate git repository and analyze existing PAC structure
2. **Directory Creation** - Create `.pac/` structure with epics, tickets, and templates
3. **Configuration Files** - Generate `pac.config.yaml` with project metadata and defaults
4. **Template Creation** - Create epic and ticket templates following PAC v0.1.0 specification
5. **Initial Content** - Create first epic and ticket based on user input
6. **Integration Setup** - Configure git hooks and validation scripts

**Arguments**: Use --minimal for basic structure, --epic-name for initial epic, --owner for product owner.

**Next Steps**: Use `/project:pac-create-epic` and `/project:pac-create-ticket` to manage product development.


---

## 🚀 Usage

**Reference this template:** `@command-configure-pac-project.md`


**Platform-specific:**
- **GitHub Copilot**: Add to `.github/copilot-instructions.md`
- **Augment Code**: Use `aug context add` command
- **Cursor/Windsurf**: Reference in chat with `@filename`
- **Claude**: Add to Project Knowledge
- **ChatGPT**: Add to Custom GPT configuration
