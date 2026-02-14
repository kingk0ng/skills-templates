---
id: command-setup-code-formatting
type: command
name: Setup Code Formatting
description: '---

  allowed-tools: Read, Write, Edit, Bash

  argument-hint: [language] | --javascript | --typescript | --python | --multi-language

  description: Configure comprehensive code formatting tools with consist...'
category: setup
complexity: medium
keywords:
- ci/cd
- javascript
- optimization
- performance
- python
- rust
- typescript
capabilities: []
token_estimate: 275
---

<!-- Converted from Claude Command Template -->
<!-- Complexity: Medium -->
<!-- Estimated Tokens: ~275 -->


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




# Setup Code Formatting

> ---
allowed-tools: Read, Write, Edit, Bash
argument-hint: [language] | --javascript | --typescript | --python | --multi-language
description: Configure comprehensive code formatting tools with consist...

---
allowed-capabilities: File operations, code editing, terminal access, search
argument-hint: [language] | --javascript | --typescript | --python | --multi-language
description: Configure comprehensive code formatting tools with consistent style enforcement
---

# Setup Code Formatting

Configure comprehensive code formatting with consistent style enforcement: **$ARGUMENTS**

## Current Project State

- Languages detected: !`find . -name "*.js" -o -name "*.ts" -o -name "*.py" -o -name "*.rs" | head -5`
- Existing formatters: @.prettierrc or @pyproject.toml or @rustfmt.toml
- Package manager: @package.json or @requirements.txt or @Cargo.toml
- IDE config: @.vscode/settings.json or @.editorconfig

## Task

Setup comprehensive code formatting system with automated enforcement and team consistency:

**Language Focus**: Use $ARGUMENTS to configure JavaScript/TypeScript, Python, Rust, or multi-language formatting

**Formatting Setup**:
1. **Tool Installation** - Prettier, Black, rustfmt, language-specific formatters and plugins
2. **Configuration** - Style rules, line length, indentation, quotes, trailing commas, language-specific options
3. **IDE Integration** - Editor extensions, format-on-save, keyboard shortcuts, workspace settings
4. **Automation** - Pre-commit hooks, CI/CD formatting checks, automated formatting scripts
5. **Team Sync** - Shared configurations, style guides, enforcement policies, onboarding documentation
6. **Validation** - Formatting verification, CI integration, team compliance monitoring

**Advanced Features**: Custom rules, framework-specific formatting, performance optimization, incremental formatting.

**Consistency**: Cross-platform compatibility, team standardization, legacy code migration strategies.

**Output**: Complete formatting system with automated enforcement, team configurations, and style compliance monitoring.

---

## 🚀 Usage

**Reference this template:** `@command-setup-code-formatting.md`


**Platform-specific:**
- **GitHub Copilot**: Add to `.github/copilot-instructions.md`
- **Augment Code**: Use `aug context add` command
- **Cursor/Windsurf**: Reference in chat with `@filename`
- **Claude**: Add to Project Knowledge
- **ChatGPT**: Add to Custom GPT configuration
