---
id: command-application-containerization
type: command
name: Application Containerization
description: '---

  allowed-tools: Read, Write, Edit, Bash

  argument-hint: [application-type] | --node | --python | --java | --go | --multi-stage

  description: Containerize application with optimized Docker configurati...'
category: deployment
complexity: medium
keywords:
- ci/cd
- deployment
- docker
- go
- java
- kubernetes
- optimization
- performance
- python
- security
- test
- testing
- vulnerability
capabilities: []
token_estimate: 665
---

<!-- Converted from Claude Command Template -->
<!-- Complexity: Medium -->
<!-- Estimated Tokens: ~665 -->


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




# Application Containerization

> ---
allowed-tools: Read, Write, Edit, Bash
argument-hint: [application-type] | --node | --python | --java | --go | --multi-stage
description: Containerize application with optimized Docker configurati...

---
allowed-capabilities: File operations, code editing, terminal access, search
argument-hint: [application-type] | --node | --python | --java | --go | --multi-stage
description: Containerize application with optimized Docker configuration, security, and multi-stage builds
---

# Application Containerization

Containerize application for deployment: $ARGUMENTS

## Current Application Analysis

- Application type: @package.json or @setup.py or @go.mod or @pom.xml (detect runtime)
- Existing Docker: @Dockerfile or @docker-compose.yml (if exists)
- Dependencies: !`find . -name "*requirements*.txt" -o -name "package*.json" -o -name "go.mod" | head -3`
- Port configuration: !`grep -r "PORT\|listen\|bind" src/ 2>/dev/null | head -3 || echo "Port detection needed"`
- Build capabilities: File operations, code editing, terminal access, search@Makefile or build scripts detection

## Task

Implement production-ready containerization strategy:

1. **Application Analysis and Containerization Strategy**
   - Analyze application architecture and runtime requirements
   - Identify application dependencies and external services
   - Determine optimal base image and runtime environment
   - Plan multi-stage build strategy for optimization
   - Assess security requirements and compliance needs

2. **Dockerfile Creation and Optimization**
   - Create comprehensive Dockerfile with multi-stage builds
   - Select minimal base images (Alpine, distroless, or slim variants)
   - Configure proper layer caching and build optimization
   - Implement security best practices (non-root user, minimal attack surface)
   - Set up proper file permissions and ownership

3. **Build Process Configuration**
   - Configure .dockerignore file to exclude unnecessary files
   - Set up build arguments and environment variables
   - Implement build-time dependency installation and cleanup
   - Configure application bundling and asset optimization
   - Set up proper build context and file structure

4. **Runtime Configuration**
   - Configure application startup and health checks
   - Set up proper signal handling and graceful shutdown
   - Configure logging and output redirection
   - Set up environment-specific configuration management
   - Configure resource limits and performance tuning

5. **Security Hardening**
   - Run application as non-root user with minimal privileges
   - Configure security scanning and vulnerability assessment
   - Implement secrets management and secure credential handling
   - Set up network security and firewall rules
   - Configure security policies and access controls

6. **Docker Compose Configuration**
   - Create docker-compose.yml for local development
   - Configure service dependencies and networking
   - Set up volume mounting and data persistence
   - Configure environment variables and secrets
   - Set up development vs production configurations

7. **Container Orchestration Preparation**
   - Prepare configurations for Kubernetes deployment
   - Create deployment manifests and service definitions
   - Configure ingress and load balancing
   - Set up persistent volumes and storage classes
   - Configure auto-scaling and resource management

8. **Monitoring and Observability**
   - Configure application metrics and health endpoints
   - Set up logging aggregation and centralized logging
   - Configure distributed tracing and monitoring
   - Set up alerting and notification systems
   - Configure performance monitoring and profiling

9. **CI/CD Integration**
   - Configure automated Docker image building
   - Set up image scanning and security validation
   - Configure image registry and artifact management
   - Set up automated deployment pipelines
   - Configure rollback and blue-green deployment strategies

10. **Testing and Validation**
    - Test container builds and functionality
    - Validate security configurations and compliance
    - Test deployment in different environments
    - Validate performance and resource utilization
    - Test backup and disaster recovery procedures
    - Create documentation for container deployment and management

---

## 🚀 Usage

**Reference this template:** `@command-application-containerization.md`


**Platform-specific:**
- **GitHub Copilot**: Add to `.github/copilot-instructions.md`
- **Augment Code**: Use `aug context add` command
- **Cursor/Windsurf**: Reference in chat with `@filename`
- **Claude**: Add to Project Knowledge
- **ChatGPT**: Add to Custom GPT configuration
