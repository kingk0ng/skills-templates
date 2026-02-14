---
id: command-task-find-command
type: command
name: Task Find Command
description: Search and locate tasks across all orchestrations using various criteria....
category: orchestration
complexity: medium
keywords:
- audit
- performance
- qa
- security
- test
- vulnerability
capabilities: []
token_estimate: 852
---

<!-- Converted from Claude Command Template -->
<!-- Complexity: Medium -->
<!-- Estimated Tokens: ~852 -->


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




# Task Find Command

> Search and locate tasks across all orchestrations using various criteria....

# Task Find Command

Search and locate tasks across all orchestrations using various criteria.

## Usage

```
/task-find [search-term] [options]
```

## Description

Powerful search functionality to quickly locate tasks by ID, content, status, dependencies, or any other criteria. Supports regex, fuzzy matching, and complex queries.

## Basic Search

### By Task ID
```
/task-find TASK-001
/task-find TASK-*
```

### By Title/Content
```
/task-find "authentication"
/task-find "payment processing"
```

### By Status
```
/task-find --status in_progress
/task-find --status qa,completed
```

## Advanced Search

### Regular Expression
```
/task-find --regex "JWT|OAuth"
/task-find --regex "TASK-0[0-9]{2}"
```

### Fuzzy Search
```
/task-find --fuzzy "autentication"  # finds "authentication"
/task-find --fuzzy "paymnt"         # finds "payment"
```

### Multiple Criteria
```
/task-find --status todos --priority high --type feature
/task-find --agent dev-backend --created-after yesterday
```

## Search Operators

### Boolean Operators
```
/task-find "auth AND login"
/task-find "payment OR billing"
/task-find "security NOT test"
```

### Field-Specific Search
```
/task-find title:"user authentication"
/task-find description:"security vulnerability"
/task-find agent:dev-frontend
/task-find blocks:TASK-001
```

### Date Ranges
```
/task-find --created "2024-03-10..2024-03-15"
/task-find --modified "last 3 days"
/task-find --completed "this week"
```

## Output Formats

### Default List View
```
Found 3 tasks matching "authentication":

TASK-001: Implement JWT authentication
  Status: in_progress | Agent: dev-frontend | Created: 2024-03-15
  Location: /task-orchestration/03_15_2024/auth_system/tasks/in_progress/

TASK-004: Add OAuth2 authentication  
  Status: todos | Priority: high | Blocked by: TASK-001
  Location: /task-orchestration/03_15_2024/auth_system/tasks/todos/

TASK-007: Authentication middleware tests
  Status: todos | Type: test | Depends on: TASK-001
  Location: /task-orchestration/03_15_2024/auth_system/tasks/todos/
```

### Detailed View
```
/task-find TASK-001 --detailed
```
Shows full task content including description, implementation notes, and history.

### Tree View
```
/task-find --tree --root TASK-001
```
Shows task and all its dependencies in tree format.

## Filtering Options

### By Orchestration
```
/task-find --orchestration "03_15_2024/payment_system"
/task-find --orchestration "*/auth_*"
```

### By Properties
```
/task-find --has-dependencies
/task-find --no-dependencies
/task-find --blocking-others
/task-find --effort ">4h"
```

### By Relationships
```
/task-find --depends-on TASK-001
/task-find --blocks TASK-005
/task-find --related-to TASK-003
```

## Special Searches

### Find Circular Dependencies
```
/task-find --circular-deps
```

### Find Orphaned Tasks
```
/task-find --orphaned
```

### Find Duplicate Tasks
```
/task-find --duplicates
```

### Find Stale Tasks
```
/task-find --stale --days 7
```

## Quick Filters

### Ready to Start
```
/task-find --ready
```
Shows todos with no blocking dependencies.

### Critical Path
```
/task-find --critical-path
```
Shows tasks on the critical path.

### High Impact
```
/task-find --high-impact
```
Shows tasks blocking multiple others.

## Export Options

### Copy Results
```
/task-find "auth" --copy
```
Copies results to clipboard.

### Export Paths
```
/task-find --status todos --export paths
```
Exports file paths for batch operations.

### Generate Report
```
/task-find --report
```
Creates detailed search report.

## Examples

### Example 1: Find Work for Agent
```
/task-find --status todos --suitable-for dev-frontend --ready
```

### Example 2: Find Blocking Issues
```
/task-find --status on_hold --show-blockers
```

### Example 3: Security Audit
```
/task-find "security OR auth OR permission" --type "feature,bugfix"
```

### Example 4: Sprint Planning
```
/task-find --status todos --effort "<4h" --no-dependencies
```

## Search Shortcuts

### Recent Tasks
```
/task-find --recent 10
```

### My Tasks
```
/task-find --mine  # Uses current agent context
```

### Modified Today
```
/task-find --modified today
```

## Complex Queries

### Compound Search
```
/task-find '(title:"auth" OR description:"security") AND status:todos AND -blocks:*'
```

### Saved Searches
```
/task-find --save "security-todos"
/task-find --load "security-todos"
```

## Performance Tips

1. **Use Indexes**: Status and ID searches are fastest
2. **Narrow Scope**: Specify orchestration when possible
3. **Cache Results**: Use `--cache` for repeated searches
4. **Limit Results**: Use `--limit 20` for large result sets

## Integration

### With Other Commands
```
/task-find "payment" --status todos | /task-move in_progress
```

### Batch Operations
```
/task-find --filter "priority:low" | /task-update priority:medium
```

## Notes

- Searches across all task files in task-orchestration/
- Case-insensitive by default (use --case for case-sensitive)
- Results sorted by relevance unless specified otherwise
- Supports command chaining with pipe operator
- Search index updated automatically on file changes

---


## 💻 Code Examples


### Example 1

```text
/task-find [search-term] [options]
```


### Example 2

```text
/task-find TASK-001
/task-find TASK-*
```


### Example 3

```text
/task-find "authentication"
/task-find "payment processing"
```


### Example 4

```text
/task-find --status in_progress
/task-find --status qa,completed
```


### Example 5

```text
/task-find --regex "JWT|OAuth"
/task-find --regex "TASK-0[0-9]{2}"
```


### Example 6

```text
/task-find --fuzzy "autentication"  # finds "authentication"
/task-find --fuzzy "paymnt"         # finds "payment"
```


### Example 7

```text
/task-find --status todos --priority high --type feature
/task-find --agent dev-backend --created-after yesterday
```


### Example 8

```text
/task-find "auth AND login"
/task-find "payment OR billing"
/task-find "security NOT test"
```


### Example 9

```text
/task-find title:"user authentication"
/task-find description:"security vulnerability"
/task-find agent:dev-frontend
/task-find blocks:TASK-001
```


### Example 10

```text
/task-find --created "2024-03-10..2024-03-15"
/task-find --modified "last 3 days"
/task-find --completed "this week"
```


### Example 11

```text
Found 3 tasks matching "authentication":

TASK-001: Implement JWT authentication
  Status: in_progress | Agent: dev-frontend | Created: 2024-03-15
  Location: /task-orchestration/03_15_2024/auth_system/tasks/in_progress/

TASK-004: Add OAuth2 authentication  
  Status: todos | Priority: high | Blocked by: TASK-001
  Location: /task-orchestration/03_15_2024/auth_system/tasks/todos/

TASK-007: Authentication middleware tests
  Status: todos | Type: test | Depends on: TASK-001
  Location: /task-orchestration/03_15_2024/auth_system/tasks/todos/
```


### Example 12

```text
/task-find TASK-001 --detailed
```


### Example 13

```text
/task-find --tree --root TASK-001
```


### Example 14

```text
/task-find --orchestration "03_15_2024/payment_system"
/task-find --orchestration "*/auth_*"
```


### Example 15

```text
/task-find --has-dependencies
/task-find --no-dependencies
/task-find --blocking-others
/task-find --effort ">4h"
```


### Example 16

```text
/task-find --depends-on TASK-001
/task-find --blocks TASK-005
/task-find --related-to TASK-003
```


### Example 17

```text
/task-find --circular-deps
```


### Example 18

```text
/task-find --orphaned
```


### Example 19

```text
/task-find --duplicates
```


### Example 20

```text
/task-find --stale --days 7
```


### Example 21

```text
/task-find --ready
```


### Example 22

```text
/task-find --critical-path
```


### Example 23

```text
/task-find --high-impact
```


### Example 24

```text
/task-find "auth" --copy
```


### Example 25

```text
/task-find --status todos --export paths
```


### Example 26

```text
/task-find --report
```


### Example 27

```text
/task-find --status todos --suitable-for dev-frontend --ready
```


### Example 28

```text
/task-find --status on_hold --show-blockers
```


### Example 29

```text
/task-find "security OR auth OR permission" --type "feature,bugfix"
```


### Example 30

```text
/task-find --status todos --effort "<4h" --no-dependencies
```


### Example 31

```text
/task-find --recent 10
```


### Example 32

```text
/task-find --mine  # Uses current agent context
```


### Example 33

```text
/task-find --modified today
```


### Example 34

```text
/task-find '(title:"auth" OR description:"security") AND status:todos AND -blocks:*'
```


### Example 35

```text
/task-find --save "security-todos"
/task-find --load "security-todos"
```


### Example 36

```text
/task-find "payment" --status todos | /task-move in_progress
```


### Example 37

```text
/task-find --filter "priority:low" | /task-update priority:medium
```


---

## 🚀 Usage

**Reference this template:** `@command-task-find-command.md`


**Platform-specific:**
- **GitHub Copilot**: Add to `.github/copilot-instructions.md`
- **Augment Code**: Use `aug context add` command
- **Cursor/Windsurf**: Reference in chat with `@filename`
- **Claude**: Add to Project Knowledge
- **ChatGPT**: Add to Custom GPT configuration
