---
id: command-generate-test-cases
type: command
name: Generate Test Cases
description: '---

  allowed-tools: Read, Write, Edit, Bash

  argument-hint: [target] | [scope] | --unit | --integration | --edge-cases | --automatic

  description: Generate comprehensive test cases with automatic analysi...'
category: testing
complexity: medium
keywords:
- optimization
- performance
- test
- testing
capabilities: []
token_estimate: 400
---

<!-- Converted from Claude Command Template -->
<!-- Complexity: Medium -->
<!-- Estimated Tokens: ~400 -->


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




# Generate Test Cases

> ---
allowed-tools: Read, Write, Edit, Bash
argument-hint: [target] | [scope] | --unit | --integration | --edge-cases | --automatic
description: Generate comprehensive test cases with automatic analysi...

---
allowed-capabilities: File operations, code editing, terminal access, search
argument-hint: [target] | [scope] | --unit | --integration | --edge-cases | --automatic
description: Generate comprehensive test cases with automatic analysis and coverage optimization
---

# Generate Test Cases

Generate comprehensive test cases with automatic analysis and intelligent coverage: **$ARGUMENTS**

## Current Test Generation Context

- Target code: Analysis of $ARGUMENTS for test case generation requirements
- Test framework: !`find . -name "jest.config.*" -o -name "*.test.*" | head -1 && echo "Jest/Vitest detected" || echo "Detect framework"`
- Code complexity: !`find . -name "*.js" -o -name "*.ts" -o -name "*.py" | xargs wc -l 2>/dev/null | tail -1 | awk '{print $1}' || echo "0"` lines of code
- Existing patterns: !`find . -name "*.test.*" -o -name "*.spec.*" | head -3` test file patterns

## Task

Execute intelligent test case generation with comprehensive coverage and optimization:

**Generation Scope**: Use $ARGUMENTS to specify target file, unit tests, integration tests, edge cases, or automatic comprehensive generation

**Test Case Generation Framework**:

1. **Code Structure Analysis** - Parse function signatures, analyze control flow, identify branching paths, assess complexity metrics
2. **Test Pattern Recognition** - Analyze existing test patterns, identify testing conventions, extract reusable patterns, optimize consistency
3. **Input Space Analysis** - Identify parameter domains, analyze boundary conditions, discover edge cases, evaluate error conditions
4. **Test Case Design** - Generate positive test cases, negative test cases, boundary value tests, equivalence class tests
5. **Mock Strategy Planning** - Identify external dependencies, design mock implementations, create test data factories, optimize test isolation
6. **Coverage Optimization** - Ensure path coverage, optimize test efficiency, eliminate redundancy, maximize testing value

**Advanced Features**: Automatic edge case discovery, intelligent input generation, test data synthesis, coverage gap analysis, performance test generation.

**Quality Assurance**: Test maintainability, execution performance, assertion quality, debugging effectiveness.

**Output**: Comprehensive test case suite with optimized coverage, intelligent mocking, proper assertions, and maintenance guidelines.


---

## 🚀 Usage

**Reference this template:** `@command-generate-test-cases.md`


**Platform-specific:**
- **GitHub Copilot**: Add to `.github/copilot-instructions.md`
- **Augment Code**: Use `aug context add` command
- **Cursor/Windsurf**: Reference in chat with `@filename`
- **Claude**: Add to Project Knowledge
- **ChatGPT**: Add to Custom GPT configuration
