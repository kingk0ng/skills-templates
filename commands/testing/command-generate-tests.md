---
id: command-generate-tests
type: command
name: Generate Tests
description: '---

  allowed-tools: Read, Write, Edit, Bash

  argument-hint: [file-path] | [component-name]

  description: Generate comprehensive test suite with unit, integration, and edge
  case coverage

  ---...'
category: testing
complexity: medium
keywords:
- angular
- api
- performance
- react
- test
- testing
- vue
capabilities: []
token_estimate: 514
---

<!-- Converted from Claude Command Template -->
<!-- Complexity: Medium -->
<!-- Estimated Tokens: ~514 -->


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




# Generate Tests

> ---
allowed-tools: Read, Write, Edit, Bash
argument-hint: [file-path] | [component-name]
description: Generate comprehensive test suite with unit, integration, and edge case coverage
---...

---
allowed-capabilities: File operations, code editing, terminal access, search
argument-hint: [file-path] | [component-name]
description: Generate comprehensive test suite with unit, integration, and edge case coverage
---

# Generate Tests

Generate comprehensive test suite for: $ARGUMENTS

## Current Testing Setup

- Test framework: @package.json or @jest.config.js or @vitest.config.js (detect framework)
- Existing tests: !`find . -name "*.test.*" -o -name "*.spec.*" | head -5`
- Test coverage: !`npm run test:coverage 2>/dev/null || echo "No coverage script"`
- Target file: @$ARGUMENTS (if file path provided)

## Task

I'll analyze the target code and create complete test coverage including:

1. Unit tests for individual functions and methods
2. Integration tests for component interactions
3. Edge case and error handling tests
4. Mock implementations for external dependencies
5. Test utilities and helpers as needed
6. Performance and snapshot tests where appropriate

## Process

I'll follow these steps:

1. Analyze the target file/component structure
2. Identify all testable functions, methods, and behaviors
3. Examine existing test patterns in the project
4. Create test files following project naming conventions
5. Implement comprehensive test cases with proper setup/teardown
6. Add necessary mocks and test utilities
7. Verify test coverage and add missing test cases

## Test Types

### Unit Tests

- Individual function testing with various inputs
- Component rendering and prop handling
- State management and lifecycle methods
- Utility function edge cases and error conditions

### Integration Tests

- Component interaction testing
- API integration with mocked responses
- Service layer integration
- End-to-end user workflows

### Framework-Specific Tests

- **React**: Component testing with React Testing Library
- **Vue**: Component testing with Vue Test Utils
- **Angular**: Component and service testing with TestBed
- **Node.js**: API endpoint and middleware testing

## Testing Best Practices

### Test Structure

- Use descriptive test names that explain the behavior
- Follow AAA pattern (Arrange, Act, Assert)
- Group related tests with describe blocks
- Use proper setup and teardown for test isolation

### Mock Strategy

- Mock external dependencies and API calls
- Use factories for test data generation
- Implement proper cleanup for async operations
- Mock timers and dates for deterministic tests

### Coverage Goals

- Aim for 80%+ code coverage
- Focus on critical business logic paths
- Test both happy path and error scenarios
- Include boundary value testing

I'll adapt to your project's testing framework (Jest, Vitest, Cypress, etc.) and follow established patterns.


---

## 🚀 Usage

**Reference this template:** `@command-generate-tests.md`


**Platform-specific:**
- **GitHub Copilot**: Add to `.github/copilot-instructions.md`
- **Augment Code**: Use `aug context add` command
- **Cursor/Windsurf**: Reference in chat with `@filename`
- **Claude**: Add to Project Knowledge
- **ChatGPT**: Add to Custom GPT configuration
