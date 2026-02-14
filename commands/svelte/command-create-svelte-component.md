---
id: command-create-svelte-component
type: command
name: Create Svelte Component
description: '---

  allowed-tools: Read, Write, Edit

  argument-hint: [component-name] [--typescript] [--story]

  description: Create new Svelte components with best practices, TypeScript support,
  and testing

  ---...'
category: svelte
complexity: medium
keywords:
- svelte
- test
- testing
- typescript
capabilities: []
token_estimate: 370
---

<!-- Converted from Claude Command Template -->
<!-- Complexity: Medium -->
<!-- Estimated Tokens: ~370 -->


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




# Create Svelte Component

> ---
allowed-tools: Read, Write, Edit
argument-hint: [component-name] [--typescript] [--story]
description: Create new Svelte components with best practices, TypeScript support, and testing
---...

---
allowed-capabilities: File operations, code editing, terminal access, search
argument-hint: [component-name] [--typescript] [--story]
description: Create new Svelte components with best practices, TypeScript support, and testing
---

# Create Svelte Component

Create new Svelte component: $ARGUMENTS

## Current Svelte Project

- Svelte config: @svelte.config.js or @vite.config.js (if exists)
- Components directory: @src/components/ or @src/lib/ (if exists)
- TypeScript config: @tsconfig.json (detect TypeScript usage)
- Testing setup: @vitest.config.js or @jest.config.js (if exists)

## Task

Create Svelte component with best practices. When creating components:

1. **Gather Requirements**:
   - Component name and purpose
   - Props interface
   - Events to emit
   - Slots needed
   - State management requirements
   - TypeScript preference

2. **Component Structure**:
   ```svelte
   <script lang="ts">
     // Imports
     // Type definitions
     // Props
     // State
     // Derived values
     // Effects
     // Functions
   </script>
   
   <!-- Markup -->
   
   <style>
     /* Scoped styles */
   </style>
   ```

3. **Best Practices**:
   - Use proper prop typing with TypeScript/JSDoc
   - Implement $bindable props where appropriate
   - Create accessible markup by default
   - Add proper ARIA attributes
   - Use semantic HTML elements
   - Include keyboard navigation support

4. **Component Types to Create**:
   - **UI Components**: Buttons, Cards, Modals, etc.
   - **Form Components**: Inputs with validation, custom form controls
   - **Layout Components**: Headers, Sidebars, Grids
   - **Data Components**: Tables, Lists, Data visualizations
   - **Utility Components**: Portals, Transitions, Error boundaries

5. **Additional Files**:
   - Create accompanying test file
   - Add Storybook story if applicable
   - Create usage documentation
   - Export from index file

## Example Usage

User: "Create a Modal component with customizable header, footer slots, and close functionality"

Assistant will:
- Create Modal.svelte with proper structure
- Implement focus trap and keyboard handling
- Add transition effects
- Create Modal.test.js with basic tests
- Provide usage examples
- Suggest accessibility improvements

---


## 💻 Code Examples


### Example 1

```svelte
<script lang="ts">
     // Imports
     // Type definitions
     // Props
     // State
     // Derived values
     // Effects
     // Functions
   </script>
   
   <!-- Markup -->
   
   <style>
     /* Scoped styles */
   </style>
```


---

## 🚀 Usage

**Reference this template:** `@command-create-svelte-component.md`


**Platform-specific:**
- **GitHub Copilot**: Add to `.github/copilot-instructions.md`
- **Augment Code**: Use `aug context add` command
- **Cursor/Windsurf**: Reference in chat with `@filename`
- **Claude**: Add to Project Knowledge
- **ChatGPT**: Add to Custom GPT configuration
