---
id: skill-footballbin-predictions
type: skill
name: footballbin-predictions
description: Get AI-powered match predictions for Premier League and Champions League
  including scores, next goal, and corners.
category: sports
complexity: medium
keywords:
- api
- security
capabilities: []
token_estimate: 338
---

<!-- Converted from Claude Skill Template -->
<!-- Complexity: Medium -->
<!-- Estimated Tokens: ~338 -->


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




# footballbin-predictions

> Get AI-powered match predictions for Premier League and Champions League including scores, next goal, and corners.

# FootballBin Match Predictions

Get AI-powered predictions for Premier League and Champions League matches via the FootballBin MCP API.

## Quick Start

Run `scripts/footballbin.sh` with the following commands:

### Get current matchweek predictions
```bash
scripts/footballbin.sh predictions premier_league
scripts/footballbin.sh predictions champions_league
```

### Get specific matchweek
```bash
scripts/footballbin.sh predictions premier_league 27
```

### Filter by team
```bash
scripts/footballbin.sh predictions premier_league --home arsenal
scripts/footballbin.sh predictions premier_league --away liverpool
scripts/footballbin.sh predictions premier_league --home chelsea --away wolves
```

### List available tools
```bash
scripts/footballbin.sh tools
```

## Supported Leagues

| Input | League |
|-------|--------|
| `premier_league`, `epl`, `pl`, `prem` | Premier League |
| `champions_league`, `ucl`, `cl` | Champions League |

## Supported Team Aliases

Common aliases work: `united` (Man Utd), `city` (Man City), `spurs` (Tottenham), `wolves` (Wolverhampton), `gunners` (Arsenal), `reds` (Liverpool), `blues` (Chelsea), `villa` (Aston Villa), `forest` (Nottingham Forest), `palace` (Crystal Palace), `barca` (Barcelona), `real` (Real Madrid), `bayern` (Bayern Munich), `psg` (PSG), `juve` (Juventus), `inter` (Inter Milan), `bvb` (Dortmund), `atleti` (Atletico Madrid).

## Response Data

Each match prediction includes:
- **Half-time score** (e.g., "1:0")
- **Full-time score** (e.g., "2:1")
- **Next goal scorer** (e.g., "Home,Salah")
- **Corner count** (e.g., "7:4")
- **Key players** with form-based reasoning

## External Endpoints

| URL | Data Sent | Purpose |
|-----|-----------|---------|
| `https://ru7m5svay1.execute-api.eu-central-1.amazonaws.com/prod/mcp` | League, matchweek, team filters | Fetch match predictions |

## Security & Privacy

- No API key required (public endpoint, rate-limited)
- No user data collected or stored
- Read-only: only fetches prediction data
- No secrets or environment variables needed

## Links

- iOS App: https://apps.apple.com/app/footballbin/id6757111871
- Android App: https://play.google.com/store/apps/details?id=com.achan.footballbinandroid


---

## 🚀 Usage

**Reference this template:** `@skill-footballbin-predictions.md`


**Platform-specific:**
- **GitHub Copilot**: Add to `.github/copilot-instructions.md`
- **Augment Code**: Use `aug context add` command
- **Cursor/Windsurf**: Reference in chat with `@filename`
- **Claude**: Add to Project Knowledge
- **ChatGPT**: Add to Custom GPT configuration
