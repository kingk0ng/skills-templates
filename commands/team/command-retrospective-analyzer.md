---
id: command-retrospective-analyzer
type: command
name: Retrospective Analyzer
description: '---

  allowed-tools: Read, Write, Bash, Glob

  argument-hint: [sprint-identifier] | --metrics | --insights | --action-items | --trends

  description: Analyze team retrospectives with quantitative metrics an...'
category: team
complexity: medium
keywords:
- git
- optimization
- performance
- testing
capabilities: []
token_estimate: 334
---

<!-- Converted from Claude Command Template -->
<!-- Complexity: Medium -->
<!-- Estimated Tokens: ~334 -->


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




# Retrospective Analyzer

> ---
allowed-tools: Read, Write, Bash, Glob
argument-hint: [sprint-identifier] | --metrics | --insights | --action-items | --trends
description: Analyze team retrospectives with quantitative metrics an...

---
allowed-capabilities: File operations, code editing, terminal access, search
argument-hint: [sprint-identifier] | --metrics | --insights | --action-items | --trends
description: Analyze team retrospectives with quantitative metrics and actionable insights generation
---

# Retrospective Analyzer

Analyze team retrospectives with comprehensive metrics and actionable improvement insights: **$ARGUMENTS**

## Current Retrospective Context

- Sprint period: !`git log --oneline --since='2 weeks ago' | wc -l` commits in recent sprint
- Team activity: Analysis of recent collaboration patterns and productivity metrics
- Linear sprint: Current sprint data and completion metrics from Linear MCP
- Previous retrospectives: Historical retrospective data and improvement tracking

## Task

Execute comprehensive retrospective analysis with quantitative insights and improvement recommendations:

**Analysis Focus**: Use $ARGUMENTS to specify sprint identifier, quantitative metrics, insight generation, action item tracking, or trend analysis

**Retrospective Analysis Framework**:
1. **Sprint Performance Analysis** - Analyze velocity trends, completion rates, cycle time metrics, quality indicators
2. **Team Collaboration Assessment** - Evaluate communication patterns, code review effectiveness, knowledge sharing, pair programming impact
3. **Process Effectiveness** - Assess meeting efficiency, planning accuracy, impediment resolution, workflow optimization
4. **Quality Metrics** - Analyze bug rates, technical debt accumulation, code review quality, testing effectiveness
5. **Individual Contribution** - Evaluate workload distribution, skill development, mentorship activities, cross-training progress
6. **Actionable Insights Generation** - Identify improvement opportunities, prioritize action items, track progress, measure impact

**Advanced Features**: Trend analysis across multiple sprints, predictive performance modeling, team satisfaction correlation, continuous improvement tracking.

**Insight Quality**: Data-driven recommendations, quantified improvement potential, implementation feasibility, success measurement criteria.

**Output**: Comprehensive retrospective analysis with quantitative metrics, actionable insights, prioritized improvements, and progress tracking framework.

---

## 🚀 Usage

**Reference this template:** `@command-retrospective-analyzer.md`


**Platform-specific:**
- **GitHub Copilot**: Add to `.github/copilot-instructions.md`
- **Augment Code**: Use `aug context add` command
- **Cursor/Windsurf**: Reference in chat with `@filename`
- **Claude**: Add to Project Knowledge
- **ChatGPT**: Add to Custom GPT configuration
