---
id: command-all-platform-content-generator
type: command
name: All-Platform Content Generator
description: '---

  allowed-tools: Read, Write, Bash, Glob, WebFetch

  argument-hint: <input> [lang]

  description: Generate content for ALL platforms at once (X, LinkedIn, Medium, Dev.to)

  ---...'
category: marketing
complexity: medium
keywords:
- api
- go
capabilities: []
token_estimate: 288
has_scripts: true
languages:
- bash
---

<!-- Converted from Claude Command Template -->
<!-- Complexity: Medium -->
<!-- Estimated Tokens: ~288 -->


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




# All-Platform Content Generator

> ---
allowed-tools: Read, Write, Bash, Glob, WebFetch
argument-hint: <input> [lang]
description: Generate content for ALL platforms at once (X, LinkedIn, Medium, Dev.to)
---...

---
allowed-capabilities: File operations, code editing, terminal access, searchWebFetch
argument-hint: <input> [lang]
description: Generate content for ALL platforms at once (X, LinkedIn, Medium, Dev.to)
---

# All-Platform Content Generator

Generate content for X/Twitter, LinkedIn, Medium, and Dev.to in a single command.

**Usage:** `$ARGUMENTS`

**Examples:**
```bash
/publisher:all my-post              # All platforms, English
/publisher:all my-post ja           # All platforms, Japanese
/publisher:all article.md           # From file path
```

**What it does:**
Runs all publisher commands sequentially:
1. `/publisher:x` - X/Twitter thread (3 versions: thread, long, short)
2. `/publisher:linkedin` - LinkedIn post with media attachment
3. `/publisher:medium` - Medium-ready HTML article
4. `/publisher:devto` - Dev.to RSS feed (if not already generated)

**Process:**

For each platform:
1. Parse the same input (slug, file, or URL)
2. Generate platform-specific content
3. Create HTML previews
4. Open all previews in browser tabs

**Output:**
- `x-thread-[LANG].html` - X thread with 3 format tabs
- LinkedIn draft post (via API) or HTML preview
- `medium-article-[LANG].html` - Medium-ready content
- `rss-devto.xml` - Complete RSS feed

**Time Savings:**
Instead of running 4 separate commands and spending ~2 hours manually adapting content, this generates everything in one go.

**Next Steps:**
1. Browser tabs open automatically
2. Review each platform's content
3. Copy-paste or publish via API
4. Adjust as needed for your audience

**Note**: Each platform's content is uniquely optimized - not just copy-paste across channels.


---


## 💻 Code Examples


### Example 1

```bash
/publisher:all my-post              # All platforms, English
/publisher:all my-post ja           # All platforms, Japanese
/publisher:all article.md           # From file path
```


---

## 🚀 Usage

**Reference this template:** `@command-all-platform-content-generator.md`


**Platform-specific:**
- **GitHub Copilot**: Add to `.github/copilot-instructions.md`
- **Augment Code**: Use `aug context add` command
- **Cursor/Windsurf**: Reference in chat with `@filename`
- **Claude**: Add to Project Knowledge
- **ChatGPT**: Add to Custom GPT configuration
