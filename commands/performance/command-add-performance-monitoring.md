---
id: command-add-performance-monitoring
type: command
name: Add Performance Monitoring
description: '---

  allowed-tools: Read, Bash, Grep, Glob

  argument-hint: [monitoring-type] | --apm | --rum | --custom

  description: Setup comprehensive application performance monitoring with metrics,
  alerting, and ob...'
category: performance
complexity: medium
keywords:
- database
- optimization
- performance
- test
- testing
capabilities: []
token_estimate: 626
---

<!-- Converted from Claude Command Template -->
<!-- Complexity: Medium -->
<!-- Estimated Tokens: ~626 -->


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




# Add Performance Monitoring

> ---
allowed-tools: Read, Bash, Grep, Glob
argument-hint: [monitoring-type] | --apm | --rum | --custom
description: Setup comprehensive application performance monitoring with metrics, alerting, and ob...

---
allowed-capabilities: File operations, code editing, terminal access, search
argument-hint: [monitoring-type] | --apm | --rum | --custom
description: Setup comprehensive application performance monitoring with metrics, alerting, and observability
---

# Add Performance Monitoring

Setup application performance monitoring: **$ARGUMENTS**

## Instructions

1. **Performance Monitoring Strategy**
   - Define key performance indicators (KPIs) and service level objectives (SLOs)
   - Identify critical user journeys and performance bottlenecks
   - Plan monitoring architecture and data collection strategy
   - Assess existing monitoring infrastructure and integration points
   - Define alerting thresholds and escalation procedures

2. **Application Performance Monitoring (APM)**
   - Set up comprehensive APM solution (New Relic, Datadog, AppDynamics)
   - Configure distributed tracing for request lifecycle visibility
   - Implement custom metrics and performance tracking
   - Set up transaction monitoring and error tracking
   - Configure performance profiling and diagnostics

3. **Real User Monitoring (RUM)**
   - Implement client-side performance tracking and web vitals monitoring
   - Set up user experience metrics collection (LCP, FID, CLS, TTFB)
   - Configure custom performance metrics for user interactions
   - Monitor page load performance and resource loading
   - Track user journey performance across different devices

4. **Server Performance Monitoring**
   - Monitor system metrics (CPU, memory, disk, network)
   - Set up process and application-level monitoring
   - Configure event loop lag and garbage collection monitoring
   - Implement custom server performance metrics
   - Monitor resource utilization and capacity planning

5. **Database Performance Monitoring**
   - Track database query performance and slow query identification
   - Monitor database connection pool utilization
   - Set up database performance metrics and alerting
   - Implement query execution plan analysis
   - Monitor database resource usage and optimization opportunities

6. **Error Tracking and Monitoring**
   - Implement comprehensive error tracking (Sentry, Bugsnag, Rollbar)
   - Configure error categorization and impact analysis
   - Set up error alerting and notification systems
   - Track error trends and resolution metrics
   - Implement error context and debugging information

7. **Custom Metrics and Dashboards**
   - Implement business metrics tracking (Prometheus, StatsD)
   - Create performance dashboards and visualizations
   - Configure custom alerting rules and thresholds
   - Set up performance trend analysis and reporting
   - Implement performance regression detection

8. **Alerting and Notification System**
   - Configure intelligent alerting based on performance thresholds
   - Set up multi-channel notifications (email, Slack, PagerDuty)
   - Implement alert escalation and on-call procedures
   - Configure alert fatigue prevention and noise reduction
   - Set up performance incident management workflows

9. **Performance Testing Integration**
   - Integrate monitoring with load testing and performance testing
   - Set up continuous performance testing and monitoring
   - Configure performance baseline tracking and comparison
   - Implement performance test result analysis and reporting
   - Monitor performance under different load scenarios

10. **Performance Optimization Recommendations**
    - Generate actionable performance insights and recommendations
    - Implement automated performance analysis and reporting
    - Set up performance optimization tracking and measurement
    - Configure performance improvement validation
    - Create performance optimization prioritization frameworks

Focus on monitoring strategies that provide actionable insights for performance optimization. Ensure monitoring overhead is minimal and doesn't impact application performance.

---

## 🚀 Usage

**Reference this template:** `@command-add-performance-monitoring.md`


**Platform-specific:**
- **GitHub Copilot**: Add to `.github/copilot-instructions.md`
- **Augment Code**: Use `aug context add` command
- **Cursor/Windsurf**: Reference in chat with `@filename`
- **Claude**: Add to Project Knowledge
- **ChatGPT**: Add to Custom GPT configuration
