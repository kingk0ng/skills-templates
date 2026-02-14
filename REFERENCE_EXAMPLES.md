# Template Reference Examples

## How to Use These Templates

All templates are now single markdown files with descriptive names that can be referenced using `@` syntax in modern AI assistants.

### File Naming Convention

- **Agents**: `agent-{name}.md`
- **Commands**: `command-{name}.md`  
- **Skills**: `skill-{name}.md`
- **Integrations**: `integration-{name}.md`

### @ Reference Syntax

In AI assistants like Cursor, Windsurf, GitHub Copilot Chat, etc:

```
@agent-mcp-developer.md help me build an MCP server
@skill-pytest-advanced.md write comprehensive unit tests
@command-generate-tests.md automate test generation for my API
@integration-github.md setup GitHub integration
```

### Quick Examples

**Architecture Design:**
```
@agent-senior-architect.md design a microservices architecture for an e-commerce platform
```

**Testing:**
```
@skill-pytest-advanced.md create integration tests with proper fixtures and mocking
```

**Git Workflow:**
```
@agent-git-workflow-manager.md setup a branching strategy for our team
```

**Security:**
```
@agent-security-auditor.md review this authentication implementation
```

### Finding Templates

Use the search command:
```bash
python converter/convert.py search "testing"
python converter/convert.py search "git" --type agent
python converter/convert.py search "" --tag security
```

Or browse indexes:
```bash
cat converted-templates/metadata/index.json
cat converted-templates/metadata/categories.json
cat converted-templates/metadata/tags.json
```

### Template Statistics

$(cat converted-templates/metadata/stats.json | python3 -m json.tool | head -20)

---

**Total Templates Converted:** 1,371
**Success Rate:** 93%
**Average Tokens:** ~1,199 per template
