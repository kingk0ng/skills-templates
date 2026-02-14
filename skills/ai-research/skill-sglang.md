---
id: skill-sglang
type: skill
name: sglang
description: Fast structured generation and serving for LLMs with RadixAttention prefix
  caching. Use for JSON/regex outputs, constrained decoding, agentic workflows with
  tool calls, or when you need 5× faster inference than vLLM with prefix sharing.
  Powers 300,000+ GPUs at xAI, AMD, NVIDIA, and LinkedIn.
category: ai-research
complexity: medium
keywords:
- api
- deployment
- git
- github
- optimization
- performance
- python
capabilities: []
token_estimate: 1660
has_references: true
reference_count: 3
---

<!-- Converted from Claude Skill Template -->
<!-- Complexity: Medium -->
<!-- Estimated Tokens: ~1,660 -->
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




# sglang

> Fast structured generation and serving for LLMs with RadixAttention prefix caching. Use for JSON/regex outputs, constrained decoding, agentic workflows with tool calls, or when you need 5× faster inference than vLLM with prefix sharing. Powers 300,000+ GPUs at xAI, AMD, NVIDIA, and LinkedIn.

# SGLang

High-performance serving framework for LLMs and VLMs with RadixAttention for automatic prefix caching.

## When to use SGLang

**Use SGLang when:**
- Need structured outputs (JSON, regex, grammar)
- Building agents with repeated prefixes (system prompts, tools)
- Agentic workflows with function calling
- Multi-turn conversations with shared context
- Need faster JSON decoding (3× vs standard)

**Use vLLM instead when:**
- Simple text generation without structure
- Don't need prefix caching
- Want mature, widely-tested production system

**Use TensorRT-LLM instead when:**
- Maximum single-request latency (no batching needed)
- NVIDIA-only deployment
- Need FP8/INT4 quantization on H100

## Quick start

### Installation

```bash
# pip install (recommended)
pip install "sglang[all]"

# With FlashInfer (faster, CUDA 11.8/12.1)
pip install sglang[all] flashinfer -i https://flashinfer.ai/whl/cu121/torch2.4/

# From source
git clone https://github.com/sgl-project/sglang.git
cd sglang
pip install -e "python[all]"
```

### Launch server

```bash
# Basic server (Llama 3-8B)
python -m sglang.launch_server \
    --model-path meta-llama/Meta-Llama-3-8B-Instruct \
    --port 30000

# With RadixAttention (automatic prefix caching)
python -m sglang.launch_server \
    --model-path meta-llama/Meta-Llama-3-8B-Instruct \
    --port 30000 \
    --enable-radix-cache  # Default: enabled

# Multi-GPU (tensor parallelism)
python -m sglang.launch_server \
    --model-path meta-llama/Meta-Llama-3-70B-Instruct \
    --tp 4 \
    --port 30000
```

### Basic inference

```python
import sglang as sgl

# Set backend
sgl.set_default_backend(sgl.OpenAI("http://localhost:30000/v1"))

# Simple generation
@sgl.function
def simple_gen(s, question):
    s += "Q: " + question + "\n"
    s += "A:" + sgl.gen("answer", max_tokens=100)

# Run
state = simple_gen.run(question="What is the capital of France?")
print(state["answer"])
# Output: "The capital of France is Paris."
```

### Structured JSON output

```python
import sglang as sgl

@sgl.function
def extract_person(s, text):
    s += f"Extract person information from: {text}\n"
    s += "Output JSON:\n"

    # Constrained JSON generation
    s += sgl.gen(
        "json_output",
        max_tokens=200,
        regex=r'\{"name": "[^"]+", "age": \d+, "occupation": "[^"]+"\}'
    )

# Run
state = extract_person.run(
    text="John Smith is a 35-year-old software engineer."
)
print(state["json_output"])
# Output: {"name": "John Smith", "age": 35, "occupation": "software engineer"}
```

## RadixAttention (Key Innovation)

**What it does**: Automatically caches and reuses common prefixes across requests.

**Performance**:
- **5× faster** for agentic workloads with shared system prompts
- **10× faster** for few-shot prompting with repeated examples
- **Zero configuration** - works automatically

**How it works**:
1. Builds radix tree of all processed tokens
2. Automatically detects shared prefixes
3. Reuses KV cache for matching prefixes
4. Only computes new tokens

**Example** (Agent with system prompt):

```
Request 1: [SYSTEM_PROMPT] + "What's the weather?"
→ Computes full prompt (1000 tokens)

Request 2: [SAME_SYSTEM_PROMPT] + "Book a flight"
→ Reuses system prompt KV cache (998 tokens)
→ Only computes 2 new tokens
→ 5× faster!
```

## Structured generation patterns

### JSON with schema

```python
@sgl.function
def structured_extraction(s, article):
    s += f"Article: {article}\n\n"
    s += "Extract key information as JSON:\n"

    # JSON schema constraint
    schema = {
        "type": "object",
        "properties": {
            "title": {"type": "string"},
            "author": {"type": "string"},
            "summary": {"type": "string"},
            "sentiment": {"type": "string", "enum": ["positive", "negative", "neutral"]}
        },
        "required": ["title", "author", "summary", "sentiment"]
    }

    s += sgl.gen("info", max_tokens=300, json_schema=schema)

state = structured_extraction.run(article="...")
print(state["info"])
# Output: Valid JSON matching schema
```

### Regex-constrained generation

```python
@sgl.function
def extract_email(s, text):
    s += f"Extract email from: {text}\n"
    s += "Email: "

    # Email regex pattern
    s += sgl.gen(
        "email",
        max_tokens=50,
        regex=r'[a-zA-Z0-9._%+-]+@[a-zA-Z0-9.-]+\.[a-zA-Z]{2,}'
    )

state = extract_email.run(text="Contact john.doe@example.com for details")
print(state["email"])
# Output: "john.doe@example.com"
```

### Grammar-based generation

```python
@sgl.function
def generate_code(s, description):
    s += f"Generate Python code for: {description}\n"
    s += "```python\n"

    # EBNF grammar for Python
    python_grammar = """
    ?start: function_def
    function_def: "def" NAME "(" [parameters] "):" suite
    parameters: parameter ("," parameter)*
    parameter: NAME
    suite: simple_stmt | NEWLINE INDENT stmt+ DEDENT
    """

    s += sgl.gen("code", max_tokens=200, grammar=python_grammar)
    s += "\n```"
```

## Agent workflows with function calling

```python
import sglang as sgl

# Define tools
tools = [
    {
        "name": "get_weather",
        "description": "Get weather for a location",
        "parameters": {
            "type": "object",
            "properties": {
                "location": {"type": "string"}
            }
        }
    },
    {
        "name": "book_flight",
        "description": "Book a flight",
        "parameters": {
            "type": "object",
            "properties": {
                "from": {"type": "string"},
                "to": {"type": "string"},
                "date": {"type": "string"}
            }
        }
    }
]

@sgl.function
def agent_workflow(s, user_query, tools):
    # System prompt (cached with RadixAttention)
    s += "You are a helpful assistant with access to tools.\n"
    s += f"Available capabilities: File operations, code editing, terminal access, search{tools}\n\n"

    # User query
    s += f"User: {user_query}\n"
    s += "Assistant: "

    # Generate with function calling
    s += sgl.gen(
        "response",
        max_tokens=200,
        tools=tools,  # SGLang handles tool call format
        stop=["User:", "\n\n"]
    )

# Multiple queries reuse system prompt
state1 = agent_workflow.run(
    user_query="What's the weather in NYC?",
    tools=tools
)
# First call: Computes full system prompt

state2 = agent_workflow.run(
    user_query="Book a flight to LA",
    tools=tools
)
# Second call: Reuses system prompt (5× faster)
```

## Performance benchmarks

### RadixAttention speedup

**Few-shot prompting** (10 examples in prompt):
- vLLM: 2.5 sec/request
- SGLang: **0.25 sec/request** (10× faster)
- Throughput: 4× higher

**Agent workflows** (1000-token system prompt):
- vLLM: 1.8 sec/request
- SGLang: **0.35 sec/request** (5× faster)

**JSON decoding**:
- Standard: 45 tok/s
- SGLang: **135 tok/s** (3× faster)

### Throughput (Llama 3-8B, A100)

| Workload | vLLM | SGLang | Speedup |
|----------|------|--------|---------|
| Simple generation | 2500 tok/s | 2800 tok/s | 1.12× |
| Few-shot (10 examples) | 500 tok/s | 5000 tok/s | 10× |
| Agent (tool calls) | 800 tok/s | 4000 tok/s | 5× |
| JSON output | 600 tok/s | 2400 tok/s | 4× |

## Multi-turn conversations

```python
@sgl.function
def multi_turn_chat(s, history, new_message):
    # System prompt (always cached)
    s += "You are a helpful AI assistant.\n\n"

    # Conversation history (cached as it grows)
    for msg in history:
        s += f"{msg['role']}: {msg['content']}\n"

    # New user message (only new part)
    s += f"User: {new_message}\n"
    s += "Assistant: "
    s += sgl.gen("response", max_tokens=200)

# Turn 1
history = []
state = multi_turn_chat.run(history=history, new_message="Hi there!")
history.append({"role": "User", "content": "Hi there!"})
history.append({"role": "Assistant", "content": state["response"]})

# Turn 2 (reuses Turn 1 KV cache)
state = multi_turn_chat.run(history=history, new_message="What's 2+2?")
# Only computes new message (much faster!)

# Turn 3 (reuses Turn 1 + Turn 2 KV cache)
state = multi_turn_chat.run(history=history, new_message="Tell me a joke")
# Progressively faster as history grows
```

## Advanced features

### Speculative decoding

```bash
# Launch with draft model (2-3× faster)
python -m sglang.launch_server \
    --model-path meta-llama/Meta-Llama-3-70B-Instruct \
    --speculative-model meta-llama/Meta-Llama-3-8B-Instruct \
    --speculative-num-steps 5
```

### Multi-modal (vision models)

```python
@sgl.function
def describe_image(s, image_path):
    s += sgl.image(image_path)
    s += "Describe this image in detail: "
    s += sgl.gen("description", max_tokens=200)

state = describe_image.run(image_path="photo.jpg")
print(state["description"])
```

### Batching and parallel requests

```python
# Automatic batching (continuous batching)
states = sgl.run_batch(
    [
        simple_gen.bind(question="What is AI?"),
        simple_gen.bind(question="What is ML?"),
        simple_gen.bind(question="What is DL?"),
    ]
)

# All 3 processed in single batch (efficient)
```

## OpenAI-compatible API

```bash
# Start server with OpenAI API
python -m sglang.launch_server \
    --model-path meta-llama/Meta-Llama-3-8B-Instruct \
    --port 30000

# Use with OpenAI client
curl http://localhost:30000/v1/chat/completions \
  -H "Content-Type: application/json" \
  -d '{
    "model": "default",
    "messages": [
      {"role": "system", "content": "You are helpful"},
      {"role": "user", "content": "Hello"}
    ],
    "temperature": 0.7,
    "max_tokens": 100
  }'

# Works with OpenAI Python SDK
from openai import OpenAI
client = OpenAI(base_url="http://localhost:30000/v1", api_key="EMPTY")

response = client.chat.completions.create(
    model="default",
    messages=[{"role": "user", "content": "Hello"}]
)
```

## Supported models

**Text models**:
- Llama 2, Llama 3, Llama 3.1, Llama 3.2
- Mistral, Mixtral
- Qwen, Qwen2, QwQ
- DeepSeek-V2, DeepSeek-V3
- Gemma, Phi-3

**Vision models**:
- LLaVA, LLaVA-OneVision
- Phi-3-Vision
- Qwen2-VL

**100+ models** from HuggingFace

## Hardware support

**NVIDIA**: A100, H100, L4, T4 (CUDA 11.8+)
**AMD**: MI300, MI250 (ROCm 6.0+)
**Intel**: Xeon with GPU (coming soon)
**Apple**: M1/M2/M3 via MPS (experimental)

## References

- **[Structured Generation Guide](references/structured-generation.md)** - JSON schemas, regex, grammars, validation
- **[RadixAttention Deep Dive](references/radix-attention.md)** - How it works, optimization, benchmarks
- **[Production Deployment](references/deployment.md)** - Multi-GPU, monitoring, autoscaling

## Resources

- **GitHub**: https://github.com/sgl-project/sglang
- **Docs**: https://sgl-project.github.io/
- **Paper**: RadixAttention (arXiv:2312.07104)
- **Discord**: https://discord.gg/sglang




---


## 📚 Reference Materials


### Deployment

# Production Deployment Guide

Complete guide to deploying SGLang in production environments.

## Server Deployment

### Basic server

```bash
python -m sglang.launch_server \
    --model-path meta-llama/Meta-Llama-3-8B-Instruct \
    --host 0.0.0.0 \
    --port 30000 \
    --mem-fraction-static 0.9
```

### Multi-GPU (Tensor Parallelism)

```bash
# Llama 3-70B on 4 GPUs
python -m sglang.launch_server \
    --model-path meta-llama/Meta-Llama-3-70B-Instruct \
    --tp 4 \
    --port 30000
```

### Quantization

```bash
# FP8 quantization (H100)
python -m sglang.launch_server \
    --model-path meta-llama/Meta-Llama-3-70B-Instruct \
    --quantization fp8 \
    --tp 4

# INT4 AWQ quantization
python -m sglang.launch_server \
    --model-path TheBloke/Llama-2-70B-AWQ \
    --quantization awq \
    --tp 2

# INT4 GPTQ quantization
python -m sglang.launch_server \
    --model-path TheBloke/Llama-2-70B-GPTQ \
    --quantization gptq \
    --tp 2
```

## Docker Deployment

### Dockerfile

```dockerfile
FROM nvidia/cuda:12.1.0-devel-ubuntu22.04

# Install Python
RUN apt-get update && apt-get install -y python3.10 python3-pip git

# Install SGLang
RUN pip3 install "sglang[all]" flashinfer -i https://flashinfer.ai/whl/cu121/torch2.4/

# Copy model (or download at runtime)
WORKDIR /app

# Expose port
EXPOSE 30000

# Start server
CMD ["python3", "-m", "sglang.launch_server", \
     "--model-path", "meta-llama/Meta-Llama-3-8B-Instruct", \
     "--host", "0.0.0.0", \
     "--port", "30000"]
```

### Build and run

```bash
# Build image
docker build -t sglang:latest .

# Run with GPU
docker run --gpus all -p 30000:30000 sglang:latest

# Run with specific GPUs
docker run --gpus '"device=0,1,2,3"' -p 30000:30000 sglang:latest

# Run with custom model
docker run --gpus all -p 30000:30000 \
    -e MODEL_PATH="meta-llama/Meta-Llama-3-70B-Instruct" \
    -e TP_SIZE="4" \
    sglang:latest
```

## Kubernetes Deployment

### Deployment YAML

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: sglang-llama3-70b
spec:
  replicas: 2
  selector:
    matchLabels:
      app: sglang
  template:
    metadata:
      labels:
        app: sglang
    spec:
      containers:
      - name: sglang
        image: sglang:latest
        command:
          - python3
          - -m
          - sglang.launch_server
          - --model-path=meta-llama/Meta-Llama-3-70B-Instruct
          - --tp=4
          - --host=0.0.0.0
          - --port=30000
          - --mem-fraction-static=0.9
        ports:
        - containerPort: 30000
          name: http
        resources:
          limits:
            nvidia.com/gpu: 4
        livenessProbe:
          httpGet:
            path: /health
            port: 30000
          initialDelaySeconds: 60
          periodSeconds: 10
        readinessProbe:
          httpGet:
            path: /health
            port: 30000
          initialDelaySeconds: 30
          periodSeconds: 5
---
apiVersion: v1
kind: Service
metadata:
  name: sglang-service
spec:
  selector:
    app: sglang
  ports:
  - port: 80
    targetPort: 30000
  type: LoadBalancer
```

## Monitoring

### Health checks

```bash
# Health endpoint
curl http://localhost:30000/health

# Model info
curl http://localhost:30000/v1/models

# Server stats
curl http://localhost:30000/stats
```

### Prometheus metrics

```bash
# Start server with metrics
python -m sglang.launch_server \
    --model-path meta-llama/Meta-Llama-3-8B-Instruct \
    --enable-metrics

# Metrics endpoint
curl http://localhost:30000/metrics

# Key metrics:
# - sglang_request_total
# - sglang_request_duration_seconds
# - sglang_tokens_generated_total
# - sglang_active_requests
# - sglang_queue_size
# - sglang_radix_cache_hit_rate
# - sglang_gpu_memory_used_bytes
```

### Logging

```bash
# Enable debug logging
python -m sglang.launch_server \
    --model-path meta-llama/Meta-Llama-3-8B-Instruct \
    --log-level debug

# Log to file
python -m sglang.launch_server \
    --model-path meta-llama/Meta-Llama-3-8B-Instruct \
    --log-file /var/log/sglang.log
```

## Load Balancing

### NGINX configuration

```nginx
upstream sglang_backend {
    least_conn;  # Route to least busy instance
    server sglang-1:30000 max_fails=3 fail_timeout=30s;
    server sglang-2:30000 max_fails=3 fail_timeout=30s;
    server sglang-3:30000 max_fails=3 fail_timeout=30s;
}

server {
    listen 80;

    location / {
        proxy_pass http://sglang_backend;
        proxy_http_version 1.1;
        proxy_set_header Connection "";
        proxy_read_timeout 300s;
        proxy_connect_timeout 10s;

        # For streaming
        proxy_buffering off;
        proxy_cache off;
    }

    location /metrics {
        proxy_pass http://sglang_backend/metrics;
    }
}
```

## Autoscaling

### HPA based on GPU utilization

```yaml
apiVersion: autoscaling/v2
kind: HorizontalPodAutoscaler
metadata:
  name: sglang-hpa
spec:
  scaleTargetRef:
    apiVersion: apps/v1
    kind: Deployment
    name: sglang-llama3-70b
  minReplicas: 2
  maxReplicas: 10
  metrics:
  - type: Pods
    pods:
      metric:
        name: nvidia_gpu_duty_cycle
      target:
        type: AverageValue
        averageValue: "80"  # Scale when GPU >80%
```

### HPA based on active requests

```yaml
metrics:
- type: Pods
  pods:
    metric:
      name: sglang_active_requests
    target:
      type: AverageValue
      averageValue: "50"  # Scale when >50 active requests per pod
```

## Performance Tuning

### Memory optimization

```bash
# Reduce memory usage
python -m sglang.launch_server \
    --model-path meta-llama/Meta-Llama-3-70B-Instruct \
    --tp 4 \
    --mem-fraction-static 0.85 \  # Use 85% of GPU memory
    --max-radix-cache-len 8192    # Limit cache to 8K tokens
```

### Throughput optimization

```bash
# Maximize throughput
python -m sglang.launch_server \
    --model-path meta-llama/Meta-Llama-3-8B-Instruct \
    --mem-fraction-static 0.95 \  # More memory for batching
    --max-radix-cache-len 16384 \ # Larger cache
    --max-running-requests 256    # More concurrent requests
```

### Latency optimization

```bash
# Minimize latency
python -m sglang.launch_server \
    --model-path meta-llama/Meta-Llama-3-8B-Instruct \
    --max-running-requests 32 \   # Fewer concurrent (less queueing)
    --schedule-policy fcfs         # First-come first-served
```

## Multi-Node Deployment

### Ray cluster setup

```bash
# Head node
ray start --head --port=6379

# Worker nodes
ray start --address='head-node:6379'

# Launch server across cluster
python -m sglang.launch_server \
    --model-path meta-llama/Meta-Llama-3-405B-Instruct \
    --tp 8 \
    --num-nodes 2  # Use 2 nodes (8 GPUs each)
```

## Security

### API authentication

```bash
# Start with API key
python -m sglang.launch_server \
    --model-path meta-llama/Meta-Llama-3-8B-Instruct \
    --api-key YOUR_SECRET_KEY

# Client request
curl http://localhost:30000/v1/chat/completions \
  -H "Authorization: Bearer YOUR_SECRET_KEY" \
  -H "Content-Type: application/json" \
  -d '{"model": "default", "messages": [...]}'
```

### Network policies (Kubernetes)

```yaml
apiVersion: networking.k8s.io/v1
kind: NetworkPolicy
metadata:
  name: sglang-policy
spec:
  podSelector:
    matchLabels:
      app: sglang
  policyTypes:
  - Ingress
  ingress:
  - from:
    - podSelector:
        matchLabels:
          app: api-gateway  # Only allow from gateway
    ports:
    - protocol: TCP
      port: 30000
```

## Troubleshooting

### High memory usage

**Check**:
```bash
nvidia-smi
curl http://localhost:30000/stats | grep cache
```

**Solutions**:
```bash
# Reduce cache size
--max-radix-cache-len 4096

# Reduce memory fraction
--mem-fraction-static 0.75

# Enable quantization
--quantization fp8
```

### Low throughput

**Check**:
```bash
curl http://localhost:30000/stats | grep queue_size
```

**Solutions**:
```bash
# Increase batch size
--max-running-requests 256

# Add more GPUs
--tp 4  # Increase tensor parallelism

# Check cache hit rate (should be >70%)
curl http://localhost:30000/stats | grep cache_hit_rate
```

### High latency

**Check**:
```bash
curl http://localhost:30000/metrics | grep duration
```

**Solutions**:
```bash
# Reduce concurrent requests
--max-running-requests 32

# Use FCFS scheduling (no batching delay)
--schedule-policy fcfs

# Add more replicas (horizontal scaling)
```

### OOM errors

**Solutions**:
```bash
# Reduce batch size
--max-running-requests 128

# Reduce cache
--max-radix-cache-len 2048

# Enable quantization
--quantization awq

# Increase tensor parallelism
--tp 8
```

## Best Practices

1. **Use RadixAttention** - Enabled by default, 5-10× speedup for agents
2. **Monitor cache hit rate** - Target >70% for agent/few-shot workloads
3. **Set health checks** - Use `/health` endpoint for k8s probes
4. **Enable metrics** - Monitor with Prometheus + Grafana
5. **Use load balancing** - Distribute load across replicas
6. **Tune memory** - Start with `--mem-fraction-static 0.9`, adjust based on OOM
7. **Use quantization** - FP8 on H100, AWQ/GPTQ on A100
8. **Set up autoscaling** - Scale based on GPU utilization or active requests
9. **Log to persistent storage** - Use `--log-file` for debugging
10. **Test before production** - Run load tests with expected traffic patterns

## Cost Optimization

### GPU selection

**A100 80GB** ($3-4/hour):
- Llama 3-70B with FP8 (TP=4)
- Throughput: 10,000-15,000 tok/s
- Cost per 1M tokens: $0.20-0.30

**H100 80GB** ($6-8/hour):
- Llama 3-70B with FP8 (TP=4)
- Throughput: 20,000-30,000 tok/s
- Cost per 1M tokens: $0.15-0.25 (2× faster)

**L4** ($0.50-1/hour):
- Llama 3-8B
- Throughput: 1,500-2,500 tok/s
- Cost per 1M tokens: $0.20-0.40

### Batching for cost efficiency

**Low batch (batch=1)**:
- Throughput: 1,000 tok/s
- Cost: $3/hour ÷ 1M tok/hour = $3/M tokens

**High batch (batch=128)**:
- Throughput: 8,000 tok/s
- Cost: $3/hour ÷ 8M tok/hour = $0.375/M tokens
- **8× cost reduction**

**Recommendation**: Target batch size 64-256 for optimal cost/latency.




### Structured Generation

# Structured Generation Guide

Complete guide to generating structured outputs with SGLang.

## JSON Generation

### Basic JSON output

```python
import sglang as sgl

@sgl.function
def basic_json(s, text):
    s += f"Extract person info from: {text}\n"
    s += "Output as JSON:\n"

    # Simple regex for JSON object
    s += sgl.gen(
        "json",
        max_tokens=150,
        regex=r'\{[^}]+\}'  # Basic JSON pattern
    )

state = basic_json.run(text="Alice is a 28-year-old doctor")
print(state["json"])
# Output: {"name": "Alice", "age": 28, "profession": "doctor"}
```

### JSON with schema validation

```python
@sgl.function
def schema_json(s, description):
    s += f"Create a product from: {description}\n"

    # Detailed JSON schema
    schema = {
        "type": "object",
        "properties": {
            "name": {"type": "string"},
            "price": {"type": "number", "minimum": 0},
            "category": {
                "type": "string",
                "enum": ["electronics", "clothing", "food", "books"]
            },
            "in_stock": {"type": "boolean"},
            "tags": {
                "type": "array",
                "items": {"type": "string"},
                "minItems": 1,
                "maxItems": 5
            }
        },
        "required": ["name", "price", "category", "in_stock"]
    }

    s += sgl.gen("product", max_tokens=300, json_schema=schema)

state = schema_json.run(
    description="Wireless headphones, $79.99, currently available, audio"
)
print(state["product"])
# Output: Valid JSON matching schema exactly
```

**Output example**:
```json
{
  "name": "Wireless Headphones",
  "price": 79.99,
  "category": "electronics",
  "in_stock": true,
  "tags": ["audio", "wireless", "bluetooth"]
}
```

### Nested JSON structures

```python
schema = {
    "type": "object",
    "properties": {
        "user": {
            "type": "object",
            "properties": {
                "id": {"type": "integer"},
                "name": {"type": "string"},
                "email": {"type": "string", "format": "email"}
            },
            "required": ["id", "name", "email"]
        },
        "orders": {
            "type": "array",
            "items": {
                "type": "object",
                "properties": {
                    "order_id": {"type": "string"},
                    "total": {"type": "number"},
                    "items": {
                        "type": "array",
                        "items": {"type": "string"}
                    }
                },
                "required": ["order_id", "total"]
            }
        }
    },
    "required": ["user", "orders"]
}

@sgl.function
def nested_json(s, data):
    s += f"Convert to JSON: {data}\n"
    s += sgl.gen("output", max_tokens=500, json_schema=schema)
```

## Regex-Constrained Generation

### Email extraction

```python
@sgl.function
def extract_email(s, text):
    s += f"Find email in: {text}\n"
    s += "Email: "

    # Email regex
    email_pattern = r'[a-zA-Z0-9._%+-]+@[a-zA-Z0-9.-]+\.[a-zA-Z]{2,}'
    s += sgl.gen("email", max_tokens=30, regex=email_pattern)

state = extract_email.run(text="Contact support at help@company.com")
print(state["email"])
# Output: "help@company.com" (guaranteed valid email format)
```

### Phone number extraction

```python
@sgl.function
def extract_phone(s, text):
    s += f"Extract phone from: {text}\n"
    s += "Phone: "

    # US phone number pattern
    phone_pattern = r'\(?\d{3}\)?[-.\s]?\d{3}[-.\s]?\d{4}'
    s += sgl.gen("phone", max_tokens=20, regex=phone_pattern)

state = extract_phone.run(text="Call me at (555) 123-4567")
print(state["phone"])
# Output: "(555) 123-4567"
```

### URL generation

```python
@sgl.function
def generate_url(s, domain, path):
    s += f"Create URL for domain {domain} with path {path}\n"
    s += "URL: "

    # URL pattern
    url_pattern = r'https?://[a-zA-Z0-9.-]+\.[a-zA-Z]{2,}(/[a-zA-Z0-9._~:/?#\[\]@!$&\'()*+,;=-]*)?'
    s += sgl.gen("url", max_tokens=50, regex=url_pattern)

state = generate_url.run(domain="example.com", path="/api/users")
print(state["url"])
# Output: "https://example.com/api/users"
```

### Date extraction

```python
@sgl.function
def extract_date(s, text):
    s += f"Find date in: {text}\n"
    s += "Date (YYYY-MM-DD): "

    # ISO date pattern
    date_pattern = r'\d{4}-\d{2}-\d{2}'
    s += sgl.gen("date", max_tokens=15, regex=date_pattern)

state = extract_date.run(text="Event scheduled for 2025-03-15")
print(state["date"])
# Output: "2025-03-15" (always valid format)
```

## Grammar-Based Generation

### EBNF grammar for Python

```python
python_grammar = """
?start: statement+

?statement: assignment
          | if_stmt
          | function_def
          | return_stmt

assignment: NAME "=" expr

if_stmt: "if" expr ":" suite ("elif" expr ":" suite)* ("else" ":" suite)?

function_def: "def" NAME "(" [parameters] "):" suite

return_stmt: "return" expr

?suite: simple_stmt | NEWLINE INDENT statement+ DEDENT

?simple_stmt: assignment | return_stmt | expr

?expr: NAME
     | NUMBER
     | STRING
     | expr "+" expr
     | expr "-" expr
     | expr "*" expr
     | expr "/" expr
     | NAME "(" [arguments] ")"

parameters: NAME ("," NAME)*
arguments: expr ("," expr)*

%import common.CNAME -> NAME
%import common.NUMBER
%import common.ESCAPED_STRING -> STRING
%import common.WS
%import common.NEWLINE
%import common.INDENT
%import common.DEDENT

%ignore WS
"""

@sgl.function
def generate_python(s, description):
    s += f"Generate Python function for: {description}\n"
    s += "```python\n"
    s += sgl.gen("code", max_tokens=300, grammar=python_grammar)
    s += "\n```"

state = generate_python.run(
    description="Calculate factorial of a number"
)
print(state["code"])
# Output: Valid Python code following grammar
```

### SQL query grammar

```python
sql_grammar = """
?start: select_stmt

select_stmt: "SELECT" column_list "FROM" table_name [where_clause] [order_clause] [limit_clause]

column_list: column ("," column)*
           | "*"

column: NAME
      | NAME "." NAME
      | NAME "AS" NAME

table_name: NAME

where_clause: "WHERE" condition

condition: NAME "=" value
         | NAME ">" value
         | NAME "<" value
         | condition "AND" condition
         | condition "OR" condition

order_clause: "ORDER BY" NAME ["ASC" | "DESC"]

limit_clause: "LIMIT" NUMBER

?value: STRING | NUMBER | "NULL"

%import common.CNAME -> NAME
%import common.NUMBER
%import common.ESCAPED_STRING -> STRING
%import common.WS

%ignore WS
"""

@sgl.function
def generate_sql(s, description):
    s += f"Generate SQL query for: {description}\n"
    s += sgl.gen("query", max_tokens=200, grammar=sql_grammar)

state = generate_sql.run(
    description="Find all active users sorted by join date"
)
print(state["query"])
# Output: SELECT * FROM users WHERE status = 'active' ORDER BY join_date DESC
```

## Multi-Step Structured Workflows

### Information extraction pipeline

```python
@sgl.function
def extract_structured_info(s, article):
    # Step 1: Extract entities
    s += f"Article: {article}\n\n"
    s += "Extract named entities:\n"

    entities_schema = {
        "type": "object",
        "properties": {
            "people": {"type": "array", "items": {"type": "string"}},
            "organizations": {"type": "array", "items": {"type": "string"}},
            "locations": {"type": "array", "items": {"type": "string"}},
            "dates": {"type": "array", "items": {"type": "string"}}
        }
    }

    s += sgl.gen("entities", max_tokens=200, json_schema=entities_schema)

    # Step 2: Classify sentiment
    s += "\n\nClassify sentiment:\n"

    sentiment_schema = {
        "type": "object",
        "properties": {
            "sentiment": {"type": "string", "enum": ["positive", "negative", "neutral"]},
            "confidence": {"type": "number", "minimum": 0, "maximum": 1}
        }
    }

    s += sgl.gen("sentiment", max_tokens=50, json_schema=sentiment_schema)

    # Step 3: Generate summary
    s += "\n\nGenerate brief summary (max 50 words):\n"
    s += sgl.gen("summary", max_tokens=75, stop=["\n\n"])

# Run pipeline
state = extract_structured_info.run(article="...")

print("Entities:", state["entities"])
print("Sentiment:", state["sentiment"])
print("Summary:", state["summary"])
```

### Form filling workflow

```python
@sgl.function
def fill_form(s, user_input):
    s += "Fill out the application form based on: " + user_input + "\n\n"

    # Name
    s += "Full Name: "
    s += sgl.gen("name", max_tokens=30, regex=r'[A-Z][a-z]+ [A-Z][a-z]+', stop=["\n"])

    # Email
    s += "\nEmail: "
    s += sgl.gen("email", max_tokens=50, regex=r'[a-zA-Z0-9._%+-]+@[a-zA-Z0-9.-]+\.[a-zA-Z]{2,}', stop=["\n"])

    # Phone
    s += "\nPhone: "
    s += sgl.gen("phone", max_tokens=20, regex=r'\d{3}-\d{3}-\d{4}', stop=["\n"])

    # Address (structured JSON)
    s += "\nAddress (JSON): "
    address_schema = {
        "type": "object",
        "properties": {
            "street": {"type": "string"},
            "city": {"type": "string"},
            "state": {"type": "string", "pattern": "^[A-Z]{2}$"},
            "zip": {"type": "string", "pattern": "^\\d{5}$"}
        },
        "required": ["street", "city", "state", "zip"]
    }
    s += sgl.gen("address", max_tokens=150, json_schema=address_schema)

state = fill_form.run(
    user_input="John Doe, john.doe@email.com, 555-123-4567, 123 Main St, Boston MA 02101"
)

print("Name:", state["name"])
print("Email:", state["email"])
print("Phone:", state["phone"])
print("Address:", state["address"])
```

## Error Handling and Validation

### Retry on invalid format

```python
@sgl.function
def extract_with_retry(s, text, max_retries=3):
    schema = {
        "type": "object",
        "properties": {
            "value": {"type": "number"},
            "unit": {"type": "string", "enum": ["kg", "lb", "g"]}
        },
        "required": ["value", "unit"]
    }

    for attempt in range(max_retries):
        s += f"Extract weight from: {text}\n"
        s += f"Attempt {attempt + 1}:\n"
        s += sgl.gen(f"output_{attempt}", max_tokens=100, json_schema=schema)

        # Validate (in production, check if parsing succeeded)
        # If valid, break; else continue

state = extract_with_retry.run(text="Package weighs 5.2 kilograms")
```

### Fallback to less strict pattern

```python
@sgl.function
def extract_email_flexible(s, text):
    s += f"Extract email from: {text}\n"

    # Try strict pattern first
    s += "Email (strict): "
    s += sgl.gen(
        "email_strict",
        max_tokens=30,
        regex=r'[a-zA-Z0-9._%+-]+@[a-zA-Z0-9.-]+\.[a-zA-Z]{2,}',
        temperature=0.0
    )

    # If fails, fallback to looser pattern
    s += "\nEmail (loose): "
    s += sgl.gen(
        "email_loose",
        max_tokens=30,
        regex=r'\S+@\S+',
        temperature=0.0
    )
```

## Performance Tips

### Optimize regex patterns

```python
# BAD: Too complex, slow
complex_pattern = r'(https?://)?(www\.)?[a-zA-Z0-9-]+(\.[a-zA-Z0-9-]+)+(/[a-zA-Z0-9._~:/?#\[\]@!$&\'()*+,;=-]*)?'

# GOOD: Simpler, faster
simple_pattern = r'https?://[a-z0-9.-]+\.[a-z]{2,}'
```

### Cache compiled grammars

```python
# Compile grammar once
from lark import Lark
compiled_grammar = Lark(python_grammar, start='start')

# Reuse across requests
@sgl.function
def gen_with_cached_grammar(s, desc):
    s += sgl.gen("code", max_tokens=200, grammar=compiled_grammar)
```

### Batch structured generation

```python
# Generate multiple structured outputs in parallel
results = sgl.run_batch([
    extract_person.bind(text="Alice, 30, engineer"),
    extract_person.bind(text="Bob, 25, doctor"),
    extract_person.bind(text="Carol, 35, teacher")
])

# All processed efficiently with RadixAttention
```

## Real-World Examples

### API response generation

```python
@sgl.function
def api_response(s, query, data):
    s += f"Generate API response for query: {query}\n"
    s += f"Data: {data}\n\n"

    api_schema = {
        "type": "object",
        "properties": {
            "status": {"type": "string", "enum": ["success", "error"]},
            "data": {"type": "object"},
            "message": {"type": "string"},
            "timestamp": {"type": "string"}
        },
        "required": ["status", "data", "message"]
    }

    s += sgl.gen("response", max_tokens=300, json_schema=api_schema)

# Always returns valid API response format
```

### Database query builder

```python
@sgl.function
def build_query(s, natural_language):
    s += f"Convert to SQL: {natural_language}\n"
    s += "SELECT "
    s += sgl.gen("columns", max_tokens=50, stop=[" FROM"])
    s += " FROM "
    s += sgl.gen("table", max_tokens=20, stop=[" WHERE", "\n"])
    s += " WHERE "
    s += sgl.gen("condition", max_tokens=100, stop=[" ORDER", "\n"])

state = build_query.run(
    natural_language="Get all names and emails of users who joined after 2024"
)
# Output: Valid SQL query
```

### Code generation with syntax guarantee

```python
@sgl.function
def generate_function(s, spec):
    s += f"Generate Python function for: {spec}\n"
    s += "def "
    s += sgl.gen("func_name", max_tokens=15, regex=r'[a-z_][a-z0-9_]*', stop=["("])
    s += "("
    s += sgl.gen("params", max_tokens=30, stop=[")"])
    s += "):\n    "
    s += sgl.gen("body", max_tokens=200, grammar=python_grammar)

# Always generates syntactically valid Python
```




### Radix Attention

# RadixAttention Deep Dive

Complete guide to RadixAttention - SGLang's key innovation for automatic prefix caching.

## What is RadixAttention?

**RadixAttention** is an algorithm that automatically caches and reuses KV cache for common prefixes across requests using a radix tree data structure.

**Key insight**: In real-world LLM serving:
- System prompts are repeated across requests
- Few-shot examples are shared
- Multi-turn conversations build on previous context
- Agent tools/functions are defined once

**Problem with traditional serving**:
- Every request recomputes the entire prompt
- Wasteful for shared prefixes
- 5-10× slower than necessary

**RadixAttention solution**:
- Build radix tree of all processed tokens
- Automatically detect shared prefixes
- Reuse KV cache for matching tokens
- Only compute new/different tokens

## How It Works

### Radix Tree Structure

```
Example requests:
1. "System: You are helpful\nUser: What's AI?"
2. "System: You are helpful\nUser: What's ML?"
3. "System: You are helpful\nUser: What's DL?"

Radix tree:
Root
└── "System: You are helpful\nUser: What's "
    ├── "AI?" → [KV cache for request 1]
    ├── "ML?" → [KV cache for request 2]
    └── "DL?" → [KV cache for request 3]

Shared prefix: "System: You are helpful\nUser: What's "
→ Computed once, reused 3 times
→ 5× speedup!
```

### Token-Level Matching

RadixAttention works at the token level:

```python
# Request 1: "Hello world"
Tokens: [15496, 1917]  # Hello=15496, world=1917
→ KV cache computed and stored in tree

# Request 2: "Hello there"
Tokens: [15496, 612]   # Hello=15496, there=612
→ Reuses KV cache for token 15496
→ Only computes token 612
→ 2× faster
```

### Automatic Eviction

When memory is full:
1. **LRU policy**: Evict least recently used prefixes
2. **Leaf-first**: Remove leaf nodes before internal nodes
3. **Preserves common prefixes**: Frequently used prefixes stay cached

```
Before eviction (memory full):
Root
├── "System A" (used 5 min ago)
│   ├── "Task 1" (used 1 min ago) ← Keep (recent)
│   └── "Task 2" (used 30 min ago) ← Evict (old + leaf)
└── "System B" (used 60 min ago) ← Evict (very old)

After eviction:
Root
└── "System A"
    └── "Task 1"
```

## Performance Analysis

### Few-Shot Prompting

**Scenario**: 10 examples in prompt (2000 tokens), user query (50 tokens)

**Without RadixAttention** (vLLM):
- Request 1: Compute 2050 tokens (2000 examples + 50 query)
- Request 2: Compute 2050 tokens (recompute all examples)
- Request 3: Compute 2050 tokens (recompute all examples)
- Total: 6150 tokens computed

**With RadixAttention** (SGLang):
- Request 1: Compute 2050 tokens (initial)
- Request 2: Reuse 2000 tokens, compute 50 (query only)
- Request 3: Reuse 2000 tokens, compute 50 (query only)
- Total: 2150 tokens computed
- **Speedup: 2.86×** (6150 / 2150)

### Agent Workflows

**Scenario**: System prompt (1000 tokens) + tools (500 tokens) + query (100 tokens)

**Without RadixAttention**:
- Request 1: 1600 tokens
- Request 2: 1600 tokens
- Request 3: 1600 tokens
- Total: 4800 tokens

**With RadixAttention**:
- Request 1: 1600 tokens (initial)
- Request 2: Reuse 1500, compute 100
- Request 3: Reuse 1500, compute 100
- Total: 1800 tokens
- **Speedup: 2.67×**

### Multi-Turn Conversations

**Scenario**: Conversation grows from 100 → 500 → 1000 tokens

| Turn | Tokens | vLLM | SGLang (RadixAttention) |
|------|--------|------|-------------------------|
| 1 | 100 | 100 | 100 (initial) |
| 2 | 500 | 500 | 400 (reuse 100) |
| 3 | 1000 | 1000 | 500 (reuse 500) |
| **Total** | | **1600** | **1000** |
| **Speedup** | | | **1.6×** |

As conversation grows, speedup increases!

## Benchmarks

### Throughput Comparison (Llama 3-8B, A100)

| Workload | Prefix Length | vLLM | SGLang | Speedup |
|----------|---------------|------|--------|---------|
| Simple generation | 0 | 2500 tok/s | 2800 tok/s | 1.12× |
| Few-shot (5 ex) | 1000 | 800 tok/s | 3200 tok/s | 4× |
| Few-shot (10 ex) | 2000 | 500 tok/s | 5000 tok/s | **10×** |
| Agent (tools) | 1500 | 800 tok/s | 4000 tok/s | 5× |
| Chat (history) | 500-2000 | 1200 tok/s | 3600 tok/s | 3× |

**Key insight**: Longer shared prefixes = bigger speedups

### Latency Reduction

**Agent workflow** (1000-token system prompt):

| Metric | vLLM | SGLang | Improvement |
|--------|------|--------|-------------|
| First request | 1.8s | 1.8s | Same (no cache) |
| Subsequent requests | 1.8s | **0.35s** | **5× faster** |
| P50 latency (100 req) | 1.8s | 0.42s | 4.3× faster |
| P99 latency | 2.1s | 0.58s | 3.6× faster |

### Memory Efficiency

**Without RadixAttention**:
- Each request stores its own KV cache
- 100 requests with 2000-token prefix = 200K tokens cached
- Memory: ~1.5 GB (Llama 3-8B, FP16)

**With RadixAttention**:
- Prefix stored once in radix tree
- 100 requests share 2000-token prefix
- Memory: ~15 MB for prefix + unique tokens
- **Savings: 99%** for shared portions

## Configuration

### Enable/Disable RadixAttention

```bash
# Enabled by default
python -m sglang.launch_server \
    --model-path meta-llama/Meta-Llama-3-8B-Instruct

# Disable (for comparison)
python -m sglang.launch_server \
    --model-path meta-llama/Meta-Llama-3-8B-Instruct \
    --disable-radix-cache
```

### Cache Size Tuning

```bash
# Set max cache size (default: 90% of GPU memory)
python -m sglang.launch_server \
    --model-path meta-llama/Meta-Llama-3-8B-Instruct \
    --max-radix-cache-len 16384  # Max 16K tokens cached

# Reserve memory for KV cache
--mem-fraction-static 0.85  # Use 85% GPU memory for cache
```

### Eviction Policy

```bash
# LRU eviction (default)
--eviction-policy lru

# FIFO eviction
--eviction-policy fifo
```

## Best Practices

### Design prompts for prefix sharing

**Bad** (no prefix sharing):
```python
# Each request has unique prefix
request_1 = "User Alice asks: What is AI?"
request_2 = "User Bob asks: What is ML?"
request_3 = "User Carol asks: What is DL?"

# No common prefix → No speedup
```

**Good** (maximize prefix sharing):
```python
# Shared system prompt
system = "You are a helpful AI assistant.\n\n"

request_1 = system + "User: What is AI?"
request_2 = system + "User: What is ML?"
request_3 = system + "User: What is DL?"

# Shared prefix → 5× speedup!
```

### Structure agent prompts

```python
# Template for maximum caching
@sgl.function
def agent_template(s, user_query):
    # Layer 1: System prompt (always cached)
    s += "You are a helpful assistant.\n\n"

    # Layer 2: Tools definition (always cached)
    s += "Available tools:\n"
    s += "- get_weather(location)\n"
    s += "- send_email(to, subject, body)\n\n"

    # Layer 3: Examples (always cached)
    s += "Examples:\n"
    s += "User: What's the weather?\n"
    s += "Assistant: <tool>get_weather('NYC')</tool>\n\n"

    # Layer 4: User query (unique per request)
    s += f"User: {user_query}\n"
    s += "Assistant: "
    s += sgl.gen("response", max_tokens=200)

# Layers 1-3 cached, only Layer 4 computed
# 5× faster for typical agent queries
```

### Optimize few-shot prompting

```python
# BAD: Examples mixed with query
def bad_few_shot(s, query):
    s += f"Query: {query}\n"  # Unique
    s += "Example 1: ..."     # Can't be cached
    s += "Example 2: ..."
    s += sgl.gen("answer")

# GOOD: Examples first, then query
def good_few_shot(s, query):
    # Examples (shared prefix, always cached)
    s += "Example 1: ...\n"
    s += "Example 2: ...\n"
    s += "Example 3: ...\n\n"

    # Query (unique suffix, computed)
    s += f"Query: {query}\n"
    s += sgl.gen("answer")

# 10× faster with RadixAttention
```

## Monitoring

### Cache hit rate

```python
# Check cache statistics
import requests
response = requests.get("http://localhost:30000/stats")
stats = response.json()

print(f"Cache hit rate: {stats['radix_cache_hit_rate']:.2%}")
print(f"Tokens cached: {stats['radix_cache_tokens']}")
print(f"Cache size: {stats['radix_cache_size_mb']} MB")

# Target: >80% hit rate for agent/few-shot workloads
```

### Optimization metrics

```bash
# Monitor cache usage
curl http://localhost:30000/metrics | grep radix

# Key metrics:
# - radix_cache_hit_tokens: Tokens reused from cache
# - radix_cache_miss_tokens: Tokens computed (not cached)
# - radix_cache_evictions: Number of evictions (should be low)
```

## Advanced Patterns

### Hierarchical caching

```python
@sgl.function
def hierarchical_agent(s, domain, task, query):
    # Level 1: Global system (cached across all requests)
    s += "You are an AI assistant.\n\n"

    # Level 2: Domain knowledge (cached per domain)
    s += f"Domain: {domain}\n"
    s += f"Knowledge: {get_domain_knowledge(domain)}\n\n"

    # Level 3: Task context (cached per task)
    s += f"Task: {task}\n"
    s += f"Instructions: {get_task_instructions(task)}\n\n"

    # Level 4: User query (unique)
    s += f"Query: {query}\n"
    s += sgl.gen("response")

# Example cache tree:
# Root
# └── "You are an AI assistant\n\n" (L1)
#     ├── "Domain: Finance\n..." (L2)
#     │   ├── "Task: Analysis\n..." (L3)
#     │   │   └── "Query: ..." (L4)
#     │   └── "Task: Forecast\n..." (L3)
#     └── "Domain: Legal\n..." (L2)
```

### Batch requests with common prefix

```python
# All requests share system prompt
system_prompt = "You are a helpful assistant.\n\n"

queries = [
    "What is AI?",
    "What is ML?",
    "What is DL?",
]

# Run in batch (RadixAttention automatically optimizes)
results = sgl.run_batch([
    agent.bind(prefix=system_prompt, query=q)
    for q in queries
])

# System prompt computed once, shared across all 3 requests
# 3× faster than sequential
```

## Troubleshooting

### Low cache hit rate (<50%)

**Causes**:
1. Prompts have no common structure
2. Dynamic content in prefix (timestamps, IDs)
3. Cache size too small (evictions)

**Solutions**:
1. Restructure prompts (shared prefix first)
2. Move dynamic content to suffix
3. Increase `--max-radix-cache-len`

### High memory usage

**Cause**: Too many unique prefixes cached

**Solutions**:
```bash
# Reduce cache size
--max-radix-cache-len 8192

# More aggressive eviction
--mem-fraction-static 0.75
```

### Performance worse than vLLM

**Cause**: No prefix sharing in workload

**Solution**: RadixAttention has small overhead if no sharing. Use vLLM for simple generation workloads without repeated prefixes.

## Comparison with Other Systems

| System | Prefix Caching | Automatic | Performance |
|--------|----------------|-----------|-------------|
| **SGLang** | ✅ RadixAttention | ✅ Automatic | 5-10× for agents |
| vLLM | ❌ No prefix caching | N/A | Baseline |
| Text Generation Inference | ✅ Prefix caching | ❌ Manual | 2-3× (if configured) |
| TensorRT-LLM | ✅ Static prefix | ❌ Manual | 2× (if configured) |

**SGLang advantage**: Fully automatic - no configuration needed, works for any workload with prefix sharing.




---

## 🚀 Usage

**Reference this template:** `@skill-sglang.md`


**Platform-specific:**
- **GitHub Copilot**: Add to `.github/copilot-instructions.md`
- **Augment Code**: Use `aug context add` command
- **Cursor/Windsurf**: Reference in chat with `@filename`
- **Claude**: Add to Project Knowledge
- **ChatGPT**: Add to Custom GPT configuration
