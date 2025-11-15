# Introduction to Vector Databases

## Course and Instructors

- Database Management Systems[^1]
- Prof. Nivedita Kasturi and Prof. Mahitha G, Department of Computer Science and Engineering[^1]

## Learning Objectives

- Understand why vector databases are needed alongside traditional DBMS features.[^1]
- Explain embeddings, similarity search, and semantic retrieval.[^1]
- Recognize core capabilities, indexing approaches, and workloads.[^1]
- Identify use cases across domains like search, personalization, and life sciences.[^1]
- Differentiate vector search from full-text search (FTS).[^1]
- Practice with concept-check Q\&A.[^1]

---

## From Relational to Vector Support

- Traditional RDBMS manage numbers, text, BLOBs (images, video) and CLOBs (large text like PDFs/Word docs) with access, modification, and update facilities over file-managed storage.[^1]
- SQL databases historically support primitive types (integer, float, boolean, etc.); new platforms increasingly add vector types to serve ML-driven applications needing semantic search and recommendations.[^1]

---

## Why Not Only FTS?

- Full-text search indexes tokens and ranks results for large textual fields, exceeding simple LIKE/regex matching, delivering relevance-ranked results at scale.[^1]
- Vector search retrieves results by semantic meaning using high-dimensional embeddings that capture context, synonyms, and intent, making it effective for recommendation, QA, and multilingual search.[^1]

---

## What Is a Vector Database?

- Definition: Specialized system for storing and querying high-dimensional vectors for fast similarity search and retrieval across unstructured data.[^1]
- Analogy: A librarian who knows conceptual connections between books to surface similar content beyond exact keyword matches.[^1]
- How it works: Organizes points by similarity/context and returns nearest vectors; integrates with LLMs for Retrieval-Augmented Generation (RAG).[^1]

---

## Landscape

- Vendors and ecosystems are actively evolving; dedicated DBs (e.g., Chroma, Weaviate) and existing SQL/NoSQL systems are adding vector search options.[^1]

---

## Core Characteristics

- Store embeddings that encode semantic meaning and relationships from text, images, audio.[^1]
- Use specialized indexing such as hashing, quantization, and graph-based methods for approximate nearest neighbor (ANN) search.[^1]
- Suited for semantically similar retrieval tasks at AI scale; computationally intensive but designed for horizontal scaling.[^1]

---

## Capabilities and Applications

- Efficient similarity search: Retrieve nearest vectors to a query vector.[^1]
- High-dimensional handling: Practical for NLP, vision, genomics, and other vector-heavy workloads.[^1]
- ML/AI storage: Persist embeddings for clustering, classification, anomaly detection, and retrieval.[^1]
- Real-time applications: Low-latency recommendations, fraud detection, IoT monitoring.[^1]
- Personalization: User profiling for streaming, social media, marketplaces.[^1]
- Spatial/geographic: Points, lines, polygons for GIS and navigation-like queries.[^1]
- Healthcare/life sciences: Genetic sequences, proteins, molecular representations.[^1]
- Data fusion: Combine text, images, and numeric features for richer multimodal analysis.[^1]
- Multilingual search: Cross-lingual similarity through shared embedding spaces.[^1]

---

## Conceptual Example

- Think of a 2D grid where one axis encodes color and another size; vectors near each other are semantically similar, enabling quick retrieval of similar items (e.g., nearest crayons in color space).[^1]

---

## How Vector DBs Differ From Traditional DBs

- Traditional DBs excel at structured tables, exact matching, joins, and transactions; vector DBs prioritize similarity search on embeddings with ANN indexes, typically trading exactness for speed at scale.[^1]

---

## Workflow Overview

1. Generate embeddings from data (text, images, audio).
2. Store vectors and metadata in the vector DB.
3. At query time, embed the query and perform nearest neighbor search.
4. Optionally filter by metadata and rerank results.
5. Use retrieved context for applications like RAG or recommendations.
   [^1]

---

## Q\&A

1. Which is a primary advantage of vector databases over traditional relational databases?

- A) Store tabular data more efficiently
- B) Faster numeric transaction processing
- C) Similarity searches based on semantic meaning
- D) Use B-tree indexes to optimize joins
  Answer: C[^1]

2. In DB systems, what does FTS primarily enable?

- A) Semantic similarity retrieval
- B) Tokenization and word-level indexing over large text columns
- C) Efficient storage of high-dimensional vectors
- D) Generating vector embeddings
  Answer: B[^1]

3. Typical use case for vector databases?

- A) Enforcing foreign keys
- B) Multilingual similarity search across documents
- C) SUM/COUNT over numeric fields
- D) ACID for bank balances
  Answer: B[^1]

---

## References

- Medium and vendor resources, Microsoft, IBM, and community articles on vector databases, utilities, and use cases.[^1]

---

## Thank You

- Prof. Nivedita Kasturi (niveditak@pes.edu)[^1]

<div align="center">⁂</div>

[^1]: 66_Introduction-to-Vector-Databases.pdf
