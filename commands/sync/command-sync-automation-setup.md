---
id: command-sync-automation-setup
type: command
name: Sync Automation Setup
description: '---

  allowed-tools: Read, Write, Edit, Bash

  argument-hint: [setup-mode] | --full | --webhooks-only | --monitoring | --deploy-target

  description: Setup comprehensive automated synchronization workflows ...'
category: sync
complexity: medium
keywords:
- ci/cd
- database
- deploy
- deployment
- docker
- github
- gitlab
- performance
- security
- test
capabilities: []
token_estimate: 322
---

<!-- Converted from Claude Command Template -->
<!-- Complexity: Medium -->
<!-- Estimated Tokens: ~322 -->


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




# Sync Automation Setup

> ---
allowed-tools: Read, Write, Edit, Bash
argument-hint: [setup-mode] | --full | --webhooks-only | --monitoring | --deploy-target
description: Setup comprehensive automated synchronization workflows ...

---
allowed-capabilities: File operations, code editing, terminal access, search
argument-hint: [setup-mode] | --full | --webhooks-only | --monitoring | --deploy-target
description: Setup comprehensive automated synchronization workflows with monitoring and CI/CD integration
---

# Sync Automation Setup

Setup comprehensive automated synchronization workflows: **$ARGUMENTS**

## Current Infrastructure State

- GitHub CLI: !`gh --version 2>/dev/null && echo "✓ Available" || echo "⚠ Not available"`
- Linear MCP: Check Linear MCP server availability and configuration
- Infrastructure: Docker, webhook endpoints, database connectivity, queue services
- CI/CD: !`find . -name ".github" -o -name ".gitlab-ci.yml" -o -name "azure-pipelines.yml" | wc -l` existing workflows

## Task

Configure production-ready automated synchronization with comprehensive infrastructure:

**Setup Mode**: Use $ARGUMENTS to specify full automation, webhooks-only, monitoring setup, or deployment target

**Automation Framework**:
1. **Prerequisites Setup** - Validate GitHub/Linear access, check infrastructure requirements, configure authentication, test connectivity
2. **Webhook Configuration** - Setup GitHub/Linear webhooks, configure endpoints, implement security, test delivery
3. **CI/CD Integration** - Create GitHub Actions workflows, setup scheduled syncs, implement event handling, configure deployments
4. **Sync Server Deployment** - Configure sync engine, setup queue management, implement error handling, enable monitoring
5. **Database & State Management** - Initialize sync databases, setup schema, configure backups, implement state tracking
6. **Monitoring & Alerting** - Configure dashboards, setup alerts, implement health checks, enable notifications

**Advanced Features**: Real-time webhook processing, intelligent conflict resolution, comprehensive monitoring, scalable infrastructure.

**Production Ready**: High availability setup, comprehensive error handling, performance monitoring, security implementation, automated backups.

**Output**: Complete automation infrastructure with webhook integration, CI/CD workflows, monitoring dashboards, and production deployment capabilities.

---

## 🚀 Usage

**Reference this template:** `@command-sync-automation-setup.md`


**Platform-specific:**
- **GitHub Copilot**: Add to `.github/copilot-instructions.md`
- **Augment Code**: Use `aug context add` command
- **Cursor/Windsurf**: Reference in chat with `@filename`
- **Claude**: Add to Project Knowledge
- **ChatGPT**: Add to Custom GPT configuration
