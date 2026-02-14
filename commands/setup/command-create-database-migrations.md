---
id: command-create-database-migrations
type: command
name: Create Database Migrations
description: '---

  allowed-tools: Read, Write, Edit, Bash

  argument-hint: [migration-name] | --create-table | --add-column | --alter-table

  description: Create and manage database migrations with proper versioning and...'
category: setup
complexity: medium
keywords:
- database
- testing
capabilities: []
token_estimate: 262
---

<!-- Converted from Claude Command Template -->
<!-- Complexity: Medium -->
<!-- Estimated Tokens: ~262 -->


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




# Create Database Migrations

> ---
allowed-tools: Read, Write, Edit, Bash
argument-hint: [migration-name] | --create-table | --add-column | --alter-table
description: Create and manage database migrations with proper versioning and...

---
allowed-capabilities: File operations, code editing, terminal access, search
argument-hint: [migration-name] | --create-table | --add-column | --alter-table
description: Create and manage database migrations with proper versioning and rollback support
---

# Create Database Migrations

Create and manage database migrations: **$ARGUMENTS**

## Current Database State

- ORM detection: @package.json or @requirements.txt (detect Sequelize, Prisma, Alembic, etc.)
- Migration files: !`find . -name "*migration*" -type f | head -5`
- Database config: @config/database.* or @prisma/schema.prisma
- Current schema: !`ls migrations/ 2>/dev/null | wc -l` migrations found

## Task

Create comprehensive database migrations with proper versioning and rollback capabilities:

**Migration Types**: Use $ARGUMENTS to specify table creation, column addition, table alteration, or data migration

**Migration Framework**:
1. **Migration Planning** - Analyze schema changes, dependencies, and data impact
2. **Migration Generation** - Create timestamped migration files with up/down methods
3. **Schema Updates** - Table creation, column modifications, index management
4. **Data Migrations** - Safe data transformations and backfills
5. **Rollback Strategy** - Implement reliable rollback procedures for each change
6. **Testing** - Validate migrations in development and staging environments

**Best Practices**: Follow database-specific conventions, maintain referential integrity, handle large datasets efficiently, and ensure zero-downtime deployments.

**Output**: Production-ready migration files with comprehensive rollback support, proper indexing, and data safety measures.

---

## 🚀 Usage

**Reference this template:** `@command-create-database-migrations.md`


**Platform-specific:**
- **GitHub Copilot**: Add to `.github/copilot-instructions.md`
- **Augment Code**: Use `aug context add` command
- **Cursor/Windsurf**: Reference in chat with `@filename`
- **Claude**: Add to Project Knowledge
- **ChatGPT**: Add to Custom GPT configuration
