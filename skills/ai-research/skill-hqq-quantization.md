---
id: skill-hqq-quantization
type: skill
name: hqq-quantization
description: Half-Quadratic Quantization for LLMs without calibration data. Use when
  quantizing models to 4/3/2-bit precision without needing calibration datasets, for
  fast quantization workflows, or when deploying with vLLM or HuggingFace Transformers.
category: ai-research
complexity: medium
keywords:
- benchmark
- deployment
- github
- optimization
- python
- test
capabilities: []
token_estimate: 1513
has_references: true
reference_count: 2
---

<!-- Converted from Claude Skill Template -->
<!-- Complexity: Medium -->
<!-- Estimated Tokens: ~1,513 -->
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




# hqq-quantization

> Half-Quadratic Quantization for LLMs without calibration data. Use when quantizing models to 4/3/2-bit precision without needing calibration datasets, for fast quantization workflows, or when deploying with vLLM or HuggingFace Transformers.

# HQQ - Half-Quadratic Quantization

Fast, calibration-free weight quantization supporting 8/4/3/2/1-bit precision with multiple optimized backends.

## When to use HQQ

**Use HQQ when:**
- Quantizing models without calibration data (no dataset needed)
- Need fast quantization (minutes vs hours for GPTQ/AWQ)
- Deploying with vLLM or HuggingFace Transformers
- Fine-tuning quantized models with LoRA/PEFT
- Experimenting with extreme quantization (2-bit, 1-bit)

**Key advantages:**
- **No calibration**: Quantize any model instantly without sample data
- **Multiple backends**: PyTorch, ATEN, TorchAO, Marlin, BitBlas for optimized inference
- **Flexible precision**: 8/4/3/2/1-bit with configurable group sizes
- **Framework integration**: Native HuggingFace and vLLM support
- **PEFT compatible**: Fine-tune quantized models with LoRA

**Use alternatives instead:**
- **AWQ**: Need calibration-based accuracy, production serving
- **GPTQ**: Maximum accuracy with calibration data available
- **bitsandbytes**: Simple 8-bit/4-bit without custom backends
- **llama.cpp/GGUF**: CPU inference, Apple Silicon deployment

## Quick start

### Installation

```bash
pip install hqq

# With specific backend
pip install hqq[torch]      # PyTorch backend
pip install hqq[torchao]    # TorchAO int4 backend
pip install hqq[bitblas]    # BitBlas backend
pip install hqq[marlin]     # Marlin backend
```

### Basic quantization

```python
from hqq.core.quantize import BaseQuantizeConfig, HQQLinear
import torch.nn as nn

# Configure quantization
config = BaseQuantizeConfig(
    nbits=4,           # 4-bit quantization
    group_size=64,     # Group size for quantization
    axis=1             # Quantize along output dimension
)

# Quantize a linear layer
linear = nn.Linear(4096, 4096)
hqq_linear = HQQLinear(linear, config)

# Use normally
output = hqq_linear(input_tensor)
```

### Quantize full model with HuggingFace

```python
from transformers import AutoModelForCausalLM, HqqConfig

# Configure HQQ
quantization_config = HqqConfig(
    nbits=4,
    group_size=64,
    axis=1
)

# Load and quantize
model = AutoModelForCausalLM.from_pretrained(
    "meta-llama/Llama-3.1-8B",
    quantization_config=quantization_config,
    device_map="auto"
)

# Model is quantized and ready to use
```

## Core concepts

### Quantization configuration

HQQ uses `BaseQuantizeConfig` to define quantization parameters:

```python
from hqq.core.quantize import BaseQuantizeConfig

# Standard 4-bit config
config_4bit = BaseQuantizeConfig(
    nbits=4,           # Bits per weight (1-8)
    group_size=64,     # Weights per quantization group
    axis=1             # 0=input dim, 1=output dim
)

# Aggressive 2-bit config
config_2bit = BaseQuantizeConfig(
    nbits=2,
    group_size=16,     # Smaller groups for low-bit
    axis=1
)

# Mixed precision per layer type
layer_configs = {
    "self_attn.q_proj": BaseQuantizeConfig(nbits=4, group_size=64),
    "self_attn.k_proj": BaseQuantizeConfig(nbits=4, group_size=64),
    "self_attn.v_proj": BaseQuantizeConfig(nbits=4, group_size=64),
    "mlp.gate_proj": BaseQuantizeConfig(nbits=2, group_size=32),
    "mlp.up_proj": BaseQuantizeConfig(nbits=2, group_size=32),
    "mlp.down_proj": BaseQuantizeConfig(nbits=4, group_size=64),
}
```

### HQQLinear layer

The core quantized layer that replaces `nn.Linear`:

```python
from hqq.core.quantize import HQQLinear
import torch

# Create quantized layer
linear = torch.nn.Linear(4096, 4096)
hqq_layer = HQQLinear(linear, config)

# Access quantized weights
W_q = hqq_layer.W_q           # Quantized weights
scale = hqq_layer.scale       # Scale factors
zero = hqq_layer.zero         # Zero points

# Dequantize for inspection
W_dequant = hqq_layer.dequantize()
```

### Backends

HQQ supports multiple inference backends for different hardware:

```python
from hqq.core.quantize import HQQLinear

# Available backends
backends = [
    "pytorch",          # Pure PyTorch (default)
    "pytorch_compile",  # torch.compile optimized
    "aten",            # Custom CUDA kernels
    "torchao_int4",    # TorchAO int4 matmul
    "gemlite",         # GemLite CUDA kernels
    "bitblas",         # BitBlas optimized
    "marlin",          # Marlin 4-bit kernels
]

# Set backend globally
HQQLinear.set_backend("torchao_int4")

# Or per layer
hqq_layer.set_backend("marlin")
```

**Backend selection guide:**
| Backend | Best For | Requirements |
|---------|----------|--------------|
| pytorch | Compatibility | Any GPU |
| pytorch_compile | Moderate speedup | torch>=2.0 |
| aten | Good balance | CUDA GPU |
| torchao_int4 | 4-bit inference | torchao installed |
| marlin | Maximum 4-bit speed | Ampere+ GPU |
| bitblas | Flexible bit-widths | bitblas installed |

## HuggingFace integration

### Load pre-quantized models

```python
from transformers import AutoModelForCausalLM, AutoTokenizer

# Load HQQ-quantized model from Hub
model = AutoModelForCausalLM.from_pretrained(
    "mobiuslabsgmbh/Llama-3.1-8B-HQQ-4bit",
    device_map="auto"
)
tokenizer = AutoTokenizer.from_pretrained("meta-llama/Llama-3.1-8B")

# Use normally
inputs = tokenizer("Hello, world!", return_tensors="pt").to(model.device)
outputs = model.generate(**inputs, max_new_tokens=50)
```

### Quantize and save

```python
from transformers import AutoModelForCausalLM, HqqConfig

# Quantize
config = HqqConfig(nbits=4, group_size=64)
model = AutoModelForCausalLM.from_pretrained(
    "meta-llama/Llama-3.1-8B",
    quantization_config=config,
    device_map="auto"
)

# Save quantized model
model.save_pretrained("./llama-8b-hqq-4bit")

# Push to Hub
model.push_to_hub("my-org/Llama-3.1-8B-HQQ-4bit")
```

### Mixed precision quantization

```python
from transformers import AutoModelForCausalLM, HqqConfig

# Different precision per layer type
config = HqqConfig(
    nbits=4,
    group_size=64,
    # Attention layers: higher precision
    # MLP layers: lower precision for memory savings
    dynamic_config={
        "attn": {"nbits": 4, "group_size": 64},
        "mlp": {"nbits": 2, "group_size": 32}
    }
)
```

## vLLM integration

### Serve HQQ models with vLLM

```python
from vllm import LLM, SamplingParams

# Load HQQ-quantized model
llm = LLM(
    model="mobiuslabsgmbh/Llama-3.1-8B-HQQ-4bit",
    quantization="hqq",
    dtype="float16"
)

# Generate
sampling_params = SamplingParams(temperature=0.7, max_tokens=100)
outputs = llm.generate(["What is machine learning?"], sampling_params)
```

### vLLM with custom HQQ config

```python
from vllm import LLM

llm = LLM(
    model="meta-llama/Llama-3.1-8B",
    quantization="hqq",
    quantization_config={
        "nbits": 4,
        "group_size": 64
    }
)
```

## PEFT/LoRA fine-tuning

### Fine-tune quantized models

```python
from transformers import AutoModelForCausalLM, HqqConfig
from peft import LoraConfig, get_peft_model

# Load quantized model
quant_config = HqqConfig(nbits=4, group_size=64)
model = AutoModelForCausalLM.from_pretrained(
    "meta-llama/Llama-3.1-8B",
    quantization_config=quant_config,
    device_map="auto"
)

# Apply LoRA
lora_config = LoraConfig(
    r=16,
    lora_alpha=32,
    target_modules=["q_proj", "k_proj", "v_proj", "o_proj"],
    lora_dropout=0.05,
    bias="none",
    task_type="CAUSAL_LM"
)

model = get_peft_model(model, lora_config)

# Train normally with Trainer or custom loop
```

### QLoRA-style training

```python
from transformers import TrainingArguments, Trainer

training_args = TrainingArguments(
    output_dir="./hqq-lora-output",
    per_device_train_batch_size=4,
    gradient_accumulation_steps=4,
    learning_rate=2e-4,
    num_train_epochs=3,
    fp16=True,
    logging_steps=10,
    save_strategy="epoch"
)

trainer = Trainer(
    model=model,
    args=training_args,
    train_dataset=train_dataset,
    data_collator=data_collator
)

trainer.train()
```

## Quantization workflows

### Workflow 1: Quick model compression

```python
from transformers import AutoModelForCausalLM, AutoTokenizer, HqqConfig

# 1. Configure quantization
config = HqqConfig(nbits=4, group_size=64)

# 2. Load and quantize (no calibration needed!)
model = AutoModelForCausalLM.from_pretrained(
    "meta-llama/Llama-3.1-8B",
    quantization_config=config,
    device_map="auto"
)
tokenizer = AutoTokenizer.from_pretrained("meta-llama/Llama-3.1-8B")

# 3. Verify quality
prompt = "The capital of France is"
inputs = tokenizer(prompt, return_tensors="pt").to(model.device)
outputs = model.generate(**inputs, max_new_tokens=20)
print(tokenizer.decode(outputs[0]))

# 4. Save
model.save_pretrained("./llama-8b-hqq")
tokenizer.save_pretrained("./llama-8b-hqq")
```

### Workflow 2: Optimize for inference speed

```python
from hqq.core.quantize import HQQLinear
from transformers import AutoModelForCausalLM, HqqConfig

# 1. Quantize with optimal backend
config = HqqConfig(nbits=4, group_size=64)
model = AutoModelForCausalLM.from_pretrained(
    "meta-llama/Llama-3.1-8B",
    quantization_config=config,
    device_map="auto"
)

# 2. Set fast backend
HQQLinear.set_backend("marlin")  # or "torchao_int4"

# 3. Compile for additional speedup
import torch
model = torch.compile(model)

# 4. Benchmark
import time
inputs = tokenizer("Hello", return_tensors="pt").to(model.device)
start = time.time()
for _ in range(10):
    model.generate(**inputs, max_new_tokens=100)
print(f"Avg time: {(time.time() - start) / 10:.2f}s")
```

## Best practices

1. **Start with 4-bit**: Best quality/size tradeoff for most models
2. **Use group_size=64**: Good balance; smaller for extreme quantization
3. **Choose backend wisely**: Marlin for 4-bit Ampere+, TorchAO for flexibility
4. **Verify quality**: Always test generation quality after quantization
5. **Mixed precision**: Keep attention at higher precision, compress MLP more
6. **PEFT training**: Use LoRA r=16-32 for good fine-tuning results

## Common issues

**Out of memory during quantization:**
```python
# Quantize layer-by-layer
from hqq.models.hf.base import AutoHQQHFModel

model = AutoHQQHFModel.from_pretrained(
    "meta-llama/Llama-3.1-8B",
    quantization_config=config,
    device_map="sequential"  # Load layers sequentially
)
```

**Slow inference:**
```python
# Switch to optimized backend
from hqq.core.quantize import HQQLinear
HQQLinear.set_backend("marlin")  # Requires Ampere+ GPU

# Or compile
model = torch.compile(model, mode="reduce-overhead")
```

**Poor quality at 2-bit:**
```python
# Use smaller group size
config = BaseQuantizeConfig(
    nbits=2,
    group_size=16,  # Smaller groups help at low bits
    axis=1
)
```

## References

- **[Advanced Usage](references/advanced-usage.md)** - Custom backends, mixed precision, optimization
- **[Troubleshooting](references/troubleshooting.md)** - Common issues, debugging, benchmarks

## Resources

- **Repository**: https://github.com/mobiusml/hqq
- **Paper**: Half-Quadratic Quantization
- **HuggingFace Models**: https://huggingface.co/mobiuslabsgmbh
- **Version**: 0.2.0+
- **License**: Apache 2.0


---


## 📚 Reference Materials


### Advanced Usage

# HQQ Advanced Usage Guide

## Custom Backend Configuration

### Backend Selection by Hardware

```python
from hqq.core.quantize import HQQLinear
import torch

def select_optimal_backend():
    """Select best backend based on hardware."""
    device = torch.cuda.get_device_properties(0)
    compute_cap = device.major * 10 + device.minor

    if compute_cap >= 80:  # Ampere+
        return "marlin"
    elif compute_cap >= 70:  # Volta/Turing
        return "aten"
    else:
        return "pytorch_compile"

backend = select_optimal_backend()
HQQLinear.set_backend(backend)
print(f"Using backend: {backend}")
```

### Per-Layer Backend Assignment

```python
from hqq.core.quantize import HQQLinear

def set_layer_backends(model):
    """Assign optimal backends per layer type."""
    for name, module in model.named_modules():
        if isinstance(module, HQQLinear):
            if "attn" in name:
                module.set_backend("marlin")  # Fast for attention
            elif "mlp" in name:
                module.set_backend("bitblas")  # Flexible for MLP
            else:
                module.set_backend("aten")

set_layer_backends(model)
```

### TorchAO Integration

```python
from hqq.core.quantize import HQQLinear
import torchao

# Enable TorchAO int4 backend
HQQLinear.set_backend("torchao_int4")

# Configure TorchAO options
import torch
torch._inductor.config.coordinate_descent_tuning = True
torch._inductor.config.triton.unique_kernel_names = True
```

## Mixed Precision Quantization

### Layer-Specific Configuration

```python
from hqq.core.quantize import BaseQuantizeConfig
from transformers import AutoModelForCausalLM

# Define configs per layer pattern
quant_configs = {
    # Embeddings: Keep full precision
    "embed_tokens": None,
    "lm_head": None,

    # Attention: 4-bit with larger groups
    "self_attn.q_proj": BaseQuantizeConfig(nbits=4, group_size=128),
    "self_attn.k_proj": BaseQuantizeConfig(nbits=4, group_size=128),
    "self_attn.v_proj": BaseQuantizeConfig(nbits=4, group_size=128),
    "self_attn.o_proj": BaseQuantizeConfig(nbits=4, group_size=128),

    # MLP: More aggressive 2-bit
    "mlp.gate_proj": BaseQuantizeConfig(nbits=2, group_size=32),
    "mlp.up_proj": BaseQuantizeConfig(nbits=2, group_size=32),
    "mlp.down_proj": BaseQuantizeConfig(nbits=3, group_size=64),
}

def quantize_with_mixed_precision(model, configs):
    """Apply mixed precision quantization."""
    from hqq.core.quantize import HQQLinear

    for name, module in model.named_modules():
        if isinstance(module, torch.nn.Linear):
            for pattern, config in configs.items():
                if pattern in name:
                    if config is None:
                        continue  # Skip quantization
                    parent = get_parent_module(model, name)
                    setattr(parent, name.split(".")[-1],
                            HQQLinear(module, config))
                    break
    return model
```

### Sensitivity-Based Quantization

```python
import torch
from hqq.core.quantize import BaseQuantizeConfig, HQQLinear

def measure_layer_sensitivity(model, calibration_data, layer_name):
    """Measure quantization sensitivity of a layer."""
    original_output = None
    quantized_output = None

    # Get original output
    def hook_original(module, input, output):
        nonlocal original_output
        original_output = output.clone()

    layer = dict(model.named_modules())[layer_name]
    handle = layer.register_forward_hook(hook_original)

    with torch.no_grad():
        model(calibration_data)
    handle.remove()

    # Quantize and measure error
    for nbits in [4, 3, 2]:
        config = BaseQuantizeConfig(nbits=nbits, group_size=64)
        quant_layer = HQQLinear(layer, config)

        with torch.no_grad():
            quantized_output = quant_layer(calibration_data)

        error = torch.mean((original_output - quantized_output) ** 2).item()
        print(f"{layer_name} @ {nbits}-bit: MSE = {error:.6f}")

# Auto-select precision based on sensitivity
def auto_select_precision(sensitivity_results, threshold=0.01):
    """Select precision based on sensitivity threshold."""
    configs = {}
    for layer_name, errors in sensitivity_results.items():
        for nbits, error in sorted(errors.items()):
            if error < threshold:
                configs[layer_name] = BaseQuantizeConfig(nbits=nbits, group_size=64)
                break
    return configs
```

## Advanced Quantization Options

### Custom Zero Point Handling

```python
from hqq.core.quantize import BaseQuantizeConfig

# Symmetric quantization (zero point = 0)
config_symmetric = BaseQuantizeConfig(
    nbits=4,
    group_size=64,
    axis=1,
    zero_point=False  # No zero point, symmetric
)

# Asymmetric quantization (learned zero point)
config_asymmetric = BaseQuantizeConfig(
    nbits=4,
    group_size=64,
    axis=1,
    zero_point=True  # Include zero point
)
```

### Axis Selection

```python
from hqq.core.quantize import BaseQuantizeConfig

# Quantize along output dimension (default, better for inference)
config_axis1 = BaseQuantizeConfig(
    nbits=4,
    group_size=64,
    axis=1  # Output dimension
)

# Quantize along input dimension (better for some architectures)
config_axis0 = BaseQuantizeConfig(
    nbits=4,
    group_size=64,
    axis=0  # Input dimension
)
```

### Group Size Optimization

```python
def find_optimal_group_size(layer, test_input, target_bits=4):
    """Find optimal group size for a layer."""
    from hqq.core.quantize import BaseQuantizeConfig, HQQLinear
    import torch

    group_sizes = [16, 32, 64, 128, 256]
    results = {}

    with torch.no_grad():
        original_output = layer(test_input)

        for gs in group_sizes:
            config = BaseQuantizeConfig(nbits=target_bits, group_size=gs)
            quant_layer = HQQLinear(layer.clone(), config)
            quant_output = quant_layer(test_input)

            mse = torch.mean((original_output - quant_output) ** 2).item()
            memory = quant_layer.W_q.numel() * target_bits / 8

            results[gs] = {"mse": mse, "memory_bytes": memory}
            print(f"Group size {gs}: MSE={mse:.6f}, Memory={memory/1024:.1f}KB")

    return results
```

## Model Export and Deployment

### Export for ONNX

```python
import torch
from transformers import AutoModelForCausalLM, HqqConfig

# Load quantized model
config = HqqConfig(nbits=4, group_size=64)
model = AutoModelForCausalLM.from_pretrained(
    "meta-llama/Llama-3.1-8B",
    quantization_config=config,
    device_map="cpu"
)

# Export to ONNX (requires dequantization for compatibility)
dummy_input = torch.randint(0, 32000, (1, 128))
torch.onnx.export(
    model,
    dummy_input,
    "model_hqq.onnx",
    input_names=["input_ids"],
    output_names=["logits"],
    dynamic_axes={"input_ids": {0: "batch", 1: "seq_len"}}
)
```

### SafeTensors Export

```python
from safetensors.torch import save_file

def export_hqq_safetensors(model, output_path):
    """Export HQQ model to safetensors format."""
    tensors = {}

    for name, param in model.named_parameters():
        tensors[name] = param.data.cpu()

    # Include quantization metadata
    for name, module in model.named_modules():
        if hasattr(module, "W_q"):
            tensors[f"{name}.W_q"] = module.W_q.cpu()
            tensors[f"{name}.scale"] = module.scale.cpu()
            if hasattr(module, "zero"):
                tensors[f"{name}.zero"] = module.zero.cpu()

    save_file(tensors, output_path)

export_hqq_safetensors(model, "model_hqq.safetensors")
```

## Performance Optimization

### Kernel Fusion

```python
import torch
from hqq.core.quantize import HQQLinear

# Enable torch.compile for kernel fusion
def optimize_model(model):
    """Apply optimizations for inference."""
    # Set optimal backend
    HQQLinear.set_backend("marlin")

    # Compile with optimizations
    model = torch.compile(
        model,
        mode="reduce-overhead",
        fullgraph=True
    )

    return model

model = optimize_model(model)
```

### Batch Size Optimization

```python
def find_optimal_batch_size(model, tokenizer, max_batch=64):
    """Find optimal batch size for throughput."""
    import time

    prompt = "Hello, world!"
    inputs = tokenizer([prompt], return_tensors="pt", padding=True)

    results = {}
    for batch_size in [1, 2, 4, 8, 16, 32, max_batch]:
        try:
            batch_inputs = {
                k: v.repeat(batch_size, 1).to(model.device)
                for k, v in inputs.items()
            }

            # Warmup
            model.generate(**batch_inputs, max_new_tokens=10)

            # Benchmark
            torch.cuda.synchronize()
            start = time.time()
            for _ in range(5):
                model.generate(**batch_inputs, max_new_tokens=50)
            torch.cuda.synchronize()

            elapsed = (time.time() - start) / 5
            throughput = batch_size * 50 / elapsed

            results[batch_size] = {
                "time": elapsed,
                "throughput": throughput
            }
            print(f"Batch {batch_size}: {throughput:.1f} tokens/sec")

        except torch.cuda.OutOfMemoryError:
            print(f"Batch {batch_size}: OOM")
            break

    return results
```

### Memory-Efficient Inference

```python
import torch
from contextlib import contextmanager

@contextmanager
def low_memory_inference(model):
    """Context manager for memory-efficient inference."""
    # Disable gradient computation
    with torch.no_grad():
        # Enable inference mode
        with torch.inference_mode():
            # Clear cache before inference
            torch.cuda.empty_cache()
            yield
            # Clear cache after inference
            torch.cuda.empty_cache()

# Usage
with low_memory_inference(model):
    outputs = model.generate(**inputs, max_new_tokens=100)
```

## Benchmarking

### Comprehensive Benchmark Suite

```python
import time
import torch
from dataclasses import dataclass
from typing import Dict, List

@dataclass
class BenchmarkResult:
    latency_ms: float
    throughput: float
    memory_mb: float
    perplexity: float

def benchmark_hqq_model(model, tokenizer, test_texts: List[str]) -> BenchmarkResult:
    """Comprehensive benchmark for HQQ models."""
    device = next(model.parameters()).device

    # Prepare inputs
    inputs = tokenizer(test_texts, return_tensors="pt", padding=True).to(device)

    # Memory measurement
    torch.cuda.reset_peak_memory_stats()

    # Latency measurement
    torch.cuda.synchronize()
    start = time.time()

    with torch.no_grad():
        outputs = model.generate(
            **inputs,
            max_new_tokens=100,
            do_sample=False
        )

    torch.cuda.synchronize()
    latency = (time.time() - start) * 1000

    # Calculate metrics
    total_tokens = outputs.shape[0] * outputs.shape[1]
    throughput = total_tokens / (latency / 1000)
    memory = torch.cuda.max_memory_allocated() / 1024 / 1024

    # Perplexity (simplified)
    with torch.no_grad():
        outputs = model(**inputs, labels=inputs["input_ids"])
        perplexity = torch.exp(outputs.loss).item()

    return BenchmarkResult(
        latency_ms=latency,
        throughput=throughput,
        memory_mb=memory,
        perplexity=perplexity
    )

# Compare different configurations
def compare_quantization_configs(model_name, configs: Dict[str, dict]):
    """Compare different HQQ configurations."""
    results = {}

    for name, config in configs.items():
        print(f"\nBenchmarking: {name}")
        model = load_hqq_model(model_name, **config)
        result = benchmark_hqq_model(model, tokenizer, test_texts)
        results[name] = result

        print(f"  Latency: {result.latency_ms:.1f}ms")
        print(f"  Throughput: {result.throughput:.1f} tok/s")
        print(f"  Memory: {result.memory_mb:.1f}MB")
        print(f"  Perplexity: {result.perplexity:.2f}")

        del model
        torch.cuda.empty_cache()

    return results
```

## Integration Examples

### LangChain Integration

```python
from langchain_community.llms import HuggingFacePipeline
from transformers import AutoModelForCausalLM, AutoTokenizer, HqqConfig, pipeline

# Load HQQ model
config = HqqConfig(nbits=4, group_size=64)
model = AutoModelForCausalLM.from_pretrained(
    "meta-llama/Llama-3.1-8B",
    quantization_config=config,
    device_map="auto"
)
tokenizer = AutoTokenizer.from_pretrained("meta-llama/Llama-3.1-8B")

# Create pipeline
pipe = pipeline(
    "text-generation",
    model=model,
    tokenizer=tokenizer,
    max_new_tokens=256
)

# Wrap for LangChain
llm = HuggingFacePipeline(pipeline=pipe)

# Use in chain
from langchain.chains import LLMChain
from langchain.prompts import PromptTemplate

prompt = PromptTemplate(
    input_variables=["question"],
    template="Answer the question: {question}"
)

chain = LLMChain(llm=llm, prompt=prompt)
result = chain.run("What is machine learning?")
```

### Gradio Interface

```python
import gradio as gr
from transformers import AutoModelForCausalLM, AutoTokenizer, HqqConfig

# Load model
config = HqqConfig(nbits=4, group_size=64)
model = AutoModelForCausalLM.from_pretrained(
    "meta-llama/Llama-3.1-8B",
    quantization_config=config,
    device_map="auto"
)
tokenizer = AutoTokenizer.from_pretrained("meta-llama/Llama-3.1-8B")

def generate(prompt, max_tokens, temperature):
    inputs = tokenizer(prompt, return_tensors="pt").to(model.device)
    outputs = model.generate(
        **inputs,
        max_new_tokens=int(max_tokens),
        temperature=temperature,
        do_sample=temperature > 0
    )
    return tokenizer.decode(outputs[0], skip_special_tokens=True)

demo = gr.Interface(
    fn=generate,
    inputs=[
        gr.Textbox(label="Prompt"),
        gr.Slider(10, 500, value=100, label="Max Tokens"),
        gr.Slider(0, 2, value=0.7, label="Temperature")
    ],
    outputs=gr.Textbox(label="Output"),
    title="HQQ Quantized LLM"
)

demo.launch()
```




### Troubleshooting

# HQQ Troubleshooting Guide

## Installation Issues

### Package Not Found

**Error**: `ModuleNotFoundError: No module named 'hqq'`

**Fix**:
```bash
pip install hqq

# Verify installation
python -c "import hqq; print(hqq.__version__)"
```

### Backend Dependencies Missing

**Error**: `ImportError: Cannot import marlin backend`

**Fix**:
```bash
# Install specific backend
pip install hqq[marlin]

# Or all backends
pip install hqq[all]

# For BitBlas
pip install bitblas

# For TorchAO
pip install torchao
```

### CUDA Version Mismatch

**Error**: `RuntimeError: CUDA error: no kernel image is available`

**Fix**:
```bash
# Check CUDA version
nvcc --version
python -c "import torch; print(torch.version.cuda)"

# Reinstall PyTorch with matching CUDA
pip install torch --index-url https://download.pytorch.org/whl/cu121

# Then reinstall hqq
pip install hqq --force-reinstall
```

## Quantization Errors

### Out of Memory During Quantization

**Error**: `torch.cuda.OutOfMemoryError`

**Solutions**:

1. **Use CPU offloading**:
```python
from transformers import AutoModelForCausalLM, HqqConfig

model = AutoModelForCausalLM.from_pretrained(
    "meta-llama/Llama-3.1-8B",
    quantization_config=HqqConfig(nbits=4, group_size=64),
    device_map="auto",
    offload_folder="./offload"
)
```

2. **Quantize layer by layer**:
```python
from hqq.models.hf.base import AutoHQQHFModel

model = AutoHQQHFModel.from_pretrained(
    "meta-llama/Llama-3.1-8B",
    quantization_config=config,
    device_map="sequential"
)
```

3. **Reduce group size**:
```python
config = HqqConfig(
    nbits=4,
    group_size=32  # Smaller groups use less memory during quantization
)
```

### NaN Values After Quantization

**Error**: `RuntimeWarning: invalid value encountered` or NaN outputs

**Solutions**:

1. **Check for outliers**:
```python
import torch

def check_weight_stats(model):
    for name, param in model.named_parameters():
        if param.numel() > 0:
            has_nan = torch.isnan(param).any().item()
            has_inf = torch.isinf(param).any().item()
            if has_nan or has_inf:
                print(f"{name}: NaN={has_nan}, Inf={has_inf}")
                print(f"  min={param.min():.4f}, max={param.max():.4f}")

check_weight_stats(model)
```

2. **Use higher precision for problematic layers**:
```python
layer_configs = {
    "problematic_layer": BaseQuantizeConfig(nbits=8, group_size=128),
    "default": BaseQuantizeConfig(nbits=4, group_size=64)
}
```

3. **Skip embedding/lm_head**:
```python
config = HqqConfig(
    nbits=4,
    group_size=64,
    skip_modules=["embed_tokens", "lm_head"]
)
```

### Wrong Output Shape

**Error**: `RuntimeError: shape mismatch`

**Fix**:
```python
# Ensure axis is correct for your model
config = BaseQuantizeConfig(
    nbits=4,
    group_size=64,
    axis=1  # Usually 1 for most models, try 0 if issues
)
```

## Backend Issues

### Marlin Backend Not Working

**Error**: `RuntimeError: Marlin kernel not available`

**Requirements**:
- Ampere (A100) or newer GPU (compute capability >= 8.0)
- 4-bit quantization only
- Group size must be 128

**Fix**:
```python
# Check GPU compatibility
import torch
device = torch.cuda.get_device_properties(0)
print(f"Compute capability: {device.major}.{device.minor}")

# Marlin requires >= 8.0
if device.major >= 8:
    HQQLinear.set_backend("marlin")
else:
    HQQLinear.set_backend("aten")  # Fallback
```

### TorchAO Backend Errors

**Error**: `ImportError: torchao not found`

**Fix**:
```bash
pip install torchao

# Verify
python -c "import torchao; print('TorchAO installed')"
```

**Error**: `RuntimeError: torchao int4 requires specific shapes`

**Fix**:
```python
# TorchAO int4 has shape requirements
# Ensure dimensions are divisible by 32
config = BaseQuantizeConfig(
    nbits=4,
    group_size=64  # Must be power of 2
)
```

### Fallback to PyTorch Backend

```python
from hqq.core.quantize import HQQLinear

def safe_set_backend(preferred_backend):
    """Set backend with fallback."""
    try:
        HQQLinear.set_backend(preferred_backend)
        print(f"Using {preferred_backend} backend")
    except Exception as e:
        print(f"Failed to set {preferred_backend}: {e}")
        print("Falling back to pytorch backend")
        HQQLinear.set_backend("pytorch")

safe_set_backend("marlin")
```

## Performance Issues

### Slow Inference

**Problem**: Inference slower than expected

**Solutions**:

1. **Use optimized backend**:
```python
from hqq.core.quantize import HQQLinear

# Try backends in order of speed
for backend in ["marlin", "torchao_int4", "aten", "pytorch_compile"]:
    try:
        HQQLinear.set_backend(backend)
        print(f"Using {backend}")
        break
    except:
        continue
```

2. **Enable torch.compile**:
```python
import torch
model = torch.compile(model, mode="reduce-overhead")
```

3. **Use CUDA graphs** (for fixed input shapes):
```python
# Warmup
for _ in range(3):
    model.generate(**inputs, max_new_tokens=100)

# Enable CUDA graphs
torch.cuda.synchronize()
```

### High Memory Usage During Inference

**Problem**: Memory usage higher than expected for quantized model

**Solutions**:

1. **Clear KV cache**:
```python
# Use past_key_values management
outputs = model.generate(
    **inputs,
    max_new_tokens=100,
    use_cache=True,
    return_dict_in_generate=True
)
# Clear after use
del outputs.past_key_values
torch.cuda.empty_cache()
```

2. **Reduce batch size**:
```python
# Process in smaller batches
batch_size = 4  # Reduce if OOM
for i in range(0, len(prompts), batch_size):
    batch = prompts[i:i+batch_size]
    outputs = model.generate(...)
    torch.cuda.empty_cache()
```

3. **Use gradient checkpointing** (for training):
```python
model.gradient_checkpointing_enable()
```

## Quality Issues

### Poor Generation Quality

**Problem**: Quantized model produces gibberish or low-quality output

**Solutions**:

1. **Increase precision**:
```python
# Try higher bit-width
config = HqqConfig(nbits=8, group_size=128)  # Start high
# Then gradually reduce: 8 -> 4 -> 3 -> 2
```

2. **Use smaller group size**:
```python
config = HqqConfig(
    nbits=4,
    group_size=32  # Smaller = more accurate, more memory
)
```

3. **Skip sensitive layers**:
```python
config = HqqConfig(
    nbits=4,
    group_size=64,
    skip_modules=["embed_tokens", "lm_head", "model.layers.0"]
)
```

4. **Compare outputs**:
```python
def compare_outputs(original_model, quantized_model, prompt):
    """Compare outputs between original and quantized."""
    inputs = tokenizer(prompt, return_tensors="pt").to("cuda")

    with torch.no_grad():
        orig_out = original_model.generate(**inputs, max_new_tokens=50)
        quant_out = quantized_model.generate(**inputs, max_new_tokens=50)

    print("Original:", tokenizer.decode(orig_out[0]))
    print("Quantized:", tokenizer.decode(quant_out[0]))
```

### Perplexity Degradation

**Problem**: Significant perplexity increase after quantization

**Diagnosis**:
```python
import torch
from datasets import load_dataset

def measure_perplexity(model, tokenizer, dataset_name="wikitext", split="test"):
    """Measure model perplexity."""
    dataset = load_dataset(dataset_name, "wikitext-2-raw-v1", split=split)
    text = "\n\n".join(dataset["text"])

    encodings = tokenizer(text, return_tensors="pt")
    max_length = 2048
    stride = 512

    nlls = []
    for i in range(0, encodings.input_ids.size(1), stride):
        begin = max(i + stride - max_length, 0)
        end = min(i + stride, encodings.input_ids.size(1))

        input_ids = encodings.input_ids[:, begin:end].to(model.device)
        target_ids = input_ids.clone()
        target_ids[:, :-stride] = -100

        with torch.no_grad():
            outputs = model(input_ids, labels=target_ids)
            nlls.append(outputs.loss)

    ppl = torch.exp(torch.stack(nlls).mean())
    return ppl.item()

# Compare
orig_ppl = measure_perplexity(original_model, tokenizer)
quant_ppl = measure_perplexity(quantized_model, tokenizer)
print(f"Original PPL: {orig_ppl:.2f}")
print(f"Quantized PPL: {quant_ppl:.2f}")
print(f"Degradation: {((quant_ppl - orig_ppl) / orig_ppl * 100):.1f}%")
```

## Integration Issues

### HuggingFace Integration Errors

**Error**: `ValueError: Unknown quantization method: hqq`

**Fix**:
```bash
# Update transformers
pip install -U transformers>=4.36.0
```

**Error**: `AttributeError: 'HqqConfig' object has no attribute`

**Fix**:
```python
from transformers import HqqConfig

# Use correct parameter names
config = HqqConfig(
    nbits=4,           # Not 'bits'
    group_size=64,     # Not 'groupsize'
    axis=1             # Not 'quant_axis'
)
```

### vLLM Integration Issues

**Error**: `ValueError: HQQ quantization not supported`

**Fix**:
```bash
# Update vLLM
pip install -U vllm>=0.3.0
```

**Usage**:
```python
from vllm import LLM

# Load pre-quantized model
llm = LLM(
    model="mobiuslabsgmbh/Llama-3.1-8B-HQQ-4bit",
    quantization="hqq"
)
```

### PEFT Integration Issues

**Error**: `RuntimeError: Cannot apply LoRA to quantized layer`

**Fix**:
```python
from peft import prepare_model_for_kbit_training

# Prepare model for training
model = prepare_model_for_kbit_training(model)

# Then apply LoRA
model = get_peft_model(model, lora_config)
```

## Debugging Tips

### Enable Verbose Logging

```python
import logging

logging.basicConfig(level=logging.DEBUG)
logging.getLogger("hqq").setLevel(logging.DEBUG)
```

### Verify Quantization Applied

```python
def verify_quantization(model):
    """Check if model is properly quantized."""
    from hqq.core.quantize import HQQLinear

    total_linear = 0
    quantized_linear = 0

    for name, module in model.named_modules():
        if isinstance(module, torch.nn.Linear):
            total_linear += 1
        elif isinstance(module, HQQLinear):
            quantized_linear += 1
            print(f"Quantized: {name} ({module.W_q.dtype}, {module.W_q.shape})")

    print(f"\nTotal Linear: {total_linear}")
    print(f"Quantized: {quantized_linear}")
    print(f"Ratio: {quantized_linear / max(total_linear + quantized_linear, 1) * 100:.1f}%")

verify_quantization(model)
```

### Memory Profiling

```python
import torch

def profile_memory():
    """Profile GPU memory usage."""
    print(f"Allocated: {torch.cuda.memory_allocated() / 1024**3:.2f} GB")
    print(f"Reserved: {torch.cuda.memory_reserved() / 1024**3:.2f} GB")
    print(f"Max Allocated: {torch.cuda.max_memory_allocated() / 1024**3:.2f} GB")

# Before quantization
profile_memory()

# After quantization
model = load_quantized_model(...)
profile_memory()
```

## Getting Help

1. **GitHub Issues**: https://github.com/mobiusml/hqq/issues
2. **HuggingFace Forums**: https://discuss.huggingface.co
3. **Discord**: Check HQQ community channels

### Reporting Issues

Include:
- HQQ version: `pip show hqq`
- PyTorch version: `python -c "import torch; print(torch.__version__)"`
- CUDA version: `nvcc --version`
- GPU model: `nvidia-smi --query-gpu=name --format=csv`
- Full error traceback
- Minimal reproducible code




---

## 🚀 Usage

**Reference this template:** `@skill-hqq-quantization.md`


**Platform-specific:**
- **GitHub Copilot**: Add to `.github/copilot-instructions.md`
- **Augment Code**: Use `aug context add` command
- **Cursor/Windsurf**: Reference in chat with `@filename`
- **Claude**: Add to Project Knowledge
- **ChatGPT**: Add to Custom GPT configuration
