---
id: command-secrets-scanner
type: command
name: Secrets Scanner
description: '---

  allowed-tools: Read, Bash, Grep, Glob

  argument-hint: [scope] | --api-keys | --passwords | --certificates | --fix

  description: Scan codebase for exposed secrets, credentials, and sensitive informat...'
category: security
complexity: medium
keywords:
- api
- database
- git
- github
- security
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




# Secrets Scanner

> ---
allowed-tools: Read, Bash, Grep, Glob
argument-hint: [scope] | --api-keys | --passwords | --certificates | --fix
description: Scan codebase for exposed secrets, credentials, and sensitive informat...

---
allowed-capabilities: File operations, code editing, terminal access, search
argument-hint: [scope] | --api-keys | --passwords | --certificates | --fix
description: Scan codebase for exposed secrets, credentials, and sensitive information
---

# Secrets Scanner

Scan codebase for exposed secrets and sensitive information: **$ARGUMENTS**

## Current Repository State

- Git status: !`git status --porcelain | wc -l` uncommitted files
- File types: !`find . -name "*.js" -o -name "*.py" -o -name "*.env*" -o -name "*.yml" | wc -l` scannables
- Recent commits: !`git log --oneline --grep="password\|key\|secret\|token" -5`
- Environment files: @.env* or @config/* (if exists)

## Task

Perform comprehensive secrets detection and remediation across codebase:

**Scan Scope**: Use $ARGUMENTS to focus on API keys, passwords, certificates, or complete scan

**Detection Categories**:
1. **API Keys & Tokens** - GitHub, AWS, Google Cloud, Stripe, third-party services
2. **Database Credentials** - Connection strings, usernames, passwords
3. **Certificates & Keys** - Private keys, SSH keys, SSL certificates
4. **Authentication Secrets** - JWT secrets, session keys, OAuth credentials
5. **Configuration Leaks** - Hardcoded URLs, internal endpoints, debug settings

**Remediation Actions**:
- Identify exposed secrets with file locations and line numbers
- Provide secure alternatives (environment variables, secret management)
- Generate .gitignore entries for sensitive files
- Create secure configuration templates
- Implement secrets management best practices

**Output**: Detailed security report with risk levels, immediate actions, and long-term security improvements.

---

## 🚀 Usage

**Reference this template:** `@command-secrets-scanner.md`


**Platform-specific:**
- **GitHub Copilot**: Add to `.github/copilot-instructions.md`
- **Augment Code**: Use `aug context add` command
- **Cursor/Windsurf**: Reference in chat with `@filename`
- **Claude**: Add to Project Knowledge
- **ChatGPT**: Add to Custom GPT configuration
