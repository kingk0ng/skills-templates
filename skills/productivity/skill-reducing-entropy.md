---
id: skill-reducing-entropy
type: skill
name: reducing-entropy
description: Manual-only skill for minimizing total codebase size. Only activate when
  explicitly requested by user. Measures success by final code amount, not effort.
  Bias toward deletion.
category: productivity
complexity: medium
keywords: []
capabilities: []
token_estimate: 513
has_references: true
reference_count: 4
---

<!-- Converted from Claude Skill Template -->
<!-- Complexity: Medium -->
<!-- Estimated Tokens: ~513 -->
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




# reducing-entropy

> Manual-only skill for minimizing total codebase size. Only activate when explicitly requested by user. Measures success by final code amount, not effort. Bias toward deletion.

# Reducing Entropy

More code begets more code. Entropy accumulates. This skill biases toward the smallest possible codebase.

**Core question:** "What does the codebase look like *after*?"

## Before You Begin

**Load at least one mindset from `references/`**

1. List the files in the reference directory
2. Read frontmatter descriptions to pick which applies
3. Load at least one
4. State which you loaded and its core principle

**Do not proceed until you've done this.**

## The Goal

The goal is **less total code in the final codebase** - not less code to write right now.

- Writing 50 lines that delete 200 lines = net win
- Keeping 14 functions to avoid writing 2 = net loss
- "No churn" is not a goal. Less code is the goal.

**Measure the end state, not the effort.**

## Three Questions

### 1. What's the smallest codebase that solves this?

Not "what's the smallest change" - what's the smallest *result*.

- Could this be 2 functions instead of 14?
- Could this be 0 functions (delete the feature)?
- What would we delete if we did this?

### 2. Does the proposed change result in less total code?

Count lines before and after. If after > before, reject it.

- "Better organized" but more code = more entropy
- "More flexible" but more code = more entropy
- "Cleaner separation" but more code = more entropy

### 3. What can we delete?

Every change is an opportunity to delete. Ask:

- What does this make obsolete?
- What was only needed because of what we're replacing?
- What's the maximum we could remove?

## Red Flags

- **"Keep what exists"** - Status quo bias. The question is total code, not churn.
- **"This adds flexibility"** - Flexibility for what? YAGNI.
- **"Better separation of concerns"** - More files/functions = more code. Separation isn't free.
- **"Type safety"** - Worth how many lines? Sometimes runtime checks in less code wins.
- **"Easier to understand"** - 14 things are not easier than 2 things.

## When This Doesn't Apply

- The codebase is already minimal for what it does
- You're in a framework with strong conventions (don't fight it)
- Regulatory/compliance requirements mandate certain structures

## Reference Mindsets

See `references/` for philosophical grounding.

To add new mindsets, see `adding-reference-mindsets.md`.

---

**Bias toward deletion. Measure the end state.**


---


## 📚 Reference Materials


### Data Over Abstractions

---
description: 100 functions on one data structure beats 10 functions on 10 structures. Generic data and operations enable composition.
---

# Data-Oriented Design

## The Core Principle

> "It is better to have 100 functions operate on one data structure than 10 functions on 10 data structures."

Generic operations on generic data structures beat specialized operations on specialized structures.

## Why Custom Abstractions Hurt

Every custom type, wrapper class, or specialized structure:
- Adds a concept to understand
- Requires its own operations
- Limits composition with other code
- Creates maintenance burden

A `Map<String, Value>` can be processed by hundreds of existing functions. A `SettingsManager` class can only be processed by the methods you write for it.

## Information Over Objects

Model the information domain, not the behavior.

- What data exists?
- What are the essential relationships?
- What transformations do you need?

Then use generic structures (maps, vectors, sets) to represent that information. The behavior comes from functions that operate on data - not from methods bound to custom objects.

## Practical Application

Before creating a new class/type, ask:
- Could this be a map/dict with well-known keys?
- Could this be a simple tuple/record?
- Do I need custom behavior, or just data?

If you just need data, use data structures. Save custom types for when you genuinely need custom behavior that can't be a function.

**The power is in the combinations, not the custom constructs.**

## External References

- [The Value of Values](https://www.infoq.com/presentations/Value-Values/) - Rich Hickey on data vs objects
- [Data-Oriented Design](https://www.dataorienteddesign.com/dodbook/) - Richard Fabian's book on DOD
- [CppCon 2014: Mike Acton "Data-Oriented Design"](https://www.youtube.com/watch?v=rX0ItVEVjHc) - Practical DOD in game engines




### Design Is Taking Apart

---
description: Design is taking things apart, not adding features. Separate concerns, remove dependencies, compose simple pieces.
---

# Composition Over Construction

## The Core Insight

> "Design is about taking things apart."

Good design is not about adding features. It's about removing dependencies. It's about separating concerns so cleanly that each piece can be understood, tested, and changed independently.

## Taking Things Apart

When you see a complex system, the instinct is to understand how the pieces fit together. But the real skill is seeing how to *pull them apart*.

- What different concerns are mixed here?
- Which responsibilities could be separate?
- Where are we conflating different concepts?

Each separation reduces complexity. Each coupling increases it.

## Building from Simple Parts

Once you have simple, independent pieces:
- They compose freely
- They test trivially
- They change safely

Inheritance complects. Composition liberates.

A system built from small, focused functions that transform data is:
- Easier to understand (each piece does one thing)
- Easier to test (pure functions, clear inputs/outputs)
- Easier to change (modify one piece without touching others)

## The Anti-Pattern

The opposite of composition is the "god object" or "kitchen sink" - one thing that knows about everything, does everything, and can't be changed without breaking everything else.

Every helper method you add to a class is a small step toward the kitchen sink. Every layer of abstraction is a coupling waiting to cause pain.

## Practical Application

Before adding a method, wrapper, or abstraction:
- Does this *separate* concerns, or *combine* them?
- Am I making pieces more independent, or more coupled?
- Could I solve this with a function that takes data and returns data?

**Separate, don't combine. Compose, don't construct.**

## External References

- [Simple Made Easy](https://www.infoq.com/presentations/Simple-Made-Easy/) - Rich Hickey on complecting vs composing
- [Out of the Tar Pit](https://curtclifton.net/papers/MosesleyMarks06a.pdf) - Moseley & Marks on essential vs accidental complexity
- [A Philosophy of Software Design](https://www.amazon.com/dp/173210221X) - John Ousterhout on deep vs shallow modules




### Simplicity Vs Easy

---
description: Simple is objective (not intertwined), easy is subjective (familiar). Choose simple over easy for long-term maintainability.
---

# Simplicity vs Easy

## The Core Distinction

**Simple** and **easy** are not the same thing. We conflate them constantly, and it costs us.

- **Simple**: One concept, not intertwined with others. Objective measure.
- **Easy**: Near at hand, familiar, relative to our capabilities. Subjective.

Easy changes over time as you learn. Simple is absolute.

## Why This Matters

When we reach for the "easy" solution - the familiar pattern, the framework we know, the abstraction we've used before - we often add complexity. The easy path introduces concepts that intertwine with others.

The simple solution might be unfamiliar. It might require thinking. But it doesn't braid together concerns that should be separate.

## Complecting

"Complect" means to intertwine or braid together.

Complexity comes from braiding together concepts that should be separate. Every time we couple things, we create complexity. Every coupling is a future debugging session.

Simple means:
- One role
- One task  
- One concept

If you can't explain it simply, it's too complex.

## The Choice

When designing, ask: "Am I choosing this because it's simple, or because it's familiar?"

Familiar feels productive. Simple *is* productive - over the lifetime of the code.

**Choose simple over easy. Always.**

## External References

- [Simple Made Easy](https://www.infoq.com/presentations/Simple-Made-Easy/) - Rich Hickey's canonical talk on this distinction
- [The Value of Values](https://www.infoq.com/presentations/Value-Values/) - Related talk on immutability and simplicity




### Expensive To Add Later

---
description: Some things you Probably Are Gonna Need - know when YAGNI doesn't apply because retrofitting is dramatically more expensive than building it in from the start.
---

# PAGNI: Probably Are Gonna Need It

## The Core Insight

> "When should you override YAGNI? When the cost of adding something later is so dramatically expensive compared with the cost of adding it early on that it's worth taking the risk."

YAGNI is a good default. But some things are genuinely cheaper to build in from the start than to retrofit later. Know the difference.

## Why This Matters

Ruthless YAGNI application can backfire when:
- Adding it later requires touching everything (logging, timestamps, audit trails)
- You can't add it retroactively at all (API versioning, mobile app kill switches)
- The cost of not having it is catastrophic (security fundamentals, data you threw away)

The avoiding-complexity skill pushes toward less. This mindset identifies the exceptions where "add it now" beats "add it later."

## Common PAGNIs

**Data you can't get back:**
- `created_at` / `updated_at` timestamps on every table
- Audit logs (who did what when)
- Many-to-many from the start if there's any hint you'll need more than one

**Infrastructure that's painful to retrofit:**
- API versioning (even if v1 is the only version)
- API pagination (even if lists are small now)
- Automated deploys and CI from day one
- Logging infrastructure

**Security fundamentals:**
- Vulnerability disclosure policy and security@ email
- Session/password invalidation mechanisms
- Safe ways to move redacted data out of production

## The Test

Before invoking PAGNI, ask:
1. **Is retrofitting dramatically more expensive?** (10x+, not 2x)
2. **Is this a known pattern from experience?** (not speculative)
3. **Is the cost of adding it now actually low?** (minutes/hours, not days)

If yes to all three, it's a PAGNI. Otherwise, YAGNI still applies.

## The Balance

PAGNI is not an escape hatch for over-engineering. It's a small, specific list of exceptions learned from painful experience. When in doubt, YAGNI wins.

**Most features: YAGNI. Infrastructure and data collection: probably PAGNI.**

## External References

- [PAGNIs: Probably Are Gonna Need Its](https://simonwillison.net/2021/Jul/1/pagnis/) - Simon Willison
- [YAGNI Exceptions](https://lukeplant.me.uk/blog/posts/yagni-exceptions/) - Luke Plant
- [Application Security PAGNIs](https://jacobian.org/2021/jul/8/appsec-pagnis/) - Jacob Kaplan-Moss




---

## 🚀 Usage

**Reference this template:** `@skill-reducing-entropy.md`


**Platform-specific:**
- **GitHub Copilot**: Add to `.github/copilot-instructions.md`
- **Augment Code**: Use `aug context add` command
- **Cursor/Windsurf**: Reference in chat with `@filename`
- **Claude**: Add to Project Knowledge
- **ChatGPT**: Add to Custom GPT configuration
