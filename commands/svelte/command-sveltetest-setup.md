---
id: command-sveltetest-setup
type: command
name: /svelte:test-setup
description: Set up comprehensive testing infrastructure for Svelte/SvelteKit projects,
  including unit testing, component testing, and E2E testing frameworks....
category: svelte
complexity: medium
keywords:
- api
- ci/cd
- javascript
- svelte
- test
- testing
capabilities: []
token_estimate: 365
has_scripts: true
languages:
- javascript
- text
---

<!-- Converted from Claude Command Template -->
<!-- Complexity: Medium -->
<!-- Estimated Tokens: ~365 -->


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




# /svelte:test-setup

> Set up comprehensive testing infrastructure for Svelte/SvelteKit projects, including unit testing, component testing, and E2E testing frameworks....

# /svelte:test-setup

Set up comprehensive testing infrastructure for Svelte/SvelteKit projects, including unit testing, component testing, and E2E testing frameworks.

## Instructions

You are acting as the Svelte Testing Specialist Agent focused on testing infrastructure. When setting up testing:

1. **Assess Current State**:
   - Check existing test setup
   - Identify missing testing tools
   - Review package.json for test scripts
   - Analyze project structure

2. **Testing Stack Setup**:
   
   **Unit/Component Testing (Vitest)**:
   - Install dependencies: `vitest`, `@testing-library/svelte`, `jsdom`
   - Configure vitest.config.js
   - Set up test helpers and utilities
   - Create setup files
   
   **E2E Testing (Playwright)**:
   - Install Playwright
   - Configure playwright.config.js
   - Set up test fixtures
   - Create page object models
   
   **Additional Tools**:
   - Coverage reporting (c8/istanbul)
   - Test utilities (@testing-library/user-event)
   - Mock service worker for API mocking
   - Visual regression testing tools

3. **Configuration Files**:
   ```javascript
   // vitest.config.js
   import { sveltekit } from '@sveltejs/kit/vite';
   import { defineConfig } from 'vitest/config';
   
   export default defineConfig({
     plugins: [sveltekit()],
     test: {
       environment: 'jsdom',
       setupFiles: ['./src/tests/setup.ts'],
       coverage: {
         reporter: ['text', 'html', 'lcov']
       }
     }
   });
   ```

4. **Test Structure**:
   ```
   src/
   ├── tests/
   │   ├── setup.ts
   │   ├── helpers/
   │   └── fixtures/
   ├── routes/
   │   └── +page.test.ts
   └── lib/
       └── Component.test.ts
   ```

5. **NPM Scripts**:
   - `test`: Run all tests
   - `test:unit`: Run unit tests
   - `test:e2e`: Run E2E tests
   - `test:coverage`: Generate coverage report
   - `test:watch`: Run tests in watch mode

## Example Usage

User: "Set up testing for my new SvelteKit project"

Assistant will:
- Analyze current project setup
- Install and configure Vitest
- Install and configure Playwright
- Create test configuration files
- Set up test utilities and helpers
- Add comprehensive npm scripts
- Create example tests
- Set up CI/CD test workflows

---


## 💻 Code Examples


### Example 1

```javascript
// vitest.config.js
   import { sveltekit } from '@sveltejs/kit/vite';
   import { defineConfig } from 'vitest/config';
   
   export default defineConfig({
     plugins: [sveltekit()],
     test: {
       environment: 'jsdom',
       setupFiles: ['./src/tests/setup.ts'],
       coverage: {
         reporter: ['text', 'html', 'lcov']
       }
     }
   });
```


### Example 2

```text
src/
   ├── tests/
   │   ├── setup.ts
   │   ├── helpers/
   │   └── fixtures/
   ├── routes/
   │   └── +page.test.ts
   └── lib/
       └── Component.test.ts
```


---

## 🚀 Usage

**Reference this template:** `@command-sveltetest-setup.md`


**Platform-specific:**
- **GitHub Copilot**: Add to `.github/copilot-instructions.md`
- **Augment Code**: Use `aug context add` command
- **Cursor/Windsurf**: Reference in chat with `@filename`
- **Claude**: Add to Project Knowledge
- **ChatGPT**: Add to Custom GPT configuration
