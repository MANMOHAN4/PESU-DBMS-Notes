# Higher Normal Forms: 4NF and 5NF with Practice Problems

## Multivalued Dependencies (MVD)

### Definition

A **multivalued dependency (MVD)** X ↠ Y specified on relation schema R (where X and Y are subsets of R) specifies a constraint:

**If two tuples t1 and t2 exist in r such that t1[X] = t2[X], then two tuples t3 and t4 should also exist in r with properties**:

Where Z = (R - (X ∪ Y)):
- t3[X] = t4[X] = t1[X] = t2[X]
- t3[Y] = t1[Y] and t4[Y] = t2[Y]
- t3[Z] = t2[Z] and t4[Z] = t1[Z]

### Trivial MVD

An MVD X ↠ Y is **trivial** if:
- (a) Y is subset of X, **OR**
- (b) X ∪ Y = R (covers all attributes)

**Trivial MVDs** don't specify significant constraints.

**Non-trivial MVD**: Neither (a) nor (b) holds.

### Example MVDs

**EMP relation** with employees, projects, and dependents:

- SID ↠ CID (if employee has multiple courses, independent of other attributes)
- SID ↠ CNAME
- SNAME ↠ CNAME

---

## Fourth Normal Form (4NF)

### Definition

A relation schema R is in **4NF** with respect to set of dependencies F (functional + multivalued) if:

**For every nontrivial multivalued dependency X ↠ Y in F⁺, X is a superkey of R**

### F⁺ Definition

F⁺ is the complete set of all dependencies (functional or multivalued) that will hold in every relation state r of R that satisfies F.

Also called the **closure of F**.

### MVD Origins

MVDs are consequences of 1NF (which disallows sets of values).

MVDs introduced because of 2nd normalization option (expansion of key) in 1NF.

### 4NF Decomposition

**Problem**: EMP relation with multiple independent multivalued dependencies

**Solution**: Decompose into separate relations, each removing one MVD

**Example**:
- Original: EMP (Ename, Pname, Dname) with Ename ↠ Pname and Ename ↠ Dname
- Decompose into:
  - EMP_PROJECTS (Ename, Pname)
  - EMP_DEPENDENTS (Ename, Dname)

**Result**: Both relations in 4NF (each separate MVD in own relation)

---

## Join Dependencies (JD)

### Definition

A **join dependency (JD)**, denoted JD(R1, R2, ..., Rn), specified on relation schema R specifies:

**Every legal state r of R should have non-additive join decomposition into R1, R2, ..., Rn**

**Formally**:
```
(πR1(r) ⋈ πR2(r) ⋈ ... ⋈ πRn(r)) = r
```

### MVD as Special Case of JD

An MVD is a special case of JD where **n = 2**.

### Trivial JD

A JD is **trivial** if one of the relation schemas Ri equals R (entire relation).

---

## Fifth Normal Form (5NF)

### Definition

A relation schema R is in **5NF** (or **PJNF - Project-Join Normal Form**) with respect to set F of functional, multivalued, and join dependencies if:

**For every nontrivial join dependency JD(R1, R2, ..., Rn) in F⁺ (implied by F)**:

**Every Ri is a superkey of R**

### Practical Limitations

Discovering join dependencies in practical databases with hundreds of relations is **next to impossible**.

**Therefore, 5NF is RARELY USED in practice.**

### 5NF Decomposition Example

**Problem**: SUPPLY relation with multiple join dependencies

**Solution**: Decompose into three relations

Original: SUPPLY (R1, R2, R3) with complex JD

Decomposed:
- R1 (specific attributes)
- R2 (specific attributes)
- R3 (specific attributes)

Result: All three relations in 5NF

---

## Practice Problems: Normalization

### Problem 1: 2NF Violation

**Given**:
- Relation R(P, Q, R, S, T)
- FDs: {PQ → R, S → T}

**Find**: Is R in 2NF? If not, normalize to 2NF.

**Solution**:

**Step 1: Find candidate key**
- PQS⁺ = {P, Q, S, R, T}
- Candidate key: PQS

**Step 2: Identify prime and non-prime attributes**
- Prime: P, Q, S (in candidate key)
- Non-prime: R, T

**Step 3: Check for partial dependencies**
- FD: PQ → R (PQ is part of key PQS, non-prime R depends on part of key)
  - **Partial dependency** ✗
- FD: S → T (S is part of key PQS, non-prime T depends on part of key)
  - **Partial dependency** ✗

**Conclusion**: NOT in 2NF

**Step 4: Decompose to 2NF**

Remove partial dependencies:
- R1(P, Q, R) [PQ → R is now full FD]
- R2(S, T) [S → T is now full FD]
- R3(P, Q, S) [preserve key]

**Result**: All relations in 2NF ✓

---

### Problem 2: 3NF Violation

**Given**:
- Relation R(X, Y, Z)
- FDs: {X → Y, Y → Z}

**Find**: Is R in 3NF? If not, normalize to 3NF.

**Solution**:

**Step 1: Find candidate key**
- X⁺ = {X, Y, Z}
- Candidate key: X

**Step 2: Check 2NF**
- FD: X → Y (X is key, Y non-prime, full dependency) ✓
- FD: Y → Z (Y not key, but check if needed for 2NF)
- **In 2NF** ✓

**Step 3: Check 3NF for transitive dependencies**
- FD: X → Y (X is superkey) ✓
- FD: Y → Z (Y is NOT superkey; Z is NOT prime attribute)
  - **Transitive dependency** ✗

**Conclusion**: NOT in 3NF

**Step 4: Decompose to 3NF**

Remove transitive dependency (Y → Z):
- R1(X, Y) [preserve X → Y]
- R2(Y, Z) [move Y → Z to separate relation]

**Result**: Both relations in 3NF ✓

---

### Problem 3: BCNF Violation

**Given**:
- Relation R(A, B, C, D, E)
- FDs: {A → BC, C → DE}

**Find**: Is R in BCNF? If not, normalize to BCNF.

**Solution**:

**Step 1: Find candidate key**
- A⁺ = {A, B, C, D, E}
- Candidate key: A

**Step 2: Check each FD against BCNF**
- FD: A → BC (determinant A is candidate key) ✓
- FD: C → DE (determinant C is NOT candidate key) ✗

**Conclusion**: NOT in BCNF

**Step 3: Decompose to BCNF**

Use violating FD: C → DE

Decompose:
- R1 = R - DE = (A, B, C)
- R2 = C ∪ DE = (C, D, E)

**Check if R1 in BCNF**:
- FD: A → BC (determinant A is superkey) ✓
- **R1 in BCNF** ✓

**Check if R2 in BCNF**:
- FD: C → DE (determinant C is superkey of R2) ✓
- **R2 in BCNF** ✓

**Result**: Both relations in BCNF ✓

---

### Problem 4: 2NF to 3NF Example

**Given**:
- Relation LOTS(Property_id#, County_name, Lot#, Area, Price)
- FDs: Property_id# → County_name; County_name → Tax_rate; Property_id#, Lot# → Area; Area → Price

**Find**: Normalize to 3NF.

**Solution**:

**Step 1: Identify keys and attributes**
- Candidate key: {Property_id#, Lot#}
- Prime: Property_id#, Lot#
- Non-prime: County_name, Area, Price, Tax_rate

**Step 2: Check 2NF**
- FD: Property_id# → County_name
  - Property_id# is part of key, County_name is non-prime
  - **Partial dependency** ✗

**Step 3: Decompose to 2NF**

Remove partial dependency:
- LOTS1(Property_id#, County_name, Lot#, Area, Price)
- LOTS2(County_name, Tax_rate)

**Step 4: Check 3NF on LOTS1**
- FD: Area → Price (Area NOT key, Price NOT prime)
  - **Transitive dependency** ✗

**Step 5: Decompose LOTS1 to 3NF**

Remove transitive dependency:
- LOTS1A(Property_id#, County_name, Lot#, Area)
- LOTS1B(Area, Price)

**Final Result** (3NF relations):
- LOTS1A(Property_id#, County_name, Lot#, Area)
- LOTS1B(Area, Price)
- LOTS2(County_name, Tax_rate)

---

## Normal Forms Summary Table

| Feature | 1NF | 2NF | 3NF | BCNF | 4NF | 5NF |
|---------|-----|-----|-----|------|-----|-----|
| Atomic values | ✓ | ✓ | ✓ | ✓ | ✓ | ✓ |
| No partial FD | ✗ | ✓ | ✓ | ✓ | ✓ | ✓ |
| No transitive FD | ✗ | ✗ | ✓ | ✓ | ✓ | ✓ |
| Every FD: X superkey | ✗ | ✗ | ✗ | ✓ | ✓ | ✓ |
| Every MVD: X superkey | ✗ | ✗ | ✗ | ✗ | ✓ | ✓ |
| Every JD: Ri superkey | ✗ | ✗ | ✗ | ✗ | ✗ | ✓ |
| Dependency preserve | ✗ | ✗ | ✓ | ✗ | ? | ✗ |
| Lossless join | ✓ | ✓ | ✓ | ✓ | ✓ | ✓ |

---

## Practical Guidelines

**Most databases**: Normalized to **3NF or BCNF**

**When to use 3NF**: Dependency preservation critical

**When to use BCNF**: Stronger guarantees needed, dependencies can be lost

**4NF**: When multivalued dependencies exist and independence needed

**5NF**: Rarely in practice; theoretical importance only
