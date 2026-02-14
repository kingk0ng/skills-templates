---
id: skill-llamaindex
type: skill
name: llamaindex
description: Data framework for building LLM applications with RAG. Specializes in
  document ingestion (300+ connectors), indexing, and querying. Features vector indices,
  query engines, agents, and multi-modal support. Use for document Q&A, chatbots,
  knowledge retrieval, or building RAG pipelines. Best for data-centric LLM applications.
category: ai-research
complexity: medium
keywords:
- api
- database
- github
- performance
- python
- react
capabilities: []
token_estimate: 1921
has_references: true
reference_count: 3
---

<!-- Converted from Claude Skill Template -->
<!-- Complexity: Medium -->
<!-- Estimated Tokens: ~1,921 -->
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




# llamaindex

> Data framework for building LLM applications with RAG. Specializes in document ingestion (300+ connectors), indexing, and querying. Features vector indices, query engines, agents, and multi-modal support. Use for document Q&A, chatbots, knowledge retrieval, or building RAG pipelines. Best for data-centric LLM applications.

# LlamaIndex - Data Framework for LLM Applications

The leading framework for connecting LLMs with your data.

## When to use LlamaIndex

**Use LlamaIndex when:**
- Building RAG (retrieval-augmented generation) applications
- Need document question-answering over private data
- Ingesting data from multiple sources (300+ connectors)
- Creating knowledge bases for LLMs
- Building chatbots with enterprise data
- Need structured data extraction from documents

**Metrics**:
- **45,100+ GitHub stars**
- **23,000+ repositories** use LlamaIndex
- **300+ data connectors** (LlamaHub)
- **1,715+ contributors**
- **v0.14.7** (stable)

**Use alternatives instead**:
- **LangChain**: More general-purpose, better for agents
- **Haystack**: Production search pipelines
- **txtai**: Lightweight semantic search
- **Chroma**: Just need vector storage

## Quick start

### Installation

```bash
# Starter package (recommended)
pip install llama-index

# Or minimal core + specific integrations
pip install llama-index-core
pip install llama-index-llms-openai
pip install llama-index-embeddings-openai
```

### 5-line RAG example

```python
from llama_index.core import VectorStoreIndex, SimpleDirectoryReader

# Load documents
documents = SimpleDirectoryReader("data").load_data()

# Create index
index = VectorStoreIndex.from_documents(documents)

# Query
query_engine = index.as_query_engine()
response = query_engine.query("What did the author do growing up?")
print(response)
```

## Core concepts

### 1. Data connectors - Load documents

```python
from llama_index.core import SimpleDirectoryReader, Document
from llama_index.readers.web import SimpleWebPageReader
from llama_index.readers.github import GithubRepositoryReader

# Directory of files
documents = SimpleDirectoryReader("./data").load_data()

# Web pages
reader = SimpleWebPageReader()
documents = reader.load_data(["https://example.com"])

# GitHub repository
reader = GithubRepositoryReader(owner="user", repo="repo")
documents = reader.load_data(branch="main")

# Manual document creation
doc = Document(
    text="This is the document content",
    metadata={"source": "manual", "date": "2025-01-01"}
)
```

### 2. Indices - Structure data

```python
from llama_index.core import VectorStoreIndex, ListIndex, TreeIndex

# Vector index (most common - semantic search)
vector_index = VectorStoreIndex.from_documents(documents)

# List index (sequential scan)
list_index = ListIndex.from_documents(documents)

# Tree index (hierarchical summary)
tree_index = TreeIndex.from_documents(documents)

# Save index
index.storage_context.persist(persist_dir="./storage")

# Load index
from llama_index.core import load_index_from_storage, StorageContext
storage_context = StorageContext.from_defaults(persist_dir="./storage")
index = load_index_from_storage(storage_context)
```

### 3. Query engines - Ask questions

```python
# Basic query
query_engine = index.as_query_engine()
response = query_engine.query("What is the main topic?")
print(response)

# Streaming response
query_engine = index.as_query_engine(streaming=True)
response = query_engine.query("Explain quantum computing")
for text in response.response_gen:
    print(text, end="", flush=True)

# Custom configuration
query_engine = index.as_query_engine(
    similarity_top_k=3,          # Return top 3 chunks
    response_mode="compact",     # Or "tree_summarize", "simple_summarize"
    verbose=True
)
```

### 4. Retrievers - Find relevant chunks

```python
# Vector retriever
retriever = index.as_retriever(similarity_top_k=5)
nodes = retriever.retrieve("machine learning")

# With filtering
retriever = index.as_retriever(
    similarity_top_k=3,
    filters={"metadata.category": "tutorial"}
)

# Custom retriever
from llama_index.core.retrievers import BaseRetriever

class CustomRetriever(BaseRetriever):
    def _retrieve(self, query_bundle):
        # Your custom retrieval logic
        return nodes
```

## Agents with tools

### Basic agent

```python
from llama_index.core.agent import FunctionAgent
from llama_index.llms.openai import OpenAI

# Define tools
def multiply(a: int, b: int) -> int:
    """Multiply two numbers."""
    return a * b

def add(a: int, b: int) -> int:
    """Add two numbers."""
    return a + b

# Create agent
llm = OpenAI(model="gpt-4o")
agent = FunctionAgent.from_tools(
    tools=[multiply, add],
    llm=llm,
    verbose=True
)

# Use agent
response = agent.chat("What is 25 * 17 + 142?")
print(response)
```

### RAG agent (document search + tools)

```python
from llama_index.core.tools import QueryEngineTool

# Create index as before
index = VectorStoreIndex.from_documents(documents)

# Wrap query engine as tool
query_tool = QueryEngineTool.from_defaults(
    query_engine=index.as_query_engine(),
    name="python_docs",
    description="Useful for answering questions about Python programming"
)

# Agent with document search + calculator
agent = FunctionAgent.from_tools(
    tools=[query_tool, multiply, add],
    llm=llm
)

# Agent decides when to search docs vs calculate
response = agent.chat("According to the docs, what is Python used for?")
```

## Advanced RAG patterns

### Chat engine (conversational)

```python
from llama_index.core.chat_engine import CondensePlusContextChatEngine

# Chat with memory
chat_engine = index.as_chat_engine(
    chat_mode="condense_plus_context",  # Or "context", "react"
    verbose=True
)

# Multi-turn conversation
response1 = chat_engine.chat("What is Python?")
response2 = chat_engine.chat("Can you give examples?")  # Remembers context
response3 = chat_engine.chat("What about web frameworks?")
```

### Metadata filtering

```python
from llama_index.core.vector_stores import MetadataFilters, ExactMatchFilter

# Filter by metadata
filters = MetadataFilters(
    filters=[
        ExactMatchFilter(key="category", value="tutorial"),
        ExactMatchFilter(key="difficulty", value="beginner")
    ]
)

retriever = index.as_retriever(
    similarity_top_k=3,
    filters=filters
)

query_engine = index.as_query_engine(filters=filters)
```

### Structured output

```python
from pydantic import BaseModel
from llama_index.core.output_parsers import PydanticOutputParser

class Summary(BaseModel):
    title: str
    main_points: list[str]
    conclusion: str

# Get structured response
output_parser = PydanticOutputParser(output_cls=Summary)
query_engine = index.as_query_engine(output_parser=output_parser)

response = query_engine.query("Summarize the document")
summary = response  # Pydantic model
print(summary.title, summary.main_points)
```

## Data ingestion patterns

### Multiple file types

```python
# Load all supported formats
documents = SimpleDirectoryReader(
    "./data",
    recursive=True,
    required_exts=[".pdf", ".docx", ".txt", ".md"]
).load_data()
```

### Web scraping

```python
from llama_index.readers.web import BeautifulSoupWebReader

reader = BeautifulSoupWebReader()
documents = reader.load_data(urls=[
    "https://docs.python.org/3/tutorial/",
    "https://docs.python.org/3/library/"
])
```

### Database

```python
from llama_index.readers.database import DatabaseReader

reader = DatabaseReader(
    sql_database_uri="postgresql://user:pass@localhost/db"
)
documents = reader.load_data(query="SELECT * FROM articles")
```

### API endpoints

```python
from llama_index.readers.json import JSONReader

reader = JSONReader()
documents = reader.load_data("https://api.example.com/data.json")
```

## Vector store integrations

### Chroma (local)

```python
from llama_index.vector_stores.chroma import ChromaVectorStore
import chromadb

# Initialize Chroma
db = chromadb.PersistentClient(path="./chroma_db")
collection = db.get_or_create_collection("my_collection")

# Create vector store
vector_store = ChromaVectorStore(chroma_collection=collection)

# Use in index
from llama_index.core import StorageContext
storage_context = StorageContext.from_defaults(vector_store=vector_store)
index = VectorStoreIndex.from_documents(documents, storage_context=storage_context)
```

### Pinecone (cloud)

```python
from llama_index.vector_stores.pinecone import PineconeVectorStore
import pinecone

# Initialize Pinecone
pinecone.init(api_key="your-key", environment="us-west1-gcp")
pinecone_index = pinecone.Index("my-index")

# Create vector store
vector_store = PineconeVectorStore(pinecone_index=pinecone_index)
storage_context = StorageContext.from_defaults(vector_store=vector_store)

index = VectorStoreIndex.from_documents(documents, storage_context=storage_context)
```

### FAISS (fast)

```python
from llama_index.vector_stores.faiss import FaissVectorStore
import faiss

# Create FAISS index
d = 1536  # Dimension of embeddings
faiss_index = faiss.IndexFlatL2(d)

vector_store = FaissVectorStore(faiss_index=faiss_index)
storage_context = StorageContext.from_defaults(vector_store=vector_store)

index = VectorStoreIndex.from_documents(documents, storage_context=storage_context)
```

## Customization

### Custom LLM

```python
from llama_index.llms.anthropic import Anthropic
from llama_index.core import Settings

# Set global LLM
Settings.llm = Anthropic(model="claude-sonnet-4-5-20250929")

# Now all queries use Anthropic
query_engine = index.as_query_engine()
```

### Custom embeddings

```python
from llama_index.embeddings.huggingface import HuggingFaceEmbedding

# Use HuggingFace embeddings
Settings.embed_model = HuggingFaceEmbedding(
    model_name="sentence-transformers/all-mpnet-base-v2"
)

index = VectorStoreIndex.from_documents(documents)
```

### Custom prompt templates

```python
from llama_index.core import PromptTemplate

qa_prompt = PromptTemplate(
    "Context: {context_str}\n"
    "Question: {query_str}\n"
    "Answer the question based only on the context. "
    "If the answer is not in the context, say 'I don't know'.\n"
    "Answer: "
)

query_engine = index.as_query_engine(text_qa_template=qa_prompt)
```

## Multi-modal RAG

### Image + text

```python
from llama_index.core import SimpleDirectoryReader
from llama_index.multi_modal_llms.openai import OpenAIMultiModal

# Load images and documents
documents = SimpleDirectoryReader(
    "./data",
    required_exts=[".jpg", ".png", ".pdf"]
).load_data()

# Multi-modal index
index = VectorStoreIndex.from_documents(documents)

# Query with multi-modal LLM
multi_modal_llm = OpenAIMultiModal(model="gpt-4o")
query_engine = index.as_query_engine(llm=multi_modal_llm)

response = query_engine.query("What is in the diagram on page 3?")
```

## Evaluation

### Response quality

```python
from llama_index.core.evaluation import RelevancyEvaluator, FaithfulnessEvaluator

# Evaluate relevance
relevancy = RelevancyEvaluator()
result = relevancy.evaluate_response(
    query="What is Python?",
    response=response
)
print(f"Relevancy: {result.passing}")

# Evaluate faithfulness (no hallucination)
faithfulness = FaithfulnessEvaluator()
result = faithfulness.evaluate_response(
    query="What is Python?",
    response=response
)
print(f"Faithfulness: {result.passing}")
```

## Best practices

1. **Use vector indices for most cases** - Best performance
2. **Save indices to disk** - Avoid re-indexing
3. **Chunk documents properly** - 512-1024 tokens optimal
4. **Add metadata** - Enables filtering and tracking
5. **Use streaming** - Better UX for long responses
6. **Enable verbose during dev** - See retrieval process
7. **Evaluate responses** - Check relevance and faithfulness
8. **Use chat engine for conversations** - Built-in memory
9. **Persist storage** - Don't lose your index
10. **Monitor costs** - Track embedding and LLM usage

## Common patterns

### Document Q&A system

```python
# Complete RAG pipeline
documents = SimpleDirectoryReader("docs").load_data()
index = VectorStoreIndex.from_documents(documents)
index.storage_context.persist(persist_dir="./storage")

# Query
query_engine = index.as_query_engine(
    similarity_top_k=3,
    response_mode="compact",
    verbose=True
)
response = query_engine.query("What is the main topic?")
print(response)
print(f"Sources: {[node.metadata['file_name'] for node in response.source_nodes]}")
```

### Chatbot with memory

```python
# Conversational interface
chat_engine = index.as_chat_engine(
    chat_mode="condense_plus_context",
    verbose=True
)

# Multi-turn chat
while True:
    user_input = input("You: ")
    if user_input.lower() == "quit":
        break
    response = chat_engine.chat(user_input)
    print(f"Bot: {response}")
```

## Performance benchmarks

| Operation | Latency | Notes |
|-----------|---------|-------|
| Index 100 docs | ~10-30s | One-time, can persist |
| Query (vector) | ~0.5-2s | Retrieval + LLM |
| Streaming query | ~0.5s first token | Better UX |
| Agent with tools | ~3-8s | Multiple tool calls |

## LlamaIndex vs LangChain

| Feature | LlamaIndex | LangChain |
|---------|------------|-----------|
| **Best for** | RAG, document Q&A | Agents, general LLM apps |
| **Data connectors** | 300+ (LlamaHub) | 100+ |
| **RAG focus** | Core feature | One of many |
| **Learning curve** | Easier for RAG | Steeper |
| **Customization** | High | Very high |
| **Documentation** | Excellent | Good |

**Use LlamaIndex when:**
- Your primary use case is RAG
- Need many data connectors
- Want simpler API for document Q&A
- Building knowledge retrieval system

**Use LangChain when:**
- Building complex agents
- Need more general-purpose tools
- Want more flexibility
- Complex multi-step workflows

## References

- **[Query Engines Guide](references/query_engines.md)** - Query modes, customization, streaming
- **[Agents Guide](references/agents.md)** - Tool creation, RAG agents, multi-step reasoning
- **[Data Connectors Guide](references/data_connectors.md)** - 300+ connectors, custom loaders

## Resources

- **GitHub**: https://github.com/run-llama/llama_index ⭐ 45,100+
- **Docs**: https://developers.llamaindex.ai/python/framework/
- **LlamaHub**: https://llamahub.ai (data connectors)
- **LlamaCloud**: https://cloud.llamaindex.ai (enterprise)
- **Discord**: https://discord.gg/dGcwcsnxhU
- **Version**: 0.14.7+
- **License**: MIT




---


## 📚 Reference Materials


### Agents

# LlamaIndex Agents Guide

Building agents with tools and RAG capabilities.

## Basic agent

```python
from llama_index.core.agent import FunctionAgent
from llama_index.llms.openai import OpenAI

def multiply(a: int, b: int) -> int:
    """Multiply two numbers."""
    return a * b

llm = OpenAI(model="gpt-4o")
agent = FunctionAgent.from_tools(
    tools=[multiply],
    llm=llm,
    verbose=True
)

response = agent.chat("What is 25 * 17?")
```

## RAG agent

```python
from llama_index.core.tools import QueryEngineTool

# Create query engine as tool
index = VectorStoreIndex.from_documents(documents)

query_tool = QueryEngineTool.from_defaults(
    query_engine=index.as_query_engine(),
    name="python_docs",
    description="Useful for Python programming questions"
)

# Agent with RAG + calculator
agent = FunctionAgent.from_tools(
    tools=[query_tool, multiply],
    llm=llm
)

response = agent.chat("According to the docs, what is Python?")
```

## Multi-document agent

```python
# Multiple knowledge bases
python_tool = QueryEngineTool.from_defaults(
    query_engine=python_index.as_query_engine(),
    name="python_docs",
    description="Python programming documentation"
)

numpy_tool = QueryEngineTool.from_defaults(
    query_engine=numpy_index.as_query_engine(),
    name="numpy_docs",
    description="NumPy array documentation"
)

agent = FunctionAgent.from_tools(
    tools=[python_tool, numpy_tool],
    llm=llm
)

# Agent chooses correct knowledge base
response = agent.chat("How do I create numpy arrays?")
```

## Best practices

1. **Clear tool descriptions** - Agent needs to know when to use each tool
2. **Limit tools to 5-10** - Too many confuses agent
3. **Use verbose mode during dev** - See agent reasoning
4. **Combine RAG + calculation** - Powerful combination
5. **Test tool combinations** - Ensure they work together

## Resources

- **Agents Docs**: https://developers.llamaindex.ai/python/framework/modules/agents/




### Data_Connectors

# LlamaIndex Data Connectors Guide

300+ data connectors via LlamaHub.

## Built-in loaders

### SimpleDirectoryReader

```python
from llama_index.core import SimpleDirectoryReader

# Load all files
documents = SimpleDirectoryReader("./data").load_data()

# Filter by extension
documents = SimpleDirectoryReader(
    "./data",
    required_exts=[".pdf", ".docx", ".txt"]
).load_data()

# Recursive
documents = SimpleDirectoryReader("./data", recursive=True).load_data()
```

### Web pages

```python
from llama_index.readers.web import SimpleWebPageReader, BeautifulSoupWebReader

# Simple loader
reader = SimpleWebPageReader()
documents = reader.load_data(["https://example.com"])

# Advanced (BeautifulSoup)
reader = BeautifulSoupWebReader()
documents = reader.load_data(urls=[
    "https://docs.python.org",
    "https://numpy.org"
])
```

### PDF

```python
from llama_index.readers.file import PDFReader

reader = PDFReader()
documents = reader.load_data("paper.pdf")
```

### GitHub

```python
from llama_index.readers.github import GithubRepositoryReader

reader = GithubRepositoryReader(
    owner="facebook",
    repo="react",
    filter_file_extensions=[".js", ".jsx"],
    verbose=True
)

documents = reader.load_data(branch="main")
```

## LlamaHub connectors

Visit https://llamahub.ai for 300+ connectors:
- Notion, Google Docs, Confluence
- Slack, Discord, Twitter
- PostgreSQL, MongoDB, MySQL
- S3, GCS, Azure Blob
- Stripe, Shopify, Salesforce

### Install from LlamaHub

```bash
pip install llama-index-readers-notion
```

```python
from llama_index.readers.notion import NotionPageReader

reader = NotionPageReader(integration_token="your-token")
documents = reader.load_data(page_ids=["page-id"])
```

## Custom loader

```python
from llama_index.core.readers.base import BaseReader
from llama_index.core import Document

class CustomReader(BaseReader):
    def load_data(self, file_path: str):
        # Your custom loading logic
        with open(file_path) as f:
            text = f.read()
        return [Document(text=text, metadata={"source": file_path})]

reader = CustomReader()
documents = reader.load_data("data.txt")
```

## Resources

- **LlamaHub**: https://llamahub.ai
- **Data Connectors Docs**: https://developers.llamaindex.ai/python/framework/modules/data_connectors/




### Query_Engines

# LlamaIndex Query Engines Guide

Complete guide to query engines, modes, and customization.

## What are query engines?

Query engines power the retrieval and response generation in LlamaIndex:
1. Retrieve relevant chunks from index
2. Generate response using LLM + context
3. Return answer (optionally with sources)

## Basic query engine

```python
from llama_index.core import VectorStoreIndex

index = VectorStoreIndex.from_documents(documents)

# Default query engine
query_engine = index.as_query_engine()
response = query_engine.query("What is the main topic?")
print(response)
```

## Response modes

### 1. Compact (default) - Best for most cases

```python
query_engine = index.as_query_engine(
    response_mode="compact"
)

# Combines chunks that fit in context window
response = query_engine.query("Explain quantum computing")
```

### 2. Tree summarize - Hierarchical summarization

```python
query_engine = index.as_query_engine(
    response_mode="tree_summarize"
)

# Builds summary tree from chunks
# Best for: Summarization tasks, many retrieved chunks
response = query_engine.query("Summarize all the key findings")
```

### 3. Simple summarize - Concatenate and summarize

```python
query_engine = index.as_query_engine(
    response_mode="simple_summarize"
)

# Concatenates all chunks, then summarizes
# Fast but may lose context if too many chunks
```

### 4. Refine - Iterative refinement

```python
query_engine = index.as_query_engine(
    response_mode="refine"
)

# Refines answer iteratively across chunks
# Most thorough, slowest
# Best for: Complex questions requiring synthesis
```

### 5. No text - Return nodes only

```python
query_engine = index.as_query_engine(
    response_mode="no_text"
)

# Returns retrieved nodes without LLM response
# Useful for: Debugging retrieval, custom processing
response = query_engine.query("machine learning")
for node in response.source_nodes:
    print(node.text)
```

## Configuration options

### Similarity top-k

```python
# Return top 3 most similar chunks
query_engine = index.as_query_engine(
    similarity_top_k=3  # Default: 2
)
```

### Streaming

```python
# Stream response tokens
query_engine = index.as_query_engine(streaming=True)

response = query_engine.query("Explain neural networks")
for text in response.response_gen:
    print(text, end="", flush=True)
```

### Verbose mode

```python
# Show retrieval and generation process
query_engine = index.as_query_engine(verbose=True)

response = query_engine.query("What is Python?")
# Prints: Retrieved chunks, prompts, LLM calls
```

## Custom prompts

### Text QA template

```python
from llama_index.core import PromptTemplate

qa_prompt = PromptTemplate(
    "Context information is below.\n"
    "---------------------\n"
    "{context_str}\n"
    "---------------------\n"
    "Given the context, answer: {query_str}\n"
    "If the context doesn't contain the answer, say 'I don't know'.\n"
    "Answer: "
)

query_engine = index.as_query_engine(text_qa_template=qa_prompt)
```

### Refine template

```python
refine_prompt = PromptTemplate(
    "The original query is: {query_str}\n"
    "We have an existing answer: {existing_answer}\n"
    "We have new context: {context_msg}\n"
    "Refine the answer based on new context. "
    "If context isn't useful, return original answer.\n"
    "Refined Answer: "
)

query_engine = index.as_query_engine(
    response_mode="refine",
    refine_template=refine_prompt
)
```

## Node postprocessors

### Metadata filtering

```python
from llama_index.core.postprocessor import MetadataReplacementPostProcessor

postprocessor = MetadataReplacementPostProcessor(
    target_metadata_key="window"  # Replace node content with window
)

query_engine = index.as_query_engine(
    node_postprocessors=[postprocessor]
)
```

### Similarity cutoff

```python
from llama_index.core.postprocessor import SimilarityPostprocessor

# Filter nodes below similarity threshold
postprocessor = SimilarityPostprocessor(similarity_cutoff=0.7)

query_engine = index.as_query_engine(
    node_postprocessors=[postprocessor]
)
```

### Reranking

```python
from llama_index.core.postprocessor import SentenceTransformerRerank

# Rerank retrieved nodes
reranker = SentenceTransformerRerank(
    model="cross-encoder/ms-marco-MiniLM-L-2-v2",
    top_n=3
)

query_engine = index.as_query_engine(
    node_postprocessors=[reranker],
    similarity_top_k=10  # Retrieve 10, rerank to 3
)
```

## Advanced query engines

### Sub-question query engine

```python
from llama_index.core.query_engine import SubQuestionQueryEngine
from llama_index.core.tools import QueryEngineTool

# Multiple indices for different topics
python_index = VectorStoreIndex.from_documents(python_docs)
numpy_index = VectorStoreIndex.from_documents(numpy_docs)

# Create tools
python_tool = QueryEngineTool.from_defaults(
    query_engine=python_index.as_query_engine(),
    description="Useful for Python programming questions"
)
numpy_tool = QueryEngineTool.from_defaults(
    query_engine=numpy_index.as_query_engine(),
    description="Useful for NumPy array questions"
)

# Sub-question engine decomposes complex queries
query_engine = SubQuestionQueryEngine.from_defaults(
    query_engine_tools=[python_tool, numpy_tool]
)

# "How do I create numpy arrays in Python?" becomes:
# 1. Query numpy_tool about array creation
# 2. Query python_tool about syntax
# 3. Synthesize answers
response = query_engine.query("How do I create numpy arrays in Python?")
```

### Router query engine

```python
from llama_index.core.query_engine import RouterQueryEngine
from llama_index.core.selectors import LLMSingleSelector

# Route to appropriate index based on query
selector = LLMSingleSelector.from_defaults()

query_engine = RouterQueryEngine(
    selector=selector,
    query_engine_tools=[python_tool, numpy_tool]
)

# Automatically routes to correct index
response = query_engine.query("What is Python?")  # Routes to python_tool
response = query_engine.query("NumPy broadcasting?")  # Routes to numpy_tool
```

### Transform query engine

```python
from llama_index.core.query_engine import TransformQueryEngine
from llama_index.core.query_transforms import HyDEQueryTransform

# HyDE: Generate hypothetical document before retrieval
hyde_transform = HyDEQueryTransform(include_original=True)

query_engine = TransformQueryEngine(
    query_engine=base_query_engine,
    query_transform=hyde_transform
)

# Improves retrieval quality
response = query_engine.query("What are the benefits of Python?")
```

## Chat engine (conversational)

### Basic chat engine

```python
# Chat engine with memory
chat_engine = index.as_chat_engine(
    chat_mode="condense_plus_context"
)

# Multi-turn conversation
response1 = chat_engine.chat("What is Python?")
response2 = chat_engine.chat("What are its main features?")  # Remembers context
response3 = chat_engine.chat("Can you give examples?")
```

### Chat modes

```python
# 1. condense_plus_context (recommended)
chat_engine = index.as_chat_engine(chat_mode="condense_plus_context")
# Condenses chat history + retrieves relevant context

# 2. context - Simple RAG
chat_engine = index.as_chat_engine(chat_mode="context")
# Retrieves context for each query

# 3. react - Agent-based
chat_engine = index.as_chat_engine(chat_mode="react")
# Uses ReAct agent pattern with tools

# 4. best - Automatically selects best mode
chat_engine = index.as_chat_engine(chat_mode="best")
```

### Reset conversation

```python
# Clear chat history
chat_engine.reset()

# Start new conversation
response = chat_engine.chat("New topic: what is machine learning?")
```

## Structured output

### Pydantic models

```python
from pydantic import BaseModel
from llama_index.core.output_parsers import PydanticOutputParser

class Summary(BaseModel):
    title: str
    main_points: list[str]
    category: str

output_parser = PydanticOutputParser(output_cls=Summary)

query_engine = index.as_query_engine(
    output_parser=output_parser
)

response = query_engine.query("Summarize the document")
# response is a Pydantic model
print(response.title, response.main_points)
```

## Source tracking

### Get source nodes

```python
query_engine = index.as_query_engine()

response = query_engine.query("What is Python?")

# Access source nodes
for node in response.source_nodes:
    print(f"Text: {node.text}")
    print(f"Score: {node.score}")
    print(f"Metadata: {node.metadata}")
```

## Best practices

1. **Use compact mode for most cases** - Good balance
2. **Set similarity_top_k appropriately** - 2-5 usually optimal
3. **Enable streaming for long responses** - Better UX
4. **Add postprocessors for quality** - Reranking improves results
5. **Use chat engine for conversations** - Built-in memory
6. **Track source nodes** - Cite sources to users
7. **Custom prompts for domain** - Better responses
8. **Test different response modes** - Pick best for use case
9. **Monitor token usage** - Retrieval + generation costs
10. **Cache query engines** - Don't recreate each time

## Performance tips

### Caching

```python
from llama_index.core.storage.chat_store import SimpleChatStore

# Cache chat history
chat_store = SimpleChatStore()
chat_engine = index.as_chat_engine(
    chat_mode="condense_plus_context",
    chat_store=chat_store
)
```

### Async queries

```python
import asyncio

# Async query for concurrent requests
response = await query_engine.aquery("What is Python?")

# Multiple concurrent queries
responses = await asyncio.gather(
    query_engine.aquery("What is Python?"),
    query_engine.aquery("What is Java?")
)
```

## Resources

- **Query Engines Docs**: https://developers.llamaindex.ai/python/framework/modules/querying/
- **Response Modes**: https://developers.llamaindex.ai/python/framework/modules/querying/response_modes/
- **Chat Engines**: https://developers.llamaindex.ai/python/framework/modules/chat/




---

## 🚀 Usage

**Reference this template:** `@skill-llamaindex.md`


**Platform-specific:**
- **GitHub Copilot**: Add to `.github/copilot-instructions.md`
- **Augment Code**: Use `aug context add` command
- **Cursor/Windsurf**: Reference in chat with `@filename`
- **Claude**: Add to Project Knowledge
- **ChatGPT**: Add to Custom GPT configuration
