---
id: skill-autogpt-agents
type: skill
name: autogpt-agents
description: Autonomous AI agent platform for building and deploying continuous agents.
  Use when creating visual workflow agents, deploying persistent autonomous agents,
  or building complex multi-step AI automation systems.
category: ai-research
complexity: medium
keywords:
- api
- benchmark
- database
- deploy
- deployment
- docker
- git
- github
- javascript
- performance
- python
- react
- rest
- test
- testing
capabilities: []
token_estimate: 1458
has_references: true
reference_count: 2
---

<!-- Converted from Claude Skill Template -->
<!-- Complexity: Medium -->
<!-- Estimated Tokens: ~1,458 -->
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




# autogpt-agents

> Autonomous AI agent platform for building and deploying continuous agents. Use when creating visual workflow agents, deploying persistent autonomous agents, or building complex multi-step AI automation systems.

# AutoGPT - Autonomous AI Agent Platform

Comprehensive platform for building, deploying, and managing continuous AI agents through a visual interface or development toolkit.

## When to use AutoGPT

**Use AutoGPT when:**
- Building autonomous agents that run continuously
- Creating visual workflow-based AI agents
- Deploying agents with external triggers (webhooks, schedules)
- Building complex multi-step automation pipelines
- Need a no-code/low-code agent builder

**Key features:**
- **Visual Agent Builder**: Drag-and-drop node-based workflow editor
- **Continuous Execution**: Agents run persistently with triggers
- **Marketplace**: Pre-built agents and blocks to share/reuse
- **Block System**: Modular components for LLM, tools, integrations
- **Forge Toolkit**: Developer tools for custom agent creation
- **Benchmark System**: Standardized agent performance testing

**Use alternatives instead:**
- **LangChain/LlamaIndex**: If you need more control over agent logic
- **CrewAI**: For role-based multi-agent collaboration
- **OpenAI Assistants**: For simple hosted agent deployments
- **Semantic Kernel**: For Microsoft ecosystem integration

## Quick start

### Installation (Docker)

```bash
# Clone repository
git clone https://github.com/Significant-Gravitas/AutoGPT.git
cd AutoGPT/autogpt_platform

# Copy environment file
cp .env.example .env

# Start backend services
docker compose up -d --build

# Start frontend (in separate terminal)
cd frontend
cp .env.example .env
npm install
npm run dev
```

### Access the platform

- **Frontend UI**: http://localhost:3000
- **Backend API**: http://localhost:8006/api
- **WebSocket**: ws://localhost:8001/ws

## Architecture overview

AutoGPT has two main systems:

### AutoGPT Platform (Production)
- Visual agent builder with React frontend
- FastAPI backend with execution engine
- PostgreSQL + Redis + RabbitMQ infrastructure

### AutoGPT Classic (Development)
- **Forge**: Agent development toolkit
- **Benchmark**: Performance testing framework
- **CLI**: Command-line interface for development

## Core concepts

### Graphs and nodes

Agents are represented as **graphs** containing **nodes** connected by **links**:

```
Graph (Agent)
  ├── Node (Input)
  │   └── Block (AgentInputBlock)
  ├── Node (Process)
  │   └── Block (LLMBlock)
  ├── Node (Decision)
  │   └── Block (SmartDecisionMaker)
  └── Node (Output)
      └── Block (AgentOutputBlock)
```

### Blocks

Blocks are reusable functional components:

| Block Type | Purpose |
|------------|---------|
| `INPUT` | Agent entry points |
| `OUTPUT` | Agent outputs |
| `AI` | LLM calls, text generation |
| `WEBHOOK` | External triggers |
| `STANDARD` | General operations |
| `AGENT` | Nested agent execution |

### Execution flow

```
User/Trigger → Graph Execution → Node Execution → Block.execute()
     ↓              ↓                 ↓
  Inputs      Queue System      Output Yields
```

## Building agents

### Using the visual builder

1. **Open Agent Builder** at http://localhost:3000
2. **Add blocks** from the BlocksControl panel
3. **Connect nodes** by dragging between handles
4. **Configure inputs** in each node
5. **Run agent** using PrimaryActionBar

### Available blocks

**AI Blocks:**
- `AITextGeneratorBlock` - Generate text with LLMs
- `AIConversationBlock` - Multi-turn conversations
- `SmartDecisionMakerBlock` - Conditional logic

**Integration Blocks:**
- GitHub, Google, Discord, Notion connectors
- Webhook triggers and handlers
- HTTP request blocks

**Control Blocks:**
- Input/Output blocks
- Branching and decision nodes
- Loop and iteration blocks

## Agent execution

### Trigger types

**Manual execution:**
```http
POST /api/v1/graphs/{graph_id}/execute
Content-Type: application/json

{
  "inputs": {
    "input_name": "value"
  }
}
```

**Webhook trigger:**
```http
POST /api/v1/webhooks/{webhook_id}
Content-Type: application/json

{
  "data": "webhook payload"
}
```

**Scheduled execution:**
```json
{
  "schedule": "0 */2 * * *",
  "graph_id": "graph-uuid",
  "inputs": {}
}
```

### Monitoring execution

**WebSocket updates:**
```javascript
const ws = new WebSocket('ws://localhost:8001/ws');

ws.onmessage = (event) => {
  const update = JSON.parse(event.data);
  console.log(`Node ${update.node_id}: ${update.status}`);
};
```

**REST API polling:**
```http
GET /api/v1/executions/{execution_id}
```

## Using Forge (Development)

### Create custom agent

```bash
# Setup forge environment
cd classic
./run setup

# Create new agent from template
./run forge create my-agent

# Start agent server
./run forge start my-agent
```

### Agent structure

```
my-agent/
├── agent.py          # Main agent logic
├── abilities/        # Custom abilities
│   ├── __init__.py
│   └── custom.py
├── prompts/          # Prompt templates
└── config.yaml       # Agent configuration
```

### Implement custom ability

```python
from forge import Ability, ability

@ability(
    name="custom_search",
    description="Search for information",
    parameters={
        "query": {"type": "string", "description": "Search query"}
    }
)
def custom_search(query: str) -> str:
    """Custom search ability."""
    # Implement search logic
    result = perform_search(query)
    return result
```

## Benchmarking agents

### Run benchmarks

```bash
# Run all benchmarks
./run benchmark

# Run specific category
./run benchmark --category coding

# Run with specific agent
./run benchmark --agent my-agent
```

### Benchmark categories

- **Coding**: Code generation and debugging
- **Retrieval**: Information finding
- **Web**: Web browsing and interaction
- **Writing**: Text generation tasks

### VCR cassettes

Benchmarks use recorded HTTP responses for reproducibility:

```bash
# Record new cassettes
./run benchmark --record

# Run with existing cassettes
./run benchmark --playback
```

## Integrations

### Adding credentials

1. Navigate to Profile > Integrations
2. Select provider (OpenAI, GitHub, Google, etc.)
3. Enter API keys or authorize OAuth
4. Credentials are encrypted and stored securely

### Using credentials in blocks

Blocks automatically access user credentials:

```python
class MyLLMBlock(Block):
    def execute(self, inputs):
        # Credentials are injected by the system
        credentials = self.get_credentials("openai")
        client = OpenAI(api_key=credentials.api_key)
        # ...
```

### Supported providers

| Provider | Auth Type | Use Cases |
|----------|-----------|-----------|
| OpenAI | API Key | LLM, embeddings |
| Anthropic | API Key | Claude models |
| GitHub | OAuth | Code, repos |
| Google | OAuth | Drive, Gmail, Calendar |
| Discord | Bot Token | Messaging |
| Notion | OAuth | Documents |

## Deployment

### Docker production setup

```yaml
# docker-compose.prod.yml
services:
  rest_server:
    image: autogpt/platform-backend
    environment:
      - DATABASE_URL=postgresql://...
      - REDIS_URL=redis://redis:6379
    ports:
      - "8006:8006"

  executor:
    image: autogpt/platform-backend
    command: poetry run executor

  frontend:
    image: autogpt/platform-frontend
    ports:
      - "3000:3000"
```

### Environment variables

| Variable | Purpose |
|----------|---------|
| `DATABASE_URL` | PostgreSQL connection |
| `REDIS_URL` | Redis connection |
| `RABBITMQ_URL` | RabbitMQ connection |
| `ENCRYPTION_KEY` | Credential encryption |
| `SUPABASE_URL` | Authentication |

### Generate encryption key

```bash
cd autogpt_platform/backend
poetry run cli gen-encrypt-key
```

## Best practices

1. **Start simple**: Begin with 3-5 node agents
2. **Test incrementally**: Run and test after each change
3. **Use webhooks**: External triggers for event-driven agents
4. **Monitor costs**: Track LLM API usage via credits system
5. **Version agents**: Save working versions before changes
6. **Benchmark**: Use agbenchmark to validate agent quality

## Common issues

**Services not starting:**
```bash
# Check container status
docker compose ps

# View logs
docker compose logs rest_server

# Restart services
docker compose restart
```

**Database connection issues:**
```bash
# Run migrations
cd backend
poetry run prisma migrate deploy
```

**Agent execution stuck:**
```bash
# Check RabbitMQ queue
# Visit http://localhost:15672 (guest/guest)

# Clear stuck executions
docker compose restart executor
```

## References

- **[Advanced Usage](references/advanced-usage.md)** - Custom blocks, deployment, scaling
- **[Troubleshooting](references/troubleshooting.md)** - Common issues, debugging

## Resources

- **Documentation**: https://docs.agpt.co
- **Repository**: https://github.com/Significant-Gravitas/AutoGPT
- **Discord**: https://discord.gg/autogpt
- **License**: MIT (Classic) / Polyform Shield (Platform)


---


## 📚 Reference Materials


### Advanced Usage

# AutoGPT Advanced Usage Guide

## Custom Block Development

### Block structure

```python
from backend.data.block import Block, BlockSchema, BlockType
from pydantic import BaseModel

class MyBlockInput(BaseModel):
    """Input schema for the block."""
    query: str
    max_results: int = 10

class MyBlockOutput(BaseModel):
    """Output schema for the block."""
    results: list[str]
    count: int

class MyCustomBlock(Block):
    """Custom block for specific functionality."""

    id = "my-custom-block-uuid"
    name = "My Custom Block"
    description = "Does something specific"
    block_type = BlockType.STANDARD

    input_schema = MyBlockInput
    output_schema = MyBlockOutput

    async def execute(self, input_data: MyBlockInput) -> dict:
        """Execute the block logic."""
        # Implement your logic
        results = await self.process(input_data.query, input_data.max_results)

        yield "results", results
        yield "count", len(results)

    async def process(self, query: str, max_results: int) -> list[str]:
        """Internal processing logic."""
        # Implementation
        return ["result1", "result2"]
```

### Block registration

```python
# backend/blocks/__init__.py
from backend.blocks.my_block import MyCustomBlock

# Add to block registry
BLOCKS = [
    MyCustomBlock,
    # ... other blocks
]
```

### Block with credentials

```python
from backend.data.block import Block
from backend.integrations.providers import ProviderName

class APIIntegrationBlock(Block):
    """Block that uses external API credentials."""

    credentials_required = [ProviderName.OPENAI]

    async def execute(self, input_data):
        # Get credentials from the system
        credentials = await self.get_credentials(ProviderName.OPENAI)

        # Use credentials
        client = OpenAI(api_key=credentials.api_key)

        response = await client.chat.completions.create(
            model="gpt-4o",
            messages=[{"role": "user", "content": input_data.prompt}]
        )

        yield "response", response.choices[0].message.content
```

### Block with cost tracking

```python
from backend.data.block import Block
from backend.data.block_cost_config import BlockCostConfig

class LLMBlock(Block):
    """Block with cost tracking."""

    cost_config = BlockCostConfig(
        cost_type="token",
        cost_per_unit=0.00002,  # Per token
        provider="openai"
    )

    async def execute(self, input_data):
        response = await self.call_llm(input_data.prompt)

        # Report token usage for cost tracking
        self.report_usage(
            input_tokens=response.usage.prompt_tokens,
            output_tokens=response.usage.completion_tokens
        )

        yield "output", response.content
```

## Advanced Execution Patterns

### Parallel node execution

```python
from backend.executor.manager import ExecutionManager

async def execute_parallel_nodes(graph_exec_id: str, node_ids: list[str]):
    """Execute multiple nodes in parallel."""
    manager = ExecutionManager()

    tasks = [
        manager.execute_node(graph_exec_id, node_id)
        for node_id in node_ids
    ]

    results = await asyncio.gather(*tasks)
    return results
```

### Conditional branching

```python
from backend.blocks.branching import BranchingBlock

class SmartBranchBlock(BranchingBlock):
    """Advanced conditional branching."""

    async def execute(self, input_data):
        condition = await self.evaluate_condition(input_data)

        if condition == "path_a":
            yield "output_a", input_data.value
        elif condition == "path_b":
            yield "output_b", input_data.value
        else:
            yield "output_default", input_data.value
```

### Loop execution

```python
class LoopBlock(Block):
    """Execute a subgraph in a loop."""

    async def execute(self, input_data):
        items = input_data.items
        results = []

        for i, item in enumerate(items):
            # Execute nested graph for each item
            result = await self.execute_subgraph(
                graph_id=input_data.subgraph_id,
                inputs={"item": item, "index": i}
            )
            results.append(result)

            yield "progress", f"Processed {i+1}/{len(items)}"

        yield "results", results
```

## Graph composition

### Nested agents

```python
from backend.blocks.agent import AgentExecutorBlock

class ParentAgentBlock(Block):
    """Execute child agents within a parent agent."""

    async def execute(self, input_data):
        # Execute child agent
        child_result = await self.execute_agent(
            agent_id=input_data.child_agent_id,
            inputs={"query": input_data.query}
        )

        # Process child result
        processed = await self.process_result(child_result)

        yield "output", processed
```

### Dynamic graph construction

```python
from backend.data.graph import GraphModel, NodeModel, LinkModel

async def create_dynamic_graph(user_id: str, template: str):
    """Create a graph dynamically based on template."""
    graph = GraphModel(
        name=f"Dynamic Graph - {template}",
        description="Auto-generated graph",
        user_id=user_id
    )

    # Add nodes based on template
    nodes = []
    if template == "research":
        nodes = [
            NodeModel(block_id="search-block", position={"x": 0, "y": 0}),
            NodeModel(block_id="summarize-block", position={"x": 200, "y": 0}),
            NodeModel(block_id="output-block", position={"x": 400, "y": 0})
        ]
    elif template == "code-review":
        nodes = [
            NodeModel(block_id="github-block", position={"x": 0, "y": 0}),
            NodeModel(block_id="review-block", position={"x": 200, "y": 0}),
            NodeModel(block_id="comment-block", position={"x": 400, "y": 0})
        ]

    graph.nodes = nodes

    # Create links between nodes
    for i in range(len(nodes) - 1):
        graph.links.append(LinkModel(
            source_id=nodes[i].id,
            sink_id=nodes[i+1].id,
            source_name="output",
            sink_name="input"
        ))

    return await graph.save()
```

## Production deployment

### Kubernetes deployment

```yaml
# autogpt-deployment.yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: autogpt-backend
spec:
  replicas: 3
  selector:
    matchLabels:
      app: autogpt-backend
  template:
    metadata:
      labels:
        app: autogpt-backend
    spec:
      containers:
      - name: rest-server
        image: autogpt/platform-backend:latest
        command: ["poetry", "run", "rest"]
        ports:
        - containerPort: 8006
        env:
        - name: DATABASE_URL
          valueFrom:
            secretKeyRef:
              name: autogpt-secrets
              key: database-url
        resources:
          requests:
            memory: "512Mi"
            cpu: "500m"
          limits:
            memory: "2Gi"
            cpu: "2000m"
---
apiVersion: apps/v1
kind: Deployment
metadata:
  name: autogpt-executor
spec:
  replicas: 5
  selector:
    matchLabels:
      app: autogpt-executor
  template:
    spec:
      containers:
      - name: executor
        image: autogpt/platform-backend:latest
        command: ["poetry", "run", "executor"]
        resources:
          requests:
            memory: "1Gi"
            cpu: "1000m"
          limits:
            memory: "4Gi"
            cpu: "4000m"
```

### Horizontal scaling

```yaml
# autogpt-hpa.yaml
apiVersion: autoscaling/v2
kind: HorizontalPodAutoscaler
metadata:
  name: autogpt-executor-hpa
spec:
  scaleTargetRef:
    apiVersion: apps/v1
    kind: Deployment
    name: autogpt-executor
  minReplicas: 2
  maxReplicas: 20
  metrics:
  - type: Resource
    resource:
      name: cpu
      target:
        type: Utilization
        averageUtilization: 70
  - type: External
    external:
      metric:
        name: rabbitmq_queue_messages
        selector:
          matchLabels:
            queue: graph-execution
      target:
        type: AverageValue
        averageValue: 10
```

### Database optimization

```sql
-- Optimize for high-volume execution tracking
CREATE INDEX CONCURRENTLY idx_node_exec_graph_status
ON "AgentNodeExecution" ("graphExecutionId", "executionStatus");

CREATE INDEX CONCURRENTLY idx_graph_exec_user_status
ON "AgentGraphExecution" ("userId", "executionStatus", "createdAt" DESC);

-- Partition execution tables by date
CREATE TABLE "AgentGraphExecution_partitioned" (
    LIKE "AgentGraphExecution" INCLUDING ALL
) PARTITION BY RANGE ("createdAt");

-- Create monthly partitions
CREATE TABLE "AgentGraphExecution_2024_01"
PARTITION OF "AgentGraphExecution_partitioned"
FOR VALUES FROM ('2024-01-01') TO ('2024-02-01');
```

## Monitoring and observability

### Prometheus metrics

```python
from prometheus_client import Counter, Histogram, Gauge

# Define metrics
EXECUTIONS_TOTAL = Counter(
    'autogpt_executions_total',
    'Total graph executions',
    ['graph_id', 'status']
)

EXECUTION_DURATION = Histogram(
    'autogpt_execution_duration_seconds',
    'Execution duration in seconds',
    ['graph_id'],
    buckets=[0.1, 0.5, 1, 5, 10, 30, 60, 120]
)

ACTIVE_EXECUTIONS = Gauge(
    'autogpt_active_executions',
    'Currently running executions'
)

# Use in executor
class ExecutionManager:
    async def execute_graph(self, graph_id, inputs):
        ACTIVE_EXECUTIONS.inc()
        start_time = time.time()

        try:
            result = await self._execute(graph_id, inputs)
            EXECUTIONS_TOTAL.labels(graph_id=graph_id, status='success').inc()
            return result
        except Exception as e:
            EXECUTIONS_TOTAL.labels(graph_id=graph_id, status='failed').inc()
            raise
        finally:
            ACTIVE_EXECUTIONS.dec()
            EXECUTION_DURATION.labels(graph_id=graph_id).observe(
                time.time() - start_time
            )
```

### Grafana dashboard

```json
{
  "dashboard": {
    "title": "AutoGPT Platform",
    "panels": [
      {
        "title": "Executions per Minute",
        "type": "graph",
        "targets": [
          {
            "expr": "rate(autogpt_executions_total[1m])",
            "legendFormat": "{{status}}"
          }
        ]
      },
      {
        "title": "Execution Latency (p95)",
        "type": "gauge",
        "targets": [
          {
            "expr": "histogram_quantile(0.95, rate(autogpt_execution_duration_seconds_bucket[5m]))"
          }
        ]
      },
      {
        "title": "Active Executions",
        "type": "stat",
        "targets": [
          {"expr": "autogpt_active_executions"}
        ]
      }
    ]
  }
}
```

### Sentry error tracking

```python
import sentry_sdk
from sentry_sdk.integrations.fastapi import FastApiIntegration
from sentry_sdk.integrations.asyncio import AsyncioIntegration

sentry_sdk.init(
    dsn=os.environ.get("SENTRY_DSN"),
    integrations=[
        FastApiIntegration(),
        AsyncioIntegration(),
    ],
    traces_sample_rate=0.1,
    profiles_sample_rate=0.1,
    environment=os.environ.get("APP_ENV", "development")
)

# Custom error context
with sentry_sdk.push_scope() as scope:
    scope.set_tag("graph_id", graph_id)
    scope.set_extra("inputs", sanitized_inputs)
    sentry_sdk.capture_exception(error)
```

## API integration patterns

### Webhook handling

```python
from fastapi import APIRouter, Request
from backend.data.webhook import WebhookHandler

router = APIRouter()

@router.post("/webhooks/{webhook_id}")
async def handle_webhook(webhook_id: str, request: Request):
    """Handle incoming webhook."""
    handler = WebhookHandler()

    # Verify webhook signature
    signature = request.headers.get("X-Webhook-Signature")
    if not await handler.verify_signature(webhook_id, signature, await request.body()):
        return {"error": "Invalid signature"}, 401

    # Parse payload
    payload = await request.json()

    # Trigger associated graph
    execution = await handler.trigger_graph(webhook_id, payload)

    return {
        "execution_id": execution.id,
        "status": "queued"
    }
```

### External API rate limiting

```python
from asyncio import Semaphore
from functools import wraps

class RateLimiter:
    """Rate limiter for external API calls."""

    def __init__(self, max_concurrent: int = 10, rate_per_second: float = 5):
        self.semaphore = Semaphore(max_concurrent)
        self.rate = rate_per_second
        self.last_call = 0

    async def acquire(self):
        await self.semaphore.acquire()
        now = time.time()
        wait_time = max(0, (1 / self.rate) - (now - self.last_call))
        if wait_time > 0:
            await asyncio.sleep(wait_time)
        self.last_call = time.time()

    def release(self):
        self.semaphore.release()

# Usage in block
class RateLimitedAPIBlock(Block):
    rate_limiter = RateLimiter(max_concurrent=5, rate_per_second=2)

    async def execute(self, input_data):
        await self.rate_limiter.acquire()
        try:
            result = await self.call_api(input_data)
            yield "output", result
        finally:
            self.rate_limiter.release()
```




### Troubleshooting

# AutoGPT Troubleshooting Guide

## Installation Issues

### Docker compose fails

**Error**: `Cannot connect to the Docker daemon`

**Fix**:
```bash
# Start Docker daemon
sudo systemctl start docker

# Or on macOS
open -a Docker

# Verify Docker is running
docker ps
```

**Error**: `Port already in use`

**Fix**:
```bash
# Find process using port
lsof -i :8006

# Kill process
kill -9 <PID>

# Or change port in docker-compose.yml
```

### Database migration fails

**Error**: `Migration failed: relation already exists`

**Fix**:
```bash
# Reset database
docker compose down -v
docker compose up -d db

# Re-run migrations
cd backend
poetry run prisma migrate reset --force
poetry run prisma migrate deploy
```

**Error**: `Connection refused to database`

**Fix**:
```bash
# Check database is running
docker compose ps db

# Check database logs
docker compose logs db

# Verify DATABASE_URL in .env
echo $DATABASE_URL
```

### Frontend build fails

**Error**: `Module not found: Can't resolve '@/components/...'`

**Fix**:
```bash
# Clear node modules and reinstall
rm -rf node_modules
rm -rf .next
npm install

# Or with pnpm
pnpm install --force
```

**Error**: `Supabase client not initialized`

**Fix**:
```bash
# Verify environment variables
cat .env | grep SUPABASE

# Required variables:
# NEXT_PUBLIC_SUPABASE_URL=http://localhost:8000
# NEXT_PUBLIC_SUPABASE_ANON_KEY=your-key
```

## Service Issues

### Backend services not starting

**Error**: `rest_server exited with code 1`

**Diagnose**:
```bash
# Check logs
docker compose logs rest_server

# Common issues:
# - Missing environment variables
# - Database connection failed
# - Redis connection failed
```

**Fix**:
```bash
# Verify all dependencies are running
docker compose ps

# Restart services in order
docker compose restart db redis rabbitmq
sleep 10
docker compose restart rest_server executor
```

### Executor not processing tasks

**Error**: Tasks stuck in QUEUED status

**Diagnose**:
```bash
# Check executor logs
docker compose logs executor

# Check RabbitMQ queue
# Visit http://localhost:15672 (guest/guest)
# Look at queue depths
```

**Fix**:
```bash
# Restart executor
docker compose restart executor

# If queue is backlogged, scale executors
docker compose up -d --scale executor=3
```

### WebSocket connection fails

**Error**: `WebSocket connection to 'ws://localhost:8001/ws' failed`

**Fix**:
```bash
# Check WebSocket server is running
docker compose logs websocket_server

# Verify port is accessible
nc -zv localhost 8001

# Check firewall rules
sudo ufw allow 8001
```

## Agent Execution Issues

### Agent stuck in running state

**Diagnose**:
```bash
# Check execution status via API
curl http://localhost:8006/api/v1/executions/{execution_id}

# Check node execution logs
docker compose logs executor | grep {execution_id}
```

**Fix**:
```python
# Cancel stuck execution via API
import requests

response = requests.post(
    f"http://localhost:8006/api/v1/executions/{execution_id}/cancel",
    headers={"Authorization": f"Bearer {token}"}
)
```

### LLM block timeout

**Error**: `TimeoutError: LLM call exceeded timeout`

**Fix**:
```python
# Increase timeout in block configuration
{
    "block_id": "llm-block",
    "config": {
        "timeout_seconds": 120,  # Increase from default 60
        "max_retries": 3
    }
}
```

### Credential errors

**Error**: `CredentialsNotFoundError: No credentials for provider openai`

**Fix**:
1. Navigate to Profile > Integrations
2. Add OpenAI API key
3. Ensure graph has credential mapping

```json
{
    "credential_mapping": {
        "openai": "user_credential_id"
    }
}
```

### Memory issues during execution

**Error**: `MemoryError` or container killed (OOMKilled)

**Fix**:
```yaml
# Increase memory limits in docker-compose.yml
executor:
    deploy:
        resources:
            limits:
                memory: 4G
            reservations:
                memory: 2G
```

## Graph/Block Issues

### Block not appearing in UI

**Diagnose**:
```python
# Check block registration
from backend.data.block import get_all_blocks

blocks = get_all_blocks()
print([b.name for b in blocks])
```

**Fix**:
```python
# Ensure block is imported in __init__.py
# backend/blocks/__init__.py
from backend.blocks.my_block import MyBlock

BLOCKS = [
    MyBlock,
    # ...
]
```

### Graph save fails

**Error**: `GraphValidationError: Invalid link configuration`

**Diagnose**:
```python
# Validate graph structure
from backend.data.graph import validate_graph

errors = validate_graph(graph_data)
print(errors)
```

**Fix**:
- Ensure all links connect valid nodes
- Check input/output name matches
- Verify required inputs are connected

### Circular dependency detected

**Error**: `GraphValidationError: Circular dependency in graph`

**Fix**:
```python
# Find cycle
import networkx as nx

G = nx.DiGraph()
for link in graph.links:
    G.add_edge(link.source_id, link.sink_id)

cycles = list(nx.simple_cycles(G))
print(f"Cycles found: {cycles}")
```

## Performance Issues

### Slow graph execution

**Diagnose**:
```python
# Profile execution
import cProfile

profiler = cProfile.Profile()
profiler.enable()
await executor.execute_graph(graph_id, inputs)
profiler.disable()
profiler.print_stats(sort='cumulative')
```

**Fix**:
- Parallelize independent nodes
- Reduce unnecessary API calls
- Cache repeated computations

### High database query latency

**Diagnose**:
```bash
# Enable query logging in PostgreSQL
docker exec -it autogpt-db psql -U postgres
\x
SHOW log_min_duration_statement;
SET log_min_duration_statement = 100;  -- Log queries > 100ms
```

**Fix**:
```sql
-- Add missing indexes
CREATE INDEX CONCURRENTLY idx_executions_user_created
ON "AgentGraphExecution" ("userId", "createdAt" DESC);

ANALYZE "AgentGraphExecution";
```

### Redis memory growing

**Diagnose**:
```bash
# Check Redis memory usage
docker exec -it autogpt-redis redis-cli INFO memory

# Check key count
docker exec -it autogpt-redis redis-cli DBSIZE
```

**Fix**:
```bash
# Clear expired keys
docker exec -it autogpt-redis redis-cli --scan --pattern "exec:*" | head -1000 | xargs docker exec -i autogpt-redis redis-cli DEL

# Set memory policy
docker exec -it autogpt-redis redis-cli CONFIG SET maxmemory-policy volatile-lru
```

## Debugging Tips

### Enable debug logging

```bash
# Set in .env
LOG_LEVEL=DEBUG

# Or for specific module
LOG_LEVEL_EXECUTOR=DEBUG
LOG_LEVEL_BLOCKS=DEBUG
```

### Trace execution flow

```python
import logging

logging.basicConfig(level=logging.DEBUG)
logger = logging.getLogger("backend.executor")

# Add to executor
logger.debug(f"Executing node {node_id} with inputs: {inputs}")
```

### Test block in isolation

```python
import asyncio
from backend.blocks.my_block import MyBlock

async def test_block():
    block = MyBlock()
    inputs = {"query": "test"}

    async for output_name, value in block.execute(inputs):
        print(f"{output_name}: {value}")

asyncio.run(test_block())
```

### Inspect message queues

```bash
# RabbitMQ management UI
# http://localhost:15672 (guest/guest)

# List queues via CLI
docker exec autogpt-rabbitmq rabbitmqctl list_queues name messages consumers

# Purge a queue
docker exec autogpt-rabbitmq rabbitmqctl purge_queue graph-execution
```

## Getting Help

1. **Documentation**: https://docs.agpt.co
2. **GitHub Issues**: https://github.com/Significant-Gravitas/AutoGPT/issues
3. **Discord**: https://discord.gg/autogpt

### Reporting Issues

Include:
- AutoGPT version: `git describe --tags`
- Docker version: `docker --version`
- Error logs: `docker compose logs > logs.txt`
- Steps to reproduce
- Graph configuration (sanitized)
- Environment: OS, hardware specs




---

## 🚀 Usage

**Reference this template:** `@skill-autogpt-agents.md`


**Platform-specific:**
- **GitHub Copilot**: Add to `.github/copilot-instructions.md`
- **Augment Code**: Use `aug context add` command
- **Cursor/Windsurf**: Reference in chat with `@filename`
- **Claude**: Add to Project Knowledge
- **ChatGPT**: Add to Custom GPT configuration
