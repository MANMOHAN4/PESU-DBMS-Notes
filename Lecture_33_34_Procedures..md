# Lecture 33 & 34 – Stored Procedures

---

# Introduction to Stored Procedures

- A **stored procedure** is a collection of pre-compiled SQL statements stored in the database.
- Acts like a subroutine/subprogram in regular programming.
- Contains:
  - Name
  - Parameter list
  - SQL statements
- Can be invoked by:
  - Triggers
  - Other procedures
  - External applications (Java, Python, PHP, etc.).

### Syntax (MySQL)

```sql
DELIMITER &&
CREATE PROCEDURE procedure_name [[IN | OUT | INOUT] parameter_name datatype,
                                  [...]]
BEGIN
   -- Declaration section
   -- Executable section
END &&
DELIMITER ;
```

---

# Use Cases of Stored Procedures

- Shared logic: procedures used by multiple applications.
- Reduces duplication and improves modularity.
- Executed at the server → reduces client-server communication cost.
- Enhance modeling beyond views.
- Enforce complex constraints not possible with assertions/triggers.

---

# Modes of Parameters

1. **IN**

   - Default mode.
   - Takes input from calling program.
   - Value is protected (cannot be changed inside procedure).

2. **OUT**

   - Used to return a value.
   - Initial value not accessible; only modified value is returned.

3. **INOUT**
   - Combination of IN and OUT.
   - Caller passes a value; procedure modifies and returns it.

### Calling Syntax

```sql
CALL procedure_name(parameter_list);
```

---

# Examples

## Example 1 – Procedure without Parameters

Display students with marks > 70 and count total students.

```sql
DELIMITER $$
CREATE PROCEDURE get_merit_student()
BEGIN
   SELECT * FROM student WHERE marks > 70;
   SELECT COUNT(student_code) AS Total_Student FROM student;
END $$
DELIMITER ;
```

**Query:**

```sql
CALL get_merit_student();
```

---

## Example 2 – Procedure with IN Parameter

Accepts a number and fetches limited rows.

```sql
DELIMITER &&
CREATE PROCEDURE get_student(IN var1 INT)
BEGIN
   SELECT * FROM student_info LIMIT var1;
   SELECT COUNT(stud_code) AS Total_Student FROM student_info;
END &&
DELIMITER ;
```

**Queries:**

```sql
CALL get_student(3);
CALL get_student(5);
```

---

## Example 3 – Procedure with OUT Parameter

Returns maximum marks from student table.

```sql
DELIMITER &&
CREATE PROCEDURE display_max_mark(OUT highestmark INT)
BEGIN
   SELECT MAX(marks) INTO highestmark FROM student;
END &&
DELIMITER ;
```

**Query:**

```sql
CALL display_max_mark(@output);
SELECT @output;
```

---

## Example 4 – Procedure with INOUT Parameter

Fetch marks for given student ID.

```sql
DELIMITER &&
CREATE PROCEDURE display_marks(INOUT var1 INT)
BEGIN
   SELECT marks INTO var1 FROM student WHERE stud_id = var1;
END &&
DELIMITER ;
```

**Queries:**

```sql
SET @M = 3;
CALL display_marks(@M);
SELECT @M;

SET @M = 4;
CALL display_marks(@M);
SELECT @M;
```

---

# Deleting a Procedure

Remove a stored procedure from DB.

```sql
DROP PROCEDURE [IF EXISTS] procedure_name;
```

**Example:**

```sql
DROP PROCEDURE display_marks;
```

Verify:

```sql
SHOW PROCEDURE STATUS WHERE db = 'mystudentdb';
```

---

# Example – Procedure with WHILE and IF-ELSE

Check student pass/fail.

```sql
CREATE PROCEDURE StudentMarksWithLoop()
BEGIN
   DECLARE done INT DEFAULT 0;
   DECLARE marks INT;
   DECLARE student_name VARCHAR(25);

   WHILE done = 0 DO
       SELECT marks, student_name INTO marks, student_name
       FROM student
       WHERE done = 0
       LIMIT 1;

       IF marks >= 50 THEN
           SELECT CONCAT(student_name, ' passed') AS Result;
       ELSE
           SELECT CONCAT(student_name, ' failed') AS Result;
       END IF;

       SET done = 1;
   END WHILE;
END //
DELIMITER ;
```

- Uses WHILE loop with condition flag.
- Fetches student data, checks pass/fail, exits after processing.

---

# Stored Procedure vs Function

- **Stored Procedure**:

  - May or may not return values.
  - Supports IN, OUT, INOUT parameters.
  - Invoked using `CALL`.

- **Function**:
  - Always returns a single value.
  - Called from SELECT or SQL statements.
  - Cannot use OUT/INOUT.

---

# Summary

- Stored procedures encapsulate reusable SQL logic.
- Parameter modes: IN, OUT, INOUT.
- Can enforce rules, return results, or automate tasks.
- Procedures can include control structures (loops, conditions).
- Differences from functions: procedures may not return values, invoked differently.

---

**End of Lecture 33 & 34 Notes – Complete Extract of Slides**
