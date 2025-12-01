# Set Operations, Equivalent Queries, Binary Algebra, and Aggregate Functions

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
or
r ∪ s
```

**Properties**:
- **Commutative**: R ∪ S = S ∪ R
- **Associative**: (R ∪ S) ∪ T = R ∪ (S ∪ T)
- **Duplicates eliminated**: Set semantics applied
- Result size ≤ |R| + |S|

**Example 1: Courses in Different Semesters**:
```
Query: Find all courses taught in Fall 2017 or Spring 2018 (or both)

∏_course_id (σ_semester="Fall" ∧ year=2017 (section)) 
∪ 
∏_course_id (σ_semester="Spring" ∧ year=2018 (section))

Output: All unique course IDs taught in either semester
CS-101, CS-315, CS-190, CS-319, CS-347, FIN-201, HIS-351, MU-199, PHY-101
```

**Example 2: Union with Renaming**:
```
Problem: Paternity vs Maternity relations with different attribute names

Paternity (Father, Child)
Maternity (Mother, Child)

❌ WRONG: Paternity ∪ Maternity
Problem: Father and Mother are different names

✅ RIGHT:
ρ_Parent→Parent (Paternity) ∪ ρ_Parent→Parent (Maternity)

Result: Parent-Child pairs from both relations
Parent | Child
Adam  | Cain
Adam  | Abel
Abraham | Isaac
Eve   | Cain
Eve   | Seth
Sarah | Isaac
Hagar | Ishmael
```

**Example 3: Employee SSNs**:
```
Query: SSN of employees in Dept 5 OR supervising someone in Dept 5

RESULT1 ← ∏_SSN (σ_DNO=5 (EMPLOYEE))
RESULT2 ← ∏_SSN (EMPLOYEE ⋈_Mgr_ssn=SSN RESULT1)
RESULT ← RESULT1 ∪ RESULT2

Result: Union of direct employees and supervisors
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
or
r ∩ s
```

**Properties**:
- **Commutative**: R ∩ S = S ∩ R
- **Associative**: (R ∩ S) ∩ T = R ∩ (S ∩ T)
- Result size ≤ min(|R|, |S|)

**Example 1: Courses in Both Semesters**:
```
Query: Find courses taught in BOTH Fall 2017 AND Spring 2018

FALL_2017 ← ∏_course_id (σ_semester="Fall" ∧ year=2017 (section))
SPRING_2018 ← ∏_course_id (σ_semester="Spring" ∧ year=2018 (section))
RESULT ← FALL_2017 ∩ SPRING_2018

Output: Courses offered in both semesters
```

**Example 2: Student-Instructor Overlap**:
```
Query: People who are both Students AND Instructors

STUDENT | Fn      | Ln
---------|---------|--------
         | Susan   | Yao
         | Ramesh  | Shah
         | Johnny  | Kohler
         | Barbara | Jones
         | Amy     | Ford
         | Jimmy   | Wang
         | Ernest  | Gilbert

INSTRUCTOR | Fname   | Lname
------------|---------|--------
           | John    | Smith
           | Ricardo | Browne
           | Susan   | Yao
           | Francis | Johnson
           | Ramesh  | Shah

RESULT ← STUDENT ∩ INSTRUCTOR

Output:
Fn      | Ln
--------|--------
Susan   | Yao
Ramesh  | Shah
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
or
r − s
```

**Properties**:
- **NOT Commutative**: R − S ≠ S − R
- R − S contains tuples only in R
- S − R contains tuples only in S
- Result size ≤ |R|

**Example 1: Exclusive Courses**:
```
Query: Courses taught in Fall 2017 but NOT in Spring 2018

FALL_2017 ← ∏_course_id (σ_semester="Fall" ∧ year=2017 (section))
SPRING_2018 ← ∏_course_id (σ_semester="Spring" ∧ year=2018 (section))
RESULT ← FALL_2017 − SPRING_2018

Output: Courses only in Fall 2017 (not offered in Spring 2018)
```

**Example 2: Students Only (Not Instructors)**:
```
RESULT ← STUDENT − INSTRUCTOR

Output: People who are students but NOT instructors
Fn      | Ln
--------|----------
Johnny  | Kohler
Barbara | Jones
Amy     | Ford
Jimmy   | Wang
Ernest  | Gilbert
```

**Example 3: Non-Commutative Property**:
```
INSTRUCTOR − STUDENT:

Fname   | Lname
--------|--------
John    | Smith
Ricardo | Browne
Francis | Johnson

Clearly: (STUDENT − INSTRUCTOR) ≠ (INSTRUCTOR − STUDENT)
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
Difference: NOT associative in general
```

### Expressing Intersection Using Union and Difference
```
R ∩ S = ((R ∪ S) − (R − S)) − (S − R)

Why this works:
(R ∪ S)         = all tuples in either relation
(R − S)         = tuples only in R
(S − R)         = tuples only in S
Remove both     = leaves only tuples in both
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

**Notation**:
```
R ÷ S
or
T ← R(Z) ÷ S(X)
```

**Example**:
```
Query: Find SSNs of employees who worked on ALL projects

SSN_PNOS (Essn, Pno):
Essn      | Pno
-----------|------
123456789  | 1
123456789  | 2
666884444  | 3
453453453  | 1
453453453  | 2
333445555  | 2
333445555  | 3
333445555  | 10
333445555  | 20
... more rows

SMITH_PNOS (Pno):
Pno
----
1
2

RESULT ← SSN_PNOS ÷ SMITH_PNOS

Output:
Ssn
-----------
123456789    (worked on projects 1 AND 2)
453453453    (worked on projects 1 AND 2)

These employees worked on BOTH projects that Smith worked on
```

---

## CARTESIAN PRODUCT (×)

**Purpose**:
Combines every tuple from first relation with every tuple from second.

**Notation**:
```
R × S
or
r × s
```

**Result**:
- **Tuples**: |R| × |S| (product of counts)
- **Attributes**: All attributes from R and all from S (concatenated)
- **Schema**: Q(A₁, A₂, ..., Aₙ, B₁, B₂, ..., Bₘ)

**Example - Size Calculation**:
```
EMPLOYEE (8 tuples)
DEPENDENT (4 tuples)

EMPLOYEE × DEPENDENT = 8 × 4 = 32 tuples
(Each employee paired with each dependent)
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
or
R ⋈ S
```

**Advantage over Cartesian Product**:
- More efficient (filters during join, not after)
- Produces only meaningful combinations
- Automatically handles matching attributes

**Types of Joins**:

**1. Natural Join (⋈)**
- Joins on all common attributes automatically
- Removes duplicate columns

**2. Theta-join**
- Condition uses any comparison: R.ID = S.EmployeeID
- Can use =, ≠, >, ≥, <, ≤

**3. Equi-join**
- Condition uses equality operator

**Example**:
```
Query: Manager names and department names they manage

DEPT_MGR ← DEPARTMENT ⋈_Mgr_ssn=SSN EMPLOYEE
RESULT ← ∏_Dname, Lname, Fname (DEPT_MGR)

Output:
Dname           | Lname   | Fname
-----------------|---------|--------
Research        | Wong    | Franklin
Administration  | Wallace | Jennifer
Headquarters    | Borg    | James
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
(Reduces intermediate results quickly)

Example:
σ_dept="Physics" (σ_salary>90000 (INSTRUCTOR))

More efficient than:
σ_salary>90000 (σ_dept="Physics" (INSTRUCTOR))

If dept "Physics" is small, first approach filters faster
```

---

## Aggregate Functions

### Overview

**Definition**:
Functions that take a collection of values as input and return a single value.

**Five Aggregate Functions**:
1. COUNT
2. SUM
3. AVERAGE (AVG)
4. MAXIMUM (MAX)
5. MINIMUM (MIN)

**Purpose**:
Express requests that cannot be written in basic relational algebra, such as:
- Average or total salary of all employees
- Total number of employee tuples
- Maximum salary by department
- Count of projects per employee

### COUNT Function

**Purpose**: Count number of tuples in a relation

**Syntax**:
```
ℱ_COUNT (R)
or
COUNT(*) (R)
or
ℑ_COUNT SSN (EMPLOYEE)
```

**Characteristics**:
- Works on both numeric and non-numeric data types
- Counts all rows including NULL values (when using *)
- Can count specific attributes

**Example**:
```
Relation R:
A | B
--|--
1 | 5
2 | 6
3 | 8
4 | 10

ℱ_COUNT (R) = 4

Output: 4 (number of tuples)
```

---

### SUM Function

**Purpose**: Calculate sum of all selected numeric values

**Syntax**:
```
ℱ_SUM Salary (EMPLOYEE)
or
SUM Salary (EMPLOYEE)
```

**Characteristics**:
- Works on numeric fields only
- Ignores NULL values
- Returns total of all values

**Example**:
```
Query: Find total salary of all employees

EMPLOYEE:
Fname    | Salary
---------|--------
John     | 30000
Franklin | 40000
Alicia   | 25000
Jennifer | 43000
Ramesh   | 38000
Joyce    | 25000
Ahmad    | 25000
James    | 55000

ℱ_SUM Salary (EMPLOYEE) = 281000

Output: 281000 (total of all salaries)
```

---

### AVERAGE Function

**Purpose**: Calculate average value of numeric data

**Syntax**:
```
ℱ_AVG Salary (EMPLOYEE)
or
AVG Salary (EMPLOYEE)
```

**Characteristics**:
- Works on numeric fields only
- Returns average of all non-NULL values
- Ignores NULL values

**Example**:
```
Relation R:
C
---
3
5
8
10

ℱ_AVG (C) (R) = (3+5+8+10) / 4 = 26 / 4 = 6.5

Output: 6.5
```

---

### MINIMUM Function

**Purpose**: Find minimum value in a column

**Syntax**:
```
ℱ_MIN Salary (EMPLOYEE)
or
MIN Salary (EMPLOYEE)
```

**Characteristics**:
- Finds smallest value
- Works on numeric and non-numeric data
- Returns single value

**Example**:
```
Relation R:
C
---
3
5
8
10

ℱ_MIN (C) (R) = 3

Output: 3 (smallest value)
```

---

### MAXIMUM Function

**Purpose**: Find maximum value in a column

**Syntax**:
```
ℱ_MAX Salary (EMPLOYEE)
or
MAX Salary (EMPLOYEE)
```

**Characteristics**:
- Finds largest value
- Works on numeric and non-numeric data
- Returns single value

**Example**:
```
Relation R:
C
---
3
5
8
10

ℱ_MAX (C) (R) = 10

Output: 10 (largest value)
```

---

## Aggregate Functions Without Grouping

**Example: Company-Wide Statistics**:
```
Query: Find sum of salaries, maximum salary, minimum salary, and average salary

ρ_R(Total_Sal, Highest_Sal, Lowest_Sal, Average_Sal) (
  ℱ_SUM Salary, MAX Salary, MIN Salary, AVG Salary (EMPLOYEE)
)

Output:
Total_Sal | Highest_Sal | Lowest_Sal | Average_Sal
----------|-------------|------------|------------
281000    | 55000       | 25000      | 35125
```

---

## Grouping (GROUP BY)

**Purpose**:
Apply aggregate functions to subgroups of data instead of entire relation.

**Concept**:
- Divide data into non-overlapping groups based on specified attributes
- Group consists of tuples with same value of grouping attribute(s)
- Apply function independently to each group
- Produces summary information per group

**Syntax**:
```
GroupingAttr ℱ_Function1, Function2, ... (RELATION)
```

**Example Grouping by Department**:
```
Query: Find average salary for each department

Dno ℱ_COUNT SSN, AVG Salary (EMPLOYEE)

This groups employees by DNO (department number) and computes:
- Count of employees per department
- Average salary per department

Output:
Dno | COUNT(*) | AVG_Salary
----|----------|------------
1   | 1        | 55000
4   | 3        | 36333.33
5   | 4        | 33250
```

**Example 2: Multiple Grouping Attributes**:
```
Query: Retrieve each department number, number of employees, and average salary

ρ_R(Dno, No_of_employees, Average_sal) (
  Dno ℱ_COUNT SSN, AVG Salary (EMPLOYEE)
)

Output:
Dno | No_of_employees | Average_sal
----|-----------------|------------
1   | 1               | 55000
4   | 3               | 36333.33
5   | 4               | 33250
```

---

## Practical Aggregate Query Examples

### Example 1: Total Numbers Only
```
Query: Find total employees and average salary across entire company

ℱ_COUNT SSN, AVG Salary (EMPLOYEE)

Output:
COUNT | AVG_Salary
------|----------
8     | 35125
```

### Example 2: Per-Department Summary
```
Query: Show DNO, count of employees, and average salary per department

Dno ℱ_COUNT SSN, AVG Salary (EMPLOYEE)

Output:
Dno | COUNT_SSN | AVG_Salary
----|-----------|----------
1   | 1         | 55000
4   | 3         | 36333.33
5   | 4         | 33250
```

### Example 3: Complex Aggregation
```
Query: For employees earning > $30,000, average salary by department

Dno ℱ_COUNT SSN, AVG Salary (σ_Salary>30000 (EMPLOYEE))

Output:
Dno | COUNT | AVG_Salary
----|-------|----------
1   | 1     | 55000
4   | 2     | 43000
5   | 2     | 40000
```

---

## Common Aggregate Patterns

### Pattern 1: Overall Statistics
```
ℱ_SUM Salary, MAX Salary, MIN Salary, AVG Salary (RELATION)
```

### Pattern 2: Grouped Statistics
```
GroupAttr ℱ_COUNT(*), AVG Value (RELATION)
```

### Pattern 3: Filtered Then Aggregated
```
ℱ_COUNT SSN, AVG Salary (σ_condition (EMPLOYEE))
```

### Pattern 4: Multiple Grouping
```
Attr1, Attr2 ℱ_COUNT(*), SUM Value (RELATION)
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
8. **DIVISION**: Finds values appearing with all combinations
9. **Equivalent Queries**: Multiple valid expressions for same query
10. **Aggregate Functions**: COUNT, SUM, AVG, MIN, MAX
11. **GROUP BY**: Apply aggregates to data subgroups
12. **Optimization**: Different execution plans have different costs

---

## Quick Reference: Operator Properties

| Operator | Commutative | Associative | Union-Compatible Required |
|----------|-------------|-------------|--------------------------|
| UNION    | YES         | YES         | YES                       |
| INTERSECT| YES         | YES         | YES                       |
| MINUS    | NO          | NO          | YES                       |
| CARTESIAN| NO          | NO          | NO                        |
| JOIN     | YES*        | YES*        | NO                        |

*Under certain conditions

---

## Practice Problem Solutions

### Problem 1: Set Operations
```
Query: Employees in Sales OR Management (or both)

SALES_EMPS ← σ_Dept="Sales" (EMPLOYEE)
MGMT_EMPS ← σ_Dept="Management" (EMPLOYEE)
RESULT ← SALES_EMPS ∪ MGMT_EMPS
```

### Problem 2: Filtering Intersection
```
Query: Courses offered in both semesters

FALL ← ∏_CourseID (σ_Semester="Fall" (SECTION))
SPRING ← ∏_CourseID (σ_Semester="Spring" (SECTION))
RESULT ← FALL ∩ SPRING
```

### Problem 3: Aggregation
```
Query: Average salary per department

Dno ℱ_AVG Salary (EMPLOYEE)
```

### Problem 4: Grouped and Filtered
```
Query: Average salary > 40000 by department

Dno ℱ_AVG Salary (σ_Salary>40000 (EMPLOYEE))
```

