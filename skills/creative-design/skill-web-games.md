---
id: skill-web-games
type: skill
name: web-games
description: Web browser game development principles. Framework selection, WebGPU,
  optimization, PWA.
category: creative-design
complexity: medium
keywords:
- api
- optimization
- performance
capabilities:
- file_reading
- file_writing
- code_editing
- file_search
- text_search
token_estimate: 640
---

<!-- Converted from Claude Skill Template -->
<!-- Complexity: Medium -->
<!-- Estimated Tokens: ~640 -->


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




# web-games

> Web browser game development principles. Framework selection, WebGPU, optimization, PWA.

# Web Browser Game Development

> Framework selection and browser-specific principles.

---

## 1. Framework Selection

### Decision Tree

```
What type of game?
│
├── 2D Game
│   ├── Full game engine features? → Phaser
│   └── Raw rendering power? → PixiJS
│
├── 3D Game
│   ├── Full engine (physics, XR)? → Babylon.js
│   └── Rendering focused? → Three.js
│
└── Hybrid / Canvas
    └── Custom → Raw Canvas/WebGL
```

### Comparison (2025)

| Framework | Type | Best For |
|-----------|------|----------|
| **Phaser 4** | 2D | Full game features |
| **PixiJS 8** | 2D | Rendering, UI |
| **Three.js** | 3D | Visualizations, lightweight |
| **Babylon.js 7** | 3D | Full engine, XR |

---

## 2. WebGPU Adoption

### Browser Support (2025)

| Browser | Support |
|---------|---------|
| Chrome | ✅ Since v113 |
| Edge | ✅ Since v113 |
| Firefox | ✅ Since v131 |
| Safari | ✅ Since 18.0 |
| **Total** | **~73%** global |

### Decision

- **New projects**: Use WebGPU with WebGL fallback
- **Legacy support**: Start with WebGL
- **Feature detection**: Check `navigator.gpu`

---

## 3. Performance Principles

### Browser Constraints

| Constraint | Strategy |
|------------|----------|
| No local file access | Asset bundling, CDN |
| Tab throttling | Pause when hidden |
| Mobile data limits | Compress assets |
| Audio autoplay | Require user interaction |

### Optimization Priority

1. **Asset compression** - KTX2, Draco, WebP
2. **Lazy loading** - Load on demand
3. **Object pooling** - Avoid GC
4. **Draw call batching** - Reduce state changes
5. **Web Workers** - Offload heavy computation

---

## 4. Asset Strategy

### Compression Formats

| Type | Format |
|------|--------|
| Textures | KTX2 + Basis Universal |
| Audio | WebM/Opus (fallback: MP3) |
| 3D Models | glTF + Draco/Meshopt |

### Loading Strategy

| Phase | Load |
|-------|------|
| Startup | Core assets, <2MB |
| Gameplay | Stream on demand |
| Background | Prefetch next level |

---

## 5. PWA for Games

### Benefits

- Offline play
- Install to home screen
- Full screen mode
- Push notifications

### Requirements

- Service worker for caching
- Web app manifest
- HTTPS

---

## 6. Audio Handling

### Browser Requirements

- Audio context requires user interaction
- Create AudioContext on first click/tap
- Resume context if suspended

### Best Practices

- Use Web Audio API
- Pool audio sources
- Preload common sounds
- Compress with WebM/Opus

---

## 7. Anti-Patterns

| ❌ Don't | ✅ Do |
|----------|-------|
| Load all assets upfront | Progressive loading |
| Ignore tab visibility | Pause when hidden |
| Block on audio load | Lazy load audio |
| Skip compression | Compress everything |
| Assume fast connection | Handle slow networks |

---

> **Remember:** Browser is the most accessible platform. Respect its constraints.


---

## 🚀 Usage

**Reference this template:** `@skill-web-games.md`


**Platform-specific:**
- **GitHub Copilot**: Add to `.github/copilot-instructions.md`
- **Augment Code**: Use `aug context add` command
- **Cursor/Windsurf**: Reference in chat with `@filename`
- **Claude**: Add to Project Knowledge
- **ChatGPT**: Add to Custom GPT configuration
