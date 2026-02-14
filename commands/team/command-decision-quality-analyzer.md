---
id: command-decision-quality-analyzer
type: command
name: Decision Quality Analyzer
description: '---

  allowed-tools: Read, Write, Edit, Bash

  argument-hint: [analysis-type] | --bias-detection | --scenario-testing | --process-optimization
  | --outcome-tracking

  description: Analyze team decision quali...'
category: team
complexity: medium
keywords:
- git
- optimization
- test
- testing
capabilities: []
token_estimate: 328
---

<!-- Converted from Claude Command Template -->
<!-- Complexity: Medium -->
<!-- Estimated Tokens: ~328 -->


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




# Decision Quality Analyzer

> ---
allowed-tools: Read, Write, Edit, Bash
argument-hint: [analysis-type] | --bias-detection | --scenario-testing | --process-optimization | --outcome-tracking
description: Analyze team decision quali...

---
allowed-capabilities: File operations, code editing, terminal access, search
argument-hint: [analysis-type] | --bias-detection | --scenario-testing | --process-optimization | --outcome-tracking
description: Analyze team decision quality with bias detection, scenario testing, and process improvement recommendations
---

# Decision Quality Analyzer

Analyze and improve team decision-making quality with comprehensive bias detection: **$ARGUMENTS**

## Current Decision Context

- Team size: !`git log --format='%ae' --since='1 month ago' | sort -u | wc -l` active contributors
- Recent decisions: Major decisions from recent commits and discussions
- Decision frequency: Pattern analysis of decision-making cadence
- Process maturity: Current decision frameworks and methodologies in use

## Task

Execute comprehensive decision quality analysis with bias mitigation and process optimization:

**Analysis Type**: Use $ARGUMENTS for bias detection, scenario testing, process optimization, or outcome tracking analysis

**Decision Quality Framework**:
1. **Process Quality Assessment** - Evaluate information gathering, stakeholder involvement, alternative generation, analysis rigor
2. **Bias Detection Analysis** - Identify confirmation bias, anchoring bias, groupthink, authority bias, planning fallacy patterns
3. **Outcome Evaluation** - Assess goal achievement, unintended consequences, stakeholder satisfaction, sustainability measures
4. **Scenario Testing** - Historical decision analysis, hypothetical scenario testing, stress test scenarios, learning extraction
5. **Timing Analysis** - Decision speed evaluation, information timing optimization, implementation coordination, review scheduling
6. **Learning Integration** - Knowledge capture, institutional learning, process improvement, capability building

**Advanced Features**: Multi-dimensional quality metrics, systematic bias mitigation strategies, decision simulation testing, predictive outcome modeling.

**Process Optimization**: Stakeholder engagement frameworks, analytical tool integration, communication enhancement, cultural development strategies.

**Output**: Comprehensive decision quality assessment with specific bias mitigation strategies, process improvements, and implementation roadmap.

---

## 🚀 Usage

**Reference this template:** `@command-decision-quality-analyzer.md`


**Platform-specific:**
- **GitHub Copilot**: Add to `.github/copilot-instructions.md`
- **Augment Code**: Use `aug context add` command
- **Cursor/Windsurf**: Reference in chat with `@filename`
- **Claude**: Add to Project Knowledge
- **ChatGPT**: Add to Custom GPT configuration
