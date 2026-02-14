---
id: command-update-changelog
type: command
name: Update Changelog
description: '---

  allowed-tools: Read, Write, Edit, Bash

  argument-hint: [version] [change-type] [message] | --added | --changed | --fixed

  description: Add entry to project changelog following Keep a Changelog forma...'
category: project-management
complexity: medium
keywords:
- security
capabilities: []
token_estimate: 221
---

<!-- Converted from Claude Command Template -->
<!-- Complexity: Medium -->
<!-- Estimated Tokens: ~221 -->


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




# Update Changelog

> ---
allowed-tools: Read, Write, Edit, Bash
argument-hint: [version] [change-type] [message] | --added | --changed | --fixed
description: Add entry to project changelog following Keep a Changelog forma...

---
allowed-capabilities: File operations, code editing, terminal access, search
argument-hint: [version] [change-type] [message] | --added | --changed | --fixed
description: Add entry to project changelog following Keep a Changelog format
---

# Update Changelog

Add a new entry to the project's CHANGELOG.md file: **$ARGUMENTS**

## Usage Examples
- `/add-to-changelog 1.1.0 added "New markdown to BlockDoc conversion feature"`
- `/add-to-changelog 1.0.2 fixed "Bug in HTML renderer causing incorrect output"`

## Current Changelog State

- Existing changelog: @CHANGELOG.md (if exists)
- Project version files: @package.json or @setup.py (if exists)

## Task

Add the specified change entry to CHANGELOG.md:

**Arguments**: 
- Version: First argument (e.g., "1.1.0")
- Change Type: Second argument (added/changed/deprecated/removed/fixed/security)  
- Message: Third argument (description of the change)

**Requirements**:
1. Create CHANGELOG.md with standard header if it doesn't exist
2. Find or create version section with today's date
3. Add entry under appropriate change type section
4. Follow Keep a Changelog format and Semantic Versioning
5. Update package version files if this is a new version

The changelog should follow [Keep a Changelog](https://keepachangelog.com/) format.

---

## 🚀 Usage

**Reference this template:** `@command-update-changelog.md`


**Platform-specific:**
- **GitHub Copilot**: Add to `.github/copilot-instructions.md`
- **Augment Code**: Use `aug context add` command
- **Cursor/Windsurf**: Reference in chat with `@filename`
- **Claude**: Add to Project Knowledge
- **ChatGPT**: Add to Custom GPT configuration
