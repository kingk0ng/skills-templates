---
id: command-create-product-requirement-prompt
type: command
name: Create Product Requirement Prompt
description: '---

  allowed-tools: Read, Write, Edit, WebSearch, Grep, Glob

  argument-hint: [feature-description] | --research | --template | --validate

  description: Create comprehensive Product Requirement Prompt (PR...'
category: project-management
complexity: medium
keywords: []
capabilities: []
token_estimate: 247
---

<!-- Converted from Claude Command Template -->
<!-- Complexity: Medium -->
<!-- Estimated Tokens: ~247 -->


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




# Create Product Requirement Prompt

> ---
allowed-tools: Read, Write, Edit, WebSearch, Grep, Glob
argument-hint: [feature-description] | --research | --template | --validate
description: Create comprehensive Product Requirement Prompt (PR...

---
allowed-capabilities: File operations, code editing, terminal access, searchWebSearch, Grep, Glob
argument-hint: [feature-description] | --research | --template | --validate
description: Create comprehensive Product Requirement Prompt (PRP) with research and validation
---

# Create Product Requirement Prompt

Create comprehensive Product Requirement Prompt (PRP) following structured research process: **$ARGUMENTS**

## PRP Foundation

- Base template: @concept_library/cc_PRP_flow/PRPs/base_template_v1
- PRP concept: @concept_library/cc_PRP_flow/README.md
- Existing PRPs: !`find concept_library/cc_PRP_flow/PRPs/ -name "*.md" | head -5`
- Documentation: @ai_docs/ directory analysis

## Task

Develop comprehensive PRP through systematic research and structured documentation:

**Research Process**:
1. **Documentation Review** - Analyze existing ai_docs/ and project documentation
2. **Web Research** - Gather implementation examples, library docs, and best practices
3. **Template Analysis** - Study base_template_v1 structure and existing PRPs
4. **Codebase Exploration** - Identify patterns, dependencies, and integration points
5. **Context Synthesis** - Compile comprehensive implementation context

**PRP Development**:
- Follow base_template_v1 structure exactly
- Include specific file references and web resources
- Provide curated codebase intelligence
- Define clear validation criteria and success metrics
- Create production-ready implementation guide

**Remember**: PRP = PRD + curated codebase intelligence + agent/runbook—the minimum viable packet an AI needs to ship production-ready code on the first pass.


---

## 🚀 Usage

**Reference this template:** `@command-create-product-requirement-prompt.md`


**Platform-specific:**
- **GitHub Copilot**: Add to `.github/copilot-instructions.md`
- **Augment Code**: Use `aug context add` command
- **Cursor/Windsurf**: Reference in chat with `@filename`
- **Claude**: Add to Project Knowledge
- **ChatGPT**: Add to Custom GPT configuration
