---
id: skill-agile-product-owner
type: skill
name: agile-product-owner
description: Agile product ownership toolkit for Senior Product Owner including INVEST-compliant
  user story generation, sprint planning, backlog management, and velocity tracking.
  Use for story writing, sprint planning, stakeholder communication, and agile ceremonies.
category: business-marketing
complexity: medium
keywords:
- python
capabilities: []
token_estimate: 118
---

<!-- Converted from Claude Skill Template -->
<!-- Complexity: Medium -->
<!-- Estimated Tokens: ~118 -->


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




# agile-product-owner

> Agile product ownership toolkit for Senior Product Owner including INVEST-compliant user story generation, sprint planning, backlog management, and velocity tracking. Use for story writing, sprint planning, stakeholder communication, and agile ceremonies.

# Agile Product Owner

Complete toolkit for Product Owners to excel at backlog management and sprint execution.

## Core Capabilities
- INVEST-compliant user story generation
- Automatic acceptance criteria creation
- Sprint capacity planning
- Backlog prioritization
- Velocity tracking and metrics

## Key Scripts

### user_story_generator.py
Generates well-formed user stories with acceptance criteria from epics.

**Usage**: 
- Generate stories: `python scripts/user_story_generator.py`
- Plan sprint: `python scripts/user_story_generator.py sprint [capacity]`

**Features**:
- Breaks epics into stories
- INVEST criteria validation
- Automatic point estimation
- Priority assignment
- Sprint planning with capacity


---

## 🚀 Usage

**Reference this template:** `@skill-agile-product-owner.md`


**Platform-specific:**
- **GitHub Copilot**: Add to `.github/copilot-instructions.md`
- **Augment Code**: Use `aug context add` command
- **Cursor/Windsurf**: Reference in chat with `@filename`
- **Claude**: Add to Project Knowledge
- **ChatGPT**: Add to Custom GPT configuration
