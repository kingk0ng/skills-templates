---
id: command-git-status-command
type: command
name: Git Status Command
description: Show detailed git repository status...
category: utilities
complexity: medium
keywords:
- git
- github
capabilities: []
token_estimate: 249
---

<!-- Converted from Claude Command Template -->
<!-- Complexity: Medium -->
<!-- Estimated Tokens: ~249 -->


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




# Git Status Command

> Show detailed git repository status...

# Git Status Command

Show detailed git repository status

*Command originally created by IndyDevDan (YouTube: https://www.youtube.com/@indydevdan) / DislerH (GitHub: https://github.com/disler)*

## Instructions

Analyze the current state of the git repository by performing the following steps:

1. **Run Git Status Commands**
   - Execute `git status` to see current working tree state
   - Run `git diff HEAD origin/main` to check differences with remote
   - Execute `git branch --show-current` to display current branch
   - Check for uncommitted changes and untracked files

2. **Analyze Repository State**
   - Identify staged vs unstaged changes
   - List any untracked files
   - Check if branch is ahead/behind remote
   - Review any merge conflicts if present

3. **Read Key Files**
   - Review README.md for project context
   - Check for any recent changes in important files
   - Understand project structure if needed

4. **Provide Summary**
   - Current branch and its relationship to main/master
   - Number of commits ahead/behind
   - List of modified files with change types
   - Any action items (commits needed, pulls required, etc.)

This command helps developers quickly understand:
- What changes are pending
- The repository's sync status
- Whether any actions are needed before continuing work

Arguments: $ARGUMENTS

---

## 🚀 Usage

**Reference this template:** `@command-git-status-command.md`


**Platform-specific:**
- **GitHub Copilot**: Add to `.github/copilot-instructions.md`
- **Augment Code**: Use `aug context add` command
- **Cursor/Windsurf**: Reference in chat with `@filename`
- **Claude**: Add to Project Knowledge
- **ChatGPT**: Add to Custom GPT configuration
