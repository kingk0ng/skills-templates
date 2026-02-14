---
id: command-dependency-mapper
type: command
name: Dependency Mapper
description: '---

  allowed-tools: Read, Glob, Grep, Bash

  argument-hint: [scope] | --tasks | --code | --circular | --critical-path

  description: Map project and task dependencies with critical path analysis and circul...'
category: team
complexity: medium
keywords:
- optimization
capabilities: []
token_estimate: 366
---

<!-- Converted from Claude Command Template -->
<!-- Complexity: Medium -->
<!-- Estimated Tokens: ~366 -->


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




# Dependency Mapper

> ---
allowed-tools: Read, Glob, Grep, Bash
argument-hint: [scope] | --tasks | --code | --circular | --critical-path
description: Map project and task dependencies with critical path analysis and circul...

---
allowed-capabilities: File operations, code editing, terminal access, search
argument-hint: [scope] | --tasks | --code | --circular | --critical-path
description: Map project and task dependencies with critical path analysis and circular dependency detection
---

# Dependency Mapper

Map and analyze project dependencies with task ordering optimization: **$ARGUMENTS**

## Current Dependency Context

- Repository: !`gh repo view --json nameWithOwner -q .nameWithOwner 2>/dev/null || echo "No repo context"`
- Project files: !`find . -name "*.js" -o -name "*.ts" -o -name "*.py" | wc -l` code files analyzed
- Task tracking: Linear MCP server connectivity and task relationship data
- Import analysis: Code dependency structure and circular dependency detection

## Task

Execute comprehensive dependency analysis with optimization recommendations:

**Analysis Scope**: Use $ARGUMENTS to focus on task dependencies, code dependencies, circular dependency detection, or critical path analysis

**Dependency Analysis Framework**:
1. **Code Dependency Mapping** - Extract import statements, analyze module relationships, identify coupling levels, map file interdependencies
2. **Task Relationship Analysis** - Query Linear task dependencies, extract task mentions, analyze project relationships, map epic structures
3. **Dependency Graph Construction** - Build comprehensive graph structure, identify dependency chains, calculate critical paths, detect bottlenecks
4. **Circular Dependency Detection** - Implement cycle detection algorithms, identify problematic loops, assess impact severity, recommend resolution strategies
5. **Execution Order Optimization** - Calculate topological sort, optimize task sequence, balance team capacity, minimize blocking dependencies
6. **Risk Assessment** - Identify high-risk chains, assess single points of failure, evaluate dependency complexity, recommend mitigation strategies

**Advanced Features**: Visual dependency graphs, ASCII tree representations, impact analysis, sprint planning optimization, real-time dependency tracking.

**Quality Insights**: Dependency health metrics, coupling analysis, maintainability assessment, team workload distribution.

**Output**: Complete dependency analysis with visual representations, execution order recommendations, risk mitigation strategies, and optimization roadmap.

---

## 🚀 Usage

**Reference this template:** `@command-dependency-mapper.md`


**Platform-specific:**
- **GitHub Copilot**: Add to `.github/copilot-instructions.md`
- **Augment Code**: Use `aug context add` command
- **Cursor/Windsurf**: Reference in chat with `@filename`
- **Claude**: Add to Project Knowledge
- **ChatGPT**: Add to Custom GPT configuration
