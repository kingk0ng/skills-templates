---
id: command-setup-monorepo
type: command
name: Setup Monorepo
description: '---

  allowed-tools: Read, Write, Edit, Bash

  argument-hint: [monorepo-tool] | --nx | --lerna | --rush | --turborepo | --yarn-workspaces

  description: Configure monorepo project structure with comprehensi...'
category: setup
complexity: medium
keywords:
- ci/cd
- deployment
- optimization
- performance
- testing
capabilities: []
token_estimate: 292
---

<!-- Converted from Claude Command Template -->
<!-- Complexity: Medium -->
<!-- Estimated Tokens: ~292 -->


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




# Setup Monorepo

> ---
allowed-tools: Read, Write, Edit, Bash
argument-hint: [monorepo-tool] | --nx | --lerna | --rush | --turborepo | --yarn-workspaces
description: Configure monorepo project structure with comprehensi...

---
allowed-capabilities: File operations, code editing, terminal access, search
argument-hint: [monorepo-tool] | --nx | --lerna | --rush | --turborepo | --yarn-workspaces
description: Configure monorepo project structure with comprehensive workspace management and build orchestration
---

# Setup Monorepo

Configure comprehensive monorepo structure with advanced workspace management: **$ARGUMENTS**

## Current Project State

- Repository structure: !`find . -maxdepth 2 -type d | head -10`
- Package manager: @package.json or existing workspace configuration
- Existing monorepo: @nx.json or @lerna.json or @rush.json or @turbo.json
- Project count: !`find . -name "package.json" -not -path "./node_modules/*" | wc -l`

## Task

Implement production-ready monorepo with advanced workspace management and build orchestration:

**Monorepo Tool**: Use $ARGUMENTS to configure Nx, Lerna, Rush, Turborepo, or Yarn Workspaces

**Monorepo Architecture**:
1. **Workspace Structure** - Directory organization, package architecture, shared libraries, application separation
2. **Dependency Management** - Workspace dependencies, version management, package hoisting, conflict resolution
3. **Build Orchestration** - Task dependencies, parallel builds, incremental compilation, affected package detection
4. **Development Workflow** - Hot reloading, debugging, testing strategies, development server coordination
5. **CI/CD Integration** - Build pipelines, affected project detection, deployment orchestration, artifact management
6. **Tooling Configuration** - Shared configurations, code quality tools, testing frameworks, documentation

**Advanced Features**: Task caching, distributed execution, performance optimization, plugin ecosystem integration.

**Team Productivity**: Developer experience optimization, onboarding automation, maintenance procedures.

**Output**: Complete monorepo setup with optimized build system, comprehensive tooling, and team productivity enhancements.

---

## 🚀 Usage

**Reference this template:** `@command-setup-monorepo.md`


**Platform-specific:**
- **GitHub Copilot**: Add to `.github/copilot-instructions.md`
- **Augment Code**: Use `aug context add` command
- **Cursor/Windsurf**: Reference in chat with `@filename`
- **Claude**: Add to Project Knowledge
- **ChatGPT**: Add to Custom GPT configuration
