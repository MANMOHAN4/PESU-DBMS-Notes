# Equivalence of Functional Dependencies

## Covering and Equivalence Concepts

### Covering Definition

A set of functional dependencies **F is said to cover** another set of functional dependencies **E** if every FD in E is also in F⁺.

**Meaning**: Every dependency in E can be inferred from F. Alternatively, we say E is covered by F.

### Equivalence Definition

Two sets of functional dependencies **E and F are equivalent** if **E⁺ = F⁺**.

**Meaning**: Every FD in E can be inferred from F, AND every FD in F can be inferred from E.

**Condition**: E is equivalent to F if BOTH conditions hold:
- E covers F (F ⊆ E⁺)
- F covers E (E ⊆ F⁺)

### Testing Covering

**Method**: Determine whether F covers E by calculating X⁺ with respect to F for each FD X → Y in E, then checking whether this X⁺ includes the attributes in Y.

**If Y ⊆ X⁺ (with respect to F)**, then X → Y is covered by F.

---

## Three Cases of Equivalence

If F and G are two sets of functional dependencies, three cases are possible:

### Case 1: F Covers G (F ⊇ G)

F⁺ ⊇ G⁺ (closure of F contains closure of G)

**Steps to determine if F covers G**:

**Step 1**: For each FD X → Y in G, find closure of X using FDs in G
- Result: G-determined attributes

**Step 2**: For each FD X → Y in G, find closure of X using FDs in F
- Result: F-determined attributes

**Step 3**: Compare results
- If F-determined attributes ⊇ G-determined attributes → F covers G
- Otherwise → F does NOT cover G

### Case 2: G Covers F (G ⊇ F)

F⁺ ⊆ G⁺ (closure of F is subset of closure of G)

**Steps to determine if G covers F**:

**Step 1**: For each FD X → Y in F, find closure of X using FDs in F
- Result: F-determined attributes

**Step 2**: For each FD X → Y in F, find closure of X using FDs in G
- Result: G-determined attributes

**Step 3**: Compare results
- If G-determined attributes ⊇ F-determined attributes → G covers F
- Otherwise → G does NOT cover F

### Case 3: Both F and G Cover Each Other (F = G)

F⁺ = G⁺ (closures are identical)

**Condition**: If F covers G AND G covers F, then F = G (equivalent)

**Meaning**: Two sets are equivalent if they produce the same closure.

---

## Example: Testing Equivalence

### Problem

Given:
- F = {A → C, AC → D, E → AD, E → H}
- G = {A → CD, E → AH}

Which holds: G ⊇ F, F ⊇ G, F = G, or All of above?

### Solution: Test if F covers G

**Step 1**: Calculate closures using G

For A → CD in G:
- A⁺ = {A, C, D}

For E → AH in G:
- E⁺ = {A, C, D, E, H}

**Step 2**: Calculate closures using F

For A → CD in F:
- A⁺ = {A, C, D} (A → C, AC → D)

For E → AH in F:
- E⁺ = {A, C, D, E, H} (E → AD, E → H, then A → C, AC → D)

**Step 3**: Compare
- F-closures match G-closures
- **Conclusion: F covers G (F ⊇ G)** ✓

### Solution: Test if G covers F

**Step 1**: Calculate closures using F

For A → C in F:
- A⁺ = {A, C, D}

For AC → D in F:
- AC⁺ = {A, C, D}

For E → AD in F:
- E⁺ = {A, C, D, E, H}

For E → H in F:
- E⁺ = {A, C, D, E, H}

**Step 2**: Calculate closures using G

For A → C in G:
- A⁺ = {A, C, D}

For AC → D in G:
- AC⁺ = {A, C, D}

For E → AD in G:
- E⁺ = {A, C, D, E, H}

For E → H in G:
- E⁺ = {A, C, D, E, H}

**Step 3**: Compare
- G-closures match F-closures
- **Conclusion: G covers F (G ⊇ F)** ✓

### Final Answer

Since both F ⊇ G and G ⊇ F:
- **F = G (Equivalent)** ✓
- **All cases hold true** ✓

---

## Equivalence Example 2

### Problem

Given:
- F = {A → B, B → C, AB → D}
- G = {A → B, B → C, A → C, A → D}

Are F and G equivalent?

### Solution: Test if G covers F

Check each FD in G against F using closures:

**A → B in F**:
- A⁺(using F) = {A, B, C, D}
- A⁺(using G) = {A, B, C, D}
- Present ✓

**B → C in F**:
- B⁺(using F) = {B, C}
- B⁺(using G) = {B, C}
- Present ✓

**AB → D in F**:
- AB⁺(using F) = {A, B, C, D}
- D ∈ {A, B, C, D} ✓
- AB⁺(using G) = {A, B, C, D}
- D ∈ {A, B, C, D} ✓
- Can be derived ✓

**Conclusion: G covers F (G ⊇ F)** ✓

### Solution: Test if F covers G

Check each FD in F against G using closures:

**A → C in G**:
- A⁺(using F) = {A, B, C, D}
- C ∈ {A, B, C, D} ✓
- Can be derived from F ✓

**A → D in G**:
- A⁺(using F) = {A, B, C, D}
- D ∈ {A, B, C, D} ✓
- Can be derived from F ✓

**Conclusion: F covers G (F ⊇ G)** ✓

### Final Answer

Since F ⊇ G and G ⊇ F:
- **F = G (Equivalent)** ✓

---

## Key Points for Testing Equivalence

1. **Compute closures accurately** using given FD sets
2. **For each FD in set E**: compute X⁺ using set F and check if Y ⊆ X⁺
3. **Both directions matter**: F covers G AND G covers F for equivalence
4. **Transitive rules help**: Derived FDs through inference rules count as covered
5. **Order doesn't matter**: F = G and G = F are same equivalence relation
