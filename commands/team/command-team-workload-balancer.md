---
id: command-team-workload-balancer
type: command
name: Team Workload Balancer
description: '---

  allowed-tools: Read, Bash, Grep, Glob

  argument-hint: [analysis-type] | --current-workload | --skill-matching | --capacity-planning
  | --assignment-optimization

  description: Analyze and optimize tea...'
category: team
complexity: medium
keywords:
- git
- optimization
- performance
capabilities: []
token_estimate: 367
---

<!-- Converted from Claude Command Template -->
<!-- Complexity: Medium -->
<!-- Estimated Tokens: ~367 -->


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




# Team Workload Balancer

> ---
allowed-tools: Read, Bash, Grep, Glob
argument-hint: [analysis-type] | --current-workload | --skill-matching | --capacity-planning | --assignment-optimization
description: Analyze and optimize tea...

---
allowed-capabilities: File operations, code editing, terminal access, search
argument-hint: [analysis-type] | --current-workload | --skill-matching | --capacity-planning | --assignment-optimization
description: Analyze and optimize team workload distribution with skill matching and capacity planning
---

# Team Workload Balancer

Analyze and optimize team workload distribution with intelligent assignment recommendations: **$ARGUMENTS**

## Current Team Context

- Team size: !`git log --format='%ae' --since='1 month ago' | sort -u | wc -l` active team members
- Active tasks: Linear MCP query for current sprint tasks and assignments
- Recent activity: !`git log --oneline --since='1 week ago' | wc -l` commits in last week
- Capacity metrics: Analysis of team velocity and individual contribution patterns

## Task

Execute comprehensive workload analysis with intelligent assignment optimization:

**Analysis Type**: Use $ARGUMENTS to focus on current workload assessment, skill matching, capacity planning, or assignment optimization

**Workload Balancing Framework**:
1. **Current Workload Assessment** - Analyze task distribution, evaluate individual capacity, assess deadline pressure, identify overloaded team members
2. **Skill Matching Analysis** - Map team member expertise, identify skill gaps, assess learning opportunities, optimize skill utilization
3. **Capacity Planning** - Calculate available capacity, project future workload, plan skill development, optimize resource allocation
4. **Performance Integration** - Analyze historical performance, identify productivity patterns, assess collaboration effectiveness, factor in availability constraints
5. **Assignment Optimization** - Generate optimal task assignments, balance workload distribution, maximize skill utilization, minimize bottlenecks
6. **Risk Mitigation** - Identify single points of failure, plan cross-training, assess knowledge distribution, ensure backup coverage

**Advanced Features**: Predictive workload modeling, skill gap analysis, burnout prevention, performance-based assignment, dynamic rebalancing recommendations.

**Quality Metrics**: Workload distribution equity, skill utilization efficiency, team satisfaction indicators, delivery predictability measures.

**Output**: Comprehensive workload analysis with optimized assignments, capacity recommendations, skill development plans, and team health insights.

---

## 🚀 Usage

**Reference this template:** `@command-team-workload-balancer.md`


**Platform-specific:**
- **GitHub Copilot**: Add to `.github/copilot-instructions.md`
- **Augment Code**: Use `aug context add` command
- **Cursor/Windsurf**: Reference in chat with `@filename`
- **Claude**: Add to Project Knowledge
- **ChatGPT**: Add to Custom GPT configuration
