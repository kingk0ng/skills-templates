---
id: skill-sentencepiece
type: skill
name: sentencepiece
description: Language-independent tokenizer treating text as raw Unicode. Supports
  BPE and Unigram algorithms. Fast (50k sentences/sec), lightweight (6MB memory),
  deterministic vocabulary. Used by T5, ALBERT, XLNet, mBART. Train on raw text without
  pre-tokenization. Use when you need multilingual support, CJK languages, or reproducible
  tokenization.
category: ai-research
complexity: medium
keywords:
- api
- deployment
- git
- github
- performance
- python
- test
capabilities: []
token_estimate: 735
has_references: true
reference_count: 2
---

<!-- Converted from Claude Skill Template -->
<!-- Complexity: Medium -->
<!-- Estimated Tokens: ~735 -->
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




# sentencepiece

> Language-independent tokenizer treating text as raw Unicode. Supports BPE and Unigram algorithms. Fast (50k sentences/sec), lightweight (6MB memory), deterministic vocabulary. Used by T5, ALBERT, XLNet, mBART. Train on raw text without pre-tokenization. Use when you need multilingual support, CJK languages, or reproducible tokenization.

# SentencePiece - Language-Independent Tokenization

Unsupervised tokenizer that works on raw text without language-specific preprocessing.

## When to use SentencePiece

**Use SentencePiece when:**
- Building multilingual models (no language-specific rules)
- Working with CJK languages (Chinese, Japanese, Korean)
- Need reproducible tokenization (deterministic vocabulary)
- Want to train on raw text (no pre-tokenization needed)
- Require lightweight deployment (6MB memory, 50k sentences/sec)

**Performance**:
- **Speed**: 50,000 sentences/sec
- **Memory**: ~6MB for loaded model
- **Languages**: All (language-independent)

**Use alternatives instead**:
- **HuggingFace Tokenizers**: Faster training, more flexibility
- **tiktoken**: OpenAI models (GPT-3.5/4)
- **BERT WordPiece**: English-centric tasks

## Quick start

### Installation

```bash
# Python
pip install sentencepiece

# C++ (requires CMake)
git clone https://github.com/google/sentencepiece.git
cd sentencepiece
mkdir build && cd build
cmake .. && make -j $(nproc)
sudo make install
```

### Train model

```bash
# Command-line (BPE with 8000 vocab)
spm_train --input=data.txt --model_prefix=m --vocab_size=8000 --model_type=bpe

# Python API
import sentencepiece as spm

spm.SentencePieceTrainer.train(
    input='data.txt',
    model_prefix='m',
    vocab_size=8000,
    model_type='bpe'
)
```

**Training time**: ~1-2 minutes for 100MB corpus

### Encode and decode

```python
import sentencepiece as spm

# Load model
sp = spm.SentencePieceProcessor(model_file='m.model')

# Encode to pieces
pieces = sp.encode('This is a test', out_type=str)
print(pieces)  # ['▁This', '▁is', '▁a', '▁test']

# Encode to IDs
ids = sp.encode('This is a test', out_type=int)
print(ids)  # [284, 47, 11, 1243]

# Decode
text = sp.decode(ids)
print(text)  # "This is a test"
```

## Language-independent design

### Whitespace as symbol (▁)

```python
text = "Hello world"
pieces = sp.encode(text, out_type=str)
print(pieces)  # ['▁Hello', '▁world']

# Decode preserves spaces
decoded = sp.decode_pieces(pieces)
print(decoded)  # "Hello world"
```

**Key principle**: Treat text as raw Unicode, whitespace = ▁ (meta symbol)

## Tokenization algorithms

### BPE (Byte-Pair Encoding)

```python
spm.SentencePieceTrainer.train(
    input='data.txt',
    model_prefix='bpe_model',
    vocab_size=16000,
    model_type='bpe'
)
```

**Used by**: mBART

### Unigram (default)

```python
spm.SentencePieceTrainer.train(
    input='data.txt',
    model_prefix='unigram_model',
    vocab_size=8000,
    model_type='unigram'
)
```

**Used by**: T5, ALBERT, XLNet

## Training configuration

### Essential parameters

```python
spm.SentencePieceTrainer.train(
    input='corpus.txt',
    model_prefix='m',
    vocab_size=32000,
    model_type='unigram',
    character_coverage=0.9995,  # 1.0 for CJK
    user_defined_symbols=['[SEP]', '[CLS]'],
    unk_piece='<unk>',
    num_threads=16
)
```

### Character coverage

| Language Type | Coverage | Rationale |
|---------------|----------|-----------|
| English       | 0.9995   | Most common chars |
| CJK (Chinese) | 1.0      | All characters needed |
| Multilingual  | 0.9995   | Balance |

## Encoding options

### Subword regularization

```python
# Sample different tokenizations
for _ in range(3):
    pieces = sp.encode('tokenization', out_type=str, enable_sampling=True, alpha=0.1)
    print(pieces)

# Output (different each time):
# ['▁token', 'ization']
# ['▁tok', 'en', 'ization']
```

**Use case**: Data augmentation for robustness.

## Common patterns

### T5-style training

```python
spm.SentencePieceTrainer.train(
    input='c4_corpus.txt',
    model_prefix='t5',
    vocab_size=32000,
    model_type='unigram',
    user_defined_symbols=[f'<extra_id_{i}>' for i in range(100)],
    unk_id=2,
    eos_id=1,
    pad_id=0
)
```

### Integration with transformers

```python
from transformers import T5Tokenizer

# T5 uses SentencePiece internally
tokenizer = T5Tokenizer.from_pretrained('t5-base')
inputs = tokenizer('translate English to French: Hello', return_tensors='pt')
```

## Performance benchmarks

### Training speed

| Corpus | BPE (16k) | Unigram (8k) |
|--------|-----------|--------------|
| 100 MB | 1-2 min   | 3-4 min      |
| 1 GB   | 10-15 min | 30-40 min    |

### Tokenization speed

- **SentencePiece**: 50,000 sentences/sec
- **HF Tokenizers**: 200,000 sentences/sec (4× faster)

## Supported models

**T5 family**: `t5-base`, `t5-large` (32k vocab, Unigram)
**ALBERT**: `albert-base-v2` (30k vocab, Unigram)
**XLNet**: `xlnet-base-cased` (32k vocab, Unigram)
**mBART**: `facebook/mbart-large-50` (250k vocab, BPE)

## References

- **[Training Guide](references/training.md)** - Detailed options, corpus preparation
- **[Algorithms](references/algorithms.md)** - BPE vs Unigram, subword regularization

## Resources

- **GitHub**: https://github.com/google/sentencepiece ⭐ 10,000+
- **Paper**: https://arxiv.org/abs/1808.06226 (EMNLP 2018)
- **Version**: 0.2.0+




---


## 📚 Reference Materials


### Algorithms

# Tokenization Algorithms

BPE vs Unigram comparison and subword regularization.

## BPE (Byte-Pair Encoding)

### Algorithm

1. Initialize vocabulary with characters
2. Count frequency of adjacent token pairs
3. Merge most frequent pair
4. Repeat until vocabulary size reached

### Example

**Corpus**:
```
low: 5
lower: 2
newest: 6
widest: 3
```

**Iteration 1**:
- Most frequent pair: 'e' + 's' (9 times)
- Merge → 'es'
- Vocabulary: [chars] + ['es']

**Iteration 2**:
- Most frequent: 'es' + 't' (9 times)
- Merge → 'est'
- Vocabulary: [chars] + ['es', 'est']

**Result**: `newest` → `new|est`, `widest` → `wid|est`

### Implementation

```python
import sentencepiece as spm

spm.SentencePieceTrainer.train(
    input='corpus.txt',
    model_type='bpe',
    vocab_size=16000
)
```

### Advantages

- Simple algorithm
- Fast training
- Good compression ratio

### Disadvantages

- Deterministic (no sampling)
- May split common words unexpectedly

## Unigram

### Algorithm

1. Start with large vocabulary (all substrings)
2. Compute probability of each token
3. Remove tokens with minimal loss impact
4. Repeat until vocabulary size reached

### Probabilistic tokenization

Given vocabulary with probabilities:
```
P('low') = 0.02
P('est') = 0.03
P('l') = 0.01
P('o') = 0.015
...
```

Tokenize "lowest":
```
Option 1: ['low', 'est']
P = 0.02 × 0.03 = 0.0006  ← highest

Option 2: ['l', 'o', 'w', 'est']
P = 0.01 × 0.015 × 0.01 × 0.03 = 0.000000045

Choose option 1 (highest probability)
```

### Implementation

```python
spm.SentencePieceTrainer.train(
    input='corpus.txt',
    model_type='unigram',
    vocab_size=8000
)
```

### Advantages

- Probabilistic (can sample)
- Better for morphologically rich languages
- Supports subword regularization

### Disadvantages

- Slower training
- More complex algorithm

## Comparison

| Feature | BPE | Unigram |
|---------|-----|---------|
| Training speed | Fast | Slow |
| Tokenization | Deterministic | Probabilistic |
| Sampling | No | Yes |
| Typical vocab size | 16k-32k | 8k-32k |
| Used by | mBART | T5, ALBERT, XLNet |

## Subword regularization

Sample different tokenizations during training for robustness.

### Enable sampling

```python
sp = spm.SentencePieceProcessor(model_file='m.model')

# Sample different tokenizations
for _ in range(5):
    pieces = sp.encode('tokenization', out_type=str, enable_sampling=True, alpha=0.1)
    print(pieces)

# Output (different each time):
# ['▁token', 'ization']
# ['▁tok', 'en', 'ization']
# ['▁token', 'iz', 'ation']
# ['▁to', 'ken', 'ization']
# ['▁token', 'ization']
```

### Parameters

- `alpha`: Regularization strength
  - 0.0 = deterministic (no sampling)
  - 0.1 = slight variation
  - 0.5 = high variation
  - 1.0 = maximum variation

### Benefits

1. **Robustness**: Model learns multiple tokenizations
2. **Data augmentation**: More diverse training data
3. **Better generalization**: Less overfitting to specific tokenization

### Use case

```python
# Training loop with regularization
for batch in dataloader:
    # Sample different tokenizations each epoch
    tokens = sp.encode(batch['text'], enable_sampling=True, alpha=0.1)
    # Train model...
```

**Used by**: mT5, XLM-RoBERTa

## NBest encoding

Get multiple tokenization candidates with scores.

```python
sp = spm.SentencePieceProcessor(model_file='m.model')

# Get top-5 tokenizations
nbest = sp.nbest_encode('tokenization', nbest_size=5, out_type=str)

for pieces, score in nbest:
    print(f"{pieces} (log prob: {score:.4f})")

# Output:
# ['▁token', 'ization'] (log prob: -2.34)
# ['▁tok', 'en', 'ization'] (log prob: -2.41)
# ['▁token', 'iz', 'ation'] (log prob: -2.57)
```

### Use cases

1. **Ensemble tokenization**: Average over multiple tokenizations
2. **Uncertainty estimation**: Check variance in scores
3. **Debugging**: Understand tokenizer behavior

## Best practices

1. **Use Unigram for multilingual** - Better for diverse languages
2. **Use BPE for speed** - Faster training and inference
3. **Enable subword regularization** - Improves model robustness
4. **Set alpha=0.1 for slight variation** - Good balance
5. **Use deterministic mode for inference** - Consistent results




### Training

# SentencePiece Training Guide

Complete guide to training SentencePiece models.

## Training workflow

### Step 1: Prepare corpus

```bash
# Plain text file, one sentence per line (recommended)
cat corpus.txt
# Hello world
# This is a test
# SentencePiece is language-independent

# Or use raw text (SentencePiece handles sentence splitting)
```

### Step 2: Train model

**Command-line**:
```bash
spm_train \
  --input=corpus.txt \
  --model_prefix=m \
  --vocab_size=8000 \
  --model_type=unigram \
  --character_coverage=0.9995
```

**Python API**:
```python
import sentencepiece as spm

spm.SentencePieceTrainer.train(
    input='corpus.txt',
    model_prefix='m',
    vocab_size=8000,
    model_type='unigram'
)
```

**Output**: `m.model` (binary), `m.vocab` (text vocabulary)

### Step 3: Load and use

```python
sp = spm.SentencePieceProcessor(model_file='m.model')
pieces = sp.encode('Test sentence', out_type=str)
```

## Training parameters

### Core parameters

```python
spm.SentencePieceTrainer.train(
    # Required
    input='corpus.txt',           # Input corpus
    model_prefix='output',        # Output prefix
    vocab_size=8000,              # Target vocabulary size

    # Algorithm
    model_type='unigram',         # 'unigram', 'bpe', 'char', 'word'

    # Coverage
    character_coverage=0.9995,    # 0.9995 for most, 1.0 for CJK

    # Normalization
    normalization_rule_name='nmt_nfkc',  # 'nmt_nfkc', 'nfkc', 'identity'

    # Performance
    num_threads=16,               # Training threads
    input_sentence_size=10000000  # Max sentences to load
)
```

### Special tokens

```python
spm.SentencePieceTrainer.train(
    input='corpus.txt',
    model_prefix='m',
    vocab_size=32000,

    # Control symbols (special tokens for model control)
    control_symbols=['<s>', '</s>', '<pad>'],

    # User-defined symbols (never split)
    user_defined_symbols=['[MASK]', '[SEP]', '[CLS]'],

    # Special token pieces
    unk_piece='<unk>',
    bos_piece='<s>',
    eos_piece='</s>',
    pad_piece='<pad>',

    # Special token IDs
    unk_id=0,
    bos_id=1,
    eos_id=2,
    pad_id=3
)
```

### Advanced options

```python
spm.SentencePieceTrainer.train(
    input='corpus.txt',
    model_prefix='m',
    vocab_size=32000,

    # Byte fallback (handle unknown chars)
    byte_fallback=True,

    # Digit handling
    split_digits=True,            # Split digits individually

    # Script splitting
    split_by_unicode_script=True, # Split by Unicode script
    split_by_whitespace=True,     # Split by whitespace

    # Length constraints
    max_sentencepiece_length=16,  # Max token length

    # Rare word handling
    min_frequency=2,              # Min frequency for token

    # Training size
    input_sentence_size=10000000, # Max sentences
    shuffle_input_sentence=True,  # Shuffle training data

    # Seed
    seed_sentencepiece_size=1000000  # Seed vocab size
)
```

## Training from Python iterator

```python
import sentencepiece as spm
from datasets import load_dataset

# Load dataset
dataset = load_dataset('wikitext', 'wikitext-103-raw-v1', split='train')

# Create iterator
def corpus_iterator():
    for example in dataset:
        if example['text'].strip():
            yield example['text']

# Train from iterator
spm.SentencePieceTrainer.train(
    sentence_iterator=corpus_iterator(),
    model_prefix='wiki',
    vocab_size=32000,
    model_type='unigram'
)
```

## Model types

### BPE

```python
spm.SentencePieceTrainer.train(
    input='corpus.txt',
    model_type='bpe',
    vocab_size=16000
)
```

**Training time**: ~10-15 min for 1GB corpus

### Unigram (recommended)

```python
spm.SentencePieceTrainer.train(
    input='corpus.txt',
    model_type='unigram',
    vocab_size=8000
)
```

**Training time**: ~30-40 min for 1GB corpus

## Character coverage

### English/European (0.9995)

```python
spm.SentencePieceTrainer.train(
    input='en_corpus.txt',
    character_coverage=0.9995  # Cover 99.95% of chars
)
```

Covers: a-z, A-Z, punctuation, common accents

### CJK (1.0)

```python
spm.SentencePieceTrainer.train(
    input='zh_corpus.txt',
    character_coverage=1.0  # Cover ALL characters
)
```

Required for: Chinese, Japanese, Korean

### Multilingual (0.9995-1.0)

```python
spm.SentencePieceTrainer.train(
    input='multilingual_corpus.txt',
    character_coverage=0.9995  # Balance coverage/size
)
```

## Vocabulary size selection

| Task | Vocab Size | Rationale |
|------|------------|-----------|
| English monolingual | 16k-32k | Standard |
| Multilingual | 32k-250k | More languages |
| CJK | 32k-100k | More characters |
| Code | 16k-32k | Similar to English |

## Normalization rules

### nmt_nfkc (recommended)

```python
normalization_rule_name='nmt_nfkc'
```

- NFKC Unicode normalization
- Whitespace handling
- **Recommended for most tasks**

### identity (no normalization)

```python
normalization_rule_name='identity'
```

- Preserves input exactly
- Use for code, case-sensitive tasks

### nfkc (standard Unicode)

```python
normalization_rule_name='nfkc'
```

- Standard Unicode normalization
- Less aggressive than nmt_nfkc

## Performance optimization

### Multi-threading

```python
spm.SentencePieceTrainer.train(
    input='large_corpus.txt',
    num_threads=32  # Use all cores
)
```

**Speedup**: ~4-8× with 16+ cores

### Sampling input

```python
spm.SentencePieceTrainer.train(
    input='huge_corpus.txt',
    input_sentence_size=10000000,  # Sample 10M sentences
    shuffle_input_sentence=True
)
```

**For very large corpora** (>10GB)

### Extremely large corpus

```python
spm.SentencePieceTrainer.train(
    input='massive_corpus.txt',
    train_extremely_large_corpus=True,  # Enable for >10GB
    input_sentence_size=100000000
)
```

## Best practices

1. **Use Unigram for most tasks** - Better for multilingual
2. **Set character_coverage=1.0 for CJK** - Required for full coverage
3. **Use nmt_nfkc normalization** - Works well for most cases
4. **Add user_defined_symbols for special tokens** - BERT-style tokens
5. **Enable byte_fallback for robustness** - Handles emojis/rare chars
6. **Start with vocab_size=32000** - Good default for most tasks
7. **Use multi-threading** - Speeds up training significantly




---

## 🚀 Usage

**Reference this template:** `@skill-sentencepiece.md`


**Platform-specific:**
- **GitHub Copilot**: Add to `.github/copilot-instructions.md`
- **Augment Code**: Use `aug context add` command
- **Cursor/Windsurf**: Reference in chat with `@filename`
- **Claude**: Add to Project Knowledge
- **ChatGPT**: Add to Custom GPT configuration
