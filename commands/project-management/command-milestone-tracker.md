---
id: command-milestone-tracker
type: command
name: Milestone Tracker
description: '---

  allowed-tools: Bash, Read, Grep, Glob

  argument-hint: [time-period] | --sprint | --quarter | --all

  description: Track and analyze project milestone progress with predictive analytics

  ---...'
category: project-management
complexity: medium
keywords:
- git
- github
- optimization
capabilities: []
token_estimate: 250
---

<!-- Converted from Claude Command Template -->
<!-- Complexity: Medium -->
<!-- Estimated Tokens: ~250 -->


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




# Milestone Tracker

> ---
allowed-tools: Bash, Read, Grep, Glob
argument-hint: [time-period] | --sprint | --quarter | --all
description: Track and analyze project milestone progress with predictive analytics
---...

---
allowed-capabilities: File operations, code editing, terminal access, search
argument-hint: [time-period] | --sprint | --quarter | --all
description: Track and analyze project milestone progress with predictive analytics
---

# Milestone Tracker

Track and monitor project milestone progress with comprehensive analytics: **$ARGUMENTS**

## Current Project Context

- Project activity: !`git log --oneline --since="30 days ago" | wc -l` commits
- Active branches: !`git branch -r | wc -l` remote branches
- Recent releases: !`git tag -l --sort=-creatordate | head -5`
- Milestone data: @.github/milestones/ or Linear integration

## Task

Generate comprehensive milestone tracking report analyzing project delivery progress:

**Time Period**: Use $ARGUMENTS or default to current sprint/quarter

**Analysis Dimensions**:
1. **Milestone Progress Tracking**
   - Current milestone completion rates
   - Velocity trends and burn-down analysis
   - Critical path identification
   - Dependency mapping and risk assessment

2. **Predictive Analytics**
   - Completion date predictions with confidence intervals
   - Risk-adjusted timeline recommendations
   - Resource allocation optimization
   - Scenario planning (what-if analysis)

3. **Health Indicators**
   - Schedule adherence metrics
   - Team capacity utilization
   - Blocker identification and impact
   - Quality vs delivery balance

**Output**: Interactive milestone dashboard with visual progress indicators, predictive analytics, risk assessments, and actionable recommendations for milestone delivery optimization.

---

## 🚀 Usage

**Reference this template:** `@command-milestone-tracker.md`


**Platform-specific:**
- **GitHub Copilot**: Add to `.github/copilot-instructions.md`
- **Augment Code**: Use `aug context add` command
- **Cursor/Windsurf**: Reference in chat with `@filename`
- **Claude**: Add to Project Knowledge
- **ChatGPT**: Add to Custom GPT configuration
