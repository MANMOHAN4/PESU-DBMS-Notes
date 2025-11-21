# Vector Search with Relational Databases

## Understanding Vectors

### What is a Vector?

A vector is a dynamic array of numbers. Commonly used in math and physics to describe spatial coordinates.

**Example:** The two-dimensional vector (2.45, 1.28) expresses the location of a point in two-dimensional space.

**Physics view:** A quantity with magnitude and direction  
**Math view:** A geometric entity showing magnitude and direction  
**Simplified view:** A vector = list of attributes of an object

**House example:** [bedrooms, bathrooms, area, price]

### Vectors in Machine Learning

In ML, vectors are a dynamic array of numbers where each number describes a feature of a data point.

**Flower dataset example:**

| ID | Petal Length (cm) | Petal Width (cm) | Vector |
|----|-------------------|------------------|--------|
| FL1 | 2.5 | 1.6 | (2.5, 1.6) |
| FL2 | 1.1 | 0.9 | (1.1, 0.9) |
| FL3 | 2.7 | 0.4 | (2.7, 0.4) |
| FL4 | 2.7 | 1.4 | (2.7, 1.4) |
| FL5 | 0.9 | 1.3 | (0.9, 1.3) |
| FL6 | 0.5 | 0.5 | (0.5, 0.5) |
| FL7 | 1.8 | 0.9 | (1.8, 0.9) |
| FL8 | 0.4 | 1.1 | (0.4, 1.1) |

Plotting these vectors on a graph reveals that FL1 and FL4 are closest, meaning they're most similar to each other.

## Understanding Vector Embeddings

### Vector Embeddings

- A single vector always corresponds to one object: a word, image, video, or document
- Once an embedding model generates vectors, they're stored in a vector database alongside original content (page URL, image link, product SKU)
- Vectors are sometimes called vector embeddings—a vector produced by an embedding model

### Vector Database Storage

Stores vectors describing objects or data points.

**Examples:**
- **Text:** Vectors for words, paragraphs, documents (used in chatbots, search)
- **Images:** Pixels as numbers combined into high-dimensional vectors
- **Audio:** Sound waves converted to numerical data then vectors (voice recognition)

### Example: Product Recommendation

An ML application recommending products based on previous purchases determines which products are similar to each other. Similar products cluster together (e.g., clothing, phones, board games), with greater distance between dissimilar categories. Within clothing, similar items are closer (black t-shirt and black polo shirt extremely close; blue jeans further away).

## Understanding Embedding

### What is Embedding?

Embedding models convert complex data (text, images, documents, videos) into vectors of numerical data. Converting data into vectors simplifies data while retaining important features.

**How it works:**
- Words represented as vectors in high-dimensional space
- Each dimension corresponds to a specific aspect or feature of word meaning (context, semantics, syntax)
- The procedure to generate vectors from data is called embeddings

**Data types converted to embeddings:**
- Text
- Documents
- Sentences
- Chat messages
- Images
- Audio
- Videos

### Vector Embeddings Capture Semantic Meaning

- Numerical interpretations that retain contextual significance of data
- Facilitate alignment of similar entities within vector space for similarity searches
- Numerically capture semantic meaning of objects in relation to others
- Similar objects grouped together in vector space—closer objects are more similar
- Example: "Boats" and "Ferries" positioned near each other due to contextual links

### Pre-trained Embedding Models

Several pre-trained embedding models available from OpenAI, Google, Meta AI, and open-source community. These models learn from vast text to map words into multi-dimensional vector space.

**Example:** Using paraphrase-MiniLM-L6-v2 on 10 sample sentences:
- "Sunsets are breathtaking."
- "Kindness is contagious."
- "Laughter brings joy."
- "Music is uplifting."
- "Success is rewarding."
- "Traffic jams are frustrating."
- "Rainy days can be gloomy."
- "Failure is disheartening."
- "Mosquitoes are annoying."
- "Colds are unpleasant."

Embeddings are generated and plotted in vector space showing semantic relationships.

### Searching Vector Embeddings in RDBMS

- Storing embeddings as numeric arrays in SQL databases can be memory-intensive and time-consuming
- Every search operation retrieves all vectors, calculates cosine similarity, and returns matches based on similarity value
- Vector length for image, video, and audio data much bigger than text data
- To address this, most RDBMS systems provide wrappers or add-ons that allow vectors as a data type
- Allows retrieving data based on vector similarity rather than manually calculating distances

### Common Embedding Models

**Hugging Face:**
- Creates text embeddings using Word2Vec, Glove, and Paragram

**TensorFlow Universal Sentence Encoder:**
- Creates embeddings using deep learning models optimized for greater-than-word length text

**OpenAI:**
- Creates text embeddings using transformers like GPT models
- Example: text-embedding-ada-002

**Google GenerativeAI:**
- Creates text embeddings using BERT and other transformer architectures
- Example: embedding-gecko-001

## Steps for Generating Embeddings for Products in Relational DB

### Step 1: Extract Data from Relational Table
- Connect to database (e.g., PostgreSQL)
- Select text field to be embedded (e.g., product description)

### Step 2: Generate Embeddings
- Use sentence embedding model (sentence-transformers)
- Convert each row's text into high-dimensional vector

### Step 3: Store Embeddings into DB
- Use pgvector to add vector column
- Update each record with generated vector

### Step 4: Query Using Similarity
- Use vector similarity operators (like <=>)
- Find semantically similar rows (nearest neighbors)

## How Vector Databases Work with Embeddings

### Data Processing
- User query may involve raw data: images, documents, videos, audio
- Data can be structured or unstructured

### Embedding Generation
- Processed through embedding model (complex neural network)
- Converts data into high-dimensional vectors
- Encodes data characteristics as vector embeddings
- Stored in vector database

### Retrieval & Similarity Search
- On query, vector DB performs similarity searches
- Finds vectors most similar to query vector
- Delivers relevant results quickly, handling complex searches

### Benefit
- Enables rapid, accurate management of vast and varied data types
- Ideal for applications needing high-speed search and retrieval

## Similarity Score

### Distance Metrics

Various metrics measure distance between vectors or data points. Two commonly used: Cosine and Euclidean.

**Cosine Metric:**
- Determines similarity by calculating cosine value of angle (θ) between two vectors
- When angle between vectors close to zero, cosine value close to 1
- If angle is 90 degrees (orthogonal vectors), cosine value is zero

### Cosine Similarity Example

Consider new sentence: "Rainy days make me sad". Finding similar sentences from existing list using cosine metric:

- "Rainy days can be gloomy" - most similar (both convey negative feelings about rain)
- "Success is rewarding" - smallest cosine value (expresses positive sentiment)

The vector database rapidly identifies similar entries using these metrics.

### Euclidean Distance

Calculates straight-line distance between vectors. Useful for measuring overall difference magnitude.

## Vector Search in Vector Databases

### Vector Search Process

- Compares similarity between vectors
- Each vector represents an object or data point
- Retrieves objects similar to query vector by analyzing semantic meaning

### Distance Metrics Used

- **Euclidean distance:** Straight-line distance, useful for magnitude-based similarity
- **Cosine similarity:** Angle-based similarity, effective for text/semantic similarity

### Vector Indexing

Vector databases use Approximate Nearest Neighbor algorithms to speed up similarity search:
- Organizes vectors to speed up retrieval (critical for large datasets)
- Uses clustering and indexing algorithms (e.g., HNSW)
- Enables fast, scalable similarity searches

## Popular Algorithms for Clustering and Indexing

### KNN (K-Nearest Neighbors)
- Finds exact nearest neighbors based on distance
- Good for small to medium datasets
- Time-consuming for large datasets

### ANN (Approximate Nearest Neighbors)
- Finds approximate nearest neighbors
- Improves efficiency for large, high-dimensional data
- Trades exact accuracy for speed

### HNSW (Hierarchical Navigable Small World)
- Builds hierarchical graph structure during indexing
- Enables fast neighbor queries, balancing accuracy and speed
- Used in vector DBs like Azure AI Search for ANN searches
- Memory-efficient

### IVFPQ (Inverted File with Product Quantization)
- Combines inverted files with vector compression
- Achieves high-accuracy searches on massive datasets
- Effective for billion-scale vectors

## Q&A

**Q: Which of the following is used to measure similarity between vectors in vector search?**
- a) Primary key indexing
- b) Cosine similarity
- c) Sorting by timestamps
- d) Hashing

Answer: b) Cosine similarity

---

**Q: What is a primary benefit of using an HNSW index in vector search?**
- a) It guarantees exact nearest neighbors with exhaustive search
- b) It compresses data using product quantization
- c) It builds a hierarchical graph for fast and memory-efficient search
- d) It only works for low-dimensional data

Answer: c) It builds a hierarchical graph for fast and memory-efficient search

---

**Q: Which indexing method combines inverted files with vector compression to achieve high-accuracy searches on massive datasets?**
- a) Flat index
- b) IVFPQ
- c) Vantage-Point Tree
- d) HNSW

Answer: b) IVFPQ
