---
id: command-setup-docker-containers
type: command
name: Setup Docker Containers
description: '---

  allowed-tools: Read, Write, Edit, Bash

  argument-hint: [environment-type] | --development | --production | --microservices
  | --compose

  description: Setup Docker containerization with multi-stage bu...'
category: setup
complexity: medium
keywords:
- ci/cd
- database
- deployment
- docker
- optimization
- performance
- python
- security
- vulnerability
capabilities: []
token_estimate: 276
---

<!-- Converted from Claude Command Template -->
<!-- Complexity: Medium -->
<!-- Estimated Tokens: ~276 -->


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




# Setup Docker Containers

> ---
allowed-tools: Read, Write, Edit, Bash
argument-hint: [environment-type] | --development | --production | --microservices | --compose
description: Setup Docker containerization with multi-stage bu...

---
allowed-capabilities: File operations, code editing, terminal access, search
argument-hint: [environment-type] | --development | --production | --microservices | --compose
description: Setup Docker containerization with multi-stage builds and development workflows
---

# Setup Docker Containers

Setup comprehensive Docker containerization for development and production: **$ARGUMENTS**

## Current Project State

- Application type: @package.json or @requirements.txt (detect Node.js, Python, etc.)
- Existing Docker: @Dockerfile or @docker-compose.yml (if exists)
- Dependencies: !`find . -name "package-lock.json" -o -name "poetry.lock" -o -name "Pipfile.lock" | wc -l`
- Services needed: Database, cache, message queue detection from configs

## Task

Implement production-ready Docker containerization with optimized builds and development workflows:

**Environment Type**: Use $ARGUMENTS to specify development, production, microservices, or Docker Compose setup

**Containerization Strategy**:
1. **Dockerfile Creation** - Multi-stage builds, layer optimization, security best practices
2. **Development Workflow** - Hot reloading, volume mounts, debugging capabilities
3. **Production Optimization** - Image size reduction, security scanning, health checks
4. **Multi-Service Setup** - Docker Compose, service discovery, networking configuration
5. **CI/CD Integration** - Build automation, registry management, deployment pipelines
6. **Monitoring & Logs** - Container observability, log aggregation, resource monitoring

**Security Features**: Non-root users, minimal base images, vulnerability scanning, secrets management.

**Performance Optimization**: Layer caching, build contexts, multi-platform builds, and resource constraints.

**Output**: Complete Docker setup with optimized containers, development workflows, production deployment, and comprehensive documentation.

---

## 🚀 Usage

**Reference this template:** `@command-setup-docker-containers.md`


**Platform-specific:**
- **GitHub Copilot**: Add to `.github/copilot-instructions.md`
- **Augment Code**: Use `aug context add` command
- **Cursor/Windsurf**: Reference in chat with `@filename`
- **Claude**: Add to Project Knowledge
- **ChatGPT**: Add to Custom GPT configuration
