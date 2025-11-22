# Practice Problems: Inference Rules and Closure

## Inference Rules Practice Problems

### Problem 1: Two FDs

**Given**: F = {A → B, BC → D}

**Derive**: AC → D

**Solution**:

Step 1: Given A → B; Augment both sides with C
- AC → BC (augmentation rule)

Step 2: 
- Derived: AC → BC
- Given: BC → D
- By transitivity: **AC → D** ✓

---

### Problem 2: Three FDs with Union

**Given**: F = {A → BC, B → D, C → E}

**Derive**: A → DE

**Solution**:

Step 1: Given A → BC; By decomposition
- A → B and A → C

Step 2: 
- Derived: A → B
- Given: B → D
- By transitivity: A → D

Step 3:
- Derived: A → C
- Given: C → E
- By transitivity: A → E

Step 4:
- Derived: A → D and A → E
- By union: **A → DE** ✓

---

### Problem 3: Chain of Transitivities

**Given**: F = {AB → C, C → D, D → E}

**Derive**: AB → E

**Solution**:

Step 1:
- Given: AB → C
- Given: C → D
- By transitivity: AB → D

Step 2:
- Derived: AB → D
- Given: D → E
- By transitivity: **AB → E** ✓

---

### Problem 4: Union and Transitivity

**Given**: F = {A → B, A → C, BC → D}

**Derive**: A → D

**Solution**:

Step 1:
- Given: A → B and A → C
- By union: A → BC

Step 2:
- Derived: A → BC
- Given: BC → D
- By transitivity: **A → D** ✓

---

### Problem 5: Complex Augmentation and Union

**Given**: F = {A → B, C → D, BD → E}

**Derive**: AC → E

**Solution**:

Step 1:
- Given: A → B
- Augment both sides with C: AC → BC

Step 2:
- Given: C → D
- Augment both sides with A: AC → AD

Step 3:
- By decomposition on AC → AD: AC → D

Step 4:
- By decomposition on AC → BC: AC → B

Step 5:
- By union on AC → B and AC → D: AC → BD

Step 6:
- Derived: AC → BD
- Given: BD → E
- By transitivity: **AC → E** ✓

---

### Problem 6: Street-Zip-City Example

**Given**: R = {Street, Zip, City}
**FDs**: F = {City Street → Zip, Zip → City}

**Show**: Street Zip → Street Zip City

**Solution**:

1. Zip → City (Given)
2. Street Zip → Street City (Augmentation of (1) by Street)
3. City Street → Zip (Given)
4. City Street → City Street Zip (Augmentation of (3) by City Street)
5. Street Zip → City Street Zip (Transitivity of (2) and (4))

**Result**: Street Zip → Street Zip City ✓

---

### Problem 7: ABCDEGHI with Multiple Dependencies

**Given**: R = (ABCDEGHI)
**FDs**: F = {AB → E, BE → I, E → G, GI → H}

**Show**: AB → GH

**Solution**:

1. AB → E (Given)
2. E → G (Given)
3. BE → I (Given)
4. GI → H (Given)
5. AB → G (Transitivity on 1 and 2)
6. AB → BE (Augmentation of 1 by B)
7. AB → I (Transitivity on 6 and 3)
8. AB → GI (Union of 5 and 7)
9. AB → H (Transitivity on 8 and 4)
10. **AB → GH** (Union of 5 and 9) ✓

---

### Problem 8: Seven-Attribute Relation

**Given**: R (A, B, C, D, E, F, G)
**FDs**: F = {A → B, BC → DE, AEG → G}

**Find**: Closure of A, AC, and ADE

**Solution**:

**A⁺ (Closure of A)**:
- A⁺ = {A}
- Apply A → B: A⁺ = {A, B}
- BC → DE doesn't apply (C not in A⁺)
- AEG → G doesn't apply (E, G not in A⁺)
- **A⁺ = {A, B}**

**AC⁺ (Closure of AC)**:
- AC⁺ = {A, C}
- Apply A → B: AC⁺ = {A, C, B}
- Apply BC → DE (B, C present): AC⁺ = {A, C, B, D, E}
- AEG → G doesn't apply (G not in AC⁺)
- **AC⁺ = {A, C, B, D, E}**

**ADE⁺ (Closure of ADE)**:
- ADE⁺ = {A, D, E}
- Apply A → B: ADE⁺ = {A, D, E, B}
- BC → DE doesn't apply (C not in ADE⁺)
- AEG → G doesn't apply (G not in ADE⁺)
- **ADE⁺ = {A, D, E, B}**

---

## Closure Practice Problems

### Closure Problem 1

**Given**: R (A, B, C, D, E)
**FDs**: F = {A → BC, CD → E, B → D, E → A}

**Find**: B⁺

**Solution**:

- B⁺ = {B}
- Apply B → D: B⁺ = {B, D}
- A → BC doesn't apply (A not in B⁺)
- CD → E doesn't apply (C not in B⁺)
- E → A doesn't apply (E not in B⁺)
- **B⁺ = {B, D}**

---

### Closure Problem 2

**Given**: R (A, B, C, D, E, F, G, H)
**FDs**: F = {A → BC, CD → E, D → AEH, E → C, ABH → BD, DH → BC}

**Find**: BCD⁺

**Solution**:

- BCD⁺ = {B, C, D}
- A → BC doesn't apply (A not in BCD⁺)
- CD → E applies (C, D present): BCD⁺ = {B, C, D, E}
- D → AEH applies (D present): BCD⁺ = {B, C, D, E, A, H}
- E → C already in set
- ABH → BD doesn't add new (A, B, H, D present, result in B, D)
- DH → BC doesn't add new (all in set)
- **BCD⁺ = {B, C, D, E, A, H}**

---

## Key Techniques for Solving FD Problems

### For Inference Rule Problems:

1. **Identify given FDs**
2. **Choose applicable inference rule**
   - Reflexive for trivial dependencies
   - Augmentation for adding attributes to both sides
   - Transitive for chains
   - Union for combining separate dependencies
   - Decomposition for splitting combined dependencies
3. **Show each step clearly**
4. **Verify logic**

### For Closure Problems:

1. **Start with attribute set X**
2. **Apply all FDs iteratively**
3. **Add RHS attributes if all LHS attributes present in current closure**
4. **Repeat until no new attributes added**
5. **Result is X⁺**

### Tips:

- Be careful with augmentation: add same attributes to both sides
- Transitivity requires Y of first FD equals X of second FD
- Union combines two separate FDs from same X
- Decomposition splits RHS into separate FDs
- Always verify FD applies (all LHS attributes in closure before adding RHS)
