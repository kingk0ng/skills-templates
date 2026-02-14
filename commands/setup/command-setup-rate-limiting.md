---
id: command-setup-rate-limiting
type: command
name: Setup Rate Limiting
description: '---

  allowed-tools: Read, Write, Edit, Bash

  argument-hint: [rate-limit-type] | --api | --authentication | --file-upload | --database

  description: Implement comprehensive API rate limiting with advanced...'
category: setup
complexity: medium
keywords:
- api
- database
- optimization
- performance
- security
- testing
capabilities: []
token_estimate: 314
---

<!-- Converted from Claude Command Template -->
<!-- Complexity: Medium -->
<!-- Estimated Tokens: ~314 -->


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




# Setup Rate Limiting

> ---
allowed-tools: Read, Write, Edit, Bash
argument-hint: [rate-limit-type] | --api | --authentication | --file-upload | --database
description: Implement comprehensive API rate limiting with advanced...

---
allowed-capabilities: File operations, code editing, terminal access, search
argument-hint: [rate-limit-type] | --api | --authentication | --file-upload | --database
description: Implement comprehensive API rate limiting with advanced algorithms and user-specific policies
---

# Setup Rate Limiting

Implement comprehensive API rate limiting with advanced control mechanisms: **$ARGUMENTS**

## Current API State

- Framework detection: @package.json or @requirements.txt (Express, FastAPI, Spring Boot, etc.)
- Existing rate limiting: !`grep -r "rate.limit\|throttle\|rateLimit" src/ 2>/dev/null | wc -l`
- Redis availability: !`redis-cli ping 2>/dev/null || echo "Redis not available"`
- API endpoints: !`grep -r "route\|endpoint\|@app\\.route" src/ 2>/dev/null | wc -l`

## Task

Implement production-ready rate limiting system with sophisticated algorithms and user policies:

**Rate Limit Type**: Use $ARGUMENTS to focus on API rate limiting, authentication limiting, file upload controls, or database access limiting

**Rate Limiting Architecture**:
1. **Algorithm Implementation** - Token bucket, sliding window, fixed window, leaky bucket algorithms
2. **User Policies** - Tier-based limits, authenticated vs anonymous, user-specific quotas, IP-based controls
3. **Storage Backend** - Redis integration, distributed rate limiting, persistence strategies, failover mechanisms
4. **Endpoint Configuration** - Per-route limits, method-specific rules, dynamic configuration, A/B testing
5. **Monitoring & Analytics** - Usage tracking, abuse detection, performance metrics, alerting systems
6. **Bypass Mechanisms** - Whitelist management, internal request handling, emergency overrides

**Advanced Features**: Adaptive rate limiting, geo-based controls, API key management, quota systems, abuse prevention.

**Production Readiness**: High availability, performance optimization, security controls, comprehensive monitoring.

**Output**: Complete rate limiting system with intelligent policies, comprehensive monitoring, and advanced abuse prevention capabilities.

---

## 🚀 Usage

**Reference this template:** `@command-setup-rate-limiting.md`


**Platform-specific:**
- **GitHub Copilot**: Add to `.github/copilot-instructions.md`
- **Augment Code**: Use `aug context add` command
- **Cursor/Windsurf**: Reference in chat with `@filename`
- **Claude**: Add to Project Knowledge
- **ChatGPT**: Add to Custom GPT configuration
