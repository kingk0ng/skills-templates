---
id: command-memory-spring-cleaning
type: command
name: Memory Spring Cleaning
description: '---

  allowed-tools: Read, Write, Edit, Glob

  argument-hint: [scope] | --claude-md | --documentation | --outdated-patterns | --implementation-sync

  description: Clean and organize project memory files wit...'
category: team
complexity: medium
keywords:
- optimization
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




# Memory Spring Cleaning

> ---
allowed-tools: Read, Write, Edit, Glob
argument-hint: [scope] | --claude-md | --documentation | --outdated-patterns | --implementation-sync
description: Clean and organize project memory files wit...

---
allowed-capabilities: File operations, code editing, terminal access, search
argument-hint: [scope] | --claude-md | --documentation | --outdated-patterns | --implementation-sync
description: Clean and organize project memory files with implementation synchronization and pattern updates
---

# Memory Spring Cleaning

Clean and synchronize project memory with current implementation patterns: **$ARGUMENTS**

## Current Memory Context

- Memory files: !`find . -name "CLAUDE*.md" | wc -l` CLAUDE.md files in project
- Documentation: !`find . -name "README*" -o -name "*.md" | wc -l` total documentation files
- Last update: !`find . -name "CLAUDE.md" -exec stat -c "%y" {} \; 2>/dev/null | head -1 || echo "No CLAUDE.md found"`
- Implementation drift: Analysis of documented vs actual patterns

## Task

Execute comprehensive memory cleanup with implementation synchronization:

**Cleanup Scope**: Use $ARGUMENTS to focus on CLAUDE.md files, general documentation, outdated pattern identification, or implementation synchronization

**Memory Cleaning Framework**:
1. **Memory File Discovery** - Locate all CLAUDE.md and documentation files, assess hierarchy and organization, identify redundant content
2. **Implementation Analysis** - Compare documented patterns with actual code, identify implementation drift, assess accuracy gaps
3. **Pattern Validation** - Verify documented conventions, validate code examples, check dependency accuracy, assess technology stack alignment
4. **Content Optimization** - Remove outdated information, consolidate duplicate content, improve organization structure, enhance clarity
5. **Synchronization Updates** - Update development commands, refresh technology stack references, sync architectural patterns, validate workflows
6. **Quality Assurance** - Ensure consistency across files, validate markdown formatting, check link integrity, maintain version alignment

**Advanced Features**: Automated pattern detection, implementation drift analysis, cross-reference validation, documentation health scoring.

**Memory Health**: Content freshness metrics, accuracy validation, usage pattern analysis, maintenance scheduling recommendations.

**Output**: Cleaned and synchronized memory files with updated patterns, validated implementations, and maintenance recommendations.

---

## 🚀 Usage

**Reference this template:** `@command-memory-spring-cleaning.md`


**Platform-specific:**
- **GitHub Copilot**: Add to `.github/copilot-instructions.md`
- **Augment Code**: Use `aug context add` command
- **Cursor/Windsurf**: Reference in chat with `@filename`
- **Claude**: Add to Project Knowledge
- **ChatGPT**: Add to Custom GPT configuration
