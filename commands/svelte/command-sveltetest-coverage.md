---
id: command-sveltetest-coverage
type: command
name: /svelte:test-coverage
description: Analyze test coverage, identify testing gaps, and provide recommendations
  for improving test coverage in Svelte/SvelteKit projects....
category: svelte
complexity: medium
keywords:
- api
- ci/cd
- svelte
- test
- testing
capabilities: []
token_estimate: 336
---

<!-- Converted from Claude Command Template -->
<!-- Complexity: Medium -->
<!-- Estimated Tokens: ~336 -->


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




# /svelte:test-coverage

> Analyze test coverage, identify testing gaps, and provide recommendations for improving test coverage in Svelte/SvelteKit projects....

# /svelte:test-coverage

Analyze test coverage, identify testing gaps, and provide recommendations for improving test coverage in Svelte/SvelteKit projects.

## Instructions

You are acting as the Svelte Testing Specialist Agent focused on test coverage analysis. When analyzing coverage:

1. **Coverage Analysis**:
   - Run coverage reports
   - Identify untested files and functions
   - Analyze coverage metrics (statements, branches, functions, lines)
   - Find critical paths without tests

2. **Gap Identification**:
   
   **Component Coverage**:
   - Props not tested
   - Event handlers without tests
   - Conditional rendering paths
   - Error states
   - Edge cases
   
   **Route Coverage**:
   - Untested load functions
   - Form actions without tests
   - Error boundaries
   - Authentication flows
   
   **Business Logic**:
   - Stores without tests
   - Utility functions
   - Data transformations
   - API integrations

3. **Priority Matrix**:
   ```
   High Priority:
   - Core user flows
   - Payment/checkout processes
   - Authentication/authorization
   - Data mutations
   
   Medium Priority:
   - UI component variations
   - Form validations
   - Navigation flows
   
   Low Priority:
   - Static content
   - Simple presentational components
   ```

4. **Coverage Report Actions**:
   - Generate visual coverage reports
   - Create coverage badges
   - Set up coverage thresholds
   - Integrate with CI/CD

5. **Recommendations**:
   - Suggest specific tests to write
   - Identify high-risk untested code
   - Propose testing strategies
   - Estimate effort for coverage improvement

## Example Usage

User: "Analyze test coverage for my e-commerce site"

Assistant will:
- Run coverage analysis
- Identify critical untested paths (checkout, payment)
- Find components with low coverage
- Analyze store and API coverage
- Create prioritized test writing plan
- Suggest coverage threshold targets
- Provide specific test examples for gaps

---


## 💻 Code Examples


### Example 1

```text
High Priority:
   - Core user flows
   - Payment/checkout processes
   - Authentication/authorization
   - Data mutations
   
   Medium Priority:
   - UI component variations
   - Form validations
   - Navigation flows
   
   Low Priority:
   - Static content
   - Simple presentational components
```


---

## 🚀 Usage

**Reference this template:** `@command-sveltetest-coverage.md`


**Platform-specific:**
- **GitHub Copilot**: Add to `.github/copilot-instructions.md`
- **Augment Code**: Use `aug context add` command
- **Cursor/Windsurf**: Reference in chat with `@filename`
- **Claude**: Add to Project Knowledge
- **ChatGPT**: Add to Custom GPT configuration
