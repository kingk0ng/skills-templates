---
id: skill-railway-domain
type: skill
name: railway-domain
description: Add, view, or remove domains for Railway services. Use when user wants
  to add a domain, generate a railway domain, check current domains, get the URL for
  a service, or remove a domain.
category: railway
complexity: medium
keywords:
- api
- deploy
- deployment
- graphql
capabilities: []
token_estimate: 495
---

<!-- Converted from Claude Skill Template -->
<!-- Complexity: Medium -->
<!-- Estimated Tokens: ~495 -->


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




# railway-domain

> Add, view, or remove domains for Railway services. Use when user wants to add a domain, generate a railway domain, check current domains, get the URL for a service, or remove a domain.

# Railway Domain Management

Add, view, or remove domains for Railway services.

## When to Use

- User asks to "add a domain", "generate a domain", "get a URL"
- User wants to add a custom domain
- User asks "what's the URL for my service"
- User wants to remove a domain

## Add Railway Domain

Generate a railway-provided domain (max 1 per service):

```bash
railway domain --json
```

For a specific service:
```bash
railway domain --json --service backend
```

### Response
Returns the generated domain URL. Service must have a deployment.

## Add Custom Domain

```bash
railway domain example.com --json
```

### Response
Returns required DNS records:
```json
{
  "domain": "example.com",
  "dnsRecords": [
    { "type": "CNAME", "host": "@", "value": "..." }
  ]
}
```

Tell user to add these records to their DNS provider.

## Read Current Domains

Use railway-environment skill to see configured domains, or query directly:

```graphql
query domains($envId: String!) {
  environment(id: $envId) {
    config(decryptVariables: false)
  }
}
```

Domains are in `config.services.<serviceId>.networking`:
- `serviceDomains` - Railway-provided domains
- `customDomains` - User-provided domains

## Remove Domain

Use railway-environment skill to remove domains:

### Remove custom domain
```json
{
  "services": {
    "<serviceId>": {
      "networking": {
        "customDomains": { "<domainId>": null }
      }
    }
  }
}
```

### Remove railway domain
```json
{
  "services": {
    "<serviceId>": {
      "networking": {
        "serviceDomains": { "<domainId>": null }
      }
    }
  }
}
```

Then use railway-environment skill to apply and commit the change.

## CLI Options

| Flag | Description |
|------|-------------|
| `[DOMAIN]` | Custom domain to add (omit for railway domain) |
| `-p, --port <PORT>` | Port to connect |
| `-s, --service <NAME>` | Target service (defaults to linked) |
| `--json` | JSON output |

## Composability

- **Read domains**: Use railway-environment skill
- **Remove domains**: Use railway-environment skill
- **Apply removal**: Use railway-environment skill
- **Check service**: Use railway-service skill

## Error Handling

### No Service Linked
```
No service linked. Use --service flag or run `railway service` to select one.
```

### Domain Already Exists
```
Service already has a railway-provided domain. Maximum 1 per service.
```

### No Deployment
```
Service has no deployment. Deploy first with `railway up`.
```

### Invalid Domain
```
Invalid domain format. Use a valid domain like "example.com" or "api.example.com".
```


---

## 🚀 Usage

**Reference this template:** `@skill-railway-domain.md`


**Platform-specific:**
- **GitHub Copilot**: Add to `.github/copilot-instructions.md`
- **Augment Code**: Use `aug context add` command
- **Cursor/Windsurf**: Reference in chat with `@filename`
- **Claude**: Add to Project Knowledge
- **ChatGPT**: Add to Custom GPT configuration
