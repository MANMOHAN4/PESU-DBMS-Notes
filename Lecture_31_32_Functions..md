# Lecture 31 & 32 – Functions in SQL

### Prepared by: Dr. Nagasundari S & Prof. Mahitha G

*Department of Computer Science and Engineering*  
Slides adapted from:
- “Database System Concepts” – Silberschatz, Korth, Sudarshan (McGrawHill, 7th Ed., 2019)
- “Fundamentals of Database Systems” – Elmasri, Navathe (Pearson, 7th Ed., 2017)

---

# Introduction to Functions in SQL
- SQL includes many built-in functions.
- Developers can also define their own functions and procedures, store them in the database, and invoke them from SQL.
- Functions and procedures allow **business logic** to be stored in the database and reused.
- Functions can be defined:
  - Using procedural SQL.
  - Using external languages (Java, C, C++).
- **User-defined functions (UDFs)**:
  - Accept parameters.
  - Perform actions.
  - Return results.

---

# Benefits of Functions
- Simplify development by encapsulating complex logic.
- Improve query readability.
- Facilitate reusability across multiple queries.
- Allow consistent execution of the same procedure across users.
- Useful for computations that appear repeatedly in queries.

---

# Function Basics
- A SQL function always:
  - Accepts parameters (single or multiple).
  - Returns a single value or a table.
- Functions help simplify code.

**Example use case:**
- Instead of repeating a complex formula in queries, encapsulate it in a function.

---

# Syntax of Function Creation
```sql
CREATE FUNCTION schema_name.function_name (parameter_list)  
RETURNS data_type AS  
BEGIN  
    statements  
    RETURN value  
END;
```

### Explanation
- **function_name**: Name of the function.
- **parameter_list**: Optional parameters declared in parentheses.
- A function may have zero, one, or many parameters.
- **BEGIN … END**: Defines body of the function.
- **DROP FUNCTION**: Used to delete a function.
```sql
DROP FUNCTION IF EXISTS function_name;
```

---

# Function Return Types
- The **RETURN** clause specifies the datatype of the output.
- Execution stops when `RETURN` is encountered.
- MySQL functions can be:
  - **Deterministic**: Always return the same value for the same input.
  - **Non-deterministic**: May return different results for the same input.
- By default, functions are **NON-DETERMINISTIC**.

---

# Example 1 – Department Size Function
```sql
CREATE FUNCTION Dept_size(deptno INT)
RETURNS VARCHAR(7)
BEGIN  
  DECLARE No_of_emps INT;  
  SELECT COUNT(*) INTO No_of_emps
  FROM EMPLOYEE
  WHERE Dno = deptno;  

  IF No_of_emps > 3 THEN    RETURN 'HUGE';
  ELSEIF No_of_emps > 2 THEN    RETURN 'LARGE';  
  ELSEIF No_of_emps > 1 THEN    RETURN 'MEDIUM';  
  ELSE    RETURN 'SMALL';  
  END IF;
END;
```

**Query:**
```sql
SELECT Dname, Dnumber, Dept_size(Dnumber)
FROM DEPARTMENT;
```

**Output:**
- Returns department name, number, and classification as HUGE/LARGE/MEDIUM/SMALL.

---

# Example 2 – Employee Age Function
```sql
CREATE FUNCTION no_of_years(date1 DATE)
RETURNS INTEGER DETERMINISTIC
BEGIN
  DECLARE date2 DATE;  
  SELECT current_date() INTO date2;  
  RETURN year(date2) - year(date1);
END;
```

**Query:**
```sql
SELECT Fname, no_of_years(Bdate)
FROM EMPLOYEE;
```

**Output:**
- Returns employee name and their age in years.

---

# Key Takeaways
- Functions encapsulate reusable logic.
- Improve query readability and consistency.
- Can return values based on input parameters.
- May be deterministic or non-deterministic.
- Syntax includes `CREATE FUNCTION`, parameters, return type, and `RETURN` statement.
- Functions can be dropped using `DROP FUNCTION`.

---

**End of Lecture 31 & 32 Notes – Complete Extract of Slides**

