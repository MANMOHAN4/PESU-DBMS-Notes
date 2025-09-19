# Lecture 22 – Nested Queries

### Prepared by: Prof. Mahitha G

*Department of Computer Science and Engineering*  
Slides adapted from:
- “Database System Concepts” – Silberschatz, Korth, Sudarshan (McGrawHill, 7th Ed., 2019)
- “Fundamentals of Database Systems” – Elmasri, Navathe (Pearson, 7th Ed., 2017)

---

# Introduction to Nested Queries
- A **nested query** is a complete SELECT-FROM-WHERE block **within another SQL query**.
- The outer query uses the results of the nested query for comparisons.
- Nested queries can appear in:
  - WHERE clause
  - FROM clause
  - SELECT clause
  - Other SQL clauses
- SQL provides:
  - **IN** operator → checks if a value is present in a set (or multiset).
  - **NOT IN** operator → checks if a value is absent from a set.

---

# Example 1 – Projects with Employee Smith
Retrieve project numbers of projects that have an employee with last name ‘Smith’, either as a manager or as a worker.
```sql
SELECT DISTINCT Pnumber
FROM PROJECT
WHERE Pnumber IN (
    SELECT Pnumber
    FROM PROJECT, DEPARTMENT, EMPLOYEE
    WHERE Dnum = Dnumber AND Mgr_ssn = Ssn AND Lname = 'Smith')

OR Pnumber IN (
    SELECT Pno
    FROM WORKS_ON, EMPLOYEE
    WHERE Essn = Ssn AND Lname = 'Smith');
```
- First subquery finds projects managed by employees with last name Smith.
- Second subquery finds projects worked on by employees named Smith.
- Combined with outer query using `IN`.

---

# Scalar vs Set Results
- A nested query usually returns a **set (or multiset) of tuples**.
- But sometimes it returns a single value (scalar).
  - Example: using aggregate functions or conditions that return only one tuple.
- In such cases:
  - Use `=` instead of `IN`.
  - Tuples can also be compared using parentheses.

---

# Example 2 – Employee John Smith’s Projects
Retrieve ssn of employees who work on any (project, hours) combination that employee John Smith (ssn = 123456789) works on.
```sql
SELECT DISTINCT Essn
FROM WORKS_ON
WHERE (Pno, Hours) IN (
    SELECT Pno, Hours
    FROM WORKS_ON
    WHERE Essn = '123456789');
```
- `IN` compares tuples `(Pno, Hours)` from WORKS_ON with results of nested query.

---

# Additional Set Comparison Operators
- Operators of the form: `comp_op [ANY | SOME | ALL]`
- `comp_op` = comparison operator (`=`, `<>`, `>`, `>=`, `<`, `<=`).

### Behavior
- `> ALL` → True if value is greater than **all values** in the set.
- `> SOME` (or `> ANY`) → True if value is greater than **at least one value** in the set.
- `= SOME` ≡ `IN`
- `<> ALL` ≡ `NOT IN`

---

# Example 3 – Salary Greater than All in Dept 5
Retrieve employees whose salary is greater than salary of all employees in department 5.
```sql
SELECT Lname, Fname
FROM EMPLOYEE
WHERE Salary > ALL (
    SELECT Salary
    FROM EMPLOYEE
    WHERE Dno = 5);
```
- First, nested query gets salaries of dept 5 employees.
- Outer query compares each salary with all values.
- Equivalent result can be obtained using MAX.

---

# Example 4 – Salary Greater than Some in Dept 5
Retrieve employees whose salary is greater than salary of at least one employee in department 5.
```sql
SELECT Lname, Fname
FROM EMPLOYEE
WHERE Salary > SOME (
    SELECT Salary
    FROM EMPLOYEE
    WHERE Dno = 5);
```
- Condition satisfied if employee’s salary > any single value in the set.
- More employees satisfy this compared to > ALL.
- Equivalent result can be achieved using MIN.

---

# Levels of Nesting
- Queries can have **multiple levels of nesting**.
- Ambiguity arises if attribute names overlap in outer and inner queries.
- Rule: an unqualified attribute belongs to the relation in the **innermost query**.

### Example – Projects by Smith
Retrieve project numbers of projects managed by or worked on by Smith.
- Attributes like `Pnumber` and `Dnum` inside nested queries refer to inner query’s relations, not outer query’s.

---

# Avoiding Ambiguity with Aliases
- Use tuple variables (aliases) for clarity.
- Example: Retrieve employees who have a dependent with the same name and same sex.
```sql
SELECT E.Fname, E.Lname
FROM EMPLOYEE E
WHERE EXISTS (
    SELECT *
    FROM DEPENDENT D
    WHERE E.Ssn = D.Essn AND E.Sex = D.Sex AND E.Fname = D.Dependent_name);
```
- Here:
  - `E` = alias for EMPLOYEE (outer query).
  - `D` = alias for DEPENDENT (inner query).
  - Qualification removes ambiguity.

---

# Key Takeaways
- Nested queries allow complex conditions and set membership tests.
- IN, NOT IN check membership.
- ANY, SOME, ALL provide flexible set comparisons.
- Nesting levels may cause attribute ambiguity → use aliases.
- Equivalent results often possible using aggregate functions.

---

**End of Lecture 22 Notes – Complete Extract of Slides**

