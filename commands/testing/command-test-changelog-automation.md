---
id: command-test-changelog-automation
type: command
name: Test Changelog Automation
description: '---

  allowed-tools: Read, Write, Edit, Bash

  argument-hint: [automation-type] | --changelog | --workflow-demo | --ci-integration
  | --validation

  description: Automate changelog testing workflow with CI i...'
category: testing
complexity: medium
keywords:
- ci/cd
- deployment
- git
- github
- gitlab
- performance
- test
- testing
capabilities: []
token_estimate: 358
---

<!-- Converted from Claude Command Template -->
<!-- Complexity: Medium -->
<!-- Estimated Tokens: ~358 -->


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




# Test Changelog Automation

> ---
allowed-tools: Read, Write, Edit, Bash
argument-hint: [automation-type] | --changelog | --workflow-demo | --ci-integration | --validation
description: Automate changelog testing workflow with CI i...

---
allowed-capabilities: File operations, code editing, terminal access, search
argument-hint: [automation-type] | --changelog | --workflow-demo | --ci-integration | --validation
description: Automate changelog testing workflow with CI integration and validation
---

# Test Changelog Automation

Automate changelog testing workflow with comprehensive CI integration: **$ARGUMENTS**

## Current Automation Context

- Changelog files: !`find . -name "CHANGELOG*" -o -name "changelog*" | head -1 || echo "No changelog detected"`
- CI system: !`find . -name ".github" -o -name ".gitlab-ci.yml" -o -name "Jenkinsfile" | head -1 || echo "No CI detected"`
- Version control: !`git status >/dev/null 2>&1 && echo "Git repository" || echo "No git repository"`
- Release process: Analysis of existing release automation and versioning

## Task

Implement comprehensive changelog automation with testing and validation workflows:

**Automation Type**: Use $ARGUMENTS to focus on changelog automation, workflow demonstration, CI integration, or validation testing

**Changelog Automation Framework**:

1. **Automation Setup** - Configure changelog generation, setup version control integration, implement automated updates, design validation rules
2. **Workflow Integration** - Design CI/CD integration, configure automated triggers, implement validation checks, optimize execution performance
3. **Testing Strategy** - Create changelog validation tests, implement format verification, design content validation, setup regression testing
4. **Quality Assurance** - Configure automated formatting, implement consistency checks, setup content validation, optimize maintenance workflows
5. **Validation Framework** - Design automated validation rules, implement compliance checking, configure error reporting, optimize feedback loops
6. **CI Integration** - Setup automated execution, configure deployment triggers, implement notification systems, optimize pipeline performance

**Advanced Features**: Automated release note generation, semantic versioning integration, automated documentation updates, compliance validation.

**Quality Metrics**: Changelog accuracy, automation reliability, validation effectiveness, maintenance efficiency.

**Output**: Complete changelog automation with testing workflows, CI integration, validation rules, and maintenance procedures.


---

## 🚀 Usage

**Reference this template:** `@command-test-changelog-automation.md`


**Platform-specific:**
- **GitHub Copilot**: Add to `.github/copilot-instructions.md`
- **Augment Code**: Use `aug context add` command
- **Cursor/Windsurf**: Reference in chat with `@filename`
- **Claude**: Add to Project Knowledge
- **ChatGPT**: Add to Custom GPT configuration
