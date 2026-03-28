---
id: skill-gcc
type: skill
name: gcc
description: Git Context Controller (GCC) - Manages agent memory as a versioned file
  system under .GCC/. This skill should be used when working on multi-step projects
  that benefit from structured memory persistence, milestone tracking, branching for
  alternative approaches, and cross-session context recovery. Triggers on /gcc commands
  or natural language like 'commit this progress', 'branch to try an alternative',
  'merge results', 'recover context'.
category: git
complexity: medium
keywords:
- git
- go
capabilities: []
token_estimate: 1145
has_references: true
reference_count: 1
---

<!-- Converted from Claude Skill Template -->
<!-- Complexity: Medium -->
<!-- Estimated Tokens: ~1,145 -->
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




# gcc

> Git Context Controller (GCC) - Manages agent memory as a versioned file system under .GCC/. This skill should be used when working on multi-step projects that benefit from structured memory persistence, milestone tracking, branching for alternative approaches, and cross-session context recovery. Triggers on /gcc commands or natural language like 'commit this progress', 'branch to try an alternative', 'merge results', 'recover context'.

# Git Context Controller (GCC)

## Overview

GCC transforms agent memory from a passive token stream into a structured, versioned file system under `.GCC/`. Inspired by Git, it provides four operations — COMMIT, BRANCH, MERGE, CONTEXT — to persist milestones, explore alternatives in isolation, synthesize results, and recover historical context efficiently.

## Initialization

On first use, check if `.GCC/` exists in the project root. If not, run `scripts/gcc_init.sh` to create the directory structure:

```
.GCC/
├── main.md          # Global roadmap and objectives
├── metadata.yaml    # Infrastructure state (branches, file tree, config)
├── commit.md        # Commit history for main branch
├── log.md           # OTA execution log for main branch
└── branches/        # Isolated workspaces for experiments
    └── <branch-name>/
        ├── commit.md
        ├── log.md
        └── summary.md
```

For detailed file format specifications, read `references/file_formats.md`.

## Configuration

GCC behavior is controlled via `metadata.yaml`:

- `proactive_commits: true` — Automatically suggest commits after completing coherent sub-tasks
- `proactive_commits: false` — Only commit when explicitly requested

Toggle with: "enable/disable proactive commits" or by editing `metadata.yaml`.

## Commands

### COMMIT

Persist a milestone on the current branch.

**Triggers**: `/gcc commit <summary>`, "commit this progress", "save this milestone", "checkpoint"

**Procedure**:
1. Read the current branch's `commit.md` to determine the next commit number
2. Append a new entry to `commit.md` with:
   - Sequential ID (e.g., `[C004]`)
   - Date (UTC ISO 8601)
   - Current branch name
   - Branch purpose (from `summary.md` if on a branch, or from `main.md`)
   - Previous progress summary (1-2 sentences from last commit)
   - This commit's contribution (detailed technical description with files touched)
3. Append an OTA entry to `log.md` recording the commit action
4. Update `metadata.yaml` file tree if files were created/modified
5. If on main branch, update milestones section in `main.md`

**Proactive behavior**: When `proactive_commits: true`, suggest a commit after:
- Completing a function, module, or coherent unit of work
- Fixing a bug and verifying the fix
- Finishing a research/exploration phase with conclusions
- Any point where losing context would mean re-doing significant work

### BRANCH

Create an isolated workspace for exploring an alternative approach.

**Triggers**: `/gcc branch <name>`, "branch to try...", "explore alternative...", "experiment with..."

**Procedure**:
1. Create `.GCC/branches/<branch-name>/` directory
2. Create `summary.md` with: purpose, parent branch, creation date, key hypotheses
3. Create empty `commit.md` and `log.md` for the branch
4. Update `metadata.yaml` to register the new branch
5. Update `main.md` Active Branches section
6. Log the branch creation in the parent branch's `log.md`

From this point, all COMMITs and OTA logs go to the branch-specific files until a MERGE or explicit branch switch.

### MERGE

Integrate a completed branch back into the main flow.

**Triggers**: `/gcc merge <branch>`, "merge results from...", "integrate the experiment", "branch X is done"

**Procedure**:
1. Read the branch's `summary.md` and `commit.md` to understand outcomes
2. Append a synthesis commit to main's `commit.md` summarizing:
   - What was tried
   - What was learned
   - What is being integrated (or why the branch is being abandoned)
3. Update `main.md`:
   - Add milestone entry with branch results
   - Remove from Active Branches
   - Update objectives if applicable
4. Update `metadata.yaml`: set branch status to `merged` or `abandoned`
5. Log the merge in main's `log.md`

### CONTEXT

Retrieve historical memory at different resolution levels.

**Triggers**: `/gcc context <flag>`, "what did we do on...", "recover context", "show me the history", "where were we"

**Flags**:

- `--branch [name]` — Read `summary.md` and latest commits for a specific branch (or current branch if no name). Provides high-level understanding of what happened and why.

- `--log [n]` — Read last N entries (default 20) from the current branch's `log.md`. Provides fine-grained OTA traces for debugging or resuming interrupted work.

- `--metadata` — Read `metadata.yaml` to recover project structure: file tree, dependencies, active branches, configuration.

- `--full` — Read `main.md` for the complete project roadmap, all milestones, and active branches. Use for cross-session recovery or handoff to another agent.

When no flag is specified, default to `--branch` for the current active branch.

## OTA Logging

Throughout all work (not just during explicit commands), maintain the OTA execution log:

1. **Observation**: What was noticed or discovered
2. **Thought**: Reasoning about what to do next
3. **Action**: What action was taken

Append entries to the active branch's `log.md`. Keep a maximum of 50 entries; when exceeding, remove the oldest entries. Each entry includes a sequential ID, timestamp, and branch name.

Log OTA entries at meaningful decision points — not every single action, but significant observations, strategy changes, and outcomes.

## Cross-Session Recovery

When starting a new session on an existing project with `.GCC/`:

1. Read `metadata.yaml` to understand project state and active branches
2. Read `main.md` for the global roadmap and objectives
3. Read the active branch's latest commits and log entries
4. Resume work with full context of what was accomplished and what remains

## Natural Language Mapping

| User says | Command |
|---|---|
| "save/checkpoint/persist this" | COMMIT |
| "try a different approach" | BRANCH |
| "that experiment worked, integrate it" | MERGE |
| "where were we?" / "what's the status?" | CONTEXT --full |
| "what happened on branch X?" | CONTEXT --branch X |
| "show recent activity" | CONTEXT --log |
| "what files do we have?" | CONTEXT --metadata |
| "enable/disable auto-commits" | Toggle `proactive_commits` in metadata.yaml |


---


## 📚 Reference Materials


### File_Formats

# GCC File Format Reference

## main.md

The global roadmap. Updated on every MERGE and periodically on significant COMMITs.

```markdown
# Project Roadmap

## Objectives
- [ ] Objective 1
- [x] Objective 2 (completed)

## Milestones
### M1: Feature X implemented
- Branch: feature-x
- Commits: 3
- Status: merged

### M2: Bug fix Y
- Branch: fix-y
- Commits: 1
- Status: active

## Active Branches
- `experiment-z`: Testing alternative approach for caching
```

## commit.md

Each commit entry captures the full reasoning context, not just a diff summary.

```markdown
## [C003] Implement retry logic for API calls
- **Date**: 2025-01-15T10:30:00Z
- **Branch**: feature-resilience
- **Branch Purpose**: Add fault tolerance to external API integrations
- **Previous Progress**: Identified failure patterns in logs; designed retry strategy with exponential backoff
- **This Commit's Contribution**: Implemented `retry_with_backoff(fn, max_retries=3)` in `utils/http.py`. Added unit tests covering timeout, 5xx, and network error scenarios. Validated against staging API.
- **Files touched**: utils/http.py, tests/test_http.py
```

## log.md

Fine-grained OTA (Observation-Thought-Action) trace entries. Keep the last 50 entries maximum.

```markdown
---
**[OTA-042]** 2025-01-15T10:15:00Z | Branch: feature-resilience
- **Observation**: API calls to /users endpoint failing with 503 errors intermittently
- **Thought**: Need exponential backoff rather than fixed delay; should cap at 3 retries to avoid infinite loops
- **Action**: Implementing retry_with_backoff() in utils/http.py

---
**[OTA-043]** 2025-01-15T10:28:00Z | Branch: feature-resilience
- **Observation**: Tests passing for timeout and 5xx scenarios
- **Thought**: Ready to commit this milestone - retry logic is complete and validated
- **Action**: COMMIT with summary of retry implementation
```

## metadata.yaml

Structured infrastructure state. Updated on every operation.

```yaml
version: 1
created: "2025-01-10T08:00:00Z"
proactive_commits: true
branches:
  - name: main
    status: active
    created: "2025-01-10T08:00:00Z"
  - name: feature-resilience
    status: active
    created: "2025-01-15T09:00:00Z"
    parent: main
  - name: experiment-cache
    status: abandoned
    created: "2025-01-12T14:00:00Z"
    reason: "Redis dependency too heavy for this use case"
file_tree:
  - src/main.py
  - src/utils/http.py
  - tests/test_http.py
dependencies:
  - requests>=2.28
  - pytest>=7.0
config:
  language: python
  framework: fastapi
```

## Branch Directory Structure

Each branch under `.GCC/branches/<branch-name>/` contains:

```
.GCC/branches/feature-resilience/
├── commit.md    # Commits specific to this branch
├── log.md       # OTA traces for this branch
└── summary.md   # Branch purpose and current status
```

### summary.md (per-branch)

```markdown
# Branch: feature-resilience

## Purpose
Add fault tolerance to external API integrations to handle intermittent 503 errors.

## Status: active
## Parent: main
## Created: 2025-01-15T09:00:00Z

## Key Decisions
- Using exponential backoff (not fixed delay)
- Max 3 retries to prevent cascade failures
- Logging all retry attempts for observability
```




---

## 🚀 Usage

**Reference this template:** `@skill-gcc.md`


**Platform-specific:**
- **GitHub Copilot**: Add to `.github/copilot-instructions.md`
- **Augment Code**: Use `aug context add` command
- **Cursor/Windsurf**: Reference in chat with `@filename`
- **Claude**: Add to Project Knowledge
- **ChatGPT**: Add to Custom GPT configuration
