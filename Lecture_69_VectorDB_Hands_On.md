# Vector Database Hands On (PostgreSQL)

## Course and Instructors

- Database Management Systems[^1]
- Prof. Nivedita Kasturi, Department of Computer Science and Engineering[^1]
- Prof. Mahitha G, Department of Computer Science and Engineering[^1]
- Acknowledgments: Teaching Assistants[^1]

## Goal

- Build a PostgreSQL-backed movie recommendation system using vector embeddings and semantic search via pgvector and HNSW indexing.[^1]

---

## Case Study Overview

- Each movie has a title, synopsis, and an embedding generated from its description.[^1]
- Perform CRUD and semantic similarity search to recommend movies similar to a user’s query.[^1]

---

## Prerequisites

- PostgreSQL with superuser access and psql CLI.[^1]
- pgvector extension available on the server.[^1]
- Python 3 with packages: psycopg2, sentence-transformers.[^1]

Install Python deps:

```bash
pip install psycopg2 sentence-transformers
```

---

## Step 1 — Create Database

In psql:

```sql
-- Create a database
CREATE DATABASE dbms_class;

-- List databases
\list

-- Connect to the database
\c dbms_class
```

Example CLI session shows database creation and connection prompts, then an empty dbms_class ready for use.[^1]

---

## Step 2 — Create Table and Vector Index

Enable pgvector and create a table:

```sql
-- Enable vector extension
CREATE EXTENSION vector;

-- Create movies table
CREATE TABLE movies (
    id SERIAL PRIMARY KEY,
    title TEXT,
    synopsis TEXT,
    embedding VECTOR(8)
);

-- HNSW index using cosine distance
CREATE INDEX ON movies USING hnsw (embedding vector_cosine_ops);
```

Notes:

- vector_cosine_ops configures cosine distance for the index; vector_l2_ops would use Euclidean.[^1]
- Explore tables and schema:
  - \dt to list tables
  - \d movies or \desc movies to show schema.[^1]

---

## Step 3 — Insert Seed Data

Insert initial rows with NULL embeddings:

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

Verify:

```sql
SELECT * FROM movies;
```

- Shows inserted titles and synopses with NULL embeddings.[^1]

---

## Step 4 — Generate Embeddings and Run Semantic Search

Use Python to generate embeddings with sentence-transformers and update the table.

Python script (post_hands_on.py):

```python
import psycopg2
from sentence_transformers import SentenceTransformer

# Connect to PostgreSQL
conn = psycopg2.connect(
    dbname="dbms_class",
    user="postgres",
    password="your_password",   # update to your password
    host="localhost",
    port="5432"
)
cur = conn.cursor()

# Load model
model = SentenceTransformer("sentence-transformers/all-MiniLM-L6-v2")

# Fetch movies (title+synopsis) and ids
cur.execute("SELECT id, title, synopsis FROM movies;")
rows = cur.fetchall()

# Generate 8-dim embeddings (truncate for demo)
for mid, title, synopsis in rows:
    text = f"{title}. {synopsis}"
    emb = model.encode(text)
    emb_small = emb[:8]
    # Store as pgvector array literal
    emb_str = "[" + ",".join(map(str, emb_small)) + "]"
    cur.execute("UPDATE movies SET embedding = %s WHERE id = %s;", (emb_str, mid))

conn.commit()

# Query by semantic similarity
user_query = "Movies about technology"
q_emb = model.encode(user_query)[:8]
q_str = "[" + ",".join(map(str, q_emb)) + "]"

cur.execute("""
SELECT id, title, synopsis, embedding <=> %s AS similarity
FROM movies
ORDER BY embedding <=> %s
LIMIT 3;
""", (q_str, q_str))

results = cur.fetchall()
print(f'Recommended movies for: "{user_query}"')
for r in results:
    print(f"ID {r[^0]} | {r[^1]} ({r[^3]:.4f}) - {r[^2]}")

cur.close()
conn.close()
```

Key points:

- Model: all-MiniLM-L6-v2; demo truncates to 8 dimensions to match VECTOR(8).[^1]
- In pgvector, operator <=> with vector_cosine_ops returns cosine distance: 0.0 perfect, higher is worse; thus lower is better.[^1]

Run:

```bash
python post_hands_on.py
```

---

## Expected Output

- Embeddings updated and printed per movie (8-d arrays).[^1]
- Top-3 recommendations for “Movies about technology,” e.g.:

```
Recommended movies for: "Movies about technology"
ID 2 | The Matrix (0.2726) - A computer hacker learns the true nature of reality and fights to free humanity.
ID 10 | Ex Machina (0.4468) - A programmer interacts with an AI to test its consciousness.
ID 1 | Inception (0.5127) - A skilled thief leads a team into dreams to steal secrets.
```

Verify embeddings persisted:

```sql
SELECT id, embedding FROM movies;
```

- Each row shows its stored 8-d vector.[^1]

---

## Notes and Variations

- Change distance metric by indexing with vector_l2_ops and ordering by embedding <-> query for L2 operators in some setups; current slides use vector_cosine_ops with <=>.[^1]
- Dimension choice: 8 is for demo; production often uses 384–1536+ depending on the model.[^1]
- Security: Avoid hardcoding passwords; use env vars or secrets managers in real deployments.[^1]
- Performance: HNSW parameters can be tuned; batch updates and server CPU/memory affect index build/search performance.[^1]

---

## Thank You

- Prof. Nivedita Kasturi
- Prof. Mahitha G (mahithag@pes.edu)[^1]

<div align="center">⁂</div>

[^1]: 69_VectorDB_Hands_On.pdf
