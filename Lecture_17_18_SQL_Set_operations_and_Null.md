# Lecture_17_SQL_Set_Operations_and_Null_Values

---

# Set Operations in SQL

SQL set operations are used to combine two or more SQL SELECT statements. The number of columns and their data types must be the same in the participating queries.

### Types of Set Operations

1. UNION
2. UNION ALL
3. INTERSECT
4. INTERSECT ALL
5. EXCEPT
6. EXCEPT ALL

---

## UNION

- Combines results of two queries and removes duplicates.
- Syntax:

```sql
SELECT column_name FROM table1
UNION
SELECT column_name FROM table2;
```

**Example:**

```sql
SELECT grade FROM student_course WHERE course = 'Physics'
UNION
SELECT grade FROM student_course WHERE course = 'Mathematics';
```

---

## UNION ALL

- Similar to UNION but **retains duplicates**.
- If a value occurs `c1` times in one relation and `c2` times in the other, result contains `c1 + c2` occurrences.
- Syntax:

```sql
SELECT column_name FROM table1
UNION ALL
SELECT column_name FROM table2;
```

**Example:**

```sql
SELECT grade FROM student_course WHERE course = 'Physics'
UNION ALL
SELECT grade FROM student_course WHERE course = 'Mathematics';
```

With DISTINCT:

```sql
SELECT DISTINCT grade FROM student_course WHERE course = 'Physics'
UNION ALL
SELECT DISTINCT grade FROM student_course WHERE course = 'Mathematics';
```

Here, duplicates are removed within each SELECT, but UNION ALL still retains duplicates across results.

---

## INTERSECT

- Returns rows common to both queries.
- Removes duplicates.
- Syntax:

```sql
SELECT column_name FROM table1
INTERSECT
SELECT column_name FROM table2;
```

**Example:**

```sql
SELECT grade FROM student_course WHERE course = 'Physics'
INTERSECT
SELECT grade FROM student_course WHERE course = 'Mathematics';
```

---

## INTERSECT ALL

- Similar to INTERSECT but retains duplicates.
- If a value occurs `c1` times in one set and `c2` times in another, result contains `min(c1, c2)` occurrences.
- Syntax:

```sql
SELECT column_name FROM table1
INTERSECT ALL
SELECT column_name FROM table2;
```

**Example:**

```sql
SELECT grade FROM student_course WHERE course = 'Physics'
INTERSECT ALL
SELECT grade FROM student_course WHERE course = 'Mathematics';
```

---

## EXCEPT

- Returns rows present in the first query but not in the second.
- Removes duplicates.
- Syntax:

```sql
SELECT column_name FROM table1
EXCEPT
SELECT column_name FROM table2;
```

**Example:**

```sql
SELECT grade FROM student_course WHERE course = 'Physics'
EXCEPT
SELECT grade FROM student_course WHERE course = 'Mathematics';
```

---

## EXCEPT ALL

- Similar to EXCEPT but retains duplicates.
- If a value occurs `c1` times in the first set and `c2` times in the second, result contains `max(c1 - c2, 0)` occurrences.
- Syntax:

```sql
SELECT column_name FROM table1
EXCEPT ALL
SELECT column_name FROM table2;
```

**Example:**

```sql
SELECT grade FROM student_course WHERE course = 'Physics'
EXCEPT ALL
SELECT grade FROM student_course WHERE course = 'Mathematics';
```

---

# Null Values in SQL

SQL has rules for dealing with NULL values. NULL can mean:

1. **Unknown value** – Value exists but not known.
2. **Unavailable/withheld value** – Exists but intentionally hidden.
3. **Not applicable** – Attribute doesn’t apply for that tuple.

### Properties

- SQL does not distinguish between meanings of NULL.
- Each NULL is considered distinct.
- Arithmetic, comparison, and set operations handle NULL differently.

---

## Null Values in Arithmetic

- Any arithmetic with NULL results in NULL.

**Example:**

```sql
SELECT Hours, Hours+10 FROM WORKS_ON;
```

- If Hours = NULL → Result = NULL.

---

## Null Values in Comparisons

- Introduces a third logic state: **UNKNOWN**.
- Example: `(1 < NULL)` = UNKNOWN.
- `(NOT (1 < NULL))` = UNKNOWN.

### Three-Valued Logic

- SQL logic includes TRUE, FALSE, UNKNOWN.
- WHERE clause includes tuples only if condition = TRUE.

---

## Null Values and Joins

- Tuples with NULL in join attributes are excluded (unless OUTER JOIN).

---

## IS NULL and IS NOT NULL

- Used to check for NULL explicitly.

**Example:**

```sql
SELECT Fname, Lname FROM EMPLOYEE WHERE Super_ssn IS NULL;
```

- Retrieves employees without supervisors.

---

## IS UNKNOWN and IS NOT UNKNOWN

- Allows testing if a comparison evaluates to UNKNOWN.

**Example:**

```sql
SELECT Essn, Pno
FROM WORKS_ON
WHERE Hours > 10 IS UNKNOWN;
```

- Returns tuples where Hours = NULL.

---

## Null Values and DISTINCT

- DISTINCT treats NULLs as identical when eliminating duplicates.

Example: Tuples `(A, NULL)` and `(A, NULL)` → considered identical.

- Behavior is different in predicates (where NULL = NULL → UNKNOWN).
- Same rule applies for UNION, INTERSECT, and EXCEPT.

---

# Quick Revision Q&A

1. **Which set operation keeps duplicates?**

   - UNION ALL, INTERSECT ALL, EXCEPT ALL

2. **What does INTERSECT return?**

   - Common rows from two queries.

3. **What are the three meanings of NULL?**

   - Unknown, unavailable/withheld, not applicable.

4. **What logical values exist in SQL?**

   - TRUE, FALSE, UNKNOWN

5. **Which SQL operator is used to check NULL?**
   - IS NULL, IS NOT NULL

---

# Summary

- SQL supports UNION, UNION ALL, INTERSECT, INTERSECT ALL, EXCEPT, EXCEPT ALL.
- NULL values represent unknown/unavailable/not applicable data.
- Arithmetic with NULL = NULL.
- Comparisons with NULL → UNKNOWN.
- SQL supports three-valued logic.
- DISTINCT treats NULLs as equal for duplicate elimination.

---

**End of Lecture 17 Notes – Complete Extract of Slides**
