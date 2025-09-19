# Lecture 23 – Nested Queries (Part 2)

### Prepared by: Dr. Nagasundari & Prof. Mahitha G

*Department of Computer Science and Engineering*  
Slides adapted from:
- “Database System Concepts” – Silberschatz, Korth, Sudarshan (McGrawHill, 7th Ed., 2019)
- “Fundamentals of Database Systems” – Elmasri, Navathe (Pearson, 7th Ed., 2017)

---

# Subqueries in the FROM Clause
- SQL allows a subquery expression to appear in the **FROM clause**.
- Since a subquery returns a relation as a result, it can be inserted anywhere a relation is valid.

**Example:** Find average employee salary of departments where the average salary is greater than $32,000.

### Using GROUP BY and HAVING
```sql
SELECT Dno, ROUND(AVG(Salary),2) AS avg_salary
FROM EMPLOYEE
GROUP BY Dno
HAVING AVG(Salary) > 32000;
```

**Note on ROUND:**
- `ROUND(X, D)` rounds `X` to `D` decimal places.
- If `D` is not given, rounds to nearest integer.

### Using Nested Subqueries
```sql
SELECT Dno, avg_salary
FROM (SELECT Dno, ROUND(AVG(Salary),2)
      FROM EMPLOYEE
      GROUP BY Dno)
      AS dept_avg_salary(Dno, avg_salary)
WHERE avg_salary > 32000;
```

### How it Works
1. Subquery:
```sql
SELECT Dno, ROUND(AVG(Salary),2)
FROM EMPLOYEE
GROUP BY Dno;
```
- Produces department numbers with average salaries (rounded to 2 decimals).

2. Aliasing:
```sql
AS dept_avg_salary(Dno, avg_salary)
```
- Names the result relation `dept_avg_salary` with attributes `Dno`, `avg_salary`.
- MySQL requires alias names; otherwise → Error 1248 (derived table must have alias).

3. Filtering:
```sql
WHERE avg_salary > 32000;
```
- Selects only departments with average salary > 32000.

### SQL Implementation Differences
- **MySQL/PostgreSQL**: require subquery alias names.
- **Oracle**: allows naming subquery relation but not renaming attributes.

---

# Subquery in FROM – Example
Find maximum of total employee salaries of each department.
```sql
SELECT MAX(tot_salary)
FROM (SELECT Dno, SUM(Salary)
      FROM EMPLOYEE
      GROUP BY Dno) AS dept_total(Dno, tot_salary);
```

---

# Common Table Expressions (CTE)
- A **CTE** is a temporary result set defined within the execution scope of a query.
- Defined using `WITH` clause.
- Can be referenced multiple times within the main query.

### Benefits
- Simplifies complex queries.
- Breaks down subqueries.
- Useful for recursive querying.

### Syntax
```sql
WITH cte_name AS (
    SELECT query1 ...
)
SELECT query2 ... FROM cte_name;
```
- `cte_name`: name of temporary relation.
- `query1`: subquery result treated as virtual table.
- `query2`: main query using CTE.

Multiple CTEs can be defined:
```sql
WITH cte1 AS (...), cte2 AS (...)
SELECT ... FROM cte1 JOIN cte2;
```

---

# CTE Examples

### Example 1 – Employees earning > 50,000
```sql
WITH cte_emp AS (
    SELECT ssn, fname, dno, salary
    FROM Employee
    WHERE salary > 50000
)
SELECT fname FROM cte_emp;
```

### Example 2 – Employees earning above average salary
```sql
WITH avg_salary AS (
    SELECT AVG(salary) AS avg_sal
    FROM Employee
)
SELECT fname, dno, salary
FROM Employee, avg_salary
WHERE Employee.salary > avg_salary.avg_sal;
```
Equivalent without WITH:
```sql
SELECT fname, dno, salary
FROM Employee
WHERE salary > (SELECT AVG(salary)
                FROM Employee);
```

### Example 3 – Employees > 40,000 with department names
Without CTE:
```sql
SELECT fname, salary, dname
FROM Employee JOIN Department
ON dno = dnumber
WHERE salary > 40000;
```
With CTE:
```sql
WITH cte_emp AS (
    SELECT ssn, fname, dno, salary
    FROM Employee
    WHERE salary > 40000
),
cte_dept AS (
    SELECT dnumber, dname
    FROM Department
)
SELECT e.fname, e.salary, d.dname
FROM cte_emp e JOIN cte_dept d
ON e.dno = d.dnumber;
```

---

# WITH Clause
- Defines a temporary relation available only in the query it appears.

### Example – Max hours worked
```sql
WITH max_work(max_hours) AS (
    SELECT MAX(Hours)
    FROM WORKS_ON
)
SELECT Essn, Pno, Hours
FROM WORKS_ON, max_work
WHERE Hours = max_hours;
```

**How it Works:**
- `max_work` stores maximum hours.
- Main query joins WORKS_ON with `max_work` to get tuples where Hours = max_hours.

Equivalent using nested query:
```sql
SELECT Essn, Pno, Hours
FROM WORKS_ON
WHERE Hours = (SELECT MAX(Hours)
               FROM WORKS_ON);
```

- WITH clause makes queries clearer and reusable.

---

# WITH Clause – Example
Find all departments where total salary > average total salary across departments.
```sql
WITH dept_total(Dno, tot_salary) AS (
    SELECT Dno, SUM(Salary)
    FROM EMPLOYEE
    GROUP BY Dno
),
dept_total_avg(avg_tot_salary) AS (
    SELECT AVG(tot_salary)
    FROM dept_total
)
SELECT Dname, Dnumber
FROM dept_total, dept_total_avg, DEPARTMENT
WHERE tot_salary > avg_tot_salary
  AND Dnumber = Dno;
```

**How it Works:**
1. `dept_total`: department numbers + total salary.
2. `dept_total_avg`: average of all department totals.
3. Final query: select departments where total salary > average total salary, joining with DEPARTMENT for names.

---

# Summary
- Subqueries can appear in the FROM clause as derived tables.
- MySQL/PostgreSQL require alias names; Oracle differs in handling.
- CTEs simplify queries using `WITH` clause.
- WITH allows temporary results used multiple times in a query.
- Enhances clarity and modularity of SQL queries.

---

**End of Lecture 23 Notes – Complete Extract of Slides**

