---
id: skill-learning-path-document-creator
type: skill
name: learning-path-document-creator
description: Use when creating structured learning documents, phased curricula,
  study guides, onboarding-style training plans, or tutorial series. Combines
  milestone planning, beginner-first teaching, hands-on exercises, and clear writing
  into one reusable workflow for documentation creation and learning path design.
category: enterprise-communication
complexity: medium
keywords:
- curriculum
- documentation
- guide
- learning
- milestones
- teaching
- tutorial
capabilities: []
token_estimate: 1042
---

<!-- Custom combined learning-path and documentation skill -->
<!-- Complexity: Medium -->
<!-- Estimated Tokens: ~1,042 -->

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




# learning-path-document-creator

> Use when creating structured learning documents, phased curricula, study guides, onboarding-style training plans, or tutorial series.

# Learning Path and Document Creator

Create documents that do two jobs well:
- explain material clearly enough to be read as documentation
- organize learning into a path with milestones, exercises, and projects

This skill is designed for work like:
- beginner learning plans
- phased curricula
- technical onboarding guides
- tutorial series
- study guides
- project-based teaching material

## Best use cases

Use this skill when the user wants:
- a step-by-step learning plan
- a structured teaching document
- phased training material with exercises
- a document that teaches and guides progress
- beginner-friendly but expandable curriculum

Do not use this skill when:
- the user only wants a short answer
- the task is pure reference documentation with no teaching flow
- the task is domain research only and does not need a learning sequence

## What this skill combines

This skill merges the strongest patterns from existing templates:
- onboarding-style milestone planning
- interactive tutorial structure
- beginner-first lesson design
- game and project-based learning framing
- concise, human-readable writing

## Core outputs

Depending on the request, produce one or more of these:
- learning roadmap
- phased curriculum
- tutorial document
- workshop guide
- study guide
- milestone checklist
- practice project sequence

## Workflow

1. Define the learner.
   - Age or experience level
   - Prior knowledge
   - Motivation and goal
   - Time horizon

2. Define the target outcome.
   - What the learner should be able to build, explain, or do
   - What level counts as success
   - Which outputs prove progress

3. Break the topic into stages.
   - Start from fundamentals
   - Group concepts into teachable phases
   - Keep each phase coherent and not overloaded

4. Design each phase.
   - Learning goals
   - Concepts to cover
   - Hands-on exercises
   - A small project or checkpoint
   - Common mistakes to avoid

5. Write the document for humans.
   - Keep the prose direct and concrete
   - Prefer active voice
   - Explain terms before using them heavily
   - Keep sections scannable and useful offline

6. Add progression scaffolding.
   - Milestones
   - Practice tasks
   - Review points
   - Stretch goals for advanced learners

## Recommended document structure

Use this structure unless the user asks for a different format:

1. Title
2. Audience and prerequisites
3. Learning goals
4. Roadmap overview
5. Phase-by-phase curriculum
6. Exercises and mini-projects
7. Milestones and review checkpoints
8. Common pitfalls
9. Recommended resources
10. Next steps

## Phase template

For each phase, include:
- Phase name
- Goal of the phase
- Concepts introduced
- What the learner builds or practices
- One concrete exercise
- One checkpoint that proves understanding

## Teaching rules

Use a beginner-first approach unless told otherwise:
- start with visible, concrete outcomes before deep theory
- keep early wins small and motivating
- avoid introducing too many new concepts at once
- build each exercise on what the learner already used
- choose examples that feel practical and immediate

When writing tutorial sections:
- state audience, prerequisites, and learning goals
- give a short outline before the detailed steps
- make the steps sequential and testable
- include at least one exercise that reinforces the lesson
- include one pitfall and one optional extension

## Writing rules

Write like strong documentation, not marketing copy:
- use specific language
- omit filler
- avoid hype
- prefer examples over abstraction when teaching
- say what the learner will do, not what the document aspires to do

## Project-based learning guidance

When possible, center the path on projects.

Good progression:
- first project proves the learner can use the tools
- second project combines two or three concepts
- later project shows system-level understanding

For beginner audiences:
- keep the first project simple enough to finish in one sitting
- make checkpoints visible and satisfying
- reward progress with tangible results

## Quality checklist

Before finalizing, verify that the document:
- matches the learner's age and level
- has a clear progression from easy to harder work
- includes real exercises, not vague suggestions
- has milestones that can be checked
- uses clear language and avoids jargon overload
- can stand alone without constant external explanation

## Related existing templates

This skill combines patterns from these existing templates:
- `@command-developer-onboarding-guide-generator.md`
- `@command-interactive-documentation-platform.md`
- `@skill-jupyter-notebook.md`
- `@skill-game-development.md`
- `@skill-writing-clearly-and-concisely.md`

## Reusable prompt block

```text
Create a structured learning document for this topic: {topic}

Audience:
{age or level}

Goal:
Build a document that teaches the topic clearly and organizes it into a practical learning path.

Requirements:
- Include prerequisites and learning goals
- Break the material into phases or milestones
- Add exercises and small projects
- Use beginner-first explanations unless told otherwise
- Keep the writing clear, concrete, and useful as documentation
- End with checkpoints, resources, and next steps
```

## Example uses

- create a 4-phase Roblox Studio learning plan for an 11-year-old
- write an onboarding-style curriculum for junior backend engineers
- build a study guide plus exercises for SQL fundamentals
- create a project-based tutorial path for learning React


---

## 🚀 Usage

**Reference this template:** `@skill-learning-path-document-creator.md`


**Platform-specific:**
- **GitHub Copilot**: Add to `.github/copilot-instructions.md`
- **Augment Code**: Use `aug context add` command
- **Cursor/Windsurf**: Reference in chat with `@filename`
- **Claude**: Add to Project Knowledge
- **ChatGPT**: Add to Custom GPT configuration