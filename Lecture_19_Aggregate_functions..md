# Lecture 19 – Aggregate Functions

### Prepared by: Prof. Mahitha G

*Department of Computer Science and Engineering*  
Slides adapted from:  
- “Database System Concepts” (Silberschatz, Korth, Sudarshan, McGrawHill, 7th Ed., 2019)  
- “Fundamentals of Database Systems” (Elmasri, Navathe, Pearson, 7th Ed., 2017)

---

# Introduction
- **Aggregate functions** summarize information from multiple tuples into a single-tuple summary.
- **Grouping** is used to create subgroups of tuples before summarization.

### Built-in Aggregate Functions
- `COUNT` – number of tuples/values specified in a query
- `SUM` – sum of numeric values
- `MAX` – maximum value
- `MIN` – minimum value
- `AVG` – average value

> **Note:** A **multiset** is like a set but allows duplicate values (multiplicity ≥ 1).

---

# Aggregate Functions – Example
- `MAX` and `MIN` can also be applied to non-numeric domains if they have a total ordering (DATE, TIME, TIMESTAMP, strings).

**Example:** Find sum, max, min, and average salary.
```sql
SELECT SUM(Salary), MAX(Salary), MIN(Salary), AVG(Salary)
FROM EMPLOYEE;
```

---

# Using `AS` Clause
- `AS` renames columns to meaningful names.

**Example:**
```sql
SELECT SUM(Salary) AS Total_Sal,
       MAX(Salary) AS Highest_Sal,
       MIN(Salary) AS Lowest_Sal,
       AVG(Salary) AS Average_Sal
FROM EMPLOYEE;
```

---

# COUNT – Examples
**Example 1:** Total number of employees.
```sql
SELECT COUNT(*) FROM EMPLOYEE;
```

**Example 2:** Number of employees in Research department.
```sql
SELECT COUNT(*)
FROM EMPLOYEE, DEPARTMENT
WHERE DNO = DNUMBER AND DNAME = 'Research';
```

- `COUNT(*)` counts rows in the query result.

---

# Aggregate Functions with Conditions
**Example:** Sum, max, min, and avg salary of employees in Research department.
```sql
SELECT SUM(Salary), MAX(Salary), MIN(Salary), AVG(Salary)
FROM (EMPLOYEE JOIN DEPARTMENT ON Dno = Dnumber)
WHERE Dname = 'Research';
```

---

# Use of DISTINCT
- `COUNT` can be applied to specific attributes.

**Example 1:** Count distinct salaries.
```sql
SELECT COUNT(DISTINCT Salary)
FROM EMPLOYEE;
```

**Example 2:** Count all salaries (duplicates included).
```sql
SELECT COUNT(Salary)
FROM EMPLOYEE;
```

- By default, SQL does **not** eliminate duplicates.
- `DISTINCT` explicitly removes duplicates.
- `ALL` keyword can be used to explicitly retain duplicates.

---

# Handling of NULL Values
- Aggregate functions ignore NULL values.
- If all values are NULL:
  - `COUNT` returns 0
  - Other aggregates return NULL

**Example:** Average hours worked per project.
```sql
SELECT Pno, AVG(Hours) AS avg_hours
FROM WORKS_ON
GROUP BY Pno;
```

If Hours = (10, 15, NULL) → Average = (10 + 15) / 2 = 12.5

---

# Aggregate Functions in Nested Queries
- Aggregates can appear in selection conditions and correlated subqueries.

**Example:** Retrieve employees with 2+ dependents.
```sql
SELECT Lname, Fname
FROM EMPLOYEE
WHERE (SELECT COUNT(*)
       FROM DEPENDENT
       WHERE Ssn = Essn) >= 2;
```

- SQL also supports Boolean aggregates:
  - `SOME` – true if at least one element is true
  - `ALL` – true if all elements are true

---

# Grouping
- Aggregate functions can be applied to subgroups.
- **Grouping attributes**: attributes used to partition data.
- Each group = tuples with same value for grouping attribute(s).
- NULL grouping attributes → separate group.

**Example:** Average salary per department.
```sql
SELECT Dno, COUNT(*), AVG(Salary)
FROM EMPLOYEE
GROUP BY Dno;
```

---

# GROUP BY with Joins
**Example:** Employees per project.
```sql
SELECT Pnumber, Pname, COUNT(*)
FROM PROJECT, WORKS_ON
WHERE Pnumber = Pno
GROUP BY Pnumber, Pname;
```

---

# HAVING Clause
- Applies conditions on groups (WHERE applies on individual tuples).

**Example:** Projects with more than 2 employees.
```sql
SELECT Pnumber, Pname, COUNT(*)
FROM PROJECT, WORKS_ON
WHERE Pnumber = Pno
GROUP BY Pnumber, Pname
HAVING COUNT(*) > 2;
```

---

# WHERE vs HAVING
- WHERE filters tuples → groups are then formed.
- HAVING filters groups after aggregation.

**Example (incorrect):** Employees > 40,000 in depts with > 2 employees.
```sql
SELECT Dno, COUNT(*)
FROM EMPLOYEE
WHERE Salary > 40000
GROUP BY Dno
HAVING COUNT(*) > 2;
```
- This only finds departments with > 2 employees each earning > 40,000.

**Correct query:**
```sql
SELECT Dno, COUNT(*)
FROM EMPLOYEE
WHERE Salary > 40000 AND Dno IN (
   SELECT Dno
   FROM EMPLOYEE
   GROUP BY Dno
   HAVING COUNT(*) > 2)
GROUP BY Dno;
```

---

# Quick Revision Q&A

1. **Which aggregate functions are built-in?**  
   - COUNT, SUM, MAX, MIN, AVG

2. **Which clause filters individual tuples?**  
   - WHERE

3. **Which clause filters groups?**  
   - HAVING

4. **Do aggregate functions ignore NULLs?**  
   - Yes (except COUNT(*))

5. **How to eliminate duplicates in aggregates?**  
   - Use DISTINCT

---

# Summary
- Aggregates summarize multiple tuples.
- GROUP BY partitions relations into groups.
- HAVING filters groups.
- NULL values are discarded by aggregates (except COUNT). 
- DISTINCT eliminates duplicates in aggregation.

---

**End of Lecture 19 Notes – Complete Extract of Slides**