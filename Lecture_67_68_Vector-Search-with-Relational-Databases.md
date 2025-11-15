# Vector Search with Relational Databases

## Course and Instructor

- Database Management Systems[^1]
- Prof. Nivedita Kasturi, Department of Computer Science and Engineering[^1]

## Learning Objectives

- Understand vectors and embeddings in ML contexts.[^1]
- Explain how vectors are stored and queried in databases.[^1]
- Learn how relational systems integrate vector data types and similarity search.[^1]
- Differentiate distance metrics and ANN indexing approaches.[^1]
- Apply a step-by-step workflow to vectorize relational data and run semantic queries.[^1]

---

## Understanding Vectors

- A vector is a dynamic array of numbers used in math and physics to represent magnitude and direction; in 2D, a point like (2.45, 1.28) encodes a location.[^1]
- Conceptual simplification: treat a vector as a list of object attributes, such as house = [bedrooms, bathrooms, area, price].[^1]
- In ML, vectors represent feature values per data point; e.g., flowers with petal length/width become 2D vectors like (2.5, 1.6) to compare similarity on a plot.[^1]

---

## Vector Databases and Embeddings

- Each vector corresponds to one object (word, image, audio, document); vectors are often called embeddings when produced by an embedding model.[^1]
- Store vectors alongside source content/metadata (URL, image link, SKU) to support retrieval tasks across text, images, and audio.[^1]
- Clusters form by semantic similarity: related items like “black t-shirt” and “black polo shirt” are closer; unrelated categories like phones vs. clothes are farther apart.[^1]

---

## Embeddings: What and Why

- Embedding models convert complex data types into numeric vectors so similarity can be computed and compared; they preserve key contextual features while simplifying raw data.[^1]
- Dimensions capture aspects of meaning such as semantics and syntax; closely related concepts (e.g., “Boats” and “Ferries”) appear nearby in the space.[^1]
- Pretrained models are available from OpenAI, Google, Meta, and open-source communities (e.g., sentence-transformers like paraphrase-MiniLM-L6-v2).[^1]

Example sentences used for visualization and clustering:

- “Sunsets are breathtaking.”
- “Kindness is contagious.”
- “Laughter brings joy.”
- “Music is uplifting.”
- “Success is rewarding.”
- “Traffic jams are frustrating.”
- “Rainy days can be gloomy.”
- “Failure is disheartening.”
- “Mosquitoes are annoying.”
- “Colds are unpleasant.”
  [^1]

---

## Why Vector Types in RDBMS

- Storing embeddings as numeric arrays in plain SQL tables makes querying expensive because each search requires retrieving all vectors and computing similarity per row.[^1]
- Modern RDBMS add native vector data types and operators so the engine can perform similarity search intrinsically, avoiding manual cosine/distance loops in application code.[^1]

Common ecosystems and model sources:

- Hugging Face: Word2Vec, GloVe, Paragram for text embeddings.[^1]
- TensorFlow Universal Sentence Encoder for sentence/paragraph embeddings.[^1]
- OpenAI embeddings using transformer families; GoogleGenerativeAI embeddings (e.g., BERT-derived).[^1]

---

## End-to-End Workflow: Vectorizing Relational Data

Step 1: Extract data

- Connect to your RDBMS (e.g., PostgreSQL) and select the text field to embed (e.g., product description).[^1]

Step 2: Generate embeddings

- Use a sentence embedding model to convert each row’s text to a vector.[^1]

Step 3: Store embeddings

- Add a vector column (e.g., via pgvector) and update records with embeddings.[^1]

Step 4: Query by similarity

- Use vector operators (e.g., <=>) to find nearest neighbors to an embedded query.[^1]

Note: Slides reference “55_56 code for VectorSearch with RelationalDB” for detailed code walkthroughs.[^1]

---

## How Vector DBs Work with Embeddings

- Data processing: handle structured and unstructured inputs (text, images, audio, video).[^1]
- Embedding generation: a neural model encodes items into high-dimensional vectors and stores them.[^1]
- Retrieval: on query, embed the query and perform similarity search to return the most relevant vectors quickly.[^1]

Benefits:

- Rapid, accurate retrieval over vast and varied data types for use cases like search, recommendations, and analytics.[^1]

---

## Similarity Metrics

- Similarity/distance quantifies how close two vectors are.[^1]
- Cosine similarity: uses the cosine of the angle between vectors; angles near 0 imply cosine near 1 (high similarity), 90° implies 0 (orthogonal).[^1]
- Euclidean distance: geometric distance in vector space; common alternative for nearest neighbor search.[^1]

Sentence example:

- Query: “Rainy days make me sad”
- Closest: “Rainy days can be gloomy” (similar negative sentiment)
- Lowest similarity: “Success is rewarding” (positive sentiment)
  [^1]

---

## Vector Search and Indexing

- Vector search returns objects similar to a query vector based on semantic meaning using distance metrics like cosine and Euclidean.[^1]
- Indexing: approximate nearest neighbor (ANN) structures like HNSW organize vectors for fast retrieval at scale, trading a small accuracy margin for big speed gains.[^1]

Popular methods:

- KNN: exact nearest neighbors for small/medium datasets.[^1]
- ANN: approximate neighbors for large/high-dimensional data.[^1]
- HNSW: hierarchical graph for fast, memory-efficient search; used in multiple vector-capable systems.[^1]
- IVFPQ: inverted file with product quantization to scale to massive datasets while maintaining high accuracy.[^1]

---

## RDBMS Integration Pattern

- Add a vector column to a relational table (e.g., products.embedding).[^1]
- Use a vector index (pgvector in PostgreSQL; similar capabilities exist in SQL Server, Oracle, and others).[^1]
- Hybrid search: combine metadata filters (WHERE clauses) with vector similarity for scoped, relevant results.[^1]

---

## Q\&A

1. Which metric is commonly used to measure vector similarity?

- a) Primary key indexing
- b) Cosine similarity
- c) Sorting by timestamps
- d) Hashing
  Answer: b[^1]

2. What is a primary benefit of HNSW?

- a) Exact exhaustive search
- b) Product quantization compression
- c) Hierarchical graph enabling fast, memory-efficient search
- d) Works only for low-dimensional data
  Answer: c[^1]

3. Which indexing method combines inverted files with vector compression for high-accuracy at scale?

- a) Flat index
- b) IVFPQ
- c) Vantage-Point Tree
- d) HNSW
  Answer: b[^1]

---

## References

- Oracle AI Vector Search lecture slides, O’Reilly chapter on vector databases, Microsoft SQL Server vectors docs, and explanatory articles/videos listed in the deck.[^1]

---

## Thank You

- Prof. Nivedita Kasturi (niveditak@pes.edu)[^1]

<div align="center">⁂</div>

[^1]: 67_68_Vector-Search-with-Relational-Databases.pdf
