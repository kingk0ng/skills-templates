---
id: command-feature-pipeline
type: command
name: Feature Pipeline
description: '---

  description: Execute implementation tasks from a design document. Tasks are tracked
  as markdown checkboxes directly in the design file.

  argument-hint: <design-file.md>

  allowed-tools: Read, Write, ...'
category: orchestration
complexity: medium
keywords:
- git
- go
capabilities: []
token_estimate: 612
has_scripts: true
languages:
- bash
- text
---

<!-- Converted from Claude Command Template -->
<!-- Complexity: Medium -->
<!-- Estimated Tokens: ~612 -->


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




# Feature Pipeline

> ---
description: Execute implementation tasks from a design document. Tasks are tracked as markdown checkboxes directly in the design file.
argument-hint: <design-file.md>
allowed-tools: Read, Write, ...

---
description: Execute implementation tasks from a design document. Tasks are tracked as markdown checkboxes directly in the design file.
argument-hint: <design-file.md>
allowed-capabilities: File operations, code editing, terminal access, searchTodoWrite, Task, AskUserQuestion
---

# Feature Pipeline

Execute implementation tasks from a design document using markdown checkboxes.

## Input Detection

`$ARGUMENTS` should be a path to a design markdown file (e.g., `docs/designs/xxx.md`)

If empty or unclear, ask user for the design file path.

## Phase 1: Initialize

1. Read the design file
2. Run `python3 .claude/skills/task-execution-engine/scripts/task_manager.py status --file <design.md>` to show current progress
3. If all tasks completed, report and exit
4. Otherwise, proceed to execution

## Phase 2: Execution Loop

**UNATTENDED MODE - No questions, no stopping**

```
LOOP:
  1. RUN: task_manager.py next --file <design.md> --json
  2. IF no task available → EXIT to Phase 3
  3. READ task details (files, criteria)
  4. IMPLEMENT the task
     - Create/modify files as specified
     - Follow codebase patterns
     - Run tests if applicable
  5. VERIFY acceptance criteria
  6. UPDATE status:
     - Success: task_manager.py done --file <design.md> --task "Title"
     - Failure: task_manager.py fail --file <design.md> --task "Title" --reason "..."
  7. OUTPUT result summary
  8. CONTINUE (go to step 1)
```

## Phase 3: Completion

1. Run `task_manager.py status --file <design.md>` to show final summary
2. Report:
   - Tasks completed
   - Tasks failed (with reasons)
   - Files modified
3. Ask if user wants to commit changes to git

## Implementation Guidelines

### For Each Task:

1. **Read files** specified in task's `files:` line
2. **Understand criteria** from the checkbox items
3. **Implement** following existing codebase patterns
4. **Verify** each acceptance criterion is met
5. **Update** the design file with completion status

### Decision Making:

- If unclear about implementation details → use codebase patterns
- If blocked by missing dependency → mark as failed, continue
- If needs external resource → mark as failed with reason, continue

### Status Updates:

```bash
# Mark task completed (updates checkbox to [x] ✅)
python3 .claude/skills/task-execution-engine/scripts/task_manager.py done \
  --file <design.md> --task "Task Title"

# Mark task failed (updates checkbox to [x] ❌ with reason)
python3 .claude/skills/task-execution-engine/scripts/task_manager.py fail \
  --file <design.md> --task "Task Title" --reason "Error description"
```

## Output Format

After each task:

```
---TASK RESULT---
task: Task Title
status: completed|failed
files: [list of modified files]
notes: Brief description
---END TASK RESULT---
```

## Error Handling

| Error | Action |
|-------|--------|
| Task implementation fails | Mark failed, continue to next |
| Script error | Log error, retry once, then fail task |
| No tasks available | Exit loop, show summary |
| File not found | Ask user for correct path |

## Key Rules

1. **NEVER stop** in the middle of execution loop
2. **NEVER ask** questions during execution
3. **ALWAYS** update task status in design file
4. **Exit only** when no pending tasks remain


---


## 💻 Code Examples


### Example 1

```text
LOOP:
  1. RUN: task_manager.py next --file <design.md> --json
  2. IF no task available → EXIT to Phase 3
  3. READ task details (files, criteria)
  4. IMPLEMENT the task
     - Create/modify files as specified
     - Follow codebase patterns
     - Run tests if applicable
  5. VERIFY acceptance criteria
  6. UPDATE status:
     - Success: task_manager.py done --file <design.md> --task "Title"
     - Failure: task_manager.py fail --file <design.md> --task "Title" --reason "..."
  7. OUTPUT result summary
  8. CONTINUE (go to step 1)
```


### Example 2

```bash
# Mark task completed (updates checkbox to [x] ✅)
python3 .claude/skills/task-execution-engine/scripts/task_manager.py done \
  --file <design.md> --task "Task Title"

# Mark task failed (updates checkbox to [x] ❌ with reason)
python3 .claude/skills/task-execution-engine/scripts/task_manager.py fail \
  --file <design.md> --task "Task Title" --reason "Error description"
```


### Example 3

```text
---TASK RESULT---
task: Task Title
status: completed|failed
files: [list of modified files]
notes: Brief description
---END TASK RESULT---
```


---

## 🚀 Usage

**Reference this template:** `@command-feature-pipeline.md`


**Platform-specific:**
- **GitHub Copilot**: Add to `.github/copilot-instructions.md`
- **Augment Code**: Use `aug context add` command
- **Cursor/Windsurf**: Reference in chat with `@filename`
- **Claude**: Add to Project Knowledge
- **ChatGPT**: Add to Custom GPT configuration
