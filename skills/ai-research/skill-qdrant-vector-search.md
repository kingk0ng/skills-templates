---
id: skill-qdrant-vector-search
type: skill
name: qdrant-vector-search
description: High-performance vector similarity search engine for RAG and semantic
  search. Use when building production RAG systems requiring fast nearest neighbor
  search, hybrid search with filtering, or scalable vector storage with Rust-powered
  performance.
category: ai-research
complexity: medium
keywords:
- api
- database
- deployment
- docker
- github
- graphql
- grpc
- optimization
- performance
- python
- rest
- rust
capabilities: []
token_estimate: 1575
has_references: true
reference_count: 2
---

<!-- Converted from Claude Skill Template -->
<!-- Complexity: Medium -->
<!-- Estimated Tokens: ~1,575 -->
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




# qdrant-vector-search

> High-performance vector similarity search engine for RAG and semantic search. Use when building production RAG systems requiring fast nearest neighbor search, hybrid search with filtering, or scalable vector storage with Rust-powered performance.

# Qdrant - Vector Similarity Search Engine

High-performance vector database written in Rust for production RAG and semantic search.

## When to use Qdrant

**Use Qdrant when:**
- Building production RAG systems requiring low latency
- Need hybrid search (vectors + metadata filtering)
- Require horizontal scaling with sharding/replication
- Want on-premise deployment with full data control
- Need multi-vector storage per record (dense + sparse)
- Building real-time recommendation systems

**Key features:**
- **Rust-powered**: Memory-safe, high performance
- **Rich filtering**: Filter by any payload field during search
- **Multiple vectors**: Dense, sparse, multi-dense per point
- **Quantization**: Scalar, product, binary for memory efficiency
- **Distributed**: Raft consensus, sharding, replication
- **REST + gRPC**: Both APIs with full feature parity

**Use alternatives instead:**
- **Chroma**: Simpler setup, embedded use cases
- **FAISS**: Maximum raw speed, research/batch processing
- **Pinecone**: Fully managed, zero ops preferred
- **Weaviate**: GraphQL preference, built-in vectorizers

## Quick start

### Installation

```bash
# Python client
pip install qdrant-client

# Docker (recommended for development)
docker run -p 6333:6333 -p 6334:6334 qdrant/qdrant

# Docker with persistent storage
docker run -p 6333:6333 -p 6334:6334 \
    -v $(pwd)/qdrant_storage:/qdrant/storage \
    qdrant/qdrant
```

### Basic usage

```python
from qdrant_client import QdrantClient
from qdrant_client.models import Distance, VectorParams, PointStruct

# Connect to Qdrant
client = QdrantClient(host="localhost", port=6333)

# Create collection
client.create_collection(
    collection_name="documents",
    vectors_config=VectorParams(size=384, distance=Distance.COSINE)
)

# Insert vectors with payload
client.upsert(
    collection_name="documents",
    points=[
        PointStruct(
            id=1,
            vector=[0.1, 0.2, ...],  # 384-dim vector
            payload={"title": "Doc 1", "category": "tech"}
        ),
        PointStruct(
            id=2,
            vector=[0.3, 0.4, ...],
            payload={"title": "Doc 2", "category": "science"}
        )
    ]
)

# Search with filtering
results = client.search(
    collection_name="documents",
    query_vector=[0.15, 0.25, ...],
    query_filter={
        "must": [{"key": "category", "match": {"value": "tech"}}]
    },
    limit=10
)

for point in results:
    print(f"ID: {point.id}, Score: {point.score}, Payload: {point.payload}")
```

## Core concepts

### Points - Basic data unit

```python
from qdrant_client.models import PointStruct

# Point = ID + Vector(s) + Payload
point = PointStruct(
    id=123,                              # Integer or UUID string
    vector=[0.1, 0.2, 0.3, ...],        # Dense vector
    payload={                            # Arbitrary JSON metadata
        "title": "Document title",
        "category": "tech",
        "timestamp": 1699900000,
        "tags": ["python", "ml"]
    }
)

# Batch upsert (recommended)
client.upsert(
    collection_name="documents",
    points=[point1, point2, point3],
    wait=True  # Wait for indexing
)
```

### Collections - Vector containers

```python
from qdrant_client.models import VectorParams, Distance, HnswConfigDiff

# Create with HNSW configuration
client.create_collection(
    collection_name="documents",
    vectors_config=VectorParams(
        size=384,                        # Vector dimensions
        distance=Distance.COSINE         # COSINE, EUCLID, DOT, MANHATTAN
    ),
    hnsw_config=HnswConfigDiff(
        m=16,                            # Connections per node (default 16)
        ef_construct=100,                # Build-time accuracy (default 100)
        full_scan_threshold=10000        # Switch to brute force below this
    ),
    on_disk_payload=True                 # Store payload on disk
)

# Collection info
info = client.get_collection("documents")
print(f"Points: {info.points_count}, Vectors: {info.vectors_count}")
```

### Distance metrics

| Metric | Use Case | Range |
|--------|----------|-------|
| `COSINE` | Text embeddings, normalized vectors | 0 to 2 |
| `EUCLID` | Spatial data, image features | 0 to ∞ |
| `DOT` | Recommendations, unnormalized | -∞ to ∞ |
| `MANHATTAN` | Sparse features, discrete data | 0 to ∞ |

## Search operations

### Basic search

```python
# Simple nearest neighbor search
results = client.search(
    collection_name="documents",
    query_vector=[0.1, 0.2, ...],
    limit=10,
    with_payload=True,
    with_vectors=False  # Don't return vectors (faster)
)
```

### Filtered search

```python
from qdrant_client.models import Filter, FieldCondition, MatchValue, Range

# Complex filtering
results = client.search(
    collection_name="documents",
    query_vector=query_embedding,
    query_filter=Filter(
        must=[
            FieldCondition(key="category", match=MatchValue(value="tech")),
            FieldCondition(key="timestamp", range=Range(gte=1699000000))
        ],
        must_not=[
            FieldCondition(key="status", match=MatchValue(value="archived"))
        ]
    ),
    limit=10
)

# Shorthand filter syntax
results = client.search(
    collection_name="documents",
    query_vector=query_embedding,
    query_filter={
        "must": [
            {"key": "category", "match": {"value": "tech"}},
            {"key": "price", "range": {"gte": 10, "lte": 100}}
        ]
    },
    limit=10
)
```

### Batch search

```python
from qdrant_client.models import SearchRequest

# Multiple queries in one request
results = client.search_batch(
    collection_name="documents",
    requests=[
        SearchRequest(vector=[0.1, ...], limit=5),
        SearchRequest(vector=[0.2, ...], limit=5, filter={"must": [...]}),
        SearchRequest(vector=[0.3, ...], limit=10)
    ]
)
```

## RAG integration

### With sentence-transformers

```python
from sentence_transformers import SentenceTransformer
from qdrant_client import QdrantClient
from qdrant_client.models import VectorParams, Distance, PointStruct

# Initialize
encoder = SentenceTransformer("all-MiniLM-L6-v2")
client = QdrantClient(host="localhost", port=6333)

# Create collection
client.create_collection(
    collection_name="knowledge_base",
    vectors_config=VectorParams(size=384, distance=Distance.COSINE)
)

# Index documents
documents = [
    {"id": 1, "text": "Python is a programming language", "source": "wiki"},
    {"id": 2, "text": "Machine learning uses algorithms", "source": "textbook"},
]

points = [
    PointStruct(
        id=doc["id"],
        vector=encoder.encode(doc["text"]).tolist(),
        payload={"text": doc["text"], "source": doc["source"]}
    )
    for doc in documents
]
client.upsert(collection_name="knowledge_base", points=points)

# RAG retrieval
def retrieve(query: str, top_k: int = 5) -> list[dict]:
    query_vector = encoder.encode(query).tolist()
    results = client.search(
        collection_name="knowledge_base",
        query_vector=query_vector,
        limit=top_k
    )
    return [{"text": r.payload["text"], "score": r.score} for r in results]

# Use in RAG pipeline
context = retrieve("What is Python?")
prompt = f"Context: {context}\n\nQuestion: What is Python?"
```

### With LangChain

```python
from langchain_community.vectorstores import Qdrant
from langchain_community.embeddings import HuggingFaceEmbeddings

embeddings = HuggingFaceEmbeddings(model_name="all-MiniLM-L6-v2")
vectorstore = Qdrant.from_documents(documents, embeddings, url="http://localhost:6333", collection_name="docs")
retriever = vectorstore.as_retriever(search_kwargs={"k": 5})
```

### With LlamaIndex

```python
from llama_index.vector_stores.qdrant import QdrantVectorStore
from llama_index.core import VectorStoreIndex, StorageContext

vector_store = QdrantVectorStore(client=client, collection_name="llama_docs")
storage_context = StorageContext.from_defaults(vector_store=vector_store)
index = VectorStoreIndex.from_documents(documents, storage_context=storage_context)
query_engine = index.as_query_engine()
```

## Multi-vector support

### Named vectors (different embedding models)

```python
from qdrant_client.models import VectorParams, Distance

# Collection with multiple vector types
client.create_collection(
    collection_name="hybrid_search",
    vectors_config={
        "dense": VectorParams(size=384, distance=Distance.COSINE),
        "sparse": VectorParams(size=30000, distance=Distance.DOT)
    }
)

# Insert with named vectors
client.upsert(
    collection_name="hybrid_search",
    points=[
        PointStruct(
            id=1,
            vector={
                "dense": dense_embedding,
                "sparse": sparse_embedding
            },
            payload={"text": "document text"}
        )
    ]
)

# Search specific vector
results = client.search(
    collection_name="hybrid_search",
    query_vector=("dense", query_dense),  # Specify which vector
    limit=10
)
```

### Sparse vectors (BM25, SPLADE)

```python
from qdrant_client.models import SparseVectorParams, SparseIndexParams, SparseVector

# Collection with sparse vectors
client.create_collection(
    collection_name="sparse_search",
    vectors_config={},
    sparse_vectors_config={"text": SparseVectorParams(index=SparseIndexParams(on_disk=False))}
)

# Insert sparse vector
client.upsert(
    collection_name="sparse_search",
    points=[PointStruct(id=1, vector={"text": SparseVector(indices=[1, 5, 100], values=[0.5, 0.8, 0.2])}, payload={"text": "document"})]
)
```

## Quantization (memory optimization)

```python
from qdrant_client.models import ScalarQuantization, ScalarQuantizationConfig, ScalarType

# Scalar quantization (4x memory reduction)
client.create_collection(
    collection_name="quantized",
    vectors_config=VectorParams(size=384, distance=Distance.COSINE),
    quantization_config=ScalarQuantization(
        scalar=ScalarQuantizationConfig(
            type=ScalarType.INT8,
            quantile=0.99,        # Clip outliers
            always_ram=True      # Keep quantized in RAM
        )
    )
)

# Search with rescoring
results = client.search(
    collection_name="quantized",
    query_vector=query,
    search_params={"quantization": {"rescore": True}},  # Rescore top results
    limit=10
)
```

## Payload indexing

```python
from qdrant_client.models import PayloadSchemaType

# Create payload index for faster filtering
client.create_payload_index(
    collection_name="documents",
    field_name="category",
    field_schema=PayloadSchemaType.KEYWORD
)

client.create_payload_index(
    collection_name="documents",
    field_name="timestamp",
    field_schema=PayloadSchemaType.INTEGER
)

# Index types: KEYWORD, INTEGER, FLOAT, GEO, TEXT (full-text), BOOL
```

## Production deployment

### Qdrant Cloud

```python
from qdrant_client import QdrantClient

# Connect to Qdrant Cloud
client = QdrantClient(
    url="https://your-cluster.cloud.qdrant.io",
    api_key="your-api-key"
)
```

### Performance tuning

```python
# Optimize for search speed (higher recall)
client.update_collection(
    collection_name="documents",
    hnsw_config=HnswConfigDiff(ef_construct=200, m=32)
)

# Optimize for indexing speed (bulk loads)
client.update_collection(
    collection_name="documents",
    optimizer_config={"indexing_threshold": 20000}
)
```

## Best practices

1. **Batch operations** - Use batch upsert/search for efficiency
2. **Payload indexing** - Index fields used in filters
3. **Quantization** - Enable for large collections (>1M vectors)
4. **Sharding** - Use for collections >10M vectors
5. **On-disk storage** - Enable `on_disk_payload` for large payloads
6. **Connection pooling** - Reuse client instances

## Common issues

**Slow search with filters:**
```python
# Create payload index for filtered fields
client.create_payload_index(
    collection_name="docs",
    field_name="category",
    field_schema=PayloadSchemaType.KEYWORD
)
```

**Out of memory:**
```python
# Enable quantization and on-disk storage
client.create_collection(
    collection_name="large_collection",
    vectors_config=VectorParams(size=384, distance=Distance.COSINE),
    quantization_config=ScalarQuantization(...),
    on_disk_payload=True
)
```

**Connection issues:**
```python
# Use timeout and retry
client = QdrantClient(
    host="localhost",
    port=6333,
    timeout=30,
    prefer_grpc=True  # gRPC for better performance
)
```

## References

- **[Advanced Usage](references/advanced-usage.md)** - Distributed mode, hybrid search, recommendations
- **[Troubleshooting](references/troubleshooting.md)** - Common issues, debugging, performance tuning

## Resources

- **GitHub**: https://github.com/qdrant/qdrant (22k+ stars)
- **Docs**: https://qdrant.tech/documentation/
- **Python Client**: https://github.com/qdrant/qdrant-client
- **Cloud**: https://cloud.qdrant.io
- **Version**: 1.12.0+
- **License**: Apache 2.0


---


## 📚 Reference Materials


### Advanced Usage

# Qdrant Advanced Usage Guide

## Distributed Deployment

### Cluster Setup

Qdrant uses Raft consensus for distributed coordination.

```yaml
# docker-compose.yml for 3-node cluster
version: '3.8'
services:
  qdrant-node-1:
    image: qdrant/qdrant:latest
    ports:
      - "6333:6333"
      - "6334:6334"
      - "6335:6335"
    volumes:
      - ./node1_storage:/qdrant/storage
    environment:
      - QDRANT__CLUSTER__ENABLED=true
      - QDRANT__CLUSTER__P2P__PORT=6335
      - QDRANT__SERVICE__HTTP_PORT=6333
      - QDRANT__SERVICE__GRPC_PORT=6334

  qdrant-node-2:
    image: qdrant/qdrant:latest
    ports:
      - "6343:6333"
      - "6344:6334"
      - "6345:6335"
    volumes:
      - ./node2_storage:/qdrant/storage
    environment:
      - QDRANT__CLUSTER__ENABLED=true
      - QDRANT__CLUSTER__P2P__PORT=6335
      - QDRANT__CLUSTER__BOOTSTRAP=http://qdrant-node-1:6335
    depends_on:
      - qdrant-node-1

  qdrant-node-3:
    image: qdrant/qdrant:latest
    ports:
      - "6353:6333"
      - "6354:6334"
      - "6355:6335"
    volumes:
      - ./node3_storage:/qdrant/storage
    environment:
      - QDRANT__CLUSTER__ENABLED=true
      - QDRANT__CLUSTER__P2P__PORT=6335
      - QDRANT__CLUSTER__BOOTSTRAP=http://qdrant-node-1:6335
    depends_on:
      - qdrant-node-1
```

### Sharding Configuration

```python
from qdrant_client import QdrantClient
from qdrant_client.models import VectorParams, Distance, ShardingMethod

client = QdrantClient(host="localhost", port=6333)

# Create sharded collection
client.create_collection(
    collection_name="large_collection",
    vectors_config=VectorParams(size=384, distance=Distance.COSINE),
    shard_number=6,  # Number of shards
    replication_factor=2,  # Replicas per shard
    write_consistency_factor=1  # Required acks for write
)

# Check cluster status
cluster_info = client.get_cluster_info()
print(f"Peers: {cluster_info.peers}")
print(f"Raft state: {cluster_info.raft_info}")
```

### Replication and Consistency

```python
from qdrant_client.models import WriteOrdering

# Strong consistency write
client.upsert(
    collection_name="critical_data",
    points=points,
    ordering=WriteOrdering.STRONG  # Wait for all replicas
)

# Eventual consistency (faster)
client.upsert(
    collection_name="logs",
    points=points,
    ordering=WriteOrdering.WEAK  # Return after primary ack
)

# Read from specific shard
results = client.search(
    collection_name="documents",
    query_vector=query,
    consistency="majority"  # Read from majority of replicas
)
```

## Hybrid Search

### Dense + Sparse Vectors

Combine semantic (dense) and keyword (sparse) search:

```python
from qdrant_client.models import (
    VectorParams, SparseVectorParams, SparseIndexParams,
    Distance, PointStruct, SparseVector, Prefetch, Query
)

# Create hybrid collection
client.create_collection(
    collection_name="hybrid",
    vectors_config={
        "dense": VectorParams(size=384, distance=Distance.COSINE)
    },
    sparse_vectors_config={
        "sparse": SparseVectorParams(
            index=SparseIndexParams(on_disk=False)
        )
    }
)

# Insert with both vector types
def encode_sparse(text: str) -> SparseVector:
    """Simple BM25-like sparse encoding"""
    from collections import Counter
    tokens = text.lower().split()
    counts = Counter(tokens)
    # Map tokens to indices (use vocabulary in production)
    indices = [hash(t) % 30000 for t in counts.keys()]
    values = list(counts.values())
    return SparseVector(indices=indices, values=values)

client.upsert(
    collection_name="hybrid",
    points=[
        PointStruct(
            id=1,
            vector={
                "dense": dense_encoder.encode("Python programming").tolist(),
                "sparse": encode_sparse("Python programming language code")
            },
            payload={"text": "Python programming language code"}
        )
    ]
)

# Hybrid search with Reciprocal Rank Fusion (RRF)
from qdrant_client.models import FusionQuery

results = client.query_points(
    collection_name="hybrid",
    prefetch=[
        Prefetch(query=dense_query, using="dense", limit=20),
        Prefetch(query=sparse_query, using="sparse", limit=20)
    ],
    query=FusionQuery(fusion="rrf"),  # Combine results
    limit=10
)
```

### Multi-Stage Search

```python
from qdrant_client.models import Prefetch, Query

# Two-stage retrieval: coarse then fine
results = client.query_points(
    collection_name="documents",
    prefetch=[
        Prefetch(
            query=query_vector,
            limit=100,  # Broad first stage
            params={"quantization": {"rescore": False}}  # Fast, approximate
        )
    ],
    query=Query(nearest=query_vector),
    limit=10,
    params={"quantization": {"rescore": True}}  # Accurate reranking
)
```

## Recommendations

### Item-to-Item Recommendations

```python
# Find similar items
recommendations = client.recommend(
    collection_name="products",
    positive=[1, 2, 3],  # IDs user liked
    negative=[4],         # IDs user disliked
    limit=10
)

# With filtering
recommendations = client.recommend(
    collection_name="products",
    positive=[1, 2],
    query_filter={
        "must": [
            {"key": "category", "match": {"value": "electronics"}},
            {"key": "in_stock", "match": {"value": True}}
        ]
    },
    limit=10
)
```

### Lookup from Another Collection

```python
from qdrant_client.models import RecommendStrategy, LookupLocation

# Recommend using vectors from another collection
results = client.recommend(
    collection_name="products",
    positive=[
        LookupLocation(
            collection_name="user_history",
            id="user_123"
        )
    ],
    strategy=RecommendStrategy.AVERAGE_VECTOR,
    limit=10
)
```

## Advanced Filtering

### Nested Payload Filtering

```python
from qdrant_client.models import Filter, FieldCondition, MatchValue, NestedCondition

# Filter on nested objects
results = client.search(
    collection_name="documents",
    query_vector=query,
    query_filter=Filter(
        must=[
            NestedCondition(
                key="metadata",
                filter=Filter(
                    must=[
                        FieldCondition(
                            key="author.name",
                            match=MatchValue(value="John")
                        )
                    ]
                )
            )
        ]
    ),
    limit=10
)
```

### Geo Filtering

```python
from qdrant_client.models import FieldCondition, GeoRadius, GeoPoint

# Find within radius
results = client.search(
    collection_name="locations",
    query_vector=query,
    query_filter=Filter(
        must=[
            FieldCondition(
                key="location",
                geo_radius=GeoRadius(
                    center=GeoPoint(lat=40.7128, lon=-74.0060),
                    radius=5000  # meters
                )
            )
        ]
    ),
    limit=10
)

# Geo bounding box
from qdrant_client.models import GeoBoundingBox

results = client.search(
    collection_name="locations",
    query_vector=query,
    query_filter=Filter(
        must=[
            FieldCondition(
                key="location",
                geo_bounding_box=GeoBoundingBox(
                    top_left=GeoPoint(lat=40.8, lon=-74.1),
                    bottom_right=GeoPoint(lat=40.6, lon=-73.9)
                )
            )
        ]
    ),
    limit=10
)
```

### Full-Text Search

```python
from qdrant_client.models import TextIndexParams, TokenizerType

# Create text index
client.create_payload_index(
    collection_name="documents",
    field_name="content",
    field_schema=TextIndexParams(
        type="text",
        tokenizer=TokenizerType.WORD,
        min_token_len=2,
        max_token_len=15,
        lowercase=True
    )
)

# Full-text filter
from qdrant_client.models import MatchText

results = client.search(
    collection_name="documents",
    query_vector=query,
    query_filter=Filter(
        must=[
            FieldCondition(
                key="content",
                match=MatchText(text="machine learning")
            )
        ]
    ),
    limit=10
)
```

## Quantization Strategies

### Scalar Quantization (INT8)

```python
from qdrant_client.models import ScalarQuantization, ScalarQuantizationConfig, ScalarType

# ~4x memory reduction, minimal accuracy loss
client.create_collection(
    collection_name="scalar_quantized",
    vectors_config=VectorParams(size=384, distance=Distance.COSINE),
    quantization_config=ScalarQuantization(
        scalar=ScalarQuantizationConfig(
            type=ScalarType.INT8,
            quantile=0.99,       # Clip extreme values
            always_ram=True     # Keep quantized vectors in RAM
        )
    )
)
```

### Product Quantization

```python
from qdrant_client.models import ProductQuantization, ProductQuantizationConfig, CompressionRatio

# ~16x memory reduction, some accuracy loss
client.create_collection(
    collection_name="product_quantized",
    vectors_config=VectorParams(size=384, distance=Distance.COSINE),
    quantization_config=ProductQuantization(
        product=ProductQuantizationConfig(
            compression=CompressionRatio.X16,
            always_ram=True
        )
    )
)
```

### Binary Quantization

```python
from qdrant_client.models import BinaryQuantization, BinaryQuantizationConfig

# ~32x memory reduction, requires oversampling
client.create_collection(
    collection_name="binary_quantized",
    vectors_config=VectorParams(size=384, distance=Distance.COSINE),
    quantization_config=BinaryQuantization(
        binary=BinaryQuantizationConfig(always_ram=True)
    )
)

# Search with oversampling
results = client.search(
    collection_name="binary_quantized",
    query_vector=query,
    search_params={
        "quantization": {
            "rescore": True,
            "oversampling": 2.0  # Retrieve 2x candidates, rescore
        }
    },
    limit=10
)
```

## Snapshots and Backups

### Create Snapshot

```python
# Create collection snapshot
snapshot_info = client.create_snapshot(collection_name="documents")
print(f"Snapshot: {snapshot_info.name}")

# List snapshots
snapshots = client.list_snapshots(collection_name="documents")
for s in snapshots:
    print(f"{s.name}: {s.size} bytes")

# Full storage snapshot
full_snapshot = client.create_full_snapshot()
```

### Restore from Snapshot

```python
# Download snapshot
client.download_snapshot(
    collection_name="documents",
    snapshot_name="documents-2024-01-01.snapshot",
    target_path="./backup/"
)

# Restore (via REST API)
import requests

response = requests.put(
    "http://localhost:6333/collections/documents/snapshots/recover",
    json={"location": "file:///backup/documents-2024-01-01.snapshot"}
)
```

## Collection Aliases

```python
# Create alias
client.update_collection_aliases(
    change_aliases_operations=[
        {"create_alias": {"alias_name": "production", "collection_name": "documents_v2"}}
    ]
)

# Blue-green deployment
# 1. Create new collection with updates
client.create_collection(collection_name="documents_v3", ...)

# 2. Populate new collection
client.upsert(collection_name="documents_v3", points=new_points)

# 3. Atomic switch
client.update_collection_aliases(
    change_aliases_operations=[
        {"delete_alias": {"alias_name": "production"}},
        {"create_alias": {"alias_name": "production", "collection_name": "documents_v3"}}
    ]
)

# Search via alias
results = client.search(collection_name="production", query_vector=query, limit=10)
```

## Scroll and Iteration

### Scroll Through All Points

```python
# Paginated iteration
offset = None
all_points = []

while True:
    results, offset = client.scroll(
        collection_name="documents",
        limit=100,
        offset=offset,
        with_payload=True,
        with_vectors=False
    )
    all_points.extend(results)

    if offset is None:
        break

print(f"Total points: {len(all_points)}")
```

### Filtered Scroll

```python
# Scroll with filter
results, _ = client.scroll(
    collection_name="documents",
    scroll_filter=Filter(
        must=[
            FieldCondition(key="status", match=MatchValue(value="active"))
        ]
    ),
    limit=1000
)
```

## Async Client

```python
import asyncio
from qdrant_client import AsyncQdrantClient

async def main():
    client = AsyncQdrantClient(host="localhost", port=6333)

    # Async operations
    await client.create_collection(
        collection_name="async_docs",
        vectors_config=VectorParams(size=384, distance=Distance.COSINE)
    )

    await client.upsert(
        collection_name="async_docs",
        points=points
    )

    results = await client.search(
        collection_name="async_docs",
        query_vector=query,
        limit=10
    )

    return results

results = asyncio.run(main())
```

## gRPC Client

```python
from qdrant_client import QdrantClient

# Prefer gRPC for better performance
client = QdrantClient(
    host="localhost",
    port=6333,
    grpc_port=6334,
    prefer_grpc=True  # Use gRPC when available
)

# gRPC-only client
from qdrant_client import QdrantClient

client = QdrantClient(
    host="localhost",
    grpc_port=6334,
    prefer_grpc=True,
    https=False
)
```

## Multitenancy

### Payload-Based Isolation

```python
# Single collection, filter by tenant
client.upsert(
    collection_name="multi_tenant",
    points=[
        PointStruct(
            id=1,
            vector=embedding,
            payload={"tenant_id": "tenant_a", "text": "..."}
        )
    ]
)

# Search within tenant
results = client.search(
    collection_name="multi_tenant",
    query_vector=query,
    query_filter=Filter(
        must=[FieldCondition(key="tenant_id", match=MatchValue(value="tenant_a"))]
    ),
    limit=10
)
```

### Collection-Per-Tenant

```python
# Create tenant collection
def create_tenant_collection(tenant_id: str):
    client.create_collection(
        collection_name=f"tenant_{tenant_id}",
        vectors_config=VectorParams(size=384, distance=Distance.COSINE)
    )

# Search tenant collection
def search_tenant(tenant_id: str, query_vector: list, limit: int = 10):
    return client.search(
        collection_name=f"tenant_{tenant_id}",
        query_vector=query_vector,
        limit=limit
    )
```

## Performance Monitoring

### Collection Statistics

```python
# Collection info
info = client.get_collection("documents")
print(f"Points: {info.points_count}")
print(f"Indexed vectors: {info.indexed_vectors_count}")
print(f"Segments: {len(info.segments)}")
print(f"Status: {info.status}")

# Detailed segment info
for i, segment in enumerate(info.segments):
    print(f"Segment {i}: {segment}")
```

### Telemetry

```python
# Get telemetry data
telemetry = client.get_telemetry()
print(f"Collections: {telemetry.collections}")
print(f"Operations: {telemetry.operations}")
```




### Troubleshooting

# Qdrant Troubleshooting Guide

## Installation Issues

### Docker Issues

**Error**: `Cannot connect to Docker daemon`

**Fix**:
```bash
# Start Docker daemon
sudo systemctl start docker

# Or use Docker Desktop on Mac/Windows
open -a Docker
```

**Error**: `Port 6333 already in use`

**Fix**:
```bash
# Find process using port
lsof -i :6333

# Kill process or use different port
docker run -p 6334:6333 qdrant/qdrant
```

### Python Client Issues

**Error**: `ModuleNotFoundError: No module named 'qdrant_client'`

**Fix**:
```bash
pip install qdrant-client

# With specific version
pip install qdrant-client>=1.12.0
```

**Error**: `grpc._channel._InactiveRpcError`

**Fix**:
```bash
# Install with gRPC support
pip install 'qdrant-client[grpc]'

# Or disable gRPC
client = QdrantClient(host="localhost", port=6333, prefer_grpc=False)
```

## Connection Issues

### Cannot Connect to Server

**Error**: `ConnectionRefusedError: [Errno 111] Connection refused`

**Solutions**:

1. **Check server is running**:
```bash
docker ps | grep qdrant
curl http://localhost:6333/healthz
```

2. **Verify port binding**:
```bash
# Check listening ports
netstat -tlnp | grep 6333

# Docker port mapping
docker port <container_id>
```

3. **Use correct host**:
```python
# Docker on Linux
client = QdrantClient(host="localhost", port=6333)

# Docker on Mac/Windows with networking issues
client = QdrantClient(host="127.0.0.1", port=6333)

# Inside Docker network
client = QdrantClient(host="qdrant", port=6333)
```

### Timeout Errors

**Error**: `TimeoutError: Connection timed out`

**Fix**:
```python
# Increase timeout
client = QdrantClient(
    host="localhost",
    port=6333,
    timeout=60  # seconds
)

# For large operations
client.upsert(
    collection_name="documents",
    points=large_batch,
    wait=False  # Don't wait for indexing
)
```

### SSL/TLS Errors

**Error**: `ssl.SSLCertVerificationError`

**Fix**:
```python
# Qdrant Cloud
client = QdrantClient(
    url="https://cluster.cloud.qdrant.io",
    api_key="your-api-key"
)

# Self-signed certificate
client = QdrantClient(
    host="localhost",
    port=6333,
    https=True,
    verify=False  # Disable verification (not recommended for production)
)
```

## Collection Issues

### Collection Already Exists

**Error**: `ValueError: Collection 'documents' already exists`

**Fix**:
```python
# Check before creating
collections = client.get_collections().collections
names = [c.name for c in collections]

if "documents" not in names:
    client.create_collection(...)

# Or recreate
client.recreate_collection(
    collection_name="documents",
    vectors_config=VectorParams(size=384, distance=Distance.COSINE)
)
```

### Collection Not Found

**Error**: `NotFoundException: Collection 'docs' not found`

**Fix**:
```python
# List available collections
collections = client.get_collections()
print([c.name for c in collections.collections])

# Check exact name (case-sensitive)
try:
    info = client.get_collection("documents")
except Exception as e:
    print(f"Collection not found: {e}")
```

### Vector Dimension Mismatch

**Error**: `ValueError: Vector dimension mismatch. Expected 384, got 768`

**Fix**:
```python
# Check collection config
info = client.get_collection("documents")
print(f"Expected dimension: {info.config.params.vectors.size}")

# Recreate with correct dimension
client.recreate_collection(
    collection_name="documents",
    vectors_config=VectorParams(size=768, distance=Distance.COSINE)  # Match your embeddings
)
```

## Search Issues

### Empty Search Results

**Problem**: Search returns empty results.

**Solutions**:

1. **Verify data exists**:
```python
info = client.get_collection("documents")
print(f"Points: {info.points_count}")

# Scroll to check data
points, _ = client.scroll(
    collection_name="documents",
    limit=10,
    with_payload=True
)
print(points)
```

2. **Check vector format**:
```python
# Must be list of floats
query_vector = embedding.tolist()  # Convert numpy to list

# Check dimensions
print(f"Query dimension: {len(query_vector)}")
```

3. **Verify filter conditions**:
```python
# Test without filter first
results = client.search(
    collection_name="documents",
    query_vector=query,
    limit=10
    # No filter
)

# Then add filter incrementally
```

### Slow Search Performance

**Problem**: Search takes too long.

**Solutions**:

1. **Create payload indexes**:
```python
# Index fields used in filters
client.create_payload_index(
    collection_name="documents",
    field_name="category",
    field_schema="keyword"
)
```

2. **Enable quantization**:
```python
client.update_collection(
    collection_name="documents",
    quantization_config=ScalarQuantization(
        scalar=ScalarQuantizationConfig(type=ScalarType.INT8)
    )
)
```

3. **Tune HNSW parameters**:
```python
# Faster search (less accurate)
client.update_collection(
    collection_name="documents",
    hnsw_config=HnswConfigDiff(ef_construct=64, m=8)
)

# Use ef search parameter
results = client.search(
    collection_name="documents",
    query_vector=query,
    search_params={"hnsw_ef": 64},  # Lower = faster
    limit=10
)
```

4. **Use gRPC**:
```python
client = QdrantClient(
    host="localhost",
    port=6333,
    grpc_port=6334,
    prefer_grpc=True
)
```

### Inconsistent Results

**Problem**: Same query returns different results.

**Solutions**:

1. **Wait for indexing**:
```python
client.upsert(
    collection_name="documents",
    points=points,
    wait=True  # Wait for index update
)
```

2. **Check replication consistency**:
```python
# Strong consistency read
results = client.search(
    collection_name="documents",
    query_vector=query,
    consistency="all"  # Read from all replicas
)
```

## Upsert Issues

### Batch Upsert Fails

**Error**: `PayloadError: Payload too large`

**Fix**:
```python
# Split into smaller batches
def batch_upsert(client, collection, points, batch_size=100):
    for i in range(0, len(points), batch_size):
        batch = points[i:i + batch_size]
        client.upsert(
            collection_name=collection,
            points=batch,
            wait=True
        )

batch_upsert(client, "documents", large_points_list)
```

### Invalid Point ID

**Error**: `ValueError: Invalid point ID`

**Fix**:
```python
# Valid ID types: int or UUID string
from uuid import uuid4

# Integer ID
PointStruct(id=123, vector=vec, payload={})

# UUID string
PointStruct(id=str(uuid4()), vector=vec, payload={})

# NOT valid
PointStruct(id="custom-string-123", ...)  # Use UUID format
```

### Payload Validation Errors

**Error**: `ValidationError: Invalid payload`

**Fix**:
```python
# Ensure JSON-serializable payload
import json

payload = {
    "title": "Document",
    "count": 42,
    "tags": ["a", "b"],
    "nested": {"key": "value"}
}

# Validate before upsert
json.dumps(payload)  # Should not raise

# Avoid non-serializable types
# NOT valid: datetime, numpy arrays, custom objects
payload = {
    "timestamp": datetime.now().isoformat(),  # Convert to string
    "vector": embedding.tolist()  # Convert numpy to list
}
```

## Memory Issues

### Out of Memory

**Error**: `MemoryError` or container killed

**Solutions**:

1. **Enable on-disk storage**:
```python
client.create_collection(
    collection_name="large_collection",
    vectors_config=VectorParams(size=384, distance=Distance.COSINE),
    on_disk_payload=True,  # Store payloads on disk
    hnsw_config=HnswConfigDiff(on_disk=True)  # Store HNSW on disk
)
```

2. **Use quantization**:
```python
# 4x memory reduction
client.update_collection(
    collection_name="large_collection",
    quantization_config=ScalarQuantization(
        scalar=ScalarQuantizationConfig(
            type=ScalarType.INT8,
            always_ram=False  # Keep on disk
        )
    )
)
```

3. **Increase Docker memory**:
```bash
docker run -m 8g -p 6333:6333 qdrant/qdrant
```

4. **Configure Qdrant storage**:
```yaml
# config.yaml
storage:
  performance:
    max_search_threads: 2
  optimizers:
    memmap_threshold_kb: 20000
```

### High Memory Usage During Indexing

**Fix**:
```python
# Increase indexing threshold for bulk loads
client.update_collection(
    collection_name="documents",
    optimizer_config={
        "indexing_threshold": 50000  # Delay indexing
    }
)

# Bulk insert
client.upsert(collection_name="documents", points=all_points, wait=False)

# Then optimize
client.update_collection(
    collection_name="documents",
    optimizer_config={
        "indexing_threshold": 10000  # Resume normal indexing
    }
)
```

## Cluster Issues

### Node Not Joining Cluster

**Problem**: New node fails to join cluster.

**Fix**:
```bash
# Check network connectivity
docker exec qdrant-node-2 ping qdrant-node-1

# Verify bootstrap URL
docker logs qdrant-node-2 | grep bootstrap

# Check Raft state
curl http://localhost:6333/cluster
```

### Split Brain

**Problem**: Cluster has inconsistent state.

**Fix**:
```bash
# Force leader election
curl -X POST http://localhost:6333/cluster/recover

# Or restart minority nodes
docker restart qdrant-node-2 qdrant-node-3
```

### Replication Lag

**Problem**: Replicas fall behind.

**Fix**:
```python
# Check collection status
info = client.get_collection("documents")
print(f"Status: {info.status}")

# Use strong consistency for critical writes
client.upsert(
    collection_name="documents",
    points=points,
    ordering=WriteOrdering.STRONG
)
```

## Performance Tuning

### Benchmark Configuration

```python
import time
import numpy as np

def benchmark_search(client, collection, n_queries=100, dimension=384):
    # Generate random queries
    queries = [np.random.rand(dimension).tolist() for _ in range(n_queries)]

    # Warmup
    for q in queries[:10]:
        client.search(collection_name=collection, query_vector=q, limit=10)

    # Benchmark
    start = time.perf_counter()
    for q in queries:
        client.search(collection_name=collection, query_vector=q, limit=10)
    elapsed = time.perf_counter() - start

    print(f"QPS: {n_queries / elapsed:.2f}")
    print(f"Latency: {elapsed / n_queries * 1000:.2f}ms")

benchmark_search(client, "documents")
```

### Optimal HNSW Parameters

```python
# High recall (slower)
client.create_collection(
    collection_name="high_recall",
    vectors_config=VectorParams(size=384, distance=Distance.COSINE),
    hnsw_config=HnswConfigDiff(
        m=32,              # More connections
        ef_construct=200   # Higher build quality
    )
)

# High speed (lower recall)
client.create_collection(
    collection_name="high_speed",
    vectors_config=VectorParams(size=384, distance=Distance.COSINE),
    hnsw_config=HnswConfigDiff(
        m=8,               # Fewer connections
        ef_construct=64    # Lower build quality
    )
)

# Balanced
client.create_collection(
    collection_name="balanced",
    vectors_config=VectorParams(size=384, distance=Distance.COSINE),
    hnsw_config=HnswConfigDiff(
        m=16,              # Default
        ef_construct=100   # Default
    )
)
```

## Debugging Tips

### Enable Verbose Logging

```python
import logging

logging.basicConfig(level=logging.DEBUG)
logging.getLogger("qdrant_client").setLevel(logging.DEBUG)
```

### Check Server Logs

```bash
# Docker logs
docker logs -f qdrant

# With timestamps
docker logs --timestamps qdrant

# Last 100 lines
docker logs --tail 100 qdrant
```

### Inspect Collection State

```python
# Collection info
info = client.get_collection("documents")
print(f"Status: {info.status}")
print(f"Points: {info.points_count}")
print(f"Segments: {len(info.segments)}")
print(f"Config: {info.config}")

# Sample points
points, _ = client.scroll(
    collection_name="documents",
    limit=5,
    with_payload=True,
    with_vectors=True
)
for p in points:
    print(f"ID: {p.id}, Payload: {p.payload}")
```

### Test Connection

```python
def test_connection(host="localhost", port=6333):
    try:
        client = QdrantClient(host=host, port=port, timeout=5)
        collections = client.get_collections()
        print(f"Connected! Collections: {len(collections.collections)}")
        return True
    except Exception as e:
        print(f"Connection failed: {e}")
        return False

test_connection()
```

## Getting Help

1. **Documentation**: https://qdrant.tech/documentation/
2. **GitHub Issues**: https://github.com/qdrant/qdrant/issues
3. **Discord**: https://discord.gg/qdrant
4. **Stack Overflow**: Tag `qdrant`

### Reporting Issues

Include:
- Qdrant version: `curl http://localhost:6333/`
- Python client version: `pip show qdrant-client`
- Full error traceback
- Minimal reproducible code
- Collection configuration




---

## 🚀 Usage

**Reference this template:** `@skill-qdrant-vector-search.md`


**Platform-specific:**
- **GitHub Copilot**: Add to `.github/copilot-instructions.md`
- **Augment Code**: Use `aug context add` command
- **Cursor/Windsurf**: Reference in chat with `@filename`
- **Claude**: Add to Project Knowledge
- **ChatGPT**: Add to Custom GPT configuration
