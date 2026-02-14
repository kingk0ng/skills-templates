---
id: command-game-testing-framework-automation
type: command
name: Game Testing Framework & Automation
description: '---

  allowed-tools: Read, Write, Edit, Bash

  argument-hint: [test-type] | --unit | --integration | --performance | --automation
  | --comprehensive

  description: Use PROACTIVELY to implement comprehensive ...'
category: game-development
complexity: medium
keywords:
- ci/cd
- github
- gitlab
- performance
- test
- testing
capabilities: []
token_estimate: 711
---

<!-- Converted from Claude Command Template -->
<!-- Complexity: Medium -->
<!-- Estimated Tokens: ~711 -->


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




# Game Testing Framework & Automation

> ---
allowed-tools: Read, Write, Edit, Bash
argument-hint: [test-type] | --unit | --integration | --performance | --automation | --comprehensive
description: Use PROACTIVELY to implement comprehensive ...

---
allowed-capabilities: File operations, code editing, terminal access, search
argument-hint: [test-type] | --unit | --integration | --performance | --automation | --comprehensive
description: Use PROACTIVELY to implement comprehensive game testing frameworks with automated validation, performance testing, and multi-platform verification
---

# Game Testing Framework & Automation

Implement comprehensive game testing framework: $ARGUMENTS

## Current Testing Context

- Game engine: @package.json or detect Unity/Unreal/Godot project files
- Existing tests: !`find . -name "*test*" -o -name "*Test*" | head -10`
- CI/CD setup: @.github/workflows/ or @.gitlab-ci.yml or @Jenkinsfile (if exists)
- Build configs: !`find . -name "*.sln" -o -name "*.csproj" -o -name "build.gradle" | head -3`
- Platform targets: !`grep -r "BuildTarget\|Platform\|Target" . 2>/dev/null | wc -l` target configurations

## Task

Create a comprehensive testing framework for game development with automated validation, performance benchmarks, cross-platform testing, and continuous integration.

## Testing Framework Components

### 1. Unit Testing Infrastructure
- Core game logic and mechanics testing
- Component-based testing for modular systems
- Mock and stub systems for external dependencies
- Data validation and serialization testing
- Mathematical calculations and algorithm verification

### 2. Integration Testing Suite
- Scene loading and transition testing
- Asset loading and management validation
- Save/load system integrity testing
- Networking and multiplayer functionality
- Platform-specific feature integration testing

### 3. Performance & Benchmarking
- Frame rate stability testing across scenarios
- Memory usage profiling and leak detection
- Loading time benchmarks for different content
- Stress testing with high entity counts
- Platform-specific performance validation

### 4. Automated Gameplay Testing
- AI behavior validation and regression testing
- User input simulation and response verification
- Game state progression and checkpoint validation
- Balance testing for game mechanics
- Procedural content generation validation

## Testing Categories

### Functional Testing
- Core gameplay mechanics validation
- User interface responsiveness and functionality
- Audio system integration and spatial audio
- Physics simulation accuracy and stability
- Animation system timing and blending

### Compatibility Testing
- Multi-platform build verification
- Device-specific feature testing (mobile, console, VR)
- Different screen resolutions and aspect ratios
- Hardware capability scaling and adaptation
- Operating system compatibility validation

### Regression Testing
- Automated testing for code changes impact
- Asset modification impact on game performance
- Save file compatibility across versions
- Feature functionality preservation
- Performance regression detection

### User Experience Testing
- Accessibility features validation
- Control scheme testing across input devices
- Localization and internationalization testing
- Tutorial and onboarding flow validation
- Error handling and recovery testing

## Deliverables

1. **Testing Framework Setup**
   - Test runner configuration and automation
   - Mock systems and test data generation
   - Continuous integration pipeline integration
   - Test reporting and metrics collection

2. **Test Suite Implementation**
   - Unit tests for core game systems
   - Integration tests for complex interactions
   - Performance benchmarks and monitoring
   - Automated gameplay validation scripts

3. **Platform Testing Strategy**
   - Device-specific test configurations
   - Cloud testing and device farm integration
   - Performance validation across target platforms
   - Compatibility testing automation

4. **Monitoring & Reporting**
   - Test results dashboard and visualization
   - Performance regression tracking
   - Code coverage analysis and reporting
   - Automated test failure investigation

## Implementation Guidelines

Integrate with game engine testing tools and establish CI/CD pipelines for automated testing. Ensure scalable test architecture that grows with project complexity and team size.

---

## 🚀 Usage

**Reference this template:** `@command-game-testing-framework-automation.md`


**Platform-specific:**
- **GitHub Copilot**: Add to `.github/copilot-instructions.md`
- **Augment Code**: Use `aug context add` command
- **Cursor/Windsurf**: Reference in chat with `@filename`
- **Claude**: Add to Project Knowledge
- **ChatGPT**: Add to Custom GPT configuration
