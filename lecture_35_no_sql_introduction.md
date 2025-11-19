# Lecture 35 – Introduction to NoSQL Systems

### Prepared by: Prof. Shilpa S
*Department of Computer Science and Engineering*

---

# What is NoSQL?
- **NoSQL** stands for **Not Only SQL**.
- Refers to a broad class of **non‑tabular databases**.
- Data is **not stored in relational tables**.
- Supports **flexible schemas**, high scalability, and distributed architectures.
- Designed to store **huge amounts of unstructured, semi‑structured, or polymorphic data**.

### Why NoSQL?
- Traditional SQL systems expect a fixed schema.
- Modern applications generate **vast and diverse data**.
- Defining schema in advance is often impossible.
- NoSQL databases allow **dynamic schemas** and flexible data models.

---

# NoSQL Data Models
NoSQL systems differ based on how data is stored.

1. **Document Stores** (JSON / BSON based)
   - Examples: **MongoDB, CouchDB**
   - Store data as self‑describing documents.

2. **Key‑Value Stores**
   - Examples: **DynamoDB, Redis**
   - Fast access using a key to fetch the associated value (document/object/record).

3. **Column‑Based / Wide Column Stores**
   - Examples: **Cassandra, HBase**
   - Tables partitioned by **column families** (vertical partitioning).

4. **Graph Databases**
   - Examples: **Neo4j, GraphBase**
   - Data stored as nodes and edges, supports graph traversal queries.

---

# Why NoSQL Became Popular
- Storage costs decreased → applications started storing **massive datasets**.
- Many apps need to store semi‑structured/unstructured data.
- Real‑world example:
  - Email services (Gmail, Yahoo Mail)
  - Millions of users × thousands of emails → huge data.
  - SQL may be:
    1. Overkill (complex services not required)
    2. Too rigid (strict schema)
- NoSQL gives dynamic schema and flexibility.

---

# Companies Using NoSQL
- **Google BigTable** → wide column store (used in Gmail, Maps, Indexing)
- **Apache HBase** → open‑source form of BigTable
- **Amazon DynamoDB** → key‑value / key‑object store
- **Facebook Cassandra** → hybrid of key‑value + column store
- **MongoDB, CouchDB** → document stores
- **Neo4j** → graph database

---

# Characteristics of NoSQL Systems
Two broad categories:

## 1. Characteristics related to **Distributed Databases / Systems**

### 1. Scalability
- NoSQL supports **horizontal scalability**.
- System remains operational while new nodes are added.
- Requires efficient **data redistribution** without downtime.

### 2. Availability, Replication & Eventual Consistency
- Data is automatically **replicated** across nodes.
- Improves availability: if one node fails, data still accessible.
- Reads may be served from any replica.
- Consistency may not be immediate → **eventual consistency**.

### 3. Replication Models
- **Master‑Slave**
  - Writes go to master.
  - Propagated to slaves.
- **Master‑Master**
  - Reads/writes allowed at any replica.
  - May see different values at different nodes.

### 4. Sharding
- File records are partitioned across nodes.
- **Shards** may also be replicated.
- Improves load balancing + availability.

### 5. High‑Performance Data Access
Using:
- **Hashing**: h(K) → location of object key K.
- **Range partitioning**: keys in ranges (Kmin to Kmax) assigned to specific locations.

---

## 2. Characteristics Related to Data Models & Query Languages

### 1. Dynamic Schema
- No need for predefined schema.
- Supports semi‑structured / self‑describing data.

### 2. Less Powerful Query Languages
- Many NoSQL systems do **not** provide SQL‑like queries.
- Use API‑based functions for read/write operations.

### 3. Versioning
- Some NoSQL systems store **multiple versions** of a data item.
- Each version timestamped.

---

# Categories of NoSQL Systems – Summary
1. **Document Store** → MongoDB, CouchDB
2. **Key‑Value Store** → DynamoDB, Redis
3. **Wide Column Store** → Cassandra, HBase
4. **Graph Database** → Neo4j

---

# CAP Theorem
The CAP theorem explains the trade‑off in distributed systems.

### CAP stands for:
- **C – Consistency** → all replicas show same data
- **A – Availability** → every request receives a valid response
- **P – Partition Tolerance** → system continues working despite network partitions

### CAP Theorem Statement
> "A distributed system with replication **cannot guarantee all three**: consistency, availability, and partition tolerance—simultaneously."

### What does this mean?
- System designers must pick **two of the three**.
- SQL (traditional systems) → prioritize **Consistency**.
- NoSQL → prioritize **Availability + Partition Tolerance**.
- NoSQL uses **eventual consistency** instead of strict serializability.

---

# Q&A (from slides)
1. **Which is a key‑value database?**
   - (c) **DynamoDB**

2. **The A in CAP theorem stands for?**
   - (b) **Availability**

3. **Identify the correct statements:**
   - (i) NoSQL allows dynamic schema → TRUE
   - (ii) Vertical scalability used in NoSQL → FALSE (horizontal used)
   - (iii) Sharding distributes load → TRUE
   - (iv) Neo4j is column‑based → FALSE (Neo4j is graph‑based)

Correct option: **(d) (i), (iii)**

---

# End of Lecture 35 – Complete Notes

