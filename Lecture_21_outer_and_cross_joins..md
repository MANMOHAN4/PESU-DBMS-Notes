# Lecture 21 – Outer and Cross Joins

---

# Outer Join

- An extension of join operation that avoids loss of information.
- Computes the join and adds tuples from one relation that do not match in the other relation.
- In SQL Outer Join:
  - All the content from both tables is integrated together.
  - Both matching and non-matching records from both tables are returned.

### Types of Outer Join

1. Left Outer Join
2. Right Outer Join
3. Full Outer Join

---

# Left Outer Join

- Every tuple in the **left table** must appear in the result.
- If no matching tuple exists in the right table, NULL values are padded for right table attributes.
- Returns:
  - All records from the left table.
  - Only matching records from the right table.

### Syntax

```sql
SELECT Table1.col1, Table2.col2
FROM Table1
LEFT OUTER JOIN Table2
ON Table1.colX = Table2.colX;
```

### Example 1

```sql
SELECT e.Employee_ID, e.Employee_Name, e.Employee_Salary,
       d.DepartmentID, d.Department_Name
FROM employee e
LEFT OUTER JOIN department d
ON e.Employee_ID = d.Employee_ID;
```

### Example 2

```sql
SELECT l.Loan_ID, l.Branch, l.Amount,
       b.CustID, b.CustName
FROM Loan l
LEFT OUTER JOIN Borrower b
ON l.Loan_ID = b.Loan_ID;
```

---

# Right Outer Join

- Every tuple in the **right table** must appear in the result.
- If no matching tuple exists in the left table, NULL values are padded for left table attributes.
- Right outer join is the reverse of left outer join.

### Syntax

```sql
SELECT Table1.col1, Table2.col2
FROM Table1
RIGHT OUTER JOIN Table2
ON Table1.colX = Table2.colX;
```

### Example 1

```sql
SELECT e.Employee_ID, e.Employee_Name, e.Employee_Salary,
       d.DepartmentID, d.Department_Name
FROM employee e
RIGHT OUTER JOIN department d
ON e.Employee_ID = d.Employee_ID;
```

### Example 2

```sql
SELECT l.Loan_ID, l.Branch, l.Amount,
       b.CustID, b.CustName
FROM Loan l
RIGHT OUTER JOIN Borrower b
ON l.Loan_ID = b.Loan_ID;
```

---

# Full Outer Join

- Returns all records from both left and right tables.
- MySQL does **not** directly support FULL OUTER JOIN.
- To implement in MySQL:
  - Perform `LEFT OUTER JOIN`
  - Perform `RIGHT OUTER JOIN`
  - Combine results using `UNION`

### Example 1

```sql
SELECT e.Employee_ID, e.Employee_Name, e.Employee_Salary,
       d.DepartmentID, d.Department_Name
FROM department d
LEFT OUTER JOIN employee e
ON e.Employee_ID = d.Employee_ID

UNION

SELECT e.Employee_ID, e.Employee_Name, e.Employee_Salary,
       d.DepartmentID, d.Department_Name
FROM department d
RIGHT OUTER JOIN employee e
ON e.Employee_ID = d.Employee_ID;
```

### Example 2

```sql
SELECT l.Loan_ID, l.Branch, l.Amount,
       b.CustID, b.CustName
FROM Loan l
LEFT OUTER JOIN Borrower b
ON l.Loan_ID = b.Loan_ID

UNION

SELECT l.Loan_ID, l.Branch, l.Amount,
       b.CustID, b.CustName
FROM Loan l
RIGHT OUTER JOIN Borrower b
ON l.Loan_ID = b.Loan_ID;
```

---

# Cross Join

- Returns the **Cartesian product** of two tables.
- No join condition (ON ...).
- Matches each row from first table with every row in the second.
- If table A has `m` rows and table B has `n` rows, result = `m * n` rows.

### Example

Loan table has 8 rows, Borrower table has 7 rows → Result = 8 \* 7 = 56 rows.

```sql
SELECT *
FROM Loan CROSS JOIN Borrower;
```

---

# Summary

- Outer Join includes matching and non-matching tuples:
  - Left Outer Join → All left table tuples + matching right.
  - Right Outer Join → All right table tuples + matching left.
  - Full Outer Join → All tuples from both tables.
- Cross Join returns the Cartesian product.

---

**End of Lecture 21 Notes – Complete Extract of Slides**
