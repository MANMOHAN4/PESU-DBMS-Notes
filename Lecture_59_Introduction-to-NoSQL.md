# Introduction to NoSQL Systems

## What is NoSQL?

NoSQL databases ("not only SQL") are non-tabular databases that store data differently from relational tables.

Main types based on data model:
- Document
- Key-value
- Wide-column (column-based)
- Graph

## Why NoSQL Emerged?

Storage costs dramatically decreased. As a result:
- Applications needed to store and query massive amounts of data
- Data came in all shapes and sizes: structured, semi-structured, polymorphic
- Defining schema in advance became nearly impossible
- Rigid relational model became too restrictive

## Characteristics of NoSQL Systems

### Data Flexibility
- Allow developers to store huge amounts of unstructured data
- Give flexibility to handle diverse data types
- Schema-less or dynamic schema support
- Support for polymorphic data

### Distribution Focus
- Most NoSQL systems are distributed databases or storage systems
- Focus on scalability, replication, partitioning
- Horizontal scaling capabilities
- High availability prioritized

### Typical Features
- Distributed systems with emphasis on scalability
- Replication across multiple nodes
- Partitioning/sharding for load distribution
- High-performance data access
- Dynamic schema evolution
- Semi-structured storage
- Versioning and multiple versions per item
- Often ACID is sacrificed for availability

## Motivation: Why NoSQL?

Large-scale applications (e.g., free email services) handle:
- Millions of users
- Vast numbers of messages/data
- Need massive, scalable storage that remains highly available

Traditional SQL systems may be inappropriate when:
- System doesn't need all services (complex concurrency, rich SQL)
- Relational model's strict schema is too restrictive for evolving data
- Need simpler, lighter systems focused on specific use cases

## Famous NoSQL Systems

### Google Bigtable
- Wide-column store
- Used in Gmail, Maps, web indexing
- Apache HBase based on similar concepts

### Amazon DynamoDB
- Key-value/key-object store
- Managed cloud service
- Highly available and scalable

### Facebook Cassandra
- Combines key-value and column-based concepts
- Open source as Apache Cassandra
- Designed for distributed systems

### Document Stores
- MongoDB: Document-oriented database
- CouchDB: Another document-focused system

### Graph Databases
- Neo4j: Graph database for connected data
- GraphBase: Alternative graph database

## Distributed Characteristics

### Scalability
- Horizontal scalability online
- Data redistributed to new nodes without downtime
- Rebalancing techniques essential

### Availability and Replication
- Continuous availability is design goal
- Data replicated across 2+ nodes
- Failures don't interrupt service
- Reads served from replicas improving availability and performance

### Eventual Consistency
- Replicas converge to same state over time
- Not guaranteed immediate consistency
- More available than strong consistency

### Replication Models

**Master-Slave Model**
- One master accepts writes
- Updates propagate to slaves
- Eventual consistency achieved

**Master-Master Model**
- Reads and writes at any replica
- Immediate read consistency not guaranteed

### Sharding (Partitioning)
- Records distributed across nodes
- Load distributed for access patterns
- Combined with replication enhances availability

### High-Performance Access

Two common partitioning strategies:
- **Hashing**: Use h(K) to map key K to node/location
- **Range partitioning**: Assign key ranges [K_min, K_max] to specific nodes

## Data Model and Query Aspects

### Dynamic Schema
- Many systems don't require predefined schemas
- Data can be semi-structured and self-describing
- Accommodates polymorphism and evolution

### Query Interfaces
- Often less powerful than SQL by design
- Typical access via programming APIs
- Frequently keyed lookups in single collection
- Optimized for specific query patterns

### Versioning
- Many systems maintain multiple versions tagged with timestamps
- Aids conflict resolution
- Enables historical access

## Categories of NoSQL

### Document Stores
- Store documents in formats like JSON
- Examples: CouchDB, MongoDB
- Flexible schema, query via fields

### Key-Value Stores
- Simple model: key → value lookup
- Fast access by key to associated value (object/document/complex structure)
- Examples: DynamoDB, Redis
- Extremely fast, simple semantics

### Column-Based / Wide-Column Stores
- Partition tables by column families
- Vertical partitioning, separate files per family
- Examples: Cassandra, HBase
- Efficient for certain analytical queries

### Graph Databases
- Represent data as nodes and relationships
- Related nodes found by traversing edges
- Path expressions for queries
- Example: Neo4j
- Efficient for highly connected data

## CAP Theorem

Distributed, replicated systems face trade-off among three properties.

### Three Properties

**Consistency (C)**
- All nodes expose same value for replicated item to clients
- Strong consistency guarantee

**Availability (A)**
- Each request receives success or failure response
- System remains responsive to reads and writes
- No timeouts waiting indefinitely

**Partition Tolerance (P)**
- System continues operating despite network partitions
- Partitions split nodes into subgroups unable to communicate

### The Theorem
It is impossible to guarantee all three properties simultaneously in a replicated distributed system.

### Implications
- Designers must choose 2 of 3 properties to prioritize
- Many SQL applications favor consistency (C + P)
- Many NoSQL systems favor availability (A + P)
- Trade-offs defined by design choices

### Typical Strategies

**SQL Databases (C + P)**
- Prioritize consistency and partition tolerance
- Sacrifice availability in case of network partition
- Ensures data integrity and correctness

**NoSQL Systems (A + P)**
- Prioritize availability and partition tolerance
- Sacrifice immediate strong consistency
- Adopt eventual consistency instead of serializability
- Ensure high availability and responsiveness

## Q&A

**Q: Which of the following is a key-value database?**
- A. CouchDB
- B. MongoDB
- C. DynamoDB
- D. Neo4j

Answer: C

**Q: The "A" in CAP theorem stands for?**
- A. Atomicity
- B. Availability
- C. Adaptability
- D. Active

Answer: B

**Q: Consider the statements:**
- (i) NoSQL systems allow dynamic schema
- (ii) Vertical scalability is employed in NoSQL systems
- (iii) Sharding distributes load of accessing file records to multiple nodes
- (iv) Neo4j is a column-based NoSQL system

**Choose the correct set:**
- A. (i), (iv)
- B. (ii), (iii), (iv)
- C. (i), (iii)
- D. (ii), (iv)

Answer: C (Statements i and iii are correct; ii mentions vertical instead of horizontal scalability; iv wrongly identifies Neo4j as column-based when it's graph-based)
