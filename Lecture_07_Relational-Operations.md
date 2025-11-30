# Relational Algebra Operations

## What is Relational Algebra?

**Definition**:
Procedural query language consisting of set of operations where:
- Each operation takes one or two relations as input
- Produces a new relation as result
- Creates "closed" system (all objects are relations)

**Key Property - Closure**:
- Result of operation is a relation
- Result can be used as input to another operation
- Allows composing operations into expressions

**Relational Algebra Expression**:
- Sequence of operations on relations
- Result is a relation representing query answer

---

## Classification of Operators

### Unary Operators (Single Relation Input)

1. **SELECT (σ)** - Choose rows based on condition
2. **PROJECT (∏)** - Choose columns (attributes)
3. **RENAME (ρ)** - Rename relation or attributes

### Binary Operators (Two Relations Input)

**Set Operations**:
1. **UNION (∪)** - Combine rows from two relations
2. **INTERSECTION (∩)** - Common rows in both relations
3. **SET DIFFERENCE (-)** - Rows in first but not second

**Relational Operations**:
1. **CARTESIAN PRODUCT (×)** - All combinations of rows
2. **JOIN (⋈)** - Combine relations based on condition

---

## Unary Operations

### SELECT Operation (σ)

**Purpose**:
- Choose subset of tuples (rows) satisfying selection condition
- Filter operation (horizontal partition)
- Keeps only matching tuples

**Notation**:
```
σ_p (r)
p = selection predicate
r = relation name
```

**Properties**:
- Result has same schema (attributes) as input
- Result tuples ⊆ input tuples
- Commutative: σ_p1(σ_p2(r)) = σ_p2(σ_p1(r))

**Operators Allowed**:
- Comparison: =, ≠, >, ≥, <, ≤
- Logical: ∧ (AND), ∨ (OR), ¬ (NOT)

**Examples**:

**Example 1**:
```
Query: Find all instructors in Physics department

σ_dept_name="Physics" (Instructor)

Result: All rows where Dept_Name = "Physics"
```

**Example 2**:
```
Query: Find instructors in Physics with salary > 90,000

σ_(dept_name="Physics" ∧ salary>90000) (Instructor)

Result: Rows matching BOTH conditions
```

**Example 3**:
```
Query: Employees in department 4 earning > 25,000 
OR employees in department 5 earning > 30,000

σ_((Dno=4 AND Salary>25000) OR (Dno=5 AND Salary>30000)) (EMPLOYEE)
```

**Important**:
- Cascade of SELECT operations can be done in any order (commutativity)
- Result size ≤ input size (may be empty if no tuples match)

### PROJECT Operation (∏)

**Purpose**:
- Choose specific attributes (columns)
- Discard other attributes
- Vertical partition of relation

**Notation**:
```
∏_A1,A2,...,Ak (r)
A1, A2, ..., Ak = attribute names to keep
r = relation name
```

**Key Property**:
- Removes duplicate rows (relations are sets)
- Only rows with different combinations of selected attributes remain

**Properties**:
- Result has only specified attributes
- Result tuples ≤ input tuples (may eliminate duplicates)
- Number of attributes in result < input relation

**Examples**:

**Example 1**:
```
Query: Show only ID, Name, and Salary of instructors

∏_ID,Name,Salary (Instructor)

Input: ID | Name | Dept_Name | Salary
Result: ID | Name | Salary
(Dept_Name column removed)
```

**Example 2**:
```
Query: Get Employee last name, first name, and salary

∏_Lname,Fname,Salary (EMPLOYEE)

Result shows only these 3 columns
```

**Example 3**:
```
Query: Get Sex and Salary from employees

∏_Sex,Salary (EMPLOYEE)

If multiple employees have same (Sex, Salary), duplicates removed
(e.g., multiple males earning 50,000 appears as ONE row)
```

**Important**:
- Duplicates automatically removed
- If key attribute included, result size = input size
- If no key included, result size ≤ input size

### RENAME Operation (ρ)

**Purpose**:
- Rename relation and/or attributes
- Useful when referencing intermediate results
- Needed for recursive operations

**Notation**:
```
ρ_S (E)                    - Rename relation to S
ρ_S(B1,B2,...,Bn) (E)      - Rename relation to S, attributes to B1,B2,...,Bn
ρ_(B1,B2,...,Bn) (E)       - Rename only attributes
```

**Alternate Form**:
```
ρ_OldName→NewName (r)      - Rename specific attribute
```

**Examples**:

**Example 1**:
```
Rename EMPLOYEE relation to EMP:
ρ_EMP (EMPLOYEE)
```

**Example 2**:
```
Rename both relation and attributes:
ρ_R(A,B,C,D) (EMPLOYEE)

Renames EMPLOYEE to R with attributes A,B,C,D
```

**Example 3**:
```
Rename specific attribute:
ρ_Salary→Pay (EMPLOYEE)

Changes Salary attribute name to Pay
```

---

## Composition of Operations

**Concept**:
- Operations can be combined into larger expressions
- Intermediate result of one operation serves as input to next
- Single expression or sequential steps

**Methods**:
1. **Nested**: σ_condition (∏_attributes (relation))
2. **Sequential**: Create intermediate relations with names

### Nested Expression Example

```
Query: Find names of all instructors in Physics department

∏_name (σ_dept_name="Physics" (Instructor))

Evaluation:
Step 1: σ_dept_name="Physics" (Instructor) → physics_instructors
Step 2: ∏_name (physics_instructors) → result
```

### Sequential Expression Example

```
Query: Get first, last name and salary of department 5 employees

Approach 1 (Nested):
∏_Fname,Lname,Salary (σ_DNO=5 (EMPLOYEE))

Approach 2 (Sequential):
DEP5_EMPS ← σ_DNO=5 (EMPLOYEE)
RESULT ← ∏_Fname,Lname,Salary (DEP5_EMPS)
```

---

## Binary Operations

### UNION (∪)

**Purpose**:
- Combines tuples from two relations
- Result contains rows from both relations
- Removes duplicates

**Notation**:
```
R ∪ S
```

**Requirement**:
- Relations must be union-compatible (same attributes)

**Properties**:
- Commutative: R ∪ S = S ∪ R
- Associative: (R ∪ S) ∪ T = R ∪ (S ∪ T)

### INTERSECTION (∩)

**Purpose**:
- Returns tuples common to both relations
- Rows appearing in both relations

**Notation**:
```
R ∩ S
```

**Requirement**:
- Relations must be union-compatible

### SET DIFFERENCE (−)

**Purpose**:
- Returns tuples in first relation but not in second
- A − B = rows in A but not in B

**Notation**:
```
R − S
```

**Requirement**:
- Relations must be union-compatible

**Important**:
- NOT commutative: R − S ≠ S − R

### CARTESIAN PRODUCT (×)

**Purpose**:
- Combines every row from first relation with every row from second
- Creates all possible combinations

**Notation**:
```
R × S
```

**Result Size**:
- Rows: |R| × |S| (product of tuple counts)
- Columns: |R| + |S| (sum of attributes)

**Usage**:
- Often combined with SELECT for join-like operations
- Base for JOIN operation

### JOIN (⋈)

**Purpose**:
- Combines rows from two relations based on join condition
- More efficient than Cartesian product + select

**Notation**:
```
R ⋈_condition S
```

**Types**:
- **Equi-join**: Condition uses equality
- **Natural join**: Joins on common attributes
- **Theta-join**: Uses any comparison operator

---

## THE ASSIGNMENT OPERATOR (←)

**Purpose**:
- Assign results of operations to temporary relations
- Simplifies complex queries
- Makes queries more readable

**Notation**:
```
VarName ← expression
```

**Workflow**:
1. Perform operation
2. Assign result to variable
3. Use variable in next operation
4. Final expression displays result

**Example**:
```
Query: Get names of employees in department 5

R1 ← σ_DNO=5 (EMPLOYEE)
RESULT ← ∏_Fname,Lname (R1)

Or single nested expression:
∏_Fname,Lname (σ_DNO=5 (EMPLOYEE))
```

---

## Practical Query Examples

### Example 1: Single Operation

```
Query: Find all employees in department 4

σ_DNO=4 (EMPLOYEE)

Result: All employee rows with DNO=4
```

### Example 2: Two Operations

```
Query: Find IDs and names of instructors in Physics earning > 90,000

∏_ID,Name (σ_(dept_name="Physics" ∧ salary>90000) (Instructor))

Step 1: SELECT instructors in Physics with salary > 90,000
Step 2: PROJECT only ID and Name columns
```

### Example 3: Sequential Operations

```
Query: Find names of products priced under 50 and in Electronics category

ELECTRONICS ← σ_category="Electronics" (PRODUCT)
CHEAP ← σ_price<50 (ELECTRONICS)
RESULT ← ∏_productname (CHEAP)

Result: Product names that are in Electronics AND cost < 50
```

### Example 4: Union Example

```
Query: Students in CS major OR students with GPA > 3.5

CS_STUDENTS ← σ_major="CS" (STUDENT)
HIGH_GPA ← σ_GPA>3.5 (STUDENT)
RESULT ← CS_STUDENTS ∪ HIGH_GPA

Result: Students matching either condition (or both)
```

---

## Key Properties and Rules

### Commutativity
- SELECT: σ_p1 (σ_p2 (R)) = σ_p2 (σ_p1 (R))
- UNION: R ∪ S = S ∪ R
- INTERSECTION: R ∩ S = S ∩ R

### Associativity
- UNION: (R ∪ S) ∪ T = R ∪ (S ∪ T)
- INTERSECTION: (R ∩ S) ∩ T = R ∩ (S ∩ T)

### Non-Commutative
- SET DIFFERENCE: R − S ≠ S − R
- CARTESIAN PRODUCT: Generally not meaningful to commute

### Cascade of SELECT
- Multiple SELECT operations can be combined and reordered
- Optimization opportunity: Apply most restrictive first

### Cascade of PROJECT
- Can combine to single PROJECT with desired attributes

---

## Closure Property Importance

**Significance**:
- Every operation produces a relation
- Result can be input to another operation
- Enables composing complex queries
- Provides solid theoretical foundation

**Practical Benefit**:
- Can write arbitrarily complex queries
- Build from simple operations
- Combine and nest operations freely
