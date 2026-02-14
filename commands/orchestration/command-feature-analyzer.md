---
id: command-feature-analyzer
type: command
name: feature-analyzer
description: '---

  description: "Turn ideas into fully formed designs and specs through natural collaborative
  dialogue. Use before implementing new features or making significant changes."

  argument-hint: Optional fe...'
category: orchestration
complexity: medium
keywords: []
capabilities: []
token_estimate: 148
---

<!-- Converted from Claude Command Template -->
<!-- Complexity: Medium -->
<!-- Estimated Tokens: ~148 -->


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




# feature-analyzer

> ---
description: "Turn ideas into fully formed designs and specs through natural collaborative dialogue. Use before implementing new features or making significant changes."
argument-hint: Optional fe...

---
description: "Turn ideas into fully formed designs and specs through natural collaborative dialogue. Use before implementing new features or making significant changes."
argument-hint: Optional feature description
allowed-capabilities: File operations, code editing, terminal access, searchTodoWrite, AskUserQuestion, Skill, Task
---

## Phase 1: Discovery

**Goal**: Understand what needs to be built

Initial request: $ARGUMENTS

**Actions**:
1. Create todo list with all phases
2. If feature unclear, ask user for:
   - What problem are they solving?
   - What should the feature do?
   - Any constraints or requirements?
3. Summarize understanding and confirm with user

---

## Phase 2: Run with Feature Analyzer Skill

Use the Skill tool to invoke the "feature-design-assistant" skill and follow its complete process.


---

## 🚀 Usage

**Reference this template:** `@command-feature-analyzer.md`


**Platform-specific:**
- **GitHub Copilot**: Add to `.github/copilot-instructions.md`
- **Augment Code**: Use `aug context add` command
- **Cursor/Windsurf**: Reference in chat with `@filename`
- **Claude**: Add to Project Knowledge
- **ChatGPT**: Add to Custom GPT configuration
