---
id: command-standup-report
type: command
name: Standup Report
description: '---

  allowed-tools: Read, Bash, Glob, Grep

  argument-hint: [time-range] | --yesterday | --last-24h | --since-friday | --custom-range

  description: Generate comprehensive daily standup reports with team a...'
category: team
complexity: medium
keywords:
- git
- optimization
capabilities: []
token_estimate: 358
---

<!-- Converted from Claude Command Template -->
<!-- Complexity: Medium -->
<!-- Estimated Tokens: ~358 -->


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




# Standup Report

> ---
allowed-tools: Read, Bash, Glob, Grep
argument-hint: [time-range] | --yesterday | --last-24h | --since-friday | --custom-range
description: Generate comprehensive daily standup reports with team a...

---
allowed-capabilities: File operations, code editing, terminal access, search
argument-hint: [time-range] | --yesterday | --last-24h | --since-friday | --custom-range
description: Generate comprehensive daily standup reports with team activity analysis and progress tracking
---

# Standup Report

Generate comprehensive daily standup reports with team activity and progress analysis: **$ARGUMENTS**

## Current Standup Context

- Linear connection: Linear MCP server status and task synchronization
- Time range: !`date -d 'yesterday' '+%Y-%m-%d'` to !`date '+%Y-%m-%d'` analysis period
- Team members: !`git log --format='%ae' --since='1 day ago' | sort -u | wc -l` active contributors
- Repository: !`gh repo view --json nameWithOwner -q .nameWithOwner 2>/dev/null || echo "No repo context"`

## Task

Generate comprehensive standup report with team activity analysis and progress insights:

**Time Range**: Use $ARGUMENTS to specify yesterday, last 24 hours, since Friday, or custom date range for analysis

**Standup Report Framework**:
1. **Git Activity Analysis** - Extract commit activity, analyze code changes, identify contributors, assess impact scope
2. **Linear Task Progress** - Query task updates, analyze completion status, track sprint progress, identify blockers
3. **Pull Request Activity** - Review PR submissions, analyze review activity, track merge status, assess collaboration patterns
4. **Team Collaboration** - Analyze pair programming, code review participation, knowledge sharing, mentorship activities
5. **Progress Tracking** - Calculate velocity metrics, assess goal completion, identify trends, predict sprint outcomes
6. **Blockers & Impediments** - Identify stuck tasks, analyze delay patterns, assess resource needs, recommend solutions

**Advanced Features**: Automated activity categorization, progress visualization, trend analysis, predictive insights, team health scoring.

**Report Quality**: Actionable insights, clear progress indicators, obstacle identification, team coordination support, meeting efficiency optimization.

**Output**: Comprehensive standup report with team activity summary, progress metrics, blocker identification, and actionable next steps.

---

## 🚀 Usage

**Reference this template:** `@command-standup-report.md`


**Platform-specific:**
- **GitHub Copilot**: Add to `.github/copilot-instructions.md`
- **Augment Code**: Use `aug context add` command
- **Cursor/Windsurf**: Reference in chat with `@filename`
- **Claude**: Add to Project Knowledge
- **ChatGPT**: Add to Custom GPT configuration
