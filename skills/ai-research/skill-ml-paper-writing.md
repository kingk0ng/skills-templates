---
id: skill-ml-paper-writing
type: skill
name: ml-paper-writing
description: Write publication-ready ML/AI papers for NeurIPS, ICML, ICLR, ACL, AAAI,
  COLM. Use when drafting papers from research repos, structuring arguments, verifying
  citations, or preparing camera-ready submissions. Includes LaTeX templates, reviewer
  guidelines, and citation verification workflows.
category: ai-research
complexity: medium
keywords:
- api
- github
- go
- performance
- python
- react
- rest
capabilities: []
token_estimate: 6436
has_references: true
reference_count: 5
---

<!-- Converted from Claude Skill Template -->
<!-- Complexity: Medium -->
<!-- Estimated Tokens: ~6,436 -->
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




# ml-paper-writing

> Write publication-ready ML/AI papers for NeurIPS, ICML, ICLR, ACL, AAAI, COLM. Use when drafting papers from research repos, structuring arguments, verifying citations, or preparing camera-ready submissions. Includes LaTeX templates, reviewer guidelines, and citation verification workflows.

# ML Paper Writing for Top AI Conferences

Expert-level guidance for writing publication-ready papers targeting **NeurIPS, ICML, ICLR, ACL, AAAI, and COLM**. This skill combines writing philosophy from top researchers (Nanda, Farquhar, Karpathy, Lipton, Steinhardt) with practical capabilities: File operations, code editing, terminal access, searchLaTeX templates, citation verification APIs, and conference checklists.

## Core Philosophy: Collaborative Writing

**Paper writing is collaborative, but Claude should be proactive in delivering drafts.**

The typical workflow starts with a research repository containing code, results, and experimental artifacts. Claude's role is to:

1. **Understand the project** by exploring the repo, results, and existing documentation
2. **Deliver a complete first draft** when confident about the contribution
3. **Search literature** using web search and APIs to find relevant citations
4. **Refine through feedback cycles** when the scientist provides input
5. **Ask for clarification** only when genuinely uncertain about key decisions

**Key Principle**: Be proactive. If the repo and results are clear, deliver a full draft. Don't block waiting for feedback on every section—scientists are busy. Produce something concrete they can react to, then iterate based on their response.

---

## ⚠️ CRITICAL: Never Hallucinate Citations

**This is the most important rule in academic writing with AI assistance.**

### The Problem
AI-generated citations have a **~40% error rate**. Hallucinated references—papers that don't exist, wrong authors, incorrect years, fabricated DOIs—are a serious form of academic misconduct that can result in desk rejection or retraction.

### The Rule
**NEVER generate BibTeX entries from memory. ALWAYS fetch programmatically.**

| Action | ✅ Correct | ❌ Wrong |
|--------|-----------|----------|
| Adding a citation | Search API → verify → fetch BibTeX | Write BibTeX from memory |
| Uncertain about a paper | Mark as `[CITATION NEEDED]` | Guess the reference |
| Can't find exact paper | Note: "placeholder - verify" | Invent similar-sounding paper |

### When You Can't Verify a Citation

If you cannot programmatically verify a citation, you MUST:

```latex
% EXPLICIT PLACEHOLDER - requires human verification
\cite{PLACEHOLDER_author2024_verify_this}  % TODO: Verify this citation exists
```

**Always tell the scientist**: "I've marked [X] citations as placeholders that need verification. I could not confirm these papers exist."

### Recommended: Install Exa MCP for Paper Search

For the best paper search experience, install **Exa MCP** which provides real-time academic search:

**Claude Code:**
```bash
claude mcp add exa -- npx -y mcp-remote "https://mcp.exa.ai/mcp"
```

**Cursor / VS Code** (add to MCP settings):
```json
{
  "mcpServers": {
    "exa": {
      "type": "http",
      "url": "https://mcp.exa.ai/mcp"
    }
  }
}
```

Exa MCP enables searches like:
- "Find papers on RLHF for language models published after 2023"
- "Search for transformer architecture papers by Vaswani"
- "Get recent work on sparse autoencoders for interpretability"

Then verify results with Semantic Scholar API and fetch BibTeX via DOI.

---

## Workflow 0: Starting from a Research Repository

When beginning paper writing, start by understanding the project:

```
Project Understanding:
- [ ] Step 1: Explore the repository structure
- [ ] Step 2: Read README, existing docs, and key results
- [ ] Step 3: Identify the main contribution with the scientist
- [ ] Step 4: Find papers already cited in the codebase
- [ ] Step 5: Search for additional relevant literature
- [ ] Step 6: Outline the paper structure together
- [ ] Step 7: Draft sections iteratively with feedback
```

**Step 1: Explore the Repository**

```bash
# Understand project structure
ls -la
find . -name "*.py" | head -20
find . -name "*.md" -o -name "*.txt" | xargs grep -l -i "result\|conclusion\|finding"
```

Look for:
- `README.md` - Project overview and claims
- `results/`, `outputs/`, `experiments/` - Key findings
- `configs/` - Experimental settings
- Existing `.bib` files or citation references
- Any draft documents or notes

**Step 2: Identify Existing Citations**

Check for papers already referenced in the codebase:

```bash
# Find existing citations
grep -r "arxiv\|doi\|cite" --include="*.md" --include="*.bib" --include="*.py"
find . -name "*.bib"
```

These are high-signal starting points for Related Work—the scientist has already deemed them relevant.

**Step 3: Clarify the Contribution**

Before writing, explicitly confirm with the scientist:

> "Based on my understanding of the repo, the main contribution appears to be [X].
> The key results show [Y]. Is this the framing you want for the paper,
> or should we emphasize different aspects?"

**Never assume the narrative—always verify with the human.**

**Step 4: Search for Additional Literature**

Use web search to find relevant papers:

```
Search queries to try:
- "[main technique] + [application domain]"
- "[baseline method] comparison"
- "[problem name] state-of-the-art"
- Author names from existing citations
```

Then verify and retrieve BibTeX using the citation workflow below.

**Step 5: Deliver a First Draft**

**Be proactive—deliver a complete draft rather than asking permission for each section.**

If the repo provides clear results and the contribution is apparent:
1. Write the full first draft end-to-end
2. Present the complete draft for feedback
3. Iterate based on scientist's response

If genuinely uncertain about framing or major claims:
1. Draft what you can confidently
2. Flag specific uncertainties: "I framed X as the main contribution—let me know if you'd prefer to emphasize Y instead"
3. Continue with the draft rather than blocking

**Questions to include with the draft** (not before):
- "I emphasized X as the main contribution—adjust if needed"
- "I highlighted results A, B, C—let me know if others are more important"
- "Related work section includes [papers]—add any I missed"

---

## When to Use This Skill

Use this skill when:
- **Starting from a research repo** to write a paper
- **Drafting or revising** specific sections
- **Finding and verifying citations** for related work
- **Formatting** for conference submission
- **Resubmitting** to a different venue (format conversion)
- **Iterating** on drafts with scientist feedback

**Always remember**: First drafts are starting points for discussion, not final outputs.

---

## Balancing Proactivity and Collaboration

**Default: Be proactive. Deliver drafts, then iterate.**

| Confidence Level | Action |
|-----------------|--------|
| **High** (clear repo, obvious contribution) | Write full draft, deliver, iterate on feedback |
| **Medium** (some ambiguity) | Write draft with flagged uncertainties, continue |
| **Low** (major unknowns) | Ask 1-2 targeted questions, then draft |

**Draft first, ask with the draft** (not before):

| Section | Draft Autonomously | Flag With Draft |
|---------|-------------------|-----------------|
| Abstract | Yes | "Framed contribution as X—adjust if needed" |
| Introduction | Yes | "Emphasized problem Y—correct if wrong" |
| Methods | Yes | "Included details A, B, C—add missing pieces" |
| Experiments | Yes | "Highlighted results 1, 2, 3—reorder if needed" |
| Related Work | Yes | "Cited papers X, Y, Z—add any I missed" |

**Only block for input when:**
- Target venue is unclear (affects page limits, framing)
- Multiple contradictory framings seem equally valid
- Results seem incomplete or inconsistent
- Explicit request to review before continuing

**Don't block for:**
- Word choice decisions
- Section ordering
- Which specific results to show (make a choice, flag it)
- Citation completeness (draft with what you find, note gaps)

---

## The Narrative Principle

**The single most critical insight**: Your paper is not a collection of experiments—it's a story with one clear contribution supported by evidence.

Every successful ML paper centers on what Neel Nanda calls "the narrative": a short, rigorous, evidence-based technical story with a takeaway readers care about.

**Three Pillars (must be crystal clear by end of introduction):**

| Pillar | Description | Example |
|--------|-------------|---------|
| **The What** | 1-3 specific novel claims within cohesive theme | "We prove that X achieves Y under condition Z" |
| **The Why** | Rigorous empirical evidence supporting claims | Strong baselines, experiments distinguishing hypotheses |
| **The So What** | Why readers should care | Connection to recognized community problems |

**If you cannot state your contribution in one sentence, you don't yet have a paper.**

---

## Paper Structure Workflow

### Workflow 1: Writing a Complete Paper (Iterative)

Copy this checklist and track progress. **Each step involves drafting → feedback → revision:**

```
Paper Writing Progress:
- [ ] Step 1: Define the one-sentence contribution (with scientist)
- [ ] Step 2: Draft Figure 1 → get feedback → revise
- [ ] Step 3: Draft abstract → get feedback → revise
- [ ] Step 4: Draft introduction → get feedback → revise
- [ ] Step 5: Draft methods → get feedback → revise
- [ ] Step 6: Draft experiments → get feedback → revise
- [ ] Step 7: Draft related work → get feedback → revise
- [ ] Step 8: Draft limitations → get feedback → revise
- [ ] Step 9: Complete paper checklist (required)
- [ ] Step 10: Final review cycle and submission
```

**Step 1: Define the One-Sentence Contribution**

**This step requires explicit confirmation from the scientist.**

Before writing anything, articulate and verify:
- What is the single thing your paper contributes?
- What was not obvious or present before your work?

> "I propose framing the contribution as: '[one sentence]'. Does this capture
> what you see as the main takeaway? Should we adjust the emphasis?"

**Step 2: Draft Figure 1**

Figure 1 deserves special attention—many readers skip directly to it.
- Convey core idea, approach, or most compelling result
- Use vector graphics (PDF/EPS for plots)
- Write captions that stand alone without main text
- Ensure readability in black-and-white (8% of men have color vision deficiency)

**Step 3: Write Abstract (5-Sentence Formula)**

From Sebastian Farquhar (DeepMind):

```
1. What you achieved: "We introduce...", "We prove...", "We demonstrate..."
2. Why this is hard and important
3. How you do it (with specialist keywords for discoverability)
4. What evidence you have
5. Your most remarkable number/result
```

**Delete** generic openings like "Large language models have achieved remarkable success..."

**Step 4: Write Introduction (1-1.5 pages max)**

Must include:
- 2-4 bullet contribution list (max 1-2 lines each in two-column format)
- Clear problem statement
- Brief approach overview
- Methods should start by page 2-3 maximum

**Step 5: Methods Section**

Enable reimplementation:
- Conceptual outline or pseudocode
- All hyperparameters listed
- Architectural details sufficient for reproduction
- Present final design decisions; ablations go in experiments

**Step 6: Experiments Section**

For each experiment, explicitly state:
- What claim it supports
- How it connects to main contribution
- Experimental setting (details in appendix)
- What to observe: "the blue line shows X, which demonstrates Y"

Requirements:
- Error bars with methodology (standard deviation vs standard error)
- Hyperparameter search ranges
- Compute infrastructure (GPU type, total hours)
- Seed-setting methods

**Step 7: Related Work**

Organize methodologically, not paper-by-paper:

**Good:** "One line of work uses Floogledoodle's assumption [refs] whereas we use Doobersnoddle's assumption because..."

**Bad:** "Snap et al. introduced X while Crackle et al. introduced Y."

Cite generously—reviewers likely authored relevant papers.

**Step 8: Limitations Section (REQUIRED)**

All major conferences require this. Counter-intuitively, honesty helps:
- Reviewers are instructed not to penalize honest limitation acknowledgment
- Pre-empt criticisms by identifying weaknesses first
- Explain why limitations don't undermine core claims

**Step 9: Paper Checklist**

NeurIPS, ICML, and ICLR all require paper checklists. See [references/checklists.md](references/checklists.md).

---

## Writing Philosophy for Top ML Conferences

**This section distills the most important writing principles from leading ML researchers.** These aren't optional style suggestions—they're what separates accepted papers from rejected ones.

> "A paper is a short, rigorous, evidence-based technical story with a takeaway readers care about." — Neel Nanda

### The Sources Behind This Guidance

This skill synthesizes writing philosophy from researchers who have published extensively at top venues:

| Source | Key Contribution | Link |
|--------|-----------------|------|
| **Neel Nanda** (Google DeepMind) | The Narrative Principle, What/Why/So What framework | [How to Write ML Papers](https://www.alignmentforum.org/posts/eJGptPbbFPZGLpjsp/highly-opinionated-advice-on-how-to-write-ml-papers) |
| **Sebastian Farquhar** (DeepMind) | 5-sentence abstract formula | [How to Write ML Papers](https://sebastianfarquhar.com/on-research/2024/11/04/how_to_write_ml_papers/) |
| **Gopen & Swan** | 7 principles of reader expectations | [Science of Scientific Writing](https://cseweb.ucsd.edu/~swanson/papers/science-of-writing.pdf) |
| **Zachary Lipton** | Word choice, eliminating hedging | [Heuristics for Scientific Writing](https://www.approximatelycorrect.com/2018/01/29/heuristics-technical-scientific-writing-machine-learning-perspective/) |
| **Jacob Steinhardt** (UC Berkeley) | Precision, consistent terminology | [Writing Tips](https://bounded-regret.ghost.io/) |
| **Ethan Perez** (Anthropic) | Micro-level clarity tips | [Easy Paper Writing Tips](https://ethanperez.net/easy-paper-writing-tips/) |
| **Andrej Karpathy** | Single contribution focus | Various lectures |

**For deeper dives into any of these, see:**
- [references/writing-guide.md](references/writing-guide.md) - Full explanations with examples
- [references/sources.md](references/sources.md) - Complete bibliography

### Time Allocation (From Neel Nanda)

Spend approximately **equal time** on each of:
1. The abstract
2. The introduction
3. The figures
4. Everything else combined

**Why?** Most reviewers form judgments before reaching your methods. Readers encounter your paper as: **title → abstract → introduction → figures → maybe the rest.**

### Writing Style Guidelines

#### Sentence-Level Clarity (Gopen & Swan's 7 Principles)

These principles are based on how readers actually process prose. Violating them forces readers to spend cognitive effort on structure rather than content.

| Principle | Rule | Example |
|-----------|------|---------|
| **Subject-verb proximity** | Keep subject and verb close | ❌ "The model, which was trained on..., achieves" → ✅ "The model achieves... after training on..." |
| **Stress position** | Place emphasis at sentence ends | ❌ "Accuracy improves by 15% when using attention" → ✅ "When using attention, accuracy improves by **15%**" |
| **Topic position** | Put context first, new info after | ✅ "Given these constraints, we propose..." |
| **Old before new** | Familiar info → unfamiliar info | Link backward, then introduce new |
| **One unit, one function** | Each paragraph makes one point | Split multi-point paragraphs |
| **Action in verb** | Use verbs, not nominalizations | ❌ "We performed an analysis" → ✅ "We analyzed" |
| **Context before new** | Set stage before presenting | Explain before showing equation |

**Full 7 principles with detailed examples:** See [references/writing-guide.md](references/writing-guide.md#the-7-principles-of-reader-expectations)

#### Micro-Level Tips (Ethan Perez)

These small changes accumulate into significantly clearer prose:

- **Minimize pronouns**: ❌ "This shows..." → ✅ "This result shows..."
- **Verbs early**: Position verbs near sentence start
- **Unfold apostrophes**: ❌ "X's Y" → ✅ "The Y of X" (when awkward)
- **Delete filler words**: "actually," "a bit," "very," "really," "basically," "quite," "essentially"

**Full micro-tips with examples:** See [references/writing-guide.md](references/writing-guide.md#micro-level-writing-tips)

#### Word Choice (Zachary Lipton)

- **Be specific**: ❌ "performance" → ✅ "accuracy" or "latency" (say what you mean)
- **Eliminate hedging**: Drop "may" and "can" unless genuinely uncertain
- **Avoid incremental vocabulary**: ❌ "combine," "modify," "expand" → ✅ "develop," "propose," "introduce"
- **Delete intensifiers**: ❌ "provides *very* tight approximation" → ✅ "provides tight approximation"

#### Precision Over Brevity (Jacob Steinhardt)

- **Consistent terminology**: Different terms for same concept creates confusion. Pick one and stick with it.
- **State assumptions formally**: Before theorems, list all assumptions explicitly
- **Intuition + rigor**: Provide intuitive explanations alongside formal proofs

### What Reviewers Actually Read

Understanding reviewer behavior helps prioritize your effort:

| Paper Section | % Reviewers Who Read | Implication |
|---------------|---------------------|-------------|
| Abstract | 100% | Must be perfect |
| Introduction | 90%+ (skimmed) | Front-load contribution |
| Figures | Examined before methods | Figure 1 is critical |
| Methods | Only if interested | Don't bury the lede |
| Appendix | Rarely | Put only supplementary details |

**Bottom line**: If your abstract and intro don't hook reviewers, they may never read your brilliant methods section.

---

## Conference Requirements Quick Reference

| Conference | Page Limit | Extra for Camera-Ready | Key Requirement |
|------------|------------|------------------------|-----------------|
| **NeurIPS 2025** | 9 pages | +0 | Mandatory checklist, lay summary for accepted |
| **ICML 2026** | 8 pages | +1 | Broader Impact Statement required |
| **ICLR 2026** | 9 pages | +1 | LLM disclosure required, reciprocal reviewing |
| **ACL 2025** | 8 pages (long) | varies | Limitations section mandatory |
| **AAAI 2026** | 7 pages | +1 | Strict style file adherence |
| **COLM 2025** | 9 pages | +1 | Focus on language models |

**Universal Requirements:**
- Double-blind review (anonymize submissions)
- References don't count toward page limit
- Appendices unlimited but reviewers not required to read
- LaTeX required for all venues

**LaTeX Templates:** See [templates/](templates/) directory for all conference templates.

---

## Using LaTeX Templates Properly

### Workflow 4: Starting a New Paper from Template

**Always copy the entire template directory first, then write within it.**

```
Template Setup Checklist:
- [ ] Step 1: Copy entire template directory to new project
- [ ] Step 2: Verify template compiles as-is (before any changes)
- [ ] Step 3: Read the template's example content to understand structure
- [ ] Step 4: Replace example content section by section
- [ ] Step 5: Keep template comments/examples as reference until done
- [ ] Step 6: Clean up template artifacts only at the end
```

**Step 1: Copy the Full Template**

```bash
# Create your paper directory with the complete template
cp -r templates/neurips2025/ ~/papers/my-new-paper/
cd ~/papers/my-new-paper/

# Verify structure is complete
ls -la
# Should see: main.tex, neurips.sty, Makefile, etc.
```

**⚠️ IMPORTANT**: Copy the ENTIRE directory, not just `main.tex`. Templates include:
- Style files (`.sty`) - required for compilation
- Bibliography styles (`.bst`) - required for references
- Example content - useful as reference
- Makefiles - for easy compilation

**Step 2: Verify Template Compiles First**

Before making ANY changes, compile the template as-is:

```bash
# Using latexmk (recommended)
latexmk -pdf main.tex

# Or manual compilation
pdflatex main.tex
bibtex main
pdflatex main.tex
pdflatex main.tex
```

If the unmodified template doesn't compile, fix that first. Common issues:
- Missing TeX packages → install via `tlmgr install <package>`
- Wrong TeX distribution → use TeX Live (recommended)

**Step 3: Keep Template Content as Reference**

Don't immediately delete all example content. Instead:

```latex
% KEEP template examples commented out as you write
% This shows you the expected format

% Template example (keep for reference):
% \begin{figure}[t]
%   \centering
%   \includegraphics[width=0.8\linewidth]{example-image}
%   \caption{Template shows caption style}
% \end{figure}

% Your actual figure:
\begin{figure}[t]
  \centering
  \includegraphics[width=0.8\linewidth]{your-figure.pdf}
  \caption{Your caption following the same style.}
\end{figure}
```

**Step 4: Replace Content Section by Section**

Work through the paper systematically:

```
Replacement Order:
1. Title and authors (anonymize for submission)
2. Abstract
3. Introduction
4. Methods
5. Experiments
6. Related Work
7. Conclusion
8. References (your .bib file)
9. Appendix
```

For each section:
1. Read the template's example content
2. Note any special formatting or macros used
3. Replace with your content following the same patterns
4. Compile frequently to catch errors early

**Step 5: Use Template Macros**

Templates often define useful macros. Check the preamble for:

```latex
% Common template macros to use:
\newcommand{\method}{YourMethodName}  % Consistent method naming
\newcommand{\eg}{e.g.,\xspace}        % Proper abbreviations
\newcommand{\ie}{i.e.,\xspace}
\newcommand{\etal}{\textit{et al.}\xspace}
```

**Step 6: Clean Up Only at the End**

Only remove template artifacts when paper is nearly complete:

```latex
% BEFORE SUBMISSION - remove these:
% - Commented-out template examples
% - Unused packages
% - Template's example figures/tables
% - Lorem ipsum or placeholder text

% KEEP these:
% - All style files (.sty)
% - Bibliography style (.bst)
% - Required packages from template
% - Any custom macros you're using
```

### Template Pitfalls to Avoid

| Pitfall | Problem | Solution |
|---------|---------|----------|
| Copying only `main.tex` | Missing `.sty`, won't compile | Copy entire directory |
| Modifying `.sty` files | Breaks conference formatting | Never edit style files |
| Adding random packages | Conflicts, breaks template | Only add if necessary |
| Deleting template content too early | Lose formatting reference | Keep as comments until done |
| Not compiling frequently | Errors accumulate | Compile after each section |

### Quick Template Reference

| Conference | Main File | Key Style File | Notes |
|------------|-----------|----------------|-------|
| NeurIPS 2025 | `main.tex` | `neurips.sty` | Has Makefile |
| ICML 2026 | `example_paper.tex` | `icml2026.sty` | Includes algorithm packages |
| ICLR 2026 | `iclr2026_conference.tex` | `iclr2026_conference.sty` | Has math_commands.tex |
| ACL | `acl_latex.tex` | `acl.sty` | Strict formatting |
| AAAI 2026 | `aaai2026-unified-template.tex` | `aaai2026.sty` | Very strict compliance |
| COLM 2025 | `colm2025_conference.tex` | `colm2025_conference.sty` | Similar to ICLR |

---

## Conference Resubmission & Format Conversion

When a paper is rejected or withdrawn from one venue and resubmitted to another, format conversion is required. This is a common workflow in ML research.

### Workflow 3: Converting Between Conference Formats

```
Format Conversion Checklist:
- [ ] Step 1: Identify source and target template differences
- [ ] Step 2: Create new project with target template
- [ ] Step 3: Copy content sections (not preamble)
- [ ] Step 4: Adjust page limits and content
- [ ] Step 5: Update conference-specific requirements
- [ ] Step 6: Verify compilation and formatting
```

**Step 1: Key Template Differences**

| From → To | Page Change | Key Adjustments |
|-----------|-------------|-----------------|
| NeurIPS → ICML | 9 → 8 pages | Cut 1 page, add Broader Impact if missing |
| ICML → ICLR | 8 → 9 pages | Can expand experiments, add LLM disclosure |
| NeurIPS → ACL | 9 → 8 pages | Restructure for NLP conventions, add Limitations |
| ICLR → AAAI | 9 → 7 pages | Significant cuts needed, strict style adherence |
| Any → COLM | varies → 9 | Reframe for language model focus |

**Step 2: Content Migration (NOT Template Merge)**

**Never copy LaTeX preambles between templates.** Instead:

```bash
# 1. Start fresh with target template
cp -r templates/icml2026/ new_submission/

# 2. Copy ONLY content sections from old paper
# - Abstract text
# - Section content (between \section{} commands)
# - Figures and tables
# - Bibliography entries

# 3. Paste into target template structure
```

**Step 3: Adjusting for Page Limits**

When cutting pages (e.g., NeurIPS 9 → AAAI 7):
- Move detailed proofs to appendix
- Condense related work (cite surveys instead of individual papers)
- Combine similar experiments into unified tables
- Use smaller figure sizes with subfigures
- Tighten writing: eliminate redundancy, use active voice

When expanding (e.g., ICML 8 → ICLR 9):
- Add ablation studies reviewers requested
- Expand limitations discussion
- Include additional baselines
- Add qualitative examples

**Step 4: Conference-Specific Adjustments**

| Target Venue | Required Additions |
|--------------|-------------------|
| **ICML** | Broader Impact Statement (after conclusion) |
| **ICLR** | LLM usage disclosure, reciprocal reviewing agreement |
| **ACL/EMNLP** | Limitations section (mandatory), Ethics Statement |
| **AAAI** | Strict adherence to style file (no modifications) |
| **NeurIPS** | Paper checklist (appendix), lay summary if accepted |

**Step 5: Update References**

```latex
% Remove self-citations that reveal identity (for blind review)
% Update any "under review" citations to published versions
% Add new relevant work published since last submission
```

**Step 6: Addressing Previous Reviews**

When resubmitting after rejection:
- **Do** address reviewer concerns in the new version
- **Do** add experiments/clarifications reviewers requested
- **Don't** include a "changes from previous submission" section (blind review)
- **Don't** reference the previous submission or reviews

**Common Conversion Pitfalls:**
- ❌ Copying `\usepackage` commands (causes conflicts)
- ❌ Keeping old conference header/footer commands
- ❌ Forgetting to update `\bibliography{}` path
- ❌ Missing conference-specific required sections
- ❌ Exceeding page limit after format change

---

## Citation Workflow (Hallucination Prevention)

**⚠️ CRITICAL**: AI-generated citations have ~40% error rate. **Never write BibTeX from memory.**

### The Golden Rule

```
IF you cannot programmatically fetch a citation:
    → Mark it as [CITATION NEEDED] or [PLACEHOLDER - VERIFY]
    → Tell the scientist explicitly
    → NEVER invent a plausible-sounding reference
```

### Workflow 2: Adding Citations

```
Citation Verification (MANDATORY for every citation):
- [ ] Step 1: Search using Exa MCP or Semantic Scholar API
- [ ] Step 2: Verify paper exists in 2+ sources (Semantic Scholar + arXiv/CrossRef)
- [ ] Step 3: Retrieve BibTeX via DOI (programmatically, not from memory)
- [ ] Step 4: Verify the claim you're citing actually appears in the paper
- [ ] Step 5: Add verified BibTeX to bibliography
- [ ] Step 6: If ANY step fails → mark as placeholder, inform scientist
```

**Step 0: Use Exa MCP for Initial Search (Recommended)**

If Exa MCP is installed, use it to find relevant papers:
```
Search: "RLHF language model alignment 2023"
Search: "sparse autoencoders interpretability"
Search: "attention mechanism transformers Vaswani"
```

Then verify each result with Semantic Scholar and fetch BibTeX via DOI.

**Step 1: Search Semantic Scholar**

```python
from semanticscholar import SemanticScholar

sch = SemanticScholar()
results = sch.search_paper("attention mechanism transformers", limit=5)
for paper in results:
    print(f"{paper.title} - {paper.paperId}")
    print(f"  DOI: {paper.externalIds.get('DOI', 'N/A')}")
```

**Step 2: Verify Existence**

Confirm paper appears in at least two sources (Semantic Scholar + CrossRef/arXiv).

**Step 3: Retrieve BibTeX via DOI**

```python
import requests

def doi_to_bibtex(doi: str) -> str:
    """Get verified BibTeX from DOI via CrossRef."""
    response = requests.get(
        f"https://doi.org/{doi}",
        headers={"Accept": "application/x-bibtex"}
    )
    response.raise_for_status()
    return response.text

# Example
bibtex = doi_to_bibtex("10.48550/arXiv.1706.03762")
print(bibtex)
```

**Step 4: Verify Claims**

Before citing for a specific claim, access the paper and confirm the attributed claim actually appears.

**Step 5: Handle Failures Explicitly**

If you cannot verify a citation at ANY step:

```latex
% Option 1: Explicit placeholder
\cite{PLACEHOLDER_smith2023_verify}  % TODO: Could not verify - scientist must confirm

% Option 2: Note in text
... as shown in prior work [CITATION NEEDED - could not verify Smith et al. 2023].
```

**Always inform the scientist:**
> "I could not verify the following citations and have marked them as placeholders:
> - Smith et al. 2023 on reward hacking - could not find in Semantic Scholar
> - Jones 2022 on scaling laws - found similar paper but different authors
> Please verify these before submission."

### Summary: Citation Rules

| Situation | Action |
|-----------|--------|
| Found paper, got DOI, fetched BibTeX | ✅ Use the citation |
| Found paper, no DOI | ✅ Use arXiv BibTeX or manual entry from paper |
| Paper exists but can't fetch BibTeX | ⚠️ Mark placeholder, inform scientist |
| Uncertain if paper exists | ❌ Mark `[CITATION NEEDED]`, inform scientist |
| "I think there's a paper about X" | ❌ **NEVER cite** - search first or mark placeholder |

**🚨 NEVER generate BibTeX from memory—always fetch programmatically. 🚨**

See [references/citation-workflow.md](references/citation-workflow.md) for complete API documentation.

---

## Common Issues and Solutions

**Issue: Abstract too generic**

Delete first sentence if it could be prepended to any ML paper. Start with your specific contribution.

**Issue: Introduction exceeds 1.5 pages**

Split background into Related Work. Front-load contribution bullets. Methods should start by page 2-3.

**Issue: Experiments lack explicit claims**

Add sentence before each experiment: "This experiment tests whether [specific claim]..."

**Issue: Reviewers find paper hard to follow**

- Add explicit signposting: "In this section, we show X"
- Use consistent terminology throughout
- Include figure captions that stand alone

**Issue: Missing statistical significance**

Always include:
- Error bars (specify: std dev or std error)
- Number of runs
- Statistical tests if comparing methods

---

## Reviewer Evaluation Criteria

Reviewers assess papers on four dimensions:

| Criterion | What Reviewers Look For |
|-----------|------------------------|
| **Quality** | Technical soundness, well-supported claims |
| **Clarity** | Clear writing, reproducible by experts |
| **Significance** | Community impact, advances understanding |
| **Originality** | New insights (doesn't require new method) |

**Scoring (NeurIPS 6-point scale):**
- 6: Strong Accept - Groundbreaking, flawless
- 5: Accept - Technically solid, high impact
- 4: Borderline Accept - Solid, limited evaluation
- 3: Borderline Reject - Solid but weaknesses outweigh
- 2: Reject - Technical flaws
- 1: Strong Reject - Known results or ethics issues

See [references/reviewer-guidelines.md](references/reviewer-guidelines.md) for detailed reviewer instructions.

---

## Tables and Figures

### Tables

Use `booktabs` LaTeX package for professional tables:

```latex
\usepackage{booktabs}
\begin{tabular}{lcc}
\toprule
Method & Accuracy ↑ & Latency ↓ \\
\midrule
Baseline & 85.2 & 45ms \\
\textbf{Ours} & \textbf{92.1} & 38ms \\
\bottomrule
\end{tabular}
```

**Rules:**
- Bold best value per metric
- Include direction symbols (↑ higher is better, ↓ lower is better)
- Right-align numerical columns
- Consistent decimal precision

### Figures

- **Vector graphics** (PDF, EPS) for all plots and diagrams
- **Raster** (PNG 600 DPI) only for photographs
- Use **colorblind-safe palettes** (Okabe-Ito or Paul Tol)
- Verify **grayscale readability** (8% of men have color vision deficiency)
- **No title inside figure**—the caption serves this function
- **Self-contained captions**—reader should understand without main text

---

## References & Resources

### Reference Documents (Deep Dives)

| Document | Contents |
|----------|----------|
| [writing-guide.md](references/writing-guide.md) | Gopen & Swan 7 principles, Ethan Perez micro-tips, word choice |
| [citation-workflow.md](references/citation-workflow.md) | Citation APIs, Python code, BibTeX management |
| [checklists.md](references/checklists.md) | NeurIPS 16-item, ICML, ICLR, ACL requirements |
| [reviewer-guidelines.md](references/reviewer-guidelines.md) | Evaluation criteria, scoring, rebuttals |
| [sources.md](references/sources.md) | Complete bibliography of all sources |

### LaTeX Templates

Templates in `templates/` directory: **ICML 2026**, **ICLR 2026**, **NeurIPS 2025**, **ACL/EMNLP**, **AAAI 2026**, **COLM 2025**.

**Compiling to PDF:**
- **VS Code/Cursor**: Install LaTeX Workshop extension + TeX Live → Save to auto-compile
- **Command line**: `latexmk -pdf main.tex` or `pdflatex` + `bibtex` workflow
- **Online**: Upload to [Overleaf](https://overleaf.com)

See [templates/README.md](templates/README.md) for detailed setup instructions.

### Key External Sources

**Writing Philosophy:**
- [Neel Nanda: How to Write ML Papers](https://www.alignmentforum.org/posts/eJGptPbbFPZGLpjsp/highly-opinionated-advice-on-how-to-write-ml-papers) - Narrative, "What/Why/So What"
- [Farquhar: How to Write ML Papers](https://sebastianfarquhar.com/on-research/2024/11/04/how_to_write_ml_papers/) - 5-sentence abstract
- [Gopen & Swan: Science of Scientific Writing](https://cseweb.ucsd.edu/~swanson/papers/science-of-writing.pdf) - 7 reader expectation principles
- [Lipton: Heuristics for Scientific Writing](https://www.approximatelycorrect.com/2018/01/29/heuristics-technical-scientific-writing-machine-learning-perspective/) - Word choice
- [Perez: Easy Paper Writing Tips](https://ethanperez.net/easy-paper-writing-tips/) - Micro-level clarity

**APIs:** [Semantic Scholar](https://api.semanticscholar.org/api-docs/) | [CrossRef](https://www.crossref.org/documentation/retrieve-metadata/rest-api/) | [arXiv](https://info.arxiv.org/help/api/basics.html)

**Venues:** [NeurIPS](https://neurips.cc/Conferences/2025/PaperInformation/StyleFiles) | [ICML](https://icml.cc/Conferences/2025/AuthorInstructions) | [ICLR](https://iclr.cc/Conferences/2026/AuthorGuide) | [ACL](https://github.com/acl-org/acl-style-files)



---


## 📚 Reference Materials


### Reviewer Guidelines

# Reviewer Guidelines & Evaluation Criteria

This reference documents how reviewers evaluate papers at major ML/AI conferences, helping authors anticipate and address reviewer concerns.

---

## Contents

- [Universal Evaluation Dimensions](#universal-evaluation-dimensions)
- [NeurIPS Reviewer Guidelines](#neurips-reviewer-guidelines)
- [ICML Reviewer Guidelines](#icml-reviewer-guidelines)
- [ICLR Reviewer Guidelines](#iclr-reviewer-guidelines)
- [ACL Reviewer Guidelines](#acl-reviewer-guidelines)
- [What Makes Reviews Strong](#what-makes-reviews-strong)
- [Common Reviewer Concerns](#common-reviewer-concerns)
- [How to Address Reviewer Feedback](#how-to-address-reviewer-feedback)

---

## Universal Evaluation Dimensions

All major ML conferences assess papers across four core dimensions:

### 1. Quality (Technical Soundness)

**What reviewers ask:**
- Are claims well-supported by theoretical analysis or experimental results?
- Are the proofs correct? Are the experiments properly controlled?
- Are baselines appropriate and fairly compared?
- Is the methodology sound?

**How to ensure high quality:**
- Include complete proofs (main paper or appendix with sketches)
- Use appropriate baselines (not strawmen)
- Report variance/error bars with methodology
- Document hyperparameter selection process

### 2. Clarity (Writing & Organization)

**What reviewers ask:**
- Is the paper clearly written and well organized?
- Can an expert in the field reproduce the results?
- Is notation consistent? Are terms defined?
- Is the paper self-contained?

**How to ensure clarity:**
- Use consistent terminology throughout
- Define all notation at first use
- Include reproducibility details (appendix acceptable)
- Have non-authors read before submission

### 3. Significance (Impact & Importance)

**What reviewers ask:**
- Are the results impactful for the community?
- Will others build upon this work?
- Does it address an important problem?
- What is the potential for real-world impact?

**How to demonstrate significance:**
- Clearly articulate the problem's importance
- Connect to broader research themes
- Discuss potential applications
- Compare to existing approaches meaningfully

### 4. Originality (Novelty & Contribution)

**What reviewers ask:**
- Does this provide new insights?
- How does it differ from prior work?
- Is the contribution non-trivial?

**Key insight from NeurIPS guidelines:**
> "Originality does not necessarily require introducing an entirely new method. Papers that provide novel insights from evaluating existing approaches or shed light on why methods succeed can also be highly original."

---

## NeurIPS Reviewer Guidelines

### Scoring System (1-6 Scale)

| Score | Label | Description |
|-------|-------|-------------|
| **6** | Strong Accept | Groundbreaking, flawless work; top 2-3% of submissions |
| **5** | Accept | Technically solid, high impact; would benefit the community |
| **4** | Borderline Accept | Solid work with limited evaluation; leans accept |
| **3** | Borderline Reject | Solid but weaknesses outweigh strengths; leans reject |
| **2** | Reject | Technical flaws or weak evaluation |
| **1** | Strong Reject | Well-known results or unaddressed ethics concerns |

### Reviewer Instructions

Reviewers are explicitly instructed to:

1. **Evaluate the paper as written** - not what it could be with revisions
2. **Provide constructive feedback** - 3-5 actionable points
3. **Not penalize honest limitations** - acknowledging weaknesses is encouraged
4. **Assess reproducibility** - can the work be verified?
5. **Consider ethical implications** - potential misuse or harm

### What Reviewers Should Avoid

- Superficial, uninformed reviews
- Demanding unreasonable additional experiments
- Penalizing authors for honest limitation acknowledgment
- Rejecting for missing citations to reviewer's own work

### Timeline (NeurIPS 2025)

- Bidding: May 17-21
- Reviewing period: May 29 - July 2
- Author rebuttals: July 24-30
- Discussion period: July 31 - August 13
- Final notifications: September 18

---

## ICML Reviewer Guidelines

### Review Structure

ICML reviewers provide:

1. **Summary** - Brief description of contributions
2. **Strengths** - Positive aspects
3. **Weaknesses** - Areas for improvement
4. **Questions** - Clarifications for authors
5. **Limitations** - Assessment of stated limitations
6. **Ethics** - Any concerns
7. **Overall Score** - Recommendation

### Scoring Guidelines

ICML uses a similar 1-6 scale with calibration:
- Top 25% of accepted papers: Score 5-6
- Typical accepted paper: Score 4-5
- Borderline: Score 3-4
- Clear reject: Score 1-2

### Key Evaluation Points

1. **Reproducibility** - Are there enough details?
2. **Experimental rigor** - Multiple seeds, proper baselines?
3. **Writing quality** - Clear, organized, well-structured?
4. **Novelty** - Non-trivial contribution?

---

## ICLR Reviewer Guidelines

### OpenReview Process

ICLR uses OpenReview with:
- Public reviews (after acceptance decisions)
- Author responses visible to reviewers
- Discussion between reviewers and ACs

### Scoring

ICLR reviews include:
- **Soundness**: 1-4 scale
- **Presentation**: 1-4 scale
- **Contribution**: 1-4 scale
- **Overall**: 1-10 scale
- **Confidence**: 1-5 scale

### Unique ICLR Considerations

1. **LLM Disclosure** - Reviewers assess whether LLM use is properly disclosed
2. **Reproducibility** - Emphasis on code availability
3. **Reciprocal Reviewing** - Authors must also serve as reviewers

---

## ACL Reviewer Guidelines

### ACL-Specific Criteria

ACL adds NLP-specific evaluation:

1. **Linguistic soundness** - Are linguistic claims accurate?
2. **Resource documentation** - Are datasets/models properly documented?
3. **Multilingual consideration** - If applicable, is language diversity addressed?

### Limitations Section

ACL specifically requires a Limitations section. Reviewers check:
- Are limitations honest and comprehensive?
- Do limitations undermine core claims?
- Are potential negative impacts addressed?

### Ethics Review

ACL has a dedicated ethics review process for:
- Dual-use concerns
- Data privacy issues
- Bias and fairness implications

---

## What Makes Reviews Strong

### Following Daniel Dennett's Rules

Good reviewers follow these principles:

1. **Re-express the position fairly** - Show you understand the paper
2. **List agreements** - Acknowledge what works well
3. **List what you learned** - Credit the contribution
4. **Only then critique** - After establishing understanding

### Review Structure Best Practices

**Strong Review Structure:**
```
Summary (1 paragraph):
- What the paper does
- Main contribution claimed

Strengths (3-5 bullets):
- Specific positive aspects
- Why these matter

Weaknesses (3-5 bullets):
- Specific concerns
- Why these matter
- Suggestions for addressing

Questions (2-4 items):
- Clarifications needed
- Things that would change assessment

Minor Issues (optional):
- Typos, unclear sentences
- Formatting issues

Overall Assessment:
- Clear recommendation with reasoning
```

---

## Common Reviewer Concerns

### Technical Concerns

| Concern | How to Pre-empt |
|---------|-----------------|
| "Baselines too weak" | Use state-of-the-art baselines, cite recent work |
| "Missing ablations" | Include systematic ablation study |
| "No error bars" | Report std dev/error, multiple runs |
| "Hyperparameters not tuned" | Document tuning process, search ranges |
| "Claims not supported" | Ensure every claim has evidence |

### Novelty Concerns

| Concern | How to Pre-empt |
|---------|-----------------|
| "Incremental contribution" | Clearly articulate what's new vs prior work |
| "Similar to [paper X]" | Explicitly compare to X in Related Work |
| "Straightforward extension" | Highlight non-obvious aspects |

### Clarity Concerns

| Concern | How to Pre-empt |
|---------|-----------------|
| "Hard to follow" | Use clear structure, signposting |
| "Notation inconsistent" | Review all notation, create notation table |
| "Missing details" | Include reproducibility appendix |
| "Figures unclear" | Self-contained captions, proper sizing |

### Significance Concerns

| Concern | How to Pre-empt |
|---------|-----------------|
| "Limited impact" | Discuss broader implications |
| "Narrow evaluation" | Evaluate on multiple benchmarks |
| "Only works in restricted setting" | Acknowledge scope, explain why still valuable |

---

## How to Address Reviewer Feedback

### Rebuttal Best Practices

**Do:**
- Thank reviewers for their time
- Address each concern specifically
- Provide evidence (new experiments if possible)
- Be concise—reviewers are busy
- Acknowledge valid criticisms

**Don't:**
- Be defensive or dismissive
- Make promises you can't keep
- Ignore difficult criticisms
- Write excessively long rebuttals
- Argue about subjective assessments

### Rebuttal Template

```markdown
We thank the reviewers for their thoughtful feedback.

## Reviewer 1

**R1-Q1: [Quoted concern]**
[Direct response with evidence]

**R1-Q2: [Quoted concern]**
[Direct response with evidence]

## Reviewer 2

...

## Summary of Changes
If accepted, we will:
1. [Specific change]
2. [Specific change]
3. [Specific change]
```

### When to Accept Criticism

Some reviewer feedback should simply be accepted:
- Valid technical errors
- Missing important related work
- Unclear explanations
- Missing experimental details

Acknowledge these gracefully: "The reviewer is correct that... We will revise to..."

### When to Push Back

You can respectfully disagree when:
- Reviewer misunderstood the paper
- Requested experiments are out of scope
- Criticism is factually incorrect

Frame disagreements constructively: "We appreciate this perspective. However, [explanation]..."

---

## Pre-Submission Reviewer Simulation

Before submitting, ask yourself:

**Quality:**
- [ ] Would I trust these results if I saw them?
- [ ] Are all claims supported by evidence?
- [ ] Are baselines fair and recent?

**Clarity:**
- [ ] Can someone reproduce this from the paper?
- [ ] Is the writing clear to non-experts in this subfield?
- [ ] Are all terms and notation defined?

**Significance:**
- [ ] Why should the community care about this?
- [ ] What can people do with this work?
- [ ] Is the problem important?

**Originality:**
- [ ] What specifically is new here?
- [ ] How does this differ from closest related work?
- [ ] Is the contribution non-trivial?




### Checklists

# Conference Paper Checklists

This reference documents the mandatory checklist requirements for major ML/AI conferences. All major venues now require paper checklists—missing them results in desk rejection.

---

## Contents

- [NeurIPS Paper Checklist](#neurips-paper-checklist)
- [ICML Paper Checklist](#icml-paper-checklist)
- [ICLR Requirements](#iclr-requirements)
- [ACL Requirements](#acl-requirements)
- [Universal Pre-Submission Checklist](#universal-pre-submission-checklist)

---

## NeurIPS Paper Checklist

### Mandatory Components

All NeurIPS submissions must include a completed paper checklist. Papers lacking this element face **automatic desk rejection**. The checklist appears after references and supplemental material, outside the page limit.

### 16 Required Checklist Items

#### 1. Claims Alignment
Authors must verify that abstract and introduction claims match theoretical and experimental results, with clearly stated contributions, assumptions, and limitations.

**What to check:**
- [ ] Abstract claims match actual results
- [ ] Introduction doesn't overclaim
- [ ] Contributions are specific and falsifiable

#### 2. Limitations Discussion
Papers should include a dedicated "Limitations" section addressing strong assumptions, robustness to violations, scope constraints, and performance-influencing factors.

**What to include:**
- [ ] Dedicated Limitations section
- [ ] Honest assessment of scope
- [ ] Conditions where method may fail

#### 3. Theory & Proofs
Theoretical contributions require full assumption statements and complete proofs (main paper or appendix with proof sketches for intuition).

**What to check:**
- [ ] All assumptions stated formally
- [ ] Complete proofs provided (main text or appendix)
- [ ] Proof sketches for intuition in main text

#### 4. Reproducibility
Authors must describe steps ensuring results verification through code release, detailed instructions, model access, or checkpoints appropriate to their contribution type.

**What to provide:**
- [ ] Clear reproducibility statement
- [ ] Code availability information
- [ ] Model checkpoints if applicable

#### 5. Data & Code Access
Instructions for reproducing main experimental results should be provided (supplemental material or URLs), including exact commands and environment specifications.

**What to include:**
- [ ] Exact commands to run experiments
- [ ] Environment specifications (requirements.txt, conda env)
- [ ] Data access instructions

#### 6. Experimental Details
Papers must specify training details: data splits, hyperparameters, and selection methods in the main paper or supplementary materials.

**What to document:**
- [ ] Train/val/test split details
- [ ] All hyperparameters used
- [ ] Hyperparameter selection method

#### 7. Statistical Significance
Results require error bars, confidence intervals, or statistical tests with clearly stated calculation methods and underlying assumptions.

**What to include:**
- [ ] Error bars or confidence intervals
- [ ] Number of runs/seeds
- [ ] Calculation method (std dev vs std error)

#### 8. Compute Resources
Specifications needed: compute worker types (CPU/GPU), memory, storage, execution time per run, and total project compute requirements.

**What to document:**
- [ ] GPU type and count
- [ ] Training time per run
- [ ] Total compute used

#### 9. Ethics Code Compliance
Authors confirm adherence to the NeurIPS Code of Ethics, noting any necessary deviations.

**What to verify:**
- [ ] Read NeurIPS Code of Ethics
- [ ] Confirm compliance
- [ ] Note any deviations with justification

#### 10. Broader Impacts
Discussion of potential negative societal applications, fairness concerns, privacy risks, and possible mitigation strategies when applicable.

**What to address:**
- [ ] Potential negative applications
- [ ] Fairness considerations
- [ ] Privacy implications
- [ ] Mitigation strategies

#### 11. Safeguards
High-risk models (language models, internet-scraped datasets) require controlled release mechanisms and usage guidelines.

**What to consider:**
- [ ] Release strategy for sensitive models
- [ ] Usage guidelines if needed
- [ ] Access controls if appropriate

#### 12. License Respect
All existing assets require creator citations, license names, URLs, version numbers, and terms-of-service acknowledgment.

**What to document:**
- [ ] Dataset licenses cited
- [ ] Code licenses respected
- [ ] Version numbers included

#### 13. Asset Documentation
New releases need structured templates documenting training details, limitations, consent procedures, and licensing information.

**For new datasets/models:**
- [ ] Datasheet or model card
- [ ] Training data documentation
- [ ] Known limitations

#### 14. Human Subjects
Crowdsourcing studies must include participant instructions, screenshots, compensation details, and comply with minimum wage requirements.

**What to include:**
- [ ] Task instructions
- [ ] Compensation details
- [ ] Time estimates

#### 15. IRB Approvals
Human subjects research requires documented institutional review board approval or equivalent, with risk descriptions disclosed (maintaining anonymity at submission).

**What to verify:**
- [ ] IRB approval obtained
- [ ] Risk assessment completed
- [ ] Anonymized at submission

#### 16. LLM Declaration
Usage of large language models as core methodology components requires disclosure; writing/editing use doesn't require declaration.

**What to disclose:**
- [ ] LLM used as core methodology component
- [ ] How LLM was used
- [ ] (Writing assistance doesn't require disclosure)

### Response Format

Authors select "yes," "no," or "N/A" per question, with optional 1-2 sentence justifications.

**Important:** Reviewers are explicitly instructed not to penalize honest limitation acknowledgment.

---

## ICML Paper Checklist

### Broader Impact Statement

ICML requires a Broader Impact Statement at the end of the paper, before references. This does NOT count toward the page limit.

**Required elements:**
- Potential positive impacts
- Potential negative impacts
- Mitigation strategies
- Who may be affected

### ICML Specific Requirements

#### Reproducibility Checklist

- [ ] Data splits clearly specified
- [ ] Hyperparameters listed
- [ ] Search ranges documented
- [ ] Selection method explained
- [ ] Compute resources specified
- [ ] Code availability stated

#### Statistical Reporting

- [ ] Error bars on all figures
- [ ] Standard deviation vs standard error specified
- [ ] Number of runs stated
- [ ] Significance tests if comparing methods

#### Anonymization

- [ ] No author names in paper
- [ ] No acknowledgments
- [ ] No grant numbers
- [ ] Prior work cited in third person
- [ ] No identifiable repository URLs

---

## ICLR Requirements

### LLM Disclosure Policy (New for 2026)

ICLR has a specific LLM disclosure requirement:

> "If LLMs played a significant role in research ideation and/or writing to the extent that they could be regarded as a contributor, authors must describe their precise role in a separate appendix section."

**When disclosure is required:**
- LLM used for significant research ideation
- LLM used for substantial writing
- LLM could be considered a contributor

**When disclosure is NOT required:**
- Grammar checking
- Minor editing assistance
- Code completion tools

**Consequences of non-disclosure:**
- Desk rejection
- Potential post-publication issues

### ICLR Specific Requirements

#### Reproducibility Statement (Optional but Recommended)

Add a statement referencing:
- Supporting materials
- Code availability
- Data availability
- Model checkpoints

#### Ethics Statement (Optional)

Address potential concerns in ≤1 page. Does not count toward page limit.

#### Reciprocal Reviewing

- Authors on 3+ papers must serve as reviewers for ≥6 papers
- Each submission needs ≥1 author registered to review ≥3 papers

---

## ACL Requirements

### Limitations Section (Mandatory)

ACL specifically requires a Limitations section:

**What to include:**
- Strong assumptions made
- Scope limitations
- When method may fail
- Generalization concerns

**Important:** The Limitations section does NOT count toward the page limit.

### ACL Specific Checklist

#### Responsible NLP

- [ ] Bias considerations addressed
- [ ] Fairness evaluated if applicable
- [ ] Dual-use concerns discussed

#### Multilingual Considerations

If applicable:
- [ ] Language diversity addressed
- [ ] Non-English languages included
- [ ] Translation quality verified

#### Human Evaluation

If applicable:
- [ ] Annotator details provided
- [ ] Agreement metrics reported
- [ ] Compensation documented

---

## Universal Pre-Submission Checklist

### Before Every Submission

#### Paper Content

- [ ] Abstract ≤ word limit (usually 250-300 words)
- [ ] Main content within page limit
- [ ] References complete and verified
- [ ] Limitations section included
- [ ] All figures/tables have captions
- [ ] Captions are self-contained

#### Formatting

- [ ] Correct template used (venue + year specific)
- [ ] Margins not modified
- [ ] Font sizes not modified
- [ ] Double-blind requirements met
- [ ] Page numbers (for review) or none (camera-ready)

#### Technical

- [ ] All claims supported by evidence
- [ ] Error bars included
- [ ] Baselines appropriate
- [ ] Hyperparameters documented
- [ ] Compute resources stated

#### Reproducibility

- [ ] Code will be available (or justification)
- [ ] Data will be available (or justification)
- [ ] Environment documented
- [ ] Commands to reproduce provided

#### Ethics

- [ ] Broader impacts considered
- [ ] Limitations honestly stated
- [ ] Licenses respected
- [ ] IRB obtained if needed

#### Final Checks

- [ ] PDF compiles without errors
- [ ] All figures render correctly
- [ ] All citations resolve
- [ ] Supplementary material organized
- [ ] Conference checklist completed

---

## Quick Reference: Page Limits

| Conference | Main Content | References | Appendix |
|------------|-------------|------------|----------|
| NeurIPS 2025 | 9 pages | Unlimited | Unlimited (checklist separate) |
| ICML 2026 | 8 pages (+1 camera) | Unlimited | Unlimited |
| ICLR 2026 | 9 pages (+1 camera) | Unlimited | Unlimited |
| ACL 2025 | 8 pages (long) | Unlimited | Unlimited |
| AAAI 2026 | 7 pages (+1 camera) | Unlimited | Unlimited |
| COLM 2025 | 9 pages (+1 camera) | Unlimited | Unlimited |

---

## Template Locations

All conference templates are in the `templates/` directory:

```
templates/
├── icml2026/       # ICML 2026 official
├── iclr2026/       # ICLR 2026 official
├── neurips2025/    # NeurIPS 2025
├── acl/            # ACL style files
├── aaai2026/       # AAAI 2026
└── colm2025/       # COLM 2025
```




### Citation Workflow

# Citation Management & Hallucination Prevention

This reference provides a complete workflow for managing citations programmatically, preventing AI-generated citation hallucinations, and maintaining clean bibliographies.

---

## Contents

- [Why Citation Verification Matters](#why-citation-verification-matters)
- [Citation APIs Overview](#citation-apis-overview)
- [Verified Citation Workflow](#verified-citation-workflow)
- [Python Implementation](#python-implementation)
- [BibTeX Management](#bibtex-management)
- [Common Citation Formats](#common-citation-formats)
- [Troubleshooting](#troubleshooting)

---

## Why Citation Verification Matters

### The Hallucination Problem

Research has documented significant issues with AI-generated citations:
- **~40% error rate** in AI-generated citations (Enago Academy research)
- NeurIPS 2025 found **100+ hallucinated citations** slipped through review
- Common errors include:
  - Fabricated paper titles with real author names
  - Wrong publication venues or years
  - Non-existent papers with plausible metadata
  - Incorrect DOIs or arXiv IDs

### Consequences

- Desk rejection at some venues
- Loss of credibility with reviewers
- Potential retraction if published
- Wasted time chasing non-existent sources

### Solution

**Never generate citations from memory—always verify programmatically.**

---

## Citation APIs Overview

### Primary APIs

| API | Coverage | Rate Limits | Best For |
|-----|----------|-------------|----------|
| **Semantic Scholar** | 214M papers | 1 RPS (free key) | ML/AI papers, citation graphs |
| **CrossRef** | 140M+ DOIs | Polite pool with mailto | DOI lookup, BibTeX retrieval |
| **arXiv** | Preprints | 3-second delays | ML preprints, PDF access |
| **OpenAlex** | 240M+ works | 100K/day, 10 RPS | Open alternative to MAG |

### API Selection Guide

```
Need ML paper search? → Semantic Scholar
Have DOI, need BibTeX? → CrossRef content negotiation
Looking for preprint? → arXiv API
Need open data, bulk access? → OpenAlex
```

### No Official Google Scholar API

Google Scholar has no official API. Scraping violates ToS. Use SerpApi ($75-275/month) only if Semantic Scholar coverage is insufficient.

---

## Verified Citation Workflow

### 5-Step Process

```
1. SEARCH → Query Semantic Scholar with specific keywords
     ↓
2. VERIFY → Confirm paper exists in 2+ sources
     ↓
3. RETRIEVE → Get BibTeX via DOI content negotiation
     ↓
4. VALIDATE → Confirm the claim appears in source
     ↓
5. ADD → Add verified entry to .bib file
```

### Step 1: Search

Use Semantic Scholar for ML/AI papers:

```python
from semanticscholar import SemanticScholar

sch = SemanticScholar()
results = sch.search_paper("transformer attention mechanism", limit=10)

for paper in results:
    print(f"Title: {paper.title}")
    print(f"Year: {paper.year}")
    print(f"DOI: {paper.externalIds.get('DOI', 'N/A')}")
    print(f"arXiv: {paper.externalIds.get('ArXiv', 'N/A')}")
    print(f"Citation count: {paper.citationCount}")
    print("---")
```

### Step 2: Verify Existence

Confirm paper exists in at least two sources:

```python
import requests

def verify_paper(doi=None, arxiv_id=None, title=None):
    """Verify paper exists in multiple sources."""
    sources_found = []

    # Check Semantic Scholar
    sch = SemanticScholar()
    if doi:
        paper = sch.get_paper(f"DOI:{doi}")
        if paper:
            sources_found.append("Semantic Scholar")

    # Check CrossRef (via DOI)
    if doi:
        resp = requests.get(f"https://api.crossref.org/works/{doi}")
        if resp.status_code == 200:
            sources_found.append("CrossRef")

    # Check arXiv
    if arxiv_id:
        resp = requests.get(
            f"http://export.arxiv.org/api/query?id_list={arxiv_id}"
        )
        if "<entry>" in resp.text:
            sources_found.append("arXiv")

    return len(sources_found) >= 2, sources_found
```

### Step 3: Retrieve BibTeX

Use DOI content negotiation for guaranteed accuracy:

```python
import requests

def doi_to_bibtex(doi: str) -> str:
    """Get verified BibTeX from DOI via CrossRef content negotiation."""
    response = requests.get(
        f"https://doi.org/{doi}",
        headers={"Accept": "application/x-bibtex"},
        allow_redirects=True
    )
    response.raise_for_status()
    return response.text

# Example: "Attention Is All You Need"
bibtex = doi_to_bibtex("10.48550/arXiv.1706.03762")
print(bibtex)
```

### Step 4: Validate Claims

Before citing a paper for a specific claim, verify the claim exists:

```python
def get_paper_abstract(doi):
    """Get abstract to verify claims."""
    sch = SemanticScholar()
    paper = sch.get_paper(f"DOI:{doi}")
    return paper.abstract if paper else None

# Verify claim appears in abstract
abstract = get_paper_abstract("10.48550/arXiv.1706.03762")
claim = "attention mechanism"
if claim.lower() in abstract.lower():
    print("Claim appears in paper")
```

### Step 5: Add to Bibliography

Add verified entry to your .bib file with consistent key format:

```python
def generate_citation_key(bibtex: str) -> str:
    """Generate consistent citation key: author_year_firstword."""
    import re

    # Extract author
    author_match = re.search(r'author\s*=\s*\{([^}]+)\}', bibtex, re.I)
    if author_match:
        first_author = author_match.group(1).split(',')[0].split()[-1]
    else:
        first_author = "unknown"

    # Extract year
    year_match = re.search(r'year\s*=\s*\{?(\d{4})\}?', bibtex, re.I)
    year = year_match.group(1) if year_match else "0000"

    # Extract title first word
    title_match = re.search(r'title\s*=\s*\{([^}]+)\}', bibtex, re.I)
    if title_match:
        first_word = title_match.group(1).split()[0].lower()
        first_word = re.sub(r'[^a-z]', '', first_word)
    else:
        first_word = "paper"

    return f"{first_author.lower()}_{year}_{first_word}"
```

---

## Python Implementation

### Complete Citation Manager Class

```python
"""
Citation Manager - Verified citation workflow for ML papers.
"""

import requests
import time
from typing import Optional, List, Dict, Tuple
from dataclasses import dataclass

try:
    from semanticscholar import SemanticScholar
except ImportError:
    print("Install: pip install semanticscholar")
    SemanticScholar = None

@dataclass
class Paper:
    title: str
    authors: List[str]
    year: int
    doi: Optional[str]
    arxiv_id: Optional[str]
    venue: Optional[str]
    citation_count: int
    abstract: Optional[str]

class CitationManager:
    """Manage citations with verification."""

    def __init__(self, api_key: Optional[str] = None):
        self.sch = SemanticScholar(api_key=api_key) if SemanticScholar else None
        self.verified_papers: Dict[str, Paper] = {}

    def search(self, query: str, limit: int = 10) -> List[Paper]:
        """Search for papers using Semantic Scholar."""
        if not self.sch:
            raise RuntimeError("Semantic Scholar not available")

        results = self.sch.search_paper(query, limit=limit)
        papers = []

        for r in results:
            paper = Paper(
                title=r.title,
                authors=[a.name for a in (r.authors or [])],
                year=r.year or 0,
                doi=r.externalIds.get('DOI') if r.externalIds else None,
                arxiv_id=r.externalIds.get('ArXiv') if r.externalIds else None,
                venue=r.venue,
                citation_count=r.citationCount or 0,
                abstract=r.abstract
            )
            papers.append(paper)

        return papers

    def verify(self, paper: Paper) -> Tuple[bool, List[str]]:
        """Verify paper exists in multiple sources."""
        sources = []

        # Already found in Semantic Scholar via search
        sources.append("Semantic Scholar")

        # Check CrossRef if DOI available
        if paper.doi:
            try:
                resp = requests.get(
                    f"https://api.crossref.org/works/{paper.doi}",
                    timeout=10
                )
                if resp.status_code == 200:
                    sources.append("CrossRef")
            except:
                pass

        # Check arXiv if ID available
        if paper.arxiv_id:
            try:
                resp = requests.get(
                    f"http://export.arxiv.org/api/query?id_list={paper.arxiv_id}",
                    timeout=10
                )
                if "<entry>" in resp.text and "<title>" in resp.text:
                    sources.append("arXiv")
            except:
                pass

        return len(sources) >= 2, sources

    def get_bibtex(self, paper: Paper) -> Optional[str]:
        """Get BibTeX for verified paper."""
        if paper.doi:
            try:
                resp = requests.get(
                    f"https://doi.org/{paper.doi}",
                    headers={"Accept": "application/x-bibtex"},
                    timeout=10,
                    allow_redirects=True
                )
                if resp.status_code == 200:
                    return resp.text
            except:
                pass

        # Fallback: generate from paper data
        return self._generate_bibtex(paper)

    def _generate_bibtex(self, paper: Paper) -> str:
        """Generate BibTeX from paper metadata."""
        # Generate citation key
        first_author = paper.authors[0].split()[-1] if paper.authors else "unknown"
        first_word = paper.title.split()[0].lower().replace(',', '').replace(':', '')
        key = f"{first_author.lower()}_{paper.year}_{first_word}"

        # Format authors
        authors = " and ".join(paper.authors) if paper.authors else "Unknown"

        bibtex = f"""@article{{{key},
  title = {{{paper.title}}},
  author = {{{authors}}},
  year = {{{paper.year}}},
  {'doi = {' + paper.doi + '},' if paper.doi else ''}
  {'eprint = {' + paper.arxiv_id + '},' if paper.arxiv_id else ''}
  {'journal = {' + paper.venue + '},' if paper.venue else ''}
}}"""
        return bibtex

    def cite(self, query: str) -> Optional[str]:
        """Full workflow: search, verify, return BibTeX."""
        # Search
        papers = self.search(query, limit=5)
        if not papers:
            return None

        # Take top result
        paper = papers[0]

        # Verify
        verified, sources = self.verify(paper)
        if not verified:
            print(f"Warning: Could only verify in {sources}")

        # Get BibTeX
        bibtex = self.get_bibtex(paper)

        # Cache
        if bibtex:
            self.verified_papers[paper.title] = paper

        return bibtex


# Usage example
if __name__ == "__main__":
    cm = CitationManager()

    # Search and cite
    bibtex = cm.cite("attention is all you need transformer")
    if bibtex:
        print(bibtex)
```

### Quick Functions

```python
def quick_cite(query: str) -> str:
    """One-liner citation."""
    cm = CitationManager()
    return cm.cite(query)

def batch_cite(queries: List[str], output_file: str = "references.bib"):
    """Cite multiple papers and save to file."""
    cm = CitationManager()
    bibtex_entries = []

    for query in queries:
        print(f"Processing: {query}")
        bibtex = cm.cite(query)
        if bibtex:
            bibtex_entries.append(bibtex)
        time.sleep(1)  # Rate limiting

    with open(output_file, 'w') as f:
        f.write("\n\n".join(bibtex_entries))

    print(f"Saved {len(bibtex_entries)} citations to {output_file}")
```

---

## BibTeX Management

### BibTeX vs BibLaTeX

| Feature | BibTeX | BibLaTeX |
|---------|--------|----------|
| Unicode support | Limited | Full |
| Entry types | Standard | Extended (@online, @dataset) |
| Customization | Limited | Highly flexible |
| Backend | bibtex | Biber (recommended) |

**Recommendation**: Use BibLaTeX with Biber for new papers.

### LaTeX Setup

```latex
% In preamble
\usepackage[
    backend=biber,
    style=numeric,
    sorting=none
]{biblatex}
\addbibresource{references.bib}

% In document
\cite{vaswani_2017_attention}

% At end
\printbibliography
```

### Citation Commands

```latex
\cite{key}      % Numeric: [1]
\citep{key}     % Parenthetical: (Author, 2020)
\citet{key}     % Textual: Author (2020)
\citeauthor{key} % Just author name
\citeyear{key}  % Just year
```

### Consistent Citation Keys

Use format: `author_year_firstword`

```
vaswani_2017_attention
devlin_2019_bert
brown_2020_language
```

---

## Common Citation Formats

### Conference Paper

```bibtex
@inproceedings{vaswani_2017_attention,
  title = {Attention Is All You Need},
  author = {Vaswani, Ashish and Shazeer, Noam and Parmar, Niki and
            Uszkoreit, Jakob and Jones, Llion and Gomez, Aidan N and
            Kaiser, Lukasz and Polosukhin, Illia},
  booktitle = {Advances in Neural Information Processing Systems},
  volume = {30},
  year = {2017},
  publisher = {Curran Associates, Inc.}
}
```

### Journal Article

```bibtex
@article{hochreiter_1997_long,
  title = {Long Short-Term Memory},
  author = {Hochreiter, Sepp and Schmidhuber, J{\"u}rgen},
  journal = {Neural Computation},
  volume = {9},
  number = {8},
  pages = {1735--1780},
  year = {1997},
  publisher = {MIT Press}
}
```

### arXiv Preprint

```bibtex
@misc{brown_2020_language,
  title = {Language Models are Few-Shot Learners},
  author = {Brown, Tom and Mann, Benjamin and Ryder, Nick and others},
  year = {2020},
  eprint = {2005.14165},
  archiveprefix = {arXiv},
  primaryclass = {cs.CL}
}
```

---

## Troubleshooting

### Common Issues

**Issue: Semantic Scholar returns no results**
- Try more specific keywords
- Check spelling of author names
- Use quotation marks for exact phrases

**Issue: DOI doesn't resolve to BibTeX**
- DOI may be registered but not linked to CrossRef
- Try arXiv ID instead if available
- Generate BibTeX from metadata manually

**Issue: Rate limiting errors**
- Add delays between requests (1-3 seconds)
- Use API key if available
- Cache results to avoid repeat queries

**Issue: Encoding problems in BibTeX**
- Use proper LaTeX escaping: `{\"u}` for ü
- Ensure file is UTF-8 encoded
- Use BibLaTeX with Biber for better Unicode

### Verification Checklist

Before adding a citation:

- [ ] Paper found in at least 2 sources
- [ ] DOI or arXiv ID verified
- [ ] BibTeX retrieved (not generated from memory)
- [ ] Entry type correct (@inproceedings vs @article)
- [ ] Author names complete and correctly formatted
- [ ] Year and venue verified
- [ ] Citation key follows consistent format

---

## Additional Resources

**APIs:**
- Semantic Scholar: https://api.semanticscholar.org/api-docs/
- CrossRef: https://www.crossref.org/documentation/retrieve-metadata/rest-api/
- arXiv: https://info.arxiv.org/help/api/basics.html
- OpenAlex: https://docs.openalex.org/

**Python Libraries:**
- `semanticscholar`: https://pypi.org/project/semanticscholar/
- `arxiv`: https://pypi.org/project/arxiv/
- `habanero` (CrossRef): https://github.com/sckott/habanero

**Verification Tools:**
- Citely: https://citely.ai/citation-checker
- ReciteWorks: https://reciteworks.com/




### Writing Guide

# ML Paper Writing Philosophy & Best Practices

This reference compiles writing advice from prominent ML researchers including Neel Nanda, Andrej Karpathy, Sebastian Farquhar, Zachary Lipton, and Jacob Steinhardt.

---

## Contents

- [The Narrative Principle](#the-narrative-principle)
- [Time Allocation](#time-allocation)
- [Abstract Writing Formula](#abstract-writing-formula)
- [Introduction Structure](#introduction-structure)
- [Sentence-Level Clarity](#sentence-level-clarity)
- [Word Choice and Precision](#word-choice-and-precision)
- [Mathematical Writing](#mathematical-writing)
- [Figure Design](#figure-design)
- [Common Mistakes to Avoid](#common-mistakes-to-avoid)

---

## The Narrative Principle

### From Neel Nanda

"A paper is a short, rigorous, evidence-based technical story with a takeaway readers care about."

The narrative rests on three pillars that must be crystal clear by the end of your introduction:

**The "What"**: One to three specific novel claims fitting within a cohesive theme. Vague contributions like "we study X" fail immediately—reviewers need precise, falsifiable claims.

**The "Why"**: Rigorous empirical evidence that convincingly supports those claims, including strong baselines honestly tuned and experiments that distinguish between competing hypotheses rather than merely showing "decent results."

**The "So What"**: Why readers should care, connecting your contribution to problems the community recognizes as important.

### From Andrej Karpathy

"A paper is not a random collection of experiments you report on. The paper sells a single thing that was not obvious or present before. The entire paper is organized around this core contribution with surgical precision."

This applies whether you're presenting a new architecture, a theoretical result, or improved understanding of existing methods—NeurIPS explicitly notes that "originality does not necessarily require an entirely new method."

**Practical Implication**: If you cannot state your contribution in one sentence, you don't yet have a paper. Everything else—experiments, related work, discussion—exists only to support that core claim.

---

## Time Allocation

### From Neel Nanda

Spend approximately **the same amount of time** on each of:
1. The abstract
2. The introduction
3. The figures
4. Everything else combined

This isn't hyperbole—most reviewers form preliminary judgments before reaching your methods section. Readers encounter your paper in a predictable pattern: **title → abstract → introduction → figures → maybe the rest.**

### Reviewer Reading Patterns

Studies of reviewer behavior show:
- Abstract is read 100% of the time
- Introduction is skimmed by 90%+ of reviewers
- Figures are examined before methods by most reviewers
- Full methods are read only if interest is established

**Implication**: Front-load your paper's value. Don't bury the contribution.

---

## Abstract Writing Formula

### Sebastian Farquhar's 5-Sentence Formula

1. **What you achieved**: "We introduce...", "We prove...", "We demonstrate..."
2. **Why this is hard and important**
3. **How you do it** (with specialist keywords for discoverability)
4. **What evidence you have**
5. **Your most remarkable number/result**

### Example (Good Abstract)

```
We prove that gradient descent on overparameterized neural networks
converges to global minima at a linear rate. [What]
This resolves a fundamental question about why deep learning works
despite non-convex optimization landscapes. [Why hard/important]
Our proof relies on showing that the Neural Tangent Kernel remains
approximately constant during training, reducing the problem to
kernel regression. [How with keywords]
We validate our theory on CIFAR-10 and ImageNet, showing that
predicted convergence rates match experiments within 5%. [Evidence]
This is the first polynomial-time convergence guarantee for
networks with practical depth and width. [Remarkable result]
```

### What to Avoid

From Zachary Lipton: "If the first sentence can be pre-pended to any ML paper, delete it."

**Delete these openings**:
- "Large language models have achieved remarkable success..."
- "Deep learning has revolutionized..."
- "In recent years, neural networks have..."

**Start with your specific contribution instead.**

---

## Introduction Structure

### Requirements

- **1-1.5 pages maximum** (in two-column format)
- **Methods should start by page 2-3**
- Must include **2-4 bullet contribution list** (max 1-2 lines each)

### Structure Template

```markdown
1. Opening Hook (2-3 sentences)
   - State the problem your paper addresses
   - Why it matters RIGHT NOW

2. Background/Challenge (1 paragraph)
   - What makes this problem hard?
   - What have others tried? Why is it insufficient?

3. Your Approach (1 paragraph)
   - What do you do differently?
   - Key insight that enables your contribution

4. Contribution Bullets (2-4 items)
   - Be specific and falsifiable
   - Each bullet: 1-2 lines maximum

5. Results Preview (2-3 sentences)
   - Most impressive numbers
   - Scope of evaluation

6. Paper Organization (optional, 1-2 sentences)
   - "Section 2 presents... Section 3 describes..."
```

### Contribution Bullets: Good vs Bad

**Good:**
- We prove that X converges in O(n log n) time under assumption Y
- We introduce Z, a 3-layer architecture that reduces memory by 40%
- We demonstrate that A outperforms B by 15% on benchmark C

**Bad:**
- We study the problem of X (not a contribution)
- We provide extensive experiments (too vague)
- We make several contributions to the field (says nothing)

---

## Sentence-Level Clarity

### From Gopen & Swan: "The Science of Scientific Writing"

The seminal 1990 paper by George Gopen and Judith Swan establishes that **readers have structural expectations** about where information appears in prose. Violating these expectations forces readers to spend energy on structure rather than content.

> "If the reader is to grasp what the writer means, the writer must understand what the reader needs."

#### The 7 Principles of Reader Expectations

**Principle 1: Subject-Verb Proximity**

Keep grammatical subject and verb close together. Anything intervening reads as interruption of lesser importance.

**Weak**: "The model, which was trained on 100M tokens and fine-tuned on domain-specific data using LoRA with rank 16, achieves state-of-the-art results"

**Strong**: "The model achieves state-of-the-art results after training on 100M tokens and fine-tuning with LoRA (rank 16)"

**Principle 2: Stress Position (Save the Best for Last)**

Readers naturally emphasize the **last words of a sentence**. Place your most important information there.

**Weak**: "Accuracy improves by 15% when using attention"
**Strong**: "When using attention, accuracy improves by **15%**"

**Principle 3: Topic Position (First Things First)**

The beginning of a sentence establishes perspective. Put the "whose story" element first—readers expect the sentence to be about whoever shows up first.

**Weak**: "A novel attention mechanism that computes alignment scores is introduced"
**Strong**: "To address the alignment problem, we introduce a novel attention mechanism"

**Principle 4: Old Information Before New**

Put familiar information (old) in the topic position for backward linkage; put new information in the stress position for emphasis.

**Weak**: "Sparse attention was introduced by Child et al. The quadratic complexity of standard attention motivates this work."
**Strong**: "Standard attention has quadratic complexity. To address this, Child et al. introduced sparse attention."

**Principle 5: One Unit, One Function**

Each unit of discourse (sentence, paragraph, section) should serve a single function. If you have two points, use two units.

**Principle 6: Articulate Action in the Verb**

Express the action of each sentence in its verb, not in nominalized nouns.

**Weak**: "We performed an analysis of the results" (nominalization)
**Strong**: "We analyzed the results" (action in verb)

**Principle 7: Context Before New Information**

Provide context before asking the reader to consider anything new. This applies at all levels—sentence, paragraph, section.

**Weak**: "Equation 3 shows that convergence is guaranteed when the learning rate satisfies..."
**Strong**: "For convergence to be guaranteed, the learning rate must satisfy the condition in Equation 3..."

#### Summary Table

| Principle | Rule | Mnemonic |
|-----------|------|----------|
| Subject-Verb Proximity | Keep subject and verb close | "Don't interrupt yourself" |
| Stress Position | Emphasis at sentence end | "Save the best for last" |
| Topic Position | Context at sentence start | "First things first" |
| Old Before New | Familiar → unfamiliar | "Build on known ground" |
| One Unit, One Function | Each paragraph = one point | "One idea per container" |
| Action in Verb | Use verbs, not nominalizations | "Verbs do, nouns sit" |
| Context Before New | Explain before presenting | "Set the stage first" |

---

---

## Micro-Level Writing Tips

### From Ethan Perez (Anthropic)

These practical micro-level tips improve clarity at the sentence and word level.

#### Pronoun Management

**Minimize pronouns** ("this," "it," "these," "that"). When pronouns are necessary, use them as adjectives with a noun:

**Weak**: "This shows that the model converges."
**Strong**: "This result shows that the model converges."

**Weak**: "It improves performance."
**Strong**: "This modification improves performance."

#### Verb Placement

**Position verbs early** in sentences for better parsing:

**Weak**: "The gradient, after being computed and normalized, updates the weights."
**Strong**: "The gradient updates the weights after being computed and normalized."

#### Apostrophe Unfolding

Transform possessive constructions for clarity:

**Original**: "X's Y" → **Unfolded**: "The Y of X"

**Before**: "The model's accuracy on the test set"
**After**: "The accuracy of the model on the test set"

This isn't always better, but when sentences feel awkward, try unfolding.

#### Words to Eliminate

Delete these filler words in almost all cases:
- "actually"
- "a bit"
- "fortunately" / "unfortunately"
- "very" / "really"
- "quite"
- "basically"
- "essentially"
- Excessive connectives ("however," "moreover," "furthermore" when not needed)

#### Sentence Construction Rules

1. **One idea per sentence** - If struggling to express an idea in one sentence, it needs two
2. **No repeated sounds** - Avoid similar-sounding words in the same sentence
3. **Every sentence adds information** - Delete sentences that merely restate
4. **Active voice always** - Specify the actor ("We find..." not "It is found...")
5. **Expand contractions** - "don't" → "do not" for formality

#### Paragraph Architecture

- **First sentence**: State the point clearly
- **Middle sentences**: Support with evidence
- **Last sentence**: Reinforce or transition

Don't bury key information in the middle of paragraphs.

---

## Word Choice and Precision

### From Zachary Lipton

**Eliminate hedging** unless genuine uncertainty exists:
- Delete "may" and "can" unless necessary
- "provides *very* tight approximation" drips with insecurity
- "provides tight approximation" is confident

**Avoid vacuous intensifiers**:
- Delete: very, extremely, highly, significantly (unless statistical)
- These words signal insecurity, not strength

### From Jacob Steinhardt

**Precision over brevity**: Replace vague terms with specific ones.

| Vague | Specific |
|-------|----------|
| performance | accuracy, latency, throughput |
| improves | increases accuracy by X%, reduces latency by Y |
| large | 1B parameters, 100M tokens |
| fast | 3x faster, 50ms latency |
| good results | 92% accuracy, 0.85 F1 |

**Consistent terminology**: Referring to the same concept with different terms creates confusion.

**Choose one and stick with it**:
- "model" vs "network" vs "architecture"
- "training" vs "learning" vs "optimization"
- "sample" vs "example" vs "instance"

### Vocabulary Signaling

**Avoid words signaling incremental work**:
- Never: "combine," "modify," "expand," "extend"
- Instead: "develop," "propose," "introduce"

**Why**: "We combine X and Y" sounds like you stapled two existing ideas together. "We develop a method that leverages X for Y" sounds like genuine contribution.

---

## Mathematical Writing

### From Ethan Perez

**Unfold apostrophes** for clarity:
- Weak: "X's Y"
- Strong: "The Y of X"

Example: "the model's accuracy" → "the accuracy of the model"

### General Principles

1. **State all assumptions formally** before theorems
2. **Provide intuitive explanations** alongside proofs
3. **Use consistent notation** throughout the paper
4. **Define symbols at first use**

### Notation Conventions

```latex
% Scalars: lowercase italic
$x$, $y$, $\alpha$, $\beta$

% Vectors: lowercase bold
$\mathbf{x}$, $\mathbf{v}$

% Matrices: uppercase bold
$\mathbf{W}$, $\mathbf{X}$

% Sets: uppercase calligraphic
$\mathcal{X}$, $\mathcal{D}$

% Functions: roman for named functions
$\mathrm{softmax}$, $\mathrm{ReLU}$
```

---

## Figure Design

### From Neel Nanda

Figures should tell a coherent story even if the reader skips the text. Many readers DO skip the text initially.

### Design Principles

1. **Figure 1 is crucial**: Often the first thing readers examine after abstract
2. **Self-contained captions**: Reader should understand figure without main text
3. **No title inside figure**: The caption serves this function (ICML/NeurIPS rule)
4. **Vector graphics**: PDF/EPS for plots, PNG (600 DPI) only for photographs

### Accessibility Requirements

8% of men have color vision deficiency. Your figures must work for them.

**Solutions**:
- Use colorblind-safe palettes: Okabe-Ito or Paul Tol
- Avoid red-green combinations
- Verify figures work in grayscale
- Use different line styles (solid, dashed, dotted) in addition to colors

### Tools

```python
# SciencePlots: Publication-ready styles
import matplotlib.pyplot as plt
plt.style.use(['science', 'ieee'])

# Or for Nature-style
plt.style.use(['science', 'nature'])
```

---

## Common Mistakes to Avoid

### Structure Mistakes

| Mistake | Solution |
|---------|----------|
| Introduction too long (>1.5 pages) | Move background to Related Work |
| Methods buried (after page 3) | Front-load contribution, cut intro |
| Missing contribution bullets | Add 2-4 specific, falsifiable claims |
| Experiments without explicit claims | State what each experiment tests |

### Writing Mistakes

| Mistake | Solution |
|---------|----------|
| Generic abstract opening | Start with your specific contribution |
| Inconsistent terminology | Choose one term per concept |
| Passive voice overuse | Use active voice: "We show" not "It is shown" |
| Hedging everywhere | Be confident unless genuinely uncertain |

### Figure Mistakes

| Mistake | Solution |
|---------|----------|
| Raster graphics for plots | Use vector (PDF/EPS) |
| Red-green color scheme | Use colorblind-safe palette |
| Title inside figure | Put title in caption |
| Captions require main text | Make captions self-contained |

### Citation Mistakes

| Mistake | Solution |
|---------|----------|
| Paper-by-paper Related Work | Organize methodologically |
| Missing relevant citations | Reviewers authored papers—cite generously |
| AI-generated citations | Always verify via APIs |
| Inconsistent citation format | Use BibLaTeX with consistent keys |

---

## Pre-Submission Checklist

Before submitting, verify:

**Narrative**:
- [ ] Can state contribution in one sentence
- [ ] Three pillars (What/Why/So What) clear in intro
- [ ] Every experiment supports a specific claim

**Structure**:
- [ ] Abstract follows 5-sentence formula
- [ ] Introduction ≤1.5 pages
- [ ] Methods start by page 2-3
- [ ] 2-4 contribution bullets included
- [ ] Limitations section present

**Writing**:
- [ ] Consistent terminology throughout
- [ ] No generic opening sentences
- [ ] Hedging removed unless necessary
- [ ] All figures have self-contained captions

**Technical**:
- [ ] All citations verified via API
- [ ] Error bars included with methodology
- [ ] Compute resources documented
- [ ] Code/data availability stated




### Sources

# Source Bibliography

This document lists all authoritative sources used to build this skill, organized by topic.

---

## Writing Philosophy & Guides

### Primary Sources (Must-Read)

| Source | Author | URL | Key Contribution |
|--------|--------|-----|------------------|
| **Highly Opinionated Advice on How to Write ML Papers** | Neel Nanda | [Alignment Forum](https://www.alignmentforum.org/posts/eJGptPbbFPZGLpjsp/highly-opinionated-advice-on-how-to-write-ml-papers) | Narrative framework, "What/Why/So What", time allocation |
| **How to Write ML Papers** | Sebastian Farquhar (DeepMind) | [Blog](https://sebastianfarquhar.com/on-research/2024/11/04/how_to_write_ml_papers/) | 5-sentence abstract formula, structure templates |
| **A Survival Guide to a PhD** | Andrej Karpathy | [Blog](http://karpathy.github.io/2016/09/07/phd/) | Paper structure recipe, contribution framing |
| **Heuristics for Scientific Writing** | Zachary Lipton (CMU) | [Blog](https://www.approximatelycorrect.com/2018/01/29/heuristics-technical-scientific-writing-machine-learning-perspective/) | Word choice, section balance, intensifier warnings |
| **Advice for Authors** | Jacob Steinhardt (UC Berkeley) | [Blog](https://jsteinhardt.stat.berkeley.edu/blog/advice-for-authors) | Precision over brevity, consistent terminology |
| **Easy Paper Writing Tips** | Ethan Perez (Anthropic) | [Blog](https://ethanperez.net/easy-paper-writing-tips/) | Micro-level tips, apostrophe unfolding, clarity tricks |

### Foundational Scientific Writing

| Source | Author | URL | Key Contribution |
|--------|--------|-----|------------------|
| **The Science of Scientific Writing** | Gopen & Swan | [PDF](https://cseweb.ucsd.edu/~swanson/papers/science-of-writing.pdf) | Topic/stress positions, old-before-new, 7 principles |
| **Summary of Science of Scientific Writing** | Lawrence Crowl | [Summary](https://www.crowl.org/Lawrence/writing/GopenSwan90.html) | Condensed version of Gopen & Swan |

### Additional Resources

| Source | URL | Key Contribution |
|--------|-----|------------------|
| How To Write A Research Paper In ML | [Blog](https://grigorisg9gr.github.io/machine%20learning/research%20paper/how-to-write-a-research-paper-in-machine-learning/) | Practical walkthrough, LaTeX tips |
| A Recipe for Training Neural Networks | [Karpathy Blog](http://karpathy.github.io/2019/04/25/recipe/) | Debugging methodology that translates to paper structure |
| ICML Paper Writing Best Practices | [ICML](https://icml.cc/Conferences/2022/BestPractices) | Official venue guidance |
| Bill Freeman's Writing Slides | [MIT](https://billf.mit.edu/sites/default/files/documents/cvprPapers.pdf) | Visual guide to paper structure |

---

## Official Conference Guidelines

### NeurIPS

| Document | URL | Purpose |
|----------|-----|---------|
| Paper Checklist Guidelines | [NeurIPS](https://neurips.cc/public/guides/PaperChecklist) | 16-item mandatory checklist |
| Reviewer Guidelines 2025 | [NeurIPS](https://neurips.cc/Conferences/2025/ReviewerGuidelines) | Evaluation criteria, scoring |
| Style Files | [NeurIPS](https://neurips.cc/Conferences/2025/PaperInformation/StyleFiles) | LaTeX templates |

### ICML

| Document | URL | Purpose |
|----------|-----|---------|
| Paper Guidelines | [ICML](https://icml.cc/Conferences/2024/PaperGuidelines) | Submission requirements |
| Reviewer Instructions 2025 | [ICML](https://icml.cc/Conferences/2025/ReviewerInstructions) | Review form, evaluation |
| Style & Author Instructions | [ICML](https://icml.cc/Conferences/2022/StyleAuthorInstructions) | Formatting specifications |

### ICLR

| Document | URL | Purpose |
|----------|-----|---------|
| Author Guide 2026 | [ICLR](https://iclr.cc/Conferences/2026/AuthorGuide) | Submission requirements, LLM disclosure |
| Reviewer Guide 2025 | [ICLR](https://iclr.cc/Conferences/2025/ReviewerGuide) | Review process, evaluation |

### ACL/EMNLP

| Document | URL | Purpose |
|----------|-----|---------|
| ACL Style Files | [GitHub](https://github.com/acl-org/acl-style-files) | LaTeX templates |
| ACL Rolling Review | [ARR](https://aclrollingreview.org/) | Submission process |

### AAAI

| Document | URL | Purpose |
|----------|-----|---------|
| Author Kit 2026 | [AAAI](https://aaai.org/authorkit26/) | Templates and guidelines |

### COLM

| Document | URL | Purpose |
|----------|-----|---------|
| Template | [GitHub](https://github.com/COLM-org/Template) | LaTeX templates |

---

## Citation APIs & Tools

### APIs

| API | Documentation | Best For |
|-----|---------------|----------|
| **Semantic Scholar** | [Docs](https://api.semanticscholar.org/api-docs/) | ML/AI papers, citation graphs |
| **CrossRef** | [Docs](https://www.crossref.org/documentation/retrieve-metadata/rest-api/) | DOI lookup, BibTeX retrieval |
| **arXiv** | [Docs](https://info.arxiv.org/help/api/basics.html) | Preprints, PDF access |
| **OpenAlex** | [Docs](https://docs.openalex.org/) | Open alternative, bulk access |

### Python Libraries

| Library | Install | Purpose |
|---------|---------|---------|
| `semanticscholar` | `pip install semanticscholar` | Semantic Scholar wrapper |
| `arxiv` | `pip install arxiv` | arXiv search and download |
| `habanero` | `pip install habanero` | CrossRef client |

### Citation Verification

| Tool | URL | Purpose |
|------|-----|---------|
| Citely | [citely.ai](https://citely.ai/citation-checker) | Batch verification |
| ReciteWorks | [reciteworks.com](https://reciteworks.com/) | In-text citation checking |

---

## Visualization & Formatting

### Figure Creation

| Tool | URL | Purpose |
|------|-----|---------|
| PlotNeuralNet | [GitHub](https://github.com/HarisIqbal88/PlotNeuralNet) | TikZ neural network diagrams |
| SciencePlots | [GitHub](https://github.com/garrettj403/SciencePlots) | Publication-ready matplotlib |
| Okabe-Ito Palette | [Reference](https://jfly.uni-koeln.de/color/) | Colorblind-safe colors |

### LaTeX Resources

| Resource | URL | Purpose |
|----------|-----|---------|
| Overleaf Templates | [Overleaf](https://www.overleaf.com/latex/templates) | Online LaTeX editor |
| BibLaTeX Guide | [CTAN](https://ctan.org/pkg/biblatex) | Modern citation management |

---

## Research on AI Writing & Hallucination

| Source | URL | Key Finding |
|--------|-----|-------------|
| AI Hallucinations in Citations | [Enago](https://www.enago.com/academy/ai-hallucinations-research-citations/) | ~40% error rate |
| Hallucination in AI Writing | [PMC](https://pmc.ncbi.nlm.nih.gov/articles/PMC10726751/) | Types of citation errors |
| NeurIPS 2025 AI Report | [ByteIota](https://byteiota.com/neurips-2025-100-ai-hallucinations-slip-through-review/) | 100+ hallucinated citations |

---

## Quick Reference by Topic

### For Narrative & Structure
→ Start with: Neel Nanda, Sebastian Farquhar, Andrej Karpathy

### For Sentence-Level Clarity
→ Start with: Gopen & Swan, Ethan Perez, Zachary Lipton

### For Word Choice & Style
→ Start with: Zachary Lipton, Jacob Steinhardt

### For Conference-Specific Requirements
→ Start with: Official venue guidelines (NeurIPS, ICML, ICLR, ACL)

### For Citation Management
→ Start with: Semantic Scholar API, CrossRef, citation-workflow.md

### For Reviewer Expectations
→ Start with: Venue reviewer guidelines, reviewer-guidelines.md




---

## 🚀 Usage

**Reference this template:** `@skill-ml-paper-writing.md`


**Platform-specific:**
- **GitHub Copilot**: Add to `.github/copilot-instructions.md`
- **Augment Code**: Use `aug context add` command
- **Cursor/Windsurf**: Reference in chat with `@filename`
- **Claude**: Add to Project Knowledge
- **ChatGPT**: Add to Custom GPT configuration
