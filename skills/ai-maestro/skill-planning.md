---
id: skill-planning
type: skill
name: planning
description: Create and manage persistent markdown planning files for structured task
  execution. Use when the user asks to "create a plan", "track progress", "start a
  research project", or when a task requires more than 5 tool calls and needs structured
  phase tracking to stay focused and avoid goal drift.
category: ai-maestro
complexity: medium
keywords:
- github
- test
capabilities: []
token_estimate: 507
---

<!-- Converted from Claude Skill Template -->
<!-- Complexity: Medium -->
<!-- Estimated Tokens: ~507 -->


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




# planning

> Create and manage persistent markdown planning files for structured task execution. Use when the user asks to "create a plan", "track progress", "start a research project", or when a task requires more than 5 tool calls and needs structured phase tracking to stay focused and avoid goal drift.

# AI Maestro Planning

Solve the execution problem -- staying focused during complex, multi-step tasks. Uses persistent markdown files to track goals, findings, and progress so you never lose context. Part of the [AI Maestro](https://github.com/23blocks-OS/ai-maestro) suite.

## When to Use

- Multi-step tasks (3+ steps)
- Research projects
- Building features requiring >5 tool calls
- Any task where you might lose track of the goal

## The 3-File Pattern

Create in `docs_dev/` (or `$AIMAESTRO_PLANNING_DIR`):

| File | Purpose | Update When |
|------|---------|-------------|
| `task_plan.md` | Goals, phases, decisions, errors | After each phase |
| `findings.md` | Research, discoveries, resources | During research |
| `progress.md` | Session log, test results | Throughout session |

## Quick Start

```bash
PLAN_DIR="${AIMAESTRO_PLANNING_DIR:-docs_dev}"
mkdir -p "$PLAN_DIR"
```

Then create `task_plan.md` with:
```markdown
# Task: [Goal]

## Phases
- [ ] Phase 1: Research
- [ ] Phase 2: Design
- [ ] Phase 3: Implement
- [ ] Phase 4: Test

## Decisions
| Decision | Rationale | Date |
|----------|-----------|------|

## Errors Encountered
| Error | Attempt | Resolution |
|-------|---------|------------|
```

## The 6 Rules

1. **Create plan first** -- Never start complex work without `task_plan.md`
2. **Read before decide** -- Re-read the plan before any major decision
3. **Update after act** -- Mark phases complete, log what changed
4. **2-action rule** -- After every 2 search/browse operations, save findings to `findings.md`
5. **Log all errors** -- Every error goes in the plan with attempt number and resolution
6. **Never repeat failures** -- If an action failed, change your approach

## The 3-Strike Protocol

| Strike | Action |
|--------|--------|
| 1 | Diagnose root cause, apply targeted fix |
| 2 | Try a different approach entirely |
| 3 | Question assumptions, search for similar issues |
| After 3 | Escalate to user with all attempts documented |

## The 5-Question Reboot

Lost? Answer these from your planning files:

1. Where am I? (current phase in `task_plan.md`)
2. Where am I going? (remaining phases)
3. What's the goal? (goal section)
4. What have I learned? (`findings.md`)
5. What have I done? (`progress.md`)

## Full AI Maestro Experience

This skill works standalone with no dependencies. For the complete experience including **memory search**, **docs search**, **graph query**, **agent messaging**, and **agent management**, install the full [AI Maestro](https://github.com/23blocks-OS/ai-maestro) platform.


---

## 🚀 Usage

**Reference this template:** `@skill-planning.md`


**Platform-specific:**
- **GitHub Copilot**: Add to `.github/copilot-instructions.md`
- **Augment Code**: Use `aug context add` command
- **Cursor/Windsurf**: Reference in chat with `@filename`
- **Claude**: Add to Project Knowledge
- **ChatGPT**: Add to Custom GPT configuration
