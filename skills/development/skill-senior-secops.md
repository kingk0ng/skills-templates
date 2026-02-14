---
id: skill-senior-secops
type: skill
name: senior-secops
description: Comprehensive SecOps skill for application security, vulnerability management,
  compliance, and secure development practices. Includes security scanning, vulnerability
  assessment, compliance checking, and security automation. Use when implementing
  security controls, conducting security audits, responding to vulnerabilities, or
  ensuring compliance requirements.
category: development
complexity: medium
keywords:
- database
- deployment
- docker
- github
- go
- graphql
- javascript
- k8s
- kubernetes
- optimization
- performance
- python
- react
- rest
- security
- test
- typescript
- vulnerability
capabilities: []
token_estimate: 643
has_references: true
reference_count: 3
---

<!-- Converted from Claude Skill Template -->
<!-- Complexity: Medium -->
<!-- Estimated Tokens: ~643 -->
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




# senior-secops

> Comprehensive SecOps skill for application security, vulnerability management, compliance, and secure development practices. Includes security scanning, vulnerability assessment, compliance checking, and security automation. Use when implementing security controls, conducting security audits, responding to vulnerabilities, or ensuring compliance requirements.

# Senior Secops

Complete toolkit for senior secops with modern tools and best practices.

## Quick Start

### Main Capabilities

This skill provides three core capabilities through automated scripts:

```bash
# Script 1: Security Scanner
python scripts/security_scanner.py [options]

# Script 2: Vulnerability Assessor
python scripts/vulnerability_assessor.py [options]

# Script 3: Compliance Checker
python scripts/compliance_checker.py [options]
```

## Core Capabilities

### 1. Security Scanner

Automated tool for security scanner tasks.

**Features:**
- Automated scaffolding
- Best practices built-in
- Configurable templates
- Quality checks

**Usage:**
```bash
python scripts/security_scanner.py <project-path> [options]
```

### 2. Vulnerability Assessor

Comprehensive analysis and optimization tool.

**Features:**
- Deep analysis
- Performance metrics
- Recommendations
- Automated fixes

**Usage:**
```bash
python scripts/vulnerability_assessor.py <target-path> [--verbose]
```

### 3. Compliance Checker

Advanced tooling for specialized tasks.

**Features:**
- Expert-level automation
- Custom configurations
- Integration ready
- Production-grade output

**Usage:**
```bash
python scripts/compliance_checker.py [arguments] [options]
```

## Reference Documentation

### Security Standards

Comprehensive guide available in `references/security_standards.md`:

- Detailed patterns and practices
- Code examples
- Best practices
- Anti-patterns to avoid
- Real-world scenarios

### Vulnerability Management Guide

Complete workflow documentation in `references/vulnerability_management_guide.md`:

- Step-by-step processes
- Optimization strategies
- Tool integrations
- Performance tuning
- Troubleshooting guide

### Compliance Requirements

Technical reference guide in `references/compliance_requirements.md`:

- Technology stack details
- Configuration examples
- Integration patterns
- Security considerations
- Scalability guidelines

## Tech Stack

**Languages:** TypeScript, JavaScript, Python, Go, Swift, Kotlin
**Frontend:** React, Next.js, React Native, Flutter
**Backend:** Node.js, Express, GraphQL, REST APIs
**Database:** PostgreSQL, Prisma, NeonDB, Supabase
**DevOps:** Docker, Kubernetes, Terraform, GitHub Actions, CircleCI
**Cloud:** AWS, GCP, Azure

## Development Workflow

### 1. Setup and Configuration

```bash
# Install dependencies
npm install
# or
pip install -r requirements.txt

# Configure environment
cp .env.example .env
```

### 2. Run Quality Checks

```bash
# Use the analyzer script
python scripts/vulnerability_assessor.py .

# Review recommendations
# Apply fixes
```

### 3. Implement Best Practices

Follow the patterns and practices documented in:
- `references/security_standards.md`
- `references/vulnerability_management_guide.md`
- `references/compliance_requirements.md`

## Best Practices Summary

### Code Quality
- Follow established patterns
- Write comprehensive tests
- Document decisions
- Review regularly

### Performance
- Measure before optimizing
- Use appropriate caching
- Optimize critical paths
- Monitor in production

### Security
- Validate all inputs
- Use parameterized queries
- Implement proper authentication
- Keep dependencies updated

### Maintainability
- Write clear code
- Use consistent naming
- Add helpful comments
- Keep it simple

## Common Commands

```bash
# Development
npm run dev
npm run build
npm run test
npm run lint

# Analysis
python scripts/vulnerability_assessor.py .
python scripts/compliance_checker.py --analyze

# Deployment
docker build -t app:latest .
docker-compose up -d
kubectl apply -f k8s/
```

## Troubleshooting

### Common Issues

Check the comprehensive troubleshooting section in `references/compliance_requirements.md`.

### Getting Help

- Review reference documentation
- Check script output messages
- Consult tech stack documentation
- Review error logs

## Resources

- Pattern Reference: `references/security_standards.md`
- Workflow Guide: `references/vulnerability_management_guide.md`
- Technical Guide: `references/compliance_requirements.md`
- Tool Scripts: `scripts/` directory


---


## 📚 Reference Materials


### Vulnerability_Management_Guide

# Vulnerability Management Guide

## Overview

This reference guide provides comprehensive information for senior secops.

## Patterns and Practices

### Pattern 1: Best Practice Implementation

**Description:**
Detailed explanation of the pattern.

**When to Use:**
- Scenario 1
- Scenario 2
- Scenario 3

**Implementation:**
```typescript
// Example code implementation
export class Example {
  // Implementation details
}
```

**Benefits:**
- Benefit 1
- Benefit 2
- Benefit 3

**Trade-offs:**
- Consider 1
- Consider 2
- Consider 3

### Pattern 2: Advanced Technique

**Description:**
Another important pattern for senior secops.

**Implementation:**
```typescript
// Advanced example
async function advancedExample() {
  // Code here
}
```

## Guidelines

### Code Organization
- Clear structure
- Logical separation
- Consistent naming
- Proper documentation

### Performance Considerations
- Optimization strategies
- Bottleneck identification
- Monitoring approaches
- Scaling techniques

### Security Best Practices
- Input validation
- Authentication
- Authorization
- Data protection

## Common Patterns

### Pattern A
Implementation details and examples.

### Pattern B
Implementation details and examples.

### Pattern C
Implementation details and examples.

## Anti-Patterns to Avoid

### Anti-Pattern 1
What not to do and why.

### Anti-Pattern 2
What not to do and why.

## Tools and Resources

### Recommended Tools
- Tool 1: Purpose
- Tool 2: Purpose
- Tool 3: Purpose

### Further Reading
- Resource 1
- Resource 2
- Resource 3

## Conclusion

Key takeaways for using this reference guide effectively.




### Compliance_Requirements

# Compliance Requirements

## Overview

This reference guide provides comprehensive information for senior secops.

## Patterns and Practices

### Pattern 1: Best Practice Implementation

**Description:**
Detailed explanation of the pattern.

**When to Use:**
- Scenario 1
- Scenario 2
- Scenario 3

**Implementation:**
```typescript
// Example code implementation
export class Example {
  // Implementation details
}
```

**Benefits:**
- Benefit 1
- Benefit 2
- Benefit 3

**Trade-offs:**
- Consider 1
- Consider 2
- Consider 3

### Pattern 2: Advanced Technique

**Description:**
Another important pattern for senior secops.

**Implementation:**
```typescript
// Advanced example
async function advancedExample() {
  // Code here
}
```

## Guidelines

### Code Organization
- Clear structure
- Logical separation
- Consistent naming
- Proper documentation

### Performance Considerations
- Optimization strategies
- Bottleneck identification
- Monitoring approaches
- Scaling techniques

### Security Best Practices
- Input validation
- Authentication
- Authorization
- Data protection

## Common Patterns

### Pattern A
Implementation details and examples.

### Pattern B
Implementation details and examples.

### Pattern C
Implementation details and examples.

## Anti-Patterns to Avoid

### Anti-Pattern 1
What not to do and why.

### Anti-Pattern 2
What not to do and why.

## Tools and Resources

### Recommended Tools
- Tool 1: Purpose
- Tool 2: Purpose
- Tool 3: Purpose

### Further Reading
- Resource 1
- Resource 2
- Resource 3

## Conclusion

Key takeaways for using this reference guide effectively.




### Security_Standards

# Security Standards

## Overview

This reference guide provides comprehensive information for senior secops.

## Patterns and Practices

### Pattern 1: Best Practice Implementation

**Description:**
Detailed explanation of the pattern.

**When to Use:**
- Scenario 1
- Scenario 2
- Scenario 3

**Implementation:**
```typescript
// Example code implementation
export class Example {
  // Implementation details
}
```

**Benefits:**
- Benefit 1
- Benefit 2
- Benefit 3

**Trade-offs:**
- Consider 1
- Consider 2
- Consider 3

### Pattern 2: Advanced Technique

**Description:**
Another important pattern for senior secops.

**Implementation:**
```typescript
// Advanced example
async function advancedExample() {
  // Code here
}
```

## Guidelines

### Code Organization
- Clear structure
- Logical separation
- Consistent naming
- Proper documentation

### Performance Considerations
- Optimization strategies
- Bottleneck identification
- Monitoring approaches
- Scaling techniques

### Security Best Practices
- Input validation
- Authentication
- Authorization
- Data protection

## Common Patterns

### Pattern A
Implementation details and examples.

### Pattern B
Implementation details and examples.

### Pattern C
Implementation details and examples.

## Anti-Patterns to Avoid

### Anti-Pattern 1
What not to do and why.

### Anti-Pattern 2
What not to do and why.

## Tools and Resources

### Recommended Tools
- Tool 1: Purpose
- Tool 2: Purpose
- Tool 3: Purpose

### Further Reading
- Resource 1
- Resource 2
- Resource 3

## Conclusion

Key takeaways for using this reference guide effectively.




---

## 🚀 Usage

**Reference this template:** `@skill-senior-secops.md`


**Platform-specific:**
- **GitHub Copilot**: Add to `.github/copilot-instructions.md`
- **Augment Code**: Use `aug context add` command
- **Cursor/Windsurf**: Reference in chat with `@filename`
- **Claude**: Add to Project Knowledge
- **ChatGPT**: Add to Custom GPT configuration
