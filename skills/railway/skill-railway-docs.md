---
id: skill-railway-docs
type: skill
name: railway-docs
description: Fetch up-to-date Railway documentation to answer questions accurately.
  Use when user asks about Railway features, how Railway works, or shares a docs.railway.com
  URL.
category: railway
complexity: medium
keywords:
- api
capabilities: []
token_estimate: 208
---

<!-- Converted from Claude Skill Template -->
<!-- Complexity: Medium -->
<!-- Estimated Tokens: ~208 -->


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




# railway-docs

> Fetch up-to-date Railway documentation to answer questions accurately. Use when user asks about Railway features, how Railway works, or shares a docs.railway.com URL.

# Railway Documentation

Fetch up-to-date Railway documentation to answer questions accurately.

## When to Use

- User asks how something works on Railway (projects, deployments, volumes, etc.)
- User shares a docs.railway.com URL
- User needs current info about Railway features or pricing
- Before answering Railway questions from memory - check the docs first

## LLM-Optimized Sources

Start here for comprehensive, up-to-date info:

| Source             | URL                                         |
| ------------------ | ------------------------------------------- |
| **Full docs**      | `https://docs.railway.com/api/llms-docs.md` |
| **llms.txt index** | `https://railway.com/llms.txt`              |
| **Templates**      | `https://railway.com/llms-templates.md`     |
| **Changelog**      | `https://railway.com/llms-changelog.md`     |
| **Blog**           | `https://blog.railway.com/llms-blog.md`     |

## Fetching Specific Pages

Append `.md` to any docs.railway.com URL:

```
https://docs.railway.com/guides/projects
→ https://docs.railway.com/guides/projects.md
```

## Common Doc Paths

| Topic       | URL                                              |
| ----------- | ------------------------------------------------ |
| Projects    | `https://docs.railway.com/guides/projects.md`    |
| Deployments | `https://docs.railway.com/guides/deployments.md` |
| Volumes     | `https://docs.railway.com/guides/volumes.md`     |
| Variables   | `https://docs.railway.com/guides/variables.md`   |
| CLI         | `https://docs.railway.com/reference/cli-api.md`  |
| Pricing     | `https://docs.railway.com/reference/pricing.md`  |


---

## 🚀 Usage

**Reference this template:** `@skill-railway-docs.md`


**Platform-specific:**
- **GitHub Copilot**: Add to `.github/copilot-instructions.md`
- **Augment Code**: Use `aug context add` command
- **Cursor/Windsurf**: Reference in chat with `@filename`
- **Claude**: Add to Project Knowledge
- **ChatGPT**: Add to Custom GPT configuration
