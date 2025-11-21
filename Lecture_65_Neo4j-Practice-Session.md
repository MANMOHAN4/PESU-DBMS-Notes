# Neo4j Practice Session

## Node Creation and Viewing

### Create Single Node
```cypher
CREATE (n)
```
Creates a single node with identifier n (not the node name). Output: "Created one node"

### View Nodes
```cypher
MATCH (n) RETURN n
```
Fetches created nodes. Output shows internal id (auto-generated from 0), unique per node, used for internal reference.

### Create Multiple Nodes
```cypher
CREATE (n), (m)
```
Creates two nodes with identifiers n and m. Output: "Created two nodes"

### View All Created Nodes
```cypher
MATCH (n) RETURN n
```
Output: 3 nodes (includes previously created nodes)

### Limit Results
```cypher
MATCH (n) RETURN n LIMIT 2
```
Restricts output to first 2 nodes using LIMIT clause.

---

## Searching Nodes by Internal ID

### Find Node by Internal ID
```cypher
MATCH (n:Person) WHERE n.nodeId = 1 RETURN n
```
Returns node with internal id = 1.

### Comparison Operators
```cypher
MATCH (n:Person) WHERE n.nodeId <= 6 RETURN n
```
Operators: <, >, <=, >=, <>. Returns all 7 nodes (id 0-6).

### Search Multiple Nodes with IN Clause
```cypher
MATCH (n:Person) WHERE n.nodeId IN [1, 2, 0] RETURN n
```
Returns 3 nodes with id 0, 1, 2.

---

## Deleting Nodes

### Delete Single Node
```cypher
MATCH (n:Person) WHERE n.nodeId = 1 DELETE n
```
Deletes node matching WHERE condition. Output: "Deleted one node"

### Delete Multiple Nodes
```cypher
MATCH (n:Person) WHERE n.nodeId IN [2, 3] DELETE n
```
Deletes nodes with id 2 and 3. Output: "Deleted two nodes"

### Delete All Nodes
```cypher
MATCH (n) DELETE n
```
Deletes all nodes (only if no relationships exist). Output: "Deleted x nodes"

### Delete Entire Database
```
sudo service neo4j stop
sudo rm -rf /var/lib/neo4j/data/*
sudo service neo4j start
```

Stop server, delete graph directory, restart server. All data erased.

### Neo4j Server Commands
```
sudo service neo4j start    — Start server
sudo service neo4j stop     — Stop server
sudo service neo4j status   — Check running/stopped status
```

---

## Labels (Tags for Grouping Nodes)

Labels are optional tags grouping similar nodes. Each node can have multiple labels. Any Unicode string valid as label. Convention: CamelCase.

### Create Node with Label
```cypher
CREATE (n:Person { nodeId: 1 })
```
Creates node with label Person and property nodeId=1. Output: "Added 1 label, created 1 node"

### Search Nodes by Label
```cypher
MATCH (n:Person) RETURN n
```
Returns all nodes with label Person.

### Create Node with Multiple Labels
```cypher
CREATE (n:Person:Indian)
```
Creates node with two labels (Person and Indian). Output: "Added 2 labels, created 1 node"

### Search by Multiple Labels (AND)
```cypher
MATCH (n:Person:Indian) RETURN n
```
Returns nodes with BOTH Person AND Indian labels.

### Search by Multiple Labels (OR)
```cypher
MATCH (n) WHERE n:Person OR n:Indian RETURN n
```
Returns nodes with Person label OR Indian label OR both.

---

## Adding Labels to Existing Nodes

### Add Label to All Nodes
```cypher
MATCH (n) SET n:Employee RETURN n
```
Adds Employee label to all nodes.

### Add Multiple Labels to All Nodes
```cypher
MATCH (n) SET n:Vacation:Food RETURN n
```
Adds both Vacation and Food labels to all nodes.

### Add Label to Single Node by ID
```cypher
MATCH (n) WHERE n.nodeId = 1 SET n:Manager RETURN n
```
Adds Manager label to node with nodeId=1.

### Add Label to Multiple Selected Nodes
```cypher
MATCH (n) WHERE n.nodeId IN [2, 3] SET n:TeamLeader RETURN n
```
Adds TeamLeader label to nodes with id 2 and 3.

---

## Removing Labels

### Remove Label from All Nodes
```cypher
MATCH (n) REMOVE n:Person RETURN n
```
Removes Person label from all nodes.

### Remove Label from Selected Nodes
```cypher
MATCH (n) WHERE n.nodeId IN [2, 3] REMOVE n:Employee RETURN n
```
Removes Employee label from nodes with id 2 and 3.

### Remove Multiple Labels from All Nodes
```cypher
MATCH (n) REMOVE n:Food:Vacation RETURN n
```
Removes Food and Vacation labels from all nodes having either.

### Update Label (Replace)
```cypher
MATCH (n) WHERE n.nodeId = 0 REMOVE n:Manager SET n:Director RETURN n
```
Changes node's label from Manager to Director.

---

## Listing and Counting Labels

### List All Labels in Database
```cypher
MATCH (n) RETURN DISTINCT labels(n)
```
Displays all existing labels. DISTINCT removes duplicates. Labels are case-sensitive.

### List Labels for Specific Node
```cypher
MATCH (n) WHERE n.nodeId = 0 RETURN DISTINCT labels(n)
```
Lists all labels for node with id=0.

### Count Distinct Labels
```cypher
MATCH (n) RETURN DISTINCT COUNT(labels(n))
```
Returns number of distinct label combinations in database.

### List Labels with Counts
```cypher
MATCH (n) RETURN DISTINCT COUNT(labels(n)), labels(n)
```
Returns each label set with corresponding count.

### Delete Nodes by Label
```cypher
MATCH (n) WHERE n:TeamLeader DELETE n
```
Deletes all nodes having TeamLeader label.

---

## Properties (Attributes of Nodes)

Properties are optional key-value pairs stored as {}. A node can have zero or more properties. Null is not a valid property value.

### Create Node with Single Property
```cypher
CREATE (x:Book {title: "The Bengal Tiger"}) RETURN x
```
Creates Book node with title property.

### Create Node with Multiple Properties
```cypher
CREATE (x:Book {title: "Database System", author: "Navathe", publisher: "Pearson"}) RETURN x
```
Creates Book node with 3 properties. Property names are case-sensitive.

### Property Names
- Case-sensitive
- Contain underscore and alphanumeric characters
- Must start with a letter
- Example: author and Author are different properties

### Property Data Types

**Primitive**: Boolean, Integer, Double, Long, Character, String

**Arrays of primitives**: Boolean[], Integer[], Double[], Long[], Character[], String[]

### Create Node with Mixed Data Types
```cypher
CREATE (x:Book {
  title: "the lives of others",
  author: "Neel",
  publisher: ["Chato & Windos", "w norton"],
  price: 285.00,
  pages: 528,
  instock: false
}) RETURN x
```

- title, author: String
- publisher: String array
- price: Double (float)
- pages: Integer
- instock: Boolean

### Mixed-Type Arrays
```cypher
CREATE (x:Book {details: ["the nagas", 172.00, 398]}) RETURN x
```

Not allowed. Collections must contain single primitive type. This treats all as strings for the first element's type.

Correct:
```cypher
CREATE (x:Book {details: ["the nagas", "172.00", "398"]}) RETURN x
```

All strings.

---

## Filtering Nodes by Properties

### Direct Property Matching
```cypher
MATCH (n:Book {author: "Chetan"}) RETURN n
```
Returns all Book nodes with author property = "Chetan".

### Case-Sensitive Matching
```cypher
MATCH (n:Book1 {Author: "Elmasri"}) RETURN n
```
Author (capital A) differs from author (lowercase).

### Multiple Conditions with Operators
```cypher
MATCH (n:Book) WHERE n.price < 1000 AND (n.author = "Neel" OR n.author = "Navathe") RETURN n
```
Combines AND, OR, < operators for complex filtering.

---

## Updating Properties

### Update Single Property
```cypher
MATCH (n) WHERE n.title = "the lives of others" SET n.title = "The lives" RETURN n
```
Updates title from "the lives of others" to "The lives".

### Update Multiple Properties
```cypher
MATCH (n:Book {title: "existing title"}) SET n.author = "Gosh", n.price = 324.00, n.stock = true RETURN n
```
Updates multiple properties in single SET clause.

---

## Deleting Properties

### Method 1: Set to NULL
```cypher
MATCH (n:Book {author: "Neel"}) SET n.publisher = NULL RETURN n
```
Removes publisher property by setting to NULL.

### Method 2: WHERE with NULL
```cypher
MATCH (n:Book) WHERE n.author = "Neel" SET n.publisher = NULL RETURN n
```
Same effect using WHERE clause.

### Remove All Instances of Property
```cypher
MATCH (n) REMOVE n.pages RETURN n
```
Removes pages property from all nodes.

### Remove Property from Labeled Nodes
```cypher
MATCH (n) WHERE n:Bestseller REMOVE n.title RETURN n
```
Removes title from nodes with Bestseller label.

### Delete Nodes by Property Value
```cypher
MATCH (n) WHERE n.stock = false DELETE n
```
Deletes nodes where stock property = false.

### Remove Property from Specific Node
```cypher
MATCH (n) WHERE n.nodeId = 1 REMOVE n.price RETURN n
```
Removes price property from node with nodeId=1.

---

## Creating Relationships

### Format
```
CREATE (node1)-[:RelationshipType]->(node2)
```

Relationships are directional: - and hyphen indicate direction, -> shows arrow direction.

### Example: Create Author and Book Relationship
```cypher
CREATE (chetan:author {name: "Chetan", dob: 1980, age: 41})
CREATE (nagas:Book {title: "Nagas", price: 200, publication: "westland publication"})
CREATE (chetan)-[r:author_of]->(nagas)
```

First CREATE: author node. Second CREATE: Book node. Third CREATE: relationship between them.

---

## Creating Complete Paths

A path is continuous relationships across multiple nodes.

```cypher
CREATE p = (Node1 {properties})-[:Relationship_Type]->(Node2 {properties})-[:Relationship_Type]->(Node3 {properties}) RETURN p
```

Creates and returns the complete path.
