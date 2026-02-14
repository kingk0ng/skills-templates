---
id: command-vercel-analytics-setup
type: command
name: Vercel Analytics Setup
description: '---

  allowed-tools: Read, Write, Edit, Bash

  argument-hint:

  description: Set up Vercel Analytics and Speed Insights for React/Vite projects

  ---...'
category: setup
complexity: medium
keywords:
- deploy
- deployment
- react
capabilities: []
token_estimate: 334
has_scripts: true
languages:
- bash
- json
---

<!-- Converted from Claude Command Template -->
<!-- Complexity: Medium -->
<!-- Estimated Tokens: ~334 -->


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




# Vercel Analytics Setup

> ---
allowed-tools: Read, Write, Edit, Bash
argument-hint:
description: Set up Vercel Analytics and Speed Insights for React/Vite projects
---...

---
allowed-capabilities: File operations, code editing, terminal access, search
argument-hint:
description: Set up Vercel Analytics and Speed Insights for React/Vite projects
---

# Vercel Analytics Setup

Automatically configure Vercel Analytics and Speed Insights for your React/Vite project.

**Usage:** `/vercel-analytics` (no arguments needed)

**What it does:**
- Installs @vercel/analytics and @vercel/speed-insights packages
- Adds components to your React app
- Configures SPA routing for Vercel deployment
- Fixes 404 errors for direct route access

**Process:**

1. **Install Vercel Packages**
   ```bash
   npm install @vercel/analytics @vercel/speed-insights
   ```

2. **Detect Main App File**
   - Search for main React entry point:
     - `src/App.tsx` or `src/App.jsx`
     - `src/main.tsx` or `src/main.jsx`
   - Read file to determine current structure

3. **Add Analytics Components**
   - Import Analytics from '@vercel/analytics/react'
   - Import SpeedInsights from '@vercel/speed-insights/react'
   - Add both components to the main App component
   - Use `/react` imports (not `/next`)

4. **Create vercel.json Configuration**
   - Create `vercel.json` in project root
   - Add SPA rewrite rules:
   ```json
   {
     "rewrites": [
       { "source": "/(.*)", "destination": "/index.html" }
     ]
   }
   ```
   - This ensures all routes serve index.html (fixes 404s)

5. **Verify Setup**
   - Confirm components are properly imported
   - Check vercel.json exists and is valid
   - Display success message with next steps

**Expected Outcome:**
- ✅ Analytics tracking active
- ✅ Speed Insights monitoring configured
- ✅ SPA routing works correctly on Vercel
- ✅ No 404 errors on direct route access

**Next Steps:**
1. Deploy to Vercel: `vercel deploy`
2. View analytics at: https://vercel.com/dashboard/analytics
3. Check Speed Insights: https://vercel.com/dashboard/speed-insights

**Note**: Works with React, Vite, Create React App, and other SPA frameworks.


---


## 💻 Code Examples


### Example 1

```bash
npm install @vercel/analytics @vercel/speed-insights
```


### Example 2

```json
{
     "rewrites": [
       { "source": "/(.*)", "destination": "/index.html" }
     ]
   }
```


---

## 🚀 Usage

**Reference this template:** `@command-vercel-analytics-setup.md`


**Platform-specific:**
- **GitHub Copilot**: Add to `.github/copilot-instructions.md`
- **Augment Code**: Use `aug context add` command
- **Cursor/Windsurf**: Reference in chat with `@filename`
- **Claude**: Add to Project Knowledge
- **ChatGPT**: Add to Custom GPT configuration
