# General Definitions of 2NF and 3NF, BCNF

## BCNF: Boyce-Codd Normal Form

### Definition

A relation schema R is in **Boyce-Codd Normal Form (BCNF)** if whenever a functional dependency X → A holds in R, then **X is a superkey of R**.

### BCNF Strength Hierarchy

**Every BCNF relation is in 3NF, but NOT every 3NF relation is in BCNF**

Hierarchy (strictest to weakest):
```
BCNF ⊂ 3NF ⊂ 2NF ⊂ 1NF
```

**Property relationships**:
- Every 2NF relation is in 1NF ✓
- Every 3NF relation is in 2NF ✓
- Every BCNF relation is in 3NF ✓
- There exist 3NF relations NOT in BCNF ✗
- BCNF is considered stronger form of 3NF

### BCNF vs 3NF

**3NF Allows**: X → A where:
- (a) X is a superkey, OR
- (b) A is a prime attribute

**BCNF Requires**: X → A only if X is a superkey (no exception for prime attributes)

**Example**: Relation in 3NF but NOT in BCNF
- FD: C → B (C is not superkey, B is prime attribute)
- 3NF allows this (condition b satisfied)
- BCNF rejects (X not superkey)

---

## General Definitions for Multiple Candidate Keys

### General 2NF Definition

**Old definition** considered only primary key.

**New definition** (general, for multiple candidate keys):

A relation schema R is in **2NF** if every non-prime attribute A in R is **fully functionally dependent on EVERY key** of R.

**Example violation**:
- LOTS relation with FD: County_name → Tax_rate
- County_name is not a key
- Violates general 2NF

### General 3NF Definition

**Old definition** considered only primary key and transitive dependencies.

**New definition** (general, for multiple candidate keys):

A relation schema R is in **3NF** if whenever a FD X → A holds in R, then either:

**(a) X is a superkey of R**, OR

**(b) A is a prime attribute of R**

### Interpreting General 3NF Definition

**Condition (a)** catches two types of violations:
1. Prime attribute determines non-prime attribute → 2NF violation
2. Non-prime attribute determines non-prime attribute → 3NF violation (transitive)

**Condition (b)** handles cases:
- Where a non-superkey determines a prime attribute
- Allowed in 3NF but caught by BCNF

### Alternative 3NF Definition

**Restatement of 3NF**:

A relation schema R is in **3NF** if every non-prime attribute in R meets BOTH:

1. **It is fully functionally dependent on EVERY key of R**
2. **It is non-transitively dependent on EVERY key of R**

**Advantage**: This formulation makes clear that 3NF requires 2NF as prerequisite.

---

## BCNF Decomposition

### When to Apply BCNF

Apply BCNF when relation is NOT in BCNF, i.e., there exists FD X → A where X is NOT a superkey.

### Algorithm for BCNF Decomposition

**Input**: Relation R not in BCNF with violating FD X → A

**Output**: BCNF relations

**Steps**:
1. Let X → A be FD that violates BCNF (X NOT superkey)
2. Decompose R into:
   - **R1 = R - A** (remove A from R)
   - **R2 = X ∪ A** (keep X and A)
3. If either R1 or R2 not in BCNF, repeat process

**Example decomposition**:

Violating FD: Instructor → Course

TEACH → decomposed into:
- R1 = TEACH - Course = (Student, Instructor)
- R2 = Instructor ∪ Course = (Instructor, Course)

### Important BCNF Properties

**BCNF Decomposition sacrifices**:
- May lose some functional dependencies
- Dependency preservation NOT guaranteed

**BCNF Decomposition preserves**:
- Non-additivity (lossless join property) - MUST HAVE
- No spurious tuples on join

---

## Additional Normalization Properties

### Lossless Join Property (Non-Additivity)

**Definition**: A decomposition D = {R1, R2, ...} is **lossless** if:

For any valid relation r of R:
```
r = πR1(r) ⋈ πR2(r) ⋈ ... ⋈ πRn(r)
```

**Critical**: Must NOT generate spurious (meaningless) tuples on rejoin.

**Status**: MUST be achieved at any cost

### Dependency Preservation Property

**Definition**: A decomposition preserves **dependency preservation** if each FD in original can be checked within some individual decomposed relation.

**Status**: Desirable, but sometimes sacrificed for BCNF

### Test for Binary Decomposition Losslessness

**Property NJB** (Non-additive Join test for Binary decompositions):

A decomposition D = {R1, R2} of R has lossless join property if and only if:

**EITHER**:
- FD ((R1 ∩ R2) → (R1 - R2)) is in F⁺, **OR**
- FD ((R1 ∩ R2) → (R2 - R1)) is in F⁺

**Interpretation**:
- R1 ∩ R2 = common attributes
- R1 - R2 = attributes only in R1
- R2 - R1 = attributes only in R2

**Example - TEACH Decomposition**:

Original: TEACH (Student, Course, Instructor)
FDs: {Student, Course} → Instructor; Instructor → Course

**Test Decomposition D3**: {Instructor, Course} and {Instructor, Student}
- R1 ∩ R2 = {Instructor}
- R1 - R2 = {Course}
- R2 - R1 = {Student}

Check: Instructor → Course? YES! ✓
- Lossless join property satisfied
- This is good decomposition

---

## 3NF vs BCNF: When to Use What

### 3NF Advantages

- Achieves lossless join decomposition
- Preserves all functional dependencies
- Easier to achieve than BCNF
- Sufficient for most practical applications

### BCNF Advantages

- Stronger form eliminates all anomalies related to FDs
- Cleaner structure (every determinant is superkey)
- Theoretical purity

### Practical Decision

**Use 3NF when**:
- Dependency preservation critical
- Some design complexity acceptable

**Use BCNF when**:
- No dependency preservation conflicts
- Simpler, cleaner design preferred
- Can sacrifice some dependencies

**In Practice**: Most practical databases normalized to 3NF or BCNF

---

## Example: TEACH Relation (3NF but NOT BCNF)

### Original Relation

**TEACH (Student, Course, Instructor)**

### Functional Dependencies

- fd1: {Student, Course} → Instructor
- fd2: Instructor → Course

### Key Analysis

- Candidate key: {Student, Course}
- Prime attributes: Student, Course
- Non-prime attribute: Instructor

### 2NF Check

fd1: {Student, Course} → Instructor (full FD on key) ✓
fd2: Instructor → Course (not applicable, Instructor not key) ✓

**In 2NF** ✓

### 3NF Check

fd1: {Student, Course} is superkey ✓
fd2: Instructor → Course; Instructor NOT superkey; Course IS prime ✓

**In 3NF** ✓

### BCNF Check

fd2: Instructor → Course; Instructor NOT superkey ✗

**NOT in BCNF** ✗

### BCNF Decomposition

**Violating FD**: Instructor → Course

Decompose:
- R1 = TEACH - Course = (Student, Instructor)
- R2 = Instructor ∪ Course = (Instructor, Course)

Result:
- R1: (Student, Instructor)
- R2: (Instructor, Course)

**Both in BCNF** ✓

---

## Summary Table

| Property | 1NF | 2NF | 3NF | BCNF |
|----------|-----|-----|-----|------|
| Atomic values | ✓ | ✓ | ✓ | ✓ |
| Full dependency on key | ✗ | ✓ | ✓ | ✓ |
| Non-transitive on key | ✗ | ✗ | ✓ | ✓ |
| Every determinant is superkey | ✗ | ✗ | ✗ | ✓ |
| Preserves dependencies | ✗ | ✗ | ✓ | ✗ |
| Lossless join possible | ✓ | ✓ | ✓ | ✓ |
