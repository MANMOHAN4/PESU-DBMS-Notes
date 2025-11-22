# Normal Forms Based on Primary Keys (1NF, 2NF, 3NF)

## Normalization Concepts

### What is Normalization?

**Normalization** is a process of analyzing relation schemas based on their FDs and keys to:
- Minimize redundancy
- Minimize insertion, deletion, and update anomalies
- Decompose unsatisfactory "bad" relations by breaking attributes into smaller relations

### Normal Form Definition

The **normal form** of a relation refers to the highest normal form condition that it meets, indicating the degree of normalization.

### Key Definitions

**Superkey**: A set of attributes S (subset of R) such that no two tuples in any legal relation state have same S value.

**Key**: A superkey with the additional property that removal of any attribute causes it to no longer be a superkey (minimal superkey).

**Candidate Key**: When relation has multiple keys, each is a candidate key.

**Primary Key**: One candidate key arbitrarily designated as primary key; others are secondary keys.

**Prime Attribute**: An attribute that is member of SOME candidate key.

**Non-Prime Attribute**: An attribute that is NOT a member of any candidate key.

---

## First Normal Form (1NF)

### Definition

A relation is in **1NF** if the domain of each attribute contains only atomic (indivisible) values.

### Key Constraints

- Each field holds a single value from its domain
- Does NOT allow:
  - Composite attributes
  - Multivalued attributes
  - Nested relations
  - Repeating groups
  - Sets of values for a single tuple

### Why 1NF?

Atomicity is fundamental to the relational model. Every value in every field must be a single atomic value.

### Example: Converting to 1NF

❌ **NOT 1NF**: DEPARTMENT relation with multivalued Locations attribute
```
Department: (DeptID, DName, Locations={New York, Los Angeles, Chicago})
```

✓ **IS 1NF**: Decomposed into two relations
```
DEPARTMENT (DeptID, DName)
DEPT_LOCATIONS (DeptID, Location)
```

---

## Second Normal Form (2NF)

### Definition

A relation is in **2NF** if:
1. It is in 1NF, AND
2. Every non-prime attribute A is **fully functionally dependent** on the primary key

### Full Functional Dependency

An FD Y → Z is **full** if removal of any attribute from Y makes the dependency no longer hold.

**Partial Dependency**: An FD where removing some LHS attributes still leaves valid dependency.

**Example of Partial vs Full**:
- {SSN, PNUMBER} → HOURS is full FD (neither SSN nor PNUMBER alone determines HOURS)
- {SSN, PNUMBER} → ENAME is partial FD (SSN alone determines ENAME)

### 2NF Violation

❌ Partial dependency violation:
```
FD: {SSN, PNUMBER} → ENAME (partial, SSN → ENAME holds)
This violates 2NF
```

### Normalizing to 2NF

To convert to 2NF: Decompose based on partial dependencies

Example:
- Original: EMP_PROJ(SSN, PNUMBER, ENAME, HOURS, DNAME)
- FD: SSN → ENAME, DNAME (partial dependencies)

Decompose into:
```
EMPLOYEE (SSN, ENAME, DNAME)
WORKS_ON (SSN, PNUMBER, HOURS)
```

### 2NF Rule

The test for 2NF involves testing FDs whose left-hand side attributes are part of primary key.

**Special case**: If primary key contains single attribute, 2NF test NOT needed.

---

## Third Normal Form (3NF)

### Definition

A relation is in **3NF** if:
1. It is in 2NF, AND
2. No non-prime attribute A is **transitively dependent** on the primary key

### Transitive Functional Dependency

An FD X → Z is **transitive** if derived from two FDs X → Y and Y → Z.

**Example of Transitive Dependency**:
```
SSN → DNUMBER (primary key determines department)
DNUMBER → DNAME (department determines manager name)
---
Therefore: SSN → DNAME (transitive dependency)
```

This violates 3NF if DNUMBER is NOT a candidate key.

### 3NF Violation Example

❌ Transitive dependency violation:
```
EMPLOYEE (SSN, ENAME, DNUMBER, DNAME, DMGRSSN)
FDs: SSN → DNUMBER, DNUMBER → DNAME, DNUMBER → DMGRSSN
DNAME and DMGRSSN transitively depend on SSN through DNUMBER
```

### Normalizing to 3NF

To convert to 3NF: Remove transitive dependencies

Example:
- Original: EMP(SSN, ENAME, DNUMBER, DNAME, DMGRSSN)
- Violating FD: DNUMBER → DNAME (DNUMBER not a candidate key)

Decompose into:
```
EMPLOYEE (SSN, ENAME, DNUMBER)
DEPARTMENT (DNUMBER, DNAME, DMGRSSN)
```

### 3NF Informal Definition

**"All attributes depend on nothing but the key"**

If X → Y and Y → Z with X as key, problem exists ONLY if:
- Y is NOT a candidate key

If Y IS a candidate key, transitive dependency is acceptable.

---

## Informal Normal Forms Summary

| Form | Definition | Focus |
|------|-----------|-------|
| 1NF | All attributes are atomic | Atomicity |
| 2NF | All attributes depend on whole key | Full dependency on entire key |
| 3NF | All attributes depend on nothing but key | No transitive dependencies |

---

## Practical Use of Normal Forms

### Denormalization

**Denormalization** is intentional storage of redundant data in higher normal form relations as lower normal form base relation.

**Purpose**: Database optimization technique to reduce costly joins while maintaining indexed fast lookups.

### When to Denormalize

**NOT**: "Skip normalization"

**YES**: Applied AFTER normalization to improve read performance when tables become large.

### Pros of Denormalization

- Retrieving data is faster (fewer joins needed)
- Queries simpler and less error-prone
- Less tables to join against

### Cons of Denormalization

- Updates and inserts more expensive
- Harder to write update/insert code
- Data inconsistency risks ("which value is correct?")
- More storage needed
- Maintenance complexity increases

### Example: Normalization vs Denormalization

**Normalized (3NF)**:
- Courses table: CourseID, CourseName, TeacherID
- Teachers table: TeacherID, TeacherName

Query with join: `SELECT Course, TeacherName FROM Courses JOIN Teachers`

Benefit: If teacher changes name, update once in Teachers

**Denormalized (Lower Form)**:
- Courses table: CourseID, CourseName, TeacherID, TeacherName

Benefit: Single table query, no joins
Cost: If teacher name changes, update all course records

---

## Normalization Decision Tree

```
START with Relation R
    ↓
Is every attribute atomic?
    NO → Apply 1NF (remove multivalued/composite attributes)
    YES → Continue
    ↓
Are non-prime attributes fully dependent on primary key?
    NO → Apply 2NF (decompose based on partial FDs)
    YES → Continue
    ↓
Are non-prime attributes transitively dependent on primary key?
    NO → In 3NF ✓
    YES → Apply 3NF (decompose based on transitive FDs)
    ↓
RESULT: 3NF Relation (or Further Normalization)
```

---

## Key Points

1. **1NF is Foundation**: All relational databases must be in 1NF by definition
2. **2NF eliminates partial dependencies**: On composite keys
3. **3NF eliminates transitive dependencies**: Between non-key attributes
4. **Practical limits**: Most databases normalized to 3NF or BCNF
5. **Denormalization strategic**: Only after achieving normalized form, for performance
6. **Trade-offs matter**: Speed vs. consistency, storage vs. complexity
