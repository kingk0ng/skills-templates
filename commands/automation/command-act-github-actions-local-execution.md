---
id: command-act-github-actions-local-execution
type: command
name: Act - GitHub Actions Local Execution
description: '---

  allowed-tools: Read, Edit, Bash

  argument-hint: [workflow-name]

  description: Execute GitHub Actions locally using act

  ---...'
category: automation
complexity: medium
keywords:
- docker
- github
- testing
capabilities: []
token_estimate: 244
has_scripts: true
languages:
- bash
---

<!-- Converted from Claude Command Template -->
<!-- Complexity: Medium -->
<!-- Estimated Tokens: ~244 -->


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




# Act - GitHub Actions Local Execution

> ---
allowed-tools: Read, Edit, Bash
argument-hint: [workflow-name]
description: Execute GitHub Actions locally using act
---...

---
allowed-capabilities: File operations, code editing, terminal access, search
argument-hint: [workflow-name]
description: Execute GitHub Actions locally using act
---

# Act - GitHub Actions Local Execution

Execute GitHub Actions workflows locally using act: $ARGUMENTS

## Current Workflows

- Available workflows: !`find .github/workflows -name "*.yml" -o -name "*.yaml" | head -10`
- Act configuration: @.actrc (if exists)
- Docker status: !`docker --version`

## Task

Execute GitHub Actions workflow locally:

1. **Setup Verification**
   - Ensure act is installed: `act --version`
   - Verify Docker is running
   - Check available workflows in `.github/workflows/`

2. **Workflow Selection**
   - If workflow specified: Run specific workflow `$ARGUMENTS`
   - If no workflow: List all available workflows
   - Check workflow triggers and events

3. **Local Execution**
   - Run workflow with appropriate flags
   - Use secrets from `.env` or `.secrets`
   - Handle platform-specific runners
   - Monitor execution and logs

4. **Debugging Support**
   - Use `--verbose` for detailed output
   - Use `--dry-run` for testing
   - Use `--list` to show available actions

## Example Commands

```bash
# List all workflows
act --list

# Run specific workflow
act workflow_dispatch -W .github/workflows/$ARGUMENTS.yml

# Run with secrets
act --secret-file .env

# Debug mode
act --verbose --dry-run
```


---


## 💻 Code Examples


### Example 1

```bash
# List all workflows
act --list

# Run specific workflow
act workflow_dispatch -W .github/workflows/$ARGUMENTS.yml

# Run with secrets
act --secret-file .env

# Debug mode
act --verbose --dry-run
```


---

## 🚀 Usage

**Reference this template:** `@command-act-github-actions-local-execution.md`


**Platform-specific:**
- **GitHub Copilot**: Add to `.github/copilot-instructions.md`
- **Augment Code**: Use `aug context add` command
- **Cursor/Windsurf**: Reference in chat with `@filename`
- **Claude**: Add to Project Knowledge
- **ChatGPT**: Add to Custom GPT configuration
