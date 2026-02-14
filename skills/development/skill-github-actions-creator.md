---
id: skill-github-actions-creator
type: skill
name: github-actions-creator
description: Use when the user wants to create, generate, or set up a GitHub Actions
  workflow. Handles CI/CD pipelines, testing, deployment, linting, security scanning,
  release automation, Docker builds, scheduled tasks, and any custom workflow for
  any language or framework.
category: development
complexity: medium
keywords:
- angular
- audit
- ci/cd
- deploy
- deployment
- docker
- github
- go
- java
- python
- react
- rust
- security
- svelte
- test
- testing
- vue
- vulnerability
capabilities: []
token_estimate: 1606
has_references: true
reference_count: 1
---

<!-- Converted from Claude Skill Template -->
<!-- Complexity: Medium -->
<!-- Estimated Tokens: ~1,606 -->
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




# github-actions-creator

> Use when the user wants to create, generate, or set up a GitHub Actions workflow. Handles CI/CD pipelines, testing, deployment, linting, security scanning, release automation, Docker builds, scheduled tasks, and any custom workflow for any language or framework.

# GitHub Actions Creator

You are an expert at creating GitHub Actions workflows. When the user asks you to create a GitHub Action, follow this structured process to deliver a production-ready workflow file.

## Workflow Creation Process

### Step 1: Analyze the Project

Before writing any YAML, scan the project to understand the stack:

1. **Check for language/framework indicators:**
   - `package.json` → Node.js (check for React, Next.js, Vue, Angular, Svelte, etc.)
   - `requirements.txt` / `pyproject.toml` / `setup.py` → Python
   - `go.mod` → Go
   - `Cargo.toml` → Rust
   - `pom.xml` / `build.gradle` → Java/Kotlin
   - `Gemfile` → Ruby
   - `composer.json` → PHP
   - `pubspec.yaml` → Dart/Flutter
   - `Package.swift` → Swift
   - `*.csproj` / `*.sln` → .NET

2. **Check for existing CI/CD:**
   - `.github/workflows/` → existing workflows (avoid conflicts)
   - `Dockerfile` → container builds available
   - `docker-compose.yml` → multi-service setup
   - `vercel.json` / `netlify.toml` → deployment targets
   - `terraform/` / `pulumi/` → infrastructure as code

3. **Check for tooling:**
   - `.eslintrc*` / `eslint.config.*` → ESLint configured
   - `prettier*` → Prettier configured
   - `jest.config*` / `vitest.config*` / `pytest.ini` → test framework
   - `.env.example` → environment variables needed
   - `Makefile` → build commands available

### Step 2: Ask Clarifying Questions (if needed)

If the user's request is ambiguous, ask ONE focused question. Common clarifications:

- **"Create a CI pipeline"** → "Should it run tests only, or also lint and type-check?"
- **"Add deployment"** → "Where does this deploy? (Vercel, AWS, GCP, Docker Hub, etc.)"
- **"Set up tests"** → "Should tests run on PR only, or also on push to main?"

If the intent is clear, skip this step and proceed.

### Step 3: Generate the Workflow

Create the `.github/workflows/{name}.yml` file following these rules:

#### File Naming
- Use descriptive kebab-case names: `ci.yml`, `deploy-production.yml`, `release.yml`
- For simple CI: `ci.yml`
- For deployment: `deploy.yml` or `deploy-{target}.yml`
- For scheduled tasks: `scheduled-{task}.yml`

#### YAML Structure Rules

```yaml
name: Human-readable name        # Always include

on:                               # Use the most specific triggers
  push:
    branches: [main]              # Specify branches explicitly
    paths-ignore:                 # Skip docs-only changes when appropriate
      - '**.md'
      - 'docs/**'
  pull_request:
    branches: [main]

permissions:                      # Always set minimal permissions
  contents: read

concurrency:                      # Prevent duplicate runs on PRs
  group: ${{ github.workflow }}-${{ github.ref }}
  cancel-in-progress: true

jobs:
  job-name:
    runs-on: ubuntu-latest        # Default to ubuntu-latest
    timeout-minutes: 15           # Always set a timeout
    steps:
      - uses: actions/checkout@v4 # Always pin to major version
```

## Core Patterns by Use Case

### CI (Test + Lint)

**Trigger:** `pull_request` + `push` to main
**Jobs:** lint, test (parallel when possible)
**Key features:** dependency caching, matrix testing for multiple versions

### Deployment

**Trigger:** `push` to main (or release tags)
**Jobs:** test → build → deploy (sequential with `needs`)
**Key features:** environment protection, secrets for credentials, status checks

### Release / Publish

**Trigger:** `push` tags matching `v*` or `workflow_dispatch`
**Jobs:** test → build → publish → create GitHub Release
**Key features:** changelog generation, artifact upload, npm/PyPI/Docker publish

### Scheduled Tasks

**Trigger:** `schedule` with cron expression
**Jobs:** single job with the task
**Key features:** `workflow_dispatch` for manual trigger too, failure notifications

### Security Scanning

**Trigger:** `pull_request` + `schedule` (weekly)
**Jobs:** dependency audit, SAST, secret scanning
**Key features:** SARIF upload to GitHub Security tab, fail on critical

### Docker Build & Push

**Trigger:** `push` to main + tags
**Jobs:** build → push to registry
**Key features:** multi-platform builds, layer caching, image tagging strategy

## Essential Actions Reference

### Setup Actions (always pin to major version)
| Action | Purpose |
|--------|---------|
| `actions/checkout@v4` | Clone repository |
| `actions/setup-node@v4` | Node.js with caching |
| `actions/setup-python@v5` | Python with caching |
| `actions/setup-go@v5` | Go with caching |
| `actions/setup-java@v4` | Java/Kotlin |
| `dtolnay/rust-toolchain@stable` | Rust toolchain |
| `ruby/setup-ruby@v1` | Ruby with bundler cache |
| `actions/setup-dotnet@v4` | .NET SDK |

### Build & Deploy Actions
| Action | Purpose |
|--------|---------|
| `docker/build-push-action@v6` | Docker multi-platform builds |
| `docker/login-action@v3` | Docker registry authentication |
| `aws-actions/configure-aws-credentials@v4` | AWS authentication |
| `google-github-actions/auth@v2` | GCP authentication |
| `azure/login@v2` | Azure authentication |
| `cloudflare/wrangler-action@v3` | Cloudflare Workers deploy |
| `amondnet/vercel-action@v25` | Vercel deployment |

### Quality & Security Actions
| Action | Purpose |
|--------|---------|
| `github/codeql-action/analyze@v3` | CodeQL SAST scanning |
| `aquasecurity/trivy-action@master` | Container vulnerability scan |
| `codecov/codecov-action@v4` | Coverage upload |
| `actions/dependency-review-action@v4` | Dependency audit on PRs |

### Utility Actions
| Action | Purpose |
|--------|---------|
| `actions/cache@v4` | Generic caching |
| `actions/upload-artifact@v4` | Store build artifacts |
| `actions/download-artifact@v4` | Retrieve artifacts between jobs |
| `softprops/action-gh-release@v2` | Create GitHub Releases |
| `slackapi/slack-github-action@v2` | Slack notifications |
| `peter-evans/create-pull-request@v7` | Automated PR creation |

## Security Best Practices (ALWAYS follow)

1. **Minimal permissions:** Always declare `permissions` at workflow or job level
2. **Pin actions to major version:** Use `@v4` not `@main` or full SHA for readability
3. **Never echo secrets:** Secrets are masked but avoid `echo ${{ secrets.X }}`
4. **Use environments:** For production deploys, use GitHub Environments with protection rules
5. **Validate inputs:** For `workflow_dispatch`, validate input values
6. **Avoid script injection:** Never use `${{ github.event.*.body }}` directly in `run:` — pass via environment variables
7. **Use GITHUB_TOKEN:** Prefer `${{ secrets.GITHUB_TOKEN }}` over PATs when possible
8. **Concurrency controls:** Use `concurrency` to prevent parallel deploys

```yaml
# WRONG - script injection vulnerability
- run: echo "${{ github.event.issue.title }}"

# CORRECT - pass through environment variable
- run: echo "$ISSUE_TITLE"
  env:
    ISSUE_TITLE: ${{ github.event.issue.title }}
```

## Caching Strategies

### Node.js
```yaml
- uses: actions/setup-node@v4
  with:
    node-version: 20
    cache: 'npm'  # or 'yarn' or 'pnpm'
```

### Python
```yaml
- uses: actions/setup-python@v5
  with:
    python-version: '3.12'
    cache: 'pip'  # or 'poetry' or 'pipenv'
```

### Go
```yaml
- uses: actions/setup-go@v5
  with:
    go-version: '1.22'
    cache: true
```

### Rust
```yaml
- uses: actions/cache@v4
  with:
    path: |
      ~/.cargo/bin/
      ~/.cargo/registry/index/
      ~/.cargo/registry/cache/
      target/
    key: ${{ runner.os }}-cargo-${{ hashFiles('**/Cargo.lock') }}
```

### Docker
```yaml
- uses: docker/build-push-action@v6
  with:
    cache-from: type=gha
    cache-to: type=gha,mode=max
```

## Matrix Testing Patterns

### Multiple Node.js versions
```yaml
strategy:
  matrix:
    node-version: [18, 20, 22]
  fail-fast: false
```

### Multiple OS
```yaml
strategy:
  matrix:
    os: [ubuntu-latest, macos-latest, windows-latest]
runs-on: ${{ matrix.os }}
```

### Complex matrix with exclusions
```yaml
strategy:
  matrix:
    os: [ubuntu-latest, windows-latest]
    node-version: [18, 20]
    exclude:
      - os: windows-latest
        node-version: 18
```

## Cron Syntax Quick Reference

| Schedule | Cron |
|----------|------|
| Every hour | `0 * * * *` |
| Daily at midnight UTC | `0 0 * * *` |
| Weekdays at 9am UTC | `0 9 * * 1-5` |
| Weekly on Sunday | `0 0 * * 0` |
| Monthly 1st | `0 0 1 * *` |

## Output Format

After creating the workflow file, provide:

1. **What the workflow does** — one-paragraph summary
2. **Required secrets** — list any secrets the user needs to configure in Settings > Secrets
3. **Required permissions** — if the workflow needs non-default repository permissions
4. **How to test** — how to trigger the workflow (push, create PR, manual dispatch)

## Common Patterns to Combine

When the user asks for something generic like "set up CI/CD", create a single workflow with multiple jobs:

```yaml
jobs:
  lint:        # Fast feedback
  test:        # Core validation
  build:       # Ensure it compiles/bundles
    needs: [lint, test]
  deploy:      # Only after everything passes
    needs: build
    if: github.ref == 'refs/heads/main'
```

Keep workflows focused. Prefer one workflow per concern over one massive workflow, unless the jobs are tightly coupled.


---


## 📚 Reference Materials


### Workflow Templates

# Workflow Templates

Complete, production-ready templates for the most common GitHub Actions use cases. Use these as starting points and adapt to the user's project.

---

## Node.js CI

```yaml
name: CI

on:
  push:
    branches: [main]
  pull_request:
    branches: [main]

permissions:
  contents: read

concurrency:
  group: ${{ github.workflow }}-${{ github.ref }}
  cancel-in-progress: true

jobs:
  lint:
    runs-on: ubuntu-latest
    timeout-minutes: 10
    steps:
      - uses: actions/checkout@v4

      - uses: actions/setup-node@v4
        with:
          node-version: 20
          cache: 'npm'

      - run: npm ci

      - run: npm run lint

      - run: npm run typecheck
        if: hashFiles('tsconfig.json') != ''

  test:
    runs-on: ubuntu-latest
    timeout-minutes: 15
    strategy:
      matrix:
        node-version: [18, 20, 22]
      fail-fast: false

    steps:
      - uses: actions/checkout@v4

      - uses: actions/setup-node@v4
        with:
          node-version: ${{ matrix.node-version }}
          cache: 'npm'

      - run: npm ci

      - run: npm test

      - uses: codecov/codecov-action@v4
        if: matrix.node-version == 20
        with:
          token: ${{ secrets.CODECOV_TOKEN }}
```

---

## Python CI

```yaml
name: CI

on:
  push:
    branches: [main]
  pull_request:
    branches: [main]

permissions:
  contents: read

concurrency:
  group: ${{ github.workflow }}-${{ github.ref }}
  cancel-in-progress: true

jobs:
  lint:
    runs-on: ubuntu-latest
    timeout-minutes: 10
    steps:
      - uses: actions/checkout@v4

      - uses: actions/setup-python@v5
        with:
          python-version: '3.12'
          cache: 'pip'

      - run: pip install ruff mypy

      - run: ruff check .

      - run: ruff format --check .

  test:
    runs-on: ubuntu-latest
    timeout-minutes: 15
    strategy:
      matrix:
        python-version: ['3.10', '3.11', '3.12']
      fail-fast: false

    steps:
      - uses: actions/checkout@v4

      - uses: actions/setup-python@v5
        with:
          python-version: ${{ matrix.python-version }}
          cache: 'pip'

      - run: pip install -e ".[dev]"

      - run: pytest --cov --cov-report=xml

      - uses: codecov/codecov-action@v4
        if: matrix.python-version == '3.12'
        with:
          token: ${{ secrets.CODECOV_TOKEN }}
```

---

## Go CI

```yaml
name: CI

on:
  push:
    branches: [main]
  pull_request:
    branches: [main]

permissions:
  contents: read

concurrency:
  group: ${{ github.workflow }}-${{ github.ref }}
  cancel-in-progress: true

jobs:
  lint:
    runs-on: ubuntu-latest
    timeout-minutes: 10
    steps:
      - uses: actions/checkout@v4

      - uses: actions/setup-go@v5
        with:
          go-version: '1.22'

      - uses: golangci/golangci-lint-action@v6
        with:
          version: latest

  test:
    runs-on: ubuntu-latest
    timeout-minutes: 15
    steps:
      - uses: actions/checkout@v4

      - uses: actions/setup-go@v5
        with:
          go-version: '1.22'

      - run: go test -race -coverprofile=coverage.out ./...

      - uses: codecov/codecov-action@v4
        with:
          files: coverage.out
          token: ${{ secrets.CODECOV_TOKEN }}

  build:
    runs-on: ubuntu-latest
    timeout-minutes: 10
    needs: [lint, test]
    steps:
      - uses: actions/checkout@v4

      - uses: actions/setup-go@v5
        with:
          go-version: '1.22'

      - run: go build ./...
```

---

## Rust CI

```yaml
name: CI

on:
  push:
    branches: [main]
  pull_request:
    branches: [main]

permissions:
  contents: read

concurrency:
  group: ${{ github.workflow }}-${{ github.ref }}
  cancel-in-progress: true

jobs:
  check:
    runs-on: ubuntu-latest
    timeout-minutes: 20
    steps:
      - uses: actions/checkout@v4

      - uses: dtolnay/rust-toolchain@stable
        with:
          components: rustfmt, clippy

      - uses: actions/cache@v4
        with:
          path: |
            ~/.cargo/bin/
            ~/.cargo/registry/index/
            ~/.cargo/registry/cache/
            target/
          key: ${{ runner.os }}-cargo-${{ hashFiles('**/Cargo.lock') }}

      - run: cargo fmt --all -- --check

      - run: cargo clippy --all-targets -- -D warnings

  test:
    runs-on: ubuntu-latest
    timeout-minutes: 20
    steps:
      - uses: actions/checkout@v4

      - uses: dtolnay/rust-toolchain@stable

      - uses: actions/cache@v4
        with:
          path: |
            ~/.cargo/bin/
            ~/.cargo/registry/index/
            ~/.cargo/registry/cache/
            target/
          key: ${{ runner.os }}-cargo-${{ hashFiles('**/Cargo.lock') }}

      - run: cargo test --all
```

---

## Docker Build & Push

```yaml
name: Docker

on:
  push:
    branches: [main]
    tags: ['v*']
  pull_request:
    branches: [main]

permissions:
  contents: read
  packages: write

concurrency:
  group: ${{ github.workflow }}-${{ github.ref }}
  cancel-in-progress: true

jobs:
  build:
    runs-on: ubuntu-latest
    timeout-minutes: 30
    steps:
      - uses: actions/checkout@v4

      - uses: docker/setup-qemu-action@v3

      - uses: docker/setup-buildx-action@v3

      - uses: docker/login-action@v3
        if: github.event_name != 'pull_request'
        with:
          registry: ghcr.io
          username: ${{ github.actor }}
          password: ${{ secrets.GITHUB_TOKEN }}

      - uses: docker/metadata-action@v5
        id: meta
        with:
          images: ghcr.io/${{ github.repository }}
          tags: |
            type=ref,event=branch
            type=semver,pattern={{version}}
            type=semver,pattern={{major}}.{{minor}}
            type=sha

      - uses: docker/build-push-action@v6
        with:
          context: .
          platforms: linux/amd64,linux/arm64
          push: ${{ github.event_name != 'pull_request' }}
          tags: ${{ steps.meta.outputs.tags }}
          labels: ${{ steps.meta.outputs.labels }}
          cache-from: type=gha
          cache-to: type=gha,mode=max
```

---

## Deploy to Vercel

```yaml
name: Deploy

on:
  push:
    branches: [main]
  pull_request:
    branches: [main]

permissions:
  contents: read
  pull-requests: write

concurrency:
  group: ${{ github.workflow }}-${{ github.ref }}
  cancel-in-progress: true

jobs:
  deploy:
    runs-on: ubuntu-latest
    timeout-minutes: 15
    environment:
      name: ${{ github.ref == 'refs/heads/main' && 'production' || 'preview' }}
      url: ${{ steps.deploy.outputs.url }}

    steps:
      - uses: actions/checkout@v4

      - uses: actions/setup-node@v4
        with:
          node-version: 20
          cache: 'npm'

      - run: npm ci

      - run: npm test

      - name: Deploy to Vercel
        id: deploy
        run: |
          npm i -g vercel
          if [ "${{ github.ref }}" = "refs/heads/main" ]; then
            url=$(vercel --prod --token=${{ secrets.VERCEL_TOKEN }} 2>&1 | tail -1)
          else
            url=$(vercel --token=${{ secrets.VERCEL_TOKEN }} 2>&1 | tail -1)
          fi
          echo "url=$url" >> $GITHUB_OUTPUT
        env:
          VERCEL_ORG_ID: ${{ secrets.VERCEL_ORG_ID }}
          VERCEL_PROJECT_ID: ${{ secrets.VERCEL_PROJECT_ID }}

      - name: Comment PR with preview URL
        if: github.event_name == 'pull_request'
        uses: actions/github-script@v7
        with:
          script: |
            github.rest.issues.createComment({
              owner: context.repo.owner,
              repo: context.repo.repo,
              issue_number: context.issue.number,
              body: `Preview deployed: ${{ steps.deploy.outputs.url }}`
            })
```

---

## Deploy to AWS (ECS)

```yaml
name: Deploy to AWS

on:
  push:
    branches: [main]

permissions:
  id-token: write
  contents: read

concurrency:
  group: deploy-production
  cancel-in-progress: false

jobs:
  deploy:
    runs-on: ubuntu-latest
    timeout-minutes: 20
    environment: production

    steps:
      - uses: actions/checkout@v4

      - uses: aws-actions/configure-aws-credentials@v4
        with:
          role-to-assume: ${{ secrets.AWS_ROLE_ARN }}
          aws-region: us-east-1

      - uses: aws-actions/amazon-ecr-login@v2
        id: ecr

      - name: Build and push image
        run: |
          IMAGE=${{ steps.ecr.outputs.registry }}/${{ github.repository }}:${{ github.sha }}
          docker build -t $IMAGE .
          docker push $IMAGE
          echo "image=$IMAGE" >> $GITHUB_OUTPUT
        id: build

      - name: Deploy to ECS
        run: |
          aws ecs update-service \
            --cluster production \
            --service app \
            --force-new-deployment
```

---

## npm Publish on Release

```yaml
name: Publish

on:
  push:
    tags: ['v*']

permissions:
  contents: write
  id-token: write

jobs:
  publish:
    runs-on: ubuntu-latest
    timeout-minutes: 15
    steps:
      - uses: actions/checkout@v4

      - uses: actions/setup-node@v4
        with:
          node-version: 20
          cache: 'npm'
          registry-url: 'https://registry.npmjs.org'

      - run: npm ci

      - run: npm test

      - run: npm publish --provenance --access public
        env:
          NODE_AUTH_TOKEN: ${{ secrets.NPM_TOKEN }}

      - uses: softprops/action-gh-release@v2
        with:
          generate_release_notes: true
```

---

## PyPI Publish

```yaml
name: Publish to PyPI

on:
  push:
    tags: ['v*']

permissions:
  contents: write
  id-token: write

jobs:
  publish:
    runs-on: ubuntu-latest
    timeout-minutes: 15
    environment: pypi

    steps:
      - uses: actions/checkout@v4

      - uses: actions/setup-python@v5
        with:
          python-version: '3.12'

      - run: pip install build

      - run: python -m build

      - uses: pypa/gh-action-pypi-publish@release/v1

      - uses: softprops/action-gh-release@v2
        with:
          generate_release_notes: true
```

---

## Scheduled Dependency Updates Check

```yaml
name: Dependency Review

on:
  pull_request:
    branches: [main]
  schedule:
    - cron: '0 9 * * 1'  # Monday 9am UTC
  workflow_dispatch:

permissions:
  contents: read
  pull-requests: write

jobs:
  audit:
    runs-on: ubuntu-latest
    timeout-minutes: 10
    steps:
      - uses: actions/checkout@v4

      - uses: actions/dependency-review-action@v4
        if: github.event_name == 'pull_request'

      - uses: actions/setup-node@v4
        with:
          node-version: 20
          cache: 'npm'

      - run: npm ci

      - run: npm audit --audit-level=high
        continue-on-error: ${{ github.event_name == 'schedule' }}
```

---

## Monorepo CI (with path filters)

```yaml
name: CI

on:
  push:
    branches: [main]
  pull_request:
    branches: [main]

permissions:
  contents: read

concurrency:
  group: ${{ github.workflow }}-${{ github.ref }}
  cancel-in-progress: true

jobs:
  changes:
    runs-on: ubuntu-latest
    timeout-minutes: 5
    outputs:
      frontend: ${{ steps.filter.outputs.frontend }}
      backend: ${{ steps.filter.outputs.backend }}
      infra: ${{ steps.filter.outputs.infra }}
    steps:
      - uses: actions/checkout@v4
      - uses: dorny/paths-filter@v3
        id: filter
        with:
          filters: |
            frontend:
              - 'packages/frontend/**'
            backend:
              - 'packages/backend/**'
            infra:
              - 'terraform/**'
              - 'docker/**'

  frontend:
    needs: changes
    if: needs.changes.outputs.frontend == 'true'
    runs-on: ubuntu-latest
    timeout-minutes: 15
    defaults:
      run:
        working-directory: packages/frontend
    steps:
      - uses: actions/checkout@v4
      - uses: actions/setup-node@v4
        with:
          node-version: 20
          cache: 'npm'
          cache-dependency-path: packages/frontend/package-lock.json
      - run: npm ci
      - run: npm run lint
      - run: npm test
      - run: npm run build

  backend:
    needs: changes
    if: needs.changes.outputs.backend == 'true'
    runs-on: ubuntu-latest
    timeout-minutes: 15
    defaults:
      run:
        working-directory: packages/backend
    steps:
      - uses: actions/checkout@v4
      - uses: actions/setup-node@v4
        with:
          node-version: 20
          cache: 'npm'
          cache-dependency-path: packages/backend/package-lock.json
      - run: npm ci
      - run: npm test
```

---

## Release Please (Automated Versioning)

```yaml
name: Release

on:
  push:
    branches: [main]

permissions:
  contents: write
  pull-requests: write

jobs:
  release:
    runs-on: ubuntu-latest
    timeout-minutes: 10
    outputs:
      release_created: ${{ steps.release.outputs.release_created }}
      tag_name: ${{ steps.release.outputs.tag_name }}

    steps:
      - uses: googleapis/release-please-action@v4
        id: release
        with:
          release-type: node  # or python, go, etc.

  publish:
    needs: release
    if: needs.release.outputs.release_created
    runs-on: ubuntu-latest
    timeout-minutes: 15
    steps:
      - uses: actions/checkout@v4
      - uses: actions/setup-node@v4
        with:
          node-version: 20
          cache: 'npm'
          registry-url: 'https://registry.npmjs.org'
      - run: npm ci
      - run: npm publish --provenance --access public
        env:
          NODE_AUTH_TOKEN: ${{ secrets.NPM_TOKEN }}
```

---

## Cloudflare Workers Deploy

```yaml
name: Deploy Worker

on:
  push:
    branches: [main]
  pull_request:
    branches: [main]

permissions:
  contents: read

concurrency:
  group: ${{ github.workflow }}-${{ github.ref }}
  cancel-in-progress: true

jobs:
  deploy:
    runs-on: ubuntu-latest
    timeout-minutes: 10
    steps:
      - uses: actions/checkout@v4

      - uses: actions/setup-node@v4
        with:
          node-version: 20
          cache: 'npm'

      - run: npm ci

      - name: Deploy
        uses: cloudflare/wrangler-action@v3
        with:
          apiToken: ${{ secrets.CLOUDFLARE_API_TOKEN }}
          command: ${{ github.ref == 'refs/heads/main' && 'deploy' || 'deploy --dry-run' }}
```

---

## E2E Tests with Playwright

```yaml
name: E2E Tests

on:
  push:
    branches: [main]
  pull_request:
    branches: [main]
  schedule:
    - cron: '0 6 * * *'  # Daily at 6am UTC

permissions:
  contents: read

concurrency:
  group: ${{ github.workflow }}-${{ github.ref }}
  cancel-in-progress: true

jobs:
  e2e:
    runs-on: ubuntu-latest
    timeout-minutes: 30
    steps:
      - uses: actions/checkout@v4

      - uses: actions/setup-node@v4
        with:
          node-version: 20
          cache: 'npm'

      - run: npm ci

      - run: npx playwright install --with-deps chromium

      - run: npm run build

      - run: npx playwright test
        env:
          CI: true

      - uses: actions/upload-artifact@v4
        if: failure()
        with:
          name: playwright-report
          path: playwright-report/
          retention-days: 7
```

---

## Security Scanning (CodeQL + Trivy)

```yaml
name: Security

on:
  push:
    branches: [main]
  pull_request:
    branches: [main]
  schedule:
    - cron: '0 0 * * 1'  # Weekly on Monday

permissions:
  contents: read
  security-events: write

jobs:
  codeql:
    runs-on: ubuntu-latest
    timeout-minutes: 20
    steps:
      - uses: actions/checkout@v4

      - uses: github/codeql-action/init@v3
        with:
          languages: javascript-typescript  # or python, go, java, etc.

      - uses: github/codeql-action/analyze@v3

  trivy:
    runs-on: ubuntu-latest
    timeout-minutes: 10
    if: hashFiles('Dockerfile') != ''
    steps:
      - uses: actions/checkout@v4

      - uses: aquasecurity/trivy-action@master
        with:
          scan-type: 'fs'
          format: 'sarif'
          output: 'trivy-results.sarif'
          severity: 'CRITICAL,HIGH'

      - uses: github/codeql-action/upload-sarif@v3
        with:
          sarif_file: 'trivy-results.sarif'
```

---

## Terraform Plan & Apply

```yaml
name: Infrastructure

on:
  push:
    branches: [main]
    paths: ['terraform/**']
  pull_request:
    branches: [main]
    paths: ['terraform/**']

permissions:
  contents: read
  pull-requests: write
  id-token: write

concurrency:
  group: terraform-${{ github.ref }}
  cancel-in-progress: false

jobs:
  plan:
    runs-on: ubuntu-latest
    timeout-minutes: 15
    defaults:
      run:
        working-directory: terraform
    steps:
      - uses: actions/checkout@v4

      - uses: hashicorp/setup-terraform@v3
        with:
          terraform_version: '1.7'

      - uses: aws-actions/configure-aws-credentials@v4
        with:
          role-to-assume: ${{ secrets.AWS_ROLE_ARN }}
          aws-region: us-east-1

      - run: terraform init

      - run: terraform validate

      - run: terraform plan -out=tfplan
        id: plan

      - uses: actions/github-script@v7
        if: github.event_name == 'pull_request'
        with:
          script: |
            const plan = `${{ steps.plan.outputs.stdout }}`;
            github.rest.issues.createComment({
              owner: context.repo.owner,
              repo: context.repo.repo,
              issue_number: context.issue.number,
              body: `### Terraform Plan\n\`\`\`\n${plan}\n\`\`\``
            })

  apply:
    needs: plan
    if: github.ref == 'refs/heads/main'
    runs-on: ubuntu-latest
    timeout-minutes: 30
    environment: production
    defaults:
      run:
        working-directory: terraform
    steps:
      - uses: actions/checkout@v4

      - uses: hashicorp/setup-terraform@v3
        with:
          terraform_version: '1.7'

      - uses: aws-actions/configure-aws-credentials@v4
        with:
          role-to-assume: ${{ secrets.AWS_ROLE_ARN }}
          aws-region: us-east-1

      - run: terraform init

      - run: terraform apply -auto-approve
```




---

## 🚀 Usage

**Reference this template:** `@skill-github-actions-creator.md`


**Platform-specific:**
- **GitHub Copilot**: Add to `.github/copilot-instructions.md`
- **Augment Code**: Use `aug context add` command
- **Cursor/Windsurf**: Reference in chat with `@filename`
- **Claude**: Add to Project Knowledge
- **ChatGPT**: Add to Custom GPT configuration
