# Query Optimization

## What is Query Optimization?

**Query Optimization** is the process of improving SQL query efficiency by comparing different execution methods and choosing the one with the lowest cost.

### Goal

Reduce system resources required to fulfill query:
- Minimize execution time
- Maximize performance
- Faster results to user
- Service more queries in same time
- Reduce hardware wear (disk drives, CPU, memory)

---

## Query Optimization Overview

### Query Optimizer Role

**Conducted by**: Query optimizer module in DBMS

**Process**:
1. Converts high-level SQL into internal tree representation (parse tree or query tree)
2. Each node represents operation (selection, join, projection)
3. Leaves represent relations (tables)
4. Explores alternative execution plans using equivalent transformations
5. Uses heuristics (rule-based simplifications) to prune operations early

### Cost Model Components

**Metrics considered**:
- Disk I/O cost (most important)
- CPU time
- Memory usage
- Communication cost

**Statistics from catalog**:
- File size and number of records
- Number of distinct attribute values
- Attribute selectivity
- Histogram distributions
- Index availability

### Plan Selection

1. **Cost estimation**: Calculates cost for each possible plan
2. **Comparison**: Analyzes costs using cost model
3. **Selection**: Chooses plan with lowest estimated cost
4. **Conversion**: Converts to physical query plan with specific algorithms
5. **Adaptive**: May refine mid-execution if runtime statistics deviate

---

## Query Access Methods

### MySQL Access Methods

**MySQL uses three primary access methods**:

1. **Full Table Scan**
   - When query accesses all rows
   - No WHERE clause filtering
   - Cost: O(n) — reads all blocks

2. **Index Level Scan**
   - Columns have primary key, unique key, or non-unique key index
   - Jumps directly to matching records
   - Cost: O(log n) — uses B-tree traversal

3. **Range Scan**
   - Integer or date columns with range conditions (>, <, >=, <=)
   - Uses index to find range boundaries
   - Cost: O(log n + k) — log for finding range start, k for scanning range

---

## Query Trees and Heuristics

### Query Optimization Steps

**Step 1**: Scanner and parser generate initial query representation
- Tokenization and syntax checking
- Creates initial (often inefficient) tree

**Step 2**: Representation optimized according to heuristic rules
- Applies transformation rules
- Moves operations for efficiency
- Eliminates redundancy

**Step 3**: Query execution plan developed
- Execute groups of operations
- Consider access paths available
- Specify file access methods

---

## Heuristic Optimization

### What is Heuristic Optimization?

**Purpose**: Rapid exploration of almost all execution plans efficiently

**Method**: Implementation of heuristics (rules of thumb)

**Benefits**:
- Efficient execution query plan convergence
- Minimized computational cost
- No complex cost estimation needed

### Common Heuristic Rules

1. **Push Selection Down**: Apply WHERE clauses early
   - Reduces intermediate result sizes
   - Filters tuples before operations

2. **Replace Cartesian Product + Selection with JOIN**
   - Avoids expensive Cartesian product
   - More efficient join operation

3. **Apply Most Restrictive SELECT First**
   - SELECT with lowest selectivity first
   - Reduces tuples for subsequent operations

4. **Push Projection Down**: Remove unnecessary columns early
   - Reduces data transferred
   - Smaller intermediate relations

---

## Query Trees Structure

### Query Tree Definition

**Query Tree** (also Query Evaluation/Execution Tree):
- Data structure for internal query representation
- Leaf nodes: relations (tables)
- Internal nodes: relational algebra operators (σ SELECT, ⋈ JOIN, π PROJECT)
- Root node: query output

### Query Graphs

**Query Graph** alternative representation:
- Nodes: relations involved in query
- Edges: join conditions or relationships
- Labels: selection conditions, projection attributes, operations
- Directed acyclic graph (DAG) structure

**Advantages over trees**:
- Shows all relationships simultaneously
- Less committed to operation order
- Better for complex queries

---

## Query Transformation Example

### Initial Query

SQL query usually produces very inefficient initial tree.

### Transformation Steps

**Step 1: Initial (Canonical) Query Tree**
- Direct translation from SQL
- Often very inefficient
- Would cause poor performance

**Step 2: Move SELECT Operations Down**
- Apply selection filters early
- Reduce tuples before joins
- Major efficiency gain

**Step 3: Apply Most Restrictive SELECT First**
- Identify most selective conditions
- Execute highest-filtering conditions first
- Maximize tuple reduction

**Step 4: Replace Cartesian Product + SELECT with JOIN**
- Convert inefficient cross-product to join
- Use join algorithm (nested-loop, hash, sort-merge)
- Significant performance improvement

**Step 5: Move PROJECT Operations Down**
- Eliminate unnecessary columns early
- Reduce data carried through operations
- Smaller intermediate results

---

## Types of Query Optimization

### Type 1: Heuristic (Rule-Based) Optimization

**Method**: Transformation rules on query trees

**Algebraic Transformations**:
- Apply SELECT and PROJECT early
- Replace Cartesian Product + SELECT with JOIN
- Execute most restrictive filters first
- Reorder join operations

**Characteristics**:
- No cost estimation
- Decisions follow established heuristics
- Fast optimization process
- May not find optimal plan

**Example**:
```sql
SELECT name FROM Employee WHERE dept_id = 10 AND salary > 50000;
```
Optimizer applies selections first → then projection

### Type 2: Cost-Based Optimization

**Method**: Estimates cost for each possible execution plan

**Process**:
1. Generate alternative plans for query
2. Estimate cost (I/O, CPU, memory) for each
3. Select plan with lowest total cost

**Information Used**:
- File size and number of records
- Number of distinct attribute values
- Attribute selectivity
- Histogram distributions
- Index availability and type

**Cost Components**:
- Disk I/O cost (dominant)
- CPU computation cost
- Memory usage cost
- Communication cost (distributed systems)

**Example**:
```sql
SELECT E.name, D.dept_name 
FROM Employee E 
JOIN Department D ON E.dept_id = D.dept_id 
WHERE E.salary > 50000;
```

**Alternative Plans**:
- Plan A: Nested-loop join, selection after join
- Plan B: Selection first on Employee, then join (lower cost)
- Plan C: Index on salary + hash join

Optimizer estimates costs and chooses Plan B or C with lowest cost.

### Type 3: Semantic Query Optimization

**Method**: Uses semantic information from schema

**Techniques**:
- Exploits integrity constraints (primary key, foreign key, unique)
- Applies functional dependencies
- Eliminates redundant operations
- Rewrites queries using constraints

**Example**:
- If Employee.dept_id has foreign key to Department.dept_id
- Query accessing non-existent department automatically filtered
- Unnecessary joins eliminated

**Benefit**: Improves performance while maintaining correctness

### Type 4: Dynamic Query Optimization

**Method**: Optimization decisions made during execution

**Characteristics**:
- Adaptive to runtime conditions
- Used by modern optimizers (Oracle Adaptive Optimization)
- Adjusts join order, access paths, memory usage dynamically
- Feedback loops refine execution plans

---

## Cost-Based Optimization Details

### Cost Calculation Features

1. **Cost-based on query and environment**
   - Allocates numerical cost to each step
   - Adds costs for total estimate

2. **Multiple possible paths**
   - Indexes available or not
   - Sorting methods available
   - Constraints applicable

3. **Optimal path selection**
   - Chooses most efficient implementation
   - Lowest minimum cost algorithm

4. **Cost provision by optimizer**
   - Needed for most suitable query selection

5. **Cardinality dependency**
   - Cost depends on input cardinality
   - Larger inputs typically higher cost

---

## MySQL EXPLAIN Utility

### Purpose

Powerful tool for analyzing and optimizing SQL queries.

Provides insight into:
- How MySQL executes query
- Chosen execution plan details
- Table access order
- Index usage

### Syntax

```sql
EXPLAIN SELECT column1, column2, ...
FROM table_name
WHERE condition;
```

### Output Information

Returned as table with columns providing insights:
- **id**: SELECT query sequence number
- **select_type**: Type of SELECT
- **table**: Table referenced
- **type**: Join type (system, const, eq_ref, ref, range, index, ALL)
- **possible_keys**: Indexes MySQL could use
- **key**: Index actually used
- **key_len**: Length of used key
- **ref**: Columns compared to index
- **rows**: Estimated rows examined
- **filtered**: Percentage of rows filtered
- **Extra**: Additional information (Using index, Using where, etc.)

### Example: EXPLAIN Output

```sql
EXPLAIN SELECT customer_id, SUM(total_amount) AS total_orders 
FROM orders 
GROUP BY customer_id;
```

Output reveals:
- Simple SELECT on orders table
- Index used for GROUP BY operation
- Efficient execution

---

## Indexing Impact

### Without Indexing

```sql
SELECT * FROM Employee WHERE salary > 50000;
```

**Execution**:
- Full table scan
- Every record checked
- Cost: O(n) — linear growth

**Performance**: Slow on large tables

### With Indexing

```sql
CREATE INDEX idx_salary ON Employee(salary);
EXPLAIN SELECT * FROM Employee WHERE salary > 50000;
```

**Execution**:
- B-tree index used
- Jumps to first qualifying record
- Only relevant blocks read
- Cost: O(log n) — exponential improvement

**Performance**: Much faster

### Indexing Example with Large Dataset

**Scenario**: Table with 1 million records, select where customer_id = 12345

| Metric | Without Index | With Index |
|--------|--------------|-----------|
| Approach | Full table scan | B-tree lookup |
| Blocks read | ~All blocks | ~log(1,000,000) ≈ 20 blocks |
| Time | Seconds | Milliseconds |
| Cost complexity | O(n) | O(log n) |

**Conclusion**: Indexing on WHERE clause columns dramatically improves performance.

---

## Key Optimization Techniques

1. **Use Indexes Wisely**
   - Create indexes on WHERE clause columns
   - Use composite indexes for multi-column conditions
   - Avoid over-indexing (write performance trade-off)

2. **Retrieve Only Needed Columns**
   - Avoid SELECT *
   - Specify required columns
   - Reduces data transfer and processing

3. **Optimize Joins**
   - Join only required tables
   - Use indexes on join attributes
   - Choose appropriate join order

4. **Filter Early Using WHERE**
   - Limit rows before further operations
   - Apply most selective conditions first
   - Use indexed columns in WHERE clause

5. **Use LIMIT for Large Results**
   - Fetch only needed records
   - Avoids unnecessary processing
   - Speeds up result delivery

---

## Query Optimization Summary

**Optimization is critical for**:
- Large database query performance
- System resource efficiency
- User experience
- Scalability

**Tools available**:
- EXPLAIN for query analysis
- Indexes for fast lookups
- Cost-based and heuristic optimization
- Query rewriting and transformation

**Performance depends on**:
- Query structure
- Index availability
- Data distribution
- System resources
- Optimizer quality
