---
id: command-add-mutation-testing
type: command
name: Add Mutation Testing
description: '---

  allowed-tools: Read, Write, Edit, Bash

  argument-hint: [language] | --javascript | --java | --python | --rust | --go | --csharp

  description: Setup comprehensive mutation testing with framework sele...'
category: testing
complexity: medium
keywords:
- ci/cd
- deployment
- github
- gitlab
- go
- java
- javascript
- optimization
- performance
- python
- rust
- test
- testing
- typescript
capabilities: []
token_estimate: 423
---

<!-- Converted from Claude Command Template -->
<!-- Complexity: Medium -->
<!-- Estimated Tokens: ~423 -->


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




# Add Mutation Testing

> ---
allowed-tools: Read, Write, Edit, Bash
argument-hint: [language] | --javascript | --java | --python | --rust | --go | --csharp
description: Setup comprehensive mutation testing with framework sele...

---
allowed-capabilities: File operations, code editing, terminal access, search
argument-hint: [language] | --javascript | --java | --python | --rust | --go | --csharp
description: Setup comprehensive mutation testing with framework selection and CI integration
---

# Add Mutation Testing

Setup mutation testing framework with quality metrics and CI integration: **$ARGUMENTS**

## Current Testing Context

- Language: !`find . -name "*.js" -o -name "*.ts" | head -1 >/dev/null && echo "JavaScript/TypeScript" || find . -name "*.py" | head -1 >/dev/null && echo "Python" || find . -name "*.java" | head -1 >/dev/null && echo "Java" || echo "Multi-language"`
- Test coverage: !`find . -name "coverage" -o -name ".nyc_output" | head -1 || echo "No coverage data"`
- Test framework: !`grep -l "jest\\|mocha\\|pytest\\|junit" package.json pom.xml setup.py 2>/dev/null | head -1 || echo "Detect from tests"`
- CI system: !`find . -name ".github" -o -name ".gitlab-ci.yml" -o -name "Jenkinsfile" | head -1 || echo "No CI detected"`

## Task

Implement comprehensive mutation testing with framework optimization and quality gates:

**Language Focus**: Use $ARGUMENTS to specify JavaScript, Java, Python, Rust, Go, C#, or auto-detect from codebase

**Mutation Testing Framework**:

1. **Tool Selection & Setup** - Choose framework (Stryker, PIT, mutmut, cargo-mutants), install dependencies, configure basic settings, validate installation
2. **Mutation Operator Configuration** - Configure arithmetic operators, relational operators, logical operators, conditional boundaries, statement mutations
3. **Performance Optimization** - Setup parallel execution, configure incremental testing, optimize file filtering, implement caching strategies
4. **Quality Metrics** - Configure mutation score calculation, setup survival analysis, implement threshold enforcement, track effectiveness trends
5. **CI/CD Integration** - Automate execution triggers, configure performance monitoring, setup result reporting, implement deployment gates
6. **Result Analysis** - Setup visualization dashboards, configure surviving mutant analysis, implement remediation workflows, track regression patterns

**Advanced Features**: Selective mutation testing, performance profiling, automated test improvement suggestions, mutation trend analysis, quality gate integration.

**Framework Support**: Language-specific optimizations, tool ecosystem integration, performance tuning, reporting customization.

**Output**: Complete mutation testing setup with configured framework, CI integration, quality thresholds, and analysis workflows.


---

## 🚀 Usage

**Reference this template:** `@command-add-mutation-testing.md`


**Platform-specific:**
- **GitHub Copilot**: Add to `.github/copilot-instructions.md`
- **Augment Code**: Use `aug context add` command
- **Cursor/Windsurf**: Reference in chat with `@filename`
- **Claude**: Add to Project Knowledge
- **ChatGPT**: Add to Custom GPT configuration
