# Lecture_14_SQL_Constraints_and_Alter_Commands

---

# Specifying Constraints in SQL

## Introduction

- Constraints in SQL are **conditions and restrictions** applied on the database.
- Before inserting data, the system validates constraints. Only valid data is accepted.
- Constraints can be applied:
  - During `CREATE TABLE`
  - After table creation using `ALTER TABLE`

---

## Basic Constraints

- **Key Constraint:** Primary key values cannot be duplicated.
- **Entity Integrity Constraint:** Primary key values cannot be NULL.
- **Referential Integrity Constraint:** A foreign key must match an existing primary key value or be NULL.

---

## Attribute Domain Restrictions

- **Default Value:** `DEFAULT <value>`
- **NOT NULL:** Attribute cannot store NULL values.
- **CHECK Clause:** Restricts values within a condition.

### Example

```sql
Dnumber INT NOT NULL CHECK (Dnumber > 0 AND Dnumber < 21);
```

---

## NOT NULL Constraint

- Ensures attribute values cannot be NULL.
- Implicit for primary keys.
- Can be applied to any attribute.

### Example

```sql
CREATE TABLE Employee (
    EmpID INT PRIMARY KEY,
    Name VARCHAR(50) NOT NULL
);
```

---

## DEFAULT Constraint

- Assigns a default value when none is provided.
- If unspecified, the default is `NULL` unless `NOT NULL` is defined.

### Example:

1. The column cannot accept `NULL`.
2. `DEFAULT 1` ensures that if you don’t provide a value, it will auto-fill with `1`.

```sql
CREATE TABLE Employee (
    EmpID INT PRIMARY KEY,
    Dno INT NOT NULL DEFAULT 1
);

INSERT INTO Employee (EmpID, Name) VALUES (103, 'Charlie');
-- Dno = 1 (default applied)

INSERT INTO Employee (EmpID, Name, Dno) VALUES (104, 'David', NULL);
-- ❌ Error: NULL not allowed

```

---

## PRIMARY KEY and UNIQUE

- **PRIMARY KEY:** Defines main unique identifier.
- **UNIQUE:** Defines alternate keys (candidate keys).

### Example

```sql
Dnumber INT PRIMARY KEY,
Dname VARCHAR(15) UNIQUE
```

---

## FOREIGN KEY Constraint

- Ensures referential integrity.
- Default: Reject update if violated.
- Options: `SET NULL`, `SET DEFAULT`, `CASCADE`.

### Example

```sql
FOREIGN KEY (Dno) REFERENCES Department(Dnumber)
    ON DELETE SET NULL
    ON UPDATE CASCADE;
```

---

## Using CONSTRAINT Keyword

- Names constraints for easy reference and modification.

### Example

```sql
CREATE TABLE Employee (
    Ssn INT,
    Dno INT DEFAULT 1 NOT NULL,
    CONSTRAINT EMPPK PRIMARY KEY (Ssn),
    CONSTRAINT EMPSUPERFK FOREIGN KEY (Super_ssn)
        REFERENCES Employee(Ssn)
        ON DELETE SET NULL ON UPDATE CASCADE,
    CONSTRAINT EMPDEPTFK FOREIGN KEY (Dno)
        REFERENCES Department(Dnumber)
        ON DELETE SET DEFAULT ON UPDATE CASCADE
);
```

---

## Example: DEPT_LOCATIONS

```sql
CREATE TABLE Dept_Locations (
    Dnumber INT NOT NULL,
    Dlocation VARCHAR(15) NOT NULL DEFAULT 'Bangalore',
    PRIMARY KEY (Dnumber, Dlocation),
    CONSTRAINT fk_dno FOREIGN KEY (Dnumber)
        REFERENCES Department(Dnumber)
);
```

---

# Referential Actions

### ON DELETE Options

- **NO ACTION:** Rollback delete if dependent records exist.
- **CASCADE:** Delete dependent rows.
- **SET NULL:** Set foreign key column values to NULL.
- **SET DEFAULT:** Set foreign key column values to default.

### ON UPDATE Options

- **NO ACTION:** Rollback update if dependent rows exist.
- **CASCADE:** Update dependent rows.
- **SET NULL:** Set foreign key values to NULL.
- **SET DEFAULT:** Set dependent values to default.

---

## CHECK on Tuples

- Used to enforce constraints across tuples.

### Example

```sql
CHECK (Dept_create_date <= Mgr_start_date)
```

---

# Schema Change Statements in SQL

- Schema evolution allows changes while DB is operational.
- Can add/drop tables, attributes, constraints without recompilation.
- DBMS ensures changes don’t cause inconsistency.

---

## DROP Command

- Removes schema elements like tables, types, or constraints

### Drop Schema

Deletes an entire schema and all of its elements.

- Syntax:

  ```
  DROP SCHEMA schema_name {CASCADE | RESTRICT};

  ```

- **CASCADE** → Removes the schema and all objects inside it (tables, views, constraints, etc.).
- **RESTRICT** → Removes the schema **only if it is empty**.

### &#x20;

### Example

```sql
DROP SCHEMA Company CASCADE;
// Removes the COMPANY schema and everything it contains.
```

### Drop Table

1. Deletes a base relation (table) and its definition.
2. Syntax:

   ```
   DROP TABLE table_name {CASCADE | RESTRICT};

   ```

3. **CASCADE** → Drops the table even if other constraints or views depend on it (those dependent objects are also dropped).
4. **RESTRICT** → Drops the table **only if nothing else depends on it**.

### &#x20;

### Example

```sql
DROP TABLE Dependent CASCADE;
// Removes the DEPENDENT table and all constraints referencing it.
```

---

# MySQL Commands

### Create Database

```sql
CREATE DATABASE [IF NOT EXISTS] employeedb;
```

### Show Databases

```sql
SHOW DATABASES;
SHOW CREATE DATABASE employeedb;
```

### Use Database

```sql
USE employeedb;
```

### Drop Database

```sql
DROP DATABASE [IF EXISTS] employeedb;
```

---

# DESCRIBE Command

- Shows column names, data types, and constraints.

### Syntax

```sql
DESCRIBE table_name;
SHOW COLUMNS FROM table_name;
DESC table_name;
```

---

# ALTER TABLE Command

### Actions

1. **Add Column**

```sql
ALTER TABLE table_name ADD col_name datatype;
```

2. **Add Multiple Columns**

```sql
ALTER TABLE table_name
    ADD col1 datatype,
    ADD col2 datatype;
```

3. **Modify Column**

```sql
ALTER TABLE table_name MODIFY col_name datatype;
```

4. **Drop Column**

```sql
ALTER TABLE table_name DROP COLUMN col_name;
```

5. **Rename Column**

```sql
ALTER TABLE table_name RENAME COLUMN old_name TO new_name;
```

6. **Rename Table**

```sql
ALTER TABLE old_table_name RENAME TO new_table_name;
```

---

## Changing Column Defaults

- **Set Default**

```sql
ALTER TABLE mytbl ALTER j SET DEFAULT 1000;
```

- **Drop Default**

```sql
ALTER TABLE mytbl ALTER j DROP DEFAULT;
```

---

## Adding & Dropping Constraints

- **Add Constraint**

```sql
ALTER TABLE people ADD CONSTRAINT people_gender_fk FOREIGN KEY (gender)
REFERENCES gender_tab;
```

- **Drop Constraint**

```sql
ALTER TABLE Persons DROP CONSTRAINT PK_Person;
```

---

# DROP vs TRUNCATE

### DROP

- Removes table definition & data.
- Deletes constraints & indexes.
- Cannot be rolled back.

### TRUNCATE

- Deletes only rows, keeps structure.
- Resets auto-increment (MySQL).
- Cannot be rolled back.

---

# RENAME Command

```sql
RENAME TABLE products TO products_old;
RENAME TABLE products_new TO products;
```

---

# ALTER Examples

### Create Tables

```sql
CREATE TABLE employee (
    emp_id INT PRIMARY KEY,
    name VARCHAR(50)
);

CREATE TABLE department (
    id SMALLINT PRIMARY KEY
);
```

### Adding New Column

```sql
ALTER TABLE employee ADD COLUMN department_id INT;
```

### Reposition Column

```sql
ALTER TABLE employee MODIFY COLUMN department_id INT AFTER name;
```

### Rename Column

```sql
ALTER TABLE employee RENAME COLUMN department_id TO dept_ref_id;
```

### Change Datatype

```sql
ALTER TABLE employee MODIFY COLUMN dept_ref_id SMALLINT;
```

### Adding Constraints

- **NOT NULL**

```sql
ALTER TABLE employee MODIFY COLUMN dept_ref_id SMALLINT NOT NULL;
```

- **DEFAULT**

```sql
ALTER TABLE employee ALTER COLUMN dept_ref_id SET DEFAULT 100;
```

- **CHECK**

```sql
ALTER TABLE employee ADD CONSTRAINT chk_dept CHECK (dept_ref_id > 0);
```

- **PRIMARY KEY**

```sql
ALTER TABLE employee DROP PRIMARY KEY;
ALTER TABLE employee ADD CONSTRAINT pk_emp PRIMARY KEY (dept_ref_id);
```

- **FOREIGN KEY**

```sql
ALTER TABLE employee ADD CONSTRAINT fk_dept FOREIGN KEY (dept_ref_id)
REFERENCES department(id) ON DELETE CASCADE ON UPDATE CASCADE;
```

---

## Dropping Constraints

- Drop Default

```sql
ALTER TABLE employee ALTER COLUMN dept_ref_id DROP DEFAULT;
```

- Drop Check

```sql
ALTER TABLE employee DROP CONSTRAINT chk_dept;
```

- Drop Primary Key

```sql
ALTER TABLE employee DROP PRIMARY KEY;
```

- Drop Foreign Key

```sql
ALTER TABLE employee DROP FOREIGN KEY fk_dept;
```

---

## Dropping Column

```sql
ALTER TABLE employee DROP COLUMN dept_ref_id;
```

---

# Quick Revision Q&A

1. **Which command deletes all records but keeps structure?**

   - TRUNCATE

2. **What does DESCRIBE do?**

   - Shows table structure.

3. **Which command removes an entire table?**

   - DROP

4. **ALTER TABLE is used for?**

   - Modify table structure.

---

# Summary

- SQL constraints ensure integrity (PRIMARY, FOREIGN, UNIQUE, CHECK, DEFAULT, NOT NULL).
- Referential actions manage updates and deletes across related tables.
- Schema change commands (`ALTER`, `DROP`, `TRUNCATE`, `RENAME`) allow schema evolution.
- Examples demonstrate practical applications of constraints and ALTER operations.

---

**End of Documentation Notes – Complete Extract of Slides**
