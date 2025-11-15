# Vector Search with NoSQL

## Course and Instructors

- Database Management Systems[^1]
- Prof. Nivedita Kasturi and Prof. Mahitha G, Department of Computer Science and Engineering[^1]
- Acknowledgments: Teaching Assistants[^1]

## Learning Objectives

- Understand why NoSQL databases are well-suited for vector search.[^1]
- Learn Redis Stack’s vector search capabilities and schema design.[^1]
- Implement ANN indexing (FLAT and HNSW) and hybrid search.[^1]
- Practice with JSON.SET, FT.CREATE, and FT.SEARCH for semantic retrieval.[^1]

---

## Introduction

- NoSQL databases focus on horizontal scalability and support varied data types, including high-dimensional vectors for embeddings.[^1]
- Vector search finds similar items using vector embeddings; these operations are computationally intensive, and low-latency NoSQL engines like Redis are designed to serve real-time results.[^1]

---

## Why NoSQL for Vector Search?

1. Unified data model

- Store embeddings alongside operational metadata (e.g., price, userId) to keep a single source of truth and simplify architecture.[^1]

2. Built for AI-scale

- Flexible schema handles diverse, evolving AI data for recommendation systems, RAG, and chatbots; semantic caching can reduce cost and latency.[^1]

3. Hybrid search queries

- Combine semantic vector similarity with exact metadata filters in one query when vectors live with the rest of the data.[^1]

---

## Use Cases and Examples

- Real-world: e-commerce personalization, geospatial mapping, recommendation systems, biometrics and anomaly detection, RAG/NLP/vision.[^1]
- Database offerings: MongoDB Vector Search (Atlas), Redis (Redis Stack), Azure Cosmos DB for NoSQL, Aerospike Database.[^1]

---

## Redis Stack for Vector Search

- Redis Stack bundles Redis with RediSearch to provide vector search.[^1]
- VECTOR is a native, binary-safe type for fast vector comparisons—more efficient than storing as strings.[^1]
- Indexes can combine VECTOR fields with text, numeric, and tag fields to enable hybrid semantic + metadata queries.[^1]
- ANN algorithm options allow trade-offs in speed, memory, and accuracy, tuned to workload needs.[^1]

---

## Indexing Methods in Redis

1. FLAT (Exact)

- Brute-force scan over all vectors; exact nearest neighbors with O(N) complexity; best for small datasets.[^1]

2. HNSW (Approximate)

- Multi-layer small-world graph enabling fast traversal to approximate neighbors; near-logarithmic performance and high recall, typical for production scale.[^1]

---

## Step 1 — Create an Index Schema

- Define index over JSON docs (or Hashes), set field types, specify vector algorithm, dimension, and metric.[^1]

Example with JSON and HNSW:

```bash
FT.CREATE idx:user_prefs ON JSON PREFIX 1 "user:" SCHEMA
  $.user AS user TEXT
  $.descr AS descr TEXT
  $.labels AS labels TAG
  $.vector_embedding AS vector_embedding VECTOR HNSW
    6 TYPE FLOAT32
    6 DIM 3
    6 DISTANCE_METRIC COSINE
```

- idx:user_prefs is index name; PREFIX 1 "user:" limits to keys starting with user:.[^1]
- TEXT fields tokenize; TAG fields are exact-match categories; VECTOR HNSW sets vector parameters (type, dimension, metric).[^1]

Notes:

- In CLI, module options are often passed as alternating name/value tokens as shown above.[^1]

---

## Step 2 — Add Data and Vectors

- Use JSON.SET per key; vectors are binary blobs (not strings) when using client libraries. For illustration, raw arrays are shown.[^1]

Example inserts:

```bash
JSON.SET user:1 $ '{
  "user": "samuel",
  "descr": "Samuel likes mountain and kid'\''s bikes.",
  "labels": "mountain, kids",
  "vector_embedding": [0.9, 0.7, 0.2]
}'

JSON.SET user:2 $ '{
  "user": "andrew",
  "descr": "Andrew prefers road and hybrid bikes.",
  "labels": "road, hybrid",
  "vector_embedding": [0.85, 0.65, 0.15]
}'

JSON.SET user:3 $ '{
  "user": "emma",
  "descr": "Emma enjoys city rides and e-bikes.",
  "labels": "city, ebike",
  "vector_embedding": [0.1, 0.2, 0.9]
}'
```

---

## Step 3 — Perform Vector (KNN) Search

- Use FT.SEARCH with a wildcard query and KNN to retrieve most similar documents; pass the query vector with PARAMS and specify DIALECT 2.[^1]

Example: top-2 most similar

```bash
FT.SEARCH idx:user_prefs
  "*"
  RETURN 3 user descr labels
  PARAMS 2 input_vector "\x00\x00..."  /* supply binary FLOAT32 for 3 dims via client */
  DIALECT 2
  KNN 2 @vector_embedding $input_vector
```

Notes:

- In practice, client SDKs handle vector binary encoding; raw shell examples use placeholders.[^1]
- Combine semantic and metadata filters, e.g., restrict to labels using TAG while still ranking by vector similarity.[^1]

Hybrid example (semantic + tag filter):

```bash
FT.SEARCH idx:user_prefs
  "@labels:{mountain}"
  RETURN 2 user descr
  PARAMS 2 input_vector "<binary-f32-3>"
  DIALECT 2
  KNN 2 @vector_embedding $input_vector
```

---

## Practical Tips

- Choose HNSW for large datasets; FLAT for small or exact-match needs.[^1]
- Keep vectors as FLOAT32 to save memory unless higher precision is necessary; match DIM to your embedding model output.[^1]
- Use TAG fields for exact filtering on categories; TEXT fields for keywords; combine with KNN for hybrid relevance.[^1]
- Semantic caching: store query embeddings and top results to speed up repeated queries in RAG/chatbots.[^1]

---

## References

- mlops.community vector similarity primer; Redis vector database overviews and docs; how-to guides for building RAG with Redis; community tutorials.[^1]

---

## Thank You

- Prof. Nivedita Kasturi
- Prof. Mahitha G (mahithag@pes.edu)[^1]

<div align="center">⁂</div>

[^1]: 70_Vector_Search_NoSQL.pdf
