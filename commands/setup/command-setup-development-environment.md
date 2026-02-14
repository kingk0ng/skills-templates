---
id: command-setup-development-environment
type: command
name: Setup Development Environment
description: '---

  allowed-tools: Read, Write, Edit, Bash

  argument-hint: [environment-type] | --local | --docker | --cloud | --full-stack

  description: Setup comprehensive development environment with tools, configur...'
category: setup
complexity: medium
keywords:
- ci/cd
- database
- docker
- performance
- python
- testing
capabilities: []
token_estimate: 284
---

<!-- Converted from Claude Command Template -->
<!-- Complexity: Medium -->
<!-- Estimated Tokens: ~284 -->


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




# Setup Development Environment

> ---
allowed-tools: Read, Write, Edit, Bash
argument-hint: [environment-type] | --local | --docker | --cloud | --full-stack
description: Setup comprehensive development environment with tools, configur...

---
allowed-capabilities: File operations, code editing, terminal access, search
argument-hint: [environment-type] | --local | --docker | --cloud | --full-stack
description: Setup comprehensive development environment with tools, configurations, and workflows
---

# Setup Development Environment

Setup comprehensive development environment with modern tooling: **$ARGUMENTS**

## Current Environment State

- Operating system: !`uname -s` and architecture detection
- Development capabilities: File operations, code editing, terminal access, search!`node --version 2>/dev/null || python --version 2>/dev/null || echo "No runtime detected"`
- Package managers: !`which npm yarn pnpm pip poetry cargo 2>/dev/null | wc -l` managers available
- IDE/Editor: Check for VS Code, IntelliJ, or other development environments

## Task

Configure complete development environment with modern tools and best practices:

**Environment Type**: Use $ARGUMENTS to specify local setup, Docker-based, cloud environment, or full-stack development

**Environment Setup**:
1. **Runtime Installation** - Programming languages, package managers, version managers (nvm, pyenv, rustup)
2. **Development Tools** - IDE configuration, extensions, debuggers, profilers, database clients
3. **Build System** - Compilers, bundlers, task runners, CI/CD tools, testing frameworks
4. **Code Quality** - Linting, formatting, pre-commit hooks, code analysis tools
5. **Environment Configuration** - Environment variables, secrets management, configuration files
6. **Team Synchronization** - Shared configurations, documentation, onboarding guides

**Advanced Features**: Hot reloading, debugging configuration, performance monitoring, container orchestration.

**Automation**: Automated setup scripts, configuration management, team environment synchronization.

**Output**: Complete development environment with documented setup process, team configurations, and troubleshooting guides.

---

## 🚀 Usage

**Reference this template:** `@command-setup-development-environment.md`


**Platform-specific:**
- **GitHub Copilot**: Add to `.github/copilot-instructions.md`
- **Augment Code**: Use `aug context add` command
- **Cursor/Windsurf**: Reference in chat with `@filename`
- **Claude**: Add to Project Knowledge
- **ChatGPT**: Add to Custom GPT configuration
