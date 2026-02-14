---
id: skill-avalonia-zafiro-development
type: skill
name: avalonia-zafiro-development
description: Mandatory skills, conventions, and behavioral rules for Avalonia UI development
  using the Zafiro toolkit.
category: development
complexity: medium
keywords: []
capabilities: []
token_estimate: 249
---

<!-- Converted from Claude Skill Template -->
<!-- Complexity: Medium -->
<!-- Estimated Tokens: ~249 -->


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




# avalonia-zafiro-development

> Mandatory skills, conventions, and behavioral rules for Avalonia UI development using the Zafiro toolkit.

# Avalonia Zafiro Development

This skill defines the mandatory conventions and behavioral rules for developing cross-platform applications with Avalonia UI and the Zafiro toolkit. These rules prioritize maintainability, correctness, and a functional-reactive approach.

## Core Pillars

1.  **Functional-Reactive MVVM**: Pure MVVM logic using DynamicData and ReactiveUI.
2.  **Safety & Predictability**: Explicit error handling with `Result` types and avoidance of exceptions for flow control.
3.  **Cross-Platform Excellence**: Strictly Avalonia-independent ViewModels and composition-over-inheritance.
4.  **Zafiro First**: Leverage existing Zafiro abstractions and helpers to avoid redundancy.

## Guides

- [Core Technical Skills & Architecture](core-technical-skills.md): Fundamental skills and architectural principles.
- [Naming & Coding Standards](naming-standards.md): Rules for naming, fields, and error handling.
- [Avalonia, Zafiro & Reactive Rules](avalonia-reactive-rules.md): Specific guidelines for UI, Zafiro integration, and DynamicData pipelines.
- [Zafiro Shortcuts](zafiro-shortcuts.md): Concise mappings for common Rx/Zafiro operations.
- [Common Patterns](patterns.md): Advanced patterns like `RefreshableCollection` and Validation.

## Procedure Before Writing Code

1.  **Search First**: Search the codebase for similar implementations or existing Zafiro helpers.
2.  **Reusable Extensions**: If a helper is missing, propose a new reusable extension method instead of inlining complex logic.
3.  **Reactive Pipelines**: Ensure DynamicData operators are used instead of plain Rx where applicable.


---

## 🚀 Usage

**Reference this template:** `@skill-avalonia-zafiro-development.md`


**Platform-specific:**
- **GitHub Copilot**: Add to `.github/copilot-instructions.md`
- **Augment Code**: Use `aug context add` command
- **Cursor/Windsurf**: Reference in chat with `@filename`
- **Claude**: Add to Project Knowledge
- **ChatGPT**: Add to Custom GPT configuration
