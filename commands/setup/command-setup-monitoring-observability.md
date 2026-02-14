---
id: command-setup-monitoring-observability
type: command
name: Setup Monitoring & Observability
description: '---

  allowed-tools: Read, Write, Edit, Bash

  argument-hint: [monitoring-type] | --metrics | --logging | --tracing | --full-stack

  description: Setup comprehensive monitoring and observability with metric...'
category: setup
complexity: medium
keywords:
- docker
- kubernetes
- optimization
- performance
- security
capabilities: []
token_estimate: 295
---

<!-- Converted from Claude Command Template -->
<!-- Complexity: Medium -->
<!-- Estimated Tokens: ~295 -->


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




# Setup Monitoring & Observability

> ---
allowed-tools: Read, Write, Edit, Bash
argument-hint: [monitoring-type] | --metrics | --logging | --tracing | --full-stack
description: Setup comprehensive monitoring and observability with metric...

---
allowed-capabilities: File operations, code editing, terminal access, search
argument-hint: [monitoring-type] | --metrics | --logging | --tracing | --full-stack
description: Setup comprehensive monitoring and observability with metrics, logging, tracing, and alerting
---

# Setup Monitoring & Observability

Setup comprehensive monitoring and observability infrastructure: **$ARGUMENTS**

## Current Application State

- Application type: @package.json or @requirements.txt (detect framework and services)
- Existing monitoring: !`find . -name "*prometheus*" -o -name "*grafana*" -o -name "*jaeger*" | wc -l`
- Infrastructure: @docker-compose.yml or @kubernetes/ or cloud platform detection
- Logging setup: !`grep -r "winston\|logging\|console.log" src/ 2>/dev/null | wc -l`

## Task

Implement production-ready monitoring and observability with comprehensive insights:

**Monitoring Type**: Use $ARGUMENTS to focus on metrics, logging, distributed tracing, or complete observability stack

**Observability Stack**:
1. **Metrics Collection** - Application metrics, infrastructure monitoring, business KPIs, custom dashboards
2. **Logging Infrastructure** - Centralized logging, structured logs, log aggregation, search capabilities
3. **Distributed Tracing** - Request tracing, performance analysis, bottleneck identification, service dependencies
4. **Alerting System** - Smart alerts, escalation policies, notification channels, incident management
5. **Performance Monitoring** - APM integration, real-user monitoring, synthetic monitoring, SLA tracking
6. **Analytics & Reports** - Usage analytics, performance trends, capacity planning, business insights

**Platform Integration**: Prometheus, Grafana, ELK Stack, Jaeger, DataDog, New Relic, cloud-native solutions.

**Production Features**: High availability, data retention policies, security controls, cost optimization.

**Output**: Complete observability platform with real-time monitoring, intelligent alerting, and comprehensive analytics dashboards.

---

## 🚀 Usage

**Reference this template:** `@command-setup-monitoring-observability.md`


**Platform-specific:**
- **GitHub Copilot**: Add to `.github/copilot-instructions.md`
- **Augment Code**: Use `aug context add` command
- **Cursor/Windsurf**: Reference in chat with `@filename`
- **Claude**: Add to Project Knowledge
- **ChatGPT**: Add to Custom GPT configuration
