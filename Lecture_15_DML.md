# Lecture_15_SQL_DML_Commands

### Prepared by: Prof. Mahitha G

*Department of Computer Science and Engineering*  
Acknowledgments: Dr. Nagasundari S (Dept of CSE), Teaching Assistants

---

# Database Modification

## Introduction
- DML (Data Manipulation Language) commands in SQL modify the data stored in the database.
- They allow us to **access, insert, update, and delete records**.

### Main DML Commands
- `SELECT`
- `INSERT`
- `UPDATE`
- `DELETE`

---

# INSERT Command

## Basics
- Adds one or more tuples (rows) to a relation (table).
- Two forms:
  1. Insert values in the same order as attributes in the table definition.
  2. Insert values for specific attributes.

### Form 1: Insert All Attributes
```sql
INSERT INTO table_name VALUES (value1, value2, ...);
```

**Example:**
```sql
INSERT INTO EMPLOYEE 
VALUES ('Richard', 'K', 'Marini', '653298653', '1962-12-30', 
        '98 Oak Forest, Katy, TX', 'M', 37000, '653298653', 4);
```

### Form 2: Insert Specific Attributes
```sql
INSERT INTO table_name (attribute1, attribute2, ...) 
VALUES (value1, value2, ...);
```

**Example:**
```sql
INSERT INTO EMPLOYEE (Fname, Lname, Dno, Ssn)
VALUES ('Richard', 'Marini', 4, '653298653');
```

### Insert from Query Results
- Insert multiple tuples using `SELECT`.

**Example 1:** Create and populate a table from existing relations.
```sql
CREATE TABLE WORKS_ON_INFO (
    Emp_name VARCHAR(15),
    Proj_name VARCHAR(15),
    Hours_per_week DECIMAL(3,1)
);

INSERT INTO WORKS_ON_INFO (Emp_name, Proj_name, Hours_per_week)
SELECT E.Lname, P.Pname, W.Hours
FROM PROJECT P, WORKS_ON W, EMPLOYEE E
WHERE P.Pnumber = W.Pno AND W.Essn = E.Ssn;
```

**Example 2:** Backup table contents.
```sql
INSERT INTO WORKS_ON_INFO_Backup
SELECT * FROM WORKS_ON_INFO;
```
*(Backup table must exist with the same schema.)*

---

# SELECT Command

- Retrieves data from one or more tables.
- Corresponds to the **projection** operation in relational algebra.

### Syntax
```sql
SELECT column1, column2, ...
FROM table_name;
```

**Example:**
```sql
SELECT Fname, Lname
FROM EMPLOYEE;
```

> Note: SELECT will be discussed in detail in the next lecture.

---

# UPDATE Command

- Modifies attribute values of selected tuples.
- A `WHERE` clause selects tuples to update.
- Updates may propagate if referential actions are specified.

### Syntax
```sql
UPDATE table_name
SET column_name = value
WHERE condition;
```

### Example 1: Update Project Details
```sql
UPDATE PROJECT
SET Plocation = 'Bellaire', Dnum = 5
WHERE Pnumber = 10;
```

### Example 2: Salary Raise by Department
```sql
UPDATE EMPLOYEE
SET Salary = Salary * 1.1
WHERE Dno = 5;
```

### Example 3: Salary Raise for Below-Average Employees
```sql
-- MySQL
UPDATE employee
SET Salary = Salary * 1.05
WHERE salary < (
    SELECT avg_sal FROM (SELECT AVG(salary) AS avg_sal FROM employee) AS temp
);

-- PostgreSQL / Oracle / SQL Server
UPDATE employee
SET Salary = Salary * 1.05
WHERE salary < (SELECT AVG(salary) FROM employee);
```

---

# DELETE Command

- Removes tuples from a table.
- Uses a `WHERE` clause to select tuples to delete.
- Deletion may propagate to other tables if referential actions are defined.

### Syntax
```sql
DELETE FROM table_name
WHERE condition;
```

### Examples
1. Delete employee with last name Zelaya:
```sql
DELETE FROM EMPLOYEE WHERE Lname = 'Zelaya';
```

2. Delete all employees:
```sql
DELETE FROM EMPLOYEE;
```
*(Similar to TRUNCATE, but table remains in schema.)*

---

# Quick Revision Q&A

1. Which SQL clause specifies attributes to display in output?
   - **Answer:** SELECT

2. Which DML operation removes data from a table?
   - **Answer:** DELETE

3. In `SELECT name FROM instructor WHERE dept_name = 'History'`, what is `name`?
   - **Answer:** A column in the instructor table

4. What is the purpose of DML?
   - **Answer:** Querying and modifying data in the database

---

# Summary
- DML includes `INSERT`, `SELECT`, `UPDATE`, `DELETE`.
- `INSERT`: Add tuples (all attributes, specific attributes, or from queries).
- `SELECT`: Retrieve tuples (covered in detail later).
- `UPDATE`: Modify tuples based on conditions.
- `DELETE`: Remove tuples, with or without conditions.

---

**End of Lecture 15 Notes – Complete Extract of Slides**

