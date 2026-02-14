---
id: command-setup-cdn-optimization
type: command
name: Setup CDN Optimization
description: '---

  allowed-tools: Read, Bash, Grep, Glob

  argument-hint: [cdn-provider] | --cloudflare | --aws | --fastly

  description: Configure CDN for optimal content delivery, caching, and global performance
  optim...'
category: performance
complexity: medium
keywords:
- api
- deployment
- optimization
- performance
- security
capabilities: []
token_estimate: 663
---

<!-- Converted from Claude Command Template -->
<!-- Complexity: Medium -->
<!-- Estimated Tokens: ~663 -->


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




# Setup CDN Optimization

> ---
allowed-tools: Read, Bash, Grep, Glob
argument-hint: [cdn-provider] | --cloudflare | --aws | --fastly
description: Configure CDN for optimal content delivery, caching, and global performance optim...

---
allowed-capabilities: File operations, code editing, terminal access, search
argument-hint: [cdn-provider] | --cloudflare | --aws | --fastly
description: Configure CDN for optimal content delivery, caching, and global performance optimization
---

# Setup CDN Optimization

Configure CDN for optimal delivery: **$ARGUMENTS**

## Instructions

1. **CDN Strategy and Provider Selection**
   - Analyze application traffic patterns and global user distribution
   - Evaluate CDN providers based on performance, cost, and features
   - Assess content types and specific caching requirements
   - Plan CDN architecture and edge location strategy
   - Define performance and cost optimization goals

2. **CDN Configuration and Setup**
   - Configure CDN with optimal settings for your content types
   - Set up origin servers and failover configurations
   - Configure SSL/TLS certificates and security settings
   - Implement custom domain and DNS configuration
   - Set up monitoring and analytics tracking

3. **Static Asset Optimization**
   - Optimize asset build process for CDN delivery
   - Configure content hashing and versioning strategies
   - Set up asset bundling and code splitting for CDN
   - Implement responsive image delivery and optimization
   - Configure font loading and optimization strategies

4. **Compression and Optimization**
   - Configure Gzip and Brotli compression settings
   - Set up build-time compression for static assets
   - Implement dynamic compression for API responses
   - Configure minification and asset optimization
   - Set up progressive image formats (WebP, AVIF)

5. **Cache Headers and Policies**
   - Design intelligent caching strategies for different content types
   - Configure cache control headers and TTL values
   - Implement ETags and conditional request handling
   - Set up cache hierarchy and multi-tier caching
   - Configure cache warming and preloading strategies

6. **Image Optimization and Delivery**
   - Implement responsive image delivery with multiple formats
   - Set up automatic image compression and optimization
   - Configure lazy loading and progressive image loading
   - Implement image resizing and format conversion
   - Set up WebP and AVIF format support with fallbacks

7. **CDN Purging and Cache Invalidation**
   - Implement intelligent cache invalidation strategies
   - Set up automated purging for deployment pipelines
   - Configure selective purging by tags or patterns
   - Implement real-time cache invalidation for dynamic content
   - Set up cache invalidation monitoring and alerts

8. **Performance Monitoring and Analytics**
   - Set up CDN performance monitoring and metrics tracking
   - Monitor cache hit ratios and bandwidth usage
   - Track response times and error rates across regions
   - Implement real user monitoring for CDN performance
   - Set up alerts for performance degradation

9. **Security and Access Control**
   - Configure CDN security headers and policies
   - Implement hotlink protection and referrer validation
   - Set up DDoS protection and rate limiting
   - Configure geo-blocking and access restrictions
   - Implement secure token authentication for protected content

10. **Cost Optimization and Monitoring**
    - Monitor CDN usage and costs across different tiers
    - Implement cost optimization strategies for bandwidth usage
    - Set up automated cost alerts and budget monitoring
    - Analyze usage patterns for tier optimization
    - Configure cost-effective caching policies

Focus on CDN optimizations that provide the most significant performance improvements for your specific content types and user base. Always measure CDN performance impact and adjust configurations based on real-world usage patterns.

---

## 🚀 Usage

**Reference this template:** `@command-setup-cdn-optimization.md`


**Platform-specific:**
- **GitHub Copilot**: Add to `.github/copilot-instructions.md`
- **Augment Code**: Use `aug context add` command
- **Cursor/Windsurf**: Reference in chat with `@filename`
- **Claude**: Add to Project Knowledge
- **ChatGPT**: Add to Custom GPT configuration
