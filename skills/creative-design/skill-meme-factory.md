---
id: skill-meme-factory
type: skill
name: meme-factory
description: Generate memes using the memegen.link API. Use when users request memes,
  want to add humor to content, or need visual aids for social media. Supports 100+
  popular templates with custom text and styling.
category: creative-design
complexity: medium
keywords:
- api
- deploy
- github
- performance
- python
- test
capabilities: []
token_estimate: 1259
has_references: true
reference_count: 2
---

<!-- Converted from Claude Skill Template -->
<!-- Complexity: Medium -->
<!-- Estimated Tokens: ~1,259 -->
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




# meme-factory

> Generate memes using the memegen.link API. Use when users request memes, want to add humor to content, or need visual aids for social media. Supports 100+ popular templates with custom text and styling.

# Meme Factory

Create memes using the free memegen.link API and textual meme formats.

---

## Triggers

| Trigger | Description |
|---------|-------------|
| `/meme-factory` | Manual invocation |
| `/meme-factory {template} {top} {bottom}` | Direct meme generation |
| `meme-factory: create a meme about X` | Natural language request |

---

## Quick Reference

| Action | Format |
|--------|--------|
| Basic meme | `https://api.memegen.link/images/{template}/{top}/{bottom}.png` |
| With sizing | `?width=1200&height=630` |
| Custom background | `?style=https://example.com/image.jpg` |
| All templates | https://api.memegen.link/templates/ |
| Interactive docs | https://api.memegen.link/docs/ |

**Additional Resources:**
- [Markdown Memes Guide](references/markdown-memes-guide.md) - 15+ textual meme formats
- [Examples](references/examples.md) - Practical usage examples
- [meme_generator.py](scripts/meme_generator.py) - Python helper script

---

## Quick Start

### Basic Meme Structure

```
https://api.memegen.link/images/{template}/{top_text}/{bottom_text}.{extension}
```

**Example:**
```
https://api.memegen.link/images/buzz/memes/memes_everywhere.png
```

Result: Buzz Lightyear meme with "memes" at top and "memes everywhere" at bottom.

### Text Formatting

| Character | Encoding |
|-----------|----------|
| Space | `_` or `-` |
| Newline | `~n` |
| Question mark | `~q` |
| Percent | `~p` |
| Slash | `~s` |
| Hash | `~h` |
| Single quote | `''` |
| Double quote | `""` |

---

## Popular Templates

| Template | Use Case | Example |
|----------|----------|---------|
| `buzz` | X, X everywhere | bugs/bugs_everywhere |
| `drake` | Comparisons | manual_testing/automated_testing |
| `success` | Victories | deployed/no_errors |
| `fine` | Things going wrong | server_on_fire/this_is_fine |
| `fry` | Uncertainty | not_sure_if_bug/or_feature |
| `changemind` | Hot takes | tabs_are_better_than_spaces |
| `distracted` | Priorities | my_code/new_framework/current_project |
| `mordor` | One does not simply | one_does_not_simply/deploy_on_friday |

---

## Template Selection Guide

| Context | Template | Why |
|---------|----------|-----|
| Comparing options | `drake` | Two-panel reject/approve format |
| Celebrating wins | `success` | Positive outcome emphasis |
| Problems ignored | `fine` | Ironic "everything is fine" |
| Uncertainty | `fry` | "Not sure if X or Y" format |
| Controversial opinion | `changemind` | Statement + challenge |
| Ubiquitous things | `buzz` | "X, X everywhere" |
| Bad ideas | `mordor` | "One does not simply..." |

---

## Validation

After generating a meme:

- [ ] URL returns valid image (test in browser)
- [ ] Text is readable (not too long)
- [ ] Template matches the message context
- [ ] Special characters properly encoded
- [ ] Dimensions appropriate for platform

### Platform Dimensions

| Platform | Dimensions |
|----------|------------|
| Social media (Open Graph) | 1200x630 |
| Slack/Discord | 800x600 |
| GitHub | Default |

---

## Anti-Patterns

| Avoid | Why | Instead |
|-------|-----|---------|
| Spaces without encoding | URL breaks | Use `_` or `-` |
| Too much text | Unreadable | 2-6 words per line |
| Wrong template | Message mismatch | Match template to context |
| Missing extension | Invalid URL | Always include `.png`, `.jpg`, etc. |
| Unencoded special chars | URL breaks | Use `~q`, `~s`, `~p`, etc. |
| Assuming template exists | 404 error | Check templates list first |

---

## Verification

Meme generation is successful when:

1. **URL is valid** - Returns HTTP 200
2. **Image renders** - Displays correctly in markdown
3. **Text is visible** - Properly formatted on image
4. **Context matches** - Template fits the message

**Test command:**
```bash
curl -I "https://api.memegen.link/images/buzz/test/test.png"
# Should return: HTTP/2 200
```

---

<details>
<summary><strong>Deep Dive: Advanced Features</strong></summary>

### Image Formats

| Extension | Use Case |
|-----------|----------|
| `.png` | Best quality, default |
| `.jpg` | Smaller file size |
| `.webp` | Modern, good compression |
| `.gif` | Animated templates |

### Dimensions

```
?width=800
?height=600
?width=800&height=600  (padded to exact)
```

### Layout Options

```
?layout=top     # Text at top only
?layout=bottom  # Text at bottom only
?layout=default # Standard top/bottom
```

### Custom Fonts

View available: https://api.memegen.link/fonts/

```
?font=impact  (default)
```

### Custom Images

Use any image as background:

```
https://api.memegen.link/images/custom/hello/world.png?style=https://example.com/image.jpg
```

</details>

<details>
<summary><strong>Deep Dive: Contextual Memes</strong></summary>

### Code Reviews

```
Template: fry
https://api.memegen.link/images/fry/not_sure_if_feature/or_bug.png
```

### Deployments

```
Template: interesting
https://api.memegen.link/images/interesting/i_dont_always_test/but_when_i_do_i_do_it_in_production.png
```

### Documentation

```
Template: yodawg
https://api.memegen.link/images/yodawg/yo_dawg_i_heard_you_like_docs/so_i_documented_the_documentation.png
```

### Performance Issues

```
Template: fine
https://api.memegen.link/images/fine/memory_usage_at_99~/this_is_fine.png
```

### Successful Deploy

```
Template: success
https://api.memegen.link/images/success/deployed_to_production/zero_downtime.png
```

</details>

<details>
<summary><strong>Deep Dive: Workflow Integration</strong></summary>

### Generating Memes in Response

```markdown
Here's a relevant meme:

![Meme](https://api.memegen.link/images/buzz/bugs/bugs_everywhere.png)
```

### Dynamic Generation (Python)

```python
def generate_status_meme(status: str, message: str):
    template_map = {
        "success": "success",
        "failure": "fine",
        "review": "fry",
        "deploy": "interesting"
    }

    template = template_map.get(status, "buzz")
    words = message.split()
    top = "_".join(words[0:3])
    bottom = "_".join(words[3:6])

    return f"https://api.memegen.link/images/{template}/{top}/{bottom}.png"
```

### Using the Helper Script

```python
from meme_generator import MemeGenerator

meme = MemeGenerator()
url = meme.generate("buzz", "features", "features everywhere")
print(url)
```

</details>

<details>
<summary><strong>Deep Dive: API Reference</strong></summary>

### Endpoints

| Endpoint | Purpose |
|----------|---------|
| `/templates/` | List all templates |
| `/templates/{id}` | Template details |
| `/fonts/` | Available fonts |
| `/images/{template}/{top}/{bottom}.{ext}` | Generate meme |

### API Characteristics

- Free and open-source
- No API key required
- No rate limiting (normal use)
- Stateless (all info in URL)
- Images generated on-demand

### Error Handling

1. Check template at https://api.memegen.link/templates/
2. Verify text formatting (underscores for spaces)
3. Check special character encoding
4. Ensure valid extension
5. Test URL in browser

</details>

---

## References

| Document | Content |
|----------|---------|
| [markdown-memes-guide.md](references/markdown-memes-guide.md) | 15+ textual meme formats (greentext, copypasta, ASCII, etc.) |
| [examples.md](references/examples.md) | Practical usage examples |

### Scripts

| Script | Purpose |
|--------|---------|
| [meme_generator.py](scripts/meme_generator.py) | Python helper for meme generation |

---

## Summary

Generate contextual memes to:
- Add humor to conversations
- Create social media visuals
- Make code reviews engaging
- Celebrate successes

**Golden rule:** Keep text concise, match template to context.


---


## 📚 Reference Materials


### Markdown Memes Guide

# The Complete Practical Guide to Memes in Markdown Blog Posts

*A comprehensive toolbox for creating textual and image memes in Markdown-based blogs (MkDocs, Jekyll, Hugo, Astro, etc.)*

---

## 📖 Table of Contents

1. [Why Markdown for Memes?](#why-markdown-for-memes)
2. [Core Markdown Techniques](#core-markdown-techniques)
3. [Layout Patterns for Text Memes](#layout-patterns-for-text-memes)
4. [Textual Meme Formats](#textual-meme-formats)
5. [Image Memes via Pure URL](#image-memes-via-pure-url)
6. [Mixing Text + Image Memes](#mixing-text--image-memes)
7. [Advanced Features](#advanced-features)
8. [Production Tips](#production-tips)
9. [Complete Blog Example](#complete-blog-example)

---

## 🎯 Why Markdown for Memes?

Markdown is deceptively powerful for meme creation:

- **Preserves formatting** - Code blocks maintain spacing, ASCII art, and alignment
- **Readable source** - Plain text is portable and version-controllable
- **Native structure** - Blockquotes, emphasis, and spacing create rhythm naturally
- **No uploads needed** - Text memes live in your content, image memes via URL
- **Accessible** - Screen readers handle text better than image-text
- **Fast** - No external dependencies for textual memes

With a few tricks—code fences, blockquotes, emphasis, spacing—you can reproduce nearly any textual meme format inside a blog post.

---

## 🧱 Core Markdown Techniques

Master these building blocks first:

### ✅ Code Fences

Use triple backticks to preserve whitespace, arrows, emoji alignment, ASCII art, and raw text.

````markdown
```text
>be me
>perfect spacing preserved
>mfw markdown just works
```
````

**Use for:** Greentext, ASCII art, chat logs, anything needing exact spacing.

### ✅ Blockquotes

Use `>` for layered dialogue (Tumblr chains, ironic replies, greentext variants).

```markdown
> Person A: this is fine
>> Person B: no it's not
>>> Person C: *concern intensifies*
```

**Use for:** Tumblr chains, Twitter-style replies, nested irony.

### ✅ Hard Line Breaks

Two spaces at end of line or `<br>` for poetic timing.

```markdown
roses are red
markdown is neat
two trailing spaces
make formatting sweet
```

**Use for:** Poetry, dramatic pauses, controlled pacing.

### ✅ Horizontal Rules

Use `---` to reset comedic pacing or transition between bits.

```markdown
this joke is good

---

this joke is completely unrelated
```

**Use for:** Section breaks, punchline separation, tempo control.

### ✅ Emphasis

Use `*italics*` and `**bold**` to simulate tone, stage directions, or emotional beats.

```markdown
**me:** i'm fine
*narrator:* he was not fine
```

**Use for:** Stage directions, emphasis, emotional subtext.

---

## 🎨 Layout Patterns for Text Memes

### ✅ Choose a Meme Container

Each meme should have a clear "block" form for readability:

- **Code block** - Greentext, ASCII art, chat logs
- **Blockquote** - Tumblr chains, Twitter style
- **Heading + narrative** - Reddit AITA/TIFU, fake documentation
- **List** - Corporate satire, fake legal notices
- **Plain paragraph** - Emojicore, slang mutations

### ✅ Aim for Three Layers

Good textual memes read as:

1. **Setup** - Establish context
2. **Pattern / Rhythm** - Build expectation
3. **Punchline or twist** - Subvert or amplify

### ✅ Use Spacing as a Comedic Tool

Memes breathe through white space. Don't be afraid to isolate lines.

```markdown
this is fine

this is also fine

*nothing is fine*
```

---

## 📝 Textual Meme Formats

### 💚 1. Greentext

**Requirements:** Code fence with literal `>` characters

**How to do it:**

````markdown
```text
>be me
>writing markdown memes
>turn blog into 4chan but cleaner
>mfw it actually works
```
````

**Renders as:**

```text
>be me
>writing markdown memes
>turn blog into 4chan but cleaner
>mfw it actually works
```

**Tips:**

- Keep sentence fragments
- Use anticlimax for humor
- Monospace evokes "anon culture" immediately
- Works great for technical narratives

---

### 📦 2. Copypasta (Dramatic Walls of Text)

**Requirements:** Code fence for preservation

**How to do it:**

````markdown
```text
What the fuck did you just fucking say about Markdown, you little bitch?
I'll have you know I graduated top of my class in the Navy Seals of Documentation,
and I've been involved in numerous secret raids on WordPress blogs...
```
````

**Tips:**

- Let it be long - that's the point
- Overblown emotion is essential
- Preserve original formatting for authenticity
- Consider using collapsible sections for very long copypastas

---

### ✍️ 3. Shitpost Poetry & Micro-poems

**Requirements:** Hard line breaks (two spaces or `<br>`)

**How to do it:**

```markdown
roses are red
markdown is sly
press two spaces
to force a new line

violets are blue
syntax highlighting is lit
code fences preserve
every last bit
```

**Tips:**

- Lean into minimalism
- Add `<br>` if a theme strips trailing whitespace
- Break expectations with sudden philosophical turns
- Lowercase adds casual authenticity

---

### 🅰️ 4. ASCII Art & ASCII Storytelling

**Requirements:** Monospaced code fence

**How to do it:**

````markdown
```text
(╯°□°）╯︵ ┻━┻
┬─┬ノ( º _ ºノ)

    ___
   /   \
  | o o |  <- me reading documentation
   \___/
    |||
```
````

**Tips:**

- Use `text` or no language to avoid syntax coloring
- Test in multiple fonts
- Center using HTML `<div align="center">` if needed
- Keep it simple for broad compatibility

---

### 🌀 5. Surreal / Absurdist Text Memes

**Requirements:** Markdown + strategic spacing

**How to do it:**

```markdown
you awaken in the hallway of beans

the moon whispers: *not again*

---

**OPTION 1:** accept the beans
**OPTION 2:** become the beans

you choose Option 3

*there was no Option 3*
```

**Tips:**

- Use italics for dream-logic emphasis
- Insert `---` to create uncanny jumps
- Be playful with lowercase/capitalization
- Treat whitespace as tension

---

### 🗨️ 6. Tumblrisms (Multi-Speaker Chains)

**Requirements:** Nested blockquotes

**How to do it:**

```markdown
> **Person A:** frogs are neat
>> **Person B:** frogs are powerful
>>> **Person C:** I aspire to frog
>>>> **Person D:** this post is making me lose my mind
>>>>> **Person A:** good
```

**Tips:**

- Increase `>` depth to show escalation
- Add italics for emotional chaos
- Bold names for clarity
- Peak at 5 levels deep for readability

---

### 🐦 7. Twitter / X-style Micro-memes

**Requirements:** Blockquote or plain markdown

**How to do it:**

```markdown
> me: i'm productive
> also me: alphabetizes rocks by emotional energy

---

**me at 3am:** what if bread could read

**bread:** *softly* "help"
```

**Tips:**

- Keep it tight (2–4 lines)
- Use italics for "stage directions"
- Short exaggeration = comedy
- Em dash works great: "me: — also me:"

---

### 🧾 8. Reddit AITA / TIFU / Narrative Memes

**Requirements:** Markdown headings + paragraphs

**How to do it:**

```markdown
### TIFU by enabling Markdown features too much

**Context:** I work in documentation.

**What Happened:** Today I formatted a shopping list with H3 headings,
bullet points, and citation links. My family staged an intervention.

**Update:** They've hidden the Markdown guide. I'm formatting this from memory.

**AITA?**
```

**Tips:**

- Use headings for "post titles"
- Sincere tone + mild absurdity = gold
- Include typical Reddit formatting (bold labels)
- The more mundane the subject, the funnier

---

### 🗿 9. Wojak-style Dialogues (Text-Only)

**Requirements:** Bold names + minimal dialogue

**How to do it:**

```markdown
**Doomer:** nothing matters
**Zoomer:** drink water bro
**Wojak:** *internal screaming*
**Chad:** have you tried not caring
**Doomer:** ...wait that's actually helpful
**Chad:** i know
```

**Tips:**

- Keep dialogue short
- Rely on archetypes for instant recognition
- Tone = half-honest, half-ironic
- Works great in code blocks too

---

### 💬 10. Discord / Chat Log Memes

**Requirements:** Code fence with timestamp format

**How to do it:**

````markdown
```text
[12:41] user123: you up?
[12:42] system: error: feelings.exe not found
[12:42] user123: same
[12:43] bot: did you try turning your emotions off and on again?
[12:44] user123: yes
[12:44] system: critical error: emotions.dll missing since 2019
```
````

**Tips:**

- Timestamps add realism
- Use usernames to amplify the joke
- System messages = comedic gold
- Maintain deadpan delivery

---

### 😀 11. Emojicore Memes

**Requirements:** Just good spacing

**How to do it:**

```markdown
🚶💨 leaving my responsibilities

😔👉👈 wondering if coffee counts as a personality

🎯 hitting the target (the target is rock bottom)

✨ manifesting ✨ (chaos)
```

**Tips:**

- Use emojis as syntax, not decoration
- Vertical stacking works great
- Pair emoji with understated text
- Less is more

---

### 📚 12. Fake Wiki / Manual Pages

**Requirements:** Markdown headings + bold labels

**How to do it:**

```markdown
## Bread.exe

**Category:** Deprecated Carbohydrate
**Status:** Unstable
**Introduced:** ~8000 BCE
**Last Update:** Never

### Known Issues

- Becomes stale without manual intervention
- Incompatible with lactose-intolerant systems
- Memory leak when toasted incorrectly

### Workarounds

See: `butter.dll` documentation
```

**Tips:**

- Keep the tone pseudo-academic
- Use definition-style formatting
- Technical jargon for mundane objects = peak comedy
- Cross-reference other fake docs

---

### 🐕 13. Slang-Mutation Text Memes

**Requirements:** Markdown paragraphs with stylized orthography

**How to do it:**

```markdown
how 2 markdown:

1. make text smol
2. add sparklez ✨
3. u done it

**congrations** u did a format

no take backsies
```

**Tips:**

- Lean into childlike spelling
- Use emoji to exaggerate tone
- Mix formal structure (lists) with informal language
- "Congrations" beats "congratulations" every time

---

### 👶 14. ELI5 But Wrong

**Requirements:** Heading + confident incorrect explanation

**How to do it:**

```markdown
### ELI5: Why do volcanoes erupt?

Volcanoes are mountains that get too excited and sneeze the earth.
The rocks fly out because they want to be birds but forgot how.
This is called "geology."

**Follow-up:** Why is lava hot?

Because it's embarrassed about the whole situation.
```

**Tips:**

- Confidence + incorrectness = humor
- Keep explanation "plausible" sounding
- Use actual ELI5 structure for authenticity
- Works great for tech concepts too

---

### 📄 15. Corporate / Legalese Satire

**Requirements:** Lists + bold labels

**How to do it:**

```markdown
## Notice of Emotional Noncompliance

**Issued to:** You
**Date:** Whenever
**Reason:** Your vibe is irregular

### Required Actions

- [ ] Submit Form 42-B "Vibe Correction Protocol" within 5 business days
- [ ] Attend mandatory "Feeling Feelings Appropriately" training
- [ ] Provide written documentation of three (3) genuine smiles

**Failure to comply will result in:**

1. Passive-aggressive Slack messages
2. A pizza party (no pizza will be provided)
3. Being asked to "circle back" indefinitely

---

*This notice has been automatically generated by the Department of Vibes.*
```

**Tips:**

- Official tone for trivial things
- Short, concise bullet points increase contrast
- Use checkboxes for "action items"
- Footer disclaimers are pure gold

---

## 🖼️ Image Memes via Pure URL

Sometimes you want a visual punchline next to your textual meme. With **memegen.link**, you can generate classic image memes just by crafting a URL—no uploads, no editors. Then embed it in your Markdown with standard image syntax.

### 🚀 Quick Start

```markdown
![Drake meme about Markdown](https://api.memegen.link/images/drake/using_word_art/using_markdown.png)
```

That's it—your post renders the Drake format with top "using word art" and bottom "using markdown".

---

### 🔧 URL Anatomy

```
https://api.memegen.link/images/{template}/{top_text}/{bottom_text}.{ext}?{options}
```

**Components:**

- `template`: meme name (e.g., `drake`, `two-buttons`, `distracted-boyfriend`)
- `top_text` / `bottom_text`: your captions
- `ext`: `png` (default), `jpg`, or `webp` (good for lighter pages)
- `options`: query params (e.g., `font`, `width`, `watermark`)

**Example:**

```markdown
![Top/Bottom meme](https://api.memegen.link/images/two-buttons/write_tests/document_everything.png)
```

---

### 📝 Text Encoding Cheatsheet

Memegen uses compact encoding so URLs stay readable:

| Character | Encoding | Example |
|-----------|----------|---------|
| Space | `_` | `hello world` → `hello_world` |
| Hyphen | `--` | `foo-bar` → `foo--bar` |
| Underscore | `__` | `foo_bar` → `foo__bar` |
| Question mark | `~q` | `why?` → `why~q` |
| Percent | `~p` | `50%` → `50~p` |
| Hash | `~h` | `#tag` → `~htag` |
| Slash (literal) | `~s` | `foo/bar` → `foo~sbar` |
| Quotes | `''` | `he said "hi"` → `he_said_''hi''` |

**Line breaks:** Use `%0A` (URL-encoded newline) inside a caption.

**Example:**
```
top_line%0Asecond_line
```

---

### ⚙️ Common Options (Query String)

Append with `?` after the file extension:

```markdown
?font=impact               # Default is already impact; try notosans
?width=600&height=600      # Size control; height optional
?watermark=none            # Remove tiny watermark
?background=URL            # Custom template; URL-encode it
```

**Example:**

```markdown
![Clean meme](https://api.memegen.link/images/drake/write_specs/rely_on_vibes.webp?width=640&watermark=none)
```

---

### 🎭 Popular Templates

Quick reference for common meme formats:

| Template | Use Case |
|----------|----------|
| `drake` | Rejecting one thing, approving another |
| `two-buttons` | Difficult choice between two options |
| `distracted-boyfriend` | Being distracted by something new |
| `gru-plan` | Plan that goes wrong at the end |
| `change-my-mind` | Stating an opinion confidently |
| `mocking-spongebob` | Mocking someone's statement |
| `is-this-a-pigeon` | Misidentifying something obvious |
| `surprised-pikachu` | Shocked by predictable outcome |
| `success-kid` | Celebrating small victories |
| `uno-draw-25` | Refusing to do something even if costly |
| `custom` | Use with `?background=` for any image |

---

### 🎯 Blank Sides & One-Sided Captions

Leave a side blank with `_` (single underscore).

**Top only:**

```markdown
![Top-only](https://api.memegen.link/images/change-my-mind/markdown_is_a_meme_engine/_.png)
```

**Bottom only:**

```markdown
![Bottom-only](https://api.memegen.link/images/success-kid/_/finally_fixed_the_build.png)
```

---

### 📏 Multiline Captions

Use `%0A` for line breaks inside a side:

```markdown
![Multiline](https://api.memegen.link/images/gru-plan/plan_the_feature%0Adeliver_the_feature/forget_the_docs%0Awrite_them_later.png)
```

---

### 🎨 Custom Backgrounds (Brand or Screenshot)

Use the `custom` template + `background` parameter. The background must be publicly reachable.

```markdown
![Custom background meme](https://api.memegen.link/images/custom/top_text/bottom_text.png?background=https%3A%2F%2Fexample.com%2Fimage.png&watermark=none)
```

**Tip:** Pair a screenshot of your app/graph as background to comment on it memetically in the post.

---

### ♿ Accessibility & SEO

**Always provide descriptive alt text:**

```markdown
![Drake rejecting "manual edits", approving "Markdown memegen URLs"](https://api.memegen.link/images/drake/manual_edits/markdown_memegen_urls.png?watermark=none)
```

Screen readers will read your alt text, making the joke accessible to everyone.

---

### 📦 Quick Copy-Paste Recipes

**Drake (top/bottom):**
```markdown
![Drake](https://api.memegen.link/images/drake/write_docs/write_memes.png?watermark=none)
```

**Two Buttons (multiline with %0A):**
```markdown
![Two buttons](https://api.memegen.link/images/two-buttons/fix_the_build%0A_now_/add_more_features%0A_today_.png)
```

**Change My Mind (top only):**
```markdown
![Change my mind](https://api.memegen.link/images/change-my-mind/markdown_is_a_design_tool/_.png)
```

**Surprised Pikachu (bottom only):**
```markdown
![Pikachu](https://api.memegen.link/images/surprised-pikachu/_/forgot_to_escape_slash~sagain.png)
```

**Custom background (brand screenshot):**
```markdown
![Custom](https://api.memegen.link/images/custom/ship_it/friday_release.webp?background=https%3A%2F%2Fyour.cdn%2Fapp_screenshot.png&width=720&watermark=none)
```

---

## 🎭 Mixing Text + Image Memes

Blend text-only formats and image memes to pace a post effectively.

### ✅ Rhythm Pattern

**1. Start with text** to set up context:

````markdown
### The Day I Realized Markdown Is Too Powerful

```text
>be me
>ship blog revamp
>marketing wants "fun"
>accidentally create meme culture
```
````

**2. Follow with a visual** to amplify the punchline:

```markdown
![Two buttons](https://api.memegen.link/images/two-buttons/make_it_fun/make_it_accessible.png)
```

**3. Close with another text format** for resolution:

```markdown
**Status:** fun achieved
**Risk:** puns leaked to production
**Next Steps:** embrace chaos
```

### ✅ Strategic Placement

**Good patterns:**

- Greentext → Image → Corporate satire
- Tumblr chain → ASCII art → Image
- Chat log → Image → Shitpost poetry

**Avoid:**

- 5 images in a row (visual fatigue)
- 3 long copypastas back-to-back (reader exhaustion)
- Image without context (confusing)

### ✅ Complementary Pairs

Match text and image memes thematically:

| Text Format | Image Format | Why It Works |
|-------------|--------------|--------------|
| Greentext | Drake | Both are format-specific classics |
| Corporate satire | Two buttons | Amplifies "decision paralysis" theme |
| Chat log | Surprised Pikachu | Both capture reactions |
| Reddit AITA | Distracted boyfriend | Narrative + visual work together |
| Wojak dialogue | Custom background | Text provides context for visual |

---

## 🔥 Advanced Features

### 🎨 Custom CSS for Greentext (MkDocs / Hugo)

Add custom styling for greentext blocks:

```css
.greentext {
  color: #789922;
  font-family: 'Courier New', monospace;
  background-color: #f0f0f0;
  padding: 1rem;
  border-left: 4px solid #789922;
}
```

Then wrap greentext in HTML:

```html
<div class="greentext">

>be you
>styling markdown like a pro
>mfw it actually looks good

</div>
```

---

### 📢 Admonitions (MkDocs Material, Docusaurus)

Use admonition blocks for "official" memes or mock disclaimers:

```markdown
!!! warning "System Alert"
    Your snacks have been revoked pending performance review.

!!! danger "Critical Error"
    `feelings.exe` has stopped responding.
    Would you like to send an error report? [Yes] [Yes]
```

---

### 🗂️ Collapsible Sections

Great for long copypastas and Tumblr chains:

```markdown
<details>
<summary>Click to expand the legendary copypasta</summary>

What the fuck did you just fucking say about Markdown...
(rest of copypasta)

</details>
```

---

### 🎯 Tabs (Docusaurus, MkDocs)

Show multiple meme variations side-by-side:

```markdown
=== "Greentext"

    ```text
    >be me
    >tabs are cool
    ```

=== "Corporate"

    **Notice:** Tabs have been deemed acceptable for meme deployment.

=== "Image"

    ![Drake](https://api.memegen.link/images/drake/regular_text/tabs.png)
```

---

## 🚀 Production Tips

### ⚡ Performance

**For text memes:**
- Already optimal (it's just text!)
- No external dependencies
- Fast rendering

**For image memes:**
- Prefer `webp` format for 30-50% smaller files
- Set consistent `?width=640` for predictable layout
- Consider lazy loading: `loading="lazy"` on `<img>` tags
- Cache memegen.link URLs (they're stable)

### ♿ Accessibility

**Text memes:**
- Already screen-reader friendly
- Use semantic HTML where appropriate
- Avoid ASCII art for critical information

**Image memes:**
- Always provide descriptive alt text
- Describe the joke, not just "meme image"
- Good: `![Drake rejecting documentation, approving memes]`
- Bad: `![funny meme]`

### 🔍 SEO

**Text memes:**
- Search engines index text content
- Use semantic HTML for structure
- Don't hide important information in ASCII art

**Image memes:**
- Alt text provides indexable content
- Descriptive filenames help (though memegen URLs are generated)
- Consider adding captions below images for context

---

## 📚 Complete Blog Example

Here's a full blog post example mixing multiple meme formats:

```markdown
---
title: "The Deployment Chronicles"
date: 2025-01-11
tags: [devops, humor, deployments]
---

# The Deployment Chronicles

## Pre-Deployment Anxiety

```text
>be me
>friday afternoon
>manager: "quick deploy?"
>mfw "quick" and "deploy" in same sentence
```

**Narrator:** *It was not quick.*

---

## The Planning Phase

### Decision Matrix

![Two Buttons](https://api.memegen.link/images/two-buttons/test_thoroughly/deploy_on_friday.png?watermark=none)

**Status:** We chose poorly.

---

## During Deployment

```text
[15:42] devops_bot: deployment started
[15:43] engineer1: looks good
[15:44] engineer2: why is prod slow
[15:45] system: ERROR: connection timeout
[15:46] engineer1: this is fine
[15:47] system: CRITICAL: database locked
[15:48] engineer1: THIS IS NOT FINE
```

![This is Fine](https://api.memegen.link/images/fine/production_is_down/this_is_fine.png)

---

## Post-Mortem

### Incident Report #2847

**Issued to:** Engineering Team
**Date:** 2025-01-11 18:30 UTC
**Reason:** Premature optimization of deployment strategy

#### Required Actions

- [ ] Never deploy on Friday again
- [ ] Actually read the deployment checklist
- [ ] Stop trusting "it works on my machine"

**Consequences:**

1. Mandatory attendance at "Why Friday Deploys Are Bad" training
2. Team pizza party (cancelled due to incident)
3. Existential dread

---

*This incident report has been automatically generated by the Department of Preventable Disasters.*

---

## Lessons Learned

**me:** let's deploy carefully next time
*also me next Friday:* what could go wrong

---

## Success Metrics

After fixing everything:

![Success Kid](https://api.memegen.link/images/success/deployed_successfully/only_took_4_hours.png?watermark=none)

🎯 hitting targets (the target was "don't break prod permanently")
✨ manifesting ✨ (working deployments)
😅 lessons learned (until next time)

---

## ELI5: What is a deployment?

A deployment is when you tell the computer to use your new code.
Sometimes the computer listens.
Sometimes the computer decides chaos is more fun.
This is called "DevOps."

**Follow-up:** Why do deployments fail?

Because the computer has feelings and Friday afternoon hurts those feelings.

---

## Conclusion

roses are red
deploys cause pain
test in production
again and again

---

*Want more deployment horror stories? Subscribe to our RSS feed of suffering.*
```

---

## 🎓 Summary

This guide covered:

1. **Why Markdown** - Preserves formatting, accessible, version-controllable
2. **Core techniques** - Code fences, blockquotes, spacing, emphasis
3. **15 textual formats** - From greentext to corporate satire
4. **Image memes** - Using memegen.link URLs
5. **Mixing formats** - Strategic pacing and rhythm
6. **Advanced features** - CSS, admonitions, tabs
7. **Production tips** - Performance, accessibility, SEO
8. **Complete example** - Real-world blog post

**Key takeaways:**

- Text memes are fast, accessible, and version-controllable
- Image memes via URL require no uploads or storage
- Mix formats strategically for comedic pacing
- Always provide alt text for accessibility
- Use spacing and whitespace as a comedic tool
- Keep text concise for maximum impact

**Resources:**

- Memegen API: https://api.memegen.link/docs/
- Templates: https://api.memegen.link/templates/
- MkDocs Material: https://squidfunk.github.io/mkdocs-material/

---

*This guide is a living document. Contributions welcome.*




### Examples

# Meme Generation Examples

This document provides practical examples of using the meme generation skill in various contexts.

## Basic Examples

### Example 1: Simple Meme

**Request:**
"Create a meme about bugs everywhere"

**Response:**
```
Here's a Buzz Lightyear meme about bugs:

![Bugs Everywhere](https://api.memegen.link/images/buzz/bugs/bugs_everywhere.png)
```

### Example 2: Drake Comparison Meme

**Request:**
"Make a Drake meme comparing manual testing vs automated testing"

**Response:**
```
Here's a Drake meme comparing testing approaches:

![Testing Comparison](https://api.memegen.link/images/drake/manual_testing/automated_testing.png)
```

### Example 3: Success Meme

**Request:**
"Generate a success kid meme about all tests passing"

**Response:**
```
Here's a success meme:

![Tests Passing](https://api.memegen.link/images/success/all_tests_passing/on_the_first_try.png)
```

## Programming Examples

### Python Script Example

```python
from meme_generator import MemeGenerator

# Initialize generator
meme = MemeGenerator()

# Generate a basic meme
url = meme.generate(
    template="buzz",
    top_text="features",
    bottom_text="features everywhere"
)
print(f"Meme URL: {url}")

# Generate with custom dimensions for social media
url = meme.generate(
    template="drake",
    top_text="writing tests later",
    bottom_text="writing tests first",
    width=1200,
    height=630
)
print(f"Social media meme: {url}")

# Get markdown for embedding
markdown = meme.get_markdown_image(url, alt_text="TDD Meme")
print(f"Markdown: {markdown}")
```

### CLI Example

```bash
# Generate a basic meme
python meme_generator.py generate buzz "features" "features everywhere"

# Generate with markdown output
python meme_generator.py generate success "deployed" "no errors" --markdown

# Generate with custom dimensions
python meme_generator.py generate drake "old way" "new way" --width 1200 --height 630

# List all available templates
python meme_generator.py list-templates

# Suggest template for context
python meme_generator.py suggest "deployment success"
```

## Context-Specific Examples

### Code Review Context

**Scenario:** Reviewing pull request with many changes

```python
# Not sure if improvements or over-engineering
url = meme.generate("fry", "not sure if improvements", "or over engineering")
```

**Result:**
```
https://api.memegen.link/images/fry/not_sure_if_improvements/or_over_engineering.png
```

### Deployment Context

**Scenario:** Successful production deployment

```python
# Success kid meme
url = meme.generate("success", "deployed to production", "zero downtime")
```

**Result:**
```
https://api.memegen.link/images/success/deployed_to_production/zero_downtime.png
```

### Incident Response Context

**Scenario:** Production fire

```python
# This is fine meme
url = meme.generate("fine", "production is down", "this is fine")
```

**Result:**
```
https://api.memegen.link/images/fine/production_is_down/this_is_fine.png
```

### Documentation Context

**Scenario:** Documentation improvements

```python
# Yo dawg meme
url = meme.generate(
    "yodawg",
    "yo dawg i heard you like docs",
    "so i documented the documentation"
)
```

**Result:**
```
https://api.memegen.link/images/yodawg/yo_dawg_i_heard_you_like_docs/so_i_documented_the_documentation.png
```

## Advanced Examples

### Custom Dimensions for Social Media

**Open Graph (1200x630)**

```python
url = meme.generate(
    "buzz",
    "features",
    "features everywhere",
    width=1200,
    height=630
)
```

### Custom Layout

**Top-only text**

```python
url = meme.generate(
    "rollsafe",
    "cant have bugs in production",
    layout="top"
)
```

### Multiple Format Generation

```python
formats = ["png", "jpg", "webp"]
for fmt in formats:
    url = meme.generate(
        "success",
        "all tests passing",
        "first try",
        extension=fmt
    )
    print(f"{fmt.upper()}: {url}")
```

## Integration Examples

### Slack Bot Integration

```python
def send_deployment_meme(channel: str, status: str):
    """Send a meme to Slack based on deployment status."""
    meme = MemeGenerator()

    if status == "success":
        url = meme.generate("success", "deployed", "no errors")
        message = "Deployment successful!"
    elif status == "failure":
        url = meme.generate("fine", "deployment failed", "this is fine")
        message = "Deployment needs attention"
    else:
        url = meme.generate("fry", "not sure if deployed", "or still deploying")
        message = "Deployment status unclear"

    # Send to Slack (pseudo-code)
    slack_client.chat_postMessage(
        channel=channel,
        text=message,
        attachments=[{"image_url": url}]
    )
```

### GitHub PR Comments

```python
def comment_pr_review(pr_number: int, review_type: str):
    """Add a meme to PR comments based on review."""
    meme = MemeGenerator()

    review_memes = {
        "approved": meme.generate("success", "lgtm", "ship it"),
        "changes_requested": meme.generate("yuno", "y u no", "write tests"),
        "complex": meme.generate("fry", "not sure if feature", "or bug")
    }

    url = review_memes.get(review_type)
    markdown = meme.get_markdown_image(url, alt_text="Code Review Meme")

    # Post comment (pseudo-code)
    github_client.issues.create_comment(
        pr_number,
        f"Code review complete!\n\n{markdown}"
    )
```

### Discord Bot Integration

```python
@bot.command()
async def deploy_meme(ctx, status: str):
    """Generate deployment meme for Discord."""
    meme = MemeGenerator()

    if status == "success":
        url = meme.generate("success", "deployed", "zero downtime")
    elif status == "fail":
        url = meme.generate("fine", "servers on fire", "this is fine")

    await ctx.send(f"Deployment Status: {status}")
    await ctx.send(url)
```

## Batch Generation Examples

### Generate Multiple Memes for a Topic

```python
def generate_testing_memes():
    """Generate a collection of testing-related memes."""
    meme = MemeGenerator()

    memes = [
        meme.generate("drake", "manual testing", "automated testing"),
        meme.generate("success", "all tests passing", "on first try"),
        meme.generate("fry", "not sure if bug", "or feature"),
        meme.generate("interesting", "i dont always test", "but when i do i test in production"),
    ]

    print("Testing Meme Collection:\n")
    for i, url in enumerate(memes, 1):
        print(f"{i}. {url}")

generate_testing_memes()
```

### Generate Memes for Documentation

```python
def generate_docs_memes():
    """Generate documentation-related memes."""
    meme = MemeGenerator()

    topics = [
        ("yodawg", "yo dawg i heard you like docs", "so i documented your docs"),
        ("buzz", "documentation", "documentation everywhere"),
        ("wonka", "oh you write documentation", "tell me more about this fantasy"),
        ("ancient", "documentation", "documentation"),
    ]

    for template, top, bottom in topics:
        url = meme.generate(template, top, bottom)
        markdown = meme.get_markdown_image(url)
        print(markdown)

generate_docs_memes()
```

## Error Handling Examples

### Check Template Validity

```python
def generate_safe_meme(template: str, top: str, bottom: str):
    """Generate meme with error handling."""
    meme = MemeGenerator()

    # Check if template exists
    if template not in meme.TEMPLATES:
        print(f"Warning: '{template}' not in known templates")
        print(f"Falling back to 'buzz'")
        template = "buzz"

    url = meme.generate(template, top, bottom)
    return url
```

### Context-Based Template Selection

```python
def smart_meme_generation(context: str, top: str, bottom: str):
    """Generate meme with automatic template selection."""
    meme = MemeGenerator()

    # Suggest template based on context
    suggested_template = meme.suggest_template_for_context(context)

    print(f"Context: {context}")
    print(f"Suggested template: {suggested_template}")

    url = meme.generate(suggested_template, top, bottom)
    return url

# Examples
url1 = smart_meme_generation("deployment success", "deployed", "no errors")
url2 = smart_meme_generation("debugging nightmare", "bugs", "bugs everywhere")
url3 = smart_meme_generation("code comparison", "old code", "new code")
```

## Tips for Effective Memes

### Good Examples

```python
# ✅ Concise text
meme.generate("buzz", "bugs", "bugs everywhere")

# ✅ Relevant template
meme.generate("drake", "manual work", "automation")

# ✅ Clear message
meme.generate("success", "deployed", "zero errors")
```

### Bad Examples

```python
# ❌ Too much text
meme.generate(
    "buzz",
    "there are way too many bugs in this codebase",
    "seriously there are bugs absolutely everywhere i look"
)

# ❌ Wrong template choice
meme.generate("success", "production is down", "servers crashed")  # Should use 'fine'

# ❌ Unclear message
meme.generate("buzz", "stuff", "things")
```

## Testing Your Memes

```python
def test_meme_generation():
    """Test meme generation with various inputs."""
    meme = MemeGenerator()

    test_cases = [
        ("buzz", "test", "test everywhere", "png"),
        ("drake", "bugs", "features", "jpg"),
        ("success", "deployed", "working", "webp"),
    ]

    print("Testing meme generation:\n")
    for template, top, bottom, ext in test_cases:
        url = meme.generate(template, top, bottom, extension=ext)
        print(f"✓ {template} ({ext}): {url}")

test_meme_generation()
```

## Real-World Workflow Example

```python
def deployment_workflow_with_memes(deployment_result: dict):
    """Complete deployment workflow with contextual memes."""
    meme = MemeGenerator()

    # Pre-deployment
    pre_deploy = meme.generate("buzz", "deployments", "deployments incoming")
    print(f"Pre-deployment: {pre_deploy}")

    # During deployment
    if deployment_result["status"] == "in_progress":
        during = meme.generate("fry", "not sure if deploying", "or already deployed")
        print(f"During: {during}")

    # Post-deployment
    if deployment_result["success"]:
        post = meme.generate(
            "success",
            "deployed to production",
            "zero downtime",
            width=1200,
            height=630
        )
        slack_message = f"Deployment successful! {post}"
    else:
        post = meme.generate("fine", "deployment failed", "this is fine")
        slack_message = f"Deployment needs attention: {post}"

    print(f"Post-deployment: {slack_message}")

# Example usage
deployment_workflow_with_memes({
    "status": "completed",
    "success": True,
    "duration": "5m 32s"
})
```

## Summary

These examples demonstrate:
- Basic meme generation
- Context-aware template selection
- Integration with popular platforms
- Error handling and validation
- Batch generation workflows
- Real-world use cases

For more examples and templates, visit:
- https://api.memegen.link/docs/
- https://api.memegen.link/templates/




---

## 🚀 Usage

**Reference this template:** `@skill-meme-factory.md`


**Platform-specific:**
- **GitHub Copilot**: Add to `.github/copilot-instructions.md`
- **Augment Code**: Use `aug context add` command
- **Cursor/Windsurf**: Reference in chat with `@filename`
- **Claude**: Add to Project Knowledge
- **ChatGPT**: Add to Custom GPT configuration
