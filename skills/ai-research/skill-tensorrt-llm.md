---
id: skill-tensorrt-llm
type: skill
name: tensorrt-llm
description: Optimizes LLM inference with NVIDIA TensorRT for maximum throughput and
  lowest latency. Use for production deployment on NVIDIA GPUs (A100/H100), when you
  need 10-100x faster inference than PyTorch, or for serving models with quantization
  (FP8/INT4), in-flight batching, and multi-GPU scaling.
category: ai-research
complexity: medium
keywords:
- api
- deployment
- docker
- github
- optimization
- performance
- python
capabilities: []
token_estimate: 695
has_references: true
reference_count: 3
---

<!-- Converted from Claude Skill Template -->
<!-- Complexity: Medium -->
<!-- Estimated Tokens: ~695 -->
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




# tensorrt-llm

> Optimizes LLM inference with NVIDIA TensorRT for maximum throughput and lowest latency. Use for production deployment on NVIDIA GPUs (A100/H100), when you need 10-100x faster inference than PyTorch, or for serving models with quantization (FP8/INT4), in-flight batching, and multi-GPU scaling.

# TensorRT-LLM

NVIDIA's open-source library for optimizing LLM inference with state-of-the-art performance on NVIDIA GPUs.

## When to use TensorRT-LLM

**Use TensorRT-LLM when:**
- Deploying on NVIDIA GPUs (A100, H100, GB200)
- Need maximum throughput (24,000+ tokens/sec on Llama 3)
- Require low latency for real-time applications
- Working with quantized models (FP8, INT4, FP4)
- Scaling across multiple GPUs or nodes

**Use vLLM instead when:**
- Need simpler setup and Python-first API
- Want PagedAttention without TensorRT compilation
- Working with AMD GPUs or non-NVIDIA hardware

**Use llama.cpp instead when:**
- Deploying on CPU or Apple Silicon
- Need edge deployment without NVIDIA GPUs
- Want simpler GGUF quantization format

## Quick start

### Installation

```bash
# Docker (recommended)
docker pull nvidia/tensorrt_llm:latest

# pip install
pip install tensorrt_llm==1.2.0rc3

# Requires CUDA 13.0.0, TensorRT 10.13.2, Python 3.10-3.12
```

### Basic inference

```python
from tensorrt_llm import LLM, SamplingParams

# Initialize model
llm = LLM(model="meta-llama/Meta-Llama-3-8B")

# Configure sampling
sampling_params = SamplingParams(
    max_tokens=100,
    temperature=0.7,
    top_p=0.9
)

# Generate
prompts = ["Explain quantum computing"]
outputs = llm.generate(prompts, sampling_params)

for output in outputs:
    print(output.text)
```

### Serving with trtllm-serve

```bash
# Start server (automatic model download and compilation)
trtllm-serve meta-llama/Meta-Llama-3-8B \
    --tp_size 4 \              # Tensor parallelism (4 GPUs)
    --max_batch_size 256 \
    --max_num_tokens 4096

# Client request
curl -X POST http://localhost:8000/v1/chat/completions \
  -H "Content-Type: application/json" \
  -d '{
    "model": "meta-llama/Meta-Llama-3-8B",
    "messages": [{"role": "user", "content": "Hello!"}],
    "temperature": 0.7,
    "max_tokens": 100
  }'
```

## Key features

### Performance optimizations
- **In-flight batching**: Dynamic batching during generation
- **Paged KV cache**: Efficient memory management
- **Flash Attention**: Optimized attention kernels
- **Quantization**: FP8, INT4, FP4 for 2-4× faster inference
- **CUDA graphs**: Reduced kernel launch overhead

### Parallelism
- **Tensor parallelism (TP)**: Split model across GPUs
- **Pipeline parallelism (PP)**: Layer-wise distribution
- **Expert parallelism**: For Mixture-of-Experts models
- **Multi-node**: Scale beyond single machine

### Advanced features
- **Speculative decoding**: Faster generation with draft models
- **LoRA serving**: Efficient multi-adapter deployment
- **Disaggregated serving**: Separate prefill and generation

## Common patterns

### Quantized model (FP8)

```python
from tensorrt_llm import LLM

# Load FP8 quantized model (2× faster, 50% memory)
llm = LLM(
    model="meta-llama/Meta-Llama-3-70B",
    dtype="fp8",
    max_num_tokens=8192
)

# Inference same as before
outputs = llm.generate(["Summarize this article..."])
```

### Multi-GPU deployment

```python
# Tensor parallelism across 8 GPUs
llm = LLM(
    model="meta-llama/Meta-Llama-3-405B",
    tensor_parallel_size=8,
    dtype="fp8"
)
```

### Batch inference

```python
# Process 100 prompts efficiently
prompts = [f"Question {i}: ..." for i in range(100)]

outputs = llm.generate(
    prompts,
    sampling_params=SamplingParams(max_tokens=200)
)

# Automatic in-flight batching for maximum throughput
```

## Performance benchmarks

**Meta Llama 3-8B** (H100 GPU):
- Throughput: 24,000 tokens/sec
- Latency: ~10ms per token
- vs PyTorch: **100× faster**

**Llama 3-70B** (8× A100 80GB):
- FP8 quantization: 2× faster than FP16
- Memory: 50% reduction with FP8

## Supported models

- **LLaMA family**: Llama 2, Llama 3, CodeLlama
- **GPT family**: GPT-2, GPT-J, GPT-NeoX
- **Qwen**: Qwen, Qwen2, QwQ
- **DeepSeek**: DeepSeek-V2, DeepSeek-V3
- **Mixtral**: Mixtral-8x7B, Mixtral-8x22B
- **Vision**: LLaVA, Phi-3-vision
- **100+ models** on HuggingFace

## References

- **[Optimization Guide](references/optimization.md)** - Quantization, batching, KV cache tuning
- **[Multi-GPU Setup](references/multi-gpu.md)** - Tensor/pipeline parallelism, multi-node
- **[Serving Guide](references/serving.md)** - Production deployment, monitoring, autoscaling

## Resources

- **Docs**: https://nvidia.github.io/TensorRT-LLM/
- **GitHub**: https://github.com/NVIDIA/TensorRT-LLM
- **Models**: https://huggingface.co/models?library=tensorrt_llm




---


## 📚 Reference Materials


### Optimization

# TensorRT-LLM Optimization Guide

Comprehensive guide to optimizing LLM inference with TensorRT-LLM.

## Quantization

### FP8 Quantization (Recommended for H100)

**Benefits**:
- 2× faster inference
- 50% memory reduction
- Minimal accuracy loss (<1% perplexity degradation)

**Usage**:
```python
from tensorrt_llm import LLM

# Automatic FP8 quantization
llm = LLM(
    model="meta-llama/Meta-Llama-3-70B",
    dtype="fp8",
    quantization="fp8"
)
```

**Performance** (Llama 3-70B on 8× H100):
- FP16: 5,000 tokens/sec
- FP8: **10,000 tokens/sec** (2× speedup)
- Memory: 140GB → 70GB

### INT4 Quantization (Maximum compression)

**Benefits**:
- 4× memory reduction
- 3-4× faster inference
- Fits larger models on same hardware

**Usage**:
```python
# INT4 with AWQ calibration
llm = LLM(
    model="meta-llama/Meta-Llama-3-405B",
    dtype="int4_awq",
    quantization="awq"
)

# INT4 with GPTQ calibration
llm = LLM(
    model="meta-llama/Meta-Llama-3-405B",
    dtype="int4_gptq",
    quantization="gptq"
)
```

**Trade-offs**:
- Accuracy: 1-3% perplexity increase
- Speed: 3-4× faster than FP16
- Use case: When memory is critical

## In-Flight Batching

**What it does**: Dynamically batches requests during generation instead of waiting for all sequences to finish.

**Configuration**:
```python
# Server configuration
trtllm-serve meta-llama/Meta-Llama-3-8B \
    --max_batch_size 256 \           # Maximum concurrent sequences
    --max_num_tokens 4096 \           # Total tokens in batch
    --enable_chunked_context \        # Split long prompts
    --scheduler_policy max_utilization
```

**Performance**:
- Throughput: **4-8× higher** vs static batching
- Latency: Lower P50/P99 for mixed workloads
- GPU utilization: 80-95% vs 40-60%

## Paged KV Cache

**What it does**: Manages KV cache memory like OS manages virtual memory (paging).

**Benefits**:
- 40-60% higher throughput
- No memory fragmentation
- Supports longer sequences

**Configuration**:
```python
# Automatic paged KV cache (default)
llm = LLM(
    model="meta-llama/Meta-Llama-3-8B",
    kv_cache_free_gpu_mem_fraction=0.9,  # Use 90% GPU mem for cache
    enable_prefix_caching=True            # Cache common prefixes
)
```

## Speculative Decoding

**What it does**: Uses small draft model to predict multiple tokens, verified by target model in parallel.

**Speedup**: 2-3× faster for long generations

**Usage**:
```python
from tensorrt_llm import LLM

# Target model (Llama 3-70B)
llm = LLM(
    model="meta-llama/Meta-Llama-3-70B",
    speculative_model="meta-llama/Meta-Llama-3-8B",  # Draft model
    num_speculative_tokens=5                          # Tokens to predict ahead
)

# Same API, 2-3× faster
outputs = llm.generate(prompts)
```

**Best models for drafting**:
- Target: Llama 3-70B → Draft: Llama 3-8B
- Target: Qwen2-72B → Draft: Qwen2-7B
- Same family, 8-10× smaller

## CUDA Graphs

**What it does**: Reduces kernel launch overhead by recording GPU operations.

**Benefits**:
- 10-20% lower latency
- More stable P99 latency
- Better for small batch sizes

**Configuration** (automatic by default):
```python
llm = LLM(
    model="meta-llama/Meta-Llama-3-8B",
    enable_cuda_graph=True,  # Default: True
    cuda_graph_cache_size=2  # Cache 2 graph variants
)
```

## Chunked Context

**What it does**: Splits long prompts into chunks to reduce memory spikes.

**Use case**: Prompts >8K tokens with limited GPU memory

**Configuration**:
```bash
trtllm-serve meta-llama/Meta-Llama-3-8B \
    --max_num_tokens 4096 \
    --enable_chunked_context \
    --max_chunked_prefill_length 2048  # Process 2K tokens at a time
```

## Overlap Scheduling

**What it does**: Overlaps compute and memory operations.

**Benefits**:
- 15-25% higher throughput
- Better GPU utilization
- Default in v1.2.0+

**No configuration needed** - enabled automatically.

## Quantization Comparison Table

| Method | Memory | Speed | Accuracy | Use Case |
|--------|--------|-------|----------|----------|
| FP16 | 1× (baseline) | 1× | Best | High accuracy needed |
| FP8 | 0.5× | 2× | -0.5% ppl | **H100 default** |
| INT4 AWQ | 0.25× | 3-4× | -1.5% ppl | Memory critical |
| INT4 GPTQ | 0.25× | 3-4× | -2% ppl | Maximum speed |

## Tuning Workflow

1. **Start with defaults**:
   ```python
   llm = LLM(model="meta-llama/Meta-Llama-3-70B")
   ```

2. **Enable FP8** (if H100):
   ```python
   llm = LLM(model="...", dtype="fp8")
   ```

3. **Tune batch size**:
   ```python
   # Increase until OOM, then reduce 20%
   trtllm-serve ... --max_batch_size 256
   ```

4. **Enable chunked context** (if long prompts):
   ```bash
   --enable_chunked_context --max_chunked_prefill_length 2048
   ```

5. **Try speculative decoding** (if latency critical):
   ```python
   llm = LLM(model="...", speculative_model="...")
   ```

## Benchmarking

```bash
# Install benchmark tool
pip install tensorrt_llm[benchmark]

# Run benchmark
python benchmarks/python/benchmark.py \
    --model meta-llama/Meta-Llama-3-8B \
    --batch_size 64 \
    --input_len 128 \
    --output_len 256 \
    --dtype fp8
```

**Metrics to track**:
- Throughput (tokens/sec)
- Latency P50/P90/P99 (ms)
- GPU memory usage (GB)
- GPU utilization (%)

## Common Issues

**OOM errors**:
- Reduce `max_batch_size`
- Reduce `max_num_tokens`
- Enable INT4 quantization
- Increase `tensor_parallel_size`

**Low throughput**:
- Increase `max_batch_size`
- Enable in-flight batching
- Verify CUDA graphs enabled
- Check GPU utilization

**High latency**:
- Try speculative decoding
- Reduce `max_batch_size` (less queueing)
- Use FP8 instead of FP16




### Serving

# Production Serving Guide

Comprehensive guide to deploying TensorRT-LLM in production environments.

## Server Modes

### trtllm-serve (Recommended)

**Features**:
- OpenAI-compatible API
- Automatic model download and compilation
- Built-in load balancing
- Prometheus metrics
- Health checks

**Basic usage**:
```bash
trtllm-serve meta-llama/Meta-Llama-3-8B \
    --tp_size 1 \
    --max_batch_size 256 \
    --port 8000
```

**Advanced configuration**:
```bash
trtllm-serve meta-llama/Meta-Llama-3-70B \
    --tp_size 4 \
    --dtype fp8 \
    --max_batch_size 256 \
    --max_num_tokens 4096 \
    --enable_chunked_context \
    --scheduler_policy max_utilization \
    --port 8000 \
    --api_key $API_KEY  # Optional authentication
```

### Python LLM API (For embedding)

```python
from tensorrt_llm import LLM

class LLMService:
    def __init__(self):
        self.llm = LLM(
            model="meta-llama/Meta-Llama-3-8B",
            dtype="fp8"
        )

    def generate(self, prompt, max_tokens=100):
        from tensorrt_llm import SamplingParams

        params = SamplingParams(
            max_tokens=max_tokens,
            temperature=0.7
        )
        outputs = self.llm.generate([prompt], params)
        return outputs[0].text

# Use in FastAPI, Flask, etc
from fastapi import FastAPI
app = FastAPI()
service = LLMService()

@app.post("/generate")
def generate(prompt: str):
    return {"response": service.generate(prompt)}
```

## OpenAI-Compatible API

### Chat Completions

```bash
curl -X POST http://localhost:8000/v1/chat/completions \
  -H "Content-Type: application/json" \
  -d '{
    "model": "meta-llama/Meta-Llama-3-8B",
    "messages": [
      {"role": "system", "content": "You are a helpful assistant."},
      {"role": "user", "content": "Explain quantum computing"}
    ],
    "temperature": 0.7,
    "max_tokens": 500,
    "stream": false
  }'
```

**Response**:
```json
{
  "id": "chat-abc123",
  "object": "chat.completion",
  "created": 1234567890,
  "model": "meta-llama/Meta-Llama-3-8B",
  "choices": [{
    "index": 0,
    "message": {
      "role": "assistant",
      "content": "Quantum computing is..."
    },
    "finish_reason": "stop"
  }],
  "usage": {
    "prompt_tokens": 25,
    "completion_tokens": 150,
    "total_tokens": 175
  }
}
```

### Streaming

```bash
curl -X POST http://localhost:8000/v1/chat/completions \
  -H "Content-Type: application/json" \
  -d '{
    "model": "meta-llama/Meta-Llama-3-8B",
    "messages": [{"role": "user", "content": "Count to 10"}],
    "stream": true
  }'
```

**Response** (SSE stream):
```
data: {"choices":[{"delta":{"content":"1"}}]}

data: {"choices":[{"delta":{"content":", 2"}}]}

data: {"choices":[{"delta":{"content":", 3"}}]}

data: [DONE]
```

### Completions

```bash
curl -X POST http://localhost:8000/v1/completions \
  -H "Content-Type: application/json" \
  -d '{
    "model": "meta-llama/Meta-Llama-3-8B",
    "prompt": "The capital of France is",
    "max_tokens": 10,
    "temperature": 0.0
  }'
```

## Monitoring

### Prometheus Metrics

**Enable metrics**:
```bash
trtllm-serve meta-llama/Meta-Llama-3-8B \
    --enable_metrics \
    --metrics_port 9090
```

**Key metrics**:
```bash
# Scrape metrics
curl http://localhost:9090/metrics

# Important metrics:
# - trtllm_request_success_total - Total successful requests
# - trtllm_request_latency_seconds - Request latency histogram
# - trtllm_tokens_generated_total - Total tokens generated
# - trtllm_active_requests - Current active requests
# - trtllm_queue_size - Requests waiting in queue
# - trtllm_gpu_memory_usage_bytes - GPU memory usage
# - trtllm_kv_cache_usage_ratio - KV cache utilization
```

### Health Checks

```bash
# Readiness probe
curl http://localhost:8000/health/ready

# Liveness probe
curl http://localhost:8000/health/live

# Model info
curl http://localhost:8000/v1/models
```

**Kubernetes probes**:
```yaml
livenessProbe:
  httpGet:
    path: /health/live
    port: 8000
  initialDelaySeconds: 60
  periodSeconds: 10

readinessProbe:
  httpGet:
    path: /health/ready
    port: 8000
  initialDelaySeconds: 30
  periodSeconds: 5
```

## Production Deployment

### Docker Deployment

**Dockerfile**:
```dockerfile
FROM nvidia/tensorrt_llm:latest

# Copy any custom configs
COPY config.yaml /app/config.yaml

# Expose ports
EXPOSE 8000 9090

# Start server
CMD ["trtllm-serve", "meta-llama/Meta-Llama-3-8B", \
     "--tp_size", "4", \
     "--dtype", "fp8", \
     "--max_batch_size", "256", \
     "--enable_metrics", \
     "--metrics_port", "9090"]
```

**Run container**:
```bash
docker run --gpus all -p 8000:8000 -p 9090:9090 \
    tensorrt-llm:latest
```

### Kubernetes Deployment

**Complete deployment**:
```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: tensorrt-llm
spec:
  replicas: 2  # Multiple replicas for HA
  selector:
    matchLabels:
      app: tensorrt-llm
  template:
    metadata:
      labels:
        app: tensorrt-llm
    spec:
      containers:
      - name: trtllm
        image: nvidia/tensorrt_llm:latest
        command:
          - trtllm-serve
          - meta-llama/Meta-Llama-3-70B
          - --tp_size=4
          - --dtype=fp8
          - --max_batch_size=256
          - --enable_metrics
        ports:
        - containerPort: 8000
          name: http
        - containerPort: 9090
          name: metrics
        resources:
          limits:
            nvidia.com/gpu: 4
        livenessProbe:
          httpGet:
            path: /health/live
            port: 8000
        readinessProbe:
          httpGet:
            path: /health/ready
            port: 8000
---
apiVersion: v1
kind: Service
metadata:
  name: tensorrt-llm
spec:
  selector:
    app: tensorrt-llm
  ports:
  - name: http
    port: 80
    targetPort: 8000
  - name: metrics
    port: 9090
    targetPort: 9090
  type: LoadBalancer
```

### Load Balancing

**NGINX configuration**:
```nginx
upstream tensorrt_llm {
    least_conn;  # Route to least busy server
    server trtllm-1:8000 max_fails=3 fail_timeout=30s;
    server trtllm-2:8000 max_fails=3 fail_timeout=30s;
    server trtllm-3:8000 max_fails=3 fail_timeout=30s;
}

server {
    listen 80;
    location / {
        proxy_pass http://tensorrt_llm;
        proxy_read_timeout 300s;  # Long timeout for slow generations
        proxy_connect_timeout 10s;
    }
}
```

## Autoscaling

### Horizontal Pod Autoscaler (HPA)

```yaml
apiVersion: autoscaling/v2
kind: HorizontalPodAutoscaler
metadata:
  name: tensorrt-llm-hpa
spec:
  scaleTargetRef:
    apiVersion: apps/v1
    kind: Deployment
    name: tensorrt-llm
  minReplicas: 2
  maxReplicas: 10
  metrics:
  - type: Pods
    pods:
      metric:
        name: trtllm_active_requests
      target:
        type: AverageValue
        averageValue: "50"  # Scale when avg >50 active requests
```

### Custom Metrics

```yaml
# Scale based on queue size
- type: Pods
  pods:
    metric:
      name: trtllm_queue_size
    target:
      type: AverageValue
      averageValue: "10"
```

## Cost Optimization

### GPU Selection

**A100 80GB** ($3-4/hour):
- Use for: 70B models with FP8
- Throughput: 10,000-15,000 tok/s (TP=4)
- Cost per 1M tokens: $0.20-0.30

**H100 80GB** ($6-8/hour):
- Use for: 70B models with FP8, 405B models
- Throughput: 20,000-30,000 tok/s (TP=4)
- Cost per 1M tokens: $0.15-0.25 (2× faster = lower cost)

**L4** ($0.50-1/hour):
- Use for: 7-8B models
- Throughput: 1,000-2,000 tok/s
- Cost per 1M tokens: $0.25-0.50

### Batch Size Tuning

**Impact on cost**:
- Batch size 1: 1,000 tok/s → $3/hour per 1M = $3/M tokens
- Batch size 64: 5,000 tok/s → $3/hour per 5M = $0.60/M tokens
- **5× cost reduction** with batching

**Recommendation**: Target batch size 32-128 for cost efficiency.

## Security

### API Authentication

```bash
# Generate API key
export API_KEY=$(openssl rand -hex 32)

# Start server with authentication
trtllm-serve meta-llama/Meta-Llama-3-8B \
    --api_key $API_KEY

# Client request
curl -X POST http://localhost:8000/v1/chat/completions \
  -H "Authorization: Bearer $API_KEY" \
  -H "Content-Type: application/json" \
  -d '{"model": "...", "messages": [...]}'
```

### Network Policies

```yaml
apiVersion: networking.k8s.io/v1
kind: NetworkPolicy
metadata:
  name: tensorrt-llm-policy
spec:
  podSelector:
    matchLabels:
      app: tensorrt-llm
  policyTypes:
  - Ingress
  ingress:
  - from:
    - podSelector:
        matchLabels:
          app: api-gateway  # Only allow from gateway
    ports:
    - protocol: TCP
      port: 8000
```

## Troubleshooting

### High latency

**Diagnosis**:
```bash
# Check queue size
curl http://localhost:9090/metrics | grep queue_size

# Check active requests
curl http://localhost:9090/metrics | grep active_requests
```

**Solutions**:
- Scale horizontally (more replicas)
- Increase batch size (if GPU underutilized)
- Enable chunked context (if long prompts)
- Use FP8 quantization

### OOM crashes

**Solutions**:
- Reduce `max_batch_size`
- Reduce `max_num_tokens`
- Enable FP8 or INT4 quantization
- Increase `tensor_parallel_size`

### Timeout errors

**NGINX config**:
```nginx
proxy_read_timeout 600s;  # 10 minutes for very long generations
proxy_send_timeout 600s;
```

## Best Practices

1. **Use FP8 on H100** for 2× speedup and 50% cost reduction
2. **Monitor metrics** - Set up Prometheus + Grafana
3. **Set readiness probes** - Prevent routing to unhealthy pods
4. **Use load balancing** - Distribute load across replicas
5. **Tune batch size** - Balance latency and throughput
6. **Enable streaming** - Better UX for chat applications
7. **Set up autoscaling** - Handle traffic spikes
8. **Use persistent volumes** - Cache compiled models
9. **Implement retries** - Handle transient failures
10. **Monitor costs** - Track cost per token




### Multi Gpu

# Multi-GPU Deployment Guide

Comprehensive guide to scaling TensorRT-LLM across multiple GPUs and nodes.

## Parallelism Strategies

### Tensor Parallelism (TP)

**What it does**: Splits model layers across GPUs horizontally.

**Use case**:
- Model fits in total GPU memory but not single GPU
- Need low latency (single forward pass)
- GPUs on same node (NVLink required for best performance)

**Example** (Llama 3-70B on 4× A100):
```python
from tensorrt_llm import LLM

llm = LLM(
    model="meta-llama/Meta-Llama-3-70B",
    tensor_parallel_size=4,  # Split across 4 GPUs
    dtype="fp16"
)

# Model automatically sharded across GPUs
# Single forward pass, low latency
```

**Performance**:
- Latency: ~Same as single GPU
- Throughput: 4× higher (4 GPUs)
- Communication: High (activations synced every layer)

### Pipeline Parallelism (PP)

**What it does**: Splits model layers across GPUs vertically (layer-wise).

**Use case**:
- Very large models (175B+)
- Can tolerate higher latency
- GPUs across multiple nodes

**Example** (Llama 3-405B on 8× H100):
```python
llm = LLM(
    model="meta-llama/Meta-Llama-3-405B",
    tensor_parallel_size=4,   # TP=4 within nodes
    pipeline_parallel_size=2, # PP=2 across nodes
    dtype="fp8"
)

# Total: 8 GPUs (4×2)
# Layers 0-40: Node 1 (4 GPUs with TP)
# Layers 41-80: Node 2 (4 GPUs with TP)
```

**Performance**:
- Latency: Higher (sequential through pipeline)
- Throughput: High with micro-batching
- Communication: Lower than TP

### Expert Parallelism (EP)

**What it does**: Distributes MoE experts across GPUs.

**Use case**: Mixture-of-Experts models (Mixtral, DeepSeek-V2)

**Example** (Mixtral-8x22B on 8× A100):
```python
llm = LLM(
    model="mistralai/Mixtral-8x22B",
    tensor_parallel_size=4,
    expert_parallel_size=2,  # Distribute 8 experts across 2 groups
    dtype="fp8"
)
```

## Configuration Examples

### Small model (7-13B) - Single GPU

```python
# Llama 3-8B on 1× A100 80GB
llm = LLM(
    model="meta-llama/Meta-Llama-3-8B",
    dtype="fp16"  # or fp8 for H100
)
```

**Resources**:
- GPU: 1× A100 80GB
- Memory: ~16GB model + 30GB KV cache
- Throughput: 3,000-5,000 tokens/sec

### Medium model (70B) - Multi-GPU same node

```python
# Llama 3-70B on 4× A100 80GB (NVLink)
llm = LLM(
    model="meta-llama/Meta-Llama-3-70B",
    tensor_parallel_size=4,
    dtype="fp8"  # 70GB → 35GB per GPU
)
```

**Resources**:
- GPU: 4× A100 80GB with NVLink
- Memory: ~35GB per GPU (FP8)
- Throughput: 10,000-15,000 tokens/sec
- Latency: 15-20ms per token

### Large model (405B) - Multi-node

```python
# Llama 3-405B on 2 nodes × 8 H100 = 16 GPUs
llm = LLM(
    model="meta-llama/Meta-Llama-3-405B",
    tensor_parallel_size=8,    # TP within each node
    pipeline_parallel_size=2,  # PP across 2 nodes
    dtype="fp8"
)
```

**Resources**:
- GPU: 2 nodes × 8 H100 80GB
- Memory: ~25GB per GPU (FP8)
- Throughput: 20,000-30,000 tokens/sec
- Network: InfiniBand recommended

## Server Deployment

### Single-node multi-GPU

```bash
# Llama 3-70B on 4 GPUs (automatic TP)
trtllm-serve meta-llama/Meta-Llama-3-70B \
    --tp_size 4 \
    --max_batch_size 256 \
    --dtype fp8

# Listens on http://localhost:8000
```

### Multi-node with Ray

```bash
# Node 1 (head node)
ray start --head --port=6379

# Node 2 (worker)
ray start --address='node1:6379'

# Deploy across cluster
trtllm-serve meta-llama/Meta-Llama-3-405B \
    --tp_size 8 \
    --pp_size 2 \
    --num_workers 2 \  # 2 nodes
    --dtype fp8
```

### Kubernetes deployment

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: tensorrt-llm-llama3-70b
spec:
  replicas: 1
  template:
    spec:
      containers:
      - name: trtllm
        image: nvidia/tensorrt_llm:latest
        command:
          - trtllm-serve
          - meta-llama/Meta-Llama-3-70B
          - --tp_size=4
          - --max_batch_size=256
        resources:
          limits:
            nvidia.com/gpu: 4  # Request 4 GPUs
```

## Parallelism Decision Tree

```
Model size < 20GB?
├─ YES: Single GPU (no parallelism)
└─ NO: Model size < 80GB?
    ├─ YES: TP=2 or TP=4 (same node)
    └─ NO: Model size < 320GB?
        ├─ YES: TP=4 or TP=8 (same node, NVLink required)
        └─ NO: TP=8 + PP=2 (multi-node)
```

## Communication Optimization

### NVLink vs PCIe

**NVLink** (DGX A100, HGX H100):
- Bandwidth: 600 GB/s (A100), 900 GB/s (H100)
- Ideal for TP (high communication)
- **Recommended for all multi-GPU setups**

**PCIe**:
- Bandwidth: 64 GB/s (PCIe 4.0 x16)
- 10× slower than NVLink
- Avoid TP, use PP instead

### InfiniBand for multi-node

**HDR InfiniBand** (200 Gb/s):
- Required for multi-node TP or PP
- Latency: <1μs
- **Essential for 405B+ models**

## Monitoring Multi-GPU

```python
# Monitor GPU utilization
nvidia-smi dmon -s u

# Monitor memory
nvidia-smi dmon -s m

# Monitor NVLink utilization
nvidia-smi nvlink --status

# TensorRT-LLM built-in metrics
curl http://localhost:8000/metrics
```

**Key metrics**:
- GPU utilization: Target 80-95%
- Memory usage: Should be balanced across GPUs
- NVLink traffic: High for TP, low for PP
- Throughput: Tokens/sec across all GPUs

## Common Issues

### Imbalanced GPU memory

**Symptom**: GPU 0 has 90% memory, GPU 3 has 40%

**Solutions**:
- Verify TP/PP configuration
- Check model sharding (should be equal)
- Restart server to reset state

### Low NVLink utilization

**Symptom**: NVLink bandwidth <100 GB/s with TP=4

**Solutions**:
- Verify NVLink topology: `nvidia-smi topo -m`
- Check for PCIe fallback
- Ensure GPUs are on same NVSwitch

### OOM with multi-GPU

**Solutions**:
- Increase TP size (more GPUs)
- Reduce batch size
- Enable FP8 quantization
- Use pipeline parallelism

## Performance Scaling

### TP Scaling (Llama 3-70B, FP8)

| GPUs | TP Size | Throughput | Latency | Efficiency |
|------|---------|------------|---------|------------|
| 1 | 1 | OOM | - | - |
| 2 | 2 | 6,000 tok/s | 18ms | 85% |
| 4 | 4 | 11,000 tok/s | 16ms | 78% |
| 8 | 8 | 18,000 tok/s | 15ms | 64% |

**Note**: Efficiency drops with more GPUs due to communication overhead.

### PP Scaling (Llama 3-405B, FP8)

| Nodes | TP | PP | Total GPUs | Throughput |
|-------|----|----|------------|------------|
| 1 | 8 | 1 | 8 | OOM |
| 2 | 8 | 2 | 16 | 25,000 tok/s |
| 4 | 8 | 4 | 32 | 45,000 tok/s |

## Best Practices

1. **Prefer TP over PP** when possible (lower latency)
2. **Use NVLink** for all TP deployments
3. **Use InfiniBand** for multi-node deployments
4. **Start with smallest TP** that fits model in memory
5. **Monitor GPU balance** - all GPUs should have similar utilization
6. **Test with benchmark** before production
7. **Use FP8** on H100 for 2× speedup




---

## 🚀 Usage

**Reference this template:** `@skill-tensorrt-llm.md`


**Platform-specific:**
- **GitHub Copilot**: Add to `.github/copilot-instructions.md`
- **Augment Code**: Use `aug context add` command
- **Cursor/Windsurf**: Reference in chat with `@filename`
- **Claude**: Add to Project Knowledge
- **ChatGPT**: Add to Custom GPT configuration
