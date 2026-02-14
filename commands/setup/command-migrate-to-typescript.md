---
id: command-migrate-to-typescript
type: command
name: Migrate to TypeScript
description: '---

  allowed-tools: Read, Write, Edit, Bash

  argument-hint: [migration-strategy] | --gradual | --complete | --strict | --incremental

  description: Migrate JavaScript project to TypeScript with proper typ...'
category: setup
complexity: medium
keywords:
- javascript
- test
- testing
- typescript
capabilities: []
token_estimate: 306
---

<!-- Converted from Claude Command Template -->
<!-- Complexity: Medium -->
<!-- Estimated Tokens: ~306 -->


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




# Migrate to TypeScript

> ---
allowed-tools: Read, Write, Edit, Bash
argument-hint: [migration-strategy] | --gradual | --complete | --strict | --incremental
description: Migrate JavaScript project to TypeScript with proper typ...

---
allowed-capabilities: File operations, code editing, terminal access, search
argument-hint: [migration-strategy] | --gradual | --complete | --strict | --incremental
description: Migrate JavaScript project to TypeScript with proper typing and tooling setup
---

# Migrate to TypeScript

Migrate JavaScript project to TypeScript with comprehensive type safety: **$ARGUMENTS**

## Current JavaScript State

- Project structure: @package.json (analyze JS/TS mix and dependencies)
- JavaScript files: !`find . -name "*.js" -not -path "./node_modules/*" | wc -l`
- Existing TypeScript: !`find . -name "*.ts" -not -path "./node_modules/*" | wc -l`
- Build system: @webpack.config.js or @vite.config.js or @rollup.config.js

## Task

Systematically migrate JavaScript codebase to TypeScript with proper typing and tooling:

**Migration Strategy**: Use $ARGUMENTS to specify gradual migration, complete conversion, strict mode, or incremental approach

**Migration Process**:
1. **Environment Setup** - TypeScript installation, tsconfig.json configuration, build tool integration
2. **Type Definitions** - Install @types packages, create custom type declarations, define interfaces
3. **File Migration** - Rename .js to .ts/.tsx, add type annotations, resolve compiler errors
4. **Code Transformation** - Convert classes, functions, and modules with proper typing
5. **Error Resolution** - Fix type mismatches, null/undefined handling, strict mode issues
6. **Testing & Validation** - Update test files, configure type checking, validate type coverage

**Advanced Features**: Generic types, mapped types, conditional types, module augmentation, and strict compiler settings.

**Developer Experience**: Configure IDE integration, debugging, linting rules, and team onboarding.

**Output**: Fully typed TypeScript codebase with strict type checking, comprehensive IntelliSense, and improved developer productivity.

---

## 🚀 Usage

**Reference this template:** `@command-migrate-to-typescript.md`


**Platform-specific:**
- **GitHub Copilot**: Add to `.github/copilot-instructions.md`
- **Augment Code**: Use `aug context add` command
- **Cursor/Windsurf**: Reference in chat with `@filename`
- **Claude**: Add to Project Knowledge
- **ChatGPT**: Add to Custom GPT configuration
