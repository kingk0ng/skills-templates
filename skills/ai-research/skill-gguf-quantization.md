---
id: skill-gguf-quantization
type: skill
name: gguf-quantization
description: GGUF format and llama.cpp quantization for efficient CPU/GPU inference.
  Use when deploying models on consumer hardware, Apple Silicon, or when needing flexible
  quantization from 2-8 bit without GPU requirements.
category: ai-research
complexity: medium
keywords:
- deployment
- git
- github
- optimization
- python
- test
capabilities: []
token_estimate: 1686
has_references: true
reference_count: 2
---

<!-- Converted from Claude Skill Template -->
<!-- Complexity: Medium -->
<!-- Estimated Tokens: ~1,686 -->
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




# gguf-quantization

> GGUF format and llama.cpp quantization for efficient CPU/GPU inference. Use when deploying models on consumer hardware, Apple Silicon, or when needing flexible quantization from 2-8 bit without GPU requirements.

# GGUF - Quantization Format for llama.cpp

The GGUF (GPT-Generated Unified Format) is the standard file format for llama.cpp, enabling efficient inference on CPUs, Apple Silicon, and GPUs with flexible quantization options.

## When to use GGUF

**Use GGUF when:**
- Deploying on consumer hardware (laptops, desktops)
- Running on Apple Silicon (M1/M2/M3) with Metal acceleration
- Need CPU inference without GPU requirements
- Want flexible quantization (Q2_K to Q8_0)
- Using local AI tools (LM Studio, Ollama, text-generation-webui)

**Key advantages:**
- **Universal hardware**: CPU, Apple Silicon, NVIDIA, AMD support
- **No Python runtime**: Pure C/C++ inference
- **Flexible quantization**: 2-8 bit with various methods (K-quants)
- **Ecosystem support**: LM Studio, Ollama, koboldcpp, and more
- **imatrix**: Importance matrix for better low-bit quality

**Use alternatives instead:**
- **AWQ/GPTQ**: Maximum accuracy with calibration on NVIDIA GPUs
- **HQQ**: Fast calibration-free quantization for HuggingFace
- **bitsandbytes**: Simple integration with transformers library
- **TensorRT-LLM**: Production NVIDIA deployment with maximum speed

## Quick start

### Installation

```bash
# Clone llama.cpp
git clone https://github.com/ggml-org/llama.cpp
cd llama.cpp

# Build (CPU)
make

# Build with CUDA (NVIDIA)
make GGML_CUDA=1

# Build with Metal (Apple Silicon)
make GGML_METAL=1

# Install Python bindings (optional)
pip install llama-cpp-python
```

### Convert model to GGUF

```bash
# Install requirements
pip install -r requirements.txt

# Convert HuggingFace model to GGUF (FP16)
python convert_hf_to_gguf.py ./path/to/model --outfile model-f16.gguf

# Or specify output type
python convert_hf_to_gguf.py ./path/to/model \
    --outfile model-f16.gguf \
    --outtype f16
```

### Quantize model

```bash
# Basic quantization to Q4_K_M
./llama-quantize model-f16.gguf model-q4_k_m.gguf Q4_K_M

# Quantize with importance matrix (better quality)
./llama-imatrix -m model-f16.gguf -f calibration.txt -o model.imatrix
./llama-quantize --imatrix model.imatrix model-f16.gguf model-q4_k_m.gguf Q4_K_M
```

### Run inference

```bash
# CLI inference
./llama-cli -m model-q4_k_m.gguf -p "Hello, how are you?"

# Interactive mode
./llama-cli -m model-q4_k_m.gguf --interactive

# With GPU offload
./llama-cli -m model-q4_k_m.gguf -ngl 35 -p "Hello!"
```

## Quantization types

### K-quant methods (recommended)

| Type | Bits | Size (7B) | Quality | Use Case |
|------|------|-----------|---------|----------|
| Q2_K | 2.5 | ~2.8 GB | Low | Extreme compression |
| Q3_K_S | 3.0 | ~3.0 GB | Low-Med | Memory constrained |
| Q3_K_M | 3.3 | ~3.3 GB | Medium | Balance |
| Q4_K_S | 4.0 | ~3.8 GB | Med-High | Good balance |
| Q4_K_M | 4.5 | ~4.1 GB | High | **Recommended default** |
| Q5_K_S | 5.0 | ~4.6 GB | High | Quality focused |
| Q5_K_M | 5.5 | ~4.8 GB | Very High | High quality |
| Q6_K | 6.0 | ~5.5 GB | Excellent | Near-original |
| Q8_0 | 8.0 | ~7.2 GB | Best | Maximum quality |

### Legacy methods

| Type | Description |
|------|-------------|
| Q4_0 | 4-bit, basic |
| Q4_1 | 4-bit with delta |
| Q5_0 | 5-bit, basic |
| Q5_1 | 5-bit with delta |

**Recommendation**: Use K-quant methods (Q4_K_M, Q5_K_M) for best quality/size ratio.

## Conversion workflows

### Workflow 1: HuggingFace to GGUF

```bash
# 1. Download model
huggingface-cli download meta-llama/Llama-3.1-8B --local-dir ./llama-3.1-8b

# 2. Convert to GGUF (FP16)
python convert_hf_to_gguf.py ./llama-3.1-8b \
    --outfile llama-3.1-8b-f16.gguf \
    --outtype f16

# 3. Quantize
./llama-quantize llama-3.1-8b-f16.gguf llama-3.1-8b-q4_k_m.gguf Q4_K_M

# 4. Test
./llama-cli -m llama-3.1-8b-q4_k_m.gguf -p "Hello!" -n 50
```

### Workflow 2: With importance matrix (better quality)

```bash
# 1. Convert to GGUF
python convert_hf_to_gguf.py ./model --outfile model-f16.gguf

# 2. Create calibration text (diverse samples)
cat > calibration.txt << 'EOF'
The quick brown fox jumps over the lazy dog.
Machine learning is a subset of artificial intelligence.
Python is a popular programming language.
# Add more diverse text samples...
EOF

# 3. Generate importance matrix
./llama-imatrix -m model-f16.gguf \
    -f calibration.txt \
    --chunk 512 \
    -o model.imatrix \
    -ngl 35  # GPU layers if available

# 4. Quantize with imatrix
./llama-quantize --imatrix model.imatrix \
    model-f16.gguf \
    model-q4_k_m.gguf \
    Q4_K_M
```

### Workflow 3: Multiple quantizations

```bash
#!/bin/bash
MODEL="llama-3.1-8b-f16.gguf"
IMATRIX="llama-3.1-8b.imatrix"

# Generate imatrix once
./llama-imatrix -m $MODEL -f wiki.txt -o $IMATRIX -ngl 35

# Create multiple quantizations
for QUANT in Q4_K_M Q5_K_M Q6_K Q8_0; do
    OUTPUT="llama-3.1-8b-${QUANT,,}.gguf"
    ./llama-quantize --imatrix $IMATRIX $MODEL $OUTPUT $QUANT
    echo "Created: $OUTPUT ($(du -h $OUTPUT | cut -f1))"
done
```

## Python usage

### llama-cpp-python

```python
from llama_cpp import Llama

# Load model
llm = Llama(
    model_path="./model-q4_k_m.gguf",
    n_ctx=4096,          # Context window
    n_gpu_layers=35,     # GPU offload (0 for CPU only)
    n_threads=8          # CPU threads
)

# Generate
output = llm(
    "What is machine learning?",
    max_tokens=256,
    temperature=0.7,
    stop=["</s>", "\n\n"]
)
print(output["choices"][0]["text"])
```

### Chat completion

```python
from llama_cpp import Llama

llm = Llama(
    model_path="./model-q4_k_m.gguf",
    n_ctx=4096,
    n_gpu_layers=35,
    chat_format="llama-3"  # Or "chatml", "mistral", etc.
)

messages = [
    {"role": "system", "content": "You are a helpful assistant."},
    {"role": "user", "content": "What is Python?"}
]

response = llm.create_chat_completion(
    messages=messages,
    max_tokens=256,
    temperature=0.7
)
print(response["choices"][0]["message"]["content"])
```

### Streaming

```python
from llama_cpp import Llama

llm = Llama(model_path="./model-q4_k_m.gguf", n_gpu_layers=35)

# Stream tokens
for chunk in llm(
    "Explain quantum computing:",
    max_tokens=256,
    stream=True
):
    print(chunk["choices"][0]["text"], end="", flush=True)
```

## Server mode

### Start OpenAI-compatible server

```bash
# Start server
./llama-server -m model-q4_k_m.gguf \
    --host 0.0.0.0 \
    --port 8080 \
    -ngl 35 \
    -c 4096

# Or with Python bindings
python -m llama_cpp.server \
    --model model-q4_k_m.gguf \
    --n_gpu_layers 35 \
    --host 0.0.0.0 \
    --port 8080
```

### Use with OpenAI client

```python
from openai import OpenAI

client = OpenAI(
    base_url="http://localhost:8080/v1",
    api_key="not-needed"
)

response = client.chat.completions.create(
    model="local-model",
    messages=[{"role": "user", "content": "Hello!"}],
    max_tokens=256
)
print(response.choices[0].message.content)
```

## Hardware optimization

### Apple Silicon (Metal)

```bash
# Build with Metal
make clean && make GGML_METAL=1

# Run with Metal acceleration
./llama-cli -m model.gguf -ngl 99 -p "Hello"

# Python with Metal
llm = Llama(
    model_path="model.gguf",
    n_gpu_layers=99,     # Offload all layers
    n_threads=1          # Metal handles parallelism
)
```

### NVIDIA CUDA

```bash
# Build with CUDA
make clean && make GGML_CUDA=1

# Run with CUDA
./llama-cli -m model.gguf -ngl 35 -p "Hello"

# Specify GPU
CUDA_VISIBLE_DEVICES=0 ./llama-cli -m model.gguf -ngl 35
```

### CPU optimization

```bash
# Build with AVX2/AVX512
make clean && make

# Run with optimal threads
./llama-cli -m model.gguf -t 8 -p "Hello"

# Python CPU config
llm = Llama(
    model_path="model.gguf",
    n_gpu_layers=0,      # CPU only
    n_threads=8,         # Match physical cores
    n_batch=512          # Batch size for prompt processing
)
```

## Integration with tools

### Ollama

```bash
# Create Modelfile
cat > Modelfile << 'EOF'
FROM ./model-q4_k_m.gguf
TEMPLATE """{{ .System }}
{{ .Prompt }}"""
PARAMETER temperature 0.7
PARAMETER num_ctx 4096
EOF

# Create Ollama model
ollama create mymodel -f Modelfile

# Run
ollama run mymodel "Hello!"
```

### LM Studio

1. Place GGUF file in `~/.cache/lm-studio/models/`
2. Open LM Studio and select the model
3. Configure context length and GPU offload
4. Start inference

### text-generation-webui

```bash
# Place in models folder
cp model-q4_k_m.gguf text-generation-webui/models/

# Start with llama.cpp loader
python server.py --model model-q4_k_m.gguf --loader llama.cpp --n-gpu-layers 35
```

## Best practices

1. **Use K-quants**: Q4_K_M offers best quality/size balance
2. **Use imatrix**: Always use importance matrix for Q4 and below
3. **GPU offload**: Offload as many layers as VRAM allows
4. **Context length**: Start with 4096, increase if needed
5. **Thread count**: Match physical CPU cores, not logical
6. **Batch size**: Increase n_batch for faster prompt processing

## Common issues

**Model loads slowly:**
```bash
# Use mmap for faster loading
./llama-cli -m model.gguf --mmap
```

**Out of memory:**
```bash
# Reduce GPU layers
./llama-cli -m model.gguf -ngl 20  # Reduce from 35

# Or use smaller quantization
./llama-quantize model-f16.gguf model-q3_k_m.gguf Q3_K_M
```

**Poor quality at low bits:**
```bash
# Always use imatrix for Q4 and below
./llama-imatrix -m model-f16.gguf -f calibration.txt -o model.imatrix
./llama-quantize --imatrix model.imatrix model-f16.gguf model-q4_k_m.gguf Q4_K_M
```

## References

- **[Advanced Usage](references/advanced-usage.md)** - Batching, speculative decoding, custom builds
- **[Troubleshooting](references/troubleshooting.md)** - Common issues, debugging, benchmarks

## Resources

- **Repository**: https://github.com/ggml-org/llama.cpp
- **Python Bindings**: https://github.com/abetlen/llama-cpp-python
- **Pre-quantized Models**: https://huggingface.co/TheBloke
- **GGUF Converter**: https://huggingface.co/spaces/ggml-org/gguf-my-repo
- **License**: MIT


---


## 📚 Reference Materials


### Advanced Usage

# GGUF Advanced Usage Guide

## Speculative Decoding

### Draft Model Approach

```bash
# Use smaller model as draft for faster generation
./llama-speculative \
    -m large-model-q4_k_m.gguf \
    -md draft-model-q4_k_m.gguf \
    -p "Write a story about AI" \
    -n 500 \
    --draft 8  # Draft tokens before verification
```

### Self-Speculative Decoding

```bash
# Use same model with different context for speculation
./llama-cli -m model-q4_k_m.gguf \
    --lookup-cache-static lookup.bin \
    --lookup-cache-dynamic lookup-dynamic.bin \
    -p "Hello world"
```

## Batched Inference

### Process Multiple Prompts

```python
from llama_cpp import Llama

llm = Llama(
    model_path="model-q4_k_m.gguf",
    n_ctx=4096,
    n_gpu_layers=35,
    n_batch=512  # Larger batch for parallel processing
)

prompts = [
    "What is Python?",
    "Explain machine learning.",
    "Describe neural networks."
]

# Process in batch (each prompt gets separate context)
for prompt in prompts:
    output = llm(prompt, max_tokens=100)
    print(f"Q: {prompt}")
    print(f"A: {output['choices'][0]['text']}\n")
```

### Server Batching

```bash
# Start server with batching
./llama-server -m model-q4_k_m.gguf \
    --host 0.0.0.0 \
    --port 8080 \
    -ngl 35 \
    -c 4096 \
    --parallel 4        # Concurrent requests
    --cont-batching     # Continuous batching
```

## Custom Model Conversion

### Convert with Vocabulary Modifications

```python
# custom_convert.py
import sys
sys.path.insert(0, './llama.cpp')

from convert_hf_to_gguf import main
from gguf import GGUFWriter

# Custom conversion with modified vocab
def convert_with_custom_vocab(model_path, output_path):
    # Load and modify tokenizer
    from transformers import AutoTokenizer
    tokenizer = AutoTokenizer.from_pretrained(model_path)

    # Add special tokens if needed
    special_tokens = {"additional_special_tokens": ["<|custom|>"]}
    tokenizer.add_special_tokens(special_tokens)
    tokenizer.save_pretrained(model_path)

    # Then run standard conversion
    main([model_path, "--outfile", output_path])
```

### Convert Specific Architecture

```bash
# For Mistral-style models
python convert_hf_to_gguf.py ./mistral-model \
    --outfile mistral-f16.gguf \
    --outtype f16

# For Qwen models
python convert_hf_to_gguf.py ./qwen-model \
    --outfile qwen-f16.gguf \
    --outtype f16

# For Phi models
python convert_hf_to_gguf.py ./phi-model \
    --outfile phi-f16.gguf \
    --outtype f16
```

## Advanced Quantization

### Mixed Quantization

```bash
# Quantize different layer types differently
./llama-quantize model-f16.gguf model-mixed.gguf Q4_K_M \
    --allow-requantize \
    --leave-output-tensor
```

### Quantization with Token Embeddings

```bash
# Keep embeddings at higher precision
./llama-quantize model-f16.gguf model-q4.gguf Q4_K_M \
    --token-embedding-type f16
```

### IQ Quantization (Importance-aware)

```bash
# Ultra-low bit quantization with importance
./llama-quantize --imatrix model.imatrix \
    model-f16.gguf model-iq2_xxs.gguf IQ2_XXS

# Available IQ types: IQ2_XXS, IQ2_XS, IQ2_S, IQ3_XXS, IQ3_XS, IQ3_S, IQ4_XS
```

## Memory Optimization

### Memory Mapping

```python
from llama_cpp import Llama

# Use memory mapping for large models
llm = Llama(
    model_path="model-q4_k_m.gguf",
    use_mmap=True,       # Memory map the model
    use_mlock=False,     # Don't lock in RAM
    n_gpu_layers=35
)
```

### Partial GPU Offload

```python
# Calculate layers to offload based on VRAM
import subprocess

def get_free_vram_gb():
    result = subprocess.run(
        ['nvidia-smi', '--query-gpu=memory.free', '--format=csv,nounits,noheader'],
        capture_output=True, text=True
    )
    return int(result.stdout.strip()) / 1024

# Estimate layers based on VRAM (rough: 0.5GB per layer for 7B Q4)
free_vram = get_free_vram_gb()
layers_to_offload = int(free_vram / 0.5)

llm = Llama(
    model_path="model-q4_k_m.gguf",
    n_gpu_layers=min(layers_to_offload, 35)  # Cap at total layers
)
```

### KV Cache Optimization

```python
from llama_cpp import Llama

# Optimize KV cache for long contexts
llm = Llama(
    model_path="model-q4_k_m.gguf",
    n_ctx=8192,          # Large context
    n_gpu_layers=35,
    type_k=1,            # Q8_0 for K cache (1)
    type_v=1,            # Q8_0 for V cache (1)
    # Or use Q4_0 (2) for more compression
)
```

## Context Management

### Context Shifting

```python
from llama_cpp import Llama

llm = Llama(
    model_path="model-q4_k_m.gguf",
    n_ctx=4096,
    n_gpu_layers=35
)

# Handle long conversations with context shifting
conversation = []
max_history = 10

def chat(user_message):
    conversation.append({"role": "user", "content": user_message})

    # Keep only recent history
    if len(conversation) > max_history * 2:
        conversation = conversation[-max_history * 2:]

    response = llm.create_chat_completion(
        messages=conversation,
        max_tokens=256
    )

    assistant_message = response["choices"][0]["message"]["content"]
    conversation.append({"role": "assistant", "content": assistant_message})
    return assistant_message
```

### Save and Load State

```bash
# Save state to file
./llama-cli -m model.gguf \
    -p "Once upon a time" \
    --save-session session.bin \
    -n 100

# Load and continue
./llama-cli -m model.gguf \
    --load-session session.bin \
    -p " and they lived" \
    -n 100
```

## Grammar Constrained Generation

### JSON Output

```python
from llama_cpp import Llama, LlamaGrammar

# Define JSON grammar
json_grammar = LlamaGrammar.from_string('''
root ::= object
object ::= "{" ws pair ("," ws pair)* "}" ws
pair ::= string ":" ws value
value ::= string | number | object | array | "true" | "false" | "null"
array ::= "[" ws value ("," ws value)* "]" ws
string ::= "\\"" [^"\\\\]* "\\""
number ::= [0-9]+
ws ::= [ \\t\\n]*
''')

llm = Llama(model_path="model-q4_k_m.gguf", n_gpu_layers=35)

output = llm(
    "Output a JSON object with name and age:",
    grammar=json_grammar,
    max_tokens=100
)
print(output["choices"][0]["text"])
```

### Custom Grammar

```python
# Grammar for specific format
answer_grammar = LlamaGrammar.from_string('''
root ::= "Answer: " letter "\\n" "Explanation: " explanation
letter ::= [A-D]
explanation ::= [a-zA-Z0-9 .,!?]+
''')

output = llm(
    "Q: What is 2+2? A) 3 B) 4 C) 5 D) 6",
    grammar=answer_grammar,
    max_tokens=100
)
```

## LoRA Integration

### Load LoRA Adapter

```bash
# Apply LoRA at runtime
./llama-cli -m base-model-q4_k_m.gguf \
    --lora lora-adapter.gguf \
    --lora-scale 1.0 \
    -p "Hello!"
```

### Multiple LoRA Adapters

```bash
# Stack multiple adapters
./llama-cli -m base-model.gguf \
    --lora adapter1.gguf --lora-scale 0.5 \
    --lora adapter2.gguf --lora-scale 0.5 \
    -p "Hello!"
```

### Python LoRA Usage

```python
from llama_cpp import Llama

llm = Llama(
    model_path="base-model-q4_k_m.gguf",
    lora_path="lora-adapter.gguf",
    lora_scale=1.0,
    n_gpu_layers=35
)
```

## Embedding Generation

### Extract Embeddings

```python
from llama_cpp import Llama

llm = Llama(
    model_path="model-q4_k_m.gguf",
    embedding=True,      # Enable embedding mode
    n_gpu_layers=35
)

# Get embeddings
embeddings = llm.embed("This is a test sentence.")
print(f"Embedding dimension: {len(embeddings)}")
```

### Batch Embeddings

```python
texts = [
    "Machine learning is fascinating.",
    "Deep learning uses neural networks.",
    "Python is a programming language."
]

embeddings = [llm.embed(text) for text in texts]

# Calculate similarity
import numpy as np

def cosine_similarity(a, b):
    return np.dot(a, b) / (np.linalg.norm(a) * np.linalg.norm(b))

sim = cosine_similarity(embeddings[0], embeddings[1])
print(f"Similarity: {sim:.4f}")
```

## Performance Tuning

### Benchmark Script

```python
import time
from llama_cpp import Llama

def benchmark(model_path, prompt, n_tokens=100, n_runs=5):
    llm = Llama(
        model_path=model_path,
        n_gpu_layers=35,
        n_ctx=2048,
        verbose=False
    )

    # Warmup
    llm(prompt, max_tokens=10)

    # Benchmark
    times = []
    for _ in range(n_runs):
        start = time.time()
        output = llm(prompt, max_tokens=n_tokens)
        elapsed = time.time() - start
        times.append(elapsed)

    avg_time = sum(times) / len(times)
    tokens_per_sec = n_tokens / avg_time

    print(f"Model: {model_path}")
    print(f"Avg time: {avg_time:.2f}s")
    print(f"Tokens/sec: {tokens_per_sec:.1f}")

    return tokens_per_sec

# Compare quantizations
for quant in ["q4_k_m", "q5_k_m", "q8_0"]:
    benchmark(f"model-{quant}.gguf", "Explain quantum computing:", 100)
```

### Optimal Configuration Finder

```python
def find_optimal_config(model_path, target_vram_gb=8):
    """Find optimal n_gpu_layers and n_batch for target VRAM."""
    from llama_cpp import Llama
    import gc

    best_config = None
    best_speed = 0

    for n_gpu_layers in range(0, 50, 5):
        for n_batch in [128, 256, 512, 1024]:
            try:
                gc.collect()
                llm = Llama(
                    model_path=model_path,
                    n_gpu_layers=n_gpu_layers,
                    n_batch=n_batch,
                    n_ctx=2048,
                    verbose=False
                )

                # Quick benchmark
                start = time.time()
                llm("Hello", max_tokens=50)
                speed = 50 / (time.time() - start)

                if speed > best_speed:
                    best_speed = speed
                    best_config = {
                        "n_gpu_layers": n_gpu_layers,
                        "n_batch": n_batch,
                        "speed": speed
                    }

                del llm
                gc.collect()

            except Exception as e:
                print(f"OOM at layers={n_gpu_layers}, batch={n_batch}")
                break

    return best_config
```

## Multi-GPU Setup

### Distribute Across GPUs

```bash
# Split model across multiple GPUs
./llama-cli -m large-model.gguf \
    --tensor-split 0.5,0.5 \
    -ngl 60 \
    -p "Hello!"
```

### Python Multi-GPU

```python
import os
os.environ["CUDA_VISIBLE_DEVICES"] = "0,1"

from llama_cpp import Llama

llm = Llama(
    model_path="large-model-q4_k_m.gguf",
    n_gpu_layers=60,
    tensor_split=[0.5, 0.5]  # Split evenly across 2 GPUs
)
```

## Custom Builds

### Build with All Optimizations

```bash
# Clean build with all CPU optimizations
make clean
LLAMA_OPENBLAS=1 LLAMA_BLAS_VENDOR=OpenBLAS make -j

# With CUDA and cuBLAS
make clean
GGML_CUDA=1 LLAMA_CUBLAS=1 make -j

# With specific CUDA architecture
GGML_CUDA=1 CUDA_DOCKER_ARCH=sm_86 make -j
```

### CMake Build

```bash
mkdir build && cd build
cmake .. -DGGML_CUDA=ON -DCMAKE_BUILD_TYPE=Release
cmake --build . --config Release -j
```




### Troubleshooting

# GGUF Troubleshooting Guide

## Installation Issues

### Build Fails

**Error**: `make: *** No targets specified and no makefile found`

**Fix**:
```bash
# Ensure you're in llama.cpp directory
cd llama.cpp
make
```

**Error**: `fatal error: cuda_runtime.h: No such file or directory`

**Fix**:
```bash
# Install CUDA toolkit
# Ubuntu
sudo apt install nvidia-cuda-toolkit

# Or set CUDA path
export CUDA_PATH=/usr/local/cuda
export PATH=$CUDA_PATH/bin:$PATH
make GGML_CUDA=1
```

### Python Bindings Issues

**Error**: `ERROR: Failed building wheel for llama-cpp-python`

**Fix**:
```bash
# Install build dependencies
pip install cmake scikit-build-core

# For CUDA support
CMAKE_ARGS="-DGGML_CUDA=on" pip install llama-cpp-python --force-reinstall --no-cache-dir

# For Metal (macOS)
CMAKE_ARGS="-DGGML_METAL=on" pip install llama-cpp-python --force-reinstall --no-cache-dir
```

**Error**: `ImportError: libcudart.so.XX: cannot open shared object file`

**Fix**:
```bash
# Add CUDA libraries to path
export LD_LIBRARY_PATH=/usr/local/cuda/lib64:$LD_LIBRARY_PATH

# Or reinstall with correct CUDA version
pip uninstall llama-cpp-python
CUDACXX=/usr/local/cuda/bin/nvcc CMAKE_ARGS="-DGGML_CUDA=on" pip install llama-cpp-python
```

## Conversion Issues

### Model Not Supported

**Error**: `KeyError: 'model.embed_tokens.weight'`

**Fix**:
```bash
# Check model architecture
python -c "from transformers import AutoConfig; print(AutoConfig.from_pretrained('./model').architectures)"

# Use appropriate conversion script
# For most models:
python convert_hf_to_gguf.py ./model --outfile model.gguf

# For older models, check if legacy script needed
```

### Vocabulary Mismatch

**Error**: `RuntimeError: Vocabulary size mismatch`

**Fix**:
```python
# Ensure tokenizer matches model
from transformers import AutoTokenizer, AutoModelForCausalLM

tokenizer = AutoTokenizer.from_pretrained("./model")
model = AutoModelForCausalLM.from_pretrained("./model")

print(f"Tokenizer vocab size: {len(tokenizer)}")
print(f"Model vocab size: {model.config.vocab_size}")

# If mismatch, resize embeddings before conversion
model.resize_token_embeddings(len(tokenizer))
model.save_pretrained("./model-fixed")
```

### Out of Memory During Conversion

**Error**: `torch.cuda.OutOfMemoryError` during conversion

**Fix**:
```bash
# Use CPU for conversion
CUDA_VISIBLE_DEVICES="" python convert_hf_to_gguf.py ./model --outfile model.gguf

# Or use low memory mode
python convert_hf_to_gguf.py ./model --outfile model.gguf --outtype f16
```

## Quantization Issues

### Wrong Output File Size

**Problem**: Quantized file is larger than expected

**Check**:
```bash
# Verify quantization type
./llama-cli -m model.gguf --verbose

# Expected sizes for 7B model:
# Q4_K_M: ~4.1 GB
# Q5_K_M: ~4.8 GB
# Q8_0: ~7.2 GB
# F16: ~13.5 GB
```

### Quantization Crashes

**Error**: `Segmentation fault` during quantization

**Fix**:
```bash
# Increase stack size
ulimit -s unlimited

# Or use less threads
./llama-quantize -t 4 model-f16.gguf model-q4.gguf Q4_K_M
```

### Poor Quality After Quantization

**Problem**: Model outputs gibberish after quantization

**Solutions**:

1. **Use importance matrix**:
```bash
# Generate imatrix with good calibration data
./llama-imatrix -m model-f16.gguf \
    -f wiki_sample.txt \
    --chunk 512 \
    -o model.imatrix

# Quantize with imatrix
./llama-quantize --imatrix model.imatrix \
    model-f16.gguf model-q4_k_m.gguf Q4_K_M
```

2. **Try higher precision**:
```bash
# Use Q5_K_M or Q6_K instead of Q4
./llama-quantize model-f16.gguf model-q5_k_m.gguf Q5_K_M
```

3. **Check original model**:
```bash
# Test FP16 version first
./llama-cli -m model-f16.gguf -p "Hello, how are you?" -n 50
```

## Inference Issues

### Slow Generation

**Problem**: Generation is slower than expected

**Solutions**:

1. **Enable GPU offload**:
```bash
./llama-cli -m model.gguf -ngl 35 -p "Hello"
```

2. **Optimize batch size**:
```python
llm = Llama(
    model_path="model.gguf",
    n_batch=512,        # Increase for faster prompt processing
    n_gpu_layers=35
)
```

3. **Use appropriate threads**:
```bash
# Match physical cores, not logical
./llama-cli -m model.gguf -t 8 -p "Hello"
```

4. **Enable Flash Attention** (if supported):
```bash
./llama-cli -m model.gguf -ngl 35 --flash-attn -p "Hello"
```

### Out of Memory

**Error**: `CUDA out of memory` or system freeze

**Solutions**:

1. **Reduce GPU layers**:
```python
# Start low and increase
llm = Llama(model_path="model.gguf", n_gpu_layers=10)
```

2. **Use smaller quantization**:
```bash
./llama-quantize model-f16.gguf model-q3_k_m.gguf Q3_K_M
```

3. **Reduce context length**:
```python
llm = Llama(
    model_path="model.gguf",
    n_ctx=2048,  # Reduce from 4096
    n_gpu_layers=35
)
```

4. **Quantize KV cache**:
```python
llm = Llama(
    model_path="model.gguf",
    type_k=2,    # Q4_0 for K cache
    type_v=2,    # Q4_0 for V cache
    n_gpu_layers=35
)
```

### Garbage Output

**Problem**: Model outputs random characters or nonsense

**Diagnose**:
```python
# Check model loading
llm = Llama(model_path="model.gguf", verbose=True)

# Test with simple prompt
output = llm("1+1=", max_tokens=5, temperature=0)
print(output)
```

**Solutions**:

1. **Check model integrity**:
```bash
# Verify GGUF file
./llama-cli -m model.gguf --verbose 2>&1 | head -50
```

2. **Use correct chat format**:
```python
llm = Llama(
    model_path="model.gguf",
    chat_format="llama-3"  # Match your model: chatml, mistral, etc.
)
```

3. **Check temperature**:
```python
# Use lower temperature for deterministic output
output = llm("Hello", max_tokens=50, temperature=0.1)
```

### Token Issues

**Error**: `RuntimeError: unknown token` or encoding errors

**Fix**:
```python
# Ensure UTF-8 encoding
prompt = "Hello, world!".encode('utf-8').decode('utf-8')
output = llm(prompt, max_tokens=50)
```

## Server Issues

### Connection Refused

**Error**: `Connection refused` when accessing server

**Fix**:
```bash
# Bind to all interfaces
./llama-server -m model.gguf --host 0.0.0.0 --port 8080

# Check if port is in use
lsof -i :8080
```

### Server Crashes Under Load

**Problem**: Server crashes with multiple concurrent requests

**Solutions**:

1. **Limit parallelism**:
```bash
./llama-server -m model.gguf \
    --parallel 2 \
    -c 4096 \
    --cont-batching
```

2. **Add request timeout**:
```bash
./llama-server -m model.gguf --timeout 300
```

3. **Monitor memory**:
```bash
watch -n 1 nvidia-smi  # For GPU
watch -n 1 free -h     # For RAM
```

### API Compatibility Issues

**Problem**: OpenAI client not working with server

**Fix**:
```python
from openai import OpenAI

# Use correct base URL format
client = OpenAI(
    base_url="http://localhost:8080/v1",  # Include /v1
    api_key="not-needed"
)

# Use correct model name
response = client.chat.completions.create(
    model="local",  # Or the actual model name
    messages=[{"role": "user", "content": "Hello"}]
)
```

## Apple Silicon Issues

### Metal Not Working

**Problem**: Metal acceleration not enabled

**Check**:
```bash
# Verify Metal support
./llama-cli -m model.gguf --verbose 2>&1 | grep -i metal
```

**Fix**:
```bash
# Rebuild with Metal
make clean
make GGML_METAL=1

# Python bindings
CMAKE_ARGS="-DGGML_METAL=on" pip install llama-cpp-python --force-reinstall
```

### Incorrect Memory Usage on M1/M2

**Problem**: Model uses too much unified memory

**Fix**:
```python
# Offload all layers for Metal
llm = Llama(
    model_path="model.gguf",
    n_gpu_layers=99,    # Offload everything
    n_threads=1         # Metal handles parallelism
)
```

## Debugging

### Enable Verbose Output

```bash
# CLI verbose mode
./llama-cli -m model.gguf --verbose -p "Hello" -n 50

# Python verbose
llm = Llama(model_path="model.gguf", verbose=True)
```

### Check Model Metadata

```bash
# View GGUF metadata
./llama-cli -m model.gguf --verbose 2>&1 | head -100
```

### Validate GGUF File

```python
import struct

def validate_gguf(filepath):
    with open(filepath, 'rb') as f:
        magic = f.read(4)
        if magic != b'GGUF':
            print(f"Invalid magic: {magic}")
            return False

        version = struct.unpack('<I', f.read(4))[0]
        print(f"GGUF version: {version}")

        tensor_count = struct.unpack('<Q', f.read(8))[0]
        metadata_count = struct.unpack('<Q', f.read(8))[0]
        print(f"Tensors: {tensor_count}, Metadata: {metadata_count}")

        return True

validate_gguf("model.gguf")
```

## Getting Help

1. **GitHub Issues**: https://github.com/ggml-org/llama.cpp/issues
2. **Discussions**: https://github.com/ggml-org/llama.cpp/discussions
3. **Reddit**: r/LocalLLaMA

### Reporting Issues

Include:
- llama.cpp version/commit hash
- Build command used
- Model name and quantization
- Full error message/stack trace
- Hardware: CPU/GPU model, RAM, VRAM
- OS version
- Minimal reproduction steps




---

## 🚀 Usage

**Reference this template:** `@skill-gguf-quantization.md`


**Platform-specific:**
- **GitHub Copilot**: Add to `.github/copilot-instructions.md`
- **Augment Code**: Use `aug context add` command
- **Cursor/Windsurf**: Reference in chat with `@filename`
- **Claude**: Add to Project Knowledge
- **ChatGPT**: Add to Custom GPT configuration
