---
id: skill-game-design
type: skill
name: game-design
description: Game design principles. GDD structure, balancing, player psychology,
  progression.
category: creative-design
complexity: medium
keywords:
- rest
- test
capabilities:
- file_reading
- file_search
- text_search
token_estimate: 556
---

<!-- Converted from Claude Skill Template -->
<!-- Complexity: Medium -->
<!-- Estimated Tokens: ~556 -->


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




# game-design

> Game design principles. GDD structure, balancing, player psychology, progression.

# Game Design Principles

> Design thinking for engaging games.

---

## 1. Core Loop Design

### The 30-Second Test

```
Every game needs a fun 30-second loop:
1. ACTION → Player does something
2. FEEDBACK → Game responds
3. REWARD → Player feels good
4. REPEAT
```

### Loop Examples

| Genre | Core Loop |
|-------|-----------|
| Platformer | Run → Jump → Land → Collect |
| Shooter | Aim → Shoot → Kill → Loot |
| Puzzle | Observe → Think → Solve → Advance |
| RPG | Explore → Fight → Level → Gear |

---

## 2. Game Design Document (GDD)

### Essential Sections

| Section | Content |
|---------|---------|
| **Pitch** | One-sentence description |
| **Core Loop** | 30-second gameplay |
| **Mechanics** | How systems work |
| **Progression** | How player advances |
| **Art Style** | Visual direction |
| **Audio** | Sound direction |

### Principles

- Keep it living (update regularly)
- Visuals help communicate
- Less is more (start small)

---

## 3. Player Psychology

### Motivation Types

| Type | Driven By |
|------|-----------|
| **Achiever** | Goals, completion |
| **Explorer** | Discovery, secrets |
| **Socializer** | Interaction, community |
| **Killer** | Competition, dominance |

### Reward Schedules

| Schedule | Effect | Use |
|----------|--------|-----|
| **Fixed** | Predictable | Milestone rewards |
| **Variable** | Addictive | Loot drops |
| **Ratio** | Effort-based | Grind games |

---

## 4. Difficulty Balancing

### Flow State

```
Too Hard → Frustration → Quit
Too Easy → Boredom → Quit
Just Right → Flow → Engagement
```

### Balancing Strategies

| Strategy | How |
|----------|-----|
| **Dynamic** | Adjust to player skill |
| **Selection** | Let player choose |
| **Accessibility** | Options for all |

---

## 5. Progression Design

### Progression Types

| Type | Example |
|------|---------|
| **Skill** | Player gets better |
| **Power** | Character gets stronger |
| **Content** | New areas unlock |
| **Story** | Narrative advances |

### Pacing Principles

- Early wins (hook quickly)
- Gradually increase challenge
- Rest beats between intensity
- Meaningful choices

---

## 6. Anti-Patterns

| ❌ Don't | ✅ Do |
|----------|-------|
| Design in isolation | Playtest constantly |
| Polish before fun | Prototype first |
| Force one way to play | Allow player expression |
| Punish excessively | Reward progress |

---

> **Remember:** Fun is discovered through iteration, not designed on paper.


---

## 🚀 Usage

**Reference this template:** `@skill-game-design.md`


**Platform-specific:**
- **GitHub Copilot**: Add to `.github/copilot-instructions.md`
- **Augment Code**: Use `aug context add` command
- **Cursor/Windsurf**: Reference in chat with `@filename`
- **Claude**: Add to Project Knowledge
- **ChatGPT**: Add to Custom GPT configuration
