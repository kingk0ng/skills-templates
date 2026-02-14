---
id: command-supabase-performance-optimizer
type: command
name: Supabase Performance Optimizer
description: '---

  allowed-tools: Read, Write, Edit, Bash

  argument-hint: [optimization-type] | --queries | --indexes | --storage | --rls |
  --functions

  description: Optimize Supabase database performance with intelli...'
category: database
complexity: medium
keywords:
- database
- optimization
- performance
- security
- sql
capabilities: []
token_estimate: 356
---

<!-- Converted from Claude Command Template -->
<!-- Complexity: Medium -->
<!-- Estimated Tokens: ~356 -->


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




# Supabase Performance Optimizer

> ---
allowed-tools: Read, Write, Edit, Bash
argument-hint: [optimization-type] | --queries | --indexes | --storage | --rls | --functions
description: Optimize Supabase database performance with intelli...

---
allowed-capabilities: File operations, code editing, terminal access, search
argument-hint: [optimization-type] | --queries | --indexes | --storage | --rls | --functions
description: Optimize Supabase database performance with intelligent analysis and recommendations
---

# Supabase Performance Optimizer

Optimize Supabase database performance with intelligent analysis and automated improvements: **$ARGUMENTS**

## Current Performance Context

- Supabase metrics: Database performance data via MCP integration
- Query patterns: !`find . -name "*.sql" -o -name "*.ts" -o -name "*.js" | xargs grep -l "from\|select\|insert\|update" 2>/dev/null | head -5` application queries
- Schema analysis: Current table structures and relationship complexity
- Performance logs: Recent query execution times and resource usage patterns

## Task

Execute comprehensive performance optimization with intelligent analysis and automated improvements:

**Optimization Focus**: Use $ARGUMENTS to focus on query optimization, index management, storage optimization, RLS policies, or database functions

**Performance Optimization Framework**:
1. **Performance Analysis** - Analyze query execution times, identify slow operations, assess resource utilization, evaluate bottlenecks
2. **Index Optimization** - Analyze index usage, recommend new indexes, identify redundant indexes, optimize index strategies
3. **Query Optimization** - Review application queries, suggest query improvements, implement query caching, optimize join operations
4. **Storage Optimization** - Analyze storage patterns, recommend archival strategies, optimize data types, implement compression
5. **RLS Policy Review** - Analyze Row Level Security policies, optimize policy performance, reduce policy complexity, improve security efficiency
6. **Function Optimization** - Review database functions, optimize function performance, implement caching strategies, improve execution plans

**Advanced Features**: Automated index recommendations, query plan analysis, performance trend monitoring, cost optimization, scaling recommendations.

**Monitoring Integration**: Real-time performance tracking, alert configuration, performance regression detection, optimization impact measurement.

**Output**: Comprehensive optimization plan with performance improvements, index recommendations, query optimizations, and monitoring setup.

---

## 🚀 Usage

**Reference this template:** `@command-supabase-performance-optimizer.md`


**Platform-specific:**
- **GitHub Copilot**: Add to `.github/copilot-instructions.md`
- **Augment Code**: Use `aug context add` command
- **Cursor/Windsurf**: Reference in chat with `@filename`
- **Claude**: Add to Project Knowledge
- **ChatGPT**: Add to Custom GPT configuration
