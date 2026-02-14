---
id: command-dependency-audit
type: command
name: Dependency Audit
description: '---

  allowed-tools: Read, Bash, Grep

  argument-hint: [scope] | --security | --licenses | --updates | --all

  description: Audit dependencies for security vulnerabilities, license compliance,
  and update re...'
category: security
complexity: medium
keywords:
- audit
- optimization
- performance
- security
- vulnerability
capabilities: []
token_estimate: 235
---

<!-- Converted from Claude Command Template -->
<!-- Complexity: Medium -->
<!-- Estimated Tokens: ~235 -->


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




# Dependency Audit

> ---
allowed-tools: Read, Bash, Grep
argument-hint: [scope] | --security | --licenses | --updates | --all
description: Audit dependencies for security vulnerabilities, license compliance, and update re...

---
allowed-capabilities: File operations, code editing, terminal access, search
argument-hint: [scope] | --security | --licenses | --updates | --all
description: Audit dependencies for security vulnerabilities, license compliance, and update recommendations
---

# Dependency Audit

Audit dependencies for security vulnerabilities and compliance: **$ARGUMENTS**

## Current Dependencies

- Package files: @package.json or @requirements.txt or @Cargo.toml or @pom.xml
- Lock files: @package-lock.json or @poetry.lock or @Cargo.lock
- Security scan: !`npm audit --audit-level=moderate 2>/dev/null || pip check 2>/dev/null || cargo audit 2>/dev/null || echo "No security scanner available"`
- Outdated packages: !`npm outdated 2>/dev/null || pip list --outdated 2>/dev/null || echo "Check manually"`

## Task

Perform comprehensive dependency security and compliance audit:

**Audit Scope**: Use $ARGUMENTS to focus on security, licenses, updates, or complete audit

**Analysis Areas**:
1. **Vulnerability Scanning** - Known CVEs, security advisories, exploit availability
2. **Version Analysis** - Outdated packages, breaking changes, update recommendations
3. **License Compliance** - License compatibility, restrictions, legal obligations
4. **Supply Chain Security** - Package authenticity, maintainer status, suspicious dependencies
5. **Performance Impact** - Bundle size, unused dependencies, optimization opportunities

**Output**: Prioritized security report with critical vulnerabilities, recommended actions, and compliance status.


---

## 🚀 Usage

**Reference this template:** `@command-dependency-audit.md`


**Platform-specific:**
- **GitHub Copilot**: Add to `.github/copilot-instructions.md`
- **Augment Code**: Use `aug context add` command
- **Cursor/Windsurf**: Reference in chat with `@filename`
- **Claude**: Add to Project Knowledge
- **ChatGPT**: Add to Custom GPT configuration
