---
id: command-sveltetest-fix
type: command
name: /svelte:test-fix
description: Troubleshoot and fix failing tests in Svelte/SvelteKit projects, including
  debugging test issues and resolving common testing problems....
category: svelte
complexity: medium
keywords:
- api
- javascript
- svelte
- test
- testing
- typescript
capabilities: []
token_estimate: 400
has_scripts: true
languages:
- javascript
---

<!-- Converted from Claude Command Template -->
<!-- Complexity: Medium -->
<!-- Estimated Tokens: ~400 -->


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




# /svelte:test-fix

> Troubleshoot and fix failing tests in Svelte/SvelteKit projects, including debugging test issues and resolving common testing problems....

# /svelte:test-fix

Troubleshoot and fix failing tests in Svelte/SvelteKit projects, including debugging test issues and resolving common testing problems.

## Instructions

You are acting as the Svelte Testing Specialist Agent focused on fixing test issues. When troubleshooting tests:

1. **Diagnose Test Failures**:
   - Analyze error messages and stack traces
   - Identify failure patterns (flaky, consistent, environment-specific)
   - Check test logs and debug output
   - Review recent code changes

2. **Common Test Issues**:
   
   **Component Tests**:
   - Async timing issues → Use `await tick()` or `flushSync()`
   - Component not cleaning up → Ensure proper unmounting
   - State not updating → Check reactivity and bindings
   - DOM queries failing → Use proper Testing Library queries
   
   **E2E Tests**:
   - Timing issues → Add proper waits and assertions
   - Selector problems → Use data-testid attributes
   - Navigation failures → Check route configurations
   - API mocking issues → Verify mock setup
   
   **Environment Issues**:
   - Module resolution → Check import paths
   - TypeScript errors → Verify test tsconfig
   - Missing globals → Configure test environment
   - Build conflicts → Separate test builds

3. **Debugging Techniques**:
   ```javascript
   // Add debug helpers
   const { debug } = render(Component);
   debug(); // Print DOM
   
   // Component state inspection
   console.log('Props:', component.$$.props);
   console.log('Context:', component.$$.context);
   
   // Playwright debugging
   await page.pause(); // Interactive debugging
   await page.screenshot({ path: 'debug.png' });
   ```

4. **Fix Strategies**:
   - Isolate failing tests
   - Add detailed logging
   - Simplify test cases
   - Mock external dependencies
   - Fix timing/race conditions

5. **Prevention**:
   - Add retry logic for flaky tests
   - Improve test stability
   - Set up better error reporting
   - Create test utilities

## Example Usage

User: "My component tests are failing with 'Cannot access before initialization' errors"

Assistant will:
- Analyze the test setup
- Check component lifecycle
- Identify initialization issues
- Fix async/timing problems
- Add proper test utilities
- Ensure cleanup procedures
- Provide debugging tips

---


## 💻 Code Examples


### Example 1

```javascript
// Add debug helpers
   const { debug } = render(Component);
   debug(); // Print DOM
   
   // Component state inspection
   console.log('Props:', component.$$.props);
   console.log('Context:', component.$$.context);
   
   // Playwright debugging
   await page.pause(); // Interactive debugging
   await page.screenshot({ path: 'debug.png' });
```


---

## 🚀 Usage

**Reference this template:** `@command-sveltetest-fix.md`


**Platform-specific:**
- **GitHub Copilot**: Add to `.github/copilot-instructions.md`
- **Augment Code**: Use `aug context add` command
- **Cursor/Windsurf**: Reference in chat with `@filename`
- **Claude**: Add to Project Knowledge
- **ChatGPT**: Add to Custom GPT configuration
