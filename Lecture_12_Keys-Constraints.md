# Database Keys and Constraints

## Constraints Overview

**Definition**:
Rules that determine which values are permissible in the database and maintain data integrity.

**Three Main Types of Constraints**:

1. **Inherent/Implicit Constraints**
   - Based on data model itself
   - Example: Relational model doesn't allow composite/multivalued attributes as atomic values

2. **Schema-Based/Explicit Constraints**
   - Expressed in schema using database facilities
   - Can be enforced by DBMS automatically
   - Example: Domain constraints, key constraints, integrity constraints

3. **Application-Based/Semantic Constraints**
   - Business rules beyond model's expressiveness
   - Must be specified and enforced by application programs
   - Example: Salary < Manager's salary, Employee age ≥ 18

---

## Main Types of Explicit/Schema-Based Constraints

1. **Domain Constraint**
2. **Key Constraint**
3. **Null Constraint**
4. **Entity Integrity Constraint**
5. **Referential Integrity Constraint**

---

## Domain Constraints

**Definition**:
Every value in a tuple must be an atomic value from the domain of its attribute.

**Requirements**:
- Values must be atomic (smallest indivisible units)
- No composite attributes allowed
- No multivalued attributes allowed
- Value domain must be defined with specific data type

**Atomic Values Rule**:
- ✅ ALLOWED: Single name, single value
- ❌ NOT ALLOWED: Full name as composite (FirstName + LastName in same cell)
- ❌ NOT ALLOWED: Multiple phone numbers in single cell

**Data Types for Domains**:
- **Numeric**: short integer, integer, long integer, float, double-precision float
- **Character**: char, varchar
- **Date/Time**: date, time, timestamp
- **Boolean**: true/false
- **Special Types**: enumerated types, subranges

**Example - Domain Constraint Violation**:
```
PERSON relation:
Name: "John Smith" (Composite - VIOLATES domain constraint)
Phone: "555-1234, 555-5678" (Multivalued - VIOLATES domain constraint)
Age: 25 (Atomic - OK)
```

**Correction**:
```
PERSON (FirstName, LastName, Age)
PHONE (PersonID, PhoneNumber)

FirstName: "John" (Atomic)
LastName: "Smith" (Atomic)
Age: 25 (Atomic)
PhoneNumber appears in separate relation
```

---

## Keys

### Superkey

**Definition**:
Set of one or more attributes that, taken collectively, uniquely identifies a tuple.

**Formal Definition**:
SK is a superkey if no two distinct tuples have same values on all attributes in SK.

**Characteristics**:
- May contain extra (extraneous) attributes
- Any superset of a superkey is also a superkey
- May not be minimal

**Example**:
```
INSTRUCTOR (ID, Name, Dept_Name, Salary)

Candidate Superkeys:
- {ID} ✓ (unique)
- {ID, Name} ✓ (unique but redundant with ID)
- {ID, Name, Dept_Name} ✓ (unique but redundant)
- {Name} ✗ (NOT unique - multiple instructors with same name)
- {Dept_Name} ✗ (NOT unique - multiple instructors per department)
```

### Candidate Key

**Definition**:
Minimal superkey - a superkey where removing any attribute makes it NOT a superkey.

**Characteristics**:
- No extraneous attributes
- Relation can have multiple candidate keys
- Each is equally valid as primary key
- Must be minimal AND unique

**Example**:
```
CAR (State, RegNo, SerialNo, Make, Model, Year)

Candidate Keys:
- {SerialNo} ✓ Unique for every car
- {State, RegNo} ✓ Unique combination (license plate)
- {SerialNo, State, RegNo} ✗ Not minimal (SerialNo alone is sufficient)

Two candidate keys exist: {SerialNo} and {State, RegNo}
```

### Primary Key

**Definition**:
One candidate key chosen arbitrarily as the main unique identifier for tuples.

**Characteristics**:
- Only ONE primary key per relation
- Used to uniquely identify each tuple
- Provides tuple identity
- Cannot have NULL values (Entity Integrity)
- Should be small (few attributes)
- Attributes rarely or never change

**Selection Criteria**:
1. Choose smallest candidate key (fewer attributes)
2. Attributes should never/rarely change
3. Should be unique identifier issued by organization

**Example - Bad Choice**:
```
❌ WRONG: Use Address as primary key
Why? Address changes when person moves

✅ RIGHT: Use SSN or AadharNumber
Why? Unique identifier that never changes
```

**Notation**:
Primary key attributes underlined in schema.

**Example**:
```
CLASSROOM (building, room_number, capacity)
┴─────────┴─────────────

Primary Key: {building, room_number}
(Neither alone is unique, but together they identify room)
```

---

## Null Constraints

**Definition**:
Specifies whether NULL values are allowed for an attribute.

**Options**:
- **NOT NULL**: Attribute must have value (required field)
- **NULL allowed**: Attribute can be NULL (optional field)

**Example**:
```
STUDENT (StudentID NOT NULL, Name NOT NULL, Phone NULL)

- StudentID: Must have value
- Name: Must have value
- Phone: Can be NULL if student has no phone
```

**Common NULL Constraints**:
- Primary key attributes: Always NOT NULL
- Foreign key attributes: Usually NOT NULL (sometimes NULL if optional relationship)
- Optional fields: NULL allowed

---

## Entity Integrity Constraint

**Definition**:
Primary key attributes of a relation cannot have NULL values in any tuple.

**Rationale**:
- Primary key identifies tuples
- NULL would make identification impossible
- Violates entity's existence

**Formal Rule**:
```
For any tuple t in r(R):
t[PK] ≠ NULL
```

**Example - Violation**:
```
EMPLOYEE (EID [PK], Name, Dept, Salary)

❌ INVALID:
EID: NULL, Name: "John", Dept: "IT", Salary: 50000
Why? Primary key is NULL - violates entity integrity

✅ VALID:
EID: 001, Name: "John", Dept: "IT", Salary: 50000
Primary key has value
```

**Multi-Attribute Primary Key**:
```
If PK = {CourseID, SecID, Year}:
- None of these can be NULL
- All must have values simultaneously
```

---

## Foreign Key Constraint

**Definition**:
Attribute(s) in one relation referencing primary key of another relation, maintaining referential integrity.

**Components**:
- **Referencing Relation**: Contains foreign key (child table)
- **Referenced Relation**: Contains referenced primary key (parent table)
- **Foreign Key (FK)**: Attribute(s) in referencing relation
- **Primary Key (PK)**: Attribute(s) being referenced

**Rules**:
1. FK attributes have same domain as referenced PK
2. FK value either:
   - Equals some PK value in referenced relation, OR
   - Is NULL (if NULL allowed)

**Example**:
```
DEPARTMENT (DeptID [PK], DeptName, Manager)
EMPLOYEE (EmpID [PK], Name, DeptID [FK], Salary)

- DeptID in EMPLOYEE references DeptID in DEPARTMENT
- EMPLOYEE is referencing relation
- DEPARTMENT is referenced relation
- EmpID must work in existing department (or DeptID = NULL)
```

**Valid Foreign Key Values**:
```
If DEPARTMENT contains DeptID: 10, 20, 30

EMPLOYEE can have DeptID: 10, 20, 30, or NULL
EMPLOYEE cannot have DeptID: 40, 50 (don't exist)
```

**Real-World Example**:
```
INSTRUCTOR (ID [PK], Name, DeptName [FK])
DEPARTMENT (DeptName [PK], Building, Budget)

instructor.DeptName must match some DEPARTMENT.DeptName
OR be NULL if optional relationship
```

---

## Referential Integrity Constraint

**Definition**:
Generalization of foreign key constraint. Referenced attributes don't need to be primary key.

**Difference from Foreign Key**:
- **Foreign Key**: References PRIMARY KEY of referenced relation
- **Referential Integrity**: References ANY attribute of referenced relation

**Example - Referential Integrity (Not Foreign Key)**:
```
TEACHES (InstructorID, CourseID, TimeSlotID)
TIMESLOT (TimeSlotID [NOT primary key], Time)

TimeSlotID in TEACHES should exist in TIMESLOT.TimeSlotID
Even though TimeSlotID is not primary key of TIMESLOT
```

**Why Foreign Keys Preferred**:
- Simpler to understand
- Most DBMSs support foreign key constraints automatically
- Most DBMSs don't support referential integrity on non-PK attributes

---

## Database State: Valid vs Invalid

**Database State**:
Set of relation states at particular moment in time.

**Valid State**:
- Satisfies ALL integrity constraints
- Database is consistent
- All rules enforced

**Invalid State**:
- Violates one or more integrity constraints
- Database is inconsistent
- Data integrity compromised

**Example - Valid vs Invalid**:
```
VALID State:
- All primary keys have values
- All foreign keys reference existing tuples
- All values within domain constraints
- Entity and referential integrity maintained

INVALID State:
- Student has NULL StudentID (violates entity integrity)
- Instructor references non-existent DeptID (violates referential integrity)
- Name field contains number (violates domain constraint)
```

---

## Constraint Enforcement in DBMS

**Automatic by DBMS**:
- Domain constraints (data type checking)
- Primary key uniqueness
- NULL constraints
- Entity integrity (NO NULL in PK)
- Foreign key constraints

**Application Enforced**:
- Business rules (age >= 18, salary reasonable ranges)
- Complex semantic constraints
- Multi-table business logic

---

## Summary Table: Constraints

| Constraint | Type | Enforced | Example |
|-----------|------|----------|---------|
| Domain | Explicit | DBMS | Age: integer 0-120 |
| Superkey | Explicit | DBMS | {ID} is superkey |
| Candidate Key | Explicit | DBMS | {SSN}, {DL#} both candidates |
| Primary Key | Explicit | DBMS | StudentID NOT NULL, unique |
| Null | Explicit | DBMS | Name NOT NULL |
| Entity Integrity | Explicit | DBMS | PK ≠ NULL |
| Foreign Key | Explicit | DBMS | DeptID references Department |
| Referential Integrity | Explicit | DBMS | FK exists in referenced table |
| Semantic | Implicit | Application | Salary >= 0 |

---

## Key Takeaways

1. **Domain Constraints**: Atomic values, specific data types
2. **Superkey**: Any set that uniquely identifies tuples (may have extra attributes)
3. **Candidate Key**: Minimal superkey (no extra attributes)
4. **Primary Key**: One chosen candidate key used for identification
5. **Entity Integrity**: Primary key attributes never NULL
6. **Foreign Key**: References primary key of another table
7. **Referential Integrity**: Maintains consistency between related tables
8. **Valid Database**: Satisfies all constraints
9. **Invalid Database**: Violates one or more constraints
