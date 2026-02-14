---
id: command-security-audit
type: command
name: Security Audit
description: '---

  allowed-tools: Read, Bash, Grep, Glob

  argument-hint: [focus-area] | --full

  description: Perform comprehensive security assessment and vulnerability analysis

  ---...'
category: security
complexity: medium
keywords:
- api
- audit
- ci/cd
- deployment
- docker
- git
- github
- rest
- security
- sql
- vulnerability
capabilities: []
token_estimate: 513
---

<!-- Converted from Claude Command Template -->
<!-- Complexity: Medium -->
<!-- Estimated Tokens: ~513 -->


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




# Security Audit

> ---
allowed-tools: Read, Bash, Grep, Glob
argument-hint: [focus-area] | --full
description: Perform comprehensive security assessment and vulnerability analysis
---...

---
allowed-capabilities: File operations, code editing, terminal access, search
argument-hint: [focus-area] | --full
description: Perform comprehensive security assessment and vulnerability analysis
---

# Security Audit

Perform comprehensive security assessment: $ARGUMENTS

## Current Environment

- Dependency scan: !`npm audit --audit-level=moderate 2>/dev/null || pip check 2>/dev/null || echo "No package manager detected"`
- Environment files: @.env* (if exists)
- Security config: @.github/workflows/security.yml or @security/ (if exists)
- Recent commits: !`git log --oneline --grep="security\|fix" -10`

## Task

Perform systematic security audit following these steps:

1. **Environment Setup**
   - Identify the technology stack and framework
   - Check for existing security tools and configurations
   - Review deployment and infrastructure setup

2. **Dependency Security**
   - Scan all dependencies for known vulnerabilities
   - Check for outdated packages with security issues
   - Review dependency sources and integrity
   - Use appropriate capabilities: File operations, code editing, terminal access, search`npm audit`, `pip check`, `cargo audit`, etc.

3. **Authentication & Authorization**
   - Review authentication mechanisms and implementation
   - Check for proper session management
   - Verify authorization controls and access restrictions
   - Examine password policies and storage

4. **Input Validation & Sanitization**
   - Check all user input validation and sanitization
   - Look for SQL injection vulnerabilities
   - Identify potential XSS (Cross-Site Scripting) issues
   - Review file upload security and validation

5. **Data Protection**
   - Identify sensitive data handling practices
   - Check encryption implementation for data at rest and in transit
   - Review data masking and anonymization practices
   - Verify secure communication protocols (HTTPS, TLS)

6. **Secrets Management**
   - Scan for hardcoded secrets, API keys, and passwords
   - Check for proper secrets management practices
   - Review environment variable security
   - Identify exposed configuration files

7. **Error Handling & Logging**
   - Review error messages for information disclosure
   - Check logging practices for security events
   - Verify sensitive data is not logged
   - Assess error handling robustness

8. **Infrastructure Security**
   - Review containerization security (Docker, etc.)
   - Check CI/CD pipeline security
   - Examine cloud configuration and permissions
   - Assess network security configurations

9. **Security Headers & CORS**
   - Check security headers implementation
   - Review CORS configuration
   - Verify CSP (Content Security Policy) settings
   - Examine cookie security attributes

10. **Reporting**
    - Document all findings with severity levels (Critical, High, Medium, Low)
    - Provide specific remediation steps for each issue
    - Include code examples and file references
    - Create an executive summary with key recommendations

Use automated security scanning tools when available and provide manual review for complex security patterns.

---

## 🚀 Usage

**Reference this template:** `@command-security-audit.md`


**Platform-specific:**
- **GitHub Copilot**: Add to `.github/copilot-instructions.md`
- **Augment Code**: Use `aug context add` command
- **Cursor/Windsurf**: Reference in chat with `@filename`
- **Claude**: Add to Project Knowledge
- **ChatGPT**: Add to Custom GPT configuration
