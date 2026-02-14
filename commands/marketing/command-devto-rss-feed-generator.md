---
id: command-devto-rss-feed-generator
type: command
name: Dev.to RSS Feed Generator
description: '---

  allowed-tools: Read, Write, Bash, Glob

  argument-hint:

  description: Generate Dev.to RSS feed from all blog posts for automatic syndication

  ---...'
category: marketing
complexity: medium
keywords:
- deploy
- deployment
- go
capabilities: []
token_estimate: 331
---

<!-- Converted from Claude Command Template -->
<!-- Complexity: Medium -->
<!-- Estimated Tokens: ~331 -->


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




# Dev.to RSS Feed Generator

> ---
allowed-tools: Read, Write, Bash, Glob
argument-hint:
description: Generate Dev.to RSS feed from all blog posts for automatic syndication
---...

---
allowed-capabilities: File operations, code editing, terminal access, search
argument-hint:
description: Generate Dev.to RSS feed from all blog posts for automatic syndication
---

# Dev.to RSS Feed Generator

Generate a complete RSS feed from all your blog posts for automatic import to Dev.to.

**Usage:** `/publisher:devto` (no arguments needed)

**What it does:**
- Scans all blog posts in your codebase
- Converts markdown to HTML
- Generates RSS 2.0 feed with proper encoding
- Creates `public/rss-devto.xml` file
- Provides setup instructions for Dev.to

**Process:**

1. **Scan for Blog Posts**
   - Search codebase for markdown files
   - Common patterns:
     - `src/content/blog/**/*.md`
     - `content/blog/**/*.md`
     - `posts/**/*.md`
     - `blog/**/*.md`

2. **Parse Blog Posts**
   - Extract frontmatter (title, date, description, tags)
   - Convert markdown body to HTML
   - Encode HTML properly for RSS (CDATA sections)
   - Extract publication dates

3. **Generate RSS Feed**
   - Create valid RSS 2.0 XML structure
   - Include all blog posts as items
   - Add proper channel metadata
   - HTML-encode content for Dev.to compatibility

4. **Save Feed File**
   - Write to `public/rss-devto.xml`
   - Ensure proper XML formatting
   - Validate RSS structure

5. **Display Setup Instructions**
   - Show how to add RSS to Dev.to
   - Explain deployment requirements
   - Guide user through configuration

**One-time Setup:**
1. Run this command to generate RSS feed
2. Deploy your site (make RSS publicly accessible)
3. Go to https://dev.to/settings/extensions
4. Add your RSS URL (e.g., `https://yoursite.com/rss-devto.xml`)
5. Dev.to will auto-import all future posts

**Benefits:**
- Automatic syndication to Dev.to
- All future posts sync automatically
- No manual copying required
- Maintains original formatting


---

## 🚀 Usage

**Reference this template:** `@command-devto-rss-feed-generator.md`


**Platform-specific:**
- **GitHub Copilot**: Add to `.github/copilot-instructions.md`
- **Augment Code**: Use `aug context add` command
- **Cursor/Windsurf**: Reference in chat with `@filename`
- **Claude**: Add to Project Knowledge
- **ChatGPT**: Add to Custom GPT configuration
