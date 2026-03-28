---
id: skill-search
type: skill
name: search
description: Search Google via Bright Data SERP API. Returns structured JSON results
  with title, link, and description. Requires BRIGHTDATA_API_KEY and BRIGHTDATA_UNLOCKER_ZONE
  environment variables.
category: web-data
complexity: medium
keywords:
- api
capabilities: []
token_estimate: 192
---

<!-- Converted from Claude Skill Template -->
<!-- Complexity: Medium -->
<!-- Estimated Tokens: ~192 -->


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




# search

> Search Google via Bright Data SERP API. Returns structured JSON results with title, link, and description. Requires BRIGHTDATA_API_KEY and BRIGHTDATA_UNLOCKER_ZONE environment variables.

# Bright Data - Google Search

Search Google and get structured JSON results using Bright Data's SERP API.

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
bash scripts/search.sh "query" [cursor]
```

**Parameters:**
- `query` (required): Search term
- `cursor` (optional): Page number for pagination (0-indexed, default: 0)

**Examples:**
```bash
# Basic search
bash scripts/search.sh "climate change"

# Get page 2 of results
bash scripts/search.sh "climate change" 1
```

## Output Format

Returns JSON with structured `organic` array:
```json
{
  "organic": [
    {
      "link": "https://example.com/article",
      "title": "Article Title",
      "description": "Brief description of the page..."
    }
  ]
}
```

## Dependencies

- `curl` - For API requests
- `jq` - For JSON processing


---

## 🚀 Usage

**Reference this template:** `@skill-search.md`


**Platform-specific:**
- **GitHub Copilot**: Add to `.github/copilot-instructions.md`
- **Augment Code**: Use `aug context add` command
- **Cursor/Windsurf**: Reference in chat with `@filename`
- **Claude**: Add to Project Knowledge
- **ChatGPT**: Add to Custom GPT configuration
