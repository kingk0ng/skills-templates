---
id: command-add-property-based-testing
type: command
name: Add Property-Based Testing
description: '---

  allowed-tools: Read, Write, Edit, Bash

  argument-hint: [language] | --javascript | --python | --java | --haskell | --rust
  | --clojure

  description: Implement property-based testing with framework se...'
category: testing
complexity: medium
keywords:
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
token_estimate: 374
---

<!-- Converted from Claude Command Template -->
<!-- Complexity: Medium -->
<!-- Estimated Tokens: ~374 -->


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




# Add Property-Based Testing

> ---
allowed-tools: Read, Write, Edit, Bash
argument-hint: [language] | --javascript | --python | --java | --haskell | --rust | --clojure
description: Implement property-based testing with framework se...

---
allowed-capabilities: File operations, code editing, terminal access, search
argument-hint: [language] | --javascript | --python | --java | --haskell | --rust | --clojure
description: Implement property-based testing with framework selection and invariant identification
---

# Add Property-Based Testing

Implement property-based testing framework with invariant analysis and test generation: **$ARGUMENTS**

## Current Testing Context

- Language: !`find . -name "*.js" -o -name "*.ts" | head -1 >/dev/null && echo "JavaScript/TypeScript" || find . -name "*.py" | head -1 >/dev/null && echo "Python" || echo "Multi-language"`
- Test framework: !`find . -name "jest.config.*" -o -name "pytest.ini" | head -1 || echo "Detect framework"`
- Mathematical functions: Analysis of codebase for property-testable functions
- Business logic: Identification of invariants and properties in domain logic

## Task

Implement comprehensive property-based testing with invariant analysis and automated test generation:

**Language Focus**: Use $ARGUMENTS to specify JavaScript, Python, Java, Haskell, Rust, Clojure, or auto-detect from codebase

**Property-Based Testing Framework**:

1. **Framework Selection** - Choose appropriate tool (fast-check, Hypothesis, QuickCheck, proptest), install dependencies, configure integration
2. **Property Identification** - Analyze mathematical properties, identify business invariants, discover symmetries, evaluate round-trip properties
3. **Generator Design** - Create custom data generators, implement constraint-based generation, design composite generators, optimize generation strategies
4. **Property Implementation** - Write property tests, implement preconditions, design postconditions, create invariant checks
5. **Shrinking Configuration** - Configure test case shrinking, optimize failure minimization, implement custom shrinkers, enhance debugging
6. **Integration & Reporting** - Integrate with existing test suite, configure reporting, setup CI integration, optimize execution performance

**Advanced Features**: Stateful property testing, model-based testing, custom generators, parallel property execution, performance property testing.

**Quality Assurance**: Property completeness analysis, edge case coverage, performance optimization, maintainability assessment.

**Output**: Complete property-based testing setup with identified properties, custom generators, integrated test suite, and performance optimization.


---

## 🚀 Usage

**Reference this template:** `@command-add-property-based-testing.md`


**Platform-specific:**
- **GitHub Copilot**: Add to `.github/copilot-instructions.md`
- **Augment Code**: Use `aug context add` command
- **Cursor/Windsurf**: Reference in chat with `@filename`
- **Claude**: Add to Project Knowledge
- **ChatGPT**: Add to Custom GPT configuration
