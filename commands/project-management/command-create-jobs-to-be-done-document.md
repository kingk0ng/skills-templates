---
id: command-create-jobs-to-be-done-document
type: command
name: Create Jobs-to-be-Done Document
description: '---

  allowed-tools: Read, Write, Edit, Grep, Glob

  argument-hint: [feature-name] | --template | --interactive

  description: Create Jobs-to-be-Done (JTBD) analysis for product features

  ---...'
category: project-management
complexity: medium
keywords: []
capabilities: []
token_estimate: 271
---

<!-- Converted from Claude Command Template -->
<!-- Complexity: Medium -->
<!-- Estimated Tokens: ~271 -->


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




# Create Jobs-to-be-Done Document

> ---
allowed-tools: Read, Write, Edit, Grep, Glob
argument-hint: [feature-name] | --template | --interactive
description: Create Jobs-to-be-Done (JTBD) analysis for product features
---...

---
allowed-capabilities: File operations, code editing, terminal access, search
argument-hint: [feature-name] | --template | --interactive
description: Create Jobs-to-be-Done (JTBD) analysis for product features
---

# Create Jobs-to-be-Done Document

You are an experienced Product Manager. Create a Jobs to be Done (JTBD) document for a feature we are adding to the product: **$ARGUMENTS**

**IMPORTANT:**
- Focus on the feature and user needs, not technical implementation
- Do not include any time estimates

## Required Documentation

1. **Product Documentation**: @product-development/resources/product.md (to understand the product)
2. **Feature Idea**: @product-development/current-feature/feature.md (to understand the feature idea)

**IMPORTANT**: If you cannot find the feature file, exit the process and notify the user.

## Task

Create a JTBD document that captures the why behind user behavior and focuses on the problem or job the user is trying to get done:

1. Use the JTBD template from `@product-development/resources/JTBD-template.md` 
2. Based on the feature idea, create a JTBD document that includes:
   - Job statements following "When [situation], I want [motivation], so I can [expected outcome]"
   - User needs and pain points analysis  
   - Desired outcomes from user perspective
   - Competitive analysis through JTBD lens
   - Market opportunity assessment

3. Output the JTBD document to `product-development/current-feature/JTBD.md`

Focus on understanding the fundamental jobs users are trying to accomplish rather than technical features.

---

## 🚀 Usage

**Reference this template:** `@command-create-jobs-to-be-done-document.md`


**Platform-specific:**
- **GitHub Copilot**: Add to `.github/copilot-instructions.md`
- **Augment Code**: Use `aug context add` command
- **Cursor/Windsurf**: Reference in chat with `@filename`
- **Claude**: Add to Project Knowledge
- **ChatGPT**: Add to Custom GPT configuration
