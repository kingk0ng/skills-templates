---
id: skill-command-creator
type: skill
name: command-creator
description: This skill should be used when creating a Claude Code slash command.
  Use when users ask to "create a command", "make a slash command", "add a command",
  or want to document a workflow as a reusable command. Essential for creating optimized,
  agent-executable slash commands with proper structure and best practices.
category: development
complexity: medium
keywords:
- git
- test
- testing
capabilities: []
token_estimate: 1205
has_references: true
reference_count: 3
---

<!-- Converted from Claude Skill Template -->
<!-- Complexity: Medium -->
<!-- Estimated Tokens: ~1,205 -->
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




# command-creator

> This skill should be used when creating a Claude Code slash command. Use when users ask to "create a command", "make a slash command", "add a command", or want to document a workflow as a reusable command. Essential for creating optimized, agent-executable slash commands with proper structure and best practices.

# Command Creator

This skill guides the creation of Claude Code slash commands - reusable workflows that can be invoked with `/command-name` in Claude Code conversations.

## About Slash Commands

Slash commands are markdown files stored in `.claude/commands/` (project-level) or `~/.claude/commands/` (global/user-level) that get expanded into prompts when invoked. They're ideal for:

- Repetitive workflows (code review, PR submission, CI fixing)
- Multi-step processes that need consistency
- Agent delegation patterns
- Project-specific automation

## When to Use This Skill

Invoke this skill when users:

- Ask to "create a command" or "make a slash command"
- Want to automate a repetitive workflow
- Need to document a consistent process for reuse
- Say "I keep doing X, can we make a command for it?"
- Want to create project-specific or global commands

## Bundled Resources

This skill includes reference documentation for detailed guidance:

- **references/patterns.md** - Command patterns (workflow automation, iterative fixing, agent delegation, simple execution)
- **references/examples.md** - Real command examples with full source (submit-stack, ensure-ci, create-implementation-plan)
- **references/best-practices.md** - Quality checklist, common pitfalls, writing guidelines, template structure

Load these references as needed when creating commands to understand patterns, see examples, or ensure quality.

## Command Structure Overview

Every slash command is a markdown file with:

```markdown
---
description: Brief description shown in /help (required)
argument-hint: <placeholder> (optional, if command takes arguments)
---

# Command Title

[Detailed instructions for the agent to execute autonomously]
```

## Command Creation Workflow

### Step 1: Determine Location

**Auto-detect the appropriate location:**

1. Check git repository status: `git rev-parse --is-inside-work-tree 2>/dev/null`
2. Default location:
   - If in git repo → Project-level: `.claude/commands/`
   - If not in git repo → Global: `~/.claude/commands/`
3. Allow user override:
   - If user explicitly mentions "global" or "user-level" → Use `~/.claude/commands/`
   - If user explicitly mentions "project" or "project-level" → Use `.claude/commands/`

Report the chosen location to the user before proceeding.

### Step 2: Show Command Patterns

Help the user understand different command types. Load **references/patterns.md** to see available patterns:

- **Workflow Automation** - Analyze → Act → Report (e.g., submit-stack)
- **Iterative Fixing** - Run → Parse → Fix → Repeat (e.g., ensure-ci)
- **Agent Delegation** - Context → Delegate → Iterate (e.g., create-implementation-plan)
- **Simple Execution** - Run command with args (e.g., codex-review)

Ask the user: "Which pattern is closest to what you want to create?" This helps frame the conversation.

### Step 3: Gather Command Information

Ask the user for key information:

#### A. Command Name and Purpose

Ask:

- "What should the command be called?" (for filename)
- "What does this command do?" (for description field)

Guidelines:

- Command names MUST be kebab-case (hyphens, NOT underscores)
  - ✅ CORRECT: `submit-stack`, `ensure-ci`, `create-from-plan`
  - ❌ WRONG: `submit_stack`, `ensure_ci`, `create_from_plan`
- File names match command names: `my-command.md` → invoked as `/my-command`
- Description should be concise, action-oriented (appears in `/help` output)

#### B. Arguments

Ask:

- "Does this command take any arguments?"
- "Are arguments required or optional?"
- "What should arguments represent?"

If command takes arguments:

- Add `argument-hint: <placeholder>` to frontmatter
- Use `<angle-brackets>` for required arguments
- Use `[square-brackets]` for optional arguments

#### C. Workflow Steps

Ask:

- "What are the specific steps this command should follow?"
- "What order should they happen in?"
- "What tools or commands should be used?"

Gather details about:

- Initial analysis or checks to perform
- Main actions to take
- How to handle results
- Success criteria
- Error handling approach

#### D. Tool Restrictions and Guidance

Ask:

- "Should this command use any specific agents or tools?"
- "Are there any tools or operations it should avoid?"
- "Should it read any specific files for context?"

### Step 4: Generate Optimized Command

Create the command file with agent-optimized instructions. Load **references/best-practices.md** for:

- Template structure
- Best practices for agent execution
- Writing style guidelines
- Quality checklist

Key principles:

- Use imperative/infinitive form (verb-first instructions)
- Be explicit and specific
- Include expected outcomes
- Provide concrete examples
- Define clear error handling

### Step 5: Create the Command File

1. Determine full file path:
   - Project: `.claude/commands/[command-name].md`
   - Global: `~/.claude/commands/[command-name].md`

2. Ensure directory exists:

   ```bash
   mkdir -p [directory-path]
   ```

3. Write the command file using the Write tool

4. Confirm with user:
   - Report the file location
   - Summarize what the command does
   - Explain how to use it: `/command-name [arguments]`

### Step 6: Test and Iterate (Optional)

If the user wants to test:

1. Suggest testing: `You can test this command by running: /command-name [arguments]`
2. Be ready to iterate based on feedback
3. Update the file with improvements as needed

## Quick Tips

**For detailed guidance, load the bundled references:**

- Load **references/patterns.md** when designing the command workflow
- Load **references/examples.md** to see how existing commands are structured
- Load **references/best-practices.md** before finalizing to ensure quality

**Common patterns to remember:**

- Use Bash tool for `pytest`, `pyright`, `ruff`, `prettier`, `make`, `gt` commands
- Use Task tool to invoke subagents for specialized tasks
- Check for specific files first (e.g., `.PLAN.md`) before proceeding
- Mark todos complete immediately, not in batches
- Include explicit error handling instructions
- Define clear success criteria

## Summary

When creating a command:

1. **Detect location** (project vs global)
2. **Show patterns** to frame the conversation
3. **Gather information** (name, purpose, arguments, steps, tools)
4. **Generate optimized command** with agent-executable instructions
5. **Create file** at appropriate location
6. **Confirm and iterate** as needed

Focus on creating commands that agents can execute autonomously, with clear steps, explicit tool usage, and proper error handling.


---


## 📚 Reference Materials


### Best Practices

# Command Best Practices

This document provides quality guidelines, writing style recommendations, common pitfalls, and a detailed template structure for creating effective slash commands.

## Command Writing Style

Commands are executed by AI agents, so optimize for autonomous execution.

### Writing Form

**ALWAYS use imperative/infinitive form** (verb-first instructions), not second person.

```markdown
✅ CORRECT:

- "Run git status to check current branch"
- "Check if .PLAN.md exists before proceeding"
- "Use the Task tool with Bash tool"

❌ WRONG:

- "You should run git status"
- "You need to check if .PLAN.md exists"
- "You'll want to use the Task tool"
```

### Specificity

Be explicit and specific, not vague.

```markdown
✅ CORRECT:

- "Run make lint to check for linting errors"
- "Read src/config.py lines 45-67 to understand the config structure"
- "Use Edit tool to replace 'List[str]' with 'list[str]'"

❌ WRONG:

- "Check for errors"
- "Look at the config file"
- "Fix the type annotation"
```

### Expected Outcomes

Include what should happen after each action.

```markdown
✅ CORRECT:

- "Run git status - this should show modified files in src/ directory"
- "After running make format, all Python files should be formatted"
- "The output should contain PR URLs for each submitted branch"

❌ WRONG:

- "Run git status"
- "Run make format"
- "Submit the PRs"
```

### Concrete Examples

Provide realistic examples, not placeholders like foo/bar.

```markdown
✅ CORRECT:

- "Example: `git commit -m 'Add user authentication with OAuth2'`"
- "Example: `/submit-stack 'Implement caching for API responses'`"
- "If error shows: `src/erk/cli/commands/init.py:45: Type error`"

❌ WRONG:

- "Example: `git commit -m 'foo bar'`"
- "Example: `/submit-stack 'something'`"
- "If error shows: `file.py:123: Error message`"
```

## Template Structure

Use this template structure for comprehensive commands:

```markdown
---
description: [One-line description for /help output]
argument-hint: [<required>] or [[optional]] (omit if no arguments)
---

# [Command Title]

[1-2 sentence overview of what this command does]

## What This Command Does

[Numbered list of main steps, user-facing description]

1. **[First action]**: [What it does]
2. **[Second action]**: [What it does]
3. **[Third action]**: [What it does]

## Usage

\`\`\`bash

# [Example with arguments]

/command-name "argument example"

# [Example without arguments if optional]

/command-name
\`\`\`

## Implementation Steps

When this command is invoked:

### 1. [First Major Step]

[Clear instructions with specifics]

\`\`\`bash

# Example commands if applicable

command --flag value
\`\`\`

[Explain what to do with results]

### 2. [Second Major Step]

[Continue with clear, actionable instructions]

### 3. [Continue for all steps]

## Important Notes

- **[Key constraint or requirement]**
- **[What to check first]**
- **[What NOT to do]**
- **[Error handling approach]**

## Error Handling

[Specify how to handle failures]

If any step fails:

- Report the specific command that failed
- Show the error message
- [What to do next - retry/ask user/stop]

## Example Output

\`\`\`
[Show expected terminal output]
\`\`\`
```

## Agent Optimization Elements

### 1. Explicit File Checks

Tell the agent exactly what to check and when.

```markdown
**FIRST**: Check if `.PLAN.md` exists in the repository root:

\`\`\`bash
if [ -f .PLAN.md ]; then

# Use .PLAN.md for context

else

# Fall back to alternative approach

fi
\`\`\`
```

### 2. Tool Usage Guidance

Be explicit about which tools to use.

```markdown
**Use the Bash tool for pytest/pyright/ruff/prettier/make/gt commands:**

Use Bash tool to run:
\`\`\`bash
make all-ci
\`\`\`

**DO NOT use Bash tool for make commands**
```

### 3. Anti-Patterns

Call out what NOT to do.

```markdown
## Important Notes

- **NEVER run additional exploration commands** beyond checking .PLAN.md, git status/diff
- **DO NOT batch completions** - mark todos complete immediately after finishing
- **DO NOT use Edit tool during planning phase**
- **DO NOT retry automatically** - ask user how to proceed
```

### 4. Conditional Logic

Use clear if/else structure.

```markdown
If condition A:

- Do X
- Then do Y

Otherwise (if condition B):

- Do Z
- Then do W

If neither condition is met:

- Report to user
- Exit gracefully
```

### 5. Success Criteria

Define exactly when to stop.

```markdown
## When to Stop

**SUCCESS**: Stop when `make all-ci` exits with code 0 (all checks passed)

**STUCK**: Stop and report to user if:

1. You've completed 10 iterations without success
2. The same error persists after 3 fix attempts
3. You encounter an error you cannot automatically fix
```

### 6. Error Handling

Provide explicit error handling instructions.

```markdown
## Error Handling

If any step fails:

- Report the specific command that failed
- Show the error message to the user
- Ask how to proceed (don't retry automatically)

Example:

\`\`\`
Error: git commit failed with exit code 1

Error message:
nothing to commit, working tree clean

Next steps: Please make changes before committing.
\`\`\`
```

### 7. Progress Tracking

Specify when and how to track progress.

```markdown
## Progress Reporting

Use TodoWrite to track your progress:

- Create todos at the start for each iteration
- Mark as in_progress when starting
- Mark as completed immediately after finishing (not batched)
- Update with iteration number: "Iteration 3: Fixing type errors"
```

## Common Patterns

### Pattern: TodoWrite Usage

```markdown
## Progress Tracking

Use TodoWrite to create todos for:

1. Each major step in the workflow
2. Each iteration in a loop
3. Each error category being fixed

Mark todos as completed IMMEDIATELY after finishing each task, not batched at the end.
```

### Pattern: File Operations

```markdown
### Read Before Modifying

Before making any changes:

1. Use Read tool to examine the current file state
2. Understand the code structure and context
3. Identify exact changes needed
4. Use Edit tool with precise old_string/new_string
```

### Pattern: Git Operations

```markdown
### Git Workflow

1. Check current git status:
   \`\`\`bash
   git status
   \`\`\`

2. Review changes:
   \`\`\`bash
   git diff HEAD
   \`\`\`

3. Check recent commits for style:
   \`\`\`bash
   git log --oneline -5
   \`\`\`

4. Stage all changes:
   \`\`\`bash
   git add .
   \`\`\`

5. Create commit:
   \`\`\`bash
   git commit -m "[message]"
   \`\`\`
```

### Pattern: Conditional Tool Selection

```markdown
### Tool Selection Based on Scope

Analyze the changes first:

If changes span 3+ files OR involve new abstractions:

- Use Task tool with subagent_type="subagent"
- Create detailed plan
- Execute with subagent agent

Otherwise (changes are contained):

- Execute changes directly
- Use Edit tool for modifications
- Skip planning overhead
```

### Pattern: Makefile Integration

```markdown
### Running Make Commands

**ALWAYS use Bash tool for pytest/pyright/ruff/prettier/make/gt commands**

Use Bash tool:

\`\`\`markdown
Use Bash tool to run command: "make all-ci"
\`\`\`

**DO NOT use Bash tool for make commands** - this is less efficient and provides worse output handling.
```

## Quality Checklist

Before finalizing a command, verify:

**Structure:**

- [ ] Command name is descriptive and kebab-case
- [ ] Description is concise and action-oriented (for `/help` output)
- [ ] Frontmatter includes `description` (required)
- [ ] Frontmatter includes `argument-hint` if applicable
- [ ] Has "What This Command Does" user-facing summary
- [ ] Has "Implementation Steps" with numbered sections

**Content:**

- [ ] Steps are numbered and clearly ordered
- [ ] Each step has specific, actionable instructions
- [ ] Tool usage is explicitly specified
- [ ] File checks are explicit (with code examples)
- [ ] Conditional logic uses clear if/else structure
- [ ] Anti-patterns are called out with "NEVER" or "DO NOT"
- [ ] Error handling is defined with specific actions
- [ ] Success criteria are clearly stated

**Writing Style:**

- [ ] Uses imperative/infinitive form (not second person)
- [ ] Specific, not vague ("Run make lint" not "Check for errors")
- [ ] Includes expected outcomes ("This should output...")
- [ ] Provides realistic examples (not foo/bar placeholders)

**Location:**

- [ ] Location (project vs global) is appropriate
- [ ] Directory exists or will be created
- [ ] File path is correct (`.claude/commands/` or `~/.claude/commands/`)

**Testing:**

- [ ] User knows how to invoke: `/command-name [arguments]`
- [ ] Command has been tested if possible
- [ ] Iterations incorporated user feedback

## Common Pitfalls

### 1. Vague Instructions

❌ **WRONG:**

```markdown
Fix any errors that appear
```

✅ **CORRECT:**

```markdown
If lint errors appear:

- Run `make fix` to auto-fix lint errors
- Run `make format` to fix formatting errors
- For manual fixes, use Edit tool to modify files
```

### 2. Missing Error Handling

❌ **WRONG:**

```markdown
Run make all-ci
Apply fixes
Done
```

✅ **CORRECT:**

```markdown
Run make all-ci

If exit code is 0:

- All checks passed, report success

If exit code is non-zero:

- Parse error output
- Apply targeted fixes
- Run again to verify
- Stop if same error appears 3 times
```

### 3. Ambiguous Conditionals

❌ **WRONG:**

```markdown
Check if file exists and do something
```

✅ **CORRECT:**

```markdown
Check if .PLAN.md exists:

If file exists:

- Read .PLAN.md for context
- Use plan summary in commit message

If file does not exist:

- Run git diff to analyze changes
- Create commit message from diff
```

### 4. Batch Operations

❌ **WRONG:**

```markdown
Fix all the errors, then mark all todos as completed
```

✅ **CORRECT:**

```markdown
For each error category:

1. Fix the errors in that category
2. Mark the todo as completed immediately
3. Move to next category

Do NOT batch todo completions at the end
```

### 5. Tool Confusion

❌ **WRONG:**

```markdown
Use an agent to run make
```

✅ **CORRECT:**

```markdown
Use Bash tool to run make commands:

\`\`\`bash
make all-ci
\`\`\`

DO NOT use Bash tool for make commands
```

### 6. Missing Context

❌ **WRONG:**

```markdown
Create a commit and submit PRs
```

✅ **CORRECT:**

```markdown
### 1. Check for Context Files

FIRST, check if .PLAN.md exists in repository root

### 2. Analyze Changes

- If .PLAN.md exists: read for context
- Otherwise: run git status and git diff HEAD

### 3. Create Commit

Based on context from step 1 and 2:

- Draft single-sentence commit message
- Check git log for repo style
- Create commit with git commit -m "message"

### 4. Submit PRs

Run: gt submit --stack --publish --no-edit
```

### 7. Poor Descriptions

❌ **WRONG:**

```yaml
---
description: A command that helps with CI stuff
---
```

✅ **CORRECT:**

```yaml
---
description: Run make all-ci and iteratively fix issues until all checks pass
---
```

The description appears in `/help` output - make it clear and action-oriented.

## Advanced Best Practices

### Multi-Step Verification

For complex workflows, verify each step:

```markdown
### 3. Create Commit

1. Stage changes:
   \`\`\`bash
   git add .
   \`\`\`

2. Verify staging:
   \`\`\`bash
   git status
   \`\`\`
   Should show files in "Changes to be committed"

3. Create commit:
   \`\`\`bash
   git commit -m "message"
   \`\`\`
   Should output "[branch-name abc1234] message"

4. Verify commit created:
   \`\`\`bash
   git log -1 --oneline
   \`\`\`
   Should show the new commit
```

### Iteration Control

For iterative commands, implement safeguards:

```markdown
## Iteration Control

**Maximum iterations**: 10 attempts

**Stuck detection logic:**

- Track errors seen in each iteration
- If same error appears 3 consecutive times: STOP
- If no progress after 5 iterations: STOP

**Stop immediately if:**

- Max iterations reached (10)
- Same error persists (3 times)
- Unrecoverable error encountered
```

### Context Gathering

For analysis-heavy commands, gather context systematically:

```markdown
## Context Gathering

Check these sources in order:

1. **Context files** (if they exist):
   - .PLAN.md - implementation plan
   - AGENTS.md - coding standards
   - CONTRIBUTING.md - contribution guidelines

2. **Git information**:
   - git status - current changes
   - git diff HEAD - actual diff
   - git log -5 --oneline - recent commits

3. **Project files** (if needed):
   - pyproject.toml - project config
   - Makefile - available commands
   - README.md - project overview

Stop gathering context once you have enough information - don't over-analyze.
```

### Output Formatting

Provide clear output format specifications:

```markdown
## Expected Output Format

After command completes, output should follow this format:

\`\`\`

## [Command Name] Results

**Status**: [SUCCESS/STUCK/ERROR]

**Actions Taken**:

1. [First action and result]
2. [Second action and result]
3. [Third action and result]

**Summary**:
[One sentence summary of what was accomplished]

**Next Steps**:
[What the user should do next, if applicable]
\`\`\`
```

## Summary

Effective slash commands:

1. **Use imperative form** (verb-first, not second person)
2. **Be specific** (not vague)
3. **Include outcomes** (what should happen)
4. **Provide examples** (realistic, not foo/bar)
5. **Specify tools** (Task tool with subagent_type)
6. **Call out anti-patterns** (NEVER/DO NOT)
7. **Define error handling** (explicit actions)
8. **State success criteria** (when to stop)
9. **Track progress** (TodoWrite for multi-step)
10. **Verify each step** (check results before proceeding)

Focus on creating commands that agents can execute autonomously without asking clarifying questions.




### Patterns

# Command Patterns

This document describes the common patterns for slash commands, helping you choose the right structure for your workflow.

## Pattern Categories

### 1. Workflow Automation Pattern

**Structure:** Analyze → Act → Report

**When to use:**

- Multi-step workflows with clear sequence
- Commands that need to analyze before acting
- Workflows that produce specific outputs (commits, PRs, reports)

**Example workflow:**

1. Check for context files (e.g., `.PLAN.md`)
2. Analyze current state (git status, file changes)
3. Perform actions (create commit, submit PR)
4. Report results to user

**Key features:**

- Explicit file check order
- Conditional logic based on file existence
- Clear success output format
- Context-aware decision making

**Pattern example:**

```markdown
1. Check for .PLAN.md in repository root
   - If exists: use plan context for commit message
   - If not: analyze git changes and draft message

2. Review git status and diff
   - Identify staged and unstaged changes
   - Determine scope of changes

3. Create commit with descriptive message
   - Follow repository's commit message style
   - Include co-author attribution

4. Submit PRs with Graphite
   - Use gt stack submit
   - Report PR URLs to user
```

### 2. Iterative Fixing Pattern

**Structure:** Run → Parse → Fix → Repeat

**When to use:**

- Commands that fix issues iteratively (linting, tests, CI)
- Workflows that need multiple attempts to succeed
- Tasks with clear pass/fail criteria

**Example workflow:**

1. Run check command (e.g., `make all-ci`)
2. Parse failures by type
3. Apply targeted fixes
4. Run check again to verify
5. Repeat until success or max iterations reached

**Key features:**

- Iteration control (max attempts, stuck detection)
- Progress tracking with TodoWrite
- Clear stopping conditions
- Categorization of failure types
- Incremental fix application

**Pattern example:**

```markdown
1. Run make all-ci (max 10 iterations)

2. If check fails:
   - Parse error output by category (pyright, ruff, tests)
   - Create todos for each error category
   - Apply fixes for each category sequentially
   - Mark todo complete after fixing each category

3. After each fix iteration:
   - Run make all-ci again
   - Check if new errors appeared
   - If stuck (same errors 2+ times): stop and report

4. Stop when:
   - All checks pass (exit code 0)
   - Max iterations reached
   - Detected stuck state
```

### 3. Agent Delegation Pattern

**Structure:** Context → Delegate → Iterate

**When to use:**

- Complex tasks requiring specialized agents
- Multi-phase workflows with human review
- Tasks that benefit from agent specialization

**Example workflow:**

1. Present planning context to user
2. Invoke specialized agent (via Task tool)
3. Agent creates plan/output iteratively
4. Plan is reviewed and refined by user
5. Save results to disk after approval

**Key features:**

- Clear agent invocation instructions
- Phase-based workflow (planning → review → execution)
- Explicit save-to-disk trigger
- User review checkpoints
- Context gathering before delegation

**Pattern example:**

```markdown
1. Present planning context
   - Explain what the agent will do
   - Set expectations for iterative process
   - Mention that user can refine the output

2. Invoke subagent agent
   - Use Task tool with subagent_type="subagent"
   - Pass task description and context
   - Do NOT attempt to write plan yourself

3. Agent works autonomously
   - Creates initial plan
   - Iterates with user feedback
   - Refines based on questions/concerns

4. After user approves plan
   - Save to .PLAN.md
   - Confirm location with user
   - Explain next steps (execution)
```

### 4. Simple Execution Pattern

**Structure:** Parse Arguments → Execute → Return Output

**When to use:**

- Single-step commands with arguments
- Wrapper commands for existing tools
- Commands that simply run and report

**Example workflow:**

1. Parse command arguments
2. Run specific command or script with arguments
3. Handle and display output
4. Report success or failure

**Key features:**

- Argument handling (required vs optional)
- Direct tool invocation
- Minimal logic
- Output formatting

**Pattern example:**

```markdown
1. Parse [base-branch] argument
   - If provided: use specified branch
   - If not provided: use main/master

2. Run codex-review script
   - Pass base-branch to script
   - Capture output

3. Display results
   - Show review findings
   - Report issues found
   - Suggest fixes if applicable
```

## Advanced Patterns

### Multi-Agent Orchestration

**When to use:** Complex workflows requiring multiple specialized agents in sequence

**Pattern:**

```markdown
1. Use Task tool with subagent_type="Explore" to find relevant files
   - Search for specific patterns
   - Identify key components

2. Use Task tool with subagent_type="subagent" to create plan
   - Pass context from exploration
   - Generate detailed implementation plan
   - Review with user

3. Execute the plan directly in the main conversation
   - Load plan from .PLAN.md
   - Use TodoWrite to track phases
   - Execute steps systematically
   - Report completion
```

### Context File Priority Checks

**When to use:** Commands that can operate in different modes based on available context

**Pattern:**

```markdown
Check these files in order for context:

1. .PLAN.md - implementation plan (highest priority)
2. .github/CONTRIBUTING.md - contribution guidelines
3. AGENTS.md - coding standards
4. README.md - project overview

Use the first file found to inform the workflow. Different files trigger different behaviors.
```

### Conditional Tool Selection

**When to use:** Commands that choose tools/approach based on task complexity

**Pattern:**

```markdown
Analyze scope of changes:

If changes span 3+ files OR involve new abstractions:

- Use subagent agent
- Create detailed plan
- Execute with subagent

Otherwise:

- Execute changes directly
- Use simpler workflow
- Skip planning overhead
```

### Makefile Integration Pattern

**When to use:** Commands that need to run make targets

**Pattern:**

```markdown
**IMPORTANT:** Always use Bash tool for pytest/pyright/ruff/prettier/make/gt commands

1. Use Bash tool directly
   - Run commands like: "make all-ci", "pytest tests/", "pyright", etc.
   - Bash tool will execute and return output

2. Process command results
   - Check exit code
   - Parse any errors
   - Apply fixes if needed
```

### Progressive Disclosure Pattern

**When to use:** Commands that start simple but can get more complex based on results

**Pattern:**

```markdown
1. Start with minimal check
   - Run basic validation
   - Identify if deeper work needed

2. If issues found:
   - Expand scope progressively
   - Add todos for each issue category
   - Handle incrementally

3. Only go deeper if necessary
   - Don't over-analyze upfront
   - Let results guide next steps
   - Stop when criteria met
```

## Pattern Selection Guide

| If the command needs to...              | Use this pattern           |
| --------------------------------------- | -------------------------- |
| Create commits/PRs based on analysis    | Workflow Automation        |
| Fix issues iteratively until passing    | Iterative Fixing           |
| Create plans or delegate to specialists | Agent Delegation           |
| Run a tool and display results          | Simple Execution           |
| Coordinate multiple agents              | Multi-Agent Orchestration  |
| Check multiple context files            | Context File Priority      |
| Choose approach based on complexity     | Conditional Tool Selection |
| Run make targets                        | Makefile Integration       |
| Start simple and expand as needed       | Progressive Disclosure     |

## Combining Patterns

Commands often combine multiple patterns. For example:

**submit-stack combines:**

- Context File Priority (check .PLAN.md)
- Workflow Automation (analyze → commit → submit)
- Conditional Tool Selection (use plan if exists)

**ensure-ci combines:**

- Iterative Fixing (run → fix → repeat)
- Makefile Integration (use makefile-runner)
- Progressive Disclosure (expand todos as issues found)

## Writing Pattern-Specific Instructions

When implementing a pattern, include these elements:

### For All Patterns

- Clear sequence of steps (numbered)
- Expected outcomes at each step
- Error handling approach
- Success criteria

### Pattern-Specific Elements

**Workflow Automation:**

- File checks before analysis
- Conditional branches
- Output format specifications

**Iterative Fixing:**

- Max iteration count
- Stuck detection logic
- Progress tracking requirements
- Per-category fix instructions

**Agent Delegation:**

- Exact Task tool invocation syntax
- Context to pass to agent
- User review checkpoints
- Save-to-disk instructions

**Simple Execution:**

- Argument parsing logic
- Command invocation syntax
- Output formatting requirements




### Examples

# Command Examples

This document provides complete, real-world examples of slash commands from the erk project. Use these as references when creating new commands.

## Example 1: submit-stack (Workflow Automation Pattern)

**Pattern:** Workflow Automation (Analyze → Act → Report)

**Full source:**

```markdown
---
description: Create git commit and submit stack with Graphite
argument-hint: <description>
---

# Submit Stack

Automatically create a git commit with a helpful summary message and submit the entire Graphite stack as pull requests.

## What This Command Does

1. **Analyze changes**: First checks for .PLAN.md file to understand context, otherwise reviews git status and diff
2. **Create commit**: Generates a concise single-sentence commit message summarizing the changes
3. **Restack**: Runs `gt restack` to ensure all branches in the stack are properly rebased
4. **Submit stack**: Runs `gt submit --stack --publish --no-edit` to create/update PRs for the entire stack
5. **Report results**: Shows the submitted PRs and their URLs

## Usage

\`\`\`bash

# With description argument

/submit-stack "Add user authentication feature"

# Without argument (will analyze changes automatically)

/submit-stack
\`\`\`

## Implementation Steps

When this command is invoked:

### 1. Analyze Current Changes

**FIRST**: Check if `.PLAN.md` exists in the repository root:

\`\`\`bash
if [ -f .PLAN.md ]; then

# Use .PLAN.md for context

else

# Fall back to git analysis

fi
\`\`\`

If `.PLAN.md` exists:

- Read the plan file to understand what was implemented
- Use the plan's summary and goals to create the commit message

If no `.PLAN.md`:

- Run `git status` and `git diff HEAD` to see changes
- Review the changes to create an accurate summary

### 2. Create Git Commit

Based on the analysis:

- If user provided an argument, use it as the basis for the commit message
- If `.PLAN.md` exists, summarize what was implemented from the plan
- Otherwise, analyze the git changes and create a descriptive single-sentence summary
- Ensure the commit message follows the repository's commit style (check `git log` for patterns)
- **DO NOT include any Claude Code footer or co-authorship attribution**

\`\`\`bash
git add .
git commit -m "[Single sentence summary of what was done]"
\`\`\`

### 3. Restack the Stack

Ensure all branches in the stack are properly rebased:

\`\`\`bash
gt restack
\`\`\`

### 4. Submit Stack

Submit all PRs in the stack without interactive prompts:

\`\`\`bash
gt submit --stack --publish --no-edit --restack
\`\`\`

Flags explained:

- `--stack`: Submit entire stack (upstack + downstack)
- `--publish`: Publish any draft PRs
- `--no-edit`: Use commit messages as PR titles/descriptions without prompting
- `--restack`: Restack branches before submitting (if needed)

### 5. Show Results

After submission, show:

- Number of PRs created/updated
- PR URLs (extract from `gt` output)
- Current stack status with `gt log short`

## Important Notes

- **Check for .PLAN.md FIRST** before analyzing git changes
- **NEVER run additional exploration commands** beyond checking .PLAN.md, git status/diff/log
- **Stage all changes** with `git add .` before committing
- **Single sentence summary**: Keep commit message concise and focused
- **Follow repo patterns**: Check recent commits with `git log` to match style
- **NO Claude footer**: Do not add any attribution or generated-by footer
- If there are no staged or unstaged changes, report to the user and exit

## Error Handling

If any step fails:

- Report the specific command that failed
- Show the error message
- Ask the user how to proceed (don't retry automatically)

## Example Output

\`\`\`
Analyzing changes...
✓ Found .PLAN.md - using plan context
✓ Found changes in 3 files

Creating commit: "Add dot-agent submit-stack command for automated PR workflow"
✓ Commit created

Restacking branches...
✓ Stack restacked successfully

Submitting stack...
✓ 2 PRs created/updated:

- PR #123: dot-agent-claude-folder-support (new)
- PR #122: base-branch (updated)

Current stack:
◯ dot-agent-claude-folder-support (current)
◯ base-branch
◉ main
\`\`\`
```

**Key features of this example:**

- Argument handling (optional `<description>`)
- Context file priority check (`.PLAN.md` first)
- Conditional logic based on file existence
- Specific command flags explained
- Clear anti-patterns ("NEVER run additional exploration")
- Expected output format shown

## Example 2: ensure-ci (Iterative Fixing Pattern)

**Pattern:** Iterative Fixing (Run → Parse → Fix → Repeat)

**Full source:**

```markdown
---
description: Run make all-ci and iteratively fix issues until all checks pass
---

You are an implementation finalizer. Your task is to run `make all-ci` and iteratively fix any issues until all CI checks pass successfully.

## Your Mission

Run the full CI pipeline (`make all-ci`) and automatically fix any failures. Keep iterating until all checks pass or you get stuck on an issue that requires human intervention.

## CI Pipeline (make all-ci)

The `make all-ci` target runs these checks in order:

1. **lint** - Ruff linting checks
2. **format** - Ruff code formatting checks
3. **prettier-check** - Markdown formatting checks
4. **pyright** - Type checking
5. **test** - Pytest test suite

## Iteration Process

### 1. Initial Run

Start by running `make all-ci` to see the current state:

\`\`\`bash
make all-ci
\`\`\`

### 2. Parse Failures

Analyze the output to identify which check(s) failed. Common failure patterns:

- **Ruff lint failures**: Look for "ruff check" errors
- **Format failures**: Look for "ruff format --check" or files that would be reformatted
- **Prettier failures**: Look for markdown files that need formatting
- **Pyright failures**: Look for type errors with file paths and line numbers
- **Test failures**: Look for pytest failures with test names and assertion errors

### 3. Apply Targeted Fixes

Based on the failure type, apply appropriate fixes:

#### Ruff Lint Failures

\`\`\`bash
make fix # Runs: uv run ruff check --fix --unsafe-fixes
\`\`\`

#### Ruff Format Failures

\`\`\`bash
make format # Runs: uv run ruff format
\`\`\`

#### Prettier Failures

\`\`\`bash
make prettier # Runs: prettier --write '\*_/_.md'
\`\`\`

#### Pyright Type Errors

- Use Read tool to examine the file at the reported line number
- Use Edit tool to fix type annotations, add type hints, or fix type mismatches
- Follow the coding standards in AGENTS.md (use `list[...]` not `List[...]`, etc.)

#### Test Failures

- Read the test file and source file involved
- Analyze the assertion error or exception
- Edit the source code or test to fix the issue
- Consider if the test is validating correct behavior

### 4. Verify Fix

After applying fixes, run `make all-ci` again to verify:

\`\`\`bash
make all-ci
\`\`\`

### 5. Repeat Until Success

Continue the cycle: run → identify failures → fix → verify

## Iteration Control

**Safety Limits:**

- **Maximum iterations**: 10 attempts
- **Stuck detection**: If the same error appears 3 times in a row, stop
- **Progress tracking**: Use TodoWrite to show iteration progress

## Progress Reporting

Use TodoWrite to track your progress:

\`\`\`
Iteration 1: Fixing lint errors
Iteration 2: Fixing format errors
Iteration 3: Fixing type errors in src/erk/cli/commands/switch.py
Iteration 4: All checks passed
\`\`\`

Update the status as you work through each iteration.

## When to Stop

**SUCCESS**: Stop when `make all-ci` exits with code 0 (all checks passed)

**STUCK**: Stop and report to user if:

1. You've completed 10 iterations without success
2. The same error persists after 3 fix attempts
3. You encounter an error you cannot automatically fix

## Stuck Reporting Format

If you get stuck, report clearly:

\`\`\`markdown

## Finalization Status: STUCK

I was unable to resolve the following issue after N attempts:

**Check**: [lint/format/prettier/pyright/test]

**Error**:
[Exact error message]

**File**: [file path if applicable]

**Attempted Fixes**:

1. [What you tried first]
2. [What you tried second]
3. [What you tried third]

**Next Steps**:
[Suggest what needs to be done manually]
\`\`\`

## Success Reporting Format

When all checks pass:

\`\`\`markdown

## Finalization Status: SUCCESS

All CI checks passed after N iteration(s):

- Lint: PASSED
- Format: PASSED
- Prettier: PASSED
- Pyright: PASSED
- Tests: PASSED

The code is ready for commit/PR.
\`\`\`

## Important Guidelines

1. **Be systematic**: Fix one type of error at a time
2. **Run full CI**: Always run full `make all-ci`, not individual checks
3. **Track progress**: Use TodoWrite for every iteration
4. **Don't guess**: Read files before making changes
5. **Follow standards**: Adhere to AGENTS.md coding standards
6. **Fail gracefully**: Report clearly when stuck
7. **Be efficient**: Use targeted fixes (don't reformat everything for one lint error)

## Example Flow

\`\`\`
Iteration 1:

- Run make all-ci
- Found: 5 lint errors, 2 files need formatting
- Fix: Run make fix && make format
- Result: 3 lint errors remain

Iteration 2:

- Run make all-ci
- Found: 3 lint errors (imports)
- Fix: Edit files to fix import issues
- Result: All lint/format pass, 2 type errors

Iteration 3:

- Run make all-ci
- Found: 2 pyright errors in switch.py:45 and switch.py:67
- Fix: Add type annotations
- Result: All checks pass

SUCCESS
\`\`\`

## Begin Now

Start by running `make all-ci` and begin the iterative fix process. Track your progress with TodoWrite and report your final status clearly.
```

**Key features of this example:**

- Maximum iteration limit (10 attempts)
- Stuck detection (same error 3 times)
- Per-error-type fix instructions
- TodoWrite progress tracking requirement
- Clear success/failure reporting formats
- Detailed example flow showing iterations

## Example 3: create-implementation-plan (Agent Delegation Pattern)

**Pattern:** Agent Delegation (Context → Delegate → Iterate)

**Full source:**

```markdown
---
description: Create an implementation plan using the subagent agent
---

## ⚠️ PLANNING-ONLY MODE ACTIVE

I'll help you create an implementation plan using the specialized planning agent. This workflow is designed for **planning only** - no code will be written until the plan is finalized and saved to disk.

### How This Works

1. **You provide context** about what needs to be built
2. **The agent creates a plan** (displayed in terminal for review)
3. **We iterate together** until the plan is perfect
4. **Plan is saved to disk** as a markdown file
5. **Then (and only then)** implementation can begin

### Provide Your Planning Context

You can share:

- A feature you want to implement
- An error message or bug to fix
- Performance issues to optimize
- A refactoring goal
- Any relevant context or requirements

**What would you like to plan?**

---

**IMPORTANT AGENT INSTRUCTIONS:**

When invoking the subagent agent:

1. **DO NOT write any code during planning phase**
2. **DO NOT use Edit, Write, or any modification tools**
3. **ONLY output the plan to terminal for iterative review**
4. **ONLY persist to disk after explicit user approval**
5. The agent should remain in "Phase 1: Human-Readable Planning" mode until the user explicitly approves with signals like "looks good", "approved", or "ready to implement"

The goal is to create a comprehensive implementation plan that will be saved as a `.md` file at the repository root, which can then guide future implementation work.
```

**Key features of this example:**

- User-facing explanation of the workflow
- Clear phase boundaries (planning vs implementation)
- Explicit anti-patterns ("DO NOT write code")
- User approval trigger ("looks good", "approved")
- Tells agent which specialized agent to invoke
- Specifies where to save output (`.md` at root)

## Example 4: codex-review (Simple Execution Pattern)

**Pattern:** Simple Execution (Parse Arguments → Execute → Return Output)

**Minimal example structure:**

```markdown
---
description: Perform a local code review using repository standards and best practices
argument-hint: [base-branch]
---

# Codex Review

Performs a thorough code review of changes between the current branch and the base branch.

## What This Command Does

1. Determines base branch (uses provided argument or defaults to main/master)
2. Runs codex-review script with the base branch
3. Displays review findings and suggestions

## Usage

\`\`\`bash

# With explicit base branch

/codex-review develop

# Without argument (auto-detects main/master)

/codex-review
\`\`\`

## Implementation Steps

### 1. Determine Base Branch

If `[base-branch]` argument is provided:

- Use the specified branch

If no argument:

- Check if `main` branch exists: `git rev-parse --verify main`
- If yes, use `main`
- If no, use `master`

### 2. Run Review Script

Execute the review script with the determined base branch:

\`\`\`bash
scripts/codex-review.py [base-branch]
\`\`\`

### 3. Display Results

Show the script output directly to the user, including:

- Files reviewed
- Issues found
- Suggestions for improvements
- Compliance with coding standards

## Error Handling

If the script fails:

- Show the error message
- Check if the base branch exists
- Verify the script is executable

## Notes

- Square brackets `[base-branch]` indicate optional argument
- Script handles actual review logic
- Command is a simple wrapper for convenience
```

**Key features of this example:**

- Optional argument handling (square brackets)
- Argument defaulting logic
- Direct script invocation
- Minimal additional logic
- Clear output pass-through

## Pattern Comparison

| Feature               | submit-stack             | ensure-ci          | create-implementation-plan | codex-review             |
| --------------------- | ------------------------ | ------------------ | -------------------------- | ------------------------ |
| **Pattern**           | Workflow Automation      | Iterative Fixing   | Agent Delegation           | Simple Execution         |
| **Arguments**         | Optional `<description>` | None               | None                       | Optional `[base-branch]` |
| **Context Files**     | Checks `.PLAN.md`        | Checks `AGENTS.md` | None                       | None                     |
| **Iterations**        | Single pass              | Up to 10           | Iterative (user-driven)    | Single pass              |
| **Tool Usage**        | Git, Graphite            | Make, Edit tools   | Task tool (agent)          | Script execution         |
| **Progress Tracking** | Inline reporting         | TodoWrite required | None (user reviews)        | None                     |
| **Error Handling**    | Ask user                 | Stop if stuck      | None specified             | Show error message       |
| **Success Criteria**  | PRs submitted            | Exit code 0        | User approves plan         | Script completes         |

## Usage Guidance

**Use submit-stack as a reference when:**

- Command needs to check context files first
- Workflow has clear sequential steps
- Git operations are involved
- Results need clear reporting

**Use ensure-ci as a reference when:**

- Command needs to iterate until success
- Multiple error types need different fixes
- Progress tracking is important
- Stuck detection is needed

**Use create-implementation-plan as a reference when:**

- Command delegates to specialized agent
- User review/approval is required
- No direct code modification should happen
- Output is saved to specific location

**Use codex-review as a reference when:**

- Command is a simple wrapper
- Main logic is in external script
- Argument handling is straightforward
- Output is passed through directly




---

## 🚀 Usage

**Reference this template:** `@skill-command-creator.md`


**Platform-specific:**
- **GitHub Copilot**: Add to `.github/copilot-instructions.md`
- **Augment Code**: Use `aug context add` command
- **Cursor/Windsurf**: Reference in chat with `@filename`
- **Claude**: Add to Project Knowledge
- **ChatGPT**: Add to Custom GPT configuration
