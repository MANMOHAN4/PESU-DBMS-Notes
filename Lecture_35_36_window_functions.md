# **Lecture 35 & 36 – Window Functions (Complete Notes)**

---

# **1. Introduction to Window Functions**

Window functions in SQL allow performing calculations **across a set of rows related to the current row**, without collapsing the result into a single row.

### **Difference from Aggregate Functions**

| Aggregate Functions                   | Window Functions                               |
| ------------------------------------- | ---------------------------------------------- |
| Collapse rows into one output         | Keep all rows and add extra calculated columns |
| Use GROUP BY                          | Use OVER() clause                              |
| Cannot show detail + summary together | Can show detail + summary together             |

Example: Calculate average salary for each department **while keeping each employee row**.

---

# **2. Why Window Functions?**

Window functions are useful when you need:

1. **Row-level detail** (every employee, every sale)
2. **Aggregated insights** (average, sum, rank) at the same time

### **Common Use Cases**

- Ranking (ROW_NUMBER, RANK, DENSE_RANK)
- Running totals & cumulative sums
- Moving averages
- Comparing current row to previous/next row (LAG/LEAD)
- Percentiles
- Trends and patterns

---

# **3. Types of Window Functions**

1. **Aggregate Window Functions**
   - SUM(), AVG(), COUNT(), MIN(), MAX()

2. **Ranking Functions**
   - ROW_NUMBER(), RANK(), DENSE_RANK()

3. **Value Functions**
   - LAG(), LEAD(), FIRST_VALUE(), LAST_VALUE()

---

# **4. General Syntax**

```
SELECT column1, column2,
       window_function(columnX)
       OVER([PARTITION BY columnY] [ORDER BY columnZ]) AS new_column
FROM table_name;
```

### **Important Terms**

- **window_function** – any aggregate/ranking/value function
- **PARTITION BY** – divides rows into groups (like GROUP BY but non-destructive)
- **ORDER BY** – defines ordering within each partition

---

# **5. Understanding Through Example**

```
SELECT name, department, salary,
       AVG(salary) OVER (PARTITION BY department) AS dept_avg_salary
FROM employees;
```

### How it works:

1. **OVER** → tells SQL not to collapse rows
2. **PARTITION BY department** → creates mini-groups for each department
3. **AVG(salary)** → computed within each department window

Result → Each employee row gets an additional column showing department average.

---

# **6. Aggregate Window Functions**

Aggregate functions calculate summary stats over a window while keeping rows intact.

### **Examples**

#### **1. Maximum salary per department**

```
SELECT emp_ID, emp_name, department, salary,
       MAX(salary) OVER (PARTITION BY department) AS MaxDeptSalary
FROM Employees;
```

#### **2. Count of employees per department**

```
SELECT emp_ID, emp_name, department, salary,
       COUNT(*) OVER (PARTITION BY department) AS DeptEmployeeCount
FROM Employees;
```

---

# **7. Ranking Window Functions**

Window functions that assign ranks within partitions.

## **1. RANK()**

- Gives same rank for ties
- Skips next rank (1, 1, 3…)

```
SELECT emp_name, department, salary,
       RANK() OVER (PARTITION BY department ORDER BY salary DESC) AS rank_in_dept
FROM Employees;
```

## **2. DENSE_RANK()**

- Same rank for ties
- Does **not** skip ranks (1,1,2…)

```
SELECT emp_name, department, salary,
       DENSE_RANK() OVER (PARTITION BY department ORDER BY salary DESC)
       AS dense_rank_in_dept
FROM Employees;
```

## **3. ROW_NUMBER()**

- Always assigns unique sequential rank (1,2,3…)

```
SELECT emp_name, department, salary,
       ROW_NUMBER() OVER (PARTITION BY department ORDER BY salary DESC)
       AS row_num_in_dept
FROM Employees;
```

---

# **8. Value Functions**

These help compare a row with previous/next row.

## **1. LAG()** – Access previous row value

General syntax:

```
LAG(column, offset, default_value)
OVER (PARTITION BY column ORDER BY column)
```

Example:

```
SELECT emp_name, department, salary,
       LAG(salary,1) OVER (PARTITION BY department ORDER BY salary DESC)
       AS prev_salary
FROM Employees;
```

### **Use Cases**

- Compare current vs previous salary
- Track stock prices
- Detect trends

## **2. LEAD()** – Access next row value

Syntax similar to LAG.
Example:

```
SELECT emp_name, department, salary,
       LEAD(salary,1) OVER (PARTITION BY department ORDER BY salary DESC)
       AS next_salary
FROM Employees;
```

### **Use Cases**

- Predict upcoming values
- Compare sale with next month sale
- Sequence transitions

---

# **9. Practical Use Case Dataset**

The dataset used in slides:

```
SaleID EmpID EmpName Department CustomerID SaleDate      Month Amount
1      101   Alice    HR        C001       2023-01-05    Jan   1000
2      101   Alice    HR        C002       2023-02-10    Feb   2000
3      101   Alice    HR        C001       2023-03-15    Mar   1500
4      102   Bob      HR        C003       2023-01-20    Jan   1200
5      102   Bob      HR        C001       2023-02-25    Feb   1800
6      103   Charlie  IT        C004       2023-01-10    Jan   7000
7      103   Charlie  IT        C005       2023-02-18    Feb   7500
8      104   David    IT        C006       2023-01-28    Jan   7200
```

The slide also provides SQL to create and insert these values.

---

# **10. Practical Use Case Queries**

## **1. Top 2 Sales by each Employee (with Ranking)**

```
SELECT * FROM (
    SELECT *,
           RANK() OVER (PARTITION BY EmpID ORDER BY Amount DESC) AS SaleRank
    FROM Sales
) AS ranked
WHERE SaleRank <= 2;
```

## **2. Running Total of Sales per Employee**

```
SELECT EmpID, EmpName, Month, Amount,
       SUM(Amount) OVER (PARTITION BY EmpID ORDER BY SaleDate) AS RunningTotal
FROM Sales;
```

## **3. First Sale of Each Customer**

```
SELECT * FROM (
    SELECT *, ROW_NUMBER() OVER (PARTITION BY CustomerID ORDER BY SaleDate) AS rn
    FROM Sales
) AS first_sales
WHERE rn = 1;
```

## **4. Compare Current Sale with Previous Sale (LAG)**

```
SELECT EmpID, EmpName, Month, Amount,
       LAG(Amount) OVER (PARTITION BY EmpID ORDER BY SaleDate) AS PrevAmount
FROM Sales;
```

## **5. Rank Employees by Sale Amount within Department**

```
SELECT EmpID, EmpName, Department, Amount,
       RANK() OVER (PARTITION BY Department ORDER BY Amount DESC) AS RankInDept
FROM Sales;
```

---

# **11. References (as per slides)**

- MySQL Window Functions Reference
- SQL Server Window Functions (Microsoft Docs)
- GeeksforGeeks
