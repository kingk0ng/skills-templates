---
id: command-supabase-security-audit
type: command
name: Supabase Security Audit
description: '---

  allowed-tools: Read, Write, Edit, Bash

  argument-hint: [audit-scope] | --rls | --permissions | --auth | --api-keys | --comprehensive

  description: Conduct comprehensive Supabase security audit with ...'
category: database
complexity: medium
keywords:
- api
- audit
- optimization
- performance
- security
- test
- testing
- vulnerability
capabilities: []
token_estimate: 371
---

<!-- Converted from Claude Command Template -->
<!-- Complexity: Medium -->
<!-- Estimated Tokens: ~371 -->


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




# Supabase Security Audit

> ---
allowed-tools: Read, Write, Edit, Bash
argument-hint: [audit-scope] | --rls | --permissions | --auth | --api-keys | --comprehensive
description: Conduct comprehensive Supabase security audit with ...

---
allowed-capabilities: File operations, code editing, terminal access, search
argument-hint: [audit-scope] | --rls | --permissions | --auth | --api-keys | --comprehensive
description: Conduct comprehensive Supabase security audit with RLS analysis and vulnerability assessment
---

# Supabase Security Audit

Conduct comprehensive Supabase security audit with RLS policy analysis and vulnerability assessment: **$ARGUMENTS**

## Current Security Context

- Supabase access: MCP integration for security analysis and policy review
- RLS policies: Current Row Level Security implementation and policy effectiveness
- Auth configuration: !`find . -name "*auth*" -o -name "*supabase*" | grep -E "\\.(js|ts|json)$" | head -5` authentication setup
- API security: Current API key management and access control implementation

## Task

Execute comprehensive security audit with vulnerability assessment and policy optimization:

**Audit Scope**: Use $ARGUMENTS to focus on RLS policies, permission analysis, authentication security, API key management, or comprehensive security review

**Security Audit Framework**:
1. **RLS Policy Analysis** - Review Row Level Security policies, test policy effectiveness, identify policy gaps, optimize policy performance
2. **Permission Assessment** - Analyze table permissions, review role-based access, validate permission hierarchies, identify over-privileged access
3. **Authentication Security** - Review auth configuration, analyze JWT security, validate session management, assess multi-factor authentication
4. **API Key Management** - Audit API key usage, review key rotation policies, validate key scoping, assess exposure risks
5. **Data Protection** - Analyze sensitive data handling, review encryption implementation, validate data masking, assess backup security
6. **Vulnerability Scanning** - Identify security vulnerabilities, assess injection risks, review CORS configuration, validate rate limiting

**Advanced Features**: Automated security testing, policy simulation, vulnerability scoring, compliance checking, security monitoring setup.

**Compliance Integration**: GDPR compliance checking, SOC2 requirements validation, security best practices enforcement, audit trail analysis.

**Output**: Comprehensive security audit report with vulnerability assessments, policy recommendations, security improvements, and compliance validation.

---

## 🚀 Usage

**Reference this template:** `@command-supabase-security-audit.md`


**Platform-specific:**
- **GitHub Copilot**: Add to `.github/copilot-instructions.md`
- **Augment Code**: Use `aug context add` command
- **Cursor/Windsurf**: Reference in chat with `@filename`
- **Claude**: Add to Project Knowledge
- **ChatGPT**: Add to Custom GPT configuration
