---
id: command-file-analysis-tool
type: command
name: File Analysis Tool
description: Perform comprehensive analysis of $ARGUMENTS to identify code quality
  issues, security vulnerabilities, and optimization opportunities....
category: utilities
complexity: medium
keywords:
- angular
- javascript
- optimization
- performance
- react
- security
- test
- testing
- typescript
- vue
- vulnerability
capabilities: []
token_estimate: 297
---

<!-- Converted from Claude Command Template -->
<!-- Complexity: Medium -->
<!-- Estimated Tokens: ~297 -->


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




# File Analysis Tool

> Perform comprehensive analysis of $ARGUMENTS to identify code quality issues, security vulnerabilities, and optimization opportunities....

# File Analysis Tool

Perform comprehensive analysis of $ARGUMENTS to identify code quality issues, security vulnerabilities, and optimization opportunities.

## Task

I'll analyze the specified file and provide detailed insights on:

1. Code quality metrics and maintainability
2. Security vulnerabilities and best practices
3. Performance bottlenecks and optimization opportunities
4. Dependency usage and potential issues
5. TypeScript/JavaScript specific patterns and improvements
6. Test coverage and missing tests

## Process

I'll follow these steps:

1. Read and parse the target file
2. Analyze code structure and complexity
3. Check for security vulnerabilities and anti-patterns  
4. Evaluate performance implications
5. Review dependency usage and imports
6. Provide actionable recommendations for improvement

## Analysis Areas

### Code Quality
- Cyclomatic complexity and maintainability metrics
- Code duplication and refactoring opportunities
- Naming conventions and code organization
- TypeScript type safety and best practices

### Security Assessment
- Input validation and sanitization
- Authentication and authorization patterns
- Sensitive data exposure risks
- Common vulnerability patterns (XSS, injection, etc.)

### Performance Review
- Bundle size impact and optimization opportunities
- Runtime performance bottlenecks
- Memory usage patterns
- Lazy loading and code splitting opportunities

### Best Practices
- Framework-specific patterns (React, Vue, Angular)
- Modern JavaScript/TypeScript features usage
- Error handling and logging practices
- Testing patterns and coverage gaps

I'll provide specific, actionable recommendations tailored to your project's technology stack and architecture.

---

## 🚀 Usage

**Reference this template:** `@command-file-analysis-tool.md`


**Platform-specific:**
- **GitHub Copilot**: Add to `.github/copilot-instructions.md`
- **Augment Code**: Use `aug context add` command
- **Cursor/Windsurf**: Reference in chat with `@filename`
- **Claude**: Add to Project Knowledge
- **ChatGPT**: Add to Custom GPT configuration
