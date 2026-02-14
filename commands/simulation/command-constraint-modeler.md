---
id: command-constraint-modeler
type: command
name: Constraint Modeler
description: '---

  allowed-tools: Read, Write, Edit, WebSearch

  argument-hint: [constraint-domain] | --business | --technical | --regulatory | --resource

  description: Model system constraints with validation, depende...'
category: simulation
complexity: medium
keywords:
- optimization
capabilities: []
token_estimate: 278
---

<!-- Converted from Claude Command Template -->
<!-- Complexity: Medium -->
<!-- Estimated Tokens: ~278 -->


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




# Constraint Modeler

> ---
allowed-tools: Read, Write, Edit, WebSearch
argument-hint: [constraint-domain] | --business | --technical | --regulatory | --resource
description: Model system constraints with validation, depende...

---
allowed-capabilities: File operations, code editing, terminal access, searchWebSearch
argument-hint: [constraint-domain] | --business | --technical | --regulatory | --resource
description: Model system constraints with validation, dependency mapping, and optimization strategies
---

# Constraint Modeler

Model comprehensive system constraints with systematic validation and optimization: **$ARGUMENTS**

## Current System Context

- Domain scope: Based on $ARGUMENTS (business, technical, operational, financial)
- Existing constraints: @documentation or configuration files
- System boundaries: Current limitations and dependencies
- Change dynamics: Historical constraint evolution patterns

## Task

Create comprehensive constraint models for accurate simulation and decision-making:

**Constraint Domain**: Use $ARGUMENTS to focus on business, technical, regulatory, or resource constraints

**Constraint Framework**:
1. **Hard Constraints** - Absolute limits that cannot be violated (legal, physical, technical)
2. **Soft Constraints** - Preferences and trade-offs that can be managed (budget, quality, timing)
3. **Dynamic Constraints** - Limitations that evolve over time (market, technology, capacity)
4. **Constraint Dependencies** - Relationships and interactions between different limitations
5. **Validation Framework** - Methods to verify constraint accuracy and relevance
6. **Optimization Strategies** - Approaches to relax, substitute, or circumvent constraints

**Advanced Analysis**: Constraint sensitivity analysis, bottleneck identification, scenario boundary definition, and optimization algorithms.

**Strategic Application**: Link constraint models to decision scenarios, resource allocation, and strategic planning.

**Output**: Complete constraint model with interaction matrices, validation reports, optimization recommendations, and scenario boundary definitions.

---

## 🚀 Usage

**Reference this template:** `@command-constraint-modeler.md`


**Platform-specific:**
- **GitHub Copilot**: Add to `.github/copilot-instructions.md`
- **Augment Code**: Use `aug context add` command
- **Cursor/Windsurf**: Reference in chat with `@filename`
- **Claude**: Add to Project Knowledge
- **ChatGPT**: Add to Custom GPT configuration
