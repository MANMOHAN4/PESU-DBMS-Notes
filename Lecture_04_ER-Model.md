# Entity-Relationship Model (ER Model)

## ER Model Overview

**Purpose**:
Graphical representation of database logical structure using symbols for entities, relationships, and attributes.

**Benefits**:
- Simple and clear representation
- Makes complex relationships easy to understand
- Identifies potential issues before implementation
- Comprehensive documentation tool
- More efficient, optimized database structures

**Basis for Database Design**:
ER diagrams represent conceptual layer before physical implementation in relational databases.

---

## Database Design Phases

**Six Main Phases**:
1. **Characterizing User Data Needs** - Understand requirements
2. **Choosing a Data Model** - Select (usually ER model)
3. **Translating Requirements** - Convert to data model
4. **Review and Refinement** - Check schema for issues
5. **Logical-Design Phase** - Define logical structure
6. **Physical-Design Phase** - Implement on storage media

---

## Core ER Concepts

### 1. Entity

**Definition**:
- Basic concept of ER model
- Specific things or objects in mini-world represented in database
- Can have physical or conceptual existence

**Types**:
- **Physical entities**: Car, person, employee, house
- **Conceptual entities**: Company, job, university course, project

**Characteristics**:
- Each entity has attributes
- Entities grouped into entity sets

**Example - Company Database**:
- EMPLOYEE entity (represents individual employees)
- DEPARTMENT entity (represents departments)
- PROJECT entity (represents projects)

---

### 2. Entity Set

**Definition**:
Collection of similar entities of the same type sharing common attributes and properties.

**Terminology**:
- **Entity Type**: The category (Student, Instructor)
- **Extension**: All entities of that type
- **Entity Instance**: Single entity (John - a specific student)

**Examples**:
- "STUDENT" entity set: all students in university
- "INSTRUCTOR" entity set: all instructors in university
- "COURSE" entity set: all courses offered

**Types of Entity Sets**:
1. **Strong Entity Set** - Entities have independent existence
   - STUDENT, DEPARTMENT, EMPLOYEE
2. **Weak Entity Set** - Entities depend on strong entities
   - DEPENDENT (depends on EMPLOYEE)

---

### 3. Attributes

**Definition**:
Qualities, characteristics, or properties describing entities.

**Characteristics**:
- Entities represented by specific attributes
- Signify information stored for each entity
- Each entity has own set of attribute values
- Multiple entities may have same attribute names

**Example**:
```
Instructor Entity:
- ID attribute
- Name attribute
- Department attribute
- Salary attribute

Each instructor has unique values for these attributes
```

---

## Types of Attributes

### A. Simple vs Composite Attributes

**Simple Attribute**:
- Cannot be subdivided into components
- Examples: Student ID, Roll number, Phone number, Email ID

**Composite Attribute**:
- Can be split into smaller component attributes
- Examples:
  - **Address**: street, city, state, zip code
  - **Name**: first name, middle name, last name
  - **Street**: street_no, street_name, apt_number

**When to Use Composite**:
- When users might want entire attribute sometimes
- When users might want individual components other times

**Representation in ER**:
- Composite attribute shown with components radiating from main attribute
- Hierarchy possible (street subdivided further)

### B. Single-Valued vs Multivalued Attributes

**Single-Valued Attribute**:
- Takes only ONE value for each entity instance
- Examples: Instructor name, student age

**Multivalued Attribute**:
- Can have ZERO, ONE, or MULTIPLE values per entity
- Examples:
  - Instructor phone numbers (may have 0, 1, or several)
  - Instructor email IDs (may have multiple)
  - Student previous degrees

**Representation in ER**:
- Multivalued attributes shown as double ellipse
- Single-valued attributes shown as single ellipse

**Common Uses**:
- Contact information (multiple phones, emails)
- Education history (multiple degrees)
- Skills (multiple competencies)

### C. Stored vs Derived Attributes

**Stored Attribute**:
- Actually stored in database
- Must be explicitly entered or created
- Example: Date_of_Birth of instructor

**Derived Attribute**:
- NOT stored in database
- Calculated/derived from other attributes
- Computed when needed
- Example: Age (derived from Date_of_Birth)

**Why Distinguish**:
- Stored: increases storage but provides base data
- Derived: saves storage but requires computation
- Reduces redundancy and ensures consistency

**Representation in ER**:
- Stored attributes: solid oval
- Derived attributes: dashed oval

### D. Complex Attributes

**Definition**:
Attributes formed by nesting composite and multivalued attributes.

**Characteristics**:
- Rarely used in DBMS
- Combination of composite AND multivalued

**Example**:
```
PreviousDegrees (Composite + Multivalued):
{PreviousDegrees (College, Year, Degree, Field)}
- Multiple PreviousDegrees values can exist
- Each has four subcomponents
```

### E. Value Set (Domain)

**Definition**:
Set of permitted values for an attribute.

**Formal Definition**:
Attribute A of entity set E with value set V:
A : E → P(V)

**Examples**:
- Age attribute domain: integers 16-70
- Grade attribute domain: {A, B, C, D, F}
- Gender domain: {Male, Female, Other}

---

### Key Attribute

**Definition**:
Attribute (or set of attributes) that uniquely identifies each entity in entity set.

**Characteristics**:
- Each entity must have unique value
- No two entities have same key value
- Prevents confusion (multiple people with same name)
- May be composite

**Examples**:
- **SSN**: Uniquely identifies EMPLOYEE
- **StudentID**: Uniquely identifies STUDENT
- **VIN**: Uniquely identifies vehicle

**Multiple Keys**:
- Entity type may have more than one key
- Each is a candidate key
- All candidate keys underlined in ER diagram

**Key Attribute Representation**:
- Underlined in ER diagram
- Indicates uniqueness constraint

---

### Null Values

**Definition**:
Special value indicating absence of information.

**When Used**:
- Attribute doesn't apply to entity
- Information missing
- Information unknown

**Three Interpretations**:

1. **Not Applicable**: Attribute doesn't make sense
   - Apartment number (for house-only address)
   - Middle name (person has only first/last)

2. **Missing**: Value exists but we don't have it
   - Student phone number (exists but we don't know)
   - Employee salary (exists but not entered yet)

3. **Unknown**: Uncertain whether value exists
   - Apartment number (don't know if part of address)
   - Employee middle name (don't know if person has one)

**Example**:
```
Student: John Doe
- Middle name: NULL
Meaning could be:
- John has no middle name (not applicable)
- John has middle name but not recorded (missing)
- Unknown whether John has middle name (unknown)
```

---

## Relationship and Relationship Sets

### Relationship

**Definition**:
Connection or association between entities.

**Example**:
- "Advisor" relationship links instructor Katz to student Shankar
- Represents that instructor Katz is advising student Shankar

### Relationship Set

**Definition**:
Collection of relationships of same type.

**Example**:
- "advisor" relationship set links students and their advisors
- Contains all student-advisor pairs in university

### Relationship Instance

**Definition**:
Individual association between specific entities.

**Example**:
- Individual pair: Instructor Katz (ID 45565) and Student Shankar (ID 12345)
- Represents one instance of advisor relationship

**Representation in ER**:
- Relationships shown as diamonds
- Lines connect diamond to involved entity sets
- Label on diamond describes relationship

---

## Roles in Relationships

**Definition**:
Function that entity plays in relationship.

**When Specified**:
- Usually implicit when entity sets are distinct
- Essential when same entity set participates multiple times
- Called recursive relationships

**Recursive Relationship Example**:
```
EMPLOYEE supervises EMPLOYEE (same entity set)
- Employee A supervises Employee B
- Role labels needed:
  - "supervisor" (entity supervising)
  - "supervisee" (entity being supervised)
```

**Representation**:
- Role names label lines connecting entity to relationship
- Clarifies entity's function in relationship

---

## Degree of Relationships

**Definition**:
Number of entity sets participating in relationship.

**Types**:

### Binary Relationships (Degree 2)
- Most common in databases
- Involves TWO entity sets
- Example: advisor (instructor, student)

### Ternary Relationships (Degree 3)
- Involves THREE entity sets
- Cannot be decomposed into binary relationships without loss of info
- Example: proj_guide (instructor, student, project)

**Example - Ternary Relationship**:
```
PROJECT GUIDE relation:
- Instructor guides which student
- On which project
- Cannot express as: Instructor guides Student + Student does Project
  (doesn't capture which instructor for which student on project)
```

### Higher Degree Relationships
- Quaternary (4 entity sets), etc.
- Increasingly rare in practice

---

## Relationship Attributes

**Definition**:
Attributes associated with relationships, not entities.

**When Used**:
- Information meaningless without relationship
- Example: GRADE attribute
  - Only exists when STUDENT enrolls in COURSE
  - Doesn't belong in STUDENT or COURSE
  - Belongs in ENROLLMENT relationship

**Other Examples**:
- Hours per week (EMPLOYEE works on PROJECT)
- Date hired (MANAGER manages DEPARTMENT)
- Duration (TEACHER teaches COURSE)

**Representation in ER**:
- Oval connected to relationship diamond
- Enclosed within relationship

---

## Example: University Database ER Diagram

### Entities:
- **INSTRUCTOR**: ID (key), Name (composite: first, middle, last), Address (composite: street, city, state, zip), Phone (multivalued), Age (derived)
- **STUDENT**: StudentID (key), Name, Address
- **COURSE**: CourseID (key), CourseName
- **DEPARTMENT**: DeptID (key), DeptName

### Relationships:
- **ADVISOR**: Instructor advises Student
- **TEACHES**: Instructor teaches Course
- **ENROLLED**: Student enrolls in Course (with GRADE attribute)
- **BELONG**: Student belongs to Department

---

## ER Diagram Symbols Summary

| Symbol | Represents |
|--------|-----------|
| Rectangle | Entity set |
| Oval | Attribute |
| Double Oval | Multivalued attribute |
| Dashed Oval | Derived attribute |
| Underlined Oval | Key attribute |
| Diamond | Relationship |
| Line | Connection |
| Double Line | Participation (total) |
| Single Line | Participation (partial) |

---

## ER Model Key Points

1. **Conceptual Modeling**: ER model represents conceptual design before physical implementation
2. **Communication**: Clear graphical representation facilitates understanding
3. **Documentation**: ER diagrams serve as comprehensive database documentation
4. **Analysis**: Helps identify issues, redundancies, and inconsistencies
5. **Flexibility**: Models complex real-world relationships accurately
6. **Foundation**: Basis for converting to relational schema for SQL implementation
