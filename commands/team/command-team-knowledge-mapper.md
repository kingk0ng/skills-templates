---
id: command-team-knowledge-mapper
type: command
name: Team Knowledge Mapper
description: '---

  allowed-tools: Read, Bash, Glob, Grep

  argument-hint: [mapping-type] | --skill-matrix | --knowledge-gaps | --expertise-areas
  | --learning-paths

  description: Map team knowledge and expertise with sk...'
category: team
complexity: medium
keywords:
- git
- optimization
capabilities: []
token_estimate: 378
---

<!-- Converted from Claude Command Template -->
<!-- Complexity: Medium -->
<!-- Estimated Tokens: ~378 -->


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




# Team Knowledge Mapper

> ---
allowed-tools: Read, Bash, Glob, Grep
argument-hint: [mapping-type] | --skill-matrix | --knowledge-gaps | --expertise-areas | --learning-paths
description: Map team knowledge and expertise with sk...

---
allowed-capabilities: File operations, code editing, terminal access, search
argument-hint: [mapping-type] | --skill-matrix | --knowledge-gaps | --expertise-areas | --learning-paths
description: Map team knowledge and expertise with skill gap analysis and learning path recommendations
---

# Team Knowledge Mapper

Map team knowledge and expertise with comprehensive skill gap analysis: **$ARGUMENTS**

## Current Knowledge Context

- Team expertise: !`git log --format='%ae' --since='3 months ago' | sort | uniq -c | sort -nr` contributor activity patterns
- Technology stack: Analysis of languages, frameworks, and tools used in codebase
- Knowledge distribution: Assessment of expertise concentration and bus factor risks
- Learning activity: Recent skill development and cross-training initiatives

## Task

Execute comprehensive knowledge mapping with skill gap analysis and learning optimization:

**Mapping Type**: Use $ARGUMENTS to focus on skill matrix creation, knowledge gap identification, expertise area analysis, or learning path recommendations

**Knowledge Mapping Framework**:
1. **Skill Matrix Creation** - Map individual expertise levels, identify core competencies, assess technology proficiencies, evaluate domain knowledge
2. **Knowledge Gap Analysis** - Identify critical skill gaps, assess team vulnerabilities, evaluate learning priorities, recommend skill development
3. **Expertise Distribution** - Analyze knowledge concentration, identify single points of failure, assess bus factor risks, recommend knowledge sharing
4. **Learning Path Planning** - Design skill development roadmaps, recommend training priorities, plan mentorship programs, optimize knowledge transfer
5. **Cross-Training Optimization** - Identify pairing opportunities, plan knowledge rotation, design shadowing programs, optimize skill redundancy
6. **Knowledge Retention** - Assess knowledge preservation, plan documentation strategies, design knowledge capture systems, prevent expertise loss

**Advanced Features**: Dynamic skill tracking, expertise prediction modeling, learning ROI analysis, knowledge graph visualization, competency gap forecasting.

**Strategic Planning**: Succession planning support, hiring decision guidance, team composition optimization, skill portfolio balancing.

**Output**: Comprehensive knowledge map with skill matrices, gap analysis, learning recommendations, and strategic knowledge management plans.

---

## 🚀 Usage

**Reference this template:** `@command-team-knowledge-mapper.md`


**Platform-specific:**
- **GitHub Copilot**: Add to `.github/copilot-instructions.md`
- **Augment Code**: Use `aug context add` command
- **Cursor/Windsurf**: Reference in chat with `@filename`
- **Claude**: Add to Project Knowledge
- **ChatGPT**: Add to Custom GPT configuration
