# Practice Problems: Equivalence and Minimal Cover

## Equivalence Practice Problems

### Problem 1: Two FD Sets

**Given**:
- FD1 = {x → y, xy → z, w → xz, w → vy}
- FD2 = {x → yz, w → xv}

**Question**: Are they equivalent?

**Answer**: **FD1 ≡ FD2** (Yes, equivalent)

**Verification**:
- FD2 ⊇ FD1: Every FD in FD1 can be derived from FD2
  - x → y: Direct from x → yz (decomposition)
  - xy → z: Direct from x → yz (decomposition)
  - w → xz: Direct from w → xv using transitivity (may need verification)
  - w → vy: Can be derived

- FD1 ⊇ FD2: Every FD in FD2 can be derived from FD1
  - x → yz: From x → y and x → z (via xy → z with augmentation/union)
  - w → xv: From w → xz, w → vy via union

---

### Problem 2: Are These Equivalent?

**Given**:
- F = {A → B, B → C, A → C}
- G = {A → B, B → C, A → D}

**Question**: Are they equivalent?

**Answer**: **NOT equivalent**

**Explanation**:

**Check if G ⊇ F**:
- F contains A → C
- G does not contain A → C
- Can A → C be derived from G? NO (A → D is different)
- G does NOT cover F

**Check if F ⊇ G**:
- G contains A → D
- F does not contain A → D
- Can A → D be derived from F? NO (F has A → C, not A → D)
- F does NOT cover G

**Conclusion**: G ⊇ F and F ⊄ G
- Sets are NOT equivalent

---

## Minimal Cover Practice Problems

### Problem 1: Simple Minimal Cover

**Given**:
G = {v → w, vw → x, y → vxz}

**Find**: Minimal cover

**Solution**:

**Step 1: Decompose RHS**
```
F = {v → w, vw → x, y → v, y → x, y → z}
```

**Step 2: Remove redundant FDs**

- v → w: Keep (essential)
- vw → x: Keep for now
- y → v: Keep
- y → x: Check if redundant
  - Without y → x: y⁺ = {y, v, x} (via y → v, then vw → x? NO, need w)
  - Actually: y⁺ = {y, v, z} (via y → v and y → z)
  - x is not in closure without y → x
  - Keep y → x
- y → z: Keep

**Step 3: Remove extraneous attributes**

- vw → x: Check if v or w extraneous
  - v⁺ = {v, w} (via v → w only)
  - x not in v⁺, so v NOT extraneous
  - w⁺ = {w}
  - x not in w⁺, so w NOT extraneous
  - Keep vw → x

**Result**: Minimal cover = **{v → w, vw → x, y → v, y → x, y → z}**

Or simplified (union some): **{v → wx, y → vxz}**

---

### Problem 2: Minimal Cover with Redundancy

**Given**:
G = {A → B, C → B, D → ABC, AC → D}

**Find**: Minimal cover

**Solution**:

**Step 1: Decompose RHS**
```
F = {A → B, C → B, D → A, D → B, D → C, AC → D}
```

**Step 2: Remove redundant FDs**

- A → B: Keep
- C → B: Keep
- D → A: Keep
- D → B: Redundant? Remove and check: D⁺ = {D, A, C} then A⁺ = {A, B}, C⁺ = {C, B}
  - So D → ABC can give D → B via D → A → B and D → C → B
  - Actually need to check: without D → B, D⁺ = {D, A, C}, then using A → B: D⁺ = {D, A, C, B}
  - So D → B can be derived, REMOVE
- D → C: Keep
- AC → D: Keep

**After step 2**: {A → B, C → B, D → A, D → C, AC → D}

**Step 3: Remove extraneous attributes**

- AC → D: Check if A or C extraneous
  - A⁺ = {A, B}
  - D not in A⁺, so A NOT extraneous
  - C⁺ = {C, B}
  - D not in C⁺, so C NOT extraneous
  - Keep AC → D

**Result**: Minimal cover = **{A → B, C → B, D → AC, AC → D}**

---

### Problem 3: Complex Minimal Cover

**Given**:
G = {A → BCDE, CD → E}

**Find**: Minimal cover

**Solution**:

**Step 1: Decompose RHS**
```
F = {A → B, A → C, A → D, A → E, CD → E}
```

**Step 2: Remove redundant FDs**

- A → B: Keep
- A → C: Keep
- A → D: Keep
- A → E: Check if redundant
  - Without A → E: A⁺ = {A, B, C, D}
  - With CD → E: Need C, D in closure, both present
  - So A⁺ = {A, B, C, D, E} (via CD → E)
  - A → E IS redundant, REMOVE
- CD → E: Keep

**After step 2**: {A → B, A → C, A → D, CD → E}

**Step 3: Remove extraneous attributes**

- CD → E: Check if C or D extraneous
  - C⁺ = {C, D, E}? No, C⁺ = {C} only
  - E not in C⁺, so C NOT extraneous
  - D⁺ = {D}
  - E not in D⁺, so D NOT extraneous
  - Keep CD → E

**Result**: Minimal cover = **{A → BCD, CD → E}** or **{A → B, A → C, A → D, CD → E}**

---

## Candidate Key Practice Problems

### Problem 1: Finding Candidate Keys

**Given**:
- R = (A, B, C, D, E)
- Functional Dependencies:
  - AB → C
  - C → D
  - B → E

**Find**: All candidate keys and number of super keys

**Solution**:

**Step 1: Find essential attributes**

- Attributes appearing on LHS only: A, B, C
- Attributes appearing on RHS only: C, D, E
- Attributes appearing on both: C
- Attributes appearing nowhere: None
- **Essential attributes (never on RHS as sole determinant): A, B**

**Step 2: Check if essential attributes determine all**

(AB)⁺ = {A, B, E} (via B → E)
- Not all attributes included
- Need to include C or D

**Step 3: Check combinations**

- (AB)⁺ = {A, B, E} - NOT a key
- (ABC)⁺ = {A, B, C, D, E} ✓ - IS a key
  - A, B → C (given AB → C)
  - C → D (given)
  - B → E (given)
  
- (ABD)⁺ = {A, B, D, E}
  - Does not include C
  - NOT a key

- Check minimality of ABC:
  - (AB)⁺ = {A, B, E} - missing C, D
  - (AC)⁺ = {A, C, D} - missing B, E
  - (BC)⁺ = {B, C, D, E} - missing A
  - None are supersets containing all, so ABC cannot be reduced
  - **ABC is candidate key** ✓

**Candidate Keys**: ABC (only one)

**Super Keys**: Any set containing ABC
- ABC (required)
- ABCD, ABCE, ABCDE (add any/all from {D, E})
- Number of subsets of {D, E} = 2² = 4
- Total super keys = **4**: {ABC, ABCD, ABCE, ABCDE}

---

### Problem 2: Multiple Candidate Keys

**Given**:
- R = (C, E, F)
- Functional Dependencies:
  - C → F
  - E → A
  - EC → D
  - A → B

**Find**: Candidate key(s) and total super keys

**Solution**:

**Step 1: Essential attributes**

- On LHS only: C, E
- On RHS only: F, A, D, B
- Essential: C, E

**Step 2: Check if CE determines all**

(CE)⁺ = {C, E, F, A, D, B}

With the given attributes {C, E, F} in R:
- (CE)⁺ from R's perspective = {C, E, F}
  - C → F gives F
- **CE⁺ = {C, E, F}** ✓

**Candidate Key**: CE (only one)

**Super Keys**: Any set containing CE within R
- R = {C, E, F}
- CE is already in R
- Only super key: **{C, E, F}**
- Total super keys = **1**

---

## Algorithm: Finding Candidate Keys

### Steps

1. **Find essential attributes**: Attributes on LHS only (never on RHS as sole)

2. **Check if essential attributes form key**: Compute closure
   - If closure = all attributes → candidate key
   - Otherwise, continue

3. **Add non-essential attributes**: Try combinations
   - Essential + each non-essential
   - Essential + combinations of non-essentials

4. **Check minimality**: For each candidate, verify no proper subset is also candidate

5. **Count super keys**: 2^(number of non-essential attributes that aren't forced)

---

## Quick Reference

| Term | Definition |
|------|-----------|
| Equivalence | F = G means F⁺ = G⁺ |
| Covering | F covers E if E ⊆ F⁺ |
| Extraneous attribute | Can be removed from LHS without changing closure |
| Redundant FD | Can be derived from other FDs |
| Minimal cover | Canonical, no extraneous, no redundant FDs |
| Candidate key | Minimal set determining all attributes |
| Super key | Any set containing candidate key |
