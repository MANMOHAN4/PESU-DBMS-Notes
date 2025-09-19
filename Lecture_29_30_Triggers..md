# Lecture 29 & 30 – Triggers

### Prepared by: Dr. Nagasundari S & Prof. Mahitha G

*Department of Computer Science and Engineering*  
Slides adapted from:
- “Database System Concepts” – Silberschatz, Korth, Sudarshan (McGrawHill, 7th Ed., 2019)
- “Fundamentals of Database Systems” – Elmasri, Navathe (Pearson, 7th Ed., 2017)

---

# Introduction to Triggers
- A **trigger** is a statement executed automatically as a side effect of a modification to the database.
- Defining a trigger involves:
  - **Event** – the modification (INSERT, UPDATE, DELETE) that causes the trigger to be checked.
  - **Condition** – must be satisfied for the trigger to execute.
  - **Action** – executed when the event occurs and condition holds.
- Once created, the DBMS automatically executes the trigger whenever the specified event occurs.

---

# Need for Triggers
- Enforce integrity constraints not easily defined with SQL constraints.
- Automatically perform tasks (alerts, calculations, data propagation).
- Example: When a student enrolls in a course (new tuple in `TAKES`), update the student’s total credits in `STUDENT`.
- Some systems allow triggers to send notifications (e.g., emails).
- Applications:
  - Maintain consistency
  - Monitor updates
  - Automatically update derived data

---

# Trigger Syntax
```sql
CREATE TRIGGER trigger_name    
(AFTER | BEFORE) (INSERT | UPDATE | DELETE) ON table_name
FOR EACH ROW    
BEGIN    
   -- variable declarations    
   -- trigger code    
END;
```

### Key Points
1. **CREATE TRIGGER trigger_name** → assigns a name.
2. **AFTER | BEFORE (INSERT | UPDATE | DELETE) ON table_name** → defines event and timing.
3. **FOR EACH ROW** → ensures trigger executes once per modified row.
4. **BEGIN … END** → specifies action, including variables, SQL statements, or stored procedures.

---

# Sample Database Schema
- **Student_sample(SRN, name)** – student info.
- **Marks_sample(SRN, Course, Marks)** – marks of students in courses.
- **Marks_history(SRN, Course, Marks)** – backup table to store deleted records from Marks_sample.

Initially, `Marks_history` is empty.

---

# Example 1 – Check Valid Marks
Trigger ensures marks inserted are within 0–100.
```sql
DELIMITER //
CREATE TRIGGER CheckMarks
BEFORE INSERT ON Marks_sample
FOR EACH ROW
BEGIN  
   IF NEW.marks < 0 OR NEW.marks > 100 THEN    
      SIGNAL SQLSTATE '45000'    
      SET MESSAGE_TEXT = 'Invalid marks: Marks must be between 0 and 100';  
   END IF;
END;//
DELIMITER ;
```

### How it Works
- Executed **before insertion**.
- For each row, checks if marks < 0 or > 100.
- If invalid, raises error and prevents insertion.
- If valid (or NULL), tuple inserted.

**Example:**
```sql
INSERT INTO Marks_sample VALUES (1, 'C03', 101);
```
Fails → marks > 100.

```sql
INSERT INTO Marks_sample VALUES (1, 'C03', NULL);
```
Succeeds → NULL allowed.

---

# Example 2 – Mimic ON UPDATE CASCADE
Trigger updates foreign key references in `Marks_sample` when SRN in `Student_sample` is updated.
```sql
DELIMITER //
CREATE TRIGGER update_marks_on_cascade
BEFORE UPDATE ON Student_sample
FOR EACH ROW
BEGIN
   SET FOREIGN_KEY_CHECKS = 0;
   UPDATE Marks_sample   
   SET SRN = NEW.SRN    
   WHERE SRN = OLD.SRN;
   SET FOREIGN_KEY_CHECKS = 1;
END;//
DELIMITER ;
```

### How it Works
- Executed before an update on `Student_sample`.
- Temporarily disables FK checks.
- Updates all matching SRN values in `Marks_sample`.
- Restores FK checks.

**Example:**
```sql
UPDATE Student_sample
SET SRN = 5
WHERE name = 'Harry';
```
- Updates `Harry`’s SRN in both `Student_sample` and `Marks_sample`.
- Without this trigger, update would fail due to FK violation.

---

# Example 3 – Backup Deleted Marks
Trigger copies deleted tuples into `Marks_history`.
```sql
DELIMITER //
CREATE TRIGGER backup_marks_info
BEFORE DELETE ON Marks_sample
FOR EACH ROW
BEGIN
   INSERT INTO Marks_history
   SELECT *
   FROM Marks_sample
   WHERE SRN = OLD.SRN AND COURSE = OLD.COURSE;
END //
DELIMITER ;
```

### How it Works
- Executed before deletion from `Marks_sample`.
- Copies the tuple being deleted into `Marks_history`.

**Example:**
```sql
DELETE FROM Marks_sample WHERE SRN = 2;
```
- Before deletion, no history.
- After deletion, deleted tuple stored in `Marks_history`.

---

# Key Takeaways
- Triggers enforce rules, automate updates, and maintain history.
- They can:
  - Validate input (CheckMarks)
  - Propagate updates (update_marks_on_cascade)
  - Preserve deleted data (backup_marks_info)
- Syntax allows specifying event (INSERT, UPDATE, DELETE), timing (BEFORE, AFTER), and row-level execution.

---

**End of Lecture 29 & 30 Notes – Complete Extract of Slides**

