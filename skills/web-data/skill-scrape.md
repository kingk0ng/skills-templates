---
id: skill-scrape
type: skill
name: scrape
description: Scrape any webpage as clean markdown via Bright Data Web Unlocker API.
  Bypasses bot detection and CAPTCHA. Requires BRIGHTDATA_API_KEY and BRIGHTDATA_UNLOCKER_ZONE
  environment variables.
category: web-data
complexity: medium
keywords:
- api
- javascript
capabilities: []
token_estimate: 221
---

<!-- Converted from Claude Skill Template -->
<!-- Complexity: Medium -->
<!-- Estimated Tokens: ~221 -->


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




# scrape

> Scrape any webpage as clean markdown via Bright Data Web Unlocker API. Bypasses bot detection and CAPTCHA. Requires BRIGHTDATA_API_KEY and BRIGHTDATA_UNLOCKER_ZONE environment variables.

# Bright Data - Web Scraper

Scrape any webpage and get clean markdown content using Bright Data's Web Unlocker API. Automatically bypasses bot detection and CAPTCHA.

## Setup

**1. Get your API Key:**
Get a key from [Bright Data Dashboard](https://brightdata.com/cp).

**2. Create a Web Unlocker zone:**
Create a zone at brightdata.com/cp by clicking "Add" (top-right), selecting "Unlocker zone".

**3. Set environment variables:**
```bash
export BRIGHTDATA_API_KEY="your-api-key"
export BRIGHTDATA_UNLOCKER_ZONE="your-zone-name"
```

## Usage

```bash
bash scripts/scrape.sh "url"
```

**Parameters:**
- `url` (required): The webpage URL to scrape

**Examples:**
```bash
# Scrape a news article
bash scripts/scrape.sh "https://example.com/article"

# Scrape a product page
bash scripts/scrape.sh "https://shop.example.com/product/123"
```

## Output Format

Returns clean markdown content extracted from the webpage:
```markdown
# Page Title

Main content of the page converted to markdown format...

## Section Heading

More content...
```

## Features

- **Bot Detection Bypass**: Automatically handles anti-bot measures
- **CAPTCHA Solving**: Bypasses CAPTCHA challenges
- **Clean Markdown**: Returns well-formatted markdown content
- **JavaScript Rendering**: Handles JavaScript-heavy pages

## Dependencies

- `curl` - For API requests


---

## 🚀 Usage

**Reference this template:** `@skill-scrape.md`


**Platform-specific:**
- **GitHub Copilot**: Add to `.github/copilot-instructions.md`
- **Augment Code**: Use `aug context add` command
- **Cursor/Windsurf**: Reference in chat with `@filename`
- **Claude**: Add to Project Knowledge
- **ChatGPT**: Add to Custom GPT configuration
