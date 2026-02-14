---
id: command-update-dependencies
type: command
name: Update Dependencies
description: '---

  allowed-tools: Read, Write, Edit, Bash

  argument-hint: [update-strategy] | --patch | --minor | --major | --security-only

  description: Update and modernize project dependencies with comprehensive te...'
category: setup
complexity: medium
keywords:
- audit
- performance
- security
- test
- testing
- vulnerability
capabilities: []
token_estimate: 301
---

<!-- Converted from Claude Command Template -->
<!-- Complexity: Medium -->
<!-- Estimated Tokens: ~301 -->


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




# Update Dependencies

> ---
allowed-tools: Read, Write, Edit, Bash
argument-hint: [update-strategy] | --patch | --minor | --major | --security-only
description: Update and modernize project dependencies with comprehensive te...

---
allowed-capabilities: File operations, code editing, terminal access, search
argument-hint: [update-strategy] | --patch | --minor | --major | --security-only
description: Update and modernize project dependencies with comprehensive testing and compatibility checks
---

# Update Dependencies

Update and modernize project dependencies with safety checks: **$ARGUMENTS**

## Current Dependencies State

- Package manager: @package.json or @requirements.txt or @Cargo.toml (detect package manager)
- Outdated packages: !`npm outdated 2>/dev/null || pip list --outdated 2>/dev/null || echo "Manual check needed"`
- Security issues: !`npm audit --audit-level=moderate 2>/dev/null || pip check 2>/dev/null || echo "Run security audit"`
- Lock files: @package-lock.json or @poetry.lock or @Cargo.lock

## Task

Systematically update project dependencies with comprehensive testing and compatibility validation:

**Update Strategy**: Use $ARGUMENTS to specify patch updates, minor updates, major updates, or security-only updates

**Update Process**:
1. **Dependency Analysis** - Audit current versions, identify outdated packages, assess security vulnerabilities
2. **Impact Assessment** - Check changelogs, breaking changes, deprecation warnings, compatibility matrix
3. **Staged Updates** - Apply patch updates first, then minor, finally major versions with testing between stages
4. **Testing & Validation** - Run full test suite, build verification, integration testing, performance checks
5. **Rollback Strategy** - Document changes, create restore points, maintain rollback procedures
6. **Documentation Updates** - Update README, dependencies list, migration guides, team notifications

**Safety Features**: Automated testing between updates, dependency conflict resolution, security vulnerability prioritization.

**Output**: Updated dependency manifest with comprehensive testing results, security audit report, and upgrade documentation.

---

## 🚀 Usage

**Reference this template:** `@command-update-dependencies.md`


**Platform-specific:**
- **GitHub Copilot**: Add to `.github/copilot-instructions.md`
- **Augment Code**: Use `aug context add` command
- **Cursor/Windsurf**: Reference in chat with `@filename`
- **Claude**: Add to Project Knowledge
- **ChatGPT**: Add to Custom GPT configuration
