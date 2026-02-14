---
id: skill-deepspeed
type: skill
name: deepspeed
description: Expert guidance for distributed training with DeepSpeed - ZeRO optimization
  stages, pipeline parallelism, FP16/BF16/FP8, 1-bit Adam, sparse attention
category: ai-research
complexity: medium
keywords:
- api
- github
- go
- optimization
- performance
- python
- rest
- test
capabilities: []
token_estimate: 25201
has_references: true
reference_count: 9
---

<!-- Converted from Claude Skill Template -->
<!-- Complexity: Medium -->
<!-- Estimated Tokens: ~25,201 -->
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




# deepspeed

> Expert guidance for distributed training with DeepSpeed - ZeRO optimization stages, pipeline parallelism, FP16/BF16/FP8, 1-bit Adam, sparse attention

# Deepspeed Skill

Comprehensive assistance with deepspeed development, generated from official documentation.

## When to Use This Skill

This skill should be triggered when:
- Working with deepspeed
- Asking about deepspeed features or APIs
- Implementing deepspeed solutions
- Debugging deepspeed code
- Learning deepspeed best practices

## Quick Reference

### Common Patterns

**Pattern 1:** DeepNVMe Contents Requirements Creating DeepNVMe Handles Using DeepNVMe Handles Blocking File Write Non-Blocking File Write Parallel File Write Pinned Tensors Putting it together Acknowledgements Appendix Advanced Handle Creation Performance Tuning DeepNVMe APIs General I/O APIs GDS-specific APIs Handle Settings APIs This tutorial will show how to use DeepNVMe for data transfers between persistent storage and tensors residing in host or device memory. DeepNVMe improves the performance and efficiency of I/O operations in Deep Learning applications through powerful optimizations built on Non-Volatile Memory Express (NVMe) Solid State Drives (SSDs), Linux Asynchronous I/O (libaio), and NVIDIA Magnum IOTM GPUDirect® Storage (GDS). Requirements Ensure your environment is properly configured to use DeepNVMe. First, you need to install DeepSpeed version >= 0.15.0. Next, ensure that the DeepNVMe operators are available in the DeepSpeed installation. The async_io operator is required for any DeepNVMe functionality, while the gds operator is required only for GDS functionality. You can confirm availability of each operator by inspecting the output of ds_report to check that compatible status is [OKAY]. Below is a snippet of ds_report output confirming the availability of both async_io and gds operators. If async_io operator is unavailable, you will need to install the appropriate libaio library binaries for your Linux flavor. For example, Ubuntu users will need to run apt install libaio-dev. In general, you should carefully inspect ds_report output for helpful tips such as the following: [WARNING] async_io requires the dev libaio .so object and headers but these were not found. [WARNING] async_io: please install the libaio-dev package with apt [WARNING] If libaio is already installed (perhaps from source), try setting the CFLAGS and LDFLAGS environment variables to where it can be found. To enable gds operator, you will need to install NVIDIA GDS by consulting the appropriate guide for bare-metal systems or Azure VMs (coming soon). Creating DeepNVMe Handles DeepNVMe functionality can be accessed through two abstractions: aio_handle and gds_handle. The aio_handle is usable on both host and device tensors. while gds_handle works only on CUDA tensors, but is more efficient. The first step to use DeepNVMe is to create a desired handle. aio_handle requires async_io operator, while gds_handle requires both async_io and gds operators. The following snippets illustrate aio_handle and gds_handle creation respectively. ### Create aio_handle from deepspeed.ops.op_builder import AsyncIOBuilder aio_handle = AsyncIOBuilder().load().aio_handle() ### Create gds_handle from deepspeed.ops.op_builder import GDSBuilder gds_handle = GDSBuilder().load().gds_handle() For simplicity, the above examples illustrate handle creation using default parameters. We expect that handles created with default parameters to provide good performance in most environments. However, you can see below for advanced handle creation. Using DeepNVMe Handles aio_handle and gds_handle provide identical APIs for storing tensors to files or loading tensors from files. A common feature of these APIs is that they take a tensor and a file path as arguments for the desired I/O operation. For best performance, pinned device or host tensors should be used for I/O operations (see here for details). For brevity, this tutorial will use aio_handle for illustration, but keep in mind that gds_handle works similarly. You can see the available APIs in a Python shell via tab completion on an aio_handle object . This is illustrated using tab completion of h.. >python Python 3.10.12 (main, Jul 29 2024, 16:56:48) [GCC 11.4.0] on linux Type "help", "copyright", "credits" or "license" for more information. >>> from deepspeed.ops.op_builder import AsyncIOBuilder >>> h = AsyncIOBuilder().load().aio_handle() >>> h. h.async_pread( h.free_cpu_locked_tensor( h.get_overlap_events( h.get_single_submit( h.new_cpu_locked_tensor( h.pwrite( h.sync_pread( h.wait( h.async_pwrite( h.get_block_size( h.get_queue_depth( h.get_intra_op_parallelism( h.pread( h.read( h.sync_pwrite( h.write( The APIs of interest for performing I/O operations are those named with pread and pwrite substrings. For brevity, we will focus on the file write APIs, namely sync_pwrite, async_pwrite, and pwrite. We will discuss only sync_pwrite and async_pwrite below because they are specializations of pwrite. Blocking File Write sync_pwrite provides the standard blocking semantics of Python file write. The example below illustrates using sync_pwrite to store a 1GB CUDA tensor to a local NVMe file. >>> import os >>> os.path.isfile('/local_nvme/test_1GB.pt') False >>> import torch >>> t=torch.empty(1024**3, dtype=torch.uint8).cuda() >>> from deepspeed.ops.op_builder import AsyncIOBuilder >>> h = AsyncIOBuilder().load().aio_handle() >>> h.sync_pwrite(t,'/local_nvme/test_1GB.pt') >>> os.path.isfile('/local_nvme/test_1GB.pt') True >>> os.path.getsize('/local_nvme/test_1GB.pt') 1073741824 Non-Blocking File Write An important DeepNVMe optimization is the non-blocking I/O semantics which enables Python threads to overlap computations with I/O operations. async_pwrite provides the non-blocking semantics for file writes. The Python thread can later use wait() to synchronize with the I/O operation. async_write can also be used to submit multiple back-to-back non-blocking I/O operations, of which can then be later blocked on using a single wait(). The example below illustrates using async_pwrite to store a 1GB CUDA tensor to a local NVMe file. >>> import os >>> os.path.isfile('/local_nvme/test_1GB.pt') False >>> import torch >>> t=torch.empty(1024**3, dtype=torch.uint8).cuda() >>> from deepspeed.ops.op_builder import AsyncIOBuilder >>> h = AsyncIOBuilder().load().aio_handle() >>> h.async_pwrite(t,'/local_nvme/test_1GB.pt') >>> h.wait() 1 >>> os.path.isfile('/local_nvme/test_1GB.pt') True >>> os.path.getsize('/local_nvme/test_1GB.pt') 1073741824 Warning for non-blocking I/O operations: To avoid data races and corruptions, .wait() must be carefully used to serialize the writing of source tensors, and the reading of destination tensors. For example, the following update of t during a non-blocking file write is unsafe and could corrupt /local_nvme/test_1GB.pt. >>> t=torch.empty(1024**3, dtype=torch.uint8).cuda() >>> from deepspeed.ops.op_builder import AsyncIOBuilder >>> h = AsyncIOBuilder().load().aio_handle() >>> h.async_pwrite(t,'/local_nvme/test_1GB.pt') >>> t += 1 # <--- Data race; avoid by preceding with `h.wait()` Similar safety problems apply to reading the destination tensor of a non-blocking file read without .wait() synchronization. Parallel File Write An important DeepNVMe optimization is the ability to parallelize individual I/O operations. This optimization is enabled by specifying the desired parallelism degree when constructing a DeepNVMe handle. Subsequent I/O operations with that handle are automatically parallelized over the requested number of host or device threads, as appropriate. I/O parallelism is composable with either the blocking or non-blocking I/O APIs. The example below illustrates 4-way parallelism of a file write using async_pwrite. Note the use of intra_op_parallelism argument to specify the desired parallelism degree in handle creation. >>> import os >>> os.path.isfile('/local_nvme/test_1GB.pt') False >>> import torch >>> t=torch.empty(1024**3, dtype=torch.uint8).cuda() >>> from deepspeed.ops.op_builder import AsyncIOBuilder >>> h = AsyncIOBuilder().load().aio_handle(intra_op_parallelism=4) >>> h.async_pwrite(t,'/local_nvme/test_1GB.pt') >>> h.wait() 1 >>> os.path.isfile('/local_nvme/test_1GB.pt') True >>> os.path.getsize('/local_nvme/test_1GB.pt') 1073741824 Pinned Tensors A key part of DeepNVMe optimizations is using direct memory access (DMA) for I/O operations, which requires that the host or device tensor be pinned. To pin host tensors, you can use mechanisms provided by Pytorch or DeepSpeed Accelerators. The following example illustrates writing a pinned CPU tensor to a local NVMe file. >>> import os >>> os.path.isfile('/local_nvme/test_1GB.pt') False >>> import torch >>> t=torch.empty(1024**3, dtype=torch.uint8).pin_memory() >>> from deepspeed.ops.op_builder import AsyncIOBuilder >>> h = AsyncIOBuilder().load().aio_handle() >>> h.async_pwrite(t,'/local_nvme/test_1GB.pt') >>> h.wait() 1 >>> os.path.isfile('/local_nvme/test_1GB.pt') True >>> os.path.getsize('/local_nvme/test_1GB.pt') 1073741824 On the other hand,gds_handle provides new_pinned_device_tensor() and pin_device_tensor() functions for pinning CUDA tensors. The following example illustrates writing a pinned CUDA tensor to a local NVMe file. >>> import os >>> os.path.isfile('/local_nvme/test_1GB.pt') False >>> import torch >>> t=torch.empty(1024**3, dtype=torch.uint8).cuda() >>> from deepspeed.ops.op_builder import GDSBuilder >>> h = GDSBuilder().load().gds_handle() >>> h.pin_device_tensor(t) >>> h.async_pwrite(t,'/local_nvme/test_1GB.pt') >>> h.wait() 1 >>> os.path.isfile('/local_nvme/test_1GB.pt') True >>> os.path.getsize('/local_nvme/test_1GB.pt') 1073741824 >>> h.unpin_device_tensor(t) Putting it together We hope that the above material helps you to get started with DeepNVMe. You can also use the following links to see DeepNVMe usage in real-world Deep Learning applications. Parameter swapper in ZeRO-Inference and ZeRO-Infinity. Optimizer swapper in ZeRO-Infinity. Gradient swapper in ZeRO-Infinity. Simple file read and write operations. Acknowledgements This tutorial has been significantly improved by feedback from Guanhua Wang, Masahiro Tanaka, and Stas Bekman. Appendix Advanced Handle Creation Achieving peak I/O performance with DeepNVMe requires careful configuration of handle creation. In particular, the parameters of aio_handle and gds_handle constructors are performance-critical because they determine how efficiently DeepNVMe interacts with the underlying storage subsystem (i.e., libaio, GDS, PCIe, and SSD). For convenience we make it possible to create handles using default parameter values which will provide decent performance in most scenarios. However, squeezing out every available performance in your environment will likely require tuning the constructor parameters, namely block_size, queue_depth, single_submit, overlap_events, and intra_op_parallelism. The aio_handle constructor parameters and default values are illustrated below: >>> from deepspeed.ops.op_builder import AsyncIOBuilder >>> help(AsyncIOBuilder().load().aio_handle()) Help on aio_handle in module async_io object: class aio_handle(pybind11_builtins.pybind11_object) | Method resolution order: | aio_handle | pybind11_builtins.pybind11_object | builtins.object | | Methods defined here: | | __init__(...) | __init__(self: async_io.aio_handle, block_size: int = 1048576, queue_depth: int = 128, single_submit: bool = False, overlap_events: bool = False, intra_op_parallelism: int = 1) -> None | | AIO handle constructor Performance Tuning As discussed earlier, achieving peak DeepNVMe performance for a target workload or environment requires using optimally configured aio_handle or gds_handle handles. For configuration convenience, we provide a utility called ds_nvme_tune to automate the discovery of optimal DeepNVMe configurations. ds_nvme_tune automatically explores a user-specified or default configuration space and recommends the option that provides the best read and write performance. Below is an example usage of ds_nvme_tune to tune aio_handle data transfers between GPU memory and a local NVVMe SSD mounted on /local_nvme. This example used the default configuration space of ds_nvme_tune for tuning. $ ds_nvme_tune --nvme_dir /local_nvme --gpu Running DeepNVMe performance tuning on ['/local_nvme/'] Best performance (GB/sec): read = 3.69, write = 3.18 { "aio": { "single_submit": "false", "overlap_events": "true", "intra_op_parallelism": 8, "queue_depth": 32, "block_size": 1048576 } } The above tuning was executed on a Lambda workstation equipped with two NVIDIA A6000-48GB GPUs, 252GB of DRAM, and a CS3040 NVMe 2TB SDD with peak read and write speeds of 5.6 GB/s and 4.3 GB/s respectively. The tuning required about four and half minutes. Based on the results, one can expect to achieve read and write transfer speeds of 3.69 GB/sec and 3.18 GB/sec respectively by using an aio_handle configured as below. >>> from deepspeed.ops.op_builder import AsyncIOBuilder >>> h = AsyncIOBuilder().load().aio_handle(block_size=1048576, queue_depth=32, single_submit=False, overlap_events=True, intra_op_parallelism=8) The full command line options of ds_nvme_tune can be obtained via the normal -h or --help. usage: ds_nvme_tune [-h] --nvme_dir NVME_DIR [NVME_DIR ...] [--sweep_config SWEEP_CONFIG] [--no_read] [--no_write] [--io_size IO_SIZE] [--gpu] [--gds] [--flush_page_cache] [--log_dir LOG_DIR] [--loops LOOPS] [--verbose] options: -h, --help show this help message and exit --nvme_dir NVME_DIR [NVME_DIR ...] Directory in which to perform I/O tests. A writeable directory on a NVMe device. --sweep_config SWEEP_CONFIG Performance sweep configuration json file. --no_read Disable read performance measurements. --no_write Disable write performance measurements. --io_size IO_SIZE Number of I/O bytes to read/write for performance measurements. --gpu Test tensor transfers between GPU device and NVME device. --gds Run the sweep over NVIDIA GPUDirectStorage operator --flush_page_cache Page cache will not be flushed and reported read speeds may be higher than actual ***Requires sudo access***. --log_dir LOG_DIR Output directory for performance log files. Default is ./_aio_bench_logs --loops LOOPS Count of operation repetitions --verbose Print debugging information. DeepNVMe APIs For convenience, we provide listing and brief descriptions of the DeepNVMe APIs. General I/O APIs The following functions are used for I/O operations with both aio_handle and gds_handle. Function Description async_pread Non-blocking file read into tensor sync_pread Blocking file read into tensor pread File read with blocking and non-blocking options async_pwrite Non-blocking file write from tensor sync_pwrite Blocking file write from tensor pwrite File write with blocking and non-blocking options wait Wait for non-blocking I/O operations to complete GDS-specific APIs The following functions are available only for gds_handle Function Description new_pinned_device_tensor Allocate and pin a device tensor free_pinned_device_tensor Unpin and free a device tensor pin_device_tensor Pin a device tensor unpin_device_tensor unpin a device tensor Handle Settings APIs The following APIs can be used to probe handle configuration. Function Description get_queue_depth Return queue depth setting get_single_submit Return whether single_submit is enabled get_intra_op_parallelism Return I/O parallelism degree get_block_size Return I/O block size setting get_overlap_events Return whether overlap_event is enabled Updated: November 5, 2025 Previous Next

```
libaio
```

**Pattern 2:** Mixture of Experts for NLG models Contents 1. Installation 2. Training NLG+MoE models 2.1. Changes to the model 2.2. Pre-training the Standard MoE model 2.3. Pre-training the PR-MoE model 2.4. Training MoS with reduced model size In this tutorial, we introduce how to apply DeepSpeed Mixture of Experts (MoE) to NLG models, which reduces the training cost by 5 times and reduce the MoE model size by 3 times (details in our Blog). We use the GPT-3 like models in Megatron-LM framework as the example. Before reading this tutorial, we recommend to first read the tutorials about Mixture of Experts and Megatron-LM GPT pre-training. 1. Installation You would need to install DeepSpeed v0.6.0 or higher to use the MoE feature. The MoE for NLG model examples are in the Megatron-DeepSpeed repo under the MoE folder. 2. Training NLG+MoE models 2.1. Changes to the model To apply MoE to the GPT-style model, we made several changes in Megatron framework, mostly in megatron/model/ where we add the MoE layers into the model. 2.2. Pre-training the Standard MoE model We provide example training scripts under examples_deepspeed/MoE which we used to perform the experiments in our Blog. There are a few new hyperparameters for standard MoE model: --num-experts: the number of experts per MoE layer. In our experiments we set it to 128. Larger number of experts tend to provide better convergence, but it’s a diminishing return. --moe-expert-parallel-size: degree of the MoE expert parallelism. In other words, there will be num-experts/moe-expert-parallel-size experts on each GPU. Thus --moe-expert-parallel-size should be no more than both number of GPUs, and --num-experts. --moe-loss-coeff: scaling coefficient for adding MoE loss to model loss. In our experiments we find that 0.01 is a good setting. --moe-train-capacity-factor, --moe-eval-capacity-factor, --moe-min-capacity: these configs determine how many tokens can a single expert handle. Larger numbers could lead to better convergence, but would also lead to slower training since the load would be more unbalanced on different experts. --disable-moe-token-dropping: this will completely remove the limitation of how many tokens can a single expert handle. For the same reason as above, we only recommend using this during inference/eval. 2.3. Pre-training the PR-MoE model PR-MoE is a new designed MoE models, standing for Pyramid-Residual-MoE, which improves the parameter efficiency up to 3x as compared to standard MoE. Please see our Blog for more details. We provide example training scripts under examples_deepspeed/MoE. There are a few different hyperparameters for PR-MoE model compared to standard MoE: --num-experts: Instead of providing a single number, to enable Pyramid-MoE, you need to provide a list, whose length is the same as the number of MoE layers. We suggest to use more experts in the latter stage (close to output) of the model. --mlp-type: chosen from [standard, residual]. When it is residual, Residual-MoE is enabled. In addition to the new hyperparameters above for standard MoE and PR-MoE, for NLG+MoE models we found that it’s helpful to lower the learning rate and increase the learning rate decay duration compared to the base dense model. Details of our tuning can be found in the example training scripts. Regarding training data, we are not able to release our internal data but any public data for Megatron-LM pre-training can be directly used to train MoE models (with the caveat that it might not provide the exact same model quality as in our experiments). For example, we evaluated The Pile dataset (pile.eleuther.ai, github.com/EleutherAI/the-pile) for both dense and MoE models. Table 1 below shows that this public data provides similar evaluation results as our internal data. Model size LAMBADA: completion prediction PIQA: commonsense reasoning BoolQ: reading comprehension RACE-h: reading comprehension TriviaQA: question answering WebQs: question answering Dense NLG: 350M, internal data 0.5203 0.6931 0.5364 0.3177 0.0321 0.0157 350M, public Pile 0.5106 0.6589 0.5933 0.3196 0.0257 0.0064 Standard MoE NLG: 350M+MoE-128, internal data 0.6270 0.7459 0.6046 0.3560 0.1658 0.0517 350M+MoE-128, public Pile 0.6128 0.7323 0.6040 0.3349 0.1111 0.0335 PR-MoE NLG: 350M+MoE-128, internal data 0.6365 0.7399 0.5988 0.3569 0.1630 0.0473 PR-MoE + MoS NLG: 350M+MoE-128, internal data 0.6346 0.7334 0.5807 0.3483 0.1369 0.0522 Table 1: Zero-shot evaluation results (last six columns) for different dense and MoE NLG models. All zero-shot evaluation results use the accuracy metric. 2.4. Training MoS with reduced model size MoS, standing for Mixture-of-Students, is a staged distillation-based technique for compressing large MoE models. MoS further reduces the model size by 12.5%, leading to up 3.7x model size reduction when combined with PR-MoE over the standard MoE. The reduced model size helps reduce the latency and cost during inference. To train an MoS model, one needs to specify a few additional parameters. We will use PR-MoE as an example: --mos: This would enable Mixture-of-Students via knowledge distillation. --load-teacher: This specifies the path to the teacher model checkpoint. This is a mandatory argument for using MoS and the teacher model checkpoint can be obtained by either training a standard MoE or the PR-MoE. num-layers-teacher, --hidden-size-teacher, --hidden-size-teacher, --num-experts-teacher: In addition to the teacher model checkpoint path, we also need to specify the model architecture of the teacher model such as its number of layers, hidden dimension size, and the number of experts per MoE layer. In the case of PR-MoE, we need to also provide a list of experts for the teacher model, where we remove a few expert layers from the teacher model. In addition to the new parameters above, we observe that using the teacher PR-MoE during the entire training process may adversely impact the final student model accuracy. In our experiments, we use a staged distillation method by stopping distillation early in the training process (e.g., after 400K steps) and perform optimization only against the standard language modeling loss for the rest of the training. We provide example training scripts under examples_deepspeed/MoE. Details of our parameter settings can be found in the example training scripts. The performance results of MoS can be seen from our blog post and our paper. Updated: November 5, 2025 Previous Next

```
megatron/model/
```

**Pattern 3:** MoS, standing for Mixture-of-Students, is a staged distillation-based technique for compressing large MoE models. MoS further reduces the model size by 12.5%, leading to up 3.7x model size reduction when combined with PR-MoE over the standard MoE. The reduced model size helps reduce the latency and cost during inference. To train an MoS model, one needs to specify a few additional parameters. We will use PR-MoE as an example:

```
--mos
```

**Pattern 4:** Learning Rate Range Test Contents Learning Rate Range Test (LRRT) Prerequisites LRRT Parameters Required Model Configuration Changes PyTorch Example: Tuning for Large Batch Sizes This tutorial shows how to use to perform Learning Rate range tests in PyTorch. Learning Rate Range Test (LRRT) Learning rate range test ( LRRT ) is a method for discovering the largest learning rate values that can be used to train a model without divergence. Data scientists are often interested in this information because large learning rates lead to faster model convergence than a small learning rates. Moreover, large learning rates are crucial in learning rate schedules such as CLR and 1Cycle, which are used to train effectively with large batch sizes. DeepSpeed provides LRRT for model training in PyTorch frameworks. Prerequisites To use DeepSpeed’s LRRT, you must satisfy the following two conditions: Integrate DeepSpeed into your training script using the Getting Started guide. Add the parameters to configure LRRT to the parameters of your model. The LRRT parameters are defined below. LRRT Parameters LRRT works by linearly increasing the learning rate by a predefined amount, at predefined intervals. Thus, LRRT is a form of learning rate schedule because it defines how and when the learning rate should change during model training. To configure LRRT, you will need to set these parameters: lr_range_test_min_lr : The initial learning rate for training (float) lr_range_test_step_size: The interval for scaling up learning rate, defined in training steps (integer) lr_range_test_step_rate: The scaling factor for increasing learning rate (float) lr_range_test_staircase: If true, learning rate is changed every lr_range_test_step_size training steps, otherwise learning rate is changed at every training step (boolean) Required Model Configuration Changes We will illustrate the required model configuration changes an example LRRT schedule that: Starts training with an initial learning rate of 0.0001 Uses a scaling rate of 5 Uses a scaling interval of 200 training steps Scales learning rate at every training step, i.e., does not use staircase PyTorch For PyTorch models, LRRT is implemented as a learning rate scheduler, a feature that is available in PyTorch versions 1.0.1 and newer. Thus, you can add a "scheduler" entry of type "LRRangeTest" into your model configuration as illustrated below: "scheduler": { "type": "LRRangeTest", "params": { "lr_range_test_min_lr": 0.0001, "lr_range_test_step_size": 200, "lr_range_test_step_rate": 5, "lr_range_test_staircase": false } } Example: Tuning for Large Batch Sizes We illustrate how LRRT can benefit data scientists with a snippet of our experience of tuning an internal production model to converge efficiently on larger batch sizes, as we scaled from one GPU (batch size 512) to four GPUs (batch size 2048). Our goal was to train the model with the larger batch size to match the performance of the smaller batch size using the same amount of data samples. The challenge here is the well known problem of slow convergence of large batch size training. Our approach was to use a 1Cycle schedule in DeepSpeed to tackle this problem, and we used LRRT to configure the schedule. In the plots below, we illustrate using LRRT to discover the maximum learning rates for effective training with batch size 2048. The plot on the left shows the impact of large learning rates on validation loss over the first 9000 batches of training. The plot on the right shows the learning rate values during the same period of training. Using grid search we discover that the best fixed learning rate for the batch size 2048 is 0.0002. The blue line (lr=0.0002) represents training with this fixed learning rate. We compare the two LRRT schedules with this fixed learning rate. The orange (lr_range_test_step_rate=5) and gray (lr_range_test_step_rate=50) lines represent training with similar LRRT schedules that differ only in lr_range_test_step_rate values. Although the LRRT schedules start from the same base learning rate, the gray line’s learning rate grows about 10 times faster than the orange line. Also, the learning rates of the LRRT schedules had grown larger than that of the blue line in the presented data points. We subsequently refer to the gray line as “fast growing”, and the orange line as “slow growing” LRRT schedules respectively. We make the following observations from this small example. Larger learning rates clearly benefit model performance, up to some point. The fast growing LRRT schedule achieves validation loss of 0.46 after 3000 batches, which the fixed learning rate does not achieve with 9000 batches. The slow growing LRRT does not match that score until after 6000 batches, however it maintains an increasing performance advantage over the fixed learning rate. There is an upper bound on learning rate values that are useful for training the model. The fast growing LRRT schedule hits this boundary quickly and diverges, while the slow growing LRRT will later diverge for the same reason. LRRT helped us discover these boundaries quickly, using less than 2% of the training data. These boundaries are useful information for constructing learning rate schedules. These observations from LRRT helped us to configure the learning rate boundaries and the cycle span for a 1Cycle schedule that solves the problem, as shown below. "OneCycle": { "cycle_min_lr": 0.002, "cycle_max_lr": 0.005, "cycle_first_step_size": 2000, "cycle_second_step_size": 2000, ... } In our experience these are four most critical parameters of 1Cycle schedules. We chose to use the slower LRRT schedule (lr_range_test_step_rate=5) to set cycle_min_lr because it achieves the best loss and the faster schedule diverges fairly quickly. We set cycle_max_lr to 0.005 even though the plot shows that performance was still improving at slightly higher learning rate. This is because we observed that if we wait till the maximum learning rate, the model could be at the point of divergence and impossible to recover. Since it takes 8000 batches for the learning rate to become 0.005, we set cycle_first_step_size and (cycle_second_step_size) to 2000 which is the number of steps that it takes for four GPUs to process 8000 batches. We hope this brief example sparks your imagination on using LRRT for your own unique tuning challenges. Updated: November 5, 2025 Previous Next

```
lr_range_test_min_lr
```

**Pattern 5:** Training Overview and Features Contents Overview Distributed, Effective, and Efficient Training with Ease Speed Memory efficiency Scalability Communication efficiency Data efficiency Supporting long sequence length Fast convergence for effectiveness Good Usability Features Distributed Training with Mixed Precision Mixed Precision Training Single-GPU, Multi-GPU, and Multi-Node Training Pipeline Parallelism Model Parallelism Support for Custom Model Parallelism Integration with Megatron-LM The Zero Redundancy Optimizer Optimizer State and Gradient Partitioning Activation Partitioning Constant Buffer Optimization (CBO) Contiguous Memory Optimization (CMO) ZeRO-Offload Additional Memory and Bandwidth Optimizations Smart Gradient Accumulation Communication Overlapping Training Features Simplified training API Activation Checkpointing API Gradient Clipping Automatic loss scaling with mixed precision Training Optimizers 1-bit Adam, 0/1 Adam and 1-bit LAMB optimizers with up to 26x less communication Fused Adam optimizer and arbitrary torch.optim.Optimizer CPU-Adam: High-Performance vectorized implementation of Adam Memory bandwidth optimized FP16 Optimizer Large Batch Training with LAMB Optimizer Memory-Efficient Training with ZeRO Optimizer Training Agnostic Checkpointing Advanced parameter search Learning Rate Range Test 1Cycle Learning Rate Schedule Simplified Data Loader Data Efficiency Curriculum Learning Performance Analysis and Debugging Wall Clock Breakdown Timing Activation Checkpoint Functions Flops Profiler Autotuning Monitor Communication Logging Sparse Attention Mixture of Experts (MoE) Overview Training advanced deep learning models is challenging. Beyond model design, model scientists also need to set up the state-of-the-art training techniques such as distributed training, mixed precision, gradient accumulation, and checkpointing. Yet still, scientists may not achieve the desired system performance and convergence rate. Large model sizes are even more challenging: a large model easily runs out of memory with pure data parallelism and it is difficult to use model parallelism. DeepSpeed addresses these challenges to accelerate model development and training. Distributed, Effective, and Efficient Training with Ease The DeepSpeed API is a lightweight wrapper on PyTorch. This means that you can use everything you love in PyTorch and without learning a new platform. In addition, DeepSpeed manages all of the boilerplate state-of-the-art training techniques, such as distributed training, mixed precision, gradient accumulation, and checkpoints so that you can focus on your model development. Most importantly, you can leverage the distinctive efficiency and effectiveness benefit of DeepSpeed to boost speed and scale with just a few lines of code changes to your PyTorch models. Speed DeepSpeed achieves high performance and fast convergence through a combination of efficiency optimizations on compute/communication/memory/IO and effectiveness optimizations on advanced hyperparameter tuning and optimizers. For example: DeepSpeed trains BERT-large to parity in 44 mins using 1024 V100 GPUs (64 DGX-2 boxes) and in 2.4 hours using 256 GPUs (16 DGX-2 boxes). BERT-large Training Times Devices Source Training Time 1024 V100 GPUs DeepSpeed 44 min 256 V100 GPUs DeepSpeed 2.4 hr 64 V100 GPUs DeepSpeed 8.68 hr 16 V100 GPUs DeepSpeed 33.22 hr BERT code and tutorials will be available soon. DeepSpeed trains GPT2 (1.5 billion parameters) 3.75x faster than state-of-art, NVIDIA Megatron on Azure GPUs. Read more: GPT tutorial Memory efficiency DeepSpeed provides memory-efficient data parallelism and enables training models without model parallelism. For example, DeepSpeed can train models with up to 13 billion parameters on a single GPU. In comparison, existing frameworks (e.g., PyTorch’s Distributed Data Parallel) run out of memory with 1.4 billion parameter models. DeepSpeed reduces the training memory footprint through a novel solution called Zero Redundancy Optimizer (ZeRO). Unlike basic data parallelism where memory states are replicated across data-parallel processes, ZeRO partitions model states and gradients to save significant memory. Furthermore, it also reduces activation memory and fragmented memory. The current implementation (ZeRO-2) reduces memory by up to 8x relative to the state-of-art. You can read more about ZeRO in our paper, and in our blog posts related to ZeRO-1 and ZeRO-2. With this impressive memory reduction, early adopters of DeepSpeed have already produced a language model (LM) with over 17B parameters called Turing-NLG, establishing a new SOTA in the LM category. For model scientists with limited GPU resources, ZeRO-Offload leverages both CPU and GPU memory for training large models. Using a machine with a single GPU, our users can run models of up to 13 billion parameters without running out of memory, 10x bigger than the existing approaches, while obtaining competitive throughput. This feature democratizes multi-billion-parameter model training and opens the window for many deep learning practitioners to explore bigger and better models. Scalability DeepSpeed supports efficient data parallelism, model parallelism, pipeline parallelism and their combinations, which we call 3D parallelism. 3D parallelism of DeepSpeed provides system support to run models with trillions of parameters, read more in our press-release and tutorial. DeepSpeed can run large models more efficiently, up to 10x faster for models with various sizes spanning 1.5B to hundred billion. More specifically, the data parallelism powered by ZeRO is complementary and can be combined with different types of model parallelism. It allows DeepSpeed to fit models using lower degree of model parallelism and higher batch size, offering significant performance gains compared to using model parallelism alone. Read more: ZeRO paper, and GPT tutorial. The figure depicts system throughput improvements of DeepSpeed (combining ZeRO-powered data parallelism with model parallelism of NVIDIA Megatron-LM) over using Megatron-LM alone. Communication efficiency Pipeline parallelism of DeepSpeed reduce communication volume during distributed training, which allows users to train multi-billion-parameter models 2–7x faster on clusters with limited network bandwidth. 1-bit Adam, 0/1 Adam and 1-bit LAMB reduce communication volume by up to 26x while achieving similar convergence efficiency to Adam, allowing for scaling to different types of GPU clusters and networks. 1-bit Adam blog post, 1-bit Adam tutorial, 0/1 Adam tutorial, 1-bit LAMB tutorial. Data efficiency DeepSpeed Data Efficiency Library provides efficient data sampling via curriculum learning and efficient data routing via random layerwise token dropping. The composed solution enables up to 2x data and 2x time saving during GPT-3/BERT pretraining and GPT/ViT finetuning, or further improve model quality under the same data/time. See more in the tutorial. Supporting long sequence length DeepSpeed offers sparse attention kernels—an instrumental technology to support long sequences of model inputs, whether for text, image, or sound. Compared with the classic dense Transformers, it powers an order-of-magnitude longer input sequence and obtains up to 6x faster execution with comparable accuracy. It also outperforms state-of-the-art sparse implementations with 1.5–3x faster execution. Furthermore, our sparse kernels support efficient execution of flexible sparse format and empower users to innovate on their custom sparse structures. Read more here. Fast convergence for effectiveness DeepSpeed supports advanced hyperparameter tuning and large batch size optimizers such as LAMB. These improve the effectiveness of model training and reduce the number of samples required to convergence to desired accuracy. Read more: Tuning tutorial. Good Usability Only a few lines of code changes are needed to enable a PyTorch model to use DeepSpeed and ZeRO. Compared to current model parallelism libraries, DeepSpeed does not require a code redesign or model refactoring. It also does not put limitations on model dimensions (such as number of attention heads, hidden sizes, and others), batch size, or any other training parameters. For models of up to 13 billion parameters, you can use ZeRO-powered data parallelism conveniently without requiring model parallelism, while in contrast, standard data parallelism will run out of memory for models with more than 1.4 billion parameters. In addition, DeepSpeed conveniently supports flexible combination of ZeRO-powered data parallelism with custom model parallelisms, such as tensor slicing of NVIDIA’s Megatron-LM. Features Below we provide a brief feature list, see our detailed feature overview for descriptions and usage. Distributed Training with Mixed Precision 16-bit mixed precision Single-GPU/Multi-GPU/Multi-Node Model Parallelism Support for Custom Model Parallelism Integration with Megatron-LM Pipeline Parallelism 3D Parallelism The Zero Redundancy Optimizer Optimizer State and Gradient Partitioning Activation Partitioning Constant Buffer Optimization Contiguous Memory Optimization ZeRO-Offload Leverage both CPU/GPU memory for model training Support 10B model training on a single GPU Ultra-fast dense transformer kernels Sparse attention Memory- and compute-efficient sparse kernels Support 10x long sequences than dense Flexible support to different sparse structures 1-bit Adam, 0/1 Adam and 1-bit LAMB Custom communication collective Up to 26x communication volume saving Additional Memory and Bandwidth Optimizations Smart Gradient Accumulation Communication/Computation Overlap Training Features Simplified training API Gradient Clipping Automatic loss scaling with mixed precision Training Optimizers Fused Adam optimizer and arbitrary torch.optim.Optimizer Memory bandwidth optimized FP16 Optimizer Large Batch Training with LAMB Optimizer Memory efficient Training with ZeRO Optimizer CPU-Adam Training Agnostic Checkpointing Advanced Parameter Search Learning Rate Range Test 1Cycle Learning Rate Schedule Simplified Data Loader Data Efficiency Efficient data sampling via curriculum learning and efficient data routing via random layerwise token dropping Up to 2x data and 2x time saving during GPT-3/BERT pretraining and GPT/ViT finetuning Or further improve model quality under the same data/time Curriculum Learning A curriculum learning-based data pipeline that presents easier or simpler examples earlier during training Stable and 3.3x faster GPT-2 pre-training with 8x/4x larger batch size/learning rate while maintaining token-wise convergence speed Complementary to many other DeepSpeed features Note that the Data Efficiency Library above provides more general curriculum learning support. This legacy curriculum learning feature is still supported but we recommend to use the Data Efficiency Library. Progressive Layer Dropping Efficient and robust compressed training Up to 2.5x convergence speedup for pre-training Performance Analysis and Debugging Mixture of Experts (MoE) title: “Feature Overview” layout: single permalink: /features/ toc: true toc_label: “Contents” — Distributed Training with Mixed Precision Mixed Precision Training Enable 16-bit (FP16) training by in the deepspeed_config JSON. "fp16": { "enabled": true, "loss_scale": 0, "loss_scale_window": 1000, "hysteresis": 2, "consecutive_hysteresis": false, "min_loss_scale": 1 } Single-GPU, Multi-GPU, and Multi-Node Training Easily switch between single-GPU, single-node multi-GPU, or multi-node multi-GPU execution by specifying resources with a hostfile. deepspeed --hostfile=<hostfile> \ <client_entry.py> <client args> \ --deepspeed --deepspeed_config ds_config.json The script <client_entry.py> will execute on the resources specified in <hostfile>. Pipeline Parallelism DeepSpeed provides pipeline parallelism for memory- and communication- efficient training. DeepSpeed supports a hybrid combination of data, model, and pipeline parallelism and has scaled to over one trillion parameters using 3D parallelism. Pipeline parallelism can also improve communication efficiency and has accelerated training by up to 7x on low-bandwidth clusters. Model Parallelism Support for Custom Model Parallelism DeepSpeed supports all forms of model parallelism including tensor slicing based approaches such as the Megatron-LM. It does so by only requiring the model parallelism framework to provide a model parallelism unit (mpu) that implements a few bookkeeping functionalities: mpu.get_model_parallel_rank() mpu.get_model_parallel_group() mpu.get_model_parallel_world_size() mpu.get_data_parallel_rank() mpu.get_data_parallel_group() mpu.get_data_parallel_world_size() Integration with Megatron-LM DeepSpeed is fully compatible with Megatron. Please see the Megatron-LM tutorial for details. The Zero Redundancy Optimizer The Zero Redundancy Optimizer (ZeRO) is at the heart of DeepSpeed and enables large model training at a scale that is simply not possible with model parallelism alone. When enabled, ZeRO allows training models with over 13 billion parameters without any model parallelism, and up to 200 billion parameter models with model parallelism on current generation hardware. For more details see the ZeRO paper, GPT tutorial on integration with DeepSpeed. Optimizer State and Gradient Partitioning Optimizer State and Gradient Partitioning in ZeRO reduces the memory consumption of the model states (optimizer states, gradients and parameters) by 8x compared to standard data parallelism by partitioning these states across data parallel process instead of replicating them. Activation Partitioning Activation Partitioning is a memory optimization in ZeRO that can reduce the memory consumed by activations during model parallel training (MP). In MP certain activations maybe required by all MP processes, resulting in a replication of activations across MP GPUs. Activation Partitioning stores these activations in a partitioned state once they are used for computation in the forward propagation. These activations are allgathered right before they are needed again during the backward propagation. By storing activations in a partitioned state, ZeRO in DeepSpeed can reduce the activation memory footprint proportional to the MP degree. Constant Buffer Optimization (CBO) CBO enables high network and memory throughput while restricting memory usage to a constant size. For memory- and network-bound operations such as normalization or allreduce collectives, the performance depends on the size of the operand. Simply fusing all operands into a single large operand can enable great throughput at the expense of unnecessary memory overhead. CBO in DeepSpeed fuses smaller operands into approximately a pre-defined sized buffer large enough to achieve great performance without the unnecessary memory overhead. Contiguous Memory Optimization (CMO) CMO reduces memory fragmentation during training, preventing out of memory errors due to lack of contiguous memory. Memory fragmentation is a result of interleaving between short lived and long lived memory objects. During the forward propagation activation checkpoints are long lived but the activations that recomputed are short lived. Similarly, during the backward computation, the activation gradients are short lived while the parameter gradients are long lived. CMO transfers activation checkpoints and parameter gradients to contiguous buffers preventing memory fragmentation. ZeRO-Offload ZeRO-Offload pushes the boundary of the maximum model size that can be trained efficiently using minimal GPU resources, by exploiting computational and memory resources on both GPUs and their host CPUs. It allows training up to 13-billion-parameter models on a single NVIDIA V100 GPU, 10x larger than the state-of-the-art, while retaining high training throughput of over 30 teraflops per GPU. For more details see the ZeRO-Offload release blog, and tutorial on integration with DeepSpeed. Additional Memory and Bandwidth Optimizations Smart Gradient Accumulation Gradient accumulation allows running larger batch size with limited memory by breaking an effective batch into several sequential micro-batches, and averaging the parameter gradients across these micro-batches. Furthermore, instead of averaging the gradients of each micro-batch across all GPUs, the gradients are averaged locally during each step of the sequence, and a single allreduce is done at the end of the sequence to produce the averaged gradients for the effective batch across all GPUs. This strategy significantly reduces the communication involved over the approach of averaging globally for each micro-batch, specially when the number of micro-batches per effective batch is large. Communication Overlapping During back propagation, DeepSpeed can overlap the communication required for averaging parameter gradients that have already been computed with the ongoing gradient computation. This computation-communication overlap allows DeepSpeed to achieve higher throughput even at modest batch sizes. Training Features Simplified training API The DeepSpeed core API consists of just a handful of methods: initialization: initialize training: backward and step argument parsing: add_config_arguments checkpointing : load_checkpoint and store_checkpoint DeepSpeed supports most of the features described in this document, via the use of these API, along with a deepspeed_config JSON file for enabling and disabling the features. Please see the core API doc for more details. Activation Checkpointing API DeepSpeed’s Activation Checkpointing API supports activation checkpoint partitioning, cpu checkpointing, and contiguous memory optimizations, while also allowing layerwise profiling. Please see the core API doc for more details. Gradient Clipping { "gradient_clipping": 1.0 } DeepSpeed handles gradient clipping under the hood based on the max gradient norm specified by the user. Please see the core API doc for more details. Automatic loss scaling with mixed precision DeepSpeed internally handles loss scaling for mixed precision training. The parameters for loss scaling can be specified in the deepspeed_config JSON file. Please see the core API doc for more details. Training Optimizers 1-bit Adam, 0/1 Adam and 1-bit LAMB optimizers with up to 26x less communication DeepSpeed has three communication-efficient optimizers called 1-bit Adam, 0/1 Adam and 1-bit LAMB. They offer the same convergence as Adam/LAMB, incur up to 26x less communication that enables up to 6.6x higher throughput for BERT-Large pretraining and up to 2.7x higher throughput for SQuAD fine-tuning on bandwidth-limited clusters. For more details on usage and performance, please refer to the 1-bit Adam tutorial, 1-bit Adam blog post, 0/1 Adam tutorial and 1-bit LAMB tutorial. For technical details, please refer to the 1-bit Adam paper, 0/1 Adam paper and 1-bit LAMB paper. Fused Adam optimizer and arbitrary torch.optim.Optimizer With DeepSpeed, the user can choose to use a high performance implementation of ADAM from NVIDIA, or any training optimizer that extends torch’s torch.optim.Optimizer class. CPU-Adam: High-Performance vectorized implementation of Adam We introduce an efficient implementation of Adam optimizer on CPU that improves the parameter-update performance by nearly an order of magnitude. We use the AVX SIMD instructions on Intel-x86 architecture for the CPU-Adam implementation. We support both AVX-512 and AVX-2 instruction sets. DeepSpeed uses AVX-2 by default which can be switched to AVX-512 by setting the build flag, DS_BUILD_AVX512 to 1 when installing DeepSpeed. Using AVX-512, we observe 5.1x to 6.5x speedups considering the model-size between 1 to 10 billion parameters with respect to torch-adam. Memory bandwidth optimized FP16 Optimizer Mixed precision training is handled by the DeepSpeed FP16 Optimizer. This optimizer not only handles FP16 training but is also highly efficient. The performance of weight update is primarily dominated by the memory bandwidth, and the achieved memory bandwidth is dependent on the size of the input operands. The FP16 Optimizer is designed to maximize the achievable memory bandwidth by merging all the parameters of the model into a single large buffer, and applying the weight updates in a single kernel, allowing it to achieve high memory bandwidth. Large Batch Training with LAMB Optimizer DeepSpeed makes it easy to train with large batch sizes by enabling the LAMB Optimizer. For more details on LAMB, see the LAMB paper. Memory-Efficient Training with ZeRO Optimizer DeepSpeed can train models with up to 13 billion parameters without model parallelism, and models with up to 200 billion parameters with 16-way model parallelism. This leap in model size is possible through the memory efficiency achieved via the ZeRO Optimizer. For more details see ZeRO paper . Training Agnostic Checkpointing DeepSpeed can simplify checkpointing for you regardless of whether you are using data parallel training, model parallel training, mixed-precision training, a mix of these three, or using the zero optimizer to enable larger model sizes. Please see the Getting Started guide and the core API doc for more details. Advanced parameter search DeepSpeed supports multiple Learning Rate Schedules to enable faster convergence for large batch scaling. Learning Rate Range Test Please refer to the Learning Rate Range Test tutorial. 1Cycle Learning Rate Schedule Please refer to the 1Cycle Learning Rate Schedule tutorial. Simplified Data Loader DeepSpeed abstracts away data parallelism and model parallelism from the user when it comes to data loading. Users simply provide a PyTorch dataset, and DeepSpeed data loader can automatically handle batch creation appropriately. Data Efficiency Please refer to the Data Efficiency tutorial. Curriculum Learning Please refer to the Curriculum Learning tutorial. Note that the Data Efficiency Library above provides more general curriculum learning support. This legacy curriculum learning feature is still supported but we recommend to use the Data Efficiency Library. Performance Analysis and Debugging DeepSpeed provides a set of tools for performance analysis and debugging. Wall Clock Breakdown DeepSpeed provides a detailed breakdown of the time spent in different parts of the training. This can be enabled by setting the following in the deepspeed_config file. { "wall_clock_breakdown": true, } Timing Activation Checkpoint Functions When activation checkpointing is enabled, profiling the forward and backward time of each checkpoint function can be enabled in the deepspeed_config file. { "activation_checkpointing": { "profile": true } } Flops Profiler The DeepSpeed flops profiler measures the time, flops and parameters of a PyTorch model and shows which modules or layers are the bottleneck. When used with the DeepSpeed runtime, the flops profiler can be configured in the deepspeed_config file as follows: { "flops_profiler": { "enabled": true, "profile_step": 1, "module_depth": -1, "top_modules": 3, "detailed": true, } } The flops profiler can also be used as a standalone package. Please refer to the Flops Profiler tutorial for more details. Autotuning The DeepSpeed Autotuner uses model information, system information, and heuristics to efficiently tune Zero stage, micro batch size, and other Zero configurations. Using the autotuning feature requires no code change from DeepSpeed users. While "autotuning": {"enabled": true} is the minimal required to enable autotuning, there are other parameters users can define to configure the autotuning process. Below shows major parameters and their default values in the autotuning configuration. Please refer to the Autotuning tutorial for more details. { "autotuning": { "enabled": true, "results_dir": null, "exps_dir": null, "overwrite": false, "metric": "throughput", "num_nodes": null, "num_gpus": null, "start_profile_step": 3, "end_profile_step": 5, "fast": true, "num_tuning_micro_batch_sizes": 3, "tuner_type": "model_based", "tuner_early_stopping": 5, "tuner_num_trials": 50, "arg_mappings": null } } The flops profiler can also be used as a standalone package. Please refer to the Flops Profiler tutorial for more details. Monitor The DeepSpeed Monitor logs live training metrics to one or more monitoring backends, including PyTorch’s TensorBoard, WandB, or simply to CSV files. The Monitor can be configured with one or more backends in the deepspeed_config file as follows: { "tensorboard": { "enabled": true, "output_path": "output/ds_logs/", "job_name": "train_bert" } "wandb": { "enabled": true, "team": "my_team", "group": "my_group", "project": "my_project" } "csv_monitor": { "enabled": true, "output_path": "output/ds_logs/", "job_name": "train_bert" } } The Monitor can also be added to log custom metrics and client codes. Please refer to the Monitor tutorial for more details. Communication Logging DeepSpeed provides logging of all communication operations launched within deepspeed.comm. The communication logger can be configured in the deepspeed_config file as follows: { "comms_logger": { "enabled": true, "verbose": false, "prof_all": true, "debug": false } } Client codes can then print a summary with a call to deepspeed.comm.log_summary(). For more details and example usage, see the Communication Logging tutorial. Sparse Attention DeepSpeed offers sparse attention to support long sequences. Please refer to the Sparse Attention tutorial. --deepspeed_sparse_attention "sparse_attention": { "mode": "fixed", "block": 16, "different_layout_per_head": true, "num_local_blocks": 4, "num_global_blocks": 1, "attention": "bidirectional", "horizontal_global_attention": false, "num_different_global_patterns": 4 } Mixture of Experts (MoE) To learn more about training Mixture of Experts (MoE) models with DeepSpeed, see our tutorial for more details.

```
torch.optim.Optimizer
```

**Pattern 6:** Flops Profiler Contents Overview Flops Measurement Multi-GPU, Multi-node, Data Parallelism, and Model Parallelism Usage Usage With the DeepSpeed Runtime Example: Megatron-LM Usage Outside the DeepSpeed Runtime In Model Inference Example: AlexNet Example: Bert In Model Training Workflow Example Training Workflow In this tutorial, we introduce the DeepSpeed Flops Profiler and provide examples of its usage. Overview Flops Measurement Multi-GPU, Multi-node, Data Parallelism, and Model Parallelism Usage Overview Effective use of hardware resources is critical to good performance, but performance inefficiency in existing implementations for large-scale model training and inference are often hard to spot and attribute to specific module components. DeepSpeed Flops Profiler helps users easily measure both the model training/inference speed (latency, throughput) and efficiency (floating-point operations per second, i.e., FLOPS) of a model and its submodules, with an eye towards eliminating inefficiencies in existing implementations. Below is an example output for BERT-Large(NVIDIA) on an A100 GPU with batch size 80: -------------------------- DeepSpeed Flops Profiler -------------------------- Profile Summary at step 10: Notations: data parallel size (dp_size), model parallel size(mp_size), number of parameters (params), number of multiply-accumulate operations(MACs), number of floating-point operations (flops), floating-point operations per second (FLOPS), fwd latency (forward propagation latency), bwd latency (backward propagation latency), step (weights update latency), iter latency (sum of fwd, bwd and step latency) world size: 1 data parallel size: 1 model parallel size: 1 batch size per GPU: 80 params per gpu: 336.23 M params of model = params per GPU * mp_size: 336.23 M fwd MACs per GPU: 3139.93 G fwd flops per GPU: 6279.86 G fwd flops of model = fwd flops per GPU * mp_size: 6279.86 G fwd latency: 76.67 ms bwd latency: 108.02 ms fwd FLOPS per GPU = fwd flops per GPU / fwd latency: 81.9 TFLOPS bwd FLOPS per GPU = 2 * fwd flops per GPU / bwd latency: 116.27 TFLOPS fwd+bwd FLOPS per GPU = 3 * fwd flops per GPU / (fwd+bwd latency): 102.0 TFLOPS step latency: 34.09 us iter latency: 184.73 ms samples/second: 433.07 ----------------------------- Aggregated Profile per GPU ----------------------------- Top modules in terms of params, MACs or fwd latency at different model depths: depth 0: params - {'BertForPreTrainingPreLN': '336.23 M'} MACs - {'BertForPreTrainingPreLN': '3139.93 GMACs'} fwd latency - {'BertForPreTrainingPreLN': '76.39 ms'} depth 1: params - {'BertModel': '335.15 M', 'BertPreTrainingHeads': '32.34 M'} MACs - {'BertModel': '3092.96 GMACs', 'BertPreTrainingHeads': '46.97 GMACs'} fwd latency - {'BertModel': '34.29 ms', 'BertPreTrainingHeads': '3.23 ms'} depth 2: params - {'BertEncoder': '302.31 M', 'BertLMPredictionHead': '32.34 M'} MACs - {'BertEncoder': '3092.88 GMACs', 'BertLMPredictionHead': '46.97 GMACs'} fwd latency - {'BertEncoder': '33.45 ms', 'BertLMPredictionHead': '2.61 ms'} depth 3: params - {'ModuleList': '302.31 M', 'Embedding': '31.79 M', 'Linear': '31.26 M'} MACs - {'ModuleList': '3092.88 GMACs', 'Linear': '36.23 GMACs'} fwd latency - {'ModuleList': '33.11 ms', 'BertPredictionHeadTransform': '1.83 ms''} depth 4: params - {'BertLayer': '302.31 M', 'LinearActivation': '1.05 M''} MACs - {'BertLayer': '3092.88 GMACs', 'LinearActivation': '10.74 GMACs'} fwd latency - {'BertLayer': '33.11 ms', 'LinearActivation': '1.43 ms'} depth 5: params - {'BertAttention': '100.76 M', 'BertIntermediate': '100.76 M'} MACs - {'BertAttention': '1031.3 GMACs', 'BertIntermediate': '1030.79 GMACs'} fwd latency - {'BertAttention': '19.83 ms', 'BertOutput': '4.38 ms'} depth 6: params - {'LinearActivation': '100.76 M', 'Linear': '100.69 M'} MACs - {'LinearActivation': '1030.79 GMACs', 'Linear': '1030.79 GMACs'} fwd latency - {'BertSelfAttention': '16.29 ms', 'LinearActivation': '3.48 ms'} ------------------------------ Detailed Profile per GPU ------------------------------ Each module profile is listed after its name in the following order: params, percentage of total params, MACs, percentage of total MACs, fwd latency, percentage of total fwd latency, fwd FLOPS BertForPreTrainingPreLN( 336.23 M, 100.00% Params, 3139.93 GMACs, 100.00% MACs, 76.39 ms, 100.00% latency, 82.21 TFLOPS, (bert): BertModel( 335.15 M, 99.68% Params, 3092.96 GMACs, 98.50% MACs, 34.29 ms, 44.89% latency, 180.4 TFLOPS, (embeddings): BertEmbeddings(...) (encoder): BertEncoder( 302.31 M, 89.91% Params, 3092.88 GMACs, 98.50% MACs, 33.45 ms, 43.79% latency, 184.93 TFLOPS, (FinalLayerNorm): FusedLayerNorm(...) (layer): ModuleList( 302.31 M, 89.91% Params, 3092.88 GMACs, 98.50% MACs, 33.11 ms, 43.35% latency, 186.8 TFLOPS, (0): BertLayer( 12.6 M, 3.75% Params, 128.87 GMACs, 4.10% MACs, 1.29 ms, 1.69% latency, 199.49 TFLOPS, (attention): BertAttention( 4.2 M, 1.25% Params, 42.97 GMACs, 1.37% MACs, 833.75 us, 1.09% latency, 103.08 TFLOPS, (self): BertSelfAttention( 3.15 M, 0.94% Params, 32.23 GMACs, 1.03% MACs, 699.04 us, 0.92% latency, 92.22 TFLOPS, (query): Linear(1.05 M, 0.31% Params, 10.74 GMACs, 0.34% MACs, 182.39 us, 0.24% latency, 117.74 TFLOPS,...) (key): Linear(1.05 M, 0.31% Params, 10.74 GMACs, 0.34% MACs, 57.22 us, 0.07% latency, 375.3 TFLOPS,...) (value): Linear(1.05 M, 0.31% Params, 10.74 GMACs, 0.34% MACs, 53.17 us, 0.07% latency, 403.91 TFLOPS,...) (dropout): Dropout(...) (softmax): Softmax(...) ) (output): BertSelfOutput( 1.05 M, 0.31% Params, 10.74 GMACs, 0.34% MACs, 114.68 us, 0.15% latency, 187.26 TFLOPS, (dense): Linear(1.05 M, 0.31% Params, 10.74 GMACs, 0.34% MACs, 64.13 us, 0.08% latency, 334.84 TFLOPS, ...) (dropout): Dropout(...) ) ) (PreAttentionLayerNorm): FusedLayerNorm(...) (PostAttentionLayerNorm): FusedLayerNorm(...) (intermediate): BertIntermediate( 4.2 M, 1.25% Params, 42.95 GMACs, 1.37% MACs, 186.68 us, 0.24% latency, 460.14 TFLOPS, (dense_act): LinearActivation(4.2 M, 1.25% Params, 42.95 GMACs, 1.37% MACs, 175.0 us, 0.23% latency, 490.86 TFLOPS,...) ) (output): BertOutput( 4.2 M, 1.25% Params, 42.95 GMACs, 1.37% MACs, 116.83 us, 0.15% latency, 735.28 TFLOPS, (dense): Linear(4.2 M, 1.25% Params, 42.95 GMACs, 1.37% MACs, 65.57 us, 0.09% latency, 1310.14 TFLOPS,...) (dropout): Dropout(...) ) ) ... (23): BertLayer(...) ) ) (pooler): BertPooler(...) ) (cls): BertPreTrainingHeads(...) ) ------------------------------------------------------------------------------ In the summary profile, the DeepSpeed Flops Profiler outputs the number of parameters, floating-point operations (flops), FLOPS, latency, and throughput in samples/second of the model. This profile shows how much performance gap (compared to the peak hardware performance) the current model execution has and helps users tune the training or inference setup (e.g., hyperparameters, data parallelism, model parallelism, system configurations, etc.) for better performance. The DeepSpeed Flops Profiler also measures significant modules at different model depths (aggregated profile) and module-specific profile in the model architecture (detailed profile). Using these profiles, DeepSpeed users can understand how each layer or submodule contributes to the overall model complexity/performance. Then users can adjust or refactor the model design to improve performance. For example, using the profiler, DeepSpeed users can quantitatively tell if stacking smaller layers is lighter or more performant than having bigger ones. The aggregated and detailed profiles also allow users to quickly identify bottleneck modules. In the BERT-Large example above, using the DeepSpeed Flops Profiler, we find that BertLayer is the most significant layer and contains quite a few dropout, softmax, and layer norm along with linear modules. These modules are not heavy in flops and would trigger many GPU kernel invocations and create excessive read/write requests to memory. The pattern shown in the detailed profile suggests this is a perfect match for kernel fusion, and we developed fused transformer-kernels to reduce data movement (see DeepSpeedBert). After applying our optimizations, we see a 25% improvement in FLOPS per GPU and overall training samples/second in the DeepSpeed Flops Profiler output. The DeepSpeed Flops Profiler can be used with the DeepSpeed runtime without any user code change or be used independently from DeepSpeed as a standalone package. When using DeepSpeed for model training, the profiler can be enabled in the DeepSpeed configuration file. As a standalone package, the profiler API can be used in both training and inference code. The DeepSpeed profiler is still under active development and includes just initial features. Stay connected for more exciting features to be added soon. Flops Measurement Similar to existing flops calculation tools or methods, the DeepSpeed Flops Profiler measures the flops of the forward pass of a module and the flops of the backward pass is estimated as 2 times of that of the forward pass. Different from the PyTorch profiler which calculates the flops of PyTorch operators, the DeepSpeed Flops Profiler measures the flops within modules in a model and provides more insights to the users about the model execution. The flops estimation is partly inspired by ptflops with the major difference being that the DeepSpeed Flops Profiler not only supports flops computation directly at module level, but can also capture torch.nn.functional invoked in a module to estimate the flops. Thus the DeepSpeed Flops Profiler allows for customized modules in the model, e.g., ParallelTransformerLayerworks, ParallelSelfAttention, RowParallelLinear, etc. in Megatron-LM. This is in contrast to ptflops which requires users to write customized flops calculation functions for each customized module. Multi-GPU, Multi-node, Data Parallelism, and Model Parallelism The DeepSpeed Flops Profiler outputs the per GPU profile as well as the world size, data parallel size, and model parallel size. For models running on multi-GPU or multi-node, only change of the model parallelism (e.g., --model-parallel-size in Megatron-LM) affects the number of flops and parameters profiled, i.e., model_parallel_size * flops = total_flops and model_parallel_size * parameters = total_parameters. The data parallel size or world size (related to the number of GPUs or nodes) does not affect the per GPU profile. Usage The DeepSpeed Flops Profiler can be used with the DeepSpeed runtime or as a standalone package. When using DeepSpeed for model training, the profiler can be configured in the deepspeed configuration file without user code changes. To use the flops profiler outside the DeepSpeed runtime, install DeepSpeed and import the flops_profiler package to use the APIs directly. Examples of each usage are given below. Usage With the DeepSpeed Runtime Example: Megatron-LM Usage Outside the DeepSpeed Runtime In Model Inference Example: AlexNet Example: Bert In Model Training Workflow Example Training Workflow Usage With the DeepSpeed Runtime When using DeepSpeed for model training, the profiler can be configured in the deepspeed configuration file. No explicit API calls are needed to use the profiler. The profiler can be enabled by adding the following field to deepspeed’s configuration json file. Refer to flops profiler for details. { "flops_profiler": { "enabled": true, "profile_step": 1, "module_depth": -1, "top_modules": 1, "detailed": true, "output_file": null } } Example: Megatron-LM For information on running Megatron-LM with DeepSpeed, please refer to our tutorial Megatron-LM. An example output of 12-layer Megatron-LM model (hidden_size = 8192, num_attention_heads = 32, batch_size = 1024, seq_length = 1024) is shown below. -------------------------- DeepSpeed Flops Profiler -------------------------- Profile Summary at step 10: Notations: data parallel size (dp_size), model parallel size(mp_size), number of parameters (params), number of multiply-accumulate operations(MACs), number of floating-point operations (flops), floating-point operations per second (FLOPS), fwd latency (forward propagation latency), bwd latency (backward propagation latency), step (weights update latency), iter latency (sum of fwd, bwd and step latency) world size: 1 data parallel size: 1 model parallel size: 1 batch size per GPU: 1024 params per gpu: 1.29 M params of model = params per GPU * mp_size: 1.29 M fwd MACs per GPU: 41271.95 G fwd flops per GPU: 82543.9 G fwd flops of model = fwd flops per GPU * mp_size: 82543.9 G fwd latency: 1.89 s bwd latency: 5.38 s fwd FLOPS per GPU = fwd flops per GPU / fwd latency: 43.68 TFLOPS bwd FLOPS per GPU = 2 * fwd flops per GPU / bwd latency: 30.7 TFLOPS fwd+bwd FLOPS per GPU = 3 * fwd flops per GPU / (fwd+bwd latency): 34.07 TFLOPS step latency: 34.12 s iter latency: 41.39 s samples/second: 24.74 ----------------------------- Aggregated Profile per GPU ----------------------------- Top 1 modules in terms of params, MACs or fwd latency at different model depths: depth 0: params - {'GPT2Model': '1.29 M'} MACs - {'GPT2Model': '41271.95 GMACs'} fwd latency - {'GPT2Model': '1.84 s'} depth 1: params - {'TransformerLanguageModel': '1.29 M'} MACs - {'TransformerLanguageModel': '39584.03 GMACs'} fwd latency - {'TransformerLanguageModel': '1.83 s'} depth 2: params - {'ParallelTransformer': '1.29 M'} MACs - {'ParallelTransformer': '39584.03 GMACs'} fwd latency - {'ParallelTransformer': '1.81 s'} depth 3: params - {'ModuleList': '1.28 M'} MACs - {'ModuleList': '39584.03 GMACs'} fwd latency - {'ModuleList': '1.3 s'} depth 4: params - {'ParallelTransformerLayerPart2': '688.15 k'} MACs - {'ParallelTransformerLayerPart2': '26388.28 GMACs'} fwd latency - {'ParallelTransformerLayerPart2': '865.73 ms'} depth 5: params - {'ParallelMLP': '491.54 k'} MACs - {'ParallelMLP': '26388.28 GMACs'} fwd latency - {'ParallelMLP': '849.4 ms'} ------------------------------ Detailed Profile per GPU ------------------------------ Each module profile is listed after its name in the following order: params, percentage of total params, MACs, percentage of total MACs, fwd latency, percentage of total fwd latency, fwd FLOPS Note: 1. A module can have torch.nn.module or torch.nn.functional to compute logits (e.g. CrossEntropyLoss). They are not counted as submodules, thus not to be printed out. However they make up the difference between a parent's MACs(or latency) and the sum of its submodules'. 1. Number of floating-point operations is a theoretical estimation, thus FLOPS computed using that could be larger than the maximum system throughput. 2. The fwd latency listed in the top module's profile is directly captured at the module forward function in PyTorch, thus it's less than the fwd latency shown above which is captured in DeepSpeed. GPT2Model( 1.29 M, 100.00% Params, 41271.95 GMACs, 100.00% MACs, 1.84 s, 100.00% latency, 44.78 TFLOPS, (language_model): TransformerLanguageModel( 1.29 M, 100.00% Params, 39584.03 GMACs, 95.91% MACs, 1.83 s, 99.11% latency, 43.34 TFLOPS, (embedding): Embedding( 2, 0.00% Params, 0 MACs, 0.00% MACs, 18.1 ms, 0.98% latency, 0.0 FLOPS, (word_embeddings): VocabParallelEmbedding(1, 0.00% Params, 0 MACs, 0.00% MACs, 164.75 us, 0.01% latency, 0.0 FLOPS, ) (position_embeddings): Embedding(1, 0.00% Params, 0 MACs, 0.00% MACs, 489.23 us, 0.03% latency, 0.0 FLOPS, 1024, 8192) (embedding_dropout): Dropout(0, 0.00% Params, 0 MACs, 0.00% MACs, 93.94 us, 0.01% latency, 0.0 FLOPS, p=0.1, inplace=False) ) (transformer): ParallelTransformer( 1.29 M, 100.00% Params, 39584.03 GMACs, 95.91% MACs, 1.81 s, 98.11% latency, 43.78 TFLOPS, (layers): ModuleList( 1.28 M, 98.73% Params, 39584.03 GMACs, 95.91% MACs, 1.3 s, 70.66% latency, 60.79 TFLOPS, (0): ParallelTransformerLayerPart1( 49.15 k, 3.80% Params, 1099.65 GMACs, 2.66% MACs, 23.5 ms, 1.27% latency, 93.6 TFLOPS, (input_layernorm): FusedLayerNorm(16.38 k, 1.27% Params, 0 MACs, 0.00% MACs, 128.75 us, 0.01% latency, 0.0 FLOPS, torch.Size([8192]), eps=1e-05, elementwise_affine=True) (attention): ParallelSelfAttention( 32.77 k, 2.53% Params, 1099.65 GMACs, 2.66% MACs, 22.8 ms, 1.24% latency, 96.46 TFLOPS, (query_key_value): ColumnParallelLinear(24.58 k, 1.90% Params, 824.63 GMACs, 2.00% MACs, 8.93 ms, 0.48% latency, 184.7 TFLOPS, ) (scale_mask_softmax): FusedScaleMaskSoftmax(0, 0.00% Params, 134.22 MMACs, 0.00% MACs, 151.16 us, 0.01% latency, 1.78 TFLOPS, ) (attention_dropout): Dropout(0, 0.00% Params, 0 MACs, 0.00% MACs, 79.63 us, 0.00% latency, 0.0 FLOPS, p=0.1, inplace=False) (dense): RowParallelLinear(8.19 k, 0.63% Params, 274.88 GMACs, 0.67% MACs, 2.67 ms, 0.14% latency, 205.81 TFLOPS, ) ) ) (1): ParallelTransformerLayerPart2( 57.35 k, 4.43% Params, 2199.02 GMACs, 5.33% MACs, 77.53 ms, 4.21% latency, 56.73 TFLOPS, (post_attention_layernorm): FusedLayerNorm(16.38 k, 1.27% Params, 0 MACs, 0.00% MACs, 116.11 us, 0.01% latency, 0.0 FLOPS, torch.Size([8192]), eps=1e-05, elementwise_affine=True) (mlp): ParallelMLP( 40.96 k, 3.16% Params, 2199.02 GMACs, 5.33% MACs, 76.19 ms, 4.13% latency, 57.72 TFLOPS, (dense_h_to_4h): ColumnParallelLinear(32.77 k, 2.53% Params, 1099.51 GMACs, 2.66% MACs, 10.79 ms, 0.59% latency, 203.81 TFLOPS, ) (dense_4h_to_h): RowParallelLinear(8.19 k, 0.63% Params, 1099.51 GMACs, 2.66% MACs, 14.38 ms, 0.78% latency, 152.95 TFLOPS, ) ) ) ... (23): ParallelTransformerLayerPart2(...) ) (final_layernorm): FusedLayerNorm(16.38 k, 1.27% Params, 0 MACs, 0.00% MACs, 110.86 us, 0.01% latency, 0.0 FLOPS, torch.Size([8192]), eps=1e-05, elementwise_affine=True) ) ) ) ------------------------------------------------------------------------------ Usage Outside the DeepSpeed Runtime The profiler can be used as a standalone package outside of the DeepSpeed runtime. One can simply install DeepSpeed and import the flops_profiler package to use the APIs directly. Refer to installation of DeepSpeed for installing DeepSpeed. In Model Inference To profile a trained model in inference, use the get_model_profile function. Examples are given below. Example: AlexNet The following example shows how to profile AlexNet using the DeepSpeed flops profiler. import torchvision.models as models import torch from deepspeed.profiling.flops_profiler import get_model_profile from deepspeed.accelerator import get_accelerator with get_accelerator().device(0): model = models.alexnet() batch_size = 256 flops, macs, params = get_model_profile(model=model, # model input_shape=(batch_size, 3, 224, 224), # input shape to the model. If specified, the model takes a tensor with this shape as the only positional argument. args=None, # list of positional arguments to the model. kwargs=None, # dictionary of keyword arguments to the model. print_profile=True, # prints the model graph with the measured profile attached to each module detailed=True, # print the detailed profile module_depth=-1, # depth into the nested modules, with -1 being the inner most modules top_modules=1, # the number of top modules to print aggregated profile warm_up=10, # the number of warm-ups before measuring the time of each module as_string=True, # print raw numbers (e.g. 1000) or as human-readable strings (e.g. 1k) output_file=None, # path to the output file. If None, the profiler prints to stdout. ignore_modules=None) # the list of modules to ignore in the profiling Example: Bert from functools import partial import torch from transformers import BertForSequenceClassification, BertTokenizer from deepspeed.profiling.flops_profiler import get_model_profile from deepspeed.accelerator import get_accelerator def bert_input_constructor(batch_size, seq_len, tokenizer): fake_seq = "" for _ in range(seq_len - 2): # ignore the two special tokens [CLS] and [SEP] fake_seq += tokenizer.pad_token inputs = tokenizer([fake_seq] * batch_size, padding=True, truncation=True, return_tensors="pt") labels = torch.tensor([1] * batch_size) inputs = dict(inputs) inputs.update({"labels": labels}) return inputs with get_accelerator().device(0): tokenizer = BertTokenizer.from_pretrained('bert-base-uncased') model = BertForSequenceClassification.from_pretrained('bert-base-uncased') batch_size = 4 seq_len = 128 enable_profile = True if enable_profile: flops, macs, params = get_model_profile( model, kwargs=bert_input_constructor(batch_size, seq_len, tokenizer), print_profile=True, detailed=True, ) else: inputs = bert_input_constructor((batch_size, seq_len), tokenizer) outputs = model(inputs) In Model Training Workflow To profile model forward in a training workflow, use the FlopsProfilerclass. The FlopsProfilerclass provides the following methods: start_profile() - starts profiling get_total_flops(as_string=False) - returns the total number of floating-point operations in the model get_total_macs(as_string=False) - returns the total number of MACs in the model get_total_params(as_string=False) - returns the total number of parameters in the model print_model_profile(profile_step=1, module_depth=-1, top_modules=3, detailed=True, output_file=None) - prints the model profile stop_profile() - stops profiling. This stops the flops counting in the model. end_profile() - cleans up. This cleans up the profile attributes added to the model during the profiling. This should be invoked at the end of the profiling and AFTER get_total_flops, get_total_params or print_model_profile. Example Training Workflow Below is an example of this usage in a typical training workflow. from deepspeed.profiling.flops_profiler import FlopsProfiler model = Model() prof = FlopsProfiler(model) profile_step = 5 print_profile= True for step, batch in enumerate(data_loader): # start profiling at training step "profile_step" if step == profile_step: prof.start_profile() # forward() method loss = model(batch) # end profiling and print output if step == profile_step: # if using multi nodes, check global_rank == 0 as well prof.stop_profile() flops = prof.get_total_flops() macs = prof.get_total_macs() params = prof.get_total_params() if print_profile: prof.print_model_profile(profile_step=profile_step) prof.end_profile() # runs backpropagation loss.backward() # weight update optimizer.step() Updated: November 5, 2025 Previous Next

```
80
```

**Pattern 7:** DeepSpeed Configuration JSON Contents Batch Size Related Parameters Optimizer Parameters Scheduler Parameters Communication options FP16 training options BFLOAT16 training options Automatic mixed precision (AMP) training options Gradient Clipping ZeRO Optimizations for FP16 Training Parameter offloading Optimizer offloading Asynchronous I/O Logging Autotuning Flops Profiler Activation Checkpointing Sparse Attention Data Efficiency Curriculum Learning Monitoring Module Elastic Training Config (V0.1 and V0.2) Communication Logging Compression Layer Reduction Weight Quantization Activation Quantization Sparse Pruning Row Pruning Head Pruning Channel Pruning Checkpoint options Data Type options Batch Size Related Parameters Note: train_batch_size must be equal to train_micro_batch_size_per_gpu * gradient_accumulation_steps * number of GPUs. For simplicity, you can choose to only specify two of the three parameters, the last one will be inferred automatically by DeepSpeed. train_batch_size: [integer] Value Example The effective training batch size. This is the amount of data samples that leads to one step of model update. train_batch_size is aggregated by the batch size that a single GPU processes in one forward/backward pass (a.k.a., train_micro_batch_size_per_gpu), the gradient accumulation steps (a.k.a., gradient_accumulation_steps), and the number of GPUs. Can be omitted if both train_micro_batch_size_per_gpu and gradient_accumulation_steps are provided. 32 train_micro_batch_size_per_gpu: [integer] Description Default Batch size to be processed by one GPU in one step (without gradient accumulation). Can be omitted if both train_batch_size and gradient_accumulation_steps are provided. train_batch_size value gradient_accumulation_steps: [integer] Description Default Number of training steps to accumulate gradients before averaging and applying them. This feature is sometimes useful to improve scalability since it results in less frequent communication of gradients between steps. Another impact of this feature is the ability to train with larger batch sizes per GPU. Can be omitted if both train_batch_size and train_micro_batch_size_per_gpu are provided. 1 Optimizer Parameters optimizer: [dictionary] Fields Value Example type The optimizer name. DeepSpeed natively supports Adam, AdamW, OneBitAdam, Lamb, and OneBitLamb optimizers (See here for details) and will import other optimizers from torch. "Adam" params Dictionary of parameters to instantiate optimizer. The parameter names must match the optimizer constructor signature (e.g., for Adam). {"lr": 0.001, "eps": 1e-8} Example of optimizer with Adam "optimizer": { "type": "Adam", "params": { "lr": 0.001, "betas": [ 0.8, 0.999 ], "eps": 1e-8, "weight_decay": 3e-7 } } The Adam optimizer also supports the following two params keys/values in addition to the standard parameters from torch.optim.Adam: “params” key Description Default torch_adam Use torch’s implementation of adam instead of our fused adam implementation false adam_w_mode Apply L2 regularization (also known as AdamW) true Another example of optimizer with 1-bit Adam specific parameters is as follows. "optimizer": { "type": "OneBitAdam", "params": { "lr": 0.001, "betas": [ 0.8, 0.999 ], "eps": 1e-8, "weight_decay": 3e-7, "freeze_step": 400, "cuda_aware": false, "comm_backend_name": "nccl" } } The 1-bit Adam optimizer supports the following three params keys/values in addition to the standard Adam (learn more in our tutorial): “params” key Description Default freeze_step Number of warm up steps before 1-bit compression gets applied to the communication 100000 cuda_aware To indicate that the underlying MPI library supports CUDA-Aware communication false comm_backend_name To indicate which backend implementation to use “nccl” A variant optimizer for 1-bit Adam is 0/1 Adam, which further optimizes 1-bit Adam via adaptive variance freezing and 1-bit synchronization over optimizer states. "optimizer": { "type": "ZeroOneAdam", "params": { "lr": 1e-3, "weight_decay": 0.01, "bias_correction": false, "var_freeze_step": 1000, "var_update_scaler": 16, "local_step_scaler": 1000, "local_step_clipper": 16, "cuda_aware": false, "comm_backend_name": "nccl" } } 0/1 Adam supports the following params key/values in addition to standard Adam (learn more in our tutorial.) “params” key Description Default var_freeze_step The latest step to update the variance 100000 var_update_scaler The interval to update the variance 16 local_step_scaler The interval to scale the local steps interval according to the learning rate policy 32678 local_step_clipper The largest interval for local steps with learning rate policy 16 cuda_aware To indicate that the underlying MPI library supports CUDA-Aware communication false comm_backend_name To indicate which backend implementation to use “nccl” Another example of optimizer with 1-bit LAMB "optimizer": { "type": "OneBitLamb", "params": { "lr": 11e-3, "weight_decay": 0.01, "bias_correction": false, "max_coeff": 0.3, "min_coeff": 0.01, "freeze_step": 1000, "cuda_aware": false, "comm_backend_name": "nccl", "coeff_beta": 0.9, "factor_max": 4.0, "factor_min": 0.5, "factor_threshold": 0.1 } } The 1-bit LAMB optimizer supports the following params keys/values in addition to the standard LAMB (learn more in our tutorial): “params” key Description Default max_coeff Scaling coefficient upper bound for original LAMB algorithm and 1-bit LAMB’s warmup stage 10.0 min_coeff Scaling coefficient lower bound for original LAMB algorithm and 1-bit LAMB’s warmup stage 0.01 freeze_step Number of warm up steps before 1-bit compression gets applied to the communication 100000 cuda_aware To indicate that the underlying MPI library supports CUDA-Aware communication false comm_backend_name To indicate which backend implementation to use “nccl” coeff_beta Coefficient used for computing running averages of lamb coefficient 0.9 factor_max Maximum value of scaling factor to the frozen lamb coefficient during compression stage 4.0 factor_min Minimum value of scaling factor to the frozen lamb coefficient during compression stage 0.5 factor_threshold Threshold of how much the scaling factor can fluctuate between steps 0.1 Scheduler Parameters DeepSpeed calls the step() method of the scheduler at every training step when model_engine.step() is executed. scheduler: [dictionary] Fields Value Example type The scheduler name. See here for list of support schedulers. "WarmupLR" params Dictionary of parameters to instantiate scheduler. The parameter names should match scheduler constructor signature. {"warmup_min_lr": 0, "warmup_max_lr": 0.001} Example of scheduler "scheduler": { "type": "WarmupLR", "params": { "warmup_min_lr": 0, "warmup_max_lr": 0.001, "warmup_num_steps": 1000 } } Communication options communication_data_type: [string] Description Default During gradient averaging perform communication with selected data type. By default it will be determined by selected regime None prescale_gradients: [boolean] Description Default Scale gradients before doing allreduce false gradient_predivide_factor: [float] Description Default Before gradient averaging predivide gradients by a specified factor, can sometimes help with fp16 stability when scaling to large numbers of GPUs 1.0 sparse_gradients: [boolean] Description Default Enable sparse compression of torch.nn.Embedding gradients. This feature is essentially deprecated as we don’t see use cases for it as much anymore. It should be noted that this feature is not compatible with torch.sparse related features. false FP16 training options Note: this mode cannot be combined with the amp mode described below. fp16: [dictionary] Description Default Configuration for using mixed precision/FP16 training that leverages NVIDIA’s Apex package. An example, including the available dictionary keys is illustrated below. NOTE: this does not use Apex’s AMP mode that allows for more flexibility in mixed precision training modes, this mode is similar to AMP’s O2 mode. Please see AMP support below if you want to use more complex mixed precision modes. If you want to use ZeRO (currently) you must use this mode. None "fp16": { "enabled": true, "auto_cast": false, "loss_scale": 0, "initial_scale_power": 16, "loss_scale_window": 1000, "hysteresis": 2, "consecutive_hysteresis": false, "min_loss_scale": 1 } fp16:enabled: [boolean] Description Default enabled is a fp16 parameter indicating whether or not FP16 training enabled. false fp16:auto_cast: [boolean] Description Default auto_cast automatically casts inputs to fp16 false fp16:loss_scale: [float] Description Default loss_scale is a fp16 parameter representing the loss scaling value for FP16 training. The default value of 0.0 results in dynamic loss scaling, otherwise the value will be used for static fixed loss scaling. 0.0 fp16:initial_scale_power: [integer] Description Default initial_scale_power is a fp16 parameter representing the power of the initial dynamic loss scale value. The actual loss scale is computed as 2initial_scale_power. 16 fp16:loss_scale_window: [integer] Description Default loss_scale_window is a fp16 parameter representing the window over which to raise/lower the dynamic loss scale value. 1000 fp16:hysteresis: [integer] Description Default hysteresis is a fp16 parameter representing the delay shift in dynamic loss scaling. 2 fp16:consecutive_hysteresis: [boolean] Description Default consecutive_hysteresis is a fp16 parameter representing whether to refill the hysteresis if we reach an iteration that doesn’t overflow false fp16:min_loss_scale: [integer] Description Default min_loss_scale is a fp16 parameter representing the minimum dynamic loss scale value. 1 BFLOAT16 training options Note: this mode cannot be combined with the amp mode described below. Note: this mode cannot be combined with the fp16 mode described above. bf16: [dictionary] Description Default Configuration for using bfloat16 floating-point format as an alternative to FP16. BFLOAT16 requires hardware support (e.g., NVIDIA A100). An example, including the available dictionary keys is illustrated below. Training with bfloat16 does not require loss scaling. None "bf16": { "enabled": true } bf16:enabled: [boolean] Description Default enabled indicates whether BFLOAT16 training is enabled. false Automatic mixed precision (AMP) training options Note: this mode cannot be combined with the fp16 mode described above. In addition this mode is not currently compatible with ZeRO. amp: [dictionary] Description Default Configuration for using automatic mixed precision (AMP) training that leverages NVIDIA’s Apex AMP package. An example, including the available dictionary keys is illustrated below. Is not compatible with fp16 mode above or ZeRO. Any parameters outside of “enabled” will be passed to AMP’s initialize call, see the API and descriptions here at the apex.amp.initialize documentation. None "amp": { "enabled": true, ... "opt_level": "O1", ... } amp:enabled: [boolean] Description Default enabled is an amp parameter indicating whether or not AMP training is enabled. false amp params: [various] Description Default Any parameters outside of “enabled” will be passed to AMP’s initialize call, see the API and descriptions here at the apex.amp.initialize documentation. None Gradient Clipping gradient_clipping: [float] Description Default Enable gradient clipping with value 1.0 ZeRO Optimizations for FP16 Training Enabling and configuring ZeRO memory optimizations "zero_optimization": { "stage": [0|1|2|3], "allgather_partitions": [true|false], "allgather_bucket_size": 5e8, "overlap_comm": false, "reduce_scatter": [true|false], "reduce_bucket_size": 5e8, "contiguous_gradients" : [true|false], "offload_param": { ... }, "offload_optimizer": { ... }, "stage3_max_live_parameters" : 1e9, "stage3_max_reuse_distance" : 1e9, "stage3_prefetch_bucket_size" : 5e8, "stage3_param_persistence_threshold" : 1e6, "sub_group_size" : 1e12, "elastic_checkpoint" : [true|false], "stage3_gather_16bit_weights_on_model_save": [true|false], "ignore_unused_parameters": [true|false], "round_robin_gradients": [true|false], "zero_hpz_partition_size": 1, "zero_quantized_weights": [true|false], "zero_quantized_gradients": [true|false], "log_trace_cache_warnings": [true|false], } zero_optimization: [dictionary] Description Default Enable ZeRO memory optimizations, compatible with FP16/BF16/FP32 and the Adam optimizer. false stage: [integer] Description Default Chooses different stages of ZeRO Optimizer. Stage 0, 1, 2, and 3 refer to disabled, optimizer state partitioning, and optimizer+gradient state partitioning, and optimizer+gradient+parameter partitioning, respectively. 0 allgather_partitions: [boolean] Description Default Chooses between allgather collective or a series of broadcast collectives to gather updated parameters from all the GPUs at the end of each step true allgather_bucket_size: [integer] Description Default Number of elements allgathered at a time. Limits the memory required for the allgather for large model sizes 5e8 overlap_comm: [boolean] Description Default Attempts to overlap the reduction of the gradients with backward computation false reduce_scatter: [boolean] Description Default Uses reduce or reduce scatter instead of allreduce to average gradients true reduce_bucket_size: [integer] Description Default Number of elements reduced/allreduced at a time. Limits the memory required for the allgather for large model sizes 5e8 contiguous_gradients: [boolean] Description Default Copies the gradients to a contiguous buffer as they are produced. Avoids memory fragmentation during backward pass. True load_from_fp32_weights: [boolean] Description Default Initialize fp32 master weights from fp32 copies in checkpoint (no precision loss) or from model’s fp16 copies (with precision loss). This can be used to initialize optimizer state even when checkpoint is missing optimizer state. True grad_hooks: [boolean] Description Default For use with ZeRO stage 1, enable backward hooks to reduce gradients during the backward pass or wait until the end of the backward pass. True round_robin_gradients: [boolean] Description Default Stage 1 and 2 optimization for CPU offloading that parallelizes gradient copying to CPU memory among ranks by fine-grained gradient partitioning. Performance benefit grows with gradient accumulation steps (more copying between optimizer steps) or GPU count (increased parallelism). False offload_param: [dictionary] Description Default Enable offloading of model parameters to CPU or NVMe. This frees up GPU memory for larger models or batch sizes. Valid only with stage 3. See here for more details. False offload_optimizer: [dictionary] Description Default Enable offloading of optimizer state to CPU or NVMe, and optimizer computation to CPU. This frees up GPU memory for larger models or batch sizes. Valid for ZeRO stage 1, 2, 3. See here for more details. False stage3_max_live_parameters: [integer] Description Default The maximum number of parameters resident per GPU before releasing. Smaller values use less memory, but perform more communication. 1e9 stage3_max_reuse_distance: [integer] Description Default Do not release a parameter if it will be reused within this threshold of parameters. Smaller values use less memory, but perform more communication. 1e9 stage3_prefetch_bucket_size: [integer] Description Default The size of the fixed buffer for prefetching parameters. Smaller values use less memory, but can increase stalls due to communication. 5e8 stage3_param_persistence_threshold: [integer] Description Default Do not partition parameters smaller than this threshold. Smaller values use less memory, but can greatly increase communication (especially latency-bound messages). 1e5 stage3_gather_16bit_weights_on_model_save: [boolean] Description Default Consolidate the weights before saving the model by save_16bit_model(). Since the weights are partitioned across GPUs, they aren’t part of state_dict, so this function automatically gathers the weights when this option is enabled and then saves the fp16 model weights. False stage3_module_granularity_threshold: [integer] | Description | Default | |——————————————————————————————————————————————————————————————————————————————————————————–| ——- | | The granularity of a module is determined by the ratio of parameter_count / (1 + descendant_count). ZeRO3 classifies modules with a granularity below the threshold as fine-grained, treating them as integral units during parameter fetching. This reduces host and communication overhead from separate hooks. | 0 | zero_hpz_partition_size: [integer] Description Default Number of ranks in hiearchical partitioning ZeRO (hpZ) secondary tensor group of ZeRO++, default is 1 meaning no hpZ, ideal is number of ranks (gpus) per node. 1 zero_quantized_weights: [boolean] Description Default Boolean indicating whether to enable communication efficient quantized weights of ZeRO++. False zero_quantized_gradients: [boolean] Description Default Boolean indicating whether to enable communication efficient quantized gradients of ZeRO++. False log_trace_cache_warnings: [boolean] Description Default Log warnings from trace cache optimization of parameter sharding, such as cache invalidation events. False cpu_offload: [boolean] Deprecated: cpu_offload is deprecated and will be removed in future, please use offload_optimizer instead. Description Default Enable offloading of optimizer memory and computation to CPU. This frees up GPU memory for larger models or batch sizes. Valid with stage 1 and 2. False Parameter offloading Enabling and configuring ZeRO optimization of parameter offloading to CPU/NVMe. Available only with ZeRO stage 3. Note that if the value of “device” is not specified or not supported, an assertion will be triggered. "offload_param": { "device": "[cpu|nvme]", "nvme_path": "/local_nvme", "pin_memory": [true|false], "buffer_count": 5, "buffer_size": 1e8, "max_in_cpu": 1e9 } device: [string] Description Default Device memory to offload model parameters. Supported options are cpu and nvme. cpu nvme_path: [string] Description Default Filesystem path for NVMe device for parameter offloading. /local_nvme pin_memory: [boolean] Description Default Offload to page-locked CPU memory. This could boost throughput at the cost of extra memory overhead. false buffer_count: [integer] Description Default Number of buffers in buffer pool for parameter offloading to NVMe. 5 buffer_size: [integer] Description Default Size of buffers in buffer pool for parameter offloading to NVMe. 1e8 max_in_cpu: [integer] Description Default Number of parameter elements to maintain in CPU memory when offloading to NVMe is enabled. 1e9 Optimizer offloading Enabling and configuring ZeRO optimization of offloading optimizer computation to CPU and state to CPU/NVMe. CPU offloading is available with ZeRO stage 1, 2, 3. NVMe offloading is available only with ZeRO stage 3. Note that if the value of “device” is not specified or not supported, an assertion will be triggered. "offload_optimizer": { "device": "[cpu|nvme]", "nvme_path": "/local_nvme", "pin_memory": [true|false], "ratio": 0.3, "buffer_count": 4, "fast_init": false } device: [string] Description Default Device memory to offload optimizer state. Supported options are cpu and nvme. Optimizer computation is offload to CPU regardless of device option. cpu nvme_path: [string] Description Default Filesystem path for NVMe device for optimizer state offloading. /local_nvme pin_memory: [boolean] Description Default Offload to page-locked CPU memory. This could boost throughput at the cost of extra memory overhead. false ratio: [float] Description Default the ratio of parameters updating (i.e. optimizer step) on CPU side. 1 buffer_count: [integer] Description Default Number of buffers in buffer pool for optimizer state offloading to NVMe. This should be at least the number of states maintained per parameter by the optimizer. For example, Adam optimizer has 4 states (parameter, gradient, momentum, and variance). 4 fast_init: [boolean] Description Default Enable fast optimizer initialization when offloading to NVMe. false Asynchronous I/O Configuring the asynchronous I/O module for offloading parameter and optimizer states to persistent (NVMe) storage. This module uses Linux native asynchronous I/O (libaio). "aio": { "block_size": 1048576, "queue_depth": 8, "thread_count": 1, "single_submit": false, "overlap_events": true } block_size: [integer] Description Default I/O block size in bytes. 1048576 queue_depth: [integer] Description Default I/O queue depth. 8 thread_count: [integer] Description Default Intra-request parallelism for each read/write submitted by a user thread. 1 single_submit: [boolean] Description Default Submit requests to storage device as multiple individual requests as opposed to one block of requests. false overlap_events: [boolean] Description Default Submit requests to storage device in an overlapped fashion without waiting for completion of earlier requests. true ignore_unused_parameters: [boolean] Description Default Unused parameters in modules may be unexpected in static networks, but could be normal in dynamic networks. This controls whether or not training should terminate with an error message when unused parameters are detected. This is set to True by default, which means unused parameters are ignored and training continues. Now is just used in stage 2. True Logging steps_per_print: [integer] Description Default Print progress report every N training steps. The report includes the number of training steps, number of skipped optimizer updates (likely due to overflows in mixed-precision training), current learning rate, and current momentum. 10 wall_clock_breakdown: [boolean] Description Default Enable timing of the latency of forward/backward/update training phases false dump_state: [boolean] Description Default Print out state information of DeepSpeed object after initialization false Autotuning { "autotuning": { "enabled": false, "results_dir": "autotuning_results", "exps_dir": "autotuning_exps", "overwrite": false, "metric": "throughput", "start_profile_step": 3, "end_profile_step": 5, "fast": true, "max_train_batch_size": null, "mp_size": 1, "num_tuning_micro_batch_sizes": 3, "tuner_type": "model_based", "tuner_early_stopping": 5, "tuner_num_trials": 50, "arg_mappings": null } } enabled: [boolean] Description Default Enables the autotuner. false results_dir: [string] Description Default Path to the autotuning experiment results directory. The default appears in the working directory from which Deepspeed was launched. “autotuning_results” exps_dir: [string] Description Default Path to the auotuning experiment descriptions directory. The default appears in the working directory from which Deepspeed was launched. “autotuning_exps” overwrite: [boolean] Description Default Whether to run autotuning experiments whose results already exist. Setting it to true would overwrite the existing result. false metric: [string] Description Default The performance metric to use for ranking autotuning experiments. latency, throughput, and FLOPS are currently supported, referring to training step latency, training samples per second, and floating-point operations per second achieved per GPU respectively. throughput start_profile_step: [integer] Description Default The global training step at which to start profiling in an autotuning experiment. Note that warm-up is needed for accurate performance measurement. 3 end_profile_step: [integer] Description Default The global training step at which to end profiling in an autotuning experiment. Must not be less than start_profile_step. 5 fast: [boolean] Description Default Enables fast-model autotuning where only Zero stages and micro-batch sizes per GPU are tuned. true max_train_batch_size: [int] Description Default The maximum train batch size (global effective batch size) for the model training. null mp_size: [int] Description Default Model parallelism degree. 1 num_tuning_micro_batch_sizes: [integer] Description Default The number of micro-batch sizes to explore. 3 tuner_type: [string] Description Default The algorithm defines the order of autotuning space exploration within a ZeRO stage. model_based tuner_early_stopping: [integer] Description Default The number of experiments to run beyond the current best experiment. If no better experiment is found within that number, the Autotuner stops the exploration. 5 tuner_num_trials: [integer] Description Default The maximum number of experiments to explore in the tuning space within a ZeRO stage. 50 Flops Profiler { "flops_profiler": { "enabled": false, "profile_step": 1, "module_depth": -1, "top_modules": 1, "detailed": true, "output_file": null, } } enabled: [boolean] Description Default Enables the flops profiler. This would also enables wall_clock_breakdown false profile_step: [integer] Description Default The global training step at which to profile. Note that warm up steps are needed for accurate time measurement. 1 module_depth: [integer] Description Default The depth of the model at which to print the aggregated module information. When set to -1, it prints information from the top module to the innermost modules (the maximum depth). -1 top_modules: [integer] Description Default Limits the aggregated profile output to the number of top modules specified. 1 detailed: [boolean] Description Default Whether to print the detailed model profile. true output_file: [string] Description Default Path to the output file. If None, the profiler prints to stdout.. null Activation Checkpointing "activation_checkpointing": { "partition_activations": false, "cpu_checkpointing": false, "contiguous_memory_optimization": false, "number_checkpoints": null, "synchronize_checkpoint_boundary": false, "profile": false } partition_activations: [boolean] Description Default Enables partition activation when used with model parallelism false cpu_checkpointing: [boolean] Description Default Offloads partitioned activations to CPU if partition_activations is enabled false contiguous_memory_optimization: [boolean] Description Default Copies partitioned activations so that they are contiguous in memory false number_checkpoints: [integer] Description Default Total number of activation checkpoints used to allocate memory buffer for contiguous_memory_optimization None synchronize_checkpoint_boundary: [boolean] Description Default Inserts get_accelerator().synchronize() at each checkpoint boundary. false profile: [boolean] Description Default Logs the forward and backward time for each checkpoint function false Sparse Attention sparse_attention: [dictionary] Fields Value Example mode A string determining sparsity structure type. Deepspeed currently supports "dense", "fixed", "bigbird", "bslongformer", and "variable". "fixed" block An integer determining the block size. Current implementation of sparse self-attention is based on blocked sparse matrices. In which this parameter defines size of such blocks, Block X Block. 16 different_layout_per_head A boolean determining if each head should be assigned a different sparsity layout; this will be satisfied based on availability. false num_local_blocks An integer determining the number of random blocks in each block row; only used in "fixed" mode. 4 num_global_blocks An integer determining how many consecutive blocks in a local window is used as the representative of the window for global attention; used in "fixed" and "bigbird" modes. 1 attention A string determining attention type. Attention can be "unidirectional", such as autoregressive models, in which tokens attend only to tokens appear before them in the context. Considering that, the upper triangular of attention matrix is empty. Or it can be "bidirectional", such as BERT, in which tokens can attend to any other tokens before or after them. Then, the upper triangular part of the attention matrix is mirror of the lower triangular; used in "fixed" and "variable" modes. "bidirectional" horizontal_global_attention A boolean determining if blocks that are global representative of a local window, also attend to all other blocks. This is valid only if attention type is "bidirectional". Looking at the attention matrix, that means global attention not only includes the vertical blocks, but also horizontal blocks; used in "fixed" and "variable" modes. false num_different_global_patterns An integer determining number of different global attentions layouts. While global attention can be fixed by which block/s are representative of any local window, since there are multi-heads, each head can use a different global representative; used only in "fixed" mode. 4 num_random_blocks An integer determining the number of random blocks in each block row; used in "variable" and "bigbird" modes. 0 local_window_blocks A list of integers determining the number of blocks in each local attention window. It assumes first number determines # of blocks in the first local window, second the second window, …, and the last number determines the number of blocks in the remaining local windows; only used in "variable" mode. [4] global_block_indices A list of integers determining which blocks are considered as global attention. Given indices, determine the blocks that all other token blocks attend to and they attend to all other token blocks. Notice that if global_block_end_indices parameter is set, this parameter is used as starting index of each global window; used in "variable" and "bslongformer" modes. [0] global_block_end_indices A list of integers determining end indices of global window blocks. By default this is not used. But if it is set, it must have the same size of global_block_indices parameter, and combining this two parameters, for each index i, blocks from global_block_indices[i] to global_block_end_indices[i], exclusive, are considered as global attention; used in "variable" and "bslongformer" modes. None num_sliding_window_blocks An integer determining the number of blocks in sliding local attention window; used in "bigbird" and "bslongformer" modes. 3 Example of sparse_attention "sparse_attention": { "mode": "fixed", "block": 16, "different_layout_per_head": true, "num_local_blocks": 4, "num_global_blocks": 1, "attention": "bidirectional", "horizontal_global_attention": false, "num_different_global_patterns": 4, "num_random_blocks": 0, "local_window_blocks": [4], "global_block_indices": [0], "global_block_end_indices": None, "num_sliding_window_blocks": 3 } Data Efficiency DeepSpeed Data Efficiency Library includes two techniques: curriculum learning and random layerwise token dropping (random-LTD). Read more about how to use the DeepSpeed Data Efficiency Library in our tutorial. "data_efficiency": { "enabled": true, "seed": 1234, "data_routing": { "enabled": true, "random_ltd":{ "enabled": true, "total_layer_num": 24, "random_ltd_layer_num": 22, "random_ltd_layer_id": [1,2,3,4,5,6,7,8,9,10,11,12,13,14,15,16,17,18,19,20,21,22], "model_mask_name": "attention_mask", "model_type": "decoder", "hidden_state_order": "seq_batch_dim", "random_ltd_schedule": { "min_value": 128, "max_value": 2048, "schedule_type":"fixed_linear", "schedule_config": { "require_steps": 200000, "seq_per_step": 16 } } } }, "data_sampling": { "enabled": true, "num_epochs": 1, "num_workers": 0, "curriculum_learning": { "enabled": true, "data_cluster_path": "/path/to/data_clusters", "curriculum_metrics": { "vocabularyrarity": { "index_to_sample_path": "/path/to/index_to_sample", "index_to_metric_path": "/path/to/index_to_metric", "difficulty_type": "percentile", "clustering_type": "schedule_based", "min_difficulty": 1, "max_difficulty": 100, "schedule_type": "fixed_root", "schedule_config": { "total_curriculum_step": 110000, "difficulty_step": 1, "root_degree": 2 } } } } } } data_efficiency: [dictionary] Fields Value Default enabled: [boolean] Enable data efficiency or not. false seed: [integer] Random seed for data sampling. 1234 data_routing: [dictionary] Configs for data routing techniques. N/A data_sampling: [dictionary] Configs for data sampling techniques. N/A data_routing: [dictionary] Fields Value Default enabled: [boolean] Enable data routing techniques or not. false random_ltd: [dictionary] Configs for random-LTD technique. N/A data_sampling: [dictionary] Fields Value Default enabled: [boolean] Enable data sampling techniques or not. false num_epochs: [integer] At most how many epoches of the original dataset will be iterated. 1000 num_workers: [integer] Data loader number of workers. 0 curriculum_learning: [dictionary] Configs for curriculum learing technique. N/A random_ltd: [dictionary] Fields Value Default enabled: [boolean] Enable random-LTD technique or not. false total_layer_num: [integer] The number of layer (or the depth) for the pretraining/fine-tuning model. N/A random_ltd_layer_num: [integer] The number of layers that will be applied with random-LTD. N/A random_ltd_layer_id: [list] The exact layer_id that will be applied with random-LTD. The length of this list must be the same as random_ltd_layer_num. N/A model_mask_name: [str] The variable name of the attention_mask. Different libraries have different names, such as att_mask. For huggingface model, it’s named “attention_mask”. Users need to check the forward function in the original model files. If the attention mask input in the original model’s forward function is not a keyword/named argument (e.g., attention_mask=None), user would need to change it to a keyword/named argument and provide that keyword as model_mask_name. N/A model_type: [str] Users need to identify whether the model is decoder or encoder. Currently we only support these two. N/A hidden_state_order: [str] Users need to know the input order of the hidden state tensor. Normally, it’s batch, sequence and then the hidden dimension, which is batch_seq_dim. Somethings, the order between batch and sequence will be switch like seq_batch_dim. Currently, we support these two. N/A random_ltd_schedule: [dictionary] The schedule of the effective sequence length after token dropping. It’s a linear function where random-LTD gradually drops less tokens and increases effective sequence length. N/A min_value: [integer] The initial effective sequence length (after token dropping) at step/iteration 0. N/A max_value: [integer] The max effective sequence length (usually the case without any token dropping). Usually this is set as baseline’s seqlen. N/A schedule_type: [str] The sequence length follows a linear increasing function starting from min_value and reaching max_value. We currently only support this type. N/A schedule_config: [dictionary] Configs for the linear increasing function. N/A require_steps: [integer] How many iterations will be needed to reach max_value from min_value. N/A seq_per_step: [integer] At any time, the effective sequence length be multiple of this seq_per_step. Set this to multiple of 8 (for FP16 data) or 16 (for INT8 data) to enable NVIDIA Tensor Core acceleration. N/A curriculum_learning: [dictionary] Fields Value Default enabled: [boolean] Enable curriculum learing technique or not. false data_cluster_path: [str] Path to directory where curriculum learning will store the indexes of data samples within the same difficulty ranges. N/A curriculum_metrics: [dictionary] This dictionary includes all desired curriculum metrics and their configs. Each metric will be a separate sub-dictionary, where the key is the metric name and the values are configs below. N/A index_to_sample_path: [str] Path to the index_to_sample file generated during offline data analysis. Note that data analysis will generate two kinds of index_to_sample files: The metric_name_index_to_sample_percentile_merged file is a concatenated index for perf improvement, but it only works when you set difficulty_type=percentile. If you use difficulty_type=value, you need to change this to use the metric_name_index_to_sample file. N/A index_to_metric_path: [str] Path to the index_to_metric_path file generated during offline data analysis. N/A difficulty_type: [str] During training, how to increase the max accepted difficulty. Currently support value (increase by absolute value) and percentile (increase by difficulty percentile). N/A clustering_type: [str] Currently support schedule_based (cluster data based on the difficulty schedule (pacing function) below) and single_cluster (no clustering required and probably CL is achieved by data postprocessing, such as sequence length truncation). N/A min_difficulty: [integer] Starting difficulty at first step. When difficulty_type=value the min_difficulty is an absolute difficulty value. When difficulty_type=percentile the min_difficulty is a difficulty percentile value. N/A max_difficulty: [integer] Final max difficulty. When difficulty_type=value the max_difficulty is an absolute difficulty value. When difficulty_type=percentile the max_difficulty is a difficulty percentile value. N/A schedule_type: [str] The difficulty schedule (pacing function) that defines how the max accepted difficulty increases from min_difficulty to max_difficulty during training. Currently support fixed_linear, fixed_root, fixed_discrete, and custom. N/A schedule_config: [dictionary] Configs for the pacing function. When schedule_type=custom this dictionary is not necessary. Instead user needs to provide a callback function (via the set_custom_curriculum_learning_schedule API in deepspeed/runtime/engine.py) which will update the max accepted difficulty during training. Configs below are all belongs to schedule_config. N/A total_curriculum_step: [integer] How many steps the curriculum learning takes to go from min difficulty to max difficulty. Used by fixed_linear and fixed_root schedule. N/A difficulty_step: [integer] The max accepted difficulty level determined every step must be a multiple of this difficulty_step. This is used to ensure the use of NVIDIA Tensor Core acceleration (requires multiple of 8 (FP16) or 16 (INT8)). Used by fixed_linear and fixed_root schedule. N/A root_degree: [integer] The degree of the root function. Degree of 2 means square root and degree of 3 means cube root. Degree of 1 is equivalent to linear. Used by fixed_root schedule. N/A difficulty: [list] List of max accepted difficulty levels to be used during schedule. Used by fixed_discrete schedule. N/A max_step: [list] List of which step to change max accepted difficulty level. Used by fixed_discrete schedule. N/A Curriculum Learning Note: On 12/12/2022, we released DeepSpeed Data Efficiency Library which provides a more general curriculum learning support. This legacy curriculum learning feature below is still supported but we recommend to use the Data Efficiency Library. "curriculum_learning": { "enabled": true, "curriculum_type": "seqlen", "min_difficulty": 8, "max_difficulty": 1024, "schedule_type": "fixed_linear", "schedule_config": { "total_curriculum_step": 40000, "difficulty_step": 8 } } enabled: [boolean] Description Default Set to true to enable curriculum learning false curriculum_type: [string] Description Default Type of curriculum difficulty metric. Currently support seqlen. N/A min_difficulty: [integer] Description Default The starting difficulty level N/A max_difficulty: [integer] Description Default The ending difficulty level N/A schedule_type: [string] Description Default Type of curriculum schedule. Currently support fixed_linear, fixed_root, and fixed_discrete. N/A total_curriculum_step: [integer] Description Default Total number of steps for the curriculum learning. One of the schedule_config when the fixed_linear and fixed_root schedule_type are used. N/A difficulty_step: [integer] Description Default At any time, the curriculum learning difficulty must be multiple of this difficulty_step. Set this to multiple of 8 (for FP16 data) or 16 (for INT8 data) to enable NVIDIA Tensor Core acceleration. One of the schedule_config when the fixed_linear and fixed_root schedule_type are used. N/A root_degree: [integer] Description Default Root degree of the curriculum schedule function. One of the schedule_config when the fixed_root schedule_type is used. N/A difficulty: [list of integer] Description Default List of difficulty levels to be used during schedule. One of the schedule_config when the fixed_discrete schedule_type is used. N/A max_step: [list of integer] Description Default List of which step to change difficulty level. One of the schedule_config when the fixed_discrete schedule_type is used. N/A Monitoring Module Note: Deepspeed logs to TensorBoard through PyTorch. Logging to TensorBoard requires that the tensorboard package is installed (read more in the PyTorch documentation). Note: Logging to WandB requires that the wandb package is installed (read more in the WandB documentation). Note: Logging to Comet requires that the comet_ml package is installed (read more in the Comet documentation). Deepspeed’s Monitor module can log training details into a Tensorboard-compatible file, to WandB, to Comet or to simple CSV files. Below is an overview of what DeepSpeed will log automatically. Field Description Conditions Train/Samples/train_loss The training loss. None Train/Samples/lr The learning rate during training. None Train/Samples/loss_scale The loss scale when training using fp16. fp16 must be enabled. Train/Eigenvalues/ModelBlockParam_{i} Eigen values per param block. eigenvalue must be enabled. Train/Samples/elapsed_time_ms_forward The global duration of the forward pass. flops_profiler.enabled or wall_clock_breakdown. Train/Samples/elapsed_time_ms_backward The global duration of the forward pass. flops_profiler.enabled or wall_clock_breakdown. Train/Samples/elapsed_time_ms_backward_inner The backward time that does not include the gradient reduction time. Only in cases where the gradient reduction is not overlapped, if it is overlapped then the inner time should be about the same as the entire backward time. flops_profiler.enabled or wall_clock_breakdown. Train/Samples/elapsed_time_ms_backward_allreduce The global duration of the allreduce operation. flops_profiler.enabled or wall_clock_breakdown. Train/Samples/elapsed_time_ms_step The optimizer step time flops_profiler.enabled or wall_clock_breakdown. tensorboard: [dictionary] Fields Value Default enabled Whether logging to Tensorboard is enabled. false output_path Path to where the Tensorboard logs will be written. If None, the output path is set under the training script’s launching path. null job_name Name for the current job. This will become a new directory inside output_path. "DeepSpeedJobName" Example of tensorboard configuration: "tensorboard": { "enabled": true, "output_path": "output/ds_logs/", "job_name": "train_bert" } wandb: [dictionary] Fields Value Default enabled Whether logging to WandB is enabled. false group Name for the WandB group. This can be used to group together runs. None team Name for the WandB team. None project Name for the WandB project. deepspeed Example of wandb configuration: "wandb": { "enabled": true, "group": "my_group", "team": "my_team", "project": "my_project" } comet: [dictionary] Fields Value Default enabled Whether logging to Comet is enabled. false workspace Comet workspace name. None project Comet project name. None samples_log_interval Metrics will be submitted to Comet after processing every samples_log_intervas samples. 100 experiment_name The name for comet experiment to be used for logging. None api_key Comet API key. It’s not recommended to save the Comet API Key in code. None experiment_key The key for comet experiment to be used for logging. Must be an alphanumeric string whose length is between 32 and 50 characters. None online If True, the data will be logged to Comet server, otherwise it will be stored locally in offline experiment. Default is True. None mode Control how the Comet experiment is started. “get”: Continue logging to an existing experiment identified by the experiment_key value. “create”: Always creates of a new experiment, useful for HPO sweeps. “get_or_create” (default): Starts a fresh experiment if required, or persists logging to an existing one. None Example of comet configuration: "comet": { "enabled": true, "workspace": "my_workspace", "project": "my_project", "samples_log_interval": 50, "experiment_name": "llama-fine-tuning", "experiment_key": "0c4a1c4a90664f2a8084e600b19a9d7", "online": false, "mode": "get", } csv_monitor: [dictionary] Fields Value Default enabled Whether logging to local CSV files is enabled. false output_path Path to where the csv files will be written. If None, the output path is set under the training script’s launching path. null job_name Name for the current job. This will become a new directory inside output_path "DeepSpeedJobName" Example of csv_monitor configuration: "csv_monitor": { "enabled": true, "output_path": "output/ds_logs/", "job_name": "train_bert" } Elastic Training Config (V0.1 and V0.2) "elasticity": { "enabled": true, "max_train_batch_size": "seqlen", "micro_batch_sizes": 8, "min_gpus": 1024, "max_gpus": "fixed_linear", "min_time": "seqlen", "version": 8, "ignore_non_elastic_batch_info": 1024, "num_gpus_per_node": "fixed_linear", "model_parallel_size": MODEL_PARALLEL_SIZE } Field Description Default enabled Enables computation of global batch size in elastic training. false max_train_batch_size Max acceptable batch size can be used in training. 2000 micro_batch_sizes Acceptable micro batch sizes, same as train_micro_batch_size_per_gpu [2,4,6] min_gpus Min number of GPUs to search over when computing highly composite batch size in v0.1 and v0.2. 1 max_gpus Max number of GPUs to search over when computing highly composite batch size in v0.1 and v0.2. 10000 min_time Minimum running time (minutes) before the scheduler will scale again (only used in v0.1). 0 implies it’s unknown 0 prefer_large_batch When finding a suitable batch size, attempt to find one that is closest to the max train batch size given. true version Version of elastic logic to use. 0.2 ignore_non_elastic_batch_info Ignore all batch info provided outside the elastic config. To reduce confusion, we require all batch related info to be given in elastic config only. false num_gpus_per_node Number of GPUs per node. This information is used by v0.2 to support model-parallel training (only used by v0.2) 1 model_parallel_size Tensor or model parallel size (only used by v0.2) 1 Communication Logging DeepSpeed provides a flexible communication logging tool which can automatically detect and record communication operations launched via deepspeed.comm. NOTE: All logging communication calls are synchronized in order to provide accurate timing information. This may hamper performance if your model heavily uses asynchronous communication operations. Once the logs are populated, they can be summarized with deepspeed.comm.log_summary(). For more detail and example usage, see the tutorial comms_logger: [dictionary] Fields Value Default enabled Whether communication logging is enabled. false verbose Whether to immediately print every communication operation false prof_all Whether to profile all operations. true debug Appends the caller function to each communication operation’s log_name. false prof_ops A list of communication operations to log (only the specified ops will be profiled). [] Example of recommended comms_logger configuration: "comms_logger": { "enabled": true, "verbose": false, "prof_all": true, "debug": false } Example of comms_logger configuration for logging specific operations only: "comms_logger": { "enabled": true, "verbose": false, "prof_all": false, "debug": false, "prof_ops": ["all_reduce", "all_gather"] } Compression Note: Compression has seven different components, including layer reduction, weight quantization, activation quantization, sparse pruning, row pruning, head pruning, and channel pruning. We explain them one by one with simple json examples. Read more about how to use the DeepSpeed Compression library in our tutorial. Layer Reduction Note: Layer reduction works much better when using knowledage distillation (learn more in our tutorial): "compression_training": { "layer_reduction": { "enabled": true, "keep_number_layer": 5, "module_name_prefix": "bert.encoder.layer", "teacher_layer": [ 2, 4, 6, 8, 10 ], "other_module_name": [ "bert.pooler", "bert.embeddings", "classifier" ] } } layer_reduction: [dictionary] Fields Value Default enabled: [boolean] Enable layer reduction or not. false keep_number_layer: [list] The number of layer in the model to be kept. N/A module_name_prefix: [str] The (uniform) name prefix of the model’s modules of which the associated weight parameters are to be reinitialized. N/A teacher_layer: [list] The layer of the weight parameters are to be reinitialized. The length of the list equals to ‘keep_number_layer’. N/A other_module_name: [list] The name of modules of which the associated weight parameters are to be reinitialized. It is an complemenatory or alternative of module_name_prefix. For instance, “other_module_name”: [“bert.encoder.layer.2”,”bert.encoder.layer.4”] equals to “module_name_prefix”:”bert.encoder.layer” and “teacher_layer”: [2,4]. N/A Weight Quantization "compression_training": { "weight_quantization": { "shared_parameters":{ "enabled": true, "quantizer_kernel": false, "schedule_offset": 0, "quantize_groups": 1, "quantize_verbose": false, "quantization_type": "symmetric", "rounding": "nearest", "quantize_weight_in_forward": false, "fp16_mixed_quantize":{ "enabled": false, "quantize_change_ratio": 0.001 } }, "different_groups":{ "wq1": { "params": { "start_bits": 8, "target_bits": 8, "quantization_period": 50 }, "modules": [ "attention.self", "intermediate" ] }, "wq2": { "params": { "start_bits": 4, "target_bits": 4, "quantization_period": 50 }, "modules": [ "attention.output" ] } } } } shared_parameters: [dictionary] Shared parameters for all weight quantization groups. Fields Value Default enabled: [boolean] Enable weight quantization or not. false quantizer_kernel: [boolean] Use DeepSpeed quantization kernel for >=4 bit quantization. This can only be enabled when using DeepSpeed FP16 optimizer. false schedule_offset: [integer] Enable weight quantization after scheduled steps (can be treated as warmup steps). 0 quantize_groups: [integer] Split the weight matrix into different number of groups, and each of them has its own scaling factor. 1 quantize_verbose: [boolean] Print the quantization related logs. false quantization_type: [string] Choose the quantization algorithm, symmetric or asymmetric. "symmetric" rounding: [string] Rounding algorithm associated with quantization, nearest or stochastic. "nearest" quantize_weight_in_forward: [boolean] Quantize weight in optimizer or forward step, must set to be true for FP32 optimizer training. false fp16_mixed_quantize: [dictionary] Using the value mixed by FP16 value and the quantized value. N/A enabled: [boolean] Whether fp16 mixed quantization is enabled. false quantize_change_ratio: [float] Initial quantize value ratio, will gradually increase to 1. 0.001 different_groups: [dictionary] Different quantization sets, this is used for different quantization parameters. In this example, we give two different sets. In practice, you can choose the number of sets based on your requirements. Fields Value Default params: [dictionary] start_bits: [integer] Quantization starting bits, will gradaully reduce to target bits. 8 target_bits: [integer] Quantization target bits, need to be <= start_bits. 8 quantization_period: [integer] For every n steps, the quantization bits will be reduce by 1. 1 modules: [list] Scope of weight parameters associated to the params setting. "All Linear and CONV2D layers" Activation Quantization "compression_training": { "activation_quantization": { "shared_parameters":{ "enabled": true, "quantization_type": "asymmetric", "range_calibration": "dynamic", "schedule_offset": 50 }, "different_groups":{ "aq1": { "params": { "bits": 8 }, "modules": [ "attention.output" ] } } } shared_parameters: [dictionary] Shared parameters for all activation quantization groups. Fields Value Default enabled: [boolean] Enable activation quantization or not. false quantization_type: [string] Choose the quantization algorithm, symmetric or asymmetric. "symmetric" range_calibration: [string] Using dynamic (per token or per image) or static (fixed min/max using momentum) for inference. "static" schedule_offset: [integer] Enable activation quantization after scheduled steps (can be treated as warmup steps). 0 different_groups: [dictionary] Different quantization sets, this is used for different quantization parameters. In this example, we give one set. In practice, you can choose the number of sets based on your requirements. Fields Value Default params: [dictionary] bits: [integer] Number of bits used for activation target bits, need to be >= 4. 8 modules: [list] Scope of weight parameters associated to the params setting. "All Linear and CONV2D layers" Sparse Pruning "compression_training": { "sparse_pruning":{ "shared_parameters":{ "enabled": true, "schedule_offset": 30, "method": "l1" }, "different_groups":{ "sp1": { "params": { "dense_ratio": 0.5 }, "modules": [ "attention.self" ] } } } } "compression_training": { "sparse_pruning":{ "shared_parameters":{ "enabled": true, "schedule_offset": 30, "schedule_offset_end": 90, "schedule_offset_stride": 15, "method": "snip_momentum", "block_pattern": "4x1", "dense_ratio": 0.4, "excluded_modules": ['classifier', 'pooler'] }, "different_groups":{ } } } shared_parameters: [dictionary] Shared parameters for all sparse pruning groups. Fields Value Default enabled: [boolean] Enable sparse pruning or not. false schedule_offset: [integer] Enable sparse pruning after scheduled steps (can be treated as warmup steps). 0 schedule_offset_end: [integer] Disable sparse pruning after scheduled steps, mandotory for snip_momentum. 0 schedule_offset_stride: [integer] The stride of pruning on training steps, mandotory for snip_momentum. "1" method: [string] Choose different pruning methods, l1 (static, magnitude based), topk (dynamic, learnable) or snip_momentum (structured pruning). "l1" block_pattern: [string] Choose different structured pruning block patterns, NxM or N:M (N and M are integers). For instance, “4x1” or “2:4” are common block patterns, mandotory for snip_momentum. "4x1" dense_ratio: [float] Used to get the targeted global sparsity ratio, mandotory for snip_momentum. "0.1" excluded_modules: [list] Excluded pruning scope on some special modules like output layer. [] different_groups: [dictionary] Different pruning sets, this is used for different pruning parameters. In this example, we give one set. In practice, you can choose the number of sets based on your requirements. Note for snip_momentum method, you can leave it as empty. Fields Value Default params: [dictionary] dense_ratio: [float] The percentage of weights to keep after pruning. 0.5 modules: [list] Scope of weight parameters associated to the params setting. "All Linear and CONV2D layers" Row Pruning Note: Row Pruning is a feature designed for two back-to-back linear layers (e.g., Feed Forward Network in Transformers). As such, we suggested use row pruning for the first linear layer (i.e., the intermediate.dense layer for BERT). Reducing the row dimension of this matrix can help reducing the column of the follow-up matrix (i.e., layer.\\w+.output.dense layer for BERT). It should also work for other linear layers as well. "compression_training": { "row_pruning":{ "shared_parameters":{ "enabled": true, "schedule_offset": 20, "method": "topk" }, "different_groups":{ "rp1": { "params": { "dense_ratio": 0.5 }, "modules": [ "intermediate.dense" ], "related_modules":[ ["layer.\\w+.output.dense"] ] } } } } shared_parameters: [dictionary] Shared parameters for all row pruning groups. Fields Value Default enabled: [boolean] Enable row pruning or not. false schedule_offset: [integer] Enable row pruning after scheduled steps (can be treated as warmup steps). 0 method: [string] Choose different pruning methods, l1 (static, magnitude based) or topk (dynamic, learnable). "l1" different_groups: [dictionary] Different pruning sets, this is used for different pruning parameters. In this example, we give one set. In practice, you can choose the number of sets based on your requirements. Fields Value Default params: [dictionary] dense_ratio: [float] The percentage of weights to keep after pruning. 0.5 modules: [list] Scope of weight parameters associated to the params setting. "All Linear and CONV2D layers" related_modules: [list[list]] Related module to the row pruned module, which can be performed column pruning. None Head Pruning Note: Head Pruning is a feature designed for two attention layers (e.g., Multi Head Attention in Transformers). For now, it can only be applied to output matrix of the Transformer (i.e., attention.output.dense in BERT). Pruning the output matrix can lead to the pruning of Query/Key/Value matrix as well. "compression_training": { "head_pruning":{ "shared_parameters":{ "enabled": true, "schedule_offset": 10, "method": "topk", "num_heads": 12 }, "different_groups":{ "rp1": { "params": { "dense_ratio": 0.5 }, "modules": [ "attention.output.dense" ], "related_modules":[ ["self.query", "self.key", "self.value"] ] } } } } shared_parameters: [dictionary] Shared parameters for all head pruning groups. Fields Value Default enabled: [boolean] Enable head pruning or not. false schedule_offset: [integer] Enable head pruning after scheduled steps (can be treated as warmup steps). 0 method: [string] Choose different pruning methods. For now, we only support topk (dynamic, learnable). "topk" num_heads: [int] Number of heads (must be provided by user). N/A different_groups: [dictionary] Different pruning sets, this is used for different pruning parameters. In this example, we give one set. In practice, you can choose the number of sets based on your requirements. Fields Value Default params: [dictionary] dense_ratio: [float] The percentage of weights to keep after pruning. 0.5 modules: [list] Scope of weight parameters associated to the params setting. "All Linear and CONV2D layers" related_modules: [list[list]] Related module (Usually Q/K/V) to the head pruned module (i.e., the output matrix). For now, this feature only works for BERT. None Channel Pruning Note: Channel Pruning is a feature designed for two back-to-back CONV2d layers (e.g., residual connection in ResNet). As such, we suggested use channel pruning for the first CONV2d layer. Reducing the number of output channels of this layer can help reducing the number of input channels the follow-up layer. It should also work for other CONV2d layers as well. "compression_training": { "channel_pruning":{ "shared_parameters":{ "enabled": true, "schedule_offset": 0, "method": "topk" }, "different_groups":{ "cp1": { "params": { "dense_ratio": 0.5 }, "modules": [ "layer....conv1" ], "related_modules": [ ["layer....conv2", "layer....bn1"] ] } } } } shared_parameters: [dictionary] Shared parameters for all channel pruning groups. Fields Value Default enabled: [boolean] Enable channel pruning or not. false schedule_offset: [integer] Enable channel pruning after scheduled steps (can be treated as warmup steps). 0 method: [string] Choose different pruning methods, l1 (static, magnitude based) or topk (dynamic, learnable). "l1" different_groups: [dictionary] Different pruning sets, this is used for different pruning parameters. In this example, we give one set. In practice, you can choose the number of sets based on your requirements. Fields Value Default params: [dictionary] dense_ratio: [float] The percentage of weights to keep after pruning. 0.5 modules: [list] Scope of weight parameters associated to the params setting. "All CONV2D layers" related_modules: [list[list]] Related module to the channel pruned module. None Checkpoint options "checkpoint": { "tag_validation"="Warn", "load_universal"=false, "use_node_local_storage"=false, "parallel_write":{ "pipeline_stage": false } } tag_validation: [“Ignore” “Warn” “Fail”] Description Default Enables level of checking to ensure checkpoint tags are consistent across all ranks. Useful when restoring with different world sizes. “Warn” load_universal: [boolean] Description Default Load the latest checkpoint for all. false use_node_local_storage: [boolean] Description Default If true DeepSpeed will store model parameter states and checkpoint states based on local rank allowing checkpoints to be loaded without access to a shared filesystem. false pipeline_stage: [boolean] Description Default Use pipeline stages to parallelize the writing of checkpoints. false Data Type options "data_types": { "grad_accum_dtype"=["fp32"|"fp16"|"bf16"] } } grad_accum_dtype: [“fp32” “fp16” “bf16”] Description Default Specifies the data type in which to do gradient accumulation. If None the default is to match the model type. None

```
32
```

**Pattern 8:** Monitor Contents Overview Usage Automatic Monitoring Custom Monitoring In this tutorial, we introduce the DeepSpeed Monitor and provide examples of its usage. Overview Usage Overview Monitoring model and system metrics during training is vital to ensure hardware resources are fully utilized. The DeepSpeed Monitor enables live logging of metrics through one or more monitoring backends such as PyTorch’s TensorBoard, WandB, Comet and simple CSV files. Below is a live monitoring view for TensorBoard: Below is a live monitoring view for WandB: Below is a live monitoring view for Comet: Usage The DeepSpeed Monitor is configured within the deepspeed configuration file. DeepSpeed will automatically monitor key training metrics, including those tracked with the wall_clock_breakdown configuration option. In addition, users can log their own custom events and metrics. Automatic Monitoring Custom Monitoring Automatic Monitoring When using DeepSpeed for model training, the Monitor can be configured in the DeepSpeed configuration file. No explicit API calls are needed to use the Monitor. The Monitor can be enabled by adding the following field to DeepSpeed’s configuration json file. Refer to Monitoring for details. { "tensorboard": { "enabled": true, "output_path": "output/ds_logs/", "job_name": "train_bert" } "wandb": { "enabled": true, "team": "my_team", "group": "my_group", "project": "my_project" } "comet": { "enabled": true, "project": "my_project", "experiment_name": "my_experiment" } "csv_monitor": { "enabled": true, "output_path": "output/ds_logs/", "job_name": "train_bert" } } DeepSpeed will automatically log to all available and enabled monitoring backends listed in the config, and will generate live monitoring views such as those listed above. Custom Monitoring In addition to automatic monitoring, users can log their own custom metrics in client scripts. Currently, there are two ways to initialize Monitor objects: (Recommended) - Create a MonitorMaster(ds_config.monitor_config) object, which automatically initializes all monitor backends present in the DeepSpeed configuration Create a specific TensorBoardMonitor(ds_config.monitor_config), WandbMonitor(ds_config.monitor_config), csvMonitor(ds_config.monitor_config) object which will only initialize a specific monitor backend present in the DeepSpeed configuration The steps to create a custom monitor are as follows: Add import to your desired Monitor Initialize monitor with DeepSpeed config’s monitor_config Create a list of one or more 3-tuples in the format [("label", value, ds_engine.global_samples), ...]* Call monitor.write_events on the list from step 3 * Note - Some Monitor backends don’t support mixed sample values. Be sure to use your DeepSpeed engine object’s global_samples attribute in each 3-tuple For example usage, see the following modified DeepSpeedExamples/cifar example: # Step 1: Import monitor (and DeepSpeed config, if needed) from deepspeed.monitor.monitor import MonitorMaster from deepspeed.runtime.config import DeepSpeedConfig # Step 2: Initialized monitor with DeepSpeed config (get DeepSpeed config object, if needed) ds_config = DeepSpeedConfig("ds_config.json") monitor = MonitorMaster(ds_config.monitor_config) for epoch in range(2): running_loss = 0.0 for i, data in enumerate(trainloader): pre = time.time() inputs, labels = data[0].to(model_engine.local_rank), data[1].to( model_engine.local_rank) if fp16: inputs = inputs.half() outputs = model_engine(inputs) loss = criterion(outputs, labels) model_engine.backward(loss) model_engine.step() post = time.time() # Step 3: Create list of 3-tuple records (single entry in this case) events = [("Time per step", post-pre, model_engine.global_samples)] # Step 4: Call monitor.write_events on the list from step 3 monitor.write_events(events) Updated: November 5, 2025 Previous Next

```
wall_clock_breakdown
```

### Example Code Patterns

**Example 1** (python):
```python
### Create aio_handle
from deepspeed.ops.op_builder import AsyncIOBuilder
aio_handle = AsyncIOBuilder().load().aio_handle()
```

## Reference Files

This skill includes comprehensive documentation in `references/`:

- **08.md** - 08 documentation
- **09.md** - 09 documentation
- **2020.md** - 2020 documentation
- **2023.md** - 2023 documentation
- **assets.md** - Assets documentation
- **mii.md** - Mii documentation
- **other.md** - Other documentation
- **tutorials.md** - Tutorials documentation

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

# Deepspeed Documentation Index

## Categories

### 08
**File:** `08.md`
**Pages:** 1

### 09
**File:** `09.md`
**Pages:** 2

### 2020
**File:** `2020.md`
**Pages:** 16

### 2023
**File:** `2023.md`
**Pages:** 21

### Assets
**File:** `assets.md`
**Pages:** 29

### Mii
**File:** `mii.md`
**Pages:** 1

### Other
**File:** `other.md`
**Pages:** 15

### Tutorials
**File:** `tutorials.md`
**Pages:** 59




### 2020

# Deepspeed - 2020

**Pages:** 16

---

## DeepSpeed Microsoft Research Webinar is now on-demand

**URL:** https://www.deepspeed.ai/2020/08/06/webinar-on-demand.html

**Contents:**
- DeepSpeed Microsoft Research Webinar is now on-demand
    - Contents

Updated: August 6, 2020

---

## An Order-of-Magnitude Larger and Faster Training with ZeRO-2

**URL:** https://www.deepspeed.ai/2020/05/18/zero-stage2.html

**Contents:**
- An Order-of-Magnitude Larger and Faster Training with ZeRO-2

ZeRO-2 expands the scope of memory optimizations in the original ZeRO by tackling the full spectrum of memory consumption during training. More specifically, ZeRO-2 introduces new technology to reduce the memory footprint of gradients, activation memory, and fragmented memory, in addition to optimizer state memory optimization in the original ZeRO. Altogether, the memory savings empower DeepSpeed to improve the scale and speed of deep learning training by an order of magnitude. More concretely, ZeRO-2 allows training models as large as 170 billion parameters up to 10x faster compared to state of the art.

For more information on ZeRO-2, see our blog post.

For more information on how to use ZeRO-2, see an example of training GPT family of models in this tutorial.

For a technical overview, see our technical report.

Updated: May 18, 2020

---

## 10x bigger model training on a single GPU with ZeRO-Offload

**URL:** https://www.deepspeed.ai/2020/09/08/ZeRO-Offload.html

**Contents:**
- 10x bigger model training on a single GPU with ZeRO-Offload

We introduce a new technology called ZeRO-Offload to enable 10X bigger model training on a single GPU. ZeRO-Offload extends ZeRO-2 to leverage both CPU and GPU memory for training large models. Using a machine with a single GPU, our users now can run models of up to 13 billion parameters without running out of memory, 10x bigger than the existing approaches, while obtaining competitive throughput. This feature democratizes multi-billion-parameter model training and opens the window for many deep learning practitioners to explore bigger and better models.

Updated: September 8, 2020

---

## Progressive Layer Dropping

**URL:** https://www.deepspeed.ai/2020/10/28/progressive-layer-dropping-news.html

**Contents:**
- Progressive Layer Dropping

We introduce a new technology called progressive layer dropping (PLD) to speedup the pre-training of Transformer-based networks through efficient and robust compressed training. The pre-training step of Transformer networks often suffer from unbearable overall computational expenses. We analyze the training dynamics and stability of Transformer networks and propose PLD to sparsely update Transformer blocks following a progressive dropping schedule, which smoothly increases the layer dropping rate for each mini-batch as training evolves along both the temporal and the model depth dimension. PLD is able to allow the pre-training to be 2.5X faster to get similar accuracy on downstream tasks and allows the training to be 24% faster when training the same number of samples, not at the cost of excessive hardware resources.

Updated: October 28, 2020

---

## ZeRO-2 & DeepSpeed: Shattering Barriers of Deep Learning Speed & Scale

**URL:** https://www.deepspeed.ai/2020/05/18/press-release.html

**Contents:**
- ZeRO-2 & DeepSpeed: Shattering Barriers of Deep Learning Speed & Scale
    - Contents

Updated: May 18, 2020

---

## ZeRO stage 1 with reduced communication

**URL:** https://www.deepspeed.ai/2020/03/17/reduce-scatter.html

**Contents:**
- ZeRO stage 1 with reduced communication
    - Contents
- Further updates coming soon!

Updated: March 17, 2020

---

## Powering 10x longer sequences and 6x faster execution through DeepSpeed Sparse Attention

**URL:** https://www.deepspeed.ai/2020/09/08/sparse-attention-news.html

**Contents:**
- Powering 10x longer sequences and 6x faster execution through DeepSpeed Sparse Attention

DeepSpeed offers sparse attention kernels, an instrumental technology to support long sequences of model inputs, whether for text, image, or sound. Compared with the classic dense Transformers, it powers an order-of-magnitude longer input sequence and obtains up to 6x faster execution with comparable accuracy. It also outperforms state-of-the-art sparse implementations with 1.5-3x faster execution. Furthermore, our sparse kernels support efficient execution of flexible sparse format and empower users to innovate on their custom sparse structures.

Updated: September 8, 2020

---

## ZeRO & DeepSpeed: New system optimizations enable training models with over 100 billion parameters

**URL:** https://www.deepspeed.ai/2020/02/13/release.html

**Contents:**
- ZeRO & DeepSpeed: New system optimizations enable training models with over 100 billion parameters
    - Contents

Updated: February 13, 2020

---

## Microsoft DeepSpeed achieves the fastest BERT training time

**URL:** https://www.deepspeed.ai/2020/05/27/fastest-bert-training.html

**Contents:**
- Microsoft DeepSpeed achieves the fastest BERT training time
    - Contents
- Performance Results for BERT Pretraining
- Performance Results for Fine-Tuning Tasks
- BERT Highly Optimized Transformer Kernels
  - (a) Advanced fused kernels to reduce data movement
  - (b) Invertible operators to save memory and run large batches
- Overlapping I/O with Computation through Asynchronous Prefetching Queue
- Exploiting Sparsity of BERT’s Output Processing
- Pre-LayerNorm vs Post-LayerNorm Architecture

Good news! DeepSpeed obtains the fastest BERT training record: 44 minutes on 1024 NVIDIA V100 GPU. This is a 30% improvement over the best published result of 67 mins in end-to-end training time to achieve the same accuracy on the same number and generation of GPUs. This improvement does not come at the cost of excessive hardware resources but comes from improved software efficiency. For example, DeepSpeed can attain a staggering 64 teraflops of single GPU performance on a NVIDIA V100 GPU which is over 50% of the hardware peak.

In this blog post, we will discuss four technological improvements that enable DeepSpeed to achieve this record-breaking BERT training time.

These optimizations not only benefit BERT; they are also applicable to many other transformer-based models such as RoBERTa, XLNet, and UniLM. Furthermore, besides the improvements mentioned for pre-training, DeepSpeed achieves up to 1.5x speedups for the downstream tasks, such as the fine-tuning for Bing-BERT SQuAD.

Compared to SOTA, DeepSpeed significantly improves single GPU performance for transformer-based model like BERT. Figure 1 shows the single GPU throughput of training BERT-Large optimized through DeepSpeed, comparing with the two well-known PyTorch implementations from NVIDIA BERT and Hugging Face BERT. DeepSpeed reaches as high as 64 and 53 teraflops throughputs (corresponding to 272 and 52 samples/second) for sequence lengths 128 and 512, respectively, exhibiting up to 28% throughput improvements over NVIDIA BERT and up to 62% over HuggingFace BERT. We also support up to 1.8x larger batch size without running out of memory.

To achieve this performance, DeepSpeed implements a stochastic transformer which exhibits some level of non-deterministic noise without affecting overall convergence. In addition, DeepSpeed also implements a deterministic transformer kernel that is completely reproducible at the expense of a small performance regression of approximately 2% on average. Users can easily choose and switch between the two versions depending on their usage scenarios: Stochastic version pursues ultimate training performance goal, and deterministic version may save development time by better facilitating experimentation and debugging. We report performance numbers for both these kernels in Figure 1. The performance numbers were collected with a gradient accumulation step of 10 for all batch sizes and configurations, since on average an overall batch size used in practical scenarios range from a few hundred to a few thousand.

Figure 1: Performance evaluation of BERT-Large on a single V100 GPU, comparing DeepSpeed with NVIDIA and HuggingFace versions of BERT in mixed-sequence length training. The labeled points show the highest throughput of each implementation in teraflops (Tflops). DeepSpeed boosts throughput and allows for higher batch sizes without running out-of-memory.

Looking at distributed training across GPUs, Table 1 shows our end-to-end BERT-Large pre-training time (F1 score of 90.5 for SQUAD) using 16 to 1024 GPUs. We complete BERT pre-training in 44 minutes using 1024 V100 GPUs (64 NVIDIA DGX-2 nodes). In comparison, the previous SOTA from NVIDIA takes 47 mins using 1472 V100 GPUs. DeepSpeed is not only faster but also uses 30% less resources. Using the same 1024 GPUS,NVIDIA BERT takes 67 minutes using the same 1024 GPUs [1] BERT, whereas DeepSpeed takes 44 minutes, reducing training time by 30%. Similarly, on 256 GPUs, NVIDIA BERT takes 236 minutes while DeepSpeed takes 144 minutes (39% faster).

Table 1: BERT-Large training time using 1 to 64 DGX-2’s with DeepSpeed.

At the recent GTC 2020, NVIDIA announced the next generation hardware A100, which now offers 2.5X hardware peak performance over the V100 GPU. Assuming the A100 GPU allows us to obtain the same percentage of hardware peak performance (50%) as we obtained on V100 GPUs, we expect to obtain even higher throughput by combining our software optimizations with the new hardware. We project it would reduce BERT training time further to less than 25 minutes on a cluster of 1024 A100 GPUs.

In addition to the performance benefits we show for the pre-training, we have evaluated the performance of our customized kernel for fine-tuning the downstream tasks. Tables 2 and 3 show the samples-per-second achieved when running Bing-BERT SQuAD on NVIDIA V100 using 16 and 32 GB of memory, using PyTorch and DeepSpeed transformer kernels. For the 16-GB V100, we can achieve up to 1.5x speedup while supporting 2x larger batch size per GPU. On the other hand, we can support as large as 32 batch size (2.6x more than Pytorch) using 32GB of memory, while providing 1.3x speedup for the end-to-end fine-tune training. Note, that we use the best samples-per-second to compute speedup for the cases that PyTorch runs out-of-memory (OOM).

Table 2. Samples/second for running SQuAD fine-tuning on NVIDIA V100 (16-GB) using PyTorch and DeepSpeed transformer kernels.

Table 3: Samples/second for running SQuAD fine-tuning on NVIDIA V100 (32-GB) using PyTorch and DeepSpeed transformer kernels.

GPUs have very high peak floating-point throughput, but the default Transformer blocks in most framework implementations are far from reaching this peak. Figure 2 shows the structure of a Transformer block with the LayerNorm placed on the input stream of the two sublayers: Attention and Feed-Forward. To approach the GPU peak performance, we employ two lines of optimizations in our own Transformer kernel implementation: advanced fusion, and invertible operators.

Figure 2: Transformer Layer with Pre-LayerNorm Architecture

We observe that transformer-based networks trigger many invocations of CUDA kernels operating in a producer-consumer fashion, adding a lot of cost for transferring data to and from global memory and overhead from kernel launching. Existing compiler-based approaches perform fine-grained fusion (e.g., fusion of element-wise operations), leading to missed fusion opportunities. In contrast, we fully exploit both fine-grain and coarse-grained fusion, tailored for Transformer blocks.

QKV and various fusions. We merge the three Query (Q), Key (K), and Value (V) weight matrices to dispatch a larger QKV GEMM to expose more parallelism and improve data locality on GPU’s shared memory and register files, as shown in Figure 3. Next, we combine the data-layout transformation of the QKV’s output matrix with the bias addition. We then partition the large QKV matrix into three transformed ones, used for the following self-attention computation.

As Figure 3 illustrates, we read the QKV matrix in consecutive rows (shown by red box), and write them in the three transformed Q, K, and V matrices. Since each matrix starts from a different offset, we may have uncoalesced access to the main memory. Thus, we use the shared memory as an intermediate buffer, in order to rearrange the data in a way that we can put the data in consecutive parts of memory. Even though we produce an uncoalesced pattern when accessing shared memory, we reduce the cost of uncoalesced access to main memory to better exploit memory bandwidth, resulting in 3% to 5% performance improvement in the end-to-end training.

Figure 3: QKV’s GEMM and transform Kernel-Fusion

We perform additional fusions such as merging the addition of bias from the attention-output GEMM with the addition from the residual connection and also dropout, which allows accesses to happen in the register files and shared memory, which are orders of magnitude faster than the expensive write-back to the global memory.

Warp-level communication. To alleviate the synchronization overhead among parallel GPU cores and further increase the resource utilization of the fused kernels, we use the warp-level (data shuffle instructions) instead of the default inter-warp communication. Take the layer-normalization and SoftMax kernel as examples, we perform each reduction operation inside a warp, while distributing different reductions across different warps. This way, we alleviate the synchronization among the parallel threads and further increase the GPU resource utilization.

Stochastic vs deterministic kernels. DL training is generally robust to some level of stochasticity, and in some cases, controlled noises such as dropouts act as regularizer which improve generalization. In designing our transformer kernel, we embrace some level of stochasticity to improve throughput by allowing for limited data race conditions to exist in the kernel: We leverage implicit warp synchronous programming to achieve higher performance for the warp-level cooperative operations [3]. The lack of explicit warp level synchronization act as non-deterministic noise without affecting the overall convergence behavior of the transformer kernels while giving a decent throughput boost.

In addition, DeepSpeed also implements a non-stochastic transformer kernel with explicit warp synchronization that produces deterministic results at the expense of a small performance regression. Users can easily choose and switch between the two versions depending on their usage scenarios: Stochastic version pursues ultimate training performance goal, and deterministic version may save development time by better facilitating experimentation and debugging.

In our experiments, we use stochastic kernels for the pre-training BERT, while using non-stochastic kernels for fine-tuning to achieve fully reproducible results. We recommend using stochastic kernels for training tasks involving massive amounts of data such as pre-training, while using non-stochastic version when training with limited data such as in the case of fine-tuning for more consistent results.

Cost-effective rematerialization. When fusing kernels of the different operations, we observe that some operators are inexpressive to compute but incur expensive data movement cost, such as addition of bias and dropout. For these operations, we avoid saving their results in the forward pass, but instead recompute them during the backward pass, which turns out to be much faster than having their results written and reloaded from the main memory.

We also observe that the intermediate activations from several operators in the Transformer blocks incur a large memory consumption, such as SoftMax and Layer Norm. For these operators, we drop the inputs to these layers to reduce the footprint of activation memory, by leveraging the fact that they are invertible functions, which are functions whose backward pass is independent of the inputs and can be formulated based only on the outputs [2]. Figure 4 and Figure 5 show the examples of the original implementation of SoftMax and Layer-Norm in PyTorch versus the invertible SoftMax implementation in DeepSpeed. Through this optimization, we are able to reduce the activation memory of the operator by half, and the reduced memory allows us to train with larger batch sizes, which once again improves GPU efficiency.

Figure 4: DeepSpeed invertible SoftMax operation versus Default PyTorch SoftMax operation

Figure 5: DeepSpeed invertible LayerNorm operation versus Default PyTorch LayerNorm operation

Beyond highly optimized transformer kernels, the BERT training has other performance limiting factors, e.g., data loading. We develop our own asynchronous worker which prefetches batches of data into a queue only at “safe points” – points when the CPUs are idle (e.g., right after asynchronously launching the forward pass). In this way, we make sure that there is no dequeuing and copying data from CPU to GPU when there is computation on the CPU side. This is different from the default PyTorch data loader, which can prefetch data at any points and cause performance interference. By using this method, we hide almost all I/O overhead, which accounts for 4% of the original training time.

We improve the end-to-end training time by 5.4% by recognizing and exploiting sparsity in BERT’s output processing. The output processing involves two steps: i) BERT projection from the hidden output dimension of the final transformer layer to the language vocabulary, using a matrix-matrix multiplication, and ii) a cross-entropy of the masked output tokens to the get each sequence’s prediction error. The cost of the first step is proportional to the vocabulary size, hidden output dimension and the sequence length, and can be as expensive as a transformer layer computation or more. However, only about 15% of the tokens are masked, and we only need the cross-entropy for the masked tokens. Therefore, the projection can be done as an efficient sparse computation. To do so, we discard the rows of the final transformer layer that corresponding to the non-masked tokens before doing the projection, reducing the computation cost of output processing by 85%.

We observe that with large batch size (e.g., 64K) the default BERT pre-training suffers from training instability, which can result in model divergence or convergence to bad/suspicious local optima. Further investigation shows that the default BERT has vanishing gradients issue. To mitigate the issue, we changed the placement of LayerNorm (Post-LayerNorm) by placing it only on the input stream of the sublayers in the Transformer block (called Pre-LayerNorm), a modification described by several recent works for neural machine translation. The Pre-LayerNorm results in several useful characteristics such as avoiding vanishing gradient, stable optimization, and performance gain. It allows us to train at aggregated batch size of 64K with increased learning rate and faster convergence.

To try out these optimizations and training recipe, please check out our BERT training tutorial and source code at the DeepSpeed GitHub repo.

[1] “NVIDIA Clocks World’s Fastest BERT Training Time and Largest Transformer Based Model, Paving Path For Advanced Conversational AI” https://devblogs.nvidia.com/training-bert-with-gpus/.

[2] S. R. Bulo, L. Porzi, and P. Kontschieder, “In-place activated batch norm for memory-optimized training of dnns” 2017. http://arxiv.org/abs/1712.02616.

[3] Mark Harris and Kyrylo Perelygin, “Cooperative Groups: Flexible CUDA Thread Programming”, https://devblogs.nvidia.com/cooperative-groups/.

Updated: May 27, 2020

---

## Training a Trillion Parameters with Pipeline Parallelism

**URL:** https://www.deepspeed.ai/2020/09/08/pipeline-parallelism.html

**Contents:**
- Training a Trillion Parameters with Pipeline Parallelism
    - Contents

DeepSpeed includes new support for pipeline parallelism! DeepSpeed’s training engine provides hybrid 3D parallelism for training models with over a trillion parameters. In addition to scaling to the extreme, we have demonstrated that hybrid parallelism accelerates training on clusters with low-bandwidth network by up to 7x.

Updated: September 8, 2020

---

## Turing-NLG: A 17-billion-parameter language model by Microsoft

**URL:** https://www.deepspeed.ai/2020/02/13/turing-nlg.html

**Contents:**
- Turing-NLG: A 17-billion-parameter language model by Microsoft
    - Contents

Updated: February 13, 2020

---

## Up to 5x less communication and 3.4x faster training through 1-bit Adam

**URL:** https://www.deepspeed.ai/2020/09/08/onebit-adam-news.html

**Contents:**
- Up to 5x less communication and 3.4x faster training through 1-bit Adam

Adam is an effective and probably the most well-utilized optimizer for training many large-scale deep learning models. However, Adam is generally not compatible with communication-efficient optimization algorithms, and therefore the communication cost could become a bottleneck while scaling across distributed devices. We introduce a new algorithm - 1-bit Adam - and its efficient implementation in DeepSpeed. 1-bit Adam offers the same convergence as Adam, incurs up to 5x less communication that enables up to 3.5x higher throughput for BERT-Large pretraining and up to 2.7x higher throughput for SQuAD fine-tuning on bandwidth-limited clusters.

Updated: September 8, 2020

---

## DeepSpeed Sparse Attention

**URL:** https://www.deepspeed.ai/2020/09/08/sparse-attention.html

**Contents:**
- DeepSpeed Sparse Attention
    - Contents
- Performance Results

Attention-based deep learning models such as the transformers are highly effective in capturing the relationship between tokens in an input sequence, even across long distances. As a result, they are used with text, image, and sound-based inputs, where the sequence length can be in thousands of tokens. However, despite the effectiveness of attention modules to capture long term dependencies, in practice, their application to long sequence input is limited by compute and memory requirements of the attention computation that grow quadratically, O(n^2), with the sequence length n.

To address this limitation, DeepSpeed offers a suite of sparse attention kernels –an instrumental technology that can reduce the compute and memory requirement of attention computation by orders-of-magnitude via block-sparse computation. The suite not only alleviates the memory bottleneck of attention calculation, but also performs sparse computation efficiently. Its APIs allow convenient integration with any transformer-based models. Along with providing a wide spectrum of sparsity structures, it has the flexibility of handling any user-defined block-sparse structures. More specifically, sparse attention (SA) can be designed to compute local attention between nearby tokens, or global attention via summary tokens computed with local attention. Moreover, SA can also allow random attention, or any combination of local, global, and random attention as shown in the following figure with blue, orange, and green blocks, respectively. As a result, SA decreases the memory footprint to O(wn), in which 1 < w < n is a parameter, whose value depends on the attention structure.

This library is PyTorch based and develops required kernels through Triton platform; kernels are not written in CUDA, which leaves the door open for CPU/OpenCL/Vulkan support in the future. The library is an extension to DeepSpeed and can be used through DeepSpeed as well as stand alone. Block-sparse computations handled by DeepSpeed Sparse Attention kernels are illustrated in following figures for forward and backward passes respectively. In the figures, S stands for a block-sparse matrix and D a dense matrix.

To learn more about Sparsity Config, and also how to use this library, please check our tutorial that provides detailed information about it.

We also define a template to have variable structure (top figure), which can be used to simply customize any block-sparse random/local/global attention pattern. In addition to this list, user can add any other sparsity structure as described in tutorial section.

Updated: September 8, 2020

---

## The Fastest and Most Efficient BERT Training through Optimized Transformer Kernels

**URL:** https://www.deepspeed.ai/2020/05/18/bert-record.html

**Contents:**
- The Fastest and Most Efficient BERT Training through Optimized Transformer Kernels

We introduce new technology to accelerate single GPU performance via kernel optimizations. These optimizations not only create a strong foundation for scaling out large models, but also improve the single GPU performance of highly tuned and moderately sized models like BERT by more than 30%, reaching a staggering performance of 66 teraflops per V100 GPU, which is 52% of the hardware peak. Using optimized transformer kernels as the building block, DeepSpeed achieves the fastest BERT training record: 44 minutes on 1,024 NVIDIA V100 GPUs, compared with the best published result of 67 minutes on the same number and generation of GPUs.

Updated: May 18, 2020

---

## DeepSpeed Microsoft Research Webinar on August 6th, 2020

**URL:** https://www.deepspeed.ai/2020/07/23/deepspeed-webinar.html

**Contents:**
- DeepSpeed Microsoft Research Webinar on August 6th, 2020
    - Contents

Updated: July 23, 2020

---

## DeepSpeed with 1-bit Adam: 5x less communication and 3.4x faster training

**URL:** https://www.deepspeed.ai/2020/09/08/onebit-adam-blog-post.html

**Contents:**
- DeepSpeed with 1-bit Adam: 5x less communication and 3.4x faster training
    - Contents
- 1. Introduction
  - 1.1 Background: Classic compression techniques
  - 1.2 Challenges in applying error-compensation to Adam
- 2. Compressing communication with 1-bit Adam
  - 2.1 How 1-bit Adam works under the hood
  - 2.2 Addressing system challenges for 1-bit Adam
- 3. Benefits of 1-bit Adam on communication-constrained systems
- 4. Dive deeper into 1-bit Adam evaluation results

Scalable training of large models (like BERT and GPT-3) requires careful optimization rooted in model design, architecture, and system capabilities. From a system standpoint, communication has become a major bottleneck, especially on commodity systems with standard TCP interconnects that offer limited network bandwidth. Communication compression is an important technique to reduce training time on such systems. One of the most effective ways to compress communication is via error compensation compression, which offers robust convergence speed, even under 1-bit compression. However, state-of-the-art error compensation techniques only work with basic optimizers like Stochastic Gradient Descent (SGD) and momentum SGD, which are linearly dependent on the gradients. They do not work with non-linear gradient-based optimizers like Adam, which offers state-of-the-art convergence efficiency and accuracy for many tasks, including training of BERT-like models. For a powerful optimizer like ADAM, the non-linear dependency on gradient (in the variance term) makes it challenging to develop error compensation-based compression techniques, limiting the practical value of the state-of-the-art communication compression techniques.

One way of communication compression is 1-bit compression, which can be expressed as:

With this compression, we could achieve a 32x reduction of memory size by representing each number using one bit. The problem is that using this straightforward method would significantly degrade the convergence speed, which makes this method inapplicable. To solve this problem, recent studies show that by using error compensation compression, we could expect almost the same convergence rate with communication compression. The idea of error compensation can be summarized as: 1) doing compression, 2) memorizing the compression error, and then 3) adding the compression error back in during the next iteration. For SGD, doing error compression leads to:

Where C(⋅) is the 1-bit compression operator. The good thing about doing this error compensation is that the history compression error (e_t and e_(t-1)) would be canceled by itself eventually, which can be seen by:

This strategy has been proven to work for optimization algorithms that are linearly dependent on the gradient, such as SGD and Momentum SGD.

We provide an overview of the Adam algorithm below. The update rules are as follows.

As shown in the equations above, the variance term v_t is nonlinearly dependent on the gradient g_t. If we apply basic error compensation compression to Adam, we observe that Adam will not converge as shown in Figure 1.

Figure 1: Inapplicability of Error-compensation Compression for Adam due to non-linear dependence on the gradient

To compress communication while using the Adam optimizer, we develop 1-bit Adam, which addresses the non-linearity in gradients via preconditioning. We observe that the magnitude of changes on the non-linear term, variance ( v_t), decrease significantly after a few epochs of training and setting v_t constant afterwards will not change the convergence speed. The proposed 1-bit Adam optimizer, as shown in Figure 2, consists of two parts: the warmup stage, which is essentially the vanilla Adam algorithm; and the compression stage, which keeps the variance term constant and compresses the remaining linear term, that is the momentum, into 1-bit representation.

The compression stage of the algorithm is controlled by a threshold parameter (as shown in Figure 2). When we detect that the change in “variance” falls below a certain threshold, we switch to the compression stage. Our study shows that only 15-20% of the overall training steps are needed for the warmup stage.

Figure 2: Comparison of distributed training steps in classic Adam and the proposed 1-bit compressed Adam algorithm

The weight update rule for 1-bit Adam is governed by the following equations.

For the i-th worker, in the compression stage:

Where x_t is the model after iteration (t-1), m_t^(i), e_t^(i) are the momentum and compression error on worker i after iteration (t-1), and v_warmup is the variance term after the warmup stage.

Besides the algorithmic challenge, there are two system challenges in applying 1-bit Adam in training systems. First, we need efficient kernels that convert the momentum to 1-bit representations. Second, we need efficient communication schemes to exchange this compressed momentum across different GPUs. The goal of compression is to reduce the overall training time so that commodity systems with bandwidth-limited interconnects can be used to train large models. We address these challenges in DeepSpeed and introduce a fully optimized 1-bit Adam implementation for training on communication-constrained systems.

1-bit Adam offers the same convergence as Adam, incurs up to 5x less communication that enables up to 3.5x higher throughput for BERT-Large pretraining and up to 2.7x higher throughput for SQuAD fine-tuning. This end-to-end throughput improvement is enabled by the 6.6x (Figure 3) and 6.2x (Figure 4) speedup observed during the compression stage. It is worth mentioning that our 1-bit Adam optimizer scales so well on a 40 Gigabit Ethernet system that its performance is comparable to Adam’s scalability on a 40 Gigabit InfiniBand QDR system. We note that the effective bandwidth on 40 Gigabit Ethernet is 4.1 Gbps based on iperf benchmarks whereas InfiniBand provides near-peak bandwidth of 32Gbps based on InfiniBand perftest microbenchmarks.

Figure 3: Scalability of 1-bit Adam for BERT-Large Pretraining on V100 GPUs with batch size of 16/GPU.

Figure 4: Scalability of 1-bit Adam for SQuAD Finetuning on V100 GPUs with batch size of 3/GPU.

One major question for using 1-bit Adam is the convergence speed, and we find that 1-bit Adam can achieve the same convergence speed and comparable testing performance using the same number of training samples as shown in Figure 5.

Figure 5: 1-bit Adam converges like Adam using the same number of training samples.

Detailed BERT-Base and BERT-Large results are shown in Table 1. We see that the scores are on par with or better than the original model for both the uncompressed and compressed cases.

Table 1: Verifying correctness of 1-bit Adam on various testing tasks

Up to 5x less communication: 1-bit Adam provides the same convergence as Adam and reduces the communication volume by 16x during the compression stage for 16-bit (FP16) training. For BERT pretraining, this leads to an overall communication reduction of 5x as we observed the warmup stage to be just 15% of the end-to-end training time.

The formula to calculate the communication volume ratio of the original versus 1-bit Adam is as follows:

In the case of warmup equaling 15%, original Adam incurs 5x of the communication as 1-bit Adam.

We present two main results for training BERT-Large on systems with two different bandwidth-limited interconnects: 1) 40 gigabit Ethernet (Figure 5) and 2) 40 gbps InfiniBand QDR (Figure 6). During the compression phase, we observe up to 6.6x higher throughput on the system with Ethernet and up to 2x higher throughput on the system with InfiniBand, resulting in end-to-end speed up (including both warmup and compression stages) of 3.5x and 2.7x, respectively. The major benefit of 1-bit Adam comes from the communication volume reduction—enabled by our compressed momentum exchange—and from our custom allreduce operation that implements efficient 1-bit communication using non-blocking gather operations followed by an allgather operation.

It is important to note that one can also increase total batch size to reduce communication using optimizers like LAMB instead of Adam for BERT pretraining. However, 1-bit Adam avoids the need for rigorous hyperparameter tuning, which is often more difficult for large batches from our experience. Furthermore, 1-bit Adam also works very well for workloads that have small critical batch size (cannot converge well with large batch size) like many fine-tuning tasks.

Figure 5: Performance of 1-bit Adam for BERT-Large training on 40 Gbps Ethernet interconnect during the compression stage.

Figure 6: Performance of 1-bit Adam for BERT-Large training on 40 Gbps InfiniBand interconnect during the compression stage.

1-bit Adam offers scalability not only on large-scale training tasks but also on tasks like SQuAD fine-tuning. As shown in Figures 7 and 8, 1-bit Adam scales well on both Ethernet- and InfiniBand-based systems and offers up to 6.2x higher throughput (during the compression stage) on the Ethernet-based system, resulting in 2.7x end-to-end speedup (25% warmup plus 75% compression stage). For SQuAD fine-tuning, we observed that a total batch size of 96 offers the best F1 score. Batch sizes larger than this value lower the convergence rate and require additional hyperparameter tuning. Therefore, in order to scale to 32 GPUs, we can only apply a small batch size of 3-4 per GPU. This makes fine-tuning tasks communication intensive and hard to scale. 1-bit Adam addresses the scaling challenge well, obtaining 3.4x communication reduction without enlarging batch size, and it results in a 2.7x end-to-end speedup.

Figure 7: Performance of 1-bit Adam for SQuAD fine-tuning on 40 gbps Ethernet during the compression stage.

Figure 8: Performance of 1-bit Adam for SQuAD fine-tuning on 40 gbps InfiniBand interconnect during the compression stage.

Updated: September 8, 2020

**Examples:**

Example 1 (unknown):
```unknown
1 / (warmup + (1 – warmup)/16)
```

---




### Mii

# Deepspeed - Mii

**Pages:** 1

---

## DeepSpeed-MII: instant speedup on 24,000+ open-source DL models with up to 40x cheaper inference

**URL:** https://www.deepspeed.ai/2022/10/10/mii.html

**Contents:**
- DeepSpeed-MII: instant speedup on 24,000+ open-source DL models with up to 40x cheaper inference
    - Contents
- How does MII work?
- Supported Models and Tasks
- Inference Optimizations with MII
- MII-Public and MII-Azure
- Quantifying Latency and Cost Reduction
- Latency Critical Scenarios
- Cost Sensitive Scenarios
- Deployment Options

The Deep Learning (DL) open-source community has seen tremendous growth in the last few months. Incredibly powerful text generation models such as the Bloom 176B, or image generation models such as Stable Diffusion are now available to anyone with access to a handful or even a single GPU through platforms such as Hugging Face. While open-sourcing has democratized access to AI capabilities, their application is still restricted by two critical factors: 1) inference latency and 2) cost.

There has been significant progress in system optimizations for DL model inference that can drastically reduce both latency and cost, but those are not easily accessible. The main reason for this limited accessibility is that the DL model inference landscape is diverse with models varying in size, architecture, system performance characteristics, hardware requirements, etc. Identifying the appropriate set of system optimizations applicable to a given model and applying them correctly is often beyond the scope of most data scientists, making low latency and low-cost inference mostly inaccessible.

DeepSpeed Model Implementations for Inference (MII) is a new open-source python library from DeepSpeed, aimed towards making low-latency, low-cost inference of powerful models not only feasible but also easily accessible.

Figure 1: MII Architecture, showing how MII automatically optimizes OSS models using DS-Inference before deploying them on-premises using GRPC, or on Microsoft Azure using AML Inference.

Under-the-hood MII is powered by DeepSpeed-Inference. Based on the model type, model size, batch size, and available hardware resources, MII automatically applies the appropriate set of system optimizations from DeepSpeed-Inference to minimize latency and maximize throughput. It does so by using one of many pre-specified model injection policies, that allows MII and DeepSpeed-Inference to identify the underlying PyTorch model architecture and replace it with an optimized implementation (see Figure 1). In doing so, MII makes the expansive set of optimizations in DeepSpeed-Inference automatically available for thousands of popular models that it supports.

MII supports a growing list of tasks such as text generation, question-answering, text classification, etc, across thousands of transformer models available through multiple open-sourced model repositories such as Hugging Face, FairSeq, EluetherAI, etc. It supports dense models based on BERT, RoBERTa, GPT, OPT, and BLOOM architectures ranging from a few hundred million parameters in size to hundreds of billions of parameters in size. At the same time, it supports recent image generation models such as Stable Diffusion.

See the MII GitHub repo for an up-to-date list of models and tasks supported by MII.

Here we provide a summary of the expansive set of optimizations from DeepSpeed-inference made available via MII. For more details, please refer to [1, 2]:

DeepFusion for Transformers: For transformer-based models such as Bert, Roberta, GPT-2, and GPT-J, MII leverages the transformer kernels in DeepSpeed-Inference that are optimized to achieve low latency at small batch sizes and high throughput at large batch sizes using DeepFusion.

Multi-GPU Inference with Tensor-Slicing: For massive models such as Bloom 176B, MII automatically enables tensor-parallelism within a node to leverage aggregate memory bandwidth and compute across multiple GPUs to achieve the lowest latency and throughput compared to anything else that is currently available.

INT8 Inference with ZeroQuant: For massive models with tens or hundreds of billions of parameters, MII supports INT8 Inference with ZeroQuant. Using this feature not only reduces the memory footprint and the number of GPUs required for inference but also increases the inference throughput by supporting larger batch sizes and using INT8 compute, thus lowering cost compared to FP16.

ZeRO-Inference for Resource Constrained Systems: Models such as Bloom 176B, require over 176 GB of memory to just fit the model even with INT8 support. In the absence of the aggregate GPU memory across multiple GPUs required to deploy such models, MII enables ZeRO-Inference that can leverage the system CPU memory to deploy these massive models with a single GPU with limited memory.

Compiler Optimizations: When applicable, MII automatically applies compiler-based optimizations via TorchScript, nvFuser, and CUDA graph, in addition to the above optimizations, to further lower latency and improve throughput.

MII can work with two variations of DeepSpeed-Inference. The first, referred to as ds-public, contains most of the optimizations discussed above and is also available via our open-source DeepSpeed library. The second referred to as ds-azure, offers tighter integration with Azure, and is available via MII to all Microsoft Azure customers. We refer to MII running the two DeepSpeed-Inference variants as MII-Public and MII-Azure, respectively.

Both MII-Public and MII-Azure offer significant latency and cost reduction compared to open-sourced PyTorch implementation (Baseline). However for certain generative workloads, they can have differentiated performance: MII-Azure provides further improvements beyond MII-Public. We quantify the latency and cost reduction for both variations in the next section.

Inference workloads can be either latency critical, where the primary objective is to minimize latency, or cost sensitive, where the primary objective is to minimize cost. In this section, we quantify the benefits of using MII for both latency-critical and cost-sensitive scenarios.

For latency-critical scenarios, where a small batch size of 1 is often used, MII can reduce the latency by up to 6x for a wide range of open-source models, across multiple tasks. More specifically, we show model latency reduction of 1:

Up to 5.7x for multi-GPU inference for text generation using massive models such as Big Science Bloom, Facebook OPT, and EluetherAI NeoX (Figure 2 (left))

Up to 1.9x for image generation tasks model using Stable Diffusion (Figure 2 (right))

Up to 3x for relatively smaller text generation models (up to 7B parameters) based on OPT, BLOOM, and GPT architectures, running on a single GPU (Figures 3 and 4)

Up to 9x for various text representation tasks like fill-mask, text classification, question answering, and token classification using RoBERTa- and BERT- based models (Figures 5 and 6).

Figure 2: (left) Best achievable latency for large models. MII-Azure (int8) offers 5.7X lower latency compared to Baseline for Bloom-176B. (right) Stable Diffusion text to image generation latency comparison.

Figure 3: Latency comparison for OPT and BLOOM models. MII-Azure is up to 2.8x faster than baseline.

Figure 4: Latency comparison for GPT models. MII-Azure is up to 3x faster than baseline.

Figure 5: Latency comparison for RoBERTa models. MII offers up to 9x lower model latency and up to 3x lower end-to-end latency than baseline on several tasks and RoBERTa variants 1.

Figure 6: Latency comparison for BERT models. MII offers up to 8.9x lower model latency and up to 4.5x end-to-end latency across several tasks and BERT variants1.

MII can significantly reduce the inference cost of very expensive language models like Bloom, OPT, etc. To get the lowest cost, we use a large batch size that maximizes throughput for both baseline and MII. Here we look at the cost reduction from MII using two different metrics: i) tokens generated per second per GPU, and ii) dollars per million tokens generated.

Figures 7 and 8 show that MII-Public offers over 10x throughput improvement and cost reduction compared to the baseline, respectively. Furthermore, MII-Azure offers over 30x improvement in throughput and cost compared to the baseline.

Figure 7: Throughput comparison per A100-80GB GPU for large models. MII-Public offers over 15x throughput improvement while MII-Azure offers over 40x throughput improvement.

Figure 8: Cost of generating 1 million tokens on Azure with different model types. MII-Azure reduces the cost of generation by over 40x.

MII supported models can be deployed in two different ways as shown in Figure 1 with just a few lines of code.

MII-Public can be deployed on-premises or on any cloud offering. MII creates a lightweight GRPC server to support this form of deployment and provides a GRPC inference endpoint for queries. The code below shows how a supported model can be deployed with MII-Public Deployment.

MII supports deployment on Azure via AML Inference. To enable this, MII generates AML deployment assets for a given model that can be deployed using the Azure-CLI, as shown in the code below. Furthermore, deploying on Azure, allows MII to leverage DeepSpeed-Azure as its optimization backend, which offers better latency and cost reduction than DeepSpeed-Public.

To learn more about these deployment options and get started with MII, please the MII getting started guide.

We are very excited to share MII with the community and improve it with your feedback. We will continue to add support for more models in MII as well as enhance both MII-Public and MII-Azure for both on-premise and Azure users. Our hope is that while open sourcing has made powerful AI capabilities accessible to many, MII will allow for a wider infusion of these capabilities into a diverse set of applications and product offerings by instantly reducing the latency and cost of inferencing.

The table below shows the mapping between model aliases used in Figures 3, 4, 5, and 6 and real model names.

The end-to-end latency of an inference workload is comprised of two components: i) actual model execution, and ii) pre-/post-processing before and after the model execution. MII optimizes the actual model execution but leaves the pre-/post-processing pipeline for future optimizations. We notice that text representation tasks have significant pre-/post-processing overhead (Figures G and H). We plan to address those in a future update. ↩ ↩2 ↩3

Updated: October 10, 2022

**Examples:**

Example 1 (unknown):
```unknown
import mii
mii.deploy(task="text-to-image",
           model="CompVis/stable-diffusion-v1-4",
           deployment_name="sd-deployment")
```

Example 2 (unknown):
```unknown
import mii
mii.deploy(task="text-to-image",
           model="CompVis/stable-diffusion-v1-4",
           deployment_name="sd-deployment",
           deployment_type=DeploymentType.AML)
```

---




### 2023

# Deepspeed - 2023

**Pages:** 21

---

## DeepSpeed-VisualChat: Improve Your Chat Experience with Multi-Round Multi-Image Inputs

**URL:** https://www.deepspeed.ai/2023/10/03/deepspeed-visualchat.html

**Contents:**
- DeepSpeed-VisualChat: Improve Your Chat Experience with Multi-Round Multi-Image Inputs
    - Contents

Updated: October 3, 2023

---

## DeepSpeed4Science：利用先进的AI系统优化技术实现科学发现

**URL:** https://www.deepspeed.ai/2023/09/18/deepspeed4science-chinese.html

**Contents:**
- DeepSpeed4Science：利用先进的AI系统优化技术实现科学发现
    - Contents

Updated: September 18, 2023

---

## DeepSpeed Ulysses: System Optimizations for Enabling Training of Extreme Long Sequence Transformer Models

**URL:** https://www.deepspeed.ai/2023/08/23/ulysses.html

**Contents:**
- DeepSpeed Ulysses: System Optimizations for Enabling Training of Extreme Long Sequence Transformer Models
    - Contents

Updated: August 23, 2023

---

## DeepSpeed Ulysses: 训练极长序列Transformer模型的系统优化

**URL:** https://www.deepspeed.ai/2023/08/23/ulysses-chinese.html

**Contents:**
- DeepSpeed Ulysses: 训练极长序列Transformer模型的系统优化
    - Contents

Updated: August 23, 2023

---

## DeepSpeed Chat: 一键式RLHF训练，让你的类ChatGPT千亿大模型提速省钱15倍

**URL:** https://www.deepspeed.ai/2023/04/23/deepspeed-chat-chinese.html

**Contents:**
- DeepSpeed Chat: 一键式RLHF训练，让你的类ChatGPT千亿大模型提速省钱15倍
    - Contents

Updated: April 23, 2023

---

## DeepSpeed ZeRO++: LLMやチャットモデルの訓練を劇的に高速化 – 通信オーバヘッドを1/4に大幅削減 -

**URL:** https://www.deepspeed.ai/2023/06/21/zeropp-japanese.html

**Contents:**
- DeepSpeed ZeRO++: LLMやチャットモデルの訓練を劇的に高速化 – 通信オーバヘッドを1/4に大幅削減 -
    - Contents

Updated: June 21, 2023

---

## DeepSpeed-FastGen: High-throughput Text Generation for LLMs via MII and DeepSpeed-Inference

**URL:** https://www.deepspeed.ai/2023/11/05/deepspeed-fastgen.html

**Contents:**
- DeepSpeed-FastGen: High-throughput Text Generation for LLMs via MII and DeepSpeed-Inference
    - Contents

Updated: November 5, 2023

---

## DeepSpeed-VisualChat: 複数ラウンド・複数画像の入力が可能なAIチャット体験を実現

**URL:** https://www.deepspeed.ai/2023/10/03/deepspeed-visualchat-japanese.html

**Contents:**
- DeepSpeed-VisualChat: 複数ラウンド・複数画像の入力が可能なAIチャット体験を実現
    - Contents

Updated: October 3, 2023

---

## DeepSpeed-FastGen: MIIとDeepSpeed-InferenceによるLLMのための高速なテキスト生成

**URL:** https://www.deepspeed.ai/2023/11/05/deepspeed-fastgen-japanese.html

**Contents:**
- DeepSpeed-FastGen: MIIとDeepSpeed-InferenceによるLLMのための高速なテキスト生成
    - Contents

Updated: November 5, 2023

---

## Zero Inference

**URL:** https://www.deepspeed.ai/2023/09/12/ZeRO-Inference.html

**Contents:**
- Zero Inference
    - Contents

title: “ZeRO-Inference: 20X faster inference through weight quantization and KV cache offloading” excerpt: “” link: https://github.com/deepspeedai/DeepSpeedExamples/blob/master/inference/huggingface/zero_inference/README.md date: 2023-09-12 00:09:00 tags: inference ZeRO quantization English —

Updated: September 12, 2023

---

## DeepSpeed Ulysses: Transformerモデルを非常に長いシーケンスで訓練するための最適化

**URL:** https://www.deepspeed.ai/2023/08/23/ulysses-japanese.html

**Contents:**
- DeepSpeed Ulysses: Transformerモデルを非常に長いシーケンスで訓練するための最適化
    - Contents

Updated: August 23, 2023

---

## DeepSpeed-VisualChat：多轮图像+文字，为你展现不一样的AI聊天魅力

**URL:** https://www.deepspeed.ai/2023/10/03/deepspeed-visualchat-chinese.html

**Contents:**
- DeepSpeed-VisualChat：多轮图像+文字，为你展现不一样的AI聊天魅力
    - Contents

Updated: October 3, 2023

---

## DeepSpeed ZeRO++: A leap in speed for LLM and chat model training with 4X less communication

**URL:** https://www.deepspeed.ai/2023/06/21/zeropp.html

**Contents:**
- DeepSpeed ZeRO++: A leap in speed for LLM and chat model training with 4X less communication
    - Contents

Updated: June 21, 2023

---

## Announcing the DeepSpeed4Science Initiative: Enabling large-scale scientific discovery through sophisticated AI system technologies

**URL:** https://www.deepspeed.ai/2023/09/18/deepspeed4science.html

**Contents:**
- Announcing the DeepSpeed4Science Initiative: Enabling large-scale scientific discovery through sophisticated AI system technologies
    - Contents

Updated: September 18, 2023

---

## Scaling Large-Scale Generative Mixture-of-Expert Multimodal Model With VL-MoE

**URL:** https://www.deepspeed.ai/2023/03/30/multi-modal.html

**Contents:**
- Scaling Large-Scale Generative Mixture-of-Expert Multimodal Model With VL-MoE
    - Contents

The field of Artificial Intelligence-Generated Content (AIGC) is rapidly growing, with the goal of making content creation more efficient and accessible. One of the most exciting areas of AIGC is the development of large-scale multi-modal models like Flamingo, BLIP, and GPT4, which can accept inputs from multiple resources, e.g., image, text, audio, etc., and generate a variety of formats as outputs. For example, image creation can be made through stable diffusion and DALLE using the prompt text, and the new feature in the coming Office can create slides with texts, images, animations, etc., by leveraging the power of the new Microsoft Office Copilot.

Scaling up the model size is one common approach to boost usability and capability of AIGC tasks. However, simply scaling up dense architectures (e.g., from GPT-1 to GPT-3) is usually extremely resource-intensive and time-consuming for both model training and inference. One effective way to tackle this challenge is to apply mixture of experts (MoE). In particular, recent text-based MoE and vision-based MoE studies have demonstrated that MoE models can significantly reduce the training and resource cost as compared to a quality-equivalent dense model, or produce a higher quality model under the same training budget. Up to now, the effectiveness of jointly training MoE for multi-modal models remains not well understood. To explore this important capability, DeepSpeed team is proud to announce our first large-scale generative mixture-of-expert (MoE) multimodal model, named VL-MoE.

Figure 1: The new encoding process in our VL-MoE for various modality inputs, for which gray and colored blocks indicate non-activated and activated modules, respectively.

Specifically, we incorporate the MoE structure into the classical single-tower multi-modal model by comprising of the following components: (1) a shared self-attention module across modalities, (2) a pool of modality-specific experts in the feed-forward network (FFN), and (3) a sparse gated MoE extended from the dense FFN. Subsequently, under the same amount of training resources as that used in VLMO (200k training steps), we demonstrate VL-MoE’s advantages over the state-of-the-art dense counterparts in the following two aspects:

(1) VL-MoE can achieve significant accuracy improvement in comparison to its dense counterparts. Table 1 demonstrates that under the same training budget (i.e., have the same number of activated parameters for each token), VL-MoE Base with 32 experts achieves better accuracy than the VLMO-Base dense model on all four vision-language datasets.

(2) VL-MoE achieves similar model quality with a much smaller activated number of parameters compared to its dense counterparts. Our results show that the finetuning performance of our VL-MoE is similar to that of the 3.1X larger VLMO-Large dense model (i.e., 3.1X more activated number of parameters per token). This can directly translate to approximately 3.1X training cost reduction as the training FLOPs for transformers are proportional to the activated model size per token.

Table 1: Comparison of finetuning accuracy results for different models used in vision-language classification tasks and image-text retrieval tasks.

A sophisticated MoE model design requires a highly efficient and scalable training system that can support multi-dimensional parallelism and efficient memory management. DeepSpeed MoE training system offers such advanced capabilities including easy-to-use APIs enabling flexible combinations of data, tensor, and expert parallelism. Furthermore, DeepSpeed MoE enables larger model scale than state-of-the-art systems by exploiting expert parallelism and ZeRO optimizations together. By leveraging the DeepSpeed MoE system, VL-MoE Base with 32 experts achieves similar model quality as VLMO-dense Large with about 2.5x training speedup.

DeepSpeed MoE system is already open-sourced and can be easily used as plug-and-play component to achieve high-performance low-cost training for any large-scale MoE models. The tutorial of how to use DeepSpeed MoE is available here. VL-MoE is currently in the process of being integrated as a model example of DeepSpeed Examples. Please stay tuned for our upcoming updates on this thread.

Updated: March 30, 2023

---

## DeepSpeed-FastGen：通过 MII 和 DeepSpeed-Inference 实现 LLM 高吞吐量文本生成

**URL:** https://www.deepspeed.ai/2023/11/05/deepspeed-fastgen-chinese.html

**Contents:**
- DeepSpeed-FastGen：通过 MII 和 DeepSpeed-Inference 实现 LLM 高吞吐量文本生成
    - Contents

Updated: November 5, 2023

---

## DeepSpeed4Scienceイニシアティブ: 洗練されたAIシステムのテクノロジーにより大規模な科学的発見を可能に

**URL:** https://www.deepspeed.ai/2023/09/18/deepspeed4science-japanese.html

**Contents:**
- DeepSpeed4Scienceイニシアティブ: 洗練されたAIシステムのテクノロジーにより大規模な科学的発見を可能に
    - Contents

Updated: September 18, 2023

---

## DeepSpeed Chat: Easy, Fast and Affordable RLHF Training of ChatGPT-like Models at All Scales

**URL:** https://www.deepspeed.ai/2023/04/23/deepspeed-chat.html

**Contents:**
- DeepSpeed Chat: Easy, Fast and Affordable RLHF Training of ChatGPT-like Models at All Scales
    - Contents

Updated: April 23, 2023

---

## DeepSpeed ZeRO++：降低4倍网络通信，显著提高大模型及类ChatGPT模型训练效率

**URL:** https://www.deepspeed.ai/2023/06/21/zeropp-chinese.html

**Contents:**
- DeepSpeed ZeRO++：降低4倍网络通信，显著提高大模型及类ChatGPT模型训练效率
    - Contents

Updated: June 21, 2023

---

## DeepSpeed主要技術の概要紹介

**URL:** https://www.deepspeed.ai/2023/06/06/deepspeed-overview-japanese.html

**Contents:**
- DeepSpeed主要技術の概要紹介
    - Contents

我々が研究開発しているDeepSpeedについて、主要技術を日本語で説明した資料を公開しました。GPT3やChatGPTのような生成型AIのための大規模言語モデルを含む、様々な深層学習の訓練や推論に容易に適用でき、モデルの大規模化、高速化、コスト削減を可能にします。こちらよりダウンロードしてください。

Updated: June 6, 2023

---

## DeepSpeed Chat: ChatGPTライクなモデルを簡単・高速・低コストに、あらゆるスケールで学習

**URL:** https://www.deepspeed.ai/2023/04/23/deepspeed-chat-japanese.html

**Contents:**
- DeepSpeed Chat: ChatGPTライクなモデルを簡単・高速・低コストに、あらゆるスケールで学習
    - Contents

Updated: April 23, 2023

---




### 08

# Deepspeed - 08

**Pages:** 1

---

## DeepSpeed powers 8x larger MoE model training with high performance

**URL:** https://www.deepspeed.ai/2021/08/17/deepspeed-moe.html

**Contents:**
- DeepSpeed powers 8x larger MoE model training with high performance
    - Contents

Updated: August 17, 2021

---




### Assets

# Deepspeed - Assets

**Pages:** 29

---

## 

**URL:** https://www.deepspeed.ai/assets/images/zero1_dp8_1.5B_log.png

---

## 

**URL:** https://www.deepspeed.ai/assets/images/mii/bert.png

---

## 

**URL:** https://www.deepspeed.ai/assets/files/DeepSpeed_Overview_Japanese_2023Jun7th.pdf

---

## 

**URL:** https://www.deepspeed.ai/assets/images/zero_offload_dp1_10B_smi.png

---

## 

**URL:** https://www.deepspeed.ai/assets/images/zero3-offload-512-v100.png

---

## 

**URL:** https://www.deepspeed.ai/assets/images/data_efficiency/data_efficiecy_fig1.png

---

## 

**URL:** https://www.deepspeed.ai/assets/images/zeropp/ZeRO-baseline.png

---

## 

**URL:** https://www.deepspeed.ai/assets/images/mii/azure-cost.png

---

## 

**URL:** https://www.deepspeed.ai/assets/images/data_efficiency/data_efficiecy_fig0.png

---

## 

**URL:** https://www.deepspeed.ai/assets/images/zero3-offload-200B-scalability.png

---

## 

**URL:** https://www.deepspeed.ai/assets/images/mii/hero.png

---

## 

**URL:** https://www.deepspeed.ai/assets/images/zero_offload_dp1_10B_log.png

---

## 

**URL:** https://www.deepspeed.ai/assets/images/zero2_dp32_10B_smi.png

---

## 

**URL:** https://www.deepspeed.ai/assets/images/data_efficiency/data_efficiecy_fig3.png

---

## 

**URL:** https://www.deepspeed.ai/assets/images/mii/roberta.png

---

## 

**URL:** https://www.deepspeed.ai/assets/images/zero_offload_dp1_10B_cpu.png

---

## 

**URL:** https://www.deepspeed.ai/assets/images/oom_dp8_1.5B_log.png

---

## 

**URL:** https://www.deepspeed.ai/assets/images/data_efficiency/data_efficiecy_fig2.png

---

## 

**URL:** https://www.deepspeed.ai/assets/images/vl_moe.png

---

## 

**URL:** https://www.deepspeed.ai/assets/images/zero3-offload-1-v100.png

---

## 

**URL:** https://www.deepspeed.ai/assets/images/zero3-offload-memory-overview.png

---

## 

**URL:** https://www.deepspeed.ai/assets/images/mii/opt-bloom.png

---

## 

**URL:** https://www.deepspeed.ai/assets/images/zero1_dp8_1.5B_smi.png

---

## 

**URL:** https://www.deepspeed.ai/assets/images/mii/tput-llms.png

---

## 

**URL:** https://www.deepspeed.ai/assets/images/mii/llm-latency-sd-latency-zoom.png

---

## 

**URL:** https://www.deepspeed.ai/assets/images/mii/gpt.png

---

## 

**URL:** https://www.deepspeed.ai/assets/images/zeropp/ZeROpp.png

---

## 

**URL:** https://www.deepspeed.ai/assets/images/mii/mii-arch.png

---

## 

**URL:** https://www.deepspeed.ai/assets/images/zero2_dp32_10B_log.png

---




### Other

# Deepspeed - Other

**Pages:** 15

---

## Training Overview and Features

**URL:** https://www.deepspeed.ai/training/

**Contents:**
- Training Overview and Features
    - Contents
- Overview
- Distributed, Effective, and Efficient Training with Ease
- Speed
- Memory efficiency
- Scalability
- Communication efficiency
- Data efficiency
- Supporting long sequence length

Training advanced deep learning models is challenging. Beyond model design, model scientists also need to set up the state-of-the-art training techniques such as distributed training, mixed precision, gradient accumulation, and checkpointing. Yet still, scientists may not achieve the desired system performance and convergence rate. Large model sizes are even more challenging: a large model easily runs out of memory with pure data parallelism and it is difficult to use model parallelism. DeepSpeed addresses these challenges to accelerate model development and training.

The DeepSpeed API is a lightweight wrapper on PyTorch. This means that you can use everything you love in PyTorch and without learning a new platform. In addition, DeepSpeed manages all of the boilerplate state-of-the-art training techniques, such as distributed training, mixed precision, gradient accumulation, and checkpoints so that you can focus on your model development. Most importantly, you can leverage the distinctive efficiency and effectiveness benefit of DeepSpeed to boost speed and scale with just a few lines of code changes to your PyTorch models.

DeepSpeed achieves high performance and fast convergence through a combination of efficiency optimizations on compute/communication/memory/IO and effectiveness optimizations on advanced hyperparameter tuning and optimizers. For example:

DeepSpeed trains BERT-large to parity in 44 mins using 1024 V100 GPUs (64 DGX-2 boxes) and in 2.4 hours using 256 GPUs (16 DGX-2 boxes).

BERT-large Training Times

BERT code and tutorials will be available soon.

DeepSpeed trains GPT2 (1.5 billion parameters) 3.75x faster than state-of-art, NVIDIA Megatron on Azure GPUs.

Read more: GPT tutorial

DeepSpeed provides memory-efficient data parallelism and enables training models without model parallelism. For example, DeepSpeed can train models with up to 13 billion parameters on a single GPU. In comparison, existing frameworks (e.g., PyTorch’s Distributed Data Parallel) run out of memory with 1.4 billion parameter models.

DeepSpeed reduces the training memory footprint through a novel solution called Zero Redundancy Optimizer (ZeRO). Unlike basic data parallelism where memory states are replicated across data-parallel processes, ZeRO partitions model states and gradients to save significant memory. Furthermore, it also reduces activation memory and fragmented memory. The current implementation (ZeRO-2) reduces memory by up to 8x relative to the state-of-art. You can read more about ZeRO in our paper, and in our blog posts related to ZeRO-1 and ZeRO-2.

With this impressive memory reduction, early adopters of DeepSpeed have already produced a language model (LM) with over 17B parameters called Turing-NLG, establishing a new SOTA in the LM category.

For model scientists with limited GPU resources, ZeRO-Offload leverages both CPU and GPU memory for training large models. Using a machine with a single GPU, our users can run models of up to 13 billion parameters without running out of memory, 10x bigger than the existing approaches, while obtaining competitive throughput. This feature democratizes multi-billion-parameter model training and opens the window for many deep learning practitioners to explore bigger and better models.

DeepSpeed supports efficient data parallelism, model parallelism, pipeline parallelism and their combinations, which we call 3D parallelism.

DeepSpeed can run large models more efficiently, up to 10x faster for models with various sizes spanning 1.5B to hundred billion. More specifically, the data parallelism powered by ZeRO is complementary and can be combined with different types of model parallelism. It allows DeepSpeed to fit models using lower degree of model parallelism and higher batch size, offering significant performance gains compared to using model parallelism alone.

Read more: ZeRO paper, and GPT tutorial.

The figure depicts system throughput improvements of DeepSpeed (combining ZeRO-powered data parallelism with model parallelism of NVIDIA Megatron-LM) over using Megatron-LM alone.

Pipeline parallelism of DeepSpeed reduce communication volume during distributed training, which allows users to train multi-billion-parameter models 2–7x faster on clusters with limited network bandwidth.

1-bit Adam, 0/1 Adam and 1-bit LAMB reduce communication volume by up to 26x while achieving similar convergence efficiency to Adam, allowing for scaling to different types of GPU clusters and networks. 1-bit Adam blog post, 1-bit Adam tutorial, 0/1 Adam tutorial, 1-bit LAMB tutorial.

DeepSpeed Data Efficiency Library provides efficient data sampling via curriculum learning and efficient data routing via random layerwise token dropping. The composed solution enables up to 2x data and 2x time saving during GPT-3/BERT pretraining and GPT/ViT finetuning, or further improve model quality under the same data/time. See more in the tutorial.

DeepSpeed offers sparse attention kernels—an instrumental technology to support long sequences of model inputs, whether for text, image, or sound. Compared with the classic dense Transformers, it powers an order-of-magnitude longer input sequence and obtains up to 6x faster execution with comparable accuracy. It also outperforms state-of-the-art sparse implementations with 1.5–3x faster execution. Furthermore, our sparse kernels support efficient execution of flexible sparse format and empower users to innovate on their custom sparse structures. Read more here.

DeepSpeed supports advanced hyperparameter tuning and large batch size optimizers such as LAMB. These improve the effectiveness of model training and reduce the number of samples required to convergence to desired accuracy.

Read more: Tuning tutorial.

Only a few lines of code changes are needed to enable a PyTorch model to use DeepSpeed and ZeRO. Compared to current model parallelism libraries, DeepSpeed does not require a code redesign or model refactoring. It also does not put limitations on model dimensions (such as number of attention heads, hidden sizes, and others), batch size, or any other training parameters. For models of up to 13 billion parameters, you can use ZeRO-powered data parallelism conveniently without requiring model parallelism, while in contrast, standard data parallelism will run out of memory for models with more than 1.4 billion parameters. In addition, DeepSpeed conveniently supports flexible combination of ZeRO-powered data parallelism with custom model parallelisms, such as tensor slicing of NVIDIA’s Megatron-LM.

Below we provide a brief feature list, see our detailed feature overview for descriptions and usage.

title: “Feature Overview” layout: single permalink: /features/ toc: true toc_label: “Contents” —

Enable 16-bit (FP16) training by in the deepspeed_config JSON.

Easily switch between single-GPU, single-node multi-GPU, or multi-node multi-GPU execution by specifying resources with a hostfile.

The script <client_entry.py> will execute on the resources specified in <hostfile>.

DeepSpeed provides pipeline parallelism for memory- and communication- efficient training. DeepSpeed supports a hybrid combination of data, model, and pipeline parallelism and has scaled to over one trillion parameters using 3D parallelism. Pipeline parallelism can also improve communication efficiency and has accelerated training by up to 7x on low-bandwidth clusters.

DeepSpeed supports all forms of model parallelism including tensor slicing based approaches such as the Megatron-LM. It does so by only requiring the model parallelism framework to provide a model parallelism unit (mpu) that implements a few bookkeeping functionalities:

DeepSpeed is fully compatible with Megatron. Please see the Megatron-LM tutorial for details.

The Zero Redundancy Optimizer (ZeRO) is at the heart of DeepSpeed and enables large model training at a scale that is simply not possible with model parallelism alone. When enabled, ZeRO allows training models with over 13 billion parameters without any model parallelism, and up to 200 billion parameter models with model parallelism on current generation hardware.

For more details see the ZeRO paper, GPT tutorial on integration with DeepSpeed.

Optimizer State and Gradient Partitioning in ZeRO reduces the memory consumption of the model states (optimizer states, gradients and parameters) by 8x compared to standard data parallelism by partitioning these states across data parallel process instead of replicating them.

Activation Partitioning is a memory optimization in ZeRO that can reduce the memory consumed by activations during model parallel training (MP). In MP certain activations maybe required by all MP processes, resulting in a replication of activations across MP GPUs. Activation Partitioning stores these activations in a partitioned state once they are used for computation in the forward propagation. These activations are allgathered right before they are needed again during the backward propagation. By storing activations in a partitioned state, ZeRO in DeepSpeed can reduce the activation memory footprint proportional to the MP degree.

CBO enables high network and memory throughput while restricting memory usage to a constant size. For memory- and network-bound operations such as normalization or allreduce collectives, the performance depends on the size of the operand. Simply fusing all operands into a single large operand can enable great throughput at the expense of unnecessary memory overhead. CBO in DeepSpeed fuses smaller operands into approximately a pre-defined sized buffer large enough to achieve great performance without the unnecessary memory overhead.

CMO reduces memory fragmentation during training, preventing out of memory errors due to lack of contiguous memory. Memory fragmentation is a result of interleaving between short lived and long lived memory objects. During the forward propagation activation checkpoints are long lived but the activations that recomputed are short lived. Similarly, during the backward computation, the activation gradients are short lived while the parameter gradients are long lived. CMO transfers activation checkpoints and parameter gradients to contiguous buffers preventing memory fragmentation.

ZeRO-Offload pushes the boundary of the maximum model size that can be trained efficiently using minimal GPU resources, by exploiting computational and memory resources on both GPUs and their host CPUs. It allows training up to 13-billion-parameter models on a single NVIDIA V100 GPU, 10x larger than the state-of-the-art, while retaining high training throughput of over 30 teraflops per GPU.

For more details see the ZeRO-Offload release blog, and tutorial on integration with DeepSpeed.

Gradient accumulation allows running larger batch size with limited memory by breaking an effective batch into several sequential micro-batches, and averaging the parameter gradients across these micro-batches. Furthermore, instead of averaging the gradients of each micro-batch across all GPUs, the gradients are averaged locally during each step of the sequence, and a single allreduce is done at the end of the sequence to produce the averaged gradients for the effective batch across all GPUs. This strategy significantly reduces the communication involved over the approach of averaging globally for each micro-batch, specially when the number of micro-batches per effective batch is large.

During back propagation, DeepSpeed can overlap the communication required for averaging parameter gradients that have already been computed with the ongoing gradient computation. This computation-communication overlap allows DeepSpeed to achieve higher throughput even at modest batch sizes.

The DeepSpeed core API consists of just a handful of methods:

DeepSpeed supports most of the features described in this document, via the use of these API, along with a deepspeed_config JSON file for enabling and disabling the features. Please see the core API doc for more details.

DeepSpeed’s Activation Checkpointing API supports activation checkpoint partitioning, cpu checkpointing, and contiguous memory optimizations, while also allowing layerwise profiling. Please see the core API doc for more details.

DeepSpeed handles gradient clipping under the hood based on the max gradient norm specified by the user. Please see the core API doc for more details.

DeepSpeed internally handles loss scaling for mixed precision training. The parameters for loss scaling can be specified in the deepspeed_config JSON file. Please see the core API doc for more details.

DeepSpeed has three communication-efficient optimizers called 1-bit Adam, 0/1 Adam and 1-bit LAMB. They offer the same convergence as Adam/LAMB, incur up to 26x less communication that enables up to 6.6x higher throughput for BERT-Large pretraining and up to 2.7x higher throughput for SQuAD fine-tuning on bandwidth-limited clusters. For more details on usage and performance, please refer to the 1-bit Adam tutorial, 1-bit Adam blog post, 0/1 Adam tutorial and 1-bit LAMB tutorial. For technical details, please refer to the 1-bit Adam paper, 0/1 Adam paper and 1-bit LAMB paper.

With DeepSpeed, the user can choose to use a high performance implementation of ADAM from NVIDIA, or any training optimizer that extends torch’s torch.optim.Optimizer class.

We introduce an efficient implementation of Adam optimizer on CPU that improves the parameter-update performance by nearly an order of magnitude. We use the AVX SIMD instructions on Intel-x86 architecture for the CPU-Adam implementation. We support both AVX-512 and AVX-2 instruction sets. DeepSpeed uses AVX-2 by default which can be switched to AVX-512 by setting the build flag, DS_BUILD_AVX512 to 1 when installing DeepSpeed. Using AVX-512, we observe 5.1x to 6.5x speedups considering the model-size between 1 to 10 billion parameters with respect to torch-adam.

Mixed precision training is handled by the DeepSpeed FP16 Optimizer. This optimizer not only handles FP16 training but is also highly efficient. The performance of weight update is primarily dominated by the memory bandwidth, and the achieved memory bandwidth is dependent on the size of the input operands. The FP16 Optimizer is designed to maximize the achievable memory bandwidth by merging all the parameters of the model into a single large buffer, and applying the weight updates in a single kernel, allowing it to achieve high memory bandwidth.

DeepSpeed makes it easy to train with large batch sizes by enabling the LAMB Optimizer. For more details on LAMB, see the LAMB paper.

DeepSpeed can train models with up to 13 billion parameters without model parallelism, and models with up to 200 billion parameters with 16-way model parallelism. This leap in model size is possible through the memory efficiency achieved via the ZeRO Optimizer. For more details see ZeRO paper .

DeepSpeed can simplify checkpointing for you regardless of whether you are using data parallel training, model parallel training, mixed-precision training, a mix of these three, or using the zero optimizer to enable larger model sizes. Please see the Getting Started guide and the core API doc for more details.

DeepSpeed supports multiple Learning Rate Schedules to enable faster convergence for large batch scaling.

Please refer to the Learning Rate Range Test tutorial.

Please refer to the 1Cycle Learning Rate Schedule tutorial.

DeepSpeed abstracts away data parallelism and model parallelism from the user when it comes to data loading. Users simply provide a PyTorch dataset, and DeepSpeed data loader can automatically handle batch creation appropriately.

Please refer to the Data Efficiency tutorial.

Please refer to the Curriculum Learning tutorial. Note that the Data Efficiency Library above provides more general curriculum learning support. This legacy curriculum learning feature is still supported but we recommend to use the Data Efficiency Library.

DeepSpeed provides a set of tools for performance analysis and debugging.

DeepSpeed provides a detailed breakdown of the time spent in different parts of the training. This can be enabled by setting the following in the deepspeed_config file.

When activation checkpointing is enabled, profiling the forward and backward time of each checkpoint function can be enabled in the deepspeed_config file.

The DeepSpeed flops profiler measures the time, flops and parameters of a PyTorch model and shows which modules or layers are the bottleneck. When used with the DeepSpeed runtime, the flops profiler can be configured in the deepspeed_config file as follows:

The flops profiler can also be used as a standalone package. Please refer to the Flops Profiler tutorial for more details.

The DeepSpeed Autotuner uses model information, system information, and heuristics to efficiently tune Zero stage, micro batch size, and other Zero configurations. Using the autotuning feature requires no code change from DeepSpeed users. While "autotuning": {"enabled": true} is the minimal required to enable autotuning, there are other parameters users can define to configure the autotuning process. Below shows major parameters and their default values in the autotuning configuration. Please refer to the Autotuning tutorial for more details.

The flops profiler can also be used as a standalone package. Please refer to the Flops Profiler tutorial for more details.

The DeepSpeed Monitor logs live training metrics to one or more monitoring backends, including PyTorch’s TensorBoard, WandB, or simply to CSV files. The Monitor can be configured with one or more backends in the deepspeed_config file as follows:

The Monitor can also be added to log custom metrics and client codes. Please refer to the Monitor tutorial for more details.

DeepSpeed provides logging of all communication operations launched within deepspeed.comm. The communication logger can be configured in the deepspeed_config file as follows:

Client codes can then print a summary with a call to deepspeed.comm.log_summary(). For more details and example usage, see the Communication Logging tutorial.

DeepSpeed offers sparse attention to support long sequences. Please refer to the Sparse Attention tutorial.

To learn more about training Mixture of Experts (MoE) models with DeepSpeed, see our tutorial for more details.

**Examples:**

Example 1 (unknown):
```unknown
"fp16": {
    "enabled": true,
    "loss_scale": 0,
    "loss_scale_window": 1000,
    "hysteresis": 2,
    "consecutive_hysteresis": false,
    "min_loss_scale": 1
}
```

Example 2 (unknown):
```unknown
deepspeed --hostfile=<hostfile> \
	<client_entry.py> <client args> \
	--deepspeed --deepspeed_config ds_config.json
```

Example 3 (unknown):
```unknown
mpu.get_model_parallel_rank()
mpu.get_model_parallel_group()
mpu.get_model_parallel_world_size()

mpu.get_data_parallel_rank()
mpu.get_data_parallel_group()
mpu.get_data_parallel_world_size()
```

Example 4 (unknown):
```unknown
{
  "gradient_clipping": 1.0
}
```

---

## Latest News

**URL:** https://www.deepspeed.ai/

**Contents:**
- Latest News
    - Contents
- Extreme Speed and Scale for DL Training
- DeepSpeed Adoption
- Contributing
- Contributor License Agreement
- Code of Conduct
- Publications
- Videos

[2025/10] SuperOffload: Unleashing the Power of Large-Scale LLM Training on Superchips

[2025/10] Study of ZenFlow and ZeRO offload performance with DeepSpeed CPU core binding

[2025/08] ZenFlow: Stall-Free Offloading Engine for LLM Training

[2025/06] Arctic Long Sequence Training (ALST) with DeepSpeed: Scalable And Efficient Training For Multi-Million Token Sequences

[2025/06] DeepNVMe: Affordable I/O scaling for Deep Learning Applications

DeepSpeed enabled the world’s most powerful language models (at the time of this writing) such as MT-530B and BLOOM. DeepSpeed offers a confluence of system innovations, that has made large scale DL training effective, and efficient, greatly improved ease of use, and redefined the DL training landscape in terms of scale that is possible. These innovations include ZeRO, 3D-Parallelism, DeepSpeed-MoE, ZeRO-Infinity, etc.

DeepSpeed has been used to train many different large-scale models. Below is a list of several examples that we are aware of (if you’d like to include your model please submit a PR):

DeepSpeed has been integrated with several different popular open-source DL frameworks such as:

DeepSpeed is an integral part of Microsoft’s AI at Scale initiative to enable next-generation AI capabilities at scale.

DeepSpeed welcomes your contributions! Please see our contributing guide for more details on formatting, testing, etc.

This project welcomes contributions and suggestions. Most contributions require you to agree to a Contributor License Agreement (CLA) declaring that you have the right to, and actually do, grant us the rights to use your contribution. For details, visit https://cla.opensource.microsoft.com.

When you submit a pull request, a CLA bot will automatically determine whether you need to provide a CLA and decorate the PR appropriately (e.g., status check, comment). Simply follow the instructions provided by the bot. You will only need to do this once across all repos using our CLA.

This project has adopted the Microsoft Open Source Code of Conduct. For more information see the Code of Conduct FAQ or contact opencode@microsoft.com with any additional questions or comments.

Xinyu Lian, Sam Ade Jacobs, Lev Kurilenko, Masahiro Tanaka, Stas Bekman, Olatunji Ruwase, Minjia Zhang. (2024) Universal Checkpointing: Efficient and Flexible Checkpointing for Large Scale Distributed Training arXiv:2406.18820

---

## Supporting efficient large model training on AMD Instinct GPUs with DeepSpeed

**URL:** https://www.deepspeed.ai/2022/03/20/amd-support.html

**Contents:**
- Supporting efficient large model training on AMD Instinct GPUs with DeepSpeed
    - Contents

Updated: March 20, 2022

---

## DeepSpeed Configuration JSON

**URL:** https://www.deepspeed.ai/docs/config-json/

**Contents:**
- DeepSpeed Configuration JSON
    - Contents
  - Batch Size Related Parameters
  - Optimizer Parameters
  - Scheduler Parameters
  - Communication options
  - FP16 training options
  - BFLOAT16 training options
  - Automatic mixed precision (AMP) training options
  - Gradient Clipping

Note: train_batch_size must be equal to train_micro_batch_size_per_gpu * gradient_accumulation_steps * number of GPUs. For simplicity, you can choose to only specify two of the three parameters, the last one will be inferred automatically by DeepSpeed.

train_batch_size: [integer]

train_micro_batch_size_per_gpu: [integer]

gradient_accumulation_steps: [integer]

optimizer: [dictionary]

Example of optimizer with Adam

The Adam optimizer also supports the following two params keys/values in addition to the standard parameters from torch.optim.Adam:

Another example of optimizer with 1-bit Adam specific parameters is as follows.

The 1-bit Adam optimizer supports the following three params keys/values in addition to the standard Adam (learn more in our tutorial):

A variant optimizer for 1-bit Adam is 0/1 Adam, which further optimizes 1-bit Adam via adaptive variance freezing and 1-bit synchronization over optimizer states.

0/1 Adam supports the following params key/values in addition to standard Adam (learn more in our tutorial.)

Another example of optimizer with 1-bit LAMB

The 1-bit LAMB optimizer supports the following params keys/values in addition to the standard LAMB (learn more in our tutorial):

DeepSpeed calls the step() method of the scheduler at every training step when model_engine.step() is executed.

scheduler: [dictionary]

communication_data_type: [string]

prescale_gradients: [boolean]

gradient_predivide_factor: [float]

sparse_gradients: [boolean]

Note: this mode cannot be combined with the amp mode described below.

fp16:enabled: [boolean]

fp16:auto_cast: [boolean]

fp16:loss_scale: [float]

fp16:initial_scale_power: [integer]

fp16:loss_scale_window: [integer]

fp16:hysteresis: [integer]

fp16:consecutive_hysteresis: [boolean]

fp16:min_loss_scale: [integer]

Note: this mode cannot be combined with the amp mode described below.

Note: this mode cannot be combined with the fp16 mode described above.

bf16:enabled: [boolean]

Note: this mode cannot be combined with the fp16 mode described above. In addition this mode is not currently compatible with ZeRO.

amp:enabled: [boolean]

amp params: [various]

gradient_clipping: [float]

Enabling and configuring ZeRO memory optimizations

zero_optimization: [dictionary]

allgather_partitions: [boolean]

allgather_bucket_size: [integer]

overlap_comm: [boolean]

reduce_scatter: [boolean]

reduce_bucket_size: [integer]

contiguous_gradients: [boolean]

load_from_fp32_weights: [boolean]

grad_hooks: [boolean]

round_robin_gradients: [boolean]

offload_param: [dictionary]

offload_optimizer: [dictionary]

stage3_max_live_parameters: [integer]

stage3_max_reuse_distance: [integer]

stage3_prefetch_bucket_size: [integer]

stage3_param_persistence_threshold: [integer]

stage3_gather_16bit_weights_on_model_save: [boolean]

stage3_module_granularity_threshold: [integer] | Description | Default | |——————————————————————————————————————————————————————————————————————————————————————————–| ——- | | The granularity of a module is determined by the ratio of parameter_count / (1 + descendant_count). ZeRO3 classifies modules with a granularity below the threshold as fine-grained, treating them as integral units during parameter fetching. This reduces host and communication overhead from separate hooks. | 0 |

zero_hpz_partition_size: [integer]

zero_quantized_weights: [boolean]

zero_quantized_gradients: [boolean]

log_trace_cache_warnings: [boolean]

cpu_offload: [boolean]

Deprecated: cpu_offload is deprecated and will be removed in future, please use offload_optimizer instead.

Enabling and configuring ZeRO optimization of parameter offloading to CPU/NVMe. Available only with ZeRO stage 3. Note that if the value of “device” is not specified or not supported, an assertion will be triggered.

pin_memory: [boolean]

buffer_count: [integer]

buffer_size: [integer]

max_in_cpu: [integer]

Enabling and configuring ZeRO optimization of offloading optimizer computation to CPU and state to CPU/NVMe. CPU offloading is available with ZeRO stage 1, 2, 3. NVMe offloading is available only with ZeRO stage 3. Note that if the value of “device” is not specified or not supported, an assertion will be triggered.

pin_memory: [boolean]

buffer_count: [integer]

Configuring the asynchronous I/O module for offloading parameter and optimizer states to persistent (NVMe) storage. This module uses Linux native asynchronous I/O (libaio).

block_size: [integer]

queue_depth: [integer]

thread_count: [integer]

single_submit: [boolean]

overlap_events: [boolean]

ignore_unused_parameters: [boolean]

steps_per_print: [integer]

wall_clock_breakdown: [boolean]

dump_state: [boolean]

results_dir: [string]

start_profile_step: [integer]

end_profile_step: [integer]

max_train_batch_size: [int]

num_tuning_micro_batch_sizes: [integer]

tuner_early_stopping: [integer]

tuner_num_trials: [integer]

profile_step: [integer]

module_depth: [integer]

top_modules: [integer]

output_file: [string]

partition_activations: [boolean]

cpu_checkpointing: [boolean]

contiguous_memory_optimization: [boolean]

number_checkpoints: [integer]

synchronize_checkpoint_boundary: [boolean]

sparse_attention: [dictionary]

Example of sparse_attention

DeepSpeed Data Efficiency Library includes two techniques: curriculum learning and random layerwise token dropping (random-LTD). Read more about how to use the DeepSpeed Data Efficiency Library in our tutorial.

data_efficiency: [dictionary]

data_routing: [dictionary]

data_sampling: [dictionary]

random_ltd: [dictionary]

curriculum_learning: [dictionary]

Note: On 12/12/2022, we released DeepSpeed Data Efficiency Library which provides a more general curriculum learning support. This legacy curriculum learning feature below is still supported but we recommend to use the Data Efficiency Library.

curriculum_type: [string]

min_difficulty: [integer]

max_difficulty: [integer]

schedule_type: [string]

total_curriculum_step: [integer]

difficulty_step: [integer]

root_degree: [integer]

difficulty: [list of integer]

max_step: [list of integer]

Note: Deepspeed logs to TensorBoard through PyTorch. Logging to TensorBoard requires that the tensorboard package is installed (read more in the PyTorch documentation).

Note: Logging to WandB requires that the wandb package is installed (read more in the WandB documentation).

Note: Logging to Comet requires that the comet_ml package is installed (read more in the Comet documentation).

Deepspeed’s Monitor module can log training details into a Tensorboard-compatible file, to WandB, to Comet or to simple CSV files. Below is an overview of what DeepSpeed will log automatically.

tensorboard: [dictionary]

Example of tensorboard configuration:

Example of wandb configuration:

Example of comet configuration:

csv_monitor: [dictionary]

Example of csv_monitor configuration:

DeepSpeed provides a flexible communication logging tool which can automatically detect and record communication operations launched via deepspeed.comm. NOTE: All logging communication calls are synchronized in order to provide accurate timing information. This may hamper performance if your model heavily uses asynchronous communication operations.

Once the logs are populated, they can be summarized with deepspeed.comm.log_summary(). For more detail and example usage, see the tutorial

comms_logger: [dictionary]

Example of recommended comms_logger configuration:

Example of comms_logger configuration for logging specific operations only:

Note: Compression has seven different components, including layer reduction, weight quantization, activation quantization, sparse pruning, row pruning, head pruning, and channel pruning. We explain them one by one with simple json examples. Read more about how to use the DeepSpeed Compression library in our tutorial.

Note: Layer reduction works much better when using knowledage distillation (learn more in our tutorial):

layer_reduction: [dictionary]

shared_parameters: [dictionary]

Shared parameters for all weight quantization groups.

different_groups: [dictionary]

Different quantization sets, this is used for different quantization parameters. In this example, we give two different sets. In practice, you can choose the number of sets based on your requirements.

shared_parameters: [dictionary]

Shared parameters for all activation quantization groups.

different_groups: [dictionary]

Different quantization sets, this is used for different quantization parameters. In this example, we give one set. In practice, you can choose the number of sets based on your requirements.

shared_parameters: [dictionary]

Shared parameters for all sparse pruning groups.

different_groups: [dictionary]

Different pruning sets, this is used for different pruning parameters. In this example, we give one set. In practice, you can choose the number of sets based on your requirements. Note for snip_momentum method, you can leave it as empty.

Note: Row Pruning is a feature designed for two back-to-back linear layers (e.g., Feed Forward Network in Transformers). As such, we suggested use row pruning for the first linear layer (i.e., the intermediate.dense layer for BERT). Reducing the row dimension of this matrix can help reducing the column of the follow-up matrix (i.e., layer.\\w+.output.dense layer for BERT). It should also work for other linear layers as well.

shared_parameters: [dictionary]

Shared parameters for all row pruning groups.

different_groups: [dictionary]

Different pruning sets, this is used for different pruning parameters. In this example, we give one set. In practice, you can choose the number of sets based on your requirements.

Note: Head Pruning is a feature designed for two attention layers (e.g., Multi Head Attention in Transformers). For now, it can only be applied to output matrix of the Transformer (i.e., attention.output.dense in BERT). Pruning the output matrix can lead to the pruning of Query/Key/Value matrix as well.

shared_parameters: [dictionary]

Shared parameters for all head pruning groups.

different_groups: [dictionary]

Different pruning sets, this is used for different pruning parameters. In this example, we give one set. In practice, you can choose the number of sets based on your requirements.

Note: Channel Pruning is a feature designed for two back-to-back CONV2d layers (e.g., residual connection in ResNet). As such, we suggested use channel pruning for the first CONV2d layer. Reducing the number of output channels of this layer can help reducing the number of input channels the follow-up layer. It should also work for other CONV2d layers as well.

shared_parameters: [dictionary]

Shared parameters for all channel pruning groups.

different_groups: [dictionary]

Different pruning sets, this is used for different pruning parameters. In this example, we give one set. In practice, you can choose the number of sets based on your requirements.

load_universal: [boolean]

use_node_local_storage: [boolean]

pipeline_stage: [boolean]

**Examples:**

Example 1 (unknown):
```unknown
"optimizer": {
    "type": "Adam",
    "params": {
      "lr": 0.001,
      "betas": [
        0.8,
        0.999
      ],
      "eps": 1e-8,
      "weight_decay": 3e-7
    }
  }
```

Example 2 (unknown):
```unknown
"optimizer": {
    "type": "OneBitAdam",
    "params": {
      "lr": 0.001,
      "betas": [
        0.8,
        0.999
      ],
      "eps": 1e-8,
      "weight_decay": 3e-7,
      "freeze_step": 400,
      "cuda_aware": false,
      "comm_backend_name": "nccl"
    }
  }
```

Example 3 (unknown):
```unknown
"optimizer": {
    "type": "ZeroOneAdam",
    "params": {
      "lr": 1e-3,
      "weight_decay": 0.01,
      "bias_correction": false,
      "var_freeze_step": 1000,
      "var_update_scaler": 16,
      "local_step_scaler": 1000,
      "local_step_clipper": 16,
      "cuda_aware": false,
      "comm_backend_name": "nccl"
    }
  }
```

Example 4 (unknown):
```unknown
"optimizer": {
    "type": "OneBitLamb",
    "params": {
      "lr": 11e-3,
      "weight_decay": 0.01,
      "bias_correction": false,
      "max_coeff": 0.3,
      "min_coeff": 0.01,
      "freeze_step": 1000,
      "cuda_aware": false,
      "comm_backend_name": "nccl",
      "coeff_beta": 0.9,
      "factor_max": 4.0,
      "factor_min": 0.5,
      "factor_threshold": 0.1
    }
  }
```

---

## DeepSpeed ZeRO-3 Offload

**URL:** https://www.deepspeed.ai/2021/03/07/zero3-offload.html

**Contents:**
- DeepSpeed ZeRO-3 Offload
    - Contents
- Overview of ZeRO family of technology
- ZeRO-3 Offload
- Unprecedented model scale
- Ease of supporting very large models
- Excellent training efficiency
- How to use ZeRO-3 Offload

Today we are announcing the release of ZeRO-3 Offload, a highly efficient and easy to use implementation of ZeRO Stage 3 and ZeRO Offload combined, geared towards our continued goal of democratizing AI by making efficient large-scale DL training available to everyone. The key benefits of ZeRO-3 Offload are:

The ZeRO Redundancy Optimizer (abbreviated ZeRO) is a family of memory optimization technologies for large-scale distributed deep learning. Unlike data parallelism (that is efficient but can only support a limited model size) or model parallelism (that can support larger model sizes but requires significant code refactoring while adding communication overhead that limits efficiency), ZeRO allows fitting larger models in memory without requiring code refactoring while remaining very efficient. ZeRO does so by eliminating the memory redundancy that is inherent in data parallelism while limiting the communication overhead to a minimum. ZeRO removes the memory redundancies across data-parallel processes by partitioning the three model states (optimizer states, gradients, and parameters) across data-parallel processes instead of replicating them. By doing this, it boosts memory efficiency compared to classic data-parallelism while retaining its computational granularity and communication efficiency. There are three stages in ZeRO corresponding to three model states, as shown in the Figure 1: the first stage (ZeRO-1) partitions only the optimizer states, the second stage (ZeRO-2) partitions both the optimizer states and the gradients and the final stage (ZeRO-3) partitions all three model states (for more details see the ZeRO paper).

Figure 1. Overview of ZeRO memory savings

In addition to these three stages, ZeRO family of technology also consists of ZeRO-2 Offload. ZeRO-2 Offload is a heterogeneous DL training technology that works in conjunction with ZeRO-2 to offload partitioned optimizer states and gradients to CPU memory. ZeRO-2 Offload offers the full memory advantage of ZeRO-2 even on a single GPU, while at the same time offering great scalability of ZeRO-2 on multi-GPU setup. DeepSpeed library has been offering ZeRO-2 Offload since Sept 2020. For details, please see below:

With today’s release of ZeRO-3 Offload, we are adding support for partitioning and offloading parameters in addition to optimizer states and gradients partitioning already supported by ZeRO-2 Offload in DeepSpeed. With parameter partitioning ZeRO-3 Offload implements the full set of features in the three stages of ZeRO, that allows for a linear growth in model size with the number of GPUs. In addition, ZeRO-3 Offload can also optionally offload all these model states to CPU to further reduce GPU memory consumption, leveraging both CPU and GPU to maximize memory and compute efficiency of the entire system.

We believe ZeRO-3 Offload offers a massive leap for large model training, in three regards:

i) Unprecedented model scale,

ii) Ease of supporting very-large models, and

iii) Achieving excellent training efficiency.

Unlike ZeRO-2 and ZeRO-Offload where the parameters have to fit in the memory of a single GPU, ZeRO-3 Offload can partition the parameters across GPUs, and offload them to CPU, supporting model sizes that are much larger than the memory on a single GPU. Furthermore, ZeRO-3 Offload goes beyond the state-of-the-art hybrid 3D-parallelism (data, model and pipeline parallelism combined). While 3D Parallelism is limited by the aggregate GPU memory, ZeRO-3 Offload can exploit both GPU and CPU memory, the latter of which is much larger and cheaper compared to GPU memory. This allows ZeRO-3 Offload to train larger model sizes with the given GPU and CPU resources than any other currently available technology.

Model Scale on Single GPU: ZeRO-3 Offload can train models with over 40B parameters efficiently on a single GPU (e.g., 32GB V100 GPU + 1.5TB CPU memory). This is 3x larger than what is possible with ZeRO-2 Offload, the current state-of-the art.

Model Scale on Multi-GPUs: With ZeRO-3 Offload you can train a trillion and two trillion parameter models on NVIDIA 32GB V100 DGX-2 cluster with 256 GPUs and 512 GPUs, respectively. In contrast, the state-of-art 3D Parallelism requires 800 GPUs, and 1600 GPUs, respectively, to fit the same sized models. This represents a 3x reduction in GPUs required to fit models with over a trillion parameters.

From a system perspective, training models with hundreds of billions and trillions of parameters is extremely challenging. Data parallelism cannot scale the model size much further beyond a billion parameters, model parallelism (with tensor slicing) cannot be used to scale model size efficiently beyond a single node boundary due to massive communication overheads, and pipeline parallelism cannot scale beyond the number of layers available in a model, which limits both the model size and the number of GPUs that it can scale to.

The only existing parallel technology available that can scale to over a trillion parameters on massively parallel GPU clusters is the 3D parallelism that combines data, model and pipeline parallelism in complex ways. While such a system can be very efficient, it requires major model code refactoring from data scientists to split the model into load balanced pipeline stages. This also makes 3D parallelism inflexible in the type of models that it can support, since models with complex dependency graphs cannot be easily converted into a load balanced pipeline.

ZeRO-3 Offload address these challenges in two ways:

i) With ground-breaking memory efficiency, ZeRO-3 and ZeRO-3 Offload are the only DL parallel technology that can efficiently scale to over a trillion parameters by itself, without requiring a hybrid parallelism strategy, greatly simplifying the system stack for DL training.

ii) ZeRO-3 Offload requires virtually no model refactoring from model scientists, liberating data scientists to scale up complex models to hundreds of billions to trillions of parameters.

High-performance per-GPU throughput on multiple nodes: ZeRO-3 Offload offers excellent training efficiency for multi-billion and trillion parameter models on multiple nodes. It achieves a sustained throughput of up to 50 Tflops per GPU running on 32 DGX2 nodes comprising 512 NVIDIA V100 GPUs (see Figure 2). In comparison, the standard data parallel training with PyTorch can only achieve 30 TFlops per GPU for a 1.2B parameter model, the largest model that can be trained using data parallelism alone.

Figure 2. ZeRO-3 Offload: Multi-billion and trillion parameter model throughput on 512 V100 GPUs

ZeRO-3 Offload obtains high efficiency despite the 50% communication overhead of ZeRO Stage 3 compared to standard data parallel training for a fixed batch size. This is made possible through a communication overlap centric design and implementation, which allows ZeRO-3 Offload to hide nearly all of the communication volume with computation, while taking advantage of a larger batch size for improved efficiency resulting from better GPU memory efficiency.

Efficient multi-billion parameter model training on a single GPU: ZeRO-3 Offload further democratizes AI by enabling efficient training of multi-billion parameter models on a single GPU. For single GPU training, ZeRO-3 Offload provides benefits over ZeRO-2 Offload along two dimensions. First, ZeRO-3 Offload increases the size of models trainable on a single V100 from 13B to 40B. Second, for ZeRO-3 Offload provides speedups (e.g., 2.3X for 13B) compared to ZeRO-2 Offload for model sizes trainable by both solutions. These results are summarized in Figure 3.

Figure 3. Multi-billion parameter model training on one V100 GPU

Super-Linear scalability across GPUs: Additionally, ZeRO-3 Offload also preserves the super-linear scalability characteristics that we have demonstrated with all our previous ZeRO technologies (ZeRO Stage 1, ZeRO Stage 2 and ZeRO Offload). ZeRO-3 Offload can exploit the aggregate PCI-E bandwidth between GPU and CPU across all the GPUs in multi-GPU training configuration, and at the same time, it can also exploit the aggregate CPU compute across all the nodes. As a result, the CPU-GPU-CPU communication time as well as the optimizer update time decreases linearly with number of GPUs and nodes, respectively, allowing ZeRO-3 Offload to exhibit super-linear scaling (see Figure 4).

Figure 4. ZeRO-3 Offload Superlinear Scalability for a 200B parameter model.

As with many other existing DeepSpeed features, once the user model has been converted to use DeepSpeed, enabling ZeRO-3 Offload is as easy as turning on a couple of flags in DeepSpeed Config file. Supporting advanced features like weight sharing, or enabling extremely large models that requires to be partitioned across GPUs/nodes to fit in GPU/CPU memory, can be done with just a couple of additional lines of code change using the ZeRO-3 Offload API.

If you are already a DeepSpeed user, you can find our detailed tutorial on ZeRO-3 Offload below. If you are new to DeepSpeed, we recommend that you start at the getting started page before trying out our ZeRO-3 Offload Tutorial.

DeepSpeed: Getting Started Page

ZeRO-3 Offload Documentation, Tutorial

The DeepSpeed Team is very excited to share ZeRO-3 Offload with the DL community.

Updated: March 7, 2021

---

## DeepSpeed: Advancing MoE inference and training to power next-generation AI scale

**URL:** https://www.deepspeed.ai/2022/01/18/moe-inference.html

**Contents:**
- DeepSpeed: Advancing MoE inference and training to power next-generation AI scale
    - Contents

Updated: January 18, 2022

---

## Azure empowers easy-to-use, high-performance, and hyperscale model training using DeepSpeed

**URL:** https://www.deepspeed.ai/2022/07/25/deepspeed-azure.html

**Contents:**
- Azure empowers easy-to-use, high-performance, and hyperscale model training using DeepSpeed
    - Contents
- Introduction
- Making distributed training faster and easier on Azure using DeepSpeed
- Key Performance Benefits
- Experimental Setup
  - Hardware (Azure instances)
  - Training setup using AzureML
  - Training setup using Azure VMSS
- Performance Evaluation on Various Model Configurations

Large-scale transformer-based deep learning models trained on large amounts of data have shown great results in recent years in several cognitive tasks and are behind new products and features that augment human capabilities. These models have grown several orders of magnitude in size during the last five years. Starting from a few million parameters of the original transformer model all the way to the latest 530 billion-parameter Megatron-Turing model as shown in Figure 1. There is a growing need for customers to train and fine tune large models at an unprecedented scale.

Figure 1: Landscape of large models and hardware capabilities

To train these models, users needed to set up and maintain a complex distributed training infrastructure that usually required several manual and error-prone steps. These lead to a subpar experience both in terms of usability and performance. We recently announced how we are making great strides to simplify this and enable easy-to-use and high-performance training at 1K+ GPU scale on Azure.

In this extended post, we share the details of how DeepSpeed users can train trillion-parameter models with a new easy-to-use, streamlined, scalable, and high-performance distributed training experience on Azure. We also share details of the experimental setup, model configurations, additional performance trends, and guide our users on how to run these experiments in their own environments.

We compare the existing manual and error-prone workflow with our proposed easy-to-use workflow for DeepSpeed on Azure in Figure 2. Customers can now use easy-to-use training pipelines to launch training jobs at scale. The new workflow reduces the number of steps from 11 to just 1 if users rely on the recommended AzureML recipes.

Figure 2: An easy-to-use and streamlined distributed training experience with DeepSpeed on Azure

For users who have custom environments built using Azure VMs or Azure VMSS, only two steps are needed:

We already shared a summary of our key performance results in the Azure announcement. We enable the capability to train 2x larger model sizes (2 trillion vs. 1 trillion parameters), scale to 2x more GPUs (1024 vs. 512), and offer up to 1.8x higher compute throughput/GPU (150 TFLOPs vs. 81 TFLOPs) compared to other cloud providers.

DeepSpeed on Azure offers near-linear scalability both in terms of increase in model size as well as increase in number of GPUs. As shown in Figure 3a, together with the DeepSpeed ZeRO-3, its novel CPU offloading capabilities, and a high-performance Azure stack powered by InfiniBand interconnects and A100 GPUs, we were able to maintain an efficient throughput/GPU (>157 TFLOPs) in a near-linear fashion as the model size increases from 175 billion parameters to 2 trillion parameters. On the other hand, for a given model size, e.g., 175B, we achieve near-linear scaling as we increase the number of GPUs from 128 all the way to 1024 as shown in Figure 3b. The key takeaway is that Azure and DeepSpeed together are breaking the GPU memory wall and enabling our customers to easily and efficiently train trillion-parameter models at scale.

Figure 3: (a) Near-perfect throughput/GPU as we increase the model size from 175 billion to 2 trillion parameters (BS/GPU=8). (b) Near-perfect performance scaling with the increase in number of GPU devices for the 175B model (BS/GPU=16). The sequence length is 1024 for both cases.

We share the details of our experimental setup and some of the best practices we followed. The users can either directly use them to reproduce our results or modify them to fit their own setup in terms of model scale as well as the scale of Azure hardware being provisioned.

We used NDm A100 v4-series instances in our experiments. Each instance includes two socket AMD EPYC 7V12 64-Core CPUs, 1.7TB main memory and eight A100 80GB GPUs. The system has a balanced PCIe topology connecting 4 GPU devices to each CPU socket. Each GPU within the VM is provided with its own dedicated, topology-agnostic 200 Gb/s NVIDIA Mellanox HDR InfiniBand connection providing an accelerated 200 Gbps high speed fabric. The DeepSpeed library exploits offload capabilities where the activation and optimizer states are allocated in the main memory. Hence, 1.7TB memory capacity per node helps us to scale to large model sizes.

Users can directly use the AzureML studio and use our published recipes to run experiments without any additional setup. This is the easiest and recommended way of running experiments on Azure.

Existing VMSS customers and others who have custom Azure VM based environments can follow the setup as follows. The scripts to make these steps easy will be released in the coming weeks. A cluster is created using Azure Virtual Machine Scale Sets (VMSS) to provision the desired number of compute nodes running the new Azure HPAI VM image specialized for extreme-scale deep learning applications using the software stack listed in Table 1.

Table 1: Detailed version information of the software packages in the Azure HPC VM image

Users can create a VMSS with up to 600 VM instances enabling up to 4,800 A100 GPUs. In addition to the VMSS for the compute nodes, we provision a distinct login node using an inexpensive D4s v4 (or similar) instance with 4-core Intel VCPU, running the same image, for compiling, launching, and monitoring jobs. The login node, compute nodes, and a shared storage filesystem are grouped within an Azure Virtual Network (vnet) allowing VMs to connect to each other over SSH and to shared NFS volume shown in Figure 4.

Figure 4: Organization of our VMSS-based experimental setup

We ran our experiments with four different model sizes – 175B, 530B, 1T, and 2T – using the configurations shown in Table 2.

Table 2: Model configuration

For each of these configurations, we report peak throughput of the system using TFLOPs/GPU as the main performance metric. To calculate TFLOPs, we use the formula used by the Megatron paper as shown below.

FLOPs/GPU = 96 * B * s * l * h2 * (1 + s/6h + V/(16*l*h))

B is batch size, s is sequence length, l is the number of layers, h is hidden size, and V is vocabulary size.

Figures 5a and 5b show the results of 175B model with sequence length 512 and 1024, respectively. We only scale to 512 GPUs for seq-length 512 as adding more GPUs shows similar performance. On the other hand, with sequence length 1024, we saw linear performance increase to 1024 GPUs. Overall, the peak throughput of 204.49 TFLOPs/GPU was achieved on 256 GPUs with a micro batch size of 32 and sequence length of 512.

Figure 5: Performance characteristics of 175B model on 512 and 1K GPUs respectively. The colored columns signify different micro batch sizes.

Next, we report the 530B model scaling. Previous results on the 530B MT-NLG model using DeepSpeed and Megatron-LM on 280 DGX A100 servers on the Selene supercomputer showed the peak throughput of 126 TFLOPS/GPU. However, we were able to surpass that throughput and achieved up to 171.37 TFLOPs/GPU on 128 NDm A100 v4-series A100 systems (i.e., 1024 GPUs) as shown in Figure 6.

The benefit of this 530B model is its simpler parallelization configuration as there is no tensor/pipeline parallelism. With ZeRO powered data parallelism, there are fewer heuristics required to optimally configure the distributed model. In addition, the consistent steady state performance of more than 140 TFLOPs/GPU for micro batch sizes >1 demonstrates a robust software and hardware platform.

Figure 6: Throughput achieved with a 530B parameter model on 512 and 1024 GPUs for micro-batch sizes per GPU of 1, 2, 4, and 8, with sequence length 1,024.

The 1T parameter model contains 128 layers with 160 attention heads. Training such an extreme-scale model is not an easy task. Figure 7 shows the throughput achieved for each of the model configurations we explored on 512 and 1024 GPUs. Peak throughput achieved was 165.36 TFLOPs/GPU for micro batch size of 8 across 1024 GPUs and the model reached steady state performance within the first 3-4 iterations.

Figure 7: Performance characteristics of 1T parameter model on 512 and 1024 GPUs with 1, 2, 4, and 8 micro batch sizes, with sequence length 1,024.

The 2T parameter model consists of 160 layers, 32k hidden dimension, and 128 attention heads. Given the large size of the model and the significant time required on 1024 GPUs, we limited our benchmark runs for the 2T model to a batch size of 8 per GPU with a sequence length of 1024. We were able to achieve 157 TFLOPs/GPU on 1,024 GPUs.

We recognize that DeepSpeed users are diverse and have different environments. In this tutorial, our focus is on making things simpler for users who plan to run large model training experiments on Azure.

The easiest way to do model training on Azure is via the Azure ML recipes. The job submission and data preparation scripts have been made available here. Users simply need to setup their Azure ML workspace following the guide and submit experiment using the aml_submit.py file.

Some users have customized environments built on top of Azure VMs and VMSS based clusters. To simplify training on such setups, we are working on an easy-to-use cluster setup script that will be published in the next few weeks. If you already have a cluster setup running, you can use the azure recipes for the 175B and the 1T model. The recipes can easily be modified to train other model configurations.

This blog post was written by the DeepSpeed team in collaboration with the AzureML and the AzureHPC team. We would like to acknowledge several individuals who made this work possible:

Updated: July 25, 2022

---

## DeepSpeed Data Efficiency: A composable library that makes better use of data, increases training efficiency, and improves model quality

**URL:** https://www.deepspeed.ai/2022/12/11/data-efficiency.html

**Contents:**
- DeepSpeed Data Efficiency: A composable library that makes better use of data, increases training efficiency, and improves model quality
    - Contents
- Efficient Data Sampling via Curriculum Learning
- Motivation
- Design
- Evaluation Results
- Efficient Data Routing via Random Layerwise Token Dropping
- Motivation
- Design
- Evaluation Results

Recently, large-scale deep learning models are empowering us to achieve more in many ways, such as improving programming efficiency by code generation and providing art inspiration by text-to-image generation. To enable these services and keep improving the quality, deep learning model architecture evolves rapidly, and the model size is also growing at a tremendous speed. For example, from GPT to GPT-3 the model size increased 1500x in 2 years. The increasing model size leads to unprecedented training cost, making it challenging for many AI practitioners to train their own models. On the other hand, a less-emphasized perspective is that data scale is actually increasing at a similar speed as model scale, and the training cost is proportional to both of them. In Figure 1 below we plot the model and data scales of several representative language models in the last 5 years. From the oldest model on the left to the newest models on the right, both the model and data scales increase at similar speed. This demonstrates the importance of improving data efficiency: achieve same model quality with less data and reduced training cost, or achieve better model quality with the same amount of data and similar training cost.

Figure 1: Model scale (number of parameters) and data scale (number of tokens consumed during training) of representative language models in the last 5 years.

There are two popular research directions among existing data efficiency techniques: Data sampling techniques aim to improve the convergence speed by sampling the most suitable next data batch from the whole data pool; Data routing techniques aim to reduce the computation by routing each data to only a subset of the model components. These techniques improve data and training efficiency, but existing solutions on them have limitations on extensibility, flexibility, and composability. They are commonly designed for specific training tasks, making them hard to be extended with customized strategies and making them less flexible to be applied on diverse workloads from different users. Furthermore, different techniques are implemented separately, making it challenging to compose multiple solutions to further improve data and training efficiency.

To address these challenges, we, the DeepSpeed team as part of Microsoft’s AI at Scale initiative, are proud to announce DeepSpeed Data Efficiency Library – a composable framework that makes better use of data, increases training efficiency, and improves model quality. DeepSpeed Data Efficiency takes extensibility, flexibility, and composability into consideration, and it specifically demonstrates the following innovations:

Efficient data sampling via curriculum learning. Curriculum learning (CL) improves data efficiency by sampling from easier data. We present a general curriculum learning library which enables users to employ curriculum learning to their models at maximum extensibility: users can easily analyze, index, and sample their training data based on various customizable strategies. Using this library, we were able to explore different CL strategies for GPT-3 and BERT pretraining and identify the best solution that provides up to 1.5x data saving while still maintaining similar model quality.

Efficient data routing via random layerwise token dropping. We present a novel data routing technique called random layerwise token dropping (random-LTD) to skip the computation of a subset of the input tokens at all middle layers. Random-LTD employs a simple yet effective routing strategy and requires minimal model architecture change. It is flexible to apply random-LTD to various tasks (GPT-3/BERT pretraining and GPT/ViT finetuning), and we achieve great data efficiency improvement (up to 1.5x data saving while still maintaining the model quality).

Seamlessly composing multiple methods. The proposed DeepSpeed Data Efficiency framework seamlessly composes the curriculum learning and random-LTD techniques, and only requires minimal changes on the user code side. Furthermore, by composing both methods we can achieve even better data and training efficiency: for GPT-3 1.3B pretraining, we achieve 2x data and 2x time savings together with better or similar model quality compared to the baseline training. When using the same amount of data, our approach further improves the model quality over the baseline. Users can also extend and contribute to the library by adding additional data efficiency techniques to compose together.

Each of these advances is explored further in the blog post below. For more about the technical details, please read our papers, “Random-LTD: Random and Layerwise Token Dropping Brings Efficient Training for Large-scale Transformers” which describes the random-LTD technique, and “DeepSpeed Data Efficiency: Improving Deep Learning Model Quality and Training Efficiency via Efficient Data Sampling and Routing” which describes the curriculum learning technique and overall DeepSpeed Data Efficiency framework.

Curriculum learning aims to improve training convergence speed by presenting relatively easier or simpler examples earlier during training. Building a curriculum learning solution usually requires two components: the difficulty metric (i.e., how to quantify the difficulty of each data sample) and the pacing function (i.e., how to decide the curriculum difficulty range when sampling next training data batch). Curriculum learning has been successfully applied to various training tasks, and last year we also released a specific curriculum learning technique (sequence length warmup) for GPT-style model pretraining (see technical details in our paper “The Stability-Efficiency Dilemma: Investigating Sequence Length Warmup for Training GPT Models” published in NeurIPS 2022). However, one common limitation among existing works is that there does not exist a generalized and extensible curriculum learning library, which allows practitioners to easily apply custom curriculum difficulty metrics, the combination of metrics, and pacing functions.

To solve the limitation of existing solutions, we design and implement a general curriculum learning library emphasizing the extensibility. It consists of three components as shown in Figure 2 below (top part). First, we use a data analyzer to perform the offline CPU-only data analysis which indexes the whole data pool based on any difficulty metric such as the sequence length, the vocabulary rarity, or anything defined by user. Next, during training, the curriculum scheduler determines the difficulty threshold for the current step based on a pacing function such as linear, rooted, or any strategy provided by users. Then the data sampler will sample the data with desired difficulty from the indexed data pool. Overall, this general implementation would enable users to explore curriculum learning on their workloads with maximum customizability (more technical details in our DeepSpeed Data Efficiency paper).

Figure 2: Design of the DeepSpeed Data Efficiency framework.

Using this general and extensible curriculum learning solution for GPT-3 and BERT-Large model pretraining, we are able to easily analyze and index the huge training data based on up to 7 difficulty metrics and enable better data and training efficiency. For GPT-3 pretraining, our solution with the best difficulty metric (combination of truncation-based sequence length and vocabulary rarity) achieves 1.5x data and training cost saving while still maintaining model quality as baseline (Table 1 Case (8) vs. (1)). For BERT-Large pretraining, our solution with the best difficulty metric (vocabulary rarity) achieves 1.5x saving while still maintaining model quality (Table 2 Case (8) vs. (1)). On the other hand, our solutions can further improve model quality when using the same amount of data as baseline (Table 1 Case (2) to (6), Table 2 Case (2) to (6)).

Table 1: GPT-3 1.3B pretraining data consumption and average evaluation accuracy on 19 tasks.

Table 2: BERT-Large pretraining data consumption and average GLUE finetuning score on 8 tasks.

Standard data routing usually feeds the full images/sequences into all layers of a model. However, this process may not be optimal for training efficiency since some parts of an image (or words of a sentence) do not require a frequent feature update. As such, the token dropping method has been proposed, which is illustrated in Figure 3 (b) below, to skip the compute of some tokens/words (i.e., G-2 tokens in Figure 3 (b)) of a sentence in order to save the compute cost.

Although existing methods show promising results, they also exhibit several caveats: (1) most works solely focus on BERT (encoder-only on text data) pretraining and do not include decoder pretraining and/or other modalities (e.g., images); (2) the ability to skip layers is limited, which bounds the total amount of compute saving. By analyzing existing methods, we found out the potential main issue that limits their skipping and coverage abilities is the loss of attention mechanism for G-2 tokens for all skipped layers, since multi-head attention focuses on different tokens at different layer depths and the attention map aligns with the dependency relation most strongly in the middle of transformer architectures.

To resolve this main issue, we propose random-LTD, a random and layerwise token dropping mechanism, which processes only a subset of tokens among the entire data batch for all middle layers in order to save compute cost (see more details in our Random-LTD paper). As such, each token rarely bypasses all middle layers and its dependency with other tokens can be captured by the model. The illustration of random-LTD compared to baseline is shown in Figure 3 below, where random-LTD splits the input tokens into two groups and only the first group involves the compute.

Figure 3: Comparison between baseline, existing token dropping methods, and random-LTD. Note that for random-LTD, only part of the inputs (Group 1) is used for Layer i.

Random-LTD is simple yet very effective. Particularly, compared to other existing token dropping methods, random-LTD (1) does a purely random selection for each layer for two different groups, as such we do not require any expert design for the selection criterion; (2) is able to apply to all middle layers to achieve better saving ratio; (3) demonstrates great generalizability for both encoder and decoder models; and (4) is easy to use without much modeling change. These advantages enable maximum flexibility when applying random-LTD to various workloads.

Thanks to its great flexibility, we were able to apply random-LTD method to broader applications, including BERT and GPT pretraining as well as ViT and GPT finetuning tasks. For all cases, random-LTD achieves similar model quality as baseline while using less data, and/or achieve better model quality while using the same amount of data (Table 3 to 6). For GPT-3 and BERT-Large pretraining, random-LTD achieves 1.5-2x data saving while still maintaining the same model quality. For GPT-3 we also tested random-LTD with full data which further improves the model quality compared to baseline.

Table 3: GPT-3 1.3B pretraining data consumption and average evaluation accuracy on 19 tasks.

Table 4: BERT-Large pretraining data consumption and average GLUE finetuning score on 8 tasks.

Table 5: Finetuning result of ViT on ImageNet.

Table 6: GPT-2 350M finetuning result on the PTB task.

The curriculum learning and random-LTD techniques are complementary. Inside DeepSpeed Data Efficiency framework, we seamlessly compose the two techniques as shown in Figure 2 above, where curriculum learning helps to sample the next data batch and random-LTD helps to decide how to route each sampled data inside the model. DeepSpeed Data Efficiency solves several complexities when composing the two techniques so that users can easily apply each technique or both to their training pipeline. The composability of DeepSpeed Data Efficiency also applies to data sampling and routing techniques in general, so that it provides a platform to implement and compose additional data efficiency techniques.

The composed DeepSpeed Data Efficiency solution leverages both data efficiency techniques and achieves even better data and training efficiency. Take the GPT-3 pretraining task as an example, composing CL and random-LTD, with 100% data, leads to the best model quality in our experiments (Table 7 Case (1) to (4)). When pretraining with 50% data, the baseline training results in worse zero-shot and 10-shot evaluation accuracy, and using either CL or random-LTD can only recover part of the 10-shot accuracy loss. On the other hand, the composed data efficiency solution achieves the same or better accuracy results as baseline with 100% data, demonstrating a 2x data and 2x time saving (Case (5) to (8)). Similar benefit such as 2x data saving was also observed when applying our solution to BERT pretraining.

Table 7: GPT-3 1.3B pretraining data/time consumption and average evaluation accuracy on 19 tasks.

We are very excited to share DeepSpeed Data Efficiency library with the community and improve it with your feedback. Please find the code, tutorial, and documents at the DeepSpeed GitHub, and website. And for more technical details please read our Random-LTD paper and DeepSpeed Data Efficiency paper. We believe that our composable library and novel data efficiency techniques will help users reduce training cost while maintaining model quality or achieve better quality under similar cost. And we hope DeepSpeed Data Efficiency could become a platform that motivates and accelerates future research on deep learning data efficiency.

Updated: December 11, 2022

---

## DeepSpeed Inference: Multi-GPU inference with customized inference kernels and quantization support

**URL:** https://www.deepspeed.ai/2021/03/15/inference-kernel-optimization.html

**Contents:**
- DeepSpeed Inference: Multi-GPU inference with customized inference kernels and quantization support
    - Contents
- Multi-GPU Inference with Adaptive Parallelism
- Customized Inference Kernels for Boosted Compute Efficiency of Transformer Blocks
- Kernel-Fusion
- Seamless pipeline from training to inference with automatic kernel-injection
- Flexible quantization support
- Performance results

While DeepSpeed supports training advanced large-scale models, using these trained models in the desired application scenarios is still challenging due to three major limitations in existing inference solutions: 1) lack of support for multi-GPU inference to fit large models and meet latency requirements, 2) limited GPU kernel performance when running inference with small batch sizes, and 3) difficulties in exploiting quantization, which includes both quantizing the model to reduce the model size and latency as well as supporting high-performance inference of quantized models without specialized hardware.

To handle these challenges, we introduce DeepSpeed Inference, which seamlessly adds high-performance inference support to large models trained in DeepSpeed with three key features: inference-adapted parallelism for multi-GPU inference, inference-optimized kernels tuned for small batch sizes, and flexible support for quantize-aware training and inference kernels for quantized models.

Parallelism is an effective approach to fit large models and reduce per-device memory consumption for both training and inference. However, simply applying training parallelism choices and degree to inference does not work well. The MP and PP configuration is normally set during the model training, apart from the data parallelism (DP), based on the memory footprint and computation style, and resource budget. On one hand, inference computation intrinsically requires less memory, so it can afford a larger partition per device. It helps reduce the degree of parallelism needed for model deployment. On the other hand, optimizing latency or meeting latency requirements is often a first-class citizen in inference while training optimizes throughput.

To obtain desired latency, DeepSpeed Inference automatically adapts MP as an effective approach to reduce model latency, and its parallelism degree is often determined first. With MP, we can split the mode and parallelize computational operations across multiple devices (GPUs) to reduce latency, but it reduces computation granularity and increases communication that may hurt throughput. Once the latency target has been met, DeepSpeed can apply pipeline parallelism to maximize the throughput. Overall, DeepSpeed Inference supports flexible adaptation of both parallelism approach and degree choices from training to inference, minimizing latency while saving deployment costs.

To achieve high compute efficiency, DeepSpeed-inference offers inference kernels tailored for Transformer blocks through operator fusion, taking model-parallelism for multi-GPU into account. The main difference between our kernel-fusion scheme and similar approaches is that we not only fuse element-wise operations (such as bias-add, residual, and activation function), but also merge the General matrix multiply (GeMM) operations with other operations. To do this, we design an efficient implementation for the vector-matrix or skinny matrix-matrix multiplication that allows us to fuse more operations at the reduction boundary of GeMM operations.

We take two main policies for fusing operations: 1) keeping the access-pattern of inputs and outputs intact throughout the sequence of operations fused together; 2) fusing operations at each all-reduce boundary. The first policy ensures that different thread-blocks won’t encounter transferring data between Streaming-Multiprocessors (SMs). This is due to no straight-forward communication among SMs other than using the main memory which adds the block-synching overhead because of non-deterministic behavior of memory access. The reason behind the second policy is that we cannot continue the execution unless the partial results are reduced among the model-parallel GPUs.

Figure 1: Transformer Layer with Megatron-style model-parallelism all-reduce components. The figure illustrates the parts of layer fused together with broken lines (width of line shows the fusion depth).

Figure 1 shows the different components of a Transformer layer, and the groups of operations considered for fusion in our inference optimization. We also consider the NVIDIA Megatron-LM style of parallelism that partitions attention (Attn) and feed-forward (FF) blocks across multiple GPUs. Thus, we include the two all-reduce operations that reduce the results among parallel GPUs after Attn and FF blocks. As Figure 1 shows, we fuse the operations inside a Transformer layer at four main regions:

To fuse these operations, we exploit shared-memory as an intermediate cache for transferring data between reduction operations used in layer-norm and GeMM, and the element-wise operations. Moreover, we use the warp-level instructions to communicate data between threads when reducing partial computations. In addition, we use a new schedule for GeMM operations, which allows for fusing as many operations as needed for the third kernel-fusion. We also combine the GeMMs for the attention computation in the second kernel-fusion, by using an implicit matrix transformation in order to reduce the memory pressure. Compared to the unfused computation style using cuBLAS GeMM, we improve the performance by 1.5x, 2.9x. 3x, and 1.2x for all these kernel-fusions, respectively.

To run the model in Inference mode, DeepSpeed simply requires the location of the model checkpoints and the desired parallelism configuration, i.e., MP/PP degree. DeepSpeed Inference kernels can also be enabled for many well-known model architectures such as HuggingFace (Bert and GPT-2) or Megatron GPT-based models using a pre-defined policy map that maps the original parameters to the parameters in the inference kernels. For other transformer-based models, user can specify their own policy map. Note that DS-Inference can run independent of the training pipeline as long as it receives all model checkpoints, and the DeepSpeed Transformer kernels for inference can be injected into any Transformer model if the right mapping policy is defined. For more information on how to enable Transformer inference kernel as well as specifying parallelism, please refer to out inference tutorial.

To further reduce the inference cost for large-scale models, we created the DeepSpeed Quantization Toolkit, supporting flexible quantize-aware training and high-performance kernels for quantized inference.

For training, we introduce a novel approach called Mixture of Quantization (MoQ), which is inspired by mixed-precision training while seamlessly applying quantization. With MoQ, we can control the precision of the model by simulating the impact of quantization when updating the parameters at each step of training. Moreover, it supports flexible quantization policies and schedules—we find that by dynamically adjusting the number of quantization bits during training, the final quantized model provides higher accuracy under the same compression ratio. To adapt to different tasks, MoQ can also leverage the second order information of models to detect their sensitivity to precision and adjust the quantization schedule and target accordingly.

To maximize the performance gains from the quantization model, we provide inference kernels tailored for quantized models that reduce latency through optimizing data movement but do not require specialized hardware. Finally, our toolkit does not require any code changes on the client side, making it easy to use.

Boosting throughput and reducing inference cost. Figure 3 shows the inference throughput per GPU for the three model sizes corresponding to the three Transformer networks, GPT-2, Turing-NLG, and GPT-3. DeepSpeed Inference increases in per-GPU throughput by 2 to 4 times when using the same precision of FP16 as the baseline. By enabling quantization, we boost throughput further. We reach a throughput improvement of 3x for GPT-2, 5x for Turing-NLG, and 3x for a model that is similar in characteristics and size to GPT-3, which directly translates to 3–5x inference cost reduction on serving these large models. In addition, we achieve these throughput and cost improvements without compromising latency as shown in Figure 5.

Figure 3: Inference throughput for different model sizes. DeepSpeed Inference achieves 3x to 5x higher throughput than baseline.

One source of inference cost reduction is through reducing the number of GPUs for hosting large models as shown in Figure 4. The optimized GPU resources comes from 1) using inference-adapted parallelism, allowing users to adjust the model and pipeline parallelism degree from the trained model checkpoints, and 2) shrinking model memory footprint by half with INT8 quantization. As shown in this figure, we use 2x less GPUs to run inference for the 17B model size by adapting the parallelism. Together with INT8 quantization through DeepSpeed MoQ, we use 4x and 2x fewer GPUs for 17B and 175B sizes respectively.

Figure 4: Number of GPUs used for running inference on the different model sizes shown in Figure 4.

Reducing inference latency. For the application scenarios where inference latency is critical, we can increase model parallelism degree in DeepSpeed Inference to reduce inference latency further. As Figure 5 depicts, we can reduce the latency by 2.3x compared to PyTorch as we increase the model-parallelism size to 4. Furthermore, we can still have high latency improvement with a fewer number of GPUs by adapting the parallelism at inference and using MoQ to quantize the model. We obtain 1.3x and 1.9x speedups while using 4x and 2x lower resources than baseline, respectively.

For the application scenarios where inference latency is critical, we can increase model parallelism degree in DeepSpeed Inference to reduce inference latency further. As Figure 5 depicts, we can reduce the latency by 2.3x compared to PyTorch as we increase the model-parallelism size to 4. Furthermore, we can still have high latency improvement with a fewer number of GPUs by adapting the parallelism at inference and using MoQ to quantize the model. We obtain 1.3x and 1.9x speedups while using 4x and 2x lower resources than baseline, respectively.

Figure 5. Inference latency for the 17B model using different parallelism configuration to optimize latency.

Updated: March 15, 2021

---

## Inference Overview and Features

**URL:** https://www.deepspeed.ai/inference/

**Contents:**
- Inference Overview and Features
    - Contents

DeepSpeed-Inference v2 is here and it’s called DeepSpeed-FastGen! For the best performance, latest features, and newest model support please see our DeepSpeed-FastGen release blog!

DeepSpeed-Inference introduces several features to efficiently serve transformer-based PyTorch models. It supports model parallelism (MP) to fit large models that would otherwise not fit in GPU memory. Even for smaller models, MP can be used to reduce latency for inference. To further reduce latency and cost, we introduce inference-customized kernels. Finally, we propose a novel approach to quantize models, called MoQ, to both shrink the model and reduce the inference cost at production. For more details on the inference related optimizations in DeepSpeed, please refer to our blog post.

DeepSpeed provides a seamless inference mode for compatible transformer based models trained using DeepSpeed, Megatron, and HuggingFace, meaning that we don’t require any change on the modeling side such as exporting the model or creating a different checkpoint from your trained checkpoints. To run inference on multi-GPU for compatible models, provide the model parallelism degree and the checkpoint information or the model which is already loaded from a checkpoint, and DeepSpeed will do the rest. It will automatically partition the model as necessary, inject compatible high performance kernels into your model and manage the inter-gpu communication. For list of compatible models please see here.

To get started with DeepSpeed-Inference, please checkout our tutorial.

---

## Mixture-of-Quantization: A novel quantization approach for reducing model size with minimal accuracy impact

**URL:** https://www.deepspeed.ai/2021/05/04/MoQ.html

**Contents:**
- Mixture-of-Quantization: A novel quantization approach for reducing model size with minimal accuracy impact
    - Contents
- A unified suite for quantization-aware training and inference
- Quantization methodology
- Quantized Inference Kernels
- Ease of use
- Improving quantization accuracy.

Running large-scale models on multi-GPU might help reduce latency but increases the deployment cost significantly, especially as the model size grows bigger. To mitigate this issue, we resort to model compression techniques and introduce a new methodology that quantizes Transformer networks with a minimal impact on accuracy. Our technique achieves similar or better performance thanFP16 models through customized inference kernels on lower or equal number of GPUs.

Our scheme is flexible in the sense that it provides users the ability to experiment with any quantization configuration, such as the target number of bits used for quantization precision, and the scheduling by which the model gets quantized during training. Furthermore, we combine both the FP16 and quantized precision as a mixed-precision mechanism to smooth the transition from a high to low precision. Finally, we use the second-order gradient (eigenvalue) of the parameters to adjust the quantization schedule during training.

There are two main approaches of applying quantization: offline quantization on the trained model and quantization-aware training (QAT) that reduces the data-precision during training. Unlike the former scheme, QAT gets the model trained by taking the impact of precision loss into account during the training optimization. This will result in significant improvement of the quantized model accuracy. MoQ is designed on top QAT approach, with the difference that we use a mixture of precisions to train the model toward target quantization, as well as defining a scheduling for reducing the precision.

All existing QAT approaches quantize the model with a certain precision (number of bits) from the beginning of training until completion. However, even by using a relatively high quantization precision (8-bit), there will be some drop in model accuracy, which might not be acceptable for some downstream tasks. For instance, the Q8BERT work tries QAT for the BERT network, which results in good accuracy for some tasks while others (like SQuAD) lose 0.8% in the F1 score. Other techniques, such as Q-BERT, use grouped quantization with a large grouping size (128) when quantizing a parameter matrix to gain higher accuracy, but they are still inferior to the baseline.

Here, we present MoQ as a flexible solution for linear quantization that allows users to define a schedule as the model trains. Similar to iterative pruning to inject sparsity, we start quantization from a higher precision (16-bit quantization or FP16) and gradually reduce the quantization bits or the mixed-precision ratio for the FP16 part until reaching a target precision (8-bit). To control the precision transition, we define a hyperparameter, called quantization period, that indicates when the precision reduction should happen. We observe that by using such a schedule, we get the closest accuracy to the baseline. Note that in order to reach a certain precision, we need to define the starting bits and period in a way that within the number of samples to train, the model eventually gets quantized using the target number of bits. Please refer to the quantization tutorial for more information.

In order to dynamically adjust quantization precision, we employ eigenvalue as a metric that shows the sensitivity of training to the precision change. Eigenvalue has been previously used (Q-BERT) for quantization to choose the precision bits on different parts of the network. To combine this with MoQ, we cluster the eigenvalues into several regions based on their absolute values and tune the quantization period for each region accordingly, the higher the magnitude of eigenvalue, the larger the factor and the slower the precision decreases.

Figure 1. Quantization scheduling of one of the GLUE tasks (QNLI), using the eigenvalue of different layers. Different colors show the layers from 0 to 11 for Bert-Base.

Figure 1 shows the result of combining eigenvalue with MoQ for a 12-layer Bert Base model. As we see, the first few layers (0-4) tend to be more sensitive to reduced precision than the last layers, as their quantization period is an order of magnitude larger than the rest. Another observation from this figure is that the neighbor layers reduce the precision in the same way. For instance, layers 9, 10, and 11 on the left chart, and layers 0 and 4 and 1 and 3 on the right chart of Figure 1 get similar schedule. This is due to having similar eigenvalues for these layers throughout the training.

Figure 2: Mixed-precision quantization for the QNLI using target quantization period as 4 bits.

Figure 2 shows another mixed-precision quantization that sets target bits as 4, however the quantization period keeps updated through the eigenvalues of each layer. As we see, the end quantization bits are different for all layers. The first layers still get to 8-bit quantization as the training samples is not enough to decrease the quantization bits. On the other hand, the last layers keep reducing the precision. We finally reduce the average precision to 6 bits for the entire network while maintaining the accuracy of the model (0.3% drop in accuracy).

Figure 3: Mixed-precision quantization with MoQ for Bert SQuAD plus.

As another example, we use eigenvalue-based MoQ to quantize Bert-Large for SQuAD finetuning. Figure 3 shows the number of bits we get to at the end of finetuning on each layer. Here, we see slightly different precision spectrum compared to BertBase on GLUE tasks. As the figure shows, we can reduce the precision on the first few layers more aggressively than the middle ones. Also, the last few layers can tolerate very low precision similar to the beginning layers. This way of quantization finally results in 90.56 F1 Score which is pretty similar to the baseline.

By using other quantization methodologies, after the model is quantized, it can only have performance benefit if there is hardware support for integer-based operations. For this reason, the inputs and output of all GeMM operations need to be quantized. However, since the range of input may vary request by request, finding a range of data for each input at inference time is challenging. On the other hand, using a static range for all inputs can impact the inference accuracy.

To alleviate this problem, we introduce inference custom kernels that neither require the hardware support nor the input quantization. These kernels read quantized parameters and dequantize them on-the-fly and use the floating-point units of GPU cores for the GeMM operations. The main benefit of using these kernels is that they reduce the memory footprint required to load a model so that we can run inference on fewer number of GPUs, while improving the performance by saving the memory bandwidth required to run the inference on GPU.

Regarding the quantization implementation, we use different algorithms to quantize a value based on the range of data and the rounding policy. We support both symmetric and asymmetric quantization as the two mostly used schemes. We applied both techniques for QAT and see very similar results, however since symmetric approach is simpler to implement, we implement our inference kernels based on that. Regarding the rounding, we support stochastic rounding as another option besides the normal rounding. We have seen that for reducing the precision to as low as 4-bit or lower, stochastic rounding is more helpful as it has an unbiased random behavior during training.

For enabling quantization through Deepspeed, we only need to pass the scheduling through a JSON configuration file. To add the impact of quantization, we quantize and dequantize the parameters just before they are updated in the optimizer. Thus, we do not incur any change on the modeling side to quantize a model. Instead, we simulate the quantization impact by lowering the precision of data saved in FP16 format. By using this kind of implementation, we have the full flexibility of changing the precision using the training characteristics such as number of steps, and eigenvalue of the parameters and the original FP16 data format. As shown in this blog post, we can improve the quality of a quantized model by adaptively changing the scheduling of the quantization throughout training. For more information on how to use MoQ scheme, please look at our quantization tutorial.

To show how our quantization scheme preserves accuracy, we have experimented MoQ on several tasks and networks: GLUE tasks on Bert-Base and SQuAD on Bert-Large. Table 1 shows the accuracy results for the baseline without quantization (w/o Quant), basic quantization without using any scheduling during training (Basic Quant), and our MoQ scheme. Without using any scheduling, the accuracy for 8-bit quantization is often inferior to the baseline, and in this workload, it suffers from a drop of 1.02 point in accuracy (ACC). In contrast, MoQ powers 8-bit quantization to obtain comparable accuracy as the FP16 baseline, even with a slightly higher ACC, demonstrating the effectiveness of our quantization approach.

---

## DeepSpeed: Accelerating large-scale model inference and training via system optimizations and compression

**URL:** https://www.deepspeed.ai/2021/05/14/inference-release.html

**Contents:**
- DeepSpeed: Accelerating large-scale model inference and training via system optimizations and compression
    - Contents

Updated: May 14, 2021

---

## Autotuning: Automatically discover the optimal DeepSpeed configuration that delivers good training speed

**URL:** https://www.deepspeed.ai/2021/11/16/autotuning.html

**Contents:**
- Autotuning: Automatically discover the optimal DeepSpeed configuration that delivers good training speed

We introduce a new feature called Autotuning to automatically discover the optimal DeepSpeed configuration that delivers good training speed. One pain point in model training is to figure out good performance-relevant configurations such as micro-batch size to fully utilize the hardware and achieve a high throughput number. This configuration exploring process is commonly done manually but is important since model training is repeated many times and benefits from using a good configuration. Not only is the hand-tuning process time-consuming, but the outcome is hardware-dependent. This means that a good configuration on one hardware might not be the best on another different hardware. The user thus has to hand tune the configuration again. With DeepSpeed, there are more configuration parameters that could potentially affect the training speed, thus making it more tedious to manually tune the configuration.

The DeepSpeed Autotuner mitigates this pain point and automatically discovers the optimal DeepSpeed configuration that delivers good training speed. It not only reduces the time and resources users spend on tuning, but also can discover configurations better than hand-tuned methods. DeepSpeedExamples would demonstrate the effectiveness of autotuning across different models.

Updated: November 16, 2021

---

## Contributing

**URL:** https://www.deepspeed.ai/contributing/

**Contents:**
- Contributing
    - Contents
- Prerequisites
- Testing
  - Unit Tests
  - Model Tests
- Contributor License Agreement
- Code of Conduct

DeepSpeed welcomes your contributions!

DeepSpeed uses pre-commit to ensure that formatting is consistent across DeepSpeed. First, ensure that pre-commit is installed from either installing DeepSpeed or pip install pre-commit. Next, the pre-commit hooks must be installed once before commits can be made:

Afterwards, our suite of formatting tests run automatically before each git commit. You can also run these manually:

If a formatting test fails, it will fix the modified code in place and abort the git commit. After looking over the changes, you can git add <modified files> and then repeat the previous git commit command.

DeepSpeed tracks two types of tests: unit tests and more costly model convergence tests. The model convergence tests train DeepSpeedExamples and measure end-to-end convergence and related metrics. Unit tests are found in tests/unit/ and the model convergence tests are found in tests/model/.

PyTest is used to execute tests. PyTest can be installed from PyPI via pip install pytest. Simply invoke pytest --forked to run the unit tests:

You can also provide the -v flag to pytest to see additional information about the tests. Note that pytest-forked and the --forked flag are required to test CUDA functionality in distributed tests.

Model tests require four GPUs and training data downloaded for DeepSpeedExamples.

To execute model tests, first install DeepSpeed. The DeepSpeedExamples repository is cloned as part of this process. Next, execute the model test driver:

Note that the --forked flag is not necessary for the model tests.

This project welcomes contributions and suggestions. Most contributions require you to agree to a Contributor License Agreement (CLA) declaring that you have the right to, and actually do, grant us the rights to use your contribution. For details, visit https://cla.opensource.microsoft.com.

When you submit a pull request, a CLA bot will automatically determine whether you need to provide a CLA and decorate the PR appropriately (e.g., status check, comment). Simply follow the instructions provided by the bot. You will only need to do this once across all repos using our CLA.

This project has adopted the Microsoft Open Source Code of Conduct. For more information see the Code of Conduct FAQ or contact opencode@microsoft.com with any additional questions or comments.

**Examples:**

Example 1 (unknown):
```unknown
pre-commit install
```

Example 2 (unknown):
```unknown
pre-commit run --all-files
```

Example 3 (unknown):
```unknown
pytest --forked tests/unit/
```

Example 4 (unknown):
```unknown
cd tests/model/
pytest run_sanity_check.py
```

---

## Latest News

**URL:** https://www.deepspeed.ai

**Contents:**
- Latest News
    - Contents
- Extreme Speed and Scale for DL Training
- DeepSpeed Adoption
- Contributing
- Contributor License Agreement
- Code of Conduct
- Publications
- Videos

[2025/10] SuperOffload: Unleashing the Power of Large-Scale LLM Training on Superchips

[2025/10] Study of ZenFlow and ZeRO offload performance with DeepSpeed CPU core binding

[2025/08] ZenFlow: Stall-Free Offloading Engine for LLM Training

[2025/06] Arctic Long Sequence Training (ALST) with DeepSpeed: Scalable And Efficient Training For Multi-Million Token Sequences

[2025/06] DeepNVMe: Affordable I/O scaling for Deep Learning Applications

DeepSpeed enabled the world’s most powerful language models (at the time of this writing) such as MT-530B and BLOOM. DeepSpeed offers a confluence of system innovations, that has made large scale DL training effective, and efficient, greatly improved ease of use, and redefined the DL training landscape in terms of scale that is possible. These innovations include ZeRO, 3D-Parallelism, DeepSpeed-MoE, ZeRO-Infinity, etc.

DeepSpeed has been used to train many different large-scale models. Below is a list of several examples that we are aware of (if you’d like to include your model please submit a PR):

DeepSpeed has been integrated with several different popular open-source DL frameworks such as:

DeepSpeed is an integral part of Microsoft’s AI at Scale initiative to enable next-generation AI capabilities at scale.

DeepSpeed welcomes your contributions! Please see our contributing guide for more details on formatting, testing, etc.

This project welcomes contributions and suggestions. Most contributions require you to agree to a Contributor License Agreement (CLA) declaring that you have the right to, and actually do, grant us the rights to use your contribution. For details, visit https://cla.opensource.microsoft.com.

When you submit a pull request, a CLA bot will automatically determine whether you need to provide a CLA and decorate the PR appropriately (e.g., status check, comment). Simply follow the instructions provided by the bot. You will only need to do this once across all repos using our CLA.

This project has adopted the Microsoft Open Source Code of Conduct. For more information see the Code of Conduct FAQ or contact opencode@microsoft.com with any additional questions or comments.

Xinyu Lian, Sam Ade Jacobs, Lev Kurilenko, Masahiro Tanaka, Stas Bekman, Olatunji Ruwase, Minjia Zhang. (2024) Universal Checkpointing: Efficient and Flexible Checkpointing for Large Scale Distributed Training arXiv:2406.18820

---




### Tutorials

# Deepspeed - Tutorials

**Pages:** 59

---

## DeepNVMe

**URL:** https://www.deepspeed.ai/tutorials/deepnvme/

**Contents:**
- DeepNVMe
    - Contents
- Requirements
- Creating DeepNVMe Handles
- Using DeepNVMe Handles
  - Blocking File Write
  - Non-Blocking File Write
  - Parallel File Write
  - Pinned Tensors
- Putting it together

This tutorial will show how to use DeepNVMe for data transfers between persistent storage and tensors residing in host or device memory. DeepNVMe improves the performance and efficiency of I/O operations in Deep Learning applications through powerful optimizations built on Non-Volatile Memory Express (NVMe) Solid State Drives (SSDs), Linux Asynchronous I/O (libaio), and NVIDIA Magnum IOTM GPUDirect® Storage (GDS).

Ensure your environment is properly configured to use DeepNVMe. First, you need to install DeepSpeed version >= 0.15.0. Next, ensure that the DeepNVMe operators are available in the DeepSpeed installation. The async_io operator is required for any DeepNVMe functionality, while the gds operator is required only for GDS functionality. You can confirm availability of each operator by inspecting the output of ds_report to check that compatible status is [OKAY]. Below is a snippet of ds_report output confirming the availability of both async_io and gds operators.

If async_io operator is unavailable, you will need to install the appropriate libaio library binaries for your Linux flavor. For example, Ubuntu users will need to run apt install libaio-dev. In general, you should carefully inspect ds_report output for helpful tips such as the following:

To enable gds operator, you will need to install NVIDIA GDS by consulting the appropriate guide for bare-metal systems or Azure VMs (coming soon).

DeepNVMe functionality can be accessed through two abstractions: aio_handle and gds_handle. The aio_handle is usable on both host and device tensors. while gds_handle works only on CUDA tensors, but is more efficient. The first step to use DeepNVMe is to create a desired handle. aio_handle requires async_io operator, while gds_handle requires both async_io and gds operators. The following snippets illustrate aio_handle and gds_handle creation respectively.

For simplicity, the above examples illustrate handle creation using default parameters. We expect that handles created with default parameters to provide good performance in most environments. However, you can see below for advanced handle creation.

aio_handle and gds_handle provide identical APIs for storing tensors to files or loading tensors from files. A common feature of these APIs is that they take a tensor and a file path as arguments for the desired I/O operation. For best performance, pinned device or host tensors should be used for I/O operations (see here for details). For brevity, this tutorial will use aio_handle for illustration, but keep in mind that gds_handle works similarly.

You can see the available APIs in a Python shell via tab completion on an aio_handle object . This is illustrated using tab completion of h..

The APIs of interest for performing I/O operations are those named with pread and pwrite substrings. For brevity, we will focus on the file write APIs, namely sync_pwrite, async_pwrite, and pwrite. We will discuss only sync_pwrite and async_pwrite below because they are specializations of pwrite.

sync_pwrite provides the standard blocking semantics of Python file write. The example below illustrates using sync_pwrite to store a 1GB CUDA tensor to a local NVMe file.

An important DeepNVMe optimization is the non-blocking I/O semantics which enables Python threads to overlap computations with I/O operations. async_pwrite provides the non-blocking semantics for file writes. The Python thread can later use wait() to synchronize with the I/O operation. async_write can also be used to submit multiple back-to-back non-blocking I/O operations, of which can then be later blocked on using a single wait(). The example below illustrates using async_pwrite to store a 1GB CUDA tensor to a local NVMe file.

Warning for non-blocking I/O operations: To avoid data races and corruptions, .wait() must be carefully used to serialize the writing of source tensors, and the reading of destination tensors. For example, the following update of t during a non-blocking file write is unsafe and could corrupt /local_nvme/test_1GB.pt.

Similar safety problems apply to reading the destination tensor of a non-blocking file read without .wait() synchronization.

An important DeepNVMe optimization is the ability to parallelize individual I/O operations. This optimization is enabled by specifying the desired parallelism degree when constructing a DeepNVMe handle. Subsequent I/O operations with that handle are automatically parallelized over the requested number of host or device threads, as appropriate. I/O parallelism is composable with either the blocking or non-blocking I/O APIs. The example below illustrates 4-way parallelism of a file write using async_pwrite. Note the use of intra_op_parallelism argument to specify the desired parallelism degree in handle creation.

A key part of DeepNVMe optimizations is using direct memory access (DMA) for I/O operations, which requires that the host or device tensor be pinned. To pin host tensors, you can use mechanisms provided by Pytorch or DeepSpeed Accelerators. The following example illustrates writing a pinned CPU tensor to a local NVMe file.

On the other hand,gds_handle provides new_pinned_device_tensor() and pin_device_tensor() functions for pinning CUDA tensors. The following example illustrates writing a pinned CUDA tensor to a local NVMe file.

We hope that the above material helps you to get started with DeepNVMe. You can also use the following links to see DeepNVMe usage in real-world Deep Learning applications.

This tutorial has been significantly improved by feedback from Guanhua Wang, Masahiro Tanaka, and Stas Bekman.

Achieving peak I/O performance with DeepNVMe requires careful configuration of handle creation. In particular, the parameters of aio_handle and gds_handle constructors are performance-critical because they determine how efficiently DeepNVMe interacts with the underlying storage subsystem (i.e., libaio, GDS, PCIe, and SSD). For convenience we make it possible to create handles using default parameter values which will provide decent performance in most scenarios. However, squeezing out every available performance in your environment will likely require tuning the constructor parameters, namely block_size, queue_depth, single_submit, overlap_events, and intra_op_parallelism. The aio_handle constructor parameters and default values are illustrated below:

As discussed earlier, achieving peak DeepNVMe performance for a target workload or environment requires using optimally configured aio_handle or gds_handle handles. For configuration convenience, we provide a utility called ds_nvme_tune to automate the discovery of optimal DeepNVMe configurations. ds_nvme_tune automatically explores a user-specified or default configuration space and recommends the option that provides the best read and write performance. Below is an example usage of ds_nvme_tune to tune aio_handle data transfers between GPU memory and a local NVVMe SSD mounted on /local_nvme. This example used the default configuration space of ds_nvme_tune for tuning.

The above tuning was executed on a Lambda workstation equipped with two NVIDIA A6000-48GB GPUs, 252GB of DRAM, and a CS3040 NVMe 2TB SDD with peak read and write speeds of 5.6 GB/s and 4.3 GB/s respectively. The tuning required about four and half minutes. Based on the results, one can expect to achieve read and write transfer speeds of 3.69 GB/sec and 3.18 GB/sec respectively by using an aio_handle configured as below.

The full command line options of ds_nvme_tune can be obtained via the normal -h or --help.

For convenience, we provide listing and brief descriptions of the DeepNVMe APIs.

The following functions are used for I/O operations with both aio_handle and gds_handle.

The following functions are available only for gds_handle

The following APIs can be used to probe handle configuration.

Updated: November 5, 2025

**Examples:**

Example 1 (unknown):
```unknown
[WARNING]  async_io requires the dev libaio .so object and headers but these were not found.
[WARNING]  async_io: please install the libaio-dev package with apt
[WARNING]  If libaio is already installed (perhaps from source), try setting the CFLAGS and LDFLAGS environment variables to where it can be found.
```

Example 2 (python):
```python
### Create aio_handle
from deepspeed.ops.op_builder import AsyncIOBuilder
aio_handle = AsyncIOBuilder().load().aio_handle()
```

Example 3 (python):
```python
### Create gds_handle
from deepspeed.ops.op_builder import GDSBuilder
gds_handle = GDSBuilder().load().gds_handle()
```

Example 4 (python):
```python
>python
Python 3.10.12 (main, Jul 29 2024, 16:56:48) [GCC 11.4.0] on linux
Type "help", "copyright", "credits" or "license" for more information.
>>> from deepspeed.ops.op_builder import AsyncIOBuilder
>>> h = AsyncIOBuilder().load().aio_handle()
>>> h.
h.async_pread(             h.free_cpu_locked_tensor(  h.get_overlap_events(      h.get_single_submit(       h.new_cpu_locked_tensor(   h.pwrite(                  h.sync_pread(              h.wait(
h.async_pwrite(            h.get_block_size(          h.get_queue_depth(         h.get_intra_op_parallelism(        h.pread(                   h.read(                    h.sync_pwrite(             h.write(
```

---

## DeepSpeed Data Efficiency: A composable library that makes better use of data, increases training efficiency, and improves model quality

**URL:** https://www.deepspeed.ai/tutorials/data-efficiency

**Contents:**
- DeepSpeed Data Efficiency: A composable library that makes better use of data, increases training efficiency, and improves model quality
    - Contents
- 1. Curriculum Learning
  - 1.1 What is Curriculum Learning
  - 1.2 When to use Curriculum Learning
  - 1.3 How to use Curriculum Learning
    - 1.3.1 GPT-3 and BERT pretraining
    - 1.3.2 GPT-2 finetuning
- 2. Random layerwise token dropping (random-LTD)
  - 2.1 What is random-LTD

What is DeepSpeed Data Efficiency: DeepSpeed Data Efficiency is a library purposely built to make better use of data, increases training efficiency, and improves model quality.

Why use DeepSpeed Data Efficiency: DeepSpeed Data Efficiency offers novel data efficiency techniques to achieve better training efficiency and/or better model quality. DeepSpeed Data Efficiency takes extensibility, flexibility, and composability into consideration, which makes it easier to customize the techniques, apply the techniques to various training tasks, and compose multiple techniques together. We highly recommend you also to read our blog to learn more about (at a high level) why we build DeepSpeed Data Efficiency and what benefits it provides to users. Additional technical details can be found in our papers, “Random-LTD: Random and Layerwise Token Dropping Brings Efficient Training for Large-scale Transformers” which describes the random-LTD technique, and “DeepSpeed Data Efficiency: Improving Deep Learning Model Quality and Training Efficiency via Efficient Data Sampling and Routing” which describes the curriculum learning technique and overall DeepSpeed Data Efficiency framework.

How to use DeepSpeed Data Efficiency: In the following tutorial, the first two sections will describe the data efficiency techniques supported by the library. The third section will describe how to compose the two techniques to achieve even better training efficiency/model quality.

Curriculum learning (proposed by Yoshua Bengio et al.) aims to improve training convergence speed by presenting relatively easier or simpler examples earlier during training. Building a curriculum learning solution usually requires two components: the difficulty metric (i.e., how to quantify the difficulty of each data sample) and the pacing function (i.e., how to decide the curriculum difficulty range when sampling next training data batch).

Curriculum learning has been successfully applied to various training tasks (see details in for example this survey paper), and last year we also released a specific curriculum learning technique (sequence length warmup) for GPT-style model pretraining (see technical details in our paper “The Stability-Efficiency Dilemma: Investigating Sequence Length Warmup for Training GPT Models” published in NeurIPS 2022 and the tutorial for this legacy curriculum learning feature). This new general curriculum learning library inside DeepSpeed Data Efficiency enables users to employ curriculum learning to their models at maximum extensibility: users can easily analyze, index, and sample their training data based on various customizable strategies. Using this library, we were able to explore different CL strategies for GPT-3 and BERT pretraining and identify the best solution that provides up to 1.5x data saving while still maintaining similar model quality.

The examples_deepspeed/data_efficiency directory in our Megatron-DeepSpeed repo includes our examples of how to apply curriculum learning to GPT-3 and BERT pretraining. There are 3 steps: data analysis, pretraining, and eval/finetuning.

Data analysis: Curriculum learning requires a data analysis before pretraining that calculate the difficulty of each data sample (based on the metric provided by user), and build an index that map difficulty value to corresponding data samples. (There are exceptions: for example the truncation-based sequence length metric can be achieved by data postprocessing without data analysis.) We provide a data analyzer to perform the offline CPU-only data analysis.

examples_deepspeed/data_efficiency/gpt/ds_analyze_*.sh and examples_deepspeed/data_efficiency/bert/ds_analyze_*.sh are example scripts for GPT-3 and BERT’s data analysis. Our data analyzer employs a simple Map-Reduce scheme. First, at the Map stage the ds_analyze_*_data_map.sh is used to split the dataset and compute the difficulty value for each data sample. User would need to provide a function to compute the metric (we implement ours in examples_deepspeed/data_efficiency/analyze_data.py), the raw training dataset, and other configurations such as number of CPU nodes and number of threads per node. Then the data analyzer will automatically splits the dataset based on number of workers, compute the difficulty values in a batched fashion, and write the results to two indexes: one index maps each data sample to its difficulty value, and another index maps each distinct difficulty value to the corresponding samples. Second, at the Reduce stage the ds_analyze_*_data_reduce.sh is used to merge the index files produced by all workers. One thing to note is that in order to enable speedup by distribution yet still being able to merge all the output, the Map stage will potentially generate a lot of output files, which is proportional to number of CPU nodes, number of threads per node, and number of possible metric values. Thus to avoid generating too much output files, we recommend to start with a smaller number of nodes/threads (in the output log we provide an estimate required time for users to judge if they want to increase number of workers), and we recommend to limit number of possible difficulty values when designing your difficulty metric (our experience shows that a few thousands of distinct values is already sufficient to enjoy the benefit of curriculum learning).

Pretraining examples_deepspeed/data_efficiency/gpt/pretrain and examples_deepspeed/data_efficiency/bert/pretrain include the example pretraining scripts with curriculum learning feature. Several changes are needed to enable curriculum learning during pretraining: (1) User need to provide a DeepSpeed json config file which includes configurations for curriculum learning (see list of configuration for details). We provide tested example configurations in examples_deepspeed/data_efficiency/gpt/pretrain/ds_pretrain_gpt_1.3B_dense_run.sh and examples_deepspeed/data_efficiency/bert/pretrain/ds_pretrain_bert_336M_run.sh. (2) When initializing the DeepSpeed engine via deepspeed.initialize, user needs to provide the train dataset and use the dataloader returned by the initialization (this dataloader includes the curriculum learning capability). We provide an example implementation of this change in megatron/training.py function setup_model_and_optimizer. (3) If the curriculum learning metric requires data postprocessing (such as truncation-based sequence length), user needs to use the DeepSpeed engine’s set_data_post_process_func API to provide the postprocessing function. We provide an example implementation of this change in megatron/training.py, pretrain_bert.py, and pretrain_gpt.py. (4) If the curriculum learning metric requires a custom scheduling strategy (the pacing function), user needs to use the DeepSpeed engine’s set_custom_curriculum_learning_schedule API to provide the function to update the max accepted difficulty during training. DeepSpeed engine will provide a global train step input to this callback function.

Eval/finetuning examples_deepspeed/data_efficiency/gpt/eval/ and examples_deepspeed/data_efficiency/bert/finetune include the example scripts for GPT-3 model’s zero-/few-shot evaluation and BERT model’s finetuning. Our paper includes the reference eval/finetune results if you follow our example scripts to perform the pretraining/eval/finetuning.

The data_efficiency/gpt_finetuning directory in our DeepSpeedExamples repo includes our examples of how to apply curriculum learning to GPT-2 finetuning. data_efficiency/gpt_finetuning/finetune/ds_finetune_gpt2_run.sh is the example finetuning script. For CL metrics that require data analysis (e.g., the vocabulary rarity metric), you need to first use data_efficiency/gpt_finetuning/finetune/ds_analyze_gpt_data_* to analyze and index the dataset, similar to the GPT-3 pre-training case described above in 1.3.1.

Random-LTD is an efficient token drop method applied to each layer with random assignment. Precisely, for each layer, as compared to the baseline, random-LTD randomly selects a subset of the tokens and feeds them into the transformer layer. Afterward, we combine the output of transformer layer with the dropped tokens to recover the full sequence length. Thus, the next layer still receives the full sequence and can repeat this process. For more technical details please read our random-LTD paper.

When you want to pretrain/fine-tune a transformer-based model, it is always a good idea to try random-LTD, as it can achieve a better performance than the standard baseline training given the same amount of computational cost. If you have limited resources, random-LTD achieves similar accuracy as the original baseline method with up to 33.3% theoretical cost saving and up to 25.6% wall-clock time saving. Particularly, if you need to train a much larger model with >=24 layers and with >=2048 sequence length, our method will be much more efficient than baseline.

The examples_deepspeed/data_efficiency directory in our Megatron-DeepSpeed repo includes our examples of how to apply random-LTD to GPT-3 and BERT pretraining.

examples_deepspeed/data_efficiency/gpt/pretrain and examples_deepspeed/data_efficiency/bert/pretrain include the example pretraining scripts with random-LTD feature. Several changes are needed to enable random-LTD during pretraining: (1) User need to provide a DeepSpeed json config file which includes configurations for random-LTD (see list of configuration for details). We provide tested example configurations in examples_deepspeed/data_efficiency/gpt/pretrain/ds_pretrain_gpt_1.3B_dense_run.sh and examples_deepspeed/data_efficiency/bert/pretrain/ds_pretrain_bert_336M_run.sh. (2) After initializing the DeepSpeed engine via deepspeed.initialize, user needs to use the convert_to_random_ltd API to convert and wrap the model layers in order to enable the random-LTD feature. We provide an example implementation of this change in megatron/training.py function setup_model_and_optimizer. (3) In order for random-LTD to understand the input argument mapping of the forward function, user need to change all the input arguments (except the hidden_states input) into keyword/named argument. For example, in megatron/model/transformer.py we changed the forward function from def forward(self, hidden_states, attention_mask, encoder_output=None, enc_dec_attn_mask=None, layer_past=None, get_key_value=False): to def forward(self, hidden_states, attention_mask=None, encoder_output=None, enc_dec_attn_mask=None, layer_past=None, get_key_value=False):. (4) When saving model checkpoints, (especially if the state dictionary has non-traditional structure) user needs to use the remove_random_ltd_state_dict API to convert the random-LTD-wrapped layers back to original model layers. We provide an example implementation of this change in megatron/model/language_model.py.

For eval/finetuning of the pretrained model, see previous section about how to use our example scripts.

The data_efficiency directory in our DeepSpeedExamples repo includes our examples of how to apply random-LTD to GPT-2 and ViT finetuning.

Just like pretraining case, similar changes are required to enable random-LTD for finetuning: (1) DeepSpeed json config file. (2) Use the convert_to_random_ltd API to convert and wrap the model layers. (3) When saving model checkpoints, use the remove_random_ltd_state_dict API to convert the random-LTD-wrapped layers back to original model layers.

One can run our GPT finetuning example by:

And the reference final result is:

One can run our ViT finetuning example by:

And the reference final result is:

The examples_deepspeed/data_efficiency directory in our Megatron-DeepSpeed repo includes our examples of how to compose curriculum learning random-LTD, and apply both of them to GPT-3 and BERT pretraining.

The changes needed are the same as described in previous two sections, since DeepSpeed Data Efficiency already handles the complexity when composing the two techniques. However, one thing to note is that since both random-LTD and some of the curriculum learning metrics will change the sequence length, it could require some extra code to calculate the effective sequence length at each step. We provide an example implementation of this change in megatron/training.py function train where we calculate the actual_seq_length.

The data_efficiency/gpt_finetuning directory in our DeepSpeedExamples repo includes our examples of how to compose curriculum learning random-LTD for GPT-2 finetuning. data_efficiency/gpt_finetuning/finetune/ds_finetune_gpt2_run.sh is the example finetuning script.

Updated: November 5, 2025

**Examples:**

Example 1 (unknown):
```unknown
DeepSpeedExamples/data_efficiency/gpt_finetuning$ pip install -r requirement.txt
DeepSpeedExamples/data_efficiency/gpt_finetuning$ bash ./bash_script/run_base_random_ltd.sh
DeepSpeedExamples/data_efficiency/gpt_finetuning$ bash ./bash_script/run_medium_random_ltd.sh
```

Example 2 (unknown):
```unknown
For run_base_random_ltd.sh:
End of training epoch 3 step 1344 consumed_token 2148032 best perplexity 22.552324221233757 time 0.17486039188173083 hr

For run_medium_random_ltd.sh:
End of training epoch 3 step 1373 consumed_token 2147024 best perplexity 17.332243199130996 time 0.4661190489927928 hr
```

Example 3 (unknown):
```unknown
DeepSpeedExamples/data_efficiency/vit_finetuning$ pip install -r requirement.txt
DeepSpeedExamples/data_efficiency/vit_finetuning$ bash ./bash_script/run_cifar.sh
DeepSpeedExamples/data_efficiency/vit_finetuning$ bash ./bash_script/run_imagenet.sh
```

Example 4 (unknown):
```unknown
For run_cifar.sh:
13 epoch at time 480.6546013355255s | reserved_length 197
iter 5474 | LR [0.0001]| val_acc 97.97000122070312 | layer_token 305784192
```

---

## Mixture of Experts for NLG models

**URL:** https://www.deepspeed.ai/tutorials/mixture-of-experts-nlg

**Contents:**
- Mixture of Experts for NLG models
    - Contents
- 1. Installation
- 2. Training NLG+MoE models
  - 2.1. Changes to the model
  - 2.2. Pre-training the Standard MoE model
  - 2.3. Pre-training the PR-MoE model
  - 2.4. Training MoS with reduced model size

In this tutorial, we introduce how to apply DeepSpeed Mixture of Experts (MoE) to NLG models, which reduces the training cost by 5 times and reduce the MoE model size by 3 times (details in our Blog). We use the GPT-3 like models in Megatron-LM framework as the example. Before reading this tutorial, we recommend to first read the tutorials about Mixture of Experts and Megatron-LM GPT pre-training.

You would need to install DeepSpeed v0.6.0 or higher to use the MoE feature. The MoE for NLG model examples are in the Megatron-DeepSpeed repo under the MoE folder.

To apply MoE to the GPT-style model, we made several changes in Megatron framework, mostly in megatron/model/ where we add the MoE layers into the model.

We provide example training scripts under examples_deepspeed/MoE which we used to perform the experiments in our Blog. There are a few new hyperparameters for standard MoE model:

--num-experts: the number of experts per MoE layer. In our experiments we set it to 128. Larger number of experts tend to provide better convergence, but it’s a diminishing return.

--moe-expert-parallel-size: degree of the MoE expert parallelism. In other words, there will be num-experts/moe-expert-parallel-size experts on each GPU. Thus --moe-expert-parallel-size should be no more than both number of GPUs, and --num-experts.

--moe-loss-coeff: scaling coefficient for adding MoE loss to model loss. In our experiments we find that 0.01 is a good setting.

--moe-train-capacity-factor, --moe-eval-capacity-factor, --moe-min-capacity: these configs determine how many tokens can a single expert handle. Larger numbers could lead to better convergence, but would also lead to slower training since the load would be more unbalanced on different experts.

--disable-moe-token-dropping: this will completely remove the limitation of how many tokens can a single expert handle. For the same reason as above, we only recommend using this during inference/eval.

PR-MoE is a new designed MoE models, standing for Pyramid-Residual-MoE, which improves the parameter efficiency up to 3x as compared to standard MoE. Please see our Blog for more details. We provide example training scripts under examples_deepspeed/MoE. There are a few different hyperparameters for PR-MoE model compared to standard MoE:

--num-experts: Instead of providing a single number, to enable Pyramid-MoE, you need to provide a list, whose length is the same as the number of MoE layers. We suggest to use more experts in the latter stage (close to output) of the model.

--mlp-type: chosen from [standard, residual]. When it is residual, Residual-MoE is enabled.

In addition to the new hyperparameters above for standard MoE and PR-MoE, for NLG+MoE models we found that it’s helpful to lower the learning rate and increase the learning rate decay duration compared to the base dense model. Details of our tuning can be found in the example training scripts.

Regarding training data, we are not able to release our internal data but any public data for Megatron-LM pre-training can be directly used to train MoE models (with the caveat that it might not provide the exact same model quality as in our experiments). For example, we evaluated The Pile dataset (pile.eleuther.ai, github.com/EleutherAI/the-pile) for both dense and MoE models. Table 1 below shows that this public data provides similar evaluation results as our internal data.

Table 1: Zero-shot evaluation results (last six columns) for different dense and MoE NLG models. All zero-shot evaluation results use the accuracy metric.

MoS, standing for Mixture-of-Students, is a staged distillation-based technique for compressing large MoE models. MoS further reduces the model size by 12.5%, leading to up 3.7x model size reduction when combined with PR-MoE over the standard MoE. The reduced model size helps reduce the latency and cost during inference. To train an MoS model, one needs to specify a few additional parameters. We will use PR-MoE as an example:

--mos: This would enable Mixture-of-Students via knowledge distillation.

--load-teacher: This specifies the path to the teacher model checkpoint. This is a mandatory argument for using MoS and the teacher model checkpoint can be obtained by either training a standard MoE or the PR-MoE.

num-layers-teacher, --hidden-size-teacher, --hidden-size-teacher, --num-experts-teacher: In addition to the teacher model checkpoint path, we also need to specify the model architecture of the teacher model such as its number of layers, hidden dimension size, and the number of experts per MoE layer. In the case of PR-MoE, we need to also provide a list of experts for the teacher model, where we remove a few expert layers from the teacher model.

In addition to the new parameters above, we observe that using the teacher PR-MoE during the entire training process may adversely impact the final student model accuracy. In our experiments, we use a staged distillation method by stopping distillation early in the training process (e.g., after 400K steps) and perform optimization only against the standard language modeling loss for the rest of the training.

We provide example training scripts under examples_deepspeed/MoE. Details of our parameter settings can be found in the example training scripts. The performance results of MoS can be seen from our blog post and our paper.

Updated: November 5, 2025

---

## DeepSpeed Transformer Kernel

**URL:** https://www.deepspeed.ai/tutorials/transformer_kernel/

**Contents:**
- DeepSpeed Transformer Kernel
    - Contents
- DeepSpeed Transformer Kernel
- Prerequisites
  - Integrate Transformer Kernel
  - Transformer kernel Parameters
  - Memory Optimization Flags
  - Enable Transformer Kernel

This tutorial shows how to enable the DeepSpeed transformer kernel and set its different configuration parameters.

Transformer layers are ubiquitous in many recent sequence-processing models, such as Natural-Language-Processing. Thus, training transformer-based networks requires to be highly efficient in term of performance, in order to allow scientists to explore different models across various application domains in a reasonable amount of time. To this end, we have developed a new kernel for transformer networks which includes several optimizations specific to these layers, which boost the training throughput on single GPU and scales well as we increase the number of GPUs. For more information on the details of transformer kernel, please visit our recent blog post on the fastest BERT training.

To use transformer kernel for training a model, you should Integrate DeepSpeed into your training script using the Getting Started guide.

Note: Currently DeepSpeed Transformer Kernels do not support Sparse Attention. To use Sparse Attention, you need to disable Transformer Kernels!

First of all, you need to integrate transformer kernel into the top-level model. Here, we show an example of instantiating the transformer kernel using the Pre-LN BERT-Large configuration settings. This configuration has 24 layers with 1024 hidden-dimension and uses the sequence length of 128 and batch size of 64. To add all these layers, we copy the same layer specification num_hidden_layer times with different IDs inside a ModuleList.

The transformer kernel is configured by a number of parameters which allow users to explore different settings. We partition these parameters into four categories:

The general parameters for configuring the transformer kernel are:

The environment parameters of the transformer kernel includes:

High-performance optimization flag:

The memory-optimization flags consist of:

To illustrate the required model configuration changes to use transformer kernel in model training, we use a BERT model and go through the different configurations in order to support the different sequence lengths and batch sizes. Please see the instruction at BERT training tutorial.

We provide several techniques into the transformer kernel which saves the memory at different parts of a layer. We expose them as the configurable settings that can be enabled when calling the kernel. By turning on each of these optimization flags, we can support larger batch sizes. Even though we trade off performance for memory using some of these techniques, the end-to-end training efficiency increases by using the larger batch size.

By setting the normalize_invertible flag, we force the kernel to drop the input activations to the normalize layers of transformer. We can do this since the kernel includes an optimization to compute the gradients of the parameters and the input to this layer by only using the output activations.

The attn_dropout_checkpoint and gelu_checkpoint flags refer to the checkpointing approach, in which we drop the inputs to some parts of the transformer layer, attention dropout and Gelu, in order to save an important part of the activation memory. Based on our performance profiling, the performance cost of rematerializing these two are negligible and finally the performance benefit that we gain from running larger batch size compensate for that.

The following table shows which memory optimization flags need to be turned on when running BERT-Large on NVIDIA V100 GPU with 32GB of memory, considering different micro-batch sizes and sequence lengths. For the two sequence lengths, 128 and 512, used in our experiments, we have seen that larger batch size improves the overall training performance for both. Please see our blog post for more information regarding the performance evaluation of these configurations.

As mentioned earlier, in order to run the transformer network using the custom DeepSpeed kernel, we only need to pass the deepspeed_transformer_kernel option when running the training script. Below, we show an example of how we pass this parameter to the deepspeed launcher, besides the rest of parameters for the BERT pre-training task.

In addition to transformer kernel flag, we can specify the memory optimization settings as discussed earlier. As an example, we use the attention_dropout_checkpoint option here for running the sequence length 512, in order to run the micro-batch size of 16 at each GPU. If larger batch size is required, we can turn on the rest of memory optimization flags too.

Updated: November 5, 2025

**Examples:**

Example 1 (unknown):
```unknown
config = DeepSpeedTransformerConfig(batch_size = 64,
                                    max_seq_length = 128,
                                    hidden_size = 1024,
                                    heads = 16,
                                    attn_dropout_ratio = 0.1,
                                    hidden_dropout_ratio = 0.1,
                                    num_hidden_layers = 24,
                                    initializer_range = 0.02,
                                    local_rank = 0,
                                    seed = 1234,
                                    fp16 = True,
                                    pre_layer_norm=True,
                                    attn_dropout_checkpoint=False,
                                    normalize_invertible=False,
                                    gelu_checkpoint=False)
self.layer = nn.ModuleList([
    copy.deepcopy(DeepSpeedTransformerLayer(cuda_config))
    for _ in range(config.num_hidden_layers)
])
```

Example 2 (unknown):
```unknown
deepspeed deepspeed_train.py \
--cf bert_large_lamb.json \
--max_seq_length 512 \
--print_steps 100 \
--deepspeed \
--deepspeed_transformer_kernel \
--deepspeed_config deepspeed_bsz32K_lamb_config_seq512.json \
--rewarmup \
--lr_schedule "EE" \
--lr_offset 0.0 \
--attention_dropout_checkpoint \
--load_training_checkpoint ${CHECKPOINT_BASE_PATH} \
--load_checkpoint_id ${CHECKPOINT_EPOCH150_NAME}
```

---

## Domino

**URL:** https://www.deepspeed.ai/tutorials/domino/

**Contents:**
- Domino
    - Contents

Domino achieves near-complete communication hiding behind computation for tensor parallel training. Please find our Domino-tutorial in DeepSpeedExample repo.

Updated: November 5, 2025

---

## Pipeline Parallelism

**URL:** https://www.deepspeed.ai/tutorials/pipeline/

**Contents:**
- Pipeline Parallelism
    - Contents
- Getting Starting with Pipeline Parallelism
  - Expressing Pipeline Models
  - AlexNet
  - Inputs and Outputs
  - Training Loops
  - Dealing with Data
- Advanced Topics
  - Load Balancing Pipeline Modules

DeepSpeed v0.3 includes new support for pipeline parallelism! Pipeline parallelism improves both the memory and compute efficiency of deep learning training by partitioning the layers of a model into stages that can be processed in parallel. DeepSpeed’s training engine provides hybrid data and pipeline parallelism and can be further combined with model parallelism such as Megatron-LM. An illustration of 3D parallelism is shown below. Our latest results demonstrate that this 3D parallelism enables training models with over a trillion parameters.

DeepSpeed uses gradient accumulation to extract pipeline parallelism (shown below). Each batch of training data is divided into micro-batches that can be processed in parallel by the pipeline stages. Once a stage completes the forward pass for a micro-batch, the activation memory is communicated to the next stage in the pipeline. Similarly, as the next stage completes its backward pass on a micro-batch, the gradient with respect to the activation is communicated backwards through the pipeline. Each backward pass accumulates gradients locally. Next, all data parallel groups perform reductions of the gradients in parallel. Lastly, the optimizer updates the model weights.

Below is an illustration of how DeepSpeed will train a batch with eight micro-batches using hybrid two-way data parallelism and two-stage pipeline parallelism. GPUs 0 and 2 are arranged in a pipeline and will alternate forward (F) and backward (B) passes. They will then all-reduce (AR) gradients with their data parallel counterparts, GPUs 1 and 3, respectively. Finally, the two pipeline stages update their model weights.

DeepSpeed strives to accelerate and simplify the process of pipeline parallel training. This section provides first steps with hybrid data and pipeline parallel training by preparing torchvision’s AlexNet model.

Pipeline parallelism requires models to be expressed as a sequence of layers. In the forward pass, each layer consumes the output of the previous layer. In fact, there is no need to specify a forward() for a pipeline parallel model! The forward pass of a pipeline parallel model implicitly takes the form:

PyTorch’s torch.nn.Sequential is a convenient container for expressing pipeline parallel models and can be parallelized by DeepSpeed with no modification:

PipelineModule uses its layers argument as the sequence of layers that comprise the model. After initialization, net is divided into two pipeline stages and its layers moved to the corresponding GPUs. If more than two GPUs are present, DeepSpeed will also use hybrid data parallelism.

Note: The total number of GPUs must be divisible by the number of pipeline stages.

Note: For large model training, see memory-efficient model construction.

Let’s look at an abbreviated implementation of torchvision’s AlexNet:

AlexNet is mostly a composition of several Sequential submodules. We can turn this into a PipelineModule by flattening its submodules into a single sequence of layers:

Note: the lambda in the middle of layers above is not a torch.nn.Module type. Any object that implements __call__() can be a layer in a PipelineModule: this allows for convenient data transformations in the pipeline.

Following torch.nn.Sequential, the inputs and outputs of each layer must be either a single torch.Tensor or a tuple of tensors. In practice, some models may need to modify their forward pass to pack and unpack arguments to forward(). Consider an abbreviated implementation of a stack of Transformer blocks:

Two modifications to TransformerBlock are required:

These modifications can be accomplished with a short subclass:

Pipeline parallelism interleaves forward and backward passes, and thus the training loop cannot be divided into separate stages of forward(), backward() and step(). Instead, DeepSpeed’s pipeline engine provides a train_batch() method that advances the pipeline engine until the next batch of training data is consumed and the model weights updated.

The above train_batch() example is equivalent to the following with traditional data parallel DeepSpeed:

Data parallel training typically has each worker perform IO independently at the start of each batch. However, in a pipeline parallel environment, only the first stage uses the input data, and only the last stage uses labels for loss calculation.

Note: The pipeline engine expects data loaders to return a tuple of two items. The first returned item is the input batch data, and the second item is the data to be used in the loss calculation. As before, inputs and labels should be either torch.Tensor type or a tuple of tensors.

For convenience, the DeepSpeed pipeline engine can construct a distributed data loader when a dataset is provided to deepspeed.initialize(). DeepSpeed handles the rest of the complexity of data loading, and so the pipeline training loop becomes:

Of course, DeepSpeed will work with any data loader that you wish to use. Data loaders should be constructed by the first and last stages in the pipeline. Each worker should load micro-batches of size engine.train_micro_batch_size_per_gpu() and will be queried a total of engine.gradient_accumulation_steps() times per train_batch().

Watch out! The pipeline engine pulls data from an iterator instead of iterating over it. It’s critical that the data stream does not empty in the middle of a training batch. Each invocation of train_batch() will pull a total of engine.gradient_accumulation_steps() micro-batches of data from the data iterator.

DeepSpeed provides a convenience class deepspeed.utils.RepeatingLoader that simply wraps an iterable such as a data loader and restarts it whenever the end is reached:

The performance of pipeline parallel training strongly relies on load balance. DeepSpeed provides several mechanisms for partitioning the model across GPUs. These strategies can be set with the partition_method keyword argument to PipelineModule. Here are partitioning methods currently provided by DeepSpeed:

Building a Sequential container and providing it to a PipelineModule is a convenient way of specifying a pipeline parallel model. However, this approach encounters scalability issues for massive models because each worker replicates the whole model in CPU memory. For example, a machine with 16 GPUs must have as much local CPU memory as 16 times the model size.

DeepSpeed provides a LayerSpec class that delays the construction of modules until the model layers have been partitioned across workers. Then each worker will allocate only the layers it’s assigned to. So, comparing to the example from the previous paragraph, using LayerSpec a machine with 16 GPUs will need to allocate a total of 1x model size on its CPU memory and not 16x.

Here is an example of the abbreviated AlexNet model, but expressed only with LayerSpecs. Note that the syntax is almost unchanged: nn.ReLU(inplace=True) simply becomes LayerSpec(nn.ReLU, inplace=True).

Some models cannot be entirely expressed as pipeline parallel models because some layers are reused in the pipeline. For example, Transformer based language models commonly use an embedding layer early in the pipeline to map vocabulary to hidden states, and then use the embedding to map hidden states back to vocabulary at the end of the pipeline. If the model was restricted to pure pipeline parallelism, this embedding reuse would prohibit pipeline parallelism.

DeepSpeed provides a TiedLayerSpec that is an extension of LayerSpec. TiedLayerSpec requires an additional argument: key. Each reuse of a layer is specified with a TiedLayerSpec, and the key field is used to identify where a layer is reused.

Tied layers are replicated on every pipeline stage that owns an instance of reuse. Training then proceeds as normal, but an additional all-reduce of the tied gradients is added after all backward passes complete. The all-reduce ensures that the weights of the tied layer remain in sync across pipeline stages.

Updated: November 5, 2025

**Examples:**

Example 1 (python):
```python
def forward(self, inputs):
    x = inputs
    for layer in self.layers:
        x = layer(x)
    return x
```

Example 2 (python):
```python
net = nn.Sequential(
    nn.Linear(in_features, hidden_dim),
    nn.ReLU(inplace=True),
    nn.Linear(hidden_dim, out_features)
)
from deepspeed.pipe import PipelineModule
net = PipelineModule(layers=net, num_stages=2)
```

Example 3 (python):
```python
class AlexNet(nn.Module):
    def __init__(self, num_classes=1000):
        super(AlexNet, self).__init__()
        self.features = nn.Sequential(
            nn.Conv2d(3, 64, kernel_size=11, stride=4, padding=2),
            ...
            nn.MaxPool2d(kernel_size=3, stride=2),
        )
        self.avgpool = nn.AdaptiveAvgPool2d((6, 6))
        self.classifier = nn.Sequential(
            nn.Dropout(),
            ...
            nn.Linear(4096, num_classes),
        )

    def forward(self, x):
        x = self.features(x)
        x = self.avgpool(x)
        x = torch.flatten(x, 1)
        x = self.classifier(x)
        return x
```

Example 4 (python):
```python
class AlexNetPipe(AlexNet):
    def to_layers(self):
        layers = [
            *self.features,
            self.avgpool,
            lambda x: torch.flatten(x, 1),
            *self.classifier
        ]
        return layers

from deepspeed.pipe import PipelineModule
net = AlexNetPipe()
net = PipelineModule(layers=net.to_layers(), num_stages=2)
```

---

## Mixture of Experts

**URL:** https://www.deepspeed.ai/tutorials/mixture-of-experts/

**Contents:**
- Mixture of Experts
    - Contents
- Getting started with a simple MoE example
  - Expert groups initialization
  - MoE layer API
  - Pyramid-Residual MoE
  - An Example Scenario
  - Combining ZeRO-Offload and DeepSpeed MoE for very large models
- Random Token Selection
- Advanced MoE usage

DeepSpeed v0.5 introduces new support for training Mixture of Experts (MoE) models. MoE models are an emerging class of sparsely activated models that have sublinear compute costs with respect to their parameters. For example, the Switch Transformer consists of over 1.6 trillion parameters, while the compute required to train it is approximately equal to that of a 10 billion-parameter dense model. This increase in model size offers tremendous accuracy gains for a constant compute budget.

For more details on results and further discussion, please see our press release: DeepSpeed powers 8x larger MoE model training with high performance.

Note: DeepSpeed MoE requires Pytorch 1.8 or above.

As a simple starting point we will show how to apply DeepSpeed MoE to a cifar10 example. Please refer to our cifar10 example going forward.

If you are adding MoE to an existing model you can use the snippet below to help guide you:

DeepSpeed MoE supports five different forms of parallelism, and it exploits both GPU and CPU memory. Its flexible design enables users to mix different types of prevalent parallelism techniques, as shown in the table below.

To support different forms of parallelism, we create various process groups inside DeepSpeed. The helper functions that DeepSpeed uses reside in deepspeed/utils/groups.py

Note: The following function has been deprecated now and model training code does not need to call this anymore.

Instead, the MoE layer API now accepts ep_size as an argument in addition to num_experts. This new API allows users to create MoE models, which can have a different number of experts and a different expert parallelism degree for each MoE layer.

The GPUs (or ranks) participating in an expert-parallel group of size ep_size will distribute the total number of experts specified by the layer.

The hidden_size is the input dimension of a particular layer and the output dimension is the same as that. This could lead to some changes to your model definition, especially for vision/convolutional models because the input/output dimensions don’t match in certain cases. E.g. in the CIFAR-10 example, we modify the third fully connected layer to add the MoE layer. To cater for this, we need to add an additional fully-connected layer, whose input dimension is equal to the output dimension of the MoE layer.

Original model config

Updated with MoE Layers

Recently, we proposed a novel Pyramid-Residual MoE (PR-MoE) model architecture. To create such an MoE model, the users need to do two additional things:

Given a total number of GPUs in our world size and a subset of GPUs in our expert-parallel world as follows.

The model code needs to use the deepspeed.moe.layer.MoE API as follows.

With the above code, the DeepSpeed runtime will be set to train an MoE model with a total of 8 experts on 4 GPUs in 4 experts/GPU mode. We call this the E + D mode as described earlier in the table.

For a runnable end-to-end example that covers both the standard MoE architecture, as well as the PR-MoE model, please look at the cifar10 example. In addition, see the advanced usage section of this tutorial that links to a more comprehensive example for NLG models.

To use MoE Layers in DeepSpeed, we rely on two parameter groups that are passed to an optimizer. A concrete example to create such groups is available from the cifar10 example.

The relevant function that creates these param groups is as follows.

The above param groups can then be fed to the ZeRO stage-2 optimizer as follows.

We are working on automating this functionality in the DeepSpeed ZeRO optimizer so the model training code can be simplified further.

To run the cifar10 example with ZeRO-Offload (stage 2) and MoE, please set the ds_config flags

An additional optimization to save memory for extremely large model training on limited number of GPUs has also been introduced. Please enable that using the following config flag to the fp16 optimizer in ds_config.

We have devised a new technique called “Random Token Selection” that greatly improves convergence. Random token selection addresses the limitation of biased selection problem in MoE model training. Our upcoming paper describes this technique and its results in detail. This feature is already part of the DeepSpeed runtime and is enabled by default so users can take advantage without any config flags or command-line arguments.

We have added an example of applying MoE to NLG models. Please read more in this newsletter and tutorial.

Updated: November 5, 2025

**Examples:**

Example 1 (unknown):
```unknown
deepspeed.utils.groups.initialize(ep_size="desired expert-parallel world size")
```

Example 2 (unknown):
```unknown
self.fc3 = nn.Linear(84, 10)
```

Example 3 (unknown):
```unknown
self.fc3 = nn.Linear(84, 84)
    self.fc3 = deepspeed.moe.layer.MoE(hidden_size=84, expert=self.fc3, num_experts=args.num_experts, ep_size=<desired expert-parallel world size> ...)
    self.fc4 = nn.Linear(84, 10)
```

Example 4 (unknown):
```unknown
self.experts = deepspeed.moe.layer.MoE(hidden_size=input_dim, expert=ExpertModule(), num_experts=[..], ep_size=ep_size, use_residual=True)
```

---

## Learning Rate Range Test

**URL:** https://www.deepspeed.ai/tutorials/lrrt/

**Contents:**
- Learning Rate Range Test
    - Contents
- Learning Rate Range Test (LRRT)
- Prerequisites
- LRRT Parameters
- Required Model Configuration Changes
  - PyTorch
- Example: Tuning for Large Batch Sizes

This tutorial shows how to use to perform Learning Rate range tests in PyTorch.

Learning rate range test ( LRRT ) is a method for discovering the largest learning rate values that can be used to train a model without divergence. Data scientists are often interested in this information because large learning rates lead to faster model convergence than a small learning rates. Moreover, large learning rates are crucial in learning rate schedules such as CLR and 1Cycle, which are used to train effectively with large batch sizes. DeepSpeed provides LRRT for model training in PyTorch frameworks.

To use DeepSpeed’s LRRT, you must satisfy the following two conditions:

LRRT works by linearly increasing the learning rate by a predefined amount, at predefined intervals. Thus, LRRT is a form of learning rate schedule because it defines how and when the learning rate should change during model training. To configure LRRT, you will need to set these parameters:

We will illustrate the required model configuration changes an example LRRT schedule that:

For PyTorch models, LRRT is implemented as a learning rate scheduler, a feature that is available in PyTorch versions 1.0.1 and newer. Thus, you can add a "scheduler" entry of type "LRRangeTest" into your model configuration as illustrated below:

We illustrate how LRRT can benefit data scientists with a snippet of our experience of tuning an internal production model to converge efficiently on larger batch sizes, as we scaled from one GPU (batch size 512) to four GPUs (batch size 2048). Our goal was to train the model with the larger batch size to match the performance of the smaller batch size using the same amount of data samples. The challenge here is the well known problem of slow convergence of large batch size training. Our approach was to use a 1Cycle schedule in DeepSpeed to tackle this problem, and we used LRRT to configure the schedule.

In the plots below, we illustrate using LRRT to discover the maximum learning rates for effective training with batch size 2048. The plot on the left shows the impact of large learning rates on validation loss over the first 9000 batches of training. The plot on the right shows the learning rate values during the same period of training. Using grid search we discover that the best fixed learning rate for the batch size 2048 is 0.0002. The blue line (lr=0.0002) represents training with this fixed learning rate. We compare the two LRRT schedules with this fixed learning rate. The orange (lr_range_test_step_rate=5) and gray (lr_range_test_step_rate=50) lines represent training with similar LRRT schedules that differ only in lr_range_test_step_rate values. Although the LRRT schedules start from the same base learning rate, the gray line’s learning rate grows about 10 times faster than the orange line. Also, the learning rates of the LRRT schedules had grown larger than that of the blue line in the presented data points. We subsequently refer to the gray line as “fast growing”, and the orange line as “slow growing” LRRT schedules respectively.

We make the following observations from this small example.

Larger learning rates clearly benefit model performance, up to some point. The fast growing LRRT schedule achieves validation loss of 0.46 after 3000 batches, which the fixed learning rate does not achieve with 9000 batches. The slow growing LRRT does not match that score until after 6000 batches, however it maintains an increasing performance advantage over the fixed learning rate.

There is an upper bound on learning rate values that are useful for training the model. The fast growing LRRT schedule hits this boundary quickly and diverges, while the slow growing LRRT will later diverge for the same reason. LRRT helped us discover these boundaries quickly, using less than 2% of the training data. These boundaries are useful information for constructing learning rate schedules.

These observations from LRRT helped us to configure the learning rate boundaries and the cycle span for a 1Cycle schedule that solves the problem, as shown below.

In our experience these are four most critical parameters of 1Cycle schedules.

We hope this brief example sparks your imagination on using LRRT for your own unique tuning challenges.

Updated: November 5, 2025

**Examples:**

Example 1 (unknown):
```unknown
"scheduler": {
    "type": "LRRangeTest",
    "params": {
        "lr_range_test_min_lr": 0.0001,
        "lr_range_test_step_size": 200,
        "lr_range_test_step_rate": 5,
        "lr_range_test_staircase": false
    }
}
```

Example 2 (unknown):
```unknown
"OneCycle": {
    "cycle_min_lr": 0.002,
    "cycle_max_lr": 0.005,
    "cycle_first_step_size": 2000,
    "cycle_second_step_size": 2000,
    ...
}
```

---

## Autotuning

**URL:** https://www.deepspeed.ai/tutorials/autotuning

**Contents:**
- Autotuning
    - Contents
- Tuning scope and strategy
- Ease of use
- Example
  - Environment
  - Enabling Autotuning
  - Throughput Comparison
  - DeepSpeed Autotuning with AzureML

Make sure you’ve read the DeepSpeed tutorials on Getting Started and Zero Redundancy Optimizer before stepping through this tutorial.

One pain point in model training is to figure out good performance-relevant configurations such as micro-batch size to fully utilize the hardware and achieve a high throughput number. This configuration exploring process is commonly done manually but is important since model training is repeated many times and benefits from using a good configuration. Not only is the hand-tuning process time-consuming, but the outcome is hardware-dependent. This means that a good configuration on one hardware might not be the best on another different hardware. The user thus has to hand tune the configuration again. With DeepSpeed, there are more configuration parameters that could potentially affect the training speed, thus making it more tedious to manually tune the configuration.

The DeepSpeed Autotuner mitigates this pain point and automatically discovers the optimal DeepSpeed configuration that delivers good training speed. It not only reduces the time and resources users spend on tuning, but also can discover configurations better than hand-tuned methods. In this tutorial, we showcase the usage and benefits of the autotuning feature in DeepSpeed. For more details, please see the README.md.

The DeepSpeed Autotuner uses model information, system information, and heuristics to efficiently tune system knobs that affect compute and memory efficiencies, such as ZeRO optimization stages, micro-batch sizes, and many other ZeRO optimization configurations. Currently, the DeepSpeed Autotuner tunes ZeRO stages, micro-batch size per GPU, and ZeRO configurations (offloading is not yet supported) on top of other configurations such as optimizer, scheduler, fp16 defined by the user in the DeepSpeed configuration file. Note that ZeRO stages, micro-batch sizes, and other ZeRO configurations to tune are also configurable and can be overwritten by the user through the DeepSpeed configuration file. See Configuring Tuning Scope for details.

DeepSpeed Autotuning is easy to use, requiring no code change from DeepSpeed users. Compared to the original training script (deepspeed your_program.py <normal cl args> --deepspeed ds_config.json), invoking the autotuning feature in DeepSpeed only requires setting an autotuning flag after the DeepSpeed launcher (see Usage for details), and adding " autotuning": {"enabled": true} to the DeepSpeed configuration file. Users can further tailor the autotuning process by changing the autotuning configuration in the DeepSpeed configuration JSON file (See Autotuning Configuration for details).

We demonstrate the usage and benefit of autotuning using the training of a 0.77 billion parameter GPT2-large model from Hugging Face on 16 Nvidia V100 GPUs. For more examples, refer to autotuning in the DeepSpeedExamples repo. Note that autotuning works with any DeepSpeed-accelerated model training, not limited to Hugging Face models.

The training use fp16 and runs on 1 node with 16 Nvidia V100 GPUs. The autotuning uses the same hardware resource as the training. max_train_batch_size is not defined. The HF packages below are used.

HF examples require installing the transformers package from source:

The datasets package can be installed by pip install datasets

Below are the versions used in this test.

To enable the autotuning, add --autotuning run is added to the training script and add "autotuning": {"enabled": true} to the DeepSpeed configuration file. If the user training script uses DeepSpeed configuration parameters as training script arguments, the name mappings between the parameters in DeepSpeed configuration and the training script arguments must be provided in the arg_mappings dictionary in the autotuning section of the DeepSpeed configuration file.

DeepSpeed configuration file:

The table below shows the throughput (samples per second) comparison. The corresponding micro-batch size per GPU (mbs or tmbspg) and ZeRO stage used to achieve the throughput value is also shown in the parentheses. Assume the strategy users would use in the hand-tuning process is to start from mbs = 1 and increase mbs by 2 each time until running out of GPU memory.

Notation: Hugging Face (HF), DeepSpeed (DS), ZeRO stage (z), gradient accumulation steps (gas), micro-batch size per GPU (mbs or tmbspg).

The detailed HF + DS autotuning result summary is shown below.

Note that the performance metric used in autotuning is calculated using the timings captured within DeepSpeed forward, backward and step functions. The sum of these timings is less than the actual training step latency, thus the throughput metric values used by autotuning would be higher than the end-to-end throughput in training.

Tuning completed in 0:27:33.988447. Total number of experiments: 13.

As we can see the DeepSpeed Autotuner can select a better than hand-tuned configuration with a reasonable number of experiments. Examples in Autotuning Hugging Face Examples would demonstrate the effectiveness of autotuning across different models.

To try DeepSpeed autotuning with AzureML, please see the example here.

Updated: November 5, 2025

**Examples:**

Example 1 (unknown):
```unknown
git clone https://github.com/huggingface/transformers.git
    cd transformers
    pip install .
```

Example 2 (unknown):
```unknown
deepspeed --autotuning run --num_nodes=$NNODES --num_gpus=$NGPUS $HF_PATH/transformers/examples/pytorch/language-modeling/run_clm.py --deepspeed $DS_CONFIG\
    --model_name_or_path $MODEL_NAME \
    --dataset_name wikitext \
    --dataset_config_name wikitext-2-raw-v1 \
    --do_train \
    --do_eval \
    --fp16 \
    --per_device_train_batch_size $PER_DEVICE_TRAIN_BATCH_SIZE \
    --gradient_accumulation_steps $GRADIENT_ACCUMULATION_STEPS \
    --learning_rate 2e-5 \
    --num_train_epochs $NEPOCHS \
    --output_dir ${OUTPUT_DIR} \
    --overwrite_output_dir
```

Example 3 (unknown):
```unknown
{
  "train_micro_batch_size_per_gpu": "auto",
  "fp16": {
    "enabled": true
  },
  "autotuning": {
    "enabled": true,
    "arg_mappings": {
      "train_micro_batch_size_per_gpu": "--per_device_train_batch_size",
      "gradient_accumulation_steps ": "--gradient_accumulation_steps"
    }
  }
}
```

---

## Flops Profiler

**URL:** https://www.deepspeed.ai/tutorials/flops-profiler

**Contents:**
- Flops Profiler
    - Contents
- Overview
- Flops Measurement
- Multi-GPU, Multi-node, Data Parallelism, and Model Parallelism
- Usage
  - Usage With the DeepSpeed Runtime
    - Example: Megatron-LM
  - Usage Outside the DeepSpeed Runtime
    - In Model Inference

In this tutorial, we introduce the DeepSpeed Flops Profiler and provide examples of its usage.

Effective use of hardware resources is critical to good performance, but performance inefficiency in existing implementations for large-scale model training and inference are often hard to spot and attribute to specific module components. DeepSpeed Flops Profiler helps users easily measure both the model training/inference speed (latency, throughput) and efficiency (floating-point operations per second, i.e., FLOPS) of a model and its submodules, with an eye towards eliminating inefficiencies in existing implementations.

Below is an example output for BERT-Large(NVIDIA) on an A100 GPU with batch size 80:

In the summary profile, the DeepSpeed Flops Profiler outputs the number of parameters, floating-point operations (flops), FLOPS, latency, and throughput in samples/second of the model. This profile shows how much performance gap (compared to the peak hardware performance) the current model execution has and helps users tune the training or inference setup (e.g., hyperparameters, data parallelism, model parallelism, system configurations, etc.) for better performance.

The DeepSpeed Flops Profiler also measures significant modules at different model depths (aggregated profile) and module-specific profile in the model architecture (detailed profile). Using these profiles, DeepSpeed users can understand how each layer or submodule contributes to the overall model complexity/performance. Then users can adjust or refactor the model design to improve performance. For example, using the profiler, DeepSpeed users can quantitatively tell if stacking smaller layers is lighter or more performant than having bigger ones. The aggregated and detailed profiles also allow users to quickly identify bottleneck modules. In the BERT-Large example above, using the DeepSpeed Flops Profiler, we find that BertLayer is the most significant layer and contains quite a few dropout, softmax, and layer norm along with linear modules. These modules are not heavy in flops and would trigger many GPU kernel invocations and create excessive read/write requests to memory. The pattern shown in the detailed profile suggests this is a perfect match for kernel fusion, and we developed fused transformer-kernels to reduce data movement (see DeepSpeedBert). After applying our optimizations, we see a 25% improvement in FLOPS per GPU and overall training samples/second in the DeepSpeed Flops Profiler output.

The DeepSpeed Flops Profiler can be used with the DeepSpeed runtime without any user code change or be used independently from DeepSpeed as a standalone package. When using DeepSpeed for model training, the profiler can be enabled in the DeepSpeed configuration file. As a standalone package, the profiler API can be used in both training and inference code. The DeepSpeed profiler is still under active development and includes just initial features. Stay connected for more exciting features to be added soon.

Similar to existing flops calculation tools or methods, the DeepSpeed Flops Profiler measures the flops of the forward pass of a module and the flops of the backward pass is estimated as 2 times of that of the forward pass. Different from the PyTorch profiler which calculates the flops of PyTorch operators, the DeepSpeed Flops Profiler measures the flops within modules in a model and provides more insights to the users about the model execution. The flops estimation is partly inspired by ptflops with the major difference being that the DeepSpeed Flops Profiler not only supports flops computation directly at module level, but can also capture torch.nn.functional invoked in a module to estimate the flops. Thus the DeepSpeed Flops Profiler allows for customized modules in the model, e.g., ParallelTransformerLayerworks, ParallelSelfAttention, RowParallelLinear, etc. in Megatron-LM. This is in contrast to ptflops which requires users to write customized flops calculation functions for each customized module.

The DeepSpeed Flops Profiler outputs the per GPU profile as well as the world size, data parallel size, and model parallel size.

For models running on multi-GPU or multi-node, only change of the model parallelism (e.g., --model-parallel-size in Megatron-LM) affects the number of flops and parameters profiled, i.e., model_parallel_size * flops = total_flops and model_parallel_size * parameters = total_parameters. The data parallel size or world size (related to the number of GPUs or nodes) does not affect the per GPU profile.

The DeepSpeed Flops Profiler can be used with the DeepSpeed runtime or as a standalone package. When using DeepSpeed for model training, the profiler can be configured in the deepspeed configuration file without user code changes. To use the flops profiler outside the DeepSpeed runtime, install DeepSpeed and import the flops_profiler package to use the APIs directly. Examples of each usage are given below.

When using DeepSpeed for model training, the profiler can be configured in the deepspeed configuration file. No explicit API calls are needed to use the profiler. The profiler can be enabled by adding the following field to deepspeed’s configuration json file. Refer to flops profiler for details.

For information on running Megatron-LM with DeepSpeed, please refer to our tutorial Megatron-LM.

An example output of 12-layer Megatron-LM model (hidden_size = 8192, num_attention_heads = 32, batch_size = 1024, seq_length = 1024) is shown below.

The profiler can be used as a standalone package outside of the DeepSpeed runtime. One can simply install DeepSpeed and import the flops_profiler package to use the APIs directly. Refer to installation of DeepSpeed for installing DeepSpeed.

To profile a trained model in inference, use the get_model_profile function. Examples are given below.

The following example shows how to profile AlexNet using the DeepSpeed flops profiler.

To profile model forward in a training workflow, use the FlopsProfilerclass. The FlopsProfilerclass provides the following methods:

Below is an example of this usage in a typical training workflow.

Updated: November 5, 2025

**Examples:**

Example 1 (unknown):
```unknown
-------------------------- DeepSpeed Flops Profiler --------------------------
Profile Summary at step 10:
Notations:
data parallel size (dp_size), model parallel size(mp_size),
number of parameters (params), number of multiply-accumulate operations(MACs),
number of floating-point operations (flops), floating-point operations per second (FLOPS),
fwd latency (forward propagation latency), bwd latency (backward propagation latency),
step (weights update latency), iter latency (sum of fwd, bwd and step latency)

world size:                                                   1
data parallel size:                                           1
model parallel size:                                          1
batch size per GPU:                                           80
params per gpu:                                               336.23 M
params of model = params per GPU * mp_size:                   336.23 M
fwd MACs per GPU:                                             3139.93 G
fwd flops per GPU:                                            6279.86 G
fwd flops of model = fwd flops per GPU * mp_size:             6279.86 G
fwd latency:                                                  76.67 ms
bwd latency:                                                  108.02 ms
fwd FLOPS per GPU = fwd flops per GPU / fwd latency:          81.9 TFLOPS
bwd FLOPS per GPU = 2 * fwd flops per GPU / bwd latency:      116.27 TFLOPS
fwd+bwd FLOPS per GPU = 3 * fwd flops per GPU / (fwd+bwd latency):   102.0 TFLOPS
step latency:                                                 34.09 us
iter latency:                                                 184.73 ms
samples/second:                                               433.07

----------------------------- Aggregated Profile per GPU -----------------------------
Top modules in terms of params, MACs or fwd latency at different model depths:
depth 0:
    params      - {'BertForPreTrainingPreLN': '336.23 M'}
    MACs        - {'BertForPreTrainingPreLN': '3139.93 GMACs'}
    fwd latency - {'BertForPreTrainingPreLN': '76.39 ms'}
depth 1:
    params      - {'BertModel': '335.15 M', 'BertPreTrainingHeads': '32.34 M'}
    MACs        - {'BertModel': '3092.96 GMACs', 'BertPreTrainingHeads': '46.97 GMACs'}
    fwd latency - {'BertModel': '34.29 ms', 'BertPreTrainingHeads': '3.23 ms'}
depth 2:
    params      - {'BertEncoder': '302.31 M', 'BertLMPredictionHead': '32.34 M'}
    MACs        - {'BertEncoder': '3092.88 GMACs', 'BertLMPredictionHead': '46.97 GMACs'}
    fwd latency - {'BertEncoder': '33.45 ms', 'BertLMPredictionHead': '2.61 ms'}
depth 3:
    params      - {'ModuleList': '302.31 M', 'Embedding': '31.79 M', 'Linear': '31.26 M'}
    MACs        - {'ModuleList': '3092.88 GMACs', 'Linear': '36.23 GMACs'}
    fwd latency - {'ModuleList': '33.11 ms', 'BertPredictionHeadTransform': '1.83 ms''}
depth 4:
    params      - {'BertLayer': '302.31 M', 'LinearActivation': '1.05 M''}
    MACs        - {'BertLayer': '3092.88 GMACs', 'LinearActivation': '10.74 GMACs'}
    fwd latency - {'BertLayer': '33.11 ms', 'LinearActivation': '1.43 ms'}
depth 5:
    params      - {'BertAttention': '100.76 M', 'BertIntermediate': '100.76 M'}
    MACs        - {'BertAttention': '1031.3 GMACs', 'BertIntermediate': '1030.79 GMACs'}
    fwd latency - {'BertAttention': '19.83 ms', 'BertOutput': '4.38 ms'}
depth 6:
    params      - {'LinearActivation': '100.76 M', 'Linear': '100.69 M'}
    MACs        - {'LinearActivation': '1030.79 GMACs', 'Linear': '1030.79 GMACs'}
    fwd latency - {'BertSelfAttention': '16.29 ms', 'LinearActivation': '3.48 ms'}

------------------------------ Detailed Profile per GPU ------------------------------
Each module profile is listed after its name in the following order:
params, percentage of total params, MACs, percentage of total MACs, fwd latency, percentage of total fwd latency, fwd FLOPS

BertForPreTrainingPreLN(
  336.23 M, 100.00% Params, 3139.93 GMACs, 100.00% MACs, 76.39 ms, 100.00% latency, 82.21 TFLOPS,
  (bert): BertModel(
    335.15 M, 99.68% Params, 3092.96 GMACs, 98.50% MACs, 34.29 ms, 44.89% latency, 180.4 TFLOPS,
    (embeddings): BertEmbeddings(...)
    (encoder): BertEncoder(
      302.31 M, 89.91% Params, 3092.88 GMACs, 98.50% MACs, 33.45 ms, 43.79% latency, 184.93 TFLOPS,
      (FinalLayerNorm): FusedLayerNorm(...)
      (layer): ModuleList(
        302.31 M, 89.91% Params, 3092.88 GMACs, 98.50% MACs, 33.11 ms, 43.35% latency, 186.8 TFLOPS,
        (0): BertLayer(
          12.6 M, 3.75% Params, 128.87 GMACs, 4.10% MACs, 1.29 ms, 1.69% latency, 199.49 TFLOPS,
          (attention): BertAttention(
            4.2 M, 1.25% Params, 42.97 GMACs, 1.37% MACs, 833.75 us, 1.09% latency, 103.08 TFLOPS,
            (self): BertSelfAttention(
              3.15 M, 0.94% Params, 32.23 GMACs, 1.03% MACs, 699.04 us, 0.92% latency, 92.22 TFLOPS,
              (query): Linear(1.05 M, 0.31% Params, 10.74 GMACs, 0.34% MACs, 182.39 us, 0.24% latency, 117.74 TFLOPS,...)
              (key): Linear(1.05 M, 0.31% Params, 10.74 GMACs, 0.34% MACs, 57.22 us, 0.07% latency, 375.3 TFLOPS,...)
              (value): Linear(1.05 M, 0.31% Params, 10.74 GMACs, 0.34% MACs, 53.17 us, 0.07% latency, 403.91 TFLOPS,...)
              (dropout): Dropout(...)
              (softmax): Softmax(...)
            )
            (output): BertSelfOutput(
              1.05 M, 0.31% Params, 10.74 GMACs, 0.34% MACs, 114.68 us, 0.15% latency, 187.26 TFLOPS,
              (dense): Linear(1.05 M, 0.31% Params, 10.74 GMACs, 0.34% MACs, 64.13 us, 0.08% latency, 334.84 TFLOPS, ...)
              (dropout): Dropout(...)
            )
          )
          (PreAttentionLayerNorm): FusedLayerNorm(...)
          (PostAttentionLayerNorm): FusedLayerNorm(...)
          (intermediate): BertIntermediate(
            4.2 M, 1.25% Params, 42.95 GMACs, 1.37% MACs, 186.68 us, 0.24% latency, 460.14 TFLOPS,
            (dense_act): LinearActivation(4.2 M, 1.25% Params, 42.95 GMACs, 1.37% MACs, 175.0 us, 0.23% latency, 490.86 TFLOPS,...)
          )
          (output): BertOutput(
            4.2 M, 1.25% Params, 42.95 GMACs, 1.37% MACs, 116.83 us, 0.15% latency, 735.28 TFLOPS,
            (dense): Linear(4.2 M, 1.25% Params, 42.95 GMACs, 1.37% MACs, 65.57 us, 0.09% latency, 1310.14 TFLOPS,...)
            (dropout): Dropout(...)
          )
        )
        ...
        (23): BertLayer(...)
      )
    )
    (pooler): BertPooler(...)
  )
  (cls): BertPreTrainingHeads(...)
)
------------------------------------------------------------------------------
```

Example 2 (unknown):
```unknown
{
  "flops_profiler": {
    "enabled": true,
    "profile_step": 1,
    "module_depth": -1,
    "top_modules": 1,
    "detailed": true,
    "output_file": null
    }
}
```

Example 3 (unknown):
```unknown
-------------------------- DeepSpeed Flops Profiler --------------------------
Profile Summary at step 10:
Notations:
data parallel size (dp_size), model parallel size(mp_size),
number of parameters (params), number of multiply-accumulate operations(MACs),
number of floating-point operations (flops), floating-point operations per second (FLOPS),
fwd latency (forward propagation latency), bwd latency (backward propagation latency),
step (weights update latency), iter latency (sum of fwd, bwd and step latency)

world size:                                                   1
data parallel size:                                           1
model parallel size:                                          1
batch size per GPU:                                           1024
params per gpu:                                               1.29 M
params of model = params per GPU * mp_size:                   1.29 M
fwd MACs per GPU:                                             41271.95 G
fwd flops per GPU:                                            82543.9 G
fwd flops of model = fwd flops per GPU * mp_size:             82543.9 G
fwd latency:                                                  1.89 s
bwd latency:                                                  5.38 s
fwd FLOPS per GPU = fwd flops per GPU / fwd latency:          43.68 TFLOPS
bwd FLOPS per GPU = 2 * fwd flops per GPU / bwd latency:      30.7 TFLOPS
fwd+bwd FLOPS per GPU = 3 * fwd flops per GPU / (fwd+bwd latency):   34.07 TFLOPS
step latency:                                                 34.12 s
iter latency:                                                 41.39 s
samples/second:                                               24.74

----------------------------- Aggregated Profile per GPU -----------------------------
Top 1 modules in terms of params, MACs or fwd latency at different model depths:
depth 0:
    params      - {'GPT2Model': '1.29 M'}
    MACs        - {'GPT2Model': '41271.95 GMACs'}
    fwd latency - {'GPT2Model': '1.84 s'}
depth 1:
    params      - {'TransformerLanguageModel': '1.29 M'}
    MACs        - {'TransformerLanguageModel': '39584.03 GMACs'}
    fwd latency - {'TransformerLanguageModel': '1.83 s'}
depth 2:
    params      - {'ParallelTransformer': '1.29 M'}
    MACs        - {'ParallelTransformer': '39584.03 GMACs'}
    fwd latency - {'ParallelTransformer': '1.81 s'}
depth 3:
    params      - {'ModuleList': '1.28 M'}
    MACs        - {'ModuleList': '39584.03 GMACs'}
    fwd latency - {'ModuleList': '1.3 s'}
depth 4:
    params      - {'ParallelTransformerLayerPart2': '688.15 k'}
    MACs        - {'ParallelTransformerLayerPart2': '26388.28 GMACs'}
    fwd latency - {'ParallelTransformerLayerPart2': '865.73 ms'}
depth 5:
    params      - {'ParallelMLP': '491.54 k'}
    MACs        - {'ParallelMLP': '26388.28 GMACs'}
    fwd latency - {'ParallelMLP': '849.4 ms'}

------------------------------ Detailed Profile per GPU ------------------------------
Each module profile is listed after its name in the following order:
params, percentage of total params, MACs, percentage of total MACs, fwd latency, percentage of total fwd latency, fwd FLOPS

Note: 1. A module can have torch.nn.module or torch.nn.functional to compute logits (e.g. CrossEntropyLoss). They are not counted as submodules, thus not to be printed out. However they make up the difference between a parent's MACs(or latency) and the sum of its submodules'.
1. Number of floating-point operations is a theoretical estimation, thus FLOPS computed using that could be larger than the maximum system throughput.
2. The fwd latency listed in the top module's profile is directly captured at the module forward function in PyTorch, thus it's less than the fwd latency shown above which is captured in DeepSpeed.

GPT2Model(
  1.29 M, 100.00% Params, 41271.95 GMACs, 100.00% MACs, 1.84 s, 100.00% latency, 44.78 TFLOPS,
  (language_model): TransformerLanguageModel(
    1.29 M, 100.00% Params, 39584.03 GMACs, 95.91% MACs, 1.83 s, 99.11% latency, 43.34 TFLOPS,
    (embedding): Embedding(
      2, 0.00% Params, 0 MACs, 0.00% MACs, 18.1 ms, 0.98% latency, 0.0 FLOPS,
      (word_embeddings): VocabParallelEmbedding(1, 0.00% Params, 0 MACs, 0.00% MACs, 164.75 us, 0.01% latency, 0.0 FLOPS, )
      (position_embeddings): Embedding(1, 0.00% Params, 0 MACs, 0.00% MACs, 489.23 us, 0.03% latency, 0.0 FLOPS, 1024, 8192)
      (embedding_dropout): Dropout(0, 0.00% Params, 0 MACs, 0.00% MACs, 93.94 us, 0.01% latency, 0.0 FLOPS, p=0.1, inplace=False)
    )
    (transformer): ParallelTransformer(
      1.29 M, 100.00% Params, 39584.03 GMACs, 95.91% MACs, 1.81 s, 98.11% latency, 43.78 TFLOPS,
      (layers): ModuleList(
        1.28 M, 98.73% Params, 39584.03 GMACs, 95.91% MACs, 1.3 s, 70.66% latency, 60.79 TFLOPS,
        (0): ParallelTransformerLayerPart1(
          49.15 k, 3.80% Params, 1099.65 GMACs, 2.66% MACs, 23.5 ms, 1.27% latency, 93.6 TFLOPS,
          (input_layernorm): FusedLayerNorm(16.38 k, 1.27% Params, 0 MACs, 0.00% MACs, 128.75 us, 0.01% latency, 0.0 FLOPS, torch.Size([8192]), eps=1e-05, elementwise_affine=True)
          (attention): ParallelSelfAttention(
            32.77 k, 2.53% Params, 1099.65 GMACs, 2.66% MACs, 22.8 ms, 1.24% latency, 96.46 TFLOPS,
            (query_key_value): ColumnParallelLinear(24.58 k, 1.90% Params, 824.63 GMACs, 2.00% MACs, 8.93 ms, 0.48% latency, 184.7 TFLOPS, )
            (scale_mask_softmax): FusedScaleMaskSoftmax(0, 0.00% Params, 134.22 MMACs, 0.00% MACs, 151.16 us, 0.01% latency, 1.78 TFLOPS, )
            (attention_dropout): Dropout(0, 0.00% Params, 0 MACs, 0.00% MACs, 79.63 us, 0.00% latency, 0.0 FLOPS, p=0.1, inplace=False)
            (dense): RowParallelLinear(8.19 k, 0.63% Params, 274.88 GMACs, 0.67% MACs, 2.67 ms, 0.14% latency, 205.81 TFLOPS, )
          )
        )
        (1): ParallelTransformerLayerPart2(
          57.35 k, 4.43% Params, 2199.02 GMACs, 5.33% MACs, 77.53 ms, 4.21% latency, 56.73 TFLOPS,
          (post_attention_layernorm): FusedLayerNorm(16.38 k, 1.27% Params, 0 MACs, 0.00% MACs, 116.11 us, 0.01% latency, 0.0 FLOPS, torch.Size([8192]), eps=1e-05, elementwise_affine=True)
          (mlp): ParallelMLP(
            40.96 k, 3.16% Params, 2199.02 GMACs, 5.33% MACs, 76.19 ms, 4.13% latency, 57.72 TFLOPS,
            (dense_h_to_4h): ColumnParallelLinear(32.77 k, 2.53% Params, 1099.51 GMACs, 2.66% MACs, 10.79 ms, 0.59% latency, 203.81 TFLOPS, )
            (dense_4h_to_h): RowParallelLinear(8.19 k, 0.63% Params, 1099.51 GMACs, 2.66% MACs, 14.38 ms, 0.78% latency, 152.95 TFLOPS, )
          )
        )
        ...
        (23): ParallelTransformerLayerPart2(...)
      )
      (final_layernorm): FusedLayerNorm(16.38 k, 1.27% Params, 0 MACs, 0.00% MACs, 110.86 us, 0.01% latency, 0.0 FLOPS, torch.Size([8192]), eps=1e-05, elementwise_affine=True)
    )
  )
)
------------------------------------------------------------------------------
```

Example 4 (python):
```python
import torchvision.models as models
import torch
from deepspeed.profiling.flops_profiler import get_model_profile
from deepspeed.accelerator import get_accelerator

with get_accelerator().device(0):
    model = models.alexnet()
    batch_size = 256
    flops, macs, params = get_model_profile(model=model, # model
                                    input_shape=(batch_size, 3, 224, 224), # input shape to the model. If specified, the model takes a tensor with this shape as the only positional argument.
                                    args=None, # list of positional arguments to the model.
                                    kwargs=None, # dictionary of keyword arguments to the model.
                                    print_profile=True, # prints the model graph with the measured profile attached to each module
                                    detailed=True, # print the detailed profile
                                    module_depth=-1, # depth into the nested modules, with -1 being the inner most modules
                                    top_modules=1, # the number of top modules to print aggregated profile
                                    warm_up=10, # the number of warm-ups before measuring the time of each module
                                    as_string=True, # print raw numbers (e.g. 1000) or as human-readable strings (e.g. 1k)
                                    output_file=None, # path to the output file. If None, the profiler prints to stdout.
                                    ignore_modules=None) # the list of modules to ignore in the profiling
```

---

## 1-bit Adam: Up to 5x less communication volume and up to 3.4x faster training

**URL:** https://www.deepspeed.ai/tutorials/onebit-adam/

**Contents:**
- 1-bit Adam: Up to 5x less communication volume and up to 3.4x faster training
- 1. Overview
  - 1.1 Pre-requisites for installing DeepSpeed
  - 1.2 Pre-requisites for 1-bit Adam
    - 1.2.1 (New in v2) NCCL-based implementation
    - 1.2.2 MPI-based implementation
    - 1.2.3 Compressed implementation
  - 1.3 1-bit Algorithm
  - 1.4 Configuration of 1-bit Adam
    - 1.4.1 (New in v2) Momentum masks for parameters with constant zero gradients

Note: On 03/07/2022 we released 0/1 Adam, which is a new communication-efficient Adam optimizer partially following the 1-bit Adam’s design. Compared to the 1-bit Adam described below, 0/1 Adam provides better communication efficiency and the same final model quality on different tasks including BERT, GPT-2, and ImageNet. Thus we would recommend to first try 0/1 Adam (tutorial), and then try 1-bit Adam if 0/1 Adam couldn’t provide baseline Adam’s convergence in your task.

Note: This tutorial is updated on 03/04/2021 to reflect the 1-bit Adam v2. Changes include: 1) NCCL-based implementation which provides better performance and usability compared to the MPI-based implementation. 2) Add support to momentum masks for those parameters with constant zero gradients during training. 3) Bug fixes. See details below.

Watch out! 1) The NCCL-based implementation requires PyTorch >= 1.8 (and NCCL >= 2.8.3 when you have 64 or more GPUs). See details below. 2) Although 1-bit Adam is compatible with both FP16 and FP32, currently we only verified the convergence under mixed precision/FP16 training. 3) Currently the MPI-based implementation is not compatible with pipeline parallelism. 4) Frequent checkpoint loading could hurt 1-bit Adam’s convergence. See details below.

In this tutorial, we are going to introduce the 1-bit Adam optimizer in DeepSpeed. 1-bit Adam can improve model training speed on communication-constrained clusters, especially for communication-intensive large models by reducing the overall communication volume by up to 5x. Detailed description of the 1-bit Adam algorithm, its implementation in DeepSpeed, and performance evaluation is available from our blog post. We also have a paper which provides the most complete details including algorithm, system implementation, theoretical analysis, and more evaluations.

To illustrate the benefits and usage of 1-bit Adam optimizer in DeepSpeed, we use the following two training tasks as examples:

For more details on these tasks, please refer to the tutorial posts on BingBertSQuAD Fine-tuning and BERT Pre-training.

If you don’t already have a copy of the DeepSpeed repository, please clone it now and checkout the DeepSpeedExamples submodule that contains the BingBertSQuAD and BERT Pre-training examples.

In 1-bit Adam v2, we introduce a new system implementation for compressed communication using the NCCL backend of PyTorch distributed. This significantly improves the usability due to NCCL’s integration with PyTorch distributed. The performance of our new NCCL-based implementation is also better than our earlier MPI-based implementation for Ethernet-based systems and on-par for InfiniBand-based systems. Thus we highly recommend users to choose this implementation.

Watch out! This NCCL-based implementation requires PyTorch >= 1.8. It also requires NCCL >= 2.8.3 when you have 64 or more GPUs to avoid certain NCCL runtime bugs. Currently (2021/03/16) NCCL 2.8.3 is not officially supported by PyTorch. The solution we used is by hacking in NCCL 2.8.3 via LD_PRELOAD: 1) Install NCCL 2.8.3. This works for us on a CUDA 11 system: apt-get install -y libnccl2=2.8.3-1+cuda11.0 libnccl-dev=2.8.3-1+cuda11.0. 2) Set LD_PRELOAD to the library path. This works for us: LD_PRELOAD=/usr/lib/x86_64-linux-gnu/libnccl.so.2.8.3. To confirm LD_PRELOAD is working you can see the version it uses in the NCCL logs if you have NCCL_DEBUG=INFO, it should say: NCCL version 2.8.3+cuda11.0.

For this implementation, we rely on Message Passing Interface (MPI) for advanced communication primitives.

We package the necessary dependencies in the DeepSpeed docker images. However, if you are using a different build system, please install MPI and mpi4py on your system. To install the prerequisites run:

We have tested CUDA-Aware MPI communication using the MVAPICH2-GDR library. However, any CUDA-Aware communication library including OpenMPI should work fine with these examples.

An example launch command for 1-bit Adam using the deepspeed launcher is as follows:

Please note that for MPI-based implementation of 1-bit Adam, the --launcher=[mvapich|openmpi] flag is required when using the deepspeed launcher.

Alternatively, the standard mpirun launcher can also be used as follows:

This backend provides an approach to abstract the generic part of one-bit optimizers and implements accelerator dependent part with DeepSpeed custom op builder. To use this CompressedBackend, you should make sure that your current accelerator supports PackbitsBuilder, so that it could be loaded to do high performance packing and unpacking between float and Byte datatype, which is utilized in one-bit algorithm. An example can be found in Deepspeed/op_builder/xpu/packbits.py.

This approach does not require NCCL or MPI based communication library. It will automatically use your default communication library selected by your accelerator in deepspeed/comm.

The detailed description of the 1-bit Algorithm can be seen from our blog post and our paper.

The 1-bit Adam feature can be used by setting the optimizer configuration options as follows. An example json config file is shown below.

Please note three new parameters freeze_step, cuda_aware, and comm_backend_name that have been added to support the 1-bit Adam feature.

freeze_step is the number of warm up steps before 1-bit compression gets applied to the communication. In order to determine the number of warm up steps, one strategy is to set 15-25% of the total training steps for a given model (This is related to Adam’s variance/second moment term. See detailed analysis in our paper). If it provides the desired outcome, one can try to extract more performance by reducing the steps systematically. In future, we plan to introduce a threshold that can automatically search and decide for the number of warm up steps for different models. The examples below have been tuned for the number of warm up steps. The freeze_step parameter has already been set to the best number we found in the corresponding run scripts.

cuda_aware is used for MPI-based implementation to indicate that the underlying MPI library supports CUDA-Aware communication. This feature is only supported on systems with InfiniBand interconnect and a CUDA-Aware MPI library like MVAPICH2-GDR or OpenMPI built with CUDA-Aware support. Setting cuda_aware to False will allow training on Ethernet based systems. However, the communication will happen using sender as well as receiver side memory copies between CPU and GPU buffers before and after communication.

(New in v2) comm_backend_name is used to indicate which backend implementation to use. You can choose between NCCL, MPI-based and compressed implementations by setting comm_backend_name to “nccl”, “mpi” or “compressed”. When using NCCL-based implementation, there is no need to set cuda_aware.

Because 1-bit compression cannot represent exact zero, the compression error would keep accumulating in the momentum if a parameter have constant zero gradients during training. For example, for BERT pre-training seq length 128, bert.embeddings.position_embeddings.weight has constant zeros in its gradient and momentum for row 129 to 512, because it only learns up to seq length 128 while the model supports up to seq length 512. Thus in 1-bit Adam v2 we added support of a momentum mask for users to specify those params that have constant exact zeros in their gradients. See example script for how to configure this momentum mask. One thing to note is that we don’t use momentum mask saved in checkpoints since this mask could change during training (e.g., BERT seqlen 128 and 512 require different masks). So you have to provide this mask every time in your training script.

Watch out! 1-bit Adam relies on an compression error compensation mechanism to maintain the convergence speed at compression stage. When loading checkpoints, we actually reset the compression errors for 3 reasons: 1) The worker and server error at each GPU are distinct, so in current implementation only rank 0’s errors are saved in the checkpoint. Thus we have to reset the errors. If we want to save them correctly we need O(num_gpu*model_size) memory in order to gather all the error, which is a very large memory requirement. It’s possible to save them in a distributed way, but it will make the checkpoint saving/loading much more complicated. 2) Even if we are able to save the compression errors correctly, you need to have the exact same number of GPUs in order to load them correctly. 3) We verified on BERT pre-training that occasionally resetting the compression error at checkpoint loading does not affect the convergence. However, please avoid frequent checkpoint loading which could break the error compensation mechanism thus affect the convergence.

You can also use a pre-trained BERT model checkpoint from either DeepSpeed, HuggingFace, or TensorFlow to run the fine-tuning.

Note: For details about loading checkpoint, argument parsing, initialization, forward pass, backward pass, weight update and evaluation, please refer to the BingBertSQuAD Fine-tuning tutorial.

We provide example scripts under DeepSpeedExamples/training/BingBertSquad/1-bit_adam/. There are 3 sets of scripts corresponding to NCCL-based implementation, MPI-based implementation on Ethernet systems, and MPI-based implementation on InfiniBand systems. For MPI-based implementation, we provide both example scripts when launching with deepspeed or mpirun.

The deepspeed_onebitadam_bsz96_config.json file gives the user the ability to specify DeepSpeed options in terms of batch size, micro batch size, optimizer, learning rate, and other parameters. When running the nvidia_run_squad_deepspeed.py, in addition to the --deepspeed flag to enable DeepSpeed, the appropriate DeepSpeed configuration file must be specified using --deepspeed_config deepspeed_onebitadam_bsz96_config.json.

Table 1 shows the fine-tuning configuration we used in our experiments.

Table 1. Fine-tuning configuration

Accuracy: The results are summarized in the table below. The total batch size is set to 96 and training is conducted on 32 GPUs for 2 epochs. A set of parameters (seeds and learning rates) were tried and the best ones were selected. We fixed the learning rate to 3e-5. The table below shows the F1 and the EM scores we achieved that are on-par or better than the HuggingFace results.

Training Speed and Scalability:

Performance results of SQuAD Fine-tuning can be seen from our blog post and our paper.

For data downloading and pre-processing, please refer to the BERT Pre-training tutorial.

We provide example scripts under DeepSpeedExamples/bing_bert/1-bit_adam/. There are 3 sets of scripts corresponding to NCCL-based implementation, MPI-based implementation on Ethernet systems, and MPI-based implementation on InfiniBand systems. For MPI-based implementation, we provide both example scripts when launching with deepspeed or mpirun.

The deepspeed_bsz4k_onebit_config_seq128_*.json file gives the user the ability to specify DeepSpeed options in terms of batch size, micro batch size, optimizer, learning rate, and other parameters.

Below is the DeepSpeed configuration file for running BERT-large pre-training with sequence length of 128 using the 1-bit Adam optimizer.

The above file is for BERT-large. For BERT-base training (sequence length 128), the suggested freeze_step is 16000. For sequence 512 pre-training, we suggest to use a freeze_step of 1500 for both BERT-base and BERT-large. And make sure to set the comm_backend_name and cuda_aware correctly as described above.

Performance results of BERT Pre-training can be seen from our blog post and our paper.

Updated: November 5, 2025

**Examples:**

Example 1 (unknown):
```unknown
git clone https://github.com/deepspeedai/DeepSpeed
cd DeepSpeed
git submodule update --init --recursive
cd DeepSpeedExamples/
```

Example 2 (unknown):
```unknown
pip install deepspeed[1bit_adam]
```

Example 3 (unknown):
```unknown
deepspeed --launcher=[mvapich|openmpi] script.py
```

Example 4 (unknown):
```unknown
mpirun -np [#processes] -ppn [#GPUs on each node] -hostfile [hostfile] [MPI flags] python [training_script.py]
```

---

## Getting Started with DeepSpeed on Azure

**URL:** https://www.deepspeed.ai/tutorials/azure/

**Contents:**
- Getting Started with DeepSpeed on Azure
    - Contents
- DeepSpeed on Azure via AzureML
- DeepSpeed on Azure VMs

This tutorial will help you get started with DeepSpeed on Azure.

If you don’t already have an Azure account please see more details here: https://azure.microsoft.com/.

The recommended and simplest method to try DeepSpeed on Azure is through AzureML. A training example and a DeepSpeed autotuning example using AzureML v2 can be found here.

For AzureML v1 examples, please take a look at easy-to-use examples for Megatron-DeepSpeed, Transformers and CIFAR training here.

Our Megatron-DeepSpeed contains the most up to date recipe for end-to-end training on AzureML.

If you don’t have access to AzureML or if want to build a custom environments using Azure virtual machines or Azure VM Scale-Sets (VMSS), we are working on easy-to-use cluster setup scripts that will be published in the next few weeks.

If you already have a cluster setup, you can use the azure recipes that can easily be modified to train various model configurations.

Updated: November 5, 2025

---

## Mixed Precision ZeRO++

**URL:** https://www.deepspeed.ai/tutorials/mixed_precision_zeropp/

**Contents:**
- Mixed Precision ZeRO++
    - Contents
- Key Designs
- Enabling Mixed Precision ZeRO++ (MixZ++)
  - DeepSpeed Configuration Changes
  - Training Script Changes

Mixed Precision ZeRO++ (MixZ++) is a set of optimization strategies based on ZeRO and ZeRO++ to improve the efficiency and reduce memory usage for large model training and inference when users use Low-Rank Adaptation (LoRA) training. MixZ++ partitions model parameters across GPUs to reduce footprint and gathers them with quantized communication only when needed similar to its ZeRO and ZeRO++ siblings. Our evaluation indicates MixZ++ increases the training throughput by up to 3.3x for the Llama-2-70B model running on 128 V100 GPUs. Read our DeepSpeed Chat Blog, ZeRO++ blog and paper to learn more!

We recommend that you read the tutorials on Getting Started, ZeRO and Megatron-DeepSpeed before stepping through this tutorial.

Mixed Precision ZeRO++ (MixZ++) inherits key designs from ZeRO++, namely quantized weights (qwZ), hierarchical partitioning ZeRO (hpZ) but has different applicability:

Collectively, the optimizations bring better scalability and efficiency to LoRA training. Each of the components can be enabled independent of each other and collectively as a group.

A ready to go MixZ++ example has been prepared at MixZ++ example script. If you prefer to manually enable MixZ++ in your pipeline, please refer to the instructions below.

An example snippet of deepspeed configurations with all MixZ++ optimization enabled is shown below:

Note that for multi-node training, the "zero_hpz_partition_size" should be set to the number of GPUs per node. For example, if you have 8 GPUs per node, then "zero_hpz_partition_size" should be set to 8. For single-node training, the "zero_hpz_partition_size" should not be set.

DeepSpeed engine will identify the LoRA frozen parameters if the LoRA model is passed when DeepSpeed initializes. However, the popular implementation is to initialize a base model and then convert to LoRA model later. In such cases, users need to explicitly call DeepSpeed engine after LoRA model is converted. This is only a 1-line effort. An example snippet of training script is shown below:

Congratulations! You have completed the Mixed Precision ZeRO++ tutorial.

Updated: November 5, 2025

**Examples:**

Example 1 (unknown):
```unknown
{
    "zero_optimization": {
        "stage": 3,
        "..."
        "zero_quantized_nontrainable_weights": true,
        "zero_hpz_partition_size": 16,
        "..."
    }
}
```

Example 2 (unknown):
```unknown
model, optimizer, _, lr_scheduler = deepspeed.initialize(
    model=model,
    optimizer=optimizer,
    args=args,
    config=ds_config,
    lr_scheduler=lr_scheduler,
    dist_init_required=True)
# ...
# (the custom code to convert base model to LoRA model)
# ...
# call DeepSpeed engine again to identify LoRA frozen parameters
model.optimizer.quantize_nontrainable_params()
# ...
```

---

## Arctic Long Sequence Training (ALST) for HF Transformers integration

**URL:** https://www.deepspeed.ai/tutorials/ulysses-alst-sequence-parallelism/

**Contents:**
- Arctic Long Sequence Training (ALST) for HF Transformers integration
    - Contents
- Part 1: Ulysses Sequence Parallelism for HF Transformers
  - UlyssesSPAttentionHF.register_with_transformers
  - UlyssesSPDataLoaderAdapter
  - Loss averaging
- Nuances
  - Why do labels need to be pre-shifted?
- Part 2. Arctic Long Sequence Training (ALST) enables even longer sequence lengths using a bag of tricks
  - Tiled loss computation

It enables on LLama-8B training on 500K tokens on a single H100 GPU, 3.7M on a single node, and 15M on Llama-8B using just four nodes.

To learn about this technology please read this paper: Arctic Long Sequence Training: Scalable And Efficient Training For Multi-Million Token Sequences.

It’s already fully integrated into Arctic Training, see this guide.

The rest of the document explains how to integrate it into other frameworks or your own training loop.

There is another older version of UlyssesSP which only works with Megatron-Deepspeed and can be found here.

If you want to integrate Ulysses Sequence Parallelism for HF Transformers into your framework, it’s easy to do. Here is a full training loop with a hardcoded dataset:

This example has been derived from the UlyssesSP unit test.

Let’s study the parts not normally present in the vanilla training loop:

UlyssesSPAttentionHF.register_with_transformers injects Ulysses Attention adapter into HF Transformers.

It also creates nccl process groups encapsulated by the mpu object it returns.

For the model_name_or_path argument you can also pass the already existing HF Transformers model object.

UlyssesSPAttentionHF.register_with_transformers has to be called before from_pretrained is called.

If seq_length_is_variable is True (which is also the default value), UlyssesSPAttentionHF will recalculate the shapes on each forward based on the incoming batch’s shapes - in which case you don’t need to set seq_length - you can just skip it like so:

If, however, all your batches have an identical sequence length, then you’d save a few microseconds per run with using the seq_length_is_variable=False code path, which will pre-measure all shapes once and re-use them in all runs:

If you pass seq_length, remember that it has to be divisible by sequence_parallel_size. And of course, this also applies to all batches, even if you use seq_length_is_variable=True.

This takes an existing DataLoader object and returns a new one that will shard the batches on the sequence dimension and synchronize all GPUs of the replica to return to each rank only its corresponding sequence shard.

It also takes care of replacing labels with shift_labels in the batch, by pre-shifting labels, which is crucial for the correct loss calculation when using Ulysses sequence parallelism.

Since each rank processes a segment we need to average loss. To get the gradients right we need to use a differentiable all_gather

In theory you could just average losses_per_rank, but the system supports variable sequence length so the last rank is likely to have a shorter sequence length and also use cases like SFT may have a variable number of tokens that contribute to the loss calculation, so it’s best to compute a weighted loss.

When using batch sharding one can’t let the upstream loss function do the labels shifting. Here is why:

When calculating loss in an unsharded batch we end up with (shift left):

When sharded we lose label 5 once shifted:

So a new API was added in HF transformers to support pre-shifted labels, and then we end up with the correct labels passed to the loss function for each shard:

If you use Liger-kernel it’ll automatically do the very memory efficient loss computation without manifesting intermediate full logits tensor, which consume a huge among of GPU memory when long sequence lengths are used.

If your model isn’t supported by Liger-kernel you can use our implementation, which uses about the same amount of memory, but which is slightly slower since it’s written in plain PyTorch. Here is a simplified version of it:

You can see the full version here.

If you want to use Tiled MLP computation you’d need to monkey patch the model you work with, for a full example see this unit test.

You can of course come up with a different way of computing the number of shards to be used.

You will find a prototype implementation version here

We hope PyTorch core will provide an internal support for offloading. If not we will need to come up with some better solution - perhaps using a context manager.

This currently implementation isn’t yet efficient (blocking), but it barely makes any difference for very long sequence lengths where matmuls dominate the compute.

Before launching your script add:

This will help with minimizing memory fragmentation and will allow a longer sequence length.

Updated: November 5, 2025

**Examples:**

Example 1 (python):
```python
# train.py
from deepspeed.runtime.sequence_parallel.ulysses_sp import UlyssesSPAttentionHF, UlyssesSPDataLoaderAdapter
from deepspeed.runtime.utils import move_to_device
from deepspeed.utils import groups
from torch import tensor
from transformers import AutoModelForCausalLM
import deepspeed
import deepspeed.comm as dist
import torch

model_name_or_path = 'hf-internal-testing/tiny-random-LlamaForCausalLM'
seq_length = 64
sequence_parallel_size = 2
micro_batch_size = 1

config_dict = {
    "train_micro_batch_size_per_gpu": 1,
    "zero_optimization": {
        "stage": 3,
    },
    "optimizer": {
        "type": "Adam",
        "params": {
            "lr": 1e-3
        }
    },
    "sequence_parallel_size": sequence_parallel_size,
}

dtype = torch.bfloat16

# a simple Dataset
# replace with a real dataset but make sure `position_ids` are returned
input_ids = tensor([[1, 10, 10, 10, 2, 2], [1, 20, 20, 20, 2, 2]], )
position_ids = tensor([[0, 1, 2, 3, 4, 5], [0, 1, 2, 3, 4, 5]])
ds = torch.utils.data.TensorDataset(input_ids, position_ids)
def collate_fn(batch):
    input_ids, position_ids = batch[0]
    return dict(input_ids=input_ids.unsqueeze(0),
                position_ids=position_ids.unsqueeze(0),
                labels=input_ids.unsqueeze(0))

dist.init_distributed(dist_backend='nccl', dist_init_required=True)

# Ulysses injection into HF Transformers
mpu = UlyssesSPAttentionHF.register_with_transformers(
    model_name_or_path=model_name_or_path,
    core_attn_implementation="sdpa",
    sequence_parallel_size=sequence_parallel_size,
    micro_batch_size=micro_batch_size,
    seq_length=seq_length,
    seq_length_is_variable=True,
)

# Deepspeed setup
model = AutoModelForCausalLM.from_pretrained(model_name_or_path)
model, _, _, _ = deepspeed.initialize(config=config_dict,
                                        model=model,
                                        model_parameters=model.parameters(),
                                        mpu=mpu)

# UlyssesSPDataLoaderAdapter injection
sp_group = groups._get_sequence_parallel_group()
sp_world_size = groups._get_sequence_parallel_world_size()
sp_rank = groups._get_sequence_parallel_rank()
dl = torch.utils.data.DataLoader(ds, batch_size=micro_batch_size, collate_fn=collate_fn)
dl = UlyssesSPDataLoaderAdapter(
    dl,
    sp_rank=sp_rank,
    sp_group=sp_group,
    sp_world_size=sp_world_size,
    device=model.device,
)

# Normal training loop
for iter, batch in enumerate(dl):
    batch = move_to_device(batch, model.device)

    outputs = model(**batch)
    # as of this writing HF doesn't calculate loss with shift_labels yet and requires us to do it manually (liger does that automatically)
    shift_labels = batch["shift_labels"]
    loss = model.module.loss_function(
        logits=outputs.logits,
        labels=None,
        shift_labels=shift_labels,
        vocab_size=model.module.config.vocab_size,
    )

    # differentiable weighted per-shard-loss aggregation across ranks
    losses_per_rank = torch.distributed.nn.functional.all_gather(loss, group=sp_group)
    # special dealing with SFT that has prompt tokens that aren't used in loss computation
    good_tokens = (shift_labels != -100).view(-1).sum()
    good_tokens_per_rank = torch.distributed.nn.functional.all_gather(good_tokens, group=sp_group)
    total_loss = sum(losses_per_rank[rank] * good_tokens_per_rank[rank] for rank in range(sp_world_size))
    total_good_tokens = sum(good_tokens_per_rank)
    loss = total_loss / max(total_good_tokens, 1)

    if dist.get_rank() == 0:
        print(f"{iter}: {loss=}")

    model.backward(loss)
```

Example 2 (unknown):
```unknown
$ deepspeed --num_gpus 2 train.py
0: loss=tensor(10.4248, device='cuda:0', grad_fn=<DivBackward0>)
1: loss=tensor(10.4248, device='cuda:0', grad_fn=<DivBackward0>)
2: loss=tensor(10.3818, device='cuda:0', grad_fn=<DivBackward0>)
3: loss=tensor(10.3818, device='cuda:0', grad_fn=<DivBackward0>)
```

Example 3 (unknown):
```unknown
mpu = UlyssesSPAttentionHF.register_with_transformers(
    model_name_or_path=model_name_or_path,
    core_attn_implementation="sdpa",
    sequence_parallel_size=sequence_parallel_size,
    micro_batch_size=micro_batch_size,
    seq_length=seq_length,
    seq_length_is_variable=True,
)
```

Example 4 (unknown):
```unknown
mpu = UlyssesSPAttentionHF.register_with_transformers(
    model_name_or_path=model_name_or_path,
    core_attn_implementation="sdpa",
    sequence_parallel_size=sequence_parallel_size,
    micro_batch_size=micro_batch_size,
    seq_length_is_variable=True,
)
```

---

## Getting Started with DeepSpeed-MoE for Inferencing Large-Scale MoE Models

**URL:** https://www.deepspeed.ai/tutorials/mixture-of-experts-inference/

**Contents:**
- Getting Started with DeepSpeed-MoE for Inferencing Large-Scale MoE Models
    - Contents
- MoE Inference Performance
- End-to-End MoE Inference Example
  - Initializing for Inference
  - Various configuration options
  - Performance for standard MoE model
  - Faster Performance and Lower Inference Cost using PR-MoE optimizations

DeepSpeed-MoE Inference introduces several important features on top of the inference optimization for dense models (DeepSpeed-Inference blog post). It embraces several different types of parallelism, i.e. data-parallelism and tensor-slicing for the non-expert parameters and expert-parallelism and expert-slicing for the expert parameters. To maximize the aggregate memory-bandwidth, we provide the communication scheduling with parallelism coordination to effectively group and route tokens with the same critical-data-path. Moreover, we propose new modeling optimizations, PR-MoE and MoS, to reduce MoE model size while maintaining accuracy. For more information on the DeepSpeed MoE inference optimization, please refer to our blog post.

DeepSpeed provides a seamless inference mode for the variant of MoE models that are trained via the DeepSpeed-MoE library (MoE tutorial). To do so, one needs to simply use the deepspeed-inference engine to initialize the model to run the model in the eval mode.

In modern production environments, powerful DL models are often served using hundreds of GPU devices to meet the traffic demand and deliver low latency. It is important to explore how these two broad goals of high throughput and low latency can be realized for MoE model inference at scale.

For dense models, throughput can be increased by using multiple GPUs and data parallelism (independent replicas with no inter-GPU communication), whereas lower latency can be achieved by techniques like tensor-slicing to partition the model across multiple GPUs. The best case scaling in terms of total throughput is linear with respect to the increasing number of GPUs, i.e., a constant throughput per GPU. This is possible for pure data parallel inference scenarios as there is no communication between GPUs. To reduce latency, tensor-slicing style of model parallelism has proven to be beneficial but it comes with the cost - communication overhead between GPUs - which often lowers per GPU throughput and results in sublinear scaling of total throughput. In other words, for dense models, we cannot leverage parallelism to optimize both latency and throughput at the same time; there is a tradeoff between them. MoE inference, however, provides unique opportunities to offer optimized latency and throughput simultaneously while scaling to a large number of devices.

Figure below shows how we achieve both low latency and super-linear throughput increase simultaneously. We discuss this at length in our paper.

In this part, we elaborate the usage of MoE inference support in the DeepSpeed library using an end-to-end example.

For inference with DeepSpeed-MoE, use init_inference API to load the DeepSpeed MoE model for inference. Here, you can specify the model-parallelism/tensor-slicing degree (mp_size), expert parallelism degree (ep_size), and number of experts (moe_experts). We create various process groups based on minimum of the world_size (total number of GPUs) and expert parallel size. By using this group, we can partition the experts among expert-parallel GPUs. If number of experts is lower than total number of GPUs, DeepSpeed-MoE leverages expert-slicing for partitioning the expert parameters between the expert-parallel GPUs. Furthermore, if the model has not been loaded with the appropriate checkpoint, you can also provide the checkpoint description using a json file or simply pass the 'checkpoint' path to load the model. To inject the high-performance inference kernels, you can set replace_with_kernel_inject to True.

Here, we show a text-generation example using an MoE model for which we can specify the model-parallel size and number of experts. DeepSpeed inference-engine takes care of creating the different parallelism groups using the tensor-slicing degree, number of experts, and the total number of GPUs used for running the MoE model. Regarding the expert parameters, we first use the expert-parallelism to assign each group of experts to one GPU. If number of GPUs is higher than number of experts, we use expert-slicing to partition each expert vertically/horizontally across the GPUs.

Let’s take a look at some of the parameters passed to run our example. Please refer to DeepSpeed-Example for a complete generate-text inference example.

In order to show the performance scaling of DeepSpeed-MoE inference with increasing number of GPUs, we consider a 52B model architecture with 128 experts and 1.3B dense model using the parameters shown in the script above. In this example, we set tensor-slicing degree to one since the non-expert part of the model is relatively small (805M parameters). We use the last flag, ds-inference, to switch between DeepSpeed-MoE and PyTorch implementations.

For DeepSpeed-MoE inference, we show our results in this tutorial using two versions: 1) Generic, the current open source version of the DeepSpeed library that includes support for flexible parallelism and PR-MoE model optimization, and 2) Specialized, the most optimized version of DeepSpeed MoE inference system including special computation and communication kernels that will be released later. As mentioned in our blog post, MoE inference optimizations will be released in a staged fashion.

Figure below shows the inference performance of three different configuration, PyTorch, DeepSpeed-MoE (Generic), and DeepSpeed-MoE (Specialized), running on 8, 16, and 32 GPUs. Compared to PyTorch, DeepSpeed-MoE obtains significantly higher performance benefit as we increased the number of GPUs. By using the generic DeepSpeed-MoE inference, we can get between 24% to 60% performance improvement over PyTorch. Additionally, by enabling the full features of DeepSpeed-MoE inference, such as communication optimization and MoE customized kernels, the performance speedup gets boosted (2x – 3.2x).

To select between different MoE structures, we add a new parameter in our inference example, called mlp-type, to select between the 'standard' MoE structure and the 'residual' one to enable the modeling optimizations offered by PR-MoE. In addition to changing the mlp-type, we need to pass the number of experts differently when using PR-MoE. In contrast to standard MoE which uses the same number of experts for each MoE layer, PR-MoE uses different expert-count for the initial layers than the deeper layers of the network. Below is an example of PR-MoE using a mixture of 64 and 128 experts for every other layers:

To evaluate the performance of PR-MoE, we use the two model structures, 'standard' and 'residual' and the configuration parameters as shown in the table below. Since we cannot fit the non-expert part of the 24B+MoE-128 on a single GPU, we use a model-parallel size larger than one. We choose the tensor-slicing degree in order to get the best performance benefit.

We use 1 node (8 A100 GPUs) to run inference on the 2.4B+MoE-128 and 8 nodes (64 A100 GPUs) for the 24B+MoE-128. Figure below shows the performance of three different configurations: MoE-Standard (PyTorch), MoE-Standard (DeepSpeed-Generic), PR-MoE (DeepSpeed-Generic). By using the standard-MoE DeepSpeed improves inference performance by 1.4x and 1.65x compared to PyTorch for the two models, respectively. Furthermore, by using the PR-MoE, we can improve the performance speedups to 1.81x and 1.87x, while keeping the model quality maintained.

More performance results and scaling toward bigger models and larger number of GPUs can be seen from our blog post and paper.

Congratulations! You have completed the DeepSpeed MoE inference tutorial.

Updated: November 5, 2025

**Examples:**

Example 1 (unknown):
```unknown
import deepspeed
import torch.distributed as dist

# Set expert-parallel size
world_size = dist.get_world_size()
expert_parallel_size = min(world_size, args.num_experts)

# create the MoE model
moe_model = get_model(model, ep_size=expert_parallel_size)
...

# Initialize the DeepSpeed-Inference engine
ds_engine = deepspeed.init_inference(moe_model,
                                     mp_size=tensor_slicing_size,
                                     dtype=torch.half,
                                     moe_experts=args.num_experts,
                                     checkpoint=args.checkpoint_path,
                                     replace_with_kernel_inject=True,)
model = ds_engine.module
output = model('Input String')
```

Example 2 (unknown):
```unknown
generate_samples_gpt.py \
       --tensor-model-parallel-size 1 \
       --num-experts ${experts} \
       --num-layers 24 \
       --hidden-size 2048 \
       --num-attention-heads 32 \
       --max-position-embeddings 1024 \
       --tokenizer-type GPT2BPETokenizer \
       --load $checkpoint_path \
       --fp16 \
       --ds-inference \
```

Example 3 (unknown):
```unknown
experts="64 64 64 64 64 64 64 64 64 64 128 128"
generate_samples_gpt.py \
       --tensor-model-parallel-size 1 \
       --num-experts ${experts} \
       --mlp_type 'residual' \
       --num-layers 24 \
       --hidden-size 2048 \
       --num-attention-heads 16 \
       --max-position-embeddings 1024 \
       --tokenizer-type GPT2BPETokenizer \
       --load $checkpoint_path \
       --fp16 \
       --ds-inference \
```

---

## Curriculum Learning: A Regularization Method for Efficient and Stable Billion-Scale GPT Model Pre-Training

**URL:** https://www.deepspeed.ai/tutorials/curriculum-learning/

**Contents:**
- Curriculum Learning: A Regularization Method for Efficient and Stable Billion-Scale GPT Model Pre-Training
    - Contents
- 1. Configurations and tuning strategy
  - 1.1 fixed_linear schedule
  - 1.2 fixed_root schedule
  - 1.3 fixed_discrete schedule
- 2. Curriculum learning for Megatron-LM GPT-2 pre-training
  - 2.1 Training data truncation
  - 2.2 Disable batch size warmup (--rampup-batch-size)
  - 2.3 Token-based training termination

Watch out! On 12/12/2022, we released DeepSpeed Data Efficiency Library which provides a more general curriculum learning support. This legacy curriculum learning feature below is still supported but we recommend to use the Data Efficiency Library (tutorial).

Note: This tutorial was updated on 10/29/2021. Changes include: 1) A more detailed tuning strategy. 2) Pipeline parallelism support. 3) Token-based learning rate decay. 4) A new GPT-2 example at github.com/deepspeedai/Megatron-DeepSpeed. See details below.

In this tutorial, we introduce DeepSpeed’s curriculum learning-based data pipeline, which presents easier or simpler examples earlier during training. By enabling stable training with 8x/4x larger batch size/learning rate (whereas the baseline approach struggles with training divergence), we observe that curriculum learning (based on sequence length) provides stable and 3.3x faster GPT-2 pre-training (tested on 117M and 1.5B parameters), together with better token-wise convergence speed and zero-shot WikiText-103/LAMBADA evaluation results. In addition, since curriculum learning only affects the data pipeline, its benefit is complementary to many DeepSpeed features and other system optimization techniques. For example, curriculum learning is compatible with DeepSpeed’s ZeRO Redundancy Optimizer, ZeRO-Offload, and 3D Parallelism.

To illustrate the benefits and usage of curriculum learning, we use the Megatron-LM GPT-2 pre-training task as example. For more details on this task, please refer to the Megatron-LM GPT2 tutorial. In addition, we also have a paper which provides the technical details including implementation and evaluations.

Curriculum learning can be used by setting the curriculum_learning key in the DeepSpeed configuration file:

To support curriculum learning, we add the following new parameters:

curriculum_type is the type of curriculum difficulty metric. Currently we support the seqlen metric which presents shorter sequences earlier in training. We implement this type of curriculum learning by performing training data sequence truncation before the actual forward pass. We will describe how to implement this in the Megatron-LM GPT-2 pre-training example below.

min_difficulty is the starting difficulty level. For the seqlen metric it means we start with sequence length as min_difficulty. We observe that lower min_difficulty usually provides better stability/convergence speed but with two caveats: First, sometimes (especially for large models) starting with too small difficulty level may lead to severe overfitting (e.g., training loss divergence or validation perplexity fluctuations) thus hurting the convergence. Second, for seqlen metric we recommended setting min_difficulty to a multiple of 8 (for FP16 data) or 16 (for INT8 data) to enable NVIDIA GPU’s Tensor Core acceleration. To tune this hyperparameter for seqlen metric, we recommend starting with min_difficulty at 8 (million-scale models) or 64 (billion-scale models), and then increase it if you observe divergence or validation perplexity fluctuations at the very beginning.

max_difficulty is the ending difficulty level. For the seqlen metric it should be set to the full sequence length (e.g., 1024 for Megatron-LM GPT-2 pre-training).

schedule_type is the scheduling policy for curriculum learning (i.e., which difficulty level to use at certain step). Currently we support three schedules: fixed_linear, fixed_root, and fixed_discrete. We recommend to first try the fixed_linear schedule, which is easier to tune and provides great training stability/efficiency gain in our tests. Each schedule has its own configurations:

For fixed_linear schedule there are two configurations:

The total_curriculum_step is the total number of steps for the curriculum learning. For fixed_linear schedule the difficulty level will increase linearly from min_difficulty to max_difficulty during total_curriculum_step steps. This configuration must be tuned for each training task. We observe that too small and too large total_curriculum_step are both suboptimal: with too small total_curriculum_step curriculum learning might not be able to provide enough training stability benefit so the training might still diverge; with too large total_curriculum_step the model may overfit during curriculum learning on the easier/simpler training data thus hurt the overall convergence. To tune this hyperparameter, we recommend a binary search to find the largest total_curriculum_step that does not have significant validation perplexity fluctuation during the first few multiples of LR warmup steps. The underlying rationale can be found in our paper Appendix A.1.

The difficulty_step configuration ensures that at any time the difficulty level is a multiple of difficulty_step. A smaller value is preferable since it gives more smooth curriculum and better stability. We usually set it to 8 (for FP16 data) or 16 (for INT8 data) to enable NVIDIA GPU’s Tensor Core acceleration. If this is unrelated to your hardware, you can set it to 1.

For fixed_root schedule there are three configurations:

The total_curriculum_step and difficulty_step have the same meaning as for the fixed_linear schedule. The root_degree determines the root degree of the root function of the schedule. The difficulty level at certain step is determined as ((current step/total_curriculum_step)**(1/root_degree)) * (max_difficulty - min_difficulty) + min_difficulty. Thus fixed_linear is basically a special case of fixed_root with root_degree as 1. In our (limited) study, we find the fixed_root schedule does not provide any clear advantage over fixed_linear schedule, while requiring one additional parameter.

For fixed_discrete schedule there are two configurations:

The difficulty is a list of difficulty levels to be used during schedule. The max_step is a list of step timestamp to determine when to switch to next difficulty level. For example, the json config above means that at step 1-5 difficulty 1 is used, at step 6-10 difficulty 2 is used, from step 11 difficulty 3 is used. This fixed_discrete schedule provides the most flexible curriculum learning scheduling. However, we find that one risk of this kind of schedule is that if the model stays at certain difficulty level for too long, training divergence may happen when switching to next difficulty due to severe overfitting.

Watch out! After the update on 10/29/2021, now there are two curriculum learning examples for Megatron-LM GPT-2 pre-training. Both of them have some unique features and limitations. See details below.

We provide two curriculum learning examples for Megatron-LM GPT-2 pre-training:

The first one is at Megatron-DeepSpeed/tree/main/examples_deepspeed/curriculum_learning. This integration is based on a newer Megatron-LM fork, and only this curriculum learning example supports pipeline parallelism. However, as of 10/29/2021, we haven’t verified ZeRO-2 and ZeRO-3 on this fork. Overall, we highly recommend you to use this example if your model does not require ZeRO-2/3.

The second one is at DeepSpeedExamples/Megatron-LM-v1.1.5-ZeRO3/curriculum_learning/. This integration is based on an older Megatron-LM hard copy that we will eventually deprecate and this curriculum learning example does not support pipeline parallelism. We recommend you to ONLY use this example if your model requires ZeRO-2/3.

Besides the DeepSpeed curriculum learning json configurations described above, there are some other necessary changes on the user side to integrate curriculum learning:

To enable seqlen-based curriculum learning, we need to add the functionality of training data truncation based on the given curriculum sequence length. For the case without pipeline parallelism, it is necessary to add a curriculum_seqlen argument in the model’s forward pass and use it to perform training data sequence length truncation. For Megatron-LM GPT-2 pre-training, we implement this in forward() in megatron/model/gpt2_model.py and in forward_step() in pretrain_gpt2.py.

For the case with pipeline parallelism, due to DeepSpeed engine limitations we cannot inject the curriculum_seqlen argument in the forward pass. Instead, we create a duplicate of deepspeed.runtime.data_pipeline.curriculum_scheduler on the user side, and use it to retrieve the curriculum_seqlen. This implementation can be found in megatron/training.py.

In our paper section 5.4 we demonstrate that curriculum learning (seqlen-based) provides much better training stability than the batch size warmup technique introduced by Open AI GPT-3. So when using curriculum learning you need to remove the --rampup-batch-size config in your training script. It’s not recommended using both curriculum learning and batch size warmup, because both of them reduce the number of tokens in a batch. Another related change you might want is to increase your micro batch size, since without batch size warmup your batch size will be fixed now.

Because curriculum learning changes the length of each sequence/sample during training, it is very hard/impossible to use a number of steps/samples to terminate the training exactly at the desired number of tokens. Thus, we add a --train-tokens config for accurate token-based termination. We recommend increasing your original --train-samples or --train-iters to a large enough number (e.g., 3X of what you used for baseline), and set --train-tokens at the exact desired number of training tokens.

Again because curriculum learning changes the number of tokens per batch, in our paper Appendix A.2 we show that it is also necessary to change the LR decay to token-based (to avoid decaying LR too fast). Thus, we add a --lr-decay-tokens which will be the number of LR decay tokens. If previously you were using --lr-decay-samples, you can calculate your --lr-decay-tokens simply by multiplying the former by full seqlen (e.g., 1K for GPT-2 and 2K for GPT-3). If previously you were using --lr-decay-iters, you can calculate your --lr-decay-tokens by multiplying the former by full seqlen and the global batch size. Then you need to replace --lr-decay-samples or --lr-decay-iters with --lr-decay-tokens in your script.

For LR warmup we don’t change it to token-based, because doing so for curriculum learning means slowing down the LR warmup, which is both unnecessary and harmful. However, to avoid too fast warmup you may need to adjust your --lr-warmup-samples or --lr-warmup-iters from non-CL cases for various reasons (e.g., if you used --rampup-batch-size in non-CL case, for CL we don’t use it so the number of samples per batch will be different at beginning). Assuming you want to use X tokens to warmup the LR (for OpenAI GPT-3 this was 375M tokens), then for curriculum learning case you shall set --lr-warmup-samples as X divided by the min_difficulty, or set --lr-warmup-iters as X divided by min_difficulty * --global-batch-size. This is a rough estimation based on that curriculum learning starts from seqlen min_difficulty and it won’t increase too much during LR warmup.

Updated: November 5, 2025

**Examples:**

Example 1 (unknown):
```unknown
{
  "train_batch_size": 4096,
  "gradient_accumulation_steps": 1,
  "steps_per_print": 1,
  "optimizer": {
    "type": "Adam",
    "params": {
      "lr": 0.00015,
      "max_grad_norm": 1.0,
      "betas": [0.9, 0.95]
    }
  },
  "gradient_clipping": 1.0,
  "fp16": {
    "enabled": true,
    "loss_scale": 0,
    "loss_scale_window": 1000,
    "hysteresis": 2,
    "consecutive_hysteresis": false,
    "min_loss_scale": 1
  },
  "curriculum_learning": {
    "enabled": true,
    "curriculum_type": "seqlen",
    "min_difficulty": 8,
    "max_difficulty": 1024,
    "schedule_type": "fixed_linear",
    "schedule_config": {
      "total_curriculum_step": 15000,
      "difficulty_step": 8
    }
  }
}
```

Example 2 (unknown):
```unknown
"schedule_type": "fixed_linear",
"schedule_config": {
  "total_curriculum_step": 15000,
  "difficulty_step": 8
}
```

Example 3 (unknown):
```unknown
"schedule_type": "fixed_root",
"schedule_config": {
  "total_curriculum_step": 15000,
  "difficulty_step": 8,
  "root_degree": 2
}
```

Example 4 (unknown):
```unknown
"schedule_type": "fixed_discrete",
"schedule_config": {
  "difficulty": [1,2,3],
  "max_step": [5,10]
}
```

---

## Getting Started

**URL:** https://www.deepspeed.ai/getting-started/

**Contents:**
- Getting Started
    - Contents
- Installation
- Writing DeepSpeed Models
  - Training
  - Model Checkpointing
- DeepSpeed Configuration
- Launching DeepSpeed Training
- Resource Configuration (multi-node)
  - Launching without passwordless SSH

DeepSpeed model training is accomplished using the DeepSpeed engine. The engine can wrap any arbitrary model of type torch.nn.module and has a minimal set of APIs for training and checkpointing the model. Please see the tutorials for detailed examples.

To initialize the DeepSpeed engine:

deepspeed.initialize ensures that all of the necessary setup required for distributed data parallel or mixed precision training are done appropriately under the hood. In addition to wrapping the model, DeepSpeed can construct and manage the training optimizer, data loader, and the learning rate scheduler based on the parameters passed to deepspeed.initialize and the DeepSpeed configuration file. Note that DeepSpeed automatically executes the learning rate schedule at every training step.

If you already have a distributed environment setup, you’d need to replace:

The default is to use the NCCL backend, which DeepSpeed has been thoroughly tested with, but you can also override the default.

But if you don’t need the distributed environment setup until after deepspeed.initialize() you don’t have to use this function, as DeepSpeed will automatically initialize the distributed environment during its initialize. Regardless, you will need to remove torch.distributed.init_process_group if you already had it in place.

Once the DeepSpeed engine has been initialized, it can be used to train the model using three simple APIs for forward propagation (callable object), backward propagation (backward), and weight updates (step).

Under the hood, DeepSpeed automatically performs the necessary operations required for distributed data parallel training, in mixed precision, with a pre-defined learning rate scheduler:

Gradient Averaging: in distributed data parallel training, backward ensures that gradients are averaged across data parallel processes after training on an train_batch_size.

Loss Scaling: in FP16/mixed precision training, the DeepSpeed engine automatically handles scaling the loss to avoid precision loss in the gradients.

Learning Rate Scheduler: when using a DeepSpeed’s learning rate scheduler (specified in the ds_config.json file), DeepSpeed calls the step() method of the scheduler at every training step (when model_engine.step() is executed). When not using DeepSpeed’s learning rate scheduler:

Saving and loading the training state is handled via the save_checkpoint and load_checkpoint API in DeepSpeed which takes two arguments to uniquely identify a checkpoint:

DeepSpeed can automatically save and restore the model, optimizer, and the learning rate scheduler states while hiding away these details from the user. However, the user may want to save additional data that are unique to a given model training. To support these items, save_checkpoint accepts a client state dictionary client_sd for saving. These items can be retrieved from load_checkpoint as a return argument. In the example above, the step value is stored as part of the client_sd.

Important: all processes must call this method and not just the process with rank 0. It is because each process needs to save its master weights and scheduler+optimizer states. This method will hang waiting to synchronize with other processes if it’s called just for the process with rank 0.

DeepSpeed features can be enabled, disabled, or configured using a config JSON file that should be specified as args.deepspeed_config. A sample config file is shown below. For a full set of features see API doc.

DeepSpeed installs the entry point deepspeed to launch distributed training. We illustrate an example usage of DeepSpeed with the following assumptions:

DeepSpeed configures multi-node compute resources with hostfiles that are compatible with OpenMPI and Horovod. A hostfile is a list of hostnames (or SSH aliases), which are machines accessible via passwordless SSH, and slot counts, which specify the number of GPUs available on the system. For example,

specifies that two machines named worker-1 and worker-2 each have four GPUs to use for training.

Hostfiles are specified with the --hostfile command line option. If no hostfile is specified, DeepSpeed searches for /job/hostfile. If no hostfile is specified or found, DeepSpeed queries the number of GPUs on the local machine to discover the number of local slots available.

The following command launches a PyTorch training job across all available nodes and GPUs specified in myhostfile:

Alternatively, DeepSpeed allows you to restrict distributed training of your model to a subset of the available nodes and GPUs. This feature is enabled through two command line arguments: --num_nodes and --num_gpus. For example, distributed training can be restricted to use only two nodes with the following command:

You can instead include or exclude specific resources using the --include and --exclude flags. For example, to use all available resources except GPU 0 on node worker-2 and GPUs 0 and 1 on worker-3:

Similarly, you can use only GPUs 0 and 1 on worker-2:

DeepSpeed now supports launching training jobs without the need for passwordless SSH. This mode is particularly useful in cloud environments such as Kubernetes, where flexible container orchestration is possible, and setting up a leader-worker architecture with passwordless SSH adds unnecessary complexity.

To use this mode, you need to run the DeepSpeed command separately on all nodes. The command should be structured as follows:

In this setup, the hostnames in the hostfile do not need to be reachable via passwordless SSH. However, the hostfile is still required for the launcher to collect information about the environment, such as the number of nodes and the number of GPUs per node.

Each node must be launched with a unique node_rank, and all nodes must be provided with the address and port of the leader node (rank 0). This mode causes the launcher to act similarly to the torchrun launcher, as described in the PyTorch documentation.

When training across multiple nodes we have found it useful to support propagating user-defined environment variables. By default DeepSpeed will propagate all NCCL and PYTHON related environment variables that are set. If you would like to propagate additional variables you can specify them in a dot-file named .deepspeed_env that contains a new-line separated list of VAR=VAL entries. The DeepSpeed launcher will look in the local path you are executing from and also in your home directory (~/). If you would like to override the default name of this file or path and name with your own, you can specify this with the environment variable, DS_ENV_FILE. This is mostly useful if you are launching multiple jobs that all require different variables.

As a concrete example, some clusters require special NCCL variables to set prior to training. The user can simply add these variables to a .deepspeed_env file in their home directory that looks like this:

DeepSpeed will then make sure that these environment variables are set when launching each process on every node across their training job.

As described above, DeepSpeed provides its own parallel launcher to help launch multi-node/multi-gpu training jobs. If you prefer to launch your training job using MPI (e.g., mpirun), we provide support for this. It should be noted that DeepSpeed will still use the torch distributed NCCL backend and not the MPI backend.

To launch your training job with mpirun + DeepSpeed or with AzureML (which uses mpirun as a launcher backend) you simply need to install the mpi4py python package. DeepSpeed will use this to discover the MPI environment and pass the necessary state (e.g., world size, rank) to the torch distributed backend.

If you are using model parallelism, pipeline parallelism, or otherwise require torch.distributed calls before calling deepspeed.initialize(..) we provide the same MPI support with an additional DeepSpeed API call. Replace your initial torch.distributed.init_process_group(..) call with:

In the case that we are only running on a single node (with one or more GPUs) DeepSpeed does not require a hostfile as described above. If a hostfile is not detected or passed in then DeepSpeed will query the number of GPUs on the local machine to discover the number of slots available. The --include and --exclude arguments work as normal, but the user should specify ‘localhost’ as the hostname.

Also note that CUDA_VISIBLE_DEVICES can be used with deepspeed to control which devices should be used on a single node. So either of these would work to launch just on devices 0 and 1 of the current node:

Updated: November 5, 2025

**Examples:**

Example 1 (unknown):
```unknown
model_engine, optimizer, _, _ = deepspeed.initialize(args=cmd_args,
                                                     model=model,
                                                     model_parameters=params)
```

Example 2 (unknown):
```unknown
torch.distributed.init_process_group(...)
```

Example 3 (unknown):
```unknown
deepspeed.init_distributed()
```

Example 4 (unknown):
```unknown
for step, batch in enumerate(data_loader):
    #forward() method
    loss = model_engine(batch)

    #runs backpropagation
    model_engine.backward(loss)

    #weight update
    model_engine.step()
```

---

## BERT Pre-training

**URL:** https://www.deepspeed.ai/tutorials/bert-pretraining/

**Contents:**
- BERT Pre-training
    - Contents
- Pre-training Bing BERT without DeepSpeed
  - Training Data Setup
  - Running the Bing BERT model
- Enabling DeepSpeed
  - Argument Parsing
  - Initialization and Training
    - Initialization
    - Training

Note: On 08/15/2022 we have added another BERT pre-training/fine-tuning example at github.com/deepspeedai/Megatron-DeepSpeed/tree/main/examples_deepspeed/bert_with_pile, which includes a README.md that describes how to use it. Compared to the example described below, the new example in Megatron-DeepSpeed adds supports of ZeRO and tensor-slicing model parallelism (thus support larger model scale), uses a public and richer Pile dataset (user can also use their own data), together with some changes to the model architecture and training hyperparameters as described in this paper. As a result, the BERT models trained by the new example is able to provide better MNLI results than original BERT, but with a slightly different model architecture and larger computation requirements. If you want to train a larger-scale or better quality BERT-style model, we recommend to follow the new example in Megatron-DeepSpeed. If your goal is to strictly reproduce the original BERT model, we recommend to follow the example under DeepSpeedExamples/bing_bert as described below. On the other hand, the tutorial below helps explaining how to integrate DeepSpeed into a pre-training codebase, regardless of which BERT example you use.

In this tutorial we will apply DeepSpeed to pre-train the BERT (Bidirectional Encoder Representations from Transformers), which is widely used for many Natural Language Processing (NLP) tasks. The details of BERT can be found here: BERT: Pre-training of Deep Bidirectional Transformers for Language Understanding.

We will go through how to setup the data pipeline and how to run the original BERT model. Then we will show step-by-step how to modify the model to leverage DeepSpeed. Finally, we demonstrate the performance evaluation and memory usage reduction from using DeepSpeed.

We work from adaptations of huggingface/transformers and NVIDIA/DeepLearningExamples. We have forked this repo under DeepSpeedExamples/bing_bert and made several modifications in their script:

Note: Downloading and pre-processing instructions are coming soon.

Download the Wikipedia and BookCorpus datasets and specify their paths in the model config file DeepSpeedExamples/bing_bert/bert_large_adam_seq128.json:

From DeepSpeedExamples/bing_bert, run:

To use DeepSpeed we need to edit two files :

We first need to add DeepSpeed’s argument parsing to train.py using deepspeed.add_config_arguments(). This step allows the application to recognize DeepSpeed specific configurations.

We modify the train.py to enable training with DeepSpeed.

We use deepspeed.initialize() to create the model, optimizer, and learning rate scheduler. For the Bing BERT model, we initialize DeepSpeed in its prepare_model_optimizer() function as below, to pass the raw model and optimizer (specified from the command option).

Note that for Bing BERT, the raw model is kept in model.network, so we pass model.network as a parameter instead of just model.

The model returned by deepspeed.initialize is the DeepSpeed model engine that we will use to train the model using the forward, backward and step API. Since the model engine exposes the same forward pass API as nn.Module objects, there is no change in the forward pass. Thus, we only modify the backward pass and optimizer/scheduler steps.

Backward propagation is performed by calling backward(loss) directly with the model engine.

The step() function in DeepSpeed engine updates the model parameters as well as the learning rate. Zeroing the gradients is handled automatically by DeepSpeed after the weights have been updated after each step.

DeepSpeed’s model engine has flexible APIs for checkpoint saving and loading in order to handle the both the client model state and its own internal state.

In train.py, we use DeepSpeed’s checkpointing API in the checkpoint_model() function as below, where we collect the client model states and pass them to the model engine by calling save_checkpoint():

In the load_training_checkpoint() function, we use DeepSpeed’s loading checkpoint API and return the states for the client model:

The last step to use DeepSpeed is to create a configuration JSON file (e.g., deepspeed_bsz4096_adam_config.json). This file provides DeepSpeed specific parameters defined by the user, e.g., batch size per GPU, optimizer and its parameters, and whether enabling training with FP16.

In particular, this sample json is specifying the following configuration parameters to DeepSpeed:

That’s it! That’s all you need do in order to use DeepSpeed in terms of modifications. We have included a modified train.py file called DeepSpeedExamples/bing_bert/deepspeed_train.py with all of the changes applied.

To enable the transformer kernel for higher performance, first add an argument --deepspeed_transformer_kernel in utils.py, we can set it as False by default, for easily turning on/off.

Then in the BertEncoder class of the modeling source file, instantiate transformer layers using DeepSpeed transformer kernel as below.

All configuration settings come from the DeepSpeed configuration file and command arguments and thus we must pass the args variable to here in this model.

For more details about the transformer kernel, please see DeepSpeed Transformer Kernel and DeepSpeed Fast-Bert Training.

An example of launching deepspeed_train.py on four nodes with four GPUs each would be:

See the Getting Started guide for more information on launching DeepSpeed.

We achieve the fastest BERT training time while remaining competitive across the industry in terms of achieving F1 score of 90.5 or better on the SQUAD 1.1 dev set. Please follow the BERT fine-tuning tutorial to fine-tune your model that was pre-trained by transformer kernel and reproduce the SQUAD F1 score.

Our configuration for the BERT training result above can be reproduced with the scripts/json configs in our DeepSpeedExamples repo. Below is a table containing a summary of the configurations. Specifically see the ds_train_bert_bsz64k_seq128.sh and ds_train_bert_bsz32k_seq512.sh scripts for more details in DeepSpeedExamples.

Compared to SOTA, DeepSpeed significantly improves single GPU performance for transformer-based model like BERT. Figure above shows the single GPU throughput of training BertBERT-Large optimized through DeepSpeed, compared with two well-known Pytorch implementations, NVIDIA BERT and HuggingFace BERT. DeepSpeed reaches as high as 64 and 53 teraflops throughputs (corresponding to 272 and 52 samples/second) for sequence lengths of 128 and 512, respectively, exhibiting up to 28% throughput improvements over NVIDIA BERT and up to 62% over HuggingFace BERT. We also support up to 1.8x larger batch size without running out of memory.

For more details on how we achieve the record breaking BERT training time please check out deep dive into DeepSpeed BERT Fastest BERT Training

Updated: November 5, 2025

**Examples:**

Example 1 (unknown):
```unknown
{
  ...
  "datasets": {
      "wiki_pretrain_dataset": "/data/bert/bnorick_format/128/wiki_pretrain",
      "bc_pretrain_dataset": "/data/bert/bnorick_format/128/bookcorpus_pretrain"
  },
  ...
}
```

Example 2 (unknown):
```unknown
python train.py  \
    --cf bert_large_adam_seq128.json \
    --train_batch_size 64 \
    --max_seq_length 128 \
    --gradient_accumulation_steps 1  \
    --max_grad_norm 1.0 \
    --fp16 \
    --loss_scale 0 \
    --delay_allreduce \
    --max_steps 10 \
    --output_dir <path-to-model-output>
```

Example 3 (python):
```python
def get_arguments():
    parser = get_argument_parser()
    # Include DeepSpeed configuration arguments
    parser = deepspeed.add_config_arguments(parser)

    args = parser.parse_args()

    return args
```

Example 4 (python):
```python
def prepare_model_optimizer(args):
    # Loading Model
    model = BertMultiTask(args)

    # Optimizer parameters
    optimizer_parameters = prepare_optimizer_parameters(args, model)
    model.network, optimizer, _, _ = deepspeed.initialize(args=args,
                                         model=model.network,
                                         model_parameters=optimizer_parameters,
                                         dist_init_required=False)
    return model, optimizer
```

---

## Megatron-LM GPT2

**URL:** https://www.deepspeed.ai/tutorials/megatron

**Contents:**
- Megatron-LM GPT2
    - Contents
- Training GPT-2 with the Original Megatron-LM
  - Training Data Setup
  - Running Unmodified Megatron-LM GPT2 model
- Enabling DeepSpeed
  - Argument Parsing
  - Initialization and Training
    - Initialization
    - Using the Training API

If you haven’t already, we advise you to first read through the Getting Started guide before stepping through this tutorial.

In this tutorial we will be adding DeepSpeed to Megatron-LM GPT2 model, which is a large, powerful transformer. Megatron-LM supports model-parallel and multi-node training. Please see the corresponding paper for more details: Megatron-LM: Training Multi-Billion Parameter Language Models Using Model Parallelism.

First, we discuss data and environment setup and how to train the GPT-2 model with the original Megatron-LM. Next, we proceed step-by-step in enabling this model to run with DeepSpeed. Finally, we demonstrate the performance gains, and memory footprint reduction from using DeepSpeed.

We’ve copied the original model code from Megatron-LM into DeepSpeed Megatron-LM and made it available as a submodule. To download, execute:

To use DeepSpeed we will modify three files :

The first step is adding DeepSpeed arguments to Megatron-LM GPT2 model, using deepspeed.add_config_arguments() in arguments.py.

We will modify pretrain.py to enable training with DeepSpeed.

We use deepspeed.initialize to create model_engine, optimizer and LR scheduler. Below is its definition:

For the Megatron-LM GPT2 model, we initialize DeepSpeed in its setup_model_and_optimizer() function as below, to pass the raw model, optimizer, args, lr_scheduler and mpu.

Note that when FP16 is enabled, Megatron-LM GPT2 adds a wrapper to the Adam optimizer. DeepSpeed has its own FP16 Optimizer, so we need to pass the Adam optimizer to DeepSpeed directly without any wrapper. We return the unwrapped Adam optimizer from get_optimizer() when DeepSpeed is enabled.

The model returned by deepspeed.initialize is the DeepSpeed Model Engine that we will use to train the model using the forward, backward and step API.

The forward propagation API is compatible to PyTorch and no change is required.

Backward propagation is done by calling backward(loss) directly on the model engine.

Zeroing the gradients is handled automatically by DeepSpeed after the weights have been updated using a mini-batch.

Furthermore, DeepSpeed addresses distributed data parallel and FP16 under the hood, simplifying code in multiple places.

(A) DeepSpeed also performs gradient averaging automatically at the gradient accumulation boundaries. So we skip the allreduce communication.

(B) We also skip updating master gradients, since DeepSpeed addresses it internally.

The step() function in DeepSpeed engine updates the model parameters as well as the learning rate.

The GPT2 training script logs the loss scaling value during training. Inside the DeepSpeed optimizer, this value is stored as cur_scale instead of loss_scale as in Megatron’s optimizer. Therefore, we appropriately replace it in the logging string.

The DeepSpeed engine has flexible APIs for checkpoint saving and loading, to handle the states from both the client model and its own internal.

To use DeepSpeed, we need to update utils.py in which Megatron-LM GPT2 saves and loads checkpoints.

Create a new function save_ds_checkpoint() as shown below. The new function collects the client model states and passes them to the DeepSpeed engine by calling DeepSpeed’s save_checkpoint().

In Megatron-LM GPT2’s save_checkpoint() function, add the following lines to invoke the above function for DeepSpeed.

In the load_checkpoint() function, use DeepSpeed checkpoint loading API as below, and return the states for the client model.

DeepSpeed can reduce the activation memory during model parallel training by partitioning activation checkpoints across model parallel GPUs, or offloading them to CPU. These optimizations are optional, and can be skipped unless activation memory becomes a bottleneck. To enable partition activation, we use the deepspeed.checkpointing API to replace Megatron’s activation checkpointing and random state tracker APIs. The replacement should happen before the first invocation of these APIs.

a) Replace in pretrain_gpt.py :

b) Replace in mpu/transformer.py:

With these replacements, various DeepSpeed activation checkpointing optimizations such as activation partitioning, contiguous checkpointing, and CPU checkpointing, can be specified either with deepspeed.checkpointing.configure or in the deepspeed_config file.

We assume that the webtext data was prepared in the previous step. To start training Megatron-LM GPT2 model with DeepSpeed applied, execute the following command to start training.

DeepSpeed enables training very large models effectively via the advanced ZeRO optimizer. In February 2020, we released a sub-set of optimizations from ZeRO in DeepSpeed that perform optimizer state partitioning. We refer to them as ZeRO-1. In May 2020, we extended ZeRO-1 in DeepSpeed to include additional optimizations from ZeRO including gradient and activation partitioning, as well as contiguous memory optimizations. We refer to this release as ZeRO-2.

ZeRO-2 significantly reduces the memory footprint for training large models which means large models can be trained with i) less model parallelism and ii) larger batch sizes. A lower model parallelism degree improves training efficiency by increasing the granularity of computations such as matrix multiplications where performance is directly related to the size of the matrices. Furthermore, less model parallelism also results in less communication between model parallel GPUs, which further boosts performance. Larger batch size has a similar effect of increasing the computational granularity as well as reducing communication, also resulting in better performance. Therefore, with DeepSpeed and ZeRO-2 integration into Megatron, we elevate the model scale and speed to an entirely new level compared to Megatron alone.

Figure 2: ZeRO-2 scales to 170 billion parameters, has up to 10x higher throughput, obtains super linear speedup, and improves usability by avoiding the need for code refactoring for models up to 13 billion parameters.

More concretely, DeepSpeed and ZeRO-2 excel in four aspects (as visualized in Figure 2), supporting an order-of-magnitude bigger models, up to 10x faster, with superlinear scalability, and improved usability to democratize large model training. These four aspects are detailed below.

Model size: State-of-the-art large models such as OpenAI GPT-2, NVIDIA Megatron-LM, Google T5, and Microsoft Turing-NLG have sizes of 1.5B, 8.3B, 11B, and 17B parameters respectively. ZeRO-2 provides system support to efficiently run models of 170 billion parameters, an order-of-magnitude bigger than these largest models (Figure 2, top left).

Speed: Improved memory efficiency powers higher throughput and faster training. Figure 2 (bottom left) shows system throughput of ZeRO-2 and ZeRO-1 (both combining ZeRO-powered data parallelism with NVIDIA Megatron-LM model parallelism) as well as using the state-of-the-art model parallelism approach Megatron-LM alone (baseline in Figure 2, bottom left). ZeRO-2 runs 100-billion-parameter models on a 400 NVIDIA V100 GPU cluster with over 38 teraflops per GPU and aggregated performance over 15 petaflops. For models of the same size, ZeRO-2 is 10x faster in training speed when compared with using Megatron-LM alone and 5x faster when compared with ZeRO-1.

Scalability: We observe superlinear speedup (Figure 2, top right), where the performance more than doubles when the number of GPUs are doubled. ZeRO-2 reduces the memory footprint of the model states as we increase the data parallelism degree, allowing us to fit larger batch sizes per GPU and resulting in better performance.

Democratizing large model training: ZeRO-2 empowers model scientists to train models up to 13 billion parameters efficiently without any model parallelism that typically requires model refactoring (Figure 2, bottom right). 13 billion parameters is larger than most of the largest state-of-the-art models (such as Google T5, with 11 billion parameters). Model scientists can therefore experiment freely with large models without worrying about model parallelism. In comparison, the implementations of classic data-parallelism approaches (such as PyTorch Distributed Data Parallel) run out of memory with 1.4-billion-parameter models, while ZeRO-1 supports up to 6 billion parameters for comparison.

Furthermore, in the absence of model parallelism, these models can be trained on low bandwidth clusters while still achieving significantly better throughput compared to using model parallelism. For example, the GPT-2 model can be trained nearly 4x faster with ZeRO powered data parallelism compared to using model parallelism on a four node cluster connected with 40 Gbps Infiniband interconnect, where each node has four NVIDIA 16GB V100 GPUs connected with PCI-E. Therefore, with this performance improvement, large model training is no longer limited to GPU clusters with ultra fast interconnect, but also accessible on modest clusters with limited bandwidth.

Updated: November 5, 2025

**Examples:**

Example 1 (unknown):
```unknown
git submodule update --init --recursive
```

Example 2 (python):
```python
def get_args():
    """Parse all the args."""

    parser = argparse.ArgumentParser(description='PyTorch BERT Model')
    parser = add_model_config_args(parser)
    parser = add_fp16_config_args(parser)
    parser = add_training_args(parser)
    parser = add_evaluation_args(parser)
    parser = add_text_generate_args(parser)
    parser = add_data_args(parser)

    # Include DeepSpeed configuration arguments
    parser = deepspeed.add_config_arguments(parser)
```

Example 3 (python):
```python
def initialize(args,
               model,
               optimizer=None,
               model_parameters=None,
               training_data=None,
               lr_scheduler=None,
               mpu=None,
               dist_init_required=True,
               collate_fn=None):
```

Example 4 (python):
```python
def setup_model_and_optimizer(args):
    """Setup model and optimizer."""

    model = get_model(args)
    optimizer = get_optimizer(model, args)
    lr_scheduler = get_learning_rate_scheduler(optimizer, args)

    if args.deepspeed:
        import deepspeed

        print_rank_0("DeepSpeed is enabled.")

        model, optimizer, _, lr_scheduler = deepspeed.initialize(
            model=model,
            optimizer=optimizer,
            args=args,
            lr_scheduler=lr_scheduler,
            mpu=mpu,
            dist_init_required=False
       )
```

---

## 1-bit LAMB: Communication Efficient Large-Scale Large-Batch Training with LAMB’s Convergence Speed

**URL:** https://www.deepspeed.ai/tutorials/onebit-lamb/

**Contents:**
- 1-bit LAMB: Communication Efficient Large-Scale Large-Batch Training with LAMB’s Convergence Speed
    - Contents
- 1. Overview
  - 1.1 Pre-requisites for installing DeepSpeed
  - 1.2 Pre-requisites for 1-bit LAMB
    - 1.2.1 NCCL-based implementation
    - 1.2.2 MPI-based implementation
    - 1.2.3 Compressed implementation
  - 1.3 1-bit LAMB Algorithm
  - 1.4 Configuration of 1-bit LAMB

Watch out! 1) The NCCL-based implementation requires PyTorch >= 1.8 (and NCCL >= 2.8.3 when you have 64 or more GPUs). See details below. 2) Although 1-bit LAMB is compatible with both FP16 and FP32, currently we only verified the convergence under mixed precision/FP16 training. 3) Currently the MPI-based implementation is not compatible with pipeline parallelism. 4) Frequent checkpoint loading could hurt 1-bit LAMB’s convergence. See details below.

In this tutorial, we introduce DeepSpeed’s 1-bit LAMB optimizer which enables communication-efficient large-scale large-batch training with LAMB’s convergence speed. 1-bit LAMB can improve model training speed on communication-constrained clusters, especially for communication-intensive large models by reducing the overall communication volume by up to 4.6x. We also have a paper which provides the technical details including algorithm, system implementation, and evaluations.

To illustrate the benefits and usage of 1-bit LAMB optimizer, we use the BERT Pre-training task as example. For more details on this task, please refer to the tutorial.

If you don’t already have a copy of the DeepSpeed repository, please clone it now and checkout the DeepSpeedExamples submodule that contains the BERT Pre-training example.

In DeepSpeed, we introduce a system implementation for compressed communication using the NCCL backend of PyTorch distributed. This implementation provides better performance and usability than the MPI-based implementation below. Thus we highly recommend users to choose this implementation.

Watch out! This NCCL-based implementation requires PyTorch >= 1.8. It also requires NCCL >= 2.8.3 when you have 64 or more GPUs to avoid certain NCCL runtime bugs. Currently (2021/03/16) NCCL 2.8.3 is not officially supported by PyTorch. The solution we used is by hacking in NCCL 2.8.3 via LD_PRELOAD: 1) Install NCCL 2.8.3. This works for us on a CUDA 11 system: apt-get install -y libnccl2=2.8.3-1+cuda11.0 libnccl-dev=2.8.3-1+cuda11.0. 2) Set LD_PRELOAD to the library path. This works for us: LD_PRELOAD=/usr/lib/x86_64-linux-gnu/libnccl.so.2.8.3. To confirm LD_PRELOAD is working you can see the version it uses in the NCCL logs if you have NCCL_DEBUG=INFO, it should say: NCCL version 2.8.3+cuda11.0.

For this implementation, we rely on Message Passing Interface (MPI) for advanced communication primitives.

We package the necessary dependencies in the DeepSpeed docker images. However, if you are using a different build system, please install MPI and mpi4py on your system. To install the prerequisites run:

We have tested CUDA-Aware MPI communication using the MVAPICH2-GDR library. However, any CUDA-Aware communication library including OpenMPI should work fine with these examples.

An example launch command for 1-bit LAMB using the deepspeed launcher is as follows:

Please note that for MPI-based implementation of 1-bit LAMB, the --launcher=[mvapich|openmpi] flag is required when using the deepspeed launcher.

Alternatively, the standard mpirun launcher can also be used as follows:

This backend provides an approach to abstract the generic part of one-bit optimizers and implements accelerator dependent part with DeepSpeed custom op builder. To use this CompressedBackend, you should make sure that your current accelerator supports PackbitsBuilder, so that it could be loaded to do high performance packing and unpacking between float and Byte datatype, which is utilized in one-bit algorithm. An example can be found in Deepspeed/op_builder/xpu/packbits.py. This approach does not require NCCL or MPI based communication library. It will automatically use your default communication library selected by your accelerator in deepspeed/comm.

The detailed description of the 1-bit LAMB algorithm can be seen from our paper.

The 1-bit LAMB feature can be used by setting the optimizer configuration options as follows. An example json config file is shown below.

Please note the new parameters freeze_step, cuda_aware, comm_backend_name, coeff_beta, factor_max, factor_min, and factor_threshold that have been added to support the 1-bit LAMB feature:

freeze_step is the number of warm up steps before 1-bit compression gets applied to the communication. In order to determine the number of warm up steps, one strategy is to set 15-25% of the total training steps for a given model (This is related to LAMB’s variance/second moment term and scaling coefficient. See detailed analysis in our paper). If it provides the desired outcome, one can try to extract more performance by reducing the steps systematically. In future, we plan to introduce a threshold that can automatically search and decide for the number of warm up steps for different models. The examples below have been tuned for the number of warm up steps. The freeze_step parameter has already been set to the best number we found in the corresponding run scripts.

cuda_aware is used for MPI-based implementation to indicate that the underlying MPI library supports CUDA-Aware communication. This feature is only supported on systems with InfiniBand interconnect and a CUDA-Aware MPI library like MVAPICH2-GDR or OpenMPI built with CUDA-Aware support. Setting cuda_aware to False will allow training on Ethernet based systems. However, the communication will happen using sender as well as receiver side memory copies between CPU and GPU buffers before and after communication.

comm_backend_name is used to indicate which backend implementation to use. You can choose between NCCL, MPI-based and compressed implementations by setting comm_backend_name to “nccl”, “mpi” or “compressed”. When using NCCL-based implementation, there is no need to set cuda_aware.

coeff_beta is used when calculating a moving average of the LAMB scaling coefficient during the warmup stage. This moving average is then used as the frozen base scaling coefficient during the compression stage.

factor_max, factor_min, and factor_threshold are used to regularize the adaptive scaling of the frozen base scaling coefficient during the compression stage. factor_max and factor_min are the scaling factor upper/lower bound. factor_threshold defines the threshold of how much the scaling factor can fluctuate between steps.

Because 1-bit compression cannot represent exact zero, the compression error would keep accumulating in the momentum if a parameter have constant zero gradients during training. For example, for BERT pre-training seq length 128, bert.embeddings.position_embeddings.weight has constant zeros in its gradient and momentum for row 129 to 512, because it only learns up to seq length 128 while the model supports up to seq length 512. Thus in 1-bit LAMB we added support of a momentum mask for users to specify those params that have constant exact zeros in their gradients. See example script for how to configure this momentum mask. One thing to note is that we don’t use momentum mask saved in checkpoints since this mask could change during training (e.g., BERT seqlen 128 and 512 require different masks). So you have to provide this mask every time in your training script.

Watch out! 1-bit LAMB relies on an compression error compensation mechanism to maintain the convergence speed at compression stage. When loading checkpoints, we actually reset the compression errors for 3 reasons: 1) The worker and server error at each GPU are distinct, so in current implementation only rank 0’s errors are saved in the checkpoint. Thus we have to reset the errors. If we want to save them correctly we need O(num_gpu*model_size) memory in order to gather all the error, which is a very large memory requirement. It’s possible to save them in a distributed way, but it will make the checkpoint saving/loading much more complicated. 2) Even if we are able to save the compression errors correctly, you need to have the exact same number of GPUs in order to load them correctly. 3) We verified on BERT pre-training that occasionally resetting the compression error at checkpoint loading does not affect the convergence. However, please avoid frequent checkpoint loading which could break the error compensation mechanism thus affect the convergence.

For data downloading and pre-processing, please refer to the BERT Pre-training tutorial.

We provide example scripts under DeepSpeedExamples/bing_bert/1-bit_lamb/. There are 3 sets of scripts corresponding to NCCL-based implementation, MPI-based implementation on Ethernet systems, and MPI-based implementation on InfiniBand systems. For MPI-based implementation, we provide both example scripts when launching with deepspeed or mpirun.

The deepspeed_bsz64k_onebitlamb_config_seq128_*.json and deepspeed_bsz32k_onebitlamb_config_seq512_*.json files give the user the ability to specify DeepSpeed options in terms of batch size, micro batch size, optimizer, learning rate, and other parameters. In these files we include the tuned hyperparameters to reproduce experiments in our paper.

Performance results can be seen in our paper.

Updated: November 5, 2025

**Examples:**

Example 1 (unknown):
```unknown
git clone https://github.com/deepspeedai/DeepSpeed
cd DeepSpeed
git submodule update --init --recursive
cd DeepSpeedExamples/
```

Example 2 (unknown):
```unknown
pip install deepspeed[1bit_adam]
```

Example 3 (unknown):
```unknown
deepspeed --launcher=[mvapich|openmpi] script.py
```

Example 4 (unknown):
```unknown
mpirun -np [num processes] -ppn [num GPUs on each node] -hostfile [hostfile] [MPI flags] python [training_script.py]
```

---

## Automatic Tensor Parallelism for HuggingFace Models

**URL:** https://www.deepspeed.ai/tutorials/automatic-tensor-parallelism/

**Contents:**
- Automatic Tensor Parallelism for HuggingFace Models
    - Contents
- Contents
- Introduction
- Example Script
- Launching
- T5 11B Inference Performance Comparison
  - Latency
  - Throughput
  - Memory

This tutorial demonstrates the new automatic tensor parallelism feature for inference. Previously, the user needed to provide an injection policy to DeepSpeed to enable tensor parallelism. DeepSpeed now supports automatic tensor parallelism for HuggingFace models by default as long as kernel injection is not enabled and an injection policy is not provided. This allows our users to improve performance of models that are not currently supported via kernel injection, without providing the injection policy. Below is an example of the new method:

Previously, to run inference with only tensor parallelism for the models that don’t have kernel injection support, you could pass an injection policy that showed the two specific linear layers on a Transformer Encoder/Decoder layer: 1) the attention output GeMM and 2) layer output GeMM. We needed these parts of the layer to add the required all-reduce communication between GPUs to merge the partial results across model-parallel ranks. Below, we show an example of this previous method:

With automatic tensor parallelism, we do not need to provide the injection policy for supported models. The injection policy will be determined at runtime and applied automatically.

We can observe performance improvement with automatic tensor parallelism using the inference test suite. This script is for testing text-generation models and includes per token latency, bandwidth, throughput and memory checks for comparison. See the README for more information.

Use the following command to run without DeepSpeed and without tensor parallelism. Set the test_performance flag to collect performance data:

To enable tensor parallelism, you need to use the flag ds_inference for the compatible models:

The following results were collected using V100 SXM2 32GB GPUs.

The following results were collected using V100 SXM2 32GB GPUs.

The following model families have been successfully tested with automatic tensor parallelism. Other models may work but have not been tested yet.

The following models are not currently supported with automatic tensor parallelism. They may still be compatible with other DeepSpeed features (e.g., kernel injection for Bloom):

Updated: November 5, 2025

**Examples:**

Example 1 (unknown):
```unknown
# ---------------------------------------
# New automatic tensor parallelism method
# ---------------------------------------
import os
import torch
import transformers
import deepspeed
local_rank = int(os.getenv("LOCAL_RANK", "0"))
world_size = int(os.getenv("WORLD_SIZE", "1"))
# create the model pipeline
pipe = transformers.pipeline(task="text2text-generation", model="google/t5-v1_1-small", device=local_rank)
# Initialize the DeepSpeed-Inference engine
pipe.model = deepspeed.init_inference(
    pipe.model,
    mp_size=world_size,
    dtype=torch.float
)
output = pipe('Input String')
```

Example 2 (python):
```python
# ----------------------------------
# Previous tensor parallelism method
# ----------------------------------
import os
import torch
import transformers
import deepspeed
from transformers.models.t5.modeling_t5 import T5Block
local_rank = int(os.getenv("LOCAL_RANK", "0"))
world_size = int(os.getenv("WORLD_SIZE", "1"))
# create the model pipeline
pipe = transformers.pipeline(task="text2text-generation", model="google/t5-v1_1-small", device=local_rank)
# Initialize the DeepSpeed-Inference engine
pipe.model = deepspeed.init_inference(
    pipe.model,
    mp_size=world_size,
    dtype=torch.float,
    injection_policy={T5Block: ('SelfAttention.o', 'EncDecAttention.o', 'DenseReluDense.wo')}
)
output = pipe('Input String')
```

Example 3 (unknown):
```unknown
deepspeed --num_gpus <num_gpus> DeepSpeedExamples/inference/huggingface/text-generation/inference-test.py --name <model> --batch_size <batch_size> --test_performance
```

Example 4 (unknown):
```unknown
deepspeed --num_gpus <num_gpus> DeepSpeedExamples/inference/huggingface/text-generation/inference-test.py --name <model> --batch_size <batch_size> --test_performance --ds_inference
```

---

## Monitor

**URL:** https://www.deepspeed.ai/tutorials/monitor

**Contents:**
- Monitor
    - Contents
- Overview
- Usage
  - Automatic Monitoring
  - Custom Monitoring

In this tutorial, we introduce the DeepSpeed Monitor and provide examples of its usage.

Monitoring model and system metrics during training is vital to ensure hardware resources are fully utilized. The DeepSpeed Monitor enables live logging of metrics through one or more monitoring backends such as PyTorch’s TensorBoard, WandB, Comet and simple CSV files.

Below is a live monitoring view for TensorBoard:

Below is a live monitoring view for WandB:

Below is a live monitoring view for Comet:

The DeepSpeed Monitor is configured within the deepspeed configuration file. DeepSpeed will automatically monitor key training metrics, including those tracked with the wall_clock_breakdown configuration option. In addition, users can log their own custom events and metrics.

When using DeepSpeed for model training, the Monitor can be configured in the DeepSpeed configuration file. No explicit API calls are needed to use the Monitor. The Monitor can be enabled by adding the following field to DeepSpeed’s configuration json file. Refer to Monitoring for details.

DeepSpeed will automatically log to all available and enabled monitoring backends listed in the config, and will generate live monitoring views such as those listed above.

In addition to automatic monitoring, users can log their own custom metrics in client scripts. Currently, there are two ways to initialize Monitor objects:

The steps to create a custom monitor are as follows:

* Note - Some Monitor backends don’t support mixed sample values. Be sure to use your DeepSpeed engine object’s global_samples attribute in each 3-tuple

For example usage, see the following modified DeepSpeedExamples/cifar example:

Updated: November 5, 2025

**Examples:**

Example 1 (unknown):
```unknown
{
  "tensorboard": {
    "enabled": true,
    "output_path": "output/ds_logs/",
    "job_name": "train_bert"
  }
  "wandb": {
    "enabled": true,
    "team": "my_team",
    "group": "my_group",
    "project": "my_project"
  }
  "comet": {
    "enabled": true,
    "project": "my_project",
    "experiment_name": "my_experiment"
  }
  "csv_monitor": {
    "enabled": true,
    "output_path": "output/ds_logs/",
    "job_name": "train_bert"
  }
}
```

Example 2 (python):
```python
# Step 1: Import monitor (and DeepSpeed config, if needed)
from deepspeed.monitor.monitor import MonitorMaster
from deepspeed.runtime.config import DeepSpeedConfig

# Step 2: Initialized monitor with DeepSpeed config (get DeepSpeed config object, if needed)
ds_config = DeepSpeedConfig("ds_config.json")
monitor = MonitorMaster(ds_config.monitor_config)

for epoch in range(2):

    running_loss = 0.0
    for i, data in enumerate(trainloader):
        pre = time.time()
        inputs, labels = data[0].to(model_engine.local_rank), data[1].to(
            model_engine.local_rank)
        if fp16:
            inputs = inputs.half()
        outputs = model_engine(inputs)
        loss = criterion(outputs, labels)

        model_engine.backward(loss)
        model_engine.step()
        post = time.time()
        # Step 3: Create list of 3-tuple records (single entry in this case)
        events = [("Time per step", post-pre, model_engine.global_samples)]
        # Step 4: Call monitor.write_events on the list from step 3
        monitor.write_events(events)
```

---

## ZeRO++

**URL:** https://www.deepspeed.ai/tutorials/zeropp/

**Contents:**
- ZeRO++
    - Contents
- Three Components of ZeRO++
- Training Environment
- Training a 18B parameter GPT-2 with ZeRO++
  - DeepSpeed Configuration Changes

ZeRO++ is a system of communication optimization strategies built on top of ZeRO to offer unmatched efficiency for large model training regardless of the scale or cross-device bandwidth constraints. Read our ZeRO++ blog and paper to learn more!

We recommend that you read the tutorials on Getting Started, ZeRO and Megatron-DeepSpeed before stepping through this tutorial.

ZeRO++ consists of three key designs, namely quantized weights (qwZ), hiearchical partitioning ZeRO (hpZ), and quantized gradients (qgZ):

Collectively, the three optimization reduces communication volume by 4x compared to ZeRO baseline. Each of the three components can be enabled independent of each other and collectively as a group as described in the next section.

For this tutorial, we will configure a 18 billion parameter GPT-2 model using the DeepSpeed Megatron-DeepSpeed GPT-2 code. We will use 4 nodes of 16x NVIDIA Tesla V100-SXM3 Tensor Core GPU with 32GB RAM per node for this exercise.

There are no change needed to the user code. However, since ZeRO++ extends ZeRO Stage 3 (ZeRO-3), appropriate flags need to be added to activate each or all of the three ZeRO++ communication collective optimizations. The three flags and their meanings and defaults and preferred values:

An example snippet of deepspeed configurations with all three ZeRO++ optimization enable is shown below:

Finally, to launch your experiment, issue the following command:

See more details on Megatron-DeepSpeed tutorial examples on how to launch a Megatron-DeepSpeed job.

Here is a screenshots of the training log for both ZeRO baseline and ZeRO++:

Congratulations! You have completed the ZeRO++ tutorial.

Updated: November 5, 2025

**Examples:**

Example 1 (unknown):
```unknown
{
    "zero_optimization": {
        "stage": 3,
        "reduce_bucket_size": 10000000,
        "reduce_scatter": true,

        "zero_quantized_weights": true,
        "zero_hpz_partition_size": 16,
        "zero_quantized_gradients": true,

        "contiguous_gradients": true,
        "overlap_comm": true
    }
}
```

Example 2 (unknown):
```unknown
deepspeed pretrain_zeropp_gpt.py \
       --tensor-model-parallel-size 1 \
       --pipeline-model-parallel-size 1 \
       --num-layers 40 \
       --hidden-size 6144 \
       --seq-length 512 \
       --num-attention-heads 32 \
       --batch-size 1 \
       --zero-stage 3 \
       --deepspeed_config ds_zeropp_config.json \
       --deepspeed-activation-checkpointing \
       --fp16 \
       --checkpoint-activations
```

---

## 1-bit Adam: Up to 5x less communication volume and up to 3.4x faster training

**URL:** https://www.deepspeed.ai/tutorials/onebit-adam

**Contents:**
- 1-bit Adam: Up to 5x less communication volume and up to 3.4x faster training
- 1. Overview
  - 1.1 Pre-requisites for installing DeepSpeed
  - 1.2 Pre-requisites for 1-bit Adam
    - 1.2.1 (New in v2) NCCL-based implementation
    - 1.2.2 MPI-based implementation
    - 1.2.3 Compressed implementation
  - 1.3 1-bit Algorithm
  - 1.4 Configuration of 1-bit Adam
    - 1.4.1 (New in v2) Momentum masks for parameters with constant zero gradients

Note: On 03/07/2022 we released 0/1 Adam, which is a new communication-efficient Adam optimizer partially following the 1-bit Adam’s design. Compared to the 1-bit Adam described below, 0/1 Adam provides better communication efficiency and the same final model quality on different tasks including BERT, GPT-2, and ImageNet. Thus we would recommend to first try 0/1 Adam (tutorial), and then try 1-bit Adam if 0/1 Adam couldn’t provide baseline Adam’s convergence in your task.

Note: This tutorial is updated on 03/04/2021 to reflect the 1-bit Adam v2. Changes include: 1) NCCL-based implementation which provides better performance and usability compared to the MPI-based implementation. 2) Add support to momentum masks for those parameters with constant zero gradients during training. 3) Bug fixes. See details below.

Watch out! 1) The NCCL-based implementation requires PyTorch >= 1.8 (and NCCL >= 2.8.3 when you have 64 or more GPUs). See details below. 2) Although 1-bit Adam is compatible with both FP16 and FP32, currently we only verified the convergence under mixed precision/FP16 training. 3) Currently the MPI-based implementation is not compatible with pipeline parallelism. 4) Frequent checkpoint loading could hurt 1-bit Adam’s convergence. See details below.

In this tutorial, we are going to introduce the 1-bit Adam optimizer in DeepSpeed. 1-bit Adam can improve model training speed on communication-constrained clusters, especially for communication-intensive large models by reducing the overall communication volume by up to 5x. Detailed description of the 1-bit Adam algorithm, its implementation in DeepSpeed, and performance evaluation is available from our blog post. We also have a paper which provides the most complete details including algorithm, system implementation, theoretical analysis, and more evaluations.

To illustrate the benefits and usage of 1-bit Adam optimizer in DeepSpeed, we use the following two training tasks as examples:

For more details on these tasks, please refer to the tutorial posts on BingBertSQuAD Fine-tuning and BERT Pre-training.

If you don’t already have a copy of the DeepSpeed repository, please clone it now and checkout the DeepSpeedExamples submodule that contains the BingBertSQuAD and BERT Pre-training examples.

In 1-bit Adam v2, we introduce a new system implementation for compressed communication using the NCCL backend of PyTorch distributed. This significantly improves the usability due to NCCL’s integration with PyTorch distributed. The performance of our new NCCL-based implementation is also better than our earlier MPI-based implementation for Ethernet-based systems and on-par for InfiniBand-based systems. Thus we highly recommend users to choose this implementation.

Watch out! This NCCL-based implementation requires PyTorch >= 1.8. It also requires NCCL >= 2.8.3 when you have 64 or more GPUs to avoid certain NCCL runtime bugs. Currently (2021/03/16) NCCL 2.8.3 is not officially supported by PyTorch. The solution we used is by hacking in NCCL 2.8.3 via LD_PRELOAD: 1) Install NCCL 2.8.3. This works for us on a CUDA 11 system: apt-get install -y libnccl2=2.8.3-1+cuda11.0 libnccl-dev=2.8.3-1+cuda11.0. 2) Set LD_PRELOAD to the library path. This works for us: LD_PRELOAD=/usr/lib/x86_64-linux-gnu/libnccl.so.2.8.3. To confirm LD_PRELOAD is working you can see the version it uses in the NCCL logs if you have NCCL_DEBUG=INFO, it should say: NCCL version 2.8.3+cuda11.0.

For this implementation, we rely on Message Passing Interface (MPI) for advanced communication primitives.

We package the necessary dependencies in the DeepSpeed docker images. However, if you are using a different build system, please install MPI and mpi4py on your system. To install the prerequisites run:

We have tested CUDA-Aware MPI communication using the MVAPICH2-GDR library. However, any CUDA-Aware communication library including OpenMPI should work fine with these examples.

An example launch command for 1-bit Adam using the deepspeed launcher is as follows:

Please note that for MPI-based implementation of 1-bit Adam, the --launcher=[mvapich|openmpi] flag is required when using the deepspeed launcher.

Alternatively, the standard mpirun launcher can also be used as follows:

This backend provides an approach to abstract the generic part of one-bit optimizers and implements accelerator dependent part with DeepSpeed custom op builder. To use this CompressedBackend, you should make sure that your current accelerator supports PackbitsBuilder, so that it could be loaded to do high performance packing and unpacking between float and Byte datatype, which is utilized in one-bit algorithm. An example can be found in Deepspeed/op_builder/xpu/packbits.py.

This approach does not require NCCL or MPI based communication library. It will automatically use your default communication library selected by your accelerator in deepspeed/comm.

The detailed description of the 1-bit Algorithm can be seen from our blog post and our paper.

The 1-bit Adam feature can be used by setting the optimizer configuration options as follows. An example json config file is shown below.

Please note three new parameters freeze_step, cuda_aware, and comm_backend_name that have been added to support the 1-bit Adam feature.

freeze_step is the number of warm up steps before 1-bit compression gets applied to the communication. In order to determine the number of warm up steps, one strategy is to set 15-25% of the total training steps for a given model (This is related to Adam’s variance/second moment term. See detailed analysis in our paper). If it provides the desired outcome, one can try to extract more performance by reducing the steps systematically. In future, we plan to introduce a threshold that can automatically search and decide for the number of warm up steps for different models. The examples below have been tuned for the number of warm up steps. The freeze_step parameter has already been set to the best number we found in the corresponding run scripts.

cuda_aware is used for MPI-based implementation to indicate that the underlying MPI library supports CUDA-Aware communication. This feature is only supported on systems with InfiniBand interconnect and a CUDA-Aware MPI library like MVAPICH2-GDR or OpenMPI built with CUDA-Aware support. Setting cuda_aware to False will allow training on Ethernet based systems. However, the communication will happen using sender as well as receiver side memory copies between CPU and GPU buffers before and after communication.

(New in v2) comm_backend_name is used to indicate which backend implementation to use. You can choose between NCCL, MPI-based and compressed implementations by setting comm_backend_name to “nccl”, “mpi” or “compressed”. When using NCCL-based implementation, there is no need to set cuda_aware.

Because 1-bit compression cannot represent exact zero, the compression error would keep accumulating in the momentum if a parameter have constant zero gradients during training. For example, for BERT pre-training seq length 128, bert.embeddings.position_embeddings.weight has constant zeros in its gradient and momentum for row 129 to 512, because it only learns up to seq length 128 while the model supports up to seq length 512. Thus in 1-bit Adam v2 we added support of a momentum mask for users to specify those params that have constant exact zeros in their gradients. See example script for how to configure this momentum mask. One thing to note is that we don’t use momentum mask saved in checkpoints since this mask could change during training (e.g., BERT seqlen 128 and 512 require different masks). So you have to provide this mask every time in your training script.

Watch out! 1-bit Adam relies on an compression error compensation mechanism to maintain the convergence speed at compression stage. When loading checkpoints, we actually reset the compression errors for 3 reasons: 1) The worker and server error at each GPU are distinct, so in current implementation only rank 0’s errors are saved in the checkpoint. Thus we have to reset the errors. If we want to save them correctly we need O(num_gpu*model_size) memory in order to gather all the error, which is a very large memory requirement. It’s possible to save them in a distributed way, but it will make the checkpoint saving/loading much more complicated. 2) Even if we are able to save the compression errors correctly, you need to have the exact same number of GPUs in order to load them correctly. 3) We verified on BERT pre-training that occasionally resetting the compression error at checkpoint loading does not affect the convergence. However, please avoid frequent checkpoint loading which could break the error compensation mechanism thus affect the convergence.

You can also use a pre-trained BERT model checkpoint from either DeepSpeed, HuggingFace, or TensorFlow to run the fine-tuning.

Note: For details about loading checkpoint, argument parsing, initialization, forward pass, backward pass, weight update and evaluation, please refer to the BingBertSQuAD Fine-tuning tutorial.

We provide example scripts under DeepSpeedExamples/training/BingBertSquad/1-bit_adam/. There are 3 sets of scripts corresponding to NCCL-based implementation, MPI-based implementation on Ethernet systems, and MPI-based implementation on InfiniBand systems. For MPI-based implementation, we provide both example scripts when launching with deepspeed or mpirun.

The deepspeed_onebitadam_bsz96_config.json file gives the user the ability to specify DeepSpeed options in terms of batch size, micro batch size, optimizer, learning rate, and other parameters. When running the nvidia_run_squad_deepspeed.py, in addition to the --deepspeed flag to enable DeepSpeed, the appropriate DeepSpeed configuration file must be specified using --deepspeed_config deepspeed_onebitadam_bsz96_config.json.

Table 1 shows the fine-tuning configuration we used in our experiments.

Table 1. Fine-tuning configuration

Accuracy: The results are summarized in the table below. The total batch size is set to 96 and training is conducted on 32 GPUs for 2 epochs. A set of parameters (seeds and learning rates) were tried and the best ones were selected. We fixed the learning rate to 3e-5. The table below shows the F1 and the EM scores we achieved that are on-par or better than the HuggingFace results.

Training Speed and Scalability:

Performance results of SQuAD Fine-tuning can be seen from our blog post and our paper.

For data downloading and pre-processing, please refer to the BERT Pre-training tutorial.

We provide example scripts under DeepSpeedExamples/bing_bert/1-bit_adam/. There are 3 sets of scripts corresponding to NCCL-based implementation, MPI-based implementation on Ethernet systems, and MPI-based implementation on InfiniBand systems. For MPI-based implementation, we provide both example scripts when launching with deepspeed or mpirun.

The deepspeed_bsz4k_onebit_config_seq128_*.json file gives the user the ability to specify DeepSpeed options in terms of batch size, micro batch size, optimizer, learning rate, and other parameters.

Below is the DeepSpeed configuration file for running BERT-large pre-training with sequence length of 128 using the 1-bit Adam optimizer.

The above file is for BERT-large. For BERT-base training (sequence length 128), the suggested freeze_step is 16000. For sequence 512 pre-training, we suggest to use a freeze_step of 1500 for both BERT-base and BERT-large. And make sure to set the comm_backend_name and cuda_aware correctly as described above.

Performance results of BERT Pre-training can be seen from our blog post and our paper.

Updated: November 5, 2025

**Examples:**

Example 1 (unknown):
```unknown
git clone https://github.com/deepspeedai/DeepSpeed
cd DeepSpeed
git submodule update --init --recursive
cd DeepSpeedExamples/
```

Example 2 (unknown):
```unknown
pip install deepspeed[1bit_adam]
```

Example 3 (unknown):
```unknown
deepspeed --launcher=[mvapich|openmpi] script.py
```

Example 4 (unknown):
```unknown
mpirun -np [#processes] -ppn [#GPUs on each node] -hostfile [hostfile] [MPI flags] python [training_script.py]
```

---

## DS4Sci_EvoformerAttention eliminates memory explosion problems for scaling Evoformer-centric structural biology models

**URL:** https://www.deepspeed.ai/tutorials/ds4sci_evoformerattention/

**Contents:**
- DS4Sci_EvoformerAttention eliminates memory explosion problems for scaling Evoformer-centric structural biology models
    - Contents
- 1. What is DS4Sci_EvoformerAttention
- 2. When to use DS4Sci_EvoformerAttention
- 3. How to use DS4Sci_EvoformerAttention
  - 3.1 Installation
  - 3.2 Unit test and benchmark
  - 3.3 Applying DS4Sci_EvoformerAttention to your own model
- 4. DS4Sci_EvoformerAttention scientific application
  - 4.1 DS4Sci_EvoformerAttention eliminates memory explosion problems for scaling Evoformer-centric structural biology models in OpenFold

DS4Sci_EvoformerAttention is a collection of kernels built to scale the Evoformer computation to larger number of sequences and residuals by reducing the memory footprint and increasing the training speed.

DS4Sci_EvoformerAttention is most beneficial when the number of sequences and residuals is large. The forward kernel is optimized to accelerate computation. It is beneficial to use the forward kernel during inference for various attention mechanisms. The associated backward kernel can be used during training to reduce the memory footprint at the cost of some computation. Therefore, it is beneficial to use DS4Sci_EvoformerAttention in training for memory-constrained operations such as MSA row-wise attention and MSA column-wise attention.

DS4Sci_EvoformerAttention is released as part of DeepSpeed >= 0.10.3. DS4Sci_EvoformerAttention is implemented based on CUTLASS. You need to clone the CUTLASS repository and specify the path to it in the environment variable CUTLASS_PATH.

The kernels will be compiled when DS4Sci_EvoformerAttention is called for the first time.

DS4Sci_EvoformerAttention requires GPUs with compute capability 7.0 or higher (NVIDIA V100 or later GPUs) and the minimal CUDA version is 11.3. It is recommended to use CUDA 11.7 or later for better performance. Besides, the performance of backward kernel on V100 kernel is not as good as that on A100 for now.

The unit test and benchmark are available in the tests folder in DeepSpeed repo. You can use the following command to run the unit test and benchmark.

To use DS4Sci_EvoformerAttention in user’s own models, you need to import DS4Sci_EvoformerAttention from deepspeed.ops.deepspeed4science.

DS4Sci_EvoformerAttention supports four attention mechanisms in Evoformer (MSA row-wise, MSA column-wise, and 2 kinds of Triangular) by using different inputs as shown in the following examples. In the examples, we denote the number of sequences as N_seq and the number of residuals as N_res. The dimension of the hidden states Dim and head number Head are different among different attention. Note that DS4Sci_EvoformerAttention requires the input tensors to be in torch.float16 or torch.bfloat16 data type.

(a) MSA row-wise attention builds attention weights for residue pairs and integrates the information from the pair representation as an additional bias term.

(b) MSA column-wise attention lets the elements that belong to the same target residue exchange information.

(c) Triangular self-attention updates the pair representation. There are two kinds of Triangular self-attention: around starting and around ending node. Below is the example of triangular self-attention around starting node. The triangular self-attention around ending node is similar.

OpenFold is a community reproduction of DeepMind’s AlphaFold2 that makes it possible to train or finetune AlphaFold2 on new datasets. Training AlphaFold2 incurs a memory explosion problem because it contains several custom Evoformer attention variants that manifest unusually large activations. By leveraging DeepSpeed4Science’s DS4Sci_EvoformerAttention kernels, OpenFold team is able to reduce the peak memory requirement by 13x without accuracy loss. Detailed information about the methodology can be found at our website.

Updated: November 5, 2025

**Examples:**

Example 1 (unknown):
```unknown
git clone https://github.com/NVIDIA/cutlass
export CUTLASS_PATH=/path/to/cutlass
```

Example 2 (unknown):
```unknown
pytest -s tests/unit/ops/deepspeed4science/test_DS4Sci_EvoformerAttention.py
python tests/benchmarks/DS4Sci_EvoformerAttention_bench.py
```

Example 3 (python):
```python
from deepspeed.ops.deepspeed4science import DS4Sci_EvoformerAttention
```

Example 4 (unknown):
```unknown
# Q, K, V: [Batch, N_seq, N_res, Head, Dim]
# res_mask: [Batch, N_seq, 1, 1, N_res]
# pair_bias: [Batch, 1, Head, N_res, N_res]
out = DS4Sci_EvoformerAttention(Q, K, V, [res_mask, pair_bias])
```

---

## Training your large model with DeepSpeed

**URL:** https://www.deepspeed.ai/tutorials/large-models-w-deepspeed/

**Contents:**
- Training your large model with DeepSpeed
    - Contents
- Overview
- Possible ways to train a large model
- Deciding which technology to use
- Understanding performance tradeoff between ZeRO and 3D Parallelism

DeepSpeed has been used to train or is in the process of training some of the largest dense models in existence. These include but not limited to:

DeepSpeed offers a collection of system technologies, that has made it possible to train models at these scales. The best technology to train your large model depends on various factors such as the model architecture, batch size, inter-connect bandwidth, etc. Given the number of available choices, this can be confusing and outright daunting. This page is meant as a starting guide to help you navigate your journey towards training your large model.

At a broad level, there are two primary paths to training a large model:

ZeRO based technologies: In simple terms, ZeRO is a memory efficient form of data parallelism that gives you access to the aggregate GPU memory of all the GPU devices available to you, without inefficiency caused by the data replication in data parallelism. In addition, DeepSpeed also offers heterogeneous memory technologies based on ZeRO such as ZeRO-Offload and ZeRO-Infinity, which allow you to effectively leverage CPU and NVMe memory when they are available on your target systems.

Since, ZeRO is a replacement to data parallelism, it offers a seamless integration that does not require model code refactoring for existing data-parallel models. For majority of cases, ZeRO based technologies offers model scalability, training throughput efficiency without compromising ease of use.

3D Parallelism based technologies: 3D Parallelism refers to a combination of three different forms of parallel technologies namely tensor-slicing, pipeline-parallelism, and data parallelism (or ZeRO powered data parallelism). Combing these three forms allows for harnessing the strength of each of these technologies without the drawback of any. 3D Parallelism enables DeepSpeed to achieve excellent training throughput efficiency in the scenarios where relying on ZeRO based technologies alone might be insufficient. However, 3D parallelism requires non-trivial model code refactoring, and therefore a careful consideration is important to identify cases where 3D-Parallelism can bring non-trivial throughput benefits.

3D Parallelism for GPT-2/GPT-3 like models: If you are attempting to train a model whose architecture resembles very closely with GPT-2 or GPT-3, then we have already done the hard work of porting 3D parallelism to a GPT-2/GPT-3 architecture-based model and have created a training pipeline that you can use to efficiently train models with hundreds of billion or even trillions of parameters. Both Megatron-Turing NLG 530B and Big Science use a variation of this code base to scale the model training. You can find the code and tutorial to get started in the DeepSpeed-Megatron GPT-3 repo. For more information on 3D parallelism please checkout the resources below:

3D Parallelism Tutorial A generic tutorial on how to port your model to use DeepSpeed 3D parallelism

3D Parallelism Deep Dive A Microsoft Research blog post that takes a deep dive into 3D parallelism implementation in DeepSpeed.

ZeRO based technologies: For most training scenarios, ZeRO offer training efficiency that is on par with 3D parallelism without requiring model code refactoring. Therefore, if you do not already have your code ported to use 3D parallelism, we suggest first trying ZeRO lines of technology to see if it fits your need. Adding ZeRO to your training pipeline with DeepSpeed is simple and does not require you to make changes to your model. Given the trivial cost of trying out ZeRO with DeepSpeed, it is the fastest way to evaluate and decide if you should further invest in porting your model to use 3D parallelism. Enabling ZeRO with DeepSpeed also gives you access to ZeRO-Offload and ZeRO-Infinity that can enable fine tuning large models on limited GPU resources. To get started, please checkout our ZeRO Tutorial.

For more in-depth information on ZeRO lines of technologies, please checkout our papers:

ZeRO (SC20), ZeRO Offload (ATC21) , and ZeRO-Infinity (SC21),

ZeRO & DeepSpeed, ZeRO-2 & DeepSpeed, ZeRO-Offload, and ZeRO-Infinity & DeepSpeed

The performance of ZeRO and 3D parallelism is generally on par with each other, when the batch size per GPU is not extremely small. ZeRO is a more memory efficient form of data parallelism, and the communication cost of ZeRO is quite similar to that of data parallelism itself. Therefore, for all scenarios where data parallelism works well, so will ZeRO. In fact, ZeRO enables fitting significantly larger batch sizes for large models, when compared to data parallelism due to its memory efficiency, allowing for much better throughput efficiency than data parallelism.

However, in certain scenarios the batch size may not be large enough for ZeRO to be efficient. This maybe especially true when training on thousands of GPUs or with limited network bandwidth. For example, training a GPT-3 model on 4K GPUs, and with a batch size limit of 2K will result in a batch on 0.5 per GPU, which depending on sequence length and network bandwidth might not be sufficiently large to sustain good performance using ZeRO alone.

In such scenarios, one should consider if its possible to increase the batch size to get better efficiency. However, if increasing the batch size is not an option due to convergence related concerns, then pipeline parallelism in 3D parallelism can increase the effective network bandwidth proportional to the number of pipeline stages, allowing 3D parallelism to achieve better throughput than ZeRO.

Updated: November 5, 2025

---

## DeepSpeed Accelerator Abstraction Interface

**URL:** https://www.deepspeed.ai/tutorials/accelerator-abstraction-interface/

**Contents:**
- DeepSpeed Accelerator Abstraction Interface
    - Contents
- Contents
- Introduction
- Write accelerator agnostic models
- Port accelerator runtime calls
- Port accelerator device name
- Tensor operations
- Communication backend
- Run DeepSpeed model on different accelerators

The DeepSpeed Accelerator Abstraction allows user to run large language model seamlessly on various Deep Learning acceleration hardware with DeepSpeed. It offers a set of accelerator runtime and accelerator op builder interface which can be implemented for different hardware. This means user can write large language model code without hardware specific code. With DeepSpeed Accelerator Abstraction, the same large language model can run on different hardware platform, without the need to rewrite model code. This makes running large language model on different hardware easier.

This document covers three topics related to DeepSpeed Accelerator Abstraction Interface:

In this part, you will learn how to write a model that does not contain HW specific code, or how to port a model that run on a specific HW only to be accelerator agnostic. To do this, we first import get_accelerator from deepspeed.accelerator

Note: get_accelerator() is the entrance to DeepSpeed Accelerator Abstraction Interface

First we need to port accelerator runtime calls. On CUDA device, accelerator runtime call appears in the form of torch.cuda.<interface>(...). With DeepSpeed Accelerator Abstract Interface, such accelerator runtime call can be written in the form of get_accelerator().<interface>(...) which will be accelerator agnostic.

A typical conversion looks like the following example:

For most torch.cuda.<interface>(...) call, we can literally replace torch.cuda with get_accelerator(). However, there are some exceptions that needs attention:

However, if we wish to get device index as a number, we should call get_accelerator().current_device()

For CUDA specific device name such as 'cuda' or 'cuda:0', or 'cuda:1', we convert them to get_accelerator().device_name(), get_accelerator().device_name(0), and get_accelerator().device_name(1).

A device name without index can be used if model need to do specific thing for certain accelerator. We suggest to make as less as such usage only for situations can not be resolve other way.

CUDA specific tensor operations needs to be converted according to the following rules:

When we convert a torch tensor to accelerator device such as my_tensor.cuda(), we use my_tensor.to(get_accelerator().device_name())

When we check whether a torch tensor is on accelerator device such as my_tensor.is_cuda, we use get_accelerator().on_accelerator(my_tensor)

When pin a tensor to GPU memory such as my_tensor.pin_memory(), we use get_accelerator().pin_memory(my_tensor)

When a communication backend string is used, the interface get_accelerator().communication_backend_name() is used get get communication backend name. So instead of:

Accelerator Setup Guide provides a guide on how to setup different accelerators for DeepSpeed. It also comes with simple example how to run deepspeed for different accelerators. The following guides are provided:

It is possible to implement a new DeepSpeed accelerator extension to support new accelerator in DeepSpeed. An example to follow is Intel Extension For DeepSpeed. An accelerator extension contains the following components:

Note that an extension does not have to implement all op builders under https://github.com/deepspeedai/DeepSpeed/tree/master/op_builder all at a time. A missing op builder usually means certain DeepSpeed functionality cannot be used for that Accelerator, but models that does not use that functionality can still run.

When implementing op builder for an accelerator extension, one thing needs to be noted is that the op builder native code is being built by DeepSpeed jit load mechanism. This mean the native source file being built needs to be in DeepSpeed installation directory. However these files are defined in accelerator extension installation directory, which cannot be built by DeepSpeed directly. To solve this, follow the example in https://github.com/intel/intel-extension-for-deepspeed/blob/main/intel_extension_for_deepspeed/op_builder/cpu_adam.py to use ‘sycl_kernel_path’ and ‘sycl_kernel_include’ (User can change ‘sycl’ to other prefix in their own accelerator extension) to allow native code be built during DeepSpeed jit load.

When accelerator extension is installed in the environment, it can be used by either explicit call deepspeed.accelerator.set_accelerator(XYZ_Accelerator()) following the example in https://github.com/deepspeedai/DeepSpeed/blob/master/accelerator/real_accelerator.py, or add an implicit detection code in get_accelerator in the same file above.

Updated: November 5, 2025

**Examples:**

Example 1 (python):
```python
from deepspeed.accelerator import get_accelerator
```

Example 2 (unknown):
```unknown
if torch.cuda.is_available():
    ...
```

Example 3 (unknown):
```unknown
if get_accelerator().is_available():
    ...
```

Example 4 (unknown):
```unknown
torch.empty(weight_shape, dtype=dtype, device=get_accelerator().current_device_name())
```

---

## BingBertSQuAD Fine-tuning

**URL:** https://www.deepspeed.ai/tutorials/bert-finetuning/

**Contents:**
- BingBertSQuAD Fine-tuning
    - Contents
- Overview
  - Pre-requisites
  - Running BingBertSquad
- DeepSpeed Integration
  - Configuration
  - Argument Parsing
  - Training
    - Initialization

In this tutorial we will be adding DeepSpeed to the BingBert model for the SQuAD fine-tuning task, called “BingBertSquad” henceforth. We will also demonstrate performance gains.

If you don’t already have a copy of the DeepSpeed repository, please clone in now and checkout the DeepSpeedExamples submodule the contains the BingBertSquad example (DeepSpeedExamples/training/BingBertSquad) we will be going over in the rest of this tutorial.

You also need a pre-trained BERT model checkpoint from either DeepSpeed, HuggingFace, or TensorFlow to run the fine-tuning. Regarding the DeepSpeed model, we will use checkpoint 160 from the BERT pre-training tutorial.

The main part of training is done in nvidia_run_squad_deepspeed.py, which has already been modified to use DeepSpeed. The run_squad_deepspeed.sh script helps to invoke training and setup several different hyperparameters relevant to the training process. In the next few sections we will cover what changes we made to the baseline in order to enable DeepSpeed, you don’t have to make these changes yourself since we have already done them for you.

The deepspeed_bsz24_config.json file gives the user the ability to specify DeepSpeed options in terms of batch size, micro batch size, learning rate, and other parameters. When running the nvidia_run_squad_deepspeed.py, in addition to the --deepspeed flag to enable DeepSpeed, the appropriate DeepSpeed configuration file must be specified using --deepspeed_config deepspeed_bsz24_config.json. Table 1 shows the fine-tuning configuration used in our experiments.

Table 1. Fine-tuning configuration

The first step to apply DeepSpeed is adding arguments to BingBertSquad, using deepspeed.add_config_arguments() in the beginning of the main entry point as in the main() function in nvidia_run_squad_deepspeed.py. The argument passed to add_config_arguments() is obtained from the get_argument_parser() function in utils.py.

Similar to this, all the options with their corresponding description are available in utils.py.

DeepSpeed has an initialization function to wrap the model, optimizer, LR scheduler, and data loader. For BingBertSquad, we simply augment the baseline script with the initialize function to wrap the model and create the optimizer as follows:

This is identical in both Baseline and DeepSpeed, and is performed by loss = model(input_ids, segment_ids, input_mask, start_positions, end_positions).

In the Baseline script you need to handle the all-reduce operation at the gradient accumulation boundary explicitly by using enable_need_reduction() followed by optimizer.backward(loss) in FP16 and loss.backward() in FP32. In DeepSpeed, you may simply do model.backward(loss).

In the Baseline Script, you are required to explicitly specify the optimizer as FusedAdam (along with the handling of dynamic loss scaling) in FP16 and BertAdam in FP32, followed by the call optimizer.step() and optimizer.zero_grad(). DeepSpeed handles this internally (by setting the optimizer using the JSON config) when initialize() is called and thus you don’t need to explicitly write code but just do model.step().

Congratulations! Porting to DeepSpeed is complete.

Once training is complete, the EM and F1 scores may be obtained from the following command:

The table summarizing the results are given below. In all cases (unless otherwise noted), the total batch size is set to 24 and training is conducted on 4 GPUs for 2 epochs on a DGX-2 node. A set of parameters (seeds and learning rates) were tried and the best ones were selected. All learning rates were 3e-5; We set the seeds to 9041 and 19068 for HuggingFace and TensorFlow models, respectively. The checkpoints used for each case are linked in the table below.

DeepSpeed’s optimized transformer kernel can be enabled during fine-tuning to increase the training throughput. In addition to supporting the models pre-trained with DeepSpeed, the kernel can be used with TensorFlow and HuggingFace checkpoints.

An argument --deepspeed_transformer_kernel is already created in utils.py, we enable the transformer kernel by adding it in the shell script.

In the BertEncoder class of the modeling source file, DeepSpeed transformer kernel is created as below when it is enabled by using --deepspeed_transformer_kernel argument.

All configuration settings come from the DeepSpeed configuration file and command arguments and thus we must pass the args variable to here in this model.

Note: batch_size is the maximum bath size of input data, all fine-tuning training data or prediction data shouldn’t exceed this threshold, otherwise it will throw an exception. In the DeepSpeed configuration file micro batch size is defined as train_micro_batch_size_per_gpu, e.g., if it is set as 8 then the --predict_batch_size should also be 8.

For further details about the transformer kernel, please see our usage tutorial and technical deep dive on the fastest BERT training.

BingBertSquad supports both HuggingFace and TensorFlow pretrained models. Here, we show the two model examples:

There are three arguments used for loading these two types of checkpoints.

We can add the following in our fine-tuning shell script in run_squad_deepspeed.sh to run the above HuggingFace and TensorFlow examples.

--deepspeed_transformer_kernel flag is required for using HuggingFace or TensorFlow pretrained models.

--preln flag cannot be used with HuggingFace or TensorFlow pretrained models, since they use a post-layer-norm.

BingBertSquad will check the pretrained models to have the same vocabulary size and won’t be able to run if there is any mismatch. We advise that you use a model checkpoint of the style described above or a DeepSpeed bing_bert checkpoint.

In order to perform fine-tuning, we set the total batch size to 24 as shown in Table 1. However, we can tune the micro-batch size per GPU to get high-performance training. In this regard, we have tried different micro-batch sizes on NVIDIA V100 using either 16GB or 32GB of memory. As Tables 2 and 3 show, we can improve performance by increasing the micro-batch. Compared with PyTorch, we can achieve up to 1.5x speedup for the 16GB V100 while supporting a 2x larger batch size per GPU. On the other hand, we can support as large as 32 batch size (2.6x higher than PyTorch) using a 32GB V100, while providing 1.3x speedup in the end-to-end fine-tune training. Note, that we use the best samples-per-second to compute speedup for the cases that PyTorch runs out-of-memory (OOM).

Table 2. Samples/second for running SQuAD fine-tuning on NVIDIA V100 (16GB) using PyTorch and DeepSpeed transformer kernels.

Table 3. Samples/second for running SQuAD fine-tuning on NVIDIA V100 (32GB) using PyTorch and DeepSpeed transformer kernels.

As mentioned, we can increase the micro-batch size per GPU from 3 to 24 or even higher if a larger batch size is desired. In order to support a larger micro-batch size, we may need to enable different memory-optimization flags for our transformer kernel as described in DeepSpeed Transformer Kernel tutorial. Table 4 shows which optimization flags are required for running different range of micro-batch sizes.

Table 4. The setting of memory-optimization flags for a range of micro-batch size on 16-GB and 32-GB V100.

Fine-tuning the model pre-trained using DeepSpeed Transformer and the recipe in DeepSpeed Fast-Bert Training should yield F1 score of 90.5 and is expected to increase if you let the pre-training longer than suggested in the tutorial.

To get these results, we do require some tuning of the dropout settings as described below:

For the fine-tuning, we only use the deterministic transformer to have reproducible the fine-tuning results. But, we choose different values for dropout based on whether pre-training was done using deterministic or stochastic transformer (Please see Transformer tutorial for more detail of selecting these two modes).

For models pre-trained with deterministic transformer, we use the same dropout ratio used in pre-training (0.1). However, we slightly increase the dropout ratio when fine-tuning the model pre-trained using the stochastic transformer to compensate for the lack of stochastic noise during fine-tuning.

Updated: November 5, 2025

**Examples:**

Example 1 (unknown):
```unknown
git clone https://github.com/deepspeedai/DeepSpeed
cd DeepSpeed
git submodule update --init --recursive
cd DeepSpeedExamples/training/BingBertSquad
```

Example 2 (unknown):
```unknown
parser = get_argument_parser()
# Include DeepSpeed configuration arguments
parser = deepspeed.add_config_arguments(parser)
args = parser.parse_args()
```

Example 3 (unknown):
```unknown
model, optimizer, _, _ = deepspeed.initialize(
    args=args,
    model=model,
    model_parameters=optimizer_grouped_parameters
)
```

Example 4 (unknown):
```unknown
python evaluate-v1.1.py <PATH_TO_DATA_DIR>/dev-v1.1.json <PATH_TO_DATA_DIR>/predictions.json
```

---

## BERT Pre-training

**URL:** https://www.deepspeed.ai/tutorials/bert-pretraining

**Contents:**
- BERT Pre-training
    - Contents
- Pre-training Bing BERT without DeepSpeed
  - Training Data Setup
  - Running the Bing BERT model
- Enabling DeepSpeed
  - Argument Parsing
  - Initialization and Training
    - Initialization
    - Training

Note: On 08/15/2022 we have added another BERT pre-training/fine-tuning example at github.com/deepspeedai/Megatron-DeepSpeed/tree/main/examples_deepspeed/bert_with_pile, which includes a README.md that describes how to use it. Compared to the example described below, the new example in Megatron-DeepSpeed adds supports of ZeRO and tensor-slicing model parallelism (thus support larger model scale), uses a public and richer Pile dataset (user can also use their own data), together with some changes to the model architecture and training hyperparameters as described in this paper. As a result, the BERT models trained by the new example is able to provide better MNLI results than original BERT, but with a slightly different model architecture and larger computation requirements. If you want to train a larger-scale or better quality BERT-style model, we recommend to follow the new example in Megatron-DeepSpeed. If your goal is to strictly reproduce the original BERT model, we recommend to follow the example under DeepSpeedExamples/bing_bert as described below. On the other hand, the tutorial below helps explaining how to integrate DeepSpeed into a pre-training codebase, regardless of which BERT example you use.

In this tutorial we will apply DeepSpeed to pre-train the BERT (Bidirectional Encoder Representations from Transformers), which is widely used for many Natural Language Processing (NLP) tasks. The details of BERT can be found here: BERT: Pre-training of Deep Bidirectional Transformers for Language Understanding.

We will go through how to setup the data pipeline and how to run the original BERT model. Then we will show step-by-step how to modify the model to leverage DeepSpeed. Finally, we demonstrate the performance evaluation and memory usage reduction from using DeepSpeed.

We work from adaptations of huggingface/transformers and NVIDIA/DeepLearningExamples. We have forked this repo under DeepSpeedExamples/bing_bert and made several modifications in their script:

Note: Downloading and pre-processing instructions are coming soon.

Download the Wikipedia and BookCorpus datasets and specify their paths in the model config file DeepSpeedExamples/bing_bert/bert_large_adam_seq128.json:

From DeepSpeedExamples/bing_bert, run:

To use DeepSpeed we need to edit two files :

We first need to add DeepSpeed’s argument parsing to train.py using deepspeed.add_config_arguments(). This step allows the application to recognize DeepSpeed specific configurations.

We modify the train.py to enable training with DeepSpeed.

We use deepspeed.initialize() to create the model, optimizer, and learning rate scheduler. For the Bing BERT model, we initialize DeepSpeed in its prepare_model_optimizer() function as below, to pass the raw model and optimizer (specified from the command option).

Note that for Bing BERT, the raw model is kept in model.network, so we pass model.network as a parameter instead of just model.

The model returned by deepspeed.initialize is the DeepSpeed model engine that we will use to train the model using the forward, backward and step API. Since the model engine exposes the same forward pass API as nn.Module objects, there is no change in the forward pass. Thus, we only modify the backward pass and optimizer/scheduler steps.

Backward propagation is performed by calling backward(loss) directly with the model engine.

The step() function in DeepSpeed engine updates the model parameters as well as the learning rate. Zeroing the gradients is handled automatically by DeepSpeed after the weights have been updated after each step.

DeepSpeed’s model engine has flexible APIs for checkpoint saving and loading in order to handle the both the client model state and its own internal state.

In train.py, we use DeepSpeed’s checkpointing API in the checkpoint_model() function as below, where we collect the client model states and pass them to the model engine by calling save_checkpoint():

In the load_training_checkpoint() function, we use DeepSpeed’s loading checkpoint API and return the states for the client model:

The last step to use DeepSpeed is to create a configuration JSON file (e.g., deepspeed_bsz4096_adam_config.json). This file provides DeepSpeed specific parameters defined by the user, e.g., batch size per GPU, optimizer and its parameters, and whether enabling training with FP16.

In particular, this sample json is specifying the following configuration parameters to DeepSpeed:

That’s it! That’s all you need do in order to use DeepSpeed in terms of modifications. We have included a modified train.py file called DeepSpeedExamples/bing_bert/deepspeed_train.py with all of the changes applied.

To enable the transformer kernel for higher performance, first add an argument --deepspeed_transformer_kernel in utils.py, we can set it as False by default, for easily turning on/off.

Then in the BertEncoder class of the modeling source file, instantiate transformer layers using DeepSpeed transformer kernel as below.

All configuration settings come from the DeepSpeed configuration file and command arguments and thus we must pass the args variable to here in this model.

For more details about the transformer kernel, please see DeepSpeed Transformer Kernel and DeepSpeed Fast-Bert Training.

An example of launching deepspeed_train.py on four nodes with four GPUs each would be:

See the Getting Started guide for more information on launching DeepSpeed.

We achieve the fastest BERT training time while remaining competitive across the industry in terms of achieving F1 score of 90.5 or better on the SQUAD 1.1 dev set. Please follow the BERT fine-tuning tutorial to fine-tune your model that was pre-trained by transformer kernel and reproduce the SQUAD F1 score.

Our configuration for the BERT training result above can be reproduced with the scripts/json configs in our DeepSpeedExamples repo. Below is a table containing a summary of the configurations. Specifically see the ds_train_bert_bsz64k_seq128.sh and ds_train_bert_bsz32k_seq512.sh scripts for more details in DeepSpeedExamples.

Compared to SOTA, DeepSpeed significantly improves single GPU performance for transformer-based model like BERT. Figure above shows the single GPU throughput of training BertBERT-Large optimized through DeepSpeed, compared with two well-known Pytorch implementations, NVIDIA BERT and HuggingFace BERT. DeepSpeed reaches as high as 64 and 53 teraflops throughputs (corresponding to 272 and 52 samples/second) for sequence lengths of 128 and 512, respectively, exhibiting up to 28% throughput improvements over NVIDIA BERT and up to 62% over HuggingFace BERT. We also support up to 1.8x larger batch size without running out of memory.

For more details on how we achieve the record breaking BERT training time please check out deep dive into DeepSpeed BERT Fastest BERT Training

Updated: November 5, 2025

**Examples:**

Example 1 (unknown):
```unknown
{
  ...
  "datasets": {
      "wiki_pretrain_dataset": "/data/bert/bnorick_format/128/wiki_pretrain",
      "bc_pretrain_dataset": "/data/bert/bnorick_format/128/bookcorpus_pretrain"
  },
  ...
}
```

Example 2 (unknown):
```unknown
python train.py  \
    --cf bert_large_adam_seq128.json \
    --train_batch_size 64 \
    --max_seq_length 128 \
    --gradient_accumulation_steps 1  \
    --max_grad_norm 1.0 \
    --fp16 \
    --loss_scale 0 \
    --delay_allreduce \
    --max_steps 10 \
    --output_dir <path-to-model-output>
```

Example 3 (python):
```python
def get_arguments():
    parser = get_argument_parser()
    # Include DeepSpeed configuration arguments
    parser = deepspeed.add_config_arguments(parser)

    args = parser.parse_args()

    return args
```

Example 4 (python):
```python
def prepare_model_optimizer(args):
    # Loading Model
    model = BertMultiTask(args)

    # Optimizer parameters
    optimizer_parameters = prepare_optimizer_parameters(args, model)
    model.network, optimizer, _, _ = deepspeed.initialize(args=args,
                                         model=model.network,
                                         model_parameters=optimizer_parameters,
                                         dist_init_required=False)
    return model, optimizer
```

---

## DeepSpeed Mixture-of-Quantization (MoQ)

**URL:** https://www.deepspeed.ai/tutorials/MoQ-tutorial/

**Contents:**
- DeepSpeed Mixture-of-Quantization (MoQ)
    - Contents
- Prerequisites
  - MoQ Parameters
  - Eigenvalue Parameters
- How to Use MoQ for GLUE Training Tasks
  - DeepSpeed Configuration File
  - Test Script
  - Quantization with dynamic schedule using second-order information (Eigenvalue)
  - Finetuning Results

DeepSpeed introduces new support for model compression using quantization, called Mixture-of-Quantization (MoQ). MoQ is designed on top of QAT (Quantization-Aware Training), with the difference that it schedules various data precisions across the training process. It starts with quantizing the model with a high precision, such as FP16 or 16-bit quantization, and reduce the precision through a pre-defined schedule until reaching the target quantization bits (like 8-bit). Moreover, we use second-order information of the model parameters to dynamically adjust the quantization schedule for each layer of the network separately. We have seen that by adding such schedule and using various data precision in the training process, we can quantize the model with better quality and preserve accuracy. For a better understanding of MoQ methodology, please refer to MoQ deep-dive, here.

Below, we use fine-tune for the GLUE tasks as an illustration of how to use MoQ.

To use MoQ for model quantization training, you should satisfy these two requirements:

MoQ quantization schedule is defined by a number of parameters which allow users to explore different configurations.

enabled: Whether to enable quantization training, default is False.

quantize_verbose: Whether to display verbose details, default is False.

quantizer_kernel: Whether to enable quantization kernel, default is False.

quantize_type: Quantization type, “symmetric” or “asymmetric”, default is “symmetric”.

quantize_groups: Quantization groups, which shows the number of scales used to quantize a model, default is 1.

quantize_bits, The number of bits to control the data-precision transition from a start-bit to the final target-bits (e.g. starting from 16-bit down to 8-bit).

quantize_schedule, This determines how to schedule the training steps at each precision level.

quantize_algo, The algorithm used to quantize the model.

enabled: Whether to enable quantization training with eigenvalue schedule, default value is set to False.

verbose: Whether to display verbose details of eigenvalue computation, default value is set to False.

max_iter: Max iteration in computing eigenvalue, default value is set to 100.

tol: The tolerance error in computing eigenvalue, default value is set to 1e-2.

stability: Variance stabilization factor, default value is set to 1e-6.

gas_boundary_resolution: Indicates eigenvalue computation by every N gas boundary, default value is set to 1.

layer_name: The model scope name pointing to all layers for eigenvalue computation, default value is set to “bert.encoder.layer”.

layer_num: How many layers to compute eigenvalue.

Before fine-tuning the GLUE tasks using DeepSpeed MoQ, you need:

Prepare a config file test.json as below, please note the following important parameters for quantization training:

Create a script file under huggingface/examples folder as below, enabling DeepSpeed using the json file prepared above.

Here we use MRPC task as an example.

Running this script will get MRPC accuracy and F1 metric results with MoQ quantization.

Eigenvalues can be used as a proxy for layer sensitivity during training, and can be used to create a layer-wise quantization schedule. When eigenvalue calculation is enabled, DeepSpeed will compute the eigenvalues for each specified layer at the gas_boundary_resolution and use it to increase the quantize_period by up to 5x based on layer sensitivity to allow the layer enough iterations to adapt before the next precision reduction phase. The factor of 5x was chosen based on heuristics.

Here, we show the results for the GLUE tasks fine-tuning with quantization. The below table illustrates the scheduling parameters we used for each task to reach the reported accuracy. For all these experiments, we use symmetric grouped quantization with 8 groups.

As we see in the following table, MoQ consistently preserve accuracy across different down-stream tasks.

When using the MoQ, one needs to consider the number of samples and training iterations before setting the correct quantization period or offset to make sure that the quantization reaches the desired level of precision before training finishes.

Enabling eigenvalues for quantization dynamically adjust the quantization period on the different parts of the network. This has two positive impact: 1) the quantized network can potentially produce higher accuracy than quantizing each layer with same quantize_period ; 2) it automatically identifies a good quantization schedule for each layer based on its sensitivity.

Updated: November 5, 2025

**Examples:**

Example 1 (unknown):
```unknown
`start_bits`: The start bits in quantization training. Default is set to 16.
`target_bits`: The target bits in quantization training. Default is set to 16.
```

Example 2 (unknown):
```unknown
`quantize_period`: indicates the period by which we reduce down the precision (number of bits) for quantization. By default, we use a period of 100 training steps, that will be doubled every time the precision reduces by 1 bit.
`schedule_offset`: indicates when the quantization starts to happen (before this offset, we just use the normal training precision which can be either FP32/FP16). Default is set to 100 steps.
```

Example 3 (unknown):
```unknown
`q_type`: we currently support symmetric and asymmetric quantization that result in signed and unsigned integer values, respectively. Default is set to symmetric
`rounding`: for the rounding of the quantized values, we can either round to the nearest value or use stochastic rounding. Default is set to nearest.
```

Example 4 (unknown):
```unknown
{
    "optimizer": {
      "type": "AdamW",
      "params": {
        "lr": 2e-5,
        "weight_decay": 0.0,
        "bias_correction": true
      }
    },
    "gradient_clipping": 1.0,
    "fp16": {
      "initial_scale_power": 16,
      "enabled": true
    },
    "quantize_training": {
      "enabled": true,
      "quantize_verbose": true,
      "quantizer_kernel": true,
      "quantize-algo": {
        "q_type": "symmetric"
      },
      "quantize_bits": {
        "start_bits": 16,
        "target_bits": 8
      },
      "quantize_schedule": {
        "quantize_period": 400,
        "schedule_offset": 0
      },
      "quantize_groups": 8,
    }
}
```

---

## Monitor

**URL:** https://www.deepspeed.ai/tutorials/monitor/

**Contents:**
- Monitor
    - Contents
- Overview
- Usage
  - Automatic Monitoring
  - Custom Monitoring

In this tutorial, we introduce the DeepSpeed Monitor and provide examples of its usage.

Monitoring model and system metrics during training is vital to ensure hardware resources are fully utilized. The DeepSpeed Monitor enables live logging of metrics through one or more monitoring backends such as PyTorch’s TensorBoard, WandB, Comet and simple CSV files.

Below is a live monitoring view for TensorBoard:

Below is a live monitoring view for WandB:

Below is a live monitoring view for Comet:

The DeepSpeed Monitor is configured within the deepspeed configuration file. DeepSpeed will automatically monitor key training metrics, including those tracked with the wall_clock_breakdown configuration option. In addition, users can log their own custom events and metrics.

When using DeepSpeed for model training, the Monitor can be configured in the DeepSpeed configuration file. No explicit API calls are needed to use the Monitor. The Monitor can be enabled by adding the following field to DeepSpeed’s configuration json file. Refer to Monitoring for details.

DeepSpeed will automatically log to all available and enabled monitoring backends listed in the config, and will generate live monitoring views such as those listed above.

In addition to automatic monitoring, users can log their own custom metrics in client scripts. Currently, there are two ways to initialize Monitor objects:

The steps to create a custom monitor are as follows:

* Note - Some Monitor backends don’t support mixed sample values. Be sure to use your DeepSpeed engine object’s global_samples attribute in each 3-tuple

For example usage, see the following modified DeepSpeedExamples/cifar example:

Updated: November 5, 2025

**Examples:**

Example 1 (unknown):
```unknown
{
  "tensorboard": {
    "enabled": true,
    "output_path": "output/ds_logs/",
    "job_name": "train_bert"
  }
  "wandb": {
    "enabled": true,
    "team": "my_team",
    "group": "my_group",
    "project": "my_project"
  }
  "comet": {
    "enabled": true,
    "project": "my_project",
    "experiment_name": "my_experiment"
  }
  "csv_monitor": {
    "enabled": true,
    "output_path": "output/ds_logs/",
    "job_name": "train_bert"
  }
}
```

Example 2 (python):
```python
# Step 1: Import monitor (and DeepSpeed config, if needed)
from deepspeed.monitor.monitor import MonitorMaster
from deepspeed.runtime.config import DeepSpeedConfig

# Step 2: Initialized monitor with DeepSpeed config (get DeepSpeed config object, if needed)
ds_config = DeepSpeedConfig("ds_config.json")
monitor = MonitorMaster(ds_config.monitor_config)

for epoch in range(2):

    running_loss = 0.0
    for i, data in enumerate(trainloader):
        pre = time.time()
        inputs, labels = data[0].to(model_engine.local_rank), data[1].to(
            model_engine.local_rank)
        if fp16:
            inputs = inputs.half()
        outputs = model_engine(inputs)
        loss = criterion(outputs, labels)

        model_engine.backward(loss)
        model_engine.step()
        post = time.time()
        # Step 3: Create list of 3-tuple records (single entry in this case)
        events = [("Time per step", post-pre, model_engine.global_samples)]
        # Step 4: Call monitor.write_events on the list from step 3
        monitor.write_events(events)
```

---

## DeepSpeed Sparse Attention

**URL:** https://www.deepspeed.ai/tutorials/sparse-attention/

**Contents:**
- DeepSpeed Sparse Attention
    - Contents
- Sparse attention modules
- How to use sparse attention with DeepSpeed launcher
- How to use individual kernels
- How to config sparsity structures
- How to support new user defined sparsity structures

In this tutorial we describe how to use DeepSpeed Sparse Attention (SA) and its building-block kernels. The easiest way to use SA is through DeepSpeed launcher. We will describe this through an example in How to use sparse attention with DeepSpeed launcher section. But before that, we introduce modules provided by DeepSpeed SA in the next section.

Note: Currently, DeepSpeed Sparse Attention can be used only on NVIDIA V100 or A100 GPUs using Torch >= 1.6 and CUDA 10.1, 10.2, 11.0, or 11.1.

Note: Currently DeepSpeed Transformer Kernels do not support Sparse Attention. To use Sparse Attention, you need to disable Transformer Kernels!

In this section we describe how to use DeepSpeed Sparse Attention through our bing_bert code.

in which sparse_self_attention is an instance of SparseSelfAttention. This module computes attention context through sparse attention replacing underlying matrix multiplications and softmax with their equivalent sparse version. You can update any other attention module similarly.

Please check our bing_bert runner script as an example of how to enable SA with DeepSpeed launcher.

DeepSpeed Sparse Attention can be used as a feature through DeepSpeed, as described above, or simply integrated with any Transformer model as a self-attention module alone. Further, the building block kernels, matrix multiplication and softmax can be used separately. To use sparse attention alone, you can simply install DeepSpeed and import any of the modules described in modules section; example:

Please refer to the Docstrings for details of how to use each module separately.

Following we describe supported sparsity structures, their parameter set and the flexibility of adding arbitrary sparsity pattern on the self-attention layer. You can update DeepSpeed config file using any of the supported sparsity structures and set the parameters accordingly.

Further, we provide a dense pattern (DenseSparsityConfig), that can be used for the sake of testing while it represents the full attention.

Our building block kernels, block-based MatMul and Softmax, can accept any block-based sparsity. This provides the flexibility to apply any block-based sparsity pattern to attention score. To define and apply a new sparsity pattern, you can simply follow any of the above sparsity structures. You need to add a new class that expands SparsityConfig and define make_layout function based on how your sparsity is structured. You can add any extra parameters you may need or just use default parameters of the parent class.

Updated: November 5, 2025

**Examples:**

Example 1 (unknown):
```unknown
attention_scores = torch.matmul(query_layer, key_layer)
attention_scores = attention_scores / math.sqrt(
    self.attention_head_size)

# Apply the attention mask is (precomputed for all layers in BertModel forward() function)
attention_scores = attention_scores + attention_mask

pdtype = attention_scores.dtype
# Normalize the attention scores to probabilities.
attention_probs = self.softmax(attention_scores)

# This is actually dropping out entire tokens to attend to, which might
# seem a bit unusual, but is taken from the original Transformer paper.
attention_probs = self.dropout(attention_probs)

context_layer = torch.matmul(attention_probs, value_layer)
```

Example 2 (unknown):
```unknown
context_layer =
  self.sparse_self_attention(
	query_layer,
	key_layer,
	value_layer,
	key_padding_mask=attention_mask)
```

Example 3 (unknown):
```unknown
self.pad_token_id = config.pad_token_id if hasattr(
   config, 'pad_token_id') and config.pad_token_id is not None else 0
# set sparse_attention_config if it has been selected
self.sparse_attention_config = get_sparse_attention_config(
   args, config.num_attention_heads)
self.encoder = BertEncoder(
   config, args, sparse_attention_config=self.sparse_attention_config)
```

Example 4 (python):
```python
if sparse_attention_config is not None:
    from deepspeed.ops.sparse_attention import BertSparseSelfAttention

    layer.attention.self = BertSparseSelfAttention(
         config, sparsity_config=sparse_attention_config)
```

---

## ZeRO-Offload

**URL:** https://www.deepspeed.ai/tutorials/zero-offload/

**Contents:**
- ZeRO-Offload
    - Contents
- ZeRO-Offload Overview
- Training Environment
- Training a 10B parameter GPT-2 on a single V100 GPU
  - Megatron-LM GPT-2 launch script changes
  - DeepSpeed Configuration Changes
  - CPU Adam perf tuning

ZeRO-3 Offload consists of a subset of features in our newly released ZeRO-Infinity. Read our ZeRO-Infinity blog to learn more!

We recommend that you read the tutorials on Getting Started and ZeRO before stepping through this tutorial.

ZeRO-Offload is a ZeRO optimization that offloads the optimizer memory and computation from the GPU to the host CPU. ZeRO-Offload enables large models with up to 13 billion parameters to be efficiently trained on a single GPU. In this tutorial we will use ZeRO-Offload to train a 10-billion parameter GPT-2 model in DeepSpeed. Furthermore, using ZeRO-Offload in a DeepSpeed model is quick and easy because all you need is to change a few configurations in the DeepSpeed configuration json. No code changes are needed.

For large model training, optimizers such as Adam, can consume a significant amount of GPU compute and memory. ZeRO-Offload reduces the GPU compute and memory requirements of such models by leveraging compute and memory resources on the host CPU to execute the optimizer. Furthermore, to prevent the optimizer from becoming a bottleneck, ZeRO-Offload uses DeepSpeed’s highly optimized CPU implementation of Adam called DeepSpeedCPUAdam. DeepSpeedCPUAdam is 5X–7X faster than the standard PyTorch implementation. To deep dive into the design and performance of ZeRO-Offload, please see our blog post.

For this tutorial, we will configure a 10 billion parameter GPT-2 model using the DeepSpeed Megatron-LM GPT-2 code. We advise stepping through the Megatron-LM tutorial if you have not previously done so. We will use a single NVIDIA Tesla V100-SXM3 Tensor Core GPU with 32GB RAM for this exercise.

We need to make changes to the Megatron-LM launch script and to the DeepSpeed configuration json.

We need to apply two changes to the launch script for the DeepSpeed Megatron-LM GPT-2 model. The first change is to configure a 10B parameter GPT-2 model with activation checkpointing enabled, which can be achieved by the following set of changes:

Most of the flags in the changes above should be familiar if you have stepped through the Megatron-LM tutorial.

Second, we need to apply the following changes to ensure that only one GPU is used for training.

ZeRO-Offload leverages many ZeRO stage 1 and 2 mechanisms, and so the configuration changes to enable ZeRO-Offload are an extension of those required to enable ZeRO stage 1 or 2. The zero_optimization configuration to enable ZeRO-Offload is shown below:

As seen above, in addition to setting the stage field to 2 (to enable ZeRO stage 2, but stage 1 also works), we also need to set the offload_optimizer device to cpu to enable ZeRO-Offload optimizations. In addition, we can set other ZeRO stage 2 optimization flags, such as overlap_comm to tune ZeRO-Offload performance. With these changes we can now run the model. We share some screenshots of the training below.

Here is a screenshot of the training log:

Here is a screenshot of nvidia-smi showing that only GPU 0 is active during training:

Finally, here is a screenshot of htop showing host CPU and memory activity during optimizer computation:

ZeRO offload already support multi-gpu training. If the workload is using CPU optimizer, the workload can be further tuned by passing --bind_cores_to_rank to the deepspeed launch command. This switch will mainly do two things:

ZeRO offload is a hybrid workload that is both heavy on GPU and CPU, and DeepSpeed is optimized for both GPU and CPU performance. Refer to How to launch DeepSpeed on Intel Architecture CPU for more details on how to tune core bindings for CPU performance.

Congratulations! You have completed the ZeRO-Offload tutorial.

Updated: November 5, 2025

**Examples:**

Example 1 (unknown):
```unknown
--model-parallel-size 1 \
       --num-layers 50 \
       --hidden-size 4096 \
       --num-attention-heads 32 \
       --batch-size 10 \
       --deepspeed_config ds_zero_offload.config \
       --checkpoint-activations
```

Example 2 (unknown):
```unknown
deepspeed --num_nodes 1 --num_gpus 1 ...
```

Example 3 (unknown):
```unknown
{
    "zero_optimization": {
        "stage": 2,
        "offload_optimizer": {
            "device": "cpu",
        }
        "contiguous_gradients": true,
        "overlap_comm": true
    }
}
```

---

## Accelerating Training of Transformer-Based Language Models with Progressive Layer Dropping

**URL:** https://www.deepspeed.ai/tutorials/progressive_layer_dropping/

**Contents:**
- Accelerating Training of Transformer-Based Language Models with Progressive Layer Dropping
    - Contents
- Running Pre-training with DeepSpeed and PLD
- Fine-tuning with DeepSpeed on GLUE Tasks
  - Expected Results

In this tutorial, we are going to introduce the progressive layer dropping (PLD) in DeepSpeed and provide examples on how to use PLD. PLD allows to train Transformer networks such as BERT 24% faster under the same number of samples and 2.5 times faster to get similar accuracy on downstream tasks. Detailed description of PLD and the experimental results are available in our technical report.

To illustrate how to use PLD in DeepSpeed, we show how to enable PLD to pre-train a BERT model and fine-tune the pre-trained model on the GLUE datasets.

To perform pre-training, one needs to first prepare the datasets. For this part, please refer our BERT Pre-training post, which contains detailed information on how to do data downloading and pre-processing. For the below experiment, we use Wikipedia text and Bookcorpus, similar as Devlin et. al..

The main part of pre-training is done in deepspeed_train.py, which has already been modified to use DeepSpeed. The ds_train_bert_progressive_layer_drop_bsz4k_seq128.sh is the shell script that launches the pre-training with DeepSpeed and PLD.

Most of the flags in the above script should be familiar if you have stepped through the BERT pre-training tutorial. To enable training with PLD, one needs to enable PLD in both the client script and in the DeepSpeed engine. To enable PLD in the client script, one needs to add the following command line flag to enable progressive layer dropping on Transformer blocks.

To enable PLD in DeepSpeed, one needs to update the json configuration file with an appropriate PLD configuration dictionary like below:

we recommend a PLD theta value of 0.5 and gamma of 0.001 because these have worked well in our experiments.

With these configuration changes, the DeepSpeed engine should print a runtime message as below:

The deepspeed_bsz4k_progressive_layer_drop_config_seq128.json file allows users to specify DeepSpeed options in terms of batch size, micro batch size, optimizer, learning rate, sequence length, and other parameters. Below is the DeepSpeed configuration file we use for running BERT and PLD.

Note that the above configuration assumes training on 64 X 32GB V100 GPUs. Each GPU uses a micro batch size of 16 and accumulates gradients until the effective batch size reaches 4096. If you have GPUs with less memory, you may need to reduce “train_micro_batch_size_per_gpu”. Alternatively, if you have more GPUs, you can increase the “train_batch_size” to increase training speed. We use the following hyperparameters for pre-training BERT with PLD enabled.

Table 1. Pre-training hyperparameters

Note: DeepSpeed now supports PreLayerNorm as the default way for training BERT, because of its ability to avoid vanishing gradient, stabilize optimization, and performance gains, as described in our fastest BERT training blog post. We therefore support the switchable Transformer block directly on the BERT with PreLayerNorm. The implementation can be found at “example\bing_bert\nvidia\modelingpreln_layerdrop.py”.

We use GLUE for fine-tuning tasks. GLUE (General Language Understanding Evaluation benchmark) (https://gluebenchmark.com/) is a collection of sentence or sentence-pair natural language understanding tasks including question answering, sentiment analysis, and textual entailment. It is designed to favor sample-efficient learning and knowledge-transfer across a range of different linguistic tasks in different domains.

One can download all GLUE data using the provided helper script. Once the data has been downloaded, one can set up the data and move the data to “/data/GlueData”, which is the default location for hosting GLUE data. We then can use the PLD pre-trained BERT model checkpoint to run the fine-tuning.

The main part of fine-tuning is done in run_glue_classifier_bert_base.py, which has already been modified to use DeepSpeed. Before the fine-tuning, one needs to specify the BERT model configuration through the following config in run_glue_classifier_bert_base.py. In this case, it has already been modified to be the same as the configuration of the pre-trained model.

Next, one can load a DeepSpeed style checkpoint with the following command, which has also already been added in the script.

Finally, the run_glue_classifier_bert_base.sh script invokes pre-training and setups several hyperparameters relevant to fine-tuning.

The fine-tuning results can be found under the “logs” directory, and below are expected results for PLD on GLUE tasks. The “Lr” row indicates the learning rate we use for getting the corresponding accuracy result for each task.

Updated: November 5, 2025

**Examples:**

Example 1 (unknown):
```unknown
bash ds_train_bert_progressive_layer_drop_bsz4k_seq128.sh
```

Example 2 (unknown):
```unknown
--progressive_layer_drop
```

Example 3 (unknown):
```unknown
{
  ...
  "progressive_layer_drop": {
    "enabled": true,
    "theta": 0.5,
    "gamma": 0.001
  }
}
```

Example 4 (unknown):
```unknown
[INFO] [logging.py:60:log_dist] [Rank 0] Enabled progressive layer dropping (theta = 0.5)
```

---

## Communication Logging

**URL:** https://www.deepspeed.ai/tutorials/comms-logging

**Contents:**
- Communication Logging
    - Contents
- Overview
- Usage
  - Configuration Setup
  - Verbose Logging
  - Log Summaries

In this tutorial, we introduce DeepSpeed communication logging and provide examples of its usage.

NOTE: All logging communication calls are synchronized in order to provide accurate timing information. This may hamper performance if your model heavily uses asynchronous communication operations.

Logging communication calls is vital to ensure networking resources are fully utilized. The DeepSpeed communication logger enables the detection and logging of all communication operations launched under deepspeed.comm. Each communication operation can all be directly printed to the console immediately after completion (via the verbose config option), or a summary may be printed with a call to deepspeed.comm.log_summary() or deepspeed.com.log_summary(show_straggler=True) in the client code at the completion of training, an epoch, after N training iterations, etc.

Communication logging in DeepSpeed is configured within the deepspeed configuration file. DeepSpeed will automatically log communication either all operations (prof_all), or user-specified operations (prof_ops).

Communication logging can be configured in the DeepSpeed configuration file. Communication logging can be enabled by adding the following field to DeepSpeed’s configuration json file. Refer to Communication Logging for details.

There are currently two ways to view communication log records:

If the enabled configuration option is selected, all communication operations will be immediately printed to the console. This mode is intended for detailed debugging, and is not recommended for most users. The following is an example snippet of verbose output:

For advanced users, the debug option will append the calling function of each communication operation to that operation’s log_name. See Log Summaries for an example of a deepspeed.comm.log_summary() call with debug enabled.

It’s recommended that users add a call to deepspeed.comm.log_summary() at training milestones (e.g. every epoch or N iterations). This enables high-level communication logging without having to sift through logs from verbose.

The steps to add DeepSpeed communication log summaries are as follows:

For example usage, see the following modified DeepSpeedExamples/cifar example:

The following is a truncated example output of deepspeed.comm.log_summary() at the end of 10 iterations of Megatron-DeepSpeed with ZeRO-3:

And the following is a call to deepspeed.comm.log_summary under the same configuration with debug enabled:

Straggler effect can be shown by supplying optional argument show_straggler=True to deepspeed.comm.log_summary() call. Straggler effect is defined as the time a rank waits for the slowest rank to start communication. For each collective, log_summary would get the minimum collective time among all ranks, compute straggler effect as follows:

Print straggler effect with the following log_summary call in the example above:

Updated: November 5, 2025

**Examples:**

Example 1 (unknown):
```unknown
"comms_logger": {
  "enabled": true,
  "verbose": false,
  "prof_all": true,
  "debug": false
}
```

Example 2 (unknown):
```unknown
[2022-06-26 01:39:55,722] [INFO] [logging.py:69:log_dist] [Rank 0] rank=0 | comm op: reduce_scatter_tensor | time (ms): 9.46 | msg size: 678.86 MB | algbw (Gbps): 1204.52  | busbw (Gbps): 1129.23
[2022-06-26 01:39:56,470] [INFO] [logging.py:69:log_dist] [Rank 0] rank=0 | comm op: all_gather_into_tensor | time (ms): 0.11 | msg size: 6.0 MB | algbw (Gbps): 954.41  | busbw (Gbps): 894.76
[2022-06-26 01:39:56,471] [INFO] [logging.py:69:log_dist] [Rank 0] rank=0 | comm op: all_gather_into_tensor | time (ms): 0.08 | msg size: 6.0 MB | algbw (Gbps): 1293.47  | busbw (Gbps): 1212.63
```

Example 3 (unknown):
```unknown
# Step 2: (Optional) Import deepspeed.comm
import deepspeed.comm as dist

# Note that any communication operations using `import torch.distributed as dist` calls can remain unchanged, and will be automatically logged under deepspeed.comm!
dist.all_reduce(tensor)

for epoch in range(2):

    running_loss = 0.0
    for i, data in enumerate(trainloader):
        pre = time.time()
        inputs, labels = data[0].to(model_engine.local_rank), data[1].to(
            model_engine.local_rank)
        if fp16:
            inputs = inputs.half()
        outputs = model_engine(inputs)
        loss = criterion(outputs, labels)

        model_engine.backward(loss)
        model_engine.step()
        post = time.time()
    # Step 3: Call `deepspeed.comm.log_summary()`
    dist.log_summary()
```

Example 4 (unknown):
```unknown
Comm. Op            Message Size        Count               Total Latency(ms)   Avg Latency(ms)     tput_avg (Gbps)     busbw_avg (Gbps)
broadcast
                    2.0 KB              146                 11.12               0.08                0.43                0.41
                    98.25 MB            1                   8317.12             8317.12             0.20                0.19
reduce_scatter_tensor
                    678.86 MB           40                  602.29              9.69                1468.06             1376.31
```

---

## Universal Checkpointing with DeepSpeed: A Practical Guide

**URL:** https://www.deepspeed.ai/tutorials/universal-checkpointing/

**Contents:**
- Universal Checkpointing with DeepSpeed: A Practical Guide
    - Contents
- Introduction to Universal Checkpointing
- Prerequisites
- How to use DeepSpeed Universal Checkpointing
  - Step 1: Create ZeRO Checkpoint
  - Step 2: Convert ZeRO Checkpoint to Universal Format
  - Step 3: Resume Training with Universal Checkpoint
- Conclusion

DeepSpeed Universal Checkpointing feature is a powerful tool for saving and loading model checkpoints in a way that is both efficient and flexible, enabling seamless model training continuation and finetuning across different model architectures, different parallelism techniques and training configurations. This tutorial, tailored for both begininers and experienced users, provides a step-by-step guide on how to leverage Universal Checkpointing in your DeepSpeed-powered applications. This tutorial will guide you through the process of creating ZeRO checkpoints, converting them into a Universal format, and resuming training with these universal checkpoints. This approach is crucial for leveraging pre-trained models and facilitating seamless model training across different setups.

Universal Checkpointing in DeepSpeed abstracts away the complexities of saving and loading model states, optimizer states, and training scheduler states. This feature is designed to work out of the box with minimal configuration, supporting a wide range of model sizes and types, from small-scale models to large, distributed models with different parallelism topologies trained across multiple GPUs and other accelerators.

Before you begin, ensure you have the following:

Follow the three simple steps below:

The first step in leveraging DeepSpeed Universal Checkpointing is to create a ZeRO checkpoint. ZeRO (Zero Redundancy Optimizer) is a memory optimization technology in DeepSpeed that allows for efficient training of large models. To create a ZeRO checkpoint, you’ll need to:

Once you have a ZeRO checkpoint, the next step is to convert it into the Universal format. This format is designed to be flexible and compatible across different model architectures and DeepSpeed configurations. To convert a checkpoint:

This script will process the ZeRO checkpoint and generate a new checkpoint in the Universal format. Pass --help flag to see other options.

With the Universal checkpoint ready, you can now resume training on potentially with different parallelism topologies or training configurations. To do this add --universal-checkpoint to your DeepSpeed config (json) file

DeepSpeed Universal Checkpointing simplifies the management of model states, making it easier to save, load, and transfer model states across different training sessions and parallelism techniques. By following the steps outlined in this tutorial, you can integrate Universal Checkpointing into your DeepSpeed applications, enhancing your model training and development workflow.

For more detailed examples and advanced configurations, please refer to the Megatron-DeepSpeed examples.

For technical in-depth of DeepSpeed Universal Checkpointing, please see arxiv manuscript and blog.

Updated: November 5, 2025

**Examples:**

Example 1 (unknown):
```unknown
python ds_to_universal.py --input_folder /path/to/zero/checkpoint --output_folder /path/to/universal/checkpoint
```

---

## Zero Redundancy Optimizer

**URL:** https://www.deepspeed.ai/tutorials/zero/

**Contents:**
- Zero Redundancy Optimizer
    - Contents
- ZeRO Overview
- Training environment
- Enabling ZeRO Optimization
  - Training a 1.5B Parameter GPT-2 model
  - Training a 10B Parameter GPT-2 model
  - Training trillion-scale models with ZeRO-Infinity
    - Offloading to CPU and NVMe with ZeRO-Infinity
    - Allocating Massive Megatron-LM Models

If you have not done so already, we advise that you read the DeepSpeed tutorials on Getting Started and Megatron-LM GPT-2 before stepping through this tutorial.

In this tutorial, we will apply the ZeRO optimizer to the Megatron-LM GPT-2 model. ZeRO is a powerful set of memory optimization techniques that enable effective training of large models with trillions of parameters, such as GPT-2 and Turing-NLG 17B. Compared to the alternative model parallelism approaches for training large models, a key appeal of ZeRO is that no model code modifications are required. As this tutorial will demonstrate, using ZeRO in a DeepSpeed model is quick and easy because all you need is to change a few configurations in the DeepSpeed configuration JSON. No code changes are needed.

ZeRO leverages the aggregate computation and memory resources of data parallelism to reduce the memory and compute requirements of each device (GPU) used for model training. ZeRO reduces the memory consumption of each GPU by partitioning the various model training states (weights, gradients, and optimizer states) across the available devices (GPUs and CPUs) in the distributed training hardware. Concretely, ZeRO is being implemented as incremental stages of optimizations, where optimizations in earlier stages are available in the later stages. To deep dive into ZeRO, please see our paper.

Stage 1: The optimizer states (e.g., for Adam optimizer, 32-bit weights, and the first, and second moment estimates) are partitioned across the processes, so that each process updates only its partition.

Stage 2: The reduced 16-bit gradients for updating the model weights are also partitioned such that each process retains only the gradients corresponding to its portion of the optimizer states.

Stage 3: The 16-bit model parameters are partitioned across the processes. ZeRO-3 will automatically collect and partition them during the forward and backward passes.

In addition, ZeRO-3 includes the infinity offload engine to form ZeRO-Infinity (paper), which can offload to both CPU and NVMe memory for huge memory savings.

We use the DeepSpeed Megatron-LM GPT-2 code for this exercise. You can step through the Megatron-LM tutorial to familiarize yourself with the code. We will train the models in this tutorial on NVIDIA Tesla V100-SXM3 Tensor Core GPUs with 32GB RAM.

To enable ZeRO optimizations for a DeepSpeed model, we simply add the zero_optimization key to the DeepSpeed JSON configuration. A full description of configuration knobs of the zero_optimization key is available here.

We demonstrate the benefits of ZeRO stage 1 by showing that it enables data parallel training of a 1.5 billion parameter GPT-2 model on eight V100 GPUs. We configure training to use a batch size of 1 per device to ensure that the memory consumption is primarily due to model parameters and optimizer states. We create this training scenario by applying the following modifications to the deepspeed launch script:

Training this model without ZeRO fails with an out-of-memory (OOM) error as shown below:

A key reason why this model does not fit in GPU memory is that the Adam optimizer states for the model consume 18GB; a significant portion of the 32GB RAM. By using ZeRO stage 1 to partition the optimizer state among eight data parallel ranks, the per-device memory consumption can be reduced to 2.25GB, thus making the model trainable. To enable ZeRO stage 1, we simply update the DeepSpeed JSON config file as below:

As seen above, we set two fields in the zero_optimization key. Specifically we set the stage field to 1, and the optional reduce_bucket_size for gradient reduction to 500M. With ZeRO stage 1 enabled, the model can now train smoothly on 8 GPUs without running out of memory. Below we provide some screenshots of the model training:

From the nvidia-smi screenshot above we can see that only GPUs 6-7 are being used for training the model. With ZeRO stage 1 we can further reduce the per-device memory consumption by increasing the data parallelism degree. These memory savings can be leveraged to either increase model size and/or batch size. In contrast, such benefits are not possible with data parallelism alone.

ZeRO stage 2 optimizations further increases the size of models that can be trained using data parallelism. We show this by training a model with 10B parameters using 32 V100 GPUs.

First, we need to configure a 10B parameter model with activation checkpointing enabled. This can be done by applying the following GPT-2 model configuration changes to the DeepSpeed launch script.

Next, we need to update the DeepSpeed JSON configuration, as shown below, to enable ZeRO stage 2 optimizations:

In the above changes, we have set the stage field to 2, and configured other optimization knobs that are available in ZeRO stage 2. For example, we have enabled contiguous_gradients to reduce memory fragmentation during backward pass. A full description of these optimization knobs is available here. With these changes, we can now launch the training run.

Here is a screenshot of the training log:

Here is a screenshot of nvidia-smi showing GPU activity during training:

ZeRO-3, the third stage of ZeRO, partitions the full model state (i.e., weights, gradients, and optimizer states) to scale memory savings linearly with the degree of data parallelism. ZeRO-3 can be enabled in the JSON configuration. A full description of these configurations is available here.

ZeRO-Infinity uses DeepSpeed’s infinity offload engine to offload the full model state to CPU or NVMe memory, allowing for even larger model sizes. Offloading can be enabled inside the DeepSpeed configuration:

ZeRO-Infinity vs ZeRO-Offload: DeepSpeed first included offloading capabilities with ZeRO-Offload, a system for offloading optimizer and gradient states to CPU memory within ZeRO-2. ZeRO-Infinity is the next generation of offloading capabilities accessible to ZeRO-3. ZeRO-Infinity is able to offload more data than ZeRO-Offload and has more effective bandwidth utilization and overlapping of computation and communication.

We make two further changes to model initialization in order to support models that exceed local system memory, but not total system memory.

Allocate the model in a memory-scalable fashion. The model parameters will be allocated and immediately partitioned across the data parallel group. If remote_device is "cpu" or "nvme", the model will also be allocated in CPU/NVMe memory instead of GPU memory. Please see the full ZeRO-3 Init docs for more details.

Gather the embeddings weight for initialization. DeepSpeed will automatically gather a module’s parameters during its constructor and for its forward and backward pass. However, additional accesses must coordinate with DeepSpeed to ensure that parameter data is gathered and subsequently partitioned. If the tensor is modified, the modifier_rank argument should also be used to ensure all ranks have a consistent view of the data. Please see the full GatheredParameters docs for more details.

ZeRO-Infinity includes a replacement for Linear layers that further reduces memory. We optionally tile the model parallel linear layers found in each Transformer layer. Note that model parallelism and tiling can be combined by specifying the corresponding base class when building the layer. The deepspeed.zero.TiledLinear module exploits the data fetch and release pattern of ZeRO-3 to reduce the working memory requirements by breaking down a large operator into smaller tiles that can be executed sequentially.

We include the changes for one example from Megatron-LM’s ParallelMLP. Three more model-parallel layers in transformer.py proceed similarly.

The model parallel layers of Megatron-LM have a special form in which the additive bias of the layer is delayed and instead returned from forward() to be fused with a later operator. DeepSpeed’s deepspeed.zero.TiledLinearReturnBias subclass of TiledLinear simply also forwards the returned bias parameter without accumulating.

Note that we scale in_splits and out_splits proportionally with input_size and output_size. This results in tiles of fixed size [hidden/tile_factor, hidden/tile_factor].

Deprecated: DeepSpeed version 0.3.15 introduced automatic external parameter registration and this step is no longer needed.

If you need to take the pretrained weights out of Deepspeed here is what you can do for getting fp16 weights:

And then save the model using:

Because it requires consolidation of the weights on one GPU it can be slow and memory demanding, so only use this feature when needed.

Note that if stage3_gather_16bit_weights_on_model_save is False, no weights will be saved (again, because state_dict doesn’t have them). You can use this method to save ZeRO-2 weights as well.

If you’d like to get the fp32 weights, we supply a special script that can do offline consolidation. It requires no configuration files or GPUs. Here is an example of its usage:

The zero_to_fp32.py script gets created automatically when you save a checkpoint.

Note: currently this script uses 2x memory (general RAM) of the size of the final checkpoint.

Alternatively, if you have plenty of spare CPU memory and instead of getting the file you want your model to be updated to its fp32 weights, you can do the following at the end of the training:

Beware, that the model will be good for saving, but no longer good for continuing the training and will require a deepspeed.initialize() anew.

If you just want the state_dict, you can do:

Congratulations! You have completed the ZeRO tutorial.

Updated: November 5, 2025

**Examples:**

Example 1 (unknown):
```unknown
--model-parallel-size 1 \
       --num-layers 48 \
       --hidden-size 1600 \
       --num-attention-heads 16 \
       --batch-size 1 \
       --deepspeed_config ds_zero_stage_1.config \
```

Example 2 (unknown):
```unknown
{
    "zero_optimization": {
        "stage": 1,
        "reduce_bucket_size": 5e8
    }
}
```

Example 3 (unknown):
```unknown
--model-parallel-size 1 \
       --num-layers 50 \
       --hidden-size 4096 \
       --num-attention-heads 32 \
       --batch-size 1 \
       --deepspeed_config ds_zero_stage_2.config \
       --checkpoint-activations
```

Example 4 (unknown):
```unknown
{
    "zero_optimization": {
        "stage": 2,
        "contiguous_gradients": true,
        "overlap_comm": true,
        "reduce_scatter": true,
        "reduce_bucket_size": 5e8,
        "allgather_bucket_size": 5e8
    }
}
```

---

## Maximizing Communication Efficiency for Large-scale Training via 0/1 Adam

**URL:** https://www.deepspeed.ai/tutorials/zero-one-adam

**Contents:**
- Maximizing Communication Efficiency for Large-scale Training via 0/1 Adam
    - Contents
- 1. Overview
  - 1.1 Pre-requisites for installing DeepSpeed
  - 1.2 Pre-requisites for 0/1 Adam
    - 1.2.1 NCCL-based implementation
    - 1.2.2 MPI-based implementation
    - 1.2.3 Compressed implementation
  - 1.3 0/1 Adam Algorithm
  - 1.4 Configuration of 0/1 Adam

Watch out! 1) The NCCL-based implementation requires PyTorch >= 1.8 (and NCCL >= 2.8.3 when you have 64 or more GPUs). See details below. 2) Although 0/1 Adam is compatible with both FP16 and FP32, currently we only verified the convergence under mixed precision/FP16 training. 3) Currently the MPI-based implementation is not compatible with pipeline parallelism. 4) Frequent checkpoint loading could hurt 0/1 Adam’s convergence. See details below.

In this tutorial, we introduce DeepSpeed’s 0/1 Adam optimizer, which can improve model training speed on communication-constrained clusters, especially for communication-intensive large models. For instance, it is able to reduce the overall communication volume on BERT-large pre-training by up to 26x without affecting the end-to-end model accuracy. Compared to the 1-bit Adam optimizer, 0/1 Adam provides a more flexible way of using compressed communication via adaptive variance state freezing. Additionally, it allows the computing nodes to skip communication rounds during training using a technique called 1-bit sync, without compromising the convergence speed. We have a paper which provides the technical details including algorithm, system implementation, and evaluations.

To illustrate the benefits and usage of 0/1 Adam optimizer, we use the BERT Pre-training task as example. For more details on this task, please refer to the tutorial.

If you don’t already have a copy of the DeepSpeed repository, please clone it now and checkout the DeepSpeedExamples submodule that contains the BERT Pre-training example.

In DeepSpeed, we introduce a system implementation for compressed communication using the NCCL backend of PyTorch distributed. This implementation provides better performance and usability than the MPI-based implementation below. Thus we highly recommend users to choose this implementation.

Watch out! This NCCL-based implementation requires PyTorch >= 1.8. It also requires NCCL >= 2.8.3 when you have 64 or more GPUs to avoid certain NCCL runtime bugs. Currently (2021/03/16) NCCL 2.8.3 is not officially supported by PyTorch. The solution we used is by hacking in NCCL 2.8.3 via LD_PRELOAD: 1) Install NCCL 2.8.3. This works for us on a CUDA 11 system: apt-get install -y libnccl2=2.8.3-1+cuda11.0 libnccl-dev=2.8.3-1+cuda11.0. 2) Set LD_PRELOAD to the library path. This works for us: LD_PRELOAD=/usr/lib/x86_64-linux-gnu/libnccl.so.2.8.3. To confirm LD_PRELOAD is working you can see the version it uses in the NCCL logs if you have NCCL_DEBUG=INFO, it should say: NCCL version 2.8.3+cuda11.0.

For this implementation, we rely on Message Passing Interface (MPI) for advanced communication primitives.

We package the necessary dependencies in the DeepSpeed docker images. However, if you are using a different build system, please install MPI and mpi4py on your system. To install the prerequisites run:

We have tested CUDA-Aware MPI communication using the MVAPICH2-GDR library. However, any CUDA-Aware communication library including OpenMPI should work fine with these examples.

An example launch command for 0/1 Adam using the deepspeed launcher is as follows:

Please note that for MPI-based implementation of 0/1 Adam, the --launcher=[mvapich|openmpi] flag is required when using the deepspeed launcher.

Alternatively, the standard mpirun launcher can also be used as follows:

This backend provides an approach to abstract the generic part of one-bit optimizers and implements accelerator dependent part with DeepSpeed custom op builder. To use this CompressedBackend, you should make sure that your current accelerator supports PackbitsBuilder, so that it could be loaded to do high performance packing and unpacking between float and Byte datatype, which is utilized in one-bit algorithm. An example can be found in Deepspeed/op_builder/xpu/packbits.py. This approach does not require NCCL or MPI based communication library. It will automatically use your default communication library selected by your accelerator in deepspeed/comm.

The detailed description of the 0/1 Adam algorithm can be seen from our paper.

The 0/1 Adam feature can be used by setting the optimizer configuration options as follows. An example json config file is shown below.

Please note the new parameters var_freeze_step, var_update_scaler, local_step_scaler, local_step_clipper, cuda_aware and comm_backend_name that have been added to support the 0/1 Adam feature:

var_update_scaler is the interval to update the variance. Note that the update policy for variance follows an exponential rule. Formally, if we denote $k_j$ as the step where $j$-th variance update takes place, then it follows that $k_{j+1} - k_j = 2\cdot\exp{\lfloor j/\kappa\rfloor}$ (please refer to the 0/1 Adam paper for detailed explanation), and the var_update_scaler denotes the $\kappa$ factor in such expression. In practice, we found its default value (16) is able to work well on most of the tasks, including BERT-Base/Large pretraining, GPT pretraining, and ImageNet training.

local_step_scaler and local_step_clipper are two hyperparameters for learning rate based local step policy in 0/1 Adam. Formally, if we denote $k_j$ as the step where $j$-th synchronization takes place among all the workers, then it follows that $k_{j+1} - k_j = 2\cdot\exp{\min(\lfloor j/\alpha\rfloor, \beta )}$ (please refer to the 0/1 Adam paper for detailed explanation). Following such notations, local_step_scaler and local_step_clipper denote the $\alpha$ and $\beta$, respectively. Informally, local_step_scaler decides the frequency of synchronization while local_step_clipper denotes the maximal local step interval 0/1 Adam can use. The learning rate policy is the default policy used in 0/1 Adam, and the value of local_step_scaler can be pre-calculated (see 0/1 Adam paper Section 6). We can also trivially construct other policies by setting these two hyperparameters such as constant local step interval policy by setting local_step_scaler=1 and local_step_clipper=constant.

cuda_aware is used for MPI-based implementation to indicate that the underlying MPI library supports CUDA-Aware communication. This feature is only supported on systems with InfiniBand interconnect and a CUDA-Aware MPI library like MVAPICH2-GDR or OpenMPI built with CUDA-Aware support. Setting cuda_aware to False will allow training on Ethernet based systems. However, the communication will happen using sender as well as receiver side memory copies between CPU and GPU buffers before and after communication.

comm_backend_name is used to indicate which backend implementation to use. You can choose between NCCL, MPI-based and compressed implementations by setting comm_backend_name to “nccl”, “mpi” or “compressed”. When using NCCL-based implementation, there is no need to set cuda_aware.

Because 1-bit compression cannot represent exact zero, the compression error would keep accumulating in the momentum if a parameter have constant zero gradients during training. For example, for BERT pre-training seq length 128, bert.embeddings.position_embeddings.weight has constant zeros in its gradient and momentum for row 129 to 512, because it only learns up to seq length 128 while the model supports up to seq length 512. Thus in 0/1 Adam we added support of a momentum mask for users to specify those params that have constant exact zeros in their gradients. See example script for how to configure this momentum mask. One thing to note is that we don’t use momentum mask saved in checkpoints since this mask could change during training (e.g., BERT seqlen 128 and 512 require different masks). So you have to provide this mask every time in your training script.

Watch out! 0/1 Adam relies on an compression error compensation mechanism to maintain the convergence speed at compression stage. When loading checkpoints, aside from resetting the compression errors as 1-bit Adam, we additionally need to reset the local step buffer. Since the local step buffer can potentially fail to capture the training dynamics if the checkpoints are loaded by different number of nodes (GPUs).

For data downloading and pre-processing, please refer to the BERT Pre-training tutorial.

We provide example scripts under DeepSpeedExamples/bing_bert/01_adam/. There are 3 sets of scripts corresponding to NCCL-based implementation, MPI-based implementation on Ethernet systems, and MPI-based implementation on InfiniBand systems. For MPI-based implementation, we provide both example scripts when launching with deepspeed or mpirun.

The deepspeed_bsz4k_01adam_config_seq128_*.json and deepspeed_bsz4k_01adam_config_seq512_*.json files give the user the ability to specify DeepSpeed options in terms of batch size, micro batch size, optimizer, learning rate, and other parameters. In these files we include the tuned hyperparameters to reproduce experiments in our paper.

Performance results can be seen in our paper.

We additionally provide the fine-tuning scripts for BERT pre-training checkpoints over GLUE tasks. The scripts are available at DeepSpeedExamples/BingBertGlue. The glue_bert_base.json and glue_bert_large.json files give the user the ability to specify DeepSpeed options/parameters like micro batch size over BERT-base and BERT-large checkpoints, respectively. Currently we use Adam as the default optimizer for GLUE fine-tuning since the fine-tuning tasks usually use small batch size (~32) and do not require large-scale systems. run_glue_bert_base_finetune.sh and run_glue_bert_large_finetune.sh give the scripts for launching fine-tuning tasks, where we can modify variables like task name, number of epochs, model, etc. Note that to launch the fine-tuning, we must specify the path for checkpoint, for instance,

Specific GLUE scores and hyperparameters for 0/1 Adam are included in our paper Table 1.

Updated: November 5, 2025

**Examples:**

Example 1 (unknown):
```unknown
git clone https://github.com/deepspeedai/DeepSpeed
cd DeepSpeed
git submodule update --init --recursive
cd DeepSpeedExamples/
```

Example 2 (unknown):
```unknown
pip install deepspeed[1bit_adam]
```

Example 3 (unknown):
```unknown
deepspeed --launcher=[mvapich|openmpi] script.py
```

Example 4 (unknown):
```unknown
mpirun -np [num processes] -ppn [num GPUs on each node] -hostfile [hostfile] [MPI flags] python [training_script.py]
```

---

## Mixture of Experts for NLG models

**URL:** https://www.deepspeed.ai/tutorials/mixture-of-experts-nlg/

**Contents:**
- Mixture of Experts for NLG models
    - Contents
- 1. Installation
- 2. Training NLG+MoE models
  - 2.1. Changes to the model
  - 2.2. Pre-training the Standard MoE model
  - 2.3. Pre-training the PR-MoE model
  - 2.4. Training MoS with reduced model size

In this tutorial, we introduce how to apply DeepSpeed Mixture of Experts (MoE) to NLG models, which reduces the training cost by 5 times and reduce the MoE model size by 3 times (details in our Blog). We use the GPT-3 like models in Megatron-LM framework as the example. Before reading this tutorial, we recommend to first read the tutorials about Mixture of Experts and Megatron-LM GPT pre-training.

You would need to install DeepSpeed v0.6.0 or higher to use the MoE feature. The MoE for NLG model examples are in the Megatron-DeepSpeed repo under the MoE folder.

To apply MoE to the GPT-style model, we made several changes in Megatron framework, mostly in megatron/model/ where we add the MoE layers into the model.

We provide example training scripts under examples_deepspeed/MoE which we used to perform the experiments in our Blog. There are a few new hyperparameters for standard MoE model:

--num-experts: the number of experts per MoE layer. In our experiments we set it to 128. Larger number of experts tend to provide better convergence, but it’s a diminishing return.

--moe-expert-parallel-size: degree of the MoE expert parallelism. In other words, there will be num-experts/moe-expert-parallel-size experts on each GPU. Thus --moe-expert-parallel-size should be no more than both number of GPUs, and --num-experts.

--moe-loss-coeff: scaling coefficient for adding MoE loss to model loss. In our experiments we find that 0.01 is a good setting.

--moe-train-capacity-factor, --moe-eval-capacity-factor, --moe-min-capacity: these configs determine how many tokens can a single expert handle. Larger numbers could lead to better convergence, but would also lead to slower training since the load would be more unbalanced on different experts.

--disable-moe-token-dropping: this will completely remove the limitation of how many tokens can a single expert handle. For the same reason as above, we only recommend using this during inference/eval.

PR-MoE is a new designed MoE models, standing for Pyramid-Residual-MoE, which improves the parameter efficiency up to 3x as compared to standard MoE. Please see our Blog for more details. We provide example training scripts under examples_deepspeed/MoE. There are a few different hyperparameters for PR-MoE model compared to standard MoE:

--num-experts: Instead of providing a single number, to enable Pyramid-MoE, you need to provide a list, whose length is the same as the number of MoE layers. We suggest to use more experts in the latter stage (close to output) of the model.

--mlp-type: chosen from [standard, residual]. When it is residual, Residual-MoE is enabled.

In addition to the new hyperparameters above for standard MoE and PR-MoE, for NLG+MoE models we found that it’s helpful to lower the learning rate and increase the learning rate decay duration compared to the base dense model. Details of our tuning can be found in the example training scripts.

Regarding training data, we are not able to release our internal data but any public data for Megatron-LM pre-training can be directly used to train MoE models (with the caveat that it might not provide the exact same model quality as in our experiments). For example, we evaluated The Pile dataset (pile.eleuther.ai, github.com/EleutherAI/the-pile) for both dense and MoE models. Table 1 below shows that this public data provides similar evaluation results as our internal data.

Table 1: Zero-shot evaluation results (last six columns) for different dense and MoE NLG models. All zero-shot evaluation results use the accuracy metric.

MoS, standing for Mixture-of-Students, is a staged distillation-based technique for compressing large MoE models. MoS further reduces the model size by 12.5%, leading to up 3.7x model size reduction when combined with PR-MoE over the standard MoE. The reduced model size helps reduce the latency and cost during inference. To train an MoS model, one needs to specify a few additional parameters. We will use PR-MoE as an example:

--mos: This would enable Mixture-of-Students via knowledge distillation.

--load-teacher: This specifies the path to the teacher model checkpoint. This is a mandatory argument for using MoS and the teacher model checkpoint can be obtained by either training a standard MoE or the PR-MoE.

num-layers-teacher, --hidden-size-teacher, --hidden-size-teacher, --num-experts-teacher: In addition to the teacher model checkpoint path, we also need to specify the model architecture of the teacher model such as its number of layers, hidden dimension size, and the number of experts per MoE layer. In the case of PR-MoE, we need to also provide a list of experts for the teacher model, where we remove a few expert layers from the teacher model.

In addition to the new parameters above, we observe that using the teacher PR-MoE during the entire training process may adversely impact the final student model accuracy. In our experiments, we use a staged distillation method by stopping distillation early in the training process (e.g., after 400K steps) and perform optimization only against the standard language modeling loss for the rest of the training.

We provide example training scripts under examples_deepspeed/MoE. Details of our parameter settings can be found in the example training scripts. The performance results of MoS can be seen from our blog post and our paper.

Updated: November 5, 2025

---

## DataStates-LLM Checkpointing Engine

**URL:** https://www.deepspeed.ai/tutorials/datastates-async-checkpointing/

**Contents:**
- DataStates-LLM Checkpointing Engine
    - Contents
- Overview of DataStates-LLM
- Prerequisites
- Configuring DeepSpeed for DataStates-LLM
  - Configuration Parameters
- Implementing DataStates-LLM in Your Training Script
- Limitations and Ongoing Work
- Questions and Support

This tutorial will show how to use DataStates-LLM for asynchronous checkpointing. DataStates-LLM introduces a lazy asynchronous checkpointing mechanism tailored for LLMs, aiming to minimize I/O overhead and enhance training efficiency. This tutorial provides a guide on integrating DataStates-LLM with the DeepSpeed framework.

DataStates-LLM is designed to address the challenges of frequent checkpointing in LLM training by introducing a lazy asynchronous multi-level approach. It leverages the immutability of model parameters and optimizer states during forward and backward passes to perform non-blocking data transfers, thereby reducing interference with the training process. This method has demonstrated up to 48x faster checkpointing and 2.2x faster end-to-end training times compared to traditional approaches as outlined in DataStates-LLM: Lazy Asynchronous Checkpointing for Large Language Models.

Before integrating DataStates-LLM with DeepSpeed, ensure the following:

DeepSpeed Installation: DeepSpeed should be installed in your environment. If not, refer to the DeepSpeed Getting Started Guide for installation instructions.

DataStates-LLM Repository: Access the DataStates-LLM source code from its GitHub repository and follow the installation instructions provided therein.

To enable DataStates-LLM’s asynchronous checkpointing within DeepSpeed, please modify the deepspeed_config.json file to include specific configurations under the datastates_ckpt section. Below is an example configuration:

After enabling datastates checkpointing the deepspeed_config.json, the frequency of checkpointing can be configured by specifying the number of iterations after which the checkpoints should be captured using command-line parameter ` –save-interval`.

DataStates-LLM currently only supports the CUDA runtime on Nvidia-based GPUs.

DataStates-LLM has only been tested with ZeRO stage-1 without offloading to any other tiers.

While the checkpoint layout of datastates matches Huggingface’s safetensor format, due to pickled objects required by DeepSpeed during restart, it is not fully compatible with safetensor library yet.

DataStates-LLM does not yet support universal or elastic checkpointing.

Please use the DataStates-LLM Github repository for any questions, issues, or feature requests.

Updated: November 5, 2025

**Examples:**

Example 1 (unknown):
```unknown
{
    // ... other DeepSpeed configuration options
    "datastates_ckpt": {
        "host_cache_size": 16
    }
}
```

---

## DCGAN Tutorial

**URL:** https://www.deepspeed.ai/tutorials/gan/

**Contents:**
- DCGAN Tutorial
    - Contents
- Running Original DCGAN
- Enabling DeepSpeed
  - Argument Parsing
  - Initialization
  - Discriminator Training
  - Generator Training
  - Configuration
  - Run DCGAN Model with DeepSpeed Enabled

If you haven’t already, we advise you to first read through the Getting Started guide before stepping through this tutorial.

In this tutorial, we will port the DCGAN model to DeepSpeed using custom (user-defined) optimizers and a multi-engine setup!

Please go through the original tutorial for the Celebrities dataset first using the original code. Then run bash gan_baseline_run.sh.

The codes may be obtained here.

The first step to apply DeepSpeed is adding configuration arguments to DCGAN model, using the deepspeed.add_config_arguments() function as below.

We use deepspeed.initialize to create two model engines (one for the discriminator network and one for the generator network along with their respective optimizers) as follows:

Note that DeepSpeed automatically takes care of the distributed training aspect, so we set ngpu=0 to disable the default data parallel mode of pytorch.

We modify the backward for discriminator as follows:

which leads to the inclusion of the gradients due to both real and fake mini-batches in the optimizer update.

We modify the backward for generator as follows:

Note: In the case where we use gradient accumulation, backward on the generator would result in accumulation of gradients on the discriminator, due to the tensor dependencies as a result of errG being computed from a forward pass through the discriminator; so please set requires_grad=False for the netD parameters before doing the generator backward.

The next step to use DeepSpeed is to create a configuration JSON file (gan_deepspeed_config.json). This file provides DeepSpeed specific parameters defined by the user, e.g., batch size, optimizer, scheduler and other parameters.

To start training the DCGAN model with DeepSpeed, we execute the following command which will use all detected GPUs by default.

We use a total batch size of 64 and perform the training on 16 GPUs for 1 epoch on a DGX-2 node which leads to 3x speed-up. The summary of the results is given below:

Baseline total wall clock time for 1 epochs is 393 secs

Deepspeed total wall clock time for 1 epochs is 128 secs

Updated: November 5, 2025

**Examples:**

Example 1 (python):
```python
import deepspeed

def main():
    parser = get_argument_parser()
    parser = deepspeed.add_config_arguments(parser)
    args = parser.parse_args()
    train(args)
```

Example 2 (unknown):
```unknown
model_engineD, optimizerD, _, _ = deepspeed.initialize(args=args, model=netD, model_parameters=netD.parameters(), optimizer=optimizerD)
    model_engineG, optimizerG, _, _ = deepspeed.initialize(args=args, model=netG, model_parameters=netG.parameters(), optimizer=optimizerG)
```

Example 3 (unknown):
```unknown
model_engineD.backward(errD_real)
model_engineD.backward(errD_fake)
```

Example 4 (unknown):
```unknown
model_engineG.backward(errG)
```

---

## Getting Started with DeepSpeed for Inferencing Transformer based Models

**URL:** https://www.deepspeed.ai/tutorials/inference-tutorial/

**Contents:**
- Getting Started with DeepSpeed for Inferencing Transformer based Models
    - Contents
- Initializing for Inference
- Loading Checkpoints
- Launching
- End-to-End GPT NEO 2.7B Inference
- Datatypes and Quantized Models

DeepSpeed-Inference v2 is here and it’s called DeepSpeed-FastGen! For the best performance, latest features, and newest model support please see our DeepSpeed-FastGen release blog!

DeepSpeed-Inference introduces several features to efficiently serve transformer-based PyTorch models. It supports model parallelism (MP) to fit large models that would otherwise not fit in GPU memory. Even for smaller models, MP can be used to reduce latency for inference. To further reduce latency and cost, we introduce inference-customized kernels. Finally, we propose a novel approach to quantize models, called MoQ, to both shrink the model and reduce the inference cost at production. For more details on the inference related optimizations in DeepSpeed, please refer to our blog post.

DeepSpeed provides a seamless inference mode for compatible transformer based models trained using DeepSpeed, Megatron, and HuggingFace, meaning that we don’t require any change on the modeling side such as exporting the model or creating a different checkpoint from your trained checkpoints. To run inference on multi-GPU for compatible models, provide the model parallelism degree and the checkpoint information or the model which is already loaded from a checkpoint, and DeepSpeed will do the rest. It will automatically partition the model as necessary, inject compatible high performance kernels into your model and manage the inter-gpu communication. For list of compatible models please see here.

For inference with DeepSpeed, use init_inference API to load the model for inference. Here, you can specify the MP degree, and if the model has not been loaded with the appropriate checkpoint, you can also provide the checkpoint description using a json file or the checkpoint path.

To inject the high-performance kernels, you need to set the replace_with_kernel_inject to True for the compatible models. For models not supported by DeepSpeed, the users can submit a PR that defines a new policy in replace_policy class that specifies the different parameters of a Transformer layer, such as attention and feed-forward parts. The policy classes in DeepSpeed create a mapping between the parameters of the original user-supplied layer implementation with DeepSpeed’s inference-optimized Transformer layer.

To run inference with only model-parallelism for the models that we don’t support kernels, you can pass an injection policy that shows the two specific linear layers on a Transformer Encoder/Decoder layer: 1) the attention output GeMM and 2) layer output GeMM. We need these part of the layer to add the required all-reduce communication between GPUs to merge the partial results across model-parallel ranks. Below, we bring an example that shows how you can use deepspeed-inference with a T5 model:

For the models trained using HuggingFace, the model checkpoint can be pre-loaded using the from_pretrained API as shown above. For Megatron-LM models trained with model parallelism, we require a list of all the model parallel checkpoints passed in JSON config. Below we show how to load a Megatron-LM checkpoint trained using MP=2.

For models that are trained with DeepSpeed, the checkpoint json file only requires storing the path to the model checkpoints.

DeepSpeed supports running different MP degree for inference than from training. For example, a model trained without any MP can be run with MP=2, or a model trained with MP=4 can be inferenced without any MP. DeepSpeed automatically merges or splits checkpoints during initialization as necessary.

Use the DeepSpeed launcher deepspeed to launch inference on multiple GPUs:

DeepSpeed inference can be used in conjunction with HuggingFace pipeline. Below is the end-to-end client code combining DeepSpeed inference with HuggingFace pipeline for generating text using the GPT-NEO-2.7B model.

The above script modifies the model in HuggingFace text-generation pipeline to use DeepSpeed inference. Note that here we can run the inference on multiple GPUs using the model-parallel tensor-slicing across GPUs even though the original model was trained without any model parallelism and the checkpoint is also a single GPU checkpoint. To run the client simply run:

Below is an output of the generated text. You can try other prompt and see how this model generates text.

DeepSpeed inference supports fp32, fp16 and int8 parameters. The appropriate datatype can be set using dtype in init_inference, and DeepSpeed will choose the kernels optimized for that datatype. For quantized int8 models, if the model was quantized using DeepSpeed’s quantization approach (MoQ), the setting by which the quantization is applied needs to be passed to init_inference. This setting includes the number of groups used for quantization and whether the MLP part of transformer is quantized with extra grouping. For more information on these parameters, please visit our quantization tutorial.

Congratulations! You have completed DeepSpeed inference Tutorial.

Updated: November 5, 2025

**Examples:**

Example 1 (unknown):
```unknown
# create the model
if args.pre_load_checkpoint:
    model = model_class.from_pretrained(args.model_name_or_path)
else:
    model = model_class()

# create the tokenizer
tokenizer = model_class.from_pretrained(args.model_name_or_path)
...

import deepspeed

# Initialize the DeepSpeed-Inference engine
ds_engine = deepspeed.init_inference(model,
                                     tensor_parallel={"tp_size": world_size},
                                     dtype=torch.half,
                                     checkpoint=None if args.pre_load_checkpoint else args.checkpoint_json,
                                     replace_with_kernel_inject=True)
model = ds_engine.module
pipe = pipeline("text-generation", model=model, tokenizer=tokenizer)
output = pipe('Input String')
```

Example 2 (python):
```python
# create the model
import transformers
from transformers.models.t5.modeling_t5 import T5Block

import deepspeed

pipe = pipeline("text2text-generation", model="google/t5-v1_1-small", device=local_rank)
# Initialize the DeepSpeed-Inference engine
pipe.model = deepspeed.init_inference(
    pipe.model,
    tensor_parallel={"tp_size": world_size},
    dtype=torch.float,
    injection_policy={T5Block: ('SelfAttention.o', 'EncDecAttention.o', 'DenseReluDense.wo')}
)
output = pipe('Input String')
```

Example 3 (unknown):
```unknown
"checkpoint.json":
{
    "type": "Megatron",
    "version": 0.0,
    "checkpoints": [
        "mp_rank_00/model_optim_rng.pt",
        "mp_rank_01/model_optim_rng.pt",
    ],
}
```

Example 4 (unknown):
```unknown
"checkpoint.json":
{
    "type": "ds_model",
    "version": 0.0,
    "checkpoints": "path_to_checkpoints",
}
```

---

## Maximizing Communication Efficiency for Large-scale Training via 0/1 Adam

**URL:** https://www.deepspeed.ai/tutorials/zero-one-adam/

**Contents:**
- Maximizing Communication Efficiency for Large-scale Training via 0/1 Adam
    - Contents
- 1. Overview
  - 1.1 Pre-requisites for installing DeepSpeed
  - 1.2 Pre-requisites for 0/1 Adam
    - 1.2.1 NCCL-based implementation
    - 1.2.2 MPI-based implementation
    - 1.2.3 Compressed implementation
  - 1.3 0/1 Adam Algorithm
  - 1.4 Configuration of 0/1 Adam

Watch out! 1) The NCCL-based implementation requires PyTorch >= 1.8 (and NCCL >= 2.8.3 when you have 64 or more GPUs). See details below. 2) Although 0/1 Adam is compatible with both FP16 and FP32, currently we only verified the convergence under mixed precision/FP16 training. 3) Currently the MPI-based implementation is not compatible with pipeline parallelism. 4) Frequent checkpoint loading could hurt 0/1 Adam’s convergence. See details below.

In this tutorial, we introduce DeepSpeed’s 0/1 Adam optimizer, which can improve model training speed on communication-constrained clusters, especially for communication-intensive large models. For instance, it is able to reduce the overall communication volume on BERT-large pre-training by up to 26x without affecting the end-to-end model accuracy. Compared to the 1-bit Adam optimizer, 0/1 Adam provides a more flexible way of using compressed communication via adaptive variance state freezing. Additionally, it allows the computing nodes to skip communication rounds during training using a technique called 1-bit sync, without compromising the convergence speed. We have a paper which provides the technical details including algorithm, system implementation, and evaluations.

To illustrate the benefits and usage of 0/1 Adam optimizer, we use the BERT Pre-training task as example. For more details on this task, please refer to the tutorial.

If you don’t already have a copy of the DeepSpeed repository, please clone it now and checkout the DeepSpeedExamples submodule that contains the BERT Pre-training example.

In DeepSpeed, we introduce a system implementation for compressed communication using the NCCL backend of PyTorch distributed. This implementation provides better performance and usability than the MPI-based implementation below. Thus we highly recommend users to choose this implementation.

Watch out! This NCCL-based implementation requires PyTorch >= 1.8. It also requires NCCL >= 2.8.3 when you have 64 or more GPUs to avoid certain NCCL runtime bugs. Currently (2021/03/16) NCCL 2.8.3 is not officially supported by PyTorch. The solution we used is by hacking in NCCL 2.8.3 via LD_PRELOAD: 1) Install NCCL 2.8.3. This works for us on a CUDA 11 system: apt-get install -y libnccl2=2.8.3-1+cuda11.0 libnccl-dev=2.8.3-1+cuda11.0. 2) Set LD_PRELOAD to the library path. This works for us: LD_PRELOAD=/usr/lib/x86_64-linux-gnu/libnccl.so.2.8.3. To confirm LD_PRELOAD is working you can see the version it uses in the NCCL logs if you have NCCL_DEBUG=INFO, it should say: NCCL version 2.8.3+cuda11.0.

For this implementation, we rely on Message Passing Interface (MPI) for advanced communication primitives.

We package the necessary dependencies in the DeepSpeed docker images. However, if you are using a different build system, please install MPI and mpi4py on your system. To install the prerequisites run:

We have tested CUDA-Aware MPI communication using the MVAPICH2-GDR library. However, any CUDA-Aware communication library including OpenMPI should work fine with these examples.

An example launch command for 0/1 Adam using the deepspeed launcher is as follows:

Please note that for MPI-based implementation of 0/1 Adam, the --launcher=[mvapich|openmpi] flag is required when using the deepspeed launcher.

Alternatively, the standard mpirun launcher can also be used as follows:

This backend provides an approach to abstract the generic part of one-bit optimizers and implements accelerator dependent part with DeepSpeed custom op builder. To use this CompressedBackend, you should make sure that your current accelerator supports PackbitsBuilder, so that it could be loaded to do high performance packing and unpacking between float and Byte datatype, which is utilized in one-bit algorithm. An example can be found in Deepspeed/op_builder/xpu/packbits.py. This approach does not require NCCL or MPI based communication library. It will automatically use your default communication library selected by your accelerator in deepspeed/comm.

The detailed description of the 0/1 Adam algorithm can be seen from our paper.

The 0/1 Adam feature can be used by setting the optimizer configuration options as follows. An example json config file is shown below.

Please note the new parameters var_freeze_step, var_update_scaler, local_step_scaler, local_step_clipper, cuda_aware and comm_backend_name that have been added to support the 0/1 Adam feature:

var_update_scaler is the interval to update the variance. Note that the update policy for variance follows an exponential rule. Formally, if we denote $k_j$ as the step where $j$-th variance update takes place, then it follows that $k_{j+1} - k_j = 2\cdot\exp{\lfloor j/\kappa\rfloor}$ (please refer to the 0/1 Adam paper for detailed explanation), and the var_update_scaler denotes the $\kappa$ factor in such expression. In practice, we found its default value (16) is able to work well on most of the tasks, including BERT-Base/Large pretraining, GPT pretraining, and ImageNet training.

local_step_scaler and local_step_clipper are two hyperparameters for learning rate based local step policy in 0/1 Adam. Formally, if we denote $k_j$ as the step where $j$-th synchronization takes place among all the workers, then it follows that $k_{j+1} - k_j = 2\cdot\exp{\min(\lfloor j/\alpha\rfloor, \beta )}$ (please refer to the 0/1 Adam paper for detailed explanation). Following such notations, local_step_scaler and local_step_clipper denote the $\alpha$ and $\beta$, respectively. Informally, local_step_scaler decides the frequency of synchronization while local_step_clipper denotes the maximal local step interval 0/1 Adam can use. The learning rate policy is the default policy used in 0/1 Adam, and the value of local_step_scaler can be pre-calculated (see 0/1 Adam paper Section 6). We can also trivially construct other policies by setting these two hyperparameters such as constant local step interval policy by setting local_step_scaler=1 and local_step_clipper=constant.

cuda_aware is used for MPI-based implementation to indicate that the underlying MPI library supports CUDA-Aware communication. This feature is only supported on systems with InfiniBand interconnect and a CUDA-Aware MPI library like MVAPICH2-GDR or OpenMPI built with CUDA-Aware support. Setting cuda_aware to False will allow training on Ethernet based systems. However, the communication will happen using sender as well as receiver side memory copies between CPU and GPU buffers before and after communication.

comm_backend_name is used to indicate which backend implementation to use. You can choose between NCCL, MPI-based and compressed implementations by setting comm_backend_name to “nccl”, “mpi” or “compressed”. When using NCCL-based implementation, there is no need to set cuda_aware.

Because 1-bit compression cannot represent exact zero, the compression error would keep accumulating in the momentum if a parameter have constant zero gradients during training. For example, for BERT pre-training seq length 128, bert.embeddings.position_embeddings.weight has constant zeros in its gradient and momentum for row 129 to 512, because it only learns up to seq length 128 while the model supports up to seq length 512. Thus in 0/1 Adam we added support of a momentum mask for users to specify those params that have constant exact zeros in their gradients. See example script for how to configure this momentum mask. One thing to note is that we don’t use momentum mask saved in checkpoints since this mask could change during training (e.g., BERT seqlen 128 and 512 require different masks). So you have to provide this mask every time in your training script.

Watch out! 0/1 Adam relies on an compression error compensation mechanism to maintain the convergence speed at compression stage. When loading checkpoints, aside from resetting the compression errors as 1-bit Adam, we additionally need to reset the local step buffer. Since the local step buffer can potentially fail to capture the training dynamics if the checkpoints are loaded by different number of nodes (GPUs).

For data downloading and pre-processing, please refer to the BERT Pre-training tutorial.

We provide example scripts under DeepSpeedExamples/bing_bert/01_adam/. There are 3 sets of scripts corresponding to NCCL-based implementation, MPI-based implementation on Ethernet systems, and MPI-based implementation on InfiniBand systems. For MPI-based implementation, we provide both example scripts when launching with deepspeed or mpirun.

The deepspeed_bsz4k_01adam_config_seq128_*.json and deepspeed_bsz4k_01adam_config_seq512_*.json files give the user the ability to specify DeepSpeed options in terms of batch size, micro batch size, optimizer, learning rate, and other parameters. In these files we include the tuned hyperparameters to reproduce experiments in our paper.

Performance results can be seen in our paper.

We additionally provide the fine-tuning scripts for BERT pre-training checkpoints over GLUE tasks. The scripts are available at DeepSpeedExamples/BingBertGlue. The glue_bert_base.json and glue_bert_large.json files give the user the ability to specify DeepSpeed options/parameters like micro batch size over BERT-base and BERT-large checkpoints, respectively. Currently we use Adam as the default optimizer for GLUE fine-tuning since the fine-tuning tasks usually use small batch size (~32) and do not require large-scale systems. run_glue_bert_base_finetune.sh and run_glue_bert_large_finetune.sh give the scripts for launching fine-tuning tasks, where we can modify variables like task name, number of epochs, model, etc. Note that to launch the fine-tuning, we must specify the path for checkpoint, for instance,

Specific GLUE scores and hyperparameters for 0/1 Adam are included in our paper Table 1.

Updated: November 5, 2025

**Examples:**

Example 1 (unknown):
```unknown
git clone https://github.com/deepspeedai/DeepSpeed
cd DeepSpeed
git submodule update --init --recursive
cd DeepSpeedExamples/
```

Example 2 (unknown):
```unknown
pip install deepspeed[1bit_adam]
```

Example 3 (unknown):
```unknown
deepspeed --launcher=[mvapich|openmpi] script.py
```

Example 4 (unknown):
```unknown
mpirun -np [num processes] -ppn [num GPUs on each node] -hostfile [hostfile] [MPI flags] python [training_script.py]
```

---

## 1-Cycle Schedule

**URL:** https://www.deepspeed.ai/tutorials/one-cycle

**Contents:**
- 1-Cycle Schedule
    - Contents
- 1-Cycle Schedule
- Prerequisites
- Overview
  - 1-Cycle Parameters
- Required Model Configuration Changes
  - PyTorch model
- Batch Scaling Example

This tutorial shows how to implement 1Cycle schedules for learning rate and momentum in PyTorch.

Recent research has demonstrated that the slow convergence problems of large batch size training can be addressed by tuning critical hyperparameters such as learning rate and momentum, during training using cyclic and decay schedules. In DeepSpeed, we have implemented a state-of-the-art schedule called 1-Cycle to help data scientists effectively use larger batch sizes to train their models in PyTorch.

To use 1-cycle schedule for model training, you should satisfy these two requirements:

The 1-cycle schedule operates in two phases, a cycle phase and a decay phase which span one iteration over the training data. For concreteness, we will review how the 1-cycle learning rate schedule works. In the cycle phase, the learning rate oscillates between a minimum value and a maximum value over a number of training steps. In the decay phase, the learning rate decays starting from the minimum value of the cycle phase. An example of 1-cycle learning rate schedule during model training is illustrated below.

The 1-Cycle schedule is defined by a number of parameters which allow users to explore different configurations. The literature recommends concurrent tuning of learning rate and momentum because they are correlated hyperparameters. We have leveraged this recommendation to reduce configuration burden by organizing the 1-cycle parameters into two groups:

The global parameters for configuring the 1-cycle phases are:

The local parameters for the hyperparameters are:

Although appropriate values cycle_min_lr and cycle_max_lr values can be selected based on experience or expertise, we recommend using learning rate range test feature of DeepSpeed to configure them.

To illustrate the required model configuration changes to use 1-Cycle schedule in model training, we will use a schedule with the following properties:

Note that these parameters are processed by DeepSpeed as session parameters, and so should be added to the appropriate section of the model configuration.

PyTorch versions 1.0.1 and newer provide a feature for implementing schedulers for hyper-parameters, called learning rate schedulers. We have implemented 1-Cycle schedule using this feature. You will add a scheduler entry of type “OneCycle” as illustrated below.

As example of how 1-Cycle schedule can enable effective batch scaling, we briefly share our experience with an internal model in Microsoft. In this case, the model was well-tuned for fast convergence (in data samples) on a single GPU, but was converging slowly to target performance (AUC) when training on 8 GPUs (8X batch size). The plot below shows model convergence with 8 GPUs for these learning rate schedules:

With 1Cycle, the model converges faster than the other schedules to the target AUC . In fact, 1Cycle converges as fast as the optimal 1-GPU training (not shown). For Fixed, convergence is about 5X slower (needs 5X more data samples). With LinearScale, the model diverges because the learning rate is too high. The plot below illustrates the schedules by reporting the learning rate values during 8-GPU training.

We see that the learning rate for 1Cycle is always larger than Fixed and is briefly larger than LinearScale to achieve faster convergence. Also 1Cycle lowers the learning rate later during training to avoid model divergence, in contrast to LinearScale. In summary, by configuring an appropriate 1-Cycle schedule we were able to effective scale the training batch size for this model by 8X without loss of convergence speed.

Updated: November 5, 2025

**Examples:**

Example 1 (unknown):
```unknown
"scheduler": {
    "type": "OneCycle",
    "params": {
        "cycle_first_step_size": 1000,
        "cycle_first_stair_count": 500,
        "cycle_second_step_size": 1000,
        "cycle_second_stair_count": 500,
        "decay_step_size": 1000,
        "cycle_min_lr": 0.0001,
        "cycle_max_lr": 0.0010,
        "decay_lr_rate": 0.001,
        "cycle_min_mom": 0.85,
        "cycle_max_mom": 0.99,
        "decay_mom_rate": 0.0
    }
},
```

---

## Pipeline Parallelism

**URL:** https://www.deepspeed.ai/tutorials/pipeline

**Contents:**
- Pipeline Parallelism
    - Contents
- Getting Starting with Pipeline Parallelism
  - Expressing Pipeline Models
  - AlexNet
  - Inputs and Outputs
  - Training Loops
  - Dealing with Data
- Advanced Topics
  - Load Balancing Pipeline Modules

DeepSpeed v0.3 includes new support for pipeline parallelism! Pipeline parallelism improves both the memory and compute efficiency of deep learning training by partitioning the layers of a model into stages that can be processed in parallel. DeepSpeed’s training engine provides hybrid data and pipeline parallelism and can be further combined with model parallelism such as Megatron-LM. An illustration of 3D parallelism is shown below. Our latest results demonstrate that this 3D parallelism enables training models with over a trillion parameters.

DeepSpeed uses gradient accumulation to extract pipeline parallelism (shown below). Each batch of training data is divided into micro-batches that can be processed in parallel by the pipeline stages. Once a stage completes the forward pass for a micro-batch, the activation memory is communicated to the next stage in the pipeline. Similarly, as the next stage completes its backward pass on a micro-batch, the gradient with respect to the activation is communicated backwards through the pipeline. Each backward pass accumulates gradients locally. Next, all data parallel groups perform reductions of the gradients in parallel. Lastly, the optimizer updates the model weights.

Below is an illustration of how DeepSpeed will train a batch with eight micro-batches using hybrid two-way data parallelism and two-stage pipeline parallelism. GPUs 0 and 2 are arranged in a pipeline and will alternate forward (F) and backward (B) passes. They will then all-reduce (AR) gradients with their data parallel counterparts, GPUs 1 and 3, respectively. Finally, the two pipeline stages update their model weights.

DeepSpeed strives to accelerate and simplify the process of pipeline parallel training. This section provides first steps with hybrid data and pipeline parallel training by preparing torchvision’s AlexNet model.

Pipeline parallelism requires models to be expressed as a sequence of layers. In the forward pass, each layer consumes the output of the previous layer. In fact, there is no need to specify a forward() for a pipeline parallel model! The forward pass of a pipeline parallel model implicitly takes the form:

PyTorch’s torch.nn.Sequential is a convenient container for expressing pipeline parallel models and can be parallelized by DeepSpeed with no modification:

PipelineModule uses its layers argument as the sequence of layers that comprise the model. After initialization, net is divided into two pipeline stages and its layers moved to the corresponding GPUs. If more than two GPUs are present, DeepSpeed will also use hybrid data parallelism.

Note: The total number of GPUs must be divisible by the number of pipeline stages.

Note: For large model training, see memory-efficient model construction.

Let’s look at an abbreviated implementation of torchvision’s AlexNet:

AlexNet is mostly a composition of several Sequential submodules. We can turn this into a PipelineModule by flattening its submodules into a single sequence of layers:

Note: the lambda in the middle of layers above is not a torch.nn.Module type. Any object that implements __call__() can be a layer in a PipelineModule: this allows for convenient data transformations in the pipeline.

Following torch.nn.Sequential, the inputs and outputs of each layer must be either a single torch.Tensor or a tuple of tensors. In practice, some models may need to modify their forward pass to pack and unpack arguments to forward(). Consider an abbreviated implementation of a stack of Transformer blocks:

Two modifications to TransformerBlock are required:

These modifications can be accomplished with a short subclass:

Pipeline parallelism interleaves forward and backward passes, and thus the training loop cannot be divided into separate stages of forward(), backward() and step(). Instead, DeepSpeed’s pipeline engine provides a train_batch() method that advances the pipeline engine until the next batch of training data is consumed and the model weights updated.

The above train_batch() example is equivalent to the following with traditional data parallel DeepSpeed:

Data parallel training typically has each worker perform IO independently at the start of each batch. However, in a pipeline parallel environment, only the first stage uses the input data, and only the last stage uses labels for loss calculation.

Note: The pipeline engine expects data loaders to return a tuple of two items. The first returned item is the input batch data, and the second item is the data to be used in the loss calculation. As before, inputs and labels should be either torch.Tensor type or a tuple of tensors.

For convenience, the DeepSpeed pipeline engine can construct a distributed data loader when a dataset is provided to deepspeed.initialize(). DeepSpeed handles the rest of the complexity of data loading, and so the pipeline training loop becomes:

Of course, DeepSpeed will work with any data loader that you wish to use. Data loaders should be constructed by the first and last stages in the pipeline. Each worker should load micro-batches of size engine.train_micro_batch_size_per_gpu() and will be queried a total of engine.gradient_accumulation_steps() times per train_batch().

Watch out! The pipeline engine pulls data from an iterator instead of iterating over it. It’s critical that the data stream does not empty in the middle of a training batch. Each invocation of train_batch() will pull a total of engine.gradient_accumulation_steps() micro-batches of data from the data iterator.

DeepSpeed provides a convenience class deepspeed.utils.RepeatingLoader that simply wraps an iterable such as a data loader and restarts it whenever the end is reached:

The performance of pipeline parallel training strongly relies on load balance. DeepSpeed provides several mechanisms for partitioning the model across GPUs. These strategies can be set with the partition_method keyword argument to PipelineModule. Here are partitioning methods currently provided by DeepSpeed:

Building a Sequential container and providing it to a PipelineModule is a convenient way of specifying a pipeline parallel model. However, this approach encounters scalability issues for massive models because each worker replicates the whole model in CPU memory. For example, a machine with 16 GPUs must have as much local CPU memory as 16 times the model size.

DeepSpeed provides a LayerSpec class that delays the construction of modules until the model layers have been partitioned across workers. Then each worker will allocate only the layers it’s assigned to. So, comparing to the example from the previous paragraph, using LayerSpec a machine with 16 GPUs will need to allocate a total of 1x model size on its CPU memory and not 16x.

Here is an example of the abbreviated AlexNet model, but expressed only with LayerSpecs. Note that the syntax is almost unchanged: nn.ReLU(inplace=True) simply becomes LayerSpec(nn.ReLU, inplace=True).

Some models cannot be entirely expressed as pipeline parallel models because some layers are reused in the pipeline. For example, Transformer based language models commonly use an embedding layer early in the pipeline to map vocabulary to hidden states, and then use the embedding to map hidden states back to vocabulary at the end of the pipeline. If the model was restricted to pure pipeline parallelism, this embedding reuse would prohibit pipeline parallelism.

DeepSpeed provides a TiedLayerSpec that is an extension of LayerSpec. TiedLayerSpec requires an additional argument: key. Each reuse of a layer is specified with a TiedLayerSpec, and the key field is used to identify where a layer is reused.

Tied layers are replicated on every pipeline stage that owns an instance of reuse. Training then proceeds as normal, but an additional all-reduce of the tied gradients is added after all backward passes complete. The all-reduce ensures that the weights of the tied layer remain in sync across pipeline stages.

Updated: November 5, 2025

**Examples:**

Example 1 (python):
```python
def forward(self, inputs):
    x = inputs
    for layer in self.layers:
        x = layer(x)
    return x
```

Example 2 (python):
```python
net = nn.Sequential(
    nn.Linear(in_features, hidden_dim),
    nn.ReLU(inplace=True),
    nn.Linear(hidden_dim, out_features)
)
from deepspeed.pipe import PipelineModule
net = PipelineModule(layers=net, num_stages=2)
```

Example 3 (python):
```python
class AlexNet(nn.Module):
    def __init__(self, num_classes=1000):
        super(AlexNet, self).__init__()
        self.features = nn.Sequential(
            nn.Conv2d(3, 64, kernel_size=11, stride=4, padding=2),
            ...
            nn.MaxPool2d(kernel_size=3, stride=2),
        )
        self.avgpool = nn.AdaptiveAvgPool2d((6, 6))
        self.classifier = nn.Sequential(
            nn.Dropout(),
            ...
            nn.Linear(4096, num_classes),
        )

    def forward(self, x):
        x = self.features(x)
        x = self.avgpool(x)
        x = torch.flatten(x, 1)
        x = self.classifier(x)
        return x
```

Example 4 (python):
```python
class AlexNetPipe(AlexNet):
    def to_layers(self):
        layers = [
            *self.features,
            self.avgpool,
            lambda x: torch.flatten(x, 1),
            *self.classifier
        ]
        return layers

from deepspeed.pipe import PipelineModule
net = AlexNetPipe()
net = PipelineModule(layers=net.to_layers(), num_stages=2)
```

---

## Communication Logging

**URL:** https://www.deepspeed.ai/tutorials/comms-logging/

**Contents:**
- Communication Logging
    - Contents
- Overview
- Usage
  - Configuration Setup
  - Verbose Logging
  - Log Summaries

In this tutorial, we introduce DeepSpeed communication logging and provide examples of its usage.

NOTE: All logging communication calls are synchronized in order to provide accurate timing information. This may hamper performance if your model heavily uses asynchronous communication operations.

Logging communication calls is vital to ensure networking resources are fully utilized. The DeepSpeed communication logger enables the detection and logging of all communication operations launched under deepspeed.comm. Each communication operation can all be directly printed to the console immediately after completion (via the verbose config option), or a summary may be printed with a call to deepspeed.comm.log_summary() or deepspeed.com.log_summary(show_straggler=True) in the client code at the completion of training, an epoch, after N training iterations, etc.

Communication logging in DeepSpeed is configured within the deepspeed configuration file. DeepSpeed will automatically log communication either all operations (prof_all), or user-specified operations (prof_ops).

Communication logging can be configured in the DeepSpeed configuration file. Communication logging can be enabled by adding the following field to DeepSpeed’s configuration json file. Refer to Communication Logging for details.

There are currently two ways to view communication log records:

If the enabled configuration option is selected, all communication operations will be immediately printed to the console. This mode is intended for detailed debugging, and is not recommended for most users. The following is an example snippet of verbose output:

For advanced users, the debug option will append the calling function of each communication operation to that operation’s log_name. See Log Summaries for an example of a deepspeed.comm.log_summary() call with debug enabled.

It’s recommended that users add a call to deepspeed.comm.log_summary() at training milestones (e.g. every epoch or N iterations). This enables high-level communication logging without having to sift through logs from verbose.

The steps to add DeepSpeed communication log summaries are as follows:

For example usage, see the following modified DeepSpeedExamples/cifar example:

The following is a truncated example output of deepspeed.comm.log_summary() at the end of 10 iterations of Megatron-DeepSpeed with ZeRO-3:

And the following is a call to deepspeed.comm.log_summary under the same configuration with debug enabled:

Straggler effect can be shown by supplying optional argument show_straggler=True to deepspeed.comm.log_summary() call. Straggler effect is defined as the time a rank waits for the slowest rank to start communication. For each collective, log_summary would get the minimum collective time among all ranks, compute straggler effect as follows:

Print straggler effect with the following log_summary call in the example above:

Updated: November 5, 2025

**Examples:**

Example 1 (unknown):
```unknown
"comms_logger": {
  "enabled": true,
  "verbose": false,
  "prof_all": true,
  "debug": false
}
```

Example 2 (unknown):
```unknown
[2022-06-26 01:39:55,722] [INFO] [logging.py:69:log_dist] [Rank 0] rank=0 | comm op: reduce_scatter_tensor | time (ms): 9.46 | msg size: 678.86 MB | algbw (Gbps): 1204.52  | busbw (Gbps): 1129.23
[2022-06-26 01:39:56,470] [INFO] [logging.py:69:log_dist] [Rank 0] rank=0 | comm op: all_gather_into_tensor | time (ms): 0.11 | msg size: 6.0 MB | algbw (Gbps): 954.41  | busbw (Gbps): 894.76
[2022-06-26 01:39:56,471] [INFO] [logging.py:69:log_dist] [Rank 0] rank=0 | comm op: all_gather_into_tensor | time (ms): 0.08 | msg size: 6.0 MB | algbw (Gbps): 1293.47  | busbw (Gbps): 1212.63
```

Example 3 (unknown):
```unknown
# Step 2: (Optional) Import deepspeed.comm
import deepspeed.comm as dist

# Note that any communication operations using `import torch.distributed as dist` calls can remain unchanged, and will be automatically logged under deepspeed.comm!
dist.all_reduce(tensor)

for epoch in range(2):

    running_loss = 0.0
    for i, data in enumerate(trainloader):
        pre = time.time()
        inputs, labels = data[0].to(model_engine.local_rank), data[1].to(
            model_engine.local_rank)
        if fp16:
            inputs = inputs.half()
        outputs = model_engine(inputs)
        loss = criterion(outputs, labels)

        model_engine.backward(loss)
        model_engine.step()
        post = time.time()
    # Step 3: Call `deepspeed.comm.log_summary()`
    dist.log_summary()
```

Example 4 (unknown):
```unknown
Comm. Op            Message Size        Count               Total Latency(ms)   Avg Latency(ms)     tput_avg (Gbps)     busbw_avg (Gbps)
broadcast
                    2.0 KB              146                 11.12               0.08                0.43                0.41
                    98.25 MB            1                   8317.12             8317.12             0.20                0.19
reduce_scatter_tensor
                    678.86 MB           40                  602.29              9.69                1468.06             1376.31
```

---

## CIFAR-10 Tutorial

**URL:** https://www.deepspeed.ai/tutorials/cifar-10/

**Contents:**
- CIFAR-10 Tutorial
    - Contents
- Running Original CIFAR-10
- Enabling DeepSpeed
  - Argument Parsing
  - Initialization
  - Training API
  - Configuration
  - Run CIFAR-10 Model with DeepSpeed Enabled

If you haven’t already, we advise you to first read through the Getting Started guide before stepping through this tutorial.

In this tutorial we will be adding DeepSpeed to the CIFAR-10 model, which is a small image classification model.

First we will go over how to run the original CIFAR-10 model. Then we will proceed step-by-step in enabling this model to run with DeepSpeed.

Original model code from the CIFAR-10 Tutorial, We’ve copied this repo under DeepSpeedExamples/training/cifar/ and made it available as a submodule. To download, execute:

To install the requirements for the CIFAR-10 model:

Run python cifar10_tutorial.py, it downloads the training data set at first run.

The first step to apply DeepSpeed is adding DeepSpeed arguments to CIFAR-10 model, using deepspeed.add_config_arguments() function as below.

We create model_engine, optimizer and trainloader with the help of deepspeed.initialize, which is defined as following:

Here we initialize DeepSpeed with the CIFAR-10 model (net), args, parameters and trainset:

After initializing DeepSpeed, the original device and optimizer are removed:

The model returned by deepspeed.initialize is the DeepSpeed Model Engine that we will use to train the model using the forward, backward and step API.

Zeroing the gradients is handled automatically by DeepSpeed after the weights have been updated using a mini-batch.

The next step to use DeepSpeed is to create a configuration JSON file (ds_config.json). This file provides DeepSpeed specific parameters defined by the user, e.g., batch size, optimizer, scheduler and other parameters.

To start training the CIFAR-10 model with DeepSpeed applied, execute the following command, it will use all detected GPUs by default.

DeepSpeed usually prints more training details for the user to monitor, including training settings, performance statistics and loss trends.

Updated: November 5, 2025

**Examples:**

Example 1 (unknown):
```unknown
git submodule update --init --recursive
```

Example 2 (unknown):
```unknown
cd DeepSpeedExamples/cifar
pip install -r requirements.txt
```

Example 3 (unknown):
```unknown
Downloading https://www.cs.toronto.edu/~kriz/cifar-10-python.tar.gz to ./data/cifar-10-python.tar.gz
170500096it [00:02, 61124868.24it/s]
Extracting ./data/cifar-10-python.tar.gz to ./data
Files already downloaded and verified
  cat  frog  frog  frog
[1,  2000] loss: 2.170
[1,  4000] loss: 1.879
[1,  6000] loss: 1.690
[1,  8000] loss: 1.591
[1, 10000] loss: 1.545
[1, 12000] loss: 1.467
[2,  2000] loss: 1.377
[2,  4000] loss: 1.374
[2,  6000] loss: 1.363
[2,  8000] loss: 1.322
[2, 10000] loss: 1.295
[2, 12000] loss: 1.287
Finished Training
GroundTruth:    cat  ship  ship plane
Predicted:    cat  ship plane plane
Accuracy of the network on the 10000 test images: 53 %
Accuracy of plane : 69 %
Accuracy of   car : 59 %
Accuracy of  bird : 56 %
Accuracy of   cat : 36 %
Accuracy of  deer : 37 %
Accuracy of   dog : 26 %
Accuracy of  frog : 70 %
Accuracy of horse : 61 %
Accuracy of  ship : 51 %
Accuracy of truck : 63 %
cuda:0
```

Example 4 (python):
```python
import argparse
 import deepspeed

 def add_argument():

     parser=argparse.ArgumentParser(description='CIFAR')

     # Data.
     # Cuda.
     parser.add_argument('--with_cuda', default=False, action='store_true',
                         help='use CPU in case there\'s no GPU support')
     parser.add_argument('--use_ema', default=False, action='store_true',
                         help='whether use exponential moving average')

     # Train.
     parser.add_argument('-b', '--batch_size', default=32, type=int,
                         help='mini-batch size (default: 32)')
     parser.add_argument('-e', '--epochs', default=30, type=int,
                         help='number of total epochs (default: 30)')
     parser.add_argument('--local_rank', type=int, default=-1,
                        help='local rank passed from distributed launcher')

     # Include DeepSpeed configuration arguments.
     parser = deepspeed.add_config_arguments(parser)

     args=parser.parse_args()

     return args
```

---

## DeepSpeed Ulysses-Offload

**URL:** https://www.deepspeed.ai/tutorials/ulysses-offload/

**Contents:**
- DeepSpeed Ulysses-Offload
    - Contents
- Design of Ulysses-Offload
- Training Environment
- Training a 6.7B parameter GPT with Ulysses-Offload
  - Megatron-DeepSpeed Configuration Changes

DeepSpeed Ulysses-Offload is a system of chunking and offloading long-context transformer model training scheme built on top of ZeRO and DeepSpeed Ulysses. It adopts Fully Pipeliend Distributed Transformer (FPDT) which enables 2M context size training on 8B models with only 4 GPUs, and 4M context size training on 70B models with 32 GPUs. Read our Ulysses-Offload blog and paper to learn more!

We recommend that you read the tutorials on Getting Started, ZeRO and Megatron-DeepSpeed before stepping through this tutorial.

Ulysses-Offload is a chunking and offloading-based transformer implementation, which retain the full precision of the vanilla transformer, while significantly reduce the activation memory required during long-context model training. FPDT breaks long sequence input into smaller chunks, moving them among host and GPU memory to achieve the superior memory efficiency while reaching over 50% of MFU. FPDT adopts a double-buffer design, which overlaps the fetching/offloading with the attention computation. FPDT also allows uUsers to configure the chunk size to match the expected memory budget.

Ulysses-Offload supports ZeRO, which shards the model and tensors among GPU memory, further pushing the limit of long-context model training with state-of-the-art hardware efficiency.

For this tutorial, Flash Attention (CUDA) is required. We will configure a 8 billion parameter LLaMA model using the DeepSpeed Megatron-DeepSpeed code. We will use 1 nodes of 4x NVIDIA Tesla A100-SXM4 Tensor Core GPU.

Users can set the context size at the beginning of the script, for this exercise, we will use 256K context and mini batch of one.

For 6.7B model, we will enable ZeRO-3, Ulysses, activation checkpointing with CPU offloading first reach a decent GPU memory efficiency, then users can configure the following arguments:

You can find the full script here.

See more details on Megatron-DeepSpeed tutorial examples on how to launch a Megatron-DeepSpeed job.

Congratulations! You have completed the Ulysses-Offload tutorial.

Updated: November 5, 2025

**Examples:**

Example 1 (unknown):
```unknown
### Main configs
seq_len=262144 # need to be power of 2
```

Example 2 (unknown):
```unknown
megatron_options="\
 --ds-sequence-parallel-fpdt \
 --ds-sequence-parallel-fpdt-chunk-size 65536 \
 --ds-sequence-parallel-fpdt-offloading \
 --ds-sequence-parallel-size 4"
```

Example 3 (unknown):
```unknown
--use-flash-attn-v2 \
 --use-rotary-position-embeddings \
 --rotary-percent 0.25 \
 --rotary-position-embeddings-theta 100000000 \
```

Example 4 (unknown):
```unknown
if [ "${activation_checkpoint}" = "true" ]; then
 deepspeed_options="${deepspeed_options} \
     --deepspeed-activation-checkpointing \
     --checkpoint-in-cpu"
 fi
```

---

## Getting Started with DeepSpeed-Ulysses for Training Transformer Models with Extreme Long Sequences

**URL:** https://www.deepspeed.ai/tutorials/ds-sequence/

**Contents:**
- Getting Started with DeepSpeed-Ulysses for Training Transformer Models with Extreme Long Sequences
    - Contents
- 1. Installation
- 2. How to use DeepSpeed-Ulysses in your application?
- 3. Enabling DeepSpeed-Ulysses with FlashAttention?

In this tutorial we describe how to enable DeepSpeed-Ulysses for Megatron-Deepspeed. DeepSpeed-Ulysses is a simple but highly communication and memory efficient mechanism sequence parallelism approach for training of large transformer models with massive sequence lengths. It partitions input tensors along the sequence dimension and uses a communication-efficient all-2-all collective for distributed attention computations. Additionally, DeepSpeed-Ulysses incorporates advanced modeling and system optimizations, such as Flash attention, sparse attention, and ZeRO optimizer, to optimize both computational efficiency and memory usage. Training with DeepSpeed sequence parallelism allows both model size and sequence length to scale near indefinitely unbounded by single GPU memory limitation and at a high fraction of peak compute performance. Currently, DeepSpeed-Ulysses can handle sequences up to 1 million in length (10 times the size of a complete Harry Potter book!) on 64 A100 GPUs. Please read our DeepSpeed-Ulysses blog to learn more!

If you’re interested in a newer version that works with HF Transformers, please see https://www.deepspeed.ai/tutorials/ulysses-alst-sequence-parallelism

You will need to install DeepSpeed v0.10.2 or higher to use the DeepSpeed Sequence feature. Installing DeepSpeed is as simple as pip install deepspeed, see more details.

Integrating DS-Seq into your training code is easy, and in this section we describe how to integrate DeepSpeed-Ulysses through our Megatron-DeepSpeed code repo.

In the Megatron-DeepSpeed exampele, to enable sequence parallelism, set the degree of parallelism using the –ds-sequence-parallel-size argument. You also need to ensure that the number of attention heads is divisible by this value. We have prepared scripts for you to quickly get some examples for training GPT-3 like models with very long sequences:

Please note that our sequence parallelism feature is currently incompatible with Megatron-LM’s tensor or pipeline parallelism.

DeepSpeed’s sequence parallelism can be combined with different types of attention implementations to further improve the memory and compute efficiency of long sequence training:

Classic attention: attention mechanism implemented via PyTorch.

FlashAttention: the implementation from FlashAttention: Fast and Memory-Efficient Exact Attention with IO-Awareness. Enabled by --use-flash-attn.

FlashAttention + Triton: FlashAttention in Triton (tested with triton==2.0.0.dev20221202). Enabled by --use-flash-attn-triton.

For the best performance, we recommend using FlashAttention + Triton. Below are the installation steps. Note that FlashAttention is compatible only with NVIDIA Turing, Ampere, Ada, or Hopper GPUs.

You may also want to ensure your model configuration is compliant with FlashAttention’s requirements. For instance, to achieve optimal performance, the head size should be divisible by 8. Refer to the FlashAttention documentation for more details.

Updated: November 5, 2025

**Examples:**

Example 1 (python):
```python
def __init__():
    ...
    self.local_attn = CoreAttention(self.layer_number, config, self.attn_mask_type)
    self.core_attention = local_attn
    ...

def forward():
    ...
    context_layer = self.core_attention(
                    query_layer, key_layer, value_layer, attention_mask)
    ...
```

Example 2 (python):
```python
from deepspeed.sequence.layer import DistributedAttention

def __init__():
    ...
    self.local_attn = CoreAttention(self.layer_number, config, self.attn_mask_type)
    self.dist_attn = DistributedAttention(self.local_attn, parallel_state.get_sequence_parallel_group())
    ...

def forward():
    ...
    context_layer = self.dist_attn(query_layer, key_layer, value_layer, attention_mask)
    ...
```

Example 3 (python):
```python
def initialize_model_parallel(
    ...
    sequence_parallel_size,
    ...
):
    ...
    num_sequence_parallel_groups: int = world_size // sequence_parallel_size
    num_sequence_data_parallel_groups: int = world_size // sequence_parallel_size // data_parallel_size
    ...
    global _SEQUENCE_PARALLEL_GROUP
    for i in range(num_sequence_parallel_groups):
        ranks = range(i * sequence_parallel_size,
                      (i + 1) * sequence_parallel_size)
        group = torch.distributed.new_group(ranks)
        if rank in ranks:
            _SEQUENCE_PARALLEL_GROUP = group

def get_sequence_parallel_group():
    """Get the sequence parallel group the caller rank belongs to."""
    return _SEQUENCE_PARALLEL_GROUP
```

Example 4 (unknown):
```unknown
Megatron-DeepSpeed/examples_deepspeed/sequence_parallel$ bash ds_pretrain_gpt_1.3B_seq_parallel_32k.sh
Megatron-DeepSpeed/examples_deepspeed/sequence_parallel$ bash ds_pretrain_gpt_30B_seq_parallel_32k.sh
```

---

## DeepSpeed Model Compression Library

**URL:** https://www.deepspeed.ai/tutorials/model-compression/

**Contents:**
- DeepSpeed Model Compression Library
    - Contents
- 1. General Tutorial
  - 1.1 Layer Reduction
  - 1.2 Weight Quantization
  - 1.3 Activation Quantization
  - 1.4 Pruning
    - 1.4.1 Sparse Pruning
    - 1.4.2 Row Pruning
    - 1.4.3 Head Pruning

What is DeepSpeed Compression: DeepSpeed Compression is a library purposely built to make it easy to compress models for researchers and practitioners while delivering faster speed, smaller model size, and significantly reduced compression cost.

Why use DeepSpeed Compression: DeepSpeed Compression offers novel state-of-the-art compression techniques to achieve faster model compression with better model quality and lower compression cost. DeepSpeed Compression also takes an end-to-end approach to improve the computation efficiency of compressed models via a highly optimized inference engine. Furthermore, our library has multiple built-in state-of-the-art compression methods. It supports the synergistic composition of these methods and the system optimizations, offering the best of both worlds while allowing a seamless and easy-to-use pipeline for efficient DL model inference. We highly recommend you also to read our blog to learn more about (at a high level) why we build DeepSpeed Compression and what benefits it provides to users.

How to use DeepSpeed Compression: The first section General Tutorial will describe the compression methods supported by the library. The following sections will describe our research work on how to compose different compression methods to perform zero-cost quantization (ZeroQuant) and extreme compression (XTC). Unless otherwise stated, experiment results listed below are based on NVIDIA A100 GPU, and we observe slightly different result numbers when using different GPU hardwares.

To use DeepSpeed Compression library, you need to install DeepSpeed >= 0.7.0 following the installation guide. Currently the DeepSpeed Compression includes seven compression methods: layer reduction via knowledge distillation, weight quantization, activation quantization, sparse pruning, row pruning, head pruning, and channel pruning. In the following subsections, we will describe what these methods are, when to use them, and how to use them via our library.

What is layer reduction

Neural networks are constructed from input layer, output layer and hidden layer. For example, the BERT-base language model consists of embedding layer (input layer), classification layer (output layer) and 12 hidden layers. Layer reduction means reducing the number of hidden layers while keeping the width of the network intact (i.e., it does not reduce the dimension of the hidden layer). This method can linearly reduce the inference latency of hidden layers regardless of the hardware and/or scenarios.

When to use layer reduction

If the model is very deep, you may consider using this method. It works much better when applying knowledge distillation. Layer reduction can be applied in both the pre-training and fine-tuning stages. The former generates a distilled task-agnostic model, while the latter generates a task-specific distilled model. In our XTC work (paper, tutorial), we also discuss when to apply layer reduction.

How to use layer reduction

Layer reduction can be enabled and configured using the DeepSpeed config JSON file (configuration details). Users have the freedom to select any depth by keep_number_layer and any subset of the network layers by teacher_layer. In addition, users also can choose whether to reinitialize the input/output layers from the given model (teacher model) by other_module_name.

To apply layer reduction for task-specific compression, we provide an example on how to do so for BERT fine-tuning. Layer reduction is about resetting the depth of network architecture and reinitialization of weight parameters, which happens before the training process. The example includes the following changes to the client code (compression/bert/run_glue_no_trainer.py in DeepSpeedExamples):

(1) When initial the model, the number of layers in the model config should be the same as keep_number_layer in DeepSpeed config JSON file. For Hugging Face BERT example, set config.num_hidden_layers = ds_config["compression_training"]["layer_reduction"]["keep_number_layer"].

(2) Then we need to re-initialize the model based on the DeepSpeed JSON configurations using the function init_compression imported from deepspeed.compression.compress.

(3) During training, if KD is not used, nothing needs to be done. Otherwise, one needs to consider applying KD with the teacher_layer JSON configuration when calculating the difference between teacher’s and student’s output.

One can run our layer reduction example in DeepSpeedExamples by:

And the final result is:

To apply layer reduction for task-agnostic compression, we provide an example on how to do so in the GPT pre-training stage.

Step 1: Obtain the latest version of the Megatron-DeepSpeed.

Step 2: Enter Megatron-DeepSpeed/examples_deepspeed/compression directory.

Step 3: Run the example bash script such as ds_pretrain_gpt_125M_dense_cl_kd.sh. The args related to the pre-training distillation are:

(1)--kd, this enables knowledge distillation.

(2)--kd-beta-ce, this specifies the knowledge distillation coefficient. You can often leave it set to the default value 1, but sometimes tuning this hyperparameter leads to better distillation results.

(3)--num-layers-teacher, —hidden-size-teacher, num-attention-heads-teacher, these parameters specify the network configuration of the teacher model. Please make sure they match the teacher model dimensions in the checkpoint.

(4)--load-teacher, this is where one specifies the teacher model checkpoint.

(5)--load, this is where the initial checkpoint for the student model that is going to be loaded. By default, it will load the bottom layers of the teacher models for initialization, but you can pass your own checkpoints for initialization.

Apart from the above configs, you may also need to modify the data path in the data_options so that the trainer knows the data location. To make things slightly easier, we provide several example scripts for running distillation for different model sizes, including 350M (ds_pretrain_gpt_350M_dense_kd.sh) and 1.3B models (ds_pretrain_gpt_1.3B_dense_cl_kd.sh). We also empirically found that a staged KD often led to a better pre-trained distilled model on downstream tasks. Therefore, we suggest an easy approach to early-stop KD by not setting --kd in the script provided (e.g., disabling KD in the remaining 40% of training).

Step 4: After distilling the model, one can also choose to further quantize the distilled model by running the script 125M-L10-Int8-test-64gpu-distilled-group48.sh, which quantizes both the weights and activations of a distilled model with INT8 quantizer (the weight and activation quantization are introduced in the following sections). note that you need to set the -reset-iteration flag when performing the quantization. We provide the zero-shot perplexity result from WikiText-2 and LAMBADA in the following table.

What is weight quantization

Weight quantization maps the full precision weight (FP32/FP16) to the low bit ones, like INT8 and INT4. Quoted from this Coursera lecture: “Quantization involves transforming a model into an equivalent representation that uses parameters and computations at a lower precision. This improves the model’s execution performance and efficiency, but it can often result in lower model accuracy”.

When to use weight quantization

From one-side, again quoted from this Coursera lecture: “Mobile and embedded devices have limited computational resources, so it’s important to keep your application resource efficient. Depending on the task, you will need to make a trade-off between model accuracy and model complexity. If your task requires high accuracy, then you may need a large and complex model. For tasks that require less precision, it’s better to use a smaller, less complex model.”. On the other hand, recent server accelerators, like GPU, support low-precision arithmetic. Therefore, combining weight quantization with activation quantization (introduced in later section) can offer better efficiency as well.

How to use weight quantization

Weight quantization can be enabled and configured using the DeepSpeed config JSON file (configuration details). The key configurations we would like to point out are:

(1)quantize_groups, a group-wise weight matrix quantization: a weight matrix W is partitioned into multiple groups, and each group is quantized separately. See more details in this paper.

(2)quantize_weight_in_forward must be set to true for FP32 optimizer training and false for FP16.

(3)wq1/wq2, users can expand more groups such as wq3, wq4, etc.

(4)start_bit and target_bit, to simplify the first experiment we suggest to set them the same such that we apply quantization to the target bit once the iteration reaches schedule_offset.

There are two changes to the client code (compression/bert/run_glue_no_trainer.py in DeepSpeedExamples):

(1) After initialization of the model, apply init_compression function to the model with DeepSpeed JSON configurations.

(2) After training, apply redundancy_clean function to save the quantized weight.

One can run our weight quantization example in DeepSpeedExamples by:

And the final result is:

What is activation quantization

Activation means the input to each layer. Activation quantization maps the input from full/half precision to low precision. See more in this blog.

When to use activation quantization

It can improve computation efficiency similar to weight quantization.

How to use activation quantization

Activation quantization can be enabled and configured using the DeepSpeed config JSON file (configuration details). Some of the components are same as weight quantization, such as schedule_offset and quantization_type. The key configurations we would like to point out are:

(1)range_calibration, user has option to set dynamic or static. When using “dynamic”, the activation quantization groups will be automatically set to be token-wise (for Transformer-based models) and image-wise (for CNN-based models). See more in our ZeroQuant paper and the code (deepspeed/compression/basic_layer.py in DeepSpeed).

(2)aq1/aq2, users can expand more groups such as aq3, aq4, etc.

The client code change is the same as weight quantization.

One can run our activation quantization example in DeepSpeedExamples by:

And the final result is:

Pruning aims to reduce the number of parameters and operations involved in generating a prediction by removing network connections. With pruning, you can lower the overall parameter count in the network (see more in this Coursera lecture). We can divide the pruning strategy into two types: structured and unstructured pruning (see more in this paper).

What is sparse pruning

Sparse pruning means we set some of the elements in each weight matrix with zero values. Relying on the pruning method user chosen, the zero values may have structured pattern or unstructured pattern. One way to perform pruning is based on the absolute value of the weight parameters, see for instance this paper. Another way to perform pruning is based on the weights’ effect to the loss function when they are masked, see for instance this paper.

When to use sparse pruning

If your model is significantly over-parameterized, you may consider using sparse pruning. However, to see the real benefit of hardware computation efficiency, the density ratio (percentage of weights to keep after pruning) must be considerably low.

How to use sparse pruning

Sparse pruning can be enabled and configured using the DeepSpeed config JSON file (configuration details). The key configurations we would like to point out are:

(1)schedule_offset, we empirically find that when using method: topk, it’s better to set the schedule_offset to a large value such as 10% of the total training steps.

(2)method, we support L1 norm, topk and snip_momentum methods. Users are welcome to contribute more methods.

(3)sp1, users can expand more groups such as sp2, sp3, etc. Note this is not needed for snip_momentum method.

(4)dense_ratio, for unstructured sparse pruning, the dense ratio could be less than 0.1 for BRET-base model while still yielding a good accuracy. For ResNet-50, the dense ratio could be as low as 0.3 while still having good accuracy on ImageNet. for structured sparse pruning like snip_momentum, the dense ratio should be specified in shared_parameters and is used to calculate the global sparsity ratio.

(5)frequency, block_pattern and schedule_offset_end, they are used to specify the pruning frequency on steps, the block-wise pruning pattern (NxM and N in M), and the end steps for pruning. For snip_momentum method, these configurations are mandatory.

The client code change is the same as weight quantization.

One can run our sparse pruning example in DeepSpeedExamples by:

And the final result is:

Row pruning sets all the elements in certain rows of the weight matrix with zero values. If a row is pruned, all elements in that row are set to zero.

When to use row pruning

Row pruning can be beneficial to hardware speedup, much better than sparse pruning (but may result in larger accuracy loss compared to sparse pruning). It is a feature designed for two back-to-back linear layers (e.g., Feed Forward Network in Transformers). As such, we suggested using row pruning for the first linear layer (i.e., the intermediate.dense layer for BERT). Reducing the row dimension of this matrix can help to reduce the column of the follow-up matrix (i.e., layer.\\w+.output.dense layer for BERT). Row pruning would also work for other kinds of linear layers.

How to use row pruning

Row pruning can be enabled and configured using the DeepSpeed config JSON file (configuration details). The key configurations we would like to point out are:

(1)method, only topk method is supported currently. Users are welcome to contribute more methods.

(2)rp1, users can expand more groups such as rp2, rp3, etc.

(3)related_modules, as mentioned in “when to use row pruning”, if we do row pruning, the follow-up matrix will be affected. Thus, one needs to know the connection between the modules.

The client code change is the same as weight quantization.

One can run our row pruning example in DeepSpeedExamples by:

And the final result is:

Head pruning is designed specifically for networks with multi-head attention, such as transformer-based models (see more in this blog). For example, the BERT-base (BERT-large) model has 12 heads (24 heads).

When to use head pruning

Head pruning is beneficial to hardware speedup. Moreover, as stated in this blog: “Surprising observations are made in the paper, that even after training models normally (with all heads), many heads can be removed at a test time and it will not significantly affect the BLEU score, in fact, some cases removing few heads led to improving BLEU scores.”.

NOTE: Head pruning is a feature designed for the attention layers (e.g., Multi Head Attention in Transformers). For now, it can only be applied to output matrix of the Transformer (i.e., attention.output.dense in BERT). Pruning the output matrix can lead to the pruning of Query/Key/Value matrix as well.

How to use head pruning

Head pruning can be enabled and configured using the DeepSpeed config JSON file (configuration details). The key configurations we would like to point out are:

(1)num_heads: users need to provide the correct number of heads for their models.

(2)modules: the module attention.output.dense is made specific for Hugging Face BERT model. Currently, we only support this case when Query/Key/Values are separated matrices and followed by attention.output.dense. We are happy to assist and welcome contributions on variants of attention models.

(3)related_modules: as mentioned in “when to use head pruning”, pruning the attention output matrix can lead to pruning QKV matrices as well. Thus, the input here is [“self.query”, “self.key”, “self.value”].

The client code change is the same as weight quantization.

One can run our head pruning example in DeepSpeedExamples by:

And the final result is:

What is channel pruning

Channel pruning is made specifically for convolutional layers and computer vision. According to wikipedia.org, “The color data of an image is stored in three arrays of values, known as channels.”. For example, an image with three channels passing through ResNet-18 produces 64 channels after the first layer.

When to use channel pruning

Channel pruning is a feature designed for two back-to-back CONV2d layers (e.g., residual connection in ResNet). As such, we suggest using channel pruning for the first CONV2d layer. Reducing the number of output channels of this layer can help reduce the number of input channels of the next layer. Channel pruning would also work for other kinds of CONV2d layers.

How to use channel pruning

Channel pruning can be enabled and configured using the DeepSpeed config JSON file (configuration details).

One can run our channel pruning example in DeepSpeedExamples by:

And the final result is:

Note that the above result is when not using batch-norm (BN) in the “ResNet” model. If you use BN for the model and apply channel pruning, the validation after cleaning the model will be different from the model before cleaning. We suggest users to further finetune the model after applying redundancy_clean for such cases.

In this section, we introduce how to apply DS-Compression to perform cost-free INT8 quantization and lightweight INT4/INT8 mixed-precision quantization. For more details, please refer to our paper.

ZeroQuant is an efficient Post Training Quantization method that includes (1) a fine-grained hardware-friendly quantization scheme for both weight and activations, which can significantly reduce the quantization error; (2) a novel affordable layer-by-layer knowledge distillation algorithm (LKD) even without the access to the original training data; (3) a highly-optimized quantization system backend support to remove the quantization/dequantization overhead. By these techniques, ZeroQuant is able to (1) quantize models to INT8 without any cost and (2) quantize models to INT4/INT8 mixed-precision quantization with minimal resource requirements (e.g., 31s for BERT-base quantization).

When to use ZeroQuant

When you want to quantize the transformer-based model to INT8 or INT4/INT8 format, it is always a good idea to try ZeroQuant first, especially when the model is very resource-hungry (GPU and/or time) to do quantization aware training and/or when the original training data is not accessible.

One can run our BERT example in DeepSpeedExamples by:

And the final result is:

One can run our GPT example by:

And the final result is:

NOTE: right now, we only support zero cost quantization. Stay tuned for the code release on layer-by-layer knowledge distillation proposed in the ZeroQuant paper.

In this section, we introduce how to apply DeepSpeed Compression library to perform the light-weight layer reduction and ultra-low bit precision (binary/ternary) quantization. In particularly, we will guide you on implementing the XTC methods, namely:

(1) Obtaining a 1-bit or 2-bit BERT-base (12-layer) with 8-bit activation quantization.

(2) Reducing the 12-layer Bert-base to a 5-layer one and then obtaining its 1-bit or 2-bit counterparts.

XTC (short for eXTreme Compression) is our new simple yet efficient method that compresses a model to its limit with lightweight layer reduction and robust binarization. XTC reduces the model size by 32x with almost no loss in the average score on the GLUE tasks via simple yet effective binarization technique. By combining extreme quantization and lightweight layer reduction, we can further improve the binarized model, achieving 50x model size reduction while keeping 97% of the accuracy. For more details, see how we derive our method in our paper where we perform a systematic study on the impacts of various techniques currently used for extreme compression.

If you want to significantly compress your models while retaining competitive performance, XTC could be a desirable choice. It is a simple and hyper-parameter tuning friendly method.

Installation: Examples of XTC extreme compression for BERT models are at compression/bert/bash_script/XTC in DeepSpeedExamples. You will need to install the requirements by:

Implementation of XTC methods: To accommodate users who do not have a fine-tuned model or task-specific model for compression, with the arg --model_name_or_path yoshitomo-matsubara/bert-base-uncased-${TASK_NAME} our python script run_glue_no_trainer.py automatically downloads the models from Hugging Face. Users can also use their own models with better accuracy as the teacher and the student model initialization.

For the configurations, see compression/bert/config/XTC/ds_config_W1A8_Qgroup1_fp32.json in DeepSpeedExamples. In our paper, we used FP32 ("fp16": {"enabled": false}) to perform training, while directly applying 8-bit quantization ("bits": 8) to the activations and 1-bit quantization ("start_bits": 1, "target_bits": 1) to the attention (query, key, val) and feedforward weight matrices ("modules": ["attention.self", "intermediate", "output.dense"]) at the beginning of the training ("schedule_offset": 0). In addition, we also apply 1-bit quantization to word_embeddings as weight quantization.

One can run this example by:

And the final result is:

The other important feature we would like to mention is the quantize_groups inside weight_quantization, which is set to be 1 here to match our XTC paper’s FP32 training setup. We find that under FP16 training, smaller number of quantization group (e.g., 1 or 2) could lead to unstable training. Thus, we recommend using larger number of groups (e.g., 64) under FP16. compression/bert/config/ds_config_W1A8_Qgroup64_fp16.json in DeepSpeedExamples is the FP16 example configurations, where "fp16": {"enabled": true} and "weight_quantization": {"shared_parameters": {"quantize_weight_in_forward": false}} are different from FP32 case.

With this config, we quantize the existing fined-tuned models downloaded from Hugging Face. For 2-bit weight quantization, user needs to update the ds_config JSON file. To give a sense of the compression performance of downloaded models compared to our paper, we collect the results (1/2-bit BERT on MNLI and QQP with 18 training epochs) in table below. The difference between this tutorial and paper is because they use different checkpoints. Data augmentation introduces in TinyBERT will help significantly for smaller tasks (such as mrpc, rte, sst-b and cola). See more details in our paper.

This section consists of two parts: (a) we first perform a light-weight layer reduction, and (b) based on the model in (a), we perform 1-bit or 2-bit quantization.

3.2.1 Light-weight Layer Reduction

compression/bert/config/XTC/ds_config_layer_reduction_fp16.json in DeepSpeedExamples is the example configuration for reducing the 12-layer BERT-base to a 6-layer one. The student’s layers are initialized from i-layer of the teacher with i= [1, 3 ,5 ,7 ,9 ,11] (note that the layer starts from 0), which is called Skip-BERT_5 in our XTC paper. In addition, student’s modules including embedding, pooler and classifier are also initialized from teacher. For 5-layer layer reduction, one needs to change the configs in ds_config_layer_reduction_fp16.json to "keep_number_layer": 5, "teacher_layer": [2, 4 ,6, 8, 10](like in compression/bert/config/ds_config_TEMPLATE.json).

One can run this example by:

And the final result is:

Notably, when using one-stage knowledge distillation (--distill_method one_stage), the difference between the outputs of teacher and student models (att_loss and rep_loss) also need to be consistent with the initialization. See the function _kd_function under forward_loss in compression/bert/util.py.

For mnli/qqp, we set --num_train_epochs 36, --learning_rate 5e-5, and with the JSON config above. The results are given below (we also include the fp16 training results). Using fp32 clearly results in more stable performance than fp16, although fp16 can speed up the training time.

3.2.2 One-bit or Two-bit quantization for 6-layer (5-layer) BERT

Given the above layer-reduced models ready, we now continue to compress the model with 1/2-bit quantization. compression/bert/config/XTC/ds_config_layer_reduction_W1Q8_fp32.json in DeepSpeedExamples is the example configuration where we set the layer reduction to be true on top of compression/bert/config/XTC/ds_config_W1A8_Qgroup1_fp32.json. In addition to the configuration, we need to update the path for the student model using --pretrained_dir_student in the script compression/bert/bash_script/XTC/layer_reduction_1bit.sh. User can train with a different teacher model by adding --pretrained_dir_teacher.

One can run this example by:

And the final result is:

With the command above, one can now obtain the results of 1-bit 6-layer model. Now we list more results for 2-/1-bit 6/5-layer models in the following table. Note that the checkpoints we used for the compression below are from the above table in section 3.2.1.

Updated: November 5, 2025

**Examples:**

Example 1 (unknown):
```unknown
DeepSpeedExamples/compression/bert$ pip install -r requirements.txt
DeepSpeedExamples/compression/bert$ bash bash_script/layer_reduction.sh
```

Example 2 (unknown):
```unknown
Epoch: 18 | Time: 12m 38s
Clean the best model, and the accuracy of the clean model is acc/mm-acc:0.8340295466123281/0.8339096826688365
```

Example 3 (unknown):
```unknown
DeepSpeedExamples/compression/bert$ pip install -r requirements.txt
DeepSpeedExamples/compression/bert$ bash bash_script/quant_weight.sh
```

Example 4 (unknown):
```unknown
Epoch: 09 | Time: 27m 10s
Clean the best model, and the accuracy of the clean model is acc/mm-acc:0.8414671421293938/0.8422497965825875
```

---

## Flops Profiler

**URL:** https://www.deepspeed.ai/tutorials/flops-profiler/

**Contents:**
- Flops Profiler
    - Contents
- Overview
- Flops Measurement
- Multi-GPU, Multi-node, Data Parallelism, and Model Parallelism
- Usage
  - Usage With the DeepSpeed Runtime
    - Example: Megatron-LM
  - Usage Outside the DeepSpeed Runtime
    - In Model Inference

In this tutorial, we introduce the DeepSpeed Flops Profiler and provide examples of its usage.

Effective use of hardware resources is critical to good performance, but performance inefficiency in existing implementations for large-scale model training and inference are often hard to spot and attribute to specific module components. DeepSpeed Flops Profiler helps users easily measure both the model training/inference speed (latency, throughput) and efficiency (floating-point operations per second, i.e., FLOPS) of a model and its submodules, with an eye towards eliminating inefficiencies in existing implementations.

Below is an example output for BERT-Large(NVIDIA) on an A100 GPU with batch size 80:

In the summary profile, the DeepSpeed Flops Profiler outputs the number of parameters, floating-point operations (flops), FLOPS, latency, and throughput in samples/second of the model. This profile shows how much performance gap (compared to the peak hardware performance) the current model execution has and helps users tune the training or inference setup (e.g., hyperparameters, data parallelism, model parallelism, system configurations, etc.) for better performance.

The DeepSpeed Flops Profiler also measures significant modules at different model depths (aggregated profile) and module-specific profile in the model architecture (detailed profile). Using these profiles, DeepSpeed users can understand how each layer or submodule contributes to the overall model complexity/performance. Then users can adjust or refactor the model design to improve performance. For example, using the profiler, DeepSpeed users can quantitatively tell if stacking smaller layers is lighter or more performant than having bigger ones. The aggregated and detailed profiles also allow users to quickly identify bottleneck modules. In the BERT-Large example above, using the DeepSpeed Flops Profiler, we find that BertLayer is the most significant layer and contains quite a few dropout, softmax, and layer norm along with linear modules. These modules are not heavy in flops and would trigger many GPU kernel invocations and create excessive read/write requests to memory. The pattern shown in the detailed profile suggests this is a perfect match for kernel fusion, and we developed fused transformer-kernels to reduce data movement (see DeepSpeedBert). After applying our optimizations, we see a 25% improvement in FLOPS per GPU and overall training samples/second in the DeepSpeed Flops Profiler output.

The DeepSpeed Flops Profiler can be used with the DeepSpeed runtime without any user code change or be used independently from DeepSpeed as a standalone package. When using DeepSpeed for model training, the profiler can be enabled in the DeepSpeed configuration file. As a standalone package, the profiler API can be used in both training and inference code. The DeepSpeed profiler is still under active development and includes just initial features. Stay connected for more exciting features to be added soon.

Similar to existing flops calculation tools or methods, the DeepSpeed Flops Profiler measures the flops of the forward pass of a module and the flops of the backward pass is estimated as 2 times of that of the forward pass. Different from the PyTorch profiler which calculates the flops of PyTorch operators, the DeepSpeed Flops Profiler measures the flops within modules in a model and provides more insights to the users about the model execution. The flops estimation is partly inspired by ptflops with the major difference being that the DeepSpeed Flops Profiler not only supports flops computation directly at module level, but can also capture torch.nn.functional invoked in a module to estimate the flops. Thus the DeepSpeed Flops Profiler allows for customized modules in the model, e.g., ParallelTransformerLayerworks, ParallelSelfAttention, RowParallelLinear, etc. in Megatron-LM. This is in contrast to ptflops which requires users to write customized flops calculation functions for each customized module.

The DeepSpeed Flops Profiler outputs the per GPU profile as well as the world size, data parallel size, and model parallel size.

For models running on multi-GPU or multi-node, only change of the model parallelism (e.g., --model-parallel-size in Megatron-LM) affects the number of flops and parameters profiled, i.e., model_parallel_size * flops = total_flops and model_parallel_size * parameters = total_parameters. The data parallel size or world size (related to the number of GPUs or nodes) does not affect the per GPU profile.

The DeepSpeed Flops Profiler can be used with the DeepSpeed runtime or as a standalone package. When using DeepSpeed for model training, the profiler can be configured in the deepspeed configuration file without user code changes. To use the flops profiler outside the DeepSpeed runtime, install DeepSpeed and import the flops_profiler package to use the APIs directly. Examples of each usage are given below.

When using DeepSpeed for model training, the profiler can be configured in the deepspeed configuration file. No explicit API calls are needed to use the profiler. The profiler can be enabled by adding the following field to deepspeed’s configuration json file. Refer to flops profiler for details.

For information on running Megatron-LM with DeepSpeed, please refer to our tutorial Megatron-LM.

An example output of 12-layer Megatron-LM model (hidden_size = 8192, num_attention_heads = 32, batch_size = 1024, seq_length = 1024) is shown below.

The profiler can be used as a standalone package outside of the DeepSpeed runtime. One can simply install DeepSpeed and import the flops_profiler package to use the APIs directly. Refer to installation of DeepSpeed for installing DeepSpeed.

To profile a trained model in inference, use the get_model_profile function. Examples are given below.

The following example shows how to profile AlexNet using the DeepSpeed flops profiler.

To profile model forward in a training workflow, use the FlopsProfilerclass. The FlopsProfilerclass provides the following methods:

Below is an example of this usage in a typical training workflow.

Updated: November 5, 2025

**Examples:**

Example 1 (unknown):
```unknown
-------------------------- DeepSpeed Flops Profiler --------------------------
Profile Summary at step 10:
Notations:
data parallel size (dp_size), model parallel size(mp_size),
number of parameters (params), number of multiply-accumulate operations(MACs),
number of floating-point operations (flops), floating-point operations per second (FLOPS),
fwd latency (forward propagation latency), bwd latency (backward propagation latency),
step (weights update latency), iter latency (sum of fwd, bwd and step latency)

world size:                                                   1
data parallel size:                                           1
model parallel size:                                          1
batch size per GPU:                                           80
params per gpu:                                               336.23 M
params of model = params per GPU * mp_size:                   336.23 M
fwd MACs per GPU:                                             3139.93 G
fwd flops per GPU:                                            6279.86 G
fwd flops of model = fwd flops per GPU * mp_size:             6279.86 G
fwd latency:                                                  76.67 ms
bwd latency:                                                  108.02 ms
fwd FLOPS per GPU = fwd flops per GPU / fwd latency:          81.9 TFLOPS
bwd FLOPS per GPU = 2 * fwd flops per GPU / bwd latency:      116.27 TFLOPS
fwd+bwd FLOPS per GPU = 3 * fwd flops per GPU / (fwd+bwd latency):   102.0 TFLOPS
step latency:                                                 34.09 us
iter latency:                                                 184.73 ms
samples/second:                                               433.07

----------------------------- Aggregated Profile per GPU -----------------------------
Top modules in terms of params, MACs or fwd latency at different model depths:
depth 0:
    params      - {'BertForPreTrainingPreLN': '336.23 M'}
    MACs        - {'BertForPreTrainingPreLN': '3139.93 GMACs'}
    fwd latency - {'BertForPreTrainingPreLN': '76.39 ms'}
depth 1:
    params      - {'BertModel': '335.15 M', 'BertPreTrainingHeads': '32.34 M'}
    MACs        - {'BertModel': '3092.96 GMACs', 'BertPreTrainingHeads': '46.97 GMACs'}
    fwd latency - {'BertModel': '34.29 ms', 'BertPreTrainingHeads': '3.23 ms'}
depth 2:
    params      - {'BertEncoder': '302.31 M', 'BertLMPredictionHead': '32.34 M'}
    MACs        - {'BertEncoder': '3092.88 GMACs', 'BertLMPredictionHead': '46.97 GMACs'}
    fwd latency - {'BertEncoder': '33.45 ms', 'BertLMPredictionHead': '2.61 ms'}
depth 3:
    params      - {'ModuleList': '302.31 M', 'Embedding': '31.79 M', 'Linear': '31.26 M'}
    MACs        - {'ModuleList': '3092.88 GMACs', 'Linear': '36.23 GMACs'}
    fwd latency - {'ModuleList': '33.11 ms', 'BertPredictionHeadTransform': '1.83 ms''}
depth 4:
    params      - {'BertLayer': '302.31 M', 'LinearActivation': '1.05 M''}
    MACs        - {'BertLayer': '3092.88 GMACs', 'LinearActivation': '10.74 GMACs'}
    fwd latency - {'BertLayer': '33.11 ms', 'LinearActivation': '1.43 ms'}
depth 5:
    params      - {'BertAttention': '100.76 M', 'BertIntermediate': '100.76 M'}
    MACs        - {'BertAttention': '1031.3 GMACs', 'BertIntermediate': '1030.79 GMACs'}
    fwd latency - {'BertAttention': '19.83 ms', 'BertOutput': '4.38 ms'}
depth 6:
    params      - {'LinearActivation': '100.76 M', 'Linear': '100.69 M'}
    MACs        - {'LinearActivation': '1030.79 GMACs', 'Linear': '1030.79 GMACs'}
    fwd latency - {'BertSelfAttention': '16.29 ms', 'LinearActivation': '3.48 ms'}

------------------------------ Detailed Profile per GPU ------------------------------
Each module profile is listed after its name in the following order:
params, percentage of total params, MACs, percentage of total MACs, fwd latency, percentage of total fwd latency, fwd FLOPS

BertForPreTrainingPreLN(
  336.23 M, 100.00% Params, 3139.93 GMACs, 100.00% MACs, 76.39 ms, 100.00% latency, 82.21 TFLOPS,
  (bert): BertModel(
    335.15 M, 99.68% Params, 3092.96 GMACs, 98.50% MACs, 34.29 ms, 44.89% latency, 180.4 TFLOPS,
    (embeddings): BertEmbeddings(...)
    (encoder): BertEncoder(
      302.31 M, 89.91% Params, 3092.88 GMACs, 98.50% MACs, 33.45 ms, 43.79% latency, 184.93 TFLOPS,
      (FinalLayerNorm): FusedLayerNorm(...)
      (layer): ModuleList(
        302.31 M, 89.91% Params, 3092.88 GMACs, 98.50% MACs, 33.11 ms, 43.35% latency, 186.8 TFLOPS,
        (0): BertLayer(
          12.6 M, 3.75% Params, 128.87 GMACs, 4.10% MACs, 1.29 ms, 1.69% latency, 199.49 TFLOPS,
          (attention): BertAttention(
            4.2 M, 1.25% Params, 42.97 GMACs, 1.37% MACs, 833.75 us, 1.09% latency, 103.08 TFLOPS,
            (self): BertSelfAttention(
              3.15 M, 0.94% Params, 32.23 GMACs, 1.03% MACs, 699.04 us, 0.92% latency, 92.22 TFLOPS,
              (query): Linear(1.05 M, 0.31% Params, 10.74 GMACs, 0.34% MACs, 182.39 us, 0.24% latency, 117.74 TFLOPS,...)
              (key): Linear(1.05 M, 0.31% Params, 10.74 GMACs, 0.34% MACs, 57.22 us, 0.07% latency, 375.3 TFLOPS,...)
              (value): Linear(1.05 M, 0.31% Params, 10.74 GMACs, 0.34% MACs, 53.17 us, 0.07% latency, 403.91 TFLOPS,...)
              (dropout): Dropout(...)
              (softmax): Softmax(...)
            )
            (output): BertSelfOutput(
              1.05 M, 0.31% Params, 10.74 GMACs, 0.34% MACs, 114.68 us, 0.15% latency, 187.26 TFLOPS,
              (dense): Linear(1.05 M, 0.31% Params, 10.74 GMACs, 0.34% MACs, 64.13 us, 0.08% latency, 334.84 TFLOPS, ...)
              (dropout): Dropout(...)
            )
          )
          (PreAttentionLayerNorm): FusedLayerNorm(...)
          (PostAttentionLayerNorm): FusedLayerNorm(...)
          (intermediate): BertIntermediate(
            4.2 M, 1.25% Params, 42.95 GMACs, 1.37% MACs, 186.68 us, 0.24% latency, 460.14 TFLOPS,
            (dense_act): LinearActivation(4.2 M, 1.25% Params, 42.95 GMACs, 1.37% MACs, 175.0 us, 0.23% latency, 490.86 TFLOPS,...)
          )
          (output): BertOutput(
            4.2 M, 1.25% Params, 42.95 GMACs, 1.37% MACs, 116.83 us, 0.15% latency, 735.28 TFLOPS,
            (dense): Linear(4.2 M, 1.25% Params, 42.95 GMACs, 1.37% MACs, 65.57 us, 0.09% latency, 1310.14 TFLOPS,...)
            (dropout): Dropout(...)
          )
        )
        ...
        (23): BertLayer(...)
      )
    )
    (pooler): BertPooler(...)
  )
  (cls): BertPreTrainingHeads(...)
)
------------------------------------------------------------------------------
```

Example 2 (unknown):
```unknown
{
  "flops_profiler": {
    "enabled": true,
    "profile_step": 1,
    "module_depth": -1,
    "top_modules": 1,
    "detailed": true,
    "output_file": null
    }
}
```

Example 3 (unknown):
```unknown
-------------------------- DeepSpeed Flops Profiler --------------------------
Profile Summary at step 10:
Notations:
data parallel size (dp_size), model parallel size(mp_size),
number of parameters (params), number of multiply-accumulate operations(MACs),
number of floating-point operations (flops), floating-point operations per second (FLOPS),
fwd latency (forward propagation latency), bwd latency (backward propagation latency),
step (weights update latency), iter latency (sum of fwd, bwd and step latency)

world size:                                                   1
data parallel size:                                           1
model parallel size:                                          1
batch size per GPU:                                           1024
params per gpu:                                               1.29 M
params of model = params per GPU * mp_size:                   1.29 M
fwd MACs per GPU:                                             41271.95 G
fwd flops per GPU:                                            82543.9 G
fwd flops of model = fwd flops per GPU * mp_size:             82543.9 G
fwd latency:                                                  1.89 s
bwd latency:                                                  5.38 s
fwd FLOPS per GPU = fwd flops per GPU / fwd latency:          43.68 TFLOPS
bwd FLOPS per GPU = 2 * fwd flops per GPU / bwd latency:      30.7 TFLOPS
fwd+bwd FLOPS per GPU = 3 * fwd flops per GPU / (fwd+bwd latency):   34.07 TFLOPS
step latency:                                                 34.12 s
iter latency:                                                 41.39 s
samples/second:                                               24.74

----------------------------- Aggregated Profile per GPU -----------------------------
Top 1 modules in terms of params, MACs or fwd latency at different model depths:
depth 0:
    params      - {'GPT2Model': '1.29 M'}
    MACs        - {'GPT2Model': '41271.95 GMACs'}
    fwd latency - {'GPT2Model': '1.84 s'}
depth 1:
    params      - {'TransformerLanguageModel': '1.29 M'}
    MACs        - {'TransformerLanguageModel': '39584.03 GMACs'}
    fwd latency - {'TransformerLanguageModel': '1.83 s'}
depth 2:
    params      - {'ParallelTransformer': '1.29 M'}
    MACs        - {'ParallelTransformer': '39584.03 GMACs'}
    fwd latency - {'ParallelTransformer': '1.81 s'}
depth 3:
    params      - {'ModuleList': '1.28 M'}
    MACs        - {'ModuleList': '39584.03 GMACs'}
    fwd latency - {'ModuleList': '1.3 s'}
depth 4:
    params      - {'ParallelTransformerLayerPart2': '688.15 k'}
    MACs        - {'ParallelTransformerLayerPart2': '26388.28 GMACs'}
    fwd latency - {'ParallelTransformerLayerPart2': '865.73 ms'}
depth 5:
    params      - {'ParallelMLP': '491.54 k'}
    MACs        - {'ParallelMLP': '26388.28 GMACs'}
    fwd latency - {'ParallelMLP': '849.4 ms'}

------------------------------ Detailed Profile per GPU ------------------------------
Each module profile is listed after its name in the following order:
params, percentage of total params, MACs, percentage of total MACs, fwd latency, percentage of total fwd latency, fwd FLOPS

Note: 1. A module can have torch.nn.module or torch.nn.functional to compute logits (e.g. CrossEntropyLoss). They are not counted as submodules, thus not to be printed out. However they make up the difference between a parent's MACs(or latency) and the sum of its submodules'.
1. Number of floating-point operations is a theoretical estimation, thus FLOPS computed using that could be larger than the maximum system throughput.
2. The fwd latency listed in the top module's profile is directly captured at the module forward function in PyTorch, thus it's less than the fwd latency shown above which is captured in DeepSpeed.

GPT2Model(
  1.29 M, 100.00% Params, 41271.95 GMACs, 100.00% MACs, 1.84 s, 100.00% latency, 44.78 TFLOPS,
  (language_model): TransformerLanguageModel(
    1.29 M, 100.00% Params, 39584.03 GMACs, 95.91% MACs, 1.83 s, 99.11% latency, 43.34 TFLOPS,
    (embedding): Embedding(
      2, 0.00% Params, 0 MACs, 0.00% MACs, 18.1 ms, 0.98% latency, 0.0 FLOPS,
      (word_embeddings): VocabParallelEmbedding(1, 0.00% Params, 0 MACs, 0.00% MACs, 164.75 us, 0.01% latency, 0.0 FLOPS, )
      (position_embeddings): Embedding(1, 0.00% Params, 0 MACs, 0.00% MACs, 489.23 us, 0.03% latency, 0.0 FLOPS, 1024, 8192)
      (embedding_dropout): Dropout(0, 0.00% Params, 0 MACs, 0.00% MACs, 93.94 us, 0.01% latency, 0.0 FLOPS, p=0.1, inplace=False)
    )
    (transformer): ParallelTransformer(
      1.29 M, 100.00% Params, 39584.03 GMACs, 95.91% MACs, 1.81 s, 98.11% latency, 43.78 TFLOPS,
      (layers): ModuleList(
        1.28 M, 98.73% Params, 39584.03 GMACs, 95.91% MACs, 1.3 s, 70.66% latency, 60.79 TFLOPS,
        (0): ParallelTransformerLayerPart1(
          49.15 k, 3.80% Params, 1099.65 GMACs, 2.66% MACs, 23.5 ms, 1.27% latency, 93.6 TFLOPS,
          (input_layernorm): FusedLayerNorm(16.38 k, 1.27% Params, 0 MACs, 0.00% MACs, 128.75 us, 0.01% latency, 0.0 FLOPS, torch.Size([8192]), eps=1e-05, elementwise_affine=True)
          (attention): ParallelSelfAttention(
            32.77 k, 2.53% Params, 1099.65 GMACs, 2.66% MACs, 22.8 ms, 1.24% latency, 96.46 TFLOPS,
            (query_key_value): ColumnParallelLinear(24.58 k, 1.90% Params, 824.63 GMACs, 2.00% MACs, 8.93 ms, 0.48% latency, 184.7 TFLOPS, )
            (scale_mask_softmax): FusedScaleMaskSoftmax(0, 0.00% Params, 134.22 MMACs, 0.00% MACs, 151.16 us, 0.01% latency, 1.78 TFLOPS, )
            (attention_dropout): Dropout(0, 0.00% Params, 0 MACs, 0.00% MACs, 79.63 us, 0.00% latency, 0.0 FLOPS, p=0.1, inplace=False)
            (dense): RowParallelLinear(8.19 k, 0.63% Params, 274.88 GMACs, 0.67% MACs, 2.67 ms, 0.14% latency, 205.81 TFLOPS, )
          )
        )
        (1): ParallelTransformerLayerPart2(
          57.35 k, 4.43% Params, 2199.02 GMACs, 5.33% MACs, 77.53 ms, 4.21% latency, 56.73 TFLOPS,
          (post_attention_layernorm): FusedLayerNorm(16.38 k, 1.27% Params, 0 MACs, 0.00% MACs, 116.11 us, 0.01% latency, 0.0 FLOPS, torch.Size([8192]), eps=1e-05, elementwise_affine=True)
          (mlp): ParallelMLP(
            40.96 k, 3.16% Params, 2199.02 GMACs, 5.33% MACs, 76.19 ms, 4.13% latency, 57.72 TFLOPS,
            (dense_h_to_4h): ColumnParallelLinear(32.77 k, 2.53% Params, 1099.51 GMACs, 2.66% MACs, 10.79 ms, 0.59% latency, 203.81 TFLOPS, )
            (dense_4h_to_h): RowParallelLinear(8.19 k, 0.63% Params, 1099.51 GMACs, 2.66% MACs, 14.38 ms, 0.78% latency, 152.95 TFLOPS, )
          )
        )
        ...
        (23): ParallelTransformerLayerPart2(...)
      )
      (final_layernorm): FusedLayerNorm(16.38 k, 1.27% Params, 0 MACs, 0.00% MACs, 110.86 us, 0.01% latency, 0.0 FLOPS, torch.Size([8192]), eps=1e-05, elementwise_affine=True)
    )
  )
)
------------------------------------------------------------------------------
```

Example 4 (python):
```python
import torchvision.models as models
import torch
from deepspeed.profiling.flops_profiler import get_model_profile
from deepspeed.accelerator import get_accelerator

with get_accelerator().device(0):
    model = models.alexnet()
    batch_size = 256
    flops, macs, params = get_model_profile(model=model, # model
                                    input_shape=(batch_size, 3, 224, 224), # input shape to the model. If specified, the model takes a tensor with this shape as the only positional argument.
                                    args=None, # list of positional arguments to the model.
                                    kwargs=None, # dictionary of keyword arguments to the model.
                                    print_profile=True, # prints the model graph with the measured profile attached to each module
                                    detailed=True, # print the detailed profile
                                    module_depth=-1, # depth into the nested modules, with -1 being the inner most modules
                                    top_modules=1, # the number of top modules to print aggregated profile
                                    warm_up=10, # the number of warm-ups before measuring the time of each module
                                    as_string=True, # print raw numbers (e.g. 1000) or as human-readable strings (e.g. 1k)
                                    output_file=None, # path to the output file. If None, the profiler prints to stdout.
                                    ignore_modules=None) # the list of modules to ignore in the profiling
```

---

## Megatron-LM GPT2

**URL:** https://www.deepspeed.ai/tutorials/megatron/

**Contents:**
- Megatron-LM GPT2
    - Contents
- Training GPT-2 with the Original Megatron-LM
  - Training Data Setup
  - Running Unmodified Megatron-LM GPT2 model
- Enabling DeepSpeed
  - Argument Parsing
  - Initialization and Training
    - Initialization
    - Using the Training API

If you haven’t already, we advise you to first read through the Getting Started guide before stepping through this tutorial.

In this tutorial we will be adding DeepSpeed to Megatron-LM GPT2 model, which is a large, powerful transformer. Megatron-LM supports model-parallel and multi-node training. Please see the corresponding paper for more details: Megatron-LM: Training Multi-Billion Parameter Language Models Using Model Parallelism.

First, we discuss data and environment setup and how to train the GPT-2 model with the original Megatron-LM. Next, we proceed step-by-step in enabling this model to run with DeepSpeed. Finally, we demonstrate the performance gains, and memory footprint reduction from using DeepSpeed.

We’ve copied the original model code from Megatron-LM into DeepSpeed Megatron-LM and made it available as a submodule. To download, execute:

To use DeepSpeed we will modify three files :

The first step is adding DeepSpeed arguments to Megatron-LM GPT2 model, using deepspeed.add_config_arguments() in arguments.py.

We will modify pretrain.py to enable training with DeepSpeed.

We use deepspeed.initialize to create model_engine, optimizer and LR scheduler. Below is its definition:

For the Megatron-LM GPT2 model, we initialize DeepSpeed in its setup_model_and_optimizer() function as below, to pass the raw model, optimizer, args, lr_scheduler and mpu.

Note that when FP16 is enabled, Megatron-LM GPT2 adds a wrapper to the Adam optimizer. DeepSpeed has its own FP16 Optimizer, so we need to pass the Adam optimizer to DeepSpeed directly without any wrapper. We return the unwrapped Adam optimizer from get_optimizer() when DeepSpeed is enabled.

The model returned by deepspeed.initialize is the DeepSpeed Model Engine that we will use to train the model using the forward, backward and step API.

The forward propagation API is compatible to PyTorch and no change is required.

Backward propagation is done by calling backward(loss) directly on the model engine.

Zeroing the gradients is handled automatically by DeepSpeed after the weights have been updated using a mini-batch.

Furthermore, DeepSpeed addresses distributed data parallel and FP16 under the hood, simplifying code in multiple places.

(A) DeepSpeed also performs gradient averaging automatically at the gradient accumulation boundaries. So we skip the allreduce communication.

(B) We also skip updating master gradients, since DeepSpeed addresses it internally.

The step() function in DeepSpeed engine updates the model parameters as well as the learning rate.

The GPT2 training script logs the loss scaling value during training. Inside the DeepSpeed optimizer, this value is stored as cur_scale instead of loss_scale as in Megatron’s optimizer. Therefore, we appropriately replace it in the logging string.

The DeepSpeed engine has flexible APIs for checkpoint saving and loading, to handle the states from both the client model and its own internal.

To use DeepSpeed, we need to update utils.py in which Megatron-LM GPT2 saves and loads checkpoints.

Create a new function save_ds_checkpoint() as shown below. The new function collects the client model states and passes them to the DeepSpeed engine by calling DeepSpeed’s save_checkpoint().

In Megatron-LM GPT2’s save_checkpoint() function, add the following lines to invoke the above function for DeepSpeed.

In the load_checkpoint() function, use DeepSpeed checkpoint loading API as below, and return the states for the client model.

DeepSpeed can reduce the activation memory during model parallel training by partitioning activation checkpoints across model parallel GPUs, or offloading them to CPU. These optimizations are optional, and can be skipped unless activation memory becomes a bottleneck. To enable partition activation, we use the deepspeed.checkpointing API to replace Megatron’s activation checkpointing and random state tracker APIs. The replacement should happen before the first invocation of these APIs.

a) Replace in pretrain_gpt.py :

b) Replace in mpu/transformer.py:

With these replacements, various DeepSpeed activation checkpointing optimizations such as activation partitioning, contiguous checkpointing, and CPU checkpointing, can be specified either with deepspeed.checkpointing.configure or in the deepspeed_config file.

We assume that the webtext data was prepared in the previous step. To start training Megatron-LM GPT2 model with DeepSpeed applied, execute the following command to start training.

DeepSpeed enables training very large models effectively via the advanced ZeRO optimizer. In February 2020, we released a sub-set of optimizations from ZeRO in DeepSpeed that perform optimizer state partitioning. We refer to them as ZeRO-1. In May 2020, we extended ZeRO-1 in DeepSpeed to include additional optimizations from ZeRO including gradient and activation partitioning, as well as contiguous memory optimizations. We refer to this release as ZeRO-2.

ZeRO-2 significantly reduces the memory footprint for training large models which means large models can be trained with i) less model parallelism and ii) larger batch sizes. A lower model parallelism degree improves training efficiency by increasing the granularity of computations such as matrix multiplications where performance is directly related to the size of the matrices. Furthermore, less model parallelism also results in less communication between model parallel GPUs, which further boosts performance. Larger batch size has a similar effect of increasing the computational granularity as well as reducing communication, also resulting in better performance. Therefore, with DeepSpeed and ZeRO-2 integration into Megatron, we elevate the model scale and speed to an entirely new level compared to Megatron alone.

Figure 2: ZeRO-2 scales to 170 billion parameters, has up to 10x higher throughput, obtains super linear speedup, and improves usability by avoiding the need for code refactoring for models up to 13 billion parameters.

More concretely, DeepSpeed and ZeRO-2 excel in four aspects (as visualized in Figure 2), supporting an order-of-magnitude bigger models, up to 10x faster, with superlinear scalability, and improved usability to democratize large model training. These four aspects are detailed below.

Model size: State-of-the-art large models such as OpenAI GPT-2, NVIDIA Megatron-LM, Google T5, and Microsoft Turing-NLG have sizes of 1.5B, 8.3B, 11B, and 17B parameters respectively. ZeRO-2 provides system support to efficiently run models of 170 billion parameters, an order-of-magnitude bigger than these largest models (Figure 2, top left).

Speed: Improved memory efficiency powers higher throughput and faster training. Figure 2 (bottom left) shows system throughput of ZeRO-2 and ZeRO-1 (both combining ZeRO-powered data parallelism with NVIDIA Megatron-LM model parallelism) as well as using the state-of-the-art model parallelism approach Megatron-LM alone (baseline in Figure 2, bottom left). ZeRO-2 runs 100-billion-parameter models on a 400 NVIDIA V100 GPU cluster with over 38 teraflops per GPU and aggregated performance over 15 petaflops. For models of the same size, ZeRO-2 is 10x faster in training speed when compared with using Megatron-LM alone and 5x faster when compared with ZeRO-1.

Scalability: We observe superlinear speedup (Figure 2, top right), where the performance more than doubles when the number of GPUs are doubled. ZeRO-2 reduces the memory footprint of the model states as we increase the data parallelism degree, allowing us to fit larger batch sizes per GPU and resulting in better performance.

Democratizing large model training: ZeRO-2 empowers model scientists to train models up to 13 billion parameters efficiently without any model parallelism that typically requires model refactoring (Figure 2, bottom right). 13 billion parameters is larger than most of the largest state-of-the-art models (such as Google T5, with 11 billion parameters). Model scientists can therefore experiment freely with large models without worrying about model parallelism. In comparison, the implementations of classic data-parallelism approaches (such as PyTorch Distributed Data Parallel) run out of memory with 1.4-billion-parameter models, while ZeRO-1 supports up to 6 billion parameters for comparison.

Furthermore, in the absence of model parallelism, these models can be trained on low bandwidth clusters while still achieving significantly better throughput compared to using model parallelism. For example, the GPT-2 model can be trained nearly 4x faster with ZeRO powered data parallelism compared to using model parallelism on a four node cluster connected with 40 Gbps Infiniband interconnect, where each node has four NVIDIA 16GB V100 GPUs connected with PCI-E. Therefore, with this performance improvement, large model training is no longer limited to GPU clusters with ultra fast interconnect, but also accessible on modest clusters with limited bandwidth.

Updated: November 5, 2025

**Examples:**

Example 1 (unknown):
```unknown
git submodule update --init --recursive
```

Example 2 (python):
```python
def get_args():
    """Parse all the args."""

    parser = argparse.ArgumentParser(description='PyTorch BERT Model')
    parser = add_model_config_args(parser)
    parser = add_fp16_config_args(parser)
    parser = add_training_args(parser)
    parser = add_evaluation_args(parser)
    parser = add_text_generate_args(parser)
    parser = add_data_args(parser)

    # Include DeepSpeed configuration arguments
    parser = deepspeed.add_config_arguments(parser)
```

Example 3 (python):
```python
def initialize(args,
               model,
               optimizer=None,
               model_parameters=None,
               training_data=None,
               lr_scheduler=None,
               mpu=None,
               dist_init_required=True,
               collate_fn=None):
```

Example 4 (python):
```python
def setup_model_and_optimizer(args):
    """Setup model and optimizer."""

    model = get_model(args)
    optimizer = get_optimizer(model, args)
    lr_scheduler = get_learning_rate_scheduler(optimizer, args)

    if args.deepspeed:
        import deepspeed

        print_rank_0("DeepSpeed is enabled.")

        model, optimizer, _, lr_scheduler = deepspeed.initialize(
            model=model,
            optimizer=optimizer,
            args=args,
            lr_scheduler=lr_scheduler,
            mpu=mpu,
            dist_init_required=False
       )
```

---

## 1-Cycle Schedule

**URL:** https://www.deepspeed.ai/tutorials/one-cycle/

**Contents:**
- 1-Cycle Schedule
    - Contents
- 1-Cycle Schedule
- Prerequisites
- Overview
  - 1-Cycle Parameters
- Required Model Configuration Changes
  - PyTorch model
- Batch Scaling Example

This tutorial shows how to implement 1Cycle schedules for learning rate and momentum in PyTorch.

Recent research has demonstrated that the slow convergence problems of large batch size training can be addressed by tuning critical hyperparameters such as learning rate and momentum, during training using cyclic and decay schedules. In DeepSpeed, we have implemented a state-of-the-art schedule called 1-Cycle to help data scientists effectively use larger batch sizes to train their models in PyTorch.

To use 1-cycle schedule for model training, you should satisfy these two requirements:

The 1-cycle schedule operates in two phases, a cycle phase and a decay phase which span one iteration over the training data. For concreteness, we will review how the 1-cycle learning rate schedule works. In the cycle phase, the learning rate oscillates between a minimum value and a maximum value over a number of training steps. In the decay phase, the learning rate decays starting from the minimum value of the cycle phase. An example of 1-cycle learning rate schedule during model training is illustrated below.

The 1-Cycle schedule is defined by a number of parameters which allow users to explore different configurations. The literature recommends concurrent tuning of learning rate and momentum because they are correlated hyperparameters. We have leveraged this recommendation to reduce configuration burden by organizing the 1-cycle parameters into two groups:

The global parameters for configuring the 1-cycle phases are:

The local parameters for the hyperparameters are:

Although appropriate values cycle_min_lr and cycle_max_lr values can be selected based on experience or expertise, we recommend using learning rate range test feature of DeepSpeed to configure them.

To illustrate the required model configuration changes to use 1-Cycle schedule in model training, we will use a schedule with the following properties:

Note that these parameters are processed by DeepSpeed as session parameters, and so should be added to the appropriate section of the model configuration.

PyTorch versions 1.0.1 and newer provide a feature for implementing schedulers for hyper-parameters, called learning rate schedulers. We have implemented 1-Cycle schedule using this feature. You will add a scheduler entry of type “OneCycle” as illustrated below.

As example of how 1-Cycle schedule can enable effective batch scaling, we briefly share our experience with an internal model in Microsoft. In this case, the model was well-tuned for fast convergence (in data samples) on a single GPU, but was converging slowly to target performance (AUC) when training on 8 GPUs (8X batch size). The plot below shows model convergence with 8 GPUs for these learning rate schedules:

With 1Cycle, the model converges faster than the other schedules to the target AUC . In fact, 1Cycle converges as fast as the optimal 1-GPU training (not shown). For Fixed, convergence is about 5X slower (needs 5X more data samples). With LinearScale, the model diverges because the learning rate is too high. The plot below illustrates the schedules by reporting the learning rate values during 8-GPU training.

We see that the learning rate for 1Cycle is always larger than Fixed and is briefly larger than LinearScale to achieve faster convergence. Also 1Cycle lowers the learning rate later during training to avoid model divergence, in contrast to LinearScale. In summary, by configuring an appropriate 1-Cycle schedule we were able to effective scale the training batch size for this model by 8X without loss of convergence speed.

Updated: November 5, 2025

**Examples:**

Example 1 (unknown):
```unknown
"scheduler": {
    "type": "OneCycle",
    "params": {
        "cycle_first_step_size": 1000,
        "cycle_first_stair_count": 500,
        "cycle_second_step_size": 1000,
        "cycle_second_stair_count": 500,
        "decay_step_size": 1000,
        "cycle_min_lr": 0.0001,
        "cycle_max_lr": 0.0010,
        "decay_lr_rate": 0.001,
        "cycle_min_mom": 0.85,
        "cycle_max_mom": 0.99,
        "decay_mom_rate": 0.0
    }
},
```

---

## ZenFlow

**URL:** https://www.deepspeed.ai/tutorials/zenflow/

**Contents:**
- ZenFlow
    - Contents
- Configuration Changes
- Quick Start: Fine-tuning Example

ZenFlow is an extension of ZeRO-Offload that decouples and asynchronously updates gradients during training. It reduces CPU-induced stalls when using offload optimizers, enabling smoother and faster training. Like ZeRO-Offload, ZenFlow requires no code changes, only configuration updates in your DeepSpeed JSON file.

We recommend that you read the tutorials on Getting Started and ZeRO before stepping through this tutorial. ZenFlow builds on top of ZeRO-Offload, so shared setup details can be found there.

To enable ZenFlow, simply add a zenflow section under the existing zero_optimization block in your DeepSpeed config:

Each field in the zenflow block controls selective gradient update behavior:

Recommended: Use "auto" for select_strategy, select_interval, and update_interval to enable adaptive behavior with minimal tuning.

You can continue using the same training setup and launch script as in the ZeRO-Offload tutorial, since ZenFlow builds directly on top of ZeRO Offload.

A complete fine-tuning example using ZenFlow is available in DeepSpeedExamples – ZenFlow Fine-Tuning on GLUE

This example shows how to fine-tune a GPT model on the GLUE benchmark with:

Refer to the README.md in the folder for setup instructions, dataset preparation, and configuration details.

Congratulations! You have successfully enabled ZenFlow for stall-free offloading.

Updated: November 5, 2025

**Examples:**

Example 1 (unknown):
```unknown
{
  "zero_optimization": {
    "stage": 2,
    "offload_optimizer": {
      "device": "cpu",
      "pin_memory": true
    },
    "zenflow": {
      "topk_ratio": 0.05,
      "select_strategy": "auto",
      "select_interval": "auto",
      "update_interval": 4,
      "full_warm_up_rounds": 0,
      "overlap_step": true
    }
  }
}
```

Example 2 (unknown):
```unknown
cd DeepSpeedExamples/training/DeepSpeed-ZenFlow
bash finetune_gpt_glue.sh
```

---

## Installation Details

**URL:** https://www.deepspeed.ai/tutorials/advanced-install/

**Contents:**
- Installation Details
    - Contents
- Pre-install DeepSpeed Ops
- Install DeepSpeed from source
  - Conda environment for building from source
- Building for the correct architectures
- CUDA version mismatch
- Feature specific dependencies
- Pre-compiled DeepSpeed builds from PyPI

The quickest way to get started with DeepSpeed is via pip, this will install the latest release of DeepSpeed which is not tied to specific PyTorch or CUDA versions. DeepSpeed includes several C++/CUDA extensions that we commonly refer to as our ‘ops’. By default, all of these extensions/ops will be built just-in-time (JIT) using torch’s JIT C++ extension loader that relies on ninja to build and dynamically link them at runtime.

After installation, you can validate your installation and see which ops your machine is compatible with via the DeepSpeed environment report with ds_report or python -m deepspeed.env_report. We’ve found this report useful when debugging DeepSpeed install or compatibility issues.

Note: PyTorch must be installed before pre-compiling any DeepSpeed C++/CUDA ops. However, this is not required if using the default mode of JIT compilation of ops.

Sometimes we have found it useful to pre-install either some or all DeepSpeed C++/CUDA ops instead of using the JIT compiled path. In order to support pre-installation we introduce build environment flags to turn on/off building specific ops.

You can indicate to our installer (either install.sh or pip install) that you want to attempt to install all of our ops by setting the DS_BUILD_OPS environment variable to 1, for example:

DeepSpeed will only install any ops that are compatible with your machine. For more details on which ops are compatible with your system please try our ds_report tool described above.

If you want to install only a specific op (e.g., FusedLamb), you can toggle with DS_BUILD environment variables at installation time. For example, to install DeepSpeed with only the FusedLamb op use:

Available DS_BUILD options include:

To speed up the build-all process, you can parallelize the compilation process with:

This should complete the full build 2-3 times faster. You can adjust -j to specify how many cpu-cores are to be used during the build. In the example it is set to 8 cores.

You can also build a binary wheel and install it on multiple machines that have the same type of GPUs and the same software environment (CUDA toolkit, PyTorch, Python, etc.)

This will create a pypi binary wheel under dist, e.g., dist/deepspeed-0.3.13+8cd046f-cp38-cp38-linux_x86_64.whl and then you can install it directly on multiple machines, in our example:

After cloning the DeepSpeed repo from GitHub, you can install DeepSpeed in JIT mode via pip (see below). This installation should complete quickly since it is not compiling any C++/CUDA source files.

For installs spanning multiple nodes we find it useful to install DeepSpeed using the install.sh script in the repo. This will build a Python wheel locally and copy it to all the nodes listed in your hostfile (either given via --hostfile, or defaults to /job/hostfile).

When the code using DeepSpeed is used for the first time it’ll automatically build only the CUDA extensions, required for the run, and by default it’ll place them under ~/.cache/torch_extensions/. The next time the same program is executed these now precompiled extensions will be loaded form that directory.

If you use multiple virtual environments this could be a problem, since by default there is only one torch_extensions directory, but different virtual environments may use different setups (e.g., different Python or CUDA versions) and then the loading of a CUDA extension built by another environment will fail. Therefore, if you need to you can override the default location with the help of the TORCH_EXTENSIONS_DIR environment variable. So in each virtual environment you can point it to a unique directory and DeepSpeed will use it to save and load CUDA extensions.

You can also change it just for a specific run with:

If you encounter difficulties during compilation using the default system environment, you can try the conda environment provided, which includes the necessary compilation toolchain and PyTorch.

and try above install commands after activating it.

If you’re getting the following error:

when running deepspeed, that means that the CUDA extensions weren’t built for the card you’re trying to use it for.

When building from source DeepSpeed will try to support a wide range of architectures, but under jit-mode it’ll only support the architectures visible at the time of building.

You can build specifically for a desired range of architectures by setting a TORCH_CUDA_ARCH_LIST env variable:

It will also make the build faster when you only build for a few architectures.

This is also recommended to ensure your exact architecture is used. Due to a variety of technical reasons, a distributed PyTorch binary isn’t built to fully support all architectures, skipping binary compatible ones, at a potential cost of underutilizing your full card’s compute capabilities. To see which architectures get included during the DeepSpeed build from source - save the log and grep for -gencode arguments.

The full list of Nvidia GPUs and their compute capabilities can be found here.

If you’re getting the following error:

You have a misaligned version of CUDA installed compared to the version of CUDA used to compile Torch. A mismatch in the major version is likely to result in errors or unexpected behavior.

The easiest fix for this error is changing the CUDA version installed (check with nvcc --version) or updating the torch version to match the installed CUDA version (check with python3 -c "import torch; print(torch.__version__)").

We only require that the major version matches (e.g., 11.1 and 11.8). However, note that even a mismatch in the minor version may still result in unexpected behavior and errors, so it’s recommended to match both major and minor versions. When there’s a minor version mismatch, DeepSpeed will log a warning.

If you want to skip this check and proceed with the mismatched CUDA versions, use the following environment variable, but beware of unexpected behavior:

Some DeepSpeed features require specific dependencies outside the general dependencies of DeepSpeed.

Python package dependencies per feature/op please see our requirements directory.

We attempt to keep the system level dependencies to a minimum, however some features do require special system-level packages. Please see our ds_report tool output to see if you are missing any system-level packages for a given feature.

Updated: October 28, 2020

**Examples:**

Example 1 (unknown):
```unknown
pip install deepspeed
```

Example 2 (unknown):
```unknown
DS_BUILD_OPS=1 pip install deepspeed
```

Example 3 (unknown):
```unknown
DS_BUILD_FUSED_LAMB=1 pip install deepspeed
```

Example 4 (unknown):
```unknown
DS_BUILD_OPS=1 pip install deepspeed --global-option="build_ext" --global-option="-j8"
```

---

## Autotuning

**URL:** https://www.deepspeed.ai/tutorials/autotuning/

**Contents:**
- Autotuning
    - Contents
- Tuning scope and strategy
- Ease of use
- Example
  - Environment
  - Enabling Autotuning
  - Throughput Comparison
  - DeepSpeed Autotuning with AzureML

Make sure you’ve read the DeepSpeed tutorials on Getting Started and Zero Redundancy Optimizer before stepping through this tutorial.

One pain point in model training is to figure out good performance-relevant configurations such as micro-batch size to fully utilize the hardware and achieve a high throughput number. This configuration exploring process is commonly done manually but is important since model training is repeated many times and benefits from using a good configuration. Not only is the hand-tuning process time-consuming, but the outcome is hardware-dependent. This means that a good configuration on one hardware might not be the best on another different hardware. The user thus has to hand tune the configuration again. With DeepSpeed, there are more configuration parameters that could potentially affect the training speed, thus making it more tedious to manually tune the configuration.

The DeepSpeed Autotuner mitigates this pain point and automatically discovers the optimal DeepSpeed configuration that delivers good training speed. It not only reduces the time and resources users spend on tuning, but also can discover configurations better than hand-tuned methods. In this tutorial, we showcase the usage and benefits of the autotuning feature in DeepSpeed. For more details, please see the README.md.

The DeepSpeed Autotuner uses model information, system information, and heuristics to efficiently tune system knobs that affect compute and memory efficiencies, such as ZeRO optimization stages, micro-batch sizes, and many other ZeRO optimization configurations. Currently, the DeepSpeed Autotuner tunes ZeRO stages, micro-batch size per GPU, and ZeRO configurations (offloading is not yet supported) on top of other configurations such as optimizer, scheduler, fp16 defined by the user in the DeepSpeed configuration file. Note that ZeRO stages, micro-batch sizes, and other ZeRO configurations to tune are also configurable and can be overwritten by the user through the DeepSpeed configuration file. See Configuring Tuning Scope for details.

DeepSpeed Autotuning is easy to use, requiring no code change from DeepSpeed users. Compared to the original training script (deepspeed your_program.py <normal cl args> --deepspeed ds_config.json), invoking the autotuning feature in DeepSpeed only requires setting an autotuning flag after the DeepSpeed launcher (see Usage for details), and adding " autotuning": {"enabled": true} to the DeepSpeed configuration file. Users can further tailor the autotuning process by changing the autotuning configuration in the DeepSpeed configuration JSON file (See Autotuning Configuration for details).

We demonstrate the usage and benefit of autotuning using the training of a 0.77 billion parameter GPT2-large model from Hugging Face on 16 Nvidia V100 GPUs. For more examples, refer to autotuning in the DeepSpeedExamples repo. Note that autotuning works with any DeepSpeed-accelerated model training, not limited to Hugging Face models.

The training use fp16 and runs on 1 node with 16 Nvidia V100 GPUs. The autotuning uses the same hardware resource as the training. max_train_batch_size is not defined. The HF packages below are used.

HF examples require installing the transformers package from source:

The datasets package can be installed by pip install datasets

Below are the versions used in this test.

To enable the autotuning, add --autotuning run is added to the training script and add "autotuning": {"enabled": true} to the DeepSpeed configuration file. If the user training script uses DeepSpeed configuration parameters as training script arguments, the name mappings between the parameters in DeepSpeed configuration and the training script arguments must be provided in the arg_mappings dictionary in the autotuning section of the DeepSpeed configuration file.

DeepSpeed configuration file:

The table below shows the throughput (samples per second) comparison. The corresponding micro-batch size per GPU (mbs or tmbspg) and ZeRO stage used to achieve the throughput value is also shown in the parentheses. Assume the strategy users would use in the hand-tuning process is to start from mbs = 1 and increase mbs by 2 each time until running out of GPU memory.

Notation: Hugging Face (HF), DeepSpeed (DS), ZeRO stage (z), gradient accumulation steps (gas), micro-batch size per GPU (mbs or tmbspg).

The detailed HF + DS autotuning result summary is shown below.

Note that the performance metric used in autotuning is calculated using the timings captured within DeepSpeed forward, backward and step functions. The sum of these timings is less than the actual training step latency, thus the throughput metric values used by autotuning would be higher than the end-to-end throughput in training.

Tuning completed in 0:27:33.988447. Total number of experiments: 13.

As we can see the DeepSpeed Autotuner can select a better than hand-tuned configuration with a reasonable number of experiments. Examples in Autotuning Hugging Face Examples would demonstrate the effectiveness of autotuning across different models.

To try DeepSpeed autotuning with AzureML, please see the example here.

Updated: November 5, 2025

**Examples:**

Example 1 (unknown):
```unknown
git clone https://github.com/huggingface/transformers.git
    cd transformers
    pip install .
```

Example 2 (unknown):
```unknown
deepspeed --autotuning run --num_nodes=$NNODES --num_gpus=$NGPUS $HF_PATH/transformers/examples/pytorch/language-modeling/run_clm.py --deepspeed $DS_CONFIG\
    --model_name_or_path $MODEL_NAME \
    --dataset_name wikitext \
    --dataset_config_name wikitext-2-raw-v1 \
    --do_train \
    --do_eval \
    --fp16 \
    --per_device_train_batch_size $PER_DEVICE_TRAIN_BATCH_SIZE \
    --gradient_accumulation_steps $GRADIENT_ACCUMULATION_STEPS \
    --learning_rate 2e-5 \
    --num_train_epochs $NEPOCHS \
    --output_dir ${OUTPUT_DIR} \
    --overwrite_output_dir
```

Example 3 (unknown):
```unknown
{
  "train_micro_batch_size_per_gpu": "auto",
  "fp16": {
    "enabled": true
  },
  "autotuning": {
    "enabled": true,
    "arg_mappings": {
      "train_micro_batch_size_per_gpu": "--per_device_train_batch_size",
      "gradient_accumulation_steps ": "--gradient_accumulation_steps"
    }
  }
}
```

---

## Using PyTorch Profiler with DeepSpeed for performance debugging

**URL:** https://www.deepspeed.ai/tutorials/pytorch-profiler/

**Contents:**
- Using PyTorch Profiler with DeepSpeed for performance debugging
    - Contents
- Profile the model training loop
- Label arbitrary code ranges
- Profile CPU or GPU activities
- Profile memory consumption

This tutorial describes how to use PyTorch Profiler with DeepSpeed.

PyTorch Profiler is an open-source tool that enables accurate and efficient performance analysis and troubleshooting for large-scale deep learning models. The profiling results can be outputted as a .json trace file and viewed in Google’s Perfetto trace viewer (https://ui.perfetto.dev). Microsoft Visual Studio Code’s Python extension integrates TensorBoard into the code editor, including the support for the PyTorch Profiler.

For more details, refer to PYTORCH PROFILER.

Below shows how to profile the training loop by wrapping the code in the profiler context manager. The Profiler assumes that the training process is composed of steps (which are numbered starting from zero). PyTorch profiler accepts a number of parameters, e.g. schedule, on_trace_ready, with_stack, etc.

In the example below, the profiler will skip the first 5 steps, use the next 2 steps as the warm up, and actively record the next 6 steps. The profiler will stop the recording after the first two cycles since repeat is set to 2. For the detailed usage of the schedule, please refer to Using profiler to analyze long-running jobs.

The record_function context manager can be used to label arbitrary code ranges with user provided names. For example, the following code marks "model_forward" as a label:

The activities parameter passed to the Profiler specifies a list of activities to profile during the execution of the code range wrapped with a profiler context manager:

The example below profiles both the CPU and GPU activities in the model forward pass and prints the summary table sorted by total CUDA time.

By passing profile_memory=True to PyTorch profiler, we enable the memory profiling functionality which records the amount of memory (used by the model’s tensors) that was allocated (or released) during the execution of the model’s operators. For example:

self memory corresponds to the memory allocated (released) by the operator, excluding the children calls to the other operators.

Updated: November 5, 2025

**Examples:**

Example 1 (python):
```python
from torch.profiler import profile, record_function, ProfilerActivity

with torch.profiler.profile(
    schedule=torch.profiler.schedule(
        wait=5, # During this phase profiler is not active.
        warmup=2, # During this phase profiler starts tracing, but the results are discarded.
        active=6, # During this phase profiler traces and records data.
        repeat=2), # Specifies an upper bound on the number of cycles.
    on_trace_ready=tensorboard_trace_handler,
    with_stack=True # Enable stack tracing, adds extra profiling overhead.
) as profiler:
    for step, batch in enumerate(data_loader):
        print("step:{}".format(step))

        #forward() method
        loss = model_engine(batch)

        #runs backpropagation
        model_engine.backward(loss)

        #weight update
        model_engine.step()
        profiler.step() # Send the signal to the profiler that the next step has started.
```

Example 2 (unknown):
```unknown
with profile(record_shapes=True) as prof: # record_shapes indicates whether to record shapes of the operator inputs.
    with record_function("model_forward"):
        model_engine(inputs)
```

Example 3 (unknown):
```unknown
with profile(activities=[
        ProfilerActivity.CPU, ProfilerActivity.CUDA], record_shapes=True) as prof:
    with record_function("model_forward"):
        model_engine(inputs)

print(prof.key_averages().table(sort_by="cuda_time_total", row_limit=10))
```

Example 4 (unknown):
```unknown
with profile(activities=[ProfilerActivity.CUDA],
        profile_memory=True, record_shapes=True) as prof:
    model(inputs)

print(prof.key_averages().table(sort_by="self_cuda_memory_usage", row_limit=10))
```

---

## DeepSpeed Data Efficiency: A composable library that makes better use of data, increases training efficiency, and improves model quality

**URL:** https://www.deepspeed.ai/tutorials/data-efficiency/

**Contents:**
- DeepSpeed Data Efficiency: A composable library that makes better use of data, increases training efficiency, and improves model quality
    - Contents
- 1. Curriculum Learning
  - 1.1 What is Curriculum Learning
  - 1.2 When to use Curriculum Learning
  - 1.3 How to use Curriculum Learning
    - 1.3.1 GPT-3 and BERT pretraining
    - 1.3.2 GPT-2 finetuning
- 2. Random layerwise token dropping (random-LTD)
  - 2.1 What is random-LTD

What is DeepSpeed Data Efficiency: DeepSpeed Data Efficiency is a library purposely built to make better use of data, increases training efficiency, and improves model quality.

Why use DeepSpeed Data Efficiency: DeepSpeed Data Efficiency offers novel data efficiency techniques to achieve better training efficiency and/or better model quality. DeepSpeed Data Efficiency takes extensibility, flexibility, and composability into consideration, which makes it easier to customize the techniques, apply the techniques to various training tasks, and compose multiple techniques together. We highly recommend you also to read our blog to learn more about (at a high level) why we build DeepSpeed Data Efficiency and what benefits it provides to users. Additional technical details can be found in our papers, “Random-LTD: Random and Layerwise Token Dropping Brings Efficient Training for Large-scale Transformers” which describes the random-LTD technique, and “DeepSpeed Data Efficiency: Improving Deep Learning Model Quality and Training Efficiency via Efficient Data Sampling and Routing” which describes the curriculum learning technique and overall DeepSpeed Data Efficiency framework.

How to use DeepSpeed Data Efficiency: In the following tutorial, the first two sections will describe the data efficiency techniques supported by the library. The third section will describe how to compose the two techniques to achieve even better training efficiency/model quality.

Curriculum learning (proposed by Yoshua Bengio et al.) aims to improve training convergence speed by presenting relatively easier or simpler examples earlier during training. Building a curriculum learning solution usually requires two components: the difficulty metric (i.e., how to quantify the difficulty of each data sample) and the pacing function (i.e., how to decide the curriculum difficulty range when sampling next training data batch).

Curriculum learning has been successfully applied to various training tasks (see details in for example this survey paper), and last year we also released a specific curriculum learning technique (sequence length warmup) for GPT-style model pretraining (see technical details in our paper “The Stability-Efficiency Dilemma: Investigating Sequence Length Warmup for Training GPT Models” published in NeurIPS 2022 and the tutorial for this legacy curriculum learning feature). This new general curriculum learning library inside DeepSpeed Data Efficiency enables users to employ curriculum learning to their models at maximum extensibility: users can easily analyze, index, and sample their training data based on various customizable strategies. Using this library, we were able to explore different CL strategies for GPT-3 and BERT pretraining and identify the best solution that provides up to 1.5x data saving while still maintaining similar model quality.

The examples_deepspeed/data_efficiency directory in our Megatron-DeepSpeed repo includes our examples of how to apply curriculum learning to GPT-3 and BERT pretraining. There are 3 steps: data analysis, pretraining, and eval/finetuning.

Data analysis: Curriculum learning requires a data analysis before pretraining that calculate the difficulty of each data sample (based on the metric provided by user), and build an index that map difficulty value to corresponding data samples. (There are exceptions: for example the truncation-based sequence length metric can be achieved by data postprocessing without data analysis.) We provide a data analyzer to perform the offline CPU-only data analysis.

examples_deepspeed/data_efficiency/gpt/ds_analyze_*.sh and examples_deepspeed/data_efficiency/bert/ds_analyze_*.sh are example scripts for GPT-3 and BERT’s data analysis. Our data analyzer employs a simple Map-Reduce scheme. First, at the Map stage the ds_analyze_*_data_map.sh is used to split the dataset and compute the difficulty value for each data sample. User would need to provide a function to compute the metric (we implement ours in examples_deepspeed/data_efficiency/analyze_data.py), the raw training dataset, and other configurations such as number of CPU nodes and number of threads per node. Then the data analyzer will automatically splits the dataset based on number of workers, compute the difficulty values in a batched fashion, and write the results to two indexes: one index maps each data sample to its difficulty value, and another index maps each distinct difficulty value to the corresponding samples. Second, at the Reduce stage the ds_analyze_*_data_reduce.sh is used to merge the index files produced by all workers. One thing to note is that in order to enable speedup by distribution yet still being able to merge all the output, the Map stage will potentially generate a lot of output files, which is proportional to number of CPU nodes, number of threads per node, and number of possible metric values. Thus to avoid generating too much output files, we recommend to start with a smaller number of nodes/threads (in the output log we provide an estimate required time for users to judge if they want to increase number of workers), and we recommend to limit number of possible difficulty values when designing your difficulty metric (our experience shows that a few thousands of distinct values is already sufficient to enjoy the benefit of curriculum learning).

Pretraining examples_deepspeed/data_efficiency/gpt/pretrain and examples_deepspeed/data_efficiency/bert/pretrain include the example pretraining scripts with curriculum learning feature. Several changes are needed to enable curriculum learning during pretraining: (1) User need to provide a DeepSpeed json config file which includes configurations for curriculum learning (see list of configuration for details). We provide tested example configurations in examples_deepspeed/data_efficiency/gpt/pretrain/ds_pretrain_gpt_1.3B_dense_run.sh and examples_deepspeed/data_efficiency/bert/pretrain/ds_pretrain_bert_336M_run.sh. (2) When initializing the DeepSpeed engine via deepspeed.initialize, user needs to provide the train dataset and use the dataloader returned by the initialization (this dataloader includes the curriculum learning capability). We provide an example implementation of this change in megatron/training.py function setup_model_and_optimizer. (3) If the curriculum learning metric requires data postprocessing (such as truncation-based sequence length), user needs to use the DeepSpeed engine’s set_data_post_process_func API to provide the postprocessing function. We provide an example implementation of this change in megatron/training.py, pretrain_bert.py, and pretrain_gpt.py. (4) If the curriculum learning metric requires a custom scheduling strategy (the pacing function), user needs to use the DeepSpeed engine’s set_custom_curriculum_learning_schedule API to provide the function to update the max accepted difficulty during training. DeepSpeed engine will provide a global train step input to this callback function.

Eval/finetuning examples_deepspeed/data_efficiency/gpt/eval/ and examples_deepspeed/data_efficiency/bert/finetune include the example scripts for GPT-3 model’s zero-/few-shot evaluation and BERT model’s finetuning. Our paper includes the reference eval/finetune results if you follow our example scripts to perform the pretraining/eval/finetuning.

The data_efficiency/gpt_finetuning directory in our DeepSpeedExamples repo includes our examples of how to apply curriculum learning to GPT-2 finetuning. data_efficiency/gpt_finetuning/finetune/ds_finetune_gpt2_run.sh is the example finetuning script. For CL metrics that require data analysis (e.g., the vocabulary rarity metric), you need to first use data_efficiency/gpt_finetuning/finetune/ds_analyze_gpt_data_* to analyze and index the dataset, similar to the GPT-3 pre-training case described above in 1.3.1.

Random-LTD is an efficient token drop method applied to each layer with random assignment. Precisely, for each layer, as compared to the baseline, random-LTD randomly selects a subset of the tokens and feeds them into the transformer layer. Afterward, we combine the output of transformer layer with the dropped tokens to recover the full sequence length. Thus, the next layer still receives the full sequence and can repeat this process. For more technical details please read our random-LTD paper.

When you want to pretrain/fine-tune a transformer-based model, it is always a good idea to try random-LTD, as it can achieve a better performance than the standard baseline training given the same amount of computational cost. If you have limited resources, random-LTD achieves similar accuracy as the original baseline method with up to 33.3% theoretical cost saving and up to 25.6% wall-clock time saving. Particularly, if you need to train a much larger model with >=24 layers and with >=2048 sequence length, our method will be much more efficient than baseline.

The examples_deepspeed/data_efficiency directory in our Megatron-DeepSpeed repo includes our examples of how to apply random-LTD to GPT-3 and BERT pretraining.

examples_deepspeed/data_efficiency/gpt/pretrain and examples_deepspeed/data_efficiency/bert/pretrain include the example pretraining scripts with random-LTD feature. Several changes are needed to enable random-LTD during pretraining: (1) User need to provide a DeepSpeed json config file which includes configurations for random-LTD (see list of configuration for details). We provide tested example configurations in examples_deepspeed/data_efficiency/gpt/pretrain/ds_pretrain_gpt_1.3B_dense_run.sh and examples_deepspeed/data_efficiency/bert/pretrain/ds_pretrain_bert_336M_run.sh. (2) After initializing the DeepSpeed engine via deepspeed.initialize, user needs to use the convert_to_random_ltd API to convert and wrap the model layers in order to enable the random-LTD feature. We provide an example implementation of this change in megatron/training.py function setup_model_and_optimizer. (3) In order for random-LTD to understand the input argument mapping of the forward function, user need to change all the input arguments (except the hidden_states input) into keyword/named argument. For example, in megatron/model/transformer.py we changed the forward function from def forward(self, hidden_states, attention_mask, encoder_output=None, enc_dec_attn_mask=None, layer_past=None, get_key_value=False): to def forward(self, hidden_states, attention_mask=None, encoder_output=None, enc_dec_attn_mask=None, layer_past=None, get_key_value=False):. (4) When saving model checkpoints, (especially if the state dictionary has non-traditional structure) user needs to use the remove_random_ltd_state_dict API to convert the random-LTD-wrapped layers back to original model layers. We provide an example implementation of this change in megatron/model/language_model.py.

For eval/finetuning of the pretrained model, see previous section about how to use our example scripts.

The data_efficiency directory in our DeepSpeedExamples repo includes our examples of how to apply random-LTD to GPT-2 and ViT finetuning.

Just like pretraining case, similar changes are required to enable random-LTD for finetuning: (1) DeepSpeed json config file. (2) Use the convert_to_random_ltd API to convert and wrap the model layers. (3) When saving model checkpoints, use the remove_random_ltd_state_dict API to convert the random-LTD-wrapped layers back to original model layers.

One can run our GPT finetuning example by:

And the reference final result is:

One can run our ViT finetuning example by:

And the reference final result is:

The examples_deepspeed/data_efficiency directory in our Megatron-DeepSpeed repo includes our examples of how to compose curriculum learning random-LTD, and apply both of them to GPT-3 and BERT pretraining.

The changes needed are the same as described in previous two sections, since DeepSpeed Data Efficiency already handles the complexity when composing the two techniques. However, one thing to note is that since both random-LTD and some of the curriculum learning metrics will change the sequence length, it could require some extra code to calculate the effective sequence length at each step. We provide an example implementation of this change in megatron/training.py function train where we calculate the actual_seq_length.

The data_efficiency/gpt_finetuning directory in our DeepSpeedExamples repo includes our examples of how to compose curriculum learning random-LTD for GPT-2 finetuning. data_efficiency/gpt_finetuning/finetune/ds_finetune_gpt2_run.sh is the example finetuning script.

Updated: November 5, 2025

**Examples:**

Example 1 (unknown):
```unknown
DeepSpeedExamples/data_efficiency/gpt_finetuning$ pip install -r requirement.txt
DeepSpeedExamples/data_efficiency/gpt_finetuning$ bash ./bash_script/run_base_random_ltd.sh
DeepSpeedExamples/data_efficiency/gpt_finetuning$ bash ./bash_script/run_medium_random_ltd.sh
```

Example 2 (unknown):
```unknown
For run_base_random_ltd.sh:
End of training epoch 3 step 1344 consumed_token 2148032 best perplexity 22.552324221233757 time 0.17486039188173083 hr

For run_medium_random_ltd.sh:
End of training epoch 3 step 1373 consumed_token 2147024 best perplexity 17.332243199130996 time 0.4661190489927928 hr
```

Example 3 (unknown):
```unknown
DeepSpeedExamples/data_efficiency/vit_finetuning$ pip install -r requirement.txt
DeepSpeedExamples/data_efficiency/vit_finetuning$ bash ./bash_script/run_cifar.sh
DeepSpeedExamples/data_efficiency/vit_finetuning$ bash ./bash_script/run_imagenet.sh
```

Example 4 (unknown):
```unknown
For run_cifar.sh:
13 epoch at time 480.6546013355255s | reserved_length 197
iter 5474 | LR [0.0001]| val_acc 97.97000122070312 | layer_token 305784192
```

---

## DeepSpeed Accelerator Setup Guides

**URL:** https://www.deepspeed.ai/tutorials/accelerator-setup-guide/

**Contents:**
- DeepSpeed Accelerator Setup Guides
    - Contents
- Contents
- Introduction
- Intel Architecture (IA) CPU
- Installation steps for Intel Architecture CPU
- How to launch DeepSpeed on Intel Architecture CPU
- Install with Intel Extension for PyTorch and oneCCL
- Optimize LLM inference with Intel Extension for PyTorch
- More examples for using DeepSpeed on Intel CPU

DeepSpeed supports different accelerators from different companies. Setup steps to run DeepSpeed on certain accelerators might be different. This guide allows user to lookup setup instructions for the accelerator family and hardware they are using.

DeepSpeed supports CPU with Intel Architecture instruction set. It is recommended to have the CPU support at least AVX2 instruction set and recommend AMX instruction set.

DeepSpeed has been verified on the following CPU processors:

To install DeepSpeed on Intel Architecture CPU, use the following steps:

Install gcc compiler DeepSpeed requires gcc-9 or above to build kernels on Intel Architecture CPU, install gcc-9 or above.

Install numactl DeepSpeed use numactl for fine grain CPU core allocation for load-balancing, install numactl on your system. For example, on Ubuntu system, use the following command: sudo apt-get install numactl

Install PyTorch pip install torch

Install DeepSpeed pip install deepspeed

DeepSpeed can launch on Intel Architecture CPU with default deepspeed command. However, for compute intensive workloads, Intel Architecture CPU works best when each worker process runs on different set of physical CPU cores, so worker process does not compete CPU cores with each other. To bind cores to each worker (rank), use the following command line switch for better performance.

This switch would automatically detect the number of CPU NUMA node on the host, launch the same number of workers, and bind each worker to cores/memory of a different NUMA node. This improves performance by ensuring workers do not interfere with each other, and that all memory allocation is from local memory.

If a user wishes to have more control on the number of workers and specific cores that can be used by the workload, user can use the following command line switches.

This would start 4 workers for the workload. The core list range will be divided evenly between 4 workers, with worker 0 take 0-13, worker 1, take 14-27, worker 2 take 32-45, and worker 3 take 46-59. Core 28-31,60-63 are left out because there might be some background process running on the system, leaving some idle cores will reduce performance jitting and straggler effect.

Launching DeepSpeed model on multiple CPU nodes is similar to other accelerators. We need to specify impi as launcher and specify --bind_cores_to_rank for better core binding. Also specify slots number according to number of CPU sockets in host file.

Although not mandatory, Intel Extension for PyTorch and Intel oneCCL provide better optimizations for LLM models. Intel oneCCL also provide optimization when running LLM model on multi-node. To use DeepSpeed with Intel Extension for PyTorch and oneCCL, use the following steps:

The following steps are to install oneCCL binding for PyTorch. This is suggested if you are running DeepSpeed on multiple CPU node, for better communication performance. On single node with multiple CPU socket, these steps are not needed.

Install Intel oneCCL binding for PyTorch python -m pip install oneccl_bind_pt -f https://developer.intel.com/ipex-whl-stable-cpu

Install Intel oneCCL, this will be used to build direct oneCCL kernels (CCLBackend kernels)

Then set the environment variables for Intel oneCCL (assuming using conda environment).

Intel Extension for PyTorch compatible with DeepSpeed AutoTP tensor parallel inference. It allows CPU inference to benefit from both DeepSpeed Automatic Tensor Parallelism, and LLM optimizations of Intel Extension for PyTorch. To use Intel Extension for PyTorch, after calling deepspeed.init_inference, call

to get model optimzied by Intel Extension for PyTorch.

Refer to LLM examples for more code samples of running inference with DeepSpeed on Intel CPU.

DeepSpeed XPU accelerator supports Intel® Data Center GPU Max Series.

DeepSpeed has been verified on the following GPU products:

To install DeepSpeed on Intel XPU, use the following steps:

Install PyTorch, Intel extension for pytorch, Intel oneCCL Bindings for PyTorch. These packages are required in xpu_accelerator for torch functionality and performance, also communication backend on Intel platform. The recommended installation reference: https://intel.github.io/intel-extension-for-pytorch/index.html#installation?platform=gpu.

DeepSpeed can be launched on Intel XPU with deepspeed launch command. Before that, user needs activate the oneAPI environment by: source <oneAPI installed path>/setvars.sh

To validate the XPU availability and if the XPU accelerator is correctly chosen, here is an example:

Refer to LLM examples, Megatron-DeepSpeed training examples for more code samples of running LLM with DeepSpeed on Intel XPU.

DeepSpeed has been verified on the following Huawei Ascend NPU products:

The following steps outline the process for installing DeepSpeed on an Huawei Ascend NPU:

Install PyTorch pip install torch torch_npu

You can view the installation results using the ds_report command, Here is an example:

To validate the Huawei Ascend NPU availability and if the accelerator is correctly chosen, here is an example(Huawei Ascend NPU detection is automatic starting with DeepSpeed v0.12.6):

To perform model training across multiple Huawei Ascend NPU cards using DeepSpeed, see the examples provided in DeepSpeed Examples.

PyTorch models can be run on Intel® Gaudi® AI accelerator using DeepSpeed. Refer to the following user guides to start using DeepSpeed with Intel Gaudi:

Updated: November 5, 2025

**Examples:**

Example 1 (unknown):
```unknown
deepspeed --bind_cores_to_rank <deepspeed-model-script>
```

Example 2 (unknown):
```unknown
deepspeed --num_accelerators <number-of-workers> --bind_cores_to_rank --bind_core_list <comma-seperated-dash-range> <deepspeed-model-script>
```

Example 3 (unknown):
```unknown
deepspeed --num_accelerators 4 --bind_cores_to_rank --bind_core_list <0-27,32-59> inference.py
```

Example 4 (unknown):
```unknown
# hostfile content should follow the format
# worker-1-hostname slots=<#sockets>
# worker-2-hostname slots=<#sockets>
# ...

deepspeed --hostfile=<hostfile> --bind_cores_to_rank --launcher impi --master_addr <master-ip> <deepspeed-model-script>
```

---




### 09

# Deepspeed - 09

**Pages:** 2

---

## DeepSpeed-MoE for NLG: Reducing the training cost of language models by 5 times

**URL:** https://www.deepspeed.ai/2021/12/09/deepspeed-moe-nlg.html

**Contents:**
- DeepSpeed-MoE for NLG: Reducing the training cost of language models by 5 times
    - Contents
- MoE based NLG model architecture
- MoE training infrastructure and dataset
- MoE leads to better quality for NLG models
- Same quality with 5x less training cost
- MoE for Inference
- Conclusion and Release
- Acknowledgement

Autoregressive transformer-based natural language generation (referred to as NLG in the rest of the blog) models can offer convincing solutions to a broad range of language tasks from document summarization, headline generation, question and answering to even generating code in a wide variety of programming languages. Due to the general applicability of these models, improving their quality has been of great interest for both academia and industry alike.

The quality of NLG improves with the increase in model size. However, today we are getting close to the limit of what the current generation of hardware can do. The Megatron-Turing NLG 530B model took 3 months to train on over 2K A100 GPUs on the NVIDIA Selene Supercomputer, consuming over 3 million GPU hours. Another 3 to 5 times of increase in model size would be infeasible within a reasonable timeframe. Given the exorbitant compute resources required to train the state-of-art NLG models, a natural question to ask is: “Is it possible to make non-trivial improvement to model quality without increasing the compute cost?” Or equivalently, “Is it possible to produce model with similar quality using 3 to 5 times less resources?”

Recent works like GShard and Switch Transformers have shown that Mixture of Experts (MoE) model structure reduces large model training cost significantly for transformer-based encoder-decoder models. An MoE model contains a set of sparsely gated experts. During training and inference, only a subset of these experts is activated for each input token. Therefore, the model could scale to billions of parameters without a proportional increase in the computation. Despite showing promising results, the effectiveness of MoE for the much more computation intensive NLG family models remains mostly unknown.

Given the tremendous compute and energy requirements for training NLG family of models, we explore the opportunities that MoE presents to reduce their training cost. We show that MoE can be applied to NLG family of models to significantly improve their model quality with the same training cost. Alternatively, it can achieve 5x reduction in training cost to achieve the same model quality of a dense NLG model. For example, by applying MoE we achieved the model quality of a 6.7B parameter dense NLG model at the cost of training a 1.3B parameter dense model, thanks to the sparse structure of MoE.

Assuming the scaling holds, the results have the potential to completely transform the large model training landscape in terms of cost. For example, a trillion-parameter dense model can be potentially trained at the cost of a 200B parameter (like GPT-3) sized dense model, translating to millions of dollars in training cost reduction and energy savings (Brown et al., 2020, Language models are few-shot learners).

To create an MoE based NLG model we studied the GPT like transformer-based NLG model. To complete training in a reasonable timeframe, the following models are selected: 350M (24 layers, 1024 hidden size, 16 attention heads), 1.3B (24 layers, 2048 hidden size, 16 attention heads), and 6.7B (32 layers, 4096 hidden size, 32 attention heads). We use “350M+MoE-128” to denote a MoE model that uses 350M dense model as the base model and adds 128 experts on every other feedforward layer. That is to say, there are in total 12 MoE layers for both 350M+MoE-128 and 1.3B+MoE-128.

We use a gating function to activate a subset of experts in the MoE layer for each token. Specifically, in our experiments, only the top-1 expert is selected. Therefore, during both training and inference, our MoE model will have the same number of parameters to be activated for each token as their dense part. For example, our 1.3B+MoE-128 will only activate 1.3B parameter per token, and the amount of training computation per token will be similar to a 1.3B dense model.

We pre-trained both the dense and MoE version of the above models using DeepSpeed on 128 A100 GPUs. DeepSpeed uses a combination of data parallel and expert parallel training to effectively scale the MoE model training.

We used the same training data as described in the MT-NLG blog. For a fair comparison, we use 300B tokens to train both the dense model and the MoE model.

Figure 1 shows that the validation loss for the MoE versions of the model is significantly better than their dense counter parts. Furthermore, notice that the validation loss of the MoE model, 350M+MoE-128, is on par with the validation loss of the 1.3B dense model with 4x larger base. This is also true for 1.3B+MoE-128 in comparison with 6.7B dense model with 5x larger base. Furthermore, the model quality is on par not only for the validation loss but also for a wide variety of 6 ZeRO-shot evaluation tasks as shown in Table 1, demonstrating that these models in fact have very similar model quality.

Figure 1: Token-wise validation loss curves for dense and MoE NLG models with different model sizes.

Table 1: ZeRO-shot evaluation results (last six columns) for different dense and MoE NLG models. All ZeRO-shot evaluation results use the accuracy metric.

As we saw from the results above, adding MoE with 128 experts to the NLG model significantly improves the quality of the NLG model. However, these experts do not change the compute requirements of the model as each token is only processed by a single expert. Therefore, the compute requirements for dense model and its corresponding MoE models with the same base are similar.

More concretely, a 1.3B+MoE-128 model training requires roughly the same amount of compute operations as 1.3B dense, while offering much better model quality. Furthermore, our results show that by applying MoE we can achieve the model quality of a 6.7B parameter dense model at the training cost of 1.3B parameter dense model, resulting in an effective training compute reduction of 5x.

This compute cost reduction can directly be translated into throughput gain, training time and training cost reduction by leveraging the efficient DeepSpeed MoE training system. Table 2 shows the training throughput of the 1.3B+MoE-128 model in comparison to the 6.7B dense model on 128 NVIDIA A100 GPUs.

Table 2: Training throughput (on 128 A100 GPUs) comparing MoE based model vs dense model that can both achieve the same model quality.

The training cost reduction of MoE is not free and comes at the expense of increasing the total number of parameters required to achieve the same model quality compared to dense models. The 1.3B+MoE-128 have roughly 8x the number of parameters (52B) compared to the 6.7B dense model. So, does this mean inference will be 8x slower than the dense model, since inference is generally limited by the time taken to read all the model parameters, especially for small batch sizes?

Not quite. Note that in the 1.3B+MoE-128 model, each token is processed by a unique expert per MoE layer, and the total number of parameters used in processing the token is just 1.3B. This can in theory result in even faster inference than the quality-equivalent dense 6.7B model because of 5x less compute and parameter read. In reality though, the number of tokens in a batch during inference is generally larger than 1. Inferencing, a long sequence length or a non-unit batch size may require loading all the experts, increasing the total number of parameters loaded by 8x compared to the quality-equivalent dense model. Therefore, achieving good inference performance with MoE is still challenging even though the parameters used and the computation incurred per token is small compared to the quality-equivalent dense model.

Nonetheless, we believe that it is possible to use different forms of parallelism to leverage massive memory bandwidth by scaling across a large number of devices to speed up MoE inference, making it comparable or faster than quality-equivalent dense models for extended inference scenarios and creating opportunities to make MoE based models cost efficient for inference in addition to training.

We demonstrate that MoE based models can be applied to NLG task, reducing the training cost by 5x compared to dense, autoregressive transformer-based models like GPT-3 and MT-NLG 530B. Through MoE based low-cost training we hope to make high quality language models accessible to a broad audience, even with limited compute resources.

To this end we are releasing our end-to-end pipeline for training MoE based NLG models, along with specific example scripts and tutorial to help get started with our pipeline. We look forward to the application and the innovations that this may bring to the deep learning community.

This work was done in collaboration with Brandon Norick, Zhun Liu, Xia Song from the Turing Team, and Young Jin Kim, Alex Muzio, Hany Hassan Awadalla from Z-Code Team. We also thank Luis Vargas, Umesh Madan, Gopi Kumar, Andrey Proskurin and Mikhail Parakhin for their continuous support and guidance.

Updated: December 9, 2021

---

## ZeRO-Inference: Democratizing massive model inference

**URL:** https://www.deepspeed.ai/2022/09/09/zero-inference.html

**Contents:**
- ZeRO-Inference: Democratizing massive model inference
    - Contents
- Introduction
- How ZeRO-Inference works
  - Offload all model weights
  - Optimizations
  - Alternative approach: Host some model weights in GPU memory
- Model Scaling on 1 GPU
- Token Generation Performance
  - Models

The current trends in artificial intelligence (AI) domains such as image, speech, and natural language, demonstrate that model quality can be improved by increasing model size. In natural language processing, for example, the state-of-the-art (SOTA) model has grown from 300 million parameters (Bert-Large) to 500 billion parameters (Megatron-Turing-530B) in less than four years. However, this dramatic growth in model sizes has significantly increased the GPU cost to train, finetune or inference these models, making them unaffordable to most users. To democratize access to AI innovations, large organizations, such as Hugging Face (BigScience), Meta, and Yandex have recently publicly released pre-trained massive models. Unfortunately, even these publicly available models are not broadly usable because many users cannot afford the dozens of GPUs required to fit them for inference computation. For example, half-precision inference computation on Megatron-Turing-530B (SOTA model for natural language) requires at least 40 A100-40GB GPUs, which is unaffordable to many students, model scientists, hobbyists, and small businesses that could benefit from using these powerful models. And so, a real concern is that if the dramatic increase in model sizes continues, then a growing fraction of users could be excluded from the benefits of these AI innovations.

DeepSpeed, a part of Microsoft’s AI at Scale Initiative, has developed the ZeRO-Inference technology to address these obstacles to AI democratization. ZeRO-Inference comes from the family of ZeRO technologies, which are a collection of powerful memory and parallelism optimizations for efficient large scale model training and inference on modern GPU clusters. DeepSpeed had previously developed ZeRO-Infinity, a technology that leverages heterogeneous memory (GPU, CPU, and NVMe) to efficiently scale model training to extreme levels. ZeRO-Inference adapts and optimizes ZeRO-Infinity techniques for model inference on GPUs by hosting the model weights in CPU or NVMe memory, thus hosting no (zero) weights in GPU. This approach is inspired by the observation that the aggregate capacity of CPU and NVMe memories in most commodity computing devices (e.g., laptops, desktops, workstations, etc.) is on the order of terabytes and sufficient to host the largest known models for inference computation. By leveraging this non-GPU memory, ZeRO-Inference enables inference computation of massive models (with hundreds of billions of parameters) on as few as a single GPU, thereby making massive model inference accessible to almost everyone. Moreover, by dramatically reducing GPU memory requirements with CPU or NVMe memory which are significantly cheaper, it significantly reduces the cost of massive model inference, offering an affordable inference path to SOTA models.

The massive computational requirements of large model inference means that accelerators like GPUs are required for efficient execution. Therefore, an important design decision for large model inference on limited GPU budget is how to apportion GPU memory among model weights, inference inputs, and intermediate results.

ZeRO-Inference pins the entire model weights in CPU or NVMe (whichever is sufficient to accommodate the full model) and streams the weights layer-by-layer into the GPU for inference computation. After computing a layer, the outputs are retained in GPU memory as inputs for the next layer, while memory consumed by the layer weights is released for use by the next layer. Thus, model inference time is composed of the time to compute the layers on GPU, and the time to fetch the layers over PCIe. For large model inference, this approach provides scaling and efficiency benefits, as explained below.

ZeRO-Inference offers scaling benefits in two ways. First, by keeping just one (or a few) model layers in GPU memory at any time, ZeRO-Inference significantly reduces the amount of GPU memory required to inference massive models. For current SOTA models which have about a hundred layers (e.g., 96 and 105 layers in GPT3-175B and Megatron-Turing-530B respectively), ZeRO-Inference reduces the GPU memory requirements by up to two orders of magnitude. For example, with ZeRO-Inference, GPU memory consumption of Megaton-Turing-530B for half-precision inference drops from 1TB to 10GB. Second, by fitting the model into CPU or NVMe memory which are orders of magnitude cheaper than GPU memory, ZeRO-Inference makes scaling to future SOTA models (e.g., with trillions or tens-of-trillions of parameters) more affordable compared to approaches that fit the entire model into GPU memory.

ZeRO-Inference delivers efficient computation for throughput-oriented inference applications despite the latency of fetching model weights from CPU or NVMe over PCIe interconnect. The primary reason for this is that by limiting GPU memory usage of the model to one or a few layers of weights, ZeRO-Inference can use the majority of GPU memory to support a large amount of input tokens in the form of long sequences or large batch sizes. A large model layer requires a significant amount of computation, especially when processing inputs with many input tokens. For example, one GPT3-175B layer requires about 7 TFlops to process an input of batch size 1 and sequence length of 2048. Therefore, for inference scenarios with long sequence length and large batch sizes, the computation time dominates the latency of fetching model weights, which ultimately improves efficiency. In summary, ZeRO-Inference’s strategy to utilize GPU memory to support large number of input tokens results in high performance inference for large models.

To further improve system efficiency, ZeRO-Inference leverages two additional optimizations to reduce the latency of fetching layer weights from CPU or NVMe memory into GPU memory.

The first optimization involves overlapping the fetch of a layer with the computation of an earlier layer, a.k.a., layer prefetching. Layer prefetching allows ZeRO-Inference to hide portions of the transfer latency of the prefetched layers. This is especially useful when computation time is not large enough or cannot be sufficiently increased (e.g., with larger batch size) to dominate the latency of fetching layer weights.

The second optimization, which is applicable for inference on multiple GPUs, involves parallelizing the fetch of each layer across multiple GPUs by using each GPU to fetch only a portion of the layer. Employing the aggregate PCIe links of the GPUs in this manner essentially increases the transfer bandwidth linearly, thus reducing the latency. With this approach, fetching layers into GPU memory occurs in two phases. First, each GPU independently fetches a partition of the layer over PCIe into its memory. At this point, only a partition of the layer will be resident on each GPU. Next, each GPU assembles the full layer for computation by fetching the missing layer pieces from other GPUs over the high-bandwidth GPU-GPU interconnect (e.g., NVLink, xGMI, etc.). Since GPU-GPU interconnect bandwidth is typically over an order of magnitude higher than PCIe bandwidth, efficient multi-GPU or multi-node communication primitives, such as NCCL or RCCL all-gather, can be used to efficiently assemble the full layer on all GPUs with negligible latency compared to the PCIe latency.

An alternative approach to ZeRO-Inference is to pin as many of the model weights as possible into GPU memory and fetch the remainder (from CPU or NVMe) when needed for computation. A benefit of this approach is avoidance of the latency of fetching weights that are already pinned in GPU memory. However, this approach has two downsides: (i) the latency savings for hundred-billion parameter models are negligible since only a small fraction of the weights can fit in GPU memory, and (ii) even when a decent portion of the model weights can fit (e.g., > 50% for ~10B models), the remaining GPU memory can only fit small batch sizes which hurts inference throughput. We later show evaluation results to demonstrate that this approach is sub-optimal.

ZeRO-Inference enables significant model scaling for inference on a single GPU compared to a baseline that hosts the model in GPU memory (i.e., HBM). As an example, we consider half-precision model inference using a single NVIDIA Tesla V100 GPU in a NVIDIA DGX2 system. While the V100 GPU has 32GB of memory, the system is equipped with 1.5TB of CPU DRAM and 30TB of NVMe storage. The maximum model size supported for inference computation on GPU depends on the memory in which the model is hosted. Figure 1 below shows the achievable model scales in this system for GPU inference with ZeRO-Inference. In comparison, the baseline cannot support models larger than 16 billion parameters for GPU inference1. In contrast, ZeRO-Inference has the flexibility to host the model in a different memory (DRAM or NVMe) than HBM. This flexibility allows ZeRO-Inference to support much larger models than baseline. For example, by hosting a model on NVMe memory, Zero-Inference can support models with up to 15 trillion parameters for GPU inference, which is almost a thousand times larger compared to baseline. A practical takeaway from Figure 1 is that ZeRO-Inference enables single GPU inference computation of current SOTA models, since they are smaller than 15 trillion parameters.

An important inference workload is token generation based on an input prompt. In this workload the model is provided a text sequence as input prompt, and based on this prompt, the model generates output text of configurable length. We use this workload to demonstrate the performance of ZeRO-Inference. This workload consists of two phases: (1) the prompt processing phase where the model processes the input prompt, and (2) the generation phase where the model generates the output tokens.

ZeRO-Inference is targeted for throughput-oriented inference applications, and so the performance metric that we use for this workload is the number of tokens generated per second in the generation phase. We use the Hugging Face token generation pipeline in our experiments to measure the performance of using a greedy search algorithm to generate ten output tokens given an input prompt of four tokens. The generation pipeline in our experiments uses KV-caching optimization to improve performance by caching generated tokens to avoid re-computation. We consider the performance impact of three aspects of ZeRO-Inference design choices and optimizations: (1) full offloading model weights as opposed to partial offloading, (2) prefetching layer weights ahead of use, and (3) using multiple GPUs to parallelize layer fetching over PCIe. Additionally, we measure the performance impact of varying the number of output tokens.

For our experiments, we use the three publicly available massive language models listed in Table 1. We configure these models for half-precision inference computations. ZeRO-Inference is required to inference these models on a single V100-32GB since they are bigger than GPU memory.

A key design choice in ZeRO-Offload is to offload all the weights of models larger than GPU memory rather than host a subset of the weights in GPU memory. Our intuition for this approach is that for throughput-oriented inference applications, the larger batch sizes enabled by full offload yields better performance than partial offload. In Table 2, we present results for OPT-30B token generation on a single V100-32GB that compare fully offloading the model weights versus hosting a portion (i.e., 10 and 12 billion parameters2) in GPU memory. The results show that full offload delivers the best performance for both CPU memory (43 tokens per second) and NVMe memory (30 tokens per second). With both CPU and NVMe memory, full offload is over 1.3x and 2.4x faster than partial offload of 18 and 20 billion parameters respectively. The performance advantage of full offload comes from the larger batch sizes compared to the partial offload options. Thus when a model does not fit in GPU, using GPU memory to increase batch size rather than to partially fit the model leads to faster token generation.

ZeRO-Inference fetches layers ahead of use, overlapping with current layer computation, to hide layer transfer latency. We measure the impact of prefetching on token generation performance on a single V100-32GB and summarize the results in Table 3. We observe that prefetching did not improve CPU offload. This is because the relatively short sequences in token generation (i.e., less than 50 tokens) resulted in layer computation time that is insufficient to hide a significant portion of layer fetch time from CPU. In contrast, prefetching improves NVMe offloading performance by 1.13x, 1.14x and 1.21x for OPT-30B, OPT-175B, and BLOOM-176B respectively. This is because transferring weights from NVMe through CPU memory allows prefetching to overlap transfers from CPU to GPU memory with transfers from NVMe to CPU boosting the effective transfer bandwidth.

ZeRO-Inference leverages the four PCIe interconnects between GPUs and CPU memory to parallelize layer fetching for faster inference computations on multiple GPUs. In Table 4, we report the throughput improvements for token generation on two and four GPUs compared to a single GPU3 . These results were collected with layer prefetching enabled. The reported throughput numbers are per GPU showing that token generation becomes faster on each GPU as the aggregated PCIe links reduce the layer fetch latencies. The improved per GPU throughput translates to super-linear scaling performance. Additionally, these results suggest improved bandwidths of future PCIe generations could help to improve ZeRO-Inference performance.

We measure the performance impact of the number of output tokens since the memory overhead of KV-caching optimization increases with longer output tokens and could limit batch size. First, we consider the impact of token lengths 10, 20, 50, and 100 on batch size that can fit one V100-32GB GPU. The results in Table 5 show a 2X reduction in batch size for a 5X increase in token count (compared to baseline count of 10).

Next, we measure the impact on generation throughput using four V100-32GB GPUs. The results are presented in Table 6 for CPU offload, and Table 7 for NVMe-Offload. We observe an impact that is consistent across models and offload memory, which is that increasing the number of output tokens reduces throughput proportionally to batch size reduction. These results also demonstrate the importance of large batch sizes to the performance of ZeRO-Inference.

We briefly discuss how users can determine when ZeRO-Inference is suitable for their application and how to enable ZeRO-Inference in DeepSpeed.

ZeRO-Inference is designed for inference applications that require GPU acceleration but lack sufficient GPU memory to host the model. Also, ZeRO-Inference is optimized for inference applications that are throughput-oriented and allow large batch sizes. Alternative techniques, such as Accelerate, DeepSpeed-Inference, and DeepSpeed-MII that fit the entire model into GPU memory, possibly using multiple GPUs, are more suitable for inference applications that are latency sensitive or have small batch sizes.

ZeRO-Inference is available in the DeepSpeed library versions >= 0.6.6. Integrating ZeRO-Inference into token generation pipelines, such as Hugging Face generate, requires updating the DeepSpeed configuration to set ZeRO optimization to stage 3 and parameter offloading to CPU or NVMe.

Below is a configuration snippet for enabling ZeRO-Inference with offloading to CPU memory.

Below is a configuration snippet for offloading to a NVMe device mounted on “/local_nvme”.

Recent advances in AI technology have primarily come from extreme scaling of model sizes. However, extreme model scaling has also made the hardware cost of training and inferencing prohibitive for all but the largest organizations, severely restricting access to AI innovations. To help democratize AI, we developed ZeRO-Inference, a technology that enables inference computations of massive models on as few as a single GPU. ZeRO-Inference reduces the GPU cost of SOTA model inference by hosting the model on CPU or NVMe memory and streaming the model layers into GPU memory for inference computation. ZeRO-Inference complements the democratization efforts of large organizations that publicly release pre-trained SOTA models by ensuring that inference computation of these models is affordable for most users (e.g., students, hobbyists, model scientists, etc.).

The DeepSpeed team would like to acknowledge Stas Bekman for previewing this blog and providing valuable feedback.

16 billion parameters model won’t fit in V100-32GB for half-precision inference since no memory will be left for inputs and intermediate results. ↩

Pinning more parameters in GPU memory resulted in out of memory errors for small batch sizes. ↩

For multiple GPU runs, we select GPUs with independent PCIe interconnects to CPU memory. ↩

Updated: September 9, 2022

**Examples:**

Example 1 (unknown):
```unknown
"zero_optimization": {
        "stage": 3,
        "offload_param": {
            "device": "cpu",
            ...
        },
        ...
    }
```

Example 2 (unknown):
```unknown
"zero_optimization": {
        "stage": 3,
        "offload_param": {
            "device": "nvme",
            "nvme_path": "/local_nvme",
            ...
        },
        ...
    }
```

---




---

## 🚀 Usage

**Reference this template:** `@skill-deepspeed.md`


**Platform-specific:**
- **GitHub Copilot**: Add to `.github/copilot-instructions.md`
- **Augment Code**: Use `aug context add` command
- **Cursor/Windsurf**: Reference in chat with `@filename`
- **Claude**: Add to Project Knowledge
- **ChatGPT**: Add to Custom GPT configuration
