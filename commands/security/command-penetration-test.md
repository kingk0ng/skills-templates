---
id: command-penetration-test
type: command
name: Penetration Test
description: '---

  allowed-tools: Read, Bash, Grep, Glob

  argument-hint: [target] | --web-app | --api | --auth | --full-scan

  description: Perform penetration testing and vulnerability assessment on application

  ---...'
category: security
complexity: medium
keywords:
- api
- security
- sql
- test
- testing
- vulnerability
capabilities: []
token_estimate: 308
---

<!-- Converted from Claude Command Template -->
<!-- Complexity: Medium -->
<!-- Estimated Tokens: ~308 -->


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




# Penetration Test

> ---
allowed-tools: Read, Bash, Grep, Glob
argument-hint: [target] | --web-app | --api | --auth | --full-scan
description: Perform penetration testing and vulnerability assessment on application
---...

---
allowed-capabilities: File operations, code editing, terminal access, search
argument-hint: [target] | --web-app | --api | --auth | --full-scan
description: Perform penetration testing and vulnerability assessment on application
---

# Penetration Test

Perform penetration testing and vulnerability assessment: **$ARGUMENTS**

## Application Context

- Running services: !`netstat -tlnp 2>/dev/null | grep LISTEN | head -10 || lsof -i -P | grep LISTEN | head -10`
- Web framework: @package.json or @requirements.txt (detect framework and version)
- API endpoints: !`grep -r "route\|endpoint\|@app\\.route\|@RequestMapping" src/ 2>/dev/null | wc -l`
- Authentication: !`grep -r "auth\|login\|jwt\|session" src/ 2>/dev/null | wc -l`

## Task

Conduct systematic penetration testing following ethical hacking methodologies:

**Test Target**: Use $ARGUMENTS to focus on web application, API, authentication, or comprehensive testing

**Testing Phases**:
1. **Reconnaissance** - Service discovery, technology fingerprinting, attack surface mapping
2. **Vulnerability Assessment** - OWASP Top 10, injection flaws, broken authentication
3. **Exploitation Testing** - XSS, CSRF, SQL injection, privilege escalation attempts
4. **Authentication Testing** - Brute force, session management, authorization bypasses
5. **API Security Testing** - Input validation, rate limiting, authentication bypass
6. **Infrastructure Testing** - Network security, container security, configuration issues

**Testing Methodology**:
- Follow OWASP Testing Guide and NIST guidelines
- Use both automated tools and manual testing techniques
- Document all findings with proof-of-concept examples
- Provide remediation recommendations for each vulnerability
- Maintain ethical boundaries and avoid data damage

**Output**: Comprehensive penetration test report with executive summary, detailed findings, risk ratings, and remediation roadmap.

---

## 🚀 Usage

**Reference this template:** `@command-penetration-test.md`


**Platform-specific:**
- **GitHub Copilot**: Add to `.github/copilot-instructions.md`
- **Augment Code**: Use `aug context add` command
- **Cursor/Windsurf**: Reference in chat with `@filename`
- **Claude**: Add to Project Knowledge
- **ChatGPT**: Add to Custom GPT configuration
