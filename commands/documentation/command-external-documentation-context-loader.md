---
id: command-external-documentation-context-loader
type: command
name: External Documentation Context Loader
description: '---

  allowed-tools: Bash, WebFetch

  argument-hint: [data-source] | --xatu | --custom-url | --validate

  description: Load and process external documentation context from llms.txt files
  or custom sources

  -...'
category: documentation
complexity: medium
keywords: []
capabilities: []
token_estimate: 215
has_scripts: true
languages:
- bash
---

<!-- Converted from Claude Command Template -->
<!-- Complexity: Medium -->
<!-- Estimated Tokens: ~215 -->


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




# External Documentation Context Loader

> ---
allowed-tools: Bash, WebFetch
argument-hint: [data-source] | --xatu | --custom-url | --validate
description: Load and process external documentation context from llms.txt files or custom sources
-...

---
allowed-capabilities: File operations, code editing, terminal access, searchWebFetch
argument-hint: [data-source] | --xatu | --custom-url | --validate
description: Load and process external documentation context from llms.txt files or custom sources
---

# External Documentation Context Loader

Load external documentation context: $ARGUMENTS

## Current Context Status

- Network access: !`curl -s --connect-timeout 5 https://httpbin.org/status/200 >/dev/null && echo "✅ Available" || echo "❌ Limited"`
- Existing context: Check for local llms.txt or documentation cache
- Project type: @package.json or @README.md (detect project context needs)

## Task

Load and process external documentation context from specified source.

### Default Action (Xatu Data)
Load the llms.txt file from Xatu data repository:
```bash
curl -s https://raw.githubusercontent.com/ethpandaops/xatu-data/refs/heads/master/llms.txt
```

### Custom Source Loading
For custom URLs or alternative documentation sources:
- Validate URL accessibility
- Download and cache content
- Process and structure information
- Integration with project context

### Processing Options
- **Raw loading**: Direct content retrieval
- **Validation**: Check content format and structure  
- **Integration**: Merge with existing project documentation
- **Caching**: Store locally for offline access

---


## 💻 Code Examples


### Example 1

```bash
curl -s https://raw.githubusercontent.com/ethpandaops/xatu-data/refs/heads/master/llms.txt
```


---

## 🚀 Usage

**Reference this template:** `@command-external-documentation-context-loader.md`


**Platform-specific:**
- **GitHub Copilot**: Add to `.github/copilot-instructions.md`
- **Augment Code**: Use `aug context add` command
- **Cursor/Windsurf**: Reference in chat with `@filename`
- **Claude**: Add to Project Knowledge
- **ChatGPT**: Add to Custom GPT configuration
