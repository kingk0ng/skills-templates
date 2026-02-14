---
id: command-build-optimization-commands
type: command
name: Build optimization commands
description: '---

  allowed-tools: Read, Write, Edit, Bash

  argument-hint: [environment] [--analyze] [--preview]

  description: Optimize and deploy Next.js application to Vercel with performance
  monitoring

  ---...'
category: nextjs-vercel
complexity: medium
keywords:
- api
- audit
- database
- deploy
- deployment
- git
- javascript
- optimization
- performance
- react
- security
- test
- testing
- typescript
capabilities: []
token_estimate: 1448
has_scripts: true
languages:
- bash
- javascript
- json
- typescript
---

<!-- Converted from Claude Command Template -->
<!-- Complexity: Medium -->
<!-- Estimated Tokens: ~1,448 -->


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




# Build optimization commands

> ---
allowed-tools: Read, Write, Edit, Bash
argument-hint: [environment] [--analyze] [--preview]
description: Optimize and deploy Next.js application to Vercel with performance monitoring
---...

---
allowed-capabilities: File operations, code editing, terminal access, search
argument-hint: [environment] [--analyze] [--preview]
description: Optimize and deploy Next.js application to Vercel with performance monitoring
---

## Vercel Deployment Optimization

**Target Environment**: $ARGUMENTS

## Current Deployment State

- Project directory: !`pwd`
- Git status: !`git status --porcelain`
- Current branch: !`git branch --show-current`
- Vercel project status: !`vercel --version 2>/dev/null || echo "Vercel CLI not installed"`
- Build output: !`ls -la .next/ 2>/dev/null || echo "No build found"`

## Configuration Analysis

### Project Configuration
- Next.js config: @next.config.js
- Vercel config: @vercel.json (if exists)
- Package.json: @package.json
- Environment variables: @.env.local (if exists)
- Environment example: @.env.example (if exists)

### Vercel Configuration
Analyze and optimize `vercel.json` configuration:
```json
{
  "framework": "nextjs",
  "buildCommand": "npm run build",
  "devCommand": "npm run dev",
  "installCommand": "npm install",
  "regions": ["iad1", "sfo1", "lhr1"],
  "functions": {
    "app/api/**/*.ts": {
      "runtime": "nodejs18.x",
      "maxDuration": 30,
      "memory": 1024
    }
  },
  "crons": [],
  "headers": [
    {
      "source": "/api/(.*)",
      "headers": [
        {
          "key": "Cache-Control",
          "value": "s-maxage=300, stale-while-revalidate=86400"
        }
      ]
    },
    {
      "source": "/(.*)",
      "headers": [
        {
          "key": "X-Frame-Options",
          "value": "DENY"
        },
        {
          "key": "X-Content-Type-Options",
          "value": "nosniff"
        },
        {
          "key": "Referrer-Policy",
          "value": "strict-origin-when-cross-origin"
        }
      ]
    }
  ],
  "redirects": [],
  "rewrites": []
}
```

## Pre-Deployment Optimization

### 1. Build Optimization
Run comprehensive build analysis:
- **Bundle Analysis**: Generate bundle analyzer report
- **Performance Check**: Analyze build output for optimization opportunities
- **Type Checking**: Ensure TypeScript compilation is error-free
- **Lint Check**: Run ESLint for code quality

```bash
# Build optimization commands
npm run build
npm run lint
npm run type-check  # If TypeScript project
```

### 2. Performance Optimization

#### Image Optimization Check
```javascript
// Verify Next.js image configuration
const nextConfig = {
  images: {
    formats: ['image/webp', 'image/avif'],
    deviceSizes: [640, 750, 828, 1080, 1200, 1920, 2048, 3840],
    imageSizes: [16, 32, 48, 64, 96, 128, 256, 384],
    minimumCacheTTL: 31536000,
    dangerouslyAllowSVG: false,
    contentSecurityPolicy: "default-src 'self'; script-src 'none'; sandbox;",
  },
};
```

#### Bundle Analysis
Generate and analyze webpack bundle:
```bash
ANALYZE=true npm run build
# or
npm run build -- --analyze
```

### 3. Environment Configuration

#### Environment Variables Setup
Ensure proper environment variable configuration:
- **Production**: Verify all required environment variables are set in Vercel dashboard
- **Preview**: Configure preview environment variables
- **Development**: Local development environment setup

### 4. Security Headers Optimization
```javascript
// Enhanced security headers in next.config.js
const securityHeaders = [
  {
    key: 'X-DNS-Prefetch-Control',
    value: 'on'
  },
  {
    key: 'Strict-Transport-Security',
    value: 'max-age=63072000; includeSubDomains; preload'
  },
  {
    key: 'X-XSS-Protection',
    value: '1; mode=block'
  },
  {
    key: 'X-Frame-Options',
    value: 'SAMEORIGIN'
  },
  {
    key: 'Permissions-Policy',
    value: 'camera=(), microphone=(), geolocation=()'
  },
  {
    key: 'X-Content-Type-Options',
    value: 'nosniff'
  },
  {
    key: 'Referrer-Policy',
    value: 'origin-when-cross-origin'
  }
];
```

## Deployment Process

### 1. Pre-deployment Checklist
- [ ] Build passes without errors
- [ ] All tests pass (if available)
- [ ] Environment variables configured
- [ ] Security headers implemented
- [ ] Performance metrics baseline established
- [ ] Database migrations complete (if applicable)

### 2. Deployment Commands

#### Production Deployment
```bash
# Deploy to production
vercel --prod

# Deploy with environment variables
vercel --prod --env-file .env.production

# Deploy specific directory
vercel --prod --cwd ./path/to/project
```

#### Preview Deployment
```bash
# Deploy preview from current branch
vercel

# Deploy with custom alias
vercel --alias preview-branch-name.vercel.app
```

#### Deployment with Analytics
```bash
# Deploy with build analytics
ANALYZE=true vercel --prod

# Deploy with performance monitoring
vercel --prod --meta performance=true
```

## Post-Deployment Optimization

### 1. Performance Monitoring Setup

#### Core Web Vitals Tracking
```typescript
// Add to _app.tsx or layout.tsx
import { Analytics } from '@vercel/analytics/react';
import { SpeedInsights } from '@vercel/speed-insights/next';

export default function App({ Component, pageProps }) {
  return (
    <>
      <Component {...pageProps} />
      <Analytics />
      <SpeedInsights />
    </>
  );
}
```

#### Custom Performance Tracking
```typescript
// lib/analytics.ts
export function reportWebVitals({ id, name, label, value }) {
  fetch('/api/analytics', {
    method: 'POST',
    headers: { 'Content-Type': 'application/json' },
    body: JSON.stringify({
      metric: name,
      value: value,
      label: label,
      timestamp: Date.now()
    })
  });
}
```

### 2. Deployment Validation

#### Health Checks
- **Application Health**: Verify application loads correctly
- **API Endpoints**: Test critical API routes
- **Database Connectivity**: Verify database connections (if applicable)
- **External Services**: Test third-party integrations

#### Performance Validation
- **Core Web Vitals**: Check LCP, FID, CLS scores
- **Lighthouse Score**: Run Lighthouse audit
- **Load Testing**: Verify application performance under load
- **Error Monitoring**: Confirm error tracking is working

### 3. Rollback Strategy
```bash
# List recent deployments
vercel list

# Rollback to specific deployment
vercel rollback <deployment-url>

# Alias management for instant rollback
vercel alias set <previous-deployment-url> <production-domain>
```

## Environment-Specific Optimizations

### Production Environment
- **Caching Strategy**: Implement aggressive caching with ISR
- **CDN Configuration**: Optimize asset delivery
- **Database Optimization**: Connection pooling and query optimization
- **Monitoring**: Comprehensive error tracking and performance monitoring

### Preview Environment
- **Feature Testing**: Safe environment for feature validation
- **Stakeholder Review**: Shareable preview URLs
- **Integration Testing**: End-to-end testing environment
- **Performance Benchmarking**: Compare against production metrics

### Development Environment
- **Hot Reloading**: Fast development feedback loop
- **Debug Tools**: Enhanced debugging capabilities
- **Test Data**: Isolated test database and services
- **Development Analytics**: Local performance profiling

## Monitoring and Maintenance

### 1. Deployment Metrics
Track key deployment metrics:
- **Build Time**: Monitor build performance
- **Deploy Time**: Track deployment duration
- **Success Rate**: Monitor deployment success/failure rates
- **Rollback Frequency**: Track rollback events

### 2. Performance Monitoring
- **Real User Monitoring**: Track actual user performance
- **Synthetic Monitoring**: Automated performance testing
- **Error Tracking**: Monitor and alert on errors
- **Uptime Monitoring**: Track application availability

### 3. Cost Optimization
- **Function Duration**: Optimize serverless function execution time
- **Bandwidth Usage**: Monitor and optimize data transfer
- **Build Minutes**: Optimize build processes
- **Edge Requests**: Monitor edge function usage

## Troubleshooting Common Issues

### Build Failures
- Check build logs in Vercel dashboard
- Verify all dependencies are in package.json
- Ensure environment variables are properly set
- Check for TypeScript errors (if applicable)

### Performance Issues
- Analyze bundle size and optimize imports
- Implement proper code splitting
- Optimize images and static assets
- Use Next.js performance optimization features

### Deployment Issues
- Verify Git repository connection
- Check branch protection rules
- Ensure proper access permissions
- Validate deployment configuration

## Success Criteria

Deployment is successful when:
- [ ] Application builds without errors
- [ ] All tests pass (if available)
- [ ] Core Web Vitals scores are optimal (LCP < 2.5s, FID < 100ms, CLS < 0.1)
- [ ] Security headers are properly configured
- [ ] Performance monitoring is active
- [ ] Error tracking is operational
- [ ] Rollback procedures are tested and documented

Provide post-deployment recommendations and next steps for ongoing optimization and monitoring.

---


## 💻 Code Examples


### Example 1

```json
{
  "framework": "nextjs",
  "buildCommand": "npm run build",
  "devCommand": "npm run dev",
  "installCommand": "npm install",
  "regions": ["iad1", "sfo1", "lhr1"],
  "functions": {
    "app/api/**/*.ts": {
      "runtime": "nodejs18.x",
      "maxDuration": 30,
      "memory": 1024
    }
  },
  "crons": [],
  "headers": [
    {
      "source": "/api/(.*)",
      "headers": [
        {
          "key": "Cache-Control",
          "value": "s-maxage=300, stale-while-revalidate=86400"
        }
      ]
    },
    {
      "source": "/(.*)",
      "headers": [
        {
          "key": "X-Frame-Options",
          "value": "DENY"
        },
        {
          "key": "X-Content-Type-Options",
          "value": "nosniff"
        },
        {
          "key": "Referrer-Policy",
          "value": "strict-origin-when-cross-origin"
        }
      ]
    }
  ],
  "redirects": [],
  "rewrites": []
}
```


### Example 2

```bash
# Build optimization commands
npm run build
npm run lint
npm run type-check  # If TypeScript project
```


### Example 3

```javascript
// Verify Next.js image configuration
const nextConfig = {
  images: {
    formats: ['image/webp', 'image/avif'],
    deviceSizes: [640, 750, 828, 1080, 1200, 1920, 2048, 3840],
    imageSizes: [16, 32, 48, 64, 96, 128, 256, 384],
    minimumCacheTTL: 31536000,
    dangerouslyAllowSVG: false,
    contentSecurityPolicy: "default-src 'self'; script-src 'none'; sandbox;",
  },
};
```


### Example 4

```bash
ANALYZE=true npm run build
# or
npm run build -- --analyze
```


### Example 5

```javascript
// Enhanced security headers in next.config.js
const securityHeaders = [
  {
    key: 'X-DNS-Prefetch-Control',
    value: 'on'
  },
  {
    key: 'Strict-Transport-Security',
    value: 'max-age=63072000; includeSubDomains; preload'
  },
  {
    key: 'X-XSS-Protection',
    value: '1; mode=block'
  },
  {
    key: 'X-Frame-Options',
    value: 'SAMEORIGIN'
  },
  {
    key: 'Permissions-Policy',
    value: 'camera=(), microphone=(), geolocation=()'
  },
  {
    key: 'X-Content-Type-Options',
    value: 'nosniff'
  },
  {
    key: 'Referrer-Policy',
    value: 'origin-when-cross-origin'
  }
];
```


### Example 6

```bash
# Deploy to production
vercel --prod

# Deploy with environment variables
vercel --prod --env-file .env.production

# Deploy specific directory
vercel --prod --cwd ./path/to/project
```


### Example 7

```bash
# Deploy preview from current branch
vercel

# Deploy with custom alias
vercel --alias preview-branch-name.vercel.app
```


### Example 8

```bash
# Deploy with build analytics
ANALYZE=true vercel --prod

# Deploy with performance monitoring
vercel --prod --meta performance=true
```


### Example 9

```typescript
// Add to _app.tsx or layout.tsx
import { Analytics } from '@vercel/analytics/react';
import { SpeedInsights } from '@vercel/speed-insights/next';

export default function App({ Component, pageProps }) {
  return (
    <>
      <Component {...pageProps} />
      <Analytics />
      <SpeedInsights />
    </>
  );
}
```


### Example 10

```typescript
// lib/analytics.ts
export function reportWebVitals({ id, name, label, value }) {
  fetch('/api/analytics', {
    method: 'POST',
    headers: { 'Content-Type': 'application/json' },
    body: JSON.stringify({
      metric: name,
      value: value,
      label: label,
      timestamp: Date.now()
    })
  });
}
```


### Example 11

```bash
# List recent deployments
vercel list

# Rollback to specific deployment
vercel rollback <deployment-url>

# Alias management for instant rollback
vercel alias set <previous-deployment-url> <production-domain>
```


---

## 🚀 Usage

**Reference this template:** `@command-build-optimization-commands.md`


**Platform-specific:**
- **GitHub Copilot**: Add to `.github/copilot-instructions.md`
- **Augment Code**: Use `aug context add` command
- **Cursor/Windsurf**: Reference in chat with `@filename`
- **Claude**: Add to Project Knowledge
- **ChatGPT**: Add to Custom GPT configuration
