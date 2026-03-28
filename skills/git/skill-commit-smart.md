---
id: skill-commit-smart
type: skill
name: commit-smart
description: Analyze staged/unstaged changes and create semantic conventional commits
  with context about WHY, not just WHAT. Auto-detects commit type and scope from the
  diff. Supports optional type/scope arguments. Usage - /commit-smart, /commit-smart
  fix, /commit-smart refactor api
category: git
complexity: medium
keywords:
- api
- git
- performance
- security
- test
capabilities: []
token_estimate: 643
---

<!-- Converted from Claude Skill Template -->
<!-- Complexity: Medium -->
<!-- Estimated Tokens: ~643 -->


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




# commit-smart

> Analyze staged/unstaged changes and create semantic conventional commits with context about WHY, not just WHAT. Auto-detects commit type and scope from the diff. Supports optional type/scope arguments. Usage - /commit-smart, /commit-smart fix, /commit-smart refactor api

# Smart Commit

Create meaningful conventional commits by analyzing your actual changes.

## Workflow

### Step 1: Assess the working tree

Run these commands to understand the current state:

```bash
git status
git diff --stat
git diff --cached --stat
```

### Step 2: Handle unstaged changes

If nothing is staged (`git diff --cached` is empty):

1. Show the user what files have changed
2. Suggest what to stage based on logical grouping (e.g., "these 3 files are all related to the auth refactor")
3. Ask if they want to stage all, or select specific files
4. Stage the approved files with `git add <files>`

If changes are already staged, proceed to analysis.

### Step 3: Analyze the diff

Read the full staged diff:

```bash
git diff --cached
```

Determine the commit type from the changes:

| Signal | Type |
|--------|------|
| New files with new functionality | `feat` |
| New test files or test additions | `test` |
| Changes to existing logic fixing incorrect behavior | `fix` |
| Structural changes without behavior change | `refactor` |
| package.json, tsconfig, CI config changes | `chore` |
| Build/bundler config changes | `build` |
| README, docs, comments only | `docs` |
| Formatting, whitespace, semicolons only | `style` |
| Performance improvements | `perf` |

Determine the scope from the primary directory or module affected:
- `src/api/` -> `api`
- `src/components/auth/` -> `auth`
- `tests/` -> `tests`
- Root config files -> omit scope
- Multiple unrelated areas -> omit scope

### Step 4: Check for user overrides

If the user provided arguments via `$ARGUMENTS`:
- Single word (e.g., `fix`) -> use as commit type
- Two words (e.g., `refactor api`) -> use as type and scope
- Otherwise -> use auto-detected values

### Step 5: Compose the commit message

Format: `type(scope): imperative short description`

Rules:
- Subject line max 72 characters
- Use imperative mood ("add", "fix", "refactor", not "added", "fixes")
- Don't end with a period
- Body explains **WHY** this change was made, not what changed (the diff shows what)
- If changes are trivial (typo fix, formatting), skip the body

Example:
```
feat(auth): add JWT refresh token rotation

Tokens were expiring mid-session for users with slow connections.
Rotating refresh tokens extends the session without compromising
security, since each refresh token can only be used once.
```

### Step 6: Confirm and commit

Show the user the proposed commit message and ask for confirmation.

If confirmed, run:
```bash
git commit -m "<message>"
```

Then verify with:
```bash
git log --oneline -1
```

Show the committed hash and message.

## Tips

- Run after completing a logical unit of work, not after every file change
- If the diff is too large for one commit, suggest splitting into multiple commits
- For breaking changes, add `!` after the scope: `feat(api)!: change response format`
- The body should answer "if someone reads this commit in 6 months, will they understand WHY?"


---

## 🚀 Usage

**Reference this template:** `@skill-commit-smart.md`


**Platform-specific:**
- **GitHub Copilot**: Add to `.github/copilot-instructions.md`
- **Augment Code**: Use `aug context add` command
- **Cursor/Windsurf**: Reference in chat with `@filename`
- **Claude**: Add to Project Knowledge
- **ChatGPT**: Add to Custom GPT configuration
