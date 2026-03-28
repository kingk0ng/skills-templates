---
id: skill-study-topic-research
type: skill
name: study-topic-research
description: Research a user-specified topic using public websites, select credible
  sources, and synthesize the findings into a detailed study article or learning
  guide. Best for structured explanations, topic breakdowns, source-backed summaries,
  and beginner-to-advanced learning materials.
category: web-data
complexity: medium
keywords:
- article
- learning
- research
- study
- tutorial
- web
capabilities: []
token_estimate: 612
---

<!-- Custom study research template -->
<!-- Complexity: Medium -->
<!-- Estimated Tokens: ~612 -->

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




# study-topic-research

> Research a user-specified topic using public websites, select credible sources, and synthesize the findings into a detailed study article or learning guide.

# Study Topic Research and Article Writing

Use this skill when the user wants to learn a topic deeply from public web sources and needs a well-structured article, tutorial, or study guide instead of a short answer.

## Primary objective

Produce a study-oriented article that:
- matches the exact topic the user asked for
- uses credible public sources
- explains concepts clearly from foundations to practical detail
- separates verified facts from interpretation
- helps the reader study, not just skim

## When to use this skill

Use this skill when the user asks for any of the following:
- a study guide for a topic
- a long-form explanation or article
- a tutorial based on current public information
- a research-backed overview of a subject
- a beginner-to-advanced learning path

Do not use this skill when:
- the user only wants a quick definition
- the topic requires private or licensed data you cannot access
- the user needs code-only output with no explanatory article

## Source selection rules

Prioritize sources in this order:
1. official documentation, standards, specifications, and vendor docs
2. university pages, textbooks, academic labs, and reputable educational sites
3. recognized industry blogs, engineering posts, and technical publications
4. high-quality community explanations only when stronger sources do not cover the gap

Avoid weak sources unless the user explicitly requests them:
- SEO content farms
- anonymous listicles
- pages with no author, no date, or obvious factual drift
- forum opinions used as primary evidence

## Research workflow

1. Restate the requested topic and infer the likely learning level.
2. Break the topic into subtopics the learner must understand.
3. Search for high-quality public sources for each subtopic.
4. Fetch and read the most relevant source pages, not just search snippets.
5. Cross-check important claims across multiple sources.
6. Build a teaching outline before writing.
7. Write the article in clear progression from basic concepts to deeper details.
8. End with a source list and optional next-study steps.

## Related existing templates

This skill fits best when combined with these existing web-data templates:
- `@skill-search.md` to discover candidate public sources
- `@skill-scrape.md` to extract the actual page content as clean markdown

Recommended flow:
1. Search for candidate sources.
2. Scrape the strongest sources.
3. Compare and synthesize them into one study article.

## Topic matching rules

Before writing, ensure the article matches the user's requested scope:
- if the topic is broad, narrow it into a teachable structure
- if the topic is ambiguous, state the interpretation you are using
- if the user names specific subtopics, cover each one explicitly
- if the user asks for practical learning, include examples and common mistakes

## Writing rules

The article should be optimized for understanding:
- open with a plain-language explanation of what the topic is and why it matters
- define important terms before using advanced language
- use sections that build on each other logically
- include concrete examples, analogies, or short scenarios when useful
- explain tradeoffs, limitations, and misconceptions
- avoid unsupported hype and vague claims

## Recommended article structure

Use this structure unless the user asks for something different:

1. Title
2. Why this topic matters
3. Core idea in simple terms
4. Key concepts and terminology
5. How it works step by step
6. Important variations, edge cases, or tradeoffs
7. Practical examples
8. Common misunderstandings
9. Summary for review
10. Sources for further study

## Output quality bar

The final article should:
- be specific enough that a learner can study from it directly
- stay grounded in source material
- avoid filler and repetition
- distinguish basics from advanced details
- mention where the sources disagree or are incomplete

## Reusable prompt block

Use or adapt this prompt when invoking the skill:

```text
Research the topic: {topic}

Goal:
Create a detailed study article that helps a learner understand this topic thoroughly.

Requirements:
- Find credible public web sources
- Prefer official docs and reputable educational material
- Match the requested scope exactly
- Explain the topic from foundations to more advanced details
- Include examples, tradeoffs, and common misconceptions
- End with a concise summary and a source list

Audience:
{beginner | intermediate | advanced}

Output format:
Long-form article suitable for studying.
```

## Example uses

- study article on TCP vs UDP for beginners
- detailed tutorial on OAuth 2.0 flows using official docs
- learning guide for vector databases with practical examples
- explain Kubernetes scheduling in a way a backend engineer can study
- collect sources with `@skill-search.md`, read them with `@skill-scrape.md`, then write a study guide


---

## 🚀 Usage

**Reference this template:** `@skill-study-topic-research.md`


**Platform-specific:**
- **GitHub Copilot**: Add to `.github/copilot-instructions.md`
- **Augment Code**: Use `aug context add` command
- **Cursor/Windsurf**: Reference in chat with `@filename`
- **Claude**: Add to Project Knowledge
- **ChatGPT**: Add to Custom GPT configuration