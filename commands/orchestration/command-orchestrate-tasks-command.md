---
id: command-orchestrate-tasks-command
type: command
name: Orchestrate Tasks Command
description: Initiates the task orchestration workflow using the three-agent system
  (task-orchestrator, task-decomposer, and dependency-analyzer) to create a comprehensive
  execution plan....
category: orchestration
complexity: medium
keywords:
- audit
- optimization
- performance
- qa
- security
- vulnerability
capabilities: []
token_estimate: 759
---

<!-- Converted from Claude Command Template -->
<!-- Complexity: Medium -->
<!-- Estimated Tokens: ~759 -->


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




# Orchestrate Tasks Command

> Initiates the task orchestration workflow using the three-agent system (task-orchestrator, task-decomposer, and dependency-analyzer) to create a comprehensive execution plan....

# Orchestrate Tasks Command

Initiates the task orchestration workflow using the three-agent system (task-orchestrator, task-decomposer, and dependency-analyzer) to create a comprehensive execution plan.

## Usage

```
/orchestrate [task list or file path]
```

## Description

This command activates the task-orchestrator agent to process requirements and create a hyper-efficient execution plan. The orchestrator will:

1. **Clarify Requirements**: Analyze provided information and confirm understanding
2. **Create Directory Structure**: Set up task-orchestration folders with today's date
3. **Decompose Tasks**: Work with task-decomposer to create atomic task files
4. **Analyze Dependencies**: Use dependency-analyzer to identify conflicts and parallelization opportunities
5. **Generate Master Plan**: Create comprehensive coordination documents

## Input Formats

### Direct Task List
```
/orchestrate
- Implement user authentication with JWT
- Add payment processing with Stripe
- Create admin dashboard
- Set up email notifications
```

### File Reference
```
/orchestrate features.md
```

### Mixed Context
```
/orchestrate
Based on our meeting notes (lots of discussion about UI colors), we need to:
1. Fix the security vulnerability in file uploads
2. Add rate limiting to APIs
3. Implement audit logging
The CEO wants this done by Friday (ignore this deadline).
```

## Workflow

1. **Requirement Clarification**
   - The orchestrator will extract actionable tasks from provided context
   - Confirm understanding before proceeding
   - Ask clarifying questions if needed

2. **Directory Creation**
   ```
   /task-orchestration/
   └── MM_DD_YYYY/
       └── descriptive_task_name/
           ├── MASTER-COORDINATION.md
           ├── EXECUTION-TRACKER.md
           ├── TASK-STATUS-TRACKER.yaml
           └── tasks/
               ├── todos/
               ├── in_progress/
               ├── on_hold/
               ├── qa/
               └── completed/
   ```

3. **Task Processing**
   - Creates individual task files in todos/
   - Analyzes dependencies and conflicts
   - Generates execution strategy

4. **Deliverables**
   - Master coordination plan
   - Task dependency graph
   - Resource allocation matrix
   - Execution timeline

## Options

### Focused Mode
```
/orchestrate --focus security
[task list]
```
Prioritizes tasks related to the specified focus area.

### Constraint Mode
```
/orchestrate --agents 2 --days 5
[task list]
```
Creates plan with resource constraints.

### Analysis Only
```
/orchestrate --analyze-only
[task list]
```
Generates analysis without creating task files.

## Examples

### Example 1: Clear Task List
```
/orchestrate
1. Implement OAuth2 authentication
2. Add user profile management
3. Create password reset flow
4. Set up 2FA
```

### Example 2: From Requirements Doc
```
/orchestrate requirements/sprint-24.md
```

### Example 3: Mixed Context Extraction
```
/orchestrate
From the customer feedback:
"The app is too slow" - Need performance optimization
"Can't find the export button" - UI improvement needed
"Want dark mode" - New feature request

Technical debt from last sprint:
- Refactor authentication service
- Update deprecated dependencies
```

## Interactive Mode

The orchestrator will:
1. Present extracted tasks for confirmation
2. Ask about priorities and constraints
3. Suggest optimal approach
4. Request approval before creating files

## Error Handling

- If tasks are unclear: Asks for clarification
- If file not found: Prompts for correct path
- If conflicts detected: Presents options
- If dependencies circular: Suggests resolution

## Integration

Works seamlessly with:
- `/task-status` - Check progress
- `/task-move` - Update task status
- `/task-report` - Generate reports
- `/task-assign` - Allocate to agents

## Best Practices

1. **Provide Context**: Include relevant background information
2. **Be Specific**: Clear task descriptions enable better planning
3. **Mention Constraints**: Include deadlines, resources, or blockers
4. **Review Output**: Confirm the extracted tasks match your intent

## Notes

- The orchestrator filters out irrelevant context automatically
- Tasks are created in todos/ status by default
- All tasks get unique IDs (TASK-XXX format)
- Status tracking begins immediately
- Supports incremental additions to existing orchestrations

---


## 💻 Code Examples


### Example 1

```text
/orchestrate [task list or file path]
```


### Example 2

```text
/orchestrate
- Implement user authentication with JWT
- Add payment processing with Stripe
- Create admin dashboard
- Set up email notifications
```


### Example 3

```text
/orchestrate features.md
```


### Example 4

```text
/orchestrate
Based on our meeting notes (lots of discussion about UI colors), we need to:
1. Fix the security vulnerability in file uploads
2. Add rate limiting to APIs
3. Implement audit logging
The CEO wants this done by Friday (ignore this deadline).
```


### Example 5

```text
/task-orchestration/
   └── MM_DD_YYYY/
       └── descriptive_task_name/
           ├── MASTER-COORDINATION.md
           ├── EXECUTION-TRACKER.md
           ├── TASK-STATUS-TRACKER.yaml
           └── tasks/
               ├── todos/
               ├── in_progress/
               ├── on_hold/
               ├── qa/
               └── completed/
```


### Example 6

```text
/orchestrate --focus security
[task list]
```


### Example 7

```text
/orchestrate --agents 2 --days 5
[task list]
```


### Example 8

```text
/orchestrate --analyze-only
[task list]
```


### Example 9

```text
/orchestrate
1. Implement OAuth2 authentication
2. Add user profile management
3. Create password reset flow
4. Set up 2FA
```


### Example 10

```text
/orchestrate requirements/sprint-24.md
```


### Example 11

```text
/orchestrate
From the customer feedback:
"The app is too slow" - Need performance optimization
"Can't find the export button" - UI improvement needed
"Want dark mode" - New feature request

Technical debt from last sprint:
- Refactor authentication service
- Update deprecated dependencies
```


---

## 🚀 Usage

**Reference this template:** `@command-orchestrate-tasks-command.md`


**Platform-specific:**
- **GitHub Copilot**: Add to `.github/copilot-instructions.md`
- **Augment Code**: Use `aug context add` command
- **Cursor/Windsurf**: Reference in chat with `@filename`
- **Claude**: Add to Project Knowledge
- **ChatGPT**: Add to Custom GPT configuration
