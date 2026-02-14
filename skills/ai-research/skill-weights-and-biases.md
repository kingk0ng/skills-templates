---
id: skill-weights-and-biases
type: skill
name: weights-and-biases
description: Track ML experiments with automatic logging, visualize training in real-time,
  optimize hyperparameters with sweeps, and manage model registry with W&B - collaborative
  MLOps platform
category: ai-research
complexity: medium
keywords:
- api
- github
- optimization
- python
capabilities: []
token_estimate: 1634
has_references: true
reference_count: 3
---

<!-- Converted from Claude Skill Template -->
<!-- Complexity: Medium -->
<!-- Estimated Tokens: ~1,634 -->
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




# weights-and-biases

> Track ML experiments with automatic logging, visualize training in real-time, optimize hyperparameters with sweeps, and manage model registry with W&B - collaborative MLOps platform

# Weights & Biases: ML Experiment Tracking & MLOps

## When to Use This Skill

Use Weights & Biases (W&B) when you need to:
- **Track ML experiments** with automatic metric logging
- **Visualize training** in real-time dashboards
- **Compare runs** across hyperparameters and configurations
- **Optimize hyperparameters** with automated sweeps
- **Manage model registry** with versioning and lineage
- **Collaborate on ML projects** with team workspaces
- **Track artifacts** (datasets, models, code) with lineage

**Users**: 200,000+ ML practitioners | **GitHub Stars**: 10.5k+ | **Integrations**: 100+

## Installation

```bash
# Install W&B
pip install wandb

# Login (creates API key)
wandb login

# Or set API key programmatically
export WANDB_API_KEY=your_api_key_here
```

## Quick Start

### Basic Experiment Tracking

```python
import wandb

# Initialize a run
run = wandb.init(
    project="my-project",
    config={
        "learning_rate": 0.001,
        "epochs": 10,
        "batch_size": 32,
        "architecture": "ResNet50"
    }
)

# Training loop
for epoch in range(run.config.epochs):
    # Your training code
    train_loss = train_epoch()
    val_loss = validate()

    # Log metrics
    wandb.log({
        "epoch": epoch,
        "train/loss": train_loss,
        "val/loss": val_loss,
        "train/accuracy": train_acc,
        "val/accuracy": val_acc
    })

# Finish the run
wandb.finish()
```

### With PyTorch

```python
import torch
import wandb

# Initialize
wandb.init(project="pytorch-demo", config={
    "lr": 0.001,
    "epochs": 10
})

# Access config
config = wandb.config

# Training loop
for epoch in range(config.epochs):
    for batch_idx, (data, target) in enumerate(train_loader):
        # Forward pass
        output = model(data)
        loss = criterion(output, target)

        # Backward pass
        optimizer.zero_grad()
        loss.backward()
        optimizer.step()

        # Log every 100 batches
        if batch_idx % 100 == 0:
            wandb.log({
                "loss": loss.item(),
                "epoch": epoch,
                "batch": batch_idx
            })

# Save model
torch.save(model.state_dict(), "model.pth")
wandb.save("model.pth")  # Upload to W&B

wandb.finish()
```

## Core Concepts

### 1. Projects and Runs

**Project**: Collection of related experiments
**Run**: Single execution of your training script

```python
# Create/use project
run = wandb.init(
    project="image-classification",
    name="resnet50-experiment-1",  # Optional run name
    tags=["baseline", "resnet"],    # Organize with tags
    notes="First baseline run"      # Add notes
)

# Each run has unique ID
print(f"Run ID: {run.id}")
print(f"Run URL: {run.url}")
```

### 2. Configuration Tracking

Track hyperparameters automatically:

```python
config = {
    # Model architecture
    "model": "ResNet50",
    "pretrained": True,

    # Training params
    "learning_rate": 0.001,
    "batch_size": 32,
    "epochs": 50,
    "optimizer": "Adam",

    # Data params
    "dataset": "ImageNet",
    "augmentation": "standard"
}

wandb.init(project="my-project", config=config)

# Access config during training
lr = wandb.config.learning_rate
batch_size = wandb.config.batch_size
```

### 3. Metric Logging

```python
# Log scalars
wandb.log({"loss": 0.5, "accuracy": 0.92})

# Log multiple metrics
wandb.log({
    "train/loss": train_loss,
    "train/accuracy": train_acc,
    "val/loss": val_loss,
    "val/accuracy": val_acc,
    "learning_rate": current_lr,
    "epoch": epoch
})

# Log with custom x-axis
wandb.log({"loss": loss}, step=global_step)

# Log media (images, audio, video)
wandb.log({"examples": [wandb.Image(img) for img in images]})

# Log histograms
wandb.log({"gradients": wandb.Histogram(gradients)})

# Log tables
table = wandb.Table(columns=["id", "prediction", "ground_truth"])
wandb.log({"predictions": table})
```

### 4. Model Checkpointing

```python
import torch
import wandb

# Save model checkpoint
checkpoint = {
    'epoch': epoch,
    'model_state_dict': model.state_dict(),
    'optimizer_state_dict': optimizer.state_dict(),
    'loss': loss,
}

torch.save(checkpoint, 'checkpoint.pth')

# Upload to W&B
wandb.save('checkpoint.pth')

# Or use Artifacts (recommended)
artifact = wandb.Artifact('model', type='model')
artifact.add_file('checkpoint.pth')
wandb.log_artifact(artifact)
```

## Hyperparameter Sweeps

Automatically search for optimal hyperparameters.

### Define Sweep Configuration

```python
sweep_config = {
    'method': 'bayes',  # or 'grid', 'random'
    'metric': {
        'name': 'val/accuracy',
        'goal': 'maximize'
    },
    'parameters': {
        'learning_rate': {
            'distribution': 'log_uniform',
            'min': 1e-5,
            'max': 1e-1
        },
        'batch_size': {
            'values': [16, 32, 64, 128]
        },
        'optimizer': {
            'values': ['adam', 'sgd', 'rmsprop']
        },
        'dropout': {
            'distribution': 'uniform',
            'min': 0.1,
            'max': 0.5
        }
    }
}

# Initialize sweep
sweep_id = wandb.sweep(sweep_config, project="my-project")
```

### Define Training Function

```python
def train():
    # Initialize run
    run = wandb.init()

    # Access sweep parameters
    lr = wandb.config.learning_rate
    batch_size = wandb.config.batch_size
    optimizer_name = wandb.config.optimizer

    # Build model with sweep config
    model = build_model(wandb.config)
    optimizer = get_optimizer(optimizer_name, lr)

    # Training loop
    for epoch in range(NUM_EPOCHS):
        train_loss = train_epoch(model, optimizer, batch_size)
        val_acc = validate(model)

        # Log metrics
        wandb.log({
            "train/loss": train_loss,
            "val/accuracy": val_acc
        })

# Run sweep
wandb.agent(sweep_id, function=train, count=50)  # Run 50 trials
```

### Sweep Strategies

```python
# Grid search - exhaustive
sweep_config = {
    'method': 'grid',
    'parameters': {
        'lr': {'values': [0.001, 0.01, 0.1]},
        'batch_size': {'values': [16, 32, 64]}
    }
}

# Random search
sweep_config = {
    'method': 'random',
    'parameters': {
        'lr': {'distribution': 'uniform', 'min': 0.0001, 'max': 0.1},
        'dropout': {'distribution': 'uniform', 'min': 0.1, 'max': 0.5}
    }
}

# Bayesian optimization (recommended)
sweep_config = {
    'method': 'bayes',
    'metric': {'name': 'val/loss', 'goal': 'minimize'},
    'parameters': {
        'lr': {'distribution': 'log_uniform', 'min': 1e-5, 'max': 1e-1}
    }
}
```

## Artifacts

Track datasets, models, and other files with lineage.

### Log Artifacts

```python
# Create artifact
artifact = wandb.Artifact(
    name='training-dataset',
    type='dataset',
    description='ImageNet training split',
    metadata={'size': '1.2M images', 'split': 'train'}
)

# Add files
artifact.add_file('data/train.csv')
artifact.add_dir('data/images/')

# Log artifact
wandb.log_artifact(artifact)
```

### Use Artifacts

```python
# Download and use artifact
run = wandb.init(project="my-project")

# Download artifact
artifact = run.use_artifact('training-dataset:latest')
artifact_dir = artifact.download()

# Use the data
data = load_data(f"{artifact_dir}/train.csv")
```

### Model Registry

```python
# Log model as artifact
model_artifact = wandb.Artifact(
    name='resnet50-model',
    type='model',
    metadata={'architecture': 'ResNet50', 'accuracy': 0.95}
)

model_artifact.add_file('model.pth')
wandb.log_artifact(model_artifact, aliases=['best', 'production'])

# Link to model registry
run.link_artifact(model_artifact, 'model-registry/production-models')
```

## Integration Examples

### HuggingFace Transformers

```python
from transformers import Trainer, TrainingArguments
import wandb

# Initialize W&B
wandb.init(project="hf-transformers")

# Training arguments with W&B
training_args = TrainingArguments(
    output_dir="./results",
    report_to="wandb",  # Enable W&B logging
    run_name="bert-finetuning",
    logging_steps=100,
    save_steps=500
)

# Trainer automatically logs to W&B
trainer = Trainer(
    model=model,
    args=training_args,
    train_dataset=train_dataset,
    eval_dataset=eval_dataset
)

trainer.train()
```

### PyTorch Lightning

```python
from pytorch_lightning import Trainer
from pytorch_lightning.loggers import WandbLogger
import wandb

# Create W&B logger
wandb_logger = WandbLogger(
    project="lightning-demo",
    log_model=True  # Log model checkpoints
)

# Use with Trainer
trainer = Trainer(
    logger=wandb_logger,
    max_epochs=10
)

trainer.fit(model, datamodule=dm)
```

### Keras/TensorFlow

```python
import wandb
from wandb.keras import WandbCallback

# Initialize
wandb.init(project="keras-demo")

# Add callback
model.fit(
    x_train, y_train,
    validation_data=(x_val, y_val),
    epochs=10,
    callbacks=[WandbCallback()]  # Auto-logs metrics
)
```

## Visualization & Analysis

### Custom Charts

```python
# Log custom visualizations
import matplotlib.pyplot as plt

fig, ax = plt.subplots()
ax.plot(x, y)
wandb.log({"custom_plot": wandb.Image(fig)})

# Log confusion matrix
wandb.log({"conf_mat": wandb.plot.confusion_matrix(
    probs=None,
    y_true=ground_truth,
    preds=predictions,
    class_names=class_names
)})
```

### Reports

Create shareable reports in W&B UI:
- Combine runs, charts, and text
- Markdown support
- Embeddable visualizations
- Team collaboration

## Best Practices

### 1. Organize with Tags and Groups

```python
wandb.init(
    project="my-project",
    tags=["baseline", "resnet50", "imagenet"],
    group="resnet-experiments",  # Group related runs
    job_type="train"             # Type of job
)
```

### 2. Log Everything Relevant

```python
# Log system metrics
wandb.log({
    "gpu/util": gpu_utilization,
    "gpu/memory": gpu_memory_used,
    "cpu/util": cpu_utilization
})

# Log code version
wandb.log({"git_commit": git_commit_hash})

# Log data splits
wandb.log({
    "data/train_size": len(train_dataset),
    "data/val_size": len(val_dataset)
})
```

### 3. Use Descriptive Names

```python
# ✅ Good: Descriptive run names
wandb.init(
    project="nlp-classification",
    name="bert-base-lr0.001-bs32-epoch10"
)

# ❌ Bad: Generic names
wandb.init(project="nlp", name="run1")
```

### 4. Save Important Artifacts

```python
# Save final model
artifact = wandb.Artifact('final-model', type='model')
artifact.add_file('model.pth')
wandb.log_artifact(artifact)

# Save predictions for analysis
predictions_table = wandb.Table(
    columns=["id", "input", "prediction", "ground_truth"],
    data=predictions_data
)
wandb.log({"predictions": predictions_table})
```

### 5. Use Offline Mode for Unstable Connections

```python
import os

# Enable offline mode
os.environ["WANDB_MODE"] = "offline"

wandb.init(project="my-project")
# ... your code ...

# Sync later
# wandb sync <run_directory>
```

## Team Collaboration

### Share Runs

```python
# Runs are automatically shareable via URL
run = wandb.init(project="team-project")
print(f"Share this URL: {run.url}")
```

### Team Projects

- Create team account at wandb.ai
- Add team members
- Set project visibility (private/public)
- Use team-level artifacts and model registry

## Pricing

- **Free**: Unlimited public projects, 100GB storage
- **Academic**: Free for students/researchers
- **Teams**: $50/seat/month, private projects, unlimited storage
- **Enterprise**: Custom pricing, on-prem options

## Resources

- **Documentation**: https://docs.wandb.ai
- **GitHub**: https://github.com/wandb/wandb (10.5k+ stars)
- **Examples**: https://github.com/wandb/examples
- **Community**: https://wandb.ai/community
- **Discord**: https://wandb.me/discord

## See Also

- `references/sweeps.md` - Comprehensive hyperparameter optimization guide
- `references/artifacts.md` - Data and model versioning patterns
- `references/integrations.md` - Framework-specific examples




---


## 📚 Reference Materials


### Artifacts

# Artifacts & Model Registry Guide

Complete guide to data versioning and model management with W&B Artifacts.

## Table of Contents
- What are Artifacts
- Creating Artifacts
- Using Artifacts
- Model Registry
- Versioning & Lineage
- Best Practices

## What are Artifacts

Artifacts are versioned datasets, models, or files tracked with lineage.

**Key Features:**
- Automatic versioning (v0, v1, v2...)
- Lineage tracking (which runs produced/used artifacts)
- Efficient storage (deduplication)
- Collaboration (team-wide access)
- Aliases (latest, best, production)

**Common Use Cases:**
- Dataset versioning
- Model checkpoints
- Preprocessed data
- Evaluation results
- Configuration files

## Creating Artifacts

### Basic Dataset Artifact

```python
import wandb

run = wandb.init(project="my-project")

# Create artifact
dataset = wandb.Artifact(
    name='training-data',
    type='dataset',
    description='ImageNet training split with augmentations',
    metadata={
        'size': '1.2M images',
        'format': 'JPEG',
        'resolution': '224x224'
    }
)

# Add files
dataset.add_file('data/train.csv')        # Single file
dataset.add_dir('data/images')            # Entire directory
dataset.add_reference('s3://bucket/data') # Cloud reference

# Log artifact
run.log_artifact(dataset)
wandb.finish()
```

### Model Artifact

```python
import torch
import wandb

run = wandb.init(project="my-project")

# Train model
model = train_model()

# Save model
torch.save(model.state_dict(), 'model.pth')

# Create model artifact
model_artifact = wandb.Artifact(
    name='resnet50-classifier',
    type='model',
    description='ResNet50 trained on ImageNet',
    metadata={
        'architecture': 'ResNet50',
        'accuracy': 0.95,
        'loss': 0.15,
        'epochs': 50,
        'framework': 'PyTorch'
    }
)

# Add model file
model_artifact.add_file('model.pth')

# Add config
model_artifact.add_file('config.yaml')

# Log with aliases
run.log_artifact(model_artifact, aliases=['latest', 'best'])

wandb.finish()
```

### Preprocessed Data Artifact

```python
import pandas as pd
import wandb

run = wandb.init(project="nlp-project")

# Preprocess data
df = pd.read_csv('raw_data.csv')
df_processed = preprocess(df)
df_processed.to_csv('processed_data.csv', index=False)

# Create artifact
processed_data = wandb.Artifact(
    name='processed-text-data',
    type='dataset',
    metadata={
        'rows': len(df_processed),
        'columns': list(df_processed.columns),
        'preprocessing_steps': ['lowercase', 'remove_stopwords', 'tokenize']
    }
)

processed_data.add_file('processed_data.csv')

# Log artifact
run.log_artifact(processed_data)
```

## Using Artifacts

### Download and Use

```python
import wandb

run = wandb.init(project="my-project")

# Download artifact
artifact = run.use_artifact('training-data:latest')
artifact_dir = artifact.download()

# Use files
import pandas as pd
df = pd.read_csv(f'{artifact_dir}/train.csv')

# Train with artifact data
model = train_model(df)
```

### Use Specific Version

```python
# Use specific version
artifact_v2 = run.use_artifact('training-data:v2')

# Use alias
artifact_best = run.use_artifact('model:best')
artifact_prod = run.use_artifact('model:production')

# Use from another project
artifact = run.use_artifact('team/other-project/model:latest')
```

### Check Artifact Metadata

```python
artifact = run.use_artifact('training-data:latest')

# Access metadata
print(artifact.metadata)
print(f"Size: {artifact.metadata['size']}")

# Access version info
print(f"Version: {artifact.version}")
print(f"Created at: {artifact.created_at}")
print(f"Digest: {artifact.digest}")
```

## Model Registry

Link models to a central registry for governance and deployment.

### Create Model Registry

```python
# In W&B UI:
# 1. Go to "Registry" tab
# 2. Create new registry: "production-models"
# 3. Define stages: development, staging, production
```

### Link Model to Registry

```python
import wandb

run = wandb.init(project="training")

# Create model artifact
model_artifact = wandb.Artifact(
    name='sentiment-classifier',
    type='model',
    metadata={'accuracy': 0.94, 'f1': 0.92}
)

model_artifact.add_file('model.pth')

# Log artifact
run.log_artifact(model_artifact)

# Link to registry
run.link_artifact(
    model_artifact,
    'model-registry/production-models',
    aliases=['staging']  # Deploy to staging
)

wandb.finish()
```

### Promote Model in Registry

```python
# Retrieve model from registry
api = wandb.Api()
artifact = api.artifact('model-registry/production-models/sentiment-classifier:staging')

# Promote to production
artifact.link('model-registry/production-models', aliases=['production'])

# Demote from production
artifact.aliases = ['archived']
artifact.save()
```

### Use Model from Registry

```python
import wandb

run = wandb.init()

# Download production model
model_artifact = run.use_artifact(
    'model-registry/production-models/sentiment-classifier:production'
)

model_dir = model_artifact.download()

# Load and use
import torch
model = torch.load(f'{model_dir}/model.pth')
model.eval()
```

## Versioning & Lineage

### Automatic Versioning

```python
# First log: creates v0
run1 = wandb.init(project="my-project")
dataset_v0 = wandb.Artifact('my-dataset', type='dataset')
dataset_v0.add_file('data_v1.csv')
run1.log_artifact(dataset_v0)

# Second log with same name: creates v1
run2 = wandb.init(project="my-project")
dataset_v1 = wandb.Artifact('my-dataset', type='dataset')
dataset_v1.add_file('data_v2.csv')  # Different content
run2.log_artifact(dataset_v1)

# Third log with SAME content as v1: references v1 (no new version)
run3 = wandb.init(project="my-project")
dataset_v1_again = wandb.Artifact('my-dataset', type='dataset')
dataset_v1_again.add_file('data_v2.csv')  # Same content as v1
run3.log_artifact(dataset_v1_again)  # Still v1, no v2 created
```

### Track Lineage

```python
# Training run
run = wandb.init(project="my-project")

# Use dataset (input)
dataset = run.use_artifact('training-data:v3')
data = load_data(dataset.download())

# Train model
model = train(data)

# Save model (output)
model_artifact = wandb.Artifact('trained-model', type='model')
torch.save(model.state_dict(), 'model.pth')
model_artifact.add_file('model.pth')
run.log_artifact(model_artifact)

# Lineage automatically tracked:
# training-data:v3 --> [run] --> trained-model:v0
```

### View Lineage Graph

```python
# In W&B UI:
# Artifacts → Select artifact → Lineage tab
# Shows:
# - Which runs produced this artifact
# - Which runs used this artifact
# - Parent/child artifacts
```

## Artifact Types

### Dataset Artifacts

```python
# Raw data
raw_data = wandb.Artifact('raw-data', type='dataset')
raw_data.add_dir('raw/')

# Processed data
processed_data = wandb.Artifact('processed-data', type='dataset')
processed_data.add_dir('processed/')

# Train/val/test splits
train_split = wandb.Artifact('train-split', type='dataset')
train_split.add_file('train.csv')

val_split = wandb.Artifact('val-split', type='dataset')
val_split.add_file('val.csv')
```

### Model Artifacts

```python
# Checkpoint during training
checkpoint = wandb.Artifact('checkpoint-epoch-10', type='model')
checkpoint.add_file('checkpoint_epoch_10.pth')

# Final model
final_model = wandb.Artifact('final-model', type='model')
final_model.add_file('model.pth')
final_model.add_file('tokenizer.json')

# Quantized model
quantized = wandb.Artifact('quantized-model', type='model')
quantized.add_file('model_int8.onnx')
```

### Result Artifacts

```python
# Predictions
predictions = wandb.Artifact('test-predictions', type='predictions')
predictions.add_file('predictions.csv')

# Evaluation metrics
eval_results = wandb.Artifact('evaluation', type='evaluation')
eval_results.add_file('metrics.json')
eval_results.add_file('confusion_matrix.png')
```

## Advanced Patterns

### Incremental Artifacts

Add files incrementally without re-uploading.

```python
run = wandb.init(project="my-project")

# Create artifact
dataset = wandb.Artifact('incremental-dataset', type='dataset')

# Add files incrementally
for i in range(100):
    filename = f'batch_{i}.csv'
    process_batch(i, filename)
    dataset.add_file(filename)

    # Log progress
    if (i + 1) % 10 == 0:
        print(f"Added {i + 1}/100 batches")

# Log complete artifact
run.log_artifact(dataset)
```

### Artifact Tables

Track structured data with W&B Tables.

```python
import wandb

run = wandb.init(project="my-project")

# Create table
table = wandb.Table(columns=["id", "image", "label", "prediction"])

for idx, (img, label, pred) in enumerate(zip(images, labels, predictions)):
    table.add_data(
        idx,
        wandb.Image(img),
        label,
        pred
    )

# Log as artifact
artifact = wandb.Artifact('predictions-table', type='predictions')
artifact.add(table, "predictions")
run.log_artifact(artifact)
```

### Artifact References

Reference external data without copying.

```python
# S3 reference
dataset = wandb.Artifact('s3-dataset', type='dataset')
dataset.add_reference('s3://my-bucket/data/', name='train')
dataset.add_reference('s3://my-bucket/labels/', name='labels')

# GCS reference
dataset.add_reference('gs://my-bucket/data/')

# HTTP reference
dataset.add_reference('https://example.com/data.zip')

# Local filesystem reference (for shared storage)
dataset.add_reference('file:///mnt/shared/data')
```

## Collaboration Patterns

### Team Dataset Sharing

```python
# Data engineer creates dataset
run = wandb.init(project="data-eng", entity="my-team")
dataset = wandb.Artifact('shared-dataset', type='dataset')
dataset.add_dir('data/')
run.log_artifact(dataset, aliases=['latest', 'production'])

# ML engineer uses dataset
run = wandb.init(project="ml-training", entity="my-team")
dataset = run.use_artifact('my-team/data-eng/shared-dataset:production')
data = load_data(dataset.download())
```

### Model Handoff

```python
# Training team
train_run = wandb.init(project="model-training", entity="ml-team")
model = train_model()
model_artifact = wandb.Artifact('nlp-model', type='model')
model_artifact.add_file('model.pth')
train_run.log_artifact(model_artifact)
train_run.link_artifact(model_artifact, 'model-registry/nlp-models', aliases=['candidate'])

# Evaluation team
eval_run = wandb.init(project="model-eval", entity="ml-team")
model_artifact = eval_run.use_artifact('model-registry/nlp-models/nlp-model:candidate')
metrics = evaluate_model(model_artifact)

if metrics['f1'] > 0.9:
    # Promote to production
    model_artifact.link('model-registry/nlp-models', aliases=['production'])
```

## Best Practices

### 1. Use Descriptive Names

```python
# ✅ Good: Descriptive names
wandb.Artifact('imagenet-train-augmented-v2', type='dataset')
wandb.Artifact('bert-base-sentiment-finetuned', type='model')

# ❌ Bad: Generic names
wandb.Artifact('dataset1', type='dataset')
wandb.Artifact('model', type='model')
```

### 2. Add Comprehensive Metadata

```python
model_artifact = wandb.Artifact(
    'production-model',
    type='model',
    description='ResNet50 classifier for product categorization',
    metadata={
        # Model info
        'architecture': 'ResNet50',
        'framework': 'PyTorch 2.0',
        'pretrained': True,

        # Performance
        'accuracy': 0.95,
        'f1_score': 0.93,
        'inference_time_ms': 15,

        # Training
        'epochs': 50,
        'dataset': 'imagenet',
        'num_samples': 1200000,

        # Business context
        'use_case': 'e-commerce product classification',
        'owner': 'ml-team@company.com',
        'approved_by': 'data-science-lead'
    }
)
```

### 3. Use Aliases for Deployment Stages

```python
# Development
run.log_artifact(model, aliases=['dev', 'latest'])

# Staging
run.log_artifact(model, aliases=['staging'])

# Production
run.log_artifact(model, aliases=['production', 'v1.2.0'])

# Archive old versions
old_artifact = api.artifact('model:production')
old_artifact.aliases = ['archived-v1.1.0']
old_artifact.save()
```

### 4. Track Data Lineage

```python
def create_training_pipeline():
    run = wandb.init(project="pipeline")

    # 1. Load raw data
    raw_data = run.use_artifact('raw-data:latest')

    # 2. Preprocess
    processed = preprocess(raw_data)
    processed_artifact = wandb.Artifact('processed-data', type='dataset')
    processed_artifact.add_file('processed.csv')
    run.log_artifact(processed_artifact)

    # 3. Train model
    model = train(processed)
    model_artifact = wandb.Artifact('trained-model', type='model')
    model_artifact.add_file('model.pth')
    run.log_artifact(model_artifact)

    # Lineage: raw-data → processed-data → trained-model
```

### 5. Efficient Storage

```python
# ✅ Good: Reference large files
large_dataset = wandb.Artifact('large-dataset', type='dataset')
large_dataset.add_reference('s3://bucket/huge-file.tar.gz')

# ❌ Bad: Upload giant files
# large_dataset.add_file('huge-file.tar.gz')  # Don't do this

# ✅ Good: Upload only metadata
metadata_artifact = wandb.Artifact('dataset-metadata', type='dataset')
metadata_artifact.add_file('metadata.json')  # Small file
```

## Resources

- **Artifacts Documentation**: https://docs.wandb.ai/guides/artifacts
- **Model Registry**: https://docs.wandb.ai/guides/model-registry
- **Best Practices**: https://wandb.ai/site/articles/versioning-data-and-models-in-ml




### Sweeps

# Comprehensive Hyperparameter Sweeps Guide

Complete guide to hyperparameter optimization with W&B Sweeps.

## Table of Contents
- Sweep Configuration
- Search Strategies
- Parameter Distributions
- Early Termination
- Parallel Execution
- Advanced Patterns
- Real-World Examples

## Sweep Configuration

### Basic Sweep Config

```python
sweep_config = {
    'method': 'bayes',  # Search strategy
    'metric': {
        'name': 'val/accuracy',
        'goal': 'maximize'  # or 'minimize'
    },
    'parameters': {
        'learning_rate': {
            'distribution': 'log_uniform',
            'min': 1e-5,
            'max': 1e-1
        },
        'batch_size': {
            'values': [16, 32, 64, 128]
        }
    }
}

# Initialize sweep
sweep_id = wandb.sweep(sweep_config, project="my-project")
```

### Complete Config Example

```python
sweep_config = {
    # Required: Search method
    'method': 'bayes',

    # Required: Optimization metric
    'metric': {
        'name': 'val/f1_score',
        'goal': 'maximize'
    },

    # Required: Parameters to search
    'parameters': {
        # Continuous parameter
        'learning_rate': {
            'distribution': 'log_uniform',
            'min': 1e-5,
            'max': 1e-1
        },

        # Discrete values
        'batch_size': {
            'values': [16, 32, 64, 128]
        },

        # Categorical
        'optimizer': {
            'values': ['adam', 'sgd', 'rmsprop', 'adamw']
        },

        # Uniform distribution
        'dropout': {
            'distribution': 'uniform',
            'min': 0.1,
            'max': 0.5
        },

        # Integer range
        'num_layers': {
            'distribution': 'int_uniform',
            'min': 2,
            'max': 10
        },

        # Fixed value (constant across runs)
        'epochs': {
            'value': 50
        }
    },

    # Optional: Early termination
    'early_terminate': {
        'type': 'hyperband',
        'min_iter': 5,
        's': 2,
        'eta': 3,
        'max_iter': 27
    }
}
```

## Search Strategies

### 1. Grid Search

Exhaustively search all combinations.

```python
sweep_config = {
    'method': 'grid',
    'parameters': {
        'learning_rate': {
            'values': [0.001, 0.01, 0.1]
        },
        'batch_size': {
            'values': [16, 32, 64]
        },
        'optimizer': {
            'values': ['adam', 'sgd']
        }
    }
}

# Total runs: 3 × 3 × 2 = 18 runs
```

**Pros:**
- Comprehensive search
- Reproducible results
- No randomness

**Cons:**
- Exponential growth with parameters
- Inefficient for continuous parameters
- Not scalable beyond 3-4 parameters

**When to use:**
- Few parameters (< 4)
- All discrete values
- Need complete coverage

### 2. Random Search

Randomly sample parameter combinations.

```python
sweep_config = {
    'method': 'random',
    'parameters': {
        'learning_rate': {
            'distribution': 'log_uniform',
            'min': 1e-5,
            'max': 1e-1
        },
        'batch_size': {
            'values': [16, 32, 64, 128, 256]
        },
        'dropout': {
            'distribution': 'uniform',
            'min': 0.0,
            'max': 0.5
        },
        'num_layers': {
            'distribution': 'int_uniform',
            'min': 2,
            'max': 8
        }
    }
}

# Run 100 random trials
wandb.agent(sweep_id, function=train, count=100)
```

**Pros:**
- Scales to many parameters
- Can run indefinitely
- Often finds good solutions quickly

**Cons:**
- No learning from previous runs
- May miss optimal region
- Results vary with random seed

**When to use:**
- Many parameters (> 4)
- Quick exploration
- Limited budget

### 3. Bayesian Optimization (Recommended)

Learn from previous trials to sample promising regions.

```python
sweep_config = {
    'method': 'bayes',
    'metric': {
        'name': 'val/loss',
        'goal': 'minimize'
    },
    'parameters': {
        'learning_rate': {
            'distribution': 'log_uniform',
            'min': 1e-5,
            'max': 1e-1
        },
        'weight_decay': {
            'distribution': 'log_uniform',
            'min': 1e-6,
            'max': 1e-2
        },
        'dropout': {
            'distribution': 'uniform',
            'min': 0.1,
            'max': 0.5
        },
        'num_layers': {
            'values': [2, 3, 4, 5, 6]
        }
    }
}
```

**Pros:**
- Most sample-efficient
- Learns from past trials
- Focuses on promising regions

**Cons:**
- Initial random exploration phase
- May get stuck in local optima
- Slower per iteration

**When to use:**
- Expensive training runs
- Need best performance
- Limited compute budget

## Parameter Distributions

### Continuous Distributions

```python
# Log-uniform: Good for learning rates, regularization
'learning_rate': {
    'distribution': 'log_uniform',
    'min': 1e-6,
    'max': 1e-1
}

# Uniform: Good for dropout, momentum
'dropout': {
    'distribution': 'uniform',
    'min': 0.0,
    'max': 0.5
}

# Normal distribution
'parameter': {
    'distribution': 'normal',
    'mu': 0.5,
    'sigma': 0.1
}

# Log-normal distribution
'parameter': {
    'distribution': 'log_normal',
    'mu': 0.0,
    'sigma': 1.0
}
```

### Discrete Distributions

```python
# Fixed values
'batch_size': {
    'values': [16, 32, 64, 128, 256]
}

# Integer uniform
'num_layers': {
    'distribution': 'int_uniform',
    'min': 2,
    'max': 10
}

# Quantized uniform (step size)
'layer_size': {
    'distribution': 'q_uniform',
    'min': 32,
    'max': 512,
    'q': 32  # Step by 32: 32, 64, 96, 128...
}

# Quantized log-uniform
'hidden_size': {
    'distribution': 'q_log_uniform',
    'min': 32,
    'max': 1024,
    'q': 32
}
```

### Categorical Parameters

```python
# Optimizers
'optimizer': {
    'values': ['adam', 'sgd', 'rmsprop', 'adamw']
}

# Model architectures
'model': {
    'values': ['resnet18', 'resnet34', 'resnet50', 'efficientnet_b0']
}

# Activation functions
'activation': {
    'values': ['relu', 'gelu', 'silu', 'leaky_relu']
}
```

## Early Termination

Stop underperforming runs early to save compute.

### Hyperband

```python
sweep_config = {
    'method': 'bayes',
    'metric': {'name': 'val/accuracy', 'goal': 'maximize'},
    'parameters': {...},

    # Hyperband early termination
    'early_terminate': {
        'type': 'hyperband',
        'min_iter': 3,      # Minimum iterations before termination
        's': 2,             # Bracket count
        'eta': 3,           # Downsampling rate
        'max_iter': 27      # Maximum iterations
    }
}
```

**How it works:**
- Runs trials in brackets
- Keeps top 1/eta performers each round
- Eliminates bottom performers early

### Custom Termination

```python
def train():
    run = wandb.init()

    for epoch in range(MAX_EPOCHS):
        loss = train_epoch()
        val_acc = validate()

        wandb.log({'val/accuracy': val_acc, 'epoch': epoch})

        # Custom early stopping
        if epoch > 5 and val_acc < 0.5:
            print("Early stop: Poor performance")
            break

        if epoch > 10 and val_acc > best_acc - 0.01:
            print("Early stop: No improvement")
            break
```

## Training Function

### Basic Template

```python
def train():
    # Initialize W&B run
    run = wandb.init()

    # Get hyperparameters
    config = wandb.config

    # Build model with config
    model = build_model(
        hidden_size=config.hidden_size,
        num_layers=config.num_layers,
        dropout=config.dropout
    )

    # Create optimizer
    optimizer = create_optimizer(
        model.parameters(),
        name=config.optimizer,
        lr=config.learning_rate,
        weight_decay=config.weight_decay
    )

    # Training loop
    for epoch in range(config.epochs):
        # Train
        train_loss, train_acc = train_epoch(
            model, optimizer, train_loader, config.batch_size
        )

        # Validate
        val_loss, val_acc = validate(model, val_loader)

        # Log metrics
        wandb.log({
            'train/loss': train_loss,
            'train/accuracy': train_acc,
            'val/loss': val_loss,
            'val/accuracy': val_acc,
            'epoch': epoch
        })

    # Log final model
    torch.save(model.state_dict(), 'model.pth')
    wandb.save('model.pth')

    # Finish run
    wandb.finish()
```

### With PyTorch

```python
import torch
import torch.nn as nn
from torch.utils.data import DataLoader
import wandb

def train():
    run = wandb.init()
    config = wandb.config

    # Data
    train_loader = DataLoader(
        train_dataset,
        batch_size=config.batch_size,
        shuffle=True
    )

    # Model
    model = ResNet(
        num_classes=config.num_classes,
        dropout=config.dropout
    ).to(device)

    # Optimizer
    if config.optimizer == 'adam':
        optimizer = torch.optim.Adam(
            model.parameters(),
            lr=config.learning_rate,
            weight_decay=config.weight_decay
        )
    elif config.optimizer == 'sgd':
        optimizer = torch.optim.SGD(
            model.parameters(),
            lr=config.learning_rate,
            momentum=config.momentum,
            weight_decay=config.weight_decay
        )

    # Scheduler
    scheduler = torch.optim.lr_scheduler.CosineAnnealingLR(
        optimizer, T_max=config.epochs
    )

    # Training
    for epoch in range(config.epochs):
        model.train()
        train_loss = 0.0

        for data, target in train_loader:
            data, target = data.to(device), target.to(device)

            optimizer.zero_grad()
            output = model(data)
            loss = nn.CrossEntropyLoss()(output, target)
            loss.backward()
            optimizer.step()

            train_loss += loss.item()

        # Validation
        model.eval()
        val_loss, val_acc = validate(model, val_loader)

        # Step scheduler
        scheduler.step()

        # Log
        wandb.log({
            'train/loss': train_loss / len(train_loader),
            'val/loss': val_loss,
            'val/accuracy': val_acc,
            'learning_rate': scheduler.get_last_lr()[0],
            'epoch': epoch
        })
```

## Parallel Execution

### Multiple Agents

Run sweep agents in parallel to speed up search.

```python
# Initialize sweep once
sweep_id = wandb.sweep(sweep_config, project="my-project")

# Run multiple agents in parallel
# Agent 1 (Terminal 1)
wandb.agent(sweep_id, function=train, count=20)

# Agent 2 (Terminal 2)
wandb.agent(sweep_id, function=train, count=20)

# Agent 3 (Terminal 3)
wandb.agent(sweep_id, function=train, count=20)

# Total: 60 runs across 3 agents
```

### Multi-GPU Execution

```python
import os

def train():
    # Get available GPU
    gpu_id = os.environ.get('CUDA_VISIBLE_DEVICES', '0')

    run = wandb.init()
    config = wandb.config

    # Train on specific GPU
    device = torch.device(f'cuda:{gpu_id}')
    model = model.to(device)

    # ... rest of training ...

# Run agents on different GPUs
# Terminal 1
# CUDA_VISIBLE_DEVICES=0 wandb agent sweep_id

# Terminal 2
# CUDA_VISIBLE_DEVICES=1 wandb agent sweep_id

# Terminal 3
# CUDA_VISIBLE_DEVICES=2 wandb agent sweep_id
```

## Advanced Patterns

### Nested Parameters

```python
sweep_config = {
    'method': 'bayes',
    'metric': {'name': 'val/accuracy', 'goal': 'maximize'},
    'parameters': {
        'model': {
            'parameters': {
                'type': {
                    'values': ['resnet', 'efficientnet']
                },
                'size': {
                    'values': ['small', 'medium', 'large']
                }
            }
        },
        'optimizer': {
            'parameters': {
                'type': {
                    'values': ['adam', 'sgd']
                },
                'lr': {
                    'distribution': 'log_uniform',
                    'min': 1e-5,
                    'max': 1e-1
                }
            }
        }
    }
}

# Access nested config
def train():
    run = wandb.init()
    model_type = wandb.config.model.type
    model_size = wandb.config.model.size
    opt_type = wandb.config.optimizer.type
    lr = wandb.config.optimizer.lr
```

### Conditional Parameters

```python
sweep_config = {
    'method': 'bayes',
    'parameters': {
        'optimizer': {
            'values': ['adam', 'sgd']
        },
        'learning_rate': {
            'distribution': 'log_uniform',
            'min': 1e-5,
            'max': 1e-1
        },
        # Only used if optimizer == 'sgd'
        'momentum': {
            'distribution': 'uniform',
            'min': 0.5,
            'max': 0.99
        }
    }
}

def train():
    run = wandb.init()
    config = wandb.config

    if config.optimizer == 'adam':
        optimizer = torch.optim.Adam(
            model.parameters(),
            lr=config.learning_rate
        )
    elif config.optimizer == 'sgd':
        optimizer = torch.optim.SGD(
            model.parameters(),
            lr=config.learning_rate,
            momentum=config.momentum  # Conditional parameter
        )
```

## Real-World Examples

### Image Classification

```python
sweep_config = {
    'method': 'bayes',
    'metric': {
        'name': 'val/top1_accuracy',
        'goal': 'maximize'
    },
    'parameters': {
        # Model
        'architecture': {
            'values': ['resnet50', 'resnet101', 'efficientnet_b0', 'efficientnet_b3']
        },
        'pretrained': {
            'values': [True, False]
        },

        # Training
        'learning_rate': {
            'distribution': 'log_uniform',
            'min': 1e-5,
            'max': 1e-2
        },
        'batch_size': {
            'values': [16, 32, 64, 128]
        },
        'optimizer': {
            'values': ['adam', 'sgd', 'adamw']
        },
        'weight_decay': {
            'distribution': 'log_uniform',
            'min': 1e-6,
            'max': 1e-2
        },

        # Regularization
        'dropout': {
            'distribution': 'uniform',
            'min': 0.0,
            'max': 0.5
        },
        'label_smoothing': {
            'distribution': 'uniform',
            'min': 0.0,
            'max': 0.2
        },

        # Data augmentation
        'mixup_alpha': {
            'distribution': 'uniform',
            'min': 0.0,
            'max': 1.0
        },
        'cutmix_alpha': {
            'distribution': 'uniform',
            'min': 0.0,
            'max': 1.0
        }
    },
    'early_terminate': {
        'type': 'hyperband',
        'min_iter': 5
    }
}
```

### NLP Fine-Tuning

```python
sweep_config = {
    'method': 'bayes',
    'metric': {'name': 'eval/f1', 'goal': 'maximize'},
    'parameters': {
        # Model
        'model_name': {
            'values': ['bert-base-uncased', 'roberta-base', 'distilbert-base-uncased']
        },

        # Training
        'learning_rate': {
            'distribution': 'log_uniform',
            'min': 1e-6,
            'max': 1e-4
        },
        'per_device_train_batch_size': {
            'values': [8, 16, 32]
        },
        'num_train_epochs': {
            'values': [3, 4, 5]
        },
        'warmup_ratio': {
            'distribution': 'uniform',
            'min': 0.0,
            'max': 0.1
        },
        'weight_decay': {
            'distribution': 'log_uniform',
            'min': 1e-4,
            'max': 1e-1
        },

        # Optimizer
        'adam_beta1': {
            'distribution': 'uniform',
            'min': 0.8,
            'max': 0.95
        },
        'adam_beta2': {
            'distribution': 'uniform',
            'min': 0.95,
            'max': 0.999
        }
    }
}
```

## Best Practices

### 1. Start Small

```python
# Initial exploration: Random search, 20 runs
sweep_config_v1 = {
    'method': 'random',
    'parameters': {...}
}
wandb.agent(sweep_id_v1, train, count=20)

# Refined search: Bayes, narrow ranges
sweep_config_v2 = {
    'method': 'bayes',
    'parameters': {
        'learning_rate': {
            'min': 5e-5,  # Narrowed from 1e-6 to 1e-4
            'max': 1e-4
        }
    }
}
```

### 2. Use Log Scales

```python
# ✅ Good: Log scale for learning rate
'learning_rate': {
    'distribution': 'log_uniform',
    'min': 1e-6,
    'max': 1e-2
}

# ❌ Bad: Linear scale
'learning_rate': {
    'distribution': 'uniform',
    'min': 0.000001,
    'max': 0.01
}
```

### 3. Set Reasonable Ranges

```python
# Base ranges on prior knowledge
'learning_rate': {'min': 1e-5, 'max': 1e-3},  # Typical for Adam
'batch_size': {'values': [16, 32, 64]},       # GPU memory limits
'dropout': {'min': 0.1, 'max': 0.5}           # Too high hurts training
```

### 4. Monitor Resource Usage

```python
def train():
    run = wandb.init()

    # Log system metrics
    wandb.log({
        'system/gpu_memory_allocated': torch.cuda.memory_allocated(),
        'system/gpu_memory_reserved': torch.cuda.memory_reserved()
    })
```

### 5. Save Best Models

```python
def train():
    run = wandb.init()
    best_acc = 0.0

    for epoch in range(config.epochs):
        val_acc = validate(model)

        if val_acc > best_acc:
            best_acc = val_acc
            # Save best checkpoint
            torch.save(model.state_dict(), 'best_model.pth')
            wandb.save('best_model.pth')
```

## Resources

- **Sweeps Documentation**: https://docs.wandb.ai/guides/sweeps
- **Configuration Reference**: https://docs.wandb.ai/guides/sweeps/configuration
- **Examples**: https://github.com/wandb/examples/tree/master/examples/wandb-sweeps




### Integrations

# Framework Integrations Guide

Complete guide to integrating W&B with popular ML frameworks.

## Table of Contents
- HuggingFace Transformers
- PyTorch Lightning
- Keras/TensorFlow
- Fast.ai
- XGBoost/LightGBM
- PyTorch Native
- Custom Integrations

## HuggingFace Transformers

### Automatic Integration

```python
from transformers import Trainer, TrainingArguments
import wandb

# Initialize W&B
wandb.init(project="hf-transformers", name="bert-finetuning")

# Training arguments with W&B
training_args = TrainingArguments(
    output_dir="./results",
    report_to="wandb",  # Enable W&B logging
    run_name="bert-base-finetuning",

    # Training params
    num_train_epochs=3,
    per_device_train_batch_size=16,
    per_device_eval_batch_size=64,
    learning_rate=2e-5,

    # Logging
    logging_dir="./logs",
    logging_steps=100,
    logging_first_step=True,

    # Evaluation
    evaluation_strategy="steps",
    eval_steps=500,
    save_steps=500,

    # Other
    load_best_model_at_end=True,
    metric_for_best_model="eval_accuracy"
)

# Trainer automatically logs to W&B
trainer = Trainer(
    model=model,
    args=training_args,
    train_dataset=train_dataset,
    eval_dataset=eval_dataset,
    compute_metrics=compute_metrics
)

# Train (metrics logged automatically)
trainer.train()

# Finish W&B run
wandb.finish()
```

### Custom Logging

```python
from transformers import Trainer, TrainingArguments
from transformers.integrations import WandbCallback
import wandb

class CustomWandbCallback(WandbCallback):
    def on_evaluate(self, args, state, control, metrics=None, **kwargs):
        super().on_evaluate(args, state, control, metrics, **kwargs)

        # Log custom metrics
        wandb.log({
            "custom/eval_score": metrics["eval_accuracy"] * 100,
            "custom/epoch": state.epoch
        })

# Use custom callback
trainer = Trainer(
    model=model,
    args=training_args,
    train_dataset=train_dataset,
    eval_dataset=eval_dataset,
    callbacks=[CustomWandbCallback()]
)
```

### Log Model to Registry

```python
from transformers import Trainer, TrainingArguments

training_args = TrainingArguments(
    output_dir="./results",
    report_to="wandb",
    load_best_model_at_end=True
)

trainer = Trainer(
    model=model,
    args=training_args,
    train_dataset=train_dataset,
    eval_dataset=eval_dataset
)

trainer.train()

# Save final model as artifact
model_artifact = wandb.Artifact(
    'hf-bert-model',
    type='model',
    description='BERT finetuned on sentiment analysis'
)

# Save model files
trainer.save_model("./final_model")
model_artifact.add_dir("./final_model")

# Log artifact
wandb.log_artifact(model_artifact, aliases=['best', 'production'])
wandb.finish()
```

## PyTorch Lightning

### Basic Integration

```python
import pytorch_lightning as pl
from pytorch_lightning.loggers import WandbLogger
import wandb

# Create W&B logger
wandb_logger = WandbLogger(
    project="lightning-demo",
    name="resnet50-training",
    log_model=True,  # Log model checkpoints as artifacts
    save_code=True   # Save code as artifact
)

# Lightning module
class LitModel(pl.LightningModule):
    def __init__(self, learning_rate=0.001):
        super().__init__()
        self.save_hyperparameters()
        self.model = create_model()

    def training_step(self, batch, batch_idx):
        x, y = batch
        y_hat = self.model(x)
        loss = F.cross_entropy(y_hat, y)

        # Log metrics (automatically sent to W&B)
        self.log('train/loss', loss, on_step=True, on_epoch=True)
        self.log('train/accuracy', accuracy(y_hat, y), on_epoch=True)

        return loss

    def validation_step(self, batch, batch_idx):
        x, y = batch
        y_hat = self.model(x)
        loss = F.cross_entropy(y_hat, y)

        self.log('val/loss', loss, on_step=False, on_epoch=True)
        self.log('val/accuracy', accuracy(y_hat, y), on_epoch=True)

        return loss

    def configure_optimizers(self):
        return torch.optim.Adam(self.parameters(), lr=self.hparams.learning_rate)

# Trainer with W&B logger
trainer = pl.Trainer(
    logger=wandb_logger,
    max_epochs=10,
    accelerator="gpu",
    devices=1
)

# Train (metrics logged automatically)
trainer.fit(model, datamodule=dm)

# Finish W&B run
wandb.finish()
```

### Log Media

```python
class LitModel(pl.LightningModule):
    def validation_step(self, batch, batch_idx):
        x, y = batch
        y_hat = self.model(x)

        # Log images (first batch only)
        if batch_idx == 0:
            self.logger.experiment.log({
                "examples": [wandb.Image(img) for img in x[:8]]
            })

        return loss

    def on_validation_epoch_end(self):
        # Log confusion matrix
        cm = compute_confusion_matrix(self.all_preds, self.all_targets)

        self.logger.experiment.log({
            "confusion_matrix": wandb.plot.confusion_matrix(
                probs=None,
                y_true=self.all_targets,
                preds=self.all_preds,
                class_names=self.class_names
            )
        })
```

### Hyperparameter Sweeps

```python
import pytorch_lightning as pl
from pytorch_lightning.loggers import WandbLogger
import wandb

# Define sweep
sweep_config = {
    'method': 'bayes',
    'metric': {'name': 'val/accuracy', 'goal': 'maximize'},
    'parameters': {
        'learning_rate': {'min': 1e-5, 'max': 1e-2, 'distribution': 'log_uniform'},
        'batch_size': {'values': [16, 32, 64]},
        'hidden_size': {'values': [128, 256, 512]}
    }
}

sweep_id = wandb.sweep(sweep_config, project="lightning-sweeps")

def train():
    # Initialize W&B
    run = wandb.init()

    # Get hyperparameters
    config = wandb.config

    # Create logger
    wandb_logger = WandbLogger()

    # Create model with sweep params
    model = LitModel(
        learning_rate=config.learning_rate,
        hidden_size=config.hidden_size
    )

    # Create datamodule with sweep batch size
    dm = DataModule(batch_size=config.batch_size)

    # Train
    trainer = pl.Trainer(logger=wandb_logger, max_epochs=10)
    trainer.fit(model, dm)

# Run sweep
wandb.agent(sweep_id, function=train, count=30)
```

## Keras/TensorFlow

### With Callback

```python
import tensorflow as tf
from wandb.keras import WandbCallback
import wandb

# Initialize W&B
wandb.init(
    project="keras-demo",
    config={
        "learning_rate": 0.001,
        "epochs": 10,
        "batch_size": 32
    }
)

config = wandb.config

# Build model
model = tf.keras.Sequential([
    tf.keras.layers.Dense(128, activation='relu'),
    tf.keras.layers.Dropout(0.2),
    tf.keras.layers.Dense(10, activation='softmax')
])

model.compile(
    optimizer=tf.keras.optimizers.Adam(config.learning_rate),
    loss='sparse_categorical_crossentropy',
    metrics=['accuracy']
)

# Train with W&B callback
history = model.fit(
    x_train, y_train,
    validation_data=(x_val, y_val),
    epochs=config.epochs,
    batch_size=config.batch_size,
    callbacks=[
        WandbCallback(
            log_weights=True,      # Log model weights
            log_gradients=True,    # Log gradients
            training_data=(x_train, y_train),
            validation_data=(x_val, y_val),
            labels=class_names
        )
    ]
)

# Save model as artifact
model.save('model.h5')
artifact = wandb.Artifact('keras-model', type='model')
artifact.add_file('model.h5')
wandb.log_artifact(artifact)

wandb.finish()
```

### Custom Training Loop

```python
import tensorflow as tf
import wandb

wandb.init(project="tf-custom-loop")

# Model, optimizer, loss
model = create_model()
optimizer = tf.keras.optimizers.Adam(1e-3)
loss_fn = tf.keras.losses.SparseCategoricalCrossentropy()

# Metrics
train_loss = tf.keras.metrics.Mean(name='train_loss')
train_accuracy = tf.keras.metrics.SparseCategoricalAccuracy(name='train_accuracy')

@tf.function
def train_step(x, y):
    with tf.GradientTape() as tape:
        predictions = model(x, training=True)
        loss = loss_fn(y, predictions)

    gradients = tape.gradient(loss, model.trainable_variables)
    optimizer.apply_gradients(zip(gradients, model.trainable_variables))

    train_loss(loss)
    train_accuracy(y, predictions)

# Training loop
for epoch in range(EPOCHS):
    train_loss.reset_states()
    train_accuracy.reset_states()

    for step, (x, y) in enumerate(train_dataset):
        train_step(x, y)

        # Log every 100 steps
        if step % 100 == 0:
            wandb.log({
                'train/loss': train_loss.result().numpy(),
                'train/accuracy': train_accuracy.result().numpy(),
                'epoch': epoch,
                'step': step
            })

    # Log epoch metrics
    wandb.log({
        'epoch/train_loss': train_loss.result().numpy(),
        'epoch/train_accuracy': train_accuracy.result().numpy(),
        'epoch': epoch
    })

wandb.finish()
```

## Fast.ai

### With Callback

```python
from fastai.vision.all import *
from fastai.callback.wandb import *
import wandb

# Initialize W&B
wandb.init(project="fastai-demo")

# Create data loaders
dls = ImageDataLoaders.from_folder(
    path,
    train='train',
    valid='valid',
    bs=64
)

# Create learner with W&B callback
learn = vision_learner(
    dls,
    resnet34,
    metrics=accuracy,
    cbs=WandbCallback(
        log_preds=True,     # Log predictions
        log_model=True,     # Log model as artifact
        log_dataset=True    # Log dataset as artifact
    )
)

# Train (metrics logged automatically)
learn.fine_tune(5)

wandb.finish()
```

## XGBoost/LightGBM

### XGBoost

```python
import xgboost as xgb
import wandb

# Initialize W&B
run = wandb.init(project="xgboost-demo", config={
    "max_depth": 6,
    "learning_rate": 0.1,
    "n_estimators": 100
})

config = wandb.config

# Create DMatrix
dtrain = xgb.DMatrix(X_train, label=y_train)
dval = xgb.DMatrix(X_val, label=y_val)

# XGBoost params
params = {
    'max_depth': config.max_depth,
    'learning_rate': config.learning_rate,
    'objective': 'binary:logistic',
    'eval_metric': ['logloss', 'auc']
}

# Custom callback for W&B
def wandb_callback(env):
    """Log XGBoost metrics to W&B."""
    for metric_name, metric_value in env.evaluation_result_list:
        wandb.log({
            f"{metric_name}": metric_value,
            "iteration": env.iteration
        })

# Train with callback
model = xgb.train(
    params,
    dtrain,
    num_boost_round=config.n_estimators,
    evals=[(dtrain, 'train'), (dval, 'val')],
    callbacks=[wandb_callback],
    verbose_eval=10
)

# Save model
model.save_model('xgboost_model.json')
artifact = wandb.Artifact('xgboost-model', type='model')
artifact.add_file('xgboost_model.json')
wandb.log_artifact(artifact)

wandb.finish()
```

### LightGBM

```python
import lightgbm as lgb
import wandb

run = wandb.init(project="lgbm-demo")

# Create datasets
train_data = lgb.Dataset(X_train, label=y_train)
val_data = lgb.Dataset(X_val, label=y_val, reference=train_data)

# Parameters
params = {
    'objective': 'binary',
    'metric': ['binary_logloss', 'auc'],
    'learning_rate': 0.1,
    'num_leaves': 31
}

# Custom callback
def log_to_wandb(env):
    """Log LightGBM metrics to W&B."""
    for entry in env.evaluation_result_list:
        dataset_name, metric_name, metric_value, _ = entry
        wandb.log({
            f"{dataset_name}/{metric_name}": metric_value,
            "iteration": env.iteration
        })

# Train
model = lgb.train(
    params,
    train_data,
    num_boost_round=100,
    valid_sets=[train_data, val_data],
    valid_names=['train', 'val'],
    callbacks=[log_to_wandb]
)

# Save model
model.save_model('lgbm_model.txt')
artifact = wandb.Artifact('lgbm-model', type='model')
artifact.add_file('lgbm_model.txt')
wandb.log_artifact(artifact)

wandb.finish()
```

## PyTorch Native

### Training Loop Integration

```python
import torch
import torch.nn as nn
import torch.optim as optim
import wandb

# Initialize W&B
wandb.init(project="pytorch-native", config={
    "learning_rate": 0.001,
    "epochs": 10,
    "batch_size": 32
})

config = wandb.config

# Model, loss, optimizer
model = create_model()
criterion = nn.CrossEntropyLoss()
optimizer = optim.Adam(model.parameters(), lr=config.learning_rate)

# Watch model (logs gradients and parameters)
wandb.watch(model, criterion, log="all", log_freq=100)

# Training loop
for epoch in range(config.epochs):
    model.train()
    train_loss = 0.0
    correct = 0
    total = 0

    for batch_idx, (data, target) in enumerate(train_loader):
        data, target = data.to(device), target.to(device)

        # Forward pass
        optimizer.zero_grad()
        output = model(data)
        loss = criterion(output, target)

        # Backward pass
        loss.backward()
        optimizer.step()

        # Track metrics
        train_loss += loss.item()
        _, predicted = output.max(1)
        total += target.size(0)
        correct += predicted.eq(target).sum().item()

        # Log every 100 batches
        if batch_idx % 100 == 0:
            wandb.log({
                'train/loss': loss.item(),
                'train/batch_accuracy': 100. * correct / total,
                'epoch': epoch,
                'batch': batch_idx
            })

    # Validation
    model.eval()
    val_loss = 0.0
    val_correct = 0
    val_total = 0

    with torch.no_grad():
        for data, target in val_loader:
            data, target = data.to(device), target.to(device)
            output = model(data)
            loss = criterion(output, target)

            val_loss += loss.item()
            _, predicted = output.max(1)
            val_total += target.size(0)
            val_correct += predicted.eq(target).sum().item()

    # Log epoch metrics
    wandb.log({
        'epoch/train_loss': train_loss / len(train_loader),
        'epoch/train_accuracy': 100. * correct / total,
        'epoch/val_loss': val_loss / len(val_loader),
        'epoch/val_accuracy': 100. * val_correct / val_total,
        'epoch': epoch
    })

# Save final model
torch.save(model.state_dict(), 'model.pth')
artifact = wandb.Artifact('final-model', type='model')
artifact.add_file('model.pth')
wandb.log_artifact(artifact)

wandb.finish()
```

## Custom Integrations

### Generic Framework Integration

```python
import wandb

class WandbIntegration:
    """Generic W&B integration wrapper."""

    def __init__(self, project, config):
        self.run = wandb.init(project=project, config=config)
        self.config = wandb.config
        self.step = 0

    def log_metrics(self, metrics, step=None):
        """Log training metrics."""
        if step is None:
            step = self.step
            self.step += 1

        wandb.log(metrics, step=step)

    def log_images(self, images, caption=""):
        """Log images."""
        wandb.log({
            caption: [wandb.Image(img) for img in images]
        })

    def log_table(self, data, columns):
        """Log tabular data."""
        table = wandb.Table(columns=columns, data=data)
        wandb.log({"table": table})

    def save_model(self, model_path, metadata=None):
        """Save model as artifact."""
        artifact = wandb.Artifact(
            'model',
            type='model',
            metadata=metadata or {}
        )
        artifact.add_file(model_path)
        self.run.log_artifact(artifact)

    def finish(self):
        """Finish W&B run."""
        wandb.finish()

# Usage
wb = WandbIntegration(project="my-project", config={"lr": 0.001})

# Training loop
for epoch in range(10):
    # Your training code
    loss, accuracy = train_epoch()

    # Log metrics
    wb.log_metrics({
        'train/loss': loss,
        'train/accuracy': accuracy
    })

# Save model
wb.save_model('model.pth', metadata={'accuracy': 0.95})
wb.finish()
```

## Resources

- **Integrations Guide**: https://docs.wandb.ai/guides/integrations
- **HuggingFace**: https://docs.wandb.ai/guides/integrations/huggingface
- **PyTorch Lightning**: https://docs.wandb.ai/guides/integrations/lightning
- **Keras**: https://docs.wandb.ai/guides/integrations/keras
- **Examples**: https://github.com/wandb/examples




---

## 🚀 Usage

**Reference this template:** `@skill-weights-and-biases.md`


**Platform-specific:**
- **GitHub Copilot**: Add to `.github/copilot-instructions.md`
- **Augment Code**: Use `aug context add` command
- **Cursor/Windsurf**: Reference in chat with `@filename`
- **Claude**: Add to Project Knowledge
- **ChatGPT**: Add to Custom GPT configuration
