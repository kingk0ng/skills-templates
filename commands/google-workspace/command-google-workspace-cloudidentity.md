---
id: command-google-workspace-cloudidentity
type: command
name: Google Workspace Cloudidentity
description: '---

  allowed-tools: Bash, Read, Write, Edit

  argument-hint: [resource] [method] [flags]

  description: Google Cloud Identity: Manage identity groups and memberships.

  ---...'
category: google-workspace
complexity: medium
keywords:
- api
- github
- security
capabilities: []
token_estimate: 1146
has_scripts: true
languages:
- bash
---

<!-- Converted from Claude Command Template -->
<!-- Complexity: Medium -->
<!-- Estimated Tokens: ~1,146 -->


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




# Google Workspace Cloudidentity

> ---
allowed-tools: Bash, Read, Write, Edit
argument-hint: [resource] [method] [flags]
description: Google Cloud Identity: Manage identity groups and memberships.
---...

---
allowed-capabilities: File operations, code editing, terminal access, search
argument-hint: [resource] [method] [flags]
description: Google Cloud Identity: Manage identity groups and memberships.
---

# Google Workspace Cloudidentity

Execute Google Workspace Cloudidentity operations: $ARGUMENTS

## Prerequisites

- Google Workspace CLI (`gws`) must be installed
- Authentication configured: Run `gws auth status` to verify
- Review `gws cloudidentity --help` for all available commands

## Available Resources and Methods

# cloudidentity (v1)

> **PREREQUISITE:** Read `../gws-shared/SKILL.md` for auth, global flags, and security rules. If missing, run `gws generate-skills` to create it.

```bash
gws cloudidentity <resource> <method> [flags]
```

## API Resources

### customers

  - `userinvitations` — Operations on the 'userinvitations' resource

### devices

  - `cancelWipe` — Cancels an unfinished device wipe. This operation can be used to cancel device wipe in the gap between the wipe operation returning success and the device being wiped. This operation is possible when the device is in a "pending wipe" state. The device enters the "pending wipe" state when a wipe device command is issued, but has not yet been sent to the device. The cancel wipe will fail if the wipe command has already been issued to the device.
  - `create` — Creates a device. Only company-owned device may be created. **Note**: This method is available only to customers who have one of the following SKUs: Enterprise Standard, Enterprise Plus, Enterprise for Education, and Cloud Identity Premium
  - `delete` — Deletes the specified device.
  - `get` — Retrieves the specified device.
  - `list` — Lists/Searches devices.
  - `wipe` — Wipes all data on the specified device.
  - `deviceUsers` — Operations on the 'deviceUsers' resource

### groups

  - `create` — Creates a Group.
  - `delete` — Deletes a `Group`.
  - `get` — Retrieves a `Group`.
  - `getSecuritySettings` — Get Security Settings
  - `list` — Lists the `Group` resources under a customer or namespace.
  - `lookup` — Looks up the [resource name](https://cloud.google.com/apis/design/resource_names) of a `Group` by its `EntityKey`.
  - `patch` — Updates a `Group`.
  - `search` — Searches for `Group` resources matching a specified query.
  - `updateSecuritySettings` — Update Security Settings
  - `memberships` — Operations on the 'memberships' resource

### inboundOidcSsoProfiles

  - `create` — Creates an InboundOidcSsoProfile for a customer. When the target customer has enabled [Multi-party approval for sensitive actions](https://support.google.com/a/answer/13790448), the `Operation` in the response will have `"done": false`, it will not have a response, and the metadata will have `"state": "awaiting-multi-party-approval"`.
  - `delete` — Deletes an InboundOidcSsoProfile.
  - `get` — Gets an InboundOidcSsoProfile.
  - `list` — Lists InboundOidcSsoProfile objects for a Google enterprise customer.
  - `patch` — Updates an InboundOidcSsoProfile. When the target customer has enabled [Multi-party approval for sensitive actions](https://support.google.com/a/answer/13790448), the `Operation` in the response will have `"done": false`, it will not have a response, and the metadata will have `"state": "awaiting-multi-party-approval"`.

### inboundSamlSsoProfiles

  - `create` — Creates an InboundSamlSsoProfile for a customer. When the target customer has enabled [Multi-party approval for sensitive actions](https://support.google.com/a/answer/13790448), the `Operation` in the response will have `"done": false`, it will not have a response, and the metadata will have `"state": "awaiting-multi-party-approval"`.
  - `delete` — Deletes an InboundSamlSsoProfile.
  - `get` — Gets an InboundSamlSsoProfile.
  - `list` — Lists InboundSamlSsoProfiles for a customer.
  - `patch` — Updates an InboundSamlSsoProfile. When the target customer has enabled [Multi-party approval for sensitive actions](https://support.google.com/a/answer/13790448), the `Operation` in the response will have `"done": false`, it will not have a response, and the metadata will have `"state": "awaiting-multi-party-approval"`.
  - `idpCredentials` — Operations on the 'idpCredentials' resource

### inboundSsoAssignments

  - `create` — Creates an InboundSsoAssignment for users and devices in a `Customer` under a given `Group` or `OrgUnit`.
  - `delete` — Deletes an InboundSsoAssignment. To disable SSO, Create (or Update) an assignment that has `sso_mode` == `SSO_OFF`.
  - `get` — Gets an InboundSsoAssignment.
  - `list` — Lists the InboundSsoAssignments for a `Customer`.
  - `patch` — Updates an InboundSsoAssignment. The body of this request is the `inbound_sso_assignment` field and the `update_mask` is relative to that. For example: a PATCH to `/v1/inboundSsoAssignments/0abcdefg1234567&update_mask=rank` with a body of `{ "rank": 1 }` moves that (presumably group-targeted) SSO assignment to the highest priority and shifts any other group-targeted assignments down in priority.

### policies

  - `get` — Get a policy.
  - `list` — List policies.

## Discovering Commands

Before calling any API method, inspect it:

```bash
# Browse resources and methods
gws cloudidentity --help

# Inspect a method's required params, types, and defaults
gws schema cloudidentity.<resource>.<method>
```

Use `gws schema` output to build your `--params` and `--json` flags.

## Usage

```bash
# List available resources and methods
gws cloudidentity --help

# Inspect method schema before calling
gws schema cloudidentity.<resource>.<method>

# Execute command with arguments
gws cloudidentity $ARGUMENTS
```

## Task

Execute the requested Cloudidentity operation: $ARGUMENTS

1. **Verify Prerequisites**
   - Check `gws` is installed: `gws --version`
   - Verify authentication: `gws auth status`
   - Review available commands: `gws cloudidentity --help`

2. **Inspect Method Schema**
   - Before calling any method, inspect its parameters
   - Use `gws schema` to understand required fields
   - Review parameter types and constraints

3. **Execute Operation**
   - Build command with appropriate flags
   - Use `--params` for query/path parameters
   - Use `--json` for request body
   - Handle pagination with `--max-results` or `--page-token`

4. **Error Handling**
   - Check command output for errors
   - Review API quotas and rate limits
   - Handle authentication issues
   - Retry transient failures

---

**License**: Apache License 2.0
**Source**: [Google Workspace CLI](https://github.com/googleworkspace/cli)
**Original Skill**: `gws-cloudidentity`


---


## 💻 Code Examples


### Example 1

```bash
gws cloudidentity <resource> <method> [flags]
```


### Example 2

```bash
# Browse resources and methods
gws cloudidentity --help

# Inspect a method's required params, types, and defaults
gws schema cloudidentity.<resource>.<method>
```


### Example 3

```bash
# List available resources and methods
gws cloudidentity --help

# Inspect method schema before calling
gws schema cloudidentity.<resource>.<method>

# Execute command with arguments
gws cloudidentity $ARGUMENTS
```


---

## 🚀 Usage

**Reference this template:** `@command-google-workspace-cloudidentity.md`


**Platform-specific:**
- **GitHub Copilot**: Add to `.github/copilot-instructions.md`
- **Augment Code**: Use `aug context add` command
- **Cursor/Windsurf**: Reference in chat with `@filename`
- **Claude**: Add to Project Knowledge
- **ChatGPT**: Add to Custom GPT configuration
