---
id: skill-design-mirror
type: skill
name: design-mirror
description: Replicate the visual style of any website and apply it to your existing
  codebase. Use this skill whenever the user wants to match a site's design, mirror
  a UI aesthetic, make their app look like another site, or replicate a specific visual
  style from a URL. Trigger on phrases like 'make it look like', 'match the design
  of', 'copy the style from', 'I want my app to look like X', 'mirror this design',
  'inspired by [url]', or any time the user points at a website and says they want
  their frontend to match it.
category: web-data
complexity: medium
keywords:
- api
- react
- vue
capabilities: []
token_estimate: 1394
has_references: true
reference_count: 3
---

<!-- Converted from Claude Skill Template -->
<!-- Complexity: Medium -->
<!-- Estimated Tokens: ~1,394 -->
<!-- Includes Reference Materials -->

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




# design-mirror

> Replicate the visual style of any website and apply it to your existing codebase. Use this skill whenever the user wants to match a site's design, mirror a UI aesthetic, make their app look like another site, or replicate a specific visual style from a URL. Trigger on phrases like 'make it look like', 'match the design of', 'copy the style from', 'I want my app to look like X', 'mirror this design', 'inspired by [url]', or any time the user points at a website and says they want their frontend to match it.

# Design Mirror

Capture the visual design language of any website and apply it to your existing codebase — colors, typography, spacing, layout rhythm, component shapes, and overall aesthetic — all extracted live via Bright Data's Web Unlocker.

## What This Skill Does

1. **Capture** — Screenshot + HTML scrape the inspiration site via Bright Data
2. **Extract** — Identify the full design system: colors, fonts, spacing scale, border radii, shadows, component patterns
3. **Analyze** — Study the screenshot visually and the CSS structurally to understand the design language
4. **Apply** — Translate that design system into the user's existing codebase (their framework, their components)

You are not copying content or functionality. You're understanding the *design language* — the palette, the type scale, the card shapes, the hover states, the overall aesthetic feel.

> **Important:** This skill is for design inspiration and learning — extracting publicly visible design tokens (colors, fonts, spacing) to inform your own UI work. Always use it respectfully and in accordance with the terms of service of the sites you reference.

## Setup

Requires:
- `BRIGHTDATA_API_KEY` — from [brightdata.com/cp](https://brightdata.com/cp) → Account Settings
- `BRIGHTDATA_UNLOCKER_ZONE` — create an Unlocker zone at brightdata.com/cp

```bash
export BRIGHTDATA_API_KEY="your-api-key"
export BRIGHTDATA_UNLOCKER_ZONE="your-zone-name"
```

## Step-by-Step Process

### Step 1: Capture the Inspiration Site

Run both captures in parallel — screenshot (for visual analysis) and HTML scrape (for CSS extraction):

```bash
# Screenshot (save as PNG)
bash scripts/screenshot.sh "https://inspiration-site.com" "/tmp/target_screenshot.png"

# HTML + CSS scrape
bash scripts/scrape_html.sh "https://inspiration-site.com" "/tmp/target_page.html"
```

Read `references/capture-guide.md` for how to extract CSS from the raw HTML and handle common issues.

### Step 2: Analyze the Design System

After capturing, analyze both in parallel:

**Visual analysis (screenshot):** Read the PNG image and identify:
- Primary, secondary, accent colors
- Background colors (page bg, card bg, surface hierarchy)
- Typography: font families visible, size hierarchy (h1 → body → caption)
- Layout: is it centered/constrained-width? Grid? Sidebar?
- Card/container shapes: border radius size, shadow style (hard, soft, none, colored)
- Button styles: pill, rectangle, ghost, gradient?
- Navigation: sticky? Glass/blur effect? Dark or light?
- Overall mood: dark, light, minimal, brutalist, glassmorphism, corporate, startup?

**CSS analysis (HTML):** Extract from `<style>` tags and inline styles:
- CSS custom properties (`:root { --color-... }`) — publicly declared design tokens
- Font imports (`@import` from Google Fonts, etc.)
- Tailwind config if present
- Repeated class patterns that reveal the spacing scale

Read `references/css-extraction.md` for the extraction playbook.

### Step 3: Build the Design Token Map

Produce a structured design token map before touching any code:

```
DESIGN TOKENS FROM [site]
==========================
Colors:
  --bg-primary: #0a0a0f      (page background)
  --bg-surface: #13131a      (card/panel background)
  --text-primary: #ffffff
  --text-muted: #8888aa
  --accent: #7c3aed          (primary CTA color)
  --accent-hover: #6d28d9
  --border: rgba(255,255,255,0.08)

Typography:
  --font-heading: 'Inter', sans-serif
  --font-body: 'Inter', sans-serif
  font-scale: 12/14/16/20/24/32/48px
  heading-weight: 700
  body-weight: 400

Spacing:
  base-unit: 8px
  scale: 4/8/12/16/24/32/48/64px

Borders & Shadows:
  --radius-sm: 6px
  --radius-md: 12px
  --radius-lg: 20px
  --shadow: 0 4px 24px rgba(0,0,0,0.4)

Special effects:
  glass-blur: backdrop-filter: blur(16px)
  gradient: linear-gradient(135deg, #7c3aed, #2563eb)
```

Show this token map to the user before proceeding. It's the foundation — if it's wrong, the output will be wrong.

### Step 4: Understand the User's Codebase

Before writing any code, read the relevant parts of their codebase:

- What framework? (React, Vue, Next.js, plain HTML?)
- What styling approach? (Tailwind, CSS modules, styled-components, plain CSS?)
- Where are global styles defined? (globals.css, theme.ts, tailwind.config.js?)
- What components need restyling? (ask the user if unclear)

Do not rewrite everything — surgical precision. Apply the design tokens to the existing structure.

### Step 5: Apply the Design

The application strategy depends on their stack:

**If Tailwind:** Update `tailwind.config.js` with the new color palette, font family, border radius scale. Add custom CSS variables for anything Tailwind can't handle natively.

**If CSS/CSS Modules:** Create or update a `:root` variables block in globals.css. Update component stylesheets to use the new variables.

**If styled-components/Emotion:** Update the theme object. Replace hardcoded color/spacing values with theme tokens.

**In all cases:**
- Apply colors, typography, and spacing globally first
- Then tackle component-level details (buttons, cards, nav) one at a time
- Preserve all existing functionality and layout structure — only visual properties change
- Add any special effects (glass blur, gradients, animations) that define the inspiration site's character

Read `references/apply-guide.md` for framework-specific implementation patterns.

### Step 6: Show the Before/After

After applying changes, clearly present:
- Which files were modified
- The design token mapping (source → what you set it to)
- Any special effects added
- What the user should check visually (hover states, dark/light mode, mobile)

If the user has a dev server running, remind them to check it. Offer to iterate on specific components.

## Key Principles

**Design language, not markup.** The inspiration site's HTML structure and content are theirs. You're extracting the *design language* — how colors relate, how spacing flows, what gives the site its character — to apply as your own creative foundation.

**Design tokens first, code second.** Rushing to apply colors before understanding the full system leads to inconsistent results. Always build the token map first.

**Ask about scope.** "Apply the design everywhere" vs "just make the homepage feel like it" vs "only restyle the navbar" are very different jobs. Clarify before proceeding.

**Don't break what works.** The user's components work. Only change visual properties. If you're uncertain whether a change might break layout, err on the side of caution and flag it.

**Iterative is fine.** It's often better to get the foundation right (colors, type, spacing) and let the user review before tackling component-level details.

## What to Do When...

**The site uses a design system (Material, shadcn, etc.):** Identify it, tell the user, and ask if they want to adopt the same system or just extract the visual tokens.

**The CSS is minified/obfuscated:** Fall back to the screenshot + visual analysis. You can still extract colors, spacing, and shapes from visual inspection.

**The inspiration site is JS-rendered and the HTML scrape comes back mostly empty:** Note this to the user — the screenshot will still work for visual analysis, but CSS extraction will be limited. You can still infer most tokens visually.

**The user's codebase uses a component library (shadcn, Chakra, MUI):** Apply the design by customizing the library's theme/config rather than overriding individual components.

**Multiple pages need to match:** Use the homepage for overall design tokens, but offer to check inner pages (e.g., `/pricing`, `/docs`) if the user wants to match a specific page's look.


---


## 📚 Reference Materials


### Apply Guide

# Design Application Guide

How to apply extracted design tokens to different frontend stacks.

## Tailwind CSS Projects

### 1. Update tailwind.config.js / tailwind.config.ts

```js
// tailwind.config.js
module.exports = {
  theme: {
    extend: {
      colors: {
        bg: {
          page:    '#0a0a0f',
          surface: '#13131a',
          elevated:'#1a1a2e',
        },
        brand: {
          DEFAULT: '#7c3aed',
          hover:   '#6d28d9',
        },
        border: 'rgba(255,255,255,0.08)',
        text: {
          primary:   '#ffffff',
          secondary: '#a0a0b8',
          muted:     '#606080',
        }
      },
      fontFamily: {
        sans: ['Inter', 'sans-serif'],
      },
      borderRadius: {
        'sm': '6px',
        'md': '12px',
        'lg': '20px',
        'xl': '28px',
      },
      boxShadow: {
        'card': '0 4px 24px rgba(0,0,0,0.4)',
        'glow': '0 0 24px rgba(124,58,237,0.3)',
      },
    }
  }
}
```

### 2. Update globals.css for non-Tailwind properties

```css
/* globals.css */
@import url('https://fonts.googleapis.com/css2?family=Inter:wght@400;500;600;700&display=swap');

:root {
  --gradient-hero: linear-gradient(135deg, #7c3aed 0%, #2563eb 100%);
  --gradient-subtle: radial-gradient(ellipse at top, rgba(124,58,237,0.15), transparent);
  --blur-glass: blur(16px);
  --transition: all 0.2s ease;
}

body {
  background-color: #0a0a0f;
  color: #ffffff;
  font-family: 'Inter', sans-serif;
}
```

### 3. Update component classes

Go through the user's main components and replace hardcoded color/spacing classes with the new ones. Focus on:
- `bg-*` → new background tokens
- `text-*` → new text tokens
- `border-*` → new border tokens
- `rounded-*` → new radius tokens

---

## Plain CSS / CSS Modules Projects

### 1. Create/update design tokens file

```css
/* styles/tokens.css or styles/globals.css */
@import url('https://fonts.googleapis.com/css2?family=Inter:wght@400;500;600;700&display=swap');

:root {
  /* Colors */
  --color-bg-page:     #0a0a0f;
  --color-bg-surface:  #13131a;
  --color-bg-elevated: #1a1a2e;
  --color-text:        #ffffff;
  --color-text-muted:  #a0a0b8;
  --color-brand:       #7c3aed;
  --color-brand-hover: #6d28d9;
  --color-border:      rgba(255, 255, 255, 0.08);

  /* Typography */
  --font-sans: 'Inter', sans-serif;
  --text-xs:   12px;
  --text-sm:   14px;
  --text-base: 16px;
  --text-lg:   20px;
  --text-xl:   24px;
  --text-2xl:  32px;
  --text-3xl:  48px;

  /* Spacing */
  --space-1:  4px;
  --space-2:  8px;
  --space-3:  12px;
  --space-4:  16px;
  --space-6:  24px;
  --space-8:  32px;
  --space-12: 48px;
  --space-16: 64px;

  /* Borders */
  --radius-sm:   6px;
  --radius-md:   12px;
  --radius-lg:   20px;
  --radius-pill: 9999px;

  /* Shadows & Effects */
  --shadow-card:  0 4px 24px rgba(0, 0, 0, 0.4);
  --shadow-glow:  0 0 24px rgba(124, 58, 237, 0.3);
  --blur-glass:   blur(16px);

  /* Gradients */
  --gradient-hero: linear-gradient(135deg, #7c3aed 0%, #2563eb 100%);

  /* Animation */
  --transition: all 0.2s ease;
}

body {
  background: var(--color-bg-page);
  color: var(--color-text);
  font-family: var(--font-sans);
  line-height: 1.5;
}
```

### 2. Update component styles

Replace hardcoded values with variables throughout component CSS files. Use find+replace for common patterns.

---

## styled-components / Emotion Projects

### 1. Update the theme object

```ts
// theme.ts
export const theme = {
  colors: {
    bg: {
      page:    '#0a0a0f',
      surface: '#13131a',
      elevated:'#1a1a2e',
    },
    text: {
      primary:  '#ffffff',
      muted:    '#a0a0b8',
    },
    brand: '#7c3aed',
    border:'rgba(255,255,255,0.08)',
  },
  fonts: {
    sans: "'Inter', sans-serif",
  },
  radii: {
    sm:   '6px',
    md:   '12px',
    lg:   '20px',
    pill: '9999px',
  },
  shadows: {
    card: '0 4px 24px rgba(0,0,0,0.4)',
    glow: '0 0 24px rgba(124,58,237,0.3)',
  },
  gradients: {
    hero: 'linear-gradient(135deg, #7c3aed 0%, #2563eb 100%)',
  },
  transitions: {
    default: 'all 0.2s ease',
  },
}
```

---

## Next.js / Nuxt Projects

These are usually Tailwind or CSS Modules under the hood — apply the appropriate guide above.

For Next.js specifically:
- Global styles: `app/globals.css` (App Router) or `styles/globals.css` (Pages Router)
- Tailwind config: `tailwind.config.ts` at root

---

## Component Priority Order

Apply the design in this order for maximum impact with minimum risk:

1. **Global background + text colors** — immediate transformation, zero breakage risk
2. **Font import + font-family** — single line change, huge visual impact
3. **Navigation/header** — most visible, sets the tone
4. **Cards & containers** — background, border, radius, shadow
5. **Buttons** — color, shape, hover state
6. **Form inputs** — background, border, focus ring
7. **Typography scale** — heading sizes and weights
8. **Special effects** — glass blur, gradients, glows (do these last, they're icing)

---

## Glassmorphism Effect (Common in Modern SaaS/AI Sites)

If the target site uses glass cards (common in dark AI/SaaS sites):

```css
.glass-card {
  background: rgba(255, 255, 255, 0.05);
  backdrop-filter: blur(16px);
  -webkit-backdrop-filter: blur(16px);
  border: 1px solid rgba(255, 255, 255, 0.08);
  border-radius: 12px;
}
```

In Tailwind:
```html
<div class="bg-white/5 backdrop-blur-md border border-white/8 rounded-xl">
```

Note: `backdrop-filter` requires the parent to NOT have `overflow: hidden` set on ancestors in some browsers. Flag this to the user if they see glass effects not working.

---

## Gradient Text (Popular in AI/Tech Sites)

```css
.gradient-text {
  background: linear-gradient(135deg, #7c3aed, #2563eb);
  -webkit-background-clip: text;
  -webkit-text-fill-color: transparent;
  background-clip: text;
}
```

In Tailwind (requires custom config or inline style):
```html
<span class="bg-gradient-to-r from-violet-500 to-blue-500 bg-clip-text text-transparent">
```

---

## Checking Your Work

After applying, mentally walk through:
- [ ] Page background matches
- [ ] Primary text color matches
- [ ] Card/panel background and border match
- [ ] Primary button color + shape match
- [ ] Font family matches (check Chrome DevTools → Computed → font-family)
- [ ] Heading weight and size feel similar
- [ ] Any special effects (blur, gradient, glow) are present
- [ ] Hover transitions feel similar in speed/style
- [ ] Mobile layout feels similar (padding, stacking)




### Css Extraction

# CSS Extraction Playbook

After scraping the raw HTML, extract the design tokens systematically. Work through these layers in order.

## Layer 1: CSS Custom Properties (The Jackpot)

Search the HTML for `:root` blocks — these are design systems that have done your work for you:

```html
<style>
  :root {
    --color-bg: #0a0a0f;
    --color-primary: #7c3aed;
    --font-sans: 'Inter', sans-serif;
    --radius: 12px;
  }
</style>
```

Grab every `--variable` and classify it: color, font, spacing, shadow, radius, animation.

## Layer 2: Font Imports

Look for `@import` at the top of `<style>` blocks and in `<link>` tags:

```html
<link rel="stylesheet" href="https://fonts.googleapis.com/css2?family=Inter:wght@400;500;600;700&display=swap">
```

Or within CSS:
```css
@import url('https://fonts.googleapis.com/css2?family=Outfit:wght@300;400;700&display=swap');
```

Record the font families and the weights loaded — the weights tell you what's used (e.g., 300 = light body, 700 = bold headings).

## Layer 3: Tailwind Config (if site uses Tailwind)

Signs it's Tailwind: classes like `bg-purple-600`, `text-gray-200`, `rounded-xl`, `shadow-lg`, `backdrop-blur-md`.

If there's a custom Tailwind theme, it may appear as an inline script:
```html
<script>
  tailwind.config = {
    theme: {
      extend: {
        colors: { brand: '#7c3aed' }
      }
    }
  }
</script>
```

For standard Tailwind sites without custom config, use the visual screenshot to identify which Tailwind colors they're using, then replicate with the same class names.

## Layer 4: Repeated Class Patterns

Scan the HTML for repeated class combinations on similar elements. The pattern reveals the design system:

```html
<!-- Repeated card pattern -->
<div class="bg-white/5 border border-white/10 rounded-2xl p-6 shadow-xl backdrop-blur-sm">

<!-- Repeated button pattern -->
<button class="bg-violet-600 hover:bg-violet-500 text-white font-semibold px-6 py-3 rounded-full transition-all">
```

These patterns tell you the component-level design decisions even without explicit custom properties.

## Layer 5: Inline Styles & Gradients

Look for inline `style=""` attributes on hero sections, headers, and highlighted elements — these often contain the most intentional design choices:

```html
<section style="background: linear-gradient(135deg, #667eea 0%, #764ba2 100%);">
<div style="background: radial-gradient(ellipse at top, rgba(124,58,237,0.15) 0%, transparent 60%);">
```

## Layer 6: Animation & Transition Patterns

Note CSS transitions and animations — they define the "feel" of the site:

```css
transition: all 0.2s ease;        /* snappy */
transition: all 0.4s cubic-bezier(0.4, 0, 0.2, 1);  /* Material-style */
animation: fadeIn 0.6s ease forwards;  /* page load feel */
```

## When the HTML Comes Back Sparse (JS-Rendered Sites)

Signs: `<div id="root"></div>` with minimal content, or `<div id="__next">` with a loading spinner.

In this case:
1. Screenshot is still fully usable for visual analysis
2. Look for `<link>` tags pointing to external CSS files — note their paths even if you can't fetch them
3. Look for any `<script>` tags with inline config (Next.js/Nuxt often embed theme data)
4. Check for `<meta>` theme-color tags: `<meta name="theme-color" content="#7c3aed">`
5. Fall back to full visual extraction from the screenshot

## Output Format

Always output your extraction as a structured token map before writing any code:

```
EXTRACTED DESIGN TOKENS
========================
Source: https://example.com
Method: CSS custom properties + visual analysis

COLORS
  Background:
    page:    #0a0a0f
    surface: #13131a  (cards, panels)
    elevated:#1a1a2e  (modals, dropdowns)
  Text:
    primary: #ffffff
    secondary:#a0a0b8
    muted:   #606080
  Brand:
    primary: #7c3aed
    hover:   #6d28d9
    glow:    rgba(124,58,237,0.3)
  Border:   rgba(255,255,255,0.08)

TYPOGRAPHY
  Heading font: 'Inter', sans-serif (weights: 600, 700)
  Body font:    'Inter', sans-serif (weight: 400)
  Size scale:   12 / 14 / 16 / 20 / 24 / 32 / 48 / 64px
  Line height:  1.5 body / 1.2 headings

SPACING
  Base unit: 8px
  Scale: 4 / 8 / 12 / 16 / 20 / 24 / 32 / 48 / 64 / 96px

BORDERS & EFFECTS
  Radius sm:  6px   (badges, inputs)
  Radius md:  12px  (cards)
  Radius lg:  20px  (modals, large containers)
  Radius pill: 9999px (buttons, tags)
  Shadow:     0 4px 24px rgba(0,0,0,0.4)
  Glass blur: backdrop-filter: blur(16px)

GRADIENTS
  Hero:    linear-gradient(135deg, #7c3aed 0%, #2563eb 100%)
  Subtle:  radial-gradient(ellipse at top, rgba(124,58,237,0.15), transparent)

ANIMATIONS
  Transition: all 0.2s ease
  Hover scale: transform: scale(1.02)
```




### Capture Guide

# Capture Guide

## Running Both Captures

Always run screenshot and HTML scrape in parallel (they're independent requests):

```bash
# Run both simultaneously
bash scripts/screenshot.sh "https://target.com" "/tmp/target_screenshot.png" &
SCREENSHOT_PID=$!

bash scripts/scrape_html.sh "https://target.com" "/tmp/target_page.html" &
HTML_PID=$!

wait $SCREENSHOT_PID $HTML_PID
echo "Both captures complete"
```

Or just use two Agent tool calls in the same message.

## Reading the Screenshot

Use the Read tool on the PNG file path — Claude is multimodal and can directly analyze the image. Describe what you see in terms of the design token categories (colors, type, layout, components, effects).

## Reading the HTML

The HTML file can be very large. Use Grep to extract the most useful parts:

```bash
# Extract CSS custom properties
grep -o '\-\-[a-zA-Z-]*:[^;]*' /tmp/target_page.html | head -100

# Extract @import font statements
grep -E '@import|fonts\.googleapis' /tmp/target_page.html | head -20

# Extract <style> block content
grep -A 500 '<style' /tmp/target_page.html | head -600

# Extract inline styles from key elements
grep -o 'style="[^"]*"' /tmp/target_page.html | head -50

# Look for Tailwind config
grep -A 20 'tailwind.config' /tmp/target_page.html | head -50
```

## Common Issues

### Large HTML files
Use Grep with specific patterns rather than reading the whole file. Focus on `<style>` tags, `:root`, `@import`, and `<link rel="stylesheet">`.

### Minified CSS
Minified CSS has no whitespace. You can still extract colors with:
```bash
grep -oE '#[0-9a-fA-F]{3,8}' /tmp/target_page.html | sort -u
grep -oE 'rgb\([^)]+\)' /tmp/target_page.html | sort -u
grep -oE 'rgba\([^)]+\)' /tmp/target_page.html | sort -u
```

### External CSS files
The HTML may reference external `.css` files. You'll see `<link href="/assets/style.abc123.css" rel="stylesheet">`. Unfortunately, relative paths won't work from the API response. Use the full URL with the scrape script:
```bash
bash scripts/scrape_html.sh "https://target.com/assets/style.abc123.css" "/tmp/target_styles.css"
```

### JS-rendered sites (mostly empty HTML)
If the HTML is sparse (React/Next.js SPA), the design lives in:
1. The screenshot (fully rendered) — most useful
2. External CSS chunks linked in `<head>`
3. Inline `<script>` blocks with config data

Fall back to pure visual extraction from the screenshot in this case.

## Screenshot Quality

The screenshot captures the full rendered page at desktop viewport. For sites with:
- **Dark themes**: Colors will be accurate in screenshot
- **Animations**: Screenshot captures a static moment (usually the loaded state)
- **Sticky headers**: Will show in their scrolled-to-top position
- **Above-the-fold content**: Most important — this is what defines the brand

If you need to see a specific section (e.g., pricing page, a component below the fold), scrape a different URL:
```bash
bash scripts/screenshot.sh "https://target.com/pricing" "/tmp/pricing_screenshot.png"
```




---

## 🚀 Usage

**Reference this template:** `@skill-design-mirror.md`


**Platform-specific:**
- **GitHub Copilot**: Add to `.github/copilot-instructions.md`
- **Augment Code**: Use `aug context add` command
- **Cursor/Windsurf**: Reference in chat with `@filename`
- **Claude**: Add to Project Knowledge
- **ChatGPT**: Add to Custom GPT configuration
