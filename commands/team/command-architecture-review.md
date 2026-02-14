---
id: command-architecture-review
type: command
name: Architecture Review
description: '---

  allowed-tools: Read, Glob, Grep, Bash

  argument-hint: [scope] | --modules | --patterns | --dependencies | --security

  description: Comprehensive architecture review with design patterns analysis and...'
category: team
complexity: medium
keywords:
- go
- performance
- python
- security
- test
- testing
capabilities: []
token_estimate: 391
---

<!-- Converted from Claude Command Template -->
<!-- Complexity: Medium -->
<!-- Estimated Tokens: ~391 -->


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




# Architecture Review

> ---
allowed-tools: Read, Glob, Grep, Bash
argument-hint: [scope] | --modules | --patterns | --dependencies | --security
description: Comprehensive architecture review with design patterns analysis and...

---
allowed-capabilities: File operations, code editing, terminal access, search
argument-hint: [scope] | --modules | --patterns | --dependencies | --security
description: Comprehensive architecture review with design patterns analysis and improvement recommendations
---

# Architecture Review

Perform comprehensive system architecture analysis and improvement planning: **$ARGUMENTS**

## Current Architecture Context

- Project structure: !`find . -name "*.js" -o -name "*.ts" -o -name "*.py" -o -name "*.go" | head -5 && echo "..."`
- Package dependencies: !`[ -f package.json ] && echo "Node.js project" || [ -f requirements.txt ] && echo "Python project" || [ -f go.mod ] && echo "Go project" || echo "Multiple languages"`
- Testing framework: !`find . -name "*.test.*" -o -name "*spec.*" | head -3 && echo "..." || echo "No test files found"`
- Documentation: !`find . -name "README*" -o -name "*.md" | wc -l` documentation files

## Task

Execute comprehensive architectural analysis with actionable improvement recommendations:

**Review Scope**: Use $ARGUMENTS to focus on specific modules, design patterns, dependency analysis, or security architecture

**Architecture Analysis Framework**:
1. **System Structure Assessment** - Map component hierarchy, identify architectural patterns, analyze module boundaries, assess layered design
2. **Design Pattern Evaluation** - Identify implemented patterns, assess pattern consistency, detect anti-patterns, evaluate pattern effectiveness
3. **Dependency Architecture** - Analyze coupling levels, detect circular dependencies, evaluate dependency injection, assess architectural boundaries
4. **Data Flow Analysis** - Trace information flow, evaluate state management, assess data persistence strategies, validate transformation patterns
5. **Scalability & Performance** - Analyze scaling capabilities, evaluate caching strategies, assess bottlenecks, review resource management
6. **Security Architecture** - Review trust boundaries, assess authentication patterns, analyze authorization flows, evaluate data protection

**Advanced Analysis**: Component testability, configuration management, error handling patterns, monitoring integration, extensibility assessment.

**Quality Assessment**: Code organization, documentation adequacy, team communication patterns, technical debt evaluation.

**Output**: Detailed architecture assessment with specific improvement recommendations, refactoring strategies, and implementation roadmap.

---

## 🚀 Usage

**Reference this template:** `@command-architecture-review.md`


**Platform-specific:**
- **GitHub Copilot**: Add to `.github/copilot-instructions.md`
- **Augment Code**: Use `aug context add` command
- **Cursor/Windsurf**: Reference in chat with `@filename`
- **Claude**: Add to Project Knowledge
- **ChatGPT**: Add to Custom GPT configuration
