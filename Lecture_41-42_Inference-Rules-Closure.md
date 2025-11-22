# Inference Rules and Closure

## Inference Rules for Functional Dependencies

An **FD X → Y** is inferred from or implied by a set of dependencies F specified on R if X → Y holds in every legal relation state r of R.

**Purpose**: Given set of FDs F, we can infer additional FDs that hold whenever FDs in F hold.

### Example of Inference

If:
- Dept_no → Mgr_ssn (each department has one manager)
- Mgr_ssn → Mgr_phone (manager has unique phone)

Then:
- Dept_no → Mgr_phone (inferred FD)

This inferred FD need not be explicitly stated; it logically follows.

---

## Armstrong's Inference Rules

Three fundamental rules form sound and complete set of inference rules:

### IR1: Reflexive Rule

If Y is subset of X, then X → Y always holds.

**Formal**: If Y ⊆ X, then X → Y

**Example**: If X = {A, B, C} and Y = {B}, then ABC → B

### IR2: Augmentation Rule

If X → Y, then XZ → YZ always holds (adding same attributes to both sides).

**Formal**: If X → Y, then XZ → YZ

**Notation**: XZ stands for X ∪ Z

**Example**: 
- If {Ssn} → {Ename}
- Then {Ssn, Bdate} → {Ename, Bdate}

### IR3: Transitive Rule

If X → Y and Y → Z, then X → Z always holds.

**Formal**: If X → Y and Y → Z, then X → Z

**Example**:
- If {Ssn} → {Dnumber}
- And {Dnumber} → {Dname}
- Then {Ssn} → {Dname}

### Properties of Armstrong's Rules

- **Sound**: Every FD derived using these rules is valid (actually holds)
- **Complete**: All valid FDs that logically follow from F can be derived using these rules

---

## Additional Useful Inference Rules

### Decomposition Rule

If X → YZ, then X → Y and X → Z

**Meaning**: If X determines combined YZ, it determines each separately.

**Example**: If {Ssn} → {Ename, Address}, then {Ssn} → {Ename} and {Ssn} → {Address}

### Union/Additive Rule

If X → Y and X → Z, then X → YZ

**Meaning**: If X determines both Y and Z separately, it determines their union.

**Example**:
- If {Ssn} → {Ename} and {Ssn} → {Address}
- Then {Ssn} → {Ename, Address}

### Pseudo-Transitivity Rule

If X → Y and WY → Z, then WX → Z

**Meaning**: Transitivity with additional attributes involved.

### Important Caution

- X → A and X → B implies X → AB (union rule) ✓
- X → A and Y → B does NOT imply XY → AB ✗
- XY → A does NOT necessarily imply X → A or Y → A ✗

---

## Closure

**Closure** helps determine all possible functional dependencies or all attributes derived from given set of dependencies or attributes.

### Why Closure is Important

**Key identification**: Find which attribute sets determine all other attributes

**Testing normalization**: Check if relation satisfies normalization requirements

**Checking implication**: Determine if dependency X → Y can be derived from set F

---

## Two Types of Closure

### 1. Attribute Closure (X⁺)

**Definition**: The attribute closure of set of attributes X (denoted X⁺) is set of all attributes that are functionally determined by X, based on given set of FDs (F).

**Purpose**: Check whether X is superkey or candidate key.

**How to use**: If X⁺ = R (all attributes), then X is candidate key.

### Example of Attribute Closure

Given F = {A → B, B → C}:

**A⁺ closure**:
- Start with A
- A → B, add B (A⁺ = {A, B})
- B → C, add C (A⁺ = {A, B, C})
- Result: A⁺ = {A, B, C}

### 2. Functional Dependency Closure (F⁺)

**Definition**: The closure of set of FDs (F), denoted F⁺, is set of all FDs that can be logically inferred from F using Armstrong's Axioms.

**Represents**: All possible dependencies implied by F.

### Example of FD Closure

Given F = {A → B, B → C}:

F⁺ includes:
- A → B (given)
- B → C (given)
- A → C (by transitivity)
- AB → C (by augmentation)
- A → A (by reflexivity)
- And many more...

---

## Algorithm: Computing X⁺

```
Input: Set F of FDs on relation schema R, and set of attributes X ⊆ R

X+ := X
repeat
    oldX+ := X+
    for each FD Y → Z in F do
        if X+ ⊇ Y then X+ := X+ ∪ Z
until (X+ = oldX+)
```

**Process**:
1. Start with X⁺ = X
2. Apply FDs: if all attributes on LHS in X⁺, add RHS attributes to X⁺
3. Repeat until no new attributes added
4. Result is complete closure

---

## Practical Examples

### Example 1: Closure Problem

Given relation R(A, B, C, D, E, F, G) with FDs:
- A → B
- BC → DE
- AEG → G

**Find closure of A**:

Step 1: A⁺ = {A}
Step 2: A → B applies, add B. A⁺ = {A, B}
Step 3: BC → DE doesn't apply (C not in A⁺)
Step 4: AEG → G doesn't apply (E, G not in A⁺)
Result: **A⁺ = {A, B}**

**Find closure of AC**:

Step 1: AC⁺ = {A, C}
Step 2: A → B applies, add B. AC⁺ = {A, C, B}
Step 3: BC → DE applies (B, C in AC⁺), add D, E. AC⁺ = {A, C, B, D, E}
Step 4: AEG → G doesn't apply (G not in AC⁺)
Result: **AC⁺ = {A, C, B, D, E}**

### Example 2: CLASS Relation Closure

Relation: CLASS(Classid, Course#, Instr_name, Credit_hrs, Text, Publisher, Classroom, Capacity)

Given FDs:
- Classid → Course#, Instr_name, Credit_hrs, Text, Publisher, Classroom, Capacity
- Course# → Credit_hrs
- {Course#, Instr_name} → Text, Classroom
- Text → Publisher
- Classroom → Capacity

**Closure of {Classid}**:
{Classid}⁺ = {Classid, Course#, Instr_name, Credit_hrs, Text, Publisher, Classroom, Capacity} = CLASS

Interpretation: Classid is candidate key.

**Closure of {Course#}**:
{Course#}⁺ = {Course#, Credit_hrs}

Interpretation: Different instructors can teach same course with different texts, so not a key.

**Closure of {Course#, Instr_name}**:
{Course#, Instr_name}⁺ = {Course#, Instr_name, Credit_hrs, Text, Publisher, Classroom, Capacity}

Note: Does NOT include Classid, so not candidate key.

---

## Important Note on FD Closure Size

For relation R with n attributes:
- Total possible FD subsets: 2ⁿ on left side, 2ⁿ on right side
- Total possible FDs: 2^(2n)

Example: Relation with 10 attributes has 2^20 = 1,048,576 possible FDs!

Computing F⁺ can become computationally expensive.

---

## Practical Applications

### Testing Key Candidates

To check if attribute set X is candidate key:
1. Compute X⁺ using all FDs
2. If X⁺ = R, then X is candidate key
3. Check minimality (no proper subset is key)

### Testing FD Implication

To check if F ⊨ X → Y (does F imply X → Y):
1. Compute X⁺ under F
2. If Y ⊆ X⁺, then X → Y holds
3. Otherwise, X → Y does not follow from F

### Normalization Testing

Use closures to verify:
- Is relation in 2NF? (no partial dependencies on candidate key)
- Is relation in 3NF? (no transitive dependencies from non-key attributes)
- Is relation in BCNF? (every FD has candidate key as determinant)
