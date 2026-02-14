---
id: command-add-authentication-system
type: command
name: Add Authentication System
description: '---

  allowed-tools: Read, Write, Edit, Bash

  argument-hint: [auth-method] | --oauth | --jwt | --mfa | --passwordless

  description: Implement secure user authentication system with chosen method and
  secur...'
category: security
complexity: medium
keywords:
- api
- database
- security
capabilities: []
token_estimate: 241
---

<!-- Converted from Claude Command Template -->
<!-- Complexity: Medium -->
<!-- Estimated Tokens: ~241 -->


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




# Add Authentication System

> ---
allowed-tools: Read, Write, Edit, Bash
argument-hint: [auth-method] | --oauth | --jwt | --mfa | --passwordless
description: Implement secure user authentication system with chosen method and secur...

---
allowed-capabilities: File operations, code editing, terminal access, search
argument-hint: [auth-method] | --oauth | --jwt | --mfa | --passwordless
description: Implement secure user authentication system with chosen method and security best practices
---

# Add Authentication System

Implement secure user authentication system: **$ARGUMENTS**

## Current Application State

- Framework detection: @package.json or @requirements.txt or @Cargo.toml
- Existing auth: !`grep -r "auth\|login\|jwt\|session" src/ --include="*.js" --include="*.py" --include="*.rs" | wc -l`
- Security config: @.env* (check for auth-related variables)
- Database setup: Check for user models or auth tables

## Task

Implement comprehensive authentication system with security best practices:

**Authentication Methods**: Choose from username/password, OAuth 2.0, JWT, SAML, MFA, or passwordless based on $ARGUMENTS

**Implementation Areas**:
1. **User Management** - Registration, profiles, password policies, account verification
2. **Authentication Flow** - Login/logout, session management, token handling, middleware
3. **Authorization System** - RBAC, permissions, route protection, API security
4. **Security Hardening** - Password hashing, rate limiting, CSRF protection, secure cookies
5. **Integration** - Frontend components, API endpoints, database models, middleware

**Security Standards**: Implement OWASP authentication guidelines, secure session management, and proper error handling.

**Output**: Production-ready authentication system with comprehensive security controls and user-friendly interface.


---

## 🚀 Usage

**Reference this template:** `@command-add-authentication-system.md`


**Platform-specific:**
- **GitHub Copilot**: Add to `.github/copilot-instructions.md`
- **Augment Code**: Use `aug context add` command
- **Cursor/Windsurf**: Reference in chat with `@filename`
- **Claude**: Add to Project Knowledge
- **ChatGPT**: Add to Custom GPT configuration
