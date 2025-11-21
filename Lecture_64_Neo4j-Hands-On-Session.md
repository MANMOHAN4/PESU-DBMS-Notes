# Neo4j Hands-On Session

## Setup Steps

### Step 1: Login to Neo4j
Access Neo4j Aura at neo4j.com and click "Get started Free" to create an account using Gmail.

### Step 2: Create Instance
- Navigate to Aura Dashboard
- Click "Create instance"
- Configure and create your first graph database instance
- Download credentials for connection

### Step 3: Connect to Instance
- Click "Connect" on your instance
- Click "Query" to open the Cypher editor
- Begin writing queries

### Step 4: Verify Connection
On successful connection, the editor displays:
- Database information (Nodes, Relationships)
- Sample Movie dataset with Person and Movie nodes
- Relationship types: ACTED_IN, DIRECTED, FOLLOWS, PRODUCED, REVIEWED, WROTE
- Properties: name, title, released, rating, roles, summary, tagline, born

---

## Basic Read Queries

### Query 1: Return All Nodes
```cypher
MATCH (n) RETURN n
```
Output: All 171 nodes in database (171 Movie and Person nodes)

### Query 2: Return All Movie Nodes
```cypher
MATCH (n:Movie) RETURN n
```
Output: 38 Movie nodes with their properties

### Query 3: Find Specific Movie by Title (Property Match)
```cypher
MATCH (n:Movie {title: 'Top Gun'}) RETURN n
```
Match movie where title property equals 'Top Gun' exactly.

### Query 4: Find Specific Movie by Title (WHERE Clause)
```cypher
MATCH (n:Movie) 
WHERE n.title = 'Top Gun' 
RETURN n
```
Alternative using WHERE for property filtering.

### Query 5: Movies Released After Year 2000
```cypher
MATCH (m:Movie)
WHERE m.released >= 2000
RETURN m.title, m.votes
```
Filter movies by released year and return title and votes only.

### Query 6: People Whose Name Starts with "An" or Ends with "on"
```cypher
MATCH (person:Person)
WHERE person.name STARTS WITH 'An' OR person.name ENDS WITH 'on'
RETURN person
```
String pattern matching with STARTS WITH and ENDS WITH operators.

---

## Relationship Traversal Queries

### Query 7: Actors and Movie
```cypher
MATCH (actor:Person)-[:ACTED_IN]->(movie:Movie)
WHERE movie.title = 'The Green Mile'
RETURN actor, movie
```
Find all actors who acted in specific movie; return both nodes.

### Query 8: Just the Actors
```cypher
MATCH (actor:Person)-[:ACTED_IN]->(movie:Movie)
WHERE movie.title = 'The Green Mile'
RETURN actor
```
Return only actor nodes, not the movie.

### Query 9: Director(s) of Movie
```cypher
MATCH (director:Person)-[:DIRECTED]->(movie:Movie)
WHERE movie.title = 'The Green Mile'
RETURN director, movie
```
Find director(s) by traversing DIRECTED relationships.

### Query 10: All Directors and Their Movies
```cypher
MATCH (director:Person)-[:DIRECTED]->(movie:Movie)
RETURN director, movie
```
Global query without WHERE; returns all director-movie pairs.

### Query 11: High-Rating Reviewers
```cypher
MATCH (reviewer:Person)-[r:REVIEWED]->(movie:Movie)
WHERE r.rating > 68
RETURN reviewer, movie
```
Filter by relationship property (rating on REVIEWED relationship).

### Query 12: Specific Writer, Movies, and Actors
```cypher
MATCH (writer:Person {name: 'Cameron Crowe'})-[:WROTE]->(m1:Movie)
MATCH (actor:Person)-[:ACTED_IN]->(m1:Movie)
RETURN writer, m1, actor
```
Two MATCH clauses: first finds movies written by Cameron Crowe, second finds actors in those movies.

---

## Aggregation Queries

### Query 13: Directors and Movie Count
```cypher
MATCH (director:Person)-[:DIRECTED]->(m:Movie)
RETURN director.name, COUNT(m)
```
Group by director and count movies directed.

### Query 14: Reviewers and Average Rating
```cypher
MATCH (reviewer:Person)-[r:REVIEWED]->(m:Movie)
RETURN reviewer.name, AVG(r.rating)
```
Calculate average rating per reviewer across all their reviews.

### Query 15: Reviewers and Average Rating (Ordered)
```cypher
MATCH (reviewer:Person)-[r:REVIEWED]->(m:Movie)
RETURN reviewer.name, AVG(r.rating)
ORDER BY AVG(r.rating)
```
Same query with results ordered by average rating ascending.

---

## Create Queries

### Query 16-18: Create Movie Nodes
```cypher
CREATE (n:MOVIE {title: 'Movie1'}) RETURN n
CREATE (n:MOVIE {title: 'Movie2', released: 2001}) RETURN n
CREATE (n:MOVIE {title: 'Movie3'}) RETURN n
```
Create three movies; Movie2 includes release year property.

### Query 19-22: Create Person Nodes
```cypher
CREATE (n:PERSON {name: 'Person1'}) RETURN n
CREATE (n:PERSON {name: 'Person2'}) RETURN n
CREATE (n:PERSON {name: 'Person3'}) RETURN n
CREATE (n:PERSON {name: 'Person4'}) RETURN n
```
Create four person nodes.

---

## Relationship Creation Queries

### Query 23-25: Create ACTED_IN Relationships
```cypher
MATCH (a:PERSON), (b:MOVIE)
WHERE a.name = 'Person1' AND b.title = 'Movie1'
CREATE (a)-[:ACTED_IN]->(b)
RETURN a, b

MATCH (a:PERSON), (b:MOVIE)
WHERE a.name = 'Person3' AND b.title = 'Movie2'
CREATE (a)-[:ACTED_IN]->(b)
RETURN a, b

MATCH (a:PERSON), (b:MOVIE)
WHERE a.name = 'Person4' AND b.title = 'Movie2'
CREATE (a)-[:ACTED_IN]->(b)
RETURN a, b
```
Create relationships between persons and movies; MATCH first, then CREATE relationship.

---

## Update Queries

### Query 26: Update Property
```cypher
MATCH (p:PERSON {name: 'Person1'})
SET p.age = 54
RETURN p
```
Add/update age property for Person1 to 54.

### Query 27: Add Label
```cypher
MATCH (p:PERSON {name: 'Person1'})
SET p:ACTOR
RETURN p
```
Add ACTOR label to Person1 (now has both PERSON and ACTOR labels).

---

## Delete Queries

### Query 28: Delete Node Without Relationships
```cypher
MATCH (m:MOVIE {title: 'Movie3'})
DELETE m
```
Delete Movie3 (must have no relationships).

### Query 29: Delete with Relationships
```cypher
MATCH (m:MOVIE {title: 'Movie2'})
DELETE m
```
This fails if Movie2 has relationships; use DETACH DELETE instead.

### Query 30: Detach Delete (Node and Relationships)
```cypher
MATCH (m:MOVIE {title: 'Movie2'})
DETACH DELETE m
```
Delete node and all its relationships automatically.

### Query 31: View All Remaining Nodes
```cypher
MATCH (n)
RETURN n
```
Returns all nodes currently in database.

### Query 32: Detach Delete All Nodes
```cypher
MATCH (n)
DETACH DELETE n
```
Clear entire database by removing all nodes and relationships.

---

## Key Query Patterns

| Pattern | Syntax |
|---------|--------|
| Match pattern | MATCH (a:Label)-[:REL_TYPE]->(b:Label) |
| Filter properties | WHERE a.property = value |
| Filter comparisons | WHERE a.age > 30 |
| String matching | WHERE name STARTS WITH 'An' OR name ENDS WITH 'on' |
| Aggregate | COUNT(n), AVG(rel.property), SUM() |
| Order results | ORDER BY property ASC/DESC |
| Limit results | LIMIT 10 |
| Return specific fields | RETURN a.name, b.title |
| Create node | CREATE (n:Label {prop: value}) |
| Create relationship | CREATE (a)-[:TYPE]->(b) |
| Update property | SET a.prop = value |
| Add label | SET a:NewLabel |
| Delete node | DELETE n |
| Delete with relationships | DETACH DELETE n |

---

## Important Notes

- Property matching and WHERE clauses both work; WHERE is more flexible
- Relationships are directional; use -[:TYPE]-> for forward traversal
- DETACH DELETE necessary when deleting nodes with relationships
- MATCH always comes before CREATE, SET, or DELETE in complex queries
- Multiple MATCH clauses can be chained to find intermediate nodes
- Aggregation functions work on returned values after MATCH
