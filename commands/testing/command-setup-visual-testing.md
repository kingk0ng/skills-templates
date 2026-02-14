---
id: command-setup-visual-testing
type: command
name: Setup Visual Testing
description: '---

  allowed-tools: Read, Write, Edit, Bash

  argument-hint: [testing-scope] | --components | --pages | --responsive | --cross-browser
  | --accessibility

  description: Setup comprehensive visual regression...'
category: testing
complexity: medium
keywords:
- angular
- ci/cd
- github
- gitlab
- optimization
- performance
- react
- test
- testing
- vue
capabilities: []
token_estimate: 386
---

<!-- Converted from Claude Command Template -->
<!-- Complexity: Medium -->
<!-- Estimated Tokens: ~386 -->


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




# Setup Visual Testing

> ---
allowed-tools: Read, Write, Edit, Bash
argument-hint: [testing-scope] | --components | --pages | --responsive | --cross-browser | --accessibility
description: Setup comprehensive visual regression...

---
allowed-capabilities: File operations, code editing, terminal access, search
argument-hint: [testing-scope] | --components | --pages | --responsive | --cross-browser | --accessibility
description: Setup comprehensive visual regression testing with cross-browser and responsive testing
---

# Setup Visual Testing

Setup comprehensive visual regression testing with responsive and accessibility validation: **$ARGUMENTS**

## Current Visual Testing Context

- Frontend framework: !`grep -l "react\\|vue\\|angular" package.json 2>/dev/null || echo "Detect framework"`
- UI components: !`find . -name "components" -o -name "src" | head -1 && echo "Component structure detected" || echo "Analyze structure"`
- Existing testing: !`find . -name "cypress" -o -name "playwright" -o -name "storybook" | head -1 || echo "No visual testing"`
- CI system: !`find . -name ".github" -o -name ".gitlab-ci.yml" | head -1 || echo "No CI detected"`

## Task

Implement comprehensive visual testing with regression detection and accessibility validation:

**Testing Scope**: Use $ARGUMENTS to focus on component testing, page testing, responsive testing, cross-browser testing, or accessibility testing

**Visual Testing Framework**:

1. **Tool Selection & Setup** - Choose visual testing tools (Percy, Chromatic, BackstopJS, Playwright), configure integration, setup environments
2. **Baseline Creation** - Capture visual baselines, organize screenshot structure, implement version control, optimize image management
3. **Test Scenario Design** - Create component tests, design page workflows, implement responsive breakpoints, configure browser matrix
4. **Integration Setup** - Configure CI/CD integration, setup automated execution, implement review workflows, optimize performance
5. **Regression Detection** - Configure diff algorithms, setup threshold management, implement approval workflows, optimize accuracy
6. **Advanced Testing** - Setup accessibility testing, configure cross-browser validation, implement responsive testing, design performance monitoring

**Advanced Features**: Automated visual testing, intelligent diff analysis, accessibility compliance checking, responsive design validation, performance visual metrics.

**Quality Assurance**: Test reliability, false positive reduction, maintainability optimization, execution performance.

**Output**: Complete visual testing setup with baseline management, regression detection, CI integration, and comprehensive validation workflows.


---

## 🚀 Usage

**Reference this template:** `@command-setup-visual-testing.md`


**Platform-specific:**
- **GitHub Copilot**: Add to `.github/copilot-instructions.md`
- **Augment Code**: Use `aug context add` command
- **Cursor/Windsurf**: Reference in chat with `@filename`
- **Claude**: Add to Project Knowledge
- **ChatGPT**: Add to Custom GPT configuration
