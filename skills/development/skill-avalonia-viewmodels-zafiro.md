---
id: skill-avalonia-viewmodels-zafiro
type: skill
name: avalonia-viewmodels-zafiro
description: Optimal ViewModel and Wizard creation patterns for Avalonia using Zafiro
  and ReactiveUI.
category: development
complexity: medium
keywords: []
capabilities: []
token_estimate: 232
---

<!-- Converted from Claude Skill Template -->
<!-- Complexity: Medium -->
<!-- Estimated Tokens: ~232 -->


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




# avalonia-viewmodels-zafiro

> Optimal ViewModel and Wizard creation patterns for Avalonia using Zafiro and ReactiveUI.

# Avalonia ViewModels with Zafiro

This skill provides a set of best practices and patterns for creating ViewModels, Wizards, and managing navigation in Avalonia applications, leveraging the power of **ReactiveUI** and the **Zafiro** toolkit.

## Core Principles

1.  **Functional-Reactive Approach**: Use ReactiveUI (`ReactiveObject`, `WhenAnyValue`, etc.) to handle state and logic.
2.  **Enhanced Commands**: Utilize `IEnhancedCommand` for better command management, including progress reporting and name/text attributes.
3.  **Wizard Pattern**: Implement complex flows using `SlimWizard` and `WizardBuilder` for a declarative and maintainable approach.
4.  **Automatic Section Discovery**: Use the `[Section]` attribute to register and discover UI sections automatically.
5.  **Clean Composition**: map ViewModels to Views using `DataTypeViewLocator` and manage dependencies in the `CompositionRoot`.

## Guides

- [ViewModels & Commands](viewmodels.md): Creating robust ViewModels and handling commands.
- [Wizards & Flows](wizards.md): Building multi-step wizards with `SlimWizard`.
- [Navigation & Sections](navigation_sections.md): Managing navigation and section-based UIs.
- [Composition & Mapping](composition.md): Best practices for View-ViewModel wiring and DI.

## Example Reference

For real-world implementations, refer to the **Angor** project:
- `CreateProjectFlowV2.cs`: Excellent example of complex Wizard building.
- `HomeViewModel.cs`: Simple section ViewModel using functional-reactive commands.


---

## 🚀 Usage

**Reference this template:** `@skill-avalonia-viewmodels-zafiro.md`


**Platform-specific:**
- **GitHub Copilot**: Add to `.github/copilot-instructions.md`
- **Augment Code**: Use `aug context add` command
- **Cursor/Windsurf**: Reference in chat with `@filename`
- **Claude**: Add to Project Knowledge
- **ChatGPT**: Add to Custom GPT configuration
