---
id: skill-nemo-curator
type: skill
name: nemo-curator
description: GPU-accelerated data curation for LLM training. Supports text/image/video/audio.
  Features fuzzy deduplication (16× faster), quality filtering (30+ heuristics), semantic
  deduplication, PII redaction, NSFW detection. Scales across GPUs with RAPIDS. Use
  for preparing high-quality training datasets, cleaning web data, or deduplicating
  large corpora.
category: ai-research
complexity: medium
keywords:
- github
- performance
- python
capabilities: []
token_estimate: 1210
has_references: true
reference_count: 2
---

<!-- Converted from Claude Skill Template -->
<!-- Complexity: Medium -->
<!-- Estimated Tokens: ~1,210 -->
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




# nemo-curator

> GPU-accelerated data curation for LLM training. Supports text/image/video/audio. Features fuzzy deduplication (16× faster), quality filtering (30+ heuristics), semantic deduplication, PII redaction, NSFW detection. Scales across GPUs with RAPIDS. Use for preparing high-quality training datasets, cleaning web data, or deduplicating large corpora.

# NeMo Curator - GPU-Accelerated Data Curation

NVIDIA's toolkit for preparing high-quality training data for LLMs.

## When to use NeMo Curator

**Use NeMo Curator when:**
- Preparing LLM training data from web scrapes (Common Crawl)
- Need fast deduplication (16× faster than CPU)
- Curating multi-modal datasets (text, images, video, audio)
- Filtering low-quality or toxic content
- Scaling data processing across GPU cluster

**Performance**:
- **16× faster** fuzzy deduplication (8TB RedPajama v2)
- **40% lower TCO** vs CPU alternatives
- **Near-linear scaling** across GPU nodes

**Use alternatives instead**:
- **datatrove**: CPU-based, open-source data processing
- **dolma**: Allen AI's data toolkit
- **Ray Data**: General ML data processing (no curation focus)

## Quick start

### Installation

```bash
# Text curation (CUDA 12)
uv pip install "nemo-curator[text_cuda12]"

# All modalities
uv pip install "nemo-curator[all_cuda12]"

# CPU-only (slower)
uv pip install "nemo-curator[cpu]"
```

### Basic text curation pipeline

```python
from nemo_curator import ScoreFilter, Modify
from nemo_curator.datasets import DocumentDataset
import pandas as pd

# Load data
df = pd.DataFrame({"text": ["Good document", "Bad doc", "Excellent text"]})
dataset = DocumentDataset(df)

# Quality filtering
def quality_score(doc):
    return len(doc["text"].split()) > 5  # Filter short docs

filtered = ScoreFilter(quality_score)(dataset)

# Deduplication
from nemo_curator.modules import ExactDuplicates
deduped = ExactDuplicates()(filtered)

# Save
deduped.to_parquet("curated_data/")
```

## Data curation pipeline

### Stage 1: Quality filtering

```python
from nemo_curator.filters import (
    WordCountFilter,
    RepeatedLinesFilter,
    UrlRatioFilter,
    NonAlphaNumericFilter
)

# Apply 30+ heuristic filters
from nemo_curator import ScoreFilter

# Word count filter
dataset = dataset.filter(WordCountFilter(min_words=50, max_words=100000))

# Remove repetitive content
dataset = dataset.filter(RepeatedLinesFilter(max_repeated_line_fraction=0.3))

# URL ratio filter
dataset = dataset.filter(UrlRatioFilter(max_url_ratio=0.2))
```

### Stage 2: Deduplication

**Exact deduplication**:
```python
from nemo_curator.modules import ExactDuplicates

# Remove exact duplicates
deduped = ExactDuplicates(id_field="id", text_field="text")(dataset)
```

**Fuzzy deduplication** (16× faster on GPU):
```python
from nemo_curator.modules import FuzzyDuplicates

# MinHash + LSH deduplication
fuzzy_dedup = FuzzyDuplicates(
    id_field="id",
    text_field="text",
    num_hashes=260,      # MinHash parameters
    num_buckets=20,
    hash_method="md5"
)

deduped = fuzzy_dedup(dataset)
```

**Semantic deduplication**:
```python
from nemo_curator.modules import SemanticDuplicates

# Embedding-based deduplication
semantic_dedup = SemanticDuplicates(
    id_field="id",
    text_field="text",
    embedding_model="sentence-transformers/all-MiniLM-L6-v2",
    threshold=0.8  # Cosine similarity threshold
)

deduped = semantic_dedup(dataset)
```

### Stage 3: PII redaction

```python
from nemo_curator.modules import Modify
from nemo_curator.modifiers import PIIRedactor

# Redact personally identifiable information
pii_redactor = PIIRedactor(
    supported_entities=["EMAIL_ADDRESS", "PHONE_NUMBER", "PERSON", "LOCATION"],
    anonymize_action="replace"  # or "redact"
)

redacted = Modify(pii_redactor)(dataset)
```

### Stage 4: Classifier filtering

```python
from nemo_curator.classifiers import QualityClassifier

# Quality classification
quality_clf = QualityClassifier(
    model_path="nvidia/quality-classifier-deberta",
    batch_size=256,
    device="cuda"
)

# Filter low-quality documents
high_quality = dataset.filter(lambda doc: quality_clf(doc["text"]) > 0.5)
```

## GPU acceleration

### GPU vs CPU performance

| Operation | CPU (16 cores) | GPU (A100) | Speedup |
|-----------|----------------|------------|---------|
| Fuzzy dedup (8TB) | 120 hours | 7.5 hours | 16× |
| Exact dedup (1TB) | 8 hours | 0.5 hours | 16× |
| Quality filtering | 2 hours | 0.2 hours | 10× |

### Multi-GPU scaling

```python
from nemo_curator import get_client
import dask_cuda

# Initialize GPU cluster
client = get_client(cluster_type="gpu", n_workers=8)

# Process with 8 GPUs
deduped = FuzzyDuplicates(...)(dataset)
```

## Multi-modal curation

### Image curation

```python
from nemo_curator.image import (
    AestheticFilter,
    NSFWFilter,
    CLIPEmbedder
)

# Aesthetic scoring
aesthetic_filter = AestheticFilter(threshold=5.0)
filtered_images = aesthetic_filter(image_dataset)

# NSFW detection
nsfw_filter = NSFWFilter(threshold=0.9)
safe_images = nsfw_filter(filtered_images)

# Generate CLIP embeddings
clip_embedder = CLIPEmbedder(model="openai/clip-vit-base-patch32")
image_embeddings = clip_embedder(safe_images)
```

### Video curation

```python
from nemo_curator.video import (
    SceneDetector,
    ClipExtractor,
    InternVideo2Embedder
)

# Detect scenes
scene_detector = SceneDetector(threshold=27.0)
scenes = scene_detector(video_dataset)

# Extract clips
clip_extractor = ClipExtractor(min_duration=2.0, max_duration=10.0)
clips = clip_extractor(scenes)

# Generate embeddings
video_embedder = InternVideo2Embedder()
video_embeddings = video_embedder(clips)
```

### Audio curation

```python
from nemo_curator.audio import (
    ASRInference,
    WERFilter,
    DurationFilter
)

# ASR transcription
asr = ASRInference(model="nvidia/stt_en_fastconformer_hybrid_large_pc")
transcribed = asr(audio_dataset)

# Filter by WER (word error rate)
wer_filter = WERFilter(max_wer=0.3)
high_quality_audio = wer_filter(transcribed)

# Duration filtering
duration_filter = DurationFilter(min_duration=1.0, max_duration=30.0)
filtered_audio = duration_filter(high_quality_audio)
```

## Common patterns

### Web scrape curation (Common Crawl)

```python
from nemo_curator import ScoreFilter, Modify
from nemo_curator.filters import *
from nemo_curator.modules import *
from nemo_curator.datasets import DocumentDataset

# Load Common Crawl data
dataset = DocumentDataset.read_parquet("common_crawl/*.parquet")

# Pipeline
pipeline = [
    # 1. Quality filtering
    WordCountFilter(min_words=100, max_words=50000),
    RepeatedLinesFilter(max_repeated_line_fraction=0.2),
    SymbolToWordRatioFilter(max_symbol_to_word_ratio=0.3),
    UrlRatioFilter(max_url_ratio=0.3),

    # 2. Language filtering
    LanguageIdentificationFilter(target_languages=["en"]),

    # 3. Deduplication
    ExactDuplicates(id_field="id", text_field="text"),
    FuzzyDuplicates(id_field="id", text_field="text", num_hashes=260),

    # 4. PII redaction
    PIIRedactor(),

    # 5. NSFW filtering
    NSFWClassifier(threshold=0.8)
]

# Execute
for stage in pipeline:
    dataset = stage(dataset)

# Save
dataset.to_parquet("curated_common_crawl/")
```

### Distributed processing

```python
from nemo_curator import get_client
from dask_cuda import LocalCUDACluster

# Multi-GPU cluster
cluster = LocalCUDACluster(n_workers=8)
client = get_client(cluster=cluster)

# Process large dataset
dataset = DocumentDataset.read_parquet("s3://large_dataset/*.parquet")
deduped = FuzzyDuplicates(...)(dataset)

# Cleanup
client.close()
cluster.close()
```

## Performance benchmarks

### Fuzzy deduplication (8TB RedPajama v2)

- **CPU (256 cores)**: 120 hours
- **GPU (8× A100)**: 7.5 hours
- **Speedup**: 16×

### Exact deduplication (1TB)

- **CPU (64 cores)**: 8 hours
- **GPU (4× A100)**: 0.5 hours
- **Speedup**: 16×

### Quality filtering (100GB)

- **CPU (32 cores)**: 2 hours
- **GPU (2× A100)**: 0.2 hours
- **Speedup**: 10×

## Cost comparison

**CPU-based curation** (AWS c5.18xlarge × 10):
- Cost: $3.60/hour × 10 = $36/hour
- Time for 8TB: 120 hours
- **Total**: $4,320

**GPU-based curation** (AWS p4d.24xlarge × 2):
- Cost: $32.77/hour × 2 = $65.54/hour
- Time for 8TB: 7.5 hours
- **Total**: $491.55

**Savings**: 89% reduction ($3,828 saved)

## Supported data formats

- **Input**: Parquet, JSONL, CSV
- **Output**: Parquet (recommended), JSONL
- **WebDataset**: TAR archives for multi-modal

## Use cases

**Production deployments**:
- NVIDIA used NeMo Curator to prepare Nemotron-4 training data
- Open-source datasets curated: RedPajama v2, The Pile

## References

- **[Filtering Guide](references/filtering.md)** - 30+ quality filters, heuristics
- **[Deduplication Guide](references/deduplication.md)** - Exact, fuzzy, semantic methods

## Resources

- **GitHub**: https://github.com/NVIDIA/NeMo-Curator ⭐ 500+
- **Docs**: https://docs.nvidia.com/nemo-framework/user-guide/latest/datacuration/
- **Version**: 0.4.0+
- **License**: Apache 2.0





---


## 📚 Reference Materials


### Filtering

# Quality Filtering Guide

Complete guide to NeMo Curator's 30+ quality filters.

## Text-based filters

### Word count

```python
from nemo_curator.filters import WordCountFilter

# Filter by word count
dataset = dataset.filter(WordCountFilter(min_words=50, max_words=100000))
```

### Repeated content

```python
from nemo_curator.filters import RepeatedLinesFilter

# Remove documents with >30% repeated lines
dataset = dataset.filter(RepeatedLinesFilter(max_repeated_line_fraction=0.3))
```

### Symbol ratio

```python
from nemo_curator.filters import SymbolToWordRatioFilter

# Remove documents with too many symbols
dataset = dataset.filter(SymbolToWordRatioFilter(max_symbol_to_word_ratio=0.3))
```

### URL ratio

```python
from nemo_curator.filters import UrlRatioFilter

# Remove documents with many URLs
dataset = dataset.filter(UrlRatioFilter(max_url_ratio=0.2))
```

## Language filtering

```python
from nemo_curator.filters import LanguageIdentificationFilter

# Keep only English documents
dataset = dataset.filter(LanguageIdentificationFilter(target_languages=["en"]))

# Multiple languages
dataset = dataset.filter(LanguageIdentificationFilter(target_languages=["en", "es", "fr"]))
```

## Classifier-based filtering

### Quality classifier

```python
from nemo_curator.classifiers import QualityClassifier

quality_clf = QualityClassifier(
    model_path="nvidia/quality-classifier-deberta",
    batch_size=256,
    device="cuda"
)

# Filter low-quality (threshold > 0.5 = high quality)
dataset = dataset.filter(lambda doc: quality_clf(doc["text"]) > 0.5)
```

### NSFW classifier

```python
from nemo_curator.classifiers import NSFWClassifier

nsfw_clf = NSFWClassifier(threshold=0.9, device="cuda")

# Remove NSFW content
dataset = dataset.filter(lambda doc: nsfw_clf(doc["text"]) < 0.9)
```

## Heuristic filters

Full list of 30+ filters:
- WordCountFilter
- RepeatedLinesFilter
- UrlRatioFilter
- SymbolToWordRatioFilter
- NonAlphaNumericFilter
- BulletsFilter
- WhiteSpaceFilter
- ParenthesesFilter
- LongWordFilter
- And 20+ more...

## Best practices

1. **Apply cheap filters first** - Word count before GPU classifiers
2. **Tune thresholds on sample** - Test on 10k docs before full run
3. **Use GPU classifiers sparingly** - Expensive but effective
4. **Chain filters efficiently** - Order by cost (cheap → expensive)




### Deduplication

# Deduplication Guide

Complete guide to exact, fuzzy, and semantic deduplication.

## Exact deduplication

Remove documents with identical content.

```python
from nemo_curator.modules import ExactDuplicates

# Exact deduplication
exact_dedup = ExactDuplicates(
    id_field="id",
    text_field="text",
    hash_method="md5"  # or "sha256"
)

deduped = exact_dedup(dataset)
```

**Performance**: ~16× faster on GPU vs CPU

## Fuzzy deduplication

Remove near-duplicate documents using MinHash + LSH.

```python
from nemo_curator.modules import FuzzyDuplicates

fuzzy_dedup = FuzzyDuplicates(
    id_field="id",
    text_field="text",
    num_hashes=260,        # MinHash permutations (more = accurate)
    num_buckets=20,        # LSH buckets (more = faster, less recall)
    hash_method="md5",
    jaccard_threshold=0.8  # Similarity threshold
)

deduped = fuzzy_dedup(dataset)
```

**Parameters**:
- `num_hashes`: 128-512 (default 260)
- `num_buckets`: 10-50 (default 20)
- `jaccard_threshold`: 0.7-0.9 (default 0.8)

**Performance**: 16× faster on 8TB dataset (120h → 7.5h)

## Semantic deduplication

Remove semantically similar documents using embeddings.

```python
from nemo_curator.modules import SemanticDuplicates

semantic_dedup = SemanticDuplicates(
    id_field="id",
    text_field="text",
    embedding_model="sentence-transformers/all-MiniLM-L6-v2",
    embedding_batch_size=256,
    threshold=0.85,  # Cosine similarity threshold
    device="cuda"
)

deduped = semantic_dedup(dataset)
```

**Models**:
- `all-MiniLM-L6-v2`: Fast, 384 dims
- `all-mpnet-base-v2`: Better quality, 768 dims
- Custom models supported

## Comparison

| Method | Speed | Recall | Use Case |
|--------|-------|--------|----------|
| Exact | Fastest | 100% | Exact matches only |
| Fuzzy | Fast | ~95% | Near-duplicates (recommended) |
| Semantic | Slow | ~90% | Paraphrases, rewrites |

## Best practices

1. **Start with exact dedup** - Remove obvious duplicates
2. **Use fuzzy for large datasets** - Best speed/quality trade-off
3. **Semantic for high-value data** - Expensive but thorough
4. **GPU acceleration required** - 10-16× speedup




---

## 🚀 Usage

**Reference this template:** `@skill-nemo-curator.md`


**Platform-specific:**
- **GitHub Copilot**: Add to `.github/copilot-instructions.md`
- **Augment Code**: Use `aug context add` command
- **Cursor/Windsurf**: Reference in chat with `@filename`
- **Claude**: Add to Project Knowledge
- **ChatGPT**: Add to Custom GPT configuration
