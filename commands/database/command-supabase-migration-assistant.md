---
id: command-supabase-migration-assistant
type: command
name: Supabase Migration Assistant
description: '---

  allowed-tools: Read, Write, Edit, Bash

  argument-hint: [migration-type] | --create | --alter | --seed | --rollback

  description: Generate and manage Supabase database migrations with automated testi...'
category: database
complexity: medium
keywords:
- ci/cd
- database
- deployment
- git
- performance
- sql
- test
- testing
- typescript
capabilities: []
token_estimate: 357
---

<!-- Converted from Claude Command Template -->
<!-- Complexity: Medium -->
<!-- Estimated Tokens: ~357 -->


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




# Supabase Migration Assistant

> ---
allowed-tools: Read, Write, Edit, Bash
argument-hint: [migration-type] | --create | --alter | --seed | --rollback
description: Generate and manage Supabase database migrations with automated testi...

---
allowed-capabilities: File operations, code editing, terminal access, search
argument-hint: [migration-type] | --create | --alter | --seed | --rollback
description: Generate and manage Supabase database migrations with automated testing and validation
---

# Supabase Migration Assistant

Generate and manage Supabase migrations with comprehensive testing and validation: **$ARGUMENTS**

## Current Migration Context

- Supabase project: MCP integration for migration management and validation
- Migration files: !`find . -name "*migrations*" -type d -o -name "*.sql" | head -5` existing migration structure
- Schema version: Current database schema state and migration history
- Local changes: !`git diff --name-only | grep -E "\\.sql$|\\.ts$" | head -3` pending database modifications

## Task

Execute comprehensive migration management with automated validation and testing:

**Migration Type**: Use $ARGUMENTS to specify table creation, schema alterations, data seeding, or migration rollback

**Migration Management Framework**:
1. **Migration Planning** - Analyze schema requirements, design migration strategy, identify dependencies, plan rollback procedures
2. **Code Generation** - Generate migration SQL files, create TypeScript types, implement safety checks, optimize execution order
3. **Validation Testing** - Test migration on development data, validate schema changes, verify data integrity, check constraint violations
4. **Supabase Integration** - Apply migrations via MCP server, monitor execution status, handle error conditions, validate final state
5. **Type Generation** - Generate TypeScript types, update application interfaces, sync with client-side schemas, maintain type safety
6. **Rollback Strategy** - Create rollback migrations, test rollback procedures, implement data preservation, validate recovery process

**Advanced Features**: Automated type generation, migration testing, performance impact analysis, team collaboration, CI/CD integration.

**Safety Measures**: Pre-migration backups, dry-run validation, rollback testing, data integrity checks, performance monitoring.

**Output**: Complete migration suite with SQL files, TypeScript types, test validation, rollback procedures, and deployment documentation.

---

## 🚀 Usage

**Reference this template:** `@command-supabase-migration-assistant.md`


**Platform-specific:**
- **GitHub Copilot**: Add to `.github/copilot-instructions.md`
- **Augment Code**: Use `aug context add` command
- **Cursor/Windsurf**: Reference in chat with `@filename`
- **Claude**: Add to Project Knowledge
- **ChatGPT**: Add to Custom GPT configuration
