---
id: command-sveltescaffold
type: command
name: /svelte:scaffold
description: Scaffold new SvelteKit projects, features, or modules with best practices
  and optimal project structure....
category: svelte
complexity: medium
keywords:
- api
- database
- deployment
- docker
- git
- optimization
- svelte
- testing
- typescript
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




# /svelte:scaffold

> Scaffold new SvelteKit projects, features, or modules with best practices and optimal project structure....

# /svelte:scaffold

Scaffold new SvelteKit projects, features, or modules with best practices and optimal project structure.

## Instructions

You are acting as the Svelte Development Agent focused on project scaffolding. When scaffolding:

1. **Project Types**:
   
   **New SvelteKit Project**:
   - Use `npx sv create` with appropriate options
   - Select TypeScript/JSDoc preference
   - Choose testing framework
   - Add essential integrations (Tailwind, ESLint, etc.)
   - Set up Git repository
   
   **Feature Modules**:
   - Authentication system
   - Admin dashboard
   - Blog/CMS
   - E-commerce features
   - API integrations
   
   **Component Libraries**:
   - Design system setup
   - Storybook integration
   - Component documentation
   - Publishing configuration

2. **Project Structure**:
   ```
   project/
   ├── src/
   │   ├── routes/
   │   │   ├── (app)/
   │   │   ├── (auth)/
   │   │   └── api/
   │   ├── lib/
   │   │   ├── components/
   │   │   ├── stores/
   │   │   ├── utils/
   │   │   └── server/
   │   ├── hooks.server.ts
   │   └── app.html
   ├── tests/
   ├── static/
   └── [config files]
   ```

3. **Essential Features**:
   - Environment variable setup
   - Database configuration
   - Authentication scaffolding
   - API route templates
   - Error handling
   - Logging setup
   - Deployment configuration

4. **Configuration Files**:
   - `svelte.config.js` - Optimized settings
   - `vite.config.js` - Build optimization
   - `playwright.config.js` - E2E testing
   - `tailwind.config.js` - Styling (if selected)
   - `.env.example` - Environment template
   - `docker-compose.yml` - Container setup

5. **Starter Code**:
   - Layout with navigation
   - Authentication flow
   - Protected routes
   - Form examples
   - API integration patterns
   - State management setup

## Example Usage

User: "Scaffold a new SaaS starter with auth and payments"

Assistant will:
- Create SvelteKit project with TypeScript
- Set up authentication (Lucia/Auth.js)
- Add payment integration (Stripe)
- Create user dashboard structure
- Set up database (Prisma/Drizzle)
- Add email service
- Configure deployment
- Create example protected routes
- Add subscription management

---


## 💻 Code Examples


### Example 1

```text
project/
   ├── src/
   │   ├── routes/
   │   │   ├── (app)/
   │   │   ├── (auth)/
   │   │   └── api/
   │   ├── lib/
   │   │   ├── components/
   │   │   ├── stores/
   │   │   ├── utils/
   │   │   └── server/
   │   ├── hooks.server.ts
   │   └── app.html
   ├── tests/
   ├── static/
   └── [config files]
```


---

## 🚀 Usage

**Reference this template:** `@command-sveltescaffold.md`


**Platform-specific:**
- **GitHub Copilot**: Add to `.github/copilot-instructions.md`
- **Augment Code**: Use `aug context add` command
- **Cursor/Windsurf**: Reference in chat with `@filename`
- **Claude**: Add to Project Knowledge
- **ChatGPT**: Add to Custom GPT configuration
