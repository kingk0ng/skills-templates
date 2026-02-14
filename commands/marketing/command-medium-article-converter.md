---
id: command-medium-article-converter
type: command
name: Medium Article Converter
description: '---

  allowed-tools: Read, Write, Bash, Glob, WebFetch

  argument-hint: <input> [lang]

  description: Convert blog posts to Medium-ready HTML format with image upload markers

  ---...'
category: marketing
complexity: medium
keywords: []
capabilities: []
token_estimate: 325
has_scripts: true
languages:
- bash
---

<!-- Converted from Claude Command Template -->
<!-- Complexity: Medium -->
<!-- Estimated Tokens: ~325 -->


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




# Medium Article Converter

> ---
allowed-tools: Read, Write, Bash, Glob, WebFetch
argument-hint: <input> [lang]
description: Convert blog posts to Medium-ready HTML format with image upload markers
---...

---
allowed-capabilities: File operations, code editing, terminal access, searchWebFetch
argument-hint: <input> [lang]
description: Convert blog posts to Medium-ready HTML format with image upload markers
---

# Medium Article Converter

Convert blog posts to Medium-ready format with proper HTML structure and image handling.

**Usage:** `$ARGUMENTS`

**Examples:**
```bash
/publisher:medium my-post           # Default English
/publisher:medium my-post ja        # Japanese
/publisher:medium article.md        # From file path
/publisher:medium https://blog.com/post  # From URL
```

**Process:**

1. **Parse Input & Detect Source**
   - File path, URL, or blog post slug
   - Optional language parameter (en/ja)

2. **Universal Input Detection**
   - **File**: Read markdown, PDF, HTML, or text
   - **URL**: WebFetch to retrieve content
   - **Slug**: Search codebase for blog post

3. **Convert to Medium Format**
   - Parse markdown and extract frontmatter
   - Convert to clean HTML suitable for Medium
   - Preserve headers, lists, code blocks, quotes
   - Add image upload markers for diagrams
   - Include image paths for easy upload reference

4. **Create HTML Preview File**
   - Generate `medium-article-[LANG].html` preview
   - Include one-click copy button
   - Add image upload instructions with file paths
   - Use Medium-style formatting and colors

5. **Open in Browser**
   - Open HTML preview file
   - Open Medium editor (https://medium.com/new-story)
   - User can copy HTML and paste into Medium
   - Follow image markers to upload diagrams

**Output:**
- HTML preview file with copy button
- Clear image upload markers
- File paths shown for each image
- Ready to paste into Medium editor

**Note**: Works universally - no dependencies required, just Read, Write, and Bash tools.


---


## 💻 Code Examples


### Example 1

```bash
/publisher:medium my-post           # Default English
/publisher:medium my-post ja        # Japanese
/publisher:medium article.md        # From file path
/publisher:medium https://blog.com/post  # From URL
```


---

## 🚀 Usage

**Reference this template:** `@command-medium-article-converter.md`


**Platform-specific:**
- **GitHub Copilot**: Add to `.github/copilot-instructions.md`
- **Augment Code**: Use `aug context add` command
- **Cursor/Windsurf**: Reference in chat with `@filename`
- **Claude**: Add to Project Knowledge
- **ChatGPT**: Add to Custom GPT configuration
