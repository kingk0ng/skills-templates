---
id: skill-crafting-effective-readmes
type: skill
name: crafting-effective-readmes
description: Use when writing or improving README files. Not all READMEs are the same
  — provides templates and guidance matched to your audience and project type.
category: productivity
complexity: medium
keywords:
- go
capabilities: []
token_estimate: 491
has_references: true
reference_count: 5
---

<!-- Converted from Claude Skill Template -->
<!-- Complexity: Medium -->
<!-- Estimated Tokens: ~491 -->
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




# crafting-effective-readmes

> Use when writing or improving README files. Not all READMEs are the same — provides templates and guidance matched to your audience and project type.

# Crafting Effective READMEs

## Overview

READMEs answer questions your audience will have. Different audiences need different information - a contributor to an OSS project needs different context than future-you opening a config folder.

**Always ask:** Who will read this, and what do they need to know?

## Process

### Step 1: Identify the Task

**Ask:** "What README task are you working on?"

| Task | When |
|------|------|
| **Creating** | New project, no README yet |
| **Adding** | Need to document something new |
| **Updating** | Capabilities changed, content is stale |
| **Reviewing** | Checking if README is still accurate |

### Step 2: Task-Specific Questions

**Creating initial README:**
1. What type of project? (see Project Types below)
2. What problem does this solve in one sentence?
3. What's the quickest path to "it works"?
4. Anything notable to highlight?

**Adding a section:**
1. What needs documenting?
2. Where should it go in the existing structure?
3. Who needs this info most?

**Updating existing content:**
1. What changed?
2. Read current README, identify stale sections
3. Propose specific edits

**Reviewing/refreshing:**
1. Read current README
2. Check against actual project state (package.json, main files, etc.)
3. Flag outdated sections
4. Update "Last reviewed" date if present

### Step 3: Always Ask

After drafting, ask: **"Anything else to highlight or include that I might have missed?"**

## Project Types

| Type | Audience | Key Sections | Template |
|------|----------|--------------|----------|
| **Open Source** | Contributors, users worldwide | Install, Usage, Contributing, License | `templates/oss.md` |
| **Personal** | Future you, portfolio viewers | What it does, Tech stack, Learnings | `templates/personal.md` |
| **Internal** | Teammates, new hires | Setup, Architecture, Runbooks | `templates/internal.md` |
| **Config** | Future you (confused) | What's here, Why, How to extend, Gotchas | `templates/xdg-config.md` |

**Ask the user** if unclear. Don't assume OSS defaults for everything.

## Essential Sections (All Types)

Every README needs at minimum:

1. **Name** - Self-explanatory title
2. **Description** - What + why in 1-2 sentences  
3. **Usage** - How to use it (examples help)

## References

- `section-checklist.md` - Which sections to include by project type
- `style-guide.md` - Common README mistakes and prose guidance
- `using-references.md` - Guide to deeper reference materials


---


## 📚 Reference Materials


### Make A Readme

# Make a README

> Source: [makeareadme.com](https://www.makeareadme.com) by Danny Guo
> 
> "Because no one can read your mind (yet)"

## README 101

### What is it?

A README is a text file that introduces and explains a project. It contains information that is commonly required to understand what the project is about.

### Why should I make it?

It's an easy way to answer questions that your audience will likely have regarding how to install and use your project and also how to collaborate with you.

### Who should make it?

Anyone who is working on a programming project, especially if you want others to use it or contribute.

### When should I make it?

Definitely before you show a project to other people or make it public. You might want to get into the habit of making it the first file you create in a new project.

### Where should I put it?

In the top level directory of the project. This is where someone who is new to your project will start out. Code hosting services such as GitHub, Bitbucket, and GitLab will also look for your README and display it along with the list of files and directories in your project.

### How should I make it?

While READMEs can be written in any text file format, the most common one that is used nowadays is Markdown. It allows you to add some lightweight formatting. You can learn more about it at the [CommonMark website](https://commonmark.org/).

## Suggestions for a Good README

Every project is different, so consider which of these sections apply to yours. Also keep in mind that while a README can be too long and detailed, **too long is better than too short**. If you think your README is too long, consider utilizing another form of documentation rather than cutting out information.

### Name

Choose a self-explaining name for your project.

### Description

Let people know what your project can do specifically. Provide context and add a link to any reference visitors might be unfamiliar with. A list of **Features** or a **Background** subsection can also be added here. If there are alternatives to your project, this is a good place to list differentiating factors.

### Badges

On some READMEs, you may see small images that convey metadata, such as whether or not all the tests are passing for the project. You can use [Shields.io](http://shields.io/) to add some to your README. Many services also have instructions for adding a badge.

### Visuals

Depending on what you are making, it can be a good idea to include screenshots or even a video (you'll frequently see GIFs rather than actual videos). Tools like [ttygif](https://github.com/icholy/ttygif) can help, but check out [Asciinema](https://asciinema.org/) for a more sophisticated method.

### Installation

Within a particular ecosystem, there may be a common way of installing things, such as using Yarn, NuGet, or Homebrew. However, consider the possibility that whoever is reading your README is a novice and would like more guidance. Listing specific steps helps remove ambiguity and gets people to using your project as quickly as possible. If it only runs in a specific context like a particular programming language version or operating system or has dependencies that have to be installed manually, also add a **Requirements** subsection.

### Usage

Use examples liberally, and show the expected output if you can. It's helpful to have inline the smallest example of usage that you can demonstrate, while providing links to more sophisticated examples if they are too long to reasonably include in the README.

### Support

Tell people where they can go to for help. It can be any combination of an issue tracker, a chat room, an email address, etc.

### Roadmap

If you have ideas for releases in the future, it is a good idea to list them in the README.

### Contributing

State if you are open to contributions and what your requirements are for accepting them.

For people who want to make changes to your project, it's helpful to have some documentation on how to get started. Perhaps there is a script that they should run or some environment variables that they need to set. Make these steps explicit. These instructions could also be useful to your future self.

You can also document commands to lint the code or run tests. These steps help to ensure high code quality and reduce the likelihood that the changes inadvertently break something. Having instructions for running tests is especially helpful if it requires external setup, such as starting a Selenium server for testing in a browser.

### Authors and Acknowledgment

Show your appreciation to those who have contributed to the project.

### License

For open source projects, say how it is licensed.

### Project Status

If you have run out of energy or time for your project, put a note at the top of the README saying that development has slowed down or stopped completely. Someone may choose to fork your project or volunteer to step in as a maintainer or owner, allowing your project to keep going. You can also make an explicit request for maintainers.

## FAQ

### Is there a standard README format?

Not all of the suggestions here will make sense for every project, so it's really up to the developers what information should be included in the README.

### What should the README file be named?

`README.md` (or a different file extension if you choose to use a non-Markdown file format). It is traditionally uppercase so that it is more prominent, but it's not a big deal if you think it looks better lowercase.

## What's Next?

### More Documentation

A README is a crucial but basic way of documenting your project. While every project should at least have a README, more involved ones can also benefit from a wiki or a dedicated documentation website. Tools include:

- [Docusaurus](https://docusaurus.io/)
- [GitBook](https://www.gitbook.com/)
- [MkDocs](https://www.mkdocs.org/)
- [Read the Docs](https://readthedocs.org/)
- [Docsify](https://docsify.js.org/)

### Changelog

A [changelog](https://en.wikipedia.org/wiki/Changelog) is another file that is very useful for programming projects. See [Keep a Changelog](http://keepachangelog.com/).

### Contributing Guidelines

Just having a "Contributing" section in your README is a good start. Another approach is to split off your guidelines into their own file (`CONTRIBUTING.md`). If you use GitHub and have this file, then anyone who creates an issue or opens a pull request will get a link to it.

You can also create an issue template and a pull request template. These files give your users and collaborators templates to fill in with the information that you'll need to properly respond.




### Standard Readme Example Maximal

# Title

![banner](assets/text_wordmark_dark.png)

![GitHub Created At](https://img.shields.io/github/created-at/RichardLitt/standard-readme?color=bright-green&style=flat-square)
![GitHub contributors](https://img.shields.io/github/contributors/RichardLitt/standard-readme?color=bright-green&style=flat-square)
[![license](https://img.shields.io/github/license/RichardLitt/standard-readme.svg?color=bright-green&style=flat-square)](LICENSE)
[![standard-readme compliant](https://img.shields.io/badge/readme%20style-standard-brightgreen.svg?style=flat-square)](https://github.com/RichardLitt/standard-readme)

This is an example file with maximal choices selected.

This is a long description.

## Table of Contents

- [Security](#security)
- [Background](#background)
- [Install](#install)
- [Usage](#usage)
- [API](#api)
- [Contributing](#contributing)
- [License](#license)

## Security

### Any optional sections

## Background

### Any optional sections

## Install

This module depends upon a knowledge of [Markdown]().

```
```

### Any optional sections

## Usage

```
```

Note: The `license` badge image link at the top of this file should be updated with the correct `:user` and `:repo`.

### Any optional sections

## API

### Any optional sections

## More optional sections

## Contributing

See [the contributing file](CONTRIBUTING.md)!

PRs accepted.

Small note: If editing the Readme, please conform to the [standard-readme](https://github.com/RichardLitt/standard-readme) specification.

### Any optional sections

## License

[MIT © Richard McRichface.](../LICENSE)




### Standard Readme Spec

# Standard README Specification

> Source: [Standard Readme](https://github.com/RichardLitt/standard-readme) by Richard Litt

A compliant README must satisfy all the requirements listed below.

> Note: Standard Readme is designed for open source libraries. Although it's [historically](README.md#background) made for Node and npm projects, it also applies to libraries in other languages and package managers.

**Requirements:**
  - Be called README (with capitalization) and have a specific extension depending on its format (`.md` for Markdown, `.org` for Org Mode Markup syntax, `.html` for HTML, ...)
  - If the project supports i18n, the file must be named accordingly: `README.de.md`, where `de` is the BCP 47 Language tag. For naming, prioritize non-regional subtags for languages. If there is only one README and the language is not English, then a different language in the text is permissible without needing to specify the BCP tag: e.g., `README.md` can be in German if there is no `README.md` in another language. Where there are multiple languages, `README.md` is reserved for English.
  - Be a valid file in the selected format (Markdown, Org Mode, HTML, ...).
  - Sections must appear in order given below. Optional sections may be omitted.
  - Sections must have the titles listed below, unless otherwise specified. If the README is in another language, the titles must be translated into that language.
  - Must not contain broken links.
  - If there are code examples, they should be linted in the same way as the code is linted in the rest of the project.

## Table of Contents

_Note: This is only a navigation guide for the specification, and does not define or mandate terms for any specification-compliant documents._

- [Sections](#sections)
  - [Title](#title)
  - [Banner](#banner)
  - [Badges](#badges)
  - [Short Description](#short-description)
  - [Long Description](#long-description)
  - [Table of Contents](#table-of-contents-1)
  - [Security](#security)
  - [Background](#background)
  - [Install](#install)
  - [Usage](#usage)
  - [Extra Sections](#extra-sections)
  - [API](#api)
  - [Maintainers](#maintainers)
  - [Thanks](#thanks)
  - [Contributing](#contributing)
  - [License](#license)
- [Definitions](#definitions)

## Sections

### Title
**Status:** Required.

**Requirements:**
- Title must match repository, folder and package manager names - or it may have another, relevant title with the repository, folder, and package manager title next to it in italics and in parentheses. For instance:

  ```markdown
  # Standard Readme Style _(standard-readme)_
  ```

  If any of the folder, repository, or package manager names do not match, there must be a note in the [Long Description](#long-description) explaining why.

**Suggestions:**
- Should be self-evident.

### Banner
**Status:** Optional.

**Requirements:**
- Must not have its own title.
- Must link to local image in current repository.
- Must appear directly after the title.

### Badges
**Status:** Optional.

**Requirements:**
- Must not have its own title.
- Must be newline delimited.

**Suggestions:**
- Use http://shields.io or a similar service to create and host the images.
- Add the [Standard Readme badge](https://github.com/RichardLitt/standard-readme#badge).

### Short Description
**Status:** Required.

**Requirements:**
- Must not have its own title.
- Must be less than 120 characters.
- Must not start with `> `
- Must be on its own line.
- Must match the description in the packager manager's `description` field.
- Must match GitHub's description (if on GitHub).

**Suggestions:**
- Use [gh-description](https://github.com/RichardLitt/gh-description) to set and get GitHub description.
- Use `npm show . description` to show the description from a local [npm](https://npmjs.com) package.

### Long Description
**Status:** Optional.

**Requirements:**
- Must not have its own title.
- If any of the folder, repository, or package manager names do not match, there must be a note here as to why. See [Title section](#title).

**Suggestions:**
- If too long, consider moving to the [Background](#background) section.
- Cover the main reasons for building the repository.
- "This should describe your module in broad terms,
generally in just a few paragraphs; more detail of the module's
routines or methods, lengthy code examples, or other in-depth
material should be given in subsequent sections.

  Ideally, someone who's slightly familiar with your module should be
able to refresh their memory without hitting "page down". As your
reader continues through the document, they should receive a
progressively greater amount of knowledge."

  ~ [Kirrily "Skud" Robert, perlmodstyle](http://perldoc.perl.org/perlmodstyle.html)

### Table of Contents
**Status:** Required; optional for READMEs shorter than 100 lines.

**Requirements:**
- Must link to all sections in the file.
- Must start with the next section; do not include the title or Table of Contents headings.
- Must be at least one-depth: must capture all level two headings (e.g.: Markdown's `##` or Org Mode's `**` or HTML's `<h2>` and so on).

**Suggestions:**
- May capture third and fourth depth headings. If it is a long ToC, these are optional.

### Security
**Status**: Optional.

**Requirements:**
- May go here if it is important to highlight security concerns. Otherwise, it should be in [Extra Sections](#extra-sections).

### Background
**Status:** Optional.

**Requirements:**
- Cover motivation.
- Cover abstract dependencies.
- Cover intellectual provenance: A `See Also` section is also fitting.

### Install
**Status:** Required by default, optional for [documentation repositories](#definitions).

**Requirements:**
- Code block illustrating how to install.

**Subsections:**
- `Dependencies`. Required if there are unusual dependencies or dependencies that must be manually installed.

**Suggestions:**
- Link to prerequisite sites for programming language: [npmjs](https://npmjs.com), [godocs](https://godoc.org), etc.
- Include any system-specific information needed for installation.
- An `Updating` section would be useful for most packages, if there are multiple versions which the user may interface with.

### Usage
**Status:** Required by default, optional for [documentation repositories](#definitions).

**Requirements:**
- Code block illustrating common usage.
- If CLI compatible, code block indicating common usage.
- If importable, code block indicating both import functionality and usage.

**Subsections:**
- `CLI`. Required if CLI functionality exists.

**Suggestions:**
- Cover basic choices that may affect usage: for instance, if JavaScript, cover promises/callbacks, ES6 here.
- If relevant, point to a runnable file for the usage code.

### Extra Sections
**Status**: Optional.

**Requirements:**
- None.

**Suggestions:**
- This should not be called `Extra Sections`. This is a space for 0 or more sections to be included, each of which must have their own titles.
- This should contain any other sections that are relevant, placed after [Usage](#usage) and before [API](#api).
- Specifically, the [Security](#security) section should be here if it wasn't important enough to be placed above.

### API
**Status:** Optional.

**Requirements:**
- Describe exported functions and objects.

**Suggestions:**
- Describe signatures, return types, callbacks, and events.
- Cover types covered where not obvious.
- Describe caveats.
- If using an external API generator (like go-doc, js-doc, or so on), point to an external `API.md` file. This can be the only item in the section, if present.

### Maintainer(s)
**Status**: Optional.

**Requirements:**
- Must be called `Maintainer` or `Maintainers`.
- List maintainer(s) for a repository, along with one way of contacting them (e.g. GitHub link or email).

**Suggestions:**
- This should be a small list of people in charge of the repo. This should not be everyone with access rights, such as an entire organization, but the people who should be pinged and who are in charge of the direction and maintenance of the repository.
- Listing past maintainers is good for attribution, and kind.

### Thanks
**Status**: Optional.

**Requirements:**
- Must be called `Thanks`, `Credits` or `Acknowledgements`.

**Suggestions:**
- State anyone or anything that significantly helped with the development of your project.
- State public contact hyper-links if applicable.

### Contributing
**Status**: Required.

**Requirements:**
- State where users can ask questions.
- State whether PRs are accepted.
- List any requirements for contributing; for instance, having a sign-off on commits.

**Suggestions:**
- Link to a CONTRIBUTING file -- if there is one.
- Be as friendly as possible.
- Link to the GitHub issues.
- Link to a Code of Conduct. A CoC is often in the Contributing section or document, or set elsewhere for an entire organization, so it may not be necessary to include the entire file in each repository. However, it is highly recommended to always link to the code, wherever it lives.
- A subsection for listing contributors is also welcome here.

### License
**Status:** Required.

**Requirements:**
- State license full name or identifier, as listed on the  [SPDX](https://spdx.org/licenses/) license list. For unlicensed repositories, add `UNLICENSED`. For more details, add `SEE LICENSE IN <filename>` and link to the license file. (These requirements were adapted from [npm](https://docs.npmjs.com/files/package.json#license)).
- State license owner.
- Must be last section.

**Suggestions:**
- Link to longer License file in local repository.

## Definitions

_These definitions are provided to clarify any terms used above._

- **Documentation repositories**: Repositories without any functional code. For instance, [RichardLitt/knowledge](https://github.com/RichardLitt/knowledge).




### Art Of Readme

# Art of README

> Source: [hackergrrl/art-of-readme](https://github.com/hackergrrl/art-of-readme)

*This article can also be read in [Chinese](README-zh.md),
[Japanese](README-ja-JP.md),
[Brazilian Portuguese](README-pt-BR.md), [Spanish](README-es-ES.md),
[German](README-de-DE.md), [French](README-fr.md) and [Traditional Chinese](README-zh-TW.md).*

## Etymology

Where does the term "README" come from?

The nomenclature dates back to *at least* the 1970s [and the
PDP-10](http://pdp-10.trailing-edge.com/decuslib10-04/01/43,50322/read.me.html),
though it may even harken back to the days of informative paper notes placed atop
stacks of punchcards, "READ ME!" scrawled on them, describing their use.

A reader<sup>[1](#footnote-1)</sup> suggested that the title README may be a playful nudge toward Lewis
Carroll's *Alice's Adventures in Wonderland*, which features a potion and a cake
labelled *"DRINK ME"* and *"EAT ME"*, respectively.

The pattern of README appearing in all-caps is a consistent facet throughout
history. In addition to the visual strikingness of using all-caps, UNIX systems
would sort capitals before lower case letters, conveniently putting the README
before the rest of the directory's content<sup>[2](#footnote-2)</sup>.

The intent is clear: *"This is important information for the user to read before
proceeding."* Let's explore together what constitutes "important information" in
this modern age.


## For creators, for consumers

This is an article about READMEs. About what they do, why they are an absolute
necessity, and how to craft them well.

This is written for module creators, for as a builder of modules, your job is to
create something that will last. This is an inherent motivation, even if the
author has no intent of sharing their work. Once 6 months pass, a module without
documentation begins to look new and unfamiliar.

This is also written for module consumers, for every module author is also a
module consumer. Node has a very healthy degree of interdependency: no one lives
at the bottom of the dependency tree.

Despite being focused on Node, the author contends that its lessons apply
equally well to other programming ecosystems, as well.


## Many modules: some good, some bad

The Node ecosystem is powered by its modules. [npm](https://npmjs.org) is the
magic that makes it all *go*. In the course of a week, Node developers evaluate
dozens of modules for inclusion in their projects. This is a great deal of power
being churned out on a daily basis, ripe for the plucking, just as fast as one
can write `npm install`.

Like any ecosystem that is extremely accessible, the quality bar varies. npm
does its best to nicely pack away all of these modules and ship them far and
wide. However, the tools found are widely varied: some are shining and new,
others broken and rusty, and still others are somewhere in between. There are
even some that we don't know what they do!

For modules, this can take the form of inaccurate or unhelpful names (any
guesses what the `fudge` module does?), no documentation, no tests, no source
code comments, or incomprehensible function names.

Many don't have an active maintainer. If a module has no human available to
answer questions and explain what a module does, combined with no remnants of
documentation left behind, a module becomes a bizarre alien artifact, unusable
and incomprehensible by the archaeologist-hackers of tomorrow.

For those modules that do have documentation, where do they fall on the quality
spectrum? Maybe it's just a one-liner description: `"sorts numbers by their hex
value"`. Maybe it's a snippet of example code. These are both improvements upon
nothing, but they tend to result in the worst-case scenario for a modern day
module spelunker: digging into the source code to try and understand how it
actually works. Writing excellent documentation is all about keeping the users
*out* of the source code by providing instructions sufficient to enjoy the
wonderful abstractions that your module brings.

Node has a "wide" ecosystem: it's largely made up of a very long list of
independent do-one-thing-well modules flying no flags but their own. There are
[exceptions](https://github.com/lodash/lodash), but despite these minor fiefdoms,
it is the single-purpose commoners who, given their larger numbers, truly rule the
Node kingdom.

This situation has a natural consequence: it can be hard to find *quality* modules
that do exactly what you want.

**This is okay**. Truly. A low bar to entry and a discoverability problem is
infinitely better than a culture problem, where only the privileged few may
participate.

Plus, discoverability -- as it turns out -- is easier to address.


## All roads lead to README.md

The Node community has responded to the challenge of discoverability in
different ways.

Some experienced Node developers band together to create [curated
lists](https://github.com/sindresorhus/awesome-nodejs) of quality modules.
Developers leverage their many years examining hundreds of different modules to
share with newcomers the *crème de la crème*: the best modules in each category.
This might also take the form of RSS feeds and mailing lists of new modules deemed
to be useful by trusted community members.

How about the social graph? This idea spurred the creation of
[node-modules.com](http://node-modules.com/), a npm search replacement that
leverages your GitHub social graph to find modules your friends like or have
made.

Of course there is also npm's built-in [search](https://npmjs.org)
functionality: a safe default, and the usual port of entry for new developers.

No matter your approach, regardless whether a module spelunker enters the module
underground at [npmjs.org](https://npmjs.org),
[github.com](https://github.com), or somewhere else, this would-be user will
eventually end up staring your README square in the face. Since your users
will inevitably find themselves here, what can be done to make their first
impressions maximally effective?


## Professional module spelunking

### The README: Your one-stop shop

A README is a module consumer's first -- and maybe only -- look into your
creation. The consumer wants a module to fulfill their need, so you must explain
exactly what need your module fills, and how effectively it does so.

Your job is to

1. tell them what it is (with context)
2. show them what it looks like in action
3. show them how they use it
4. tell them any other relevant details

This is *your* job. It's up to the module creator to prove that their work is a
shining gem in the sea of slipshod modules. Since so many developers' eyes will
find their way to your README before anything else, quality here is your
public-facing measure of your work.


### Brevity

The lack of a README is a powerful red flag, but even a lengthy README is not
indicative of there being high quality. The ideal README is as short as it can
be without being any shorter. Detailed documentation is good -- make separate
pages for it! -- but keep your README succinct.


### Learn from the past

It is said that those who do not study their history are doomed to make its
mistakes again. Developers have been writing documentation for quite some number
of years. It would be wasteful to not look back a little bit and see what people
did right before Node.

Perl, for all of the flak it receives, is in some ways the spiritual grandparent
of Node. Both are high-level scripting languages, adopt many UNIX idioms, fuel
much of the internet, and both feature a wide module ecosystem.

It so turns out that the [monks](http://perlmonks.org) of the Perl community
indeed have a great deal of experience in writing [quality
READMEs](http://search.cpan.org/~kane/Archive-Tar/lib/Archive/Tar.pm). CPAN is a
wonderful resource that is worth reading through to learn more about a community
that wrote consistently high-calibre documentation.


### No README? No abstraction

No README means developers will need to delve into your code in order to
understand it.

The Perl monks have wisdom to share on the matter:

> Your documentation is complete when someone can use your module without ever
> having to look at its code. This is very important. This makes it possible for
> you to separate your module's documented interface from its internal
> implementation (guts). This is good because it means that you are free to
> change the module's internals as long as the interface remains the same.
>
> Remember: the documentation, not the code, defines what a module does.
-- [Ken Williams](http://mathforum.org/ken/perl_modules.html#document)


### Key elements

Once a README is located, the brave module spelunker must scan it to discern if
it matches the developer's needs. This becomes essentially a series of pattern
matching problems for their brain to solve, where each step takes them deeper
into the module and its details.

Let's say, for example, my search for a 2D collision detection module leads me
to [`collide-2d-aabb-aabb`](https://github.com/hackergrrl/collide-2d-aabb-aabb). I
begin to examine it from top to bottom:

1. *Name* -- self-explanatory names are best. `collide-2d-aabb-aabb` sounds
   promising, though it assumes I know what an "aabb" is. If the name sounds too
   vague or unrelated, it may be a signal to move on.

2. *One-liner* -- having a one-liner that describes the module is useful for
   getting an idea of what the module does in slightly greater detail.
   `collide-2d-aabb-aabb` says it

   > Determines whether a moving axis-aligned bounding box (AABB) collides with
   > other AABBs.

   Awesome: it defines what an AABB is, and what the module does. Now to gauge how
   well it'd fit into my code:

3. *Usage* -- rather than starting to delve into the API docs, it'd be great to
   see what the module looks like in action. I can quickly determine whether the
   example JS fits the desired style and problem. People have lots of opinions
   on things like promises/callbacks and ES6. If it does fit the bill, then I
   can proceed to greater detail.

4. *API* -- the name, description, and usage of this module all sound appealing
   to me. I'm very likely to use this module at this point. I just need to scan
   the API to make sure it does exactly what I need and that it will integrate
   easily into my codebase. The API section ought to detail the module's objects
   and functions, their signatures, return types, callbacks, and events in
   detail. Types should be included where they aren't obvious. Caveats should be
   made clear.

5. *Installation* -- if I've read this far down, then I'm sold on trying out the
   module. If there are nonstandard installation notes, here's where they'd go,
   but even if it's just a regular `npm install`, I'd like to see that mentioned,
   too. New users start using Node all the time, so having a link to npmjs.org
   and an install command provides them the resources to figure out how Node
   modules work.

6. *License* -- most modules put this at the very bottom, but this might
   actually be better to have higher up; you're likely to exclude a module VERY
   quickly if it has a license incompatible with your work. I generally stick to
   the MIT/BSD/X11/ISC flavours. If you have a non-permissive license, stick it
   at the very top of the module to prevent any confusion.


## Cognitive funneling

The ordering of the above was not chosen at random.

Module consumers use many modules, and need to look at many modules.

Once you've looked at hundreds of modules, you begin to notice that the mind
benefits from predictable patterns.

You also start to build out your own personal heuristic for what information you
want, and what red flags disqualify modules quickly.

Thus, it follows that in a README it is desirable to have:

1. a predictable format
2. certain key elements present

You don't need to use *this* format, but try to be consistent to save your users
precious cognitive cycles.

The ordering presented here is lovingly referred to as "cognitive funneling,"
and can be imagined as a funnel held upright, where the widest end contains the
broadest more pertinent details, and moving deeper down into the funnel presents
more specific details that are pertinent for only a reader who is interested
enough in your work to have reached that deeply in the document. Finally, the
bottom can be reserved for details only for those intrigued by the deeper
context of the work (background, credits, biblio, etc.).

Once again, the Perl monks have wisdom to share on the subject:

> The level of detail in Perl module documentation generally goes from
> less detailed to more detailed.  Your SYNOPSIS section should
> contain a minimal example of use (perhaps as little as one line of
> code; skip the unusual use cases or anything not needed by most
> users); the DESCRIPTION should describe your module in broad terms,
> generally in just a few paragraphs; more detail of the module's
> routines or methods, lengthy code examples, or other in-depth
> material should be given in subsequent sections.
>
> Ideally, someone who's slightly familiar with your module should be
> able to refresh their memory without hitting "page down".  As your
> reader continues through the document, they should receive a
> progressively greater amount of knowledge.
> -- from `perlmodstyle`


## Care about people's time

Awesome; the ordering of these key elements should be decided by how quickly
they let someone 'short circuit' and bail on your module.

This sounds bleak, doesn't it? But think about it: your job, when you're doing
it with optimal altruism in mind, isn't to "sell" people on your work. It's to
let them evaluate what your creation does as objectively as possible, and decide
whether it meets their needs or not -- not to, say, maximize your downloads or
userbase.

This mindset doesn't appeal to everyone; it requires checking your ego at the
door and letting the work speak for itself as much as possible. Your only job is
to describe its promise as succinctly as you can, so module spelunkers can
either use your work when it's a fit, or move on to something else that does.


## Call to arms!

Go forth, brave module spelunker, and make your work discoverable and usable
through excellent documentation!


## Bonus: other good practices

Outside of the key points of the article, there are other practices you can
follow (or not follow) to raise your README's quality bar even further and
maximize its usefulness to others:

1. Consider including a **Background** section if your module depends on
   important but not widely known abstractions or other ecosystems. The function
   of [`bisecting-between`](https://github.com/hackergrrl/bisecting-between) is not
   immediately obvious from its name, so it has a detailed *Background* section
   to define and link to the big concepts and abstractions one needs to
   understand to use and grok it. This is also a great place to explain the
   module's motivation if similar modules already exist on npm.

2. Aggressively linkify! If you talk about other modules, ideas, or people, make
   that reference text a link so that visitors can more easily grok your module
   and the ideas it builds on. Few modules exist in a vacuum: all work comes
   from other work, so it pays to help users follow your module's history and
   inspiration.

3. Include information on types of arguments and return parameters if it's not
   obvious. Prefer convention wherever possible (`cb` probably means callback
   function, `num` probably means a `Number`, etc.).

4. Include the example code in **Usage** as a file in your repo -- maybe as
   `example.js`. It's great to have README code that users can actually run if
   they clone the repository.

5. Be judicious in your use of badges. They're easy to
   [abuse](https://github.com/angular/angular). They can also be a breeding
   ground for bikeshedding and endless debate. They add visual noise to your
   README and generally only function if the user is reading your Markdown in a
   browser online, since the images are often hosted elsewhere on the
   internet. For each badge, consider: "what real value is this badge providing
   to the typical viewer of this README?" Do you have a CI badge to show build/test
   status? This signal would better reach important parties by emailing
   maintainers or automatically creating an issue. Always consider the
   audience of the data in your README and ask yourself if there's a flow for
   that data that can better reach its intended audience.

6. API formatting is highly bikesheddable. Use whatever format you think is
   clearest, but make sure your format expresses important subtleties:

   a. which parameters are optional, and their defaults

   b. type information, where it is not obvious from convention

   c. for `opts` object parameters, all keys and values that are accepted

   d. don't shy away from providing a tiny example of an API function's use if
      it is not obvious or fully covered in the **Usage** section.
      However, this can also be a strong signal that the function is too complex
      and needs to be refactored, broken into smaller functions, or removed
      altogether

   e. aggressively linkify specialized terminology! In markdown you can keep
      [footnotes](https://daringfireball.net/projects/markdown/syntax#link) at
      the bottom of your document, so referring to them several times throughout
      becomes cheap. Some of my personal preferences on API formatting can be
      found
      [here](https://github.com/hackergrrl/common-readme/blob/master/api_formatting.md)

7. If your module is a small collection of stateless functions, having a
   **Usage** section as a [Node REPL
   session](https://github.com/hackergrrl/bisecting-between#example) of function
   calls and results might communicate usage more clearly than a source code
   file to run.

8. If your module provides a CLI (command line interface) instead of (or in
    addition to) a programmatic API, show usage examples as command invocations
    and their output. If you create or modify a file, `cat` it to demonstrate
    the change before and after.

9. Don't forget to use `package.json`
    [keywords](https://docs.npmjs.com/files/package.json#keywords) to direct
    module spelunkers to your doorstep.

10. The more you change your API, the more work you need to exert updating
    documentation -- the implication here is that you should keep your APIs
    small and concretely defined early on. Requirements change over time, but
    instead of front-loading assumptions into the APIs of your modules, load
    them up one level of abstraction: the module set itself. If the requirements
    *do* change and 'do-one-concrete-thing' no longer makes sense, then simply
    write a new module that does the thing you need. The 'do-one-concrete-thing'
    module remains a valid and valuable model for the npm ecosystem, and your
    course correction cost you nothing but a simple substitution of one module for
    another.

11. Finally, please remember that your version control repository and its
    embedded README will outlive your [repository host](https://github.com) and
    any of the things you hyperlink to -- especially images -- so *inline* anything
    that is essential to future users grokking your work.


## Bonus: *common-readme*

Not coincidentally, this is also the format used by
[**common-readme**](https://github.com/hackergrrl/common-readme), a set of README
guidelines and handy command-line generator. If you like what's written here,
you may save some time writing READMEs with `common-readme`. You'll find
real module examples with this format, too.

You may also enjoy
[standard-readme](https://github.com/richardlitt/standard-readme), which is a
more structured, lintable take on a common README format.


## Bonus: Exemplars

Theory is well and good, but what do excellent READMEs look like? Here are some
that I think embody the principles of this article well:

- https://github.com/hackergrrl/ice-box
- https://github.com/substack/quote-stream
- https://github.com/feross/bittorrent-dht
- https://github.com/mikolalysenko/box-intersect
- https://github.com/freeman-lab/pixel-grid
- https://github.com/mafintosh/torrent-stream
- https://github.com/pull-stream/pull-stream
- https://github.com/substack/tape
- https://github.com/yoshuawuyts/vmd


## Bonus: The README Checklist

A helpful checklist to gauge how your README is coming along:

- [ ] One-liner explaining the purpose of the module
- [ ] Necessary background context & links
- [ ] Potentially unfamiliar terms link to informative sources
- [ ] Clear, *runnable* example of usage
- [ ] Installation instructions
- [ ] Extensive API documentation
- [ ] Performs [cognitive funneling](https://github.com/hackergrrl/art-of-readme#cognitive-funneling)
- [ ] Caveats and limitations mentioned up-front
- [ ] Doesn't rely on images to relay critical information
- [ ] License


## The author

Hi, I'm [Kira](http://kira.solar).

This little project began back in May in Berlin at squatconf, where I was
digging into how Perl monks write their documentation and also lamenting the
state of READMEs in the Node ecosystem. It spurred me to create
[common-readme](https://github.com/hackergrrl/common-readme). The "README Tips"
section overflowed with tips though, which I decided could be usefully collected
into an article about writing READMEs. Thus, Art of README was born!


## Further Reading

- [README-Driven Development](http://tom.preston-werner.com/2010/08/23/readme-driven-development.html)
- [Documentation First](http://joeyh.name/blog/entry/documentation_first/)


## Footnotes

1. <a name="footnote-1"></a>Thanks,
   [Sixes666](https://www.reddit.com/r/node/comments/55eto9/nodejs_the_art_of_readme/d8akpz6)!

2. <a name="footnote-2"></a>See [The Jargon File](http://catb.org/~esr/jargon/html/R/README-file.html).
   However, most systems today will not sort capitals before all lowercase
   characters, reducing this convention's usefulness to just the visual
   strikingness of all-caps.


## Credits

A heartfelt thank you to [@mafintosh](https://github.com/mafintosh) and
[@feross](https://github.com/feross) for the encouragement I needed to get this
idea off the ground and start writing!

Thank you to the following awesome readers for noticing errors and sending me
PRs :heart: :

- [@ungoldman](https://github.com/ungoldman)
- [@boidolr](https://github.com/boidolr)
- [@imjoehaines](https://github.com/imjoehaines)
- [@radarhere](https://github.com/radarhere)
- [@joshmanders](https://github.com/joshmanders)
- [@ddbeck](https://github.com/ddbeck)
- [@RichardLitt](https://github.com/RichardLitt)
- [@StevenMaude](https://github.com/StevenMaude)
- [@KrishMunot](https://github.com/KrishMunot)
- [@chesterhow](https://github.com/chesterhow)
- [@sjsyrek](https://github.com/sjsyrek)
- [@thenickcox](https://github.com/thenickcox)

Thank you to [@qihaiyan](https://github.com/qihaiyan) for translating Art of
README to Chinese! The following users also made contributions:

- [@BrettDong](https://github.com/brettdong) for revising punctuation in Chinese version.
- [@Alex-fun](https://github.com/Alex-fun)
- [@HmyBmny](https://github.com/HmyBmny)
- [@vra](https://github.com/vra)

Thank you to [@lennonjesus](https://github.com/lennonjesus) for translating Art
of README to Brazilian Portuguese! The following users also made contributions:

- [@rectius](https://github.com/rectius)

Thank you to [@jabiinfante](https://github.com/jabiinfante) for translating Art
of README to Spanish!

Thank you to [@Ryuno-Ki](https://github.com/Ryuno-Ki) for translating Art of
README to German! The following users also made contributions:

- [@randomC0der](https://github.com/randomC0der)

Thank you to [@Manfred Madelaine](https://github.com/Manfred-Madelaine-pro) and
[@Ruben Madelaine](https://github.com/Ruben-Madelaine)
for translating Art of README to French!

## Other Resources
Some readers have suggested other useful resources for README composition:
- [Software Release Practice](https://tldp.org/HOWTO/Software-Release-Practice-HOWTO/distpractice.html#readme)
- [GNU Releases](https://www.gnu.org/prep/standards/html_node/Releases.html#index-README-file)


## License

[Creative Commons Attribution License](http://creativecommons.org/licenses/by/2.0/)




### Standard Readme Example Minimal

# Title

This is an example file with default selections.

## Install

```
```

## Usage

```
```

## Contributing

PRs accepted.

## License

MIT © Richard McRichface




---

## 🚀 Usage

**Reference this template:** `@skill-crafting-effective-readmes.md`


**Platform-specific:**
- **GitHub Copilot**: Add to `.github/copilot-instructions.md`
- **Augment Code**: Use `aug context add` command
- **Cursor/Windsurf**: Reference in chat with `@filename`
- **Claude**: Add to Project Knowledge
- **ChatGPT**: Add to Custom GPT configuration
