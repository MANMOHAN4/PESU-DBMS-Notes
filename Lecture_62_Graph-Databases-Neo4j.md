# Graph Databases (Neo4j)

## What Are Graph Databases?

Graph databases are NoSQL systems where data is represented as:
- **Nodes** (vertices): Represent entities (e.g., Person, Product)
- **Relationships** (edges): Represent connections (e.g., FRIEND_OF, PURCHASED, WORKS_FOR)

Neo4j is an open-source graph database implemented in Java, optimized for connected data and relationship-heavy queries.

## Why Neo4j?

### Traditional Database Limitations
Relational databases struggle with queries involving many joins across relationship-heavy data.

### Neo4j Advantages
- Follows direct links for fast traversals instead of rebuilding joins
- Optimized for connected data and relationship queries
- Intuitive modeling of real-world networks as graphs
- Fast path traversals essential for highly connected problems

### Flexible Schema
- Easily add nodes, relationships, properties
- No rigid upfront schema definition
- Accommodates evolving data requirements

### Query Language
- Cypher: Readable pattern matching language
- Built-in algorithms: shortest path, PageRank, community detection
- Powerful for graph traversals

## Real-Life Applications

### Social Networks
Friend suggestions, feed recommendations

### E-commerce/Streaming
Product recommendations based on user behavior and similar items

### Banking/Finance
Fraud detection from graph patterns revealing suspicious transaction chains

### Knowledge Graphs
Link and search large datasets connecting related information

### IT/Network Management
Model system dependencies showing how apps/services connect

## Neo4j Data Model

### Property Graph Basics
- **Nodes and Relationships** both have properties (key-value pairs)
- **Properties**: Primitives or arrays of single primitive type (String, int, int[])
- **Labels**: Nodes tagged with zero or more labels grouping similar entities
- **Relationships**: Directed, have types, optional properties
- **Properties format**: {Lname:'Smith', Fname:'John', Minit:'B'}

### Conceptual Mapping
- Nodes ↔ entities
- Labels ↔ entity types/subclasses
- Relationships ↔ relationship instances
- Properties ↔ attributes

## Creating Nodes

```cypher
CREATE (:Person {name: 'Alice', age: 30});
CREATE (:Person:Employee {name: 'Bob', age: 28, empId: 'E101'});
```

Multiple labels indicate subtype-like roles (Person:Employee:Manager on same node).

## Creating Relationships

```cypher
MATCH (a:Person {name:'Alice'}), (b:Person {name:'Bob'})
CREATE (a)-[:FRIEND_OF {since: 2021}]->(b);
```

Relationships are directed but traversable in either direction; types include WORKS_FOR, MANAGES, LOCATED_IN, WORKS_ON.

## Matching Patterns

```cypher
MATCH (p:Person)-[:FRIEND_OF]->(q:Person)
RETURN p.name, q.name;
```

Paths specify traversals used to match patterns from start node through relationships to end nodes.

## Optional Schema

While Neo4j allows schema-less usage:
- Can add indexes and constraints based on labels/properties
- Enforce uniqueness constraints (e.g., Person.email unique)
- Index properties on specific labels for lookup optimization (e.g., EMPLOYEE.Empid, DEPARTMENT.Dno)

## Key Concepts

- **Node**: Entity instance with optional labels and properties
- **Relationship**: Directed connection with type, start node, end node, optional properties
- **Label**: Tag on node grouping similar entities, enables index/constraint scoping
- **Relationship Type**: Tag on relationships grouping similar connections
- **Path**: One or more nodes connected by relationships forming traversal result

## Q&A

**Q: In Neo4j, what do nodes typically represent?**
- A. Relationships between entities
- B. Data attributes only
- C. Entities such as people, places, or things
- D. SQL tables

Answer: C

**Q: What is the storage model of Neo4j based on?**
- A. Tables and rows
- B. Documents and collections
- C. Key-value pairs
- D. Nodes and relationships

Answer: D

**Q: What is a key advantage of Neo4j over relational databases?**
- A. Better file storage
- B. Faster disk I/O
- C. Efficient handling of highly connected data
- D. SQL support

Answer: C
