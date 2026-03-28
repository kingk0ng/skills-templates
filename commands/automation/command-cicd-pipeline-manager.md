---
id: command-cicd-pipeline-manager
type: command
name: CI/CD Pipeline Manager
description: '---

  allowed-tools: Read, Write, Edit, Bash

  argument-hint: [pipeline-name] | setup | status | fix

  description: Manage and automate CI/CD pipeline configuration with GitHub Actions,
  multi-environment su...'
category: automation
complexity: medium
keywords:
- api
- audit
- ci/cd
- deploy
- deployment
- docker
- github
- optimization
- performance
- rest
- security
- test
- testing
capabilities: []
token_estimate: 1253
has_scripts: true
languages:
- yaml
- bash
---

<!-- Converted from Claude Command Template -->
<!-- Complexity: Medium -->
<!-- Estimated Tokens: ~1,253 -->


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




# CI/CD Pipeline Manager

> ---
allowed-tools: Read, Write, Edit, Bash
argument-hint: [pipeline-name] | setup | status | fix
description: Manage and automate CI/CD pipeline configuration with GitHub Actions, multi-environment su...

---
allowed-capabilities: File operations, code editing, terminal access, search
argument-hint: [pipeline-name] | setup | status | fix
description: Manage and automate CI/CD pipeline configuration with GitHub Actions, multi-environment support, and deployment strategies
---

# CI/CD Pipeline Manager

Manage CI/CD pipeline automation: $ARGUMENTS

## Current Pipeline State

- GitHub Actions: !`find .github/workflows -name "*.yml" -o -name "*.yaml" 2>/dev/null | head -5`
- CI configuration: @.github/workflows/ (if exists)
- Package scripts: @package.json
- Environment files: !`find . -name ".env*" | head -3`
- Recent workflow runs: !`gh run list --limit 5 2>/dev/null || echo "GitHub CLI not available"`

## Task

Automate CI/CD pipeline management with comprehensive workflow orchestration.

## Pipeline Operations

### Setup New Pipeline
Create complete CI/CD pipeline with:

```yaml
# .github/workflows/ci.yml
name: CI Pipeline
on:
  push:
    branches: [main, develop]
  pull_request:
    branches: [main]

jobs:
  test:
    runs-on: ubuntu-latest
    strategy:
      matrix:
        node-version: [18, 20]
    steps:
      - uses: actions/checkout@v4
      - name: Setup Node.js
        uses: actions/setup-node@v4
        with:
          node-version: ${{ matrix.node-version }}
          cache: 'npm'
      
      - name: Install dependencies
        run: npm ci
      
      - name: Run linter
        run: npm run lint
      
      - name: Run tests
        run: npm run test:coverage
      
      - name: Build application
        run: npm run build
      
      - name: Upload coverage
        uses: codecov/codecov-action@v3
        with:
          file: ./coverage/lcov.info
```

### Multi-Environment Deployment
```yaml
# .github/workflows/deploy.yml
name: Deploy
on:
  push:
    branches: [main]
  release:
    types: [published]

jobs:
  deploy-staging:
    if: github.ref == 'refs/heads/main'
    runs-on: ubuntu-latest
    environment: staging
    steps:
      - uses: actions/checkout@v4
      - name: Deploy to Staging
        run: |
          npm run build:staging
          npm run deploy:staging
        env:
          STAGING_API_URL: ${{ secrets.STAGING_API_URL }}
          STAGING_SECRET: ${{ secrets.STAGING_SECRET }}

  deploy-production:
    if: github.event_name == 'release'
    runs-on: ubuntu-latest
    environment: production
    needs: [test]
    steps:
      - uses: actions/checkout@v4
      - name: Deploy to Production
        run: |
          npm run build:production
          npm run deploy:production
        env:
          PROD_API_URL: ${{ secrets.PROD_API_URL }}
          PROD_SECRET: ${{ secrets.PROD_SECRET }}
```

### Security & Quality Gates
```yaml
  security-scan:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4
      
      - name: Run security audit
        run: npm audit --audit-level=moderate
      
      - name: Scan for secrets
        uses: trufflesecurity/trufflehog@main
        with:
          path: ./
          base: main
          head: HEAD
      
      - name: SAST Scan
        uses: github/super-linter@v4
        env:
          DEFAULT_BRANCH: main
          GITHUB_TOKEN: ${{ secrets.GITHUB_TOKEN }}
```

### Performance Testing
```yaml
  performance:
    runs-on: ubuntu-latest
    if: github.event_name == 'pull_request'
    steps:
      - uses: actions/checkout@v4
      - name: Performance Test
        run: |
          npm run build
          npm run start:test &
          sleep 10
          npx lighthouse http://localhost:3000 --output=json --output-path=./lighthouse.json
      
      - name: Comment PR
        uses: actions/github-script@v6
        with:
          script: |
            const fs = require('fs');
            const lighthouse = JSON.parse(fs.readFileSync('./lighthouse.json'));
            const score = lighthouse.lhr.categories.performance.score * 100;
            
            github.rest.issues.createComment({
              issue_number: context.issue.number,
              owner: context.repo.owner,
              repo: context.repo.repo,
              body: `⚡ Performance Score: ${score}/100`
            });
```

## Advanced Features

### 1. **Matrix Strategy Testing**
```yaml
strategy:
  matrix:
    os: [ubuntu-latest, windows-latest, macos-latest]
    node-version: [16, 18, 20]
    include:
      - os: ubuntu-latest
        node-version: 20
        coverage: true
```

### 2. **Conditional Workflows**
```yaml
- name: Skip CI
  if: contains(github.event.head_commit.message, '[skip ci]')
  run: echo "Skipping CI as requested"

- name: Deploy only on version tags
  if: startsWith(github.ref, 'refs/tags/v')
  run: npm run deploy
```

### 3. **Workflow Dependencies**
```yaml
jobs:
  test:
    runs-on: ubuntu-latest
    
  build:
    needs: test
    runs-on: ubuntu-latest
    
  deploy:
    needs: [test, build]
    if: github.ref == 'refs/heads/main'
    runs-on: ubuntu-latest
```

### 4. **Cache Optimization**
```yaml
- name: Cache node modules
  uses: actions/cache@v3
  with:
    path: ~/.npm
    key: ${{ runner.os }}-node-${{ hashFiles('**/package-lock.json') }}
    restore-keys: |
      ${{ runner.os }}-node-

- name: Cache build output
  uses: actions/cache@v3
  with:
    path: dist
    key: build-${{ github.sha }}
```

### 5. **Artifact Management**
```yaml
- name: Upload build artifacts
  uses: actions/upload-artifact@v3
  with:
    name: dist-files
    path: dist/
    retention-days: 7

- name: Download artifacts
  uses: actions/download-artifact@v3
  with:
    name: dist-files
    path: dist/
```

### 6. **Environment Management**
```yaml
environments:
  staging:
    url: https://staging.example.com
    
  production:
    url: https://example.com
    protection_rules:
      - type: required_reviewers
        required_reviewers:
          - devops-team
      - type: wait_timer
        wait_timer: 5
```

## Pipeline Monitoring

### Status Checks
```bash
# Check workflow status
gh run list --workflow=ci.yml --limit=10

# View specific run
gh run view [run-id] --log

# Monitor failure rate
gh api repos/:owner/:repo/actions/runs \
  --jq '.workflow_runs[0:20] | map(select(.conclusion=="failure")) | length'
```

### Performance Metrics
```bash
# Average build time
gh api repos/:owner/:repo/actions/runs \
  --jq '.workflow_runs[0:50] | map(.run_duration_ms) | add / length'

# Success rate calculation
gh api repos/:owner/:repo/actions/runs \
  --jq '.workflow_runs[0:100] | [group_by(.conclusion)[] | {conclusion: .[0].conclusion, count: length}]'
```

## Troubleshooting

### Common Issues

#### 1. **Workflow Permission Issues**
```yaml
permissions:
  contents: read
  actions: write
  security-events: write
  pull-requests: write
```

#### 2. **Secret Management**
```bash
# Add repository secret
gh secret set STAGING_API_URL --body "https://staging-api.example.com"

# List secrets
gh secret list
```

#### 3. **Timeout Configuration**
```yaml
jobs:
  long-running-job:
    runs-on: ubuntu-latest
    timeout-minutes: 60
    steps:
      - name: Long task
        timeout-minutes: 30
        run: npm run long-task
```

#### 4. **Debugging Workflows**
```yaml
- name: Debug information
  run: |
    echo "Event name: ${{ github.event_name }}"
    echo "Ref: ${{ github.ref }}"
    echo "SHA: ${{ github.sha }}"
    echo "Actor: ${{ github.actor }}"
```

## Best Practices

### 1. **Fail Fast Strategy**
- Run fastest jobs first
- Use `fail-fast: true` in matrix
- Implement early validation steps

### 2. **Security First**
- Never store secrets in code
- Use least privilege permissions
- Scan for vulnerabilities early

### 3. **Efficiency Optimization**
- Use appropriate cache strategies
- Minimize workflow duration
- Parallel job execution

### 4. **Monitoring & Alerting**
- Track build success rates
- Monitor deployment frequency
- Alert on critical failures

## Integration Examples

### Docker Integration
```yaml
- name: Build Docker image
  run: |
    docker build -t myapp:${{ github.sha }} .
    docker tag myapp:${{ github.sha }} myapp:latest

- name: Push to registry
  run: |
    echo ${{ secrets.DOCKER_PASSWORD }} | docker login -u ${{ secrets.DOCKER_USERNAME }} --password-stdin
    docker push myapp:${{ github.sha }}
    docker push myapp:latest
```

### Cloud Deployment
```yaml
- name: Deploy to AWS
  uses: aws-actions/configure-aws-credentials@v2
  with:
    aws-access-key-id: ${{ secrets.AWS_ACCESS_KEY_ID }}
    aws-secret-access-key: ${{ secrets.AWS_SECRET_ACCESS_KEY }}
    aws-region: us-east-1

- name: Deploy to S3
  run: |
    aws s3 sync dist/ s3://my-bucket --delete
    aws cloudfront create-invalidation --distribution-id ${{ secrets.CLOUDFRONT_ID }} --paths "/*"
```

This pipeline manager provides comprehensive automation for modern CI/CD workflows with security, performance, and monitoring built-in.

---


## 💻 Code Examples


### Example 1

```yaml
# .github/workflows/ci.yml
name: CI Pipeline
on:
  push:
    branches: [main, develop]
  pull_request:
    branches: [main]

jobs:
  test:
    runs-on: ubuntu-latest
    strategy:
      matrix:
        node-version: [18, 20]
    steps:
      - uses: actions/checkout@v4
      - name: Setup Node.js
        uses: actions/setup-node@v4
        with:
          node-version: ${{ matrix.node-version }}
          cache: 'npm'
      
      - name: Install dependencies
        run: npm ci
      
      - name: Run linter
        run: npm run lint
      
      - name: Run tests
        run: npm run test:coverage
      
      - name: Build application
        run: npm run build
      
      - name: Upload coverage
        uses: codecov/codecov-action@v3
        with:
          file: ./coverage/lcov.info
```


### Example 2

```yaml
# .github/workflows/deploy.yml
name: Deploy
on:
  push:
    branches: [main]
  release:
    types: [published]

jobs:
  deploy-staging:
    if: github.ref == 'refs/heads/main'
    runs-on: ubuntu-latest
    environment: staging
    steps:
      - uses: actions/checkout@v4
      - name: Deploy to Staging
        run: |
          npm run build:staging
          npm run deploy:staging
        env:
          STAGING_API_URL: ${{ secrets.STAGING_API_URL }}
          STAGING_SECRET: ${{ secrets.STAGING_SECRET }}

  deploy-production:
    if: github.event_name == 'release'
    runs-on: ubuntu-latest
    environment: production
    needs: [test]
    steps:
      - uses: actions/checkout@v4
      - name: Deploy to Production
        run: |
          npm run build:production
          npm run deploy:production
        env:
          PROD_API_URL: ${{ secrets.PROD_API_URL }}
          PROD_SECRET: ${{ secrets.PROD_SECRET }}
```


### Example 3

```yaml
security-scan:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4
      
      - name: Run security audit
        run: npm audit --audit-level=moderate
      
      - name: Scan for secrets
        uses: trufflesecurity/trufflehog@main
        with:
          path: ./
          base: main
          head: HEAD
      
      - name: SAST Scan
        uses: github/super-linter@v4
        env:
          DEFAULT_BRANCH: main
          GITHUB_TOKEN: ${{ secrets.GITHUB_TOKEN }}
```


### Example 4

```yaml
performance:
    runs-on: ubuntu-latest
    if: github.event_name == 'pull_request'
    steps:
      - uses: actions/checkout@v4
      - name: Performance Test
        run: |
          npm run build
          npm run start:test &
          sleep 10
          npx lighthouse http://localhost:3000 --output=json --output-path=./lighthouse.json
      
      - name: Comment PR
        uses: actions/github-script@v6
        with:
          script: |
            const fs = require('fs');
            const lighthouse = JSON.parse(fs.readFileSync('./lighthouse.json'));
            const score = lighthouse.lhr.categories.performance.score * 100;
            
            github.rest.issues.createComment({
              issue_number: context.issue.number,
              owner: context.repo.owner,
              repo: context.repo.repo,
              body: `⚡ Performance Score: ${score}/100`
            });
```


### Example 5

```yaml
strategy:
  matrix:
    os: [ubuntu-latest, windows-latest, macos-latest]
    node-version: [16, 18, 20]
    include:
      - os: ubuntu-latest
        node-version: 20
        coverage: true
```


### Example 6

```yaml
- name: Skip CI
  if: contains(github.event.head_commit.message, '[skip ci]')
  run: echo "Skipping CI as requested"

- name: Deploy only on version tags
  if: startsWith(github.ref, 'refs/tags/v')
  run: npm run deploy
```


### Example 7

```yaml
jobs:
  test:
    runs-on: ubuntu-latest
    
  build:
    needs: test
    runs-on: ubuntu-latest
    
  deploy:
    needs: [test, build]
    if: github.ref == 'refs/heads/main'
    runs-on: ubuntu-latest
```


### Example 8

```yaml
- name: Cache node modules
  uses: actions/cache@v3
  with:
    path: ~/.npm
    key: ${{ runner.os }}-node-${{ hashFiles('**/package-lock.json') }}
    restore-keys: |
      ${{ runner.os }}-node-

- name: Cache build output
  uses: actions/cache@v3
  with:
    path: dist
    key: build-${{ github.sha }}
```


### Example 9

```yaml
- name: Upload build artifacts
  uses: actions/upload-artifact@v3
  with:
    name: dist-files
    path: dist/
    retention-days: 7

- name: Download artifacts
  uses: actions/download-artifact@v3
  with:
    name: dist-files
    path: dist/
```


### Example 10

```yaml
environments:
  staging:
    url: https://staging.example.com
    
  production:
    url: https://example.com
    protection_rules:
      - type: required_reviewers
        required_reviewers:
          - devops-team
      - type: wait_timer
        wait_timer: 5
```


### Example 11

```bash
# Check workflow status
gh run list --workflow=ci.yml --limit=10

# View specific run
gh run view [run-id] --log

# Monitor failure rate
gh api repos/:owner/:repo/actions/runs \
  --jq '.workflow_runs[0:20] | map(select(.conclusion=="failure")) | length'
```


### Example 12

```bash
# Average build time
gh api repos/:owner/:repo/actions/runs \
  --jq '.workflow_runs[0:50] | map(.run_duration_ms) | add / length'

# Success rate calculation
gh api repos/:owner/:repo/actions/runs \
  --jq '.workflow_runs[0:100] | [group_by(.conclusion)[] | {conclusion: .[0].conclusion, count: length}]'
```


### Example 13

```yaml
permissions:
  contents: read
  actions: write
  security-events: write
  pull-requests: write
```


### Example 14

```bash
# Add repository secret
gh secret set STAGING_API_URL --body "https://staging-api.example.com"

# List secrets
gh secret list
```


### Example 15

```yaml
jobs:
  long-running-job:
    runs-on: ubuntu-latest
    timeout-minutes: 60
    steps:
      - name: Long task
        timeout-minutes: 30
        run: npm run long-task
```


### Example 16

```yaml
- name: Debug information
  run: |
    echo "Event name: ${{ github.event_name }}"
    echo "Ref: ${{ github.ref }}"
    echo "SHA: ${{ github.sha }}"
    echo "Actor: ${{ github.actor }}"
```


### Example 17

```yaml
- name: Build Docker image
  run: |
    docker build -t myapp:${{ github.sha }} .
    docker tag myapp:${{ github.sha }} myapp:latest

- name: Push to registry
  run: |
    echo ${{ secrets.DOCKER_PASSWORD }} | docker login -u ${{ secrets.DOCKER_USERNAME }} --password-stdin
    docker push myapp:${{ github.sha }}
    docker push myapp:latest
```


### Example 18

```yaml
- name: Deploy to AWS
  uses: aws-actions/configure-aws-credentials@v2
  with:
    aws-access-key-id: ${{ secrets.AWS_ACCESS_KEY_ID }}
    aws-secret-access-key: ${{ secrets.AWS_SECRET_ACCESS_KEY }}
    aws-region: us-east-1

- name: Deploy to S3
  run: |
    aws s3 sync dist/ s3://my-bucket --delete
    aws cloudfront create-invalidation --distribution-id ${{ secrets.CLOUDFRONT_ID }} --paths "/*"
```


---

## 🚀 Usage

**Reference this template:** `@command-cicd-pipeline-manager.md`


**Platform-specific:**
- **GitHub Copilot**: Add to `.github/copilot-instructions.md`
- **Augment Code**: Use `aug context add` command
- **Cursor/Windsurf**: Reference in chat with `@filename`
- **Claude**: Add to Project Knowledge
- **ChatGPT**: Add to Custom GPT configuration
