---
id: command-enhanced-ai-mode-for-complex-tasks
type: command
name: Enhanced AI Mode for Complex Tasks
description: Enhanced AI mode for complex tasks...
category: utilities
complexity: medium
keywords:
- ci/cd
- git
- github
capabilities: []
token_estimate: 295
---

<!-- Converted from Claude Command Template -->
<!-- Complexity: Medium -->
<!-- Estimated Tokens: ~295 -->


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




# Enhanced AI Mode for Complex Tasks

> Enhanced AI mode for complex tasks...

# Enhanced AI Mode for Complex Tasks

Enhanced AI mode for complex tasks

*Command originally created by IndyDevDan (YouTube: https://www.youtube.com/@indydevdan) / DislerH (GitHub: https://github.com/disler)*

## Instructions

Initialize a new Claude Code session with comprehensive project context:

1. **Analyze Codebase Structure**
   - Run `git ls-files` to understand file organization and project layout
   - Execute directory tree commands (if available) for visual structure
   - Identify key directories and their purposes
   - Note the technology stack and frameworks in use

2. **Read Project Documentation**
   - Read README.md for project overview and setup instructions
   - Check for any additional documentation in docs/ or ai_docs/
   - Review any CONTRIBUTING.md or development guides
   - Look for architecture or design documents

3. **Understand Project Context**
   - Identify the project's primary purpose and goals
   - Note any special setup requirements or dependencies
   - Check for environment configuration needs
   - Review any CI/CD configuration files

4. **Provide Concise Overview**
   - Summarize the project's purpose in 2-3 sentences
   - List the main technologies and frameworks
   - Highlight any important setup steps
   - Note key areas of the codebase

This command helps establish context quickly when:
- Starting work on a new project
- Returning to a project after time away
- Onboarding new team members
- Preparing for deep technical work

The goal is to "prime" the AI assistant with essential project knowledge for more effective assistance.

---

## 🚀 Usage

**Reference this template:** `@command-enhanced-ai-mode-for-complex-tasks.md`


**Platform-specific:**
- **GitHub Copilot**: Add to `.github/copilot-instructions.md`
- **Augment Code**: Use `aug context add` command
- **Cursor/Windsurf**: Reference in chat with `@filename`
- **Claude**: Add to Project Knowledge
- **ChatGPT**: Add to Custom GPT configuration
