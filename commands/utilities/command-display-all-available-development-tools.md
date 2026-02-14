---
id: command-display-all-available-development-tools
type: command
name: Display All Available Development Tools
description: Display all available development tools...
category: utilities
complexity: medium
keywords:
- github
- typescript
capabilities: []
token_estimate: 193
---

<!-- Converted from Claude Command Template -->
<!-- Complexity: Medium -->
<!-- Estimated Tokens: ~193 -->


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




# Display All Available Development Tools

> Display all available development tools...

# Display All Available Development Tools

Display all available development tools

*Command originally created by IndyDevDan (YouTube: https://www.youtube.com/@indydevdan) / DislerH (GitHub: https://github.com/disler)*

## Instructions

Display all available tools from your system prompt in the following format:

1. **List each tool** with its TypeScript function signature
2. **Include the purpose** of each tool as a suffix
3. **Use double line breaks** between tools for readability
4. **Format as bullet points** for clear organization

The output should help developers understand:
- What tools are available in the current Claude Code session
- The exact function signatures for reference
- The primary purpose of each tool

Example format:
```typescript
• functionName(parameters: Type): ReturnType - Purpose of the tool

• anotherFunction(params: ParamType): ResultType - What this tool does
```

This command is useful for:
- Quick reference of available capabilities
- Understanding tool signatures
- Planning which tools to use for specific tasks

---


## 💻 Code Examples


### Example 1

```typescript
• functionName(parameters: Type): ReturnType - Purpose of the tool

• anotherFunction(params: ParamType): ResultType - What this tool does
```


---

## 🚀 Usage

**Reference this template:** `@command-display-all-available-development-tools.md`


**Platform-specific:**
- **GitHub Copilot**: Add to `.github/copilot-instructions.md`
- **Augment Code**: Use `aug context add` command
- **Cursor/Windsurf**: Reference in chat with `@filename`
- **Claude**: Add to Project Knowledge
- **ChatGPT**: Add to Custom GPT configuration
