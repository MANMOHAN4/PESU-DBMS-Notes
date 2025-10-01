# Lecture_13_SQL_Overview

---

# Unit 1: Introduction to Database Management and SQL

---

## Introduction to SQL Commands

- SQL = **Structured Query Language**
- Originally named **SEQUEL** (“SEQUEL to SQUARE” – Chamberlin & Boyce, IBM).
- SQL is one of the **major reasons for the commercial success** of relational databases.

### Features of SQL

- Data definition
- Data manipulation
- Transaction control
- Indexing
- Security specification (GRANT, REVOKE)
- Active databases
- Multimedia databases
- Distributed databases

---

## Types of Database Languages

- **DDL (Data Definition Language):** Defines database structure or schema.
- **DML (Data Manipulation Language):** Manages data within schema objects.
- **DCL (Data Control Language):** Controls access to the database.
- **TCL (Transaction Control Language):** Manages changes made by DML.

---

## Schema Concepts in SQL

- **Schema:** Identified by a schema name.
- Includes:
  - Authorization identifier
  - Descriptors for each element
- Elements include tables, constraints, views, domains.
- Each SQL statement ends with a semicolon `;`.

### Syntax – CREATE SCHEMA

```sql
-- MySQL
CREATE SCHEMA Library;
-- Same as CREATE DATABASE Library;

-- PostgreSQL / Oracle
CREATE SCHEMA Library AUTHORIZATION 'Jsmith';
```

---

# Attribute Data Types in SQL

## Basic Data Types

### Numeric

- `INTEGER`, `INT`, `SMALLINT`
- Floating-point: `FLOAT`, `REAL`, `DOUBLE PRECISION`

### Character Strings

- Fixed-length: `CHAR(n)`, `CHARACTER(n)`
- Varying-length: `VARCHAR(n)`, `CHARACTER VARYING(n)`

### Bit Strings

- Fixed-length: `BIT(n)`
- Varying-length: `BIT VARYING(n)`

### Boolean

- Values: `TRUE` or `FALSE`

### DATE

- Format: `YYYY-MM-DD`
- Components: Year, Month, Day  
  (Link: [MySQL Date Functions](https://www.javatpoint.com/mysql-date-time))

### TIMESTAMP

- Includes DATE + TIME + fractional seconds
- Optional: `WITH TIME ZONE`

### INTERVAL

- Specifies relative values for date/time arithmetic.

---

## Extended Data Types

- **NUMERIC(p, d):** Fixed-point with precision `p` digits, `d` after the decimal.
- **REAL, DOUBLE PRECISION:** Floating-point with machine-dependent precision.
- **FLOAT(n):** Floating-point with at least `n` digits precision.
- **NULL:** Represents unknown/missing values.
- **ENUM:** Restricts values to a fixed list.

```sql
ENUM('value1','value2','value3')
```

- **SET:** Allows storing multiple values chosen from a predefined list.

---

## CHAR vs VARCHAR

- `CHAR(n)`: Fixed-length, padded with spaces.
- `VARCHAR(n)`: Variable-length, no padding.

---

## Large Object (LOB) Types

### BLOB (Binary Large Object)

- Stores binary data: images, videos, audio.
- Cannot have default values.
- Types: `TINYBLOB`, `BLOB`, `MEDIUMBLOB`, `LONGBLOB`

### CLOB / TEXT (Character Large Object)

- Stores large text.
- Cannot have default values.
- Types: `TINYTEXT`, `TEXT`, `MEDIUMTEXT`, `LONGTEXT`

---

## Example Table Covering All Data Types

```sql
CREATE TABLE example (
    id INT(6) UNSIGNED AUTO_INCREMENT PRIMARY KEY,
    char_column CHAR(50) NOT NULL,
    varchar_column VARCHAR(255) NOT NULL,
    tinytext_column TINYTEXT NOT NULL,
    text_column TEXT NOT NULL,
    mediumtext_column MEDIUMTEXT NOT NULL,
    longtext_column LONGTEXT NOT NULL,
    binary_column BINARY(20) NOT NULL,
    varbinary_column VARBINARY(20) NOT NULL,
    tinyblob_column TINYBLOB NOT NULL,
    blob_column BLOB NOT NULL,
    mediumblob_column MEDIUMBLOB NOT NULL,
    longblob_column LONGBLOB NOT NULL,
    enum_column ENUM('value1','value2','value3') NOT NULL,
    set_column SET('value1','value2','value3') NOT NULL,
    bool_column BOOLEAN NOT NULL DEFAULT 0,
    date_column DATE NOT NULL,
    datetime_column DATETIME NOT NULL,
    timestamp_column TIMESTAMP NOT NULL DEFAULT CURRENT_TIMESTAMP ON UPDATE CURRENT_TIMESTAMP,
    time_column TIME NOT NULL,
    year_column YEAR(4) NOT NULL,
    float_column FLOAT(10,6) NOT NULL DEFAULT '0.000000',
    double_column DOUBLE(10,6) NOT NULL DEFAULT '0.000000'
);
```

---

# SQL Data Definition, Data Types, and Standards

- **Terminology:**
  - Table = Relation
  - Row = Tuple
  - Column = Attribute

### CREATE Statement

- Main SQL command for data definition.
- Used to create a relation by specifying attributes and constraints.

---

# Data Definition Language (DDL)

## Common DDL Commands

- `CREATE`: Create objects in database.
- `ALTER`: Modify schema.
- `DROP`: Delete schema/objects.
- `TRUNCATE`: Delete all records, preserve structure.
- `COMMENT`: Add metadata.
- `RENAME`: Rename objects.

### CREATE TABLE – Syntax

```sql
CREATE TABLE table_name (
    col_name1 datatype,
    col_name2 datatype,
    ...
);
```

### CREATE TABLE from Existing

```sql
CREATE TABLE new_table AS SELECT * FROM existing_table;
CREATE TABLE new_table LIKE existing_table;
```

### ALTER TABLE

```sql
ALTER TABLE table_name ADD col_name datatype;
ALTER TABLE table_name MODIFY col_name datatype;
ALTER TABLE table_name DROP column_name;
ALTER TABLE table_name RENAME COLUMN old_name TO new_name;
```

### DESCRIBE and SHOW

```sql
DESCRIBE table_name;
SHOW CREATE TABLE table_name;
```

### DROP vs TRUNCATE

| Feature        | DROP            | TRUNCATE                    |
| -------------- | --------------- | --------------------------- |
| Removes data   | Yes             | Yes                         |
| Removes table  | Yes             | No (structure remains)      |
| Auto increment | N/A             | Resets to 1 (MySQL)         |
| Constraints    | Deleted         | Preserved                   |
| Rollback       | Cannot rollback | Cannot rollback             |
| Use case       | Delete forever  | Empty table, keep structure |

---

# COMPANY Relational Database Schema

### Tables

```sql
CREATE TABLE department (
    Dname VARCHAR(15) NOT NULL,
    Dnumber INT NOT NULL,
    Mgr_ssn CHAR(9) NOT NULL,
    Mgr_start_date DATE
);

CREATE TABLE dependent (
    Essn CHAR(9) NOT NULL,
    Dependent_name VARCHAR(15) NOT NULL,
    Gender CHAR(1),
    Bdate DATE,
    Relationship VARCHAR(8)
);

CREATE TABLE dept_locations (
    Dnumber INT NOT NULL,
    Dlocation VARCHAR(15) NOT NULL
);

CREATE TABLE employee (
    Fname VARCHAR(15) NOT NULL,
    Minit CHAR(1),
    Lname VARCHAR(15) NOT NULL,
    Ssn CHAR(9) NOT NULL,
    Bdate DATE,
    Address VARCHAR(30),
    Gender CHAR(1),
    Salary DECIMAL(10,2),
    Super_ssn CHAR(9),
    Dno INT NOT NULL
);

CREATE TABLE project (
    Pname VARCHAR(15) NOT NULL,
    Pnumber INT NOT NULL,
    Plocation VARCHAR(15),
    Dnum INT NOT NULL
);

CREATE TABLE works_on (
    Essn CHAR(9) NOT NULL,
    Pno INT NOT NULL,
    Hours DECIMAL(3,1)
);
```

---

## One Possible Database State

### Employee Table

| Fname  | Minit | Lname  | Ssn       | Bdate      | Address     | Gender | Salary | Super_ssn | Dno |
| ------ | ----- | ------ | --------- | ---------- | ----------- | ------ | ------ | --------- | --- |
| John   | B     | Smith  | 123456789 | 1965-01-09 | Houston, TX | M      | 30000  | 333445555 | 5   |
| Frank  | T     | Wong   | 333445555 | 1955-12-08 | Bellaire,TX | M      | 40000  | 888665555 | 5   |
| Alicia | J     | Zelaya | 999887777 | 1968-07-19 | Houston, TX | F      | 25000  | 987654321 | 4   |

### Department Table

| Dname          | Dnumber | Mgr_ssn   | Mgr_start_date |
| -------------- | ------- | --------- | -------------- |
| Research       | 5       | 333445555 | 1988-05-22     |
| Administration | 4       | 987654321 | 1995-01-01     |
| Headquarters   | 1       | 888665555 | 1981-06-19     |

---

# Comments in DDL

```sql
CREATE TABLE users (
    id INT PRIMARY KEY,
    name VARCHAR(255)
) COMMENT 'This table stores user information';

CREATE TABLE products (
    product_id INT PRIMARY KEY COMMENT 'Unique ID',
    product_name VARCHAR(255) COMMENT 'Product name',
    price DECIMAL(10,2) COMMENT 'Price of product'
);
```

---

# Quick Revision Q&A

1. Difference between CHAR(n) and VARCHAR(n)?
2. Format of DATE in SQL?
3. Difference between BLOB and CLOB?
4. Purpose of ENUM in MySQL?

---

# Summary

- SQL provides DDL, DML, DCL, TCL.
- Data types: numbers, text, binary, date/time, large objects.
- CHAR vs VARCHAR, BLOB vs CLOB, DROP vs TRUNCATE are key.
- DDL includes CREATE, ALTER, DROP, TRUNCATE, COMMENT, RENAME.
- COMPANY schema shows relational database design.

---

**End of Lecture 13 Notes – Complete Extract of Slides**
