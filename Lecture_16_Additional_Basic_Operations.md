# Lecture_16_SQL_Additional_Basic_Operations

### Prepared by: Prof. Mahitha G

*Department of Computer Science and Engineering*  
Acknowledgments: Dr. Nagasundari S (Dept of CSE), Teaching Assistants

---

# Additional Basic Operations in SQL

## Covered Topics
- SELECT clause
- DISTINCT clause
- WHERE clause
- FROM clause
- ORDER BY clause
- HAVING clause
- GROUP BY clause
- String operations

---

# SELECT-FROM-WHERE Structure

### SELECT & FROM Clause
- The **SELECT** clause lists the attributes desired in the result of a query.
- It corresponds to the **projection operation** of relational algebra.
- The **FROM** clause specifies the table(s) or relation(s).

**Example:** Find the names of all employees.
```sql
SELECT Fname, Lname
FROM EMPLOYEE;
```

**Note:** SQL names are case-insensitive. `Name ≡ NAME ≡ name`. Many people use uppercase for clarity.

---

# DISTINCT Clause

- SQL allows duplicates in relations and query results.
- To eliminate duplicates, use the keyword `DISTINCT` after `SELECT`.

**Example:** Retrieve the name and address of all employees (unique names only).
```sql
SELECT DISTINCT Fname, Lname, Address
FROM EMPLOYEE;
```

---

# SELECT Clause with Expressions

- The SELECT clause can include arithmetic expressions (`+`, `-`, `*`, `/`).

**Example:**
```sql
SELECT Fname, Salary/2
FROM EMPLOYEE;
```
- Returns same relation as EMPLOYEE, but divides salary by 2.

- Can rename expressions using `AS`:
```sql
SELECT Fname, Salary/2 AS monthly_salary
FROM EMPLOYEE;
```

---

# WHERE Clause

- Specifies conditions that the result must satisfy (acts as a row filter).
- Corresponds to the **selection predicate** of relational algebra.

**Example 1:** Find all employees in department no. 5.
```sql
SELECT Fname, Dno
FROM EMPLOYEE
WHERE Dno = 5;
```

**Example 2:** Find all employees in department 5 with salary < 70,000.
```sql
SELECT Fname, Salary
FROM EMPLOYEE
WHERE Dno = 5 AND Salary < 70000;
```

- SQL supports logical connectives **AND**, **OR**, **NOT**.
- Comparisons allowed: `<`, `<=`, `>`, `>=`, `=`, `<>`.
- Comparisons can involve arithmetic expressions.

---

# Example with Multiple Tables

**Query:** For every project located in 'Stafford', list:
- Project number
- Controlling department number
- Department manager’s last name, address, and birth date

```sql
SELECT Pnumber, Dnum, Lname, Address, Bdate
FROM PROJECT, DEPARTMENT, EMPLOYEE
WHERE Dnum = Dnumber 
  AND Mgr_ssn = Ssn
  AND Plocation = 'Stafford';
```

---

# ORDER BY Clause

- Used to **sort results** in ascending (ASC) or descending (DESC) order.
- Works with aggregate functions, HAVING, and GROUP BY.
- Default = ascending order.

**Example 1:** List employees by descending salary.
```sql
SELECT Fname, Lname, Salary
FROM EMPLOYEE
ORDER BY Salary DESC;
```

**Example 2:** List employees by ascending salary.
```sql
SELECT Fname, Lname, Salary
FROM EMPLOYEE
ORDER BY Salary ASC;
```

---

# RENAME Using AS

- SQL allows renaming relations and attributes with `AS`.
- Syntax: `old_name AS new_name`

**Example:** Find annual salary of employees.
```sql
SELECT Fname, Lname, Salary*12 AS Annual_Income
FROM EMPLOYEE;
```

**Note:** `AS` is optional. Example: `instructor AS T` ≡ `instructor T`.

---

# String Comparison in SQL

- SQL uses the operator **LIKE** for pattern matching.
- Special characters:
  - `%` → Matches any substring
  - `_` → Matches any single character

**Example 1:** Retrieve employees whose address is in Houston, TX.
```sql
SELECT Fname, Lname
FROM EMPLOYEE
WHERE Address LIKE '%Houston,TX%';
```

**Note:** To match literal `%`, escape with `\\`.
```sql
SELECT *
FROM table
WHERE col LIKE '100\%' ESCAPE '\\';
```

**Example 2:** Find employees born during the 1960s.
```sql
SELECT Fname, Lname, Bdate
FROM EMPLOYEE
WHERE Bdate LIKE '196_______';
```

---

# Quick Revision Q&A

1. **Which SQL clause removes duplicates?**  
   - DISTINCT

2. **Which operator is used for string matching?**  
   - LIKE

3. **Which SQL clause sorts query output?**  
   - ORDER BY

4. **Default sorting order in SQL?**  
   - ASC (Ascending)

5. **Which clauses are mandatory in SELECT queries?**  
   - SELECT and FROM

---

# Summary
- SELECT specifies attributes to project; FROM specifies relation(s).
- DISTINCT removes duplicates.
- WHERE filters rows with conditions.
- ORDER BY sorts results (ASC/DESC).
- AS renames attributes/relations.
- LIKE supports string comparison with `%` and `_` wildcards.

---

**End of Lecture 16 Notes – Complete Extract of Slides**

