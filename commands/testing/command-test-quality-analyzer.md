---
id: command-test-quality-analyzer
type: command
name: Test Quality Analyzer
description: '---

  allowed-tools: Read, Write, Edit, Bash

  argument-hint: [analysis-type] | --coverage-quality | --test-effectiveness | --maintainability
  | --performance-analysis

  description: Analyze test suite quali...'
category: testing
complexity: medium
keywords:
- benchmark
- optimization
- performance
- test
- testing
capabilities: []
token_estimate: 367
---

<!-- Converted from Claude Command Template -->
<!-- Complexity: Medium -->
<!-- Estimated Tokens: ~367 -->


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




# Test Quality Analyzer

> ---
allowed-tools: Read, Write, Edit, Bash
argument-hint: [analysis-type] | --coverage-quality | --test-effectiveness | --maintainability | --performance-analysis
description: Analyze test suite quali...

---
allowed-capabilities: File operations, code editing, terminal access, search
argument-hint: [analysis-type] | --coverage-quality | --test-effectiveness | --maintainability | --performance-analysis
description: Analyze test suite quality with comprehensive metrics and improvement recommendations
---

# Test Quality Analyzer

Analyze test suite quality with comprehensive metrics and actionable improvement insights: **$ARGUMENTS**

## Current Quality Context

- Test coverage: !`find . -name "coverage" -type d | head -1 && echo "Coverage data available" || echo "No coverage data"`
- Test files: !`find . -name "*.test.*" -o -name "*.spec.*" | wc -l` test files
- Test complexity: Analysis of test suite maintainability and effectiveness patterns
- Performance metrics: Current test execution times and resource utilization

## Task

Execute comprehensive test quality analysis with improvement recommendations and optimization strategies:

**Analysis Type**: Use $ARGUMENTS to focus on coverage quality, test effectiveness, maintainability analysis, or performance analysis

**Test Quality Analysis Framework**:

1. **Coverage Quality Assessment** - Analyze coverage depth, evaluate coverage quality, assess edge case handling, identify coverage gaps
2. **Test Effectiveness Evaluation** - Measure defect detection capability, analyze test reliability, assess assertion quality, evaluate test value
3. **Maintainability Analysis** - Evaluate test code quality, analyze test organization, assess refactoring needs, optimize test structure
4. **Performance Assessment** - Analyze execution performance, identify bottlenecks, optimize test speed, reduce resource consumption
5. **Anti-Pattern Detection** - Identify testing anti-patterns, detect flaky tests, analyze test smells, recommend corrections
6. **Quality Metrics Tracking** - Implement quality scoring, track improvement trends, configure quality gates, optimize quality processes

**Advanced Features**: AI-powered quality assessment, predictive quality modeling, automated improvement suggestions, quality trend analysis, benchmark comparison.

**Quality Insights**: Test ROI analysis, quality correlation analysis, maintenance cost assessment, effectiveness benchmarking.

**Output**: Comprehensive quality analysis with detailed metrics, improvement recommendations, optimization strategies, and quality tracking framework.


---

## 🚀 Usage

**Reference this template:** `@command-test-quality-analyzer.md`


**Platform-specific:**
- **GitHub Copilot**: Add to `.github/copilot-instructions.md`
- **Augment Code**: Use `aug context add` command
- **Cursor/Windsurf**: Reference in chat with `@filename`
- **Claude**: Add to Project Knowledge
- **ChatGPT**: Add to Custom GPT configuration
