---
id: skill-modal-serverless-gpu
type: skill
name: modal-serverless-gpu
description: Serverless GPU cloud platform for running ML workloads. Use when you
  need on-demand GPU access without infrastructure management, deploying ML models
  as APIs, or running batch jobs with automatic scaling.
category: ai-research
complexity: medium
keywords:
- api
- deploy
- deployment
- git
- github
- kubernetes
- optimization
- performance
- python
- rest
- rust
- test
capabilities: []
token_estimate: 1219
has_references: true
reference_count: 2
---

<!-- Converted from Claude Skill Template -->
<!-- Complexity: Medium -->
<!-- Estimated Tokens: ~1,219 -->
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




# modal-serverless-gpu

> Serverless GPU cloud platform for running ML workloads. Use when you need on-demand GPU access without infrastructure management, deploying ML models as APIs, or running batch jobs with automatic scaling.

# Modal Serverless GPU

Comprehensive guide to running ML workloads on Modal's serverless GPU cloud platform.

## When to use Modal

**Use Modal when:**
- Running GPU-intensive ML workloads without managing infrastructure
- Deploying ML models as auto-scaling APIs
- Running batch processing jobs (training, inference, data processing)
- Need pay-per-second GPU pricing without idle costs
- Prototyping ML applications quickly
- Running scheduled jobs (cron-like workloads)

**Key features:**
- **Serverless GPUs**: T4, L4, A10G, L40S, A100, H100, H200, B200 on-demand
- **Python-native**: Define infrastructure in Python code, no YAML
- **Auto-scaling**: Scale to zero, scale to 100+ GPUs instantly
- **Sub-second cold starts**: Rust-based infrastructure for fast container launches
- **Container caching**: Image layers cached for rapid iteration
- **Web endpoints**: Deploy functions as REST APIs with zero-downtime updates

**Use alternatives instead:**
- **RunPod**: For longer-running pods with persistent state
- **Lambda Labs**: For reserved GPU instances
- **SkyPilot**: For multi-cloud orchestration and cost optimization
- **Kubernetes**: For complex multi-service architectures

## Quick start

### Installation

```bash
pip install modal
modal setup  # Opens browser for authentication
```

### Hello World with GPU

```python
import modal

app = modal.App("hello-gpu")

@app.function(gpu="T4")
def gpu_info():
    import subprocess
    return subprocess.run(["nvidia-smi"], capture_output=True, text=True).stdout

@app.local_entrypoint()
def main():
    print(gpu_info.remote())
```

Run: `modal run hello_gpu.py`

### Basic inference endpoint

```python
import modal

app = modal.App("text-generation")
image = modal.Image.debian_slim().pip_install("transformers", "torch", "accelerate")

@app.cls(gpu="A10G", image=image)
class TextGenerator:
    @modal.enter()
    def load_model(self):
        from transformers import pipeline
        self.pipe = pipeline("text-generation", model="gpt2", device=0)

    @modal.method()
    def generate(self, prompt: str) -> str:
        return self.pipe(prompt, max_length=100)[0]["generated_text"]

@app.local_entrypoint()
def main():
    print(TextGenerator().generate.remote("Hello, world"))
```

## Core concepts

### Key components

| Component | Purpose |
|-----------|---------|
| `App` | Container for functions and resources |
| `Function` | Serverless function with compute specs |
| `Cls` | Class-based functions with lifecycle hooks |
| `Image` | Container image definition |
| `Volume` | Persistent storage for models/data |
| `Secret` | Secure credential storage |

### Execution modes

| Command | Description |
|---------|-------------|
| `modal run script.py` | Execute and exit |
| `modal serve script.py` | Development with live reload |
| `modal deploy script.py` | Persistent cloud deployment |

## GPU configuration

### Available GPUs

| GPU | VRAM | Best For |
|-----|------|----------|
| `T4` | 16GB | Budget inference, small models |
| `L4` | 24GB | Inference, Ada Lovelace arch |
| `A10G` | 24GB | Training/inference, 3.3x faster than T4 |
| `L40S` | 48GB | Recommended for inference (best cost/perf) |
| `A100-40GB` | 40GB | Large model training |
| `A100-80GB` | 80GB | Very large models |
| `H100` | 80GB | Fastest, FP8 + Transformer Engine |
| `H200` | 141GB | Auto-upgrade from H100, 4.8TB/s bandwidth |
| `B200` | Latest | Blackwell architecture |

### GPU specification patterns

```python
# Single GPU
@app.function(gpu="A100")

# Specific memory variant
@app.function(gpu="A100-80GB")

# Multiple GPUs (up to 8)
@app.function(gpu="H100:4")

# GPU with fallbacks
@app.function(gpu=["H100", "A100", "L40S"])

# Any available GPU
@app.function(gpu="any")
```

## Container images

```python
# Basic image with pip
image = modal.Image.debian_slim(python_version="3.11").pip_install(
    "torch==2.1.0", "transformers==4.36.0", "accelerate"
)

# From CUDA base
image = modal.Image.from_registry(
    "nvidia/cuda:12.1.0-cudnn8-devel-ubuntu22.04",
    add_python="3.11"
).pip_install("torch", "transformers")

# With system packages
image = modal.Image.debian_slim().apt_install("git", "ffmpeg").pip_install("whisper")
```

## Persistent storage

```python
volume = modal.Volume.from_name("model-cache", create_if_missing=True)

@app.function(gpu="A10G", volumes={"/models": volume})
def load_model():
    import os
    model_path = "/models/llama-7b"
    if not os.path.exists(model_path):
        model = download_model()
        model.save_pretrained(model_path)
        volume.commit()  # Persist changes
    return load_from_path(model_path)
```

## Web endpoints

### FastAPI endpoint decorator

```python
@app.function()
@modal.fastapi_endpoint(method="POST")
def predict(text: str) -> dict:
    return {"result": model.predict(text)}
```

### Full ASGI app

```python
from fastapi import FastAPI
web_app = FastAPI()

@web_app.post("/predict")
async def predict(text: str):
    return {"result": await model.predict.remote.aio(text)}

@app.function()
@modal.asgi_app()
def fastapi_app():
    return web_app
```

### Web endpoint types

| Decorator | Use Case |
|-----------|----------|
| `@modal.fastapi_endpoint()` | Simple function → API |
| `@modal.asgi_app()` | Full FastAPI/Starlette apps |
| `@modal.wsgi_app()` | Django/Flask apps |
| `@modal.web_server(port)` | Arbitrary HTTP servers |

## Dynamic batching

```python
@app.function()
@modal.batched(max_batch_size=32, wait_ms=100)
async def batch_predict(inputs: list[str]) -> list[dict]:
    # Inputs automatically batched
    return model.batch_predict(inputs)
```

## Secrets management

```bash
# Create secret
modal secret create huggingface HF_TOKEN=hf_xxx
```

```python
@app.function(secrets=[modal.Secret.from_name("huggingface")])
def download_model():
    import os
    token = os.environ["HF_TOKEN"]
```

## Scheduling

```python
@app.function(schedule=modal.Cron("0 0 * * *"))  # Daily midnight
def daily_job():
    pass

@app.function(schedule=modal.Period(hours=1))
def hourly_job():
    pass
```

## Performance optimization

### Cold start mitigation

```python
@app.function(
    container_idle_timeout=300,  # Keep warm 5 min
    allow_concurrent_inputs=10,  # Handle concurrent requests
)
def inference():
    pass
```

### Model loading best practices

```python
@app.cls(gpu="A100")
class Model:
    @modal.enter()  # Run once at container start
    def load(self):
        self.model = load_model()  # Load during warm-up

    @modal.method()
    def predict(self, x):
        return self.model(x)
```

## Parallel processing

```python
@app.function()
def process_item(item):
    return expensive_computation(item)

@app.function()
def run_parallel():
    items = list(range(1000))
    # Fan out to parallel containers
    results = list(process_item.map(items))
    return results
```

## Common configuration

```python
@app.function(
    gpu="A100",
    memory=32768,              # 32GB RAM
    cpu=4,                     # 4 CPU cores
    timeout=3600,              # 1 hour max
    container_idle_timeout=120,# Keep warm 2 min
    retries=3,                 # Retry on failure
    concurrency_limit=10,      # Max concurrent containers
)
def my_function():
    pass
```

## Debugging

```python
# Test locally
if __name__ == "__main__":
    result = my_function.local()

# View logs
# modal app logs my-app
```

## Common issues

| Issue | Solution |
|-------|----------|
| Cold start latency | Increase `container_idle_timeout`, use `@modal.enter()` |
| GPU OOM | Use larger GPU (`A100-80GB`), enable gradient checkpointing |
| Image build fails | Pin dependency versions, check CUDA compatibility |
| Timeout errors | Increase `timeout`, add checkpointing |

## References

- **[Advanced Usage](references/advanced-usage.md)** - Multi-GPU, distributed training, cost optimization
- **[Troubleshooting](references/troubleshooting.md)** - Common issues and solutions

## Resources

- **Documentation**: https://modal.com/docs
- **Examples**: https://github.com/modal-labs/modal-examples
- **Pricing**: https://modal.com/pricing
- **Discord**: https://discord.gg/modal


---


## 📚 Reference Materials


### Advanced Usage

# Modal Advanced Usage Guide

## Multi-GPU Training

### Single-node multi-GPU

```python
import modal

app = modal.App("multi-gpu-training")
image = modal.Image.debian_slim().pip_install("torch", "transformers", "accelerate")

@app.function(gpu="H100:4", image=image, timeout=7200)
def train_multi_gpu():
    from accelerate import Accelerator

    accelerator = Accelerator()
    model, optimizer, dataloader = accelerator.prepare(model, optimizer, dataloader)

    for batch in dataloader:
        outputs = model(**batch)
        loss = outputs.loss
        accelerator.backward(loss)
        optimizer.step()
```

### DeepSpeed integration

```python
image = modal.Image.debian_slim().pip_install(
    "torch", "transformers", "deepspeed", "accelerate"
)

@app.function(gpu="A100:8", image=image, timeout=14400)
def deepspeed_train(config: dict):
    from transformers import Trainer, TrainingArguments

    args = TrainingArguments(
        output_dir="/outputs",
        deepspeed="ds_config.json",
        fp16=True,
        per_device_train_batch_size=4,
        gradient_accumulation_steps=4
    )

    trainer = Trainer(model=model, args=args, train_dataset=dataset)
    trainer.train()
```

### Multi-GPU considerations

For frameworks that re-execute the Python entrypoint (like PyTorch Lightning), use:
- `ddp_spawn` or `ddp_notebook` strategy
- Run training as a subprocess to avoid issues

```python
@app.function(gpu="H100:4")
def train_with_subprocess():
    import subprocess
    subprocess.run(["python", "-m", "torch.distributed.launch", "train.py"])
```

## Advanced Container Configuration

### Multi-stage builds for caching

```python
# Stage 1: Base dependencies (cached)
base_image = modal.Image.debian_slim().pip_install("torch", "numpy", "scipy")

# Stage 2: ML libraries (cached separately)
ml_image = base_image.pip_install("transformers", "datasets", "accelerate")

# Stage 3: Custom code (rebuilt on changes)
final_image = ml_image.copy_local_dir("./src", "/app/src")
```

### Custom Dockerfiles

```python
image = modal.Image.from_dockerfile("./Dockerfile")
```

### Installing from Git

```python
image = modal.Image.debian_slim().pip_install(
    "git+https://github.com/huggingface/transformers.git@main"
)
```

### Using uv for faster installs

```python
image = modal.Image.debian_slim().uv_pip_install(
    "torch", "transformers", "accelerate"
)
```

## Advanced Class Patterns

### Lifecycle hooks

```python
@app.cls(gpu="A10G")
class InferenceService:
    @modal.enter()
    def startup(self):
        """Called once when container starts"""
        self.model = load_model()
        self.tokenizer = load_tokenizer()

    @modal.exit()
    def shutdown(self):
        """Called when container shuts down"""
        cleanup_resources()

    @modal.method()
    def predict(self, text: str):
        return self.model(self.tokenizer(text))
```

### Concurrent request handling

```python
@app.cls(
    gpu="A100",
    allow_concurrent_inputs=20,  # Handle 20 requests per container
    container_idle_timeout=300
)
class BatchInference:
    @modal.enter()
    def load(self):
        self.model = load_model()

    @modal.method()
    def predict(self, inputs: list):
        return self.model.batch_predict(inputs)
```

### Input concurrency vs batching

- **Input concurrency**: Multiple requests processed simultaneously (async I/O)
- **Dynamic batching**: Requests accumulated and processed together (GPU efficiency)

```python
# Input concurrency - good for I/O-bound
@app.function(allow_concurrent_inputs=10)
async def fetch_data(url: str):
    async with aiohttp.ClientSession() as session:
        return await session.get(url)

# Dynamic batching - good for GPU inference
@app.function()
@modal.batched(max_batch_size=32, wait_ms=100)
async def batch_embed(texts: list[str]) -> list[list[float]]:
    return model.encode(texts)
```

## Advanced Volumes

### Volume operations

```python
volume = modal.Volume.from_name("my-volume", create_if_missing=True)

@app.function(volumes={"/data": volume})
def volume_operations():
    import os

    # Write data
    with open("/data/output.txt", "w") as f:
        f.write("Results")

    # Commit changes (persist to volume)
    volume.commit()

    # Reload from remote (get latest)
    volume.reload()
```

### Shared volumes between functions

```python
shared_volume = modal.Volume.from_name("shared-data", create_if_missing=True)

@app.function(volumes={"/shared": shared_volume})
def writer():
    with open("/shared/data.txt", "w") as f:
        f.write("Hello from writer")
    shared_volume.commit()

@app.function(volumes={"/shared": shared_volume})
def reader():
    shared_volume.reload()  # Get latest
    with open("/shared/data.txt", "r") as f:
        return f.read()
```

### Cloud bucket mounts

```python
# Mount S3 bucket
bucket = modal.CloudBucketMount(
    bucket_name="my-bucket",
    secret=modal.Secret.from_name("aws-credentials")
)

@app.function(volumes={"/s3": bucket})
def process_s3_data():
    # Access S3 files like local filesystem
    data = open("/s3/data.parquet").read()
```

## Function Composition

### Chaining functions

```python
@app.function()
def preprocess(data):
    return cleaned_data

@app.function(gpu="T4")
def inference(data):
    return predictions

@app.function()
def postprocess(predictions):
    return formatted_results

@app.function()
def pipeline(raw_data):
    cleaned = preprocess.remote(raw_data)
    predictions = inference.remote(cleaned)
    results = postprocess.remote(predictions)
    return results
```

### Parallel fan-out

```python
@app.function()
def process_item(item):
    return expensive_computation(item)

@app.function()
def parallel_pipeline(items):
    # Fan out: process all items in parallel
    results = list(process_item.map(items))
    return results
```

### Starmap for multiple arguments

```python
@app.function()
def process(x, y, z):
    return x + y + z

@app.function()
def orchestrate():
    args = [(1, 2, 3), (4, 5, 6), (7, 8, 9)]
    results = list(process.starmap(args))
    return results
```

## Advanced Web Endpoints

### WebSocket support

```python
from fastapi import FastAPI, WebSocket

app = modal.App("websocket-app")
web_app = FastAPI()

@web_app.websocket("/ws")
async def websocket_endpoint(websocket: WebSocket):
    await websocket.accept()
    while True:
        data = await websocket.receive_text()
        await websocket.send_text(f"Processed: {data}")

@app.function()
@modal.asgi_app()
def ws_app():
    return web_app
```

### Streaming responses

```python
from fastapi.responses import StreamingResponse

@app.function(gpu="A100")
def generate_stream(prompt: str):
    for token in model.generate_stream(prompt):
        yield token

@web_app.get("/stream")
async def stream_response(prompt: str):
    return StreamingResponse(
        generate_stream.remote_gen(prompt),
        media_type="text/event-stream"
    )
```

### Authentication

```python
from fastapi import Depends, HTTPException, Header

async def verify_token(authorization: str = Header(None)):
    if not authorization or not authorization.startswith("Bearer "):
        raise HTTPException(status_code=401)
    token = authorization.split(" ")[1]
    if not verify_jwt(token):
        raise HTTPException(status_code=403)
    return token

@web_app.post("/predict")
async def predict(data: dict, token: str = Depends(verify_token)):
    return model.predict(data)
```

## Cost Optimization

### Right-sizing GPUs

```python
# For inference: smaller GPUs often sufficient
@app.function(gpu="L40S")  # 48GB, best cost/perf for inference
def inference():
    pass

# For training: larger GPUs for throughput
@app.function(gpu="A100-80GB")
def training():
    pass
```

### GPU fallbacks for availability

```python
@app.function(gpu=["H100", "A100", "L40S"])  # Try in order
def flexible_compute():
    pass
```

### Scale to zero

```python
# Default behavior: scale to zero when idle
@app.function(gpu="A100")
def on_demand():
    pass

# Keep containers warm for low latency (costs more)
@app.function(gpu="A100", keep_warm=1)
def always_ready():
    pass
```

### Batch processing for efficiency

```python
# Process in batches to reduce cold starts
@app.function(gpu="A100")
def batch_process(items: list):
    return [process(item) for item in items]

# Better than individual calls
results = batch_process.remote(all_items)
```

## Monitoring and Observability

### Structured logging

```python
import json
import logging

logging.basicConfig(level=logging.INFO)
logger = logging.getLogger(__name__)

@app.function()
def structured_logging(request_id: str, data: dict):
    logger.info(json.dumps({
        "event": "inference_start",
        "request_id": request_id,
        "input_size": len(data)
    }))

    result = process(data)

    logger.info(json.dumps({
        "event": "inference_complete",
        "request_id": request_id,
        "output_size": len(result)
    }))

    return result
```

### Custom metrics

```python
@app.function(gpu="A100")
def monitored_inference(inputs):
    import time

    start = time.time()
    results = model.predict(inputs)
    latency = time.time() - start

    # Log metrics (visible in Modal dashboard)
    print(f"METRIC latency={latency:.3f}s batch_size={len(inputs)}")

    return results
```

## Production Deployment

### Environment separation

```python
import os

env = os.environ.get("MODAL_ENV", "dev")
app = modal.App(f"my-service-{env}")

# Environment-specific config
if env == "prod":
    gpu_config = "A100"
    timeout = 3600
else:
    gpu_config = "T4"
    timeout = 300
```

### Zero-downtime deployments

Modal automatically handles zero-downtime deployments:
1. New containers are built and started
2. Traffic gradually shifts to new version
3. Old containers drain existing requests
4. Old containers are terminated

### Health checks

```python
@app.function()
@modal.web_endpoint()
def health():
    return {
        "status": "healthy",
        "model_loaded": hasattr(Model, "_model"),
        "gpu_available": torch.cuda.is_available()
    }
```

## Sandboxes

### Interactive execution environments

```python
@app.function()
def run_sandbox():
    sandbox = modal.Sandbox.create(
        app=app,
        image=image,
        gpu="T4"
    )

    # Execute code in sandbox
    result = sandbox.exec("python", "-c", "print('Hello from sandbox')")

    sandbox.terminate()
    return result
```

## Invoking Deployed Functions

### From external code

```python
# Call deployed function from any Python script
import modal

f = modal.Function.lookup("my-app", "my_function")
result = f.remote(arg1, arg2)
```

### REST API invocation

```bash
# Deployed endpoints accessible via HTTPS
curl -X POST https://your-workspace--my-app-predict.modal.run \
  -H "Content-Type: application/json" \
  -d '{"text": "Hello world"}'
```




### Troubleshooting

# Modal Troubleshooting Guide

## Installation Issues

### Authentication fails

**Error**: `modal setup` doesn't complete or token is invalid

**Solutions**:
```bash
# Re-authenticate
modal token new

# Check current token
modal config show

# Set token via environment
export MODAL_TOKEN_ID=ak-...
export MODAL_TOKEN_SECRET=as-...
```

### Package installation issues

**Error**: `pip install modal` fails

**Solutions**:
```bash
# Upgrade pip
pip install --upgrade pip

# Install with specific Python version
python3.11 -m pip install modal

# Install from wheel
pip install modal --prefer-binary
```

## Container Image Issues

### Image build fails

**Error**: `ImageBuilderError: Failed to build image`

**Solutions**:
```python
# Pin package versions to avoid conflicts
image = modal.Image.debian_slim().pip_install(
    "torch==2.1.0",
    "transformers==4.36.0",  # Pin versions
    "accelerate==0.25.0"
)

# Use compatible CUDA versions
image = modal.Image.from_registry(
    "nvidia/cuda:12.1.0-cudnn8-runtime-ubuntu22.04",  # Match PyTorch CUDA
    add_python="3.11"
)
```

### Dependency conflicts

**Error**: `ERROR: Cannot install package due to conflicting dependencies`

**Solutions**:
```python
# Layer dependencies separately
base = modal.Image.debian_slim().pip_install("torch")
ml = base.pip_install("transformers")  # Install after torch

# Use uv for better resolution
image = modal.Image.debian_slim().uv_pip_install(
    "torch", "transformers"
)
```

### Large image builds timeout

**Error**: Image build exceeds time limit

**Solutions**:
```python
# Split into multiple layers (better caching)
base = modal.Image.debian_slim().pip_install("torch")  # Cached
ml = base.pip_install("transformers", "datasets")      # Cached
app = ml.copy_local_dir("./src", "/app")               # Rebuilds on code change

# Download models during build, not runtime
image = modal.Image.debian_slim().pip_install("transformers").run_commands(
    "python -c 'from transformers import AutoModel; AutoModel.from_pretrained(\"bert-base\")'"
)
```

## GPU Issues

### GPU not available

**Error**: `RuntimeError: CUDA not available`

**Solutions**:
```python
# Ensure GPU is specified
@app.function(gpu="T4")  # Must specify GPU
def my_function():
    import torch
    assert torch.cuda.is_available()

# Check CUDA compatibility in image
image = modal.Image.from_registry(
    "nvidia/cuda:12.1.0-cudnn8-devel-ubuntu22.04",
    add_python="3.11"
).pip_install(
    "torch",
    index_url="https://download.pytorch.org/whl/cu121"  # Match CUDA
)
```

### GPU out of memory

**Error**: `torch.cuda.OutOfMemoryError: CUDA out of memory`

**Solutions**:
```python
# Use larger GPU
@app.function(gpu="A100-80GB")  # More VRAM
def train():
    pass

# Enable memory optimization
@app.function(gpu="A100")
def memory_optimized():
    import torch
    torch.backends.cuda.enable_flash_sdp(True)

    # Use gradient checkpointing
    model.gradient_checkpointing_enable()

    # Mixed precision
    with torch.autocast(device_type="cuda", dtype=torch.float16):
        outputs = model(**inputs)
```

### Wrong GPU allocated

**Error**: Got different GPU than requested

**Solutions**:
```python
# Use strict GPU selection
@app.function(gpu="H100!")  # H100! prevents auto-upgrade to H200

# Specify exact memory variant
@app.function(gpu="A100-80GB")  # Not just "A100"

# Check GPU at runtime
@app.function(gpu="A100")
def check_gpu():
    import subprocess
    result = subprocess.run(["nvidia-smi"], capture_output=True, text=True)
    print(result.stdout)
```

## Cold Start Issues

### Slow cold starts

**Problem**: First request takes too long

**Solutions**:
```python
# Keep containers warm
@app.function(
    container_idle_timeout=600,  # Keep warm 10 min
    keep_warm=1                  # Always keep 1 container ready
)
def low_latency():
    pass

# Load model during container start
@app.cls(gpu="A100")
class Model:
    @modal.enter()
    def load(self):
        # This runs once at container start, not per request
        self.model = load_heavy_model()

# Cache model in volume
volume = modal.Volume.from_name("models", create_if_missing=True)

@app.function(volumes={"/cache": volume})
def cached_model():
    if os.path.exists("/cache/model"):
        model = load_from_disk("/cache/model")
    else:
        model = download_model()
        save_to_disk(model, "/cache/model")
        volume.commit()
```

### Container keeps restarting

**Problem**: Containers are killed and restarted frequently

**Solutions**:
```python
# Increase memory
@app.function(memory=32768)  # 32GB RAM
def memory_heavy():
    pass

# Increase timeout
@app.function(timeout=3600)  # 1 hour
def long_running():
    pass

# Handle signals gracefully
import signal

def handler(signum, frame):
    cleanup()
    exit(0)

signal.signal(signal.SIGTERM, handler)
```

## Volume Issues

### Volume changes not persisting

**Error**: Data written to volume disappears

**Solutions**:
```python
volume = modal.Volume.from_name("my-volume", create_if_missing=True)

@app.function(volumes={"/data": volume})
def write_data():
    with open("/data/file.txt", "w") as f:
        f.write("data")

    # CRITICAL: Commit changes!
    volume.commit()
```

### Volume read shows stale data

**Error**: Reading outdated data from volume

**Solutions**:
```python
@app.function(volumes={"/data": volume})
def read_data():
    # Reload to get latest
    volume.reload()

    with open("/data/file.txt", "r") as f:
        return f.read()
```

### Volume mount fails

**Error**: `VolumeError: Failed to mount volume`

**Solutions**:
```python
# Ensure volume exists
volume = modal.Volume.from_name("my-volume", create_if_missing=True)

# Use absolute path
@app.function(volumes={"/data": volume})  # Not "./data"
def my_function():
    pass

# Check volume in dashboard
# modal volume list
```

## Web Endpoint Issues

### Endpoint returns 502

**Error**: Gateway timeout or bad gateway

**Solutions**:
```python
# Increase timeout
@app.function(timeout=300)  # 5 min
@modal.web_endpoint()
def slow_endpoint():
    pass

# Return streaming response for long operations
from fastapi.responses import StreamingResponse

@app.function()
@modal.asgi_app()
def streaming_app():
    async def generate():
        for i in range(100):
            yield f"data: {i}\n\n"
            await process_chunk(i)
    return StreamingResponse(generate(), media_type="text/event-stream")
```

### Endpoint not accessible

**Error**: 404 or cannot reach endpoint

**Solutions**:
```bash
# Check deployment status
modal app list

# Redeploy
modal deploy my_app.py

# Check logs
modal app logs my-app
```

### CORS errors

**Error**: Cross-origin request blocked

**Solutions**:
```python
from fastapi import FastAPI
from fastapi.middleware.cors import CORSMiddleware

web_app = FastAPI()
web_app.add_middleware(
    CORSMiddleware,
    allow_origins=["*"],
    allow_credentials=True,
    allow_methods=["*"],
    allow_headers=["*"],
)

@app.function()
@modal.asgi_app()
def cors_enabled():
    return web_app
```

## Secret Issues

### Secret not found

**Error**: `SecretNotFound: Secret 'my-secret' not found`

**Solutions**:
```bash
# Create secret via CLI
modal secret create my-secret KEY=value

# List secrets
modal secret list

# Check secret name matches exactly
```

### Secret value not accessible

**Error**: Environment variable is empty

**Solutions**:
```python
# Ensure secret is attached
@app.function(secrets=[modal.Secret.from_name("my-secret")])
def use_secret():
    import os
    value = os.environ.get("KEY")  # Use get() to handle missing
    if not value:
        raise ValueError("KEY not set in secret")
```

## Scheduling Issues

### Scheduled job not running

**Error**: Cron job doesn't execute

**Solutions**:
```python
# Verify cron syntax
@app.function(schedule=modal.Cron("0 0 * * *"))  # Daily at midnight UTC
def daily_job():
    pass

# Check timezone (Modal uses UTC)
# "0 8 * * *" = 8am UTC, not local time

# Ensure app is deployed
# modal deploy my_app.py
```

### Job runs multiple times

**Problem**: Scheduled job executes more than expected

**Solutions**:
```python
# Implement idempotency
@app.function(schedule=modal.Cron("0 * * * *"))
def hourly_job():
    job_id = get_current_hour_id()
    if already_processed(job_id):
        return
    process()
    mark_processed(job_id)
```

## Debugging Tips

### Enable debug logging

```python
import logging
logging.basicConfig(level=logging.DEBUG)

@app.function()
def debug_function():
    logging.debug("Debug message")
    logging.info("Info message")
```

### View container logs

```bash
# Stream logs
modal app logs my-app

# View specific function
modal app logs my-app --function my_function

# View historical logs
modal app logs my-app --since 1h
```

### Test locally

```python
# Run function locally without Modal
if __name__ == "__main__":
    result = my_function.local()  # Runs on your machine
    print(result)
```

### Inspect container

```python
@app.function(gpu="T4")
def debug_environment():
    import subprocess
    import sys

    # System info
    print(f"Python: {sys.version}")
    print(subprocess.run(["nvidia-smi"], capture_output=True, text=True).stdout)
    print(subprocess.run(["pip", "list"], capture_output=True, text=True).stdout)

    # CUDA info
    import torch
    print(f"CUDA available: {torch.cuda.is_available()}")
    print(f"CUDA version: {torch.version.cuda}")
    print(f"GPU: {torch.cuda.get_device_name(0)}")
```

## Common Error Messages

| Error | Cause | Solution |
|-------|-------|----------|
| `FunctionTimeoutError` | Function exceeded timeout | Increase `timeout` parameter |
| `ContainerMemoryExceeded` | OOM killed | Increase `memory` parameter |
| `ImageBuilderError` | Build failed | Check dependencies, pin versions |
| `ResourceExhausted` | No GPUs available | Use GPU fallbacks, try later |
| `AuthenticationError` | Invalid token | Run `modal token new` |
| `VolumeNotFound` | Volume doesn't exist | Use `create_if_missing=True` |
| `SecretNotFound` | Secret doesn't exist | Create secret via CLI |

## Getting Help

1. **Documentation**: https://modal.com/docs
2. **Examples**: https://github.com/modal-labs/modal-examples
3. **Discord**: https://discord.gg/modal
4. **Status**: https://status.modal.com

### Reporting Issues

Include:
- Modal client version: `modal --version`
- Python version: `python --version`
- Full error traceback
- Minimal reproducible code
- GPU type if relevant




---

## 🚀 Usage

**Reference this template:** `@skill-modal-serverless-gpu.md`


**Platform-specific:**
- **GitHub Copilot**: Add to `.github/copilot-instructions.md`
- **Augment Code**: Use `aug context add` command
- **Cursor/Windsurf**: Reference in chat with `@filename`
- **Claude**: Add to Project Knowledge
- **ChatGPT**: Add to Custom GPT configuration
