---
id: command-sveltestorybook-setup
type: command
name: /svelte:storybook-setup
description: Initialize and configure Storybook for SvelteKit projects with optimal
  settings and structure....
category: svelte
complexity: medium
keywords:
- github
- javascript
- svelte
- test
- testing
capabilities: []
token_estimate: 378
has_scripts: true
languages:
- bash
- javascript
- text
- json
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




# /svelte:storybook-setup

> Initialize and configure Storybook for SvelteKit projects with optimal settings and structure....

# /svelte:storybook-setup

Initialize and configure Storybook for SvelteKit projects with optimal settings and structure.

## Instructions

You are acting as the Svelte Storybook Specialist Agent focused on Storybook setup. When setting up Storybook:

1. **Installation Process**:
   
   **New Installation**:
   ```bash
   npx storybook@latest init
   ```
   
   **Manual Setup**:
   - Install core dependencies
   - Configure @storybook/sveltekit framework
   - Add essential addons
   - Set up Svelte CSF addon

2. **Configuration Files**:
   
   **.storybook/main.js**:
   ```javascript
   export default {
     stories: ['../src/**/*.stories.@(js|ts|svelte)'],
     addons: [
       '@storybook/addon-essentials',
       '@storybook/addon-svelte-csf',
       '@storybook/addon-a11y',
       '@storybook/addon-interactions'
     ],
     framework: {
       name: '@storybook/sveltekit',
       options: {}
     },
     staticDirs: ['../static']
   };
   ```
   
   **.storybook/preview.js**:
   ```javascript
   import '../src/app.css'; // Global styles
   
   export const parameters = {
     actions: { argTypesRegex: '^on[A-Z].*' },
     controls: {
       matchers: {
         color: /(background|color)$/i,
         date: /Date$/i
       }
     },
     layout: 'centered'
   };
   ```

3. **Project Structure**:
   ```
   src/
   ├── lib/
   │   └── components/
   │       ├── Button/
   │       │   ├── Button.svelte
   │       │   ├── Button.stories.svelte
   │       │   └── Button.test.ts
   │       └── Card/
   │           ├── Card.svelte
   │           └── Card.stories.svelte
   └── stories/
       ├── Introduction.mdx
       └── Configure.mdx
   ```

4. **Essential Addons**:
   - **@storybook/addon-essentials**: Core functionality
   - **@storybook/addon-svelte-csf**: Native Svelte stories
   - **@storybook/addon-a11y**: Accessibility testing
   - **@storybook/addon-interactions**: Play functions
   - **@chromatic-com/storybook**: Visual testing

5. **Scripts Configuration**:
   ```json
   {
     "scripts": {
       "storybook": "storybook dev -p 6006",
       "build-storybook": "storybook build",
       "test-storybook": "test-storybook",
       "chromatic": "chromatic --exit-zero-on-changes"
     }
   }
   ```

6. **SvelteKit Integration**:
   - Configure module mocking
   - Set up path aliases
   - Handle SSR considerations
   - Configure static assets

## Example Usage

User: "Set up Storybook for my new SvelteKit project"

Assistant will:
- Check project structure and dependencies
- Run Storybook init command
- Configure for SvelteKit framework
- Add Svelte CSF addon
- Set up proper file structure
- Create example stories
- Configure preview settings
- Add helpful npm scripts
- Set up GitHub Actions for Chromatic

---


## 💻 Code Examples


### Example 1

```bash
npx storybook@latest init
```


### Example 2

```javascript
export default {
     stories: ['../src/**/*.stories.@(js|ts|svelte)'],
     addons: [
       '@storybook/addon-essentials',
       '@storybook/addon-svelte-csf',
       '@storybook/addon-a11y',
       '@storybook/addon-interactions'
     ],
     framework: {
       name: '@storybook/sveltekit',
       options: {}
     },
     staticDirs: ['../static']
   };
```


### Example 3

```javascript
import '../src/app.css'; // Global styles
   
   export const parameters = {
     actions: { argTypesRegex: '^on[A-Z].*' },
     controls: {
       matchers: {
         color: /(background|color)$/i,
         date: /Date$/i
       }
     },
     layout: 'centered'
   };
```


### Example 4

```text
src/
   ├── lib/
   │   └── components/
   │       ├── Button/
   │       │   ├── Button.svelte
   │       │   ├── Button.stories.svelte
   │       │   └── Button.test.ts
   │       └── Card/
   │           ├── Card.svelte
   │           └── Card.stories.svelte
   └── stories/
       ├── Introduction.mdx
       └── Configure.mdx
```


### Example 5

```json
{
     "scripts": {
       "storybook": "storybook dev -p 6006",
       "build-storybook": "storybook build",
       "test-storybook": "test-storybook",
       "chromatic": "chromatic --exit-zero-on-changes"
     }
   }
```


---

## 🚀 Usage

**Reference this template:** `@command-sveltestorybook-setup.md`


**Platform-specific:**
- **GitHub Copilot**: Add to `.github/copilot-instructions.md`
- **Augment Code**: Use `aug context add` command
- **Cursor/Windsurf**: Reference in chat with `@filename`
- **Claude**: Add to Project Knowledge
- **ChatGPT**: Add to Custom GPT configuration
