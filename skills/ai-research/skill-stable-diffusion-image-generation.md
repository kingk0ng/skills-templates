---
id: skill-stable-diffusion-image-generation
type: skill
name: stable-diffusion-image-generation
description: State-of-the-art text-to-image generation with Stable Diffusion models
  via HuggingFace Diffusers. Use when generating images from text prompts, performing
  image-to-image translation, inpainting, or building custom diffusion pipelines.
category: ai-research
complexity: medium
keywords:
- api
- deployment
- github
- optimization
- python
capabilities: []
token_estimate: 1725
has_references: true
reference_count: 2
---

<!-- Converted from Claude Skill Template -->
<!-- Complexity: Medium -->
<!-- Estimated Tokens: ~1,725 -->
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




# stable-diffusion-image-generation

> State-of-the-art text-to-image generation with Stable Diffusion models via HuggingFace Diffusers. Use when generating images from text prompts, performing image-to-image translation, inpainting, or building custom diffusion pipelines.

# Stable Diffusion Image Generation

Comprehensive guide to generating images with Stable Diffusion using the HuggingFace Diffusers library.

## When to use Stable Diffusion

**Use Stable Diffusion when:**
- Generating images from text descriptions
- Performing image-to-image translation (style transfer, enhancement)
- Inpainting (filling in masked regions)
- Outpainting (extending images beyond boundaries)
- Creating variations of existing images
- Building custom image generation workflows

**Key features:**
- **Text-to-Image**: Generate images from natural language prompts
- **Image-to-Image**: Transform existing images with text guidance
- **Inpainting**: Fill masked regions with context-aware content
- **ControlNet**: Add spatial conditioning (edges, poses, depth)
- **LoRA Support**: Efficient fine-tuning and style adaptation
- **Multiple Models**: SD 1.5, SDXL, SD 3.0, Flux support

**Use alternatives instead:**
- **DALL-E 3**: For API-based generation without GPU
- **Midjourney**: For artistic, stylized outputs
- **Imagen**: For Google Cloud integration
- **Leonardo.ai**: For web-based creative workflows

## Quick start

### Installation

```bash
pip install diffusers transformers accelerate torch
pip install xformers  # Optional: memory-efficient attention
```

### Basic text-to-image

```python
from diffusers import DiffusionPipeline
import torch

# Load pipeline (auto-detects model type)
pipe = DiffusionPipeline.from_pretrained(
    "stable-diffusion-v1-5/stable-diffusion-v1-5",
    torch_dtype=torch.float16
)
pipe.to("cuda")

# Generate image
image = pipe(
    "A serene mountain landscape at sunset, highly detailed",
    num_inference_steps=50,
    guidance_scale=7.5
).images[0]

image.save("output.png")
```

### Using SDXL (higher quality)

```python
from diffusers import AutoPipelineForText2Image
import torch

pipe = AutoPipelineForText2Image.from_pretrained(
    "stabilityai/stable-diffusion-xl-base-1.0",
    torch_dtype=torch.float16,
    variant="fp16"
)
pipe.to("cuda")

# Enable memory optimization
pipe.enable_model_cpu_offload()

image = pipe(
    prompt="A futuristic city with flying cars, cinematic lighting",
    height=1024,
    width=1024,
    num_inference_steps=30
).images[0]
```

## Architecture overview

### Three-pillar design

Diffusers is built around three core components:

```
Pipeline (orchestration)
├── Model (neural networks)
│   ├── UNet / Transformer (noise prediction)
│   ├── VAE (latent encoding/decoding)
│   └── Text Encoder (CLIP/T5)
└── Scheduler (denoising algorithm)
```

### Pipeline inference flow

```
Text Prompt → Text Encoder → Text Embeddings
                                    ↓
Random Noise → [Denoising Loop] ← Scheduler
                      ↓
               Predicted Noise
                      ↓
              VAE Decoder → Final Image
```

## Core concepts

### Pipelines

Pipelines orchestrate complete workflows:

| Pipeline | Purpose |
|----------|---------|
| `StableDiffusionPipeline` | Text-to-image (SD 1.x/2.x) |
| `StableDiffusionXLPipeline` | Text-to-image (SDXL) |
| `StableDiffusion3Pipeline` | Text-to-image (SD 3.0) |
| `FluxPipeline` | Text-to-image (Flux models) |
| `StableDiffusionImg2ImgPipeline` | Image-to-image |
| `StableDiffusionInpaintPipeline` | Inpainting |

### Schedulers

Schedulers control the denoising process:

| Scheduler | Steps | Quality | Use Case |
|-----------|-------|---------|----------|
| `EulerDiscreteScheduler` | 20-50 | Good | Default choice |
| `EulerAncestralDiscreteScheduler` | 20-50 | Good | More variation |
| `DPMSolverMultistepScheduler` | 15-25 | Excellent | Fast, high quality |
| `DDIMScheduler` | 50-100 | Good | Deterministic |
| `LCMScheduler` | 4-8 | Good | Very fast |
| `UniPCMultistepScheduler` | 15-25 | Excellent | Fast convergence |

### Swapping schedulers

```python
from diffusers import DPMSolverMultistepScheduler

# Swap for faster generation
pipe.scheduler = DPMSolverMultistepScheduler.from_config(
    pipe.scheduler.config
)

# Now generate with fewer steps
image = pipe(prompt, num_inference_steps=20).images[0]
```

## Generation parameters

### Key parameters

| Parameter | Default | Description |
|-----------|---------|-------------|
| `prompt` | Required | Text description of desired image |
| `negative_prompt` | None | What to avoid in the image |
| `num_inference_steps` | 50 | Denoising steps (more = better quality) |
| `guidance_scale` | 7.5 | Prompt adherence (7-12 typical) |
| `height`, `width` | 512/1024 | Output dimensions (multiples of 8) |
| `generator` | None | Torch generator for reproducibility |
| `num_images_per_prompt` | 1 | Batch size |

### Reproducible generation

```python
import torch

generator = torch.Generator(device="cuda").manual_seed(42)

image = pipe(
    prompt="A cat wearing a top hat",
    generator=generator,
    num_inference_steps=50
).images[0]
```

### Negative prompts

```python
image = pipe(
    prompt="Professional photo of a dog in a garden",
    negative_prompt="blurry, low quality, distorted, ugly, bad anatomy",
    guidance_scale=7.5
).images[0]
```

## Image-to-image

Transform existing images with text guidance:

```python
from diffusers import AutoPipelineForImage2Image
from PIL import Image

pipe = AutoPipelineForImage2Image.from_pretrained(
    "stable-diffusion-v1-5/stable-diffusion-v1-5",
    torch_dtype=torch.float16
).to("cuda")

init_image = Image.open("input.jpg").resize((512, 512))

image = pipe(
    prompt="A watercolor painting of the scene",
    image=init_image,
    strength=0.75,  # How much to transform (0-1)
    num_inference_steps=50
).images[0]
```

## Inpainting

Fill masked regions:

```python
from diffusers import AutoPipelineForInpainting
from PIL import Image

pipe = AutoPipelineForInpainting.from_pretrained(
    "runwayml/stable-diffusion-inpainting",
    torch_dtype=torch.float16
).to("cuda")

image = Image.open("photo.jpg")
mask = Image.open("mask.png")  # White = inpaint region

result = pipe(
    prompt="A red car parked on the street",
    image=image,
    mask_image=mask,
    num_inference_steps=50
).images[0]
```

## ControlNet

Add spatial conditioning for precise control:

```python
from diffusers import StableDiffusionControlNetPipeline, ControlNetModel
import torch

# Load ControlNet for edge conditioning
controlnet = ControlNetModel.from_pretrained(
    "lllyasviel/control_v11p_sd15_canny",
    torch_dtype=torch.float16
)

pipe = StableDiffusionControlNetPipeline.from_pretrained(
    "stable-diffusion-v1-5/stable-diffusion-v1-5",
    controlnet=controlnet,
    torch_dtype=torch.float16
).to("cuda")

# Use Canny edge image as control
control_image = get_canny_image(input_image)

image = pipe(
    prompt="A beautiful house in the style of Van Gogh",
    image=control_image,
    num_inference_steps=30
).images[0]
```

### Available ControlNets

| ControlNet | Input Type | Use Case |
|------------|------------|----------|
| `canny` | Edge maps | Preserve structure |
| `openpose` | Pose skeletons | Human poses |
| `depth` | Depth maps | 3D-aware generation |
| `normal` | Normal maps | Surface details |
| `mlsd` | Line segments | Architectural lines |
| `scribble` | Rough sketches | Sketch-to-image |

## LoRA adapters

Load fine-tuned style adapters:

```python
from diffusers import DiffusionPipeline

pipe = DiffusionPipeline.from_pretrained(
    "stable-diffusion-v1-5/stable-diffusion-v1-5",
    torch_dtype=torch.float16
).to("cuda")

# Load LoRA weights
pipe.load_lora_weights("path/to/lora", weight_name="style.safetensors")

# Generate with LoRA style
image = pipe("A portrait in the trained style").images[0]

# Adjust LoRA strength
pipe.fuse_lora(lora_scale=0.8)

# Unload LoRA
pipe.unload_lora_weights()
```

### Multiple LoRAs

```python
# Load multiple LoRAs
pipe.load_lora_weights("lora1", adapter_name="style")
pipe.load_lora_weights("lora2", adapter_name="character")

# Set weights for each
pipe.set_adapters(["style", "character"], adapter_weights=[0.7, 0.5])

image = pipe("A portrait").images[0]
```

## Memory optimization

### Enable CPU offloading

```python
# Model CPU offload - moves models to CPU when not in use
pipe.enable_model_cpu_offload()

# Sequential CPU offload - more aggressive, slower
pipe.enable_sequential_cpu_offload()
```

### Attention slicing

```python
# Reduce memory by computing attention in chunks
pipe.enable_attention_slicing()

# Or specific chunk size
pipe.enable_attention_slicing("max")
```

### xFormers memory-efficient attention

```python
# Requires xformers package
pipe.enable_xformers_memory_efficient_attention()
```

### VAE slicing for large images

```python
# Decode latents in tiles for large images
pipe.enable_vae_slicing()
pipe.enable_vae_tiling()
```

## Model variants

### Loading different precisions

```python
# FP16 (recommended for GPU)
pipe = DiffusionPipeline.from_pretrained(
    "model-id",
    torch_dtype=torch.float16,
    variant="fp16"
)

# BF16 (better precision, requires Ampere+ GPU)
pipe = DiffusionPipeline.from_pretrained(
    "model-id",
    torch_dtype=torch.bfloat16
)
```

### Loading specific components

```python
from diffusers import UNet2DConditionModel, AutoencoderKL

# Load custom VAE
vae = AutoencoderKL.from_pretrained("stabilityai/sd-vae-ft-mse")

# Use with pipeline
pipe = DiffusionPipeline.from_pretrained(
    "stable-diffusion-v1-5/stable-diffusion-v1-5",
    vae=vae,
    torch_dtype=torch.float16
)
```

## Batch generation

Generate multiple images efficiently:

```python
# Multiple prompts
prompts = [
    "A cat playing piano",
    "A dog reading a book",
    "A bird painting a picture"
]

images = pipe(prompts, num_inference_steps=30).images

# Multiple images per prompt
images = pipe(
    "A beautiful sunset",
    num_images_per_prompt=4,
    num_inference_steps=30
).images
```

## Common workflows

### Workflow 1: High-quality generation

```python
from diffusers import StableDiffusionXLPipeline, DPMSolverMultistepScheduler
import torch

# 1. Load SDXL with optimizations
pipe = StableDiffusionXLPipeline.from_pretrained(
    "stabilityai/stable-diffusion-xl-base-1.0",
    torch_dtype=torch.float16,
    variant="fp16"
)
pipe.to("cuda")
pipe.scheduler = DPMSolverMultistepScheduler.from_config(pipe.scheduler.config)
pipe.enable_model_cpu_offload()

# 2. Generate with quality settings
image = pipe(
    prompt="A majestic lion in the savanna, golden hour lighting, 8k, detailed fur",
    negative_prompt="blurry, low quality, cartoon, anime, sketch",
    num_inference_steps=30,
    guidance_scale=7.5,
    height=1024,
    width=1024
).images[0]
```

### Workflow 2: Fast prototyping

```python
from diffusers import AutoPipelineForText2Image, LCMScheduler
import torch

# Use LCM for 4-8 step generation
pipe = AutoPipelineForText2Image.from_pretrained(
    "stabilityai/stable-diffusion-xl-base-1.0",
    torch_dtype=torch.float16
).to("cuda")

# Load LCM LoRA for fast generation
pipe.load_lora_weights("latent-consistency/lcm-lora-sdxl")
pipe.scheduler = LCMScheduler.from_config(pipe.scheduler.config)
pipe.fuse_lora()

# Generate in ~1 second
image = pipe(
    "A beautiful landscape",
    num_inference_steps=4,
    guidance_scale=1.0
).images[0]
```

## Common issues

**CUDA out of memory:**
```python
# Enable memory optimizations
pipe.enable_model_cpu_offload()
pipe.enable_attention_slicing()
pipe.enable_vae_slicing()

# Or use lower precision
pipe = DiffusionPipeline.from_pretrained(model_id, torch_dtype=torch.float16)
```

**Black/noise images:**
```python
# Check VAE configuration
# Use safety checker bypass if needed
pipe.safety_checker = None

# Ensure proper dtype consistency
pipe = pipe.to(dtype=torch.float16)
```

**Slow generation:**
```python
# Use faster scheduler
from diffusers import DPMSolverMultistepScheduler
pipe.scheduler = DPMSolverMultistepScheduler.from_config(pipe.scheduler.config)

# Reduce steps
image = pipe(prompt, num_inference_steps=20).images[0]
```

## References

- **[Advanced Usage](references/advanced-usage.md)** - Custom pipelines, fine-tuning, deployment
- **[Troubleshooting](references/troubleshooting.md)** - Common issues and solutions

## Resources

- **Documentation**: https://huggingface.co/docs/diffusers
- **Repository**: https://github.com/huggingface/diffusers
- **Model Hub**: https://huggingface.co/models?library=diffusers
- **Discord**: https://discord.gg/diffusers


---


## 📚 Reference Materials


### Advanced Usage

# Stable Diffusion Advanced Usage Guide

## Custom Pipelines

### Building from components

```python
from diffusers import (
    UNet2DConditionModel,
    AutoencoderKL,
    DDPMScheduler,
    StableDiffusionPipeline
)
from transformers import CLIPTextModel, CLIPTokenizer
import torch

# Load components individually
unet = UNet2DConditionModel.from_pretrained(
    "stable-diffusion-v1-5/stable-diffusion-v1-5",
    subfolder="unet"
)
vae = AutoencoderKL.from_pretrained(
    "stable-diffusion-v1-5/stable-diffusion-v1-5",
    subfolder="vae"
)
text_encoder = CLIPTextModel.from_pretrained(
    "stable-diffusion-v1-5/stable-diffusion-v1-5",
    subfolder="text_encoder"
)
tokenizer = CLIPTokenizer.from_pretrained(
    "stable-diffusion-v1-5/stable-diffusion-v1-5",
    subfolder="tokenizer"
)
scheduler = DDPMScheduler.from_pretrained(
    "stable-diffusion-v1-5/stable-diffusion-v1-5",
    subfolder="scheduler"
)

# Assemble pipeline
pipe = StableDiffusionPipeline(
    unet=unet,
    vae=vae,
    text_encoder=text_encoder,
    tokenizer=tokenizer,
    scheduler=scheduler,
    safety_checker=None,
    feature_extractor=None,
    requires_safety_checker=False
)
```

### Custom denoising loop

```python
from diffusers import DDIMScheduler, AutoencoderKL, UNet2DConditionModel
from transformers import CLIPTextModel, CLIPTokenizer
import torch

def custom_generate(
    prompt: str,
    num_steps: int = 50,
    guidance_scale: float = 7.5,
    height: int = 512,
    width: int = 512
):
    # Load components
    tokenizer = CLIPTokenizer.from_pretrained("openai/clip-vit-large-patch14")
    text_encoder = CLIPTextModel.from_pretrained("openai/clip-vit-large-patch14")
    unet = UNet2DConditionModel.from_pretrained("sd-model", subfolder="unet")
    vae = AutoencoderKL.from_pretrained("sd-model", subfolder="vae")
    scheduler = DDIMScheduler.from_pretrained("sd-model", subfolder="scheduler")

    device = "cuda"
    text_encoder.to(device)
    unet.to(device)
    vae.to(device)

    # Encode prompt
    text_input = tokenizer(
        prompt,
        padding="max_length",
        max_length=77,
        truncation=True,
        return_tensors="pt"
    )
    text_embeddings = text_encoder(text_input.input_ids.to(device))[0]

    # Unconditional embeddings for classifier-free guidance
    uncond_input = tokenizer(
        "",
        padding="max_length",
        max_length=77,
        return_tensors="pt"
    )
    uncond_embeddings = text_encoder(uncond_input.input_ids.to(device))[0]

    # Concatenate for batch processing
    text_embeddings = torch.cat([uncond_embeddings, text_embeddings])

    # Initialize latents
    latents = torch.randn(
        (1, 4, height // 8, width // 8),
        device=device
    )
    latents = latents * scheduler.init_noise_sigma

    # Denoising loop
    scheduler.set_timesteps(num_steps)
    for t in scheduler.timesteps:
        latent_model_input = torch.cat([latents] * 2)
        latent_model_input = scheduler.scale_model_input(latent_model_input, t)

        # Predict noise
        with torch.no_grad():
            noise_pred = unet(
                latent_model_input,
                t,
                encoder_hidden_states=text_embeddings
            ).sample

        # Classifier-free guidance
        noise_pred_uncond, noise_pred_cond = noise_pred.chunk(2)
        noise_pred = noise_pred_uncond + guidance_scale * (
            noise_pred_cond - noise_pred_uncond
        )

        # Update latents
        latents = scheduler.step(noise_pred, t, latents).prev_sample

    # Decode latents
    latents = latents / vae.config.scaling_factor
    with torch.no_grad():
        image = vae.decode(latents).sample

    # Convert to PIL
    image = (image / 2 + 0.5).clamp(0, 1)
    image = image.cpu().permute(0, 2, 3, 1).numpy()
    image = (image * 255).round().astype("uint8")[0]

    return Image.fromarray(image)
```

## IP-Adapter

Use image prompts alongside text:

```python
from diffusers import StableDiffusionPipeline
from diffusers.utils import load_image
import torch

pipe = StableDiffusionPipeline.from_pretrained(
    "stable-diffusion-v1-5/stable-diffusion-v1-5",
    torch_dtype=torch.float16
).to("cuda")

# Load IP-Adapter
pipe.load_ip_adapter(
    "h94/IP-Adapter",
    subfolder="models",
    weight_name="ip-adapter_sd15.bin"
)

# Set IP-Adapter scale
pipe.set_ip_adapter_scale(0.6)

# Load reference image
ip_image = load_image("reference_style.jpg")

# Generate with image + text prompt
image = pipe(
    prompt="A portrait in a garden",
    ip_adapter_image=ip_image,
    num_inference_steps=50
).images[0]
```

### Multiple IP-Adapter images

```python
# Use multiple reference images
pipe.set_ip_adapter_scale([0.5, 0.7])

images = [
    load_image("style_reference.jpg"),
    load_image("composition_reference.jpg")
]

result = pipe(
    prompt="A landscape painting",
    ip_adapter_image=images,
    num_inference_steps=50
).images[0]
```

## SDXL Refiner

Two-stage generation for higher quality:

```python
from diffusers import StableDiffusionXLPipeline, StableDiffusionXLImg2ImgPipeline
import torch

# Load base model
base = StableDiffusionXLPipeline.from_pretrained(
    "stabilityai/stable-diffusion-xl-base-1.0",
    torch_dtype=torch.float16,
    variant="fp16"
).to("cuda")

# Load refiner
refiner = StableDiffusionXLImg2ImgPipeline.from_pretrained(
    "stabilityai/stable-diffusion-xl-refiner-1.0",
    torch_dtype=torch.float16,
    variant="fp16"
).to("cuda")

# Generate with base (partial denoising)
image = base(
    prompt="A majestic eagle soaring over mountains",
    num_inference_steps=40,
    denoising_end=0.8,
    output_type="latent"
).images

# Refine with refiner
refined = refiner(
    prompt="A majestic eagle soaring over mountains",
    image=image,
    num_inference_steps=40,
    denoising_start=0.8
).images[0]
```

## T2I-Adapter

Lightweight conditioning without full ControlNet:

```python
from diffusers import StableDiffusionXLAdapterPipeline, T2IAdapter
import torch

# Load adapter
adapter = T2IAdapter.from_pretrained(
    "TencentARC/t2i-adapter-canny-sdxl-1.0",
    torch_dtype=torch.float16
)

pipe = StableDiffusionXLAdapterPipeline.from_pretrained(
    "stabilityai/stable-diffusion-xl-base-1.0",
    adapter=adapter,
    torch_dtype=torch.float16
).to("cuda")

# Get canny edges
canny_image = get_canny_image(input_image)

image = pipe(
    prompt="A colorful anime character",
    image=canny_image,
    num_inference_steps=30,
    adapter_conditioning_scale=0.8
).images[0]
```

## Fine-tuning with DreamBooth

Train on custom subjects:

```python
from diffusers import StableDiffusionPipeline, DDPMScheduler
from diffusers.optimization import get_scheduler
import torch
from torch.utils.data import Dataset, DataLoader
from PIL import Image
import os

class DreamBoothDataset(Dataset):
    def __init__(self, instance_images_path, instance_prompt, tokenizer, size=512):
        self.instance_images_path = instance_images_path
        self.instance_prompt = instance_prompt
        self.tokenizer = tokenizer
        self.size = size

        self.instance_images = [
            os.path.join(instance_images_path, f)
            for f in os.listdir(instance_images_path)
            if f.endswith(('.png', '.jpg', '.jpeg'))
        ]

    def __len__(self):
        return len(self.instance_images)

    def __getitem__(self, idx):
        image = Image.open(self.instance_images[idx]).convert("RGB")
        image = image.resize((self.size, self.size))
        image = torch.tensor(np.array(image)).permute(2, 0, 1) / 127.5 - 1.0

        tokens = self.tokenizer(
            self.instance_prompt,
            padding="max_length",
            max_length=77,
            truncation=True,
            return_tensors="pt"
        )

        return {"image": image, "input_ids": tokens.input_ids.squeeze()}

def train_dreambooth(
    pretrained_model: str,
    instance_data_dir: str,
    instance_prompt: str,
    output_dir: str,
    learning_rate: float = 5e-6,
    max_train_steps: int = 800,
    train_batch_size: int = 1
):
    # Load pipeline
    pipe = StableDiffusionPipeline.from_pretrained(pretrained_model)

    unet = pipe.unet
    vae = pipe.vae
    text_encoder = pipe.text_encoder
    tokenizer = pipe.tokenizer
    noise_scheduler = DDPMScheduler.from_pretrained(pretrained_model, subfolder="scheduler")

    # Freeze VAE and text encoder
    vae.requires_grad_(False)
    text_encoder.requires_grad_(False)

    # Create dataset
    dataset = DreamBoothDataset(
        instance_data_dir, instance_prompt, tokenizer
    )
    dataloader = DataLoader(dataset, batch_size=train_batch_size, shuffle=True)

    # Setup optimizer
    optimizer = torch.optim.AdamW(unet.parameters(), lr=learning_rate)
    lr_scheduler = get_scheduler(
        "constant",
        optimizer=optimizer,
        num_warmup_steps=0,
        num_training_steps=max_train_steps
    )

    # Training loop
    unet.train()
    device = "cuda"
    unet.to(device)
    vae.to(device)
    text_encoder.to(device)

    global_step = 0
    for epoch in range(max_train_steps // len(dataloader) + 1):
        for batch in dataloader:
            if global_step >= max_train_steps:
                break

            # Encode images to latents
            latents = vae.encode(batch["image"].to(device)).latent_dist.sample()
            latents = latents * vae.config.scaling_factor

            # Sample noise
            noise = torch.randn_like(latents)
            timesteps = torch.randint(0, noise_scheduler.num_train_timesteps, (latents.shape[0],))
            timesteps = timesteps.to(device)

            # Add noise
            noisy_latents = noise_scheduler.add_noise(latents, noise, timesteps)

            # Get text embeddings
            encoder_hidden_states = text_encoder(batch["input_ids"].to(device))[0]

            # Predict noise
            noise_pred = unet(noisy_latents, timesteps, encoder_hidden_states).sample

            # Compute loss
            loss = torch.nn.functional.mse_loss(noise_pred, noise)

            # Backprop
            loss.backward()
            optimizer.step()
            lr_scheduler.step()
            optimizer.zero_grad()

            global_step += 1

            if global_step % 100 == 0:
                print(f"Step {global_step}, Loss: {loss.item():.4f}")

    # Save model
    pipe.unet = unet
    pipe.save_pretrained(output_dir)
```

## LoRA Training

Efficient fine-tuning with Low-Rank Adaptation:

```python
from peft import LoraConfig, get_peft_model
from diffusers import StableDiffusionPipeline
import torch

def train_lora(
    base_model: str,
    train_dataset,
    output_dir: str,
    lora_rank: int = 4,
    learning_rate: float = 1e-4,
    max_train_steps: int = 1000
):
    pipe = StableDiffusionPipeline.from_pretrained(base_model)
    unet = pipe.unet

    # Configure LoRA
    lora_config = LoraConfig(
        r=lora_rank,
        lora_alpha=lora_rank,
        target_modules=["to_q", "to_v", "to_k", "to_out.0"],
        lora_dropout=0.1
    )

    # Apply LoRA to UNet
    unet = get_peft_model(unet, lora_config)
    unet.print_trainable_parameters()  # Shows ~0.1% trainable

    # Train (similar to DreamBooth but only LoRA params)
    optimizer = torch.optim.AdamW(
        unet.parameters(),
        lr=learning_rate
    )

    # ... training loop ...

    # Save LoRA weights only
    unet.save_pretrained(output_dir)
```

## Textual Inversion

Learn new concepts through embeddings:

```python
from diffusers import StableDiffusionPipeline
import torch

# Load with textual inversion
pipe = StableDiffusionPipeline.from_pretrained(
    "stable-diffusion-v1-5/stable-diffusion-v1-5",
    torch_dtype=torch.float16
).to("cuda")

# Load learned embedding
pipe.load_textual_inversion(
    "sd-concepts-library/cat-toy",
    token="<cat-toy>"
)

# Use in prompts
image = pipe("A photo of <cat-toy> on a beach").images[0]
```

## Quantization

Reduce memory with quantization:

```python
from diffusers import BitsAndBytesConfig, StableDiffusionXLPipeline
import torch

# 8-bit quantization
quantization_config = BitsAndBytesConfig(load_in_8bit=True)

pipe = StableDiffusionXLPipeline.from_pretrained(
    "stabilityai/stable-diffusion-xl-base-1.0",
    quantization_config=quantization_config,
    torch_dtype=torch.float16
)
```

### NF4 quantization (4-bit)

```python
quantization_config = BitsAndBytesConfig(
    load_in_4bit=True,
    bnb_4bit_quant_type="nf4",
    bnb_4bit_compute_dtype=torch.float16
)

pipe = StableDiffusionXLPipeline.from_pretrained(
    "stabilityai/stable-diffusion-xl-base-1.0",
    quantization_config=quantization_config
)
```

## Production Deployment

### FastAPI server

```python
from fastapi import FastAPI, HTTPException
from pydantic import BaseModel
from diffusers import DiffusionPipeline
import torch
import base64
from io import BytesIO

app = FastAPI()

# Load model at startup
pipe = DiffusionPipeline.from_pretrained(
    "stable-diffusion-v1-5/stable-diffusion-v1-5",
    torch_dtype=torch.float16
).to("cuda")
pipe.enable_model_cpu_offload()

class GenerationRequest(BaseModel):
    prompt: str
    negative_prompt: str = ""
    num_inference_steps: int = 30
    guidance_scale: float = 7.5
    width: int = 512
    height: int = 512
    seed: int = None

class GenerationResponse(BaseModel):
    image_base64: str
    seed: int

@app.post("/generate", response_model=GenerationResponse)
async def generate(request: GenerationRequest):
    try:
        generator = None
        seed = request.seed or torch.randint(0, 2**32, (1,)).item()
        generator = torch.Generator("cuda").manual_seed(seed)

        image = pipe(
            prompt=request.prompt,
            negative_prompt=request.negative_prompt,
            num_inference_steps=request.num_inference_steps,
            guidance_scale=request.guidance_scale,
            width=request.width,
            height=request.height,
            generator=generator
        ).images[0]

        # Convert to base64
        buffer = BytesIO()
        image.save(buffer, format="PNG")
        image_base64 = base64.b64encode(buffer.getvalue()).decode()

        return GenerationResponse(image_base64=image_base64, seed=seed)

    except Exception as e:
        raise HTTPException(status_code=500, detail=str(e))

@app.get("/health")
async def health():
    return {"status": "healthy"}
```

### Docker deployment

```dockerfile
FROM nvidia/cuda:12.1-runtime-ubuntu22.04

RUN apt-get update && apt-get install -y python3 python3-pip

WORKDIR /app

COPY requirements.txt .
RUN pip3 install -r requirements.txt

COPY . .

# Pre-download model
RUN python3 -c "from diffusers import DiffusionPipeline; DiffusionPipeline.from_pretrained('stable-diffusion-v1-5/stable-diffusion-v1-5')"

EXPOSE 8000
CMD ["uvicorn", "server:app", "--host", "0.0.0.0", "--port", "8000"]
```

### Kubernetes deployment

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: stable-diffusion
spec:
  replicas: 2
  selector:
    matchLabels:
      app: stable-diffusion
  template:
    metadata:
      labels:
        app: stable-diffusion
    spec:
      containers:
      - name: sd
        image: your-registry/stable-diffusion:latest
        ports:
        - containerPort: 8000
        resources:
          limits:
            nvidia.com/gpu: 1
            memory: "16Gi"
          requests:
            nvidia.com/gpu: 1
            memory: "8Gi"
        env:
        - name: TRANSFORMERS_CACHE
          value: "/cache/huggingface"
        volumeMounts:
        - name: model-cache
          mountPath: /cache
      volumes:
      - name: model-cache
        persistentVolumeClaim:
          claimName: model-cache-pvc
---
apiVersion: v1
kind: Service
metadata:
  name: stable-diffusion
spec:
  selector:
    app: stable-diffusion
  ports:
  - port: 80
    targetPort: 8000
  type: LoadBalancer
```

## Callback System

Monitor and modify generation:

```python
from diffusers import StableDiffusionPipeline
from diffusers.callbacks import PipelineCallback
import torch

class ProgressCallback(PipelineCallback):
    def __init__(self):
        self.progress = []

    def callback_fn(self, pipe, step_index, timestep, callback_kwargs):
        self.progress.append({
            "step": step_index,
            "timestep": timestep.item()
        })

        # Optionally modify latents
        latents = callback_kwargs["latents"]

        return callback_kwargs

# Use callback
callback = ProgressCallback()

image = pipe(
    prompt="A sunset",
    callback_on_step_end=callback.callback_fn,
    callback_on_step_end_tensor_inputs=["latents"]
).images[0]

print(f"Generation completed in {len(callback.progress)} steps")
```

### Early stopping

```python
def early_stop_callback(pipe, step_index, timestep, callback_kwargs):
    # Stop after 20 steps
    if step_index >= 20:
        pipe._interrupt = True
    return callback_kwargs

image = pipe(
    prompt="A landscape",
    num_inference_steps=50,
    callback_on_step_end=early_stop_callback
).images[0]
```

## Multi-GPU Inference

### Device map auto

```python
from diffusers import StableDiffusionXLPipeline

pipe = StableDiffusionXLPipeline.from_pretrained(
    "stabilityai/stable-diffusion-xl-base-1.0",
    device_map="auto",  # Automatically distribute across GPUs
    torch_dtype=torch.float16
)
```

### Manual distribution

```python
from accelerate import infer_auto_device_map, dispatch_model

# Create device map
device_map = infer_auto_device_map(
    pipe.unet,
    max_memory={0: "10GiB", 1: "10GiB"}
)

# Dispatch model
pipe.unet = dispatch_model(pipe.unet, device_map=device_map)
```




### Troubleshooting

# Stable Diffusion Troubleshooting Guide

## Installation Issues

### Package conflicts

**Error**: `ImportError: cannot import name 'cached_download' from 'huggingface_hub'`

**Fix**:
```bash
# Update huggingface_hub
pip install --upgrade huggingface_hub

# Reinstall diffusers
pip install --upgrade diffusers
```

### xFormers installation fails

**Error**: `RuntimeError: CUDA error: no kernel image is available for execution`

**Fix**:
```bash
# Check CUDA version
nvcc --version

# Install matching xformers
pip install xformers --index-url https://download.pytorch.org/whl/cu121  # For CUDA 12.1

# Or build from source
pip install -v -U git+https://github.com/facebookresearch/xformers.git@main#egg=xformers
```

### Torch/CUDA mismatch

**Error**: `RuntimeError: CUDA error: CUBLAS_STATUS_NOT_INITIALIZED`

**Fix**:
```bash
# Check versions
python -c "import torch; print(torch.__version__, torch.cuda.is_available())"

# Reinstall PyTorch with correct CUDA
pip uninstall torch torchvision
pip install torch torchvision --index-url https://download.pytorch.org/whl/cu121
```

## Memory Issues

### CUDA out of memory

**Error**: `torch.cuda.OutOfMemoryError: CUDA out of memory`

**Solutions**:

```python
# Solution 1: Enable CPU offloading
pipe.enable_model_cpu_offload()

# Solution 2: Sequential CPU offload (more aggressive)
pipe.enable_sequential_cpu_offload()

# Solution 3: Attention slicing
pipe.enable_attention_slicing()

# Solution 4: VAE slicing for large images
pipe.enable_vae_slicing()

# Solution 5: Use lower precision
pipe = DiffusionPipeline.from_pretrained(
    "model-id",
    torch_dtype=torch.float16  # or torch.bfloat16
)

# Solution 6: Reduce batch size
image = pipe(prompt, num_images_per_prompt=1).images[0]

# Solution 7: Generate smaller images
image = pipe(prompt, height=512, width=512).images[0]

# Solution 8: Clear cache between generations
import gc
torch.cuda.empty_cache()
gc.collect()
```

### Memory grows over time

**Problem**: Memory usage increases with each generation

**Fix**:
```python
import gc
import torch

def generate_with_cleanup(pipe, prompt, **kwargs):
    try:
        image = pipe(prompt, **kwargs).images[0]
        return image
    finally:
        # Clear cache after generation
        if torch.cuda.is_available():
            torch.cuda.empty_cache()
        gc.collect()
```

### Large model loading fails

**Error**: `RuntimeError: Unable to load model weights`

**Fix**:
```python
# Use low CPU memory mode
pipe = DiffusionPipeline.from_pretrained(
    "large-model-id",
    low_cpu_mem_usage=True,
    torch_dtype=torch.float16
)
```

## Generation Issues

### Black images

**Problem**: Output images are completely black

**Solutions**:
```python
# Solution 1: Disable safety checker
pipe.safety_checker = None

# Solution 2: Check VAE scaling
# The issue might be with VAE encoding/decoding
latents = latents / pipe.vae.config.scaling_factor  # Before decode

# Solution 3: Ensure proper dtype
pipe = pipe.to(dtype=torch.float16)
pipe.vae = pipe.vae.to(dtype=torch.float32)  # VAE often needs fp32

# Solution 4: Check guidance scale
# Too high can cause issues
image = pipe(prompt, guidance_scale=7.5).images[0]  # Not 20+
```

### Noise/static images

**Problem**: Output looks like random noise

**Solutions**:
```python
# Solution 1: Increase inference steps
image = pipe(prompt, num_inference_steps=50).images[0]

# Solution 2: Check scheduler configuration
pipe.scheduler = pipe.scheduler.from_config(pipe.scheduler.config)

# Solution 3: Verify model was loaded correctly
print(pipe.unet)  # Should show model architecture
```

### Blurry images

**Problem**: Output images are low quality or blurry

**Solutions**:
```python
# Solution 1: Use more steps
image = pipe(prompt, num_inference_steps=50).images[0]

# Solution 2: Use better VAE
from diffusers import AutoencoderKL
vae = AutoencoderKL.from_pretrained("stabilityai/sd-vae-ft-mse")
pipe.vae = vae

# Solution 3: Use SDXL or refiner
pipe = DiffusionPipeline.from_pretrained(
    "stabilityai/stable-diffusion-xl-base-1.0"
)

# Solution 4: Upscale with img2img
upscale_pipe = StableDiffusionImg2ImgPipeline.from_pretrained(...)
upscaled = upscale_pipe(
    prompt=prompt,
    image=image.resize((1024, 1024)),
    strength=0.3
).images[0]
```

### Prompt not being followed

**Problem**: Generated image doesn't match the prompt

**Solutions**:
```python
# Solution 1: Increase guidance scale
image = pipe(prompt, guidance_scale=10.0).images[0]

# Solution 2: Use negative prompts
image = pipe(
    prompt="A red car",
    negative_prompt="blue, green, yellow, wrong color",
    guidance_scale=7.5
).images[0]

# Solution 3: Use prompt weighting
# Emphasize important words
prompt = "A (red:1.5) car on a street"

# Solution 4: Use longer, more detailed prompts
prompt = """
A bright red sports car, ferrari style, parked on a city street,
photorealistic, high detail, 8k, professional photography
"""
```

### Distorted faces/hands

**Problem**: Faces and hands look deformed

**Solutions**:
```python
# Solution 1: Use negative prompts
negative_prompt = """
bad hands, bad anatomy, deformed, ugly, blurry,
extra fingers, mutated hands, poorly drawn hands,
poorly drawn face, mutation, deformed face
"""

# Solution 2: Use face-specific models
# ADetailer or similar post-processing

# Solution 3: Use ControlNet for poses
# Load pose estimation and condition generation

# Solution 4: Inpaint problematic areas
mask = create_face_mask(image)
fixed = inpaint_pipe(
    prompt="beautiful detailed face",
    image=image,
    mask_image=mask
).images[0]
```

## Scheduler Issues

### Scheduler not compatible

**Error**: `ValueError: Scheduler ... is not compatible with pipeline`

**Fix**:
```python
from diffusers import EulerDiscreteScheduler

# Create scheduler from config
pipe.scheduler = EulerDiscreteScheduler.from_config(
    pipe.scheduler.config
)

# Check compatible schedulers
print(pipe.scheduler.compatibles)
```

### Wrong number of steps

**Problem**: Model generates different quality with same steps

**Fix**:
```python
# Reset timesteps explicitly
pipe.scheduler.set_timesteps(num_inference_steps)

# Check scheduler's step count
print(len(pipe.scheduler.timesteps))
```

## LoRA Issues

### LoRA weights not loading

**Error**: `RuntimeError: Error(s) in loading state_dict for UNet2DConditionModel`

**Fix**:
```python
# Check weight file format
# Should be .safetensors or .bin

# Load with correct key prefix
pipe.load_lora_weights(
    "path/to/lora",
    weight_name="lora.safetensors"
)

# Try loading into specific component
pipe.unet.load_attn_procs("path/to/lora")
```

### LoRA not affecting output

**Problem**: Generated images look the same with/without LoRA

**Fix**:
```python
# Fuse LoRA weights
pipe.fuse_lora(lora_scale=1.0)

# Or set scale explicitly
pipe.set_adapters(["lora_name"], adapter_weights=[1.0])

# Verify LoRA is loaded
print(list(pipe.unet.attn_processors.keys()))
```

### Multiple LoRAs conflict

**Problem**: Multiple LoRAs produce artifacts

**Fix**:
```python
# Load with different adapter names
pipe.load_lora_weights("lora1", adapter_name="style")
pipe.load_lora_weights("lora2", adapter_name="subject")

# Balance weights
pipe.set_adapters(
    ["style", "subject"],
    adapter_weights=[0.5, 0.5]  # Lower weights
)

# Or use LoRA merge before loading
# Merge LoRAs offline with appropriate ratios
```

## ControlNet Issues

### ControlNet not conditioning

**Problem**: ControlNet has no effect on output

**Fix**:
```python
# Check control image format
# Should be RGB, matching generation size
control_image = control_image.resize((512, 512))

# Increase conditioning scale
image = pipe(
    prompt=prompt,
    image=control_image,
    controlnet_conditioning_scale=1.0,  # Try 0.5-1.5
    num_inference_steps=30
).images[0]

# Verify ControlNet is loaded
print(pipe.controlnet)
```

### Control image preprocessing

**Fix**:
```python
from controlnet_aux import CannyDetector

# Proper preprocessing
canny = CannyDetector()
control_image = canny(input_image)

# Ensure correct format
control_image = control_image.convert("RGB")
control_image = control_image.resize((512, 512))
```

## Hub/Download Issues

### Model download fails

**Error**: `requests.exceptions.ConnectionError`

**Fix**:
```bash
# Set longer timeout
export HF_HUB_DOWNLOAD_TIMEOUT=600

# Use mirror if available
export HF_ENDPOINT=https://hf-mirror.com

# Or download manually
huggingface-cli download stable-diffusion-v1-5/stable-diffusion-v1-5
```

### Cache issues

**Error**: `OSError: Can't load model from cache`

**Fix**:
```bash
# Clear cache
rm -rf ~/.cache/huggingface/hub

# Or set different cache location
export HF_HOME=/path/to/cache

# Force re-download
pipe = DiffusionPipeline.from_pretrained(
    "model-id",
    force_download=True
)
```

### Access denied for gated models

**Error**: `401 Client Error: Unauthorized`

**Fix**:
```bash
# Login to Hugging Face
huggingface-cli login

# Or use token
pipe = DiffusionPipeline.from_pretrained(
    "model-id",
    token="hf_xxxxx"
)

# Accept model license on Hub website first
```

## Performance Issues

### Slow generation

**Problem**: Generation takes too long

**Solutions**:
```python
# Solution 1: Use faster scheduler
from diffusers import DPMSolverMultistepScheduler
pipe.scheduler = DPMSolverMultistepScheduler.from_config(
    pipe.scheduler.config
)

# Solution 2: Reduce steps
image = pipe(prompt, num_inference_steps=20).images[0]

# Solution 3: Use LCM
from diffusers import LCMScheduler
pipe.load_lora_weights("latent-consistency/lcm-lora-sdxl")
pipe.scheduler = LCMScheduler.from_config(pipe.scheduler.config)
image = pipe(prompt, num_inference_steps=4, guidance_scale=1.0).images[0]

# Solution 4: Enable xFormers
pipe.enable_xformers_memory_efficient_attention()

# Solution 5: Compile model
pipe.unet = torch.compile(pipe.unet, mode="reduce-overhead", fullgraph=True)
```

### First generation is slow

**Problem**: First image takes much longer

**Fix**:
```python
# Warm up the model
_ = pipe("warmup", num_inference_steps=1)

# Then run actual generation
image = pipe(prompt, num_inference_steps=50).images[0]

# Compile for faster subsequent runs
pipe.unet = torch.compile(pipe.unet)
```

## Debugging Tips

### Enable debug logging

```python
import logging
logging.basicConfig(level=logging.DEBUG)

# Or for specific modules
logging.getLogger("diffusers").setLevel(logging.DEBUG)
logging.getLogger("transformers").setLevel(logging.DEBUG)
```

### Check model components

```python
# Print pipeline components
print(pipe.components)

# Check model config
print(pipe.unet.config)
print(pipe.vae.config)
print(pipe.scheduler.config)

# Verify device placement
print(pipe.device)
for name, module in pipe.components.items():
    if hasattr(module, 'device'):
        print(f"{name}: {module.device}")
```

### Validate inputs

```python
# Check image dimensions
print(f"Height: {height}, Width: {width}")
assert height % 8 == 0, "Height must be divisible by 8"
assert width % 8 == 0, "Width must be divisible by 8"

# Check prompt tokenization
tokens = pipe.tokenizer(prompt, return_tensors="pt")
print(f"Token count: {tokens.input_ids.shape[1]}")  # Max 77 for SD
```

### Save intermediate results

```python
def save_latents_callback(pipe, step_index, timestep, callback_kwargs):
    latents = callback_kwargs["latents"]

    # Decode and save intermediate
    with torch.no_grad():
        image = pipe.vae.decode(latents / pipe.vae.config.scaling_factor).sample
    image = (image / 2 + 0.5).clamp(0, 1)
    image = image.cpu().permute(0, 2, 3, 1).numpy()[0]
    Image.fromarray((image * 255).astype("uint8")).save(f"step_{step_index}.png")

    return callback_kwargs

image = pipe(
    prompt,
    callback_on_step_end=save_latents_callback,
    callback_on_step_end_tensor_inputs=["latents"]
).images[0]
```

## Getting Help

1. **Documentation**: https://huggingface.co/docs/diffusers
2. **GitHub Issues**: https://github.com/huggingface/diffusers/issues
3. **Discord**: https://discord.gg/diffusers
4. **Forum**: https://discuss.huggingface.co

### Reporting Issues

Include:
- Diffusers version: `pip show diffusers`
- PyTorch version: `python -c "import torch; print(torch.__version__)"`
- CUDA version: `nvcc --version`
- GPU model: `nvidia-smi`
- Full error traceback
- Minimal reproducible code
- Model name/ID used




---

## 🚀 Usage

**Reference this template:** `@skill-stable-diffusion-image-generation.md`


**Platform-specific:**
- **GitHub Copilot**: Add to `.github/copilot-instructions.md`
- **Augment Code**: Use `aug context add` command
- **Cursor/Windsurf**: Reference in chat with `@filename`
- **Claude**: Add to Project Knowledge
- **ChatGPT**: Add to Custom GPT configuration
