---
id: skill-task-execution-engine
type: skill
name: task-execution-engine
description: Execute implementation tasks from design documents using markdown checkboxes.
  Use when (1) implementing features from feature-design-assistant output, (2) resuming
  interrupted work, (3) batch executing tasks. Triggers on 'start implementation',
  'run tasks', 'resume'.
category: development
complexity: medium
keywords:
- api
- database
capabilities: []
token_estimate: 492
has_references: true
reference_count: 2
---

<!-- Converted from Claude Skill Template -->
<!-- Complexity: Medium -->
<!-- Estimated Tokens: ~492 -->
<!-- Includes Reference Materials -->

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




# task-execution-engine

> Execute implementation tasks from design documents using markdown checkboxes. Use when (1) implementing features from feature-design-assistant output, (2) resuming interrupted work, (3) batch executing tasks. Triggers on 'start implementation', 'run tasks', 'resume'.

# Feature Pipeline

Execute implementation tasks directly from design documents. Tasks are managed as markdown checkboxes - no separate session files needed.

## Quick Reference

```bash
# Get next task
python3 scripts/task_manager.py next --file <design.md>

# Mark task completed
python3 scripts/task_manager.py done --file <design.md> --task "Task Title"

# Mark task failed
python3 scripts/task_manager.py fail --file <design.md> --task "Task Title" --reason "..."

# Show status
python3 scripts/task_manager.py status --file <design.md>
```

## Task Format

Tasks are written as markdown checkboxes in the design document:

```markdown
## Implementation Tasks

- [ ] **Create User model** `priority:1` `phase:model`
  - files: src/models/user.py, tests/models/test_user.py
  - [ ] User model has email and password_hash fields
  - [ ] Email validation implemented
  - [ ] Password hashing uses bcrypt

- [ ] **Implement JWT utils** `priority:2` `phase:model`
  - files: src/utils/jwt.py
  - [ ] generate_token() creates valid JWT
  - [ ] verify_token() validates JWT

- [ ] **Create auth API** `priority:3` `phase:api` `deps:Create User model,Implement JWT utils`
  - files: src/api/auth.py
  - [ ] POST /register endpoint
  - [ ] POST /login endpoint
```

See [references/task-format.md](references/task-format.md) for full format specification.

## Execution Loop

```
LOOP until no tasks remain:
  1. GET next task (task_manager.py next)
  2. READ task details (files, criteria)
  3. IMPLEMENT the task
  4. VERIFY acceptance criteria
  5. UPDATE status (task_manager.py done/fail)
  6. CONTINUE
```

### Unattended Mode Rules

- **NO stopping** for questions
- **NO asking** for clarification
- Make autonomous decisions based on codebase patterns
- If blocked, mark as failed and continue

## Status Updates

Completed task:
```markdown
- [x] **Create User model** `priority:1` `phase:model` ✅
  - files: src/models/user.py
  - [x] User model has email field
  - [x] Password hashing implemented
```

Failed task:
```markdown
- [x] **Create User model** `priority:1` `phase:model` ❌
  - files: src/models/user.py
  - [ ] User model has email field
  - reason: Missing database configuration
```

## Resume / Recovery

To resume interrupted work, simply run again with the same design file:

```
/feature-pipeline docs/designs/xxx.md
```

The task manager will find the first uncompleted task and continue from there.

## Integration

This skill is typically triggered after `/feature-analyzer` completes:

```
User: /feature-analyzer implement user auth

Claude: [designs feature, generates task list]
        Design saved to docs/designs/2026-01-02-user-auth.md
        Ready to start implementation?

User: Yes / 开始实现

Claude: [executes tasks via task-execution-engine]
```


---


## 📚 Reference Materials


### Task Format

# Task Format Specification

## Basic Structure

```markdown
## Implementation Tasks

- [ ] **Task Title** `priority:N` `phase:PHASE` `deps:Dep1,Dep2`
  - files: file1.py, file2.py
  - [ ] Acceptance criterion 1
  - [ ] Acceptance criterion 2
```

## Task Line

```
- [ ] **Task Title** `priority:1` `phase:model` `deps:Other Task`
```

| Component | Required | Description |
|-----------|----------|-------------|
| `- [ ]` | Yes | Checkbox (unchecked) |
| `**Title**` | Yes | Task title in bold |
| `priority:N` | No | Priority 1-10 (default: 5, lower = higher) |
| `phase:X` | No | Phase: model, api, ui, test, docs |
| `deps:A,B` | No | Comma-separated dependency task titles |

## Task Details (Indented)

### Files Line

```markdown
  - files: src/models/user.py, tests/test_user.py
```

Comma-separated list of files to create/modify.

### Acceptance Criteria

```markdown
  - [ ] User model has email field
  - [ ] Password hashing uses bcrypt
```

Checkboxes for each acceptance criterion. All must be checked for task to be complete.

### Failure Reason (Auto-added)

```markdown
  - reason: Database connection failed
```

Added automatically when task is marked as failed.

## Status Markers

| Status | Checkbox | Marker |
|--------|----------|--------|
| Pending | `- [ ]` | (none) |
| Completed | `- [x]` | ✅ |
| Failed | `- [x]` | ❌ |

## Priority Order

1. Lower priority number = execute first
2. Dependencies must be completed first
3. Tasks with unsatisfied dependencies are "blocked"

## Examples

### Pending Task

```markdown
- [ ] **Create User model** `priority:1` `phase:model`
  - files: src/models/user.py
  - [ ] User model has email and password_hash fields
  - [ ] Email validation implemented
```

### Completed Task

```markdown
- [x] **Create User model** `priority:1` `phase:model` ✅
  - files: src/models/user.py
  - [x] User model has email and password_hash fields
  - [x] Email validation implemented
```

### Failed Task

```markdown
- [x] **Create User model** `priority:1` `phase:model` ❌
  - files: src/models/user.py
  - [ ] User model has email and password_hash fields
  - reason: bcrypt package not installed
```

### Task with Dependencies

```markdown
- [ ] **Create auth API** `priority:3` `phase:api` `deps:Create User model,Implement JWT`
  - files: src/api/auth.py
  - [ ] POST /register endpoint
  - [ ] POST /login endpoint
```

This task will not be selected by `next` until both "Create User model" and "Implement JWT" are completed.




### Workflow Guide

# Workflow Guide

从需求分析到自动化实现的完整流程。

## 流程概览

```
┌─────────────────────────────────────────────────────┐
│  /feature-analyzer 实现用户登录功能                   │
│  → 对话设计 → 生成 docs/designs/xxx.md               │
│  → "设计已完成，要开始实现吗？"                        │
└─────────────────────────────────────────────────────┘
                         ↓
              用户: "开始实现" / "Yes"
                         ↓
┌─────────────────────────────────────────────────────┐
│  /feature-pipeline docs/designs/xxx.md              │
│  → 读取任务列表 → 循环执行 → 更新 checkbox           │
└─────────────────────────────────────────────────────┘
                         ↓
┌─────────────────────────────────────────────────────┐
│  完成！"要提交到 Git 吗？"                           │
└─────────────────────────────────────────────────────┘
```

## 使用示例

### 完整流程

```
用户: /feature-analyzer 实现用户登录功能

Claude: [逐步对话理解需求...]
        [设计方案...]

        设计已保存到 docs/designs/2026-01-02-user-auth.md

        任务列表：
        1. Create User model (priority: 1)
        2. Implement JWT utils (priority: 2)
        3. Create auth API (priority: 3)

        设计已完成，要开始实现吗？

用户: 开始实现

Claude: 开始执行任务...

        ---TASK RESULT---
        task: Create User model
        status: completed
        files: [src/models/user.py]
        ---END TASK RESULT---

        ---TASK RESULT---
        task: Implement JWT utils
        status: completed
        files: [src/utils/jwt.py]
        ---END TASK RESULT---

        [继续执行...]

        所有任务完成！
        ✅ 3/3 tasks completed

        要提交到 Git 吗？
```

### 直接执行已有设计

```
用户: /feature-pipeline docs/designs/2026-01-02-user-auth.md

Claude: [读取文件，开始执行任务...]
```

### 恢复中断的工作

```
用户: /feature-pipeline docs/designs/2026-01-02-user-auth.md

Claude: 正在恢复...
        已完成: 2/5 任务
        从 "Create auth API" 继续...

        [继续执行剩余任务...]
```

## 设计文档格式

设计文档中的任务列表使用 markdown checkbox：

```markdown
# User Auth Design

## Overview
...

## Implementation Tasks

- [ ] **Create User model** `priority:1` `phase:model`
  - files: src/models/user.py
  - [ ] User model has email field
  - [ ] Password hashing implemented

- [ ] **Implement JWT utils** `priority:2` `phase:model`
  - files: src/utils/jwt.py
  - [ ] generate_token() works
  - [ ] verify_token() works

- [ ] **Create auth API** `priority:3` `phase:api` `deps:Create User model,Implement JWT utils`
  - files: src/api/auth.py
  - [ ] POST /login endpoint
  - [ ] POST /register endpoint
```

执行后：

```markdown
- [x] **Create User model** `priority:1` `phase:model` ✅
  - files: src/models/user.py
  - [x] User model has email field
  - [x] Password hashing implemented

- [x] **Implement JWT utils** `priority:2` `phase:model` ✅
  ...
```

## 优势

1. **可读性**: 设计文档直接可读
2. **Git 友好**: markdown diff 清晰
3. **简单**: 无需额外的 session 文件
4. **中断恢复**: 重新读取文件即可继续




---

## 🚀 Usage

**Reference this template:** `@skill-task-execution-engine.md`


**Platform-specific:**
- **GitHub Copilot**: Add to `.github/copilot-instructions.md`
- **Augment Code**: Use `aug context add` command
- **Cursor/Windsurf**: Reference in chat with `@filename`
- **Claude**: Add to Project Knowledge
- **ChatGPT**: Add to Custom GPT configuration
