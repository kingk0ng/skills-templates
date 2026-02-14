---
id: skill-axolotl
type: skill
name: axolotl
description: Expert guidance for fine-tuning LLMs with Axolotl - YAML configs, 100+
  models, LoRA/QLoRA, DPO/KTO/ORPO/GRPO, multimodal support
category: ai-research
complexity: medium
keywords:
- api
- github
- optimization
- python
capabilities: []
token_estimate: 734
has_references: true
reference_count: 4
---

<!-- Converted from Claude Skill Template -->
<!-- Complexity: Medium -->
<!-- Estimated Tokens: ~734 -->
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




# axolotl

> Expert guidance for fine-tuning LLMs with Axolotl - YAML configs, 100+ models, LoRA/QLoRA, DPO/KTO/ORPO/GRPO, multimodal support

# Axolotl Skill

Comprehensive assistance with axolotl development, generated from official documentation.

## When to Use This Skill

This skill should be triggered when:
- Working with axolotl
- Asking about axolotl features or APIs
- Implementing axolotl solutions
- Debugging axolotl code
- Learning axolotl best practices

## Quick Reference

### Common Patterns

**Pattern 1:** To validate that acceptable data transfer speeds exist for your training job, running NCCL Tests can help pinpoint bottlenecks, for example:

```
./build/all_reduce_perf -b 8 -e 128M -f 2 -g 3
```

**Pattern 2:** Configure your model to use FSDP in the Axolotl yaml. For example:

```
fsdp_version: 2
fsdp_config:
  offload_params: true
  state_dict_type: FULL_STATE_DICT
  auto_wrap_policy: TRANSFORMER_BASED_WRAP
  transformer_layer_cls_to_wrap: LlamaDecoderLayer
  reshard_after_forward: true
```

**Pattern 3:** The context_parallel_size should be a divisor of the total number of GPUs. For example:

```
context_parallel_size
```

**Pattern 4:** For example: - With 8 GPUs and no sequence parallelism: 8 different batches processed per step - With 8 GPUs and context_parallel_size=4: Only 2 different batches processed per step (each split across 4 GPUs) - If your per-GPU micro_batch_size is 2, the global batch size decreases from 16 to 4

```
context_parallel_size=4
```

**Pattern 5:** Setting save_compressed: true in your configuration enables saving models in a compressed format, which: - Reduces disk space usage by approximately 40% - Maintains compatibility with vLLM for accelerated inference - Maintains compatibility with llmcompressor for further optimization (example: quantization)

```
save_compressed: true
```

**Pattern 6:** Note It is not necessary to place your integration in the integrations folder. It can be in any location, so long as it’s installed in a package in your python env. See this repo for an example: https://github.com/axolotl-ai-cloud/diff-transformer

```
integrations
```

**Pattern 7:** Handle both single-example and batched data. - single example: sample[‘input_ids’] is a list[int] - batched data: sample[‘input_ids’] is a list[list[int]]

```
utils.trainer.drop_long_seq(sample, sequence_len=2048, min_sequence_len=2)
```

### Example Code Patterns

**Example 1** (python):
```python
cli.cloud.modal_.ModalCloud(config, app=None)
```

**Example 2** (python):
```python
cli.cloud.modal_.run_cmd(cmd, run_folder, volumes=None)
```

**Example 3** (python):
```python
core.trainers.base.AxolotlTrainer(
    *_args,
    bench_data_collator=None,
    eval_data_collator=None,
    dataset_tags=None,
    **kwargs,
)
```

**Example 4** (python):
```python
core.trainers.base.AxolotlTrainer.log(logs, start_time=None)
```

**Example 5** (python):
```python
prompt_strategies.input_output.RawInputOutputPrompter()
```

## Reference Files

This skill includes comprehensive documentation in `references/`:

- **api.md** - Api documentation
- **dataset-formats.md** - Dataset-Formats documentation
- **other.md** - Other documentation

Use `view` to read specific reference files when detailed information is needed.

## Working with This Skill

### For Beginners
Start with the getting_started or tutorials reference files for foundational concepts.

### For Specific Features
Use the appropriate category reference file (api, guides, etc.) for detailed information.

### For Code Examples
The quick reference section above contains common patterns extracted from the official docs.

## Resources

### references/
Organized documentation extracted from official sources. These files contain:
- Detailed explanations
- Code examples with language annotations
- Links to original documentation
- Table of contents for quick navigation

### scripts/
Add helper scripts here for common automation tasks.

### assets/
Add templates, boilerplate, or example projects here.

## Notes

- This skill was automatically generated from official documentation
- Reference files preserve the structure and examples from source docs
- Code examples include language detection for better syntax highlighting
- Quick reference patterns are extracted from common usage examples in the docs

## Updating

To refresh this skill with updated documentation:
1. Re-run the scraper with the same configuration
2. The skill will be rebuilt with the latest information




---


## 📚 Reference Materials


### Index

# Axolotl Documentation Index

## Categories

### Api
**File:** `api.md`
**Pages:** 150

### Dataset-Formats
**File:** `dataset-formats.md`
**Pages:** 9

### Other
**File:** `other.md`
**Pages:** 26




### Api

# Axolotl - Api

**Pages:** 150

---

## cli.cloud.modal_

**URL:** https://docs.axolotl.ai/docs/api/cli.cloud.modal_.html

**Contents:**
- cli.cloud.modal_
- Classes
  - ModalCloud
- Functions
  - run_cmd

Modal Cloud support from CLI

Modal Cloud implementation.

Run a command inside a folder, with Modal Volume reloading before and commit on success.

**Examples:**

Example 1 (python):
```python
cli.cloud.modal_.ModalCloud(config, app=None)
```

Example 2 (python):
```python
cli.cloud.modal_.run_cmd(cmd, run_folder, volumes=None)
```

---

## core.trainers.base

**URL:** https://docs.axolotl.ai/docs/api/core.trainers.base.html

**Contents:**
- core.trainers.base
- Classes
  - AxolotlTrainer
    - Methods
      - log
        - Parameters
      - push_to_hub
      - store_metrics
        - Parameters

Module for customized trainers

Extend the base Trainer for axolotl helpers

Log logs on the various objects watching training, including stored metrics.

Overwrite the push_to_hub method in order to force-add the tags when pushing the model on the Hub. Please refer to ~transformers.Trainer.push_to_hub for more details.

Store metrics with specified reduction type.

**Examples:**

Example 1 (python):
```python
core.trainers.base.AxolotlTrainer(
    *_args,
    bench_data_collator=None,
    eval_data_collator=None,
    dataset_tags=None,
    **kwargs,
)
```

Example 2 (python):
```python
core.trainers.base.AxolotlTrainer.log(logs, start_time=None)
```

Example 3 (python):
```python
core.trainers.base.AxolotlTrainer.push_to_hub(*args, **kwargs)
```

Example 4 (python):
```python
core.trainers.base.AxolotlTrainer.store_metrics(
    metrics,
    train_eval='train',
    reduction='mean',
)
```

---

## prompt_strategies.input_output

**URL:** https://docs.axolotl.ai/docs/api/prompt_strategies.input_output.html

**Contents:**
- prompt_strategies.input_output
- Classes
  - RawInputOutputPrompter
  - RawInputOutputStrategy

prompt_strategies.input_output

Module for plain input/output prompt pairs

prompter for raw i/o data

Prompt Strategy class for input/output pairs

**Examples:**

Example 1 (python):
```python
prompt_strategies.input_output.RawInputOutputPrompter()
```

Example 2 (python):
```python
prompt_strategies.input_output.RawInputOutputStrategy(
    *args,
    eos_token=None,
    **kwargs,
)
```

---

## prompt_strategies.completion

**URL:** https://docs.axolotl.ai/docs/api/prompt_strategies.completion.html

**Contents:**
- prompt_strategies.completion
- Classes
  - CompletionPromptTokenizingStrategy
  - CompletionPrompter

prompt_strategies.completion

Basic completion text

Tokenizing strategy for Completion prompts.

Prompter for completion

**Examples:**

Example 1 (python):
```python
prompt_strategies.completion.CompletionPromptTokenizingStrategy(
    *args,
    max_length=None,
    **kwargs,
)
```

Example 2 (python):
```python
prompt_strategies.completion.CompletionPrompter()
```

---

## utils.collators.core

**URL:** https://docs.axolotl.ai/docs/api/utils.collators.core.html

**Contents:**
- utils.collators.core

basic shared collator constants

---

## monkeypatch.data.batch_dataset_fetcher

**URL:** https://docs.axolotl.ai/docs/api/monkeypatch.data.batch_dataset_fetcher.html

**Contents:**
- monkeypatch.data.batch_dataset_fetcher
- Functions
  - apply_multipack_dataloader_patch
  - patch_fetchers
  - patched_worker_loop
  - remove_multipack_dataloader_patch

monkeypatch.data.batch_dataset_fetcher

Monkey patches for the dataset fetcher to handle batches of packed indexes.

This patch allows DataLoader to correctly process batches that contain multiple bins of packed sequences.

Apply patches to PyTorch’s DataLoader components.

Worker loop that ensures patches are applied in worker processes.

Remove the monkeypatch and restore original PyTorch DataLoader behavior.

**Examples:**

Example 1 (python):
```python
monkeypatch.data.batch_dataset_fetcher.apply_multipack_dataloader_patch()
```

Example 2 (python):
```python
monkeypatch.data.batch_dataset_fetcher.patch_fetchers()
```

Example 3 (python):
```python
monkeypatch.data.batch_dataset_fetcher.patched_worker_loop(*args, **kwargs)
```

Example 4 (python):
```python
monkeypatch.data.batch_dataset_fetcher.remove_multipack_dataloader_patch()
```

---

## core.datasets.chat

**URL:** https://docs.axolotl.ai/docs/api/core.datasets.chat.html

**Contents:**
- core.datasets.chat
- Classes
  - TokenizedChatDataset

Tokenized chat dataset

**Examples:**

Example 1 (python):
```python
core.datasets.chat.TokenizedChatDataset(
    data,
    model_transform,
    *args,
    message_transform=None,
    formatter=None,
    process_count=None,
    keep_in_memory=False,
    **kwargs,
)
```

---

## utils.freeze

**URL:** https://docs.axolotl.ai/docs/api/utils.freeze.html

**Contents:**
- utils.freeze
- Classes
  - LayerNamePattern
    - Methods
      - match
- Functions
  - freeze_layers_except

module to freeze/unfreeze parameters by name

Represents a regex pattern for layer names, potentially including a parameter index range.

Checks if the given layer name matches the regex pattern.

Parameters: - name (str): The layer name to check.

Returns: - bool: True if the layer name matches the pattern, False otherwise.

Freezes all layers of the given model except for the layers that match given regex patterns. Periods in the patterns are treated as literal periods, not as wildcard characters.

Parameters: - model (nn.Module): The PyTorch model to be modified. - regex_patterns (list of str): List of regex patterns to match layer names to keep unfrozen. Note that you cannot use a dot as a wildcard character in the patterns since it is reserved for separating layer names. Also, to match the entire layer name, the pattern should start with “^” and end with “\(", otherwise it will match any part of the layer name. The range pattern part is optional and it is not compiled as a regex pattern which means you must put "\)” before the range pattern if you want to match the entire layer name. E.g., [“^model.embed_tokens.weight\([:32000]", "layers.2[0-9]+.block_sparse_moe.gate.[a-z]+\)”]

Returns: None; the model is modified in place.

**Examples:**

Example 1 (python):
```python
utils.freeze.LayerNamePattern(pattern)
```

Example 2 (python):
```python
utils.freeze.LayerNamePattern.match(name)
```

Example 3 (python):
```python
utils.freeze.freeze_layers_except(model, regex_patterns)
```

---

## monkeypatch.unsloth_

**URL:** https://docs.axolotl.ai/docs/api/monkeypatch.unsloth_.html

**Contents:**
- monkeypatch.unsloth_

module for patching with unsloth optimizations

---

## utils.schemas.datasets

**URL:** https://docs.axolotl.ai/docs/api/utils.schemas.datasets.html

**Contents:**
- utils.schemas.datasets
- Classes
  - DPODataset
  - KTODataset
  - PretrainingDataset
  - SFTDataset
    - Methods
      - handle_legacy_message_fields
  - StepwiseSupervisedDataset
  - UserDefinedDPOType

utils.schemas.datasets

Pydantic models for datasets-related configuration

DPO configuration subset

KTO configuration subset

Pretraining dataset configuration subset

SFT configuration subset

Handle backwards compatibility between legacy message field mapping and new property mapping system.

Stepwise supervised dataset configuration subset

User defined typing for DPO

User defined typing for KTO

Structure for user defined prompt types

**Examples:**

Example 1 (python):
```python
utils.schemas.datasets.DPODataset()
```

Example 2 (python):
```python
utils.schemas.datasets.KTODataset()
```

Example 3 (python):
```python
utils.schemas.datasets.PretrainingDataset()
```

Example 4 (python):
```python
utils.schemas.datasets.SFTDataset()
```

---

## core.chat.format.llama3x

**URL:** https://docs.axolotl.ai/docs/api/core.chat.format.llama3x.html

**Contents:**
- core.chat.format.llama3x

core.chat.format.llama3x

Llama 3.x chat formatting functions for MessageContents

---

## datasets

**URL:** https://docs.axolotl.ai/docs/api/datasets.html

**Contents:**
- datasets
- Classes
  - TokenizedPromptDataset
    - Parameters

Module containing dataset functionality.

We want this to be a wrapper for an existing dataset that we have loaded. Lets use the concept of middlewares to wrap each dataset. We’ll use the collators later on to pad the datasets.

Dataset that returns tokenized prompts from a stream of text files.

**Examples:**

Example 1 (python):
```python
datasets.TokenizedPromptDataset(
    prompt_tokenizer,
    dataset,
    process_count=None,
    keep_in_memory=False,
    **kwargs,
)
```

---

## prompt_strategies.bradley_terry.llama3

**URL:** https://docs.axolotl.ai/docs/api/prompt_strategies.bradley_terry.llama3.html

**Contents:**
- prompt_strategies.bradley_terry.llama3
- Functions
  - icr

prompt_strategies.bradley_terry.llama3

chatml transforms for datasets with system, input, chosen, rejected to match llama3 chat template

chatml transforms for datasets with system, input, chosen, rejected ex. https://huggingface.co/datasets/argilla/distilabel-intel-orca-dpo-pairs

**Examples:**

Example 1 (python):
```python
prompt_strategies.bradley_terry.llama3.icr(cfg, **kwargs)
```

---

## common.datasets

**URL:** https://docs.axolotl.ai/docs/api/common.datasets.html

**Contents:**
- common.datasets
- Classes
  - TrainDatasetMeta
- Functions
  - load_datasets
    - Parameters
    - Returns
  - load_preference_datasets
    - Parameters
    - Returns

Dataset loading utilities.

Dataclass with fields for training and validation datasets and metadata.

Loads one or more training or evaluation datasets, calling axolotl.utils.data.prepare_datasets. Optionally, logs out debug information.

Loads one or more training or evaluation datasets for RL training using paired preference data, calling axolotl.utils.data.rl.prepare_preference_datasets. Optionally, logs out debug information.

Randomly sample num_samples samples with replacement from dataset.

**Examples:**

Example 1 (python):
```python
common.datasets.TrainDatasetMeta(
    train_dataset,
    eval_dataset=None,
    total_num_steps=None,
)
```

Example 2 (python):
```python
common.datasets.load_datasets(cfg, cli_args=None, debug=False)
```

Example 3 (python):
```python
common.datasets.load_preference_datasets(cfg, cli_args=None)
```

Example 4 (python):
```python
common.datasets.sample_dataset(dataset, num_samples)
```

---

## cli.train

**URL:** https://docs.axolotl.ai/docs/api/cli.train.html

**Contents:**
- cli.train
- Functions
  - do_cli
    - Parameters
  - do_train
    - Parameters

CLI to run training on a model.

Parses axolotl config, CLI args, and calls do_train.

Trains a transformers model by first loading the dataset(s) specified in the axolotl config, and then calling axolotl.train.train. Also runs the plugin manager’s post_train_unload once training completes.

**Examples:**

Example 1 (python):
```python
cli.train.do_cli(config=Path('examples/'), **kwargs)
```

Example 2 (python):
```python
cli.train.do_train(cfg, cli_args)
```

---

## cli.utils.fetch

**URL:** https://docs.axolotl.ai/docs/api/cli.utils.fetch.html

**Contents:**
- cli.utils.fetch
- Functions
  - fetch_from_github
    - Parameters

Utilities for axolotl fetch CLI command.

Sync files from a specific directory in the GitHub repository. Only downloads files that don’t exist locally or have changed.

**Examples:**

Example 1 (python):
```python
cli.utils.fetch.fetch_from_github(dir_prefix, dest_dir=None, max_workers=5)
```

---

## utils.tokenization

**URL:** https://docs.axolotl.ai/docs/api/utils.tokenization.html

**Contents:**
- utils.tokenization
- Functions
  - color_token_for_rl_debug
  - process_tokens_for_rl_debug

Module for tokenization utilities

Helper function to color tokens based on their type.

Helper function to process and color tokens.

**Examples:**

Example 1 (python):
```python
utils.tokenization.color_token_for_rl_debug(
    decoded_token,
    encoded_token,
    color,
    text_only,
)
```

Example 2 (python):
```python
utils.tokenization.process_tokens_for_rl_debug(
    tokens,
    color,
    tokenizer,
    text_only,
)
```

---

## core.trainers.grpo.sampler

**URL:** https://docs.axolotl.ai/docs/api/core.trainers.grpo.sampler.html

**Contents:**
- core.trainers.grpo.sampler
- Classes
  - SequenceParallelRepeatRandomSampler
    - Parameters
    - Methods
      - set_epoch
        - Parameters

core.trainers.grpo.sampler

Repeat random sampler (similar to the one implemented in https://github.com/huggingface/trl/blob/main/trl/trainer/grpo_trainer.py) that adds sequence parallelism functionality; i.e., duplicating data across ranks in the same sequence parallel group.

Sampler for GRPO training with sequence parallelism.

This sampler ensures: - Ranks in the same sequence parallel (SP) group receive identical data. - Each index is repeated multiple times for sampling different completions. - Entire batches are repeated for reuse in multiple updates. - Data is properly distributed across SP groups.

In the table below, the values represent dataset indices. Each SP group has context_parallel_size = 2 GPUs working together on the same data. There are 2 SP groups (SP0 and SP1), with world_size = 4 total GPUs.

grad_accum=2 ▲ ▲ 0 0 [0 0 0 1 1 1] [2 2 2 3 3 3] <- SP groups get different data ▼ | 0 1 [0 0 0 1 1 1] [2 2 2 3 3 3] <- Same data for each SP group GPU | | 1 2 [0 0 0 1 1 1] [2 2 2 3 3 3] <- Repeat same indices for iterations num_iterations=2 ▼ 1 3 [0 0 0 1 1 1] [2 2 2 3 3 3] <- When using gradient accumulation

Sets the epoch for this sampler.

**Examples:**

Example 1 (python):
```python
core.trainers.grpo.sampler.SequenceParallelRepeatRandomSampler(
    dataset,
    mini_repeat_count,
    world_size,
    rank,
    batch_size=1,
    repeat_count=1,
    context_parallel_size=1,
    shuffle=True,
    seed=0,
    drop_last=False,
)
```

Example 2 (unknown):
```unknown
Sequence Parallel Groups
                                |       SP0        |       SP1        |
                                |  GPU 0  |  GPU 1 |  GPU 2  |  GPU 3 |
            global_step  step    <---> mini_repeat_count=3
                                    <----------> batch_size=2 per SP group
```

Example 3 (unknown):
```unknown
2       4         [4 4 4  5 5 5]     [6 6 6  7 7 7]   <- New batch of data indices
                 2       5         [4 4 4  5 5 5]     [6 6 6  7 7 7]
                                    ...
```

Example 4 (python):
```python
core.trainers.grpo.sampler.SequenceParallelRepeatRandomSampler.set_epoch(epoch)
```

---

## evaluate

**URL:** https://docs.axolotl.ai/docs/api/evaluate.html

**Contents:**
- evaluate
- Functions
  - evaluate
    - Parameters
    - Returns
  - evaluate_dataset
    - Parameters
    - Returns

Module for evaluating models.

Evaluate a model on training and validation datasets.

Helper function to evaluate a single dataset.

**Examples:**

Example 1 (python):
```python
evaluate.evaluate(cfg, dataset_meta)
```

Example 2 (python):
```python
evaluate.evaluate_dataset(trainer, dataset, dataset_type, flash_optimum=False)
```

---

## utils.optimizers.adopt

**URL:** https://docs.axolotl.ai/docs/api/utils.optimizers.adopt.html

**Contents:**
- utils.optimizers.adopt
- Functions
  - adopt

utils.optimizers.adopt

Copied from https://github.com/iShohei220/adopt

ADOPT: Modified Adam Can Converge with Any β2 with the Optimal Rate (2024) Taniguchi, Shohei and Harada, Keno and Minegishi, Gouki and Oshima, Yuta and Jeong, Seong Cheol and Nagahara, Go and Iiyama, Tomoshi and Suzuki, Masahiro and Iwasawa, Yusuke and Matsuo, Yutaka

Functional API that performs ADOPT algorithm computation.

**Examples:**

Example 1 (python):
```python
utils.optimizers.adopt.adopt(
    params,
    grads,
    exp_avgs,
    exp_avg_sqs,
    state_steps,
    foreach=None,
    capturable=False,
    differentiable=False,
    fused=None,
    grad_scale=None,
    found_inf=None,
    has_complex=False,
    *,
    beta1,
    beta2,
    lr,
    clip_lambda,
    weight_decay,
    decouple,
    eps,
    maximize,
)
```

---

## prompt_tokenizers

**URL:** https://docs.axolotl.ai/docs/api/prompt_tokenizers.html

**Contents:**
- prompt_tokenizers
- Classes
  - AlpacaMultipleChoicePromptTokenizingStrategy
  - AlpacaPromptTokenizingStrategy
  - AlpacaReflectionPTStrategy
  - DatasetWrappingStrategy
  - GPTeacherPromptTokenizingStrategy
  - InstructionPromptTokenizingStrategy
  - InvalidDataException
  - JeopardyPromptTokenizingStrategy

Module containing PromptTokenizingStrategy and Prompter classes

Tokenizing strategy for Alpaca Multiple Choice prompts.

Tokenizing strategy for Alpaca prompts.

Tokenizing strategy for Alpaca Reflection prompts.

Abstract class for wrapping datasets for Chat Messages

Tokenizing strategy for GPTeacher prompts.

Tokenizing strategy for instruction-based prompts.

Exception raised when the data is invalid

Tokenizing strategy for Jeopardy prompts.

Tokenizing strategy for NomicGPT4All prompts.

Tokenizing strategy for OpenAssistant prompts.

Abstract class for tokenizing strategies

Tokenizing strategy for Reflection prompts.

Tokenizing strategy for SummarizeTLDR prompts.

Parses the tokenized prompt and append the tokenized input_ids, attention_mask and labels to the result

Returns the default values for the tokenize prompt function

**Examples:**

Example 1 (python):
```python
prompt_tokenizers.AlpacaMultipleChoicePromptTokenizingStrategy(
    prompter,
    tokenizer,
    train_on_inputs=False,
    sequence_len=2048,
)
```

Example 2 (python):
```python
prompt_tokenizers.AlpacaPromptTokenizingStrategy(
    prompter,
    tokenizer,
    train_on_inputs=False,
    sequence_len=2048,
)
```

Example 3 (python):
```python
prompt_tokenizers.AlpacaReflectionPTStrategy(
    prompter,
    tokenizer,
    train_on_inputs=False,
    sequence_len=2048,
)
```

Example 4 (python):
```python
prompt_tokenizers.DatasetWrappingStrategy()
```

---

## cli.art

**URL:** https://docs.axolotl.ai/docs/api/cli.art.html

**Contents:**
- cli.art
- Functions
  - print_axolotl_text_art

Axolotl ASCII logo utils.

Prints axolotl ASCII art.

**Examples:**

Example 1 (python):
```python
cli.art.print_axolotl_text_art()
```

---

## utils.callbacks.perplexity

**URL:** https://docs.axolotl.ai/docs/api/utils.callbacks.perplexity.html

**Contents:**
- utils.callbacks.perplexity
- Classes
  - Perplexity
    - Methods
      - compute

utils.callbacks.perplexity

callback to calculate perplexity as an evaluation metric.

Calculate perplexity as defined in https://huggingface.co/docs/transformers/en/perplexity. This is a custom variant that doesn’t re-tokenize the input or re-load the model.

Compute perplexity in a fixed length sliding window across the sequence.

**Examples:**

Example 1 (python):
```python
utils.callbacks.perplexity.Perplexity(tokenizer, max_seq_len, stride=512)
```

Example 2 (python):
```python
utils.callbacks.perplexity.Perplexity.compute(model, references=None)
```

---

## cli.utils.train

**URL:** https://docs.axolotl.ai/docs/api/cli.utils.train.html

**Contents:**
- cli.utils.train
- Functions
  - build_command
    - Parameters
    - Returns
  - generate_config_files
    - Parameters
  - launch_training

Utilities for axolotl train CLI command.

Build command list from base command and options.

Generate list of configuration files to process. Yields a tuple of the configuration file name and a boolean indicating whether this is a group of configurations (i.e., a sweep).

Execute training with the given configuration.

**Examples:**

Example 1 (python):
```python
cli.utils.train.build_command(base_cmd, options)
```

Example 2 (python):
```python
cli.utils.train.generate_config_files(config, sweep)
```

Example 3 (python):
```python
cli.utils.train.launch_training(
    cfg_file,
    launcher,
    cloud,
    kwargs,
    launcher_args=None,
    use_exec=False,
)
```

---

## cli.vllm_serve

**URL:** https://docs.axolotl.ai/docs/api/cli.vllm_serve.html

**Contents:**
- cli.vllm_serve
- Classes
  - AxolotlScriptArguments
- Functions
  - do_vllm_serve
    - Returns

CLI to start the vllm server for online RL

Additional arguments for the VLLM server

Starts the VLLM server for serving LLM models used for online RL

Args :param cfg: Parsed doct of the YAML config :param cli_args: dict of additional command-line arguments of type VllmServeCliArgs

**Examples:**

Example 1 (python):
```python
cli.vllm_serve.AxolotlScriptArguments(
    reasoning_parser='',
    enable_reasoning=None,
)
```

Example 2 (python):
```python
cli.vllm_serve.do_vllm_serve(config, cli_args)
```

---

## convert

**URL:** https://docs.axolotl.ai/docs/api/convert.html

**Contents:**
- convert
- Classes
  - FileReader
  - FileWriter
  - JsonParser
  - JsonToJsonlConverter
  - JsonlSerializer
  - StdoutWriter

Module containing File Reader, File Writer, Json Parser, and Jsonl Serializer classes

Reads a file and returns its contents as a string

Writes a string to a file

Parses a string as JSON and returns the result

Converts a JSON file to JSONL

Serializes a list of JSON objects into a JSONL string

Writes a string to stdout

**Examples:**

Example 1 (python):
```python
convert.FileReader()
```

Example 2 (python):
```python
convert.FileWriter(file_path)
```

Example 3 (python):
```python
convert.JsonParser()
```

Example 4 (python):
```python
convert.JsonToJsonlConverter(
    file_reader,
    file_writer,
    json_parser,
    jsonl_serializer,
)
```

---

## monkeypatch.utils

**URL:** https://docs.axolotl.ai/docs/api/monkeypatch.utils.html

**Contents:**
- monkeypatch.utils
- Functions
  - get_cu_seqlens
  - get_cu_seqlens_from_pos_ids
  - mask_2d_to_4d

Shared utils for the monkeypatches

generate a cumulative sequence length mask for flash attention using attn mask

generate a cumulative sequence length mask for flash attention using pos ids

Expands attention_mask from [bsz, seq_len] to [bsz, 1, tgt_seq_len, src_seq_len]. This expansion handles packed sequences so that sequences share the same attention mask integer value when they attend to each other within that sequence. This expansion transforms the mask to lower triangular form to prevent future peeking.

**Examples:**

Example 1 (python):
```python
monkeypatch.utils.get_cu_seqlens(attn_mask)
```

Example 2 (python):
```python
monkeypatch.utils.get_cu_seqlens_from_pos_ids(position_ids)
```

Example 3 (python):
```python
monkeypatch.utils.mask_2d_to_4d(mask, dtype, tgt_len=None)
```

---

## prompt_strategies.pygmalion

**URL:** https://docs.axolotl.ai/docs/api/prompt_strategies.pygmalion.html

**Contents:**
- prompt_strategies.pygmalion
- Classes
  - PygmalionPromptTokenizingStrategy
  - PygmalionPrompter

prompt_strategies.pygmalion

Module containing the PygmalionPromptTokenizingStrategy and PygmalionPrompter class

Tokenizing strategy for Pygmalion.

Prompter for Pygmalion.

**Examples:**

Example 1 (python):
```python
prompt_strategies.pygmalion.PygmalionPromptTokenizingStrategy(
    prompter,
    tokenizer,
    *args,
    **kwargs,
)
```

Example 2 (python):
```python
prompt_strategies.pygmalion.PygmalionPrompter(*args, **kwargs)
```

---

## utils.callbacks.mlflow_

**URL:** https://docs.axolotl.ai/docs/api/utils.callbacks.mlflow_.html

**Contents:**
- utils.callbacks.mlflow_
- Classes
  - SaveAxolotlConfigtoMlflowCallback

utils.callbacks.mlflow_

MLFlow module for trainer callbacks

Callback to save axolotl config to mlflow

**Examples:**

Example 1 (python):
```python
utils.callbacks.mlflow_.SaveAxolotlConfigtoMlflowCallback(axolotl_config_path)
```

---

## loaders.adapter

**URL:** https://docs.axolotl.ai/docs/api/loaders.adapter.html

**Contents:**
- loaders.adapter
- Functions
  - setup_quantized_meta_for_peft
  - setup_quantized_peft_meta_for_training

Adapter loading functionality, including LoRA / QLoRA and associated utils

Replaces quant_state.to with a dummy function to prevent PEFT from moving quant_state to meta device

Replaces dummy quant_state.to method with the original function to allow training to continue

**Examples:**

Example 1 (python):
```python
loaders.adapter.setup_quantized_meta_for_peft(model)
```

Example 2 (python):
```python
loaders.adapter.setup_quantized_peft_meta_for_training(model)
```

---

## cli.cloud.base

**URL:** https://docs.axolotl.ai/docs/api/cli.cloud.base.html

**Contents:**
- cli.cloud.base
- Classes
  - Cloud

base class for cloud platforms from cli

Abstract base class for cloud platforms.

**Examples:**

Example 1 (python):
```python
cli.cloud.base.Cloud()
```

---

## monkeypatch.llama_attn_hijack_flash

**URL:** https://docs.axolotl.ai/docs/api/monkeypatch.llama_attn_hijack_flash.html

**Contents:**
- monkeypatch.llama_attn_hijack_flash
- Functions
  - flashattn_forward_with_s2attn

monkeypatch.llama_attn_hijack_flash

Flash attention monkey patch for llama model

Input shape: Batch x Time x Channel

From: https://github.com/dvlab-research/LongLoRA/blob/main/llama_attn_replace.py

attention_mask: [bsz, q_len]

cu_seqlens will be ignored if provided max_seqlen will be ignored if provided

**Examples:**

Example 1 (python):
```python
monkeypatch.llama_attn_hijack_flash.flashattn_forward_with_s2attn(
    self,
    hidden_states,
    attention_mask=None,
    position_ids=None,
    past_key_value=None,
    output_attentions=False,
    use_cache=False,
    padding_mask=None,
    cu_seqlens=None,
    max_seqlen=None,
)
```

---

## monkeypatch.llama_patch_multipack

**URL:** https://docs.axolotl.ai/docs/api/monkeypatch.llama_patch_multipack.html

**Contents:**
- monkeypatch.llama_patch_multipack

monkeypatch.llama_patch_multipack

Patched LlamaAttention to use torch.nn.functional.scaled_dot_product_attention

---

## cli.inference

**URL:** https://docs.axolotl.ai/docs/api/cli.inference.html

**Contents:**
- cli.inference
- Functions
  - do_cli
    - Parameters
  - do_inference
    - Parameters
  - do_inference_gradio
    - Parameters
  - get_multi_line_input
    - Returns

CLI to run inference on a trained model.

Parses axolotl config, CLI args, and calls do_inference or do_inference_gradio.

Runs inference on the command line in a loop. User input is accepted, a chat template is (optionally) applied, and the model specified in the axolotl config is used to generate completions according to a default generation config.

Runs inference in a Gradio interface. User input is accepted, a chat template is (optionally) applied, and the model specified in the axolotl config is used to generate completions according to a default generation config.

Gets multi-line input from terminal.

**Examples:**

Example 1 (python):
```python
cli.inference.do_cli(config=Path('examples/'), gradio=False, **kwargs)
```

Example 2 (python):
```python
cli.inference.do_inference(cfg, cli_args)
```

Example 3 (python):
```python
cli.inference.do_inference_gradio(cfg, cli_args)
```

Example 4 (python):
```python
cli.inference.get_multi_line_input()
```

---

## loaders.tokenizer

**URL:** https://docs.axolotl.ai/docs/api/loaders.tokenizer.html

**Contents:**
- loaders.tokenizer
- Functions
  - load_tokenizer
  - modify_tokenizer_files
    - Parameters
    - Returns

Tokenizer loading functionality and associated utils

Load and configure the tokenizer based on the provided config.

Modify tokenizer files to replace added_tokens strings, save to output directory, and return the path to the modified tokenizer.

This only works with reserved tokens that were added to the tokenizer, not tokens already part of the vocab.

Ref: https://github.com/huggingface/transformers/issues/27974#issuecomment-1854188941

**Examples:**

Example 1 (python):
```python
loaders.tokenizer.load_tokenizer(cfg)
```

Example 2 (python):
```python
loaders.tokenizer.modify_tokenizer_files(
    tokenizer_path,
    token_mappings,
    output_dir,
)
```

---

## cli.utils.sweeps

**URL:** https://docs.axolotl.ai/docs/api/cli.utils.sweeps.html

**Contents:**
- cli.utils.sweeps
- Functions
  - generate_sweep_configs
    - Parameters
    - Returns
    - Example

Utilities for handling sweeps over configs for axolotl train CLI command

Recursively generates all possible configurations by applying sweeps to the base config.

sweeps_config = { ‘learning_rate’: [0.1, 0.01], ’_’: [ {‘load_in_8bit’: True, ‘adapter’: ‘lora’}, {‘load_in_4bit’: True, ‘adapter’: ‘qlora’} ] }

**Examples:**

Example 1 (python):
```python
cli.utils.sweeps.generate_sweep_configs(base_config, sweeps_config)
```

---

## prompt_strategies.dpo.chatml

**URL:** https://docs.axolotl.ai/docs/api/prompt_strategies.dpo.chatml.html

**Contents:**
- prompt_strategies.dpo.chatml
- Functions
  - argilla_chat
  - icr
  - intel
  - ultra

prompt_strategies.dpo.chatml

DPO strategies for chatml

for argilla/dpo-mix-7k conversations

chatml transforms for datasets with system, input, chosen, rejected ex. https://huggingface.co/datasets/argilla/distilabel-intel-orca-dpo-pairs

For Intel Orca DPO Pairs

for ultrafeedback binarized conversations

**Examples:**

Example 1 (python):
```python
prompt_strategies.dpo.chatml.argilla_chat(cfg, **kwargs)
```

Example 2 (python):
```python
prompt_strategies.dpo.chatml.icr(cfg, **kwargs)
```

Example 3 (python):
```python
prompt_strategies.dpo.chatml.intel(cfg, **kwargs)
```

Example 4 (python):
```python
prompt_strategies.dpo.chatml.ultra(cfg, **kwargs)
```

---

## cli.quantize

**URL:** https://docs.axolotl.ai/docs/api/cli.quantize.html

**Contents:**
- cli.quantize
- Functions
  - do_quantize
    - Parameters

CLI to post-training quantize a model using torchao

Quantizes a model’s model’s weights

**Examples:**

Example 1 (python):
```python
cli.quantize.do_quantize(config, cli_args)
```

---

## utils.dict

**URL:** https://docs.axolotl.ai/docs/api/utils.dict.html

**Contents:**
- utils.dict
- Classes
  - DictDefault
- Functions
  - remove_none_values

Module containing the DictDefault class

A Dict that returns None instead of returning empty Dict for missing keys.

Remove null from a dictionary-like obj or list. These can appear due to Dataset loading causing schema merge. See https://github.com/axolotl-ai-cloud/axolotl/pull/2909

**Examples:**

Example 1 (python):
```python
utils.dict.DictDefault()
```

Example 2 (python):
```python
utils.dict.remove_none_values(obj)
```

---

## API Reference

**URL:** https://docs.axolotl.ai/docs/api/

**Contents:**
- API Reference
- Core
- CLI
- Trainers
- Model Loading
- Mixins
- Context Managers
- Prompt Strategies
- Kernels
- Monkey Patches

Core functionality for training

Command-line interface

Training implementations

Functionality for loading and patching models, tokenizers, etc.

Mixin classes for augmenting trainers

Context managers for altering trainer behaviors

Prompt formatting strategies

Low-level performance optimizations

Runtime patches for model optimizations

Pydantic data models for Axolotl config

Third-party integrations and extensions

Common utilities and shared functionality

Custom model implementations

Data processing utilities

---

## monkeypatch.lora_kernels

**URL:** https://docs.axolotl.ai/docs/api/monkeypatch.lora_kernels.html

**Contents:**
- monkeypatch.lora_kernels
- Classes
  - FakeMLP
- Functions
  - apply_lora_kernel_patches
    - Parameters
    - Returns
    - Raises
    - Note
  - get_attention_cls_from_config

monkeypatch.lora_kernels

Module for patching custom LoRA Triton kernels and torch.autograd functions.

placeholder MLP for triton patching

Applies optimized Triton kernel patches to a PEFT model.

Patches a PEFT model with optimized implementations for MLP and attention computations. The optimizations include custom Triton kernels for activation functions and specialized autograd functions for LoRA computations.

The optimizations require LoRA adapters with no dropout and no bias terms. The function will skip patching if these conditions aren’t met.

Get the appropriate attention class by inspecting the model config. Uses dynamic import to support any model architecture that follows the standard transformers naming convention.

Get the layers of the model. Handles text-only and multimodal models.

Original implementation of output projection without optimizations.

Original implementation of QKV projection without optimizations.

Given an axolotl config, this method patches the inferred attention class forward pass with optimized LoRA implementations.

It modifies the attention class to use optimized QKV and output projections. The original implementation is preserved and can be restored if needed.

**Examples:**

Example 1 (python):
```python
monkeypatch.lora_kernels.FakeMLP(gate_proj, up_proj, down_proj)
```

Example 2 (python):
```python
monkeypatch.lora_kernels.apply_lora_kernel_patches(model, cfg)
```

Example 3 (python):
```python
monkeypatch.lora_kernels.get_attention_cls_from_config(cfg)
```

Example 4 (python):
```python
monkeypatch.lora_kernels.get_layers(model)
```

---

## monkeypatch.stablelm_attn_hijack_flash

**URL:** https://docs.axolotl.ai/docs/api/monkeypatch.stablelm_attn_hijack_flash.html

**Contents:**
- monkeypatch.stablelm_attn_hijack_flash
- Functions
  - repeat_kv
  - rotate_half

monkeypatch.stablelm_attn_hijack_flash

PyTorch StableLM Epoch model.

This is the equivalent of torch.repeat_interleave(x, dim=1, repeats=n_rep). The hidden states go from (batch, num_key_value_heads, seqlen, head_dim) to (batch, num_attention_heads, seqlen, head_dim)

Rotates half the hidden dims of the input.

**Examples:**

Example 1 (python):
```python
monkeypatch.stablelm_attn_hijack_flash.repeat_kv(hidden_states, n_rep)
```

Example 2 (python):
```python
monkeypatch.stablelm_attn_hijack_flash.rotate_half(x)
```

---

## core.trainers.mixins.rng_state_loader

**URL:** https://docs.axolotl.ai/docs/api/core.trainers.mixins.rng_state_loader.html

**Contents:**
- core.trainers.mixins.rng_state_loader
- Classes
  - RngLoaderMixin

core.trainers.mixins.rng_state_loader

Temporary fix/override for bug in resume from checkpoint

See https://github.com/huggingface/transformers/pull/37162

TODO: Remove when upstream added PR to release

mixin for method override to load RNG states from a checkpoint

**Examples:**

Example 1 (python):
```python
core.trainers.mixins.rng_state_loader.RngLoaderMixin()
```

---

## core.trainers.utils

**URL:** https://docs.axolotl.ai/docs/api/core.trainers.utils.html

**Contents:**
- core.trainers.utils

Utils for Axolotl trainers

---

## core.training_args

**URL:** https://docs.axolotl.ai/docs/api/core.training_args.html

**Contents:**
- core.training_args
- Classes
  - AxolotlCPOConfig
  - AxolotlKTOConfig
  - AxolotlORPOConfig
  - AxolotlPRMConfig
  - AxolotlRewardConfig
  - AxolotlTrainingArguments

extra axolotl specific training args

CPO config for CPO training

KTO config for KTO training

ORPO config for ORPO training

PRM config for PRM training

Reward config for Reward training

Training arguments for Causal trainer

This code is duplicated due to HF TrainingArguments not setting output_dir with a default value so it can’t be used as a mixin.

**Examples:**

Example 1 (python):
```python
core.training_args.AxolotlCPOConfig(simpo_gamma=None)
```

Example 2 (python):
```python
core.training_args.AxolotlKTOConfig()
```

Example 3 (python):
```python
core.training_args.AxolotlORPOConfig()
```

Example 4 (python):
```python
core.training_args.AxolotlPRMConfig()
```

---

## monkeypatch.btlm_attn_hijack_flash

**URL:** https://docs.axolotl.ai/docs/api/monkeypatch.btlm_attn_hijack_flash.html

**Contents:**
- monkeypatch.btlm_attn_hijack_flash

monkeypatch.btlm_attn_hijack_flash

Flash attention monkey patch for cerebras btlm model

---

## prompt_strategies.dpo.passthrough

**URL:** https://docs.axolotl.ai/docs/api/prompt_strategies.dpo.passthrough.html

**Contents:**
- prompt_strategies.dpo.passthrough

prompt_strategies.dpo.passthrough

DPO prompt strategies passthrough/zero-processing strategy

---

## kernels.swiglu

**URL:** https://docs.axolotl.ai/docs/api/kernels.swiglu.html

**Contents:**
- kernels.swiglu
- Functions
  - swiglu_backward
    - Parameters
    - Returns
  - swiglu_forward
    - Parameters
    - Returns

Module for definition of SwiGLU Triton kernels.

See “GLU Variants Improve Transformer” (https://arxiv.org/abs/2002.05202).

Credit to unsloth (https://unsloth.ai/) for inspiration for this implementation.

SwiGLU backward pass using in-place operations.

SwiGLU forward pass. Computes SwiGLU activation: x * sigmoid(x) * up, where x is the gate tensor.

**Examples:**

Example 1 (python):
```python
kernels.swiglu.swiglu_backward(grad_output, gate, up)
```

Example 2 (python):
```python
kernels.swiglu.swiglu_forward(gate, up)
```

---

## core.trainers.grpo.trainer

**URL:** https://docs.axolotl.ai/docs/api/core.trainers.grpo.trainer.html

**Contents:**
- core.trainers.grpo.trainer
- Classes
  - AxolotlGRPOSequenceParallelTrainer
    - Methods
      - get_train_dataloader
  - AxolotlGRPOTrainer

core.trainers.grpo.trainer

Axolotl GRPO trainers (with and without sequence parallelism handling)

Extend the base GRPOTrainer for sequence parallelism handling

Get dataloader for training

Extend the base GRPOTrainer for axolotl helpers

**Examples:**

Example 1 (python):
```python
core.trainers.grpo.trainer.AxolotlGRPOSequenceParallelTrainer(
    model,
    reward_funcs,
    args=None,
    train_dataset=None,
    eval_dataset=None,
    processing_class=None,
    reward_processing_classes=None,
    callbacks=None,
    optimizers=(None, None),
    peft_config=None,
    optimizer_cls_and_kwargs=None,
)
```

Example 2 (python):
```python
core.trainers.grpo.trainer.AxolotlGRPOSequenceParallelTrainer.get_train_dataloader(
)
```

Example 3 (python):
```python
core.trainers.grpo.trainer.AxolotlGRPOTrainer(*args, **kwargs)
```

---

## prompt_strategies.user_defined

**URL:** https://docs.axolotl.ai/docs/api/prompt_strategies.user_defined.html

**Contents:**
- prompt_strategies.user_defined
- Classes
  - UserDefinedDatasetConfig
  - UserDefinedPromptTokenizationStrategy

prompt_strategies.user_defined

User Defined prompts with configuration from the YML config

dataclass configuration representing a userdefined dataset type

Prompt Tokenization Strategy for user defined prompts

**Examples:**

Example 1 (python):
```python
prompt_strategies.user_defined.UserDefinedDatasetConfig(
    system_prompt='',
    field_system='system',
    field_instruction='instruction',
    field_input='input',
    field_output='output',
    format='{instruction} {input} ',
    no_input_format='{instruction} ',
    system_format='{system}',
)
```

Example 2 (python):
```python
prompt_strategies.user_defined.UserDefinedPromptTokenizationStrategy(
    prompter,
    tokenizer,
    train_on_inputs=False,
    sequence_len=2048,
)
```

---

## utils.schemas.training

**URL:** https://docs.axolotl.ai/docs/api/utils.schemas.training.html

**Contents:**
- utils.schemas.training
- Classes
  - HyperparametersConfig
  - JaggedLRConfig
  - LrGroup

utils.schemas.training

Pydantic models for training hyperparameters

Training hyperparams configuration subset

JaggedLR configuration subset, can be used w/ ReLoRA training

Custom learning rate group configuration

**Examples:**

Example 1 (python):
```python
utils.schemas.training.HyperparametersConfig()
```

Example 2 (python):
```python
utils.schemas.training.JaggedLRConfig()
```

Example 3 (python):
```python
utils.schemas.training.LrGroup()
```

---

## utils.quantization

**URL:** https://docs.axolotl.ai/docs/api/utils.quantization.html

**Contents:**
- utils.quantization
- Functions
  - convert_qat_model
  - get_quantization_config
    - Parameters
    - Returns
    - Raises
  - prepare_model_for_qat
    - Parameters
    - Raises

Utilities for quantization including QAT and PTQ using torchao.

This function converts a QAT model which has fake quantized layers back to the original model.

This function is used to build a post-training quantization config.

This function is used to prepare a model for QAT by swapping the model’s linear layers with fake quantized linear layers, and optionally the embedding weights with fake quantized embedding weights.

This function is used to quantize a model.

**Examples:**

Example 1 (python):
```python
utils.quantization.convert_qat_model(model, quantize_embedding=False)
```

Example 2 (python):
```python
utils.quantization.get_quantization_config(
    weight_dtype,
    activation_dtype=None,
    group_size=None,
)
```

Example 3 (python):
```python
utils.quantization.prepare_model_for_qat(
    model,
    weight_dtype,
    group_size=None,
    activation_dtype=None,
    quantize_embedding=False,
)
```

Example 4 (python):
```python
utils.quantization.quantize_model(
    model,
    weight_dtype,
    group_size=None,
    activation_dtype=None,
    quantize_embedding=None,
)
```

---

## logging_config

**URL:** https://docs.axolotl.ai/docs/api/logging_config.html

**Contents:**
- logging_config
- Classes
  - AxolotlLogger
  - AxolotlOrWarnErrorFilter
  - ColorfulFormatter
- Functions
  - configure_logging

Common logging module for axolotl.

Logger that applies filtering to non-axolotl loggers.

Allows ANY WARNING or higher (unless overridden by LOG_LEVEL). Allows axolotl.* at INFO or higher (unless overridden by AXOLOTL_LOG_LEVEL). Drops all other records (i.e. non-axolotl.INFO, DEBUG, etc. by default).

Formatter to add coloring to log messages by log type

Configure with default logging

**Examples:**

Example 1 (python):
```python
logging_config.AxolotlLogger(name, level=logging.NOTSET)
```

Example 2 (python):
```python
logging_config.AxolotlOrWarnErrorFilter(**kwargs)
```

Example 3 (python):
```python
logging_config.ColorfulFormatter()
```

Example 4 (python):
```python
logging_config.configure_logging()
```

---

## prompt_strategies.stepwise_supervised

**URL:** https://docs.axolotl.ai/docs/api/prompt_strategies.stepwise_supervised.html

**Contents:**
- prompt_strategies.stepwise_supervised
- Classes
  - StepwiseSupervisedPromptTokenizingStrategy

prompt_strategies.stepwise_supervised

Module for stepwise datasets, typically including a prompt and reasoning traces, and (optionally) per-step, or per-prompt-trace labels for reward modelling.

Tokenizing strategy for supervised stepwise datasets, typically used for COT-reasoning. These datasets should include the following columns: - prompt: the prompt text - completions: a list of n completion steps - labels: a list of n labels indicating the “correctness” of each step

**Examples:**

Example 1 (python):
```python
prompt_strategies.stepwise_supervised.StepwiseSupervisedPromptTokenizingStrategy(
    tokenizer,
    sequence_len=2048,
    step_separator='\n',
    max_completion_length=None,
    train_on_last_step_only=False,
)
```

---

## utils.schemas.model

**URL:** https://docs.axolotl.ai/docs/api/utils.schemas.model.html

**Contents:**
- utils.schemas.model
- Classes
  - ModelInputConfig
  - ModelOutputConfig
  - SpecialTokensConfig

Pydantic models for model input / output, etc. configuration

Model configuration subset

model save configuration subset

Special tokens configuration subset

**Examples:**

Example 1 (python):
```python
utils.schemas.model.ModelInputConfig()
```

Example 2 (python):
```python
utils.schemas.model.ModelOutputConfig()
```

Example 3 (python):
```python
utils.schemas.model.SpecialTokensConfig()
```

---

## utils.schemas.enums

**URL:** https://docs.axolotl.ai/docs/api/utils.schemas.enums.html

**Contents:**
- utils.schemas.enums
- Classes
  - ChatTemplate
  - CustomSupportedOptimizers
  - RLType
  - RingAttnFunc

Enums for Axolotl input config

Chat templates configuration subset

Custom supported optimizers

RL trainer type configuration subset

Enum class for supported ring-flash-attn implementations

**Examples:**

Example 1 (python):
```python
utils.schemas.enums.ChatTemplate()
```

Example 2 (python):
```python
utils.schemas.enums.CustomSupportedOptimizers()
```

Example 3 (python):
```python
utils.schemas.enums.RLType()
```

Example 4 (python):
```python
utils.schemas.enums.RingAttnFunc()
```

---

## core.trainers.trl

**URL:** https://docs.axolotl.ai/docs/api/core.trainers.trl.html

**Contents:**
- core.trainers.trl
- Classes
  - AxolotlCPOTrainer
  - AxolotlKTOTrainer
  - AxolotlORPOTrainer
  - AxolotlPRMTrainer
  - AxolotlRewardTrainer

Module for TRL RL trainers

Extend the base CPOTrainer for axolotl helpers

Extend the base KTOTrainer for axolotl helpers

Extend the base ORPOTrainer for axolotl helpers

Extend the base trl.PRMTrainer for axolotl helpers

Extend the base RewardTrainer for axolotl helpers

**Examples:**

Example 1 (python):
```python
core.trainers.trl.AxolotlCPOTrainer(*args, **kwargs)
```

Example 2 (python):
```python
core.trainers.trl.AxolotlKTOTrainer(*args, **kwargs)
```

Example 3 (python):
```python
core.trainers.trl.AxolotlORPOTrainer(*args, **kwargs)
```

Example 4 (python):
```python
core.trainers.trl.AxolotlPRMTrainer(*args, **kwargs)
```

---

## utils.schedulers

**URL:** https://docs.axolotl.ai/docs/api/utils.schedulers.html

**Contents:**
- utils.schedulers
- Classes
  - InterpolatingLogScheduler
  - JaggedLRRestartScheduler
  - RexLR
    - Parameters
- Functions
  - get_cosine_schedule_with_min_lr
    - Create a learning rate schedule which has
  - get_cosine_schedule_with_quadratic_warmup

Module for custom LRScheduler class

A scheduler that interpolates learning rates in a logarithmic fashion

Wraps another scheduler to apply per-lora-restart learning rate warmups.

Reflected Exponential (REX) learning rate scheduler.

Create a schedule with a learning rate that decreases following the values of the cosine function between the initial lr set in the optimizer to 0, after a warmup period during which it increases linearly between 0 and the initial lr set in the optimizer.

torch.optim.lr_scheduler.LambdaLR with the appropriate schedule.

Implementation of Continual Pre-Training of Large Language Models: How to (re)warm your model? (https://arxiv.org/pdf/2308.04014.pdf) Create a schedule with a learning rate that decreases following the values of the cosine function between the initial lr set in the optimizer to min_lr_ratio until num_training_steps * constant_lr_ratio, after constant_rate returns constant value of min_rate , after a warmup period during which it increases linearly between 0 and the initial lr set in the optimizer.

torch.optim.lr_scheduler.LambdaLR with the appropriate schedule.

**Examples:**

Example 1 (python):
```python
utils.schedulers.InterpolatingLogScheduler(
    optimizer,
    num_steps,
    min_lr,
    max_lr,
    last_epoch=-1,
)
```

Example 2 (python):
```python
utils.schedulers.JaggedLRRestartScheduler(
    optimizer,
    inner_schedule,
    jagged_restart_steps,
    jagged_restart_warmup_steps,
    jagged_restart_anneal_steps=1,
    min_lr_scale=0.001,
)
```

Example 3 (python):
```python
utils.schedulers.RexLR(
    optimizer,
    max_lr,
    min_lr,
    total_steps=0,
    num_warmup_steps=0,
    last_step=0,
)
```

Example 4 (python):
```python
utils.schedulers.get_cosine_schedule_with_min_lr(
    optimizer,
    num_warmup_steps,
    num_training_steps,
    min_lr_ratio=0.0,
)
```

---

## cli.merge_lora

**URL:** https://docs.axolotl.ai/docs/api/cli.merge_lora.html

**Contents:**
- cli.merge_lora
- Functions
  - do_cli
    - Parameters
    - Raises
  - do_merge_lora
    - Parameters

CLI to merge a trained LoRA into a base model.

Parses axolotl config, CLI args, and calls do_merge_lora. Note that various config values will be overwritten to allow the LoRA merge logic to work as expected (load_in_8bit=False, load_in4bit=False, flash_attention=False, etc.).

Calls transformers’ merge_and_unload on the model given in the axolotl config along with the LoRA adapters to combine them into a single base model.

**Examples:**

Example 1 (python):
```python
cli.merge_lora.do_cli(config=Path('examples/'), **kwargs)
```

Example 2 (python):
```python
cli.merge_lora.do_merge_lora(cfg)
```

---

## prompt_strategies.alpaca_w_system

**URL:** https://docs.axolotl.ai/docs/api/prompt_strategies.alpaca_w_system.html

**Contents:**
- prompt_strategies.alpaca_w_system
- Classes
  - InstructionWSystemPromptTokenizingStrategy
  - OpenOrcaPromptTokenizingStrategy
  - OpenOrcaSystemDataPrompter
  - SystemDataPrompter

prompt_strategies.alpaca_w_system

Prompt strategies loader for alpaca instruction datasets with system prompts

Tokenizing strategy for instruction-based prompts.

Tokenizing strategy for OpenOrca datasets

Alpaca Style Prompter that uses system prompts from the dataset, with OpenOrca prompts

Alpaca Style Prompter that uses system prompts from the dataset

**Examples:**

Example 1 (python):
```python
prompt_strategies.alpaca_w_system.InstructionWSystemPromptTokenizingStrategy(
    prompter,
    tokenizer,
    train_on_inputs=False,
    sequence_len=2048,
)
```

Example 2 (python):
```python
prompt_strategies.alpaca_w_system.OpenOrcaPromptTokenizingStrategy(
    prompter,
    tokenizer,
    train_on_inputs=False,
    sequence_len=2048,
)
```

Example 3 (python):
```python
prompt_strategies.alpaca_w_system.OpenOrcaSystemDataPrompter(
    prompt_style=PromptStyle.INSTRUCT.value,
)
```

Example 4 (python):
```python
prompt_strategies.alpaca_w_system.SystemDataPrompter(
    prompt_style=PromptStyle.INSTRUCT.value,
)
```

---

## loaders.patch_manager

**URL:** https://docs.axolotl.ai/docs/api/loaders.patch_manager.html

**Contents:**
- loaders.patch_manager
- Classes
  - PatchManager
    - Attributes
    - Methods
      - apply_post_model_load_patches
      - apply_post_plugin_pre_model_load_patches
      - apply_pre_model_load_patches

loaders.patch_manager

Patch manager class implementation to complement axolotl.loaders.ModelLoader.

Applies pre- and post-model load patches for various fixes and optimizations.

Manages the application of patches during the model loading process.

Apply patches that require the model instance.

Apply post plugin-pre_model_load load patches based on config.

Apply pre-model load patches based on config.

**Examples:**

Example 1 (python):
```python
loaders.patch_manager.PatchManager(cfg, model_config, inference=False)
```

Example 2 (python):
```python
loaders.patch_manager.PatchManager.apply_post_model_load_patches(model)
```

Example 3 (python):
```python
loaders.patch_manager.PatchManager.apply_post_plugin_pre_model_load_patches()
```

Example 4 (python):
```python
loaders.patch_manager.PatchManager.apply_pre_model_load_patches()
```

---

## utils.schemas.peft

**URL:** https://docs.axolotl.ai/docs/api/utils.schemas.peft.html

**Contents:**
- utils.schemas.peft
- Classes
  - LoftQConfig
  - LoraConfig
  - PeftConfig
  - ReLoRAConfig

Pydantic models for PEFT-related configuration

LoftQ configuration subset

Peft / LoRA configuration subset

peftq configuration subset

ReLoRA configuration subset

**Examples:**

Example 1 (python):
```python
utils.schemas.peft.LoftQConfig()
```

Example 2 (python):
```python
utils.schemas.peft.LoraConfig()
```

Example 3 (python):
```python
utils.schemas.peft.PeftConfig()
```

Example 4 (python):
```python
utils.schemas.peft.ReLoRAConfig()
```

---

## common.const

**URL:** https://docs.axolotl.ai/docs/api/common.const.html

**Contents:**
- common.const

Various shared constants

---

## prompt_strategies.kto.user_defined

**URL:** https://docs.axolotl.ai/docs/api/prompt_strategies.kto.user_defined.html

**Contents:**
- prompt_strategies.kto.user_defined

prompt_strategies.kto.user_defined

User-defined KTO strategies

---

## prompt_strategies.base

**URL:** https://docs.axolotl.ai/docs/api/prompt_strategies.base.html

**Contents:**
- prompt_strategies.base

prompt_strategies.base

module for base dataset transform strategies

---

## cli.delinearize_llama4

**URL:** https://docs.axolotl.ai/docs/api/cli.delinearize_llama4.html

**Contents:**
- cli.delinearize_llama4
- Functions
  - do_cli
    - Parameters

cli.delinearize_llama4

CLI tool to delinearize quantized/Linearized Llama-4 models.

Convert a patched HF format Llama4 model (with separated projections) back to the original HF format (with fused projections).

**Examples:**

Example 1 (python):
```python
cli.delinearize_llama4.do_cli(model, output)
```

---

## integrations.base

**URL:** https://docs.axolotl.ai/docs/api/integrations.base.html

**Contents:**
- integrations.base
- Classes
  - BaseOptimizerFactory
    - Methods
      - get_decay_parameter_names
  - BasePlugin
    - Note
    - Methods
      - add_callbacks_post_trainer
        - Parameters

Base class for all plugins.

A plugin is a reusable, modular, and self-contained piece of code that extends the functionality of Axolotl. Plugins can be used to integrate third-party models, modify the training process, or add new features.

To create a new plugin, you need to inherit from the BasePlugin class and implement the required methods.

Base class for factories to create custom optimizers

Get all parameter names that weight decay will be applied to.

This function filters out parameters in two ways: 1. By layer type (instances of layers specified in ALL_LAYERNORM_LAYERS) 2. By parameter name patterns (containing ‘bias’, or variation of ‘norm’)

Base class for all plugins. Defines the interface for plugin methods.

A plugin is a reusable, modular, and self-contained piece of code that extends the functionality of Axolotl. Plugins can be used to integrate third-party models, modify the training process, or add new features.

To create a new plugin, you need to inherit from the BasePlugin class and implement the required methods.

Plugin methods include: - register(cfg): Registers the plugin with the given configuration. - load_datasets(cfg): Loads and preprocesses the dataset for training. - pre_model_load(cfg): Performs actions before the model is loaded. - post_model_build(cfg, model): Performs actions after the model is loaded, but before LoRA adapters are applied. - pre_lora_load(cfg, model): Performs actions before LoRA weights are loaded. - post_lora_load(cfg, model): Performs actions after LoRA weights are loaded. - post_model_load(cfg, model): Performs actions after the model is loaded, inclusive of any adapters. - post_trainer_create(cfg, trainer): Performs actions after the trainer is created. - create_optimizer(cfg, trainer): Creates and returns an optimizer for training. - create_lr_scheduler(cfg, trainer, optimizer, num_training_steps): Creates and returns a learning rate scheduler. - add_callbacks_pre_trainer(cfg, model): Adds callbacks to the trainer before training. - add_callbacks_post_trainer(cfg, trainer): Adds callbacks to the trainer after training.

Adds callbacks to the trainer after creating the trainer. This is useful for callbacks that require access to the model or trainer.

Set up callbacks before creating the trainer.

Creates and returns a learning rate scheduler.

Creates and returns an optimizer for training.

Returns a custom class for the collator.

Returns a pydantic model for the plugin’s input arguments.

Returns a custom class for the trainer.

Returns custom training arguments to set on TrainingArgs.

Returns a dataclass model for the plugin’s training arguments.

Loads and preprocesses the dataset for training.

Performs actions after LoRA weights are loaded.

Performs actions after the model is built/loaded, but before any adapters are applied.

Performs actions after the model is loaded.

Performs actions after training is complete.

Performs actions after training is complete and the model is unloaded.

Performs actions after the trainer is created.

Performs actions before LoRA weights are loaded.

Performs actions before the model is loaded.

Registers the plugin with the given configuration as an unparsed dict.

The PluginManager class is responsible for loading and managing plugins. It should be a singleton so it can be accessed from anywhere in the codebase.

Key methods include: - get_instance(): Static method to get the singleton instance of PluginManager. - register(plugin_name: str): Registers a new plugin by its name. - pre_model_load(cfg): Calls the pre_model_load method of all registered plugins.

Calls the add_callbacks_post_trainer method of all registered plugins.

Calls the add_callbacks_pre_trainer method of all registered plugins.

Calls the create_lr_scheduler method of all registered plugins and returns the first non-None scheduler.

Calls the create_optimizer method of all registered plugins and returns the first non-None optimizer.

Calls the get_collator_cls_and_kwargs method of all registered plugins and returns the first non-None collator class.

Parameters: cfg (dict): The configuration for the plugins. is_eval (bool): Whether this is an eval split.

Returns: object: The collator class, or None if none was found.

Returns a list of Pydantic classes for all registered plugins’ input arguments.’

Returns the singleton instance of PluginManager. If the instance doesn’t exist, it creates a new one.

Calls the get_trainer_cls method of all registered plugins and returns the first non-None trainer class.

Calls the get_training_args method of all registered plugins and returns the combined training arguments.

Parameters: cfg (dict): The configuration for the plugins.

Returns: object: The training arguments

Returns a list of dataclasses for all registered plugins’ training args mixins’

Returns: list[str]: A list of dataclsses

Calls the load_datasets method of each registered plugin.

Calls the post_lora_load method of all registered plugins.

Calls the post_model_build method of all registered plugins after the model has been built / loaded, but before any adapters have been applied.

Calls the post_model_load method of all registered plugins after the model has been loaded inclusive of any adapters.

Calls the post_train method of all registered plugins.

Calls the post_train_unload method of all registered plugins.

Calls the post_trainer_create method of all registered plugins.

Calls the pre_lora_load method of all registered plugins.

Calls the pre_model_load method of all registered plugins.

Registers a new plugin by its name.

Loads a plugin based on the given plugin name.

The plugin name should be in the format “module_name.class_name”. This function splits the plugin name into module and class, imports the module, retrieves the class from the module, and creates an instance of the class.

**Examples:**

Example 1 (python):
```python
integrations.base.BaseOptimizerFactory()
```

Example 2 (python):
```python
integrations.base.BaseOptimizerFactory.get_decay_parameter_names(model)
```

Example 3 (python):
```python
integrations.base.BasePlugin()
```

Example 4 (python):
```python
integrations.base.BasePlugin.add_callbacks_post_trainer(cfg, trainer)
```

---

## prompt_strategies.chat_template

**URL:** https://docs.axolotl.ai/docs/api/prompt_strategies.chat_template.html

**Contents:**
- prompt_strategies.chat_template
- Classes
  - ChatTemplatePrompter
    - Methods
      - build_prompt
        - Parameters
  - ChatTemplateStrategy
    - Methods
      - find_first_eot_token
      - find_turn

prompt_strategies.chat_template

HF Chat Templates prompt strategy

Prompter for HF chat templates

Build a prompt from a conversation.

Tokenizing strategy for instruction-based prompts.

Find the first EOT token in the input_ids starting from start_idx.

Locate the starting and ending indices of the specified turn in a conversation.

Public method that can handle either a single prompt or a batch of prompts.

Mistral prompter for chat template.

Mistral strategy for chat template.

Find the first EOT token in the input_ids starting from start_idx.

Load chat template strategy based on configuration.

**Examples:**

Example 1 (python):
```python
prompt_strategies.chat_template.ChatTemplatePrompter(
    tokenizer,
    chat_template,
    processor=None,
    max_length=2048,
    message_property_mappings=None,
    message_field_training=None,
    message_field_training_detail=None,
    field_messages='messages',
    field_system='system',
    field_tools='tools',
    field_thinking='reasoning_content',
    roles=None,
    template_thinking_key='reasoning_content',
    chat_template_kwargs=None,
    drop_system_message=False,
)
```

Example 2 (python):
```python
prompt_strategies.chat_template.ChatTemplatePrompter.build_prompt(
    conversation,
    add_generation_prompt=False,
    images=None,
    tools=None,
)
```

Example 3 (python):
```python
prompt_strategies.chat_template.ChatTemplateStrategy(
    prompter,
    tokenizer,
    train_on_inputs,
    sequence_len,
    roles_to_train=None,
    train_on_eos=None,
    train_on_eot=None,
    eot_tokens=None,
    split_thinking=False,
)
```

Example 4 (python):
```python
prompt_strategies.chat_template.ChatTemplateStrategy.find_first_eot_token(
    input_ids,
    start_idx,
)
```

---

## kernels.quantize

**URL:** https://docs.axolotl.ai/docs/api/kernels.quantize.html

**Contents:**
- kernels.quantize
- Functions
  - dequantize
    - Parameters
    - Returns
    - Raises
    - Note

Dequantization utilities for bitsandbytes integration.

Fast NF4 dequantization using bitsandbytes CUDA kernels.

Performs efficient dequantization of weights from NF4 format using bitsandbytes’ optimized CUDA implementations. Supports both legacy list and new QuantState formats.

Uses CUDA streams for better performance when available in newer bitsandbytes versions (>0.43.3).

**Examples:**

Example 1 (python):
```python
kernels.quantize.dequantize(W, quant_state=None, out=None)
```

---

## integrations.spectrum.args

**URL:** https://docs.axolotl.ai/docs/api/integrations.spectrum.args.html

**Contents:**
- integrations.spectrum.args
- Classes
  - SpectrumArgs

integrations.spectrum.args

Module for handling Spectrum input arguments.

Input args for Spectrum.

**Examples:**

Example 1 (python):
```python
integrations.spectrum.args.SpectrumArgs()
```

---

## prompt_strategies.alpaca_chat

**URL:** https://docs.axolotl.ai/docs/api/prompt_strategies.alpaca_chat.html

**Contents:**
- prompt_strategies.alpaca_chat
- Classes
  - AlpacaChatPrompter
  - AlpacaConcisePrompter
  - AlpacaQAPromptTokenizingStrategy
  - CamelAIPromptTokenizingStrategy
  - NoSystemPrompter

prompt_strategies.alpaca_chat

Module for Alpaca prompt strategy classes

Alpaca Chat Prompter extending the system prompt to for chat-instruct answers

Alpaca Prompter extending the system prompt to ask for concise chat-instruct answers

Tokenizing strategy for AlpacaQA

Tokenizing strategy for CamelAI datasets

Null Prompter with no system prompts

**Examples:**

Example 1 (python):
```python
prompt_strategies.alpaca_chat.AlpacaChatPrompter()
```

Example 2 (python):
```python
prompt_strategies.alpaca_chat.AlpacaConcisePrompter(
    prompt_style=PromptStyle.INSTRUCT.value,
)
```

Example 3 (python):
```python
prompt_strategies.alpaca_chat.AlpacaQAPromptTokenizingStrategy(
    prompter,
    tokenizer,
    train_on_inputs=False,
    sequence_len=2048,
)
```

Example 4 (python):
```python
prompt_strategies.alpaca_chat.CamelAIPromptTokenizingStrategy(
    prompter,
    tokenizer,
    train_on_inputs=False,
    sequence_len=2048,
)
```

---

## utils.collators.mamba

**URL:** https://docs.axolotl.ai/docs/api/utils.collators.mamba.html

**Contents:**
- utils.collators.mamba
- Classes
  - MambaDataCollator

utils.collators.mamba

Collator for State Space Models (Mamba)

**Examples:**

Example 1 (python):
```python
utils.collators.mamba.MambaDataCollator(tokenizer)
```

---

## prompt_strategies.messages.chat

**URL:** https://docs.axolotl.ai/docs/api/prompt_strategies.messages.chat.html

**Contents:**
- prompt_strategies.messages.chat
- Classes
  - ChatMessageDatasetWrappingStrategy

prompt_strategies.messages.chat

Chat dataset wrapping strategy for new internal messages representations

Chat dataset wrapping strategy for new internal messages representations

**Examples:**

Example 1 (python):
```python
prompt_strategies.messages.chat.ChatMessageDatasetWrappingStrategy(
    processor,
    message_transform=None,
    formatter=None,
    **kwargs,
)
```

---

## train

**URL:** https://docs.axolotl.ai/docs/api/train.html

**Contents:**
- train
- Functions
  - create_model_card
    - Parameters
  - execute_training
    - Parameters
  - handle_untrained_tokens_fix
    - Parameters
  - save_initial_configs
    - Parameters

Prepare and train a model on a dataset. Can also infer from a model or merge lora

Create a model card for the trained model if needed.

Execute the training process with appropriate SDP kernel configurations.

Apply fixes for untrained tokens if configured.

Save initial configurations before training.

Save the trained model according to configuration and training setup.

Load the tokenizer, processor (for multimodal models), and model based on configuration.

Load model, tokenizer, trainer, etc. Helper function to encapsulate the full trainer setup.

Set up the Axolotl badge and add the Axolotl config to the model card if available.

Set up the reference model for RL training if needed.

Set up signal handler for graceful termination.

Train a model on the given dataset.

**Examples:**

Example 1 (python):
```python
train.create_model_card(cfg, trainer)
```

Example 2 (python):
```python
train.execute_training(cfg, trainer, resume_from_checkpoint)
```

Example 3 (python):
```python
train.handle_untrained_tokens_fix(
    cfg,
    model,
    tokenizer,
    train_dataset,
    safe_serialization,
)
```

Example 4 (python):
```python
train.save_initial_configs(cfg, tokenizer, model, peft_config, processor)
```

---

## cli.utils.load

**URL:** https://docs.axolotl.ai/docs/api/cli.utils.load.html

**Contents:**
- cli.utils.load
- Functions
  - load_model_and_tokenizer
    - Parameters
    - Returns

Utilities for model, tokenizer, etc. loading.

Helper function for loading a model, tokenizer, and processor specified in the given axolotl config.

**Examples:**

Example 1 (python):
```python
cli.utils.load.load_model_and_tokenizer(cfg, inference=False)
```

---

## loaders.model

**URL:** https://docs.axolotl.ai/docs/api/loaders.model.html

**Contents:**
- loaders.model
- Classes
  - ModelLoader
    - The loading process includes
    - Attributes
    - Methods
      - load
        - Returns

Model loader class implementation for loading, configuring, and patching various models.

Manages model configuration, initialization and application of patches during model loading.

This class orchestrates the entire process of loading a model from configuration to final preparation. It handles device mapping, quantization, attention mechanisms, adapter integration, and various optimizations.

Load and prepare the model with all configurations and patches.

**Examples:**

Example 1 (python):
```python
loaders.model.ModelLoader(
    cfg,
    tokenizer,
    *,
    inference=False,
    reference_model=False,
    **kwargs,
)
```

Example 2 (python):
```python
loaders.model.ModelLoader.load()
```

---

## utils.distributed

**URL:** https://docs.axolotl.ai/docs/api/utils.distributed.html

**Contents:**
- utils.distributed
- Functions
  - barrier
  - cleanup_distributed
  - compute_and_broadcast
  - gather_from_all_ranks
  - gather_scalar_from_all_ranks
  - is_distributed
  - is_main_process
    - Returns

Utilities for distributed functionality.

Acts as a barrier to wait for all processes. This ensures that all processes reach the barrier before proceeding further.

Destroy process group if torch distributed is initialized. Called in training early termination or when training successfully completes.

Compute a value using the function ‘fn’ only on the specified rank (default is 0). The value is then broadcasted to all other ranks.

Args: - fn (callable): A function that computes the value. This should not have any side effects. - rank (int, optional): The rank that computes the value. Default is 0.

Returns: - The computed value (int or float).

Run a callable ‘fn’ on all ranks and gather the results on the specified rank.

Args: - fn (callable): A function that computes the value. This should not have any side effects. - rank (int, optional): The rank that gathers the values. Default is 0. - world_size (int, optional): Total number of processes in the current distributed setup.

Returns: - A list of computed values from all ranks if on the gathering rank, otherwise None.

Run a callable ‘fn’ on all ranks and gather the results on the specified rank.

Args: - fn (callable): A function that computes the value. This should not have any side effects. - rank (int, optional): The rank that gathers the values. Default is 0. - world_size (int, optional): Total number of processes in the current distributed setup.

Returns: - A list of computed values from all ranks if on the gathering rank, otherwise None.

Check if distributed training is initialized.

Check if the current process is the main process. If not in distributed mode, always return True.

We use a simpler logic when the distributed state is not initialized: we just log on the 0-th local rank.

Run a callable ‘fn1’ on all ranks, gather the results, reduce them using ‘fn2’, and then broadcast the reduced result to all ranks.

Args: - fn1 (callable): A function that computes the value on each rank. - fn2 (callable): A reduction function that takes a list of values and returns a single value. - world_size (int, optional): Total number of processes in the current distributed setup.

Returns: - The reduced and broadcasted value.

runs the wrapped context so that rank 0 runs first before other ranks

**Examples:**

Example 1 (python):
```python
utils.distributed.barrier()
```

Example 2 (python):
```python
utils.distributed.cleanup_distributed()
```

Example 3 (python):
```python
utils.distributed.compute_and_broadcast(fn)
```

Example 4 (python):
```python
utils.distributed.gather_from_all_ranks(fn, world_size=1)
```

---

## cli.config

**URL:** https://docs.axolotl.ai/docs/api/cli.config.html

**Contents:**
- cli.config
- Functions
  - check_remote_config
    - Parameters
    - Returns
    - Raises
  - choose_config
    - Parameters
    - Returns
    - Raises

Configuration loading and processing.

First, determines if the passed config is a valid HTTPS URL. Then, attempts to query for it and parse its content, first as JSON, then as YAML (YAML is preferred). Finally, the parsed content is written to a local file and its path is returned.

Helper method for choosing a axolotl config YAML file (considering only files ending with .yml or .yaml). If more than one config file exists in the passed path, the user is prompted to choose one.

Loads the axolotl configuration stored at config, validates it, and performs various setup.

Registers the plugins for the given configuration.

**Examples:**

Example 1 (python):
```python
cli.config.check_remote_config(config)
```

Example 2 (python):
```python
cli.config.choose_config(path)
```

Example 3 (python):
```python
cli.config.load_cfg(config=Path('examples/'), **kwargs)
```

Example 4 (python):
```python
cli.config.prepare_plugins(cfg)
```

---

## cli.checks

**URL:** https://docs.axolotl.ai/docs/api/cli.checks.html

**Contents:**
- cli.checks
- Functions
  - check_accelerate_default_config
  - check_user_token
    - Returns
    - Raises

Various checks for Axolotl CLI.

Logs at warning level if no accelerate config file is found.

Checks for HF user info. Check is skipped if HF_HUB_OFFLINE=1.

**Examples:**

Example 1 (python):
```python
cli.checks.check_accelerate_default_config()
```

Example 2 (python):
```python
cli.checks.check_user_token()
```

---

## prompt_strategies.llama2_chat

**URL:** https://docs.axolotl.ai/docs/api/prompt_strategies.llama2_chat.html

**Contents:**
- prompt_strategies.llama2_chat
- Classes
  - LLama2ChatTokenizingStrategy
  - Llama2ChatConversation
    - Methods
      - append_message
      - get_prompt
  - Llama2ChatPrompter

prompt_strategies.llama2_chat

Prompt Strategy for finetuning Llama2 chat models see also https://github.com/facebookresearch/llama/blob/6c7fe276574e78057f917549435a2554000a876d/llama/generation.py#L213 for ma reference implementation.

This implementation is based on the Vicuna PR and the fastchat repo, see also: https://github.com/lm-sys/FastChat/blob/cdd7730686cb1bf9ae2b768ee171bdf7d1ff04f3/fastchat/conversation.py#L847

Use dataset type: “llama2_chat” in conig.yml to use this prompt style.

E.g. in the config.yml:

The dataset itself should look like this:

in a jsonl file. The first message should be from the human, the second from gpt. For a custom system message, the first “from” can be “system” (followed by alternating “human” and “gpt” turns).

Important: Don’t use “special_tokens:” in your config.yml if you are not sure what you are doing!

Tokenizing strategy for Llama2 prompts. adapted from https://github.com/lm-sys/FastChat/blob/main/fastchat/train/train.py

A class that manages prompt templates and keeps all conversation history. copied from https://github.com/lm-sys/FastChat/blob/main/fastchat/conversation.py

Append a new message.

Get the prompt for generation.

A prompter that generates prompts for Llama2 models.

**Examples:**

Example 1 (unknown):
```unknown
datasets:
  - path: llama_finetune_train.jsonl
    type: llama2_chat
```

Example 2 (unknown):
```unknown
{'conversations':[{"from": "human", "value": "Who are you?"}, {"from": "gpt", "value": "I am Vicuna"},...]}
```

Example 3 (python):
```python
prompt_strategies.llama2_chat.LLama2ChatTokenizingStrategy(*args, **kwargs)
```

Example 4 (python):
```python
prompt_strategies.llama2_chat.Llama2ChatConversation(
    name='llama2',
    system="[INST] <<SYS>>\nYou are a helpful, respectful and honest assistant. Always answer as helpfully as possible, while being safe. Your answers should not include any harmful, unethical, racist, sexist, toxic, dangerous, or illegal content. Please ensure that your responses are socially unbiased and positive in nature.\n\nIf a question does not make any sense, or is not factually coherent, explain why instead of answering something not correct. If you don't know the answer to a question, please don't share false information.\n<</SYS>>\n\n",
    roles=('[INST]', '[/INST]'),
    messages=list(),
    offset=0,
)
```

---

## cli.utils

**URL:** https://docs.axolotl.ai/docs/api/cli.utils.html

**Contents:**
- cli.utils

Init for axolotl.cli.utils module.

---

## cli.utils.args

**URL:** https://docs.axolotl.ai/docs/api/cli.utils.args.html

**Contents:**
- cli.utils.args
- Functions
  - add_options_from_config
    - Parameters
    - Returns
  - add_options_from_dataclass
    - Parameters
    - Returns
  - filter_none_kwargs
    - Parameters

Utilities for axolotl CLI args.

Create Click options from the fields of a Pydantic model.

Create Click options from the fields of a dataclass.

Wraps function to remove None-valued kwargs.

**Examples:**

Example 1 (python):
```python
cli.utils.args.add_options_from_config(config_class)
```

Example 2 (python):
```python
cli.utils.args.add_options_from_dataclass(config_class)
```

Example 3 (python):
```python
cli.utils.args.filter_none_kwargs(func)
```

---

## integrations.grokfast.optimizer

**URL:** https://docs.axolotl.ai/docs/api/integrations.grokfast.optimizer.html

**Contents:**
- integrations.grokfast.optimizer

integrations.grokfast.optimizer

---

## core.builders.causal

**URL:** https://docs.axolotl.ai/docs/api/core.builders.causal.html

**Contents:**
- core.builders.causal
- Classes
  - HFCausalTrainerBuilder

Builder for causal trainers

Build the HuggingFace training args/trainer for causal models and reward modeling using TRL.

**Examples:**

Example 1 (python):
```python
core.builders.causal.HFCausalTrainerBuilder(
    cfg,
    model,
    tokenizer,
    processor=None,
)
```

---

## prompt_strategies.dpo.user_defined

**URL:** https://docs.axolotl.ai/docs/api/prompt_strategies.dpo.user_defined.html

**Contents:**
- prompt_strategies.dpo.user_defined

prompt_strategies.dpo.user_defined

User-defined DPO strategies

---

## cli.evaluate

**URL:** https://docs.axolotl.ai/docs/api/cli.evaluate.html

**Contents:**
- cli.evaluate
- Functions
  - do_cli
    - Parameters
  - do_evaluate
    - Parameters

CLI to run evaluation on a model.

Parses axolotl config, CLI args, and calls do_evaluate.

Evaluates a transformers model by first loading the dataset(s) specified in the axolotl config, and then calling axolotl.evaluate.evaluate, which computes evaluation metrics on the given dataset(s) and writes them to disk.

**Examples:**

Example 1 (python):
```python
cli.evaluate.do_cli(config=Path('examples/'), **kwargs)
```

Example 2 (python):
```python
cli.evaluate.do_evaluate(cfg, cli_args)
```

---

## utils.schemas.utils

**URL:** https://docs.axolotl.ai/docs/api/utils.schemas.utils.html

**Contents:**
- utils.schemas.utils
- Functions
  - handle_legacy_message_fields_logic
    - Parameters
    - Returns
    - Raises

Utilities for Axolotl Pydantic models

Handle backwards compatibility between legacy message field mapping and new property mapping system.

Previously, the config only supported mapping ‘role’ and ‘content’ fields via dedicated config options: - message_field_role: Mapped to the role field - message_field_content: Mapped to the content field

The new system uses message_property_mappings to support arbitrary field mappings: message_property_mappings: role: source_role_field content: source_content_field additional_field: source_field

**Examples:**

Example 1 (python):
```python
utils.schemas.utils.handle_legacy_message_fields_logic(data)
```

---

## prompt_strategies.alpaca_instruct

**URL:** https://docs.axolotl.ai/docs/api/prompt_strategies.alpaca_instruct.html

**Contents:**
- prompt_strategies.alpaca_instruct

prompt_strategies.alpaca_instruct

Module loading the AlpacaInstructPromptTokenizingStrategy class

---

## utils.callbacks.lisa

**URL:** https://docs.axolotl.ai/docs/api/utils.callbacks.lisa.html

**Contents:**
- utils.callbacks.lisa

Adapted from https://github.com/OptimalScale/LMFlow/pull/701 for HF transformers & Axolotl Arxiv: https://arxiv.org/abs/2403.17919 License: Apache 2.0

---

## models.mamba.modeling_mamba

**URL:** https://docs.axolotl.ai/docs/api/models.mamba.modeling_mamba.html

**Contents:**
- models.mamba.modeling_mamba

models.mamba.modeling_mamba

---

## prompt_strategies.metharme

**URL:** https://docs.axolotl.ai/docs/api/prompt_strategies.metharme.html

**Contents:**
- prompt_strategies.metharme
- Classes
  - MetharmePromptTokenizingStrategy
  - MetharmePrompter

prompt_strategies.metharme

Module containing the MetharmenPromptTokenizingStrategy and MetharmePrompter class

Tokenizing strategy for the Metharme models

Prompter for the Metharme models.

**Examples:**

Example 1 (python):
```python
prompt_strategies.metharme.MetharmePromptTokenizingStrategy(
    prompter,
    tokenizer,
    train_on_inputs=False,
    sequence_len=2048,
)
```

Example 2 (python):
```python
prompt_strategies.metharme.MetharmePrompter(*args, **kwargs)
```

---

## core.trainers.mamba

**URL:** https://docs.axolotl.ai/docs/api/core.trainers.mamba.html

**Contents:**
- core.trainers.mamba
- Classes
  - AxolotlMambaTrainer

Module for mamba trainer

Mamba specific trainer to handle loss calculation

**Examples:**

Example 1 (python):
```python
core.trainers.mamba.AxolotlMambaTrainer(
    *_args,
    bench_data_collator=None,
    eval_data_collator=None,
    dataset_tags=None,
    **kwargs,
)
```

---

## utils.ctx_managers.sequence_parallel

**URL:** https://docs.axolotl.ai/docs/api/utils.ctx_managers.sequence_parallel.html

**Contents:**
- utils.ctx_managers.sequence_parallel
- Classes
  - AllGatherWithGrad
    - Methods
      - backward
        - Parameters
        - Returns
      - forward
        - Parameters
        - Returns

utils.ctx_managers.sequence_parallel

Module for Axolotl trainer sequence parallelism manager and utilities

Custom autograd function for all-gather to preserve gradients.

Backward pass for all-gather operation.

Extracts the gradient slice corresponding to this rank’s original input from the full gradient tensor.

Forward pass of all-gather of data with sequence dimension.

Context manager for sequence parallelism operations.

This class provides a context that will automatically apply sequence parallelism during model forward passes using a pre-forward hook, and gather outputs from across the sequence parallelism group using a post-forward hook.

Apply sequence parallelism slicing to a batch.

Special handling is implemented for integer logits_to_keep, which indicates to only keep the last N tokens in the sequence during generation.

**Examples:**

Example 1 (python):
```python
utils.ctx_managers.sequence_parallel.AllGatherWithGrad()
```

Example 2 (python):
```python
utils.ctx_managers.sequence_parallel.AllGatherWithGrad.backward(
    ctx,
    grad_output,
)
```

Example 3 (python):
```python
utils.ctx_managers.sequence_parallel.AllGatherWithGrad.forward(
    ctx,
    input_tensor,
    group,
)
```

Example 4 (python):
```python
utils.ctx_managers.sequence_parallel.SequenceParallelContextManager(
    models,
    context_parallel_size,
    gradient_accumulation_steps,
    ring_attn_func,
    heads_k_stride,
    gather_outputs,
    device_mesh=None,
)
```

---

## utils.callbacks.qat

**URL:** https://docs.axolotl.ai/docs/api/utils.callbacks.qat.html

**Contents:**
- utils.callbacks.qat
- Classes
  - QATCallback
- Functions
  - toggle_fake_quant
    - Parameters

QAT Callback for HF Causal Trainer

Callback to toggle fake quantization for the model.

Toggle fake quantization for any fake quantized linear or embedding layers in the model.

**Examples:**

Example 1 (python):
```python
utils.callbacks.qat.QATCallback(cfg)
```

Example 2 (python):
```python
utils.callbacks.qat.toggle_fake_quant(mod, enable)
```

---

## prompt_strategies.dpo.zephyr

**URL:** https://docs.axolotl.ai/docs/api/prompt_strategies.dpo.zephyr.html

**Contents:**
- prompt_strategies.dpo.zephyr

prompt_strategies.dpo.zephyr

DPO strategies for zephyr

---

## kernels.utils

**URL:** https://docs.axolotl.ai/docs/api/kernels.utils.html

**Contents:**
- kernels.utils

Utilities for axolotl.kernels submodules.

---

## monkeypatch.multipack

**URL:** https://docs.axolotl.ai/docs/api/monkeypatch.multipack.html

**Contents:**
- monkeypatch.multipack

monkeypatch.multipack

multipack patching for v2 of sample packing

---

## cli.main

**URL:** https://docs.axolotl.ai/docs/api/cli.main.html

**Contents:**
- cli.main
- Functions
  - cli
  - evaluate
    - Parameters
  - fetch
    - Parameters
  - inference
    - Parameters
  - merge_lora

Click CLI definitions for various axolotl commands.

Axolotl CLI - Train and fine-tune large language models

Fetch example configs or other resources.

Available directories: - examples: Example configuration files - deepspeed_configs: DeepSpeed configuration files

Run inference with a trained model.

Merge trained LoRA adapters into a base model.

Merge sharded FSDP model weights.

Preprocess datasets before training.

Train or fine-tune a model.

**Examples:**

Example 1 (python):
```python
cli.main.cli()
```

Example 2 (python):
```python
cli.main.evaluate(ctx, config, launcher, **kwargs)
```

Example 3 (python):
```python
cli.main.fetch(directory, dest)
```

Example 4 (python):
```python
cli.main.inference(ctx, config, launcher, gradio, **kwargs)
```

---

## core.trainers.mixins.optimizer

**URL:** https://docs.axolotl.ai/docs/api/core.trainers.mixins.optimizer.html

**Contents:**
- core.trainers.mixins.optimizer
- Classes
  - OptimizerInitMixin
  - OptimizerMixin

core.trainers.mixins.optimizer

Module for Axolotl trainer optimizer mixin

Mixin to handle common optimizer initialization logic for Trainers (mostly TRL) that do not accept optimizer_cls_and_kwargs as kwarg in constructor.

Mixin class for shared handling of building custom optimizers

**Examples:**

Example 1 (python):
```python
core.trainers.mixins.optimizer.OptimizerInitMixin(*args, **kwargs)
```

Example 2 (python):
```python
core.trainers.mixins.optimizer.OptimizerMixin()
```

---

## integrations.kd.trainer

**URL:** https://docs.axolotl.ai/docs/api/integrations.kd.trainer.html

**Contents:**
- integrations.kd.trainer
- Classes
  - AxolotlKDTrainer
    - Methods
      - compute_loss

integrations.kd.trainer

Custom trainer subclass for Knowledge Distillation (KD)

How the loss is computed by Trainer. By default, all models return the loss in the first element.

Subclass and override for custom behavior.

**Examples:**

Example 1 (python):
```python
integrations.kd.trainer.AxolotlKDTrainer(*args, **kwargs)
```

Example 2 (python):
```python
integrations.kd.trainer.AxolotlKDTrainer.compute_loss(
    model,
    inputs,
    return_outputs=False,
    num_items_in_batch=None,
)
```

---

## integrations.lm_eval.args

**URL:** https://docs.axolotl.ai/docs/api/integrations.lm_eval.args.html

**Contents:**
- integrations.lm_eval.args
- Classes
  - LMEvalArgs

integrations.lm_eval.args

Module for handling lm eval harness input arguments.

Input args for lm eval harness

**Examples:**

Example 1 (python):
```python
integrations.lm_eval.args.LMEvalArgs()
```

---

## integrations.cut_cross_entropy.args

**URL:** https://docs.axolotl.ai/docs/api/integrations.cut_cross_entropy.args.html

**Contents:**
- integrations.cut_cross_entropy.args
- Classes
  - CutCrossEntropyArgs

integrations.cut_cross_entropy.args

Module for handling Cut Cross Entropy input arguments.

Input args for Cut Cross Entropy.

**Examples:**

Example 1 (python):
```python
integrations.cut_cross_entropy.args.CutCrossEntropyArgs()
```

---

## monkeypatch.mistral_attn_hijack_flash

**URL:** https://docs.axolotl.ai/docs/api/monkeypatch.mistral_attn_hijack_flash.html

**Contents:**
- monkeypatch.mistral_attn_hijack_flash

monkeypatch.mistral_attn_hijack_flash

Flash attention monkey patch for mistral model

---

## loaders.constants

**URL:** https://docs.axolotl.ai/docs/api/loaders.constants.html

**Contents:**
- loaders.constants

Shared constants for axolotl.loaders module

---

## utils.bench

**URL:** https://docs.axolotl.ai/docs/api/utils.bench.html

**Contents:**
- utils.bench
- Functions
  - check_cuda_device

Benchmarking and measurement utilities

wraps a function and returns the default value instead of running the wrapped function if cuda isn’t available or the device is auto :param default_value: :return:

**Examples:**

Example 1 (python):
```python
utils.bench.check_cuda_device(default_value)
```

---

## utils.trainer

**URL:** https://docs.axolotl.ai/docs/api/utils.trainer.html

**Contents:**
- utils.trainer
- Functions
  - add_pose_position_ids
  - add_position_ids
  - drop_long_seq
  - setup_trainer
    - Parameters
    - Returns

Module containing the Trainer class and related functions

use the PoSE technique to extend the context length by randomly skipping positions in the context. We only want to skip right before tokens in the split_on_token_ids list. We should attempt to randomly distribute the skips, but we don’t need the final position_ids to be the full context_len. There may be multiple turns in the context, so we want to make sure we take into account the maximum possible number of skips remaining in each sample.

Handle both single-example and batched data. - single example: sample[‘input_ids’] is a list[int] - batched data: sample[‘input_ids’] is a list[list[int]]

Drop samples whose sequence length is either too long (> sequence_len) or too short (< min_sequence_len).

Works for both single-example (list[int]) or batched (list[list[int]]).

Helper method for instantiating and building a (causal or RLHF) trainer.

**Examples:**

Example 1 (python):
```python
utils.trainer.add_pose_position_ids(
    sample,
    max_context_len=32768,
    split_on_token_ids=None,
    chunks=2,
)
```

Example 2 (python):
```python
utils.trainer.add_position_ids(sample)
```

Example 3 (python):
```python
utils.trainer.drop_long_seq(sample, sequence_len=2048, min_sequence_len=2)
```

Example 4 (python):
```python
utils.trainer.setup_trainer(
    cfg,
    train_dataset,
    eval_dataset,
    model,
    tokenizer,
    processor,
    total_num_steps,
    model_ref=None,
    peft_config=None,
)
```

---

## utils.schemas.config

**URL:** https://docs.axolotl.ai/docs/api/utils.schemas.config.html

**Contents:**
- utils.schemas.config
- Classes
  - AxolotlConfigWCapabilities
  - AxolotlInputConfig

Module with Pydantic models for configuration.

wrapper to valdiate GPU capabilities with the configured options

Wrapper of all config options.

**Examples:**

Example 1 (python):
```python
utils.schemas.config.AxolotlConfigWCapabilities()
```

Example 2 (python):
```python
utils.schemas.config.AxolotlInputConfig()
```

---

## cli.args

**URL:** https://docs.axolotl.ai/docs/api/cli.args.html

**Contents:**
- cli.args
- Classes
  - EvaluateCliArgs
  - InferenceCliArgs
  - PreprocessCliArgs
  - QuantizeCliArgs
  - TrainerCliArgs
  - VllmServeCliArgs

Module for axolotl CLI command arguments.

Dataclass with CLI arguments for axolotl evaluate command.

Dataclass with CLI arguments for axolotl inference command.

Dataclass with CLI arguments for axolotl preprocess command.

Dataclass with CLI arguments for axolotl quantize command.

Dataclass with CLI arguments for axolotl train command.

Dataclass with CLI arguments for axolotl vllm-serve command.

**Examples:**

Example 1 (python):
```python
cli.args.EvaluateCliArgs(
    debug=False,
    debug_text_only=False,
    debug_num_examples=0,
)
```

Example 2 (python):
```python
cli.args.InferenceCliArgs(prompter=None)
```

Example 3 (python):
```python
cli.args.PreprocessCliArgs(
    debug=False,
    debug_text_only=False,
    debug_num_examples=1,
    prompter=None,
    download=True,
    iterable=False,
)
```

Example 4 (python):
```python
cli.args.QuantizeCliArgs(
    base_model=None,
    weight_dtype=None,
    activation_dtype=None,
    quantize_embedding=None,
    group_size=None,
    output_dir=None,
    hub_model_id=None,
)
```

---

## common.architectures

**URL:** https://docs.axolotl.ai/docs/api/common.architectures.html

**Contents:**
- common.architectures

Common architecture specific constants

---

## cli.merge_sharded_fsdp_weights

**URL:** https://docs.axolotl.ai/docs/api/cli.merge_sharded_fsdp_weights.html

**Contents:**
- cli.merge_sharded_fsdp_weights
- Classes
  - BFloat16CastPlanner
- Functions
  - do_cli
    - Parameters
  - merge_fsdp_weights
    - Parameters
    - Raises

cli.merge_sharded_fsdp_weights

CLI to merge sharded FSDP model checkpoints into a single combined checkpoint.

A custom planner to cast tensors to bfloat16 on the fly during loading.

Parses axolotl config, CLI args, and calls merge_fsdp_weights.

Merge the weights from sharded FSDP model checkpoints into a single combined checkpoint. Should be used if SHARDED_STATE_DICT was used for the model. Weights will be saved to {output_path}/model.safetensors if safe_serialization else pytorch_model.bin.

Note: this is a CPU-bound process.

**Examples:**

Example 1 (python):
```python
cli.merge_sharded_fsdp_weights.BFloat16CastPlanner()
```

Example 2 (python):
```python
cli.merge_sharded_fsdp_weights.do_cli(config=Path('examples/'), **kwargs)
```

Example 3 (python):
```python
cli.merge_sharded_fsdp_weights.merge_fsdp_weights(
    checkpoint_dir,
    output_path,
    safe_serialization=False,
    remove_checkpoint_dir=False,
)
```

---

## utils.data.streaming

**URL:** https://docs.axolotl.ai/docs/api/utils.data.streaming.html

**Contents:**
- utils.data.streaming

Data handling specific to streaming datasets.

---

## core.chat.format.chatml

**URL:** https://docs.axolotl.ai/docs/api/core.chat.format.chatml.html

**Contents:**
- core.chat.format.chatml

core.chat.format.chatml

ChatML transformation functions for MessageContents

---

## prompt_strategies.kto.chatml

**URL:** https://docs.axolotl.ai/docs/api/prompt_strategies.kto.chatml.html

**Contents:**
- prompt_strategies.kto.chatml
- Functions
  - argilla_chat
  - intel
  - ultra

prompt_strategies.kto.chatml

KTO strategies for chatml

for argilla/kto-mix-15k conversations

For Intel Orca KTO ex: argilla/distilabel-intel-orca-kto

for ultrafeedback binarized conversations ex: argilla/ultrafeedback-binarized-preferences-cleaned-kto

**Examples:**

Example 1 (python):
```python
prompt_strategies.kto.chatml.argilla_chat(cfg, **kwargs)
```

Example 2 (python):
```python
prompt_strategies.kto.chatml.intel(cfg, **kwargs)
```

Example 3 (python):
```python
prompt_strategies.kto.chatml.ultra(cfg, **kwargs)
```

---

## utils.schemas.trl

**URL:** https://docs.axolotl.ai/docs/api/utils.schemas.trl.html

**Contents:**
- utils.schemas.trl
- Classes
  - TRLConfig

Pydantic models for TRL trainer configuration

**Examples:**

Example 1 (python):
```python
utils.schemas.trl.TRLConfig()
```

---

## monkeypatch.llama_attn_hijack_xformers

**URL:** https://docs.axolotl.ai/docs/api/monkeypatch.llama_attn_hijack_xformers.html

**Contents:**
- monkeypatch.llama_attn_hijack_xformers

monkeypatch.llama_attn_hijack_xformers

Directly copied the code from https://raw.githubusercontent.com/oobabooga/text-generation-webui/main/modules/llama_attn_hijack.py and made some adjustments

---

## kernels.geglu

**URL:** https://docs.axolotl.ai/docs/api/kernels.geglu.html

**Contents:**
- kernels.geglu
- Functions
  - geglu_backward
    - Parameters
    - Returns
    - Note
  - geglu_forward
    - Parameters
    - Returns

Module for definition of GEGLU Triton kernels.

See “GLU Variants Improve Transformer” (https://arxiv.org/abs/2002.05202).

Credit to unsloth (https://unsloth.ai/) for inspiration for this implementation.

GEGLU backward pass using in-place operations.

This function modifies its input tensors in-place to store results.

**Examples:**

Example 1 (python):
```python
kernels.geglu.geglu_backward(grad_output, gate, up)
```

Example 2 (python):
```python
kernels.geglu.geglu_forward(gate, up)
```

---

## utils.callbacks.profiler

**URL:** https://docs.axolotl.ai/docs/api/utils.callbacks.profiler.html

**Contents:**
- utils.callbacks.profiler
- Classes
  - PytorchProfilerCallback

utils.callbacks.profiler

HF Trainer callback for creating pytorch profiling snapshots

PyTorch Profiler callback to create snapshots of GPU memory usage at specified steps.

**Examples:**

Example 1 (python):
```python
utils.callbacks.profiler.PytorchProfilerCallback(
    steps_to_profile=5,
    profiler_steps_start=0,
)
```

---

## kernels.lora

**URL:** https://docs.axolotl.ai/docs/api/kernels.lora.html

**Contents:**
- kernels.lora
- Classes
  - LoRA_MLP
    - Methods
      - backward
        - Parameters
        - Returns
      - forward
        - Parameters
        - Returns

Module for definition of Low-Rank Adaptation (LoRA) Triton kernels.

See “LoRA: Low-Rank Adaptation of Large Language Models” (https://arxiv.org/abs/2106.09685).

Credit to unsloth (https://unsloth.ai/) for inspiration for this implementation.

Optimized LoRA MLP implementation.

Performs backward pass computation for LoRA MLP.

Forward pass for LoRA MLP.

Optimized LoRA implementation for output projection.

Backward pass computing gradients for LoRA output projection.

Forward pass for output projection with LoRA.

Optimized LoRA QKV implementation with quantization support.

Implements efficient computation of query, key, value projections with LoRA, supporting quantization and memory optimization.

Backward pass computing gradients for LoRA QKV.

Forward pass computing Q, K, V projections with LoRA.

Applies LoRA to MLP layer with GEGLU activation.

Applies LoRA to MLP layer with SwiGLU activation.

Applies LoRA to output projection layer.

Applies LoRA to compute Query, Key, Value projections.

Gets LoRA parameters from a projection module.

Efficient fused matmul + LoRA computation.

**Examples:**

Example 1 (python):
```python
kernels.lora.LoRA_MLP()
```

Example 2 (python):
```python
kernels.lora.LoRA_MLP.backward(ctx, grad_output)
```

Example 3 (python):
```python
kernels.lora.LoRA_MLP.forward(
    ctx,
    X,
    gate_weight,
    gate_bias,
    gate_quant,
    gate_A,
    gate_B,
    gate_scale,
    up_weight,
    up_bias,
    up_quant,
    up_A,
    up_B,
    up_scale,
    down_weight,
    down_bias,
    down_quant,
    down_A,
    down_B,
    down_scale,
    activation_fn,
    activation_fn_backward,
    inplace=True,
)
```

Example 4 (python):
```python
kernels.lora.LoRA_O()
```

---

## monkeypatch.trainer_fsdp_optim

**URL:** https://docs.axolotl.ai/docs/api/monkeypatch.trainer_fsdp_optim.html

**Contents:**
- monkeypatch.trainer_fsdp_optim
- Functions
  - patch_training_loop_for_fsdp

monkeypatch.trainer_fsdp_optim

fix for FSDP optimizer save in trainer w 4.47.0

monkeypatch for fixing the training loop for fsdp with optimizer save

**Examples:**

Example 1 (python):
```python
monkeypatch.trainer_fsdp_optim.patch_training_loop_for_fsdp()
```

---

## utils.schemas.multimodal

**URL:** https://docs.axolotl.ai/docs/api/utils.schemas.multimodal.html

**Contents:**
- utils.schemas.multimodal
- Classes
  - MultiModalConfig
    - Methods
      - convert_image_resize_algorithm

utils.schemas.multimodal

Pydantic models for multimodal-related configuration

Multi-modal configuration subset

Convert the image resize algorithm to a PIL.Image.Resampling enum.

**Examples:**

Example 1 (python):
```python
utils.schemas.multimodal.MultiModalConfig()
```

Example 2 (python):
```python
utils.schemas.multimodal.MultiModalConfig.convert_image_resize_algorithm(
    image_resize_algorithm,
)
```

---

## prompt_strategies.dpo.llama3

**URL:** https://docs.axolotl.ai/docs/api/prompt_strategies.dpo.llama3.html

**Contents:**
- prompt_strategies.dpo.llama3
- Functions
  - argilla_chat
  - icr
  - intel
  - ultra

prompt_strategies.dpo.llama3

DPO strategies for llama-3 chat template

for argilla/dpo-mix-7k conversations

chatml transforms for datasets with system, input, chosen, rejected ex. https://huggingface.co/datasets/argilla/distilabel-intel-orca-dpo-pairs

For Intel Orca DPO Pairs

for ultrafeedback binarized conversations

**Examples:**

Example 1 (python):
```python
prompt_strategies.dpo.llama3.argilla_chat(cfg, **kwargs)
```

Example 2 (python):
```python
prompt_strategies.dpo.llama3.icr(cfg, **kwargs)
```

Example 3 (python):
```python
prompt_strategies.dpo.llama3.intel(cfg, **kwargs)
```

Example 4 (python):
```python
prompt_strategies.dpo.llama3.ultra(cfg, **kwargs)
```

---

## core.chat.format.shared

**URL:** https://docs.axolotl.ai/docs/api/core.chat.format.shared.html

**Contents:**
- core.chat.format.shared

core.chat.format.shared

shared functions for format transforms

---

## monkeypatch.llama_expand_mask

**URL:** https://docs.axolotl.ai/docs/api/monkeypatch.llama_expand_mask.html

**Contents:**
- monkeypatch.llama_expand_mask

monkeypatch.llama_expand_mask

expands the binary attention mask per 3.2.2 of https://arxiv.org/pdf/2107.02027.pdf

---

## core.chat.messages

**URL:** https://docs.axolotl.ai/docs/api/core.chat.messages.html

**Contents:**
- core.chat.messages
- Classes
  - ChatFormattedChats
  - Chats
  - MessageContentTypes
  - MessageContents
  - MessageRoles
  - Messages
  - PreferenceChats
  - SpecialToken

internal message representations of chat messages

Chat formatted chats with formatter and optional train on inputs

top level data structure for chat conversations

Message content types for text, image, audio, tool calls, and tool responses

Message contents with type, value, metadata, weight, newline, and end of contents

Message roles for the system, user, assistant, and tools

Messages with role, content, metadata, weight, and chat formatting

representation for preference data for chat

Special tokens for beginning of string and end of string

Tool with description, function, and parameters

Tool call contents with name, arguments, and optional id

Tool call function with name and arguments

Tool response contents with name, content, and optional id

**Examples:**

Example 1 (python):
```python
core.chat.messages.ChatFormattedChats()
```

Example 2 (python):
```python
core.chat.messages.Chats()
```

Example 3 (python):
```python
core.chat.messages.MessageContentTypes()
```

Example 4 (python):
```python
core.chat.messages.MessageContents()
```

---

## core.datasets.transforms.chat_builder

**URL:** https://docs.axolotl.ai/docs/api/core.datasets.transforms.chat_builder.html

**Contents:**
- core.datasets.transforms.chat_builder
- Functions
  - chat_message_transform_builder
    - Parameters
    - Returns

core.datasets.transforms.chat_builder

This module contains a function that builds a transform that takes a row from the dataset and converts it to a Chat.

Builds a transform that takes a row from the dataset and converts it to a Chat

**Examples:**

Example 1 (python):
```python
core.datasets.transforms.chat_builder.chat_message_transform_builder(
    train_on_inputs=False,
    conversations_field='messages',
    message_field_role=None,
    message_field_content=None,
    message_field_training=None,
)
```

---

## utils.chat_templates

**URL:** https://docs.axolotl.ai/docs/api/utils.chat_templates.html

**Contents:**
- utils.chat_templates

This module provides functionality for selecting chat templates based on user choices. These templates are used for formatting messages in a conversation.

---

## core.trainers.dpo.trainer

**URL:** https://docs.axolotl.ai/docs/api/core.trainers.dpo.trainer.html

**Contents:**
- core.trainers.dpo.trainer
- Classes
  - AxolotlDPOTrainer
    - Methods
      - push_to_hub

core.trainers.dpo.trainer

DPO trainer for axolotl

Extend the base DPOTrainer for axolotl helpers.

Overwrite the push_to_hub method in order to force-add the tags when pushing the model on the Hub. Please refer to ~transformers.Trainer.push_to_hub for more details.

**Examples:**

Example 1 (python):
```python
core.trainers.dpo.trainer.AxolotlDPOTrainer(*args, dataset_tags=None, **kwargs)
```

Example 2 (python):
```python
core.trainers.dpo.trainer.AxolotlDPOTrainer.push_to_hub(*args, **kwargs)
```

---

## monkeypatch.gradient_checkpointing.offload_disk

**URL:** https://docs.axolotl.ai/docs/api/monkeypatch.gradient_checkpointing.offload_disk.html

**Contents:**
- monkeypatch.gradient_checkpointing.offload_disk
- Classes
  - Disco
    - Methods
      - backward
      - forward
      - get_instance
  - DiskOffloadManager
    - Methods
      - cleanup

monkeypatch.gradient_checkpointing.offload_disk

DISCO - DIsk-based Storage and Checkpointing with Optimized prefetching

Disco: DIsk-based Storage and Checkpointing with Optimized prefetching Advanced disk-based gradient checkpointer with prefetching.

Backward pass that loads activations from disk with prefetching

Forward pass that offloads activations to disk asynchronously

Get or create the offload manager

Manages offloaded tensors and handles prefetching in a separate thread. Includes synchronization to prevent race conditions.

Clean up all temp files and stop prefetch thread with proper synchronization

Clean up a specific tensor file after it’s been used

Load tensor from disk or prefetch cache with proper synchronization

Save tensor to disk asynchronously and return file path with thread-safe operations

Trigger prefetching of the next N tensors with proper synchronization

Wait for a tensor to be saved to disk

**Examples:**

Example 1 (python):
```python
monkeypatch.gradient_checkpointing.offload_disk.Disco()
```

Example 2 (python):
```python
monkeypatch.gradient_checkpointing.offload_disk.Disco.backward(
    ctx,
    *grad_outputs,
)
```

Example 3 (python):
```python
monkeypatch.gradient_checkpointing.offload_disk.Disco.forward(
    ctx,
    forward_function,
    hidden_states,
    *args,
    prefetch_size=1,
    prefetch_to_gpu=True,
    save_workers=4,
)
```

Example 4 (python):
```python
monkeypatch.gradient_checkpointing.offload_disk.Disco.get_instance(
    prefetch_size=1,
    prefetch_to_gpu=True,
    save_workers=4,
)
```

---

## utils.samplers.multipack

**URL:** https://docs.axolotl.ai/docs/api/utils.samplers.multipack.html

**Contents:**
- utils.samplers.multipack
- Classes
  - MultipackBatchSampler
    - Methods
      - efficiency
      - gather_efficiency
        - Returns
      - gather_len_batches
      - generate_batches
        - Parameters

utils.samplers.multipack

Multipack Batch Sampler - An efficient batch sampler for packing variable-length sequences into fixed-capacity batches to optimize memory usage and training throughput.

Batch sampler class for efficient packing of variable-length sequences

This sampler packs sequences into fixed-capacity bins (batches) to maximize GPU memory utilization and training throughput by reducing padding.

It supports both parallel packing (using FFD algorithm) and sequential packing (preserving original sequence order).

Calculate the packing efficiency (ratio of tokens used to total token slots). Higher is better - 1.0 would mean perfect packing with no wasted space.

Gather and synchronize packing efficiency estimates across all distributed ranks.

Gather and synchronize batch counts across all distributed ranks. Returns the minimum number of batches available on any rank.

Generate packed batches for training.

Set the epoch number, used for reproducible shuffling across epochs

Sequential allocator that preserves example order.

First-fit-decreasing bin packing algorithm check.

Checks if sequences with the given lengths could fit in the specified number of bins.

Pack a group of sequences into bins using First-Fit Decreasing algorithm.

Pack sequences into bins using parallel processing.

Returns: List of bins, where each bin contains indices of sequences assigned to it.

**Examples:**

Example 1 (python):
```python
utils.samplers.multipack.MultipackBatchSampler(
    sampler,
    batch_size,
    batch_max_len,
    lengths,
    packing_efficiency_estimate=1.0,
    drop_last=True,
    num_count_samples=4,
    sequential=False,
    group_size=100000,
    bin_size=200,
    num_processes=None,
    safe_mode=True,
    mp_start_method='fork',
    **kwargs,
)
```

Example 2 (python):
```python
utils.samplers.multipack.MultipackBatchSampler.efficiency()
```

Example 3 (python):
```python
utils.samplers.multipack.MultipackBatchSampler.gather_efficiency()
```

Example 4 (python):
```python
utils.samplers.multipack.MultipackBatchSampler.gather_len_batches(num)
```

---

## core.trainers.mixins.scheduler

**URL:** https://docs.axolotl.ai/docs/api/core.trainers.mixins.scheduler.html

**Contents:**
- core.trainers.mixins.scheduler
- Classes
  - SchedulerMixin
    - Methods
      - create_scheduler
        - Parameters

core.trainers.mixins.scheduler

Module for Axolotl trainer scheduler mixin

Mixin class for scheduler setup in CausalTrainer.

Set up the scheduler. The optimizer of the trainer must have been set up either before this method is called or passed as an argument.

**Examples:**

Example 1 (python):
```python
core.trainers.mixins.scheduler.SchedulerMixin()
```

Example 2 (python):
```python
core.trainers.mixins.scheduler.SchedulerMixin.create_scheduler(
    num_training_steps,
    optimizer=None,
)
```

---

## utils.collators.batching

**URL:** https://docs.axolotl.ai/docs/api/utils.collators.batching.html

**Contents:**
- utils.collators.batching
- Classes
  - BatchSamplerDataCollatorForSeq2Seq
  - DataCollatorForSeq2Seq
    - Parameters
  - PretrainingBatchSamplerDataCollatorForSeq2Seq
  - V2BatchSamplerDataCollatorForSeq2Seq

utils.collators.batching

Data collators for axolotl to pad labels and position_ids for packed sequences

Collator for multipack specific to the using the BatchSampler

Data collator that will dynamically pad the inputs received, as well as the labels and position_ids

Collator for multipack specific to the using the BatchSampler

Collator for multipack specific to the using the BatchSampler

**Examples:**

Example 1 (python):
```python
utils.collators.batching.BatchSamplerDataCollatorForSeq2Seq(
    tokenizer,
    model=None,
    padding=True,
    max_length=None,
    pad_to_multiple_of=None,
    label_pad_token_id=-100,
    position_pad_token_id=0,
    return_tensors='pt',
)
```

Example 2 (python):
```python
utils.collators.batching.DataCollatorForSeq2Seq(
    tokenizer,
    model=None,
    padding=True,
    max_length=None,
    pad_to_multiple_of=None,
    label_pad_token_id=-100,
    position_pad_token_id=0,
    return_tensors='pt',
)
```

Example 3 (python):
```python
utils.collators.batching.PretrainingBatchSamplerDataCollatorForSeq2Seq(
    *args,
    multipack_attn=True,
    **kwargs,
)
```

Example 4 (python):
```python
utils.collators.batching.V2BatchSamplerDataCollatorForSeq2Seq(
    tokenizer,
    model=None,
    padding=True,
    max_length=None,
    pad_to_multiple_of=None,
    label_pad_token_id=-100,
    position_pad_token_id=0,
    return_tensors='pt',
    squash_position_ids=False,
)
```

---

## prompt_strategies.orcamini

**URL:** https://docs.axolotl.ai/docs/api/prompt_strategies.orcamini.html

**Contents:**
- prompt_strategies.orcamini
- Classes
  - OrcaMiniPrompter

prompt_strategies.orcamini

Prompt Strategy for finetuning Orca Mini (v2) models see also https://huggingface.co/psmathur/orca_mini_v2_7b for more information

Use dataset type: orcamini in conig.yml to use this prompt style.

Compared to the alpaca_w_system.open_orca dataset type, this one specifies the system prompt with “### System:”.

Not suited/tested for multiple-turn conversations without further adjustments.

Adjusted Prompter for Orca Mini (v2) datasets

**Examples:**

Example 1 (python):
```python
prompt_strategies.orcamini.OrcaMiniPrompter(
    prompt_style=PromptStyle.INSTRUCT.value,
)
```

---

## prompt_strategies.dpo.chat_template

**URL:** https://docs.axolotl.ai/docs/api/prompt_strategies.dpo.chat_template.html

**Contents:**
- prompt_strategies.dpo.chat_template
- Functions
  - argilla_chat
    - Parameters
    - Returns
    - Dataset format

prompt_strategies.dpo.chat_template

DPO prompt strategies for using tokenizer chat templates.

DPO chat template strategy for argilla-style datasets.

For argilla-style datasets where chosen/rejected contain full conversations instead of single response messages. Extracts the conversation history from the chosen field and formats both chosen/rejected responses using the configured chat template.

{ “chosen”: [ {“role”: “user”, “content”: “…”}, {“role”: “assistant”, “content”: “…”} ], “rejected”: [ {“role”: “user”, “content”: “…”}, {“role”: “assistant”, “content”: “…”} ] }

**Examples:**

Example 1 (python):
```python
prompt_strategies.dpo.chat_template.argilla_chat(cfg, dataset_idx=0, **kwargs)
```

---

## monkeypatch.relora

**URL:** https://docs.axolotl.ai/docs/api/monkeypatch.relora.html

**Contents:**
- monkeypatch.relora
- Classes
  - ReLoRACallback

Implements the ReLoRA training procedure from https://arxiv.org/abs/2307.05695, minus the initial full fine-tune.

Callback to merge LoRA weights into the base model and save full-weight checkpoints

**Examples:**

Example 1 (python):
```python
monkeypatch.relora.ReLoRACallback(cfg)
```

---

## monkeypatch.transformers_fa_utils

**URL:** https://docs.axolotl.ai/docs/api/monkeypatch.transformers_fa_utils.html

**Contents:**
- monkeypatch.transformers_fa_utils
- Functions
  - fixed_fa_peft_integration_check
    - Parameters

monkeypatch.transformers_fa_utils

see https://github.com/huggingface/transformers/pull/35834

PEFT usually casts the layer norms in float32 for training stability reasons therefore the input hidden states gets silently casted in float32. Hence, we need cast them back in float16 / bfloat16 just to be sure everything works as expected. This might slowdown training & inference so it is recommended to not cast the LayerNorms!

**Examples:**

Example 1 (python):
```python
monkeypatch.transformers_fa_utils.fixed_fa_peft_integration_check(
    query,
    key,
    value,
    target_dtype=None,
    preferred_dtype=None,
)
```

---

## utils.collators.mm_chat

**URL:** https://docs.axolotl.ai/docs/api/utils.collators.mm_chat.html

**Contents:**
- utils.collators.mm_chat
- Classes
  - MultiModalChatDataCollator

utils.collators.mm_chat

Collators for multi-modal chat messages and packing

Collator for multi-modal chat messages

**Examples:**

Example 1 (python):
```python
utils.collators.mm_chat.MultiModalChatDataCollator(
    tokenizer,
    processing_strategy,
    packing=False,
    return_tensors='pt',
    padding=True,
    pad_to_multiple_of=None,
)
```

---

## utils.lora

**URL:** https://docs.axolotl.ai/docs/api/utils.lora.html

**Contents:**
- utils.lora
- Functions
  - get_lora_merged_state_dict
    - Parameters
    - Returns

module to get the state dict of a merged lora model

Create and return a state_dict that has the LoRA deltas merged into the base model’s weights, without modifying model in place.

**Examples:**

Example 1 (python):
```python
utils.lora.get_lora_merged_state_dict(model)
```

---

## utils.model_shard_quant

**URL:** https://docs.axolotl.ai/docs/api/utils.model_shard_quant.html

**Contents:**
- utils.model_shard_quant
- Functions
  - load_and_quantize

utils.model_shard_quant

module to handle loading model on cpu/meta device for FSDP

Loads value tensor into submodule of module, optionally skipping skip_names and converting to dtype.

Quantizes Params4bit on device then places on “cpu” if to_cpu=True or “meta” if to_meta=True.

**Examples:**

Example 1 (python):
```python
utils.model_shard_quant.load_and_quantize(
    module,
    name,
    value,
    device=None,
    dtype=None,
    skip_names=None,
    to_cpu=False,
    to_meta=False,
    verbose=False,
    quant_method='bnb',
)
```

---

## monkeypatch.gradient_checkpointing.offload_cpu

**URL:** https://docs.axolotl.ai/docs/api/monkeypatch.gradient_checkpointing.offload_cpu.html

**Contents:**
- monkeypatch.gradient_checkpointing.offload_cpu
- Classes
  - CPU_Offloaded_Gradient_Checkpointer

monkeypatch.gradient_checkpointing.offload_cpu

CPU offloaded checkpointing

Saves VRAM by smartly offloading to RAM. Tiny hit to performance, since we mask the movement via non blocking calls.

**Examples:**

Example 1 (python):
```python
monkeypatch.gradient_checkpointing.offload_cpu.CPU_Offloaded_Gradient_Checkpointer(
)
```

---

## core.builders.base

**URL:** https://docs.axolotl.ai/docs/api/core.builders.base.html

**Contents:**
- core.builders.base
- Classes
  - TrainerBuilderBase
    - Methods
      - get_post_trainer_create_callbacks

Base class for trainer builder

Base class for trainer builder.

Callbacks added after the trainer is created, usually b/c these need access to the trainer

**Examples:**

Example 1 (python):
```python
core.builders.base.TrainerBuilderBase(cfg, model, tokenizer, processor=None)
```

Example 2 (python):
```python
core.builders.base.TrainerBuilderBase.get_post_trainer_create_callbacks(trainer)
```

---

## core.builders.rl

**URL:** https://docs.axolotl.ai/docs/api/core.builders.rl.html

**Contents:**
- core.builders.rl
- Classes
  - HFRLTrainerBuilder

Builder for RLHF trainers

Trainer factory class for TRL-based RLHF trainers (e.g. DPO)

**Examples:**

Example 1 (python):
```python
core.builders.rl.HFRLTrainerBuilder(cfg, model, tokenizer, processor=None)
```

---

## utils.schemas.integrations

**URL:** https://docs.axolotl.ai/docs/api/utils.schemas.integrations.html

**Contents:**
- utils.schemas.integrations
- Classes
  - CometConfig
  - GradioConfig
  - LISAConfig
  - MLFlowConfig
  - OpenTelemetryConfig
  - RayConfig
  - WandbConfig

utils.schemas.integrations

Pydantic models for Axolotl integrations

Comet configuration subset

Gradio configuration subset

LISA configuration subset

MLFlow configuration subset

OpenTelemetry configuration subset

Ray launcher configuration subset

Wandb configuration subset

**Examples:**

Example 1 (python):
```python
utils.schemas.integrations.CometConfig()
```

Example 2 (python):
```python
utils.schemas.integrations.GradioConfig()
```

Example 3 (python):
```python
utils.schemas.integrations.LISAConfig()
```

Example 4 (python):
```python
utils.schemas.integrations.MLFlowConfig()
```

---

## utils.data.sft

**URL:** https://docs.axolotl.ai/docs/api/utils.data.sft.html

**Contents:**
- utils.data.sft
- Functions
  - prepare_datasets
    - Parameters
    - Returns

Data handling specific to SFT.

Prepare training and evaluation datasets based on configuration.

**Examples:**

Example 1 (python):
```python
utils.data.sft.prepare_datasets(cfg, tokenizer, processor=None)
```

---

## integrations.liger.args

**URL:** https://docs.axolotl.ai/docs/api/integrations.liger.args.html

**Contents:**
- integrations.liger.args
- Classes
  - LigerArgs

integrations.liger.args

Module for handling LIGER input arguments.

Input args for LIGER.

**Examples:**

Example 1 (python):
```python
integrations.liger.args.LigerArgs()
```

---

## monkeypatch.mixtral

**URL:** https://docs.axolotl.ai/docs/api/monkeypatch.mixtral.html

**Contents:**
- monkeypatch.mixtral

Patches to support multipack for mixtral

---

## cli.preprocess

**URL:** https://docs.axolotl.ai/docs/api/cli.preprocess.html

**Contents:**
- cli.preprocess
- Functions
  - do_cli
    - Parameters
  - do_preprocess
    - Parameters

CLI to run preprocessing of a dataset.

Parses axolotl config, CLI args, and calls do_preprocess.

Preprocesses dataset specified in axolotl config.

**Examples:**

Example 1 (python):
```python
cli.preprocess.do_cli(config=Path('examples/'), **kwargs)
```

Example 2 (python):
```python
cli.preprocess.do_preprocess(cfg, cli_args)
```

---

## prompt_strategies.kto.llama3

**URL:** https://docs.axolotl.ai/docs/api/prompt_strategies.kto.llama3.html

**Contents:**
- prompt_strategies.kto.llama3
- Functions
  - argilla_chat
  - intel
  - ultra

prompt_strategies.kto.llama3

KTO strategies for llama-3 chat template

for argilla/kto-mix-15k conversations

For Intel Orca KTO ex: argilla/distilabel-intel-orca-kto

for ultrafeedback binarized conversations ex: argilla/ultrafeedback-binarized-preferences-cleaned-kto

**Examples:**

Example 1 (python):
```python
prompt_strategies.kto.llama3.argilla_chat(cfg, **kwargs)
```

Example 2 (python):
```python
prompt_strategies.kto.llama3.intel(cfg, **kwargs)
```

Example 3 (python):
```python
prompt_strategies.kto.llama3.ultra(cfg, **kwargs)
```

---

## prompt_strategies.orpo.chat_template

**URL:** https://docs.axolotl.ai/docs/api/prompt_strategies.orpo.chat_template.html

**Contents:**
- prompt_strategies.orpo.chat_template
- Classes
  - Message
  - MessageList
  - ORPODatasetParsingStrategy
    - Methods
      - get_chosen_conversation_thread
      - get_prompt
      - get_rejected_conversation_thread
  - ORPOPrompter

prompt_strategies.orpo.chat_template

chatml prompt tokenization strategy for ORPO

Strategy to parse chosen rejected dataset into messagelist

Dataset structure mappings

Map the data to extract everything up to the last turn

Dataset structure mappings

Single Turn prompter for ORPO

rejected_input_ids input_ids rejected_attention_mask attention_mask rejected_labels labels

chatml transforms for datasets with system, input, chosen, rejected

**Examples:**

Example 1 (python):
```python
prompt_strategies.orpo.chat_template.Message()
```

Example 2 (python):
```python
prompt_strategies.orpo.chat_template.MessageList()
```

Example 3 (python):
```python
prompt_strategies.orpo.chat_template.ORPODatasetParsingStrategy()
```

Example 4 (python):
```python
prompt_strategies.orpo.chat_template.ORPODatasetParsingStrategy.get_chosen_conversation_thread(
    prompt,
)
```

---

## loaders.processor

**URL:** https://docs.axolotl.ai/docs/api/loaders.processor.html

**Contents:**
- loaders.processor

Processor loading functionality for multi-modal models

---

## utils.callbacks.comet_

**URL:** https://docs.axolotl.ai/docs/api/utils.callbacks.comet_.html

**Contents:**
- utils.callbacks.comet_
- Classes
  - SaveAxolotlConfigtoCometCallback

utils.callbacks.comet_

Comet module for trainer callbacks

Callback to save axolotl config to comet

**Examples:**

Example 1 (python):
```python
utils.callbacks.comet_.SaveAxolotlConfigtoCometCallback(axolotl_config_path)
```

---




### Other

# Axolotl - Other

**Pages:** 26

---

## Mixed Precision Training

**URL:** https://docs.axolotl.ai/docs/mixed_precision.html

**Contents:**
- Mixed Precision Training
- 1 FP16 Mixed Precision
  - 1.1 Overview
  - 1.2 Configuration
  - 1.3 FP16 Considerations
- 2 BF16 Mixed Precision
  - 2.1 Overview
  - 2.2 Configuration
- 3 FP8 Mixed Precision
  - 3.1 What is FP8?

Mixed precision training uses lower precision data types to reduce memory usage and increase training speed while maintaining model quality. Axolotl supports several mixed precision formats:

FP16 is the traditional half-precision format, supported on older GPUs but can be less numerically stable than BF16.

BF16 (Brain Float 16) offers better numerical stability than FP16 and is the recommended mixed precision format for modern GPUs. It provides the same dynamic range as FP32 while using half the memory.

FP8 support is experimental and requires compatible hardware (H100, H200) and recent PyTorch versions with TorchAO.

FP8 (8-bit floating point) can provide significant time savings compared to FP16/BF16 while maintaining training stability. Axolotl’s implementation uses PyTorch’s TorchAO library with “tensorwise” scaling strategy.

Add to your YAML config:

torch.compile is critical for FP8 performance

FP8 training requires torch_compile: true to see meaningful speedups. Without compilation, FP8 may actually be slower and use more memory than FP16/BF16.

For FSDP (Fully Sharded Data Parallel) training:

Always validate your mixed precision setup:

See examples/llama-3/3b-fp8-fsdp2.yaml for an optimized example config. Enabling FP8 mixed precision + FP8 all-gather training results in ~10% faster iterations per second vs. BF16 for a relatively small (3B param) model

For more information on multi-GPU training, see our Multi-GPU guide.

**Examples:**

Example 1 (yaml):
```yaml
# Automatic BF16 detection (recommended)
bf16: auto

# Or explicitly enable
bf16: true

# For evaluation with BF16
bf16: full  # Equivalent to bf16_full_eval in the HF trainer
```

Example 2 (yaml):
```yaml
# Enable FP8 mixed precision
fp8: true

# Optional: Enable FP8 for FSDP all-gather operations
fp8_enable_fsdp_float8_all_gather: true

# Enable torch.compile (almost always necessary for FP8 speedups)
torch_compile: true
```

Example 3 (yaml):
```yaml
fp8: true
fp8_enable_fsdp_float8_all_gather: true

torch_compile: true

# FSDP configuration
fsdp_version: 2
fsdp_config:
  offload_params: false
  cpu_ram_efficient_loading: true
  auto_wrap_policy: TRANSFORMER_BASED_WRAP
  transformer_layer_cls_to_wrap: LlamaDecoderLayer
  state_dict_type: FULL_STATE_DICT
  reshard_after_forward: true
```

---

## FAQ

**URL:** https://docs.axolotl.ai/docs/faq.html

**Contents:**
- FAQ
  - General
  - Chat templates

Q: The trainer stopped and hasn’t progressed in several minutes.

A: Usually an issue with the GPUs communicating with each other. See the NCCL doc

A: This usually happens when you run out of system RAM.

Q: exitcode: -7 while using deepspeed

A: Try upgrading deepspeed w: pip install -U deepspeed

Q: AttributeError: ‘DummyOptim’ object has no attribute ‘step’

Q: ModuleNotFoundError: No module named ‘mpi4py’ using single GPU with deepspeed

A: You may be using deepspeed with single gpu. Please remove the deepspeed: section in the yaml file or --deepspeed CLI flag.

Q: The codes is stuck on saving preprocessed datasets.

A: This is usually an issue with the GPU. This can be resolved through setting the os environment variable CUDA_VISIBLE_DEVICES=0. If you are on runpod, this is usually a pod issue. Starting a new pod should take care of it.

Q: Received mismatch error on merge adapters / loading adapters between torch.Size of checkpoint and model.

A: This is likely due to vocab size mismatch. By default, Axolotl expands the model’s embeddings if the tokenizer has more tokens than the model. Please use the axolotl merge-lora command to merge the adapters instead of using your own scripts.

On the other hand, if the model has more tokens than the tokenizer, Axolotl does not shrink the model’s embeddings unless shrink_embeddings: true is set in the config.

Q: How to call Axolotl via custom python scripts?

A: Since Axolotl is just Python, please see src/axolotl/cli/main.py on how each command is called.

Q: How to know the value to use for fsdp_transformer_layer_cls_to_wrap?

A: This is the class name of the transformer layer to wrap with FSDP. For example, for LlamaForCausalLM, the value is LlamaDecoderLayer. To find this for a specific model, check the model’s PreTrainedModel definition and look for _no_split_modules variable in the modeling_<model_name>.py file within transformers library.

Q: ValueError: Asking to pad but the tokenizer does not have a padding token. Please select a token to use as pad_token

A: This is because the tokenizer does not have a padding token. Please add a padding token to the tokenizer via:

Q: IterableDataset error or KeyError: 'input_ids' when using preprocess CLI

A: This is because you may be using preprocess CLI with pretraining_dataset: or skip_prepare_dataset: true respectively. Please use axolotl train CLI directly instead as these datasets are prepared on demand.

Q: vLLM is not working with Axolotl

A: We currently recommend torch 2.6.0 for use with vllm. Please ensure you use the right version. For Docker, please use the main-py3.11-cu124-2.6.0 tag.

Q: FA2 2.8.0 undefined symbol runtime error on CUDA 12.4

A: There seems to be a wheel issue with FA2 2.8.0 on CUDA 12.4. Try CUDA 12.6 instead or downgrade to FA2 2.7.4. Please refer to the upstream issue: https://github.com/Dao-AILab/flash-attention/issues/1717.

Q: Can we mix text and text+image datasets for VLM training?

A: Yes, you can for newer VLM arch. The ones that would not work are LLaVA / Pixtral arch. If you notice one not working, please let us know!

Q: Why is memory/max_* different from nvidia-smi?

A: We use torch APIs to retrieve this information. You can see https://docs.pytorch.org/docs/stable/notes/cuda.html#cuda-memory-management for more information.

Q: jinja2.exceptions.UndefinedError: 'dict object' has no attribute 'content' / 'role' / ____

A: This means that the property mapping for the stated attribute does not exist when building chat_template prompt. For example, if no attribute 'content', please check you have added the correct mapping for content under message_property_mappings.

Q: Empty template generated for turn ___

A: The content is empty for that turn.

Q: Could not find content start/end boundary for turn __

A: The specific turn’s start/end could not be detected. Please ensure you have set the eos_token following your chat_template. Otherwise, this could be a chat_template which doesn’t use proper boundaries for each turn (like system). On the rare occurrence, make sure your content is not [[dummy_message]]. Please let us know about this.

Q: Content end boundary is before start boundary for turn ___

A: This is an edge case which should not occur. Please create an Issue if this happens.

Q: Content end boundary is the same as start boundary for turn ___. This is likely an empty turn.

A: This is likely an empty turn.

Q: The EOS token is incorrectly being masked or not being masked / EOS token __ not found in chat template.

A: There can be two reasons:

Q: “chat_template choice is tokenizer_default but tokenizer’s chat_template is null. Please add a chat_template in tokenizer config”

A: This is because the tokenizer does not have a chat template. Please add a chat template in the tokenizer config. See chat_template for more details.

Q: The EOT token(s) are incorrectly being masked or not being masked / EOT token __ not found in chat template.

A: There can be two reasons:

Q: EOT token encoding failed. Please check if the token is valid and can be encoded.

A: There could be some issue with the tokenizer or unicode encoding. Please raise an issue with examples with the EOT token & tokenizer causing the issue.

Q: EOT token __ is encoded as multiple tokens.

A: This is because the EOT token is encoded as multiple tokens which can cause unexpected behavior. Please add it under tokens: or (recommended) override unused added_tokens via added_tokens_overrides:.

Q: Conflict between train_on_eos and train_on_eot. eos_token is in eot_tokens and train_on_eos != train_on_eot

A: This is because the EOS token is in the eot_tokens: while mismatch between train_on_eos: and train_on_eot:. This will cause one to override the other. Please ensure that train_on_eos: and train_on_eot: are the same or remove the EOS token from eot_tokens:.

Q: If eot_tokens: is not provided, what happens?

A: If eot_tokens: is not provided, the default behavior is the same as before. EOS tokens used to delimit turns are masked/unmasked depending on whether the turn is trainable.

Internally, eot_tokens: tokenizer.eos_token and train_on_eot: train_on_eos (which defaults to turn). This transition helps clarify the naming and behavior of EOT/EOS tokens.

Q: Data processing error: CAS service error

A: Try disabling XET with export HF_HUB_DISABLE_XET=1

Q: torch._inductor.exc.LoweringException: NoValidChoicesError: No choices to select, please consider adding ATEN into max_autotune_gemm_backends config (defined in torch/_inductor/config.py) to allow at least one choice.

A: Depending on the version of torch, you may need to include this in your YAML:

**Q: ValueError("Backward pass should have cleared tracker of all tensors")

A: This may happen due to edge cases in using the modern OffloadActivations context manager for CUDA streams. If you encounter this error, you may have success using the naive implementation with offload_activations: legacy in your YAML.

**Q: Error parsing tool_calls arguments as JSON.

A: There is an error parsing string arguments to a dict. Please check your dataset and the error message for more details.

**Examples:**

Example 1 (yaml):
```yaml
special_tokens:
  # str. If you're not sure, set to same as `eos_token`.
  pad_token: "..."
```

Example 2 (yaml):
```yaml
flex_attn_compile_kwargs:
  dynamic: false
  mode: max-autotune-no-cudagraphs
```

---

## Installation

**URL:** https://docs.axolotl.ai/docs/installation.html

**Contents:**
- Installation
- 1 Requirements
- 2 Installation Methods
  - 2.1 PyPI Installation (Recommended)
  - 2.2 uv Installation
  - 2.3 Edge/Development Build
  - 2.4 Docker
- 3 Cloud Environments
  - 3.1 Cloud GPU Providers
  - 3.2 Google Colab

This guide covers all the ways you can install and set up Axolotl for your environment.

Please make sure to have Pytorch installed before installing Axolotl in your local environment.

Follow the instructions at: https://pytorch.org/get-started/locally/

For Blackwell GPUs, please use Pytorch 2.7.0 and CUDA 12.8.

We use --no-build-isolation in order to detect the installed PyTorch version (if installed) in order not to clobber it, and so that we set the correct version of dependencies that are specific to the PyTorch version or other installed co-dependencies.

uv is a fast, reliable Python package installer and resolver built in Rust. It offers significant performance improvements over pip and provides better dependency resolution, making it an excellent choice for complex environments.

Install uv if not already installed

Choose your CUDA version to use with PyTorch; e.g. cu124, cu126, cu128, then create the venv and activate

Install PyTorch - PyTorch 2.6.0 recommended

Install axolotl from PyPi

For the latest features between releases:

For development with Docker:

For Blackwell GPUs, please use axolotlai/axolotl:main-py3.11-cu128-2.7.0 or the cloud variant axolotlai/axolotl-cloud:main-py3.11-cu128-2.7.0.

Please refer to the Docker documentation for more information on the different Docker images that are available.

For providers supporting Docker:

See Section 6 for Mac-specific issues.

We recommend using WSL2 (Windows Subsystem for Linux) or Docker.

Install PyTorch: https://pytorch.org/get-started/locally/

(Optional) Login to Hugging Face:

If you encounter installation issues, see our FAQ and Debugging Guide.

**Examples:**

Example 1 (bash):
```bash
pip3 install -U packaging setuptools wheel ninja
pip3 install --no-build-isolation axolotl[flash-attn,deepspeed]
```

Example 2 (bash):
```bash
curl -LsSf https://astral.sh/uv/install.sh | sh
source $HOME/.local/bin/env
```

Example 3 (bash):
```bash
export UV_TORCH_BACKEND=cu126
uv venv --no-project --relocatable
source .venv/bin/activate
```

Example 4 (bash):
```bash
uv pip install packaging setuptools wheel
uv pip install torch==2.6.0
uv pip install awscli pydantic
```

---

## Dataset Preprocessing

**URL:** https://docs.axolotl.ai/docs/dataset_preprocessing.html

**Contents:**
- Dataset Preprocessing
- Overview
  - What are the benefits of pre-processing?
  - What are the edge cases?

Dataset pre-processing is the step where Axolotl takes each dataset you’ve configured alongside the dataset format and prompt strategies to:

The processing of the datasets can happen one of two ways:

When training interactively or for sweeps (e.g. you are restarting the trainer often), processing the datasets can oftentimes be frustratingly slow. Pre-processing will cache the tokenized/formatted datasets according to a hash of dependent training parameters so that it will intelligently pull from its cache when possible.

The path of the cache is controlled by dataset_prepared_path: and is often left blank in example YAMLs as this leads to a more robust solution that prevents unexpectedly reusing cached data.

If dataset_prepared_path: is left empty, when training, the processed dataset will be cached in a default path of ./last_run_prepared/, but will ignore anything already cached there. By explicitly setting dataset_prepared_path: ./last_run_prepared, the trainer will use whatever pre-processed data is in the cache.

Let’s say you are writing a custom prompt strategy or using a user-defined prompt template. Because the trainer cannot readily detect these changes, we cannot change the calculated hash value for the pre-processed dataset.

If you have dataset_prepared_path: ... set and change your prompt templating logic, it may not pick up the changes you made and you will be training over the old prompt.

---

## Inference and Merging

**URL:** https://docs.axolotl.ai/docs/inference.html

**Contents:**
- Inference and Merging
- 1 Quick Start
  - 1.1 Basic Inference
- 2 Advanced Usage
  - 2.1 Gradio Interface
  - 2.2 File-based Prompts
  - 2.3 Memory Optimization
- 3 Merging LoRA Weights
  - 3.1 Memory Management for Merging
- 4 Tokenization

This guide covers how to use your trained models for inference, including model loading, interactive testing, merging adapters, and common troubleshooting steps.

Use the same config used for training on inference/merging.

Launch an interactive web interface:

Process prompts from a text file:

For large models or limited memory:

Merge LoRA adapters with the base model:

Tokenization mismatches between training and inference are a common source of problems.

Verify inference tokenization by decoding tokens before model input

Compare token IDs between training and inference

Configure special tokens in your YAML:

For more details, see our debugging guide.

**Examples:**

Example 1 (bash):
```bash
axolotl inference your_config.yml --lora-model-dir="./lora-output-dir"
```

Example 2 (bash):
```bash
axolotl inference your_config.yml --base-model="./completed-model"
```

Example 3 (bash):
```bash
axolotl inference your_config.yml --gradio
```

Example 4 (bash):
```bash
cat /tmp/prompt.txt | axolotl inference your_config.yml \
  --base-model="./completed-model" --prompter=None
```

---

## MultiModal / Vision Language Models (BETA)

**URL:** https://docs.axolotl.ai/docs/multimodal.html

**Contents:**
- MultiModal / Vision Language Models (BETA)
- Supported Models
- Usage
  - Mllama
  - Llama4
  - Pixtral
  - Llava-1.5
  - Mistral-Small-3.1
  - Magistral-Small-2509
  - Voxtral

Multimodal support is limited and doesn’t have full feature parity.

Here are the hyperparams you’ll need to use to finetune a multimodal model.

Please see examples folder for full configs.

Some of our chat_templates have been extended to support broader dataset types. This should not break any existing configs.

As of now, we do not truncate nor drop samples based on sequence_len as each arch has different ways to process non-text tokens. We are looking for help on this.

Please make sure to install vision lib via pip install 'mistral-common[opencv]==1.8.5'

Please make sure to install vision lib via pip install 'mistral-common[opencv]==1.8.5'

Please make sure to install audio lib via pip3 install librosa==0.11.0 'mistral_common[audio]==1.8.3'

The Gemma3-1B model is a text-only model, so please train as regular text model.

For multi-modal 4B/12B/27B models, use the following config:

The model’s initial loss and grad norm will be very high. We suspect this to be due to the Conv in the vision layers.

Please make sure to install timm via pip3 install timm==1.0.17

Please make sure to install num2words via pip3 install num2words==0.5.14

Please uninstall causal-conv1d via pip3 uninstall -y causal-conv1d

For multi-modal datasets, we adopt an extended chat_template format similar to OpenAI’s Message format.

For backwards compatibility:

For image loading, you can use the following keys within content alongside "type": "image":

For audio loading, you can use the following keys within content alongside "type": "audio":

You may need to install librosa via pip3 install librosa==0.11.0.

This is not well tested at the moment. We welcome contributors!

For video loading, you can use the following keys within content alongside "type": "video":

Here is an example of a multi-modal dataset:

PIL could not retrieve the file at url using requests. Please check for typo. One alternative reason is that the request is blocked by the server.

**Examples:**

Example 1 (yaml):
```yaml
processor_type: AutoProcessor

skip_prepare_dataset: true
remove_unused_columns: false  # leave columns in place as they are needed to handle image embeddings during training
sample_packing: false  # not yet supported with multimodal

chat_template:  # see in next section if specified

# example dataset
datasets:
  - path: HuggingFaceH4/llava-instruct-mix-vsft
    type: chat_template
    split: train[:1%]

# (optional) if doing lora, only finetune the Language model,
# leave the vision model and vision tower frozen
# load_in_8bit: true
adapter: lora
lora_target_modules: 'model.language_model.layers.[\d]+.(mlp|cross_attn|self_attn).(up|down|gate|q|k|v|o)_proj'

# (optional) if you want to resize images to a set size
image_size: 512
image_resize_algorithm: bilinear
```

Example 2 (yaml):
```yaml
base_model: meta-llama/Llama-3.2-11B-Vision-Instruct

chat_template: llama3_2_vision
```

Example 3 (yaml):
```yaml
base_model: meta-llama/Llama-4-Scout-17B-16E-Instruct

chat_template: llama4
```

Example 4 (yaml):
```yaml
base_model: mistralai/Pixtral-12B-2409

chat_template: pixtral
```

---

## Reward Modelling

**URL:** https://docs.axolotl.ai/docs/reward_modelling.html

**Contents:**
- Reward Modelling
  - Overview
  - (Outcome) Reward Models
  - Process Reward Models (PRM)

Reward modelling is a technique used to train models to predict the reward or value of a given input. This is particularly useful in reinforcement learning scenarios where the model needs to evaluate the quality of its actions or predictions. We support the reward modelling techniques supported by trl.

Outcome reward models are trained using data which contains preference annotations for an entire interaction between the user and model (e.g. rather than per-turn or per-step). For improved training stability, you can use the center_rewards_coefficient parameter to encourage mean-zero reward outputs (see TRL docs).

Bradley-Terry chat templates expect single-turn conversations in the following format:

Check out our PRM blog.

Process reward models are trained using data which contains preference annotations for each step in a series of interactions. Typically, PRMs are trained to provide reward signals over each step of a reasoning trace and are used for downstream reinforcement learning.

Please see stepwise_supervised for more details on the dataset format.

**Examples:**

Example 1 (yaml):
```yaml
base_model: google/gemma-2-2b
model_type: AutoModelForSequenceClassification
num_labels: 1
tokenizer_type: AutoTokenizer

reward_model: true
chat_template: gemma
datasets:
  - path: argilla/distilabel-intel-orca-dpo-pairs
    type: bradley_terry.chat_template

val_set_size: 0.1
eval_steps: 100
```

Example 2 (json):
```json
{
    "system": "...", // optional
    "input": "...",
    "chosen": "...",
    "rejected": "..."
}
```

Example 3 (yaml):
```yaml
base_model: Qwen/Qwen2.5-3B
model_type: AutoModelForTokenClassification
num_labels: 2

process_reward_model: true
datasets:
  - path: trl-lib/math_shepherd
    type: stepwise_supervised
    split: train

val_set_size: 0.1
eval_steps: 100
```

---

## RLHF (Beta)

**URL:** https://docs.axolotl.ai/docs/rlhf.html

**Contents:**
- RLHF (Beta)
- Overview
- RLHF using Axolotl
  - DPO
    - chatml.argilla
    - chatml.argilla_chat
    - chatml.icr
    - chatml.intel
    - chatml.prompt_pairs
    - chatml.ultra

Reinforcement Learning from Human Feedback is a method whereby a language model is optimized from data using human feedback. Various methods include, but not limited to:

This is a BETA feature and many features are not fully implemented. You are encouraged to open new PRs to improve the integration and functionality.

We rely on the TRL library for implementations of various RL training methods, which we wrap around to expose in axolotl. Each method has their own supported ways of loading datasets and prompt formats.

You can find what each method supports by going into src/axolotl/prompt_strategies/{method} where {method} is one of our supported methods. The type: can be retrieved from {method}.{function_name}.

DPO supports the following types with the following dataset format:

For custom behaviors,

The input format is a simple JSON input with customizable fields based on the above config.

As IPO is just DPO with a different loss function, all supported dataset formats for DPO are also supported for IPO.

Paper: https://arxiv.org/abs/2403.07691

ORPO supports the following types with the following dataset format:

KTO supports the following types with the following dataset format:

For custom behaviors,

The input format is a simple JSON input with customizable fields based on the above config.

Check out our GRPO cookbook.

In the latest GRPO implementation, vLLM is used to significantly speedup trajectory generation during training. In this example, we’re using 4 GPUs - 2 for training, and 2 for vLLM:

Make sure you’ve installed the correct version of vLLM by including it as an extra when installing axolotl, e.g. pip install axolotl[vllm].

Your vLLM instance will now attempt to spin up, and it’s time to kick off training utilizing our remaining two GPUs. In another terminal, execute:

Due to TRL’s implementation with vLLM, the vLLM instance must use the last N GPUs instead of the first N GPUs. This is why in the example above, we use CUDA_VISIBLE_DEVICES=2,3 for the vLLM instance.

GRPO uses custom reward functions and transformations. Please have them ready locally.

For example, to load OpenAI’s GSM8K and use a random reward for completions:

To see other examples of custom reward functions, please see TRL GRPO Docs.

To see all configs, please see TRLConfig.

The DAPO paper and subsequently Dr. GRPO paper proposed an alternative loss function for GRPO to remediate the penalty in longer responses.

For more information, see GRPO docs.

SimPO uses CPOTrainer but with alternative loss function.

This method uses the same dataset format as DPO.

TRL supports auto-unwrapping PEFT models for RL training paradigms which rely on a reference model. This significantly reduces memory pressure as an additional refreference model does not need to be loaded, and reference model log-probabilities can be obtained by disabling PEFT adapters. This is enabled by default. To turn it off, pass the following config:

**Examples:**

Example 1 (yaml):
```yaml
rl: dpo
datasets:
  - path: Intel/orca_dpo_pairs
    split: train
    type: chatml.intel
  - path: argilla/ultrafeedback-binarized-preferences
    split: train
    type: chatml
```

Example 2 (json):
```json
{
    "system": "...", // optional
    "instruction": "...",
    "chosen_response": "...",
    "rejected_response": "..."
}
```

Example 3 (json):
```json
{
    "chosen": [
        {"role": "user", "content": "..."},
        {"role": "assistant", "content": "..."}
    ],
    "rejected": [
        {"role": "user", "content": "..."},
        {"role": "assistant", "content": "..."}
    ]
}
```

Example 4 (json):
```json
{
    "system": "...", // optional
    "input": "...",
    "chosen": "...",
    "rejected": "..."
}
```

---

## LoRA Optimizations

**URL:** https://docs.axolotl.ai/docs/lora_optims.html

**Contents:**
- LoRA Optimizations
- Usage
- Requirements
- Implementation details
  - Custom autograd functions
  - Triton kernels
  - Integration
- Future Work

Inspired by Unsloth, we’ve implemented two optimizations for LoRA and QLoRA fine-tuning, supporting both single GPU and multi-GPU (including the DDP, DeepSpeed, and FSDP2 settings) training. These include (1) SwiGLU and GEGLU activation function Triton kernels, and (2) LoRA MLP and attention custom autograd functions. Our goal was to leverage operator fusion and tensor re-use in order to improve speed and reduce memory usage during the forward and backward passes of these calculations.

We currently support several common model architectures, including (but not limited to):

The set of models we support is currently limited by our attention patching strategy, which assumes (and replaces) specific code blocks for query / key / value and output projections:

Where apply_qkv and apply_o are defined in the axolotl.kernels.lora module.

We welcome testing of other model architectures and / or PRs to expand our patching logic to be compatible with more of them.

Check out our LoRA optimizations blog.

These optimizations can be enabled in your Axolotl config YAML file. The lora_mlp_kernel option enables the optimized MLP path, while lora_qkv_kernel and lora_o_kernel enable the fused query-key-value projection and optimized output projection, respectively.

Currently, LoRA kernels are not supported for RLHF training, only SFT.

Models with pre-existing LoRA adapters that use Dropout or have bias terms may need to be re-finetuned without these features in order to be useful.

The LoRA MLP autograd function optimizes the entire MLP computation path. It fuses the LoRA and base weight computations together and provides a single, efficient backward pass for the entire MLP block.

For attention components, similar optimizations are provided through a function that handles the query, key, and value projections, and a function that handles the output projection. They are designed to work with the existing transformers attention implementation via some monkey-patching logic.

Two activation functions (SwiGLU and GeGLU) are implemented with Triton kernels for improved speed and memory performance. These kernels handle both the forward and backward passes.

The custom autograd functions and Triton kernels are designed to work together. The autograd function manages the high-level computation flow and gradient tracking, while calling the Triton kernels for the activation function computation. During the backward pass, the kernel computes both the activation output and the required gradients, which the autograd function then uses to compute the final gradients for the entire computation path.

**Examples:**

Example 1 (python):
```python
ORIGINAL_QKV_CODE = """
    query_states = self.q_proj(hidden_states).view(hidden_shape).transpose(1, 2)
    key_states = self.k_proj(hidden_states).view(hidden_shape).transpose(1, 2)
    value_states = self.v_proj(hidden_states).view(hidden_shape).transpose(1, 2)
""".lstrip(
    "\n"
)

ORIGINAL_O_CODE = """
    attn_output = self.o_proj(attn_output)
""".lstrip(
    "\n"
)
```

Example 2 (python):
```python
PATCHED_QKV_CODE = """
    query_states, key_states, value_states = self.apply_qkv(hidden_states)
    query_states = query_states.view(hidden_shape).transpose(1, 2)
    key_states = key_states.view(hidden_shape).transpose(1, 2)
    value_states = value_states.view(hidden_shape).transpose(1, 2)
""".lstrip(
    "\n"
)

PATCHED_O_CODE = """
    attn_output = self.apply_o(attn_output)
""".lstrip(
    "\n"
)
```

Example 3 (yaml):
```yaml
lora_mlp_kernel: true
lora_qkv_kernel: true
lora_o_kernel: true
```

---

## Quantization with torchao

**URL:** https://docs.axolotl.ai/docs/quantize.html

**Contents:**
- Quantization with torchao
- Configuring Quantization in Axolotl

Quantization is a technique to lower the memory footprint of your model, potentially at the cost of accuracy or model performance. We support quantizing your model using the torchao library. Quantization is supported for both post-training quantization (PTQ) and quantization-aware training (QAT).

We do not currently support quantization techniques such as GGUF/GPTQ,EXL2 at the moment.

Quantization is configured using the quantization key in your configuration file.

Once quantization is complete, your quantized model will be saved in the {output_dir}/quantized directory.

You may also use the quantize command to quantize a model which has been trained with QAT - you can do this by using the existing QAT configuration file which you used to train the model:

This ensures that an identical quantization configuration is used to quantize the model as was used to train it.

If you have configured pushing to hub with hub_model_id, your model hub name will have the quantization schema appended to it, e.g. axolotl-ai-cloud/qat-nvfp4-llama3B will become axolotl-ai-cloud/qat-nvfp4-llama3B-nvfp4w

**Examples:**

Example 1 (yaml):
```yaml
base_model: # The path to the model to quantize.
quantization:
  activation_dtype: # Optional[str] = "int8". Fake quantization layout to use for activation quantization. Valid options are "int4", "int8", "float8"
  weight_dtype: # Optional[str] = "int8". Fake quantization layout to use for weight quantization. Valid options are "int4", "fp8", and "nvfp4".
  group_size: # Optional[int] = 32. The number of elements in each group for per-group fake quantization
  quantize_embedding: # Optional[bool] = False. Whether to quantize the embedding layer.

output_dir:  # The path to the output directory.
```

Example 2 (yaml):
```yaml
# qat.yml
qat:
  activation_dtype: int8
  weight_dtype: int4
  group_size: 256

output_dir: # The path to the output directory used during training where the final checkpoint has been saved.
```

Example 3 (bash):
```bash
axolotl quantize qat.yml
```

---

## NCCL

**URL:** https://docs.axolotl.ai/docs/nccl.html

**Contents:**
- NCCL

NVIDIA NCCL is a library to facilitate and optimize multi-GPU communication operations, such as broadcast, all-gather, reduce, all-reduce, etc. Broadly, NCCL configuration is highly environment-specific and is configured via several environment variables. A common NCCL-related problem occurs when a long-running operation times out causing the training process to abort:

Often, this timeout will happen after 30 minutes (the default setting) and is accompanied by below-average power consumption with near 100% GPU utilization before the error is raised. Nvidia recommends disabling PCI access control services (ACS) as a possible solution if this is available to you.

Forcing cross-GPU communication via NVLink may help without increasing timeouts. To verify that your configuration is leveraging NVLink run the following command:

To force NCCL to use NVLink, simply set this in the environment:

If NVLink is not available in your environment there are other options for NCCL_P2P_LEVEL in the table below:

To validate that acceptable data transfer speeds exist for your training job, running NCCL Tests can help pinpoint bottlenecks, for example:

It can be useful when debugging NCCL communication timeouts to activate additional logging in both PyTorch and NCCL:

Finally, if you believe your training job needs more time you can increase the timeout past 30 minutes by setting the ddp_timeout value in the Axolotl configuration. See PyTorch init_process_group for documentation on this value.

**Examples:**

Example 1 (unknown):
```unknown
Watchdog caught collective operation timeout: WorkNCCL(SeqNum=42, OpType=ALLGATHER, Timeout(ms)=1800000) ran for 1806948 milliseconds before timing out.
```

Example 2 (bash):
```bash
nvidia-smi nvlink --status
```

Example 3 (bash):
```bash
export NCCL_P2P_LEVEL=NVL
```

Example 4 (bash):
```bash
./build/all_reduce_perf -b 8 -e 128M -f 2 -g 3
```

---

## Multi Node

**URL:** https://docs.axolotl.ai/docs/multi-node.html

**Contents:**
- Multi Node
- Accelerate
- Raytrain
- Torchrun
  - Option 1: New Axolotl CLI with launcher args (Recommended)
  - Option 2: Direct torchrun (Legacy)

The below are three ways to train multi-node in Axolotl.

Each machine needs a copy of Axolotl, we suggest using the same commit to ensure compatibility.

You will also need to have the same configuration file for your model on each machine.

Make sure the main machine is reachable by other machines.

You will need to create a configuration for accelerate, either by using accelerate config and follow the instructions or you can use one of the preset below:

~/.cache/huggingface/accelerate/default_config.yaml

Configure your model to use FSDP in the Axolotl yaml. For example:

All you have to do now is launch using accelerate as you would usually do on each machine and voila, the processes will start once you have launched accelerate on every machine.

Please see ray train doc here.

If you are using Infiniband, we recommend torchrun to utilize the full bandwidth.

Set the following env (change buffersize/socketname depending on your system):

Run the following on each node:

Please make sure to substitute the placeholder variables:

The new CLI approach (Option 1) is recommended as it provides consistent argument handling and works seamlessly with other Axolotl CLI features.

More info on the available configs can be found on the Pytorch docs here

**Examples:**

Example 1 (yaml):
```yaml
compute_environment: LOCAL_MACHINE
debug: false
distributed_type: FSDP
downcast_bf16: 'no'
machine_rank: 0 # Set to 0 for the main machine, increment by one for other machines
main_process_ip: 10.0.0.4 # Set to main machine's IP
main_process_port: 5000
main_training_function: main
mixed_precision: bf16
num_machines: 2 # Change to the number of machines
num_processes: 4 # That's the total number of GPUs, (for example: if you have 2 machines with 4 GPU, put 8)
rdzv_backend: static
same_network: true
tpu_env: []
tpu_use_cluster: false
tpu_use_sudo: false
use_cpu: false
```

Example 2 (yaml):
```yaml
fsdp_version: 2
fsdp_config:
  offload_params: true
  state_dict_type: FULL_STATE_DICT
  auto_wrap_policy: TRANSFORMER_BASED_WRAP
  transformer_layer_cls_to_wrap: LlamaDecoderLayer
  reshard_after_forward: true
```

Example 3 (bash):
```bash
export NCCL_IB_DISABLE=0
export NCCL_SOCKET_IFNAME="eth0,en,eth,em,bond"
export NCCL_BUFFSIZE=2097152
```

Example 4 (bash):
```bash
axolotl train config.yaml --launcher torchrun -- --nnodes $num_nodes --nproc_per_node $gpu_per_node --rdzv_id $rdzv_id --rdzv_backend c10d --rdzv_endpoint "$head_node_ip:$head_node_port"
```

---

## Dataset Loading

**URL:** https://docs.axolotl.ai/docs/dataset_loading.html

**Contents:**
- Dataset Loading
- Overview
- Loading Datasets
  - Local dataset
    - Files
    - Directory
      - Loading entire directory
      - Loading specific files in directory
  - HuggingFace Hub
    - Folder uploaded

Datasets can be loaded in a number of different ways depending on the how it is saved (the extension of the file) and where it is stored.

We use the datasets library to load datasets and a mix of load_dataset and load_from_disk to load them.

You may recognize the similar named configs between load_dataset and the datasets section of the config file.

Do not feel overwhelmed by the number of options here. A lot of them are optional. In fact, the most common config to use would be path and sometimes data_files.

This matches the API of datasets.load_dataset, so if you’re familiar with that, you will feel right at home.

For HuggingFace’s guide to load different dataset types, see here.

For full details on the config, see config-reference.qmd.

You can set multiple datasets in the config file by more than one entry under datasets.

To load a JSON file, you would do something like this:

Which translates to the following config:

In the example above, it can be seen that we can just point the path to the file or directory along with the ds_type to load the dataset.

This works for CSV, JSON, Parquet, and Arrow files.

If path points to a file and ds_type is not specified, we will automatically infer the dataset type from the file extension, so you could omit ds_type if you’d like.

If you’re loading a directory, you can point the path to the directory.

Then, you have two options:

You do not need any additional configs.

We will attempt to load in the following order: - datasets saved with datasets.save_to_disk - loading entire directory of files (such as with parquet/arrow files)

Provide data_files with a list of files to load.

The method you use to load the dataset depends on how the dataset was created, whether a folder was uploaded directly or a HuggingFace Dataset was pushed.

If you’re using a private dataset, you will need to enable the hf_use_auth_token flag in the root-level of the config file.

This would mean that the dataset is a single file or file(s) uploaded to the Hub.

This means that the dataset is created as a HuggingFace Dataset and pushed to the Hub via datasets.push_to_hub.

There are some other configs which may be required like name, split, revision, trust_remote_code, etc depending on the dataset.

Via the storage_options config under load_dataset, you can load datasets from remote filesystems like S3, GCS, Azure, and OCI.

This is currently experimental. Please let us know if you run into any issues!

The only difference between the providers is that you need to prepend the path with the respective protocols.

For directory, we load via load_from_disk.

Prepend the path with s3://.

The credentials are pulled in the following order:

We assume you have credentials setup and not using anonymous access. If you want to use anonymous access, let us know! We may have to open a config option for this.

Other environment variables that can be set can be found in boto3 docs

Prepend the path with gs:// or gcs://.

The credentials are loaded in the following order:

Prepend the path with adl://.

Ensure you have the following environment variables set:

Prepend the path with abfs:// or az://.

Ensure you have the following environment variables set:

Other environment variables that can be set can be found in adlfs docs

Prepend the path with oci://.

It would attempt to read in the following order:

Other environment variables:

Please see the ocifs docs.

The path should start with https://.

This must be publically accessible.

Now that you know how to load datasets, you can learn more on how to load your specific dataset format into your target output format dataset formats docs.

**Examples:**

Example 1 (yaml):
```yaml
datasets:
  - path:
    name:
    data_files:
    split:
    revision:
    trust_remote_code:
```

Example 2 (yaml):
```yaml
datasets:
  - path: /path/to/your/dataset
  - path: /path/to/your/other/dataset
```

Example 3 (python):
```python
from datasets import load_dataset

dataset = load_dataset("json", data_files="data.json")
```

Example 4 (yaml):
```yaml
datasets:
  - path: data.json
    ds_type: json
```

---

## Multi-GPU

**URL:** https://docs.axolotl.ai/docs/multi-gpu.html

**Contents:**
- Multi-GPU
- 1 Overview
- 2 DeepSpeed
  - 2.1 Configuration
  - 2.2 Usage
  - 2.3 ZeRO Stages
- 3 Fully Sharded Data Parallel (FSDP)
  - 3.1 Migrating from FSDP1 to FSDP2
    - 3.1.1 Config mapping
  - 3.2 FSDP1 (deprecated)

This guide covers advanced training configurations for multi-GPU setups using Axolotl.

Axolotl supports several methods for multi-GPU training:

Add to your YAML config:

We provide default configurations for:

Choose the configuration that offloads the least amount to memory while still being able to fit on VRAM for best performance.

Start from Stage 1 -> Stage 2 -> Stage 3.

FSDP2 is recommended for new users. FSDP1 is deprecated and will be removed in an upcoming release of Axolotl.

To migrate your config from FSDP1 to FSDP2, you must use the fsdp_version top-level config field to specify the FSDP version, and also follow the config field mapping below to update field names.

For more details, please see the migration guide in the torchtitan repo. In Axolotl, if you were using the following FSDP1 config:

You can migrate to the following FSDP2 config:

Using fsdp to configure FSDP is deprecated and will be removed in an upcoming release of Axolotl. Please use fsdp_config as above instead.

We support sequence parallelism (SP) via the ring-flash-attention project. This allows one to split up sequences across GPUs, which is useful in the event that a single sequence causes OOM errors during model training.

See our dedicated guide for more information.

For combining FSDP with QLoRA, see our dedicated guide.

Please see docs for more info.

For NCCL-related problems, see our NCCL troubleshooting guide.

For more detailed troubleshooting, see our debugging guide.

**Examples:**

Example 1 (yaml):
```yaml
deepspeed: deepspeed_configs/zero1.json
```

Example 2 (bash):
```bash
# Fetch deepspeed configs (if not already present)
axolotl fetch deepspeed_configs

# Passing arg via config
axolotl train config.yml

# Passing arg via cli
axolotl train config.yml --deepspeed deepspeed_configs/zero1.json
```

Example 3 (yaml):
```yaml
fsdp_version: 1
fsdp_config:
  fsdp_offload_params: false
  fsdp_cpu_ram_efficient_loading: true
  fsdp_auto_wrap_policy: TRANSFORMER_BASED_WRAP
  fsdp_transformer_layer_cls_to_wrap: Qwen3DecoderLayer
  fsdp_state_dict_type: FULL_STATE_DICT
  fsdp_sharding_strategy: FULL_SHARD
```

Example 4 (yaml):
```yaml
fsdp_version: 2
fsdp_config:
  offload_params: false
  cpu_ram_efficient_loading: true
  auto_wrap_policy: TRANSFORMER_BASED_WRAP
  transformer_layer_cls_to_wrap: Qwen3DecoderLayer
  state_dict_type: FULL_STATE_DICT
  reshard_after_forward: true
```

---

## Ray Train

**URL:** https://docs.axolotl.ai/docs/ray-integration.html

**Contents:**
- Ray Train
- Ray cluster setup
- Sanity check
- Configuring training with Ray Train
- Launching training

Axolotl supports using Ray as an alternative to accelerate for orchestrating training. This is especially useful for multi-node training since you only have to setup code and dependencies in a single node and launch training as if you were using a single node.

With the --use-ray CLI flag, Axolotl will use Ray Train’s TorchTrainer to run training.

A prerequisite using the Ray Train integration is to setup a Ray cluster on your desired node(s). For a detailed guide on how you can get started with ray clusters, check the official Ray docs here.

Every Ray cluster has one head node and a set of worker nodes. The head node is just like any other worker node, but it also runs certain special processes related to scheduling and orchestration. Ray-enabled scripts are run on the head node and depending on the resources (number of CPUs, GPUs, etc) they request, will be scheduled to run certain tasks on the worker nodes. For more on key concepts behind a Ray cluster, you can refer this doc.

To run a sanity check on whether your ray cluster is setup properly, execute the following on the head node:

The output should have a summary of your Ray cluster - list of all the nodes in your cluster, the number of CPUs and GPUs in your cluster, etc. For example, if you have a cluster with 1 CPU-only head node and 2 4xL40S worker nodes, the output can look like this:

You should also be able to see the same on the Ray dashboard.

You can find an example configuration at configs/llama-3/lora-1b-ray.yaml.

The key parameters to note here are:

You can simply run the following command on the head node:

This will launch training on the head node and workers will be scheduled automatically by Ray Train to run on the appropriate head or worker nodes.

You can also monitor training progress on the Ray dashboard.

Coming back to the example on a Ray cluster with 1 head node and 2 4xL40S worker nodes, let’s say you want to make use of all 8 GPUs. You would be able to just set ray_num_workers: 8 and run the previous command. The Cluster tab will show the following:

**Examples:**

Example 1 (unknown):
```unknown
Node status
---------------------------------------------------------------
Active:
 1 head
Idle:
 2 4xL40S:48CPU-384GB
Pending:
 (no pending nodes)
Recent failures:
 (no failures)

Resources
---------------------------------------------------------------
Usage:
 0.0/96.0 CPU
 0.0/8.0 GPU
 0B/800.00GiB memory
 0B/229.57GiB object_store_memory

Demands:
 (no resource demands)
```

Example 2 (yaml):
```yaml
use_ray: true
ray_num_workers: 4
# optional
resources_per_worker:
    GPU: 1
```

Example 3 (yaml):
```yaml
resources_per_worker:
    accelerator_type:L40S: 0.001
```

Example 4 (bash):
```bash
axolotl train examples/llama-3/lora-1b-ray.yml --use-ray
```

---

## Sequence Parallelism

**URL:** https://docs.axolotl.ai/docs/sequence_parallelism.html

**Contents:**
- Sequence Parallelism
- When to Use Sequence Parallelism
- Configuration
- Implementation Details
- Requirements
- Limitations
- Example
- Sample Packing with Sequence Parallelism
- Effect on Batch Size

Sequence parallelism is a technique that splits sequences across multiple GPUs, allowing you to train with very long sequences that wouldn’t fit on a single GPU. Each GPU processes a different portion of the sequence, and the results are aggregated through a ring communication pattern.

Use sequence parallelism when:

To enable sequence parallelism, add the following to your configuration file:

The context_parallel_size should be a divisor of the total number of GPUs. For example:

When sequence parallelism is enabled:

To use sequence parallelism, you need:

This will train the Llama 3 8B model with 8K context length, with each sequence split into 2 subsequences of length 4096 across 2 GPUs.

Sequence parallelism is compatible with Axolotl’s sample packing functionality. When using both features together:

When using sequence parallelism, your effective global batch size is divided by the context_parallel_size. This happens because:

For example: - With 8 GPUs and no sequence parallelism: 8 different batches processed per step - With 8 GPUs and context_parallel_size=4: Only 2 different batches processed per step (each split across 4 GPUs) - If your per-GPU micro_batch_size is 2, the global batch size decreases from 16 to 4

**Examples:**

Example 1 (yaml):
```yaml
# Set to a divisor (> 1) of the number of GPUs available
context_parallel_size: 4  # Split sequences across 4 GPUs
# Optional; strides across the key dimension. Larger values use more memory but should make training faster.
heads_k_stride: 1
# Optional; one of "varlen_llama3" or "batch_ring". Defaults to
# "varlen_llama3" when `sample_packing: true`, and "batch_ring" otherwise.
ring_attn_func:
```

Example 2 (yaml):
```yaml
base_model: meta-llama/Llama-3-8B-Instruct
sequence_len: 8192

...

context_parallel_size: 4  # Split each sequence into 4 parts, one per GPU
# Optional; strides across the key dimension. Larger values use more memory but should make training faster.
heads_k_stride: 1
# Optional; one of "varlen_llama3" or "batch_ring". Defaults to
# "varlen_llama3" when `sample_packing: true`, and "batch_ring" otherwise.
ring_attn_func:

...
```

---

## Quantization Aware Training (QAT)

**URL:** https://docs.axolotl.ai/docs/qat.html

**Contents:**
- Quantization Aware Training (QAT)
- Overview
- Configuring QAT in Axolotl

Quantization Aware Training (QAT) is a technique for improving the accuracy of models which are quantized by applying “fake” quantizations to the model’s weights (and optionally, activations) during training. This fake quantization allows for the model to adjust for noise introduced by the quantization, so when the model is eventually quantized, the accuracy loss is minimized. We use the quantization techniques implemented in torchao to provide support for QAT and post-training quantization (PTQ) in axolotl.

We recommend reviewing the excellent QAT tutorial in the torchtune library, and the QAT documentation in the torchao library, for more details.

To enable QAT in axolotl, add the following to your configuration file:

We support the following quantization schemas:

Once you have finished training, you must quantize your model by using the same quantization configuration which you used to train the model with. You can use the quantize command to do this.

**Examples:**

Example 1 (yaml):
```yaml
qat:
  activation_dtype: # Optional[str] = "int8". Fake quantization layout to use for activation quantization. Valid options are "int4", "int8", "float8"
  weight_dtype: # Optional[str] = "int8". Fake quantization layout to use for weight quantization. Valid options are "int4", "fp8", and "nvfp4".
  group_size: # Optional[int] = 32. The number of elements in each group for per-group fake quantization
  fake_quant_after_n_steps: # Optional[int] = None. The number of steps to apply fake quantization after
```

---

## FSDP + QLoRA

**URL:** https://docs.axolotl.ai/docs/fsdp_qlora.html

**Contents:**
- FSDP + QLoRA
- Background
- Usage
- Enabling Swap for FSDP2
- Example Config
- References
- Footnotes

Using FSDP with QLoRA is essential for fine-tuning larger (70b+ parameter) LLMs on consumer GPUs. For example, you can use FSDP + QLoRA to train a 70b model on two 24GB GPUs1.

Below, we describe how to use this feature in Axolotl.

To enable QLoRA with FSDP, you need to perform the following steps:

![Tip] See the example config file in addition to reading these instructions.

If available memory is insufficient even after FSDP’s CPU offloading, you can enable swap memory usage by setting cpu_offload_pin_memory: false alongside offload_params: true in FSDP config.

This disables memory pinning, allowing FSDP to use disk swap space as fallback. Disabling memory pinning itself incurs performance overhead, and actually having to use swap adds more, but it may enable training larger models that would otherwise cause OOM errors on resource constrained systems.

examples/llama-2/qlora-fsdp.yml contains an example of how to enable QLoRA + FSDP in axolotl.

This was enabled by this work from the Answer.AI team.↩︎

---

## Custom Integrations

**URL:** https://docs.axolotl.ai/docs/custom_integrations.html

**Contents:**
- Custom Integrations
- Cut Cross Entropy
  - Requirements
  - Installation
  - Usage
  - Supported Models
  - Citation
- DenseMixer
- Diffusion LM Training Plugin for Axolotl
  - Overview

Axolotl adds custom features through integrations. They are located within the src/axolotl/integrations directory.

To enable them, please check the respective documentations.

Cut Cross Entropy (CCE) reduces VRAM usage through optimization on the cross-entropy operation during loss calculation.

See https://github.com/apple/ml-cross-entropy

Run the following command to install cut_cross_entropy[transformers] if you don’t have it already.

Please see reference here

Simply add the following to your axolotl YAML config:

Please see reference here

This plugin enables diffusion language model training using an approach inspired by LLaDA (Large Language Diffusion Models) within Axolotl.

LLaDA is a diffusion-based approach to language model training that uses: - Random token masking during training instead of next-token prediction - Bidirectional attention to allow the model to attend to the full context - Importance weighting based on masking probabilities for stable training

This approach can lead to more robust language models with better understanding of bidirectional context.

The plugin is included with Axolotl. See our installation docs.

Train with an example config (Llama‑3.2 1B): - Pretrain: axolotl train examples/llama-3/diffusion-3.2-1b-pretrain.yaml - SFT: axolotl train examples/llama-3/diffusion-3.2-1b-sft.yaml

You can also modify your existing configs to enable / customize diffusion training.

Add the following to your Axolotl config:

And, configure the nested diffusion block (defaults shown):

Any models that support 4D attention masks should work out of the box. If not, please create an issue or open a PR!

During training, tokens are randomly masked: - Sample timestep t uniformly from [0, 1] - Calculate masking probability: p = (1 - eps) * t + eps - Randomly mask tokens with probability p

Loss is computed only on masked tokens with (optional) importance weighting:

When diffusion.generate_samples: true, the plugin generates samples during training:

Samples are logged to console and wandb (if enabled).

Diffusion inference is integrated into the standard Axolotl CLI. Use the same config you trained with and run:

Optionally, pass --gradio to use a simple web interface.

Interactive controls (prefix the prompt with commands): - :complete N → completion mode with N new masked tokens appended (default 64) - :mask R → random masking mode with target mask ratio R in [0.0, 1.0]

The plugin adds (or modifies) several metrics to track diffusion training:

Please see reference here

See https://github.com/ironjr/grokfast

Please see reference here

An example dataset can be found at axolotl-ai-co/evolkit-logprobs-pipeline-75k-v2-sample

Please see reference here

Fine-tune sparsified models in Axolotl using Neural Magic’s LLMCompressor.

This integration enables fine-tuning of models sparsified using LLMCompressor within the Axolotl training framework. By combining LLMCompressor’s model compression capabilities with Axolotl’s distributed training pipelines, users can efficiently fine-tune sparse models at scale.

It uses Axolotl’s plugin system to hook into the fine-tuning flows while maintaining sparsity throughout training.

Axolotl with llmcompressor extras:

Requires llmcompressor >= 0.5.1

This will install all necessary dependencies to fine-tune sparsified models using the integration.

To enable sparse fine-tuning with this integration, include the plugin in your Axolotl config:

This plugin does not apply pruning or sparsification itself — it is intended for fine-tuning models that have already been sparsified.

Pre-sparsified checkpoints can be: - Generated using LLMCompressor - Downloaded from Neural Magic’s Hugging Face page - Any custom LLM with compatible sparsity patterns that you’ve created yourself

To learn more about writing and customizing LLMCompressor recipes, refer to the official documentation: https://github.com/vllm-project/llm-compressor/blob/main/README.md

Setting save_compressed: true in your configuration enables saving models in a compressed format, which: - Reduces disk space usage by approximately 40% - Maintains compatibility with vLLM for accelerated inference - Maintains compatibility with llmcompressor for further optimization (example: quantization)

This option is highly recommended when working with sparse models to maximize the benefits of model compression.

See examples/llama-3/sparse-finetuning.yaml for a complete example.

After fine-tuning your sparse model, you can leverage vLLM for efficient inference. You can also use LLMCompressor to apply additional quantization to your fine-tuned sparse model before inference for even greater performance benefits.:

For more details on vLLM’s capabilities and advanced configuration options, see the official vLLM documentation.

For details on available sparsity and quantization schemes, fine-tuning recipes, and usage examples, visit the official LLMCompressor repository:

https://github.com/vllm-project/llm-compressor

Please see reference here

Run evaluation on model using the popular lm-evaluation-harness library.

See https://github.com/EleutherAI/lm-evaluation-harness

Please see reference here

Liger Kernel provides efficient Triton kernels for LLM training, offering:

See https://github.com/linkedin/Liger-Kernel

Please see reference here

by Eric Hartford, Lucas Atkins, Fernando Fernandes, David Golchinfar

This plugin contains code to freeze the bottom fraction of modules in a model, based on the Signal-to-Noise Ratio (SNR).

See https://github.com/cognitivecomputations/spectrum

Spectrum is a tool for scanning and evaluating the Signal-to-Noise Ratio (SNR) of layers in large language models. By identifying the top n% of layers with the highest SNR, you can optimize training efficiency.

Please see reference here

Plugins can be used to customize the behavior of the training pipeline through hooks. See axolotl.integrations.BasePlugin for the possible hooks.

To add a new integration, please follow these steps:

See src/axolotl/integrations/cut_cross_entropy for a minimal integration example.

If you could not load your integration, please ensure you are pip installing in editable mode.

and correctly spelled the integration name in the config file.

It is not necessary to place your integration in the integrations folder. It can be in any location, so long as it’s installed in a package in your python env.

See this repo for an example: https://github.com/axolotl-ai-cloud/diff-transformer

**Examples:**

Example 1 (bash):
```bash
python scripts/cutcrossentropy_install.py | sh
```

Example 2 (bash):
```bash
pip3 uninstall -y cut-cross-entropy && pip3 install "cut-cross-entropy[transformers] @ git+https://github.com/axolotl-ai-cloud/ml-cross-entropy.git@8a1a0ec"
```

Example 3 (yaml):
```yaml
plugins:
  - axolotl.integrations.cut_cross_entropy.CutCrossEntropyPlugin
```

Example 4 (unknown):
```unknown
@article{wijmans2024cut,
  author       = {Erik Wijmans and
                  Brody Huval and
                  Alexander Hertzberg and
                  Vladlen Koltun and
                  Philipp Kr\"ahenb\"uhl},
  title        = {Cut Your Losses in Large-Vocabulary Language Models},
  journal      = {arXiv},
  year         = {2024},
  url          = {https://arxiv.org/abs/2411.09009},
}
```

---

## Config Reference

**URL:** https://docs.axolotl.ai/docs/config-reference.html

**Contents:**
- Config Reference

**Examples:**

Example 1 (yaml):
```yaml
# Allow overwrite yml config using from cli
strict: bool | None = False
# Resume from a specific checkpoint dir
resume_from_checkpoint: str | None
# If resume_from_checkpoint isn't set and you simply want it to start where it left off.
# Be careful with this being turned on between different models.
auto_resume_from_checkpoints: bool | None
# Resize the model embeddings when new tokens are added to multiples of 32. This is
# reported to improve training speed on some models
resize_token_embeddings_to_32x: bool | None
mean_resizing_embeddings: bool | None = False

# Whether to shrink the embeddings to len(tokenizer). By default, we won't shrink.
shrink_embeddings: bool | None
# Don't upcast the embeddings to float32 when using PEFT. Useful for low-VRAM GPUs
embeddings_skip_upcast: bool | None
# Reinitialize model weights randomly instead of loading pretrained weights
reinit_weights: bool | None

# module to custom trainer class to use for training
trainer_cls: str | None

# Use RL training: 'dpo', 'ipo', 'kto', 'simpo', 'orpo', 'grpo'
rl: RLType | None

trl: TRLConfig | None
  # For TRLConfig:
  # Beta parameter for the RL training. Same as `rl_beta`. Use
  beta: float | None
  # Maximum length of the completion for RL training.
  max_completion_length: int | None

  # Whether to use VLLM for RL training.
  use_vllm: bool = False
  # VLLM mode to use, one of 'server' or 'colocate'
  vllm_mode: Literal['server', 'colocate'] | None
  # Host of the vLLM server to connect to.
  vllm_server_host: str | None = 0.0.0.0
  # Port of the vLLM server to connect to.
  vllm_server_port: int | None = 8000
  # Total timeout (in seconds) to wait for the vLLM server to respond.
  vllm_server_timeout: int | None
  # Regex for vLLM guided decoding.
  vllm_guided_decoding_regex: str | None

  # List of reward functions to load. Paths must be importable from current dir.
  reward_funcs: list[str] | None
  # List of reward weights for the reward functions.
  reward_weights: list[float] | None
  # Number of generations to sample.
  num_generations: int | None
  # Whether to log completions.
  log_completions: bool | None = False
  # Number of completions to print when log_completions is True.
  num_completions_to_print: int | None
  # Controls whether importance sampling ratios are computed at the `'token'` or
  # `'sequence'` level. For GSPO, use `sequence`, default is None which corresponds to
  # the original GRPO paper.
  importance_sampling_level: Literal['sequence', 'token'] | None

  # Whether to sync the reference model.
  sync_ref_model: bool | None = False
  # Mixup alpha for the reference model.
  ref_model_mixup_alpha: float | None = 0.9
  # Sync steps for the reference model.
  ref_model_sync_steps: int | None = 64
  # Whether to scale rewards by their standard deviation.
  scale_rewards: bool = True

  # Sampling temperature for the GRPO policy.
  temperature: float | None
  # Top-p sampling probability for the generation policy.
  top_p: float | None
  # Top-k sampling for the generation policy.
  top_k: int | None
  # Minimum probability for the generation policy.
  min_p: float | None
  # Penalty for tokens that appear in prompt and generated text.
  repetition_penalty: float | None
  # Number of iterations per batch (μ) for GRPO.
  num_iterations: int | None
  # Epsilon value for clipping in the GRPO algorithm.
  epsilon: float | None
  # Upper-bound epsilon value for clipping in the GRPO algorithm.
  epsilon_high: float | None
  # Whether to use Liger loss for GRPO.
  use_liger_loss: bool | None
  # Loss formulation to use. Supported values: grpo, bnpo, dr_grpo.
  loss_type: str | None
  # Whether to exclude truncated completions from loss calculation.
  mask_truncated_completions: bool = False
  # Enable sleep mode for vLLM to offload VRAM when idle
  vllm_enable_sleep_mode: bool | None

vllm: VllmConfig | None
  # For VllmConfig:
  # Device to use for VLLM
  device: str | None = auto
  # Tensor parallel size for VLLM
  tensor_parallel_size: int | None
  # Data parallel size for VLLM
  data_parallel_size: int | None
  # GPU memory utilization for VLLM
  gpu_memory_utilization: float | None = 0.9
  # Data type for VLLM
  dtype: str | None = auto
  # Maximum length of the model context for VLLM
  max_model_len: int | None
  # Enable prefix caching for VLLM
  enable_prefix_caching: bool | None
  # Host for the vLLM server to start on
  host: str | None = 0.0.0.0
  # Port of the vLLM server to start on
  port: int | None = 8000

  # Enable reasoning for VLLM
  enable_reasoning: bool | None
  # Reasoning parser for VLLM
  reasoning_parser: str | None

qat: QATConfig | None
  # For QATConfig:
  # Fake quantization layout to use for activation quantization.
  activation_dtype: TorchAOQuantDType | None
  # Fake quantization layout to use for weight quantization.
  weight_dtype: TorchAOQuantDType = TorchAOQuantDType.int8
  # Quantize embedding
  quantize_embedding: bool | None = False
  # The number of elements in each group for per-group fake quantization
  group_size: int | None = 32
  # The number of steps to apply fake quantization after
  fake_quant_after_n_steps: int | None

quantization: PTQConfig | None
  # For PTQConfig:
  # Fake quantization layout to use for weight quantization.
  weight_dtype: TorchAOQuantDType = TorchAOQuantDType.int8
  # Fake quantization layout to use for activation quantization.
  activation_dtype: TorchAOQuantDType | None
  # Whether to quantize the embedding layer.
  quantize_embedding: bool | None
  # The number of elements in each group for per-group fake quantization
  group_size: int | None = 32

# Reward modelling: `True` or `False`
reward_model: bool | None
# Process reward modelling: `True` or `False`
process_reward_model: bool | None
# Coefficient to incentivize the reward model to output mean-zero rewards (proposed by
# https://huggingface.co/papers/2312.09244, Eq. 2). Recommended value: `0.01`.
center_rewards_coefficient: float | None
num_labels: int | None

# Whether to perform weighting in DPO trainer
dpo_use_weighting: bool | None
dpo_use_logits_to_keep: bool | None
dpo_label_smoothing: float | None
dpo_norm_loss: bool | None
dpo_padding_free: bool | None
dpo_generate_during_eval: bool | None

# A list of one or more datasets to finetune the model with
datasets: Annotated[list[SFTDataset | DPODataset | KTODataset | StepwiseSupervisedDataset], MinLen(1)] | None
  # For SFTDataset:
  # HuggingFace dataset repo | s3:// | gs:// | path to local file or directory
  path: str | None
  # name of dataset split to load from
  split: str | None
  # The type of prompt to use for training. [alpaca, gpteacher, oasst, reflection]
  type: str | UserDefinedPrompterType | None
    # For UserDefinedPrompterType:
    # Custom user instruction prompt
    system_prompt: str | None
    # Use {system} as key to be replaced
    system_format: str | None
    field_system: str | None
    field_instruction: str | None
    field_input: str | None
    field_output: str | None

    # Customizable to be single line or multi-line. Use {instruction}/{input} as key to
    # be replaced. 'format' can include {input}
    format: str | None
    # 'no_input_format' cannot include {input}
    no_input_format: str | None
  input_transform: str | None
  # split dataset into N pieces (use with shards_idx)
  shards: int | None
  # the index of sharded dataset to use
  shards_idx: int | None
  # process dataset in N sequential chunks for memory efficiency (exclusive with
  # `shards`)
  preprocess_shards: int | None
  conversation: str | None

  # The name of the chat template to use for training, following values are supported:
  # tokenizer_default: Uses the chat template that is available in the
  # tokenizer_config.json. If the chat template is not available in the tokenizer, it
  # will raise an error. This is the default.
  # alpaca/inst/chatml/gemma/cohere/llama3/phi_3/deepseek_v2/jamba: These chat templates
  # are available in the axolotl codebase at src/axolotl/utils/chat_templates.py.
  # tokenizer_default_fallback_*: where * is the name of the chat template to fallback
  # to if the tokenizer does not have a chat template else default to tokenizer. E.g.
  # tokenizer_default_fallback_chatml. jinja: Uses a custom jinja template for the chat
  # template. The custom jinja template should be provided in the chat_template_jinja
  # field.
  chat_template: ChatTemplate | str | None
  # Custom jinja chat template or path to jinja file. Used only if `chat_template:
  # jinja` or empty.
  chat_template_jinja: str | None
  # path to source data files
  data_files: str | list[str] | None
  input_format: str | None
  # name of dataset configuration to load
  name: str | None
  # defines the datatype when path is a file
  ds_type: str | None
  # For `completion` datasets only, uses the provided field instead of `text` column
  field: str | None
  field_human: str | None
  field_model: str | None
  # Key containing the messages (default: "messages")
  field_messages: str | None
  # Key containing the tools (default: "tools"). Must be a list[dict] and follow [JSON
  # schema](https://json-schema.org/learn/getting-started-step-by-step).
  field_capabilities: File operations, code editing, terminal access, searchstr | None
  # Key containing the reasoning trace (default: "reasoning_content").
  field_thinking: str | None
  # The key the chat template expects that indicates the reasoning trace.
  template_thinking_key: str | None

  message_field_role: str | None

  message_field_content: str | None
  # Mapping of properties from the input dataset to the chat template. (default:
  # message_property_mappings={'role':'role', 'content':'content'}) If a property exists
  # in the template but not in this mapping, the system will attempt to load it directly
  # from the message using the property name as the key. Example: In the mapping below,
  # 'from' is loaded from input dataset and used as 'role', while 'value' is loaded and
  # used as 'content' in the chat template.
  message_property_mappings: dict[str, str] | None
  # The key in the message turn that indicates via boolean whether tokens of a turn
  # should be considered for training. Useful to selectively train on certain turns
  # besides the `roles_to_train`.
  message_field_training: str | None
  # The key in the message turn that contains the training details. Useful to
  # selectively train on certain tokens in a turn. The value of the key is a List[Dict]
  # containing `begin_offset` (start character index in content), `end_offset` (end
  # character index in content), and `train` (boolean whether to train).
  message_field_training_detail: str | None
  # (for Qwen3 template only) Whether to split the assistant content based on a
  # reasoning trace inside delimited tags
  split_thinking: bool | None
  logprobs_field: str | None
  temperature: float | None
  # Roles to train on. The tokens from these roles will be considered for the loss.
  roles_to_train: list[str] | None
  # Which EOS tokens to train on in the conversation. Possible values are: all: train on
  # all EOS tokens, turn (default): train on the EOS token at the end of each trainable
  # turn, last: train on the last EOS token in the conversation
  train_on_eos: Literal['all', 'turn', 'last'] | None
  # Roles mapping in the messages. The format is {target_role: [source_roles]}. All
  # source roles will be mapped to the target role. The default is: user: ["human",
  # "user"], assistant: ["gpt", "assistant"], system: ["system"], capabilities: File operations, code editing, terminal access, search["tool"]
  roles: dict[str, list[str]] | None
  # Whether to drop the system turn from the dataset. Only works with chat_template.
  # This does not drop the default system message from chat_template if it exists. If
  # you wish to, we recommend using a custom jinja template with the default system
  # message removed or adding a system turn with empty content.
  drop_system_message: bool | None
  # Trust remote code for untrusted source
  trust_remote_code: bool | None = False
  # The specific revision of the dataset to use when loading from the Hugging Face Hub.
  # This can be a commit hash, tag, or branch name. If not specified, the latest version
  # will be used. This parameter is ignored for local datasets.
  revision: str | None

  # For DPODataset:
  path: str | None
  split: str | None
  type: UserDefinedDPOType | str | None
    # For UserDefinedDPOType:
    field_system: str | None
    field_prompt: str | None
    field_chosen: str | None
    field_rejected: str | None
    prompt_format: str | None
    chosen_format: str | None
    rejected_format: str | None
  data_files: list[str] | None
  revision: str | None
  field_messages: str | None

  # For KTODataset:
  path: str | None
  split: str | None
  type: UserDefinedKTOType | str | None
    # For UserDefinedKTOType:
    field_system: str | None
    field_prompt: str | None
    field_completion: str | None
    field_label: bool | None
    prompt_format: str | None
    completion_format: str | None
  data_files: list[str] | None
  trust_remote_code: bool | None = False
  revision: str | None

  # For StepwiseSupervisedDataset:
  path: str | None
  split: str | None
  data_files: list[str] | None
  revision: str | None
  step_separator: str | None
  max_completion_length: int | None
  train_on_last_step_only: bool | None

# A list of one or more datasets to eval the model with. You can use either
# test_datasets, or val_set_size, but not both.
test_datasets: Annotated[list[SFTDataset | DPODataset | KTODataset | StepwiseSupervisedDataset], MinLen(1)] | None
  # For SFTDataset:
  # HuggingFace dataset repo | s3:// | gs:// | path to local file or directory
  path: str | None
  # name of dataset split to load from
  split: str | None
  # The type of prompt to use for training. [alpaca, gpteacher, oasst, reflection]
  type: str | UserDefinedPrompterType | None
    # For UserDefinedPrompterType:
    # Custom user instruction prompt
    system_prompt: str | None
    # Use {system} as key to be replaced
    system_format: str | None
    field_system: str | None
    field_instruction: str | None
    field_input: str | None
    field_output: str | None

    # Customizable to be single line or multi-line. Use {instruction}/{input} as key to
    # be replaced. 'format' can include {input}
    format: str | None
    # 'no_input_format' cannot include {input}
    no_input_format: str | None
  input_transform: str | None
  # split dataset into N pieces (use with shards_idx)
  shards: int | None
  # the index of sharded dataset to use
  shards_idx: int | None
  # process dataset in N sequential chunks for memory efficiency (exclusive with
  # `shards`)
  preprocess_shards: int | None
  conversation: str | None

  # The name of the chat template to use for training, following values are supported:
  # tokenizer_default: Uses the chat template that is available in the
  # tokenizer_config.json. If the chat template is not available in the tokenizer, it
  # will raise an error. This is the default.
  # alpaca/inst/chatml/gemma/cohere/llama3/phi_3/deepseek_v2/jamba: These chat templates
  # are available in the axolotl codebase at src/axolotl/utils/chat_templates.py.
  # tokenizer_default_fallback_*: where * is the name of the chat template to fallback
  # to if the tokenizer does not have a chat template else default to tokenizer. E.g.
  # tokenizer_default_fallback_chatml. jinja: Uses a custom jinja template for the chat
  # template. The custom jinja template should be provided in the chat_template_jinja
  # field.
  chat_template: ChatTemplate | str | None
  # Custom jinja chat template or path to jinja file. Used only if `chat_template:
  # jinja` or empty.
  chat_template_jinja: str | None
  # path to source data files
  data_files: str | list[str] | None
  input_format: str | None
  # name of dataset configuration to load
  name: str | None
  # defines the datatype when path is a file
  ds_type: str | None
  # For `completion` datasets only, uses the provided field instead of `text` column
  field: str | None
  field_human: str | None
  field_model: str | None
  # Key containing the messages (default: "messages")
  field_messages: str | None
  # Key containing the tools (default: "tools"). Must be a list[dict] and follow [JSON
  # schema](https://json-schema.org/learn/getting-started-step-by-step).
  field_capabilities: File operations, code editing, terminal access, searchstr | None
  # Key containing the reasoning trace (default: "reasoning_content").
  field_thinking: str | None
  # The key the chat template expects that indicates the reasoning trace.
  template_thinking_key: str | None

  message_field_role: str | None

  message_field_content: str | None
  # Mapping of properties from the input dataset to the chat template. (default:
  # message_property_mappings={'role':'role', 'content':'content'}) If a property exists
  # in the template but not in this mapping, the system will attempt to load it directly
  # from the message using the property name as the key. Example: In the mapping below,
  # 'from' is loaded from input dataset and used as 'role', while 'value' is loaded and
  # used as 'content' in the chat template.
  message_property_mappings: dict[str, str] | None
  # The key in the message turn that indicates via boolean whether tokens of a turn
  # should be considered for training. Useful to selectively train on certain turns
  # besides the `roles_to_train`.
  message_field_training: str | None
  # The key in the message turn that contains the training details. Useful to
  # selectively train on certain tokens in a turn. The value of the key is a List[Dict]
  # containing `begin_offset` (start character index in content), `end_offset` (end
  # character index in content), and `train` (boolean whether to train).
  message_field_training_detail: str | None
  # (for Qwen3 template only) Whether to split the assistant content based on a
  # reasoning trace inside delimited tags
  split_thinking: bool | None
  logprobs_field: str | None
  temperature: float | None
  # Roles to train on. The tokens from these roles will be considered for the loss.
  roles_to_train: list[str] | None
  # Which EOS tokens to train on in the conversation. Possible values are: all: train on
  # all EOS tokens, turn (default): train on the EOS token at the end of each trainable
  # turn, last: train on the last EOS token in the conversation
  train_on_eos: Literal['all', 'turn', 'last'] | None
  # Roles mapping in the messages. The format is {target_role: [source_roles]}. All
  # source roles will be mapped to the target role. The default is: user: ["human",
  # "user"], assistant: ["gpt", "assistant"], system: ["system"], capabilities: File operations, code editing, terminal access, search["tool"]
  roles: dict[str, list[str]] | None
  # Whether to drop the system turn from the dataset. Only works with chat_template.
  # This does not drop the default system message from chat_template if it exists. If
  # you wish to, we recommend using a custom jinja template with the default system
  # message removed or adding a system turn with empty content.
  drop_system_message: bool | None
  # Trust remote code for untrusted source
  trust_remote_code: bool | None = False
  # The specific revision of the dataset to use when loading from the Hugging Face Hub.
  # This can be a commit hash, tag, or branch name. If not specified, the latest version
  # will be used. This parameter is ignored for local datasets.
  revision: str | None

  # For DPODataset:
  path: str | None
  split: str | None
  type: UserDefinedDPOType | str | None
    # For UserDefinedDPOType:
    field_system: str | None
    field_prompt: str | None
    field_chosen: str | None
    field_rejected: str | None
    prompt_format: str | None
    chosen_format: str | None
    rejected_format: str | None
  data_files: list[str] | None
  revision: str | None
  field_messages: str | None

  # For KTODataset:
  path: str | None
  split: str | None
  type: UserDefinedKTOType | str | None
    # For UserDefinedKTOType:
    field_system: str | None
    field_prompt: str | None
    field_completion: str | None
    field_label: bool | None
    prompt_format: str | None
    completion_format: str | None
  data_files: list[str] | None
  trust_remote_code: bool | None = False
  revision: str | None

  # For StepwiseSupervisedDataset:
  path: str | None
  split: str | None
  data_files: list[str] | None
  revision: str | None
  step_separator: str | None
  max_completion_length: int | None
  train_on_last_step_only: bool | None

# If false, the datasets will not be shuffled and will keep their original order in
# `datasets`. The same applies to the `test_datasets` option and the
# `pretraining_dataset` option. Default is true.
shuffle_merged_datasets: bool | None = True
# If true, each dataset in `datasets` will be shuffled before merging. This allows
# curriculum learning strategies to be applied at the dataset level. Default is false.
shuffle_before_merging_datasets: bool | None = False
# Axolotl attempts to save the dataset as an arrow after packing the data together so
# subsequent training attempts load faster, relative path
dataset_prepared_path: str | None
# Num shards for whole dataset
dataset_shard_num: int | None
# Index of shard to use for whole dataset
dataset_shard_idx: int | None
skip_prepare_dataset: bool | None = False
# Number of shards to save the prepared dataset
num_dataset_shards_to_save: int | None

# Set to HF dataset for type: 'completion' for streaming instead of pre-tokenize
pretraining_dataset: Annotated[list[PretrainingDataset | SFTDataset], MinLen(1)] | None
  # For PretrainingDataset:
  name: str | None
  path: str | None
  split: str | None = train
  text_column: str | None = text
  type: str | None = pretrain
  trust_remote_code: bool | None = False
  data_files: str | None
  skip: int | None

  # For SFTDataset:
  # HuggingFace dataset repo | s3:// | gs:// | path to local file or directory
  path: str | None
  # name of dataset split to load from
  split: str | None
  # The type of prompt to use for training. [alpaca, gpteacher, oasst, reflection]
  type: str | UserDefinedPrompterType | None
    # For UserDefinedPrompterType:
    # Custom user instruction prompt
    system_prompt: str | None
    # Use {system} as key to be replaced
    system_format: str | None
    field_system: str | None
    field_instruction: str | None
    field_input: str | None
    field_output: str | None

    # Customizable to be single line or multi-line. Use {instruction}/{input} as key to
    # be replaced. 'format' can include {input}
    format: str | None
    # 'no_input_format' cannot include {input}
    no_input_format: str | None
  input_transform: str | None
  # split dataset into N pieces (use with shards_idx)
  shards: int | None
  # the index of sharded dataset to use
  shards_idx: int | None
  # process dataset in N sequential chunks for memory efficiency (exclusive with
  # `shards`)
  preprocess_shards: int | None
  conversation: str | None

  # The name of the chat template to use for training, following values are supported:
  # tokenizer_default: Uses the chat template that is available in the
  # tokenizer_config.json. If the chat template is not available in the tokenizer, it
  # will raise an error. This is the default.
  # alpaca/inst/chatml/gemma/cohere/llama3/phi_3/deepseek_v2/jamba: These chat templates
  # are available in the axolotl codebase at src/axolotl/utils/chat_templates.py.
  # tokenizer_default_fallback_*: where * is the name of the chat template to fallback
  # to if the tokenizer does not have a chat template else default to tokenizer. E.g.
  # tokenizer_default_fallback_chatml. jinja: Uses a custom jinja template for the chat
  # template. The custom jinja template should be provided in the chat_template_jinja
  # field.
  chat_template: ChatTemplate | str | None
  # Custom jinja chat template or path to jinja file. Used only if `chat_template:
  # jinja` or empty.
  chat_template_jinja: str | None
  # path to source data files
  data_files: str | list[str] | None
  input_format: str | None
  # name of dataset configuration to load
  name: str | None
  # defines the datatype when path is a file
  ds_type: str | None
  # For `completion` datasets only, uses the provided field instead of `text` column
  field: str | None
  field_human: str | None
  field_model: str | None
  # Key containing the messages (default: "messages")
  field_messages: str | None
  # Key containing the tools (default: "tools"). Must be a list[dict] and follow [JSON
  # schema](https://json-schema.org/learn/getting-started-step-by-step).
  field_capabilities: File operations, code editing, terminal access, searchstr | None
  # Key containing the reasoning trace (default: "reasoning_content").
  field_thinking: str | None
  # The key the chat template expects that indicates the reasoning trace.
  template_thinking_key: str | None

  message_field_role: str | None

  message_field_content: str | None
  # Mapping of properties from the input dataset to the chat template. (default:
  # message_property_mappings={'role':'role', 'content':'content'}) If a property exists
  # in the template but not in this mapping, the system will attempt to load it directly
  # from the message using the property name as the key. Example: In the mapping below,
  # 'from' is loaded from input dataset and used as 'role', while 'value' is loaded and
  # used as 'content' in the chat template.
  message_property_mappings: dict[str, str] | None
  # The key in the message turn that indicates via boolean whether tokens of a turn
  # should be considered for training. Useful to selectively train on certain turns
  # besides the `roles_to_train`.
  message_field_training: str | None
  # The key in the message turn that contains the training details. Useful to
  # selectively train on certain tokens in a turn. The value of the key is a List[Dict]
  # containing `begin_offset` (start character index in content), `end_offset` (end
  # character index in content), and `train` (boolean whether to train).
  message_field_training_detail: str | None
  # (for Qwen3 template only) Whether to split the assistant content based on a
  # reasoning trace inside delimited tags
  split_thinking: bool | None
  logprobs_field: str | None
  temperature: float | None
  # Roles to train on. The tokens from these roles will be considered for the loss.
  roles_to_train: list[str] | None
  # Which EOS tokens to train on in the conversation. Possible values are: all: train on
  # all EOS tokens, turn (default): train on the EOS token at the end of each trainable
  # turn, last: train on the last EOS token in the conversation
  train_on_eos: Literal['all', 'turn', 'last'] | None
  # Roles mapping in the messages. The format is {target_role: [source_roles]}. All
  # source roles will be mapped to the target role. The default is: user: ["human",
  # "user"], assistant: ["gpt", "assistant"], system: ["system"], capabilities: File operations, code editing, terminal access, search["tool"]
  roles: dict[str, list[str]] | None
  # Whether to drop the system turn from the dataset. Only works with chat_template.
  # This does not drop the default system message from chat_template if it exists. If
  # you wish to, we recommend using a custom jinja template with the default system
  # message removed or adding a system turn with empty content.
  drop_system_message: bool | None
  # Trust remote code for untrusted source
  trust_remote_code: bool | None = False
  # The specific revision of the dataset to use when loading from the Hugging Face Hub.
  # This can be a commit hash, tag, or branch name. If not specified, the latest version
  # will be used. This parameter is ignored for local datasets.
  revision: str | None

# The maximum number of processes to use while preprocessing your input dataset. This
# defaults to `os.cpu_count()` if not set. For Runpod VMs, it will default to number of
# vCPUs via RUNPOD_CPU_COUNT.
dataset_processes: int | None
# The maximum number of processes to use while preprocessing your input dataset. This
# defaults to `os.cpu_count()` if not set. For Runpod VMs, it will default to number of
# vCPUs via RUNPOD_CPU_COUNT.
dataset_num_proc: int | None

# Deduplicates datasets and test_datasets with identical entries
dataset_exact_deduplication: bool | None
# Keep dataset in memory while preprocessing. Only needed if cached dataset is taking
# too much storage
dataset_keep_in_memory: bool | None
dataloader_pin_memory: bool | None
dataloader_num_workers: int | None
dataloader_prefetch_factor: int | None
dataloader_drop_last: bool | None

accelerator_config: dict[str, Any] | None

remove_unused_columns: bool | None

# Push prepared dataset to hub - repo_org/repo_name
push_dataset_to_hub: str | None
# Whether to use hf `use_auth_token` for loading datasets. Useful for fetching private
# datasets. Required to be true when used in combination with `push_dataset_to_hub`
hf_use_auth_token: bool | None

device: Any | None
# Passed through to transformers when loading the model when launched without
# accelerate. Use `sequential` when training w/ model parallelism to limit memory
device_map: Any | None
world_size: int | None
# Don't mess with this, it's here for accelerate and torchrun
local_rank: int | None
ddp: bool | None

# Seed for reproducibility
seed: int | None
# Advanced DDP Arguments - timeout
ddp_timeout: int | None
# Advanced DDP Arguments - bucket cap in MB
ddp_bucket_cap_mb: int | None
# Advanced DDP Arguments - broadcast buffers
ddp_broadcast_buffers: bool | None
ddp_find_unused_parameters: bool | None

# Approximate number of predictions sent to wandb depending on batch size. Enabled above
# 0. Default is 0
eval_table_size: int | None
# Total number of tokens generated for predictions sent to wandb. Default is 128
eval_max_new_tokens: int | None
# Whether to run causal language model evaluation for metrics in
# `eval_causal_lm_metrics`
do_causal_lm_eval: bool | None
# HF evaluate metrics used during evaluation. Default is ['sacrebleu', 'comet', 'ter',
# 'chrf', 'perplexity']
eval_causal_lm_metrics: list[str] | None
do_bench_eval: bool | None
bench_dataset: str | None
bench_split: str | None
metric_for_best_model: str | None
greater_is_better: bool | None

# High loss value, indicating the learning has broken down (a good estimate is ~2 times
# the loss at the start of training)
loss_watchdog_threshold: float | None
# Number of high-loss steps in a row before the trainer aborts (default: 3)
loss_watchdog_patience: int | None

# Run garbage collection every `gc_steps` steps. -1 will run on epoch end and before
# evaluations. Default is 0 (disabled).
gc_steps: int | None

# Use CUDA bf16. bool or 'full' for `bf16_full_eval`, or 'auto' for automatic detection.
# require >=ampere
bf16: Literal['auto'] | bool | None = auto
# Use CUDA fp16
fp16: bool | None
# Enable FP8 mixed precision training using TorchAO. Best used in combination with
# torch.compile.
fp8: bool | None
# Enable FSDP float8 all-gather optimization for FP8 training. Can improve training
# speed by 10-15% when FSDP is enabled.
fp8_enable_fsdp_float8_all_gather: bool | None
# No AMP (automatic mixed precision) - require >=ampere
bfloat16: bool | None
# No AMP (automatic mixed precision)
float16: bool | None
# Use CUDA tf32 - require >=ampere
tf32: bool | None
float32: bool | None

# Whether to use gradient checkpointing. Available options are: true, false, 'offload',
# 'offload_disk'.
# https://huggingface.co/docs/transformers/v4.18.0/en/performance#gradient-checkpointing
gradient_checkpointing: Literal['offload', 'offload_disk'] | bool | None = False
# Additional kwargs to pass to the trainer for gradient checkpointing
gradient_checkpointing_kwargs: dict[str, Any] | None
# Whether to offload activations. Available options are: true, false, 'legacy', 'disk'.
activation_offloading: Literal['legacy', 'disk'] | bool | None = False

unfrozen_parameters: list[str] | None

# The maximum length of an input to train with, this should typically be less than 2048
# as most models have a token/context limit of 2048
sequence_len: int = 512
# What to do when a tokenized row exceeds sequence_len. 'drop' removes the row;
# 'truncate' slices tensors to sequence_len. Defaults to 'drop' for backward
# compatibility.
excess_length_strategy: Literal['drop', 'truncate'] | None
# The maximum length of an input for evaluation. If not specified, defaults to
# sequence_len
eval_sequence_len: int | None
min_sample_len: int | None
# maximum prompt length for RL training
max_prompt_len: int | None
# Use efficient multi-packing with block diagonal attention and per sequence
# position_ids. Recommend set to 'true'
sample_packing: bool | None
# The number of samples packed at a time. Increasing the following values helps with
# packing, but usually only slightly (<%1.)
sample_packing_group_size: int | None = 100000
# The number of samples which can be packed into one sequence. Increase if using a large
# sequence_len with many short samples.
sample_packing_bin_size: int | None = 200
# Whether to pack samples sequentially
sample_packing_sequentially: bool | None
# The multiprocessing start method to use for packing. Should be 'fork', 'spawn' or
# 'forkserver'
sample_packing_mp_start_method: str | None
# Set to 'false' if getting errors during eval with sample_packing on
eval_sample_packing: bool | None
# Pad inputs so each step uses constant sized buffers. This will reduce memory
# fragmentation and may prevent OOMs, by re-using memory more efficiently. Defaults to
# True if `sample_packing` enabled
pad_to_sequence_len: bool | None
# Whether to use sequential sampling for curriculum learning
curriculum_sampling: bool | None
multipack_real_batches: bool | None

# Use batch flattening for speedups when not using sample_packing
batch_flattening: Literal['auto'] | bool | None

use_pose: bool | None
pose_split_on_token_ids: list[int] | None
pose_max_context_len: int | None
pose_num_chunks: int | None

pretrain_multipack_buffer_size: int | None
# whether to prevent cross attention for packed sequences during pretraining
pretrain_multipack_attn: bool | None = True
# whether to concatenate samples during pretraining
pretraining_sample_concatenation: bool | None

# Use streaming mode for loading datasets
streaming: bool | None
# Buffer size for multipack streaming datasets
streaming_multipack_buffer_size: int | None = 10000

# Whether to use xformers attention patch https://github.com/facebookresearch/xformers
xformers_attention: bool | None
# Whether to use scaled-dot-product attention https://pytorch.org/docs/stable/generated/
# torch.nn.functional.scaled_dot_product_attention.html
sdp_attention: bool | None
# Shifted-sparse attention (only llama) - https://arxiv.org/pdf/2309.12307.pdf
s2_attention: bool | None
flex_attention: bool | None
flex_attn_compile_kwargs: dict[str, Any] | None
# Whether to use flash attention patch https://github.com/Dao-AILab/flash-attention
flash_attention: bool | None
# Whether to use flash-attention cross entropy implementation - advanced use only
flash_attn_cross_entropy: bool | None
# Whether to use flash-attention rms norm implementation - advanced use only
flash_attn_rms_norm: bool | None
# Whether to fuse part of the MLP into a single operation
flash_attn_fuse_mlp: bool | None
# Whether to use bettertransformers
flash_optimum: bool | None

eager_attention: bool | None

# Specify a custom attention implementation, used mostly for kernels.
attn_implementation: str | None

unsloth_cross_entropy_loss: bool | None
unsloth_lora_mlp: bool | None
unsloth_lora_qkv: bool | None
unsloth_lora_o: bool | None
unsloth_rms_norm: bool | None
unsloth_rope: bool | None

# Apply custom LoRA autograd functions and activation function Triton kernels for speed
# and memory savings. See: https://docs.axolotl.ai/docs/lora_optims.html
lora_mlp_kernel: bool | None
# Apply custom LoRA autograd functions and activation function Triton kernels for speed
# and memory savings. See: https://docs.axolotl.ai/docs/lora_optims.html
lora_qkv_kernel: bool | None
# Apply custom LoRA autograd functions and activation function Triton kernels for speed
# and memory savings. See: https://docs.axolotl.ai/docs/lora_optims.html
lora_o_kernel: bool | None

# Whether to use chunked cross entropy loss for memory efficiency
chunked_cross_entropy: bool | None
# Number of chunks to use for chunked cross entropy loss
chunked_cross_entropy_num_chunks: int | None

# Whether to use ALST tiled mlp for memory efficient long context
tiled_mlp: bool | None

# Number of shards to use for ALST tiled mlp. If unset, it will be set based on
# seqlen/hidden_size
tiled_mlp_num_shards: int | None

# Whether to use original mlp for ALST tiled mlp. Otherwise uses a generic MLP based on
# llama.
tiled_mlp_use_original_mlp: bool | None = True

llama4_linearized_experts: bool | None

# Deepspeed config path. e.g., deepspeed_configs/zero3.json
deepspeed: str | dict[str, Any] | None
# Whether to use deepcompile for faster training with deepspeed
deepcompile: bool | None
# FSDP configuration
fsdp: list[str] | None

# FSDP configuration options
fsdp_config: FSDPConfig | None
  # For FSDPConfig:
  # Enable activation checkpointing to reduce memory usage during forward passes
  activation_checkpointing: bool | None
  # Offload parameters to CPU to reduce GPU memory usage
  offload_params: bool | None
  # Synchronize module states across all processes
  sync_module_states: bool | None
  # Enable CPU RAM efficient loading to reduce memory usage during model loading
  cpu_ram_efficient_loading: bool | None
  # Disabling this enables swap memory usage for resource-constrained setups when
  # offload_params is enabled.
  cpu_offload_pin_memory: bool | None
  # Use original parameters instead of flattened parameters
  use_orig_params: bool | None

  # Type of state dict to use for saving/loading checkpoints
  state_dict_type: Literal['FULL_STATE_DICT', 'LOCAL_STATE_DICT', 'SHARDED_STATE_DICT'] | None
  # Final state dict type to use after training completion
  final_state_dict_type: Literal['FULL_STATE_DICT', 'LOCAL_STATE_DICT', 'SHARDED_STATE_DICT'] | None

  # Policy for automatically wrapping modules with FSDP
  auto_wrap_policy: Literal['TRANSFORMER_BASED_WRAP', 'SIZE_BASED_WRAP'] | None
  # Class name of transformer layers to wrap (e.g., 'LlamaDecoderLayer')
  transformer_layer_cls_to_wrap: str | None

  # Reshard parameters after forward pass to save memory
  reshard_after_forward: bool | None
  # Mixed precision policy for FSDP (e.g., 'fp16', 'bf16')
  mixed_precision_policy: str | None

# FSDP version
fsdp_version: int | None
fsdp_final_state_dict_type: Literal['FULL_STATE_DICT', 'LOCAL_STATE_DICT', 'SHARDED_STATE_DICT'] | None

# How much of the dataset to set aside as evaluation. 1 = 100%, 0.50 = 50%, etc. 0 for
# no eval.
val_set_size: float | None = 0.0

# Number of devices to shard across. If not set, will use all available devices.
dp_shard_size: int | None
# Number of devices to replicate across.
dp_replicate_size: int | None
# Deprecated: use `context_parallel_size` instead
sequence_parallel_degree: int | None
# Set to a divisor of the number of GPUs available to split sequences into chunks of
# equal size. Use in long context training to prevent OOM when sequences cannot fit into
# a single GPU's VRAM. E.g., if 4 GPUs are available, set this value to 2 to split each
# sequence into two equal-sized subsequences, or set to 4 to split into four equal-sized
# subsequences. See https://docs.axolotl.ai/docs/sequence_parallelism.html for more
# details.
context_parallel_size: int | None
# Optional; strides across the key dimension. Larger values use more memory but should
# make training faster. Must evenly divide the number of KV heads in your model.
heads_k_stride: int | None
# One of 'varlen_llama3', 'batch_ring', 'batch_zigzag', 'batch_stripe'. Defaults to
# 'varlen_llama3' in the sample packing case, and 'batch_ring' in the non-sample packing
# case.
ring_attn_func: RingAttnFunc | None
# Number of tensor parallel processes in TP group. Only supported with DeepSpeed AutoTP.
tensor_parallel_size: int | None

# Add or change special tokens. If you add tokens here, you don't need to add them to
# the `tokens` list.
special_tokens: SpecialTokensConfig | None
  # For SpecialTokensConfig:
  bos_token: str | None
  eos_token: str | None
  pad_token: str | None
  unk_token: str | None
  additional_special_tokens: list[str] | None

# Add extra tokens to the tokenizer
tokens: list[str] | None
# Mapping token_id to new_token_string to override reserved added_tokens in the
# tokenizer. Only works for tokens that are not part of the base vocab (aka are
# added_tokens). Can be checked if they exist in tokenizer.json added_tokens.
added_tokens_overrides: dict[int, str] | None

# Whether to use torch.compile and which backend to use. setting to `auto` will enable
# torch compile when torch>=2.6.0
torch_compile: Literal['auto'] | bool | None
# Backend to use for torch.compile
torch_compile_backend: str | None
torch_compile_mode: Literal['default', 'reduce-overhead', 'max-autotune'] | None

# Maximum number of iterations to train for. It precedes num_epochs which means that if
# both are set, num_epochs will not be guaranteed. e.g., when 1 epoch is 1000 steps =>
# `num_epochs: 2` and `max_steps: 100` will train for 100 steps
max_steps: int | None
# Number of warmup steps. Cannot use with warmup_ratio
warmup_steps: int | None
# Warmup ratio. Cannot use with warmup_steps
warmup_ratio: float | None
# Leave empty to eval at each epoch, integer for every N steps. float for fraction of
# total steps
eval_steps: int | float | None
# Number of times per epoch to run evals, mutually exclusive with eval_steps
evals_per_epoch: int | None
# Set to `no` to skip evaluation, `epoch` at end of each epoch, leave empty to infer
# from `eval_steps`
eval_strategy: str | None

# Leave empty to save at each epoch, integer for every N steps. float for fraction of
# total steps
save_steps: int | float | None
# Number of times per epoch to save a checkpoint, mutually exclusive with save_steps
saves_per_epoch: int | None
# Set to `no` to skip checkpoint saves, `epoch` at end of each epoch, `best` when better
# result is achieved, leave empty to infer from `save_steps`
save_strategy: str | None
# Checkpoints saved at a time
save_total_limit: int | None
# Whether to checkpoint a model after the first step of training. Defaults to False.
save_first_step: bool | None

# Logging frequency
logging_steps: int | None
# Stop training after this many evaluation losses have increased in a row. https://huggi
# ngface.co/transformers/v4.2.2/_modules/transformers/trainer_callback.html#EarlyStoppin
# gCallback
early_stopping_patience: int | None
load_best_model_at_end: bool | None = False
# Save only the model weights, skipping the optimizer. Using this means you can't resume
# from checkpoints.
save_only_model: bool | None = False
# Use tensorboard for logging
use_tensorboard: bool | None
# Enable the pytorch profiler to capture the first N steps of training to the
# output_dir. see https://pytorch.org/blog/understanding-gpu-memory-1/ for more
# information. Snapshots can be visualized @ https://pytorch.org/memory_viz
profiler_steps: int | None
# Which step to start the profiler at. Useful for only capturing a few steps mid-run.
profiler_steps_start: int | None = 0
# bool of whether to report tokens per second at the end of training. This is not
# supported with pre-training datasets.
include_tokens_per_second: bool | None
# bool of whether to report tokens per second per-gpu during training by measuring
# throughput of non-padding tokens.
include_tkps: bool | None = True
# NEFT https://arxiv.org/abs/2310.05914, set this to a number (paper default is 5) to
# add noise to embeddings. Currently only supported on Llama and Mistral
neftune_noise_alpha: float | None

# Parameter controlling the relative ratio loss weight in the ORPO loss. Passed to
# `beta` in `ORPOConfig` due to trl mapping.
orpo_alpha: float | None
# Weighting of NLL term in loss from RPO paper
rpo_alpha: float | None
# Target reward margin for the SimPO loss
simpo_gamma: float | None
# Weight of the BC regularizer
cpo_alpha: float | None

# Factor for desirable loss term in KTO loss
kto_desirable_weight: float | None
# Factor for undesirable loss term in KTO loss
kto_undesirable_weight: float | None
# The beta parameter for the RL training
rl_beta: float | None

# Defines the max memory usage per gpu on the system. Passed through to transformers
# when loading the model.
max_memory: dict[int | Literal['cpu', 'disk'], int | str] | None
# Limit the memory for all available GPUs to this amount (if an integer, expressed in
# gigabytes); default: unset
gpu_memory_limit: int | str | None
# Whether to use low_cpu_mem_usage
low_cpu_mem_usage: bool | None

# The name of the chat template to use for training, following values are supported:
# tokenizer_default: Uses the chat template that is available in the
# tokenizer_config.json. If the chat template is not available in the tokenizer, it will
# raise an error. This is the default value.
# alpaca/inst/chatml/gemma/cohere/llama3/phi_3/deepseek_v2/jamba: These chat templates
# are available in the axolotl codebase at src/axolotl/utils/chat_templates.py.
# tokenizer_default_fallback_*: where * is the name of the chat template to fallback to.
# E.g. tokenizer_default_fallback_chatml. This is useful when the chat template is not
# available in the tokenizer. jinja: Uses a custom jinja template for the chat template.
# The custom jinja template should be provided in the chat_template_jinja field. The
# selected chat template will be saved to the tokenizer_config.json for easier
# inferencing
chat_template: ChatTemplate | Annotated[str, StringConstraints(pattern='^tokenizer_default_fallback_')] | None
# Custom jinja template or path to jinja file for chat template. This will be only used
# if chat_template is set to `jinja` or `null` (in which case chat_template is
# automatically set to `jinja`). Default is null.
chat_template_jinja: str | None
# Additional kwargs to pass to the chat template. This is useful for customizing the
# chat template. For example, you can pass `thinking=False` to add a generation prompt
# to the chat template.
chat_template_kwargs: dict[str, Any] | None
# Custom EOT (End-of-Turn) tokens to mask/unmask during training. These tokens mark the
# boundaries between conversation turns. For example: ['/INST', '</s>',
# '[/SYSTEM_PROMPT]']. If not specified, defaults to just the model's eos_token. This is
# useful for templates that use multiple delimiter tokens.
eot_tokens: list[str] | None
# Changes the default system message. Currently only supports chatml.
default_system_message: str | None

# Token index or indices to adjust embedding weights to the mean of the other tokens.
# This is useful when the model has untrained embeddings.
fix_untrained_tokens: int | list[int] | None

is_preprocess: bool | None
preprocess_iterable: bool | None

# Total number of tokens - internal use
total_num_tokens: int | None
total_supervised_tokens: int | None
# You can set these packing optimizations AFTER starting a training at least once. The
# trainer will provide recommended values for these values.
sample_packing_eff_est: float | None
axolotl_config_path: str | None

# Internal use only - Used to identify which the model is based on
is_falcon_derived_model: bool | None
# Internal use only - Used to identify which the model is based on
is_llama_derived_model: bool | None
# Internal use only - Used to identify which the model is based on. Please note that if
# you set this to true, `padding_side` will be set to 'left' by default
is_mistral_derived_model: bool | None
# Internal use only - Used to identify which the model is based on
is_qwen_derived_model: bool | None

# Add plugins to extend the pipeline. See `src/axolotl/integrations` for the available
# plugins or doc below for more details.
# https://docs.axolotl.ai/docs/custom_integrations.html
plugins: list[str] | None

# This is the huggingface model that contains *.pt, *.safetensors, or *.bin files. This
# can also be a relative path to a model on disk
base_model: str (required)
# If the base_model repo on hf hub doesn't include configuration .json files, You can
# set that here, or leave this empty to default to base_model
base_model_config: str | None
cls_model_config: str | None
# Optional tokenizer configuration path in case you want to use a different tokenizer
# than the one defined in the base model
tokenizer_config: str | None
# use_fast option for tokenizer loading from_pretrained, default to True
tokenizer_use_fast: bool | None
# Whether to use the legacy tokenizer setting, defaults to True
tokenizer_legacy: bool | None
# Whether to use mistral-common tokenizer. If set to True, it will use the mistral-
# common tokenizer.
tokenizer_use_mistral_common: bool | None
# Corresponding tokenizer for the model AutoTokenizer is a good choice
tokenizer_type: str | None
# transformers processor class
processor_type: str | None
# Whether to save jinja files for tokenizer, transformers default is True
tokenizer_save_jinja_files: bool | None = True
# Trust remote code for untrusted source
trust_remote_code: bool | None

# Don't move the model to the device before sharding. Set to `false` to revert to legacy
# behavior.
experimental_skip_move_to_device: bool | None = True

# Use custom kernels, e.g. MegaBlocks.
use_kernels: bool | None

# Model loading quantization config
model_quantization_config: Literal['Mxfp4Config'] | None
# kwargs for model quantization config
model_quantization_config_kwargs: dict[str, Any] | None

# Where to save the full-finetuned model to
output_dir: str = ./model-out
# push checkpoints to hub
hub_model_id: str | None
# how to push checkpoints to hub
hub_strategy: str | None
# Save model as safetensors (require safetensors package). Default True
save_safetensors: bool | None = True

# This will attempt to quantize the model down to 8 bits and use adam 8 bit optimizer
load_in_8bit: bool | None = False
# Use bitsandbytes 4 bit
load_in_4bit: bool | None = False

# If you want to use 'lora' or 'qlora' or leave blank to train all parameters in
# original model
adapter: str | None
# If you already have a lora model trained that you want to load, put that here. This
# means after training, if you want to test the model, you should set this to the value
# of `output_dir`. Note that if you merge an adapter to the base model, a new
# subdirectory `merged` will be created under the `output_dir`.
lora_model_dir: str | None
lora_r: int | None
lora_alpha: int | None
lora_fan_in_fan_out: bool | None
lora_target_modules: str | list[str] | None
lora_target_parameters: str | list[str] | None
# If true, will target all linear modules
lora_target_linear: bool | None
# If you added new tokens to the tokenizer, you may need to save some LoRA modules
# because they need to know the new tokens. For LLaMA and Mistral, you need to save
# `embed_tokens` and `lm_head`. It may vary for other models. `embed_tokens` converts
# tokens to embeddings, and `lm_head` converts embeddings to token probabilities.
lora_modules_to_save: list[str] | None
lora_dropout: float | None = 0.0
# The layer indices to transform, otherwise, apply to all layers
peft_layers_to_transform: list[int] | None
peft_layers_pattern: list[str] | None

peft: PeftConfig | None
  # For PeftConfig:
  # Configuration options for loftq initialization for LoRA
  loftq_config: LoftQConfig | None
    # For LoftQConfig:
    # typically 4 bits
    loftq_bits: int = 4

# Whether to use DoRA.
peft_use_dora: bool | None
# Whether to use RSLoRA.
peft_use_rslora: bool | None
# List of layer indices to replicate.
peft_layer_replication: list[tuple[int, int]] | None
# How to initialize LoRA weights. Default to True which is MS original implementation.
peft_init_lora_weights: bool | str | None
# A list of token indices to fine-tune on the `embed_tokens` layer. Otherwise, a dict
# mapping an embedding layer name to its trainable token indices. See
# https://huggingface.co/docs/peft/v0.17.0/en/developer_guides/lora#efficiently-train-
# tokens-alongside-lora
peft_trainable_token_indices: list[int] | dict[str, list[int]] | None

# load qlora model in sharded format for FSDP using answer.ai technique.
qlora_sharded_model_loading: bool | None = False
# Do the LoRA/PEFT loading on CPU -- this is required if the base model is so large it
# takes up most or all of the available GPU VRAM, e.g. during a model and LoRA merge
lora_on_cpu: bool | None
# Whether you are training a 4-bit GPTQ quantized model
gptq: bool | None
# optional overrides to the bnb 4bit quantization configuration
bnb_config_kwargs: dict[str, Any] | None

# loraplus learning rate ratio lr_B / lr_A. Recommended value is 2^4.
loraplus_lr_ratio: float | None
# loraplus learning rate for lora embedding layers. Default value is 1e-6.
loraplus_lr_embedding: float | None = 1e-06

merge_lora: bool | None

# Whether to use ReLoRA. Use with jagged_restart_*steps options.
relora: bool | None
# threshold for optimizer magnitude when pruning
relora_prune_ratio: float | None
# True to perform lora weight merges on cpu during restarts, for modest gpu memory
# savings
relora_cpu_offload: bool | None

# how often to reset for jagged restarts
jagged_restart_steps: int | None
# how many warmup steps to take after reset for jagged restarts
jagged_restart_warmup_steps: int | None
# how many anneal steps to take before reset for jagged restarts
jagged_restart_anneal_steps: int | None

# If greater than 1, backpropagation will be skipped and the gradients will be
# accumulated for the given number of steps.
gradient_accumulation_steps: int | None = 1
# The number of samples to include in each batch. This is the number of samples sent to
# each GPU. Batch size per gpu = micro_batch_size * gradient_accumulation_steps
micro_batch_size: int | None = 1
# Total batch size, we do not recommended setting this manually
batch_size: int | None
# per gpu micro batch size for evals, defaults to value of micro_batch_size
eval_batch_size: int | None

# whether to find batch size that fits in memory. Passed to underlying transformers
# Trainer
auto_find_batch_size: bool | None

# Whether to mask out or include the human's prompt from the training labels
train_on_inputs: bool | None = False
# Group similarly sized data to minimize padding. May be slower to start, as it must
# download and sort the entire dataset. Note that training loss may have an oscillating
# pattern with this enabled.
group_by_length: bool | None

learning_rate: str | float (required)
embedding_lr: float | None
embedding_lr_scale: float | None
# Specify weight decay
weight_decay: float | None = 0.0
# Specify optimizer
optimizer: OptimizerNames | CustomSupportedOptimizers | None = OptimizerNames.ADAMW_TORCH_FUSED
# Dictionary of arguments to pass to the optimizer
optim_args: str | dict[str, Any] | None
# The target modules to optimize, i.e. the module names that you would like to train,
# right now this is used only for GaLore algorithm
optim_target_modules: list[str] | Literal['all_linear'] | None
# Path to torch distx for optim 'adamw_anyprecision'
torchdistx_path: str | None
lr_scheduler: SchedulerType | Literal['one_cycle'] | Literal['rex'] | None = SchedulerType.COSINE
# Specify a scheduler and kwargs to use with the optimizer
lr_scheduler_kwargs: dict[str, Any] | None
lr_quadratic_warmup: bool | None
# decay lr to some percentage of the peak lr, e.g. cosine_min_lr_ratio=0.1 for 10% of
# peak lr
cosine_min_lr_ratio: float | None
# freeze lr at some percentage of the step, e.g. cosine_constant_lr_ratio=0.8 means
# start cosine_min_lr at 80% of training step
cosine_constant_lr_ratio: float | None
# Learning rate div factor
lr_div_factor: float | None

lr_groups: list[LrGroup] | None
  # For LrGroup:
  name: str (required)
  modules: list[str] (required)
  lr: float (required)

# adamw hyperparams
adam_epsilon: float | None
# only used for CAME Optimizer
adam_epsilon2: float | None
# adamw hyperparams
adam_beta1: float | None
# adamw hyperparams
adam_beta2: float | None
# only used for CAME Optimizer
adam_beta3: float | None

# Dion Optimizer learning rate
dion_lr: float | None
# Dion Optimizer momentum
dion_momentum: float | None
# Dion Optimizer: r/d fraction for low-rank approximation. Used to compute the low-rank
# dimension.
dion_rank_fraction: float | None = 1.0
# Dion Optimizer: Round up the low-rank dimension to a multiple of this number. This may
# be useful to ensure even sharding.
dion_rank_multiple_of: int | None = 1

# Gradient clipping max norm
max_grad_norm: float | None
num_epochs: float = 1.0

use_wandb: bool | None
# Set the name of your wandb run
wandb_name: str | None
# Set the ID of your wandb run
wandb_run_id: str | None
# "offline" to save run metadata locally and not sync to the server, "disabled" to turn
# off wandb
wandb_mode: str | None
# Your wandb project name
wandb_project: str | None
# A wandb Team name if using a Team
wandb_entity: str | None
wandb_watch: str | None
# "checkpoint" to log model to wandb Artifacts every `save_steps` or "end" to log only
# at the end of training
wandb_log_model: str | None

use_mlflow: bool | None
# URI to mlflow
mlflow_tracking_uri: str | None
# Your experiment name
mlflow_experiment_name: str | None
# Your run name
mlflow_run_name: str | None
# set to true to copy each saved checkpoint on each save to mlflow artifact registry
hf_mlflow_log_artifacts: bool | None

# Enable or disable Comet integration.
use_comet: bool | None
# API key for Comet. Recommended to set via `comet login`.
comet_api_key: str | None
# Workspace name in Comet. Defaults to the user's default workspace.
comet_workspace: str | None
# Project name in Comet. Defaults to Uncategorized.
comet_project_name: str | None
# Identifier for the experiment. Used to append data to an existing experiment or
# control the key of new experiments. Default to a random key.
comet_experiment_key: str | None
# Create a new experiment ("create") or log to an existing one ("get"). Default
# ("get_or_create") auto-selects based on configuration.
comet_mode: str | None
# Set to True to log data to Comet server, or False for offline storage. Default is
# True.
comet_online: bool | None
# Dictionary for additional configuration settings, see the doc for more details.
comet_experiment_config: dict[str, Any] | None

# Enable OpenTelemetry metrics collection and Prometheus export
use_otel_metrics: bool | None = False
# Host to bind the OpenTelemetry metrics server to
otel_metrics_host: str | None = localhost
# Port for the Prometheus metrics HTTP server
otel_metrics_port: int | None = 8000

# the number of activate layers in LISA
lisa_n_layers: int | None
# how often to switch layers in LISA
lisa_step_interval: int | None
# path under the model to access the layers
lisa_layers_attribute: str | None = model.layers

gradio_title: str | None
gradio_share: bool | None
gradio_server_name: str | None
gradio_server_port: int | None
gradio_max_new_tokens: int | None
gradio_temperature: float | None

use_ray: bool = False
ray_run_name: str | None
ray_num_workers: int = 1
resources_per_worker: dict

# The size of the image to resize to. It can be an integer (resized into padded-square
# image) or a tuple (width, height).If not provided, we will attempt to load from
# preprocessor.size, otherwise, images won't be resized.
image_size: int | tuple[int, int] | None
# The resampling algorithm to use for image resizing. Default is bilinear. Please refer
# to PIL.Image.Resampling for more details.
image_resize_algorithm: Literal['bilinear', 'bicubic', 'lanczos'] | Resampling | None

# optional overrides to the base model configuration
overrides_of_model_config: dict[str, Any] | None
# optional overrides the base model loading from_pretrained
overrides_of_model_kwargs: dict[str, Any] | None
# If you want to specify the type of model to load, AutoModelForCausalLM is a good
# choice too
type_of_model: str | None
# You can specify to choose a specific model revision from huggingface hub
revision_of_model: str | None

max_packed_sequence_len: int | None
rope_scaling: Any | None
noisy_embedding_alpha: float | None
dpo_beta: float | None
evaluation_strategy: str | None
```

---

## 

**URL:** https://docs.axolotl.ai

**Contents:**
- 🎉 Latest Updates
- ✨ Overview
- 🚀 Quick Start - LLM Fine-tuning in Minutes
  - Google Colab
  - Installation
    - Using pip
    - Using Docker
    - Cloud Providers
  - Your First Fine-tune
- 📚 Documentation

A Free and Open Source LLM Fine-tuning Framework

Axolotl is a free and open-source tool designed to streamline post-training and fine-tuning for the latest large language models (LLMs).

Installing with Docker can be less error prone than installing in your own environment.

Other installation approaches are described here.

That’s it! Check out our Getting Started Guide for a more detailed walkthrough.

Contributions are welcome! Please see our Contributing Guide for details.

Interested in sponsoring? Contact us at [email protected]

If you use Axolotl in your research or projects, please cite it as follows:

This project is licensed under the Apache 2.0 License - see the LICENSE file for details.

**Examples:**

Example 1 (bash):
```bash
pip3 install -U packaging==23.2 setuptools==75.8.0 wheel ninja
pip3 install --no-build-isolation axolotl[flash-attn,deepspeed]

# Download example axolotl configs, deepspeed configs
axolotl fetch examples
axolotl fetch deepspeed_configs  # OPTIONAL
```

Example 2 (bash):
```bash
docker run --gpus '"all"' --rm -it axolotlai/axolotl:main-latest
```

Example 3 (bash):
```bash
# Fetch axolotl examples
axolotl fetch examples

# Or, specify a custom path
axolotl fetch examples --dest path/to/folder

# Train a model using LoRA
axolotl train examples/llama-3/lora-1b.yml
```

Example 4 (unknown):
```unknown
@software{axolotl,
  title = {Axolotl: Open Source LLM Post-Training},
  author = {{Axolotl maintainers and contributors}},
  url = {https://github.com/axolotl-ai-cloud/axolotl},
  license = {Apache-2.0},
  year = {2023}
}
```

---

## Quickstart

**URL:** https://docs.axolotl.ai/docs/getting-started.html

**Contents:**
- Quickstart
- 1 Quick Example
- 2 Understanding the Process
  - 2.1 The Configuration File
  - 2.2 Training
- 3 Your First Custom Training
- 4 Common Tasks
  - 4.1 Testing Your Model
  - 4.2 Using a UI
  - 4.3 Preprocessing Data

This guide will walk you through your first model fine-tuning project with Axolotl.

Let’s start by fine-tuning a small language model using LoRA. This example uses a 1B parameter model to ensure it runs on most GPUs. Assuming axolotl is installed (if not, see our Installation Guide)

That’s it! Let’s understand what just happened.

The YAML configuration file controls everything about your training. Here’s what (part of) our example config looks like:

load_in_8bit: true and adapter: lora enables LoRA adapter finetuning.

See our config options for more details.

When you run axolotl train, Axolotl:

Let’s modify the example for your own data:

This specific config is for LoRA fine-tuning a model with instruction tuning data using the alpaca dataset format, which has the following format:

Please see our Dataset Formats for more dataset formats and how to format them.

The same yaml file is used for training, inference, and merging.

After training, test your model:

More details can be found in Inference.

Launch a Gradio interface:

For large datasets, preprocess first:

Please make sure to set dataset_prepared_path: in your config to set the path to save the prepared dataset.

More details can be found in Dataset Preprocessing.

To merge the LoRA weights back into the base model, run:

The merged model will be saved in the {output_dir}/merged directory.

More details can be found in Merging LoRA weights.

Now that you have the basics, you might want to:

Check our other guides for details on these topics:

**Examples:**

Example 1 (bash):
```bash
axolotl fetch examples
```

Example 2 (bash):
```bash
axolotl train examples/llama-3/lora-1b.yml
```

Example 3 (yaml):
```yaml
base_model: NousResearch/Llama-3.2-1B

load_in_8bit: true
adapter: lora

datasets:
  - path: teknium/GPT4-LLM-Cleaned
    type: alpaca
dataset_prepared_path: last_run_prepared
val_set_size: 0.1
output_dir: ./outputs/lora-out
```

Example 4 (yaml):
```yaml
base_model: NousResearch/Nous-Hermes-llama-1b-v1

load_in_8bit: true
adapter: lora

# Training settings
micro_batch_size: 2
num_epochs: 3
learning_rate: 0.0003

# Your dataset
datasets:
  - path: my_data.jsonl        # Your local data file
    type: alpaca               # Or other format
```

---

## Multipack (Sample Packing)

**URL:** https://docs.axolotl.ai/docs/multipack.html

**Contents:**
- Multipack (Sample Packing)
- Visualization of Multipack with Flash Attention
- Multipack without Flash Attention

Because Flash Attention simply drops the attention mask, we do not need to construct a 4d attention mask. We only need to concatenate the sequences into a single batch and let flash attention know where each new sequence begins.

4k context, bsz =4, each character represents 256 tokens X represents a padding token

after padding to longest input in each step

w packing ( note it’s the same effective number of tokens per step, but a true bsz of 1)

cu_seqlens: [[ 0, 11, 17, 24, 28, 36, 41 44, 48, 51, 55, 60, 64]]

Multipack can still be achieved without Flash attention, but with lower packing efficiency as we are not able to join multiple batches into a single batch due to context length limits without flash attention. We can use either Pytorch’s Scaled Dot Product Attention implementation or native Pytorch attention implementation along with 4d attention masks to pack sequences together and avoid cross attention.

**Examples:**

Example 1 (unknown):
```unknown
0 1 2 3 4 5 6 7 8 9 0 1 2 3 4 5
[[ A A A A A A A A A A A ]
   B B B B B B ]
   C C C C C C C ]
   D D D D ]]

[[ E E E E E E E E ]
 [ F F F F ]
 [ G G G ]
 [ H H H H ]]

[[ I I I ]
 [ J J J ]
 [ K K K K K]
 [ L L L ]]
```

Example 2 (unknown):
```unknown
0 1 2 3 4 5 6 7 8 9 0 1 2 3 4 5
[[ A A A A A A A A A A A ]
   B B B B B B X X X X X X ]
   C C C C C C C X X X X ]
   D D D D X X X X X X X ]]

[[ E E E E E E E E ]
 [ F F F F X X X X ]
 [ G G G X X X X X ]
 [ H H H H X X X X ]]

[[ I I I X X ]
 [ J J J X X ]
 [ K K K K K ]
 [ L L L X X ]]
```

Example 3 (unknown):
```unknown
0 1 2 3 4 5 6 7 8 9 0 1 2 3 4 5
[[ A A A A A A A A A A A B B B B B
   B C C C C C C C D D D D E E E E
   E E E E F F F F F G G G H H H H
   I I I J J J J K K K K K L L L X ]]
```

---

## Batch size vs Gradient accumulation

**URL:** https://docs.axolotl.ai/docs/batch_vs_grad.html

**Contents:**
- Batch size vs Gradient accumulation

Gradient accumulation means accumulating gradients over several mini-batches and updating the model weights afterward. When the samples in each batch are diverse, this technique doesn’t significantly impact learning.

This method allows for effective training with larger effective batch sizes without needing proportionally larger memory. Here’s why:

Memory Consumption with Batch Size: The primary reason increasing the batch size impacts memory is due to the storage requirements for intermediate activations. When you forward propagate a batch through a network, you have to store the activations at each layer for each sample in the batch, because these activations are used during backpropagation to compute gradients. Therefore, larger batches mean more activations, leading to greater GPU memory consumption.

Gradient Accumulation: With gradient accumulation, you’re effectively simulating a larger batch size by accumulating gradients over several smaller batches (or micro-batches). However, at any given time, you’re only forward and backward propagating a micro-batch. This means you only store activations for the micro-batch, not the full accumulated batch. As a result, you can simulate the effect of a larger batch size without the memory cost of storing activations for a large batch.

Example 1: Micro batch size: 3 Gradient accumulation steps: 2 Number of GPUs: 3 Total batch size = 3 * 2 * 3 = 18

Example 2: Micro batch size: 2 Gradient accumulation steps: 1 Number of GPUs: 3 Total batch size = 2 * 1 * 3 = 6

**Examples:**

Example 1 (unknown):
```unknown
| GPU 1          | GPU 2          | GPU 3          |
|----------------|----------------|----------------|
| S1, S2, S3     | S4, S5, S6     | S7, S8, S9     |
| e1, e2, e3     | e4, e5, e6     | e7, e8, e9     |
|----------------|----------------|----------------|
| → (accumulate) | → (accumulate) | → (accumulate) |
|----------------|----------------|----------------|
| S10, S11, S12  | S13, S14, S15  | S16, S17, S18  |
| e10, e11, e12  | e13, e14, e15  | e16, e17, e18  |
|----------------|----------------|----------------|
| → (apply)      | → (apply)      | → (apply)      |

Accumulated gradient for the weight w1 after the second iteration (considering all GPUs):
Total gradient for w1 = e1 + e2 + e3 + e4 + e5 + e6 + e7 + e8 + e9 + e10 + e11 + e12 + e13 + e14 + e15 + e16 + e17 + e18

Weight update for w1:
w1_new = w1_old - learning rate x (Total gradient for w1 / 18)
```

Example 2 (unknown):
```unknown
| GPU 1     | GPU 2     | GPU 3     |
|-----------|-----------|-----------|
| S1, S2    | S3, S4    | S5, S6    |
| e1, e2    | e3, e4    | e5, e6    |
|-----------|-----------|-----------|
| → (apply) | → (apply) | → (apply) |

Accumulated gradient for the weight w1 (considering all GPUs):
Total gradient for w1 = e1 + e2 + e3 + e4 + e5 + e6

Weight update for w1:
w1_new = w1_old - learning rate × (Total gradient for w1 / 6)
```

---

## Debugging

**URL:** https://docs.axolotl.ai/docs/debugging.html

**Contents:**
- Debugging
- Table of Contents
- General Tips
- Debugging with VSCode
  - Background
  - Setup
    - Remote Hosts
  - Configuration
  - Customizing your debugger
  - Video Tutorial

This document provides some tips and tricks for debugging Axolotl. It also provides an example configuration for debugging with VSCode. A good debugging setup is essential to understanding how Axolotl code works behind the scenes.

While debugging it’s helpful to simplify your test scenario as much as possible. Here are some tips for doing so:

[!Important] All of these tips are incorporated into the example configuration for debugging with VSCode below.

Make sure you are using the latest version of axolotl: This project changes often and bugs get fixed fast. Check your git branch and make sure you have pulled the latest changes from main.

Eliminate concurrency: Restrict the number of processes to 1 for both training and data preprocessing:

Use a small dataset: Construct or use a small dataset from HF Hub. When using a small dataset, you will often have to make sure sample_packing: False and eval_sample_packing: False to avoid errors. If you are in a pinch and don’t have time to construct a small dataset but want to use from the HF Hub, you can shard the data (this will still tokenize the entire dataset, but will only use a fraction of the data for training. For example, to shard the dataset into 20 pieces, add the following to your axolotl config):

Use a small model: A good example of a small model is TinyLlama/TinyLlama-1.1B-Chat-v1.0.

Minimize iteration time: Make sure the training loop finishes as fast as possible, with these settings.

Clear Caches: Axolotl caches certain steps and so does the underlying HuggingFace trainer. You may want to clear some of these caches when debugging.

The below example shows how to configure VSCode to debug data preprocessing of the chat_template format. This is the format used when you have the following in your axolotl config:

[!Important] If you are already familiar with advanced VSCode debugging, you can skip the below explanation and look at the files .vscode/launch.json and .vscode/tasks.json for an example configuration.

[!Tip] If you prefer to watch a video, rather than read, you can skip to the video tutorial below (but doing both is recommended).

Make sure you have an editable install of Axolotl, which ensures that changes you make to the code are reflected at runtime. Run the following commands from the root of this project:

If you developing on a remote host, you can easily use VSCode to debug remotely. To do so, you will need to follow this remote - SSH guide. You can also see the video below on Docker and Remote SSH debugging.

The easiest way to get started is to modify the .vscode/launch.json file in this project. This is just an example configuration, so you may need to modify or copy it to suit your needs.

For example, to mimic the command cd devtools && CUDA_VISIBLE_DEVICES=0 accelerate launch -m axolotl.cli.train dev_chat_template.yml, you would use the below configuration1. Note that we add additional flags that override the axolotl config and incorporate the tips above (see the comments). We also set the working directory to devtools and set the env variable HF_HOME to a temporary folder that is later partially deleted. This is because we want to delete the HF dataset cache before each run in order to ensure that the data preprocessing code is run from scratch.

Additional notes about this configuration:

[!Tip] You may not want to delete these folders. For example, if you are debugging model training instead of data pre-processing, you may NOT want to delete the cache or output folders. You may also need to add additional tasks to the tasks.json file depending on your use case.

Below is the ./vscode/tasks.json file that defines the cleanup-for-dataprep task. This task is run before each debugging session when you use the above configuration. Note how there are two tasks that delete the two folders mentioned above. The third task cleanup-for-dataprep is a composite task that combines the two tasks. A composite task is necessary because VSCode does not allow you to specify multiple tasks in the preLaunchTask argument of the launch.json file.

Your debugging use case may differ from the example above. The easiest thing to do is to put your own axolotl config in the devtools folder and modify the launch.json file to use your config. You may also want to modify the preLaunchTask to delete different folders or not delete anything at all.

The following video tutorial walks through the above configuration and demonstrates how to debug with VSCode, (click the image below to watch):

Using official Axolotl Docker images is a great way to debug your code, and is a very popular way to use Axolotl. Attaching VSCode to Docker takes a few more steps.

On the host that is running axolotl (ex: if you are using a remote host), clone the axolotl repo and change your current directory to the root:

[!Tip] If you already have axolotl cloned on your host, make sure you have the latest changes and change into the root of the project.

Next, run the desired docker image and mount the current directory. Below is a docker command you can run to do this:2

[!Tip] To understand which containers are available, see the Docker section of the README and the DockerHub repo. For details of how the Docker containers are built, see axolotl’s Docker CI builds.

You will now be in the container. Next, perform an editable install of Axolotl:

Next, if you are using a remote host, Remote into this host with VSCode. If you are using a local host, you can skip this step.

Next, select Dev Containers: Attach to Running Container... using the command palette (CMD + SHIFT + P) in VSCode. You will be prompted to select a container to attach to. Select the container you just created. You will now be in the container with a working directory that is at the root of the project. Any changes you make to the code will be reflected both in the container and on the host.

Now you are ready to debug as described above (see Debugging with VSCode).

Here is a short video that demonstrates how to attach to a Docker container on a remote host:

The config actually mimics the command CUDA_VISIBLE_DEVICES=0 python -m accelerate.commands.launch -m axolotl.cli.train devtools/chat_template.yml, but this is the same thing.↩︎

Many of the below flags are recommended best practices by Nvidia when using nvidia-container-toolkit. You can read more about these flags here.↩︎

**Examples:**

Example 1 (yaml):
```yaml
datasets:
    ...
    shards: 20
```

Example 2 (yaml):
```yaml
datasets:
  - path: <path to your chat_template formatted dataset> # example on HF Hub: fozziethebeat/alpaca_messages_2k_test
    type: chat_template
```

Example 3 (bash):
```bash
pip3 install packaging
pip3 install --no-build-isolation -e '.[flash-attn,deepspeed]'
```

Example 4 (json):
```json
// .vscode/launch.json
{
    "version": "0.2.0",
    "configurations": [
        {
            "name": "Debug axolotl prompt - chat_template",
            "type": "python",
            "module": "accelerate.commands.launch",
            "request": "launch",
            "args": [
                "-m", "axolotl.cli.train", "dev_chat_template.yml",
                // The flags below simplify debugging by overriding the axolotl config
                // with the debugging tips above.  Modify as needed.
                "--dataset_num_proc=1",      // limits data preprocessing to one process
                "--max_steps=1",              // limits training to just one step
                "--batch_size=1",             // minimizes batch size
                "--micro_batch_size=1",       // minimizes batch size
                "--val_set_size=0",           // disables validation
                "--sample_packing=False",     // disables sample packing which is necessary for small datasets
                "--eval_sample_packing=False",// disables sample packing on eval set
                "--dataset_prepared_path=temp_debug/axolotl_outputs/data", // send data outputs to a temp folder
                "--output_dir=temp_debug/axolotl_outputs/model" // send model outputs to a temp folder
                ],
            "console": "integratedTerminal",      // show output in the integrated terminal
            "cwd": "${workspaceFolder}/devtools", // set working directory to devtools from the root of the project
            "justMyCode": true,                   // step through only axolotl code
            "env": {"CUDA_VISIBLE_DEVICES": "0",  // Since we aren't doing distributed training, we need to limit to one GPU
                    "HF_HOME": "${workspaceFolder}/devtools/temp_debug/.hf-cache"}, // send HF cache to a temp folder
            "preLaunchTask": "cleanup-for-dataprep", // delete temp folders (see below)
        }
    ]
}
```

---

## Docker

**URL:** https://docs.axolotl.ai/docs/docker.html

**Contents:**
- Docker
- Base
    - Image
    - Tags format
- Main
    - Image
    - Tags format
- Cloud
    - Image
    - Tags format

This section describes the different Docker images that are released by AxolotlAI at Docker Hub.

For Blackwell GPUs, please use the tags with PyTorch 2.7.1 and CUDA 12.8.

The base image is the most minimal image that can install Axolotl. It is based on the nvidia/cuda image. It includes python, torch, git, git-lfs, awscli, pydantic, and more.

The main image is the image that is used to run Axolotl. It is based on the axolotlai/axolotl-base image and includes the Axolotl codebase, dependencies, and more.

There may be some extra tags appended to the image, like -vllm which installs those packages.

The cloud image is the image that is used to run Axolotl in the cloud. It is based on the axolotlai/axolotl image and sets ENV variables like HuggingFace cache directories for volume mounts, tmux, and more for different cloud providers.

Jupyter lab is run by default. Set JUPYTER_DISABLE=1 in the environment variables to disable it.

This uses the same tags as the main image.

We recommend mounting volumes to /workspace/data for data persistence. /workspace/axolotl contains the source code and is ephemeral.

This is the same as the cloud image but without tmux.

The naming may be a bit confusing as it has -term appended to the end.

This uses the same tags as the cloud image.

**Examples:**

Example 1 (unknown):
```unknown
axolotlai/axolotl-base
```

Example 2 (bash):
```bash
main-base-py{python_version}-cu{cuda_version}-{pytorch_version}
```

Example 3 (unknown):
```unknown
axolotlai/axolotl
```

Example 4 (bash):
```bash
# on push to main
main-py{python_version}-cu{cuda_version}-{pytorch_version}

# latest main (currently torch 2.6.0, python 3.11, cuda 12.4)
main-latest

# nightly build
{branch}-{date_in_YYYYMMDD}-py{python_version}-cu{cuda_version}-{pytorch_version}

# tagged release
{version}
```

---




### Dataset Formats

# Axolotl - Dataset-Formats

**Pages:** 9

---

## Custom Pre-Tokenized Dataset

**URL:** https://docs.axolotl.ai/docs/dataset-formats/tokenized.html

**Contents:**
- Custom Pre-Tokenized Dataset

**Examples:**

Example 1 (yaml):
```yaml
datasets:
  - path: /path/to/your/file.jsonl
    ds_type: json
    type:
```

Example 2 (json):
```json
{"input_ids":[271,299,99],"attention_mask":[1,1,1],"labels":[271,-100,99]}
{"input_ids":[87,227,8383,12],"attention_mask":[1,1,1,1],"labels":[87,227,8383,12]}
```

---

## Dataset Formats

**URL:** https://docs.axolotl.ai/docs/dataset-formats/index.html

**Contents:**
- Dataset Formats
- Pre-training
  - Pre-training from Hugging Face hub datasets
  - Pre-training from local dataset files
  - Pre-training without streaming
  - Pre-training dataset configuration tips
    - Setting max_steps
    - Group_by_length
  - Reference
- Supervised fine-tuning (SFT)

Axolotl is a training framework that aims to make the process convenient yet flexible to users by simply passing a config yaml file.

As there are a lot of available options in Axolotl, this guide aims to provide an simplify the user experience to choosing the proper choice.

Axolotl supports 3 kinds of training methods: pre-training, supervised fine-tuning, and preference-based post-training (e.g. DPO, ORPO, PRMs). Each method has their own dataset format which are described below.

This guide will mainly use JSONL as an introduction. Please refer to the dataset loading docs to understand how to load datasets from other sources.

For pretraining_dataset: specifically, please refer to the Pre-training section.

When aiming to train on large corpora of text datasets, pre-training is your go-to choice. Due to the size of these datasets, downloading the entire-datasets before beginning training would be prohibitively time-consuming. Axolotl supports streaming to only load batches into memory at a time.

A sample format for a pre-training dataset is as follows:

It is typically recommended to save your dataset as .jsonl due to its flexibility and simplicity.

Axolotl supports loading from a Hugging Face hub repo or from local files.

As an example, to train using a Hugging Face dataset hf_org/name, you can pass the following config:

Given a few corpus files: A.jsonl, B.jsonl, and C.jsonl, your config will look like the below:

While we recommend .jsonl, you can also use the other formats (csv, parquet, arrow, SQL, Webdataset) that are supported by Dataset.load_dataset

In the case that the dataset is small and can be loaded entirely into memory, another approach to running pre-training is to use the completion format. This would mean that the entire dataset is pre-tokenized instead of on-demand in streaming.

One benefit of this is that the tokenization can be performed separately on a CPU-only machine, and then transferred to a GPU machine for training to save costs.

For completion only, Axolotl would split texts if it exceeds the context length into multiple smaller prompts. If you are interested in having this for pretraining_dataset too, please let us know or help make a PR!

When using streaming for large datasets, Axolotl does not know in advance how large the dataset is and does not know when to stop.

Therefore, it is necessary to set max_steps: int in your config for pre-training to run, so that Axolotl knows when to stop training.

One step is equal to sequence_len * micro_batch_size * gradient_accumulation_steps * total_num_gpus tokens.

It is recommended to leave this off if downloading from Hugging Face hub as it would download the entire dataset which can be very large.

Please see docs here.

Supervised fine-tuning is the process of training models to respond to an instruction or chat input.

As there are a wide variety of dataset formats, Axolotl tries to support a majority of the formats available in public datasets.

Axolotl provides four approaches for loading datasets, however, it’s easier to work backwards from the dataset you have available to figure out which approach to use.

A flow chart is as follows:

Do you already have the dataset tokenized? If yes, check Pre-Tokenized Dataset.

Do you want to format the dataset yourself and manually choose each section to mask? If yes, check Template Free Dataset

Is your dataset in a “conversation” format, containing a list[messages]? If yes, check Conversation Dataset

Is your dataset in an “instruct” format, containing { instruction, response }? If yes, check Instruction Dataset

If you went through the flow chart and did not find one that matches, it is recommended to preprocess your dataset into one of the above or create a thread on Github Discussion.

You can mix and match within each approach or across approaches to train a model on a variety of datasets.

We suggest this approach when you want to bring your own tokenized dataset.

Axolotl expects the dataset to have three keys:

Make sure to add BOS/EOS tokens to your prompt and mask it appropriately.

A config for this would look like:

Reference: Pre-Tokenized Dataset Documentation.

We reccomend this approach when you want granular control over the prompt formatting, special tokens, and masking, whilst letting Axolotl handle the tokenization. This is very useful if your dataset has unique prompts that differ across samples and where one single general template wouldn’t suffice.

In the example below, you could see that there is no proper structure. At the same time, it’s very flexible as there are no constraints on how your prompt can look.

Each prompt must be have a key called segments which is a list of { text, label }.

Reference: Template Free Documentation.

conversation messages are a list of messages which usually contain a role and content key.

Fun fact: Axolotl synonymously refers to “chat” messages as conversation messages due to how FastChat initially used this term to build a widely used fastchat conversation method for formatting chat messages prior to the creation of chat_templates.

The current most popular and convenient method for inference is to use chat_templates for formatting prompts. Axolotl supports using chat_templates for training to ensure that the model performs in the same environment as in inference.

Here’s a quick rundown on chat_template: A chat_template is a Jinja2 template which formats a list of messages into a prompt.

An example of a prompt formatted into a popular template called ChatML can be seen below:

Single prompt (pretty-printed):

The ChatML template is as follows:

The above prompt formatted into this template will result in:

By using delimiters (<|im_start|> and <|im_end|>), a prompt separates different speakers which helps the model identify which portion belongs to whom.

Older conversation datasets with the following format are colloquially called sharegpt datasets.

Newer conversation datasets usually follow the OpenAI format.

Axolotl supports both as well as allowing customization of any kind of key.

To properly use this method, it is important to identify three things:

Which chat_template would you use?

What are the keys in your dataset, and what are the possible roles? For example, in OpenAI format, the keys would be messages, role, and content, respectively, whereas the possible roles are system, user, and assistant.

What do you want to mask? For instance, only assistant messages, only last message, or nothing.

There are a lot of chat_templates out there. Axolotl supports the common ones: supported chat templates. For example, to use ChatML, it would be chat_template: chatml.

However, it is also possible to use the already configured template within the tokenizer by specifying chat_template: tokenizer_default. If you want a fallback (in case some tokenizer does not have it pre-configured), you can do chat_template: tokenizer_default_fallback_chatml to fallback to the ChatML template if a tokenizer template was not found.

One last but powerful approach is to bring your own template. This can be set via:

We currently default to OpenAI format for dataset keys, so if that’s your current dataset format, there’s nothing to do here.

If your dataset format is different, here are the keys you should check (with their defaults):

In some chat_templates (e.g. Gemma), the roles are hardcoded to user and assistant. Consequently, you may find it necessary to map the roles in your dataset to these above. We currently have some defaults that should work for common datasets, but if you get a KeyError, it would be necessary to add mapping for your roles. Here is an example of how it would look like:

In the example above, all gpt and model values are converted to assistant. All human values are converted to user.

The common use case for chat_template is for chat messages, therefore, it is common to mask all non-assistant messages. Assistant messages refer to the bot messages that you want the model to learn on.

To train on all assistant messages, you would set the following configs.

The train_on_eos config means that it would mask all EOS tokens for turns that aren’t assistant-turns. The other options are: all and last to choose which EOS to train on.

Perhaps, you want to train on assistant and narrator roles, you can simply add narrator to the list of roles_to_train. You would also need to add it to the mapping of roles above.

As chat_templates may use hardcoded EOS/EOT tokens that are different from the tokenizer’s EOS, it is highly recommended to set them. For example, ChatML uses <|im_end|> to end turns.

Once all the above steps are completed, you could combine all these configs together to form a bespoke configuration for your custom dataset.

If this config were to be applied to the sample dataset above, the output would look as such (which can be retrieved via axolotl preprocess config.yaml --debug):

The first number refers to the label, the second refers to the token_id. For example, -100 labels appear on non-assistant portions, meaning that they are masked during. For assistant portions, the label is the same as the token_id.

If during preprocess, there are a lot of warnings of Could not find content __ boundary, please check the FAQ section for chat_templates.

Please see docs here.

Instruction datasets are used to train instruction-following models and comprise a prompt, containing an instruction, and a single response. In contrast to chat datasets which may be multi-turn, instruct datasets are typically single-turn.

An example is of a common format called Alpaca:

Using those keys, a prompt can be built based on it.

This can be configured as such:

Axolotl supports many kinds of instruction dataset. All of them can be found in the Instruction Dataset Documentation with their respective type and sample row format.

Due to the myriad possibilities of instruction formats, Axolotl allows customizing your own instruction format without having to dive into the code directly.

In the example below, a sample row is used to output in mistral_v1 format.

The config sets that the field_instruction is actually named input, and the field_input is empty as we don’t have an input in this sample. Generally, instruction can be thought as the question to the model, and input as the additional information with output being the response. It is not necessary to have an input nor system. In the end, the most important part is to understand what format you want it to look like and how you can customize this to your use case.

Reference: Custom Instruct Prompt Format Documentation.

As there are multiple RLHF methods with their own dataset requirements. Please see RLHF documentation for more detail.

**Examples:**

Example 1 (json):
```json
{"text": "first row"}
{"text": "second row"}
...
```

Example 2 (yaml):
```yaml
pretraining_dataset: hf_org/name
```

Example 3 (yaml):
```yaml
pretraining_dataset:
  - path: json
    data_files:
      - A.jsonl
      - B.jsonl
      - C.jsonl
```

Example 4 (yaml):
```yaml
datasets:
  - path: hf_org/name
    type: completion
```

---

## Conversation

**URL:** https://docs.axolotl.ai/docs/dataset-formats/conversation.html

**Contents:**
- Conversation
- chat_template
  - Migrating from sharegpt
  - Examples
    - Training on last message
    - Overriding default chat template
    - Using default chat template with fallback
    - Custom Jinja template
    - Using template with different token for EOT and EOS
    - Using tool use

Chat Template strategy uses a jinja2 template that converts a list of messages into a prompt. Support using tokenizer’s template, a supported template, or custom jinja2.

See configs for full configs and supported templates.

Most configs can be adapted as follows:

We recommend checking the below examples for other usecases.

(Legacy) Using the default chat template in the tokenizer_config.json on OpenAI messages format, training on only last message.

If you receive an error like “chat_template choice is tokenizer_default but tokenizer’s chat_template is null.”, it means the tokenizer does not have a default chat_template. Follow the examples below instead to set a custom chat_template.

Using the gemma chat template to override the tokenizer_config.json’s chat template on OpenAI messages format, training on all assistant messages.

If you want to use built-in chat_template, use chat_template: tokenizer_default (this is set by default).

Using the tokenizer_config.json’s chat template or chatml as fallback if the former’s chat template does not exist, on OpenAI messages format, training on all assistant messages.

Using a custom jinja template on OpenAI messages format, training on all assistant messages.

Please make sure that your tokenizer.eos_token is same as EOS (End-of-Sequence) token in template. Otherwise, set eos_token under special_tokens:.

See config documentation for detailed explanations of “turn”, “last”, and “all” options for training on tokens.

Using eot_tokens requires each token that exists in chat_template to be a single token in the tokenizer. Otherwise, the tokenizer will split the token and cause unexpected behavior.

You can add those tokens as new tokens under tokens: or (recommended) override unused added_tokens via added_tokens_overrides:. See config for more details.

If EOS token only appears at the end of a prompt, train_on_eos: last is equivalent to train_on_eos: turn. Therefore, generally, you can leave them to their defaults and omit them.

Instead of passing tools via the system prompt, an alternative method would be to have the tools in a separate column and loaded via chat_template to let the template dynamically build it.

Tools need to follow JSON schema.

If you have tool arguments with same name but different dtypes (like "time": string and "time": number), please save arguments: as JSON string to prevent datasets from having casting issues.

Example config for Llama4:

Look into the chat_template you are using to see if it supports tools and what the expected role is for the tool answer. In the example above, the tool answer is expected to be in the tool or ipython role for llama4 template.

(Advanced) Using fine-grained control over tokens and turns to train in a conversation

For a data sample that looks like:

The configuration would look like:

It is not necessary to set both message_field_training and message_field_training_detail at once.

(For Qwen3 template only) Enable reasoning split, where the reasoning is split from the content and passed as a separate field into the template.

For example, a content can look like:

After split, it will look like:

ShareGPT is deprecated!. Please see chat_template section.

**Examples:**

Example 1 (json):
```json
{"messages": [{"role": "...", "content": "..."}, {"role": "...", "content": "..."}, ...]}
```

Example 2 (yaml):
```yaml
# old
chat_template: chatml
datasets:
  - path: ...
    type: sharegpt
    conversation: chatml

# new (if using tokenizer's chat_template)
datasets:
  - path: ...
    type: chat_template

    field_messages: conversations
    message_property_mappings:
      role: from
      content: value

# new (if setting a new chat_template like chatml, gemma, etc)
chat_template: chatml
datasets:
  - path: ...
    type: chat_template

    field_messages: conversations
    message_property_mappings:
      role: from
      content: value
```

Example 3 (yaml):
```yaml
datasets:
  - path: ...
    type: chat_template
    roles_to_train:
    train_on_eos:
```

Example 4 (yaml):
```yaml
chat_template: gemma # this overwrites the tokenizer's chat_template
datasets:
  - path: ...
    type: chat_template
    roles_to_train: ["assistant"]  # default value
```

---

## Pre-training

**URL:** https://docs.axolotl.ai/docs/dataset-formats/pretraining.html

**Contents:**
- Pre-training

For pretraining, there is no prompt template or roles. The only required field is text:

Axolotl usually loads the entire dataset into memory. This will be challenging for large datasets. Use the following config to enable streaming:

**Examples:**

Example 1 (json):
```json
{"text": "first row"}
{"text": "second row"}
...
```

Example 2 (yaml):
```yaml
pretraining_dataset:
  - name:
    path:
    split:
    text_column: # column in dataset with the data, usually `text`
    type: pretrain
    trust_remote_code:
    skip: # number of rows of data to skip over from the beginning
```

---

## Template-Free

**URL:** https://docs.axolotl.ai/docs/dataset-formats/template_free.html

**Contents:**
- Template-Free
- Background
  - Masking Inputs
  - You may not want prompt templates
  - The input_output format
- Usage
  - 1. Prepare Data
  - 2. Use type: input_output
  - 3. Check the prompts

One of the most popular features of axolotl is setting the following configuration value:

If you declare a dataset formats such as alpaca or chatml, axolotl knows what is an input (i.e. human) vs. an output (i.e. the assistant) and masks the input labels so that your model can focus on predicting the outputs only.

However, there are many situations where you don’t want to use one of these formats or templates. This is because they can:

You can construct your prompts without a template by using the input_output format, by setting type: input_output in your configuration file like this:

Unlike type: completion, which is also template-free, type: input_output allows you to mask segments of your text. More details on how this works are described below.

This is how you can use the input_output format:

To use the input_output format, collect your data in the following format into a jsonl file (below is the first row from the file output.jsonl` pretty printed):

Set label:false when you want to mask a segment of text so that the model isn’t trained on it. Some things to keep in mind:

[!IMPORTANT] 1. EOS, BOS, spaces, newlines etc. are entirely up to you. Axolotl concatenates all the segments as-is. The tokenizer doesn’t add anything additional. Notice how I added spaces, newlines, <s> (BOS), and </s> (EOS) myself. 2. Make sure you check the materialized output to validate that the prompt is getting assembled how you like.

Let’s materialize data with our output.jsonl file by setting type: input_output in our axolotl config:

You can use the following command to materialize your data. The --debug flag will print the tokens, along with the labels so you can verify that the correct items are being ignored:

The format is decoded_token(label, token_id), for example, <s>(1, 1) means that the token is <s>, the label is 1 and the token_id is 1. When the label is -100 then that token is ignored for training.

Here is another way to check the materialized output:

We can check that the right tokens are ignored by comparing the labels to each token:

If we look at the input data, the above table seems correct! (The jsonl version is repeated below for reference):

**Examples:**

Example 1 (yaml):
```yaml
train_on_inputs: false
```

Example 2 (yaml):
```yaml
train_on_inputs: false # Mask segments of your data
datasets:
  - path: output.jsonl
    type: input_output  # use template free prompt construction
```

Example 3 (bash):
```bash
$ head -n1 output.jsonl | python -m json.tool
```

Example 4 (unknown):
```unknown
{
    "segments": [
        {
            "label": true,
            "text": "<s>Hello\n"
        },
        {
            "label": true,
            "text": "hi there!. "
        },
        {
            "label": false,
            "text": "goodbye "
        },
        {
            "label": true,
            "text": "farewell</s>"
        }
    ]
}
```

---

## Dataset Formats

**URL:** https://docs.axolotl.ai/docs/dataset-formats/

**Contents:**
- Dataset Formats
- Pre-training
  - Pre-training from Hugging Face hub datasets
  - Pre-training from local dataset files
  - Pre-training without streaming
  - Pre-training dataset configuration tips
    - Setting max_steps
    - Group_by_length
  - Reference
- Supervised fine-tuning (SFT)

Axolotl is a training framework that aims to make the process convenient yet flexible to users by simply passing a config yaml file.

As there are a lot of available options in Axolotl, this guide aims to provide an simplify the user experience to choosing the proper choice.

Axolotl supports 3 kinds of training methods: pre-training, supervised fine-tuning, and preference-based post-training (e.g. DPO, ORPO, PRMs). Each method has their own dataset format which are described below.

This guide will mainly use JSONL as an introduction. Please refer to the dataset loading docs to understand how to load datasets from other sources.

For pretraining_dataset: specifically, please refer to the Pre-training section.

When aiming to train on large corpora of text datasets, pre-training is your go-to choice. Due to the size of these datasets, downloading the entire-datasets before beginning training would be prohibitively time-consuming. Axolotl supports streaming to only load batches into memory at a time.

A sample format for a pre-training dataset is as follows:

It is typically recommended to save your dataset as .jsonl due to its flexibility and simplicity.

Axolotl supports loading from a Hugging Face hub repo or from local files.

As an example, to train using a Hugging Face dataset hf_org/name, you can pass the following config:

Given a few corpus files: A.jsonl, B.jsonl, and C.jsonl, your config will look like the below:

While we recommend .jsonl, you can also use the other formats (csv, parquet, arrow, SQL, Webdataset) that are supported by Dataset.load_dataset

In the case that the dataset is small and can be loaded entirely into memory, another approach to running pre-training is to use the completion format. This would mean that the entire dataset is pre-tokenized instead of on-demand in streaming.

One benefit of this is that the tokenization can be performed separately on a CPU-only machine, and then transferred to a GPU machine for training to save costs.

For completion only, Axolotl would split texts if it exceeds the context length into multiple smaller prompts. If you are interested in having this for pretraining_dataset too, please let us know or help make a PR!

When using streaming for large datasets, Axolotl does not know in advance how large the dataset is and does not know when to stop.

Therefore, it is necessary to set max_steps: int in your config for pre-training to run, so that Axolotl knows when to stop training.

One step is equal to sequence_len * micro_batch_size * gradient_accumulation_steps * total_num_gpus tokens.

It is recommended to leave this off if downloading from Hugging Face hub as it would download the entire dataset which can be very large.

Please see docs here.

Supervised fine-tuning is the process of training models to respond to an instruction or chat input.

As there are a wide variety of dataset formats, Axolotl tries to support a majority of the formats available in public datasets.

Axolotl provides four approaches for loading datasets, however, it’s easier to work backwards from the dataset you have available to figure out which approach to use.

A flow chart is as follows:

Do you already have the dataset tokenized? If yes, check Pre-Tokenized Dataset.

Do you want to format the dataset yourself and manually choose each section to mask? If yes, check Template Free Dataset

Is your dataset in a “conversation” format, containing a list[messages]? If yes, check Conversation Dataset

Is your dataset in an “instruct” format, containing { instruction, response }? If yes, check Instruction Dataset

If you went through the flow chart and did not find one that matches, it is recommended to preprocess your dataset into one of the above or create a thread on Github Discussion.

You can mix and match within each approach or across approaches to train a model on a variety of datasets.

We suggest this approach when you want to bring your own tokenized dataset.

Axolotl expects the dataset to have three keys:

Make sure to add BOS/EOS tokens to your prompt and mask it appropriately.

A config for this would look like:

Reference: Pre-Tokenized Dataset Documentation.

We reccomend this approach when you want granular control over the prompt formatting, special tokens, and masking, whilst letting Axolotl handle the tokenization. This is very useful if your dataset has unique prompts that differ across samples and where one single general template wouldn’t suffice.

In the example below, you could see that there is no proper structure. At the same time, it’s very flexible as there are no constraints on how your prompt can look.

Each prompt must be have a key called segments which is a list of { text, label }.

Reference: Template Free Documentation.

conversation messages are a list of messages which usually contain a role and content key.

Fun fact: Axolotl synonymously refers to “chat” messages as conversation messages due to how FastChat initially used this term to build a widely used fastchat conversation method for formatting chat messages prior to the creation of chat_templates.

The current most popular and convenient method for inference is to use chat_templates for formatting prompts. Axolotl supports using chat_templates for training to ensure that the model performs in the same environment as in inference.

Here’s a quick rundown on chat_template: A chat_template is a Jinja2 template which formats a list of messages into a prompt.

An example of a prompt formatted into a popular template called ChatML can be seen below:

Single prompt (pretty-printed):

The ChatML template is as follows:

The above prompt formatted into this template will result in:

By using delimiters (<|im_start|> and <|im_end|>), a prompt separates different speakers which helps the model identify which portion belongs to whom.

Older conversation datasets with the following format are colloquially called sharegpt datasets.

Newer conversation datasets usually follow the OpenAI format.

Axolotl supports both as well as allowing customization of any kind of key.

To properly use this method, it is important to identify three things:

Which chat_template would you use?

What are the keys in your dataset, and what are the possible roles? For example, in OpenAI format, the keys would be messages, role, and content, respectively, whereas the possible roles are system, user, and assistant.

What do you want to mask? For instance, only assistant messages, only last message, or nothing.

There are a lot of chat_templates out there. Axolotl supports the common ones: supported chat templates. For example, to use ChatML, it would be chat_template: chatml.

However, it is also possible to use the already configured template within the tokenizer by specifying chat_template: tokenizer_default. If you want a fallback (in case some tokenizer does not have it pre-configured), you can do chat_template: tokenizer_default_fallback_chatml to fallback to the ChatML template if a tokenizer template was not found.

One last but powerful approach is to bring your own template. This can be set via:

We currently default to OpenAI format for dataset keys, so if that’s your current dataset format, there’s nothing to do here.

If your dataset format is different, here are the keys you should check (with their defaults):

In some chat_templates (e.g. Gemma), the roles are hardcoded to user and assistant. Consequently, you may find it necessary to map the roles in your dataset to these above. We currently have some defaults that should work for common datasets, but if you get a KeyError, it would be necessary to add mapping for your roles. Here is an example of how it would look like:

In the example above, all gpt and model values are converted to assistant. All human values are converted to user.

The common use case for chat_template is for chat messages, therefore, it is common to mask all non-assistant messages. Assistant messages refer to the bot messages that you want the model to learn on.

To train on all assistant messages, you would set the following configs.

The train_on_eos config means that it would mask all EOS tokens for turns that aren’t assistant-turns. The other options are: all and last to choose which EOS to train on.

Perhaps, you want to train on assistant and narrator roles, you can simply add narrator to the list of roles_to_train. You would also need to add it to the mapping of roles above.

As chat_templates may use hardcoded EOS/EOT tokens that are different from the tokenizer’s EOS, it is highly recommended to set them. For example, ChatML uses <|im_end|> to end turns.

Once all the above steps are completed, you could combine all these configs together to form a bespoke configuration for your custom dataset.

If this config were to be applied to the sample dataset above, the output would look as such (which can be retrieved via axolotl preprocess config.yaml --debug):

The first number refers to the label, the second refers to the token_id. For example, -100 labels appear on non-assistant portions, meaning that they are masked during. For assistant portions, the label is the same as the token_id.

If during preprocess, there are a lot of warnings of Could not find content __ boundary, please check the FAQ section for chat_templates.

Please see docs here.

Instruction datasets are used to train instruction-following models and comprise a prompt, containing an instruction, and a single response. In contrast to chat datasets which may be multi-turn, instruct datasets are typically single-turn.

An example is of a common format called Alpaca:

Using those keys, a prompt can be built based on it.

This can be configured as such:

Axolotl supports many kinds of instruction dataset. All of them can be found in the Instruction Dataset Documentation with their respective type and sample row format.

Due to the myriad possibilities of instruction formats, Axolotl allows customizing your own instruction format without having to dive into the code directly.

In the example below, a sample row is used to output in mistral_v1 format.

The config sets that the field_instruction is actually named input, and the field_input is empty as we don’t have an input in this sample. Generally, instruction can be thought as the question to the model, and input as the additional information with output being the response. It is not necessary to have an input nor system. In the end, the most important part is to understand what format you want it to look like and how you can customize this to your use case.

Reference: Custom Instruct Prompt Format Documentation.

As there are multiple RLHF methods with their own dataset requirements. Please see RLHF documentation for more detail.

**Examples:**

Example 1 (json):
```json
{"text": "first row"}
{"text": "second row"}
...
```

Example 2 (yaml):
```yaml
pretraining_dataset: hf_org/name
```

Example 3 (yaml):
```yaml
pretraining_dataset:
  - path: json
    data_files:
      - A.jsonl
      - B.jsonl
      - C.jsonl
```

Example 4 (yaml):
```yaml
datasets:
  - path: hf_org/name
    type: completion
```

---

## Dataset Formats

**URL:** https://docs.axolotl.ai/docs/dataset-formats

**Contents:**
- Dataset Formats
- Pre-training
  - Pre-training from Hugging Face hub datasets
  - Pre-training from local dataset files
  - Pre-training without streaming
  - Pre-training dataset configuration tips
    - Setting max_steps
    - Group_by_length
  - Reference
- Supervised fine-tuning (SFT)

Axolotl is a training framework that aims to make the process convenient yet flexible to users by simply passing a config yaml file.

As there are a lot of available options in Axolotl, this guide aims to provide an simplify the user experience to choosing the proper choice.

Axolotl supports 3 kinds of training methods: pre-training, supervised fine-tuning, and preference-based post-training (e.g. DPO, ORPO, PRMs). Each method has their own dataset format which are described below.

This guide will mainly use JSONL as an introduction. Please refer to the dataset loading docs to understand how to load datasets from other sources.

For pretraining_dataset: specifically, please refer to the Pre-training section.

When aiming to train on large corpora of text datasets, pre-training is your go-to choice. Due to the size of these datasets, downloading the entire-datasets before beginning training would be prohibitively time-consuming. Axolotl supports streaming to only load batches into memory at a time.

A sample format for a pre-training dataset is as follows:

It is typically recommended to save your dataset as .jsonl due to its flexibility and simplicity.

Axolotl supports loading from a Hugging Face hub repo or from local files.

As an example, to train using a Hugging Face dataset hf_org/name, you can pass the following config:

Given a few corpus files: A.jsonl, B.jsonl, and C.jsonl, your config will look like the below:

While we recommend .jsonl, you can also use the other formats (csv, parquet, arrow, SQL, Webdataset) that are supported by Dataset.load_dataset

In the case that the dataset is small and can be loaded entirely into memory, another approach to running pre-training is to use the completion format. This would mean that the entire dataset is pre-tokenized instead of on-demand in streaming.

One benefit of this is that the tokenization can be performed separately on a CPU-only machine, and then transferred to a GPU machine for training to save costs.

For completion only, Axolotl would split texts if it exceeds the context length into multiple smaller prompts. If you are interested in having this for pretraining_dataset too, please let us know or help make a PR!

When using streaming for large datasets, Axolotl does not know in advance how large the dataset is and does not know when to stop.

Therefore, it is necessary to set max_steps: int in your config for pre-training to run, so that Axolotl knows when to stop training.

One step is equal to sequence_len * micro_batch_size * gradient_accumulation_steps * total_num_gpus tokens.

It is recommended to leave this off if downloading from Hugging Face hub as it would download the entire dataset which can be very large.

Please see docs here.

Supervised fine-tuning is the process of training models to respond to an instruction or chat input.

As there are a wide variety of dataset formats, Axolotl tries to support a majority of the formats available in public datasets.

Axolotl provides four approaches for loading datasets, however, it’s easier to work backwards from the dataset you have available to figure out which approach to use.

A flow chart is as follows:

Do you already have the dataset tokenized? If yes, check Pre-Tokenized Dataset.

Do you want to format the dataset yourself and manually choose each section to mask? If yes, check Template Free Dataset

Is your dataset in a “conversation” format, containing a list[messages]? If yes, check Conversation Dataset

Is your dataset in an “instruct” format, containing { instruction, response }? If yes, check Instruction Dataset

If you went through the flow chart and did not find one that matches, it is recommended to preprocess your dataset into one of the above or create a thread on Github Discussion.

You can mix and match within each approach or across approaches to train a model on a variety of datasets.

We suggest this approach when you want to bring your own tokenized dataset.

Axolotl expects the dataset to have three keys:

Make sure to add BOS/EOS tokens to your prompt and mask it appropriately.

A config for this would look like:

Reference: Pre-Tokenized Dataset Documentation.

We reccomend this approach when you want granular control over the prompt formatting, special tokens, and masking, whilst letting Axolotl handle the tokenization. This is very useful if your dataset has unique prompts that differ across samples and where one single general template wouldn’t suffice.

In the example below, you could see that there is no proper structure. At the same time, it’s very flexible as there are no constraints on how your prompt can look.

Each prompt must be have a key called segments which is a list of { text, label }.

Reference: Template Free Documentation.

conversation messages are a list of messages which usually contain a role and content key.

Fun fact: Axolotl synonymously refers to “chat” messages as conversation messages due to how FastChat initially used this term to build a widely used fastchat conversation method for formatting chat messages prior to the creation of chat_templates.

The current most popular and convenient method for inference is to use chat_templates for formatting prompts. Axolotl supports using chat_templates for training to ensure that the model performs in the same environment as in inference.

Here’s a quick rundown on chat_template: A chat_template is a Jinja2 template which formats a list of messages into a prompt.

An example of a prompt formatted into a popular template called ChatML can be seen below:

Single prompt (pretty-printed):

The ChatML template is as follows:

The above prompt formatted into this template will result in:

By using delimiters (<|im_start|> and <|im_end|>), a prompt separates different speakers which helps the model identify which portion belongs to whom.

Older conversation datasets with the following format are colloquially called sharegpt datasets.

Newer conversation datasets usually follow the OpenAI format.

Axolotl supports both as well as allowing customization of any kind of key.

To properly use this method, it is important to identify three things:

Which chat_template would you use?

What are the keys in your dataset, and what are the possible roles? For example, in OpenAI format, the keys would be messages, role, and content, respectively, whereas the possible roles are system, user, and assistant.

What do you want to mask? For instance, only assistant messages, only last message, or nothing.

There are a lot of chat_templates out there. Axolotl supports the common ones: supported chat templates. For example, to use ChatML, it would be chat_template: chatml.

However, it is also possible to use the already configured template within the tokenizer by specifying chat_template: tokenizer_default. If you want a fallback (in case some tokenizer does not have it pre-configured), you can do chat_template: tokenizer_default_fallback_chatml to fallback to the ChatML template if a tokenizer template was not found.

One last but powerful approach is to bring your own template. This can be set via:

We currently default to OpenAI format for dataset keys, so if that’s your current dataset format, there’s nothing to do here.

If your dataset format is different, here are the keys you should check (with their defaults):

In some chat_templates (e.g. Gemma), the roles are hardcoded to user and assistant. Consequently, you may find it necessary to map the roles in your dataset to these above. We currently have some defaults that should work for common datasets, but if you get a KeyError, it would be necessary to add mapping for your roles. Here is an example of how it would look like:

In the example above, all gpt and model values are converted to assistant. All human values are converted to user.

The common use case for chat_template is for chat messages, therefore, it is common to mask all non-assistant messages. Assistant messages refer to the bot messages that you want the model to learn on.

To train on all assistant messages, you would set the following configs.

The train_on_eos config means that it would mask all EOS tokens for turns that aren’t assistant-turns. The other options are: all and last to choose which EOS to train on.

Perhaps, you want to train on assistant and narrator roles, you can simply add narrator to the list of roles_to_train. You would also need to add it to the mapping of roles above.

As chat_templates may use hardcoded EOS/EOT tokens that are different from the tokenizer’s EOS, it is highly recommended to set them. For example, ChatML uses <|im_end|> to end turns.

Once all the above steps are completed, you could combine all these configs together to form a bespoke configuration for your custom dataset.

If this config were to be applied to the sample dataset above, the output would look as such (which can be retrieved via axolotl preprocess config.yaml --debug):

The first number refers to the label, the second refers to the token_id. For example, -100 labels appear on non-assistant portions, meaning that they are masked during. For assistant portions, the label is the same as the token_id.

If during preprocess, there are a lot of warnings of Could not find content __ boundary, please check the FAQ section for chat_templates.

Please see docs here.

Instruction datasets are used to train instruction-following models and comprise a prompt, containing an instruction, and a single response. In contrast to chat datasets which may be multi-turn, instruct datasets are typically single-turn.

An example is of a common format called Alpaca:

Using those keys, a prompt can be built based on it.

This can be configured as such:

Axolotl supports many kinds of instruction dataset. All of them can be found in the Instruction Dataset Documentation with their respective type and sample row format.

Due to the myriad possibilities of instruction formats, Axolotl allows customizing your own instruction format without having to dive into the code directly.

In the example below, a sample row is used to output in mistral_v1 format.

The config sets that the field_instruction is actually named input, and the field_input is empty as we don’t have an input in this sample. Generally, instruction can be thought as the question to the model, and input as the additional information with output being the response. It is not necessary to have an input nor system. In the end, the most important part is to understand what format you want it to look like and how you can customize this to your use case.

Reference: Custom Instruct Prompt Format Documentation.

As there are multiple RLHF methods with their own dataset requirements. Please see RLHF documentation for more detail.

**Examples:**

Example 1 (json):
```json
{"text": "first row"}
{"text": "second row"}
...
```

Example 2 (yaml):
```yaml
pretraining_dataset: hf_org/name
```

Example 3 (yaml):
```yaml
pretraining_dataset:
  - path: json
    data_files:
      - A.jsonl
      - B.jsonl
      - C.jsonl
```

Example 4 (yaml):
```yaml
datasets:
  - path: hf_org/name
    type: completion
```

---

## Instruction Tuning

**URL:** https://docs.axolotl.ai/docs/dataset-formats/inst_tune.html

**Contents:**
- Instruction Tuning
- alpaca
- jeopardy
- oasst
- gpteacher
- reflection
- explainchoice
- concisechoice
- summarizetldr
- alpaca_chat

instruction; input(optional)

instruction; input(optional)

instruction with reflect; input(optional)

question, choices, (solution OR explanation)

question, choices, (solution OR explanation)

basic instruct for alpaca chat

question and answer for alpaca chat

question and answer for alpaca chat, for concise answers

question and answer for alpaca chat, for load_camel_ai

support for open orca datasets with included system prompts, instruct

in context question answering from an article

in context question answering (alternate)

in context question answering from an article, with default response for no answer from context

instruction and revision

instruction, adds additional eos tokens

For a dataset that is preprocessed for instruction purposes:

You can use this example in your YAML config:

See full config options under here.

**Examples:**

Example 1 (json):
```json
{"instruction": "...", "input": "...", "output": "..."}
```

Example 2 (json):
```json
{"question": "...", "category": "...", "answer": "..."}
```

Example 3 (json):
```json
{"INSTRUCTION": "...", "RESPONSE": "..."}
```

Example 4 (json):
```json
{"instruction": "...", "input": "...", "response": "..."}
```

---

## Stepwise Supervised Format

**URL:** https://docs.axolotl.ai/docs/dataset-formats/stepwise_supervised.html

**Contents:**
- Stepwise Supervised Format
- Stepwise Supervised
  - Example

The stepwise supervised format is designed for chain-of-thought (COT) reasoning datasets where each example contains multiple completion steps and a preference label for each step.

Here’s a simple example of a stepwise supervised dataset entry:

**Examples:**

Example 1 (json):
```json
{
  "prompt": "Which number is larger, 9.8 or 9.11?",
  "completions": [
    "The fractional part of 9.8 is 0.8, while the fractional part of 9.11 is 0.11.",
    "Since 0.11 is greater than 0.8, the number 9.11 is larger than 9.8."
  ],
  "labels": [true, false]
}
```

---




---

## 🚀 Usage

**Reference this template:** `@skill-axolotl.md`


**Platform-specific:**
- **GitHub Copilot**: Add to `.github/copilot-instructions.md`
- **Augment Code**: Use `aug context add` command
- **Cursor/Windsurf**: Reference in chat with `@filename`
- **Claude**: Add to Project Knowledge
- **ChatGPT**: Add to Custom GPT configuration
