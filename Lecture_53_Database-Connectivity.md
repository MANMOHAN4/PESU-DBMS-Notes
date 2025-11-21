# Database Connectivity

## Definition
Database connectivity refers to integrating applications with a DBMS to enable applications to access, modify, and manage database content programmatically.

## Why Database Connectivity?
- Separates application logic from database logic
- Enables scalable, lightweight solutions for web apps, desktop apps, data analysis, and automation
- Python + MySQL is a popular combination for these use cases

## Requirements
- Python (version 3+ recommended, 3.10+ for match-case syntax)
- MySQL Server (installed and running)
- Python package: mysql-connector-python
- Basic SQL knowledge

## Environment Setup

### Step 1: Install Python
Download from python.org and install version 3.x (preferably 3.10+)

### Step 2: Install MySQL Server
Download from dev.mysql.com/downloads/mysql/ and install

### Step 3: Install MySQL Connector for Python
```
pip install mysql-connector-python
```

### Optional: MySQL Workbench
GUI tool for database management (useful but optional)

## Sample Application Scenario

A university department needs a lightweight desktop application to manage and analyze student performance across subjects. Faculty should be able to:
- Insert new student records
- Update marks after re-evaluation
- Read all student details
- Generate subject-wise reports
- Delete data if not required

## Database Schema

### Students Table
```sql
CREATE TABLE students (
    student_id INT AUTO_INCREMENT PRIMARY KEY,
    name VARCHAR(100),
    department VARCHAR(50)
);
```

### Subjects Table
```sql
CREATE TABLE subjects (
    subject_id INT AUTO_INCREMENT PRIMARY KEY,
    name VARCHAR(100)
);
```

### Marks Table
```sql
CREATE TABLE marks (
    mark_id INT AUTO_INCREMENT PRIMARY KEY,
    student_id INT,
    subject_id INT,
    marks INT,
    FOREIGN KEY (student_id) REFERENCES students(student_id),
    FOREIGN KEY (subject_id) REFERENCES subjects(subject_id)
);
```

## Sample Data Insertion

### Insert Students
```sql
INSERT INTO students (name, department) VALUES 
('Alice Johnson', 'CSE'),
('Bob Smith', 'ECE'),
('Charlie Lee', 'ME'),
('Diana Patel', 'CSE');
```

### Insert Subjects
```sql
INSERT INTO subjects (name) VALUES 
('Mathematics'),
('Data Structures'),
('Digital Electronics'),
('Thermodynamics');
```

### Insert Marks
```sql
INSERT INTO marks (student_id, subject_id, marks) VALUES 
(1, 1, 85), -- Alice - Math
(1, 2, 90), -- Alice - DS
(2, 1, 78), -- Bob - Math
(2, 3, 88), -- Bob - Digital
(3, 1, 65), -- Charlie - Math
(3, 4, 70), -- Charlie - Thermo
(4, 2, 95), -- Diana - DS
(4, 3, 89); -- Diana - Digital
```

## Connection Basics

### How Connection Works
The mysql.connector package is used to connect to MySQL server with parameters:
- **host**: "localhost" (local machine server)
- **user**: "root" (MySQL username)
- **password**: Password for the user (replace with actual)
- **database**: "university_db" (target database)

**Connection object (conn)**: Enables communication with the database

**Cursor object**: Used to execute SQL statements

### Creating a Connection
```python
import mysql.connector

def get_connection():
    return mysql.connector.connect(
        host="localhost",
        user="root",
        password="yourpassword",
        database="university_db"
    )
```

## What is a Cursor?

A cursor is an object used to interact with the database:
- Executes SQL queries (SELECT, INSERT, UPDATE, DELETE)
- Fetches results using:
  - `fetchone()` - retrieves one row
  - `fetchmany(n)` - retrieves n rows
  - `fetchall()` - retrieves all rows
- Works within a connection
- Requires `conn.commit()` to save data-changing operations

## CRUD Operations

### Create (Insert)
```python
def insert_student(name, department):
    conn = get_connection()
    try:
        cur = conn.cursor()
        cur.execute(
            "INSERT INTO students (name, department) VALUES (%s, %s)",
            (name, department)
        )
        conn.commit()
        print("Student inserted successfully.")
    finally:
        cur.close()
        conn.close()
```

Key points:
- Use `%s` placeholders to prevent SQL injection
- Call `conn.commit()` to save changes
- Always close cursor and connection in finally block

### Read (Select)
```python
def list_students():
    conn = get_connection()
    try:
        cur = conn.cursor()
        cur.execute("SELECT * FROM students")
        for row in cur.fetchall():
            print(row)
    finally:
        cur.close()
        conn.close()
```

### Generate Report
```python
def report():
    conn = get_connection()
    try:
        cur = conn.cursor()
        cur.execute("""
            SELECT m.mark_id, s.name, sub.name, m.marks
            FROM marks m
            JOIN students s ON m.student_id = s.student_id
            JOIN subjects sub ON m.subject_id = sub.subject_id
            ORDER BY s.name, sub.name
        """)
        for row in cur.fetchall():
            print(row)
    finally:
        cur.close()
        conn.close()
```

### Update
```python
def update_marks(student_id, subject_id, new_marks):
    conn = get_connection()
    try:
        cur = conn.cursor()
        cur.execute(
            "UPDATE marks SET marks = %s WHERE student_id = %s AND subject_id = %s",
            (new_marks, student_id, subject_id)
        )
        conn.commit()
        print(f"Updated {cur.rowcount} row(s).")
    finally:
        cur.close()
        conn.close()
```

### Delete
```python
def delete_student(student_id):
    conn = get_connection()
    try:
        cur = conn.cursor()
        # Delete child rows first to respect foreign keys
        cur.execute("DELETE FROM marks WHERE student_id = %s", (student_id,))
        cur.execute("DELETE FROM students WHERE student_id = %s", (student_id,))
        conn.commit()
        print(f"Deleted student {student_id} and related marks.")
    finally:
        cur.close()
        conn.close()
```

## Running Python Code

### Prerequisites
1. Python 3.10 or higher for match-case syntax
2. mysql-connector-python installed
3. MySQL server running

### Execution Steps
1. Save code as a .py file
2. Open terminal and navigate to the file directory
3. Run: `python filename.py` or `py filename.py`

## Q&A

**Q: What is the primary purpose of integrating Python with MySQL in database applications?**
- A. To create GUI-based games
- B. To perform advanced image processing
- C. To enable scalable and lightweight database operations
- D. To convert SQL to NoSQL automatically

Answer: C

**Q: Which of the following commands is used to install the MySQL connector package in Python?**
- A. pip install python-mysql-connector
- B. install mysql-connector
- C. pip install mysql-connector-python
- D. python install mysql

Answer: C

**Q: In the university desktop application scenario, which table stores the relationship between students and their performance in subjects?**
- A. students
- B. subjects
- C. performance
- D. marks

Answer: D
