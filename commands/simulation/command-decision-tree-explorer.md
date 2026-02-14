---
id: command-decision-tree-explorer
type: command
name: Decision Tree Explorer
description: '---

  allowed-tools: Read, Write, Edit, WebSearch

  argument-hint: [decision-context] | --strategic | --investment | --operational |
  --crisis-response

  description: Explore complex decision branches with p...'
category: simulation
complexity: medium
keywords:
- optimization
- testing
capabilities: []
token_estimate: 293
---

<!-- Converted from Claude Command Template -->
<!-- Complexity: Medium -->
<!-- Estimated Tokens: ~293 -->


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




# Decision Tree Explorer

> ---
allowed-tools: Read, Write, Edit, WebSearch
argument-hint: [decision-context] | --strategic | --investment | --operational | --crisis-response
description: Explore complex decision branches with p...

---
allowed-capabilities: File operations, code editing, terminal access, searchWebSearch
argument-hint: [decision-context] | --strategic | --investment | --operational | --crisis-response
description: Explore complex decision branches with probability analysis, expected value calculation, and optimization
---

# Decision Tree Explorer

Explore complex decision scenarios with comprehensive probability analysis and optimization: **$ARGUMENTS**

## Current Decision Context

- Decision scope: Based on $ARGUMENTS (strategic, investment, operational, crisis response)
- Available options: Current alternatives under consideration
- Success criteria: Key metrics for decision evaluation
- Resource constraints: Limitations affecting available choices

## Task

Create comprehensive decision tree analysis for optimal choice selection:

**Decision Context**: Use $ARGUMENTS to analyze strategic decisions, investments, operations, or crisis responses

**Decision Framework**:
1. **Option Generation** - Comprehensive alternative identification including hybrid and innovative approaches
2. **Probability Assessment** - Systematic likelihood estimation using base rates, expert judgment, and market data
3. **Expected Value Analysis** - Multi-dimensional value calculation including financial, strategic, and risk factors
4. **Sensitivity Analysis** - Critical assumption testing and break-even analysis
5. **Risk Assessment** - Comprehensive risk identification, impact analysis, and mitigation strategies
6. **Optimization Engine** - Multi-criteria decision analysis with stakeholder preference integration

**Advanced Analytics**: Monte Carlo simulations, real options valuation, decision path optimization, and robustness testing.

**Implementation Integration**: Connect analysis to specific actions, success metrics, and contingency planning.

**Output**: Complete decision tree with probability-weighted outcomes, expected value calculations, risk assessments, and strategic recommendations with implementation guidance.

---

## 🚀 Usage

**Reference this template:** `@command-decision-tree-explorer.md`


**Platform-specific:**
- **GitHub Copilot**: Add to `.github/copilot-instructions.md`
- **Augment Code**: Use `aug context add` command
- **Cursor/Windsurf**: Reference in chat with `@filename`
- **Claude**: Add to Project Knowledge
- **ChatGPT**: Add to Custom GPT configuration
