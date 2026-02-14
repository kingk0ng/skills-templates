---
id: command-create-pac-epic
type: command
name: Create PAC Epic
description: '---

  allowed-tools: Read, Write, Edit, Bash

  argument-hint: [epic-name] | --name | --description | --owner

  description: Create new PAC epic following Product as Code specification

  ---...'
category: project-management
complexity: medium
keywords:
- git
capabilities: []
token_estimate: 271
---

<!-- Converted from Claude Command Template -->
<!-- Complexity: Medium -->
<!-- Estimated Tokens: ~271 -->


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




# Create PAC Epic

> ---
allowed-tools: Read, Write, Edit, Bash
argument-hint: [epic-name] | --name | --description | --owner
description: Create new PAC epic following Product as Code specification
---...

---
allowed-capabilities: File operations, code editing, terminal access, search
argument-hint: [epic-name] | --name | --description | --owner
description: Create new PAC epic following Product as Code specification
---

# Create PAC Epic

Create a new epic following the Product as Code specification with guided workflow: **$ARGUMENTS**

## PAC Configuration Check

- PAC directory: !`ls -la .pac/ 2>/dev/null || echo "No .pac directory found"`
- PAC config: @.pac/pac.config.yaml (if exists)
- Existing epics: !`ls -la .pac/epics/ 2>/dev/null | head -10`

## Task

Create a new Product as Code epic:

**Arguments**: 
- Epic name (required if not using --name flag)
- --name <name>: Epic name
- --description <desc>: Epic description  
- --owner <owner>: Epic owner
- --scope <scope>: Scope definition

**Epic Creation Process**:
1. Validate PAC configuration exists (suggest `/project:pac-configure` if missing)
2. Generate epic ID from name (format: epic-[kebab-case-name])
3. Create epic YAML file following PAC v0.1.0 specification in `.pac/epics/[epic-id].yaml`
4. Include required metadata: id, name, created timestamp, owner
5. Add spec with description, scope, success criteria, constraints, dependencies
6. Create epic directory structure: `.pac/epics/[epic-id]/`
7. Update PAC index if `.pac/index.yaml` exists
8. Create git branch `pac/[epic-id]` if in git repository

If information is missing, prompt user interactively for epic details.

**Next Steps**: Use `/project:pac-create-ticket --epic [epic-id]` to add tickets to this epic.

---

## 🚀 Usage

**Reference this template:** `@command-create-pac-epic.md`


**Platform-specific:**
- **GitHub Copilot**: Add to `.github/copilot-instructions.md`
- **Augment Code**: Use `aug context add` command
- **Cursor/Windsurf**: Reference in chat with `@filename`
- **Claude**: Add to Project Knowledge
- **ChatGPT**: Add to Custom GPT configuration
