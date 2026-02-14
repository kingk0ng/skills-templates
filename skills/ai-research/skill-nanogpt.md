---
id: skill-nanogpt
type: skill
name: nanogpt
description: Educational GPT implementation in ~300 lines. Reproduces GPT-2 (124M)
  on OpenWebText. Clean, hackable code for learning transformers. By Andrej Karpathy.
  Perfect for understanding GPT architecture from scratch. Train on Shakespeare (CPU)
  or OpenWebText (multi-GPU).
category: ai-research
complexity: medium
keywords:
- github
- performance
- python
capabilities: []
token_estimate: 1098
has_references: true
reference_count: 3
---

<!-- Converted from Claude Skill Template -->
<!-- Complexity: Medium -->
<!-- Estimated Tokens: ~1,098 -->
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




# nanogpt

> Educational GPT implementation in ~300 lines. Reproduces GPT-2 (124M) on OpenWebText. Clean, hackable code for learning transformers. By Andrej Karpathy. Perfect for understanding GPT architecture from scratch. Train on Shakespeare (CPU) or OpenWebText (multi-GPU).

# nanoGPT - Minimalist GPT Training

## Quick start

nanoGPT is a simplified GPT implementation designed for learning and experimentation.

**Installation**:
```bash
pip install torch numpy transformers datasets tiktoken wandb tqdm
```

**Train on Shakespeare** (CPU-friendly):
```bash
# Prepare data
python data/shakespeare_char/prepare.py

# Train (5 minutes on CPU)
python train.py config/train_shakespeare_char.py

# Generate text
python sample.py --out_dir=out-shakespeare-char
```

**Output**:
```
ROMEO:
What say'st thou? Shall I speak, and be a man?

JULIET:
I am afeard, and yet I'll speak; for thou art
One that hath been a man, and yet I know not
What thou art.
```

## Common workflows

### Workflow 1: Character-level Shakespeare

**Complete training pipeline**:
```bash
# Step 1: Prepare data (creates train.bin, val.bin)
python data/shakespeare_char/prepare.py

# Step 2: Train small model
python train.py config/train_shakespeare_char.py

# Step 3: Generate text
python sample.py --out_dir=out-shakespeare-char
```

**Config** (`config/train_shakespeare_char.py`):
```python
# Model config
n_layer = 6          # 6 transformer layers
n_head = 6           # 6 attention heads
n_embd = 384         # 384-dim embeddings
block_size = 256     # 256 char context

# Training config
batch_size = 64
learning_rate = 1e-3
max_iters = 5000
eval_interval = 500

# Hardware
device = 'cpu'  # Or 'cuda'
compile = False # Set True for PyTorch 2.0
```

**Training time**: ~5 minutes (CPU), ~1 minute (GPU)

### Workflow 2: Reproduce GPT-2 (124M)

**Multi-GPU training on OpenWebText**:
```bash
# Step 1: Prepare OpenWebText (takes ~1 hour)
python data/openwebtext/prepare.py

# Step 2: Train GPT-2 124M with DDP (8 GPUs)
torchrun --standalone --nproc_per_node=8 \
  train.py config/train_gpt2.py

# Step 3: Sample from trained model
python sample.py --out_dir=out
```

**Config** (`config/train_gpt2.py`):
```python
# GPT-2 (124M) architecture
n_layer = 12
n_head = 12
n_embd = 768
block_size = 1024
dropout = 0.0

# Training
batch_size = 12
gradient_accumulation_steps = 5 * 8  # Total batch ~0.5M tokens
learning_rate = 6e-4
max_iters = 600000
lr_decay_iters = 600000

# System
compile = True  # PyTorch 2.0
```

**Training time**: ~4 days (8× A100)

### Workflow 3: Fine-tune pretrained GPT-2

**Start from OpenAI checkpoint**:
```python
# In train.py or config
init_from = 'gpt2'  # Options: gpt2, gpt2-medium, gpt2-large, gpt2-xl

# Model loads OpenAI weights automatically
python train.py config/finetune_shakespeare.py
```

**Example config** (`config/finetune_shakespeare.py`):
```python
# Start from GPT-2
init_from = 'gpt2'

# Dataset
dataset = 'shakespeare_char'
batch_size = 1
block_size = 1024

# Fine-tuning
learning_rate = 3e-5  # Lower LR for fine-tuning
max_iters = 2000
warmup_iters = 100

# Regularization
weight_decay = 1e-1
```

### Workflow 4: Custom dataset

**Train on your own text**:
```python
# data/custom/prepare.py
import numpy as np

# Load your data
with open('my_data.txt', 'r') as f:
    text = f.read()

# Create character mappings
chars = sorted(list(set(text)))
stoi = {ch: i for i, ch in enumerate(chars)}
itos = {i: ch for i, ch in enumerate(chars)}

# Tokenize
data = np.array([stoi[ch] for ch in text], dtype=np.uint16)

# Split train/val
n = len(data)
train_data = data[:int(n*0.9)]
val_data = data[int(n*0.9):]

# Save
train_data.tofile('data/custom/train.bin')
val_data.tofile('data/custom/val.bin')
```

**Train**:
```bash
python data/custom/prepare.py
python train.py --dataset=custom
```

## When to use vs alternatives

**Use nanoGPT when**:
- Learning how GPT works
- Experimenting with transformer variants
- Teaching/education purposes
- Quick prototyping
- Limited compute (can run on CPU)

**Simplicity advantages**:
- **~300 lines**: Entire model in `model.py`
- **~300 lines**: Training loop in `train.py`
- **Hackable**: Easy to modify
- **No abstractions**: Pure PyTorch

**Use alternatives instead**:
- **HuggingFace Transformers**: Production use, many models
- **Megatron-LM**: Large-scale distributed training
- **LitGPT**: More architectures, production-ready
- **PyTorch Lightning**: Need high-level framework

## Common issues

**Issue: CUDA out of memory**

Reduce batch size or context length:
```python
batch_size = 1  # Reduce from 12
block_size = 512  # Reduce from 1024
gradient_accumulation_steps = 40  # Increase to maintain effective batch
```

**Issue: Training too slow**

Enable compilation (PyTorch 2.0+):
```python
compile = True  # 2× speedup
```

Use mixed precision:
```python
dtype = 'bfloat16'  # Or 'float16'
```

**Issue: Poor generation quality**

Train longer:
```python
max_iters = 10000  # Increase from 5000
```

Lower temperature:
```python
# In sample.py
temperature = 0.7  # Lower from 1.0
top_k = 200       # Add top-k sampling
```

**Issue: Can't load GPT-2 weights**

Install transformers:
```bash
pip install transformers
```

Check model name:
```python
init_from = 'gpt2'  # Valid: gpt2, gpt2-medium, gpt2-large, gpt2-xl
```

## Advanced topics

**Model architecture**: See [references/architecture.md](references/architecture.md) for GPT block structure, multi-head attention, and MLP layers explained simply.

**Training loop**: See [references/training.md](references/training.md) for learning rate schedule, gradient accumulation, and distributed data parallel setup.

**Data preparation**: See [references/data.md](references/data.md) for tokenization strategies (character-level vs BPE) and binary format details.

## Hardware requirements

- **Shakespeare (char-level)**:
  - CPU: 5 minutes
  - GPU (T4): 1 minute
  - VRAM: <1GB

- **GPT-2 (124M)**:
  - 1× A100: ~1 week
  - 8× A100: ~4 days
  - VRAM: ~16GB per GPU

- **GPT-2 Medium (350M)**:
  - 8× A100: ~2 weeks
  - VRAM: ~40GB per GPU

**Performance**:
- With `compile=True`: 2× speedup
- With `dtype=bfloat16`: 50% memory reduction

## Resources

- GitHub: https://github.com/karpathy/nanoGPT ⭐ 48,000+
- Video: "Let's build GPT" by Andrej Karpathy
- Paper: "Attention is All You Need" (Vaswani et al.)
- OpenWebText: https://huggingface.co/datasets/Skylion007/openwebtext
- Educational: Best for understanding transformers from scratch




---


## 📚 Reference Materials


### Training

# NanoGPT Training Guide

## Training Loop (~300 Lines)

NanoGPT's `train.py` is a self-contained training script with minimal dependencies.

### Complete Training Script Structure

```python
# train.py (simplified)
import os
import time
import math
import pickle
import torch
from model import GPTConfig, GPT

# Training config
batch_size = 12          # Micro batch size
block_size = 1024        # Context length
gradient_accumulation_steps = 5 * 8  # ~60K tokens per batch

# Model config
n_layer = 12
n_head = 12
n_embd = 768
dropout = 0.0

# Optimizer config
learning_rate = 6e-4
max_iters = 600000
weight_decay = 1e-1
beta1 = 0.9
beta2 = 0.95
grad_clip = 1.0

# Learning rate schedule
warmup_iters = 2000
lr_decay_iters = 600000
min_lr = 6e-5

# System
device = 'cuda'
dtype = 'bfloat16' if torch.cuda.is_bf16_supported() else 'float16'
compile = True  # PyTorch 2.0

# Data loader
def get_batch(split):
    data = train_data if split == 'train' else val_data
    ix = torch.randint(len(data) - block_size, (batch_size,))
    x = torch.stack([data[i:i+block_size] for i in ix])
    y = torch.stack([data[i+1:i+1+block_size] for i in ix])
    x, y = x.to(device), y.to(device)
    return x, y

# Learning rate schedule
def get_lr(it):
    # Warmup
    if it < warmup_iters:
        return learning_rate * it / warmup_iters
    # Decay to min_lr
    if it > lr_decay_iters:
        return min_lr
    # Cosine decay
    decay_ratio = (it - warmup_iters) / (lr_decay_iters - warmup_iters)
    coeff = 0.5 * (1.0 + math.cos(math.pi * decay_ratio))
    return min_lr + coeff * (learning_rate - min_lr)

# Init model
model = GPT(GPTConfig())
model.to(device)

# Compile model (PyTorch 2.0)
if compile:
    print("Compiling model...")
    model = torch.compile(model)

# Optimizer
optimizer = model.configure_optimizers(weight_decay, learning_rate, (beta1, beta2), device)

# Training loop
for iter_num in range(max_iters):
    # Set learning rate
    lr = get_lr(iter_num)
    for param_group in optimizer.param_groups:
        param_group['lr'] = lr

    # Gradient accumulation
    for micro_step in range(gradient_accumulation_steps):
        X, Y = get_batch('train')
        with torch.amp.autocast(device_type='cuda', dtype=torch.bfloat16):
            logits, loss = model(X, Y)
            loss = loss / gradient_accumulation_steps
        loss.backward()

    # Clip gradients
    if grad_clip != 0.0:
        torch.nn.utils.clip_grad_norm_(model.parameters(), grad_clip)

    # Update weights
    optimizer.step()
    optimizer.zero_grad(set_to_none=True)

    # Logging
    if iter_num % 100 == 0:
        print(f"iter {iter_num}: loss {loss.item():.4f}, lr {lr:.2e}")
```

## Data Preparation

### Shakespeare Character-Level

```bash
# Step 1: Download Shakespeare
cd data/shakespeare_char
python prepare.py

# Creates:
# - train.bin (90% of data, ~1MB)
# - val.bin (10% of data, ~110KB)
# - meta.pkl (vocab info)
```

**prepare.py**:
```python
import os
import pickle
import requests
import numpy as np

# Download
input_file = 'input.txt'
if not os.path.exists(input_file):
    url = 'https://raw.githubusercontent.com/karpathy/char-rnn/master/data/tinyshakespeare/input.txt'
    with open(input_file, 'w') as f:
        f.write(requests.get(url).text)

# Read and process
with open(input_file, 'r') as f:
    data = f.read()

print(f"Length: {len(data):,} characters")

# Create vocabulary
chars = sorted(list(set(data)))
vocab_size = len(chars)
print(f"Vocab size: {vocab_size}")

# Create mappings
stoi = {ch: i for i, ch in enumerate(chars)}
itos = {i: ch for i, ch in enumerate(chars)}

# Encode dataset
data_ids = [stoi[c] for c in data]

# Train/val split
n = len(data_ids)
train_ids = data_ids[:int(n*0.9)]
val_ids = data_ids[int(n*0.9):]

# Save as numpy arrays
train_ids = np.array(train_ids, dtype=np.uint16)
val_ids = np.array(val_ids, dtype=np.uint16)
train_ids.tofile('train.bin')
val_ids.tofile('val.bin')

# Save metadata
meta = {'vocab_size': vocab_size, 'itos': itos, 'stoi': stoi}
with open('meta.pkl', 'wb') as f:
    pickle.dump(meta, f)
```

### OpenWebText (GPT-2 Reproduction)

```bash
# Step 1: Download OpenWebText (~12GB compressed)
cd data/openwebtext
python prepare.py

# Warning: Takes 1-2 hours, creates ~54GB of tokenized data
```

**prepare.py**:
```python
import os
import numpy as np
import tiktoken
from datasets import load_dataset

# Download dataset
dataset = load_dataset("openwebtext", num_proc=8)

# Use GPT-2 tokenizer
enc = tiktoken.get_encoding("gpt2")

def tokenize(example):
    ids = enc.encode_ordinary(example['text'])
    ids.append(enc.eot_token)  # Add <|endoftext|>
    return {'ids': ids, 'len': len(ids)}

# Tokenize (parallel)
tokenized = dataset.map(
    tokenize,
    remove_columns=['text'],
    desc="Tokenizing",
    num_proc=8
)

# Concatenate all tokens
train_ids = np.concatenate([np.array(x['ids'], dtype=np.uint16) for x in tokenized['train']])
print(f"Train tokens: {len(train_ids):,}")  # ~9B tokens

# Save
train_ids.tofile('train.bin')

# Validation set (sample)
val_ids = np.concatenate([np.array(x['ids'], dtype=np.uint16) for x in tokenized['train'][:5000]])
val_ids.tofile('val.bin')

# Save metadata
meta = {'vocab_size': enc.n_vocab, 'eot_token': enc.eot_token}
with open('meta.pkl', 'wb') as f:
    pickle.dump(meta, f)
```

## Learning Rate Schedules

### Cosine Decay with Warmup (GPT-2 style)

```python
def get_lr(it):
    # 1) Linear warmup
    if it < warmup_iters:
        return learning_rate * it / warmup_iters

    # 2) Constant at min_lr after decay
    if it > lr_decay_iters:
        return min_lr

    # 3) Cosine decay in between
    decay_ratio = (it - warmup_iters) / (lr_decay_iters - warmup_iters)
    coeff = 0.5 * (1.0 + math.cos(math.pi * decay_ratio))
    return min_lr + coeff * (learning_rate - min_lr)

# Example values
learning_rate = 6e-4  # Peak LR
min_lr = 6e-5         # Final LR (10% of peak)
warmup_iters = 2000   # Warmup steps
lr_decay_iters = 600000  # Total training steps
```

**Visualization**:
```
LR
^
|     Peak (6e-4)
|    /‾‾‾‾‾‾‾‾‾‾\
|   /            \
|  /              \_____ Min (6e-5)
| /
|/________________> Iteration
  Warmup  Cosine    Const
  (2K)    (598K)
```

### Constant LR with Warmup (Simple)

```python
def get_lr(it):
    if it < warmup_iters:
        return learning_rate * it / warmup_iters
    return learning_rate

# Good for small experiments
```

## Gradient Accumulation

**Effective batch size** = `batch_size × gradient_accumulation_steps × num_gpus`

```python
# Config
batch_size = 12  # Per-GPU micro batch
gradient_accumulation_steps = 40  # Accumulate gradients
# Effective batch: 12 × 40 = 480 sequences = ~0.5M tokens

# Training loop
optimizer.zero_grad()
for micro_step in range(gradient_accumulation_steps):
    X, Y = get_batch('train')
    logits, loss = model(X, Y)
    loss = loss / gradient_accumulation_steps  # Scale loss
    loss.backward()  # Accumulate gradients

# Update once
torch.nn.utils.clip_grad_norm_(model.parameters(), grad_clip)
optimizer.step()
```

**Why?**
- Simulates large batch size without OOM
- GPT-2 (124M) uses effective batch ~0.5M tokens
- More stable training

## Mixed Precision Training

### BF16 (Best for A100/H100)

```python
# Enable bfloat16
dtype = torch.bfloat16

# Training loop
for iter in range(max_iters):
    X, Y = get_batch('train')

    # Forward in BF16
    with torch.amp.autocast(device_type='cuda', dtype=torch.bfloat16):
        logits, loss = model(X, Y)

    # Backward in FP32 (automatic)
    loss.backward()
    optimizer.step()
```

**Advantages**:
- No gradient scaler needed
- Same dynamic range as FP32
- 2× faster, 50% memory reduction

### FP16 (V100, older GPUs)

```python
from torch.cuda.amp import GradScaler, autocast

scaler = GradScaler()

for iter in range(max_iters):
    X, Y = get_batch('train')

    # Forward in FP16
    with autocast():
        logits, loss = model(X, Y)

    # Scale loss, backward
    scaler.scale(loss).backward()

    # Unscale, clip gradients
    scaler.unscale_(optimizer)
    torch.nn.utils.clip_grad_norm_(model.parameters(), grad_clip)

    # Update weights
    scaler.step(optimizer)
    scaler.update()
```

## Distributed Data Parallel (DDP)

### Single Node, Multiple GPUs

```python
# train.py (DDP version)
import torch.distributed as dist
from torch.nn.parallel import DistributedDataParallel as DDP

# Initialize
dist.init_process_group(backend='nccl')
ddp_rank = int(os.environ['RANK'])
ddp_local_rank = int(os.environ['LOCAL_RANK'])
ddp_world_size = int(os.environ['WORLD_SIZE'])
device = f'cuda:{ddp_local_rank}'
torch.cuda.set_device(device)

# Model
model = GPT(GPTConfig())
model.to(device)
model = DDP(model, device_ids=[ddp_local_rank])

# Training loop (same as before, DDP handles gradient sync)
for iter in range(max_iters):
    X, Y = get_batch('train')  # Each rank gets different data
    logits, loss = model(X, Y)
    loss.backward()  # DDP syncs gradients across GPUs
    optimizer.step()
```

**Launch**:
```bash
# 8 GPUs on single node
torchrun --standalone --nproc_per_node=8 train.py config/train_gpt2.py
```

### Multi-Node Training

```bash
# Node 0 (master)
torchrun --nproc_per_node=8 \
  --nnodes=4 --node_rank=0 \
  --master_addr=192.168.1.100 --master_port=29500 \
  train.py config/train_gpt2.py

# Node 1-3 (workers)
torchrun --nproc_per_node=8 \
  --nnodes=4 --node_rank=$RANK \
  --master_addr=192.168.1.100 --master_port=29500 \
  train.py config/train_gpt2.py
```

## Checkpointing

### Save Checkpoint

```python
# Save every N iterations
if iter_num % 5000 == 0:
    checkpoint = {
        'model': model.state_dict(),
        'optimizer': optimizer.state_dict(),
        'model_args': model_args,
        'iter_num': iter_num,
        'best_val_loss': best_val_loss,
        'config': config,
    }
    torch.save(checkpoint, os.path.join(out_dir, f'ckpt_{iter_num}.pt'))
```

### Resume from Checkpoint

```python
# Load checkpoint
init_from = 'resume'  # or 'gpt2', 'gpt2-medium', etc.

if init_from == 'resume':
    ckpt_path = os.path.join(out_dir, 'ckpt_latest.pt')
    checkpoint = torch.load(ckpt_path, map_location=device)

    # Restore model
    model_args = checkpoint['model_args']
    model = GPT(GPTConfig(**model_args))
    model.load_state_dict(checkpoint['model'])

    # Restore optimizer
    optimizer.load_state_dict(checkpoint['optimizer'])

    # Restore iteration counter
    iter_num = checkpoint['iter_num']
    best_val_loss = checkpoint['best_val_loss']
```

## Fine-Tuning Pretrained Models

### Load OpenAI GPT-2 Weights

```python
# model.py - from_pretrained method
@classmethod
def from_pretrained(cls, model_type):
    """Load pretrained GPT-2 model weights from HuggingFace."""
    from transformers import GPT2LMHeadModel

    # Download from HuggingFace
    model_hf = GPT2LMHeadModel.from_pretrained(model_type)
    sd_hf = model_hf.state_dict()

    # Filter out keys we don't need
    sd_hf_keys = [k for k in sd_hf.keys() if not k.endswith('.attn.masked_bias')]
    sd_hf_keys = [k for k in sd_hf_keys if not k.endswith('.attn.bias')]

    # Create our model
    config = GPTConfig.from_model_type(model_type)
    model = GPT(config)
    sd = model.state_dict()

    # Copy weights (transpose Conv1D → Linear)
    for k in sd_hf_keys:
        if any([k.endswith(w) for w in ['.c_attn.weight', '.c_proj.weight', '.c_fc.weight']]):
            sd[k] = sd_hf[k].t()  # Transpose
        else:
            sd[k] = sd_hf[k]  # Direct copy

    model.load_state_dict(sd)
    return model

# Usage
model = GPT.from_pretrained('gpt2')  # Load GPT-2 (124M)
```

### Fine-Tune on Custom Data

```python
# config/finetune_shakespeare.py
init_from = 'gpt2'  # Start from GPT-2
dataset = 'shakespeare_char'

# Fine-tuning hyperparameters
learning_rate = 3e-5  # Lower LR for fine-tuning
max_iters = 2000      # Short fine-tuning
warmup_iters = 100

# Regularization
weight_decay = 1e-1
dropout = 0.2  # Add dropout

# Run
# python train.py config/finetune_shakespeare.py
```

## Evaluation

### Perplexity

```python
@torch.no_grad()
def estimate_loss():
    model.eval()
    losses = torch.zeros(eval_iters)

    for k in range(eval_iters):
        X, Y = get_batch('val')
        logits, loss = model(X, Y)
        losses[k] = loss.item()

    model.train()
    return losses.mean()

# Usage
val_loss = estimate_loss()
perplexity = math.exp(val_loss)
print(f"Val perplexity: {perplexity:.2f}")
```

### Sample Generation

```python
# sample.py
model.eval()

start = "ROMEO:"  # Prompt
start_ids = encode(start)
x = torch.tensor(start_ids, dtype=torch.long, device=device)[None, ...]

# Generate
with torch.no_grad():
    y = model.generate(x, max_new_tokens=500, temperature=0.8, top_k=200)

print(decode(y[0].tolist()))
```

## Training Times

| Setup | Model | Hardware | Batch Size | Time to Perplexity 10 |
|-------|-------|----------|------------|----------------------|
| Shakespeare | 10M | 1× CPU | 64 | 5 minutes |
| Shakespeare | 10M | 1× T4 GPU | 64 | 1 minute |
| OpenWebText | 124M | 1× A100 | 480 | 7 days |
| OpenWebText | 124M | 8× A100 | 3840 | 4 days |
| OpenWebText | 350M | 8× A100 | 1920 | 14 days |

## Resources

- Training script: https://github.com/karpathy/nanoGPT/blob/master/train.py
- Configs: https://github.com/karpathy/nanoGPT/tree/master/config
- Video walkthrough: "Let's build GPT" (training section)
- GPT-2 paper: https://d4mucfpksywv.cloudfront.net/better-language-models/language_models_are_unsupervised_multitask_learners.pdf




### Data

# NanoGPT Data Preparation

## Data Format

NanoGPT uses **binary token files** for efficient loading:

```
dataset/
├── train.bin       # Training tokens (uint16 array)
├── val.bin         # Validation tokens (uint16 array)
└── meta.pkl        # Metadata (vocab_size, mappings)
```

**Why binary?**
- 100× faster than reading text files
- Memory-mapped loading (no RAM overhead)
- Simple format (just token IDs)

## Character-Level Tokenization

### Shakespeare Example

**Input text**:
```
First Citizen:
Before we proceed any further, hear me speak.

All:
Speak, speak.
```

**Character vocabulary** (65 total):
```python
chars = ['\n', ' ', '!', ',', '.', ':', ';', '?', 'A', 'B', ..., 'z']
stoi = {'\n': 0, ' ': 1, '!': 2, ...}  # char → ID
itos = {0: '\n', 1: ' ', 2: '!', ...}  # ID → char
```

**Tokenization**:
```python
text = "First Citizen:"
tokens = [18, 47, 56, 57, 58, 1, 15, 47, 58, 47, 63, 43, 52, 10]
# F=18, i=47, r=56, s=57, t=58, ' '=1, C=15, ...
```

**Full preparation script**:

```python
# data/shakespeare_char/prepare.py
import os
import requests
import pickle
import numpy as np

# Download Shakespeare dataset
input_file = 'input.txt'
if not os.path.exists(input_file):
    url = 'https://raw.githubusercontent.com/karpathy/char-rnn/master/data/tinyshakespeare/input.txt'
    with open(input_file, 'w') as f:
        f.write(requests.get(url).text)

# Load text
with open(input_file, 'r') as f:
    data = f.read()

print(f"Dataset size: {len(data):,} characters")

# Build vocabulary
chars = sorted(list(set(data)))
vocab_size = len(chars)
print(f"Vocabulary: {vocab_size} unique characters")
print(f"Characters: {''.join(chars[:20])}...")

# Create mappings
stoi = {ch: i for i, ch in enumerate(chars)}
itos = {i: ch for i, ch in enumerate(chars)}

# Encode full dataset
def encode(s):
    return [stoi[c] for c in s]

def decode(l):
    return ''.join([itos[i] for i in l])

# Split train/val (90/10)
n = len(data)
train_data = data[:int(n * 0.9)]
val_data = data[int(n * 0.9):]

# Tokenize
train_ids = encode(train_data)
val_ids = encode(val_data)

print(f"Train: {len(train_ids):,} tokens")
print(f"Val: {len(val_ids):,} tokens")

# Save as binary (uint16)
train_ids = np.array(train_ids, dtype=np.uint16)
val_ids = np.array(val_ids, dtype=np.uint16)

train_ids.tofile('train.bin')
val_ids.tofile('val.bin')

# Save metadata
meta = {
    'vocab_size': vocab_size,
    'itos': itos,
    'stoi': stoi,
}

with open('meta.pkl', 'wb') as f:
    pickle.dump(meta, f)

print("Saved train.bin, val.bin, meta.pkl")
```

**Output**:
```
Dataset size: 1,115,394 characters
Vocabulary: 65 unique characters
Characters:  !$&',-.3:;?ABCDEFGHIJKLMNOPQRSTUVWXYZabcdefghijklmnopqrstuvwxyz
Train: 1,003,854 tokens
Val: 111,540 tokens
Saved train.bin, val.bin, meta.pkl
```

### Custom Character Dataset

```python
# For your own text dataset
text = open('my_data.txt', 'r').read()

# Build vocab
chars = sorted(list(set(text)))
vocab_size = len(chars)

# Create mappings
stoi = {ch: i for i, ch in enumerate(chars)}
itos = {i: ch for i, ch in enumerate(chars)}

# Encode
encode = lambda s: [stoi[c] for c in s]
decode = lambda l: ''.join([itos[i] for i in l])

# Split and save
data = np.array(encode(text), dtype=np.uint16)
n = len(data)
train = data[:int(n*0.9)]
val = data[int(n*0.9):]

train.tofile('data/custom/train.bin')
val.tofile('data/custom/val.bin')

# Save meta
with open('data/custom/meta.pkl', 'wb') as f:
    pickle.dump({'vocab_size': vocab_size, 'itos': itos, 'stoi': stoi}, f)
```

## BPE (Byte Pair Encoding)

### OpenWebText with GPT-2 Tokenizer

**BPE advantages**:
- Handles rare words better (subword units)
- Standard for GPT-2, GPT-3
- Vocabulary: 50,257 tokens

**Preparation script**:

```python
# data/openwebtext/prepare.py
import os
import numpy as np
import tiktoken
from datasets import load_dataset
from tqdm import tqdm

# Number of workers for parallel processing
num_proc = 8
num_proc_load_dataset = num_proc

# Download OpenWebText dataset
dataset = load_dataset("openwebtext", num_proc=num_proc_load_dataset)

# Use GPT-2 tokenizer
enc = tiktoken.get_encoding("gpt2")

def process(example):
    """Tokenize a single example."""
    ids = enc.encode_ordinary(example['text'])  # Tokenize
    ids.append(enc.eot_token)  # Add end-of-text token
    out = {'ids': ids, 'len': len(ids)}
    return out

# Tokenize entire dataset (parallel)
tokenized = dataset.map(
    process,
    remove_columns=['text'],
    desc="Tokenizing",
    num_proc=num_proc,
)

# Concatenate all into one big array
train_ids = np.concatenate([
    np.array(sample['ids'], dtype=np.uint16)
    for sample in tqdm(tokenized['train'], desc="Concatenating")
])

print(f"Total tokens: {len(train_ids):,}")  # ~9 billion tokens

# Save train.bin
train_ids.tofile(os.path.join(os.path.dirname(__file__), 'train.bin'))

# Create val.bin (sample from train)
# Take first 5000 documents for validation
val_ids = np.concatenate([
    np.array(sample['ids'], dtype=np.uint16)
    for sample in tokenized['train'][:5000]
])
val_ids.tofile(os.path.join(os.path.dirname(__file__), 'val.bin'))

# Save metadata
import pickle
meta = {
    'vocab_size': enc.n_vocab,
    'eot_token': enc.eot_token,
}
with open(os.path.join(os.path.dirname(__file__), 'meta.pkl'), 'wb') as f:
    pickle.dump(meta, f)

print(f"Train tokens: {len(train_ids):,}")
print(f"Val tokens: {len(val_ids):,}")
print(f"Vocab size: {enc.n_vocab:,}")
```

**Output**:
```
Total tokens: 9,035,582,198
Train tokens: 9,035,582,198
Val tokens: 4,123,676
Vocab size: 50,257
```

**Time**: 1-2 hours on 8-core CPU

**Disk usage**:
- train.bin: ~18 GB (9B tokens × 2 bytes)
- val.bin: ~8 MB
- Original text: ~54 GB

### BPE Tokenization Example

```python
import tiktoken

enc = tiktoken.get_encoding("gpt2")

# Tokenize
text = "Hello world! This is a test."
tokens = enc.encode_ordinary(text)
print(tokens)
# [15496, 995, 0, 770, 318, 257, 1332, 13]

# Decode
decoded = enc.decode(tokens)
print(decoded)
# "Hello world! This is a test."

# Token → text
print([enc.decode([t]) for t in tokens])
# ['Hello', ' world', '!', ' This', ' is', ' a', ' test', '.']
```

**Subword splitting**:
```python
# Rare word "electroencephalography" is split
tokens = enc.encode_ordinary("electroencephalography")
print([enc.decode([t]) for t in tokens])
# ['elect', 'ro', 'ence', 'ph', 'al', 'ography']
```

## Data Loading

### Memory-Mapped Loading (Efficient)

```python
import numpy as np
import torch

# Load data (memory-mapped, no RAM overhead)
data_dir = 'data/shakespeare_char'
train_data = np.memmap(
    os.path.join(data_dir, 'train.bin'),
    dtype=np.uint16,
    mode='r'
)

print(f"Loaded {len(train_data):,} tokens")  # No actual read yet!

# Get batch (read on-demand)
def get_batch(split):
    data = train_data if split == 'train' else val_data

    # Random indices
    ix = torch.randint(len(data) - block_size, (batch_size,))

    # Extract sequences
    x = torch.stack([torch.from_numpy(data[i:i+block_size].astype(np.int64)) for i in ix])
    y = torch.stack([torch.from_numpy(data[i+1:i+1+block_size].astype(np.int64)) for i in ix])

    # Move to GPU
    x, y = x.to('cuda'), y.to('cuda')

    return x, y

# Usage
X, Y = get_batch('train')
# X shape: (batch_size, block_size)
# Y shape: (batch_size, block_size)
```

**Memory efficiency**:
- 9 GB dataset loaded with ~0 MB RAM
- Only batch data is loaded into memory

### Data Loader (PyTorch)

```python
from torch.utils.data import Dataset, DataLoader

class TokenDataset(Dataset):
    def __init__(self, data_path, block_size):
        self.data = np.memmap(data_path, dtype=np.uint16, mode='r')
        self.block_size = block_size

    def __len__(self):
        return len(self.data) - self.block_size

    def __getitem__(self, idx):
        x = torch.from_numpy(self.data[idx:idx+self.block_size].astype(np.int64))
        y = torch.from_numpy(self.data[idx+1:idx+1+self.block_size].astype(np.int64))
        return x, y

# Create data loader
train_dataset = TokenDataset('data/shakespeare_char/train.bin', block_size=256)
train_loader = DataLoader(
    train_dataset,
    batch_size=64,
    shuffle=True,
    num_workers=4,
    pin_memory=True
)

# Usage
for X, Y in train_loader:
    X, Y = X.to('cuda'), Y.to('cuda')
    # Train...
```

## Custom Datasets

### Wikipedia

```python
from datasets import load_dataset

# Load Wikipedia
dataset = load_dataset("wikipedia", "20220301.en", num_proc=8)

# Tokenize
enc = tiktoken.get_encoding("gpt2")

def tokenize(example):
    ids = enc.encode_ordinary(example['text'])
    return {'ids': ids, 'len': len(ids)}

tokenized = dataset.map(tokenize, num_proc=8, remove_columns=['text', 'title'])

# Save
train_ids = np.concatenate([np.array(x['ids'], dtype=np.uint16) for x in tokenized['train']])
train_ids.tofile('data/wikipedia/train.bin')
```

### Code (GitHub)

```python
from datasets import load_dataset

# Load code dataset (The Stack)
dataset = load_dataset("bigcode/the-stack", data_dir="data/python", num_proc=8)

# Tokenize (same as above)
enc = tiktoken.get_encoding("gpt2")
# ... tokenize and save
```

### Custom Text Files

```python
# Load custom text files
import glob

files = glob.glob('my_dataset/*.txt')
text = ''

for file in files:
    with open(file, 'r') as f:
        text += f.read() + '\n'

# Character-level
chars = sorted(list(set(text)))
stoi = {ch: i for i, ch in enumerate(chars)}
data = np.array([stoi[c] for c in text], dtype=np.uint16)

# Split and save
n = len(data)
train = data[:int(n*0.9)]
val = data[int(n*0.9):]

train.tofile('data/custom/train.bin')
val.tofile('data/custom/val.bin')

# Meta
with open('data/custom/meta.pkl', 'wb') as f:
    pickle.dump({'vocab_size': len(chars), 'itos': {i: ch for i, ch in enumerate(chars)}, 'stoi': stoi}, f)
```

## Data Augmentation (Advanced)

### Random Masking (BERT-style)

```python
def random_mask(tokens, mask_prob=0.15):
    """Randomly mask tokens for denoising objective."""
    mask = torch.rand(tokens.shape) < mask_prob
    tokens[mask] = mask_token_id
    return tokens

# Usage in training
X, Y = get_batch('train')
X_masked = random_mask(X.clone())
logits, loss = model(X_masked, Y)  # Predict original from masked
```

### Document Shuffling

```python
# Shuffle document order (not token order)
# Better generalization than sequential documents

import random

# Load documents
docs = dataset['train']
random.shuffle(docs)

# Concatenate shuffled
train_ids = np.concatenate([np.array(doc['ids'], dtype=np.uint16) for doc in docs])
```

## Benchmarks

| Dataset | Tokens | Vocab | Prep Time | Disk Size |
|---------|--------|-------|-----------|-----------|
| Shakespeare (char) | 1M | 65 | 1 sec | 2 MB |
| TinyStories | 250M | 50K | 5 min | 500 MB |
| OpenWebText | 9B | 50K | 90 min | 18 GB |
| The Pile | 300B | 50K | ~2 days | 600 GB |

## Resources

- Data preparation scripts: https://github.com/karpathy/nanoGPT/tree/master/data
- Tiktoken (BPE tokenizer): https://github.com/openai/tiktoken
- HuggingFace datasets: https://huggingface.co/datasets
- OpenWebText: https://huggingface.co/datasets/Skylion007/openwebtext
- The Stack (code): https://huggingface.co/datasets/bigcode/the-stack




### Architecture

# NanoGPT Architecture

## Model Structure (~300 Lines)

NanoGPT implements a clean GPT-2 architecture in minimal code for educational purposes.

### Complete Model (model.py)

```python
import torch
import torch.nn as nn
from torch.nn import functional as F

class CausalSelfAttention(nn.Module):
    """Multi-head masked self-attention layer."""

    def __init__(self, config):
        super().__init__()
        assert config.n_embd % config.n_head == 0

        # Key, query, value projections for all heads (batched)
        self.c_attn = nn.Linear(config.n_embd, 3 * config.n_embd, bias=config.bias)
        # Output projection
        self.c_proj = nn.Linear(config.n_embd, config.n_embd, bias=config.bias)

        # Regularization
        self.attn_dropout = nn.Dropout(config.dropout)
        self.resid_dropout = nn.Dropout(config.dropout)

        self.n_head = config.n_head
        self.n_embd = config.n_embd
        self.dropout = config.dropout

        # Flash attention flag
        self.flash = hasattr(torch.nn.functional, 'scaled_dot_product_attention')

        if not self.flash:
            # Causal mask (lower triangular)
            self.register_buffer("bias", torch.tril(
                torch.ones(config.block_size, config.block_size)
            ).view(1, 1, config.block_size, config.block_size))

    def forward(self, x):
        B, T, C = x.size()  # batch, seq_len, embedding_dim

        # Calculate Q, K, V for all heads in batch
        q, k, v = self.c_attn(x).split(self.n_embd, dim=2)

        # Reshape for multi-head attention
        k = k.view(B, T, self.n_head, C // self.n_head).transpose(1, 2)  # (B, nh, T, hs)
        q = q.view(B, T, self.n_head, C // self.n_head).transpose(1, 2)  # (B, nh, T, hs)
        v = v.view(B, T, self.n_head, C // self.n_head).transpose(1, 2)  # (B, nh, T, hs)

        # Attention
        if self.flash:
            # Flash Attention (PyTorch 2.0+)
            y = torch.nn.functional.scaled_dot_product_attention(
                q, k, v,
                attn_mask=None,
                dropout_p=self.dropout if self.training else 0,
                is_causal=True
            )
        else:
            # Manual attention implementation
            att = (q @ k.transpose(-2, -1)) * (1.0 / math.sqrt(k.size(-1)))
            att = att.masked_fill(self.bias[:, :, :T, :T] == 0, float('-inf'))
            att = F.softmax(att, dim=-1)
            att = self.attn_dropout(att)
            y = att @ v  # (B, nh, T, hs)

        # Reassemble all head outputs
        y = y.transpose(1, 2).contiguous().view(B, T, C)

        # Output projection
        y = self.resid_dropout(self.c_proj(y))
        return y


class MLP(nn.Module):
    """Feedforward network (2-layer with GELU activation)."""

    def __init__(self, config):
        super().__init__()
        self.c_fc = nn.Linear(config.n_embd, 4 * config.n_embd, bias=config.bias)
        self.gelu = nn.GELU()
        self.c_proj = nn.Linear(4 * config.n_embd, config.n_embd, bias=config.bias)
        self.dropout = nn.Dropout(config.dropout)

    def forward(self, x):
        x = self.c_fc(x)
        x = self.gelu(x)
        x = self.c_proj(x)
        x = self.dropout(x)
        return x


class Block(nn.Module):
    """Transformer block (attention + MLP with residuals)."""

    def __init__(self, config):
        super().__init__()
        self.ln_1 = nn.LayerNorm(config.n_embd)
        self.attn = CausalSelfAttention(config)
        self.ln_2 = nn.LayerNorm(config.n_embd)
        self.mlp = MLP(config)

    def forward(self, x):
        x = x + self.attn(self.ln_1(x))  # Pre-norm + residual
        x = x + self.mlp(self.ln_2(x))   # Pre-norm + residual
        return x


@dataclass
class GPTConfig:
    """GPT model configuration."""
    block_size: int = 1024    # Max sequence length
    vocab_size: int = 50304   # GPT-2 vocab size (50257 rounded up for efficiency)
    n_layer: int = 12         # Number of layers
    n_head: int = 12          # Number of attention heads
    n_embd: int = 768         # Embedding dimension
    dropout: float = 0.0      # Dropout rate
    bias: bool = True         # Use bias in Linear and LayerNorm layers


class GPT(nn.Module):
    """GPT Language Model."""

    def __init__(self, config):
        super().__init__()
        assert config.vocab_size is not None
        assert config.block_size is not None
        self.config = config

        self.transformer = nn.ModuleDict(dict(
            wte=nn.Embedding(config.vocab_size, config.n_embd),  # Token embeddings
            wpe=nn.Embedding(config.block_size, config.n_embd),  # Position embeddings
            drop=nn.Dropout(config.dropout),
            h=nn.ModuleList([Block(config) for _ in range(config.n_layer)]),
            ln_f=nn.LayerNorm(config.n_embd),
        ))
        self.lm_head = nn.Linear(config.n_embd, config.vocab_size, bias=False)

        # Weight tying (share embeddings and output projection)
        self.transformer.wte.weight = self.lm_head.weight

        # Initialize weights
        self.apply(self._init_weights)
        # Apply special scaled init to residual projections
        for pn, p in self.named_parameters():
            if pn.endswith('c_proj.weight'):
                torch.nn.init.normal_(p, mean=0.0, std=0.02/math.sqrt(2 * config.n_layer))

    def _init_weights(self, module):
        if isinstance(module, nn.Linear):
            torch.nn.init.normal_(module.weight, mean=0.0, std=0.02)
            if module.bias is not None:
                torch.nn.init.zeros_(module.bias)
        elif isinstance(module, nn.Embedding):
            torch.nn.init.normal_(module.weight, mean=0.0, std=0.02)

    def forward(self, idx, targets=None):
        device = idx.device
        b, t = idx.size()
        assert t <= self.config.block_size, f"Cannot forward sequence length {t}, max is {self.config.block_size}"

        # Generate position indices
        pos = torch.arange(0, t, dtype=torch.long, device=device).unsqueeze(0)  # (1, t)

        # Forward the GPT model
        tok_emb = self.transformer.wte(idx)  # Token embeddings (b, t, n_embd)
        pos_emb = self.transformer.wpe(pos)  # Position embeddings (1, t, n_embd)
        x = self.transformer.drop(tok_emb + pos_emb)

        for block in self.transformer.h:
            x = block(x)

        x = self.transformer.ln_f(x)

        if targets is not None:
            # Training mode: compute loss
            logits = self.lm_head(x)
            loss = F.cross_entropy(logits.view(-1, logits.size(-1)), targets.view(-1), ignore_index=-1)
        else:
            # Inference mode: only compute logits for last token
            logits = self.lm_head(x[:, [-1], :])  # (b, 1, vocab_size)
            loss = None

        return logits, loss

    @torch.no_grad()
    def generate(self, idx, max_new_tokens, temperature=1.0, top_k=None):
        """Generate new tokens autoregressively."""
        for _ in range(max_new_tokens):
            # Crop context if needed
            idx_cond = idx if idx.size(1) <= self.config.block_size else idx[:, -self.config.block_size:]

            # Forward pass
            logits, _ = self(idx_cond)
            logits = logits[:, -1, :] / temperature  # Scale by temperature

            # Optionally crop logits to top k
            if top_k is not None:
                v, _ = torch.topk(logits, min(top_k, logits.size(-1)))
                logits[logits < v[:, [-1]]] = -float('Inf')

            # Sample from distribution
            probs = F.softmax(logits, dim=-1)
            idx_next = torch.multinomial(probs, num_samples=1)

            # Append to sequence
            idx = torch.cat((idx, idx_next), dim=1)

        return idx
```

## Key Design Decisions

### 1. Pre-Norm vs Post-Norm

**NanoGPT uses Pre-Norm** (LayerNorm before sub-layers):

```python
# Pre-norm (NanoGPT)
x = x + attn(ln(x))
x = x + mlp(ln(x))

# Post-norm (original Transformer)
x = ln(x + attn(x))
x = ln(x + mlp(x))
```

**Why Pre-Norm?**
- More stable training (no gradient explosion)
- Used in GPT-2, GPT-3
- Standard for large language models

### 2. Weight Tying

**Shared weights between embeddings and output**:

```python
self.transformer.wte.weight = self.lm_head.weight
```

**Why?**
- Reduces parameters: `vocab_size × n_embd` saved
- Improves training (same semantic space)
- Standard in GPT-2

### 3. Scaled Residual Initialization

```python
# Scale down residual projections by layer depth
std = 0.02 / math.sqrt(2 * n_layer)
torch.nn.init.normal_(c_proj.weight, mean=0.0, std=std)
```

**Why?**
- Prevents gradient explosion in deep networks
- Each residual path contributes ~equally
- From GPT-2 paper

### 4. Flash Attention

```python
if hasattr(torch.nn.functional, 'scaled_dot_product_attention'):
    # Use PyTorch 2.0 Flash Attention (2× faster!)
    y = F.scaled_dot_product_attention(q, k, v, is_causal=True)
else:
    # Fallback to manual attention
    att = (q @ k.T) / sqrt(d)
    att = masked_fill(att, causal_mask, -inf)
    y = softmax(att) @ v
```

**Speedup**: 2× faster with same accuracy

## Model Sizes

| Model | n_layer | n_head | n_embd | Params | Config Name |
|-------|---------|--------|--------|--------|-------------|
| GPT-2 Small | 12 | 12 | 768 | 124M | `gpt2` |
| GPT-2 Medium | 24 | 16 | 1024 | 350M | `gpt2-medium` |
| GPT-2 Large | 36 | 20 | 1280 | 774M | `gpt2-large` |
| GPT-2 XL | 48 | 25 | 1600 | 1558M | `gpt2-xl` |

**NanoGPT default** (Shakespeare):
```python
config = GPTConfig(
    block_size=256,   # Short context for char-level
    vocab_size=65,    # Small vocab (a-z, A-Z, punctuation)
    n_layer=6,        # Shallow network
    n_head=6,
    n_embd=384,       # Small embeddings
    dropout=0.2       # Regularization
)
# Total: ~10M parameters
```

## Attention Visualization

```python
# What each token attends to (lower triangular)
# Token t can only attend to tokens 0...t

Attention Pattern (causal mask):
    t=0  t=1  t=2  t=3
t=0  ✓    -    -    -
t=1  ✓    ✓    -    -
t=2  ✓    ✓    ✓    -
t=3  ✓    ✓    ✓    ✓

# Prevents "cheating" by looking at future tokens
```

## Residual Stream

**Information flow through residuals**:

```python
# Input
x = token_emb + pos_emb

# Block 1
x = x + attn_1(ln(x))   # Attention adds to residual
x = x + mlp_1(ln(x))    # MLP adds to residual

# Block 2
x = x + attn_2(ln(x))
x = x + mlp_2(ln(x))

# ... (repeat for all layers)

# Output
logits = lm_head(ln(x))
```

**Key insight**: Each layer refines the representation, residuals preserve gradients

## Tokenization

### Character-Level (Shakespeare)

```python
# data/shakespeare_char/prepare.py
text = open('input.txt', 'r').read()
chars = sorted(list(set(text)))  # ['!', ',', '.', 'A', 'B', ..., 'z']
vocab_size = len(chars)  # 65

stoi = {ch: i for i, ch in enumerate(chars)}
itos = {i: ch for i, ch in enumerate(chars)}

# Encode
encode = lambda s: [stoi[c] for c in s]
decode = lambda l: ''.join([itos[i] for i in l])

data = torch.tensor(encode(text), dtype=torch.long)
```

### BPE (GPT-2)

```python
# data/openwebtext/prepare.py
import tiktoken

enc = tiktoken.get_encoding("gpt2")  # GPT-2 BPE tokenizer
vocab_size = enc.n_vocab  # 50257

# Encode
tokens = enc.encode_ordinary("Hello world")  # [15496, 995]

# Decode
text = enc.decode(tokens)  # "Hello world"
```

## Resources

- **GitHub**: https://github.com/karpathy/nanoGPT ⭐ 48,000+
- **Video**: "Let's build GPT" by Andrej Karpathy
- **Paper**: "Attention is All You Need" (Vaswani et al.)
- **Paper**: "Language Models are Unsupervised Multitask Learners" (GPT-2)
- **Code walkthrough**: https://github.com/karpathy/nanoGPT/blob/master/ARCHITECTURE.md




---

## 🚀 Usage

**Reference this template:** `@skill-nanogpt.md`


**Platform-specific:**
- **GitHub Copilot**: Add to `.github/copilot-instructions.md`
- **Augment Code**: Use `aug context add` command
- **Cursor/Windsurf**: Reference in chat with `@filename`
- **Claude**: Add to Project Knowledge
- **ChatGPT**: Add to Custom GPT configuration
