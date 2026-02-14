---
id: command-azure-role-selector
type: command
name: azure-role-selector
description: '---

  allowed-tools: Azure MCP/documentation, Azure MCP/bicepschema, Azure MCP/extension_cli_generate,
  Azure MCP/get_bestpractices

  description: When user is asking for guidance for which role to assign ...'
category: azure
complexity: medium
keywords: []
capabilities: []
token_estimate: 174
---

<!-- Converted from Claude Command Template -->
<!-- Complexity: Medium -->
<!-- Estimated Tokens: ~174 -->


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




# azure-role-selector

> ---
allowed-tools: Azure MCP/documentation, Azure MCP/bicepschema, Azure MCP/extension_cli_generate, Azure MCP/get_bestpractices
description: When user is asking for guidance for which role to assign ...

---
allowed-capabilities: File operations, code editing, terminal access, searchAzure MCP/documentation, Azure MCP/bicepschema, Azure MCP/extension_cli_generate, Azure MCP/get_bestpractices
description: When user is asking for guidance for which role to assign to an identity given desired permissions, this agent helps them understand the role that will meet the requirements with least privilege access and how to apply that role.
---

Use 'Azure MCP/documentation' tool to find the minimal role definition that matches the desired permissions the user wants to assign to an identity (If no built-in role matches the desired permissions, use 'Azure MCP/extension_cli_generate' tool to create a custom role definition with the desired permissions). Use 'Azure MCP/extension_cli_generate' tool to generate the CLI commands needed to assign that role to the identity and use the 'Azure MCP/bicepschema' and the 'Azure MCP/get_bestpractices' tool to provide a Bicep code snippet for adding the role assignment.


---

## 🚀 Usage

**Reference this template:** `@command-azure-role-selector.md`


**Platform-specific:**
- **GitHub Copilot**: Add to `.github/copilot-instructions.md`
- **Augment Code**: Use `aug context add` command
- **Cursor/Windsurf**: Reference in chat with `@filename`
- **Claude**: Add to Project Knowledge
- **ChatGPT**: Add to Custom GPT configuration
