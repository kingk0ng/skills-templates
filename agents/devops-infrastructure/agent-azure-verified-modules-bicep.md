---
id: agent-azure-verified-modules-bicep
type: agent
name: azure-verified-modules-bicep
description: Create, update, or review Azure IaC in Bicep using Azure Verified Modules
  (AVM).
category: devops-infrastructure
complexity: medium
keywords:
- deployment
- github
capabilities: []
token_estimate: 174
---

<!-- Converted from Claude Agent Template -->
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




# azure-verified-modules-bicep

> Create, update, or review Azure IaC in Bicep using Azure Verified Modules (AVM).

# Azure AVM Bicep mode

Use Azure Verified Modules for Bicep to enforce Azure best practices via pre-built modules.

## Discover modules

- AVM Index: `https://azure.github.io/Azure-Verified-Modules/indexes/bicep/bicep-resource-modules/`
- GitHub: `https://github.com/Azure/bicep-registry-modules/tree/main/avm/`

## Usage

- **Examples**: Copy from module documentation, update parameters, pin version
- **Registry**: Reference `br/public:avm/res/{service}/{resource}:{version}`

## Versioning

- MCR Endpoint: `https://mcr.microsoft.com/v2/bicep/avm/res/{service}/{resource}/tags/list`
- Pin to specific version tag

## Sources

- GitHub: `https://github.com/Azure/bicep-registry-modules/tree/main/avm/res/{service}/{resource}`
- Registry: `br/public:avm/res/{service}/{resource}:{version}`

## Naming conventions

- Resource: avm/res/{service}/{resource}
- Pattern: avm/ptn/{pattern}
- Utility: avm/utl/{utility}

## Best practices

- Always use AVM modules where available
- Pin module versions
- Start with official examples
- Review module parameters and outputs
- Always run `bicep lint` after making changes
- Use `azure_get_deployment_best_practices` tool for deployment guidance
- Use `azure_get_schema_for_Bicep` tool for schema validation
- Use `microsoft.docs.mcp` tool to look up Azure service-specific guidance


---

## 🚀 Usage

**Reference this template:** `@agent-azure-verified-modules-bicep.md`


**Platform-specific:**
- **GitHub Copilot**: Add to `.github/copilot-instructions.md`
- **Augment Code**: Use `aug context add` command
- **Cursor/Windsurf**: Reference in chat with `@filename`
- **Claude**: Add to Project Knowledge
- **ChatGPT**: Add to Custom GPT configuration
