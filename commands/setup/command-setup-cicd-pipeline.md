---
id: command-setup-cicd-pipeline
type: command
name: Setup CI/CD Pipeline
description: '---

  allowed-tools: Read, Write, Edit, Bash

  argument-hint: [platform] | --github-actions | --gitlab-ci | --azure-pipelines |
  --jenkins

  description: Setup comprehensive CI/CD pipeline with automated tes...'
category: setup
complexity: medium
keywords:
- audit
- ci/cd
- deployment
- git
- github
- gitlab
- performance
- security
- test
- testing
- vulnerability
capabilities: []
token_estimate: 289
---

<!-- Converted from Claude Command Template -->
<!-- Complexity: Medium -->
<!-- Estimated Tokens: ~289 -->


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




# Setup CI/CD Pipeline

> ---
allowed-tools: Read, Write, Edit, Bash
argument-hint: [platform] | --github-actions | --gitlab-ci | --azure-pipelines | --jenkins
description: Setup comprehensive CI/CD pipeline with automated tes...

---
allowed-capabilities: File operations, code editing, terminal access, search
argument-hint: [platform] | --github-actions | --gitlab-ci | --azure-pipelines | --jenkins
description: Setup comprehensive CI/CD pipeline with automated testing, deployment, and monitoring
---

# Setup CI/CD Pipeline

Setup comprehensive CI/CD pipeline with automated workflows and deployments: **$ARGUMENTS**

## Current Repository State

- Version control: !`git remote -v | head -1` (GitHub, GitLab, etc.)
- Existing CI: !`find . -name ".github" -o -name ".gitlab-ci.yml" -o -name "azure-pipelines.yml" | wc -l`
- Test framework: @package.json or testing files detection
- Deployment config: @Dockerfile or deployment manifests

## Task

Implement production-ready CI/CD pipeline with comprehensive automation and best practices:

**Platform Choice**: Use $ARGUMENTS to specify GitHub Actions, GitLab CI, Azure Pipelines, or Jenkins

**Pipeline Architecture**:
1. **Build Automation** - Code compilation, dependency installation, artifact creation
2. **Testing Strategy** - Unit tests, integration tests, e2e tests, code coverage reporting
3. **Quality Gates** - Linting, security scanning, vulnerability assessment, code quality metrics
4. **Deployment Automation** - Staging deployment, production deployment, rollback mechanisms
5. **Environment Management** - Infrastructure provisioning, configuration management, secrets handling
6. **Monitoring Integration** - Performance monitoring, error tracking, deployment notifications

**Advanced Features**: Parallel job execution, matrix builds, deployment strategies (blue-green, canary), and multi-environment support.

**Security & Compliance**: Secure credential management, compliance checks, audit trails, and approval workflows.

**Output**: Complete CI/CD pipeline with automated testing, secure deployments, monitoring integration, and comprehensive documentation.

---

## 🚀 Usage

**Reference this template:** `@command-setup-cicd-pipeline.md`


**Platform-specific:**
- **GitHub Copilot**: Add to `.github/copilot-instructions.md`
- **Augment Code**: Use `aug context add` command
- **Cursor/Windsurf**: Reference in chat with `@filename`
- **Claude**: Add to Project Knowledge
- **ChatGPT**: Add to Custom GPT configuration
