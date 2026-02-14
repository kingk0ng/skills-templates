---
id: command-git-bisect-helper-automation
type: command
name: Git Bisect Helper & Automation
description: '---

  allowed-tools: Bash(git bisect:*), Bash(git log:*), Bash(git show:*), Bash(git checkout:*),
  Bash(npm:*), Bash(yarn:*), Bash(pnpm:*), Read, Edit, Grep

  argument-hint: [good-commit] [bad-commit] | --...'
category: git-workflow
complexity: medium
keywords:
- ci/cd
- database
- git
- go
- java
- performance
- python
- rust
- test
- testing
capabilities: []
token_estimate: 1415
has_scripts: true
languages:
- bash
- text
---

<!-- Converted from Claude Command Template -->
<!-- Complexity: Medium -->
<!-- Estimated Tokens: ~1,415 -->


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




# Git Bisect Helper & Automation

> ---
allowed-tools: Bash(git bisect:*), Bash(git log:*), Bash(git show:*), Bash(git checkout:*), Bash(npm:*), Bash(yarn:*), Bash(pnpm:*), Read, Edit, Grep
argument-hint: [good-commit] [bad-commit] | --...

---
allowed-capabilities: File operations, code editing, terminal access, search(git bisect:*), Bash(git log:*), Bash(git show:*), Bash(git checkout:*), Bash(npm:*), Bash(yarn:*), Bash(pnpm:*), Read, Edit, Grep
argument-hint: [good-commit] [bad-commit] | --auto [test-command] | --reset | --continue
description: Use PROACTIVELY to guide automated git bisect sessions for finding regression commits with smart test execution
---

# Git Bisect Helper & Automation

Automated git bisect session to find regression commits: $ARGUMENTS

## Current Repository State

- Current branch: !`git branch --show-current`
- Recent commits: !`git log --oneline -10`
- Git status: !`git status --porcelain`
- Bisect status: !`git bisect log 2>/dev/null || echo "No active bisect session"`
- Available tags: !`git tag --sort=-version:refname | head -10`

## Task

Set up and manage an intelligent git bisect session to identify the exact commit that introduced a regression or bug.

## Bisect Session Management

### 1. Session Initialization
- Analyze commit history to suggest good/bad commit candidates
- Set up bisect session with appropriate range
- Validate that the range actually contains the regression
- Create backup branch before starting bisect

### 2. Automatic Test Execution
- Run specified test command at each bisect point
- Interpret test results (exit codes, output patterns)
- Automatically mark commits as good/bad based on test outcomes
- Handle test environment setup/teardown

### 3. Manual Verification Support
- Provide clear instructions for manual testing at each step
- Show relevant changes in current commit
- Guide user through good/bad decision process
- Maintain bisect log with detailed reasoning

### 4. Smart Commit Analysis
- Analyze commit messages for relevant keywords
- Show file changes that might be related to the issue
- Highlight suspicious patterns or large changes
- Skip obviously unrelated commits when possible

## Bisect Modes

### Automatic Bisect (`--auto [test-command]`)
```bash
# Automatically bisect using test command
/git-bisect-helper --auto "npm test"
/git-bisect-helper --auto "python -m pytest tests/test_regression.py"
/git-bisect-helper --auto "./scripts/check-performance.sh"
```

**Process:**
1. Run test command at each bisect point
2. Mark commit as good (exit code 0) or bad (non-zero)
3. Continue until regression commit is found
4. Provide detailed report of findings

### Manual Guided Bisect
```bash
# Interactive bisect with guidance
/git-bisect-helper v1.2.0 HEAD
/git-bisect-helper abc123 def456
```

**Process:**
1. Show current commit details and changes
2. Provide testing suggestions
3. Wait for user input (good/bad)
4. Continue to next bisect point
5. Offer insights about current commit

### Continue Existing Session (`--continue`)
```bash
# Resume interrupted bisect session
/git-bisect-helper --continue
```

**Process:**
1. Analyze current bisect state
2. Show progress and remaining steps
3. Continue with appropriate mode
4. Provide context from previous steps

### Reset Session (`--reset`)
```bash
# Clean up and reset bisect session
/git-bisect-helper --reset
```

**Process:**
1. End current bisect session
2. Return to original branch
3. Clean up temporary files
4. Provide session summary

## Intelligent Test Execution

### Test Environment Detection
- **Node.js**: Detect package.json and run appropriate package manager
- **Python**: Identify requirements.txt, setup.py, pyproject.toml
- **Ruby**: Look for Gemfile and use bundler
- **Java**: Detect Maven (pom.xml) or Gradle (build.gradle)
- **Go**: Identify go.mod and use go test
- **Rust**: Detect Cargo.toml and use cargo test

### Build System Integration
- Run build process before testing if needed
- Handle dependency installation for older commits
- Manage environment variable requirements
- Skip build for commits that don't compile (mark as bad)

### Test Result Interpretation
- Parse test output for meaningful error patterns
- Distinguish between test failures and environment issues
- Handle flaky tests with retry logic
- Provide confidence levels for automated decisions

## Commit Analysis Features

### Change Impact Assessment
```bash
# Analyze current bisect commit
Files changed: !`git show --name-only --pretty="" HEAD`
Commit message: !`git log -1 --pretty=format:"%s"`
Author and date: !`git log -1 --pretty=format:"%an (%ar)"`
```

### Regression Pattern Detection
- Identify commits touching critical areas
- Flag commits with suspicious change patterns
- Highlight performance-related modifications
- Detect dependency or configuration changes

### Context Preservation
- Maintain detailed log of bisect decisions
- Record reasoning for each good/bad marking
- Save test outputs for later analysis
- Document environmental factors

## Advanced Bisect Strategies

### Skip Strategy for Build Issues
- Automatically skip commits that don't compile
- Handle dependency version conflicts
- Skip commits with known build system issues
- Focus bisect on functional commits only

### Performance Regression Detection
- Use performance benchmarks instead of pass/fail tests
- Set acceptable performance thresholds
- Track performance trends across commits
- Identify performance cliff points

### Multi-criteria Bisecting
- Test multiple aspects simultaneously
- Handle cases where good/bad isn't binary
- Support complex regression scenarios
- Provide weighted decision making

## Bisect Session Reporting

### Progress Tracking
```
Bisect Progress:
🎯 Target: Find regression in user authentication
📊 Commits remaining: ~4 (out of 127)
⏱️  Estimated time: 8 minutes
🔍 Current commit: abc123 - "refactor auth middleware"
```

### Final Report
```
🎉 Regression Found!

Bad Commit: def456
Author: John Doe
Date: 2024-01-15 14:30:00
Message: "optimize database queries"

Files Changed:
- src/auth/database.js
- src/middleware/auth.js
- tests/auth.test.js

Bisect Log: 15 steps, 3 manual verifications
Total Time: 12 minutes

Recovery Commands:
git revert def456                    # Revert the problematic commit
git cherry-pick def456^..def456~1    # Cherry-pick the good parts
```

## Integration with Development Workflow

### CI/CD Integration
- Use same test commands as CI pipeline
- Respect CI environment variables
- Handle containerized test environments
- Integrate with existing quality gates

### Team Collaboration
- Share bisect sessions with team members
- Document findings in issue tracking
- Create reproducible bisect scripts
- Establish team bisect best practices

### Debugging Enhancement
- Generate debug reports for problematic commits
- Create minimal reproduction cases
- Suggest fix approaches based on regression type
- Link to relevant documentation or similar issues

## Safety and Recovery

### Session Backup
- Create backup branch before starting
- Save original HEAD position
- Maintain recovery information
- Handle interrupted sessions gracefully

### Error Handling
- Recover from corrupted bisect state
- Handle repository state conflicts
- Manage disk space issues during long bisects
- Provide clear error messages and solutions

## Example Workflows

### Performance Regression
```bash
# Find when tests became slower
/git-bisect-helper --auto "timeout 30s npm test"
```

### Feature Regression  
```bash
# Find when feature X broke
/git-bisect-helper --auto "./test-feature-x.sh"
```

### Build Regression
```bash
# Find when build started failing
/git-bisect-helper --auto "npm run build"
```

### Manual Investigation
```bash
# Interactive bisect for complex issues
/git-bisect-helper v2.1.0 HEAD
```

The bisect helper provides intelligent automation while maintaining full control over the debugging process, making regression hunting efficient and systematic.

---


## 💻 Code Examples


### Example 1

```bash
# Automatically bisect using test command
/git-bisect-helper --auto "npm test"
/git-bisect-helper --auto "python -m pytest tests/test_regression.py"
/git-bisect-helper --auto "./scripts/check-performance.sh"
```


### Example 2

```bash
# Interactive bisect with guidance
/git-bisect-helper v1.2.0 HEAD
/git-bisect-helper abc123 def456
```


### Example 3

```bash
# Resume interrupted bisect session
/git-bisect-helper --continue
```


### Example 4

```bash
# Clean up and reset bisect session
/git-bisect-helper --reset
```


### Example 5

```bash
# Analyze current bisect commit
Files changed: !`git show --name-only --pretty="" HEAD`
Commit message: !`git log -1 --pretty=format:"%s"`
Author and date: !`git log -1 --pretty=format:"%an (%ar)"`
```


### Example 6

```text
Bisect Progress:
🎯 Target: Find regression in user authentication
📊 Commits remaining: ~4 (out of 127)
⏱️  Estimated time: 8 minutes
🔍 Current commit: abc123 - "refactor auth middleware"
```


### Example 7

```text
🎉 Regression Found!

Bad Commit: def456
Author: John Doe
Date: 2024-01-15 14:30:00
Message: "optimize database queries"

Files Changed:
- src/auth/database.js
- src/middleware/auth.js
- tests/auth.test.js

Bisect Log: 15 steps, 3 manual verifications
Total Time: 12 minutes

Recovery Commands:
git revert def456                    # Revert the problematic commit
git cherry-pick def456^..def456~1    # Cherry-pick the good parts
```


### Example 8

```bash
# Find when tests became slower
/git-bisect-helper --auto "timeout 30s npm test"
```


### Example 9

```bash
# Find when feature X broke
/git-bisect-helper --auto "./test-feature-x.sh"
```


### Example 10

```bash
# Find when build started failing
/git-bisect-helper --auto "npm run build"
```


### Example 11

```bash
# Interactive bisect for complex issues
/git-bisect-helper v2.1.0 HEAD
```


---

## 🚀 Usage

**Reference this template:** `@command-git-bisect-helper-automation.md`


**Platform-specific:**
- **GitHub Copilot**: Add to `.github/copilot-instructions.md`
- **Augment Code**: Use `aug context add` command
- **Cursor/Windsurf**: Reference in chat with `@filename`
- **Claude**: Add to Project Knowledge
- **ChatGPT**: Add to Custom GPT configuration
