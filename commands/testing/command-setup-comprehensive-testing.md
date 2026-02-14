---
id: command-setup-comprehensive-testing
type: command
name: Setup Comprehensive Testing
description: '---

  allowed-tools: Read, Write, Edit, Bash

  argument-hint: [scope] | --unit | --integration | --e2e | --visual | --performance
  | --full-stack

  description: Setup complete testing infrastructure with fra...'
category: testing
complexity: medium
keywords:
- api
- ci/cd
- github
- gitlab
- java
- optimization
- performance
- python
- security
- test
- testing
capabilities: []
token_estimate: 388
---

<!-- Converted from Claude Command Template -->
<!-- Complexity: Medium -->
<!-- Estimated Tokens: ~388 -->


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




# Setup Comprehensive Testing

> ---
allowed-tools: Read, Write, Edit, Bash
argument-hint: [scope] | --unit | --integration | --e2e | --visual | --performance | --full-stack
description: Setup complete testing infrastructure with fra...

---
allowed-capabilities: File operations, code editing, terminal access, search
argument-hint: [scope] | --unit | --integration | --e2e | --visual | --performance | --full-stack
description: Setup complete testing infrastructure with framework configuration and CI integration
---

# Setup Comprehensive Testing

Setup complete testing infrastructure with multi-layer testing strategy: **$ARGUMENTS**

## Current Testing Infrastructure

- Project type: !`[ -f package.json ] && echo "Node.js" || [ -f requirements.txt ] && echo "Python" || [ -f pom.xml ] && echo "Java" || echo "Multi-language"`
- Existing tests: !`find . -name "*.test.*" -o -name "*.spec.*" | wc -l` test files
- CI system: !`find . -name ".github" -o -name ".gitlab-ci.yml" -o -name "Jenkinsfile" | head -1 || echo "No CI detected"`
- Framework: !`grep -l "jest\\|vitest\\|pytest\\|junit" package.json requirements.txt pom.xml 2>/dev/null | head -1 || echo "Detect framework"`

## Task

Implement comprehensive testing infrastructure with multi-layer testing strategy:

**Setup Scope**: Use $ARGUMENTS to focus on unit, integration, e2e, visual, performance testing, or full-stack implementation

**Comprehensive Testing Framework**:

1. **Testing Strategy Design** - Analyze project requirements, define testing pyramid, plan coverage goals, optimize testing investment
2. **Unit Testing Setup** - Configure primary framework (Jest, Vitest, pytest), setup test runners, implement test utilities, optimize execution
3. **Integration Testing** - Setup integration test framework, configure test databases, implement API testing, optimize test isolation
4. **E2E Testing Configuration** - Setup browser testing (Cypress, Playwright), configure test environments, implement page objects
5. **Visual & Performance Testing** - Setup visual regression testing, configure performance benchmarks, implement accessibility testing
6. **CI/CD Integration** - Configure automated test execution, setup parallel testing, implement quality gates, optimize pipeline performance

**Advanced Features**: Contract testing, chaos engineering, load testing, security testing, cross-browser testing, mobile testing.

**Infrastructure Quality**: Test reliability, execution performance, maintainability, scalability, cost optimization.

**Output**: Complete testing infrastructure with configured frameworks, CI integration, quality metrics, and maintenance workflows.


---

## 🚀 Usage

**Reference this template:** `@command-setup-comprehensive-testing.md`


**Platform-specific:**
- **GitHub Copilot**: Add to `.github/copilot-instructions.md`
- **Augment Code**: Use `aug context add` command
- **Cursor/Windsurf**: Reference in chat with `@filename`
- **Claude**: Add to Project Knowledge
- **ChatGPT**: Add to Custom GPT configuration
