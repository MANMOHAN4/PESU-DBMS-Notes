# Vector Search with NoSQL

## Introduction

NoSQL databases are used for horizontal scalability and can store various data types. Vectors represent high-dimensional data, and vector search finds similar items using vector embeddings.

Since embedding and search operations are computationally intensive, NoSQL databases—especially in-memory ones like Redis—are built for low-latency reads/writes needed for real-time results.

## Why Use NoSQL for Vector Search?

### 1. Unified Data Model
- Store vector embeddings alongside operational metadata (product price, user ID, etc.)
- Avoids separate databases for vectors and business data
- Simplifies architecture

### 2. Built for AI-Scale
- NoSQL databases have flexible schema perfectly suited for AI applications
- Ideal for recommendation systems, RAG systems, LLM chatbots
- Semantic caching reduces costs and increases performance

### 3. Hybrid Search Queries
- Combine semantic vector search with traditional metadata filtering in single query
- Vectors stored with operational data enable powerful combined searches

## Use Cases and Examples

### Real-World Use Cases
- E-commerce personalization
- Geospatial data analysis and mapping
- Recommendation systems
- Biometrics and anomaly detection
- RAG, NLP, image/video recognition, AI applications

### Database Examples
- MongoDB Vector Search (via MongoDB Atlas)
- Redis (via Redis Stack)
- Azure Cosmos DB for NoSQL
- Aerospike Database

## Using Redis NoSQL for Vector Search

Redis vector search capabilities provided by Redis Stack bundling Redis with modules like RediSearch.

### Key Features

**Native Data Type**: VECTOR is binary-safe data type for fast vector comparisons (much faster than text strings).

**Hybrid Indexing**: Build indexes combining VECTOR fields with text, numbers, tags for powerful hybrid searches.

**ANN Algorithms**: Different Approximate Nearest Neighbor algorithms available to balance speed, memory, and accuracy.

## Indexing Methods in Redis

### FLAT (Flat Index)

**How**: Brute-force search comparing query vector to every other vector.

**Pros**: 100% accurate results (Exact Nearest Neighbor).

**Cons**: Slow O(N) complexity; only for smaller datasets.

### HNSW (Hierarchical Navigable Small World)

**How**: Multi-layered graph structure enabling fast traversal. Starts at high sparse layer, navigates down to denser layers.

**Pros**: Extremely fast (logarithmic complexity) even with billions of vectors; production standard.

**Cons**: Approximate but recall typically over 99%.

## Vector Search in Redis: 3-Step Process

### Step 1: Create Index Schema

```redis
FT.CREATE idx:user_prefs
ON JSON
PREFIX 1 user
SCHEMA
  $.user AS user TAG
  $.descr AS descr TEXT
  $.labels AS labels TAG
  $.vector_embedding AS vector_embedding VECTOR HNSW
    TYPE FLOAT32
    DIM 3
    DISTANCE_METRIC COSINE
```

**Components**:
- **FT.CREATE idx:user_prefs**: Create vector index named idx:user_prefs
- **ON JSON**: Index operates on JSON data type (can also use HSET)
- **PREFIX 1 user**: Only keys starting with "user:" indexed (e.g., user:1 indexed, session:1 not indexed)
- **SCHEMA**: List of fields to index (like table columns)
- **TAG**: Field for exact matching (doesn't tokenize); used for filtering
- **VECTOR HNSW**: Vector field using HSNW indexing algorithm
- **TYPE FLOAT32**: 32-bit floating-point values
- **DIM 3**: Vector dimensionality is 3
- **DISTANCE_METRIC COSINE**: Use cosine similarity

### Step 2: Add Data and Vectors

```redis
JSON.SET user:1 $ '{"user":"samuel","descr":"Samuel likes mountain and kid''s bikes.","labels":"mountain, kids","vector_embedding":[0.9, 0.7, 0.2]}'

JSON.SET user:2 $ '{"user":"alice","descr":"Alice prefers road bikes.","labels":"road","vector_embedding":[0.8, 0.6, 0.3]}'

JSON.SET user:3 $ '{"user":"bob","descr":"Bob is into BMX bikes.","labels":"bmx","vector_embedding":[0.7, 0.9, 0.1]}'
```

**Pattern**: JSON.SET <key> <path> <JSON data>

**Example (user:1)**:
- **Key**: user:1
- **user**: "samuel" — user's name
- **descr**: "Samuel likes mountain and kid's bikes." — description for text search
- **labels**: "mountain, kids" — tags for exact matching via TAG index
- **vector_embedding**: [0.9, 0.7, 0.2] — 3D embedding vector

**Note**: Vector passed as binary blob, not string.

### Step 3: Perform Vector Search

```redis
FT.SEARCH idx:user_prefs
  (*)
  KNN 2 @vector_embedding $input_vector
  PARAMS 2 input_vector "[0.8 0.5 0.4]"
  DIALECT 2
```

**Components**:
- **FT.SEARCH idx:user_prefs**: Search in index
- **(*)**: Wildcard search pattern matching all documents (filter by similarity, not text/tags)
- **KNN 2**: Return 2 most similar documents
- **@vector_embedding**: Field name storing vector embeddings
- **$input_vector**: Parameter placeholder for query vector
- **PARAMS 2**: 2 parameters being passed (name and value)
- **input_vector**: Placeholder name
- **[0.8 0.5 0.4]**: Model-generated embeddings replacing <>
- **DIALECT 2**: RediSearch module supports vector search

## Practical Example

### Setup
Create index for user preferences with 3D vectors using cosine similarity.

### Data
Three users with bike preferences and embeddings:
- Samuel: [0.9, 0.7, 0.2]
- Alice: [0.8, 0.6, 0.3]
- Bob: [0.7, 0.9, 0.1]

### Query
Find 2 users most similar to query vector [0.8, 0.5, 0.4].

### Result
Returns users ranked by cosine distance with similarity scores.

## Key Concepts

### Vector Native Support
Redis VECTOR data type provides binary-safe storage and fast comparisons.

### Hybrid Search
Combine vector similarity (semantic) with TAG/TEXT filters (precise) in single query.

### ANN Efficiency
Approximate nearest neighbor with 99%+ recall while maintaining logarithmic complexity.

### Indexing Strategy
HNSW for production systems balancing speed and accuracy; FLAT for small datasets needing 100% accuracy.

### Distance Metrics
Cosine similarity most common for semantic similarity; alternatives include Euclidean L2.

## Production Considerations

**Scale**: HNSW handles billions of vectors efficiently; FLAT limited to millions.

**Accuracy vs Speed**: HNSW ~99% recall acceptable for most applications; trade-off for logarithmic speed.

**Memory**: HNSW uses graph structure consuming more memory than FLAT; necessary for performance.

**Filtering**: Hybrid queries combining vector search with metadata filters provide powerful functionality.

**Real-time**: Redis in-memory design enables real-time similarity search at scale.
