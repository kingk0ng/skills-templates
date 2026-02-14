---
id: command-implement-graphql-api
type: command
name: Implement GraphQL API
description: '---

  allowed-tools: Read, Write, Edit, Bash

  argument-hint: [schema-approach] | --schema-first | --code-first | --federation

  description: Implement GraphQL API with comprehensive schema, resolvers, and ...'
category: setup
complexity: medium
keywords:
- api
- database
- deployment
- graphql
- optimization
- performance
- security
capabilities: []
token_estimate: 276
---

<!-- Converted from Claude Command Template -->
<!-- Complexity: Medium -->
<!-- Estimated Tokens: ~276 -->


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




# Implement GraphQL API

> ---
allowed-tools: Read, Write, Edit, Bash
argument-hint: [schema-approach] | --schema-first | --code-first | --federation
description: Implement GraphQL API with comprehensive schema, resolvers, and ...

---
allowed-capabilities: File operations, code editing, terminal access, search
argument-hint: [schema-approach] | --schema-first | --code-first | --federation
description: Implement GraphQL API with comprehensive schema, resolvers, and real-time subscriptions
---

# Implement GraphQL API

Implement comprehensive GraphQL API with modern best practices: **$ARGUMENTS**

## Current Application Context

- Framework: @package.json or @requirements.txt (detect Apollo, GraphQL Yoga, etc.)
- Existing API: !`find . -name "*.graphql" -o -name "*schema*" -o -name "*resolver*" | wc -l`
- Database integration: @prisma/schema.prisma or database connection configs
- Authentication: !`grep -r "auth\|jwt\|context" src/ 2>/dev/null | wc -l`

## Task

Build production-ready GraphQL API with comprehensive functionality and performance optimization:

**Schema Approach**: Use $ARGUMENTS to specify schema-first, code-first, or federation architecture

**GraphQL Implementation**:
1. **Schema Design** - Type definitions, queries, mutations, subscriptions, custom scalars
2. **Resolver Architecture** - Data fetching, authentication, authorization, error handling
3. **DataLoader Integration** - N+1 query prevention, batch loading, caching strategies
4. **Real-time Features** - WebSocket subscriptions, live data updates, connection management
5. **Security & Performance** - Query complexity analysis, depth limiting, rate limiting
6. **Development Tools** - GraphQL Playground, introspection, schema stitching

**Advanced Features**: File uploads, federated schemas, Apollo Federation, schema directives, and monitoring.

**Production Readiness**: Implement comprehensive error handling, logging, metrics, and deployment strategies.

**Output**: Complete GraphQL API with optimized resolvers, real-time capabilities, security controls, and developer documentation.

---

## 🚀 Usage

**Reference this template:** `@command-implement-graphql-api.md`


**Platform-specific:**
- **GitHub Copilot**: Add to `.github/copilot-instructions.md`
- **Augment Code**: Use `aug context add` command
- **Cursor/Windsurf**: Reference in chat with `@filename`
- **Claude**: Add to Project Knowledge
- **ChatGPT**: Add to Custom GPT configuration
