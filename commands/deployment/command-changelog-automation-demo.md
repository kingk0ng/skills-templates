---
id: command-changelog-automation-demo
type: command
name: Changelog Automation Demo
description: '---

  allowed-tools: Read, Write, Edit, Bash

  argument-hint: [format] | --generate | --validate | --demo

  description: Demonstrate changelog automation features with real examples and validation

  ---...'
category: deployment
complexity: medium
keywords:
- benchmark
- git
- optimization
- performance
- test
- testing
capabilities: []
token_estimate: 248
---

<!-- Converted from Claude Command Template -->
<!-- Complexity: Medium -->
<!-- Estimated Tokens: ~248 -->


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




# Changelog Automation Demo

> ---
allowed-tools: Read, Write, Edit, Bash
argument-hint: [format] | --generate | --validate | --demo
description: Demonstrate changelog automation features with real examples and validation
---...

---
allowed-capabilities: File operations, code editing, terminal access, search
argument-hint: [format] | --generate | --validate | --demo
description: Demonstrate changelog automation features with real examples and validation
---

# Changelog Automation Demo

Demonstrate changelog automation features: $ARGUMENTS

## Current Project State

- Existing changelog: @CHANGELOG.md (if exists)
- Package version: @package.json or @pyproject.toml or @Cargo.toml (if exists)
- Recent commits: !`git log --oneline -10`
- Git tags: !`git tag -l | tail -5`

## Demo Features

### 1. **Changelog Generation Demo**
- Generate sample changelog entries from git commits
- Show different changelog formats (Keep a Changelog, conventional-changelog)
- Demonstrate automatic categorization of changes
- Show version numbering and semantic versioning

### 2. **Format Validation Demo**
- Validate existing changelog format compliance
- Show format inconsistencies and suggestions
- Demonstrate automated formatting fixes
- Show integration with release automation

### 3. **Integration Testing**
- Test changelog automation without affecting main workflow
- Validate changelog generation pipeline
- Test different commit message patterns
- Show error handling and recovery

### 4. **Performance Benchmarking**
- Measure changelog generation speed
- Test with large commit histories
- Show memory usage and optimization
- Benchmark different parsing strategies


---

## 🚀 Usage

**Reference this template:** `@command-changelog-automation-demo.md`


**Platform-specific:**
- **GitHub Copilot**: Add to `.github/copilot-instructions.md`
- **Augment Code**: Use `aug context add` command
- **Cursor/Windsurf**: Reference in chat with `@filename`
- **Claude**: Add to Project Knowledge
- **ChatGPT**: Add to Custom GPT configuration
