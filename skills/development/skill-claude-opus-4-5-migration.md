---
id: skill-claude-opus-4-5-migration
type: skill
name: claude-opus-4-5-migration
description: Migrate prompts and code from Claude Sonnet 4.0, Sonnet 4.5, or Opus
  4.1 to Opus 4.5. Use when the user wants to update their codebase, prompts, or API
  calls to use Opus 4.5. Handles model string updates and prompt adjustments for known
  Opus 4.5 behavioral differences. Does NOT migrate Haiku 4.5.
category: development
complexity: medium
keywords:
- api
- python
capabilities: []
token_estimate: 777
has_references: true
reference_count: 2
---

<!-- Converted from Claude Skill Template -->
<!-- Complexity: Medium -->
<!-- Estimated Tokens: ~777 -->
<!-- Includes Reference Materials -->

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




# claude-opus-4-5-migration

> Migrate prompts and code from Claude Sonnet 4.0, Sonnet 4.5, or Opus 4.1 to Opus 4.5. Use when the user wants to update their codebase, prompts, or API calls to use Opus 4.5. Handles model string updates and prompt adjustments for known Opus 4.5 behavioral differences. Does NOT migrate Haiku 4.5.

# Opus 4.5 Migration Guide

One-shot migration from Sonnet 4.0, Sonnet 4.5, or Opus 4.1 to Opus 4.5.

## Migration Workflow

1. Search codebase for model strings and API calls
2. Update model strings to Opus 4.5 (see platform-specific strings below)
3. Remove unsupported beta headers
4. Add effort parameter set to `"high"` (see `references/effort.md`)
5. Summarize all changes made
6. Tell the user: "If you encounter any issues with Opus 4.5, let me know and I can help adjust your prompts."

## Model String Updates

Identify which platform the codebase uses, then replace model strings accordingly.

### Unsupported Beta Headers

Remove the `context-1m-2025-08-07` beta header if present—it is not yet supported with Opus 4.5. Leave a comment noting this:

```python
# Note: 1M context beta (context-1m-2025-08-07) not yet supported with Opus 4.5
```

### Target Model Strings (Opus 4.5)

| Platform | Opus 4.5 Model String |
|----------|----------------------|
| Anthropic API (1P) | `claude-opus-4-5-20251101` |
| AWS Bedrock | `anthropic.claude-opus-4-5-20251101-v1:0` |
| Google Vertex AI | `claude-opus-4-5@20251101` |
| Azure AI Foundry | `claude-opus-4-5-20251101` |

### Source Model Strings to Replace

| Source Model | Anthropic API (1P) | AWS Bedrock | Google Vertex AI |
|--------------|-------------------|-------------|------------------|
| Sonnet 4.0 | `claude-sonnet-4-20250514` | `anthropic.claude-sonnet-4-20250514-v1:0` | `claude-sonnet-4@20250514` |
| Sonnet 4.5 | `claude-sonnet-4-5-20250929` | `anthropic.claude-sonnet-4-5-20250929-v1:0` | `claude-sonnet-4-5@20250929` |
| Opus 4.1 | `claude-opus-4-1-20250422` | `anthropic.claude-opus-4-1-20250422-v1:0` | `claude-opus-4-1@20250422` |

**Do NOT migrate**: Any Haiku models (e.g., `claude-haiku-4-5-20251001`).

## Prompt Adjustments

Opus 4.5 has known behavioral differences from previous models. **Only apply these fixes if the user explicitly requests them or reports a specific issue.** By default, just update model strings.

**Integration guidelines**: When adding snippets, don't just append them to prompts. Integrate them thoughtfully:
- Use XML tags (e.g., `<code_guidelines>`, `<tool_usage>`) to organize additions
- Match the style and structure of the existing prompt
- Place snippets in logical locations (e.g., coding guidelines near other coding instructions)
- If the prompt already uses XML tags, add new content within appropriate existing tags or create consistent new ones

### 1. Tool Overtriggering

Opus 4.5 is more responsive to system prompts. Aggressive language that prevented undertriggering on previous models may now cause overtriggering.

**Apply if**: User reports tools being called too frequently or unnecessarily.

**Find and soften**:
- `CRITICAL:` → remove or soften
- `You MUST...` → `You should...`
- `ALWAYS do X` → `Do X`
- `NEVER skip...` → `Don't skip...`
- `REQUIRED` → remove or soften

Only apply to tool-triggering instructions. Leave other uses of emphasis alone.

### 2. Over-Engineering Prevention

Opus 4.5 tends to create extra files, add unnecessary abstractions, or build unrequested flexibility.

**Apply if**: User reports unwanted files, excessive abstraction, or unrequested features. Add the snippet from `references/prompt-snippets.md`.

### 3. Code Exploration

Opus 4.5 can be overly conservative about exploring code, proposing solutions without reading files.

**Apply if**: User reports the model proposing fixes without inspecting relevant code. Add the snippet from `references/prompt-snippets.md`.

### 4. Frontend Design

**Apply if**: User requests improved frontend design quality or reports generic-looking outputs.

Add the frontend aesthetics snippet from `references/prompt-snippets.md`.

### 5. Thinking Sensitivity

When extended thinking is not enabled (the default), Opus 4.5 is particularly sensitive to the word "think" and its variants. Extended thinking is enabled only if the API request contains a `thinking` parameter.

**Apply if**: User reports issues related to "thinking" while extended thinking is not enabled (no `thinking` parameter in request).

Replace "think" with alternatives like "consider," "believe," or "evaluate."

## Reference

See `references/prompt-snippets.md` for the full text of each snippet to add.

See `references/effort.md` for configuring the effort parameter (only if user requests it).


---


## 📚 Reference Materials


### Prompt Snippets

# Prompt Snippets for Opus 4.5

Only apply these snippets if the user explicitly requests them or reports a specific issue. By default, the migration should only update model strings.

## 1. Tool Overtriggering

**Problem**: Prompts designed to reduce undertriggering on previous models may cause Opus 4.5 to overtrigger.

**When to add**: User reports tools being called too frequently or unnecessarily.

**Solution**: Replace aggressive language with normal phrasing.

| Before | After |
|--------|-------|
| `CRITICAL: You MUST use this tool when...` | `Use this tool when...` |
| `ALWAYS call the search function before...` | `Call the search function before...` |
| `You are REQUIRED to...` | `You should...` |
| `NEVER skip this step` | `Don't skip this step` |

## 2. Over-Engineering Prevention

**Problem**: Opus 4.5 may create extra files, add unnecessary abstractions, or build unrequested flexibility.

**When to add**: User reports unwanted files, excessive abstraction, or unrequested features.

**Snippet to add to system prompt**:

```
- Avoid over-engineering. Only make changes that are directly requested or clearly necessary. Keep solutions simple and focused.
- Don't add features, refactor code, or make "improvements" beyond what was asked. A bug fix doesn't need surrounding code cleaned up. A simple feature doesn't need extra configurability.
- Don't add error handling, fallbacks, or validation for scenarios that can't happen. Trust internal code and framework guarantees. Only validate at system boundaries (user input, external APIs). Don't use backwards-compatibility shims when you can just change the code.
- Don't create helpers, utilities, or abstractions for one-time operations. Don't design for hypothetical future requirements. The right amount of complexity is the minimum needed for the current task. Reuse existing abstractions where possible and follow the DRY principle.
```

## 3. Code Exploration

**Problem**: Opus 4.5 may propose solutions without reading code or make assumptions about unread files.

**When to add**: User reports the model proposing fixes without inspecting relevant code.

**Snippet to add to system prompt**:

```
ALWAYS read and understand relevant files before proposing code edits. Do not speculate about code you have not inspected. If the user references a specific file/path, you MUST open and inspect it before explaining or proposing fixes. Be rigorous and persistent in searching code for key facts. Thoroughly review the style, conventions, and abstractions of the codebase before implementing new features or abstractions.
```

## 4. Frontend Design Quality

**Problem**: Default frontend outputs may look generic ("AI slop" aesthetic).

**When to add**: User requests improved frontend design quality or reports generic-looking outputs.

**Snippet to add to system prompt**:

```xml
<frontend_aesthetics>
You tend to converge toward generic, "on distribution" outputs. In frontend design, this creates what users call the "AI slop" aesthetic. Avoid this: make creative, distinctive frontends that surprise and delight.

Focus on:
- Typography: Choose fonts that are beautiful, unique, and interesting. Avoid generic fonts like Arial and Inter; opt instead for distinctive choices that elevate the frontend's aesthetics.
- Color & Theme: Commit to a cohesive aesthetic. Use CSS variables for consistency. Dominant colors with sharp accents outperform timid, evenly-distributed palettes. Draw from IDE themes and cultural aesthetics for inspiration.
- Motion: Use animations for effects and micro-interactions. Prioritize CSS-only solutions for HTML. Use Motion library for React when available. Focus on high-impact moments: one well-orchestrated page load with staggered reveals (animation-delay) creates more delight than scattered micro-interactions.
- Backgrounds: Create atmosphere and depth rather than defaulting to solid colors. Layer CSS gradients, use geometric patterns, or add contextual effects that match the overall aesthetic.

Avoid generic AI-generated aesthetics:
- Overused font families (Inter, Roboto, Arial, system fonts)
- Clichéd color schemes (particularly purple gradients on white backgrounds)
- Predictable layouts and component patterns
- Cookie-cutter design that lacks context-specific character

Interpret creatively and make unexpected choices that feel genuinely designed for the context. Vary between light and dark themes, different fonts, different aesthetics. You still tend to converge on common choices (Space Grotesk, for example) across generations. Avoid this: it is critical that you think outside the box!
</frontend_aesthetics>
```

## 5. Thinking Sensitivity

**Problem**: When extended thinking is not enabled (the default), Opus 4.5 is particularly sensitive to the word "think" and its variants.

Extended thinking is not enabled by default. It is only enabled if the API request contains a `thinking` parameter:
```json
"thinking": {
    "type": "enabled",
    "budget_tokens": 10000
}
```

**When to apply**: User reports issues related to "thinking" while extended thinking is not enabled (no `thinking` parameter in their request).

**Solution**: Replace "think" with alternative words.

| Before | After |
|--------|-------|
| `think about` | `consider` |
| `think through` | `evaluate` |
| `I think` | `I believe` |
| `think carefully` | `consider carefully` |
| `thinking` | `reasoning` / `considering` |

## Usage Guidelines

1. **Integrate thoughtfully** - Don't just append snippets; weave them into the existing prompt structure
2. **Use XML tags** - Wrap additions in descriptive tags (e.g., `<coding_guidelines>`, `<tool_behavior>`) that match or complement existing prompt structure
3. **Match prompt style** - If the prompt is concise, trim the snippet; if verbose, keep full detail
4. **Place logically** - Put coding snippets near other coding instructions, tool guidance near tool definitions, etc.
5. **Preserve existing content** - Insert snippets without removing functional content
6. **Summarize changes** - After migration, list all model string updates and prompt modifications made




### Effort

# Effort Parameter (Beta)

**Add effort set to `"high"` during migration.** This is the default configuration for best performance with Opus 4.5.

## Overview

Effort controls how eagerly Claude spends tokens. It affects all tokens: thinking, text responses, and function calls.

| Effort | Use Case |
|--------|----------|
| `high` | Best performance, deep reasoning (default) |
| `medium` | Balance of cost/latency vs. performance |
| `low` | Simple, high-volume queries; significant token savings |

## Implementation

Requires beta flag `effort-2025-11-24` in API calls.

**Python SDK:**
```python
response = client.messages.create(
    model="claude-opus-4-5-20251101",
    max_tokens=1024,
    betas=["effort-2025-11-24"],
    output_config={
        "effort": "high"  # or "medium" or "low"
    },
    messages=[...]
)
```

**TypeScript SDK:**
```typescript
const response = await client.messages.create({
  model: "claude-opus-4-5-20251101",
  max_tokens: 1024,
  betas: ["effort-2025-11-24"],
  output_config: {
    effort: "high"  // or "medium" or "low"
  },
  messages: [...]
});
```

**Raw API:**
```json
{
  "model": "claude-opus-4-5-20251101",
  "max_tokens": 1024,
  "anthropic-beta": "effort-2025-11-24",
  "output_config": {
    "effort": "high"
  },
  "messages": [...]
}
```

## Effort vs. Thinking Budget

Effort is independent of thinking budget:

- High effort + no thinking = more tokens, but no thinking tokens
- High effort + 32k thinking = more tokens, but thinking capped at 32k

## Recommendations

1. First determine effort level, then set thinking budget
2. Best performance: high effort + high thinking budget
3. Cost/latency optimization: medium effort
4. Simple high-volume queries: low effort




---

## 🚀 Usage

**Reference this template:** `@skill-claude-opus-4-5-migration.md`


**Platform-specific:**
- **GitHub Copilot**: Add to `.github/copilot-instructions.md`
- **Augment Code**: Use `aug context add` command
- **Cursor/Windsurf**: Reference in chat with `@filename`
- **Claude**: Add to Project Knowledge
- **ChatGPT**: Add to Custom GPT configuration
