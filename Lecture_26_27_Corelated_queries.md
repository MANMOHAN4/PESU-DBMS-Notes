# Lecture 26 & 27 – Correlated Nested Queries

---

# Introduction to Correlated Nested Queries

- A **correlated nested query** is a nested query where the **WHERE clause references attributes** from the outer query.
- Unlike regular nested queries, correlated nested queries are evaluated **once for each tuple (or tuple combination)** in the outer query.

### Example

Retrieve the name of each employee who has a dependent of the same gender.

```sql
SELECT E.Fname, E.Lname
FROM EMPLOYEE AS E
WHERE E.Ssn IN (
    SELECT D.Essn
    FROM DEPENDENT AS D
    WHERE E.Gender = D.Gender);
```

**How it works:**

- For each EMPLOYEE tuple, the nested query retrieves Essn of DEPENDENT tuples with the same gender.
- If the employee’s Ssn matches, the tuple is selected.

Equivalent single-block query:

```sql
SELECT E.Fname, E.Lname
FROM EMPLOYEE AS E, DEPENDENT AS D
WHERE E.Ssn = D.Essn AND E.Gender = D.Gender;
```

---

# EXISTS and UNIQUE Operators

- **EXISTS**: Boolean function, true if nested query returns at least one tuple.
- **NOT EXISTS**: True if nested query returns no tuples.
- **UNIQUE**: True if no duplicate tuples are produced by the nested query.
- EXISTS/NOT EXISTS are typically used with correlated queries.

---

# Example – EXISTS

Retrieve employees with a dependent of the same gender.

```sql
SELECT E.Fname, E.Lname
FROM EMPLOYEE AS E
WHERE EXISTS (
    SELECT *
    FROM DEPENDENT AS D
    WHERE E.Ssn = D.Essn AND E.Gender = D.Gender);
```

**Explanation:**

- For each EMPLOYEE tuple, nested query retrieves DEPENDENT tuples with matching Essn and Gender.
- If at least one exists → employee is selected.

---

# Example – NOT EXISTS

Retrieve employees with **no dependents**.

```sql
SELECT Fname, Lname
FROM EMPLOYEE
WHERE NOT EXISTS (
    SELECT *
    FROM DEPENDENT
    WHERE Ssn = Essn);
```

**Explanation:**

- For each EMPLOYEE tuple, nested query selects DEPENDENT tuples with matching Essn.
- If result is empty → employee selected.

---

# Example – Managers with Dependents

Retrieve names of managers who have at least one dependent.

```sql
SELECT Fname, Lname
FROM EMPLOYEE
WHERE EXISTS (
    SELECT * FROM DEPENDENT
    WHERE Ssn = Essn)
  AND EXISTS (
    SELECT * FROM DEPARTMENT
    WHERE Ssn = Mgr_ssn);
```

**Explanation:**

- First subquery: checks employee has dependents.
- Second subquery: checks employee manages a department.
- If both true → employee selected.

---

# Example – Works on All Projects (NOT EXISTS)

Retrieve employees who work on **all projects controlled by department 4**.

```sql
SELECT Fname, Lname
FROM EMPLOYEE
WHERE NOT EXISTS (
    (SELECT Pnumber
     FROM PROJECT
     WHERE Dnum = 4)
    EXCEPT
    (SELECT Pno
     FROM WORKS_ON
     WHERE Ssn = Essn));
```

### How it works

1. `(SELECT Pnumber FROM PROJECT WHERE Dnum = 4)` → all projects under dept 4.
2. `(SELECT Pno FROM WORKS_ON WHERE Ssn = Essn)` → projects an employee works on.
3. Set difference = projects under dept 4 that employee does not work on.
4. If empty → employee works on all projects → selected.

---

# Alternative Query – Nested NOT EXISTS

```sql
SELECT Fname, Lname
FROM EMPLOYEE
WHERE NOT EXISTS (
    SELECT *
    FROM WORKS_ON B
    WHERE B.Pno IN (SELECT Pnumber
                    FROM PROJECT
                    WHERE Dnum = 4)
      AND NOT EXISTS (
          SELECT *
          FROM WORKS_ON C
          WHERE C.Essn = Ssn
            AND C.Pno = B.Pno));
```

### How it works

- Outer query: checks each EMPLOYEE.
- Outermost nested query (B): selects projects under dept 4.
- Innermost nested query (C): checks if employee works on project B.Pno.
- If employee works on all dept 4 projects → outer NOT EXISTS condition satisfied.

---

# Step-by-Step Evaluation

- Outermost nested query evaluated for each EMPLOYEE.
- One inner query (projects under dept 4) → not correlated.
- Another inner query (projects employee works on) → correlated.
- Final logic:
  - If no unmatched project exists → employee works on all dept 4 projects.
  - Condition satisfied → employee selected.

---

# Key Takeaways

- Correlated queries are executed per tuple of outer query.
- EXISTS/NOT EXISTS used to test presence/absence of tuples.
- Queries can often be rewritten as single-block queries.
- NOT EXISTS useful for universal quantification (works on all projects).

---

**End of Lecture 26 & 27 Notes – Complete Extract of Slides**
