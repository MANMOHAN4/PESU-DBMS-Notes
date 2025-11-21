# Vector Database Hands-On (PostgreSQL)

## Case Study: Movie Recommendation System

Build a PostgreSQL-backed movie recommendation system leveraging semantic vector search. Each movie represented by title, synopsis, and vector embeddings generated from natural language descriptions.

Perform CRUD operations and semantic search to recommend similar movies based on query descriptions.

## Step 1: Create Database

```sql
CREATE DATABASE dbms_class;
\list
\c dbms_class
```

Output shows successful database creation and connection.

## Step 2: Create Table and Vector Extension

```sql
CREATE EXTENSION vector;

CREATE TABLE movies (
    id SERIAL PRIMARY KEY,
    title TEXT,
    synopsis TEXT,
    embedding VECTOR(8)
);

CREATE INDEX ON movies USING hnsw (embedding vector_cosine_ops);
```

### Key Points
- VECTOR(8) stores 8-dimensional embeddings
- HNSW (Hierarchical Navigable Small World) index enables fast semantic search
- vector_cosine_ops uses cosine similarity for comparison (alternative: vector_l2_ops for Euclidean)
- Index created for performance optimization

### View Table Information
```sql
\dt              -- List all tables
\d movies        -- View table schema
\desc movies     -- Describe table structure
```

## Step 3: Insert Movie Data

```sql
INSERT INTO movies (title, synopsis, embedding) VALUES
('Inception', 'A skilled thief leads a team into dreams to steal secrets.', NULL),
('The Matrix', 'A computer hacker learns the true nature of reality and fights to free humanity.', NULL),
('Interstellar', 'Explorers travel through a wormhole in search of a new home.', NULL),
('The Social Network', 'The story of Facebook''s founding and rise to success.', NULL),
('Avatar', 'A marine on an alien planet learns to live among the natives.', NULL),
('The Martian', 'An astronaut struggles to survive alone on Mars.', NULL),
('Her', 'A man develops a relationship with an intelligent operating system.', NULL),
('Blade Runner 2049', 'A blade runner uncovers secrets that could change society.', NULL),
('Guardians of the Galaxy', 'A group of intergalactic criminals must save the universe.', NULL),
('Ex Machina', 'A programmer interacts with an AI to test its consciousness.', NULL);
```

Initial embeddings are NULL; will be populated in next step.

## Step 4: Generate Embeddings and Perform Semantic Search

### Install Required Packages
```bash
pip install psycopg2
pip install sentence-transformers
```

### Python Implementation

```python
import psycopg2
from sentence_transformers import SentenceTransformer

# Connect to PostgreSQL
conn = psycopg2.connect(
    dbname="dbms_class",
    user="postgres",
    password="your_password",
    host="localhost",
    port="5432"
)
cur = conn.cursor()

# Load embedding model
model = SentenceTransformer('all-MiniLM-L6-v2')

# Fetch all movies and generate embeddings
cur.execute("SELECT id, synopsis FROM movies;")
movies = cur.fetchall()

for movie_id, synopsis in movies:
    # Generate embedding
    embedding = model.encode(synopsis)
    # Truncate to 8 dimensions for demo
    embedding_small = embedding[:8]
    # Convert to PostgreSQL vector format
    embedding_str = "[" + ",".join(map(str, embedding_small)) + "]"
    # Update database
    cur.execute(
        "UPDATE movies SET embedding = %s WHERE id = %s;",
        (embedding_str, movie_id)
    )

conn.commit()

# Perform semantic search
user_query = "Movies about technology"
query_embedding = model.encode(user_query)
query_emb_small = query_embedding[:8]
query_emb_str = "[" + ",".join(map(str, query_emb_small)) + "]"

# Execute search query
cur.execute("""
    SELECT id, title, synopsis, embedding <=> %s AS similarity
    FROM movies
    ORDER BY embedding <=> %s
    LIMIT 3;
""", (query_emb_str, query_emb_str))

results = cur.fetchall()
print(f"\nRecommended movies for: \"{user_query}\"")
for r in results:
    print(f"ID {r[0]} | {r[1]} ({r[3]:.4f}) - {r[2]}")

cur.close()
conn.close()
```

### How It Works

**Embedding Model**: Uses all-MiniLM-L6-v2 to generate embeddings from movie synopses.

**Vector Dimensions**: Restricted to 8 for demo simplicity (can be 384 or higher).

**Database Update**: psycopg2 connector fetches all movies and updates embeddings.

**Similarity Search**: Uses HNSW index with cosine similarity distance operator <=>

**Distance Metric**:
- Range: 0.0 (perfect match) to 2.0 (worst match)
- Smaller values = better matches
- <=> is cosine distance (not similarity) due to vector_cosine_ops

## Expected Output

### All Movies with Embeddings

```
ID: 1, Title: Inception
Embedding: [-0.04553012, 0.060346086, -0.0064700013, 0.002296256, ...]

ID: 2, Title: The Matrix
Embedding: [-0.06289084, 0.031028645, -0.08162426, 0.0039487635, ...]

ID: 10, Title: Ex Machina
Embedding: [-0.012013591, 0.0038372856, -0.060572784, -0.034697838, ...]
```

### Semantic Search Results

```sql
SELECT id, embedding FROM movies;
```

Query: "Movies about technology"

Results:
```
ID 2 | The Matrix (0.2726) - A computer hacker learns the true nature of reality...
ID 10 | Ex Machina (0.4468) - A programmer interacts with an AI to test its consciousness...
ID 1 | Inception (0.5127) - A skilled thief leads a team into dreams to steal secrets...
```

## Key Concepts

### pgvector Extension
PostgreSQL extension enabling vector operations and HNSW indexing.

### Sentence Transformers
Pre-trained models converting text to high-dimensional vectors capturing semantic meaning.

### Cosine Similarity
Distance metric comparing angle between vectors; used for semantic similarity.

### HNSW Algorithm
Fast approximate nearest neighbor search using hierarchical navigation through graph layers.

### Semantic Search
Finding similar items based on meaning rather than exact string matching.

## Execution Steps

1. Install PostgreSQL with pgvector extension
2. Create database and table
3. Install Python packages
4. Update password in Python script
5. Ensure PostgreSQL server is running
6. Execute: `python post_hands_on.py`
7. Observe movie recommendations based on "Movies about technology" query
