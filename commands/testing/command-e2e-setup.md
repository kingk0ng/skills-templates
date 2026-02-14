---
id: command-e2e-setup
type: command
name: E2E Setup
description: '---

  allowed-tools: Read, Write, Edit, Bash

  argument-hint: [framework] | --cypress | --playwright | --webdriver | --puppeteer
  | --mobile

  description: Configure comprehensive end-to-end testing suite wi...'
category: testing
complexity: medium
keywords:
- angular
- api
- ci/cd
- database
- github
- gitlab
- optimization
- performance
- react
- test
- testing
- vue
capabilities: []
token_estimate: 380
---

<!-- Converted from Claude Command Template -->
<!-- Complexity: Medium -->
<!-- Estimated Tokens: ~380 -->


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




# E2E Setup

> ---
allowed-tools: Read, Write, Edit, Bash
argument-hint: [framework] | --cypress | --playwright | --webdriver | --puppeteer | --mobile
description: Configure comprehensive end-to-end testing suite wi...

---
allowed-capabilities: File operations, code editing, terminal access, search
argument-hint: [framework] | --cypress | --playwright | --webdriver | --puppeteer | --mobile
description: Configure comprehensive end-to-end testing suite with framework selection and CI integration
---

# E2E Setup

Configure comprehensive end-to-end testing suite with framework optimization: **$ARGUMENTS**

## Current E2E Context

- Application type: !`find . -name "index.html" -o -name "app.js" -o -name "App.tsx" | head -1 && echo "Web app" || echo "Detect app type"`
- Framework: !`grep -l "react\\|vue\\|angular" package.json 2>/dev/null || echo "Detect framework"`
- Existing tests: !`find . -name "cypress" -o -name "playwright" -o -name "e2e" | head -1 || echo "No E2E setup"`
- CI system: !`find . -name ".github" -o -name ".gitlab-ci.yml" | head -1 || echo "No CI detected"`

## Task

Implement comprehensive end-to-end testing with framework selection and optimization:

**Framework Focus**: Use $ARGUMENTS to specify Cypress, Playwright, WebDriver, Puppeteer, mobile testing, or auto-detect best fit

**E2E Testing Framework**:

1. **Framework Selection & Setup** - Choose optimal E2E tool, install dependencies, configure basic settings, setup project structure
2. **Test Environment Configuration** - Setup test environments, configure base URLs, implement environment switching, optimize test isolation
3. **Page Object Patterns** - Design page object model, create reusable components, implement element selectors, optimize maintainability
4. **Test Data Management** - Setup test data strategies, implement fixtures, configure database seeding, design cleanup procedures
5. **Cross-Browser Testing** - Configure multi-browser execution, setup mobile testing, implement responsive testing, optimize compatibility
6. **CI/CD Integration** - Configure automated execution, setup parallel testing, implement reporting, optimize performance

**Advanced Features**: Visual regression testing, accessibility testing, performance monitoring, API testing integration, mobile device testing.

**Quality Assurance**: Test reliability optimization, flaky test prevention, execution speed optimization, debugging capabilities.

**Output**: Complete E2E testing setup with framework configuration, test suites, CI integration, and maintenance workflows.


---

## 🚀 Usage

**Reference this template:** `@command-e2e-setup.md`


**Platform-specific:**
- **GitHub Copilot**: Add to `.github/copilot-instructions.md`
- **Augment Code**: Use `aug context add` command
- **Cursor/Windsurf**: Reference in chat with `@filename`
- **Claude**: Add to Project Knowledge
- **ChatGPT**: Add to Custom GPT configuration
