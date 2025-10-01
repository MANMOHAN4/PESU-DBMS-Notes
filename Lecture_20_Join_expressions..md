# Lecture 20 – Join Expressions

---

# Introduction to Join Expressions

- Join operations take two relations and return a new relation.
- A join is essentially a **Cartesian product** with a matching condition.
- It specifies attributes that appear in the result.
- Joins are commonly used as **subquery expressions** in the FROM clause.

### Types of Joins

1. Natural Join
2. Inner Join
3. Outer Join
4. Cross Join

---

# Natural Join

- Creates an **implicit join** by combining tables based on columns with the same name and data type.
- In a NATURAL JOIN between two relations R and S, no join condition is explicitly given.
- SQL automatically applies an **EQUIJOIN condition** for each pair of attributes with the same name from R and S.
- Each such pair appears only once in the result.
- If attribute names differ, they can be renamed before applying NATURAL JOIN.

### Key Points

- No need to specify column names.
- The result contains unique columns.
- NATURAL JOIN can be applied to more than two tables.
- Do **not** use the ON clause with NATURAL JOIN.

### Syntax

```sql
SELECT [column_names | *]
FROM table_name1
NATURAL JOIN table_name2;
```

### Example 1

List the employees along with their department names.

```sql
SELECT employee_name, employee_salary, Department_name
FROM employee NATURAL JOIN department;
```

### Example 2

List the customers along with their loan amount and address.

```sql
SELECT CustName, CustID, amount, mobile, address
FROM Borrower NATURAL JOIN Loan NATURAL JOIN cust_info;
```

---

# Inner Join

- SQL **Inner Join** combines records from two related tables based on a condition (join predicate).
- Compares each row of the first table with each row of the second to find matching pairs.
- When the join-predicate is satisfied, values from both tables are combined into a result row.
- Inner Join is the **default join** type. Using just `JOIN` is equivalent to `INNER JOIN`.
- Most frequently used join in SQL, also known as **Equijoin**.

### Syntax

```sql
SELECT columns
FROM table1 INNER JOIN table2
ON table1.col = table2.col;
```

### Example 1

Perform inner join between employee and department tables.

```sql
SELECT e.Employee_ID, e.Employee_Name, e.Employee_Salary,
       d.DepartmentID, d.Department_Name
FROM employee e INNER JOIN department d
ON e.Employee_ID = d.Employee_ID;
```

### Example 2

Perform inner join between loan and borrower tables.

```sql
SELECT l.Loan_ID, l.Branch, l.Amount,
       b.CustID, b.CustName
FROM Loan l JOIN Borrower b
ON l.Loan_ID = b.Loan_ID;
```

---

# Summary

- Join operations combine tuples from multiple relations.
- NATURAL JOIN automatically matches attributes with the same name.
- INNER JOIN (Equijoin) is the most common join, requiring an explicit condition.
- Joins are fundamental in relational queries and often used in FROM subqueries.

---

**End of Lecture 20 Notes – Complete Extract of Slides**
