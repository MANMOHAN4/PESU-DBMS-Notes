# Functional Dependencies

## What is Functional Dependency?

A **Functional Dependency (FD)** is a constraint between two sets of attributes from the database.

### Definition

A functional dependency, denoted by **X → Y**, between two sets of attributes X and Y that are subsets of R specifies a constraint on possible tuples that can form a relation state r of R.

**Constraint**: For any two tuples t1 and t2 in r that have t1[X] = t2[X], they must also have t1[Y] = t2[Y].

### Key Concept

**X functionally determines Y** in a relation schema R if, and only if, whenever two tuples of r(R) agree on their X-value, they must necessarily agree on their Y-value.

### Properties of FDs

- If X is candidate key of R, then X → Y for any subset Y of R
- If X is candidate key, then X → R (X determines all attributes)
- If X → Y in R, this does NOT say whether or not Y → X

---

## Uses of Functional Dependencies

Functional dependencies are used to:

1. **Specify formal measures of "goodness"** of relational designs
2. **Define normal forms** for relations
3. **Represent constraints** derived from meaning and interrelationships of data attributes

All FDs must be derived from real-world constraints on attributes, not invented arbitrarily.

---

## Examples of Functional Dependencies

### Example 1: Employee and Social Security Number

SSN → ENAME

Social security number determines employee name uniquely.

### Example 2: Project Number

PNUMBER → {PNAME, PLOCATION}

Project number determines project name and location.

### Example 3: Employee and Project Assignment

{SSN, PNUMBER} → HOURS

Employee SSN and project number together determine hours per week employee works on project.

### Example 4: Employee Works_On Relationship

{Ssn, Pnumber} → Hours is **full functional dependency**

Neither Ssn → Hours nor Pnumber → Hours individually holds true.

---

## Terms and Classifications

### Prime Attribute vs Non-Prime Attribute

**Prime Attribute**: An attribute is prime if it is member of some candidate key of R.

**Non-Prime Attribute**: An attribute that is NOT a prime attribute; not member of any candidate key.

Example: In WORKS_ON relation, both Ssn and Pnumber are prime attributes. Other attributes are non-prime.

---

## Types of Functional Dependencies

### Full Functional Dependency

A functional dependency X → Y is **full** if removing any attribute A from X causes dependency to no longer hold.

In other words, for every A ∈ X, (X − {A}) does NOT determine Y.

**Example**: {Ssn, Pnumber} → Hours is full functional dependency

- Ssn → Hours does NOT hold
- Pnumber → Hours does NOT hold
- Only combined {Ssn, Pnumber} → Hours holds

### Partial Functional Dependency

A functional dependency X → Y is **partial** if some attribute A ∈ X can be removed and dependency still holds.

In other words, for some A ∈ X, (X − {A}) → Y remains valid.

A non-prime attribute depends on only portion of candidate key, rather than entire key.

**Example**: {Ssn, Pnumber} → Ename is partial

- Ssn → Ename holds independently
- So Ssn and Pnumber together → Ename is partial dependency
- Ename depends on only Ssn, not entire key

### Transitive Functional Dependency

A **transitive functional dependency** occurs when non-prime attribute depends on another non-prime attribute.

Formally: If X → Y and Y → Z, then X → Z is transitive dependency, provided X is a key and Z is non-prime.

**Example**:
- Ssn → Dept_Number (key to attribute)
- Dept_Number → Dept_Name (non-key to non-key)
- Therefore: Ssn → Dept_Name (transitive)

Dept_Name is transitively dependent on Ssn through Dept_Number.

### Trivial Functional Dependency

A **trivial functional dependency** holds by definition based on **Reflexive Rule**.

If Y is subset of X, then dependency X → Y is always true (trivial).

**Example**:
- If X = {A, B, C} and Y = {B, C}
- Then ABC → BC is trivial functional dependency
- Because Y (BC) is subset of X (ABC)

### Non-Trivial Functional Dependency

A **non-trivial functional dependency** is one where dependent attribute (Y) is NOT subset of determinant (X).

X → Y is non-trivial if and only if **Y ⊈ X** (Y is not subset of X).

Means Y contains at least one attribute not already in X.

**Example**:
- ABC → D is non-trivial (D not in ABC)
- AB → ABC is trivial (ABC includes AB)

---

## Graphical Notation of FDs

**Representation**:
- Each FD displayed as horizontal line
- Left-hand side attributes connected by vertical lines to FD line
- Right-hand side attributes connected by lines with arrows pointing toward attributes

---

## Determining FDs from Relation Instances

### Ruling Out FDs

Not all FDs will hold. To determine if FD may exist:

**Rule**: For FD X → Y to potentially hold, whenever two tuples have same X-value, they must have same Y-value.

**If ruled out**: Element on LHS has similar values but different values on RHS → FD does NOT hold.

### Example: TEACH Relation

Given relation with Teacher, Course, Text columns:

**May hold**:
- Text → Course (each text used in one course)

**Ruled out**:
- Teacher → Course (multiple teachers teach same course)
- Teacher → Text (one teacher uses multiple texts)
- Course → Text (multiple texts in same course)

---

## Summary: Functional Dependency Properties

| Property | Meaning |
|----------|---------|
| X → Y | X determines Y; if X values same, Y must be same |
| Full FD | Removing any attribute from X breaks dependency |
| Partial FD | Can remove some attributes from X, still holds |
| Transitive FD | Non-key → non-key through intermediate attribute |
| Trivial FD | Y is subset of X; always holds |
| Non-trivial FD | Y not subset of X; provides new information |
| Prime attribute | Member of some candidate key |
| Non-prime attribute | NOT member of any candidate key |

---

## Real-World Constraint Examples

FDs represent real-world business rules:

- Each employee has one unique SSN
- Each project has unique name and location
- Each department has one manager
- Each course section has one instructor
- Each phone number belongs to one customer

These constraints are derived from understanding business semantics, not arbitrary design choices.
