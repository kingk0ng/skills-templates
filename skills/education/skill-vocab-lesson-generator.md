---
id: skill-vocab-lesson-generator
type: skill
name: vocab-lesson-generator
description: Generate structured vocabulary lessons from a word list or knowledge base
  file. Use this skill whenever a user wants to create language learning lessons, build
  a vocab knowledge base index, organize words into themed groups, generate lesson
  plans from vocabulary lists, or prepare study materials for language mastery. Triggers
  on requests like 'create lessons from my vocab list', 'organize these words into
  lessons', 'build a vocabulary knowledge base', 'generate study materials from word
  list', or any mention of vocabulary lessons, word grouping, or language learning
  content generation.
category: education
complexity: medium
keywords:
- education
- flashcard
- language
- learning
- lesson
- vocabulary
capabilities: []
token_estimate: 3500
has_references: false
reference_count: 0
---

<!-- Converted from Claude Skill Template -->
<!-- Complexity: Medium -->
<!-- Estimated Tokens: ~3,500 -->

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




# vocab-lesson-generator

> Generate structured vocabulary lessons from a word list or knowledge base file. Use this skill whenever a user wants to create language learning lessons, build a vocab knowledge base index, organize words into themed groups, generate lesson plans from vocabulary lists, or prepare study materials for language mastery.

# Vocabulary Lesson Generator

Create themed vocabulary lessons from a source word list. The workflow has two outputs: a **knowledge base index** (full word → meaning lookup for app consumption) and **lesson files** (themed groups of 10–15 words each as Markdown), both with Thai translation for each word.

## Overview

This skill reads a vocabulary source file, extracts all words with their definitions, auto-categorizes them into themes, and produces:

1. `vocab-index.md` — A complete inventory of every word and its meaning, including Thai translation, serving as the app's knowledge base lookup table.
2. `lessons/lesson-NN-<theme>.md` — One file per lesson, each containing 10–15 words grouped by theme with learning objectives, definitions, phonetics, Thai translation, and contextual example sentences.

## Step 1: Read the Vocab Source

The user provides a path to a vocabulary source file. Read the entire file and extract every vocabulary item. Supported formats:

- **Markdown with word lists** — Words may appear as headings, list items, bold terms, or table rows. Look for patterns like `**word** — definition`, `- word: definition`, or tabular layouts.
- **Structured specs** — The file may describe the vocabulary domain (e.g., "Oxford 5000, B2-C1 level, Tech and Finance focus") rather than listing individual words. In this case, use the spec to generate an appropriate word list that matches the described criteria.
- **Plain text** — One word per line, or `word: definition` per line.

If the file describes a vocabulary domain spec rather than listing words directly, tell the user: "Your file describes a vocabulary domain rather than individual words. I'll generate words matching your spec: [summarize spec]. Want me to proceed, or do you have a word list file?"

**Default vocab source path:** The user's vocab files are typically at paths like `/home/kong/git/vocabs/initial.md`. Always confirm the path with the user if not explicitly provided.

## Step 2: Build the Knowledge Base Index

Before generating lessons, produce a complete `vocab-index.md` file. This serves as the single source of truth — the app reads this file for lookups. Every vocabulary row must include a Thai translation.

**Format:**

```markdown
# Vocabulary Knowledge Base Index

> Source: <source file path>
> Total words: <count>
> Generated: <date>
> Domain: <detected or specified domain, e.g., "Tech & Finance, B2-C1">

## Word Index

| # | Word | IPA | Thai Translation | Definition | Theme |
|---|------|-----|------------------|------------|-------|
| 1 | optimize | /ˈɒptɪmaɪz/ | ปรับให้มีประสิทธิภาพสูงสุด | To make the best or most effective use of | Tech |
| 2 | leverage | /ˈlevərɪdʒ/ | ใช้ประโยชน์ให้เต็มที่ | To use something to maximum advantage | Finance |
| ... | ... | ... | ... | ... | ... |
```

**Theme assignment:** Auto-categorize each word into one of these default themes based on its definition and typical usage context:

- **Tech** — Programming, software engineering, system design, DevOps
- **Finance** — Banking, investment, accounting, economics
- **Business** — Management, strategy, marketing, operations
- **General** — Everyday professional English not specific to the above

The user can customize themes. If they specify different categories, use those instead.

## Step 3: Generate Lessons

Group words from the index into lessons of **10–15 words** each, organized by theme. Each theme gets its own sequence of lessons. If a theme has 30 words, that's 2–3 lessons.

### Lesson file naming

```
lessons/
├── lesson-01-tech.md
├── lesson-02-tech.md
├── lesson-01-finance.md
├── lesson-01-business.md
└── lesson-01-general.md
```

### Lesson template

Use this exact structure for every lesson file:

```markdown
# Lesson <NN>: <Theme> — <Descriptive Title>

> **Theme:** <Theme>
> **Words in this lesson:** <count>
> **Target level:** <B2-C1 or as specified>

## Learning Objectives

After completing this lesson, you will be able to:
1. <objective based on the words in this lesson>
2. <objective based on the words in this lesson>
3. <objective based on the words in this lesson>

## Vocabulary

### 1. <Word>

- **IPA:** /<phonetic>/
- **Thai translation:** <natural Thai translation used in professional context>
- **Definition:** <clear, concise definition>
- **Tech example:** "<sentence using the word in a software engineering context>"
- **Finance example:** "<sentence using the word in a finance/business context>"

### 2. <Word>

- **IPA:** /<phonetic>/
- **Thai translation:** <natural Thai translation used in professional context>
- **Definition:** <clear, concise definition>
- **Tech example:** "<sentence using the word in a software engineering context>"
- **Finance example:** "<sentence using the word in a finance/business context>"

<!-- repeat for all words in this lesson -->

## Key Takeaways

- <1-2 sentence summary of the theme connection between these words>
- <common usage pattern or tip for remembering these words>
```

### Writing good example sentences

Example sentences are the most important part of each lesson — they are what makes words stick. Follow these principles:

- **Be specific and realistic.** Don't write generic sentences. Use real-world scenarios a software engineer or finance professional would encounter. Reference actual tools, frameworks, or financial instruments when natural.
- **Tech examples** should sound like something from a code review, architecture discussion, stand-up meeting, or technical document.
- **Finance examples** should sound like something from an earnings call, investment memo, or financial report.
- **Vary sentence complexity.** Mix simple declarative sentences with compound ones. Not every sentence needs to be long.

**Good:**
> "We need to **optimize** the database queries before the load test — response times are 3x above our SLA."

**Bad:**
> "The engineer decided to optimize the system." _(too vague, no context)_

### Writing high-quality Thai translations

Thai translation is required for every word. Keep translations natural and useful for Thai learners in professional contexts.

- Prefer the most common Thai meaning for workplace usage. If multiple meanings exist, choose the one that fits the lesson's theme.
- Avoid word-for-word literal translation when it sounds unnatural in Thai.
- Keep translations concise (typically a phrase, not a full sentence).
- For highly technical terms that are commonly transliterated in Thai, use the transliterated form when appropriate.
- If a word is ambiguous, optionally include one short Thai clarification in parentheses.

**Example:**
> optimize -> ปรับให้มีประสิทธิภาพสูงสุด

**Ambiguous example:**
> scale -> ขยายระบบ (ด้านเทคนิค)

## Step 4: Present Results to the User

After generating all files, present a summary:

```
✅ Knowledge base index: vocab-index.md (<N> words)
✅ Generated <N> lessons across <N> themes:
   - Tech: <N> lessons (<N> words)
   - Finance: <N> lessons (<N> words)
   - Business: <N> lessons (<N> words)
   - General: <N> lessons (<N> words)

Files written to: <output directory>
```

Ask the user if they want to adjust anything — different grouping, more/fewer words per lesson, different themes, or additional example sentence domains.

## Customization

The user can override these defaults:

| Setting | Default | How to change |
|---------|---------|---------------|
| Words per lesson | 10–15 | "Make lessons with 5 words each" |
| Themes | Tech, Finance, Business, General | "Use categories: Frontend, Backend, DevOps, Soft Skills" |
| Example domains | Tech + Finance | "Add medical examples too" |
| Translation language | Thai | "Use Japanese translation instead" |
| Target level | B2-C1 | "Target A2-B1 level" |
| Output directory | `./lessons/` | "Put lessons in `/path/to/dir/`" |

## Edge Cases

- **Duplicate words across themes:** A word can belong to multiple themes (e.g., "leverage" is both Tech and Finance). Assign it to the theme where it's most commonly used. Don't duplicate it across lessons.
- **Too few words for a theme:** If a theme has fewer than 5 words, merge it into the closest related theme rather than creating a tiny lesson.
- **No definitions in source:** If the source file only lists words without definitions, generate definitions yourself and flag this to the user: "Your source file didn't include definitions — I've generated them. Please review `vocab-index.md` for accuracy."
- **No Thai translations in source:** If the source file doesn't include Thai translation, generate Thai translations yourself and flag this to the user: "Your source file didn't include Thai translations — I've generated them. Please review `vocab-index.md` for accuracy."
- **Very large word lists (500+):** Generate the index first and ask the user which themes or lesson range to generate, rather than producing 30+ lesson files at once.
