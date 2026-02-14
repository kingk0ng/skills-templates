---
id: command-create-product-requirements-document
type: command
name: Create Product Requirements Document
description: '---

  allowed-tools: Read, Write, Edit, Grep, Glob

  argument-hint: [feature-name] | --template | --interactive

  description: Create Product Requirements Document (PRD) for new features

  ---...'
category: project-management
complexity: medium
keywords: []
capabilities: []
token_estimate: 245
---

<!-- Converted from Claude Command Template -->
<!-- Complexity: Medium -->
<!-- Estimated Tokens: ~245 -->


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




# Create Product Requirements Document

> ---
allowed-tools: Read, Write, Edit, Grep, Glob
argument-hint: [feature-name] | --template | --interactive
description: Create Product Requirements Document (PRD) for new features
---...

---
allowed-capabilities: File operations, code editing, terminal access, search
argument-hint: [feature-name] | --template | --interactive
description: Create Product Requirements Document (PRD) for new features
---

# Create Product Requirements Document

You are an experienced Product Manager. Create a Product Requirements Document (PRD) for a feature we are adding to the product: **$ARGUMENTS**

**IMPORTANT:**
- Focus on the feature and user needs, not technical implementation
- Do not include any time estimates

## Product Context

1. **Product Documentation**: @product-development/resources/product.md (to understand the product)
2. **Feature Documentation**: @product-development/current-feature/feature.md (to understand the feature idea)
3. **JTBD Documentation**: @product-development/current-feature/JTBD.md (to understand the Jobs to be Done)

## Task

Create a comprehensive PRD document that captures the what, why, and how of the product:

1. Use the PRD template from `@product-development/resources/PRD-template.md`
2. Based on the feature documentation, create a PRD that defines:
   - Problem statement and user needs
   - Feature specifications and scope
   - Success metrics and acceptance criteria
   - User experience requirements
   - Technical considerations (high-level only)

3. Output the completed PRD to `product-development/current-feature/PRD.md`

Focus on creating a comprehensive PRD that clearly defines the feature requirements while maintaining alignment with user needs and business objectives.

---

## 🚀 Usage

**Reference this template:** `@command-create-product-requirements-document.md`


**Platform-specific:**
- **GitHub Copilot**: Add to `.github/copilot-instructions.md`
- **Augment Code**: Use `aug context add` command
- **Cursor/Windsurf**: Reference in chat with `@filename`
- **Claude**: Add to Project Knowledge
- **ChatGPT**: Add to Custom GPT configuration
