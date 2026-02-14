---
id: command-project-release
type: command
name: Project Release
description: '---

  allowed-tools: Read, Write, Edit, Bash

  argument-hint: [version-type] | --patch | --minor | --major | --prerelease

  description: Prepare and execute project release with version management and chang...'
category: project-management
complexity: medium
keywords:
- git
capabilities: []
token_estimate: 280
---

<!-- Converted from Claude Command Template -->
<!-- Complexity: Medium -->
<!-- Estimated Tokens: ~280 -->


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




# Project Release

> ---
allowed-tools: Read, Write, Edit, Bash
argument-hint: [version-type] | --patch | --minor | --major | --prerelease
description: Prepare and execute project release with version management and chang...

---
allowed-capabilities: File operations, code editing, terminal access, search
argument-hint: [version-type] | --patch | --minor | --major | --prerelease
description: Prepare and execute project release with version management and changelog updates
---

# Project Release

Update CHANGELOG.md with changes since the last version increase. Check our README.md for any necessary changes. Check the scope of changes since the last release and increase our version number as appropriate: **$ARGUMENTS**

## Current Project State

- Git status: !`git status --porcelain`
- Current version: !`git describe --tags --abbrev=0 2>/dev/null || echo "No previous tags"`
- Recent commits: !`git log --oneline --since="1 month ago" | head -10`
- Package info: @package.json or @setup.py or @Cargo.toml (if exists)

## Task

Prepare a project release following these steps:

1. **Analyze Changes**: Review git history since last release to determine appropriate version increment
2. **Update Version**: Update version in package.json, setup.py, or other version files based on semantic versioning
3. **Update Changelog**: Add new entries to CHANGELOG.md with proper categorization (Added, Changed, Fixed, etc.)
4. **Update Documentation**: Review and update README.md if necessary for new features or changes
5. **Create Release**: Tag the release and prepare release notes

If version type is specified in $ARGUMENTS, use that increment. Otherwise, analyze the changes and suggest appropriate versioning.

Focus on maintaining proper semantic versioning and clear changelog documentation.

---

## 🚀 Usage

**Reference this template:** `@command-project-release.md`


**Platform-specific:**
- **GitHub Copilot**: Add to `.github/copilot-instructions.md`
- **Augment Code**: Use `aug context add` command
- **Cursor/Windsurf**: Reference in chat with `@filename`
- **Claude**: Add to Project Knowledge
- **ChatGPT**: Add to Custom GPT configuration
