---
id: skill-blip-2-vision-language
type: skill
name: blip-2-vision-language
description: Vision-language pre-training framework bridging frozen image encoders
  and LLMs. Use when you need image captioning, visual question answering, image-text
  retrieval, or multimodal chat with state-of-the-art zero-shot performance.
category: ai-research
complexity: medium
keywords:
- deployment
- github
- optimization
- performance
- python
capabilities: []
token_estimate: 2077
has_references: true
reference_count: 2
---

<!-- Converted from Claude Skill Template -->
<!-- Complexity: Medium -->
<!-- Estimated Tokens: ~2,077 -->
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




# blip-2-vision-language

> Vision-language pre-training framework bridging frozen image encoders and LLMs. Use when you need image captioning, visual question answering, image-text retrieval, or multimodal chat with state-of-the-art zero-shot performance.

# BLIP-2: Vision-Language Pre-training

Comprehensive guide to using Salesforce's BLIP-2 for vision-language tasks with frozen image encoders and large language models.

## When to use BLIP-2

**Use BLIP-2 when:**
- Need high-quality image captioning with natural descriptions
- Building visual question answering (VQA) systems
- Require zero-shot image-text understanding without task-specific training
- Want to leverage LLM reasoning for visual tasks
- Building multimodal conversational AI
- Need image-text retrieval or matching

**Key features:**
- **Q-Former architecture**: Lightweight query transformer bridges vision and language
- **Frozen backbone efficiency**: No need to fine-tune large vision/language models
- **Multiple LLM backends**: OPT (2.7B, 6.7B) and FlanT5 (XL, XXL)
- **Zero-shot capabilities**: Strong performance without task-specific training
- **Efficient training**: Only trains Q-Former (~188M parameters)
- **State-of-the-art results**: Beats larger models on VQA benchmarks

**Use alternatives instead:**
- **LLaVA**: For instruction-following multimodal chat
- **InstructBLIP**: For improved instruction-following (BLIP-2 successor)
- **GPT-4V/Claude 3**: For production multimodal chat (proprietary)
- **CLIP**: For simple image-text similarity without generation
- **Flamingo**: For few-shot visual learning

## Quick start

### Installation

```bash
# HuggingFace Transformers (recommended)
pip install transformers accelerate torch Pillow

# Or LAVIS library (Salesforce official)
pip install salesforce-lavis
```

### Basic image captioning

```python
import torch
from PIL import Image
from transformers import Blip2Processor, Blip2ForConditionalGeneration

# Load model and processor
processor = Blip2Processor.from_pretrained("Salesforce/blip2-opt-2.7b")
model = Blip2ForConditionalGeneration.from_pretrained(
    "Salesforce/blip2-opt-2.7b",
    torch_dtype=torch.float16,
    device_map="auto"
)

# Load image
image = Image.open("photo.jpg").convert("RGB")

# Generate caption
inputs = processor(images=image, return_tensors="pt").to("cuda", torch.float16)
generated_ids = model.generate(**inputs, max_new_tokens=50)
caption = processor.batch_decode(generated_ids, skip_special_tokens=True)[0]
print(caption)
```

### Visual question answering

```python
# Ask a question about the image
question = "What color is the car in this image?"

inputs = processor(images=image, text=question, return_tensors="pt").to("cuda", torch.float16)
generated_ids = model.generate(**inputs, max_new_tokens=50)
answer = processor.batch_decode(generated_ids, skip_special_tokens=True)[0]
print(answer)
```

### Using LAVIS library

```python
import torch
from lavis.models import load_model_and_preprocess
from PIL import Image

# Load model
device = torch.device("cuda" if torch.cuda.is_available() else "cpu")
model, vis_processors, txt_processors = load_model_and_preprocess(
    name="blip2_opt",
    model_type="pretrain_opt2.7b",
    is_eval=True,
    device=device
)

# Process image
image = Image.open("photo.jpg").convert("RGB")
image = vis_processors["eval"](image).unsqueeze(0).to(device)

# Caption
caption = model.generate({"image": image})
print(caption)

# VQA
question = txt_processors["eval"]("What is in this image?")
answer = model.generate({"image": image, "prompt": question})
print(answer)
```

## Core concepts

### Architecture overview

```
BLIP-2 Architecture:
┌─────────────────────────────────────────────────────────────┐
│                        Q-Former                              │
│  ┌─────────────────────────────────────────────────────┐    │
│  │     Learned Queries (32 queries × 768 dim)          │    │
│  └────────────────────────┬────────────────────────────┘    │
│                           │                                  │
│  ┌────────────────────────▼────────────────────────────┐    │
│  │    Cross-Attention with Image Features               │    │
│  └────────────────────────┬────────────────────────────┘    │
│                           │                                  │
│  ┌────────────────────────▼────────────────────────────┐    │
│  │    Self-Attention Layers (Transformer)               │    │
│  └────────────────────────┬────────────────────────────┘    │
└───────────────────────────┼─────────────────────────────────┘
                            │
┌───────────────────────────▼─────────────────────────────────┐
│  Frozen Vision Encoder    │      Frozen LLM                  │
│  (ViT-G/14 from EVA-CLIP) │      (OPT or FlanT5)            │
└─────────────────────────────────────────────────────────────┘
```

### Model variants

| Model | LLM Backend | Size | Use Case |
|-------|-------------|------|----------|
| `blip2-opt-2.7b` | OPT-2.7B | ~4GB | General captioning, VQA |
| `blip2-opt-6.7b` | OPT-6.7B | ~8GB | Better reasoning |
| `blip2-flan-t5-xl` | FlanT5-XL | ~5GB | Instruction following |
| `blip2-flan-t5-xxl` | FlanT5-XXL | ~13GB | Best quality |

### Q-Former components

| Component | Description | Parameters |
|-----------|-------------|------------|
| Learned queries | Fixed set of learnable embeddings | 32 × 768 |
| Image transformer | Cross-attention to vision features | ~108M |
| Text transformer | Self-attention for text | ~108M |
| Linear projection | Maps to LLM dimension | Varies |

## Advanced usage

### Batch processing

```python
from PIL import Image
import torch

# Load multiple images
images = [Image.open(f"image_{i}.jpg").convert("RGB") for i in range(4)]
questions = [
    "What is shown in this image?",
    "Describe the scene.",
    "What colors are prominent?",
    "Is there a person in this image?"
]

# Process batch
inputs = processor(
    images=images,
    text=questions,
    return_tensors="pt",
    padding=True
).to("cuda", torch.float16)

# Generate
generated_ids = model.generate(**inputs, max_new_tokens=50)
answers = processor.batch_decode(generated_ids, skip_special_tokens=True)

for q, a in zip(questions, answers):
    print(f"Q: {q}\nA: {a}\n")
```

### Controlling generation

```python
# Control generation parameters
generated_ids = model.generate(
    **inputs,
    max_new_tokens=100,
    min_length=20,
    num_beams=5,              # Beam search
    no_repeat_ngram_size=2,   # Avoid repetition
    top_p=0.9,                # Nucleus sampling
    temperature=0.7,          # Creativity
    do_sample=True,           # Enable sampling
)

# For deterministic output
generated_ids = model.generate(
    **inputs,
    max_new_tokens=50,
    num_beams=5,
    do_sample=False,
)
```

### Memory optimization

```python
# 8-bit quantization
from transformers import BitsAndBytesConfig

quantization_config = BitsAndBytesConfig(load_in_8bit=True)

model = Blip2ForConditionalGeneration.from_pretrained(
    "Salesforce/blip2-opt-6.7b",
    quantization_config=quantization_config,
    device_map="auto"
)

# 4-bit quantization (more aggressive)
quantization_config = BitsAndBytesConfig(
    load_in_4bit=True,
    bnb_4bit_compute_dtype=torch.float16
)

model = Blip2ForConditionalGeneration.from_pretrained(
    "Salesforce/blip2-flan-t5-xxl",
    quantization_config=quantization_config,
    device_map="auto"
)
```

### Image-text matching

```python
# Using LAVIS for ITM (Image-Text Matching)
from lavis.models import load_model_and_preprocess

model, vis_processors, txt_processors = load_model_and_preprocess(
    name="blip2_image_text_matching",
    model_type="pretrain",
    is_eval=True,
    device=device
)

image = vis_processors["eval"](raw_image).unsqueeze(0).to(device)
text = txt_processors["eval"]("a dog sitting on grass")

# Get matching score
itm_output = model({"image": image, "text_input": text}, match_head="itm")
itm_scores = torch.nn.functional.softmax(itm_output, dim=1)
print(f"Match probability: {itm_scores[:, 1].item():.3f}")
```

### Feature extraction

```python
# Extract image features with Q-Former
from lavis.models import load_model_and_preprocess

model, vis_processors, _ = load_model_and_preprocess(
    name="blip2_feature_extractor",
    model_type="pretrain",
    is_eval=True,
    device=device
)

image = vis_processors["eval"](raw_image).unsqueeze(0).to(device)

# Get features
features = model.extract_features({"image": image}, mode="image")
image_embeds = features.image_embeds  # Shape: [1, 32, 768]
image_features = features.image_embeds_proj  # Projected for matching
```

## Common workflows

### Workflow 1: Image captioning pipeline

```python
import torch
from PIL import Image
from transformers import Blip2Processor, Blip2ForConditionalGeneration
from pathlib import Path

class ImageCaptioner:
    def __init__(self, model_name="Salesforce/blip2-opt-2.7b"):
        self.processor = Blip2Processor.from_pretrained(model_name)
        self.model = Blip2ForConditionalGeneration.from_pretrained(
            model_name,
            torch_dtype=torch.float16,
            device_map="auto"
        )

    def caption(self, image_path: str, prompt: str = None) -> str:
        image = Image.open(image_path).convert("RGB")

        if prompt:
            inputs = self.processor(images=image, text=prompt, return_tensors="pt")
        else:
            inputs = self.processor(images=image, return_tensors="pt")

        inputs = inputs.to("cuda", torch.float16)

        generated_ids = self.model.generate(
            **inputs,
            max_new_tokens=50,
            num_beams=5
        )

        return self.processor.decode(generated_ids[0], skip_special_tokens=True)

    def caption_batch(self, image_paths: list, prompt: str = None) -> list:
        images = [Image.open(p).convert("RGB") for p in image_paths]

        if prompt:
            inputs = self.processor(
                images=images,
                text=[prompt] * len(images),
                return_tensors="pt",
                padding=True
            )
        else:
            inputs = self.processor(images=images, return_tensors="pt", padding=True)

        inputs = inputs.to("cuda", torch.float16)

        generated_ids = self.model.generate(**inputs, max_new_tokens=50)
        return self.processor.batch_decode(generated_ids, skip_special_tokens=True)

# Usage
captioner = ImageCaptioner()

# Single image
caption = captioner.caption("photo.jpg")
print(f"Caption: {caption}")

# With prompt for style
caption = captioner.caption("photo.jpg", "a detailed description of")
print(f"Detailed: {caption}")

# Batch processing
captions = captioner.caption_batch(["img1.jpg", "img2.jpg", "img3.jpg"])
for i, cap in enumerate(captions):
    print(f"Image {i+1}: {cap}")
```

### Workflow 2: Visual Q&A system

```python
class VisualQA:
    def __init__(self, model_name="Salesforce/blip2-flan-t5-xl"):
        self.processor = Blip2Processor.from_pretrained(model_name)
        self.model = Blip2ForConditionalGeneration.from_pretrained(
            model_name,
            torch_dtype=torch.float16,
            device_map="auto"
        )
        self.current_image = None
        self.current_inputs = None

    def set_image(self, image_path: str):
        """Load image for multiple questions."""
        self.current_image = Image.open(image_path).convert("RGB")

    def ask(self, question: str) -> str:
        """Ask a question about the current image."""
        if self.current_image is None:
            raise ValueError("No image set. Call set_image() first.")

        # Format question for FlanT5
        prompt = f"Question: {question} Answer:"

        inputs = self.processor(
            images=self.current_image,
            text=prompt,
            return_tensors="pt"
        ).to("cuda", torch.float16)

        generated_ids = self.model.generate(
            **inputs,
            max_new_tokens=50,
            num_beams=5
        )

        return self.processor.decode(generated_ids[0], skip_special_tokens=True)

    def ask_multiple(self, questions: list) -> dict:
        """Ask multiple questions about current image."""
        return {q: self.ask(q) for q in questions}

# Usage
vqa = VisualQA()
vqa.set_image("scene.jpg")

# Ask questions
print(vqa.ask("What objects are in this image?"))
print(vqa.ask("What is the weather like?"))
print(vqa.ask("How many people are there?"))

# Batch questions
results = vqa.ask_multiple([
    "What is the main subject?",
    "What colors are dominant?",
    "Is this indoors or outdoors?"
])
```

### Workflow 3: Image search/retrieval

```python
import torch
import numpy as np
from PIL import Image
from lavis.models import load_model_and_preprocess

class ImageSearchEngine:
    def __init__(self):
        self.device = torch.device("cuda" if torch.cuda.is_available() else "cpu")
        self.model, self.vis_processors, self.txt_processors = load_model_and_preprocess(
            name="blip2_feature_extractor",
            model_type="pretrain",
            is_eval=True,
            device=self.device
        )
        self.image_features = []
        self.image_paths = []

    def index_images(self, image_paths: list):
        """Build index from images."""
        self.image_paths = image_paths

        for path in image_paths:
            image = Image.open(path).convert("RGB")
            image = self.vis_processors["eval"](image).unsqueeze(0).to(self.device)

            with torch.no_grad():
                features = self.model.extract_features({"image": image}, mode="image")
                # Use projected features for matching
                self.image_features.append(
                    features.image_embeds_proj.mean(dim=1).cpu().numpy()
                )

        self.image_features = np.vstack(self.image_features)

    def search(self, query: str, top_k: int = 5) -> list:
        """Search images by text query."""
        # Get text features
        text = self.txt_processors["eval"](query)
        text_input = {"text_input": [text]}

        with torch.no_grad():
            text_features = self.model.extract_features(text_input, mode="text")
            text_embeds = text_features.text_embeds_proj[:, 0].cpu().numpy()

        # Compute similarities
        similarities = np.dot(self.image_features, text_embeds.T).squeeze()
        top_indices = np.argsort(similarities)[::-1][:top_k]

        return [(self.image_paths[i], similarities[i]) for i in top_indices]

# Usage
engine = ImageSearchEngine()
engine.index_images(["img1.jpg", "img2.jpg", "img3.jpg", ...])

# Search
results = engine.search("a sunset over the ocean", top_k=5)
for path, score in results:
    print(f"{path}: {score:.3f}")
```

## Output format

### Generation output

```python
# Direct generation returns token IDs
generated_ids = model.generate(**inputs, max_new_tokens=50)
# Shape: [batch_size, sequence_length]

# Decode to text
text = processor.batch_decode(generated_ids, skip_special_tokens=True)
# Returns: list of strings
```

### Feature extraction output

```python
# Q-Former outputs
features = model.extract_features({"image": image}, mode="image")

features.image_embeds          # [B, 32, 768] - Q-Former outputs
features.image_embeds_proj     # [B, 32, 256] - Projected for matching
features.text_embeds          # [B, seq_len, 768] - Text features
features.text_embeds_proj     # [B, 256] - Projected text (CLS)
```

## Performance optimization

### GPU memory requirements

| Model | FP16 VRAM | INT8 VRAM | INT4 VRAM |
|-------|-----------|-----------|-----------|
| blip2-opt-2.7b | ~8GB | ~5GB | ~3GB |
| blip2-opt-6.7b | ~16GB | ~9GB | ~5GB |
| blip2-flan-t5-xl | ~10GB | ~6GB | ~4GB |
| blip2-flan-t5-xxl | ~26GB | ~14GB | ~8GB |

### Speed optimization

```python
# Use Flash Attention if available
model = Blip2ForConditionalGeneration.from_pretrained(
    "Salesforce/blip2-opt-2.7b",
    torch_dtype=torch.float16,
    attn_implementation="flash_attention_2",  # Requires flash-attn
    device_map="auto"
)

# Compile model (PyTorch 2.0+)
model = torch.compile(model)

# Use smaller images (if quality allows)
processor = Blip2Processor.from_pretrained("Salesforce/blip2-opt-2.7b")
# Default is 224x224, which is optimal
```

## Common issues

| Issue | Solution |
|-------|----------|
| CUDA OOM | Use INT8/INT4 quantization, smaller model |
| Slow generation | Use greedy decoding, reduce max_new_tokens |
| Poor captions | Try FlanT5 variant, use prompts |
| Hallucinations | Lower temperature, use beam search |
| Wrong answers | Rephrase question, provide context |

## References

- **[Advanced Usage](references/advanced-usage.md)** - Fine-tuning, integration, deployment
- **[Troubleshooting](references/troubleshooting.md)** - Common issues and solutions

## Resources

- **Paper**: https://arxiv.org/abs/2301.12597
- **GitHub (LAVIS)**: https://github.com/salesforce/LAVIS
- **HuggingFace**: https://huggingface.co/Salesforce/blip2-opt-2.7b
- **Demo**: https://huggingface.co/spaces/Salesforce/BLIP2
- **InstructBLIP**: https://arxiv.org/abs/2305.06500 (successor)


---


## 📚 Reference Materials


### Advanced Usage

# BLIP-2 Advanced Usage Guide

## Fine-tuning BLIP-2

### LoRA fine-tuning (recommended)

```python
import torch
from transformers import Blip2ForConditionalGeneration, Blip2Processor
from peft import LoraConfig, get_peft_model

# Load base model
model = Blip2ForConditionalGeneration.from_pretrained(
    "Salesforce/blip2-opt-2.7b",
    torch_dtype=torch.float16,
    device_map="auto"
)

# Configure LoRA for the language model
lora_config = LoraConfig(
    r=16,
    lora_alpha=32,
    target_modules=["q_proj", "v_proj", "k_proj", "out_proj"],
    lora_dropout=0.05,
    bias="none",
    task_type="CAUSAL_LM"
)

# Apply LoRA
model = get_peft_model(model, lora_config)
model.print_trainable_parameters()
# trainable params: ~4M, all params: ~3.8B (0.1%)
```

### Fine-tuning Q-Former only

```python
# Freeze everything except Q-Former
for name, param in model.named_parameters():
    if "qformer" not in name.lower():
        param.requires_grad = False
    else:
        param.requires_grad = True

# Check trainable parameters
trainable = sum(p.numel() for p in model.parameters() if p.requires_grad)
total = sum(p.numel() for p in model.parameters())
print(f"Trainable: {trainable:,} / {total:,} ({100*trainable/total:.2f}%)")
```

### Custom dataset for fine-tuning

```python
import torch
from torch.utils.data import Dataset, DataLoader
from PIL import Image

class CaptionDataset(Dataset):
    def __init__(self, data, processor, max_length=128):
        self.data = data  # List of {"image_path": str, "caption": str}
        self.processor = processor
        self.max_length = max_length

    def __len__(self):
        return len(self.data)

    def __getitem__(self, idx):
        item = self.data[idx]
        image = Image.open(item["image_path"]).convert("RGB")

        # Process inputs
        encoding = self.processor(
            images=image,
            text=item["caption"],
            padding="max_length",
            truncation=True,
            max_length=self.max_length,
            return_tensors="pt"
        )

        # Remove batch dimension
        encoding = {k: v.squeeze(0) for k, v in encoding.items()}

        # Labels for language modeling
        encoding["labels"] = encoding["input_ids"].clone()

        return encoding

# Create dataloader
dataset = CaptionDataset(train_data, processor)
dataloader = DataLoader(dataset, batch_size=8, shuffle=True)
```

### Training loop

```python
from transformers import AdamW, get_linear_schedule_with_warmup
from tqdm import tqdm

# Optimizer
optimizer = AdamW(model.parameters(), lr=1e-5, weight_decay=0.01)

# Scheduler
num_epochs = 3
num_training_steps = len(dataloader) * num_epochs
scheduler = get_linear_schedule_with_warmup(
    optimizer,
    num_warmup_steps=num_training_steps // 10,
    num_training_steps=num_training_steps
)

# Training
model.train()
for epoch in range(num_epochs):
    total_loss = 0

    for batch in tqdm(dataloader, desc=f"Epoch {epoch+1}"):
        batch = {k: v.to("cuda") for k, v in batch.items()}

        outputs = model(**batch)
        loss = outputs.loss

        loss.backward()
        torch.nn.utils.clip_grad_norm_(model.parameters(), 1.0)

        optimizer.step()
        scheduler.step()
        optimizer.zero_grad()

        total_loss += loss.item()

    avg_loss = total_loss / len(dataloader)
    print(f"Epoch {epoch+1} - Loss: {avg_loss:.4f}")

# Save fine-tuned model
model.save_pretrained("blip2-finetuned")
processor.save_pretrained("blip2-finetuned")
```

### Fine-tuning with LAVIS

```python
from lavis.models import load_model_and_preprocess
from lavis.common.registry import registry
from lavis.datasets.builders import load_dataset

# Load model
model, vis_processors, txt_processors = load_model_and_preprocess(
    name="blip2_opt",
    model_type="pretrain_opt2.7b",
    is_eval=False,  # Training mode
    device="cuda"
)

# Load dataset
dataset = load_dataset("coco_caption")

# Get trainer class
runner_cls = registry.get_runner_class("runner_base")
runner = runner_cls(
    cfg=cfg,
    task=task,
    model=model,
    datasets=datasets
)

# Train
runner.train()
```

## Multi-GPU Training

### DataParallel

```python
import torch.nn as nn

model = Blip2ForConditionalGeneration.from_pretrained(
    "Salesforce/blip2-opt-2.7b",
    torch_dtype=torch.float16
)

# Wrap with DataParallel
if torch.cuda.device_count() > 1:
    model = nn.DataParallel(model)

model.to("cuda")
```

### DistributedDataParallel

```python
import torch.distributed as dist
from torch.nn.parallel import DistributedDataParallel as DDP
from torch.utils.data.distributed import DistributedSampler

def setup(rank, world_size):
    dist.init_process_group("nccl", rank=rank, world_size=world_size)
    torch.cuda.set_device(rank)

def train(rank, world_size):
    setup(rank, world_size)

    model = Blip2ForConditionalGeneration.from_pretrained(
        "Salesforce/blip2-opt-2.7b",
        torch_dtype=torch.float16
    ).to(rank)

    model = DDP(model, device_ids=[rank])

    # Use DistributedSampler
    sampler = DistributedSampler(dataset, num_replicas=world_size, rank=rank)
    dataloader = DataLoader(dataset, sampler=sampler, batch_size=4)

    # Training loop
    for epoch in range(num_epochs):
        sampler.set_epoch(epoch)
        for batch in dataloader:
            # ... training code
            pass

    dist.destroy_process_group()

# Launch
import torch.multiprocessing as mp
world_size = torch.cuda.device_count()
mp.spawn(train, args=(world_size,), nprocs=world_size)
```

### Accelerate integration

```python
from accelerate import Accelerator
from transformers import Blip2ForConditionalGeneration, Blip2Processor

accelerator = Accelerator(mixed_precision="fp16")

model = Blip2ForConditionalGeneration.from_pretrained("Salesforce/blip2-opt-2.7b")
optimizer = torch.optim.AdamW(model.parameters(), lr=1e-5)

# Prepare for distributed training
model, optimizer, dataloader = accelerator.prepare(
    model, optimizer, dataloader
)

# Training loop
for batch in dataloader:
    outputs = model(**batch)
    loss = outputs.loss

    accelerator.backward(loss)
    optimizer.step()
    optimizer.zero_grad()
```

## Integration Patterns

### Gradio interface

```python
import gradio as gr
import torch
from PIL import Image
from transformers import Blip2Processor, Blip2ForConditionalGeneration

# Load model
processor = Blip2Processor.from_pretrained("Salesforce/blip2-opt-2.7b")
model = Blip2ForConditionalGeneration.from_pretrained(
    "Salesforce/blip2-opt-2.7b",
    torch_dtype=torch.float16,
    device_map="auto"
)

def caption_image(image, question=None):
    if question:
        inputs = processor(images=image, text=question, return_tensors="pt")
    else:
        inputs = processor(images=image, return_tensors="pt")

    inputs = inputs.to("cuda", torch.float16)

    generated_ids = model.generate(**inputs, max_new_tokens=100)
    return processor.decode(generated_ids[0], skip_special_tokens=True)

# Create interface
demo = gr.Interface(
    fn=caption_image,
    inputs=[
        gr.Image(type="pil", label="Upload Image"),
        gr.Textbox(label="Question (optional)", placeholder="What is in this image?")
    ],
    outputs=gr.Textbox(label="Response"),
    title="BLIP-2 Demo",
    examples=[
        ["example1.jpg", None],
        ["example2.jpg", "What colors are in this image?"]
    ]
)

demo.launch()
```

### FastAPI server

```python
from fastapi import FastAPI, UploadFile, File
from PIL import Image
import torch
from transformers import Blip2Processor, Blip2ForConditionalGeneration
import io

app = FastAPI()

# Load model at startup
processor = None
model = None

@app.on_event("startup")
async def load_model():
    global processor, model
    processor = Blip2Processor.from_pretrained("Salesforce/blip2-opt-2.7b")
    model = Blip2ForConditionalGeneration.from_pretrained(
        "Salesforce/blip2-opt-2.7b",
        torch_dtype=torch.float16,
        device_map="auto"
    )

@app.post("/caption")
async def caption(file: UploadFile = File(...), question: str = None):
    # Read image
    contents = await file.read()
    image = Image.open(io.BytesIO(contents)).convert("RGB")

    # Process
    if question:
        inputs = processor(images=image, text=question, return_tensors="pt")
    else:
        inputs = processor(images=image, return_tensors="pt")

    inputs = inputs.to("cuda", torch.float16)

    # Generate
    generated_ids = model.generate(**inputs, max_new_tokens=100)
    caption = processor.decode(generated_ids[0], skip_special_tokens=True)

    return {"caption": caption}

@app.post("/batch_caption")
async def batch_caption(files: list[UploadFile] = File(...)):
    images = []
    for file in files:
        contents = await file.read()
        images.append(Image.open(io.BytesIO(contents)).convert("RGB"))

    inputs = processor(images=images, return_tensors="pt", padding=True)
    inputs = inputs.to("cuda", torch.float16)

    generated_ids = model.generate(**inputs, max_new_tokens=100)
    captions = processor.batch_decode(generated_ids, skip_special_tokens=True)

    return {"captions": captions}

# Run: uvicorn server:app --host 0.0.0.0 --port 8000
```

### LangChain integration

```python
from langchain.tools import BaseTool
from langchain.agents import initialize_agent, AgentType
from langchain.llms import OpenAI
import torch
from PIL import Image
from transformers import Blip2Processor, Blip2ForConditionalGeneration

class ImageCaptionTool(BaseTool):
    name = "image_caption"
    description = "Generate a caption for an image. Input should be an image file path."

    def __init__(self):
        super().__init__()
        self.processor = Blip2Processor.from_pretrained("Salesforce/blip2-opt-2.7b")
        self.model = Blip2ForConditionalGeneration.from_pretrained(
            "Salesforce/blip2-opt-2.7b",
            torch_dtype=torch.float16,
            device_map="auto"
        )

    def _run(self, image_path: str) -> str:
        image = Image.open(image_path).convert("RGB")
        inputs = self.processor(images=image, return_tensors="pt").to("cuda", torch.float16)
        generated_ids = self.model.generate(**inputs, max_new_tokens=50)
        return self.processor.decode(generated_ids[0], skip_special_tokens=True)

class VisualQATool(BaseTool):
    name = "visual_qa"
    description = "Answer questions about an image. Input format: 'image_path|question'"

    def __init__(self, processor, model):
        super().__init__()
        self.processor = processor
        self.model = model

    def _run(self, query: str) -> str:
        image_path, question = query.split("|")
        image = Image.open(image_path.strip()).convert("RGB")
        inputs = self.processor(images=image, text=question.strip(), return_tensors="pt")
        inputs = inputs.to("cuda", torch.float16)
        generated_ids = self.model.generate(**inputs, max_new_tokens=50)
        return self.processor.decode(generated_ids[0], skip_special_tokens=True)

# Use with agent
tools = [ImageCaptionTool(), VisualQATool(processor, model)]
agent = initialize_agent(tools, llm, agent=AgentType.ZERO_SHOT_REACT_DESCRIPTION)
```

## ONNX Export and Deployment

### Export to ONNX

```python
import torch
from transformers import Blip2ForConditionalGeneration, Blip2Processor

model = Blip2ForConditionalGeneration.from_pretrained("Salesforce/blip2-opt-2.7b")
processor = Blip2Processor.from_pretrained("Salesforce/blip2-opt-2.7b")

# Example inputs
image = Image.open("example.jpg").convert("RGB")
inputs = processor(images=image, return_tensors="pt")

# Export vision encoder
torch.onnx.export(
    model.vision_model,
    inputs["pixel_values"],
    "blip2_vision.onnx",
    input_names=["pixel_values"],
    output_names=["image_embeds"],
    dynamic_axes={
        "pixel_values": {0: "batch_size"},
        "image_embeds": {0: "batch_size"}
    },
    opset_version=14
)
```

### TensorRT optimization

```python
import tensorrt as trt
import pycuda.driver as cuda

def build_engine(onnx_path, engine_path):
    logger = trt.Logger(trt.Logger.WARNING)
    builder = trt.Builder(logger)
    network = builder.create_network(1 << int(trt.NetworkDefinitionCreationFlag.EXPLICIT_BATCH))
    parser = trt.OnnxParser(network, logger)

    with open(onnx_path, 'rb') as f:
        parser.parse(f.read())

    config = builder.create_builder_config()
    config.set_flag(trt.BuilderFlag.FP16)  # Enable FP16
    config.max_workspace_size = 1 << 30  # 1GB

    engine = builder.build_serialized_network(network, config)

    with open(engine_path, 'wb') as f:
        f.write(engine)

build_engine("blip2_vision.onnx", "blip2_vision.trt")
```

## Specialized Use Cases

### Video captioning (frame-by-frame)

```python
import cv2
import torch
from PIL import Image

def caption_video(video_path, sample_rate=30):
    """Caption video by sampling frames."""
    cap = cv2.VideoCapture(video_path)
    fps = cap.get(cv2.CAP_PROP_FPS)
    frame_interval = int(fps * sample_rate / 30)  # Sample every N frames

    captions = []
    frame_count = 0

    while cap.isOpened():
        ret, frame = cap.read()
        if not ret:
            break

        if frame_count % frame_interval == 0:
            # Convert BGR to RGB
            rgb_frame = cv2.cvtColor(frame, cv2.COLOR_BGR2RGB)
            image = Image.fromarray(rgb_frame)

            # Caption
            inputs = processor(images=image, return_tensors="pt").to("cuda", torch.float16)
            generated_ids = model.generate(**inputs, max_new_tokens=50)
            caption = processor.decode(generated_ids[0], skip_special_tokens=True)

            timestamp = frame_count / fps
            captions.append({"timestamp": timestamp, "caption": caption})

        frame_count += 1

    cap.release()
    return captions

# Usage
captions = caption_video("video.mp4", sample_rate=1)  # 1 frame per second
for c in captions:
    print(f"[{c['timestamp']:.1f}s] {c['caption']}")
```

### Document understanding

```python
def analyze_document(image_path):
    """Extract information from document image."""
    image = Image.open(image_path).convert("RGB")

    questions = [
        "What type of document is this?",
        "What is the title of this document?",
        "What are the main sections?",
        "Summarize the key information."
    ]

    results = {}
    for q in questions:
        inputs = processor(images=image, text=q, return_tensors="pt").to("cuda", torch.float16)
        generated_ids = model.generate(**inputs, max_new_tokens=100)
        answer = processor.decode(generated_ids[0], skip_special_tokens=True)
        results[q] = answer

    return results

# Usage
doc_info = analyze_document("invoice.png")
for q, a in doc_info.items():
    print(f"Q: {q}\nA: {a}\n")
```

### Medical image analysis

```python
def analyze_medical_image(image_path, modality="xray"):
    """Analyze medical images with specific prompts."""
    image = Image.open(image_path).convert("RGB")

    prompts = {
        "xray": [
            "Describe any abnormalities visible in this chest X-ray.",
            "What anatomical structures are visible?",
            "Is there any evidence of pathology?"
        ],
        "ct": [
            "Describe the CT scan findings.",
            "What organs are visible in this slice?",
            "Are there any masses or lesions?"
        ],
        "mri": [
            "Describe the MRI findings.",
            "What tissues show abnormal signal intensity?",
            "What is the most likely diagnosis?"
        ]
    }

    results = []
    for prompt in prompts.get(modality, prompts["xray"]):
        inputs = processor(images=image, text=prompt, return_tensors="pt").to("cuda", torch.float16)
        generated_ids = model.generate(**inputs, max_new_tokens=150)
        answer = processor.decode(generated_ids[0], skip_special_tokens=True)
        results.append({"question": prompt, "answer": answer})

    return results

# Note: BLIP-2 is not trained on medical data - use specialized models for clinical use
```

## Evaluation

### Caption evaluation metrics

```python
from pycocoevalcap.bleu.bleu import Bleu
from pycocoevalcap.meteor.meteor import Meteor
from pycocoevalcap.rouge.rouge import Rouge
from pycocoevalcap.cider.cider import Cider

def evaluate_captions(predictions, references):
    """
    Evaluate generated captions against references.

    Args:
        predictions: dict {image_id: [caption]}
        references: dict {image_id: [ref1, ref2, ...]}
    """
    scorers = [
        (Bleu(4), ["Bleu_1", "Bleu_2", "Bleu_3", "Bleu_4"]),
        (Meteor(), "METEOR"),
        (Rouge(), "ROUGE_L"),
        (Cider(), "CIDEr"),
    ]

    results = {}
    for scorer, method in scorers:
        score, _ = scorer.compute_score(references, predictions)
        if isinstance(method, list):
            for sc, m in zip(score, method):
                results[m] = sc
        else:
            results[method] = score

    return results

# Usage
preds = {0: ["a cat sitting on a mat"], 1: ["a dog running in the park"]}
refs = {0: ["a cat on a mat", "cat sitting"], 1: ["dog in park", "running dog"]}
scores = evaluate_captions(preds, refs)
print(scores)
```

### VQA evaluation

```python
def vqa_accuracy(predictions, ground_truths):
    """
    VQA accuracy metric (soft accuracy from VQA challenge).

    Args:
        predictions: list of predicted answers
        ground_truths: list of lists (multiple annotator answers)
    """
    def compute_accuracy(pred, gts):
        pred = pred.lower().strip()
        gts = [gt.lower().strip() for gt in gts]

        # Count matches
        matches = sum(1 for gt in gts if pred == gt)
        return min(matches / 3, 1.0)  # Cap at 1.0

    accuracies = []
    for pred, gts in zip(predictions, ground_truths):
        accuracies.append(compute_accuracy(pred, gts))

    return sum(accuracies) / len(accuracies)

# Usage
preds = ["yes", "a dog", "blue"]
gts = [["yes", "yes", "no"], ["dog", "a dog", "puppy"], ["blue", "light blue", "azure"]]
acc = vqa_accuracy(preds, gts)
print(f"VQA Accuracy: {acc:.2%}")
```

## Model Comparison

### BLIP-2 variants benchmark

| Model | COCO Caption (CIDEr) | VQAv2 (Acc) | GQA (Acc) | VRAM |
|-------|---------------------|-------------|-----------|------|
| blip2-opt-2.7b | 129.7 | 52.6 | 41.3 | 8GB |
| blip2-opt-6.7b | 133.4 | 54.2 | 42.8 | 16GB |
| blip2-flan-t5-xl | 138.1 | 62.9 | 44.1 | 10GB |
| blip2-flan-t5-xxl | 145.8 | 65.0 | 45.9 | 26GB |

### Comparison with other models

| Model | Architecture | Zero-shot VQA | Training Cost |
|-------|-------------|---------------|---------------|
| BLIP-2 | Q-Former + LLM | Excellent | Low (Q-Former only) |
| LLaVA | Linear + LLM | Good | Medium |
| Flamingo | Perceiver + LLM | Excellent | High |
| InstructBLIP | Q-Former + LLM | Best | Low |




### Troubleshooting

# BLIP-2 Troubleshooting Guide

## Installation Issues

### Import errors

**Error**: `ModuleNotFoundError: No module named 'transformers'`

**Solutions**:
```bash
# Install transformers with vision support
pip install transformers[vision] accelerate

# Or install all optional dependencies
pip install transformers accelerate torch Pillow scipy

# Verify installation
python -c "from transformers import Blip2ForConditionalGeneration; print('OK')"
```

### LAVIS installation fails

**Error**: Errors installing salesforce-lavis

**Solutions**:
```bash
# Install from source
git clone https://github.com/salesforce/LAVIS.git
cd LAVIS
pip install -e .

# Or specific version
pip install salesforce-lavis==1.0.2

# Install dependencies separately if issues persist
pip install omegaconf iopath timm webdataset
pip install salesforce-lavis --no-deps
```

### CUDA version mismatch

**Error**: `RuntimeError: CUDA error: no kernel image is available`

**Solutions**:
```bash
# Check CUDA version
nvcc --version
python -c "import torch; print(torch.version.cuda)"

# Install matching PyTorch
pip install torch torchvision --index-url https://download.pytorch.org/whl/cu121

# For CUDA 11.8
pip install torch torchvision --index-url https://download.pytorch.org/whl/cu118
```

## Model Loading Issues

### Out of memory during load

**Error**: `torch.cuda.OutOfMemoryError` during model loading

**Solutions**:
```python
# Use quantization
from transformers import BitsAndBytesConfig

quantization_config = BitsAndBytesConfig(load_in_8bit=True)
model = Blip2ForConditionalGeneration.from_pretrained(
    "Salesforce/blip2-opt-2.7b",
    quantization_config=quantization_config,
    device_map="auto"
)

# Or 4-bit quantization
quantization_config = BitsAndBytesConfig(
    load_in_4bit=True,
    bnb_4bit_compute_dtype=torch.float16
)

# Use smaller model
model = Blip2ForConditionalGeneration.from_pretrained(
    "Salesforce/blip2-opt-2.7b",  # Instead of 6.7b or flan-t5-xxl
    torch_dtype=torch.float16,
    device_map="auto"
)

# Offload to CPU
model = Blip2ForConditionalGeneration.from_pretrained(
    "Salesforce/blip2-opt-6.7b",
    device_map="auto",
    offload_folder="offload"
)
```

### Model download fails

**Error**: Connection errors or incomplete downloads

**Solutions**:
```python
# Set cache directory
import os
os.environ["HF_HOME"] = "/path/to/cache"

# Resume download
from huggingface_hub import snapshot_download
snapshot_download(
    "Salesforce/blip2-opt-2.7b",
    resume_download=True
)

# Use local files only after download
model = Blip2ForConditionalGeneration.from_pretrained(
    "Salesforce/blip2-opt-2.7b",
    local_files_only=True
)
```

### Weight loading errors

**Error**: `RuntimeError: Error(s) in loading state_dict`

**Solutions**:
```python
# Ignore mismatched weights
model = Blip2ForConditionalGeneration.from_pretrained(
    "Salesforce/blip2-opt-2.7b",
    ignore_mismatched_sizes=True
)

# Check model architecture matches checkpoint
from transformers import AutoConfig
config = AutoConfig.from_pretrained("Salesforce/blip2-opt-2.7b")
print(config.text_config.model_type)  # Should be 'opt'
```

## Inference Issues

### Image format errors

**Error**: `ValueError: Unable to create tensor`

**Solutions**:
```python
from PIL import Image

# Ensure RGB format
image = Image.open("image.jpg").convert("RGB")

# Handle different formats
def load_image(path):
    image = Image.open(path)

    # Convert RGBA to RGB
    if image.mode == "RGBA":
        background = Image.new("RGB", image.size, (255, 255, 255))
        background.paste(image, mask=image.split()[3])
        image = background
    elif image.mode != "RGB":
        image = image.convert("RGB")

    return image

# Handle URL images
import requests
from io import BytesIO

def load_image_from_url(url):
    response = requests.get(url)
    image = Image.open(BytesIO(response.content))
    return image.convert("RGB")
```

### Empty or nonsensical output

**Problem**: Model returns empty string or gibberish

**Solutions**:
```python
# Check input preprocessing
inputs = processor(images=image, return_tensors="pt")
print(f"Pixel values shape: {inputs['pixel_values'].shape}")
# Should be [1, 3, 224, 224] for single image

# Ensure correct dtype
inputs = inputs.to("cuda", torch.float16)

# Use better generation parameters
generated_ids = model.generate(
    **inputs,
    max_new_tokens=100,
    min_length=10,
    num_beams=5,
    do_sample=False  # Deterministic for debugging
)

# Check decoder starting tokens
print(f"Generated IDs: {generated_ids}")
```

### Slow generation

**Problem**: Generation takes too long

**Solutions**:
```python
# Reduce max_new_tokens
generated_ids = model.generate(**inputs, max_new_tokens=30)

# Use greedy decoding (faster than beam search)
generated_ids = model.generate(
    **inputs,
    max_new_tokens=50,
    num_beams=1,
    do_sample=False
)

# Enable model compilation (PyTorch 2.0+)
model = torch.compile(model)

# Use Flash Attention
model = Blip2ForConditionalGeneration.from_pretrained(
    "Salesforce/blip2-opt-2.7b",
    torch_dtype=torch.float16,
    attn_implementation="flash_attention_2",
    device_map="auto"
)
```

### Batch processing errors

**Error**: Dimension mismatch in batch processing

**Solutions**:
```python
# Ensure consistent image sizes with padding
inputs = processor(
    images=images,
    return_tensors="pt",
    padding=True
)

# Handle variable size images
from torchvision import transforms

transform = transforms.Compose([
    transforms.Resize((224, 224)),
    transforms.ToTensor(),
])

# Ensure all images are same size before processing
images = [transform(img) for img in images]

# For text inputs, use padding
inputs = processor(
    images=images,
    text=questions,
    return_tensors="pt",
    padding="max_length",
    max_length=32,
    truncation=True
)
```

## Memory Issues

### CUDA out of memory

**Error**: `torch.cuda.OutOfMemoryError: CUDA out of memory`

**Solutions**:
```python
# Clear cache before inference
torch.cuda.empty_cache()

# Use smaller batch size
batch_size = 1  # Start with 1

# Process sequentially
results = []
for image in images:
    inputs = processor(images=image, return_tensors="pt").to("cuda", torch.float16)
    generated_ids = model.generate(**inputs, max_new_tokens=50)
    results.append(processor.decode(generated_ids[0], skip_special_tokens=True))
    torch.cuda.empty_cache()

# Use gradient checkpointing
model.gradient_checkpointing_enable()

# Monitor memory
print(f"Allocated: {torch.cuda.memory_allocated() / 1e9:.2f} GB")
print(f"Cached: {torch.cuda.memory_reserved() / 1e9:.2f} GB")
```

### Memory leak during batch processing

**Problem**: Memory grows over time

**Solutions**:
```python
import gc

# Delete tensors explicitly
del inputs, generated_ids
gc.collect()
torch.cuda.empty_cache()

# Use context manager
with torch.inference_mode():
    inputs = processor(images=image, return_tensors="pt").to("cuda", torch.float16)
    generated_ids = model.generate(**inputs, max_new_tokens=50)
    caption = processor.decode(generated_ids[0], skip_special_tokens=True)

# Move to CPU after inference
caption = processor.decode(generated_ids.cpu()[0], skip_special_tokens=True)
```

## Quality Issues

### Poor caption quality

**Problem**: Captions are generic or inaccurate

**Solutions**:
```python
# Use larger model
model = Blip2ForConditionalGeneration.from_pretrained(
    "Salesforce/blip2-flan-t5-xl",  # Better quality than OPT
    torch_dtype=torch.float16,
    device_map="auto"
)

# Use prompts for better captions
inputs = processor(
    images=image,
    text="a detailed description of the image:",
    return_tensors="pt"
)

# Increase diversity with sampling
generated_ids = model.generate(
    **inputs,
    max_new_tokens=100,
    num_beams=5,
    num_return_sequences=3,  # Generate multiple
    temperature=0.9,
    do_sample=True
)

# Select best from multiple candidates
```

### VQA hallucinations

**Problem**: Model makes up information not in image

**Solutions**:
```python
# Use more specific questions
# Instead of "What is happening?"
# Ask "Is there a person in this image?"

# Lower temperature
generated_ids = model.generate(
    **inputs,
    max_new_tokens=30,
    temperature=0.3,  # More focused
    do_sample=True
)

# Use beam search (more deterministic)
generated_ids = model.generate(
    **inputs,
    max_new_tokens=30,
    num_beams=5,
    do_sample=False
)

# Add constraints
generated_ids = model.generate(
    **inputs,
    max_new_tokens=30,
    no_repeat_ngram_size=3,
)
```

### Incorrect colors/objects

**Problem**: Model identifies wrong colors or objects

**Solutions**:
```python
# Ensure image is RGB not BGR
import cv2
image_cv = cv2.imread("image.jpg")
image_rgb = cv2.cvtColor(image_cv, cv2.COLOR_BGR2RGB)
image = Image.fromarray(image_rgb)

# Check image quality
print(f"Image size: {image.size}")
print(f"Image mode: {image.mode}")

# Use higher resolution if possible (but processor resizes to 224x224)

# Ask more specific questions
# Instead of "What color is it?"
# Ask "Is the car red or blue?"
```

## Processor Issues

### Tokenizer warnings

**Warning**: `Asking to pad but the tokenizer does not have a padding token`

**Solutions**:
```python
# Set padding token
processor.tokenizer.pad_token = processor.tokenizer.eos_token

# Or specify during processing
inputs = processor(
    images=image,
    text=question,
    return_tensors="pt",
    padding="max_length",
    max_length=32
)
```

### Image normalization issues

**Problem**: Unexpected results due to normalization

**Solutions**:
```python
# Check processor's image normalization
print(processor.image_processor.image_mean)
print(processor.image_processor.image_std)

# Manual normalization if needed
from torchvision import transforms

normalize = transforms.Normalize(
    mean=processor.image_processor.image_mean,
    std=processor.image_processor.image_std
)

# Or use raw pixel values
inputs = processor(
    images=image,
    return_tensors="pt",
    do_normalize=False  # Skip normalization
)
```

## LAVIS-Specific Issues

### Config not found

**Error**: `ConfigError: Config file not found`

**Solutions**:
```python
# Use registry properly
from lavis.common.registry import registry
from lavis.models import load_model_and_preprocess

# Check available models
print(registry.list_models())

# Load with explicit config
model, vis_processors, txt_processors = load_model_and_preprocess(
    name="blip2_opt",
    model_type="pretrain_opt2.7b",
    is_eval=True,
    device="cuda"
)
```

### Dataset loading errors

**Error**: `Dataset not found` or download issues

**Solutions**:
```python
from lavis.datasets.builders import load_dataset

# Set download directory
import os
os.environ["LAVIS_DATASETS_ROOT"] = "/path/to/datasets"

# Download manually first
# Then load with local files
dataset = load_dataset("coco_caption", split="val")
```

## Common Error Messages

| Error | Cause | Solution |
|-------|-------|----------|
| `CUDA out of memory` | Model too large | Use quantization or smaller model |
| `Unable to create tensor` | Invalid image format | Convert to RGB PIL Image |
| `padding_side must be` | Tokenizer config | Set pad_token explicitly |
| `Expected 4D input` | Wrong tensor shape | Add batch dimension with unsqueeze(0) |
| `device mismatch` | Tensors on different devices | Move all to same device |
| `half() not implemented` | CPU doesn't support FP16 | Use float32 on CPU |

## Getting Help

1. **HuggingFace Forums**: https://discuss.huggingface.co
2. **LAVIS GitHub Issues**: https://github.com/salesforce/LAVIS/issues
3. **Paper**: https://arxiv.org/abs/2301.12597
4. **Model Card**: https://huggingface.co/Salesforce/blip2-opt-2.7b

### Reporting Issues

Include:
- Python version
- transformers/lavis version
- PyTorch and CUDA versions
- GPU model and VRAM
- Full error traceback
- Minimal reproducible code
- Image resolution and format




---

## 🚀 Usage

**Reference this template:** `@skill-blip-2-vision-language.md`


**Platform-specific:**
- **GitHub Copilot**: Add to `.github/copilot-instructions.md`
- **Augment Code**: Use `aug context add` command
- **Cursor/Windsurf**: Reference in chat with `@filename`
- **Claude**: Add to Project Knowledge
- **ChatGPT**: Add to Custom GPT configuration
