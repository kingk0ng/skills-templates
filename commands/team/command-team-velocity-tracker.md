---
id: command-team-velocity-tracker
type: command
name: Team Velocity Tracker
description: '---

  allowed-tools: Read, Bash, Glob, Grep

  argument-hint: [analysis-period] | --sprint | --monthly | --quarterly | --trend-analysis

  description: Track and analyze team velocity with predictive forecast...'
category: team
complexity: medium
keywords:
- git
- optimization
- performance
capabilities: []
token_estimate: 365
---

<!-- Converted from Claude Command Template -->
<!-- Complexity: Medium -->
<!-- Estimated Tokens: ~365 -->


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




# Team Velocity Tracker

> ---
allowed-tools: Read, Bash, Glob, Grep
argument-hint: [analysis-period] | --sprint | --monthly | --quarterly | --trend-analysis
description: Track and analyze team velocity with predictive forecast...

---
allowed-capabilities: File operations, code editing, terminal access, search
argument-hint: [analysis-period] | --sprint | --monthly | --quarterly | --trend-analysis
description: Track and analyze team velocity with predictive forecasting and performance optimization recommendations
---

# Team Velocity Tracker

Track team velocity patterns with predictive forecasting and performance optimization: **$ARGUMENTS**

## Current Velocity Context

- Sprint velocity: !`git log --oneline --since='2 weeks ago' | wc -l` commits per current sprint
- Team consistency: Analysis of velocity stability across recent sprints
- Linear tracking: Sprint point completion rates and story delivery metrics
- Capacity factors: Team size changes, availability, and skill development impact

## Task

Execute comprehensive velocity tracking with predictive analytics and optimization recommendations:

**Analysis Period**: Use $ARGUMENTS to focus on sprint velocity, monthly trends, quarterly patterns, or comprehensive trend analysis

**Velocity Tracking Framework**:
1. **Historical Velocity Analysis** - Extract sprint completion data, analyze story point delivery, calculate team throughput, identify performance patterns
2. **Consistency Assessment** - Measure velocity stability, identify variance patterns, assess predictability factors, evaluate planning accuracy
3. **Capacity Correlation** - Analyze team size impact, assess skill level effects, evaluate availability constraints, measure external factor influence
4. **Predictive Forecasting** - Generate velocity projections, predict sprint outcomes, estimate delivery timelines, calculate confidence intervals
5. **Performance Optimization** - Identify improvement opportunities, recommend capacity adjustments, suggest process enhancements, optimize team composition
6. **Quality Integration** - Correlate velocity with quality metrics, assess technical debt impact, evaluate sustainable pace, measure team satisfaction

**Advanced Features**: Monte Carlo forecasting, velocity trend decomposition, capacity planning optimization, performance anomaly detection, sustainable pace analysis.

**Predictive Analytics**: Sprint outcome predictions, delivery timeline forecasting, capacity requirement planning, performance trend analysis.

**Output**: Comprehensive velocity analysis with predictive forecasts, optimization recommendations, capacity planning insights, and sustainable performance strategies.

---

## 🚀 Usage

**Reference this template:** `@command-team-velocity-tracker.md`


**Platform-specific:**
- **GitHub Copilot**: Add to `.github/copilot-instructions.md`
- **Augment Code**: Use `aug context add` command
- **Cursor/Windsurf**: Reference in chat with `@filename`
- **Claude**: Add to Project Knowledge
- **ChatGPT**: Add to Custom GPT configuration
