---
id: command-session-learning-capture
type: command
name: Session Learning Capture
description: '---

  allowed-tools: Read, Write, Edit, Glob

  argument-hint: [capture-type] | --project-learnings | --implementation-corrections
  | --structure-insights | --workflow-improvements

  description: Capture and ...'
category: team
complexity: medium
keywords:
- optimization
capabilities: []
token_estimate: 340
---

<!-- Converted from Claude Command Template -->
<!-- Complexity: Medium -->
<!-- Estimated Tokens: ~340 -->


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




# Session Learning Capture

> ---
allowed-tools: Read, Write, Edit, Glob
argument-hint: [capture-type] | --project-learnings | --implementation-corrections | --structure-insights | --workflow-improvements
description: Capture and ...

---
allowed-capabilities: File operations, code editing, terminal access, search
argument-hint: [capture-type] | --project-learnings | --implementation-corrections | --structure-insights | --workflow-improvements
description: Capture and document session learnings with automatic knowledge integration and memory updates
---

# Session Learning Capture

Capture and integrate session learnings into project memory and knowledge base: **$ARGUMENTS**

## Current Learning Context

- Session duration: Current Claude Code session learning opportunities
- Memory files: !`find . -name "CLAUDE*.md" | wc -l` available memory files for knowledge integration
- Project complexity: Assessment of project structure and documentation completeness
- Learning patterns: Identification of knowledge gaps and correction opportunities

## Task

Execute comprehensive learning capture with automatic knowledge integration:

**Capture Type**: Use $ARGUMENTS to focus on project learnings, implementation corrections, structure insights, or workflow improvements

**Learning Capture Framework**:
1. **Learning Identification** - Detect new project knowledge, identify implementation corrections, recognize structural insights, note workflow discoveries
2. **Knowledge Classification** - Categorize learning type, assess importance level, determine integration location, evaluate reusability potential
3. **Context Analysis** - Analyze session context, identify triggering conditions, assess knowledge applicability, determine documentation needs
4. **Integration Planning** - Select appropriate memory files, determine update strategy, maintain consistency, preserve existing knowledge
5. **Memory Updates** - Update CLAUDE.md files, enhance documentation, improve workflows, strengthen knowledge base
6. **Validation Process** - Verify accuracy of captured knowledge, ensure integration quality, validate accessibility, confirm usefulness

**Advanced Features**: Automated learning detection, intelligent categorization, context-aware integration, knowledge graph enhancement, version control integration.

**Quality Assurance**: Learning accuracy validation, integration consistency, accessibility optimization, knowledge retrieval efficiency.

**Output**: Comprehensive learning integration with updated memory files, enhanced documentation, improved workflows, and validated knowledge base.

---

## 🚀 Usage

**Reference this template:** `@command-session-learning-capture.md`


**Platform-specific:**
- **GitHub Copilot**: Add to `.github/copilot-instructions.md`
- **Augment Code**: Use `aug context add` command
- **Cursor/Windsurf**: Reference in chat with `@filename`
- **Claude**: Add to Project Knowledge
- **ChatGPT**: Add to Custom GPT configuration
