---
id: skill-sora
type: skill
name: sora
description: Use when the user asks to generate, remix, poll, list, download, or delete
  Sora videos via OpenAI’s video API using the bundled CLI (`scripts/sora.py`), including
  requests like “generate AI video,” “Sora,” “video remix,” “download video/thumbnail/spritesheet,”
  and batch video generation; requires `OPENAI_API_KEY` and Sora API access.
category: video
complexity: medium
keywords:
- api
- python
- rust
capabilities: []
token_estimate: 1475
has_references: true
reference_count: 8
---

<!-- Converted from Claude Skill Template -->
<!-- Complexity: Medium -->
<!-- Estimated Tokens: ~1,475 -->
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




# sora

> Use when the user asks to generate, remix, poll, list, download, or delete Sora videos via OpenAI’s video API using the bundled CLI (`scripts/sora.py`), including requests like “generate AI video,” “Sora,” “video remix,” “download video/thumbnail/spritesheet,” and batch video generation; requires `OPENAI_API_KEY` and Sora API access.

# Sora Video Generation Skill

Creates or manages short video clips for the current project (product demos, marketing spots, cinematic shots, UI mocks). Defaults to `sora-2` and a structured prompt augmentation workflow, and prefers the bundled CLI for deterministic runs. Note: `$sora` is a skill tag in prompts, not a shell command.

## When to use
- Generate a new video clip from a prompt
- Remix an existing video by ID
- Poll status, list jobs, or download assets (video/thumbnail/spritesheet)
- Batch runs (many prompts or variants)

## Decision tree (create vs remix vs status/download vs batch)
- If the user has a **video id** and wants a change → **remix**
- If the user has a **video id** and wants status or assets → **status/poll/download**
- If the user needs many prompts/assets → **create-batch**
- If the user asks for two versions with a small change (same shot, different subject/detail) → **create** the base, then **remix** for the variation
- Otherwise → **create** (or **create-and-poll** if they need a ready asset in one step)

## Workflow
1. Decide intent: create vs remix vs status/download vs batch.
2. Collect inputs: prompt, model, size, seconds, and any input reference image.
3. If batch: write a temporary JSONL under tmp/ (one job per line), run once, then delete the JSONL.
4. Prefer CLI augmentation flags (`--use-case`, `--scene`, `--camera`, etc.) instead of pre-writing a structured prompt. If you already produced a structured prompt file, pass `--no-augment` to avoid double-wrapping.
5. Run the bundled CLI (`scripts/sora.py`) with sensible defaults (see references/cli.md). For long prompts, prefer `--prompt-file` to avoid shell-escaping issues; pair with `--no-augment` if the prompt is already structured.
6. For async jobs, poll until complete (or use create-and-poll).
7. Download assets (video/thumbnail/spritesheet) and save locally.
8. Remove intermediate files created during invocation (for example `prompt.txt`, `remix_job.json`, temp JSONL). If the sandbox blocks `rm`, skip cleanup or truncate the files without surfacing an error.
9. Iterate with a single targeted change per prompt.

## Authentication
- `OPENAI_API_KEY` must be set for live API calls.

If the key is missing, give the user these steps:
1. Create an API key in the OpenAI platform UI: https://platform.openai.com/api-keys
2. Set `OPENAI_API_KEY` as an environment variable in their system.
3. Offer to guide them through setting the environment variable for their OS/shell if needed.
- Never ask the user to paste the full key in chat. Ask them to set it locally and confirm when ready.

## Defaults & rules
- Default model: `sora-2` (use `sora-2-pro` for higher fidelity).
- Default size: `1280x720`.
- Default seconds: `4` (allowed: "4", "8", "12" as strings).
- Always set size and seconds via API params; prose will not change them.
- Use the OpenAI Python SDK (`openai` package); do not use raw HTTP.
- Require `OPENAI_API_KEY` before any live API call.
- If uv cache permissions fail, set `UV_CACHE_DIR=/tmp/uv-cache`.
- Input reference images must be jpg/png/webp and should match target size.
- Download URLs expire after about 1 hour; copy assets to your own storage.
- Prefer the bundled CLI and **never modify** `scripts/sora.py` unless the user asks.
- Sora can generate audio; if a user requests voiceover/audio, specify it explicitly in the `Audio:` and `Dialogue:` lines and keep it short.

## API limitations
- Models are limited to `sora-2` and `sora-2-pro`.
- API access to Sora models requires an organization-verified account.
- Duration is limited to 4/8/12 seconds and must be set via the `seconds` parameter.
- The API expects `seconds` as a string enum ("4", "8", "12").
- Output sizes are limited by model (see `references/video-api.md` for the supported sizes).
- Video creation is async; you must poll for completion before downloading.
- Rate limits apply by usage tier (do not list specific limits).
- Content restrictions are enforced by the API (see Guardrails below).

## Guardrails (must enforce)
- Only content suitable for audiences under 18.
- No copyrighted characters or copyrighted music.
- No real people (including public figures).
- Input images with human faces are rejected.

## Prompt augmentation
Reformat prompts into a structured, production-oriented spec. Only make implicit details explicit; do not invent new creative requirements.

Template (include only relevant lines):
```
Use case: <where the clip will be used>
Primary request: <user's main prompt>
Scene/background: <location, time of day, atmosphere>
Subject: <main subject>
Action: <single clear action>
Camera: <shot type, angle, motion>
Lighting/mood: <lighting + mood>
Color palette: <3-5 color anchors>
Style/format: <film/animation/format cues>
Timing/beats: <counts or beats>
Audio: <ambient cue / music / voiceover if requested>
Text (verbatim): "<exact text>"
Dialogue:
<dialogue>
- Speaker: "Short line."
</dialogue>
Constraints: <must keep/must avoid>
Avoid: <negative constraints>
```

Augmentation rules:
- Keep it short; add only details the user already implied or provided elsewhere.
- For remixes, explicitly list invariants ("same shot, change only X").
- If any critical detail is missing and blocks success, ask a question; otherwise proceed.
- If you pass a structured prompt file to the CLI, add `--no-augment` to avoid the tool re-wrapping it.

## Examples

### Generation example (single shot)
```
Use case: product teaser
Primary request: a close-up of a matte black camera on a pedestal
Action: slow 30-degree orbit over 4 seconds
Camera: 85mm, shallow depth of field, gentle handheld drift
Lighting/mood: soft key light, subtle rim, premium studio feel
Constraints: no logos, no text
```

### Remix example (invariants)
```
Primary request: same shot and framing, switch palette to teal/sand/rust with warmer backlight
Constraints: keep the subject and camera move unchanged
```

## Prompting best practices (short list)
- One main action + one camera move per shot.
- Use counts or beats for timing ("two steps, pause, turn").
- Keep text short and the camera locked-off for UI or on-screen text.
- Add a brief avoid line when artifacts appear (flicker, jitter, fast motion).
- Shorter prompts are more creative; longer prompts are more controlled.
- Put dialogue in a dedicated block; keep lines short for 4-8s clips.
- State invariants explicitly for remixes (same shot, same camera move).
- Iterate with single-change follow-ups to preserve continuity.

## Guidance by asset type
Use these modules when the request is for a specific artifact. They provide targeted templates and defaults.
- Cinematic shots: `references/cinematic-shots.md`
- Social ads: `references/social-ads.md`

## CLI + environment notes
- CLI commands + examples: `references/cli.md`
- API parameter quick reference: `references/video-api.md`
- Prompting guidance: `references/prompting.md`
- Sample prompts: `references/sample-prompts.md`
- Troubleshooting: `references/troubleshooting.md`
- Network/sandbox tips: `references/codex-network.md`

## Reference map
- **`references/cli.md`**: how to run create/poll/remix/download/batch via `scripts/sora.py`.
- **`references/video-api.md`**: API-level knobs (models, sizes, duration, variants, status).
- **`references/prompting.md`**: prompt structure and iteration guidance.
- **`references/sample-prompts.md`**: copy/paste prompt recipes (examples only; no extra theory).
- **`references/cinematic-shots.md`**: templates for filmic shots.
- **`references/social-ads.md`**: templates for short social ad beats.
- **`references/troubleshooting.md`**: common errors and fixes.
- **`references/codex-network.md`**: network/approval troubleshooting.


---


## 📚 Reference Materials


### Cli

# CLI reference (`scripts/sora.py`)

This file contains the command catalog for the bundled video generation CLI. Keep `SKILL.md` overview-first; put verbose CLI details here.

## What this CLI does
- `create`: create a new video job (async)
- `create-and-poll`: create a job, poll until complete, optionally download
- `poll`: wait for an existing job to finish
- `status`: retrieve job status/details
- `download`: download video/thumbnail/spritesheet
- `list`: list recent jobs
- `delete`: delete a job
- `remix`: remix a completed video
- `create-batch`: create multiple jobs from a JSONL file

Real API calls require **network access** + `OPENAI_API_KEY`. `--dry-run` does not.

## Quick start (works from any repo)
Set a stable path to the skill CLI (default `CODEX_HOME` is `~/.codex`):

```
export CODEX_HOME="${CODEX_HOME:-$HOME/.codex}"
export SORA_CLI="$CODEX_HOME/skills/sora/scripts/sora.py"
```

If you're in this repo, you can set the path directly:

```
# Use repo root (tests run from output/* so $PWD is not the root)
export SORA_CLI="$(git rev-parse --show-toplevel)/<path-to-skill>/scripts/sora.py"
```

If `git` isn't available, set `SORA_CLI` to the absolute path to `<path-to-skill>/scripts/sora.py`.

If uv cache fails with permission errors, set a writable cache:

```
export UV_CACHE_DIR="/tmp/uv-cache"
```

Dry-run (no API call; no network required; does not require the `openai` package):

```
python "$SORA_CLI" create --prompt "Test" --dry-run
```

Preflight a full prompt without running the API:

```
python "$SORA_CLI" create --prompt-file prompt.txt --dry-run --json-out out/request.json
```

Create a job (requires `OPENAI_API_KEY` + network):

```
uv run --with openai python "$SORA_CLI" create \
  --model sora-2 \
  --prompt "Wide tracking shot of a teal coupe on a desert highway" \
  --size 1280x720 \
  --seconds 8
```

Create from a prompt file (avoids shell-escaping issues for multi-line prompts):

```
cat > prompt.txt << 'EOF'
Use case: landing page hero
Primary request: a matte black camera on a pedestal
Action: slow 30-degree orbit over 4 seconds
Camera: 85mm, shallow depth of field
Lighting/mood: soft key light, subtle rim
Constraints: no logos, no text
EOF

uv run --with openai python "$SORA_CLI" create \
  --prompt-file prompt.txt \
  --size 1280x720 \
  --seconds 4
```

If your prompt file is already structured (Use case/Scene/Camera/etc), disable tool augmentation:

```
uv run --with openai python "$SORA_CLI" create \
  --prompt-file prompt.txt \
  --no-augment \
  --size 1280x720 \
  --seconds 4
```

Create + poll + download:

```
uv run --with openai python "$SORA_CLI" create-and-poll \
  --model sora-2-pro \
  --prompt "Close-up of a steaming coffee cup on a wooden table" \
  --size 1280x720 \
  --seconds 8 \
  --download \
  --variant video \
  --out coffee.mp4
```

Create + poll + write JSON bundle:

```
uv run --with openai python "$SORA_CLI" create-and-poll \
  --prompt "Minimal product teaser of a matte black camera" \
  --json-out out/coffee-job.json
```

Remix a completed video:

```
uv run --with openai python "$SORA_CLI" remix \
  --id video_abc123 \
  --prompt "Same shot, shift palette to teal/sand/rust with warm backlight."
```

Download a thumbnail or spritesheet:

```
uv run --with openai python "$SORA_CLI" download --id video_abc123 --variant thumbnail --out thumb.webp
uv run --with openai python "$SORA_CLI" download --id video_abc123 --variant spritesheet --out sheet.jpg
```

## Guardrails (important)
- Use `python "$SORA_CLI" ...` (or equivalent full path) for all video work.
- For API calls, prefer `uv run --with openai ...` to avoid missing SDK errors.
- Do **not** create one-off runners unless the user explicitly asks.
- **Never modify** `scripts/sora.py` unless the user asks.

## Defaults (unless overridden by flags)
- Model: `sora-2`
- Size: `1280x720`
- Seconds: `4` (API expects a string enum: "4", "8", "12")
- Variant: `video`
- Poll interval: `10` seconds

## JSON output (`--json-out`)
- For `create`, `status`, `list`, `delete`, `poll`, and `remix`, `--json-out` writes the JSON response to a file.
- For `create-and-poll`, `--json-out` writes a bundle: `{ "create": ..., "final": ... }`.
- If the path has no extension, `.json` is added automatically.
- In `--dry-run`, `--json-out` writes the request preview instead of a response.

## Input reference images
- Must be jpg/png/webp; they should match the target size.
- Provide the path with `--input-reference`.

## Optional deps
Prefer `uv run --with ...` for an out-of-the-box run without changing the current project env; otherwise install into your active env:

```
uv pip install openai
```

## JSONL schema for `create-batch`
Each line is a JSON object (or a raw prompt string). Required key: `prompt`.

Top-level override keys:
- `model`, `size`, `seconds`
- `input_reference` (path)
- `out` (optional output filename for the job JSON)

Prompt augmentation keys (top-level or under `fields`):
- `use_case`, `scene`, `subject`, `action`, `camera`, `style`, `lighting`, `palette`, `audio`, `dialogue`, `text`, `timing`, `constraints`, `negative`

Notes:
- `fields` merges into the prompt augmentation inputs.
- Top-level keys override CLI defaults.
- `seconds` must be one of: "4", "8", "12".

## Common recipes

Create with prompt augmentation fields:

```
uv run --with openai python "$SORA_CLI" create \
  --prompt "A minimal product teaser shot of a matte black camera" \
  --use-case "landing page hero" \
  --camera "85mm, slow orbit" \
  --lighting "soft key, subtle rim" \
  --constraints "no logos, no text"
```

Two-variant workflow (base + remix):

```
# 1) Base clip
uv run --with openai python "$SORA_CLI" create-and-poll \
  --prompt "Ceramic mug on a sunlit wooden table in a cozy cafe" \
  --size 1280x720 --seconds 4 --download --out output.mp4

# 2) Remix with invariant (same shot, change only the drink)
uv run --with openai python "$SORA_CLI" remix \
  --id video_abc123 \
  --prompt "Same shot and framing; replace the mug with an iced americano in a glass, visible ice and condensation."

# 3) Poll and download the remix
uv run --with openai python "$SORA_CLI" poll \
  --id video_def456 --download --out remix.mp4
```

Poll and download after a job finishes:

```
uv run --with openai python "$SORA_CLI" poll --id video_abc123 --download --variant video --out out.mp4
```

Write JSON response to a file:

```
uv run --with openai python "$SORA_CLI" status --id video_abc123 --json-out out/status.json
```

Batch create (JSONL input):

```
mkdir -p tmp/sora
cat > tmp/sora/prompts.jsonl << 'EOB'
{"prompt":"A neon-lit rainy alley, slow dolly-in","seconds":"4"}
{"prompt":"A warm sunrise over a misty lake, gentle pan","seconds":"8",
 "fields":{"camera":"35mm, slow pan","lighting":"soft dawn light"}}
EOB

uv run --with openai python "$SORA_CLI" create-batch --input tmp/sora/prompts.jsonl --out-dir out --concurrency 3

# Cleanup (recommended)
rm -f tmp/sora/prompts.jsonl
```

Notes:
- `create-batch` writes one JSON response per job under `--out-dir`.
- Output names default to `NNN-<prompt-slug>.json`.
- Use `--concurrency` to control parallelism (default `3`). Higher concurrency can hit rate limits.
- Treat the JSONL file as temporary: write it under `tmp/` and delete it after the run (do not commit it). If `rm` is blocked in your sandbox, skip cleanup or truncate the file.

## CLI notes
- Supported sizes depend on model (see `references/video-api.md`).
- Seconds are limited to 4, 8, or 12.
- Download URLs expire after about 1 hour; copy assets to your own storage.
- In CI/sandboxes where long-running commands time out, prefer `create` + `poll` (or add `--timeout`).

## See also
- API parameter quick reference: `references/video-api.md`
- Prompt structure and examples: `references/prompting.md`
- Sample prompts: `references/sample-prompts.md`
- Troubleshooting: `references/troubleshooting.md`




### Codex Network

# Codex network approvals / sandbox notes

This guidance is intentionally isolated from `SKILL.md` because it can vary by environment and may become stale. Prefer the defaults in your environment when in doubt.

## Why am I asked to approve every video generation call?
Video generation uses the OpenAI Video API, so the CLI needs outbound network access. In many Codex setups, network access is disabled by default (especially under stricter sandbox modes), and/or the approval policy may require confirmation before networked commands run.

## How do I reduce repeated approval prompts (network)?
If you trust the repo and want fewer prompts, enable network access for the relevant sandbox mode and relax the approval policy.

Example `~/.codex/config.toml` pattern:

```
approval_policy = "never"
sandbox_mode = "workspace-write"

[sandbox_workspace_write]
network_access = true
```

Or for a single session:

```
codex --sandbox workspace-write --ask-for-approval never
```

## Safety note
Use caution: enabling network and disabling approvals reduces friction but increases risk if you run untrusted code or work in an untrusted repository.




### Cinematic Shots

# Cinematic shot templates

Use these for filmic, mood-forward clips. Keep one subject, one action, one camera move.

## Shot grammar (pick one)
- Static wide: locked-off, slow atmosphere changes
- Dolly-in: slow push toward subject
- Dolly-out: reveal more context
- Orbit: 15-45 degree arc around subject
- Lateral move: smooth left-right slide
- Crane: subtle vertical rise
- Handheld drift: gentle, controlled sway

## Default template
```
Use case: cinematic shot
Primary request: <subject + setting>
Scene/background: <location, time of day, atmosphere>
Subject: <main subject>
Action: <one clear action>
Camera: <shot type, lens, motion>
Lighting/mood: <key light + mood>
Color palette: <3-5 anchors>
Style/format: filmic, natural grain
Constraints: no logos, no text, no people
Avoid: jitter; flicker; oversharpening
```

## Example: moody exterior
```
Use case: cinematic shot
Primary request: a lone cabin on a cliff above the sea
Scene/background: foggy coastline at dawn, drifting mist
Subject: small wooden cabin with warm window glow
Action: light fog rolls past the cabin
Camera: slow dolly-in, 35mm, steady
Lighting/mood: moody, soft dawn light, subtle contrast
Color palette: deep blue, slate, warm amber
Constraints: no logos, no text, no people
```

## Example: intimate detail
```
Use case: cinematic detail
Primary request: close-up of a vinyl record spinning
Scene/background: dim room, soft lamp glow
Subject: record grooves and stylus
Action: slow rotation, subtle dust motes
Camera: macro, locked-off
Lighting/mood: warm, low-key, soft highlights
Color palette: warm amber, deep brown, charcoal
Constraints: no logos, no text
```




### Prompting

# Prompting best practices (Sora)

## Contents
- [Mindset & tradeoffs](#mindset--tradeoffs)
- [API-controlled params](#api-controlled-params)
- [Structure](#structure)
- [Specificity](#specificity)
- [Style & visual cues](#style--visual-cues)
- [Camera & composition](#camera--composition)
- [Motion & timing](#motion--timing)
- [Lighting & palette](#lighting--palette)
- [Character continuity](#character-continuity)
- [Multi-shot prompts](#multi-shot-prompts)
- [Ultra-detailed briefs](#ultra-detailed-briefs)
- [Image input](#image-input)
- [Constraints & invariants](#constraints--invariants)
- [Text, dialogue & audio](#text-dialogue--audio)
- [Avoiding artifacts](#avoiding-artifacts)
- [Remixing](#remixing)
- [Iterate deliberately](#iterate-deliberately)

## Mindset & tradeoffs
- Treat the prompt like a cinematography brief, not a contract.
- The same prompt can yield different results; rerun for variants.
- Short prompts give more creative freedom; longer prompts give more control.
- Shorter clips tend to follow instructions better; consider stitching two 4s clips instead of a single 8s if precision matters.

## API-controlled params
- Model, size, and seconds are controlled by API params, not prose.
- Put desired duration in the `seconds` param; the prompt cannot make a clip longer.

## Structure
- Use short labeled lines; omit sections that do not matter.
- Keep one main subject and one main action.
- Put timing in beats or counts if it matters.
- If you prefer a prose-first template, use:
```
<Prose scene description in plain language. Describe subject, setting, time of day, and key visual details.>

Cinematography:
Camera shot: <framing + angle>
Mood: <tone>

Actions:
- <clear action beat>
- <clear action beat>

Dialogue:
<short lines if needed>
```

## Specificity
- Name the subject and materials (metal, fabric, glass).
- Use camera language (lens, angle, shot type) for stability.
- Describe the environment with time of day and atmosphere.

## Style & visual cues
- Set style early (e.g., "1970s film", "IMAX-scale", "16mm black-and-white").
- Use visible nouns and verbs, not vague adjectives.
- Weak: "A beautiful street at night."
- Strong: "Wet asphalt, zebra crosswalk, neon signs reflecting in puddles."

## Camera & composition
- Prefer one camera move: dolly, orbit, lateral slide, or locked-off.
- Straight-on framing is best for UI and text.
- For close-ups, use longer lenses (85mm+); for wide scenes, 24-35mm.
- Depth of field is a strong lever: shallow for subject isolation, deep for context.
- Example framings: wide establishing, medium close-up, aerial wide, low angle.
- Example camera motions: slow tilt, gentle handheld drift, smooth lateral slide.

## Motion & timing
- Use short beats: "0-2s", "2-4s", "4-6s".
- Keep actions sequential, not simultaneous.
- For 4s clips, limit to 1-2 beats.
- Describe actions as counts or steps when possible (e.g., "takes four steps, pauses, turns in the final second").

## Lighting & palette
- Describe light quality and direction (soft window light, hard rim, backlight).
- Name 3-5 palette anchors to stabilize color across shots.
- If continuity matters, keep lighting logic consistent across clips.

## Character continuity
- Keep character descriptors consistent across shots; reuse phrasing.
- Avoid mixing competing traits that can shift identity or pose.

## Multi-shot prompts
- You can describe multiple shots in one prompt, but keep each shot block distinct.
- For each shot, specify one camera setup, one action, one lighting recipe.
- Treat each shot as a creative unit you can later edit or stitch.

## Ultra-detailed briefs
- Use when you need a specific, filmic look or strict continuity.
- Call out format/look, lensing/filters, grade/palette, lighting direction, texture, and sound.
- If needed, include a short shot list with timing beats.

## Image input
- Use an input image to lock composition, character design, or set dressing.
- The input image should match the target size and be jpg/png/webp.
- The image anchors the first frame; the prompt describes what happens next.
- If you lack a reference, generate one first and pass it as `input_reference`.

## Constraints & invariants
- State what must not change: "same shot", "same framing", "keep background".
- Repeat invariants in every remix to reduce drift.

## Text, dialogue & audio
- Keep text short and specific; quote exact strings.
- Specify placement and avoid motion blur.
- For dialogue, use a dedicated block and keep lines short.
- Label speakers consistently for multi-character scenes.
- If silent, you can still add a small ambient sound cue to set rhythm.
- Sora can generate audio; include an `Audio:` line and a short dialogue block when needed.
- As a rule of thumb, 4s clips fit 1-2 short lines; 8s clips can handle a few more.

Example:
```
Audio: soft ambient café noise, clear warm voiceover
Dialogue:
<dialogue>
- Speaker: "Let's get started."
</dialogue>
```

## Avoiding artifacts
- Avoid multiple actions in 4-8 seconds.
- Keep camera motion smooth and limited.
- Add explicit negatives when needed: "avoid flicker", "avoid jitter", "no fast motion".

## Remixing
- Change one thing at a time: palette, lighting, or action.
- Keep camera and subject consistent unless the change requests otherwise.
- If a shot misfires, simplify: freeze the camera, reduce action, clear background, then add complexity back in.

## Iterate deliberately
- Start simple, then add one constraint per iteration.
- If results look chaotic, reduce motion and simplify the scene.
- When a result is close, pin it as a reference and describe only the tweak.




### Social Ads

# Social ad templates (4-8s)

Short clips work best with clear beats. Use 2-3 beats and keep text minimal.

## Default template
```
Use case: social ad
Primary request: <ad concept>
Scene/background: <simple backdrop>
Subject: <product or scene>
Action: beat 1 (0-2s) <action>; beat 2 (2-4s) <action>; beat 3 (4-6s) <action>
Camera: <shot type + motion>
Lighting/mood: <mood>
Text (verbatim): "<short headline>", "<short subhead>"
Constraints: no logos; keep text legible; avoid fast motion
```

## Example: product benefit
```
Use case: social ad
Primary request: a compact humidifier emphasizing quiet operation
Scene/background: minimal bedroom nightstand
Subject: matte white humidifier with soft vapor
Action: beat 1 (0-2s) vapor begins; beat 2 (2-4s) soft glow turns on; beat 3 (4-6s) device slides to center
Camera: 50mm, gentle push-in
Lighting/mood: calm, warm night light
Text (verbatim): "Quiet mist", "Sleep better"
Constraints: no logos; text must be legible; avoid harsh highlights
```

## Example: before/after
```
Use case: social ad
Primary request: before/after of a cluttered desk becoming tidy
Scene/background: home office desk, neutral wall
Subject: desk surface, organizer tray
Action: beat 1 (0-2s) cluttered desk; beat 2 (2-4s) quick tidy motion; beat 3 (4-6s) clean desk with organizer
Camera: top-down, locked-off
Lighting/mood: soft daylight
Text (verbatim): "Before", "After"
Constraints: no logos; keep motion minimal; avoid blur
```




### Video Api

# Sora Video API quick reference

Keep this file short; the full docs live in the OpenAI platform docs.

## Models
- sora-2: faster, flexible iteration
- sora-2-pro: higher fidelity, slower, more expensive

## Sizes (by model)
- sora-2: 1280x720, 720x1280
- sora-2-pro: 1280x720, 720x1280, 1024x1792, 1792x1024
Note: higher resolutions generally yield better detail, texture, and motion consistency.

## Duration
- seconds: "4", "8", "12" (string enum; set via API param; prose will not change clip length)
Shorter clips tend to follow instructions more reliably; consider stitching multiple 4s clips for precision.

## Input reference
- Optional `input_reference` image (jpg/png/webp).
- Input reference should match the target size.

## Jobs and status
- Create is async. Status values: queued, in_progress, completed, failed.
- Prefer polling every 10-20s or use webhooks in production.

## Endpoints (conceptual)
- POST /videos: create a job
- GET /videos/{id}: retrieve status
- GET /videos/{id}/content: download video data
- GET /videos: list
- DELETE /videos/{id}: delete
- POST /videos/{id}/remix: remix a completed job

## Download variants
- video (mp4)
- thumbnail (webp)
- spritesheet (jpg)

Download URLs expire after about 1 hour; copy files to your own storage for retention.

## Guardrails (content restrictions)
- Only content suitable for audiences under 18
- No copyrighted characters or copyrighted music
- No real people (including public figures)
- Input images with human faces are currently rejected




### Sample Prompts

# Sample prompts (copy/paste)

Use these as starting points. Keep user-provided requirements and constraints; do not invent new creative elements.

For prompting principles (structure, invariants, iteration), see `references/prompting.md`.

## Contents
- [Product teaser (single shot)](#product-teaser-single-shot)
- [UI demo (screen recording style)](#ui-demo-screen-recording-style)
- [Cinematic detail shot](#cinematic-detail-shot)
- [Social ad (6s with beats)](#social-ad-6s-with-beats)
- [Motion graphics explainer](#motion-graphics-explainer)
- [Ambient loop (atmosphere)](#ambient-loop-atmosphere)

## Product teaser (single shot)
```
Use case: product teaser
Primary request: close-up of a matte black wireless speaker on a stone pedestal
Scene/background: dark studio cyclorama, subtle haze
Subject: compact speaker with soft fabric texture
Action: slow 20-degree orbit over 4 seconds
Camera: 85mm, shallow depth of field, steady dolly
Lighting/mood: soft key, gentle rim, premium studio feel
Color palette: charcoal, slate, warm amber accents
Constraints: no logos, no text
Avoid: harsh bloom; oversharpening; clutter
```

## UI demo (screen recording style)
```
Use case: UI product demo
Primary request: a clean mobile budgeting app demo showing a weekly spend chart
Scene/background: neutral gradient backdrop
Subject: smartphone UI, centered, screen content crisp and legible
Action: tap the "Add expense" button, modal opens, amount typed, save
Camera: locked-off, straight-on, no tilt
Lighting/mood: soft studio light, minimal reflections
Color palette: off-white, slate, mint accent
Text (verbatim): "Add expense", "$24.50", "Groceries"
Constraints: no brand logos; keep UI text readable; avoid motion blur
```

## Cinematic detail shot
```
Use case: cinematic product detail
Primary request: macro shot of raindrops sliding across a car hood
Scene/background: night city bokeh, soft rain mist
Subject: glossy hood surface with water beads
Action: slow push-in over 4 seconds
Camera: 100mm macro, shallow depth of field
Lighting/mood: moody, high-contrast reflections, soft speculars
Color palette: deep navy, teal, silver highlights
Constraints: no logos, no text
Avoid: flicker; unstable reflections; excessive noise
```

## Social ad (6s with beats)
```
Use case: social ad
Primary request: minimal coffee subscription ad with three quick beats
Scene/background: warm kitchen counter, morning light
Subject: ceramic mug, coffee bag, steam
Action: beat 1 (0-2s) pour coffee; beat 2 (2-4s) steam rises; beat 3 (4-6s) mug slides to center
Camera: 50mm, gentle handheld drift
Lighting/mood: warm, cozy, natural light
Text (verbatim): "Fresh roast" (top-left), "Weekly delivery" (bottom-right)
Constraints: no logos; text must be legible; avoid fast motion
```

## Motion graphics explainer
```
Use case: explainer clip
Primary request: clean motion-graphics animation showing data flowing into a dashboard
Scene/background: soft gradient background
Subject: abstract nodes and lines, simple dashboard cards
Action: nodes connect, data pulses, cards fill with charts
Camera: locked-off, no depth, flat design
Lighting/mood: minimal, modern
Color palette: off-white, graphite, teal, coral accents
Constraints: no logos; keep shapes simple; avoid heavy texture
```

## Ambient loop (atmosphere)
```
Use case: ambient background loop
Primary request: fog drifting through a pine forest at dawn
Scene/background: tall pines, soft fog layers, distant hills
Subject: drifting fog and light rays
Action: slow lateral drift, subtle light change
Camera: wide, locked-off, no tilt
Lighting/mood: calm, soft dawn light
Color palette: muted greens, cool gray, pale gold
Constraints: no text, no logos, no people
Avoid: fast motion; flicker; abrupt lighting shifts
```




### Troubleshooting

# Troubleshooting

## Job fails with size or seconds errors
- Cause: size not supported by model, or seconds not in 4/8/12.
- Fix: match size to model; use only "4", "8", or "12" seconds (see `references/video-api.md`).
- If you see `invalid_type` for seconds, update `scripts/sora.py` or pass a string value for `--seconds`.

## openai SDK not installed
- Cause: running `python "$SORA_CLI" ...` without the OpenAI SDK available.
- Fix: run with `uv run --with openai python "$SORA_CLI" ...` instead of using pip directly.

## uv cache permission error
- Cause: uv cache directory is not writable in CI or sandboxed environments.
- Fix: set `UV_CACHE_DIR=/tmp/uv-cache` (or another writable path) before running `uv`.

## Prompt shell escaping issues
- Cause: multi-line prompts or quotes break the shell.
- Fix: use `--prompt-file prompt.txt` (see `references/cli.md` for an example).

## Prompt looks double-wrapped ("Primary request: Use case: ...")
- Cause: you structured the prompt manually but left CLI augmentation on.
- Fix: add `--no-augment` when passing a structured prompt file, or use the CLI fields (`--use-case`, `--scene`, etc.) instead of pre-formatting.

## Input reference rejected
- Cause: file is not jpg/png/webp, or has a human face, or dimensions do not match target size.
- Fix: convert to jpg/png/webp, remove faces, and resize to match `--size`.

## Download fails or returns expired URL
- Cause: download URLs expire after about 1 hour.
- Fix: re-download while the link is fresh; save to your own storage.

## Video completes but looks unstable or flickers
- Cause: multiple actions or aggressive camera motion.
- Fix: reduce to one main action and one camera move; keep beats simple; add constraints like "avoid flicker" or "stable motion".

## Text is unreadable
- Cause: text too long, too small, or moving.
- Fix: shorten text, increase size, keep camera locked-off, and avoid fast motion.

## Remix drifts from the original
- Cause: too many changes requested at once.
- Fix: state invariants explicitly ("same shot and camera move") and change only one element per remix.

## Job stuck in queued/in_progress for a long time
- Cause: temporary queue delays.
- Fix: increase poll timeout, or retry later; avoid high concurrency if you are rate-limited.

## create-and-poll times out in CI/sandbox
- Cause: long-running CLI commands can exceed CI time limits.
- Fix: run `create` (capture the ID) and then `poll` separately, or set `--timeout`.

## Audio or voiceover missing / incorrect
- Cause: audio wasn't explicitly requested, or the dialogue/audio cue was too long or vague.
- Fix: add a clear `Audio:` line and a short `Dialogue:` block.

## Cleanup blocked by sandbox policy
- Cause: some environments block `rm`.
- Fix: skip cleanup, or truncate files instead of deleting.




---

## 🚀 Usage

**Reference this template:** `@skill-sora.md`


**Platform-specific:**
- **GitHub Copilot**: Add to `.github/copilot-instructions.md`
- **Augment Code**: Use `aug context add` command
- **Cursor/Windsurf**: Reference in chat with `@filename`
- **Claude**: Add to Project Knowledge
- **ChatGPT**: Add to Custom GPT configuration
