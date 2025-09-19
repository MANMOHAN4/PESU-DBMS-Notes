# Lecture 24 & 25 – Common Table Expressions (Recursive) and CASE Statement

### Prepared by: Prof. Mahitha G

*Department of Computer Science and Engineering*  
Acknowledgment: Teaching Assistants  
Slides adapted from:
- “Database System Concepts” – Silberschatz, Korth, Sudarshan (McGrawHill, 7th Ed., 2019)
- “Fundamentals of Database Systems” – Elmasri, Navathe (Pearson, 7th Ed., 2017)

---

# Common Table Expressions (CTE) – Recursive CTE

## Introduction
- A **recursive CTE** references itself within the CTE.
- Useful for working with **hierarchical data** (e.g., employee–supervisor relationships).
- Continues executing until the query returns the entire hierarchy.
- Implemented in SQL using the `WITH RECURSIVE` clause.

---

## General Syntax
```sql
WITH RECURSIVE cte_name (column_list) AS (
  -- Anchor member: base case
  SELECT ...
  FROM ...
  WHERE ...

  UNION ALL

  -- Recursive member: refers to the CTE itself
  SELECT ...
  FROM cte_name
  WHERE ...
)
-- Final SELECT statement outside the CTE
SELECT ... FROM cte_name;
```

---

## Explanation of Syntax Components
- **cte_name**: Name of the CTE (can be referenced inside itself and final query).
- **column_list**: Defines the result set columns.
- **Anchor member**: Initial query, selects base rows. Must not reference the CTE.
- **UNION ALL**: Combines anchor member results with recursive member results. `UNION ALL` preserves duplicates (important for recursion).
- **Recursive member**: Refers back to the CTE, extends results iteratively, must include a termination condition.
- **Final SELECT**: Retrieves results from the recursive CTE.

---

## Example 1 – Countdown from 5 to 1
```sql
WITH RECURSIVE Countdown (n) AS (
  SELECT 5
  UNION ALL
  SELECT n - 1
  FROM Countdown
  WHERE n > 1
)
SELECT n FROM Countdown;
```

**Output:** 5, 4, 3, 2, 1

---

## Example 2 – Count up from 1 to 5
```sql
WITH RECURSIVE cte(n) AS (
  SELECT 1
  UNION ALL
  SELECT n + 1
  FROM cte
  WHERE n < 5
)
SELECT * FROM cte;
```

**Output:** 1, 2, 3, 4, 5

---

## Execution Flow of Recursive CTE
1. **Anchor member** executes → produces base row(s).
   - Example: `SELECT 1` → returns `1`.
2. **Recursive member** executes with result of anchor.
   - Example: `SELECT n+1 FROM cte WHERE n < 5` → generates `2`.
3. Process repeats until condition fails.

---

## Example 3 – Organizational Hierarchy
Extract the hierarchy of employees starting with CEO (no supervisor).
```sql
WITH RECURSIVE orghierarchy AS (
  SELECT ssn, fname, lname, super_ssn, 1 AS level
  FROM employee
  WHERE super_ssn IS NULL

  UNION ALL

  SELECT e.ssn, e.fname, e.lname, e.super_ssn, OH.level + 1
  FROM employee e
  JOIN orghierarchy OH ON e.super_ssn = OH.ssn
)
SELECT * FROM orghierarchy;
```

**Output:** Displays each employee with their level in the hierarchy.

---

# CASE Statement in SQL

## Introduction
- **CASE** is a conditional expression in SQL.
- Enables conditional logic directly inside queries.
- Returns specific results based on conditions.
- If no condition is true → ELSE is executed. If no ELSE → returns NULL.

---

## General Syntax
```sql
CASE case_value
  WHEN condition THEN result1
  WHEN condition THEN result2
  ...
  ELSE result
END
```

---

## Example 1 – Salary Levels
Categorize employees into High, Medium, Low.
```sql
SELECT Fname, Salary,
  CASE
    WHEN Salary >= 55000 THEN 'High'
    WHEN Salary >= 40000 THEN 'Medium'
    ELSE 'Low'
  END AS Salary_Level
FROM employee;
```

---

## Example 2 – Bonus Status
```sql
SELECT Fname, Salary,
  CASE
    WHEN Salary >= 50000 THEN 'Eligible for 20% Bonus'
    WHEN Salary >= 40000 THEN 'Eligible for 10% Bonus'
    ELSE 'No Bonus'
  END AS Bonus_Status
FROM employee;
```

---

## Example 3 – Workload Status
Number of projects each employee works on.
```sql
SELECT e.Fname, COUNT(w.Pno) AS Project_Count,
  CASE
    WHEN COUNT(w.Pno) >= 3 THEN 'Overloaded'
    WHEN COUNT(w.Pno) = 2 THEN 'Normal Load'
    ELSE 'Light Load'
  END AS Workload_Status
FROM employee e
LEFT JOIN works_on w ON e.Ssn = w.Essn
GROUP BY e.Fname;
```

---

## Example 4 – Workload with CTE
Combining CTE with CASE for readability.
```sql
WITH Project_Counts AS (
  SELECT e.Fname, COUNT(w.Pno) AS Proj_Count
  FROM employee e
  LEFT JOIN works_on w ON e.Ssn = w.Essn
  GROUP BY e.Fname
)
SELECT Fname, Proj_Count,
  CASE
    WHEN Proj_Count >= 3 THEN 'Overloaded'
    WHEN Proj_Count = 2 THEN 'Normal Load'
    ELSE 'Light Load'
  END AS Workload_Status
FROM Project_Counts;
```

---

# Summary
- Recursive CTEs handle hierarchical/iterative data.
- `WITH RECURSIVE` defines anchor + recursive parts.
- Execution repeats until termination condition.
- CASE adds conditional logic to queries.
- Useful for categorization, custom outputs, workload/bonus classification.
- CASE can be combined with GROUP BY, JOIN, or CTE for advanced analysis.

---

**End of Lecture 24 & 25 Notes – Complete Extract of Slides**

