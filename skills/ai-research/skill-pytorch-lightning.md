---
id: skill-pytorch-lightning
type: skill
name: pytorch-lightning
description: High-level PyTorch framework with Trainer class, automatic distributed
  training (DDP/FSDP/DeepSpeed), callbacks system, and minimal boilerplate. Scales
  from laptop to supercomputer with same code. Use when you want clean training loops
  with built-in best practices.
category: ai-research
complexity: medium
keywords:
- github
- python
- rest
- test
- testing
capabilities: []
token_estimate: 1159
has_references: true
reference_count: 3
---

<!-- Converted from Claude Skill Template -->
<!-- Complexity: Medium -->
<!-- Estimated Tokens: ~1,159 -->
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




# pytorch-lightning

> High-level PyTorch framework with Trainer class, automatic distributed training (DDP/FSDP/DeepSpeed), callbacks system, and minimal boilerplate. Scales from laptop to supercomputer with same code. Use when you want clean training loops with built-in best practices.

# PyTorch Lightning - High-Level Training Framework

## Quick start

PyTorch Lightning organizes PyTorch code to eliminate boilerplate while maintaining flexibility.

**Installation**:
```bash
pip install lightning
```

**Convert PyTorch to Lightning** (3 steps):

```python
import lightning as L
import torch
from torch import nn
from torch.utils.data import DataLoader, Dataset

# Step 1: Define LightningModule (organize your PyTorch code)
class LitModel(L.LightningModule):
    def __init__(self, hidden_size=128):
        super().__init__()
        self.model = nn.Sequential(
            nn.Linear(28 * 28, hidden_size),
            nn.ReLU(),
            nn.Linear(hidden_size, 10)
        )

    def training_step(self, batch, batch_idx):
        x, y = batch
        y_hat = self.model(x)
        loss = nn.functional.cross_entropy(y_hat, y)
        self.log('train_loss', loss)  # Auto-logged to TensorBoard
        return loss

    def configure_optimizers(self):
        return torch.optim.Adam(self.parameters(), lr=1e-3)

# Step 2: Create data
train_loader = DataLoader(train_dataset, batch_size=32)

# Step 3: Train with Trainer (handles everything else!)
trainer = L.Trainer(max_epochs=10, accelerator='gpu', devices=2)
model = LitModel()
trainer.fit(model, train_loader)
```

**That's it!** Trainer handles:
- GPU/TPU/CPU switching
- Distributed training (DDP, FSDP, DeepSpeed)
- Mixed precision (FP16, BF16)
- Gradient accumulation
- Checkpointing
- Logging
- Progress bars

## Common workflows

### Workflow 1: From PyTorch to Lightning

**Original PyTorch code**:
```python
model = MyModel()
optimizer = torch.optim.Adam(model.parameters())
model.to('cuda')

for epoch in range(max_epochs):
    for batch in train_loader:
        batch = batch.to('cuda')
        optimizer.zero_grad()
        loss = model(batch)
        loss.backward()
        optimizer.step()
```

**Lightning version**:
```python
class LitModel(L.LightningModule):
    def __init__(self):
        super().__init__()
        self.model = MyModel()

    def training_step(self, batch, batch_idx):
        loss = self.model(batch)  # No .to('cuda') needed!
        return loss

    def configure_optimizers(self):
        return torch.optim.Adam(self.parameters())

# Train
trainer = L.Trainer(max_epochs=10, accelerator='gpu')
trainer.fit(LitModel(), train_loader)
```

**Benefits**: 40+ lines → 15 lines, no device management, automatic distributed

### Workflow 2: Validation and testing

```python
class LitModel(L.LightningModule):
    def __init__(self):
        super().__init__()
        self.model = MyModel()

    def training_step(self, batch, batch_idx):
        x, y = batch
        y_hat = self.model(x)
        loss = nn.functional.cross_entropy(y_hat, y)
        self.log('train_loss', loss)
        return loss

    def validation_step(self, batch, batch_idx):
        x, y = batch
        y_hat = self.model(x)
        val_loss = nn.functional.cross_entropy(y_hat, y)
        acc = (y_hat.argmax(dim=1) == y).float().mean()
        self.log('val_loss', val_loss)
        self.log('val_acc', acc)

    def test_step(self, batch, batch_idx):
        x, y = batch
        y_hat = self.model(x)
        test_loss = nn.functional.cross_entropy(y_hat, y)
        self.log('test_loss', test_loss)

    def configure_optimizers(self):
        return torch.optim.Adam(self.parameters(), lr=1e-3)

# Train with validation
trainer = L.Trainer(max_epochs=10)
trainer.fit(model, train_loader, val_loader)

# Test
trainer.test(model, test_loader)
```

**Automatic features**:
- Validation runs every epoch by default
- Metrics logged to TensorBoard
- Best model checkpointing based on val_loss

### Workflow 3: Distributed training (DDP)

```python
# Same code as single GPU!
model = LitModel()

# 8 GPUs with DDP (automatic!)
trainer = L.Trainer(
    accelerator='gpu',
    devices=8,
    strategy='ddp'  # Or 'fsdp', 'deepspeed'
)

trainer.fit(model, train_loader)
```

**Launch**:
```bash
# Single command, Lightning handles the rest
python train.py
```

**No changes needed**:
- Automatic data distribution
- Gradient synchronization
- Multi-node support (just set `num_nodes=2`)

### Workflow 4: Callbacks for monitoring

```python
from lightning.pytorch.callbacks import ModelCheckpoint, EarlyStopping, LearningRateMonitor

# Create callbacks
checkpoint = ModelCheckpoint(
    monitor='val_loss',
    mode='min',
    save_top_k=3,
    filename='model-{epoch:02d}-{val_loss:.2f}'
)

early_stop = EarlyStopping(
    monitor='val_loss',
    patience=5,
    mode='min'
)

lr_monitor = LearningRateMonitor(logging_interval='epoch')

# Add to Trainer
trainer = L.Trainer(
    max_epochs=100,
    callbacks=[checkpoint, early_stop, lr_monitor]
)

trainer.fit(model, train_loader, val_loader)
```

**Result**:
- Auto-saves best 3 models
- Stops early if no improvement for 5 epochs
- Logs learning rate to TensorBoard

### Workflow 5: Learning rate scheduling

```python
class LitModel(L.LightningModule):
    # ... (training_step, etc.)

    def configure_optimizers(self):
        optimizer = torch.optim.Adam(self.parameters(), lr=1e-3)

        # Cosine annealing
        scheduler = torch.optim.lr_scheduler.CosineAnnealingLR(
            optimizer,
            T_max=100,
            eta_min=1e-5
        )

        return {
            'optimizer': optimizer,
            'lr_scheduler': {
                'scheduler': scheduler,
                'interval': 'epoch',  # Update per epoch
                'frequency': 1
            }
        }

# Learning rate auto-logged!
trainer = L.Trainer(max_epochs=100)
trainer.fit(model, train_loader)
```

## When to use vs alternatives

**Use PyTorch Lightning when**:
- Want clean, organized code
- Need production-ready training loops
- Switching between single GPU, multi-GPU, TPU
- Want built-in callbacks and logging
- Team collaboration (standardized structure)

**Key advantages**:
- **Organized**: Separates research code from engineering
- **Automatic**: DDP, FSDP, DeepSpeed with 1 line
- **Callbacks**: Modular training extensions
- **Reproducible**: Less boilerplate = fewer bugs
- **Tested**: 1M+ downloads/month, battle-tested

**Use alternatives instead**:
- **Accelerate**: Minimal changes to existing code, more flexibility
- **Ray Train**: Multi-node orchestration, hyperparameter tuning
- **Raw PyTorch**: Maximum control, learning purposes
- **Keras**: TensorFlow ecosystem

## Common issues

**Issue: Loss not decreasing**

Check data and model setup:
```python
# Add to training_step
def training_step(self, batch, batch_idx):
    if batch_idx == 0:
        print(f"Batch shape: {batch[0].shape}")
        print(f"Labels: {batch[1]}")
    loss = ...
    return loss
```

**Issue: Out of memory**

Reduce batch size or use gradient accumulation:
```python
trainer = L.Trainer(
    accumulate_grad_batches=4,  # Effective batch = batch_size × 4
    precision='bf16'  # Or 'fp16', reduces memory 50%
)
```

**Issue: Validation not running**

Ensure you pass val_loader:
```python
# WRONG
trainer.fit(model, train_loader)

# CORRECT
trainer.fit(model, train_loader, val_loader)
```

**Issue: DDP spawns multiple processes unexpectedly**

Lightning auto-detects GPUs. Explicitly set devices:
```python
# Test on CPU first
trainer = L.Trainer(accelerator='cpu', devices=1)

# Then GPU
trainer = L.Trainer(accelerator='gpu', devices=1)
```

## Advanced topics

**Callbacks**: See [references/callbacks.md](references/callbacks.md) for EarlyStopping, ModelCheckpoint, custom callbacks, and callback hooks.

**Distributed strategies**: See [references/distributed.md](references/distributed.md) for DDP, FSDP, DeepSpeed ZeRO integration, multi-node setup.

**Hyperparameter tuning**: See [references/hyperparameter-tuning.md](references/hyperparameter-tuning.md) for integration with Optuna, Ray Tune, and WandB sweeps.

## Hardware requirements

- **CPU**: Works (good for debugging)
- **Single GPU**: Works
- **Multi-GPU**: DDP (default), FSDP, or DeepSpeed
- **Multi-node**: DDP, FSDP, DeepSpeed
- **TPU**: Supported (8 cores)
- **Apple MPS**: Supported

**Precision options**:
- FP32 (default)
- FP16 (V100, older GPUs)
- BF16 (A100/H100, recommended)
- FP8 (H100)

## Resources

- Docs: https://lightning.ai/docs/pytorch/stable/
- GitHub: https://github.com/Lightning-AI/pytorch-lightning ⭐ 29,000+
- Version: 2.5.5+
- Examples: https://github.com/Lightning-AI/pytorch-lightning/tree/master/examples
- Discord: https://discord.gg/lightning-ai
- Used by: Kaggle winners, research labs, production teams




---


## 📚 Reference Materials


### Callbacks

# PyTorch Lightning Callbacks

## Overview

Callbacks add functionality to training without modifying the LightningModule. They capture **non-essential logic** like checkpointing, early stopping, and logging.

## Built-In Callbacks

### 1. ModelCheckpoint

**Saves best models during training**:

```python
from lightning.pytorch.callbacks import ModelCheckpoint

# Save top 3 models based on validation loss
checkpoint = ModelCheckpoint(
    dirpath='checkpoints/',
    filename='model-{epoch:02d}-{val_loss:.2f}',
    monitor='val_loss',
    mode='min',
    save_top_k=3,
    save_last=True,  # Also save last epoch
    verbose=True
)

trainer = L.Trainer(callbacks=[checkpoint])
trainer.fit(model, train_loader, val_loader)
```

**Configuration options**:
```python
checkpoint = ModelCheckpoint(
    monitor='val_acc',        # Metric to monitor
    mode='max',               # 'max' for accuracy, 'min' for loss
    save_top_k=5,             # Keep best 5 models
    save_last=True,           # Save last epoch separately
    every_n_epochs=1,         # Save every N epochs
    save_on_train_epoch_end=False,  # Save on validation end instead
    filename='best-{epoch}-{val_acc:.3f}',  # Naming pattern
    auto_insert_metric_name=False  # Don't auto-add metric to filename
)
```

**Load checkpoint**:
```python
# Load best model
best_model_path = checkpoint.best_model_path
model = LitModel.load_from_checkpoint(best_model_path)

# Resume training
trainer = L.Trainer(callbacks=[checkpoint])
trainer.fit(model, train_loader, val_loader, ckpt_path='checkpoints/last.ckpt')
```

### 2. EarlyStopping

**Stops training when metric stops improving**:

```python
from lightning.pytorch.callbacks import EarlyStopping

early_stop = EarlyStopping(
    monitor='val_loss',
    patience=5,               # Wait 5 epochs
    mode='min',
    min_delta=0.001,          # Minimum change to qualify as improvement
    verbose=True,
    strict=True,              # Crash if monitored metric not found
    check_on_train_epoch_end=False  # Check on validation end
)

trainer = L.Trainer(callbacks=[early_stop])
trainer.fit(model, train_loader, val_loader)
# Stops automatically if no improvement for 5 epochs
```

**Advanced usage**:
```python
early_stop = EarlyStopping(
    monitor='val_loss',
    patience=10,
    min_delta=0.0,
    verbose=True,
    mode='min',
    stopping_threshold=0.1,   # Stop if val_loss < 0.1
    divergence_threshold=5.0, # Stop if val_loss > 5.0
    check_finite=True         # Stop on NaN/Inf
)
```

### 3. LearningRateMonitor

**Logs learning rate**:

```python
from lightning.pytorch.callbacks import LearningRateMonitor

lr_monitor = LearningRateMonitor(
    logging_interval='epoch',  # Or 'step'
    log_momentum=True          # Also log momentum
)

trainer = L.Trainer(callbacks=[lr_monitor])
# Learning rate automatically logged to TensorBoard/WandB
```

### 4. TQDMProgressBar

**Customizes progress bar**:

```python
from lightning.pytorch.callbacks import TQDMProgressBar

progress_bar = TQDMProgressBar(
    refresh_rate=10,  # Update every 10 batches
    process_position=0
)

trainer = L.Trainer(callbacks=[progress_bar])
```

### 5. GradientAccumulationScheduler

**Dynamic gradient accumulation**:

```python
from lightning.pytorch.callbacks import GradientAccumulationScheduler

# Accumulate more gradients as training progresses
accumulator = GradientAccumulationScheduler(
    scheduling={
        0: 8,   # Epochs 0-4: accumulate 8 batches
        5: 4,   # Epochs 5-9: accumulate 4 batches
        10: 2   # Epochs 10+: accumulate 2 batches
    }
)

trainer = L.Trainer(callbacks=[accumulator])
```

### 6. StochasticWeightAveraging (SWA)

**Averages weights for better generalization**:

```python
from lightning.pytorch.callbacks import StochasticWeightAveraging

swa = StochasticWeightAveraging(
    swa_lrs=1e-2,  # SWA learning rate
    swa_epoch_start=0.8,  # Start at 80% of training
    annealing_epochs=10,  # Annealing period
    annealing_strategy='cos'  # 'cos' or 'linear'
)

trainer = L.Trainer(callbacks=[swa])
```

## Custom Callbacks

### Basic Custom Callback

```python
from lightning.pytorch.callbacks import Callback

class PrintingCallback(Callback):
    def on_train_start(self, trainer, pl_module):
        print("Training is starting!")

    def on_train_end(self, trainer, pl_module):
        print("Training is done!")

    def on_epoch_end(self, trainer, pl_module):
        print(f"Epoch {trainer.current_epoch} ended")

# Use it
trainer = L.Trainer(callbacks=[PrintingCallback()])
```

### Advanced Custom Callback

```python
class MetricsCallback(Callback):
    """Logs custom metrics every N batches."""

    def __init__(self, log_every_n_batches=100):
        self.log_every_n_batches = log_every_n_batches
        self.metrics = []

    def on_train_batch_end(self, trainer, pl_module, outputs, batch, batch_idx):
        if batch_idx % self.log_every_n_batches == 0:
            # Compute custom metric
            metric = self.compute_metric(outputs)
            self.metrics.append(metric)

            # Log to Lightning
            pl_module.log('custom_metric', metric)

    def compute_metric(self, outputs):
        # Your custom logic
        return outputs['loss'].item()

    def state_dict(self):
        """Save callback state in checkpoint."""
        return {'metrics': self.metrics}

    def load_state_dict(self, state_dict):
        """Restore callback state from checkpoint."""
        self.metrics = state_dict['metrics']
```

### Gradient Monitoring Callback

```python
class GradientMonitorCallback(Callback):
    """Monitor gradient norms."""

    def on_after_backward(self, trainer, pl_module):
        # Compute gradient norm
        total_norm = 0.0
        for p in pl_module.parameters():
            if p.grad is not None:
                param_norm = p.grad.data.norm(2)
                total_norm += param_norm.item() ** 2
        total_norm = total_norm ** 0.5

        # Log
        pl_module.log('grad_norm', total_norm)

        # Warn if exploding
        if total_norm > 100:
            print(f"Warning: Large gradient norm: {total_norm:.2f}")
```

### Model Inspection Callback

```python
class ModelInspectionCallback(Callback):
    """Inspect model activations during training."""

    def on_train_batch_start(self, trainer, pl_module, batch, batch_idx):
        if batch_idx == 0:  # First batch of epoch
            # Register hooks
            self.activations = {}

            def get_activation(name):
                def hook(model, input, output):
                    self.activations[name] = output.detach()
                return hook

            # Attach to specific layers
            pl_module.model.layer1.register_forward_hook(get_activation('layer1'))
            pl_module.model.layer2.register_forward_hook(get_activation('layer2'))

    def on_train_batch_end(self, trainer, pl_module, outputs, batch, batch_idx):
        if batch_idx == 0:
            # Log activation statistics
            for name, activation in self.activations.items():
                mean = activation.mean().item()
                std = activation.std().item()
                pl_module.log(f'{name}_mean', mean)
                pl_module.log(f'{name}_std', std)
```

## Callback Hooks

**All available hooks**:

```python
class MyCallback(Callback):
    # Setup/Teardown
    def setup(self, trainer, pl_module, stage):
        """Called at beginning of fit/test/predict."""
        pass

    def teardown(self, trainer, pl_module, stage):
        """Called at end of fit/test/predict."""
        pass

    # Training
    def on_train_start(self, trainer, pl_module):
        pass

    def on_train_epoch_start(self, trainer, pl_module):
        pass

    def on_train_batch_start(self, trainer, pl_module, batch, batch_idx):
        pass

    def on_train_batch_end(self, trainer, pl_module, outputs, batch, batch_idx):
        pass

    def on_train_epoch_end(self, trainer, pl_module):
        pass

    def on_train_end(self, trainer, pl_module):
        pass

    # Validation
    def on_validation_start(self, trainer, pl_module):
        pass

    def on_validation_epoch_start(self, trainer, pl_module):
        pass

    def on_validation_batch_start(self, trainer, pl_module, batch, batch_idx, dataloader_idx):
        pass

    def on_validation_batch_end(self, trainer, pl_module, outputs, batch, batch_idx, dataloader_idx):
        pass

    def on_validation_epoch_end(self, trainer, pl_module):
        pass

    def on_validation_end(self, trainer, pl_module):
        pass

    # Test (same structure as validation)
    def on_test_start(self, trainer, pl_module):
        pass
    # ... (test_epoch_start, test_batch_start, etc.)

    # Predict
    def on_predict_start(self, trainer, pl_module):
        pass
    # ... (predict_epoch_start, predict_batch_start, etc.)

    # Backward
    def on_before_backward(self, trainer, pl_module, loss):
        pass

    def on_after_backward(self, trainer, pl_module):
        pass

    # Optimizer
    def on_before_optimizer_step(self, trainer, pl_module, optimizer):
        pass

    # Checkpointing
    def on_save_checkpoint(self, trainer, pl_module, checkpoint):
        """Add data to checkpoint."""
        pass

    def on_load_checkpoint(self, trainer, pl_module, checkpoint):
        """Restore data from checkpoint."""
        pass
```

## Combining Multiple Callbacks

```python
from lightning.pytorch.callbacks import ModelCheckpoint, EarlyStopping, LearningRateMonitor

# Create all callbacks
checkpoint = ModelCheckpoint(monitor='val_loss', mode='min', save_top_k=3)
early_stop = EarlyStopping(monitor='val_loss', patience=5)
lr_monitor = LearningRateMonitor(logging_interval='epoch')
custom_callback = MyCustomCallback()

# Add all to Trainer
trainer = L.Trainer(
    callbacks=[checkpoint, early_stop, lr_monitor, custom_callback]
)

trainer.fit(model, train_loader, val_loader)
```

**Execution order**: Callbacks execute in the order they're added

## Best Practices

### 1. Keep Callbacks Independent

**Bad** (dependent on other callback):
```python
class BadCallback(Callback):
    def on_train_end(self, trainer, pl_module):
        # Assumes ModelCheckpoint is present
        best_path = trainer.checkpoint_callback.best_model_path  # Fragile!
```

**Good** (self-contained):
```python
class GoodCallback(Callback):
    def on_train_end(self, trainer, pl_module):
        # Find checkpoint callback if present
        for callback in trainer.callbacks:
            if isinstance(callback, ModelCheckpoint):
                best_path = callback.best_model_path
                break
```

### 2. Use State Dict for Persistence

```python
class StatefulCallback(Callback):
    def __init__(self):
        self.counter = 0
        self.history = []

    def on_train_batch_end(self, trainer, pl_module, outputs, batch, batch_idx):
        self.counter += 1
        self.history.append(outputs['loss'].item())

    def state_dict(self):
        """Save state."""
        return {
            'counter': self.counter,
            'history': self.history
        }

    def load_state_dict(self, state_dict):
        """Restore state."""
        self.counter = state_dict['counter']
        self.history = state_dict['history']
```

### 3. Handle Distributed Training

```python
class DistributedCallback(Callback):
    def on_train_batch_end(self, trainer, pl_module, outputs, batch, batch_idx):
        # Only run on main process
        if trainer.is_global_zero:
            print("This only prints once in distributed training")

        # Run on all processes
        loss = outputs['loss']
        # ... do something with loss on each GPU
```

## Resources

- Callback API: https://lightning.ai/docs/pytorch/stable/extensions/callbacks.html
- Built-in callbacks: https://lightning.ai/docs/pytorch/stable/api_references.html#callbacks
- Examples: https://github.com/Lightning-AI/pytorch-lightning/tree/master/examples/callbacks




### Hyperparameter Tuning

# Hyperparameter Tuning with PyTorch Lightning

## Integration with Tuning Frameworks

Lightning integrates seamlessly with popular hyperparameter tuning libraries.

### 1. Ray Tune Integration

**Installation**:
```bash
pip install ray[tune]
pip install lightning
```

**Basic Ray Tune example**:

```python
import lightning as L
from ray import tune
from ray.tune.integration.pytorch_lightning import TuneReportCallback

class LitModel(L.LightningModule):
    def __init__(self, lr, batch_size):
        super().__init__()
        self.lr = lr
        self.batch_size = batch_size
        self.model = nn.Sequential(nn.Linear(10, 128), nn.ReLU(), nn.Linear(128, 1))

    def training_step(self, batch, batch_idx):
        loss = self.model(batch).mean()
        self.log('train_loss', loss)
        return loss

    def validation_step(self, batch, batch_idx):
        val_loss = self.model(batch).mean()
        self.log('val_loss', val_loss)

    def configure_optimizers(self):
        return torch.optim.Adam(self.parameters(), lr=self.lr)

def train_fn(config):
    """Training function for Ray Tune."""
    model = LitModel(lr=config["lr"], batch_size=config["batch_size"])

    # Add callback to report metrics to Tune
    trainer = L.Trainer(
        max_epochs=10,
        callbacks=[TuneReportCallback({"loss": "val_loss"}, on="validation_end")]
    )

    trainer.fit(model, train_loader, val_loader)

# Define search space
config = {
    "lr": tune.loguniform(1e-5, 1e-1),
    "batch_size": tune.choice([16, 32, 64, 128])
}

# Run hyperparameter search
analysis = tune.run(
    train_fn,
    config=config,
    num_samples=20,  # 20 trials
    resources_per_trial={"gpu": 1}
)

# Best hyperparameters
best_config = analysis.get_best_config(metric="loss", mode="min")
print(f"Best config: {best_config}")
```

**Advanced: Population-Based Training (PBT)**:

```python
from ray.tune.schedulers import PopulationBasedTraining

# PBT scheduler
scheduler = PopulationBasedTraining(
    time_attr='training_iteration',
    metric='val_loss',
    mode='min',
    perturbation_interval=5,  # Perturb every 5 epochs
    hyperparam_mutations={
        "lr": tune.loguniform(1e-5, 1e-1),
        "batch_size": [16, 32, 64, 128]
    }
)

analysis = tune.run(
    train_fn,
    config=config,
    num_samples=8,  # Population size
    scheduler=scheduler,
    resources_per_trial={"gpu": 1}
)
```

### 2. Optuna Integration

**Installation**:
```bash
pip install optuna
pip install optuna-integration
```

**Optuna example**:

```python
import optuna
from optuna.integration import PyTorchLightningPruningCallback

def objective(trial):
    # Suggest hyperparameters
    lr = trial.suggest_loguniform('lr', 1e-5, 1e-1)
    batch_size = trial.suggest_categorical('batch_size', [16, 32, 64, 128])
    n_layers = trial.suggest_int('n_layers', 1, 3)
    hidden_size = trial.suggest_int('hidden_size', 64, 512, step=64)

    # Create model
    model = LitModel(lr=lr, n_layers=n_layers, hidden_size=hidden_size)

    # Pruning callback (early stopping for bad trials)
    pruning_callback = PyTorchLightningPruningCallback(trial, monitor="val_loss")

    trainer = L.Trainer(
        max_epochs=20,
        callbacks=[pruning_callback],
        enable_progress_bar=False,
        logger=False
    )

    trainer.fit(model, train_loader, val_loader)

    return trainer.callback_metrics["val_loss"].item()

# Create study
study = optuna.create_study(
    direction='minimize',
    pruner=optuna.pruners.MedianPruner()  # Prune bad trials early
)

# Optimize
study.optimize(objective, n_trials=50, timeout=3600)

# Best params
print(f"Best trial: {study.best_trial.params}")
print(f"Best value: {study.best_value}")

# Visualization
optuna.visualization.plot_optimization_history(study).show()
optuna.visualization.plot_param_importances(study).show()
```

**Optuna with distributed training**:

```python
import optuna

# Shared database for distributed optimization
storage = optuna.storages.RDBStorage(
    url='postgresql://user:pass@localhost/optuna'
)

study = optuna.create_study(
    study_name='distributed_study',
    storage=storage,
    load_if_exists=True,
    direction='minimize'
)

# Run on multiple machines
study.optimize(objective, n_trials=50)
```

### 3. Weights & Biases (WandB) Sweeps

**Installation**:
```bash
pip install wandb
```

**WandB sweep config** (`sweep.yaml`):
```yaml
program: train.py
method: bayes
metric:
  name: val_loss
  goal: minimize
parameters:
  lr:
    distribution: log_uniform_values
    min: 0.00001
    max: 0.1
  batch_size:
    values: [16, 32, 64, 128]
  optimizer:
    values: ['adam', 'sgd', 'adamw']
  dropout:
    distribution: uniform
    min: 0.0
    max: 0.5
```

**Training script** (`train.py`):
```python
import wandb
import lightning as L
from lightning.pytorch.loggers import WandbLogger

def train():
    # Initialize wandb
    wandb.init()
    config = wandb.config

    # Create model with sweep params
    model = LitModel(
        lr=config.lr,
        batch_size=config.batch_size,
        optimizer=config.optimizer,
        dropout=config.dropout
    )

    # WandB logger
    wandb_logger = WandbLogger(project='hyperparameter-sweep')

    trainer = L.Trainer(
        max_epochs=20,
        logger=wandb_logger
    )

    trainer.fit(model, train_loader, val_loader)

if __name__ == '__main__':
    train()
```

**Launch sweep**:
```bash
# Initialize sweep
wandb sweep sweep.yaml
# Output: wandb: Created sweep with ID: abc123

# Run agent (can run on multiple machines)
wandb agent your-entity/your-project/abc123
```

### 4. Hyperopt Integration

**Installation**:
```bash
pip install hyperopt
```

**Hyperopt example**:

```python
from hyperopt import hp, fmin, tpe, Trials

def objective(params):
    model = LitModel(
        lr=params['lr'],
        batch_size=int(params['batch_size']),
        hidden_size=int(params['hidden_size'])
    )

    trainer = L.Trainer(
        max_epochs=10,
        enable_progress_bar=False,
        logger=False
    )

    trainer.fit(model, train_loader, val_loader)

    # Return loss (minimize)
    return trainer.callback_metrics["val_loss"].item()

# Define search space
space = {
    'lr': hp.loguniform('lr', np.log(1e-5), np.log(1e-1)),
    'batch_size': hp.quniform('batch_size', 16, 128, 16),
    'hidden_size': hp.quniform('hidden_size', 64, 512, 64)
}

# Optimize
trials = Trials()
best = fmin(
    fn=objective,
    space=space,
    algo=tpe.suggest,  # Tree-structured Parzen Estimator
    max_evals=50,
    trials=trials
)

print(f"Best hyperparameters: {best}")
```

## Built-In Lightning Tuning

### Auto Learning Rate Finder

```python
class LitModel(L.LightningModule):
    def __init__(self, lr=1e-3):
        super().__init__()
        self.lr = lr
        self.model = nn.Linear(10, 1)

    def configure_optimizers(self):
        return torch.optim.Adam(self.parameters(), lr=self.lr)

    def training_step(self, batch, batch_idx):
        loss = self.model(batch).mean()
        return loss

# Find optimal learning rate
model = LitModel()
trainer = L.Trainer(auto_lr_find=True)

# This runs LR finder before training
trainer.tune(model, train_loader)

# Or manually
from lightning.pytorch.tuner import Tuner
tuner = Tuner(trainer)
lr_finder = tuner.lr_find(model, train_loader)

# Plot results
fig = lr_finder.plot(suggest=True)
fig.show()

# Get suggested LR
suggested_lr = lr_finder.suggestion()
print(f"Suggested LR: {suggested_lr}")

# Update model
model.lr = suggested_lr

# Train with optimal LR
trainer.fit(model, train_loader)
```

### Auto Batch Size Finder

```python
class LitModel(L.LightningModule):
    def __init__(self, batch_size=32):
        super().__init__()
        self.batch_size = batch_size
        self.model = nn.Linear(10, 1)

    def train_dataloader(self):
        return DataLoader(dataset, batch_size=self.batch_size)

model = LitModel()
trainer = L.Trainer(auto_scale_batch_size='binsearch')

# Find optimal batch size
trainer.tune(model)

print(f"Optimal batch size: {model.batch_size}")

# Train with optimal batch size
trainer.fit(model, train_loader)
```

## Advanced Tuning Strategies

### 1. Multi-Fidelity Optimization (Successive Halving)

```python
from ray.tune.schedulers import ASHAScheduler

# ASHA: Asynchronous Successive Halving Algorithm
scheduler = ASHAScheduler(
    max_t=100,  # Max epochs
    grace_period=10,  # Min epochs before stopping
    reduction_factor=2  # Halve resources each round
)

analysis = tune.run(
    train_fn,
    config=config,
    num_samples=64,
    scheduler=scheduler,
    resources_per_trial={"gpu": 1}
)
```

**How it works**:
- Start 64 trials
- After 10 epochs, stop bottom 50% (32 trials remain)
- After 20 epochs, stop bottom 50% (16 trials remain)
- After 40 epochs, stop bottom 50% (8 trials remain)
- After 80 epochs, stop bottom 50% (4 trials remain)
- Run remaining 4 trials to completion (100 epochs)

### 2. Bayesian Optimization

```python
from ray.tune.search.bayesopt import BayesOptSearch

search = BayesOptSearch(
    metric="val_loss",
    mode="min"
)

analysis = tune.run(
    train_fn,
    config=config,
    num_samples=50,
    search_alg=search,
    resources_per_trial={"gpu": 1}
)
```

### 3. Grid Search

```python
from ray import tune

# Exhaustive grid search
config = {
    "lr": tune.grid_search([1e-5, 1e-4, 1e-3, 1e-2]),
    "batch_size": tune.grid_search([16, 32, 64, 128]),
    "optimizer": tune.grid_search(['adam', 'sgd', 'adamw'])
}

# Total trials: 4 × 4 × 3 = 48
analysis = tune.run(train_fn, config=config)
```

### 4. Random Search

```python
config = {
    "lr": tune.loguniform(1e-5, 1e-1),
    "batch_size": tune.choice([16, 32, 64, 128]),
    "dropout": tune.uniform(0.0, 0.5),
    "hidden_size": tune.randint(64, 512)
}

# Random sampling
analysis = tune.run(
    train_fn,
    config=config,
    num_samples=100  # 100 random samples
)
```

## Best Practices

### 1. Start Simple

```python
# Phase 1: Coarse search (fast)
coarse_config = {
    "lr": tune.loguniform(1e-5, 1e-1),
    "batch_size": tune.choice([32, 64])
}
coarse_analysis = tune.run(train_fn, config=coarse_config, num_samples=10, max_epochs=5)

# Phase 2: Fine-tune around best (slow)
best_lr = coarse_analysis.best_config["lr"]
fine_config = {
    "lr": tune.uniform(best_lr * 0.5, best_lr * 2),
    "batch_size": tune.choice([16, 32, 64, 128])
}
fine_analysis = tune.run(train_fn, config=fine_config, num_samples=20, max_epochs=20)
```

### 2. Use Checkpointing

```python
def train_fn(config, checkpoint_dir=None):
    model = LitModel(lr=config["lr"])

    trainer = L.Trainer(
        max_epochs=100,
        callbacks=[
            TuneReportCheckpointCallback(
                metrics={"loss": "val_loss"},
                filename="checkpoint",
                on="validation_end"
            )
        ]
    )

    # Resume from checkpoint if exists
    ckpt_path = None
    if checkpoint_dir:
        ckpt_path = os.path.join(checkpoint_dir, "checkpoint")

    trainer.fit(model, train_loader, val_loader, ckpt_path=ckpt_path)
```

### 3. Monitor Resource Usage

```python
import GPUtil

def train_fn(config):
    # Before training
    GPUs = GPUtil.getGPUs()
    print(f"GPU memory before: {GPUs[0].memoryUsed} MB")

    # Train
    model = LitModel(lr=config["lr"], batch_size=config["batch_size"])
    trainer.fit(model, train_loader)

    # After training
    GPUs = GPUtil.getGPUs()
    print(f"GPU memory after: {GPUs[0].memoryUsed} MB")
```

## Common Issues

### Issue: Trials Running Out of Memory

**Solution**: Reduce concurrent trials or batch size
```python
analysis = tune.run(
    train_fn,
    config=config,
    resources_per_trial={"gpu": 0.5},  # 2 trials per GPU
    max_concurrent_trials=2  # Limit concurrent trials
)
```

### Issue: Slow Hyperparameter Search

**Solution**: Use early stopping scheduler
```python
from ray.tune.schedulers import ASHAScheduler

scheduler = ASHAScheduler(
    max_t=100,
    grace_period=5,  # Stop bad trials after 5 epochs
    reduction_factor=3
)
```

### Issue: Can't Reproduce Best Trial

**Solution**: Set seeds in training function
```python
def train_fn(config):
    L.seed_everything(42, workers=True)
    # Rest of training...
```

## Resources

- Ray Tune + Lightning: https://docs.ray.io/en/latest/tune/examples/tune-pytorch-lightning.html
- Optuna: https://optuna.readthedocs.io/
- WandB Sweeps: https://docs.wandb.ai/guides/sweeps
- Lightning Tuner: https://lightning.ai/docs/pytorch/stable/tuning.html




### Distributed

# PyTorch Lightning Distributed Training

## Distributed Strategies

Lightning supports multiple distributed strategies with a single parameter change.

### 1. DDP (DistributedDataParallel)

**Default strategy for multi-GPU**:

```python
# Automatic DDP on all available GPUs
trainer = L.Trainer(accelerator='gpu', devices=4, strategy='ddp')

# Or auto-detect
trainer = L.Trainer(accelerator='gpu', devices='auto')
```

**How DDP works**:
- Replicates model on each GPU
- Each GPU processes different batch
- Gradients all-reduced across GPUs
- Model weights synchronized

**Launch**:
```bash
# Lightning handles spawning processes automatically
python train.py
```

**DDP Configuration**:
```python
from lightning.pytorch.strategies import DDPStrategy

strategy = DDPStrategy(
    find_unused_parameters=False,  # Set True if model has unused params
    gradient_as_bucket_view=True,  # Memory optimization
    static_graph=False,  # Set True if graph doesn't change
)

trainer = L.Trainer(strategy=strategy)
```

### 2. FSDP (Fully Sharded Data Parallel)

**For large models (7B+ parameters)**:

```python
from lightning.pytorch.strategies import FSDPStrategy

strategy = FSDPStrategy(
    sharding_strategy="FULL_SHARD",  # ZeRO-3 equivalent
    activation_checkpointing=None,   # Or specify layer types
    cpu_offload=False,               # CPU offload for memory
)

trainer = L.Trainer(
    accelerator='gpu',
    devices=8,
    strategy=strategy,
    precision='bf16'  # Recommended with FSDP
)

trainer.fit(model, train_loader)
```

**FSDP Sharding Strategies**:
```python
# FULL_SHARD (most memory efficient, equivalent to ZeRO-3)
strategy = FSDPStrategy(sharding_strategy="FULL_SHARD")

# SHARD_GRAD_OP (less memory efficient, equivalent to ZeRO-2)
strategy = FSDPStrategy(sharding_strategy="SHARD_GRAD_OP")

# NO_SHARD (no sharding, like DDP)
strategy = FSDPStrategy(sharding_strategy="NO_SHARD")
```

**Auto-wrap policy** (wrap transformer blocks):
```python
from torch.distributed.fsdp.wrap import transformer_auto_wrap_policy
from transformers.models.gpt2.modeling_gpt2 import GPT2Block
import functools

auto_wrap_policy = functools.partial(
    transformer_auto_wrap_policy,
    transformer_layer_cls={GPT2Block}
)

strategy = FSDPStrategy(
    auto_wrap_policy=auto_wrap_policy,
    activation_checkpointing_policy={GPT2Block}  # Checkpoint these blocks
)
```

### 3. DeepSpeed

**For massive models (70B+ parameters)**:

```python
from lightning.pytorch.strategies import DeepSpeedStrategy

# DeepSpeed ZeRO-3 with CPU offload
strategy = DeepSpeedStrategy(
    stage=3,                       # ZeRO-3
    offload_optimizer=True,        # CPU offload optimizer
    offload_parameters=True,       # CPU offload parameters
    cpu_checkpointing=True,        # Checkpoint to CPU
)

trainer = L.Trainer(
    accelerator='gpu',
    devices=8,
    strategy=strategy,
    precision='bf16'
)

trainer.fit(model, train_loader)
```

**DeepSpeed configuration file**:
```json
{
  "train_batch_size": "auto",
  "train_micro_batch_size_per_gpu": "auto",
  "gradient_accumulation_steps": "auto",
  "zero_optimization": {
    "stage": 3,
    "offload_optimizer": {
      "device": "cpu",
      "pin_memory": true
    },
    "offload_param": {
      "device": "cpu",
      "pin_memory": true
    },
    "overlap_comm": true,
    "contiguous_gradients": true,
    "reduce_bucket_size": 5e8,
    "stage3_prefetch_bucket_size": 5e8,
    "stage3_param_persistence_threshold": 1e6
  },
  "bf16": {
    "enabled": true
  }
}
```

**Use config file**:
```python
strategy = DeepSpeedStrategy(config='deepspeed_config.json')
trainer = L.Trainer(strategy=strategy)
```

### 4. DDP Spawn

**Windows-compatible DDP**:

```python
# Use when DDP doesn't work (e.g., Windows, Jupyter)
trainer = L.Trainer(
    accelerator='gpu',
    devices=2,
    strategy='ddp_spawn'  # Spawns new processes
)
```

**Note**: Slower than DDP due to process spawning overhead

## Multi-Node Training

### Setup Multi-Node Cluster

**Node 0 (master)**:
```bash
export MASTER_ADDR=192.168.1.100
export MASTER_PORT=12355
export WORLD_SIZE=16  # 2 nodes × 8 GPUs
export NODE_RANK=0

python train.py
```

**Node 1 (worker)**:
```bash
export MASTER_ADDR=192.168.1.100
export MASTER_PORT=12355
export WORLD_SIZE=16
export NODE_RANK=1

python train.py
```

**Training script**:
```python
trainer = L.Trainer(
    accelerator='gpu',
    devices=8,              # GPUs per node
    num_nodes=2,            # Total nodes
    strategy='ddp'
)

trainer.fit(model, train_loader)
```

### SLURM Integration

**SLURM job script**:
```bash
#!/bin/bash
#SBATCH --nodes=4
#SBATCH --ntasks-per-node=8
#SBATCH --gres=gpu:8
#SBATCH --time=24:00:00

# Lightning auto-detects SLURM environment
srun python train.py
```

**Training script** (no changes needed):
```python
# Lightning automatically reads SLURM environment variables
trainer = L.Trainer(
    accelerator='gpu',
    devices=8,
    num_nodes=4,  # From SBATCH --nodes
    strategy='ddp'
)
```

### Kubernetes (KubeFlow)

**Training script**:
```python
import os

# Lightning auto-detects Kubernetes
trainer = L.Trainer(
    accelerator='gpu',
    devices=int(os.getenv('WORLD_SIZE', 1)),
    strategy='ddp'
)
```

## Mixed Precision Training

### BF16 (A100/H100)

```python
trainer = L.Trainer(
    precision='bf16',  # Or 'bf16-mixed'
    accelerator='gpu'
)
```

**Advantages**:
- No gradient scaler needed
- Same dynamic range as FP32
- 2× speedup, 50% memory reduction

### FP16 (V100, older GPUs)

```python
trainer = L.Trainer(
    precision='16-mixed',  # Or just '16'
    accelerator='gpu'
)
```

**Automatic gradient scaling** handled by Lightning

### FP8 (H100)

```python
# Requires transformer_engine
# pip install transformer-engine[pytorch]

trainer = L.Trainer(
    precision='transformer-engine',
    accelerator='gpu'
)
```

**Benefits**: 2× faster than BF16 on H100

## Gradient Accumulation

**Simulate larger batch size**:

```python
trainer = L.Trainer(
    accumulate_grad_batches=4,  # Accumulate 4 batches
    precision='bf16'
)

# Effective batch = batch_size × accumulate_grad_batches × num_gpus
# Example: 32 × 4 × 8 = 1024
```

**Dynamic accumulation**:
```python
# Accumulate more early in training
trainer = L.Trainer(
    accumulate_grad_batches={
        0: 8,   # Epochs 0-4: accumulate 8
        5: 4,   # Epochs 5-9: accumulate 4
        10: 2   # Epochs 10+: accumulate 2
    }
)
```

## Checkpointing in Distributed

### Save Checkpoint

```python
from lightning.pytorch.callbacks import ModelCheckpoint

# Only rank 0 saves by default
checkpoint = ModelCheckpoint(
    dirpath='checkpoints/',
    filename='model-{epoch:02d}',
    save_top_k=3
)

trainer = L.Trainer(callbacks=[checkpoint], strategy='ddp')
trainer.fit(model, train_loader)
```

**Manual save**:
```python
class MyModel(L.LightningModule):
    def training_step(self, batch, batch_idx):
        # Training...
        loss = ...

        # Save every 1000 steps (only rank 0)
        if batch_idx % 1000 == 0 and self.trainer.is_global_zero:
            self.trainer.save_checkpoint(f'checkpoint_step_{batch_idx}.ckpt')

        return loss
```

### Load Checkpoint

```python
# Resume training
trainer = L.Trainer(strategy='ddp')
trainer.fit(model, train_loader, ckpt_path='checkpoints/last.ckpt')

# Load for inference
model = MyModel.load_from_checkpoint('checkpoints/best.ckpt')
model.eval()
```

## Strategy Comparison

| Strategy | Memory Efficiency | Speed | Use Case |
|----------|------------------|-------|----------|
| DDP | Low | Fast | Small models (<7B), single node |
| FSDP | High | Medium | Large models (7-70B) |
| DeepSpeed ZeRO-2 | Medium | Fast | Medium models (1-13B) |
| DeepSpeed ZeRO-3 | Very High | Slower | Massive models (70B+) |
| DDP Spawn | Low | Slow | Windows, debugging |

## Best Practices

### 1. Choose Right Strategy

```python
# Model size guide
if model_params < 1e9:  # <1B
    strategy = 'ddp'
elif model_params < 7e9:  # 1-7B
    strategy = 'ddp' or DeepSpeedStrategy(stage=2)
elif model_params < 70e9:  # 7-70B
    strategy = FSDPStrategy(sharding_strategy="FULL_SHARD")
else:  # 70B+
    strategy = DeepSpeedStrategy(stage=3, offload_optimizer=True)

trainer = L.Trainer(strategy=strategy)
```

### 2. Avoid Sync Issues

```python
class MyModel(L.LightningModule):
    def training_step(self, batch, batch_idx):
        # WRONG: This runs on all GPUs independently
        if batch_idx % 100 == 0:
            self.log_something()  # Logged 8 times on 8 GPUs!

        # CORRECT: Use is_global_zero
        if batch_idx % 100 == 0 and self.trainer.is_global_zero:
            self.log_something()  # Logged once

        loss = ...
        return loss
```

### 3. Efficient Data Loading

```python
from torch.utils.data import DataLoader, DistributedSampler

# Lightning handles DistributedSampler automatically
train_loader = DataLoader(
    dataset,
    batch_size=32,
    num_workers=4,  # 4 workers per GPU
    pin_memory=True,
    persistent_workers=True
)

# Lightning automatically wraps with DistributedSampler in DDP
trainer.fit(model, train_loader)
```

### 4. Reduce Communication Overhead

```python
from lightning.pytorch.strategies import DDPStrategy

strategy = DDPStrategy(
    gradient_as_bucket_view=True,  # Reduce memory copies
    static_graph=True,  # If model graph doesn't change (faster)
)

trainer = L.Trainer(strategy=strategy)
```

## Common Issues

### Issue: NCCL Timeout

**Symptom**: Training hangs with `NCCL timeout` error

**Solution 1**: Increase timeout
```bash
export NCCL_TIMEOUT=3600  # 1 hour
python train.py
```

**Solution 2**: Check network
```bash
# Test inter-node communication
nvidia-smi nvlink -s

# Verify all nodes can ping each other
ping <node-2-ip>
```

### Issue: OOM with FSDP

**Solution**: Enable CPU offload
```python
strategy = FSDPStrategy(
    sharding_strategy="FULL_SHARD",
    cpu_offload=True  # Offload to CPU
)
```

### Issue: Different Results with DDP

**Cause**: Different random seeds per GPU

**Solution**: Set seed in LightningModule
```python
class MyModel(L.LightningModule):
    def __init__(self):
        super().__init__()
        L.seed_everything(42, workers=True)  # Same seed everywhere
```

### Issue: DeepSpeed Config Errors

**Solution**: Use Lightning's auto config
```python
strategy = DeepSpeedStrategy(
    stage=3,
    # Don't specify config file, Lightning generates automatically
)
```

## Resources

- Distributed strategies: https://lightning.ai/docs/pytorch/stable/accelerators/gpu_intermediate.html
- FSDP guide: https://lightning.ai/docs/pytorch/stable/advanced/model_parallel/fsdp.html
- DeepSpeed: https://lightning.ai/docs/pytorch/stable/advanced/model_parallel/deepspeed.html
- Multi-node: https://lightning.ai/docs/pytorch/stable/clouds/cluster.html




---

## 🚀 Usage

**Reference this template:** `@skill-pytorch-lightning.md`


**Platform-specific:**
- **GitHub Copilot**: Add to `.github/copilot-instructions.md`
- **Augment Code**: Use `aug context add` command
- **Cursor/Windsurf**: Reference in chat with `@filename`
- **Claude**: Add to Project Knowledge
- **ChatGPT**: Add to Custom GPT configuration
