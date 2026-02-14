---
id: skill-templates
type: skill
name: templates
description: Project scaffolding templates for new applications. Use when creating
  new projects from scratch. Contains 12 templates for various tech stacks.
category: business-marketing
complexity: medium
keywords:
- api
- python
- react
- rest
capabilities:
- file_reading
- file_search
- text_search
token_estimate: 232
---

<!-- Converted from Claude Skill Template -->
<!-- Complexity: Medium -->
<!-- Estimated Tokens: ~232 -->


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




# templates

> Project scaffolding templates for new applications. Use when creating new projects from scratch. Contains 12 templates for various tech stacks.

# Project Templates

> Quick-start templates for scaffolding new projects.

---

## 🎯 Selective Reading Rule

**Read ONLY the template matching user's project type!**

| Template | Tech Stack | When to Use |
|----------|------------|-------------|
| [nextjs-fullstack](nextjs-fullstack/TEMPLATE.md) | Next.js + Prisma | Full-stack web app |
| [nextjs-saas](nextjs-saas/TEMPLATE.md) | Next.js + Stripe | SaaS product |
| [nextjs-static](nextjs-static/TEMPLATE.md) | Next.js + Framer | Landing page |
| [express-api](express-api/TEMPLATE.md) | Express + JWT | REST API |
| [python-fastapi](python-fastapi/TEMPLATE.md) | FastAPI | Python API |
| [react-native-app](react-native-app/TEMPLATE.md) | Expo + Zustand | Mobile app |
| [flutter-app](flutter-app/TEMPLATE.md) | Flutter + Riverpod | Cross-platform |
| [electron-desktop](electron-desktop/TEMPLATE.md) | Electron + React | Desktop app |
| [chrome-extension](chrome-extension/TEMPLATE.md) | Chrome MV3 | Browser extension |
| [cli-tool](cli-tool/TEMPLATE.md) | Node.js + Commander | CLI app |
| [monorepo-turborepo](monorepo-turborepo/TEMPLATE.md) | Turborepo + pnpm | Monorepo |
| [astro-static](astro-static/TEMPLATE.md) | Astro + MDX | Blog / Docs |

---

## Usage

1. User says "create [type] app"
2. Match to appropriate template
3. Read ONLY that template's TEMPLATE.md
4. Follow its tech stack and structure


---

## 🚀 Usage

**Reference this template:** `@skill-templates.md`


**Platform-specific:**
- **GitHub Copilot**: Add to `.github/copilot-instructions.md`
- **Augment Code**: Use `aug context add` command
- **Cursor/Windsurf**: Reference in chat with `@filename`
- **Claude**: Add to Project Knowledge
- **ChatGPT**: Add to Custom GPT configuration
