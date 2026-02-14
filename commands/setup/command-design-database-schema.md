---
id: command-design-database-schema
type: command
name: Design Database Schema
description: '---

  allowed-tools: Read, Write, Edit, Bash

  argument-hint: [schema-type] | --relational | --nosql | --hybrid | --normalize

  description: Design optimized database schemas with proper relationships, cons...'
category: setup
complexity: medium
keywords:
- audit
- database
- nosql
- optimization
- performance
- security
capabilities: []
token_estimate: 266
---

<!-- Converted from Claude Command Template -->
<!-- Complexity: Medium -->
<!-- Estimated Tokens: ~266 -->


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




# Design Database Schema

> ---
allowed-tools: Read, Write, Edit, Bash
argument-hint: [schema-type] | --relational | --nosql | --hybrid | --normalize
description: Design optimized database schemas with proper relationships, cons...

---
allowed-capabilities: File operations, code editing, terminal access, search
argument-hint: [schema-type] | --relational | --nosql | --hybrid | --normalize
description: Design optimized database schemas with proper relationships, constraints, and performance considerations
---

# Design Database Schema

Design optimized database schemas with comprehensive data modeling: **$ARGUMENTS**

## Current Project Context

- Application type: Based on $ARGUMENTS or codebase analysis
- Data requirements: @requirements/ or project documentation
- Existing schema: @prisma/schema.prisma or @migrations/ or database dumps
- Performance needs: Expected scale, query patterns, and data volume

## Task

Design comprehensive database schema with optimal structure and performance:

**Schema Type**: Use $ARGUMENTS to specify relational, NoSQL, hybrid approach, or normalization level

**Design Framework**:
1. **Requirements Analysis** - Business entities, relationships, data flow, and access patterns
2. **Entity Modeling** - Tables/collections, attributes, primary/foreign keys, constraints
3. **Relationship Design** - One-to-one, one-to-many, many-to-many associations
4. **Normalization Strategy** - Data consistency vs performance trade-offs
5. **Performance Optimization** - Indexing strategy, query optimization, partitioning
6. **Security Design** - Access control, data encryption, audit trails

**Advanced Patterns**: Implement temporal data, soft deletes, JSONB fields, full-text search, audit logging, and scalability patterns.

**Validation**: Ensure referential integrity, data consistency, query performance, and future extensibility.

**Output**: Complete schema design with DDL scripts, ER diagrams, performance analysis, and migration strategy.

---

## 🚀 Usage

**Reference this template:** `@command-design-database-schema.md`


**Platform-specific:**
- **GitHub Copilot**: Add to `.github/copilot-instructions.md`
- **Augment Code**: Use `aug context add` command
- **Cursor/Windsurf**: Reference in chat with `@filename`
- **Claude**: Add to Project Knowledge
- **ChatGPT**: Add to Custom GPT configuration
