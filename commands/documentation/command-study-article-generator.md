---
id: command-study-article-generator
type: command
name: Study Article Generator
description: '---

  allowed-tools: Read, Write, Edit, WebFetch, Search

  argument-hint: <topic> [audience] [depth]

  description: Find public web sources and write a study article that teaches the requested topic clearly and in depth

  ---...'
category: documentation
complexity: medium
keywords:
- article
- explain
- learning
- research
- study
- tutorial
capabilities: []
token_estimate: 534
---

<!-- Custom study article command -->
<!-- Complexity: Medium -->
<!-- Estimated Tokens: ~534 -->

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




# Study Article Generator

> ---
allowed-tools: Read, Write, Edit, WebFetch, Search
argument-hint: <topic> [audience] [depth]
description: Find public web sources and write a study article that teaches the requested topic clearly and in depth
---

---
allowed-capabilities: File operations, code editing, web search, webpage fetching
argument-hint: <topic> [audience] [depth]
description: Find strong public sources, match the requested topic, and write a detailed article for learning
---

# Study Article Generator

Create a study article for: $ARGUMENTS

## Task

Research the requested topic using public websites and produce a structured article that a learner can study directly.

## Process

1. Parse the request.
   - Extract the topic.
   - Infer or use the requested audience level.
   - Infer whether the user wants an overview, tutorial, or deep dive.

2. Find source material.
   - Search for official docs first.
   - Add reputable educational and technical sources.
   - Use existing search-style templates or equivalent search tools to discover candidate pages.
   - Use existing scrape-style templates or equivalent fetch tools to read full source pages, not just snippets.
   - Ignore low-quality or obviously recycled pages.

3. Match the scope.
   - Keep the article centered on the exact topic.
   - Cover all explicitly requested subtopics.
   - State any assumptions when the request is broad or ambiguous.

4. Write the article.
   - Start with a simple explanation.
   - Build from fundamentals to deeper detail.
   - Include examples, practical implications, and common mistakes.
   - Explain tradeoffs and limitations.

5. Finish with study aids.
   - Add a short recap.
   - Add a glossary when the topic has dense terminology.
   - Add a source list for further reading.

## Required output structure

- Title
- What this topic is
- Why it matters
- Core concepts
- Detailed explanation
- Practical examples
- Misconceptions or pitfalls
- Recap
- Further reading

## Quality rules

- Prefer accuracy over breadth.
- Prefer primary sources over commentary.
- Do not claim certainty when sources are mixed.
- Keep the writing educational, not promotional.
- Make the result useful for studying offline after generation.

## Related existing templates

- `@skill-search.md` for finding candidate public sources
- `@skill-scrape.md` for extracting full page content before synthesis
- `@command-medium-article-converter.md` if the final article later needs publishing-oriented formatting

## Example invocations

```text
/study:article OAuth 2.0 beginner deep
/study:article database sharding intermediate
/study:article Kubernetes scheduling advanced
```

## Default behavior

If audience is missing, assume intermediate.
If depth is missing, produce a full but focused article.
If the topic is too large, narrow it into the most teachable version and say what scope you chose.


---

## 🚀 Usage

**Reference this template:** `@command-study-article-generator.md`


**Platform-specific:**
- **GitHub Copilot**: Add to `.github/copilot-instructions.md`
- **Augment Code**: Use `aug context add` command
- **Cursor/Windsurf**: Reference in chat with `@filename`
- **Claude**: Add to Project Knowledge
- **ChatGPT**: Add to Custom GPT configuration