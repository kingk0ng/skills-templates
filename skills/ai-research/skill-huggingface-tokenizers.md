---
id: skill-huggingface-tokenizers
type: skill
name: huggingface-tokenizers
description: Fast tokenizers optimized for research and production. Rust-based implementation
  tokenizes 1GB in <20 seconds. Supports BPE, WordPiece, and Unigram algorithms. Train
  custom vocabularies, track alignments, handle padding/truncation. Integrates seamlessly
  with transformers. Use when you need high-performance tokenization or custom tokenizer
  training.
category: ai-research
complexity: medium
keywords:
- github
- performance
- python
- rust
- test
capabilities: []
token_estimate: 1937
has_references: true
reference_count: 4
---

<!-- Converted from Claude Skill Template -->
<!-- Complexity: Medium -->
<!-- Estimated Tokens: ~1,937 -->
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




# huggingface-tokenizers

> Fast tokenizers optimized for research and production. Rust-based implementation tokenizes 1GB in <20 seconds. Supports BPE, WordPiece, and Unigram algorithms. Train custom vocabularies, track alignments, handle padding/truncation. Integrates seamlessly with transformers. Use when you need high-performance tokenization or custom tokenizer training.

# HuggingFace Tokenizers - Fast Tokenization for NLP

Fast, production-ready tokenizers with Rust performance and Python ease-of-use.

## When to use HuggingFace Tokenizers

**Use HuggingFace Tokenizers when:**
- Need extremely fast tokenization (<20s per GB of text)
- Training custom tokenizers from scratch
- Want alignment tracking (token → original text position)
- Building production NLP pipelines
- Need to tokenize large corpora efficiently

**Performance**:
- **Speed**: <20 seconds to tokenize 1GB on CPU
- **Implementation**: Rust core with Python/Node.js bindings
- **Efficiency**: 10-100× faster than pure Python implementations

**Use alternatives instead**:
- **SentencePiece**: Language-independent, used by T5/ALBERT
- **tiktoken**: OpenAI's BPE tokenizer for GPT models
- **transformers AutoTokenizer**: Loading pretrained only (uses this library internally)

## Quick start

### Installation

```bash
# Install tokenizers
pip install tokenizers

# With transformers integration
pip install tokenizers transformers
```

### Load pretrained tokenizer

```python
from tokenizers import Tokenizer

# Load from HuggingFace Hub
tokenizer = Tokenizer.from_pretrained("bert-base-uncased")

# Encode text
output = tokenizer.encode("Hello, how are you?")
print(output.tokens)  # ['hello', ',', 'how', 'are', 'you', '?']
print(output.ids)     # [7592, 1010, 2129, 2024, 2017, 1029]

# Decode back
text = tokenizer.decode(output.ids)
print(text)  # "hello, how are you?"
```

### Train custom BPE tokenizer

```python
from tokenizers import Tokenizer
from tokenizers.models import BPE
from tokenizers.trainers import BpeTrainer
from tokenizers.pre_tokenizers import Whitespace

# Initialize tokenizer with BPE model
tokenizer = Tokenizer(BPE(unk_token="[UNK]"))
tokenizer.pre_tokenizer = Whitespace()

# Configure trainer
trainer = BpeTrainer(
    vocab_size=30000,
    special_tokens=["[UNK]", "[CLS]", "[SEP]", "[PAD]", "[MASK]"],
    min_frequency=2
)

# Train on files
files = ["train.txt", "validation.txt"]
tokenizer.train(files, trainer)

# Save
tokenizer.save("my-tokenizer.json")
```

**Training time**: ~1-2 minutes for 100MB corpus, ~10-20 minutes for 1GB

### Batch encoding with padding

```python
# Enable padding
tokenizer.enable_padding(pad_id=3, pad_token="[PAD]")

# Encode batch
texts = ["Hello world", "This is a longer sentence"]
encodings = tokenizer.encode_batch(texts)

for encoding in encodings:
    print(encoding.ids)
# [101, 7592, 2088, 102, 3, 3, 3]
# [101, 2023, 2003, 1037, 2936, 6251, 102]
```

## Tokenization algorithms

### BPE (Byte-Pair Encoding)

**How it works**:
1. Start with character-level vocabulary
2. Find most frequent character pair
3. Merge into new token, add to vocabulary
4. Repeat until vocabulary size reached

**Used by**: GPT-2, GPT-3, RoBERTa, BART, DeBERTa

```python
from tokenizers import Tokenizer
from tokenizers.models import BPE
from tokenizers.trainers import BpeTrainer
from tokenizers.pre_tokenizers import ByteLevel

tokenizer = Tokenizer(BPE(unk_token="<|endoftext|>"))
tokenizer.pre_tokenizer = ByteLevel()

trainer = BpeTrainer(
    vocab_size=50257,
    special_tokens=["<|endoftext|>"],
    min_frequency=2
)

tokenizer.train(files=["data.txt"], trainer=trainer)
```

**Advantages**:
- Handles OOV words well (breaks into subwords)
- Flexible vocabulary size
- Good for morphologically rich languages

**Trade-offs**:
- Tokenization depends on merge order
- May split common words unexpectedly

### WordPiece

**How it works**:
1. Start with character vocabulary
2. Score merge pairs: `frequency(pair) / (frequency(first) × frequency(second))`
3. Merge highest scoring pair
4. Repeat until vocabulary size reached

**Used by**: BERT, DistilBERT, MobileBERT

```python
from tokenizers import Tokenizer
from tokenizers.models import WordPiece
from tokenizers.trainers import WordPieceTrainer
from tokenizers.pre_tokenizers import Whitespace
from tokenizers.normalizers import BertNormalizer

tokenizer = Tokenizer(WordPiece(unk_token="[UNK]"))
tokenizer.normalizer = BertNormalizer(lowercase=True)
tokenizer.pre_tokenizer = Whitespace()

trainer = WordPieceTrainer(
    vocab_size=30522,
    special_tokens=["[UNK]", "[CLS]", "[SEP]", "[PAD]", "[MASK]"],
    continuing_subword_prefix="##"
)

tokenizer.train(files=["corpus.txt"], trainer=trainer)
```

**Advantages**:
- Prioritizes meaningful merges (high score = semantically related)
- Used successfully in BERT (state-of-the-art results)

**Trade-offs**:
- Unknown words become `[UNK]` if no subword match
- Saves vocabulary, not merge rules (larger files)

### Unigram

**How it works**:
1. Start with large vocabulary (all substrings)
2. Compute loss for corpus with current vocabulary
3. Remove tokens with minimal impact on loss
4. Repeat until vocabulary size reached

**Used by**: ALBERT, T5, mBART, XLNet (via SentencePiece)

```python
from tokenizers import Tokenizer
from tokenizers.models import Unigram
from tokenizers.trainers import UnigramTrainer

tokenizer = Tokenizer(Unigram())

trainer = UnigramTrainer(
    vocab_size=8000,
    special_tokens=["<unk>", "<s>", "</s>"],
    unk_token="<unk>"
)

tokenizer.train(files=["data.txt"], trainer=trainer)
```

**Advantages**:
- Probabilistic (finds most likely tokenization)
- Works well for languages without word boundaries
- Handles diverse linguistic contexts

**Trade-offs**:
- Computationally expensive to train
- More hyperparameters to tune

## Tokenization pipeline

Complete pipeline: **Normalization → Pre-tokenization → Model → Post-processing**

### Normalization

Clean and standardize text:

```python
from tokenizers.normalizers import NFD, StripAccents, Lowercase, Sequence

tokenizer.normalizer = Sequence([
    NFD(),           # Unicode normalization (decompose)
    Lowercase(),     # Convert to lowercase
    StripAccents()   # Remove accents
])

# Input: "Héllo WORLD"
# After normalization: "hello world"
```

**Common normalizers**:
- `NFD`, `NFC`, `NFKD`, `NFKC` - Unicode normalization forms
- `Lowercase()` - Convert to lowercase
- `StripAccents()` - Remove accents (é → e)
- `Strip()` - Remove whitespace
- `Replace(pattern, content)` - Regex replacement

### Pre-tokenization

Split text into word-like units:

```python
from tokenizers.pre_tokenizers import Whitespace, Punctuation, Sequence, ByteLevel

# Split on whitespace and punctuation
tokenizer.pre_tokenizer = Sequence([
    Whitespace(),
    Punctuation()
])

# Input: "Hello, world!"
# After pre-tokenization: ["Hello", ",", "world", "!"]
```

**Common pre-tokenizers**:
- `Whitespace()` - Split on spaces, tabs, newlines
- `ByteLevel()` - GPT-2 style byte-level splitting
- `Punctuation()` - Isolate punctuation
- `Digits(individual_digits=True)` - Split digits individually
- `Metaspace()` - Replace spaces with ▁ (SentencePiece style)

### Post-processing

Add special tokens for model input:

```python
from tokenizers.processors import TemplateProcessing

# BERT-style: [CLS] sentence [SEP]
tokenizer.post_processor = TemplateProcessing(
    single="[CLS] $A [SEP]",
    pair="[CLS] $A [SEP] $B [SEP]",
    special_tokens=[
        ("[CLS]", 1),
        ("[SEP]", 2),
    ],
)
```

**Common patterns**:
```python
# GPT-2: sentence <|endoftext|>
TemplateProcessing(
    single="$A <|endoftext|>",
    special_tokens=[("<|endoftext|>", 50256)]
)

# RoBERTa: <s> sentence </s>
TemplateProcessing(
    single="<s> $A </s>",
    pair="<s> $A </s> </s> $B </s>",
    special_tokens=[("<s>", 0), ("</s>", 2)]
)
```

## Alignment tracking

Track token positions in original text:

```python
output = tokenizer.encode("Hello, world!")

# Get token offsets
for token, offset in zip(output.tokens, output.offsets):
    start, end = offset
    print(f"{token:10} → [{start:2}, {end:2}): {text[start:end]!r}")

# Output:
# hello      → [ 0,  5): 'Hello'
# ,          → [ 5,  6): ','
# world      → [ 7, 12): 'world'
# !          → [12, 13): '!'
```

**Use cases**:
- Named entity recognition (map predictions back to text)
- Question answering (extract answer spans)
- Token classification (align labels to original positions)

## Integration with transformers

### Load with AutoTokenizer

```python
from transformers import AutoTokenizer

# AutoTokenizer automatically uses fast tokenizers
tokenizer = AutoTokenizer.from_pretrained("bert-base-uncased")

# Check if using fast tokenizer
print(tokenizer.is_fast)  # True

# Access underlying tokenizers.Tokenizer
fast_tokenizer = tokenizer.backend_tokenizer
print(type(fast_tokenizer))  # <class 'tokenizers.Tokenizer'>
```

### Convert custom tokenizer to transformers

```python
from tokenizers import Tokenizer
from transformers import PreTrainedTokenizerFast

# Train custom tokenizer
tokenizer = Tokenizer(BPE())
# ... train tokenizer ...
tokenizer.save("my-tokenizer.json")

# Wrap for transformers
transformers_tokenizer = PreTrainedTokenizerFast(
    tokenizer_file="my-tokenizer.json",
    unk_token="[UNK]",
    pad_token="[PAD]",
    cls_token="[CLS]",
    sep_token="[SEP]",
    mask_token="[MASK]"
)

# Use like any transformers tokenizer
outputs = transformers_tokenizer(
    "Hello world",
    padding=True,
    truncation=True,
    max_length=512,
    return_tensors="pt"
)
```

## Common patterns

### Train from iterator (large datasets)

```python
from datasets import load_dataset

# Load dataset
dataset = load_dataset("wikitext", "wikitext-103-raw-v1", split="train")

# Create batch iterator
def batch_iterator(batch_size=1000):
    for i in range(0, len(dataset), batch_size):
        yield dataset[i:i + batch_size]["text"]

# Train tokenizer
tokenizer.train_from_iterator(
    batch_iterator(),
    trainer=trainer,
    length=len(dataset)  # For progress bar
)
```

**Performance**: Processes 1GB in ~10-20 minutes

### Enable truncation and padding

```python
# Enable truncation
tokenizer.enable_truncation(max_length=512)

# Enable padding
tokenizer.enable_padding(
    pad_id=tokenizer.token_to_id("[PAD]"),
    pad_token="[PAD]",
    length=512  # Fixed length, or None for batch max
)

# Encode with both
output = tokenizer.encode("This is a long sentence that will be truncated...")
print(len(output.ids))  # 512
```

### Multi-processing

```python
from tokenizers import Tokenizer
from multiprocessing import Pool

# Load tokenizer
tokenizer = Tokenizer.from_file("tokenizer.json")

def encode_batch(texts):
    return tokenizer.encode_batch(texts)

# Process large corpus in parallel
with Pool(8) as pool:
    # Split corpus into chunks
    chunk_size = 1000
    chunks = [corpus[i:i+chunk_size] for i in range(0, len(corpus), chunk_size)]

    # Encode in parallel
    results = pool.map(encode_batch, chunks)
```

**Speedup**: 5-8× with 8 cores

## Performance benchmarks

### Training speed

| Corpus Size | BPE (30k vocab) | WordPiece (30k) | Unigram (8k) |
|-------------|-----------------|-----------------|--------------|
| 10 MB       | 15 sec          | 18 sec          | 25 sec       |
| 100 MB      | 1.5 min         | 2 min           | 4 min        |
| 1 GB        | 15 min          | 20 min          | 40 min       |

**Hardware**: 16-core CPU, tested on English Wikipedia

### Tokenization speed

| Implementation | 1 GB corpus | Throughput    |
|----------------|-------------|---------------|
| Pure Python    | ~20 minutes | ~50 MB/min    |
| HF Tokenizers  | ~15 seconds | ~4 GB/min     |
| **Speedup**    | **80×**     | **80×**       |

**Test**: English text, average sentence length 20 words

### Memory usage

| Task                    | Memory  |
|-------------------------|---------|
| Load tokenizer          | ~10 MB  |
| Train BPE (30k vocab)   | ~200 MB |
| Encode 1M sentences     | ~500 MB |

## Supported models

Pre-trained tokenizers available via `from_pretrained()`:

**BERT family**:
- `bert-base-uncased`, `bert-large-cased`
- `distilbert-base-uncased`
- `roberta-base`, `roberta-large`

**GPT family**:
- `gpt2`, `gpt2-medium`, `gpt2-large`
- `distilgpt2`

**T5 family**:
- `t5-small`, `t5-base`, `t5-large`
- `google/flan-t5-xxl`

**Other**:
- `facebook/bart-base`, `facebook/mbart-large-cc25`
- `albert-base-v2`, `albert-xlarge-v2`
- `xlm-roberta-base`, `xlm-roberta-large`

Browse all: https://huggingface.co/models?library=tokenizers

## References

- **[Training Guide](references/training.md)** - Train custom tokenizers, configure trainers, handle large datasets
- **[Algorithms Deep Dive](references/algorithms.md)** - BPE, WordPiece, Unigram explained in detail
- **[Pipeline Components](references/pipeline.md)** - Normalizers, pre-tokenizers, post-processors, decoders
- **[Transformers Integration](references/integration.md)** - AutoTokenizer, PreTrainedTokenizerFast, special tokens

## Resources

- **Docs**: https://huggingface.co/docs/tokenizers
- **GitHub**: https://github.com/huggingface/tokenizers ⭐ 9,000+
- **Version**: 0.20.0+
- **Course**: https://huggingface.co/learn/nlp-course/chapter6/1
- **Paper**: BPE (Sennrich et al., 2016), WordPiece (Schuster & Nakajima, 2012)




---


## 📚 Reference Materials


### Algorithms

# Tokenization Algorithms Deep Dive

Comprehensive explanation of BPE, WordPiece, and Unigram algorithms.

## Byte-Pair Encoding (BPE)

### Algorithm overview

BPE iteratively merges the most frequent pair of tokens in a corpus.

**Training process**:
1. Initialize vocabulary with all characters
2. Count frequency of all adjacent token pairs
3. Merge most frequent pair into new token
4. Add new token to vocabulary
5. Update corpus with new token
6. Repeat until vocabulary size reached

### Step-by-step example

**Corpus**:
```
low: 5
lower: 2
newest: 6
widest: 3
```

**Iteration 1**:
```
Count pairs:
'e' + 's': 9 (newest: 6, widest: 3)  ← most frequent
'l' + 'o': 7
'o' + 'w': 7
...

Merge: 'e' + 's' → 'es'

Updated corpus:
low: 5
lower: 2
newest: 6 → newes|t: 6
widest: 3 → wides|t: 3

Vocabulary: [a-z] + ['es']
```

**Iteration 2**:
```
Count pairs:
'es' + 't': 9  ← most frequent
'l' + 'o': 7
...

Merge: 'es' + 't' → 'est'

Updated corpus:
low: 5
lower: 2
newest: 6 → new|est: 6
widest: 3 → wid|est: 3

Vocabulary: [a-z] + ['es', 'est']
```

**Continue until desired vocabulary size...**

### Tokenization with trained BPE

Given vocabulary: `['l', 'o', 'w', 'e', 'r', 'n', 's', 't', 'i', 'd', 'es', 'est', 'lo', 'low', 'ne', 'new', 'newest', 'wi', 'wid', 'widest']`

Tokenize "lowest":
```
Step 1: Split into characters
['l', 'o', 'w', 'e', 's', 't']

Step 2: Apply merges in order learned during training
- Merge 'l' + 'o' → 'lo' (if this merge was learned)
- Merge 'lo' + 'w' → 'low' (if learned)
- Merge 'e' + 's' → 'es' (learned)
- Merge 'es' + 't' → 'est' (learned)

Final: ['low', 'est']
```

### Implementation

```python
from tokenizers import Tokenizer
from tokenizers.models import BPE
from tokenizers.trainers import BpeTrainer
from tokenizers.pre_tokenizers import Whitespace

# Initialize
tokenizer = Tokenizer(BPE(unk_token="[UNK]"))
tokenizer.pre_tokenizer = Whitespace()

# Configure trainer
trainer = BpeTrainer(
    vocab_size=1000,
    min_frequency=2,
    special_tokens=["[UNK]", "[CLS]", "[SEP]", "[PAD]", "[MASK]"]
)

# Train
corpus = [
    "This is a sample corpus for BPE training.",
    "BPE learns subword units from the training data.",
    # ... more sentences
]

tokenizer.train_from_iterator(corpus, trainer=trainer)

# Use
output = tokenizer.encode("This is tokenization")
print(output.tokens)  # ['This', 'is', 'token', 'ization']
```

### Byte-level BPE (GPT-2 variant)

**Problem**: Standard BPE has limited character coverage (256+ Unicode chars)

**Solution**: Operate on byte level (256 bytes)

```python
from tokenizers.pre_tokenizers import ByteLevel
from tokenizers.decoders import ByteLevel as ByteLevelDecoder

tokenizer = Tokenizer(BPE())

# Byte-level pre-tokenization
tokenizer.pre_tokenizer = ByteLevel()
tokenizer.decoder = ByteLevelDecoder()

# This handles ALL possible characters, including emojis
text = "Hello 🌍 世界"
tokens = tokenizer.encode(text).tokens
```

**Advantages**:
- Handles any Unicode character (256 byte coverage)
- No unknown tokens (worst case: bytes)
- Used by GPT-2, GPT-3, BART

**Trade-offs**:
- Slightly worse compression (bytes vs characters)
- More tokens for non-ASCII text

### BPE variants

**SentencePiece BPE**:
- Language-independent (no pre-tokenization)
- Treats input as raw byte stream
- Used by T5, ALBERT, XLNet

**Robust BPE**:
- Dropout during training (randomly skip merges)
- More robust tokenization at inference
- Reduces overfitting to training data

## WordPiece

### Algorithm overview

WordPiece is similar to BPE but uses a different merge selection criterion.

**Training process**:
1. Initialize vocabulary with all characters
2. Count frequency of all token pairs
3. Score each pair: `score = freq(pair) / (freq(first) × freq(second))`
4. Merge pair with highest score
5. Repeat until vocabulary size reached

### Why different scoring?

**BPE**: Merges most frequent pairs
- "aa" appears 100 times → high priority
- Even if 'a' appears 1000 times alone

**WordPiece**: Merges pairs that are semantically related
- "aa" appears 100 times, 'a' appears 1000 times → low score (100 / (1000 × 1000))
- "th" appears 50 times, 't' appears 60 times, 'h' appears 55 times → high score (50 / (60 × 55))
- Prioritizes pairs that appear together more than expected

### Step-by-step example

**Corpus**:
```
low: 5
lower: 2
newest: 6
widest: 3
```

**Iteration 1**:
```
Count frequencies:
'e': 11 (lower: 2, newest: 6, widest: 3)
's': 9
't': 9
...

Count pairs:
'e' + 's': 9 (newest: 6, widest: 3)
'es' + 't': 9 (newest: 6, widest: 3)
...

Compute scores:
score('e' + 's') = 9 / (11 × 9) = 0.091
score('es' + 't') = 9 / (9 × 9) = 0.111  ← highest score
score('l' + 'o') = 7 / (7 × 9) = 0.111   ← tied

Choose: 'es' + 't' → 'est' (or 'lo' if tied)
```

**Key difference**: WordPiece prioritizes rare combinations over frequent ones.

### Tokenization with WordPiece

Given vocabulary: `['##e', '##s', '##t', 'l', 'o', 'w', 'new', 'est', 'low']`

Tokenize "lowest":
```
Step 1: Find longest matching prefix
'lowest' → 'low' (matches)

Step 2: Find longest match for remainder
'est' → 'est' (matches)

Final: ['low', 'est']
```

**If no match**:
```
Tokenize "unknownword":
'unknownword' → no match
'unknown' → no match
'unkn' → no match
'un' → no match
'u' → no match
→ [UNK]
```

### Implementation

```python
from tokenizers import Tokenizer
from tokenizers.models import WordPiece
from tokenizers.trainers import WordPieceTrainer
from tokenizers.normalizers import BertNormalizer
from tokenizers.pre_tokenizers import BertPreTokenizer

# Initialize BERT-style tokenizer
tokenizer = Tokenizer(WordPiece(unk_token="[UNK]"))

# Normalization (lowercase, accent stripping)
tokenizer.normalizer = BertNormalizer(lowercase=True)

# Pre-tokenization (whitespace + punctuation)
tokenizer.pre_tokenizer = BertPreTokenizer()

# Configure trainer
trainer = WordPieceTrainer(
    vocab_size=30522,  # BERT vocab size
    min_frequency=2,
    special_tokens=["[UNK]", "[CLS]", "[SEP]", "[PAD]", "[MASK]"],
    continuing_subword_prefix="##"  # BERT uses ##
)

# Train
tokenizer.train_from_iterator(corpus, trainer=trainer)

# Use
output = tokenizer.encode("Tokenization works great!")
print(output.tokens)  # ['token', '##ization', 'works', 'great', '!']
```

### Subword prefix

**BERT uses `##` prefix**:
```
"unbelievable" → ['un', '##believ', '##able']
```

**Why?**
- Indicates token is a continuation
- Allows reconstruction: remove ##, concatenate
- Helps model distinguish word boundaries

### WordPiece advantages

**Semantic merges**:
- Prioritizes meaningful combinations
- "qu" has high score (always together)
- "qx" has low score (rare combination)

**Better for morphology**:
- Captures affixes: un-, -ing, -ed
- Preserves word stems

**Trade-offs**:
- Slower training than BPE
- More memory (stores vocabulary, not merges)
- Original implementation not open-source (HF reimplementation)

## Unigram

### Algorithm overview

Unigram works backward: start with large vocabulary, remove tokens.

**Training process**:
1. Initialize with large vocabulary (all substrings)
2. Estimate probability of each token (frequency-based)
3. For each token, compute loss increase if removed
4. Remove 10-20% of tokens with lowest loss impact
5. Re-estimate probabilities
6. Repeat until desired vocabulary size

### Probabilistic tokenization

**Unigram assumption**: Each token is independent.

Given vocabulary with probabilities:
```
P('low') = 0.02
P('l') = 0.01
P('o') = 0.015
P('w') = 0.01
P('est') = 0.03
P('e') = 0.02
P('s') = 0.015
P('t') = 0.015
```

Tokenize "lowest":
```
Option 1: ['low', 'est']
P = P('low') × P('est') = 0.02 × 0.03 = 0.0006

Option 2: ['l', 'o', 'w', 'est']
P = 0.01 × 0.015 × 0.01 × 0.03 = 0.000000045

Option 3: ['low', 'e', 's', 't']
P = 0.02 × 0.02 × 0.015 × 0.015 = 0.0000009

Choose option 1 (highest probability)
```

### Viterbi algorithm

Finding best tokenization is expensive (exponential possibilities).

**Viterbi algorithm** (dynamic programming):
```python
def tokenize_viterbi(word, vocab, probs):
    n = len(word)
    # dp[i] = (best_prob, best_tokens) for word[:i]
    dp = [{} for _ in range(n + 1)]
    dp[0] = (0.0, [])  # log probability

    for i in range(1, n + 1):
        best_prob = float('-inf')
        best_tokens = []

        # Try all possible last tokens
        for j in range(i):
            token = word[j:i]
            if token in vocab:
                prob = dp[j][0] + log(probs[token])
                if prob > best_prob:
                    best_prob = prob
                    best_tokens = dp[j][1] + [token]

        dp[i] = (best_prob, best_tokens)

    return dp[n][1]
```

**Time complexity**: O(n² × vocab_size) vs O(2^n) brute force

### Implementation

```python
from tokenizers import Tokenizer
from tokenizers.models import Unigram
from tokenizers.trainers import UnigramTrainer

# Initialize
tokenizer = Tokenizer(Unigram())

# Configure trainer
trainer = UnigramTrainer(
    vocab_size=8000,
    special_tokens=["<unk>", "<s>", "</s>"],
    unk_token="<unk>",
    max_piece_length=16,      # Max token length
    n_sub_iterations=2,       # EM iterations
    shrinking_factor=0.75     # Remove 25% each iteration
)

# Train
tokenizer.train_from_iterator(corpus, trainer=trainer)

# Use
output = tokenizer.encode("Tokenization with Unigram")
print(output.tokens)  # ['▁Token', 'ization', '▁with', '▁Un', 'igram']
```

### Unigram advantages

**Probabilistic**:
- Multiple valid tokenizations
- Can sample different tokenizations (data augmentation)

**Subword regularization**:
```python
# Sample different tokenizations
for _ in range(3):
    tokens = tokenizer.encode("tokenization", is_pretokenized=False).tokens
    print(tokens)

# Output (different each time):
# ['token', 'ization']
# ['tok', 'en', 'ization']
# ['token', 'iz', 'ation']
```

**Language-independent**:
- No word boundaries needed
- Works for CJK languages (Chinese, Japanese, Korean)
- Treats input as character stream

**Trade-offs**:
- Slower training (EM algorithm)
- More hyperparameters
- Larger model (stores probabilities)

## Algorithm comparison

### Training speed

| Algorithm  | Small (10MB) | Medium (100MB) | Large (1GB) |
|------------|--------------|----------------|-------------|
| BPE        | 10-15 sec    | 1-2 min        | 10-20 min   |
| WordPiece  | 15-20 sec    | 2-3 min        | 15-30 min   |
| Unigram    | 20-30 sec    | 3-5 min        | 30-60 min   |

**Tested on**: 16-core CPU, 30k vocab

### Tokenization quality

Tested on English Wikipedia (perplexity measurement):

| Algorithm  | Vocab Size | Tokens/Word | Unknown Rate |
|------------|------------|-------------|--------------|
| BPE        | 30k        | 1.3         | 0.5%         |
| WordPiece  | 30k        | 1.2         | 1.2%         |
| Unigram    | 8k         | 1.5         | 0.3%         |

**Key observations**:
- WordPiece: Slightly better compression
- BPE: Lower unknown rate
- Unigram: Smallest vocab, good coverage

### Compression ratio

Characters per token (higher = better compression):

| Language | BPE (30k) | WordPiece (30k) | Unigram (8k) |
|----------|-----------|-----------------|--------------|
| English  | 4.2       | 4.5             | 3.8          |
| Chinese  | 2.1       | 2.3             | 2.5          |
| Arabic   | 3.5       | 3.8             | 3.2          |

**Best for each**:
- English: WordPiece
- Chinese: Unigram (language-independent)
- Arabic: WordPiece

### Use case recommendations

**BPE** - Best for:
- English language models
- Code (handles symbols well)
- Fast training needed
- **Models**: GPT-2, GPT-3, RoBERTa, BART

**WordPiece** - Best for:
- Masked language modeling (BERT-style)
- Morphologically rich languages
- Semantic understanding tasks
- **Models**: BERT, DistilBERT, ELECTRA

**Unigram** - Best for:
- Multilingual models
- Languages without word boundaries (CJK)
- Data augmentation via subword regularization
- **Models**: T5, ALBERT, XLNet (via SentencePiece)

## Advanced topics

### Handling rare words

**BPE approach**:
```
"antidisestablishmentarianism"
→ ['anti', 'dis', 'establish', 'ment', 'arian', 'ism']
```

**WordPiece approach**:
```
"antidisestablishmentarianism"
→ ['anti', '##dis', '##establish', '##ment', '##arian', '##ism']
```

**Unigram approach**:
```
"antidisestablishmentarianism"
→ ['▁anti', 'dis', 'establish', 'ment', 'arian', 'ism']
```

### Handling numbers

**Challenge**: Infinite number combinations

**BPE solution**: Byte-level (handles any digit sequence)
```python
tokenizer = Tokenizer(BPE())
tokenizer.pre_tokenizer = ByteLevel()

# Handles any number
"123456789" → byte-level tokens
```

**WordPiece solution**: Digit pre-tokenization
```python
from tokenizers.pre_tokenizers import Digits

# Split digits individually or as groups
tokenizer.pre_tokenizer = Digits(individual_digits=True)

"123" → ['1', '2', '3']
```

**Unigram solution**: Learns common number patterns
```python
# Learns patterns during training
"2023" → ['202', '3'] or ['20', '23']
```

### Handling case sensitivity

**Lowercase (BERT)**:
```python
from tokenizers.normalizers import Lowercase

tokenizer.normalizer = Lowercase()

"Hello WORLD" → "hello world" → ['hello', 'world']
```

**Preserve case (GPT-2)**:
```python
# No case normalization
tokenizer.normalizer = None

"Hello WORLD" → ['Hello', 'WORLD']
```

**Cased tokens (RoBERTa)**:
```python
# Learns separate tokens for different cases
Vocabulary: ['Hello', 'hello', 'HELLO', 'world', 'WORLD']
```

### Handling emojis and special characters

**Byte-level (GPT-2)**:
```python
tokenizer.pre_tokenizer = ByteLevel()

"Hello 🌍 👋" → byte-level representation (always works)
```

**Unicode normalization**:
```python
from tokenizers.normalizers import NFKC

tokenizer.normalizer = NFKC()

"é" (composed) ↔ "é" (decomposed) → normalized to one form
```

## Troubleshooting

### Issue: Poor subword splitting

**Symptom**:
```
"running" → ['r', 'u', 'n', 'n', 'i', 'n', 'g']  (too granular)
```

**Solutions**:
1. Increase vocabulary size
2. Train longer (more merge iterations)
3. Lower `min_frequency` threshold

### Issue: Too many unknown tokens

**Symptom**:
```
5% of tokens are [UNK]
```

**Solutions**:
1. Increase vocabulary size
2. Use byte-level BPE (no UNK possible)
3. Verify training corpus is representative

### Issue: Inconsistent tokenization

**Symptom**:
```
"running" → ['run', 'ning']
"runner" → ['r', 'u', 'n', 'n', 'e', 'r']
```

**Solutions**:
1. Check normalization consistency
2. Ensure pre-tokenization is deterministic
3. Use Unigram for probabilistic variance

## Best practices

1. **Match algorithm to model architecture**:
   - BERT-style → WordPiece
   - GPT-style → BPE
   - T5-style → Unigram

2. **Use byte-level for multilingual**:
   - Handles any Unicode
   - No unknown tokens

3. **Test on representative data**:
   - Measure compression ratio
   - Check unknown token rate
   - Inspect sample tokenizations

4. **Version control tokenizers**:
   - Save with model
   - Document special tokens
   - Track vocabulary changes




### Integration

# Transformers Integration

Complete guide to using HuggingFace Tokenizers with the Transformers library.

## AutoTokenizer

The easiest way to load tokenizers.

### Loading pretrained tokenizers

```python
from transformers import AutoTokenizer

# Load from HuggingFace Hub
tokenizer = AutoTokenizer.from_pretrained("bert-base-uncased")

# Check if using fast tokenizer (Rust-based)
print(tokenizer.is_fast)  # True

# Access underlying tokenizers.Tokenizer
if tokenizer.is_fast:
    fast_tokenizer = tokenizer.backend_tokenizer
    print(type(fast_tokenizer))  # <class 'tokenizers.Tokenizer'>
```

### Fast vs slow tokenizers

| Feature                  | Fast (Rust)    | Slow (Python) |
|--------------------------|----------------|---------------|
| Speed                    | 5-10× faster   | Baseline      |
| Alignment tracking       | ✅ Full support | ❌ Limited     |
| Batch processing         | ✅ Optimized    | ⚠️ Slower      |
| Offset mapping           | ✅ Yes          | ❌ No          |
| Installation             | `tokenizers`   | Built-in      |

**Always use fast tokenizers when available.**

### Check available tokenizers

```python
from transformers import TOKENIZER_MAPPING

# List all fast tokenizers
for config_class, (slow, fast) in TOKENIZER_MAPPING.items():
    if fast is not None:
        print(f"{config_class.__name__}: {fast.__name__}")
```

## PreTrainedTokenizerFast

Wrap custom tokenizers for transformers.

### Convert custom tokenizer

```python
from tokenizers import Tokenizer
from tokenizers.models import BPE
from tokenizers.trainers import BpeTrainer
from transformers import PreTrainedTokenizerFast

# Train custom tokenizer
tokenizer = Tokenizer(BPE())
trainer = BpeTrainer(
    vocab_size=30000,
    special_tokens=["[UNK]", "[CLS]", "[SEP]", "[PAD]", "[MASK]"]
)
tokenizer.train(files=["corpus.txt"], trainer=trainer)

# Save tokenizer
tokenizer.save("my-tokenizer.json")

# Wrap for transformers
transformers_tokenizer = PreTrainedTokenizerFast(
    tokenizer_file="my-tokenizer.json",
    unk_token="[UNK]",
    sep_token="[SEP]",
    pad_token="[PAD]",
    cls_token="[CLS]",
    mask_token="[MASK]"
)

# Save in transformers format
transformers_tokenizer.save_pretrained("my-tokenizer")
```

**Result**: Directory with `tokenizer.json` + `tokenizer_config.json` + `special_tokens_map.json`

### Use like any transformers tokenizer

```python
# Load
from transformers import AutoTokenizer
tokenizer = AutoTokenizer.from_pretrained("my-tokenizer")

# Encode with all transformers features
outputs = tokenizer(
    "Hello world",
    padding="max_length",
    truncation=True,
    max_length=128,
    return_tensors="pt"
)

print(outputs.keys())
# dict_keys(['input_ids', 'token_type_ids', 'attention_mask'])
```

## Special tokens

### Default special tokens

| Model Family | CLS/BOS | SEP/EOS       | PAD     | UNK     | MASK    |
|--------------|---------|---------------|---------|---------|---------|
| BERT         | [CLS]   | [SEP]         | [PAD]   | [UNK]   | [MASK]  |
| GPT-2        | -       | <\|endoftext\|> | <\|endoftext\|> | <\|endoftext\|> | -       |
| RoBERTa      | <s>     | </s>          | <pad>   | <unk>   | <mask>  |
| T5           | -       | </s>          | <pad>   | <unk>   | -       |

### Adding special tokens

```python
# Add new special tokens
special_tokens_dict = {
    "additional_special_tokens": ["<|image|>", "<|video|>", "<|audio|>"]
}

num_added_tokens = tokenizer.add_special_tokens(special_tokens_dict)
print(f"Added {num_added_tokens} tokens")

# Resize model embeddings
model.resize_token_embeddings(len(tokenizer))

# Use new tokens
text = "This is an image: <|image|>"
tokens = tokenizer.encode(text)
```

### Adding regular tokens

```python
# Add domain-specific tokens
new_tokens = ["COVID-19", "mRNA", "vaccine"]
num_added = tokenizer.add_tokens(new_tokens)

# These are NOT special tokens (can be split if needed)
tokenizer.add_tokens(new_tokens, special_tokens=False)

# These ARE special tokens (never split)
tokenizer.add_tokens(new_tokens, special_tokens=True)
```

## Encoding and decoding

### Basic encoding

```python
# Single sentence
text = "Hello, how are you?"
encoded = tokenizer(text)

print(encoded)
# {'input_ids': [101, 7592, 1010, 2129, 2024, 2017, 1029, 102],
#  'token_type_ids': [0, 0, 0, 0, 0, 0, 0, 0],
#  'attention_mask': [1, 1, 1, 1, 1, 1, 1, 1]}
```

### Batch encoding

```python
# Multiple sentences
texts = ["Hello world", "How are you?", "I am fine"]
encoded = tokenizer(texts, padding=True, truncation=True, max_length=10)

print(encoded['input_ids'])
# [[101, 7592, 2088, 102, 0, 0, 0, 0, 0, 0],
#  [101, 2129, 2024, 2017, 1029, 102, 0, 0, 0, 0],
#  [101, 1045, 2572, 2986, 102, 0, 0, 0, 0, 0]]
```

### Return tensors

```python
# Return PyTorch tensors
outputs = tokenizer("Hello world", return_tensors="pt")
print(outputs['input_ids'].shape)  # torch.Size([1, 5])

# Return TensorFlow tensors
outputs = tokenizer("Hello world", return_tensors="tf")

# Return NumPy arrays
outputs = tokenizer("Hello world", return_tensors="np")

# Return lists (default)
outputs = tokenizer("Hello world", return_tensors=None)
```

### Decoding

```python
# Decode token IDs
ids = [101, 7592, 2088, 102]
text = tokenizer.decode(ids)
print(text)  # "[CLS] hello world [SEP]"

# Skip special tokens
text = tokenizer.decode(ids, skip_special_tokens=True)
print(text)  # "hello world"

# Batch decode
batch_ids = [[101, 7592, 102], [101, 2088, 102]]
texts = tokenizer.batch_decode(batch_ids, skip_special_tokens=True)
print(texts)  # ["hello", "world"]
```

## Padding and truncation

### Padding strategies

```python
# Pad to max length in batch
tokenizer(texts, padding="longest")

# Pad to model max length
tokenizer(texts, padding="max_length", max_length=128)

# No padding
tokenizer(texts, padding=False)

# Pad to multiple of value (for efficient computation)
tokenizer(texts, padding="max_length", max_length=128, pad_to_multiple_of=8)
# Result: length will be 128 (already multiple of 8)
```

### Truncation strategies

```python
# Truncate to max length
tokenizer(text, truncation=True, max_length=10)

# Only truncate first sequence (for pairs)
tokenizer(text1, text2, truncation="only_first", max_length=20)

# Only truncate second sequence
tokenizer(text1, text2, truncation="only_second", max_length=20)

# Truncate longest first (default for pairs)
tokenizer(text1, text2, truncation="longest_first", max_length=20)

# No truncation (error if too long)
tokenizer(text, truncation=False)
```

### Stride for long documents

```python
# For documents longer than max_length
text = "Very long document " * 1000

# Encode with overlap
encodings = tokenizer(
    text,
    max_length=512,
    stride=128,          # Overlap between chunks
    truncation=True,
    return_overflowing_tokens=True,
    return_offsets_mapping=True
)

# Get all chunks
num_chunks = len(encodings['input_ids'])
print(f"Split into {num_chunks} chunks")

# Each chunk overlaps by stride tokens
for i, chunk in enumerate(encodings['input_ids']):
    print(f"Chunk {i}: {len(chunk)} tokens")
```

**Use case**: Long document QA, sliding window inference

## Alignment and offsets

### Offset mapping

```python
# Get character offsets for each token
encoded = tokenizer("Hello, world!", return_offsets_mapping=True)

for token, (start, end) in zip(
    encoded.tokens(),
    encoded['offset_mapping'][0]
):
    print(f"{token:10s} → [{start:2d}, {end:2d})")

# Output:
# [CLS]      → [ 0,  0)
# Hello      → [ 0,  5)
# ,          → [ 5,  6)
# world      → [ 7, 12)
# !          → [12, 13)
# [SEP]      → [ 0,  0)
```

### Word IDs

```python
# Get word index for each token
encoded = tokenizer("Hello world", return_offsets_mapping=True)
word_ids = encoded.word_ids()

print(word_ids)
# [None, 0, 1, None]
# None = special token, 0 = first word, 1 = second word
```

**Use case**: Token classification (NER, POS tagging)

### Character to token mapping

```python
text = "Machine learning is awesome"
encoded = tokenizer(text, return_offsets_mapping=True)

# Find token for character position
char_pos = 8  # "l" in "learning"
token_idx = encoded.char_to_token(char_pos)

print(f"Character {char_pos} is in token {token_idx}: {encoded.tokens()[token_idx]}")
# Character 8 is in token 2: learning
```

**Use case**: Question answering (map answer character span to tokens)

### Sequence pairs

```python
# Encode sentence pair
encoded = tokenizer("Question here", "Answer here", return_offsets_mapping=True)

# Get sequence IDs (which sequence each token belongs to)
sequence_ids = encoded.sequence_ids()
print(sequence_ids)
# [None, 0, 0, 0, None, 1, 1, 1, None]
# None = special token, 0 = question, 1 = answer
```

## Model integration

### Use with transformers models

```python
from transformers import AutoModel, AutoTokenizer
import torch

# Load model and tokenizer
model = AutoModel.from_pretrained("bert-base-uncased")
tokenizer = AutoTokenizer.from_pretrained("bert-base-uncased")

# Tokenize
text = "Hello world"
inputs = tokenizer(text, return_tensors="pt")

# Forward pass
with torch.no_grad():
    outputs = model(**inputs)

# Get embeddings
last_hidden_state = outputs.last_hidden_state
print(last_hidden_state.shape)  # [1, seq_len, hidden_size]
```

### Custom model with custom tokenizer

```python
from transformers import BertConfig, BertModel

# Train custom tokenizer
from tokenizers import Tokenizer, models, trainers
tokenizer = Tokenizer(models.BPE())
trainer = trainers.BpeTrainer(vocab_size=30000)
tokenizer.train(files=["data.txt"], trainer=trainer)

# Wrap for transformers
from transformers import PreTrainedTokenizerFast
fast_tokenizer = PreTrainedTokenizerFast(
    tokenizer_object=tokenizer,
    unk_token="[UNK]",
    pad_token="[PAD]"
)

# Create model with custom vocab size
config = BertConfig(vocab_size=30000)
model = BertModel(config)

# Use together
inputs = fast_tokenizer("Hello world", return_tensors="pt")
outputs = model(**inputs)
```

### Save and load together

```python
# Save both
model.save_pretrained("my-model")
tokenizer.save_pretrained("my-model")

# Directory structure:
# my-model/
#   ├── config.json
#   ├── pytorch_model.bin
#   ├── tokenizer.json
#   ├── tokenizer_config.json
#   └── special_tokens_map.json

# Load both
from transformers import AutoModel, AutoTokenizer

model = AutoModel.from_pretrained("my-model")
tokenizer = AutoTokenizer.from_pretrained("my-model")
```

## Advanced features

### Multimodal tokenization

```python
from transformers import AutoTokenizer

# LLaVA-style (image + text)
tokenizer = AutoTokenizer.from_pretrained("llava-hf/llava-1.5-7b-hf")

# Add image placeholder token
tokenizer.add_special_tokens({"additional_special_tokens": ["<image>"]})

# Use in prompt
text = "Describe this image: <image>"
inputs = tokenizer(text, return_tensors="pt")
```

### Template formatting

```python
# Chat template
messages = [
    {"role": "system", "content": "You are a helpful assistant."},
    {"role": "user", "content": "Hello!"},
    {"role": "assistant", "content": "Hi! How can I help?"},
    {"role": "user", "content": "What's the weather?"}
]

# Apply chat template (if tokenizer has one)
if hasattr(tokenizer, "apply_chat_template"):
    text = tokenizer.apply_chat_template(messages, tokenize=False)
    inputs = tokenizer(text, return_tensors="pt")
```

### Custom template

```python
from transformers import PreTrainedTokenizerFast

tokenizer = PreTrainedTokenizerFast(tokenizer_file="tokenizer.json")

# Define chat template
tokenizer.chat_template = """
{%- for message in messages %}
    {%- if message['role'] == 'system' %}
        System: {{ message['content'] }}\\n
    {%- elif message['role'] == 'user' %}
        User: {{ message['content'] }}\\n
    {%- elif message['role'] == 'assistant' %}
        Assistant: {{ message['content'] }}\\n
    {%- endif %}
{%- endfor %}
Assistant:
"""

# Use template
text = tokenizer.apply_chat_template(messages, tokenize=False)
```

## Performance optimization

### Batch processing

```python
# Process large datasets efficiently
from datasets import load_dataset

dataset = load_dataset("imdb", split="train[:1000]")

# Tokenize in batches
def tokenize_function(examples):
    return tokenizer(
        examples["text"],
        padding="max_length",
        truncation=True,
        max_length=512
    )

# Map over dataset (batched)
tokenized_dataset = dataset.map(
    tokenize_function,
    batched=True,
    batch_size=1000,
    num_proc=4  # Parallel processing
)
```

### Caching

```python
# Enable caching for repeated tokenization
tokenizer = AutoTokenizer.from_pretrained(
    "bert-base-uncased",
    use_fast=True,
    cache_dir="./cache"  # Cache tokenizer files
)

# Tokenize with caching
from functools import lru_cache

@lru_cache(maxsize=10000)
def cached_tokenize(text):
    return tuple(tokenizer.encode(text))

# Reuses cached results for repeated inputs
```

### Memory efficiency

```python
# For very large datasets, use streaming
from datasets import load_dataset

dataset = load_dataset("pile", split="train", streaming=True)

def process_batch(batch):
    # Tokenize
    tokens = tokenizer(batch["text"], truncation=True, max_length=512)

    # Process tokens...

    return tokens

# Process in chunks (memory efficient)
for batch in dataset.batch(batch_size=1000):
    processed = process_batch(batch)
```

## Troubleshooting

### Issue: Tokenizer not fast

**Symptom**:
```python
tokenizer.is_fast  # False
```

**Solution**: Install tokenizers library
```bash
pip install tokenizers
```

### Issue: Special tokens not working

**Symptom**: Special tokens are split into subwords

**Solution**: Add as special tokens, not regular tokens
```python
# Wrong
tokenizer.add_tokens(["<|image|>"])

# Correct
tokenizer.add_special_tokens({"additional_special_tokens": ["<|image|>"]})
```

### Issue: Offset mapping not available

**Symptom**:
```python
tokenizer("text", return_offsets_mapping=True)
# Error: return_offsets_mapping not supported
```

**Solution**: Use fast tokenizer
```python
from transformers import AutoTokenizer

# Load fast version
tokenizer = AutoTokenizer.from_pretrained("bert-base-uncased", use_fast=True)
```

### Issue: Padding inconsistent

**Symptom**: Some sequences padded, others not

**Solution**: Specify padding strategy
```python
# Explicit padding
tokenizer(
    texts,
    padding="max_length",  # or "longest"
    max_length=128
)
```

## Best practices

1. **Always use fast tokenizers**:
   - 5-10× faster
   - Full alignment tracking
   - Better batch processing

2. **Save tokenizer with model**:
   - Ensures reproducibility
   - Prevents version mismatches

3. **Use batch processing for datasets**:
   - Tokenize with `.map(batched=True)`
   - Set `num_proc` for parallelism

4. **Enable caching for repeated inputs**:
   - Use `lru_cache` for inference
   - Cache tokenizer files with `cache_dir`

5. **Handle special tokens properly**:
   - Use `add_special_tokens()` for never-split tokens
   - Resize embeddings after adding tokens

6. **Test alignment for downstream tasks**:
   - Verify `offset_mapping` is correct
   - Test `char_to_token()` on samples

7. **Version control tokenizer config**:
   - Save `tokenizer_config.json`
   - Document custom templates
   - Track vocabulary changes




### Pipeline

# Tokenization Pipeline Components

Complete guide to normalizers, pre-tokenizers, models, post-processors, and decoders.

## Pipeline overview

**Full tokenization pipeline**:
```
Raw Text
  ↓
Normalization (cleaning, lowercasing)
  ↓
Pre-tokenization (split into words)
  ↓
Model (apply BPE/WordPiece/Unigram)
  ↓
Post-processing (add special tokens)
  ↓
Token IDs
```

**Decoding reverses the process**:
```
Token IDs
  ↓
Decoder (handle special encodings)
  ↓
Raw Text
```

## Normalizers

Clean and standardize input text.

### Common normalizers

**Lowercase**:
```python
from tokenizers.normalizers import Lowercase

tokenizer.normalizer = Lowercase()

# Input: "Hello WORLD"
# Output: "hello world"
```

**Unicode normalization**:
```python
from tokenizers.normalizers import NFD, NFC, NFKD, NFKC

# NFD: Canonical decomposition
tokenizer.normalizer = NFD()
# "é" → "e" + "́" (separate characters)

# NFC: Canonical composition (default)
tokenizer.normalizer = NFC()
# "e" + "́" → "é" (composed)

# NFKD: Compatibility decomposition
tokenizer.normalizer = NFKD()
# "ﬁ" → "f" + "i"

# NFKC: Compatibility composition
tokenizer.normalizer = NFKC()
# Most aggressive normalization
```

**Strip accents**:
```python
from tokenizers.normalizers import StripAccents

tokenizer.normalizer = StripAccents()

# Input: "café"
# Output: "cafe"
```

**Whitespace handling**:
```python
from tokenizers.normalizers import Strip, StripAccents

# Remove leading/trailing whitespace
tokenizer.normalizer = Strip()

# Input: "  hello  "
# Output: "hello"
```

**Replace patterns**:
```python
from tokenizers.normalizers import Replace

# Replace newlines with spaces
tokenizer.normalizer = Replace("\\n", " ")

# Input: "hello\\nworld"
# Output: "hello world"
```

### Combining normalizers

```python
from tokenizers.normalizers import Sequence, NFD, Lowercase, StripAccents

# BERT-style normalization
tokenizer.normalizer = Sequence([
    NFD(),           # Unicode decomposition
    Lowercase(),     # Convert to lowercase
    StripAccents()   # Remove accents
])

# Input: "Café au Lait"
# After NFD: "Café au Lait" (e + ́)
# After Lowercase: "café au lait"
# After StripAccents: "cafe au lait"
```

### Use case examples

**Case-insensitive model (BERT)**:
```python
from tokenizers.normalizers import BertNormalizer

# All-in-one BERT normalization
tokenizer.normalizer = BertNormalizer(
    clean_text=True,        # Remove control characters
    handle_chinese_chars=True,  # Add spaces around Chinese
    strip_accents=True,     # Remove accents
    lowercase=True          # Lowercase
)
```

**Case-sensitive model (GPT-2)**:
```python
# Minimal normalization
tokenizer.normalizer = NFC()  # Only normalize Unicode
```

**Multilingual (mBERT)**:
```python
# Preserve scripts, normalize form
tokenizer.normalizer = NFKC()
```

## Pre-tokenizers

Split text into word-like units before tokenization.

### Whitespace splitting

```python
from tokenizers.pre_tokenizers import Whitespace

tokenizer.pre_tokenizer = Whitespace()

# Input: "Hello world! How are you?"
# Output: [("Hello", (0, 5)), ("world!", (6, 12)), ("How", (13, 16)), ("are", (17, 20)), ("you?", (21, 25))]
```

### Punctuation isolation

```python
from tokenizers.pre_tokenizers import Punctuation

tokenizer.pre_tokenizer = Punctuation()

# Input: "Hello, world!"
# Output: [("Hello", ...), (",", ...), ("world", ...), ("!", ...)]
```

### Byte-level (GPT-2)

```python
from tokenizers.pre_tokenizers import ByteLevel

tokenizer.pre_tokenizer = ByteLevel(add_prefix_space=True)

# Input: "Hello world"
# Output: Byte-level tokens with Ġ prefix for spaces
# [("ĠHello", ...), ("Ġworld", ...)]
```

**Key feature**: Handles ALL Unicode characters (256 byte combinations)

### Metaspace (SentencePiece)

```python
from tokenizers.pre_tokenizers import Metaspace

tokenizer.pre_tokenizer = Metaspace(replacement="▁", add_prefix_space=True)

# Input: "Hello world"
# Output: [("▁Hello", ...), ("▁world", ...)]
```

**Used by**: T5, ALBERT (via SentencePiece)

### Digits splitting

```python
from tokenizers.pre_tokenizers import Digits

# Split digits individually
tokenizer.pre_tokenizer = Digits(individual_digits=True)

# Input: "Room 123"
# Output: [("Room", ...), ("1", ...), ("2", ...), ("3", ...)]

# Keep digits together
tokenizer.pre_tokenizer = Digits(individual_digits=False)

# Input: "Room 123"
# Output: [("Room", ...), ("123", ...)]
```

### BERT pre-tokenizer

```python
from tokenizers.pre_tokenizers import BertPreTokenizer

tokenizer.pre_tokenizer = BertPreTokenizer()

# Splits on whitespace and punctuation, preserves CJK
# Input: "Hello, 世界!"
# Output: [("Hello", ...), (",", ...), ("世", ...), ("界", ...), ("!", ...)]
```

### Combining pre-tokenizers

```python
from tokenizers.pre_tokenizers import Sequence, Whitespace, Punctuation

tokenizer.pre_tokenizer = Sequence([
    Whitespace(),     # Split on whitespace first
    Punctuation()     # Then isolate punctuation
])

# Input: "Hello, world!"
# After Whitespace: [("Hello,", ...), ("world!", ...)]
# After Punctuation: [("Hello", ...), (",", ...), ("world", ...), ("!", ...)]
```

### Pre-tokenizer comparison

| Pre-tokenizer     | Use Case                        | Example                                    |
|-------------------|---------------------------------|--------------------------------------------|
| Whitespace        | Simple English                  | "Hello world" → ["Hello", "world"]         |
| Punctuation       | Isolate symbols                 | "world!" → ["world", "!"]                  |
| ByteLevel         | Multilingual, emojis            | "🌍" → byte tokens                          |
| Metaspace         | SentencePiece-style             | "Hello" → ["▁Hello"]                       |
| BertPreTokenizer  | BERT-style (CJK aware)          | "世界" → ["世", "界"]                        |
| Digits            | Handle numbers                  | "123" → ["1", "2", "3"] or ["123"]        |

## Models

Core tokenization algorithms.

### BPE Model

```python
from tokenizers.models import BPE

model = BPE(
    vocab=None,           # Or provide pre-built vocab
    merges=None,          # Or provide merge rules
    unk_token="[UNK]",    # Unknown token
    continuing_subword_prefix="",
    end_of_word_suffix="",
    fuse_unk=False        # Keep unknown tokens separate
)

tokenizer = Tokenizer(model)
```

**Parameters**:
- `vocab`: Dict of token → id
- `merges`: List of merge rules `["a b", "ab c"]`
- `unk_token`: Token for unknown words
- `continuing_subword_prefix`: Prefix for subwords (empty for GPT-2)
- `end_of_word_suffix`: Suffix for last subword (empty for GPT-2)

### WordPiece Model

```python
from tokenizers.models import WordPiece

model = WordPiece(
    vocab=None,
    unk_token="[UNK]",
    max_input_chars_per_word=100,  # Max word length
    continuing_subword_prefix="##"  # BERT-style prefix
)

tokenizer = Tokenizer(model)
```

**Key difference**: Uses `##` prefix for continuing subwords.

### Unigram Model

```python
from tokenizers.models import Unigram

model = Unigram(
    vocab=None,  # List of (token, score) tuples
    unk_id=0,    # ID for unknown token
    byte_fallback=False  # Fall back to bytes if no match
)

tokenizer = Tokenizer(model)
```

**Probabilistic**: Selects tokenization with highest probability.

### WordLevel Model

```python
from tokenizers.models import WordLevel

# Simple word-to-ID mapping (no subwords)
model = WordLevel(
    vocab=None,
    unk_token="[UNK]"
)

tokenizer = Tokenizer(model)
```

**Warning**: Requires huge vocabulary (one token per word).

## Post-processors

Add special tokens and format output.

### Template processing

**BERT-style** (`[CLS] sentence [SEP]`):
```python
from tokenizers.processors import TemplateProcessing

tokenizer.post_processor = TemplateProcessing(
    single="[CLS] $A [SEP]",
    pair="[CLS] $A [SEP] $B [SEP]",
    special_tokens=[
        ("[CLS]", 101),
        ("[SEP]", 102),
    ],
)

# Single sentence
output = tokenizer.encode("Hello world")
# [101, ..., 102]  ([CLS] hello world [SEP])

# Sentence pair
output = tokenizer.encode("Hello", "world")
# [101, ..., 102, ..., 102]  ([CLS] hello [SEP] world [SEP])
```

**GPT-2 style** (`sentence <|endoftext|>`):
```python
tokenizer.post_processor = TemplateProcessing(
    single="$A <|endoftext|>",
    special_tokens=[
        ("<|endoftext|>", 50256),
    ],
)
```

**RoBERTa style** (`<s> sentence </s>`):
```python
tokenizer.post_processor = TemplateProcessing(
    single="<s> $A </s>",
    pair="<s> $A </s> </s> $B </s>",
    special_tokens=[
        ("<s>", 0),
        ("</s>", 2),
    ],
)
```

**T5 style** (no special tokens):
```python
# T5 doesn't add special tokens via post-processor
tokenizer.post_processor = None
```

### RobertaProcessing

```python
from tokenizers.processors import RobertaProcessing

tokenizer.post_processor = RobertaProcessing(
    sep=("</s>", 2),
    cls=("<s>", 0),
    add_prefix_space=True,  # Add space before first token
    trim_offsets=True       # Trim leading space from offsets
)
```

### ByteLevelProcessing

```python
from tokenizers.processors import ByteLevel as ByteLevelProcessing

tokenizer.post_processor = ByteLevelProcessing(
    trim_offsets=True  # Remove Ġ from offsets
)
```

## Decoders

Convert token IDs back to text.

### ByteLevel decoder

```python
from tokenizers.decoders import ByteLevel

tokenizer.decoder = ByteLevel()

# Handles byte-level tokens
# ["ĠHello", "Ġworld"] → "Hello world"
```

### WordPiece decoder

```python
from tokenizers.decoders import WordPiece

tokenizer.decoder = WordPiece(prefix="##")

# Removes ## prefix and concatenates
# ["token", "##ization"] → "tokenization"
```

### Metaspace decoder

```python
from tokenizers.decoders import Metaspace

tokenizer.decoder = Metaspace(replacement="▁", add_prefix_space=True)

# Converts ▁ back to spaces
# ["▁Hello", "▁world"] → "Hello world"
```

### BPEDecoder

```python
from tokenizers.decoders import BPEDecoder

tokenizer.decoder = BPEDecoder(suffix="</w>")

# Removes suffix and concatenates
# ["token", "ization</w>"] → "tokenization"
```

### Sequence decoder

```python
from tokenizers.decoders import Sequence, ByteLevel, Strip

tokenizer.decoder = Sequence([
    ByteLevel(),      # Decode byte-level first
    Strip(' ', 1, 1)  # Strip leading/trailing spaces
])
```

## Complete pipeline examples

### BERT tokenizer

```python
from tokenizers import Tokenizer
from tokenizers.models import WordPiece
from tokenizers.normalizers import BertNormalizer
from tokenizers.pre_tokenizers import BertPreTokenizer
from tokenizers.processors import TemplateProcessing
from tokenizers.decoders import WordPiece as WordPieceDecoder

# Model
tokenizer = Tokenizer(WordPiece(unk_token="[UNK]"))

# Normalization
tokenizer.normalizer = BertNormalizer(lowercase=True)

# Pre-tokenization
tokenizer.pre_tokenizer = BertPreTokenizer()

# Post-processing
tokenizer.post_processor = TemplateProcessing(
    single="[CLS] $A [SEP]",
    pair="[CLS] $A [SEP] $B [SEP]",
    special_tokens=[("[CLS]", 101), ("[SEP]", 102)],
)

# Decoder
tokenizer.decoder = WordPieceDecoder(prefix="##")

# Enable padding
tokenizer.enable_padding(pad_id=0, pad_token="[PAD]")

# Enable truncation
tokenizer.enable_truncation(max_length=512)
```

### GPT-2 tokenizer

```python
from tokenizers import Tokenizer
from tokenizers.models import BPE
from tokenizers.normalizers import NFC
from tokenizers.pre_tokenizers import ByteLevel
from tokenizers.decoders import ByteLevel as ByteLevelDecoder
from tokenizers.processors import TemplateProcessing

# Model
tokenizer = Tokenizer(BPE())

# Normalization (minimal)
tokenizer.normalizer = NFC()

# Byte-level pre-tokenization
tokenizer.pre_tokenizer = ByteLevel(add_prefix_space=False)

# Post-processing
tokenizer.post_processor = TemplateProcessing(
    single="$A <|endoftext|>",
    special_tokens=[("<|endoftext|>", 50256)],
)

# Byte-level decoder
tokenizer.decoder = ByteLevelDecoder()
```

### T5 tokenizer (SentencePiece-style)

```python
from tokenizers import Tokenizer
from tokenizers.models import Unigram
from tokenizers.normalizers import NFKC
from tokenizers.pre_tokenizers import Metaspace
from tokenizers.decoders import Metaspace as MetaspaceDecoder

# Model
tokenizer = Tokenizer(Unigram())

# Normalization
tokenizer.normalizer = NFKC()

# Metaspace pre-tokenization
tokenizer.pre_tokenizer = Metaspace(replacement="▁", add_prefix_space=True)

# No post-processing (T5 doesn't add CLS/SEP)
tokenizer.post_processor = None

# Metaspace decoder
tokenizer.decoder = MetaspaceDecoder(replacement="▁", add_prefix_space=True)
```

## Alignment tracking

Track token positions in original text.

### Basic alignment

```python
text = "Hello, world!"
output = tokenizer.encode(text)

for token, (start, end) in zip(output.tokens, output.offsets):
    print(f"{token:10s} → [{start:2d}, {end:2d}): {text[start:end]!r}")

# Output:
# [CLS]      → [ 0,  0): ''
# hello      → [ 0,  5): 'Hello'
# ,          → [ 5,  6): ','
# world      → [ 7, 12): 'world'
# !          → [12, 13): '!'
# [SEP]      → [ 0,  0): ''
```

### Word-level alignment

```python
# Get word_ids (which word each token belongs to)
encoding = tokenizer.encode("Hello world")
word_ids = encoding.word_ids

print(word_ids)
# [None, 0, 0, 1, None]
# None = special token, 0 = first word, 1 = second word
```

**Use case**: Token classification (NER)
```python
# Align predictions to words
predictions = ["O", "B-PER", "I-PER", "O", "O"]
word_predictions = {}

for token_idx, word_idx in enumerate(encoding.word_ids):
    if word_idx is not None and word_idx not in word_predictions:
        word_predictions[word_idx] = predictions[token_idx]

print(word_predictions)
# {0: "B-PER", 1: "O"}  # First word is PERSON, second is OTHER
```

### Span alignment

```python
# Find token span for character span
text = "Machine learning is awesome"
char_start, char_end = 8, 16  # "learning"

encoding = tokenizer.encode(text)

# Find token span
token_start = encoding.char_to_token(char_start)
token_end = encoding.char_to_token(char_end - 1) + 1

print(f"Tokens {token_start}:{token_end} = {encoding.tokens[token_start:token_end]}")
# Tokens 2:3 = ['learning']
```

**Use case**: Question answering (extract answer span)

## Custom components

### Custom normalizer

```python
from tokenizers import NormalizedString, Normalizer

class CustomNormalizer:
    def normalize(self, normalized: NormalizedString):
        # Custom normalization logic
        normalized.lowercase()
        normalized.replace("  ", " ")  # Replace double spaces

# Use custom normalizer
tokenizer.normalizer = CustomNormalizer()
```

### Custom pre-tokenizer

```python
from tokenizers import PreTokenizedString

class CustomPreTokenizer:
    def pre_tokenize(self, pretok: PreTokenizedString):
        # Custom pre-tokenization logic
        pretok.split(lambda i, char: char.isspace())

tokenizer.pre_tokenizer = CustomPreTokenizer()
```

## Troubleshooting

### Issue: Misaligned offsets

**Symptom**: Offsets don't match original text
```python
text = "  hello"  # Leading spaces
offsets = [(0, 5)]  # Expects "  hel"
```

**Solution**: Check normalization strips spaces
```python
# Preserve offsets
tokenizer.normalizer = Sequence([
    Strip(),  # This changes offsets!
])

# Use trim_offsets in post-processor instead
tokenizer.post_processor = ByteLevelProcessing(trim_offsets=True)
```

### Issue: Special tokens not added

**Symptom**: No [CLS] or [SEP] in output

**Solution**: Check post-processor is set
```python
tokenizer.post_processor = TemplateProcessing(
    single="[CLS] $A [SEP]",
    special_tokens=[("[CLS]", 101), ("[SEP]", 102)],
)
```

### Issue: Incorrect decoding

**Symptom**: Decoded text has ## or ▁

**Solution**: Set correct decoder
```python
# For WordPiece
tokenizer.decoder = WordPieceDecoder(prefix="##")

# For SentencePiece
tokenizer.decoder = MetaspaceDecoder(replacement="▁")
```

## Best practices

1. **Match pipeline to model architecture**:
   - BERT → BertNormalizer + BertPreTokenizer + WordPiece
   - GPT-2 → NFC + ByteLevel + BPE
   - T5 → NFKC + Metaspace + Unigram

2. **Test pipeline on sample inputs**:
   - Check normalization doesn't over-normalize
   - Verify pre-tokenization splits correctly
   - Ensure decoding reconstructs text

3. **Preserve alignment for downstream tasks**:
   - Use `trim_offsets` instead of stripping in normalizer
   - Test `char_to_token()` on sample spans

4. **Document your pipeline**:
   - Save complete tokenizer config
   - Document special tokens
   - Note any custom components




### Training

# Training Custom Tokenizers

Complete guide to training tokenizers from scratch.

## Training workflow

### Step 1: Choose tokenization algorithm

**Decision tree**:
- **GPT-style model** → BPE
- **BERT-style model** → WordPiece
- **Multilingual/No word boundaries** → Unigram

### Step 2: Prepare training data

```python
# Option 1: From files
files = ["train.txt", "validation.txt"]

# Option 2: From Python list
texts = [
    "This is the first sentence.",
    "This is the second sentence.",
    # ... more texts
]

# Option 3: From dataset iterator
from datasets import load_dataset

dataset = load_dataset("wikitext", "wikitext-103-raw-v1", split="train")

def batch_iterator(batch_size=1000):
    for i in range(0, len(dataset), batch_size):
        yield dataset[i:i + batch_size]["text"]
```

### Step 3: Initialize tokenizer

**BPE example**:
```python
from tokenizers import Tokenizer
from tokenizers.models import BPE
from tokenizers.trainers import BpeTrainer
from tokenizers.pre_tokenizers import ByteLevel
from tokenizers.decoders import ByteLevel as ByteLevelDecoder

tokenizer = Tokenizer(BPE())
tokenizer.pre_tokenizer = ByteLevel()
tokenizer.decoder = ByteLevelDecoder()

trainer = BpeTrainer(
    vocab_size=50000,
    min_frequency=2,
    special_tokens=["<|endoftext|>", "<|padding|>"],
    show_progress=True
)
```

**WordPiece example**:
```python
from tokenizers.models import WordPiece
from tokenizers.trainers import WordPieceTrainer
from tokenizers.normalizers import BertNormalizer
from tokenizers.pre_tokenizers import BertPreTokenizer

tokenizer = Tokenizer(WordPiece(unk_token="[UNK]"))
tokenizer.normalizer = BertNormalizer(lowercase=True)
tokenizer.pre_tokenizer = BertPreTokenizer()

trainer = WordPieceTrainer(
    vocab_size=30522,
    min_frequency=2,
    special_tokens=["[UNK]", "[CLS]", "[SEP]", "[PAD]", "[MASK]"],
    continuing_subword_prefix="##",
    show_progress=True
)
```

**Unigram example**:
```python
from tokenizers.models import Unigram
from tokenizers.trainers import UnigramTrainer

tokenizer = Tokenizer(Unigram())

trainer = UnigramTrainer(
    vocab_size=8000,
    special_tokens=["<unk>", "<s>", "</s>", "<pad>"],
    unk_token="<unk>",
    show_progress=True
)
```

### Step 4: Train

```python
# From files
tokenizer.train(files=files, trainer=trainer)

# From iterator (recommended for large datasets)
tokenizer.train_from_iterator(
    batch_iterator(),
    trainer=trainer,
    length=len(dataset)  # Optional, for progress bar
)
```

**Training time** (30k vocab on 16-core CPU):
- 10 MB: 15-30 seconds
- 100 MB: 1-3 minutes
- 1 GB: 15-30 minutes
- 10 GB: 2-4 hours

### Step 5: Add post-processing

```python
from tokenizers.processors import TemplateProcessing

# BERT-style
tokenizer.post_processor = TemplateProcessing(
    single="[CLS] $A [SEP]",
    pair="[CLS] $A [SEP] $B [SEP]",
    special_tokens=[
        ("[CLS]", tokenizer.token_to_id("[CLS]")),
        ("[SEP]", tokenizer.token_to_id("[SEP]")),
    ],
)

# GPT-2 style
tokenizer.post_processor = TemplateProcessing(
    single="$A <|endoftext|>",
    special_tokens=[
        ("<|endoftext|>", tokenizer.token_to_id("<|endoftext|>")),
    ],
)
```

### Step 6: Save

```python
# Save to JSON
tokenizer.save("my-tokenizer.json")

# Save to directory (for transformers)
tokenizer.save("my-tokenizer-dir/tokenizer.json")

# Convert to transformers format
from transformers import PreTrainedTokenizerFast

transformers_tokenizer = PreTrainedTokenizerFast(
    tokenizer_object=tokenizer,
    unk_token="[UNK]",
    pad_token="[PAD]",
    cls_token="[CLS]",
    sep_token="[SEP]",
    mask_token="[MASK]"
)

transformers_tokenizer.save_pretrained("my-tokenizer-dir")
```

## Trainer configuration

### BpeTrainer parameters

```python
from tokenizers.trainers import BpeTrainer

trainer = BpeTrainer(
    vocab_size=30000,              # Target vocabulary size
    min_frequency=2,               # Minimum frequency for merges
    special_tokens=["[UNK]"],      # Special tokens (added first)
    limit_alphabet=1000,           # Limit initial alphabet size
    initial_alphabet=[],           # Pre-defined initial characters
    show_progress=True,            # Show progress bar
    continuing_subword_prefix="",  # Prefix for continuing subwords
    end_of_word_suffix=""          # Suffix for end of words
)
```

**Parameter tuning**:
- **vocab_size**: Start with 30k for English, 50k for multilingual
- **min_frequency**: 2-5 for large corpora, 1 for small
- **limit_alphabet**: Reduce for non-English (CJK languages)

### WordPieceTrainer parameters

```python
from tokenizers.trainers import WordPieceTrainer

trainer = WordPieceTrainer(
    vocab_size=30522,              # BERT uses 30,522
    min_frequency=2,
    special_tokens=["[UNK]", "[CLS]", "[SEP]", "[PAD]", "[MASK]"],
    limit_alphabet=1000,
    continuing_subword_prefix="##", # BERT-style prefix
    show_progress=True
)
```

### UnigramTrainer parameters

```python
from tokenizers.trainers import UnigramTrainer

trainer = UnigramTrainer(
    vocab_size=8000,               # Typically smaller than BPE/WordPiece
    special_tokens=["<unk>", "<s>", "</s>"],
    unk_token="<unk>",
    max_piece_length=16,           # Maximum token length
    n_sub_iterations=2,            # EM algorithm iterations
    shrinking_factor=0.75,         # Vocabulary reduction rate
    show_progress=True
)
```

## Training from large datasets

### Memory-efficient training

```python
from datasets import load_dataset
from tokenizers import Tokenizer
from tokenizers.models import BPE
from tokenizers.trainers import BpeTrainer

# Load dataset
dataset = load_dataset("wikipedia", "20220301.en", split="train", streaming=True)

# Create iterator (yields batches)
def batch_iterator(batch_size=1000):
    batch = []
    for sample in dataset:
        batch.append(sample["text"])
        if len(batch) >= batch_size:
            yield batch
            batch = []
    if batch:
        yield batch

# Initialize tokenizer
tokenizer = Tokenizer(BPE())
trainer = BpeTrainer(vocab_size=50000, special_tokens=["<|endoftext|>"])

# Train (memory efficient - streams data)
tokenizer.train_from_iterator(
    batch_iterator(),
    trainer=trainer
)
```

**Memory usage**: ~200 MB (vs 10+ GB loading full dataset)

### Multi-file training

```python
import glob

# Find all training files
files = glob.glob("data/train/*.txt")
print(f"Training on {len(files)} files")

# Train on all files
tokenizer.train(files=files, trainer=trainer)
```

### Parallel training (multi-processing)

```python
from multiprocessing import Pool, cpu_count
import os

def train_shard(shard_files):
    """Train tokenizer on a shard of files."""
    tokenizer = Tokenizer(BPE())
    trainer = BpeTrainer(vocab_size=50000)
    tokenizer.train(files=shard_files, trainer=trainer)
    return tokenizer.get_vocab()

# Split files into shards
num_shards = cpu_count()
file_shards = [files[i::num_shards] for i in range(num_shards)]

# Train shards in parallel
with Pool(num_shards) as pool:
    vocab_shards = pool.map(train_shard, file_shards)

# Merge vocabularies (custom logic needed)
# This is a simplified example - real implementation would merge intelligently
final_vocab = {}
for vocab in vocab_shards:
    final_vocab.update(vocab)
```

## Domain-specific tokenizers

### Code tokenizer

```python
from tokenizers import Tokenizer
from tokenizers.models import BPE
from tokenizers.trainers import BpeTrainer
from tokenizers.pre_tokenizers import ByteLevel
from tokenizers.normalizers import Sequence, NFC

# Code-optimized configuration
tokenizer = Tokenizer(BPE())

# Minimal normalization (preserve case, whitespace)
tokenizer.normalizer = NFC()  # Only normalize Unicode

# Byte-level pre-tokenization (handles all characters)
tokenizer.pre_tokenizer = ByteLevel()

# Train on code corpus
trainer = BpeTrainer(
    vocab_size=50000,
    special_tokens=["<|endoftext|>", "<|pad|>"],
    min_frequency=2
)

tokenizer.train(files=["code_corpus.txt"], trainer=trainer)
```

### Medical/scientific tokenizer

```python
# Preserve case and special characters
from tokenizers.normalizers import NFKC
from tokenizers.pre_tokenizers import Whitespace, Punctuation, Sequence

tokenizer = Tokenizer(BPE())

# Minimal normalization
tokenizer.normalizer = NFKC()

# Preserve medical terms
tokenizer.pre_tokenizer = Sequence([
    Whitespace(),
    Punctuation(behavior="isolated")  # Keep punctuation separate
])

trainer = BpeTrainer(
    vocab_size=50000,
    special_tokens=["[UNK]", "[CLS]", "[SEP]"],
    min_frequency=3  # Higher threshold for rare medical terms
)

tokenizer.train(files=["pubmed_corpus.txt"], trainer=trainer)
```

### Multilingual tokenizer

```python
# Handle multiple scripts
from tokenizers.normalizers import NFKC, Lowercase, Sequence

tokenizer = Tokenizer(BPE())

# Normalize but don't lowercase (preserves script differences)
tokenizer.normalizer = NFKC()

# Byte-level handles all Unicode
from tokenizers.pre_tokenizers import ByteLevel
tokenizer.pre_tokenizer = ByteLevel()

trainer = BpeTrainer(
    vocab_size=100000,  # Larger vocab for multiple languages
    special_tokens=["<unk>", "<s>", "</s>"],
    limit_alphabet=None  # No limit (handles all scripts)
)

# Train on multilingual corpus
tokenizer.train(files=["multilingual_corpus.txt"], trainer=trainer)
```

## Vocabulary size selection

### Guidelines by task

| Task                  | Recommended Vocab Size | Rationale |
|-----------------------|------------------------|-----------|
| English (monolingual) | 30,000 - 50,000       | Balanced coverage |
| Multilingual          | 50,000 - 250,000      | More languages = more tokens |
| Code                  | 30,000 - 50,000       | Similar to English |
| Domain-specific       | 10,000 - 30,000       | Smaller, focused vocabulary |
| Character-level tasks | 1,000 - 5,000         | Only characters + subwords |

### Vocabulary size impact

**Small vocab (10k)**:
- Pros: Faster training, smaller model, less memory
- Cons: More tokens per sentence, worse OOV handling

**Medium vocab (30k-50k)**:
- Pros: Good balance, standard choice
- Cons: None (recommended default)

**Large vocab (100k+)**:
- Pros: Fewer tokens per sentence, better OOV
- Cons: Slower training, larger embedding table

### Empirical testing

```python
# Train multiple tokenizers with different vocab sizes
vocab_sizes = [10000, 30000, 50000, 100000]

for vocab_size in vocab_sizes:
    tokenizer = Tokenizer(BPE())
    trainer = BpeTrainer(vocab_size=vocab_size)
    tokenizer.train(files=["sample.txt"], trainer=trainer)

    # Evaluate on test set
    test_text = "Test sentence for evaluation..."
    tokens = tokenizer.encode(test_text).ids

    print(f"Vocab: {vocab_size:6d} | Tokens: {len(tokens):3d} | Avg: {len(test_text)/len(tokens):.2f} chars/token")

# Example output:
# Vocab:  10000 | Tokens:  12 | Avg: 2.33 chars/token
# Vocab:  30000 | Tokens:   8 | Avg: 3.50 chars/token
# Vocab:  50000 | Tokens:   7 | Avg: 4.00 chars/token
# Vocab: 100000 | Tokens:   6 | Avg: 4.67 chars/token
```

## Testing tokenizer quality

### Coverage test

```python
# Test on held-out data
test_corpus = load_dataset("wikitext", "wikitext-103-raw-v1", split="test")

total_tokens = 0
unk_tokens = 0
unk_id = tokenizer.token_to_id("[UNK]")

for text in test_corpus["text"]:
    if text.strip():
        encoding = tokenizer.encode(text)
        total_tokens += len(encoding.ids)
        unk_tokens += encoding.ids.count(unk_id)

unk_rate = unk_tokens / total_tokens
print(f"Unknown token rate: {unk_rate:.2%}")

# Good quality: <1% unknown tokens
# Acceptable: 1-5%
# Poor: >5%
```

### Compression test

```python
# Measure tokenization efficiency
import numpy as np

token_lengths = []

for text in test_corpus["text"][:1000]:
    if text.strip():
        encoding = tokenizer.encode(text)
        chars_per_token = len(text) / len(encoding.ids)
        token_lengths.append(chars_per_token)

avg_chars_per_token = np.mean(token_lengths)
print(f"Average characters per token: {avg_chars_per_token:.2f}")

# Good: 4-6 chars/token (English)
# Acceptable: 3-4 chars/token
# Poor: <3 chars/token (under-compression)
```

### Semantic test

```python
# Manually inspect tokenization of common words/phrases
test_phrases = [
    "tokenization",
    "machine learning",
    "artificial intelligence",
    "preprocessing",
    "hello world"
]

for phrase in test_phrases:
    tokens = tokenizer.encode(phrase).tokens
    print(f"{phrase:25s} → {tokens}")

# Good tokenization:
# tokenization              → ['token', 'ization']
# machine learning          → ['machine', 'learning']
# artificial intelligence   → ['artificial', 'intelligence']
```

## Troubleshooting

### Issue: Training too slow

**Solutions**:
1. Reduce vocabulary size
2. Increase `min_frequency`
3. Use `limit_alphabet` to reduce initial alphabet
4. Train on subset first

```python
# Fast training configuration
trainer = BpeTrainer(
    vocab_size=20000,      # Smaller vocab
    min_frequency=5,       # Higher threshold
    limit_alphabet=500,    # Limit alphabet
    show_progress=True
)
```

### Issue: High unknown token rate

**Solutions**:
1. Increase vocabulary size
2. Decrease `min_frequency`
3. Check normalization (might be too aggressive)

```python
# Better coverage configuration
trainer = BpeTrainer(
    vocab_size=50000,      # Larger vocab
    min_frequency=1,       # Lower threshold
)
```

### Issue: Poor quality tokenization

**Solutions**:
1. Verify normalization matches your use case
2. Check pre-tokenization splits correctly
3. Ensure training data is representative
4. Try different algorithm (BPE vs WordPiece vs Unigram)

```python
# Debug tokenization pipeline
text = "Sample text to debug"

# Check normalization
normalized = tokenizer.normalizer.normalize_str(text)
print(f"Normalized: {normalized}")

# Check pre-tokenization
pre_tokens = tokenizer.pre_tokenizer.pre_tokenize_str(text)
print(f"Pre-tokens: {pre_tokens}")

# Check final tokenization
tokens = tokenizer.encode(text).tokens
print(f"Tokens: {tokens}")
```

## Best practices

1. **Use representative training data** - Match your target domain
2. **Start with standard configs** - BERT WordPiece or GPT-2 BPE
3. **Test on held-out data** - Measure unknown token rate
4. **Iterate on vocabulary size** - Test 30k, 50k, 100k
5. **Save tokenizer with model** - Ensure reproducibility
6. **Version your tokenizers** - Track changes for reproducibility
7. **Document special tokens** - Critical for model training




---

## 🚀 Usage

**Reference this template:** `@skill-huggingface-tokenizers.md`


**Platform-specific:**
- **GitHub Copilot**: Add to `.github/copilot-instructions.md`
- **Augment Code**: Use `aug context add` command
- **Cursor/Windsurf**: Reference in chat with `@filename`
- **Claude**: Add to Project Knowledge
- **ChatGPT**: Add to Custom GPT configuration
