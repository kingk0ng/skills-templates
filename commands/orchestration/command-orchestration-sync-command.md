---
id: command-orchestration-sync-command
type: command
name: Orchestration Sync Command
description: Synchronize task status with git commits, ensuring consistency between
  version control and task tracking....
category: orchestration
complexity: medium
keywords:
- audit
- ci/cd
- git
- qa
- test
- testing
capabilities: []
token_estimate: 1089
---

<!-- Converted from Claude Command Template -->
<!-- Complexity: Medium -->
<!-- Estimated Tokens: ~1,089 -->


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




# Orchestration Sync Command

> Synchronize task status with git commits, ensuring consistency between version control and task tracking....

# Orchestration Sync Command

Synchronize task status with git commits, ensuring consistency between version control and task tracking.

## Usage

```
/orchestration/sync [options]
```

## Description

Analyzes git history and task status to identify discrepancies, automatically updating task tracking based on commit evidence and maintaining bidirectional consistency.

## Basic Commands

### Full Sync
```
/orchestration/sync
```
Performs complete synchronization between git and task status.

### Check Sync Status
```
/orchestration/sync --check
```
Reports inconsistencies without making changes.

### Sync Specific Orchestration
```
/orchestration/sync --date 03_15_2024 --project auth_system
```

## Sync Operations

### Git → Task Status
Updates task status based on commit messages:
```
Found commits:
- feat(auth): implement JWT validation (TASK-003) ✓
  Status: in_progress → qa (based on commit)
  
- test(auth): add JWT validation tests (TASK-003) ✓
  Status: qa → completed (tests indicate completion)
  
- fix(auth): resolve token expiration (TASK-007) ✓
  Status: todos → in_progress (work started)
```

### Task Status → Git
Identifies tasks marked complete without commits:
```
Status Discrepancies:
- TASK-005: Marked 'completed' but no commits found
- TASK-008: In 'qa' but no implementation commits
- TASK-010: Multiple commits but still in 'todos'
```

## Detection Patterns

### Commit Pattern Matching
```
Patterns detected:
- "feat(auth): implement" → Implementation complete
- "test(auth): add" → Testing phase
- "fix(auth): resolve" → Bug fix complete
- "docs(auth): update" → Documentation done
- "refactor(auth):" → Code improvement
```

### Task Reference Extraction
```
Scanning commits for task references:
- Explicit: "Task: TASK-003" ✓
- In body: "Implements TASK-003" ✓
- Branch name: "feature/TASK-003-jwt" ✓
- PR title: "TASK-003: JWT implementation" ✓
```

## Sync Rules

### Automatic Status Updates
```yaml
sync_rules:
  commit_patterns:
    - pattern: "feat.*TASK-(\d+)"
      action: "move to qa if in_progress"
    - pattern: "test.*TASK-(\d+).*pass"
      action: "move to completed if in qa"
    - pattern: "fix.*TASK-(\d+)"
      action: "move to qa if in_progress"
    - pattern: "WIP.*TASK-(\d+)"
      action: "keep in in_progress"
```

### Conflict Resolution
```
Conflict detected for TASK-003:
- Git evidence: 3 commits, tests passing
- Task status: in_progress
- Recommended: Move to completed

Resolution options:
[1] Trust git (move to completed)
[2] Trust tracker (keep in_progress)
[3] Manual review
[4] Skip
```

## Analysis Reports

### Sync Summary
```
Synchronization Report
======================

Analyzed: 45 commits across 3 branches
Tasks referenced: 12
Status updates needed: 4

Updates to apply:
- TASK-003: in_progress → completed (3 commits)
- TASK-007: todos → in_progress (1 commit)
- TASK-009: qa → completed (tests added)
- TASK-011: on_hold → in_progress (blocker resolved)

Warnings:
- TASK-005: Completed without commits
- TASK-013: Commits without task reference
```

### Detailed Analysis
```
Task: TASK-003 - JWT Implementation
Current Status: in_progress
Git Evidence:
  - feat(auth): implement JWT validation (2 days ago)
  - test(auth): add validation tests (1 day ago)
  - fix(auth): handle edge cases (1 day ago)
  
Recommendation: Move to completed
Confidence: High (95%)
```

## Options

### Dry Run
```
/orchestration/sync --dry-run
```
Shows what would change without applying updates.

### Force Sync
```
/orchestration/sync --force
```
Applies all recommendations without prompting.

### Time Range
```
/orchestration/sync --since "1 week ago"
```
Only analyzes recent commits.

### Branch Specific
```
/orchestration/sync --branch feature/auth
```
Syncs only tasks related to specific branch.

## Integration Features

### Update Tracking Files
```
/orchestration/sync --update-trackers
```
Updates TASK-STATUS-TRACKER.yaml with:
```yaml
git_tracking:
  TASK-003:
    status_from_git: completed
    confidence: 0.95
    evidence:
      - commit: abc123
        message: "feat(auth): implement JWT"
        date: "2024-03-13"
      - commit: def456
        message: "test(auth): add tests"
        date: "2024-03-14"
```

### Generate Commit Report
```
/orchestration/sync --commit-report
```
Creates report of all task-related commits.

### Fix Orphaned Commits
```
/orchestration/sync --link-orphans
```
Associates commits without task references.

## Sync Strategies

### Conservative
```
/orchestration/sync --conservative
```
Only updates with high confidence matches.

### Aggressive
```
/orchestration/sync --aggressive
```
Updates based on any evidence.

### Interactive
```
/orchestration/sync --interactive
```
Prompts for each potential update.

## Examples

### Example 1: Daily Sync
```
/orchestration/sync --since yesterday

Quick sync results:
- 5 commits analyzed
- 2 tasks updated
- All changes applied successfully
```

### Example 2: Branch Merge Sync
```
/orchestration/sync --after-merge feature/auth

Post-merge sync:
- 15 commits from feature/auth
- 5 tasks moved to completed
- 2 tasks have test failures (kept in qa)
```

### Example 3: Audit Mode
```
/orchestration/sync --audit --report

Audit Report:
- Tasks with commits: 85%
- Commits with task refs: 92%
- Average commits per task: 2.3
- Orphaned commits: 3
```

## Webhook Integration

### Auto-sync on Push
```yaml
git_hooks:
  post-commit: /orchestration/sync --last-commit
  post-merge: /orchestration/sync --branch HEAD
```

## Best Practices

1. **Regular Syncs**: Run daily or after major commits
2. **Review Before Force**: Check dry-run output first
3. **Maintain References**: Include task IDs in commits
4. **Handle Conflicts**: Don't ignore sync warnings
5. **Document Decisions**: Note why status differs from git

## Configuration

### Sync Preferences
```yaml
sync_config:
  auto_sync: true
  confidence_threshold: 0.8
  require_tests: true
  trust_git_over_tracker: true
  patterns:
    - implementation: "feat|feature"
    - testing: "test|spec"
    - completion: "done|complete|finish"
```

## Notes

- Requires git access to all relevant branches
- Preserves manual status overrides with flags
- Supports custom commit message patterns
- Integrates with CI/CD for automated syncing

---


## 💻 Code Examples


### Example 1

```text
/orchestration/sync [options]
```


### Example 2

```text
/orchestration/sync
```


### Example 3

```text
/orchestration/sync --check
```


### Example 4

```text
/orchestration/sync --date 03_15_2024 --project auth_system
```


### Example 5

```text
Found commits:
- feat(auth): implement JWT validation (TASK-003) ✓
  Status: in_progress → qa (based on commit)
  
- test(auth): add JWT validation tests (TASK-003) ✓
  Status: qa → completed (tests indicate completion)
  
- fix(auth): resolve token expiration (TASK-007) ✓
  Status: todos → in_progress (work started)
```


### Example 6

```text
Status Discrepancies:
- TASK-005: Marked 'completed' but no commits found
- TASK-008: In 'qa' but no implementation commits
- TASK-010: Multiple commits but still in 'todos'
```


### Example 7

```text
Patterns detected:
- "feat(auth): implement" → Implementation complete
- "test(auth): add" → Testing phase
- "fix(auth): resolve" → Bug fix complete
- "docs(auth): update" → Documentation done
- "refactor(auth):" → Code improvement
```


### Example 8

```text
Scanning commits for task references:
- Explicit: "Task: TASK-003" ✓
- In body: "Implements TASK-003" ✓
- Branch name: "feature/TASK-003-jwt" ✓
- PR title: "TASK-003: JWT implementation" ✓
```


### Example 9

```yaml
sync_rules:
  commit_patterns:
    - pattern: "feat.*TASK-(\d+)"
      action: "move to qa if in_progress"
    - pattern: "test.*TASK-(\d+).*pass"
      action: "move to completed if in qa"
    - pattern: "fix.*TASK-(\d+)"
      action: "move to qa if in_progress"
    - pattern: "WIP.*TASK-(\d+)"
      action: "keep in in_progress"
```


### Example 10

```text
Conflict detected for TASK-003:
- Git evidence: 3 commits, tests passing
- Task status: in_progress
- Recommended: Move to completed

Resolution options:
[1] Trust git (move to completed)
[2] Trust tracker (keep in_progress)
[3] Manual review
[4] Skip
```


### Example 11

```text
Synchronization Report
======================

Analyzed: 45 commits across 3 branches
Tasks referenced: 12
Status updates needed: 4

Updates to apply:
- TASK-003: in_progress → completed (3 commits)
- TASK-007: todos → in_progress (1 commit)
- TASK-009: qa → completed (tests added)
- TASK-011: on_hold → in_progress (blocker resolved)

Warnings:
- TASK-005: Completed without commits
- TASK-013: Commits without task reference
```


### Example 12

```text
Task: TASK-003 - JWT Implementation
Current Status: in_progress
Git Evidence:
  - feat(auth): implement JWT validation (2 days ago)
  - test(auth): add validation tests (1 day ago)
  - fix(auth): handle edge cases (1 day ago)
  
Recommendation: Move to completed
Confidence: High (95%)
```


### Example 13

```text
/orchestration/sync --dry-run
```


### Example 14

```text
/orchestration/sync --force
```


### Example 15

```text
/orchestration/sync --since "1 week ago"
```


### Example 16

```text
/orchestration/sync --branch feature/auth
```


### Example 17

```text
/orchestration/sync --update-trackers
```


### Example 18

```yaml
git_tracking:
  TASK-003:
    status_from_git: completed
    confidence: 0.95
    evidence:
      - commit: abc123
        message: "feat(auth): implement JWT"
        date: "2024-03-13"
      - commit: def456
        message: "test(auth): add tests"
        date: "2024-03-14"
```


### Example 19

```text
/orchestration/sync --commit-report
```


### Example 20

```text
/orchestration/sync --link-orphans
```


### Example 21

```text
/orchestration/sync --conservative
```


### Example 22

```text
/orchestration/sync --aggressive
```


### Example 23

```text
/orchestration/sync --interactive
```


### Example 24

```text
/orchestration/sync --since yesterday

Quick sync results:
- 5 commits analyzed
- 2 tasks updated
- All changes applied successfully
```


### Example 25

```text
/orchestration/sync --after-merge feature/auth

Post-merge sync:
- 15 commits from feature/auth
- 5 tasks moved to completed
- 2 tasks have test failures (kept in qa)
```


### Example 26

```text
/orchestration/sync --audit --report

Audit Report:
- Tasks with commits: 85%
- Commits with task refs: 92%
- Average commits per task: 2.3
- Orphaned commits: 3
```


### Example 27

```yaml
git_hooks:
  post-commit: /orchestration/sync --last-commit
  post-merge: /orchestration/sync --branch HEAD
```


### Example 28

```yaml
sync_config:
  auto_sync: true
  confidence_threshold: 0.8
  require_tests: true
  trust_git_over_tracker: true
  patterns:
    - implementation: "feat|feature"
    - testing: "test|spec"
    - completion: "done|complete|finish"
```


---

## 🚀 Usage

**Reference this template:** `@command-orchestration-sync-command.md`


**Platform-specific:**
- **GitHub Copilot**: Add to `.github/copilot-instructions.md`
- **Augment Code**: Use `aug context add` command
- **Cursor/Windsurf**: Reference in chat with `@filename`
- **Claude**: Add to Project Knowledge
- **ChatGPT**: Add to Custom GPT configuration
