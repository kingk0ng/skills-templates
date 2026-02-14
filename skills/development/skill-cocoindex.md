---
id: skill-cocoindex
type: skill
name: cocoindex
description: Comprehensive toolkit for developing with the CocoIndex library. Use
  when users need to create data transformation pipelines (flows), write custom functions,
  or operate flows via CLI or API. Covers building ETL workflows for AI data processing,
  including embedding documents into vector databases, building knowledge graphs,
  creating search indexes, or processing data streams with incremental updates.
category: development
complexity: medium
keywords:
- api
- database
- github
- go
- javascript
- postgres
- python
- test
- testing
capabilities: []
token_estimate: 3520
has_references: true
reference_count: 4
---

<!-- Converted from Claude Skill Template -->
<!-- Complexity: Medium -->
<!-- Estimated Tokens: ~3,520 -->
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




# cocoindex

> Comprehensive toolkit for developing with the CocoIndex library. Use when users need to create data transformation pipelines (flows), write custom functions, or operate flows via CLI or API. Covers building ETL workflows for AI data processing, including embedding documents into vector databases, building knowledge graphs, creating search indexes, or processing data streams with incremental updates.

# CocoIndex

## Overview

CocoIndex is an ultra-performant real-time data transformation framework for AI with incremental processing. This skill enables building **indexing flows** that extract data from sources, apply transformations (chunking, embedding, LLM extraction), and export to targets (vector databases, graph databases, relational databases).

**Core capabilities:**

1. **Write indexing flows** - Define ETL pipelines using Python
2. **Create custom functions** - Build reusable transformation logic
3. **Operate flows** - Run and manage flows using CLI or Python API

**Key features:**

- Incremental processing (only processes changed data)
- Live updates (continuously sync source changes to targets)
- Built-in functions (text chunking, embeddings, LLM extraction)
- Multiple data sources (local files, S3, Azure Blob, Google Drive, Postgres)
- Multiple targets (Postgres+pgvector, Qdrant, LanceDB, Neo4j, Kuzu)

**For detailed documentation:** <https://cocoindex.io/docs/>
**Search documentation:** <https://cocoindex.io/docs/search?q=url%20encoded%20keyword>

## When to Use This Skill

Use when users request:

- "Build a vector search index for my documents"
- "Create an embedding pipeline for code/PDFs/images"
- "Extract structured information using LLMs"
- "Build a knowledge graph from documents"
- "Set up live document indexing"
- "Create custom transformation functions"
- "Run/update my CocoIndex flow"

## Flow Writing Workflow

### Step 1: Understand Requirements

Ask clarifying questions to understand:

**Data source:**

- Where is the data? (local files, S3, database, etc.)
- What file types? (text, PDF, JSON, images, code, etc.)
- How often does it change? (one-time, periodic, continuous)

**Transformations:**

- What processing is needed? (chunking, embedding, extraction, etc.)
- Which embedding model? (SentenceTransformer, OpenAI, custom)
- Any custom logic? (filtering, parsing, enrichment)

**Target:**

- Where should results go? (Postgres, Qdrant, Neo4j, etc.)
- What schema? (fields, primary keys, indexes)
- Vector search needed? (specify similarity metric)

### Step 2: Set Up Dependencies

Guide user to add CocoIndex with appropriate extras to their project based on their needs:

**Required dependency:**

- `cocoindex` - Core functionality, CLI, and most built-in functions

**Optional extras (add as needed):**

- `cocoindex[embeddings]` - For SentenceTransformer embeddings (when using `SentenceTransformerEmbed`)
- `cocoindex[colpali]` - For ColPali image/document embeddings (when using `ColPaliEmbedImage` or `ColPaliEmbedQuery`)
- `cocoindex[lancedb]` - For LanceDB target (when exporting to LanceDB)
- `cocoindex[embeddings,lancedb]` - Multiple extras can be combined

**What's included:**

- Base package: Core functionality, CLI, most built-in functions, Postgres/Qdrant/Neo4j/Kuzu targets
- `embeddings` extra: SentenceTransformers library for local embedding models
- `colpali` extra: ColPali engine for multimodal document/image embeddings
- `lancedb` extra: LanceDB client library for LanceDB vector database support

Users can install using their preferred package manager (pip, uv, poetry, etc.) or add to `pyproject.toml`.

**For installation details:** <https://cocoindex.io/docs/getting_started/installation>

### Step 3: Set Up Environment

**Check existing environment first:**

1. Check if `COCOINDEX_DATABASE_URL` exists in environment variables
   - If not found, use default: `postgres://cocoindex:cocoindex@localhost/cocoindex`

2. **For flows requiring LLM APIs** (embeddings, extraction):
   - Ask user which LLM provider they want to use:
     - **OpenAI** - Both generation and embeddings
     - **Anthropic** - Generation only
     - **Gemini** - Both generation and embeddings
     - **Voyage** - Embeddings only
     - **Ollama** - Local models (generation and embeddings)
   - Check if the corresponding API key exists in environment variables
   - If not found, **ask user to provide the API key value**
   - **Never create simplified examples without LLM** - always get the proper API key and use the real LLM functions

**Guide user to create `.env` file:**

```bash
# Database connection (required - internal storage)
COCOINDEX_DATABASE_URL=postgres://cocoindex:cocoindex@localhost/cocoindex

# LLM API keys (add the ones you need)
OPENAI_API_KEY=sk-...          # For OpenAI (generation + embeddings)
ANTHROPIC_API_KEY=sk-ant-...   # For Anthropic (generation only)
GOOGLE_API_KEY=...             # For Gemini (generation + embeddings)
VOYAGE_API_KEY=pa-...          # For Voyage (embeddings only)
# Ollama requires no API key (local)
```

**For more LLM options:** <https://cocoindex.io/docs/ai/llm>

Create basic project structure:

```python
# main.py
from dotenv import load_dotenv
import cocoindex

@cocoindex.flow_def(name="FlowName")
def my_flow(flow_builder: cocoindex.FlowBuilder, data_scope: cocoindex.DataScope):
    # Flow definition here
    pass

if __name__ == "__main__":
    load_dotenv()
    cocoindex.init()
    my_flow.update()
```

### Step 4: Write the Flow

Follow this structure:

```python
@cocoindex.flow_def(name="DescriptiveName")
def flow_name(flow_builder: cocoindex.FlowBuilder, data_scope: cocoindex.DataScope):
    # 1. Import source data
    data_scope["source_name"] = flow_builder.add_source(
        cocoindex.sources.SourceType(...)
    )

    # 2. Create collector(s) for outputs
    collector = data_scope.add_collector()

    # 3. Transform data (iterate through rows)
    with data_scope["source_name"].row() as item:
        # Apply transformations
        item["new_field"] = item["existing_field"].transform(
            cocoindex.functions.FunctionName(...)
        )

        ...

        # Nested iteration (e.g., chunks within documents)
        with item["nested_table"].row() as nested_item:
            # More transformations
            nested_item["embedding"] = nested_item["text"].transform(...)

            # Collect data for export
            collector.collect(
                field1=nested_item["field1"],
                field2=item["field2"],
                generated_id=cocoindex.GeneratedField.UUID
            )

    # 4. Export to target
    collector.export(
        "target_name",
        cocoindex.targets.TargetType(...),
        primary_key_fields=["field1"],
        vector_indexes=[...]  # If needed
    )
```

**Key principles:**

- Each source creates a field in the top-level data scope
- Use `.row()` to iterate through table data
- **CRITICAL: Always assign transformed data to row fields** - Use `item["new_field"] = item["existing_field"].transform(...)`, NOT local variables like `new_field = item["existing_field"].transform(...)`
- Transformations create new fields without mutating existing data
- Collectors gather data from any scope level
- Export must happen at top level (not within row iterations)

**Common mistakes to avoid:**

❌ **Wrong:** Using local variables for transformations

```python
with data_scope["files"].row() as file:
    summary = file["content"].transform(...)  # ❌ Local variable
    summaries_collector.collect(filename=file["filename"], summary=summary)
```

✅ **Correct:** Assigning to row fields

```python
with data_scope["files"].row() as file:
    file["summary"] = file["content"].transform(...)  # ✅ Field assignment
    summaries_collector.collect(filename=file["filename"], summary=file["summary"])
```

❌ **Wrong:** Creating unnecessary dataclasses to mirror flow fields

```python
from dataclasses import dataclass

@dataclass
class FileSummary:  # ❌ Unnecessary - CocoIndex manages fields automatically
    filename: str
    summary: str
    embedding: list[float]

# This dataclass is never used in the flow!
```

### Step 5: Design the Flow Solution

**IMPORTANT:** The patterns listed below are common starting points, but **you cannot exhaustively enumerate all possible scenarios**. When user requirements don't match existing patterns:

1. **Combine elements from multiple patterns** - Mix and match sources, transformations, and targets creatively
2. **Review additional examples** - See <https://github.com/cocoindex-io/cocoindex?tab=readme-ov-file#-examples-and-demo> for diverse real-world use cases (face recognition, multimodal search, product recommendations, patient form extraction, etc.)
3. **Think from first principles** - Use the core APIs (sources, transforms, collectors, exports) and apply common sense to solve novel problems
4. **Be creative** - CocoIndex is flexible; unique combinations of components can solve unique problems

**Common starting patterns (use references for detailed examples):**

**For text embedding:** Load `references/flow_patterns.md` and refer to "Pattern 1: Simple Text Embedding"

**For code embedding:** Load `references/flow_patterns.md` and refer to "Pattern 2: Code Embedding with Language Detection"

**For LLM extraction + knowledge graph:** Load `references/flow_patterns.md` and refer to "Pattern 3: LLM-based Extraction to Knowledge Graph"

**For live updates:** Load `references/flow_patterns.md` and refer to "Pattern 4: Live Updates with Refresh Interval"

**For custom functions:** Load `references/flow_patterns.md` and refer to "Pattern 5: Custom Transform Function"

**For reusable query logic:** Load `references/flow_patterns.md` and refer to "Pattern 6: Transform Flow for Reusable Logic"

**For concurrency control:** Load `references/flow_patterns.md` and refer to "Pattern 7: Concurrency Control"

**Example of pattern composition:**

If a user asks to "index images from S3, generate captions with a vision API, and store in Qdrant", combine:

- AmazonS3 source (from S3 examples)
- Custom function for vision API calls (from custom functions pattern)
- EmbedText to embed the captions (from embedding patterns)
- Qdrant target (from target examples)

No single pattern covers this exact scenario, but the building blocks are composable.

### Step 6: Test and Run

Guide user through testing:

```bash
# 1. Run with setup
cocoindex update --setup -f main   # -f force setup without confirmation prompts


# 2. Start a server and redirect users to CocoInsight
cocoindex server -ci main
# Then open CocoInsight at https://cocoindex.io/cocoinsight

```

## Data Types

CocoIndex has a type system independent of programming languages. All data types are determined at flow definition time, making schemas clear and predictable.

**IMPORTANT: When to define types:**

- **Custom functions**: Type annotations are **required** for return values (these are the source of truth for type inference)
- **Flow fields**: Type annotations are **NOT needed** - CocoIndex automatically infers types from sources, functions, and transformations
- **Dataclasses/Pydantic models**: Only create them when they're **actually used** (as function parameters/returns or ExtractByLlm output_type), NOT to mirror flow field schemas

**Type annotation requirements:**

- **Return values of custom functions**: Must use **specific type annotations** - these are the source of truth for type inference
- **Arguments of custom functions**: Relaxed - can use `Any`, `dict[str, Any]`, or omit annotations; engine already knows the types
- **Flow definitions**: No explicit type annotations needed - CocoIndex automatically infers types from sources and functions

**Why specific return types matter:** Custom function return types let CocoIndex infer field types throughout the flow without processing real data. This enables creating proper target schemas (e.g., vector indexes with fixed dimensions).

**Common type categories:**

1. **Primitive types**: `str`, `int`, `float`, `bool`, `bytes`, `datetime.date`, `datetime.datetime`, `uuid.UUID`

2. **Vector types** (embeddings): Specify dimension in return type if you plan to export as vectors to targets, as most targets require a fixed vector dimension
   - `cocoindex.Vector[cocoindex.Float32, typing.Literal[768]]` - 768-dim float32 vector (recommended)
   - `list[float]` without dimension also works

3. **Struct types**: Dataclass, NamedTuple, or Pydantic model
   - Return type: Must use specific class (e.g., `Person`)
   - Argument: Can use `dict[str, Any]` or `Any`

4. **Table types**:
   - **KTable** (keyed): `dict[K, V]` where K = key type (primitive or frozen struct), V = Struct type
   - **LTable** (ordered): `list[R]` where R = Struct type
   - Arguments: Can use `dict[Any, Any]` or `list[Any]`

5. **Json type**: `cocoindex.Json` for unstructured/dynamic data

6. **Optional types**: `T | None` for nullable values

**Examples:**

```python
from dataclasses import dataclass
from typing import Literal
import cocoindex

@dataclass
class Person:
    name: str
    age: int

# ✅ Vector with dimension (recommended for vector search)
@cocoindex.op.function(behavior_version=1)
def embed_text(text: str) -> cocoindex.Vector[cocoindex.Float32, Literal[768]]:
    """Generate 768-dim embedding - dimension needed for vector index."""
    # ... embedding logic ...
    return embedding  # numpy array or list of 768 floats

# ✅ Struct return type, relaxed argument
@cocoindex.op.function(behavior_version=1)
def process_person(person: dict[str, Any]) -> Person:
    """Argument can be dict[str, Any], return must be specific Struct."""
    return Person(name=person["name"], age=person["age"])

# ✅ LTable return type
@cocoindex.op.function(behavior_version=1)
def filter_people(people: list[Any]) -> list[Person]:
    """Return type specifies list of specific Struct."""
    return [p for p in people if p.age >= 18]

# ❌ Wrong: dict[str, str] is not a valid specific CocoIndex type
# @cocoindex.op.function(...)
# def bad_example(person: Person) -> dict[str, str]:
#     return {"name": person.name}
```

**For comprehensive data types documentation:** <https://cocoindex.io/docs/core/data_types>

## Custom Functions

When users need custom transformation logic, create custom functions.

### Decision: Standalone vs Spec+Executor

**Use standalone function when:**

- Simple transformation
- No configuration needed
- No setup/initialization required

**Use spec+executor when:**

- Needs configuration (model names, API endpoints, parameters)
- Requires setup (loading models, establishing connections)
- Complex multi-step processing

### Creating Standalone Functions

```python
@cocoindex.op.function(behavior_version=1)
def my_function(input_arg: str, optional_arg: int | None = None) -> dict:
    """
    Function description.

    Args:
        input_arg: Description
        optional_arg: Optional description
    """
    # Transformation logic
    return {"result": f"processed-{input_arg}"}
```

**Requirements:**

- Decorator: `@cocoindex.op.function()`
- Type annotations on all arguments and return value
- Optional parameters: `cache=True` for expensive ops, `behavior_version` (required with cache)

### Creating Spec+Executor Functions

```python
# 1. Define configuration spec
class MyFunction(cocoindex.op.FunctionSpec):
    """Configuration for MyFunction."""
    model_name: str
    threshold: float = 0.5

# 2. Define executor
@cocoindex.op.executor_class(cache=True, behavior_version=1)
class MyFunctionExecutor:
    spec: MyFunction  # Required: link to spec
    model = None      # Instance variables for state

    def prepare(self) -> None:
        """Optional: run once before execution."""
        # Load model, setup connections, etc.
        self.model = load_model(self.spec.model_name)

    def __call__(self, text: str) -> dict:
        """Required: execute for each data row."""
        # Use self.spec for configuration
        # Use self.model for loaded resources
        result = self.model.process(text)
        return {"result": result}
```

**When to enable cache:**

- LLM API calls
- Model inference
- External API calls
- Computationally expensive operations

**Important:** Increment `behavior_version` when function logic changes to invalidate cache.

For detailed examples and patterns, load `references/custom_functions.md`.

**For more on custom functions:** <https://cocoindex.io/docs/custom_ops/custom_functions>

## Operating Flows

### CLI Operations

**Setup flow (create resources):**

```bash
cocoindex setup main
```

**One-time update:**

```bash
cocoindex update main

# With auto-setup
cocoindex update --setup main

# Force reset everything before setup and update
cocoindex update --reset main
```

**Live update (continuous monitoring):**

```bash
cocoindex update main.py -L

# Requires refresh_interval on source or source-specific change capture
```

**Drop flow (remove all resources):**

```bash
cocoindex drop main.py
```

**Inspect flow:**

```bash
cocoindex show main.py:FlowName
```

**Test without side effects:**

```bash
cocoindex evaluate main.py:FlowName --output-dir ./test_output
```

For complete CLI reference, load `references/cli_operations.md`.

**For CLI documentation:** <https://cocoindex.io/docs/core/cli>

### API Operations

**Basic setup:**

```python
from dotenv import load_dotenv
import cocoindex

load_dotenv()
cocoindex.init()

@cocoindex.flow_def(name="MyFlow")
def my_flow(flow_builder, data_scope):
    # ... flow definition ...
    pass
```

**One-time update:**

```python
stats = my_flow.update()
print(f"Processed {stats.total_rows} rows")

# Async
stats = await my_flow.update_async()
```

**Live update:**

```python
# As context manager
with cocoindex.FlowLiveUpdater(my_flow) as updater:
    # Updater runs in background
    # Your application logic here
    pass

# Manual control
updater = cocoindex.FlowLiveUpdater(
    my_flow,
    cocoindex.FlowLiveUpdaterOptions(
        live_mode=True,
        print_stats=True
    )
)
updater.start()
# ... application logic ...
updater.wait()
```

**Setup/drop:**

```python
my_flow.setup(report_to_stdout=True)
my_flow.drop(report_to_stdout=True)
cocoindex.setup_all_flows()
cocoindex.drop_all_flows()
```

**Query with transform flows:**

```python
@cocoindex.transform_flow()
def text_to_embedding(text: cocoindex.DataSlice[str]) -> cocoindex.DataSlice[list[float]]:
    return text.transform(
        cocoindex.functions.SentenceTransformerEmbed(model="...")
    )

# Use in flow for indexing
doc["embedding"] = text_to_embedding(doc["content"])

# Use for querying
query_embedding = text_to_embedding.eval("search query")
```

For complete API reference and patterns, load `references/api_operations.md`.

**For API documentation:** <https://cocoindex.io/docs/core/flow_methods>

## Built-in Functions

### Text Processing

**SplitRecursively** - Chunk text intelligently

```python
doc["chunks"] = doc["content"].transform(
    cocoindex.functions.SplitRecursively(),
    language="markdown",  # or "python", "javascript", etc.
    chunk_size=2000,
    chunk_overlap=500
)
```

**ParseJson** - Parse JSON strings

```python
data = json_string.transform(cocoindex.functions.ParseJson())
```

**DetectProgrammingLanguage** - Detect language from filename

```python
file["language"] = file["filename"].transform(
    cocoindex.functions.DetectProgrammingLanguage()
)
```

### Embeddings

**SentenceTransformerEmbed** - Local embedding model

```python
# Requires: cocoindex[embeddings]
chunk["embedding"] = chunk["text"].transform(
    cocoindex.functions.SentenceTransformerEmbed(
        model="sentence-transformers/all-MiniLM-L6-v2"
    )
)
```

**EmbedText** - LLM API embeddings

This is the **recommended way** to generate embeddings using LLM APIs (OpenAI, Voyage, etc.).

```python
chunk["embedding"] = chunk["text"].transform(
    cocoindex.functions.EmbedText(
        api_type=cocoindex.LlmApiType.OPENAI,
        model="text-embedding-3-small",
    )
)
```

**ColPaliEmbedImage** - Multimodal image embeddings

```python
# Requires: cocoindex[colpali]
image["embedding"] = image["img_bytes"].transform(
    cocoindex.functions.ColPaliEmbedImage(model="vidore/colpali-v1.2")
)
```

### LLM Extraction

**ExtractByLlm** - Extract structured data with LLM

This is the **recommended way** to use LLMs for extraction and summarization tasks. It supports both structured outputs (dataclasses, Pydantic models) and simple text outputs (str).

```python
import dataclasses

# For structured extraction
@dataclasses.dataclass
class ProductInfo:
    name: str
    price: float
    category: str

item["product_info"] = item["text"].transform(
    cocoindex.functions.ExtractByLlm(
        llm_spec=cocoindex.LlmSpec(
            api_type=cocoindex.LlmApiType.OPENAI,
            model="gpt-4o-mini"
        ),
        output_type=ProductInfo,
        instruction="Extract product information"
    )
)

# For text summarization/generation
file["summary"] = file["content"].transform(
    cocoindex.functions.ExtractByLlm(
        llm_spec=cocoindex.LlmSpec(
            api_type=cocoindex.LlmApiType.OPENAI,
            model="gpt-4o-mini"
        ),
        output_type=str,
        instruction="Summarize this document in one paragraph"
    )
)
```

## Common Sources and Targets

**Browse all sources:** <https://cocoindex.io/docs/sources/>
**Browse all targets:** <https://cocoindex.io/docs/targets/>

### Sources

**LocalFile:**

```python
cocoindex.sources.LocalFile(
    path="documents",
    included_patterns=["*.md", "*.txt"],
    excluded_patterns=["**/.*", "node_modules"]
)
```

**AmazonS3:**

```python
cocoindex.sources.AmazonS3(
    bucket="my-bucket",
    prefix="documents/",
    aws_access_key_id=cocoindex.add_transient_auth_entry("..."),
    aws_secret_access_key=cocoindex.add_transient_auth_entry("...")
)
```

**Postgres:**

```python
cocoindex.sources.Postgres(
    connection=cocoindex.add_auth_entry("conn", cocoindex.sources.PostgresConnection(...)),
    query="SELECT id, content FROM documents"
)
```

### Targets

**Postgres (with vector support):**

```python
collector.export(
    "target_name",
    cocoindex.targets.Postgres(),
    primary_key_fields=["id"],
    vector_indexes=[
        cocoindex.VectorIndexDef(
            field_name="embedding",
            metric=cocoindex.VectorSimilarityMetric.COSINE_SIMILARITY
        )
    ]
)
```

**Qdrant:**

```python
collector.export(
    "target_name",
    cocoindex.targets.Qdrant(collection_name="my_collection"),
    primary_key_fields=["id"]
)
```

**LanceDB:**

```python
# Requires: cocoindex[lancedb]
collector.export(
    "target_name",
    cocoindex.targets.LanceDB(uri="lancedb_data", table_name="my_table"),
    primary_key_fields=["id"]
)
```

**Neo4j (nodes):**

```python
collector.export(
    "nodes",
    cocoindex.targets.Neo4j(
        connection=neo4j_conn,
        mapping=cocoindex.targets.Nodes(label="Entity")
    ),
    primary_key_fields=["id"]
)
```

**Neo4j (relationships):**

```python
collector.export(
    "relationships",
    cocoindex.targets.Neo4j(
        connection=neo4j_conn,
        mapping=cocoindex.targets.Relationships(
            rel_type="RELATES_TO",
            source=cocoindex.targets.NodeFromFields(
                label="Entity",
                fields=[cocoindex.targets.TargetFieldMapping(source="source_id", target="id")]
            ),
            target=cocoindex.targets.NodeFromFields(
                label="Entity",
                fields=[cocoindex.targets.TargetFieldMapping(source="target_id", target="id")]
            )
        )
    ),
    primary_key_fields=["id"]
)
```

## Common Issues and Solutions

### "Flow not found"

- Check APP_TARGET format: `cocoindex show main.py`
- Use `--app-dir` if not in project root
- Verify flow name matches decorator

### "Database connection failed"

- Check `.env` has `COCOINDEX_DATABASE_URL`
- Test connection: `psql $COCOINDEX_DATABASE_URL`
- Use `--env-file` to specify custom location

### "Schema mismatch"

- Re-run setup: `cocoindex setup main.py`
- Drop and recreate: `cocoindex drop main.py && cocoindex setup main.py`

### "Live update exits immediately"

- Add `refresh_interval` to source
- Or use source-specific change capture (Postgres notifications, S3 events)

### "Out of memory"

- Add concurrency limits on sources: `max_inflight_rows`, `max_inflight_bytes`
- Set global limits in `.env`: `COCOINDEX_SOURCE_MAX_INFLIGHT_ROWS`

## Reference Documentation

This skill includes comprehensive reference documentation for common patterns and operations:

- **references/flow_patterns.md** - Complete examples of common flow patterns (text embedding, code embedding, knowledge graphs, live updates, concurrency control, etc.)
- **references/custom_functions.md** - Detailed guide for creating custom functions with examples (standalone functions, spec+executor pattern, LLM calls, external APIs, caching)
- **references/cli_operations.md** - Complete CLI reference with all commands, options, and workflows
- **references/api_operations.md** - Python API reference with examples for programmatic flow control, live updates, queries, and application integration patterns

Load these references when users need:

- Detailed examples of specific patterns
- Complete API documentation
- Advanced usage scenarios
- Troubleshooting guidance

**For comprehensive documentation:** <https://cocoindex.io/docs/>
**Search specific topics:** <https://cocoindex.io/docs/search?q=url%20encoded%20keyword>


---


## 📚 Reference Materials


### Cli_Operations

# CLI Operations Reference

Complete guide for operating CocoIndex flows using the CLI.

## Overview

The CocoIndex CLI (`cocoindex` command) provides tools for managing and inspecting flows. Most commands require an `APP_TARGET` argument specifying where flow definitions are located.

## Environment Setup

### Environment Variables

Create a `.env` file in the project directory:

```bash
# Database connection (required)
COCOINDEX_DATABASE_URL=postgresql://user:password@localhost/cocoindex_db

# Optional: App namespace for organizing flows
COCOINDEX_APP_NAMESPACE=dev

# Optional: Global concurrency limits
COCOINDEX_SOURCE_MAX_INFLIGHT_ROWS=50
COCOINDEX_SOURCE_MAX_INFLIGHT_BYTES=524288000  # 500MB

# Optional: LLM API keys (if using LLM functions)
OPENAI_API_KEY=sk-...
ANTHROPIC_API_KEY=sk-ant-...
VOYAGE_API_KEY=pa-...
```

### Loading Environment Files

```bash
# Default: loads .env from current directory
cocoindex <command> ...

# Specify custom env file
cocoindex --env-file path/to/.env <command> ...

# Specify app directory
cocoindex --app-dir /path/to/project <command> ...
```

## APP_TARGET Format

The `APP_TARGET` tells the CLI where flow definitions are located:

### Python Module
```bash
# Load from module name
cocoindex update main

# Load from package module
cocoindex update my_package.flows
```

### Python File
```bash
# Load from file path
cocoindex update main.py

# Load from nested file
cocoindex update path/to/flows.py
```

### Specific Flow
```bash
# Target specific flow in module
cocoindex update main:MyFlowName

# Target specific flow in file
cocoindex update path/to/flows.py:MyFlowName
```

## Core Commands

### setup - Initialize Flow Resources

Create all persistent backends needed by flows (database tables, collections, etc.).

```bash
# Setup all flows
cocoindex setup main.py

# Setup specific flow
cocoindex setup main.py:MyFlow
```

**What it does:**
- Creates internal storage tables in Postgres
- Creates target resources (database tables, vector collections, graph structures)
- Updates schemas if flow definition changed
- No-op if already set up and no changes needed

**When to use:**
- First time running a flow
- After modifying flow structure (new fields, new targets)
- After dropping flows to recreate resources

### update - Build/Update Target Data

Run transformations and update target data based on current source data.

```bash
# One-time update
cocoindex update main.py

# One-time update with setup
cocoindex update --setup main.py

# One-time update specific flow
cocoindex update main.py:TextEmbedding

# Force reexport even if no changes
cocoindex update --reexport main.py
```

**What it does:**
- Reads source data
- Applies transformations
- Updates target databases
- Uses incremental processing (only processes changed data)

**Options:**
- `--setup` - Run setup first if needed
- `--reexport` - Reexport all data even if unchanged (useful after data loss)

### update -L - Live Update Mode

Continuously monitor source changes and update targets.

```bash
# Live update mode
cocoindex update main.py -L

# Live update with setup
cocoindex update --setup main.py -L

# Live update with reexport on initial update
cocoindex update --reexport main.py -L
```

**What it does:**
- Performs initial one-time update
- Continuously monitors source changes
- Automatically processes updates
- Runs until aborted (Ctrl-C)

**Requires:**
- At least one source with change capture enabled:
  - `refresh_interval` parameter on source
  - Source-specific change capture (Postgres notifications, S3 events, etc.)

**Example with refresh interval:**
```python
data_scope["documents"] = flow_builder.add_source(
    cocoindex.sources.LocalFile(path="documents"),
    refresh_interval=datetime.timedelta(minutes=1)  # Check every minute
)
```

### drop - Remove Flow Resources

Remove all persistent backends owned by flows.

```bash
# Drop all flows
cocoindex drop main.py

# Drop specific flow
cocoindex drop main.py:MyFlow
```

**What it does:**
- Drops internal storage tables
- Drops target resources (tables, collections, graphs)
- Cleans up all persistent data

**Warning:** This is destructive and cannot be undone!

### show - Inspect Flow Definition

Display flow structure and statistics.

```bash
# Show flow structure
cocoindex show main.py:MyFlow

# Show all flows
cocoindex show main.py
```

**What it shows:**
- Flow name and structure
- Sources configured
- Transformations defined
- Targets and their schemas
- Current statistics (if flow is set up)

### evaluate - Test Flow Without Updating

Run transformations and dump results to files without updating targets.

```bash
# Evaluate flow
cocoindex evaluate main.py:MyFlow

# Specify output directory
cocoindex evaluate main.py:MyFlow --output-dir ./eval_results

# Disable cache
cocoindex evaluate main.py:MyFlow --no-cache
```

**What it does:**
- Runs transformations
- Saves results to files (JSON, CSV, etc.)
- Does NOT update targets
- Uses existing cache by default

**When to use:**
- Testing flow logic before running full update
- Debugging transformation issues
- Inspecting intermediate data
- Validating output format

**Options:**
- `--output-dir PATH` - Directory for output files (default: `eval_{flow_name}_{timestamp}`)
- `--no-cache` - Disable reading from cache (still doesn't write to cache)

## Complete Workflow Examples

### First-Time Setup and Indexing

```bash
# 1. Setup flow resources
cocoindex setup main.py

# 2. Run initial indexing
cocoindex update main.py

# 3. Verify results
cocoindex show main.py
```

### Development Workflow

```bash
# 1. Test with evaluate (no side effects)
cocoindex evaluate main.py:MyFlow --output-dir ./test_output

# 2. If looks good, setup and update
cocoindex update --setup main.py:MyFlow

# 3. Check results
cocoindex show main.py:MyFlow
```

### Production Live Updates

```bash
# Run with live updates and auto-setup
cocoindex update --setup main.py -L
```

### Rebuild After Changes

```bash
# Drop old resources
cocoindex drop main.py

# Setup with new definition
cocoindex setup main.py

# Reindex everything
cocoindex update --reexport main.py
```

### Multiple Flows

```bash
# Setup all flows
cocoindex setup main.py

# Update specific flows
cocoindex update main.py:CodeEmbedding
cocoindex update main.py:DocumentEmbedding

# Show all flows
cocoindex show main.py
```

## Common Issues and Solutions

### Issue: "Flow not found"

**Problem:** CLI can't find the flow definition.

**Solutions:**
```bash
# Make sure APP_TARGET is correct
cocoindex show main.py  # Should list flows

# Use --app-dir if not in project root
cocoindex --app-dir /path/to/project show main.py

# Check flow name is correct
cocoindex show main.py:CorrectFlowName
```

### Issue: "Database connection failed"

**Problem:** Can't connect to Postgres.

**Solutions:**
```bash
# Check .env file exists
cat .env | grep COCOINDEX_DATABASE_URL

# Test connection
psql $COCOINDEX_DATABASE_URL

# Use --env-file if .env is elsewhere
cocoindex --env-file /path/to/.env update main.py
```

### Issue: "Schema mismatch"

**Problem:** Flow definition changed but resources not updated.

**Solution:**
```bash
# Re-run setup to update schemas
cocoindex setup main.py

# Then update data
cocoindex update main.py
```

### Issue: "Live update exits immediately"

**Problem:** No change capture mechanisms enabled.

**Solution:**
Add refresh_interval or use source-specific change capture:
```python
data_scope["docs"] = flow_builder.add_source(
    cocoindex.sources.LocalFile(path="docs"),
    refresh_interval=datetime.timedelta(seconds=30)  # Add this
)
```

## Advanced Options

### Global Options

```bash
# Show version
cocoindex --version

# Show help
cocoindex --help
cocoindex update --help

# Specify app directory
cocoindex --app-dir /custom/path update main

# Custom env file
cocoindex --env-file prod.env update main
```

### Performance Tuning

Set environment variables for concurrency:

```bash
# In .env file
COCOINDEX_SOURCE_MAX_INFLIGHT_ROWS=100
COCOINDEX_SOURCE_MAX_INFLIGHT_BYTES=1073741824  # 1GB
```

Or per-source in code:
```python
data_scope["docs"] = flow_builder.add_source(
    cocoindex.sources.LocalFile(path="docs"),
    max_inflight_rows=50,
    max_inflight_bytes=500*1024*1024  # 500MB
)
```

## Best Practices

1. **Use evaluate before update** - Test flow logic without side effects
2. **Always setup before first update** - Or use `--setup` flag
3. **Use live updates in production** - Keeps targets always fresh
4. **Set app namespace** - Organize flows across environments (dev/staging/prod)
5. **Monitor with show** - Regularly check flow statistics
6. **Version control .env.example** - Document required environment variables
7. **Use specific flow targets** - For selective updates: `main.py:FlowName`
8. **Setup after definition changes** - Ensures schemas match flow definition




### Custom_Functions

# Custom Functions Reference

Complete guide for creating custom functions in CocoIndex.

## Overview

Custom functions allow creating data transformation logic that can be used within flows. There are two approaches:

1. **Standalone function** - Simple, no configuration or setup logic
2. **Function spec + executor** - Advanced, with configuration and setup logic

## Standalone Functions

Use for simple transformations that don't need configuration or setup.

### Basic Example

```python
@cocoindex.op.function(behavior_version=1)
def compute_word_count(text: str) -> int:
    """Count words in text."""
    return len(text.split())
```

**Requirements:**
- Decorate with `@cocoindex.op.function()`
- Type annotations required for all arguments and return value
- Supports basic types, structs, tables, and numpy arrays

### With Optional Parameters

```python
@cocoindex.op.function(behavior_version=1)
def extract_info(content: str, filename: str, max_length: int | None = None) -> dict:
    """
    Extract information from content.

    Args:
        content: The document content
        filename: Source filename
        max_length: Optional maximum length for truncation
    """
    info = {
        "filename": filename,
        "length": len(content),
        "word_count": len(content.split())
    }

    if max_length and len(content) > max_length:
        info["truncated"] = True

    return info
```

### Using in Flows

```python
@cocoindex.flow_def(name="MyFlow")
def my_flow(flow_builder: cocoindex.FlowBuilder, data_scope: cocoindex.DataScope):
    data_scope["documents"] = flow_builder.add_source(
        cocoindex.sources.LocalFile(path="documents")
    )

    collector = data_scope.add_collector()

    with data_scope["documents"].row() as doc:
        # Use standalone function
        doc["word_count"] = doc["content"].transform(compute_word_count)

        # With additional arguments
        doc["info"] = doc["content"].transform(
            extract_info,
            filename=doc["filename"],
            max_length=1000
        )

        collector.collect(
            filename=doc["filename"],
            word_count=doc["word_count"],
            info=doc["info"]
        )

    collector.export("documents", cocoindex.targets.Postgres(), primary_key_fields=["filename"])
```

## Function Spec + Executor

Use for functions that need configuration or setup logic (e.g., loading models).

### Basic Structure

```python
# 1. Define the function spec (configuration)
class ComputeSomething(cocoindex.op.FunctionSpec):
    """
    Configuration for the ComputeSomething function.
    """
    param1: str
    param2: int = 10  # Optional with default

# 2. Define the executor (implementation)
@cocoindex.op.executor_class(behavior_version=1)
class ComputeSomethingExecutor:
    spec: ComputeSomething  # Required: link to spec

    def prepare(self) -> None:
        """
        Optional: Setup logic run once before execution.
        Use for loading models, establishing connections, etc.
        """
        # Setup based on self.spec
        pass

    def __call__(self, input_data: str) -> dict:
        """
        Required: Execute the function for each data row.

        Args must have type annotations.
        Return type must have type annotation.
        """
        # Use self.spec.param1, self.spec.param2
        return {"result": f"{input_data}-{self.spec.param1}"}
```

### Example: Custom Embedding Function

```python
from sentence_transformers import SentenceTransformer
import numpy as np
from numpy.typing import NDArray

class CustomEmbed(cocoindex.op.FunctionSpec):
    """
    Embed text using a specified SentenceTransformer model.
    """
    model_name: str
    normalize: bool = True

@cocoindex.op.executor_class(cache=True, behavior_version=1)
class CustomEmbedExecutor:
    spec: CustomEmbed
    model: SentenceTransformer | None = None

    def prepare(self) -> None:
        """Load the model once during initialization."""
        self.model = SentenceTransformer(self.spec.model_name)

    def __call__(self, text: str) -> NDArray[np.float32]:
        """Embed the input text."""
        assert self.model is not None
        embedding = self.model.encode(text, normalize_embeddings=self.spec.normalize)
        return embedding.astype(np.float32)

# Usage in flow
@cocoindex.flow_def(name="CustomEmbedFlow")
def custom_embed_flow(flow_builder: cocoindex.FlowBuilder, data_scope: cocoindex.DataScope):
    data_scope["documents"] = flow_builder.add_source(
        cocoindex.sources.LocalFile(path="documents")
    )

    collector = data_scope.add_collector()

    with data_scope["documents"].row() as doc:
        doc["embedding"] = doc["content"].transform(
            CustomEmbed(
                model_name="sentence-transformers/all-MiniLM-L6-v2",
                normalize=True
            )
        )

        collector.collect(
            text=doc["content"],
            embedding=doc["embedding"]
        )

    collector.export("embeddings", cocoindex.targets.Postgres(), primary_key_fields=["text"])
```

### Example: PDF Processing

```python
import pymupdf  # PyMuPDF

class PdfToMarkdown(cocoindex.op.FunctionSpec):
    """
    Convert PDF to markdown.
    """
    extract_images: bool = False
    page_range: tuple[int, int] | None = None  # (start, end) pages

@cocoindex.op.executor_class(cache=True, behavior_version=1)
class PdfToMarkdownExecutor:
    spec: PdfToMarkdown

    def __call__(self, pdf_bytes: bytes) -> str:
        """Convert PDF bytes to markdown text."""
        doc = pymupdf.Document(stream=pdf_bytes, filetype="pdf")

        # Determine page range
        start = 0
        end = doc.page_count
        if self.spec.page_range:
            start, end = self.spec.page_range
            start = max(0, start)
            end = min(doc.page_count, end)

        markdown_parts = []
        for page_num in range(start, end):
            page = doc[page_num]
            text = page.get_text()
            markdown_parts.append(f"# Page {page_num + 1}\n\n{text}")

        return "\n\n".join(markdown_parts)

# Usage
@cocoindex.flow_def(name="PdfFlow")
def pdf_flow(flow_builder: cocoindex.FlowBuilder, data_scope: cocoindex.DataScope):
    data_scope["pdfs"] = flow_builder.add_source(
        cocoindex.sources.LocalFile(path="pdfs", included_patterns=["*.pdf"])
    )

    collector = data_scope.add_collector()

    with data_scope["pdfs"].row() as pdf:
        pdf["markdown"] = pdf["content"].transform(
            PdfToMarkdown(extract_images=False, page_range=(0, 10))
        )

        collector.collect(
            filename=pdf["filename"],
            markdown=pdf["markdown"]
        )

    collector.export("pdf_text", cocoindex.targets.Postgres(), primary_key_fields=["filename"])
```

## Function Parameters

Both standalone functions and executors support these parameters:

### cache (bool)

Enable caching of function results for reuse during reprocessing.

```python
@cocoindex.op.function(cache=True, behavior_version=1)
def expensive_computation(text: str) -> dict:
    # Computationally intensive operation
    return {"result": analyze(text)}
```

**When to use:**
- Functions that are computationally expensive
- LLM API calls
- Model inference
- External API calls

### behavior_version (int)

Required when `cache=True`. Increment this when function behavior changes to invalidate cache.

```python
@cocoindex.op.function(cache=True, behavior_version=2)  # Incremented from 1
def improved_analysis(text: str) -> dict:
    # Updated algorithm - need to reprocess cached data
    return {"result": new_analysis(text)}
```

### gpu (bool)

Indicates the function uses GPU resources, affecting scheduling.

```python
@cocoindex.op.executor_class(gpu=True, cache=True, behavior_version=1)
class GpuModelExecutor:
    spec: GpuModel

    def prepare(self) -> None:
        self.model = load_model_on_gpu(self.spec.model_name)

    def __call__(self, text: str) -> NDArray[np.float32]:
        return self.model.predict(text)
```

### arg_relationship

Specifies metadata about argument relationships for tools like CocoInsight.

```python
@cocoindex.op.function(
    cache=True,
    behavior_version=1,
    arg_relationship=(cocoindex.ArgRelationship.CHUNKS_BASE_TEXT, "content")
)
def custom_chunker(content: str, chunk_size: int) -> list[dict]:
    """
    Chunks are derived from 'content' argument.
    First element of each chunk dict must be a Range type.
    """
    # Return list of chunks with location ranges
    return [
        {"location": cocoindex.Range(...), "text": chunk}
        for chunk in split_content(content, chunk_size)
    ]
```

**Supported relationships:**
- `ArgRelationship.CHUNKS_BASE_TEXT` - Output is chunks of input text
- `ArgRelationship.EMBEDDING_ORIGIN_TEXT` - Output is embedding of input text
- `ArgRelationship.RECTS_BASE_IMAGE` - Output is rectangles on input image

## Supported Data Types

Functions can use these types for arguments and return values:

### Basic Types
- `str` - Text
- `int` - Integer (maps to Int64)
- `float` - Float (maps to Float64)
- `bool` - Boolean
- `bytes` - Binary data
- `None` / `type(None)` - Null value

### Collection Types
- `list[T]` - List of type T
- `dict[str, T]` - Dictionary (becomes Struct)
- `cocoindex.Json` - Arbitrary JSON

### Numpy Types
- `NDArray[np.float32]` - Vector[Float32, N]
- `NDArray[np.float64]` - Vector[Float64, N]
- `NDArray[np.int32]` - Vector[Int32, N]
- `NDArray[np.int64]` - Vector[Int64, N]

### CocoIndex Types
- `cocoindex.Range` - Text range with location info
- Dataclasses - Become Struct types

### Optional Types
- `T | None` or `Optional[T]` - Optional value

### Table Types (Output only)
Functions can return table-like data using dataclasses:

```python
@dataclasses.dataclass
class Chunk:
    location: cocoindex.Range
    text: str

@cocoindex.op.function(behavior_version=1)
def chunk_text(content: str) -> list[Chunk]:
    """Returns a list representing a table."""
    return [
        Chunk(location=..., text=chunk)
        for chunk in split_content(content)
    ]
```

## Common Patterns

### Pattern: LLM-based Extraction

```python
from openai import OpenAI

class ExtractStructuredInfo(cocoindex.op.FunctionSpec):
    """Extract structured information using an LLM."""
    model: str = "gpt-4"
    system_prompt: str = "Extract key information from the text."

@cocoindex.op.executor_class(cache=True, behavior_version=1)
class ExtractStructuredInfoExecutor:
    spec: ExtractStructuredInfo
    client: OpenAI | None = None

    def prepare(self) -> None:
        self.client = OpenAI()  # Uses OPENAI_API_KEY env var

    def __call__(self, text: str) -> dict:
        assert self.client is not None
        response = self.client.chat.completions.create(
            model=self.spec.model,
            messages=[
                {"role": "system", "content": self.spec.system_prompt},
                {"role": "user", "content": text}
            ]
        )
        # Parse and return structured data
        return {"extracted": response.choices[0].message.content}
```

### Pattern: External API Call

```python
import requests

class FetchEnrichmentData(cocoindex.op.FunctionSpec):
    """Fetch enrichment data from external API."""
    api_endpoint: str
    api_key: str

@cocoindex.op.executor_class(cache=True, behavior_version=1)
class FetchEnrichmentDataExecutor:
    spec: FetchEnrichmentData

    def __call__(self, entity_id: str) -> dict:
        response = requests.get(
            f"{self.spec.api_endpoint}/entities/{entity_id}",
            headers={"Authorization": f"Bearer {self.spec.api_key}"}
        )
        response.raise_for_status()
        return response.json()
```

### Pattern: Multi-step Processing

```python
class ProcessDocument(cocoindex.op.FunctionSpec):
    """Process document through multiple steps."""
    min_quality_score: float = 0.7

@cocoindex.op.executor_class(cache=True, behavior_version=1)
class ProcessDocumentExecutor:
    spec: ProcessDocument
    nlp_model = None

    def prepare(self) -> None:
        import spacy
        self.nlp_model = spacy.load("en_core_web_sm")

    def __call__(self, text: str) -> dict:
        # Step 1: Clean text
        cleaned = self._clean_text(text)

        # Step 2: Extract entities
        doc = self.nlp_model(cleaned)
        entities = [ent.text for ent in doc.ents]

        # Step 3: Quality check
        quality_score = self._compute_quality(cleaned)

        return {
            "cleaned_text": cleaned if quality_score >= self.spec.min_quality_score else None,
            "entities": entities,
            "quality_score": quality_score
        }

    def _clean_text(self, text: str) -> str:
        # Cleaning logic
        return text.strip()

    def _compute_quality(self, text: str) -> float:
        # Quality scoring logic
        return len(text) / 1000.0
```

## Best Practices

1. **Use caching for expensive operations** - Enable `cache=True` for LLM calls, model inference, or external APIs
2. **Type annotations required** - All arguments and return types must be annotated
3. **Increment behavior_version** - When changing cached function logic, increment version to invalidate cache
4. **Use prepare() for initialization** - Load models, establish connections once in prepare()
5. **Keep functions focused** - Each function should do one thing well
6. **Document parameters** - Use docstrings to explain function purpose and parameters
7. **Handle errors gracefully** - Consider edge cases and invalid inputs
8. **Use appropriate return types** - Match return types to target schema needs




### Flow_Patterns

# CocoIndex Flow Patterns

This reference provides common patterns and examples for building CocoIndex flows.

## Basic Flow Pattern

```python
import cocoindex

@cocoindex.flow_def(name="FlowName")
def my_flow(flow_builder: cocoindex.FlowBuilder, data_scope: cocoindex.DataScope):
    # 1. Import source data
    data_scope["source_data"] = flow_builder.add_source(...)

    # 2. Create collectors for output
    my_collector = data_scope.add_collector()

    # 3. Transform data
    with data_scope["source_data"].row() as item:
        item["transformed"] = item["field"].transform(...)
        my_collector.collect(...)

    # 4. Export to target
    my_collector.export("target_name", ..., primary_key_fields=[...])
```

## Common Flow Patterns

### Pattern 1: Simple Text Embedding

Embed documents from local files into a vector database.

```python
@cocoindex.flow_def(name="TextEmbedding")
def text_embedding_flow(flow_builder: cocoindex.FlowBuilder, data_scope: cocoindex.DataScope):
    # Import documents
    data_scope["documents"] = flow_builder.add_source(
        cocoindex.sources.LocalFile(path="documents")
    )

    doc_embeddings = data_scope.add_collector()

    with data_scope["documents"].row() as doc:
        # Split into chunks
        doc["chunks"] = doc["content"].transform(
            cocoindex.functions.SplitRecursively(),
            language="markdown",
            chunk_size=2000,
            chunk_overlap=500
        )

        with doc["chunks"].row() as chunk:
            # Embed each chunk
            chunk["embedding"] = chunk["text"].transform(
                cocoindex.functions.SentenceTransformerEmbed(
                    model="sentence-transformers/all-MiniLM-L6-v2"
                )
            )

            doc_embeddings.collect(
                id=cocoindex.GeneratedField.UUID,
                filename=doc["filename"],
                text=chunk["text"],
                embedding=chunk["embedding"]
            )

    # Export to Postgres with vector index
    doc_embeddings.export(
        "doc_embeddings",
        cocoindex.targets.Postgres(),
        primary_key_fields=["id"],
        vector_indexes=[
            cocoindex.VectorIndexDef(
                field_name="embedding",
                metric=cocoindex.VectorSimilarityMetric.COSINE_SIMILARITY
            )
        ]
    )
```

### Pattern 2: Code Embedding with Language Detection

```python
@cocoindex.flow_def(name="CodeEmbedding")
def code_embedding_flow(flow_builder: cocoindex.FlowBuilder, data_scope: cocoindex.DataScope):
    data_scope["files"] = flow_builder.add_source(
        cocoindex.sources.LocalFile(
            path=".",
            included_patterns=["*.py", "*.rs", "*.md"],
            excluded_patterns=["**/.*", "target", "**/node_modules"]
        )
    )

    code_embeddings = data_scope.add_collector()

    with data_scope["files"].row() as file:
        # Detect language
        file["language"] = file["filename"].transform(
            cocoindex.functions.DetectProgrammingLanguage()
        )

        # Split using language-aware chunking
        file["chunks"] = file["content"].transform(
            cocoindex.functions.SplitRecursively(),
            language=file["language"],
            chunk_size=1000,
            chunk_overlap=300
        )

        with file["chunks"].row() as chunk:
            chunk["embedding"] = chunk["text"].transform(
                cocoindex.functions.SentenceTransformerEmbed(
                    model="sentence-transformers/all-MiniLM-L6-v2"
                )
            )

            code_embeddings.collect(
                filename=file["filename"],
                location=chunk["location"],
                code=chunk["text"],
                embedding=chunk["embedding"],
                start=chunk["start"],
                end=chunk["end"]
            )

    code_embeddings.export(
        "code_embeddings",
        cocoindex.targets.Postgres(),
        primary_key_fields=["filename", "location"],
        vector_indexes=[
            cocoindex.VectorIndexDef(
                field_name="embedding",
                metric=cocoindex.VectorSimilarityMetric.COSINE_SIMILARITY
            )
        ]
    )
```

### Pattern 3: LLM-based Extraction to Knowledge Graph

Extract structured information using LLMs and build a knowledge graph.

```python
import dataclasses

@dataclasses.dataclass
class ProductInfo:
    id: str
    title: str
    price: float

@dataclasses.dataclass
class Taxonomy:
    name: str

@cocoindex.flow_def(name="ProductGraph")
def product_graph_flow(flow_builder: cocoindex.FlowBuilder, data_scope: cocoindex.DataScope):
    # Setup Neo4j connection
    neo4j_conn = cocoindex.add_auth_entry(
        "Neo4jConnection",
        cocoindex.targets.Neo4jConnection(
            uri="bolt://localhost:7687",
            user="neo4j",
            password="password"
        )
    )

    data_scope["products"] = flow_builder.add_source(
        cocoindex.sources.LocalFile(path="products", included_patterns=["*.json"])
    )

    product_nodes = data_scope.add_collector()
    product_taxonomy = data_scope.add_collector()

    with data_scope["products"].row() as product:
        # Parse JSON and extract info
        data = product["content"].transform(
            cocoindex.functions.ParseJson()
        )

        # Use LLM to extract taxonomies
        taxonomy = data["description"].transform(
            cocoindex.functions.ExtractByLlm(
                llm_spec=cocoindex.LlmSpec(
                    api_type=cocoindex.LlmApiType.OPENAI,
                    model="gpt-4"
                ),
                output_type=list[Taxonomy]
            )
        )

        product_nodes.collect(
            id=data["id"],
            title=data["title"],
            price=data["price"]
        )

        with taxonomy.row() as t:
            product_taxonomy.collect(
                id=cocoindex.GeneratedField.UUID,
                product_id=data["id"],
                taxonomy=t["name"]
            )

    # Export product nodes
    product_nodes.export(
        "product_node",
        cocoindex.targets.Neo4j(
            connection=neo4j_conn,
            mapping=cocoindex.targets.Nodes(label="Product")
        ),
        primary_key_fields=["id"]
    )

    # Declare taxonomy nodes
    flow_builder.declare(
        cocoindex.targets.Neo4jDeclaration(
            connection=neo4j_conn,
            nodes_label="Taxonomy",
            primary_key_fields=["value"]
        )
    )

    # Export relationships
    product_taxonomy.export(
        "product_taxonomy",
        cocoindex.targets.Neo4j(
            connection=neo4j_conn,
            mapping=cocoindex.targets.Relationships(
                rel_type="HAS_TAXONOMY",
                source=cocoindex.targets.NodeFromFields(
                    label="Product",
                    fields=[cocoindex.targets.TargetFieldMapping(source="product_id", target="id")]
                ),
                target=cocoindex.targets.NodeFromFields(
                    label="Taxonomy",
                    fields=[cocoindex.targets.TargetFieldMapping(source="taxonomy", target="value")]
                )
            )
        ),
        primary_key_fields=["id"]
    )
```

### Pattern 4: Live Updates with Refresh Interval

```python
import datetime

@cocoindex.flow_def(name="LiveDataFlow")
def live_data_flow(flow_builder: cocoindex.FlowBuilder, data_scope: cocoindex.DataScope):
    # Add source with refresh interval
    data_scope["documents"] = flow_builder.add_source(
        cocoindex.sources.LocalFile(path="live_documents"),
        refresh_interval=datetime.timedelta(minutes=1)  # Refresh every minute
    )

    # ... rest of flow definition
```

### Pattern 5: Custom Transform Function

```python
@cocoindex.op.function(behavior_version=1)
def extract_metadata(content: str, filename: str) -> dict:
    """Extract metadata from document content."""
    return {
        "word_count": len(content.split()),
        "char_count": len(content),
        "source": filename
    }

@cocoindex.flow_def(name="CustomFunctionFlow")
def custom_function_flow(flow_builder: cocoindex.FlowBuilder, data_scope: cocoindex.DataScope):
    data_scope["documents"] = flow_builder.add_source(
        cocoindex.sources.LocalFile(path="documents")
    )

    collector = data_scope.add_collector()

    with data_scope["documents"].row() as doc:
        # Use custom function
        doc["metadata"] = doc["content"].transform(
            extract_metadata,
            filename=doc["filename"]
        )

        collector.collect(
            filename=doc["filename"],
            word_count=doc["metadata"]["word_count"],
            char_count=doc["metadata"]["char_count"]
        )

    collector.export("metadata", cocoindex.targets.Postgres(), primary_key_fields=["filename"])
```

### Pattern 6: Transform Flow for Reusable Logic

Transform flows allow extracting reusable transformation logic that can be shared between indexing and querying.

```python
@cocoindex.transform_flow()
def text_to_embedding(text: cocoindex.DataSlice[str]) -> cocoindex.DataSlice[list[float]]:
    """Shared embedding logic for both indexing and querying."""
    return text.transform(
        cocoindex.functions.SentenceTransformerEmbed(
            model="sentence-transformers/all-MiniLM-L6-v2"
        )
    )

@cocoindex.flow_def(name="MainFlow")
def main_flow(flow_builder: cocoindex.FlowBuilder, data_scope: cocoindex.DataScope):
    data_scope["documents"] = flow_builder.add_source(
        cocoindex.sources.LocalFile(path="documents")
    )

    collector = data_scope.add_collector()

    with data_scope["documents"].row() as doc:
        # Use transform flow
        doc["embedding"] = text_to_embedding(doc["content"])
        collector.collect(text=doc["content"], embedding=doc["embedding"])

    collector.export("docs", cocoindex.targets.Postgres(), primary_key_fields=["text"])

# Later, use same transform flow for querying
def search(query: str):
    query_embedding = text_to_embedding.eval(query)  # Evaluate with input
    # ... perform search with query_embedding
```

### Pattern 7: Concurrency Control

```python
@cocoindex.flow_def(name="ConcurrencyControlFlow")
def concurrency_flow(flow_builder: cocoindex.FlowBuilder, data_scope: cocoindex.DataScope):
    # Limit concurrent processing at source level
    data_scope["documents"] = flow_builder.add_source(
        cocoindex.sources.LocalFile(path="large_documents"),
        max_inflight_rows=10,                    # Max 10 documents at once
        max_inflight_bytes=100 * 1024 * 1024    # Max 100MB in memory
    )

    collector = data_scope.add_collector()

    with data_scope["documents"].row() as doc:
        doc["chunks"] = doc["content"].transform(
            cocoindex.functions.SplitRecursively(),
            chunk_size=2000
        )

        # Limit concurrent processing at row iteration level
        with doc["chunks"].row(max_inflight_rows=100) as chunk:
            chunk["embedding"] = chunk["text"].transform(
                cocoindex.functions.SentenceTransformerEmbed(
                    model="sentence-transformers/all-MiniLM-L6-v2"
                )
            )
            collector.collect(text=chunk["text"], embedding=chunk["embedding"])

    collector.export("chunks", cocoindex.targets.Postgres(), primary_key_fields=["text"])
```

## Data Source Patterns

### Local Files

```python
cocoindex.sources.LocalFile(
    path="documents",
    included_patterns=["*.md", "*.txt"],
    excluded_patterns=["**/.*", "node_modules"]
)
```

### Amazon S3

```python
cocoindex.sources.AmazonS3(
    bucket="my-bucket",
    prefix="documents/",
    included_patterns=["*.pdf"],
    aws_access_key_id=cocoindex.add_transient_auth_entry("..."),
    aws_secret_access_key=cocoindex.add_transient_auth_entry("...")
)
```

### Postgres Source

```python
cocoindex.sources.Postgres(
    connection=cocoindex.add_auth_entry(
        "postgres_conn",
        cocoindex.sources.PostgresConnection(
            host="localhost",
            database="mydb",
            user="user",
            password="password"
        )
    ),
    query="SELECT id, content FROM documents"
)
```

## Target Patterns

### Postgres

```python
collector.export(
    "target_name",
    cocoindex.targets.Postgres(),
    primary_key_fields=["id"],
    vector_indexes=[
        cocoindex.VectorIndexDef(
            field_name="embedding",
            metric=cocoindex.VectorSimilarityMetric.COSINE_SIMILARITY
        )
    ]
)
```

### Qdrant

```python
collector.export(
    "target_name",
    cocoindex.targets.Qdrant(collection_name="my_collection"),
    primary_key_fields=["id"]
)
```

### LanceDB

```python
collector.export(
    "target_name",
    cocoindex.targets.LanceDB(
        uri="lancedb_data",
        table_name="my_table"
    ),
    primary_key_fields=["id"]
)
```

### Neo4j (Knowledge Graph)

```python
# Node export
collector.export(
    "nodes",
    cocoindex.targets.Neo4j(
        connection=neo4j_conn,
        mapping=cocoindex.targets.Nodes(label="Entity")
    ),
    primary_key_fields=["id"]
)

# Relationship export
collector.export(
    "relationships",
    cocoindex.targets.Neo4j(
        connection=neo4j_conn,
        mapping=cocoindex.targets.Relationships(
            rel_type="RELATES_TO",
            source=cocoindex.targets.NodeFromFields(
                label="Entity",
                fields=[cocoindex.targets.TargetFieldMapping(source="source_id", target="id")]
            ),
            target=cocoindex.targets.NodeFromFields(
                label="Entity",
                fields=[cocoindex.targets.TargetFieldMapping(source="target_id", target="id")]
            )
        )
    ),
    primary_key_fields=["id"]
)
```




### Api_Operations

# API Operations Reference

Guide for operating CocoIndex flows programmatically using Python APIs.

## Overview

CocoIndex flows can be operated through Python APIs, providing programmatic control over setup, updates, and queries. This is useful for embedding flows in applications, automating workflows, or building custom tools.

## Basic Setup

### Initialization

```python
from dotenv import load_dotenv
import cocoindex

# Load environment variables
load_dotenv()

# Initialize CocoIndex
cocoindex.init()
```

### Flow Definition

```python
@cocoindex.flow_def(name="MyFlow")
def my_flow(flow_builder: cocoindex.FlowBuilder, data_scope: cocoindex.DataScope):
    # Flow definition
    pass
```

The decorator returns a `cocoindex.Flow` object that can be used for operations.

## Flow Operations

### Setup Flow

Create persistent backends (tables, collections, etc.) for the flow.

```python
# Basic setup
my_flow.setup()

# With progress output
my_flow.setup(report_to_stdout=True)

# Async version
await my_flow.setup_async(report_to_stdout=True)
```

**When to use:**
- Before first update
- After modifying flow structure
- After dropping flow to recreate resources

### Setup All Flows

```python
# Setup all flows at once
cocoindex.setup_all_flows(report_to_stdout=True)
```

### Drop Flow

Remove all persistent backends owned by the flow.

```python
# Drop flow
my_flow.drop()

# With progress output
my_flow.drop(report_to_stdout=True)

# Async version
await my_flow.drop_async(report_to_stdout=True)
```

**Note:** After dropping, the Flow object is still valid and can be setup again.

### Drop All Flows

```python
# Drop all flows
cocoindex.drop_all_flows(report_to_stdout=True)
```

### Close Flow

Remove flow from current process memory (doesn't affect persistent data).

```python
my_flow.close()
# After this, my_flow is invalid and should not be used
```

## Update Operations

### One-Time Update

Build or update target data based on current source data.

```python
# Basic update
stats = my_flow.update()
print(f"Processed {stats.total_rows} rows")

# With reexport (force reprocess even if unchanged)
stats = my_flow.update(reexport_targets=True)

# Async version
stats = await my_flow.update_async()
stats = await my_flow.update_async(reexport_targets=True)
```

**Returns:** Statistics about processed data

**Note:** Multiple calls to `update()` can run simultaneously. CocoIndex will automatically combine them efficiently.

### Live Update

Continuously monitor source changes and update targets.

```python
import cocoindex

# Create live updater
updater = cocoindex.FlowLiveUpdater(
    my_flow,
    cocoindex.FlowLiveUpdaterOptions(
        live_mode=True,        # Enable live updates
        print_stats=True,      # Print progress
        reexport_targets=False # Only reexport on first update if True
    )
)

# Start the updater
updater.start()

# Your application logic here
# (updater runs in background threads)

# Wait for completion
updater.wait()

# Print final stats
print(updater.update_stats())
```

#### As Context Manager

```python
with cocoindex.FlowLiveUpdater(my_flow) as updater:
    # Updater starts automatically
    # Your application logic here
    pass
# Updater aborts and waits automatically

# Async version
async with cocoindex.FlowLiveUpdater(my_flow) as updater:
    # Your application logic
    pass
```

#### Monitoring Status Updates

```python
updater = cocoindex.FlowLiveUpdater(my_flow)
updater.start()

while True:
    # Block until next status update
    updates = updater.next_status_updates()

    # Check which sources were updated
    for source in updates.updated_sources:
        print(f"Source {source} has new data")
        # Trigger downstream operations

    # Check if updater stopped
    if not updates.active_sources:
        print("All sources stopped")
        break

# Async version
while True:
    updates = await updater.next_status_updates_async()
    # ... same logic
```

#### Control Methods

```python
# Start updater
updater.start()
await updater.start_async()

# Abort updater
updater.abort()

# Wait for completion
updater.wait()
await updater.wait_async()

# Get current stats
stats = updater.update_stats()
```

## Evaluate Flow

Run transformations without updating targets (for testing).

```python
# Evaluate and dump results
my_flow.evaluate_and_dump(
    cocoindex.EvaluateAndDumpOptions(
        output_dir="./eval_output",
        use_cache=True  # Use existing cache (but don't update it)
    )
)
```

**Use cases:**
- Testing flow logic
- Debugging transformations
- Inspecting intermediate data

## Query Operations

### Transform Flows

Transform flows enable reusable transformation logic for both indexing and querying.

```python
from numpy.typing import NDArray
import numpy as np

# Define transform flow
@cocoindex.transform_flow()
def text_to_embedding(
    text: cocoindex.DataSlice[str]
) -> cocoindex.DataSlice[NDArray[np.float32]]:
    """Convert text to embedding vector."""
    return text.transform(
        cocoindex.functions.SentenceTransformerEmbed(
            model="sentence-transformers/all-MiniLM-L6-v2"
        )
    )

# Use in indexing flow
@cocoindex.flow_def(name="TextEmbedding")
def text_embedding_flow(flow_builder, data_scope):
    # ... setup source ...
    with data_scope["documents"].row() as doc:
        doc["embedding"] = text_to_embedding(doc["content"])
        # ... collect and export ...

# Use for querying (evaluate with input)
query_embedding = text_to_embedding.eval("search query text")
# query_embedding is now a numpy array
```

### Query Handlers

Attach query logic to flows for easy query execution.

```python
import functools
from psycopg_pool import ConnectionPool
from pgvector.psycopg import register_vector

@functools.cache
def connection_pool():
    return ConnectionPool(os.environ["COCOINDEX_DATABASE_URL"])

# Register query handler
@my_flow.query_handler(
    result_fields=cocoindex.QueryHandlerResultFields(
        embedding=["embedding"],  # Field name(s) containing embeddings
        score="score"             # Field name for similarity score
    )
)
def search(query: str) -> cocoindex.QueryOutput:
    """Search for documents matching query."""

    # Get table name for this flow's export
    table_name = cocoindex.utils.get_target_default_name(my_flow, "doc_embeddings")

    # Compute query embedding using transform flow
    query_vector = text_to_embedding.eval(query)

    # Execute query
    with connection_pool().connection() as conn:
        register_vector(conn)
        with conn.cursor() as cur:
            cur.execute(
                f"""
                SELECT filename, text, embedding, embedding <=> %s AS distance
                FROM {table_name}
                ORDER BY distance
                LIMIT 10
                """,
                (query_vector,)
            )

            return cocoindex.QueryOutput(
                query_info=cocoindex.QueryInfo(
                    embedding=query_vector,
                    similarity_metric=cocoindex.VectorSimilarityMetric.COSINE_SIMILARITY
                ),
                results=[
                    {
                        "filename": row[0],
                        "text": row[1],
                        "embedding": row[2],
                        "score": 1.0 - row[3]  # Convert distance to similarity
                    }
                    for row in cur.fetchall()
                ]
            )

# Call the query handler
results = search("machine learning algorithms")
for result in results.results:
    print(f"[{result['score']:.3f}] {result['filename']}: {result['text']}")
```

### Query with Qdrant

```python
from qdrant_client import QdrantClient
import functools

@functools.cache
def get_qdrant_client():
    return QdrantClient(url="http://localhost:6334", prefer_grpc=True)

@my_flow.query_handler(
    result_fields=cocoindex.QueryHandlerResultFields(
        embedding=["embedding"],
        score="score"
    )
)
def search_qdrant(query: str) -> cocoindex.QueryOutput:
    client = get_qdrant_client()

    # Get query embedding
    query_embedding = text_to_embedding.eval(query)

    # Search Qdrant
    search_results = client.search(
        collection_name="my_collection",
        query_vector=("text_embedding", query_embedding),
        limit=10
    )

    return cocoindex.QueryOutput(
        query_info=cocoindex.QueryInfo(
            embedding=query_embedding,
            similarity_metric=cocoindex.VectorSimilarityMetric.COSINE_SIMILARITY
        ),
        results=[
            {
                "text": result.payload["text"],
                "embedding": result.vector,
                "score": result.score
            }
            for result in search_results
        ]
    )
```

## Application Integration Patterns

### Pattern 1: Simple Application with Update

```python
from dotenv import load_dotenv
import cocoindex

# Initialize
load_dotenv()
cocoindex.init()

# Define flow
@cocoindex.flow_def(name="MyApp")
def my_app_flow(flow_builder, data_scope):
    # ... flow definition ...
    pass

def main():
    # Ensure flow is set up and data is fresh
    stats = my_app_flow.update()
    print(f"Updated index: {stats}")

    # Run application logic
    while True:
        query = input("Search: ")
        if not query:
            break
        results = search(query)
        for result in results.results:
            print(f"  {result['score']:.3f}: {result['text']}")

if __name__ == "__main__":
    main()
```

### Pattern 2: Web Application with Live Updates

```python
from fastapi import FastAPI
import cocoindex
from dotenv import load_dotenv

load_dotenv()
cocoindex.init()

@cocoindex.flow_def(name="WebAppFlow")
def web_app_flow(flow_builder, data_scope):
    # ... flow definition ...
    pass

# Create FastAPI app
app = FastAPI()

# Global updater
updater = None

@app.on_event("startup")
async def startup():
    global updater
    # Start live updater in background
    updater = cocoindex.FlowLiveUpdater(
        web_app_flow,
        cocoindex.FlowLiveUpdaterOptions(live_mode=True, print_stats=True)
    )
    await updater.start_async()
    print("Live updater started")

@app.on_event("shutdown")
async def shutdown():
    global updater
    if updater:
        updater.abort()
        await updater.wait_async()
        print("Live updater stopped")

@app.get("/search")
async def search_endpoint(q: str):
    results = search(q)
    return {
        "query": q,
        "results": results.results
    }
```

### Pattern 3: Batch Processing

```python
import cocoindex
from dotenv import load_dotenv

load_dotenv()
cocoindex.init()

@cocoindex.flow_def(name="BatchProcessor")
def batch_flow(flow_builder, data_scope):
    # ... flow definition ...
    pass

def process_batch():
    """Run as scheduled job (cron, etc.)"""
    # Setup if needed (no-op if already set up)
    batch_flow.setup()

    # Run update
    stats = batch_flow.update()

    # Log results
    print(f"Batch completed: {stats.total_rows} rows processed")

    return stats

if __name__ == "__main__":
    process_batch()
```

### Pattern 4: React to Updates

```python
import cocoindex

@cocoindex.flow_def(name="ReactiveFlow")
def reactive_flow(flow_builder, data_scope):
    # ... flow definition ...
    pass

async def run_with_reactions():
    """Monitor updates and trigger downstream actions."""
    async with cocoindex.FlowLiveUpdater(reactive_flow) as updater:
        while True:
            updates = await updater.next_status_updates_async()

            # React to specific source updates
            if "products" in updates.updated_sources:
                await rebuild_product_index()

            if "customers" in updates.updated_sources:
                await refresh_customer_cache()

            # Exit when updater stops
            if not updates.active_sources:
                break

async def rebuild_product_index():
    print("Rebuilding product index...")
    # Custom logic

async def refresh_customer_cache():
    print("Refreshing customer cache...")
    # Custom logic
```

## Error Handling

### Handling Update Errors

```python
try:
    stats = my_flow.update()
except cocoindex.CocoIndexError as e:
    print(f"Update failed: {e}")
    # Handle error (log, retry, alert, etc.)
```

### Graceful Shutdown

```python
import signal

updater = None

def signal_handler(sig, frame):
    print("Shutting down gracefully...")
    if updater:
        updater.abort()
        updater.wait()
    print("Shutdown complete")
    exit(0)

signal.signal(signal.SIGINT, signal_handler)
signal.signal(signal.SIGTERM, signal_handler)

updater = cocoindex.FlowLiveUpdater(my_flow)
updater.start()
updater.wait()
```

## Best Practices

1. **Always call cocoindex.init()** - Initialize before using any CocoIndex APIs
2. **Load environment variables** - Use dotenv or similar to load configuration
3. **Use context managers** - For live updaters to ensure cleanup
4. **Cache expensive resources** - Use `@functools.cache` for database pools, clients
5. **Handle signals** - Gracefully shutdown live updaters on SIGINT/SIGTERM
6. **Separate concerns** - Keep flow definitions, queries, and application logic separate
7. **Use transform flows** - Share logic between indexing and querying
8. **Monitor update stats** - Log and track processing statistics
9. **Test with evaluate** - Use evaluate_and_dump for testing before updates




---

## 🚀 Usage

**Reference this template:** `@skill-cocoindex.md`


**Platform-specific:**
- **GitHub Copilot**: Add to `.github/copilot-instructions.md`
- **Augment Code**: Use `aug context add` command
- **Cursor/Windsurf**: Reference in chat with `@filename`
- **Claude**: Add to Project Knowledge
- **ChatGPT**: Add to Custom GPT configuration
