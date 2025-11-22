# Query Processing

## Introduction to Query Processing

**Query Processing** includes:
1. Translation of high-level SQL queries into low-level expressions
2. Query optimization
3. Actual execution of the query to produce results

### High-level to Low-level Translation

- High-level SQL is translated into low-level operations/expressions
- Translation is systematic and methodical across query pipeline
- Applied at physical storage level and during query optimization
- Final low-level plan is executed efficiently

---

## Query Processing Pipeline

### Stage 1: Scanning, Parsing, and Validation

**Scanner**: Identifies query tokens
- SQL keywords
- Attribute names
- Relation names

**Parser**: Checks query syntax
- Verifies syntax rules compliance
- Ensures proper query formulation

**Validation**: Semantic checking
- All attribute names are valid
- All relation names are valid
- Names are meaningful in database schema

### Stage 2: Query Representation

**Query Tree**: Internal data structure
- Leaf nodes: relations (tables)
- Internal nodes: relational algebra operators (SELECT σ, JOIN ⋈, PROJECT π)
- Root node: final query output

**Query Graph**: Alternative representation
- Nodes represent relations
- Edges represent join conditions/relationships
- Directed acyclic graph (DAG)

### Stage 3: Optimization and Execution

**Query Optimizer**: Produces good execution plan
- Considers multiple possible execution strategies
- Chooses most efficient one based on cost

**Code Generator**: Generates executable code
- Produces code to execute chosen plan

**Runtime Processor**: Executes query
- Runs generated code
- Produces query result

---

## Query Decomposition into Query Blocks

SQL queries decomposed into **query blocks** — basic units for translation:

**Query Block Characteristics**:
- Contains single SELECT-FROM-WHERE expression
- May contain GROUP BY and HAVING clauses
- Forms basic unit translatable to algebraic operators

### Example: Nested Query

**Outer block**: Operates on result of inner block

**Inner block**: Simple SELECT-FROM-WHERE query

Each block translated separately to relational algebra, then optimized.

---

## Translating SQL to Relational Algebra

### Process

**Step 1**: Identify SELECT, FROM, WHERE clauses

**Step 2**: Map to algebraic operators
- SELECT → σ (selection/restriction)
- FROM → base relations
- WHERE → selection conditions
- JOIN conditions from WHERE clause

**Step 3**: Map to algebraic expressions
- Inner queries map to intermediate results
- Composed queries map to algebraic compositions

### Example Translation

**SQL Query**:
```sql
SELECT E.name, D.dname
FROM Employee E, Department D
WHERE E.dept_id = D.dept_id AND E.salary > 50000
```

**Relational Algebra**:
```
π(E.name, D.dname)(σ(E.salary > 50000 AND E.dept_id = D.dept_id)(E × D))
```

Or optimized as:
```
π(E.name, D.dname)(σ(E.salary > 50000)(E) ⋈ D)
```

---

## Additional Operators for Subqueries

### Semi-Join (S=)

**Purpose**: Handle EXISTS, IN, and ANY subqueries

**Syntax**: T1.X S= T2.Y

**Behavior**:
- T1 row returned AS SOON AS match found with T2.Y
- Stops searching after first match
- Contrast: inner join finds ALL matches

**Efficiency**: Reduces redundant joins and intermediate result sizes

### Example Semi-Join

```sql
SELECT * FROM Employee E
WHERE EXISTS (SELECT 1 FROM Project P WHERE E.proj_id = P.proj_id)
```

Converts internally to:
```
Employee S= Project ON emp.proj_id = proj.proj_id
```

Result: Only one match per employee needed, then move to next.

### Anti-Join (A=)

**Purpose**: Handle NOT EXISTS, NOT IN, and ALL subqueries

**Syntax**: T1.x A= T2.y

**Behavior**:
- T1 row REJECTED as soon as match found with T2.y
- T1 row RETURNED only if NO match with T2.y
- Early termination prevents unnecessary scanning

### Example Anti-Join

```sql
SELECT * FROM Employee E
WHERE NOT EXISTS (SELECT 1 FROM Department D WHERE E.dept_id = D.dept_id)
```

Converts internally to:
```
Employee A= Department ON emp.dept_id = dept.dept_id
```

Result: Employees with no matching department excluded efficiently.

---

## Algorithms for SELECT Operation

**SELECT operation** is a search operation to locate records satisfying condition.

### Search Methods for Simple Selection

#### S1: Linear Search (Brute Force)

**Algorithm**:
1. Retrieve every record in file
2. Test whether attribute values satisfy condition
3. Return matching records

**Efficiency**:
- Must scan all records
- Cost: O(n) where n = number of records
- Disk blocks read into buffer sequentially

**Use when**:
- No index available
- Query retrieves most records anyway
- Small file size

#### S2: Binary Search

**Prerequisites**:
- File physically sorted on search attribute
- Equality comparison on ordering attribute

**Algorithm**:
1. Start at middle block
2. Compare with search value
3. Eliminate half of remaining records
4. Repeat until found

**Efficiency**:
- Cost: O(log₂ n)
- More efficient than linear search
- Limited to equality/sorted conditions

#### S3a: Primary Index Search

**Prerequisites**:
- Equality comparison on indexed key attribute
- Primary index exists on attribute

**Algorithm**:
1. Use index to locate record
2. Retrieve that single record

**Efficiency**:
- Cost: O(1) for exact match
- Very fast for single record retrieval

**Use when**:
- Looking for specific record by primary key
- E.g., Ssn = '123456789'

#### S3b: Hash Key Search

**Prerequisites**:
- Equality comparison on hashed attribute
- Hash key exists on attribute

**Algorithm**:
1. Apply hash function to search value
2. Jump directly to hash bucket
3. Retrieve record(s) in that bucket

**Efficiency**:
- Cost: O(1) average case
- Very fast for exact match
- Hash collisions may increase cost

#### S4: Primary Index Range Search

**Prerequisites**:
- Range condition (>, >=, <, <=) on indexed key
- Primary index exists

**Algorithm**:
1. Use index to find record matching equality
2. Retrieve all subsequent/preceding records
3. Stop at range boundary

**Efficiency**:
- Initial access: O(log n)
- Range scan: O(k) where k = records in range
- Better than full scan for small ranges

---

## Implementing JOIN Operation

**JOIN** is one of most time-consuming operations in query processing.

### Methods for Implementing Two-Way Joins

#### J1: Nested-Loop Join (Brute Force)

**Algorithm**:
1. For each record t in outer relation R:
2. For each record s in inner relation S:
3. Test if t[A] = s[B] (join condition)
4. Return matching pairs

**Efficiency**:
- Cost: O(n_R × n_S) where n = records
- Does NOT require indexes
- Reads outer relation once, inner relation multiple times

**Use when**:
- No indexes available
- Relations small
- Join attribute not indexed

**Example**: 
```
For each Employee e:
  For each Department d:
    If e.dept_id = d.dept_id: output (e, d)
```

#### J2: Index-Based Nested-Loop Join

**Prerequisites**:
- Index on join attribute of inner relation

**Algorithm**:
1. For each record t in outer relation R:
2. Use index to find matching records in S
3. No need to scan entire S

**Efficiency**:
- Cost: O(n_R × log(n_S)) where log = index depth
- Much faster than nested-loop when inner has index
- Reduces inner relation scans significantly

**Use when**:
- Index exists on inner relation join attribute
- Inner relation large but indexed

#### J3: Sort-Merge Join

**Prerequisites**:
- Relations sorted (or can be sorted) on join attributes

**Algorithm**:
1. Sort both relations on join attributes A, B
2. Merge scan both relations simultaneously
3. Match records with same A, B values
4. Similar to merge step in merge sort

**Efficiency**:
- Sorting cost: O(n_R log n_R + n_S log n_S)
- Merge cost: O(n_R + n_S)
- Total: O(n log n)
- Good when relations already sorted

**Use when**:
- Relations already sorted
- Both relations large and need processing anyway
- Multiple joins on sorted relations

#### J4: Hash (Partition-Hash) Join

**Prerequisites**:
- Hash function defined on join attributes

**Algorithm**:

**Phase 1 - Partitioning**:
1. Hash smaller relation R using h(A)
2. Partition R into hash buckets in memory
3. Smaller relation must fit in memory after partitioning

**Phase 2 - Probing**:
1. Hash larger relation S using same h(B)
2. For each S record, probe matching hash bucket
3. Combine S record with all R records in that bucket

**Efficiency**:
- Cost: O(n_R + n_S) expected case
- Fast when smaller relation fits in memory
- Avoids duplicate comparisons like sort-merge

**Use when**:
- Smaller relation fits in memory
- No indexes available
- Relations very large

**Example**:
```
Partition R on dept_id using hash(dept_id) % 3
Probing: For each S row, hash its dept_id and find bucket
```

---

## Query Optimization Integration

Algorithms chosen during **query optimization phase**:
- Cost estimation for each algorithm
- Consideration of indexes available
- Relation sizes and selectivity
- Join order optimization
- Best algorithm selected for execution
