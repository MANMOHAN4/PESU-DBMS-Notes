# ER Model 2: Weak Entities, Cardinality, and Participation

## Weak Entity Sets

### Weak Entity Definition

**Definition**:
- Existence dependent on another entity (identifying entity)
- Cannot be uniquely identified by its own attributes alone
- Always associated with a strong (identifying) entity

**Strong Entity**:
- Entity that is NOT a weak entity
- Can exist independently
- Has its own complete primary key

### Identifying Entity and Identifying Relationship

**Identifying Entity**:
- Strong entity on which weak entity depends
- Provides part of weak entity's primary key

**Identifying Relationship**:
- Relationship connecting weak entity to identifying entity
- Shows dependency relationship
- Essential for weak entity identification

**Example - University Database**:
```
COURSE (Strong Entity):
- Attributes: Course_ID, title, dept_name, credits
- Primary Key: {Course_ID}

SECTION (Weak Entity):
- Attributes: Course_ID, sec_ID, semester, year, building, room_number, time_slot_id
- Discriminator (Partial Key): {sec_ID, semester, year}
- Identifying Relationship: sec_course (with COURSE)
- Primary Key: {Course_ID, sec_ID, semester, year}

Why weak? A Section cannot exist without a Course.
Section 1 in CS101 is different from Section 1 in MATH101.
```

### Key Concept: Discriminator (Partial Key)

**Discriminator**:
- Attribute(s) of weak entity that distinguish instances within same identifying entity
- Also called partial key
- NOT sufficient alone to identify weak entity
- Must combine with identifying entity's primary key

**Example - Section as Weak Entity**:
```
Discriminator: {sec_ID, semester, year}
Combined Primary Key: {Course_ID, sec_ID, semester, year}

Why? sec_ID alone (like "sec_ID = 01") doesn't uniquely identify a section.
- There are many sections with sec_ID = 01 across different courses.
- Need Course_ID to know which course the section belongs to.
- Combined: CS101-Sec-01-Fall-2024 uniquely identifies section
```

### ER Diagram Representation

**Symbols**:
- **Weak Entity**: Double rectangle (instead of single rectangle)
- **Discriminator**: Dashed underline (instead of solid underline)
- **Identifying Relationship**: Double diamond (instead of single diamond)
- **Total Participation**: Double line from weak entity to identifying relationship

**Why Double Symbols**:
- Emphasizes dependency and uniqueness among weak entities
- Shows that identifying relationship is mandatory

### Multiple Identifying Entities

**Concept**:
- Weak entity can depend on multiple strong entities
- Primary key combines all identifying entities' keys + discriminator
- Each identifying entity contributes its primary key

**Example**:
```
MAINTENANCE_REQUEST (Weak Entity):
Identifying Entities: CITIZEN + INFRASTRUCTURE
Discriminator: request_ID

Primary Key: {Citizen_ID, Infrastructure_ID, request_ID}

Can't exist without both a citizen and infrastructure.
Same request_ID for different citizens = different requests.
```

---

## Cardinality Ratios (Mapping Cardinality)

### Definition

**Cardinality Ratio**:
- Expresses number of entities one entity can be associated with via relationship
- Describes constraints on relationship participation
- Most useful for binary relationships

### Four Types of Cardinality Ratios

#### 1. One-to-One (1:1)

**Definition**:
- Entity in A associated with at most one entity in B
- Entity in B associated with at most one entity in A

**Constraint**:
- Maximum on both sides = 1

**Examples**:
- PERSON has one PASSPORT (and passport belongs to one person)
- MANAGER manages one DEPARTMENT (and department has one manager)
- EMPLOYEE has one OFFICE (and office assigned to one employee)

**ER Representation**:
- Single line from relationship to both entity sets

#### 2. One-to-Many (1:N)

**Definition**:
- Entity in A associated with any number (0 or more) entities in B
- Entity in B associated with with at most one entity in A

**Constraint**:
- Maximum on A side = 1 (many entities in B point to one entity in A)
- Maximum on B side = N (one entity in A can have many in B)

**Read as**: "One A to Many B"

**Examples**:
- DEPARTMENT has many EMPLOYEES (but EMPLOYEE works for one DEPARTMENT)
- INSTRUCTOR advises many STUDENTS (but STUDENT has one ADVISOR)
- DEPARTMENT controls many PROJECTS (but PROJECT controlled by one DEPARTMENT)

**ER Representation**:
- Line to A (one side): single line
- Line to B (many side): arrow or line

#### 3. Many-to-One (N:1)

**Definition**:
- Entity in A associated with at most one entity in B
- Entity in B associated with any number (0 or more) entities in A

**Constraint**:
- Maximum on A side = N
- Maximum on B side = 1

**Read as**: "Many A to One B"

**Examples**:
- STUDENTS can have many ADVISORS (but ADVISOR advises many STUDENTS)
- Multiple PROJECTS use one SOFTWARE_TOOL

**Relationship to 1:N**:
- N:1 is converse/reverse of 1:N
- Same relationship viewed from opposite direction

#### 4. Many-to-Many (M:N)

**Definition**:
- Entity in A associated with any number (0 or more) entities in B
- Entity in B associated with any number (0 or more) entities in A

**Constraint**:
- Maximum on both sides = N

**Examples**:
- STUDENTS enroll in many COURSES (and COURSES have many STUDENTS)
- EMPLOYEES work on many PROJECTS (and PROJECTS have many EMPLOYEES)
- INSTRUCTORS teach many COURSES (and COURSES taught by many INSTRUCTORS)

**ER Representation**:
- Lines from relationship to both entities without cardinality restrictions

### Cardinality Ratio Examples

| Ratio | A to B | B to A | Example |
|-------|--------|--------|---------|
| 1:1 | at most 1 | at most 1 | PERSON:PASSPORT |
| 1:N | any number | at most 1 | DEPARTMENT:EMPLOYEE |
| N:1 | at most 1 | any number | EMPLOYEE:DEPARTMENT (reverse view) |
| M:N | any number | any number | STUDENT:COURSE |

---

## Participation Constraints

### Types of Participation

#### Total Participation

**Definition**:
- Every entity in entity set must participate in at least one relationship
- Entity cannot exist without being involved in relationship
- Mandatory participation

**Meaning**:
- Minimum cardinality = 1
- No entity can skip the relationship

**Examples**:
- Every STUDENT must be enrolled in at least one COURSE
- Every DEPENDENT must belong to an EMPLOYEE
- Every SECTION must be part of a COURSE

**ER Representation**:
- Double line connecting entity to relationship

#### Partial Participation

**Definition**:
- Some entities may not participate in relationship
- Optional participation
- Entity can exist without being involved

**Meaning**:
- Minimum cardinality = 0
- Some entities may skip the relationship

**Examples**:
- An INSTRUCTOR may not advise any STUDENT
- A PROJECT may not have any EMPLOYEE assigned
- An EMPLOYEE may not manage any DEPARTMENT

**ER Representation**:
- Single line connecting entity to relationship

### Combined Cardinality and Participation

**Example - University Advisor Relationship**:

**Scenario 1: Students must have advisors, instructors may not advise**:
```
Total participation for STUDENT: Every student has an advisor
Partial participation for INSTRUCTOR: Some instructors don't advise
Cardinality: INSTRUCTOR advises many STUDENTS (1:N)

ER: 
- Double line STUDENT → ADVISOR (total)
- Single line INSTRUCTOR → ADVISOR (partial)
```

**Scenario 2: Both must participate**:
```
Total participation for both: Every student has an advisor, every instructor advises someone
Cardinality: 1:N

ER:
- Double lines from both entities (both total)
```

---

## Min-Max Notation

### Definition

**Min-Max (l, h) Notation**:
- (l, h) where l = minimum cardinality, h = maximum cardinality
- Placed on line between entity and relationship

**Minimum Cardinality (l)**:
- 0: optional participation (partial)
- 1: mandatory participation (total)

**Maximum Cardinality (h)**:
- 1: at most one
- N: any number (no limit)

### Examples

**Student-Course Enrollment**:
- (2, 5) on STUDENT side: Each student enrolls in minimum 2, maximum 5 courses
- (0, N) on COURSE side: Each course may have 0 or many students

**Employee-Department**:
- (1, 1) on EMPLOYEE side: Each employee works for exactly 1 department (total, single)
- (1, N) on DEPARTMENT side: Each department has at least 1 employee, many possible

**Weak Entity Constraint**:
- (1, 1) on weak entity to identifying relationship: Weak entity must participate in exactly 1 identifying relationship

### Mapping to Binary Cardinality

| Min-Max | Interpretation |
|---------|----------------|
| (0, 1) | Optional, single (zero or one) |
| (1, 1) | Mandatory, single (exactly one) |
| (0, N) | Optional, many (zero or more) |
| (1, N) | Mandatory, many (one or more) |

---

## ER Notation Styles

### Chen Notation (Classic)

**Symbols**:
- Rectangle: Entity
- Oval: Attribute
- Diamond: Relationship
- Line: Connection

**Cardinality**:
- Labels on relationship lines: 1, N, M:N

### Crow's Foot Notation

**Symbols**:
- Crow's foot (branching lines) indicate many side
- Single line indicates one side
- Different line styles for participation (solid = total, dashed = partial)

### Min-Max Notation

**Display**:
- (min, max) labels on lines
- More precise specification of constraints

---

## Key Takeaways

1. **Weak Entities**: Existence dependent on strong entities, use combined keys
2. **Cardinality Ratios**: Express relationship constraints (1:1, 1:N, N:1, M:N)
3. **Participation**: Total (mandatory) vs Partial (optional)
4. **Min-Max**: Precise notation for minimum and maximum cardinality
5. **Combination**: Cardinality + Participation = Complete relationship specification
