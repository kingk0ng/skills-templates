---
id: command-task-report-command
type: command
name: Task Report Command
description: Generate comprehensive reports on task execution, progress, and metrics....
category: orchestration
complexity: medium
keywords:
- api
- audit
- database
- github
- performance
- qa
- security
- test
- testing
capabilities: []
token_estimate: 1006
---

<!-- Converted from Claude Command Template -->
<!-- Complexity: Medium -->
<!-- Estimated Tokens: ~1,006 -->


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




# Task Report Command

> Generate comprehensive reports on task execution, progress, and metrics....

# Task Report Command

Generate comprehensive reports on task execution, progress, and metrics.

## Usage

```
/task-report [report-type] [options]
```

## Description

Creates detailed reports for project management, sprint reviews, and performance analysis. Supports multiple report types and output formats.

## Report Types

### Executive Summary
```
/task-report executive
```
High-level overview for stakeholders with key metrics and progress.

### Sprint Report
```
/task-report sprint --date 03_15_2024
```
Detailed sprint progress with burndown charts and velocity.

### Daily Standup
```
/task-report standup
```
What was completed, in progress, and blocked.

### Performance Report
```
/task-report performance --period week
```
Team and individual performance metrics.

### Dependency Report
```
/task-report dependencies
```
Visual dependency graph and bottleneck analysis.

## Output Examples

### Executive Summary Report
```
EXECUTIVE SUMMARY - Authentication System Project
================================================
Report Date: 2024-03-15
Project Start: 2024-03-13
Duration: 3 days (60% complete)

KEY METRICS
-----------
• Total Tasks: 24
• Completed: 12 (50%)
• In Progress: 3 (12.5%)
• Blocked: 2 (8.3%)
• Remaining: 7 (29.2%)

TIMELINE
--------
• Original Estimate: 5 days
• Current Projection: 5.5 days
• Risk Level: Low

HIGHLIGHTS
----------
✓ Core authentication API completed
✓ Database schema migrated
✓ Unit tests passing (98% coverage)

BLOCKERS
--------
⚠ Payment integration waiting on external API
⚠ UI components need design approval

NEXT MILESTONES
--------------
→ Complete JWT implementation (Today)
→ Integration testing (Tomorrow)
→ Security audit (Day 4)
```

### Sprint Burndown Report
```
/task-report burndown --sprint current
```
```
SPRINT BURNDOWN - Sprint 24
===========================

Tasks Remaining by Day:
Day 1: ████████████████████ 24
Day 2: ████████████████     20 
Day 3: ████████████         15 (TODAY)
Day 4: ████████             10 (projected)
Day 5: ████                 5  (projected)

Velocity Metrics:
- Average: 4.5 tasks/day
- Yesterday: 5 tasks
- Today: 3 tasks (in progress)

Risk Assessment: ON TRACK
```

### Performance Report
```
TEAM PERFORMANCE REPORT - Week 11
=================================

By Agent:
┌─────────────────┬────────┬───────────┬─────────┬────────────┐
│ Agent           │ Completed │ Avg Time │ Quality │ Efficiency │
├─────────────────┼────────┼───────────┼─────────┼────────────┤
│ dev-frontend    │    8   │   3.2h    │   95%   │    125%    │
│ dev-backend     │    6   │   4.1h    │   98%   │    110%    │
│ test-developer  │    4   │   2.8h    │   100%  │    115%    │
└─────────────────┴────────┴───────────┴─────────┴────────────┘

By Task Type:
- Features: 12 completed (avg 3.8h)
- Bugfixes: 4 completed (avg 1.5h)
- Tests: 8 completed (avg 2.2h)

Quality Metrics:
- First-time pass rate: 88%
- Rework required: 2 tasks
- Blocked time: 4.5 hours total
```

## Customization Options

### Time Period
```
/task-report summary --from 2024-03-01 --to 2024-03-15
/task-report summary --last 7d
/task-report summary --this-month
```

### Specific Project
```
/task-report sprint --project authentication_system
```

### Format Options
```
/task-report executive --format markdown
/task-report executive --format html
/task-report executive --format pdf
```

### Include/Exclude
```
/task-report summary --include completed,qa
/task-report summary --exclude on_hold
```

## Specialized Reports

### Critical Path Analysis
```
/task-report critical-path
```
Shows tasks that directly impact completion time.

### Bottleneck Analysis
```
/task-report bottlenecks
```
Identifies tasks causing delays.

### Resource Utilization
```
/task-report resources
```
Shows agent allocation and availability.

### Risk Assessment
```
/task-report risks
```
Identifies potential delays and issues.

## Visualization Options

### Gantt Chart
```
/task-report gantt --weeks 2
```

### Dependency Graph
```
/task-report dependencies --visual
```

### Status Flow
```
/task-report flow --animated
```

## Automated Reports

### Schedule Reports
```
/task-report schedule daily-standup --at "9am"
/task-report schedule weekly-summary --every friday
```

### Email Reports
```
/task-report executive --email team@company.com
```

## Comparison Reports

### Sprint Comparison
```
/task-report compare --sprint 23 24
```

### Week over Week
```
/task-report trends --weeks 4
```

## Examples

### Example 1: Morning Status
```
/task-report standup --format slack
```
Generates Slack-formatted standup report.

### Example 2: Sprint Review
```
/task-report sprint --include-velocity --include-burndown
```
Comprehensive sprint metrics for review meeting.

### Example 3: Blocker Focus
```
/task-report blockers --show-dependencies --show-resolution
```
Deep dive into what's blocking progress.

## Integration Features

### Export to Tools
```
/task-report export-jira
/task-report export-asana
/task-report export-github
```

### API Endpoints
```
/task-report api --generate-endpoint
```
Creates API endpoint for external access.

## Best Practices

1. **Daily Reviews**: Run standup report each morning
2. **Weekly Summaries**: Generate performance reports on Fridays
3. **Sprint Planning**: Use velocity trends for estimation
4. **Stakeholder Updates**: Schedule automated executive summaries

## Report Components

Each report can include:
- Summary statistics
- Timeline visualization
- Task lists by status
- Agent performance
- Dependency analysis
- Risk assessment
- Recommendations
- Historical trends

## Notes

- Reports use data from all TASK-STATUS-TRACKER.yaml files
- Completed tasks are included in historical metrics
- Time calculations use business hours by default
- All times shown in local timezone
- Charts require terminal unicode support

---


## 💻 Code Examples


### Example 1

```text
/task-report [report-type] [options]
```


### Example 2

```text
/task-report executive
```


### Example 3

```text
/task-report sprint --date 03_15_2024
```


### Example 4

```text
/task-report standup
```


### Example 5

```text
/task-report performance --period week
```


### Example 6

```text
/task-report dependencies
```


### Example 7

```text
EXECUTIVE SUMMARY - Authentication System Project
================================================
Report Date: 2024-03-15
Project Start: 2024-03-13
Duration: 3 days (60% complete)

KEY METRICS
-----------
• Total Tasks: 24
• Completed: 12 (50%)
• In Progress: 3 (12.5%)
• Blocked: 2 (8.3%)
• Remaining: 7 (29.2%)

TIMELINE
--------
• Original Estimate: 5 days
• Current Projection: 5.5 days
• Risk Level: Low

HIGHLIGHTS
----------
✓ Core authentication API completed
✓ Database schema migrated
✓ Unit tests passing (98% coverage)

BLOCKERS
--------
⚠ Payment integration waiting on external API
⚠ UI components need design approval

NEXT MILESTONES
--------------
→ Complete JWT implementation (Today)
→ Integration testing (Tomorrow)
→ Security audit (Day 4)
```


### Example 8

```text
/task-report burndown --sprint current
```


### Example 9

```text
SPRINT BURNDOWN - Sprint 24
===========================

Tasks Remaining by Day:
Day 1: ████████████████████ 24
Day 2: ████████████████     20 
Day 3: ████████████         15 (TODAY)
Day 4: ████████             10 (projected)
Day 5: ████                 5  (projected)

Velocity Metrics:
- Average: 4.5 tasks/day
- Yesterday: 5 tasks
- Today: 3 tasks (in progress)

Risk Assessment: ON TRACK
```


### Example 10

```text
TEAM PERFORMANCE REPORT - Week 11
=================================

By Agent:
┌─────────────────┬────────┬───────────┬─────────┬────────────┐
│ Agent           │ Completed │ Avg Time │ Quality │ Efficiency │
├─────────────────┼────────┼───────────┼─────────┼────────────┤
│ dev-frontend    │    8   │   3.2h    │   95%   │    125%    │
│ dev-backend     │    6   │   4.1h    │   98%   │    110%    │
│ test-developer  │    4   │   2.8h    │   100%  │    115%    │
└─────────────────┴────────┴───────────┴─────────┴────────────┘

By Task Type:
- Features: 12 completed (avg 3.8h)
- Bugfixes: 4 completed (avg 1.5h)
- Tests: 8 completed (avg 2.2h)

Quality Metrics:
- First-time pass rate: 88%
- Rework required: 2 tasks
- Blocked time: 4.5 hours total
```


### Example 11

```text
/task-report summary --from 2024-03-01 --to 2024-03-15
/task-report summary --last 7d
/task-report summary --this-month
```


### Example 12

```text
/task-report sprint --project authentication_system
```


### Example 13

```text
/task-report executive --format markdown
/task-report executive --format html
/task-report executive --format pdf
```


### Example 14

```text
/task-report summary --include completed,qa
/task-report summary --exclude on_hold
```


### Example 15

```text
/task-report critical-path
```


### Example 16

```text
/task-report bottlenecks
```


### Example 17

```text
/task-report resources
```


### Example 18

```text
/task-report risks
```


### Example 19

```text
/task-report gantt --weeks 2
```


### Example 20

```text
/task-report dependencies --visual
```


### Example 21

```text
/task-report flow --animated
```


### Example 22

```text
/task-report schedule daily-standup --at "9am"
/task-report schedule weekly-summary --every friday
```


### Example 23

```text
/task-report executive --email team@company.com
```


### Example 24

```text
/task-report compare --sprint 23 24
```


### Example 25

```text
/task-report trends --weeks 4
```


### Example 26

```text
/task-report standup --format slack
```


### Example 27

```text
/task-report sprint --include-velocity --include-burndown
```


### Example 28

```text
/task-report blockers --show-dependencies --show-resolution
```


### Example 29

```text
/task-report export-jira
/task-report export-asana
/task-report export-github
```


### Example 30

```text
/task-report api --generate-endpoint
```


---

## 🚀 Usage

**Reference this template:** `@command-task-report-command.md`


**Platform-specific:**
- **GitHub Copilot**: Add to `.github/copilot-instructions.md`
- **Augment Code**: Use `aug context add` command
- **Cursor/Windsurf**: Reference in chat with `@filename`
- **Claude**: Add to Project Knowledge
- **ChatGPT**: Add to Custom GPT configuration
