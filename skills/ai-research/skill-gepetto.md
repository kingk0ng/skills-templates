---
id: skill-gepetto
type: skill
name: gepetto
description: Creates detailed, sectionized implementation plans through research,
  stakeholder interviews, and multi-LLM review. Use when planning features that need
  thorough pre-implementation analysis.
category: ai-research
complexity: medium
keywords: []
capabilities: []
token_estimate: 1710
has_references: true
reference_count: 5
---

<!-- Converted from Claude Skill Template -->
<!-- Complexity: Medium -->
<!-- Estimated Tokens: ~1,710 -->
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




# gepetto

> Creates detailed, sectionized implementation plans through research, stakeholder interviews, and multi-LLM review. Use when planning features that need thorough pre-implementation analysis.

# Gepetto

Orchestrates a multi-step planning process: Research → Interview → Spec Synthesis → Plan → External Review → Sections

## CRITICAL: First Actions

**BEFORE anything else**, do these in order:

### 1. Print Intro

Print intro banner immediately:
```
═══════════════════════════════════════════════════════════════
GEPETTO: AI-Assisted Implementation Planning
═══════════════════════════════════════════════════════════════
Research → Interview → Spec Synthesis → Plan → External Review → Sections

Note: GEPETTO will write many .md files to the planning directory you pass it
```

### 2. Validate Spec File Input

**Check if user provided @file at invocation AND it's a spec file (ends with `.md`).**

If NO @file was provided OR the path doesn't end with `.md`, output this and STOP:
```
═══════════════════════════════════════════════════════════════
GEPETTO: Spec File Required
═══════════════════════════════════════════════════════════════

This skill requires a markdown spec file path (must end with .md).
The planning directory is inferred from the spec file's parent directory.

To start a NEW plan:
  1. Create a markdown spec file describing what you want to build
  2. It can be as detailed or as vague as you like
  3. Place it in a directory where gepetto can save planning files
  4. Run: /gepetto @path/to/your-spec.md

To RESUME an existing plan:
  1. Run: /gepetto @path/to/your-spec.md

Example: /gepetto @planning/my-feature-spec.md
═══════════════════════════════════════════════════════════════
```
**Do not continue. Wait for user to re-invoke with a .md file path.**

### 3. Setup Planning Session

Determine session state by checking existing files:

1. Set `planning_dir` = parent directory of the spec file
2. Set `initial_file` = the spec file path
3. Scan for existing planning files:
   - `claude-research.md`
   - `claude-interview.md`
   - `claude-spec.md`
   - `claude-plan.md`
   - `claude-integration-notes.md`
   - `claude-ralph-loop-prompt.md`
   - `claude-ralphy-prd.md`
   - `reviews/` directory
   - `sections/` directory

4. Determine mode and resume point:

| Files Found | Mode | Resume From |
|-------------|------|-------------|
| None | new | Step 4 |
| research only | resume | Step 6 (interview) |
| research + interview | resume | Step 8 (spec synthesis) |
| + spec | resume | Step 9 (plan) |
| + plan | resume | Step 10 (external review) |
| + reviews | resume | Step 11 (integrate) |
| + integration-notes | resume | Step 12 (user review) |
| + sections/index.md | resume | Step 14 (write sections) |
| all sections complete | resume | Step 15 (execution files) |
| + claude-ralph-loop-prompt.md + claude-ralphy-prd.md | complete | Done |

5. Create TODO list with TodoWrite based on current state

Print status:
```
Planning directory: {planning_dir}
Mode: {mode}
```

If resuming:
```
Resuming from step {N}
To start fresh, delete the planning directory files.
```

---

## Logging Format

```
═══════════════════════════════════════════════════════════════
STEP {N}/17: {STEP_NAME}
═══════════════════════════════════════════════════════════════
{details}
Step {N} complete: {summary}
───────────────────────────────────────────────────────────────
```

---

## Workflow

### 4. Research Decision

See [research-protocol.md](references/research-protocol.md).

1. Read the spec file
2. Extract potential research topics (technologies, patterns, integrations)
3. Ask user about codebase research needs
4. Ask user about web research needs (present derived topics as multi-select)
5. Record which research types to perform in step 5

### 5. Execute Research

See [research-protocol.md](references/research-protocol.md).

Based on decisions from step 4, launch research subagents:
- **Codebase research:** `Task(subagent_type=Explore)`
- **Web research:** `Task(subagent_type=Explore)` with WebSearch

If both are needed, launch both Task tools in parallel (single message with multiple tool calls).

**Important:** Subagents return their findings - they do NOT write files directly. After collecting results from all subagents, combine them and write to `<planning_dir>/claude-research.md`.

Skip this step entirely if user chose no research in step 4.

### 6. Detailed Interview

See [interview-protocol.md](references/interview-protocol.md)

Run in main context (AskUserQuestion requires it). The interview should be informed by:
- The initial spec
- Research findings (if any)

### 7. Save Interview Transcript

Write Q&A to `<planning_dir>/claude-interview.md`

### 8. Write Initial Spec (Spec Synthesis)

Combine into `<planning_dir>/claude-spec.md`:
- **Initial input** (the spec file)
- **Research findings** (if step 5 was done)
- **Interview answers** (from step 6)

This synthesizes the user's raw requirements into a complete specification.

### 9. Generate Implementation Plan

Create detailed plan → `<planning_dir>/claude-plan.md`

**IMPORTANT**: Write for an unfamiliar reader. The plan must be fully self-contained - an engineer or LLM with no prior context should understand *what* we're building, *why*, and *how* just from reading this document.

### 10. External Review

See [external-review.md](references/external-review.md)

Launch TWO subagents in parallel to review the plan:
1. **Gemini** via Bash
2. **Codex** via Bash

Both receive the plan content and return their analysis. Write results to `<planning_dir>/reviews/`.

### 11. Integrate External Feedback

Analyze the suggestions in `<planning_dir>/reviews/`.

You are the authority on what to integrate or not. It's OK if you decide to not integrate anything.

**Step 1:** Write `<planning_dir>/claude-integration-notes.md` documenting:
- What suggestions you're integrating and why
- What suggestions you're NOT integrating and why

**Step 2:** Update `<planning_dir>/claude-plan.md` with the integrated changes.

### 12. User Review of Integrated Plan

Use AskUserQuestion:
```
The plan has been updated with external feedback. You can now review and edit claude-plan.md.

If you want Claude's help editing the plan, open a separate Claude session - this session
is mid-workflow and can't assist with edits until the workflow completes.

When you're done reviewing, select "Done" to continue.
```

Options: "Done reviewing"

Wait for user confirmation before proceeding.

### 13. Create Section Index

See [section-index.md](references/section-index.md)

Read `claude-plan.md`. Identify natural section boundaries and create `<planning_dir>/sections/index.md`.

**CRITICAL:** index.md MUST start with a SECTION_MANIFEST block. See the reference for format requirements.

Write `index.md` before proceeding to section file creation.

### 14. Write Section Files — Parallel Subagents

See [section-splitting.md](references/section-splitting.md)

**Launch parallel subagents** - one Task per section for maximum efficiency:

1. First, parse `sections/index.md` to get the SECTION_MANIFEST list
2. Then launch ALL section Tasks in a single message (parallel execution):

```
# Launch all in ONE message for parallel execution:

Task(
  subagent_type="general-purpose",
  prompt="""
  Write section file: section-01-{name}

  Inputs:
  - <planning_dir>/claude-plan.md
  - <planning_dir>/sections/index.md

  Output: <planning_dir>/sections/section-01-{name}.md

  The section file must be COMPLETELY SELF-CONTAINED. Include:
  - Background (why this section exists)
  - Requirements (what must be true when complete)
  - Dependencies (requires/blocks)
  - Implementation details (from the plan)
  - Acceptance criteria (checkboxes)
  - Files to create/modify

  The implementer should NOT need to reference any other document.
  """
)

Task(
  subagent_type="general-purpose",
  prompt="Write section file: section-02-{name} ..."
)

Task(
  subagent_type="general-purpose",
  prompt="Write section file: section-03-{name} ..."
)

# ... one Task per section in the manifest
```

Wait for ALL subagents to complete before proceeding.

### 15. Generate Execution Files — Subagent

**Delegate to subagent** to reduce main context token usage:

```
Task(
  subagent_type="general-purpose",
  prompt="""
  Generate two execution files for autonomous implementation.

  Input files:
  - <planning_dir>/sections/index.md (has SECTION_MANIFEST)
  - <planning_dir>/sections/section-*.md (all section files)

  OUTPUT 1: <planning_dir>/claude-ralph-loop-prompt.md
  For ralph-loop plugin. EMBED all section content inline.

  Structure:
  - Mission statement
  - Full content of sections/index.md
  - Full content of EACH section file (embedded, not referenced)
  - Execution rules (dependency order, verify acceptance criteria)
  - Completion signal: <promise>ALL-SECTIONS-COMPLETE</promise>

  OUTPUT 2: <planning_dir>/claude-ralphy-prd.md
  For Ralphy CLI. REFERENCE section files (don't embed).

  Structure:
  - PRD header
  - How to use (ralphy --prd command)
  - Context explanation
  - Checkbox task list: one "- [ ] Section NN: {name}" per section

  Write both files.
  """
)
```

Wait for subagent completion before proceeding.

### 16. Final Status

Verify all files were created successfully:
- All section files from SECTION_MANIFEST
- `claude-ralph-loop-prompt.md`
- `claude-ralphy-prd.md`

### 17. Output Summary

Print generated files and next steps:
```
═══════════════════════════════════════════════════════════════
GEPETTO: Planning Complete
═══════════════════════════════════════════════════════════════

Generated files:
  - claude-research.md (research findings)
  - claude-interview.md (Q&A transcript)
  - claude-spec.md (synthesized specification)
  - claude-plan.md (implementation plan)
  - claude-integration-notes.md (feedback decisions)
  - reviews/ (external LLM feedback)
  - sections/ (implementation units)
  - claude-ralph-loop-prompt.md (for ralph-loop plugin)
  - claude-ralphy-prd.md (for Ralphy CLI)

How to implement:

Option A - Manual (recommended for learning/control):
  1. Read sections/index.md to understand dependencies
  2. Implement each section file in order
  3. Each section is self-contained with acceptance criteria

Option B - Autonomous with ralph-loop (Claude Code plugin):
  /ralph-loop @<planning_dir>/claude-ralph-loop-prompt.md --completion-promise "COMPLETE" --max-iterations 100

Option C - Autonomous with Ralphy (external CLI):
  ralphy --prd <planning_dir>/claude-ralphy-prd.md
  # Or: cp <planning_dir>/claude-ralphy-prd.md ./PRD.md && ralphy
═══════════════════════════════════════════════════════════════
```


---


## 📚 Reference Materials


### External Review

# External Review Protocol

This step sends `claude-plan.md` to external LLMs (Gemini and Codex) for independent review using CLI subagents.

## Overview

Launch TWO parallel Bash commands to get external reviews:
1. **Gemini CLI** - Google's Gemini 3 Pro
2. **Codex CLI** - OpenAI's GPT-5.2

Both reviewers receive the same plan and return their analysis.

## Review Prompt

Use this prompt for both reviewers:

```
You are a senior software architect reviewing an implementation plan.

The plan is self-contained - it includes all background, context, and requirements.

Identify:
- Potential footguns and edge cases
- Missing considerations
- Security vulnerabilities
- Performance issues
- Architectural problems
- Unclear or ambiguous requirements
- Anything else worth adding to the plan

Be specific and actionable. Reference specific sections. Give your honest, unconstrained assessment.

Here is the plan to review:

{PLAN_CONTENT}
```

## Execution

### Step 1: Read the Plan

```bash
plan_content=$(cat "<planning_dir>/claude-plan.md")
```

### Step 2: Launch Both Reviews in Parallel

Use TWO Bash tool calls in a single message:

**Gemini Review:**
```bash
gemini -m gemini-3-pro-preview --approval-mode yolo "You are a senior software architect reviewing an implementation plan.

The plan is self-contained - it includes all background, context, and requirements.

Identify:
- Potential footguns and edge cases
- Missing considerations
- Security vulnerabilities
- Performance issues
- Architectural problems
- Unclear or ambiguous requirements
- Anything else worth adding to the plan

Be specific and actionable. Reference specific sections. Give your honest, unconstrained assessment.

Here is the plan to review:

$(cat '<planning_dir>/claude-plan.md')"
```

**Codex Review:**
```bash
echo "You are a senior software architect reviewing an implementation plan.

The plan is self-contained - it includes all background, context, and requirements.

Identify:
- Potential footguns and edge cases
- Missing considerations
- Security vulnerabilities
- Performance issues
- Architectural problems
- Unclear or ambiguous requirements
- Anything else worth adding to the plan

Be specific and actionable. Reference specific sections. Give your honest, unconstrained assessment.

Here is the plan to review:

$(cat '<planning_dir>/claude-plan.md')" | codex exec -m gpt-5.2 --sandbox read-only --skip-git-repo-check --full-auto 2>/dev/null
```

### Step 3: Write Review Files

Create `<planning_dir>/reviews/` directory and write:
- `gemini-review.md` - Gemini's analysis
- `codex-review.md` - Codex's analysis

Format each file:
```markdown
# {Provider} Review

**Model:** {model_name}
**Generated:** {timestamp}

---

{review_content}
```

## Handling Failures

| Scenario | Action |
|----------|--------|
| Gemini fails, Codex succeeds | Write only codex-review.md, note Gemini failure |
| Codex fails, Gemini succeeds | Write only gemini-review.md, note Codex failure |
| Both fail | Ask user if they want to retry or skip external review |
| CLI not installed | Skip that reviewer, note in output |

## Notes

- **Gemini**: Uses `--approval-mode yolo` for non-interactive execution
- **Codex**: Uses `--full-auto` and `2>/dev/null` to suppress thinking tokens
- Both CLIs must be installed and configured separately by the user
- If a CLI is not available, skip that reviewer and continue with the other




### Interview Protocol

# Interview Protocol

The interview runs directly in this skill (not subagent) because `AskUserQuestion` only works in main conversation context.

## Context

The interview should be informed by:
- **Initial spec** (always available)
- **Research findings** (if step 5 produced `claude-research.md`)

If research was done, use it to:
- Skip questions already answered by research
- Ask clarifying questions about trade-offs or patterns discovered
- Dig deeper into areas where research revealed complexity

## Philosophy

- You are a senior architect accountable for this implementation
- Surface everything the user knows but hasn't mentioned
- Assume the initial spec is incomplete
- Extract context from user's head

## Technique

- Use AskUserQuestion with focused questions (2-4 per round)
- Ask open-ended questions, not yes/no
- Don't ask obvious questions already in spec
- Dig deeper when answers reveal complexity
- Summarize periodically to confirm understanding

## Example Questions

**Good questions:**
- "What happens when X fails? Should we retry, log, or surface to user?"
- "Are there existing patterns in the codebase for Y that we should follow?"
- "What's the expected scale - dozens, thousands, or millions of Z?"

**Bad questions (too vague):**
- "Anything else?"
- "Is that all?"
- "Do you have any other requirements?"

## When to Stop

Stop interviewing when you are confident you can:
1. Write a detailed implementation plan
2. Make no assumptions about requirements
3. Handle all edge cases the user cares about

If uncertain, ask one more round. Better to over-clarify than make wrong assumptions.

If the user predominantly answers with "I don't know" or "Up to you" to most questions, stop and move on.

## Saving the Transcript

After the interview, save the full Q&A to `<planning_dir>/claude-interview.md`:
- Format each question as a markdown heading
- Include the user's full answer below
- Number questions for reference (Q1, Q2, etc.)




### Section Splitting

# Section File Writing

Write individual section files from the plan using **parallel subagents** for efficiency.

This step assumes `sections/index.md` already exists.

## Input Files

- `<planning_dir>/claude-plan.md` - implementation details
- `<planning_dir>/sections/index.md` - section definitions and dependencies

## Output

```
<planning_dir>/sections/
├── index.md (already exists)
├── section-01-<name>.md
├── section-02-<name>.md
└── ...
```

## Parallel Execution Strategy

**Launch one subagent per section in a single message** for maximum parallelization:

```
┌─────────────────────────────────────────────────────┐
│  PARALLEL SUBAGENT APPROACH                         │
│                                                     │
│  1. Parse index.md to get SECTION_MANIFEST list     │
│  2. Check which sections already exist              │
│  3. Launch ALL missing sections as parallel Tasks:  │
│                                                     │
│     Task(prompt="Write section-01-...")             │
│     Task(prompt="Write section-02-...")             │
│     Task(prompt="Write section-03-...")             │
│     ... (all in ONE message)                        │
│                                                     │
│  4. Wait for all subagents to complete              │
│                                                     │
└─────────────────────────────────────────────────────┘
```

### Parse SECTION_MANIFEST

Extract section list from index.md:

```markdown
<!-- SECTION_MANIFEST
section-01-foundation
section-02-config
section-03-api
END_MANIFEST -->
```

### Launch Parallel Tasks

For each section in the manifest, include a Task in a single message:

```python
Task(
  subagent_type="general-purpose",
  prompt="""
  Write section file: section-01-foundation

  Inputs:
  - <planning_dir>/claude-plan.md
  - <planning_dir>/sections/index.md

  Output: <planning_dir>/sections/section-01-foundation.md

  Requirements: [see Section File Template below]
  """
)

Task(
  subagent_type="general-purpose",
  prompt="Write section file: section-02-config ..."
)

# ... one Task per section
```

**Why parallel?** Each section is independent - they all read from the same source files (`claude-plan.md`, `index.md`) but write to different output files.

### Resume Handling

If some sections already exist:
1. Only launch Tasks for MISSING sections
2. Skip sections that have corresponding `section-*.md` files

## Section File Requirements

**CRITICAL: Each section file must be completely self-contained.**

The implementer reading a section file should NOT need to reference `claude-plan.md` or any other document. They should be able to:
1. Read the single section file
2. Create a TODO list
3. Start implementing immediately

Include all necessary background, requirements, and implementation details within each section.

### Section File Template

```markdown
# Section NN: {Section Name}

## Background

{Why this section exists, what problem it solves}

## Requirements

{What must be true when this section is complete}

## Dependencies

- Requires: {list of prior sections that must be complete}
- Blocks: {list of sections that depend on this one}

## Implementation Details

{Detailed implementation guidance}

### {Subsection 1}

{Details}

### {Subsection 2}

{Details}

## Acceptance Criteria

- [ ] {Criterion 1}
- [ ] {Criterion 2}
- [ ] {Criterion 3}

## Files to Create/Modify

- `path/to/file1.ts` - {description}
- `path/to/file2.ts` - {description}
```

## Completion

All sections are complete when every section in the SECTION_MANIFEST has a corresponding `section-NN-name.md` file.

After all parallel Tasks complete, update the main TODO list to mark section writing as done.




### Research Protocol

# Research Protocol

This document defines the research decision and execution flow.

## Overview

```
┌─────────────────────────────────────────────────────────────┐
│  RESEARCH FLOW                                              │
│                                                             │
│  Step 4: Decide what to research                            │
│    - Codebase research? (existing patterns/conventions)     │
│    - Web research? (best practices, SOTA approaches)        │
│                                                             │
│  Step 5: Execute research (parallel if both selected)       │
│    - Subagents return results                               │
│    - Main Claude combines and writes claude-research.md     │
│                                                             │
└─────────────────────────────────────────────────────────────┘
```

---

## Step 4: Research Decision

### 4.1 Read and Analyze the Spec File

Read the spec file and extract potential research topics by identifying:

- **Technologies mentioned** (React, Python, PostgreSQL, Redis, etc.)
- **Feature types** (authentication, file upload, real-time sync, caching, etc.)
- **Architecture patterns** (microservices, event-driven, serverless, etc.)
- **Integration points** (third-party APIs, OAuth providers, payment gateways, etc.)

Generate 3-5 research topic suggestions based on what you find. Format them as searchable queries with year for recency:
- "React authentication patterns 2025"
- "PostgreSQL full-text search best practices"
- "Redis session storage patterns"

If the spec is vague, fall back to generic options:
- "General best practices for {detected_language/framework}"
- "Security considerations for {feature_type}"

### 4.2 Ask About Codebase Research

Use AskUserQuestion:

```
question: "Is there existing code I should research first?"
header: "Codebase"
options:
  - label: "Yes, research the codebase"
    description: "Analyze existing patterns, conventions, dependencies"
  - label: "No existing code"
    description: "This is a new project or standalone feature"
```

### 4.3 Ask About Web Research

Present the derived topics as multi-select options:

```
question: "Should I research current best practices for any of these topics?"
header: "Web Research"
multiSelect: true
options:
  - label: "{derived_topic_1}"
    description: "Based on spec mention of {X}"
  - label: "{derived_topic_2}"
    description: "Based on spec mention of {Y}"
  - label: "{derived_topic_3}"
    description: "Based on spec mention of {Z}"
```

If user selects "Other", follow up to get custom topics.

### 4.4 Handle "No Research" Case

If user selects no codebase AND no web research, skip step 5 entirely.

---

## Step 5: Execute Research

### Critical Pattern: Subagents Return Results, Parent Writes Files

**DO NOT** have subagents write to files directly. This avoids race conditions and keeps control with the main context.

```
┌─────────────────────────────────────────────────────────────┐
│  PARALLEL RESEARCH EXECUTION                                │
│                                                             │
│  Task 1: Explore ──────────┐                                │
│    (returns codebase       │                                │
│     findings as markdown)  ├──→ Main Claude combines       │
│                            │    and writes single          │
│  Task 2: Explore ──────────┘    claude-research.md         │
│    (returns best practices                                  │
│     findings as markdown)                                   │
│                                                             │
└─────────────────────────────────────────────────────────────┘
```

### 5.1 Codebase Research (if selected)

Launch Task tool with `subagent_type=Explore`:

```
Task tool:
  subagent_type: Explore
  description: "Research codebase patterns"
  prompt: |
    Research this codebase to understand:
    - Project structure and architecture
    - Existing patterns and conventions
    - Dependencies and how they're used
    - Testing setup (framework, patterns, how tests are run)

    Focus areas from user: {user_specified_areas_if_any}

    Return your findings as markdown.
    DO NOT write to any files. Return findings in your response.
```

### 5.2 Web Research (if topics selected)

Launch Task tool with `subagent_type=Explore`:

```
Task tool:
  subagent_type: Explore
  description: "Research best practices"
  prompt: |
    Research current best practices for the following topics:
    {selected_topics_list}

    For each topic:
    1. Use WebSearch to find authoritative sources
    2. Use WebFetch on promising results to extract recommendations
    3. Cross-validate information across sources
    4. Synthesize findings with clear recommendations

    Return your findings as markdown. Always cite sources with URLs.
    DO NOT write to any files. Return findings in your response.
```

### 5.3 Parallel Execution

If both codebase and web research are needed, launch **both Task tools in a single message**.

```
# Single message with multiple tool calls:
[Task tool call 1: Explore subagent for codebase]
[Task tool call 2: Explore subagent for web research]
```

Wait for both to complete, then combine results.

### 5.4 Combine Results and Write File

After collecting results from all subagents, combine them into `<planning_dir>/claude-research.md`.

Structure the file however makes sense for the findings.

---

## Edge Cases

| Case | Handling |
|------|----------|
| Spec file is vague | Present generic options based on detected language/framework |
| User selects no research | Skip step 5, proceed to step 6 (interview) |
| One subagent fails | Log warning, write file with only successful research |
| Both subagents fail | Log error, ask user if they want to retry or proceed |
| Only one research type | Run single subagent, write file with just that content |




### Section Index

# Section Index Creation

Create `<planning_dir>/sections/index.md` to define implementation sections.

## Input Files

- `<planning_dir>/claude-plan.md` - implementation plan

## Output

```
<planning_dir>/sections/
└── index.md
```

## SECTION_MANIFEST Block

**index.md MUST start with a SECTION_MANIFEST block:**

```markdown
<!-- SECTION_MANIFEST
section-01-foundation
section-02-config
section-03-parser
section-04-api
END_MANIFEST -->

# Implementation Sections Index

... rest of human-readable content ...
```

### SECTION_MANIFEST Rules

- Must be at the TOP of index.md (before any other content)
- One section per line, format: `section-NN-name` (e.g., `section-01-foundation`)
- Section numbers must be two digits with leading zero (01, 02, ... 12)
- Section names use lowercase with hyphens (no spaces or underscores)
- Numbers should be sequential (01, 02, 03...)
- This block is parsed to track progress - the rest of index.md is for humans

## Human-Readable Content

After the manifest block, include:

### Dependency Graph

Table showing what blocks what:

```markdown
| Section | Depends On | Blocks | Parallelizable |
|---------|------------|--------|----------------|
| section-01-foundation | - | section-02, section-03 | Yes |
| section-02-config | section-01 | section-04 | No |
| section-03-parser | section-01 | section-04 | Yes |
| section-04-api | section-02, section-03 | - | No |
```

### Execution Order

Which sections can run in parallel:

```markdown
1. section-01-foundation (no dependencies)
2. section-02-config, section-03-parser (parallel after section-01)
3. section-04-api (requires section-02 AND section-03)
```

### Section Summaries

Brief description of each section:

```markdown
### section-01-foundation
Initial project setup and configuration.

### section-02-config
Configuration loading and validation.
```

## Guidelines

- **Natural boundaries**: Split by component, layer, feature, or phase
- **Focused sections**: One logical unit of work each
- **Parallelization**: Consider which sections can run independently
- **Dependency direction**: Earlier sections should not depend on later sections

## Example index.md

```markdown
<!-- SECTION_MANIFEST
section-01-foundation
section-02-core-libs
section-03-api-layer
section-04-frontend
section-05-integration
END_MANIFEST -->

# Implementation Sections Index

## Dependency Graph

| Section | Depends On | Blocks | Parallelizable |
|---------|------------|--------|----------------|
| section-01-foundation | - | all | Yes |
| section-02-core-libs | 01 | 03, 04 | No |
| section-03-api-layer | 02 | 05 | Yes |
| section-04-frontend | 02 | 05 | Yes |
| section-05-integration | 03, 04 | - | No |

## Execution Order

1. section-01-foundation (no dependencies)
2. section-02-core-libs (after 01)
3. section-03-api-layer, section-04-frontend (parallel after 02)
4. section-05-integration (final)

## Section Summaries

### section-01-foundation
Directory structure, config files, base setup.

### section-02-core-libs
Shared utilities and core libraries.

### section-03-api-layer
REST API endpoints and middleware.

### section-04-frontend
UI components and pages.

### section-05-integration
End-to-end integration and final wiring.
```




---

## 🚀 Usage

**Reference this template:** `@skill-gepetto.md`


**Platform-specific:**
- **GitHub Copilot**: Add to `.github/copilot-instructions.md`
- **Augment Code**: Use `aug context add` command
- **Cursor/Windsurf**: Reference in chat with `@filename`
- **Claude**: Add to Project Knowledge
- **ChatGPT**: Add to Custom GPT configuration
