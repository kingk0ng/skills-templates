---
id: command-project-to-linear
type: command
name: Project to Linear
description: '---

  allowed-tools: Read, Bash, Grep, Glob

  argument-hint: [project-description] | --team-id | --create-new | --epic-name

  description: Sync project structure and requirements to Linear workspace with co...'
category: project-management
complexity: medium
keywords:
- test
- testing
capabilities: []
token_estimate: 257
---

<!-- Converted from Claude Command Template -->
<!-- Complexity: Medium -->
<!-- Estimated Tokens: ~257 -->


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




# Project to Linear

> ---
allowed-tools: Read, Bash, Grep, Glob
argument-hint: [project-description] | --team-id | --create-new | --epic-name
description: Sync project structure and requirements to Linear workspace with co...

---
allowed-capabilities: File operations, code editing, terminal access, search
argument-hint: [project-description] | --team-id | --create-new | --epic-name
description: Sync project structure and requirements to Linear workspace with comprehensive task breakdown
---

# Project to Linear

Sync project structure and requirements to Linear workspace: **$ARGUMENTS**

## Linear Integration Status

- Linear MCP: Check if Linear MCP server is configured
- Workspace access: !`echo "Test Linear connection if MCP available"`
- Project context: @README.md or project documentation
- Requirements: Based on $ARGUMENTS analysis

## Task

Analyze project requirements and create comprehensive Linear task structure:

**Project Analysis Process**:
1. **Requirement Analysis** - Parse project description and identify major components
2. **Task Breakdown** - Create hierarchical task structure with epics and subtasks
3. **Dependency Mapping** - Identify task dependencies and critical path
4. **Linear Integration** - Create project, epics, and tasks in Linear workspace
5. **Validation** - Review created structure and provide project overview

**Task Organization**:
- Epic-level features and major components
- Parent tasks for feature areas
- Detailed subtasks with acceptance criteria
- Proper labeling (frontend, backend, testing, documentation)
- Priority and effort estimates
- Timeline and dependency relationships

**Output**: Complete Linear project structure with organized task hierarchy, clear descriptions, and actionable items.


---

## 🚀 Usage

**Reference this template:** `@command-project-to-linear.md`


**Platform-specific:**
- **GitHub Copilot**: Add to `.github/copilot-instructions.md`
- **Augment Code**: Use `aug context add` command
- **Cursor/Windsurf**: Reference in chat with `@filename`
- **Claude**: Add to Project Knowledge
- **ChatGPT**: Add to Custom GPT configuration
