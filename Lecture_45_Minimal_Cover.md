# Minimal Cover of Functional Dependencies

## What is Minimal Cover?

A **minimal cover** of a set of functional dependencies E is a minimal set of dependencies (in standard canonical form and without redundancy) that is equivalent to E.

**Purpose**: Reduce FD set to smallest equivalent form while maintaining same functional dependencies.

---

## Minimal Set Conditions

A set of FDs F is minimal if it satisfies ALL three conditions:

### Condition 1: Single Attribute RHS

Every dependency in F has exactly one attribute on the right-hand side (RHS).

**Example**:
- ✓ A → B (single attribute)
- ✗ A → BC (multiple attributes - NOT in canonical form)

### Condition 2: No Extraneous Attributes on LHS

Cannot replace X → A with Y → A (where Y ⊂ X) and maintain equivalence.

**Meaning**: Every attribute on LHS is necessary to determine RHS.

**Example**:
- AB → D cannot be replaced by A → D (both A and B needed)
- But if A → D directly holds, then AB → D is redundant

### Condition 3: No Redundant Dependencies

Cannot remove any FD and still maintain equivalence.

**Meaning**: Each FD is essential and cannot be derived from others.

**Example**: If A → B and B → C exist, then A → C is redundant (can derive via transitivity)

---

## Extraneous Attribute Definition

An **extraneous attribute** is an attribute in an FD that can be removed without changing the closure of the set of dependencies.

**Formal Definition**: Given F and FD X → A, attribute Y is extraneous in X if Y ⊆ X and F logically implies:

(F - {X → A}) ∪ {(X - Y) → A}

**Method to find extraneous attributes**:

1. For each attribute B in X of dependency X → A:
2. Remove B: X' = X - {B}
3. Compute X'⁺ using F
4. If A ∈ X'⁺, then B is extraneous (can be removed)

---

## Algorithm for Finding Minimal Cover

**Input**: Set E of functional dependencies

**Output**: Minimal cover F of E

### Step 1: Decompose RHS (Canonical Form)

Replace each FD X → {A₁, A₂, ..., Aₙ} with n separate FDs:
- X → A₁
- X → A₂
- ...
- X → Aₙ

**Result**: All FDs have single attribute on RHS

**Example**:
- Input: A → BC
- Output: A → B, A → C

### Step 2: Remove Redundant FDs

For each FD X → A:

1. Temporarily remove it: F' = F - {X → A}
2. Compute X⁺ with respect to F'
3. If A ∈ X⁺, then X → A is redundant (remove it)
4. Otherwise, keep X → A

**Result**: All remaining FDs are necessary

**Example**: If A → B, B → C, A → C exist:
- Remove A → C from set
- Compute A⁺ without A → C using {A → B, B → C}
- A⁺ = {A, B, C} (includes C)
- So A → C is redundant

### Step 3: Remove Extraneous Attributes

For each FD X → A where |X| > 1:

For each attribute Y in X:
1. Remove Y: X' = X - {Y}
2. Compute X'⁺ using current F
3. If A ∈ X'⁺, then Y is extraneous
4. Replace X → A with X' → A

**Result**: LHS of each FD has only essential attributes

**Example**: If AB → D exists:
- Check if A alone suffices: A⁺ (if includes D, B is extraneous)
- Check if B alone suffices: B⁺ (if includes D, A is extraneous)
- Keep AB → D only if both A⁺ and B⁺ don't include D

---

## Detailed Example: Finding Minimal Cover

### Problem

Find minimal cover of:

E = {B → A, D → A, AB → D}

### Solution

**Step 1: Canonical form**
- All FDs already have single RHS attribute
- F = {B → A, D → A, AB → D}

**Step 2: Remove extraneous attributes**

Check AB → D:
- Remove A: B⁺ using {B → A, D → A}
  - B⁺ = {B, A}
  - Does not include D
  - A is NOT extraneous

- Remove B: A⁺ using {B → A, D → A}
  - A⁺ = {A}
  - Does not include D
  - B is NOT extraneous

- By augmentation: B → A implies B → AB
- And AB → D given
- By transitivity: B → D
- So AB → D can be replaced by B → D

**Updated F = {B → A, D → A, B → D}**

**Step 3: Remove redundant FDs**

Check B → A:
- Remove it: F' = {D → A, B → D}
- B⁺ using F' = {B, D, A} (includes A via D → A)
- So B → A IS redundant via transitive path
- Remove B → A

Check D → A:
- Remove it: F' = {B → A, B → D}
- D⁺ using F' = {D}
- Does not include A
- D → A is NOT redundant

Check B → D:
- Remove it: F' = {B → A, D → A}
- B⁺ using F' = {B, A}
- Does not include D
- B → D is NOT redundant

**Final Minimal Cover: F = {B → D, D → A}**

---

## Complex Example: Minimal Cover

### Problem

Find minimal cover of:

G = {A → C, AB → C, C → DI, CD → I, EC → AB, EI → C}

### Solution

**Step 1: Decompose RHS**

F = {A → C, AB → C, C → D, C → I, CD → I, EC → A, EC → B, EI → C}

**Step 2: Remove redundant FDs**

- A → C: Keep (essential)
- AB → C: AB⁺ = {ABCDI} even without AB → C → redundant, REMOVE
- C → D: Keep (essential)
- C → I: Remove from F', C⁺ without C → I still = {CDI} via C → D, CD → I → redundant, REMOVE
- CD → I: Keep (not directly from C or D alone)
- EC → A: Keep (essential)
- EC → B: Keep (essential)
- EI → C: Keep (essential)

**After step 2: F = {A → C, C → D, CD → I, EC → A, EC → B, EI → C}**

**Step 3: Remove extraneous attributes**

- CD → I: Check if C or D extraneous
  - C⁺ = {CDI} (from C → D, CD → I) ✓ includes I, D is extraneous
  - Replace with C → I (but removed in step 2)
  - Correct form: C → D, then CD → I

**Final Minimal Cover: F = {A → C, C → D, CD → I, EC → A, EC → B, EI → C}**

---

## Key Points

| Step | Action | Result |
|------|--------|--------|
| 1 | Decompose RHS | Single attribute on right |
| 2 | Remove redundant FDs | No FD derivable from others |
| 3 | Remove extraneous attributes | Minimal LHS per FD |

**Testing equivalence**: Original and minimal cover have same F⁺ (same closure)

**Benefits of minimal cover**:
- Smaller set to work with
- Easier to understand dependencies
- Efficient for database design
- Useful for normalization

---

## Practice Tips

**For extraneous attributes**:
1. Always compute closure without candidate extraneous attribute
2. Check if RHS is in new closure
3. If yes, it's extraneous; if no, it's essential

**For redundant FDs**:
1. Remove FD temporarily
2. Try to derive it using remaining FDs
3. If derivable, remove it; if not, keep it

**Canonical form first**: Always decompose RHS before checking extraneous attributes and redundancy
