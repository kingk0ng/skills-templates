---
id: command-supabase-type-generator
type: command
name: Supabase Type Generator
description: '---

  allowed-tools: Read, Write, Edit, Bash

  argument-hint: [generation-scope] | --all-tables | --specific-table | --functions
  | --enums | --views

  description: Generate TypeScript types from Supabase sc...'
category: database
complexity: medium
keywords:
- database
- performance
- test
- testing
- typescript
capabilities: []
token_estimate: 377
---

<!-- Converted from Claude Command Template -->
<!-- Complexity: Medium -->
<!-- Estimated Tokens: ~377 -->


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




# Supabase Type Generator

> ---
allowed-tools: Read, Write, Edit, Bash
argument-hint: [generation-scope] | --all-tables | --specific-table | --functions | --enums | --views
description: Generate TypeScript types from Supabase sc...

---
allowed-capabilities: File operations, code editing, terminal access, search
argument-hint: [generation-scope] | --all-tables | --specific-table | --functions | --enums | --views
description: Generate TypeScript types from Supabase schema with automatic synchronization and validation
---

# Supabase Type Generator

Generate comprehensive TypeScript types from Supabase schema with automatic synchronization: **$ARGUMENTS**

## Current Type Context

- Supabase schema: Database schema accessible via MCP integration
- Type definitions: !`find . -name "types" -type d -o -name "*.d.ts" | head -5` existing TypeScript definitions
- Application usage: !`find . -name "*.ts" -o -name "*.tsx" | xargs grep -l "Database\|Table\|Row" 2>/dev/null | head -3` type usage patterns
- Build configuration: !`find . -name "tsconfig.json" -o -name "*.config.ts" | head -3` TypeScript setup

## Task

Execute comprehensive type generation with schema synchronization and application integration:

**Generation Scope**: Use $ARGUMENTS to generate all table types, specific table types, function signatures, enum definitions, or view types

**Type Generation Framework**:
1. **Schema Analysis** - Extract database schema via MCP, analyze table structures, identify relationships, map data types to TypeScript
2. **Type Generation** - Generate table interfaces, create utility types, implement type guards, optimize type definitions
3. **Integration Setup** - Configure import paths, setup type exports, implement auto-completion, integrate with build process
4. **Validation Process** - Validate generated types, test type compatibility, verify application integration, check build success
5. **Synchronization** - Monitor schema changes, auto-regenerate types, validate breaking changes, notify development team
6. **Developer Experience** - Implement IDE integration, provide type hints, create usage examples, optimize development workflow

**Advanced Features**: Automatic type updates, breaking change detection, custom type transformations, documentation generation, IDE plugin integration.

**Quality Assurance**: Type accuracy validation, application compatibility testing, performance impact assessment, developer feedback integration.

**Output**: Complete TypeScript type definitions with schema synchronization, application integration, validation procedures, and developer documentation.

---

## 🚀 Usage

**Reference this template:** `@command-supabase-type-generator.md`


**Platform-specific:**
- **GitHub Copilot**: Add to `.github/copilot-instructions.md`
- **Augment Code**: Use `aug context add` command
- **Cursor/Windsurf**: Reference in chat with `@filename`
- **Claude**: Add to Project Knowledge
- **ChatGPT**: Add to Custom GPT configuration
