# Practice Problems: Normalization (Closure, Equivalence, Minimal Cover, 2NF, 3NF, BCNF)

## Closure Problems

### Problem 1: Closure with 7 Attributes

**Given**:
- Relation R(P, Q, R, S, T, U, V)
- FDs: {P → Q, QR → ST, PTU → V}

**Find**: Compute (QR)⁺ and (PR)⁺

**Solution**:

**Computing (QR)⁺**:

Step 1: Start with QR⁺ = {Q, R}

Step 2: Apply FDs
- P → Q: P not in QR⁺, skip
- QR → ST: Q and R both in QR⁺ → add S, T
  - QR⁺ = {Q, R, S, T}
- PTU → V: Not all of P, T, U in QR⁺, skip

Step 3: Check again for new applications
- P → Q: Already have Q
- QR → ST: Already applied
- PTU → V: Still cannot apply

**Result**: **(QR)⁺ = {Q, R, S, T}**

**Computing (PR)⁺**:

Step 1: Start with PR⁺ = {P, R}

Step 2: Apply FDs
- P → Q: P in PR⁺ → add Q
  - PR⁺ = {P, R, Q}
- QR → ST: Q and R both in PR⁺ → add S, T
  - PR⁺ = {P, R, Q, S, T}
- PTU → V: P, T in PR⁺, but U not in PR⁺, skip

Step 3: Check again
- P → Q: Already applied
- QR → ST: Already applied
- PTU → V: Still cannot apply (missing U)

**Result**: **(PR)⁺ = {P, R, Q, S, T}**

---

### Problem 2: Closure with 5 Attributes

**Given**:
- Relation R(P, Q, R, S, T)
- FDs: {P → QR, RS → T, Q → S, T → P}

**Find**: Compute (T)⁺

**Solution**:

Step 1: Start with T⁺ = {T}

Step 2: Apply FDs
- P → QR: P not in T⁺, skip
- RS → T: R, S not in T⁺, skip
- Q → S: Q not in T⁺, skip
- T → P: T in T⁺ → add P
  - T⁺ = {T, P}

Step 3: Check again with T⁺ = {T, P}
- P → QR: P in T⁺ → add Q, R
  - T⁺ = {T, P, Q, R}
- RS → T: R, S not both in T⁺ (missing S), skip
- Q → S: Q in T⁺ → add S
  - T⁺ = {T, P, Q, R, S}
- T → P: Already applied

Step 4: Check again with T⁺ = {T, P, Q, R, S}
- RS → T: R and S both in T⁺ → add T (already present)
- All attributes reached

**Result**: **(T)⁺ = {T, P, Q, R, S}** (all attributes)

---

## Equivalence Problems

### Problem: Equivalence of Two FD Sets

**Given**:
- Relation R(A, C, D, E, H)
- Set F: {A → C, AC → D, E → AD, E → H}
- Set G: {A → CD, E → AH}

**Question**: Which holds true?
- (A) G ⊇ F
- (B) F ⊇ G  
- (C) F = G
- (D) All of above

**Solution**:

### Test: Does F cover G?

Check if all FDs in G can be derived from F:

**For A → CD in G**:
- A⁺ using F: A → C (add C), then AC → D (add D)
- A⁺ = {A, C, D} ✓
- Contains CD ✓

**For E → AH in G**:
- E⁺ using F: E → AD (add A, D), E → H (add H), then A → C (add C)
- E⁺ = {E, A, D, H, C}
- Contains AH ✓

**Conclusion**: F covers G (F ⊇ G) ✓

### Test: Does G cover F?

Check if all FDs in F can be derived from G:

**For A → C in F**:
- A⁺ using G: A → CD (add C, D)
- Contains C ✓

**For AC → D in F**:
- AC⁺ using G: A → CD (add C, D)
- (AC)⁺ = {A, C, D}
- Contains D ✓

**For E → AD in F**:
- E⁺ using G: E → AH (add A, H), then A → CD (add C, D)
- E⁺ = {E, A, H, C, D}
- Contains AD ✓

**For E → H in F**:
- E⁺ using G: E → AH (add A, H)
- Contains H ✓

**Conclusion**: G covers F (G ⊇ F) ✓

**Final Answer**: F ⊇ G AND G ⊇ F → **F = G (Answer D)** ✓

---

## Normalization: 2NF

### Problem: Complex 2NF Normalization

**Given**:
- Relation R(P, Q, R, S, T, U, V, W, X, Y) - 10 attributes
- FDs: {PQ → R, PS → VW, QS → TU, P → X, W → Y}

**Question**: Is R in 2NF? If not, normalize to 2NF.

**Solution**:

**Step 1: Find Candidate Key**

Check PQS⁺:
- P → X (add X): {P, Q, S, X}
- PQ → R (add R): {P, Q, S, X, R}
- PS → VW (add V, W): {P, Q, S, X, R, V, W}
- QS → TU (add T, U): {P, Q, S, X, R, V, W, T, U}
- W → Y (add Y): {P, Q, S, X, R, V, W, T, U, Y}

**PQS⁺ = all 10 attributes** → **Candidate Key = {P, Q, S}**

**Step 2: Identify Prime and Non-Prime Attributes**

- Prime: P, Q, S (in candidate key)
- Non-prime: R, T, U, V, W, X, Y

**Step 3: Check for Partial Dependencies**

- FD: PQ → R: (PQ ⊂ PQS, R non-prime) → **Partial dependency** ✗
- FD: PS → VW: (PS ⊂ PQS, VW non-prime) → **Partial dependency** ✗
- FD: QS → TU: (QS ⊂ PQS, TU non-prime) → **Partial dependency** ✗
- FD: P → X: (P ⊂ PQS, X non-prime) → **Partial dependency** ✗
- FD: W → Y: (W non-prime → Y non-prime) → **No violation** ✓

**Conclusion**: NOT in 2NF (4 partial dependencies)

**Step 4: Decompose to 2NF**

Remove each partial dependency into separate relations:

- **R1(P, Q, R)**: FD PQ → R (now full)
- **R2(P, S, V, W)**: FD PS → VW (now full)
- **R3(Q, S, T, U)**: FD QS → TU (now full)
- **R4(P, X)**: FD P → X (now full)
- **R5(W, Y)**: FD W → Y (independent non-prime dependency)
- **R6(P, Q, S)**: Preserve candidate key

**All decomposed relations are in 2NF** ✓

---

## Normalization: 3NF

### Problem: 3NF Normalization

**Given**:
- Relation R(X, Y, Z, W, P)
- FDs: {X → Y, Y → P, Z → W}

**Question**: Is R in 3NF? If not, normalize to 3NF.

**Solution**:

**Step 1: Find Candidate Key**

Check XZ⁺:
- X → Y (add Y): {X, Z, Y}
- Y → P (add P): {X, Z, Y, P}
- Z → W (add W): {X, Z, Y, P, W}

**XZ⁺ = all 5 attributes** → **Candidate Key = {X, Z}**

**Step 2: Check 2NF** (First prerequisite for 3NF)

- FD: X → Y: (X ⊂ XZ) → **Partial dependency** ✗
- FD: Z → W: (Z ⊂ XZ) → **Partial dependency** ✗

**NOT in 2NF, decompose first**:
- R_temp1(X, Y, P)
- R_temp2(Z, W)
- R_temp3(X, Z)

**Step 3: Check 3NF on each decomposed relation**

On R_temp1(X, Y, P) with X → Y, Y → P:
- X is superkey of R_temp1 ✓
- Y is NOT superkey, P is NOT prime → **Transitive dependency** ✗

**Decompose further**:
- **R1(X, Y)**
- **R2(Y, P)**

On R_temp2(Z, W) with Z → W:
- Z is superkey ✓
- **R3(Z, W)** in 3NF ✓

On R_temp3(X, Z):
- **R4(X, Z)** in 3NF ✓

**Final 3NF Relations**:
- R1(X, Y)
- R2(Y, P)
- R3(Z, W)
- R4(X, Z)

---

## Normalization: BCNF

### Problem: BCNF Decomposition

**Given**:
- Relation R(W, X, Y, Z)
- FDs: {WX → Y, X → Z, Y → W}

**Question**: Is R in BCNF? If not, normalize to BCNF.

**Solution**:

**Step 1: Find Candidate Keys**

Check WX⁺: W, X → Y (add Y), then X → Z (add Z)
- WX⁺ = {W, X, Y, Z} ✓

Check XY⁺: X → Z (add Z), Y → W (add W), then WX → Y (already have Y)
- XY⁺ = {X, Y, Z, W} ✓

**Candidate Keys**: {WX, XY}

**Step 2: Check each FD for BCNF**

- FD: WX → Y: (WX is candidate key) ✓
- FD: X → Z: (X is NOT candidate key) ✗ **Violates BCNF**
- FD: Y → W: (Y is NOT candidate key) ✗ **Violates BCNF**

**NOT in BCNF**

**Step 3: Decompose Using Violating FD X → Z**

Decompose:
- R1 = R - Z = (W, X, Y)
- R2 = X ∪ Z = (X, Z)

**Step 4: Check if decomposed relations in BCNF**

**R1(W, X, Y)** with FDs WX → Y, Y → W:
- WX → Y: WX is key ✓
- Y → W: Y is NOT key, but W is prime ✓
- **In BCNF** ✓

**R2(X, Z)** with FD X → Z:
- X → Z: X is key ✓
- **In BCNF** ✓

**Final BCNF Relations**:
- R1(W, X, Y)
- R2(X, Z)

---

## Summary: Key Steps for Normalization

| Step | Action | Check |
|------|--------|-------|
| 1 | Find candidate key using closure | All attributes reachable |
| 2 | Identify prime/non-prime attributes | Attributes in/outside key |
| 3 | Check 2NF | No partial dependencies |
| 4 | Check 3NF | No transitive dependencies |
| 5 | Check BCNF | Every determinant is superkey |
| 6 | Decompose | Create new relations per violations |
| 7 | Verify | Check each decomposed relation |
