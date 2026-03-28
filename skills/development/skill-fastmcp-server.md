---
id: skill-fastmcp-server
type: skill
name: fastmcp-server
description: Complete guide for building MCP servers with FastMCP 3.0 - tools, resources,
  authentication, providers, middleware, and deployment. Use when creating Python
  MCP servers or integrating AI models with external tools and data.
category: development
complexity: medium
keywords:
- api
- python
- security
capabilities: []
token_estimate: 1105
has_references: true
reference_count: 1
---

<!-- Converted from Claude Skill Template -->
<!-- Complexity: Medium -->
<!-- Estimated Tokens: ~1,105 -->
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




# fastmcp-server

> Complete guide for building MCP servers with FastMCP 3.0 - tools, resources, authentication, providers, middleware, and deployment. Use when creating Python MCP servers or integrating AI models with external tools and data.

# FastMCP 3.0 Server Development

Complete reference for building production-ready MCP (Model Context Protocol) servers with FastMCP 3.0 - the fast, Pythonic framework for connecting LLMs to tools and data.

## When to use this skill

**Use FastMCP Server when:**
- Creating a new MCP server in Python
- Adding tools, resources, or prompts to an MCP server
- Implementing authentication (OAuth, OIDC, token verification)
- Setting up middleware for logging, rate limiting, or authorization
- Configuring providers (local, filesystem, skills, custom)
- Building production MCP servers with telemetry and storage
- Upgrading from FastMCP 2.x to 3.0

**Key areas covered:**
- **Tools & Resources** (CORE): Decorators, validation, return types, templates
- **Context & DI** (CORE): MCP context, dependency injection, background tasks
- **Authentication** (SECURITY): OAuth, OIDC, token verification, proxy patterns
- **Authorization** (SECURITY): Scope-based and role-based access control
- **Middleware** (ADVANCED): Request/response pipeline, built-in middleware
- **Providers** (ADVANCED): Local, filesystem, skills, and custom providers
- **Features** (ADVANCED): Pagination, sampling, storage, OpenTelemetry, versioning

## Quick reference

### Core patterns

**Create a server with tools:**
```python
from fastmcp import FastMCP

mcp = FastMCP("MyServer")

@mcp.tool
def add(a: int, b: int) -> int:
    """Add two numbers"""
    return a + b
```

**Create a resource:**
```python
@mcp.resource("data://config")
def get_config() -> dict:
    """Return server configuration"""
    return {"version": "1.0", "debug": False}
```

**Create a resource template:**
```python
@mcp.resource("users://{user_id}/profile")
def get_user_profile(user_id: str) -> dict:
    """Get a user's profile by ID"""
    return fetch_user(user_id)
```

**Create a prompt:**
```python
@mcp.prompt
def review_code(code: str, language: str = "python") -> str:
    """Review code for best practices"""
    return f"Review this {language} code:\n\n{code}"
```

**Run the server:**
```python
if __name__ == "__main__":
    mcp.run()

# Or with transport options:
# mcp.run(transport="sse", host="0.0.0.0", port=8000)
```

### Using context in tools

```python
from fastmcp import FastMCP, Context

mcp = FastMCP("MyServer")

@mcp.tool
def process_data(uri: str, ctx: Context) -> str:
    """Process data with logging and progress"""
    ctx.info(f"Processing {uri}")
    ctx.report_progress(0, 100)
    data = ctx.read_resource(uri)
    ctx.report_progress(100, 100)
    return f"Processed: {data}"
```

### Authentication setup

```python
from fastmcp import FastMCP
from fastmcp.server.auth import BearerAuthProvider

auth = BearerAuthProvider(
    jwks_uri="https://your-provider/.well-known/jwks.json",
    audience="your-api",
    issuer="https://your-provider/"
)

mcp = FastMCP("SecureServer", auth=auth)
```

## Key concepts

### Tools
Functions exposed as executable capabilities for LLMs. Decorated with `@mcp.tool`. Support Pydantic validation, async, custom return types, and annotations (readOnlyHint, destructiveHint).

### Resources & Templates
Static or dynamic data sources identified by URIs. Resources use fixed URIs (`data://config`), templates use parameterized URIs (`users://{id}/profile`). Support MIME types, annotations, and wildcard parameters.

### Context
The `Context` object provides access to MCP features within tools/resources: logging, progress reporting, resource access, LLM sampling, user elicitation, and session state.

### Dependency Injection
Inject values into tool/resource functions using `Depends()`. Supports HTTP requests, access tokens, custom dependencies, and generator-based cleanup patterns.

### Providers
Control where components come from. `LocalProvider` (default, decorator-based), `FileSystemProvider` (load from Python files on disk), `SkillsProvider` (packaged bundles), or custom providers.

### Authentication & Authorization
Multiple auth patterns: token verification (JWT, JWKS), OAuth proxy, OIDC proxy, remote OAuth, and full OAuth server. Authorization via scopes on components and middleware.

### Middleware
Intercept and modify requests/responses. Built-in middleware for rate limiting, error handling, logging, and response size limits. Custom middleware via `@mcp.middleware`.

## Using the references

Detailed documentation is organized in the `references/` folder:

### Getting Started
- **getting-started/installation.md** - Install FastMCP, optional dependencies, verify setup
- **getting-started/upgrade-guide.md** - Migrate from FastMCP 2.x to 3.0
- **getting-started/quickstart.md** - First server, tools, resources, prompts, running

### Server
- **server/server-class.md** - FastMCP server configuration, transport options, tag filtering
- **server/tools.md** - Tool decorator, parameters, validation, return types, annotations
- **server/resources-and-templates.md** - Resources, templates, URIs, wildcards, MIME types

### Context
- **context/mcp-context.md** - Context object, logging, progress, resource access, sampling
- **context/background-tasks.md** - Long-running operations with task support
- **context/dependency-injection.md** - Depends(), custom deps, HTTP request, access tokens
- **context/user-elicitation.md** - Request structured input from users during execution

### Features
- **features/icons.md** - Custom icons for tools, resources, prompts, and servers
- **features/lifespans.md** - Server lifecycle management and startup/shutdown hooks
- **features/client-logging.md** - Send log messages to MCP clients
- **features/middleware.md** - Request/response pipeline, built-in and custom middleware
- **features/pagination.md** - Paginate large component lists
- **features/progress-reporting.md** - Report progress for long-running operations
- **features/sampling.md** - Request LLM completions from the client
- **features/storage-backends.md** - Memory, file, and Redis storage for caching and tokens
- **features/opentelemetry.md** - Distributed tracing and observability
- **features/versioning.md** - Version components and filter by version ranges

### Authentication
- **authentication/token-verification.md** - JWT, JWKS, introspection, static keys, custom
- **authentication/remote-oauth.md** - Delegate auth to upstream OAuth provider
- **authentication/oauth-proxy.md** - Full OAuth proxy with PKCE, client management
- **authentication/oidc-proxy.md** - OpenID Connect proxy with auto-discovery
- **authentication/full-oauth-server.md** - Complete built-in OAuth server

### Authorization
- **authorization.md** - Scope-based access control, middleware authorization, patterns

### Providers
- **providers/local.md** - Default provider, decorator-based component registration
- **providers/filesystem.md** - Load components from Python files on disk
- **providers/skills.md** - Package and distribute component bundles
- **providers/custom.md** - Build custom providers for any component source

## Version history

**v1.0.0** (February 2026)
- Initial release covering FastMCP 3.0 (release candidate)
- 30 reference files across 7 categories
- Complete coverage of tools, resources, context, auth, providers, and features


---


## 📚 Reference Materials


### Authorization

# Authorization

> Control access to components using callable-based authorization checks that filter visibility and enforce permissions.

Authorization controls what authenticated users can do with your FastMCP server. While authentication verifies identity (who you are), authorization determines access (what you can do). FastMCP provides a callable-based authorization system that works at both the component level and globally via middleware.

The authorization model centers on a simple concept: callable functions that receive context about the current request and return `True` to allow access or `False` to deny it. Multiple checks combine with AND logic, meaning all checks must pass for access to be granted.

> **Note:** Authorization relies on OAuth tokens which are only available with HTTP transports (SSE, Streamable HTTP). In STDIO mode, there's no OAuth mechanism, so `get_access_token()` returns `None` and all auth checks are skipped.


> **Note:** When an `AuthProvider` is configured, all requests to the MCP endpoint must carry a valid token—unauthenticated requests are rejected at the transport level before any auth checks run. Authorization checks therefore differentiate between authenticated users based on their scopes and claims, not between authenticated and unauthenticated users.


## Auth Checks

An auth check is any callable that accepts an `AuthContext` and returns a boolean. Auth checks can be synchronous or asynchronous, so checks that need to perform async operations (like reading server state or calling external services) work naturally.

```python
from fastmcp.server.auth import AuthContext

def my_custom_check(ctx: AuthContext) -> bool:
    # ctx.token is AccessToken | None
    # ctx.component is the Tool, Resource, or Prompt being accessed
    return ctx.token is not None and "special" in ctx.token.scopes
```

FastMCP provides two built-in auth checks that cover common authorization patterns.

### require\_scopes

Scope-based authorization checks that the token contains all specified OAuth scopes. When multiple scopes are provided, all must be present (AND logic).

```python
from fastmcp import FastMCP
from fastmcp.server.auth import require_scopes

mcp = FastMCP("Scoped Server")

@mcp.tool(auth=require_scopes("admin"))
def admin_operation() -> str:
    """Requires the 'admin' scope."""
    return "Admin action completed"

@mcp.tool(auth=require_scopes("read", "write"))
def read_write_operation() -> str:
    """Requires both 'read' AND 'write' scopes."""
    return "Read/write action completed"
```

### restrict\_tag

Tag-based restrictions apply scope requirements conditionally. If a component has the specified tag, the token must have the required scopes. Components without the tag are unaffected.

```python
from fastmcp import FastMCP
from fastmcp.server.auth import restrict_tag
from fastmcp.server.middleware import AuthMiddleware

mcp = FastMCP(
    "Tagged Server",
    middleware=[
        AuthMiddleware(auth=restrict_tag("admin", scopes=["admin"]))
    ]
)

@mcp.tool(tags={"admin"})
def admin_tool() -> str:
    """Tagged 'admin', so requires 'admin' scope."""
    return "Admin only"

@mcp.tool(tags={"public"})
def public_tool() -> str:
    """Not tagged 'admin', so no scope required by the restriction."""
    return "Anyone can access"
```

### Combining Checks

Multiple auth checks can be combined by passing a list. All checks must pass for authorization to succeed (AND logic).

```python
from fastmcp import FastMCP
from fastmcp.server.auth import require_scopes

mcp = FastMCP("Combined Auth Server")

@mcp.tool(auth=[require_scopes("admin"), require_scopes("write")])
def secure_admin_action() -> str:
    """Requires both 'admin' AND 'write' scopes."""
    return "Secure admin action"
```

### Custom Auth Checks

Any callable that accepts `AuthContext` and returns `bool` can serve as an auth check. This enables authorization logic based on token claims, component metadata, or external systems.

```python
from fastmcp import FastMCP
from fastmcp.server.auth import AuthContext

mcp = FastMCP("Custom Auth Server")

def require_premium_user(ctx: AuthContext) -> bool:
    """Check for premium user status in token claims."""
    if ctx.token is None:
        return False
    return ctx.token.claims.get("premium", False) is True

def require_access_level(minimum_level: int):
    """Factory function for level-based authorization."""
    def check(ctx: AuthContext) -> bool:
        if ctx.token is None:
            return False
        user_level = ctx.token.claims.get("level", 0)
        return user_level >= minimum_level
    return check

@mcp.tool(auth=require_premium_user)
def premium_feature() -> str:
    """Only for premium users."""
    return "Premium content"

@mcp.tool(auth=require_access_level(5))
def advanced_feature() -> str:
    """Requires access level 5 or higher."""
    return "Advanced feature"
```

### Async Auth Checks

Auth checks can be `async` functions, which is useful when the authorization decision depends on asynchronous operations like reading server state or querying external services.

```python
from fastmcp import FastMCP
from fastmcp.server.auth import AuthContext

mcp = FastMCP("Async Auth Server")

async def check_user_permissions(ctx: AuthContext) -> bool:
    """Async auth check that reads server state."""
    if ctx.token is None:
        return False
    user_id = ctx.token.claims.get("sub")
    # Async operations work naturally in auth checks
    permissions = await fetch_user_permissions(user_id)
    return "admin" in permissions

@mcp.tool(auth=check_user_permissions)
def admin_tool() -> str:
    return "Admin action completed"
```

Sync and async checks can be freely combined in a list — each check is handled according to its type.

### Error Handling

Auth checks can raise exceptions for explicit denial with custom messages:

* **`AuthorizationError`**: Propagates with its custom message, useful for explaining why access was denied
* **Other exceptions**: Masked for security (logged internally, treated as denial)

```python
from fastmcp.server.auth import AuthContext
from fastmcp.exceptions import AuthorizationError

def require_verified_email(ctx: AuthContext) -> bool:
    """Require verified email with explicit denial message."""
    if ctx.token is None:
        raise AuthorizationError("Authentication required")
    if not ctx.token.claims.get("email_verified"):
        raise AuthorizationError("Email verification required")
    return True
```

## Component-Level Authorization

The `auth` parameter on decorators controls visibility and access for individual components. When auth checks fail for the current request, the component is hidden from list responses and direct access returns not-found.

```python
from fastmcp import FastMCP
from fastmcp.server.auth import require_scopes

mcp = FastMCP("Component Auth Server")

@mcp.tool(auth=require_scopes("write"))
def write_tool() -> str:
    """Only visible to users with 'write' scope."""
    return "Written"

@mcp.resource("secret://data", auth=require_scopes("read"))
def secret_resource() -> str:
    """Only visible to users with 'read' scope."""
    return "Secret data"

@mcp.prompt(auth=require_scopes("admin"))
def admin_prompt() -> str:
    """Only visible to users with 'admin' scope."""
    return "Admin prompt content"
```

> **Note:** Component-level `auth` controls both visibility (list filtering) and access (direct lookups return not-found for unauthorized requests). Additionally use `AuthMiddleware` to apply server-wide authorization rules and get explicit `AuthorizationError` responses on unauthorized execution attempts.


## Server-Level Authorization

For server-wide authorization enforcement, use `AuthMiddleware`. This middleware applies auth checks globally to all components—filtering list responses and blocking unauthorized execution with explicit `AuthorizationError` responses.

```python
from fastmcp import FastMCP
from fastmcp.server.auth import require_scopes
from fastmcp.server.middleware import AuthMiddleware

mcp = FastMCP(
    "Enforced Auth Server",
    middleware=[AuthMiddleware(auth=require_scopes("api"))]
)

@mcp.tool
def any_tool() -> str:
    """Requires 'api' scope to see AND call."""
    return "Protected"
```

### Component Auth + Middleware

Component-level `auth` and `AuthMiddleware` work together as complementary layers. The middleware applies server-wide rules to all components, while component-level auth adds per-component requirements. Both layers are checked—all checks must pass.

```python
from fastmcp import FastMCP
from fastmcp.server.auth import require_scopes, restrict_tag
from fastmcp.server.middleware import AuthMiddleware

mcp = FastMCP(
    "Layered Auth Server",
    middleware=[
        AuthMiddleware(auth=restrict_tag("admin", scopes=["admin"]))
    ]
)

# Requires "write" scope (component-level)
# Also requires "admin" scope if tagged "admin" (middleware-level)
@mcp.tool(auth=require_scopes("write"), tags={"admin"})
def admin_write() -> str:
    """Requires both 'write' AND 'admin' scopes."""
    return "Admin write"

# Requires "write" scope (component-level only)
@mcp.tool(auth=require_scopes("write"))
def user_write() -> str:
    """Requires 'write' scope."""
    return "User write"
```

### Tag-Based Global Authorization

A common pattern uses `restrict_tag` with `AuthMiddleware` to apply scope requirements based on component tags.

```python
from fastmcp import FastMCP
from fastmcp.server.auth import restrict_tag
from fastmcp.server.middleware import AuthMiddleware

mcp = FastMCP(
    "Tag-Based Auth Server",
    middleware=[
        AuthMiddleware(auth=restrict_tag("admin", scopes=["admin"])),
        AuthMiddleware(auth=restrict_tag("write", scopes=["write"])),
    ]
)

@mcp.tool(tags={"admin"})
def delete_all_data() -> str:
    """Requires 'admin' scope."""
    return "Deleted"

@mcp.tool(tags={"write"})
def update_record(id: str, data: str) -> str:
    """Requires 'write' scope."""
    return f"Updated {id}"

@mcp.tool
def read_record(id: str) -> str:
    """No tag restrictions, accessible to all."""
    return f"Record {id}"
```

## Accessing Tokens in Tools

Tools can access the current authentication token using `get_access_token()` from `fastmcp.server.dependencies`. This enables tools to make decisions based on user identity or permissions beyond simple authorization checks.

```python
from fastmcp import FastMCP
from fastmcp.server.dependencies import get_access_token

mcp = FastMCP("Token Access Server")

@mcp.tool
def personalized_greeting() -> str:
    """Greet the user based on their token claims."""
    token = get_access_token()

    if token is None:
        return "Hello, guest!"

    name = token.claims.get("name", "user")
    return f"Hello, {name}!"

@mcp.tool
def user_dashboard() -> dict:
    """Return user-specific data based on token."""
    token = get_access_token()

    if token is None:
        return {"error": "Not authenticated"}

    return {
        "client_id": token.client_id,
        "scopes": token.scopes,
        "claims": token.claims,
    }
```

## Reference

### AccessToken

The `AccessToken` object contains information extracted from the OAuth token.

| Property     | Type               | Description                         |
| ------------ | ------------------ | ----------------------------------- |
| `token`      | `str`              | The raw token string                |
| `client_id`  | `str \| None`      | OAuth client identifier             |
| `scopes`     | `list[str]`        | Granted OAuth scopes                |
| `expires_at` | `datetime \| None` | Token expiration time               |
| `claims`     | `dict[str, Any]`   | All JWT claims or custom token data |

### AuthContext

The `AuthContext` dataclass is passed to all auth check functions.

| Property    | Type                         | Description                                        |
| ----------- | ---------------------------- | -------------------------------------------------- |
| `token`     | `AccessToken \| None`        | Current access token, or `None` if unauthenticated |
| `component` | `Tool \| Resource \| Prompt` | The component being accessed                       |

Access to the component object enables authorization decisions based on metadata like tags, name, or custom properties.

```python
from fastmcp.server.auth import AuthContext

def require_matching_tag(ctx: AuthContext) -> bool:
    """Require a scope matching each of the component's tags."""
    if ctx.token is None:
        return False
    user_scopes = set(ctx.token.scopes)
    return ctx.component.tags.issubset(user_scopes)
```

### Imports

```python
from fastmcp.server.auth import (
    AccessToken,       # Token with .token, .client_id, .scopes, .expires_at, .claims
    AuthContext,       # Context with .token, .component
    AuthCheck,         # Type alias: sync or async Callable[[AuthContext], bool]
    require_scopes,    # Built-in: requires specific scopes
    restrict_tag,      # Built-in: tag-based scope requirements
    run_auth_checks,   # Utility: run checks with AND logic
)

from fastmcp.server.middleware import AuthMiddleware
```

> ## Documentation Index
> Fetch the complete documentation index at: https://gofastmcp.com/llms.txt
> Use this file to discover all available pages before exploring further.




---

## 🚀 Usage

**Reference this template:** `@skill-fastmcp-server.md`


**Platform-specific:**
- **GitHub Copilot**: Add to `.github/copilot-instructions.md`
- **Augment Code**: Use `aug context add` command
- **Cursor/Windsurf**: Reference in chat with `@filename`
- **Claude**: Add to Project Knowledge
- **ChatGPT**: Add to Custom GPT configuration
