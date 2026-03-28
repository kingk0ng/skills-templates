---
id: skill-deadline-prep
type: skill
name: deadline-prep
description: Generate a structured demo outline from your session's change log and
  git history. Reads .claude/critical_log_changes.csv and git log to produce presentation-ready
  talking points for end-of-day demos, standups, or delivery deadlines.
category: productivity
complexity: medium
keywords:
- ci/cd
- docker
- git
- test
capabilities: []
token_estimate: 547
---

<!-- Converted from Claude Skill Template -->
<!-- Complexity: Medium -->
<!-- Estimated Tokens: ~547 -->


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




# deadline-prep

> Generate a structured demo outline from your session's change log and git history. Reads .claude/critical_log_changes.csv and git log to produce presentation-ready talking points for end-of-day demos, standups, or delivery deadlines.

# Deadline Prep

Generate a structured demo outline from your work session. Combines the change log CSV (from the change-logger hook) with git history to create presentation-ready talking points.

## Workflow

### Step 1: Gather data sources

**Change log** (primary source if available):
- Read `.claude/critical_log_changes.csv` if it exists
- Parse columns: timestamp, tool, file_path, action, details
- Group by: files created, files modified, commands executed

**Git history** (always available):
```bash
git log --oneline --since="today 00:00"
git diff --stat HEAD~10 2>/dev/null || git diff --stat
```

If the CSV doesn't exist, fall back to git-only mode and note this in the output.

### Step 2: Analyze and categorize changes

Group all changes into categories:

| Category | Signals |
|----------|---------|
| **Features shipped** | New files, new routes, new components, `feat` commits |
| **Bug fixes** | Modified files with `fix` commits, error handling changes |
| **Refactors** | Renamed files, structural changes, `refactor` commits |
| **Config/Setup** | package.json, tsconfig, CI/CD, Docker changes |
| **Tests** | Test files created or modified |
| **Documentation** | README, docs, comments |

### Step 3: Generate the demo outline

Create a structured markdown document:

```markdown
# Demo Outline — [Date]

## What I Shipped
- **[Feature/Fix name]**: One sentence explaining what it does and why it matters
- **[Feature/Fix name]**: One sentence explaining what it does and why it matters
- **[Feature/Fix name]**: One sentence explaining what it does and why it matters

## Architecture Decisions
- **[Decision]**: Why I chose this approach over alternatives
- **[Decision]**: Tradeoff I made and the reasoning

## What I Would Do Next
1. **[Priority 1]**: Why this is the most important next step
2. **[Priority 2]**: What this would unlock
3. **[Priority 3]**: Nice-to-have improvement

## Session Metrics
- Files changed: X
- Lines: +Y / -Z
- Commits: N
- Key files: `path/to/important/file.ts`, `path/to/other.ts`
- Time window: HH:MM - HH:MM
```

### Step 4: Save and present

Save the outline to `.claude/demo-outline.md`.

Print the full outline to the terminal so the user can review it immediately.

## Tips

- Run this 30 minutes before your deadline to have time to review and add personal context
- The "Architecture Decisions" section is what reviewers care about most — add context about tradeoffs
- "What I Would Do Next" shows you think beyond the immediate task
- Edit the generated outline to add your own voice and any context the log missed
- Works best with the `change-logger` hook installed, but functions with git history alone


---

## 🚀 Usage

**Reference this template:** `@skill-deadline-prep.md`


**Platform-specific:**
- **GitHub Copilot**: Add to `.github/copilot-instructions.md`
- **Augment Code**: Use `aug context add` command
- **Cursor/Windsurf**: Reference in chat with `@filename`
- **Claude**: Add to Project Knowledge
- **ChatGPT**: Add to Custom GPT configuration
