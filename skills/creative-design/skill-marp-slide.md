---
id: skill-marp-slide
type: skill
name: marp-slide
description: Create professional Marp presentation slides with 7 beautiful themes
  (default, minimal, colorful, dark, gradient, tech, business). Use when users request
  slide creation, presentations, or Marp documents. Supports custom themes, image
  layouts, and "make it look good" requests with automatic quality improvements.
category: creative-design
complexity: medium
keywords:
- github
capabilities: []
token_estimate: 1422
has_references: true
reference_count: 7
---

<!-- Converted from Claude Skill Template -->
<!-- Complexity: Medium -->
<!-- Estimated Tokens: ~1,422 -->
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




# marp-slide

> Create professional Marp presentation slides with 7 beautiful themes (default, minimal, colorful, dark, gradient, tech, business). Use when users request slide creation, presentations, or Marp documents. Supports custom themes, image layouts, and "make it look good" requests with automatic quality improvements.

# Marp Slide Creator

Create professional, visually appealing Marp presentation slides with 7 pre-designed themes and built-in best practices.

## When to Use This Skill

Use this skill when the user:
- Requests to create presentation slides or Marp documents
- Asks to "make slides look good" or "improve slide design"
- Provides vague instructions like "良い感じにして" (make it nice) or "かっこよく" (make it cool)
- Wants to create lecture or seminar materials
- Needs bullet-point focused slides with occasional images

## Quick Start

### Step 1: Select Theme

First, determine the appropriate theme based on the user's request and content.

**Quick theme selection:**
- **Technical/Developer content** → tech theme
- **Business/Corporate** → business theme
- **Creative/Event** → colorful or gradient theme
- **Academic/Simple** → minimal theme
- **General/Unsure** → default theme
- **Dark background preferred** → dark or tech theme

For detailed theme selection guidance, read `references/theme-selection.md`.

### Step 2: Create Slides

1. **Read relevant references first**:
   - Always start by reading `references/marp-syntax.md` for basic syntax
   - For images: `references/image-patterns.md` (official Marpit image syntax)
   - For advanced features (math, emoji): `references/advanced-features.md`
   - For custom themes: `references/theme-css-guide.md`

2. Copy content from the appropriate template file:
   - `assets/template-basic.md` - Default theme (most common)
   - `assets/template-minimal.md` - Minimal theme
   - `assets/template-colorful.md` - Colorful theme
   - `assets/template-dark.md` - Dark mode theme
   - `assets/template-gradient.md` - Gradient theme
   - `assets/template-tech.md` - Tech/code theme
   - `assets/template-business.md` - Business theme

3. Read `references/best-practices.md` for quality guidelines

4. Structure content following best practices:
   - Title slide with `<!-- _class: lead -->`
   - Concise h2 titles (5-7 characters in Japanese)
   - 3-5 bullet points per slide
   - Adequate whitespace

5. Add images if needed using patterns from `references/image-patterns.md`

6. Save to `the project output directory` with `.md` extension

## Available Themes

### 1. Default Theme
**Colors**: Beige background, navy text, blue headings
**Style**: Clean, sophisticated with decorative lines
**Use for**: General seminars, lectures, presentations
**Template**: `template-basic.md`

### 2. Minimal Theme
**Colors**: White background, gray text, black headings
**Style**: Minimal decoration, wide margins, light fonts
**Use for**: Content-focused presentations, academic talks
**Template**: `template-minimal.md`

### 3. Colorful & Pop Theme
**Colors**: Pink gradient background, multi-color accents
**Style**: Vibrant gradients, bold fonts, rainbow accents
**Use for**: Youth-oriented events, creative projects
**Template**: `template-colorful.md`

### 4. Dark Mode Theme
**Colors**: Black background, cyan/purple accents
**Style**: Dark theme with glow effects, eye-friendly
**Use for**: Tech presentations, evening talks, modern look
**Template**: `template-dark.md`

### 5. Gradient Background Theme
**Colors**: Purple/pink/blue/green gradients (varies per slide)
**Style**: Different gradient per slide, white text, shadows
**Use for**: Visual-focused, creative presentations
**Template**: `template-gradient.md`

### 6. Tech/Code Theme
**Colors**: GitHub-style dark background, blue/green accents
**Style**: Code fonts, Markdown-style headers with # symbols
**Use for**: Programming tutorials, tech meetups, developer content
**Template**: `template-tech.md`

### 7. Business Theme
**Colors**: White background, navy headings, blue accents
**Style**: Corporate presentation style, top border, table support
**Use for**: Business presentations, proposals, reports
**Template**: `template-business.md`

## Creating Slides Process

### Basic Workflow

1. **Understand requirements**
   - Identify content: title, topics, key points
   - Determine target audience
   - Assess formality level

2. **Select theme**
   - Use quick selection rules above
   - If uncertain, consult `references/theme-selection.md`
   - Default to default theme if still unsure

3. **Apply template**
   - Load appropriate template from `assets/`
   - CSS is already embedded - no external files needed
   - Maintain template structure

4. **Structure content**
   - Title slide: `<!-- _class: lead -->` + h1
   - Content slides: h2 title + bullet points
   - Keep titles to 5-7 characters (Japanese)
   - Use 3-5 bullet points per slide

5. **Refine quality**
   - Read `references/best-practices.md`
   - Ensure adequate whitespace
   - Maintain consistency
   - Keep text concise (15-25 chars per line)

6. **Add images**
   - If needed, consult `references/image-patterns.md`
   - Common: `![bg right:40%](image.png)` for side images
   - Use proper Marp image syntax

7. **Output file**
   - Save to `the project output directory`
   - Use descriptive filename like `presentation.md`

## Handling "Make It Look Good" Requests

When users give vague instructions like "良い感じにして", "かっこよく", or "make it cool":

1. **Infer theme from content**:
   - Business content → business theme
   - Technical content → tech or dark theme
   - Creative content → gradient or colorful theme
   - General → default theme

2. **Apply best practices automatically**:
   - Shorten titles to 5-7 characters
   - Limit bullet points to 3-5 items
   - Add adequate whitespace
   - Use consistent structure

3. **Enhance visual hierarchy**:
   - Use h3 for sub-sections when appropriate
   - Break up dense text into multiple slides
   - Ensure logical flow (intro → body → conclusion)

4. **Maintain professional tone**:
   - Match formality to content
   - Use parallel structure in lists
   - Keep technical terms consistent

## Image Integration

For slides with images, consult `references/image-patterns.md` for detailed syntax.

Common patterns:
- **Side image**: `![bg right:40%](image.png)` - Image on right, text on left
- **Centered**: `![w:600px](image.png)` - Centered with specific width
- **Full background**: `![bg](image.png)` - Full-screen background
- **Multiple images**: Multiple `![bg]` declarations

Example lecture pattern:
```markdown
## Slide Title

![bg right:40%](diagram.png)

- Explanation point 1
- Explanation point 2
- Explanation point 3
```

## File Output

Always save the final Marp file to `the project output directory` with `.md` extension:
- `presentation.md`
- `seminar-slides.md`
- `lecture-materials.md`

## Quality Checklist

Before delivering slides, verify:
- [ ] Theme selected appropriately for content
- [ ] CSS theme is embedded in the file
- [ ] Title slide uses `<!-- _class: lead -->`
- [ ] All h2 titles are concise (5-7 chars)
- [ ] Bullet points are 3-5 items per slide
- [ ] Images use proper Marp syntax
- [ ] File saved to outputs directory
- [ ] Content follows best practices

## References

### Core Documentation
- **Marp syntax**: `references/marp-syntax.md` - Basic Marp/Marpit syntax (directives, frontmatter, pagination, etc.)
- **Image patterns**: `references/image-patterns.md` - Official image syntax (bg, filters, split backgrounds)
- **Theme CSS guide**: `references/theme-css-guide.md` - How to create custom themes based on Marpit specification
- **Advanced features**: `references/advanced-features.md` - Math, emoji, fragmented lists, Marp CLI, VS Code
- **Official themes**: `references/official-themes.md` - default, gaia, uncover themes documentation

### Quality & Selection Guides
- **Theme selection**: `references/theme-selection.md` - How to choose the right theme for content
- **Best practices**: `references/best-practices.md` - Quality guidelines for "cool" slides

### Templates & Assets
- **Templates**: `assets/template-*.md` - Starting points with embedded CSS for each theme (7 themes)
- **Standalone CSS**: `assets/theme-*.css` - CSS files for reference (already embedded in templates)

### Official External Links
- **Marp Official Site**: https://marp.app/
- **Marpit Directives**: https://marpit.marp.app/directives
- **Marpit Image Syntax**: https://marpit.marp.app/image-syntax
- **Marpit Theme CSS**: https://marpit.marp.app/theme-css
- **Marp Core GitHub**: https://github.com/marp-team/marp-core
- **Marp CLI GitHub**: https://github.com/marp-team/marp-cli


---


## 📚 Reference Materials


### Best Practices

# Marp Slide Creation Best Practices

Guidelines for creating "cool" high-quality slides.

## Slide Titles (h2)

### ✅ Good Examples
- **Concise**: About 5-7 characters
- **Clear**: Content is immediately understandable
- **Consistent**: Use the same style at the same hierarchy level

```markdown
## Introduction
## Problem
## Solution
## Results
```

### ❌ Bad Examples
```markdown
## In this section we will explain the introduction
## What are the challenges we are facing
```

## Bullet Points

### ✅ Good Examples
- **3-5 items**: Not too many
- **Concise**: About 15-25 characters per line
- **Parallel**: Same grammatical structure at the same level

```markdown
- Simple and easy to understand
- Unified design
- Effective information delivery
```

### ❌ Bad Examples
```markdown
- This is a very long explanation that doesn't fit on one line and becomes difficult to read
- Short
- The next item is in sentence format. This lack of uniformity makes it hard to read.
```

## Slide Structure

### Basic Structure

1. **Title Slide** (`<!-- _class: lead -->`)
   - Title
   - Presenter name
   - Date

2. **Agenda Slide**
   - Show overall flow
   - About 3-5 items

3. **Content Slides**
   - 1 slide = 1 message
   - Title summarizes content

4. **Summary Slide**
   - Reconfirm key points
   - Words of thanks

### Recommended Slide Count

- 5-minute presentation: 5-8 slides
- 10-minute presentation: 10-15 slides
- 20-minute presentation: 15-25 slides

## Text Amount

### ✅ Good Balance

```markdown
## Product Features

- High-speed processing
- Intuitive UI
- Highly extensible design
```

### ❌ Too Crowded

```markdown
## About the Product

This product was developed using the latest technology.
The main features include the following 7 points:
- Feature 1: Detailed explanation continues at length...
- Feature 2: Even more detailed explanation...
(Continued)
```

## Using Whitespace

- **Adequate whitespace**: Don't cram too much information
- **Visual guidance**: Layout that naturally draws eyes to important information
- **Breathing room**: Appropriate "pauses" between slides

## Using Colors

Leverage colors defined in the theme:
- **Background color**: `#f8f8f4` (light beige)
- **Text color**: `#3a3b5a` (dark navy)
- **Heading color**: `#4f86c6` (blue)
- **Accent color**: `#000000` (black)

### When Using Additional Colors

```markdown
<span style="color: #c62828;">Important point</span>
```

Use sparingly and avoid excessive decoration.

## Using Images

### Effective Usage

- **Clear purpose**: To aid understanding, not just decoration
- **High quality**: Use high-resolution images
- **Appropriate size**: Neither too large nor too small

### Layout Tips

```markdown
# Text on left, image on right
![bg right:40%](image.png)

- Point 1
- Point 2
```

## Font Size Guidelines

Defined in the theme:
- h1: 56px (title slide only)
- h2: 40px (regular slide titles)
- h3: 28px (subheadings)
- Body text: 22px

## Animations and Transitions

Marp does not support animations by default.
Focus on simple slide transitions.

## Checklist

After completing slides, verify:

- [ ] Are titles concise (5-7 characters)?
- [ ] Are bullet points 3-5 items?
- [ ] Is it 1 slide = 1 message?
- [ ] Is the text amount appropriate?
- [ ] Is there sufficient whitespace?
- [ ] Are images used effectively?
- [ ] Is there overall consistency?
- [ ] Is the slide count appropriate?




### Image Patterns

# Marp Image Syntax Reference

Image placement and styling methods based on official Marpit image syntax.

Official documentation: https://marpit.marp.app/image-syntax

## Basic Image Insertion

### Regular Images

```markdown
![](image.png)
![alt text](image.png)
```

Images are displayed as content.

### Size Specification

Marp allows adding size keywords to images:

```markdown
![width:600px](image.png)
![height:400px](image.png)
![w:600px h:400px](image.png)
```

**Supported units**:
- `px` - Pixels
- `%` - Percent
- `em`, `rem`, `cm`, `mm`, `in`, `pt`, `pc`

**Abbreviations**:
- `w:` = `width:`
- `h:` = `height:`

## Background Images (`bg` keyword)

### Basic Background Image

```markdown
![bg](image.png)
```

Places the image as a slide background. It doesn't overlap with text content and is positioned in the background.

### Background Size Keywords

```markdown
![bg fit](image.png)
![bg cover](image.png)
![bg contain](image.png)
![bg auto](image.png)
```

| Keyword | Behavior | CSS Equivalent |
|---------|----------|----------------|
| `fit` | Preserve aspect ratio, fit within slide | `background-size: contain` |
| `cover` | Preserve aspect ratio, cover entire slide | `background-size: cover` |
| `contain` | Same as `fit` | `background-size: contain` |
| `auto` | Original size | `background-size: auto` |

### Background Size (Numeric Values)

```markdown
![bg 80%](image.png)
![bg 1280px](image.png)
![bg 50% 80%](image.png)
```

Supports the same syntax as CSS `background-size` property.

## Split Backgrounds

You can split the screen using multiple background images.

### Basic Split

```markdown
![bg](image1.png)
![bg](image2.png)
```

Two images are displayed split left and right.

### Three or More Splits

```markdown
![bg](image1.png)
![bg](image2.png)
![bg](image3.png)
```

Three or more images are divided equally.

### Specifying Split Direction

Default is horizontal split, but vertical split is also possible:

```markdown
![bg vertical](image1.png)
![bg](image2.png)
```

Use `vertical` keyword to change to vertical split.

### Left/Right Alignment

```markdown
![bg left](image.png)
```

Places image on the left, reserving text space on the right.

```markdown
![bg right](image.png)
```

Places image on the right, reserving text space on the left.

### Size Ratio Specification

```markdown
![bg left:33%](image.png)
```

33% image on left, 67% text space on right.

```markdown
![bg right:60%](image.png)
```

60% image on right, 40% text space on left.

## Filter Effects

### Brightness Adjustment

```markdown
![brightness:0.5](image.png)
![brightness:1.5](image.png)
```

Value range: 0 (completely black) ~ 1 (normal) ~ 2+ (brighter)

### Contrast

```markdown
![contrast:0.8](image.png)
![contrast:1.5](image.png)
```

### Blur

```markdown
![blur:10px](image.png)
```

### Grayscale

```markdown
![grayscale](image.png)
![grayscale:1](image.png)
```

Value range: 0 (color) ~ 1 (full grayscale)

### Sepia

```markdown
![sepia](image.png)
![sepia:0.8](image.png)
```

### Hue Rotation

```markdown
![hue-rotate:180deg](image.png)
```

### Invert

```markdown
![invert](image.png)
![invert:0.8](image.png)
```

### Opacity

```markdown
![opacity:0.5](image.png)
```

### Saturation

```markdown
![saturate:2](image.png)
```

### Multiple Filters

```markdown
![brightness:1.2 contrast:1.1 saturate:1.3](image.png)
```

## Practical Pattern Examples

### Pattern 1: Text on Left, Image on Right

```markdown
## Product Introduction

![bg right:40%](product.png)

- Feature 1
- Feature 2
- Feature 3
```

### Pattern 2: Background Image + Overlay Text

```markdown
![bg brightness:0.5](hero.png)

# Catchphrase

Subtext
```

White text placed on darkened background.

### Pattern 3: Multiple Image Comparison

```markdown
![bg left:50%](before.png)
![bg right:50%](after.png)
```

Place Before/After side by side.

### Pattern 4: Vertical Comparison

```markdown
![bg vertical](image1.png)
![bg](image2.png)
```

Place images top and bottom.

### Pattern 5: Sized Regular Image

```markdown
## Diagram

![w:600px](diagram.png)

The above diagram shows...
```

### Pattern 6: 3-Split Layout

```markdown
![bg](image1.png)
![bg](image2.png)
![bg](image3.png)
```

### Pattern 7: Background with Filter Effects

```markdown
![bg blur:5px brightness:0.7](background.png)

# Easy-to-Read Text

Subdued background with blur and darkness
```

## Important Notes

1. **Background Images and Text**: `![bg]` images are placed on the background layer and do not overlap with text
2. **Multiple Background Order**: They are placed from left to right (or top to bottom) in the order written
3. **Filter Support**: Not all filters work in all environments
4. **Relative Paths**: Image paths are specified relative to the Markdown file

## Official Reference

For details, refer to official documentation:
- **Image syntax**: https://marpit.marp.app/image-syntax




### Advanced Features

# Marp Advanced Features Reference

Advanced features of Marp Core and Marpit.

## Fragmented List (Progressive Display)

Feature to display list items progressively (animation effect).

Official documentation: https://github.com/marp-team/marpit/tree/main/docs/fragmented-list

### Basic Usage

```markdown
* Item 1
* Item 2
* Item 3
```

Normally, all items are displayed at once.

### Using Asterisks (*)

```markdown
* Item 1
* Item 2
* Item 3
```

When using `--html` option with Marp CLI, each item displays sequentially.

### Important Notes

- **Only effective in HTML output**: No effect in PDF/PPTX/images
- **Presentation mode**: Works during browser presentations
- **Marp for VS Code**: May not work in preview

## Math Notation (Marp Core Extension)

Supports Pandoc-style math formulas. Rendered using KaTeX.

Official: https://github.com/marp-team/marp-core#math-typesetting

### Inline Math

```markdown
Insert $E = mc^2$ in text
```

### Block Math

```markdown
$$
\int_{-\infty}^{\infty} e^{-x^2} dx = \sqrt{\pi}
$$
```

### Multi-line Math

```markdown
$$
\begin{aligned}
  f(x) &= x^2 + 2x + 1 \\
  &= (x + 1)^2
\end{aligned}
$$
```

### Math Examples

```markdown
## Quadratic Formula

$$
x = \frac{-b \pm \sqrt{b^2 - 4ac}}{2a}
$$

## Euler's Identity

$$
e^{i\pi} + 1 = 0
$$
```

### Important Notes

- **KaTeX notation**: Subset of LaTeX syntax
- **Unsupported notation**: Some LaTeX features not supported
- **KaTeX official**: https://katex.org/docs/supported.html

## Emoji (Marp Core Extension)

Supports GitHub Emoji notation.

Official: https://github.com/marp-team/marp-core#emoji

### Usage

```markdown
:smile: :heart: :+1: :sparkles:
```

Rendered result: 😄 ❤️ 👍 ✨

### Common Emoji

```markdown
:arrow_right: →
:check: ✓
:x: ✗
:bulb: 💡
:warning: ⚠️
:rocket: 🚀
:tada: 🎉
```

### Emoji List

Complete list: https://github.com/ikatyang/emoji-cheat-sheet

## Auto-scaling

Automatically adjusts font size when there's too much text.

### Disable

```markdown
---
marp: true
---

<!-- _class: no-scaling -->

# No auto-scaling
```

Control with custom CSS:

```css
section.no-scaling {
  --marpit-auto-scaling: off;
}
```

## Using HTML Tags

You can write HTML directly in Markdown.

### Alignment Control

```markdown
<div style="text-align: center;">
Centered text
</div>
```

### Two-Column Layout

```markdown
<div style="display: flex;">
<div style="flex: 1;">

## Left Side

- Point 1
- Point 2

</div>
<div style="flex: 1;">

## Right Side

- Point 3
- Point 4

</div>
</div>
```

### Styled Box

```markdown
<div style="background-color: #e3f2fd; padding: 20px; border-radius: 8px;">

**Important Point**

Important content goes here

</div>
```

## Marp CLI Detailed Options

Official: https://github.com/marp-team/marp-cli

### Basic Commands

```bash
# Convert to HTML
marp slide.md

# Convert to PDF
marp slide.md --pdf

# Convert to PowerPoint
marp slide.md --pptx

# Convert to image
marp slide.md --images png
```

### Watch Mode

```bash
# Watch file and auto-convert
marp -w slide.md

# Watch in server mode
marp -s -w slide.md
```

### Specify Theme

```bash
# Use custom theme
marp slide.md --theme custom-theme.css

# Specify theme directory
marp slide.md --theme-set themes/
```

### Batch Convert Multiple Files

```bash
# Convert all Markdown in directory
marp slides/*.md

# Specify output directory
marp slides/*.md -o output/
```

### HTML Output Options

```bash
# HTML output (single file)
marp slide.md -o output.html

# Standalone HTML (using CDN)
marp slide.md --html
```

### PDF Output Options

```bash
# PDF output
marp slide.md --pdf --allow-local-files

# PDF without page numbers
marp slide.md --pdf --pdf-notes
```

### Image Output

```bash
# Output as PNG images
marp slide.md --images png

# Output as JPEG images
marp slide.md --images jpeg

# Specify resolution
marp slide.md --images png --image-scale 2
```

## Marp for VS Code

Official: https://marketplace.visualstudio.com/items?itemName=marp-team.marp-vscode

### Enable

Write in Markdown file front matter:

```markdown
---
marp: true
---
```

### Preview

- `Ctrl+Shift+V` (Win/Linux)
- `Cmd+Shift+V` (Mac)

### Export

1. Command Palette (`Ctrl+Shift+P`)
2. Select "Marp: Export slide deck..."
3. Choose format (HTML/PDF/PPTX/PNG/JPEG)

### Settings

Customizable in VS Code settings:

```json
{
  "markdown.marp.themes": [
    "./themes/custom-theme.css"
  ],
  "markdown.marp.enableHtml": true
}
```

## Automated Build with GitHub Actions

Official: https://github.com/marketplace/actions/marp-action

### Basic Workflow

```yaml
name: Marp Build

on:
  push:
    branches: [ main ]

jobs:
  build:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v3

      - name: Marp Build
        uses: docker://marpteam/marp-cli:latest
        with:
          args: slides.md --pdf --allow-local-files

      - name: Upload PDF
        uses: actions/upload-artifact@v3
        with:
          name: slides
          path: slides.pdf
```

### Publish to GitHub Pages

```yaml
- name: Marp to Pages
  uses: docker://marpteam/marp-cli:latest
  with:
    args: slides.md -o index.html

- name: Deploy to Pages
  uses: peaceiris/actions-gh-pages@v3
  with:
    github_token: ${{ secrets.GITHUB_TOKEN }}
    publish_dir: ./
```

## Tips & Tricks

### 1. Customize Slide Numbers

```css
section::after {
  content: 'Page ' attr(data-marpit-pagination);
}
```

### 2. Gradient Background

```markdown
---
backgroundImage: linear-gradient(135deg, #667eea 0%, #764ba2 100%)
color: white
---
```

### 3. Two-Column Layout

```markdown
<div class="columns">
<div>

Left side content

</div>
<div>

Right side content

</div>
</div>

<style>
.columns {
  display: grid;
  grid-template-columns: 1fr 1fr;
  gap: 2rem;
}
</style>
```

### 4. Progress Bar

```css
section::before {
  content: '';
  position: absolute;
  top: 0;
  left: 0;
  width: calc(var(--paginate) / var(--paginate-total) * 100%);
  height: 5px;
  background-color: #3b82f6;
}
```

## Troubleshooting

### PDF Not Generated

- Check if Chrome or Edge is installed
- Add `--allow-local-files` option

### Fonts Not Displaying

- Load Google Fonts with `@import`
- Specify local fonts with absolute path

### Images Not Displaying

- Check image relative paths
- May need `--allow-local-files`

## Official References

- **Marp Official Site**: https://marp.app/
- **Marpit Directives**: https://marpit.marp.app/directives
- **Image Syntax**: https://marpit.marp.app/image-syntax
- **Theme CSS**: https://marpit.marp.app/theme-css
- **Marp Core**: https://github.com/marp-team/marp-core
- **Marp CLI**: https://github.com/marp-team/marp-cli
- **VS Code Extension**: https://marketplace.visualstudio.com/items?itemName=marp-team.marp-vscode




### Theme Css Guide

# Marp Theme CSS Creation Guide

How to create custom themes based on the official Marpit theme CSS specification.

Official documentation: https://marpit.marp.app/theme-css

## Basic Theme Structure

### @theme Metadata (Required)

Theme CSS requires `@theme` metadata:

```css
/* @theme theme-name */
```

Without this metadata, it won't be recognized as a theme.

### Basic Theme CSS Example

```css
/* @theme my-theme */

section {
  background-color: #fff;
  color: #333;
  font-size: 24px;
  padding: 60px;
}

h1 {
  color: #1e40af;
  font-size: 48px;
}

h2 {
  color: #3b82f6;
  font-size: 36px;
}
```

## HTML Structure

HTML structure generated by Marp:

```html
<section>
  <h1>Slide Title</h1>
  <p>Content</p>
</section>
```

Each slide is generated as a `<section>` element.

## Slide Size

### Default Size

```css
section {
  width: 1280px;
  height: 720px;
}
```

16:9 ratio (1280x720) is the default.

### Custom Size Definition

```css
/* @theme my-theme */
/* @size 4:3 960px 720px */

section {
  width: 960px;
  height: 720px;
}
```

Custom sizes can be defined with `@size` metadata.

## Pagination

Styling page numbers:

```css
section::after {
  content: attr(data-marpit-pagination) ' / ' attr(data-marpit-pagination-total);
  position: absolute;
  right: 30px;
  bottom: 20px;
  font-size: 16px;
  color: #666;
}
```

**Available attributes**:
- `data-marpit-pagination` - Current page number
- `data-marpit-pagination-total` - Total page count

## Headers and Footers

### Header

```css
header {
  position: absolute;
  top: 30px;
  left: 60px;
  right: 60px;
  font-size: 18px;
  color: #666;
}
```

### Footer

```css
footer {
  position: absolute;
  bottom: 30px;
  left: 60px;
  right: 60px;
  font-size: 18px;
  color: #666;
}
```

## Theme Extension and Inheritance

### Loading with @import

```css
/* @theme my-extended-theme */

@import 'default';

section {
  background-color: #f0f0f0;
}
```

You can extend existing themes.

### Inheriting Named Themes with @import-theme

```css
/* @theme my-theme */

@import-theme 'default';

h1 {
  color: red;
}
```

Using `@import-theme` allows loading by theme name.

## Class-Based Variations

### lead Class (Title Slide)

```css
section.lead {
  display: flex;
  flex-direction: column;
  justify-content: center;
  align-items: center;
  text-align: center;
}

section.lead h1 {
  font-size: 64px;
}
```

### invert Class (Inverted Colors)

```css
section.invert {
  background-color: #000;
  color: #fff;
}

section.invert h1,
section.invert h2 {
  color: #fff;
}
```

## Scoped Style (Slide-Specific Styles)

Use `<style scoped>` in Markdown:

```markdown
<style scoped>
section {
  background-color: #e3f2fd;
}
</style>

# Only this slide has blue background
```

## Background Image Styling

Background images are processed automatically but can be adjusted with CSS:

```css
section[data-marpit-background-image] {
  background-size: cover;
  background-position: center;
}
```

## Math Formula Styling (Marp Core)

```css
.katex {
  font-size: 1.2em;
}

.katex-display {
  margin: 1em 0;
}
```

## List Styling

```css
ul, ol {
  margin: 0.5em 0;
  padding-left: 1.5em;
}

li {
  margin: 0.3em 0;
}

li::marker {
  color: #3b82f6;
}
```

## Table Styling

```css
table {
  border-collapse: collapse;
  width: 100%;
  margin: 1em 0;
}

th, td {
  border: 1px solid #ddd;
  padding: 0.5em 1em;
  text-align: left;
}

th {
  background-color: #f0f0f0;
  font-weight: bold;
}
```

## Code Block Styling

```css
pre {
  background-color: #f5f5f5;
  border-radius: 4px;
  padding: 1em;
  overflow-x: auto;
}

code {
  font-family: 'Courier New', monospace;
  font-size: 0.9em;
}

pre code {
  background-color: transparent;
  padding: 0;
}
```

## Responsive Design

```css
@media screen and (max-width: 1280px) {
  section {
    font-size: 20px;
  }

  h1 {
    font-size: 40px;
  }
}
```

## Practical Theme Examples

### Minimal Theme

```css
/* @theme minimal */

section {
  background-color: #ffffff;
  color: #333333;
  font-family: -apple-system, BlinkMacSystemFont, 'Segoe UI', sans-serif;
  font-size: 24px;
  padding: 80px;
}

h1, h2, h3 {
  font-weight: 300;
  color: #000000;
}

h1 {
  font-size: 60px;
  margin-bottom: 0.5em;
}

h2 {
  font-size: 40px;
  margin-bottom: 0.5em;
}
```

### Dark Theme

```css
/* @theme dark */

section {
  background-color: #1a1a1a;
  color: #e0e0e0;
  font-size: 22px;
  padding: 60px;
}

h1, h2, h3 {
  color: #61dafb;
}

a {
  color: #61dafb;
}

code {
  background-color: #2d2d2d;
  color: #61dafb;
}
```

## Embedding Styles in Markdown

```markdown
---
marp: true
---

<style>
section {
  background-color: #f8f8f4;
  font-family: 'Noto Sans JP', sans-serif;
}

h1 {
  color: #4f86c6;
}
</style>

# Slide
```

This method allows applying custom styles without using a theme.

## Best Practices

1. **Ensure Contrast**: Maintain a contrast ratio of 4.5:1 or higher between background and text colors
2. **Appropriate Font Sizes**: Body text 22-24px, titles 40-60px
3. **Sufficient Spacing**: Recommended padding of 60px or more
4. **Fallback Fonts**: Specify system fonts for when web fonts fail to load
5. **Print Support**: Define print styles with `@media print`

## Official References

For details, refer to official documentation:
- **Theme CSS**: https://marpit.marp.app/theme-css
- **Marpit API**: https://marpit-api.marp.app/
- **Official Theme Implementation**: https://github.com/marp-team/marp-core/tree/main/themes




### Marp Syntax

# Marp Basic Syntax Reference

Basic Marp syntax based on official documentation.

## Front Matter (Directives)

### Basic Structure

```markdown
---
marp: true
theme: default
paginate: true
---
```

### Main Global Directives

| Directive | Description | Example Values |
|-----------|-------------|----------------|
| `marp` | Enable Marp functionality | `true` |
| `theme` | Specify theme | `default`, `gaia`, `uncover` |
| `size` | Slide size (Marp Core extension) | `16:9`, `4:3`, `A4` |
| `paginate` | Show page numbers | `true`, `false` |
| `header` | Header for all slides | Any text |
| `footer` | Footer for all slides | Any text |
| `backgroundColor` | Background color | `#fff`, `white` |
| `backgroundImage` | Background image | `url('image.png')` |
| `color` | Text color | `#000`, `black` |
| `class` | Apply CSS class | `lead`, `invert` |

### Size Directive (Marp Core)

```markdown
---
size: 16:9
---
```

Available sizes:
- `16:9` (1280x720, default)
- `4:3` (960x720)
- `A4` (210mm x 297mm)

### Page-Specific Directives

To change settings per slide, use `<!-- directive_name: value -->` format:

```markdown
<!-- _class: lead -->
<!-- _backgroundColor: black -->
<!-- _color: white -->

# Apply only to this slide
```

**Meaning of underscore `_`**:
- Without `_`: Apply to all following slides
- With `_`: Apply to current slide only

## Slide Breaks

```markdown
---

# First Slide

---

# Next Slide

---
```

`---` (horizontal rule) switches to a new slide.

## Headers and Footers

### Global Settings

```markdown
---
header: 'Lecture Name'
footer: 'October 2024'
---
```

### Per-Slide Settings

```markdown
<!-- header: 'Section 1' -->
<!-- footer: 'Page number display' -->
```

### Disable

```markdown
<!-- header: '' -->
<!-- footer: '' -->
```

## Pagination (Page Numbers)

```markdown
---
paginate: true
---
```

Display position and style vary by theme.

Hide on specific slide:
```markdown
<!-- paginate: false -->
```

Or:
```markdown
<!-- _paginate: false -->
```

## Inline Styles

### Style Specification in Markdown

```markdown
---
marp: true
---

<style>
section {
  background-color: #f0f0f0;
}

h1 {
  color: #333;
}
</style>

# Slide
```

### Scoped Style

Apply style to specific slide only:

```markdown
<style scoped>
h1 {
  color: red;
}
</style>

# Red heading on this slide only
```

## Math Formulas (Marp Core Extension)

Supports Pandoc-style math formulas:

### Inline Math

```markdown
$E = mc^2$
```

### Block Math

```markdown
$$
\frac{-b \pm \sqrt{b^2 - 4ac}}{2a}
$$
```

## Emoji (Marp Core Extension)

```markdown
:smile: :+1: :sparkles:
```

Supports GitHub Emoji notation.

## Comments

HTML comments are not rendered:

```markdown
<!-- This is a comment -->
```

Directives are also written in comment format:

```markdown
<!-- _class: lead -->
```

## Official Reference Links

For details, refer to official documentation:

- **Directives List**: https://marpit.marp.app/directives
- **Marp Core Features**: https://github.com/marp-team/marp-core
- **Theme CSS Specification**: https://marpit.marp.app/theme-css
- **Official Site**: https://marp.app/

## Common Configuration Examples

### Basic Setup

```markdown
---
marp: true
theme: default
size: 16:9
paginate: true
---
```

### Title Slide

```markdown
---
marp: true
theme: default
---

<!-- _class: lead -->
<!-- _paginate: false -->

# Presentation Title

Presenter Name
```

### Section Breaks

```markdown
<!-- _class: lead -->

# Chapter 2

New Section

---

## Regular Slide
```

### Custom Background Color

```markdown
<!-- _backgroundColor: #e3f2fd -->

# Slide with Light Blue Background
```




### Theme Selection

# Marp Theme Selection Guide

Select the optimal theme from all 7 types according to your use case.

## Theme List and Use Cases

### 1. Default (theme-default.css / template-basic.md)
**Features**: Refined design, decorative lines, footer bar
**Colors**: Beige background + navy text + blue headings
**Use for**: General seminars, training, presentations
**Atmosphere**: Calm, elegant, sophisticated

### 2. Simple & Minimal (theme-minimal.css / template-minimal.md)
**Features**: Minimal decoration, wide margins, light fonts
**Colors**: White background + gray text + black headings
**Use for**: Content-focused presentations, academic talks, quiet impression
**Atmosphere**: Clean, simple, refined

### 3. Colorful & Pop (theme-colorful.css / template-colorful.md)
**Features**: Gradients, bright colors, bold emphasis
**Colors**: Pink background + multi-color accents
**Use for**: Youth-oriented, events, creative projects
**Atmosphere**: Fun, energetic, vibrant

### 4. Dark Mode (theme-dark.css / template-dark.md)
**Features**: Dark background, glow effects, eye-friendly
**Colors**: Black background + cyan/purple accents
**Use for**: Tech presentations, evening talks, modern impression
**Atmosphere**: Cool, modern, futuristic

### 5. Gradient Background (theme-gradient.css / template-gradient.md)
**Features**: Different gradient per slide, white text, shadow effects
**Colors**: Purple~pink~blue~green gradients
**Use for**: Visual-focused, creative presentations, impressive talks
**Atmosphere**: Vivid, dynamic, impressive

### 6. Tech/Code (theme-tech.css / template-tech.md)
**Features**: GitHub-style design, code fonts, Markdown-style symbols
**Colors**: Black background + blue/green accents
**Use for**: Programming courses, tech meetups, developer-focused
**Atmosphere**: Technical, developer-oriented, GitHub-style

### 7. Business-like (theme-business.css / template-business.md)
**Features**: Corporate presentation style, table support, top border
**Colors**: White background + dark navy headings + blue accents
**Use for**: Business presentations, proposals, reports
**Atmosphere**: Formal, trustworthy, professional

## Theme Selection Decision Flow

### Step 1: Filter by Use Case

**Technical/Developer-oriented** → Tech/Code
**Business/Corporate** → Business-like
**Creative/Event** → Colorful & Pop or Gradient Background
**Academic/Simple** → Simple & Minimal
**General/Unsure** → Default

### Step 2: Choose by Atmosphere

**Bright and fun** → Colorful & Pop, Gradient Background
**Calm and elegant** → Default, Business-like
**Cool and modern** → Dark Mode, Tech
**Simple and clean** → Simple & Minimal

### Step 3: Choose by Background Color

**Bright background**: Simple & Minimal, Default, Business-like, Colorful & Pop
**Dark background**: Dark Mode, Tech
**Gradient**: Gradient Background

## Inferring from User Requests

### "Make it look good" / "Make it cool"
→ Decide based on content:
- Business content: Business-like
- Technical content: Tech or Dark Mode
- General: Default
- Creative: Gradient Background

### "Keep it simple" / "Make it readable"
→ Simple & Minimal or Default

### "Make it flashy" / "Make it stand out"
→ Colorful & Pop or Gradient Background

### "Technical" / "Programming"
→ Tech/Code

### "Business" / "Corporate" / "Proposal"
→ Business-like

### "Dark" / "Black background" / "Eye-friendly"
→ Dark Mode or Tech

## When to Use Default Theme

Use the default theme in these cases:
- User hasn't specified a particular theme
- Use case is not clear
- Only vague instructions like "make it look good"
- Training/seminar purposes

## Suggesting Multiple Themes

Suggest 2-3 themes concisely in these cases:
- User asks "Which one is best?"
- Content allows for multiple options
- First-time user seems uncertain

Example suggestion:
"I recommend the following themes for this presentation:
1. **Business-like**: Formal impression
2. **Default**: Well-balanced all-purpose
Which would you prefer?"




### Official Themes

# Marp Official Themes Reference

Explanation of the 3 official themes included in Marp Core.

Official implementation: https://github.com/marp-team/marp-core/tree/main/themes

## Official Theme List

1. **default** - Simple and versatile theme
2. **gaia** - Modern and colorful theme
3. **uncover** - Minimal and elegant theme

## Default Theme

### Features

- **Colors**: White background, black text, blue accent
- **Font**: Simple sans-serif
- **Use for**: General presentations
- **Style**: Clean, readable

### Usage

```markdown
---
marp: true
theme: default
---

# Title

Content
```

### Available Classes

#### lead (Title Slide)

```markdown
<!-- _class: lead -->

# Presentation

Subtitle or description
```

Centered, large text.

#### invert (Inverted Colors)

```markdown
<!-- _class: invert -->

# Black Background · White Text
```

Background becomes black, text becomes white.

#### Combined

```markdown
<!-- _class: lead invert -->

# Inverted Title Slide
```

Multiple classes can be applied simultaneously.

### Customization Example

```markdown
---
theme: default
---

<style>
section {
  background-color: #f5f5f5;
}

h1 {
  color: #1e40af;
}
</style>
```

## Gaia Theme

### Features

- **Colors**: Colorful, vibrant accent colors
- **Font**: Modern sans-serif
- **Use for**: Creative presentations, design showcases
- **Style**: Energetic, visually appealing

### Usage

```markdown
---
marp: true
theme: gaia
---

# Title
```

### Color Variations

The Gaia theme has multiple color schemes:

```markdown
<!-- _class: lead -->
# Default Colors

---

<!-- _class: lead invert -->
# Inverted Colors

---

<!-- _class: lead gaia -->
# Gaia Colors
```

### Distinctive Styles

- **Gradient backgrounds**: Used in title slides
- **Colorful accents**: Headings and links
- **Large typography**: Impactful headings

### Customization Example

```markdown
---
theme: gaia
---

<style>
section {
  --color-background: #fff;
  --color-foreground: #333;
  --color-highlight: #e91e63;
}
</style>
```

## Uncover Theme

### Features

- **Colors**: Minimal, white or black base
- **Font**: Elegant serif font
- **Use for**: Formal presentations, academic talks
- **Style**: Refined, simple, elegant

### Usage

```markdown
---
marp: true
theme: uncover
---

# Title
```

### Available Classes

#### lead (Title Slide)

```markdown
<!-- _class: lead -->

# Presentation
```

Centered, large serif font.

#### invert (Inverted Colors)

```markdown
<!-- _class: invert -->

# Black Background Slide
```

Black background, white text.

### Distinctive Styles

- **Serif font**: Used for headings
- **Wide margins**: Minimal layout
- **Center alignment**: Content tends to be centered

### Customization Example

```markdown
---
theme: uncover
---

<style>
section {
  font-family: 'Georgia', serif;
  background: linear-gradient(135deg, #667eea 0%, #764ba2 100%);
  color: white;
}
</style>
```

## Theme Comparison Table

| Feature | default | gaia | uncover |
|---------|---------|------|---------|
| **Background** | White | Colorful | White/Black |
| **Font** | Sans-serif | Sans-serif | Serif |
| **Colors** | Simple | Vibrant | Minimal |
| **Use Case** | General | Creative | Formal |
| **Style** | Clean | Energetic | Elegant |

## Common Class Specifications

Available in all official themes:

### lead

```markdown
<!-- _class: lead -->
```

- For title slides
- Centered
- Large text
- Hides footer/page numbers

### invert

```markdown
<!-- _class: invert -->
```

- Inverts colors
- Swaps background and text colors
- Dark mode style

## Theme Selection Guidelines

### When to Choose default

- General presentations
- Business use
- Need simple, readable design
- As a base for customization

### When to Choose gaia

- Creative presentations
- Design-related talks
- Youth-oriented audiences
- Need visual impact

### When to Choose uncover

- Formal presentations
- Academic talks
- Minimal design preference
- Want elegant impression

## Combining with Custom Themes

### Extending Official Themes

```css
/* @theme my-custom-default */

@import-theme 'default';

section {
  background: linear-gradient(135deg, #f5f7fa 0%, #c3cfe2 100%);
}

h1 {
  color: #1e3a8a;
}
```

### Switching Between Multiple Themes

```markdown
---
marp: true
theme: default
---

# Section 1 (default theme)

---

<!-- theme: gaia -->

# Section 2 (gaia theme)

---

<!-- theme: uncover -->

# Section 3 (uncover theme)
```

Note: Theme switching within the same file has limited support.

## Practical Examples

### Using default Theme

```markdown
---
marp: true
theme: default
paginate: true
---

<!-- _class: lead -->

# Project Report

October 2024

---

## Agenda

1. Progress Status
2. Challenges and Solutions
3. Future Plans

---

## Progress Status

- Task A: Completed
- Task B: In Progress
- Task C: On Schedule
```

### Using gaia Theme

```markdown
---
marp: true
theme: gaia
---

<!-- _class: lead -->

# New Product Launch

Innovative Design

---

## Concept

**Three Pillars**

1. 🎨 Beauty
2. 🚀 Speed
3. 💡 Usability
```

### Using uncover Theme

```markdown
---
marp: true
theme: uncover
---

<!-- _class: lead -->

# Research Presentation

Deep Learning Applications

---

## Research Background

Recent technological advances...

---

<!-- _class: invert -->

## Experimental Results

Accuracy: 95.3%
```

## Official References

- **Official Theme Implementation**: https://github.com/marp-team/marp-core/tree/main/themes
- **Marp Core README**: https://github.com/marp-team/marp-core
- **Theme CSS Specification**: https://marpit.marp.app/theme-css




---

## 🚀 Usage

**Reference this template:** `@skill-marp-slide.md`


**Platform-specific:**
- **GitHub Copilot**: Add to `.github/copilot-instructions.md`
- **Augment Code**: Use `aug context add` command
- **Cursor/Windsurf**: Reference in chat with `@filename`
- **Claude**: Add to Project Knowledge
- **ChatGPT**: Add to Custom GPT configuration
