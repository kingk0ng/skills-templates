---
id: skill-jira
type: skill
name: jira
description: Use when the user mentions Jira issues (e.g., "PROJ-123"), asks about
  tickets, wants to create/view/update issues, check sprint status, or manage their
  Jira workflow. Triggers on keywords like "jira", "issue", "ticket", "sprint", "backlog",
  or issue key patterns.
category: ai-research
complexity: medium
keywords:
- github
capabilities: []
token_estimate: 1145
has_references: true
reference_count: 2
---

<!-- Converted from Claude Skill Template -->
<!-- Complexity: Medium -->
<!-- Estimated Tokens: ~1,145 -->
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




# jira

> Use when the user mentions Jira issues (e.g., "PROJ-123"), asks about tickets, wants to create/view/update issues, check sprint status, or manage their Jira workflow. Triggers on keywords like "jira", "issue", "ticket", "sprint", "backlog", or issue key patterns.

# Jira

Natural language interaction with Jira. Supports multiple backends.

## Backend Detection

**Run this check first** to determine which backend to use:

```
1. Check if jira CLI is available:
   → Run: which jira
   → If found: USE CLI BACKEND

2. If no CLI, check for Atlassian MCP:
   → Look for mcp__atlassian__* tools
   → If available: USE MCP BACKEND

3. If neither available:
   → GUIDE USER TO SETUP
```

| Backend | When to Use | Reference |
|---------|-------------|-----------|
| **CLI** | `jira` command available | `references/commands.md` |
| **MCP** | Atlassian MCP tools available | `references/mcp.md` |
| **None** | Neither available | Guide to install CLI |

---

## Quick Reference (CLI)

> Skip this section if using MCP backend.

| Intent | Command |
|--------|---------|
| View issue | `jira issue view ISSUE-KEY` |
| List my issues | `jira issue list -a$(jira me)` |
| My in-progress | `jira issue list -a$(jira me) -s"In Progress"` |
| Create issue | `jira issue create -tType -s"Summary" -b"Description"` |
| Move/transition | `jira issue move ISSUE-KEY "State"` |
| Assign to me | `jira issue assign ISSUE-KEY $(jira me)` |
| Unassign | `jira issue assign ISSUE-KEY x` |
| Add comment | `jira issue comment add ISSUE-KEY -b"Comment text"` |
| Open in browser | `jira open ISSUE-KEY` |
| Current sprint | `jira sprint list --state active` |
| Who am I | `jira me` |

---

## Quick Reference (MCP)

> Skip this section if using CLI backend.

| Intent | MCP Tool |
|--------|----------|
| Search issues | `mcp__atlassian__searchJiraIssuesUsingJql` |
| View issue | `mcp__atlassian__getJiraIssue` |
| Create issue | `mcp__atlassian__createJiraIssue` |
| Update issue | `mcp__atlassian__editJiraIssue` |
| Get transitions | `mcp__atlassian__getTransitionsForJiraIssue` |
| Transition | `mcp__atlassian__transitionJiraIssue` |
| Add comment | `mcp__atlassian__addCommentToJiraIssue` |
| User lookup | `mcp__atlassian__lookupJiraAccountId` |
| List projects | `mcp__atlassian__getVisibleJiraProjects` |

See `references/mcp.md` for full MCP patterns.

---

## Triggers

- "create a jira ticket"
- "show me PROJ-123"
- "list my tickets"
- "move ticket to done"
- "what's in the current sprint"

---

## Issue Key Detection

Issue keys follow the pattern: `[A-Z]+-[0-9]+` (e.g., PROJ-123, ABC-1).

When a user mentions an issue key in conversation:
- **CLI:** `jira issue view KEY` or `jira open KEY`
- **MCP:** `mcp__atlassian__jira_get_issue` with the key

---

## Workflow

**Creating tickets:**
1. Research context if user references code/tickets/PRs
2. Draft ticket content
3. Review with user
4. Create using appropriate backend

**Updating tickets:**
1. Fetch issue details first
2. Check status (careful with in-progress tickets)
3. Show current vs proposed changes
4. Get approval before updating
5. Add comment explaining changes

---

## Before Any Operation

Ask yourself:

1. **What's the current state?** — Always fetch the issue first. Don't assume status, assignee, or fields are what user thinks they are.

2. **Who else is affected?** — Check watchers, linked issues, parent epics. A "simple edit" might notify 10 people.

3. **Is this reversible?** — Transitions may have one-way gates. Some workflows require intermediate states. Description edits have no undo.

4. **Do I have the right identifiers?** — Issue keys, transition IDs, account IDs. Display names don't work for assignment (MCP).

---

## NEVER

- **NEVER transition without fetching current status** — Workflows may require intermediate states. "To Do" → "Done" might fail silently if "In Progress" is required first.

- **NEVER assign using display name (MCP)** — Only account IDs work. Always call `lookupJiraAccountId` first, or assignment silently fails.

- **NEVER edit description without showing original** — Jira has no undo. User must see what they're replacing.

- **NEVER use `--no-input` without all required fields (CLI)** — Fails silently with cryptic errors. Check project's required fields first.

- **NEVER assume transition names are universal** — "Done", "Closed", "Complete" vary by project. Always get available transitions first.

- **NEVER bulk-modify without explicit approval** — Each ticket change notifies watchers. 10 edits = 10 notification storms.

---

## Safety

- Always show the command/tool call before running it
- Always get approval before modifying tickets
- Preserve original information when editing
- Verify updates after applying
- Always surface authentication issues clearly so the user can resolve them

---

## No Backend Available

If neither CLI nor MCP is available, guide the user:

```
To use Jira, you need one of:

1. **jira CLI** (recommended):
   https://github.com/ankitpokhrel/jira-cli

   Install: brew install ankitpokhrel/jira-cli/jira-cli
   Setup:   jira init

2. **Atlassian MCP**:
   Configure in your MCP settings with Atlassian credentials.
```

---

## Deep Dive

**LOAD reference when:**
- Creating issues with complex fields or multi-line content
- Building JQL queries beyond simple filters
- Troubleshooting errors or authentication issues
- Working with transitions, linking, or sprints

**Do NOT load reference for:**
- Simple view/list operations (Quick Reference above is sufficient)
- Basic status checks (`jira issue view KEY`)
- Opening issues in browser

| Task | Load Reference? |
|------|-----------------|
| View single issue | No |
| List my tickets | No |
| Create with description | **Yes** — CLI needs `/tmp` pattern |
| Transition issue | **Yes** — need transition ID workflow |
| JQL search | **Yes** — for complex queries |
| Link issues | **Yes** — MCP limitation, need script |

References:
- CLI patterns: `references/commands.md`
- MCP patterns: `references/mcp.md`


---


## 📚 Reference Materials


### Commands

# Commands Reference

Complete reference for the `jira` CLI.

---

## Viewing Issues

```bash
# View single issue
jira issue view ISSUE-KEY

# View with more comments
jira issue view ISSUE-KEY --comments 5

# Get raw JSON
jira issue view ISSUE-KEY --raw
```

---

## Listing Issues

```bash
# List all issues in project
jira issue list

# List my issues
jira issue list -a$(jira me)

# Filter by status (use quotes for multi-word statuses)
jira issue list -s"In Progress"
jira issue list -s"To Do"
jira issue list -sDone

# Filter by type
jira issue list -tBug
jira issue list -tStory
jira issue list -tTask
jira issue list -tEpic

# Filter by priority
jira issue list -yHigh
jira issue list -yCritical

# Filter by label
jira issue list -lurgent -lbug

# Combine filters
jira issue list -a$(jira me) -s"In Progress" -yHigh

# Search with text
jira issue list "login error"

# Recently accessed
jira issue list --history

# Issues I'm watching
jira issue list -w

# Created/updated filters
jira issue list --created today
jira issue list --created week
jira issue list --updated -2d

# Plain output for scripting
jira issue list --plain --no-headers

# Specific columns
jira issue list --plain --columns key,summary,status,assignee

# Raw JQL query
jira issue list -q"status = 'In Progress' AND assignee = currentUser()"

# Paginate results
jira issue list --paginate 20
jira issue list --paginate 10:50 # start:limit
```

---

## Creating Issues

```bash
# Interactive creation
jira issue create

# Non-interactive with all fields
jira issue create \
    -tBug \
    -s"Login button not working" \
    -b"Users cannot click the login button on Safari" \
    -yHigh \
    -lbug -lurgent

# Create and assign to self
jira issue create -tTask -s"Summary" -a$(jira me)

# Create subtask (requires parent)
jira issue create -tSub-task -P"PROJ-123" -s"Subtask summary"

# Create with custom fields
jira issue create -tStory -s"Summary" --custom story-points=3

# Skip prompts for optional fields
jira issue create -tTask -s"Quick task" --no-input

# Open in browser after creation
jira issue create -tBug -s"Bug title" --web

# Read description from file
jira issue create -tStory -s"Summary" --template /path/to/template.md

# Read description from stdin
echo "Description here" | jira issue create -tTask -s"Summary"
```

**Multi-line content:** The CLI chokes on multi-line strings. Write to `/tmp` first:

```bash
cat > /tmp/jira_body.md <<'EOF'
## Description
User needs ability to export data...

## Acceptance Criteria
- Export works for CSV
- Export works for JSON
EOF

jira issue create --no-input \
  -tStory \
  -pPROJ \
  -s"Add export functionality" \
  -b"$(cat /tmp/jira_body.md)"
```

---

## Transitioning Issues

```bash
# Move to a state
jira issue move ISSUE-KEY "In Progress"
jira issue move ISSUE-KEY "Done"
jira issue move ISSUE-KEY "To Do"

# Move with comment
jira issue move ISSUE-KEY "Done" --comment "Completed the implementation"

# Move and set resolution
jira issue move ISSUE-KEY "Done" -R"Fixed"

# Move and reassign
jira issue move ISSUE-KEY "In Review" -a"reviewer@example.com"

# Open in browser after transition
jira issue move ISSUE-KEY "Done" --web
```

---

## Assigning Issues

```bash
# Assign to specific user
jira issue assign ISSUE-KEY "user@example.com"
jira issue assign ISSUE-KEY "John Doe"

# Assign to self
jira issue assign ISSUE-KEY $(jira me)

# Assign to default assignee
jira issue assign ISSUE-KEY default

# Unassign
jira issue assign ISSUE-KEY x
```

---

## Comments

```bash
# Add comment
jira issue comment add ISSUE-KEY -b"This is my comment"

# Add comment from file
jira issue comment add ISSUE-KEY --template /path/to/comment.md
```

---

## Sprints

```bash
# List sprints
jira sprint list

# Active sprint only
jira sprint list --state active

# Add issue to sprint
jira sprint add SPRINT-ID ISSUE-KEY

# Close sprint
jira sprint close SPRINT-ID
```

---

## Linking Issues

| Relationship | Meaning |
|--------------|---------|
| `Blocks` | First ticket blocks second |
| `Relates` | General relationship |
| `Duplicate` | Same work |
| `Epic-Story` | Story belongs to Epic |

```bash
# Basic link
jira issue link PROJ-123 PROJ-456 "Relates"

# Blocker (blocker comes first)
jira issue link PROJ-100 PROJ-200 "Blocks"
# Meaning: PROJ-100 blocks PROJ-200

# Link to epic
jira issue link PROJ-EPIC PROJ-STORY "Epic-Story"
```

---

## Other Commands

```bash
# Open issue in browser
jira open ISSUE-KEY

# Show current user
jira me

# Server info
jira serverinfo

# List projects
jira project list

# List boards
jira board list
```




### Mcp

# MCP Reference

Complete reference for Atlassian Jira operations via MCP.

## MCP Tool Reference

### Search Operations

#### `mcp__atlassian__searchJiraIssuesUsingJql`
Search Jira using JQL (Jira Query Language).

**Parameters:**
- `jql` (required): JQL query string
- `maxResults`: Maximum results (default: 50)
- `startAt`: Pagination offset
- `fields`: Comma-separated fields to return

**Example:**
```
mcp__atlassian__searchJiraIssuesUsingJql(jql: "project = PROJ AND status = 'In Progress'")
```

### Issue Operations

#### `mcp__atlassian__getJiraIssue`
Retrieve full issue details by key.

**Parameters:**
- `issueKey` (required): Issue key (e.g., "PROJ-123")
- `expand`: Additional data (changelog, transitions, renderedFields)

**Example:**
```
mcp__atlassian__getJiraIssue(issueKey: "PROJ-123")
```

#### `mcp__atlassian__createJiraIssue`
Create a new issue.

**Parameters:**
- `projectKey` (required): Target project
- `issueType` (required): Issue type (Story, Bug, Task, Epic, etc.)
- `summary` (required): Issue title
- `description`: Detailed description
- `assignee`: Account ID (use lookupJiraAccountId first)
- `priority`: Priority name (Highest, High, Medium, Low, Lowest)
- `labels`: Array of labels
- `components`: Array of component names
- Custom fields as needed

**Example:**
```
mcp__atlassian__createJiraIssue(
  projectKey: "PROJ",
  issueType: "Story",
  summary: "Implement user authentication",
  description: "Add OAuth2 authentication flow...",
  labels: ["backend", "security"]
)
```

#### `mcp__atlassian__editJiraIssue`
Update an existing issue.

**Parameters:**
- `issueKey` (required): Issue to update
- Any field to update (summary, description, assignee, etc.)

**Example:**
```
mcp__atlassian__editJiraIssue(
  issueKey: "PROJ-123",
  description: "Updated description with more details..."
)
```

### Transition Operations

#### `mcp__atlassian__getTransitionsForJiraIssue`
Get available status transitions for an issue.

**Parameters:**
- `issueKey` (required): Issue key

**Returns:** List of available transitions with IDs and names.

#### `mcp__atlassian__transitionJiraIssue`
Change issue status.

**Parameters:**
- `issueKey` (required): Issue key
- `transitionId` (required): Transition ID from getTransitions
- `comment`: Optional comment for the transition

**Workflow:**
1. Get transitions: `getTransitionsForJiraIssue("PROJ-123")`
2. Find desired transition ID from results
3. Execute: `transitionJiraIssue(issueKey: "PROJ-123", transitionId: "31")`

### Comment Operations

#### `mcp__atlassian__addCommentToJiraIssue`
Add a comment to an issue.

**Parameters:**
- `issueKey` (required): Issue key
- `body` (required): Comment text (supports Jira markdown)

### User Operations

#### `mcp__atlassian__lookupJiraAccountId`
Find user account ID for assignments.

**Parameters:**
- `query` (required): Search by display name, email, or username

**Example:**
```
mcp__atlassian__lookupJiraAccountId(query: "user@example.com")
```

**Usage:** Always look up account IDs before assigning issues.

### Project Operations

#### `mcp__atlassian__getVisibleJiraProjects`
List available Jira projects.

**Parameters:**
- `maxResults`: Maximum results

#### `mcp__atlassian__getJiraProjectIssueTypesMetadata`
Get issue types and required fields for a project.

**Parameters:**
- `projectKey` (required): Project key

**Usage:** Call before creating issues to understand required fields.

#### `mcp__atlassian__getJiraIssueTypeMetaWithFields`
Get detailed field metadata for an issue type.

**Parameters:**
- `projectKey` (required): Project key
- `issueTypeId` (required): Issue type ID

---

## JQL (Jira Query Language) Reference

### Basic Syntax

```
field operator value [AND|OR field operator value]
```

### Common Fields

| Field | Description | Example |
|-------|-------------|---------|
| `project` | Project key | `project = "PROJ"` |
| `issuetype` | Issue type | `issuetype = Bug` |
| `status` | Issue status | `status = "In Progress"` |
| `assignee` | Assigned user | `assignee = currentUser()` |
| `reporter` | Issue creator | `reporter = "jobarksdale"` |
| `priority` | Priority level | `priority = High` |
| `labels` | Issue labels | `labels = "backend"` |
| `component` | Components | `component = "API"` |
| `created` | Creation date | `created >= -30d` |
| `updated` | Last update | `updated >= -7d` |
| `resolved` | Resolution date | `resolved >= startOfMonth()` |
| `sprint` | Sprint name/ID | `sprint in openSprints()` |
| `epic` | Parent epic | `"Epic Link" = PROJ-100` |
| `parent` | Parent issue | `parent = PROJ-50` |
| `text` | Full-text search | `text ~ "authentication"` |
| `summary` | Title search | `summary ~ "login"` |
| `description` | Description search | `description ~ "OAuth"` |

### Operators

| Operator | Meaning | Example |
|----------|---------|---------|
| `=` | Exact match | `status = Done` |
| `!=` | Not equal | `status != Closed` |
| `~` | Contains (text) | `summary ~ "auth*"` |
| `!~` | Does not contain | `summary !~ "test"` |
| `>` `>=` `<` `<=` | Comparisons | `priority >= High` |
| `IN` | Multiple values | `status IN (Open, "In Progress")` |
| `NOT IN` | Exclude values | `status NOT IN (Done, Closed)` |
| `IS` | Null check | `assignee IS EMPTY` |
| `IS NOT` | Not null | `assignee IS NOT EMPTY` |
| `WAS` | Historical value | `status WAS "In Progress"` |
| `CHANGED` | Field changed | `status CHANGED` |

### Functions

| Function | Description | Example |
|----------|-------------|---------|
| `currentUser()` | Logged-in user | `assignee = currentUser()` |
| `now()` | Current timestamp | `created <= now()` |
| `startOfDay()` | Midnight today | `updated >= startOfDay()` |
| `startOfWeek()` | Start of week | `created >= startOfWeek()` |
| `startOfMonth()` | Start of month | `created >= startOfMonth()` |
| `endOfDay()` | End of today | `due <= endOfDay()` |
| `openSprints()` | Active sprints | `sprint in openSprints()` |
| `closedSprints()` | Completed sprints | `sprint in closedSprints()` |
| `linkedIssues()` | Linked issues | `issue in linkedIssues("PROJ-123")` |

### Relative Dates

```jql
# Days
created >= -7d    # Last 7 days
updated >= -30d   # Last 30 days

# Weeks
created >= -2w    # Last 2 weeks

# Months
created >= -1M    # Last month

# Specific date
created >= "2024-01-01"
```

### Ordering

```jql
# Order by priority, highest first
project = PROJ ORDER BY priority DESC

# Multiple sort fields
project = PROJ ORDER BY status ASC, created DESC
```

### Complex Query Examples

```jql
# My open issues, high priority
assignee = currentUser() AND status NOT IN (Done, Closed) AND priority >= High

# Bugs created this week
issuetype = Bug AND created >= startOfWeek() ORDER BY priority DESC

# Epics in progress with stories
issuetype = Epic AND status = "In Progress" AND issueFunction in hasLinks("is parent of")

# Issues updated by me recently
updatedBy = currentUser() AND updated >= -7d ORDER BY updated DESC

# Blocked issues
status = Blocked OR "Flagged" = "Impediment"

# Issues linked to specific epic
"Epic Link" = PROJ-100 AND status != Done

# Sprint backlog items
sprint in openSprints() AND status = "To Do" ORDER BY rank ASC

# Unassigned high-priority bugs
issuetype = Bug AND assignee IS EMPTY AND priority >= High

# Issues I'm watching
watcher = currentUser()

# Recently resolved by team
resolved >= -7d AND project = PROJ ORDER BY resolved DESC
```

---

## Issue Linking

### Limitation
The Atlassian MCP does not currently support creating issue links. Use the bundled `jira-link-issues` script instead.

### Link Types

| Link Type | Inward | Outward | Use Case |
|-----------|--------|---------|----------|
| Depends On | is dependency of | depends on | Task dependencies |
| Blocks | is blocked by | blocks | Blocking relationships |
| Relates To | relates to | relates to | General relationships |
| Clones | is cloned by | clones | Cloned issues |
| Duplicates | is duplicated by | duplicates | Duplicate issues |

### Script Usage

```bash
# Link PROJ-123 depends on PROJ-456
~/.claude/skills/jira/jira-link-issues PROJ-123 PROJ-456 "Depends On"

# PROJ-100 blocks PROJ-200
~/.claude/skills/jira/jira-link-issues PROJ-100 PROJ-200 "Blocks"

# General relationship
~/.claude/skills/jira/jira-link-issues PROJ-50 PROJ-75 "Relates To"
```

### Finding Link Types
Query the Jira API to get available link types:
```bash
curl -s -u "$JIRA_USER:$JIRA_API_TOKEN" \
  "$JIRA_BASE_URL/rest/api/3/issueLinkType" | jq '.issueLinkTypes[].name'
```

Required environment variables:
- `JIRA_BASE_URL`: Your Atlassian instance (e.g., `https://yourcompany.atlassian.net`)
- `JIRA_USER`: Your email address
- `JIRA_API_TOKEN`: API token from Atlassian account settings

---

## Description Formatting

### Jira Markup (Wiki Style)

```
h1. Heading 1
h2. Heading 2
h3. Heading 3

*bold text*
_italic text_
-strikethrough-
+underline+

{code:java}
public class Example {
    // code here
}
{code}

{quote}
Quoted text here
{quote}

* Bullet list
** Nested bullet
# Numbered list
## Nested number

[Link text|https://example.com]
[Issue link|PROJ-123]

||Header 1||Header 2||
|Cell 1|Cell 2|

{panel:title=Panel Title}
Panel content here
{panel}
```

### Atlassian Document Format (ADF)

For createJiraIssue, descriptions may use ADF format:
```json
{
  "type": "doc",
  "version": 1,
  "content": [
    {
      "type": "paragraph",
      "content": [
        {"type": "text", "text": "Description text"}
      ]
    }
  ]
}
```

---

## Error Handling

### Common Errors

| HTTP Code | Error | Cause | Resolution |
|-----------|-------|-------|------------|
| 400 | Bad Request | Invalid field values | Check required fields for issue type |
| 401 | Unauthorized | Invalid credentials | Run `/mcp` to reconnect |
| 403 | Forbidden | Insufficient permissions | Check project permissions |
| 404 | Not Found | Issue/project doesn't exist | Verify key is correct |
| 422 | Unprocessable | Validation failed | Check field constraints |

### Authentication Issues

If MCP tools return authentication errors:
1. Run `/mcp` to check connection status
2. Reconnect the Atlassian MCP service if disconnected
3. Verify API token permissions in Atlassian settings

### Field Validation

Before creating issues:
1. Get project metadata: `getJiraProjectIssueTypesMetadata`
2. Check required fields for the issue type
3. Look up user account IDs for assignee fields
4. Verify custom field IDs and valid values

---

## Common Workflows

### Move Ticket to Done

```
1. Get available transitions:
   mcp__atlassian__getTransitionsForJiraIssue(issueKey: "PROJ-123")
   → Returns list with transition IDs

2. Find "Done" transition ID from response

3. Execute transition:
   mcp__atlassian__transitionJiraIssue(
     issueKey: "PROJ-123",
     transitionId: "done_id"
   )

4. Add comment:
   mcp__atlassian__addCommentToJiraIssue(
     issueKey: "PROJ-123",
     body: "Completed and deployed"
   )
```

### Create and Assign Issue

```
1. Look up user account ID:
   mcp__atlassian__lookupJiraAccountId(query: "john@example.com")
   → Returns account ID

2. Create issue with assignment:
   mcp__atlassian__createJiraIssue(
     projectKey: "PROJ",
     issueType: "Task",
     summary: "Implement feature X",
     description: "Details here...",
     assignee: "account_id_from_step_1"
   )
```

### List My In-Progress Issues

```
mcp__atlassian__searchJiraIssuesUsingJql(
  jql: "assignee = currentUser() AND status = 'In Progress' ORDER BY updated DESC"
)
```

### Get Project Info Before Creating

```
1. List available projects:
   mcp__atlassian__getVisibleJiraProjects()

2. Get issue types for project:
   mcp__atlassian__getJiraProjectIssueTypesMetadata(projectKey: "PROJ")

3. Create issue with correct type:
   mcp__atlassian__createJiraIssue(
     projectKey: "PROJ",
     issueType: "Story",
     summary: "...",
     description: "..."
   )
```




---

## 🚀 Usage

**Reference this template:** `@skill-jira.md`


**Platform-specific:**
- **GitHub Copilot**: Add to `.github/copilot-instructions.md`
- **Augment Code**: Use `aug context add` command
- **Cursor/Windsurf**: Reference in chat with `@filename`
- **Claude**: Add to Project Knowledge
- **ChatGPT**: Add to Custom GPT configuration
