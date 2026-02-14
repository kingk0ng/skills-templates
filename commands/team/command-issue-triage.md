---
id: command-issue-triage
type: command
name: Issue Triage
description: '---

  allowed-tools: Read, Write, Bash

  argument-hint: [scope] | --github-issues | --linear-tasks | --priority-analysis
  | --team-assignment

  description: Intelligent issue triage with automatic categoriza...'
category: team
complexity: medium
keywords:
- github
- optimization
- performance
capabilities: []
token_estimate: 349
---

<!-- Converted from Claude Command Template -->
<!-- Complexity: Medium -->
<!-- Estimated Tokens: ~349 -->


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




# Issue Triage

> ---
allowed-tools: Read, Write, Bash
argument-hint: [scope] | --github-issues | --linear-tasks | --priority-analysis | --team-assignment
description: Intelligent issue triage with automatic categoriza...

---
allowed-capabilities: File operations, code editing, terminal access, search
argument-hint: [scope] | --github-issues | --linear-tasks | --priority-analysis | --team-assignment
description: Intelligent issue triage with automatic categorization, prioritization, and team assignment
---

# Issue Triage

Intelligently triage and prioritize issues with automated routing and team assignment: **$ARGUMENTS**

## Current Triage Context

- Repository: !`gh repo view --json nameWithOwner -q .nameWithOwner 2>/dev/null || echo "No repo context"`
- Open issues: !`gh issue list --state open --limit 1 --json number | jq length 2>/dev/null || echo "Check manually"`
- Linear teams: Available Linear teams and project assignments for routing
- Triage backlog: Current volume and age of untriaged issues

## Task

Execute intelligent issue analysis with automated triage and priority assignment:

**Triage Scope**: Use $ARGUMENTS to focus on GitHub issues, Linear tasks, priority analysis, or team assignment optimization

**Triage Framework**:
1. **Issue Analysis** - Extract issue metadata, analyze content patterns, assess severity indicators, evaluate impact scope
2. **Category Classification** - Identify issue type (bug, feature, documentation), assess complexity level, determine urgency factors
3. **Priority Assessment** - Calculate priority score using severity, impact, effort, and business value metrics
4. **Team Routing** - Match issue skills to team expertise, balance workload distribution, consider current sprint capacity
5. **Label Management** - Apply consistent labeling scheme, maintain taxonomy standards, enable filtering and reporting
6. **SLA Assignment** - Set response time expectations, establish resolution targets, track performance metrics

**Advanced Features**: Automated severity detection, intelligent team matching, workload balancing, SLA monitoring, escalation workflows.

**Quality Assurance**: Consistency validation, triage accuracy tracking, team satisfaction monitoring, process optimization feedback.

**Output**: Complete issue triage with priority assignments, team routing recommendations, SLA targets, and process improvement insights.

---

## 🚀 Usage

**Reference this template:** `@command-issue-triage.md`


**Platform-specific:**
- **GitHub Copilot**: Add to `.github/copilot-instructions.md`
- **Augment Code**: Use `aug context add` command
- **Cursor/Windsurf**: Reference in chat with `@filename`
- **Claude**: Add to Project Knowledge
- **ChatGPT**: Add to Custom GPT configuration
