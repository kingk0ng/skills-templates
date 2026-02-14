---
id: command-game-performance-analysis-optimization
type: command
name: Game Performance Analysis & Optimization
description: '---

  allowed-tools: Read, Write, Edit, Bash

  argument-hint: [profile-type] | --fps | --memory | --rendering | --comprehensive

  description: Use PROACTIVELY to analyze game performance bottlenecks and gen...'
category: game-development
complexity: medium
keywords:
- audit
- optimization
- performance
- testing
capabilities: []
token_estimate: 488
---

<!-- Converted from Claude Command Template -->
<!-- Complexity: Medium -->
<!-- Estimated Tokens: ~488 -->


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




# Game Performance Analysis & Optimization

> ---
allowed-tools: Read, Write, Edit, Bash
argument-hint: [profile-type] | --fps | --memory | --rendering | --comprehensive
description: Use PROACTIVELY to analyze game performance bottlenecks and gen...

---
allowed-capabilities: File operations, code editing, terminal access, search
argument-hint: [profile-type] | --fps | --memory | --rendering | --comprehensive
description: Use PROACTIVELY to analyze game performance bottlenecks and generate optimization recommendations across multiple platforms
---

# Game Performance Analysis & Optimization

Analyze game performance and generate optimization recommendations: $ARGUMENTS

## Current Performance Context

- Game engine: @package.json or detect Unity/Unreal/Godot project files
- Platform targets: !`find . -name "*.pbxproj" -o -name "*.gradle" -o -name "*.vcxproj" | head -3`
- Asset pipeline: !`find . -name "*.meta" -o -name "*.asset" | wc -l` game assets
- Build configs: !`grep -r "BuildTarget\|Platform" . 2>/dev/null | wc -l` platform configurations
- Performance logs: !`find . -name "*profile*" -o -name "*perf*" | head -5`

## Task

Create comprehensive performance analysis with automated bottleneck detection, optimization suggestions, and platform-specific recommendations for game development projects.

## Performance Analysis Areas

### 1. Frame Rate & Rendering Performance
- Analyze draw calls and batching efficiency
- Identify overdraw and fillrate bottlenecks
- Review shader complexity and optimization opportunities
- Evaluate mesh and texture optimization potential
- Check lighting and shadow rendering performance

### 2. Memory Usage Analysis
- Memory allocation patterns and potential leaks
- Texture memory usage and compression opportunities
- Audio memory optimization suggestions
- Object pooling and garbage collection analysis
- Platform-specific memory constraints evaluation

### 3. CPU Performance Profiling
- Script execution bottlenecks identification
- Physics simulation optimization opportunities
- AI and pathfinding performance analysis
- Animation system efficiency review
- Threading and parallelization recommendations

### 4. Platform-Specific Optimization
- Mobile performance considerations (battery, thermal throttling)
- Console-specific optimization guidelines
- PC hardware scaling recommendations
- VR performance requirements and optimizations
- Web/WebGL specific performance considerations

## Deliverables

1. **Performance Audit Report**
   - Current performance metrics and benchmarks
   - Identified bottlenecks with severity ratings
   - Platform-specific performance analysis

2. **Optimization Recommendations**
   - Prioritized optimization suggestions
   - Implementation difficulty and impact assessment
   - Code and asset optimization guidelines

3. **Monitoring Setup**
   - Performance monitoring implementation
   - Key metrics tracking configuration
   - Automated performance regression detection

4. **Testing Strategy**
   - Performance testing procedures
   - Target device testing recommendations
   - Continuous performance monitoring setup

## Implementation Guidelines

Follow game engine best practices and target platform requirements. Generate actionable recommendations with clear implementation steps and expected performance improvements.

---

## 🚀 Usage

**Reference this template:** `@command-game-performance-analysis-optimization.md`


**Platform-specific:**
- **GitHub Copilot**: Add to `.github/copilot-instructions.md`
- **Augment Code**: Use `aug context add` command
- **Cursor/Windsurf**: Reference in chat with `@filename`
- **Claude**: Add to Project Knowledge
- **ChatGPT**: Add to Custom GPT configuration
