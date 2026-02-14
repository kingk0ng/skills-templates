---
id: command-linkedin-post-generator
type: command
name: LinkedIn Post Generator
description: '---

  allowed-tools: Read, Write, Bash, Glob, WebFetch

  argument-hint: <input> [lang] [custom-file-path]

  description: Generate LinkedIn posts from blog content with automatic media attachment
  via LinkedI...'
category: marketing
complexity: medium
keywords:
- api
- go
- python
- rust
capabilities: []
token_estimate: 417
has_scripts: true
languages:
- bash
- text
---

<!-- Converted from Claude Command Template -->
<!-- Complexity: Medium -->
<!-- Estimated Tokens: ~417 -->


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




# LinkedIn Post Generator

> ---
allowed-tools: Read, Write, Bash, Glob, WebFetch
argument-hint: <input> [lang] [custom-file-path]
description: Generate LinkedIn posts from blog content with automatic media attachment via LinkedI...

---
allowed-capabilities: File operations, code editing, terminal access, searchWebFetch
argument-hint: <input> [lang] [custom-file-path]
description: Generate LinkedIn posts from blog content with automatic media attachment via LinkedIn API
---

# LinkedIn Post Generator

Create professional LinkedIn posts from any content source with optional media attachment.

**Usage:** `$ARGUMENTS`

**Examples:**
```bash
/publisher:linkedin my-post                    # Auto-detect and attach blog diagrams
/publisher:linkedin my-post en                 # English with diagrams
/publisher:linkedin my-post en image.png       # Custom image attachment
/publisher:linkedin my-post ja report.pdf      # Japanese with custom PDF
```

**Process:**

1. **Parse Input Arguments**
   - Content input (slug, file path, or URL)
   - Optional language parameter (en/ja)
   - Optional custom file path for attachment

2. **Universal Input Detection**
   - **File path**: Read and parse (markdown, PDF, HTML, text, JSON)
   - **URL**: Use WebFetch to retrieve content
   - **Slug**: Search codebase for matching blog post

3. **Generate Professional LinkedIn Post**
   - Use thought leadership tone for English
   - Use professional business tone (敬語) for Japanese
   - Extract key insights from actual content
   - Include relevant hashtags (2-4 max)
   - Add link to full article

4. **Handle Media Attachment**
   - **Custom file**: Use specified image/PDF if provided
   - **Auto-detect**: Find blog diagrams if available
   - Supported formats: PNG, JPG, JPEG, PDF

5. **Post via LinkedIn API** (using Bash + curl)
   - Check for credentials in .env file
   - Handle OAuth flow if needed
   - **CRITICAL**: Escape LinkedIn Little Text Format reserved characters: `| { } @ [ ] ( ) < > # * _ ~`
   - Upload media file and get asset URN
   - Create draft post with commentary and media
   - Open LinkedIn in browser for review

**LinkedIn API Authentication:**
1. Create LinkedIn app at https://www.linkedin.com/developers/apps
2. Add credentials to .env:
   ```
   LINKEDIN_CLIENT_ID=your_client_id
   LINKEDIN_CLIENT_SECRET=your_secret
   LINKEDIN_ACCESS_TOKEN=your_token (auto-generated on first use)
   ```

**Without API setup**: Command still generates the post content for manual copy-paste.

**Note**: Works in ANY repo type (Python, Rust, Go, etc.) - uses only bash and curl, no Node.js required.


---


## 💻 Code Examples


### Example 1

```bash
/publisher:linkedin my-post                    # Auto-detect and attach blog diagrams
/publisher:linkedin my-post en                 # English with diagrams
/publisher:linkedin my-post en image.png       # Custom image attachment
/publisher:linkedin my-post ja report.pdf      # Japanese with custom PDF
```


### Example 2

```text
LINKEDIN_CLIENT_ID=your_client_id
   LINKEDIN_CLIENT_SECRET=your_secret
   LINKEDIN_ACCESS_TOKEN=your_token (auto-generated on first use)
```


---

## 🚀 Usage

**Reference this template:** `@command-linkedin-post-generator.md`


**Platform-specific:**
- **GitHub Copilot**: Add to `.github/copilot-instructions.md`
- **Augment Code**: Use `aug context add` command
- **Cursor/Windsurf**: Reference in chat with `@filename`
- **Claude**: Add to Project Knowledge
- **ChatGPT**: Add to Custom GPT configuration
