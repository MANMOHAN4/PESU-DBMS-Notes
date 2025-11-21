# Continuation of Neo4j

## Property Graph Recap

- Nodes and relationships both have properties (key-value pairs)
- Nodes can have multiple labels; relationships are directed and typed
- Examples: EMPLOYEE, DEPARTMENT, PROJECT nodes with WORKS_FOR, MANAGES, LOCATED_IN, WORKS_ON relationships

## Cypher Basics and Conventions

### Labels
Use for entity types: :EMPLOYEE, :DEPARTMENT, :PROJECT

### Relationship Types
For connections: :WORKS_FOR, :MANAGES, :LOCATED_IN, :WORKS_ON

### Properties
Stored in map literals: {Fname:'John', Lname:'Smith', Empid:'E101'}

## Sample Domain: Company Graph

### Node Creation
```cypher
CREATE (:EMPLOYEE {Empid:'E101', Fname:'John', Lname:'Smith', Salary:65000});
CREATE (:DEPARTMENT {Dname:'Research', Dno:5});
CREATE (:PROJECT {Pname:'Alpha', Pno:10, Plocation:'Dallas'});
CREATE (:PERSON {Name:'Alice', Relationship:'Spouse'});
```

### Creating Relationships
```cypher
MATCH (e:EMPLOYEE {Empid:'E101'}), (d:DEPARTMENT {Dno:5})
CREATE (e)-[:WORKS_FOR]->(d);

MATCH (e:EMPLOYEE {Empid:'E101'}), (p:PROJECT {Pno:10})
CREATE (e)-[:WORKS_ON {Hours:20, Job:'Developer'}]->(p);

MATCH (mgr:EMPLOYEE {Empid:'E201'}), (d:DEPARTMENT {Dno:5})
CREATE (mgr)-[:MANAGES]->(d);
```

**Notes**:
- Relationships are directional
- Cypher pattern matching can traverse either direction with -[:TYPE]-

## Querying with MATCH and WHERE

### Basic Reads
```cypher
MATCH (e:EMPLOYEE)-[:WORKS_FOR]->(d:DEPARTMENT)
RETURN e.Empid, e.Fname, e.Lname, d.Dname;
```

### Filtering
```cypher
MATCH (e:EMPLOYEE)-[:WORKS_FOR]->(d:DEPARTMENT {Dname:'Research'})
WHERE e.Salary > 60000
RETURN e.Empid, e.Fname, e.Salary
ORDER BY e.Salary DESC
LIMIT 5;
```

### Aggregation
```cypher
MATCH (e:EMPLOYEE)-[:WORKS_FOR]->(d:DEPARTMENT)
RETURN d.Dname AS Department, avg(e.Salary) AS AvgSalary, count(e) AS Staff
ORDER BY AvgSalary DESC;
```

### Path Traversal
```cypher
MATCH p = (e:EMPLOYEE {Empid:'E101'})-[:WORKS_ON]->(:PROJECT)<-[:WORKS_ON]-(:EMPLOYEE)
RETURN p;
```

## Handling Relationship Properties

WORKS_ON commonly stores Hours, Job, Role for analytics.

```cypher
MATCH (:EMPLOYEE {Empid:'E101'})-[w:WORKS_ON]->(p:PROJECT)
RETURN p.Pname, w.Hours
ORDER BY w.Hours DESC;
```

Sum of hours by project:
```cypher
MATCH (e:EMPLOYEE)-[w:WORKS_ON]->(p:PROJECT)
RETURN p.Pname, sum(w.Hours) AS TotalHours
ORDER BY TotalHours DESC;
```

## Updates and Deletes

### Set/Update Properties
```cypher
MATCH (e:EMPLOYEE {Empid:'E101'})
SET e.Salary = 70000, e.Title = 'Senior Developer'
RETURN e.Empid, e.Salary, e.Title;
```

### Add Relationships (MERGE)
```cypher
MATCH (e:EMPLOYEE {Empid:'E101'}), (d:DEPARTMENT {Dno:5})
MERGE (e)-[:WORKS_FOR]->(d);
```

MERGE either matches existing pattern or creates it (idempotent).

### Deleting Nodes/Relationships
```cypher
MATCH (e:EMPLOYEE {Empid:'E199'})-[r]-()
DELETE r, e;
```

Or use DETACH DELETE for safer deletion:
```cypher
MATCH (e:EMPLOYEE {Empid:'E199'})
DETACH DELETE e;
```

## Constraints and Indexes

### Uniqueness Constraint
```cypher
CREATE CONSTRAINT emp_empid_unique IF NOT EXISTS
FOR (e:EMPLOYEE) REQUIRE e.Empid IS UNIQUE;
```

### Property Index
```cypher
CREATE INDEX dept_dno_index IF NOT EXISTS
FOR (d:DEPARTMENT) ON (d.Dno);
```

Constraints and indexes speed lookups and enforce data integrity.

## Pattern Examples

### Employees in Department Working on Projects
```cypher
MATCH (d:DEPARTMENT {Dname:'Research'})<-[:WORKS_FOR]-(e:EMPLOYEE)-[w:WORKS_ON]->(p:PROJECT)
RETURN e.Empid, e.Fname, p.Pname, w.Hours
ORDER BY w.Hours DESC;
```

### Find Managers and Their Departments
```cypher
MATCH (m:EMPLOYEE)-[:MANAGES]->(d:DEPARTMENT)
RETURN m.Empid, m.Fname, d.Dname;
```

### Who Works with Whom
```cypher
MATCH (a:EMPLOYEE)-[:WORKS_ON]->(p:PROJECT)<-[:WORKS_ON]-(b:EMPLOYEE)
WHERE a.Empid <> b.Empid
RETURN a.Empid AS E1, b.Empid AS E2, collect(DISTINCT p.Pname) AS Projects
ORDER BY size(Projects) DESC;
```

## Practical Modeling Notes

- Use multiple labels for subtypes/roles (e.g., :EMPLOYEE:MANAGER)
- Store relationship semantics in types and properties
- Model locations and other entities as nodes with typed relationships
- Use MERGE for idempotent ETL loads avoiding duplicates
- Choose label/property names carefully for querying efficiency

## Practice Exercises

1) Create employees E101, E102, E201 with names, salaries; departments Research (5) and Admin (7); projects Alpha (10, Dallas) and Beta (20, Austin); create relationships WORKS_FOR, WORKS_ON with Hours, MANAGES for E201

2) Query:
- List employees in Research with salary > 60k sorted descending
- Sum hours per project; top project by total hours
- Co-worker pairs on same project
- Managers and their departments

3) Add uniqueness constraints on Empid; create indexes on Dno, Pno; update salaries; delete test employee
