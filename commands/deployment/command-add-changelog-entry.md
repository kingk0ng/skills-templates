---
id: command-add-changelog-entry
type: command
name: Add Changelog Entry
description: '---

  allowed-tools: Read, Edit, Write, Bash

  argument-hint: [version] | [entry-type] [description]

  description: Generate and maintain project changelog with Keep a Changelog format

  ---...'
category: deployment
complexity: medium
keywords:
- angular
- api
- git
- github
- security
- test
capabilities: []
token_estimate: 370
has_scripts: true
languages:
- bash
- markdown
---

<!-- Converted from Claude Command Template -->
<!-- Complexity: Medium -->
<!-- Estimated Tokens: ~370 -->


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




# Add Changelog Entry

> ---
allowed-tools: Read, Edit, Write, Bash
argument-hint: [version] | [entry-type] [description]
description: Generate and maintain project changelog with Keep a Changelog format
---...

---
allowed-capabilities: File operations, code editing, terminal access, search
argument-hint: [version] | [entry-type] [description]
description: Generate and maintain project changelog with Keep a Changelog format
---

# Add Changelog Entry

Generate and maintain project changelog: $ARGUMENTS

## Current State

- Existing changelog: @CHANGELOG.md (if exists)
- Recent commits: !`git log --oneline -10`
- Current version: !`git describe --tags --abbrev=0 2>/dev/null || echo "No tags found"`
- Package version: @package.json (if exists)

## Task

1. **Changelog Format (Keep a Changelog)**
   ```markdown
   # Changelog
   
   All notable changes to this project will be documented in this file.
   
   The format is based on [Keep a Changelog](https://keepachangelog.com/en/1.0.0/),
   and this project adheres to [Semantic Versioning](https://semver.org/spec/v2.0.0.html).
   
   ## [Unreleased]
   ### Added
   - New features
   
   ### Changed
   - Changes in existing functionality
   
   ### Deprecated
   - Soon-to-be removed features
   
   ### Removed
   - Removed features
   
   ### Fixed
   - Bug fixes
   
   ### Security
   - Security improvements
   ```

2. **Version Entries**
   ```markdown
   ## [1.2.3] - 2024-01-15
   ### Added
   - User authentication system
   - Dark mode toggle
   - Export functionality for reports
   
   ### Fixed
   - Memory leak in background tasks
   - Timezone handling issues
   ```

3. **Automation Tools**
   ```bash
   # Generate changelog from git commits
   npm install -D conventional-changelog-cli
   npx conventional-changelog -p angular -i CHANGELOG.md -s
   
   # Auto-changelog
   npm install -D auto-changelog
   npx auto-changelog
   ```

4. **Commit Convention**
   ```bash
   # Conventional commits for auto-generation
   feat: add user authentication
   fix: resolve memory leak in tasks
   docs: update API documentation
   style: format code with prettier
   refactor: reorganize user service
   test: add unit tests for auth
   chore: update dependencies
   ```

5. **Integration with Releases**
   - Update changelog before each release
   - Include in release notes
   - Link to GitHub releases
   - Tag versions consistently

Remember to keep entries clear, categorized, and focused on user-facing changes.

---


## 💻 Code Examples


### Example 1

```markdown
# Changelog
   
   All notable changes to this project will be documented in this file.
   
   The format is based on [Keep a Changelog](https://keepachangelog.com/en/1.0.0/),
   and this project adheres to [Semantic Versioning](https://semver.org/spec/v2.0.0.html).
   
   ## [Unreleased]
   ### Added
   - New features
   
   ### Changed
   - Changes in existing functionality
   
   ### Deprecated
   - Soon-to-be removed features
   
   ### Removed
   - Removed features
   
   ### Fixed
   - Bug fixes
   
   ### Security
   - Security improvements
```


### Example 2

```markdown
## [1.2.3] - 2024-01-15
   ### Added
   - User authentication system
   - Dark mode toggle
   - Export functionality for reports
   
   ### Fixed
   - Memory leak in background tasks
   - Timezone handling issues
```


### Example 3

```bash
# Generate changelog from git commits
   npm install -D conventional-changelog-cli
   npx conventional-changelog -p angular -i CHANGELOG.md -s
   
   # Auto-changelog
   npm install -D auto-changelog
   npx auto-changelog
```


### Example 4

```bash
# Conventional commits for auto-generation
   feat: add user authentication
   fix: resolve memory leak in tasks
   docs: update API documentation
   style: format code with prettier
   refactor: reorganize user service
   test: add unit tests for auth
   chore: update dependencies
```


---

## 🚀 Usage

**Reference this template:** `@command-add-changelog-entry.md`


**Platform-specific:**
- **GitHub Copilot**: Add to `.github/copilot-instructions.md`
- **Augment Code**: Use `aug context add` command
- **Cursor/Windsurf**: Reference in chat with `@filename`
- **Claude**: Add to Project Knowledge
- **ChatGPT**: Add to Custom GPT configuration
