---
id: command-google-workspace-calendar
type: command
name: Google Workspace Calendar
description: '---

  allowed-tools: Bash, Read, Write, Edit

  argument-hint: [resource] [method] [flags]

  description: Google Calendar: Manage calendars and events.

  ---...'
category: google-workspace
complexity: medium
keywords:
- api
- github
- security
capabilities: []
token_estimate: 1133
has_scripts: true
languages:
- bash
---

<!-- Converted from Claude Command Template -->
<!-- Complexity: Medium -->
<!-- Estimated Tokens: ~1,133 -->


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




# Google Workspace Calendar

> ---
allowed-tools: Bash, Read, Write, Edit
argument-hint: [resource] [method] [flags]
description: Google Calendar: Manage calendars and events.
---...

---
allowed-capabilities: File operations, code editing, terminal access, search
argument-hint: [resource] [method] [flags]
description: Google Calendar: Manage calendars and events.
---

# Google Workspace Calendar

Execute Google Workspace Calendar operations: $ARGUMENTS

## Prerequisites

- Google Workspace CLI (`gws`) must be installed
- Authentication configured: Run `gws auth status` to verify
- Review `gws calendar --help` for all available commands

## Available Resources and Methods

# calendar (v3)

> **PREREQUISITE:** Read `../gws-shared/SKILL.md` for auth, global flags, and security rules. If missing, run `gws generate-skills` to create it.

```bash
gws calendar <resource> <method> [flags]
```

## Helper Commands

| Command | Description |
|---------|-------------|
| [`+insert`](../gws-calendar-insert/SKILL.md) | create a new event |
| [`+agenda`](../gws-calendar-agenda/SKILL.md) | Show upcoming events across all calendars |

## API Resources

### acl

  - `delete` — Deletes an access control rule.
  - `get` — Returns an access control rule.
  - `insert` — Creates an access control rule.
  - `list` — Returns the rules in the access control list for the calendar.
  - `patch` — Updates an access control rule. This method supports patch semantics.
  - `update` — Updates an access control rule.
  - `watch` — Watch for changes to ACL resources.

### calendarList

  - `delete` — Removes a calendar from the user's calendar list.
  - `get` — Returns a calendar from the user's calendar list.
  - `insert` — Inserts an existing calendar into the user's calendar list.
  - `list` — Returns the calendars on the user's calendar list.
  - `patch` — Updates an existing calendar on the user's calendar list. This method supports patch semantics.
  - `update` — Updates an existing calendar on the user's calendar list.
  - `watch` — Watch for changes to CalendarList resources.

### calendars

  - `clear` — Clears a primary calendar. This operation deletes all events associated with the primary calendar of an account.
  - `delete` — Deletes a secondary calendar. Use calendars.clear for clearing all events on primary calendars.
  - `get` — Returns metadata for a calendar.
  - `insert` — Creates a secondary calendar.
The authenticated user for the request is made the data owner of the new calendar.

Note: We recommend to authenticate as the intended data owner of the calendar. You can use domain-wide delegation of authority to allow applications to act on behalf of a specific user. Don't use a service account for authentication. If you use a service account for authentication, the service account is the data owner, which can lead to unexpected behavior.
  - `patch` — Updates metadata for a calendar. This method supports patch semantics.
  - `update` — Updates metadata for a calendar.

### channels

  - `stop` — Stop watching resources through this channel

### colors

  - `get` — Returns the color definitions for calendars and events.

### events

  - `delete` — Deletes an event.
  - `get` — Returns an event based on its Google Calendar ID. To retrieve an event using its iCalendar ID, call the events.list method using the iCalUID parameter.
  - `import` — Imports an event. This operation is used to add a private copy of an existing event to a calendar. Only events with an eventType of default may be imported.
Deprecated behavior: If a non-default event is imported, its type will be changed to default and any event-type-specific properties it may have will be dropped.
  - `insert` — Creates an event.
  - `instances` — Returns instances of the specified recurring event.
  - `list` — Returns events on the specified calendar.
  - `move` — Moves an event to another calendar, i.e. changes an event's organizer. Note that only default events can be moved; birthday, focusTime, fromGmail, outOfOffice and workingLocation events cannot be moved.
  - `patch` — Updates an event. This method supports patch semantics.
  - `quickAdd` — Creates an event based on a simple text string.
  - `update` — Updates an event.
  - `watch` — Watch for changes to Events resources.

### freebusy

  - `query` — Returns free/busy information for a set of calendars.

### settings

  - `get` — Returns a single user setting.
  - `list` — Returns all user settings for the authenticated user.
  - `watch` — Watch for changes to Settings resources.

## Discovering Commands

Before calling any API method, inspect it:

```bash
# Browse resources and methods
gws calendar --help

# Inspect a method's required params, types, and defaults
gws schema calendar.<resource>.<method>
```

Use `gws schema` output to build your `--params` and `--json` flags.

## Usage

```bash
# List available resources and methods
gws calendar --help

# Inspect method schema before calling
gws schema calendar.<resource>.<method>

# Execute command with arguments
gws calendar $ARGUMENTS
```

## Task

Execute the requested Calendar operation: $ARGUMENTS

1. **Verify Prerequisites**
   - Check `gws` is installed: `gws --version`
   - Verify authentication: `gws auth status`
   - Review available commands: `gws calendar --help`

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
**Original Skill**: `gws-calendar`


---


## 💻 Code Examples


### Example 1

```bash
gws calendar <resource> <method> [flags]
```


### Example 2

```bash
# Browse resources and methods
gws calendar --help

# Inspect a method's required params, types, and defaults
gws schema calendar.<resource>.<method>
```


### Example 3

```bash
# List available resources and methods
gws calendar --help

# Inspect method schema before calling
gws schema calendar.<resource>.<method>

# Execute command with arguments
gws calendar $ARGUMENTS
```


---

## 🚀 Usage

**Reference this template:** `@command-google-workspace-calendar.md`


**Platform-specific:**
- **GitHub Copilot**: Add to `.github/copilot-instructions.md`
- **Augment Code**: Use `aug context add` command
- **Cursor/Windsurf**: Reference in chat with `@filename`
- **Claude**: Add to Project Knowledge
- **ChatGPT**: Add to Custom GPT configuration
