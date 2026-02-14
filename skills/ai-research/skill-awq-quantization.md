---
id: skill-awq-quantization
type: skill
name: awq-quantization
description: Activation-aware weight quantization for 4-bit LLM compression with 3x
  speedup and minimal accuracy loss. Use when deploying large models (7B-70B) on limited
  GPU memory, when you need faster inference than GPTQ with better accuracy preservation,
  or for instruction-tuned and multimodal models. MLSys 2024 Best Paper Award winner.
category: ai-research
complexity: medium
keywords:
- deployment
- github
- optimization
- performance
- python
capabilities: []
token_estimate: 1245
has_references: true
reference_count: 2
---

<!-- Converted from Claude Skill Template -->
<!-- Complexity: Medium -->
<!-- Estimated Tokens: ~1,245 -->
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




# awq-quantization

> Activation-aware weight quantization for 4-bit LLM compression with 3x speedup and minimal accuracy loss. Use when deploying large models (7B-70B) on limited GPU memory, when you need faster inference than GPTQ with better accuracy preservation, or for instruction-tuned and multimodal models. MLSys 2024 Best Paper Award winner.

# AWQ (Activation-aware Weight Quantization)

4-bit quantization that preserves salient weights based on activation patterns, achieving 3x speedup with minimal accuracy loss.

## When to use AWQ

**Use AWQ when:**
- Need 4-bit quantization with <5% accuracy loss
- Deploying instruction-tuned or chat models (AWQ generalizes better)
- Want ~2.5-3x inference speedup over FP16
- Using vLLM for production serving
- Have Ampere+ GPUs (A100, H100, RTX 40xx) for Marlin kernel support

**Use GPTQ instead when:**
- Need maximum ecosystem compatibility (more tools support GPTQ)
- Working with ExLlamaV2 backend specifically
- Have older GPUs without Marlin support

**Use bitsandbytes instead when:**
- Need zero calibration overhead (quantize on-the-fly)
- Want to fine-tune with QLoRA
- Prefer simpler integration

## Quick start

### Installation

```bash
# Default (Triton kernels)
pip install autoawq

# With optimized CUDA kernels + Flash Attention
pip install autoawq[kernels]

# Intel CPU/XPU optimization
pip install autoawq[cpu]
```

**Requirements**: Python 3.8+, CUDA 11.8+, Compute Capability 7.5+

### Load pre-quantized model

```python
from awq import AutoAWQForCausalLM
from transformers import AutoTokenizer

model_name = "TheBloke/Mistral-7B-Instruct-v0.2-AWQ"

model = AutoAWQForCausalLM.from_quantized(
    model_name,
    fuse_layers=True  # Enable fused attention for speed
)
tokenizer = AutoTokenizer.from_pretrained(model_name)

# Generate
inputs = tokenizer("Explain quantum computing", return_tensors="pt").to("cuda")
outputs = model.generate(**inputs, max_new_tokens=200)
print(tokenizer.decode(outputs[0], skip_special_tokens=True))
```

### Quantize your own model

```python
from awq import AutoAWQForCausalLM
from transformers import AutoTokenizer

model_path = "mistralai/Mistral-7B-Instruct-v0.2"

# Load model and tokenizer
model = AutoAWQForCausalLM.from_pretrained(model_path)
tokenizer = AutoTokenizer.from_pretrained(model_path)

# Quantization config
quant_config = {
    "zero_point": True,      # Use zero-point quantization
    "q_group_size": 128,     # Group size (128 recommended)
    "w_bit": 4,              # 4-bit weights
    "version": "GEMM"        # GEMM for batch, GEMV for single-token
}

# Quantize (uses pileval dataset by default)
model.quantize(tokenizer, quant_config=quant_config)

# Save
model.save_quantized("mistral-7b-awq")
tokenizer.save_pretrained("mistral-7b-awq")
```

**Timing**: ~10-15 min for 7B, ~1 hour for 70B models.

## AWQ vs GPTQ vs bitsandbytes

| Feature | AWQ | GPTQ | bitsandbytes |
|---------|-----|------|--------------|
| **Speedup (4-bit)** | ~2.5-3x | ~2x | ~1.5x |
| **Accuracy loss** | <5% | ~5-10% | ~5-15% |
| **Calibration** | Minimal (128-1K tokens) | More extensive | None |
| **Overfitting risk** | Low | Higher | N/A |
| **Best for** | Production inference | GPU inference | Easy integration |
| **vLLM support** | Native | Yes | Limited |

**Key insight**: AWQ assumes not all weights are equally important. It protects ~1% of salient weights identified by activation patterns, reducing quantization error without mixed-precision overhead.

## Kernel backends

### GEMM (default, batch inference)

```python
quant_config = {
    "zero_point": True,
    "q_group_size": 128,
    "w_bit": 4,
    "version": "GEMM"  # Best for batch sizes > 1
}
```

### GEMV (single-token generation)

```python
quant_config = {
    "version": "GEMV"  # 20% faster for batch_size=1
}
```

**Limitation**: Only batch size 1, not good for large context.

### Marlin (Ampere+ GPUs)

```python
from transformers import AwqConfig, AutoModelForCausalLM

config = AwqConfig(
    bits=4,
    version="marlin"  # 2x faster on A100/H100
)

model = AutoModelForCausalLM.from_pretrained(
    "TheBloke/Mistral-7B-AWQ",
    quantization_config=config
)
```

**Requirements**: Compute Capability 8.0+ (A100, H100, RTX 40xx)

### ExLlamaV2 (AMD compatible)

```python
config = AwqConfig(
    bits=4,
    version="exllama"  # Faster prefill, AMD GPU support
)
```

## HuggingFace Transformers integration

### Direct loading

```python
from transformers import AutoModelForCausalLM, AutoTokenizer

model = AutoModelForCausalLM.from_pretrained(
    "TheBloke/zephyr-7B-alpha-AWQ",
    device_map="auto"
)
tokenizer = AutoTokenizer.from_pretrained("TheBloke/zephyr-7B-alpha-AWQ")
```

### Fused modules (recommended)

```python
from transformers import AwqConfig, AutoModelForCausalLM

config = AwqConfig(
    bits=4,
    fuse_max_seq_len=512,  # Max sequence length for fusing
    do_fuse=True           # Enable fused attention/MLP
)

model = AutoModelForCausalLM.from_pretrained(
    "TheBloke/Mistral-7B-OpenOrca-AWQ",
    quantization_config=config
)
```

**Note**: Fused modules cannot combine with FlashAttention2.

## vLLM integration

```python
from vllm import LLM, SamplingParams

# vLLM auto-detects AWQ models
llm = LLM(
    model="TheBloke/Llama-2-7B-AWQ",
    quantization="awq",
    dtype="half"
)

sampling = SamplingParams(temperature=0.7, max_tokens=200)
outputs = llm.generate(["Explain AI"], sampling)
```

## Performance benchmarks

### Memory reduction

| Model | FP16 | AWQ 4-bit | Reduction |
|-------|------|-----------|-----------|
| Mistral 7B | 14 GB | 5.5 GB | 2.5x |
| Llama 2-13B | 26 GB | 10 GB | 2.6x |
| Llama 2-70B | 140 GB | 35 GB | 4x |

### Inference speed (RTX 4090)

| Model | Prefill (tok/s) | Decode (tok/s) | Memory |
|-------|-----------------|----------------|--------|
| Mistral 7B GEMM | 3,897 | 114 | 5.55 GB |
| TinyLlama 1B GEMV | 5,179 | 431 | 2.10 GB |
| Llama 2-13B GEMM | 2,279 | 74 | 10.28 GB |

### Accuracy (perplexity)

| Model | FP16 | AWQ 4-bit | Degradation |
|-------|------|-----------|-------------|
| Llama 3 8B | 8.20 | 8.48 | +3.4% |
| Mistral 7B | 5.25 | 5.42 | +3.2% |
| Qwen2 72B | 4.85 | 4.95 | +2.1% |

## Custom calibration data

```python
# Use custom dataset for domain-specific models
model.quantize(
    tokenizer,
    quant_config=quant_config,
    calib_data="wikitext",       # Or custom list of strings
    max_calib_samples=256,       # More samples = better accuracy
    max_calib_seq_len=512        # Sequence length
)

# Or provide your own samples
calib_samples = [
    "Your domain-specific text here...",
    "More examples from your use case...",
]
model.quantize(tokenizer, quant_config=quant_config, calib_data=calib_samples)
```

## Multi-GPU deployment

```python
model = AutoAWQForCausalLM.from_quantized(
    "TheBloke/Llama-2-70B-AWQ",
    device_map="auto",  # Auto-split across GPUs
    max_memory={0: "40GB", 1: "40GB"}
)
```

## Supported models

35+ architectures including:
- **Llama family**: Llama 2/3, Code Llama, Mistral, Mixtral
- **Qwen**: Qwen, Qwen2, Qwen2.5-VL
- **Others**: Falcon, MPT, Phi, Yi, DeepSeek, Gemma
- **Multimodal**: LLaVA, LLaVA-Next, Qwen2-VL

## Common issues

**CUDA OOM during quantization**:
```python
# Reduce batch size
model.quantize(tokenizer, quant_config=quant_config, max_calib_samples=64)
```

**Slow inference**:
```python
# Enable fused layers
model = AutoAWQForCausalLM.from_quantized(model_name, fuse_layers=True)
```

**AMD GPU support**:
```python
# Use ExLlama backend
config = AwqConfig(bits=4, version="exllama")
```

## Deprecation notice

AutoAWQ is officially deprecated. For new projects, consider:
- **vLLM llm-compressor**: https://github.com/vllm-project/llm-compressor
- **MLX-LM**: For Mac devices with Apple Silicon

Existing quantized models remain usable.

## References

- **Paper**: AWQ: Activation-aware Weight Quantization (arXiv:2306.00978) - MLSys 2024 Best Paper
- **GitHub**: https://github.com/casper-hansen/AutoAWQ
- **MIT Han Lab**: https://github.com/mit-han-lab/llm-awq
- **Models**: https://huggingface.co/models?library=awq


---


## 📚 Reference Materials


### Advanced Usage

# AWQ Advanced Usage Guide

## Quantization Algorithm Details

### How AWQ Works

AWQ (Activation-aware Weight Quantization) is based on the key insight that not all weights in an LLM are equally important. The algorithm:

1. **Identifies salient weights** (~1%) by examining activation distributions
2. **Applies mathematical scaling** to protect critical channels
3. **Quantizes remaining weights** to 4-bit with minimal error

**Core formula**: `L(s) = ||Q(W * s)(s^-1 * X) - W * X||`

Where:
- `Q` is the quantization function
- `W` is the weight matrix
- `s` is the scaling factor
- `X` is the input activation

### Why AWQ Outperforms GPTQ

| Aspect | AWQ | GPTQ |
|--------|-----|------|
| Calibration approach | Activation-aware scaling | Hessian-based reconstruction |
| Overfitting risk | Low (no backprop) | Higher (reconstruction-based) |
| Calibration data | 128-1024 tokens | Larger datasets needed |
| Generalization | Better across domains | Can overfit to calibration |

## WQLinear Kernel Variants

AutoAWQ provides multiple kernel implementations for different use cases:

### WQLinear_GEMM
- **Use case**: Batch inference, training
- **Best for**: Batch sizes > 1, throughput optimization
- **Implementation**: General matrix multiplication

```python
quant_config = {"version": "GEMM"}
```

### WQLinear_GEMV
- **Use case**: Single-token generation
- **Best for**: Streaming, chat applications
- **Speedup**: ~20% faster than GEMM for batch_size=1
- **Limitation**: Only works with batch_size=1

```python
quant_config = {"version": "GEMV"}
```

### WQLinear_GEMVFast
- **Use case**: Optimized single-token generation
- **Requirements**: awq_v2_ext kernels installed
- **Best for**: Maximum single-token speed

```python
# Requires autoawq[kernels] installation
quant_config = {"version": "gemv_fast"}
```

### WQLinear_Marlin
- **Use case**: High-throughput inference
- **Requirements**: Ampere+ GPUs (Compute Capability 8.0+)
- **Speedup**: 2x faster on A100/H100

```python
from transformers import AwqConfig

config = AwqConfig(bits=4, version="marlin")
```

### WQLinear_Exllama / ExllamaV2
- **Use case**: AMD GPU compatibility, faster prefill
- **Benefits**: Works with ROCm

```python
config = AwqConfig(bits=4, version="exllama")
```

### WQLinear_IPEX
- **Use case**: Intel CPU/XPU acceleration
- **Requirements**: Intel Extension for PyTorch, torch 2.4+

```python
pip install autoawq[cpu]
```

## Group Size Configuration

Group size determines how weights are grouped for quantization:

| Group Size | Model Size | Accuracy | Speed | Use Case |
|------------|------------|----------|-------|----------|
| 32 | Larger | Best | Slower | Maximum accuracy |
| **128** | Medium | Good | Fast | **Recommended default** |
| 256 | Smaller | Lower | Faster | Speed-critical |

```python
quant_config = {
    "q_group_size": 128,  # Recommended
    "w_bit": 4,
    "zero_point": True
}
```

## Zero-Point Quantization

Zero-point quantization adds an offset to handle asymmetric weight distributions:

```python
# With zero-point (recommended for most models)
quant_config = {"zero_point": True, "w_bit": 4, "q_group_size": 128}

# Without zero-point (symmetric quantization)
quant_config = {"zero_point": False, "w_bit": 4, "q_group_size": 128}
```

**When to disable zero-point**:
- Models with symmetric weight distributions
- When using specific kernels that don't support it

## Custom Calibration Strategies

### Domain-Specific Calibration

For domain-specific models, use relevant calibration data:

```python
# Medical domain
medical_samples = [
    "Patient presents with acute respiratory symptoms...",
    "Differential diagnosis includes pneumonia, bronchitis...",
    # More domain-specific examples
]

model.quantize(
    tokenizer,
    quant_config=quant_config,
    calib_data=medical_samples,
    max_calib_samples=256
)
```

### Instruction-Tuned Model Calibration

For chat/instruction models, include conversational data:

```python
chat_samples = [
    "Human: What is machine learning?\nAssistant: Machine learning is...",
    "Human: Explain neural networks.\nAssistant: Neural networks are...",
]

model.quantize(tokenizer, quant_config=quant_config, calib_data=chat_samples)
```

### Calibration Parameters

```python
model.quantize(
    tokenizer,
    quant_config=quant_config,
    calib_data="pileval",          # Dataset name or list
    max_calib_samples=128,         # Number of samples (more = slower but better)
    max_calib_seq_len=512,         # Sequence length
    duo_scaling=True,              # Scale weights and activations
    apply_clip=True                # Apply weight clipping
)
```

## Layer Fusion

Layer fusion combines multiple operations for better performance:

### Automatic Fusion

```python
model = AutoAWQForCausalLM.from_quantized(
    model_name,
    fuse_layers=True  # Enables automatic fusion
)
```

### What Gets Fused

- **Attention**: Q, K, V projections combined
- **MLP**: Gate and Up projections fused
- **Normalization**: Replaced with FasterTransformerRMSNorm

### Manual Fusion Configuration

```python
from transformers import AwqConfig

config = AwqConfig(
    bits=4,
    fuse_max_seq_len=2048,  # Max context for fused attention
    do_fuse=True,
    modules_to_fuse={
        "attention": ["q_proj", "k_proj", "v_proj"],
        "mlp": ["gate_proj", "up_proj"],
        "layernorm": ["input_layernorm", "post_attention_layernorm"],
    }
)
```

## Memory Optimization

### Chunked Processing

For large models, AWQ processes in chunks to avoid OOM:

```python
from awq import AutoAWQForCausalLM

# Reduce memory during quantization
model = AutoAWQForCausalLM.from_pretrained(
    model_path,
    low_cpu_mem_usage=True
)
```

### Multi-GPU Quantization

```python
model = AutoAWQForCausalLM.from_pretrained(
    "meta-llama/Llama-2-70b-hf",
    device_map="auto"
)
```

### CPU Offloading

```python
model = AutoAWQForCausalLM.from_quantized(
    model_name,
    device_map="auto",
    max_memory={
        0: "24GB",
        "cpu": "100GB"
    }
)
```

## Modules to Not Convert

Some modules should remain in full precision:

```python
# Visual encoder in multimodal models
class LlavaAWQForCausalLM(BaseAWQForCausalLM):
    modules_to_not_convert = ["visual"]
```

Common exclusions:
- `visual` - Vision encoders in VLMs
- `lm_head` - Output projection
- `embed_tokens` - Embedding layers

## Saving and Loading

### Save Quantized Model

```python
# Save locally
model.save_quantized("./my-awq-model")
tokenizer.save_pretrained("./my-awq-model")

# Save with safetensors (recommended)
model.save_quantized("./my-awq-model", safetensors=True)

# Save sharded (for large models)
model.save_quantized("./my-awq-model", shard_size="5GB")
```

### Push to HuggingFace

```python
model.push_to_hub("username/my-awq-model")
tokenizer.push_to_hub("username/my-awq-model")
```

### Load with Specific Backend

```python
from awq import AutoAWQForCausalLM

# Load with specific kernel
model = AutoAWQForCausalLM.from_quantized(
    model_name,
    use_exllama=True,           # ExLlama backend
    use_exllama_v2=True,        # ExLlamaV2 (faster)
    use_marlin=True,            # Marlin kernels
    use_ipex=True,              # Intel CPU
    fuse_layers=True            # Enable fusion
)
```

## Benchmarking Your Model

```python
from awq.utils.utils import get_best_device
import time

model = AutoAWQForCausalLM.from_quantized(model_name, fuse_layers=True)
tokenizer = AutoTokenizer.from_pretrained(model_name)

# Warmup
inputs = tokenizer("Hello", return_tensors="pt").to(get_best_device())
model.generate(**inputs, max_new_tokens=10)

# Benchmark
prompt = "Write a detailed essay about"
inputs = tokenizer(prompt, return_tensors="pt").to(get_best_device())

start = time.time()
outputs = model.generate(**inputs, max_new_tokens=200)
end = time.time()

tokens_generated = outputs.shape[1] - inputs.input_ids.shape[1]
print(f"Tokens/sec: {tokens_generated / (end - start):.2f}")
```




### Troubleshooting

# AWQ Troubleshooting Guide

## Installation Issues

### CUDA Version Mismatch

**Error**: `RuntimeError: CUDA error: no kernel image is available for execution`

**Fix**: Install matching CUDA version:
```bash
# Check your CUDA version
nvcc --version

# Install matching autoawq
pip install autoawq --extra-index-url https://download.pytorch.org/whl/cu118  # For CUDA 11.8
pip install autoawq --extra-index-url https://download.pytorch.org/whl/cu121  # For CUDA 12.1
```

### Compute Capability Too Low

**Error**: `AssertionError: Compute capability must be >= 7.5`

**Fix**: AWQ requires NVIDIA GPUs with compute capability 7.5+ (Turing or newer):
- RTX 20xx series: 7.5 (supported)
- RTX 30xx series: 8.6 (supported)
- RTX 40xx series: 8.9 (supported)
- A100/H100: 8.0/9.0 (supported)

Older GPUs (GTX 10xx, V100) are not supported.

### Transformers Version Conflict

**Error**: `ImportError: cannot import name 'AwqConfig'`

**Fix**: AutoAWQ may downgrade transformers. Reinstall correct version:
```bash
pip install autoawq
pip install transformers>=4.45.0 --upgrade
```

### Triton Not Found (Linux)

**Error**: `ModuleNotFoundError: No module named 'triton'`

**Fix**:
```bash
pip install triton
# Or install with kernels
pip install autoawq[kernels]
```

## Quantization Issues

### CUDA Out of Memory During Quantization

**Error**: `torch.cuda.OutOfMemoryError: CUDA out of memory`

**Solutions**:

1. **Reduce calibration samples**:
```python
model.quantize(
    tokenizer,
    quant_config=quant_config,
    max_calib_samples=64  # Reduce from 128
)
```

2. **Use CPU offloading**:
```python
model = AutoAWQForCausalLM.from_pretrained(
    model_path,
    low_cpu_mem_usage=True
)
```

3. **Multi-GPU quantization**:
```python
model = AutoAWQForCausalLM.from_pretrained(
    model_path,
    device_map="auto"
)
```

### NaN in Weights After Quantization

**Error**: `AssertionError: NaN detected in weights`

**Cause**: Calibration data issues or numerical instability.

**Fix**:
```python
# Use more calibration samples
model.quantize(
    tokenizer,
    quant_config=quant_config,
    max_calib_samples=256,
    max_calib_seq_len=1024
)
```

### Empty Calibration Samples

**Error**: `ValueError: Calibration samples are empty`

**Fix**: Ensure tokenizer produces valid output:
```python
# Check tokenizer
test = tokenizer("test", return_tensors="pt")
print(f"Token count: {test.input_ids.shape[1]}")

# Use explicit calibration data
calib_data = ["Your sample text here..."] * 128
model.quantize(tokenizer, quant_config=quant_config, calib_data=calib_data)
```

### Unsupported Model Architecture

**Error**: `TypeError: 'model_type' is not supported`

**Cause**: Model architecture not in AWQ registry.

**Check supported models**:
```python
from awq.models import AWQ_CAUSAL_LM_MODEL_MAP
print(list(AWQ_CAUSAL_LM_MODEL_MAP.keys()))
```

**Supported**: llama, mistral, qwen2, falcon, mpt, phi, gemma, etc.

## Inference Issues

### Slow Inference Speed

**Problem**: Inference slower than expected.

**Solutions**:

1. **Enable layer fusion**:
```python
model = AutoAWQForCausalLM.from_quantized(
    model_name,
    fuse_layers=True
)
```

2. **Use correct kernel for batch size**:
```python
# For batch_size=1
quant_config = {"version": "GEMV"}

# For batch_size>1
quant_config = {"version": "GEMM"}
```

3. **Use Marlin on Ampere+ GPUs**:
```python
from transformers import AwqConfig
config = AwqConfig(bits=4, version="marlin")
```

### Wrong Output / Garbage Text

**Problem**: Model produces nonsensical output after quantization.

**Causes and fixes**:

1. **Poor calibration data**: Use domain-relevant data
```python
calib_data = [
    "Relevant examples from your use case...",
]
model.quantize(tokenizer, quant_config=quant_config, calib_data=calib_data)
```

2. **Tokenizer mismatch**: Ensure same tokenizer
```python
tokenizer = AutoTokenizer.from_pretrained(model_name, use_fast=True)
```

3. **Check generation config**:
```python
outputs = model.generate(
    **inputs,
    max_new_tokens=200,
    do_sample=True,
    temperature=0.7,
    pad_token_id=tokenizer.eos_token_id
)
```

### FlashAttention2 Incompatibility

**Error**: `ValueError: Cannot use FlashAttention2 with fused modules`

**Fix**: Disable one or the other:
```python
# Option 1: Use fused modules (recommended for AWQ)
model = AutoAWQForCausalLM.from_quantized(model_name, fuse_layers=True)

# Option 2: Use FlashAttention2 without fusion
from transformers import AutoModelForCausalLM
model = AutoModelForCausalLM.from_pretrained(
    model_name,
    attn_implementation="flash_attention_2",
    device_map="auto"
)
```

### AMD GPU Issues

**Error**: `RuntimeError: ROCm/HIP not found`

**Fix**: Use ExLlama backend for AMD:
```python
from transformers import AwqConfig

config = AwqConfig(bits=4, version="exllama")
model = AutoModelForCausalLM.from_pretrained(
    model_name,
    quantization_config=config
)
```

## Loading Issues

### Model Not Found

**Error**: `OSError: model_name is not a valid model identifier`

**Fix**: Check HuggingFace model exists:
```bash
# Search AWQ models
https://huggingface.co/models?library=awq

# Common AWQ model providers
TheBloke, teknium, Qwen, NousResearch
```

### Safetensors Error

**Error**: `safetensors_rust.SafetensorError: Error while deserializing`

**Fix**: Try loading without safetensors:
```python
model = AutoAWQForCausalLM.from_quantized(
    model_name,
    safetensors=False
)
```

### Device Map Conflicts

**Error**: `ValueError: You cannot use device_map with max_memory`

**Fix**: Use one or the other:
```python
# Auto device map
model = AutoAWQForCausalLM.from_quantized(model_name, device_map="auto")

# OR manual memory limits
model = AutoAWQForCausalLM.from_quantized(
    model_name,
    max_memory={0: "20GB", 1: "20GB"}
)
```

## vLLM Integration Issues

### Quantization Not Detected

**Error**: vLLM loads model in FP16 instead of quantized.

**Fix**: Explicitly specify quantization:
```python
from vllm import LLM

llm = LLM(
    model="TheBloke/Llama-2-7B-AWQ",
    quantization="awq",  # Explicitly set
    dtype="half"
)
```

### Marlin Kernel Error in vLLM

**Error**: `RuntimeError: Marlin kernel not supported`

**Fix**: Check GPU compatibility:
```python
import torch
print(torch.cuda.get_device_capability())  # Must be >= (8, 0)

# If not supported, use GEMM
llm = LLM(model="...", quantization="awq")  # Uses GEMM by default
```

## Performance Debugging

### Memory Usage Check

```python
import torch

def print_gpu_memory():
    for i in range(torch.cuda.device_count()):
        allocated = torch.cuda.memory_allocated(i) / 1e9
        reserved = torch.cuda.memory_reserved(i) / 1e9
        print(f"GPU {i}: {allocated:.2f}GB allocated, {reserved:.2f}GB reserved")

print_gpu_memory()
```

### Profiling Inference

```python
import time

def benchmark_model(model, tokenizer, prompt, n_runs=5):
    inputs = tokenizer(prompt, return_tensors="pt").to("cuda")

    # Warmup
    model.generate(**inputs, max_new_tokens=10)
    torch.cuda.synchronize()

    # Benchmark
    times = []
    for _ in range(n_runs):
        start = time.perf_counter()
        outputs = model.generate(**inputs, max_new_tokens=100)
        torch.cuda.synchronize()
        times.append(time.perf_counter() - start)

    tokens = outputs.shape[1] - inputs.input_ids.shape[1]
    avg_time = sum(times) / len(times)
    print(f"Average: {tokens/avg_time:.2f} tokens/sec")
```

## Getting Help

1. **Check deprecation notice**: AutoAWQ is deprecated, use llm-compressor for new projects
2. **GitHub Issues**: https://github.com/casper-hansen/AutoAWQ/issues
3. **HuggingFace Forums**: https://discuss.huggingface.co/
4. **vLLM Discord**: For vLLM integration issues




---

## 🚀 Usage

**Reference this template:** `@skill-awq-quantization.md`


**Platform-specific:**
- **GitHub Copilot**: Add to `.github/copilot-instructions.md`
- **Augment Code**: Use `aug context add` command
- **Cursor/Windsurf**: Reference in chat with `@filename`
- **Claude**: Add to Project Knowledge
- **ChatGPT**: Add to Custom GPT configuration
