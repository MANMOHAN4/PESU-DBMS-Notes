# Set Operations, Equivalent Queries, and Additional Relational Algebra

## Set Operations Overview

**Definition**:
Binary operations applied to union-compatible relations combining or comparing tuples across relations.

**Three Main Set Operations**:
1. UNION (∪)
2. INTERSECTION (∩)
3. SET DIFFERENCE (−)

---

## Union Compatibility Requirement

**Definition**:
Two relations are union-compatible if:

1. **Same Arity**: Both relations have same number of attributes
2. **Compatible Domains**: Corresponding attributes have compatible data types
   - 1st attribute of R must have same domain as 1st attribute of S
   - 2nd attribute of R must have same domain as 2nd attribute of S
   - And so on...

**Example - Compatible Relations**:
```
R (ID, Name, Salary)        - 3 attributes
S (EmployeeID, EmpName, Pay) - 3 attributes (compatible domains)
Union-compatible: YES
```

**Example - Incompatible Relations**:
```
R (ID, Name, Salary)    - 3 attributes
S (Name, Age)           - 2 attributes (different arity)
Union-compatible: NO
```

---

## UNION Operation (∪)

**Purpose**:
Combines all tuples from both relations, eliminating duplicates.

**Definition**:
R ∪ S produces relation containing all tuples in R or in S or in both.

**Notation**:
```
R ∪ S
```

**Properties**:
- **Commutative**: R ∪ S = S ∪ R
- **Associative**: (R ∪ S) ∪ T = R ∪ (S ∪ T)
- **Duplicates eliminated**: Set semantics applied
- Result size ≤ |R| + |S|

**Example**:
```
Query: Find all courses taught in Fall 2017 or Spring 2018 (or both)

FALL_2017 ← ∏course_id (σ semester="Fall" ∧ year=2017 (section))
SPRING_2018 ← ∏course_id (σ semester="Spring" ∧ year=2018 (section))
RESULT ← FALL_2017 ∪ SPRING_2018

Courses taught in either semester (duplicates removed)
```

**Real-World Example**:
```
Query: SSN of all employees who work in Dept 5 OR supervise someone in Dept 5

DEPT5_EMPS ← ∏SSN (σ DNO=5 (EMPLOYEE))
SUPERVISORS ← ∏SSN (EMPLOYEE ⋈ DEPT5_EMPS)
RESULT ← DEPT5_EMPS ∪ SUPERVISORS
```

---

## INTERSECTION Operation (∩)

**Purpose**:
Returns tuples common to both relations.

**Definition**:
R ∩ S produces relation containing tuples in BOTH R and S.

**Notation**:
```
R ∩ S
```

**Properties**:
- **Commutative**: R ∩ S = S ∩ R
- **Associative**: (R ∩ S) ∩ T = R ∩ (S ∩ T)
- Result size ≤ min(|R|, |S|)

**Example**:
```
Query: Find courses taught in both Fall 2017 and Spring 2018

FALL_2017 ← ∏course_id (σ semester="Fall" ∧ year=2017 (section))
SPRING_2018 ← ∏course_id (σ semester="Spring" ∧ year=2018 (section))
RESULT ← FALL_2017 ∩ SPRING_2018

Courses offered both semesters
```

**Real-World Example**:
```
Query: Find people who are both Graduates and Managers

GRADUATES ∩ MANAGERS

Result: People in both relations
```

---

## SET DIFFERENCE Operation (−)

**Purpose**:
Returns tuples in first relation but not in second.

**Definition**:
R − S produces relation containing tuples in R that are NOT in S.

**Notation**:
```
R − S
```

**Properties**:
- **NOT Commutative**: R − S ≠ S − R
- R − S contains tuples only in R
- S − R contains tuples only in S
- Result size ≤ |R|

**Example**:
```
Query: Courses taught in Fall 2017 but not in Spring 2018

FALL_2017 ← ∏course_id (σ semester="Fall" ∧ year=2017 (section))
SPRING_2018 ← ∏course_id (σ semester="Spring" ∧ year=2018 (section))
RESULT ← FALL_2017 − SPRING_2018

Courses only in Fall 2017 (not offered in Spring 2018)
```

**Real-World Example**:
```
Query: Students NOT enrolled in any course

ALL_STUDENTS ← ∏SID (STUDENT)
ENROLLED_STUDENTS ← ∏SID (ENROLLED)
RESULT ← ALL_STUDENTS − ENROLLED_STUDENTS
```

---

## Properties of Set Operations

### Commutativity
- **Commutative operations**: R ∪ S = S ∪ R, R ∩ S = S ∩ R
- **Non-commutative**: R − S ≠ S − R (order matters)

### Associativity
```
Union: R ∪ (S ∪ T) = (R ∪ S) ∪ T
Intersection: R ∩ (S ∩ T) = (R ∩ S) ∩ T
Difference: Not associative in general
```

### Expressing Intersection Using Union and Difference
```
R ∩ S = ((R ∪ S) − (R − S)) − (S − R)

Why? 
(R ∪ S) = all tuples in either
(R − S) = tuples only in R
(S − R) = tuples only in S
Subtracting both gives tuples in both
```

---

## DIVISION Operation (÷)

**Purpose**:
Finds values that appear with all combinations of another relation.

**Definition**:
R(Z) ÷ S(X) where X ⊆ Z

Let Y = Z − X (attributes in R not in S)
Result T(Y) includes tuple t where for every tuple ts in S, there exists tuple tr in R such that:
- tr[Y] = t
- tr[X] = ts

**Example**:
```
Query: Find employees who worked on ALL projects

WORKS_ON (SSN, Project_ID)
ALL_PROJECTS (Project_ID)

Result ← WORKS_ON ÷ ALL_PROJECTS

Result: SSNs of employees who worked on every project
```

**Complexity**:
- Division is the most complex set operation
- Rarely used but important conceptually
- Useful for "for all" queries

---

## CARTESIAN PRODUCT (×)

**Purpose**:
Combines every tuple from first relation with every tuple from second.

**Notation**:
```
R × S
```

**Result**:
- **Tuples**: |R| × |S| (product of counts)
- **Attributes**: All attributes from R and all from S (concatenated)
- **Schema**: Q(A₁, A₂, ..., Aₙ, B₁, B₂, ..., Bₘ)

**Example**:
```
EMPLOYEE (8 tuples)
DEPENDENT (4 tuples)

EMPLOYEE × DEPENDENT = 8 × 4 = 32 tuples
```

**Important Caveat**:
- Usually not meaningful by itself (produces all combinations)
- Becomes meaningful when combined with SELECT
- Forms basis for JOIN operation

**Making Cartesian Product Meaningful**:
```
❌ WRONG - Meaningless:
EMP_DEPS ← EMPLOYEE × DEPENDENT
(All combinations - includes unrelated employees/dependents)

✅ RIGHT - Meaningful:
EMP_DEPS ← EMPLOYEE × DEPENDENT
ACTUAL_DEPS ← σ_SSN=ESSN (EMP_DEPS)
(Only related employees and their dependents)

Or simpler: σ_SSN=ESSN (EMPLOYEE × DEPENDENT)
```

---

## JOIN Operation (⋈)

**Purpose**:
Combines relations based on join condition (implicit Cartesian product + select).

**Notation**:
```
R ⋈_condition S
```

**Advantage over Cartesian Product**:
- More efficient (filters during join, not after)
- Produces only meaningful combinations
- Automatically handles matching attributes

**Types of Joins**:

**1. Natural Join (⋈)**
- Joins on all common attributes automatically
- Removes duplicate columns

**2. Equi-join**
- Condition uses equality: R.ID = S.EmployeeID

**3. Theta-join**
- Condition uses any comparison: R.Salary > S.AvgSalary

**Example**:
```
Query: Manager names and department names they manage

MANAGER ⋈ DEPARTMENT
(Natural join on common attributes)
```

---

## Equivalent Queries

**Definition**:
Different relational algebra expressions producing same result on any database instance.

**Example**:
```
Query: Find instructors in Physics with salary > 90,000

Query 1:
σ_(dept_name="Physics" ∧ salary>90000) (INSTRUCTOR)

Query 2:
σ_dept_name="Physics" (σ_salary>90000 (INSTRUCTOR))

Query 3:
σ_salary>90000 (σ_dept_name="Physics" (INSTRUCTOR))

All three are equivalent (commutative property of SELECT)
```

**Why Multiple Equivalent Queries**:
1. **Optimization opportunity**: Different execution orders have different costs
2. **Flexible expression**: Multiple valid ways to express same query
3. **Query optimization**: Database chooses most efficient equivalent form

**Optimization Rule - Cascade of SELECT**:
```
σ_p1 (σ_p2 (R)) = σ_(p1 ∧ p2) (R) = σ_p2 (σ_p1 (R))

Best practice: Apply most restrictive condition first
(Reduces intermediate results)
```

---

## Practical Query Examples

### Example 1: UNION
```
Query: Students who are in CS major OR have GPA > 3.8

CS_STUDENTS ← σ_major="CS" (STUDENT)
HIGH_GPA ← σ_GPA>3.8 (STUDENT)
RESULT ← CS_STUDENTS ∪ HIGH_GPA
```

### Example 2: INTERSECTION
```
Query: Courses taught in both 2023 and 2024

COURSES_2023 ← ∏course_id (σ_year=2023 (SECTION))
COURSES_2024 ← ∏course_id (σ_year=2024 (SECTION))
RESULT ← COURSES_2023 ∩ COURSES_2024
```

### Example 3: SET DIFFERENCE
```
Query: Employees in Marketing NOT in Management

MARKETING ← σ_dept="Marketing" (EMPLOYEE)
MANAGEMENT ← σ_dept="Management" (EMPLOYEE)
RESULT ← MARKETING − MANAGEMENT
```

### Example 4: Combining Operations
```
Query: Employees in (Dept5) OR supervising (Dept5) but NOT both

DEPT5 ← σ_DNO=5 (EMPLOYEE)
SUPERVISORS ← ∏SSN (EMPLOYEE ⋈ DEPT5)
IN_BOTH ← DEPT5 ∩ SUPERVISORS
RESULT ← (DEPT5 ∪ SUPERVISORS) − IN_BOTH
```

---

## Key Takeaways

1. **Set Operations**: Combine/compare entire relations
2. **Union-Compatibility Required**: Same arity, compatible domains
3. **UNION**: All tuples from both (duplicates eliminated)
4. **INTERSECTION**: Common tuples only
5. **SET DIFFERENCE**: Tuples in first but not second (NOT commutative)
6. **CARTESIAN PRODUCT**: All combinations (usually needs SELECT filter)
7. **JOIN**: Cartesian product with condition built-in
8. **Equivalent Queries**: Multiple valid expressions for same query
9. **Optimization**: Different execution plans have different costs
