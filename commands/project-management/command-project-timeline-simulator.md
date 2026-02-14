---
id: command-project-timeline-simulator
type: command
name: Project Timeline Simulator
description: '---

  allowed-tools: Read, Bash, Grep, Glob

  argument-hint: [project-type] | --duration | --team-size | --risk-level

  description: Simulate project outcomes with variable modeling, risk assessment,
  and re...'
category: project-management
complexity: medium
keywords:
- git
- optimization
capabilities: []
token_estimate: 258
---

<!-- Converted from Claude Command Template -->
<!-- Complexity: Medium -->
<!-- Estimated Tokens: ~258 -->


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




# Project Timeline Simulator

> ---
allowed-tools: Read, Bash, Grep, Glob
argument-hint: [project-type] | --duration | --team-size | --risk-level
description: Simulate project outcomes with variable modeling, risk assessment, and re...

---
allowed-capabilities: File operations, code editing, terminal access, search
argument-hint: [project-type] | --duration | --team-size | --risk-level
description: Simulate project outcomes with variable modeling, risk assessment, and resource optimization
---

# Project Timeline Simulator

Simulate project outcomes with comprehensive variable modeling and risk assessment: **$ARGUMENTS**

## Current Project Context

- Project type: Based on $ARGUMENTS or codebase analysis
- Team capacity: !`git shortlog -sn --since="90 days ago" | wc -l` contributors
- Velocity data: !`git log --oneline --since="30 days ago" | wc -l` commits/month
- Risk indicators: @RISKS.md or project documentation

## Task

Generate comprehensive project timeline simulations with multiple scenarios:

**Simulation Framework**:
1. **Variable Modeling** - Team capacity, skill levels, external dependencies, technical complexity
2. **Scenario Generation** - Baseline, optimistic, pessimistic, and disruption scenarios
3. **Risk Assessment** - Technical, resource, business, and external risk factors
4. **Resource Optimization** - Team allocation, budget distribution, timeline buffers
5. **Decision Points** - Milestone gates, adaptation triggers, contingency activation

**Output Deliverables**:
- Timeline prediction ranges with confidence intervals
- Critical path analysis and dependency mapping
- Risk-adjusted resource allocation recommendations
- Early warning indicators and decision triggers
- Monte Carlo simulation results with probability distributions

**Success Optimization**: Multi-objective optimization for time, quality, and resource efficiency.

---

## 🚀 Usage

**Reference this template:** `@command-project-timeline-simulator.md`


**Platform-specific:**
- **GitHub Copilot**: Add to `.github/copilot-instructions.md`
- **Augment Code**: Use `aug context add` command
- **Cursor/Windsurf**: Reference in chat with `@filename`
- **Claude**: Add to Project Knowledge
- **ChatGPT**: Add to Custom GPT configuration
