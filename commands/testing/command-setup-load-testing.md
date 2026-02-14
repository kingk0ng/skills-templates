---
id: command-setup-load-testing
type: command
name: Setup Load Testing
description: '---

  allowed-tools: Read, Write, Edit, Bash

  argument-hint: [testing-type] | --capacity | --stress | --spike | --endurance |
  --volume

  description: Configure comprehensive load testing with performance m...'
category: testing
complexity: medium
keywords:
- api
- ci/cd
- database
- go
- optimization
- performance
- sql
- test
- testing
capabilities: []
token_estimate: 387
---

<!-- Converted from Claude Command Template -->
<!-- Complexity: Medium -->
<!-- Estimated Tokens: ~387 -->


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




# Setup Load Testing

> ---
allowed-tools: Read, Write, Edit, Bash
argument-hint: [testing-type] | --capacity | --stress | --spike | --endurance | --volume
description: Configure comprehensive load testing with performance m...

---
allowed-capabilities: File operations, code editing, terminal access, search
argument-hint: [testing-type] | --capacity | --stress | --spike | --endurance | --volume
description: Configure comprehensive load testing with performance metrics and bottleneck identification
---

# Setup Load Testing

Configure comprehensive load testing with performance analysis and bottleneck identification: **$ARGUMENTS**

## Current Performance Context

- Application type: !`find . -name "server.js" -o -name "app.py" -o -name "main.go" | head -1 && echo "Server application" || echo "Detect app type"`
- API endpoints: !`grep -r "app\\.get\\|app\\.post\\|@RequestMapping" . 2>/dev/null | wc -l` detected endpoints
- Database: !`find . -name "*.sql" -o -name "database.js" | head -1 && echo "Database detected" || echo "No database files"`
- Current monitoring: !`find . -name "prometheus.yml" -o -name "newrelic.js" | head -1 || echo "No monitoring detected"`

## Task

Implement comprehensive load testing with performance optimization and bottleneck analysis:

**Testing Type**: Use $ARGUMENTS to focus on capacity planning, stress testing, spike testing, endurance testing, or volume testing

**Load Testing Framework**:

1. **Strategy & Requirements** - Analyze application architecture, define testing objectives, determine scenarios, identify performance metrics
2. **Tool Selection & Setup** - Choose appropriate tools (k6, Artillery, JMeter, Gatling), install dependencies, configure environments
3. **Test Scenario Design** - Create realistic user scenarios, implement API test scripts, configure data generation, design load patterns
4. **Performance Metrics** - Configure response time monitoring, throughput measurement, error rate tracking, resource utilization monitoring
5. **Infrastructure Setup** - Configure test environments, setup monitoring dashboards, implement result collection, optimize test execution
6. **Analysis & Optimization** - Identify performance bottlenecks, analyze resource constraints, recommend optimizations, track improvements

**Advanced Features**: Distributed load generation, real-time monitoring, automated performance regression detection, CI/CD integration, chaos engineering.

**Quality Assurance**: Test reliability, result accuracy, environment consistency, monitoring completeness.

**Output**: Complete load testing setup with configured scenarios, performance monitoring, bottleneck analysis, and optimization recommendations.


---

## 🚀 Usage

**Reference this template:** `@command-setup-load-testing.md`


**Platform-specific:**
- **GitHub Copilot**: Add to `.github/copilot-instructions.md`
- **Augment Code**: Use `aug context add` command
- **Cursor/Windsurf**: Reference in chat with `@filename`
- **Claude**: Add to Project Knowledge
- **ChatGPT**: Add to Custom GPT configuration
