# Hands-On Relational Algebra Queries and Practice Problems

## Query Expression Forms

### Procedural Form
- Step-by-step operations
- Explicit intermediate relations
- Easy to understand and debug
- Shows query execution strategy

### Single Expression Form
- Nested operations
- Compact representation
- Follows mathematical notation
- Single combined formula

---

## Example Query 1: Research Department Employees

**Query**: Retrieve name and address of all employees working for Research department.

### Procedural Form
```
RESEARCH_DEPT ← σ_Dname='Research' (DEPARTMENT)
RESEARCH_EMPS ← RESEARCH_DEPT ⋈_DNO=DNO EMPLOYEE
RESULT ← ∏_Fname, Lname, Address (RESEARCH_EMPS)
```

### Single Expression Form
```
∏_Fname, Lname, Address (σ_Dname='Research' (DEPARTMENT ⋈_DNO=DNO EMPLOYEE))
```

**Steps**:
1. SELECT from DEPARTMENT where Dname = 'Research'
2. JOIN with EMPLOYEE on DNO = Dno
3. PROJECT only name and address columns

---

## Example Query 2: Employees with No Dependents

**Query**: Retrieve names of employees who have NO dependents.

### Procedural Form
```
ALL_EMPS ← ∏_SSN (EMPLOYEE)
EMPS_WITH_DEPS ← ∏_ESSN (DEPENDENT)
EMPS_WITHOUT_DEPS ← ALL_EMPS − EMPS_WITH_DEPS
RESULT ← ∏_Lname, Fname (EMPS_WITHOUT_DEPS ⋈ EMPLOYEE)
```

### Single Expression Form
```
∏_Lname, Fname ((∏_SSN(EMPLOYEE) − ∏_ESSN(DEPENDENT)) ⋈ EMPLOYEE)
```

**Strategy**:
1. Get all employee SSNs
2. Get SSNs of employees with dependents
3. Use SET DIFFERENCE to find employees without dependents
4. JOIN back to EMPLOYEE to get names

---

## Example Queries: Sailor Database

**Schema**:
```
SAILOR (SID, Name, Rating, Age)
BOAT (BID, BName, Color)
RESERVES (SID, BID, Date)
```

### Query 1: Expert Sailors (Rating > 8)
```
σ_Rating>8 (SAILOR)
```

### Query 2: Rating of Each Sailor
```
∏_SID, Name, Rating (SAILOR)
```

### Query 3: Age of Sailors
```
∏_Age (SAILOR)
```

### Query 4: Name and Rating of Highly Rated Sailors
```
∏_Name, Rating (σ_Rating>8 (SAILOR))
```

### Query 5: Sailors Who Reserved Boat 101
```
∏_Name (σ_BID=101 (SAILOR ⋈ RESERVES))
```

### Query 6: Boats Reserved by Red-Haired Sailors
```
∏_BName (σ_Color='Red' (BOAT ⋈ RESERVES ⋈ SAILOR))
```

---

## Example Queries: Pizza Database (Practice)

**Schema**:
```
PERSON (Name, Age, Gender)
FREQUENTS (Name, Pizzeria)
EATS (Name, Pizza)
SERVES (Pizzeria, Pizza, Price)
```

### Query 1: Pizzerias Frequented by People Under 18
```
∏_Pizzeria (FREQUENTS ⋈_Name ∏_Name(σ_Age<18(PERSON)))

Or more explicitly:
YOUNG_PEOPLE ← σ_Age<18 (PERSON)
PIZZERIAS ← FREQUENTS ⋈_Name YOUNG_PEOPLE
RESULT ← ∏_Pizzeria (PIZZERIAS)
```

### Query 2: Females Who Eat Mushroom OR Pepperoni
```
FEMALES ← σ_Gender='F' (PERSON)
MUSHROOM_EATERS ← FEMALES ⋈ σ_Pizza='Mushroom' (EATS)
PEPPERONI_EATERS ← FEMALES ⋈ σ_Pizza='Pepperoni' (EATS)
RESULT ← ∏_Name (MUSHROOM_EATERS ∪ PEPPERONI_EATERS)
```

### Query 3: Females Who Eat BOTH Mushroom AND Pepperoni
```
FEMALES ← σ_Gender='F' (PERSON)
MUSHROOM_EATERS ← FEMALES ⋈ σ_Pizza='Mushroom' (EATS)
PEPPERONI_EATERS ← FEMALES ⋈ σ_Pizza='Pepperoni' (EATS)
RESULT ← ∏_Name (MUSHROOM_EATERS ∩ PEPPERONI_EATERS)
```

**Alternative**:
```
∏_Name (σ_Gender='F' (PERSON) ⋈ σ_Pizza='Mushroom'(EATS)) ∩
∏_Name (σ_Gender='F' (PERSON) ⋈ σ_Pizza='Pepperoni'(EATS))
```

### Query 4: Pizzerias Serving Pizza Amy Eats for < $10
```
AMY_EATS ← σ_Name='Amy' (EATS)
CHEAP_PIZZA ← σ_Price<10 (SERVES)
RESULT ← ∏_Pizzeria (AMY_EATS ⋈ CHEAP_PIZZA)
```

### Query 5: Pizzerias Frequented by Only Females OR Only Males
```
FEMALE_FREQ ← ∏_Pizzeria (σ_Gender='F' (PERSON) ⋈ FREQUENTS)
MALE_FREQ ← ∏_Pizzeria (σ_Gender='M' (PERSON) ⋈ FREQUENTS)
FEMALE_ONLY ← FEMALE_FREQ − MALE_FREQ
MALE_ONLY ← MALE_FREQ − FEMALE_FREQ
RESULT ← FEMALE_ONLY ∪ MALE_ONLY
```

### Query 6: Pizza Person Eats NOT Served at Their Pizzeria
```
PERSON_PIZZA ← ∏_Name, Pizza (EATS)
PERSON_SERVES ← ∏_Name, Pizza (FREQUENTS ⋈ SERVES)
RESULT ← PERSON_PIZZA − PERSON_SERVES
```

### Query 7: People Who Frequent Only Pizzerias Serving Pizzas They Eat
```
ALL_PIZZERIAS ← ∏_Pizzeria (FREQUENTS)
GOOD_PIZZERIAS ← ∏_Pizzeria (EATS ⋈ SERVES ⋈ FREQUENTS)
BAD_PEOPLE ← ∏_Name (FREQUENTS ⋈ (ALL_PIZZERIAS − GOOD_PIZZERIAS))
ALL_PEOPLE ← ∏_Name (FREQUENTS)
RESULT ← ALL_PEOPLE − BAD_PEOPLE
```

### Query 8: People Who Frequent EVERY Pizzeria Serving Pizzas They Eat
```
PERSON_PIZZA ← EATS
SERVES_PIZZA ← ∏_Pizzeria, Pizza (SERVES)
PERSON_PIZZERIA_PIZZA ← FREQUENTS ⋈ SERVES
RESULT ← PERSON_PIZZA ⋈ PERSON_PIZZERIA_PIZZA ÷ SERVES_PIZZA
```

### Query 9: Cheapest Pepperoni Pizza Pizzeria
```
PEPPERONI ← σ_Pizza='Pepperoni' (SERVES)
MIN_PRICE ← ℱ_MIN(Price) (PEPPERONI)
RESULT ← σ_Price=MIN_PRICE (PEPPERONI)
```

---

## Common Query Patterns

### Pattern 1: Find Entities with Specific Property
```
σ_condition (RELATION)
```

### Pattern 2: Find Specific Attributes
```
∏_attributes (RELATION)
```

### Pattern 3: Find Entities Meeting Multiple Conditions
```
∏_attrs (σ_cond1 ∧ cond2 ∧ cond3 (RELATION))
```

### Pattern 4: Find Entities Matching Related Data
```
∏_attrs (RELATION1 ⋈ RELATION2)
```

### Pattern 5: Find Entities with Property OR Property
```
∏_Name (σ_prop1 (TABLE)) ∪ ∏_Name (σ_prop2 (TABLE))
```

### Pattern 6: Find Entities with Property AND Property
```
∏_Name (σ_prop1 (TABLE)) ∩ ∏_Name (σ_prop2 (TABLE))
```

### Pattern 7: Find Entities Excluding Some
```
∏_Name (TABLE1) − ∏_Name (TABLE2)
```

---

## Common Mistakes and How to Avoid

### Mistake 1: Confusing INTERSECTION and DIFFERENCE
```
❌ WRONG: σ_Gender='F' (STUDENT) ∩ σ_Major='CS' (STUDENT)
Thinking: Find intersection of female students and CS majors

✅ RIGHT: σ_Gender='F' ∧ Major='CS' (STUDENT)
Or: σ_Gender='F' (σ_Major='CS' (STUDENT))
Reason: Both conditions apply to same tuple
```

### Mistake 2: Wrong Order of Operations
```
❌ WRONG: ∏_Name (EMPLOYEE) − ∏_Name (DEPENDENT)
Result doesn't include other EMPLOYEE attributes

✅ RIGHT: ∏_EmpID (EMPLOYEE) − ∏_EmpID (DEPENDENT)
Then join back if needed
```

### Mistake 3: Forgetting JOIN Condition
```
❌ WRONG: EMPLOYEE × DEPARTMENT
(All combinations - 1000 employees × 10 departments = 10,000 rows)

✅ RIGHT: EMPLOYEE × DEPARTMENT where Emp.DeptID = Dept.DeptID
Or: EMPLOYEE ⋈ DEPARTMENT
```

### Mistake 4: Misusing SET DIFFERENCE
```
❌ WRONG: A − B same as B − A
NOT TRUE! Set difference is NOT commutative

A − B = elements in A but not in B
B − A = elements in B but not in A
```

---

## Practice Problems Solutions

### Problem 1: Find Names of Instructors in Physics
```
∏_Name (σ_Dept_Name='Physics' (INSTRUCTOR))
```

### Problem 2: Find IDs and Names of Highly Paid Instructors in Tech
```
∏_ID, Name (σ_Dept_Name='Technology' ∧ Salary>100000 (INSTRUCTOR))
```

### Problem 3: Students NOT Enrolled in Any Course
```
∏_SID (STUDENT) − ∏_SID (ENROLLED)
```

### Problem 4: Find All Departments and Their Employees
```
∏_DName, Fname, Lname (DEPARTMENT ⋈ EMPLOYEE)
```

### Problem 5: Find Courses Taught by Multiple Instructors
```
COURSES_BY_INSTR1 ← ∏_CourseID (TEACHES)
COURSES_BY_INSTR2 ← ∏_CourseID (TEACHES)
RESULT ← COURSES_BY_INSTR1 ∩ COURSES_BY_INSTR2
```

---

## Tips for Writing Relational Algebra Queries

1. **Start with SELECT**: Identify which tuples you need first
2. **Then PROJECT**: Decide which columns to keep
3. **Use assignments**: Break complex queries into steps
4. **Verify union compatibility**: Before union/intersection/difference
5. **Check join conditions**: Make sure foreign keys match
6. **Eliminate duplicates intentionally**: Using set operations
7. **Test with small examples**: Verify logic on sample data
8. **Multiple valid solutions**: Same query can be expressed different ways

---

## Key Takeaways

1. **Procedural form**: Easier to understand and debug
2. **Single expression form**: More compact and mathematical
3. **Multiple equivalent queries**: Choose most efficient
4. **Common patterns**: Recognize SELECT-PROJECT-JOIN pattern
5. **Practice**: More complex queries combine multiple operations
6. **Verification**: Always test with concrete examples
