# Converting ER Diagrams to Relational Schema

## Relational Database Model

### Introduction to Relational Model

**Definition**:
- Represents database as collection of relations
- Informal view: resembles tables with rows and columns
- Formal terminology:
  - Row = Tuple
  - Column = Attribute
  - Table = Relation

**Why Relational**:
- Data arranged in two-dimensional tables
- Each table represents entity or relationship
- Easy to understand and query
- Industry standard for databases

### Formal Definitions

**Relation Schema R(A₁, A₂, ..., Aₙ)**:
- R: Relation name
- A₁, A₂, ..., Aₙ: Attributes/columns
- Each attribute has domain (set of valid values)

**Example**:
```
CUSTOMER (Cust_ID, Cust_Name, Address, Phone#)
- CUSTOMER: Relation name
- Cust_ID, Cust_Name, Address, Phone#: Attributes
```

**Tuple**:
- Ordered set of values (one row in table)
- Each value from corresponding attribute's domain

**Relation State**:
- Set of tuples at particular moment
- Schema is structure (static)
- State is data (dynamic)

### Key Concepts

**Domain**:
- Set of permitted values for attribute
- Has logical definition and data type

**Example**:
```
USA_phone_numbers domain: 10-digit numbers in format (ddd)ddd-dddd
Age domain: Integer 0-150
Grade domain: {A, B, C, D, F}
```

**Tuple**:
- Single row: John | 25 | Engineering | john@univ.edu

**Relation**:
- Set of tuples (rows)

---

## Relational Constraints

### Types of Constraints

**Implicit (Inherent Model-Based) Constraints**:
- Based on relational model itself
- Example: No list values allowed as attribute values
- Example: Relations are sets (no duplicates)

**Explicit (Schema-Based) Constraints**:
- Expressed in schema
- Specified using database facilities
- Example: Domain constraints, key constraints

**Semantic (Application-Based) Constraints**:
- Business rules beyond model's expressiveness
- Must be enforced by application programs
- Example: Employee salary always less than manager's salary

### Five Types of Schema-Based Constraints

#### 1. Domain Constraint

**Definition**:
Every value in tuple must be from corresponding attribute's domain.

**Enforcement**:
- Each attribute has specified data type
- Values checked during insertion/update
- NULL may be allowed if specified

**Example**:
```
Age attribute domain: Integer 18-70
Value "abc" violates domain constraint
Value 85 violates domain constraint
Value 25 satisfies constraint
```

#### 2. Key Constraint

**Three Levels**:

**Super Key**:
- Set of attributes SK such that no two tuples have same SK value
- Can have multiple attributes
- May include extra attributes

**Key**:
- Minimal super key
- Removing any attribute makes it not a super key
- Uniquely identifies tuple with minimum attributes

**Candidate Key**:
- Relation can have multiple keys
- Each key is candidate for primary key

**Example - CAR Relation**:
```
CAR (LicenseNo, RegNo, SerialNo, Make, Model, Year)

Candidate Keys:
- {LicenseNo} - uniquely identifies car
- {RegNo} - uniquely identifies car
- {SerialNo} - uniquely identifies car

All are super keys and candidate keys
Choose one as PRIMARY KEY (e.g., SerialNo)
```

**Primary Key**:
- Selected arbitrarily from candidate keys
- Used to uniquely identify tuples
- Attributes underlined in schema
- Cannot have NULL values

#### 3. Null Constraint

**Definition**:
Specifies whether NULL values allowed for attribute.

**Example**:
```
Name of STUDENT: NOT NULL (every student must have name)
PhoneNumber of STUDENT: NULL allowed (student may not have phone)
```

**Usage**:
- NOT NULL: Mandatory attribute
- NULL: Optional attribute

#### 4. Entity Integrity Constraint

**Definition**:
Primary key attributes cannot be NULL in any tuple.

**Rationale**:
- Primary key identifies tuple
- NULL would make identification impossible

**Example**:
```
STUDENT (StudentID [PK], Name, Email, DOB)

Student tuple:
- StudentID: NULL → violates entity integrity
- StudentID: S001 → satisfies entity integrity
```

#### 5. Referential Integrity Constraint

**Definition**:
Constraint between two relations involving foreign key.

**Foreign Key (FK)**:
- Set of attributes in relation R₁
- References primary key of relation R₂
- Maintains relationship between tables

**Rules**:
1. FK attributes have same domain as referenced primary key
2. FK value either:
   - Equals some primary key value in referenced relation, OR
   - Is NULL (if allowed)

**Example**:
```
EMPLOYEE (SSN [PK], Name, Salary, DeptNo [FK])
DEPARTMENT (DeptNo [PK], DeptName, Manager)

DeptNo in EMPLOYEE is foreign key referencing DeptNo in DEPARTMENT

Valid values for Employee.DeptNo:
- Must exist in some Department tuple
- OR be NULL

Example:
Employee: SSN=001, Name=John, DeptNo=10
Must have: Department with DeptNo=10 in database
```

---

## ER to Relational Mapping

### Mapping Process

**Principle**:
- Both ER and relational models are abstract logical representations
- ER design transforms into relational design through systematic steps
- Each entity set → relation schema
- Each relationship set → relation schema

### Seven-Step Mapping Algorithm

#### Step 1: Mapping Regular (Strong) Entity Types

**Rule**:
For each strong entity E, create relation R including:
- All simple attributes of E
- One key attribute as primary key

**For Composite Attributes**:
- Include only simple components
- Not composite attribute itself

**Example**:
```
ER Entity: EMPLOYEE
- SSN (key)
- Name (composite: FirstName, MiddleName, LastName)
- Address (composite: Street, City, State, Zip)
- Salary
- DeptNo

Relational Schema:
EMPLOYEE (SSN, FirstName, MiddleName, LastName, 
          Street, City, State, Zip, Salary, DeptNo)
Primary Key: {SSN}
```

#### Step 2: Mapping Weak Entity Types

**Rule**:
For each weak entity W with owner E, create relation R with:
- All simple attributes of W
- Primary key attributes of owner E as foreign key
- Primary key of R = {Owner's PK + W's discriminator}

**Example**:
```
ER: DEPENDENT (weak entity)
- ESSN (foreign key referencing EMPLOYEE)
- DependentName (partial key)
- Sex, BirthDate, Relationship

Relational Schema:
DEPENDENT (ESSN, DependentName, Sex, BirthDate, Relationship)
Primary Key: {ESSN, DependentName}
Foreign Key: {ESSN} references EMPLOYEE(SSN)
```

#### Step 3: Mapping Binary 1:1 Relationships

**Three Approaches**:

**Option A: Foreign Key (2 relations)**
- Choose entity with total participation
- Add foreign key to that relation

**Example**:
```
ER: MANAGES (1:1)
- DEPARTMENT (total participation)
- EMPLOYEE (partial participation)

Relational Schema:
DEPARTMENT (DeptNo, DeptName, ..., ESSN [FK to EMPLOYEE])
EMPLOYEE (SSN, Name, ...)
```

**Option B: Merged Relation (1 relation)**
- Merge entities and relationship into single relation
- Good when both have total participation

**Option C: Cross-Reference Relation (3 relations)**
- Create separate relation for relationship
- Store both primary keys as foreign keys

#### Step 4: Mapping Binary 1:N Relationships

**Rule**:
- Identify relation S on N-side
- Add primary key of 1-side relation as foreign key to S
- Include relationship attributes in S

**Example**:
```
ER: WORKS_FOR (1:N)
- DEPARTMENT (1-side)
- EMPLOYEE (N-side)

Relational Schema:
DEPARTMENT (DeptNo, DeptName, ...)
EMPLOYEE (SSN, Name, ..., DeptNo [FK to DEPARTMENT])
```

**Why**: Multiple employees can work for one department, represented by adding DepartmentNo to EMPLOYEE table.

#### Step 5: Mapping Binary M:N Relationships

**Rule**:
- Create separate relationship relation
- Include foreign keys of both entities
- Combination of FKs forms primary key
- Include relationship attributes

**Example**:
```
ER: WORKS_ON (M:N)
- EMPLOYEE (many-side)
- PROJECT (many-side)
- Attribute: Hours

Relational Schema:
EMPLOYEE (SSN, Name, ...)
PROJECT (ProjNo, ProjName, ...)
WORKS_ON (SSN [FK], ProjNo [FK], Hours)
Primary Key: {SSN, ProjNo}
```

**Why separate relation**: One employee can work on multiple projects, one project can have multiple employees.

#### Step 6: Mapping Multivalued Attributes

**Rule**:
- Create new relation for multivalued attribute
- Include attribute itself
- Include primary key of owner entity as foreign key
- Primary key of new relation = {Owner's PK + Attribute value}

**Example**:
```
ER: DEPARTMENT with multivalued attribute LOCATIONS

Relational Schema:
DEPARTMENT (DeptNo, DeptName, ...)
DEPT_LOCATIONS (DeptNo [FK], Location)
Primary Key of DEPT_LOCATIONS: {DeptNo, Location}
```

#### Step 7: Mapping N-ary Relationships (N > 2)

**Rule**:
- Create relationship relation
- Include foreign keys of all participating entities
- Combination of FKs forms primary key
- Include relationship attributes

**Example - SUPPLY (3-ary)**:
```
ER: SUPPLY relationship
- SUPPLIER, PART, PROJECT

Relational Schema:
SUPPLIER (SupplierNo, ...)
PART (PartNo, ...)
PROJECT (ProjectNo, ...)
SUPPLY (SupplierNo [FK], PartNo [FK], ProjectNo [FK], Quantity)
Primary Key: {SupplierNo, PartNo, ProjectNo}
```

---

## Mapping Summary Table

| ER Construct | Mapping Result | Key Points |
|--------------|-----------------|-----------|
| Strong Entity | Relation with all simple attributes | PK from entity's key |
| Weak Entity | Relation with owner's FK + discriminator | PK = owner's PK + discriminator |
| 1:1 Relation | FK in total participation entity | Can be merged if both total |
| 1:N Relation | FK in N-side entity | Foreign key approach |
| M:N Relation | Separate relation with both FKs | FKs form composite PK |
| Multivalued Attr | Separate relation with entity's PK | PK = owner PK + attribute |
| N-ary Relation | Separate relation with all FKs | PK = combination of all FKs |
| Composite Attr | Store simple components only | Not the composite itself |
| Derived Attr | Calculated/not stored | Use view or procedure |

---

## Key Takeaways

1. **Strong Entities** → Simple relations with simple attributes
2. **Weak Entities** → Include owner's primary key as foreign key
3. **1:1 Relationships** → Foreign key on entity with total participation
4. **1:N Relationships** → Foreign key on N-side entity
5. **M:N Relationships** → Create separate relationship relation
6. **Multivalued Attributes** → Create separate relation
7. **N-ary Relationships** → Create relation for relationship itself
8. **Constraints** → Maintained as primary keys, foreign keys, NULL constraints
