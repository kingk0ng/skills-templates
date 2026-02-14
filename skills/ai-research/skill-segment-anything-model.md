---
id: skill-segment-anything-model
type: skill
name: segment-anything-model
description: Foundation model for image segmentation with zero-shot transfer. Use
  when you need to segment any object in images using points, boxes, or masks as prompts,
  or automatically generate all object masks in an image.
category: ai-research
complexity: medium
keywords:
- deploy
- deployment
- git
- github
- optimization
- performance
- python
capabilities: []
token_estimate: 1855
has_references: true
reference_count: 2
---

<!-- Converted from Claude Skill Template -->
<!-- Complexity: Medium -->
<!-- Estimated Tokens: ~1,855 -->
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




# segment-anything-model

> Foundation model for image segmentation with zero-shot transfer. Use when you need to segment any object in images using points, boxes, or masks as prompts, or automatically generate all object masks in an image.

# Segment Anything Model (SAM)

Comprehensive guide to using Meta AI's Segment Anything Model for zero-shot image segmentation.

## When to use SAM

**Use SAM when:**
- Need to segment any object in images without task-specific training
- Building interactive annotation tools with point/box prompts
- Generating training data for other vision models
- Need zero-shot transfer to new image domains
- Building object detection/segmentation pipelines
- Processing medical, satellite, or domain-specific images

**Key features:**
- **Zero-shot segmentation**: Works on any image domain without fine-tuning
- **Flexible prompts**: Points, bounding boxes, or previous masks
- **Automatic segmentation**: Generate all object masks automatically
- **High quality**: Trained on 1.1 billion masks from 11 million images
- **Multiple model sizes**: ViT-B (fastest), ViT-L, ViT-H (most accurate)
- **ONNX export**: Deploy in browsers and edge devices

**Use alternatives instead:**
- **YOLO/Detectron2**: For real-time object detection with classes
- **Mask2Former**: For semantic/panoptic segmentation with categories
- **GroundingDINO + SAM**: For text-prompted segmentation
- **SAM 2**: For video segmentation tasks

## Quick start

### Installation

```bash
# From GitHub
pip install git+https://github.com/facebookresearch/segment-anything.git

# Optional dependencies
pip install opencv-python pycocotools matplotlib

# Or use HuggingFace transformers
pip install transformers
```

### Download checkpoints

```bash
# ViT-H (largest, most accurate) - 2.4GB
wget https://dl.fbaipublicfiles.com/segment_anything/sam_vit_h_4b8939.pth

# ViT-L (medium) - 1.2GB
wget https://dl.fbaipublicfiles.com/segment_anything/sam_vit_l_0b3195.pth

# ViT-B (smallest, fastest) - 375MB
wget https://dl.fbaipublicfiles.com/segment_anything/sam_vit_b_01ec64.pth
```

### Basic usage with SamPredictor

```python
import numpy as np
from segment_anything import sam_model_registry, SamPredictor

# Load model
sam = sam_model_registry["vit_h"](checkpoint="sam_vit_h_4b8939.pth")
sam.to(device="cuda")

# Create predictor
predictor = SamPredictor(sam)

# Set image (computes embeddings once)
image = cv2.imread("image.jpg")
image = cv2.cvtColor(image, cv2.COLOR_BGR2RGB)
predictor.set_image(image)

# Predict with point prompts
input_point = np.array([[500, 375]])  # (x, y) coordinates
input_label = np.array([1])  # 1 = foreground, 0 = background

masks, scores, logits = predictor.predict(
    point_coords=input_point,
    point_labels=input_label,
    multimask_output=True  # Returns 3 mask options
)

# Select best mask
best_mask = masks[np.argmax(scores)]
```

### HuggingFace Transformers

```python
import torch
from PIL import Image
from transformers import SamModel, SamProcessor

# Load model and processor
model = SamModel.from_pretrained("facebook/sam-vit-huge")
processor = SamProcessor.from_pretrained("facebook/sam-vit-huge")
model.to("cuda")

# Process image with point prompt
image = Image.open("image.jpg")
input_points = [[[450, 600]]]  # Batch of points

inputs = processor(image, input_points=input_points, return_tensors="pt")
inputs = {k: v.to("cuda") for k, v in inputs.items()}

# Generate masks
with torch.no_grad():
    outputs = model(**inputs)

# Post-process masks to original size
masks = processor.image_processor.post_process_masks(
    outputs.pred_masks.cpu(),
    inputs["original_sizes"].cpu(),
    inputs["reshaped_input_sizes"].cpu()
)
```

## Core concepts

### Model architecture

```
SAM Architecture:
┌─────────────────┐     ┌─────────────────┐     ┌─────────────────┐
│  Image Encoder  │────▶│ Prompt Encoder  │────▶│  Mask Decoder   │
│     (ViT)       │     │ (Points/Boxes)  │     │ (Transformer)   │
└─────────────────┘     └─────────────────┘     └─────────────────┘
        │                       │                       │
   Image Embeddings      Prompt Embeddings         Masks + IoU
   (computed once)       (per prompt)             predictions
```

### Model variants

| Model | Checkpoint | Size | Speed | Accuracy |
|-------|------------|------|-------|----------|
| ViT-H | `vit_h` | 2.4 GB | Slowest | Best |
| ViT-L | `vit_l` | 1.2 GB | Medium | Good |
| ViT-B | `vit_b` | 375 MB | Fastest | Good |

### Prompt types

| Prompt | Description | Use Case |
|--------|-------------|----------|
| Point (foreground) | Click on object | Single object selection |
| Point (background) | Click outside object | Exclude regions |
| Bounding box | Rectangle around object | Larger objects |
| Previous mask | Low-res mask input | Iterative refinement |

## Interactive segmentation

### Point prompts

```python
# Single foreground point
input_point = np.array([[500, 375]])
input_label = np.array([1])

masks, scores, logits = predictor.predict(
    point_coords=input_point,
    point_labels=input_label,
    multimask_output=True
)

# Multiple points (foreground + background)
input_points = np.array([[500, 375], [600, 400], [450, 300]])
input_labels = np.array([1, 1, 0])  # 2 foreground, 1 background

masks, scores, logits = predictor.predict(
    point_coords=input_points,
    point_labels=input_labels,
    multimask_output=False  # Single mask when prompts are clear
)
```

### Box prompts

```python
# Bounding box [x1, y1, x2, y2]
input_box = np.array([425, 600, 700, 875])

masks, scores, logits = predictor.predict(
    box=input_box,
    multimask_output=False
)
```

### Combined prompts

```python
# Box + points for precise control
masks, scores, logits = predictor.predict(
    point_coords=np.array([[500, 375]]),
    point_labels=np.array([1]),
    box=np.array([400, 300, 700, 600]),
    multimask_output=False
)
```

### Iterative refinement

```python
# Initial prediction
masks, scores, logits = predictor.predict(
    point_coords=np.array([[500, 375]]),
    point_labels=np.array([1]),
    multimask_output=True
)

# Refine with additional point using previous mask
masks, scores, logits = predictor.predict(
    point_coords=np.array([[500, 375], [550, 400]]),
    point_labels=np.array([1, 0]),  # Add background point
    mask_input=logits[np.argmax(scores)][None, :, :],  # Use best mask
    multimask_output=False
)
```

## Automatic mask generation

### Basic automatic segmentation

```python
from segment_anything import SamAutomaticMaskGenerator

# Create generator
mask_generator = SamAutomaticMaskGenerator(sam)

# Generate all masks
masks = mask_generator.generate(image)

# Each mask contains:
# - segmentation: binary mask
# - bbox: [x, y, w, h]
# - area: pixel count
# - predicted_iou: quality score
# - stability_score: robustness score
# - point_coords: generating point
```

### Customized generation

```python
mask_generator = SamAutomaticMaskGenerator(
    model=sam,
    points_per_side=32,          # Grid density (more = more masks)
    pred_iou_thresh=0.88,        # Quality threshold
    stability_score_thresh=0.95,  # Stability threshold
    crop_n_layers=1,             # Multi-scale crops
    crop_n_points_downscale_factor=2,
    min_mask_region_area=100,    # Remove tiny masks
)

masks = mask_generator.generate(image)
```

### Filtering masks

```python
# Sort by area (largest first)
masks = sorted(masks, key=lambda x: x['area'], reverse=True)

# Filter by predicted IoU
high_quality = [m for m in masks if m['predicted_iou'] > 0.9]

# Filter by stability score
stable_masks = [m for m in masks if m['stability_score'] > 0.95]
```

## Batched inference

### Multiple images

```python
# Process multiple images efficiently
images = [cv2.imread(f"image_{i}.jpg") for i in range(10)]

all_masks = []
for image in images:
    predictor.set_image(image)
    masks, _, _ = predictor.predict(
        point_coords=np.array([[500, 375]]),
        point_labels=np.array([1]),
        multimask_output=True
    )
    all_masks.append(masks)
```

### Multiple prompts per image

```python
# Process multiple prompts efficiently (one image encoding)
predictor.set_image(image)

# Batch of point prompts
points = [
    np.array([[100, 100]]),
    np.array([[200, 200]]),
    np.array([[300, 300]])
]

all_masks = []
for point in points:
    masks, scores, _ = predictor.predict(
        point_coords=point,
        point_labels=np.array([1]),
        multimask_output=True
    )
    all_masks.append(masks[np.argmax(scores)])
```

## ONNX deployment

### Export model

```bash
python scripts/export_onnx_model.py \
    --checkpoint sam_vit_h_4b8939.pth \
    --model-type vit_h \
    --output sam_onnx.onnx \
    --return-single-mask
```

### Use ONNX model

```python
import onnxruntime

# Load ONNX model
ort_session = onnxruntime.InferenceSession("sam_onnx.onnx")

# Run inference (image embeddings computed separately)
masks = ort_session.run(
    None,
    {
        "image_embeddings": image_embeddings,
        "point_coords": point_coords,
        "point_labels": point_labels,
        "mask_input": np.zeros((1, 1, 256, 256), dtype=np.float32),
        "has_mask_input": np.array([0], dtype=np.float32),
        "orig_im_size": np.array([h, w], dtype=np.float32)
    }
)
```

## Common workflows

### Workflow 1: Annotation tool

```python
import cv2

# Load model
predictor = SamPredictor(sam)
predictor.set_image(image)

def on_click(event, x, y, flags, param):
    if event == cv2.EVENT_LBUTTONDOWN:
        # Foreground point
        masks, scores, _ = predictor.predict(
            point_coords=np.array([[x, y]]),
            point_labels=np.array([1]),
            multimask_output=True
        )
        # Display best mask
        display_mask(masks[np.argmax(scores)])
```

### Workflow 2: Object extraction

```python
def extract_object(image, point):
    """Extract object at point with transparent background."""
    predictor.set_image(image)

    masks, scores, _ = predictor.predict(
        point_coords=np.array([point]),
        point_labels=np.array([1]),
        multimask_output=True
    )

    best_mask = masks[np.argmax(scores)]

    # Create RGBA output
    rgba = np.zeros((image.shape[0], image.shape[1], 4), dtype=np.uint8)
    rgba[:, :, :3] = image
    rgba[:, :, 3] = best_mask * 255

    return rgba
```

### Workflow 3: Medical image segmentation

```python
# Process medical images (grayscale to RGB)
medical_image = cv2.imread("scan.png", cv2.IMREAD_GRAYSCALE)
rgb_image = cv2.cvtColor(medical_image, cv2.COLOR_GRAY2RGB)

predictor.set_image(rgb_image)

# Segment region of interest
masks, scores, _ = predictor.predict(
    box=np.array([x1, y1, x2, y2]),  # ROI bounding box
    multimask_output=True
)
```

## Output format

### Mask data structure

```python
# SamAutomaticMaskGenerator output
{
    "segmentation": np.ndarray,  # H×W binary mask
    "bbox": [x, y, w, h],        # Bounding box
    "area": int,                 # Pixel count
    "predicted_iou": float,      # 0-1 quality score
    "stability_score": float,    # 0-1 robustness score
    "crop_box": [x, y, w, h],    # Generation crop region
    "point_coords": [[x, y]],    # Input point
}
```

### COCO RLE format

```python
from pycocotools import mask as mask_utils

# Encode mask to RLE
rle = mask_utils.encode(np.asfortranarray(mask.astype(np.uint8)))
rle["counts"] = rle["counts"].decode("utf-8")

# Decode RLE to mask
decoded_mask = mask_utils.decode(rle)
```

## Performance optimization

### GPU memory

```python
# Use smaller model for limited VRAM
sam = sam_model_registry["vit_b"](checkpoint="sam_vit_b_01ec64.pth")

# Process images in batches
# Clear CUDA cache between large batches
torch.cuda.empty_cache()
```

### Speed optimization

```python
# Use half precision
sam = sam.half()

# Reduce points for automatic generation
mask_generator = SamAutomaticMaskGenerator(
    model=sam,
    points_per_side=16,  # Default is 32
)

# Use ONNX for deployment
# Export with --return-single-mask for faster inference
```

## Common issues

| Issue | Solution |
|-------|----------|
| Out of memory | Use ViT-B model, reduce image size |
| Slow inference | Use ViT-B, reduce points_per_side |
| Poor mask quality | Try different prompts, use box + points |
| Edge artifacts | Use stability_score filtering |
| Small objects missed | Increase points_per_side |

## References

- **[Advanced Usage](references/advanced-usage.md)** - Batching, fine-tuning, integration
- **[Troubleshooting](references/troubleshooting.md)** - Common issues and solutions

## Resources

- **GitHub**: https://github.com/facebookresearch/segment-anything
- **Paper**: https://arxiv.org/abs/2304.02643
- **Demo**: https://segment-anything.com
- **SAM 2 (Video)**: https://github.com/facebookresearch/segment-anything-2
- **HuggingFace**: https://huggingface.co/facebook/sam-vit-huge


---


## 📚 Reference Materials


### Advanced Usage

# Segment Anything Advanced Usage Guide

## SAM 2 (Video Segmentation)

### Overview

SAM 2 extends SAM to video segmentation with streaming memory architecture:

```bash
pip install git+https://github.com/facebookresearch/segment-anything-2.git
```

### Video segmentation

```python
from sam2.build_sam import build_sam2_video_predictor

predictor = build_sam2_video_predictor("sam2_hiera_l.yaml", "sam2_hiera_large.pt")

# Initialize with video
predictor.init_state(video_path="video.mp4")

# Add prompt on first frame
predictor.add_new_points(
    frame_idx=0,
    obj_id=1,
    points=[[100, 200]],
    labels=[1]
)

# Propagate through video
for frame_idx, masks in predictor.propagate_in_video():
    # masks contains segmentation for all tracked objects
    process_frame(frame_idx, masks)
```

### SAM 2 vs SAM comparison

| Feature | SAM | SAM 2 |
|---------|-----|-------|
| Input | Images only | Images + Videos |
| Architecture | ViT + Decoder | Hiera + Memory |
| Memory | Per-image | Streaming memory bank |
| Tracking | No | Yes, across frames |
| Models | ViT-B/L/H | Hiera-T/S/B+/L |

## Grounded SAM (Text-Prompted Segmentation)

### Setup

```bash
pip install groundingdino-py
pip install git+https://github.com/facebookresearch/segment-anything.git
```

### Text-to-mask pipeline

```python
from groundingdino.util.inference import load_model, predict
from segment_anything import sam_model_registry, SamPredictor
import cv2

# Load Grounding DINO
grounding_model = load_model("groundingdino_swint_ogc.pth", "GroundingDINO_SwinT_OGC.py")

# Load SAM
sam = sam_model_registry["vit_h"](checkpoint="sam_vit_h_4b8939.pth")
predictor = SamPredictor(sam)

def text_to_mask(image, text_prompt, box_threshold=0.3, text_threshold=0.25):
    """Generate masks from text description."""
    # Get bounding boxes from text
    boxes, logits, phrases = predict(
        model=grounding_model,
        image=image,
        caption=text_prompt,
        box_threshold=box_threshold,
        text_threshold=text_threshold
    )

    # Generate masks with SAM
    predictor.set_image(image)

    masks = []
    for box in boxes:
        # Convert normalized box to pixel coordinates
        h, w = image.shape[:2]
        box_pixels = box * np.array([w, h, w, h])

        mask, score, _ = predictor.predict(
            box=box_pixels,
            multimask_output=False
        )
        masks.append(mask[0])

    return masks, boxes, phrases

# Usage
image = cv2.imread("image.jpg")
image = cv2.cvtColor(image, cv2.COLOR_BGR2RGB)

masks, boxes, phrases = text_to_mask(image, "person . dog . car")
```

## Batched Processing

### Efficient multi-image processing

```python
import torch
from segment_anything import SamPredictor, sam_model_registry

class BatchedSAM:
    def __init__(self, checkpoint, model_type="vit_h", device="cuda"):
        self.sam = sam_model_registry[model_type](checkpoint=checkpoint)
        self.sam.to(device)
        self.predictor = SamPredictor(self.sam)
        self.device = device

    def process_batch(self, images, prompts):
        """Process multiple images with corresponding prompts."""
        results = []

        for image, prompt in zip(images, prompts):
            self.predictor.set_image(image)

            if "point" in prompt:
                masks, scores, _ = self.predictor.predict(
                    point_coords=prompt["point"],
                    point_labels=prompt["label"],
                    multimask_output=True
                )
            elif "box" in prompt:
                masks, scores, _ = self.predictor.predict(
                    box=prompt["box"],
                    multimask_output=False
                )

            results.append({
                "masks": masks,
                "scores": scores,
                "best_mask": masks[np.argmax(scores)]
            })

        return results

# Usage
batch_sam = BatchedSAM("sam_vit_h_4b8939.pth")

images = [cv2.imread(f"image_{i}.jpg") for i in range(10)]
prompts = [{"point": np.array([[100, 100]]), "label": np.array([1])} for _ in range(10)]

results = batch_sam.process_batch(images, prompts)
```

### Parallel automatic mask generation

```python
from concurrent.futures import ThreadPoolExecutor
from segment_anything import SamAutomaticMaskGenerator

def generate_masks_parallel(images, num_workers=4):
    """Generate masks for multiple images in parallel."""
    # Note: Each worker needs its own model instance
    def worker_init():
        sam = sam_model_registry["vit_b"](checkpoint="sam_vit_b_01ec64.pth")
        return SamAutomaticMaskGenerator(sam)

    generators = [worker_init() for _ in range(num_workers)]

    def process_image(args):
        idx, image = args
        generator = generators[idx % num_workers]
        return generator.generate(image)

    with ThreadPoolExecutor(max_workers=num_workers) as executor:
        results = list(executor.map(process_image, enumerate(images)))

    return results
```

## Custom Integration

### FastAPI service

```python
from fastapi import FastAPI, File, UploadFile
from pydantic import BaseModel
import numpy as np
import cv2
import io

app = FastAPI()

# Load model once
sam = sam_model_registry["vit_h"](checkpoint="sam_vit_h_4b8939.pth")
sam.to("cuda")
predictor = SamPredictor(sam)

class PointPrompt(BaseModel):
    x: int
    y: int
    label: int = 1

@app.post("/segment/point")
async def segment_with_point(
    file: UploadFile = File(...),
    points: list[PointPrompt] = []
):
    # Read image
    contents = await file.read()
    nparr = np.frombuffer(contents, np.uint8)
    image = cv2.imdecode(nparr, cv2.IMREAD_COLOR)
    image = cv2.cvtColor(image, cv2.COLOR_BGR2RGB)

    # Set image
    predictor.set_image(image)

    # Prepare prompts
    point_coords = np.array([[p.x, p.y] for p in points])
    point_labels = np.array([p.label for p in points])

    # Generate masks
    masks, scores, _ = predictor.predict(
        point_coords=point_coords,
        point_labels=point_labels,
        multimask_output=True
    )

    best_idx = np.argmax(scores)

    return {
        "mask": masks[best_idx].tolist(),
        "score": float(scores[best_idx]),
        "all_scores": scores.tolist()
    }

@app.post("/segment/auto")
async def segment_automatic(file: UploadFile = File(...)):
    contents = await file.read()
    nparr = np.frombuffer(contents, np.uint8)
    image = cv2.imdecode(nparr, cv2.IMREAD_COLOR)
    image = cv2.cvtColor(image, cv2.COLOR_BGR2RGB)

    mask_generator = SamAutomaticMaskGenerator(sam)
    masks = mask_generator.generate(image)

    return {
        "num_masks": len(masks),
        "masks": [
            {
                "bbox": m["bbox"],
                "area": m["area"],
                "predicted_iou": m["predicted_iou"],
                "stability_score": m["stability_score"]
            }
            for m in masks
        ]
    }
```

### Gradio interface

```python
import gradio as gr
import numpy as np

# Load model
sam = sam_model_registry["vit_h"](checkpoint="sam_vit_h_4b8939.pth")
predictor = SamPredictor(sam)

def segment_image(image, evt: gr.SelectData):
    """Segment object at clicked point."""
    predictor.set_image(image)

    point = np.array([[evt.index[0], evt.index[1]]])
    label = np.array([1])

    masks, scores, _ = predictor.predict(
        point_coords=point,
        point_labels=label,
        multimask_output=True
    )

    best_mask = masks[np.argmax(scores)]

    # Overlay mask on image
    overlay = image.copy()
    overlay[best_mask] = overlay[best_mask] * 0.5 + np.array([255, 0, 0]) * 0.5

    return overlay

with gr.Blocks() as demo:
    gr.Markdown("# SAM Interactive Segmentation")
    gr.Markdown("Click on an object to segment it")

    with gr.Row():
        input_image = gr.Image(label="Input Image", interactive=True)
        output_image = gr.Image(label="Segmented Image")

    input_image.select(segment_image, inputs=[input_image], outputs=[output_image])

demo.launch()
```

## Fine-Tuning SAM

### LoRA fine-tuning (experimental)

```python
from peft import LoraConfig, get_peft_model
from transformers import SamModel

# Load model
model = SamModel.from_pretrained("facebook/sam-vit-base")

# Configure LoRA
lora_config = LoraConfig(
    r=16,
    lora_alpha=32,
    target_modules=["qkv"],  # Attention layers
    lora_dropout=0.1,
    bias="none",
)

# Apply LoRA
model = get_peft_model(model, lora_config)

# Training loop (simplified)
optimizer = torch.optim.AdamW(model.parameters(), lr=1e-4)

for batch in dataloader:
    outputs = model(
        pixel_values=batch["pixel_values"],
        input_points=batch["input_points"],
        input_labels=batch["input_labels"]
    )

    # Custom loss (e.g., IoU loss with ground truth)
    loss = compute_loss(outputs.pred_masks, batch["gt_masks"])
    loss.backward()
    optimizer.step()
    optimizer.zero_grad()
```

### MedSAM (Medical imaging)

```python
# MedSAM is a fine-tuned SAM for medical images
# https://github.com/bowang-lab/MedSAM

from segment_anything import sam_model_registry, SamPredictor
import torch

# Load MedSAM checkpoint
medsam = sam_model_registry["vit_b"](checkpoint="medsam_vit_b.pth")
medsam.to("cuda")

predictor = SamPredictor(medsam)

# Process medical image
# Convert grayscale to RGB if needed
medical_image = cv2.imread("ct_scan.png", cv2.IMREAD_GRAYSCALE)
rgb_image = np.stack([medical_image] * 3, axis=-1)

predictor.set_image(rgb_image)

# Segment with box prompt (common for medical imaging)
masks, scores, _ = predictor.predict(
    box=np.array([x1, y1, x2, y2]),
    multimask_output=False
)
```

## Advanced Mask Processing

### Mask refinement

```python
import cv2
from scipy import ndimage

def refine_mask(mask, kernel_size=5, iterations=2):
    """Refine mask with morphological operations."""
    kernel = cv2.getStructuringElement(cv2.MORPH_ELLIPSE, (kernel_size, kernel_size))

    # Close small holes
    closed = cv2.morphologyEx(mask.astype(np.uint8), cv2.MORPH_CLOSE, kernel, iterations=iterations)

    # Remove small noise
    opened = cv2.morphologyEx(closed, cv2.MORPH_OPEN, kernel, iterations=iterations)

    return opened.astype(bool)

def fill_holes(mask):
    """Fill holes in mask."""
    filled = ndimage.binary_fill_holes(mask)
    return filled

def remove_small_regions(mask, min_area=100):
    """Remove small disconnected regions."""
    labeled, num_features = ndimage.label(mask)
    sizes = ndimage.sum(mask, labeled, range(1, num_features + 1))

    # Keep only regions larger than min_area
    mask_clean = np.zeros_like(mask)
    for i, size in enumerate(sizes, 1):
        if size >= min_area:
            mask_clean[labeled == i] = True

    return mask_clean
```

### Mask to polygon conversion

```python
import cv2

def mask_to_polygons(mask, epsilon_factor=0.01):
    """Convert binary mask to polygon coordinates."""
    contours, _ = cv2.findContours(
        mask.astype(np.uint8),
        cv2.RETR_EXTERNAL,
        cv2.CHAIN_APPROX_SIMPLE
    )

    polygons = []
    for contour in contours:
        epsilon = epsilon_factor * cv2.arcLength(contour, True)
        approx = cv2.approxPolyDP(contour, epsilon, True)
        polygon = approx.squeeze().tolist()
        if len(polygon) >= 3:  # Valid polygon
            polygons.append(polygon)

    return polygons

def polygons_to_mask(polygons, height, width):
    """Convert polygons back to binary mask."""
    mask = np.zeros((height, width), dtype=np.uint8)
    for polygon in polygons:
        pts = np.array(polygon, dtype=np.int32)
        cv2.fillPoly(mask, [pts], 1)
    return mask.astype(bool)
```

### Multi-scale segmentation

```python
def multiscale_segment(image, predictor, point, scales=[0.5, 1.0, 2.0]):
    """Generate masks at multiple scales and combine."""
    h, w = image.shape[:2]
    masks_all = []

    for scale in scales:
        # Resize image
        new_h, new_w = int(h * scale), int(w * scale)
        scaled_image = cv2.resize(image, (new_w, new_h))
        scaled_point = (point * scale).astype(int)

        # Segment
        predictor.set_image(scaled_image)
        masks, scores, _ = predictor.predict(
            point_coords=scaled_point.reshape(1, 2),
            point_labels=np.array([1]),
            multimask_output=True
        )

        # Resize mask back
        best_mask = masks[np.argmax(scores)]
        original_mask = cv2.resize(best_mask.astype(np.uint8), (w, h)) > 0.5

        masks_all.append(original_mask)

    # Combine masks (majority voting)
    combined = np.stack(masks_all, axis=0)
    final_mask = np.sum(combined, axis=0) >= len(scales) // 2 + 1

    return final_mask
```

## Performance Optimization

### TensorRT acceleration

```python
import tensorrt as trt
import pycuda.driver as cuda
import pycuda.autoinit

def export_to_tensorrt(onnx_path, engine_path, fp16=True):
    """Convert ONNX model to TensorRT engine."""
    logger = trt.Logger(trt.Logger.WARNING)
    builder = trt.Builder(logger)
    network = builder.create_network(1 << int(trt.NetworkDefinitionCreationFlag.EXPLICIT_BATCH))
    parser = trt.OnnxParser(network, logger)

    with open(onnx_path, 'rb') as f:
        if not parser.parse(f.read()):
            for error in range(parser.num_errors):
                print(parser.get_error(error))
            return None

    config = builder.create_builder_config()
    config.max_workspace_size = 1 << 30  # 1GB

    if fp16:
        config.set_flag(trt.BuilderFlag.FP16)

    engine = builder.build_engine(network, config)

    with open(engine_path, 'wb') as f:
        f.write(engine.serialize())

    return engine
```

### Memory-efficient inference

```python
class MemoryEfficientSAM:
    def __init__(self, checkpoint, model_type="vit_b"):
        self.sam = sam_model_registry[model_type](checkpoint=checkpoint)
        self.sam.eval()
        self.predictor = None

    def __enter__(self):
        self.sam.to("cuda")
        self.predictor = SamPredictor(self.sam)
        return self

    def __exit__(self, *args):
        self.sam.to("cpu")
        torch.cuda.empty_cache()

    def segment(self, image, points, labels):
        self.predictor.set_image(image)
        masks, scores, _ = self.predictor.predict(
            point_coords=points,
            point_labels=labels,
            multimask_output=True
        )
        return masks, scores

# Usage with context manager (auto-cleanup)
with MemoryEfficientSAM("sam_vit_b_01ec64.pth") as sam:
    masks, scores = sam.segment(image, points, labels)
# CUDA memory freed automatically
```

## Dataset Generation

### Create segmentation dataset

```python
import json

def generate_dataset(images_dir, output_dir, mask_generator):
    """Generate segmentation dataset from images."""
    annotations = []

    for img_path in Path(images_dir).glob("*.jpg"):
        image = cv2.imread(str(img_path))
        image = cv2.cvtColor(image, cv2.COLOR_BGR2RGB)

        # Generate masks
        masks = mask_generator.generate(image)

        # Filter high-quality masks
        good_masks = [m for m in masks if m["predicted_iou"] > 0.9]

        # Save annotations
        for i, mask_data in enumerate(good_masks):
            annotation = {
                "image_id": img_path.stem,
                "mask_id": i,
                "bbox": mask_data["bbox"],
                "area": mask_data["area"],
                "segmentation": mask_to_rle(mask_data["segmentation"]),
                "predicted_iou": mask_data["predicted_iou"],
                "stability_score": mask_data["stability_score"]
            }
            annotations.append(annotation)

    # Save dataset
    with open(output_dir / "annotations.json", "w") as f:
        json.dump(annotations, f)

    return annotations
```




### Troubleshooting

# Segment Anything Troubleshooting Guide

## Installation Issues

### CUDA not available

**Error**: `RuntimeError: CUDA not available`

**Solutions**:
```python
# Check CUDA availability
import torch
print(torch.cuda.is_available())
print(torch.version.cuda)

# Install PyTorch with CUDA
pip install torch torchvision --index-url https://download.pytorch.org/whl/cu121

# If CUDA works but SAM doesn't use it
sam = sam_model_registry["vit_h"](checkpoint="sam_vit_h_4b8939.pth")
sam.to("cuda")  # Explicitly move to GPU
```

### Import errors

**Error**: `ModuleNotFoundError: No module named 'segment_anything'`

**Solutions**:
```bash
# Install from GitHub
pip install git+https://github.com/facebookresearch/segment-anything.git

# Or clone and install
git clone https://github.com/facebookresearch/segment-anything.git
cd segment-anything
pip install -e .

# Verify installation
python -c "from segment_anything import sam_model_registry; print('OK')"
```

### Missing dependencies

**Error**: `ModuleNotFoundError: No module named 'cv2'` or similar

**Solutions**:
```bash
# Install all optional dependencies
pip install opencv-python pycocotools matplotlib onnxruntime onnx

# For pycocotools on Windows
pip install pycocotools-windows
```

## Model Loading Issues

### Checkpoint not found

**Error**: `FileNotFoundError: checkpoint file not found`

**Solutions**:
```bash
# Download correct checkpoint
wget https://dl.fbaipublicfiles.com/segment_anything/sam_vit_h_4b8939.pth

# Verify file integrity
md5sum sam_vit_h_4b8939.pth
# Expected: a7bf3b02f3ebf1267aba913ff637d9a2

# Use absolute path
sam = sam_model_registry["vit_h"](checkpoint="/full/path/to/sam_vit_h_4b8939.pth")
```

### Model type mismatch

**Error**: `KeyError: 'unexpected key in state_dict'`

**Solutions**:
```python
# Ensure model type matches checkpoint
# vit_h checkpoint → vit_h model
sam = sam_model_registry["vit_h"](checkpoint="sam_vit_h_4b8939.pth")

# vit_l checkpoint → vit_l model
sam = sam_model_registry["vit_l"](checkpoint="sam_vit_l_0b3195.pth")

# vit_b checkpoint → vit_b model
sam = sam_model_registry["vit_b"](checkpoint="sam_vit_b_01ec64.pth")
```

### Out of memory during load

**Error**: `CUDA out of memory` during model loading

**Solutions**:
```python
# Use smaller model
sam = sam_model_registry["vit_b"](checkpoint="sam_vit_b_01ec64.pth")

# Load to CPU first, then move
sam = sam_model_registry["vit_h"](checkpoint="sam_vit_h_4b8939.pth")
sam.to("cpu")
torch.cuda.empty_cache()
sam.to("cuda")

# Use half precision
sam = sam_model_registry["vit_h"](checkpoint="sam_vit_h_4b8939.pth")
sam = sam.half()
sam.to("cuda")
```

## Inference Issues

### Image format errors

**Error**: `ValueError: expected input to have 3 channels`

**Solutions**:
```python
import cv2

# Ensure RGB format
image = cv2.imread("image.jpg")
image = cv2.cvtColor(image, cv2.COLOR_BGR2RGB)  # BGR to RGB

# Convert grayscale to RGB
if len(image.shape) == 2:
    image = cv2.cvtColor(image, cv2.COLOR_GRAY2RGB)

# Handle RGBA
if image.shape[2] == 4:
    image = image[:, :, :3]  # Drop alpha channel
```

### Coordinate errors

**Error**: `IndexError: index out of bounds` or incorrect mask location

**Solutions**:
```python
# Ensure points are (x, y) not (row, col)
# x = column index, y = row index
point = np.array([[x, y]])  # Correct

# Verify coordinates are within image bounds
h, w = image.shape[:2]
assert 0 <= x < w and 0 <= y < h, "Point outside image"

# For bounding boxes: [x1, y1, x2, y2]
box = np.array([x1, y1, x2, y2])
assert x1 < x2 and y1 < y2, "Invalid box coordinates"
```

### Empty or incorrect masks

**Problem**: Masks don't match expected object

**Solutions**:
```python
# Try multiple prompts
input_points = np.array([[x1, y1], [x2, y2]])
input_labels = np.array([1, 1])  # Multiple foreground points

# Add background points
input_points = np.array([[obj_x, obj_y], [bg_x, bg_y]])
input_labels = np.array([1, 0])  # 1=foreground, 0=background

# Use box prompt for large objects
box = np.array([x1, y1, x2, y2])
masks, scores, _ = predictor.predict(box=box, multimask_output=False)

# Combine box and point
masks, scores, _ = predictor.predict(
    point_coords=np.array([[center_x, center_y]]),
    point_labels=np.array([1]),
    box=np.array([x1, y1, x2, y2]),
    multimask_output=True
)

# Check scores and select best
print(f"Scores: {scores}")
best_mask = masks[np.argmax(scores)]
```

### Slow inference

**Problem**: Prediction takes too long

**Solutions**:
```python
# Use smaller model
sam = sam_model_registry["vit_b"](checkpoint="sam_vit_b_01ec64.pth")

# Reuse image embeddings
predictor.set_image(image)  # Compute once
for point in points:
    masks, _, _ = predictor.predict(...)  # Fast, reuses embeddings

# Reduce automatic generation points
mask_generator = SamAutomaticMaskGenerator(
    model=sam,
    points_per_side=16,  # Default is 32
)

# Use ONNX for deployment
# Export: python scripts/export_onnx_model.py --return-single-mask
```

## Automatic Mask Generation Issues

### Too many masks

**Problem**: Generating thousands of overlapping masks

**Solutions**:
```python
mask_generator = SamAutomaticMaskGenerator(
    model=sam,
    points_per_side=16,          # Reduce from 32
    pred_iou_thresh=0.92,        # Increase from 0.88
    stability_score_thresh=0.98,  # Increase from 0.95
    box_nms_thresh=0.5,          # More aggressive NMS
    min_mask_region_area=500,    # Remove small masks
)
```

### Too few masks

**Problem**: Missing objects in automatic generation

**Solutions**:
```python
mask_generator = SamAutomaticMaskGenerator(
    model=sam,
    points_per_side=64,          # Increase density
    pred_iou_thresh=0.80,        # Lower threshold
    stability_score_thresh=0.85,  # Lower threshold
    crop_n_layers=2,             # Add multi-scale
    min_mask_region_area=0,      # Keep all masks
)
```

### Small objects missed

**Problem**: Automatic generation misses small objects

**Solutions**:
```python
# Use crop layers for multi-scale detection
mask_generator = SamAutomaticMaskGenerator(
    model=sam,
    crop_n_layers=2,
    crop_n_points_downscale_factor=1,  # Don't reduce points in crops
    min_mask_region_area=10,  # Very small minimum
)

# Or process image patches
def segment_with_patches(image, patch_size=512, overlap=64):
    h, w = image.shape[:2]
    all_masks = []

    for y in range(0, h, patch_size - overlap):
        for x in range(0, w, patch_size - overlap):
            patch = image[y:y+patch_size, x:x+patch_size]
            masks = mask_generator.generate(patch)

            # Offset masks to original coordinates
            for m in masks:
                m['bbox'][0] += x
                m['bbox'][1] += y
                # Offset segmentation mask too

            all_masks.extend(masks)

    return all_masks
```

## Memory Issues

### CUDA out of memory

**Error**: `torch.cuda.OutOfMemoryError: CUDA out of memory`

**Solutions**:
```python
# Use smaller model
sam = sam_model_registry["vit_b"](checkpoint="sam_vit_b_01ec64.pth")

# Clear cache between images
torch.cuda.empty_cache()

# Process images sequentially, not batched
for image in images:
    predictor.set_image(image)
    masks, _, _ = predictor.predict(...)
    torch.cuda.empty_cache()

# Reduce image size
max_size = 1024
h, w = image.shape[:2]
if max(h, w) > max_size:
    scale = max_size / max(h, w)
    image = cv2.resize(image, (int(w*scale), int(h*scale)))

# Use CPU for large batch processing
sam.to("cpu")
```

### RAM out of memory

**Problem**: System runs out of RAM

**Solutions**:
```python
# Process images one at a time
for img_path in image_paths:
    image = cv2.imread(img_path)
    masks = process_image(image)
    save_results(masks)
    del image, masks
    gc.collect()

# Use generators instead of lists
def generate_masks_lazy(image_paths):
    for path in image_paths:
        image = cv2.imread(path)
        masks = mask_generator.generate(image)
        yield path, masks
```

## ONNX Export Issues

### Export fails

**Error**: Various export errors

**Solutions**:
```bash
# Install correct ONNX version
pip install onnx==1.14.0 onnxruntime==1.15.0

# Use correct opset version
python scripts/export_onnx_model.py \
    --checkpoint sam_vit_h_4b8939.pth \
    --model-type vit_h \
    --output sam.onnx \
    --opset 17
```

### ONNX runtime errors

**Error**: `ONNXRuntimeError` during inference

**Solutions**:
```python
import onnxruntime

# Check available providers
print(onnxruntime.get_available_providers())

# Use CPU provider if GPU fails
session = onnxruntime.InferenceSession(
    "sam.onnx",
    providers=['CPUExecutionProvider']
)

# Verify input shapes
for input in session.get_inputs():
    print(f"{input.name}: {input.shape}")
```

## HuggingFace Integration Issues

### Processor errors

**Error**: Issues with SamProcessor

**Solutions**:
```python
from transformers import SamModel, SamProcessor

# Use matching processor and model
model = SamModel.from_pretrained("facebook/sam-vit-huge")
processor = SamProcessor.from_pretrained("facebook/sam-vit-huge")

# Ensure input format
input_points = [[[x, y]]]  # Nested list for batch dimension
inputs = processor(image, input_points=input_points, return_tensors="pt")

# Post-process correctly
masks = processor.image_processor.post_process_masks(
    outputs.pred_masks.cpu(),
    inputs["original_sizes"].cpu(),
    inputs["reshaped_input_sizes"].cpu()
)
```

## Quality Issues

### Jagged mask edges

**Problem**: Masks have rough, pixelated edges

**Solutions**:
```python
import cv2
from scipy import ndimage

def smooth_mask(mask, sigma=2):
    """Smooth mask edges."""
    # Gaussian blur
    smooth = ndimage.gaussian_filter(mask.astype(float), sigma=sigma)
    return smooth > 0.5

def refine_edges(mask, kernel_size=5):
    """Refine mask edges with morphological operations."""
    kernel = cv2.getStructuringElement(cv2.MORPH_ELLIPSE, (kernel_size, kernel_size))
    # Close small gaps
    closed = cv2.morphologyEx(mask.astype(np.uint8), cv2.MORPH_CLOSE, kernel)
    # Open to remove noise
    opened = cv2.morphologyEx(closed, cv2.MORPH_OPEN, kernel)
    return opened.astype(bool)
```

### Incomplete segmentation

**Problem**: Mask doesn't cover entire object

**Solutions**:
```python
# Add multiple points
input_points = np.array([
    [obj_center_x, obj_center_y],
    [obj_left_x, obj_center_y],
    [obj_right_x, obj_center_y],
    [obj_center_x, obj_top_y],
    [obj_center_x, obj_bottom_y]
])
input_labels = np.array([1, 1, 1, 1, 1])

# Use bounding box
masks, _, _ = predictor.predict(
    box=np.array([x1, y1, x2, y2]),
    multimask_output=False
)

# Iterative refinement
mask_input = None
for point in points:
    masks, scores, logits = predictor.predict(
        point_coords=point.reshape(1, 2),
        point_labels=np.array([1]),
        mask_input=mask_input,
        multimask_output=False
    )
    mask_input = logits
```

## Common Error Messages

| Error | Cause | Solution |
|-------|-------|----------|
| `CUDA out of memory` | GPU memory full | Use smaller model, clear cache |
| `expected 3 channels` | Wrong image format | Convert to RGB |
| `index out of bounds` | Invalid coordinates | Check point/box bounds |
| `checkpoint not found` | Wrong path | Use absolute path |
| `unexpected key` | Model/checkpoint mismatch | Match model type |
| `invalid box coordinates` | x1 > x2 or y1 > y2 | Fix box format |

## Getting Help

1. **GitHub Issues**: https://github.com/facebookresearch/segment-anything/issues
2. **HuggingFace Forums**: https://discuss.huggingface.co
3. **Paper**: https://arxiv.org/abs/2304.02643

### Reporting Issues

Include:
- Python version
- PyTorch version: `python -c "import torch; print(torch.__version__)"`
- CUDA version: `python -c "import torch; print(torch.version.cuda)"`
- SAM model type (vit_b/l/h)
- Full error traceback
- Minimal reproducible code




---

## 🚀 Usage

**Reference this template:** `@skill-segment-anything-model.md`


**Platform-specific:**
- **GitHub Copilot**: Add to `.github/copilot-instructions.md`
- **Augment Code**: Use `aug context add` command
- **Cursor/Windsurf**: Reference in chat with `@filename`
- **Claude**: Add to Project Knowledge
- **ChatGPT**: Add to Custom GPT configuration
