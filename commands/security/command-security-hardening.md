---
id: command-security-hardening
type: command
name: Security Hardening
description: '---

  allowed-tools: Read, Write, Edit, Bash

  argument-hint: [focus-area] | --headers | --auth | --encryption | --infrastructure

  description: Harden application security configuration with comprehensive ...'
category: security
complexity: medium
keywords:
- audit
- rest
- security
- sql
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




# Security Hardening

> ---
allowed-tools: Read, Write, Edit, Bash
argument-hint: [focus-area] | --headers | --auth | --encryption | --infrastructure
description: Harden application security configuration with comprehensive ...

---
allowed-capabilities: File operations, code editing, terminal access, search
argument-hint: [focus-area] | --headers | --auth | --encryption | --infrastructure
description: Harden application security configuration with comprehensive security controls
---

# Security Hardening

Harden application security configuration and controls: **$ARGUMENTS**

## Current Security Posture

- Framework: @package.json or @requirements.txt or @Cargo.toml (detect framework)
- Security headers: !`curl -I http://localhost:3000 2>/dev/null | grep -i 'x-\|content-security\|strict-transport' || echo "No server running"`
- Environment config: @.env* (check for security-related variables)
- Dependencies: !`npm audit --audit-level=moderate 2>/dev/null || echo "Run dependency audit first"`

## Task

Implement comprehensive security hardening based on security best practices:

**Hardening Focus**: Use $ARGUMENTS to target specific areas or apply comprehensive hardening

**Security Controls**:
1. **Authentication & Authorization** - MFA, RBAC, session security, password policies
2. **Input Validation** - XSS prevention, SQL injection protection, CSRF tokens
3. **Secure Communication** - HTTPS/TLS, HSTS, certificate management
4. **Data Protection** - Encryption at rest/transit, key management, secure storage
5. **Security Headers** - CSP, CORS, security response headers
6. **Infrastructure Security** - Container hardening, network segmentation, monitoring

**Output**: Hardened application with comprehensive security controls, proper configuration, and monitoring capabilities.


---

## 🚀 Usage

**Reference this template:** `@command-security-hardening.md`


**Platform-specific:**
- **GitHub Copilot**: Add to `.github/copilot-instructions.md`
- **Augment Code**: Use `aug context add` command
- **Cursor/Windsurf**: Reference in chat with `@filename`
- **Claude**: Add to Project Knowledge
- **ChatGPT**: Add to Custom GPT configuration
