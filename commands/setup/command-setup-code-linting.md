---
id: command-setup-code-linting
type: command
name: Setup Code Linting
description: '---

  allowed-tools: Read, Write, Edit, Bash

  argument-hint: [language] | --javascript | --typescript | --python | --multi-language

  description: Configure comprehensive code linting and quality analysis ...'
category: setup
complexity: medium
keywords:
- ci/cd
- javascript
- optimization
- performance
- python
- security
- typescript
capabilities: []
token_estimate: 305
---

<!-- Converted from Claude Command Template -->
<!-- Complexity: Medium -->
<!-- Estimated Tokens: ~305 -->


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




# Setup Code Linting

> ---
allowed-tools: Read, Write, Edit, Bash
argument-hint: [language] | --javascript | --typescript | --python | --multi-language
description: Configure comprehensive code linting and quality analysis ...

---
allowed-capabilities: File operations, code editing, terminal access, search
argument-hint: [language] | --javascript | --typescript | --python | --multi-language
description: Configure comprehensive code linting and quality analysis tools with automated enforcement
---

# Setup Code Linting

Configure comprehensive code linting and quality analysis: **$ARGUMENTS**

## Current Code Quality State

- Languages detected: !`find . -name "*.js" -o -name "*.ts" -o -name "*.py" -o -name "*.rs" | head -5`
- Existing linters: @.eslintrc.* or @pyproject.toml or @tslint.json
- Package manager: @package.json or @requirements.txt or @Cargo.toml
- Code quality capabilities: File operations, code editing, terminal access, search!`which eslint flake8 pylint mypy clippy 2>/dev/null | wc -l`

## Task

Setup comprehensive code linting system with quality analysis and automated enforcement:

**Language Focus**: Use $ARGUMENTS to configure JavaScript/TypeScript ESLint, Python linting, or multi-language quality analysis

**Linting Configuration**:
1. **Tool Installation** - ESLint, Flake8, Pylint, MyPy, Clippy, language-specific linters and plugins
2. **Rule Configuration** - Code style rules, error detection, best practices, security patterns, performance guidelines
3. **IDE Integration** - Real-time linting, error highlighting, quick fixes, workspace settings
4. **Quality Gates** - Pre-commit validation, CI/CD integration, pull request checks, quality metrics
5. **Custom Rules** - Project-specific patterns, architectural constraints, team conventions
6. **Performance** - Incremental linting, caching strategies, parallel execution, optimization

**Advanced Features**: Security linting, accessibility checks, performance analysis, dependency analysis, code complexity metrics.

**Team Standards**: Shared configurations, style guides, review guidelines, onboarding documentation.

**Output**: Complete linting system with automated quality gates, team standards enforcement, and comprehensive code analysis.

---

## 🚀 Usage

**Reference this template:** `@command-setup-code-linting.md`


**Platform-specific:**
- **GitHub Copilot**: Add to `.github/copilot-instructions.md`
- **Augment Code**: Use `aug context add` command
- **Cursor/Windsurf**: Reference in chat with `@filename`
- **Claude**: Add to Project Knowledge
- **ChatGPT**: Add to Custom GPT configuration
