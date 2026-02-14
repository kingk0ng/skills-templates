---
id: skill-tensorboard
type: skill
name: tensorboard
description: Visualize training metrics, debug models with histograms, compare experiments,
  visualize model graphs, and profile performance with TensorBoard - Google's ML visualization
  toolkit
category: ai-research
complexity: medium
keywords:
- github
- performance
- python
- test
capabilities: []
token_estimate: 1960
has_references: true
reference_count: 3
---

<!-- Converted from Claude Skill Template -->
<!-- Complexity: Medium -->
<!-- Estimated Tokens: ~1,960 -->
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




# tensorboard

> Visualize training metrics, debug models with histograms, compare experiments, visualize model graphs, and profile performance with TensorBoard - Google's ML visualization toolkit

# TensorBoard: Visualization Toolkit for ML

## When to Use This Skill

Use TensorBoard when you need to:
- **Visualize training metrics** like loss and accuracy over time
- **Debug models** with histograms and distributions
- **Compare experiments** across multiple runs
- **Visualize model graphs** and architecture
- **Project embeddings** to lower dimensions (t-SNE, PCA)
- **Track hyperparameter** experiments
- **Profile performance** and identify bottlenecks
- **Visualize images and text** during training

**Users**: 20M+ downloads/year | **GitHub Stars**: 27k+ | **License**: Apache 2.0

## Installation

```bash
# Install TensorBoard
pip install tensorboard

# PyTorch integration
pip install torch torchvision tensorboard

# TensorFlow integration (TensorBoard included)
pip install tensorflow

# Launch TensorBoard
tensorboard --logdir=runs
# Access at http://localhost:6006
```

## Quick Start

### PyTorch

```python
from torch.utils.tensorboard import SummaryWriter

# Create writer
writer = SummaryWriter('runs/experiment_1')

# Training loop
for epoch in range(10):
    train_loss = train_epoch()
    val_acc = validate()

    # Log metrics
    writer.add_scalar('Loss/train', train_loss, epoch)
    writer.add_scalar('Accuracy/val', val_acc, epoch)

# Close writer
writer.close()

# Launch: tensorboard --logdir=runs
```

### TensorFlow/Keras

```python
import tensorflow as tf

# Create callback
tensorboard_callback = tf.keras.callbacks.TensorBoard(
    log_dir='logs/fit',
    histogram_freq=1
)

# Train model
model.fit(
    x_train, y_train,
    epochs=10,
    validation_data=(x_val, y_val),
    callbacks=[tensorboard_callback]
)

# Launch: tensorboard --logdir=logs
```

## Core Concepts

### 1. SummaryWriter (PyTorch)

```python
from torch.utils.tensorboard import SummaryWriter

# Default directory: runs/CURRENT_DATETIME
writer = SummaryWriter()

# Custom directory
writer = SummaryWriter('runs/experiment_1')

# Custom comment (appended to default directory)
writer = SummaryWriter(comment='baseline')

# Log data
writer.add_scalar('Loss/train', 0.5, step=0)
writer.add_scalar('Loss/train', 0.3, step=1)

# Flush and close
writer.flush()
writer.close()
```

### 2. Logging Scalars

```python
# PyTorch
from torch.utils.tensorboard import SummaryWriter
writer = SummaryWriter()

for epoch in range(100):
    train_loss = train()
    val_loss = validate()

    # Log individual metrics
    writer.add_scalar('Loss/train', train_loss, epoch)
    writer.add_scalar('Loss/val', val_loss, epoch)
    writer.add_scalar('Accuracy/train', train_acc, epoch)
    writer.add_scalar('Accuracy/val', val_acc, epoch)

    # Learning rate
    lr = optimizer.param_groups[0]['lr']
    writer.add_scalar('Learning_rate', lr, epoch)

writer.close()
```

```python
# TensorFlow
import tensorflow as tf

train_summary_writer = tf.summary.create_file_writer('logs/train')
val_summary_writer = tf.summary.create_file_writer('logs/val')

for epoch in range(100):
    with train_summary_writer.as_default():
        tf.summary.scalar('loss', train_loss, step=epoch)
        tf.summary.scalar('accuracy', train_acc, step=epoch)

    with val_summary_writer.as_default():
        tf.summary.scalar('loss', val_loss, step=epoch)
        tf.summary.scalar('accuracy', val_acc, step=epoch)
```

### 3. Logging Multiple Scalars

```python
# PyTorch: Group related metrics
writer.add_scalars('Loss', {
    'train': train_loss,
    'validation': val_loss,
    'test': test_loss
}, epoch)

writer.add_scalars('Metrics', {
    'accuracy': accuracy,
    'precision': precision,
    'recall': recall,
    'f1': f1_score
}, epoch)
```

### 4. Logging Images

```python
# PyTorch
import torch
from torchvision.utils import make_grid

# Single image
writer.add_image('Input/sample', img_tensor, epoch)

# Multiple images as grid
img_grid = make_grid(images[:64], nrow=8)
writer.add_image('Batch/inputs', img_grid, epoch)

# Predictions visualization
pred_grid = make_grid(predictions[:16], nrow=4)
writer.add_image('Predictions', pred_grid, epoch)
```

```python
# TensorFlow
import tensorflow as tf

with file_writer.as_default():
    # Encode images as PNG
    tf.summary.image('Training samples', images, step=epoch, max_outputs=25)
```

### 5. Logging Histograms

```python
# PyTorch: Track weight distributions
for name, param in model.named_parameters():
    writer.add_histogram(name, param, epoch)

    # Track gradients
    if param.grad is not None:
        writer.add_histogram(f'{name}.grad', param.grad, epoch)

# Track activations
writer.add_histogram('Activations/relu1', activations, epoch)
```

```python
# TensorFlow
with file_writer.as_default():
    tf.summary.histogram('weights/layer1', layer1.kernel, step=epoch)
    tf.summary.histogram('activations/relu1', activations, step=epoch)
```

### 6. Logging Model Graph

```python
# PyTorch
import torch

model = MyModel()
dummy_input = torch.randn(1, 3, 224, 224)

writer.add_graph(model, dummy_input)
writer.close()
```

```python
# TensorFlow (automatic with Keras)
tensorboard_callback = tf.keras.callbacks.TensorBoard(
    log_dir='logs',
    write_graph=True
)

model.fit(x, y, callbacks=[tensorboard_callback])
```

## Advanced Features

### Embedding Projector

Visualize high-dimensional data (embeddings, features) in 2D/3D.

```python
import torch
from torch.utils.tensorboard import SummaryWriter

# Get embeddings (e.g., word embeddings, image features)
embeddings = model.get_embeddings(data)  # Shape: (N, embedding_dim)

# Metadata (labels for each point)
metadata = ['class_1', 'class_2', 'class_1', ...]

# Images (optional, for image embeddings)
label_images = torch.stack([img1, img2, img3, ...])

# Log to TensorBoard
writer.add_embedding(
    embeddings,
    metadata=metadata,
    label_img=label_images,
    global_step=epoch
)
```

**In TensorBoard:**
- Navigate to "Projector" tab
- Choose PCA, t-SNE, or UMAP visualization
- Search, filter, and explore clusters

### Hyperparameter Tuning

```python
from torch.utils.tensorboard import SummaryWriter

# Try different hyperparameters
for lr in [0.001, 0.01, 0.1]:
    for batch_size in [16, 32, 64]:
        # Create unique run directory
        writer = SummaryWriter(f'runs/lr{lr}_bs{batch_size}')

        # Log hyperparameters
        writer.add_hparams(
            {'lr': lr, 'batch_size': batch_size},
            {'hparam/accuracy': final_acc, 'hparam/loss': final_loss}
        )

        # Train and log
        for epoch in range(10):
            loss = train(lr, batch_size)
            writer.add_scalar('Loss/train', loss, epoch)

        writer.close()

# Compare in TensorBoard's "HParams" tab
```

### Text Logging

```python
# PyTorch: Log text (e.g., model predictions, summaries)
writer.add_text('Predictions', f'Epoch {epoch}: {predictions}', epoch)
writer.add_text('Config', str(config), 0)

# Log markdown tables
markdown_table = """
| Metric | Value |
|--------|-------|
| Accuracy | 0.95 |
| F1 Score | 0.93 |
"""
writer.add_text('Results', markdown_table, epoch)
```

### PR Curves

Precision-Recall curves for classification.

```python
from torch.utils.tensorboard import SummaryWriter

# Get predictions and labels
predictions = model(test_data)  # Shape: (N, num_classes)
labels = test_labels  # Shape: (N,)

# Log PR curve for each class
for i in range(num_classes):
    writer.add_pr_curve(
        f'PR_curve/class_{i}',
        labels == i,
        predictions[:, i],
        global_step=epoch
    )
```

## Integration Examples

### PyTorch Training Loop

```python
import torch
import torch.nn as nn
from torch.utils.tensorboard import SummaryWriter

# Setup
writer = SummaryWriter('runs/resnet_experiment')
model = ResNet50()
optimizer = torch.optim.Adam(model.parameters(), lr=0.001)
criterion = nn.CrossEntropyLoss()

# Log model graph
dummy_input = torch.randn(1, 3, 224, 224)
writer.add_graph(model, dummy_input)

# Training loop
for epoch in range(50):
    model.train()
    train_loss = 0.0
    train_correct = 0

    for batch_idx, (data, target) in enumerate(train_loader):
        optimizer.zero_grad()
        output = model(data)
        loss = criterion(output, target)
        loss.backward()
        optimizer.step()

        train_loss += loss.item()
        pred = output.argmax(dim=1)
        train_correct += pred.eq(target).sum().item()

        # Log batch metrics (every 100 batches)
        if batch_idx % 100 == 0:
            global_step = epoch * len(train_loader) + batch_idx
            writer.add_scalar('Loss/train_batch', loss.item(), global_step)

    # Epoch metrics
    train_loss /= len(train_loader)
    train_acc = train_correct / len(train_loader.dataset)

    # Validation
    model.eval()
    val_loss = 0.0
    val_correct = 0

    with torch.no_grad():
        for data, target in val_loader:
            output = model(data)
            val_loss += criterion(output, target).item()
            pred = output.argmax(dim=1)
            val_correct += pred.eq(target).sum().item()

    val_loss /= len(val_loader)
    val_acc = val_correct / len(val_loader.dataset)

    # Log epoch metrics
    writer.add_scalars('Loss', {'train': train_loss, 'val': val_loss}, epoch)
    writer.add_scalars('Accuracy', {'train': train_acc, 'val': val_acc}, epoch)

    # Log learning rate
    writer.add_scalar('Learning_rate', optimizer.param_groups[0]['lr'], epoch)

    # Log histograms (every 5 epochs)
    if epoch % 5 == 0:
        for name, param in model.named_parameters():
            writer.add_histogram(name, param, epoch)

    # Log sample predictions
    if epoch % 10 == 0:
        sample_images = data[:8]
        writer.add_image('Sample_inputs', make_grid(sample_images), epoch)

writer.close()
```

### TensorFlow/Keras Training

```python
import tensorflow as tf

# Define model
model = tf.keras.models.Sequential([
    tf.keras.layers.Conv2D(32, 3, activation='relu', input_shape=(28, 28, 1)),
    tf.keras.layers.MaxPooling2D(),
    tf.keras.layers.Flatten(),
    tf.keras.layers.Dense(128, activation='relu'),
    tf.keras.layers.Dense(10, activation='softmax')
])

model.compile(
    optimizer='adam',
    loss='sparse_categorical_crossentropy',
    metrics=['accuracy']
)

# TensorBoard callback
tensorboard_callback = tf.keras.callbacks.TensorBoard(
    log_dir='logs/fit',
    histogram_freq=1,          # Log histograms every epoch
    write_graph=True,          # Visualize model graph
    write_images=True,         # Visualize weights as images
    update_freq='epoch',       # Log metrics every epoch
    profile_batch='500,520',   # Profile batches 500-520
    embeddings_freq=1          # Log embeddings every epoch
)

# Train
model.fit(
    x_train, y_train,
    epochs=10,
    validation_data=(x_val, y_val),
    callbacks=[tensorboard_callback]
)
```

## Comparing Experiments

### Multiple Runs

```bash
# Run experiments with different configs
python train.py --lr 0.001 --logdir runs/exp1
python train.py --lr 0.01 --logdir runs/exp2
python train.py --lr 0.1 --logdir runs/exp3

# View all runs together
tensorboard --logdir=runs
```

**In TensorBoard:**
- All runs appear in the same dashboard
- Toggle runs on/off for comparison
- Use regex to filter run names
- Overlay charts to compare metrics

### Organizing Experiments

```python
# Hierarchical organization
runs/
├── baseline/
│   ├── run_1/
│   └── run_2/
├── improved/
│   ├── run_1/
│   └── run_2/
└── final/
    └── run_1/

# Log with hierarchy
writer = SummaryWriter('runs/baseline/run_1')
```

## Best Practices

### 1. Use Descriptive Run Names

```python
# ✅ Good: Descriptive names
from datetime import datetime
timestamp = datetime.now().strftime('%Y%m%d_%H%M%S')
writer = SummaryWriter(f'runs/resnet50_lr0.001_bs32_{timestamp}')

# ❌ Bad: Auto-generated names
writer = SummaryWriter()  # Creates runs/Jan01_12-34-56_hostname
```

### 2. Group Related Metrics

```python
# ✅ Good: Grouped metrics
writer.add_scalar('Loss/train', train_loss, step)
writer.add_scalar('Loss/val', val_loss, step)
writer.add_scalar('Accuracy/train', train_acc, step)
writer.add_scalar('Accuracy/val', val_acc, step)

# ❌ Bad: Flat namespace
writer.add_scalar('train_loss', train_loss, step)
writer.add_scalar('val_loss', val_loss, step)
```

### 3. Log Regularly but Not Too Often

```python
# ✅ Good: Log epoch metrics always, batch metrics occasionally
for epoch in range(100):
    for batch_idx, (data, target) in enumerate(train_loader):
        loss = train_step(data, target)

        # Log every 100 batches
        if batch_idx % 100 == 0:
            writer.add_scalar('Loss/batch', loss, global_step)

    # Always log epoch metrics
    writer.add_scalar('Loss/epoch', epoch_loss, epoch)

# ❌ Bad: Log every batch (creates huge log files)
for batch in train_loader:
    writer.add_scalar('Loss', loss, step)  # Too frequent
```

### 4. Close Writer When Done

```python
# ✅ Good: Use context manager
with SummaryWriter('runs/exp1') as writer:
    for epoch in range(10):
        writer.add_scalar('Loss', loss, epoch)
# Automatically closes

# Or manually
writer = SummaryWriter('runs/exp1')
# ... logging ...
writer.close()
```

### 5. Use Separate Writers for Train/Val

```python
# ✅ Good: Separate log directories
train_writer = SummaryWriter('runs/exp1/train')
val_writer = SummaryWriter('runs/exp1/val')

train_writer.add_scalar('loss', train_loss, epoch)
val_writer.add_scalar('loss', val_loss, epoch)
```

## Performance Profiling

### TensorFlow Profiler

```python
# Enable profiling
tensorboard_callback = tf.keras.callbacks.TensorBoard(
    log_dir='logs',
    profile_batch='10,20'  # Profile batches 10-20
)

model.fit(x, y, callbacks=[tensorboard_callback])

# View in TensorBoard Profile tab
# Shows: GPU utilization, kernel stats, memory usage, bottlenecks
```

### PyTorch Profiler

```python
import torch.profiler as profiler

with profiler.profile(
    activities=[
        profiler.ProfilerActivity.CPU,
        profiler.ProfilerActivity.CUDA
    ],
    on_trace_ready=torch.profiler.tensorboard_trace_handler('./runs/profiler'),
    record_shapes=True,
    with_stack=True
) as prof:
    for batch in train_loader:
        loss = train_step(batch)
        prof.step()

# View in TensorBoard Profile tab
```

## Resources

- **Documentation**: https://www.tensorflow.org/tensorboard
- **PyTorch Integration**: https://pytorch.org/docs/stable/tensorboard.html
- **GitHub**: https://github.com/tensorflow/tensorboard (27k+ stars)
- **TensorBoard.dev**: https://tensorboard.dev (share experiments publicly)

## See Also

- `references/visualization.md` - Comprehensive visualization guide
- `references/profiling.md` - Performance profiling patterns
- `references/integrations.md` - Framework-specific integration examples




---


## 📚 Reference Materials


### Visualization

# Comprehensive Visualization Guide

Complete guide to visualizing ML experiments with TensorBoard.

## Table of Contents
- Scalars
- Images
- Histograms & Distributions
- Graphs
- Embeddings
- Text
- PR Curves
- Custom Visualizations

## Scalars

### Basic Scalar Logging

```python
from torch.utils.tensorboard import SummaryWriter

writer = SummaryWriter('runs/scalars_demo')

# Log single metric
for step in range(100):
    loss = compute_loss()
    writer.add_scalar('Loss', loss, step)

writer.close()
```

### Multiple Scalars

```python
# Group related metrics
writer.add_scalars('Loss', {
    'train': train_loss,
    'validation': val_loss,
    'test': test_loss
}, epoch)

writer.add_scalars('Metrics/Classification', {
    'accuracy': accuracy,
    'precision': precision,
    'recall': recall,
    'f1_score': f1
}, epoch)
```

### Time-Series Metrics

```python
# Track metrics over training
for epoch in range(100):
    # Training metrics
    train_loss = 0.0
    for batch in train_loader:
        loss = train_batch(batch)
        train_loss += loss

    train_loss /= len(train_loader)

    # Validation metrics
    val_loss, val_acc = validate()

    # Log
    writer.add_scalar('Loss/train', train_loss, epoch)
    writer.add_scalar('Loss/val', val_loss, epoch)
    writer.add_scalar('Accuracy/val', val_acc, epoch)

    # Log learning rate
    current_lr = optimizer.param_groups[0]['lr']
    writer.add_scalar('Learning_rate', current_lr, epoch)
```

### Custom Smoothing

TensorBoard UI allows smoothing scalars:
- Slider from 0 (no smoothing) to 1 (maximum smoothing)
- Exponential moving average
- Useful for noisy metrics

## Images

### Single Image

```python
import torch
from torch.utils.tensorboard import SummaryWriter

writer = SummaryWriter('runs/images_demo')

# Log single image (C, H, W)
img = torch.rand(3, 224, 224)
writer.add_image('Sample_image', img, 0)
```

### Image Grid

```python
from torchvision.utils import make_grid

# Create grid from batch
images = torch.rand(64, 3, 224, 224)  # Batch of 64 images
img_grid = make_grid(images, nrow=8)  # 8 images per row

writer.add_image('Image_grid', img_grid, epoch)
```

### Training Visualizations

```python
# Visualize inputs, predictions, and ground truth
for epoch in range(10):
    # Get batch
    images, labels = next(iter(val_loader))

    # Predict
    with torch.no_grad():
        predictions = model(images)

    # Visualize inputs
    input_grid = make_grid(images[:16], nrow=4)
    writer.add_image('Inputs', input_grid, epoch)

    # Visualize predictions (if images)
    if isinstance(predictions, torch.Tensor) and predictions.dim() == 4:
        pred_grid = make_grid(predictions[:16], nrow=4)
        writer.add_image('Predictions', pred_grid, epoch)
```

### Attention Maps

```python
# Visualize attention weights
attention_maps = model.get_attention(images)  # (B, H, W)

# Normalize to [0, 1]
attention_maps = (attention_maps - attention_maps.min()) / (attention_maps.max() - attention_maps.min())

# Add channel dimension
attention_maps = attention_maps.unsqueeze(1)  # (B, 1, H, W)

# Create grid
attention_grid = make_grid(attention_maps[:16], nrow=4)
writer.add_image('Attention_maps', attention_grid, epoch)
```

### TensorFlow Images

```python
import tensorflow as tf

file_writer = tf.summary.create_file_writer('logs/images')

with file_writer.as_default():
    # Log image batch
    tf.summary.image('Training_samples', images, step=epoch, max_outputs=25)

    # Log single image
    tf.summary.image('Sample', img[tf.newaxis, ...], step=epoch)
```

## Histograms & Distributions

### Weight Histograms

```python
# PyTorch: Track weight distributions over time
for epoch in range(100):
    train_epoch()

    # Log all model parameters
    for name, param in model.named_parameters():
        writer.add_histogram(f'Weights/{name}', param, epoch)

    # Log gradients
    for name, param in model.named_parameters():
        if param.grad is not None:
            writer.add_histogram(f'Gradients/{name}', param.grad, epoch)
```

### Activation Histograms

```python
# Hook to capture activations
activations = {}

def get_activation(name):
    def hook(model, input, output):
        activations[name] = output.detach()
    return hook

# Register hooks
model.conv1.register_forward_hook(get_activation('conv1'))
model.conv2.register_forward_hook(get_activation('conv2'))
model.fc.register_forward_hook(get_activation('fc'))

# Forward pass
output = model(input)

# Log activations
for name, activation in activations.items():
    writer.add_histogram(f'Activations/{name}', activation, epoch)
```

### Custom Distributions

```python
# Log prediction distributions
predictions = model(test_data)
writer.add_histogram('Predictions', predictions, epoch)

# Log loss distributions across batches
losses = []
for batch in val_loader:
    loss = compute_loss(batch)
    losses.append(loss)

losses = torch.tensor(losses)
writer.add_histogram('Loss_distribution', losses, epoch)
```

### TensorFlow Histograms

```python
import tensorflow as tf

file_writer = tf.summary.create_file_writer('logs/histograms')

with file_writer.as_default():
    # Log weight distributions
    for layer in model.layers:
        for weight in layer.weights:
            tf.summary.histogram(weight.name, weight, step=epoch)
```

## Graphs

### Model Architecture

```python
import torch
from torch.utils.tensorboard import SummaryWriter

# PyTorch model
model = ResNet50(num_classes=1000)

# Create dummy input (same shape as real input)
dummy_input = torch.randn(1, 3, 224, 224)

# Log graph
writer = SummaryWriter('runs/graph_demo')
writer.add_graph(model, dummy_input)
writer.close()

# View in TensorBoard "Graphs" tab
```

### TensorFlow Graph

```python
# TensorFlow automatically logs graph with Keras
tensorboard_callback = tf.keras.callbacks.TensorBoard(
    log_dir='logs',
    write_graph=True  # Enable graph logging
)

model.fit(x, y, callbacks=[tensorboard_callback])
```

## Embeddings

### Projecting Embeddings

```python
import torch
from torch.utils.tensorboard import SummaryWriter

writer = SummaryWriter('runs/embeddings_demo')

# Get embeddings (e.g., word embeddings, image features)
# Shape: (num_samples, embedding_dim)
embeddings = model.get_embeddings(data)

# Metadata (labels for each embedding)
metadata = ['cat', 'dog', 'bird', 'cat', 'dog', ...]

# Optional: Images for each embedding
label_img = torch.stack([img1, img2, img3, ...])  # (num_samples, C, H, W)

# Log embeddings
writer.add_embedding(
    embeddings,
    metadata=metadata,
    label_img=label_img,
    global_step=epoch,
    tag='Word_embeddings'
)

writer.close()
```

**In TensorBoard Projector:**
- Choose PCA, t-SNE, or UMAP
- Color by metadata labels
- Search and filter points
- Explore nearest neighbors

### Image Embeddings

```python
# Extract features from CNN
features = []
labels = []
images = []

model.eval()
with torch.no_grad():
    for data, target in test_loader:
        # Get features from penultimate layer
        feature = model.get_features(data)  # (B, feature_dim)
        features.append(feature)
        labels.extend(target.cpu().numpy())
        images.append(data)

# Concatenate
features = torch.cat(features)
images = torch.cat(images)

# Metadata (class names)
class_names = ['airplane', 'car', 'bird', 'cat', 'deer', 'dog', 'frog', 'horse', 'ship', 'truck']
metadata = [class_names[label] for label in labels]

# Log to TensorBoard
writer.add_embedding(
    features,
    metadata=metadata,
    label_img=images,
    tag='CIFAR10_features'
)
```

### Text Embeddings

```python
# Word2Vec or BERT embeddings
word_embeddings = model.word_embeddings.weight.data  # (vocab_size, embedding_dim)
vocabulary = ['the', 'cat', 'dog', 'run', 'jump', ...]

writer.add_embedding(
    word_embeddings,
    metadata=vocabulary,
    tag='Word2Vec_embeddings'
)
```

## Text

### Basic Text Logging

```python
from torch.utils.tensorboard import SummaryWriter

writer = SummaryWriter('runs/text_demo')

# Log plain text
writer.add_text('Config', str(config), 0)
writer.add_text('Hyperparameters', f'lr={lr}, batch_size={batch_size}', 0)

# Log predictions
predictions_text = f"Epoch {epoch}:\n"
for i, pred in enumerate(predictions[:5]):
    predictions_text += f"Sample {i}: {pred}\n"

writer.add_text('Predictions', predictions_text, epoch)
```

### Markdown Tables

```python
# Log results as markdown table
results = f"""
| Metric | Train | Validation | Test |
|--------|-------|------------|------|
| Accuracy | {train_acc:.4f} | {val_acc:.4f} | {test_acc:.4f} |
| Loss | {train_loss:.4f} | {val_loss:.4f} | {test_loss:.4f} |
| F1 Score | {train_f1:.4f} | {val_f1:.4f} | {test_f1:.4f} |
"""

writer.add_text('Results/Summary', results, epoch)
```

### Model Summaries

```python
# Log model architecture as text
from torchinfo import summary

model_summary = str(summary(model, input_size=(1, 3, 224, 224), verbose=0))
writer.add_text('Model/Architecture', f'```\n{model_summary}\n```', 0)
```

## PR Curves

### Precision-Recall Curves

```python
from torch.utils.tensorboard import SummaryWriter
from sklearn.metrics import precision_recall_curve

writer = SummaryWriter('runs/pr_curves')

# Get predictions and ground truth
y_true = []
y_scores = []

model.eval()
with torch.no_grad():
    for data, target in test_loader:
        output = model(data)
        probs = torch.softmax(output, dim=1)

        y_true.extend(target.cpu().numpy())
        y_scores.extend(probs.cpu().numpy())

y_true = np.array(y_true)
y_scores = np.array(y_scores)

# Log PR curve for each class
num_classes = y_scores.shape[1]
for class_idx in range(num_classes):
    # Binary classification: class vs rest
    labels = (y_true == class_idx).astype(int)
    scores = y_scores[:, class_idx]

    # Add PR curve
    writer.add_pr_curve(
        f'PR_curve/class_{class_idx}',
        labels,
        scores,
        global_step=epoch
    )

writer.close()
```

### ROC Curves

```python
# TensorBoard doesn't have built-in ROC, but we can log as image
from sklearn.metrics import roc_curve, auc
import matplotlib.pyplot as plt

fig, ax = plt.subplots()

for class_idx in range(num_classes):
    labels = (y_true == class_idx).astype(int)
    scores = y_scores[:, class_idx]

    fpr, tpr, _ = roc_curve(labels, scores)
    roc_auc = auc(fpr, tpr)

    ax.plot(fpr, tpr, label=f'Class {class_idx} (AUC = {roc_auc:.2f})')

ax.plot([0, 1], [0, 1], 'k--')
ax.set_xlabel('False Positive Rate')
ax.set_ylabel('True Positive Rate')
ax.set_title('ROC Curves')
ax.legend()

# Convert to tensor and log
fig.canvas.draw()
img = np.frombuffer(fig.canvas.tostring_rgb(), dtype=np.uint8)
img = img.reshape(fig.canvas.get_width_height()[::-1] + (3,))
img = torch.from_numpy(img).permute(2, 0, 1)

writer.add_image('ROC_curves', img, epoch)
plt.close(fig)
```

## Custom Visualizations

### Confusion Matrix

```python
import matplotlib.pyplot as plt
import seaborn as sns
from sklearn.metrics import confusion_matrix

# Compute confusion matrix
cm = confusion_matrix(y_true, y_pred)

# Plot
fig, ax = plt.subplots(figsize=(10, 10))
sns.heatmap(cm, annot=True, fmt='d', cmap='Blues', ax=ax)
ax.set_xlabel('Predicted')
ax.set_ylabel('True')
ax.set_title('Confusion Matrix')

# Convert to tensor and log
fig.canvas.draw()
img = np.frombuffer(fig.canvas.tostring_rgb(), dtype=np.uint8)
img = img.reshape(fig.canvas.get_width_height()[::-1] + (3,))
img = torch.from_numpy(img).permute(2, 0, 1)

writer.add_image('Confusion_matrix', img, epoch)
plt.close(fig)
```

### Loss Landscape

```python
# Visualize loss surface around current parameters
import numpy as np

def compute_loss_landscape(model, data, target, param1, param2):
    """Compute loss for a grid of parameter values."""
    # Save original params
    original_params = {name: param.clone() for name, param in model.named_parameters()}

    # Grid
    param1_range = np.linspace(-1, 1, 50)
    param2_range = np.linspace(-1, 1, 50)
    losses = np.zeros((50, 50))

    for i, p1 in enumerate(param1_range):
        for j, p2 in enumerate(param2_range):
            # Perturb parameters
            model.state_dict()[param1].add_(p1)
            model.state_dict()[param2].add_(p2)

            # Compute loss
            with torch.no_grad():
                output = model(data)
                loss = F.cross_entropy(output, target)
                losses[i, j] = loss.item()

            # Restore parameters
            model.load_state_dict(original_params)

    return losses

# Plot
fig = plt.figure()
ax = fig.add_subplot(111, projection='3d')
X, Y = np.meshgrid(np.linspace(-1, 1, 50), np.linspace(-1, 1, 50))
ax.plot_surface(X, Y, losses, cmap='viridis')
ax.set_title('Loss Landscape')

# Log
fig.canvas.draw()
img = np.frombuffer(fig.canvas.tostring_rgb(), dtype=np.uint8)
img = img.reshape(fig.canvas.get_width_height()[::-1] + (3,))
img = torch.from_numpy(img).permute(2, 0, 1)
writer.add_image('Loss_landscape', img, epoch)
plt.close(fig)
```

## Best Practices

### 1. Use Hierarchical Tags

```python
# ✅ Good: Organized with hierarchy
writer.add_scalar('Loss/train', train_loss, step)
writer.add_scalar('Loss/val', val_loss, step)
writer.add_scalar('Metrics/accuracy', accuracy, step)
writer.add_scalar('Metrics/f1_score', f1, step)

# ❌ Bad: Flat namespace
writer.add_scalar('train_loss', train_loss, step)
writer.add_scalar('val_loss', val_loss, step)
```

### 2. Log Regularly but Not Excessively

```python
# ✅ Good: Epoch-level + periodic batch-level
for epoch in range(100):
    for batch_idx, batch in enumerate(train_loader):
        loss = train_step(batch)

        # Log every 100 batches
        if batch_idx % 100 == 0:
            global_step = epoch * len(train_loader) + batch_idx
            writer.add_scalar('Loss/train_batch', loss, global_step)

    # Always log epoch metrics
    writer.add_scalar('Loss/train_epoch', epoch_loss, epoch)

# ❌ Bad: Every batch (creates huge logs)
for batch in train_loader:
    writer.add_scalar('Loss', loss, step)
```

### 3. Visualize Sample Predictions

```python
# Log predictions periodically
if epoch % 5 == 0:
    model.eval()
    with torch.no_grad():
        sample_images, sample_labels = next(iter(val_loader))
        predictions = model(sample_images)

        # Visualize
        img_grid = make_grid(sample_images[:16], nrow=4)
        writer.add_image('Samples/inputs', img_grid, epoch)

        # Add predictions as text
        pred_text = '\n'.join([f'{i}: {pred.argmax()}' for i, pred in enumerate(predictions[:16])])
        writer.add_text('Samples/predictions', pred_text, epoch)
```

## Resources

- **TensorBoard Documentation**: https://www.tensorflow.org/tensorboard
- **PyTorch TensorBoard**: https://pytorch.org/docs/stable/tensorboard.html
- **Projector Guide**: https://www.tensorflow.org/tensorboard/tensorboard_projector_plugin




### Integrations

# Framework Integration Guide

Complete guide to integrating TensorBoard with popular ML frameworks.

## Table of Contents
- PyTorch
- TensorFlow/Keras
- PyTorch Lightning
- HuggingFace Transformers
- Fast.ai
- JAX
- scikit-learn

## PyTorch

### Basic Integration

```python
import torch
import torch.nn as nn
from torch.utils.tensorboard import SummaryWriter

# Create writer
writer = SummaryWriter('runs/pytorch_experiment')

# Model and optimizer
model = ResNet50()
optimizer = torch.optim.Adam(model.parameters(), lr=0.001)
criterion = nn.CrossEntropyLoss()

# Log model graph
dummy_input = torch.randn(1, 3, 224, 224)
writer.add_graph(model, dummy_input)

# Training loop
for epoch in range(100):
    model.train()
    train_loss = 0.0

    for batch_idx, (data, target) in enumerate(train_loader):
        optimizer.zero_grad()
        output = model(data)
        loss = criterion(output, target)
        loss.backward()
        optimizer.step()

        train_loss += loss.item()

        # Log batch metrics
        if batch_idx % 100 == 0:
            global_step = epoch * len(train_loader) + batch_idx
            writer.add_scalar('Loss/train_batch', loss.item(), global_step)

    # Epoch metrics
    train_loss /= len(train_loader)
    writer.add_scalar('Loss/train_epoch', train_loss, epoch)

    # Log histograms
    for name, param in model.named_parameters():
        writer.add_histogram(name, param, epoch)

writer.close()
```

### torchvision Integration

```python
from torchvision.utils import make_grid

# Log image batch
for batch_idx, (images, labels) in enumerate(train_loader):
    if batch_idx == 0:  # First batch
        img_grid = make_grid(images[:64], nrow=8)
        writer.add_image('Training_batch', img_grid, epoch)
        break
```

### Distributed Training

```python
import torch.distributed as dist
from torch.nn.parallel import DistributedDataParallel as DDP

# Setup
dist.init_process_group(backend='nccl')
rank = dist.get_rank()

# Only log from rank 0
if rank == 0:
    writer = SummaryWriter('runs/distributed_experiment')

model = DDP(model, device_ids=[rank])

for epoch in range(100):
    train_loss = train_epoch()

    # Log only from rank 0
    if rank == 0:
        writer.add_scalar('Loss/train', train_loss, epoch)
```

## TensorFlow/Keras

### Keras Callback

```python
import tensorflow as tf

# TensorBoard callback
tensorboard_callback = tf.keras.callbacks.TensorBoard(
    log_dir='logs/keras_experiment',
    histogram_freq=1,          # Log histograms every epoch
    write_graph=True,          # Visualize model graph
    write_images=True,         # Visualize layer weights as images
    update_freq='epoch',       # Log metrics per epoch (or 'batch', or integer)
    profile_batch='10,20',     # Profile batches 10-20
    embeddings_freq=1          # Log embeddings every epoch
)

# Compile model
model.compile(
    optimizer='adam',
    loss='sparse_categorical_crossentropy',
    metrics=['accuracy']
)

# Train with callback
history = model.fit(
    x_train, y_train,
    epochs=10,
    validation_data=(x_val, y_val),
    callbacks=[tensorboard_callback]
)
```

### Custom Training Loop

```python
import tensorflow as tf

# Create summary writers
train_summary_writer = tf.summary.create_file_writer('logs/train')
val_summary_writer = tf.summary.create_file_writer('logs/val')

# Training loop
for epoch in range(100):
    # Training
    for step, (x_batch, y_batch) in enumerate(train_dataset):
        with tf.GradientTape() as tape:
            predictions = model(x_batch, training=True)
            loss = loss_fn(y_batch, predictions)

        gradients = tape.gradient(loss, model.trainable_variables)
        optimizer.apply_gradients(zip(gradients, model.trainable_variables))

        # Log training metrics
        with train_summary_writer.as_default():
            tf.summary.scalar('loss', loss, step=epoch * len(train_dataset) + step)

    # Validation
    for x_batch, y_batch in val_dataset:
        predictions = model(x_batch, training=False)
        val_loss = loss_fn(y_batch, predictions)
        val_acc = accuracy_fn(y_batch, predictions)

    # Log validation metrics
    with val_summary_writer.as_default():
        tf.summary.scalar('loss', val_loss, step=epoch)
        tf.summary.scalar('accuracy', val_acc, step=epoch)

    # Log histograms
    with train_summary_writer.as_default():
        for layer in model.layers:
            for weight in layer.weights:
                tf.summary.histogram(weight.name, weight, step=epoch)
```

### tf.data Integration

```python
# Log dataset samples
for images, labels in train_dataset.take(1):
    with file_writer.as_default():
        tf.summary.image('Training samples', images, step=0, max_outputs=25)
```

## PyTorch Lightning

### Built-in Logger

```python
import pytorch_lightning as pl
from pytorch_lightning.loggers import TensorBoardLogger

# Create logger
logger = TensorBoardLogger('logs', name='lightning_experiment')

# Lightning module
class LitModel(pl.LightningModule):
    def __init__(self):
        super().__init__()
        self.model = ResNet50()

    def training_step(self, batch, batch_idx):
        x, y = batch
        y_hat = self.model(x)
        loss = F.cross_entropy(y_hat, y)

        # Log metrics
        self.log('train_loss', loss, on_step=True, on_epoch=True)

        return loss

    def validation_step(self, batch, batch_idx):
        x, y = batch
        y_hat = self.model(x)
        loss = F.cross_entropy(y_hat, y)
        acc = (y_hat.argmax(dim=1) == y).float().mean()

        # Log metrics
        self.log('val_loss', loss, on_epoch=True)
        self.log('val_acc', acc, on_epoch=True)

        return loss

    def configure_optimizers(self):
        return torch.optim.Adam(self.parameters(), lr=0.001)

# Trainer
trainer = pl.Trainer(
    max_epochs=100,
    logger=logger,
    log_every_n_steps=50
)

# Train
model = LitModel()
trainer.fit(model, train_loader, val_loader)
```

### Custom Logging

```python
class LitModel(pl.LightningModule):
    def training_step(self, batch, batch_idx):
        x, y = batch
        y_hat = self.model(x)
        loss = F.cross_entropy(y_hat, y)

        # Log scalar
        self.log('train_loss', loss)

        # Log images (every 100 batches)
        if batch_idx % 100 == 0:
            from torchvision.utils import make_grid
            img_grid = make_grid(x[:8])
            self.logger.experiment.add_image('train_images', img_grid, self.global_step)

        # Log histogram
        self.logger.experiment.add_histogram('predictions', y_hat, self.global_step)

        return loss
```

## HuggingFace Transformers

### TrainingArguments Integration

```python
from transformers import Trainer, TrainingArguments

training_args = TrainingArguments(
    output_dir='./results',
    num_train_epochs=3,
    per_device_train_batch_size=16,
    per_device_eval_batch_size=64,
    logging_dir='./logs',           # TensorBoard log directory
    logging_steps=100,              # Log every 100 steps
    evaluation_strategy='epoch',
    save_strategy='epoch',
    load_best_model_at_end=True,
    report_to='tensorboard'         # Enable TensorBoard
)

trainer = Trainer(
    model=model,
    args=training_args,
    train_dataset=train_dataset,
    eval_dataset=eval_dataset,
    tokenizer=tokenizer
)

# Train (automatically logs to TensorBoard)
trainer.train()
```

### Custom Metrics

```python
from transformers import Trainer, TrainingArguments
import numpy as np

def compute_metrics(eval_pred):
    """Custom metrics for evaluation."""
    predictions, labels = eval_pred
    predictions = np.argmax(predictions, axis=1)

    accuracy = (predictions == labels).mean()
    f1 = f1_score(labels, predictions, average='weighted')

    return {
        'accuracy': accuracy,
        'f1': f1
    }

trainer = Trainer(
    model=model,
    args=training_args,
    train_dataset=train_dataset,
    eval_dataset=eval_dataset,
    compute_metrics=compute_metrics  # Custom metrics logged to TensorBoard
)
```

### Manual Logging

```python
from transformers import TrainerCallback
from torch.utils.tensorboard import SummaryWriter

class TensorBoardCallback(TrainerCallback):
    """Custom TensorBoard logging."""

    def __init__(self, log_dir='logs'):
        self.writer = SummaryWriter(log_dir)

    def on_log(self, args, state, control, logs=None, **kwargs):
        """Called when logging."""
        if logs:
            for key, value in logs.items():
                self.writer.add_scalar(key, value, state.global_step)

    def on_train_end(self, args, state, control, **kwargs):
        """Close writer."""
        self.writer.close()

# Use callback
trainer = Trainer(
    model=model,
    args=training_args,
    train_dataset=train_dataset,
    callbacks=[TensorBoardCallback()]
)
```

## Fast.ai

### Learner Integration

```python
from fastai.vision.all import *
from fastai.callback.tensorboard import TensorBoardCallback

# Create data loaders
dls = ImageDataLoaders.from_folder(path, train='train', valid='valid')

# Create learner
learn = cnn_learner(dls, resnet50, metrics=accuracy)

# Train with TensorBoard logging
learn.fit_one_cycle(
    10,
    cbs=TensorBoardCallback('logs/fastai', trace_model=True)
)

# View logs
# tensorboard --logdir=logs/fastai
```

### Custom Callbacks

```python
from fastai.callback.core import Callback
from torch.utils.tensorboard import SummaryWriter

class CustomTensorBoardCallback(Callback):
    """Custom TensorBoard callback."""

    def __init__(self, log_dir='logs'):
        self.writer = SummaryWriter(log_dir)

    def after_batch(self):
        """Log after each batch."""
        if self.train_iter % 100 == 0:
            self.writer.add_scalar('Loss/train', self.loss, self.train_iter)

    def after_epoch(self):
        """Log after each epoch."""
        self.writer.add_scalar('Loss/train_epoch', self.recorder.train_loss, self.epoch)
        self.writer.add_scalar('Loss/val_epoch', self.recorder.valid_loss, self.epoch)

        # Log metrics
        for i, metric in enumerate(self.recorder.metrics):
            metric_name = self.recorder.metric_names[i+1]
            self.writer.add_scalar(f'Metrics/{metric_name}', metric, self.epoch)

# Use callback
learn.fit_one_cycle(10, cbs=[CustomTensorBoardCallback()])
```

## JAX

### Basic Integration

```python
import jax
import jax.numpy as jnp
from torch.utils.tensorboard import SummaryWriter

writer = SummaryWriter('logs/jax_experiment')

# Training loop
for epoch in range(100):
    for batch in train_batches:
        # JAX training step
        state, loss = train_step(state, batch)

        # Log to TensorBoard (convert JAX array to numpy)
        writer.add_scalar('Loss/train', float(loss), epoch)

    # Validation
    val_loss = evaluate(state, val_batches)
    writer.add_scalar('Loss/val', float(val_loss), epoch)

writer.close()
```

### Flax Integration

```python
from flax.training import train_state
import optax
from torch.utils.tensorboard import SummaryWriter

writer = SummaryWriter('logs/flax_experiment')

# Create train state
state = train_state.TrainState.create(
    apply_fn=model.apply,
    params=params,
    tx=optax.adam(0.001)
)

# Training loop
for epoch in range(100):
    for batch in train_loader:
        state, loss = train_step(state, batch)

        # Log metrics
        writer.add_scalar('Loss/train', loss.item(), epoch)

    # Log parameters
    for name, param in state.params.items():
        writer.add_histogram(f'Params/{name}', jnp.array(param), epoch)

writer.close()
```

## scikit-learn

### Manual Logging

```python
from sklearn.ensemble import RandomForestClassifier
from sklearn.model_selection import cross_val_score
from torch.utils.tensorboard import SummaryWriter

writer = SummaryWriter('logs/sklearn_experiment')

# Hyperparameter search
for n_estimators in [10, 50, 100, 200]:
    for max_depth in [3, 5, 10, None]:
        # Train model
        model = RandomForestClassifier(
            n_estimators=n_estimators,
            max_depth=max_depth,
            random_state=42
        )

        # Cross-validation
        scores = cross_val_score(model, X_train, y_train, cv=5)

        # Log results
        run_name = f'n{n_estimators}_d{max_depth}'
        writer.add_scalar(f'{run_name}/cv_mean', scores.mean(), 0)
        writer.add_scalar(f'{run_name}/cv_std', scores.std(), 0)

        # Log hyperparameters
        writer.add_hparams(
            {'n_estimators': n_estimators, 'max_depth': max_depth or -1},
            {'cv_accuracy': scores.mean()}
        )

writer.close()
```

### GridSearchCV Logging

```python
from sklearn.model_selection import GridSearchCV
from torch.utils.tensorboard import SummaryWriter

writer = SummaryWriter('logs/gridsearch')

# Grid search
param_grid = {
    'n_estimators': [10, 50, 100],
    'max_depth': [3, 5, 10]
}

grid_search = GridSearchCV(
    RandomForestClassifier(),
    param_grid,
    cv=5,
    return_train_score=True
)

grid_search.fit(X_train, y_train)

# Log all results
for i, params in enumerate(grid_search.cv_results_['params']):
    mean_train_score = grid_search.cv_results_['mean_train_score'][i]
    mean_test_score = grid_search.cv_results_['mean_test_score'][i]

    param_str = '_'.join([f'{k}{v}' for k, v in params.items()])

    writer.add_scalar(f'{param_str}/train', mean_train_score, 0)
    writer.add_scalar(f'{param_str}/test', mean_test_score, 0)

# Log best params
writer.add_text('Best_params', str(grid_search.best_params_), 0)
writer.add_scalar('Best_score', grid_search.best_score_, 0)

writer.close()
```

## Best Practices

### 1. Consistent Naming Conventions

```python
# ✅ Good: Hierarchical names across frameworks
writer.add_scalar('Loss/train', train_loss, step)
writer.add_scalar('Loss/val', val_loss, step)
writer.add_scalar('Metrics/accuracy', accuracy, step)

# Works the same in PyTorch, TensorFlow, Lightning
```

### 2. Use Framework-Specific Features

```python
# PyTorch: Use SummaryWriter
from torch.utils.tensorboard import SummaryWriter

# TensorFlow: Use tf.summary
import tensorflow as tf
tf.summary.scalar('loss', loss, step=step)

# Lightning: Use self.log()
self.log('train_loss', loss)

# Transformers: Use report_to='tensorboard'
training_args = TrainingArguments(report_to='tensorboard')
```

### 3. Centralize Logging Logic

```python
class MetricLogger:
    """Universal metric logger."""

    def __init__(self, log_dir='logs'):
        self.writer = SummaryWriter(log_dir)

    def log_scalar(self, name, value, step):
        self.writer.add_scalar(name, value, step)

    def log_image(self, name, image, step):
        self.writer.add_image(name, image, step)

    def log_histogram(self, name, values, step):
        self.writer.add_histogram(name, values, step)

    def close(self):
        self.writer.close()

# Use across frameworks
logger = MetricLogger('logs/universal')
logger.log_scalar('Loss/train', train_loss, epoch)
```

### 4. Framework Detection

```python
def get_tensorboard_writer(framework='auto', log_dir='logs'):
    """Get TensorBoard writer for any framework."""
    if framework == 'auto':
        # Auto-detect framework
        try:
            import torch
            framework = 'pytorch'
        except ImportError:
            try:
                import tensorflow as tf
                framework = 'tensorflow'
            except ImportError:
                raise ValueError("No supported framework found")

    if framework == 'pytorch':
        from torch.utils.tensorboard import SummaryWriter
        return SummaryWriter(log_dir)

    elif framework == 'tensorflow':
        import tensorflow as tf
        return tf.summary.create_file_writer(log_dir)

# Use it
writer = get_tensorboard_writer(log_dir='logs/auto')
```

## Resources

- **PyTorch**: https://pytorch.org/docs/stable/tensorboard.html
- **TensorFlow**: https://www.tensorflow.org/tensorboard
- **Lightning**: https://pytorch-lightning.readthedocs.io/en/stable/extensions/logging.html
- **Transformers**: https://huggingface.co/docs/transformers/main_classes/trainer
- **Fast.ai**: https://docs.fast.ai/callback.tensorboard.html




### Profiling

# Performance Profiling Guide

Complete guide to profiling and optimizing ML models with TensorBoard.

## Table of Contents
- PyTorch Profiler
- TensorFlow Profiler
- GPU Utilization
- Memory Profiling
- Bottleneck Detection
- Optimization Strategies

## PyTorch Profiler

### Basic Profiling

```python
import torch
import torch.profiler as profiler

model = MyModel().cuda()
optimizer = torch.optim.Adam(model.parameters())

# Profile training loop
with profiler.profile(
    activities=[
        profiler.ProfilerActivity.CPU,
        profiler.ProfilerActivity.CUDA,
    ],
    on_trace_ready=torch.profiler.tensorboard_trace_handler('./runs/profiler'),
    record_shapes=True,
    with_stack=True
) as prof:
    for step, (data, target) in enumerate(train_loader):
        optimizer.zero_grad()
        output = model(data.cuda())
        loss = F.cross_entropy(output, target.cuda())
        loss.backward()
        optimizer.step()

        # Mark step for profiler
        prof.step()

        if step >= 10:  # Profile first 10 steps
            break
```

### Profiler Configuration

```python
with profiler.profile(
    activities=[
        profiler.ProfilerActivity.CPU,     # Profile CPU ops
        profiler.ProfilerActivity.CUDA,    # Profile GPU ops
    ],
    schedule=profiler.schedule(
        wait=1,     # Warmup steps (skip profiling)
        warmup=1,   # Steps to warmup profiler
        active=3,   # Steps to actively profile
        repeat=2    # Repeat cycle 2 times
    ),
    on_trace_ready=torch.profiler.tensorboard_trace_handler('./runs/profiler'),
    record_shapes=True,     # Record tensor shapes
    profile_memory=True,    # Track memory allocation
    with_stack=True,        # Record source code stack traces
    with_flops=True         # Estimate FLOPS
) as prof:
    for step, batch in enumerate(train_loader):
        train_step(batch)
        prof.step()
```

### Profile Inference

```python
model.eval()

with profiler.profile(
    activities=[profiler.ProfilerActivity.CPU, profiler.ProfilerActivity.CUDA],
    on_trace_ready=torch.profiler.tensorboard_trace_handler('./runs/inference_profiler')
) as prof:
    with torch.no_grad():
        for i in range(100):
            data = torch.randn(1, 3, 224, 224).cuda()
            output = model(data)
            prof.step()
```

### Analyze Profile Data

```python
# Print profiler summary
print(prof.key_averages().table(sort_by="cuda_time_total", row_limit=10))

# Export Chrome trace (for chrome://tracing)
prof.export_chrome_trace("trace.json")

# View in TensorBoard
# tensorboard --logdir=runs/profiler
```

**TensorBoard Profile Tab shows:**
- Overview: GPU utilization, step time breakdown
- Operator view: Time spent in each operation
- Kernel view: GPU kernel execution
- Trace view: Timeline of operations
- Memory view: Memory allocation over time

## TensorFlow Profiler

### Profile with Callback

```python
import tensorflow as tf

# Create profiler callback
tensorboard_callback = tf.keras.callbacks.TensorBoard(
    log_dir='logs/profiler',
    profile_batch='10,20'  # Profile batches 10-20
)

# Train with profiling
model.fit(
    x_train, y_train,
    epochs=5,
    callbacks=[tensorboard_callback]
)

# Launch TensorBoard
# tensorboard --logdir=logs/profiler
```

### Programmatic Profiling

```python
import tensorflow as tf

# Start profiler
tf.profiler.experimental.start('logs/profiler')

# Training code
for epoch in range(5):
    for step, (x, y) in enumerate(train_dataset):
        with tf.GradientTape() as tape:
            predictions = model(x, training=True)
            loss = loss_fn(y, predictions)

        gradients = tape.gradient(loss, model.trainable_variables)
        optimizer.apply_gradients(zip(gradients, model.trainable_variables))

        # Profile specific steps
        if epoch == 2 and step == 10:
            tf.profiler.experimental.start('logs/profiler_step10')

        if epoch == 2 and step == 20:
            tf.profiler.experimental.stop()

# Stop profiler
tf.profiler.experimental.stop()
```

### Profile Custom Training Loop

```python
# Profile with context manager
with tf.profiler.experimental.Profile('logs/profiler'):
    for epoch in range(3):
        for step, (x, y) in enumerate(train_dataset):
            train_step(x, y)
```

## GPU Utilization

### Monitor GPU Usage

```python
import torch
import torch.profiler as profiler

with profiler.profile(
    activities=[profiler.ProfilerActivity.CUDA],
    on_trace_ready=torch.profiler.tensorboard_trace_handler('./runs/gpu_profile'),
    with_stack=True
) as prof:
    for step, batch in enumerate(train_loader):
        # Your training step
        output = model(batch.cuda())
        loss = criterion(output, target.cuda())
        loss.backward()
        optimizer.step()

        prof.step()

# View in TensorBoard > Profile > Overview
# Shows: GPU utilization %, kernel efficiency, memory bandwidth
```

### Optimize GPU Utilization

```python
# ✅ Good: Keep GPU busy
def train_step(batch):
    # Overlap data transfer with computation
    data = batch.cuda(non_blocking=True)  # Async transfer

    # Mixed precision for faster computation
    with torch.cuda.amp.autocast():
        output = model(data)
        loss = criterion(output, target)

    return loss

# ❌ Bad: GPU idle during data transfer
def train_step_slow(batch):
    data = batch.cuda()  # Blocking transfer
    output = model(data)
    return loss
```

### Reduce CPU-GPU Synchronization

```python
# ✅ Good: Minimize synchronization
for epoch in range(100):
    for batch in train_loader:
        loss = train_step(batch)

        # Accumulate losses (no sync)
        total_loss += loss.item()

    # Synchronize once per epoch
    avg_loss = total_loss / len(train_loader)

# ❌ Bad: Frequent synchronization
for batch in train_loader:
    loss = train_step(batch)
    print(f"Loss: {loss.item()}")  # Syncs every batch!
```

## Memory Profiling

### Track Memory Allocation

```python
import torch
import torch.profiler as profiler

with profiler.profile(
    activities=[profiler.ProfilerActivity.CUDA],
    profile_memory=True,
    record_shapes=True,
    on_trace_ready=torch.profiler.tensorboard_trace_handler('./runs/memory_profile')
) as prof:
    for step, batch in enumerate(train_loader):
        train_step(batch)
        prof.step()

# View in TensorBoard > Profile > Memory View
# Shows: Memory allocation over time, peak memory, allocation stack traces
```

### Find Memory Leaks

```python
import torch

# Record memory snapshots
torch.cuda.memory._record_memory_history(
    enabled=True,
    max_entries=100000
)

# Training
for batch in train_loader:
    train_step(batch)

# Save memory snapshot
snapshot = torch.cuda.memory._snapshot()
torch.cuda.memory._dump_snapshot("memory_snapshot.pickle")

# Analyze with:
# python -m torch.cuda.memory_viz trace_plot memory_snapshot.pickle -o memory_trace.html
```

### Optimize Memory Usage

```python
# ✅ Good: Gradient accumulation for large batches
accumulation_steps = 4

for i, batch in enumerate(train_loader):
    # Forward
    output = model(batch)
    loss = criterion(output, target) / accumulation_steps

    # Backward
    loss.backward()

    # Step optimizer every accumulation_steps
    if (i + 1) % accumulation_steps == 0:
        optimizer.step()
        optimizer.zero_grad()

# ✅ Good: Release memory explicitly
del intermediate_tensor
torch.cuda.empty_cache()

# ✅ Good: Use gradient checkpointing
from torch.utils.checkpoint import checkpoint

def custom_forward(module, input):
    return checkpoint(module, input)
```

## Bottleneck Detection

### Identify Slow Operations

```python
with profiler.profile(
    activities=[profiler.ProfilerActivity.CPU, profiler.ProfilerActivity.CUDA],
    on_trace_ready=torch.profiler.tensorboard_trace_handler('./runs/bottleneck_profile'),
    with_stack=True
) as prof:
    for step, batch in enumerate(train_loader):
        train_step(batch)
        prof.step()

# Print slowest operations
print(prof.key_averages().table(
    sort_by="cuda_time_total",
    row_limit=20
))

# Expected output:
# Name                    | CPU time | CUDA time | Calls
# aten::conv2d            | 5.2 ms   | 45.3 ms   | 32
# aten::batch_norm        | 1.1 ms   | 8.7 ms    | 32
# aten::relu              | 0.3 ms   | 2.1 ms    | 32
```

### Optimize Data Loading

```python
# ✅ Good: Efficient data loading
train_loader = torch.utils.data.DataLoader(
    dataset,
    batch_size=32,
    num_workers=4,        # Parallel data loading
    pin_memory=True,      # Faster GPU transfer
    prefetch_factor=2,    # Prefetch batches
    persistent_workers=True  # Reuse workers
)

# Profile data loading
import time

start = time.time()
for batch in train_loader:
    pass
print(f"Data loading time: {time.time() - start:.2f}s")

# ❌ Bad: Single worker, no pinning
train_loader = torch.utils.data.DataLoader(
    dataset,
    batch_size=32,
    num_workers=0  # Slow!
)
```

### Profile Specific Operations

```python
# Context manager for specific code blocks
with profiler.record_function("data_preprocessing"):
    data = preprocess(batch)

with profiler.record_function("forward_pass"):
    output = model(data)

with profiler.record_function("loss_computation"):
    loss = criterion(output, target)

# View in TensorBoard > Profile > Trace View
```

## Optimization Strategies

### Mixed Precision Training

```python
import torch
from torch.cuda.amp import autocast, GradScaler

scaler = GradScaler()

for batch in train_loader:
    optimizer.zero_grad()

    # Mixed precision forward pass
    with autocast():
        output = model(batch.cuda())
        loss = criterion(output, target.cuda())

    # Scaled backward pass
    scaler.scale(loss).backward()
    scaler.step(optimizer)
    scaler.update()

# Profile to verify speedup
with profiler.profile(
    activities=[profiler.ProfilerActivity.CUDA],
    on_trace_ready=torch.profiler.tensorboard_trace_handler('./runs/mixed_precision')
) as prof:
    train_with_mixed_precision()
    prof.step()
```

### Kernel Fusion

```python
# ✅ Good: Fused operations
# torch.nn.functional.gelu() is fused
output = F.gelu(x)

# ❌ Bad: Separate operations
# Manual GELU (slower due to multiple kernels)
output = 0.5 * x * (1 + torch.tanh(math.sqrt(2 / math.pi) * (x + 0.044715 * x**3)))

# Use torch.jit to fuse custom operations
@torch.jit.script
def fused_gelu(x):
    return 0.5 * x * (1 + torch.tanh(math.sqrt(2 / math.pi) * (x + 0.044715 * x**3)))
```

### Reduce Host-Device Transfers

```python
# ✅ Good: Keep data on GPU
data = data.cuda()  # Transfer once
for epoch in range(100):
    output = model(data)  # No transfer
    loss = criterion(output, target)

# ❌ Bad: Frequent transfers
for epoch in range(100):
    output = model(data.cuda())  # Transfer every epoch!
    loss = criterion(output.cpu(), target.cpu())  # Transfer back!
```

### Batch Size Optimization

```python
# Find optimal batch size with profiling
for batch_size in [16, 32, 64, 128, 256]:
    train_loader = DataLoader(dataset, batch_size=batch_size)

    with profiler.profile(
        activities=[profiler.ProfilerActivity.CUDA],
        profile_memory=True,
        on_trace_ready=torch.profiler.tensorboard_trace_handler(f'./runs/bs{batch_size}')
    ) as prof:
        for step, batch in enumerate(train_loader):
            train_step(batch)
            prof.step()

            if step >= 10:
                break

# Compare in TensorBoard:
# - GPU utilization
# - Memory usage
# - Throughput (samples/sec)
```

## Best Practices

### 1. Profile Representative Workloads

```python
# ✅ Good: Profile realistic training scenario
with profiler.profile(...) as prof:
    for epoch in range(3):  # Profile multiple epochs
        for step, batch in enumerate(train_loader):
            train_step(batch)
            prof.step()

# ❌ Bad: Profile single step
with profiler.profile(...) as prof:
    train_step(single_batch)
```

### 2. Profile Periodically

```python
# Profile every N epochs
if epoch % 10 == 0:
    with profiler.profile(
        activities=[profiler.ProfilerActivity.CUDA],
        on_trace_ready=torch.profiler.tensorboard_trace_handler(f'./runs/epoch{epoch}')
    ) as prof:
        train_epoch()
```

### 3. Compare Before/After Optimizations

```python
# Baseline
with profiler.profile(...) as prof:
    baseline_train()
    prof.step()

# After optimization
with profiler.profile(...) as prof:
    optimized_train()
    prof.step()

# Compare in TensorBoard
```

### 4. Profile Inference

```python
# Production inference profiling
model.eval()

with profiler.profile(
    activities=[profiler.ProfilerActivity.CUDA],
    on_trace_ready=torch.profiler.tensorboard_trace_handler('./runs/inference')
) as prof:
    with torch.no_grad():
        for i in range(1000):  # Realistic load
            data = get_production_request()
            output = model(data)
            prof.step()

# Analyze latency percentiles in TensorBoard
```

## Resources

- **PyTorch Profiler**: https://pytorch.org/tutorials/recipes/recipes/profiler_recipe.html
- **TensorFlow Profiler**: https://www.tensorflow.org/guide/profiler
- **NVIDIA Nsight**: https://developer.nvidia.com/nsight-systems
- **PyTorch Bottleneck**: https://pytorch.org/docs/stable/bottleneck.html




---

## 🚀 Usage

**Reference this template:** `@skill-tensorboard.md`


**Platform-specific:**
- **GitHub Copilot**: Add to `.github/copilot-instructions.md`
- **Augment Code**: Use `aug context add` command
- **Cursor/Windsurf**: Reference in chat with `@filename`
- **Claude**: Add to Project Knowledge
- **ChatGPT**: Add to Custom GPT configuration
